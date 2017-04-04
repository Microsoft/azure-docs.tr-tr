---
title: ".NET kullanarak Azure Blob Depolama’yı (nesne depolama) kullanmaya başlama | Microsoft Belgeleri"
description: "Azure Blob Storage (nesne depolama) ile bulutta yapılandırılmamış veri depolayın."
services: storage
documentationcenter: .net
author: mmacy
manager: timlt
editor: tysonn
ms.assetid: d18a8fc8-97cb-4d37-a408-a6f8107ea8b3
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 03/27/2017
ms.author: marsma
translationtype: Human Translation
ms.sourcegitcommit: 6e0ad6b5bec11c5197dd7bded64168a1b8cc2fdd
ms.openlocfilehash: 29d8693dd1d6d1ef26ccb21e3a5b29cf1adbfcc5
ms.lasthandoff: 03/28/2017

---
# <a name="get-started-with-azure-blob-storage-using-net"></a>.NET kullanarak Azure Blob Storage’ı kullanmaya başlayın

[!INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

[!INCLUDE [storage-check-out-samples-dotnet](../../includes/storage-check-out-samples-dotnet.md)]

Azure Blob Storage, bulutta nesne/blob olarak yapılandırılmamış veri depolayan bir hizmettir. Blob Storage belge, medya dosyası veya uygulama yükleyici gibi her tür metin veya ikili veri depolayabilir. Blob Storage aynı zamanda nesne depolama olarak adlandırılır.

### <a name="about-this-tutorial"></a>Bu öğretici hakkında
Bu öğreti, Azure Blob Storage kullanarak bazı genel senaryolar için .NET kodunun nasıl yazılacağını göstermektedir. Kapsanan senaryolar arasında blob yükleme, listeleme, indirme ve silme yer alır.

**Ön koşullar:**

* [Microsoft Visual Studio](https://www.visualstudio.com/)
* [.NET için Azure Depolama İstemcisi](https://www.nuget.org/packages/WindowsAzure.Storage/)
* [.NET için Azure Yapılandırma Yöneticisi](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/)
* Bir [Azure Storage hesabı](storage-create-storage-account.md#create-a-storage-account)

[!INCLUDE [storage-dotnet-client-library-version-include](../../includes/storage-dotnet-client-library-version-include.md)]

### <a name="more-samples"></a>Daha fazla örnek
Blob depolama kullanan diğer örnekler için [.NET’te Azure Blob Depolama Kullanmaya Başlama](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/). Örnek uygulamayı indirip çalıştırabilir veya GitHub’daki örneğe göz atabilirsiniz.

[!INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

[!INCLUDE [storage-development-environment-include](../../includes/storage-development-environment-include.md)]

### <a name="add-using-directives"></a>Using yönergeleri ekleme
Aşağıdaki **using** yönergelerini `Program.cs` dosyasının üst tarafına ekleyin:

```csharp
using Microsoft.Azure; // Namespace for CloudConfigurationManager
using Microsoft.WindowsAzure.Storage; // Namespace for CloudStorageAccount
using Microsoft.WindowsAzure.Storage.Blob; // Namespace for Blob storage types
```

### <a name="parse-the-connection-string"></a>Bağlantı dizesini ayrıştırma
[!INCLUDE [storage-cloud-configuration-manager-include](../../includes/storage-cloud-configuration-manager-include.md)]

### <a name="create-the-blob-service-client"></a>Blob hizmeti istemcisi oluşturma
**CloudBlobClient** sınıfı Blob Storage’da  depolanan kapsayıcıları ve blobları almanızı sağlar. Hizmet istemcisini oluşturma yöntemlerinden biri aşağıda verilmiştir:

```csharp
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
```
Artık Blob Storage’da n veri okuyan ve bu depolamaya veri yazan kodu yazmaya hazırsınız.

## <a name="create-a-container"></a>Bir kapsayıcı oluşturma
[!INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

Bu örnek, zaten yoksa, nasıl bir kapsayıcı oluşturulacağını gösterir:

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Retrieve a reference to a container.
CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

// Create the container if it doesn't already exist.
container.CreateIfNotExists();
```

Varsayılan olarak yeni kapsayıcı özeldir, bu kapsayıcıdan blob indirmek için depolama erişim anahtarınızı belirlemeniz gerektiği anlamına gelir. Kapsayıcı içindeki dosyaların herkese açık olmasını istiyorsanız, aşağıdaki kodu kullanarak kapsayıcıyı herkesin erişimine açık hale getirebilirsiniz:

```csharp
container.SetPermissions(
    new BlobContainerPermissions { PublicAccess = BlobContainerPublicAccessType.Blob });
```

İnternet’e bağlı olan herkes, herkese açık bir kapsayıcıdaki blobları görebilir. Ancak, uygun hesap erişim anahtarı veya paylaşılan erişim imzanız olması durumunda bunları değiştirebilir veya silebilirsiniz.

## <a name="upload-a-blob-into-a-container"></a>Bir kapsayıcıya bir blob yükleme
Azure Blob Storage blok blobları ve sayfa bloblarını destekler.  Çoğu durumda, kullanılması önerilen blob türü blok blobudur.

Bir dosyayı bir blok blobuna yüklemek için bir kapsayıcı başvurusu alın ve blok blob başvurusu almak için kullanın. Bir blob başvurusu edindiğinizde **UploadFromStream** yöntemini çağırarak istediğiniz veri akışını yükleyebilirsiniz. Bu işlemle, eğer önceden oluşturulmadıysa bir blob oluşturulacaktır, aksi takdirde üzerine yazılacaktır.

Aşağıdaki örnek kapsayıcının önceden oluşturulduğunu varsayarak bir blobun bir kapsayıcıya nasıl yükleneceğini gösterir.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Retrieve reference to a previously created container.
CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

// Retrieve reference to a blob named "myblob".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

// Create or overwrite the "myblob" blob with contents from a local file.
using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
{
    blockBlob.UploadFromStream(fileStream);
}
```

## <a name="list-the-blobs-in-a-container"></a>Blob’ları bir kapsayıcıda listeleme
Blob’ları bir kapsayıcıda listelemek için ilk olarak bir kapsayıcı başvurusu edinin. Ardından içindeki blobları ve/veya dizinleri almak için kapsayıcının **ListBlobs** yöntemini kullanabilirsiniz. Dönen **IListBlobItem** için zengin özellik ve yöntem kümesine erişmek için **CloudBlockBlob**, **CloudPageBlob** veya **CloudBlobDirectory** nesnesine yayınlamanız gerekir. Tür bilinmiyorsa, hangisine yayınlayacağınızı belirlemek için bir tür denetimi kullanabilirsiniz. Aşağıdaki kod, _resimler_ kapsayıcısındaki her nesnenin URI’sinin nasıl alınacağını ve çıkacağını gösterir:

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Retrieve reference to a previously created container.
CloudBlobContainer container = blobClient.GetContainerReference("photos");

// Loop over items within the container and output the length and URI.
foreach (IListBlobItem item in container.ListBlobs(null, false))
{
    if (item.GetType() == typeof(CloudBlockBlob))
    {
        CloudBlockBlob blob = (CloudBlockBlob)item;

        Console.WriteLine("Block blob of length {0}: {1}", blob.Properties.Length, blob.Uri);

    }
    else if (item.GetType() == typeof(CloudPageBlob))
    {
        CloudPageBlob pageBlob = (CloudPageBlob)item;

        Console.WriteLine("Page blob of length {0}: {1}", pageBlob.Properties.Length, pageBlob.Uri);

    }
    else if (item.GetType() == typeof(CloudBlobDirectory))
    {
        CloudBlobDirectory directory = (CloudBlobDirectory)item;

        Console.WriteLine("Directory: {0}", directory.Uri);
    }
}
```

Blob adlarına yol bilgilerini ekleyerek, geleneksel bir dosya sisteminde olduğu gibi düzenleme ve geçiş yapabileceğiniz sanal bir dizin yapısı oluşturabilirsiniz. Dizin yapısı yalnızca sanaldır; Blob depolamada kaynak olarak yalnızca kapsayıcılar ve bloblar kullanılabilir. Buna karşın depolama istemci kitaplığı sanal bir dizine yönlendirmek ve bu şekilde düzenlenen bloblarla çalışma sürecini basitleştirmek için **CloudBlobDirectory** nesnesi sağlar.

Örneğin bir kapsayıcıda yer alan ve *resimler* olarak adlandırılan aşağıdaki blok blobları kümesine göz atın:

```
photo1.jpg
2010/architecture/description.txt
2010/architecture/photo3.jpg
2010/architecture/photo4.jpg
2011/architecture/photo5.jpg
2011/architecture/photo6.jpg
2011/architecture/description.txt
2011/photo7.jpg
```

*Fotoğraflar* kapsayıcısında (yukarıdaki kod parçacığında olduğu bibi) **ListBlobs** çağırdığınızda, hiyerarşik bir listeleme döndürülür. Kapsayıcıda sırasıyla dizinleri ve blobları temsil eden hem **CloudBlobDirectory** hem de **CloudBlockBlob** nesnelerini içerir. Sonuçta çıktı şuna benzer:

```
Directory: https://<accountname>.blob.core.windows.net/photos/2010/
Directory: https://<accountname>.blob.core.windows.net/photos/2011/
Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg
```

İsteğe bağlı olarak **ListBlobs** yönteminin **UseFlatBlobListing** parametresini **true** olarak ayarlayabilirsiniz. Bu durumda kapsayıcıdaki her blob **CloudBlockBlob** nesnesi olarak döner. Düz listeleme dönmesi için **ListBlobs**’a çağrı şuna benzer:

```csharp
// Loop over items within the container and output the length and URI.
foreach (IListBlobItem item in container.ListBlobs(null, true))
{
    ...
}
```

ve sonuçlar şöyle görünür:

```
Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
Block blob of length 314618: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
Block blob of length 522713: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
Block blob of length 419048: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
Block blob of length 506388: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
Block blob of length 399751: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg
```

## <a name="download-blobs"></a>Blob’ları indirme
Blob’ları indirmek için ilk olarak bir blob başvurusu alın ve ardından **DownloadToStream** yöntemini çağırın. Aşağıdaki örnek, blob içeriklerini bir akış nesnesine aktarmak ve ardından yerel bir dosyaya kalıcı olarak almak için **DownloadToStream** yöntemini kullanır:

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Retrieve reference to a previously created container.
CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

// Retrieve reference to a blob named "photo1.jpg".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

// Save blob contents to a file.
using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
{
    blockBlob.DownloadToStream(fileStream);
}
```

Bunun yanında bir blobun içeriklerini metin dizesi olarak indirmek için **DownloadToStream** yöntemini kullanabilirsiniz.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Retrieve reference to a previously created container.
CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

// Retrieve reference to a blob named "myblob.txt"
CloudBlockBlob blockBlob2 = container.GetBlockBlobReference("myblob.txt");

string text;
using (var memoryStream = new MemoryStream())
{
    blockBlob2.DownloadToStream(memoryStream);
    text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
}
```

## <a name="delete-blobs"></a>Blob’ları silme
Bir blobu silmek için ilk olarak bir blob başvurusu alın ve ardından **Sil** yöntemini çağırın.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Retrieve reference to a previously created container.
CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

// Retrieve reference to a blob named "myblob.txt".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

// Delete the blob.
blockBlob.Delete();
```

## <a name="list-blobs-in-pages-asynchronously"></a>Blob’ları sayfalarda zaman uyumsuz olarak listeleme
Çok sayıda blob listeliyorsanız veya bir listeleme işlemi ile dönen sonuç sayısını denetlemek isterseniz sonuç sayfalarında blobları listeleyebilirsiniz. Bu örnek, geniş bir sonuç kümesinin dönmesini beklerken çalıştırmanın engellenmemesi için sayfalardaki sonuçların zaman uyumsuz olarak nasıl döneceğini gösterir.

Bu örnek düz bir blob listesi gösterir, ancak **ListBlobsSegmentedAsync** metodunun _useFlatBlobListing_ parametresini _false_ olarak ayarlayarak hiyerarşik bir listeleme gerçekleştirebilirsiniz.

Örnek metot, zaman uyumsuz bir metot çağırdığı için _async_ anahtar sözcüğüyle başlamalı ve bir **Görev** nesnesi döndürmelidir. Belirtilen **ListBlobsSegmentedAsync** yöntemi için belirlenen bekleme anahtar kelimesi, listeleme görevi tamamlanana kadar örnek yöntemin çalıştırılmasını askıya alır.

```csharp
async public static Task ListBlobsSegmentedInFlatListing(CloudBlobContainer container)
{
    //List blobs to the console window, with paging.
    Console.WriteLine("List blobs in pages:");

    int i = 0;
    BlobContinuationToken continuationToken = null;
    BlobResultSegment resultSegment = null;

    //Call ListBlobsSegmentedAsync and enumerate the result segment returned, while the continuation token is non-null.
    //When the continuation token is null, the last page has been returned and execution can exit the loop.
    do
    {
        //This overload allows control of the page size. You can return all remaining results by passing null for the maxResults parameter,
        //or by calling a different overload.
        resultSegment = await container.ListBlobsSegmentedAsync("", true, BlobListingDetails.All, 10, continuationToken, null, null);
        if (resultSegment.Results.Count<IListBlobItem>() > 0) { Console.WriteLine("Page {0}:", ++i); }
        foreach (var blobItem in resultSegment.Results)
        {
            Console.WriteLine("\t{0}", blobItem.StorageUri.PrimaryUri);
        }
        Console.WriteLine();

        //Get the continuation token.
        continuationToken = resultSegment.ContinuationToken;
    }
    while (continuationToken != null);
}
```

## <a name="writing-to-an-append-blob"></a>Ek blobu yazma
Ek blob, günlük tutma gibi ekleme işlemleri için en iyi duruma getirilmiştir. Blok blobuna benzer şekilde bir ek blobu bloklardan oluşur ancak bir ek bloba yeni bir blok eklediğinizde her zaman blobun sonuna eklenir. Bir ek blobdaki mevcut bloğu güncelleştiremezsiniz veya silemezsiniz. Bir blok blobu olduğu için ek blobun blok kimliği gösterilmez.

Her biri en fazla 4 MB olmak üzere bir ek blobundaki her blok farklı boyutlarda olabilir ve bir ek blobu en fazla 50.000 blok içerebilir. Bu nedenle bir ek blobunun en büyük boyutu 195 GB’den biraz fazladır (4 MB x 50.000 blok).

Aşağıdaki örnek yeni bir ek blob oluşturur ve basit bir günlük yazma işlemini benzeterek buna veri ekler.

```csharp
//Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

//Create service client for credentialed access to the Blob service.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

//Get a reference to a container.
CloudBlobContainer container = blobClient.GetContainerReference("my-append-blobs");

//Create the container if it does not already exist.
container.CreateIfNotExists();

//Get a reference to an append blob.
CloudAppendBlob appendBlob = container.GetAppendBlobReference("append-blob.log");

//Create the append blob. Note that if the blob already exists, the CreateOrReplace() method will overwrite it.
//You can check whether the blob exists to avoid overwriting it by using CloudAppendBlob.Exists().
appendBlob.CreateOrReplace();

int numBlocks = 10;

//Generate an array of random bytes.
Random rnd = new Random();
byte[] bytes = new byte[numBlocks];
rnd.NextBytes(bytes);

//Simulate a logging operation by writing text data and byte data to the end of the append blob.
for (int i = 0; i < numBlocks; i++)
{
    appendBlob.AppendText(String.Format("Timestamp: {0:u} \tLog Entry: {1}{2}",
        DateTime.UtcNow, bytes[i], Environment.NewLine));
}

//Read the append blob to the console window.
Console.WriteLine(appendBlob.DownloadText());
```

Üç blob türü arasındaki farklar hakkında bilgi edinmek için bkz. [Blok Blobları, Sayfa Blobları ve Ek Bloblarını anlama](/rest/api/storageservices/fileservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs).

## <a name="managing-security-for-blobs"></a>Blobların güvenliğini sağlama
Varsayılan olarak Azure Storage, erişimi hesap erişim anahtarlarına sahip olan hesap sahibiyle sınırlandırarak verilerinizi güvende tutar. Depolama hesabınızda blob verileri paylaşmanız gerektiğinde bunu hesap erişim anahtarlarınızın güvenliğini tehlikeye atmadan yapmak önem taşır. Buna ek olarak kablo ve Azure Storage üzerinden güvenle geçmesini sağlamak için blob verilerini şifreleyebilirsiniz.

[!INCLUDE [storage-account-key-note-include](../../includes/storage-account-key-note-include.md)]

### <a name="controlling-access-to-blob-data"></a>Blob verilerine erişimi denetleme
Varsayılan olarak depolama hesabındaki blob verileri yalnızca depolama hesabı sahibi tarafından erişilebilir. Blob Depolamaya yönelik kimlik doğrulama istekleri, istek varsayılan olarak hesap erişim anahtarı gerektirir. Buna karşın çeşitli blob verilerinin diğer kullanıcılar tarafından kullanılmasını sağlamak isteyebilirsiniz. İki seçeneğiniz vardır:

* **Anonim erişim:** Bir kapsayıcıyı veya bloblarını anonim erişim için genel erişime açabilirsiniz. Daha fazla bilgi için bkz.: [Kapsayıcılar ve bloblar için anonim okuma erişimini yönetme](storage-manage-access-to-resources.md).
* **Paylaşılan Erişim İmzası:** İstemcilere, belirlediğiniz izinler ve belirlediğiniz zaman aralığında depolama hesabınızdaki bir kaynağa yetkilendirmiş erişim sağlayan bir paylaşılan erişim imzası (SAS) sağlayabilirsiniz. Daha fazla bilgi edinmek için bkz. [Paylaşılan Erişim İmzaları (SAS) kullanma](storage-dotnet-shared-access-signature-part-1.md).

### <a name="encrypting-blob-data"></a>Blob verilerini şifreleme
Azure Storage hem istemci hem de sunucuda blob verisi şifreleme özelliği destekler.

* **İstemci tarafı şifreleme:** .NET için Depolama İstemci Kitaplığı Azure Storage’a yüklemeden önce istemci uygulamalar içinde verilerin şifrelenmesi ve istemciye indirirken verilerin şifresinin çözülmesini destekler. Kitaplık ayrıca depolama hesabı anahtarı yönetimi için Azure Anahtar Kasası ile tümleştirmeyi destekler. Daha fazla bilgi için bkz. [Microsoft Azure Storage için .NET içinde İstemci Tarafı Şifreleme](storage-client-side-encryption.md). Ayrıca bkz. [Öğretici: Azure Anahtar Kasası kullanılarak Microsoft Azure Storage’daki blobları şifreleme ve şifresini çözme](storage-encrypt-decrypt-blobs-key-vault.md).
* **Sunucu tarafı şifreleme**: Azure Storage artık sunucu tarafı şifreleme desteklemektedir. Bkz. [Bekleyen Veri için Azure Storage Hizmeti Şifreleme (Önizleme)](storage-service-encryption.md).

## <a name="next-steps"></a>Sonraki adımlar
Blob Storage’ın temellerini öğrendiğinize göre, daha fazla bilgi edinmek için bu bağlantıları izleyin.

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure Depolama Gezgini
* [Microsoft Azure Depolama Gezgini (MASE)](../vs-azure-tools-storage-manage-with-storage-explorer.md), Microsoft’un Windows, macOS ve Linux üzerinde Azure Depolama verileriyle görsel olarak çalışmanızı sağlayan ücretsiz ve tek başına uygulamasıdır.

### <a name="blob-storage-samples"></a>Blob depolama örnekleri
* [.NET’te Azure Blob Depolama Kullanmaya Başlama](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/)

### <a name="blob-storage-reference"></a>Blob Storage başvurusu
* [.NET başvurusu için Depolama İstemci Kitaplığı](https://msdn.microsoft.com/library/azure/mt347887.aspx)
* [REST API başvurusu](/rest/api/storageservices/fileservices/azure-storage-services-rest-api-reference)

### <a name="conceptual-guides"></a>Kavramsal kılavuzlar
* [AzCopy komut satırı yardımcı programı ile veri aktarımı](storage-use-azcopy.md)
* [.NET için Dosya depolamayı kullanmaya başlama](storage-dotnet-how-to-use-files.md)
* [WebJobs SDK ile Azure blob depolama kullanımı](../app-service-web/websites-dotnet-webjobs-sdk-storage-blobs-how-to.md)

