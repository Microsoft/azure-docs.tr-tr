---
title: Azure depolama API 'Leri ile Blobları listeleme
description: Azure depolama istemci kitaplıklarını kullanarak Depolama hesabınızdaki Blobları listeleme hakkında bilgi edinin. Kod örnekleri, blob 'ların bir düz listede nasıl ekleneceğini ya da dizin veya klasörler halinde düzenlendikleri gibi Blobları hiyerarşik olarak nasıl listelemiş olduğunu gösterir.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/24/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: ff20b8bd0aab94cadadddbb7a4b7b32b1db1ee85
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105046951"
---
# <a name="list-blobs-with-azure-storage-client-libraries"></a>Azure Storage istemci kitaplıkları ile Blobları listeleme

Kodlarınızdan blob 'ları listelürsünüz, sonuçların Azure depolama 'dan nasıl döndürüleceğini yönetmek için bir dizi seçenek belirtebilirsiniz. Her bir sonuç kümesinde döndürülecek sonuç sayısını belirtebilir ve ardından sonraki kümeleri elde edebilirsiniz. Adları bu karakterle veya dizeyle başlayan Blobları döndürmek için bir ön ek belirtebilirsiniz. Blob 'ları düz bir liste yapısında veya hiyerarşik olarak listeleyebilirsiniz. Hiyerarşik bir liste Blobları klasörler halinde düzenlenmiş gibi döndürür.

## <a name="understand-blob-listing-options"></a>Blob listeleme seçeneklerini anlama

Blob 'ları bir depolama hesabında listelemek için şu yöntemlerden birini çağırın:

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

- [BlobContainerClient. Getbloblar](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobs)
- [BlobContainerClient. GetBlobsAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsasync)
- [BlobContainerClient. GetBlobsByHierarchy](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsbyhierarchy)
- [BlobContainerClient. Getblobsbyhiyerarşik Yasync](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsbyhierarchyasync)

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

- [CloudBlobClient. Listblob 'Lar](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobs)
- [CloudBlobClient. Listblobskesimli](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobssegmented)
- [CloudBlobClient. ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobssegmentedasync)

Bir kapsayıcıdaki Blobları listelemek için şu yöntemlerden birini çağırın:

- [CloudBlobContainer. Listblob 'Lar](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobs)
- [CloudBlobContainer. Listblobskesimli](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmented)
- [CloudBlobContainer. ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmentedasync)

# <a name="python-v12"></a>[Python V12](#tab/python)

- [ContainerClient.list_blobs](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.containerclient#list-blobs-name-starts-with-none--include-none----kwargs-)

---

### <a name="manage-how-many-results-are-returned"></a>Kaç sonuç döndürüldüğünü yönetin

Varsayılan olarak, bir listeleme işlemi bir seferde en fazla 5000 sonuç döndürür, ancak her listeleme işleminin dönmesini istediğiniz sonuç sayısını belirtebilirsiniz. Bu makalede sunulan örneklerde, sayfalardaki sonuçların nasıl döndürülayarlanacağı gösterilmektedir.

### <a name="filter-results-with-a-prefix"></a>Sonuçları bir ön eke göre filtrele

Blobların listesini filtrelemek için, parametre için bir dize belirtin `prefix` . Ön ek dizesi bir veya daha fazla karakter içerebilir. Daha sonra Azure Storage yalnızca adları bu önek ile başlayan blob 'ları döndürür.

### <a name="return-metadata"></a>Meta veri döndür

Sonuçlarla blob meta verileri döndürebilirsiniz.

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

[Blobnitelikleri](/dotnet/api/azure.storage.blobs.models.blobtraits) numaralandırması Için **meta veri** değerini belirtin.

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

[Bloblistingdetails](/dotnet/api/microsoft.azure.storage.blob.bloblistingdetails) numaralandırması Için **meta veri** değerini belirtin. Azure depolama, her blob döndürülen meta verileri içerir, bu nedenle blob meta verilerini almak için bu bağlamdaki **Fetchattributes** yöntemlerinden birini çağırmanız gerekmez.

# <a name="python-v12"></a>[Python V12](#tab/python)

`metadata` `include=` [List_blobs](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.containerclient#list-blobs-name-starts-with-none--include-none----kwargs-)çağrılırken parametre için belirtin.

---

### <a name="list-blob-versions-or-snapshots"></a>Blob sürümlerini veya anlık görüntülerini Listele

- Blob sürümlerini veya anlık görüntüleri .NET V12 istemci kitaplığı ile listelemek için **Sürüm** veya **anlık görüntü** alanıyla [blobstates](/dotnet/api/azure.storage.blobs.models.blobstates) parametresini belirtin. Sürümler ve anlık görüntüler en eskiden en yeniye listelenmiştir. Sürümleri listeleme hakkında daha fazla bilgi için bkz. [BLOB sürümlerini listeleme](versioning-enable.md#list-blob-versions).

- Python V12 istemci kitaplığı ile anlık görüntü sayısını listelemek için `num_snapshots` `include=` [list_blobs](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.containerclient#list-blobs-name-starts-with-none--include-none----kwargs-)çağrılırken parametresini belirtin.

### <a name="flat-listing-versus-hierarchical-listing"></a>Düz liste ve hiyerarşik listeye karşı

Azure depolama 'daki Bloblar, hiyerarşik bir paradigma (klasik bir dosya sistemi gibi) yerine düz bir paradigma göre düzenlenir. Ancak, bir klasör yapısını benzemek için Blobları *sanal dizinlere* göre düzenleyebilirsiniz. Bir sanal dizin, blob adının bir parçasını oluşturur ve sınırlayıcı karakterle belirtilir.

Blob 'ları sanal dizinlere göre düzenlemek için blob adında bir sınırlayıcı karakter kullanın. Varsayılan sınırlayıcı karakter eğik çizgidir (/), ancak sınırlayıcı olarak herhangi bir karakter belirtebilirsiniz.

Bloblarınızı bir sınırlayıcı kullanarak adlandırın, Blobları hiyerarşik olarak listeleyebilirsiniz. Hiyerarşik bir listeleme işlemi için Azure depolama, üst nesnenin altındaki tüm sanal dizinleri ve BLOB 'ları döndürür. Bir klasik dosya sisteminin programlama yoluyla nasıl gezineceği gibi, hiyerarşide çapraz geçiş yapmak için Listeleme işlemini yinelemeli olarak çağırabilirsiniz.

## <a name="use-a-flat-listing"></a>Düz liste kullan

Varsayılan olarak, bir listeleme işlemi blob 'ları düz bir listede döndürür. Düz bir listede, Bloblar sanal dizin tarafından düzenlenmez.

Aşağıdaki örnek, bir düz liste kullanarak belirtilen kapsayıcıdaki Blobları listeler ve isteğe bağlı bir kesim boyutu belirtildi ve BLOB adını bir konsol penceresine yazar.

Hesabınızda hiyerarşik ad alanı özelliğini etkinleştirdiyseniz, dizinler sanal değildir. Bunun yerine somut, bağımsız nesnelerdir. Bu nedenle, dizinler listede sıfır uzunluklu blob 'lar olarak görünür.

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_ListBlobsFlatListing":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Bir listeleme işlemi 5000 'den fazla BLOB döndürürse veya kullanılabilir BLOB sayısı belirttiğiniz sayıyı aşarsa Azure Storage, blob 'ların listesini içeren bir *devamlılık belirteci* döndürür. Devamlılık belirteci, Azure depolama 'nın bir sonraki sonuç kümesini almak için kullanabileceğiniz donuk bir değerdir.

Kodunuzda, null olup olmadığını anlamak için devamlılık belirtecinin değerini denetleyin. Devamlılık belirteci null olduğunda, sonuç kümesi tamamlanır. Devamlılık belirteci null değilse, devamlılık belirteci null olana kadar, sonraki sonuç kümesini almak için devamlılık belirtecini geçirerek Listeleme işlemini yeniden çağırın.

```csharp
private static async Task ListBlobsFlatListingAsync(CloudBlobContainer container, int? segmentSize)
{
    BlobContinuationToken continuationToken = null;
    CloudBlob blob;

    try
    {
        // Call the listing operation and enumerate the result segment.
        // When the continuation token is null, the last segment has been returned
        // and execution can exit the loop.
        do
        {
            BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(string.Empty,
                true, BlobListingDetails.Metadata, segmentSize, continuationToken, null, null);

            foreach (var blobItem in resultSegment.Results)
            {
                blob = (CloudBlob)blobItem;

                // Write out some blob properties.
                Console.WriteLine("Blob name: {0}", blob.Name);
            }

            Console.WriteLine();

           // Get the continuation token and loop until it is null.
           continuationToken = resultSegment.ContinuationToken;

        } while (continuationToken != null);
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

# <a name="python-v12"></a>[Python V12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/list_blobs.py" id="Snippet_ListBlobs":::

---

Örnek çıkış şuna benzerdir:

```console
Blob name: FolderA/blob1.txt
Blob name: FolderA/blob2.txt
Blob name: FolderA/blob3.txt
Blob name: FolderA/FolderB/blob1.txt
Blob name: FolderA/FolderB/blob2.txt
Blob name: FolderA/FolderB/blob3.txt
Blob name: FolderA/FolderB/FolderC/blob1.txt
Blob name: FolderA/FolderB/FolderC/blob2.txt
Blob name: FolderA/FolderB/FolderC/blob3.txt
```

## <a name="use-a-hierarchical-listing"></a>Hiyerarşik bir liste kullanın

Bir Listeleme işlemini hiyerarşik olarak çağırdığınızda, Azure Storage hiyerarşinin ilk düzeyindeki sanal dizinleri ve Blobları döndürür.

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Blobları hiyerarşik olarak listelemek için [blobcontainerclient. GetBlobsByHierarchy](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsbyhierarchy)veya [Blobcontainerclient. Getblobsbysıradüzenli yasync](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsbyhierarchyasync) yöntemini çağırın.

Aşağıdaki örnek, belirtilen kapsayıcıdaki Blobları, isteğe bağlı bir kesim boyutuyla belirtilen hiyerarşik bir liste kullanılarak listeler ve BLOB adını konsol penceresine yazar.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_ListBlobsHierarchicalListing":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Her sanal dizinin [önek](/dotnet/api/microsoft.azure.storage.blob.cloudblobdirectory.prefix) özelliği, bir sonraki dizini almak için bir özyinelemeli çağrıda öneki geçirebilmeniz için ayarlanır.

Blobları hiyerarşik olarak listelemek için, `useFlatBlobListing` Listeleme yönteminin parametresini **false** olarak ayarlayın.

Aşağıdaki örnek, bir düz liste kullanarak belirtilen kapsayıcıdaki Blobları listeler ve isteğe bağlı bir kesim boyutu belirtildi ve BLOB adını konsol penceresine yazar.

```csharp
private static async Task ListBlobsHierarchicalListingAsync(CloudBlobContainer container, string prefix)
{
    CloudBlobDirectory dir;
    CloudBlob blob;
    BlobContinuationToken continuationToken = null;

    try
    {
        // Call the listing operation and enumerate the result segment.
        // When the continuation token is null, the last segment has been returned and
        // execution can exit the loop.
        do
        {
            BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(prefix,
                false, BlobListingDetails.Metadata, null, continuationToken, null, null);
            foreach (var blobItem in resultSegment.Results)
            {
                // A hierarchical listing may return both virtual directories and blobs.
                if (blobItem is CloudBlobDirectory)
                {
                    dir = (CloudBlobDirectory)blobItem;

                    // Write out the prefix of the virtual directory.
                    Console.WriteLine("Virtual directory prefix: {0}", dir.Prefix);

                    // Call recursively with the prefix to traverse the virtual directory.
                    await ListBlobsHierarchicalListingAsync(container, dir.Prefix);
                }
                else
                {
                    // Write out the name of the blob.
                    blob = (CloudBlob)blobItem;

                    Console.WriteLine("Blob name: {0}", blob.Name);
                }
                Console.WriteLine();
            }

            // Get the continuation token and loop until it is null.
            continuationToken = resultSegment.ContinuationToken;

        } while (continuationToken != null);
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

# <a name="python-v12"></a>[Python V12](#tab/python)

Blobları hiyerarşik olarak listelemek için [walk_blobs](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.containerclient#walk-blobs-name-starts-with-none--include-none--delimiter--------kwargs-) yöntemini çağırın.

Aşağıdaki örnek, belirtilen kapsayıcıdaki Blobları, isteğe bağlı bir kesim boyutuyla belirtilen hiyerarşik bir liste kullanılarak listeler ve BLOB adını konsol penceresine yazar.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/list_blobs.py" id="Snippet_WalkHierarchy":::

---

Örnek çıkış şuna benzerdir:

```console
Virtual directory prefix: FolderA/
Blob name: FolderA/blob1.txt
Blob name: FolderA/blob2.txt
Blob name: FolderA/blob3.txt

Virtual directory prefix: FolderA/FolderB/
Blob name: FolderA/FolderB/blob1.txt
Blob name: FolderA/FolderB/blob2.txt
Blob name: FolderA/FolderB/blob3.txt

Virtual directory prefix: FolderA/FolderB/FolderC/
Blob name: FolderA/FolderB/FolderC/blob1.txt
Blob name: FolderA/FolderB/FolderC/blob2.txt
Blob name: FolderA/FolderB/FolderC/blob3.txt
```

> [!NOTE]
> Blob anlık görüntüleri hiyerarşik bir listeleme işleminde listelenmiş olamaz.

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="next-steps"></a>Sonraki adımlar

- [Blobları Listele](/rest/api/storageservices/list-blobs)
- [Blob kaynaklarını numaralandırma](/rest/api/storageservices/enumerating-blob-resources)