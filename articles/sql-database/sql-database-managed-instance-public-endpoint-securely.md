---
title: Güvenli yönetilen örnek genel uç noktaları
description: Yönetilen bir örnekle Azure 'da genel uç noktaları güvenli bir şekilde kullanın
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto, carlrab
ms.date: 05/08/2019
ms.openlocfilehash: 6dfeab3530445f8f9a102f47039d15b04fdf134a
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73821731"
---
# <a name="use-an-azure-sql-database-managed-instance-securely-with-public-endpoints"></a>Genel uç noktalarla güvenli bir şekilde Azure SQL veritabanı yönetilen örneği kullanma

Azure SQL veritabanı yönetilen örnekleri, [genel uç noktalar](../virtual-network/virtual-network-service-endpoints-overview.md)üzerinden Kullanıcı bağlantısı sağlayabilir. Bu makalede, bu yapılandırmanın nasıl daha güvenli hale kullanılacağı açıklanmaktadır.

## <a name="scenarios"></a>Senaryolar

SQL veritabanı yönetilen örneği, sanal ağının içinden bağlantıya izin veren özel bir uç nokta sağlar. Varsayılan seçenek, maksimum yalıtımı sağlamaktır. Ancak, genel uç nokta bağlantısı sağlamanız gereken senaryolar vardır:

- Yönetilen örnek, tek kiracılı hizmet olarak platform (PaaS) teklifleriyle tümleştirmelidir.
- VPN kullanırken mümkün olandan daha yüksek miktarda veri alışverişi yapmanız gerekir.
- Şirket ilkeleri, şirket ağları içinde PaaS 'yi yasaklar.

## <a name="deploy-a-managed-instance-for-public-endpoint-access"></a>Ortak uç nokta erişimi için yönetilen bir örnek dağıtma

Zorunlu olmasa da, genel uç nokta erişimiyle yönetilen bir örnek için ortak dağıtım modeli, örneği ayrılmış bir yalıtılmış sanal ağda oluşturmaktır. Bu yapılandırmada, sanal ağ yalnızca sanal küme yalıtımı için kullanılır. Yönetilen örneğin IP adresi alanının bir kurumsal ağın IP adresi alanıyla örtüştüğü ne olursa olsun.

## <a name="secure-data-in-motion"></a>Hareket halindeki verileri güvenli hale getirme

İstemci sürücüsü şifrelemeyi destekliyorsa, yönetilen örnek veri trafiği her zaman şifrelenir. Yönetilen örnek ile diğer Azure sanal makineleri veya Azure hizmetleri arasında gönderilen veriler hiçbir şekilde Azure 'un omurgasını bırakır. Yönetilen örnek ve şirket içi ağ arasında bağlantı varsa, Azure ExpressRoute 'u Microsoft eşlemesiyle kullanmanızı öneririz. ExpressRoute, verileri genel İnternet üzerinden taşımadan kaçınmanıza yardımcı olur. Yönetilen örnek özel bağlantısı için yalnızca özel eşleme kullanılabilir.

## <a name="lock-down-inbound-and-outbound-connectivity"></a>Gelen ve giden bağlantıyı kilitle

Aşağıdaki diyagramda önerilen güvenlik yapılandırması gösterilmektedir:

![Gelen ve giden bağlantıyı kilitlemek için güvenlik yapılandırması](media/sql-database-managed-instance-public-endpoint-securely/managed-instance-vnet.png)

Yönetilen bir örnek adanmış bir [genel uç nokta adresine](sql-database-managed-instance-find-management-endpoint-ip-address.md)sahiptir. İstemci tarafı giden güvenlik duvarında ve ağ güvenlik grubu kurallarında, giden bağlantıyı sınırlamak için bu genel uç nokta IP adresini ayarlayın.

Yönetilen örneğe giden trafiğin güvenilen kaynaklardan geldiğinden emin olmak için, iyi bilinen IP adreslerine sahip kaynaklardan bağlanmanız önerilir. 3342 numaralı bağlantı noktasında yönetilen örnek genel uç noktasına erişimi sınırlandırmak için bir ağ güvenlik grubu kullanın.

İstemcilerin şirket içi bir ağdan bağlantı başlatması gerektiğinde, kaynak adresin iyi bilinen bir IP adresi kümesine çevrildiğinden emin olun. Bunu yapmazsanız (örneğin, bir mobil iş gücünün tipik bir senaryo olması halinde), [noktadan sıteye VPN bağlantıları ve özel uç nokta](sql-database-managed-instance-configure-p2s.md)kullanmanızı öneririz.

Azure 'dan bağlantı başlatılırsa, trafiğin iyi bilinen bir atanan [sanal IP adresinden](../virtual-network/virtual-networks-reserved-public-ip.md) (örneğin, bir sanal makine) gelmesini öneririz. Sanal IP (VIP) adreslerini yönetmeyi daha kolay hale getirmek için [genel IP adresi öneklerini](../virtual-network/public-ip-address-prefix.md)kullanmak isteyebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

- Örnekleri yönetmek için genel uç noktayı yapılandırma hakkında bilgi edinin: [ortak uç noktayı yapılandırma](sql-database-managed-instance-public-endpoint-configure.md)
