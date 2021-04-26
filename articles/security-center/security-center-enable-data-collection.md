---
title: Azure Güvenlik Merkezi için aracıları otomatik dağıtma | Microsoft Docs
description: Bu makalede, Azure Güvenlik Merkezi tarafından kullanılan Log Analytics aracısının ve diğer aracıların ve uzantıların otomatik olarak sağlanması nasıl ayarlanacağı açıklanır.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: quickstart
ms.date: 03/04/2021
ms.author: memildin
ms.openlocfilehash: 17f3440df4fa88995f2148680aba926207a0e46b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102561271"
---
# <a name="configure-auto-provisioning-for-agents-and-extensions-from-azure-security-center"></a>Azure Güvenlik Merkezi 'nden aracılar ve uzantılar için otomatik sağlamayı yapılandırma

Azure Güvenlik Merkezi, bu kaynak için ilgili aracıyı veya uzantıları ve etkinleştirdiğiniz veri koleksiyonu türünü kullanarak kaynaklarınızdan veri toplar. Kaynaklarınızın güvenlik merkezi tarafından kullanılan gerekli aracıları ve uzantılara sahip olduğundan emin olmak için aşağıdaki yordamları kullanın.

## <a name="prerequisites"></a>Önkoşullar
Güvenlik Merkezi ile çalışmaya başlamak için Microsoft Azure aboneliğinizin olması gerekir. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/pricing/free-trial/)için kaydolabilirsiniz.

## <a name="availability"></a>Kullanılabilirlik

| Görünüş                  | Ayrıntılar                                                                                                                                                                                                                      |
|-------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Yayın durumu:          | **Özellik**: otomatik sağlama genel kullanıma sunuldu (GA)<br>**Aracı ve uzantılar**: Azure VM 'ler için log ANALYTICS Aracısı GA, Microsoft bağımlılık Aracısı önizleme aşamasındadır, Kubernetes Için Ilke eklentisi GA                |
| Fiyat                | Ücretsiz                                                                                                                                                                                                                         |
| Desteklenen hedefler: | ![Yes](./media/icons/yes-icon.png) Azure makineleri<br>![No](./media/icons/no-icon.png) Azure yay makineleri<br>![No](./media/icons/no-icon.png) Kubernetes düğümleri<br>![No](./media/icons/no-icon.png) Sanal Makine Ölçek Kümeleri |
| Larının                 | ![Yes](./media/icons/yes-icon.png) Ticari bulutlar<br>![Yes](./media/icons/yes-icon.png) US Gov, Çin gov, diğer gov                                                                                                      |
|                         |                                                                                                                                                                                                                              |

## <a name="how-does-security-center-collect-data"></a>Güvenlik Merkezi verileri nasıl toplar?

Güvenlik Merkezi, Azure sanal makinelerinizden (VM), sanal makine ölçek kümelerinden, IaaS kapsayıcılarından ve Azure olmayan (Şirket içi) makineler, güvenlik açıklarını ve tehditleri izlemek için veri toplar. 

Eksik güncelleştirmelere görünürlük sağlamak için veri toplama gerekir, yanlış yapılandırılmış işletim sistemi güvenlik ayarları, uç nokta koruma durumu ve sistem durumu ve tehdit koruması. Veri toplama yalnızca VM 'Ler, sanal makine ölçek kümeleri, IaaS kapsayıcıları ve Azure dışı bilgisayarlar gibi işlem kaynakları için gereklidir. 

Aracılar sağlamasanız bile Azure Güvenlik Merkezi 'nden faydalanabilirsiniz. Bununla birlikte, sınırlı güvenliğe sahip olacaksınız ve yukarıda listelenen yetenekler desteklenmez.  

Şu kullanılarak toplanan veriler:

- Makineden güvenlikle ilgili çeşitli yapılandırma ve olay günlüklerini okuyan ve analiz için verileri çalışma alanınıza kopyalayan **Log Analytics Aracısı**. Bu verilere örnek olarak şunlar verilebilir: işletim sistemi türü ve sürümü, işletim sistemi günlükleri (Windows olay günlükleri), çalışan süreçler, makine adı, IP adresleri ve oturum açmış kullanıcı.
- [Kubernetes Için Azure Ilke eklentisi](../governance/policy/concepts/policy-for-kubernetes.md)gibi **güvenlik uzantıları**, özel kaynak türleriyle ilgili olarak güvenlik merkezi 'ne veri de sunabilir.

> [!TIP]
> Güvenlik Merkezi artmıştır, izlenebilecek kaynak türleri de artmıştır. Uzantı sayısı da artmıştır. Otomatik sağlama, Azure Ilkesinin yeteneklerini kullanarak ek kaynak türlerini desteklemek için genişletilmiştir.

## <a name="why-use-auto-provisioning"></a>Otomatik sağlama neden kullanılmalıdır?
Bu sayfada açıklanan aracıların ve genişletmeler el *ile yüklenebilir (* bkz. [Log Analytics aracısının el ile yüklenmesi](#manual-agent)). Ancak, **Otomatik sağlama** , tüm desteklenen kaynaklar için daha hızlı güvenlik kapsamı sağlamak üzere mevcut ve yeni makinelere tüm gerekli aracıları ve uzantıları yükleyerek yönetim yükünü azaltır. 

Otomatik sağlamayı etkinleştirmeniz önerilir, ancak varsayılan olarak devre dışıdır.

## <a name="how-does-auto-provisioning-work"></a>Otomatik sağlama nasıl çalışır?
Güvenlik Merkezi 'nin otomatik sağlama ayarları her desteklenen uzantı türü için bir geçiş yapar. Bir uzantının otomatik olarak sağlanmasını etkinleştirdiğinizde uygun **dağıtım yok** ilkesi atanır. Bu ilke türü, uzantının, bu türdeki tüm mevcut ve gelecekteki kaynaklarda sağlanmış olmasını sağlar.

> [!TIP]
> Azure [ilke efektlerini anlama](../governance/policy/concepts/effects.md)bölümünde yoksa dağıtım dahil olmak üzere Azure ilke etkileri hakkında daha fazla bilgi edinin.


## <a name="enable-auto-provisioning-of-the-log-analytics-agent-and-extensions"></a>Log Analytics Aracısı ve uzantılarının otomatik sağlamasını etkinleştir <a name="auto-provision-mma"></a>

Log Analytics Aracısı için otomatik sağlama açık olduğunda, güvenlik merkezi aracıyı desteklenen tüm Azure VM 'Lere ve oluşturulan tüm yeni makinelere dağıtır. Desteklenen platformların listesi için bkz. [Azure Güvenlik Merkezi 'Nde desteklenen platformlar](security-center-os-coverage.md).

Log Analytics aracısının otomatik sağlamasını etkinleştirmek için:

1. Güvenlik Merkezi 'nin menüsünde **fiyatlandırma & ayarları**' nı seçin.
1. Uygun aboneliği seçin.
1. **Otomatik sağlama** sayfasında, Log Analytics aracısının durumunu **Açık** olarak ayarlayın.

    :::image type="content" source="./media/security-center-enable-data-collection/enable-automatic-provisioning.png" alt-text="Log Analytics aracısının otomatik olarak sağlanması etkinleştiriliyor":::

1. Yapılandırma seçenekleri bölmesinden, kullanılacak çalışma alanını tanımlayın.

    :::image type="content" source="./media/security-center-enable-data-collection/log-analytics-agent-deploy-options.png" alt-text="Aracıları sanal makinelere Log Analytics otomatik sağlama için yapılandırma seçenekleri" lightbox="./media/security-center-enable-data-collection/log-analytics-agent-deploy-options.png":::

    - **Azure VM 'Lerini Güvenlik Merkezi tarafından oluşturulan varsayılan çalışma alanına bağlama** -Güvenlik Merkezi, aynı coğrafi konum içinde yeni bir kaynak grubu ve varsayılan çalışma alanı oluşturur ve aracıyı bu çalışma alanına bağlar. Bir abonelik birden çok geoloden VM içeriyorsa, Güvenlik Merkezi veri gizliliği gereksinimleriyle uyumluluğu sağlamak için birden çok çalışma alanı oluşturur.

        Çalışma alanı ve kaynak grubu için adlandırma kuralı: 
        - Çalışma Alanı: DefaultWorkspace-[abonelik-kimliği]-[bölge] 
        - Kaynak grubu: DefaultResourceGroup-[coğrafi] 

        Güvenlik Merkezi, abonelik için ayarlanan fiyatlandırma katmanı uyarınca çalışma alanında bir güvenlik merkezi çözümünü otomatik olarak sunar. 

        > [!TIP]
        > Varsayılan çalışma alanlarıyla ilgili sorular için bkz.:
        >
        > - [Güvenlik Merkezi tarafından oluşturulan çalışma alanlarında Azure Izleyici günlükleri için faturalandırılırım mı?](faq-data-collection-agents.md#am-i-billed-for-azure-monitor-logs-on-the-workspaces-created-by-security-center)
        > - [Varsayılan Log Analytics çalışma alanı nerede oluşturulur?](faq-data-collection-agents.md#where-is-the-default-log-analytics-workspace-created)
        > - [Güvenlik Merkezi tarafından oluşturulan varsayılan çalışma alanlarını silebilir miyim?](faq-data-collection-agents.md#can-i-delete-the-default-workspaces-created-by-security-center)

    - **Azure VM 'lerini farklı bir çalışma alanına bağlama** -açılan listeden, toplanan verileri depolamak için çalışma alanını seçin. Açılır liste tüm aboneliklerinizde tüm çalışma alanlarını içerir. Bu seçeneği, farklı aboneliklerde çalışan sanal makinelerden veri toplamak ve tüm seçtiğiniz çalışma alanınızda depolamak için kullanabilirsiniz.  

        Zaten bir Log Analytics çalışma alanınız varsa, aynı çalışma alanını kullanmak isteyebilirsiniz (çalışma alanında okuma ve yazma izinleri gerekir). Bu seçenek, kuruluşunuzda merkezi bir çalışma alanı kullanıyorsanız ve onu güvenlik verileri toplama için kullanmak istiyorsanız yararlıdır. [Azure izleyici 'de günlük verilerine ve çalışma alanlarına erişimi yönetme](../azure-monitor/logs/manage-access.md)konusunda daha fazla bilgi edinin.

        Seçtiğiniz çalışma alanınızda zaten "güvenlik" veya "SecurityCenterFree" çözümü etkinse, fiyatlandırma otomatik olarak ayarlanır. Aksi takdirde, çalışma alanına bir güvenlik merkezi çözümü yüklersiniz:

        1. Güvenlik Merkezi 'nin menüsünde, **fiyatlandırma & ayarları**' nı açın.
        1. Aracıları bağlanacağınız çalışma alanını seçin.
        1. **Azure Defender** veya **Azure Defender kapalı**' yı seçin.

1. **Windows güvenlik olayları** yapılandırmasından, depolanacak Ham olay verisi miktarını seçin:
    - **Hiçbiri** – güvenlik olay depolamayı devre dışı bırakın. Bu varsayılan ayardır.
    - **Minimum** : olay birimini en aza indirmek istediğinizde küçük bir olay kümesi.
    - **Ortak** – çoğu müşteriyi karşılayan ve tam denetim izi sağlayan bir olay kümesidir.
    - **Tüm olaylar – tüm** olayların depolandığından emin olmak isteyen müşteriler için.

    > [!TIP]
    > Bu seçenekleri çalışma alanı düzeyinde ayarlamak için, bkz. [çalışma alanı düzeyinde güvenlik olay seçeneğini ayarlama](#setting-the-security-event-option-at-the-workspace-level).
    > 
    > Bu seçenekler hakkında daha fazla bilgi için, bkz. [Log Analytics Aracısı Için Windows Güvenliği olay seçenekleri](#data-collection-tier).

1. Yapılandırma bölmesinde **Uygula** ' yı seçin.

1. Log Analytics Aracısı dışında bir uzantının otomatik sağlanmasını etkinleştirmek için: 

    1. Microsoft bağımlılık Aracısı için otomatik sağlamayı etkinleştirirseniz Log Analytics aracısının otomatik dağıtım olarak ayarlandığından emin olun.
    1. İlgili uzantı için durumu **Açık** olarak değiştirin.

        :::image type="content" source="./media/security-center-enable-data-collection/toggle-kubernetes-add-on.png" alt-text="K8s İlkesi eklentisi için otomatik sağlamayı etkinleştirmek üzere değiştirin":::

    1. **Kaydet**’i seçin. Azure ilkesi atanır ve bir düzeltme görevi oluşturulur.

        |Dahili numara  |İlke  |
        |---------|---------|
        |Kubernetes için İlke Eklentisi|[Azure Kubernetes hizmet kümelerine Azure Ilkesi eklentisi dağıtma](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fa8eff44f-8c92-45c3-a3fb-9880802d67a7)|
        |Microsoft bağımlılık Aracısı (Önizleme) (Windows VM 'Ler)|[Windows sanal makineleri için bağımlılık aracısını dağıtma](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f1c210e94-a481-4beb-95fa-1571b434fb04)         |
        |Microsoft bağımlılık Aracısı (Önizleme) (Linux VM 'Leri)|[Linux sanal makineleri için bağımlılık aracısını dağıtma](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f4da21710-ce6f-4e06-8cdb-5cc4c93ffbee)|
        |||

1. **Kaydet**’i seçin. Bir çalışma alanının sağlanması gerekiyorsa, aracı yüklemesi 25 dakikaya kadar sürebilir.

1. Daha önce varsayılan bir çalışma alanına bağlı olan izlenen VM 'Leri yeniden yapılandırmak isteyip istemediğiniz sorulur:

    :::image type="content" source="./media/security-center-enable-data-collection/reconfigure-monitored-vm.png" alt-text="İzlenen VM 'Leri yeniden yapılandırma seçeneklerini gözden geçirin":::

    - **Hayır** -yeni çalışma alanı ayarlarınız yalnızca Log Analytics Aracısı yüklü olmayan yeni bulunan VM 'lere uygulanır.
    - **Evet** -yeni çalışma alanı ayarlarınız tüm VM 'ler için geçerli olacak ve şu anda bir güvenlik merkezi tarafından oluşturulan çalışma alanına bağlı olan tüm VM 'ler yeni hedef çalışma alanına yeniden bağlanacak.

   > [!NOTE]
   > **Evet**' i seçerseniz, tüm VM 'ler yeni hedef çalışma alanına yeniden bağlanana kadar Güvenlik Merkezi tarafından oluşturulan çalışma alanlarını silmeyin. Çalışma alanı çok erken silinirse bu işlem başarısız olur.


## <a name="windows-security-event-options-for-the-log-analytics-agent"></a>Log Analytics Aracısı için Windows Güvenliği olay seçenekleri <a name="data-collection-tier"></a> 

Azure Güvenlik Merkezi 'nde bir veri toplama katmanının seçilmesi yalnızca Log Analytics çalışma alanınızdaki güvenlik olaylarının *depolanmasını* etkiler. Log Analytics Aracısı, çalışma alanınızda depolamayı seçtiğiniz güvenlik olaylarının düzeyinden bağımsız olarak güvenlik merkezi 'nin tehdit koruması için gerekli güvenlik olaylarını toplayıp analiz eder. Güvenlik olaylarının depolanmasını seçmek, çalışma alanınızdaki bu olayların araştırmasını, aranmasını ve denetlenmesini sağlar.

### <a name="requirements"></a>Gereksinimler 
Windows Güvenlik olay verilerini depolamak için Azure Defender gereklidir. [Azure Defender hakkında daha fazla bilgi edinin](azure-defender.md).

Log Analytics veri depolama, veri depolama için ek ücretler gerektirebilir. Daha fazla bilgi edinmek için bkz. [fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/security-center/).

### <a name="information-for-azure-sentinel-users"></a>Azure Sentinel kullanıcıları için bilgiler 
Azure Sentinel kullanıcıları: tek bir çalışma alanı bağlamı içindeki güvenlik olayları koleksiyonunun Azure Güvenlik Merkezi 'nden veya Azure Sentinel 'ten yapılandırılabileceğini, ancak her ikisiyle de kullanılamayacağını unutmayın. Azure Güvenlik Merkezi 'nde zaten uyarı alan bir çalışma alanına Azure Sentinel eklemek istiyorsanız ve güvenlik olaylarını toplamak üzere ayarlanırsa, iki seçeneğiniz vardır:
- Azure Güvenlik Merkezi 'nde güvenlik olayları koleksiyonunu olduğu gibi bırakın. Bu olayları Azure Sentinel 'de ve Azure Defender 'da sorgulayabilir ve analiz edebilirsiniz. Bununla birlikte, bağlayıcının bağlantı durumunu izleyebilmeyecek veya Azure Sentinel 'de yapılandırmasını değiştiremeyeceksiniz. Bu sizin için önemliyse ikinci seçeneği göz önünde bulundurun.
- Azure Güvenlik Merkezi 'nde güvenlik olayları koleksiyonunu devre dışı bırakın (Log Analytics aracınızın yapılandırmasında **Windows Güvenlik olaylarını** **none** olarak ayarlayarak). Ardından Azure Sentinel 'de güvenlik olayları bağlayıcısını ekleyin. İlk seçenekte olduğu gibi, hem Azure Sentinel hem de Azure Defender/ASC içindeki olayları sorgulayabilir ve çözümleyebilirsiniz, ancak artık bağlayıcının bağlantı durumunu izleyebilir ya da yalnızca Azure Sentinel 'de ve yapılandırmasını değiştirebilirsiniz.

### <a name="what-event-types-are-stored-for-common-and-minimal"></a>"Common" ve "minimal" için hangi olay türleri depolanır?
Bu kümeler tipik senaryolara yönelik olarak tasarlanmıştır. Uygulamadan önce ihtiyaçlarınıza uygun olanı değerlendirdiğinizden emin olun.

**Ortak** ve **En düşük** seçeneklere yönelik olayları öğrenmek için, her bir olayın filtrelenmemiş sıklığı ve kullanımları hakkında bilgi edinmek üzere müşteriler ve sektör standartları ile çalıştık. Bu işlemde aşağıdaki yönergeleri kullandık:

- **En az** -bu küme, yalnızca başarılı bir ihlal ve çok düşük bir birimi olan önemli olayları gösterebilen olayları kapsadığından emin olun. Örneğin, bu küme kullanıcı başarılı ve başarısız oturum açma bilgilerini (olay kimlikleri 4624, 4625) içerir, ancak bu, denetim için önemli olan oturum açma bilgilerini içermez ancak algılama için anlamlı ve görece yüksek hacimdir. Bu küme veri hacminin çoğu, oturum açma olayları ve işlem oluşturma olayıdır (olay KIMLIĞI 4688).
- **Ortak** -bu küme içinde tam bir kullanıcı denetim izi sağlayın. Örneğin, bu küme hem Kullanıcı oturum açmaları hem de Kullanıcı imzası içerir (olay KIMLIĞI 4634). Güvenlik grubu değişiklikleri, anahtar etki alanı denetleyicisi Kerberos işlemleri ve sektör kurumları tarafından önerilen diğer olaylar gibi denetim eylemlerini de ekledik.

Çok düşük hacimmiş olan olaylar, tüm olaylar üzerinde seçim yapmak için kullanılan ana işlem, birimi azaltmaktır ve belirli olayları filtreleyememektedir.

Her bir küme için güvenlik ve uygulama dolabı olay kimliklerinin tamamen bir dökümü aşağıda verilmiştir:

| Veri katmanı | Toplanan olay göstergeleri |
| --- | --- |
| En az | 1102, 4624, 4625, 4657, 4663, 4688, 4700, 4702, 4719, 4720, 4722, 4723, 4724, 4727, 4728, 4732, 4735, 4737, 4739, 4740, 4754, 4755, |
| | 4756, 4767, 4799, 4825, 4946, 4948, 4956, 5024, 5033, 8001, 8002, 8003, 8004, 8005, 8006, 8007, 8222 |
| Common | 1, 299, 300, 324, 340, 403, 404, 410, 411, 412, 413, 431, 500, 501, 1100, 1102, 1107, 1108, 4608, 4610, 4611, 4614, 4622, |
| |  4624, 4625, 4634, 4647, 4648, 4649, 4657, 4661, 4662, 4663, 4665, 4666, 4667, 4688, 4670, 4672, 4673, 4674, 4675, 4689, 4697, |
| | 4700, 4702, 4704, 4705, 4716, 4717, 4718, 4719, 4720, 4722, 4723, 4724, 4725, 4726, 4727, 4728, 4729, 4733, 4732, 4735, 4737, |
| | 4738, 4739, 4740, 4742, 4744, 4745, 4746, 4750, 4751, 4752, 4754, 4755, 4756, 4757, 4760, 4761, 4762, 4764, 4767, 4768, 4771, |
| | 4774, 4778, 4779, 4781, 4793, 4797, 4798, 4799, 4800, 4801, 4802, 4803, 4825, 4826, 4870, 4886, 4887, 4888, 4893, 4898, 4902, |
| | 4904, 4905, 4907, 4931, 4932, 4933, 4946, 4948, 4956, 4985, 5024, 5033, 5059, 5136, 5137, 5140, 5145, 5632, 6144, 6145, 6272, |
| | 6273, 6278, 6416, 6423, 6424, 8001, 8002, 8003, 8004, 8005, 8006, 8007, 8222, 26401, 30004 |

> [!NOTE]
> - Grup ilkesi nesnesi (GPO) kullanıyorsanız, denetim ilkeleri Işlem oluşturma olayı 4688 ' nı ve olay 4688 ' nin içindeki *commandLine* alanını etkinleştirmeniz önerilir. Işlem oluşturma olayı 4688 hakkında daha fazla bilgi için Güvenlik Merkezi 'nin [SSS](faq-data-collection-agents.md#what-happens-when-data-collection-is-enabled)bölümüne bakın. Bu denetim ilkeleri hakkında daha fazla bilgi için bkz. [Denetim Ilkesi önerileri](/windows-server/identity/ad-ds/plan/security-best-practices/audit-policy-recommendations).
> -  [Uyarlamalı uygulama denetimleri](security-center-adaptive-application.md)için veri toplamayı etkinleştirmek üzere, Güvenlik Merkezi, tüm uygulamalara izin vermek için denetim modunda yerel bir AppLocker ilkesi yapılandırır. Bu, AppLocker 'ın Güvenlik Merkezi tarafından toplanan ve yararlanılabilir olayları oluşturmasına neden olur. Bu ilkenin zaten yapılandırılmış bir AppLocker ilkesinin bulunduğu makinelerde yapılandırılmaması gerektiğini unutmayın. 
> - Windows Filtre Platformu [olay kimliği 5156](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=5156)' i toplamak Için, [Denetim filtreleme platformu bağlantısını](/windows/security/threat-protection/auditing/audit-filtering-platform-connection) etkinleştirmeniz gerekir (auditpol/set/subcategory: "Platform bağlantısı filtreleniyor"/Success: etkinleştir)
>

### <a name="setting-the-security-event-option-at-the-workspace-level"></a>Çalışma alanı düzeyinde güvenlik olayı seçeneği ayarlanıyor

Çalışma alanı düzeyinde depolanacak güvenlik olay verilerinin düzeyini tanımlayabilirsiniz.

1. Güvenlik Merkezi 'nin Azure portal menüsünde **fiyatlandırma & ayarları**' nı seçin.
1. İlgili çalışma alanını seçin. Bir çalışma alanı için yalnızca veri toplama olayları, bu sayfada açıklanan Windows Güvenlik olaylardır.

    :::image type="content" source="media/security-center-enable-data-collection/event-collection-workspace.png" alt-text="Çalışma alanında depolanacak güvenlik olay verilerini ayarlama":::

1. Depolanacak Ham olay verilerinin miktarını seçin ve **Kaydet**' i seçin.

## <a name="manual-agent-provisioning"></a>El ile aracı sağlama <a name="manual-agent"></a>
 
Log Analytics aracısını el ile yüklemek için:

1. Otomatik sağlamayı devre dışı bırakın.

1. İsteğe bağlı olarak, bir çalışma alanı oluşturun.

1. Log Analytics aracısını yüklemekte olduğunuz çalışma alanında Azure Defender 'ı etkinleştirin:

    1. Güvenlik Merkezi 'nin menüsünde **fiyatlandırma & ayarları**' nı seçin.

    1. Aracıyı yüklemekte olduğunuz çalışma alanını ayarlayın. Çalışma alanının, güvenlik merkezi 'nde kullandığınız abonelikte olduğundan ve çalışma alanı için okuma/yazma izinlerine sahip olduğunuzdan emin olun.

    1. **Azure Defender**' ı seçin ve **kaydedin**.

       >[!NOTE]
       >Çalışma alanında zaten bir **güvenlik** veya **securitycenterücretsiz** çözümü etkinse, fiyatlandırma otomatik olarak ayarlanır. 

1. Kaynak Yöneticisi şablonu kullanarak aracıları yeni VM 'Lere dağıtmak için, Log Analytics Aracısı 'nı yüklemek için:

   - [Windows için Log Analytics aracısını yükler](../virtual-machines/extensions/oms-windows.md)
   - [Linux için Log Analytics aracısını yükler](../virtual-machines/extensions/oms-linux.md)

1. Mevcut sanal [makinelerinizde aracılar dağıtmak Için Azure sanal makineleri hakkında veri toplama](../azure-monitor/vm/quick-collect-azurevm.md) ( **olay topla ve performans verileri** isteğe bağlı) bölümündeki yönergeleri izleyin.

1. Aracıları dağıtmak üzere PowerShell 'i kullanmak için, sanal makineler belgelerindeki yönergeleri kullanın:

    - [Windows makineleri için](../virtual-machines/extensions/oms-windows.md?toc=%2fazure%2fazure-monitor%2ftoc.json#powershell-deployment)
    - [Linux makineleri için](../virtual-machines/extensions/oms-linux.md?toc=%2fazure%2fazure-monitor%2ftoc.json#azure-cli-deployment)

> [!TIP]
> PowerShell kullanarak güvenlik merkezi 'ni ekleme hakkında yönergeler için bkz. [PowerShell kullanarak Azure Güvenlik Merkezi 'Ni otomatik hale](security-center-powershell-onboarding.md)getirme.


## <a name="automatic-provisioning-in-cases-of-a-pre-existing-agent-installation"></a>Önceden var olan bir aracı yüklemesi durumlarında otomatik sağlama <a name="preexisting"></a> 

Aşağıdaki kullanım örnekleri, zaten bir aracı veya uzantının yüklü olduğu durumlarda otomatik sağlama 'nın nasıl çalıştığını belirtir. 

- **Log Analytics Aracısı makineye yüklendi, ancak uzantı olarak değil (doğrudan aracı)** -Log Analytics ARACıSı doğrudan VM 'ye (Azure uzantısı olarak değil) yüklenirse, güvenlik merkezi Log Analytics aracı uzantısını yükler ve Log Analytics aracısını en son sürüme yükseltebilirler.
Yüklü aracı zaten yapılandırılmış çalışma alanına (ler) rapor etmeye devam eder ve ek olarak güvenlik merkezi 'nde yapılandırılan çalışma alanına rapor eder (Windows makinelerde çoklu barındırma desteklenir).
Yapılandırılmış çalışma alanı bir kullanıcı çalışma alanı ise (Güvenlik Merkezi 'nin varsayılan çalışma alanı değil), bu çalışma alanına raporlama yapan VM 'Ler ve bilgisayarlardan gelen olayları işlemeye başlamak için Güvenlik Merkezi 'nin "güvenlik" veya "SecurityCenterFree" çözümünü yüklemeniz gerekir.

    Linux makineler için, aracı çoklu barındırma henüz desteklenmiyor. bu nedenle, mevcut bir aracı yüklemesi algılanırsa, otomatik sağlama gerçekleşmez ve makinenin yapılandırması değiştirilmez.

    Aboneliklerdeki mevcut makineler için, eklendi Mart 2019 ' den önce güvenlik merkezi 'nde, var olan bir aracı algılandığında, Log Analytics Aracısı uzantısı yüklenmez ve makine etkilenmeyecektir. Bu makineler için, bu makinelerdeki aracı yükleme sorunlarını gidermek için "makinelerinizdeki izleme Aracısı sistem durumu sorunlarını çözün" önerisine bakın.
  
- **System Center Operations Manager Aracısı makineye yüklendi** -güvenlik merkezi, Log Analytics aracı uzantısını mevcut Operations Manager yan yana yükleyecek. Mevcut Operations Manager Aracısı normal olarak Operations Manager sunucusuna rapor etmeye devam edecektir. Operations Manager Aracısı ve Log Analytics Aracısı, bu işlem sırasında en son sürüme güncellenecek ortak çalışma zamanı kitaplıklarını paylaşır. Operations Manager Agent 2012 sürümü **yüklüyse, otomatik sağlamayı etkinleştirmeyin.**

- **Önceden var olan BIR VM uzantısı var**:
    - Izleme Aracısı bir uzantı olarak yüklendiğinde, uzantı yapılandırması raporlamaya yalnızca tek bir çalışma alanına izin verir. Güvenlik Merkezi, mevcut kullanıcı çalışma alanları bağlantılarını geçersiz kılmaz. Güvenlik Merkezi, "güvenlik" veya "SecurityCenterFree" çözümünün yüklenmiş olduğu bir çalışma alanındaki VM 'den güvenlik verilerini depolar. Güvenlik Merkezi bu işlemdeki en son sürüme uzantı sürümünü yükseltebilir.
    - Var olan uzantının hangi çalışma alanına veri gönderdiğini görmek için, [Azure Güvenlik Merkezi ile bağlantıyı doğrulamak](/archive/blogs/yuridiogenes/validating-connectivity-with-azure-security-center)üzere testi çalıştırın. Alternatif olarak, Log Analytics çalışma alanlarını açabilir, bir çalışma alanı seçebilir, sanal makineyi seçebilir ve Log Analytics Aracı bağlantısına bakabilirsiniz.
    - Log Analytics aracısının istemci iş istasyonlarında yüklü olduğu bir ortamınız varsa ve var olan bir Log Analytics çalışma alanına raporlama yaptıysanız, işletim sisteminizin desteklendiğinden emin olmak için [Azure Güvenlik Merkezi tarafından desteklenen işletim sistemlerinin](security-center-os-coverage.md) listesini gözden geçirin. Daha fazla bilgi için bkz. [var olan Log Analytics müşterileri](./faq-azure-monitor-logs.md).
 

## <a name="disable-auto-provisioning"></a>Otomatik sağlamayı devre dışı bırak <a name="offprovisioning"></a>

Otomatik sağlamayı devre dışı bıraktığınızda, aracılar yeni VM 'lerde sağlanmayacak.

Bir aracının otomatik olarak sağlanmasını devre dışı bırakmak için:

1. Portalda Güvenlik Merkezi 'nin menüsünde, **fiyatlandırma & ayarları**' nı seçin.
1. Uygun aboneliği seçin.
1. **Otomatik sağlamayı** seçin.
1. İlgili aracı için durumu **kapalı** olarak değiştirin.

    :::image type="content" source="./media/security-center-enable-data-collection/agent-toggles.png" alt-text="Aracı türü başına otomatik sağlamayı devre dışı bırakmak için geçiş yapar":::

1. **Kaydet**’i seçin. Otomatik sağlama devre dışı bırakıldığında, varsayılan çalışma alanı yapılandırma bölümü görüntülenmez:

    :::image type="content" source="./media/security-center-enable-data-collection/empty-configuration-column.png" alt-text="Otomatik sağlama devre dışı bırakıldığında, yapılandırma hücresi boştur":::


> [!NOTE]
>  Otomatik sağlamayı devre dışı bırakmak, aracının sağlandığı Azure VM 'lerinden Log Analytics aracısını kaldırmaz. OMS uzantısını kaldırma hakkında bilgi için, bkz. [Güvenlik Merkezi tarafından yüklenen OMS uzantılarını kaldırma nasıl yaparım?](faq-data-collection-agents.md#remove-oms).
>


## <a name="troubleshooting"></a>Sorun giderme

-   Otomatik sağlama yükleme sorunlarını belirlemek için bkz. [Aracı sistem durumu sorunlarını izleme](security-center-troubleshooting-guide.md#mon-agent).
-  İzleme Aracısı ağ gereksinimlerini belirlemek için bkz. [İzleme Aracısı ağ gereksinimlerini giderme](security-center-troubleshooting-guide.md#mon-network-req).
-   El ile ekleme sorunlarını belirlemek için bkz. [Operations Management Suite ekleme sorunlarını giderme](https://support.microsoft.com/help/3126513/how-to-troubleshoot-operations-management-suite-onboarding-issues).



## <a name="next-steps"></a>Sonraki adımlar
Bu sayfada, Log Analytics Aracısı ve diğer güvenlik merkezi uzantıları için otomatik sağlamayı etkinleştirme konusu açıklanmaktadır. Ayrıca, toplanan verilerin depolandığı bir Log Analytics çalışma alanını nasıl tanımlayacağınızı da tarif ediyor. Veri toplamayı etkinleştirmek için her iki işlem de gereklidir. Yeni veya mevcut bir çalışma alanı kullanmanıza bakılmaksızın verileri Log Analytics depolama, veri depolama için daha fazla ücret ödemeniz istenebilir. Seçtiğiniz para birimindeki ve bölgenize göre fiyatlandırma ayrıntıları için bkz. [Güvenlik Merkezi fiyatlandırması](https://azure.microsoft.com/pricing/details/security-center/).