---
title: Oto vakum 'yi iyileştirme-PostgreSQL için Azure veritabanı-tek sunucu
description: Bu makalede, bir PostgreSQL için Azure veritabanı-tek sunucu üzerinde nasıl oto vakum iyileştirileceği açıklanır.
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: how-to
ms.date: 07/09/2020
ms.openlocfilehash: a94afc1ab970c2cd3f509c86efba4e455d46fd13
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96012570"
---
# <a name="optimize-autovacuum-on-an-azure-database-for-postgresql---single-server"></a>PostgreSQL için Azure veritabanı 'nda oto vakum 'yi iyileştirin-tek sunucu

Bu makalede, bir PostgreSQL için Azure veritabanı sunucusu üzerinde oto 'nin nasıl etkili bir şekilde iyileştirileceği açıklanır.

## <a name="overview-of-autovacuum"></a>Oto vakum 'ye Genel Bakış

PostgreSQL, daha fazla veritabanı eşzamanlılık sağlamak için çok sürümlü eşzamanlılık denetimi (MVCC) kullanır. Her güncelleştirme bir INSERT ve delete ile sonuçlanır ve her silme işlemi, silinmek üzere işaretlenmiş satırlarda oluşur. Geçici işaret, daha sonra temizlenecek atılacak başlıkları tanımlar. Bu görevleri gerçekleştirmek için PostgreSQL bir vakum işi çalıştırır.

Bir vakum işi el ile veya otomatik olarak tetiklenebilir. Veritabanı ağır güncelleştirme veya silme işlemleri yaşdığında, daha eski olmayan tanımlama grupları mevcuttur. Veritabanı boştayken daha az ölü tanımlama grubu mevcuttur. Veritabanı yükü ağır olduğunda, vakum işlerinin *el ile* uygun hale gelmesini sağlayan çok daha sık bir işlem yapmanız gerekir.

Otomatik Vakum, ayarlamadan yapılandırılabilir ve faydalanır. PostgreSQL tarafından sunulan varsayılan değerler, ürünün her türlü cihazda çalıştığından emin olmak için TRY ile birlikte gönderilir. Bu cihazlar Raspberry PSIS 'yi içerir. İdeal yapılandırma değerleri aşağıdakilere bağlıdır:

- SKU ve depolama boyutu gibi toplam kullanılabilir kaynak.
- Kaynak kullanımı.
- Bağımsız nesne özellikleri.

## <a name="autovacuum-benefits"></a>Oto vakum avantajları

Zaman zaman Vakum yoksa, biriken atılacak tanımlama grupları şu şekilde olabilir:

- Daha büyük veritabanları ve tablolar gibi veri blobunun.
- Daha büyük olan alt dizinler.
- Artan g/ç.

## <a name="monitor-bloat-with-autovacuum-queries"></a>Oluşan şişirmeyi ile oto vakum sorguları izleyin

Aşağıdaki örnek sorgu, XYZ adlı bir tablodaki ölü ve canlı tanımlama gruplarının sayısını belirlemek için tasarlanmıştır:

```sql
SELECT relname, n_dead_tup, n_live_tup, (n_dead_tup/ n_live_tup) AS DeadTuplesRatio, last_vacuum, last_autovacuum FROM pg_catalog.pg_stat_all_tables WHERE relname = 'XYZ' order by n_dead_tup DESC;
```

## <a name="autovacuum-configurations"></a>Oto vakum yapılandırması

Oto vakum 'yi denetleyen yapılandırma parametreleri, iki önemli soruya verilen yanıtlara göre yapılır:

- Ne zaman başlaması gerekir?
- Başladıktan sonra ne kadar temiz olmalıdır?

Burada, önceki sorulara göre ve bazı kılavuzlarla birlikte güncelleştirebilmeniz için bazı oto vakum yapılandırma parametreleri verilmiştir.

Parametre|Açıklama|Varsayılan değer
---|---|---
autovacuum_vacuum_threshold|Herhangi bir tabloda vakum işleminin tetiklenmesi için gereken en az güncelleştirilmiş veya silinmiş tanımlama grubu sayısını belirtir. Varsayılan değer 50 ' dir. Bu parametreyi yalnızca PostgreSQL. conf dosyasında veya sunucu komut satırında ayarlayın. Tek tek tabloların ayarını geçersiz kılmak için tablo depolama parametrelerini değiştirin.|50
autovacuum_vacuum_scale_factor|Bir vakum işleminin tetiklenmesi saptarken autovacuum_vacuum_threshold eklenecek tablo boyutunun bir bölümünü belirtir. Varsayılan değer, tablo boyutunun yüzde 20 ' si olan 0,2 ' dir. Bu parametreyi yalnızca PostgreSQL. conf dosyasında veya sunucu komut satırında ayarlayın. Tek tek tabloların ayarını geçersiz kılmak için tablo depolama parametrelerini değiştirin.|0,2
autovacuum_vacuum_cost_limit|Otomatik Vakum işlemlerinde kullanılan maliyet sınırı değerini belirtir. -1 belirtilirse, varsayılan değer olan normal vacuum_cost_limit değeri kullanılır. Birden fazla çalışan varsa, bu değer çalışan bir oto çalışıya çalışanları arasında orantılı olarak dağıtılır. Her çalışan limitlerinin toplamı, bu değişkenin değerini aşamaz. Bu parametreyi yalnızca PostgreSQL. conf dosyasında veya sunucu komut satırında ayarlayın. Tek tek tabloların ayarını geçersiz kılmak için tablo depolama parametrelerini değiştirin.|-1
autovacuum_vacuum_cost_delay|Otomatik Vakum işlemlerinde kullanılan maliyet gecikmesi değerini belirtir. -1 belirtilirse, normal vacuum_cost_delay değeri kullanılır. Varsayılan değer 20 milisaniyedir. Bu parametreyi yalnızca PostgreSQL. conf dosyasında veya sunucu komut satırında ayarlayın. Tek tek tabloların ayarını geçersiz kılmak için tablo depolama parametrelerini değiştirin.|20 MS
autovacuum_naptime | Herhangi bir veritabanı üzerinde, oto vakum çalıştırmaları arasındaki en küçük gecikmeyi belirtir. Her turunda, Daemon, veritabanı ve sorunlar vakum ' i inceler ve bu veritabanındaki tablolar için gerektiğinde komutları çözümler. Gecikme saniye cinsinden ölçülür. Bu parametreyi yalnızca PostgreSQL. conf dosyasında veya sunucu komut satırında ayarlayın.| 15 s
autovacuum_max_workers | Herhangi bir anda çalışabilen, oto olmayan bir başlatıcı dışında, oto Vakum işlem sayısının üst sınırını belirtir. Varsayılan değer üç ' dir. Bu parametreyi yalnızca sunucu başlangıcında ayarlayın.|3

Tek tek tabloların ayarlarını geçersiz kılmak için tablo depolama parametrelerini değiştirin.

## <a name="autovacuum-cost"></a>Oto vakum maliyeti

Bir vakum işlemi çalıştırmanın "maliyetleri" aşağıda verilmiştir:

- Vakum 'nin üzerinde çalıştığı veri sayfaları kilitlidir.
- İşlem ve bellek, bir vakum işi çalışırken kullanılır.

Sonuç olarak, vakum işleri çok sık veya çok seyrek çalıştırılmayın. Bir vakum işinin iş yüküne uyum sağlaması gerekir. Her birinin avantajları nedeniyle tüm oto parametresi değişikliklerini test edin.

## <a name="autovacuum-start-trigger"></a>Oto vakum başlangıç tetikleyicisi

Yok sayılma başlıkları sayısı autovacuum_vacuum_threshold + autovacuum_vacuum_scale_factor * relbaşlıkların aştığında, oto değeri tetiklenir. Burada, relbaşlıkların bir sabittir.

Temizleme işleminden temizlik, veritabanı yüküne devam etmelidir. Aksi takdirde, depolama alanı tükenmeyebilir ve sorgularda genel bir yavaşlama yaşayabilirsiniz. Zaman içinde, bir vakum işleminin ölü tanımlama gruplarını Temizleme hızının, atılacak tanımlama gruplarının oluşturulma hızına eşit olması gerekir.

Birçok güncelleştirme ve silme içeren veritabanlarının daha eski bir tanımlama grubu vardır ve daha fazla alana ihtiyacı vardır. Genellikle, çok sayıda güncelleştirme içeren veritabanları ve autovacuum_vacuum_scale_factor düşük değerden ve autovacuum_vacuum_threshold avantajları silinir. Düşük değerler, ölü tanımlama gruplarının aralıklı birikmesi yapılmasını engeller. Vacuuming gereksinimi daha az acil olduğundan, her iki parametre için de daha büyük değerler kullanabilirsiniz. Sık Vacuuming, işlem ve bellek maliyetlerine göre gelir.

Yüzde 20 ' nin varsayılan ölçek faktörü, düşük bir ölü tanımlama grubu yüzdesi olan tablolarda iyi sonuç verir. Bu, yüksek oranda ölü tanımlama gruplarının bulunduğu tablolarda iyi çalışmaz. Örneğin, 20 GB 'lik bir tabloda, bu ölçek faktörü 4 GB 'lik ölü tanımlama grubuna çevrilir. 1 TB 'lik bir tabloda, 200 GB 'lik ölü tanımlama grubu vardır.

PostgreSQL ile, bu parametreleri tablo düzeyinde veya örnek düzeyinde ayarlayabilirsiniz. Bugün, bu parametreleri yalnızca PostgreSQL için Azure veritabanı 'nda tablo düzeyinde ayarlayabilirsiniz.

## <a name="estimate-the-cost-of-autovacuum"></a>Oto vakum maliyetini tahmin etme

Oto vakum çalıştırmak "maliyetlidir" ve vakum işlemlerinin çalışma zamanının denetlenmesine yönelik parametreler vardır. Aşağıdaki parametreler, çalışma vakum maliyetini tahmin etmeye yardımcı olur:

- vacuum_cost_page_hit = 1
- vacuum_cost_page_miss = 10
- vacuum_cost_page_dirty = 20

Vakum işlemi, kullanılmayan tanımlama grupları için fiziksel sayfaları ve denetimleri okur. Shared_buffers her sayfanın maliyeti 1 (vacuum_cost_page_hit) olarak kabul edilir. Diğer tüm sayfalar, yok sayılma grupları varsa 20 (vacuum_cost_page_dirty), yok sayılma başlıkları yoksa 10 (vacuum_cost_page_miss) olarak kabul edilir. İşlem autovacuum_vacuum_cost_limit aştığında vakum işlemi durduruluyor.

Sınıra ulaşıldığında, işlem yeniden başlatılmadan önce autovacuum_vacuum_cost_delay parametresi tarafından belirtilen süre için uyku moduna geçer. Sınıra ulaşılırsa, oto, autovacuum_nap_time parametresi tarafından belirtilen değerden sonra başlar.

Özet ' de autovacuum_vacuum_cost_delay ve autovacuum_vacuum_cost_limit parametreleri, ne kadar veri temizlemesine izin verileceğini denetler. Varsayılan değerlerin çoğu fiyatlandırma katmanı için çok düşük olduğunu unutmayın. Bu parametrelerin en uygun değerleri fiyatlandırma katmanına bağımlıdır ve buna uygun şekilde yapılandırılmalıdır.

Autovacuum_max_workers parametresi, eşzamanlı olarak çalışabilecek en yüksek sayıdaki oto sayısını belirler.

PostgreSQL ile, bu parametreleri tablo düzeyinde veya örnek düzeyinde ayarlayabilirsiniz. Bugün, bu parametreleri yalnızca PostgreSQL için Azure veritabanı 'nda tablo düzeyinde ayarlayabilirsiniz.

## <a name="optimize-autovacuum-per-table"></a>Her tablo için oto vakum 'ı iyileştirme

Tablo başına önceki tüm yapılandırma parametrelerini yapılandırabilirsiniz. Aşağıda bir örnek verilmiştir:

```sql
ALTER TABLE t SET (autovacuum_vacuum_threshold = 1000);
ALTER TABLE t SET (autovacuum_vacuum_scale_factor = 0.1);
ALTER TABLE t SET (autovacuum_vacuum_cost_limit = 1000);
ALTER TABLE t SET (autovacuum_vacuum_cost_delay = 10);
```

Oto vakum, tablo başına zaman uyumlu bir işlemdir. Bir tablonun sahip olduğu kullanılmayan başlıkların daha büyük yüzdesi, "maliyet" i de oto vakum. Yüksek oranda güncelleştirme ve silmeleri olan tabloları birden çok tabloya ayırabilirsiniz. Tabloları bölmek, paralel hale getirmek oto ve bir tabloda bir bütün olarak bir tablo üzerinde tamamen vakum sağlamak için "maliyet" azaltmaya yardımcı olur. Çalışanların serbest bir şekilde zamanlandığından emin olmak için paralel otomatik çalışan sayısını da artırabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

' Nin nasıl kullanılacağı ve ayarlanacağı hakkında daha fazla bilgi edinmek için aşağıdaki PostgreSQL belgelerine bakın:

- [Bölüm 18, sunucu yapılandırması](https://www.postgresql.org/docs/9.5/static/runtime-config-autovacuum.html)
- [Bölüm 24, rutin veritabanı bakım görevleri](https://www.postgresql.org/docs/9.6/static/routine-vacuuming.html)
