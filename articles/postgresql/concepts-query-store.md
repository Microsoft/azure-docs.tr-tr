---
title: Sorgu deposu-PostgreSQL için Azure veritabanı-tek sunucu
description: Bu makalede PostgreSQL için Azure veritabanı 'nda bulunan Query Store özelliği (tek sunucu) açıklanmaktadır.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/01/2020
ms.openlocfilehash: 1779df1c5f9baf2aa46ff809ecae9ec5e3cd7adb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100581558"
---
# <a name="monitor-performance-with-the-query-store"></a>Sorgu deposuyla performansı izleme

**Uygulama hedefi:** PostgreSQL için Azure veritabanı-tek sunuculu 9,6 ve üzeri sürümleri

PostgreSQL için Azure veritabanı 'ndaki sorgu depolama özelliği zaman içinde sorgu performansını izlemek için bir yol sağlar. Sorgu deposu, en uzun çalışan ve en fazla kaynak yoğunluklu sorguları hızlı bir şekilde bulmanıza yardımcı olarak performans sorunlarını basitleştirir. Sorgu deposu sorgular ve çalışma zamanı istatistikleri geçmişini otomatik olarak yakalar ve bunları gözden geçirmeniz için saklar. Veritabanı kullanım düzenlerini görebilmeniz için verileri zaman pencereleri ile ayırır. Tüm kullanıcılar, veritabanları ve sorgular için veriler, PostgreSQL için Azure veritabanı örneğine **azure_sys** adlı bir veritabanında depolanır.

> [!IMPORTANT]
> **Azure_sys** veritabanını veya şemalarını değiştirmeyin. Bunun yapılması, sorgu deposunun ve ilgili performans özelliklerinin düzgün çalışmasını engeller.

## <a name="enabling-query-store"></a>Sorgu deposu etkinleştiriliyor
Sorgu deposu bir katılım özelliğidir, bu nedenle bir sunucuda varsayılan olarak etkin değildir. Mağaza, belirli bir sunucudaki tüm veritabanları için etkin veya devre dışı bırakılmış ve veritabanı başına açık veya kapalı olamaz.

### <a name="enable-query-store-using-the-azure-portal"></a>Azure portal kullanarak sorgu deposunu etkinleştirin
1. Azure portal oturum açın ve PostgreSQL için Azure veritabanı sunucunuzu seçin.
2. Menüdeki **Ayarlar** bölümünde **sunucu parametreleri** ' ni seçin.
3. Parametresi için arama yapın `pg_qs.query_capture_mode` .
4. Değerini olarak ayarlayın `TOP` ve **kaydedin**.

Sorgu deponuzda bekleme istatistiklerini etkinleştirmek için: 
1. Parametresi için arama yapın `pgms_wait_sampling.query_capture_mode` .
1. Değerini olarak ayarlayın `ALL` ve **kaydedin**.


Alternatif olarak, Azure CLı kullanarak bu parametreleri ayarlayabilirsiniz.
```azurecli-interactive
az postgres server configuration set --name pg_qs.query_capture_mode --resource-group myresourcegroup --server mydemoserver --value TOP
az postgres server configuration set --name pgms_wait_sampling.query_capture_mode --resource-group myresourcegroup --server mydemoserver --value ALL
```

İlk toplu iş verilerinin azure_sys veritabanında kalıcı olması için 20 dakikaya izin verin.

## <a name="information-in-query-store"></a>Sorgu deposundaki bilgiler
Sorgu deposunda iki depo vardır:
- Sorgu yürütme istatistikleri bilgilerini kalıcı hale getiren bir çalışma zamanı istatistikleri deposu.
- Kalıcı bekleme istatistikleri bilgileri için bir bekleme istatistiği deposu.

Sorgu deposunu kullanmaya yönelik yaygın senaryolar şunlardır:
- Belirli bir zaman penceresinde bir sorgunun kaç kez yürütüldüğünü belirleme
- Büyük değişimleri 'yi görmek için zaman pencereleri içindeki bir sorgunun ortalama yürütme süresini karşılaştırma
- Son X saat içinde en uzun çalışan sorguları tanımlama
- Kaynaklarda bekleyen ilk N sorguyu tanımlama
- Belirli bir sorgu için bekleme yapısını anlama

Alan kullanımını en aza indirmek için, çalışma zamanı istatistikleri deposundaki çalışma zamanı yürütme istatistikleri, sabit ve yapılandırılabilir bir zaman penceresi üzerinden toplanır. Bu mağazalardaki bilgiler, sorgu deposu görünümlerini sorgulayarak görünür.

## <a name="access-query-store-information"></a>Sorgu deposu bilgilerine erişin

Sorgu deposu verileri, Postgres sunucunuzdaki azure_sys veritabanında depolanır. 

Aşağıdaki sorgu, sorgu deposundaki sorgular hakkında bilgi döndürüyor:
```sql
SELECT * FROM query_store.qs_view; 
``` 

Ya da bekleme istatistiği için bu sorgu:
```sql
SELECT * FROM query_store.pgms_wait_sampling_view;
```

## <a name="finding-wait-queries"></a>Bekleme sorguları bulunuyor
Bekleme olay türleri, farklı bekleme olaylarını benzerliğe göre demetlere birleştirir. Sorgu deposu, bekleme olay türü, belirli bir bekleme olayı adı ve söz konusu sorguyu sağlar. Bu bekleme bilgilerini sorgu çalışma zamanı istatistikleri ile ilişkilendirebilmek, sorgu performansı özelliklerine katkıda bulunan şeyleri daha ayrıntılı bir şekilde anlayabilmeniz anlamına gelir.

İşte iş yükünüze, sorgu deposundaki bekleme istatistiklerini kullanarak nasıl daha fazla öngörü kazankullanabileceğinizi gösteren bazı örnekler:

| **Kümesinin** | **Eylem** |
|---|---|
|Yüksek kilit bekler | Etkilenen sorgular için Sorgu metinlerini denetleyin ve hedef varlıkları doğrulayın. Sık çalıştırılan ve/veya yüksek süredeki aynı varlığı değiştiren diğer sorgular için sorgu deposuna bakın. Bu sorguları tanımladıktan sonra, eşzamanlılık geliştirmek için uygulama mantığını değiştirmeyi düşünün veya daha az kısıtlayıcı bir yalıtım düzeyi kullanın.|
| Yüksek arabellekli GÇ bekler | Sorgu deposunda yüksek sayıda fiziksel okuma içeren sorguları bulun. Yüksek GÇ beklemeleri olan sorgularla eşleşiyorsa, taramalar yerine arama yapmak için temel varlık üzerinde bir dizin belirtmeyi göz önünde bulundurun. Bu, sorguların GÇ ek yükünü en aza indirir. Bu sunucu için sorguları iyileştirmek üzere dizin önerileri olup olmadığını görmek için portalda sunucunuzun **performans önerilerini** kontrol edin.|
| Yüksek bellek bekler | Sorgu deposundaki en üstteki bellek kullanan sorguları bulun. Bu sorgular büyük olasılıkla etkilenen sorguların daha fazla ilerlemesini erteliyor. Bu sorguları iyileştirebilecek Dizin önerileri olup olmadığını görmek için portalda sunucunuzun **performans önerilerini** kontrol edin.|

## <a name="configuration-options"></a>Yapılandırma seçenekleri
Sorgu deposu etkinleştirildiğinde, verileri 15 dakikalık toplama Windows 'a kadar, her pencere için en fazla 500 farklı sorguya kaydeder. 

Sorgu deposu parametrelerini yapılandırmak için aşağıdaki seçenekler kullanılabilir.

| **Parametre** | **Açıklama** | **Varsayılanını** | **Aralık**|
|---|---|---|---|
| pg_qs pg_qs.query_capture_mode | Hangi deyimlerin izleneceğini ayarlar. | yok | hiçbiri, üst, tümü |
| pg_qs pg_qs.max_query_text_length | Kaydedilebilecek maksimum sorgu uzunluğunu ayarlar. Daha uzun sorgular kesilecek. | 6000 | 100-10.000 |
| pg_qs pg_qs.retention_period_in_days | Saklama süresini ayarlar. | 7 | 1 - 30 |
| pg_qs pg_qs.track_utility | Yardımcı program komutlarının izlenip izlenmeyeceğini ayarlar | on | açık, kapalı |

Aşağıdaki seçenekler özellikle bekleme istatistikleri için geçerlidir.

| **Parametre** | **Açıklama** | **Varsayılanını** | **Aralık**|
|---|---|---|---|
| pgms_wait_sampling pgms_wait_sampling.query_capture_mode | Bekleme istatistikleri için hangi deyimlerin izleneceğini ayarlar. | yok | hiçbiri, tümü|
| Pgms_wait_sampling Pgms_wait_sampling.history_period | Bekleme olaylarının örneklendiği sıklığı milisaniye cinsinden ayarlayın. | 100 | 1-600000 |

> [!NOTE] 
> **pg_qs. query_capture_mode** , **pgms_wait_sampling. query_capture_mode** yerine geçiyor. Pg_qs. query_capture_mode NONE ise, pgms_wait_sampling. query_capture_mode ayarının etkisi yoktur.


Bir parametre için farklı bir değer almak veya ayarlamak için [Azure Portal](howto-configure-server-parameters-using-portal.md) veya [Azure CLI](howto-configure-server-parameters-using-cli.md) kullanın.

## <a name="views-and-functions"></a>Görünümler ve işlevler
Aşağıdaki görünümleri ve işlevleri kullanarak sorgu deposunu görüntüleyin ve yönetin. PostgreSQL ortak rolündeki herkes bu görünümleri kullanarak sorgu deposundaki verileri görebilirler. Bu görünümler yalnızca **azure_sys** veritabanında kullanılabilir.

Sorgular, sabit değerler ve sabitler kaldırıldıktan sonra yapısına bakılarak normalleştirilmelidir. İki sorgu özdeş değerler hariç aynıysa, aynı karma değerine sahip olur.

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

### <a name="query_storepgms_wait_sampling_view"></a>query_store query_store.pgms_wait_sampling_view
Bu görünüm sorgu deposundaki bekleme olayları verilerini döndürür. Her farklı veritabanı KIMLIĞI, Kullanıcı KIMLIĞI, sorgu KIMLIĞI ve olay için bir satır vardır.

| **Ad** | **Tür** | **Başvurular** | **Açıklama** |
|--|--|--|--|
| user_id | id | pg_authid. OID | İfadeyi yürüten kullanıcının OID 'si |
| db_id | id | pg_database. OID | Deyimin yürütüldüğü veritabanının OID 'si |
| query_id | bigint |  | Deyimin ayrıştırma ağacından hesaplanan iç karma kod |
| event_type | metin |  | Arka ucun beklediği olay türü |
| event | metin |  | Arka uç Şu anda bekliyorsa bekleme olayı adı |
| aramalarda | Tamsayı |  | Yakalanan aynı olay sayısı |

### <a name="functions"></a>İşlevler

Query_store Query_store.qs_reset () void döndürüyor

`qs_reset` Şimdiye kadar toplanan tüm istatistikleri sorgu deposu tarafından atar. Bu işlev yalnızca sunucu yöneticisi rolü tarafından yürütülebilir.

Query_store Query_store.staging_data_reset () void döndürüyor

`staging_data_reset` Sorgu deposu tarafından bellekte toplanan tüm istatistikleri atar (diğer bir deyişle, bellekteki veriler henüz veritabanına temizlenmemiştir). Bu işlev yalnızca sunucu yöneticisi rolü tarafından yürütülebilir.


## <a name="azure-monitor"></a>Azure İzleyici
PostgreSQL için Azure veritabanı, [Azure izleyici tanılama ayarlarıyla](../azure-monitor/essentials/diagnostic-settings.md)tümleşiktir. Tanılama ayarları, [Azure Izleyici günlüklerine](../azure-monitor/logs/log-query-overview.md) analiz ve uyarı, akış için Event Hubs ve arşivlenmek üzere Azure Storage günlüklerini JSON biçiminde göndermenizi sağlar.

>[!IMPORTANT]
> İçin bu tanılama özelliği yalnızca Genel Amaçlı ve bellek için Iyileştirilmiş fiyatlandırma katmanlarında kullanılabilir.

### <a name="configure-diagnostic-settings"></a>Tanılama ayarlarını yapılandırma
Azure portal, CLı, REST API ve PowerShell 'i kullanarak Postgres sunucunuz için tanılama ayarlarını etkinleştirebilirsiniz. Yapılandırılacak günlük kategorileri **QueryStoreRuntimeStatistics** ve **Querystorewaitstatistics**' dir. 

Azure portal kullanarak kaynak günlüklerini etkinleştirmek için:

1. Portalda, Postgres sunucunuzun gezinti menüsünde Tanılama Ayarları ' na gidin.
2. Tanılama ayarı Ekle ' yi seçin.
3. Bu ayarı adlandırın.
4. Tercih ettiğiniz uç noktayı (depolama hesabı, Olay Hub 'ı, Log Analytics) seçin.
5. **QueryStoreRuntimeStatistics** ve **Querystorewaitstatistics** günlük türlerini seçin.
6. Ayarınızı kaydedin.

Bu ayarı PowerShell, CLı veya REST API kullanarak etkinleştirmek için [Tanılama ayarları makalesini](../azure-monitor/essentials/diagnostic-settings.md)ziyaret edin.

### <a name="json-log-format"></a>JSON günlük biçimi
Aşağıdaki tablolar, iki günlük türü için alanları açıklar. Seçtiğiniz çıkış uç noktasına bağlı olarak, dahil edilen alanlar ve göründükleri sıralama farklılık gösterebilir.

#### <a name="querystoreruntimestatistics"></a>QueryStoreRuntimeStatistics
|**Alan** | **Açıklama** |
|---|---|
| TimeGenerated [UTC] | Günlük kaydedildiği zaman damgası (UTC) |
| ResourceId | Postgres sunucusunun Azure Kaynak URI 'SI |
| Kategori | `QueryStoreRuntimeStatistics` |
| OperationName | `QueryStoreRuntimeStatisticsEvent` |
| LogicalServerName_s | Postgres sunucu adı | 
| runtime_stats_entry_id_s | Runtime_stats_entries tablosundan KIMLIK |
| user_id_s | İfadeyi yürüten kullanıcının OID 'si |
| db_id_s | Deyimin yürütüldüğü veritabanının OID 'si |
| query_id_s | Deyimin ayrıştırma ağacından hesaplanan iç karma kod |
| end_time_s | Bu girdinin zaman aralığı ile ilgili bitiş saati |
| calls_s | Sorgunun kaç kez yürütüldüğü |
| total_time_s | Toplam sorgu yürütme süresi (milisaniye) |
| min_time_s | En düşük sorgu yürütme süresi (milisaniye) |
| max_time_s | En fazla sorgu yürütme süresi (milisaniye) |
| mean_time_s | Ortalama sorgu yürütme süresi (milisaniye) |
| adlı yönetilen örnek, | Kaynak grubu | 
| SubscriptionId | Abonelik KIMLIĞINIZ |
| ResourceProvider | `Microsoft.DBForPostgreSQL` | 
| Kaynak | Postgres sunucu adı |
| ResourceType | `Servers` | 


#### <a name="querystorewaitstatistics"></a>QueryStoreWaitStatistics
|**Alan** | **Açıklama** |
|---|---|
| TimeGenerated [UTC] | Günlük kaydedildiği zaman damgası (UTC) |
| ResourceId | Postgres sunucusunun Azure Kaynak URI 'SI |
| Kategori | `QueryStoreWaitStatistics` |
| OperationName | `QueryStoreWaitEvent` |
| user_id_s | İfadeyi yürüten kullanıcının OID 'si |
| db_id_s | Deyimin yürütüldüğü veritabanının OID 'si |
| query_id_s | Sorgunun iç karma kodu |
| calls_s | Yakalanan aynı olay sayısı |
| event_type_s | Arka ucun beklediği olay türü |
| event_s | Arka uç Şu anda bekliyorsa bekleme olayı adı |
| start_time_t | Olay başlangıç saati |
| end_time_s | Olay bitiş zamanı | 
| LogicalServerName_s | Postgres sunucu adı | 
| adlı yönetilen örnek, | Kaynak grubu | 
| SubscriptionId | Abonelik KIMLIĞINIZ |
| ResourceProvider | `Microsoft.DBForPostgreSQL` | 
| Kaynak | Postgres sunucu adı |
| ResourceType | `Servers` | 

## <a name="limitations-and-known-issues"></a>Sınırlamalar ve bilinen sorunlar
- Bir PostgreSQL sunucusunda default_transaction_read_only parametresi varsa, sorgu deposu veri yakalayamaz.
- Sorgu deposu işlevselliği, uzun Unicode sorgularıyla karşılaşırsa kesintiye uğrar (>= 6000 bayt).
- [Okuma çoğaltmaları](concepts-read-replicas.md) , birincil sunucudan sorgu deposu verilerini çoğaltır. Bu, bir okuma çoğaltmasının sorgu deposunun okuma çoğaltmasında çalıştırılan sorgular hakkında istatistikler sağlamamasıdır.


## <a name="next-steps"></a>Sonraki adımlar
- [Sorgu deposunun özellikle yararlı olabilecek senaryolar](concepts-query-store-scenarios.md)hakkında daha fazla bilgi edinin.
- [Sorgu deposunu kullanmaya yönelik en iyi uygulamalar](concepts-query-store-best-practices.md)hakkında daha fazla bilgi edinin.
