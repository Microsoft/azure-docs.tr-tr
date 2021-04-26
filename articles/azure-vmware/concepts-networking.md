---
title: Kavramlar-ağ bağlantısı
description: Azure VMware çözümünde önemli yönleri ve ağ ve bağlantı durumlarını kullanma hakkında bilgi edinin.
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: a6c86c0a9eb8e07a91a094ddf5d6fb77b0eddf67
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107871696"
---
# <a name="azure-vmware-solution-networking-and-interconnectivity-concepts"></a>Azure VMware Çözüm ağı ve karşılıklı bağlantı kavramları

[!INCLUDE [avs-networking-description](includes/azure-vmware-solution-networking-description.md)]

Azure VMware Çözüm özel bulutu 'nda birbirine bağlanmanın iki yolu vardır:

- [**Temel Azure-yalnızca karşılıklı bağlantı**](#azure-virtual-network-interconnectivity) , özel bulutunuzu yalnızca Azure 'daki tek bir sanal ağla yönetmenizi ve kullanmanızı sağlar. Bu uygulama, Azure VMware Çözüm değerlendirmeleri veya şirket içi ortamlarından erişim gerektirmeyen uygulamalar için idealdir.

- Şirket içinde [**tam ve özel bulut bağlantısı**](#on-premises-interconnectivity) , şirket Içi ve Azure VMware çözümü özel bulutları arasında karşılıklı bağlantı dahil olmak üzere yalnızca temel Azure uygulamasını genişletir.
 
Bu makalede, gereksinimler ve sınırlamalar dahil olmak üzere ağ ve bağlantı kurma temel kavramlarını ele alacağız. Bu makale, ağınızı Azure VMware çözümüyle çalışacak şekilde yapılandırmak için bilmeniz gereken bilgileri sağlar.

## <a name="azure-vmware-solution-private-cloud-use-cases"></a>Azure VMware çözümü özel bulut kullanım örnekleri

Azure VMware çözümü özel bulutları için kullanım örnekleri şunları içerir:
- Buluttaki yeni VMware VM iş yükleri
- VM iş yükü buluta gömülmüş (yalnızca şirket içi Azure VMware çözümüne)
- VM iş yükü buluta geçiş (yalnızca şirket içi Azure VMware çözümüne)
- Olağanüstü durum kurtarma (Azure VMware çözümüne Azure VMware çözümü veya şirket içi Azure VMware çözümüne)
- Azure hizmetleri kullanımı

> [!TIP]
> Azure VMware çözüm hizmeti için tüm kullanım örnekleri, şirket içi ve özel bulut bağlantısı ile etkinleştirilir.

## <a name="azure-virtual-network-interconnectivity"></a>Azure sanal ağ bağlantısı

Azure VMware çözümü özel bulut uygulamasıyla Azure Sanal ağınızı birbirine bağlayabilirsiniz. Azure VMware çözümünüz özel bulutunuzu yönetebilir, özel bulutunuzda iş yüklerini tüketebilir ve diğer Azure hizmetlerine erişebilirsiniz.

Aşağıdaki diyagramda, özel bir bulut dağıtımı sırasında belirlenen temel ağ bağlantısı gösterilmektedir. Azure 'daki bir sanal ağ ile özel bir bulut arasındaki mantıksal ağı gösterir. Bu bağlantı, Azure VMware çözüm hizmeti 'nin bir parçası olan bir arka uç ExpressRoute aracılığıyla oluşturulur. Interconnectivity aşağıdaki birincil kullanım örneklerini karşılar:

- Azure aboneliğinizdeki VM 'lerden erişilebilen vCenter Server ve NSX-T yöneticisine gelen erişim.
- Özel buluttaki VM 'lerden Azure hizmetlerine giden erişim.
- Özel bulutta çalışan iş yüklerinin gelen erişimi.


:::image type="content" source="media/concepts/adjacency-overview-drawing-single.png" alt-text="Temel sanal ağdan özel bulut bağlantısına" border="false":::

## <a name="on-premises-interconnectivity"></a>Şirket içi karşılıklı bağlantı

Tam bağlantılı senaryoda, Azure sanal ağınızdan ve şirket içi Azure VMware çözümüne erişebilirsiniz. Bu uygulama, önceki bölümde açıklanan temel uygulamanın bir uzantısıdır. Şirket içinden Azure 'da Azure VMware çözümünüz özel bulutuna bağlanmak için bir ExpressRoute bağlantı hattı gereklidir.

Aşağıdaki diyagramda, şirket içi ile özel bulut bağlantısı, aşağıdaki kullanım durumlarını sağlar:

- Şirket içi ve Azure VMware çözümü arasında sık/soğuk vCenter vMotion.
- Şirket Içinden Azure VMware çözümüne özel bulut yönetimi erişimi.

:::image type="content" source="media/concepts/adjacency-overview-drawing-double.png" alt-text="Sanal ağ ve şirket içi tam özel bulut bağlantısı" border="false":::

Özel bulutunuzun tam bağlantısı için, ExpressRoute Global Reach etkinleştirmeniz ve sonra Azure portal Global Reach için bir yetkilendirme anahtarı ve özel eşleme KIMLIĞI istemeniz gerekir. Yetkilendirme anahtarı ve eşleme KIMLIĞI, aboneliğinizdeki bir ExpressRoute bağlantı hattı ile özel bulutunuzun ExpressRoute bağlantı hattı arasında Global Reach oluşturmak için kullanılır. Bağlantı kurulduktan sonra, iki ExpressRoute devre dışı, şirket içi ortamlarınızla özel bulutunuz arasında ağ trafiğini yönlendirir. Yordamlar hakkında daha fazla bilgi için bkz. [özel buluta eşleme Global Reach ExpressRoute oluşturma öğreticisi](tutorial-expressroute-global-reach-private-cloud.md).

## <a name="limitations"></a>Sınırlamalar
[!INCLUDE [azure-vmware-solutions-limits](includes/azure-vmware-solutions-limits.md)]

## <a name="next-steps"></a>Sonraki adımlar 

Azure VMware Çözüm ağı ve ınterbağlantı kavramlarını kapsadığınıza göre, şunları öğrenmek isteyebilirsiniz:

- [Azure VMware Çözüm depolama kavramları](concepts-storage.md)
- [Azure VMware Çözüm kimliği kavramları](concepts-identity.md)
- [Azure VMware Çözüm kaynağını etkinleştirme](enable-azure-vmware-solution.md)

<!-- LINKS - external -->
[enable Global Reach]: ../expressroute/expressroute-howto-set-global-reach.md

<!-- LINKS - internal -->
[concepts-upgrades]: ./concepts-upgrades.md
[concepts-storage]: ./concepts-storage.md
