---
title: VMware olağanüstü durum kurtarma-Azure Site Recovery yapılandırma sunucusu gereksinimleri
description: Bu makalede, Azure Site Recovery ile Azure 'a VMware olağanüstü durum kurtarma için yapılandırma sunucusu dağıtma desteği ve gereksinimleri açıklanır
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.date: 09/09/2019
ms.author: raynew
ms.openlocfilehash: 0b0942b517c8dc83c048bd1203a58d9861515dfb
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73663040"
---
# <a name="configuration-server-requirements-for-vmware-disaster-recovery-to-azure"></a>Azure 'da VMware olağanüstü durum kurtarma için yapılandırma sunucusu gereksinimleri

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
