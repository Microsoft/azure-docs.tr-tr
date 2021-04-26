---
title: Kurtarma Hizmetleri kasaları için tanılama ayarlarını kullanın
description: Bu makalede, Azure Backup için eski ve yeni tanılama olaylarının nasıl kullanılacağı açıklanır.
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: b2130f06e17dd2b5cf8461d4e58342ee41c14f96
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100575424"
---
# <a name="use-diagnostics-settings-for-recovery-services-vaults"></a>Kurtarma Hizmetleri kasaları için tanılama ayarlarını kullanın

Azure Backup, analiz, uyarı ve raporlama amacıyla toplanabilecek ve kullanılabilecek tanılama olayları gönderir.

Kasaya gidip **Tanılama ayarları**' nı seçerek bir kurtarma hizmetleri Kasası için tanılama ayarlarını Azure Portal aracılığıyla yapılandırabilirsiniz. **+ Tanılama ayarı Ekle** seçeneği, bir depolama hesabına, bir olay hub 'ına veya Log Analytics çalışma alanına bir veya daha fazla tanılama olayı göndermenizi sağlar.

![Tanılama ayarları bölmesi](./media/backup-azure-diagnostics-events/diagnostics-settings-blade.png)

## <a name="diagnostics-events-available-for-azure-backup-users"></a>Azure Backup kullanıcılar için kullanılabilen tanılama olayları

Azure Backup aşağıdaki tanılama olaylarını sağlar. Her olay, belirli bir yedeklemeyle ilgili yapıtlar kümesi üzerinde ayrıntılı veriler sağlar:

* CoreAzureBackup
* AddonAzureBackupAlerts
* AddonAzureBackupProtectedInstance
* AddonAzureBackupJobs
* AddonAzureBackupPolicy
* AddonAzureBackupStorage

Hala [eski Event](#legacy-event) AzureBackupReport kullanıyorsanız, yukarıdaki olayları kullanmaya geçiş yapmanızı öneririz.

Daha fazla bilgi için bkz. [Azure Backup tanılama olayları Için veri modeli](./backup-azure-reports-data-model.md).

Bu olayların verileri bir depolama hesabına, bir Log Analytics çalışma alanına veya bir olay hub 'ına gönderilebilir. Bu verileri bir Log Analytics çalışma alanına gönderiyorsanız, **Tanılama ayarları** ekranında **kaynağa özgü** geçişi seçin. Daha fazla bilgi için aşağıdaki bölümlere bakın.

## <a name="use-diagnostics-settings-with-log-analytics"></a>Tanılama ayarlarını Log Analytics kullanma

Artık, yedekleme için adanmış Log Analytics tablolarına kasa Tanılama verileri göndermek için Azure Backup kullanabilirsiniz. Bu tablolar [kaynağa özgü tablolar](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace)olarak adlandırılır.

Kasa tanılama verilerinizi Log Analytics göndermek için:

1. Kasanıza gidin ve **Tanılama ayarları**' nı seçin. **+ Tanılama ayarı Ekle**' yi seçin.
1. Tanılama ayarına bir ad verin.
1. **Log Analytics gönder** onay kutusunu seçin ve bir Log Analytics çalışma alanı seçin.
1. Geçiş sırasında **kaynak** ' ı seçin ve aşağıdaki altı olayı seçin: **Coreazurebackup**, **AddonAzureBackupJobs**, **AddonAzureBackupAlerts**, **AddonAzureBackupPolicy**, **AddonAzureBackupStorage** ve **AddonAzureBackupProtectedInstance**.
1. **Kaydet**’i seçin.

   ![Kaynağa özgü mod](./media/backup-azure-diagnostics-events/resource-specific-blade.png)

Veriler Log Analytics çalışma alanına akar, bu olayların her biri için ayrılmış tablolar çalışma alanınızda oluşturulur. Bu tablolardan herhangi birini doğrudan sorgulayabilirsiniz. Gerekirse, bu tablolar arasında birleşimler veya birleşimler da yapabilirsiniz.

> [!IMPORTANT]
> CoreAzureBackup, AddonAzureBackupJobs, AddonAzureBackupAlerts, AddonAzureBackupPolicy, AddonAzureBackupStorage ve AddonAzureBackupProtectedInstance altı olay *yalnızca* [yedekleme raporlarında](./configure-reports.md)kaynağa özgü modda desteklenir. *Azure tanılama modunda bu altı olay için veri göndermeye çalışırsanız, yedekleme raporlarında hiçbir veri görünmez.*

## <a name="legacy-event"></a>Eski olay

Geleneksel olarak, bir kasaya yönelik yedeklemeyle ilgili tüm Tanılama verileri AzureBackupReport adlı tek bir olayda yer alır. Burada açıklanan altı olay, AzureBackupReport ' de yer alan tüm verilerin bir ayrıştırma bölümünde verilmiştir.

Şu anda, kullanıcıların bu olay üzerinde var olan özel sorgulara sahip olduğu durumlarda geriye dönük uyumluluk için AzureBackupReport olayını desteklemeye devam ediyoruz. Özel günlük uyarıları ve özel görselleştirmeler örnekleri aşağıda verilmiştir. *[Yeni olaylara](#diagnostics-events-available-for-azure-backup-users) mümkün olduğunca erken geçiş yapmanızı öneririz*. Yeni olaylar:

* Günlük sorgularında verilerin daha kolay çalışmasını sağlayın.
* Şemaların ve yapısının daha iyi keşfedilmesini sağlayın.
* Alma gecikmesi ve sorgu süreleriyle performansı geliştirir.

*Azure tanılama modundaki eski olay, sonunda kullanım dışı olacaktır. Yeni olayların seçilmesi, daha sonraki bir tarihte karmaşık geçişlere karşı kaçınmanıza yardımcı olur*. Log Analytics kullanan [raporlama çözümünüz](./configure-reports.md) , eski olaydaki verileri desteklemeye da durdurur.

### <a name="steps-to-move-to-new-diagnostics-settings-for-a-log-analytics-workspace"></a>Log Analytics çalışma alanı için yeni tanılama ayarlarına geçme adımları

1. Eski olayı ve ait oldukları abonelikleri kullanarak Log Analytics çalışma alanlarına hangi kasaların veri gönderdiğini belirler. Bu kasaları ve abonelikleri belirlemek için çalışma alanlarınızın her birinde aşağıdaki sorguyu çalıştırın.

    ````Kusto
    let RangeStart = startofday(ago(3d));
    let VaultUnderAzureDiagnostics = (){
        AzureDiagnostics
        | where TimeGenerated >= RangeStart | where Category == "AzureBackupReport" and OperationName == "Vault" and SchemaVersion_s == "V2"
        | summarize arg_max(TimeGenerated, *) by ResourceId
        | project ResourceId, Category};
    let VaultUnderResourceSpecific = (){
        CoreAzureBackup
        | where TimeGenerated >= RangeStart | where OperationName == "Vault"
        | summarize arg_max(TimeGenerated, *) by ResourceId
        | project ResourceId, Category};
        // Some Workspaces will not have AzureDiagnostics Table, so you need to use isFuzzy
    let CombinedVaultTable = (){
        union isfuzzy = true
        (VaultUnderAzureDiagnostics() ),
        (VaultUnderResourceSpecific() )
        | distinct ResourceId, Category};
    CombinedVaultTable | where Category == "AzureBackupReport"
    | join kind = leftanti (
    CombinedVaultTable | where Category == "CoreAzureBackup"
    ) on ResourceId
    | parse ResourceId with * "SUBSCRIPTIONS/" SubscriptionId:string "/RESOURCEGROUPS" * "MICROSOFT.RECOVERYSERVICES/VAULTS/" VaultName:string
    | project ResourceId, SubscriptionId, VaultName
    ````

    Aşağıda, çalışma alanlarından birinde çalıştırılan sorgunun bir ekran görüntüsü verilmiştir:

    ![Çalışma alanı sorgusu](./media/backup-azure-diagnostics-events/workspace-query.png)

2. Belirtilen kapsamdaki tüm kasalara yönelik yeni bir tanılama ayarı eklemek için Azure Backup [yerleşik Azure ilke tanımlarını](./azure-policy-configure-diagnostics.md) kullanın. Bu ilke, bir tanılama ayarı olmayan ya da yalnızca eski bir tanılama ayarına sahip olmayan kasalara yeni bir tanılama ayarı ekler. Bu ilke, bir kerede bir abonelik veya kaynak grubuna atanabilir. İlkenin atandığı her abonelik için sahip erişiminizin olması gerekir.

Yeni tablolardaki verileri kullanmak üzere özel sorgularınızın tümünü geçirene kadar, AzureBackupReport ve altı yeni olay için ayrı Tanılama ayarları olmasını tercih edebilirsiniz. Aşağıdaki görüntüde iki tanılama ayarı olan bir kasaya ait bir örnek gösterilmektedir. **Setting1** adlı ilk ayar, bir AzureBackupReport olayının verilerini Azure tanılama modundaki bir Log Analytics çalışma alanına gönderir. **Setting2** adlı ikinci ayar, altı yeni Azure Backup olayının verilerini kaynağa özgü moddaki bir Log Analytics çalışma alanına gönderir.

![İki ayar](./media/backup-azure-diagnostics-events/two-settings-example.png)

> [!IMPORTANT]
> AzureBackupReport olayı *yalnızca* Azure tanılama modunda desteklenir. *Bu olaya yönelik verileri kaynağa özgü modda göndermeye çalışırsanız, hiçbir veri Log Analytics çalışma alanına akmaz.*

> [!NOTE]
> **Azure tanılama** veya **kaynağa özgü** geçiş geçişi yalnızca Kullanıcı **Log Analytics gönder**' i seçerse görünür. Bir depolama hesabına veya bir olay hub 'ına veri göndermek için, Kullanıcı gerekli hedefi seçer ve herhangi bir ek giriş olmadan istenen olaylardan herhangi biri için onay kutularını seçer. Daha sonra, eski Event AzureBackupReport ileri ' yi seçememenizi öneririz.

## <a name="send-azure-site-recovery-events-to-log-analytics"></a>Azure Site Recovery olaylarını Log Analytics gönder

Azure Backup ve Azure Site Recovery olaylar aynı kurtarma hizmetleri kasasından gönderilir. Azure Site Recovery Şu anda kaynağa özgü tablolar için kullanılamaz. Log Analytics Azure Site Recovery olaylarını göndermek isteyen kullanıcılar, görüntüde gösterildiği gibi *yalnızca* Azure tanılama modunu kullanmaya yönlendirilir. *Azure Site Recovery olaylar için kaynağa özgü modu seçmek, gerekli verilerin Log Analytics çalışma alanına gönderilmesini engeller*.

![Site Recovery olaylar](./media/backup-azure-diagnostics-events/site-recovery-settings.png)

Özetlemek gerekirse:

* Azure Tanılama ile ayarlanmış Log Analytics tanılama zaten varsa ve üzerine özel sorgular yazdıysanız, sorgularınızı yeni olaylardan verileri kullanmak üzere geçirene kadar bu ayarı *değişmeden* tutun.
* Önerdiğimiz gibi yeni tablolara da eklemek istiyorsanız, **Yeni** bir tanılama ayarı oluşturun, **kaynağa özel**' i seçin ve altı yeni olayı seçin.
* Şu anda Log Analytics Azure Site Recovery olaylar *gönderiyorsanız, bu* olaylar için kaynağa özgü modu seçme. Aksi takdirde, bu olaylara yönelik veriler Log Analytics çalışma alanınıza akamaz. Bunun yerine, ek bir tanılama ayarı oluşturun, **Azure tanılama**' yı seçin ve ilgili Azure Site Recovery olaylarını seçin.

Aşağıdaki görüntüde, bir kasa için üç tanılama ayarı olan bir kullanıcıya ait bir örnek gösterilmektedir. **Setting1** adlı ilk ayar, bir AzureBackupReport olayından verileri Azure tanılama modundaki bir Log Analytics çalışma alanına gönderir. **Setting2** adlı ikinci ayar, altı yeni Azure Backup olaydan kaynağa özgü modda Log Analytics çalışma alanına veri gönderir. **Setting3** adlı üçüncü ayar, Azure Site Recovery olaylarından verileri Azure tanılama modundaki bir Log Analytics çalışma alanına gönderir.

![Üç ayar](./media/backup-azure-diagnostics-events/three-settings-example.png)

## <a name="next-steps"></a>Sonraki adımlar

[Tanılama olayları için Log Analytics veri modelini öğrenin](./backup-azure-reports-data-model.md)
