---
title: VM'leri çalışma saatleri dışında başlat/durdur çözümü
description: Bu VM yönetimi çözümü, Azure Izleyici günlüklerinden Azure Resource Manager sanal makinelerinizi bir zamanlamaya göre ve proaktif bir izleyiciden başlatır ve sonlandırır.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 11/06/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d7a43ee2ed8719df2c38d00c9a50811c6d5ea70d
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73718672"
---
# <a name="startstop-vms-during-off-hours-solution-in-azure-automation"></a>Azure Otomasyonu 'nda VM'leri çalışma saatleri dışında başlat/durdur çözümü

VM'leri çalışma saatleri dışında başlat/durdur çözümü, Kullanıcı tanımlı zamanlamalarda Azure sanal makinelerinizi başlatır ve sonlandırır, Azure Izleyici günlükleri aracılığıyla öngörüler sağlar ve [eylem gruplarını](../azure-monitor/platform/action-groups.md)kullanarak isteğe bağlı e-postalar gönderir. Çoğu senaryo için hem Azure Resource Manager hem de klasik VM 'Leri destekler. Bu çözümü klasik VM 'lerle birlikte kullanmak için, varsayılan olarak oluşturulmamış bir klasik RunAs hesabına ihtiyacınız vardır. Klasik RunAs hesabı oluşturma yönergeleri için bkz. klasik farklı [Çalıştır hesapları](automation-create-standalone-account.md#classic-run-as-accounts).

> [!NOTE]
> VM'leri çalışma saatleri dışında başlat/durdur çözümü, çözümü dağıtırken Otomasyon hesabınıza içeri aktarılan Azure modülleri ile test edilmiştir. Çözüm şu anda Azure modülünün daha yeni sürümleriyle çalışmaz. Bu yalnızca VM'leri çalışma saatleri dışında başlat/durdur çözümünü çalıştırmak için kullandığınız Otomasyon hesabını etkiler. Azure [Otomasyonu 'nda Azure PowerShell modüllerini güncelleştirme](automation-update-azure-modules.md) bölümünde açıklandığı gibi diğer otomasyon hesaplarınızda Azure modülünün daha yeni sürümlerini kullanmaya devam edebilirsiniz.

Bu çözüm, VM maliyetlerini iyileştirmek isteyen kullanıcılar için merkezileşmemiş düşük maliyetli bir Otomasyon seçeneği sağlar. Bu çözümle şunları yapabilirsiniz:

- VM 'Leri başlatıp durdurulacak şekilde zamanlayın.
- Azure etiketlerini kullanarak (klasik VM 'Lerde desteklenmez), sanal makinelerin artan sırada başlamasını ve durdurulmasını zamanlayın.
- VM 'Leri düşük CPU kullanımına göre oto durdur.

Geçerli çözümle ilgili sınırlamalar aşağıda verilmiştir:

- Bu çözüm, herhangi bir bölgedeki VM 'Leri yönetir, ancak yalnızca Azure Otomasyonu hesabınızla aynı abonelikte kullanılabilir.
- Bu çözüm, Azure 'da kullanılabilir ve bir Log Analytics çalışma alanını, bir Azure Otomasyonu hesabını ve uyarıları destekleyen herhangi bir bölgeye AzureGov. AzureGov bölgeleri Şu anda e-posta işlevlerini desteklemiyor.

> [!NOTE]
> Klasik VM 'Ler için çözüm kullanıyorsanız, tüm VM 'niz bulut hizmeti başına sırayla işlenir. Sanal makineler, farklı bulut hizmetlerinde paralel olarak hala işlenir. Bulut hizmeti başına 20 ' den fazla VM varsa, üst runbook **ScheduledStartStop_Parent** birden çok zamanlama oluşturmanızı ve zamanlama başına 20 VM belirtmenizi öneririz. Zamanlama özelliklerinde, **Vmlist** parametresinde, virgülle ayrılmış bir LISTE, VM adı olarak belirtin. Aksi takdirde, bu çözüm için Otomasyon işi üçten fazla saatten fazla çalışırsa, bu, [dengeli](automation-runbook-execution.md#fair-share) olmayan bir şekilde kaldırılır veya durdurulur.
>
> Azure bulut çözümü sağlayıcısı (Azure CSP) abonelikleri yalnızca Azure Resource Manager modelini destekler, Azure Resource Manager olmayan hizmetler programda kullanılamaz. Başlat/Durdur çözümü çalıştırıldığında, klasik kaynakları yönetmek için cmdlet 'ler olduğu için hatalar alabilirsiniz. CSP hakkında daha fazla bilgi edinmek için bkz. [CSP aboneliklerinde kullanılabilir hizmetler](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services#comments). CSP aboneliği kullanıyorsanız, [**External_EnableClassicVMs**](#variables) değişkenini dağıtımdan sonra **false** olarak değiştirmelisiniz.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Ön koşullar

Bu çözüme yönelik runbook 'lar bir [Azure farklı çalıştır hesabıyla](automation-create-runas-account.md)çalışır. Bu, kullanım dışı veya sıklıkla değişebilir bir parola yerine sertifika kimlik doğrulaması kullandığından, farklı çalıştır hesabı tercih edilen kimlik doğrulama yöntemidir.

VM çözümünü Başlat/Durdur çözümü için ayrı bir Otomasyon hesabı kullanmanızı öneririz. Bunun nedeni, Azure modül sürümlerinin sık sık yükseltilme ve parametrelerinin değişme nedeni olabilir. Başlat/Durdur sanal makine çözümü aynı temposunda üzerinde yükseltilmemiştir, bu nedenle kullandığı cmdlet 'lerin daha yeni sürümleriyle çalışmayabilir. Ayrıca, bir test Otomasyon hesabındaki modül güncelleştirmelerini Üretim otomasyon hesabınızda içeri aktarmadan önce test bir test Otomasyon hesabında test etmenizi öneririz.

### <a name="permissions-needed-to-deploy"></a>Dağıtım için gereken izinler

Bir kullanıcının, saatlerde VM 'Leri Başlat/Durdur çözümü dağıtması için sahip olması gereken bazı izinler vardır. Önceden oluşturulmuş bir Otomasyon hesabı ve Log Analytics çalışma alanı kullanılıyorsa veya dağıtım sırasında yenilerini oluştururken bu izinler farklıdır. Abonelik üzerinde ve Azure Active Directory kiracınızda genel yönetici olarak bir katkıda bulunağınız varsa, aşağıdaki izinleri yapılandırmanız gerekmez. Bu haklara sahip değilseniz veya özel bir rol yapılandırmanız gerekmiyorsa, aşağıda gereken izinlere bakın.

#### <a name="pre-existing-automation-account-and-log-analytics-workspace"></a>Önceden var olan Otomasyon hesabı ve Log Analytics çalışma alanı

Mevcut bir Otomasyon hesabına ve Log Analytics çalışma alanına, saat dışı bir şekilde VM 'Leri Başlat/Durdur çözümü dağıtmak için, çözümü dağıtan Kullanıcı **kaynak grubunda**aşağıdaki izinleri gerektirir. Roller hakkında daha fazla bilgi için bkz. [Azure kaynakları Için özel roller](../role-based-access-control/custom-roles.md).

| İzin | Kapsam|
| --- | --- |
| Microsoft. Automation/automationAccounts/Read | Kaynak Grubu |
| Microsoft. Automation/automationAccounts/değişkenler/yazma | Kaynak Grubu |
| Microsoft. Automation/automationAccounts/zamanlamalar/yazma | Kaynak Grubu |
| Microsoft. Automation/automationAccounts/Runbook/yazma | Kaynak Grubu |
| Microsoft. Automation/automationAccounts/bağlantılar/yazma | Kaynak Grubu |
| Microsoft. Automation/automationAccounts/sertifikalar/yazma | Kaynak Grubu |
| Microsoft. Automation/automationAccounts/modüller/yazma | Kaynak Grubu |
| Microsoft. Automation/automationAccounts/modüller/okuma | Kaynak Grubu |
| Microsoft. Automation/automationAccounts/Jobzamanlamalar/yazma | Kaynak Grubu |
| Microsoft. Automation/automationAccounts/Jobs/Write | Kaynak Grubu |
| Microsoft. Automation/automationAccounts/Jobs/okuma | Kaynak Grubu |
| Microsoft. OperationsManagement/Solutions/Write | Kaynak Grubu |
| Microsoft. Operationalınsights/çalışma alanları/* | Kaynak Grubu |
| Microsoft. Insights/diagnosticSettings/Write | Kaynak Grubu |
| Microsoft. Insights/ActionGroups/Write | Kaynak Grubu |
| Microsoft. Insights/ActionGroups/Read | Kaynak Grubu |
| Microsoft. resources/abonelikler/resourceGroups/Read | Kaynak Grubu |
| Microsoft. resources/dağıtımlar/* | Kaynak Grubu |

#### <a name="new-automation-account-and-a-new-log-analytics-workspace"></a>Yeni Otomasyon hesabı ve yeni bir Log Analytics çalışma alanı

Yeni bir Otomasyon hesabına ve Log Analytics çalışma alanına, saat dışı bir şekilde VM 'Leri Başlat/Durdur çözümü dağıtmak için, çözümü dağıtan Kullanıcı, önceki bölümde tanımlanan izinlere ve aşağıdaki izinlere sahip olmalıdır:

- Abonelikte ortak yönetici-klasik VM 'Leri yönetecekleriniz yalnızca klasik farklı çalıştır hesabı oluşturmak için gereklidir. [Klasik runas hesapları](automation-create-standalone-account.md#classic-run-as-accounts) artık varsayılan olarak oluşturulmaz.
- [Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md) **uygulama geliştirici** rolünün bir üyesi. Farklı Çalıştır hesaplarını yapılandırma hakkında daha fazla bilgi için bkz. [Farklı Çalıştır hesaplarını yapılandırma izinleri](manage-runas-account.md#permissions).
- Abonelik üzerinde veya aşağıdaki izinlerle katkıda bulunan.

| İzin |Kapsam|
| --- | --- |
| Microsoft. Authorization/Işlemler/okuma | Abonelik|
| Microsoft. Authorization/Permissions/Read |Abonelik|
| Microsoft. Authorization/Roleatamalar/okuma | Abonelik |
| Microsoft.Authorization/roleAssignments/write | Abonelik |
| Microsoft. Authorization/Roleatamaları/silme | Abonelik |
| Microsoft. Automation/automationAccounts/Connections/Read | Kaynak Grubu |
| Microsoft. Automation/automationAccounts/sertifikalar/okuma | Kaynak Grubu |
| Microsoft. Automation/automationAccounts/Write | Kaynak Grubu |
| Microsoft. Operationalınsights/çalışma alanları/yazma | Kaynak Grubu |

## <a name="deploy-the-solution"></a>Çözümü dağıtma

VM'leri çalışma saatleri dışında başlat/durdur çözümünü Otomasyon hesabınıza eklemek için aşağıdaki adımları uygulayın ve ardından çözümü özelleştirmek için değişkenleri yapılandırın.

1. Otomasyon hesabından **Ilgili kaynaklar**altında **VM Başlat/Durdur** ' u seçin. Buradan **daha fazla bilgi edinin ve çözümü etkinleştirin**. Zaten dağıtılan bir Başlat/Durdur VM çözümünüz varsa, **çözümü Yönet** ' i tıklatarak ve listede bularak bunu seçebilirsiniz.

   ![Otomasyon hesabından etkinleştir](./media/automation-solution-vm-management/enable-from-automation-account.png)

   > [!NOTE]
   > Ayrıca, **kaynak oluştur ' a**tıklayarak Azure Portal herhangi bir yerden oluşturabilirsiniz. Market sayfasında **Başlat** veya **Başlat/Durdur**gibi bir anahtar sözcük yazın. Yazmaya başladığınızda liste, girişinize göre filtrelenir. Alternatif olarak, çözümün tam adından bir veya daha fazla anahtar sözcük yazabilir ve ardından ENTER tuşuna basabilirsiniz. Arama sonuçlarından **VM'leri çalışma saatleri dışında Başlat/Durdur** seçin.

2. Seçili çözümün **VM'leri çalışma saatleri dışında Başlat/Durdur** sayfasında, Özet bilgilerini gözden geçirin ve ardından **Oluştur**' a tıklayın.

   ![Azure portal](media/automation-solution-vm-management/azure-portal-01.png)

3. **Çözüm Ekle** sayfası görüntülenir. Çözümü Otomasyon aboneliğinize aktarmadan önce yapılandırmanız istenir.

   ![VM yönetimi çözüm ekleme sayfası](media/automation-solution-vm-management/azure-portal-add-solution-01.png)

4. **Çözüm Ekle** sayfasında, **çalışma alanı**' nı seçin. Otomasyon hesabının bulunduğu Azure aboneliğine bağlı bir Log Analytics çalışma alanı seçin. Çalışma alanınız yoksa **Yeni çalışma alanı oluştur**' u seçin. **Log Analytics çalışma alanı** sayfasında, aşağıdaki adımları uygulayın:
   - Yeni **Log Analytics çalışma alanı**için bir ad belirtin (örneğin, "ContosoLAWorkspace").
   - Varsayılan seçilen değer uygun değilse, açılan listeden seçerek bağlantı için bir **abonelik** seçin.
   - **Kaynak grubu**için, yeni bir kaynak grubu oluşturabilir veya var olan bir grup seçebilirsiniz.
   - Bir **Konum** seçin. Şu anda yalnızca **Avustralya Güneydoğu**, **Kanada Orta**, **Orta Hindistan**, **Doğu ABD**, **Japonya Doğu**, **Güneydoğu Asya**, **UK Güney**, **Batı Avrupa**ve **Batı ABD 2** yer aldığı konumlar mevcuttur .
   - Bir **Fiyatlandırma katmanı** seçin. **GB başına (tek başına)** seçeneğini belirleyin. Azure Izleyici günlükleri güncelleştirilmiş [fiyatlandırmaya](https://azure.microsoft.com/pricing/details/log-analytics/) sahıptır ve GB başına katman tek seçenektir.

   > [!NOTE]
   > Çözümleri etkinleştirirken Log Analytics çalışma alanı ile Otomasyon Hesabı arasında bağlantı kurma seçeneği yalnızca belirli bölgelerde desteklenmektedir.
   >
   > Desteklenen eşleme çiftlerinin bir listesi için bkz. [Otomasyon hesabı ve Log Analytics çalışma alanı Için bölge eşleme](how-to/region-mappings.md).

5. **Log Analytics çalışma alanı** sayfasında gerekli bilgileri sağladıktan sonra **Oluştur**' a tıklayın. Bu işlemi, tamamlandığında **Çözüm Ekle** sayfasına geri getiren menüdeki **Bildirimler** bölümünden izleyebilirsiniz.
6. **Çözüm Ekle** sayfasında **Otomasyon hesabı**' nı seçin. Yeni bir Log Analytics çalışma alanı oluşturuyorsanız, onunla ilişkilendirilecek yeni bir Otomasyon hesabı oluşturabilir veya bir Log Analytics çalışma alanına bağlı olmayan var olan bir Otomasyon hesabı seçebilirsiniz. Var olan bir Otomasyon hesabı seçin veya **Otomasyon**hesabı Ekle ' ye tıklayıp **Otomasyon hesabı ekle** sayfasında aşağıdaki bilgileri sağlayın:
   - **Ad** alanına Otomasyon hesabının adını girin.

     Diğer tüm seçenekler, seçilen Log Analytics çalışma alanına göre otomatik olarak doldurulur. Bu seçenekler değiştirilemez. Bu çözüme dahil olan runbook'lar için varsayılan kimlik doğrulama yöntemi, bir Azure Farklı Çalıştır hesabıdır. **Tamam**' a tıkladıktan sonra yapılandırma seçenekleri onaylanır ve Otomasyon hesabı oluşturulur. Bu işlemin ilerleme durumunu menüdeki **Bildirimler**’in altından izleyebilirsiniz.

7. Son olarak, **Çözüm Ekle** sayfasında **yapılandırma**' yı seçin. **Parametreler** sayfası görüntülenir.

   ![Çözüm için parametreler sayfası](media/automation-solution-vm-management/azure-portal-add-solution-02.png)

   Şunları yapmanız istenir:
   - **Hedef ResourceGroup adlarını**belirtin. Bu değerler, bu çözüm tarafından yönetilecek VM 'Leri içeren kaynak grubu adlarıdır. Birden fazla ad girebilir ve her birini virgülle ayırarak ayırabilirsiniz (değerler büyük/küçük harfe duyarlı değildir). Abonelikte yer alan tüm kaynak gruplarındaki sanal makineleri hedeflemek istiyorsanız, joker karakter kullanılması desteklenir. Bu değer **External_Start_ResourceGroupNames** ve **External_Stop_ResourceGroupNames** değişkenlerinde depolanır.
   - **VM dışlama listesini (dize)** belirtin. Bu değer, hedef kaynak grubundaki bir veya daha fazla sanal makinenin adıdır. Birden fazla ad girebilir ve her birini virgülle ayırarak ayırabilirsiniz (değerler büyük/küçük harfe duyarlı değildir). Joker karakter kullanımı desteklenir. Bu değer **External_ExcludeVMNames** değişkeninde depolanır.
   - Bir **zamanlama**seçin. Zamanlamanız için bir tarih ve saat seçin. Seçtiğiniz zamandan itibaren bir yeniden gerçekleşen günlük zamanlama oluşturulacaktır. Farklı bir bölge seçme kullanılamıyor. Çözümü yapılandırdıktan sonra, zamanlamayı belirli saat diliminize göre yapılandırmak için, bkz. [başlatma ve kapatılma zamanlamasını değiştirme](#modify-the-startup-and-shutdown-schedules).
   - Bir eylem grubundan **e-posta bildirimleri** almak için **Yes** varsayılan değerini kabul edin ve geçerli bir e-posta adresi sağlayın. **Hayır** ' ı seçerseniz ve e-posta bildirimleri almak istediğiniz sonraki bir tarihte karar verirseniz, geçerli e-posta adresleriyle oluşturulan [Eylem grubunu](../azure-monitor/platform/action-groups.md) virgülle ayırarak güncelleştirebilirsiniz. Aşağıdaki uyarı kurallarını da etkinleştirmeniz gerekir:

     - AutoStop_VM_Child
     - Scheduled_StartStop_Parent
     - Sequenced_StartStop_Parent

     > [!IMPORTANT]
     > **Hedef ResourceGroup adları** için varsayılan değer bir **&ast;** . Bu, bir abonelikteki tüm VM 'Leri hedefler. Çözümün aboneliğinizdeki tüm VM 'Leri hedeflemesini istemiyorsanız, zamanlamalar etkinleştirilmeden önce bu değerin kaynak grubu adları listesine güncelleştirilmesi gerekir.

8. Çözüm için gereken başlangıç ayarlarını yapılandırdıktan sonra, **Tamam** ' a tıklayarak **Parametreler** sayfasını kapatın ve **Oluştur**' u seçin. Tüm ayarlar doğrulandıktan sonra, çözüm aboneliğinize dağıtılır. Bu işlemin tamamlanması birkaç saniye sürebilir ve ilerleme durumunu menüdeki **Bildirimler** ' in altından izleyebilirsiniz.

> [!NOTE]
> Azure bulut çözümü sağlayıcısı (Azure CSP) aboneliğiniz varsa, dağıtım tamamlandıktan sonra Otomasyon hesabınızda, **paylaşılan kaynaklar** altındaki **değişkenlere** gidin ve [**External_EnableClassicVMs**](#variables) değişkenini false olarak ayarlayın. Bu, çözümün klasik VM kaynaklarına bakmasını engeller.

## <a name="scenarios"></a>Senaryolar

Çözüm üç ayrı senaryo içerir. Bu senaryolar şunlardır:

### <a name="scenario-1-startstop-vms-on-a-schedule"></a>Senaryo 1: VM 'Leri zamanlamaya göre Başlat/Durdur

Bu senaryo, çözümü ilk kez dağıttığınızda varsayılan yapılandırmadır. Örneğin, bu uygulamayı bir abonelik genelinde tüm VM 'Leri durduracak şekilde yapılandırabilir ve ardından ofisiniz geri döndüğünüzde bu aboneliği sabah içinde başlatabilirsiniz. Dağıtım sırasında **Zamanlanmış-startvm** ve **Zamanlanmış-stopvm** zamanlamalarını yapılandırdığınızda, hedeflenen VM 'leri başlatıp durdurur. Bu çözümü yalnızca VM 'Leri durduracak şekilde yapılandırmak için, bkz. özel bir zamanlamayı yapılandırmayı öğrenmek için [Başlangıç ve kapalı zamanlamaları değiştirme](#modify-the-startup-and-shutdown-schedules) .

> [!NOTE]
> Zamanlama saati parametresini yapılandırırken saat dilimi geçerli saat diliminiz. Ancak, Azure Otomasyonu 'nda UTC biçiminde depolanır. Dağıtım sırasında işlendiği için herhangi bir zaman dilimi dönüştürmesi yapmanız gerekmez.

Şu değişkenleri yapılandırarak hangi VM 'Lerin kapsamda olduğunu denetlersiniz: **External_Start_ResourceGroupNames**, **External_Stop_ResourceGroupNames**ve **External_ExcludeVMNames**.

Eylemi bir abonelik ve kaynak grubuna karşı hedefleyerek veya VM 'lerin belirli bir listesini hedefleyerek, her ikisini birden değil de etkinleştirebilirsiniz.

#### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>Başlat ve durdur eylemlerini bir aboneliğe ve kaynak grubuna göre hedefleyin

1. Hedef VM 'Leri belirtmek için **External_Stop_ResourceGroupNames** ve **External_ExcludeVMNames** değişkenlerini yapılandırın.
2. **Zamanlanmış-startvm** ve **Zamanlanmış-stopvm** zamanlamalarını etkinleştirin ve güncelleştirin.
3. Değişikliklerinizi önizlemek için, ACTION parametresi **Start** ve whatIf parametresini **true** olarak ayarlanmış **ScheduledStartStop_Parent** runbook 'unu çalıştırın.

#### <a name="target-the-start-and-stop-action-by-vm-list"></a>VM listesine göre Başlat ve Durdur eylemini hedefleme

1. **ScheduledStartStop_Parent** **runbook 'u eylem parametresiyle çalıştırın,** *vmlist* parametresindeki VM 'lerin virgülle ayrılmış bir listesini ekleyin ve ardından whatIf parametresini **true**olarak ayarlayın. Değişikliklerinizi önizleyin.
1. **External_ExcludeVMNames** parametresini, virgülle ayrılmış VM 'lerin bir listesiyle yapılandırın (VM1, VM2, VM3).
1. Bu senaryo **External_Start_ResourceGroupNames** ve **External_Stop_ResourceGroupnames** değişkenlerini dikkate almaz. Bu senaryo için kendi Otomasyon zamanlamanızı oluşturmanız gerekir. Ayrıntılar için bkz. [Azure Otomasyonu 'nda runbook 'U zamanlama](../automation/automation-schedules.md).

> [!NOTE]
> **Hedef ResourceGroup adları** için değer, hem **External_Start_ResourceGroupNames** hem de **External_Stop_ResourceGroupNames**için değer olarak depolanır. Daha fazla ayrıntı düzeyi için, bu değişkenlerin her birini farklı kaynak gruplarını hedefleyecek şekilde değiştirebilirsiniz. Başlat eylemi için, **External_Start_ResourceGroupNames**kullanın ve durdurma eylemi için **External_Stop_ResourceGroupNames**kullanın. VM 'Ler başlatma ve durdurma zamanlamalarına otomatik olarak eklenir.

### <a name="scenario-2-startstop-vms-in-sequence-by-using-tags"></a>Senaryo 2: etiketleri kullanarak sırayla VM 'leri başlatma/durdurma

Dağıtılmış bir iş yükünü destekleyen birden çok VM üzerinde iki veya daha fazla bileşen içeren bir ortamda, bileşenlerin başlatıldığı ve sırasıyla durdurulduğu sırayı destekleme. Aşağıdaki adımları gerçekleştirerek bu senaryoyu gerçekleştirebilirsiniz:

#### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>Başlat ve durdur eylemlerini bir aboneliğe ve kaynak grubuna göre hedefleyin

1. **External_Start_ResourceGroupNames** ve **External_Stop_ResourceGroupNames** değişkenlerine hedeflenmiş VM 'lere pozitif bir tamsayı değeri olan bir **sequencestart** ve **sequencestop** etiketi ekleyin. Başlat ve Durdur eylemleri artan sırada gerçekleştirilir. Bir VM 'nin nasıl etiketleyeceğinizi öğrenmek için bkz. Azure ['Da Windows sanal makinesini etiketleme](../virtual-machines/windows/tag.md) ve [Azure 'Da Linux sanal makinesini etiketleme](../virtual-machines/linux/tag.md).
1. **Sıralı-startvm** ve **sıralı-stopvm** zamanlamalarını gereksinimlerinize uygun tarih ve saate göre değiştirin ve zamanlamayı etkinleştirin.
1. Değişikliklerinizi önizlemek için, ACTION parametresi **Start** ve whatIf parametresini **true** olarak ayarlanmış **SequencedStartStop_Parent** runbook 'unu çalıştırın.
1. Üretim VM 'lerine göre uygulamadan önce eylemin önizlemesini görüntüleyin ve gerekli değişiklikleri yapın. Hazırlık sırasında, parametresini **false**olarak ayarlanmış şekilde el ile yürütün veya Otomasyon zamanlaması **sıralı-startvm** ve **sıralı-stopvm** ' n i n belirlenmiş zamanlamanızı izleyerek otomatik olarak çalışmasını sağlayın.

#### <a name="target-the-start-and-stop-action-by-vm-list"></a>VM listesine göre Başlat ve Durdur eylemini hedefleme

1. **Vmlist** parametresine eklemeyi planladığınız VM 'lere pozitif bir tamsayı değeri olan bir **sequencestart** ve **sequencestop** etiketi ekleyin.
1. **SequencedStartStop_Parent** **runbook 'u eylem parametresiyle çalıştırın,** *vmlist* parametresindeki VM 'lerin virgülle ayrılmış bir listesini ekleyin ve ardından whatIf parametresini **true**olarak ayarlayın. Değişikliklerinizi önizleyin.
1. **External_ExcludeVMNames** parametresini, virgülle ayrılmış VM 'lerin bir listesiyle yapılandırın (VM1, VM2, VM3).
1. Bu senaryo **External_Start_ResourceGroupNames** ve **External_Stop_ResourceGroupnames** değişkenlerini dikkate almaz. Bu senaryo için kendi Otomasyon zamanlamanızı oluşturmanız gerekir. Ayrıntılar için bkz. [Azure Otomasyonu 'nda runbook 'U zamanlama](../automation/automation-schedules.md).
1. Üretim VM 'lerine göre uygulamadan önce eylemin önizlemesini görüntüleyin ve gerekli değişiklikleri yapın. Hazırlandığınızda, izleme-ve-Tanılama/izleme-eylem-groupsrunbook ' u, parametresini **false**olarak ayarlanmış şekilde el ile yürütün veya Otomasyon zamanlamasının **sıralı-startvm** ve **sıralı-stopvm** otomatik olarak çalışmasını sağlayın önceden belirlenmiş zamanlamanızı takip edin.

### <a name="scenario-3-startstop-automatically-based-on-cpu-utilization"></a>Senaryo 3: CPU kullanımına göre otomatik olarak Başlat/Durdur

Bu çözüm, saat sonu gibi yoğun olmayan dönemler sırasında kullanılmayan Azure VM 'Leri değerlendirerek ve işlemci kullanımı% x ' ten küçük olduğunda otomatik olarak kapatmak için aboneliğinizdeki sanal makinelerin maliyetinin yönetilmesine yardımcı olabilir.

Varsayılan olarak, çözüm, ortalama kullanımın yüzde 5 veya daha az olup olmadığını görmek için CPU Ölçümü yüzdesini değerlendirmek üzere önceden yapılandırılmıştır. Bu senaryo aşağıdaki değişkenlerle denetlenir ve varsayılan değerler gereksinimlerinizi karşılamıyorsa değiştirilebilir:

- External_AutoStop_MetricName
- External_AutoStop_Threshold
- External_AutoStop_TimeAggregationOperator
- External_AutoStop_TimeWindow

Eylemi bir abonelik ve kaynak grubuna karşı hedefleyerek veya VM 'lerin belirli bir listesini hedefleyerek, her ikisini birden değil de etkinleştirebilirsiniz.

#### <a name="target-the-stop-action-against-a-subscription-and-resource-group"></a>Durdurma eylemini bir aboneliğe ve kaynak grubuna karşı hedefleyin

1. Hedef VM 'Leri belirtmek için **External_Stop_ResourceGroupNames** ve **External_ExcludeVMNames** değişkenlerini yapılandırın.
1. **Schedule_AutoStop_CreateAlert_Parent** zamanlamasını etkinleştirin ve güncelleştirin.
1. Değişikliklerinizi önizlemek için, ACTION parametresi **Start** ve whatIf parametresini **true** olarak ayarlanmış **AutoStop_CreateAlert_Parent** runbook 'unu çalıştırın.

#### <a name="target-the-start-and-stop-action-by-vm-list"></a>VM listesine göre Başlat ve Durdur eylemini hedefleme

1. **AutoStop_CreateAlert_Parent** **runbook 'u eylem parametresiyle çalıştırın,** *vmlist* parametresindeki VM 'lerin virgülle ayrılmış bir listesini ekleyin ve ardından whatIf parametresini **true**olarak ayarlayın. Değişikliklerinizi önizleyin.
1. **External_ExcludeVMNames** parametresini, virgülle ayrılmış VM 'lerin bir listesiyle yapılandırın (VM1, VM2, VM3).
1. Bu senaryo **External_Start_ResourceGroupNames** ve **External_Stop_ResourceGroupnames** değişkenlerini dikkate almaz. Bu senaryo için kendi Otomasyon zamanlamanızı oluşturmanız gerekir. Ayrıntılar için bkz. [Azure Otomasyonu 'nda runbook 'U zamanlama](../automation/automation-schedules.md).

CPU kullanımına bağlı olarak VM 'Leri durdurma zamanlamanız olduğuna göre, bunları başlatmak için aşağıdaki zamanlamalardan birini etkinleştirmeniz gerekir.

- Aboneliğe ve kaynak grubuna göre hedef başlatma eylemi. **Zamanlanan-StartVM** zamanlamalarını test etmek ve etkinleştirmek için [Senaryo 1](#scenario-1-startstop-vms-on-a-schedule) ' deki adımlara bakın.
- Abonelik, kaynak grubu ve etikete göre hedef başlatma eylemi. **Sıralı-StartVM** zamanlamalarını test etmek ve etkinleştirmek için [Senaryo 2](#scenario-2-startstop-vms-in-sequence-by-using-tags) ' deki adımlara bakın.

## <a name="solution-components"></a>Çözüm bileşenleri

Bu çözüm, Azure Izleyici günlükleriyle önceden yapılandırılmış runbook 'ları, zamanlamaları ve tümleştirmeyi içerir, böylece sanal makinelerinizin başlatılması ve kapatılmasını iş gereksinimlerinize uyacak şekilde uyarlayabilirsiniz.

### <a name="runbooks"></a>Runbook'lar

Aşağıdaki tabloda, bu çözüm tarafından Otomasyon hesabınıza dağıtılan runbook 'lar listelenmektedir. Runbook kodunda değişiklik yapmayın. Bunun yerine, yeni işlevsellik için kendi runbook 'unuzu yazın.

> [!IMPORTANT]
> "Alt" ile hiçbir runbook 'u doğrudan adına eklemeyin.

Tüm üst runbook 'lar, _whatIf_ parametresini içerir. **Doğru**olarak ayarlandığında, _whatIf_ , runbook 'un _whatIf_ parametresi olmadan çalıştırıldığında aldığı tam davranış ayrıntılarını destekler ve doğru VM 'lerin hedeflenmekte olduğunu doğrular. Bir runbook yalnızca, _whatIf_ parametresi **false**olarak ayarlandığında tanımlı eylemlerini gerçekleştirir.

|Runbook | Parametreler | Açıklama|
| --- | --- | ---|
|AutoStop_CreateAlert_Child | VMObject <br> AlertAction <br> WebHookURI | Üst runbook 'tan çağırılır. Bu runbook, oto durdur senaryosu için kaynak temelinde uyarı oluşturur.|
|AutoStop_CreateAlert_Parent | VMList<br> WhatIf: true veya false  | Hedeflenen abonelik veya kaynak gruplarındaki VM 'lerde Azure uyarı kuralları oluşturur veya güncelleştirir. <br> VMList: VM 'lerin virgülle ayrılmış listesi. Örneğin, _VM1, VM2, vm3_.<br> *WhatIf* runbook mantığını yürütmeden doğrular.|
|AutoStop_Disable | yok | Oto durdurma uyarılarını ve Varsayılan zamanlamayı devre dışı bırakır.|
|AutoStop_StopVM_Child | WebHookData | Üst runbook 'tan çağırılır. Uyarı kuralları VM 'yi durdurmak için bu runbook 'u çağırır.|
|Bootstrap_Main | yok | Genellikle Azure Resource Manager tarafından erişilemeyen webhookURI gibi önyükleme yapılandırmalarının ayarlanışında bir kez kullanılır. Bu runbook başarılı dağıtım sonrasında otomatik olarak kaldırılır.|
|ScheduledStartStop_Child | VMName <br> Eylem: Başlat veya Durdur <br> ResourceGroupName | Üst runbook 'tan çağırılır. Zamanlanan durdurma için bir Başlat veya Durdur eylemi yürütür.|
|ScheduledStartStop_Parent | Eylem: Başlat veya Durdur <br>VMList <br> WhatIf: true veya false | Bu ayar, abonelikteki tüm sanal makineleri etkiler. **External_Start_ResourceGroupNames** ve **External_Stop_ResourceGroupNames** ' i yalnızca bu hedeflenen kaynak gruplarında yürütülecek şekilde düzenleyin. Ayrıca, **External_ExcludeVMNames** değişkenini güncelleştirerek belirli VM 'leri hariç bırakabilirsiniz.<br> VMList: VM 'lerin virgülle ayrılmış listesi. Örneğin, _VM1, VM2, vm3_.<br> _WhatIf_ runbook mantığını yürütmeden doğrular.|
|SequencedStartStop_Parent | Eylem: Başlat veya Durdur <br> WhatIf: true veya false<br>VMList| Başlat/Durdur etkinliğini sıralamak istediğiniz her VM üzerinde **sequencestart** ve **sequencestop** adlı Etiketler oluşturun. Bu etiket adları büyük/küçük harfe duyarlıdır. Etiketin değeri, başlatmak veya durdurmak istediğiniz sıraya karşılık gelen pozitif bir tamsayı (1, 2, 3) olmalıdır. <br> VMList: VM 'lerin virgülle ayrılmış listesi. Örneğin, _VM1, VM2, vm3_. <br> _WhatIf_ runbook mantığını yürütmeden doğrular. <br> **Note**: VM 'Lerin Azure Automation değişkenlerinde External_Start_ResourceGroupNames, External_Stop_ResourceGroupNames ve External_ExcludeVMNames olarak tanımlanan kaynak grupları içinde olması gerekir. Eylemlerin etkili olabilmesi için uygun etiketlere sahip olmaları gerekir.|

### <a name="variables"></a>Değişkenler

Aşağıdaki tabloda, Otomasyon hesabınızda oluşturulan değişkenler listelenmektedir. Yalnızca **External**ile önekli değişkenleri değiştirin. Ön önekli değişkenlerin değiştirilmesi **, istenmeyen** etkilere neden olur.

|Değişken | Açıklama|
|---------|------------|
|External_AutoStop_Condition | Bir uyarıyı tetiklemeden önce koşulu yapılandırmak için gereken koşullu işleç. Kabul edilebilir değerler şunlardır **GreaterThan**, **GreaterThanOrEqual**, **LessThan**ve **Less okarşılandığından al**.|
|External_AutoStop_Description | CPU yüzdesi eşiği aşarsa VM 'yi durdurma uyarısı.|
|External_AutoStop_MetricName | Azure uyarı kuralının yapılandırıldığı performans ölçümünün adı.|
|External_AutoStop_Threshold | _External_AutoStop_MetricName_değişkeninde belirtilen Azure uyarı kuralının eşiği. Yüzde değerleri 1 ile 100 arasında değişebilir.|
|External_AutoStop_TimeAggregationOperator | Koşulu değerlendirmek için seçilen pencere boyutuna uygulanan zaman toplama işleci. Kabul edilebilir değerler **Ortalama**, **En düşük**, **en yüksek**, **Toplam**ve **en son**değerlerdir.|
|External_AutoStop_TimeWindow | Azure 'un bir uyarının tetiklenmesi için seçili ölçümleri çözümleyen pencere boyutu. Bu parametre, girişi TimeSpan biçiminde kabul eder. Olası değerler 5 dakikadan 6 saate kadar sürer.|
|External_EnableClassicVMs| Klasik VM 'Lerin çözüm tarafından hedeflenip hedeflenmediğini belirtir. Varsayılan değer true 'dur. Bu, CSP abonelikleri için false olarak ayarlanmalıdır. Klasik VM 'Ler [Klasik bir farklı çalıştır hesabı](automation-create-standalone-account.md#classic-run-as-accounts)gerektirir.|
|External_ExcludeVMNames | Adları boşluk olmadan virgülle ayırarak dışlanacak VM adlarını girin. Bu, 140 VM ile sınırlıdır. Bu virgülle ayrılmış listeye 140 'den fazla VM eklerseniz, hariç tutulacak şekilde ayarlanan VM 'Ler istenmeden başlatılabilir veya durdurulabilir.|
|External_Start_ResourceGroupNames | Başlangıç eylemlerine yönelik olarak bir virgül kullanarak değerleri ayırarak bir veya daha fazla kaynak grubu belirtir.|
|External_Stop_ResourceGroupNames | Bir veya daha fazla kaynak grubu belirtir ve durdurma eylemlerine yönelik bir virgül kullanarak değerleri birbirinden ayırarak.|
|Internal_AutomationAccountName | Otomasyon hesabının adını belirtir.|
|Internal_AutoSnooze_WebhookUri | Oto durdurma senaryosu için çağrılan Web kancası URI 'sini belirtir.|
|Internal_AzureSubscriptionId | Azure abonelik KIMLIĞINI belirtir.|
|Internal_ResourceGroupName | Otomasyon hesabı kaynak grubunun adını belirtir.|

Tüm senaryolarda, **External_Start_ResourceGroupNames**, **External_Stop_ResourceGroupNames**ve **External_ExcludeVMNames** değişkenleri, VM 'leri hedeflemek için, virgülle ayrılmış bir istisna sağlanması gerekir **AutoStop_CreateAlert_Parent**, **SequencedStartStop_Parent**ve **ScheduledStartStop_Parent** runbook 'ları için sanal makinelerin listesi. Diğer bir deyişle, sanal makinelerinizin başlatma ve durdurma eylemlerinin gerçekleşmesi için hedef kaynak gruplarında bulunmaları gerekir. Mantığı Azure ilkesine benzer şekilde çalışarak abonelik veya kaynak grubunu hedefleyebilir ve yeni oluşturulan VM 'Ler tarafından devralınan eylemlere sahip olabilirsiniz. Bu yaklaşım her VM için ayrı bir zamanlamanın korunmasını, sonra da her bir sanal makine için ayrı bir zamanlama olmasını önler.

### <a name="schedules"></a>Zamanlamalar

Aşağıdaki tabloda, Otomasyon hesabınızda oluşturulan varsayılan zamanlamaların her biri listelenmektedir. Bunları değiştirebilir veya kendi özel zamanlamalarınızı oluşturabilirsiniz. Varsayılan olarak, **Scheduled_StartVM** ve **Scheduled_StopVM**hariç tüm zamanlamalar devre dışı bırakılır.

Bu, çakışan zamanlama eylemleri oluşturabileceğinden tüm zamanlamaları etkinleştirmemelisiniz. Hangi iyileştirmelerin yerine getirmek istediğinizi ve uygun şekilde değişiklik yapmayı belirlemektir. Daha fazla açıklama için genel bakış bölümündeki örnek senaryolar bölümüne bakın.

|Zamanlama adı | Frequency | Açıklama|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | 8 saatte bir | AutoStop_CreateAlert_Parent runbook 'unu her 8 saatte bir çalıştırır, bu da Azure Automation değişkenlerinde External_Start_ResourceGroupNames, External_Stop_ResourceGroupNames ve External_ExcludeVMNames içindeki VM tabanlı değerleri de durduruyor. Alternatif olarak, VMList parametresini kullanarak, VM 'lerin virgülle ayrılmış bir listesini belirtebilirsiniz.|
|Scheduled_StopVM | Kullanıcı tanımlı, günlük | Scheduled_Parent runbook 'unu, belirtilen saatte her gün bir _stop_ parametresiyle çalıştırır. Varlık değişkenleri tarafından tanımlanan kuralları karşılayan tüm VM 'Leri otomatik olarak sonlandırır. **Zamanlanan-StartVM**ilgili zamanlamasını etkinleştirin.|
|Scheduled_StartVM | Kullanıcı tanımlı, günlük | , Belirtilen zamanda her gün bir _Başlangıç_ parametresiyle Scheduled_Parent runbook 'unu çalıştırır. , Uygun değişkenlerle tanımlanan kuralları karşılayan tüm VM 'Leri otomatik olarak başlatır. İlgili zamanlamayı, **Zamanlanmış-StopVM**'yi etkinleştirin.|
|Sıralı-StopVM | 1:00 (UTC), her Cuma | Sequenced_Parent runbook 'unu her Cuma günü belirtilen saatte bir _Durdur_ parametresiyle çalıştırır. Sıralı (artan), uygun değişkenler tarafından tanımlanan bir **SequenceStop** etiketine sahip tüm VM 'leri sonlandırır. Etiket değerleri ve varlık değişkenleri hakkında daha fazla bilgi için Runbook 'Lar bölümüne bakın. **Sıralı-StartVM**ilgili zamanlamasını etkinleştirin.|
|Sıralı-StartVM | 1:00 PM (UTC), her Pazartesi | Sequenced_Parent runbook 'unu her Pazartesi için belirtilen saatte bir _Başlangıç_ parametresiyle çalıştırır. Sıralı (azalan), uygun değişkenlerle tanımlanan **Sequencestart** etiketiyle tüm VM 'leri başlatır. Etiket değerleri ve varlık değişkenleri hakkında daha fazla bilgi için Runbook 'Lar bölümüne bakın. İlgili zamanlamayı, **sıralı-StopVM**'yi etkinleştirin.|

## <a name="azure-monitor-logs-records"></a>Azure Izleyici günlük kayıtları

Otomasyon Log Analytics çalışma alanında iki tür kayıt oluşturur: iş günlükleri ve iş akışları.

### <a name="job-logs"></a>İş günlükleri

|Özellik | Açıklama|
|----------|----------|
|Çağıran |  İşlemi başlatandır. Olası değerler, bir e-posta adresi veya zamanlanan işlere yönelik bir sistemdir.|
|Kategori | Veri türü sınıflandırması. Otomasyon için değer JobLogs olacaktır.|
|CorrelationId | Runbook işinin bağıntı KIMLIĞI olan GUID.|
|JobId | Runbook işinin KIMLIĞI olan GUID.|
|operationName | Azure’da gerçekleştirilen işlem türünü belirtir. Otomasyon için değer Iş olur.|
|resourceId | Azure’daki kaynak türünü belirtir. Otomasyon için değer, runbook ile ilişkilendirilmiş Otomasyon hesabı olacaktır.|
|ResourceGroup | Runbook işine ait kaynak grubunun adını belirtir.|
|ResourceProvider | Dağıtıp yönetebileceğiniz kaynakları sağlayan Azure hizmetini belirtir. Otomasyon için değer, Azure Otomasyonu olacaktır.|
|ResourceType | Azure’daki kaynak türünü belirtir. Otomasyon için değer, runbook ile ilişkilendirilmiş Otomasyon hesabı olacaktır.|
|resultType | Runbook işinin durumudur. Olası değerler şunlardır:<br>- Başlatıldı<br>- Durduruldu<br>- Askıya alındı<br>- Başarısız oldu<br>- Başarılı oldu|
|resultDescription | Runbook iş sonucu durumunu açıklar. Olası değerler şunlardır:<br>- İş başlatıldı<br>- İş Başarısız Oldu<br>- İş Tamamlandı|
|RunbookName | Runbook’un adını belirtir.|
|SourceSystem | Gönderilen verilere ilişkin kaynak sistemi belirtir. Otomasyon için, değer OpsManager ' dır|
|StreamType | Olay türünü belirtir. Olası değerler şunlardır:<br>- Ayrıntılı<br>- Çıktı<br>- Hata<br>- Uyarı|
|SubscriptionId | İşin abonelik kimliğini belirtir.
|Zaman | Runbook işinin yürütüldüğü tarih ve saat.|

### <a name="job-streams"></a>İş akışları

|Özellik | Açıklama|
|----------|----------|
|Çağıran |  İşlemi başlatandır. Olası değerler, bir e-posta adresi veya zamanlanan işlere yönelik bir sistemdir.|
|Kategori | Veri türü sınıflandırması. Otomasyon için değer JobStreams olacaktır.|
|JobId | Runbook işinin KIMLIĞI olan GUID.|
|operationName | Azure’da gerçekleştirilen işlem türünü belirtir. Otomasyon için değer Iş olur.|
|ResourceGroup | Runbook işine ait kaynak grubunun adını belirtir.|
|resourceId | Azure 'da kaynak KIMLIĞINI belirtir. Otomasyon için değer, runbook ile ilişkilendirilmiş Otomasyon hesabı olacaktır.|
|ResourceProvider | Dağıtıp yönetebileceğiniz kaynakları sağlayan Azure hizmetini belirtir. Otomasyon için değer, Azure Otomasyonu olacaktır.|
|ResourceType | Azure’daki kaynak türünü belirtir. Otomasyon için değer, runbook ile ilişkilendirilmiş Otomasyon hesabı olacaktır.|
|resultType | Runbook işinin, olayın oluşturulduğu andaki sonucu. Olası bir değer:<br>- Devam Ediyor|
|resultDescription | Runbook’un çıktı akışını içerir.|
|RunbookName | Runbook’un adı.|
|SourceSystem | Gönderilen verilere ilişkin kaynak sistemi belirtir. Otomasyon için, değer OpsManager ' dır.|
|StreamType | İş akışı türü. Olası değerler şunlardır:<br>-İlerleme durumu<br>- Çıktı<br>- Uyarı<br>- Hata<br>- Hata ayıklama<br>- Ayrıntılı|
|Zaman | Runbook işinin yürütüldüğü tarih ve saat.|

**Joblogs** veya **jobstreams**kategorisi kayıtlarını döndüren bir günlük araması gerçekleştirdiğinizde, arama tarafından döndürülen güncelleştirmeleri özetleyen bir kutucuk kümesi görüntüleyen **joblogs** veya **jobstreams** görünümünü seçebilirsiniz.

## <a name="sample-log-searches"></a>Örnek günlük aramaları

Aşağıdaki tabloda, bu çözüm tarafından toplanan iş kayıtlarına ilişkin örnek günlük aramaları sunulmaktadır.

|Sorgu | Açıklama|
|----------|----------|
|Başarıyla tamamlanan runbook ScheduledStartStop_Parent için işleri bulun | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "ScheduledStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" )  <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|Başarıyla tamamlanan runbook SequencedStartStop_Parent için işleri bulun | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "SequencedStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" ) <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|

## <a name="viewing-the-solution"></a>Çözümü görüntüleme

Çözüme erişmek için Otomasyon hesabınıza gidin, **ılgılı kaynaklar**altında **çalışma alanı** ' nı seçin. Log Analytics sayfasında, **genel**altında **çözümler** ' i seçin. **Çözümler** sayfasında, listeden **Başlat-Durdur-VM [çalışma alanı]** çözümünü seçin.

Çözümü seçtiğinizde, **Start-Stop-VM [çalışma alanı]** çözüm sayfası görüntülenir. Burada, **Startstopvm** kutucuğu gibi önemli ayrıntıları gözden geçirebilirsiniz. Log Analytics çalışma alanınızda olduğu gibi, bu kutucuk, başlatılan ve başarıyla tamamlanmış çözüm için Runbook işlerinin bir sayısını ve grafik temsilini görüntüler.

![Otomasyon Güncelleştirme Yönetimi çözüm sayfası](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)

Buradan, halka kutucuğuna tıklayarak iş kayıtlarının daha fazla analizini gerçekleştirebilirsiniz. Çözüm panosu iş geçmişi ve önceden tanımlanmış günlük arama sorguları gösterir. Arama sorgularınıza göre arama yapmak için Log Analytics gelişmiş portala geçiş yapın.

## <a name="configure-email-notifications"></a>E-posta bildirimlerini yapılandırma

Çözüm dağıtıldıktan sonra e-posta bildirimlerini değiştirmek için, dağıtım sırasında oluşturulan eylem grubunu değiştirin.  

> [!NOTE]
> Azure Kamu bulutundaki abonelikler, bu çözümün e-posta işlevlerini desteklemez.

Azure portal, Izleyici-> eylem grupları ' na gidin. **StartStop_VM_Notication**başlıklı eylem grubunu seçin.

![Otomasyon Güncelleştirme Yönetimi çözüm sayfası](media/automation-solution-vm-management/azure-monitor.png)

**StartStop_VM_Notification** **sayfasında Ayrıntılar ' ın**altındaki **Ayrıntıları Düzenle** ' ye tıklayın. Bu, **e-posta/SMS/Push/ses** sayfasını açar. E-posta adresini güncelleştirip **Tamam** ' a tıklayarak değişikliklerinizi kaydedin.

![Otomasyon Güncelleştirme Yönetimi çözüm sayfası](media/automation-solution-vm-management/change-email.png)

Alternatif olarak, eylem grubu hakkında daha fazla bilgi edinmek için eylem grubuna ek eylemler ekleyebilirsiniz, eylem [grupları](../azure-monitor/platform/action-groups.md) konusuna bakın

Aşağıda, çözüm sanal makineleri kapattığında gönderilen örnek bir e-posta verilmiştir.

![Otomasyon Güncelleştirme Yönetimi çözüm sayfası](media/automation-solution-vm-management/email.png)

## <a name="add-exclude-vms"></a>VM 'Leri ekle/çıkar

Çözüm, çözüm tarafından hedeflenen VM 'Leri ekleme veya makineleri çözümden özel olarak hariç tutma yeteneği sağlar.

### <a name="add-a-vm"></a>VM ekleme

Çalışan bir sanal makinenin, çalıştırıldığında Başlat/Durdur çözümüne eklendiğinden emin olmak için kullanabileceğiniz birkaç seçenek vardır.

* Çözümün üst [runbook](#runbooks) 'larının her biri bir **vmlist** parametresine sahiptir. Durumunuza uygun üst runbook 'u zamanlarken ve bu VM 'Ler çözüm çalışırken dahil edilse bu parametreye, virgülle ayrılmış bir VM adları listesi geçirebilirsiniz.

* Birden çok sanal makine seçmek için, **External_Start_ResourceGroupNames** ve **External_Stop_ResourceGroupNames** ' yi başlatmak veya durdurmak istediğiniz VM 'leri içeren kaynak grubu adlarıyla ayarlayın. Bu değeri, çözümün abonelikteki tüm kaynak grupları üzerinde çalışmasını sağlamak için `*`olarak da ayarlayabilirsiniz.

### <a name="exclude-a-vm"></a>VM dışlama

Bir VM 'yi çözümden dışlamak için, **External_ExcludeVMNames** değişkenine ekleyebilirsiniz. Bu değişken, Başlat/Durdur çözümünün dışında tutulacak belirli VM 'lerin virgülle ayrılmış listesidir. Bu liste 140 VM 'Lerle sınırlıdır. Bu virgülle ayrılmış listeye 140 'den fazla VM eklerseniz, hariç tutulacak şekilde ayarlanan VM 'Ler istenmeden başlatılabilir veya durdurulabilir.

## <a name="modify-the-startup-and-shutdown-schedules"></a>Başlatma ve kapatılma zamanlamalarını değiştirme

Bu çözümdeki başlangıç ve kapalı zamanlamaların yönetilmesi, [Azure Otomasyonu 'nda runbook zamanlamaya](automation-schedules.md)göre belirtilen adımların aynısını izler. VM 'Leri başlatmak ve durdurmak için ayrı bir zamanlama olması gerekir.

Yalnızca belirli bir zamanda VM 'Leri durdurmak için çözümü yapılandırma desteklenir. Bu senaryoda, yalnızca bir **Dur** zamanlaması oluşturmanız ve buna karşılık gelen bir **başlama** zamanlaması yapmanız gerekir. Bunu yapmanız için gerekenler:

1. **External_Stop_ResourceGroupNames** değişkeninde kapatılacak VM 'ler için kaynak grupları eklendiğinden emin olun.
2. VM 'Leri kapatmak istediğiniz zaman için kendi zamanlamanızı oluşturun.
3. **ScheduledStartStop_Parent** runbook 'a gidin ve **zamanla**' ya tıklayın. Bu, önceki adımda oluşturduğunuz zamanlamayı seçmenizi sağlar.
4. **Parametreler ' i seçin ve ayarları çalıştırın** ve eylem parametresini "Durdur" olarak ayarlayın.
5. Değişikliklerinizi kaydetmek için **Tamam**’a tıklayın.

## <a name="update-the-solution"></a>Çözümü güncelleştirme

Bu çözümün önceki bir sürümünü dağıttıysanız, önce güncelleştirilmiş bir yayını dağıtabilmeniz için önce bu sürümü hesabınızdan silmeniz gerekir. [Çözümü kaldırmak](#remove-the-solution) için adımları izleyin ve sonra [çözümü dağıtmak](#deploy-the-solution)için yukarıdaki adımları uygulayın.

## <a name="remove-the-solution"></a>Çözümü kaldırma

Çözümü artık kullanmaya gerek kalmamaya karar verirseniz Otomasyon hesabından silebilirsiniz. Çözümün silinmesi yalnızca runbook 'ları kaldırır. Çözüm eklendiğinde oluşturulan zamanlamaları veya değişkenleri silmez. Bu varlıklar, bunları diğer runbook 'larla kullanmıyorsanız el ile silmeniz gerekir.

Çözümü silmek için aşağıdaki adımları gerçekleştirin:

1. Otomasyon hesabınızdan, **ilgili kaynaklar**altında, **bağlantılı çalışma alanı**' nı seçin.
1. **Çalışma alanına git**' i seçin.
1. **Genel**altında **çözümler**' i seçin. 
1. **Çözümler** sayfasında, **Başlat-Durdur-VM [çalışma alanı]** çözümünü seçin. **Vmmanagementsolution [çalışma alanı]** sayfasında, menüden **Sil**' i seçin.<br><br> ![VM yönetimi çözümünü Sil](media/automation-solution-vm-management/vm-management-solution-delete.png)
1. **Çözümü Sil** penceresinde, çözümü silmek istediğinizi onaylayın.
1. Bilgiler doğrulanırken ve çözüm silinirken, menünün **Bildirimler** altında ilerleme durumunu izleyebilirsiniz. Çözümü kaldırma işlemi başladıktan sonra **çözümler** sayfasına dönersiniz.

Otomasyon hesabı ve Log Analytics çalışma alanı bu işlemin bir parçası olarak silinmez. Log Analytics çalışma alanını sürdürmek istemiyorsanız, el ile silmeniz gerekir. Bu, Azure portal yapılabilir:

1. Azure portal giriş ekranından **Log Analytics çalışma alanları**' nı seçin.
1. **Log Analytics çalışma alanları** sayfasında, çalışma alanını seçin.
1. Çalışma alanı ayarları sayfasındaki menüden **Sil** ' i seçin.

Azure Otomasyonu hesap bileşenlerini sürdürmek istemiyorsanız, her birini el ile silebilirsiniz. Çözüm tarafından oluşturulan runbook 'lar, değişkenler ve zamanlamalar listesi için bkz. [çözüm bileşenleri](#solution-components).

## <a name="next-steps"></a>Sonraki adımlar

- Farklı arama sorguları oluşturma ve Azure Izleyici günlükleriyle Otomasyon iş günlüklerini İnceleme hakkında daha fazla bilgi edinmek için bkz. [Azure izleyici günlüklerinde günlük aramaları](../log-analytics/log-analytics-log-searches.md).
- Runbook yürütme, runbook işlerini izleme ve diğer teknik ayrıntılar hakkında daha fazla bilgi edinmek için bkz. [Runbook işi izleme](automation-runbook-execution.md).
- Azure Izleyici günlükleri ve veri toplama kaynakları hakkında daha fazla bilgi edinmek için bkz. Azure [izleyici günlüklerine Azure depolama verilerini toplama genel bakış](../azure-monitor/platform/collect-azure-metrics-logs.md).
