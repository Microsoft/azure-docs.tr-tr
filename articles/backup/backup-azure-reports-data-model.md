---
title: Power BI veri modeli
description: Bu makalede, Azure Backup raporlarının Power BI veri modeli ayrıntıları ele bir şekilde konuşuyor.
ms.topic: conceptual
ms.date: 06/26/2017
ms.openlocfilehash: a2f06da16280070448d7b42dc5e1dcfc46354cfa
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172796"
---
# <a name="data-model-for-azure-backup-reports"></a>Azure Backup raporları için veri modeli

Bu makalede Azure Backup rapor oluşturmak için kullanılan Power BI veri modeli açıklanmaktadır. Bu veri modelini kullanarak, mevcut raporları ilgili alanlara göre filtreleyebilir ve daha da önemlisi, modeldeki tabloları ve alanları kullanarak kendi raporlarınızı oluşturabilirsiniz.

## <a name="creating-new-reports-in-power-bi"></a>Power BI yeni raporlar oluşturma

Power BI [veri modelini kullanarak rapor oluşturabileceğiniz](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/)özelleştirme özellikleri sağlar.

## <a name="using-azure-backup-data-model"></a>Azure Backup veri modelini kullanma

Rapor oluşturmak ve mevcut raporları özelleştirmek için veri modelinin bir parçası olarak sunulan aşağıdaki alanları kullanabilirsiniz.

### <a name="alert"></a>Uyarı

Bu tablo, uyarı ile ilgili çeşitli alanlarla ilgili temel alanları ve toplamaları sağlar.

| Alan | Veri Türü | Açıklama |
| --- | --- | --- |
| #AlertsCreatedInPeriod |Tam sayı |Seçilen dönemde oluşturulan uyarı sayısı |
| % Activealertscreatedınperiod |Yüzde |Seçilen dönemdeki etkin uyarıların yüzdesi |
| % Criticalalertscreatedınperiod |Yüzde |Seçilen dönemdeki kritik uyarıların yüzdesi |
| AlertOccurrenceDate |Tarih |Uyarının oluşturulduğu tarih |
| AlertSeverity |Metin |Uyarının önem derecesi. Örneğin, kritik |
| AlertStatus |Metin |Uyarının durumu. Örneğin, etkin |
| AlertType |Metin |Oluşturulan uyarının türü. Örneğin, yedekleme |
| Alertuniqueıd |Metin |Oluşturulan uyarının benzersiz KIMLIĞI |
| AsOnDateTime |Tarih/saat |Seçili satır için en son yenileme zamanı |
| Avgresolutiontimeınminsforalertscreatedınperiod |Ondalık sayı |Seçilen zaman aralığı için uyarıyı çözümlemek için geçen ortalama süre (dakika cinsinden) |
| EntityState |Metin |Uyarı nesnesinin geçerli durumu. Örneğin, etkin, silindi |

### <a name="backup-item"></a>Yedekleme öğesi

Bu tablo, çeşitli yedekleme öğesiyle ilgili alanlarla ilgili temel alanları ve toplamaları sağlar.

| Alan | Veri Türü | Açıklama |
| --- | --- | --- |
| #BackupItems |Tam sayı |Yedekleme öğesi sayısı |
| #UnprotectedBackupItems |Tam sayı |Koruma için durdurulan veya yedeklemeler için yapılandırılan yedekleme öğelerinin sayısı ancak yedeklemeler başlatılmadı|
| AsOnDateTime |Tarih/saat |Seçili satır için en son yenileme zamanı |
| Backupıtemfriendlyname |Metin |Yedekleme öğesinin kolay adı |
| Backupıtemıd |Metin |Yedekleme öğesi KIMLIĞI |
| Backupıtemname |Metin |Yedekleme öğesinin adı |
| Backupıtemtype |Metin |Yedekleme öğesi türü. Örneğin, VM, dosya klasörü |
| EntityState |Metin |Yedekleme öğesi nesnesinin geçerli durumu. Örneğin, etkin, silindi |
| LastBackupDateTime |Tarih/saat |Seçili yedekleme öğesi için son yedekleme zamanı |
| LastBackupState |Metin |Seçili yedekleme öğesi için son yedeklemenin durumu. Örneğin, başarılı, başarısız |
| Lastbaşarılı Fulbackupdatetime |Tarih/saat |Seçili yedekleme öğesi için son başarılı yedeklemenin saati |
| ProtectionState |Metin |Yedekleme öğesinin geçerli koruma durumu. Örneğin, korumalı, Protectiondurdurulmuş |

### <a name="calendar"></a>Takvim

Bu tablo, takvimle ilgili alanlarla ilgili ayrıntıları sağlar.

| Alan | Veri Türü | Açıklama |
| --- | --- | --- |
| Tarih |Tarih |Verilerin filtrelenmesi için seçilen tarih |
| DateKey |Metin |Her bir tarih öğesi için benzersiz anahtar |
| DayDiff |Ondalık sayı |Verilerin filtrelenmesi için gün farkı. Örneğin, 0 geçerli günün verilerini gösterir,-1 önceki bir günün verilerini, 0 ve-1 geçerli ve önceki günün verilerini gösterir  |
| Ay |Metin |Verilerin filtrelenmesi için seçilen yılın ayı, ayın ilk gününde başlaması ve 31. gün tarihinde sona erecek |
| MonthDate | Tarih |Ayın bitiş tarihi, verilerin filtrelenmesi için seçildi |
| MonthDiff |Ondalık sayı |Verilerin filtrelenmesi için aydaki fark. Örneğin, 0 geçerli aya ait verileri gösterir,-1 önceki aya ait verileri gösterir, 0 ve-1 geçerli ve önceki aya ait verileri gösterir |
| Hafta |Metin |Verilerin filtrelenmesi için seçilen hafta, hafta Pazar günü başlar ve Cumartesi tarihinde sona erer |
| WeekDate |Tarih |Haftalık bitiş tarihi, verilerin filtrelenmesi için seçildi |
| WeekDiff |Ondalık sayı |Verilerin filtrelenmesi için haftalık fark. Örneğin, 0 geçerli haftanın verilerini gösterir,-1 önceki haftaki verileri gösterir, 0 ve-1 geçerli ve önceki hafta verilerini gösterir |
| Yıl |Metin |Verilerin filtrelenmesi için takvim yılı seçildi |
| Yıltarihi |Tarih |Yılın bitiş tarihinden sonra, verileri filtrelemek için seçilen yıl |

### <a name="job"></a>İş

Bu tablo, işle ilgili çeşitli alanlar üzerinde temel alanları ve toplamaları sağlar.

| Alan | Veri Türü | Açıklama |
| --- | --- | --- |
| #JobsCreatedInPeriod |Tam sayı |Seçilen dönemde oluşturulan iş sayısı |
| % Failuıresforjobscreatedınperiod |Yüzde |Seçilen dönemdeki toplam iş hatalarının yüzdesi |
| 80thPercentileDataTransferredInMBForBackupJobsCreatedInPeriod |Ondalık sayı |Seçilen dönemde oluşturulan **yedekleme** ışlerı için MB cinsinden aktarılan verilerin 500. yüzdebirlik değeri |
| AsOnDateTime |Tarih/saat |Seçili satır için en son yenileme zamanı |
| AvgBackupDurationInMinsForJobsCreatedInPeriod |Ondalık sayı |Seçilen dönemde oluşturulan **Tamamlanan yedekleme** işlerinin ortalama süresi (dakika) |
| AvgRestoreDurationInMinsForJobsCreatedInPeriod |Ondalık sayı |Seçilen dönemde oluşturulan **Tamamlanan geri yükleme** işlerinin ortalama süresi (dakika) |
| BackupStorageDestination |Metin |Yedekleme depolamanın hedefi. Örneğin, bulut, disk  |
| EntityState |Metin |İş nesnesinin geçerli durumu. Örneğin, etkin, silindi |
| JobFailureCode |Metin |İş hatası oluştuğundan hata kodu dizesi |
| JobOperation |Metin |İşin çalıştırıldığı işlem. Örneğin, yedekleme, geri yükleme, yedeklemeyi yapılandırma |
| JobStartDate |Tarih |İşin çalışmaya başladığı tarih |
| JobStartTime |Zaman |İşin çalışmaya başladığı zaman |
| JobStatus |Metin |Tamamlanan işin durumu. Örneğin, tamamlandı, başarısız |
| Jobuniqueıd |Metin |İşi tanımlamak için benzersiz KIMLIK |

### <a name="policy"></a>İlke

Bu tablo, ilkeyle ilgili çeşitli alanlarla ilgili temel alanları ve toplamaları sağlar.

| Alan | Veri Türü | Açıklama |
| --- | --- | --- |
| #Policies |Tam sayı |Sistemde var olan yedekleme ilkelerinin sayısı |
| #PoliciesInUse |Tam sayı |Yedeklemeleri yapılandırmak için şu anda kullanılan ilke sayısı |
| AsOnDateTime |Tarih/saat |Seçili satır için en son yenileme zamanı |
| Backupdayısoftheweek |Metin |Yedeklemelerin zamanlandığı haftanın günleri |
| BackupFrequency |Metin |Yedeklemelerin çalıştırıldığı sıklık. Örneğin, günlük, haftalık |
| BackupTimes |Metin |Yedeklemelerin zamanlandığı tarih ve saat |
| DailyRetentionDuration |Tam sayı |Yapılandırılan yedeklemeler için gün cinsinden toplam saklama süresi |
| DailyRetentionTimes |Metin |Günlük bekletmenin yapılandırıldığı tarih ve saat |
| EntityState |Metin |İlke nesnesinin geçerli durumu. Örneğin, etkin, silindi |
| MonthlyRetentionDaysOfTheMonth |Metin |Aylık saklama için seçilen ayın tarihleri |
| MonthlyRetentionDaysOfTheWeek |Metin |Aylık saklama için seçilen haftanın günleri |
| MonthlyRetentionDuration |Ondalık sayı |Yapılandırılan yedeklemeler için aylık toplam bekletme süresi |
| MonthlyRetentionFormat |Metin |Aylık saklama için yapılandırma türü. Örneğin günlük tabanlı, haftalık için haftalık |
| MonthlyRetentionTimes |Metin |Aylık bekletmenin yapılandırıldığı tarih ve saat |
| MonthlyRetentionWeeksOfTheMonth |Metin |Aylık bekletme yapılandırıldığında ayın haftası. Örneğin, Ilk, son vb. |
| PolicyName |Metin |Tanımlanan ilkenin adı |
| Policyuniqueıd |Metin |İlkeyi tanımlamak için benzersiz KIMLIK |
| RetentionType |Metin |Bekletme ilkesi türü. Örneğin, günlük, haftalık, aylık, yıllık |
| WeeklyRetentionDaysOfTheWeek |Metin |Haftalık saklama için seçilen haftanın günleri |
| WeeklyRetentionDuration |Ondalık sayı |Yapılandırılan yedeklemeler için hafta cinsinden toplam haftalık saklama süresi |
| WeeklyRetentionTimes |Metin |Haftalık bekletmenin yapılandırıldığı tarih ve saat |
| YearlyRetentionDaysOfTheMonth |Metin |Yıllık bekletme için seçilen ayın tarihleri |
| YearlyRetentionDaysOfTheWeek |Metin |Yıllık bekletme için seçilen haftanın günleri |
| YearlyRetentionDuration |Ondalık sayı |Yapılandırılan yedeklemeler için yıl cinsinden toplam saklama süresi |
| YearlyRetentionFormat |Metin |Yıllık saklama için yapılandırma türü. Örneğin günlük tabanlı, haftalık için haftalık |
| YearlyRetentionMonthsOfTheYear |Metin |Yıllık bekletme için seçilen yılın ayı |
| YearlyRetentionTimes |Metin |Yıllık bekletmenin yapılandırıldığı tarih ve saat |
| YearlyRetentionWeeksOfTheMonth |Metin |Yıllık bekletme yapılandırıldığında ayın haftası. Örneğin, Ilk, son vb. |

### <a name="protected-server"></a>Korumalı sunucu

Bu tablo, korumalı sunucu ile ilgili çeşitli alanlar üzerinde temel alanları ve toplamaları sağlar.

| Alan | Veri Türü | Açıklama |
| --- | --- | --- |
| #ProtectedServers |Tam sayı |Korumalı sunucu sayısı |
| AsOnDateTime |Tarih/saat |Seçili satır için en son yenileme zamanı |
| AzureBackupAgentOSType |Metin |Azure Backup aracısının işletim sistemi türü |
| AzureBackupAgentOSVersion |Metin |Azure Backup aracısının işletim sistemi sürümü |
| AzureBackupAgentUpdateDate |Metin |Aracı Yedekleme aracısının güncelleştirildiği tarih |
| AzureBackupAgentVersion |Metin |Aracı yedekleme sürümünün sürüm numarası |
| BackupManagementType |Metin |Yedekleme gerçekleştirmek için sağlayıcı türü. Örneğin, ıaasvm, FileFolder |
| EntityState |Metin |Korumalı sunucu nesnesinin geçerli durumu. Örneğin, etkin, silindi |
| ProtectedServerFriendlyName |Metin |Korumalı sunucunun kolay adı |
| ProtectedServerName |Metin |Korumalı sunucunun adı |
| ProtectedServerType |Metin |Yedeklenen korumalı sunucu türü. Örneğin, ıaasvmcontainer |
| ProtectedServerName |Metin |Yedekleme öğesinin ait olduğu korumalı sunucunun adı |
| RegisteredContainerId |Metin |Yedekleme için kaydedilen kapsayıcının KIMLIĞI |

### <a name="storage"></a>Depolama

Bu tablo, depolama ile ilgili çeşitli alanlar üzerinde temel alanları ve toplamaları sağlar.

| Alan | Veri Türü | Açıklama |
| --- | --- | --- |
| #ProtectedInstances |Ondalık sayı |Seçili zamanda en son değere göre hesaplanan, faturalandırma aşamasında ön uç depolamayı hesaplamak için kullanılan korumalı örnek sayısı |
| AsOnDateTime |Tarih/saat |Seçili satır için en son yenileme zamanı |
| Cloudstorageınmb |Ondalık sayı |Seçilen zamanda en son değere göre hesaplanan yedeklemeler tarafından kullanılan bulut yedekleme depolaması |
| EntityState |Metin |Nesnenin geçerli durumu. Örneğin, etkin, silindi |
| LastUpdatedDate |Tarih |Seçili satırın son güncelleştirilme tarihi |

### <a name="time"></a>Zaman

Bu tablo, zaman ile ilgili alanlarla ilgili ayrıntıları sağlar.

| Alan | Veri Türü | Açıklama |
| --- | --- | --- |
| Saat |Zaman |Günün saati. Örneğin, 1:00:00 PM |
| HourNumber |Ondalık sayı |Günün saat numarası. Örneğin, 13,00 |
| Dakika |Ondalık sayı |Saatin dakikası |
| PeriodOfTheDay |Metin |Günün zaman dilimi yuvası. Örneğin, 12-3 |
| Zaman |Zaman |Günün saati. Örneğin, 12:00:01 |
| TimeKey |Metin |Saati temsil eden anahtar değer |

### <a name="vault"></a>Kasa

Bu tablo, kasala ilgili çeşitli alanlarla ilgili temel alanları ve toplamaları sağlar.

| Alan | Veri Türü | Açıklama |
| --- | --- | --- |
| #Vaults |Tam sayı |Kasaların sayısı |
| AsOnDateTime |Tarih/saat |Seçili satır için en son yenileme zamanı |
| AzureDataCenter |Metin |Kasanın bulunduğu veri merkezi |
| EntityState |Metin |Kasa nesnesinin geçerli durumu. Örneğin, etkin, silindi |
| StorageReplicationType |Metin |Kasa için depolama çoğaltma türü. Örneğin, Geoyedekli |
| SubscriptionId |Metin |Rapor oluşturmak için seçilen müşterinin abonelik KIMLIĞI |
| vaultName |Metin |Kasanın adı |
| VaultTags |Metin |Kasayla ilişkili Etiketler |

## <a name="next-steps"></a>Sonraki adımlar

Azure Backup raporları oluşturmak için veri modelini gözden geçirdikten sonra, Power BI rapor oluşturma ve görüntüleme hakkında daha fazla bilgi için aşağıdaki makalelere bakın.

* [Power BI rapor oluşturma](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/)
* [Power BI raporları filtreleme](https://powerbi.microsoft.com/documentation/powerbi-service-about-filters-and-highlighting-in-reports/)
