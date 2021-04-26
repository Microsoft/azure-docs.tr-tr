---
title: .NET 'te blob anlık görüntüsü oluşturma ve yönetme
titleSuffix: Azure Storage
description: Blob verilerini belirli bir anda yedeklemek için bir Blobun salt okunurdur anlık görüntüsünü oluşturmak üzere .NET istemci kitaplığı 'nı nasıl kullanacağınızı öğrenin.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/27/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: ae1d72c30fb7c45c92f24ec5132859a45c6ae953
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95543383"
---
# <a name="create-and-manage-a-blob-snapshot-in-net"></a>.NET 'te blob anlık görüntüsü oluşturma ve yönetme

Anlık görüntü, bir Blobun zaman içinde alınmış bir salt okunurdur. Bu makalede, [.net Için Azure Storage istemci kitaplığı](/dotnet/api/overview/azure/storage)'nı kullanarak blob anlık görüntülerinin oluşturulması ve yönetilmesi gösterilmektedir.

Azure Storage 'daki blob anlık görüntüleri hakkında daha fazla bilgi için bkz. [BLOB anlık görüntüleri](snapshots-overview.md).

## <a name="create-a-snapshot"></a>Anlık görüntü oluşturma

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

.NET için Azure depolama istemci kitaplığı 'nın 12. x sürümünü kullanarak bir blok blobunun anlık görüntüsünü oluşturmak için aşağıdaki yöntemlerden birini kullanın:

- [CreateSnapshot](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.createsnapshot)
- [Createanlık görüntüsünün Tasync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.createsnapshotasync)

Aşağıdaki kod örneği, 12. x sürümüne sahip bir anlık görüntü oluşturmayı gösterir. İstekleri hizmete yetkilendirmek için Azure AD kimlik bilgilerinizi kullanmak üzere [Azure. Identity](https://www.nuget.org/packages/azure.identity) kitaplığına bir başvuru ekleyin.

```csharp
private static async Task CreateBlockBlobSnapshot(string accountName, string containerName, string blobName, Stream data)
{
    const string blobServiceEndpointSuffix = ".blob.core.windows.net";
    Uri containerUri = new Uri("https://" + accountName + blobServiceEndpointSuffix + "/" + containerName);

    // Get a container client object and create the container.
    BlobContainerClient containerClient = new BlobContainerClient(containerUri,
        new DefaultAzureCredential());
    await containerClient.CreateIfNotExistsAsync();

    // Get a blob client object.
    BlobClient blobClient = containerClient.GetBlobClient(blobName);

    try
    {
        // Upload text to create a block blob.
        await blobClient.UploadAsync(data);

        // Add blob metadata.
        IDictionary<string, string> metadata = new Dictionary<string, string>
        {
            { "ApproxBlobCreatedDate", DateTime.UtcNow.ToString() },
            { "FileType", "text" }
        };
        await blobClient.SetMetadataAsync(metadata);

        // Sleep 5 seconds.
        System.Threading.Thread.Sleep(5000);

        // Create a snapshot of the base blob.
        // You can specify metadata at the time that the snapshot is created.
        // If no metadata is specified, then the blob's metadata is copied to the snapshot.
        await blobClient.CreateSnapshotAsync();
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

.NET için Azure depolama istemci kitaplığı 'nın 11. x sürümünü kullanarak bir blok blobunun anlık görüntüsünü oluşturmak için aşağıdaki yöntemlerden birini kullanın:

- [CreateSnapshot](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.createsnapshot)
- [Createanlık görüntüsünün Tasync](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.createsnapshotasync)

Aşağıdaki kod örneği, 11. x sürümüne sahip bir anlık görüntünün nasıl oluşturulacağını göstermektedir. Bu örnek, bir anlık görüntü oluşturulduğunda diğer meta verileri belirtir.

```csharp
private static async Task CreateBlockBlobSnapshot(CloudBlobContainer container)
{
    // Create a new block blob in the container.
    CloudBlockBlob baseBlob = container.GetBlockBlobReference("sample-base-blob.txt");

    // Add blob metadata.
    baseBlob.Metadata.Add("ApproxBlobCreatedDate", DateTime.UtcNow.ToString());

    try
    {
        // Upload the blob to create it, with its metadata.
        await baseBlob.UploadTextAsync(string.Format("Base blob: {0}", baseBlob.Uri.ToString()));

        // Sleep 5 seconds.
        System.Threading.Thread.Sleep(5000);

        // Create a snapshot of the base blob.
        // You can specify metadata at the time that the snapshot is created.
        // If no metadata is specified, then the blob's metadata is copied to the snapshot.
        Dictionary<string, string> metadata = new Dictionary<string, string>();
        metadata.Add("ApproxSnapshotCreatedDate", DateTime.UtcNow.ToString());
        await baseBlob.CreateSnapshotAsync(metadata, null, null, null);
        Console.WriteLine(snapshot.SnapshotQualifiedStorageUri.PrimaryUri);
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

---

## <a name="delete-snapshots"></a>Anlık görüntüleri Sil

Bir blobu silmek için, önce söz konusu Blobun anlık görüntülerini silmeniz gerekir. Bir anlık görüntüyü tek tek silebilir veya kaynak blobu silindiğinde tüm anlık görüntülerin silineceğini belirtebilirsiniz. Anlık görüntülere sahip olan bir blobu silmeye çalışırsanız hata oluşur.

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

.NET için Azure depolama istemci kitaplığı 'nın 12. x sürümünü kullanarak bir blobu ve anlık görüntülerini silmek için aşağıdaki yöntemlerden birini kullanın ve [Deletesnapshot Tsoption](/dotnet/api/azure.storage.blobs.models.deletesnapshotsoption) sabit listesini ekleyin:

- [Silme](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.delete)
- [DeleteAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.deleteasync)
- [Deleteıfexists](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.deleteifexists)
- [Deleteıfexistsasync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.deleteifexistsasync)

Aşağıdaki kod örneği, .NET 'teki bir Blobun ve anlık görüntülerinin nasıl silineceğini ve burada `blobClient` [blobclient](/dotnet/api/azure.storage.blobs.blobclient)türünde bir nesne olduğunu gösterir:

```csharp
await blobClient.DeleteIfExistsAsync(DeleteSnapshotsOption.IncludeSnapshots, null, default);
```

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

.NET için Azure depolama istemci kitaplığı 'nın 11. x sürümünü kullanarak bir blobu ve anlık görüntülerini silmek için aşağıdaki blob silme yöntemlerinden birini kullanın ve [Deletesnapshot Tsoption](/dotnet/api/microsoft.azure.storage.blob.deletesnapshotsoption) sabit listesini ekleyin:

- [Silme](/dotnet/api/microsoft.azure.storage.blob.cloudblob.delete)
- [DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteasync)
- [Deleteıfexists](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteifexists)
- [Deleteıfexistsasync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteifexistsasync)

Aşağıdaki kod örneği, .NET 'teki bir Blobun ve anlık görüntülerinin nasıl silineceğini gösterir; burada, `blockBlob` [CloudBlockBlob] [dotnet_CloudBlockBlob] türünde bir nesnedir:

```csharp
await blockBlob.DeleteIfExistsAsync(DeleteSnapshotsOption.IncludeSnapshots, null, null, null);
```

---

## <a name="next-steps"></a>Sonraki adımlar

- [Blob anlık görüntüleri](snapshots-overview.md)
- [Blob sürümleri](versioning-overview.md)
- [Bloblar için geçici silme](./soft-delete-blob-overview.md)