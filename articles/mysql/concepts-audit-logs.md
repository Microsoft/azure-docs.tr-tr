---
title: Denetim günlükleri-MySQL için Azure veritabanı
description: MySQL için Azure veritabanı 'nda kullanılabilen Denetim günlüklerini ve günlük düzeylerini etkinleştirmek için kullanılabilen parametreleri açıklar.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 6/24/2020
ms.openlocfilehash: fa845e7c402073a64f51d75b1da51f56142eee2e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98630519"
---
# <a name="audit-logs-in-azure-database-for-mysql"></a>MySQL için Azure veritabanı 'nda denetim günlükleri

MySQL için Azure veritabanı 'nda, denetim günlüğü kullanıcılar tarafından kullanılabilir. Denetim günlüğü, veritabanı düzeyindeki etkinlikleri izlemek için ve genellikle uyumluluk için kullanılır.

## <a name="configure-audit-logging"></a>Denetim günlüğünü yapılandırma

>[!IMPORTANT]
> Sunucunuzun performansının yoğun bir şekilde etkilenmemesini sağlamak için, yalnızca denetim amaçlarıyla gerekli olay türlerini ve kullanıcıları günlüğe kaydetmek önerilir.

Varsayılan olarak, denetim günlüğü devre dışıdır. Etkinleştirmek için, `audit_log_enabled` Açık olarak ayarlayın.

Ayarlayabileceğiniz diğer parametreler şunlardır:

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
| `TABLE_ACCESS` | -MySQL 5,7 ve MySQL 8,0 için kullanılabilir <br> -SELECT veya INSERT gibi tablo okuma deyimleri... SEÇIN <br> -DELETE veya TRUNCATE TABLE gibi tablo silme deyimleri <br> -INSERT veya REPLACE gibi tablo ekleme deyimleri <br> -UPDATE gibi tablo güncelleştirme deyimleri |

## <a name="access-audit-logs"></a>Denetim günlüklerine erişme

Denetim günlükleri Azure İzleyici Tanılama Günlükleri ile tümleştirilir. MySQL sunucunuzda denetim günlüklerini etkinleştirdikten sonra bunları Azure İzleyici günlüklerine, Event Hubs’a veya Azure Depolama’ya yerleştirebilirsiniz. Azure portal tanılama günlüklerinin nasıl etkinleştirileceği hakkında daha fazla bilgi edinmek için [Denetim günlüğü portalı makalesine](howto-configure-audit-logs-portal.md#set-up-diagnostic-logs)bakın.

## <a name="diagnostic-logs-schemas"></a>Tanılama günlükleri şemaları

Aşağıdaki bölümlerde, olay türüne göre MySQL denetim günlükleri tarafından yapılan çıktılar açıklanır. Çıkış yöntemine bağlı olarak, dahil edilen alanlar ve göründükleri sıralama farklılık gösterebilir.

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
| `event_subclass_s` | `CONNECT`, `DISCONNECT` , `CHANGE USER` (yalnızca MySQL 5,7 için kullanılabilir) |
| `connection_id_d` | MySQL tarafından oluşturulan benzersiz bağlantı KIMLIĞI |
| `host_s` | Boş |
| `ip_s` | MySQL 'e bağlanan istemcinin IP adresi |
| `user_s` | Sorguyu yürüten kullanıcının adı |
| `db_s` | Bağlanılan veritabanının adı |
| `\_ResourceId` | Kaynak URI 'SI |

### <a name="general"></a>Genel

Aşağıdaki şema genel, DML_SELECT, DML_NONSELECT, DML, DDL, DCL ve yönetıcı olay türleri için geçerlidir.

> [!NOTE]
> İçin `sql_text` , 2048 karakteri aşarsa günlük kesilecek.

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
> Tablo erişim günlükleri yalnızca MySQL 5,7 için çıkışlardır.<br>İçin `sql_text` , 2048 karakteri aşarsa günlük kesilecek.

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

- [Azure portal denetim günlüklerini yapılandırma](howto-configure-audit-logs-portal.md)
