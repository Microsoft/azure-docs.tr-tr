---
title: Güvenli sanal merkez nedir?
description: Güvenli sanal merkezler hakkında bilgi edinin
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 10/12/2020
ms.author: victorh
ms.openlocfilehash: c840bf9e82b8dcdb1fbf9b380ea847b3d1b08dd9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91948077"
---
# <a name="what-is-a-secured-virtual-hub"></a>Güvenli sanal merkez nedir?

Sanal hub, diğer kaynaklardan bağlantı sağlayan, Microsoft tarafından yönetilen bir sanal ağ. Azure portal sanal bir WAN 'dan sanal bir hub oluşturulduğunda, bileşenleri olarak bir sanal hub VNet ve ağ geçitleri (isteğe bağlı) oluşturulur.

*Güvenli* bir sanal hub, Azure Güvenlik Duvarı Yöneticisi tarafından yapılandırılan ilişkili güvenlik ve yönlendirme ilkelerine sahip bir [Azure sanal WAN hub 'ına](../virtual-wan/virtual-wan-about.md#resources) sahiptir. Trafik yönetimi ve koruması için yerel güvenlik hizmetleriyle hub ve bağlı bileşen ve geçişli mimarilerin kolayca oluşturulmasını sağlamak için güvenli sanal hub 'ları kullanın. 

Sanal ağlar (V2V), sanal ağlar ve şube ofisleri (B2V) ile Internet arasındaki trafiği filtrelemek için güvenli bir sanal hub kullanabilirsiniz (B2I/V2I). Güvenli bir sanal hub otomatik yönlendirme sağlar. Trafiği güvenlik duvarınız üzerinden yönlendirmek için kendi UDRs 'nizi (Kullanıcı tanımlı yollar) yapılandırmaya gerek yoktur.

Azure Güvenlik Duvarı, üçüncü taraf güvenlik hizmeti (SECaaS) sağlayıcıları veya her ikisi de dahil olmak üzere ağ trafiğinizi korumak ve yönetmek için gerekli güvenlik sağlayıcılarını seçebilirsiniz. Şu anda, güvenli bir hub, dal (B2B) filtreleme ve birden çok hub arasında filtreleme işlemlerini desteklemez. Daha fazla bilgi edinmek için bkz. [Azure Güvenlik Duvarı Yöneticisi nedir?](overview.md#known-issues). 

## <a name="create-a-secured-virtual-hub"></a>Güvenli sanal hub oluşturma

Azure portal güvenlik duvarı Yöneticisi 'Ni kullanarak, yeni bir güvenli sanal hub oluşturabilir ya da daha önce Azure sanal WAN kullanarak oluşturduğunuz var olan bir sanal hub 'ı dönüştürebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Güvenli bir sanal hub oluşturmak ve bir hub ve bağlı ağ ağını güvenli hale getirmek ve yönetmek için kullanmak üzere, [Azure Portal kullanarak bkz. Öğretici: bulut ağınızı Azure Güvenlik Duvarı Yöneticisi Ile güvenli hale getirme](secure-cloud-network.md).