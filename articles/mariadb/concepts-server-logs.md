---
title: Yavaş sorgu günlükleri-MariaDB için Azure veritabanı
description: MariaDB için Azure veritabanı 'nda kullanılabilen günlükleri ve farklı günlük düzeylerini etkinleştirmek için kullanılabilen parametreleri açıklar.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 11/6/2020
ms.openlocfilehash: c0cb9e0da3ba5dd899cac56f393d7332bac6c597
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107258495"
---
# <a name="slow-query-logs-in-azure-database-for-mariadb"></a>MariaDB için Azure veritabanı 'nda yavaş sorgu günlükleri
MariaDB için Azure veritabanı 'nda yavaş sorgu günlüğü kullanıcılar tarafından kullanılabilir. İşlem günlüğüne erişim desteklenmez. Yavaş sorgu günlüğü, sorun giderme için performans sorunlarını belirlemek için kullanılabilir.

Yavaş sorgu günlüğü hakkında daha fazla bilgi için, [yavaş sorgu günlüğü](https://mariadb.com/kb/en/library/slow-query-log-overview/)Için MariaDB belgelerine bakın.

Sunucunuzda [sorgu deposu](concepts-query-store.md) etkinleştirildiğinde "" gibi sorguları, `CALL mysql.az_procedure_collect_wait_stats (900, 30);` yavaş sorgu günlüklerinizi günlüğe kaydedilmiş olarak görebilirsiniz. Sorgu deposu özelliği sorgularınız hakkında istatistikler toplarken bu davranış beklenmektedir. 

## <a name="configure-slow-query-logging"></a>Yavaş sorgu günlüğünü yapılandırma
Varsayılan olarak, yavaş sorgu günlüğü devre dışıdır. Etkinleştirmek için, `slow_query_log` Açık olarak ayarlayın. Bu, Azure portal veya Azure CLı kullanılarak etkinleştirilebilir. 

Ayarlayabileceğiniz diğer parametreler şunlardır:

- **long_query_time**: sorgu günlüğe kaydedilen sorgu long_query_time daha uzun sürer (saniye cinsinden). Varsayılan değer 10 saniyedir.
- **log_slow_admin_statements**: varsa, slow_query_log YAZıLAN deyimlerde ALTER_TABLE ve ANALYZE_TABLE gibi yönetim deyimlerini içerir.
- **log_queries_not_using_indexes**: dizinleri kullanmayan sorguların slow_query_log kaydedilip kaydedilmeyeceğini belirler
- **log_throttle_queries_not_using_indexes**: Bu parametre, yavaş sorgu günlüğüne yazılabilen Dizin dışı sorguların sayısını sınırlar. Bu parametre log_queries_not_using_indexes açık olarak ayarlandığında devreye girer.
- **log_output**: "dosya" ise, yavaş sorgu günlüğünün hem yerel sunucu depolamasına hem de Azure Izleyici tanılama günlüklerine yazılmasına izin verir. "None" seçilirse, yavaş sorgu günlüğü yalnızca Azure İzleyici Tanılama Günlüklerine yazılır. 

> [!IMPORTANT]
> Tablolarınızın dizini oluşturulmamış ise, `log_queries_not_using_indexes` `log_throttle_queries_not_using_indexes` Bu dizinlenmemiş tablolarda çalışan tüm sorgular yavaş sorgu günlüğüne yazılacak olduğundan ve parametrelerini açık olarak ayarlamak MariaDB performansını etkileyebilir.<br><br>
> Uzun bir süre için yavaş sorguları günlüğe kaydetmeyi planlıyorsanız, `log_output` "none" olarak ayarlanması önerilir. "Dosya" olarak ayarlanırsa, bu günlükler yerel sunucu depolamasına yazılır ve MariaDB performansını etkileyebilir. 

Yavaş sorgu günlüğü parametrelerinin tam açıklamaları için MariaDB [yavaş sorgu günlüğü belgelerine](https://mariadb.com/kb/en/library/slow-query-log-overview/) bakın.

## <a name="access-slow-query-logs"></a>Yavaş sorgu günlüklerine erişin
MariaDB için Azure veritabanı 'nda yavaş sorgu günlüklerine erişilmesine yönelik iki seçenek vardır: yerel sunucu depolaması veya Azure Izleyici tanılama günlükleri. Bu, parametresi kullanılarak ayarlanır `log_output` .

Yerel sunucu depolaması için, Azure portal veya Azure CLı kullanarak yavaş sorgu günlüklerini listeleyebilir ve indirebilirsiniz. Azure portal, Azure portal sunucunuza gidin. **İzleme** başlığı altında, **sunucu günlükleri** sayfasını seçin. Azure CLı hakkında daha fazla bilgi için bkz. [Azure CLI kullanarak sunucu günlüklerini yapılandırma ve erişme](howto-configure-server-logs-cli.md). 

Azure Izleyici tanılama günlükleri, Azure Izleyici günlüklerine (Log Analytics), Azure depolama 'ya veya Event Hubs yavaş sorgu günlüklerini boru etmenize olanak tanır. Daha fazla bilgi için [aşağıya](concepts-server-logs.md#diagnostic-logs) bakın.

## <a name="local-server-storage-log-retention"></a>Yerel sunucu depolama günlüğü saklama
Sunucunun yerel depolama alanına oturum açarken, günlükleri oluşturulduktan yedi güne kadar kullanılabilir. Kullanılabilir günlüklerin toplam boyutu 7 GB 'yi aşarsa, alan kullanılabilir olana kadar en eski dosyalar silinir. Sunucu günlükleri için 7 GB depolama sınırı, ücretsiz olarak kullanılabilir ve genişletilemez. 

Günlükler her 24 saatte bir veya 7 GB döndürülür, hangisi önce gelir.

> [!Note]
> Yukarıdaki günlük tutma, Azure Izleyici tanılama günlükleri kullanılarak yöneltilen Günlükler için uygulanmaz. Yayılmakta olan veri havuzları için saklama süresini değiştirebilirsiniz (örn. Azure depolama).

## <a name="diagnostic-logs"></a>Tanılama günlükleri
MariaDB için Azure veritabanı, Azure Izleyici tanılama günlükleriyle tümleşiktir. MariaDB sunucunuzda yavaş sorgu günlüklerini etkinleştirdikten sonra, bunların Azure Izleyici günlüklerine, Event Hubs veya Azure depolama 'ya yayılmasını tercih edebilirsiniz. Tanılama günlüklerinin nasıl etkinleştirileceği hakkında daha fazla bilgi edinmek için [tanılama günlükleri belgelerinin](../azure-monitor/essentials/platform-logs-overview.md)nasıl yapılır bölümüne bakın.

Aşağıdaki tabloda her günlükte neler olduğu açıklanmaktadır. Çıkış yöntemine bağlı olarak, dahil edilen alanlar ve göründükleri sıralama farklılık gösterebilir.

| **Özellik** | **Açıklama** |
|---|---|
| `TenantId` | Kiracı KIMLIĞINIZ |
| `SourceSystem` | `Azure` |
| `TimeGenerated` UTC | Günlük kaydedildiği zaman damgası (UTC) |
| `Type` | Günlüğün türü. Her `AzureDiagnostics` |
| `SubscriptionId` | Sunucunun ait olduğu abonelik için GUID |
| `ResourceGroup` | Sunucunun ait olduğu kaynak grubunun adı |
| `ResourceProvider` | Kaynak sağlayıcının adı. Her `MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | Kaynak URI 'SI |
| `Resource` | Sunucunun adı |
| `Category` | `MySqlSlowLogs` |
| `OperationName` | `LogEvent` |
| `Logical_server_name_s` | Sunucunun adı |
| `start_time_t` UTC | Sorgunun başladığı zaman |
| `query_time_s` | Sorgunun yürütülmesi için geçen toplam süre |
| `lock_time_s` | Sorgunun kilitlenme toplam süre |
| `user_host_s` | Kullanıcı adı |
| `rows_sent_s` | Gönderilen satır sayısı |
| `rows_examined_s` | İncelenen satır sayısı |
| `last_insert_id_s` | [last_insert_id](https://mariadb.com/kb/en/library/last_insert_id/) |
| `insert_id_s` | KIMLIĞI Ekle |
| `sql_text_s` | Tam sorgu |
| `server_id_s` | Sunucu KIMLIĞI |
| `thread_id_s` | İş parçacığı KIMLIĞI |
| `\_ResourceId` | Kaynak URI 'SI |

> [!Note]
> İçin `sql_text` , 2048 karakteri aşarsa günlük kesilecek.

## <a name="analyze-logs-in-azure-monitor-logs"></a>Azure Izleyici günlüklerinde günlükleri analiz etme

Yavaş sorgu günlüklerinizin tanılama günlükleri aracılığıyla Azure Izleyici günlüklerine bir kez alındıktan sonra, yavaş sorgularınızı daha fazla analiz gerçekleştirebilirsiniz. Aşağıda, başlamanıza yardımcı olacak bazı örnek sorgular verilmiştir. Aşağıdaki öğesini sunucu adınızla güncelleştirdiğinizden emin olun.

- Belirli bir sunucuda 10 saniyeden uzun sorgu

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | where query_time_d > 10
    ```

- Belirli bir sunucuda ilk 5 en uzun sorguyu listeleme

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | order by query_time_d desc
    | take 5
    ```

- Belirli bir sunucuda minimum, maksimum, ortalama ve standart sapma sorgu zamanına göre yavaş sorguları özetleme

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | summarize count(), min(query_time_d), max(query_time_d), avg(query_time_d), stdev(query_time_d), percentile(query_time_d, 95) by LogicalServerName_s
    ```

- Belirli bir sunucuda yavaş sorgu dağıtımını grafiktir

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | summarize count() by LogicalServerName_s, bin(TimeGenerated, 5m)
    | render timechart
    ```

- Tanılama günlükleri etkin olan tüm MariaDB sunucularında 10 saniyeden uzun sorguları görüntüle

    ```Kusto
    AzureDiagnostics
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | where query_time_d > 10
    ```    
    
## <a name="next-steps"></a>Sonraki Adımlar
- [Azure portal yavaş sorgu günlüklerini yapılandırma](howto-configure-server-logs-portal.md)
- [Azure CLı 'dan yavaş sorgu günlüklerini yapılandırma](howto-configure-server-logs-cli.md)
