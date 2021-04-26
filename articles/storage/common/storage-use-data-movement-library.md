---
title: .NET için veri taşıma kitaplığıyla veri aktarma
titleSuffix: Azure Storage
description: Veri taşıma kitaplığını kullanarak blob ve dosya içeriklerine veri taşıyın veya kopyalayın. Yerel dosyalardaki verileri Azure depolama 'ya kopyalayın veya depolama hesapları içinde veya arasında verileri kopyalayın. Verilerinizi kolayca Azure Storage 'a geçirin.
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: how-to
ms.date: 06/16/2020
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: f87379f48f82757916aef0fa0d358835f48cb9a5
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107875944"
---
# <a name="transfer-data-with-the-data-movement-library"></a>Veri Taşıma kitaplığıyla veri aktarma

Azure depolama veri taşıma kitaplığı, Blobları ve dosyaları karşıya yüklemek, indirmek ve kopyalamak için tasarlanan platformlar arası açık kaynaklı bir kitaplıktır. Veri taşıma kitaplığı, .NET için Azure Storage istemci kitaplığı 'nda kullanılamayan kullanışlı yöntemler sunar. Bu yöntemler paralel işlemlerin sayısını ayarlamanıza, aktarım ilerlemesini izlemenize, iptal edilen bir aktarımı kolayca sürdürmeye ve çok daha fazlasına olanak sağlar.

Bu kitaplık ayrıca .NET Core kullanır, bu da Windows, Linux ve macOS için .NET uygulamaları oluştururken kullanabileceğiniz anlamına gelir. .NET Core hakkında daha fazla bilgi edinmek için [.NET Core belgelerine](https://dotnet.github.io/)bakın. Bu kitaplık, Windows için geleneksel .NET Framework uygulamaları için de geçerlidir.

Bu belgede, Windows, Linux ve macOS 'ta çalışan bir .NET Core konsol uygulamasının nasıl oluşturulacağı ve aşağıdaki senaryolar gerçekleştirilir:

- Dosya ve dizinleri blob depolamaya yükleyin.
- Veri aktarımı sırasında paralel işlem sayısını tanımlayın.
- Veri aktarımı ilerlemesini izleyin.
- Veri aktarımını iptal et.
- Dosyayı URL 'den blob depolamaya kopyalayın.
- Blob depolamadan blob depolamaya kopyalama.

## <a name="prerequisites"></a>Önkoşullar

- [Visual Studio Code](https://code.visualstudio.com/)
- Bir [Azure depolama hesabı](storage-account-create.md)

## <a name="setup"></a>Kurulum

1. .NET Core SDK yüklemek için [.NET Core yükleme kılavuzunu](https://dotnet.microsoft.com/download) ziyaret edin. Ortamınızı seçerken komut satırı seçeneğini belirleyin.
2. Komut satırından projeniz için bir dizin oluşturun. Bu dizine gidip `dotnet new console -o <sample-project-name>` bir C# konsol projesi oluşturmak için yazın.
3. Bu dizini Visual Studio Code açın. Bu adım, Windows 'a yazarak komut satırı aracılığıyla hızlı bir şekilde yapılabilir `code .` .
4. Visual Studio Code marketi 'nden [C# uzantısını](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) yükler. Visual Studio Code yeniden başlatın.
5. Bu noktada, iki istem görmeniz gerekir. Biri, "derlemek ve hata ayıklamak için gerekli varlıklar" eklemek içindir. "Evet" e tıklayın. Çözümlenmemiş bağımlılıkları geri yüklemek için başka bir istem vardır. "Geri yükle" ye tıklayın.
6. `launch.json` `.vscode` Konsol olarak dış Terminal kullanmak için altında öğesini değiştirin. Bu ayar şu şekilde okunmalıdır `"console": "externalTerminal"`
7. Visual Studio Code, .NET Core uygulamalarında hata ayıklamanıza olanak tanır. `F5`Uygulamanızı çalıştırmak ve kurulumun çalıştığını doğrulamak için isabet edin. "Merhaba Dünya!" görmeniz gerekir konsola yazdırılır.

## <a name="add-the-data-movement-library-to-your-project"></a>Veri taşıma kitaplığını projenize ekleyin

1. Veri taşıma kitaplığının en son sürümünü `dependencies` dosyanızın bölümüne ekleyin `<project-name>.csproj` . Bu sürüm, yazma sırasında `"Microsoft.Azure.Storage.DataMovement": "0.6.2"`
2. Projenizi geri yüklemek için bir istem görüntülenmelidir. "Geri yükle" düğmesine tıklayın. Ayrıca, proje dizininizin köküne komutunu yazarak projenizi komut satırından da geri yükleyebilirsiniz `dotnet restore` .

Değiştir `<project-name>.csproj` :

```xml
<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <OutputType>Exe</OutputType>
        <TargetFramework>netcoreapp2.0</TargetFramework>
    </PropertyGroup>
    <ItemGroup>
        <PackageReference Include="Microsoft.Azure.Storage.DataMovement" Version="0.6.2" />
        </ItemGroup>
    </Project>
```

## <a name="set-up-the-skeleton-of-your-application"></a>Uygulamanızın iskektlerini ayarlayın

Yaptığımız ilk şey, uygulamamız için "iskelet" kodunu ayarlamuyoruz. Bu kod, bir depolama hesabı adı ve hesap anahtarı için bize sorar ve bu kimlik bilgilerini bir nesne oluşturmak için kullanır `CloudStorageAccount` . Bu nesne, tüm aktarım senaryolarında depolama hesabınızla etkileşim kurmak için kullanılır. Kod ayrıca, çalıştırmak istediğimiz aktarım işlemi türünü seçmenizi ister.

Değiştir `Program.cs` :

```csharp
using System;
using System.Threading;
using System.Threading.Tasks;
using System.Diagnostics;
using Microsoft.Azure.Storage;
using Microsoft.Azure.Storage.Blob;
using Microsoft.Azure.Storage.DataMovement;

namespace DMLibSample
{
    public class Program
    {
        public static void Main()
        {
            Console.WriteLine("Enter Storage account name:");
            string accountName = Console.ReadLine();

            Console.WriteLine("\nEnter Storage account key:");
            string accountKey = Console.ReadLine();

            string storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=" + accountName + ";AccountKey=" + accountKey;
            CloudStorageAccount account = CloudStorageAccount.Parse(storageConnectionString);

            ExecuteChoice(account);
        }

        public static void ExecuteChoice(CloudStorageAccount account)
        {
            Console.WriteLine("\nWhat type of transfer would you like to execute?\n1. Local file --> Azure Blob\n2. Local directory --> Azure Blob directory\n3. URL (e.g. Amazon S3 file) --> Azure Blob\n4. Azure Blob --> Azure Blob");
            int choice = int.Parse(Console.ReadLine());

            if(choice == 1)
            {
                TransferLocalFileToAzureBlob(account).Wait();
            }
            else if(choice == 2)
            {
                TransferLocalDirectoryToAzureBlobDirectory(account).Wait();
            }
            else if(choice == 3)
            {
                TransferUrlToAzureBlob(account).Wait();
            }
            else if(choice == 4)
            {
                TransferAzureBlobToAzureBlob(account).Wait();
            }
        }

        public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
        {

        }

        public static async Task TransferLocalDirectoryToAzureBlobDirectory(CloudStorageAccount account)
        {

        }

        public static async Task TransferUrlToAzureBlob(CloudStorageAccount account)
        {

        }

        public static async Task TransferAzureBlobToAzureBlob(CloudStorageAccount account)
        {

        }
    }
}
```

## <a name="upload-a-local-file-to-a-blob"></a>Blob 'a yerel bir dosya yükleme

Yöntemlerini `GetSourcePath` ve aşağıdakileri ekleyin `GetBlob` `Program.cs` :

```csharp
public static string GetSourcePath()
{
    Console.WriteLine("\nProvide path for source:");
    string sourcePath = Console.ReadLine();

    return sourcePath;
}

public static CloudBlockBlob GetBlob(CloudStorageAccount account)
{
    CloudBlobClient blobClient = account.CreateCloudBlobClient();

    Console.WriteLine("\nProvide name of Blob container:");
    string containerName = Console.ReadLine();
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);
    container.CreateIfNotExistsAsync().Wait();

    Console.WriteLine("\nProvide name of new Blob:");
    string blobName = Console.ReadLine();
    CloudBlockBlob blob = container.GetBlockBlobReference(blobName);

    return blob;
}
```

Yöntemi değiştirin `TransferLocalFileToAzureBlob` :

```csharp
public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
{
    string localFilePath = GetSourcePath();
    CloudBlockBlob blob = GetBlob(account);
    Console.WriteLine("\nTransfer started...");
    await TransferManager.UploadAsync(localFilePath, blob);
    Console.WriteLine("\nTransfer operation complete.");
    ExecuteChoice(account);
}
```

Bu kod, yerel bir dosyanın yolunu, yeni veya var olan bir kapsayıcının adını ve yeni bir Blobun adını ister. `TransferManager.UploadAsync`Yöntemi, bu bilgileri kullanarak karşıya yüklemeyi gerçekleştirir.

`F5`Uygulamanızı çalıştırmak için isabet edin. [Microsoft Azure Depolama Gezgini](https://storageexplorer.com/)ile depolama hesabınızı görüntüleyerek karşıya yükleme işleminin gerçekleştiğini doğrulayabilirsiniz.

## <a name="set-the-number-of-parallel-operations"></a>Paralel işlem sayısını ayarla

Veri taşıma kitaplığı tarafından sunulan bir özellik, veri aktarımı aktarım hızını artırmak için paralel işlem sayısını ayarlamanıza olanak sağlar. Varsayılan olarak, veri taşıma kitaplığı, paralel işlem sayısını, makinenizde bulunan çekirdek sayısı olarak 8 * olarak ayarlar.

Düşük bant genişliğine sahip bir ortamdaki birçok paralel işlemin ağ bağlantısını tükendiğini ve gerçekten işlemlerin tamamen tamamlanmasını önlemeyeceğini aklınızda bulundurun. Kullanılabilir ağ bant genişliğinizi temel alarak neyin en iyi şekilde çalışacağını belirlemek için bu ayarla denemeler yapmanız gerekir.

Paralel işlemlerin sayısını ayarlayabileceğimizi sağlayan bir kod ekleyelim. Ayrıca, aktarımın tamamlanma süresini belirten bir kod ekleyelim.

`SetNumberOfParallelOperations`Şunları yapmak için bir yöntem ekleyin `Program.cs` :

```csharp
public static void SetNumberOfParallelOperations()
{
    Console.WriteLine("\nHow many parallel operations would you like to use?");
    string parallelOperations = Console.ReadLine();
    TransferManager.Configurations.ParallelOperations = int.Parse(parallelOperations);
}
```

`ExecuteChoice`Kullanılacak yöntemi değiştirin `SetNumberOfParallelOperations` :

```csharp
public static void ExecuteChoice(CloudStorageAccount account)
{
    Console.WriteLine("\nWhat type of transfer would you like to execute?\n1. Local file --> Azure Blob\n2. Local directory --> Azure Blob directory\n3. URL (e.g. Amazon S3 file) --> Azure Blob\n4. Azure Blob --> Azure Blob");
    int choice = int.Parse(Console.ReadLine());

    SetNumberOfParallelOperations();

    if(choice == 1)
    {
        TransferLocalFileToAzureBlob(account).Wait();
    }
    else if(choice == 2)
    {
        TransferLocalDirectoryToAzureBlobDirectory(account).Wait();
    }
    else if(choice == 3)
    {
        TransferUrlToAzureBlob(account).Wait();
    }
    else if(choice == 4)
    {
        TransferAzureBlobToAzureBlob(account).Wait();
    }
}
```

`TransferLocalFileToAzureBlob`Yöntemi bir Zamanlayıcı kullanacak şekilde değiştirin:

```csharp
public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
{
    string localFilePath = GetSourcePath();
    CloudBlockBlob blob = GetBlob(account);
    Console.WriteLine("\nTransfer started...");
    Stopwatch stopWatch = Stopwatch.StartNew();
    await TransferManager.UploadAsync(localFilePath, blob);
    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

## <a name="track-transfer-progress"></a>Aktarım ilerlemesini izleme

Verilerin aktarım için ne kadar sürdüğünü bilme yararlı olur. Ancak, aktarım işlemi *sırasında* aktarımın ilerlemesini görmek daha da iyidir. Bu senaryoya ulaşmak için bir nesnesi oluşturuyoruz `TransferContext` . `TransferContext`Nesnesi iki biçimde gelir: `SingleTransferContext` ve `DirectoryTransferContext` . İlki tek bir dosyayı aktarmaya yöneliktir ve ikincisi bir dosya dizinini aktarmaya yöneliktir.

Yöntemlerini `GetSingleTransferContext` ve aşağıdakileri ekleyin `GetDirectoryTransferContext` `Program.cs` :

```csharp
public static SingleTransferContext GetSingleTransferContext(TransferCheckpoint checkpoint)
{
    SingleTransferContext context = new SingleTransferContext(checkpoint);

    context.ProgressHandler = new Progress<TransferStatus>((progress) =>
    {
        Console.Write("\rBytes transferred: {0}", progress.BytesTransferred );
    });

    return context;
}

public static DirectoryTransferContext GetDirectoryTransferContext(TransferCheckpoint checkpoint)
{
    DirectoryTransferContext context = new DirectoryTransferContext(checkpoint);

    context.ProgressHandler = new Progress<TransferStatus>((progress) =>
    {
        Console.Write("\rBytes transferred: {0}", progress.BytesTransferred );
    });

    return context;
}
```

`TransferLocalFileToAzureBlob`Kullanılacak yöntemi değiştirin `GetSingleTransferContext` :

```csharp
public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
{
    string localFilePath = GetSourcePath();
    CloudBlockBlob blob = GetBlob(account);
    TransferCheckpoint checkpoint = null;
    SingleTransferContext context = GetSingleTransferContext(checkpoint);
    Console.WriteLine("\nTransfer started...\n");
    Stopwatch stopWatch = Stopwatch.StartNew();
    await TransferManager.UploadAsync(localFilePath, blob, null, context);
    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

## <a name="resume-a-canceled-transfer"></a>İptal edilen bir aktarımı sürdürür

Veri taşıma kitaplığı tarafından sunulan başka bir kullanışlı özellik, iptal edilen bir aktarımı sürdürmeye olanak sağlar. Yazarak, aktarımın geçici olarak iptal etmemizi sağlayan bir kod ekleyelim `c` ve sonra 3 saniye sonra aktarıma geri dönelim.

Değiştir `TransferLocalFileToAzureBlob` :

```csharp
public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
{
    string localFilePath = GetSourcePath();
    CloudBlockBlob blob = GetBlob(account);
    TransferCheckpoint checkpoint = null;
    SingleTransferContext context = GetSingleTransferContext(checkpoint);
    CancellationTokenSource cancellationSource = new CancellationTokenSource();
    Console.WriteLine("\nTransfer started...\nPress 'c' to temporarily cancel your transfer...\n");

    Stopwatch stopWatch = Stopwatch.StartNew();
    Task task;
    ConsoleKeyInfo keyinfo;
    try
    {
        task = TransferManager.UploadAsync(localFilePath, blob, null, context, cancellationSource.Token);
        while(!task.IsCompleted)
        {
            if(Console.KeyAvailable)
            {
                keyinfo = Console.ReadKey(true);
                if(keyinfo.Key == ConsoleKey.C)
                {
                    cancellationSource.Cancel();
                }
            }
        }
        await task;
    }
    catch(Exception e)
    {
        Console.WriteLine("\nThe transfer is canceled: {0}", e.Message);
    }

    if(cancellationSource.IsCancellationRequested)
    {
        Console.WriteLine("\nTransfer will resume in 3 seconds...");
        Thread.Sleep(3000);
        checkpoint = context.LastCheckpoint;
        context = GetSingleTransferContext(checkpoint);
        Console.WriteLine("\nResuming transfer...\n");
        await TransferManager.UploadAsync(localFilePath, blob, null, context);
    }

    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

Bu aşamada, bizim `checkpoint` değeri her zaman olarak ayarlanmıştır `null` . Artık aktarımı iptal etmemiz durumunda, aktarımımız son denetim noktasını aldık ve ardından bu yeni denetim noktasını aktarma bağlamımızda kullanacağız.

## <a name="transfer-a-local-directory-to-blob-storage"></a>Yerel bir dizini blob depolamaya aktarma

Veri taşıma kitaplığı tek seferde yalnızca bir dosya aktaracağından, bu durum söz konusu olabilir. Luckily, bu durum değildir. Veri taşıma kitaplığı, bir dosya dizinini ve tüm alt dizinlerini aktarma yeteneği sağlar. Yalnızca bunu yapmamızı sağlayan bir kod ekleyelim.

İlk olarak, yöntemini `GetBlobDirectory` öğesine ekleyin `Program.cs` :

```csharp
public static CloudBlobDirectory GetBlobDirectory(CloudStorageAccount account)
{
    CloudBlobClient blobClient = account.CreateCloudBlobClient();

    Console.WriteLine("\nProvide name of Blob container. This can be a new or existing Blob container:");
    string containerName = Console.ReadLine();
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);
    container.CreateIfNotExistsAsync().Wait();

    CloudBlobDirectory blobDirectory = container.GetDirectoryReference("");

    return blobDirectory;
}
```

Sonra Değiştir `TransferLocalDirectoryToAzureBlobDirectory` :

```csharp
public static async Task TransferLocalDirectoryToAzureBlobDirectory(CloudStorageAccount account)
{
    string localDirectoryPath = GetSourcePath();
    CloudBlobDirectory blobDirectory = GetBlobDirectory(account);
    TransferCheckpoint checkpoint = null;
    DirectoryTransferContext context = GetDirectoryTransferContext(checkpoint);
    CancellationTokenSource cancellationSource = new CancellationTokenSource();
    Console.WriteLine("\nTransfer started...\nPress 'c' to temporarily cancel your transfer...\n");

    Stopwatch stopWatch = Stopwatch.StartNew();
    Task task;
    ConsoleKeyInfo keyinfo;
    UploadDirectoryOptions options = new UploadDirectoryOptions()
    {
        Recursive = true
    };

    try
    {
        task = TransferManager.UploadDirectoryAsync(localDirectoryPath, blobDirectory, options, context, cancellationSource.Token);
        while(!task.IsCompleted)
        {
            if(Console.KeyAvailable)
            {
                keyinfo = Console.ReadKey(true);
                if(keyinfo.Key == ConsoleKey.C)
                {
                    cancellationSource.Cancel();
                }
            }
        }
        await task;
    }
    catch(Exception e)
    {
        Console.WriteLine("\nThe transfer is canceled: {0}", e.Message);
    }

    if(cancellationSource.IsCancellationRequested)
    {
        Console.WriteLine("\nTransfer will resume in 3 seconds...");
        Thread.Sleep(3000);
        checkpoint = context.LastCheckpoint;
        context = GetDirectoryTransferContext(checkpoint);
        Console.WriteLine("\nResuming transfer...\n");
        await TransferManager.UploadDirectoryAsync(localDirectoryPath, blobDirectory, options, context);
    }

    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

Bu yöntem ve tek bir dosyayı karşıya yüklemek için yöntemi arasında birkaç fark vardır. Şimdi `TransferManager.UploadDirectoryAsync` ve `getDirectoryTransferContext` daha önce oluşturduğumuz yöntemi kullanıyoruz. Ayrıca, karşıya `options` yükleme operasyonimize bir değer sağlıyoruz, bu da karşıya yüklememize alt dizinler eklemek istediğimizi göstermemizi sağlar.

## <a name="copy-a-file-from-url-to-a-blob"></a>URL 'den blob 'a dosya kopyalama

Şimdi de bir URL 'den Azure Blobuna bir dosya kopyalamamızı sağlayan bir kod ekleyelim.

Değiştir `TransferUrlToAzureBlob` :

```csharp
public static async Task TransferUrlToAzureBlob(CloudStorageAccount account)
{
    Uri uri = new Uri(GetSourcePath());
    CloudBlockBlob blob = GetBlob(account);
    TransferCheckpoint checkpoint = null;
    SingleTransferContext context = GetSingleTransferContext(checkpoint);
    CancellationTokenSource cancellationSource = new CancellationTokenSource();
    Console.WriteLine("\nTransfer started...\nPress 'c' to temporarily cancel your transfer...\n");

    Stopwatch stopWatch = Stopwatch.StartNew();
    Task task;
    ConsoleKeyInfo keyinfo;
    try
    {
        task = TransferManager.CopyAsync(uri, blob, true, null, context, cancellationSource.Token);
        while(!task.IsCompleted)
        {
            if(Console.KeyAvailable)
            {
                keyinfo = Console.ReadKey(true);
                if(keyinfo.Key == ConsoleKey.C)
                {
                    cancellationSource.Cancel();
                }
            }
        }
        await task;
    }
    catch(Exception e)
    {
        Console.WriteLine("\nThe transfer is canceled: {0}", e.Message);
    }

    if(cancellationSource.IsCancellationRequested)
    {
        Console.WriteLine("\nTransfer will resume in 3 seconds...");
        Thread.Sleep(3000);
        checkpoint = context.LastCheckpoint;
        context = GetSingleTransferContext(checkpoint);
        Console.WriteLine("\nResuming transfer...\n");
        await TransferManager.CopyAsync(uri, blob, true, null, context, cancellationSource.Token);
    }

    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

Bu özellik için önemli bir kullanım durumu, başka bir bulut hizmetinden (ör. AWS) Azure 'a veri taşımanız gerektiğinde olur. Kaynağa erişim sağlayan bir URL olduğu sürece, yöntemi kullanarak bu kaynağı kolayca Azure Bloblarına taşıyabilirsiniz `TransferManager.CopyAsync` . Bu yöntem, yeni bir Boolean parametresi de tanıtır. Bu parametreyi `true` , zaman uyumsuz bir sunucu tarafı kopyası yapmak istediğdiğimiz şekilde ayarlamak. Bu parametreyi `false` bir zaman uyumlu kopya olarak ayarlamak-kaynağın önce yerel makinenize indirildiğini, sonra da Azure Blob 'a yüklendiğini belirtir. Ancak zaman uyumlu kopyalama Şu anda yalnızca bir Azure Storage kaynağından diğerine kopyalama için kullanılabilir.

## <a name="copy-a-blob"></a>Blob kopyalama

Veri taşıma kitaplığı tarafından benzersiz şekilde sağlanmış olan başka bir özellik, bir Azure Storage kaynağından diğerine kopyalama yeteneğidir.

Değiştir `TransferAzureBlobToAzureBlob` :

```csharp
public static async Task TransferAzureBlobToAzureBlob(CloudStorageAccount account)
{
    CloudBlockBlob sourceBlob = GetBlob(account);
    CloudBlockBlob destinationBlob = GetBlob(account);
    TransferCheckpoint checkpoint = null;
    SingleTransferContext context = GetSingleTransferContext(checkpoint);
    CancellationTokenSource cancellationSource = new CancellationTokenSource();
    Console.WriteLine("\nTransfer started...\nPress 'c' to temporarily cancel your transfer...\n");

    Stopwatch stopWatch = Stopwatch.StartNew();
    Task task;
    ConsoleKeyInfo keyinfo;
    try
    {
        task = TransferManager.CopyAsync(sourceBlob, destinationBlob, CopyMethod.ServiceSideAsyncCopy, null, context, cancellationSource.Token);
        while(!task.IsCompleted)
        {
            if(Console.KeyAvailable)
            {
                keyinfo = Console.ReadKey(true);
                if(keyinfo.Key == ConsoleKey.C)
                {
                    cancellationSource.Cancel();
                }
            }
        }
        await task;
    }
    catch(Exception e)
    {
        Console.WriteLine("\nThe transfer is canceled: {0}", e.Message);
    }

    if(cancellationSource.IsCancellationRequested)
    {
        Console.WriteLine("\nTransfer will resume in 3 seconds...");
        Thread.Sleep(3000);
        checkpoint = context.LastCheckpoint;
        context = GetSingleTransferContext(checkpoint);
        Console.WriteLine("\nResuming transfer...\n");
        await TransferManager.CopyAsync(sourceBlob, destinationBlob, false, null, context, cancellationSource.Token);
    }

    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

Bu örnekte, ' deki Boolean parametresini, `TransferManager.CopyAsync` `false` zaman uyumlu bir kopya yapmak istediğdiğimiz belirtmek için olarak ayarlayacağız. Bu, kaynağın önce yerel makinenize indirileceği ve sonra Azure Blob 'a yüklendiği anlamına gelir. Zaman uyumlu kopyalama seçeneği, kopyalama işlemi için tutarlı bir hız olduğundan emin olmanın harika bir yoludur. Buna karşılık, zaman uyumsuz bir sunucu tarafı kopyasının hızı sunucudaki kullanılabilir ağ bant genişliğine bağımlıdır ve bu da dalgalanmasına sahip olabilir. Ancak zaman uyumlu kopya, zaman uyumsuz kopyaya kıyasla ek çıkış maliyeti oluşturabilir. Önerilen yaklaşım, çıkış maliyetini önlemek için kaynak depolama hesabınızla aynı bölgedeki bir Azure VM 'de zaman uyumlu kopyalama kullanmaktır.

Veri taşıma uygulaması artık tamamlanmıştır. [Tam kod örneği GitHub ' da kullanılabilir](https://github.com/azure-samples/storage-dotnet-data-movement-library-app).

## <a name="next-steps"></a>Sonraki adımlar

[Azure depolama veri taşıma kitaplığı başvuru belgeleri](https://azure.github.io/azure-storage-net-data-movement).

[!INCLUDE [storage-try-azure-tools-blobs](../../../includes/storage-try-azure-tools-blobs.md)]
