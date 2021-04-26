---
title: "Azure Cosmos DB: SQL Java API 'SI, SDK & kaynakları"
description: Sürüm tarihleri, kullanımdan kaldırma tarihleri ve SQL Java SDK Azure Cosmos DB her sürümü arasında yapılan değişiklikler dahil olmak üzere SQL Java API 'SI ve SDK hakkında bilgi edinin.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 04/06/2021
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 6644f495f28fb76503948c18354a5af0fcf832e5
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107364762"
---
# <a name="azure-cosmos-db-java-sdk-for-sql-api-release-notes-and-resources"></a>SQL API için Java SDK Azure Cosmos DB: sürüm notları ve kaynakları
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

Bu, zaman uyumlu işlemleri destekleyen SQL API 'SI için özgün Azure Cosmos DB eşitleme Java SDK v2 ' dir.

> [!IMPORTANT]  
> Bu, Azure Cosmos DB için en son Java SDK 'Sı *değildir* ! Projeniz için [Azure Cosmos DB Java SDK v4](sql-api-sdk-java-v4.md) kullanmayı düşünün. Yükseltmek için [Azure Cosmos DB Java SDK 'sı v4](migrate-java-v4-sdk.md) Kılavuzu ve [reaktör vs rxjava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-rxjava-guide.md) Kılavuzu 'ndaki yönergeleri izleyin. 
>

| | Bağlantılar |
|---|---|
|**SDK Indirmesi**|[Maven](https://search.maven.org/#search%7Cgav%7C1%7Cg%3A%22com.microsoft.azure%22%20AND%20a%3A%22azure-documentdb%22)|
|**API belgeleri**|[Java API başvuru belgeleri](/java/api/com.microsoft.azure.documentdb)|
|**SDK 'ya katkıda bulunma**|[GitHub](https://github.com/Azure/azure-documentdb-java/)|
|**Kullanmaya başlama**|[Java SDK 'sını kullanmaya başlama](./create-sql-api-java.md)|
|**Web uygulaması öğreticisi**|[Azure Cosmos DB ile Web uygulaması geliştirme](sql-api-java-application.md)|
|**Desteklenen en düşük çalışma zamanı**|[Java Development Kit (JDK) 7 +](/java/azure/jdk/)|

## <a name="release-notes"></a>Sürüm notları

### <a name="261"></a><a name="2.6.1"></a>2.6.1
* Service Interop aracılığıyla bir sorguyu işlerken hata düzeltildi.

### <a name="260"></a><a name="2.6.0"></a>2.6.0
* Zaman noktasından değişiklik akışını sorgulamak için destek eklendi.

### <a name="251"></a><a name="2.5.1"></a>2.5.1
* DocumentCollection sorgusunda birincil bölüm önbelleği sorununu düzeltir.

### <a name="250"></a><a name="2.5.0"></a>2.5.0
* 449 için yeniden deneme özel yapılandırma desteği eklendi.

### <a name="247"></a><a name="2.4.7"></a>2.4.7
* Bağlantı havuzu zaman aşımı sorununu düzeltir.
* İç yeniden denemelerle kimlik doğrulama belirteci yenilemeyi düzeltir.

### <a name="246"></a><a name="2.4.6"></a>2.4.6
* DatabaseAccount üzerinde doğru istemci tarafı çoğaltma ilkesi etiketi güncelleştirildi ve önbellekten databaseAccount yapılandırma okumaları yaptı.

### <a name="245"></a><a name="2.4.5"></a>2.4.5
* Kullanıcı Prangeıd sağlıyorsa geçersiz bölüm anahtar aralığı hatası durumunda yeniden denemeyi önleme.

### <a name="244"></a><a name="2.4.4"></a>2.4.4
* İyileştirilmiş bölüm anahtar aralığı önbelleği yenilemeleri.
* SDK 'nın bölüm bölme ipucunu sunucudan çözmeyeceği ve hatalı istemci tarafı yönlendirme önbellekleri yenilemesine neden olan senaryoyu düzeltir.

### <a name="242"></a><a name="2.4.2"></a>2.4.2 sections
* İyileştirilmiş koleksiyon önbelleği yenilemeleri.

### <a name="241"></a><a name="2.4.1"></a>2.4.1
* İstek tanılama dizesinden iç özel durum iletisini almak için destek eklendi.

### <a name="240"></a><a name="2.4.0"></a>2.4.0
* PartitionKeyDefinition üzerinde sürüm API 'si kullanıma sunuldu.

### <a name="230"></a><a name="2.3.0"></a>2.3.0
* Doğrudan mod için ayrı zaman aşımı desteği eklendi.

### <a name="223"></a><a name="2.2.3"></a>2.2.3
* Hizmetten null hata iletisi tüketiyor ve belge istemci özel durumu üretiliyor.

### <a name="222"></a><a name="2.2.2"></a>2.2.2
* Yuva bağlantısı geliştirmesi, SoKeepAlive varsayılan değeri.

### <a name="220"></a><a name="2.2.0"></a>2.2.0
* İstek tanılama dizesi desteği eklendi.

### <a name="213"></a><a name="2.1.3"></a>2.1.3
* Karma v2 için PartitionKey içindeki hata düzeltildi.

### <a name="212"></a><a name="2.1.2"></a>2.1.2
* Bileşik dizinler için destek eklendi.
* Yenilemeyi zorlamak için genel uç nokta yöneticisinde hata düzeltildi.
* Doğrudan modda ön koşullara sahip olan uptts için hata düzeltildi.

### <a name="211"></a><a name="2.1.1"></a>2.1.1
* Ağ Geçidi adres önbelleğinde hata düzeltildi.

### <a name="210"></a><a name="2.1.0"></a>2.1.0
* Doğrudan mod için çok bölgeli yazma desteği eklendi.
* Bir proxy 'den ServiceUnavailable özel durumları olarak oluşturulan IOException işleme desteği eklendi.
* Uç nokta bulma yeniden deneme ilkesinde hata düzeltildi.
* BaseDatabaseAccountConfigurationProvider içinde null işaretçi özel durumlarının bulunmadığından emin olmak için bir hata düzeltildi.
* Sorgu yineleyicisinin null değeri döndürmediğinden emin olmak için hata düzeltildi.
* Büyük PartitionKey 'e izin verildiğinden emin olmak için hata düzeltildi

### <a name="200"></a><a name="2.0.0"></a>2.0.0
* Ağ Geçidi modu için çok bölgeli yazma desteği eklendi.

### <a name="1164"></a><a name="1.16.4"></a>1.16.4
* Bir sorgu için okuma bölümü anahtar aralıklarında hata düzeltildi.

### <a name="1163"></a><a name="1.16.3"></a>1.16.3
* DirectHttps modunda devamlılık belirteci üstbilgi boyutu ayarlanırken hata düzeltildi.

### <a name="1162"></a><a name="1.16.2"></a>1.16.2
* Eklenen akış, destek üzerinden başarısız oldu.
* Özel meta veriler için destek eklendi.
* İyileştirilmiş oturum işleme mantığı.
* Bölüm anahtar aralığı önbelleğinde hata düzeltildi.
* Doğrudan modda bir NPE hatası düzeltildi.

### <a name="1161"></a><a name="1.16.1"></a>1.16.1
* Benzersiz dizin desteği eklendi.
* Akış seçeneklerinde devamlılık belirteci boyutunu sınırlama desteği eklendi.
* JSON serileştirmesi (zaman damgası) içinde bir hata düzeltildi.
* JSON serileştirme (enum) hatası düzeltildi.
* Com. fasterxml. Jackson. Core: Jackson-DataBind 'a bağımlılık, 'nın 2.9.5 sürümüyle sürümüne yükseltildi.

### <a name="1160"></a><a name="1.16.0"></a>1.16.0
* Doğrudan mod için geliştirilmiş bağlantı havuzu.
* OrderBy olmayan çapraz bölüm sorgusu için önceden getirme iyileştirmesi geliştirildi.
* İyileştirilmiş UUID oluşturma.
* İyileştirilmiş oturum tutarlılığı mantığı.
* MultiPolygon için destek eklendi.
* Koleksiyon için bölüm anahtarı aralığı Istatistikleri için destek eklendi.
* Çok bölgeli destek içindeki bir hata düzeltildi.

### <a name="1150"></a><a name="1.15.0"></a>1.15.0
* İyileştirilmiş JSON serileştirme performansı.
* Bu SDK sürümü, [Azure Cosmos DB öykünücü](https://aka.ms/cosmosdb-emulator)'un en son sürümünü gerektirir.

### <a name="1140"></a><a name="1.14.0"></a>1.14.0
* Microsoft arkadaş kitaplıkları için dahili değişiklikler.

### <a name="1130"></a><a name="1.13.0"></a>1.13.0
* Tek bölümlü anahtar aralıklarını okurken bir sorun düzeltildi.
* RESOURCEID ayrıştırma sırasında, kısa adlara sahip veritabanını etkileyen bir sorun düzeltildi.
* Bölüm anahtarı kodlamasıyla bir sorun düzeltildi.

### <a name="1120"></a><a name="1.12.0"></a>1.12.0
* Bölüm bölmeler sırasında işleme istemek için kritik hata düzeltmeleri.
* Güçlü ve Boundedstalet tutarlılık düzeylerinde bir sorun düzeltildi.

### <a name="1110"></a><a name="1.11.0"></a>1.11.0
* Tutarlık ön eki olarak adlandırılan yeni bir tutarlılık düzeyi için destek eklendi.
* Koleksiyonda oturum modunda okuma hatası düzeltildi.

### <a name="1100"></a><a name="1.10.0"></a>1.10.0
* 2.500 RU/sn olarak düşük olan ve 100 RU/sn 'lik artışlarla ölçeklendirerek bölümlenmiş koleksiyon için destek etkinleştirildi.
* Yerel derlemede, bazı sorgularda NullRef özel durumuna neden olabilecek bir hata düzeltildi.

### <a name="196"></a><a name="1.9.6"></a>1.9.6
* Sorgu altyapısı yapılandırmasında, Ağ Geçidi modundaki sorgular için özel durumlara neden olabilecek bir hata düzeltildi.
* Oturum kapsayıcısında, koleksiyon oluşturulduktan hemen sonra istekler için "sahip kaynak bulunamadı" özel durumuna neden olabilecek birkaç hata düzeltildi.

### <a name="195"></a><a name="1.9.5"></a>1.9.5
* Toplama sorguları (COUNT, MIN, MAX, SUM ve AVG) için destek eklendi. Bkz. [toplama desteği](sql-query-aggregate-functions.md).
* Değişiklik akışı için destek eklendi.
* RequestOptions. Setpopulatequotaınfo aracılığıyla koleksiyon kota bilgileri için destek eklendi.
* RequestOptions. setScriptLoggingEnabled aracılığıyla depolanan yordam betiği günlüğü için destek eklendi.
* DirectHttps modundaki sorgunun, kısıtlama hatalarıyla karşılaşıldığında yanıt vermemesine neden olan bir hata düzeltildi.
* Oturum tutarlılığı modunda bir hata düzeltildi.
* İstek hızı yüksek olduğunda HttpContext 'te NullReferenceException 'e neden olabilecek bir hata düzeltildi.
* DirectHttps modunun performansı geliştirildi.

### <a name="194"></a><a name="1.9.4"></a>1.9.4
* ConnectionPolicy. setProxy () API 'si ile basit istemci örneği tabanlı proxy desteği eklendi.
* DocumentClient örneğini düzgün şekilde kapatmak için DocumentClient. Close () API 'SI eklendi.
* Sorgu planını ağ geçidi yerine yerel derlemeden türeterek doğrudan bağlantı modunda sorgu performansı geliştirildi.
* Kullanıcıların POJO 'da Jsonıgnoreproperties tanımlamasına gerek kalmaması için FAIL_ON_UNKNOWN_PROPERTIES = false olarak ayarlayın.
* DOLAYıSıYLA SLF4J kullanmak için yeniden düzenlenmiş günlüğe kaydetme.
* Tutarlılık okuyucusunda diğer birkaç hata düzeltildi.

### <a name="193"></a><a name="1.9.3"></a>1.9.3
* Doğrudan bağlantı modunda bağlantı sızıntılarını engellemek için bağlantı yönetiminde hata düzeltildi.
* ÜSTTEKI sorguda bir hata düzeltildi ve bu, NullReference özel durumu oluşturmayabilir.
* İç önbellekler için ağ çağrısı sayısını azaltarak performansı artırdık.
* Daha iyi sorun giderme için DocumentClientException içinde durum kodu, ActivityId ve Istek URI 'SI eklendi.

### <a name="192"></a><a name="1.9.2"></a>1.9.2
* Tutarlılık için bağlantı yönetiminde bir sorun düzeltildi.

### <a name="191"></a><a name="1.9.1"></a>1.9.1
* Boundedstatayetlik tutarlılığı düzeyi için destek eklendi.
* Bölümlenmiş koleksiyonlara yönelik CRUD işlemlerine yönelik doğrudan bağlantı desteği eklendi.
* SQL ile bir veritabanını sorgularken hata düzeltildi.
* Oturum belirtecinin yanlış ayarlanbildiği oturum önbelleğinde bir hata düzeltildi.

### <a name="190"></a><a name="1.9.0"></a>1.9.0
* Çapraz bölüm Paralel sorguları için destek eklendi.
* Bölümlenmiş koleksiyonlar için üst/sıra sorgularına yönelik destek eklendi.
* Güçlü tutarlılık desteği eklendi.
* Doğrudan bağlantı kullanılırken ad tabanlı istekler için destek eklendi.
* Etkinlik kimliğinin tüm istek yeniden denemeleri arasında tutarlı kalmasını sağlamak için düzeltildi.
* Aynı ada sahip bir koleksiyon yeniden oluşturulurken oturum önbelleğiyle ilgili bir hata düzeltildi.
* Coğrafi olarak uzamsal sorgular için koleksiyon dizin oluşturma ilkesi belirtilirken Çokgen ve LineString veri türleri eklendi.
* Java 1,8 için Java belgesi ile ilgili sorunlar düzeltildi.

### <a name="181"></a><a name="1.8.1"></a>1.8.1
* PartitionKeyDefinitionMap 'te tek bölümlü koleksiyonları önbelleğe almak için bir hata düzeltildi ve ek getirme bölüm anahtarı istekleri yapılamıyor.
* Yanlış bir bölüm anahtarı değeri sağlandığında yeniden denenmeyen bir hata düzeltildi.

### <a name="180"></a><a name="1.8.0"></a>1.8.0
* Çok bölgeli veritabanı hesapları için destek eklendi.
* En fazla yeniden deneme girişimlerini özelleştirme ve en fazla yeniden deneme bekleme süresi gibi kısıtlanmış isteklerde otomatik yeniden deneme desteği eklendi.  Bkz. RetryOptions and ConnectionPolicy. getRetryOptions ().
* Kullanımdan kaldırılan IPartitionResolver tabanlı özel bölümlendirme kodu. Lütfen daha yüksek depolama ve aktarım hızı için bölümlenmiş koleksiyonlar kullanın.

### <a name="171"></a><a name="1.7.1"></a>1.7.1
* Hız sınırlaması için yeniden deneme ilkesi desteği eklendi.  

### <a name="170"></a><a name="1.7.0"></a>1.7.0
* Belgeler için yaşam süresi (TTL) desteği eklendi.

### <a name="160"></a><a name="1.6.0"></a>1.6.0
* [Bölümlenmiş koleksiyonlar](partitioning-overview.md) ve [Kullanıcı tanımlı performans düzeyleri](performance-levels.md)uygulandı.

### <a name="151"></a><a name="1.5.1"></a>1.5.1
* Diğer SDK 'lar ile tutarlı olması için küçük endian 'de karma değerler oluşturmak üzere Karmadaki bir hata düzeltildi.

### <a name="150"></a><a name="1.5.0"></a>1.5.0
* Birden çok bölüm genelinde uygulama oluşturmaya yardımcı olmak için karma & aralığı bölüm Çözümleyicileri ekleyin.

### <a name="140"></a><a name="1.4.0"></a>1.4.0
* Upsert uygulama. Upsert özelliği desteklemek için yeni upsertXXX yöntemleri eklenmiştir.
* KIMLIK tabanlı yönlendirmeyi uygulayın. Ortak API değişikliği yok, tüm değişiklikler iç.

### <a name="130"></a><a name="1.3.0"></a>1.3.0
* Sürüm numarasını diğer SDK 'lara hizalı olarak getirmek için yayın atlandı

### <a name="120"></a><a name="1.2.0"></a>1.2.0
* Jeo-uzamsal dizini destekler
* Tüm kaynaklar için ID özelliğini doğrular. Kaynak kimlikleri,?,/, #, karakter içeremez \, veya boşluk ile bitemez.
* Yeni "Dizin dönüştürme ilerlemesi" üst bilgisini Resourcereslik 'e ekler.

### <a name="110"></a><a name="1.1.0"></a>1.1.0
* V2 dizin oluşturma ilkesini uygular

### <a name="100"></a><a name="1.0.0"></a>1.0.0
* GA SDK

## <a name="release-and-retirement-dates"></a>Yayın ve emeklilik tarihleri

Microsoft daha yeni/desteklenen bir sürüme geçişi kolaylaştırmak için bir SDK'yı kullanımdan kaldırmadan en az **12 ay** önce bildirim sağlar. Yeni özellikler ve işlevler ve iyileştirmeler yalnızca geçerli SDK 'ya eklenir, bu nedenle en son SDK sürümüne her zaman olabildiğince erken yükseltmeniz önerilir.

> [!WARNING]
> 30 Mayıs 2020 ' den sonra, Azure Cosmos DB artık hata düzeltmeleri yapamayacak, yeni özellikler ekleyecek ve SQL API için Azure Cosmos DB Java SDK 'sının 1. x sürümleri için destek sunacaktır. Yükseltmemeyi tercih ederseniz, SDK'nın 1.x sürümünden gönderilen isteklere Azure Cosmos DB hizmeti tarafından yanıt verilmeye devam edilecektir.
>
> 29 Şubat 2016 ' den sonra Azure Cosmos DB artık hata düzeltmeleri yapamayacak, yeni özellikler ekleyecek ve SQL API için Azure Cosmos DB Java SDK 'sının 0. x sürümlerine destek sağlamalıdır. Yükseltmemeyi tercih ediyorsanız, SDK 'nın 0. x sürümünden gönderilen istekler Azure Cosmos DB hizmeti tarafından sunulmayı sürdürmeye devam edecektir.


| Sürüm | Yayın Tarihi | Kullanımdan Kaldırma Tarihi |
| --- | --- | --- |
| [2.6.1](#2.6.1) |17 Aralık 2020 |--- |
| [2.6.0](#2.6.0) |16 Temmuz 2020 |--- |
| [2.5.1](#2.5.1) |03 Haziran 2020 |--- |
| [2.5.0](#2.5.0) |12 Mayıs 2020 |--- |
| [2.4.7](#2.4.7) |20 Şubat 2020 |--- |
| [2.4.6](#2.4.6) |24 Ocak 2020 |--- |
| [2.4.5](#2.4.5) |10 Kasım 2019 |--- |
| [2.4.4](#2.4.4) |24 Eki, 2019 |--- |
| [2.4.2 sections](#2.4.2) |26 Eyl, 2019 |--- |
| [2.4.1](#2.4.1) |18 Temmuz 2019 |--- |
| [2.4.0](#2.4.0) |04 Mayıs 2019 |--- |
| [2.3.0](#2.3.0) |24 Nis, 2019 |--- |
| [2.2.3](#2.2.3) |16 Nis 2019 |--- |
| [2.2.2](#2.2.2) |05 Nis, 2019 |--- |
| [2.2.0](#2.2.0) |27 Mar, 2019 |--- |
| [2.1.3](#2.1.3) |13 Mar, 2019 |--- |
| [2.1.2](#2.1.2) |09 Mar, 2019 |--- |
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
Cosmos DB hakkında daha fazla bilgi edinmek için [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) hizmeti sayfasına bakın.
