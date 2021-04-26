---
title: VMware olağanüstü durum kurtarma-Azure Site Recovery yapılandırma sunucusu gereksinimleri
description: Bu makalede, Azure Site Recovery ile Azure 'a VMware olağanüstü durum kurtarma için yapılandırma sunucusu dağıtma desteği ve gereksinimleri açıklanır
ms.service: site-recovery
ms.topic: article
ms.date: 09/09/2019
ms.openlocfilehash: aec08aa4f18c69195be91b7544b74fee100f6f6b
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106580188"
---
# <a name="configuration-server-requirements-for-vmware-disaster-recovery-to-azure"></a>Azure'a VMware olağanüstü durum kurtarması için yapılandırma sunucusu gereksinimleri

VMware VM 'Leri ve fiziksel sunucuları Azure 'a olağanüstü durum kurtarması için [Azure Site Recovery](site-recovery-overview.md) kullandığınızda şirket içi yapılandırma sunucusu dağıtabilirsiniz.

- Yapılandırma sunucusu, şirket içi VMware ve Azure arasındaki iletişimleri koordine eder. Ayrıca veri çoğaltmasını yönetir.
- Yapılandırma sunucusu bileşenleri ve süreçler hakkında [daha fazla bilgi edinin](vmware-azure-architecture.md) .

## <a name="configuration-server-deployment"></a>Yapılandırma sunucusu dağıtımı

VMware VM 'lerinin Azure 'a olağanüstü durum kurtarması için yapılandırma sunucusunu bir VMware VM 'si olarak dağıtırsınız.

- Site Recovery, Azure portal karşıdan yüklediğiniz bir OVA şablonu sağlar ve yapılandırma sunucusu VM 'sini ayarlamak için vCenter Server içeri aktarabilirsiniz.
- Yapılandırma sunucusunu OVA şablonunu kullanarak dağıttığınızda, VM Bu makalede listelenen gereksinimlere otomatik olarak uyar.
- OVA şablonunu kullanarak yapılandırma sunucusunu ayarlamanızı kesinlikle öneririz. Ancak, VMware VM 'Leri için olağanüstü durum kurtarma ayarlıyorsanız ve OVA şablonunu kullandığımyorsanız, [belirtilen yönergeleri](physical-azure-set-up-source.md)kullanarak yapılandırma sunucusunu dağıtabilirsiniz.
- Şirket içi fiziksel makinelerin olağanüstü durum kurtarması için yapılandırma sunucusunu Azure 'a dağıtıyorsanız, [Bu makaledeki](physical-azure-set-up-source.md)yönergeleri izleyin. 

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="next-steps"></a>Sonraki adımlar
[VMware VM](vmware-azure-tutorial.md) 'lerinin olağanüstü durum kurtarma 'yi Azure 'a ayarlayın.
