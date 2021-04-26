---
title: SQL API Azure Cosmos DB için SQL sorgu ölçümleri
description: Azure Cosmos DB isteklerinin SQL sorgu performansını işaretleme ve hatalarını ayıklama hakkında bilgi edinin.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 01/06/2021
ms.author: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: 019ca26143a4879efafa973299703f0abcb21162
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "102488095"
---
# <a name="tuning-query-performance-with-azure-cosmos-db"></a>Azure Cosmos DB ile sorgu performansını ayarlama
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB, şema veya ikincil dizinler gerekmeden [verileri sorgulamak için bir SQL API 'si](./sql-query-getting-started.md)sağlar. Bu makalede geliştiriciler için aşağıdaki bilgiler sağlanmaktadır:

* Azure Cosmos DB SQL sorgu yürütmesinin nasıl çalıştığı hakkında üst düzey Ayrıntılar
* Sorgu isteği ve yanıt üstbilgileri ve istemci SDK seçenekleri hakkındaki ayrıntılar
* Sorgu performansı için ipuçları ve en iyi uygulamalar
* Sorgu performansının hatalarını ayıklamak için SQL yürütme istatistikleriyle nasıl yararlanmanın örnekleri

## <a name="about-sql-query-execution"></a>SQL sorgu yürütmesi hakkında

Azure Cosmos DB, verileri kapsayıcılarda depoladığınızda, bu, herhangi bir [depolama boyutuna veya istek aktarım](partitioning-overview.md)hızına genişleyebilirler. Azure Cosmos DB, verilerin büyümesini veya sağlanan aktarım hızını artırmak için, kapakların altındaki fiziksel bölümlerdeki verileri sorunsuzca ölçeklendirir. REST API veya desteklenen [SQL SDK 'lardan](sql-api-sdk-dotnet.md)birini kullanarak herhangi BIR kapsayıcıya SQL sorguları verebilirsiniz.

Bölümlendirme hakkında kısa bir genel bakış: "şehir" gibi bir bölüm anahtarı tanımlayarak verilerin fiziksel bölümler arasında nasıl bölüneceği belirlenir. Tek bir bölüm anahtarına ait veriler (örneğin, "City" = = "Seattle") fiziksel bir bölüm içinde depolanır, ancak genellikle tek bir fiziksel bölümde birden çok bölüm anahtarı bulunur. Bir bölüm, depolama boyutuna ulaştığında, bu bölümü iki yeni bölüme sorunsuz bir şekilde ayırır ve bölüm anahtarını bu bölümlere eşit olarak böler. Bölümler geçici olduğundan, API 'Ler bölüm anahtarı karmalarının aralıklarını gösteren bir "bölüm anahtar aralığı" soyutlamasını kullanır. 

Azure Cosmos DB bir sorgu verdiğinizde, SDK şu mantıksal adımları gerçekleştirir:

* Sorgu yürütme planını öğrenmek için SQL sorgusunu ayrıştırın. 
* Sorgu, gibi bölüm anahtarına karşı bir filtre içeriyorsa `SELECT * FROM c WHERE c.city = "Seattle"` , tek bir bölüme yönlendirilir. Sorgunun bölüm anahtarı üzerinde bir filtresi yoksa, bu, tüm bölümlerde yürütülür ve sonuçlar birleştirilmiş istemci tarafındadır.
* Sorgu, istemci yapılandırmasına bağlı olarak, serideki her bölümde veya paralel olarak yürütülür. Her bölümde sorgu karmaşıklık, yapılandırılan sayfa boyutu ve koleksiyonun sağlanmış iş hacmi temelinde bir veya daha fazla gidiş dönüş yapabilir. Her yürütme, sorgu yürütme tarafından tüketilen [istek birimi](request-units.md) sayısını ve isteğe bağlı olarak sorgu yürütme istatistiklerini döndürür. 
* SDK, bölümler arasında sorgu sonuçlarının özetlemesini gerçekleştirir. Örneğin, sorgu bölümler arasında bir SıRALAMA içeriyorsa, tek tek bölümlerin sonuçları birleştirme-genel olarak sıralanmış sırada sonuçları döndürecek şekilde sıralanır. Sorgu, gibi bir toplamadır `COUNT` , her bölümden alınan sayımlar, genel sayıyı oluşturmak için toplanır.

SDK 'lar sorgu yürütme için çeşitli seçenekler sağlar. Örneğin, .NET ' te bu seçenekler `FeedOptions` sınıfında mevcuttur. Aşağıdaki tabloda bu seçenekler ve sorgu yürütme süresini nasıl etkilediği açıklanmaktadır. 

| Seçenek | Açıklama |
| ------ | ----------- |
| `EnableCrossPartitionQuery` | Birden fazla bölüm genelinde yürütülmesi gereken herhangi bir sorgu için true olarak ayarlanmalıdır. Bu, geliştirme sırasında bilinçli performans avantajları yapmanız için açık bir bayrak sağlar. |
| `EnableScanInQuery` | Dizin oluşturmayı kabul ettiyseniz, ancak yine de sorguyu bir tarama yoluyla çalıştırmak istiyorsanız, true olarak ayarlanmalıdır. Yalnızca istenen filtre yolu için dizin oluşturma devre dışıysa geçerlidir. | 
| `MaxItemCount` | Sunucuya gidiş dönüş başına döndürülecek en fazla öğe sayısı. -1 ' i ayarlayarak, sunucunun öğe sayısını yönetmesine izin verebilirsiniz. Ya da bu değeri, gidiş dönüş başına yalnızca az sayıda öğe almak için düşürebilirsiniz. 
| `MaxBufferedItemCount` | Bu bir istemci tarafı seçeneğidir ve çapraz bölüm SıRALAMA gerçekleştirirken bellek tüketimini sınırlamak için kullanılır. Daha yüksek bir değer, çapraz bölümlü sıralama gecikmesini azaltmaya yardımcı olur. |
| `MaxDegreeOfParallelism` | Azure Cosmos veritabanı hizmetinde paralel sorgu yürütme sırasında istemci tarafında çalışan eşzamanlı işlem sayısını alır veya ayarlar. Pozitif Özellik değeri, küme değeri ile eşzamanlı işlem sayısını sınırlar. 0 ' dan küçük olarak ayarlandıysa, sistem, çalıştırılacak eşzamanlı işlem sayısına otomatik olarak karar verir. |
| `PopulateQueryMetrics` | Sorgu yürütmenin derleme süresi, Dizin döngüsü süresi ve belge yükleme süresi gibi çeşitli aşamalarda harcanan sürenin ayrıntılı olarak günlüğe kaydedilmesini sunar. Sorgu performans sorunlarını tanılamak için, Azure desteği ile sorgu istatistikleriyle çıkış paylaşabilirsiniz. |
| `RequestContinuation` | Sorgu yürütmeyi, herhangi bir sorgu tarafından döndürülen donuk devamlılık belirtecini geçirerek devam ettirebilirsiniz. Devamlılık belirteci, sorgu yürütmesi için gereken tüm durumu kapsüller. |
| `ResponseContinuationTokenLimitInKb` | Sunucu tarafından döndürülen devamlılık belirtecinin maksimum boyutunu sınırlayabilirsiniz. Uygulama ana bilgisayarınızda yanıt üst bilgisi boyutu sınırları varsa bunu ayarlamanız gerekebilir. Bu ayar, sorgu için tüketilen toplam süreyi ve RUs düzeyini artırabilir.  |

Örneğin, bölüm anahtarı olarak bir koleksiyonda istenen bölüm anahtarı üzerinde örnek bir sorgu ele alalım `/city` ve 100.000 ru/s işleme ile sağlanır. Aşağıdaki gibi .NET ' i kullanarak bu sorguyu isteyebilirsiniz `CreateDocumentQuery<T>` :

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

Yukarıda gösterilen SDK kod parçacığı, aşağıdaki REST API isteğine karşılık gelir:

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

Her sorgu yürütme sayfası `POST` , üst bilgiyle bir REST API `Accept: application/query+json` ve GÖVDEDEKI SQL sorgusunu karşılık gelir. Her sorgu sunucuya bir veya daha fazla gidiş dönüş yapar `x-ms-continuation` ve bu belirteç, istemci ile sunucu arasında, yürütmeyi sürdürmesini sağlar. Feedoseçeneklerindeki yapılandırma seçenekleri sunucuya istek üstbilgileri biçiminde geçirilir. Örneğin, `MaxItemCount` öğesine karşılık gelir `x-ms-max-item-count` . 

İstek, aşağıdaki (okunabilirlik için kesildi) yanıtı döndürür:

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

Sorgudan döndürülen anahtar yanıt üstbilgileri şunları içerir:

| Seçenek | Açıklama |
| ------ | ----------- |
| `x-ms-item-count` | Yanıtta döndürülen öğe sayısı. Bu, sağlanan `x-ms-max-item-count` en yüksek yanıt yükü boyutu, sağlanan aktarım hızı ve sorgu yürütme süresi içinde olabilecek öğelerin sayısına bağlıdır. |  
| `x-ms-continuation:` | Ek sonuçlar varsa sorgunun yürütülmesini sürdürecek devamlılık belirteci. | 
| `x-ms-documentdb-query-metrics` | Yürütmenin sorgu istatistikleri. Bu, sorgu yürütmenin çeşitli aşamalarında harcanan sürenin istatistiklerini içeren, ayrılmış bir dizedir. `x-ms-documentdb-populatequerymetrics`Olarak ayarlandıysa döndürüldü `True` . | 
| `x-ms-request-charge` | Sorgu tarafından tüketilen [istek birimi](request-units.md) sayısı. | 

REST API istek üstbilgileri ve seçenekleriyle ilgili ayrıntılar için, bkz. [REST API kullanarak kaynakları sorgulama](/rest/api/cosmos-db/querying-cosmosdb-resources-using-the-rest-api).

## <a name="best-practices-for-query-performance"></a>Sorgu performansı için en iyi uygulamalar
Sorgu performansını Azure Cosmos DB etkileyen en yaygın etmenler aşağıda verilmiştir. Bu makaledeki konuların her birini daha ayrıntılı bir şekilde inceleyeceğiz.

| Faktör | İpucu | 
| ------ | -----| 
| Sağlanan aktarım hızı | Sorgu başına RU ile ölçüm yapın ve sorgularınız için gereken sağlanmış iş hızına sahip olduğunuzdan emin olun. | 
| Bölümlendirme ve bölüm anahtarları | Filtre yan tümcesindeki bölüm anahtarı değeri için düşük gecikme süresine sahip sorguları tercih edin. |
| SDK ve sorgu seçenekleri | Doğrudan bağlantı gibi SDK en iyi yöntemlerini izleyin ve istemci tarafı sorgu yürütme seçeneklerini ayarlayın. |
| Dizin Oluşturma İlkesi | Sorgu için gerekli dizin oluşturma yollarına/ilkesine sahip olduğunuzdan emin olun. |
| Sorgu yürütme ölçümleri | Sorgu ve veri şekillerinin olası yeniden yazar durumunu belirlemek için sorgu yürütme ölçümlerini çözümleyin.  |

### <a name="provisioned-throughput"></a>Sağlanan aktarım hızı
Cosmos DB, her biri istek birimi (RU) saniye başına belirtilen ayrılmış aktarım hızı ile veri kapsayıcıları oluşturursunuz. 1 KB 'lik bir belgenin okunması 1 RU ve her işlem (sorgular dahil), karmaşıklığına göre sabit bir RU sayısına normalleştirilir. Örneğin, Kapsayıcınız için sağlanan 1000 RU/sn 'niz varsa ve 5 RU kullanan bir sorgunuz varsa `SELECT * FROM c WHERE c.city = 'Seattle'` , saniye başına bu sorgular (1000 ru/s)/(5 ru/sorgu) = 200 sorgu/s gerçekleştirebilirsiniz. 

200 ' den fazla sorgu/sn gönderirseniz, hizmet 200/s 'ye göre hız sınırlaması gelen istekleri başlatır. SDK 'lar geri alma/yeniden deneme gerçekleştirerek bu durumu otomatik olarak işler, bu nedenle bu sorgular için daha fazla gecikme fark edebilirsiniz. Sağlanan aktarım hızını gerekli değere yükseltmek, sorgu gecikme süresini ve aktarım hızını geliştirir. 

İstek birimleri hakkında daha fazla bilgi için bkz. [İstek birimleri](request-units.md).

### <a name="partitioning-and-partition-keys"></a>Bölümlendirme ve bölüm anahtarları
Azure Cosmos DB, genellikle sorguları en hızlı/en verimli ve daha düşük ve daha az verimli bir şekilde gerçekleştirir. 

* Tek bir bölüm anahtarına ve öğe anahtarına al
* Tek bir bölüm anahtarında Filter yan tümcesi içeren sorgu
* Herhangi bir özellikte eşitlik veya Aralık filtresi yan tümcesi olmadan sorgu
* Filtre olmadan sorgulama

Tüm bölümlere ilgilenilmesi gereken sorgular daha fazla gecikme süresine gerek duyar ve daha yüksek bir RUs tüketebilir. Her bölümde tüm özelliklere karşı otomatik dizin oluşturma olduğundan, bu durumda sorgu etkin şekilde bu durumda sunulabilir. Paralellik seçeneklerini kullanarak bölümleri kapsayan sorguları daha hızlı hale getirebilirsiniz.

Bölümlendirme ve bölüm anahtarları hakkında daha fazla bilgi edinmek için bkz. [Azure Cosmos DB bölümlendirme](partitioning-overview.md).

### <a name="sdk-and-query-options"></a>SDK ve sorgu seçenekleri
Azure Cosmos DB en iyi istemci tarafı performansını alma hakkında bilgi için bkz. [Performans ipuçları](performance-tips.md) ve [performans testi](performance-testing.md) . Bu, en son SDK 'Ları kullanarak varsayılan bağlantı sayısı, çöp toplama sıklığı ve doğrudan/TCP gibi hafif bağlantı seçeneklerini kullanma gibi platforma özgü yapılandırmaların yapılandırılmasını içerir. 


#### <a name="max-item-count"></a>En fazla öğe sayısı
Sorgular için değerinin, uçtan uca `MaxItemCount` sorgu süresi üzerinde önemli bir etkisi olabilir. Sunucuya gidiş dönüş, içindeki öğe sayısından `MaxItemCount` (varsayılan 100 öğe) daha fazla olmayacaktır. Bunun daha yüksek bir değere ayarlanması (-1 en yüksek ve önerilir), özellikle büyük sonuç kümelerine sahip sorgularda sunucu ve istemci arasındaki gidiş dönüş sayısını sınırlayarak sorgu sürenizi genelleyerek geliştirir.

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
Sorgular için, `MaxDegreeOfParallelism` özellikle de çapraz bölüm sorguları (bölüm anahtarı değerinde bir filtre olmadan) gerçekleştirirseniz, uygulamanız için en iyi yapılandırmayı belirlemek üzere öğesini ayarlayın. `MaxDegreeOfParallelism`  paralel görev sayısının üst sınırını, yani paralel olarak ziyaret edilecek en fazla bölüm sayısını denetler. 

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

Şunu varsayalım
* D = varsayılan en fazla paralel görev sayısı (= istemci makinesindeki toplam işlemci sayısı)
* P = Kullanıcı tarafından belirtilen en fazla paralel görev sayısı
* N = bir sorguyu yanıtlamak için ziyaret etmesi gereken bölüm sayısı

Paralel sorguların farklı P değerleri için nasıl davrandığına ilişkin bazı etkileri aşağıda verilmiştir.
* (P = = 0) => seri modu
* (P = = 1) = bir görevin en fazla>
* (P > 1) => dk (P, N) paralel görevleri 
* (P < 1) => dk (N, D) paralel görevleri

SDK sürüm notları ve uygulanan sınıflar ve yöntemlerle ilgili ayrıntılar için bkz. [SQL SDK 'ları](sql-api-sdk-dotnet.md)

### <a name="network-latency"></a>Ağ gecikmesi
Genel dağıtımı ayarlama ve en yakın bölgeye bağlanma hakkında bilgi için bkz. [Azure Cosmos DB genel dağıtım](tutorial-global-distribution-sql-api.md) . Ağ gecikmesi, birden çok gidiş dönüş yapmanız veya sorgudan büyük bir sonuç kümesi almanız gerektiğinde sorgu performansına önemli bir etkiye sahiptir. 

Sorgu yürütme ölçümlerinde bulunan bölüm, `totalExecutionTimeInMs` sorgu yürütmede harcanan zaman ve ağ iletimde harcanan zaman arasında ayrım yapabilmeniz için, sorguların sunucu yürütme zamanının () nasıl alınacağını açıklar.

### <a name="indexing-policy"></a>Dizin oluşturma ilkesi
Bkz. Dizin oluşturma ilkesini, türleri ve modları dizin oluşturmak için [yapılandırma](index-policy.md) ve sorgu yürütmeyi nasıl etkilerler. Varsayılan olarak, dizin oluşturma ilkesi, eşitlik sorguları için geçerli olan, ancak sorgulara göre Aralık sorguları/sırası için değil, dizeler için karma dizin oluşturma kullanır. Dizeler için Aralık sorgularına ihtiyacınız varsa, tüm dizeler için Aralık dizin türünü belirtmeyi öneririz. 

Varsayılan olarak, Azure Cosmos DB tüm verilere otomatik dizin oluşturma uygular. Yüksek performanslı ekleme senaryolarında, bu işlemi her ekleme işleminin RU maliyetini azaltacak şekilde dışarıda bırakmayı düşünün. 

## <a name="query-execution-metrics"></a>Sorgu yürütme ölçümleri
İsteğe bağlı `x-ms-documentdb-populatequerymetrics` üstbilgiyi geçirerek ( `FeedOptions.PopulateQueryMetrics` .NET SDK 'da) sorgu yürütmesi hakkında ayrıntılı ölçümler elde edebilirsiniz. İçinde döndürülen değer, `x-ms-documentdb-query-metrics` sorgu yürütmeyle ilgili gelişmiş sorun giderme için aşağıdaki anahtar-değer çiftlerine sahiptir. 

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

| Metric | Birim | Description | 
| ------ | -----| ----------- |
| `totalExecutionTimeInMs` | milisaniye | Sorgu yürütme süresi | 
| `queryCompileTimeInMs` | milisaniye | Sorgu derleme süresi  | 
| `queryLogicalPlanBuildTimeInMs` | milisaniye | Mantıksal sorgu planı oluşturma süresi | 
| `queryPhysicalPlanBuildTimeInMs` | milisaniye | Fiziksel sorgu planı oluşturma süresi | 
| `queryOptimizationTimeInMs` | milisaniye | Sorguyu iyileştirmek için harcanan süre | 
| `VMExecutionTimeInMs` | milisaniye | Sorgu çalışma zamanında harcanan süre | 
| `indexLookupTimeInMs` | milisaniye | Fiziksel dizin katmanında harcanan süre | 
| `documentLoadTimeInMs` | milisaniye | Belge yükleme sırasında harcanan süre  | 
| `systemFunctionExecuteTimeInMs` | milisaniye | Sistem (yerleşik) işlevlerini milisaniye cinsinden çalıştırırken harcanan toplam süre  | 
| `userFunctionExecuteTimeInMs` | milisaniye | Milisaniye cinsinden Kullanıcı tanımlı işlevlerin yürütülmesi için harcanan toplam süre | 
| `retrievedDocumentCount` | count | Toplam alınan belge sayısı  | 
| `retrievedDocumentSize` | sayacının | Toplam alınan belge boyutu (bayt)  | 
| `outputDocumentCount` | count | Çıkış belgelerinin sayısı | 
| `writeOutputTimeInMs` | milisaniye | Sorgu yürütme süresi (milisaniye) | 
| `indexUtilizationRatio` | oran (<= 1) | Filtreyle eşleşen belge sayısının, yüklenen belge sayısına oranı  | 

İstemci SDK 'Ları, her bölüm içinde sorguyu sunmak için dahili olarak birden çok sorgu işlemi yapabilir. İstemci, Toplam Sonuç aşarsa Bölüm başına birden fazla çağrı yapar `x-ms-max-item-count` . sorgu, bölüm için sağlanan aktarım hızını aşarsa veya sorgu yükü sayfa başına en büyük boyuta ulaşırsa veya sorgu sistem tarafından ayrılan zaman aşımı sınırına ulaşırsa. Her kısmi sorgu yürütmesi `x-ms-documentdb-query-metrics` Bu sayfa için bir döndürür. 

İşte bazı örnek sorgular ve sorgu yürütmeden döndürülen bazı ölçümleri yorumlama: 

| Sorgu | Örnek ölçüm | Description | 
| ------ | -----| ----------- |
| `SELECT TOP 100 * FROM c` | `"RetrievedDocumentCount": 101` | Alınan belge sayısı, TOP yan tümcesiyle eşleşecek şekilde 100 + 1 ' dir. Sorgu süresi genellikle ve ' de `WriteOutputTime` `DocumentLoadTime` bir tarama olduğundan harcanacaktır. | 
| `SELECT TOP 500 * FROM c` | `"RetrievedDocumentCount": 501` | RetrievedDocumentCount artık daha yüksek (TOP yan tümcesiyle eşleşecek 500 + 1). | 
| `SELECT * FROM c WHERE c.N = 55` | `"IndexLookupTime": "00:00:00.0009500"` | 0,9 MS hakkında bir anahtar arama için dizin araması olduğundan, `/N/?` | 
| `SELECT * FROM c WHERE c.N > 55` | `"IndexLookupTime": "00:00:00.0017700"` | Bir dizin araması olduğundan, bir Aralık taraması üzerinden ındexlookuptime içinde biraz daha fazla zaman (1,7 MS) harcanmış `/N/?` . | 
| `SELECT TOP 500 c.N FROM c` | `"IndexLookupTime": "00:00:00.0017700"` | `DocumentLoadTime`Önceki sorgular olarak harcanan zaman, ancak `WriteOutputTime` yalnızca bir özellik yansıtıyoruz, daha düşük. | 
| `SELECT TOP 500 udf.toPercent(c.N) FROM c` | `"UserDefinedFunctionExecutionTime": "00:00:00.2136500"` | Yaklaşık 213 MS `UserDefinedFunctionExecutionTime` , her DEĞERINDE UDF 'yi yürütmede harcanmıştır `c.N` . |
| `SELECT TOP 500 c.Name FROM c WHERE STARTSWITH(c.Name, 'Den')` | `"IndexLookupTime": "00:00:00.0006400", "SystemFunctionExecutionTime": "00:00:00.0074100"` | 0,6 MS hakkında, `IndexLookupTime` tarihinde tarihinde harcanmıştır `/Name/?` . Sorgu yürütme süresinin (~ 7 MS) çoğu `SystemFunctionExecutionTime` . |
| `SELECT TOP 500 c.Name FROM c WHERE STARTSWITH(LOWER(c.Name), 'den')` | `"IndexLookupTime": "00:00:00", "RetrievedDocumentCount": 2491,  "OutputDocumentCount": 500` | Sorgu, kullandığı için tarama olarak gerçekleştirilir `LOWER` ve 500 tarafından alınan 2491 ' den fazla belge döndürülür. |


## <a name="next-steps"></a>Sonraki adımlar
* Desteklenen SQL sorgu işleçleri ve anahtar kelimeleri hakkında bilgi edinmek için bkz. [SQL Query](sql-query-getting-started.md). 
* İstek birimleri hakkında bilgi edinmek için bkz. [İstek birimleri](request-units.md).
* Dizin oluşturma ilkesi hakkında bilgi edinmek için bkz. [Dizin oluşturma ilkesi](index-policy.md)