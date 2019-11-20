---
title: CloudSimple-Azure ağ bağlantıları tarafından VMware çözümü
description: Azure Sanal ağınızı CloudSimple bölgesi ağınıza bağlama hakkında bilgi edinin
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: cfd4d65b07cf255ac2b60d6bf8376723a997374e
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69972942"
---
# <a name="azure-network-connections-overview"></a>Azure ağ bağlantılarına genel bakış

Bir bölgede CloudSimple hizmeti oluştururken ve düğümleri oluştururken şunları yapabilirsiniz:

* Bir Azure ExpressRoute bağlantı hattı isteyin ve bu bölgedeki CloudSimple ağına ekleyin.
* Azure ExpressRoute kullanarak CloudSimple bölge ağınızı Azure sanal ağınıza veya şirket içi ağınıza bağlayın.
* Özel bulut ortamınızdan Azure aboneliğinizde veya şirket içi ağınızda çalışan hizmetlere erişim sağlayın.

ExpressRoute bağlantısı, düşük gecikme süresine sahip yüksek bant genişliğidir.

## <a name="benefits"></a>Avantajlar

Azure ağ bağlantısı şunları yapmanıza olanak sağlar:

* Özel bulutunuzda sanal makineler için Azure 'u yedekleme hedefi olarak kullanın.
* Özel bulut vSAN veri deposunu şifrelemek için Azure aboneliğinizde KMS sunucuları dağıtın.
* Uygulama ve veritabanı katmanları özel bulutunuzda çalışırken uygulamanın web katmanının genel bulutta çalıştığı karma uygulamaları kullanın.

## <a name="azure-virtual-network-connection"></a>Azure sanal ağ bağlantısı

Özel bulutlar, ExpressRoute kullanarak Azure kaynaklarınıza bağlanabilir.  ExpressRoute bağlantısı, özel bulutunuzda Azure aboneliğinizde çalışan kaynaklara erişmenizi sağlar.  Bu bağlantı, özel bulut ağınızı Azure sanal ağınıza genişletmenizi sağlar.  CloudSimple ağından gelen yollar BGP aracılığıyla Azure sanal ağınızla birlikte değiştirilir.  Sanal ağ eşlemesi yapılandırdıysanız, eşlenen tüm sanal ağlara CloudSimple ağından erişilebilir.

![Sanal ağa Azure ExpressRoute bağlantısı](media/cloudsimple-azure-network-connection.png)

## <a name="expressroute-connection-to-on-premises-network"></a>Şirket içi ağa ExpressRoute bağlantısı

Mevcut Azure ExpressRoute devrenizi CloudSimple bölgenize bağlayabilirsiniz. ExpressRoute Global Reach özelliği, iki devreleri birbirleriyle bağlamak için kullanılır.  Şirket içi ve CloudSimple ExpressRoute devreleri arasında bir bağlantı oluşturulur.  Bu bağlantı, şirket içi ağlarınızı özel bulut ağına genişletmenizi sağlar. CloudSimple ağınızdan alınan yollar, şirket içi ağınızla BGP aracılığıyla değiştirilir.

![Şirket içi ExpressRoute bağlantısı-Global Reach](media/cloudsimple-global-reach-connection.png)

## <a name="connection-to-on-premises-network-and-azure-virtual-network"></a>Şirket içi ağ ve Azure sanal ağı bağlantısı

Şirket içi ağ ve Azure sanal ağı bağlantıları, CloudSimple ağınızdan birlikte bulunamaz.  Bağlantı, şirket içi ağ, Azure sanal ağı ve CloudSimple ağı arasındaki yolları Exchange için BGP kullanır.  CloudSimple ağınızı Global Reach bağlantısı olan Azure sanal ağınıza bağladığınızda, Azure sanal ağ yolları şirket içi ağınızda görünür olur.  Yönlendirme değişimi, Azure 'da Edge yönlendiricileri arasında gerçekleşir.

![Azure sanal ağ bağlantısıyla şirket içi ExpressRoute bağlantısı](media/cloudsimple-global-reach-and-vnet-connection.png)

### <a name="important-considerations"></a>Önemli konular

Şirket içi ağdan ve Azure sanal ağından CloudSimple ağına bağlanmak, Exchange 'e tüm ağlar arasında yönlendirme sağlar.

* Azure sanal ağ, hem şirket içi ağ hem de CloudSimple ağı tarafından görülebilir.
* Azure sanal ağınıza şirket içi ağdan bağlandıysanız, Global Reach kullanarak CloudSimple ağına bağlantı, CloudSimple ağından sanal ağlara erişime izin verir.
* Alt ağ adresleri bağlı ağların hiçbiriyle çakışmamalıdır.
* CloudSimple, ExpressRoute bağlantılarına varsayılan yolu duyurmayacak
* Şirket içi yönlendiricinizde varsayılan yol bildiriliyorsa, CloudSimple Network ve Azure sanal ağından gelen trafik tanıtılan varsayılan yolu kullanacaktır.  Sonuç olarak, Azure 'daki sanal makinelere genel IP adresleri kullanılarak erişilemez.

## <a name="next-steps"></a>Sonraki adımlar

* [ExpressRoute kullanarak Azure sanal ağını CloudSimple 'a bağlama](virtual-network-connection.md)
* [ExpressRoute kullanarak şirket içinden CloudSimple 'a bağlanma](on-premises-connection.md)
