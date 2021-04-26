---
title: 'Azure Cosmos DB: SQL Node.js API, SDK & kaynakları'
description: Sürüm tarihleri, emeklilik tarihleri ve Azure Cosmos DB Node.js SDK 'nın her sürümü arasında yapılan değişiklikler dahil olmak üzere SQL Node.js API 'SI ve SDK hakkında bilgi edinin.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: reference
ms.date: 04/06/2021
ms.author: anfeldma
ms.custom: devx-track-js
ms.openlocfilehash: a3e21abe2f4ed24726256689af16b48ed6721ce8
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107366156"
---
# <a name="azure-cosmos-db-nodejs-sdk-for-sql-api-release-notes-and-resources"></a>SQL API için Azure Cosmos DB Node.js SDK: sürüm notları ve kaynakları
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]
> [!div class="op_single_selector"]
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [.NET Core SDK v2](sql-api-sdk-dotnet-core.md)
> * [.NET Değişiklik Akışı SDK'sı v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK’sı v4](sql-api-sdk-java-v4.md)
> * [Zaman uyumsuz Java SDK v2](sql-api-sdk-async-java.md)
> * [Zaman uyumlu Java SDK v2](sql-api-sdk-java.md)
> * [Spring Verileri v2](sql-api-sdk-java-spring-v2.md)
> * [Spring Verileri v3](sql-api-sdk-java-spring-v3.md)
> * [Spark 3 OLTP Bağlayıcısı](sql-api-sdk-java-spark-v3.md)
> * [Spark 2 OLTP Bağlayıcısı](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [REST kaynak sağlayıcısı](/rest/api/cosmos-db-resource-provider/)
> * [SQL](./sql-query-getting-started.md)
> * [Toplu yürütücü - .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Toplu yürütücü - Java](sql-api-sdk-bulk-executor-java.md)

|Kaynak  |Bağlantı  |
|---------|---------|
|SDK 'Yı indir  |   [NPM](https://www.npmjs.com/package/@azure/cosmos) 
|API Belgeleri  |  [JavaScript SDK başvuru belgeleri](/javascript/api/%40azure/cosmos/)
|SDK yükleme yönergeleri  |  [Yükleme yönergeleri](https://github.com/Azure/azure-sdk-for-js)
|SDK 'ya katkıda bulunma | [GitHub](https://github.com/Azure/azure-cosmos-js/tree/master)
| Örnekler | [Node.js kodu örnekleri](sql-api-nodejs-samples.md)
| Başlangıç Öğreticisi | [JavaScript SDK 'sını kullanmaya başlama](sql-api-nodejs-get-started.md)
| Web uygulaması öğreticisi | [Azure Cosmos DB kullanarak Node.js Web uygulaması oluşturma](sql-api-nodejs-application.md)
| Desteklenen geçerli platform | [Node.js v12. x](https://nodejs.org/en/blog/release/v12.7.0/) -SDK sürüm 3. x. x<br/>[Node.js ile v10 arasındaki. x](https://nodejs.org/en/blog/release/v10.6.0/) -SDK sürüm 3. x. x<br/>[Node.js V8. x](https://nodejs.org/en/blog/release/v8.16.0/) -SDK sürüm 3. x. x<br/>[Node.js V6. x](https://nodejs.org/en/blog/release/v6.10.3/) -SDK sürüm 2. x. x<br/>[Node.js v 4.2.0](https://nodejs.org/en/blog/release/v4.2.0/)-SDK sürüm 1. x. x<br/> [Node.js v 0.12](https://nodejs.org/en/blog/release/v0.12.0/)-SDK sürüm 1. x. x<br/> [Node.js v 0.10](https://nodejs.org/en/blog/release/v0.10.0/)-SDK sürüm 1. x. x

## <a name="release-notes"></a>Sürüm notları

### <a name="310"></a><a name="3.1.0"></a>3.1.0
* Varsayılan Responsecontinuationtokenlimitınkb değerini 1kb olarak ayarlayın. Varsayılan olarak, uzun başlıkları önlemek için bunu 1 KB 'lık 'ye kadık (Node.js bir genel üst bilgi boyutu sınırına sahiptir). Bir Kullanıcı bu alanı daha uzun üst bilgiler için izin verecek şekilde ayarlayabilir, bu da arka ucun sorgu yürütmesini iyileştirmenize yardımcı olabilir.
* Disablessldoğrulaması 'nı kaldırın. Bu seçeneğin [#388](https://github.com/Azure/azure-cosmos-js/pull/388) açıklanan yeni alternatifleri vardır

### <a name="304"></a><a name="3.0.4"></a>3.0.4
* Initialheaders 'in bölüm anahtarı üst bilgisini açıkça ayarlamaya izin ver
* Gereksiz dosyaların yayımlanmasını engellemek için # Files üzerinde package.jskullanın
* Daha eski node + V8 sürümünde yönlendirme Haritası sıralama hatasını çözme
* Kullanıcı kısmi yeniden deneme seçenekleri sağlarsa hatayı düzeltir

### <a name="303"></a><a name="3.0.3"></a>3.0.3
* WebPack 'in gerektir ile çağrılan modülleri çözmesini engelle

### <a name="302"></a><a name="3.0.2"></a>3.0.2
* , RUs 'in toplam sorgu için her zaman 0 olarak bildirildiği, uzun bir bekleyen hatayı düzeltir

### <a name="300"></a><a name="3.0.0"></a>3.0.0

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
* iterator.executeNext () Yineleyici. fetchNext () olarak yeniden adlandırıldı
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

#### <a name="new-features"></a>Yeni özellikler
##### <a name="user-cancelable-requests"></a>Kullanıcı tarafından iptal edilebilen istekler
Dahili olarak getirilecek taşıma, Kullanıcı tarafından iptal edilebilen işlemleri desteklemek için AbortController API 'sini kullanmamızı sağlar. Birden çok isteğin devam ettiği (çapraz bölüm sorguları gibi) işlemler söz konusu olduğunda, işlem için tüm istekler iptal edilir. Modern tarayıcı kullanıcıları, zaten AbortController 'a sahip olur. Node.js kullanıcıların polyfill kitaplığı kullanması gerekir

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
Üstbilgi belirteci oluşturma yeni bir kitaplığa bölündü @azure/cosmos-sign . Cosmos REST API çağıran herkes, içinde çağırdığımız aynı kodu kullanarak üstbilgileri imzalamak için bunu kullanabilir @azure/cosmos .

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
Tarayıcıda v2 SDK kullanılması mümkün olsa da ideal bir deneyim değildir. Birden çok node.js yerleşik kitaplığı çoklu olarak doldurmanız ve WebPack veya paket gibi bir paketler kullanmanız gerekir. V3 SDK, kullanıma hazır deneyimin, tarayıcı kullanıcıları için çok daha iyi olmasını sağlar.

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
* Dizeden sayıya _ts türü (#252) (#295)
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

### <a name="215"></a><a name="2.1.5"></a>2.1.5
* Kod değişikliği yok. 2.1.4 paketine bazı ek dosyaların dahil edildiği bir sorunu düzeltir.

### <a name="214"></a><a name="2.1.4"></a>2.1.4
* Yeniden deneme ilkesi içinde bölgesel yük devretmeyi Düzeltme
* ChangeFeed hasMoreResults özelliğini düzeltir
* Geliştirme bağımlılığı güncelleştirmeleri
* PolicheckExclusions.txt Ekle

### <a name="213"></a><a name="2.1.3"></a>2.1.3
* Dizeden sayıya _ts türü Değiştir
* Varsayılan dizin oluşturma testlerini çözme
* Arka bağlantı noktası uniqueKeyPolicy-v2
* Tanıtım ve tanıtım hata ayıklama düzeltmeleri

### <a name="212"></a><a name="2.1.2"></a>2.1.2
* V3 dalından geri bağlantı için düzeltme sunma
* ExecuteNext () türü imzasında hata giderme
* Typo düzeltmeleri

### <a name="211"></a><a name="2.1.1"></a>2.1.1
* Derleme yeniden yapılandırma. Derleme zamanında SDK sürümünün çekmesini sağlar.

### <a name="210"></a><a name="2.1.0"></a>2.1.0
#### <a name="new-features"></a>Yeni Özellikler
* Değişiklik akışı desteği eklendi (#196)
* Dizin oluşturma için MultiPolygon DataType eklendi (#191)
* MasterKey (#202) için diğer ad olarak oluşturucuya "Key" özelliği ekleyin

#### <a name="fixes"></a>Düzeltmeler
* Next () ' in Yineleyici üzerinde hatalı değer döndürmesi durumunda hata düzeltildi

#### <a name="engineering-improvements"></a>Mühendislik geliştirmeleri
* TypeScript tüketim için tümleştirme testi ekleme (#199)
* Doğrudan GitHub 'dan yüklemeyi etkinleştir (#194)

### <a name="205"></a><a name="2.0.5"></a>2.0.5
* Düğüm Aracısı türü için arabirim ekler. TypeScript kullanıcılarının artık bağımlılık olarak yüklenmesi gerekmez @types/node
* Tercih edilen konumlar artık düzgün şekilde kabul edilir
* Katkıda bulunan geliştirici belgelerine yönelik iyileştirmeler
* Çeşitli yazım hatası düzeltmeleri

### <a name="204"></a><a name="2.0.4"></a>2.0.4
* 2.0.3 içinde tanıtılan düzeltme türü tanım sorunu

### <a name="203"></a><a name="2.0.3"></a>2.0.3
* `big-integer`Bağımlılığı kaldır
* Asyncıtik tür için başvuru yönergelerine geçin. TypeScript kullanıcıları artık "lib" ayarını özelleştirmek zorunda değildir.
* Typo düzeltmeleri

### <a name="202"></a><a name="2.0.2"></a>2.0.2
* Benioku bağlantılarını çözme

### <a name="201"></a><a name="2.0.1"></a>2.0.1
* Yeniden deneme arabirimi uygulamasını onarma

### <a name="200"></a><a name="2.0.0"></a>2.0.0
* JavaScript SDK 'sının 2.0.0 sürümü
* Çok bölgeli yazma desteği eklendi.

### <a name="200-3"></a><a name="2.0.0-3"></a>2.0.0-3
* Genel önizleme için JavaScript SDK 'sının 2.0.0 sürümü.
* En üst düzey CosmosClient ve yöntemlerle ilgili veritabanı, kapsayıcı ve öğe sınıflarında bölünen yeni nesne modeli. 
* [Taahhüt](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises)desteği. 
* SDK, TypeScript 'e dönüştürüldü.

### <a name="1144"></a><a name="1.14.4"></a>1.14.4
* NPM belgeleri düzeltildi.

### <a name="1143"></a><a name="1.14.3"></a>1.14.3
* Bağlantı sorunlarında varsayılan yeniden denemeler için destek eklendi.
* Koleksiyon değişikliği akışını okuma desteği eklendi.
* Zaman zaman "okuma oturumu kullanılamıyor" hatası veren sabit oturum tutarlılığı hatası.
* Sorgu ölçümleri için destek eklendi.
* Değiştirilen http aracısının bağlantı sayısı üst sınırı.

### <a name="1142"></a><a name="1.14.2"></a>1.14.2
* Belgeler, Azure DocumentDB yerine Azure Cosmos DB başvuracak şekilde güncelleştirildi.
* ConnectionPolicy içindeki proxyUrl ayarı için destek eklendi.

### <a name="1141"></a><a name="1.14.1"></a>1.14.1
* Büyük/küçük harfe duyarlı dosya sistemleri için küçük bir çözüm.

### <a name="1140"></a><a name="1.14.0"></a>1.14.0
* Oturum tutarlılığı için destek ekler.
* Bu SDK sürümü, [Azure Cosmos DB öykünücü](https://aka.ms/cosmosdb-emulator)'un en son sürümünü gerektirir.

### <a name="1130"></a><a name="1.13.0"></a>1.13.0
* Redaked çapraz bölüm sorgularını Böl.
* Baştaki ve sondaki eğik çizgi (ve karşılık gelen testler) ile kaynak bağlantısı için destekler.

### <a name="1122"></a><a name="1.12.2"></a>1.12.2
*    NPM belgeleri düzeltildi.

### <a name="1121"></a><a name="1.12.1"></a>1.12.1
* Özel Unicode karakterlerine (LS, PS) sahip olan executeStoredProcedure içindeki bir hata düzeltildi.
* Bölüm anahtarında Unicode karakterlerle belge işlemede hata düzeltildi.
* Ad medyası ile koleksiyon oluşturmak için sabit destek. GitHub sorunu #114.
* İzin yetkilendirme belirteci için sabit destek. GitHub sorunu #178.

### <a name="1120"></a><a name="1.12.0"></a>1.12.0
* Tutarlık ön eki olarak adlandırılan yeni bir [tutarlılık düzeyi](consistency-levels.md) için destek eklendi.
* UriFactory için destek eklendi.
* Unicode desteği hatası düzeltildi. GitHub sorunu #171.

### <a name="1110"></a><a name="1.11.0"></a>1.11.0
* Toplama sorguları (COUNT, MIN, MAX, SUM ve AVG) için destek eklendi.
* Çapraz bölüm sorguları için paralellik derecesini denetleme seçeneği eklendi.
* Azure Cosmos DB öykünücüsüyle çalışırken TLS doğrulamasını devre dışı bırakma seçeneği eklendi.
* 10.100 RU/sn 'den 2500 RU/sn 'ye kadar bölümlenmiş koleksiyonlar üzerinde en düşük aktarım hızını düşürdü.
* Tek bölümlü koleksiyon için devamlılık belirteci hatası düzeltildi. GitHub sorunu #107.
* Tek param olarak 0 işlenirken executeStoredProcedure hatası düzeltildi. GitHub sorunu #155.

### <a name="1102"></a><a name="1.10.2"></a>1.10.2
* SDK sürümünü dahil etmek için sabit Kullanıcı Aracısı üst bilgisi.
* Küçük kod temizleme.

### <a name="1101"></a><a name="1.10.1"></a>1.10.1
* Öykünücüyü hedeflemek için SDK kullanılırken TLS doğrulaması devre dışı bırakılıyor (hostname = localhost).
* Saklı yordam yürütülürken betik günlüğü tutmayı etkinleştirme desteği eklendi.

### <a name="1100"></a><a name="1.10.0"></a>1.10.0
* Çapraz bölüm Paralel sorguları için destek eklendi.
* Bölümlenmiş koleksiyonlar için üst/sıra sorgularına yönelik destek eklendi.

### <a name="190"></a><a name="1.9.0"></a>1.9.0
* Kısıtlanmış istekler için yeniden deneme ilkesi desteği eklendi. (Kısıtlanan istekler çok büyük bir istek hızı alıyor, hata kodu 429.) Varsayılan olarak Azure Cosmos DB, 429 hata kodu ile karşılaşıldığında her bir istek için dokuz kez yeniden dener ve yanıt üst bilgisinde retryAfter saati belirlenir. Yeniden denemeler arasında sunucu tarafından döndürülen retryAfter süresini yoksaymak istiyorsanız, artık, ConnectionPolicy nesnesindeki RetryOptions özelliğinin bir parçası olarak sabit bir yeniden deneme aralığı zaman ayarlanabilir. Azure Cosmos DB, kısıtlanmış olan her istek için en fazla 30 saniye bekler (yeniden deneme sayısı ne olursa olsun) ve 429 hata koduyla yanıtı döndürür. Bu zaman, ConnectionPolicy nesnesindeki RetryOptions özelliğinde de geçersiz kılınabilir.
* Cosmos DB, kısıtlama yeniden deneme sayısını ve isteğin yeniden denemeler arasında bekleyeceği kümülatif süreyi belirtmek için her istekteki yanıt üst bilgileri olarak x-MS-azaltma-retry-Count ve x-MS-azaltma-retry-WAIT-Time-MS döndürür.
* RetryOptions sınıfı eklenmiştir ve bu, varsayılan yeniden deneme seçeneklerinden bazılarını geçersiz kılmak için kullanılabilecek ConnectionPolicy sınıfında RetryOptions özelliğini kullanıma sunma.

### <a name="180"></a><a name="1.8.0"></a>1.8.0
* Çok bölgeli veritabanı hesapları için destek eklendi.

### <a name="170"></a><a name="1.7.0"></a>1.7.0
* Belgeler için yaşam süresi (TTL) özelliğine yönelik destek eklendi.

### <a name="160"></a><a name="1.6.0"></a>1.6.0
* [Bölümlenmiş koleksiyonlar](partitioning-overview.md) ve [Kullanıcı tanımlı performans düzeyleri](performance-levels.md)uygulandı.

### <a name="156"></a><a name="1.5.6"></a>1.5.6
* Hatalı bir sonuçların bir Concat nedeniyle bağlantıların döndürülmediğinden düzeltilen RangePartitionResolver. resolveForRead hatası.

### <a name="155"></a><a name="1.5.5"></a>1.5.5
* Düzeltilen hashPartitionResolver resolveForRead (): hiçbir bölüm anahtarı, kayıtlı tüm bağlantıların bir listesini döndürmek yerine özel durum oluşturmadıysa.

### <a name="154"></a><a name="1.5.4"></a>1.5.4
* [#100](https://github.com/Azure/azure-documentdb-node/issues/100) -adanmış https Aracısı sorunu giderilir: genel aracıyı Azure Cosmos DB amacıyla değiştirmekten kaçının. LIB 'in tüm istekleri için adanmış bir aracı kullanın.

### <a name="153"></a><a name="1.5.3"></a>1.5.3
* Düzeltmeler [#81](https://github.com/Azure/azure-documentdb-node/issues/81) sorun-medya kimliklerinde düzgün şekilde işleme çizgileri.

### <a name="152"></a><a name="1.5.2"></a>1.5.2 planlama
* [#95](https://github.com/Azure/azure-documentdb-node/issues/95) -eventemitter dinleyicisi sızıntısı uyarısı sorununu düzeltir.

### <a name="151"></a><a name="1.5.1"></a>1.5.1
* Sorun düzeltme [#92](https://github.com/Azure/azure-documentdb-node/issues/90) , büyük/küçük harfe duyarlı sistemler Için klasör karmasını karma olarak yeniden adlandırın.

### <a name="150"></a><a name="1.5.0"></a>1.5.0
* Karma & aralığı bölüm çözümleyicilerini ekleyerek parçalama desteğini uygulayın.

### <a name="140"></a><a name="1.4.0"></a>1.4.0
* Upsert uygulama. DocumentClient üzerinde yeni upsertXXX yöntemleri.

### <a name="130"></a><a name="1.3.0"></a>1.3.0
* Sürüm numaralarını diğer SDK 'lara hizalı olarak getirmek için atlandı.

### <a name="122"></a><a name="1.2.2"></a>1.2.2
* Soru-cevap sarmalayıcısı yeni depoya bölünür.
* Npm kayıt defteri için paket dosyasına güncelleştir.

### <a name="121"></a><a name="1.2.1"></a>1.2.1
* KIMLIK tabanlı yönlendirmeyi uygular.
* [#49](https://github.com/Azure/azure-documentdb-node/issues/49) sorunu düzeltmeler-geçerli özellik geçerli () yöntemiyle çakışıyor.

### <a name="120"></a><a name="1.2.0"></a>1.2.0
* Jeo-uzamsal dizin desteği eklendi.
* Tüm kaynaklar için ID özelliğini doğrular. Kaynak kimlikleri,?,/, #,  &#47;&#47;, karakter içeremez veya boşlukla bitemez.
* Yeni "Dizin dönüştürme ilerlemesi" üst bilgisini Resourcereslik 'e ekler.

### <a name="110"></a><a name="1.1.0"></a>1.1.0
* V2 dizin oluşturma ilkesini uygular.

### <a name="103"></a><a name="1.0.3"></a>1.0.3
* Çekirdek ve Promise SDK 'sında [#40](https://github.com/Azure/azure-documentdb-node/issues/40) uygulanmış eslint ve grönlama yapılandırması sorun.

### <a name="102"></a><a name="1.0.2"></a>1.0.2
* Sorun [#45](https://github.com/Azure/azure-documentdb-node/issues/45) -taahhüt sarmalayıcısı, hata içeren üst bilgi içermez.

### <a name="101"></a><a name="1.0.1"></a>1.0.1
* ReadConflicts, readConflictAsync ve queryConflicts ekleyerek çakışmaları sorgulama özelliği uygulandı.
* API belgeleri güncelleştirildi.
* [#41](https://github.com/Azure/azure-documentdb-node/issues/41) -Client. Createbelgetasync hatası verme.

### <a name="100"></a><a name="1.0.0"></a>1.0.0
* GA SDK.

## <a name="release--retirement-dates"></a>Yayın & kullanımdan kaldırma tarihleri

Microsoft, daha yeni/desteklenen bir sürüme geçişi düzgünleştirmek için SDK 'nın devre dışı bırakılmasının ardından en az **12 ay** önce bildirim sağlar. Yeni özellikler, işlevler ve iyileştirmeler yalnızca geçerli SDK'ya eklendiğinden, her zaman mümkün olan en kısa zamanda, en son SDK sürümüne yükseltmeniz önerilir.

| Sürüm | Yayın Tarihi | Kullanımdan Kaldırma Tarihi |
| --- | --- | --- |
| [3.1.0](#3.1.0) |26 Temmuz 2019 |--- |
| [3.0.4](#3.0.4) |22 Temmuz 2019 |--- |
| [3.0.3](#3.0.3) |17 Temmuz 2019 |--- |
| [3.0.2](#3.0.2) |9 Temmuz 2019 |--- |
| [3.0.0](#3.0.0) |28 Haziran 2019 |--- |
| [2.1.5](#2.1.5) |20 Mart 2019 |--- |
| [2.1.4](#2.1.4) |15 Mart 2019 |--- |
| [2.1.3](#2.1.3) |8 Mart 2019 |--- |
| [2.1.2](#2.1.2) |28 Ocak 2019 |--- |
| [2.1.1](#2.1.1) |5 Aralık 2018 |--- |
| [2.1.0](#2.1.0) |4 Aralık 2018 |--- |
| [2.0.5](#2.0.5) |7 Kasım 2018 |--- |
| [2.0.4](#2.0.4) |30 Ekim 2018 |--- |
| [2.0.3](#2.0.3) |30 Ekim 2018 |--- |
| [2.0.2](#2.0.2) |10 Ekim 2018 |--- |
| [2.0.1](#2.0.1) |25 Eylül 2018 |--- |
| [2.0.0](#2.0.0) |Eylül 24, 2018 |--- |
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
| [1.5.2 planlama](#1.5.2) |26 Ocak 2016 |30 Ağustos 2020 |
| [1.5.2 planlama](#1.5.2) |22 Ocak 2016 |30 Ağustos 2020 |
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
Cosmos DB hakkında daha fazla bilgi edinmek için [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) hizmeti sayfasına bakın.