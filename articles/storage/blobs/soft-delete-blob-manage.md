---
title: Geçici olarak silinen Blobları yönetme ve geri yükleme
titleSuffix: Azure Storage
description: Azure portal veya Azure Storage istemci kitaplıklarıyla, geçici olarak silinen blob 'ları ve anlık görüntüleri yönetin ve geri yükleyin.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/27/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 9d5ef85d947ae999fd94ba5a6e9cdb00baec9786
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106556215"
---
# <a name="manage-and-restore-soft-deleted-blobs"></a>Geçici olarak silinen Blobları yönetme ve geri yükleme

Blob geçici silme, belirli bir süre boyunca silinen verileri korumak için tek bir blobu ve sürümlerini, anlık görüntülerini ve meta verilerini yanlışlıkla silmekten veya üzerine yazacak şekilde korur. Saklama süresi boyunca Blobun silme sırasında durumuna geri yükleyebilirsiniz. Saklama süresi dolduktan sonra blob kalıcı olarak silinir. Blob geçici silme hakkında daha fazla bilgi için bkz. [Bloblar Için geçici silme](soft-delete-blob-overview.md).

Blob geçici silme, blob verileri için kapsamlı bir veri koruma stratejisinin bir parçasıdır. Microsoft 'un veri koruma önerileri hakkında daha fazla bilgi edinmek için bkz. [veri korumasına genel bakış](data-protection-overview.md).

## <a name="manage-soft-deleted-blobs-with-the-azure-portal"></a>Azure portal ile geçici olarak silinen Blobları yönetme

Azure portal, geçici olarak silinen blob 'ları ve anlık görüntüleri görüntülemek ve geri yüklemek için kullanabilirsiniz.

### <a name="view-deleted-blobs"></a>Silinen Blobları görüntüle

Blob 'lar geçici olarak silindiğinde Azure portal varsayılan olarak görünmez. Geçici olarak silinen Blobları görüntülemek için kapsayıcının **genel bakış** sayfasına gidin ve **silinen blob 'ları göster** ayarını değiştirin. Geçici olarak silinen Bloblar, **Silinmiş** durumuyla birlikte görüntülenir.

:::image type="content" source="media/soft-delete-blob-manage/soft-deleted-blobs-list-portal.png" alt-text="Azure portal 'da geçici olarak silinen Blobların nasıl ekleneceğini gösteren ekran görüntüsü":::

Sonra, özelliklerini göstermek için Bloblar listesinden silinen blobu seçin. **Genel bakış** sekmesinin altında, Blobun durumunun **Deleted** olarak ayarlandığını unutmayın. Portal, blob kalıcı olarak silinene kadar gün sayısını da görüntüler.

:::image type="content" source="media/soft-delete-blob-manage/soft-deleted-blob-properties-portal.png" alt-text="Azure portal 'da geçici olarak silinen Blobun özelliklerini gösteren ekran görüntüsü":::

### <a name="view-deleted-snapshots"></a>Silinen anlık görüntüleri görüntüle

Blob silindiğinde blob ile ilişkili tüm anlık görüntüler de silinir. Geçici olarak silinen bir Blobun anlık görüntüler varsa, silinen anlık görüntüler portalda da görüntülenebilir. Geçici olarak silinen Blobun özelliklerini görüntüleyin, ardından **anlık görüntüler** sekmesine gidin ve **silinen anlık görüntüleri göster**' i açın.

:::image type="content" source="media/soft-delete-blob-manage/soft-deleted-blob-snapshots-portal.png" alt-text="Gösteren ekran görüntüsü ":::

### <a name="restore-soft-deleted-objects-when-versioning-is-disabled"></a>Sürüm oluşturma devre dışı bırakıldığında, geçici olarak silinen nesneleri geri yükleme

Blob sürümü oluşturma etkin olmadığında Azure portal geçici olarak silinen bir blobu geri yüklemek için önce Blobun özelliklerini görüntüleyin, sonra **genel bakış** sekmesinde **geri al** düğmesini seçin. Bir Blobun geri yükleme işlemi, geçici silme Bekletme dönemi sırasında silinen tüm anlık görüntüleri de geri yükler.

:::image type="content" source="media/soft-delete-blob-manage/undelete-soft-deleted-blob-portal.png" alt-text="Azure portal ' de geçici olarak silinen bir Blobun geri yüklemeyi gösteren ekran görüntüsü":::

Geçici olarak silinen bir anlık görüntüyü temel Blobun yükseltmek için önce Blobun geçici olarak silinen anlık görüntülerinin geri yüklendiğinden emin olun. Blob 'un geçici olarak silinen anlık görüntülerini geri yüklemek için, geri **Al** düğmesini seçin. Ardından, Yükseltilecek anlık görüntüyü seçin ve anlık görüntüyü **Yükselt** düğmesini kullanarak temel Blobun anlık görüntünün içeriğiyle üzerine yazın.

:::image type="content" source="media/soft-delete-blob-manage/promote-snapshot.png" alt-text="Bir anlık görüntünün temel bloba nasıl yükseltileceğini gösteren ekran görüntüsü":::

### <a name="restore-soft-deleted-blobs-when-versioning-is-enabled"></a>Sürüm oluşturma etkinken geçici olarak silinen Blobları geri yükleme

Sürüm oluşturma etkinken Azure portal geçici olarak silinen bir blobu geri yüklemek için, özelliklerini göstermek üzere geçici olarak silinen blobu seçin ve ardından **sürümler** sekmesini seçin. Yükseltmek istediğiniz sürümü geçerli sürüm olacak şekilde seçin ve ardından **geçerli sürümü yap**' ı seçin.  

:::image type="content" source="media/soft-delete-blob-manage/soft-deleted-blob-promote-version-portal.png" alt-text="Azure portal bir blob 'u geri yüklemek için sürümün nasıl yükseltileceğini gösteren ekran görüntüsü":::

Sürüm oluşturma etkinken silinen sürümleri veya anlık görüntüleri geri yüklemek için Blobun özelliklerini görüntüleyin ve **genel bakış** sekmesinde **geri al** düğmesini seçin.

> [!NOTE]
> Sürüm oluşturma etkinleştirildiğinde, silinen bir blob üzerinde **geri al** düğmesinin seçilmesi, geçici olarak silinen sürümleri veya anlık görüntüleri geri yükler, ancak temel blobu geri yüklemez. Temel blobu geri yüklemek için önceki bir sürümü yükseltmeniz gerekir.

## <a name="manage-soft-deleted-blobs-with-code"></a>Yazılımla silinen Blobları kodla yönetme

Geçici olarak silinen bir Blobu veya anlık görüntüyü geri yüklemek için Azure Storage istemci kitaplıklarını kullanabilirsiniz. Aşağıdaki örneklerde .NET istemci kitaplığının nasıl kullanılacağı gösterilmektedir.

### <a name="restore-soft-deleted-objects-when-versioning-is-disabled"></a>Sürüm oluşturma devre dışı bırakıldığında, geçici olarak silinen nesneleri geri yükleme

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Sürüm oluşturma etkin olmadığında silinen Blobları geri yüklemek için, bu bloblarda [geri alma blobu](/rest/api/storageservices/undelete-blob) işlemini çağırın. **Silme blobu** işlemi, geçici olarak silinen Blobları ve bu bloblarla ilişkili silinen anlık görüntüleri geri yükler.

Silinmesiz bir blob üzerinde **geri alma blobu** çağırma hiçbir etkiye sahip değildir. Aşağıdaki örnek, bir kapsayıcıdaki tüm bloblarda **geri alma blobu** çağırır ve geçici olarak silinen Blobları ve bunların anlık görüntülerini geri yükler:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_RecoverDeletedBlobs":::

Belirli bir sürümü geri yüklemek için, önce temel blob veya sürümde **geri alma blobu** işlemini çağırın, sonra istediğiniz sürümü temel blob üzerine kopyalayın. Aşağıdaki örnek bir Blok Blobu en son kaydedilen sürüme geri yükler:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_RecoverSpecificBlobSnapshot":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Sürüm oluşturma etkin olmadığında silinen Blobları geri yüklemek için, bu bloblarda [geri alma blobu](/rest/api/storageservices/undelete-blob) işlemini çağırın. **Silme blobu** işlemi, geçici olarak silinen Blobları ve bu bloblarla ilişkili silinen anlık görüntüleri geri yükler.

Silinmesiz bir blob üzerinde **geri alma blobu** çağırma hiçbir etkiye sahip değildir. Aşağıdaki örnek, bir kapsayıcıdaki tüm bloblarda **geri alma blobu** çağırır ve geçici olarak silinen Blobları ve bunların anlık görüntülerini geri yükler:

```csharp
// Restore all blobs in a container.
foreach (CloudBlob blob in container.ListBlobs(useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Deleted))
{
       await blob.UndeleteAsync();
}
```

Belirli bir anlık görüntüyü geri yüklemek için, önce temel Blobun üzerinde **geri alma blobu** işlemini çağırın, sonra istediğiniz anlık görüntüyü temel blob üzerine kopyalayın. Aşağıdaki örnek, Blok Blobu en son oluşturulan anlık görüntüye geri yükler:

```csharp
// Restore the block blob.
await blockBlob.UndeleteAsync();

// List all blobs and snapshots in the container, prefixed by the blob name.
IEnumerable<IListBlobItem> allBlobSnapshots = container.ListBlobs(
    prefix: blockBlob.Name, useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Snapshots);

// Copy the most recently generated snapshot to the base blob.
CloudBlockBlob copySource = allBlobSnapshots.First(snapshot => ((CloudBlockBlob)version).IsSnapshot &&
    ((CloudBlockBlob)snapshot).Name == blockBlob.Name) as CloudBlockBlob;
blockBlob.StartCopy(copySource);
```  

---

### <a name="restore-soft-deleted-blobs-when-versioning-is-enabled"></a>Sürüm oluşturma etkinken geçici olarak silinen Blobları geri yükleme

Sürüm oluşturma etkinken, geçici olarak silinen bir blobu geri yüklemek için, önceki bir sürümü bir [kopya blobu](/rest/api/storageservices/copy-blob) veya [URL 'den blob kopyalama](/rest/api/storageservices/copy-blob-from-url) işleminden kopyalayın.  

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_RestorePreviousVersion":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Geçerli değildir. Blob sürümü oluşturma yalnızca Azure Storage istemci kitaplıkları sürümü 12. x ve üzeri sürümlerde desteklenir.

---

## <a name="next-steps"></a>Sonraki adımlar

- [BLOB depolama için geçici silme](./soft-delete-blob-overview.md)
- [Bloblar için geçici silmeyi etkinleştirme](soft-delete-blob-enable.md)
- [Blob sürümü oluşturma](versioning-overview.md)
