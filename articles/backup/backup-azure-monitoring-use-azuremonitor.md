---
title: Azure Izleyici ile Azure Backup izleme
description: Azure Izleyici 'yi kullanarak Azure Backup iş yüklerini izleyin ve özel uyarılar oluşturun.
ms.topic: conceptual
ms.date: 06/04/2019
ms.assetid: 01169af5-7eb0-4cb0-bbdb-c58ac71bf48b
ms.openlocfilehash: 65bab1a6d6d424c90b38a3bdf99b6bf5bd8ded09
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172904"
---
# <a name="monitor-at-scale-by-using-azure-monitor"></a>Azure Izleyici 'yi kullanarak ölçeğe göre izleme

Azure Backup, bir kurtarma hizmetleri kasasında [yerleşik izleme ve uyarı özellikleri](backup-azure-monitoring-built-in-monitor.md) sağlar. Bu yetenekler ek bir yönetim altyapısı olmadan kullanılabilir. Ancak bu yerleşik hizmet aşağıdaki senaryolarda sınırlandırılır:

- Birden çok kurtarma hizmeti kasalarından verileri abonelikler arasında izleyebilirsiniz
- Tercih edilen bildirim kanalı e-posta *değilse*
- Kullanıcılar daha fazla senaryo için uyarı istiyor
- Azure 'da System Center Data Protection Manager gibi şirket içi bir bileşenden bilgi görüntülemek istiyorsanız, portalın [**yedekleme işlerinde**](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault) veya [**yedekleme uyarılarında**](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault) gösterilmez

## <a name="using-log-analytics-workspace"></a>Log Analytics çalışma alanı kullanma

> [!NOTE]
> Azure VM yedeklemelerinden veri, Azure Backup Aracısı, System Center Data Protection Manager, Azure VM 'lerdeki SQL yedeklemeleri ve Azure dosya paylaşma yedeklemeleri, Tanılama ayarları aracılığıyla Log Analytics çalışma alanına potılmış olur. Microsoft Azure Backup Server (MABS) desteği yakında eklenecektir

Ölçekteki izlemek/raporlamak için iki Azure hizmetinin özelliklerine ihtiyacınız vardır. *Tanılama ayarları* birden çok Azure Resource Manager kaynağından başka bir kaynağa veri gönderir. *Log Analytics* , diğer bildirim kanallarını tanımlamak için eylem gruplarını kullanabileceğiniz özel uyarılar oluşturur.

Aşağıdaki bölümlerde, Azure Backup ölçeklendirmede Log Analytics nasıl kullanılacağı açıklanır.

### <a name="configure-diagnostic-settings"></a>Tanılama ayarlarını yapılandırma

Kurtarma Hizmetleri kasası gibi kaynaklar Azure Resource Manager, zamanlanmış işlemler ve Kullanıcı tarafından tetiklenen işlemler hakkındaki bilgileri Tanılama verileri olarak kaydeder.

İzleme bölümünde **Tanılama ayarları** ' nı seçin ve kurtarma hizmetleri kasasının tanılama verilerinin hedefini belirtin.

![Kurtarma Hizmetleri kasasının tanılama ayarı, hedefleme Log Analytics](media/backup-azure-monitoring-laworkspace/rs-vault-diagnostic-setting.png)

Bir Log Analytics çalışma alanını başka bir abonelikten hedefleyebilirsiniz. Tek bir yerde abonelikler arasında kasa izlemek için, birden çok kurtarma hizmeti Kasası için aynı Log Analytics çalışma alanını seçin. Log Analytics çalışma alanına Azure Backup ilgili tüm bilgileri kanala almak için, günlük olarak **AzureBackupReport** ' yi seçin.

> [!IMPORTANT]
> Yapılandırmayı tamamladıktan sonra, ilk veri gönderimi işleminin tamamlanabilmesi için 24 saat beklemeniz gerekir. İlk veri gönderdikten sonra, tüm olaylar bu makalede daha sonra açıklanan [Sıklık bölümünde](#diagnostic-data-update-frequency)açıklandığı gibi gönderilir.

### <a name="deploy-a-solution-to-the-log-analytics-workspace"></a>Log Analytics çalışma alanına çözüm dağıtma

> [!IMPORTANT]
> Azure Backup ' de, LA tabanlı Izleme ve raporlama için güncelleştirilmiş, çok görünüm bir [şablon](https://azure.microsoft.com/resources/templates/101-backup-la-reporting/) yayımladık. [Önceki çözümü](https://azure.microsoft.com/resources/templates/101-backup-oms-monitoring/) kullanan kullanıcıların, yeni çözümü dağıttıktan sonra bile çalışma alanlarında görmeye devam edebileceğine lütfen unutmayın. Ancak, eski çözüm bazı küçük şema değişikliklerinden dolayı hatalı sonuçlar verebilir. Bu nedenle, kullanıcıların yeni şablonu dağıtması gerekir.

Veriler Log Analytics çalışma alanının içindeyken, verileri görselleştirmek için Log Analytics için [bir GitHub şablonu dağıtın](https://azure.microsoft.com/resources/templates/101-backup-la-reporting/) . Çalışma alanını düzgün bir şekilde tanımlamak için, aynı kaynak grubu, çalışma alanı adı ve çalışma alanı konumuna sahip olduğunuzdan emin olun. Sonra bu şablonu çalışma alanına yükler.

### <a name="view-azure-backup-data-by-using-log-analytics"></a>Log Analytics kullanarak Azure Backup verileri görüntüleme

Şablon dağıtıldıktan sonra, Azure Backup ' de izleme ve raporlama çözümü çalışma alanı Özet bölgesinde görünür. Özete gitmek için şu yollardan birini izleyin:

- **Azure izleyici**: **Öngörüler** bölümünde **diğer** ' i seçin ve ardından ilgili çalışma alanını seçin.
- **Log Analytics çalışma alanları**: ilgili çalışma alanını seçin ve ardından **genel**altında **çalışma alanı Özeti**' ni seçin.

![Log Analytics izleme ve raporlama kutucukları](media/backup-azure-monitoring-laworkspace/la-azurebackup-overview-dashboard.png)

Genel Bakış kutucuklarından herhangi birini seçtiğinizde daha fazla bilgi görüntüleyebilirsiniz. Göreceğiniz raporlardan bazıları şunlardır:

- Günlük yedekleme dışı Işler

   ![Yedekleme işleri için grafik Log Analytics](media/backup-azure-monitoring-laworkspace/la-azurebackup-backupjobsnonlog.png)

- Azure kaynakları yedeğinden gelen uyarılar

   ![Geri yükleme işleri için Log Analytics grafiği](media/backup-azure-monitoring-laworkspace/la-azurebackup-alertsazure.png)

Benzer şekilde, diğer kutucuklara tıklayarak, geri yükleme Işleri, bulut depolaması, yedekleme öğeleri, şirket Içi kaynaklardan gelen uyarılar ve günlük yedekleme Işleri hakkında raporları görebileceksiniz.

Bu grafikler, şablonla birlikte sağlanır. Gerektiğinde grafikleri düzenleyebilir veya daha fazla grafik ekleyebilirsiniz.

### <a name="create-alerts-by-using-log-analytics"></a>Log Analytics kullanarak uyarı oluşturma

Azure Izleyici 'de, bir Log Analytics çalışma alanında kendi uyarılarınızı oluşturabilirsiniz. Çalışma alanında, tercih ettiğiniz bildirim mekanizmasını seçmek için *Azure eylem gruplarını* kullanırsınız.

> [!IMPORTANT]
> Bu sorguyu oluşturma maliyeti hakkında daha fazla bilgi için bkz. [Azure Monitor fiyatlandırması](https://azure.microsoft.com/pricing/details/monitor/).

Log Analytics çalışma alanının **Günlükler** bölümünü açmak için grafiklerinden herhangi birini seçin. **Günlükler** bölümünde sorguları düzenleyin ve üzerinde uyarılar oluşturun.

![Log Analytics çalışma alanında uyarı oluşturma](media/backup-azure-monitoring-laworkspace/la-azurebackup-customalerts.png)

**Yeni uyarı kuralı**' nı seçtiğinizde, aşağıdaki görüntüde gösterildiği gibi Azure izleyici uyarı oluşturma sayfası açılır. Kaynak zaten Log Analytics çalışma alanı olarak işaretlenmiş ve eylem grubu tümleştirmesi sağlanmış.

![Log Analytics uyarı oluşturma sayfası](media/backup-azure-monitoring-laworkspace/inkedla-azurebackup-createalert.jpg)

#### <a name="alert-condition"></a>Uyarı koşulu

Bir uyarının tanımlama özelliği tetikleme koşulusıdır. Aşağıdaki görüntüde gösterildiği gibi **Günlükler** sayfasında kusto sorgusunu otomatik olarak yüklemek için **koşul** ' ı seçin. Burada, koşulu gereksinimlerinize uyacak şekilde düzenleyebilirsiniz. Daha fazla bilgi için bkz. [Sample kusto Queries](#sample-kusto-queries).

![Uyarı koşulu ayarlama](media/backup-azure-monitoring-laworkspace/la-azurebackup-alertlogic.png)

Gerekirse, kusto sorgusunu düzenleyebilirsiniz. Bir eşik, nokta ve sıklık seçin. Eşik uyarının ne zaman ortaya kaldırılacağını belirler. Süre, sorgunun çalıştırıldığı zaman penceresidir. Örneğin, eşik 0 ' dan büyükse, dönem 5 dakikadır, sıklık 5 dakikadır, bu durumda, önceki 5 dakika boyunca sorgu her 5 dakikada bir çalıştırılır. Sonuç sayısı 0 ' dan büyükse, seçili eylem grubu aracılığıyla size bildirilir.

#### <a name="alert-action-groups"></a>Uyarı eylem grupları

Bir bildirim kanalı belirtmek için bir eylem grubu kullanın. Kullanılabilir bildirim mekanizmalarını görmek için, **eylem grupları**altında **Yeni oluştur**' u seçin.

!["Eylem grubu Ekle" penceresinde kullanılabilir bildirim mekanizmaları](media/backup-azure-monitoring-laworkspace/LA-AzureBackup-ActionGroup.png)

Tek başına Log Analytics tüm uyarı ve izleme gereksinimlerini karşılarsanız veya yerleşik bildirimleri tamamlamak için Log Analytics kullanabilirsiniz.

Daha fazla bilgi için bkz. [Azure izleyici kullanarak günlük uyarıları oluşturma, görüntüleme ve yönetme](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) ve [Azure Portal eylem grupları oluşturma ve yönetme](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups).

### <a name="sample-kusto-queries"></a>Örnek kusto sorguları

Varsayılan grafikler, size uyarı oluşturabileceğiniz temel senaryolar için kusto sorguları sunar. Ayrıca, uyarı almak istediğiniz verileri almak için sorguları da değiştirebilirsiniz. Aşağıdaki örnek kusto sorgularını **Günlükler** sayfasına yapıştırın ve sorgular üzerinde uyarılar oluşturun:

- Tüm başarılı yedekleme işleri

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | where OperationName == "Job" and JobOperation_s == "Backup"
    | where JobStatus_s == "Completed"
    ````

- Tüm başarısız yedekleme işleri

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | where OperationName == "Job" and JobOperation_s == "Backup"
    | where JobStatus_s == "Failed"
    ````

- Tüm başarılı Azure VM yedekleme işleri

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
    | where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s != "Log" and JobOperationSubType_s != "Recovery point_Log"
    | join kind=inner
    (
        AzureDiagnostics
        | where Category == "AzureBackupReport"
        | where OperationName == "BackupItem"
        | where SchemaVersion_s == "V2"
        | where BackupItemType_s == "VM" and BackupManagementType_s == "IaaSVM"
        | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
        | project BackupItemUniqueId_s , BackupItemFriendlyName_s
    )
    on BackupItemUniqueId_s
    | extend Vault= Resource
    | project-away Resource
    ````

- Tüm başarılı SQL günlük yedekleme işleri

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
    | where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s == "Log"
    | join kind=inner
    (
        AzureDiagnostics
        | where Category == "AzureBackupReport"
        | where OperationName == "BackupItem"
        | where SchemaVersion_s == "V2"
        | where BackupItemType_s == "SQLDataBase" and BackupManagementType_s == "AzureWorkload"
        | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
        | project BackupItemUniqueId_s , BackupItemFriendlyName_s
    )
    on BackupItemUniqueId_s
    | extend Vault= Resource
    | project-away Resource
    ````

- Tüm başarılı Azure Backup Aracısı işleri

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
    | where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s != "Log" and JobOperationSubType_s != "Recovery point_Log"
    | join kind=inner
    (
        AzureDiagnostics
        | where Category == "AzureBackupReport"
        | where OperationName == "BackupItem"
        | where SchemaVersion_s == "V2"
        | where BackupItemType_s == "FileFolder" and BackupManagementType_s == "MAB"
        | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
        | project BackupItemUniqueId_s , BackupItemFriendlyName_s
    )
    on BackupItemUniqueId_s
    | extend Vault= Resource
    | project-away Resource
    ````

### <a name="diagnostic-data-update-frequency"></a>Tanılama verileri güncelleştirme sıklığı

Kasadaki Tanılama verileri, bazı gecikmeye sahip Log Analytics çalışma alanına potılmış olur. Her olay, kurtarma hizmetleri kasasından gönderdikten sonra, Log Analytics çalışma alanına *20 ila 30 dakika* sonra ulaşır. Gecikme hakkında daha fazla ayrıntı aşağıda verilmiştir:

- Tüm çözümlerde, yedekleme hizmetinin yerleşik uyarıları, oluşturulduktan hemen sonra gönderilir. Bu nedenle, genellikle 20 ila 30 dakika sonra Log Analytics çalışma alanında görünürler.
- Tüm çözümlerde, isteğe bağlı yedekleme işleri ve geri yükleme işleri, *tamamlanır*almaz gönderilir.
- SQL yedekleme dışındaki tüm çözümler için, zamanlanmış yedekleme işleri, *tamamlanır*almaz gönderilir.
- SQL yedekleme için, günlük yedeklemeleri 15 dakikada bir gerçekleşebildiğinden, günlükler de dahil olmak üzere tamamlanan tüm zamanlanmış yedekleme işlerinin bilgileri toplu olarak oluşturulur ve her 6 saatte bir gönderilir.
- Tüm çözümlerde yedekleme öğesi, ilke, kurtarma noktaları, depolama vb. gibi diğer bilgiler günde en az *bir kez gönderilir.*
- Yedekleme yapılandırmasındaki (ilkeyi değiştirme veya ilkeyi değiştirme gibi) bir değişiklik, ilgili tüm yedekleme bilgilerinin bir anında gönderimini tetikler.

## <a name="using-the-recovery-services-vaults-activity-logs"></a>Kurtarma Hizmetleri kasasının etkinlik günlüklerini kullanma

> [!CAUTION]
> Aşağıdaki adımlar yalnızca *Azure VM yedeklemeleri* için geçerlidir. Bu adımları Azure Backup Aracısı, Azure içindeki SQL yedeklemeleri veya Azure dosyaları gibi çözümler için kullanamazsınız.

Ayrıca, yedekleme başarısı gibi olaylara yönelik bildirim almak için etkinlik günlüklerini de kullanabilirsiniz. Başlamak için şu adımları izleyin:

1. Azure portal oturum açın.
1. İlgili kurtarma hizmetleri kasasını açın.
1. Kasanın özelliklerinde, **etkinlik günlüğü** bölümünü açın.

Uygun günlüğü belirlemek ve bir uyarı oluşturmak için:

1. Aşağıdaki görüntüde gösterilen filtreleri uygulayarak başarılı yedeklemeler için etkinlik günlükleri aldığını doğrulayın. Kayıtları görüntülemek için **TimeSpan** değerini gereken şekilde değiştirin.

   ![Azure VM yedeklemeleri için etkinlik günlüklerini bulmak üzere filtreleme](media/backup-azure-monitoring-laworkspace/activitylogs-azurebackup-vmbackups.png)

1. İlgili ayrıntıları görmek için işlem adını seçin.
1. **Kural oluştur** sayfasını açmak için **Yeni uyarı kuralı** ' nı seçin.
1. [Azure izleyici 'yi kullanarak etkinlik günlüğü uyarılarını oluşturma, görüntüleme ve yönetme](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)bölümündeki adımları izleyerek bir uyarı oluşturun.

   ![Yeni uyarı kuralı](media/backup-azure-monitoring-laworkspace/new-alert-rule.png)

Kaynak, kurtarma hizmetleri kasasının kendisidir. Etkinlik günlükleri aracılığıyla bildirilmesini istediğiniz tüm kasaların tümünde aynı adımları yineleyin. Bu uyarı olaylara dayalı olduğundan koşulun eşiği, dönemi veya sıklığı olmaz. İlgili etkinlik günlüğü oluşturulduğunda, uyarı tetiklenir.

## <a name="using-log-analytics-to-monitor-at-scale"></a>Ölçekte izlemek için Log Analytics kullanma

Etkinlik günlüklerinden oluşturulan tüm uyarıları ve Azure Izleyici 'de Log Analytics çalışma alanlarını görüntüleyebilirsiniz. Sol taraftaki **Uyarılar** bölmesini açmanız yeterlidir.

Etkinlik günlükleri aracılığıyla bildirim alabilmeniz mümkün olsa da, ölçeklendirerek izleme için etkinlik günlükleri yerine Log Analytics kullanmanızı kesinlikle öneririz. İşte şunları yapın:

- **Sınırlı senaryolar**: etkinlik günlükleri aracılığıyla yapılan bildirimler yalnızca Azure VM yedeklemeleri için geçerlidir. Bildirimlerin her kurtarma hizmetleri Kasası için ayarlanmış olması gerekir.
- **Tanım Sığdır**: zamanlanmış yedekleme etkinliği, etkinlik günlüklerinin en son tanımına uymuyor. Bunun yerine, [kaynak günlükleriyle](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace#what-you-can-do-with-resource-logs-in-a-workspace)hizalanır. Bu hizalama, etkinlik günlüğü kanalı üzerinden akan veriler değiştiğinde beklenmeyen etkilere neden olur.
- **Etkinlik günlüğü kanalında Ilgili sorunlar**: kurtarma hizmetleri kasalarında, Azure Backup POED olan etkinlik günlükleri yeni bir modeli takip ediyor. Ne yazık ki bu değişiklik, Azure Kamu, Azure Almanya ve Azure Çin 21Vianet 'deki etkinlik günlüklerinin oluşturulmasını etkiler. Bu bulut Hizmetleri kullanıcıları Azure Izleyici 'de etkinlik günlüklerinden herhangi bir uyarı oluşturup yapılandırırsa, uyarılar tetiklenmez. Ayrıca, tüm Azure genel bölgelerinde, bir Kullanıcı [Kurtarma Hizmetleri etkinlik günlüklerini bir Log Analytics çalışma alanında topluyorsa](https://docs.microsoft.com/azure/azure-monitor/platform/collect-activity-logs), bu Günlükler görünmez.

Azure Backup tarafından korunan tüm iş yüklerinizin ölçeğini izlemek ve uyarmak için bir Log Analytics çalışma alanı kullanın.

## <a name="next-steps"></a>Sonraki adımlar

Özel sorgular oluşturmak için bkz. [veri modeli Log Analytics](backup-azure-log-analytics-data-model.md).
