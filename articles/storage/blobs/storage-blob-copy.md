---
title: .NET-Azure Storage ile blob kopyalama
description: .NET istemci kitaplığını kullanarak Azure Storage hesabınızda bir blob kopyalamayı öğrenin.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/20/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.openlocfilehash: 9b3dba0041b38d9d59a10eaf80592bab91f65b98
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/18/2019
ms.locfileid: "72600286"
---
# <a name="copy-a-blob-with-net"></a>.NET ile blob kopyalama

Bu makalede, bir blob 'un Azure Storage hesabıyla nasıl kopyalanacağı gösterilmektedir. Ayrıca, zaman uyumsuz bir kopyalama işleminin nasıl iptal alınacağını gösterir. Örnek kod, [.net Için Azure Storage istemci kitaplığı](/dotnet/api/overview/azure/storage/client)'nı kullanır.

## <a name="about-copying-blobs"></a>Blob 'ları kopyalama hakkında

Aynı depolama hesabı içindeki bir blobu kopyaladığınızda, zaman uyumlu bir işlemdir. Hesaplar arasında kopyalama yaptığınızda zaman uyumsuz bir işlemdir. [StartCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy?view=azure-dotnet) ve [Startcopyasync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync?view=azure-dotnet) yöntemleri, durumu denetlemek veya kopyalama işlemini durdurmak IÇIN kullanılan bir kopya kimliği değeri döndürür.

Kopyalama işlemi için kaynak blobu bir Blok Blobu, bir ekleme blobu, Sayfa Blobu veya anlık görüntü olabilir. Hedef blobu zaten varsa, kaynak blobu ile aynı blob türünde olması gerekir. Var olan tüm hedef Blobun üzerine yazılacak. 

Hedef blobu, bir kopyalama işlemi sürerken değiştirilemez. Hedef Blobun yalnızca bir adet bekleyen kopyalama blob işlemi olabilir. Diğer bir deyişle, bir blob birden çok bekleyen kopyalama işlemi için hedef olamaz.

Tüm kaynak Blobu veya dosya her zaman kopyalanır. Bir dizi bayt veya blok kümesi kopyalama desteklenmez.

Blob kopyalandığında, sistem özellikleri hedef Blobun aynı değerlerle kopyalanır.

Tüm blob türlerinde, kopyalama işleminin durumunu almak için hedef Blobun [CopyState. Status](/dotnet/api/microsoft.azure.storage.blob.copystate.status?view=azure-dotnet) özelliğini kontrol edebilirsiniz. Kopyalama tamamlandığında son blob uygulanır.

Kopyalama işlemi aşağıdaki formlardan herhangi birini gerçekleştirebilir:

  - Kaynak blobu, farklı bir ada sahip bir hedef bloba kopyalayabilirsiniz. Hedef blobu aynı blob türünde (blok, ekleme veya sayfa) var olan bir BLOB olabilir veya kopyalama işlemi tarafından oluşturulan yeni bir BLOB olabilir.
  - Kaynak blobu, hedef blobu etkin bir şekilde değiştirerek aynı ada sahip bir hedef bloba kopyalayabilirsiniz. Bu tür bir kopyalama işlemi, kaydedilmemiş blokları kaldırır ve hedef Blobun meta verilerinin üzerine yazar.
  - Azure dosya hizmetindeki bir kaynak dosyayı hedef bloba kopyalayabilirsiniz. Hedef blobu varolan bir Blok Blobu olabilir veya kopyalama işlemi tarafından oluşturulan yeni bir Blok Blobu olabilir. Dosyalardan sayfa bloblarına veya ekleme bloblarına kopyalama desteklenmez.
  - Bir anlık görüntüyü kendi temel blobundan kopyalayabilirsiniz. Bir anlık görüntüyü temel Blobun konumuna yükselterek bir blob 'un önceki bir sürümünü geri yükleyebilirsiniz.
  - Bir anlık görüntüyü hedef bloba farklı bir adla kopyalayabilirsiniz. Elde edilen hedef blobu anlık görüntü değil yazılabilir bir Blobun.

## <a name="copy-a-blob"></a>Blob kopyalama

Bir blobu kopyalamak için aşağıdaki yöntemlerden birini çağırın:

 - [StartCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy?view=azure-dotnet)
 - [StartCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync?view=azure-dotnet)

Aşağıdaki kod örneği, daha önce oluşturulan bir Blobun başvurusunu alır ve aynı kapsayıcıda yeni bir bloba kopyalar:

```csharp
private static async Task CopyBlockBlobAsync(CloudBlobContainer container)
{
    CloudBlockBlob sourceBlob = null;
    CloudBlockBlob destBlob = null;
    string leaseId = null;

    try
    {
        // Get a block blob from the container to use as the source.
        sourceBlob = container.ListBlobs().OfType<CloudBlockBlob>().FirstOrDefault();

        // Lease the source blob for the copy operation to prevent another client from modifying it.
        // Specifying null for the lease interval creates an infinite lease.
        leaseId = await sourceBlob.AcquireLeaseAsync(null);

        // Get a reference to a destination blob (in this case, a new blob).
        destBlob = container.GetBlockBlobReference("copy of " + sourceBlob.Name);

        // Ensure that the source blob exists.
        if (await sourceBlob.ExistsAsync())
        {
            // Get the ID of the copy operation.
            string copyId = await destBlob.StartCopyAsync(sourceBlob);

            // Fetch the destination blob's properties before checking the copy state.
            await destBlob.FetchAttributesAsync();

            Console.WriteLine("Status of copy operation: {0}", destBlob.CopyState.Status);
            Console.WriteLine("Completion time: {0}", destBlob.CopyState.CompletionTime);
            Console.WriteLine("Bytes copied: {0}", destBlob.CopyState.BytesCopied.ToString());
            Console.WriteLine("Total bytes: {0}", destBlob.CopyState.TotalBytes.ToString());
            Console.WriteLine();
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
    finally
    {
        // Break the lease on the source blob.
        if (sourceBlob != null)
        {
            await sourceBlob.FetchAttributesAsync();

            if (sourceBlob.Properties.LeaseState != LeaseState.Available)
            {
                await sourceBlob.BreakLeaseAsync(new TimeSpan(0));
            }
        }
    }
}
```

## <a name="abort-a-blob-copy-operation"></a>Blob kopyalama işlemini durdur

Bir kopyalama işleminin iptal edilmeden, blok Blobları, ekleme Blobları ve sayfa Blobları için sıfır uzunluğundaki hedef Blobun sonuçlanır. Ancak, hedef Blobun meta verileri, kaynak Blobun yeni değerleri kopyalayacak veya [StartCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy?view=azure-dotnet) ya da [Startcopyasync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync?view=azure-dotnet) çağrısında açıkça ayarlanmış olacak. Özgün meta verileri kopyalama öncesinde tutmak için, `StartCopy` veya `StartCopyAsync` çağrılmadan önce hedef Blobun anlık görüntüsünü alın.

Devam eden bir blob kopyalama işlemini iptal ettiğinizde, hedef Blobun [CopyState. Status](/dotnet/api/microsoft.azure.storage.blob.copystate.status?view=azure-dotnet#Microsoft_Azure_Storage_Blob_CopyState_Status) , [Copystatus. durduruldu](/dotnet/api/microsoft.azure.storage.blob.copystatus?view=azure-dotnet)olarak ayarlanır.

[Abortcopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.abortcopy?view=azure-dotnet) ve [Abortcopyasync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.abortcopyasync?view=azure-dotnet) yöntemleri devam eden bir blob kopyalama işlemini iptal eder ve bir hedef Blobun sıfır uzunluğunda ve tam meta verilerle ayrılmaz.

```csharp
// Fetch the destination blob's properties before checking the copy state.
await destBlob.FetchAttributesAsync();

// Check the copy status. If it is still pending, abort the copy operation.
if (destBlob.CopyState.Status == CopyStatus.Pending)
{
    await destBlob.AbortCopyAsync(copyId);
    Console.WriteLine("Copy operation {0} has been aborted.", copyId);
}
```

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="next-steps"></a>Sonraki adımlar

Aşağıdaki konular, Azure REST API 'Lerini kullanarak blob 'ları kopyalama ve devam eden kopyalama işlemlerini iptal etme hakkında bilgiler içerir.

- [Blobu Kopyala](/rest/api/storageservices/copy-blob)
- [Kopyalama blobu durdur](/rest/api/storageservices/abort-copy-blob)
