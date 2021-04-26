---
title: CloudSimple tarafından Azure VMware çözümünü yönetmeye yönelik temel kavramlar
titleSuffix: Azure VMware Solution by CloudSimple
description: CloudSimple ile Azure VMware çözümlerini yönetmeye yönelik temel kavramları açıklar
author: Ajayan1008
ms.author: v-hborys
ms.date: 04/24/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e5544ef7725855d28e20d39ff345db6bb07671a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97895334"
---
# <a name="key-concepts-for-administration-of-azure-vmware-solutions-by-cloudsimple"></a>CloudSimple tarafından Azure VMware çözümlerini yönetmek için temel kavramlar

CloudSimple tarafından Azure VMware çözümlerini yönetmek için aşağıdaki kavramların anlaşılmasına gerek vardır:

* CloudSimple hizmeti tarafından Azure VMware çözümleri olarak görünen CloudSimple hizmeti
* CloudSimple düğümü tarafından Azure VMware çözümleri olarak görünen CloudSimple düğümü
* CloudSimple özel bulutu
* Hizmet ağı
* CloudSimple sanal makinesi tarafından Azure VMware çözümleri olarak görünen CloudSimple sanal makinesi

## <a name="cloudsimple-service"></a>CloudSimple hizmeti

CloudSimple hizmeti sayesinde, Azure portal CloudSimple ile VMware çözümleriyle ilişkili tüm kaynakları oluşturabilir ve yönetebilirsiniz. Hizmeti kullanmayı düşündüğünüz her bölgede bir hizmet kaynağı oluşturun.

[Cloudsimple hizmeti](cloudsimple-service.md)hakkında daha fazla bilgi edinin.

## <a name="cloudsimple-node"></a>CloudSimple düğümü

CloudSimple düğümü, VMware ESXi hiper yöneticinin dağıtıldığı adanmış, çıplak ve hiper yakınsanmış bir işlem ve depolama konağından bağımsız bir işlemdir. Daha sonra bu düğüm VMware vSphere, vCenter, vSAN ve NSX platformlarına birleştirilir. CloudSimple ağ hizmetleri ve Edge ağ hizmetleri de etkinleştirilir. Her düğüm, [Cloudsimple özel bulutları](cloudsimple-private-cloud.md)oluşturmak için sağlayabileceğiniz bir işlem ve depolama kapasitesi birimi görevi görür. CloudSimple hizmetinin kullanılabildiği bir bölgede düğüm temin edersiniz veya ayırtın.

[Cloudsimple düğümleri](cloudsimple-node.md)hakkında daha fazla bilgi edinin.

## <a name="cloudsimple-private-cloud"></a>CloudSimple özel bulutu

CloudSimple özel bulutu, kendi yönetim etki alanında bir vCenter sunucusu tarafından yönetilen yalıtılmış bir VMware Stack ortamıdır. VMware yığını ESXi Konakları, vSphere, vCenter, vSAN ve NSX içerir. Yığın adanmış düğümlerde çalışır (ayrılmış ve yalıtılmış çıplak donanım) ve Kullanıcı tarafından vCenter ve NSX Yöneticisi içeren yerel VMware araçları aracılığıyla kullanılır. Adanmış düğümler Azure konumlarında dağıtılır ve Azure tarafından yönetilir. Her özel bulut, VLAN 'Lar ve alt ağlar ve güvenlik duvarı tabloları gibi ağ hizmetleri kullanılarak kesimlere ve güvenli hale getirilir. Şirket içi ortamınıza ve Azure ağına bağlantılar, güvenli, özel VPN ve Azure ExpressRoute bağlantıları kullanılarak oluşturulur.

[Cloudsimple özel bulutu](cloudsimple-private-cloud.md)hakkında daha fazla bilgi edinin.

## <a name="service-networking"></a>Hizmet ağı

CloudSimple hizmeti, CloudSimple hizmetinizin dağıtıldığı bölge başına bir ağ sağlar. Ağ, yönlendirme özelliği varsayılan olarak etkin olan tek bir TCP katman 3 adres alanıdır. Bu bölgede oluşturulan tüm özel bulutlar ve alt ağlar, ek bir yapılandırma olmadan birbirleriyle iletişim kurar. VLAN 'Ları kullanarak, vCenter üzerinde dağıtılmış bağlantı noktası grupları oluşturursunuz. Özel bulutunuzda iş yükü kaynaklarınızı yapılandırmak ve güvenli hale getirmek için aşağıdaki ağ özelliklerini kullanabilirsiniz:

* [VLAN ve alt ağlar](cloudsimple-vlans-subnets.md)
* [Güvenlik duvarı tabloları](cloudsimple-firewall-tables.md)
* [VPN ağ geçitleri](cloudsimple-vpn-gateways.md)
* [Genel IP](cloudsimple-public-ip-address.md)
* [Azure ağ bağlantısı](cloudsimple-azure-network-connection.md)

## <a name="cloudsimple-virtual-machine"></a>CloudSimple sanal makinesi

CloudSimple hizmeti ile VMware sanal makinelerini Azure portal yönetebilirsiniz. VSphere ortamınızdan bir veya daha fazla küme veya kaynak havuzu, hizmetin oluşturulduğu abonelikle eşleştirilebilir.

Aşağıdakiler hakkında daha fazla bilgi edinin:

* [CloudSimple sanal makineleri](cloudsimple-virtual-machines.md)
* [Azure abonelik eşleme](./azure-subscription-mapping.md)
