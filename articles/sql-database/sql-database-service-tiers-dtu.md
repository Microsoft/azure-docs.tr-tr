---
title: Hizmet katmanları-DTU tabanlı satın alma modeli
description: İşlem ve depolama boyutları sağlamak üzere tek ve havuza alınmış veritabanları için DTU tabanlı satın alma modelindeki hizmet katmanları hakkında bilgi edinin.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 09/06/2019
ms.openlocfilehash: 6d4c9dd08f2d00bc12f041748cc78363c9abecbd
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822514"
---
# <a name="service-tiers-in-the-dtu-based-purchase-model"></a>DTU tabanlı satın alma modelindeki hizmet katmanları

DTU tabanlı satın alma modelindeki hizmet katmanları, sabit bir dahil edilen depolama alanı, yedeklemeler için sabit saklama süresi ve sabit fiyat içeren bir dizi işlem boyutuna göre farklılaştırılır. DTU tabanlı satın alma modelindeki tüm hizmet katmanları, işlem boyutlarının en az [kapalı kalma süresiyle](https://azure.microsoft.com/support/legal/sla/sql-database/v1_2/)değiştirilmesi esnekliği sağlar; Ancak, bağlantının veritabanına kısa bir süre boyunca kaybedildiği, yeniden deneme mantığı kullanılarak azaltılan bir süre içinde bir anahtar vardır. Tek veritabanları ve elastik havuzlar, hizmet katmanı ve işlem boyutu temelinde saatlik olarak faturalandırılır.

> [!IMPORTANT]
> SQL veritabanı yönetilen örneği, DTU tabanlı satın alma modelini desteklemez. Daha fazla bilgi için bkz. [Azure SQL veritabanı yönetilen örneği](sql-database-managed-instance.md).
> [!NOTE]
> Sanal çekirdek tabanlı hizmet katmanları hakkında daha fazla bilgi için bkz. [sanal çekirdek tabanlı hizmet katmanları](sql-database-service-tiers-vcore.md). DTU tabanlı hizmet katmanlarını ve sanal çekirdek tabanlı hizmet katmanlarını farklılaştırmayı sağlamak hakkında bilgi için bkz. [Azure SQL veritabanı satın alma modelleri](sql-database-purchase-models.md).

## <a name="compare-the-dtu-based-service-tiers"></a>DTU tabanlı hizmet katmanlarını karşılaştırın

Bir hizmet katmanını seçmek, birincil olarak iş sürekliliği, depolama ve performans gereksinimlerine bağlıdır.

||Temel|Standart|Premium|
| :-- | --: |--:| --:|
|Hedef iş yükü|Geliştirme ve üretim|Geliştirme ve üretim|Geliştirme ve üretim|
|Çalışma Süresi SLA'sı|%99,99|%99,99|%99,99|
|Maksimum yedekleme saklama|7 gün|35 gün|35 gün|
|CPU|Düşük|Düşük, orta, yüksek|Orta, yüksek|
|GÇ verimlilik (yaklaşık) |DTU başına 1-5 ıOPS| DTU başına 1-5 ıOPS | DTU başına 25 ıOPS|
|GÇ gecikme süresi (yaklaşık)|5 ms (okuma), 10 MS (yazma)|5 ms (okuma), 10 MS (yazma)|2 ms (okuma/yazma)|
|Columnstore dizin oluşturma |Yok|S3 ve üzeri|Destekleniyor|
|Bellek içi OLTP|Yok|Yok|Destekleniyor|
|||||

> [!NOTE]
> Azure 'ı araştırmak için Azure Ücretsiz hesabıyla birlikte, temel hizmet katmanında ücretsiz bir Azure SQL veritabanı edinebilirsiniz. Daha fazla bilgi için bkz. [Azure Ücretsiz hesabınızla yönetilen bulut veritabanı oluşturma](https://azure.microsoft.com/free/services/sql-database/).

## <a name="single-database-dtu-and-storage-limits"></a>Tek veritabanı DTU ve depolama sınırları

İşlem boyutları, tek veritabanları için veritabanı Işlem birimleri (DTU 'Lar) ve elastik havuzlar için elastik veritabanı Işlem birimleri (eDTU 'lar) bakımından ifade edilir. DTU 'Lar ve eDTU 'lar hakkında daha fazla bilgi için bkz. [DTU tabanlı satın alma modeli](sql-database-purchase-models.md#dtu-based-purchasing-model)

||Temel|Standart|Premium|
| :-- | --: | --: | --: |
| Maksimum depolama boyutu | 2 GB | 1 TB | 4 TB  |
| En fazla DTU | 5 | 3000 | 4000 | 
|||||

> [!IMPORTANT]
> Bazı durumlarda, kullanılmayan alanı geri kazanmak için bir veritabanını daraltmanız gerekebilir. Daha fazla bilgi için bkz. [Azure SQL veritabanı 'nda dosya alanını yönetme](sql-database-file-space-management.md).

## <a name="elastic-pool-edtu-storage-and-pooled-database-limits"></a>Elastik havuz eDTU, depolama ve havuza alınmış veritabanı limitleri

| | **Temel** | **Standart** | **Premium** |
| :-- | --: | --: | --: |
| Veritabanı başına en fazla depolama boyutu  | 2 GB | 1 TB | 1 TB |
| Havuz başına en fazla depolama boyutu | 156 GB | 4 TB | 4 TB |
| Veritabanı başına en fazla eDTU | 5 | 3000 | 4000 |
| Havuz başına en fazla eDTU | 1600 | 3000 | 4000 |
| Havuz başına en fazla veritabanı sayısı | 500  | 500 | 100 |
|||||

> [!IMPORTANT]
> Premium katmanda 1 TB 'den fazla depolama alanı şu anda tüm bölgelerde kullanılabilir: Çin Doğu, Çin Kuzey, Almanya Orta, Almanya Kuzeydoğu, Orta Batı ABD, US DoD bölgeleri ve ABD Devlet Merkezi. Bu bölgelerde Premium katmanda depolama için 1 TB üst sınırı uygulanır.  Daha fazla bilgi için bkz. [P11-P15 geçerli sınırlamalar](sql-database-single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb).  
> [!IMPORTANT]
> Bazı durumlarda, kullanılmayan alanı geri kazanmak için bir veritabanını daraltmanız gerekebilir. Daha fazla bilgi için bkz. [Azure SQL veritabanı 'nda dosya alanını yönetme](sql-database-file-space-management.md).

## <a name="dtu-benchmark"></a>DTU kıyaslama

Her DTU ölçüsüne ilişkin fiziksel Özellikler (CPU, bellek, GÇ), gerçek dünya veritabanı iş yükünü taklit eden bir kıyaslama kullanılarak ayarlanır.

### <a name="correlating-benchmark-results-to-real-world-database-performance"></a>Kıyaslama sonuçlarını gerçek dünya veritabanı performansına karşı ilişkilendirme

Tüm kıyaslamalar temsilci ve yalnızca bir değer olduğunu anlamak önemlidir. Kıyaslama uygulamasıyla elde edilen işlem ücretleri, diğer uygulamalarla sağlananlarla aynı olmayacaktır. Kıyaslama, bir dizi tablo ve veri türü içeren bir şemaya karşı çalıştırılan farklı işlem türleri koleksiyonunu içerir. Kıyaslama, tüm OLTP iş yükleri için ortak olan aynı temel işlemleri yaparken, belirli bir veritabanı veya uygulama sınıfını temsil etmez. Kıyaslama hedefi, işlem boyutları arasında ölçeği büyütme veya küçültme sırasında beklenen bir veritabanının göreli performansına yönelik makul bir kılavuz sağlamaktır. Gerçekte veritabanları farklı boyutlarda ve karmaşıklıklardır, farklı iş yüklerinin farklı karışımlarından karşılaşır ve farklı yollarla yanıt verir. Örneğin, GÇ yoğun bir uygulama GÇ eşiklerini daha erken döndürebilir veya CPU kullanımı yoğun bir uygulama CPU sınırlarına daha erken gelebilir. Belirli bir veritabanının, artan yük kapsamındaki kıyaslanla aynı şekilde ölçeklenmeyeceği garantisi yoktur.

Kıyaslama ve yöntemi aşağıda daha ayrıntılı olarak açıklanmıştır.

### <a name="benchmark-summary"></a>Kıyaslama Özeti

Kıyaslama, çevrimiçi işlem işleme (OLTP) iş yüklerinde en sık oluşan temel veritabanı işlemleri karışımının performansını ölçer. Kıyaslama, bulut bilgi işlem ile tasarlanmış olsa da, veritabanı şeması, veri popülasyonu ve işlemler, OLTP iş yükleri içinde en sık kullanılan temel öğelerin genel olarak temsili olacak şekilde tasarlanmıştır.

### <a name="schema"></a>Şema

Şema, çok çeşitli işlemleri desteklemek için yeterli sayıda ve karmaşıklığa sahip olacak şekilde tasarlanmıştır. Kıyaslama, altı tablodan oluşan bir veritabanında çalışır. Tablolar üç kategoriye ayrılır: sabit boyutlu, ölçekleme ve büyüyen. İki sabit boyutlu tablo vardır; Üç ölçeklendirme tablosu; ve büyüyen bir tablo. Sabit boyutlu tablolarda sabit sayıda satır vardır. Ölçek tablolarının veritabanı performansına orantılı, ancak kıyaslama sırasında değişmeyen bir kardinalite vardır. Büyüyen tablo, ilk yükün ölçekleme tablosu gibi boyutlandırılır, ancak daha sonra, bir satır eklenir ve silinirken kıyaslanın çalışma sırasında kardinalite değişir.

Şema, tamsayı, sayısal, karakter ve tarih/saat dahil olmak üzere veri türlerinin bir karışımını içerir. Şema, birincil ve ikincil anahtarları içerir, ancak yabancı anahtarlar içermez; tablolar arasında hiçbir bilgi tutarlılığı kısıtlaması yoktur.

Veri oluşturma programı ilk veritabanı için verileri oluşturur. Tamsayı ve sayısal veriler çeşitli stratejiler ile oluşturulur. Bazı durumlarda, değerler bir aralığa rastgele dağıtılır. Diğer durumlarda, belirli bir dağıtımın korunduğundan emin olmak için bir değerler kümesi rastgele olarak sessizdir. Metin alanları, gerçekçi arama verileri oluşturmak için ağırlıklı bir sözcük listesinden oluşturulur.

Veritabanı "ölçek faktörü" temelinde boyutlandırılır. Ölçek faktörü (SF olarak kısaltılır), ölçeklendirmenin ve büyüyen tabloların kardinalitesini belirler. Aşağıda açıklandığı gibi, kullanıcılar ve hız bölümünde, veritabanı boyutu, Kullanıcı sayısı ve en yüksek performans ölçeği, birbirleriyle orantılı olarak ölçeklendirilir.

### <a name="transactions"></a>İşlemler

İş yükü, aşağıdaki tabloda gösterildiği gibi dokuz işlem türünden oluşur. Her işlem, veritabanı altyapısı ve sistem donanımında belirli bir sistem özellikleri kümesini, diğer işlemlerden yüksek karşıtlığa göre vurgulamak için tasarlanmıştır. Bu yaklaşım, farklı bileşenlerin genel performansa etkilerini değerlendirmeyi kolaylaştırır. Örneğin, "okuma ağır" işlemi diskten önemli sayıda okuma işlemi üretir.

| İşlem türü | Açıklama |
| --- | --- |
| Lite 'ı oku |SEÇIN bellek içi; salt okunurdur |
| Ortamı oku |SEÇIN genellikle bellek içi; salt okunurdur |
| Ağır oku |SEÇIN genellikle bellek içi değil; salt okunurdur |
| Lite güncelleştirme |Update bellek içi; okuma-yazma |
| Yoğun güncelleştirme |Update genellikle bellek içi değil; okuma-yazma |
| Lite Ekle |EKLEYIN bellek içi; okuma-yazma |
| Ağır Ekle |EKLEYIN genellikle bellek içi değil; okuma-yazma |
| Sil |SILMELI bellek içi olmayan, bellek içi karışımı; okuma-yazma |
| Yoğun CPU |SEÇIN bellek içi; görece ağır CPU yükü; salt okunurdur |

### <a name="workload-mix"></a>İş yükü karışımı

İşlemler, aşağıdaki genel karışımdan ağırlıklı bir dağılımıdan rastgele seçilir. Genel karışımı yaklaşık 2:1 okuma/yazma oranına sahiptir.

| İşlem türü | Mix yüzdesi |
| --- | --- |
| Lite 'ı oku |35 |
| Ortamı oku |20 |
| Ağır oku |5 |
| Lite güncelleştirme |20 |
| Yoğun güncelleştirme |3 |
| Lite Ekle |3 |
| Ağır Ekle |2 |
| Sil |2 |
| Yoğun CPU |10 |

### <a name="users-and-pacing"></a>Kullanıcılar ve hız

Kıyaslama iş yükü, bir dizi eşzamanlı kullanıcı davranışının benzetimini yapmak için bir dizi bağlantı üzerinden işlem gönderen bir araçtan çalıştırılır. Tüm bağlantılar ve işlemler makine tarafından üretilmiş olsa da, kolaylık sağlaması için bu bağlantılara "kullanıcılar" olarak başvurduk. Her Kullanıcı diğer tüm kullanıcılardan bağımsız olarak çalışsa da, tüm kullanıcılar aşağıda gösterilen adımların aynı döngüsünü gerçekleştirir:

1. Bir veritabanı bağlantısı oluşturun.
2. Çıkmak için şu kadar Yinele:
   - Rastgele bir işlem (ağırlıklı bir dağılımı) seçin.
   - Seçili işlemi gerçekleştirin ve yanıt süresini ölçün.
   - Bir hız gecikmesi için bekleyin.
3. Veritabanı bağlantısını kapatın.
4. Çıkıp.

İlerleme gecikmesi (adım 2C), rastgele bir ortalama 1,0 saniyelik bir dağıtımla seçilir. Böylece her Kullanıcı ortalama olarak saniyede en çok bir işlem oluşturabilir.

### <a name="scaling-rules"></a>Ölçek kuralları

Kullanıcı sayısı, veritabanı boyutuna göre belirlenir (ölçek faktörü birimi cinsinden). Her beş ölçek faktörü birimi için bir kullanıcı vardır. İlerleme gecikmesi nedeniyle, bir Kullanıcı ortalama olarak saniyede en çok bir işlem oluşturabilir.

Örneğin, 500 (SF = 500) veritabanının ölçek faktörü 100 kullanıcıya sahip olur ve en yüksek hız olan 100 TPS elde edebilir. Daha yüksek bir TPS oranı sağlamak için daha fazla Kullanıcı ve daha büyük bir veritabanı gerekir.

### <a name="measurement-duration"></a>Ölçüm süresi

Geçerli bir Kıyaslama çalıştırması, en az bir saat için sabit durum ölçümü süresi gerektirir.

### <a name="metrics"></a>Ölçümler

Kıyaslama ve yanıt süresi ölçümlerinde bulunan önemli ölçümler.

- Verimlilik, kıyaslanmasındaki önemli performans ölçümüdür. Aktarım hızı, tüm işlem türlerini sayarak, zaman birimi başına işlem halinde raporlanır.
- Yanıt süresi, performans öngörülebilirlik ölçüdür. Yanıt süresi kısıtlaması, daha yüksek hizmet sınıfları aşağıda gösterildiği gibi daha sıkı bir yanıt süresi gereksinimine sahip olan hizmet sınıfına göre farklılık gösterir.

| Hizmet sınıfı | Verimlilik ölçüsü | Yanıt süresi gereksinimi |
| --- | --- | --- |
| Premium |Saniye başına işlem |0,5 saniye içinde 95. yüzdebirlik |
| Standart |Dakika başına işlem |1,0 saniye içinde 90. yüzdebirlik |
| Temel |Saat başına işlem |2,0 saniye içinde 80th yüzdebirlik |

## <a name="next-steps"></a>Sonraki adımlar

- Tek veritabanları için kullanılabilen belirli işlem boyutları ve depolama boyutu seçimleri hakkındaki ayrıntılar için bkz. [tek veritabanları Için SQL VERITABANı DTU tabanlı kaynak sınırları](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes).
- Elastik havuzlarda bulunan belirli işlem boyutları ve depolama boyutu seçimleri hakkındaki ayrıntılar için bkz. [SQL VERITABANı DTU tabanlı kaynak sınırları](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes).
