---
title: Dizin oluşturucu oluşturma
titleSuffix: Azure Cognitive Search
description: Veri kaynağını ve hedeflerini öğrenmek için Dizin oluşturucudaki özellikleri ayarlayın. Çalışma zamanı davranışlarını değiştirecek parametreleri ayarlayabilirsiniz.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/28/2021
ms.openlocfilehash: 596eca0d73ffc4a590fae9b346658a2c31a1d68c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101676486"
---
# <a name="creating-indexers-in-azure-cognitive-search"></a>Azure Bilişsel Arama Dizin oluşturucular oluşturma

Arama Dizin Oluşturucusu, bir dış veri kaynağından belge ve içerik aktarmaya yönelik otomatik bir iş akışını, arama hizmetinizde bir arama dizinine sağlar. Başlangıçta tasarlandığı gibi, bir Azure veri kaynağından metin ve meta verileri ayıklar, belgeleri JSON 'a serileştirir ve sonuç belgelerini dizin oluşturma için bir arama altyapısına geçirir. Bu, derin içerik işleme için [AI zenginleştirme](cognitive-search-concept-intro.md) desteği sağlamak üzere genişletilmiştir. 

Dizin oluşturucular kullanmak, yazmanız gereken kodun miktarını ve karmaşıklığını önemli ölçüde azaltır. Bu makalede, kaynak özel Dizin oluşturucular ve [becerileri](cognitive-search-working-with-skillsets.md)ile daha gelişmiş çalışma için hazırlık olarak bir Dizin Oluşturucu oluşturma mekanizması ele alınmaktadır.

## <a name="whats-an-indexer-definition"></a>Dizin Oluşturucu tanımı nedir?

Dizin oluşturucular, kaynak alanlardan dizin alanlarına alfasayısal içerik çeken metin tabanlı dizin oluşturma veya yapı için değişmeyen metni çözümleyen ya da metin ve bilgilerin görüntülerini analiz eden ve bu içeriği bir dizine ekleyen AI tabanlı işleme için kullanılır. Aşağıdaki dizin tanımları, her iki senaryo için de oluşturabileceğiniz şeydir.

### <a name="indexers-for-text-content"></a>Metin içeriği için Dizin oluşturucular

Bir dizin oluşturucunun özgün amacı, bir veri kaynağındaki alanlardan metin ve sayısal içerik bağlama ve okuma, bu içeriği JSON belgeleri olarak serileştirme ve bu belgeleri dizin oluşturma için arama altyapısına bırakma için bir mekanizma sağlayarak bir dizin yükleme işleminin karmaşık bir sürecini basitleştirmektir. Bu hala birincil bir kullanım örneğidir ve bu işlem için aşağıdaki örnekte tanımlanan özelliklerle bir dizin oluşturucu oluşturmanız gerekir.

```json
{
  "name": (required) String that uniquely identifies the indexer,
  "dataSourceName": (required) String indicated which existing data source to use,
  "targetIndexName": (required) String,
  "parameters": {
    "batchSize": null,
    "maxFailedItems": null,
    "maxFailedItemsPerBatch": null
  },
  "fieldMappings": [ optional unless there are field discrepancies that need resolution]
}
```

**`name`**, **`dataSourceName`** , Ve **`targetIndexName`** özellikleri gereklidir ve Dizin oluşturucuyu çalıştırmadan önce, hem veri kaynağı hem de dizin zaten hizmette var olmalıdır. 

**`parameters`** Özelliği, tüm işin başarısız olması için kaç hatanın kabul edileceği gibi çalışma zamanı davranışlarını değiştirir. Parametreler, kaynağa özgü davranışları da belirtmektir. Örneğin, kaynak BLOB depolama ise, dosya uzantılarına filtre uygulayan bir parametre ayarlayabilirsiniz: `"parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }` .

**`field mappings`** Özelliği, bu alanlar ada veya türe göre farklıysa, kaynak-hedefe alanları açıkça eşlemek için kullanılır. Diğer Özellikler (gösterilmez), [bir zamanlama belirtmek](search-howto-schedule-indexers.md), Dizin oluşturucuyu devre dışı durumda oluşturmak veya bekleyen verilerin ek şifrelenmesi için bir [şifreleme anahtarı](search-security-manage-encryption-keys.md) belirtmek için kullanılır.

### <a name="indexers-for-ai-indexing"></a>AI dizin oluşturma için Dizin oluşturucular

Dizin oluşturucular bir arama hizmetinin giden istek yaptığı mekanizmaya sahip olduğundan, Dizin oluşturucular AI zenginleştirmelerinin desteklenmesi, bu kullanım örneğini uygulamak için altyapı ve nesne ekleme için genişletilmiştir.

Yukarıdaki özelliklerin ve parametrelerin tümü, AI zenginleştirme işlemini gerçekleştiren Dizin oluşturucular için geçerlidir. Aşağıdaki özellikler AI zenginleştirme için özeldir: **`skillSets`** , **`outputFieldMappings`** , **`cache`** (yalnızca önizleme ve REST). 

```json
{
  "name": (required) String that uniquely identifies the indexer,
  "dataSourceName": (required) String, name of an existing data source,
  "targetIndexName": (required) String, name of an existing index,
  "skillsetName" : (required for AI enrichment) String, name of an existing skillset,
  "cache":  {
    "storageConnectionString" : (required for caching AI enriched content) Connection string to a blob container,
    "enableReprocessing": true
    },
  "parameters": {
    "batchSize": null,
    "maxFailedItems": null,
    "maxFailedItemsPerBatch": null
  },
  "fieldMappings": [],
  "outputFieldMappings" : (required for AI enrichment) { ... },
}
```

AI zenginleştirme, bu makalenin kapsamı dışındadır. Daha fazla bilgi için şu makalelerle başlayın: [AI zenginleştirme](cognitive-search-concept-intro.md), [Azure bilişsel arama 'de becerileri](cognitive-search-working-with-skillsets.md)ve [Beceri (REST)](/rest/api/searchservice/create-skillset).

## <a name="choose-an-indexer-client-and-create-the-indexer"></a>Bir Dizin Oluşturucu istemcisi seçin ve Dizin oluşturucuyu oluşturun

Uzak arama hizmetinde bir dizin oluşturucu oluşturmaya hazırsanız, Azure portal veya Postman gibi bir araç biçiminde bir arama istemcisine veya bir Dizin Oluşturucu istemcisini örnekleyen koda ihtiyacınız olacaktır. Erken geliştirme ve kavram kanıtı testi için Azure portal veya REST API 'Leri öneririz.

### <a name="permissions"></a>İzinler

Durum ve tanımlar için istekleri al da dahil olmak üzere dizin oluşturucularla ilgili tüm işlemler, istekte bir [yönetici API anahtarı](search-security-api-keys.md) gerektirir.

### <a name="limits"></a>Sınırlar

Tüm [hizmet katmanları](search-limits-quotas-capacity.md#indexer-limits) , oluşturabileceğiniz nesne sayısını sınırlar. Ücretsiz katmanda deneme yapıyorsanız, her bir türden 3 nesneniz ve Dizin Oluşturucu işlemenin 2 dakikası (beceri işleme dahil değildir) olabilir.

### <a name="use-azure-portal-to-create-an-indexer"></a>Dizin Oluşturucu oluşturmak için Azure portal kullanma

Portal, Dizin Oluşturucu tanımını belirtmek için alanlar sağlayan bir Dizin Oluşturucu: [**veri Içeri aktarma Sihirbazı**](search-import-data-portal.md) ve **Yeni Dizin Oluşturucu** oluşturmak için iki seçenek sunar. Sihirbaz, gerekli tüm öğeleri oluşturduğundan benzersizdir. Diğer yaklaşımlar, önceden tanımlanmış bir veri kaynağı ve Dizin gerektirir.

Aşağıdaki ekran görüntüsünde bu özellikleri portalda nerede bulabileceğiniz gösterilmektedir. 

  :::image type="content" source="media/search-howto-create-indexers/portal-indexer-client.png" alt-text="oteller Dizin Oluşturucusu" border="true":::

### <a name="use-a-rest-client"></a>REST istemcisi kullanma

Hem Postman hem de Visual Studio Code (Azure Bilişsel Arama Uzantısı ile) Dizin Oluşturucu istemcisi olarak çalışabilir. İki aracı kullanarak, arama hizmetinize bağlanabilir ve [Dizin oluşturma (REST)](/rest/api/searchservice/create-indexer) istekleri gönderebilirsiniz. Nesneleri oluşturmak için REST istemcilerini gösteren çok sayıda öğretici ve örnek vardır. 

Her istemci hakkında bilgi edinmek için şu makalelerden birini başlatın:

+ [REST ve Postman kullanarak arama dizini oluşturma](search-get-started-rest.md)
+ [Visual Studio Code ve Azure Bilişsel Arama kullanmaya başlama](search-get-started-vs-code.md)

Dizin Oluşturucu isteklerini formüllemekte yardımcı olması için [Dizin Oluşturucu işlemlerine (REST)](/rest/api/searchservice/Indexer-operations) bakın.

### <a name="use-an-sdk"></a>SDK kullanma

Bilişsel Arama için Azure SDK 'Ları, genel olarak kullanılabilen özellikleri uygular. Bu nedenle, Dizin oluşturucudan ilişkili nesneler oluşturmak için SDK 'Lardan herhangi birini kullanabilirsiniz. Hepsi, becerileri dahil olmak üzere Dizin oluşturucular ve ilgili nesneler oluşturma yöntemlerine sahip bir **SearchIndexerClient** sağlar.

| Azure SDK | İstemci | Örnekler |
|-----------|--------|----------|
| .NET | [SearchIndexerClient](/dotnet/api/azure.search.documents.indexes.searchindexerclient) | [Dotnethowtoındexers](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToIndexers) |
| Java | [SearchIndexerClient](/java/api/com.azure.search.documents.indexes.searchindexerclient) | [CreateIndexerExample. Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexerExample.java) |
| JavaScript | [SearchIndexerClient](/javascript/api/@azure/search-documents/searchindexerclient) | [Dizin Oluşturucular](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/indexers) |
| Python | [SearchIndexerClient](/python/api/azure-search-documents/azure.search.documents.indexes.searchindexerclient) | [sample_indexers_operations. Kopyala](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_indexers_operations.py) |

## <a name="run-the-indexer"></a>Dizin oluşturucuyu çalıştırma

Bir Dizin Oluşturucu, hizmette Dizin oluşturucuyu oluşturduğunuzda otomatik olarak çalışır. Bu, veri kaynağı bağlantı hataları, alan eşleme sorunları veya beceri sorunları olup olmadığını bulmak için gereken gerçeği. 

Dizin oluşturucuyu çalıştırmanın birkaç yolu vardır:

+ Tanımı eklemek veya değiştirmek için [Create Indexer](/rest/api/searchservice/create-indexer) veya [Update Indexer](/rest/api/searchservice/update-indexer) için bir http isteği gönderin ve Dizin oluşturucuyu çalıştırın.

+ Bir Dizin Oluşturucu için, tanımda değişiklik yapmadan bir Dizin Oluşturucu [çalıştırmak](/rest/api/searchservice/run-indexer) IÇIN bir http isteği gönderin.

+ Oluşturma, güncelleştirme veya çalıştırma için SearchIndexerClient yöntemlerini çağıran bir program çalıştırın.

> [!NOTE]
> Oluşturma sonrasında bir dizin oluşturucuyu hemen çalıştırmayı önlemek için **`disabled=true`** Dizin Oluşturucu tanımına dahil edin.

Alternatif olarak, Dizin oluşturucuyu düzenli aralıklarla işlemeyi çağırmak için [bir zamanlamaya](search-howto-schedule-indexers.md) koyun. 

Zamanlanan işleme genellikle Değiştirilen içeriğin artımlı dizin oluşturma gereksinimi ile saatle çakışan. Değişiklik algılama mantığı, kaynak platformlarda yerleşik olarak bulunan bir özelliktir. Blob kapsayıcısındaki değişiklikler Dizin Oluşturucu tarafından otomatik olarak algılanır. Diğer veri kaynaklarında değişiklik algılamayı kullanma hakkında rehberlik için, belirli veri kaynakları için Dizin Oluşturucu belgelerine bakın:

+ [Azure SQL veritabanı](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Azure Data Lake Storage 2. Nesil](search-howto-index-azure-data-lake-storage.md)
+ [Azure Tablo depolama](search-howto-indexing-azure-tables.md)
+ [Azure Cosmos DB](search-howto-index-cosmosdb.md)

## <a name="change-detection-and-indexer-state"></a>Algılama ve Dizin Oluşturucu durumunu değiştir

Dizin oluşturucular temel verilerdeki değişiklikleri algılayabilir ve yalnızca her bir Dizin Oluşturucu çalıştırmasında yeni veya güncelleştirilmiş belgeleri işleyebilir. Örneğin, Dizin Oluşturucu durumu bir çalıştırmanın işlenen belgelerle başarılı olduğunu söyyorsa `0/0` , dizin oluşturucunun temel alınan veri kaynağında yeni veya değiştirilmiş satır ya da blob bulmadığını gösterir.

Bir dizin oluşturucunun değişiklik algılamayı nasıl desteklediği, veri kaynağına göre farklılık gösterir:

+ Azure Blob depolama, Azure Tablo depolama ve bir tarih ve saat ile her blob veya satır güncelleştirmesi Azure Data Lake Storage 2. damgası. Çeşitli Dizin oluşturucular bu bilgileri, dizinde güncelleştirilecek belgeleri belirlemede kullanır. Yerleşik değişiklik algılama, bir dizin oluşturucunun, sizin bölüminizdeki ek yapılandırma gerekmeden yeni ve güncelleştirilmiş belgeleri tanıyabileceği anlamına gelir.

+ Azure SQL ve Cosmos DB platformlarındaki değişiklik algılama özelliklerini sağlar. Veri kaynağı tanımınızda değişiklik algılama ilkesini belirtebilirsiniz.

Büyük dizin oluşturma yükleri için bir Dizin Oluşturucu, iç "yüksek su işareti" ile işlenen son belgeyi de izler. İşaretleyici, API 'de hiçbir yerde gösterilmez, ancak dahili olarak Dizin Oluşturucu durdurulma konumunu izler. Dizin oluşturma devam ederse, zamanlanmış bir çalıştırma veya isteğe bağlı bir çağrı aracılığıyla Dizin Oluşturucu, kaldığı yerden seçim yapabilmesi için yüksek su işaretine başvurur.

Tam olarak yeniden dizin eklemek için yüksek su işaretini temizlemeniz gerekiyorsa, [sıfırlama dizin oluşturucuyu](/rest/api/searchservice/reset-indexer)kullanabilirsiniz. Daha seçmeli yeniden dizin oluşturmak için, [becerileri Sıfırla](/rest/api/searchservice/preview-api/reset-skills) veya [belgeleri Sıfırla](/rest/api/searchservice/preview-api/reset-documents)' yı kullanın. API 'Leri sıfırlama ile iç durumu temizleyebilir ve [artımlı zenginleştirme](search-howto-incremental-index.md)seçeneğini etkinleştirdiyseniz önbelleği de temizleyebilirsiniz. Her sıfırlama seçeneğinin daha fazla arka planı ve karşılaştırması için bkz. [Dizinleyicileri, becerileri ve belgeleri çalıştırma veya sıfırlama](search-howto-run-reset-indexers.md).

## <a name="know-your-data"></a>Verilerinizi öğrenin

Dizin oluşturucular, her satırın dizinde tam veya kısmi arama belgesi haline geldiği tablolu bir satır kümesi bekler. Genellikle, satır ve sonuçta elde edilen arama belgesi arasında bire bir yazışmalar vardır; burada satır kümesindeki tüm alanlar her belgeyi tamamen doldurur. Ancak, dizin oluşturmak için birden çok Dizin Oluşturucu veya yaklaşım kullanıyorsanız, örneğin, bir belgenin yalnızca bir kısmını oluşturmak için Dizin oluşturucular kullanabilirsiniz. 

İlişkisel verileri bir satır kümesine düzleştirmek için, bir SQL görünümü oluşturmanız veya üst ve alt kayıtları aynı satırda döndüren bir sorgu oluşturmanız gerekir. Örneğin, yerleşik oteller örnek veri kümesi, ilişkili bir tablodaki Oda kayıtlarına bağlı olan 50 kayda (her otel için bir tane) sahip bir SQL veritabanıdır. Ortak verileri bir satır kümesine düzleyen sorgu, her otel kaydındaki tüm oda bilgilerini JSON belgelerindeki tüm oda bilgilerini gömer. Katıştırılmış Oda bilgileri, **for JSON Auto** yan tümcesini kullanan bir sorgu tarafından oluşturulur. Bu teknik hakkında daha fazla bilgi için [gömülü JSON döndüren bir sorgu tanımlayın](index-sql-relational-data.md#define-a-query-that-returns-embedded-json). Bu yalnızca bir örnektir; aynı etkiyi oluşturacak diğer yaklaşımları de bulabilirsiniz.

Düzleştirilmiş verilere ek olarak, yalnızca aranabilir verileri çekmek önemlidir. Aranabilir veriler alfasayısal. Bilişsel Arama, tüm biçimdeki ikili verileri arayamıyor, ancak aranabilir içerik oluşturmak için görüntü dosyalarının metin açıklamalarını ayıklayabilir ve çıkarsmasına (bkz. [AI zenginleştirme](cognitive-search-concept-intro.md)) izin verebilir. Benzer şekilde, AI zenginleştirme kullanarak büyük metin, yapıyı veya ilgili bilgileri bulmak için doğal dil modelleriyle analiz edilebilir ve bu da bir arama belgesine ekleyebileceğiniz yeni içerik oluşturabilir.

Dizin oluşturucular veri sorunlarını gidermezse, diğer veri temizleme veya işleme gibi diğer biçimler de gerekebilir. Daha fazla bilgi için [Azure veritabanı ürününüzün](../index.yml?product=databases)ürün belgelerine başvurmalısınız.

## <a name="know-your-index"></a>Dizininizi öğrenin

Dizin oluşturucuların, arama belgelerini dizin oluşturma için arama motoruna geçiri hatırlayın. Dizinleyicilerin yürütme davranışını tespit eden özellikleri olduğu gibi, dizin şemasının dizelerin dizine eklenmesini sağlayan özellikler vardır (yalnızca dizeler çözümlenir ve simgeleştirilir). Çözümleyici atamalarına bağlı olarak, dizinli dizeler, geçirilen verilerden farklı olabilir. Çözümleyicileri [metin (REST) kullanarak çözümleyicilerin](/rest/api/searchservice/test-analyzer)etkilerini değerlendirebilirsiniz. Çözümleyiciler hakkında daha fazla bilgi için bkz. [metin işleme Için çözümleyiciler](search-analyzers.md).

Dizin oluşturucularının bir dizinle nasıl etkileşime gireceğini gösteren bir Dizin Oluşturucu yalnızca alan adlarını ve türlerini denetler. Dizindeki ilgili arama alanı için gelen içeriğin doğru olmasını sağlayan bir doğrulama adımı yoktur. Doğrulama adımı olarak, tüm belgeleri veya seçili alanları döndüren doldurulmuş dizin üzerinde sorgular çalıştırabilirsiniz. Bir dizinin içeriğini sorgulama hakkında daha fazla bilgi için bkz. [temel sorgu oluşturma](search-query-create.md).

## <a name="next-steps"></a>Sonraki adımlar

+ [Zamanlama dizin oluşturucuları](search-howto-schedule-indexers.md)
+ [Alan eşlemelerini tanımlama](search-indexer-field-mappings.md)
+ [Dizin Oluşturucu durumunu izleme](search-howto-monitor-indexers.md)
+ [Yönetilen kimlikleri kullanarak bağlanma](search-howto-managed-identities-data-sources.md)