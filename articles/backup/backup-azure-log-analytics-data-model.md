---
title: Azure Izleyici günlükleri veri modeli
description: Bu makalede, Azure Backup verileri için Azure Izleyici Log Analytics veri modeli ayrıntıları hakkında bilgi edinin.
ms.topic: conceptual
ms.date: 02/26/2019
ms.openlocfilehash: 055a36fb2aa8e9048089a338f57555242e8eb754
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173023"
---
# <a name="log-analytics-data-model-for-azure-backup-data"></a>Azure Backup verileri için Log Analytics veri modeli

Log Analytics özel uyarılar oluşturmak için Log Analytics veri modelini kullanın.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="using-azure-backup-data-model"></a>Azure Backup veri modelini kullanma

Gereksinimlerinize göre görseller, özel sorgular ve panolar oluşturmak için veri modelinin bir parçası olarak sunulan aşağıdaki alanları kullanabilirsiniz.

### <a name="alert"></a>Uyarı

Bu tablo, uyarı ile ilgili alanlarla ilgili ayrıntıları sağlar.

| Alan | Veri Türü | Açıklama |
| --- | --- | --- |
| AlertUniqueId_s |Metin |Oluşturulan uyarının benzersiz tanıtıcısı |
| AlertType_s |Metin |Uyarının türü, örneğin, yedekleme |
| AlertStatus_s |Metin |Uyarının durumu, örneğin, etkin |
| AlertOccurrenceDateTime_s |Tarih/saat |Uyarının oluşturulduğu tarih ve saat |
| AlertSeverity_s |Metin |Uyarının önem derecesi, örneğin, kritik |
|AlertTimeToResolveInMinutes_s    | Sayı        |Bir uyarının çözülmesi için geçen süre. Etkin uyarılar için boş.         |
|AlertConsolidationStatus_s   |Metin         |Uyarının birleştirilmiş bir uyarı olup olmadığını tanımla         |
|CountOfAlertsConsolidated_s     |Sayı         |Birleştirilmiş bir uyarıda birleştirilmiş uyarı sayısı          |
|AlertRaisedOn_s     |Metin         |Uyarının üzerinde ortaya çıkarılan varlık türü         |
|AlertCode_s     |Metin         |Uyarı türünü benzersiz şekilde tanımlamak için kod         |
|RecommendedAction_s   |Metin         |Uyarıyı çözümlemek için önerilen eylem         |
| EventName_s |Metin |Olayın adı. Always AzureBackupCentralReport |
| BackupItemUniqueId_s |Metin |Uyarıyla ilişkili yedekleme öğesinin benzersiz tanımlayıcısı |
| SchemaVersion_s |Metin |Şemanın geçerli sürümü, örneğin **v2** |
| State_s |Metin |Uyarı nesnesinin geçerli durumu, örneğin, etkin, silindi |
| BackupManagementType_s |Metin |Yedekleme gerçekleştirmek için sağlayıcı türü, örneğin, bu uyarının ait olduğu ıaasvm, FileFolder |
| ThrottledRequests |Metin |Geçerli işlemin adı, örneğin, uyarı |
| Kategori |Metin |Azure Izleyici günlüklerine gönderilen tanılama verileri kategorisi. Always AzureBackupReport |
| Kaynak |Metin |Bu, verilerin toplandığı kaynaktır, kurtarma hizmetleri kasasının adını gösterir |
| ProtectedContainerUniqueId_s |Metin |Uyarıyla ilişkili korumalı sunucunun benzersiz tanıtıcısı (v1 'de ProtectedServerUniqueId_s)|
| VaultUniqueId_s |Metin |Uyarıyla ilişkili korumalı kasasının benzersiz tanıtıcısı |
| SourceSystem |Metin |Geçerli verilerin kaynak sistemi-Azure |
| ResourceId |Metin |Verilerin toplandığı kaynak için benzersiz tanımlayıcı. Örneğin, bir kurtarma hizmetleri Kasası kaynak KIMLIĞI |
| SubscriptionId |Metin |Kaynağın abonelik tanımlayıcısı (örn. Verilerin toplandığı kurtarma hizmetleri Kasası) |
| ResourceGroup |Metin |Kaynağın kaynak grubu (örn. Verilerin toplandığı kurtarma hizmetleri Kasası) |
| ResourceProvider |Metin |Verilerin toplandığı kaynak sağlayıcısı. Örneğin, Microsoft. RecoveryServices |
| ResourceType |Metin |Verilerin toplandığı kaynak türü. Örneğin, kasa |

### <a name="backupitem"></a>Backupıtem

Bu tablo, yedekleme öğesiyle ilgili alanlarla ilgili ayrıntıları sağlar.

| Alan | Veri Türü | Açıklama |
| --- | --- | --- |
| EventName_s |Metin |Olayın adı. Always AzureBackupCentralReport |  
| BackupItemUniqueId_s |Metin |Yedekleme öğesinin benzersiz tanımlayıcısı |
| BackupItemId_s |Metin |Yedekleme öğesinin tanımlayıcısı (Bu alan yalnızca v1 şeması içindir) |
| BackupItemName_s |Metin |Yedekleme öğesinin adı |
| BackupItemFriendlyName_s |Metin |Yedekleme öğesinin kolay adı |
| BackupItemType_s |Metin |Yedekleme öğesinin türü, örneğin, VM, dosya klasörü |
| BackupItemProtectionState_s |Metin |Yedekleme öğesinin koruma durumu |
| BackupItemAppVersion_s |Metin |Yedekleme öğesinin uygulama sürümü |
| ProtectionState_s |Metin |Yedekleme öğesinin geçerli koruma durumu, örneğin korumalı, Protectiondurduruldu |
| ProtectionGroupName_s |Metin | Yedekleme öğesinin korunan koruma grubunun adı, SC DPM ve varsa MABS için|
| SecondaryBackupProtectionState_s |Metin |Yedekleme öğesi için ikincil korumanın etkinleştirilip etkinleştirilmediği|
| SchemaVersion_s |Metin |Şemanın sürümü, örneğin, **v2** |
| State_s |Metin |Yedekleme öğesi nesnesinin durumu, örneğin, etkin, silindi |
| BackupManagementType_s |Metin |Yedekleme gerçekleştirmek için sağlayıcı türü, örneğin, bu yedekleme öğesinin ait olduğu ıaasvm, FileFolder |
| ThrottledRequests |Metin |İşlemin adı, örneğin, Backupıtem |
| Kategori |Metin |Azure Izleyici günlüklerine gönderilen tanılama verileri kategorisi. Always AzureBackupReport |
| Kaynak |Metin |Verilerin toplandığı kaynak (örneğin, kurtarma hizmetleri Kasası adı) |
| SourceSystem |Metin |Geçerli verilerin kaynak sistemi-Azure |
| ResourceId |Metin |Toplanmakta olan verilerin kaynak KIMLIĞI, örneğin kurtarma hizmetleri Kasası kaynak KIMLIĞI |
| SubscriptionId |Metin |Kaynağın abonelik tanımlayıcısı (örn. Toplanan veriler için kurtarma hizmetleri Kasası) |
| ResourceGroup |Metin |Kaynağın kaynak grubu (örn. Toplanan veriler için kurtarma hizmetleri Kasası) |
| ResourceProvider |Metin |Toplanmakta olan veriler için kaynak sağlayıcı, örneğin, Microsoft. RecoveryServices |
| ResourceType |Metin |Toplanmakta olan veriler için kaynak türü, örneğin Kasakasaları |

### <a name="backupitemassociation"></a>BackupItemAssociation

Bu tablo, çeşitli varlıklarla yedekleme öğesi ilişkilendirmeleri hakkında ayrıntılar sağlar.

| Alan | Veri Türü | Açıklama |
| --- | --- | --- |
| EventName_s |Metin |Bu alan, bu olayın adını temsil eder, her zaman AzureBackupCentralReport |  
| BackupItemUniqueId_s |Metin |Yedekleme öğesinin benzersiz KIMLIĞI |
| SchemaVersion_s |Metin |Bu alan, şemanın geçerli sürümünü belirtir, **v2** 'dir |
| State_s |Metin |Yedekleme öğesi ilişkilendirme nesnesinin geçerli durumu, örneğin, etkin, silindi |
| BackupManagementType_s |Metin |Yedekleme işi yapan sunucu için sağlayıcı türü, örneğin ıaasvm, FileFolder |
| BackupItemSourceSize_s |Metin | Yedekleme öğesinin ön uç boyutu |
| BackupManagementServerUniqueId_s |Metin | Yedekleme yönetim sunucusunu benzersiz şekilde tanımlamak için alan, varsa yedekleme öğesi tarafından korunur |
| Kategori |Metin |Bu alan, Log Analytics gönderilen tanılama verilerinin kategorisini temsil eder, AzureBackupReport |
| ThrottledRequests |Metin |Bu alan geçerli işlemin adını temsil eder-BackupItemAssociation |
| Kaynak |Metin |Bu, verilerin toplandığı kaynaktır, kurtarma hizmetleri kasasının adını gösterir |
| ProtectedContainerUniqueId_s |Metin |Yedekleme öğesiyle ilişkili korumalı sunucunun benzersiz tanıtıcısı (v1 'de ProtectedServerUniqueId_s) |
| VaultUniqueId_s |Metin |Yedekleme öğesini içeren kasanın benzersiz tanımlayıcısı |
| SourceSystem |Metin |Geçerli verilerin kaynak sistemi-Azure |
| ResourceId |Metin |Toplanmakta olan veriler için kaynak tanımlayıcısı. Örneğin, kurtarma hizmetleri Kasası kaynak KIMLIĞI |
| SubscriptionId |Metin |Kaynağın abonelik tanımlayıcısı (örn. Verilerin toplandığı kurtarma hizmetleri Kasası) |
| ResourceGroup |Metin |Kaynağın kaynak grubu (örn. Verilerin toplandığı kurtarma hizmetleri Kasası) |
| ResourceProvider |Metin |Toplanmakta olan veriler için kaynak sağlayıcı, örneğin, Microsoft. RecoveryServices |
| ResourceType |Metin |Verilerin toplandığı kaynak türü, örneğin kasa |

### <a name="backupmanagementserver"></a>BackupManagementServer

Bu tablo, çeşitli varlıklarla yedekleme öğesi ilişkilendirmeleri hakkında ayrıntılar sağlar.

| Alan | Veri Türü | Açıklama |
| --- | --- | --- |
|BackupManagementServerName_s     |Metin         |Yedekleme yönetim sunucusunun adı        |
|AzureBackupAgentVersion_s     |Metin         |Yedekleme yönetim sunucusundaki Azure Backup aracısının sürümü          |
|BackupManagementServerVersion_s     |Metin         |Yedekleme yönetim sunucusunun sürümü|
|BackupManagementServerOSVersion_s    |Metin            |Yedekleme yönetim sunucusunun işletim sistemi sürümü|
|BackupManagementServerType_s     |Metin         |Yedekleme yönetim sunucusunun türü, MABS, SC DPM olarak|
|BackupManagementServerUniqueId_s     |Metin         |Yedekleme yönetim sunucusunu benzersiz olarak tanımlamak için alan       |
| SourceSystem |Metin |Geçerli verilerin kaynak sistemi-Azure |
| ResourceId |Metin |Toplanmakta olan veriler için kaynak tanımlayıcısı. Örneğin, kurtarma hizmetleri Kasası kaynak KIMLIĞI |
| SubscriptionId |Metin |Kaynağın abonelik tanımlayıcısı (örn. Verilerin toplandığı kurtarma hizmetleri Kasası) |
| ResourceGroup |Metin |Kaynağın kaynak grubu (örn. Verilerin toplandığı kurtarma hizmetleri Kasası) |
| ResourceProvider |Metin |Toplanmakta olan veriler için kaynak sağlayıcı, örneğin, Microsoft. RecoveryServices |
| ResourceType |Metin |Verilerin toplandığı kaynak türü, örneğin kasa |

### <a name="job"></a>İş

Bu tablo, işle ilgili alanlarla ilgili ayrıntıları sağlar.

| Alan | Veri Türü | Açıklama |
| --- | --- | --- |
| EventName_s |Metin |Olayın adı. Always AzureBackupCentralReport |
| BackupItemUniqueId_s |Metin |Yedekleme öğesinin benzersiz tanımlayıcısı |
| SchemaVersion_s |Metin |Şemanın sürümü, örneğin, **v2** |
| State_s |Metin |İş nesnesinin geçerli durumu, örneğin, etkin, silindi |
| BackupManagementType_s |Metin |Yedekleme işi yapan sunucu için sağlayıcı türü, örneğin ıaasvm, FileFolder |
| ThrottledRequests |Metin |Bu alan geçerli işlemin adını temsil eder-Iş |
| Kategori |Metin |Bu alan, Azure Izleyici günlüklerine gönderilen tanılama verilerinin kategorisini temsil eder, AzureBackupReport |
| Kaynak |Metin |Bu, verilerin toplandığı kaynaktır, kurtarma hizmetleri kasasının adını gösterir |
| ProtectedServerUniqueId_s |Metin |İşle ilişkili korumalı sunucunun benzersiz tanıtıcısı |
| ProtectedContainerUniqueId_s |Metin | İşin çalıştırıldığı korunan kapsayıcıyı belirlemek için benzersiz KIMLIK |
| VaultUniqueId_s |Metin |Korumalı kasanın benzersiz tanıtıcısı |
| JobOperation_s |Metin |İşin çalıştırıldığı işlem Örneğin, yedekleme, geri yükleme, yedeklemeyi yapılandırma |
| JobStatus_s |Metin |Tamamlanmış işin durumu, örneğin, tamamlandı, başarısız |
| JobFailureCode_s |Metin |İş hatası oluştuğundan hata kodu dizesi |
| JobStartDateTime_s |Tarih/saat |İşin çalışmaya başladığı tarih ve saat |
| BackupStorageDestination_s |Metin |Yedekleme depolamanın hedefi, örneğin, bulut, disk  |
| AdHocOrScheduledJob_s |Metin | İşin geçici veya zamanlanmış olduğunu belirten alan |
| JobDurationInSecs_s | Sayı |Saniye cinsinden toplam iş süresi |
| DataTransferredInMB_s | Sayı |Bu iş için MB olarak aktarılan veriler|
| JobUniqueId_g |Metin |İşi tanımlamak için benzersiz KIMLIK |
| RecoveryJobDestination_s |Metin | Verilerin kurtarıldığı bir kurtarma işinin hedefi |
| RecoveryJobRPDateTime_s |DateTime | Kurtarılan kurtarma noktasının oluşturulduğu tarih, saat |
| RecoveryJobRPLocation_s |Metin | Kurtarılan kurtarma noktasının depolandığı konum|
| SourceSystem |Metin |Geçerli verilerin kaynak sistemi-Azure |
| ResourceId |Metin |Toplanmakta olan veriler için kaynak tanımlayıcısı. Örneğin, kurtarma hizmetleri Kasası kaynak KIMLIĞI|
| SubscriptionId |Metin |Kaynağın abonelik tanımlayıcısı (örn. Verilerin toplandığı kurtarma hizmetleri Kasası) |
| ResourceGroup |Metin |Kaynağın kaynak grubu (örn. Verilerin toplandığı kurtarma hizmetleri Kasası) |
| ResourceProvider |Metin |Verilerin toplandığı kaynak sağlayıcısı. Örneğin, Microsoft. RecoveryServices |
| ResourceType |Metin |Verilerin toplandığı kaynak türü. Örneğin, kasa |

### <a name="policy"></a>İlke

Bu tablo ilkeyle ilgili alanlarla ilgili ayrıntıları sağlar.

| Alan | Veri Türü | Uygulanabilir sürümler | Açıklama |
| --- | --- | --- | --- |
| EventName_s |Metin ||Bu alan, bu olayın adını temsil eder, her zaman AzureBackupCentralReport |
| SchemaVersion_s |Metin ||Bu alan, şemanın geçerli sürümünü belirtir, **v2** 'dir |
| State_s |Metin ||İlke nesnesinin geçerli durumu, örneğin, etkin, silindi |
| BackupManagementType_s |Metin ||Yedekleme işi yapan sunucu için sağlayıcı türü, örneğin ıaasvm, FileFolder |
| ThrottledRequests |Metin ||Bu alan geçerli işlemin adını temsil eder-Ilke |
| Kategori |Metin ||Bu alan, Azure Izleyici günlüklerine gönderilen tanılama verilerinin kategorisini temsil eder, AzureBackupReport |
| Kaynak |Metin ||Bu, verilerin toplandığı kaynaktır, kurtarma hizmetleri kasasının adını gösterir |
| PolicyUniqueId_g |Metin ||İlkeyi tanımlamak için benzersiz KIMLIK |
| PolicyName_s |Metin ||Tanımlanan ilkenin adı |
| BackupFrequency_s |Metin ||Yedeklemelerin çalıştırıldığı sıklık (örneğin, günlük, haftalık) |
| BackupTimes_s |Metin ||Yedeklemelerin zamanlandığı tarih ve saat |
| BackupDaysOfTheWeek_s |Metin ||Yedeklemelerin zamanlandığı haftanın günleri |
| RetentionDuration_s |Tam sayı ||Yapılandırılan yedeklemeler için bekletme süresi |
| DailyRetentionDuration_s |Tam sayı ||Yapılandırılan yedeklemeler için gün cinsinden toplam saklama süresi |
| DailyRetentionTimes_s |Metin ||Günlük bekletmenin yapılandırıldığı tarih ve saat |
| WeeklyRetentionDuration_s |Ondalık sayı ||Yapılandırılan yedeklemeler için hafta cinsinden toplam haftalık saklama süresi |
| WeeklyRetentionTimes_s |Metin ||Haftalık bekletmenin yapılandırıldığı tarih ve saat |
| WeeklyRetentionDaysOfTheWeek_s |Metin ||Haftalık saklama için seçilen haftanın günleri |
| MonthlyRetentionDuration_s |Ondalık sayı ||Yapılandırılan yedeklemeler için aylık toplam bekletme süresi |
| MonthlyRetentionTimes_s |Metin ||Aylık bekletmenin yapılandırıldığı tarih ve saat |
| MonthlyRetentionFormat_s |Metin ||Aylık saklama için yapılandırma türü, örneğin günlük tabanlı için günlük, haftalık tabanlı için haftalık |
| MonthlyRetentionDaysOfTheWeek_s |Metin ||Aylık saklama için seçilen haftanın günleri |
| MonthlyRetentionWeeksOfTheMonth_s |Metin ||Aylık bekletme yapılandırıldığında ayın haftası (örneğin, First, Last vb.) |
| YearlyRetentionDuration_s |Ondalık sayı ||Yapılandırılan yedeklemeler için yıl cinsinden toplam saklama süresi |
| YearlyRetentionTimes_s |Metin ||Yıllık bekletmenin yapılandırıldığı tarih ve saat |
| YearlyRetentionMonthsOfTheYear_s |Metin ||Yıllık bekletme için seçilen yılın ayı |
| YearlyRetentionFormat_s |Metin ||Yıllık saklama için yapılandırma türü, örneğin, günlük tabanlı için günlük, haftalık tabanlı için haftalık | |
| YearlyRetentionDaysOfTheMonth_s |Metin ||Yıllık bekletme için seçilen ayın tarihleri |
| SynchronisationFrequencyPerDay_s |Tam sayı |v2|SC DPM ve MABS için bir dosya yedeklemesinin eşitlendiği gün sayısı |
| DiffBackupFormat_s |Metin |v2|Azure VM yedeklemesi 'nde SQL için değişiklik yedeklemelerinin biçimi |
| DiffBackupTime_s |Zaman |v2|Azure VM yedeklemesi 'nde SQL için değişiklik yedeklemeleri süresi|
| DiffBackupRetentionDuration_s |Ondalık sayı |v2|Azure VM yedeklemesi 'nde SQL için değişiklik yedeklemeleri bekletme süresi|
| LogBackupFrequency_s |Ondalık sayı |v2|SQL için günlük yedeklemeleri sıklığı|
| LogBackupRetentionDuration_s |Ondalık sayı |v2|Azure VM yedeklemesi 'nde SQL için günlük yedeklemeleri bekletme süresi|
| DiffBackupDaysofTheWeek_s |Metin |v2|Azure VM yedeklemesi 'nde SQL için değişiklik yedeklemeleri için haftanın günleri|
| SourceSystem |Metin ||Geçerli verilerin kaynak sistemi-Azure |
| ResourceId |Metin ||Toplanmakta olan veriler için kaynak tanımlayıcısı. Örneğin, kurtarma hizmetleri Kasası kaynak KIMLIĞI |
| SubscriptionId |Metin ||Kaynağın abonelik tanımlayıcısı (örn. Verilerin toplandığı kurtarma hizmetleri Kasası) |
| ResourceGroup |Metin ||Kaynağın kaynak grubu (örn. Verilerin toplandığı kurtarma hizmetleri Kasası) |
| ResourceProvider |Metin ||Verilerin toplandığı kaynak sağlayıcısı. Örneğin, Microsoft. RecoveryServices |
| ResourceType |Metin ||Verilerin toplandığı kaynak türü. Örneğin, kasa |

### <a name="policyassociation"></a>Poliyassociation

Bu tablo, çeşitli varlıklarla ilke ilişkilendirmeleri hakkında ayrıntılar sağlar.

| Alan | Veri Türü | Uygulanabilir sürümler | Açıklama |
| --- | --- | --- | --- |
| EventName_s |Metin ||Bu alan, bu olayın adını temsil eder, her zaman AzureBackupCentralReport |
| SchemaVersion_s |Metin ||Bu alan, şemanın geçerli sürümünü belirtir, **v2** 'dir |
| State_s |Metin ||İlke nesnesinin geçerli durumu, örneğin, etkin, silindi |
| BackupManagementType_s |Metin ||Yedekleme işi yapan sunucu için sağlayıcı türü, örneğin ıaasvm, FileFolder |
| ThrottledRequests |Metin ||Bu alan geçerli işlemin adını temsil eder-PolicyAssociation |
| Kategori |Metin ||Bu alan, Azure Izleyici günlüklerine gönderilen tanılama verilerinin kategorisini temsil eder, AzureBackupReport |
| Kaynak |Metin ||Bu, verilerin toplandığı kaynaktır, kurtarma hizmetleri kasasının adını gösterir |
| PolicyUniqueId_g |Metin ||İlkeyi tanımlamak için benzersiz KIMLIK |
| VaultUniqueId_s |Metin ||Bu ilkenin ait olduğu kasanın benzersiz KIMLIĞI |
| BackupManagementServerUniqueId_s |Metin |v2 |Yedekleme yönetim sunucusunu benzersiz şekilde tanımlamak için alan, varsa yedekleme öğesi tarafından korunur        |
| SourceSystem |Metin ||Geçerli verilerin kaynak sistemi-Azure |
| ResourceId |Metin ||Toplanmakta olan veriler için kaynak tanımlayıcısı. Örneğin, kurtarma hizmetleri Kasası kaynak KIMLIĞI |
| SubscriptionId |Metin ||Kaynağın abonelik tanımlayıcısı (örn. Verilerin toplandığı kurtarma hizmetleri Kasası) |
| ResourceGroup |Metin ||Kaynağın kaynak grubu (örn. Verilerin toplandığı kurtarma hizmetleri Kasası) |
| ResourceProvider |Metin ||Verilerin toplandığı kaynak sağlayıcısı. Örneğin, Microsoft. RecoveryServices |
| ResourceType |Metin ||Verilerin toplandığı kaynak türü. Örneğin, kasa |

### <a name="protected-container"></a>Korumalı kapsayıcı

Bu tablo, korumalı kapsayıcılar hakkında temel alanlar sağlar. (V1 'de ProtectedServer idi)

| Alan | Veri Türü | Açıklama |
| --- | --- | --- |
| ProtectedContainerUniqueId_s |Metin | Korumalı kapsayıcıyı benzersiz olarak tanımlamak için alan |
| ProtectedContainerOSType_s |Metin |Korumalı kapsayıcının işletim sistemi türü |
| ProtectedContainerOSVersion_s |Metin |Korumalı kapsayıcının işletim sistemi sürümü |
| AgentVersion_s |Metin |Aracı yedeklemesinin veya koruma aracısının sürüm numarası (SC DPM ve MABS durumunda) |
| BackupManagementType_s |Metin |Yedekleme gerçekleştirmek için sağlayıcı türü. Örneğin, ıaasvm, FileFolder |
| EntityState_s |Metin |Korumalı sunucu nesnesinin geçerli durumu. Örneğin, etkin, silindi |
| ProtectedContainerFriendlyName_s |Metin |Korumalı sunucunun kolay adı |
| ProtectedContainerName_s |Metin |Korumalı kapsayıcının adı |
| ProtectedContainerWorkloadType_s |Metin |Yedeklenen korumalı kapsayıcının türü. Örneğin, ıaasvmcontainer |
| ProtectedContainerLocation_s |Metin |Korumalı kapsayıcının şirket içinde mi yoksa Azure 'da mı bulunduğu |
| ProtectedContainerType_s |Metin |Korumalı kapsayıcının bir sunucu mı yoksa bir kapsayıcı mi olduğunu belirtir |
| ProtectedContainerProtectionState_s '  |Metin |Korumalı kapsayıcının koruma durumu |

### <a name="storage"></a>Depolama

Bu tablo, depolama ile ilgili alanlarla ilgili ayrıntıları sağlar.

| Alan | Veri Türü | Açıklama |
| --- | --- | --- |
| CloudStorageInBytes_s |Ondalık sayı |Yedeklemeler tarafından kullanılan bulut yedekleme depolaması, en son değere göre hesaplanır (Bu alan yalnızca v1 şeması içindir)|
| ProtectedInstances_s |Ondalık sayı |En son değere göre hesaplanan, faturalandırma aşamasında ön uç depolamayı hesaplamak için kullanılan korumalı örnek sayısı |
| EventName_s |Metin |Bu alan, bu olayın adını temsil eder, her zaman AzureBackupCentralReport |
| SchemaVersion_s |Metin |Bu alan, şemanın geçerli sürümünü belirtir, **v2** 'dir |
| State_s |Metin |Depolama nesnesinin geçerli durumu, örneğin, etkin, silindi |
| BackupManagementType_s |Metin |Yedekleme işi yapan sunucu için sağlayıcı türü, örneğin ıaasvm, FileFolder |
| ThrottledRequests |Metin |Bu alan, geçerli işlem-depolama alanının adını temsil eder |
| Kategori |Metin |Bu alan, Azure Izleyici günlüklerine gönderilen tanılama verilerinin kategorisini temsil eder, AzureBackupReport |
| Kaynak |Metin |Bu, verilerin toplandığı kaynaktır, kurtarma hizmetleri kasasının adını gösterir |
| ProtectedServerUniqueId_s |Metin |Depolama alanının hesaplandığı korumalı sunucunun benzersiz KIMLIĞI |
| VaultUniqueId_s |Metin |Depolama kasasının benzersiz KIMLIĞI hesaplanır |
| SourceSystem |Metin |Geçerli verilerin kaynak sistemi-Azure |
| ResourceId |Metin |Toplanmakta olan veriler için kaynak tanımlayıcısı. Örneğin, kurtarma hizmetleri Kasası kaynak KIMLIĞI |
| SubscriptionId |Metin |Kaynağın abonelik tanımlayıcısı (örn. Verilerin toplandığı kurtarma hizmetleri Kasası) |
| ResourceGroup |Metin |Kaynağın kaynak grubu (örn. Verilerin toplandığı kurtarma hizmetleri Kasası) |
| ResourceProvider |Metin |Verilerin toplandığı kaynak sağlayıcısı. Örneğin, Microsoft. RecoveryServices |
| ResourceType |Metin |Verilerin toplandığı kaynak türü. Örneğin, kasa |
| StorageUniqueId_s |Metin |Depolama varlığını tanımlamak için kullanılan benzersiz KIMLIK |
| StorageType_s |Metin |Depolama türü, örneğin bulut, birim, disk |
| StorageName_s |Metin |Depolama varlığının adı, örneğin E:\ |
| StorageTotalSizeInGBs_s |Metin |Depolama varlığı tarafından tüketilen, GB cinsinden toplam depolama alanı boyutu|

### <a name="storageassociation"></a>StorageAssociation

Bu tablo, depolamayı diğer varlıklara bağlayan temel depolama ile ilgili alanları sağlar.

| Alan | Veri Türü | Açıklama |
| --- | --- |  --- |
| StorageUniqueId_s |Metin |Depolama varlığını tanımlamak için kullanılan benzersiz KIMLIK |
| SchemaVersion_s |Metin |Bu alan, şemanın geçerli sürümünü belirtir, **v2** 'dir |
| BackupItemUniqueId_s |Metin |Depolama varlığıyla ilgili yedekleme öğesini tanımlamak için kullanılan benzersiz KIMLIK |
| BackupManagementServerUniqueId_s |Metin |Depolama varlığıyla ilgili yedekleme yönetim sunucusunu tanımlamak için kullanılan benzersiz KIMLIK|
| VaultUniqueId_s |Metin |Depolama varlığıyla ilgili kasayı tanımlamak için kullanılan benzersiz KIMLIK|
| StorageConsumedInMBs_s |Sayı|Karşılık gelen depolama alanındaki karşılık gelen yedekleme öğesi tarafından tüketilen depolamanın boyutu |
| StorageAllocatedInMBs_s |Sayı |Disk türünde karşılık gelen yedekleme öğesi tarafından ayrılan depolama alanı boyutu|

### <a name="vault"></a>Kasa

Bu tablo, kasala ilgili alanlarla ilgili ayrıntıları sağlar.

| Alan | Veri Türü | Açıklama |
| --- | --- | --- |
| EventName_s |Metin |Bu alan, bu olayın adını temsil eder, her zaman AzureBackupCentralReport |
| SchemaVersion_s |Metin |Bu alan, şemanın geçerli sürümünü belirtir, **v2** 'dir |
| State_s |Metin |Kasa nesnesinin geçerli durumu, örneğin, etkin, silindi |
| ThrottledRequests |Metin |Bu alan, geçerli işlem kasasının adını temsil eder |
| Kategori |Metin |Bu alan, Azure Izleyici günlüklerine gönderilen tanılama verilerinin kategorisini temsil eder, AzureBackupReport |
| Kaynak |Metin |Bu, verilerin toplandığı kaynaktır, kurtarma hizmetleri kasasının adını gösterir |
| VaultUniqueId_s |Metin |Kasanın benzersiz KIMLIĞI |
| VaultName_s |Metin |Kasanın adı |
| AzureDataCenter_s |Metin |Kasanın bulunduğu veri merkezi |
| StorageReplicationType_s |Metin |Kasa için depolama çoğaltma türü (örneğin, Geoyedekli) |
| SourceSystem |Metin |Geçerli verilerin kaynak sistemi-Azure |
| ResourceId |Metin |Toplanmakta olan veriler için kaynak tanımlayıcısı. Örneğin, kurtarma hizmetleri Kasası kaynak KIMLIĞI |
| SubscriptionId |Metin |Kaynağın abonelik tanımlayıcısı (örn. Verilerin toplandığı kurtarma hizmetleri Kasası) |
| ResourceGroup |Metin |Kaynağın kaynak grubu (örn. Verilerin toplandığı kurtarma hizmetleri Kasası) |
| ResourceProvider |Metin |Verilerin toplandığı kaynak sağlayıcısı. Örneğin, Microsoft. RecoveryServices |
| ResourceType |Metin |Verilerin toplandığı kaynak türü. Örneğin, kasa |

### <a name="backup-management-server"></a>Yedekleme yönetim sunucusu

Bu tablo yedekleme yönetim sunucuları hakkında temel alanlar sağlar.

|Alan  |Veri Türü  | Açıklama  |
|---------|---------|----------|
|BackupManagementServerName_s     |Metin         |Yedekleme yönetim sunucusunun adı        |
|AzureBackupAgentVersion_s     |Metin         |Yedekleme yönetim sunucusundaki Azure Backup aracısının sürümü          |
|BackupManagementServerVersion_s     |Metin         |Yedekleme yönetim sunucusunun sürümü|
|BackupManagementServerOSVersion_s     |Metin            |Yedekleme yönetim sunucusunun işletim sistemi sürümü|
|BackupManagementServerType_s     |Metin         |Yedekleme yönetim sunucusunun türü, MABS, SC DPM olarak|
|BackupManagementServerUniqueId_s     |Metin         |Yedekleme yönetim sunucusunu benzersiz olarak tanımlamak için alan       |

### <a name="preferredworkloadonvolume"></a>PreferredWorkloadOnVolume

Bu tablo, bir birimin ilişkilendirildiği iş yüklerini belirtir.

| Alan | Veri Türü | Açıklama |
| --- | --- | --- |
| StorageUniqueId_s |Metin |Depolama varlığını tanımlamak için kullanılan benzersiz KIMLIK |
| BackupItemType_s |Metin |Bu birimin tercih edilen depolama alanı olduğu iş yükleri|

### <a name="protectedinstance"></a>Protectedınstance

Bu tablo, temel korumalı örneklerle ilgili alanları sağlar.

| Alan | Veri Türü |Uygulanabilir sürümler | Açıklama |
| --- | --- | --- | --- |
| BackupItemUniqueId_s |Metin |v2|DPM, MABS kullanılarak yedeklenen VM 'Ler için yedekleme öğesini tanımlamak üzere kullanılan benzersiz KIMLIK|
| ProtectedContainerUniqueId_s |Metin |v2|DPM, MABS kullanılarak yedeklenen VM 'Ler hariç her şey için korunan kapsayıcıyı tanımlamak üzere kullanılan benzersiz KIMLIK|
| ProtectedInstanceCount_s |Metin |v2|İlişkili yedekleme öğesi veya korunan kapsayıcı için bu tarih-saat için korunan örnek sayısı|

### <a name="recoverypoint"></a>RecoveryPoint

Bu tablo, temel kurtarma noktasıyla ilgili alanları sağlar.

| Alan | Veri Türü | Açıklama |
| --- | --- | --- |
| BackupItemUniqueId_s |Metin |DPM, MABS kullanılarak yedeklenen VM 'Ler için yedekleme öğesini tanımlamak üzere kullanılan benzersiz KIMLIK|
| OldestRecoveryPointTime_s |Metin |Yedekleme öğesi için en eski kurtarma noktasının tarih saati|
| OldestRecoveryPointLocation_s |Metin |Yedekleme öğesi için en eski kurtarma noktası konumu|
| LatestRecoveryPointTime_s |Metin |Yedekleme öğesi için en son kurtarma noktasının tarih saati|
| LatestRecoveryPointLocation_s |Metin |Yedekleme öğesi için en son kurtarma noktasının konumu|

## <a name="next-steps"></a>Sonraki adımlar

Veri modelini gözden geçirdikten sonra, kendi panonuzu oluşturmak için Azure Izleyici günlüklerinde [özel sorgular oluşturmaya](../azure-monitor/learn/tutorial-logs-dashboards.md) başlayabilirsiniz.
