---
title: MariaDB için Azure veritabanı 'nda sorgu deposu
description: Zaman içinde performansı izlemenize yardımcı olması için, MariaDB için Azure veritabanı 'nda sorgu depolama özelliği hakkında bilgi edinin.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 67ca6aa36166e8ae08bedec82441e45930976b80
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73604007"
---
# <a name="monitor-azure-database-for-mariadb-performance-with-query-store"></a>Sorgu deposu ile MariaDB için Azure veritabanı performansını izleme

**Uygulama hedefi:** MariaDB için Azure veritabanı 10,2

MariaDB için Azure veritabanı 'nda bulunan Query Store özelliği, sorgu performansını zamana göre izlemek için bir yol sağlar. Sorgu deposu, en uzun çalışan ve en fazla kaynak yoğunluklu sorguları hızlı bir şekilde bulmanıza yardımcı olarak performans sorunlarını basitleştirir. Sorgu deposu sorgular ve çalışma zamanı istatistikleri geçmişini otomatik olarak yakalar ve bunları gözden geçirmeniz için saklar. Veritabanı kullanım düzenlerini görebilmeniz için verileri zaman pencereleri ile ayırır. Tüm kullanıcılar, veritabanları ve sorgular için veriler, MariaDB için Azure veritabanı örneğindeki **MySQL** şema veritabanında depolanır.

## <a name="common-scenarios-for-using-query-store"></a>Sorgu deposunu kullanmaya yönelik genel senaryolar

Sorgu deposu, aşağıdakiler de dahil olmak üzere birkaç senaryo için kullanılabilir:

- Gerileyen sorgular algılanıyor
- Belirli bir zaman penceresinde bir sorgunun kaç kez yürütüldüğünü belirleme
- Büyük değişimleri 'yi görmek için zaman pencereleri içindeki bir sorgunun ortalama yürütme süresini karşılaştırma

## <a name="enabling-query-store"></a>Sorgu deposu etkinleştiriliyor

Sorgu deposu bir katılım özelliğidir, bu nedenle bir sunucuda varsayılan olarak etkin değildir. Sorgu deposu, belirli bir sunucudaki tüm veritabanları için etkin veya devre dışı bırakılır ve veritabanı başına açılamaz veya kapatılamaz.

### <a name="enable-query-store-using-the-azure-portal"></a>Azure portal kullanarak sorgu deposunu etkinleştirin

1. Azure portal oturum açın ve MariaDB sunucusu için Azure veritabanınızı seçin.
1. Menüdeki **Ayarlar** bölümünde **sunucu parametreleri** ' ni seçin.
1. Query_store_capture_mode parametresini arayın.
1. Değeri tümü ve **Kaydet**olarak ayarlayın.

Sorgu deponuzda bekleme istatistiklerini etkinleştirmek için:

1. Query_store_wait_sampling_capture_mode parametresini arayın.
1. Değeri tümü ve **Kaydet**olarak ayarlayın.

İlk veri toplu işi MySQL veritabanında kalıcı hale getirilemediği için 20 dakikaya kadar izin verin.

## <a name="information-in-query-store"></a>Sorgu deposundaki bilgiler

Sorgu deposunda iki depo vardır:

- Sorgu yürütme istatistikleri bilgilerini kalıcı hale getiren çalışma zamanı istatistikleri deposu.
- Kalıcı bekleme istatistikleri bilgileri için bekleme istatistikleri deposu.

Alan kullanımını en aza indirmek için, çalışma zamanı istatistikleri deposundaki çalışma zamanı yürütme istatistikleri, sabit ve yapılandırılabilir bir zaman penceresi üzerinden toplanır. Bu mağazalardaki bilgiler, sorgu deposu görünümlerini sorgulayarak görünür.

Aşağıdaki sorgu, sorgu deposundaki sorgular hakkında bilgi döndürüyor:

```sql
SELECT * FROM mysql.query_store;
```

Ya da bekleme istatistikleri için bu sorgu:

```sql
SELECT * FROM mysql.query_store_wait_stats;
```

## <a name="finding-wait-queries"></a>Bekleme sorguları bulunuyor

> [!NOTE]
> Bekleme istatistikleri yoğun iş yükü saatlerinde etkinleştirilmemelidir veya hassas iş yükleri için süresiz olarak açık olmalıdır. <br>Yüksek CPU kullanımı veya daha düşük sanal çekirdekler ile yapılandırılmış sunucularda çalışan iş yükleri için bekleme istatistiklerini etkinleştirirken dikkatli olun. Süresiz olarak açılmamalıdır. 

Bekleme olay türleri, farklı bekleme olaylarını benzerliğe göre demetlere birleştirir. Sorgu deposu, bekleme olay türü, belirli bir bekleme olayı adı ve söz konusu sorguyu sağlar. Bu bekleme bilgilerini sorgu çalışma zamanı istatistikleri ile ilişkilendirebilmek, sorgu performansı özelliklerine katkıda bulunan şeyleri daha ayrıntılı bir şekilde anlayabilmeniz anlamına gelir.

İşte iş yükünüze, sorgu deposundaki bekleme istatistiklerini kullanarak nasıl daha fazla öngörü kazankullanabileceğinizi gösteren bazı örnekler:

| **Kümesinin** | **Eylem** |
|---|---|
|Yüksek kilit bekler | Etkilenen sorgular için Sorgu metinlerini denetleyin ve hedef varlıkları doğrulayın. Sık çalıştırılan ve/veya yüksek süredeki aynı varlığı değiştiren diğer sorgular için sorgu deposuna bakın. Bu sorguları tanımladıktan sonra, eşzamanlılık geliştirmek için uygulama mantığını değiştirmeyi düşünün veya daha az kısıtlayıcı bir yalıtım düzeyi kullanın. |
|Yüksek arabellekli GÇ bekler | Sorgu deposunda yüksek sayıda fiziksel okuma içeren sorguları bulun. Yüksek GÇ beklemeleri olan sorgularla eşleşiyorsa, taramalar yerine aramalar yapmak için temel alınan varlıktaki bir dizin ile tanışın. Bu, sorguların GÇ ek yükünü en aza indirir. Bu sunucu için sorguları iyileştirmek üzere dizin önerileri olup olmadığını görmek için portalda sunucunuzun **performans önerilerini** kontrol edin. |
|Yüksek bellek bekler | Sorgu deposundaki en üstteki bellek kullanan sorguları bulun. Bu sorgular büyük olasılıkla etkilenen sorguların daha fazla ilerlemesini erteliyor. Bu sorguları iyileştirebilecek Dizin önerileri olup olmadığını görmek için portalda sunucunuzun **performans önerilerini** kontrol edin.|

## <a name="configuration-options"></a>Yapılandırma seçenekleri

Sorgu deposu etkinleştirildiğinde, verileri 15 dakikalık toplama Windows 'a kadar, her pencere için en fazla 500 farklı sorguya kaydeder.

Sorgu deposu parametrelerini yapılandırmak için aşağıdaki seçenekler kullanılabilir.

| **Parametresinin** | **Açıklama** | **Varsayılan** | **Aralık** |
|---|---|---|---|
| query_store_capture_mode | Değer temelinde sorgu deposu özelliğini açın/kapatın. Note: performance_schema KAPALıYSA, query_store_capture_mode ' ın etkinleştirilmesi, bu özellik için gerekli olan performance_schema ve performans şeması gereçlerinin bir alt kümesini etkinleştirebilir. | Bütün | HIÇBIRI, TÜMÜ |
| query_store_capture_interval | Sorgu deposu yakalama aralığı dakika olarak. Sorgu ölçümlerinin toplanmış olduğu aralığın belirtilmesine izin verir | 15 | 5 - 60 |
| query_store_capture_utility_queries | Sistemde yürütülen tüm yardımcı program sorgularını yakalamak için açma veya kapatma. | NO | EVET, HAYıR |
| query_store_retention_period_in_days | Sorgu deposundaki verilerin saklanacağı zaman penceresi. | 7 | 1 - 30 |

Aşağıdaki seçenekler özellikle bekleme istatistikleri için geçerlidir.

| **Parametresinin** | **Açıklama** | **Varsayılan** | **Aralık** |
|---|---|---|---|
| query_store_wait_sampling_capture_mode | Bekleme istatistiklerinin açılmasını/KAPATıLMASıNı sağlar. | SEÇIM | HIÇBIRI, TÜMÜ |
| query_store_wait_sampling_frequency | Saniye cinsinden bekleme örnekleme sıklığını değiştirir. 5-300 saniye. | 30 | 5-300 |

> [!NOTE]
> Şu anda **query_store_capture_mode** , bu yapılandırmanın yerini alır, ancak her iki **query_store_capture_mode** ve **query_store_wait_sampling_capture_mode** de bekleyen istatistiklerin çalışması için her ikisi de etkinleştirilmelidir. **Query_store_capture_mode** devre dışı bırakılırsa, bekleme istatistiği, performance_schema etkin ' i ve sorgu deposu tarafından yakalanan query_text ' den yararlandığından, bekleme istatistikleri de kapanır.

Bir parametre için farklı bir değer almak veya ayarlamak için [Azure Portal](howto-server-parameters.md) kullanın.

## <a name="views-and-functions"></a>Görünümler ve işlevler

Aşağıdaki görünümleri ve işlevleri kullanarak sorgu deposunu görüntüleyin ve yönetin. [Ayrıcalık Seç ortak rolünde](howto-create-users.md#create-additional-admin-users) bulunan herkes, bu görünümleri sorgu deposundaki verileri görmek için kullanabilir. Bu görünümler yalnızca **MySQL** veritabanında kullanılabilir.

Sorgular, sabit değerler ve sabitler kaldırıldıktan sonra yapısına bakılarak normalleştirilmelidir. İki sorgu özdeş değerler hariç aynıysa, aynı karma değerine sahip olur.

### <a name="mysqlquery_store"></a>MySQL. query_store

Bu görünüm, sorgu deposundaki tüm verileri döndürür. Her farklı veritabanı KIMLIĞI, Kullanıcı KIMLIĞI ve sorgu KIMLIĞI için bir satır vardır.

| **Ad** | **Veri türü** | **IS_NULLABLE** | **Açıklama** |
|---|---|---|---|
| `schema_name`| varchar (64) | NO | Şemanın adı |
| `query_id`| büyük tamsayı (20) | NO| Belirli sorgu için oluşturulan benzersiz KIMLIK, aynı sorgu farklı şemada yürütülüyorsa yeni bir KIMLIK oluşturulur |
| `timestamp_id` | timestamp| NO| Sorgunun yürütüldüğü zaman damgası. Bu, query_store_interval yapılandırmasına dayalıdır|
| `query_digest_text`| LONGTEXT| NO| Tüm sabit değerleri kaldırdıktan sonra normalleştirilmiş sorgu metni|
| `query_sample_text` | LONGTEXT| NO| Değişmez değer içeren gerçek sorgunun ilk görünümü|
| `query_digest_truncated` | sürümleri| YES| Sorgu metninin kesilip kesilmediğini belirtir. Sorgu 1 KB 'den uzunsa değer Evet olur|
| `execution_count` | büyük tamsayı (20)| NO| Sorgunun bu zaman damgası KIMLIĞI için kaç kez yürütüldüğü (yapılandırılmış Aralık dönemi sırasında)|
| `warning_count` | büyük tamsayı (20)| NO| Bu sorgunun iç sırada oluşturduğu uyarı sayısı|
| `error_count` | büyük tamsayı (20)| NO| Bu sorgunun Aralık sırasında oluşturduğu hata sayısı|
| `sum_timer_wait` | double| YES| Bu sorgunun Aralık boyunca toplam yürütme süresi|
| `avg_timer_wait` | double| YES| Bu sorgu için Aralık sırasında ortalama yürütme süresi|
| `min_timer_wait` | double| YES| Bu sorgu için en düşük yürütme süresi|
| `max_timer_wait` | double| YES| En fazla yürütme süresi|
| `sum_lock_time` | büyük tamsayı (20)| NO| Bu zaman penceresi sırasında bu sorgu yürütmesinin tüm kilitleri için harcanan toplam süre miktarı|
| `sum_rows_affected` | büyük tamsayı (20)| NO| Etkilenen satır sayısı|
| `sum_rows_sent` | büyük tamsayı (20)| NO| İstemciye gönderilen satır sayısı|
| `sum_rows_examined` | büyük tamsayı (20)| NO| İncelenen satır sayısı|
| `sum_select_full_join` | büyük tamsayı (20)| NO| Tam birleşim sayısı|
| `sum_select_scan` | büyük tamsayı (20)| NO| Seçme taraması sayısı |
| `sum_sort_rows` | büyük tamsayı (20)| NO| Sıralanan satır sayısı|
| `sum_no_index_used` | büyük tamsayı (20)| NO| Sorgunun herhangi bir dizini kullanmayan zaman sayısı|
| `sum_no_good_index_used` | büyük tamsayı (20)| NO| Sorgu yürütme altyapısının herhangi bir iyi Dizin kullanmayan zaman sayısı|
| `sum_created_tmp_tables` | büyük tamsayı (20)| NO| Toplam oluşturulan geçici tablo sayısı|
| `sum_created_tmp_disk_tables` | büyük tamsayı (20)| NO| Diskte oluşturulan toplam geçici tablo sayısı (g/ç oluşturur)|
| `first_seen` | timestamp| NO| Toplama penceresi sırasında sorgunun ilk oluşumu (UTC)|
| `last_seen` | timestamp| NO| Bu toplama penceresi sırasında sorgunun son oluşumu (UTC)|

### <a name="mysqlquery_store_wait_stats"></a>MySQL. query_store_wait_stats

Bu görünüm sorgu deposundaki bekleme olayları verilerini döndürür. Her farklı veritabanı KIMLIĞI, Kullanıcı KIMLIĞI, sorgu KIMLIĞI ve olay için bir satır vardır.

| **Ad**| **Veri türü** | **IS_NULLABLE** | **Açıklama** |
|---|---|---|---|
| `interval_start` | timestamp | NO| Aralık başlangıcı (15 dakikalık artış)|
| `interval_end` | timestamp | NO| Aralığın sonu (15 dakikalık artış)|
| `query_id` | büyük tamsayı (20) | NO| Normalleştirilmiş sorguda (sorgu deposundan) oluşturulan benzersiz KIMLIK|
| `query_digest_id` | varchar (32) | NO| Tüm sabit değerleri kaldırıldıktan sonra normalleştirilmiş sorgu metni (sorgu deposundan) |
| `query_digest_text` | LONGTEXT | NO| Değişmez değer içeren gerçek sorgunun ilk görünümü (sorgu deposundan) |
| `event_type` | varchar (32) | NO| Bekleme olayının kategorisi |
| `event_name` | varchar (128) | NO| Bekleme olayının adı |
| `count_star` | büyük tamsayı (20) | NO| Sorgu aralığı boyunca örneklenen bekleme olaylarının sayısı |
| `sum_timer_wait_ms` | double | NO| Bu sorgunun Aralık boyunca toplam bekleme süresi (milisaniye cinsinden) |

### <a name="functions"></a>İşlevler

| **Ad**| **Açıklama** |
|---|---|
| `mysql.az_purge_querystore_data(TIMESTAMP)` | Verilen zaman damgasından önce tüm sorgu depolama verilerini temizler |
| `mysql.az_procedure_purge_querystore_event(TIMESTAMP)` | Verilen zaman damgasından önce tüm bekleme olayı verilerini temizler |
| `mysql.az_procedure_purge_recommendation(TIMESTAMP)` | Süre sonu verilen zaman damgasından önce olan önerileri temizler |

## <a name="limitations-and-known-issues"></a>Sınırlamalar ve bilinen sorunlar

- Bir MariaDB sunucusunda `default_transaction_read_only` parametresi varsa, sorgu deposu veri yakalayamaz.
- Sorgu deposu işlevselliği, uzun Unicode sorgularıyla karşılaşırsa kesintiye uğrar (\>= 6000 bayt).
- Bekleme istatistikleri için bekletme süresi 24 saattir.
- Bekleme istatistikleri örnek TI kullanarak olayların bir bölümünü yakalar. Sıklık `query_store_wait_sampling_frequency`parametresi kullanılarak değiştirilebilir.

## <a name="next-steps"></a>Sonraki adımlar

- [Sorgu performansı öngörüleri](concepts-query-performance-insight.md) hakkında daha fazla bilgi edinin
