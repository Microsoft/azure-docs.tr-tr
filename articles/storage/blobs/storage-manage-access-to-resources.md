---
title: Azure Blob depolamada kapsayıcılar ve Bloblar için genel okuma erişimini etkinleştirme | Microsoft Docs
description: Kapsayıcıları ve Blobları anonim erişim için nasıl kullanılabilir yapacağınızı ve programlı olarak nasıl erişebileceğini öğrenin.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 09/19/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.openlocfilehash: d0e3121fe773a9725eb7cfd9e8b14d0ed86f3fbb
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/29/2019
ms.locfileid: "71673309"
---
# <a name="manage-anonymous-read-access-to-containers-and-blobs"></a>Kapsayıcılara ve blob’lara anonim okuma erişimini yönetme

Azure Blob depolama alanındaki bir kapsayıcıya ve bloblarına anonim, genel okuma erişimi sağlayabilirsiniz. Bunu yaptığınızda, hesap anahtarınızı paylaşmadan ve paylaşılan erişim imzası (SAS) gerekmeden bu kaynaklara salt okuma erişimi verebilirsiniz.

Genel okuma erişimi, belirli Blobların anonim okuma erişimi için her zaman kullanılabilir olmasını istediğiniz senaryolar için idealdir. Daha ayrıntılı denetim için, paylaşılan erişim imzası oluşturabilirsiniz. Paylaşılan erişim imzaları, belirli bir süre boyunca farklı izinler kullanarak kısıtlı erişim sağlamanıza olanak tanır. Paylaşılan erişim imzaları oluşturma hakkında daha fazla bilgi için bkz. [Azure depolama 'da paylaşılan erişim imzaları (SAS) kullanma](../common/storage-sas-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="grant-anonymous-users-permissions-to-containers-and-blobs"></a>Kapsayıcılar ve bloblara anonim kullanıcılara izin verme

Varsayılan olarak, bir kapsayıcıya ve içindeki bloblara yalnızca uygun izinlere sahip olan bir kullanıcı tarafından erişilebilir. Anonim kullanıcılara bir kapsayıcıya ve bloblarına okuma erişimi sağlamak için kapsayıcı genel erişim düzeyini ayarlayabilirsiniz. Bir kapsayıcıya genel erişim verdiğinizde, anonim kullanıcılar, isteği yetkilendirmeden genel olarak erişilebilen bir kapsayıcı içindeki Blobları okuyabilir.

Aşağıdaki izinlerle bir kapsayıcı yapılandırabilirsiniz:

- **Genel okuma erişimi yok:** Kapsayıcıya ve bloblarına yalnızca depolama hesabı sahibi tarafından erişilebilir. Bu, tüm yeni kapsayıcılar için varsayılandır.
- **Yalnızca Bloblar için genel okuma erişimi:** Kapsayıcı içindeki Bloblar anonim istek tarafından okunabilir, ancak kapsayıcı verileri kullanılamıyor. Anonim istemciler kapsayıcı içindeki Blobları numaralandıramaz.
- **Kapsayıcı ve Blobları için genel okuma erişimi:** Tüm kapsayıcı ve blob verileri, anonim istek tarafından okunabilir. İstemciler kapsayıcı içindeki Blobları anonim istek ile numaralandırabilirler, ancak depolama hesabındaki kapsayıcıları numaralandıramaz.

### <a name="set-container-public-access-level-in-the-azure-portal"></a>Azure portal kapsayıcı genel erişim düzeyini ayarlama

[Azure Portal](https://portal.azure.com), bir veya daha fazla kapsayıcı için genel erişim düzeyini güncelleştirebilirsiniz:

1. Azure portal depolama hesabınıza genel bakış bölümüne gidin.
1. Menü dikey penceresinde **BLOB hizmeti** altında **Bloblar**' ı seçin.
1. Ortak erişim düzeyini ayarlamak istediğiniz kapsayıcıları seçin.
1. Genel erişim ayarlarını göstermek için **erişim düzeyini Değiştir** düğmesini kullanın.
1. **Genel erişim düzeyi** açılan menüsünde istenen genel erişim düzeyini seçin ve değişikliği seçili kapsayıcılara uygulamak için Tamam düğmesine tıklayın.

Aşağıdaki ekran görüntüsünde, seçili kapsayıcılar için genel erişim düzeyinin nasıl değiştirileceği gösterilmektedir.

![Portalda genel erişim düzeyinin nasıl ayarlanacağını gösteren ekran görüntüsü](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

> [!NOTE]
> Tek bir blob için genel erişim düzeyini değiştiremezsiniz. Genel erişim düzeyi yalnızca kapsayıcı düzeyinde ayarlanır.

### <a name="set-container-public-access-level-with-net"></a>.NET ile kapsayıcı genel erişim düzeyini ayarlama

.NET için Azure Storage istemci kitaplığı 'nı kullanarak bir kapsayıcının izinlerini ayarlamak için, önce aşağıdaki yöntemlerden birini çağırarak kapsayıcının mevcut izinlerini alın:

- [GetPermissions](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getpermissions)
- [GetPermissionsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getpermissionsasync)

Ardından, **GetPermissions** yöntemi tarafından döndürülen [blobcontainerpermissions](/dotnet/api/microsoft.azure.storage.blob.blobcontainerpermissions) nesnesinde **publicAccess** özelliğini ayarlayın.

Son olarak, kapsayıcının izinlerini güncelleştirmek için aşağıdaki yöntemlerden birini çağırın:

- [SetPermissions](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setpermissions)
- [SetPermissionsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setpermissionsasync)

Aşağıdaki örnek, kapsayıcının izinlerini tam genel okuma erişimi olarak ayarlar. Yalnızca Bloblar için genel okuma erişimi izinleri ayarlamak için, **publicAccess** özelliğini **Blobcontainerpublicaccesstype. blob**olarak ayarlayın. Anonim kullanıcılar için tüm izinleri kaldırmak için, özelliği **Blobcontainerpublicaccesstype. off**olarak ayarlayın.

```csharp
private static async Task SetPublicContainerPermissions(CloudBlobContainer container)
{
    BlobContainerPermissions permissions = await container.GetPermissionsAsync();
    permissions.PublicAccess = BlobContainerPublicAccessType.Container;
    await container.SetPermissionsAsync(permissions);

    Console.WriteLine("Container {0} - permissions set to {1}", container.Name, permissions.PublicAccess);
}
```

## <a name="access-containers-and-blobs-anonymously"></a>Kapsayıcılar ve bloblara anonim erişim

Kapsayıcılara ve bloblara erişen bir istemci, kimlik bilgileri gerektirmeyen oluşturucuları kullanabilir. Aşağıdaki örneklerde, kapsayıcılara ve bloblara anonim olarak başvurmak için birkaç farklı yol gösterilmektedir.

### <a name="create-an-anonymous-client-object"></a>Anonim istemci nesnesi oluşturma

Hesap için BLOB depolama uç noktası sağlayarak anonim erişim için yeni bir hizmet istemci nesnesi oluşturabilirsiniz. Ancak, bu hesapta anonim erişim için kullanılabilen bir kapsayıcının adını da bilmeniz gerekir.

```csharp
public static void CreateAnonymousBlobClient()
{
    // Create the client object using the Blob storage endpoint for your account.
    CloudBlobClient blobClient = new CloudBlobClient(
        new Uri(@"https://storagesamples.blob.core.windows.net"));

    // Get a reference to a container that's available for anonymous access.
    CloudBlobContainer container = blobClient.GetContainerReference("sample-container");

    // Read the container's properties. 
    // Note this is only possible when the container supports full public read access.
    container.FetchAttributes();
    Console.WriteLine(container.Properties.LastModified);
    Console.WriteLine(container.Properties.ETag);
}
```

### <a name="reference-a-container-anonymously"></a>Kapsayıcıya anonim olarak başvuru

Anonim olarak kullanılabilen bir kapsayıcının URL 'SI varsa, kapsayıcıyı doğrudan başvuruda bulunmak için kullanabilirsiniz.

```csharp
public static void ListBlobsAnonymously()
{
    // Get a reference to a container that's available for anonymous access.
    CloudBlobContainer container = new CloudBlobContainer(
        new Uri(@"https://storagesamples.blob.core.windows.net/sample-container"));

    // List blobs in the container.
    // Note this is only possible when the container supports full public read access.
    foreach (IListBlobItem blobItem in container.ListBlobs())
    {
        Console.WriteLine(blobItem.Uri);
    }
}
```

### <a name="reference-a-blob-anonymously"></a>Bir Blobun anonim olarak başvur

Anonim erişim için kullanılabilen bir Blobun URL 'SI varsa, bu URL 'YI kullanarak blob 'a doğrudan başvurabilirsiniz:

```csharp
public static void DownloadBlobAnonymously()
{
    CloudBlockBlob blob = new CloudBlockBlob(
        new Uri(@"https://storagesamples.blob.core.windows.net/sample-container/logfile.txt"));
    blob.DownloadToFile(@"C:\Temp\logfile.txt", FileMode.Create);
}
```

## <a name="next-steps"></a>Sonraki adımlar

- [Azure depolama 'ya erişimi yetkilendirme](../common/storage-auth.md)
- [Paylaşılan erişim imzalarını (SAS) kullanarak Azure depolama kaynaklarına sınırlı erişim verme](../common/storage-sas-overview.md)
- [Blob hizmeti REST API](/rest/api/storageservices/blob-service-rest-api)
