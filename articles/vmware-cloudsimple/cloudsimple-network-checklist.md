---
title: CloudSimple-Network denetim listesi ile Azure VMware çözümü
description: CloudSimple tarafından Azure VMware çözümünde ağ CıDR ayırma denetim listesi
author: sharaths-cs
ms.author: dikamath
ms.date: 09/25/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: bfb170036293dc9f519259dc92737f30380aa84a
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72244819"
---
# <a name="networking-prerequisites-for-azure-vmware-solution-by-cloudsimple"></a>CloudSimple tarafından Azure VMware çözümü için ağ önkoşulları

CloudSimple tarafından sunulan Azure VMware çözümü, şirket içi ortamlardan, kurumsal olarak yönetilen cihazlardan ve Azure kaynaklarından kullanıcılar ve uygulamalar için erişilebilen bir VMware özel bulut ortamı sunar. Bağlantı, VPN 'Ler ve Azure ExpressRoute bağlantıları gibi ağ hizmetleri aracılığıyla dağıtılır. Bu ağ hizmetlerinden bazıları, hizmetleri etkinleştirmek için ağ adresi aralıklarını belirtmenizi gerektirir. 

Bu makaledeki tablolarda, belirtilen adresleri kullanan adres aralıkları ve ilgili hizmetler kümesi açıklanır. Adreslerin bazıları zorunludur ve bazıları dağıtmak istediğiniz hizmetlere bağımlıdır. Bu adres alanları, şirket içi alt ağlarınız, Azure sanal ağ alt ağları veya planlı CloudSimple iş yükü alt ağları ile çakışmamalıdır.

## <a name="network-address-ranges-required-for-creating-a-private-cloud"></a>Özel bulut oluşturmak için gereken ağ adresi aralıkları

CloudSimple hizmeti ve özel bir bulut oluşturma sırasında, belirtilen ağ sınıfsız etki alanları arası yönlendirme (CıDR) aralıklarına aşağıdaki şekilde uymanız gerekir.

| Ad/kullanım     | Açıklama                                                                                                                            | Adres aralığı            |
|-------------------|----------------------------------------------------------------------------------------------------------------------------------------|--------------------------|
| Ağ Geçidi CıDR      | Microsoft Edge Hizmetleri (VPN ağ geçitleri) için gereklidir.  Bu CıDR, CloudSimple hizmeti oluşturma sırasında gereklidir ve RFC 1918 alanından olmalıdır. | /28                      |
| vSphere/vSAN CıDR | VMware yönetim ağları için gereklidir. Bu CıDR, özel bulut oluşturma sırasında belirtilmelidir.                                    | /24 veya/23 veya/22 veya/21 |

## <a name="network-address-range-required-for-azure-network-connection-to-an-on-premises-network"></a>Şirket içi ağa Azure ağ bağlantısı için gereken ağ adresi aralığı

[ExpressRoute aracılığıyla şirket içi bir ağdan özel bulut ağına](on-premises-connection.md) bağlanmak Global Reach bir bağlantı kurar.  Bağlantı, şirket içi ağınız, özel bulut ağınız ve Azure ağlarınız arasındaki yolları Exchange için Sınır Ağ Geçidi Protokolü (BGP) kullanır.

| Ad/kullanım             | Açıklama                                                                                                                                                                             | Adres aralığı |
|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|
| ExpressRoute eşleme CıDR | Şirket içi bağlantı için ExpressRoute Global Reach kullandığınızda gereklidir. Bir destek bileti aracılığıyla Global Reach bağlantı isteği yapıldığında bu CıDR sağlanmalıdır. | /29           |

## <a name="network-address-range-required-for-using-a-site-to-site-vpn-connection-to-an-on-premises-network"></a>Şirket içi ağa siteden siteye VPN bağlantısı kullanmak için gereken ağ adresi aralığı

[Siteden sıteye VPN kullanarak şirket içi bir ağdan özel bulut ağına](vpn-gateway.md) bağlanmak IÇIN aşağıdaki IP adresleri, şirket içi ağ ve tanımlayıcılar gerekir. 

| Adres/adres aralığı | Açıklama                                                                                                                                                                                                                                                           |
|-----------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Eş IP               | Şirket içi VPN ağ geçidi genel IP adresi. Şirket içi veri merkezi ve CloudSimple hizmet bölgesi arasında siteden siteye VPN bağlantısı kurmak için gereklidir. Siteden siteye VPN ağ geçidi oluşturma işlemi sırasında bu IP adresi gereklidir.                                         |
| Eş tanımlayıcı       | Şirket içi VPN ağ geçidinin eş tanımlayıcısı. Bu genellikle **eş IP**ile aynıdır.  Şirket içi VPN ağ geçidiniz üzerinde benzersiz bir tanımlayıcı belirtilmişse, tanımlayıcı belirtilmelidir.  Siteden siteye VPN ağ geçidi oluşturma sırasında eş KIMLIĞI gereklidir.   |
| Şirket içi ağlar   | Bölgede CloudSimple ağlarına erişmesi gereken şirket içi ön ekler.  Kullanıcıların ağa erişebileceği istemci ağı da dahil olmak üzere CloudSimple ağına erişecek şirket içi bir ağdan tüm ön ekleri dahil edin.                                         |

## <a name="network-address-range-required-for-using-point-to-site-vpn-connections"></a>Noktadan siteye VPN bağlantıları kullanmak için gereken ağ adresi aralığı

Noktadan siteye VPN bağlantısı, bir istemci makinesinden CloudSimple ağına erişim sağlar.  [Noktadan sıteye VPN ayarlamak için](vpn-gateway.md)aşağıdaki ağ adresi aralığını belirtmeniz gerekir.

| Adres/adres aralığı | Açıklama                                                                                                                                                                                                                                                                                                  |
|-----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| İstemci alt ağı         | Noktadan siteye VPN kullanarak bağlandığınızda, DHCP adresleri istemci alt ağı tarafından sağlanır. Bu alt ağ, CloudSimple portalında Noktadan siteye VPN ağ geçidi oluştururken gereklidir.  Ağ iki alt ağa ayrılmıştır; biri UDP bağlantısı ve diğeri de TCP bağlantıları için. |

## <a name="next-steps"></a>Sonraki adımlar

* [Özel bulutunuzun erişimine yönelik şirket içi güvenlik duvarı kurulumu](on-premises-firewall-configuration.md)
* [Hızlı başlangıç-CloudSimple hizmeti oluşturma](quickstart-create-cloudsimple-service.md)
* [Hızlı başlangıç-özel bir bulut yapılandırma](quickstart-create-private-cloud.md)
* [Azure ağ bağlantıları](cloudsimple-azure-network-connection.md) hakkında daha fazla bilgi
* [VPN ağ geçitleri](cloudsimple-vpn-gateways.md) hakkında daha fazla bilgi
