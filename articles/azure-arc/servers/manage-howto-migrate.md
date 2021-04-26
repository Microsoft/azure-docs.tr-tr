---
title: Azure Arc etkin sunucularını bölgeler arasında geçirme
description: Azure Arc etkin sunucusunu bir bölgeden diğerine geçirmeyi öğrenin.
ms.date: 02/10/2021
ms.topic: conceptual
ms.openlocfilehash: 251a347205d93af715add52db293d8000438df44
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101650184"
---
# <a name="how-to-migrate-azure-arc-enabled-servers-across-regions"></a>Azure Arc etkin sunucularını bölgeler arasında geçirme

Mevcut Azure yay etkin sunucunuzu bir bölgeden diğerine taşımak isteyebileceğiniz senaryolar vardır. Örneğin, makinenin yanlış bölgeye kaydolmasını, yönetilebilirlik 'i geliştirmeyi veya idare nedenlerini öğrenmek için olduğunu fark edersiniz.

Azure Arc etkin sunucusunu bir Azure bölgesinden diğerine geçirmek için VM uzantılarını kaldırmanız, kaynağı Azure 'da silmeniz ve diğer bölgede yeniden oluşturmanız gerekir. Bu adımları gerçekleştirmeden önce, hangi VM uzantılarının yüklendiğini doğrulamak için makineyi denetlemeniz gerekir.

> [!NOTE]
> Yüklenen uzantılar çalışmaya devam eder ve bu yordam tamamlandıktan sonra normal işlemlerini gerçekleştirmeyecektir. Makinede uzantıları yeniden dağıtmaya çalışırsanız öngörülemeyen davranışlarla karşılaşabilirsiniz.

## <a name="move-machine-to-other-region"></a>Makineyi diğer bölgeye taşı

> [!NOTE]
> Bu işlem sırasında, geçiş sırasında kapalı kalma süresine neden olur.

1. [Azure CLI](manage-vm-extensions-cli.md#remove-an-installed-extension)kullanarak veya [Azure PowerShell](manage-vm-extensions-powershell.md#remove-an-installed-extension)kullanarak [Azure Portal](manage-vm-extensions-portal.md#uninstall-extension)yüklü VM uzantılarını kaldırın.

2. Makinenin Azure Arc bağlantısını kesmek ve makine kaynağını Azure 'dan silmek için **azcmagent** aracını [Disconnect](manage-agent.md#disconnect) parametresiyle birlikte kullanın. Makinenin yay özellikli sunuculardan bağlantısının kesilmesi bağlı makine aracısını kaldırmaz ve aracıyı bu işlemin bir parçası olarak kaldırmanız gerekmez. Etkileşimli olarak oturum açtığınızda bunu el ile çalıştırabilir veya birden çok aracı eklemek için kullandığınız hizmet sorumlusunu veya Microsoft Identity Platform [erişim belirtecini](../../active-directory/develop/access-tokens.md)kullanarak otomatikleştirin. Makineyi Azure Arc etkin sunucularla kaydetmek için bir hizmet sorumlusu kullanmıyorsanız, hizmet sorumlusu oluşturmak için aşağıdaki [makaleye](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) bakın.

3. Bağlı makine aracısını diğer bölgedeki Arc etkin sunucularla yeniden kaydedin. `azcmagent`Aracı [Connect](manage-agent.md#connect) parametresiyle çalıştırın bu adımı gerçekleştirin.

4. Özgün olarak dağıtılan sanal makine uzantılarını yay etkin sunuculardan yeniden dağıtın. Azure ilkesi kullanarak VM'ler için Azure İzleyici (Öngörüler) Aracısı veya Log Analytics Aracısı dağıttıysanız, aracılar bir sonraki [değerlendirme döngüsünden](../../governance/policy/how-to/get-compliance-data.md#evaluation-triggers)sonra yeniden dağıtılır.

## <a name="next-steps"></a>Sonraki adımlar

* Sorun giderme bilgileri, [bağlı makine Aracısı sorunlarını giderme kılavuzunda](troubleshoot-agent-onboard.md)bulunabilir.

* VM [Konuk yapılandırması](../../governance/policy/concepts/guest-configuration.md), makinenin beklenen Log Analytics çalışma alanına rapor olduğunu doğrulama, [VM 'Ler ile Azure izleyici](../../azure-monitor/vm/vminsights-enable-policy.md) ile izlemeyi etkinleştirme ve çok daha fazlası için [Azure ilkesi](../../governance/policy/overview.md)'ni kullanarak makinenizi yönetmeyi öğrenin.