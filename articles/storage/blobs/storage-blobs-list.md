---
title: .NET-Azure depolama ile Blobları listeleme
description: .NET istemci kitaplığını kullanarak Azure Storage hesabınızdaki bir kapsayıcıdaki Blobları listeleyeceğinizi öğrenin. Kod örnekleri, blob 'ların bir düz listede nasıl ekleneceğini ya da dizin veya klasörler halinde düzenlendikleri gibi Blobları hiyerarşik olarak nasıl listelemiş olduğunu gösterir.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 11/08/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 4b6dc9d80cfe96e501e575d265b9fa383b1c4d2c
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/10/2019
ms.locfileid: "73902012"
---
# <a name="list-blobs-with-net"></a>.NET ile Blobları listeleme

Kodlarınızdan blob 'ları listelürsünüz, sonuçların Azure depolama 'dan nasıl döndürüleceğini yönetmek için bir dizi seçenek belirtebilirsiniz. Her bir sonuç kümesinde döndürülecek sonuç sayısını belirtebilir ve ardından sonraki kümeleri elde edebilirsiniz. Adları bu karakterle veya dizeyle başlayan Blobları döndürmek için bir ön ek belirtebilirsiniz. Blob 'ları düz bir liste yapısında veya hiyerarşik olarak listeleyebilirsiniz. Hiyerarşik bir liste Blobları klasörler halinde düzenlenmiş gibi döndürür. 

Bu makalede, [.net Için Azure Storage istemci kitaplığı](/dotnet/api/overview/azure/storage/client)kullanılarak Blobların nasıl listeyapılacağı gösterilmektedir.  

## <a name="understand-blob-listing-options"></a>Blob listeleme seçeneklerini anlama

Blob 'ları bir depolama hesabında listelemek için şu yöntemlerden birini çağırın:

- [CloudBlobClient. Listblob 'Lar](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobs)
- [CloudBlobClient. Listblobskesimli](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobssegmented)
- [CloudBlobClient. ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobssegmentedasync)

Bir kapsayıcıdaki Blobları listelemek için şu yöntemlerden birini çağırın:

- [CloudBlobContainer. Listblob 'Lar](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobs)
- [CloudBlobContainer. Listblobskesimli](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmented)
- [CloudBlobContainer. ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmentedasync)

Bu yöntemlerin aşırı yüklemeleri, Blobların listeleme işlemi tarafından nasıl döndürüldüğünü yönetmek için ek seçenekler sağlar. Bu seçenekler aşağıdaki bölümlerde açıklanmıştır.

### <a name="manage-how-many-results-are-returned"></a>Kaç sonuç döndürüldüğünü yönetin

Varsayılan olarak, bir listeleme işlemi bir seferde en fazla 5000 sonuç döndürür. Daha küçük bir sonuç kümesi döndürmek için, **Listblobları** yöntemlerinden birini çağırırken `maxresults` parametresi için sıfır olmayan bir değer sağlayın.

Bir listeleme işlemi 5000 'den fazla BLOB döndürürse veya `maxresults` için bir değer belirttiyseniz, listeleme işlemi depolama hesabındaki kapsayıcıların bir alt kümesini döndürürse Azure Storage, blob 'ların listesini içeren bir *devamlılık belirteci* döndürür. Devamlılık belirteci, Azure depolama 'nın bir sonraki sonuç kümesini almak için kullanabileceğiniz donuk bir değerdir.

Kodunuzda, null olup olmadığını anlamak için devamlılık belirtecinin değerini denetleyin. Devamlılık belirteci null olduğunda, sonuç kümesi tamamlanır. Devamlılık belirteci null değilse, devamlılık belirteci null olana kadar, sonraki sonuç kümesini almak için devamlılık belirtecini geçirerek Listeleme işlemini yeniden çağırın.

### <a name="filter-results-with-a-prefix"></a>Sonuçları bir ön eke göre filtrele

Kapsayıcılar listesini filtrelemek için `prefix` parametresi için bir dize belirtin. Ön ek dizesi bir veya daha fazla karakter içerebilir. Daha sonra Azure Storage yalnızca adları bu önek ile başlayan blob 'ları döndürür.

### <a name="return-metadata"></a>Meta veri döndür

Sonuçlara blob meta verileri döndürmek için [Bloblistingdetails](/dotnet/api/microsoft.azure.storage.blob.bloblistingdetails) numaralandırması Için **meta veri** değerini belirtin. Azure depolama, her blob döndürülen meta verileri içerir, bu nedenle blob meta verilerini almak için bu bağlamdaki **Fetchattributes** yöntemlerinden birini çağırmanız gerekmez.

### <a name="flat-listing-versus-hierarchical-listing"></a>Düz liste ve hiyerarşik listeye karşı

Azure depolama 'daki Bloblar, hiyerarşik bir paradigma (klasik bir dosya sistemi gibi) yerine düz bir paradigma göre düzenlenir. Ancak, bir klasör yapısını benzemek için Blobları *sanal dizinlere* göre düzenleyebilirsiniz. Bir sanal dizin, blob adının bir parçasını oluşturur ve sınırlayıcı karakterle belirtilir.

Blob 'ları sanal dizinlere göre düzenlemek için blob adında bir sınırlayıcı karakter kullanın. Varsayılan sınırlayıcı karakter eğik çizgidir (/), ancak sınırlayıcı olarak herhangi bir karakter belirtebilirsiniz.

Bloblarınızı bir sınırlayıcı kullanarak adlandırın, Blobları hiyerarşik olarak listeleyebilirsiniz. Hiyerarşik bir listeleme işlemi için Azure depolama, üst nesnenin altındaki tüm sanal dizinleri ve BLOB 'ları döndürür. Bir klasik dosya sisteminin programlama yoluyla nasıl gezineceği gibi, hiyerarşide çapraz geçiş yapmak için Listeleme işlemini yinelemeli olarak çağırabilirsiniz.

## <a name="use-a-flat-listing"></a>Düz liste kullan

Varsayılan olarak, bir listeleme işlemi blob 'ları düz bir listede döndürür. Düz bir listede, Bloblar sanal dizin tarafından düzenlenmez.

Aşağıdaki örnek, bir düz liste kullanarak belirtilen kapsayıcıdaki Blobları listeler ve isteğe bağlı bir kesim boyutu belirtildi ve BLOB adını bir konsol penceresine yazar.

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
                // A flat listing operation returns only blobs, not virtual directories.
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

Örnek çıkış şuna benzerdir:

```
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

Bir Listeleme işlemini hiyerarşik olarak çağırdığınızda, Azure Storage hiyerarşinin ilk düzeyindeki sanal dizinleri ve Blobları döndürür. Her sanal dizinin [önek](/dotnet/api/microsoft.azure.storage.blob.cloudblobdirectory.prefix) özelliği, bir sonraki dizini almak için bir özyinelemeli çağrıda öneki geçirebilmeniz için ayarlanır.

Blob 'ları hiyerarşik olarak listelemek için, liste yönteminin `useFlatBlobListing` parametresini **false**olarak ayarlayın.

Aşağıdaki örnek, bir düz liste kullanarak belirtilen kapsayıcıdaki Blobları listeler ve isteğe bağlı bir kesim boyutu belirtildi ve BLOB adını konsol penceresine yazar.

```csharp
private static async Task ListBlobsHierarchicalListingAsync(CloudBlobContainer container, string prefix)
{
    CloudBlobDirectory dir;
    CloudBlob blob;
    BlobContinuationToken continuationToken;

    try
    {
        // Call the listing operation and enumerate the result segment.
        // When the continuation token is null, the last segment has been returned and 
        // execution can exit the loop.
        do
        {
            BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(prefix,
                false, BlobListingDetails.Metadata, null, null, null, null);
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

Örnek çıkış şuna benzerdir:

```
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
