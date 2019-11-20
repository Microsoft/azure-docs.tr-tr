---
title: "Azure Cosmos DB: SQL Java API 'SI, SDK & kaynakları"
description: Sürüm tarihleri, kullanımdan kaldırma tarihleri ve SQL Java SDK Azure Cosmos DB her sürümü arasında yapılan değişiklikler dahil olmak üzere SQL Java API 'SI ve SDK hakkında bilgi edinin.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 03/13/2019
ms.author: sngun
ms.openlocfilehash: eb8110c953787d1ebf7f01037808ee77db2eb8b4
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72934154"
---
# <a name="azure-cosmos-db-java-sdk-for-sql-api-release-notes-and-resources"></a>SQL API için Java SDK Azure Cosmos DB: sürüm notları ve kaynakları
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

SQL API Java SDK 'Sı zaman uyumlu işlemleri destekler. Zaman uyumsuz destek için, [SQL API zaman uyumsuz Java SDK 'sını](sql-api-sdk-async-java.md)kullanın. 

| |  |
|---|---|
|**SDK Indirmesi**|[Maven](https://search.maven.org/#search%7Cgav%7C1%7Cg%3A%22com.microsoft.azure%22%20AND%20a%3A%22azure-documentdb%22)|
|**API belgeleri**|[Java API başvuru belgeleri](/java/api/com.microsoft.azure.documentdb)|
|**SDK 'ya katkıda bulunma**|[GitHub](https://github.com/Azure/azure-documentdb-java/)|
|**Başlarken**|[Java SDK 'sını kullanmaya başlama](sql-api-java-get-started.md)|
|**Web uygulaması öğreticisi**|[Azure Cosmos DB ile Web uygulaması geliştirme](sql-api-java-application.md)|
|**Desteklenen en düşük çalışma zamanı**|[Java Development Kit (JDK) 7 +](https://aka.ms/azure-jdks)|

## <a name="release-notes"></a>Sürüm notları

### <a name="a-name244244"></a><a name="2.4.4"/>2.4.4
* İyileştirilmiş bölüm anahtar aralığı önbelleği yenilemeleri.

### <a name="a-name242242"></a><a name="2.4.2"/>2.4.2 sections
* İyileştirilmiş koleksiyon önbelleği yenilemeleri.

### <a name="a-name241241"></a><a name="2.4.1"/>2.4.1
* İstek tanılama dizesinden iç özel durum iletisini almak için destek eklendi.

### <a name="a-name240240"></a><a name="2.4.0"/>2.4.0
* PartitionKeyDefinition üzerinde sürüm API 'si kullanıma sunuldu.

### <a name="a-name230230"></a><a name="2.3.0"/>2.3.0
* Doğrudan mod için ayrı zaman aşımı desteği eklendi.

### <a name="a-name223223"></a><a name="2.2.3"/>2.2.3
* Hizmetten null hata iletisi tüketiyor ve belge istemci özel durumu üretiliyor.

### <a name="a-name222222"></a><a name="2.2.2"/>2.2.2
* Yuva bağlantısı geliştirmesi, SoKeepAlive varsayılan değeri.

### <a name="a-name220220"></a><a name="2.2.0"/>2.2.0
* İstek tanılama dizesi desteği eklendi.

### <a name="a-name213213"></a><a name="2.1.3"/>2.1.3
* Karma v2 için PartitionKey içindeki hata düzeltildi.

### <a name="a-name212212"></a><a name="2.1.2"/>2.1.2 'yi
* Bileşik dizinler için destek eklendi.
* Yenilemeyi zorlamak için genel uç nokta yöneticisinde hata düzeltildi.
* Doğrudan modda ön koşullara sahip olan uptts için hata düzeltildi.

### <a name="a-name211211"></a><a name="2.1.1"/>2.1.1
* Ağ Geçidi adres önbelleğinde hata düzeltildi.

### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0
* Doğrudan mod için çok bölgeli yazma desteği eklendi.
* Bir proxy 'den ServiceUnavailable özel durumları olarak oluşturulan IOException işleme desteği eklendi.
* Uç nokta bulma yeniden deneme ilkesinde hata düzeltildi.
* BaseDatabaseAccountConfigurationProvider içinde null işaretçi özel durumlarının bulunmadığından emin olmak için bir hata düzeltildi.
* Sorgu yineleyicisinin null değeri döndürmediğinden emin olmak için hata düzeltildi.
* Büyük PartitionKey 'e izin verildiğinden emin olmak için hata düzeltildi

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0
* Ağ Geçidi modu için çok bölgeli yazma desteği eklendi.

### <a name="a-name11641164"></a><a name="1.16.4"/>1.16.4
* Bir sorgu için okuma bölümü anahtar aralıklarında hata düzeltildi.

### <a name="a-name11631163"></a><a name="1.16.3"/>1.16.3
* DirectHttps modunda devamlılık belirteci üstbilgi boyutu ayarlanırken hata düzeltildi.

### <a name="a-name11621162"></a><a name="1.16.2"/>1.16.2
* Eklenen akış, destek üzerinden başarısız oldu.
* Özel meta veriler için destek eklendi.
* İyileştirilmiş oturum işleme mantığı.
* Bölüm anahtar aralığı önbelleğinde hata düzeltildi.
* Doğrudan modda bir NPE hatası düzeltildi.

### <a name="a-name11611161"></a><a name="1.16.1"/>1.16.1
* Benzersiz dizin desteği eklendi.
* Akış seçeneklerinde devamlılık belirteci boyutunu sınırlama desteği eklendi.
* JSON serileştirmesi (zaman damgası) içinde bir hata düzeltildi.
* JSON serileştirme (enum) hatası düzeltildi.
* Com. fasterxml. Jackson. Core: Jackson-DataBind 'a bağımlılık, 'nın 2.9.5 sürümüyle sürümüne yükseltildi.

### <a name="a-name11601160"></a><a name="1.16.0"/>1.16.0
* Doğrudan mod için geliştirilmiş bağlantı havuzu.
* OrderBy olmayan çapraz bölüm sorgusu için önceden getirme iyileştirmesi geliştirildi.
* İyileştirilmiş UUID oluşturma.
* İyileştirilmiş oturum tutarlılığı mantığı.
* MultiPolygon için destek eklendi.
* Koleksiyon için bölüm anahtarı aralığı Istatistikleri için destek eklendi.
* Çok bölgeli destek içindeki bir hata düzeltildi.

### <a name="a-name11501150"></a><a name="1.15.0"/>1.15.0
* İyileştirilmiş JSON serileştirme performansı.
* Bu SDK sürümü, Azure Cosmos DB öykünücüsü 'nin https://aka.ms/cosmosdb-emulator 'den indirileceği en son sürümünü gerektirir.

### <a name="a-name11401140"></a><a name="1.14.0"/>1.14.0
* Microsoft arkadaş kitaplıkları için dahili değişiklikler.

### <a name="a-name11301130"></a><a name="1.13.0"/>1.13.0
* Tek bölümlü anahtar aralıklarını okurken bir sorun düzeltildi.
* RESOURCEID ayrıştırma sırasında, kısa adlara sahip veritabanını etkileyen bir sorun düzeltildi.
* Bölüm anahtarı kodlamasıyla bir sorun düzeltildi.

### <a name="a-name11201120"></a><a name="1.12.0"/>1.12.0
* Bölüm bölmeler sırasında işleme istemek için kritik hata düzeltmeleri.
* Güçlü ve Boundedstalet tutarlılık düzeylerinde bir sorun düzeltildi.

### <a name="a-name11101110"></a><a name="1.11.0"/>1.11.0
* Tutarlık ön eki olarak adlandırılan yeni bir tutarlılık düzeyi için destek eklendi.
* Koleksiyonda oturum modunda okuma hatası düzeltildi.

### <a name="a-name11001100"></a><a name="1.10.0"/>1.10.0
* 2\.500 RU/sn olarak düşük olan ve 100 RU/sn 'lik artışlarla ölçeklendirerek bölümlenmiş koleksiyon için destek etkinleştirildi.
* Yerel derlemede, bazı sorgularda NullRef özel durumuna neden olabilecek bir hata düzeltildi.

### <a name="a-name196196"></a><a name="1.9.6"/>1.9.6
* Sorgu altyapısı yapılandırmasında, Ağ Geçidi modundaki sorgular için özel durumlara neden olabilecek bir hata düzeltildi.
* Oturum kapsayıcısında, koleksiyon oluşturulduktan hemen sonra istekler için "sahip kaynak bulunamadı" özel durumuna neden olabilecek birkaç hata düzeltildi.

### <a name="a-name195195"></a><a name="1.9.5"/>1.9.5
* Toplama sorguları (COUNT, MIN, MAX, SUM ve AVG) için destek eklendi. Bkz. [toplama desteği](sql-query-aggregates.md).
* Değişiklik akışı için destek eklendi.
* RequestOptions. Setpopulatequotaınfo aracılığıyla koleksiyon kota bilgileri için destek eklendi.
* RequestOptions. setScriptLoggingEnabled aracılığıyla depolanan yordam betiği günlüğü için destek eklendi.
* DirectHttps modundaki sorgunun, kısıtlama hatalarıyla karşılaşıldığında yanıt vermemesine neden olan bir hata düzeltildi.
* Oturum tutarlılığı modunda bir hata düzeltildi.
* İstek hızı yüksek olduğunda HttpContext 'te NullReferenceException 'e neden olabilecek bir hata düzeltildi.
* DirectHttps modunun performansı geliştirildi.

### <a name="a-name194194"></a><a name="1.9.4"/>1.9.4
* ConnectionPolicy. setProxy () API 'si ile basit istemci örneği tabanlı proxy desteği eklendi.
* DocumentClient örneğini düzgün şekilde kapatmak için DocumentClient. Close () API 'SI eklendi.
* Sorgu planını ağ geçidi yerine yerel derlemeden türeterek doğrudan bağlantı modunda sorgu performansı geliştirildi.
* Kullanıcıların POJO 'da Jsonıgnoreproperties tanımlamasına gerek kalmaması için FAIL_ON_UNKNOWN_PROPERTIES = false değerini ayarlayın.
* DOLAYıSıYLA SLF4J kullanmak için yeniden düzenlenmiş günlüğe kaydetme.
* Tutarlılık okuyucusunda diğer birkaç hata düzeltildi.

### <a name="a-name193193"></a><a name="1.9.3"/>1.9.3
* Doğrudan bağlantı modunda bağlantı sızıntılarını engellemek için bağlantı yönetiminde hata düzeltildi.
* ÜSTTEKI sorguda bir hata düzeltildi ve bu, NullReference özel durumu oluşturmayabilir.
* İç önbellekler için ağ çağrısı sayısını azaltarak performansı artırdık.
* Daha iyi sorun giderme için DocumentClientException içinde durum kodu, ActivityId ve Istek URI 'SI eklendi.

### <a name="a-name192192"></a><a name="1.9.2"/>1.9.2
* Tutarlılık için bağlantı yönetiminde bir sorun düzeltildi.

### <a name="a-name191191"></a><a name="1.9.1"/>1.9.1
* Boundedstatayetlik tutarlılığı düzeyi için destek eklendi.
* Bölümlenmiş koleksiyonlara yönelik CRUD işlemlerine yönelik doğrudan bağlantı desteği eklendi.
* SQL ile bir veritabanını sorgularken hata düzeltildi.
* Oturum belirtecinin yanlış ayarlanbildiği oturum önbelleğinde bir hata düzeltildi.

### <a name="a-name190190"></a><a name="1.9.0"/>1.9.0
* Çapraz bölüm Paralel sorguları için destek eklendi.
* Bölümlenmiş koleksiyonlar için üst/sıra sorgularına yönelik destek eklendi.
* Güçlü tutarlılık desteği eklendi.
* Doğrudan bağlantı kullanılırken ad tabanlı istekler için destek eklendi.
* Etkinlik kimliğinin tüm istek yeniden denemeleri arasında tutarlı kalmasını sağlamak için düzeltildi.
* Aynı ada sahip bir koleksiyon yeniden oluşturulurken oturum önbelleğiyle ilgili bir hata düzeltildi.
* Coğrafi olarak uzamsal sorgular için koleksiyon dizin oluşturma ilkesi belirtilirken Çokgen ve LineString veri türleri eklendi.
* Java 1,8 için Java belgesi ile ilgili sorunlar düzeltildi.

### <a name="a-name181181"></a><a name="1.8.1"/>1.8.1
* PartitionKeyDefinitionMap 'te tek bölümlü koleksiyonları önbelleğe almak için bir hata düzeltildi ve ek getirme bölüm anahtarı istekleri yapılamıyor.
* Yanlış bir bölüm anahtarı değeri sağlandığında yeniden denenmeyen bir hata düzeltildi.

### <a name="a-name180180"></a><a name="1.8.0"/>1.8.0
* Çok bölgeli veritabanı hesapları için destek eklendi.
* En fazla yeniden deneme girişimlerini özelleştirme ve en fazla yeniden deneme bekleme süresi gibi kısıtlanmış isteklerde otomatik yeniden deneme desteği eklendi.  Bkz. RetryOptions and ConnectionPolicy. getRetryOptions ().
* Kullanımdan kaldırılan IPartitionResolver tabanlı özel bölümlendirme kodu. Lütfen daha yüksek depolama ve aktarım hızı için bölümlenmiş koleksiyonlar kullanın.

### <a name="a-name171171"></a><a name="1.7.1"/>1.7.1
* Hız sınırlaması için yeniden deneme ilkesi desteği eklendi.  

### <a name="a-name170170"></a><a name="1.7.0"/>1.7.0
* Belgeler için yaşam süresi (TTL) desteği eklendi.

### <a name="a-name160160"></a><a name="1.6.0"/>1.6.0
* [Bölümlenmiş koleksiyonlar](partition-data.md) ve [Kullanıcı tanımlı performans düzeyleri](performance-levels.md)uygulandı.

### <a name="a-name151151"></a><a name="1.5.1"/>1.5.1
* Diğer SDK 'lar ile tutarlı olması için küçük endian 'de karma değerler oluşturmak üzere Karmadaki bir hata düzeltildi.

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0
* Birden çok bölüm genelinde uygulama oluşturmaya yardımcı olmak için karma & aralığı bölüm Çözümleyicileri ekleyin.

### <a name="a-name140140"></a><a name="1.4.0"/>1.4.0
* Upsert uygulama. Upsert özelliği desteklemek için yeni upsertXXX yöntemleri eklenmiştir.
* KIMLIK tabanlı yönlendirmeyi uygulayın. Ortak API değişikliği yok, tüm değişiklikler iç.

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0
* Sürüm numarasını diğer SDK 'lara hizalı olarak getirmek için yayın atlandı

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* Jeo-uzamsal dizini destekler
* Tüm kaynaklar için ID özelliğini doğrular. Kaynak kimlikleri,?,/, # \, karakter içeremez veya boşlukla bitemez.
* Yeni "Dizin dönüştürme ilerlemesi" üst bilgisini Resourcereslik 'e ekler.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* V2 dizin oluşturma ilkesini uygular

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* GA SDK

## <a name="release-and-retirement-dates"></a>Yayın ve emeklilik tarihleri
Microsoft, daha yeni/desteklenen bir sürüme geçişi düzgünleştirmek için SDK 'nın devre dışı bırakılmasının ardından en az **12 ay** önce bildirim sağlayacaktır.

Yeni özellikler ve işlevler ve iyileştirmeler yalnızca geçerli SDK 'ya eklenir, bu nedenle en son SDK sürümüne her zaman olabildiğince erken yükseltmeniz önerilir.

Kullanımdan kaldırılan bir SDK 'Yı kullanarak Cosmos DB istek, hizmet tarafından reddedilir.

> [!WARNING]
> Java için SQL SDK 'sının tüm **1. x** sürümleri **30 Mayıs 2020**tarihinde kullanımdan kaldırılacaktır.
> 
>

> [!WARNING]
> Sürüm **1.0.0** öncesinde Java IÇIN SQL SDK 'nın tüm sürümleri **, 29 Şubat 2016**tarihinde devre dışı bırakıldı.
> 
> 

<br/>

| Sürüm | Yayın tarihi | Emeklilik tarihi |
| --- | --- | --- |
| [2.1.3](#2.1.3) |13 Mar, 2018 |--- |
| [2.1.2 'yi](#2.1.2) |09 Mar, 2018 |--- |
| [2.1.1](#2.1.1) |13. ara, 2018 |--- |
| [2.1.0](#2.1.0) |20 Kasım 2018 |--- |
| [2.0.0](#2.0.0) |21 Eyl, 2018 |--- |
| [1.16.4](#1.16.4) |10 Eylül 2018 |30 Mayıs 2020 |
| [1.16.3](#1.16.3) |09 Eyl, 2018 |30 Mayıs 2020 |
| [1.16.2](#1.16.2) |29 Haziran 2018 |30 Mayıs 2020 |
| [1.16.1](#1.16.1) |16 Mayıs 2018 |30 Mayıs 2020 |
| [1.16.0](#1.16.0) |15 Mart 2018 |30 Mayıs 2020 |
| [1.15.0](#1.15.0) |14 Kasım 2017 |30 Mayıs 2020 |
| [1.14.0](#1.14.0) |28 Ekim 2017 |30 Mayıs 2020 |
| [1.13.0](#1.13.0) |25 Ağustos 2017 |30 Mayıs 2020 |
| [1.12.0](#1.12.0) |11 Temmuz 2017 |30 Mayıs 2020 |
| [1.11.0](#1.11.0) |10 Mayıs 2017 |30 Mayıs 2020 |
| [1.10.0](#1.10.0) |11 Mart 2017 |30 Mayıs 2020 |
| [1.9.6](#1.9.6) |21 Şubat 2017 |30 Mayıs 2020 |
| [1.9.5](#1.9.5) |31 Ocak 2017 |30 Mayıs 2020 |
| [1.9.4](#1.9.4) |24 Kasım 2016 |30 Mayıs 2020 |
| [1.9.3](#1.9.3) |30 Ekim 2016 |30 Mayıs 2020 |
| [1.9.2](#1.9.2) |28 Ekim 2016 |30 Mayıs 2020 |
| [1.9.1](#1.9.1) |26 Ekim 2016 |30 Mayıs 2020 |
| [1.9.0](#1.9.0) |03 Ekim 2016 |30 Mayıs 2020 |
| [1.8.1](#1.8.1) |30 Haziran 2016 |30 Mayıs 2020 |
| [1.8.0](#1.8.0) |14 Haziran 2016 |30 Mayıs 2020 |
| [1.7.1](#1.7.1) |30 Nisan 2016 |30 Mayıs 2020 |
| [1.7.0](#1.7.0) |27 Nisan 2016 |30 Mayıs 2020 |
| [1.6.0](#1.6.0) |29 Mart 2016 |30 Mayıs 2020 |
| [1.5.1](#1.5.1) |31 Aralık 2015 |30 Mayıs 2020 |
| [1.5.0](#1.5.0) |04 Aralık 2015 |30 Mayıs 2020 |
| [1.4.0](#1.4.0) |05 Ekim 2015 |30 Mayıs 2020 |
| [1.3.0](#1.3.0) |05 Ekim 2015 |30 Mayıs 2020 |
| [1.2.0](#1.2.0) |05 Ağustos 2015 |30 Mayıs 2020 |
| [1.1.0](#1.1.0) |09 Temmuz 2015 |30 Mayıs 2020 |
| 1.0.1 |12 Mayıs 2015 |30 Mayıs 2020 |
| [1.0.0](#1.0.0) |07 Nisan 2015 |30 Mayıs 2020 |
| 0.9.5-ürününün |09 Mar, 2015 |29 Şubat 2016 |
| 0.9.4-ürününün |17 Şubat 2015 |29 Şubat 2016 |
| 0.9.3-ürününün |13 Ocak 2015 |29 Şubat 2016 |
| 0.9.2-ürününün |19 Aralık 2014 |29 Şubat 2016 |
| 0.9.1-ürününün |19 Aralık 2014 |29 Şubat 2016 |
| 0.9.0-ürününün |10 Aralık 2014 |29 Şubat 2016 |

## <a name="faq"></a>SSS
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Ayrıca bkz.
Cosmos DB hakkında daha fazla bilgi için bkz. [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) hizmet sayfası.

