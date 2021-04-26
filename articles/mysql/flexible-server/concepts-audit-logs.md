---
title: Denetim günlükleri-MySQL için Azure veritabanı-esnek sunucu
description: MySQL için Azure veritabanı esnek sunucusu 'nda bulunan denetim günlüklerini açıklar.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 9/21/2020
ms.openlocfilehash: 1232a0753c988f5a28ebba28f9819aa67ce28603
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101718752"
---
# <a name="track-database-activity-with-audit-logs-in-azure-database-for-mysql-flexible-server"></a>MySQL için Azure veritabanı esnek sunucusu 'nda denetim günlükleriyle veritabanı etkinliğini izleme

> [!IMPORTANT] 
> MySQL için Azure veritabanı esnek sunucu şu anda genel önizlemede

MySQL için Azure veritabanı esnek sunucu kullanıcılara denetim günlüklerini yapılandırma olanağı sağlar. Denetim günlükleri, bağlantı, yönetici, DDL ve DML olayları dahil olmak üzere veritabanı düzeyi etkinliğini izlemek için kullanılabilir. Bu tür günlükler genellikle uyumluluk amacıyla kullanılır.

## <a name="configure-audit-logging"></a>Denetim günlüğünü yapılandırma

>[!IMPORTANT]
> Sunucunuzun performansının yoğun bir şekilde etkilenmemesini sağlamak için, yalnızca denetim amaçlarıyla gerekli olay türlerini ve kullanıcıları günlüğe kaydetmek önerilir.

Varsayılan olarak, denetim günlükleri devre dışıdır. Bunları etkinleştirmek için `audit_log_enabled` sunucu parametresini *Açık* olarak ayarlayın. Bu, Azure portal veya Azure CLı kullanılarak yapılandırılabilir <!-- add link to server parameter-->. 

Denetim günlüğü davranışlarını denetlemek için ayarlayabileceğiniz diğer parametreler şunlardır:

- `audit_log_events`: günlüğe kaydedilecek olayları denetler. Belirli denetim olayları için aşağıdaki tabloya bakın.
- `audit_log_include_users`: Günlük kaydına eklenecek MySQL kullanıcıları. Bu parametre için varsayılan değer boştur; bu, günlüğe kaydedilecek tüm kullanıcıları içerir. Bunun önceliği daha yüksektir `audit_log_exclude_users` . Parametrenin uzunluk üst sınırı 512 karakterdir.
- `audit_log_exclude_users`: MySQL kullanıcılarının günlüğe kaydetme dışında tutulması. Parametrenin uzunluk üst sınırı 512 karakterdir.

> [!NOTE]
> `audit_log_include_users` daha yüksek önceliğe sahiptir `audit_log_exclude_users` . Örneğin, ve ise `audit_log_include_users`  =  `demouser` `audit_log_exclude_users`  =  `demouser` , Kullanıcı `audit_log_include_users` daha yüksek önceliğe sahip olduğu için denetim günlüklerine dahil edilir.

| **Olay** | **Açıklama** |
|---|---|
| `CONNECTION` | -Bağlantı başlatma (başarılı veya başarısız) <br> -Oturum sırasında farklı kullanıcı/parola ile Kullanıcı yeniden kimlik doğrulaması <br> -Bağlantı sonlandırma |
| `DML_SELECT`| Sorguları Seç |
| `DML_NONSELECT` | EKLEME/SILME/GÜNCELLEŞTIRME sorguları |
| `DML` | DML = DML_SELECT + DML_NONSELECT |
| `DDL` | "VERITABANıNı bırak" gibi sorgular |
| `DCL` | "Izın ver" gibi sorgular |
| `ADMIN` | "Durumu göster" gibi sorgular |
| `GENERAL` | DML_SELECT, DML_NONSELECT, DML, DDL, DCL ve ADMIN içinde tümü |
| `TABLE_ACCESS` | -SELECT veya INSERT gibi tablo okuma deyimleri... SEÇIN <br> -DELETE veya TRUNCATE TABLE gibi tablo silme deyimleri <br> -INSERT veya REPLACE gibi tablo ekleme deyimleri <br> -UPDATE gibi tablo güncelleştirme deyimleri |

## <a name="access-audit-logs"></a>Denetim günlüklerine erişme

Denetim günlükleri Azure Izleyici tanılama ayarlarıyla tümleşiktir. MySQL esnek sunucunuzda denetim günlüklerini etkinleştirdikten sonra Azure Izleyici günlüklerine, Event Hubs veya Azure Storage 'a yayabilirsiniz. Tanılama ayarları hakkında daha fazla bilgi edinmek için bkz. [tanılama günlükleri belgeleri](../../azure-monitor/essentials/platform-logs-overview.md). Azure portal tanılama ayarlarının nasıl etkinleştirileceği hakkında daha fazla bilgi edinmek için [Denetim günlüğü portalı makalesine](how-to-configure-audit-logs-portal.md#set-up-diagnostics)bakın.

Aşağıdaki bölümlerde, olay türüne göre MySQL denetim günlüklerinin çıktısı açıklanır. Çıkış yöntemine bağlı olarak, dahil edilen alanlar ve göründükleri sıralama farklılık gösterebilir.

### <a name="connection"></a>Bağlantı

| **Özellik** | **Açıklama** |
|---|---|
| `TenantId` | Kiracı KIMLIĞINIZ |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Günlük kaydedildiği zaman damgası (UTC) |
| `Type` | Günlüğün türü. Her `AzureDiagnostics` |
| `SubscriptionId` | Sunucunun ait olduğu abonelik için GUID |
| `ResourceGroup` | Sunucunun ait olduğu kaynak grubunun adı |
| `ResourceProvider` | Kaynak sağlayıcının adı. Her `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | Kaynak URI 'SI |
| `Resource` | Sunucunun adı |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | Sunucunun adı |
| `event_class_s` | `connection_log` |
| `event_subclass_s` | `CONNECT`, `DISCONNECT`, `CHANGE USER` |
| `connection_id_d` | MySQL tarafından oluşturulan benzersiz bağlantı KIMLIĞI |
| `host_s` | Boş |
| `ip_s` | MySQL 'e bağlanan istemcinin IP adresi |
| `user_s` | Sorguyu yürüten kullanıcının adı |
| `db_s` | Bağlanılan veritabanının adı |
| `\_ResourceId` | Kaynak URI 'SI |

### <a name="general"></a>Genel

Aşağıdaki şema genel, DML_SELECT, DML_NONSELECT, DML, DDL, DCL ve yönetıcı olay türleri için geçerlidir.

> [!NOTE]
> İçin `sql_text_s` , 2048 karakteri aşarsa günlük kesilecek.

| **Özellik** | **Açıklama** |
|---|---|
| `TenantId` | Kiracı KIMLIĞINIZ |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Günlük kaydedildiği zaman damgası (UTC) |
| `Type` | Günlüğün türü. Her `AzureDiagnostics` |
| `SubscriptionId` | Sunucunun ait olduğu abonelik için GUID |
| `ResourceGroup` | Sunucunun ait olduğu kaynak grubunun adı |
| `ResourceProvider` | Kaynak sağlayıcının adı. Her `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | Kaynak URI 'SI |
| `Resource` | Sunucunun adı |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | Sunucunun adı |
| `event_class_s` | `general_log` |
| `event_subclass_s` | `LOG`, `ERROR` , `RESULT` (yalnızca MySQL 5,6 için kullanılabilir) |
| `event_time` | UTC zaman damgasında sorgu başlangıç saati |
| `error_code_d` | Sorgu başarısız olduysa hata kodu. `0` hata yok demektir |
| `thread_id_d` | Sorguyu yürüten iş parçacığının KIMLIĞI |
| `host_s` | Boş |
| `ip_s` | MySQL 'e bağlanan istemcinin IP adresi |
| `user_s` | Sorguyu yürüten kullanıcının adı |
| `sql_text_s` | Tam sorgu metni |
| `\_ResourceId` | Kaynak URI 'SI |

### <a name="table-access"></a>Tablo erişimi

> [!NOTE]
> İçin `sql_text_s` , 2048 karakteri aşarsa günlük kesilecek.

| **Özellik** | **Açıklama** |
|---|---|
| `TenantId` | Kiracı KIMLIĞINIZ |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Günlük kaydedildiği zaman damgası (UTC) |
| `Type` | Günlüğün türü. Her `AzureDiagnostics` |
| `SubscriptionId` | Sunucunun ait olduğu abonelik için GUID |
| `ResourceGroup` | Sunucunun ait olduğu kaynak grubunun adı |
| `ResourceProvider` | Kaynak sağlayıcının adı. Her `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | Kaynak URI 'SI |
| `Resource` | Sunucunun adı |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | Sunucunun adı |
| `event_class_s` | `table_access_log` |
| `event_subclass_s` | `READ`, `INSERT` , `UPDATE` veya `DELETE` |
| `connection_id_d` | MySQL tarafından oluşturulan benzersiz bağlantı KIMLIĞI |
| `db_s` | Erişilen veritabanının adı |
| `table_s` | Erişilen tablonun adı |
| `sql_text_s` | Tam sorgu metni |
| `\_ResourceId` | Kaynak URI 'SI |

## <a name="analyze-logs-in-azure-monitor-logs"></a>Azure Izleyici günlüklerinde günlükleri analiz etme

Denetim günlüklerinizin tanılama günlükleri aracılığıyla Azure Izleyici günlüklerine alındıktan sonra, denetlenen olaylarınızın daha fazla analizini gerçekleştirebilirsiniz. Aşağıda, başlamanıza yardımcı olacak bazı örnek sorgular verilmiştir. Aşağıdaki öğesini sunucu adınızla güncelleştirdiğinizden emin olun.

- Belirli bir sunucudaki genel olayları listeleme

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs' and event_class_s == "general_log"
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last 
    ```

- Belirli bir sunucudaki bağlantı olaylarını listeleme

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs' and event_class_s == "connection_log"
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last
    ```

- Belirli bir sunucuda denetlenen olayları özetleme

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | summarize count() by event_class_s, event_subclass_s, user_s, ip_s
    ```

- Belirli bir sunucuda denetim olayı türü dağıtımını grafiktir

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | summarize count() by LogicalServerName_s, bin(TimeGenerated, 5m)
    | render timechart 
    ```

- Denetim günlükleri için etkinleştirilmiş tanılama günlükleri ile tüm MySQL sunucularındaki denetlenen olayları listeleme

    ```kusto
    AzureDiagnostics
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last
    ``` 

## <a name="next-steps"></a>Sonraki adımlar
- [Yavaş sorgu günlükleri](concepts-slow-query-logs.md) hakkında daha fazla bilgi edinin
- [Azure Portal](how-to-configure-audit-logs-portal.md) denetim sorgusu günlüklerini yapılandırma
<!-- - [How to configure audit logs in the Azure portal](howto-configure-audit-logs-portal.md)-->
