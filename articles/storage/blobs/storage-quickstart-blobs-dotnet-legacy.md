---
title: 'Hızlı başlangıç: .NET için Azure Blob depolama istemci kitaplığı'
description: Bu hızlı başlangıçta, blob (nesne) deposunda bir kapsayıcı ve BLOB oluşturmak için .NET için Azure Blob Storage istemci kitaplığı 'nı nasıl kullanacağınızı öğreneceksiniz. Ardından, blob’u yerel bilgisayarınıza indirmeyi ve bir kapsayıcıdaki tüm blobların listesini görüntülemeyi öğreneceksiniz.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 07/20/2019
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: ee95ccd30d0f2eddcca7327dc4e5b60a3efe7e64
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73825412"
---
# <a name="quickstart-azure-blob-storage-client-library-v11-for-net"></a>Hızlı başlangıç: .NET için Azure Blob depolama istemci kitaplığı v11

.NET için Azure Blob depolama istemci kitaplığı v11 ile çalışmaya başlayın. Azure Blob depolama, Microsoft 'un bulut için nesne depolama çözümüdür. Paketi yüklemek ve temel görevler için örnek kodu denemek için adımları izleyin. Blob depolama, çok miktarda yapılandırılmamış veriyi depolamak için iyileştirilmiştir.

.NET için Azure Blob depolama istemci kitaplığı 'nı kullanarak şunları yapın:

* Bir kapsayıcı oluşturma
* Bir kapsayıcıda izinleri ayarlama
* Azure depolama 'da blob oluşturma
* Blobu yerel bilgisayarınıza indirme
* Bir kapsayıcıdaki tüm Blobları listeleme
* Kapsayıcı silme

[API başvuru belgeleri](https://docs.microsoft.com/dotnet/api/overview/azure/storage?view=azure-dotnet) | [kitaplığı kaynak kodu](https://github.com/Azure/azure-storage-net/tree/master/Blob) | [paketi (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/) | [örnekleri](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=dotnet&term=blob)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Ön koşullar

* Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/)
* Azure depolama hesabı- [depolama hesabı oluşturma](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* İşletim sisteminiz için geçerli [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) . Çalışma zamanını değil, SDK 'Yı aldığınızdan emin olun.

## <a name="setting-up"></a>Ayarlanıyor

Bu bölümde, bir projeyi .NET için Azure Blob depolama istemci kitaplığıyla çalışacak şekilde hazırlama işlemi adım adım gösterilmektedir.

### <a name="create-the-project"></a>Proje oluşturma

İlk olarak, blob adlı bir .NET Core uygulaması oluşturun *-hızlı başlangıç*.

1. Konsol penceresinde (cmd, PowerShell veya Bash gibi), *BLOB-QuickStart*adlı yeni bir konsol uygulaması oluşturmak için `dotnet new` komutunu kullanın. Bu komut, tek bir kaynak dosyası olan C# basit bir "Merhaba Dünya" projesi oluşturur: *program.cs*.

   ```console
   dotnet new console -n blob-quickstart
   ```

2. Yeni oluşturulan *BLOB-hızlı başlangıç* klasörüne geçin ve bunların tümünün iyi olduğunu doğrulamak için uygulamayı oluşturun.

   ```console
   cd blob-quickstart
   ```

   ```console
   dotnet build
   ```

Derlemeden beklenen çıktı şuna benzemelidir:

```output
C:\QuickStarts\blob-quickstart> dotnet build
Microsoft (R) Build Engine version 16.0.450+ga8dc7f1d34 for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.

  Restore completed in 44.31 ms for C:\QuickStarts\blob-quickstart\blob-quickstart.csproj.
  blob-quickstart -> C:\QuickStarts\blob-quickstart\bin\Debug\netcoreapp2.1\blob-quickstart.dll

Build succeeded.
    0 Warning(s)
    0 Error(s)

Time Elapsed 00:00:03.08
```

### <a name="install-the-package"></a>Paketi yükler

Hala uygulama dizininde, `dotnet add package` komutunu kullanarak .NET için Azure Blob Storage istemci kitaplığı 'nı yükleyebilirsiniz.

```console
dotnet add package Microsoft.Azure.Storage.Blob
```

### <a name="set-up-the-app-framework"></a>Uygulama çerçevesini ayarlama

Proje dizininden:

1. *Program.cs* dosyasını Düzenleyicinizde açın
2. `Console.WriteLine` ifadesini kaldır
3. `using` yönergeleri ekleme
4. Örnek için ana kodun bulunacağı bir `ProcessAsync` yöntemi oluşturun
5. `ProcessAsync` yöntemi zaman uyumsuz olarak `Main` çağırın

Kod şu şekildedir:

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.Azure.Storage;
using Microsoft.Azure.Storage.Blob;

namespace blob_quickstart
{
    class Program
    {
        public static void Main()
        {
            Console.WriteLine("Azure Blob Storage - .NET quickstart sample\n");

            // Run the examples asynchronously, wait for the results before proceeding
            ProcessAsync().GetAwaiter().GetResult();

            Console.WriteLine("Press any key to exit the sample application.");
            Console.ReadLine();
        }

        private static async Task ProcessAsync()
        {
        }
    }
}
```

### <a name="copy-your-credentials-from-the-azure-portal"></a>Azure portalından kimlik bilgilerinizi kopyalama

Örnek uygulama, Azure depolama 'ya istek yaptığında yetkilendirilmiş olmalıdır. Bir isteği yetkilendirmek için, depolama hesabı kimlik bilgilerinizi uygulamaya bağlantı dizesi olarak ekleyin. Bu adımları izleyerek depolama hesabı kimlik bilgilerinizi görüntüleyin:

1. [Azure portalına](https://portal.azure.com) gidin.
2. Depolama hesabınızı bulun.
3. Depolama hesabına genel bakışın **Ayarlar** bölümünde **Erişim anahtarları**’nı seçin. Burada, hesap erişim anahtarlarınızı ve her anahtar için tam bağlantı dizesini görüntüleyebilirsiniz.
4. **key1** bölümünde **Bağlantı dizesi** değerini bulun ve **Kopyala** düğmesini seçerek bağlantı dizesini kopyalayın. Sonraki adımda bir ortam değişkenine bağlantı dizesini ekleyeceksiniz.

    ![Azure portalından bağlantı dizesinin kopyalanmasını gösteren ekran görüntüsü](../../../includes/media/storage-copy-connection-string-portal/portal-connection-string.png)

### <a name="configure-your-storage-connection-string"></a>Depolama bağlantı dizelerinizi yapılandırma

Bağlantı dizenizi kopyaladıktan sonra uygulamayı çalıştıran yerel makine üzerindeki yeni bir ortam değişkenine yazın. Ortam değişkenini ayarlamak için bir konsol penceresi açın ve işletim sisteminizin yönergelerini izleyin. `<yourconnectionstring>` gerçek bağlantı dizeniz ile değiştirin.

#### <a name="windows"></a>Windows

```cmd
setx CONNECT_STR "<yourconnectionstring>"
```

Windows 'a ortam değişkenini ekledikten sonra, komut penceresinin yeni bir örneğini başlatmanız gerekir.

#### <a name="linux"></a>Linux

```bash
export CONNECT_STR="<yourconnectionstring>"
```

#### <a name="macos"></a>MacOS

```bash
export CONNECT_STR="<yourconnectionstring>"
```

Ortam değişkenini ekledikten sonra, ortam değişkenini okumak için gereken tüm çalışan programları yeniden başlatın. Örneğin, devam etmeden önce geliştirme ortamınızı veya düzenleyiciyi yeniden başlatın.

## <a name="object-model"></a>Nesne modeli

Azure Blob depolama, büyük miktarlarda yapılandırılmamış verileri depolamak için iyileştirilmiştir. Yapılandırılmamış veriler, metin veya ikili veriler gibi belirli bir veri modeline veya tanımına bağlı olmayan bir veri. BLOB depolama üç tür kaynak sunar:

* Depolama hesabı.
* Depolama hesabındaki bir kapsayıcı
* Kapsayıcıda bir blob

Aşağıdaki diyagramda bu kaynaklar arasındaki ilişki gösterilmektedir.

![BLOB depolama mimarisi diyagramı](./media/storage-quickstart-blobs-dotnet/blob1.png)

Şu kaynaklarla etkileşim kurmak için aşağıdaki .NET sınıflarını kullanın:

* [Cloudstorageaccount](/dotnet/api/microsoft.azure.storage.cloudstorageaccount): `CloudStorageAccount` sınıfı, Azure depolama hesabınızı temsil eder. Bu sınıfı, hesap erişim anahtarlarınızı kullanarak blob depolamaya erişim yetkisi vermek için kullanın.
* [Cloudblobclient](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient): `CloudBlobClient` sınıfı, kodunuzdaki blob hizmetine bir erişim noktası sağlar.
* [Cloudblobcontainer](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer): `CloudBlobContainer` sınıfı kodunuzda bir blob kapsayıcısını temsil eder.
* [Cloudblockblob](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob): `CloudBlockBlob` nesnesi kodunuzda bir Blok Blobu temsil eder. Blok blobları, ayrı ayrı yönetilebilen veri bloklarından oluşur.

## <a name="code-examples"></a>Kod örnekleri

Bu örnek kod parçacıkları, .NET için Azure Blob depolama istemci kitaplığı ile aşağıdakilerin nasıl gerçekleştirileceğini göstermektedir:

   * [İstemcinin kimliğini doğrulama](#authenticate-the-client)
   * [Kapsayıcı oluşturma](#create-a-container)
   * [Bir kapsayıcıda izinleri ayarlama](#set-permissions-on-a-container)
   * [Blobları bir kapsayıcıya yükleme](#upload-blobs-to-a-container)
   * [Bir kapsayıcıdaki Blobları listeleme](#list-the-blobs-in-a-container)
   * [Blob 'ları indir](#download-blobs)
   * [Kapsayıcı silme](#delete-a-container)

### <a name="authenticate-the-client"></a>İstemcinin kimliğini doğrulama

Aşağıdaki kod, ortam değişkeninin depolama hesabına işaret eden bir [Cloudstorageaccount](/dotnet/api/microsoft.azure.storage.cloudstorageaccount?view=azure-dotnet) nesnesi oluşturmak için ayrıştırılabilen bir bağlantı dizesi içerip içermediğini denetler. Bağlantı dizesinin geçerli olup olmadığını denetlemek için [TryParse](/dotnet/api/microsoft.azure.storage.cloudstorageaccount.tryparse?view=azure-dotnet) yöntemini kullanın. `TryParse` başarılı olursa, `storageAccount` değişkenini başlatır ve `true`döndürür.

Bu kodu `ProcessAsync` yönteminin içine ekleyin:

```csharp
// Retrieve the connection string for use with the application. The storage 
// connection string is stored in an environment variable on the machine 
// running the application called CONNECT_STR. If the 
// environment variable is created after the application is launched in a 
// console or with Visual Studio, the shell or application needs to be closed
// and reloaded to take the environment variable into account.
string storageConnectionString = Environment.GetEnvironmentVariable("CONNECT_STR");

// Check whether the connection string can be parsed.
CloudStorageAccount storageAccount;
if (CloudStorageAccount.TryParse(storageConnectionString, out storageAccount))
{
    // If the connection string is valid, proceed with operations against Blob
    // storage here.
    // ADD OTHER OPERATIONS HERE
}
else
{
    // Otherwise, let the user know that they need to define the environment variable.
    Console.WriteLine(
        "A connection string has not been defined in the system environment variables. " +
        "Add an environment variable named 'CONNECT_STR' with your storage " +
        "connection string as a value.");
    Console.WriteLine("Press any key to exit the application.");
    Console.ReadLine();
}
```

> [!NOTE]
> Bu makaledeki işlemlerin geri kalanını gerçekleştirmek için yukarıdaki koddaki `// ADD OTHER OPERATIONS HERE` aşağıdaki bölümlerdeki kod parçacıkları ile değiştirin.

### <a name="create-a-container"></a>Bir kapsayıcı oluşturma

Kapsayıcıyı oluşturmak için öncelikle [CloudBlobClient](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient) nesnesinin depolama hesabınızdaki Blob depolama alanına işaret eden bir örneğini oluşturun. Ardından, [CloudBlobContainer](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer) nesnesinin bir örneğini ve sonra kapsayıcıyı oluşturun.

Bu durumda, kod kapsayıcıyı oluşturmak için [Createasync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createasync) yöntemini çağırır. Kapsayıcı adının benzersiz olduğundan emin olmak için kapsayıcı adına bir GUID değeri eklenir. Bir üretim ortamında, yalnızca henüz yoksa bir kapsayıcı oluşturmak için [Createifnotexistsasync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createifnotexistsasync) yönteminin kullanılması tercih edilir.

> [!IMPORTANT]
> Kapsayıcı adlarının küçük harfle yazılması gerekir. Kapsayıcıları ve blobları adlandırma hakkında daha fazla bilgi için bkz. [Kapsayıcıları, Blobları ve Meta Verileri Adlandırma ve Bunlara Başvurma](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

```csharp
// Create the CloudBlobClient that represents the 
// Blob storage endpoint for the storage account.
CloudBlobClient cloudBlobClient = storageAccount.CreateCloudBlobClient();

// Create a container called 'quickstartblobs' and 
// append a GUID value to it to make the name unique.
CloudBlobContainer cloudBlobContainer = 
    cloudBlobClient.GetContainerReference("quickstartblobs" + 
        Guid.NewGuid().ToString());
await cloudBlobContainer.CreateAsync();
```

### <a name="set-permissions-on-a-container"></a>Bir kapsayıcıda izinleri ayarlama

Kapsayıcıdaki tüm Bloblar herkese açık olacak şekilde kapsayıcı üzerindeki izinleri ayarlayın. Bir blob herkese açık ise, herhangi bir istemci tarafından anonim olarak erişilebilir.

```csharp
// Set the permissions so the blobs are public.
BlobContainerPermissions permissions = new BlobContainerPermissions
{
    PublicAccess = BlobContainerPublicAccessType.Blob
};
await cloudBlobContainer.SetPermissionsAsync(permissions);
```

### <a name="upload-blobs-to-a-container"></a>Blobları bir kapsayıcıya yükleme

Aşağıdaki kod parçacığı, önceki bölümde oluşturulan kapsayıcıda [Getblockblobreference](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getblockblobreference) yöntemini çağırarak bir `CloudBlockBlob` nesnesine bir başvuru alır. Ardından, [Uploadfromfileasync](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.uploadfromfileasync) yöntemini çağırarak seçili yerel dosyayı bloba yükler. Bu yöntem, daha önce oluşturulmadıysa bir blob oluşturur, aksi takdirde üzerine yazar.

```csharp
// Create a file in your local MyDocuments folder to upload to a blob.
string localPath = Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments);
string localFileName = "QuickStart_" + Guid.NewGuid().ToString() + ".txt";
string sourceFile = Path.Combine(localPath, localFileName);
// Write text to the file.
File.WriteAllText(sourceFile, "Hello, World!");

Console.WriteLine("Temp file = {0}", sourceFile);
Console.WriteLine("Uploading to Blob storage as blob '{0}'", localFileName);

// Get a reference to the blob address, then upload the file to the blob.
// Use the value of localFileName for the blob name.
CloudBlockBlob cloudBlockBlob = cloudBlobContainer.GetBlockBlobReference(localFileName);
await cloudBlockBlob.UploadFromFileAsync(sourceFile);
```

### <a name="list-the-blobs-in-a-container"></a>Blob’ları bir kapsayıcıda listeleme

[ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmentedasync) yöntemini kullanarak kapsayıcıdaki Blobları listeleyin. Bu durumda, kapsayıcıya yalnızca bir blob eklenmiş, bu nedenle listeleme işlemi yalnızca bir BLOB döndürüyor.

Tek bir çağrıda döndürülecek çok fazla BLOB varsa (varsayılan olarak 5000 ' den fazla), `ListBlobsSegmentedAsync` yöntemi Toplam sonuç kümesinin ve devamlılık belirtecinin bir segmentini döndürür. Blobların sonraki segmentini almak için, devamlılık belirteci null olana kadar önceki çağrı tarafından döndürülen devamlılık belirtecini art arda sağlayın. Null devamlılık belirteci tüm blobların alındığını gösterir. Kod, en iyi uygulamalar için devamlılık belirtecinin nasıl kullanılacağını gösterir.

```csharp
// List the blobs in the container.
Console.WriteLine("List blobs in container.");
BlobContinuationToken blobContinuationToken = null;
do
{
    var results = await cloudBlobContainer.ListBlobsSegmentedAsync(null, blobContinuationToken);
    // Get the value of the continuation token returned by the listing call.
    blobContinuationToken = results.ContinuationToken;
    foreach (IListBlobItem item in results.Results)
    {
        Console.WriteLine(item.Uri);
    }
} while (blobContinuationToken != null); // Loop while the continuation token is not null.

```

### <a name="download-blobs"></a>Blob’ları indirme

Daha önce oluşturulan blobu, [Downloadtofileasync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.downloadtofileasync) yöntemini kullanarak yerel dosya sisteminize indirin. Örnek kod, yerel dosya sisteminde her iki dosyayı da görebilmeniz için blob adına "_DOWNLOADED" sonekini ekler.

```csharp
// Download the blob to a local file, using the reference created earlier.
// Append the string "_DOWNLOADED" before the .txt extension so that you 
// can see both files in MyDocuments.
string destinationFile = sourceFile.Replace(".txt", "_DOWNLOADED.txt");
Console.WriteLine("Downloading blob to {0}", destinationFile);
await cloudBlockBlob.DownloadToFileAsync(destinationFile, FileMode.Create);
```

### <a name="delete-a-container"></a>Kapsayıcı silme

Aşağıdaki kod, [Cloudblobcontainer. DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteasync)kullanarak tüm kapsayıcıyı silerek uygulamanın oluşturduğu kaynakları temizler. Ayrıca isterseniz yerel dosyaları silebilirsiniz.

```csharp
Console.WriteLine("Press the 'Enter' key to delete the example files, " +
    "example container, and exit the application.");
Console.ReadLine();
// Clean up resources. This includes the container and the two temp files.
Console.WriteLine("Deleting the container");
if (cloudBlobContainer != null)
{
    await cloudBlobContainer.DeleteIfExistsAsync();
}
Console.WriteLine("Deleting the source, and downloaded files");
File.Delete(sourceFile);
File.Delete(destinationFile);
```

## <a name="run-the-code"></a>Kodu çalıştırma

Bu uygulama yerel *MyDocuments* klasörünüzde bir sınama dosyası oluşturur ve BLOB depolama alanına yükler. Örnek daha sonra kapsayıcıdaki Blobları listeler ve eski ve yeni dosyaları karşılaştırabilmeniz için dosyayı yeni bir adla indirir.

Uygulama dizininize gidip uygulamayı derleyin ve çalıştırın.

```console
dotnet build
```

```console
dotnet run
```

Uygulamanın çıktısı aşağıdaki örneğe benzer:

```output
Azure Blob storage - .NET Quickstart example

Created container 'quickstartblobs33c90d2a-eabd-4236-958b-5cc5949e731f'

Temp file = C:\Users\myusername\Documents\QuickStart_c5e7f24f-a7f8-4926-a9da-96
97c748f4db.txt
Uploading to Blob storage as blob 'QuickStart_c5e7f24f-a7f8-4926-a9da-9697c748f
4db.txt'

Listing blobs in container.
https://storagesamples.blob.core.windows.net/quickstartblobs33c90d2a-eabd-4236-
958b-5cc5949e731f/QuickStart_c5e7f24f-a7f8-4926-a9da-9697c748f4db.txt

Downloading blob to C:\Users\myusername\Documents\QuickStart_c5e7f24f-a7f8-4926
-a9da-9697c748f4db_DOWNLOADED.txt

Press any key to delete the example files and example container.
```

Devam etmek için **Enter** tuşuna bastığınızda uygulama, depolama kapsayıcısını ve dosyaları siler. Silmeden önce, *MyDocuments* klasörünüzde iki dosyayı denetleyin. Dosyaları açarak aynı olduklarını görebilirsiniz. Konsol penceresinden blob URL’sini kopyalayıp tarayıcıya yapıştırarak blobun içeriğini görüntüleyin.

Dosyaları doğruladıktan sonra, tanıtımı tamamlamak ve sınama dosyalarını silmek için herhangi bir tuşa basın.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta .NET kullanarak blobları karşıya yükleme, indirme ve listeleme hakkında bilgi edindiniz.

Blob depolamaya görüntü yükleyen bir Web uygulamasının nasıl oluşturulacağını öğrenmek için şu işleme devam edin:

> [!div class="nextstepaction"]
> [Bir görüntüyü karşıya yükleme ve işleme](storage-upload-process-images.md)

* .NET Core hakkında daha fazla bilgi için bkz. [10 dakika içinde .NET kullanmaya başlama](https://www.microsoft.com/net/learn/get-started/).
* Windows için Visual Studio’dan dağıtabileceğiniz örnek bir uygulamayı incelemek için bkz. [Azure Blob Depolama ile .NET Fotoğraf Galerisi Web Uygulaması Örneği](https://azure.microsoft.com/resources/samples/storage-blobs-dotnet-webapp/).
