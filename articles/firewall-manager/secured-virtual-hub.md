---
title: Güvenli sanal hub nedir?
description: Güvenli sanal hub 'lar hakkında bilgi edinin
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: victorh
ms.openlocfilehash: d93c3b47c518962bf45762b846eaf53e490ae8f8
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73518443"
---
# <a name="what-is-a-secured-virtual-hub"></a>Güvenli sanal hub nedir?

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Sanal hub, diğer kaynaklardan bağlantı sağlayan, Microsoft tarafından yönetilen bir sanal ağ. Azure portal sanal bir WAN 'dan sanal bir hub oluşturulduğunda, bileşenleri olarak bir sanal hub VNet ve ağ geçitleri (isteğe bağlı) oluşturulur.

*Güvenli* bir sanal hub, Azure Güvenlik Duvarı Yöneticisi tarafından yapılandırılan ilişkili güvenlik ve yönlendirme ilkelerine sahip bir [Azure sanal WAN hub 'ına](../virtual-wan/virtual-wan-about.md#resources) sahiptir. Trafik yönetimi ve koruması için yerel güvenlik hizmetleriyle hub ve bağlı bileşen ve geçişli mimarilerin kolayca oluşturulmasını sağlamak için güvenli sanal hub 'ları kullanın. 

Şirket içi bağlantı olmadan yönetilen merkezi VNet olarak güvenli bir sanal hub kullanabilirsiniz. Daha önce bir Azure Güvenlik Duvarı dağıtımı için gerekli olan merkezi VNet 'in yerini alır. Güvenli sanal hub otomatik yönlendirme sağladığından, trafiği güvenlik duvarınız üzerinden yönlendirmek için kendi UDRs 'nizi (Kullanıcı tanımlı yollar) yapılandırmanız gerekmez.

Tam sanal WAN mimarisinin bir parçası olarak güvenli sanal hub 'ları kullanmak da mümkündür. Bu mimari, Azure ile ve arasında güvenli, iyileştirilmiş ve otomatik bir dal bağlantısı sağlar. Azure Güvenlik Duvarı ve hizmet olarak diğer üçüncü taraf güvenlik (SECaaS) sağlayıcıları da dahil olmak üzere ağ trafiğinizi korumak ve yönetmek için Hizmetleri seçebilirsiniz.

## <a name="create-a-secured-virtual-hub"></a>Güvenli sanal hub oluşturma

Azure portal güvenlik duvarı Yöneticisi 'Ni kullanarak, yeni bir güvenli sanal hub oluşturabilir ya da daha önce Azure sanal WAN kullanarak oluşturduğunuz var olan bir sanal hub 'ı dönüştürebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Güvenli bir sanal hub oluşturmak ve bir hub ve bağlı ağ ağını güvenli hale getirmek ve yönetmek için kullanmak üzere, [Azure Portal kullanarak bkz. Öğretici: bulut ağınızı Azure Güvenlik Duvarı Yöneticisi Ile güvenli hale getirme](secure-cloud-network.md).