---
title: Azure Cosmos DB SQL API SQL sorgusu ölçümleri
description: İzleme ve Azure Cosmos DB isteklerinin SQL sorgu performansı hata ayıklama hakkında bilgi edinin.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: sngun
ms.openlocfilehash: ae1773ec1d470b9cff2efb00c200427b7b4c2fb4
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2019
ms.locfileid: "69614828"
---
# <a name="tuning-query-performance-with-azure-cosmos-db"></a>Azure Cosmos DB ile sorgu performansını ayarlama

Azure Cosmos DB sağlar bir [veri sorgulama için SQL API](how-to-sql-query.md), şema veya ikincil dizinler gerektirmeden. Bu makalede, geliştiriciler için aşağıdaki bilgileri sağlar:

* Azure Cosmos DB SQL sorgusu yürütme birlikte nasıl çalıştığı hakkında üst düzey ayrıntıları
* Sorgu istek ve yanıt üst bilgileri ve istemci SDK'sı seçenekleri ile ilgili ayrıntılar
* İpuçları ve sorgu performansı için en iyi yöntemler
* SQL yürütme istatistikleri sorgu performansı hata ayıklamak için nasıl örnekleri

## <a name="about-sql-query-execution"></a>SQL sorgusu yürütme hakkında

Azure Cosmos DB'de hiçbir işlem genişleyebileceği kapsayıcılar, verileri depolamak [depolama boyutu veya istek işleme](partition-data.md). Azure Cosmos DB veri veri büyümesiyle veya sağlanan aktarım hızı artırmak için arka planda altında fiziksel bölümler arasında sorunsuz bir şekilde ölçeklendirir. REST API veya desteklenen birini kullanarak herhangi bir kapsayıcıya SQL sorgu iletebilirsiniz [SQL SDK'ları](sql-api-sdk-dotnet.md).

Kısa bir genel bölümleme: verileri fiziksel bölümler arasında nasıl bölünür belirler "Şehir" gibi bir bölüm anahtarı tanımlayın. Tek bölüm anahtarına ait verileri (örneğin, "city" "Seattle" ==) bir fiziksel bölüm içinde depolanır ancak genellikle tek bir fiziksel bölüm birden çok bölüm anahtarları sahiptir. Bir bölüm depolama boyutuna ulaştığında, hizmet sorunsuz bir şekilde bölüm iki yeni bölümlere ayırır ve bölüm anahtarı bu bölümler arasında eşit olarak böler. Bölümleri geçici olduğundan, "Bölüm anahtarı karmaları aralıklarını gösterir bir bölüm anahtar aralığı" bir soyutlamasıdır API'leri kullanın. 

Azure Cosmos DB'ye bir sorgu verdiğinizde, SDK'sı mantıksal adımları gerçekleştirir:

* Sorgu yürütme planını belirlemek için SQL sorgusunu ayrıştırılamıyor. 
* Sorgu filtre bölüm anahtarı karşı içeriyorsa, ister `SELECT * FROM c WHERE c.city = "Seattle"`, tek bir bölüme yönlendirilir. Sorgu bölüm anahtarına göre bir filtre yok sonra'ındaki tüm bölümler yürütülür ve sonuçları birleştirilir, istemci tarafı.
* Sorgunun her bölümü serisi içinde yürütülen veya paralel, istemci yapılandırmasını temel alan. Her bölüm içindeki bir sorgu yapabileceğiniz veya sorgu karmaşıklığına bağlı olarak daha fazla gidiş dönüş sayfa boyutu yapılandırılmış ve toplamanın sağlanan. Her yürütme sayısını döndürür [istek birimi](request-units.md) sorgu yürütme ve isteğe bağlı olarak, sorgu yürütme istatistikleri kullanılan. 
* SDK'ın bölümler arasında sorgu sonuçlarının bir özetini gerçekleştirir. Bölümler arasında sorgu bir ORDER BY içerir, örneğin, daha sonra tek tek bölümler sonuçlardan birleştirme-sıralanmış içinde genel olarak sıralanmış sonuçları döndürmek için sunulur. Sorgu gibi bir toplama ise `COUNT`, genel sayısı üretmek için tek tek bölümler alınan sayımların toplanır.

SDK'ları, sorgu yürütme için çeşitli seçenekler sunar. Örneğin,. NET'te Bu seçenekler kullanılabilir `FeedOptions` sınıfı. Aşağıdaki tabloda bu seçeneklerin ve bunların sorgu yürütme süresini nasıl etkilediği açıklar. 

| Seçenek | Açıklama |
| ------ | ----------- |
| `EnableCrossPartitionQuery` | Ayarlanmalıdır birden fazla bölüm arasında yürütülecek true herhangi sorgu için gerektirir. Bu, bilinçli performans seçenekleri geliştirme zamanı sırasında yapmanız sağlamak için açık bir bayrak olur. |
| `EnableScanInQuery` | Dizin oluşturma dışında seçtiyseniz true, ancak sorguyu bir tarama ile yine de çalıştırmak istediğiniz ayarlamanız gerekir. Yalnızca geçerli istenen filtre yolu için dizin oluşturma devre dışı bırakıldı. | 
| `MaxItemCount` | Sunucuya gidiş dönüş döndürülecek öğe maksimum sayısı. -1, ayarına göre sunucu öğe sayısını yönetme izin verebilirsiniz. Veya yalnızca az sayıda gidiş dönüş başına öğeleri almak için bu değer düşürebilirsiniz. 
| `MaxBufferedItemCount` | Bir istemci-tarafı seçenektir ve bölümler arası ORDER BY gerçekleştirirken bellek tüketimini sınırlamak için kullanılır. Daha yüksek bir değer bölümler arası sıralama gecikme süresini azaltmaya yardımcı olur. |
| `MaxDegreeOfParallelism` | Azure Cosmos veritabanı hizmetinde paralel sorgu yürütme sırasında istemci tarafında çalışan eşzamanlı işlem sayısını alır veya ayarlar. Bir pozitif özellik değeri kümesi değerine eşzamanlı işlemlerin sayısını sınırlar. 0'dan düşük bir değere ayarlanırsa, sistem çalıştırmak için eşzamanlı işlemlerin sayısını otomatik olarak karar verir. |
| `PopulateQueryMetrics` | Yük süresi sorgu yürütme derleme zamanı, döngü süresi dizini ve belge gibi çeşitli aşamaları harcanan süreyi istatistiklerin ayrıntılı günlüğü etkinleştirir. İstatistikleri sorgu çıktısı sorgu performansı sorunlarını tanılamak için Azure desteği ile paylaşabilirsiniz. |
| `RequestContinuation` | Herhangi bir sorgu tarafından döndürülen donuk devamlılık belirteci ileterek sorgu yürütme devam edebilir. Devamlılık belirteci, sorgu yürütme için gerekli tüm durum kapsüller. |
| `ResponseContinuationTokenLimitInKb` | Sunucu tarafından döndürülen devam belirtecini en büyük boyutunu sınırlandırabilirsiniz. Uygulama ana bilgisayarı yanıt üstbilgi boyutu sınırları varsa bunu ayarlamanız gerekebilir. Bu ayar, genel süresi ve sorgu için tüketilen RU artırabilir.  |

Örneğin, örnek bir sorgu ile bir koleksiyon üzerinde istenen bölüm anahtarı ele alalım `/city` bölüm anahtarı ve 100.000 RU/sn aktarım hızı ile sağlanan olarak. Bu sorgu kullanarak istek `CreateDocumentQuery<T>` aşağıdaki gibi .NET içinde:

```cs
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
    "SELECT * FROM c WHERE c.city = 'Seattle'", 
    new FeedOptions 
    { 
        PopulateQueryMetrics = true, 
        MaxItemCount = -1, 
        MaxDegreeOfParallelism = -1, 
        EnableCrossPartitionQuery = true 
    }).AsDocumentQuery();

FeedResponse<dynamic> result = await query.ExecuteNextAsync();
```

Yukarıda gösterilen SDK kod parçacığını aşağıdaki REST API isteği karşılık gelmektedir:

```
POST https://arramacquerymetrics-westus.documents.azure.com/dbs/db/colls/sample/docs HTTP/1.1
x-ms-continuation: 
x-ms-documentdb-isquery: True
x-ms-max-item-count: -1
x-ms-documentdb-query-enablecrosspartition: True
x-ms-documentdb-query-parallelizecrosspartitionquery: True
x-ms-documentdb-query-iscontinuationexpected: True
x-ms-documentdb-populatequerymetrics: True
x-ms-date: Tue, 27 Jun 2017 21:52:18 GMT
authorization: type%3dmaster%26ver%3d1.0%26sig%3drp1Hi83Y8aVV5V6LzZ6xhtQVXRAMz0WNMnUuvriUv%2b4%3d
x-ms-session-token: 7:8,6:2008,5:8,4:2008,3:8,2:2008,1:8,0:8,9:8,8:4008
Cache-Control: no-cache
x-ms-consistency-level: Session
User-Agent: documentdb-dotnet-sdk/1.14.1 Host/32-bit MicrosoftWindowsNT/6.2.9200.0
x-ms-version: 2017-02-22
Accept: application/json
Content-Type: application/query+json
Host: arramacquerymetrics-westus.documents.azure.com
Content-Length: 52
Expect: 100-continue

{"query":"SELECT * FROM c WHERE c.city = 'Seattle'"}
```

Her sorgu yürütme sayfası bir REST API'ye karşılık gelen `POST` ile `Accept: application/query+json` üstbilgi ve gövde SQL sorgusu. Her sorgu bir hale getirir veya daha fazla sunucuya ile yuvarlak `x-ms-continuation` belirteci, yürütme devam etmek için istemci ile sunucu arasında okunmaz. FeedOptions yapılandırma seçenekleri, sunucuya istek üst bilgilerini biçiminde geçirilir. Örneğin, `MaxItemCount` karşılık gelen `x-ms-max-item-count`. 

İstek (okunabilirliği artırmak için kesilmiştir) şu yanıtı döndürür:

```
HTTP/1.1 200 Ok
Cache-Control: no-store, no-cache
Pragma: no-cache
Transfer-Encoding: chunked
Content-Type: application/json
Server: Microsoft-HTTPAPI/2.0
Strict-Transport-Security: max-age=31536000
x-ms-last-state-change-utc: Tue, 27 Jun 2017 21:01:57.561 GMT
x-ms-resource-quota: documentSize=10240;documentsSize=10485760;documentsCount=-1;collectionSize=10485760;
x-ms-resource-usage: documentSize=1;documentsSize=884;documentsCount=2000;collectionSize=1408;
x-ms-item-count: 2000
x-ms-schemaversion: 1.3
x-ms-alt-content-path: dbs/db/colls/sample
x-ms-content-path: +9kEANVq0wA=
x-ms-xp-role: 1
x-ms-documentdb-query-metrics: totalExecutionTimeInMs=33.67;queryCompileTimeInMs=0.06;queryLogicalPlanBuildTimeInMs=0.02;queryPhysicalPlanBuildTimeInMs=0.10;queryOptimizationTimeInMs=0.00;VMExecutionTimeInMs=32.56;indexLookupTimeInMs=0.36;documentLoadTimeInMs=9.58;systemFunctionExecuteTimeInMs=0.00;userFunctionExecuteTimeInMs=0.00;retrievedDocumentCount=2000;retrievedDocumentSize=1125600;outputDocumentCount=2000;writeOutputTimeInMs=18.10;indexUtilizationRatio=1.00
x-ms-request-charge: 604.42
x-ms-serviceversion: version=1.14.34.4
x-ms-activity-id: 0df8b5f6-83b9-4493-abda-cce6d0f91486
x-ms-session-token: 2:2008
x-ms-gatewayversion: version=1.14.33.2
Date: Tue, 27 Jun 2017 21:59:49 GMT
```

Bir sorgudan döndürülen anahtar yanıt üstbilgilerini şunları içerir:

| Seçenek | Açıklama |
| ------ | ----------- |
| `x-ms-item-count` | Yanıtta döndürülen öğe sayısı. Sağlanan üzerinde bağımlı budur `x-ms-max-item-count`, en yüksek yanıt yükü boyutu, sağlanan aktarım hızı ve sorgu yürütme zamanı içinde uygun öğe sayısı. |  
| `x-ms-continuation:` | Ek sonuçlar varsa, sorgu yürütme devam etmek için devamlılık belirteci. | 
| `x-ms-documentdb-query-metrics` | Sorgu yürütme istatistikleri. İstatistikleri sorgu yürütme çeşitli aşamalarında harcanan süreyi içeren ayrılmış bir dize budur. Döndürülen if `x-ms-documentdb-populatequerymetrics` ayarlanır `True`. | 
| `x-ms-request-charge` | Sayısını [istek birimi](request-units.md) sorgu tarafından kullanılan. | 

REST API istek üst bilgileri ve seçenekleri hakkında daha fazla bilgi için bkz: [REST API kullanarak kaynakları sorgulama](https://docs.microsoft.com/rest/api/cosmos-db/querying-cosmosdb-resources-using-the-rest-api).

## <a name="best-practices-for-query-performance"></a>Sorgu performansı için en iyi yöntemler
Azure Cosmos DB sorgu performansını etkileyen en yaygın faktörler aşağıda verilmiştir. Biz bu makalede bu konuların her daha derinlemesine.

| faktörü | İpucu | 
| ------ | -----| 
| Sağlanan aktarım hızı | RU sorgu ölçün ve sorgularınızı gerekli sağlanan aktarım hızına sahip olduğundan emin olun. | 
| Bölümleme ve bölüm anahtarları | Düşük gecikme süresi filtre yan tümcesi bölüm anahtar değer sorguları favor. |
| SDK ve sorgu seçenekleri | Doğrudan bağlantı gibi SDK en iyi uygulamaları izleyin ve istemci tarafı sorgu yürütme seçeneklerini ayarlayın. |
| Ağ gecikmesi | Ağ Yükü ölçümdeki hesaba ve en yakın bölgeden okumak için çok girişli API'lerini kullanın. |
| Dizin Oluşturma İlkesi | Gerekli dizin yolları/ilke sorgu olduğundan emin olun. |
| Sorgu yürütme ölçümleri | Sorgu ve veri şekillerinin olası yeniden tanımlamak için sorgu yürütme ölçümleri çözümleyin.  |

### <a name="provisioned-throughput"></a>Sağlanan aktarım hızı
Cosmos DB kapsayıcıları ayrılmış üretilen iş istek birimi (RU) saniye başına ifade edilen her veri oluşturun. 1 KB boyutundaki bir belgeyi okuma 1. RU ve (sorguları da dahil) her işlemin dönüşür sabit sayıda RU kendi kapsamına bağlı. Örneğin, 1000 varsa kapsayıcınız için sağlanan RU/s ve sahip olduğunuz gibi bir sorguda `SELECT * FROM c WHERE c.city = 'Seattle'` , 5 RU tüketir, sonra gerçekleştirebileceğiniz (1000 RU/sn) / (5 RU/sorgu) sorgularını saniye başına 200 Sorgu/sn =. 

200'den fazla sorgular/sn gönderirseniz, gelen istekleri yukarıda 200/sn hız sınırlama hizmetini başlatır. Otomatik olarak SDK'lar geri alma/yeniden deneme yaparak bu durumu işlemek, bu nedenle bu sorgular için daha yüksek bir gecikme fark edebilirsiniz. Gerekli değer için sağlanan aktarım hızı artırmak, sorgunun gecikme süresi ve aktarım hızını artırır. 

İstek birimleri hakkında daha fazla bilgi için bkz: [istek birimi](request-units.md).

### <a name="partitioning-and-partition-keys"></a>Bölümleme ve bölüm anahtarları
Azure Cosmos DB ile genellikle aşağıdaki sırayla kadar hızlı/en verimli daha yavaş/daha az verimli sorgular gerçekleştirin. 

* Bir tek bölüm anahtarı ve öğe anahtarı alma
* Tek bir bölüm anahtarı üzerindeki filtre yan tümcesi ile sorgulama
* Bir eşitlik veya aralık filtre yan tümcesi olmadan herhangi bir özellikte sorgulama
* Bir filtre içermeyen sorgulama

Tüm bölümleri başvurmanız gereken sorguları gereken daha yüksek gecikme süresi ve yüksek RU'ları kullanabilir. Her bölüm tüm özellikleri karşı otomatik dizin oluşturma olduğundan, sorgu verimli bir şekilde dizinden bu durumda sunulabilir. Paralellik seçenekleri kullanarak daha hızlı bölüme yayılan sorguları yapabilirsiniz.

Bölümlendirme ve bölüm anahtarları hakkında daha fazla bilgi için bkz: [Azure Cosmos DB'de bölümleme](partition-data.md).

### <a name="sdk-and-query-options"></a>SDK ve sorgu seçenekleri
Bkz: [performans ipuçları](performance-tips.md) ve [performans testi](performance-testing.md) için en iyi istemci tarafında performans Azure Cosmos DB'den alma. Bu, en son SDK'larını kullanarak, varsayılan bağlantı, çöp toplama sıklığı sayısı gibi platforma özgü yapılandırmaları yapılandırma ve doğrudan/TCP gibi basit bağlantı seçeneklerini kullanarak içerir. 


#### <a name="max-item-count"></a>En Fazla Öğe Sayısı
Sorgular, değerini `MaxItemCount` uçtan uca sorgu zamanında önemli bir etkisi olabilir. Her sunucuya gidiş dönüş öğe sayısından daha fazla iade `MaxItemCount` (varsayılan 100 öğe). Bu daha yüksek bir değere ayarlanması (-1'dir maksimum ve önerilen), sorgu süresi genel sorgular büyük sonuç kümeleri için özellikle bir istemci ve sunucu arasındaki gidiş dönüş sayısını sınırlayarak artırır.

```cs
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
    "SELECT * FROM c WHERE c.city = 'Seattle'", 
    new FeedOptions 
    { 
        MaxItemCount = -1, 
    }).AsDocumentQuery();
```

#### <a name="max-degree-of-parallelism"></a>Maksimum paralellik derecesi
Sorguları için ayarlama `MaxDegreeOfParallelism` özellikle bölümler arası sorgular (Filtresiz bölüm anahtarı değeri) çalıştırıyorsanız, uygulamanız için en iyi yapılandırmaları tanımlamak için. `MaxDegreeOfParallelism`  en fazla sayısını Paralel Görevler, başka bir deyişle, en fazla paralel olarak ziyaret etme işlemlerinin bölüm denetler. 

```cs
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
    "SELECT * FROM c WHERE c.city = 'Seattle'", 
    new FeedOptions 
    { 
        MaxDegreeOfParallelism = -1, 
        EnableCrossPartitionQuery = true 
    }).AsDocumentQuery();
```

Varsayalım
* D = varsayılan en yüksek sayısı (toplam istemci makinede işlemci sayısı =) Paralel Görevler
* P kullanıcı tarafından belirtilen en fazla sayıda paralel görev =
* N sorgu yanıtlarken ziyaret gereken bölüm sayısı =

P. farklı değerleri için paralel sorguları nasıl davranacaktır etkilerini aşağıda verilmiştir
* (P == 0) = > seri modu
* (P 1 ==) = > bir görevin en fazla
* (P > 1) = > Min (P, N) Paralel Görevler 
* (P < 1) = > Min (N, D) Paralel Görevler

SDK sürüm notları ve uygulanan sınıflar ve yöntemler hakkında bilgi için [SQL SDK'ları](sql-api-sdk-dotnet.md)

### <a name="network-latency"></a>Ağ gecikmesi
Bkz: [Azure Cosmos DB genel dağıtımını](tutorial-global-distribution-sql-api.md) genel dağıtımını ayarlama ve en yakın bölgeyi bağlanın. Birden çok gidiş dönüş yapmak veya büyük sonuç sorgudan kümesi almak, ihtiyacınız olduğunda ağ gecikmesi sorgu performansı üzerinde önemli bir etkisi vardır. 

Sorgu yürütme ölçümleri bölümüne sorguları sunucu yürütme süresi almak nasıl açıklar ( `totalExecutionTimeInMs`), böylece sorgu yürütme için harcanan zaman ve ağ yolda harcadığı sürenin arasında ayırt edebilirsiniz.

### <a name="indexing-policy"></a>Dizin oluşturma ilkesi
Bkz: [dizin oluşturma ilkesini yapılandırma](index-policy.md) yollarını, tür ve modlarını ve bunların sorgu yürütme nasıl etkilediği dizinleme. Varsayılan olarak, dizin oluşturma ilkesini dizeler için karma dizin kullanır etkin olduğu için eşitlik sorguları, ancak aralık sorguları/sıralama ölçütü sorguları için değil. Aralık sorguları için dizeleri ihtiyacınız varsa, tüm dizeleri aralığı dizin türü belirtme öneririz. 

Varsayılan olarak, Azure Cosmos DB tüm verilere otomatik dizin oluşturma uygular. Yüksek performanslı ekleme senaryolarında, bu işlemi her ekleme işleminin RU maliyetini azaltacak şekilde dışarıda bırakmayı düşünün. 

## <a name="query-execution-metrics"></a>Sorgu yürütme ölçümleri
İsteğe bağlı geçirerek sorgu yürütme üzerindeki ayrıntılı ölçümleri elde `x-ms-documentdb-populatequerymetrics` üst bilgisi (`FeedOptions.PopulateQueryMetrics` .NET SDK'sındaki). Döndürülen değer `x-ms-documentdb-query-metrics` Gelişmiş sorun giderme sorgunun yürütülmesi için gereken aşağıdaki anahtar-değer çiftleri sahiptir. 

```cs
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
    "SELECT * FROM c WHERE c.city = 'Seattle'", 
    new FeedOptions 
    { 
        PopulateQueryMetrics = true, 
    }).AsDocumentQuery();

FeedResponse<dynamic> result = await query.ExecuteNextAsync();

// Returns metrics by partition key range Id
IReadOnlyDictionary<string, QueryMetrics> metrics = result.QueryMetrics;

```

| Ölçüm | Birim | Açıklama | 
| ------ | -----| ----------- |
| `totalExecutionTimeInMs` | milisaniye | Sorgu yürütme süresi | 
| `queryCompileTimeInMs` | milisaniye | Sorgu derleme zamanı  | 
| `queryLogicalPlanBuildTimeInMs` | milisaniye | Mantıksal bir sorgu planı oluşturabilirsiniz. | 
| `queryPhysicalPlanBuildTimeInMs` | milisaniye | Fiziksel bir sorgu planı oluşturabilirsiniz. | 
| `queryOptimizationTimeInMs` | milisaniye | Sorgu en iyi duruma getirme için harcanan süre | 
| `VMExecutionTimeInMs` | milisaniye | Sorgu çalışma zamanında harcanan süre | 
| `indexLookupTimeInMs` | milisaniye | Fiziksel dizini katmanda için harcanan süre | 
| `documentLoadTimeInMs` | milisaniye | Belge yüklenirken harcanan süre  | 
| `systemFunctionExecuteTimeInMs` | milisaniye | Milisaniye cinsinden yürütme sistemi (yerleşik) işlevleri için harcanan toplam süre  | 
| `userFunctionExecuteTimeInMs` | milisaniye | Milisaniye cinsinden yürütme kullanıcı tarafından tanımlanan işlevler için harcanan toplam süre | 
| `retrievedDocumentCount` | count | Toplam alınan belge sayısı  | 
| `retrievedDocumentSize` | bayt | Bayt alınan belgelerin toplam boyutu  | 
| `outputDocumentCount` | count | Çıkış belge sayısı | 
| `writeOutputTimeInMs` | milisaniye | Milisaniye cinsinden sorgu yürütme süresi | 
| `indexUtilizationRatio` | oranı (< = 1) | Belge sayısı filtre ile eşleşen belge sayısının oranını yüklendi  | 

İstemci SDK'ları, dahili olarak sorgu her bölüm içinde sunmak için birden çok sorgu işlemleri yapabilir. İstemci toplam sonuç aşarsanız bölüm başına birden çok çağrıda `x-ms-max-item-count`, sorgu bölüm için sağlanan aktarım hızını aşarsa veya sorgu yükü en büyük boyut sayfa başına ulaşırsa veya sorgu ayrılan sistem ulaşırsa zaman aşımı sınırı. Her kısmi sorgu yürütme döndürür bir `x-ms-documentdb-query-metrics` o sayfanın. 

Bazı ölçümler yorumlama sorgu yürütmeyi döndürdü ve bazı örnek sorgular'aşağıda verilmiştir: 

| Sorgu | Örnek ölçüm | Açıklama | 
| ------ | -----| ----------- |
| `SELECT TOP 100 * FROM c` | `"RetrievedDocumentCount": 101` | Alınan belge sayısı TOP yan tümcesini eşleştirmek için 100'den fazla 1'dir. Sorgu zaman harcanan çoğunlukla `WriteOutputTime` ve `DocumentLoadTime` tarama olduğundan. | 
| `SELECT TOP 500 * FROM c` | `"RetrievedDocumentCount": 501` | RetrievedDocumentCount (TOP yan tümcesini eşleştirmek için daha yüksek 500 + 1) sunulmuştur. | 
| `SELECT * FROM c WHERE c.N = 55` | `"IndexLookupTime": "00:00:00.0009500"` | Bir dizin arama olduğu için yaklaşık 0,9 ms içinde IndexLookupTime bir anahtar arama için harcandığını `/N/?`. | 
| `SELECT * FROM c WHERE c.N > 55` | `"IndexLookupTime": "00:00:00.0017700"` | Biraz daha fazla (1.7 ms) için harcanan süre IndexLookupTime bir aralık tarama, bir dizin arama olduğu için `/N/?`. | 
| `SELECT TOP 500 c.N FROM c` | `"IndexLookupTime": "00:00:00.0017700"` | Aynı anda için harcanan `DocumentLoadTime` önceki sorgular, ancak daha düşük `WriteOutputTime` biz yalnızca tek bir özellik yansıtma çünkü. | 
| `SELECT TOP 500 udf.toPercent(c.N) FROM c` | `"UserDefinedFunctionExecutionTime": "00:00:00.2136500"` | Yaklaşık 213 ms, geçen `UserDefinedFunctionExecutionTime` her değerini UDF çalıştırma `c.N`. |
| `SELECT TOP 500 c.Name FROM c WHERE STARTSWITH(c.Name, 'Den')` | `"IndexLookupTime": "00:00:00.0006400", "SystemFunctionExecutionTime": "00:00:00.0074100"` | Yaklaşık 0,6 ms, geçen `IndexLookupTime` üzerinde `/Name/?`. Çoğu sorgu yürütme süresi (ms yaklaşık 7) `SystemFunctionExecutionTime`. |
| `SELECT TOP 500 c.Name FROM c WHERE STARTSWITH(LOWER(c.Name), 'den')` | `"IndexLookupTime": "00:00:00", "RetrievedDocumentCount": 2491,  "OutputDocumentCount": 500` | Sorgu, bir tarama gerçekleştirilir, kullandığından `LOWER`, ve 500 2491 alınan belgeleri dışında döndürülür. |


## <a name="next-steps"></a>Sonraki adımlar
* Desteklenen SQL sorgu işleçlerini ve anahtar sözcükleri hakkında bilgi edinmek için [SQL sorgusu](sql-query-getting-started.md). 
* İstek birimleri hakkında bilgi edinmek için [istek birimi](request-units.md).
* Dizin oluşturma ilkesi hakkında bilgi edinmek için [dizin oluşturma ilkesi](index-policy.md) 


