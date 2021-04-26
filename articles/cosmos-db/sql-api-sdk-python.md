---
title: Azure Cosmos DB SQL Python API 'SI, SDK & kaynakları
description: Sürüm tarihleri, emeklilik tarihleri ve Azure Cosmos DB Python SDK 'nın her sürümü arasında yapılan değişiklikler dahil olmak üzere SQL Python API 'SI ve SDK hakkında bilgi edinin.
author: Rodrigossz
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: reference
ms.date: 04/06/2021
ms.author: rosouz
ms.custom: devx-track-python
ms.openlocfilehash: ff551c5d677029f39d9a3db3a64f3d03e2c57eba
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107366044"
---
# <a name="azure-cosmos-db-python-sdk-for-sql-api-release-notes-and-resources"></a>SQL API için Azure Cosmos DB Python SDK’sı: Sürüm notları ve kaynaklar
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
> * [Toplu yürütücü-.NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Toplu yürütücü - Java](sql-api-sdk-bulk-executor-java.md)

| Sayfa| Bağlantı |
|---|---|
|**SDK 'Yı indir**|[PyPI](https://pypi.org/project/azure-cosmos)|
|**API belgeleri**|[Python API başvuru belgeleri](https://docs.microsoft.com/python/api/azure-cosmos/azure.cosmos?view=azure-python&preserve-view=true)|
|**SDK yükleme yönergeleri**|[Python SDK yükleme yönergeleri](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos)|
|**Kullanmaya başlama**|[Python SDK 'Yı kullanmaya başlama](create-sql-api-python.md)|
|**Desteklenen geçerli platform**|[Python 2,7](https://www.python.org/downloads/) ve [Python 3.6 +](https://www.python.org/downloads/)|

## <a name="release-history"></a>Yayın geçmişi

## <a name="420"></a>4.2.0

**Hata düzeltmeleri**
- Sonuçları sayfasına göre almak için query_iterable kullanıldığında devamlılık belirtecinin dikkate alınmamasına neden olan hata düzeltildi.
- Belge okuma ve silme için kaynak belirteçlerinin kabul edildiği hata düzeltildi. 

**Yeni özellikler**
- `partitionKey`Değişiklik akışını sorgularken geçiş için destek eklendi.

## <a name="410"></a>4.1.0

- "Yavaş" Dizin oluşturma modu için kullanımdan kaldırma uyarısı eklendi. Arka uç artık bu moda kapsayıcı oluşturulmasına izin vermez ve bunun yerine tutarlı olarak ayarlar.

**Yeni özellikler**
- Yeni bir kapsayıcı oluştururken analitik depolama TTL 'yi ayarlama özelliği eklendi.

**Hata düzeltmeleri**
- `dicts`Get_client API 'leri için giriş olarak düzeltilen destek.
- Sorgu yineleyiciler içinde düzeltilen Python 2/3 uyumluluğu.
- Tür ipucu hatası düzeltildi.
- Upsert_item işlevine seçenek üst bilgilerinin eklendiği hata düzeltildi. 
- Bir öğede dize olmayan bir KIMLIK kullanıldığında oluşan hata düzeltildi. Şimdi AttributeError yerine TypeError hatası oluşturuyor.


## <a name="400"></a>4.0.0

* Kararlı sürüm.
* İstek ve yanıt üst bilgileri için özel bir günlükçüsü geçişine olanak tanımak üzere işlem hattına HttpLoggingPolicy eklendi.

### <a name="400b6"></a>4.0.0 B6

* Medya API 'Leri için synchronized_request hata düzeltildi.
* Medya istekleri desteklenmediğinden, ConnectionPolicy 'den MediaReadMode ve MediaRequestTimeout kaldırıldı.

### <a name="400b5"></a>4.0.0 B5

* Azure. Cosmos. Errors modülü, Azure. Cosmos. Exceptions tarafından kullanımdan kaldırıldı ve değiştirilmiştir
* Erişim koşulu parametreleri ( `access_condition` , `if_match` , `if_none_match` ) ayrı ve parametre kullanımı için kullanımdan kaldırılmıştır `match_condition` `etag` .
* Yönlendirme eşleme sağlayıcısında hata düzeltildi.
* Sorgu benzersiz, konum ve limit desteği eklendi.
* İçin varsayılan belge sorgusu yürütme bağlamı artık kullanıldı

  * Akış sorgularını değiştirme
  * tek bölümlü sorgular ( `partitionkey` , `partitionKeyRangeId` seçeneklerde mevcuttur)
  * Belge olmayan sorgular

* Birden çok bölümdeki toplamalar için, çapraz bölüm sorgusu ayarı true olarak ayarlanmış ancak "Value" anahtar sözcüğü yok olarak bir hata oluştu
* Sorgu planını getirmeye yönelik diğer senaryolar için sorgu planı uç noktası isabetleri
* `__repr__`Cosmos varlık nesneleri için destek eklendi.
* Belgeler güncelleştirildi.

### <a name="400b4"></a>4.0.0 B4

* `timeout`İşlemin tamamlanması gereken saniye cinsinden mutlak bir zaman aşımı belirtmek için tüm işlemlere bir anahtar sözcük bağımsız değişkeni desteği eklendi. Zaman aşımı değeri aşılırsa, bir `azure.cosmos.errors.CosmosClientTimeoutError` oluşturulur.

* `ConnectionRetryPolicy`Http bağlantı hataları sırasında yeniden deneme davranışını yönetmek için yeni bir eklendi.

* Yeni Oluşturucu ve işlem başına yapılandırma anahtar sözcüğü bağımsız değişkenleri eklendi:

  * `retry_total` -En fazla yeniden deneme girişimleri.
  * `retry_backoff_max` -Saniye cinsinden en fazla yeniden deneme bekleme süresi.
  * `retry_fixed_interval` -Milisaniye cinsinden sabit yeniden deneme aralığı.
  * `retry_read` -En fazla yuva okuma yeniden deneme girişimi sayısı.
  * `retry_connect` -En fazla bağlantı hatası yeniden deneme girişimi sayısı.
  * `retry_status` -Hata durum kodlarında en fazla yeniden deneme denemesi sayısı.
  * `retry_on_status_codes` -Yeniden denenecek belirli durum kodlarının listesi.
  * `retry_backoff_factor` -Yeniden deneme girişimleri arasında bekleme süresini hesaplama faktörü.

### <a name="400b3"></a>4.0.0 B3

* `create_database_if_not_exists()` `create_container_if_not_exists` CosmosClient ve veritabanına sırasıyla ve işlevleri eklendi.

### <a name="400b2"></a>4.0.0 B2

* Sürüm 4.0.0 B2, Python dili en iyi uygulamalarına uygun bir istemci kitaplığı oluşturmaya yönelik çabalarımızın ikinci yinelemedir.

**Yeni değişiklikler**

* İstemci bağlantısı, içinde tanımlanan HTTP işlem hattını tüketmek üzere uyarlanmıştır `azure.core.pipeline` .

* Etkileşimli nesneler artık proxy olarak yeniden adlandırıldı. Şunları içerir:

  * `Database` -> `DatabaseProxy`
  * `User` -> `UserProxy`
  * `Container` -> `ContainerProxy`
  * `Scripts` -> `ScriptsProxy`

* Oluşturucusunun sürümü `CosmosClient` güncelleştirildi:

  * `auth`Parametresi olarak yeniden adlandırıldı `credential` ve artık doğrudan bir kimlik doğrulama türü alacak. Bu, birincil anahtar değeri, kaynak belirteçlerinin bir sözlüğü veya bir izin listesi geçirilebileceği anlamına gelir. Ancak eski sözlük biçimi hala desteklenmektedir.

  * `connection_policy`Parametresi yalnızca bir anahtar sözcük parametresi yaptı ve hala desteklenirken, ilkenin her bir özniteliği artık açık anahtar sözcük bağımsız değişkenleri olarak geçirilebilir:

    * `request_timeout`
    * `media_request_timeout`
    * `connection_mode`
    * `media_read_mode`
    * `proxy_config`
    * `enable_endpoint_discovery`
    * `preferred_locations`
    * `multiple_write_locations`

* `CosmosClient`Azure Portal alınan bir bağlantı dizesi aracılığıyla oluşturmayı etkinleştirmek için yeni bir Oluşturucu eklendi.

* Bazı `read_all` işlemler işlemler olarak yeniden adlandırıldı `list` :

  * `CosmosClient.read_all_databases` -> `CosmosClient.list_databases`
  * `Container.read_all_conflicts` -> `ContainerProxy.list_conflicts`
  * `Database.read_all_containers` -> `DatabaseProxy.list_containers`
  * `Database.read_all_users` -> `DatabaseProxy.list_users`
  * `User.read_all_permissions` -> `UserProxy.list_permissions`

* Veya parametreleri olan tüm `request_options` İşlemler `feed_options` , bunlar yalnızca anahtar sözcük parametrelerine taşınmıştır. Ayrıca, bu seçenek sözlükleri hala desteklenirken, sözlük içindeki her bir seçenek artık açık anahtar sözcük bağımsız değişkenleri olarak desteklenmektedir.

* Hata hiyerarşisi artık şuradan devralındı `azure.core.AzureError` :

  * `HTTPFailure`, `CosmosHttpResponseError` olarak yeniden adlandırıldı
  * `JSONParseFailure` kaldırılmış ve değiştirildi `azure.core.DecodeError`
  * Belirli yanıt kodları için ek hatalar eklendi:
    * `CosmosResourceNotFoundError` durum 404 için
    * `CosmosResourceExistsError` durum 409 için
    * `CosmosAccessConditionFailedError` durum 412 için

* `CosmosClient` Artık, istemci bağlantısını kapatmayı işlemek için bir bağlam yöneticisinde çalıştırılabilir.

* Tekrarlayabileceğiniz yanıtlar (örneğin, sorgu yanıtları ve liste yanıtları) artık türündedir `azure.core.paging.ItemPaged` . Yöntemi, `fetch_next_block` yöntemi tarafından erişilen bir ikincil Yineleyici ile değiştirilmiştir `by_page` .

### <a name="400b1"></a>4.0.0 B1

Sürüm 4.0.0 B1, Python dili en iyi uygulamalarına uygun bir Kullanıcı dostu istemci kitaplığı oluşturmaya yönelik çabalarımızın ilk önizlemesidir. Diğer Azure SDK kitaplıklarının bu ve önizleme sürümleri hakkında daha fazla bilgi için lütfen adresini ziyaret edin https://aka.ms/azure-sdk-preview1-python .

**Son değişiklikler: yeni API tasarımı**

* İşlemler artık belirli bir istemcinin kapsamına alındı:

  * `CosmosClient`: Bu istemci, hesap düzeyindeki işlemleri işler. Bu, hizmet özelliklerini yönetmeyi ve veritabanlarını bir hesap içinde listelemeyi içerir.
  * `Database`: Bu istemci veritabanı düzeyi işlemlerini işler. Bu, kapsayıcıları, kullanıcıları ve saklı yordamları oluşturmayı ve silmeyi içerir. Bir cosmosClient ' örneğinden ada göre erişilebilir.
  * `Container`: Bu istemci belirli bir kapsayıcı için işlemleri işler. Buna öğe sorgulama ve ekleme ve özellikleri yönetme dahildir.
  * `User`: Bu istemci belirli bir kullanıcı için işlemleri işler. Bu, izinleri ekleme ve silme ve kullanıcı özelliklerini yönetme içerir.

    Bu istemcilere, yöntemi kullanılarak istemci hiyerarşisinde aşağı gidilerek erişilebilir `get_<child>_client` . Yeni API hakkında tam Ayrıntılar için lütfen [başvuru belgelerine](https://aka.ms/azsdk-python-cosmos-ref)bakın.

* İstemcilere KIMLIK yerine ad ile erişilir. Bağlantı oluşturmak için dizeleri birleştirme gerekmez.

* Bağımsız modüllerden türleri ve yöntemleri içeri aktarmaya gerek yoktur. Ortak API yüzey alanı doğrudan `azure.cosmos` pakette kullanılabilir.

* Bağımsız istek özellikleri, ayrı bir örnek oluşturmak yerine anahtar sözcük bağımsız değişkenleri olarak sağlanıyor `RequestOptions` .

### <a name="302"></a>3.0.2

* MultiPolygon veri türü için destek eklendi
* Oturum okuma yeniden deneme Ilkesinde hata düzeltmesini
* Base 64 dizelerinin kodu çözülürken yanlış doldurma sorunları için hata düzeltilme

### <a name="301"></a>3.0.1

* LocationCache 'de hata düzeltilme
* Hata düzeltmesini uç noktası yeniden deneme mantığı
* Sabit belge

### <a name="300"></a>3.0.0

* Çok bölgeli yazma desteği eklendi
* Değişiklikleri adlandırma
  * DocumentClient to CosmosClient
  * Kapsayıcıya koleksiyon
  * Belgeyi öğeye
  * Paket adı "Azure-Cosmos" olarak güncelleştirildi
  * Ad alanı "Azure. Cosmos" olarak güncelleştirildi

### <a name="233"></a>2.3.3

* Proxy desteği eklendi
* Değişiklik akışını okuma desteği eklendi
* Koleksiyon kota üstbilgileri için destek eklendi
* Büyük oturum belirteçleri sorunu için bugdüzeltmesini
* ReadMedia API için bugdüzeltmesini
* Bölüm anahtar aralığı önbelleğinde bugdüzeltmesini

### <a name="232"></a>2.3.2

* Bağlantı sorunlarında varsayılan yeniden denemeler için destek eklendi.

### <a name="231"></a>2.3.1

* Belgeler, Azure DocumentDB yerine Azure Cosmos DB başvuracak şekilde güncelleştirildi.

### <a name="230"></a>2.3.0

* Bu SDK sürümü, Azure Cosmos DB öykünücüsü 'nin indirileceği en son sürümünü gerektirir https://aka.ms/cosmosdb-emulator .

### <a name="221"></a>2.2.1

* Toplam dict için bugdüzeltmesini
* Kaynak bağlantısında eğik çizgi kırpma için bugdüzeltmesini
* Unicode kodlaması için testler

### <a name="220"></a>2.2.0

* Dakika başına Istek birimi (RU/y) özelliği için destek eklendi.
* Tutarlık ön eki olarak adlandırılan yeni bir tutarlılık düzeyi için destek eklendi.

### <a name="210"></a>2.1.0

* Toplama sorguları (COUNT, MIN, MAX, SUM ve AVG) için destek eklendi.
* DocumentDB öykünücüsünde çalışırken SSL doğrulamasını devre dışı bırakma seçeneği eklendi.
* Bağımlı istekler modülünün kısıtlaması tam olarak 2.10.0 olarak kaldırıldı.
* 10.100 RU/sn 'den 2500 RU/sn 'ye kadar bölümlenmiş koleksiyonlar üzerinde en düşük aktarım hızını düşürdü.
* Saklı yordam yürütülürken betik günlüğü tutmayı etkinleştirme desteği eklendi.
* REST API sürümü bu sürümle ' 2017-01-19 ' öğesine göre tamponlanır.

### <a name="201"></a>2.0.1

* Belge açıklamalarında düzenleme değişiklikleri yapıldı.

### <a name="200"></a>2.0.0

* Python 3,5 için destek eklendi.
* İstekler modülünü kullanarak bağlantı havuzu oluşturma desteği eklendi.
* Oturum tutarlılığı için destek eklendi.
* Bölümlenmiş koleksiyonlar için TOP/ORDERBY sorguları için destek eklendi.

### <a name="190"></a>1.9.0

* Kısıtlanmış istekler için yeniden deneme ilkesi desteği eklendi. (Kısıtlanan istekler çok büyük bir istek hızı alıyor, hata kodu 429.) Varsayılan olarak, DocumentDB hata kodu 429 ile karşılaşıldığında her bir istek için dokuz kez yeniden dener.
  Yeniden denemeler arasında sunucu tarafından döndürülen retryAfter süresini yoksaymak istiyorsanız, artık, ConnectionPolicy nesnesindeki RetryOptions özelliğinin bir parçası olarak sabit bir yeniden deneme aralığı zaman ayarlanabilir.
  DocumentDB artık, kısıtlanmış olan her istek için en fazla 30 saniye bekler (yeniden deneme sayısı ne olursa olsun) ve 429 hata koduyla yanıtı döndürür.
  Bu zaman, ConnectionPolicy nesnesindeki RetryOptions özelliğinde de geçersiz kılınabilir.

* DocumentDB artık, kısıtlama yeniden deneme sayısını ve isteğin yeniden denemeler arasında bekleyeceği kümülatif süreyi belirtmek için her istekteki yanıt üst bilgileri olarak x-MS-valf retry-Count ve x-MS-azaltma-retry-WAIT-Time-MS döndürür.

* RetryPolicy sınıfı ve document_client sınıfında kullanıma sunulan karşılık gelen Özellik (retry_policy) kaldırıldı ve bunun yerine, varsayılan yeniden deneme seçeneklerinden bazılarını geçersiz kılmak için kullanılan ConnectionPolicy sınıfında RetryOptions özelliğini kullanıma sunan bir RetryOptions sınıfı getirdi.

### <a name="180"></a>1.8.0

* Coğrafi olarak çoğaltılan veritabanı hesapları için destek eklendi.
* Genel Konağı ve masterKey 'i ayrı test sınıflarına taşımak için test düzeltmeleri.

### <a name="170"></a>1.7.0

* Belgeler için yaşam süresi (TTL) özelliğine yönelik destek eklendi.

### <a name="161"></a>1.6.1

* Bölüm anahtarı yolunda özel karakterlere izin vermek için sunucu tarafı bölümleme ile ilgili hata düzeltmeleri.

### <a name="160"></a>1.6.0

* Sunucu tarafı bölümlenmiş koleksiyonlar özelliği için destek eklendi.

### <a name="150"></a>1.5.0

* SDK 'ya Istemci tarafı parçalara ayırma çerçevesi eklendi. HashPartionResolver ve RangePartitionResolver sınıfları uygulandı.

### <a name="142"></a>1.4.2

* Upsert uygulama. Upsert özelliği desteklemek için yeni UpsertXXX yöntemleri eklenmiştir.
* ID-Based yönlendirmeyi uygulayın. Ortak API değişikliği yok, tüm değişiklikler iç.

### <a name="130"></a>1.3.0

* Sürüm numarasını diğer SDK 'lara hizalı olarak getirmek için yayın atlandı

### <a name="120"></a>1.2.0

* Jeo-uzamsal dizini destekler.
* Tüm kaynaklar için ID özelliğini doğrular. Kaynak kimlikleri `?, /, #, \\` karakter içeremez veya boşluk ile bitemez.
* Yeni "Dizin dönüştürme ilerlemesi" üst bilgisini Resourcereslik 'e ekler.

### <a name="110"></a>1.1.0

* V2 dizin oluşturma ilkesini uygular

### <a name="101"></a>1.0.1

* Proxy bağlantısını destekler

## <a name="release--retirement-dates"></a>Yayın & kullanımdan kaldırma tarihleri

Microsoft, daha yeni/desteklenen bir sürüme geçişi düzgünleştirmek için SDK 'nın devre dışı bırakılmasının ardından en az **12 ay** önce bildirim sağlar. Yeni özellikler, işlevler ve iyileştirmeler yalnızca geçerli SDK'ya eklendiğinden, her zaman mümkün olan en kısa zamanda, en son SDK sürümüne yükseltmeniz önerilir.

> [!WARNING]
> 31 Ağustos 2022 ' den sonra Azure Cosmos DB artık hata düzeltmeleri yapmayacaktır ya da SQL API için Azure Cosmos DB Python SDK 'sının 1. x ve 2. x sürümlerini desteklemez. Yükseltmemeyi tercih ediyorsanız, SDK 'nın 1. x ve 2. x sürümünden gönderilen istekler Azure Cosmos DB hizmeti tarafından sunulmayı sürdürmeye devam edecektir.

| Sürüm | Yayın Tarihi | Kullanımdan Kaldırma Tarihi |
| --- | --- | --- |
| [4.2.0](#420) |09 Eki, 2020 |--- |
| [4.1.0](#410) |10 Ağu, 2020 |--- |
| [4.0.0](#400) |20 Mayıs 2020 |--- |
| [3.0.2](#302) |15 Kasım 2018 |--- |
| [3.0.1](#301) |Eki 04, 2018 |--- |
| [2.3.3](#233) |08 Eyl, 2018 |31 Ağustos 2022 |
| [2.3.2](#232) |08 Mayıs 2018 |31 Ağustos 2022 |
| [2.3.1](#231) |21 Aralık 2017 |31 Ağustos 2022 |
| [2.3.0](#230) |10 Kasım 2017 |31 Ağustos 2022 |
| [2.2.1](#221) |29 Eyl, 2017 |31 Ağustos 2022 |
| [2.2.0](#220) |10 Mayıs 2017 |31 Ağustos 2022 |
| [2.1.0](#210) |01 Mayıs 2017 |31 Ağustos 2022 |
| [2.0.1](#201) |30 Ekim 2016 |31 Ağustos 2022 |
| [2.0.0](#200) |29 Eylül 2016 |31 Ağustos 2022 |
| [1.9.0](#190) |07 Temmuz 2016 |31 Ağustos 2022 |
| [1.8.0](#180) |14 Haziran 2016 |31 Ağustos 2022 |
| [1.7.0](#170) |26 Nisan 2016 |31 Ağustos 2022 |
| [1.6.1](#161) |08 Nisan 2016 |31 Ağustos 2022 |
| [1.6.0](#160) |29 Mart 2016 |31 Ağustos 2022 |
| [1.5.0](#150) |03 Ocak 2016 |31 Ağustos 2022 |
| [1.4.2](#142) |06 Ekim 2015 |31 Ağustos 2022 |
| 1.4.1 |06 Ekim 2015 |31 Ağustos 2022 |
| [1.2.0](#120) |06 Ağustos 2015 |31 Ağustos 2022 |
| [1.1.0](#110) |09 Temmuz 2015 |31 Ağustos 2022 |
| [1.0.1](#101) |25 Mayıs 2015 |31 Ağustos 2022 |
| 1.0.0 |07 Nisan 2015 |31 Ağustos 2022 |
| 0.9.4-ürününün |14 Ocak 2015 |29 Şubat 2016 |
| 0.9.3-ürününün |09 Aralık 2014 |29 Şubat 2016 |
| 0.9.2-ürününün |25 Kasım 2014 |29 Şubat 2016 |
| 0.9.1-ürününün |23 Eylül 2014 |29 Şubat 2016 |
| 0.9.0-ürününün |21 Ağustos 2014 |29 Şubat 2016 |

## <a name="faq"></a>SSS

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Sonraki adımlar

Cosmos DB hakkında daha fazla bilgi edinmek için [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) hizmeti sayfasına bakın.
