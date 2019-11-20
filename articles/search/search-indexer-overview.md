---
title: İçeri aktarma sırasında verileri gezinmek için Dizin oluşturucular
titleSuffix: Azure Cognitive Search
description: Aranabilir verileri ayıklamak ve bir Azure Bilişsel Arama dizinini doldurmak için Azure SQL veritabanı, Azure Cosmos DB veya Azure Storage 'ı gezin.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 1fd667e14501355f2567712d13b10dd7fbc7ee19
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112696"
---
# <a name="indexers-in-azure-cognitive-search"></a>Azure Bilişsel Arama Dizin oluşturucular

Azure Bilişsel Arama 'de bir *Dizin Oluşturucu* , bir dış Azure veri kaynağından aranabilir verileri ve meta verileri çıkaran ve dizin ile veri kaynağınız arasındaki alandan alana eşlemeleri temel alan bir gezgin. Bu yaklaşım bazen ' çekme modeli ' olarak adlandırılır, çünkü hizmet verileri bir dizine ekleyen herhangi bir kod yazmak zorunda kalmadan içine veri çeker.

Dizin oluşturucular, Azure, Cosmos DB, Azure Tablo depolama ve BLOB depolama alanı SQL Server bireysel dizin oluşturucular ile veri kaynağı türlerini veya platformları temel alır. BLOB depolama Dizin oluşturucular, blob içerik türlerine özgü ek özelliklere sahiptir.

Bir dizin oluşturucusunu yalnızca veri alımı amacıyla kullanabilir veya dizininize alanların yalnızca bazılarını yüklemek için bir dizin oluşturucu kullanımını içeren bir teknikler birleşimini kullanabilirsiniz.

Dizin oluşturucularını isteğe bağlı olarak veya her beş dakikada bir çalışan yinelenen bir veri yenileme zamanlaması üzerinde çalıştırabilirsiniz. Daha sık güncellemeler, hem Azure Bilişsel Arama hem de dış veri kaynağınızdaki verileri eşzamanlı olarak güncelleştiren bir anında iletme modeli gerektirir.

## <a name="approaches-for-creating-and-managing-indexers"></a>Dizin oluşturucular oluşturma ve yönetme yaklaşımları

Dizin oluşturucuları aşağıdaki yaklaşımlarla oluşturabilir ve yönetebilirsiniz:

* [Portal > verileri Içeri aktarma Sihirbazı](search-import-data-portal.md)
* [Hizmet REST API'si](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations)
* [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations)

Başlangıçta, yeni bir dizin oluşturucu bir önizleme özelliği olarak duyurulur. Önizleme özellikleri API'lere (REST ve .NET) eklenir ve ardından genel kullanıma açık hale geldiklerinde portala entegre edilir. Yeni bir dizin oluşturucuyu değerlendiriyorsanız kod yazmayı planlamanız gerekir.

## <a name="permissions"></a>İzinler

Durum ve tanımlar için istekleri al da dahil olmak üzere dizin oluşturucularla ilgili tüm işlemler, [yönetici API anahtarı](search-security-api-keys.md)gerektirir. 

<a name="supported-data-sources"></a>

## <a name="supported-data-sources"></a>Desteklenen veri kaynakları

Dizin oluşturucular Azure 'da veri depolarında gezinme.

* [Azure Blob Depolama](search-howto-indexing-azure-blob-storage.md)
* [Azure Tablo Depolama](search-howto-indexing-azure-tables.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Azure SQL Veritabanı](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure Sanal Makinelerde SQL Server](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)
* [Azure 'da SQL yönetilen örnekler](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md)

## <a name="basic-configuration-steps"></a>Temel yapılandırma adımları
Dizin oluşturucular veri kaynağına özgü özellikler sunabilir. Bu bakımdan, dizin oluşturucu veya veri kaynağı yapılandırmasının bazı boyutları dizin oluşturucu türüne göre farklılık gösterir. Bununla birlikte, tüm dizin oluşturucuların temel birleşimi ve gereksinimleri aynıdır. Tüm dizin oluşturucularda ortak olan adımlar aşağıda ele alınmıştır.

### <a name="step-1-create-a-data-source"></a>1\. Adım: Veri kaynağı oluşturma
Bir dizin *Oluşturucu veri kaynağı nesnesinden veri* kaynağı bağlantısı edinir. Veri kaynağı tanımı, bir bağlantı dizesi ve muhtemelen kimlik bilgileri sağlar. Kaynağı oluşturmak için [Create Datasource](https://docs.microsoft.com/rest/api/searchservice/create-data-source) REST API veya [DataSource sınıfını](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource) çağırın.

Veri kaynakları, bunları kullanan dizin oluşturuculardan bağımsız olarak yapılandırılır ve yönetilir. Bu da bir veri kaynağının, bir seferde birden çok dizin yüklemek amacıyla birden çok dizin oluşturucu tarafından kullanılabileceği anlamına gelir.

### <a name="step-2-create-an-index"></a>2\. Adım: Dizin oluşturma
Dizin oluşturucu veri alımıyla ilgili bazı görevleri otomatikleştirir, ancak dizin oluşturma genellikle bu görevlerden biri değildir. Bir önkoşul olarak dış veri kaynağınızdaki alanlarla eşleşen alanlara sahip önceden tanımlı bir dizininiz olmalıdır. Alanların ad ve veri türüyle eşleşmesi gerekir. Dizini yapılandırma hakkında daha fazla bilgi için bkz. [Dizin oluşturma (Azure Bilişsel Arama REST API)](https://docs.microsoft.com/rest/api/searchservice/Create-Index) veya [Dizin sınıfı](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index). Alan ilişkilendirmelerinde yardım için bkz. [Azure bilişsel arama Dizin oluşturucularda alan eşlemeleri](search-indexer-field-mappings.md).

> [!Tip]
> Dizin oluşturucular sizin için dizin oluşturamasa da, portaldaki **Verileri içeri aktarma** sihirbazı bu işlem için size yardımcı olabilir. Çoğu durumda, sihirbaz, kaynaktaki mevcut meta verilerden dizin şeması çıkarsayarak, sihirbaz etkin olduğunda satır içinde düzenleyebileceğiniz geçici bir dizin şeması sunar. Hizmet için sihirbaz oluşturulduğunda, portalda yapılabilecek ayrıntılı düzenlemeler, genellikle yeni alanlar eklemeyle sınırlıdır. Sihirbaz dizin oluşturmak için uygun olsa da, düzenlemek için uygun değildir. Uygulama yaparak öğrenmek için, [portal kılavuzundaki](search-get-started-portal.md) adımları izleyin.

### <a name="step-3-create-and-schedule-the-indexer"></a>3\. Adım: Dizin oluşturucuyu oluşturma ve zamanlama
Dizin Oluşturucu tanımı, veri alımı ile ilgili tüm öğeleri birlikte getiren bir yapıdır. Gerekli öğeler bir veri kaynağı ve dizin içerir. İsteğe bağlı öğeler bir zamanlama ve alan eşlemeleri içerir. Alan eşleme yalnızca kaynak alanları ve dizin alanları açıkça karşılık geliyorsa isteğe bağlıdır. Bir dizin oluşturucu, aynı abonelikten olduğu sürece başka bir hizmetteki bir veri kaynağına başvurabilir. Dizin oluşturucuyu yapılandırma hakkında daha fazla bilgi için bkz. [Dizin Oluşturucu oluşturma (Azure Bilişsel Arama REST API)](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer).

<a id="RunIndexer"></a>

## <a name="run-indexers-on-demand"></a>Dizin oluşturucuyu isteğe bağlı olarak çalıştır

Dizin oluşturma zamanlaması yaygın olsa da, [Çalıştır komutu](https://docs.microsoft.com/rest/api/searchservice/run-indexer)kullanılarak bir Dizin Oluşturucu da isteğe bağlı olarak çağrılabilir:

    POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=2019-05-06
    api-key: [Search service admin key]

> [!NOTE]
> Run API 'SI başarıyla döndüğünde, Dizin Oluşturucu çağrısı zamanlandı, ancak gerçek işlem zaman uyumsuz olarak gerçekleşir. 

Dizin Oluşturucu durumunu portalda veya Dizin Oluşturucu durumunu Al API 'SI aracılığıyla izleyebilirsiniz. 

<a name="GetIndexerStatus"></a>

## <a name="get-indexer-status"></a>Dizin Oluşturucu durumunu al

Bir dizin oluşturucunun durum ve yürütme geçmişini [Get Indexer durum komutuyla](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status)alabilirsiniz:


    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2019-05-06
    api-key: [Search service admin key]

Yanıt genel Dizin Oluşturucu durumunu, son (veya sürmekte olan) Dizin Oluşturucu çağrısını ve son Dizin Oluşturucu etkinleştirmeleri geçmişini içerir.

    {
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2018-11-26T03:37:18.853Z",
            "endTime":"2018-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
         },
        "executionHistory":[ {
            "status":"success",
             "errorMessage":null,
            "startTime":"2018-11-26T03:37:18.853Z",
            "endTime":"2018-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        }]
    }

Yürütme geçmişi, geriye doğru kronolojik düzende sıralanan en son tamamlanan yürütme sayısı olan 50 ' i içerir (Bu nedenle, en son yürütme yanıtta ilk kez gelir).

## <a name="next-steps"></a>Sonraki adımlar
Artık temel fikri anladığınıza göre, atmanız gereken bir sonraki adım her bir veri kaynağı türüne özgü gereksinimleri ve görevleri incelemektir.

* [Bir Azure sanal makinesinde Azure SQL Veritabanı veya SQL Server](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Azure Blob Depolama](search-howto-indexing-azure-blob-storage.md)
* [Azure Tablo Depolama](search-howto-indexing-azure-tables.md)
* [Azure Bilişsel Arama blob Dizin oluşturucuyu kullanarak CSV bloblarını dizine ekleme](search-howto-index-csv-blobs.md)
* [JSON bloblarını Azure Bilişsel Arama blob Indexer ile dizinleme](search-howto-index-json-blobs.md)
