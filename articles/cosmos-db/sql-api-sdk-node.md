---
title: "Azure Cosmos DB: SQL Node. js API 'SI, SDK & kaynakları"
description: Sürüm tarihleri, kullanımdan kaldırma tarihleri ve Azure Cosmos DB Node. js SDK 'sının her sürümü arasında yapılan değişiklikler dahil olmak üzere SQL Node. js API 'SI ve SDK hakkında bilgi edinin.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: reference
ms.date: 09/24/2018
ms.author: dech
ms.openlocfilehash: de1c1b93b813f71b321da0625bc60e0762a859c7
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/01/2019
ms.locfileid: "70207965"
---
# <a name="azure-cosmos-db-nodejs-sdk-for-sql-api-release-notes-and-resources"></a>SQL API için Node. js SDK Azure Cosmos DB: Sürüm notları ve kaynakları
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET değişiklik akışı](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST Kaynak Sağlayıcısı](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Toplu yürütücü-.NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Toplu yürütücü-Java](sql-api-sdk-bulk-executor-java.md)

|Resource  |Bağlantı  |
|---------|---------|
|SDK'yı indir  |   [NPM](https://www.npmjs.com/package/@azure/cosmos) 
|API Belgeleri  |  [JavaScript SDK başvuru belgeleri](https://docs.microsoft.com/javascript/api/%40azure/cosmos/?view=azure-node-latest)
|SDK yükleme yönergeleri  |  [Yükleme yönergeleri](https://github.com/Azure/azure-cosmos-js#installation)
|SDK 'ya katkıda bulunma | [GitHub](https://github.com/Azure/azure-cosmos-js/tree/master)
| Örnekler | [Node. js kod örnekleri](sql-api-nodejs-samples.md)
| Başlangıç Öğreticisi | [JavaScript SDK 'sını kullanmaya başlama](sql-api-nodejs-get-started.md)
| Web uygulaması öğreticisi | [Azure Cosmos DB kullanarak bir Node. js web uygulaması oluşturma](sql-api-nodejs-application.md)
| Desteklenen geçerli platform | [Node. js v12. x](https://nodejs.org/en/blog/release/v12.7.0/) -SDK sürüm 3. x. x<br/>[Node. js ile v10 arasındaki. x](https://nodejs.org/en/blog/release/v10.6.0/) -SDK sürüm 3. x. x<br/>[Node. js V8. x](https://nodejs.org/en/blog/release/v8.16.0/) -SDK sürüm 3. x. x<br/>[Node. js V6. x](https://nodejs.org/en/blog/release/v6.10.3/) -SDK sürüm 2. x. x<br/>[Node. js v 4.2.0](https://nodejs.org/en/blog/release/v4.2.0/)-SDK sürüm 1. x. x<br/> [Node. js v 0.12](https://nodejs.org/en/blog/release/v0.12.0/)-SDK sürüm 1. x. x<br/> [Node. js v 0.10](https://nodejs.org/en/blog/release/v0.10.0/)-SDK sürüm 1. x. x

## <a name="release-notes"></a>Sürüm notları

### <a name="3.1.0"/>3.1.0</a>
* Varsayılan Responsecontinuationtokenlimitınkb değerini 1kb olarak ayarlayın. Varsayılan olarak, uzun üstbilgileri önlemek için bunu 1 KB 'lık 'ye kadık (node. js ' nin genel üst bilgi boyutu sınırı vardır). Bir Kullanıcı bu alanı daha uzun üst bilgiler için izin verecek şekilde ayarlayabilir, bu da arka ucun sorgu yürütmesini iyileştirmenize yardımcı olabilir.
* Disablessldoğrulaması 'nı kaldırın. Bu seçeneğin [#388](https://github.com/Azure/azure-cosmos-js/pull/388) açıklanan yeni alternatifleri vardır

### <a name="3.0.4"/>3.0.4</a>
* Initialheaders 'in bölüm anahtarı üst bilgisini açıkça ayarlamaya izin ver
* Gereksiz dosyaların yayımlanmasını engellemek için Package. JSON # dosyalarını kullanın
* Daha eski node + V8 sürümünde yönlendirme Haritası sıralama hatasını çözme
* Kullanıcı kısmi yeniden deneme seçenekleri sağlarsa hatayı düzeltir

### <a name="3.0.3"/>3.0.3</a>
* WebPack 'in gerektir ile çağrılan modülleri çözmesini engelle

### <a name="3.0.2"/>3.0.2</a>
* , RUs 'in toplam sorgu için her zaman 0 olarak bildirildiği, uzun bir bekleyen hatayı düzeltir

### <a name="3.0.0"/>3.0.0</a>

🎉 v3 sürümü! birçok yeni özellik, hata düzeltmesi ve birkaç Son değişiklik 🎉. Bu sürümün birincil amaçları:

* Önemli yeni özellikleri Uygula
  * AYRı sorgular
  * Sorguları SıNıRLA/KAYıN
  * Kullanıcı tarafından iptal edilebilen istekler
* Tüm kapsayıcıların sınırsız ölçeği olduğu en son Cosmos REST API sürümüne güncelleştirin
* Tarayıcıdan Cosmos kullanmayı kolaylaştırın
* Yeni Azure JS SDK yönergeleriyle daha iyi uyum

#### <a name="migration-guide-for-breaking-changes"></a>Son değişiklikler için geçiş kılavuzu
##### <a name="improved-client-constructor-options"></a>Geliştirilmiş istemci Oluşturucu seçenekleri

Oluşturucu seçenekleri basitleştirildi:

* masterKey anahtarı yeniden adlandırıldı ve üst düzeye taşındı
* Daha önce Options. auth altında bulunan özellikler en üst düzeye taşındı

``` js
// v2
const client = new CosmosClient({
    endpoint: "https://your-database.cosmos.azure.com",
    auth: {
        masterKey: "your-primary-key"
    }
})

// v3
const client = new CosmosClient({
    endpoint: "https://your-database.cosmos.azure.com",
    key: "your-primary-key"
})
```

##### <a name="simplified-queryiterator-api"></a>Basitleştirilmiş Queryıterator API 'SI
V2 'de bir sorgudan sonuçları yinelemek veya almak için birçok farklı yol vardı. V3 API 'YI basitleştirmeye ve benzer veya yinelenen API 'Leri kaldırmaya çalıştık:

* Yineleyici. Next () ve yineleyici. Current () öğesini kaldırın. Sonuçların sayfalarını almak için fetchNext () kullanın.
* Yineleyici. forEach () öğesini kaldırın. Bunun yerine zaman uyumsuz yineleyiciler kullanın.
* Yineleyici. executeNext () Yineleyici. fetchNext () olarak yeniden adlandırıldı
* Yineleyici. toArray () Yineleyici. fetchAll () olarak yeniden adlandırıldı
* Sayfalar artık düz JS nesneleri yerine doğru yanıt nesneleridir
* const Container = Client. Database (DBID). Container (Containerıd)

``` js
// v2
container.items.query('SELECT * from c').toArray()
container.items.query('SELECT * from c').executeNext()
container.items.query('SELECT * from c').forEach(({ body: item }) => { console.log(item.id) })

// v3
container.items.query('SELECT * from c').fetchAll()
container.items.query('SELECT * from c').fetchNext()
for await(const { result: item } in client.databases.readAll().getAsyncIterator()) {
    console.log(item.id)
}
```

##### <a name="fixed-containers-are-now-partitioned"></a>Sabit kapsayıcılar artık bölümlenmiş
Cosmos hizmeti artık, daha önce sabit kapsayıcılar olarak oluşturulanlar dahil olmak üzere tüm kapsayıcılardaki bölüm anahtarlarını desteklemektedir. V3 SDK, bu değişikliği uygulayan en son API sürümüne güncelleştirir, ancak bu, kırıdeğildir. İşlemler için bir bölüm anahtarı belirtmezseniz, varsayılan olarak tüm mevcut kapsayıcılarınız ve belgeleriniz ile çalışacak bir sistem anahtarı kullanacağız.

##### <a name="upsert-removed-for-stored-procedures"></a>Saklı yordamlar için upsert kaldırıldı
Bölümlenmemiş koleksiyonlar için daha önce upsert olarak izin verildi, ancak API sürümü güncelleştirmesiyle, tüm koleksiyonlar tamamen kaldırdık.

##### <a name="item-reads-will-not-throw-on-404"></a>Öğe okuma 404 üzerinde throw
const Container = Client. Database (DBID). Container (Containerıd)

``` js
// v2
try {
    container.items.read(id, undefined)
} catch (e) {
    if (e.code === 404) { console.log('item not found') }
}

// v3
const { result: item }  = container.items.read(id, undefined)
if (item === undefined) { console.log('item not found') }
```

##### <a name="default-multi-region-write"></a>Varsayılan çok bölgeli yazma
SDK, Cosmos yapılandırmanız destekliyorsa, artık varsayılan olarak birden çok bölgeye yazar. Bu, önceden kabul etme davranışıdır.

##### <a name="proper-error-objects"></a>Uygun hata nesneleri
Başarısız istekler artık doğru hata veya hata alt sınıfları oluşturur. Daha önce düz JS nesneleri oluşturuyordu.

#### <a name="new-features"></a>Yeni Özellikler
##### <a name="user-cancelable-requests"></a>Kullanıcı tarafından iptal edilebilen istekler
Dahili olarak getirilecek taşıma, Kullanıcı tarafından iptal edilebilen işlemleri desteklemek için AbortController API 'sini kullanmamızı sağlar. Birden çok isteğin devam ettiği (çapraz bölüm sorguları gibi) işlemler söz konusu olduğunda, işlem için tüm istekler iptal edilir. Modern tarayıcı kullanıcıları, zaten AbortController 'a sahip olur. Node. js kullanıcılarının polyfill kitaplığı kullanması gerekir

``` js
 const controller = new AbortController()
 const {result: item} = await items.query('SELECT * from c', { abortSignal: controller.signal});
 controller.abort()
```

##### <a name="set-throughput-as-part-of-dbcontainer-create-operation"></a>Veritabanı/kapsayıcı oluşturma işleminin parçası olarak üretilen işi ayarla
``` js
const { database }  = client.databases.create({ id: 'my-database', throughput: 10000 })
database.containers.create({ id: 'my-container', throughput: 10000 })
```

##### <a name="azurecosmos-sign"></a>@azure/cosmos-sign
Üstbilgi belirteci oluşturma yeni bir kitaplığa @azure/cosmos-signbölündü. Cosmos REST API çağıran herkes, içinde @azure/cosmosçağırdığımız aynı kodu kullanarak üstbilgileri imzalamak için bunu kullanabilir.

##### <a name="uuid-for-generated-ids"></a>Oluşturulan kimlikler için UUID
v2 öğe kimliklerini oluşturmak için özel koda sahipti. İyi bilinen ve korunan topluluk kitaplığı UUID 'sine geçtik.

##### <a name="connection-strings"></a>Bağlantı dizeleri
Artık Azure portal kopyalanmış bir bağlantı dizesinin iletilmesi mümkündür:

``` js
const client = new CosmosClient("AccountEndpoint=https://test-account.documents.azure.com:443/;AccountKey=c213asdasdefgdfgrtweaYPpgoeCsHbpRTHhxuMsTaw==;")
Add DISTINCT and LIMIT/OFFSET queries (#306)
 const { results } = await items.query('SELECT DISTINCT VALUE r.name FROM ROOT').fetchAll()
 const { results } = await items.query('SELECT * FROM root r OFFSET 1 LIMIT 2').fetchAll()
```

#### <a name="improved-browser-experience"></a>Geliştirilmiş tarayıcı deneyimi
Tarayıcıda v2 SDK kullanılması mümkün olsa da ideal bir deneyim değildir. Çeşitli Node. js yerleşik kitaplıklarını çoklu olarak doldurmanız ve WebPack veya paket gibi bir paketler kullanmanız gerekir. V3 SDK, kullanıma hazır deneyimin, tarayıcı kullanıcıları için çok daha iyi olmasını sağlar.

* İstek iç yapıları Fetch ile değiştirme (#245)
* Arabellek kullanımını Kaldır (#330)
* Evrensel paketler/API 'Ler (#328) için kullanılan düğüm yerleşik kullanımını kaldır
* Düğüme geç-Abort-Controller (#294)

#### <a name="bug-fixes"></a>Hata düzeltmeleri
* Teklif okuma ve geri getirme teklif testlerini düzeltir (#224)
* EnableEndpointDiscovery 'ı (#207) düzeltir
* Sayfalandırılmış sonuçlar üzerinde eksik RUs 'yi çözme (#360)
* SQL sorgu parametresi türünü genişlet (#346)
* TTL 'yi ItemDefinition (#341) öğesine ekleyin
* CP sorgu ölçümlerini çözme (#311)
* FeedResponse 'a ActivityId ekleyin (#293)
* _Ts türü dizeden sayıya (#252) geç (#295)
* Istek ücreti toplamasını çözme (#289)
* Boş dize bölüm anahtarlarına izin ver (#277)
* Çakışma sorgu türüne dize Ekle (#237)
* Kapsayıcıya uniqueKeyPolicy Ekle (#234)

#### <a name="engineering-systems"></a>Mühendislik sistemleri
Her zaman en görünür değişiklikler değildir, ancak ekibimizin daha hızlı bir şekilde daha fazla kod göndermesine yardımcı olurlar.

* Üretim derlemeleri için toplu kullanım (#104)
* TypeScript 3,5 Güncelleştirmesi (#327)
* TS proje başvurularına Dönüştür. Test klasörünü Ayıkla (#270)
* Enable noUnusedLocals ve noUnusedParameters (#275)
* CI derlemeler için Azure Pipelines YAML (#298)

### <a name="2.1.5"/>2.1.5</a>
* Kod değişikliği yok. 2\.1.4 paketine bazı ek dosyaların dahil edildiği bir sorunu düzeltir.

### <a name="2.1.4"/>2.1.4</a>
* Yeniden deneme ilkesi içinde bölgesel yük devretmeyi Düzeltme
* ChangeFeed hasMoreResults özelliğini düzeltir
* Geliştirme bağımlılığı güncelleştirmeleri
* Policheckdışlamalar. txt ekleyin

### <a name="2.1.3"/>2.1.3</a>
* _Ts türünü dizeden sayıya geçir
* Varsayılan dizin oluşturma testlerini çözme
* Arka bağlantı noktası uniqueKeyPolicy-v2
* Tanıtım ve tanıtım hata ayıklama düzeltmeleri

### <a name="2.1.2"/>2.1.2'yi</a>
* V3 dalından geri bağlantı için düzeltme sunma
* ExecuteNext () türü imzasında hata giderme
* Typo düzeltmeleri

### <a name="2.1.1"/>2.1.1</a>
* Derleme yeniden yapılandırma. Derleme zamanında SDK sürümünün çekmesini sağlar.

### <a name="2.1.0"/>2.1.0</a>
#### <a name="new-features"></a>Yeni Özellikler
* Değişiklik akışı desteği eklendi (#196)
* Dizin oluşturma için MultiPolygon DataType eklendi (#191)
* MasterKey (#202) için diğer ad olarak oluşturucuya "Key" özelliği ekleyin

#### <a name="fixes"></a>Düzeltmeleri
* Next () ' in Yineleyici üzerinde hatalı değer döndürmesi durumunda hata düzeltildi

#### <a name="engineering-improvements"></a>Mühendislik geliştirmeleri
* TypeScript tüketim için tümleştirme testi ekleme (#199)
* Doğrudan GitHub 'dan yüklemeyi etkinleştir (#194)

### <a name="2.0.5"/>2.0.5</a>
* Düğüm Aracısı türü için arabirim ekler. TypeScript kullanıcılarının artık bağımlılık olarak yüklenmesi @types/node gerekmez
* Tercih edilen konumlar artık düzgün şekilde kabul edilir
* Katkıda bulunan geliştirici belgelerine yönelik iyileştirmeler
* Çeşitli yazım hatası düzeltmeleri

### <a name="2.0.4"/>2.0.4</a>
* 2\.0.3 içinde tanıtılan düzeltme türü tanım sorunu

### <a name="2.0.3"/>2.0.3</a>
* Bağımlılığı `big-integer` kaldır
* Asyncıtik tür için başvuru yönergelerine geçin. TypeScript kullanıcıları artık "lib" ayarını özelleştirmek zorunda değildir.
* Typo düzeltmeleri

### <a name="2.0.2"/>2.0.2</a>
* Benioku bağlantılarını çözme

### <a name="2.0.1"/>2.0.1</a>
* Yeniden deneme arabirimi uygulamasını onarma

### <a name="2.0.0"/>2.0.0</a>
* JavaScript SDK 'sının 2.0.0 sürümü
* Çok bölgeli yazma desteği eklendi.

### <a name="2.0.0-3"/>2.0.0-3</a>
* Genel önizleme için JavaScript SDK 'sının 2.0.0 sürümü.
* En üst düzey CosmosClient ve yöntemlerle ilgili veritabanı, kapsayıcı ve öğe sınıflarında bölünen yeni nesne modeli. 
* [Taahhüt](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises)desteği. 
* SDK, TypeScript 'e dönüştürüldü.

### <a name="1.14.4"/>1.14.4</a>
* NPM belgeleri düzeltildi.

### <a name="1.14.3"/>1.14.3</a>
* Bağlantı sorunlarında varsayılan yeniden denemeler için destek eklendi.
* Koleksiyon değişikliği akışını okuma desteği eklendi.
* Zaman zaman "okuma oturumu kullanılamıyor" hatası veren sabit oturum tutarlılığı hatası.
* Sorgu ölçümleri için destek eklendi.
* Değiştirilen http aracısının bağlantı sayısı üst sınırı.

### <a name="1.14.2"/>1.14.2</a>
* Belgeler, Azure DocumentDB yerine Azure Cosmos DB başvuracak şekilde güncelleştirildi.
* ConnectionPolicy içindeki proxyUrl ayarı için destek eklendi.

### <a name="1.14.1"/>1.14.1</a>
* Büyük/küçük harfe duyarlı dosya sistemleri için küçük bir çözüm.

### <a name="1.14.0"/>1.14.0</a>
* Oturum tutarlılığı için destek ekler.
* Bu SDK sürüm Merkezi'nden Azure Cosmos DB Emulator kullanılabilir en son sürümünü gerektirir. https://aka.ms/cosmosdb-emulator.

### <a name="1.13.0"/>1.13.0</a>
* Redaked çapraz bölüm sorgularını Böl.
* Baştaki ve sondaki eğik çizgi (ve karşılık gelen testler) ile kaynak bağlantısı için destekler.

### <a name="1.12.2"/>1.12.2</a>
*   NPM belgeleri düzeltildi.

### <a name="1.12.1"/>1.12.1</a>
* Özel Unicode karakterlerine (LS, PS) sahip olan executeStoredProcedure içindeki bir hata düzeltildi.
* Bölüm anahtarında Unicode karakterlerle belge işlemede hata düzeltildi.
* Ad medyası ile koleksiyon oluşturmak için sabit destek. GitHub sorunu #114.
* İzin yetkilendirme belirteci için sabit destek. GitHub sorunu #178.

### <a name="1.12.0"/>1.12.0</a>
* Tutarlık ön eki olarak adlandırılan yeni bir [tutarlılık düzeyi](consistency-levels.md) için destek eklendi.
* UriFactory için destek eklendi.
* Unicode desteği hatası düzeltildi. GitHub sorunu #171.

### <a name="1.11.0"/>1.11.0</a>
* Toplama sorguları (COUNT, MIN, MAX, SUM ve AVG) için destek eklendi.
* Çapraz bölüm sorguları için paralellik derecesini denetleme seçeneği eklendi.
* Azure Cosmos DB öykünücüsüyle çalışırken SSL doğrulamasını devre dışı bırakma seçeneği eklendi.
* Bölümlenmiş koleksiyonlardan 10,100 RU/sn 2500 RU/sn için en düşük aktarım hızını düşürdü.
* Tek bölümlü koleksiyon için devamlılık belirteci hatası düzeltildi. GitHub sorunu #107.
* Tek param olarak 0 işlenirken executeStoredProcedure hatası düzeltildi. GitHub sorunu #155.

### <a name="1.10.2"/>1.10.2</a>
* SDK sürümünü dahil etmek için sabit Kullanıcı Aracısı üst bilgisi.
* Küçük kod temizleme.

### <a name="1.10.1"/>1.10.1</a>
* Öykünücüyü hedeflemek için SDK kullanılırken SSL doğrulaması devre dışı bırakılıyor (hostname = localhost).
* Saklı yordam yürütülürken betik günlüğü tutmayı etkinleştirme desteği eklendi.

### <a name="1.10.0"/>1.10.0</a>
* Çapraz bölüm paralel sorgular için eklenen destek.
* ÜST/ORDER BY sorguları bölümlenmiş koleksiyonlar için destek eklendi.

### <a name="1.9.0"/>1.9.0</a>
* Kısıtlanmış istekler için yeniden deneme ilkesi desteği eklendi. (Kısıtlanan istekler çok büyük bir istek hızı alıyor, hata kodu 429.) Varsayılan olarak Azure Cosmos DB, 429 hata kodu ile karşılaşıldığında her bir istek için dokuz kez yeniden dener ve yanıt üst bilgisinde retryAfter saati belirlenir. Yeniden denemeler arasında sunucu tarafından döndürülen retryAfter süresini yoksaymak istiyorsanız, artık, ConnectionPolicy nesnesindeki RetryOptions özelliğinin bir parçası olarak sabit bir yeniden deneme aralığı zaman ayarlanabilir. Azure Cosmos DB, kısıtlanmış olan her istek için en fazla 30 saniye bekler (yeniden deneme sayısı ne olursa olsun) ve 429 hata koduyla yanıtı döndürür. Bu zaman, ConnectionPolicy nesnesindeki RetryOptions özelliğinde de geçersiz kılınabilir.
* Cosmos DB, kısıtlama yeniden deneme sayısını ve isteğin yeniden denemeler arasında bekleyeceği kümülatif süreyi belirtmek için her istekteki yanıt üst bilgileri olarak x-MS-azaltma-retry-Count ve x-MS-azaltma-retry-WAIT-Time-MS döndürür.
* RetryOptions sınıfı eklenmiştir ve bu, varsayılan yeniden deneme seçeneklerinden bazılarını geçersiz kılmak için kullanılabilecek ConnectionPolicy sınıfında RetryOptions özelliğini kullanıma sunma.

### <a name="1.8.0"/>1.8.0</a>
* Çoklu bölge veritabanı hesapları için destek eklendi.

### <a name="1.7.0"/>1.7.0</a>
* Belgeler için yaşam süresi (TTL) özelliğine yönelik destek eklendi.

### <a name="1.6.0"/>1.6.0</a>
* Uygulanan [bölümlenmiş koleksiyonları](partition-data.md) ve [kullanıcı tanımlı performans düzeyleri](performance-levels.md).

### <a name="1.5.6"/>1.5.6</a>
* Hatalı bir sonuçların bir Concat nedeniyle bağlantıların döndürülmediğinden düzeltilen RangePartitionResolver. resolveForRead hatası.

### <a name="1.5.5"/>1.5.5</a>
* Sabit hashPartitionResolver resolveForRead (): Sağlanan hiçbir bölüm anahtarı, kayıtlı tüm bağlantıların bir listesini döndürmek yerine özel durum oluşturmadıysa.

### <a name="1.5.4"/>1.5.4</a>
* [#100](https://github.com/Azure/azure-documentdb-node/issues/100) -adanmış https Aracısı sorunu giderilir: Azure Cosmos DB amacıyla genel aracıyı değiştirmekten kaçının. LIB 'in tüm istekleri için adanmış bir aracı kullanın.

### <a name="1.5.3"/>1.5.3</a>
* Düzeltmeler [#81](https://github.com/Azure/azure-documentdb-node/issues/81) sorun-medya kimliklerinde düzgün şekilde işleme çizgileri.

### <a name="1.5.2"/>1.5.2</a>
* [#95](https://github.com/Azure/azure-documentdb-node/issues/95) -eventemitter dinleyicisi sızıntısı uyarısı sorununu düzeltir.

### <a name="1.5.1"/>1.5.1</a>
* Sorun düzeltme [#92](https://github.com/Azure/azure-documentdb-node/issues/90) , büyük/küçük harfe duyarlı sistemler Için klasör karmasını karma olarak yeniden adlandırın.

### <a name="1.5.0"/>1.5.0</a>
* Karma & aralığı bölüm çözümleyicilerini ekleyerek parçalama desteğini uygulayın.

### <a name="1.4.0"/>1.4.0</a>
* Upsert uygulayın. DocumentClient üzerinde yeni upsertXXX yöntemleri.

### <a name="1.3.0"/>1.3.0</a>
* Sürüm numaralarını diğer SDK 'lara hizalı olarak getirmek için atlandı.

### <a name="1.2.2"/>1.2.2</a>
* Soru-cevap sarmalayıcısı yeni depoya bölünür.
* Npm kayıt defteri için paket dosyasına güncelleştir.

### <a name="1.2.1"/>1.2.1</a>
* KIMLIK tabanlı yönlendirmeyi uygular.
* [#49](https://github.com/Azure/azure-documentdb-node/issues/49) sorunu düzeltmeler-geçerli özellik geçerli () yöntemiyle çakışıyor.

### <a name="1.2.0"/>1.2.0</a>
* Jeo-uzamsal dizin desteği eklendi.
* Tüm kaynaklar için kimlik özelliği doğrular. Kaynak kimlikleri,?,/, #, &#47; &#47;, karakter içeremez veya boşluk ile bitemez.
* Yeni üst bilgi "dizin dönüştürme ilerleme durumu" için ResourceResponse ekler.

### <a name="1.1.0"/>1.1.0</a>
* V2 dizin oluşturma ilkesini uygular.

### <a name="1.0.3"/>1.0.3</a>
* Çekirdek ve Promise SDK 'sında [#40](https://github.com/Azure/azure-documentdb-node/issues/40) uygulanmış eslint ve grönlama yapılandırması sorun.

### <a name="1.0.2"/>1.0.2</a>
* Sorun [#45](https://github.com/Azure/azure-documentdb-node/issues/45) -taahhüt sarmalayıcısı, hata içeren üst bilgi içermez.

### <a name="1.0.1"/>1.0.1</a>
* ReadConflicts, readConflictAsync ve queryConflicts ekleyerek çakışmaları sorgulama özelliği uygulandı.
* API belgeleri güncelleştirildi.
* [#41](https://github.com/Azure/azure-documentdb-node/issues/41) -Client. Createbelgetasync hatası verme.

### <a name="1.0.0"/>1.0.0</a>
* GA SDK.

## <a name="release--retirement-dates"></a>Yayın ve sona erme tarihlerini
Microsoft, daha yeni/desteklenen bir sürüme geçişi düzgünleştirmek için SDK 'nın devre dışı bırakılmasının ardından en az **12 ay** önce bildirim sağlar.

Yeni özellikler ve işlevler ve iyileştirmeler yalnızca geçerli SDK 'ya eklenir, bu nedenle en son SDK sürümüne her zaman olabildiğince erken yükseltmeniz önerilir.

Cosmos DB devre dışı bırakılan bir SDK'sını kullanarak yapılan tüm istekleri hizmet tarafından reddedilir.

> [!WARNING]
> SQL API için düğüm istemci SDK 'sının tüm **1. x** sürümleri **30 Ağustos 2020**tarihinde kullanımdan kaldırılacaktır. Bu, yalnızca istemci tarafı düğüm SDK 'sını etkiler ve sunucu tarafı betikleri (saklı yordamlar, Tetikleyiciler ve UDF 'ler) etkilemez.
> 
>
<br/>

| Version | Yayınlanma Tarihi | Sona erme tarihi |
| --- | --- | --- |
| [3.1.0](#3.1.0) |26 Temmuz 2019 |--- |
| [3.0.4](#3.0.4) |22 Temmuz 2019 |--- |
| [3.0.3](#3.0.3) |17 Temmuz 2019 |--- |
| [3.0.2](#3.0.2) |9 Temmuz 2019 |--- |
| [3.0.0](#3.0.0) |28 Haziran 2019 |--- |
| [2.1.5](#2.1.5) |20 Mart 2019 |--- |
| [2.1.4](#2.1.4) |15 Mart 2019 |--- |
| [2.1.3](#2.1.3) |8 Mart 2019 |--- |
| [2.1.2'yi](#2.1.2) |28 Ocak 2019 |--- |
| [2.1.1](#2.1.1) |5 Aralık 2018 |--- |
| [2.1.0](#2.1.0) |4 Aralık 2018'e |--- |
| [2.0.5](#2.0.5) |7 Kasım 2018 |--- |
| [2.0.4](#2.0.4) |30 Ekim 2018 |--- |
| [2.0.3](#2.0.3) |30 Ekim 2018 |--- |
| [2.0.2](#2.0.2) |10 Ekim 2018 |--- |
| [2.0.1](#2.0.1) |25 Eylül 2018 |--- |
| [2.0.0](#2.0.0) |24 Eylül 2018 |--- |
| [2.0.0-3 (RC)](#2.0.0-3) |2 Ağustos 2018 |--- |
| [1.14.4](#1.14.4) |03 Mayıs, 2018 |30 Ağustos 2020 |
| [1.14.3](#1.14.3) |03 Mayıs, 2018 |30 Ağustos 2020 |
| [1.14.2](#1.14.2) |21 Aralık 2017 |30 Ağustos 2020 |
| [1.14.1](#1.14.1) |10 Kasım 2017 |30 Ağustos 2020 |
| [1.14.0](#1.14.0) |9 Kasım 2017 |30 Ağustos 2020 |
| [1.13.0](#1.13.0) |11 Ekim 2017 |30 Ağustos 2020 |
| [1.12.2](#1.12.2) |10 Ağustos 2017 |30 Ağustos 2020 |
| [1.12.1](#1.12.1) |10 Ağustos 2017 |30 Ağustos 2020 |
| [1.12.0](#1.12.0) |10 Mayıs 2017 |30 Ağustos 2020 |
| [1.11.0](#1.11.0) |16 Mart 2017 |30 Ağustos 2020 |
| [1.10.2](#1.10.2) |27 Ocak 2017 |30 Ağustos 2020 |
| [1.10.1](#1.10.1) |22 Aralık 2016 |30 Ağustos 2020 |
| [1.10.0](#1.10.0) |03 Ekim 2016 |30 Ağustos 2020 |
| [1.9.0](#1.9.0) |07 Temmuz 2016 |30 Ağustos 2020 |
| [1.8.0](#1.8.0) |14 Haziran 2016 |30 Ağustos 2020 |
| [1.7.0](#1.7.0) |26 Nisan 2016 |30 Ağustos 2020 |
| [1.6.0](#1.6.0) |29 Mart 2016 |30 Ağustos 2020 |
| [1.5.6](#1.5.6) |08 Mart 2016 |30 Ağustos 2020 |
| [1.5.5](#1.5.5) |02 Şubat 2016 |30 Ağustos 2020 |
| [1.5.4](#1.5.4) |01 Şubat 2016 |30 Ağustos 2020 |
| [1.5.2](#1.5.2) |26 Ocak 2016 |30 Ağustos 2020 |
| [1.5.2](#1.5.2) |22 Ocak 2016 |30 Ağustos 2020 |
| [1.5.1](#1.5.1) |4 Ocak 2016 |30 Ağustos 2020 |
| [1.5.0](#1.5.0) |31 Aralık 2015 |30 Ağustos 2020 |
| [1.4.0](#1.4.0) |06 Ekim 2015 |30 Ağustos 2020 |
| [1.3.0](#1.3.0) |06 Ekim 2015 |30 Ağustos 2020 |
| [1.2.2](#1.2.2) |10 Eylül 2015 |30 Ağustos 2020 |
| [1.2.1](#1.2.1) |15 Ağustos 2015 |30 Ağustos 2020 |
| [1.2.0](#1.2.0) |05 Ağustos 2015 |30 Ağustos 2020 |
| [1.1.0](#1.1.0) |09 Temmuz 2015 |30 Ağustos 2020 |
| [1.0.3](#1.0.3) |04 Haziran 2015 |30 Ağustos 2020 |
| [1.0.2](#1.0.2) |23 Mayıs 2015 |30 Ağustos 2020 |
| [1.0.1](#1.0.1) |15 Mayıs 2015 |30 Ağustos 2020 |
| [1.0.0](#1.0.0) |08 Nisan 2015 |30 Ağustos 2020 |

## <a name="faq"></a>SSS
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Ayrıca bkz.
Cosmos DB hakkında daha fazla bilgi için bkz: [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) hizmeti sayfası.

