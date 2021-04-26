---
title: Azure Arc etkin sunucusunu Azure Sentinel 'e ekleme
description: Azure Arc etkin sunucularınızı Azure Sentinel 'e eklemeyi ve güvenlik durumlarını önceden izlemeyi öğrenin.
ms.date: 11/16/2020
ms.topic: conceptual
ms.openlocfilehash: 2364ba72ac5b10ec4e1f433cc6d591c3ca389ecd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100584740"
---
# <a name="onboard-azure-arc-enabled-servers-to-azure-sentinel"></a>Azure Arc etkin sunucularını Azure Sentinel 'e ekleme

Bu makale, Azure Arc etkin sunucunuzu [Azure Sentinel](../../sentinel/overview.md) 'e eklemenize ve güvenlikle ilgili olayları toplamaya başlamanıza yardımcı olmaya yöneliktir. Azure Sentinel, bir uyarı algılama, tehdit görünürlüğü, proaktif arama ve kuruluş genelinde tehdit yanıtı için tek bir çözüm sunar.

## <a name="prerequisites"></a>Önkoşullar

Başlamadan önce, aşağıdaki gereksinimleri karşıladığınızdan emin olun:

- Bir [Log Analytics çalışma alanı](../../azure-monitor/logs/data-platform-logs.md). Log Analytics çalışma alanları hakkında daha fazla bilgi için bkz. [Azure Izleyici günlükleri dağıtımınızı tasarlama](../../azure-monitor/logs/design-logs-deployment.md).

- Aboneliğinizde Azure Sentinel [etkin](../../sentinel/quickstart-onboard.md).

- Makine veya sunucu, Azure Arc etkin sunucularına bağlı.

## <a name="onboard-azure-arc-enabled-servers-to-azure-sentinel"></a>Azure Arc etkin sunucularını Azure Sentinel 'e ekleme

Azure Sentinel, Microsoft çözümleri için kullanıma hazır olan ve gerçek zamanlı tümleştirme sağlayan birçok bağlayıcıyla birlikte gelir. Fiziksel ve sanal makineler için, günlükleri toplayan ve bunları Azure Sentinel 'e ileten Log Analytics aracısını yükleyebilirsiniz. Yay özellikli sunucular, aşağıdaki yöntemleri kullanarak Log Analytics aracısının dağıtılmasını destekler:

- VM uzantıları çerçevesini kullanma.

    Azure Arc etkin sunucuları 'ndaki bu özellik, Azure olmayan bir Windows ve/veya Linux sunucusuna Log Analytics Agent VM uzantısını dağıtmanıza olanak tanır. SANAL makine uzantıları, karma makinelerinizde veya Arc etkin sunucularla yönetilen sunucularda aşağıdaki yöntemler kullanılarak yönetilebilir:

    - [Azure Portal](manage-vm-extensions-portal.md)
    - [Azure CLI](manage-vm-extensions-cli.md)
    - [Azure PowerShell](manage-vm-extensions-powershell.md)
    - Azure [Kaynak Yöneticisi şablonları](manage-vm-extensions-template.md)

- Azure Ilkesi 'ni kullanma.

    Bu yaklaşımı kullanarak, Arc etkin sunucusunda Log Analytics aracısının yüklü olup olmadığını denetlemek için Azure Ilke [dağıtma Log Analytics aracısını Linux veya Windows Azure Arc makineler](../../governance/policy/samples/built-in-policies.md#monitoring) yerleşik ilkesine kullanırsınız. Aracı yüklü değilse, bir düzeltme görevi kullanarak otomatik olarak dağıtır. Alternatif olarak, makineleri VM'ler için Azure İzleyici ile izlemeyi planlıyorsanız, bunun yerine Log Analytics aracısını yüklemek ve yapılandırmak için [VM'ler için Azure izleyici etkinleştir](../../governance/policy/samples/built-in-initiatives.md#monitoring) girişim kullanın.

Azure Ilkesini kullanarak Windows veya Linux için Log Analytics Aracısı yüklemenizi öneririz.

Arc etkin sunucularınız bağlandıktan sonra Verileriniz Azure Sentinel 'e akışa başlar ve ile çalışmaya başlayabilirsiniz. Günlükleri [yerleşik çalışma kitaplarında](../../sentinel/quickstart-get-visibility.md) görüntüleyebilir ve [verileri araştırmak](../../sentinel/tutorial-investigate-cases.md)için Log Analytics sorguları oluşturmaya başlayabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

[Azure Sentinel ile tehditleri algılamaya](../../sentinel/tutorial-detect-threats-built-in.md)başlayın.