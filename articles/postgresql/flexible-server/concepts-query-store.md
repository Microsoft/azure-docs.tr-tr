---
title: Sorgu deposu-PostgreSQL için Azure veritabanı-esnek sunucu
description: Bu makalede PostgreSQL için Azure veritabanı 'nda bulunan Query Store özelliği (esnek sunucu) açıklanmaktadır.
author: ssen-msft
ms.author: ssen
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/01/2021
ms.openlocfilehash: 6ab2ea6f6544bcf561e92f00b5afee693393c157
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559208"
---
# <a name="monitor-performance-with-query-store"></a>Sorgu deposu ile performansı izleme
**Uygulama hedefi:** PostgreSQL için Azure veritabanı-esnek sunucu sürümleri 11 ve üzeri

PostgreSQL için Azure veritabanı 'ndaki sorgu depolama özelliği zaman içinde sorgu performansını izlemek için bir yol sağlar. Sorgu deposu, en uzun çalışan ve en fazla kaynak yoğunluklu sorguları hızlı bir şekilde bulmanıza yardımcı olarak performans sorunlarını ortadan basitleştirir. Sorgu deposu sorgular ve çalışma zamanı istatistikleri geçmişini otomatik olarak yakalar ve bunları gözden geçirmeniz için saklar. Zamana göre verileri dilimleyerek, zamana bağlı kullanım düzenlerini görebilirsiniz. Tüm kullanıcılar, veritabanları ve sorgular için veriler, PostgreSQL için Azure veritabanı örneğine **azure_sys** adlı bir veritabanında depolanır.

> [!IMPORTANT]
> **Azure_sys** veritabanını veya şemasını değiştirmeyin. Bunun yapılması, sorgu deposunun ve ilgili performans özelliklerinin düzgün çalışmasını engeller.
## <a name="enabling-query-store"></a>Sorgu deposu etkinleştiriliyor
Sorgu deposu bir katılım özelliğidir, bu nedenle bir sunucuda varsayılan olarak etkinleştirilmez. Sorgu deposu, belirli bir sunucudaki tüm veritabanları için etkin veya devre dışı bırakılmış ve veritabanı başına açılamaz veya kapatılamaz.
### <a name="enable-query-store-using-the-azure-portal"></a>Azure portal kullanarak sorgu deposunu etkinleştirin
1. Azure portal oturum açın ve PostgreSQL için Azure veritabanı sunucunuzu seçin.
2. Menüdeki **Ayarlar** bölümünde **sunucu parametreleri** ' ni seçin.
3. Parametresi için arama yapın `pg_qs.query_capture_mode` .
4. Değeri olarak ayarlayın `TOP` veya `ALL` **kaydedin**.
İlk toplu iş verilerinin azure_sys veritabanında kalıcı olması için 20 dakikaya izin verin.
## <a name="information-in-query-store"></a>Sorgu deposundaki bilgiler
Sorgu deposunda bir depo vardır:
- Sorgu yürütme istatistikleri bilgilerini kalıcı hale getiren bir çalışma zamanı istatistikleri deposu.

Sorgu deposunu kullanmaya yönelik yaygın senaryolar şunlardır:
- Belirli bir zaman penceresinde bir sorgunun kaç kez yürütüldüğünü belirleme
- Büyük değişimleri 'yi görmek için zaman pencereleri içindeki bir sorgunun ortalama yürütme süresini karşılaştırma
- Son birkaç saat içinde, alan kullanımını en aza indirmek Için en uzun çalışan sorguları tanımlama, çalışma zamanı istatistikleri deposundaki çalışma zamanı yürütme istatistikleri, sabit ve yapılandırılabilir bir zaman penceresi üzerinden toplanır. Bu mağazalardaki bilgiler, görünümler kullanılarak sorgulanabilir.
## <a name="access-query-store-information"></a>Sorgu deposu bilgilerine erişin
Sorgu deposu verileri, Postgres sunucunuzdaki azure_sys veritabanında depolanır. Aşağıdaki sorgu, sorgu deposundaki sorgular hakkında bilgi döndürüyor:
```sql
SELECT * FROM query_store.qs_view; 
``` 

## <a name="configuration-options"></a>Yapılandırma seçenekleri
Sorgu deposu etkinleştirildiğinde, verileri 15 dakikalık toplama Windows 'a kadar, her pencere için en fazla 500 farklı sorguya kaydeder. Sorgu deposu parametrelerini yapılandırmak için aşağıdaki seçenekler kullanılabilir.

| **Parametre** | **Açıklama** | **Varsayılanını** | **Aralık**|
|---|---|---|---|
| pg_qs pg_qs.query_capture_mode | Hangi deyimlerin izleneceğini ayarlar. | yok | hiçbiri, üst, tümü |
| pg_qs pg_qs.max_query_text_length | Kaydedilebilecek maksimum sorgu uzunluğunu ayarlar. Daha uzun sorgular kesilecek. | 6000 | 100-10.000 |
| pg_qs pg_qs.retention_period_in_days | Saklama süresini ayarlar. | 7 | 1 - 30 |
| pg_qs pg_qs.track_utility | Yardımcı program komutlarının izlenip izlenmeyeceğini ayarlar | on | açık, kapalı |

Bir parametre için farklı bir değer almak veya ayarlamak için [Azure Portal](howto-configure-server-parameters-using-portal.md) kullanın.

## <a name="views-and-functions"></a>Görünümler ve işlevler
Aşağıdaki görünümleri ve işlevleri kullanarak sorgu deposunu görüntüleyin ve yönetin. PostgreSQL ortak rolündeki herkes bu görünümleri kullanarak sorgu deposundaki verileri görebilirler. Bu görünümler yalnızca **azure_sys** veritabanında kullanılabilir.
Sorgular, sabit değerler ve sabitler kaldırıldıktan sonra yapısına bakılarak normalleştirilmelidir. İki sorgu aynı değer hariç aynıysa aynı QueryId olur.
### <a name="query_storeqs_view"></a>query_store query_store.qs_view
Bu görünüm, sorgu deposundaki tüm verileri döndürür. Her farklı veritabanı KIMLIĞI, Kullanıcı KIMLIĞI ve sorgu KIMLIĞI için bir satır vardır. 

|**Ad**   |**Tür** | **Başvurular**  | **Açıklama**|
|---|---|---|---|
|runtime_stats_entry_id |bigint | | Runtime_stats_entries tablosundan KIMLIK|
|user_id    |id    |pg_authid. OID  |İfadeyi yürüten kullanıcının OID 'si|
|db_id  |id    |pg_database. OID    |Deyimin yürütüldüğü veritabanının OID 'si|
|query_id   |bigint  || Deyimin ayrıştırma ağacından hesaplanan iç karma kod|
|query_sql_text |Varchar (10000)  || Temsili ifadesinin metni. Aynı yapıya sahip farklı sorgular birlikte kümelenir; Bu metin, kümedeki sorguların ilki için metindir.|
|plan_id    |bigint |   |Bu sorguya karşılık gelen planın KIMLIĞI henüz kullanılamaz|
|start_time |timestamp  ||  Sorgular zaman demetlerine göre toplanır. bir Bucket zaman aralığı varsayılan olarak 15 dakikadır. Bu, bu girişin zaman aralığı ile ilgili olan başlangıç zamanı.|
|end_time   |timestamp  ||  Bu girdinin zaman aralığı ile ilgili bitiş saati.|
|aramalarda  |bigint  || Sorgunun kaç kez yürütüldüğü|
|total_time |çift duyarlık   ||  Toplam sorgu yürütme süresi (milisaniye)|
|min_time   |çift duyarlık   ||  En düşük sorgu yürütme süresi (milisaniye)|
|max_time   |çift duyarlık   ||  En fazla sorgu yürütme süresi (milisaniye)|
|mean_time  |çift duyarlık   ||  Ortalama sorgu yürütme süresi (milisaniye)|
|stddev_time|   çift duyarlık    ||  Sorgu yürütme süresinin milisaniye cinsinden standart sapması |
|sütunları   |bigint ||  Deyimden alınan veya etkilenen toplam satır sayısı|
|shared_blks_hit|   bigint  ||  Deyimle toplam paylaşılan blok önbelleği isabetli okuma sayısı|
|shared_blks_read|  bigint  ||  İfadesiyle okunan paylaşılan blokların toplam sayısı|
|shared_blks_dirtied|   bigint   || Deyimle bağlanmış olan paylaşılan blokların toplam sayısı |
|shared_blks_written|   bigint  ||  İfadesiyle yazılan paylaşılan blokların toplam sayısı|
|local_blks_hit|    bigint ||   Bildirimde bulunan toplam yerel blok önbellek okuması sayısı|
|local_blks_read|   bigint   || Deyimle okunan toplam yerel blok sayısı|
|local_blks_dirtied|    bigint  ||  Deyimle bağlı olan toplam yerel blok sayısı|
|local_blks_written|    bigint  ||  İfadesiyle yazılan toplam yerel blok sayısı|
|temp_blks_read |bigint  || Deyimle okunan toplam geçici blok sayısı|
|temp_blks_written| bigint   || Deyimle yazılan toplam geçici blok sayısı|
|blk_read_time  |çift duyarlık    || Deyimin (track_io_timing etkinse, yoksa sıfır) blokları okumak için harcadığı toplam süre (milisaniye cinsinden)|
|blk_write_time |çift duyarlık    || Deyimin (track_io_timing etkinse, yoksa sıfır) blokları yazarken harcadığı toplam süre (milisaniye cinsinden)|
    
### <a name="query_storequery_texts_view"></a>query_store query_store.query_texts_view
Bu görünüm sorgu deposunda sorgu metin verileri döndürür. Her ayrı query_text için bir satır vardır.

| **Ad** | **Tür** | **Açıklama** |
|--|--|--|
| query_text_id | bigint | Query_texts tablosunun KIMLIĞI |
| query_sql_text | Varchar (10000) | Temsili ifadesinin metni. Aynı yapıya sahip farklı sorgular birlikte kümelenir; Bu metin, kümedeki sorguların ilki için metindir. |

### <a name="functions"></a>İşlevler
`qs_reset` Şimdiye kadar toplanan tüm istatistikleri sorgu deposu tarafından atar. Bu işlev yalnızca sunucu yöneticisi rolü tarafından yürütülebilir.

`staging_data_reset` Sorgu deposu tarafından bellekte toplanan tüm istatistikleri atar (diğer bir deyişle, bellekteki veriler henüz veritabanına temizlenmemiştir). Bu işlev yalnızca sunucu yöneticisi rolü tarafından yürütülebilir.

## <a name="limitations-and-known-issues"></a>Sınırlamalar ve bilinen sorunlar
- Bir PostgreSQL sunucusunda default_transaction_read_only parametresi varsa, sorgu deposu herhangi bir veri yakalamaz.
## <a name="next-steps"></a>Sonraki adımlar
- [Sorgu deposunun özellikle yararlı olabilecek senaryolar](concepts-query-store-scenarios.md)hakkında daha fazla bilgi edinin.
- [Sorgu deposunu kullanmaya yönelik en iyi uygulamalar](concepts-query-store-best-practices.md)hakkında daha fazla bilgi edinin.
