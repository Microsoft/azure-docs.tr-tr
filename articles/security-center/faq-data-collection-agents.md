---
title: Azure Güvenlik Merkezi SSS-veri toplama ve aracılar
description: Azure Güvenlik Merkezi için veri toplama, aracılar ve çalışma alanları hakkında sık sorulan sorular ve tehditleri önlemenize, algılamanıza ve yanıtlamanıza yardımcı olan bir ürün
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2020
ms.author: memildin
ms.openlocfilehash: 4ef01045d1ca16d0101cdd9ccfcd118231cd28de
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "102456122"
---
# <a name="faq---questions-about-data-collection-agents-and-workspaces"></a>SSS-veri toplama, aracılar ve çalışma alanları hakkında sorular

Güvenlik Merkezi, güvenlik açıklarını ve tehditleri izlemek için Azure sanal makinelerinizden (VM), sanal makine ölçek kümelerinden, IaaS kapsayıcılarından ve Azure olmayan bilgisayarlardan (Şirket içi makineler dahil) veri toplar. Veriler, makineden güvenlikle ilgili çeşitli yapılandırma ve olay günlüklerini okuyan ve analiz için verileri çalışma alanınıza kopyalayan Log Analytics Aracı kullanılarak toplanır.


## <a name="am-i-billed-for-azure-monitor-logs-on-the-workspaces-created-by-security-center"></a>Güvenlik Merkezi tarafından oluşturulan çalışma alanlarında Azure Izleyici günlükleri için faturalandırılırım mı?

Hayır. Güvenlik Merkezi tarafından oluşturulan ve düğüm başına Azure Izleyici günlükleri için yapılandırılmış çalışma alanları Azure Izleyici günlükleri ücretlerine tabi değildir. Güvenlik Merkezi 'nin faturalandırması her zaman Güvenlik Merkezi güvenlik ilkenize ve bir çalışma alanına yüklenmiş çözümlere göre belirlenir:

- **Azure Defender kapalı** – Güvenlik Merkezi, varsayılan çalışma alanında "SecurityCenterFree" çözümüne izin vermez. Azure Defender kapalıysa faturalandırılmaz.

- **Azure Defender on** -Security Center, varsayılan çalışma alanındaki "güvenlik" çözümüne izin vermez.

Seçtiğiniz para birimindeki ve bölgenize göre fiyatlandırma ayrıntıları için bkz. [Güvenlik Merkezi fiyatlandırması](https://azure.microsoft.com/pricing/details/security-center/).

> [!NOTE]
> Güvenlik Merkezi tarafından oluşturulan çalışma alanlarının Log Analytics fiyatlandırma katmanı, güvenlik merkezi faturalandırmasını etkilemez.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


## <a name="what-is-the-log-analytics-agent"></a>Log Analytics Aracısı nedir?

Azure Güvenlik Merkezi, güvenlik açıklarını ve tehditleri izlemek için [Log Analytics aracısına](../azure-monitor/agents/log-analytics-agent.md) bağlıdır. Bu, Azure İzleyici hizmeti tarafından kullanılan aracıdır. 

Aracı bazen Microsoft Monitoring Agent (veya "MMA") olarak adlandırılır. 

Aracı, bağlantılı makinelerden güvenlikle ilgili çeşitli yapılandırma ayrıntılarını ve olay günlüklerini toplar ve daha fazla analiz için verileri Log Analytics çalışma alanınıza kopyalar. Bu verilere örnek olarak şunlar verilebilir: işletim sistemi türü ve sürümü, işletim sistemi günlükleri (Windows olay günlükleri), çalışan süreçler, makine adı, IP adresleri ve oturum açmış kullanıcı.

Aşağıdaki sayfalarda açıklandığı gibi makinelerinizin aracının desteklenen işletim sistemlerinden birini çalıştırdığından emin olun:

* [Windows tarafından desteklenen işletim sistemleri için Log Analytics Aracısı](../azure-monitor/agents/agents-overview.md#supported-operating-systems)

* [Linux tarafından desteklenen işletim sistemleri için Log Analytics Aracısı](../azure-monitor/agents/agents-overview.md#supported-operating-systems)

[Log Analytics Aracısı tarafından toplanan veriler](security-center-enable-data-collection.md)hakkında daha fazla bilgi edinin.




## <a name="what-qualifies-a-vm-for-automatic-provisioning-of-the-log-analytics-agent-installation"></a>Log Analytics Aracısı yüklemesinin otomatik olarak sağlanması için bir VM nitelendirir mi?

Windows veya Linux IaaS VM 'Leri şu durumlarda niteler:

- Log Analytics Aracısı uzantısı Şu anda VM 'de yüklü değil.
- VM çalışıyor durumunda.
- Windows veya Linux [Azure sanal makine Aracısı](../virtual-machines/extensions/agent-windows.md) yüklendi.
- VM, Web uygulaması güvenlik duvarı veya yeni nesil güvenlik duvarı gibi bir gereç olarak kullanılmaz.


## <a name="where-is-the-default-log-analytics-workspace-created"></a>Varsayılan Log Analytics çalışma alanı nerede oluşturulur?

Varsayılan çalışma alanının konumu, Azure bölgenize bağlıdır:

- Birleşik Devletler ve Brezilya 'daki VM 'Ler için çalışma alanı konumu Birleşik Devletler
- Kanada 'daki VM 'Ler için çalışma alanı konumu Kanada
- Avrupa 'daki VM 'Ler için çalışma alanı konumu Avrupa
- UK 'teki VM 'Ler için çalışma alanı konumu UK
- Doğu Asya ve Güneydoğu Asya 'daki VM 'Ler için çalışma alanı konumu Asya
- Kore 'deki VM 'Ler için çalışma alanı konumu Kore 'dir
- Hindistan 'daki VM 'Ler için çalışma alanı konumu Hindistan ' dır
- Japonya 'daki VM 'Ler için çalışma alanı konumu Japonya olur
- Çin 'deki VM 'Ler için çalışma alanı konumu Çin 'dir
- Avustralya 'daki VM 'Ler için çalışma alanı konumu Avustralya ' dır


## <a name="what-security-events-are-collected-by-the-log-analytics-agent"></a>Log Analytics Aracısı tarafından hangi güvenlik olayları toplandı?

Aracı tarafından toplanan güvenlik olaylarının tam listesi için bkz. ["Common" ve "minimal" güvenlik olayları ayarları Için hangi olay türlerinin depolandığı](security-center-enable-data-collection.md#what-event-types-are-stored-for-common-and-minimal).

> [!IMPORTANT]
> Azure Güvenlik Duvarı gibi bazı hizmetlerde günlüğe kaydetmeyi etkinleştirdiyseniz ve günlüğe kaydedilecek bir geveze kaynağı seçtiyseniz (örneğin, günlüğü *verbose* olarak ayarlamak), Log Analytics çalışma alanı depolama gereksinimlerinizin önemli etkilerini görebilirsiniz. 


## <a name="can-i-delete-the-default-workspaces-created-by-security-center"></a>Güvenlik Merkezi tarafından oluşturulan varsayılan çalışma alanlarını silebilir miyim?

**Varsayılan çalışma alanını silme önerilmez.** Güvenlik Merkezi, sanal makinelerinizden güvenlik verilerini depolamak için varsayılan çalışma alanlarını kullanır. Bir çalışma alanını silerseniz, güvenlik merkezi bu verileri toplayamıyor ve bazı güvenlik önerileri ve uyarılar kullanılamıyor.

Kurtarmak için, silinen çalışma alanına bağlı olan VM 'lerde Log Analytics aracısını kaldırın. Güvenlik Merkezi aracıyı yeniden yükler ve yeni varsayılan çalışma alanları oluşturur.

## <a name="how-can-i-use-my-existing-log-analytics-workspace"></a>Mevcut Log Analytics çalışma alanım 'ı nasıl kullanabilirim?

Güvenlik Merkezi tarafından toplanan verileri depolamak için mevcut bir Log Analytics çalışma alanı seçebilirsiniz. Mevcut Log Analytics çalışma alanınızı kullanmak için:

- Çalışma alanının seçtiğiniz Azure aboneliğiniz ile ilişkilendirilmesi gerekir.
- En azından, çalışma alanına erişmek için okuma izninizin olması gerekir.

Mevcut bir Log Analytics çalışma alanını seçmek için:

1. Güvenlik Merkezi 'nin menüsünde **fiyatlandırma & ayarları**' nı seçin.
1. Uygun aboneliği seçin.
1. **Otomatik sağlama** sayfasını açın.
1. Log Analytics Aracısı için **yapılandırmayı Düzenle**' yi seçin. 

    :::image type="content" source="./media/security-center-enable-data-collection/edit-configuration-auto-deploy-agent.png" alt-text="Otomatik dağıtım kullanılırken kullanılacak Log Analytics aracısının yapılandırması" lightbox="./media/security-center-enable-data-collection/edit-configuration-auto-deploy-agent.png":::

1. **Azure VM 'lerini farklı bir çalışma alanına bağla** ' yı seçin ve mevcut çalışma alanınızı seçin.

    :::image type="content" source="./media/security-center-enable-data-collection/choose-workspace.png" alt-text="Log Analytics aracınızın raporlamak için varsayılan olmayan bir çalışma alanı seçme" lightbox="./media/security-center-enable-data-collection/choose-workspace.png":::

    > [!TIP]
    > Liste yalnızca erişiminiz olan ve Azure aboneliğinizde olan çalışma alanlarını içerir.

1. **Kaydet**’i seçin. İzlenen VM 'Leri yeniden yapılandırmak isteyip istemediğiniz sorulur.

    - Yeni çalışma alanı ayarlarının **yalnızca yeni VM 'lere uygulanmasını** istiyorsanız **Hayır** ' ı seçin. Yeni çalışma alanı ayarları yalnızca yeni aracı yüklemeleri için geçerlidir; Log Analytics Aracısı yüklü olmayan yeni bulunan VM 'Ler.
    - Yeni çalışma alanı ayarlarının **Tüm VM 'lere uygulanmasını** istiyorsanız **Evet** ' i seçin. Ayrıca, bir güvenlik merkezi tarafından oluşturulan çalışma alanına bağlı her VM yeni hedef çalışma alanına yeniden bağlanır.

    > [!NOTE]
    > **Evet**' i seçerseniz, tüm VM 'ler yeni hedef çalışma alanına yeniden bağlanana kadar Güvenlik Merkezi tarafından oluşturulan çalışma alanlarını silmeyin. Çalışma alanı çok erken silinirse bu işlem başarısız olur.


## <a name="what-if-the-log-analytics-agent-was-already-installed-as-an-extension-on-the-vm"></a>Log Analytics Aracısı VM üzerinde zaten bir uzantı olarak yüklendiyse ne olacak?<a name="mmaextensioninstalled"></a>

Izleme Aracısı bir uzantı olarak yüklendiğinde, uzantı yapılandırması raporlamaya yalnızca tek bir çalışma alanına izin verir. Güvenlik Merkezi, mevcut kullanıcı çalışma alanları bağlantılarını geçersiz kılmaz. Güvenlik Merkezi, "güvenlik" veya "SecurityCenterFree" çözümünün yüklenmiş olması şartıyla, zaten bağlı olan bir çalışma alanındaki bir VM 'den güvenlik verilerini depolar. Güvenlik Merkezi bu işlemdeki en son sürüme uzantı sürümünü yükseltebilir.

Daha fazla bilgi için bkz. [önceden var olan aracı yüklemesi durumlarında otomatik sağlama](security-center-enable-data-collection.md#preexisting).



## <a name="what-if-a-log-analytics-agent-is-directly-installed-on-the-machine-but-not-as-an-extension-direct-agent"></a>Bir Log Analytics Aracısı makineye doğrudan yüklenmişse ancak uzantı olarak (doğrudan aracı) yoksa ne olur?<a name="directagentinstalled"></a>

Log Analytics Aracısı doğrudan VM 'ye (Azure uzantısı olarak değil) yüklenirse, güvenlik merkezi Log Analytics aracı uzantısını yükler ve Log Analytics aracısını en son sürüme yükseltecektir.

Yüklü aracı zaten yapılandırılmış çalışma alanına (ler) raporlama yapmaya devam edecektir ve ayrıca, güvenlik merkezi 'nde yapılandırılan çalışma alanına rapor eder (Windows makinelerde çoklu barındırma desteklenir).

Yapılandırılmış çalışma alanı bir kullanıcı çalışma alanı ise (Güvenlik Merkezi 'nin varsayılan çalışma alanı değil), bu çalışma alanına raporlama yapan VM 'Ler ve bilgisayarlardan gelen olayları işlemeye başlamak için Güvenlik Merkezi 'nin "güvenlik" veya "SecurityCenterFree" çözümünü yüklemeniz gerekir.

Linux makineler için, aracı çoklu barındırma henüz desteklenmiyor. bu nedenle, mevcut bir aracı yüklemesi algılanırsa, otomatik sağlama gerçekleşmez ve makinenin yapılandırması değiştirilmez.

Aboneliklerdeki mevcut makineler için eklendi Mart 17 2019 ' den önce güvenlik merkezi 'ne, mevcut bir aracı algılandığında, Log Analytics Aracısı uzantısı yüklenmez ve makine etkilenmeyecektir. Bu makineler için, bu makinelerdeki aracı yükleme sorunlarını gidermek için "makinelerinizdeki izleme Aracısı sistem durumu sorunlarını çözme" önerisine bakın

Daha fazla bilgi için, [VM 'imde System Center Operations Manager veya OMS doğrudan Aracısı zaten yüklüyse](#scomomsinstalled) , sonraki bölüme bakın.

## <a name="what-if-a-system-center-operations-manager-agent-is-already-installed-on-my-vm"></a>System Center Operations Manager Aracısı VM 'imde zaten yüklüyse ne olur?<a name="scomomsinstalled"></a>

Güvenlik Merkezi, Log Analytics aracı uzantısını mevcut System Center Operations Manager aracısına yan yana yükleyecek. Mevcut Aracı, System Center Operations Manager sunucusuna normal olarak rapor etmeye devam edecektir. Operations Manager Aracısı ve Log Analytics aracısının, bu işlem sırasında en son sürüme güncellenecek ortak çalışma zamanı kitaplıklarını paylaştığından emin olun. Not-Operations Manager aracısının 2012 sürümü yüklüyse otomatik sağlamayı etkinleştirmeyin (Operations Manager sunucusu da sürüm 2012 olduğunda yönetilebilirlik özellikleri kaybolabilir).


## <a name="what-is-the-impact-of-removing-these-extensions"></a>Bu uzantıları kaldırmanın etkisi nedir?

Microsoft Monitoring uzantısını kaldırırsanız, güvenlik merkezi VM 'den güvenlik verileri toplayamaz ve bazı güvenlik önerileri ve uyarılar kullanılamaz. Güvenlik Merkezi, 24 saat içinde, VM 'nin uzantının eksik olduğunu belirler ve uzantıyı yeniden yükler.


## <a name="how-do-i-stop-the-automatic-agent-installation-and-workspace-creation"></a>Nasıl yaparım? otomatik aracı yüklemesi ve çalışma alanı oluşturma durdurulsun mu?

Güvenlik ilkesinde abonelikleriniz için otomatik sağlamayı devre dışı bırakabilirsiniz, ancak bu önerilmez. Otomatik sağlama devre dışı bırakmak, güvenlik merkezi önerilerini ve uyarılarını kısıtlar. Otomatik sağlamayı devre dışı bırakmak için:

1. Güvenlik Merkezi 'nin menüsünde **fiyatlandırma & ayarları**' nı seçin.
1. Uygun aboneliği seçin.
1. Aboneliğiniz Azure Defender 'ı etkinleştirmişse **Azure Defender planlarını** açın ve **Azure Defender kapalı**' yı seçin.

    :::image type="content" source="./media/security-center-platform-migration-faq/pricing-tier.png" alt-text="Azure Defender 'ı etkinleştirme veya devre dışı bırakma":::

1. **Otomatik sağlama** sayfasında, kalem ' i seçin ve **güvenlik ilkesi – veri toplama** sayfasında otomatik sağlamayı Kapat ' ı seçin.

    :::image type="content" source="./media/security-center-enable-data-collection/agent-toggles.png" alt-text="Log Analytics Aracısı için otomatik dağıtımı etkinleştirme":::

1. **Kaydet**’i seçin.


## <a name="should-i-opt-out-of-the-automatic-agent-installation-and-workspace-creation"></a>Otomatik aracı yüklemesi ve çalışma alanı oluşturmayı kabul etmem gerekir mi?

> [!NOTE]
> Bölümleri yeniden [kullanıma alma etkileri nelerdir?](#what-are-the-implications-of-opting-out-of-automatic-provisioning) ve otomatik sağlamayı devre dışı bırakmak isterseniz, [Önerilen adımları](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning) gözden geçirdiğinizden emin olun.

Aşağıdakiler geçerliyse otomatik sağlamayı devre dışı bırakmak isteyebilirsiniz:

- Güvenlik Merkezi tarafından otomatik aracı yüklemesi tüm abonelik için geçerlidir. Otomatik yüklemeyi VM 'lerin bir alt kümesine uygulayamazsınız. Log Analytics aracısıyla yüklenemeden kritik VM 'Ler varsa, otomatik sağlamayı devre dışı geçirmeniz gerekir.
- Log Analytics Agent uzantısının yüklenmesi aracının sürümünü güncelleştirir. Bu, doğrudan aracı ve bir System Center Operations Manager Aracısı için geçerlidir (ikinci olarak, Operations Manager ve Log Analytics Aracısı, işlem içinde güncelleştirilecektir ortak çalışma zamanı kitaplıklarını paylaşır). Yüklü Operations Manager Aracısı sürüm 2012 ise ve yükseltildiyse, Operations Manager sunucusu da sürüm 2012 olduğunda yönetilebilirlik özellikleri kaybolabilir. Yüklü Operations Manager Aracısı sürüm 2012 ise otomatik sağlamayı tercih edin.
- Abonelik dışında (Merkezi çalışma alanı) özel bir çalışma alanınız varsa, otomatik sağlamayı devre dışı geçirmeniz gerekir. Log Analytics Aracısı uzantısını el ile yükleyebilir ve bağlantıyı geçersiz kılarak Güvenlik Merkezi 'Ni kullanmadan çalışma alanınıza bağlayabilirsiniz.
- Abonelik başına birden çok çalışma alanı oluşturulmasını önlemek istiyorsanız ve abonelik içinde kendi özel çalışma alanınız varsa iki seçeneğiniz vardır:

   1. Otomatik sağlamayı devre dışı bırakabilirsiniz. Geçişten sonra, [var olan Log Analytics çalışma alanım 'ı nasıl kullanabilirim](#how-can-i-use-my-existing-log-analytics-workspace) bölümünde açıklandığı gibi varsayılan çalışma alanı ayarlarını ayarlayın.

   1. Ya da geçişin tamamlanmasına, VM 'Lere yüklenecek Log Analytics aracısına ve oluşturulan çalışma alanına bağlı VM 'Lere izin verebilirsiniz. Daha sonra, zaten yüklü olan aracıları yeniden yapılandırmak için, varsayılan çalışma alanınızı ' de kabul edilmesiyle olarak ayarlayarak kendi özel çalışma alanınızı seçin. Daha fazla bilgi için bkz. [var olan Log Analytics çalışma alanım 'ı nasıl kullanabilirim?](#how-can-i-use-my-existing-log-analytics-workspace)


## <a name="what-are-the-implications-of-opting-out-of-automatic-provisioning"></a>Otomatik sağlamayı geri alma etkileri nelerdir?

Geçiş tamamlandığında, güvenlik merkezi VM 'den güvenlik verileri toplayabilir ve bazı güvenlik önerileri ve uyarılar kullanılamaz. Devre dışı bırakırsanız Log Analytics aracısını el ile yükleyebilirsiniz. [Çalışırken önerilen adımlara](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning)bakın.


## <a name="what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning"></a>Otomatik sağlamayı geri yüklerken önerilen adımlar nelerdir?

Güvenlik Merkezi 'nin sanal makinelerinizden güvenlik verilerini toplayabilmesi ve öneriler ve uyarılar sağlaması için Log Analytics Aracısı uzantısını el ile yükleyebilirsiniz. Yükleme ile ilgili yönergeler için bkz. [WINDOWS VM için aracı yükleme](../virtual-machines/extensions/oms-windows.md) veya [Linux sanal makinesi için aracı yükleme](../virtual-machines/extensions/oms-linux.md) .

Aracıyı var olan herhangi bir özel çalışma alanına veya Güvenlik Merkezi tarafından oluşturulan çalışma alanına bağlayabilirsiniz. Özel bir çalışma alanında "güvenlik" veya "SecurityCenterFree" çözümleri etkinleştirilmemişse, bir çözüm uygulamanız gerekir. Uygulamak için, özel çalışma alanı veya aboneliği seçin ve **güvenlik ilkesi – fiyatlandırma katmanı** sayfası aracılığıyla bir fiyatlandırma katmanı uygulayın.

:::image type="content" source="./media/security-center-platform-migration-faq/pricing-tier.png" alt-text="Azure Defender 'ı etkinleştirme veya devre dışı bırakma":::

Güvenlik Merkezi, seçili fiyatlandırma katmanına göre çalışma alanında doğru çözümü etkinleştirir.


## <a name="how-do-i-remove-oms-extensions-installed-by-security-center"></a>Güvenlik Merkezi tarafından yüklenen OMS uzantıları Nasıl yaparım? kaldırılsın mı?<a name="remove-oms"></a>

Log Analytics aracısını el ile kaldırabilirsiniz. Bu, güvenlik merkezi önerilerini ve uyarılarını sınırlandırdığından önerilmez.

> [!NOTE]
> Veri toplama etkinleştirilirse Güvenlik Merkezi, aracıyı kaldırdıktan sonra yeniden yükler.  Aracıyı el ile kaldırmadan önce veri toplamayı devre dışı bırakmanız gerekir. Otomatik aracı yüklemesini ve çalışma alanı oluşturmayı durdurmak Nasıl yaparım? bakın. veri toplamayı devre dışı bırakma yönergeleri için.

Aracıyı el ile kaldırmak için:

1.    Portalda **Log Analytics** açın.

1.    Log Analytics sayfasında bir çalışma alanı seçin:

1.    İzlemek istemediğiniz VM 'Leri seçin ve **bağlantıyı kes**' i seçin.

   ![Aracıyı Kaldırma][3]

> [!NOTE]
> Bir Linux VM 'sinde zaten uzantı olmayan OMS Aracısı varsa, uzantının kaldırılması aracıyı de kaldırır ve yeniden yüklemeniz gerekir.


## <a name="how-do-i-disable-data-collection"></a>Veri toplamayı devre dışı Nasıl yaparım?.

Sistem güncelleştirmeleri, işletim sistemi güvenlik açıkları ve uç nokta koruması hakkında güvenlik uyarıları ve öneriler almak için otomatik sağlama kesinlikle önerilir. Varsayılan olarak otomatik sağlama devre dışıdır.

Onu etkinleştirdiyseniz, ancak şimdi devre dışı bırakmak istiyorsam:

1. [Azure Portal](https://portal.azure.com), **Güvenlik Merkezi** 'ni açın ve **fiyatlandırma ve ayarlar**' ı seçin.

1. Otomatik sağlamayı devre dışı bırakmak istediğiniz aboneliği seçin.

1. **Otomatik sağlama** altında, Log Analytics aracısına yönelik geçişi devre dışı bırakın.


## <a name="how-do-i-enable-data-collection"></a>Veri toplamayı etkinleştirmek Nasıl yaparım? mı?

Azure aboneliğiniz için veri toplamayı güvenlik ilkesinde etkinleştirebilirsiniz. Veri toplamayı etkinleştirmek için. [Azure Portal oturum açın](https://portal.azure.com), **Araştır**' ı seçin, **Güvenlik Merkezi**' ni seçin ve **güvenlik ilkesi**' ni seçin. Otomatik sağlamayı etkinleştirmek istediğiniz aboneliği seçin. Bir abonelik **güvenlik ilkesi seçtiğinizde, veri toplama** açılır. **Otomatik sağlama** altında **Açık**' ı seçin.


## <a name="what-happens-when-data-collection-is-enabled"></a>Veri toplama etkinleştirildiğinde ne olur?

Otomatik sağlama etkinleştirildiğinde Güvenlik Merkezi, desteklenen tüm Azure VM 'lerde ve oluşturulan tüm yeni makinelerde Log Analytics aracısını sağlar. Otomatik sağlama önerilir, ancak el ile aracı yüklemesi de kullanılabilir. [Log Analytics Aracısı uzantısını yüklemeyi öğrenin](../azure-monitor/vm/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension). 

Aracı, işlem oluşturma olayı 4688 ve olay 4688 ' nin içindeki *commandLine* alanı ' na izin vermez. VM üzerinde oluşturulan yeni süreçler, olay günlüğü tarafından kaydedilir ve Güvenlik Merkezi 'nin algılama hizmetleri tarafından izlenir. Her yeni işlem için kaydedilen ayrıntılar hakkında daha fazla bilgi için bkz. [4688 içindeki açıklama alanları](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=4688#fields). Aracı Ayrıca, VM 'de oluşturulan 4688 olayını toplar ve bunları arama halinde depolar.

Aracı, [Uyarlamalı uygulama denetimleri](security-center-adaptive-application.md)için veri toplamayı da sağlar, Güvenlik Merkezi, tüm uygulamalara izin vermek için denetim modunda yerel bir AppLocker ilkesi yapılandırır. Bu ilke, AppLocker 'ın Güvenlik Merkezi tarafından toplanan ve yararlanılabilir olayları oluşturmasına neden olur. Bu ilkenin zaten yapılandırılmış bir AppLocker ilkesinin bulunduğu makinelerde yapılandırılmaması gerektiğini unutmayın. 

Güvenlik Merkezi, VM 'de şüpheli etkinlik algıladığında, [güvenlik iletişim bilgileri](security-center-provide-security-contact-details.md) sağlanmışsa müşteri e-posta ile bildirilir. Güvenlik Merkezi 'nin güvenlik uyarıları panosunda bir uyarı da görünür.


## <a name="will-security-center-work-using-an-oms-gateway"></a>Güvenlik Merkezi, bir OMS ağ geçidi kullanarak çalışacak mı?

Evet. Azure Güvenlik Merkezi, Azure sanal makinelerinden ve sunuculardan veri toplamak için Log Analytics aracısını kullanarak Azure Izleyicisi 'ni kullanır.
Verileri toplamak için, her VM ve sunucunun HTTPS kullanarak Internet 'e bağlanması gerekir. Bağlantı, bir proxy kullanılarak veya [OMS ağ geçidi](../azure-monitor/agents/gateway.md)aracılığıyla doğrudan olabilir.


## <a name="does-the-monitoring-agent-impact-the-performance-of-my-servers"></a>Izleme Aracısı sunucularım performansını etkiler mi?

Aracı nominal miktarda sistem kaynağı tüketir ve performansı çok az etkiler. Performans etkisi ve aracı ve uzantı hakkında daha fazla bilgi için bkz. [planlama ve işlemler Kılavuzu](security-center-planning-and-operations-guide.md#data-collection-and-storage).




<!--Image references-->
[2]: ./media/security-center-platform-migration-faq/data-collection.png
[3]: ./media/security-center-platform-migration-faq/remove-the-agent.png
[4]: ./media/security-center-platform-migration-faq/use-another-workspace.png