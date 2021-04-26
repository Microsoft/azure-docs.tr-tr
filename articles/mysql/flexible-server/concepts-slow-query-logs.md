---
title: Yavaş sorgu günlükleri-bir MySQL için Azure veritabanı-esnek sunucu
description: MySQL için Azure veritabanı esnek sunucusu 'nda bulunan yavaş sorgu günlüklerini açıklar.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 9/21/2020
ms.openlocfilehash: d311ea3158e1f9d53c51fe239103039849597d11
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100579198"
---
# <a name="slow-query-logs-in-azure-database-for-mysql-flexible-server-preview"></a>MySQL için Azure veritabanı esnek sunucusu 'nda yavaş sorgu günlükleri (Önizleme)

> [!IMPORTANT] 
> MySQL için Azure veritabanı-esnek sunucu şu anda genel önizlemededir.

MySQL için Azure veritabanı esnek sunucu 'da, yavaş sorgu günlüğü kullanıcıların yapılandırması ve erişimi için kullanılabilir. Yavaş sorgu günlükleri varsayılan olarak devre dışıdır ve sorun giderme sırasında performans sorunlarını tanımlamaya yardımcı olmak için etkinleştirilebilir.

MySQL yavaş sorgu günlüğü hakkında daha fazla bilgi için MySQL Engine belgelerindeki [yavaş sorgu günlüğü bölümüne](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html) bakın.

## <a name="configure-slow-query-logging"></a>Yavaş sorgu günlüğünü yapılandırma 
Varsayılan olarak, yavaş sorgu günlüğü devre dışıdır. Günlükleri etkinleştirmek için `slow_query_log` sunucu parametresini *Açık* olarak ayarlayın. Bu, Azure portal veya Azure CLı kullanılarak yapılandırılabilir <!-- add link to server parameter-->. 

Yavaş sorgu günlüğü davranışlarını denetlemek için ayarlayabileceğiniz diğer parametreler şunlardır:

- **long_query_time**: bir sorguyu `long_query_time` (saniye cinsinden) tamamlandıktan sonra günlüğe kaydedin. Varsayılan değer 10 saniyedir.
- **log_slow_admin_statements**: yönetim deyimlerinin (örn.) olup olmadığını belirler. `ALTER_TABLE`, `ANALYZE_TABLE` ) günlüğe kaydedilir.
- **log_queries_not_using_indexes**: dizinleri kullanmayan sorguların günlüğe kaydedileceğini belirler.
- **log_throttle_queries_not_using_indexes**: yavaş sorgu günlüğüne yazılabilen dizinlenmemiş sorguların sayısını kısıtlar. Bu parametre `log_queries_not_using_indexes` *Açık* olarak ayarlandığında geçerli olur

> [!IMPORTANT]
> Tablolarınızın dizini oluşturulmamış ise, `log_queries_not_using_indexes` `log_throttle_queries_not_using_indexes` Bu dizinlenmemiş tablolarda çalışan tüm sorgular yavaş sorgu günlüğüne yazılacak olduğundan ve parametrelerini **Açık** olarak ayarlamak MySQL performansını etkileyebilir.

Yavaş sorgu günlüğü parametrelerinin tam açıklamaları için MySQL 'in [yavaş sorgu günlüğü belgelerini](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html) inceleyin.

## <a name="access-slow-query-logs"></a>Yavaş sorgu günlüklerine erişin

Yavaş sorgu günlükleri Azure Izleyici tanılama ayarlarıyla tümleşiktir. MySQL esnek sunucunuzda yavaş sorgu günlüklerini etkinleştirdikten sonra Azure Izleyici günlüklerine, Event Hubs veya Azure Storage 'a yayabilirsiniz. Tanılama ayarları hakkında daha fazla bilgi edinmek için bkz. [tanılama günlükleri belgeleri](../../azure-monitor/essentials/platform-logs-overview.md). Azure portal tanılama ayarlarının nasıl etkinleştirileceği hakkında daha fazla bilgi edinmek için, [yavaş sorgu günlüğü portalı makalesine](how-to-configure-slow-query-logs-portal.md#set-up-diagnostics)bakın.

Aşağıdaki tabloda, yavaş sorgu günlüğü çıkışı açıklanmaktadır. Çıkış yöntemine bağlı olarak, dahil edilen alanlar ve göründükleri sıralama farklılık gösterebilir.

| **Özellik** | **Açıklama** |
|---|---|
| `TenantId` | Kiracı KIMLIĞINIZ |
| `SourceSystem` | `Azure` |
| `TimeGenerated` UTC | Günlük kaydedildiği zaman damgası (UTC) |
| `Type` | Günlüğün türü. Her `AzureDiagnostics` |
| `SubscriptionId` | Sunucunun ait olduğu abonelik için GUID |
| `ResourceGroup` | Sunucunun ait olduğu kaynak grubunun adı |
| `ResourceProvider` | Kaynak sağlayıcının adı. Her `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | Kaynak URI 'SI |
| `Resource` | Sunucunun adı |
| `Category` | `MySqlSlowLogs` |
| `OperationName` | `LogEvent` |
| `Logical_server_name_s` | Sunucunun adı |
| `start_time_t` UTC | Sorgunun başladığı zaman |
| `query_time_s` | Sorgunun yürütülmesi için geçen saniye cinsinden toplam süre |
| `lock_time_s` | Sorgunun kilitlenme saniye cinsinden toplam süre |
| `user_host_s` | Kullanıcı adı |
| `rows_sent_s` | Gönderilen satır sayısı |
| `rows_examined_s` | İncelenen satır sayısı |
| `last_insert_id_s` | [last_insert_id](https://dev.mysql.com/doc/refman/5.7/en/information-functions.html#function_last-insert-id) |
| `insert_id_s` | KIMLIĞI Ekle |
| `sql_text_s` | Tam sorgu |
| `server_id_s` | Sunucunun KIMLIĞI |
| `thread_id_s` | İş parçacığı KIMLIĞI |
| `\_ResourceId` | Kaynak URI 'SI |

> [!Note]
> İçin `sql_text_s` , 2048 karakteri aşarsa günlük kesilecek.

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

- Tanılama günlükleri etkin olan tüm MySQL sunucularında 10 saniyeden uzun sorguları görüntüle

    ```Kusto
    AzureDiagnostics
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | where query_time_d > 10
    ```    
    
## <a name="next-steps"></a>Sonraki adımlar
- [Denetim günlükleri](concepts-audit-logs.md) hakkında daha fazla bilgi edinin
- [Azure Portal](how-to-configure-slow-query-logs-portal.md) yavaş sorgu günlüklerini yapılandırma
<!-- - [How to configure slow query logs from the Azure CLI](howto-configure-server-logs-in-cli.md). -->