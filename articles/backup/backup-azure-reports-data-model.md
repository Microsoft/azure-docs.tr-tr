---
title: Azure Backup tanılama olayları için veri modeli
description: Bu veri modeli, Log Analytics (LA) ' a tanılama olayları göndermenin kaynağa özgü moda başvurdadır.
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: 0d75af6d2b41aad0b5f821dd1f6409b30f7ca531
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "102499603"
---
# <a name="data-model-for-azure-backup-diagnostics-events"></a>Azure Backup tanılama olayları için veri modeli

> [!NOTE]
>
> Özel Raporlama görünümleri oluşturmak için, aşağıda listelenen ham tablolarla çalışmak yerine [Azure izleyici günlüklerinde sistem işlevlerinin](backup-reports-system-functions.md) kullanılması önerilir.

## <a name="coreazurebackup"></a>CoreAzureBackup

Bu tablo, kasa ve yedekleme öğeleri gibi temel yedekleme varlıkları hakkında bilgiler sağlar.

| **Alan**                         | **Veri türü** | **Açıklama**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| ResourceId                        | Metin          | Toplanmakta olan veriler için kaynak tanımlayıcısı. Örneğin, kurtarma hizmetleri Kasası kaynak KIMLIĞI. |
| OperationName                     | Metin          | Bu alan, geçerli işlem-Backupıtem, BackupItemAssociation veya ProtectedContainer adını temsil eder. |
| Kategori                          | Metin          | Bu alan, Azure Izleyici günlüklerine gönderilen tanılama verilerinin kategorisini temsil eder. Örneğin, CoreAzureBackup. |
| AgentVersion                      | Metin          | Aracı yedeklemesinin veya koruma aracısının sürüm numarası (SC DPM ve MABS durumunda) |
| AzureBackupAgentVersion           | Metin          | Yedekleme yönetim sunucusundaki Azure Backup aracısının sürümü |
| AzureDataCenter                   | Metin          | Kasanın bulunduğu veri merkezi                       |
| BackupItemAppVersion              | Metin          | Yedekleme öğesinin uygulama sürümü                       |
| Backupıtemfriendlyname            | Metin          | Yedekleme öğesinin kolay adı                             |
| Backupıtemname                    | Metin          | Yedekleme öğesinin adı                                      |
| Backupıtemprotectionstate         | Metin          | Yedekleme öğesinin koruma durumu                          |
| Backupıtemfrontendsize            | Metin          | Yedekleme öğesinin ön uç boyutu                            |
| Backupıtemtype                    | Metin          | Yedekleme öğesi türü. Örneğin: VM, dosya klasörü             |
| Backupıtemuniqueıd                | Metin          | Yedekleme öğesinin benzersiz tanımlayıcısı                         |
| BackupManagementServerType        | Metin          | Yedekleme yönetim sunucusunun türü, MABS 'de olduğu gibi, SC DPM     |
| Backupmanagementserveruniqueıd    | Metin          | Yedekleme yönetim sunucusunu benzersiz olarak tanımlamak için alan      |
| BackupManagementType              | Metin          | Yedekleme işi yapan sunucu için sağlayıcı türü. Örneğin, ıaasvm, FileFolder |
| BackupManagementServerName        | Metin          | Yedekleme yönetim sunucusunun adı                         |
| BackupManagementServerOSVersion   | Metin          | Yedekleme yönetim sunucusunun işletim sistemi sürümü                   |
| BackupManagementServerVersion     | Metin          | Yedekleme yönetim sunucusunun sürümü                      |
| LatestRecoveryPointLocation       | Metin          | Yedekleme öğesi için en son kurtarma noktasının konumu    |
| LatestRecoveryPointTime           | DateTime      | Yedekleme öğesi için en son kurtarma noktasının tarih saati   |
| OldestRecoveryPointLocation       | Metin          | Yedekleme öğesi için en eski kurtarma noktası konumu    |
| OldestRecoveryPointTime           | DateTime      | Yedekleme öğesi için en son kurtarma noktasının tarih saati   |
| Policyuniqueıd                    | Metin          | İlkeyi tanımlamak için benzersiz KIMLIK                             |
| ProtectedContainerFriendlyName    | Metin          | Korumalı sunucunun kolay adı                        |
| ProtectedContainerLocation        | Metin          | Korumalı kapsayıcının şirket içinde mi yoksa Azure 'da mı bulunduğu |
| ProtectedContainerName            | Metin          | Korumalı kapsayıcının adı                            |
| ProtectedContainerOSType          | Metin          | Korumalı kapsayıcının işletim sistemi türü                          |
| ProtectedContainerOSVersion       | Metin          | Korumalı kapsayıcının işletim sistemi sürümü                        |
| ProtectedContainerProtectionState | Metin          | Korumalı kapsayıcının koruma durumu                  |
| ProtectedContainerType            | Metin          | Korumalı kapsayıcının bir sunucu mı yoksa bir kapsayıcı mi olduğunu belirtir  |
| Protectedcontaineruniqueıd        | Metin          | DPM, MABS kullanılarak yedeklenen VM 'Ler hariç her şey için korunan kapsayıcıyı tanımlamak üzere kullanılan benzersiz KIMLIK |
| ProtectedContainerWorkloadType    | Metin          | Yedeklenen korumalı kapsayıcının türü. Örneğin, ıaasvmcontainer |
| ProtectionGroupName               | Metin          | Yedekleme öğesinin korunan koruma grubunun adı, SC DPM ve varsa MABS için |
| ResourceGroupName                 | Metin          | Toplanmakta olan veriler için kaynağın kaynak grubu (örneğin, kurtarma hizmetleri Kasası) |
| SchemaVersion                     | Metin          | Bu alan, şemanın geçerli sürümünü gösterir. **V2** 'dir |
| SecondaryBackupProtectionState    | Metin          | Yedekleme öğesi için ikincil korumanın etkinleştirilip etkinleştirilmediği  |
| Durum                             | Metin          | Yedekleme öğesi nesnesinin durumu. Örneğin, etkin, silindi |
| StorageReplicationType            | Metin          | Kasa için depolama çoğaltma türü. Örneğin, Geoyedekli |
| SubscriptionId                    | Metin          | Verilerin toplandığı kaynağın abonelik tanımlayıcısı (örneğin, kurtarma hizmetleri Kasası) |
| VaultName                         | Metin          | Kasanın adı                                            |
| VaultTags                         | Metin          | Kasa kaynağıyla ilişkili Etiketler                    |
| Vaultuniqueıd                     | Metin          | Kasanın benzersiz tanımlayıcısı                             |
| SourceSystem                      | Metin          | Geçerli verilerin kaynak sistemi-Azure                  |

## <a name="addonazurebackupalerts"></a>AddonAzureBackupAlerts

Bu tablo, uyarı ile ilgili alanlarla ilgili ayrıntıları sağlar.

| **Alan**                      | **Veri türü** | **Açıklama**                                              |
| :----------------------------- | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Metin          | Verilerin toplandığı kaynak için benzersiz tanımlayıcı. Örneğin, bir kurtarma hizmetleri Kasası kaynak KIMLIĞI |
| OperationName                  | Metin          | Geçerli işlemin adı. Örneğin, uyarı            |
| Kategori                       | Metin          | Azure Izleyici günlüklerine gönderilen tanılama verilerinin kategorisi-AddonAzureBackupAlerts |
| AlertCode                      | Metin          | Uyarı türünü benzersiz şekilde tanımlamak için kod                     |
| AlertConsolidationStatus       | Metin          | Uyarının birleştirilmiş bir uyarı olup olmadığını tanımla         |
| AlertOccurrenceDateTime        | DateTime      | Uyarının oluşturulduğu tarih ve saat                     |
| AlertRaisedOn                  | Metin          | Uyarının üzerinde ortaya çıkarılan varlık türü                        |
| AlertSeverity                  | Metin          | Uyarının önem derecesi. Örneğin, kritik                 |
| AlertStatus                    | Metin          | Uyarının durumu. Örneğin, etkin                     |
| Alerttimetoresolveınminutes    | Sayı        | Bir uyarının çözülmesi için geçen süre. Etkin uyarılar için boş.     |
| AlertType                      | Metin          | Uyarı türü. Örneğin, yedekleme                           |
| Alertuniqueıd                  | Metin          | Oluşturulan uyarının benzersiz tanıtıcısı                    |
| Backupıtemuniqueıd             | Metin          | Uyarıyla ilişkili yedekleme öğesinin benzersiz tanımlayıcısı |
| Backupmanagementserveruniqueıd | Metin          | Yedekleme yönetim sunucusunu benzersiz şekilde tanımlamak için alan, varsa yedekleme öğesi tarafından korunur |
| BackupManagementType           | Metin          | Yedekleme işi yapan sunucu için sağlayıcı türü, örneğin ıaasvm, FileFolder |
| CountOfAlertsConsolidated      | Sayı        | Birleştirilmiş bir uyarıda birleştirilmiş uyarı sayısı  |
| Protectedcontaineruniqueıd     | Metin          | Uyarıyla ilişkili korumalı sunucunun benzersiz tanıtıcısı |
| RecommendedAction              | Metin          | Uyarıyı çözümlemek için önerilen eylem                      |
| SchemaVersion                  | Metin          | Şemanın geçerli sürümü, örneğin **v2**            |
| Durum                          | Metin          | Uyarı nesnesinin geçerli durumu, örneğin, etkin, silindi |
| Storageuniqueıd                | Metin          | Depolama varlığını tanımlamak için kullanılan benzersiz KIMLIK                |
| Vaultuniqueıd                  | Metin          | Uyarıyla ilgili kasayı tanımlamak için kullanılan benzersiz KIMLIK    |
| SourceSystem                   | Metin          | Geçerli verilerin kaynak sistemi-Azure                    |

## <a name="addonazurebackupprotectedinstance"></a>AddonAzureBackupProtectedInstance

Bu tablo, temel korumalı örneklerle ilgili alanları sağlar.

| **Alan**                      | **Veri türü** | **Açıklama**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Metin          | Verilerin toplandığı kaynak için benzersiz tanımlayıcı. Örneğin, bir kurtarma hizmetleri Kasası kaynak KIMLIĞI |
| OperationName                  | Metin          | İşlemin adı, örneğin Protectedınstance         |
| Kategori                       | Metin          | Azure Izleyici günlüklerine gönderilen tanılama verilerinin kategorisi-AddonAzureBackupProtectedInstance |
| Backupıtemuniqueıd             | Metin          | Yedekleme öğesinin benzersiz KIMLIĞI                                 |
| Backupmanagementserveruniqueıd | Metin          | Yedekleme yönetim sunucusunu benzersiz şekilde tanımlamak için alan, varsa yedekleme öğesi tarafından korunur |
| BackupManagementType           | Metin          | Yedekleme işi yapan sunucu için sağlayıcı türü, örneğin ıaasvm, FileFolder |
| Protectedcontaineruniqueıd     | Metin          | İşin çalıştırıldığı korunan kapsayıcıyı belirlemek için benzersiz KIMLIK |
| Protectedınstancecount         | Metin          | İlişkili yedekleme öğesi veya korunan kapsayıcı için bu tarih-saat için korunan örnek sayısı |
| SchemaVersion                  | Metin          | Şemanın geçerli sürümü, örneğin **v2**            |
| Durum                          | Metin          | Yedekleme öğesi nesnesinin durumu, örneğin, etkin, silindi |
| Vaultuniqueıd                  | Metin          | Korunan örnekle ilişkili korumalı kasasının benzersiz tanıtıcısı |
| SourceSystem                   | Metin          | Geçerli verilerin kaynak sistemi-Azure                    |

## <a name="addonazurebackupjobs"></a>AddonAzureBackupJobs

Bu tablo, işle ilgili alanlarla ilgili ayrıntıları sağlar.

| **Alan**                      | **Veri türü** | **Açıklama**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Metin          | Toplanmakta olan veriler için kaynak tanımlayıcısı. Örneğin, kurtarma hizmetleri Kasası kaynak KIMLIĞI |
| OperationName                  | Metin          | Bu alan geçerli işlemin adını temsil eder-Iş    |
| Kategori                       | Metin          | Bu alan, Azure Izleyici günlüklerine gönderilen tanılama verilerinin kategorisini temsil eder-AddonAzureBackupJobs |
| AdhocOrScheduledJob            | Metin          | İşin geçici veya zamanlanmış olduğunu belirten alan           |
| Backupıtemuniqueıd             | Metin          | Depolama varlığıyla ilgili yedekleme öğesini tanımlamak için kullanılan benzersiz KIMLIK |
| Backupmanagementserveruniqueıd | Metin          | Depolama varlığıyla ilgili yedekleme yönetim sunucusunu tanımlamak için kullanılan benzersiz KIMLIK |
| BackupManagementType           | Metin          | Yedekleme gerçekleştirmek için sağlayıcı türü, örneğin, bu işin ait olduğu ıaasvm, FileFolder |
| DataTransferredInMB            | Sayı        | Bu iş için MB olarak aktarılan veriler                          |
| JobDurationInSecs              | Sayı        | Saniye cinsinden toplam iş süresi                                |
| JobFailureCode                 | Metin          | İş hatası oluştuğundan hata kodu dizesi    |
| JobOperation                   | Metin          | İşin çalıştırıldığı işlem Örneğin, yedekleme, geri yükleme, yedeklemeyi yapılandırma |
| JobOperationSubType            | Metin          | Iş Işleminin alt türü. Örneğin, günlük yedekleme Işi durumunda ' log ' |
| JobStartDateTime               | DateTime      | İşin çalışmaya başladığı tarih ve saat                       |
| JobStatus                      | Metin          | Tamamlanmış işin durumu, örneğin, tamamlandı, başarısız   |
| Jobuniqueıd                    | Metin          | İşi tanımlamak için benzersiz KIMLIK                                |
| Protectedcontaineruniqueıd     | Metin          | İşle ilişkili korumalı sunucunun benzersiz tanıtıcısı |
| RecoveryJobDestination         | Metin          | Verilerin kurtarıldığı bir kurtarma işinin hedefi   |
| RecoveryJobRPDateTime          | DateTime      | Kurtarılan kurtarma noktasının oluşturulduğu tarih, saat |
| RecoveryJobLocation            | Metin          | Kurtarılan kurtarma noktasının depolandığı konum |
| RecoveryLocationType           | Metin          | Kurtarma konumunun türü                                |
| SchemaVersion                  | Metin          | Şemanın geçerli sürümü, örneğin **v2**            |
| Durum                          | Metin          | İş nesnesinin geçerli durumu, örneğin, etkin, silindi |
| Vaultuniqueıd                  | Metin          | İşle ilişkili korumalı kasasının benzersiz tanıtıcısı |
| SourceSystem                   | Metin          | Geçerli verilerin kaynak sistemi-Azure                    |

## <a name="addonazurebackuppolicy"></a>AddonAzureBackupPolicy

Bu tablo ilkeyle ilgili alanlarla ilgili ayrıntıları sağlar.

| **Alan**                       | **Veri türü**  | **Açıklama**                                              |
| ------------------------------- | -------------- | ------------------------------------------------------------ |
| ResourceId                      | Metin           | Verilerin toplandığı kaynak için benzersiz tanımlayıcı. Örneğin, bir kurtarma hizmetleri Kasası kaynak KIMLIĞI |
| OperationName                   | Metin           | İşlemin adı, örneğin, Ilke veya Poliyassociation |
| Kategori                        | Metin           | Azure Izleyici günlüklerine gönderilen tanılama verilerinin kategorisi-AddonAzureBackupPolicy |
| Backupdayısoftheweek             | Metin           | Yedeklemelerin zamanlandığı haftanın günleri            |
| BackupFrequency                 | Metin           | Yedeklemelerin çalıştırıldığı sıklık. Örneğin, günlük, haftalık |
| BackupManagementType            | Metin           | Yedekleme işi yapan sunucu için sağlayıcı türü. Örneğin, ıaasvm, FileFolder |
| Backupmanagementserveruniqueıd  | Metin           | Yedekleme yönetim sunucusunu benzersiz şekilde tanımlamak için alan, varsa yedekleme öğesi tarafından korunur |
| BackupTimes                     | Metin           | Yedeklemelerin zamanlandığı tarih ve saat                     |
| DailyRetentionDuration          | Tam Sayı   | Yapılandırılan yedeklemeler için gün cinsinden toplam saklama süresi      |
| DailyRetentionTimes             | Metin           | Günlük bekletmenin yapılandırıldığı tarih ve saat            |
| Diffbackupdayısoftheweek         | Metin           | Azure VM yedeklemesi 'nde SQL için değişiklik yedeklemeleri için haftanın günleri |
| DiffBackupFormat                | Metin           | Azure VM yedeklemesi 'nde SQL için değişiklik yedeklemelerinin biçimi   |
| DiffBackupRetentionDuration     | Ondalık Sayı | Azure VM yedeklemesi 'nde SQL için değişiklik yedeklemeleri bekletme süresi |
| DiffBackupTime                  | Saat           | Azure VM yedeklemesi 'nde SQL için değişiklik yedeklemeleri süresi     |
| LogBackupFrequency              | Ondalık Sayı | SQL için günlük yedeklemeleri sıklığı                            |
| LogBackupRetentionDuration      | Ondalık Sayı | Azure VM yedeklemesi 'nde SQL için günlük yedeklemeleri bekletme süresi |
| MonthlyRetentionDaysOfTheMonth  | Metin           | Aylık bekletme yapılandırıldığında ayın haftası.  Örneğin, Ilk, son |
| MonthlyRetentionDaysOfTheWeek   | Metin           | Aylık saklama için seçilen haftanın günleri              |
| MonthlyRetentionDuration        | Metin           | Yapılandırılan yedeklemeler için aylık toplam bekletme süresi    |
| MonthlyRetentionFormat          | Metin           | Aylık saklama için yapılandırma türü. Örneğin günlük tabanlı, haftalık için haftalık |
| MonthlyRetentionTimes           | Metin           | Aylık bekletmenin yapılandırıldığı tarih ve saat           |
| MonthlyRetentionWeeksOfTheMonth | Metin           | Aylık bekletme yapılandırıldığında ayın haftası.   Örneğin, Ilk, son |
| PolicyName                      | Metin           | Tanımlanan ilkenin adı                                   |
| Policyuniqueıd                  | Metin           | İlkeyi tanımlamak için benzersiz KIMLIK                             |
| PolicyTimeZone                  | Metin           | Günlüklerde Ilke saat alanlarının belirtildiği saat dilimi |
| Retentionduration 'a               | Metin           | Yapılandırılan yedeklemeler için bekletme süresi                    |
| RetentionType                   | Metin           | Bekletme türü                                            |
| SchemaVersion                   | Metin           | Bu alan, şemanın geçerli sürümünü belirtir, **v2** 'dir |
| Durum                           | Metin           | İlke nesnesinin geçerli durumu. Örneğin, etkin, silindi |
| SynchronisationFrequencyPerDay  | Tam Sayı   | SC DPM ve MABS için bir dosya yedeklemesinin eşitlendiği gün sayısı |
| Vaultuniqueıd                   | Metin           | Bu ilkenin ait olduğu kasanın benzersiz KIMLIĞI          |
| WeeklyRetentionDaysOfTheWeek    | Metin           | Haftalık saklama için seçilen haftanın günleri               |
| WeeklyRetentionDuration         | Ondalık Sayı | Yapılandırılan yedeklemeler için hafta cinsinden toplam haftalık saklama süresi |
| WeeklyRetentionTimes            | Metin           | Haftalık bekletmenin yapılandırıldığı tarih ve saat            |
| YearlyRetentionDaysOfTheMonth   | Metin           | Yıllık bekletme için seçilen ayın tarihleri             |
| YearlyRetentionDaysOfTheWeek    | Metin           | Yıllık bekletme için seçilen haftanın günleri               |
| YearlyRetentionDuration         | Ondalık Sayı | Yapılandırılan yedeklemeler için yıl cinsinden toplam saklama süresi     |
| YearlyRetentionFormat           | Metin           | Yıllık saklama için yapılandırma türü, örneğin, günlük tabanlı için günlük, haftalık tabanlı için haftalık |
| YearlyRetentionMonthsOfTheYear  | Metin           | Yıllık bekletme için seçilen yılın ayı             |
| YearlyRetentionTimes            | Metin           | Yıllık bekletmenin yapılandırıldığı tarih ve saat            |
| YearlyRetentionWeeksOfTheMonth  | Metin           | Yıllık bekletme için seçilen ayın haftası             |
| SourceSystem                    | Metin           | Geçerli verilerin kaynak sistemi-Azure                    |

## <a name="addonazurebackupstorage"></a>AddonAzureBackupStorage

Bu tablo, depolama ile ilgili alanlarla ilgili ayrıntıları sağlar.

| **Alan**                      | **Veri türü** | **Açıklama**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Metin          | Toplanmakta olan veriler için kaynak tanımlayıcısı. Örneğin, kurtarma hizmetleri Kasası kaynak KIMLIĞI |
| OperationName                  | Metin          | Bu alan geçerli işlemin adını temsil eder-depolama alanı veya StorageAssociation |
| Kategori                       | Metin          | Bu alan, Azure Izleyici günlüklerine gönderilen tanılama verilerinin kategorisini temsil eder-AddonAzureBackupStorage |
| Backupıtemuniqueıd             | Metin          | DPM, MABS kullanılarak yedeklenen VM 'Ler için yedekleme öğesini tanımlamak üzere kullanılan benzersiz KIMLIK |
| Backupmanagementserveruniqueıd | Metin          | Yedekleme yönetim sunucusunu benzersiz şekilde tanımlamak için alan, varsa yedekleme öğesi tarafından korunur |
| BackupManagementType           | Metin          | Yedekleme işi yapan sunucu için sağlayıcı türü. Örneğin, ıaasvm, FileFolder |
| PreferredWorkloadOnVolume      | Metin          | Bu birimin tercih edilen depolama alanı olduğu iş yükü      |
| Protectedcontaineruniqueıd     | Metin          | Yedekleme öğesiyle ilişkili korumalı kapsayıcının benzersiz tanıtıcısı |
| SchemaVersion                  | Metin          | Şema sürümü. Örneğin, **v2**                   |
| Durum                          | Metin          | Yedekleme öğesi nesnesinin durumu. Örneğin, etkin, silindi |
| Storageallocatedinmb          | Sayı        | Disk türünde karşılık gelen yedekleme öğesi tarafından ayrılan depolama alanı boyutu |
| Storagetüketimedinmb           | Sayı        | Karşılık gelen depolama alanındaki karşılık gelen yedekleme öğesi tarafından tüketilen depolamanın boyutu |
| StorageName                    | Metin          | Depolama varlığının adı. Örneğin, E:\                      |
| StorageTotalSizeInGBs          | Metin          | Depolama varlığı tarafından tüketilen, GB cinsinden toplam depolama alanı boyutu     |
| StorageType                    | Metin          | Depolama türü, örneğin bulut, birim, disk             |
| Storageuniqueıd                | Metin          | Depolama varlığını tanımlamak için kullanılan benzersiz KIMLIK                |
| Vaultuniqueıd                  | Metin          | Depolama varlığıyla ilgili kasayı tanımlamak için kullanılan benzersiz KIMLIK |
| Birimfriendlyname             | Metin          | Depolama biriminin kolay adı                          |
| SourceSystem                   | Metin          | Geçerli verilerin kaynak sistemi-Azure                    |

## <a name="valid-operation-names-for-each-table"></a>Her tablo için geçerli Işlem adları

Yukarıdaki tablolardaki her kaydın ilişkili bir **Işlem adı** vardır. Işlem adı kayıt türünü açıklar (ve ayrıca tablodaki hangi alanların bu kayıt için doldurulduğunu gösterir). Her tablo (kategori) bir veya daha fazla farklı Işlem adını destekler. Yukarıdaki tabloların her biri için desteklenen Işlem adlarının özeti aşağıda verilmiştir.

| **Tablo adı/kategori**                   | **Desteklenen Işlem adları** | **Açıklama**              |
| ------------------------------------------- | ------------------------------|----------------------------- |
| CoreAzureBackup | Backupıtem | Verilen bir yedekleme öğesinin tüm ayrıntılarını içeren, örneğin ID, Name, Type, vb. bir kaydı temsil eder. |
| CoreAzureBackup | BackupItemAssociation | Bir yedekleme öğesi ile ilişkili korunan kapsayıcısı (varsa) arasındaki eşlemeyi temsil eder. |
| CoreAzureBackup | Backupıtemfrontendsizetüketim | Bir yedekleme öğesiyle ön uç boyutu arasındaki eşlemeyi temsil eder. |
| CoreAzureBackup | ProtectedContainer | Belirtilen korumalı kapsayıcının KIMLIK, ad, tür vb. tüm ayrıntılarını içeren bir kaydı temsil eder. |
| CoreAzureBackup | ProtectedContainerAssociation | Korunan bir kapsayıcı ile yedeklemesi için kullanılan kasa arasındaki eşlemeyi temsil eder. |
| CoreAzureBackup | Kasa | Belirli bir kasanın tüm ayrıntılarını içeren bir kaydı temsil eder. KIMLIK, ad, Etiketler, konum vb. |
| CoreAzureBackup | RecoveryPoint | Belirli bir yedekleme öğesi için en eski ve en son kurtarma noktasını içeren bir kaydı temsil eder. |
| AddonAzureBackupJobs | İş |  Belirli bir işin tüm ayrıntılarını içeren bir kaydı temsil eder. Örneğin, iş işlemi, başlangıç zamanı, durum vb. |
| AddonAzureBackupAlerts | Uyarı | Belirli bir uyarının tüm ayrıntılarını içeren bir kaydı temsil eder. Örneğin, uyarı oluşturma süresi, önem derecesi, durum vb.  |
| AddonAzureBackupStorage | Depolama | Belirli bir depolama varlığının tüm ayrıntılarını içeren bir kaydı temsil eder. Örneğin, depolama adı, tür vb. |
| AddonAzureBackupStorage | StorageAssociation | Yedekleme öğesi tarafından tüketilen toplam bulut depolaması arasındaki bir eşlemeyi temsil eder. |
| AddonAzureBackupProtectedInstance | Protectedınstance | Her bir kapsayıcı veya yedekleme öğesi için korunan örnek sayısını içeren bir kaydı temsil eder. Azure VM yedeklemesi için, korumalı örnek sayısı yedekleme öğesi düzeyinde kullanılabilir ve diğer iş yükleri için korunan kapsayıcı düzeyinde kullanılabilir. |
| AddonAzureBackupPolicy | İlke |  Bir yedekleme ve bekletme ilkesinin tüm ayrıntılarını içeren bir kaydı temsil eder. Örneğin, KIMLIK, ad, bekletme ayarları, vb. |
| AddonAzureBackupPolicy | Poliyassociation | Bir yedekleme öğesi ile buna uygulanan yedekleme ilkesi arasındaki eşlemeyi temsil eder. |   

Genellikle, analiz için gerekli tüm alanları almak için farklı tablolar ve aynı tablonun parçası olan (Işlem adına göre farklılaştırılan) farklı kayıt kümeleri arasında birleştirmeler gerçekleştirmeniz gerekecektir. Başlamak için [örnek sorgulara](./backup-azure-monitoring-use-azuremonitor.md#sample-kusto-queries) bakın. 

## <a name="next-steps"></a>Sonraki adımlar

- [Log Analytics Tanılama verileri gönderme hakkında bilgi edinin](./backup-azure-diagnostic-events.md)
- [Kaynağa özgü tablolara sorgu yazmayı öğrenin](./backup-azure-monitoring-use-azuremonitor.md#sample-kusto-queries)