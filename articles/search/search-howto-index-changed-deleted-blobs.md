---
title: Değiştirilen ve silinen blob 'lar
titleSuffix: Azure Cognitive Search
description: Azure Blob depolamadan içeri aktaran bir ilk arama dizini oluşturulduktan sonra, sonraki dizin oluşturma yalnızca değiştirilen veya silinen Blobları alabilir. Bu makalede Ayrıntılar açıklanmaktadır.
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/29/2021
ms.openlocfilehash: 79d5583f8c9e562a0d21a91c210aa6259472661d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100383543"
---
# <a name="change-and-deletion-detection-in-blob-indexing-azure-cognitive-search"></a>Blob dizinlemesi 'nde değişiklik ve silme algılaması (Azure Bilişsel Arama)

İlk arama dizini oluşturulduktan sonra, sonraki Dizin Oluşturucu işlerinin yalnızca yeni ve değiştirilmiş belgeleri seçmesini isteyebilirsiniz. Azure Blob depolamadan kaynaklanan arama içerikleri için, dizin oluşturmayı tetiklemek üzere bir zamanlama kullandığınızda değişiklik algılama otomatik olarak gerçekleşir. Varsayılan olarak, hizmet blob 'un zaman damgasıyla belirlendiği şekilde yalnızca değiştirilen Blobları yeniden dizinler `LastModified` . Arama Dizin oluşturucular tarafından desteklenen diğer veri kaynaklarının aksine, Bloblar her zaman bir zaman damgasına sahiptir ve bu da bir değişiklik algılama ilkesini el ile ayarlama ihtiyacını ortadan kaldırır.

Değişiklik algılama belirli bir olsa da, silme algılaması değildir. Silinen belgeleri algılamak isterseniz, "geçici silme" yaklaşımını kullandığınızdan emin olun. Blob 'ları sağ silme, ilgili belgeler arama dizininden kaldırılmaz.

Geçici silme yaklaşımını uygulamak için iki yol vardır:

+ Yerel blob geçici silme (Önizleme), daha sonra açıklanan
+ [Özel meta verileri kullanarak geçici silme](#soft-delete-using-custom-metadata)

## <a name="native-blob-soft-delete-preview"></a>Yerel blob geçici silme (Önizleme)

Bu silme algılama yaklaşımı için Bilişsel Arama, Blobların geçici olarak silinen bir duruma geçirilip geçirilmediğini saptamak için Azure Blob depolama 'daki [Yerel blob geçici silme](../storage/blobs/soft-delete-blob-overview.md) özelliğine bağlıdır. Bu durumda blob 'lar algılandığında, bir arama Dizin Oluşturucusu bu bilgileri kullanarak karşılık gelen belgeyi dizinden kaldırır.

> [!IMPORTANT]
> Yerel blob geçici silme desteği önizlemededir. Önizleme işlevselliği, bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yükleri için önerilmez. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). [REST API sürüm 2020-06-30-önizleme](./search-api-preview.md) bu özelliği sağlar. Şu anda portal veya .NET SDK desteği yok.

### <a name="prerequisites"></a>Önkoşullar

+ [Bloblar için geçici silmeyi etkinleştirin](../storage/blobs/soft-delete-blob-enable.md).
+ Blob 'lar bir Azure Blob depolama kapsayıcısında olmalıdır. Bilişsel Arama yerel blob geçici silme ilkesi, Azure Data Lake Storage 2. Blobları için desteklenmez.
+ Dizininizdeki belgelerin belge anahtarlarının bir blob özelliği ya da blob meta verileri olacak şekilde eşlenmesi gerekir.
+ `api-version=2020-06-30-Preview`Geçici silme desteğini yapılandırmak için önizleme REST API () kullanmanız gerekir.

### <a name="how-to-configure-deletion-detection-using-native-soft-delete"></a>Yerel geçici silme kullanarak silme algılamasını yapılandırma

1. BLOB depolama alanında, geçici silme etkinleştirilirken, bekletme ilkesini Dizin Oluşturucu aralığı zamanlamaınızın çok daha yüksek bir değerine ayarlayın. Bu şekilde, Dizin oluşturucuyu çalıştıran bir sorun varsa veya dizinlemek için çok sayıda belgeniz varsa, dizin oluşturucunun, geçici olarak silinen Blobları işlemesi çok fazla zaman vardır. Azure Bilişsel Arama Dizin oluşturucular, bir belgeyi, geçici olarak silinen bir durumdayken blobu işliyorsa siler.

1. Bilişsel Arama ' de, veri kaynağında yerel bir blob geçici silme algılama ilkesi ayarlayın. Aşağıda bir örnek gösterilmiştir. Bu özellik önizlemede olduğundan, önizleme REST API kullanmanız gerekir.

    ```http
    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : null },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.NativeBlobSoftDeleteDeletionDetectionPolicy"
        }
    }
    ```

1. [Dizin oluşturucuyu çalıştırın](/rest/api/searchservice/run-indexer) veya dizin oluşturucuyu [bir zamanlamaya göre](search-howto-schedule-indexers.md)çalışacak şekilde ayarlayın. Dizin Oluşturucu çalıştırıldığında ve geçici silme durumuna sahip bir blobu işlediğinde, karşılık gelen arama belgesi dizinden kaldırılır.

### <a name="reindexing-undeleted-blobs-using-native-soft-delete-policies"></a>Silinmeyen Blobların yeniden oluşturulması (yerel geçici silme ilkelerini kullanarak)

Blob depolamada geçici olarak silinen bir blobu geri yüklerseniz, Dizin Oluşturucu her zaman yeniden dizin haline gelir. Bunun nedeni, dizin oluşturucunun, `LastModified` Dizin oluşturmanın gerekli olup olmadığını belirlemede blob zaman damgasını kullanması. Geçici olarak silinen bir blob silindiğinde, `LastModified` zaman damgası güncellenmez. bu nedenle, Dizin Oluşturucu daha son zaman damgalarıyla blob 'ları zaten işdiyse `LastModified` , silinmeyen blobu yeniden oluşturmaz. 

Silinmeyen bir Blobun yeniden dizinlendiğinden emin olmak için Blobun `LastModified` zaman damgasını güncelleştirmeniz gerekir. Bunu yapmanın bir yolu, söz konusu Blobun meta verilerinin yeniden kaydedilerek yapılır. Meta verileri değiştirmeniz gerekmez, ancak meta verilerin yeniden kaydedilmesi Blobun `LastModified` zaman damgasını, dizin oluşturucunun onu seçmesini bilmesi için güncelleştirecektir.

## <a name="soft-delete-using-custom-metadata"></a>Özel meta verileri kullanarak geçici silme

Bu yöntem bir blob 'un meta verilerini kullanarak bir arama belgesinin dizinden kaldırılıp kaldırılmayacağını belirlemektir. Bu yöntem iki ayrı eylem gerektirir, arama belgesini dizinden silebilir ve ardından Azure Storage 'da blob silme işlemi gelir.

Hem blob depolamada hem de Bilişsel Arama izlemeniz gereken adımlar vardır, ancak başka bir özellik bağımlılığı yoktur. Bu özellik, genel olarak kullanılabilir API 'lerde desteklenir.

1. Bilişsel Arama Azure 'da mantıksal olarak silindiğini göstermek için blob 'a özel bir meta veri anahtar-değer çifti ekleyin.

1. Veri kaynağında geçici silme sütunu algılama ilkesi yapılandırın. Örneğin, aşağıdaki ilke, bir blob 'un şu değere sahip bir meta veri özelliği varsa silineceğini kabul eder `IsDeleted` `true` :

    ```http
    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : null },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName" : "IsDeleted",
            "softDeleteMarkerValue" : "true"
        }
    }
    ```

1. Dizin Oluşturucu blobu işledikten ve belgeyi dizinden sildikten sonra, blob 'u Azure Blob depolama alanında silebilirsiniz.

### <a name="reindexing-undeleted-blobs-using-custom-metadata"></a>Silinmemiş Blobların yeniden oluşturulması (özel meta veriler kullanılarak)

Bir Dizin Oluşturucu silinen bir blobu işleyerek ve karşılık gelen arama belgesini dizinden kaldırdığında, daha sonra Blobun `LastModified` zaman damgası son dizin oluşturucudan daha eskiyse, bu blobu geri görmez.

Bu belgeyi yeniden eklemek istiyorsanız, `"softDeleteMarkerValue" : "false"` o blob için öğesini değiştirin ve Dizin oluşturucuyu yeniden çalıştırın.

## <a name="next-steps"></a>Sonraki adımlar

+ [Azure Bilişsel Arama'daki Dizin Oluşturucular](search-indexer-overview.md)
+ [Blob Dizin oluşturucuyu yapılandırma](search-howto-indexing-azure-blob-storage.md)
+ [Blob dizinlemeye genel bakış](search-blob-storage-integration.md)