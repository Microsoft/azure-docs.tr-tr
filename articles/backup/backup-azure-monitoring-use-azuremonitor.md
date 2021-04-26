---
title: Azure Izleyici ile Azure Backup izleme
description: Azure Izleyici 'yi kullanarak Azure Backup iş yüklerini izleyin ve özel uyarılar oluşturun.
ms.topic: conceptual
ms.date: 06/04/2019
ms.assetid: 01169af5-7eb0-4cb0-bbdb-c58ac71bf48b
ms.openlocfilehash: 1800771bfff0afbcec8440383536734246ea8f5c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100580741"
---
# <a name="monitor-at-scale-by-using-azure-monitor"></a>Azure Izleyici 'yi kullanarak ölçeğe göre izleme

Azure Backup, bir kurtarma hizmetleri kasasında [yerleşik izleme ve uyarı özellikleri](backup-azure-monitoring-built-in-monitor.md) sağlar. Bu özellikler ek yönetim altyapısı olmadan kullanılabilir. Ancak bu yerleşik hizmet aşağıdaki senaryolarda sınırlandırılır:

- Birden çok kurtarma hizmeti kasalarından verileri abonelikler arasında izleyebilirsiniz
- Tercih edilen bildirim kanalı e-posta *değilse*
- Kullanıcılar daha fazla senaryo için uyarı istiyor
- Azure 'da System Center Data Protection Manager gibi şirket içi bir bileşenden bilgi görüntülemek istiyorsanız, portalın [**yedekleme işlerinde**](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault) veya [**yedekleme uyarılarında**](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault) gösterilmez

## <a name="using-log-analytics-workspace"></a>Log Analytics çalışma alanı kullanma

### <a name="create-alerts-by-using-log-analytics"></a>Log Analytics kullanarak uyarı oluşturma

Azure Izleyici 'de, bir Log Analytics çalışma alanında kendi uyarılarınızı oluşturabilirsiniz. Çalışma alanında, tercih ettiğiniz bildirim mekanizmasını seçmek için *Azure eylem gruplarını* kullanırsınız.

> [!IMPORTANT]
> Bu sorguyu oluşturma maliyeti hakkında daha fazla bilgi için bkz. [Azure Monitor fiyatlandırması](https://azure.microsoft.com/pricing/details/monitor/).

Log Analytics çalışma alanının **Günlükler** bölümünü açın ve kendi günlüklerinizi için bir sorgu oluşturun. **Yeni uyarı kuralı**' nı seçtiğinizde, aşağıdaki görüntüde gösterildiği gibi Azure izleyici uyarı oluşturma sayfası açılır.

![Log Analytics çalışma alanında uyarı oluşturma](media/backup-azure-monitoring-laworkspace/custom-alert.png)

Kaynak zaten Log Analytics çalışma alanı olarak işaretlenmiş ve eylem grubu tümleştirmesi sağlanmış.

![Log Analytics uyarı oluşturma sayfası](media/backup-azure-monitoring-laworkspace/inkedla-azurebackup-createalert.jpg)

#### <a name="alert-condition"></a>Uyarı koşulu

Bir uyarının tanımlama özelliği tetikleme koşulusıdır. Aşağıdaki görüntüde gösterildiği gibi **Günlükler** sayfasında kusto sorgusunu otomatik olarak yüklemek için **koşul** ' ı seçin. Burada, koşulu gereksinimlerinize uyacak şekilde düzenleyebilirsiniz. Daha fazla bilgi için bkz. [Sample kusto Queries](#sample-kusto-queries).

![Uyarı koşulu ayarlama](media/backup-azure-monitoring-laworkspace/la-azurebackup-alertlogic.png)

Gerekirse, kusto sorgusunu düzenleyebilirsiniz. Bir eşik, nokta ve sıklık seçin. Eşik uyarının ne zaman ortaya kaldırılacağını belirler. Süre, sorgunun çalıştırıldığı zaman penceresidir. Örneğin, eşik 0 ' dan büyükse, dönem 5 dakikadır, sıklık 5 dakikadır, bu durumda, önceki 5 dakika boyunca sorgu her 5 dakikada bir çalıştırılır. Sonuç sayısı 0 ' dan büyükse, seçili eylem grubu aracılığıyla size bildirilir.

> [!NOTE]
> Uyarı kuralını günde bir kez çalıştırmak için, belirtilen gün içinde oluşturulan tüm olaylar/günlüklerde ' period ' ve ' Frequency ' değerlerini 1440, yani 24 saat olarak değiştirin.

#### <a name="alert-action-groups"></a>Uyarı eylem grupları

Bir bildirim kanalı belirtmek için bir eylem grubu kullanın. Kullanılabilir bildirim mekanizmalarını görmek için, **eylem grupları** altında **Yeni oluştur**' u seçin.

!["Eylem grubu Ekle" penceresinde kullanılabilir bildirim mekanizmaları](media/backup-azure-monitoring-laworkspace/LA-AzureBackup-ActionGroup.png)

Tek başına Log Analytics tüm uyarı ve izleme gereksinimlerini karşılarsanız veya yerleşik bildirimleri tamamlamak için Log Analytics kullanabilirsiniz.

Daha fazla bilgi için bkz. [Azure izleyici kullanarak günlük uyarıları oluşturma, görüntüleme ve yönetme](../azure-monitor/alerts/alerts-log.md) ve [Azure Portal eylem grupları oluşturma ve yönetme](../azure-monitor/alerts/action-groups.md).

### <a name="sample-kusto-queries"></a>Örnek kusto sorguları

Varsayılan grafikler, size uyarı oluşturabileceğiniz temel senaryolar için kusto sorguları sunar. Ayrıca, uyarı almak istediğiniz verileri almak için sorguları da değiştirebilirsiniz. Aşağıdaki örnek kusto sorgularını **Günlükler** sayfasına yapıştırın ve sorgular üzerinde uyarılar oluşturun:

- Tüm başarılı yedekleme işleri

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | summarize arg_max(TimeGenerated,*) by JobUniqueId
    | where JobStatus=="Completed"
    ````

- Tüm başarısız yedekleme işleri

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | summarize arg_max(TimeGenerated,*) by JobUniqueId
    | where JobStatus=="Failed"
    ````

- Tüm başarılı Azure VM yedekleme işleri

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | summarize arg_max(TimeGenerated,*) by JobUniqueId
    | where JobStatus=="Completed"
    | join kind=inner
    (
        CoreAzureBackup
        | where OperationName == "BackupItem"
        | where BackupItemType=="VM" and BackupManagementType=="IaaSVM"
        | distinct BackupItemUniqueId, BackupItemFriendlyName
    )
    on BackupItemUniqueId
    ````

- Tüm başarılı SQL günlük yedekleme işleri

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup" and JobOperationSubType=="Log"
    | summarize arg_max(TimeGenerated,*) by JobUniqueId
    | where JobStatus=="Completed"
    | join kind=inner
    (
        CoreAzureBackup
        | where OperationName == "BackupItem"
        | where BackupItemType=="SQLDataBase" and BackupManagementType=="AzureWorkload"
        | distinct BackupItemUniqueId, BackupItemFriendlyName
    )
    on BackupItemUniqueId
    ````

- Tüm başarılı Azure Backup Aracısı işleri

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | summarize arg_max(TimeGenerated,*) by JobUniqueId
    | where JobStatus=="Completed"
    | join kind=inner
    (
        CoreAzureBackup
        | where OperationName == "BackupItem"
        | where BackupItemType=="FileFolder" and BackupManagementType=="MAB"
        | distinct BackupItemUniqueId, BackupItemFriendlyName
    )
    on BackupItemUniqueId
    ````

- Yedekleme öğesi başına tüketilen yedekleme alanı

    ````Kusto
    CoreAzureBackup
    //Get all Backup Items
    | where OperationName == "BackupItem"
    //Get distinct Backup Items
    | distinct BackupItemUniqueId, BackupItemFriendlyName
    | join kind=leftouter
    (AddonAzureBackupStorage
    | where OperationName == "StorageAssociation"
    //Get latest record for each Backup Item
    | summarize arg_max(TimeGenerated, *) by BackupItemUniqueId
    | project BackupItemUniqueId , StorageConsumedInMBs)
    on BackupItemUniqueId
    | project BackupItemUniqueId , BackupItemFriendlyName , StorageConsumedInMBs
    | sort by StorageConsumedInMBs desc
    ````

### <a name="diagnostic-data-update-frequency"></a>Tanılama verileri güncelleştirme sıklığı

Kasadaki Tanılama verileri, bazı gecikmeye sahip Log Analytics çalışma alanına potılmış olur. Her olay, kurtarma hizmetleri kasasından gönderdikten sonra, Log Analytics çalışma alanına *20 ila 30 dakika* sonra ulaşır. Gecikme hakkında daha fazla ayrıntı aşağıda verilmiştir:

- Tüm çözümlerde, yedekleme hizmetinin yerleşik uyarıları, oluşturulduktan hemen sonra gönderilir. Bu nedenle, genellikle 20 ila 30 dakika sonra Log Analytics çalışma alanında görünürler.
- Tüm çözümlerde, isteğe bağlı yedekleme işleri ve geri yükleme işleri, *tamamlanır* almaz gönderilir.
- SQL yedekleme dışındaki tüm çözümler için, zamanlanmış yedekleme işleri, *tamamlanır* almaz gönderilir.
- SQL yedekleme için, günlük yedeklemeleri 15 dakikada bir gerçekleşebildiğinden, günlükler de dahil olmak üzere tamamlanan tüm zamanlanmış yedekleme işlerinin bilgileri toplu olarak oluşturulur ve her 6 saatte bir gönderilir.
- Tüm çözümlerde yedekleme öğesi, ilke, kurtarma noktaları, depolama vb. gibi diğer bilgiler günde en az *bir kez gönderilir.*
- Yedekleme yapılandırmasındaki (ilkeyi değiştirme veya ilkeyi değiştirme gibi) bir değişiklik, ilgili tüm yedekleme bilgilerinin bir anında gönderimini tetikler.

## <a name="using-the-recovery-services-vaults-activity-logs"></a>Kurtarma Hizmetleri kasasının etkinlik günlüklerini kullanma

> [!CAUTION]
> Aşağıdaki adımlar yalnızca *Azure VM yedeklemeleri* için geçerlidir. Bu adımları Azure Backup Aracısı, Azure içindeki SQL yedeklemeleri veya Azure dosyaları gibi çözümler için kullanamazsınız.

Ayrıca, yedekleme başarısı gibi olaylara yönelik bildirim almak için etkinlik günlüklerini de kullanabilirsiniz. Başlamak için şu adımları izleyin:

1. Azure portalda oturum açın.
2. İlgili kurtarma hizmetleri kasasını açın.
3. Kasanın özelliklerinde, **etkinlik günlüğü** bölümünü açın.

Uygun günlüğü belirlemek ve bir uyarı oluşturmak için:

1. Aşağıdaki görüntüde gösterilen filtreleri uygulayarak başarılı yedeklemeler için etkinlik günlükleri aldığını doğrulayın. Kayıtları görüntülemek için **TimeSpan** değerini gereken şekilde değiştirin.

   ![Azure VM yedeklemeleri için etkinlik günlüklerini bulmak üzere filtreleme](media/backup-azure-monitoring-laworkspace/activitylogs-azurebackup-vmbackups.png)

2. İlgili ayrıntıları görmek için işlem adını seçin.
3. **Kural oluştur** sayfasını açmak için **Yeni uyarı kuralı** ' nı seçin.
4. [Azure izleyici 'yi kullanarak etkinlik günlüğü uyarılarını oluşturma, görüntüleme ve yönetme](../azure-monitor/alerts/alerts-activity-log.md)bölümündeki adımları izleyerek bir uyarı oluşturun.

   ![Yeni uyarı kuralı](media/backup-azure-monitoring-laworkspace/new-alert-rule.png)

Kaynak, kurtarma hizmetleri kasasının kendisidir. Etkinlik günlükleri aracılığıyla bildirilmesini istediğiniz tüm kasaların tümünde aynı adımları yineleyin. Bu uyarı olaylara dayalı olduğundan koşulun eşiği, dönemi veya sıklığı olmaz. İlgili etkinlik günlüğü oluşturulduğunda, uyarı tetiklenir.

## <a name="using-log-analytics-to-monitor-at-scale"></a>Ölçekte izlemek için Log Analytics kullanma

Etkinlik günlüklerinden oluşturulan tüm uyarıları ve Azure Izleyici 'de Log Analytics çalışma alanlarını görüntüleyebilirsiniz. Sol taraftaki **Uyarılar** bölmesini açmanız yeterlidir.

Etkinlik günlükleri aracılığıyla bildirim alabilmeniz mümkün olsa da, ölçeklendirerek izleme için etkinlik günlükleri yerine Log Analytics kullanmanızı kesinlikle öneririz. Nedeni:

- **Sınırlı senaryolar**: etkinlik günlükleri aracılığıyla yapılan bildirimler yalnızca Azure VM yedeklemeleri için geçerlidir. Bildirimlerin her kurtarma hizmetleri Kasası için ayarlanmış olması gerekir.
- **Tanım Sığdır**: zamanlanmış yedekleme etkinliği, etkinlik günlüklerinin en son tanımına uymuyor. Bunun yerine, [kaynak günlükleriyle](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace)hizalanır. Bu hizalama, etkinlik günlüğü kanalı üzerinden akan veriler değiştiğinde beklenmeyen etkilere neden olur.
- **Etkinlik günlüğü kanalında Ilgili sorunlar**: kurtarma hizmetleri kasalarında, Azure Backup POED olan etkinlik günlükleri yeni bir modeli takip ediyor. Ne yazık ki bu değişiklik, Azure Kamu, Azure Almanya ve Azure Çin 21Vianet 'deki etkinlik günlüklerinin oluşturulmasını etkiler. Bu bulut Hizmetleri kullanıcıları Azure Izleyici 'de etkinlik günlüklerinden herhangi bir uyarı oluşturup yapılandırırsa, uyarılar tetiklenmez. Ayrıca, tüm Azure genel bölgelerinde, bir Kullanıcı [Kurtarma Hizmetleri etkinlik günlüklerini bir Log Analytics çalışma alanında topluyorsa](../azure-monitor/essentials/activity-log.md), bu Günlükler görünmez.

Azure Backup tarafından korunan tüm iş yüklerinizin ölçeğini izlemek ve uyarmak için bir Log Analytics çalışma alanı kullanın.

## <a name="next-steps"></a>Sonraki adımlar

Özel sorgular oluşturmak için bkz. [veri modeli Log Analytics](backup-azure-reports-data-model.md).
