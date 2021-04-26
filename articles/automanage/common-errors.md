---
title: Yaygın Azure oto yönetimi ekleme hatalarını giderme
description: Ortak oto yönetimi ekleme hataları ve bunların sorunlarını giderme
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: alsin
ms.openlocfilehash: 6ee0164dd8243d30cf691350352757f2503e34c8
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107862984"
---
# <a name="troubleshoot-common-automanage-onboarding-errors"></a>Ortak oto yönetimi ekleme hatalarını giderme
Oto yönetimi, bir makineyi hizmete eklemek için başarısız olabilir. Bu belgede dağıtım hatalarının nasıl giderileceği, dağıtımların başarısız olmasının bazı yaygın nedenlerini nasıl paylaştığı ve hafifletme konusunda olası bir sonraki adım açıklanmaktadır.

## <a name="troubleshooting-deployment-failures"></a>Dağıtım hatalarıyla ilgili sorunları giderme
Bir makineyi tekrar yönetmeye eklemek Azure Resource Manager dağıtımının oluşturulmasını sağlar. Daha fazla bilgi için, neden başarısız olduğuna ilişkin daha fazla ayrıntı için dağıtıma bakın. Hata ayrıntısı açılır penceresinde, aşağıda yer alarak dağıtımların bağlantıları vardır.

:::image type="content" source="media\common-errors\failure-flyout.png" alt-text="Hata ayrıntısı açılır öğesini oto Yönet.":::

### <a name="check-the-deployments-for-the-resource-group-containing-the-failed-vm"></a>Başarısız VM 'yi içeren kaynak grubunun dağıtımlarını denetleyin
Hata açılır öğesi, kaynak grubundaki, ekleme başarısız olan makineyi içeren dağıtımlar için bir bağlantı içerir. Bu açılır pencere, ile dağıtımları filtrelemek için kullanabileceğiniz bir ön ek adı da içerir. Dağıtım bağlantısına tıkladığınızda sizi makinenize yönelik dağıtımları görmek için dağıtımları filtreleyebileceğiniz dağıtımlar dikey penceresine götürür. Birden çok bölgeye dağıtıyorsanız, doğru bölgede dağıtıma tıklatığınızdan emin olun.

### <a name="check-the-deployments-for-the-subscription-containing-the-failed-vm"></a>Başarısız VM 'yi içeren aboneliğin dağıtımlarını denetleyin
Kaynak grubu dağıtımında bir hata görmüyorsanız, sonraki adımınız, aboneliğinizdeki, ekleme başarısız olan VM 'yi içeren dağıtımlara bakmak olacaktır. Hata açılır penceresinde **abonelik dağıtımları** bağlantısı ' na tıklayın ve yeniden **Manage-defaultresourcegroup** filtresi kullanarak dağıtımları filtreleyin. Dağıtımları filtrelemek için başarısızlık dikey penceresinden kaynak grubu adını kullanın. Dağıtım adı bir bölge adı ile son olarak düzeltilmelidir. Birden çok bölgeye dağıtıyorsanız, doğru bölgede dağıtıma tıklatığınızdan emin olun.

### <a name="check-deployments-in-a-subscription-linked-to-a-log-analytics-workspace"></a>Log Analytics çalışma alanına bağlı abonelikte dağıtımları denetleme
Kaynak grubunda veya başarısız sanal makinenizin bulunduğu abonelikte başarısız bir dağıtım görmüyorsanız ve başarısız VM 'niz farklı bir abonelikteki bir Log Analytics çalışma alanına bağlıysa, Log Analytics çalışma alanınıza bağlı aboneliğe gidin ve başarısız dağıtımları denetleyin.

## <a name="common-deployment-errors"></a>Sık karşılaşılan dağıtım hataları

Hata |  Risk azaltma
:-----|:-------------|
Hesap yetersiz izin hatası | Bu hata, son zamanlarda yeni bir oto Yönet hesabı içeren bir aboneliği yeni bir kiracıya taşıdıysanız meydana gelebilir. Bu hatayı çözmeye yönelik adımlar [burada](./repair-automanage-account.md)yer alır.
Bölge eşleme gereksinimleriyle eşleşmeyen çalışma alanı bölgesi | Makinenin Şu anda bağlı olduğu Log Analytics çalışma alanı desteklenen bir Otomasyon bölgesine eşlenmediğinden otomatik Yönet, makinenizi ekleyemedi. Mevcut Log Analytics çalışma alanınızın ve otomasyon hesabınızın [desteklenen bir bölge eşlemesinde](../automation/how-to/region-mappings.md)bulunduğundan emin olun.
"' Yönetilen uygulama tarafından oluşturulan ' sistem reddetme ataması '" adlı reddetme ataması nedeniyle erişim engellendi " | Kaynağınız üzerinde bir [denyAssignment](https://docs.microsoft.com/azure/role-based-access-control/deny-assignments) oluşturulmuştur ve bu da, sizin için, bu, sizin verilerinize erişimini engelledi. Bu denyAssignment, bir [Blueprint](https://docs.microsoft.com/azure/governance/blueprints/concepts/resource-locking) veya [yönetilen uygulama](https://docs.microsoft.com/azure/azure-resource-manager/managed-applications/overview)tarafından oluşturulmuş olabilir.
"OS ınformation: Name = ' (null) ', ver = ' (null) ', aracı durumu = ' Ready '." | [En az desteklenen bir aracı sürümü](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/support-extensions-agent-version)([Linux](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/linux-azure-guest-agent) ve [Windows](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/windows-azure-guest-agent)) çalıştırdığından ve aracının güncel olduğundan emin olun ([Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent) ve [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows)).
"VM IŞLETIM sistemi adı için işletim sistemi belirlenemiyor:, ver. Lütfen VM aracısının çalıştığını, geçerli durumunun Ready olduğunu kontrol edin. " | [En az desteklenen bir aracı sürümü](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/support-extensions-agent-version)([Linux](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/linux-azure-guest-agent) ve [Windows](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/windows-azure-guest-agent)) çalıştırdığından ve aracının güncel olduğundan emin olun ([Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent) ve [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows)).
"VM, ' ıaasantimalware ' uzantısını işlerken bir hata bildirdi" | VM 'nize zaten yüklü başka bir kötü amaçlı yazılımdan koruma/virüsten koruma teklifi olmadığından emin olun. Başarısız olursa, desteğe başvurun.
ASC çalışma alanı: oto yönetimi şu anda _konumunda_ Log Analytics hizmetini desteklememektedir. | SANAL makinenizin [desteklenen bir bölgede](./automanage-virtual-machines.md#supported-regions)bulunduğundan emin olun.
İlke ihlali nedeniyle şablon dağıtımı başarısız oldu. Daha fazla bilgi için lütfen ayrıntılara bakın. | Bir ilke, VM 'nizi ekleme işlemini yapmanızı önler. Aboneliğinize veya sizin için eklemek istediğiniz VM 'nizi içeren kaynak grubunuza uygulanan ilkeleri kontrol edin.
"Atama başarısız oldu; kullanılabilir ek bilgi yok " | Lütfen Microsoft Azure desteğiyle bir servis talebi açın.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure oto yönetimi hakkında daha fazla bilgi edinin](./automanage-virtual-machines.md)

> [!div class="nextstepaction"]
> [Azure portal sanal makineler için oto yönetimini etkinleştir](quick-create-virtual-machines-portal.md)