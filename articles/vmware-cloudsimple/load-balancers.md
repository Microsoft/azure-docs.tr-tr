---
title: CloudSimple tarafından Azure VMware çözümü-CloudSimple özel bulutları için bir yük dengeleme çözümü seçin
description: Özel bir bulutta uygulama dağıtma Yük Dengeleme seçeneklerini açıklar
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f6fc5112f7106c6cc8f8736237ce803da43cd882
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69881053"
---
# <a name="choose-a-load-balancing-solution-for-cloudsimple-private-clouds"></a>CloudSimple özel bulutları için bir yük dengeleme çözümü seçin

Bir CloudSimple özel bulutu 'nda bir uygulama dağıttığınızda, yük dengeleme için birkaç seçenekten birini seçebilirsiniz.

CloudSimple özel bulutunuzda bir sanal veya yazılım tabanlı yük dengeleyici seçebilir veya Azure aboneliğinizde çalışan Azure L7 yük dengeleyiciyi kullanarak CloudSimple özel bulutu 'nda çalışan Web katmanı sanal makinelerinizi ön uca ekleyebilirsiniz. Burada birkaç seçenek listeliyoruz:

## <a name="virtual-load-balancers"></a>Sanal yük dengeleyiciler

VMware ortamınızda sanal yük dengeleyici gereçlerini vCenter arabiriminden dağıtabilir ve uygulama trafiğinizi ön uca yapılandırabilirsiniz.

Popüler satıcıların bazıları şunlardır: NGINX http://nginx.org/en/docs/http/load_balancing.html F5-BigIP-Traffic Manager: https://www.f5.com/products/big-ip-services/virtual-editions Citrix ADC: https://www.citrix.com/products/citrix-adc/

## <a name="azure-l7-load-balancer"></a>Azure L7 yük dengeleyici

Azure Application Gateway 'i özel bir bulutta çalışan uygulamanız için bir L7 yük dengeleyici olarak kullandığınızda, yük dengeleyici yazılımını yönetmeniz gerekmez. Yük dengeleyici yazılımı Azure tarafından yönetilir. Özel buluttaki tüm Web katmanı VM 'Leri özel IP adresleri kullanır ve adları çözümlemek için ek NAT kuralları ya da genel IP adresleri gerektirmez. Web katmanı VM 'Leri, Azure Application Gateway ile özel, düşük gecikmeli ve yüksek bant genişliğine sahip bir bağlantı üzerinden iletişim kurar.

Bu çözümü yapılandırma hakkında daha fazla bilgi edinmek için Azure Application Gateway 'yi bir L7 yük dengeleyici olarak kullanmaya yönelik çözüm kılavuzuna bakın.

## <a name="azure-internal-load-balancer"></a>Azure iç yük dengeleyici

Uygulamanızı, Web ön uç katmanının Azure aboneliğinizdeki bir Azure sanal ağı içinde çalıştığı karma dağıtımda çalıştırmayı tercih ederseniz ve uygulamanın DB katmanı CloudSimple özel bulutundaki VMware VM 'lerinde çalışıyorsa, Azure iç yükünü kullanabilirsiniz trafik yönetimi için VERITABANı katmanı sanal makinelerinizin önünde yer alarak dengeleyici (L4 yük dengeleyici).

Daha fazla bilgi için bkz. Azure [iç Load Balancer](../load-balancer/load-balancer-overview.md#internalloadbalancer) belgeleri.

## <a name="global-server-load-balancer"></a>Küresel sunucu yük dengeleyici

DNS tabanlı yük dengeleyici arıyorsanız, Azure Marketi 'nde bulunan üçüncü taraf çözümlerini kullanabilir ya da yerel Azure çözümüne gidebilirsiniz.

Azure Traffic Manager, genel Azure bölgelerinde ve şirket içinde bulunan hizmetlere en uygun trafiği dağıtmanıza olanak tanıyan, yüksek kullanılabilirlik ve yanıt verme olanağı sunan bir DNS tabanlı trafik yük dengeleyicidir. Daha fazla bilgi için bkz. Azure [Traffic Manager](../traffic-manager/traffic-manager-configure-geographic-routing-method.md) belgeleri.
