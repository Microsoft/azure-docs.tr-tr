---
title: .NET ile Azure Dosyaları için geliştirme | Microsoft Docs
description: Verileri depolamak için Azure dosyaları 'nı kullanan .NET uygulamaları ve hizmetlerini geliştirmeyi öğrenin.
author: roygara
ms.service: storage
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 10/02/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-csharp
ms.openlocfilehash: 2c00f001ae3cba9420a137a42f9f696619584d50
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107817386"
---
# <a name="develop-for-azure-files-with-net"></a>.NET ile Azure Dosyaları için geliştirme

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

Verileri depolamak için [Azure dosyaları](storage-files-introduction.md) 'nı kullanan .NET uygulamaları geliştirmeye ilişkin temel bilgileri öğrenin. Bu makalede, .NET ve Azure dosyaları ile aşağıdakileri yapmak üzere basit bir konsol uygulamasının nasıl oluşturulacağı gösterilmektedir:

- Bir dosyanın içeriğini alın.
- Dosya paylaşımının en büyük boyutunu veya kotasını ayarlayın.
- Bir dosya için paylaşılan erişim imzası (SAS) oluşturun.
- Bir dosyayı aynı depolama hesabındaki başka bir dosyaya kopyalama.
- Bir dosyayı aynı depolama hesabındaki bir bloba kopyalama.
- Dosya paylaşımının anlık görüntüsünü oluşturun.
- Paylaşma anlık görüntüsünden bir dosyayı geri yükleyin.
- Sorun giderme için Azure depolama ölçümlerini kullanın.

Azure dosyaları hakkında daha fazla bilgi edinmek için bkz. [Azure dosyaları nedir?](storage-files-introduction.md)

[!INCLUDE [storage-check-out-samples-dotnet](../../../includes/storage-check-out-samples-dotnet.md)]

## <a name="understanding-the-net-apis"></a>.NET API'lerini anlama

Azure Dosyaları istemci uygulamalarına iki geniş yaklaşım sağlar: Sunucu İleti Bloğu (SMB) ve REST. .NET içinde, `System.IO` ve `Azure.Storage.Files.Shares` API 'leri bu yaklaşımları soyutlar.

API | Kullanılması gereken durumlar | Notlar
----|-------------|------
[System.IO](/dotnet/api/system.io) | Uygulamanız: <ul><li>SMB kullanarak dosyaları okuma/yazma gerekir</li><li>Azure Dosyaları hesabınıza 445 bağlantı noktası üzerinden erişimi olan bir cihazda çalışıyor</li><li>Dosya paylaşımının yönetim ayarlarından herhangi birini yönetmesi gerekmiyor</li></ul> | SMB üzerinden Azure dosyaları ile uygulanan dosya g/ç, genellikle herhangi bir ağ dosya paylaşımıyla veya yerel depolama cihazındaki g/ç ile aynıdır. .NET 'teki dosya g/ç dahil olmak üzere çeşitli özelliklere giriş için, bkz. [konsol uygulaması](/dotnet/csharp/tutorials/console-teleprompter) öğreticisi.
[Azure. Storage. Files. paylaşımlar](/dotnet/api/azure.storage.files.shares) | Uygulamanız: <ul><li>Güvenlik duvarı veya ISS kısıtlamaları nedeniyle 445 numaralı bağlantı noktasında SMB kullanılarak Azure dosyalarına erişilemiyor</li><li>Bir dosya paylaşımının kotasını ayarlama veya paylaşılan bir erişim imzası oluşturma gibi yönetim işlevleri gerektiriyor</li></ul> | Bu makalede, dosya `Azure.Storage.Files.Shares` PAYLAŞıMıNıN SMB ve yönetimi yerıne Rest kullanan dosya g/ç için kullanımı gösterilmektedir.

## <a name="create-the-console-application-and-obtain-the-assembly"></a>Konsol uygulaması oluşturma ve derleme alma

Azure dosyaları istemci kitaplığını herhangi bir tür .NET uygulamasında kullanabilirsiniz. Bu uygulamalar Azure bulutu, Web, masaüstü ve mobil uygulamaları içerir. Bu kılavuzda basitlik için bir konsol uygulaması oluşturacağız.

Visual Studio'da yeni bir Windows konsol uygulaması oluşturun. Aşağıdaki adımlarda, Visual Studio 2019 ' de bir konsol uygulamasının nasıl oluşturulacağı gösterilmektedir. Adımlar Visual Studio’nun diğer sürümlerinde de benzerdir.

1. Visual Studio 'Yu başlatın ve **Yeni proje oluştur**' u seçin.
1. **Yeni proje oluştur** bölümünde C# için **konsol uygulaması (.NET Framework)** öğesini seçin ve ardından **İleri**' yi seçin.
1. **Yeni projenizi yapılandırın** bölümünde uygulama için bir ad girin ve **Oluştur**' u seçin.

Bu makaledeki tüm kod örneklerini `Program` *program. cs* dosyasındaki sınıfına ekleyin.

## <a name="use-nuget-to-install-the-required-packages"></a>Gereken paketleri yüklemek için NuGet kullanma

Projenizdeki bu paketlere başvurun:

# <a name="azure-net-sdk-v12"></a>[Azure \. NET SDK V12](#tab/dotnet)

- [.Net Için Azure çekirdek kitaplığı](https://www.nuget.org/packages/Azure.Core/): Bu paket, Azure istemci işlem hattının uygulamasıdır.
- [.NET için Azure Depolama Blobu istemci kitaplığı](https://www.nuget.org/packages/Azure.Storage.Blobs/): Bu paket, Depolama hesabınızdaki blob kaynaklarına programlı erişim sağlar.
- [.Net Için Azure depolama dosyaları istemci kitaplığı](https://www.nuget.org/packages/Azure.Storage.Files.Shares/): Bu paket, Depolama hesabınızdaki dosya kaynaklarına programlı erişim sağlar.
- [.Net Için sistem Configuration Manager kitaplığı](https://www.nuget.org/packages/System.Configuration.ConfigurationManager/): Bu paket, bir yapılandırma dosyasında değerleri depolayan ve almak için bir sınıf sağlar.

Paketleri edinmek için NuGet kullanabilirsiniz. Şu adımları izleyin:

1. **Çözüm Gezgini**, projenize sağ tıklayın ve **NuGet Paketlerini Yönet**' i seçin.
1. **NuGet Paket Yöneticisi**' nde, **Araştır**' ı seçin. Sonra **Azure. Core**' u arayıp seçin ve ardından **Install**' ı seçin.

   Bu adım paketini ve bağımlılıklarını yüklenir.

1. Bu paketleri arayın ve yükler:

   - **Azure. Storage. blob 'Ları**
   - **Azure. Storage. Files. paylaşımlar**
   - **System.Configuration.ConfigurationManager**

# <a name="azure-net-sdk-v11"></a>[Azure \. NET SDK v11](#tab/dotnetv11)

- [.NET için Microsoft Azure depolama ortak kitaplığı](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/): Bu paket, Depolama hesabınızdaki ortak kaynaklara programlı erişim sağlar.
- [.NET için Microsoft Azure depolama blob kitaplığı](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/): Bu paket, Depolama hesabınızdaki blob kaynaklarına programlı erişim sağlar.
- [.NET için Microsoft Azure depolama dosya kitaplığı](https://www.nuget.org/packages/Microsoft.Azure.Storage.File/): Bu paket, Depolama hesabınızdaki dosya kaynaklarına programlı erişim sağlar.
- [.NET için Microsoft Azure Configuration Manager kitaplığı](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/): Bu paket, uygulamanızın çalıştığı her yerde yapılandırma dosyasında bir bağlantı dizesini ayrıştırmak için bir sınıf sağlar.

Paketleri edinmek için NuGet kullanabilirsiniz. Şu adımları izleyin:

1. **Çözüm Gezgini**, projenize sağ tıklayın ve **NuGet Paketlerini Yönet**' i seçin.
1. **NuGet Paket Yöneticisi**' nde, **Araştır**' ı seçin. Sonra **Microsoft. Azure. Storage. blob**' u arayıp seçin ve ardından **Install**' ı seçin.

   Bu adım paketini ve bağımlılıklarını yüklenir.
1. Bu paketleri arayın ve yükler:

   - **Microsoft. Azure. Storage. Common**
   - **Microsoft. Azure. Storage. File**
   - **Microsoft.Azure.ConfigurationManager**

---

## <a name="save-your-storage-account-credentials-to-the-appconfig-file"></a>Depolama hesabı kimlik bilgilerinizi App.config dosyasına kaydedin

Ardından, kimlik bilgilerinizi projenizin *App.config* dosyasına kaydedin. **Çözüm Gezgini**, `App.config` dosyayı aşağıdaki örneğe benzer olacak şekilde çift tıklayın ve düzenleyin.

# <a name="azure-net-sdk-v12"></a>[Azure \. NET SDK V12](#tab/dotnet)

`myaccount`Depolama hesabı adınızla ve `mykey` depolama hesabı anahtarınızla değiştirin.

:::code language="xml" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/app.config" highlight="5,6,7":::

# <a name="azure-net-sdk-v11"></a>[Azure \. NET SDK v11](#tab/dotnetv11)

`myaccount`Depolama hesabı adınızla ve `StorageAccountKeyEndingIn==` depolama hesabı anahtarınızla değiştirin.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <startup>
    <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
  </startup>
  <appSettings>
    <add key="StorageConnectionString"
      value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=StorageAccountKeyEndingIn==" />
  </appSettings>
</configuration>
```

---

> [!NOTE]
> Azurite depolama öykünücüsü Şu anda Azure dosyalarını desteklemiyor. Bağlantı dizeniz, Azure dosyaları ile çalışmak için buluttaki bir Azure Depolama hesabını hedeflemelidir.

## <a name="add-using-directives"></a>Using yönergeleri ekleme

**Çözüm Gezgini**, *program. cs* dosyasını açın ve aşağıdaki using yönergelerini dosyanın en üstüne ekleyin.

# <a name="azure-net-sdk-v12"></a>[Azure \. NET SDK V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_UsingStatements":::

# <a name="azure-net-sdk-v11"></a>[Azure \. NET SDK v11](#tab/dotnetv11)

```csharp
using Microsoft.Azure; // Namespace for Azure Configuration Manager
using Microsoft.Azure.Storage; // Namespace for Storage Client Library
using Microsoft.Azure.Storage.Blob; // Namespace for Azure Blobs
using Microsoft.Azure.Storage.File; // Namespace for Azure Files
```

[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

---

## <a name="access-the-file-share-programmatically"></a>Dosya paylaşımına programlamayla erişme

*Program. cs* dosyasında dosya paylaşımında program aracılığıyla erişmek için aşağıdaki kodu ekleyin.

# <a name="azure-net-sdk-v12"></a>[Azure \. NET SDK V12](#tab/dotnet)

Aşağıdaki yöntem, zaten yoksa bir dosya paylaşma oluşturur. Yöntemi bir bağlantı dizesinden bir [parça eClient](/dotnet/api/azure.storage.files.shares.shareclient) nesnesi oluşturarak başlar. Örnek daha sonra daha önce oluşturduğumuz bir dosyayı indirmeye çalışır. Bu yöntemi kaynağından çağırın `Main()` .

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_CreateShare":::

# <a name="azure-net-sdk-v11"></a>[Azure \. NET SDK v11](#tab/dotnetv11)

Sonra, `Main()` bağlantı dizesini almak için yukarıda gösterilen koddan sonra yöntemine aşağıdaki içeriği ekleyin. Bu kod, daha önce oluşturduğumuz dosyaya bir başvuru alır ve içeriğini çıkarır.

```csharp
// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();

    // Get a reference to the directory we created previously.
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");

    // Ensure that the directory exists.
    if (sampleDir.Exists())
    {
        // Get a reference to the file we created previously.
        CloudFile file = sampleDir.GetFileReference("Log1.txt");

        // Ensure that the file exists.
        if (file.Exists())
        {
            // Write the contents of the file to the console window.
            Console.WriteLine(file.DownloadTextAsync().Result);
        }
    }
}
```

Çıkışı görmek konsol uygulamasını çalıştırın.

---

## <a name="set-the-maximum-size-for-a-file-share"></a>Dosya paylaşımı için boyut üst sınırını ayarlama

Azure dosyaları istemci kitaplığı 'nın 5. x sürümünden başlayarak, bir dosya paylaşımının kotasını (en büyük boyut) ayarlayabilirsiniz. Paylaşımda halihazırda ne kadar verinin depolandığını da kontrol edebilirsiniz.

Bir paylaşımın kotasının ayarlanması, paylaşımda depolanan dosyaların toplam boyutunu sınırlar. Paylaşımdaki dosyaların toplam boyutu kotayı aşarsa, istemciler mevcut dosyaların boyutunu artırabilir. İstemciler ayrıca, bu dosyalar boş olmadığı takdirde yeni dosyalar oluşturamaz.

Aşağıdaki örnekte, paylaşımdaki mevcut kullanımını nasıl kontrol edileceği veya paylaşım için nasıl kota ayarlanacağı gösterilmiştir.

# <a name="azure-net-sdk-v12"></a>[Azure \. NET SDK V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_SetMaxShareSize":::

# <a name="azure-net-sdk-v11"></a>[Azure \. NET SDK v11](#tab/dotnetv11)

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Check current usage stats for the share.
    // Note that the ShareStats object is part of the protocol layer for the File service.
    Microsoft.Azure.Storage.File.Protocol.ShareStats stats = share.GetStats();
    Console.WriteLine("Current share usage: {0} GB", stats.Usage.ToString());

    // Specify the maximum size of the share, in GB.
    // This line sets the quota to be 10 GB greater than the current usage of the share.
    share.Properties.Quota = 10 + stats.Usage;
    share.SetProperties();

    // Now check the quota for the share. Call FetchAttributes() to populate the share's properties.
    share.FetchAttributes();
    Console.WriteLine("Current share quota: {0} GB", share.Properties.Quota);
}
```

---

### <a name="generate-a-shared-access-signature-for-a-file-or-file-share"></a>Dosya veya dosya paylaşımı için paylaşılan erişim imzası oluşturma

Azure dosyaları istemci kitaplığı 'nın 5. x sürümünden başlayarak, bir dosya paylaşımında veya tek bir dosya için paylaşılan erişim imzası (SAS) oluşturabilirsiniz.

# <a name="azure-net-sdk-v12"></a>[Azure \. NET SDK V12](#tab/dotnet)

Aşağıdaki örnek yöntemi belirtilen paylaşımdaki bir dosya üzerinde bir SAS döndürür.

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_GetFileSasUri":::

# <a name="azure-net-sdk-v11"></a>[Azure \. NET SDK v11](#tab/dotnetv11)

Paylaşılan erişim imzalarını yönetmek için, bir dosya paylaşımında depolanan erişim ilkesi de oluşturabilirsiniz. Güvenlik açığı varsa SAS 'yi iptal etmenizi sağladığından, depolanan bir erişim ilkesi oluşturmanızı öneririz. Aşağıdaki örnek, bir paylaşımda depolanan erişim ilkesi oluşturur. Örnek, paylaşımdaki bir dosyadaki bir SAS için kısıtlamalar sağlamak üzere bu ilkeyi kullanır.

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    string policyName = "sampleSharePolicy" + DateTime.UtcNow.Ticks;

    // Create a new stored access policy and define its constraints.
    SharedAccessFilePolicy sharedPolicy = new SharedAccessFilePolicy()
        {
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessFilePermissions.Read | SharedAccessFilePermissions.Write
        };

    // Get existing permissions for the share.
    FileSharePermissions permissions = share.GetPermissions();

    // Add the stored access policy to the share's policies. Note that each policy must have a unique name.
    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
    share.SetPermissions(permissions);

    // Generate a SAS for a file in the share and associate this access policy with it.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");
    CloudFile file = sampleDir.GetFileReference("Log1.txt");
    string sasToken = file.GetSharedAccessSignature(null, policyName);
    Uri fileSasUri = new Uri(file.StorageUri.PrimaryUri.ToString() + sasToken);

    // Create a new CloudFile object from the SAS, and write some text to the file.
    CloudFile fileSas = new CloudFile(fileSasUri);
    fileSas.UploadText("This write operation is authorized via SAS.");
    Console.WriteLine(fileSas.DownloadText());
}
```

---

Paylaşılan erişim imzaları oluşturma ve kullanma hakkında daha fazla bilgi için bkz. [paylaşılan erişim Imzası nasıl kullanılır](../common/storage-sas-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#how-a-shared-access-signature-works).

## <a name="copy-files"></a>Dosyaları kopyalama

Azure dosyaları istemci kitaplığı 'nın 5. x sürümünden başlayarak, bir dosyayı başka bir dosyaya, bir bloba bir dosyaya veya bir bloba bir dosyaya kopyalayabilirsiniz.

AzCopy komutunu Ayrıca bir dosyayı başka bir dosyaya kopyalamak veya bir blobu bir dosyaya ya da başka bir yolla kopyalamak için de kullanabilirsiniz. Bkz. [AzCopy ile çalışmaya başlama](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

> [!NOTE]
> Bir blobu dosyaya veya bir dosyayı bloba kopyalamak için aynı depolama hesabında kopyalama yapıyor olsanız da kaynak nesnesi erişimini yetkilendirmek amacıyla bir paylaşılan erişim imzası (SAS) kullanmanız gerekir.

### <a name="copy-a-file-to-another-file"></a>Dosyayı başka bir dosyaya kopyalama

Aşağıdaki örnekte, bir dosya aynı paylaşımdaki başka bir dosyaya kopyalanır. Bu işlem dosyaları aynı depolama hesabında kopyalayacağından kopyayı yapmak için [paylaşılan anahtar kimlik doğrulamasını](/rest/api/storageservices/authorize-with-shared-key) kullanabilirsiniz.

# <a name="azure-net-sdk-v12"></a>[Azure \. NET SDK V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_CopyFile":::

# <a name="azure-net-sdk-v11"></a>[Azure \. NET SDK v11](#tab/dotnetv11)

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();

    // Get a reference to the directory we created previously.
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");

    // Ensure that the directory exists.
    if (sampleDir.Exists())
    {
        // Get a reference to the file we created previously.
        CloudFile sourceFile = sampleDir.GetFileReference("Log1.txt");

        // Ensure that the source file exists.
        if (sourceFile.Exists())
        {
            // Get a reference to the destination file.
            CloudFile destFile = sampleDir.GetFileReference("Log1Copy.txt");

            // Start the copy operation.
            destFile.StartCopy(sourceFile);

            // Write the contents of the destination file to the console window.
            Console.WriteLine(destFile.DownloadText());
        }
    }
}
```

---

### <a name="copy-a-file-to-a-blob"></a>Dosyayı bir bloba kopyalama

Aşağıdaki örnekte, bir dosya oluşturulur ve aynı depolama hesabındaki bir bloba kopyalanır. Örnekte, kaynak dosya için hizmetin kopyalama sırasında kaynak dosyaya erişimi yetkilendirmek üzere kullandığı bir SAS oluşturulur.

# <a name="azure-net-sdk-v12"></a>[Azure \. NET SDK V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_CopyFileToBlob":::

# <a name="azure-net-sdk-v11"></a>[Azure \. NET SDK v11](#tab/dotnetv11)

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Create a new file share, if it does not already exist.
CloudFileShare share = fileClient.GetShareReference("sample-share");
share.CreateIfNotExists();

// Create a new file in the root directory.
CloudFile sourceFile = share.GetRootDirectoryReference().GetFileReference("sample-file.txt");
sourceFile.UploadText("A sample file in the root directory.");

// Get a reference to the blob to which the file will be copied.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
CloudBlobContainer container = blobClient.GetContainerReference("sample-container");
container.CreateIfNotExists();
CloudBlockBlob destBlob = container.GetBlockBlobReference("sample-blob.txt");

// Create a SAS for the file that's valid for 24 hours.
// Note that when you are copying a file to a blob, or a blob to a file, you must use a SAS
// to authorize access to the source object, even if you are copying within the same
// storage account.
string fileSas = sourceFile.GetSharedAccessSignature(new SharedAccessFilePolicy()
{
    // Only read permissions are required for the source file.
    Permissions = SharedAccessFilePermissions.Read,
    SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24)
});

// Construct the URI to the source file, including the SAS token.
Uri fileSasUri = new Uri(sourceFile.StorageUri.PrimaryUri.ToString() + fileSas);

// Copy the file to the blob.
destBlob.StartCopy(fileSasUri);

// Write the contents of the file to the console window.
Console.WriteLine("Source file contents: {0}", sourceFile.DownloadText());
Console.WriteLine("Destination blob contents: {0}", destBlob.DownloadText());
```

---

Aynı şekilde, bir blobu bir dosyaya kopyalayabilirsiniz. Kaynak dosya bir blob ise, kopyalama sırasında bu bloba erişimi yetkilendirmesi için bir SAS oluşturun.

## <a name="share-snapshots"></a>Anlık görüntü paylaşma

Azure dosyaları istemci kitaplığı sürüm 8,5 ' den başlayarak bir paylaşma anlık görüntüsü oluşturabilirsiniz. Ayrıca paylaşım anlık görüntülerini listeleyebilir, onlara göz atabilir ve paylaşım anlık görüntülerini silebilirsiniz. Oluşturulduktan sonra, paylaşma anlık görüntüleri salt okunurdur.

### <a name="create-share-snapshots"></a>Paylaşım anlık görüntüsü oluşturma

Aşağıdaki örnekte dosya paylaşım anlık görüntüsü oluşturulmaktadır.

# <a name="azure-net-sdk-v12"></a>[Azure \. NET SDK V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_CreateShareSnapshot":::

# <a name="azure-net-sdk-v11"></a>[Azure \. NET SDK v11](#tab/dotnetv11)

```csharp
storageAccount = CloudStorageAccount.Parse(ConnectionString); 
fClient = storageAccount.CreateCloudFileClient(); 
string baseShareName = "myazurefileshare"; 
CloudFileShare myShare = fClient.GetShareReference(baseShareName); 
var snapshotShare = myShare.Snapshot();

```

---

### <a name="list-share-snapshots"></a>Paylaşım anlık görüntülerini listeleme

Aşağıdaki örnek, bir paylaşımdaki anlık görüntüleri listeler.

# <a name="azure-net-sdk-v12"></a>[Azure \. NET SDK V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_ListShareSnapshots":::

# <a name="azure-net-sdk-v11"></a>[Azure \. NET SDK v11](#tab/dotnetv11)

```csharp
var shares = fClient.ListShares(baseShareName, ShareListingDetails.All);
```

---

### <a name="list-files-and-directories-within-share-snapshots"></a>Dosya ve dizinleri paylaşılan anlık görüntüler içinde Listele

Aşağıdaki örnek, paylaşılan anlık görüntüler içindeki dosyalara ve dizinlere göz atar.

# <a name="azure-net-sdk-v12"></a>[Azure \. NET SDK V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_ListSnapshotContents":::

# <a name="azure-net-sdk-v11"></a>[Azure \. NET SDK v11](#tab/dotnetv11)

```csharp
CloudFileShare mySnapshot = fClient.GetShareReference(baseShareName, snapshotTime); 
var rootDirectory = mySnapshot.GetRootDirectoryReference(); 
var items = rootDirectory.ListFilesAndDirectories();
```

---

### <a name="restore-file-shares-or-files-from-share-snapshots"></a>Dosya paylaşımlarını veya dosyaları paylaşım anlık görüntülerinden geri yükleme

Bir dosya paylaşımının anlık görüntüsünü almak, tek tek dosyaları veya tüm dosya paylaşımının kurtarılmasına olanak sağlar.

Bir dosya paylaşımının paylaşım anlık görüntülerini sorgulayarak bir dosya paylaşım anlık görüntüsündeki dosyayı geri yükleyebilirsiniz. Ardından, belirli bir paylaşılan anlık görüntüye ait olan bir dosyayı alabilirsiniz. Dosyayı doğrudan okumak veya geri yüklemek için bu sürümü kullanın.

# <a name="azure-net-sdk-v12"></a>[Azure \. NET SDK V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_RestoreFileFromSnapshot":::

# <a name="azure-net-sdk-v11"></a>[Azure \. NET SDK v11](#tab/dotnetv11)

```csharp
CloudFileShare liveShare = fClient.GetShareReference(baseShareName);
var rootDirOfliveShare = liveShare.GetRootDirectoryReference();
var dirInliveShare = rootDirOfliveShare.GetDirectoryReference(dirName);
var fileInliveShare = dirInliveShare.GetFileReference(fileName);

CloudFileShare snapshot = fClient.GetShareReference(baseShareName, snapshotTime);
var rootDirOfSnapshot = snapshot.GetRootDirectoryReference();
var dirInSnapshot = rootDirOfSnapshot.GetDirectoryReference(dirName);
var fileInSnapshot = dir1InSnapshot.GetFileReference(fileName);

string sasContainerToken = string.Empty;
SharedAccessFilePolicy sasConstraints = new SharedAccessFilePolicy();
sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
sasConstraints.Permissions = SharedAccessFilePermissions.Read;

//Generate the shared access signature on the container, setting the constraints directly on the signature.
sasContainerToken = fileInSnapshot.GetSharedAccessSignature(sasConstraints);

string sourceUri = (fileInSnapshot.Uri.ToString() + sasContainerToken + "&" + fileInSnapshot.SnapshotTime.ToString()); ;
fileInliveShare.StartCopyAsync(new Uri(sourceUri));
```

---

### <a name="delete-share-snapshots"></a>Paylaşım anlık görüntülerini silme

Aşağıdaki örnekte dosya paylaşım anlık görüntüsü silinmektedir.

# <a name="azure-net-sdk-v12"></a>[Azure \. NET SDK V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_DeleteSnapshot":::

# <a name="azure-net-sdk-v11"></a>[Azure \. NET SDK v11](#tab/dotnetv11)

```csharp
CloudFileShare mySnapshot = fClient.GetShareReference(baseShareName, snapshotTime); mySnapshot.Delete(null, null, null);
```

---

## <a name="troubleshoot-azure-files-by-using-metrics"></a>Ölçümleri kullanarak Azure dosyaları sorunlarını giderme<a name="troubleshooting-azure-files-using-metrics"></a>

Azure Depolama Analizi, Azure dosyaları için ölçümleri destekler. Ölçüm verilerini kullanarak istekleri ve tanılama sorunlarını izleyebilirsiniz.

[Azure Portal](https://portal.azure.com)Azure dosyaları için ölçümleri etkinleştirebilirsiniz. Ayrıca, Azure Files istemci kitaplığındaki REST API veya analoglarından biri ile [Dosya hizmeti özelliklerini ayarlama](/rest/api/storageservices/set-file-service-properties) işlemini çağırarak ölçümleri programlı bir şekilde etkinleştirebilirsiniz.

Aşağıdaki kod örneği, Azure dosyaları için ölçümleri etkinleştirmek üzere .NET istemci kitaplığı 'nın nasıl kullanılacağını göstermektedir.

# <a name="azure-net-sdk-v12"></a>[Azure \. NET SDK V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_UseMetrics":::

# <a name="azure-net-sdk-v11"></a>[Azure \. NET SDK v11](#tab/dotnetv11)

İlk olarak, aşağıdaki `using` yönergeleri, yukarıdaki eklendikleriyle birlikte *program. cs* dosyanıza ekleyin:

```csharp
using Microsoft.Azure.Storage.File.Protocol;
using Microsoft.Azure.Storage.Shared.Protocol;
```

Azure Blobları, Azure tabloları ve Azure kuyrukları `ServiceProperties` ad alanındaki paylaşılan türünü kullanmasına rağmen `Microsoft.Azure.Storage.Shared.Protocol` , Azure dosyaları `FileServiceProperties` ad alanındaki türü kendi türünü kullanır `Microsoft.Azure.Storage.File.Protocol` . Kodunuzda her iki ad alanına da başvurmanız gerekir, ancak aşağıdaki kodun derlenmesi için.

```csharp
// Parse your storage connection string from your application's configuration file.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));
// Create the File service client.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Set metrics properties for File service.
// Note that the File service currently uses its own service properties type,
// available in the Microsoft.Azure.Storage.File.Protocol namespace.
fileClient.SetServiceProperties(new FileServiceProperties()
{
    // Set hour metrics
    HourMetrics = new MetricsProperties()
    {
        MetricsLevel = MetricsLevel.ServiceAndApi,
        RetentionDays = 14,
        Version = "1.0"
    },
    // Set minute metrics
    MinuteMetrics = new MetricsProperties()
    {
        MetricsLevel = MetricsLevel.ServiceAndApi,
        RetentionDays = 7,
        Version = "1.0"
    }
});

// Read the metrics properties we just set.
FileServiceProperties serviceProperties = fileClient.GetServiceProperties();
Console.WriteLine("Hour metrics:");
Console.WriteLine(serviceProperties.HourMetrics.MetricsLevel);
Console.WriteLine(serviceProperties.HourMetrics.RetentionDays);
Console.WriteLine(serviceProperties.HourMetrics.Version);
Console.WriteLine();
Console.WriteLine("Minute metrics:");
Console.WriteLine(serviceProperties.MinuteMetrics.MetricsLevel);
Console.WriteLine(serviceProperties.MinuteMetrics.RetentionDays);
Console.WriteLine(serviceProperties.MinuteMetrics.Version);
```

---

Herhangi bir sorunla karşılaşırsanız, bkz. [Windows 'Da Azure dosyaları sorunlarını giderme](storage-troubleshoot-windows-file-connection-problems.md).

## <a name="next-steps"></a>Sonraki adımlar

Azure dosyaları hakkında daha fazla bilgi için aşağıdaki kaynaklara bakın:

### <a name="conceptual-articles-and-videos"></a>Kavramsal makaleler ve videolar

- [Azure Dosyaları: Windows ve Linux için uyumlu bulut SMB dosya sistemi](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)
- [Linux ile Azure Dosyalar’ı kullanma](storage-how-to-use-files-linux.md)

### <a name="tooling-support-for-file-storage"></a>File Storage için araç desteği

- [AzCopy’yi kullanmaya başlama](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
- [Windows’ta Azure Dosyalar sorunlarını giderme](./storage-troubleshoot-windows-file-connection-problems.md)

### <a name="reference"></a>Başvuru

- [.NET için Azure Depolama API'leri](/dotnet/api/overview/azure/storage)
- [Dosya Hizmeti REST API'si](/rest/api/storageservices/File-Service-REST-API)