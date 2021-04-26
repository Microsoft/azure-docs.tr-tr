---
title: Kavramlar-API Management
description: API Management Azure VMware Çözüm sanal makinelerinde (VM) çalışan API 'Leri nasıl koruduğunu öğrenin
ms.topic: conceptual
ms.date: 10/27/2020
ms.openlocfilehash: 958cc52c48d1121a69dca2fc901289ad1ed671cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94541972"
---
# <a name="api-management-to-publish-and-protect-apis-running-on-azure-vmware-solution-based-vms"></a>Azure VMware Çözüm tabanlı VM 'lerde çalışan API 'Leri yayımlamak ve korumak için API Management

Microsoft Azure [API Management](https://azure.microsoft.com/services/api-management/) iç veya dış tüketicilere güvenli bir şekilde yayımlama olanağı sağlar.  Yalnızca geliştirici ve Premium SKU 'Ları Azure sanal ağ tümleştirmesinin Azure VMware Çözüm iş yükleri üzerinde çalışan API 'Leri yayımlamasına izin verir.  Her iki SKU da API Management hizmeti ile arka uç arasındaki bağlantıyı güvenli bir şekilde etkinleştirir. 

>[!NOTE]
>Geliştirici SKU 'su, Premium SKU, üretim dağıtımları için olduğu sürece geliştirme ve test amaçlıdır.

API Management yapılandırması, Azure VMware Çözüm sanal makinelerinin (VM 'Ler) ve şirket içinde çalışan arka uç hizmetleri için aynıdır. Her iki dağıtımda de API Management, arka uç sunucusu Azure VMware çözümünde bir NSX Load Balancer arkasına yerleştirildiğinde, yük dengeleyicideki sanal IP 'yi (VIP) arka uç uç noktası olarak yapılandırır. 


## <a name="external-deployment"></a>Dış dağıtım

Dış dağıtım, dış kullanıcılar tarafından tüketilen API 'Leri ortak bir uç nokta kullanarak yayımlar. Geliştiriciler ve DevOps mühendisleri, API 'Leri Azure portal veya PowerShell aracılığıyla ve API Management geliştirici portalından yönetebilir.

Dış dağıtım diyagramı, tüm işlemi ve ilgili aktörleri (en üstte gösterilen) gösterir. Aktörler şunlardır:

- **Yönetici:** PowerShell veya Azure DevOps gibi Azure portal ve otomasyon mekanizmaları aracılığıyla Azure VMware çözümünü yöneten yönetici veya DevOps ekibini temsil eder.

- **Kullanıcılar:**  , Sunulan API tüketicilerini temsil eder ve API 'Leri kullanan kullanıcı ve hizmetleri temsil eder.

Trafik akışı, hub sanal ağına takılmış arka uç hizmetlerini soyutlayan API Management örneğinden geçer. ExpressRoute ağ geçidi, trafiği ExpressRoute Global Reach kanalına yönlendirir ve gelen trafiği farklı arka uç hizmetleri örneklerine dağıtmaya yönelik bir NSX 'e ulaşır Load Balancer.

API Management Azure genel API 'sine sahiptir ve Azure DDOS koruma hizmeti 'nin etkinleştirilmesi önerilir. 

:::image type="content" source="media/api-management/external-deployment.png" alt-text="Dış dağıtım-Azure VMware çözümü için API Management":::


## <a name="internal-deployment"></a>İç dağıtım

İç dağıtım, dahili kullanıcılar veya sistemler tarafından kullanılan API 'Leri yayımlar. DevOps ekibi ve API geliştiricileri, dış dağıtımda olduğu gibi aynı yönetim araçlarını ve geliştirici portalını kullanır.

İç dağıtımlar, API için genel ve güvenli bir uç nokta oluşturmak üzere [Azure Application Gateway ile](../api-management/api-management-howto-integrate-internal-vnet-appgateway.md) yapılabilir.  Ağ geçidinin özellikleri, farklı senaryolar sağlayan karma bir dağıtım oluşturmak için kullanılır.  

* Hem iç hem de dış tüketicilerle tüketim için aynı API Management kaynağını kullanın.

* Dış tüketiciler için tanımlanmış ve kullanılabilir bir API alt kümesiyle tek bir API Management kaynağına sahip olmak.

* Genel internet 'ten API Management erişimi açmak ve kapatmak için kolay bir yol sağlar.

Aşağıdaki dağıtım diyagramı, iç veya dış olabilecek tüketicileri gösterir. her tür aynı veya farklı API 'Lere erişiyor.

İç dağıtımda API 'Ler aynı API Management örneğine sunulur. API Management önünde Application Gateway Azure Web uygulaması güvenlik duvarı (WAF) özelliği etkinleştirilmiş olarak dağıtılır. Ayrıca, Azure VMware çözümünde çalışan arka uç hizmetlerinin yalnızca bir alt kümesini açığa çıkarmak üzere trafiği filtrelemek için bir HTTP dinleyicileri ve kuralları kümesi de dağıtılır.


* ExpressRoute ağ geçidi ile Azure Güvenlik Duvarı arasındaki iç trafik rotaları ve API Management, doğrudan veya trafik kuralları aracılığıyla.   

* Dış trafik, API Management için dış koruma katmanını kullanan Application Gateway üzerinden Azure 'u girer.


:::image type="content" source="media/api-management/internal-deployment.png" alt-text="İç dağıtım-Azure VMware çözümü için API Management" lightbox="media/api-management/internal-deployment.png":::