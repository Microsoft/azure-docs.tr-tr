---
title: Azure depolama API 'Leriyle blob kopyalama
description: Azure depolama istemci kitaplıklarını kullanarak bir Blobun nasıl kopyalanacağını öğrenin.
services: storage
author: twooley
ms.author: twooley
ms.date: 01/08/2021
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: bfd471a880e167bb2cc92bc87925b4aac91f66af
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106278482"
---
# <a name="copy-a-blob-with-azure-storage-client-libraries"></a>Azure depolama istemci kitaplıklarıyla blob kopyalama

Bu makalede, bir Azure depolama hesabındaki bir Blobun nasıl kopyalanacağı gösterilmektedir. Ayrıca, zaman uyumsuz bir kopyalama işleminin nasıl iptal alınacağını gösterir. Örnek kod, Azure depolama istemci kitaplıklarını kullanır.

## <a name="about-copying-blobs"></a>Blob 'ları kopyalama hakkında

Aynı depolama hesabı içindeki bir blobu kopyaladığınızda, zaman uyumlu bir işlemdir. Hesaplar arasında kopyalama yaptığınızda zaman uyumsuz bir işlemdir.

Kopyalama işlemi için kaynak blobu bir Blok Blobu, bir ekleme blobu, Sayfa Blobu veya anlık görüntü olabilir. Hedef blobu zaten varsa, kaynak blobu ile aynı blob türünde olması gerekir. Var olan bir hedef Blobun üzerine yazılacak.

Hedef blobu, bir kopyalama işlemi sürerken değiştirilemez. Hedef Blobun yalnızca bir adet bekleyen kopyalama işlemi olabilir. Diğer bir deyişle, bir blob birden çok bekleyen kopyalama işlemi için hedef olamaz.

Tüm kaynak Blobu veya dosya her zaman kopyalanır. Bir dizi bayt veya blok kümesi kopyalama desteklenmez.

Blob kopyalandığında, sistem özellikleri hedef Blobun aynı değerlerle kopyalanır.

Kopyalama işlemi aşağıdaki formlardan herhangi birini gerçekleştirebilir:

- Kaynak blobunu, farklı bir ada sahip bir hedef bloba kopyalayın. Hedef blobu aynı blob türünde (blok, ekleme veya sayfa) var olan bir BLOB olabilir veya kopyalama işlemi tarafından oluşturulan yeni bir BLOB olabilir.
- Kaynak blobu, hedef blobu etkin bir şekilde değiştirerek aynı ada sahip bir hedef bloba kopyalayın. Bu tür bir kopyalama işlemi, kaydedilmemiş blokları kaldırır ve hedef Blobun meta verilerinin üzerine yazar.
- Azure dosya hizmetindeki bir kaynak dosyayı hedef bloba kopyalayın. Hedef blobu varolan bir Blok Blobu olabilir veya kopyalama işlemi tarafından oluşturulan yeni bir Blok Blobu olabilir. Dosyalardan sayfa bloblarına veya ekleme bloblarına kopyalama desteklenmez.
- Bir anlık görüntüyü temel blobunun üzerine kopyalayın. Bir anlık görüntüyü temel Blobun konumuna yükselterek bir blob 'un önceki bir sürümünü geri yükleyebilirsiniz.
- Bir anlık görüntüyü hedef bloba farklı bir adla kopyalayın. Elde edilen hedef blobu anlık görüntü değil yazılabilir bir Blobun.

## <a name="copy-a-blob"></a>Blob kopyalama

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Bir blobu kopyalamak için aşağıdaki yöntemlerden birini çağırın:

- [StartCopyFromUri](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.startcopyfromuri)
- [StartCopyFromUriAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.startcopyfromuriasync)

`StartCopyFromUri`Ve `StartCopyFromUriAsync` yöntemleri kopyalama işlemi hakkında bilgi Içeren bir [Copyfromurioperation](/dotnet/api/azure.storage.blobs.models.copyfromurioperation) nesnesi döndürür.

Aşağıdaki kod örneği, daha önce oluşturulmuş bir blobu temsil eden bir [Blobclient](/dotnet/api/azure.storage.blobs.blobclient) alır ve onu aynı kapsayıcıda yeni bir bloba kopyalar:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CopyBlob.cs" id="Snippet_CopyBlob":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Bir blobu kopyalamak için aşağıdaki yöntemlerden birini çağırın:

- [StartCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy)
- [StartCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync)

`StartCopy`Ve `StartCopyAsync` yöntemleri, durumu denetlemek veya kopyalama işlemini durdurmak için kullanılan BIR kopya kimliği değeri döndürür.

Aşağıdaki kod örneği, daha önce oluşturulmuş bir Blobun başvurusunu alır ve aynı kapsayıcıda yeni bir bloba kopyalar:

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

        // Lease the source blob for the copy operation 
        // to prevent another client from modifying it.
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

# <a name="python-v12"></a>[Python V12](#tab/python)

Bir blobu kopyalamak için [start_copy_from_url](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.blobclient#start-copy-from-url-source-url--metadata-none--incremental-copy-false----kwargs-) yöntemini çağırın. `start_copy_from_url`Yöntemi, kopyalama işlemi hakkında bilgi içeren bir sözlük döndürür.

Aşağıdaki kod örneği, daha önce oluşturulmuş bir blobu temsil eden bir [Blobclient](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.blobclient) alır ve onu aynı kapsayıcıda yeni bir bloba kopyalar:

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/copy_blob.py" id="Snippet_BlobCopy":::

---

## <a name="abort-a-copy-operation"></a>Kopyalama işlemini iptal et

Bir kopyalama işleminin iptal edilmeden, sıfır uzunluğunda bir hedef Blobun sonucu oluşur. Ancak, hedef Blobun meta verileri, kaynak Blobun yeni değerleri kopyalayacak veya kopyalama işlemi sırasında açıkça ayarlanmış olacaktır. Özgün meta verileri kopyalama öncesinde tutmak için, kopyalama yöntemlerinden birini çağırmadan önce hedef Blobun anlık görüntüsünü oluşturun.

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Kopyalama işleminin durumunu almak için hedef Blobun üzerindeki [Blobproperties. CopyStatus](/dotnet/api/azure.storage.blobs.models.blobproperties.copystatus) özelliğini denetleyin. Kopyalama tamamlandığında son blob uygulanır.

Bir kopyalama işlemini iptal ettiğinizde, hedef Blobun kopyalama durumu [Copystatus. durduruldu](/dotnet/api/microsoft.azure.storage.blob.copystatus)olarak ayarlanır.

[Abortcopyfromuri](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.abortcopyfromuri) ve [Abortcopyfromuriasync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.abortcopyfromuriasync) yöntemleri devam eden bir kopyalama işlemini iptal eder.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CopyBlob.cs" id="Snippet_StopBlobCopy":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Kopyalama işleminin durumunu almak için hedef Blobun [CopyState. Status](/dotnet/api/microsoft.azure.storage.blob.copystate.status) özelliğini denetleyin. Kopyalama tamamlandığında son blob uygulanır.

Bir kopyalama işlemini iptal ettiğinizde, hedef Blobun kopyalama durumu [Copystatus. durduruldu](/dotnet/api/microsoft.azure.storage.blob.copystatus)olarak ayarlanır.

[Abortcopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.abortcopy) ve [Abortcopyasync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.abortcopyasync) yöntemleri devam eden bir kopyalama işlemini iptal eder.

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

# <a name="python-v12"></a>[Python V12](#tab/python)

Kopyalama işleminin durumunu almak için [get_blob_properties](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.blobclient#get-blob-properties---kwargs-) yöntemi tarafından döndürülen [CopyProperties](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.copyproperties) sözlüğündeki "durum" girişini denetleyin. Kopyalama tamamlandığında son blob uygulanır.

Bir kopyalama işlemini iptal ettiğinizde, [durum](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.copyproperties) "durduruldu" olarak ayarlanır.

[Abort_copy](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.blobclient#abort-copy-copy-id----kwargs-) yöntemi devam eden bir kopyalama işlemini iptal eder.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/copy_blob.py" id="Snippet_StopBlobCopy":::

---

## <a name="azure-sdks"></a>Azure SDK’ları

Azure SDK 'Ları hakkında daha fazla bilgi alın:

 - [.NET için Azure SDK](https://github.com/azure/azure-sdk-for-net)
 - [Java için Azure SDK](https://github.com/azure/azure-sdk-for-java)
 - [Python için Azure SDK](https://github.com/azure/azure-sdk-for-python)
 - [JavaScript için Azure SDK](https://github.com/azure/azure-sdk-for-js)

## <a name="next-steps"></a>Sonraki adımlar

Aşağıdaki konular, Azure REST API 'Lerini kullanarak blob 'ları kopyalama ve devam eden kopyalama işlemlerini iptal etme hakkında bilgiler içerir.

- [İkili Büyük Nesneyi Kopyalama](/rest/api/storageservices/copy-blob)
- [Kopyalama blobu durdur](/rest/api/storageservices/abort-copy-blob)
