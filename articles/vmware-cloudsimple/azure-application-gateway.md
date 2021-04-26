---
title: Azure Application Gateway ile VMware sanal makinelerini kullanma
description: VMware sanal makinelerinde çalışan Web sunucuları için gelen Web trafiğini yönetmek üzere Azure Application Gateway 'in nasıl kullanılacağını açıklar CloudSimple özel bulut ortamını Win
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d14fdc4e2b2b1dfca8194077acc0453808611946
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97897952"
---
# <a name="use-azure-application-gateway-with-vmware-virtual-machines-in-the-cloudsimple-private-cloud-environment"></a>CloudSimple özel bulut ortamındaki VMware sanal makineleri ile Azure Application Gateway kullanma

CloudSimple özel bulut ortamınızdaki VMware sanal makinelerinde çalışan web sunucularınız için gelen Web trafiğini yönetmek üzere Azure Application Gateway kullanabilirsiniz.

Azure Application Gateway 'yi ortak özel karma dağıtımda kullanarak, uygulamalarınıza Web trafiğini yönetebilir, VMware ortamında çalışan hizmetleri için güvenli bir ön uç ve yük devretme TLS işlemi sağlayabilirsiniz. Azure Application Gateway, gelen Web trafiğini, yapılandırılmış kurallara ve sistem durumu araştırmalara göre VMware ortamlarında bulunan arka uç havuzu örneklerine yönlendirir.

Bu Azure Application Gateway çözümü şunları yapmanızı gerektirir:

* Bir Azure aboneliğine sahip olmanız.
* Bir Azure sanal ağı ve sanal ağ içinde bir alt ağ oluşturun ve yapılandırın.
* CloudSimple özel bulutunuzun ExpressRoute kullanarak NSG kuralları ve vNet 'iniz oluşturun ve yapılandırın.
* Özel bulutunuzu oluşturun & yapılandırın.
* Azure Application Gateway oluşturun & yapılandırın.

## <a name="azure-application-gateway-deployment-scenario"></a>Azure Application Gateway dağıtım senaryosu

Bu senaryoda Azure Application Gateway, Azure sanal ağınızda çalışır. Sanal ağ, bir ExpressRoute bağlantı hattı üzerinden özel buluta bağlanır. Özel buluttaki tüm alt ağların sanal ağ alt ağlarından erişilebilen IP 'si vardır.

![Azure sanal ağ 'da Azure yük dengeleyici](media/load-balancer-use-case.png)

## <a name="how-to-deploy-the-solution"></a>Çözümü dağıtma

Dağıtım işlemi aşağıdaki görevlerden oluşur:

1. [Önkoşulların karşılandığından emin olun](#1-verify-prerequisites)
2. [Azure sanal bağlantınızı özel buluta bağlama](#2-connect-your-azure-virtual-network-to-your-private-cloud)
3. [Azure Application Gateway dağıtma](#3-deploy-an-azure-application-gateway)
4. [Özel bulutunuzda Web sunucusu VM havuzu oluşturma ve yapılandırma](#4-create-and-configure-a-web-server-vm-pool-in-your-private-cloud)

## <a name="1-verify-prerequisites"></a>1. önkoşulları doğrulama

Bu önkoşulların karşılandığından emin olun:

* Bir Azure Resource Manager ve sanal ağ zaten oluşturulmuş.
* Azure sanal ağınız içindeki adanmış bir alt ağ (Application Gateway için) zaten oluşturuldu.
* CloudSimple özel bulutu zaten oluşturulmuş.
* Sanal ağ ve alt ağlar arasında özel buluttaki IP alt ağları arasında bir IP çakışması yoktur.

## <a name="2-connect-your-azure-virtual-network-to-your-private-cloud"></a>2. Azure Sanal ağınızı özel buluta bağlayın

Azure Sanal ağınızı özel buluta bağlamak için bu işlemi izleyin.

1. [CloudSimple portalında ExpressRoute eşleme bilgilerini kopyalayın](virtual-network-connection.md).

2. [Azure sanal ağınız için bir sanal ağ geçidi yapılandırın](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md).

3. [Sanal ağınızı CloudSimple ExpressRoute devresine bağlayın](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription).

4. [Sanal ağınızı ExpressRoute devresine bağlamak için kopyaladığınız eşleme bilgilerini kullanın](virtual-network-connection.md).

## <a name="3-deploy-an-azure-application-gateway"></a>3. bir Azure Application Gateway dağıtma

Bu için ayrıntılı yönergeler, [Azure Portal kullanarak yol tabanlı yönlendirme kurallarına sahip bir uygulama ağ geçidi oluşturma](../application-gateway/create-url-route-portal.md)bölümünde bulunur. Gerekli adımların özeti aşağıda verilmiştir:

1. Aboneliğinizde ve kaynak grubunuzda bir sanal ağ oluşturun.
2. Sanal ağınızda (adanmış alt ağ olarak kullanılmak üzere) bir alt ağ oluşturun.
3. Standart Application Gateway oluştur (isteğe bağlı olarak WAF 'yi etkinleştir): Azure Portal giriş sayfasından,   >    >  sayfanın sol üst tarafındaki kaynak ağı **Application Gateway** ' na tıklayın. Standart SKU 'YU ve boyutu seçin ve Azure aboneliği, kaynak grubu ve konum bilgilerini sağlayın. Gerekirse, bu uygulama ağ geçidi için yeni bir genel IP oluşturun ve sanal ağ ile uygulama ağ geçidi için ayrılmış alt ağ hakkında ayrıntılı bilgi sağlayın.
4. Sanal makineler içeren bir arka uç havuzu ekleyin ve uygulama ağ geçidinize ekleyin.

## <a name="4-create-and-configure-a-web-server-vm-pool-in-your-private-cloud"></a>4. özel bulutunuzda bir Web sunucusu VM havuzu oluşturun ve yapılandırın

VCenter 'da, tercih ettiğiniz işletim sistemi ve Web sunucusu (Windows/IIS veya Linux/Apache gibi) ile VM 'Ler oluşturun. Özel bulutunuzda Web katmanı için belirtilen bir alt ağ/VLAN seçin. Web sunucusu VM 'lerinin en az bir vNIC 'in Web katmanı alt ağında olduğunu doğrulayın.
