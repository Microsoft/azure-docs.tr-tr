---
title: Redsıs ağ yalıtımı seçenekleri için Azure önbelleği
description: Bu makalede, gereksinimlerinize uygun en iyi ağ yalıtımı çözümünü nasıl belirleyebileceğinizi öğreneceksiniz. Azure özel bağlantısı, Azure sanal ağ (VNet) ekleme ve Azure Güvenlik Duvarı kurallarının avantajları ve sınırlamaları ile ilgili temel bilgileri inceleyeceğiz.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: ef284661d44f700cf0b5282efcd2e6f7b94fa3b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96621527"
---
# <a name="azure-cache-for-redis-network-isolation-options"></a>Redsıs ağ yalıtımı seçenekleri için Azure önbelleği 
Bu makalede, gereksinimlerinize uygun en iyi ağ yalıtımı çözümünü nasıl belirleyebileceğinizi öğreneceksiniz. Azure özel bağlantısı, Azure sanal ağ (VNet) ekleme ve Azure Güvenlik Duvarı kurallarının avantajları ve sınırlamaları ile ilgili temel bilgileri inceleyeceğiz.  

## <a name="azure-private-link-public-preview"></a>Azure özel bağlantısı (Genel Önizleme) 
Azure özel bağlantısı, bir sanal ağdan Azure PaaS hizmetlerine özel bağlantı sağlar. Ağ mimarisini basitleştirir ve genel İnternet 'teki verileri açığa kaldırarak Azure 'daki uç noktalar arasındaki bağlantıyı korur. 

### <a name="advantages"></a>Avantajlar
* Redsıs örnekleri için temel, standart ve Premium Azure önbelleğinde desteklenir. 
* [Azure özel bağlantısı](../private-link/private-link-overview.md)' nı kullanarak, sanal ağ Içindeki bir Azure önbellek örneğine, sanal ağ içindeki bir alt ağda özel bir IP adresi atanmış özel bir uç nokta aracılığıyla bağlanabilirsiniz. Bununla birlikte, önbellek örnekleri hem VNet içinde hem de genel olarak kullanılabilir.  
* Özel bir uç nokta oluşturulduktan sonra, genel ağa erişim bayrağı aracılığıyla kısıtlanabilir `publicNetworkAccess` . Bu bayrak `Disabled` Varsayılan olarak olarak ayarlanır ve yalnızca özel bağlantı erişimine izin verir. Değeri `Enabled` BIR yama isteğiyle veya olarak ayarlayabilirsiniz `Disabled` . Daha fazla bilgi için bkz. [Azure özel bağlantısı (Önizleme) Ile Reda Için Azure önbelleği](cache-private-link.md). 
* Tüm dış önbellek bağımlılıkları VNet 'in NSG kurallarını etkilemez.

### <a name="limitations"></a>Sınırlamalar 
* Ağ güvenlik grupları (NSG) özel uç noktalar için devre dışıdır. Ancak, alt ağda başka kaynaklar varsa NSG zorlaması bu kaynaklara uygulanır.
* Coğrafi çoğaltma, güvenlik duvarı kuralları, Portal konsolu desteği, kümelenmiş önbellek başına birden fazla uç nokta, güvenlik duvarı ve VNet 'e eklenen önbellekler henüz desteklenmiyor. 
* Kümelenmiş bir önbelleğe bağlanmak için, `publicNetworkAccess` olarak ayarlanması gerekir `Disabled` ve yalnızca bir özel uç nokta bağlantısı olabilir.

> [!NOTE]
> Bir önbellek örneğine özel bir uç nokta eklenirken, DNS nedeniyle tüm redin trafiği özel uç noktaya taşınır.
> Önceki Güvenlik Duvarı kurallarının daha önce ayarlanmış olduğundan emin olun.  
>
>

## <a name="azure-virtual-network-injection"></a>Azure sanal ağ ekleme 
VNet, Azure 'daki özel ağınız için temel yapı taşdır. VNet birçok Azure kaynağını birbirleriyle, internet ve şirket içi ağlarla güvenli bir şekilde iletişim kurmasına olanak sağlar. VNet, kendi veri merkezinizde çalışdığınız geleneksel bir ağ gibidir, ancak Azure altyapısı, ölçek, kullanılabilirlik ve yalıtımın avantajlarından yararlanabilir. 

### <a name="advantages"></a>Avantajlar
* Redsıs örneği için bir Azure önbelleği bir sanal ağ ile yapılandırıldığında, bu, genel olarak adreslenebilir ve yalnızca VNet içindeki sanal makineler ve uygulamalardan erişilebilir.  
* VNet kısıtlanmış NSG ilkeleriyle birleştirildiğinde, veri kaybı riskini azaltmaya yardımcı olur. 
* VNet dağıtımı, Azure önbelleğiniz için gelişmiş güvenlik ve yalıtımın yanı sıra alt ağlar, erişim denetim ilkeleri ve erişimi daha da kısıtlamak için diğer özellikleri sağlar. 
* Coğrafi çoğaltma desteklenir. 

### <a name="limitations"></a>Sınırlamalar
* VNet eklenen önbellekler yalnızca redin için Premium Azure önbelleği için kullanılabilir. 
* VNet 'e eklenen önbellek kullanılırken, sanal ağı, CRL 'Ler/PKI, AKV, Azure depolama, Azure Izleyici gibi bağımlılıkları önbelleğe almak için açmanız gerekir.  


## <a name="azure-firewall-rules"></a>Azure Güvenlik duvarı kuralları
[Azure Güvenlik Duvarı](../firewall/overview.md) , Azure VNET kaynaklarınızı koruyan, yönetilen, bulut tabanlı bir ağ güvenlik hizmetidir. Yerleşik yüksek kullanılabilirliğe sahip ve Kısıtlanmamış bulut ölçeklenebilirliğine sahip bir hizmet olarak tam durum bilgisi olmayan bir güvenlik duvarıdır. Aboneliklerle sanal ağlarda uygulama ve ağ bağlantısı ilkelerini merkezi olarak oluşturabilir, zorlayabilir ve günlüğe alabilirsiniz.  

### <a name="advantages"></a>Avantajlar
* Güvenlik duvarı kuralları yapılandırıldığında, yalnızca belirtilen IP adresi aralıklarından gelen istemci bağlantıları önbelleğe bağlanabilir. Güvenlik duvarı kuralları yapılandırılmış olsa bile, Redsıs izleme sistemlerine yönelik Azure önbelleğinden gelen bağlantılara her zaman izin verilir. Tanımladığınız NSG kurallarına da izin verilir.  

### <a name="limitations"></a>Sınırlamalar
* Güvenlik duvarı kuralları, şu anda özel uç noktalar değil, VNet eklenen önbellekler ile birlikte kullanılabilir. 


## <a name="next-steps"></a>Sonraki adımlar
* [Redsıs örneği Için Premium bir Azure önbelleği Için VNET 'e eklenmiş önbelleğin](cache-how-to-premium-vnet.md)nasıl yapılandırılacağını öğrenin.  
* [Redsıs katmanları için tüm Azure önbelleği için güvenlik duvarı kurallarını](cache-configure.md#firewall)yapılandırmayı öğrenin. 
* [Özel uç noktaları redsıs katmanları için tüm Azure önbelleğinde yapılandırmayı](cache-private-link.md)öğrenin.