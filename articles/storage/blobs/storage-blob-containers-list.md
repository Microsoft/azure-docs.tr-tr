---
title: .NET-Azure depolama ile blob kapsayıcılarını listeleme
description: .NET istemci kitaplığını kullanarak Azure Storage hesabınızdaki blob kapsayıcılarını listeleyeceğinizi öğrenin.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 10/14/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: a12fc991734fe74e450aa14a477f3a4500ba659c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96937262"
---
# <a name="list-blob-containers-with-net"></a>.NET ile blob kapsayıcılarını listeleme

Kodlarınızdan bir Azure depolama hesabındaki kapsayıcıları listelürsünüz, sonuçların Azure depolama 'dan nasıl döndürüleceğini yönetmek için bir dizi seçenek belirtebilirsiniz. Bu makalede, [.net Için Azure Storage istemci kitaplığı](/dotnet/api/overview/azure/storage)kullanılarak kapsayıcıların nasıl listeyapılacağı gösterilmektedir.  

## <a name="understand-container-listing-options"></a>Kapsayıcı listesi seçeneklerini anlama

Depolama hesabınızdaki kapsayıcıları listelemek için aşağıdaki yöntemlerden birini çağırın:

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

- [GetBlobContainers](/dotnet/api/azure.storage.blobs.blobserviceclient.getblobcontainers)
- [GetBlobContainersAsync](/dotnet/api/azure.storage.blobs.blobserviceclient.getblobcontainersasync)

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

- [Listcontainerskesimli](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listcontainerssegmented)
- [ListContainersSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listcontainerssegmentedasync)

---

Bu yöntemlerin aşırı yüklemeleri, kapsayıcıların listeleme işlemi tarafından nasıl döndürüleceğini yönetmek için ek seçenekler sağlar. Bu seçenekler aşağıdaki bölümlerde açıklanmıştır.

### <a name="manage-how-many-results-are-returned"></a>Kaç sonuç döndürüldüğünü yönetin

Varsayılan olarak, bir listeleme işlemi bir seferde en fazla 5000 sonuç döndürür. Daha küçük bir sonuç kümesi döndürmek için, döndürülecek sonuç sayfasının boyutu için sıfır dışında bir değer sağlayın.

Depolama hesabınızda 5000 ' den fazla kapsayıcı varsa veya listeleme işleminin depolama hesabındaki kapsayıcıların bir alt kümesini döndürdüğü şekilde bir sayfa boyutu belirttiyseniz, Azure Storage kapsayıcı listesini içeren bir *devamlılık belirteci* döndürür. Devamlılık belirteci, Azure depolama 'nın bir sonraki sonuç kümesini almak için kullanabileceğiniz donuk bir değerdir.

Kodunuzda, boş olup olmadığını (.NET V12 için) veya null olduğunu (.NET v11 ve önceki sürümler için) öğrenmek için devamlılık belirtecinin değerini denetleyin. Devamlılık belirteci null olduğunda, sonuç kümesi tamamlanır. Devamlılık belirteci null değilse, devamlılık belirteci null olana kadar, sonraki sonuç kümesini almak için devamlılık belirtecini geçirerek listeleme yöntemini yeniden çağırın.

### <a name="filter-results-with-a-prefix"></a>Sonuçları bir ön eke göre filtrele

Kapsayıcılar listesini filtrelemek için, parametre için bir dize belirtin `prefix` . Ön ek dizesi bir veya daha fazla karakter içerebilir. Daha sonra Azure Storage yalnızca adları bu önek ile başlayan kapsayıcıları döndürür.

### <a name="return-metadata"></a>Meta veri döndür

Sonuçlarla kapsayıcı meta verileri döndürmek için [Blobcontainernitelikler](/dotnet/api/azure.storage.blobs.models.blobcontainertraits) numaralandırması (.net V12 için) veya [Containerlistingdetails](/dotnet/api/microsoft.azure.storage.blob.containerlistingdetails) enum (.net v11 ve öncesi Için) için **meta veri** değerini belirtin. Azure depolama, her kapsayıcı tarafından döndürülen meta verileri içerir, bu nedenle kapsayıcı meta verilerini de almanız gerekmez.

## <a name="example-list-containers"></a>Örnek: liste kapsayıcıları

Aşağıdaki örnek, belirtilen bir önek ile başlayan bir depolama hesabındaki kapsayıcıları zaman uyumsuz olarak listeler. Örnek, belirtilen önekle başlayan kapsayıcıları listeler ve listeleme işlemine çağrı başına belirtilen sonuç sayısını döndürür. Daha sonra sonuçların sonraki segmentini almak için devamlılık belirtecini kullanır. Örnek ayrıca, sonuçlarla birlikte kapsayıcı meta verileri döndürür.

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Containers.cs" id="Snippet_ListContainers":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

```csharp
private static async Task ListContainersWithPrefixAsync(CloudBlobClient blobClient,
                                                        string prefix,
                                                        int? segmentSize)
{
    Console.WriteLine("List containers beginning with prefix {0}, plus container metadata:", prefix);

    BlobContinuationToken continuationToken = null;
    ContainerResultSegment resultSegment;

    try
    {
        do
        {
            // List containers beginning with the specified prefix,
            // returning segments of 5 results each.
            // Passing in null for the maxResults parameter returns the maximum number of results (up to 5000).
            // Requesting the container's metadata as part of the listing operation populates the metadata,
            // so it's not necessary to call FetchAttributes() to read the metadata.
            resultSegment = await blobClient.ListContainersSegmentedAsync(
                prefix, ContainerListingDetails.Metadata, segmentSize, continuationToken, null, null);

            // Enumerate the containers returned.
            foreach (var container in resultSegment.Results)
            {
                Console.WriteLine("\tContainer:" + container.Name);

                // Write the container's metadata keys and values.
                foreach (var metadataItem in container.Metadata)
                {
                    Console.WriteLine("\t\tMetadata key: " + metadataItem.Key);
                    Console.WriteLine("\t\tMetadata value: " + metadataItem.Value);
                }
            }

            // Get the continuation token.
            continuationToken = resultSegment.ContinuationToken;

        } while (continuationToken != null);

        Console.WriteLine();
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

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>Ayrıca bkz.

- [Kapsayıcıları Listele](/rest/api/storageservices/list-containers2)
- [Blob kaynaklarını numaralandırma](/rest/api/storageservices/enumerating-blob-resources)
