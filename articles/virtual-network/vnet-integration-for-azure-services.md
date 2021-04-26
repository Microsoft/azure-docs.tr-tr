---
title: Ağ yalıtımı için Azure hizmetlerinin sanal ağ tümleştirmesi
titlesuffix: Azure Virtual Network
description: Bu makalede, Azure hizmetine güvenli bir şekilde erişmenizi sağlayan bir Azure hizmetini bir sanal ağla tümleştirmeyle ilgili farklı yöntemler açıklanır.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 12/01/2020
ms.author: kumud
ms.openlocfilehash: 2c0c4bec93b8fa61275c376fbae2a3a063e72a6f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98785541"
---
# <a name="integrate-azure-services-with-virtual-networks-for-network-isolation"></a>Azure hizmetlerini ağ yalıtımı için sanal ağlarla tümleştirme

Azure hizmeti için sanal ağ (VNet) tümleştirmesi, hizmete erişiminizi yalnızca sanal ağ altyapınızla kilitlemenizi sağlar. VNet altyapısı, eşlenmiş sanal ağları ve şirket içi ağları da içerir.

VNet tümleştirmesi, Azure hizmetlerine ağ yalıtımının avantajlarını sağlar ve aşağıdaki yöntemlerden birini veya daha fazlasını gerçekleştirebilir:
- [Hizmetin adanmış örneklerini bir sanal ağa dağıtma](virtual-network-for-azure-services.md). Daha sonra hizmetlere sanal ağ ve şirket içi ağlardan özel olarak erişilebilir.
- [Azure özel bağlantısı](../private-link/private-link-overview.md)tarafından desteklenen bir hizmete özel ve güvenli bir şekilde bağlanan [Özel uç nokta](../private-link/private-endpoint-overview.md) kullanımı. Özel uç nokta, VNet 'iniz tarafından sanal ağınıza etkin bir şekilde hizmet getiren özel bir IP adresi kullanır.
- Hizmet [uç noktaları](virtual-network-service-endpoints-overview.md)aracılığıyla bir sanal ağı hizmete genişleterek genel uç noktaları kullanarak hizmete erişme. Hizmet uç noktaları, hizmet kaynaklarının sanal ağla güvenliğini sağlar.
- Azure kaynaklarınıza giden ve genel IP uç noktalarına giden trafiğe izin vermek veya reddetmek için [hizmet etiketlerini](service-tags-overview.md) kullanma.

## <a name="deploy-dedicated-azure-services-into-virtual-networks"></a>Adanmış Azure hizmetlerini sanal ağlara dağıtma

Adanmış Azure hizmetlerini bir sanal ağda dağıtırken, özel IP adresleri aracılığıyla hizmet kaynaklarıyla özel olarak iletişim kurabilirsiniz.

![Adanmış Azure hizmetlerini sanal ağlara dağıtma](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

Sanal ağınıza adanmış bir Azure hizmeti dağıtmak aşağıdaki özellikleri sağlar:
- Sanal ağ içindeki kaynaklar özel IP adresleri aracılığıyla birbirleriyle özel olarak iletişim kurabilir. Örnek olarak, sanal ağdaki HDInsight ve bir sanal makinede çalışan SQL Server arasında doğrudan veri aktarımı.
- Şirket içi kaynaklar, bir siteden siteye VPN (VPN Gateway) veya ExpressRoute üzerinden özel IP adresleri kullanarak bir sanal ağdaki kaynaklara erişebilir.
- Sanal ağlar, sanal ağlardaki kaynakların birbirleriyle iletişim kurmasını ve özel IP adreslerini kullanmasını sağlamak için eşlenebilir.
- Bir sanal ağdaki hizmet örnekleri genellikle Azure hizmeti tarafından tam olarak yönetilir. Bu, kaynakların sistem durumunu izlemeyi ve yükleme ile ölçeklendirmeyi içerir.
- Hizmet örnekleri, bir sanal ağ içindeki bir alt ağa dağıtılır. Alt ağ için gelen ve giden ağ erişimi, hizmet tarafından sunulan bir kılavuza göre ağ güvenlik grupları aracılığıyla açılmalıdır.
- Ayrıca, bazı hizmetler dağıtılan alt ağa kısıtlamalar getirerek ilkelerin uygulamasını kısıtlar, VM 'Leri ve hizmet kaynaklarını aynı alt ağ içinde yönlendirir. Belirli kısıtlamaların zaman içinde değiştirebilecekleri her bir hizmete sahip olup olmadığını denetleyin. Bu hizmetlere örnek olarak Azure NetApp Files, adanmış HSM, Azure Container Instances, App Service verilebilir.
- İsteğe bağlı olarak, hizmetler bir alt ağın belirli bir hizmeti barındırabilecek açık bir tanımlayıcı olarak bir temsilci alt ağı gerektirebilir. Temsilci seçerek hizmetler, temsilci alt ağında hizmete özel kaynaklar oluşturmak için açık izinler alır.
- Temsil edilen alt ağa sahip bir sanal ağ üzerinde REST API yanıtı örneğine bir örnek bakın. Temsil edilen alt ağ modelini kullanan hizmetlerin kapsamlı bir listesi, kullanılabilir temsilciler API 'SI aracılığıyla elde edilebilir.

Bir sanal ağa dağıtılabilecek hizmetlerin listesi için bkz. [adanmış Azure hizmetlerini sanal ağlara dağıtma](virtual-network-for-azure-services.md).

## <a name="private-link-and-private-endpoints"></a>Özel bağlantı ve özel uç noktalar

Özel uç noktaları kullanarak doğrudan sanal ağınızdan Azure kaynağına, genel İnternet üzerinden geçmeden özel bir bağlantı üzerinden güvenli bir şekilde olay alma olanağı sağlayabilirsiniz. Özel uç nokta, sanal ağınızdaki bir Azure hizmeti için özel bir ağ arabirimidir. Azure kaynağınız için özel bir uç nokta oluşturduğunuzda, sanal ağınızdaki istemciler ve Azure kaynağınız arasında güvenli bağlantı sağlar. Özel uç noktaya, sanal Ağınızın IP adresi aralığından bir IP adresi atanır. Özel uç nokta ve Azure hizmeti arasındaki bağlantı güvenli bir özel bağlantı kullanır.

Aşağıdaki örnek, bir sanal ağ ve Event Grid kaynaktaki istemciler arasında güvenli bağlantı sağlayan Event Grid kaynağa özel bir uç noktanın özel erişimini gösterir.

![Özel uç nokta kullanılarak SQL DB kaynağına özel erişim](./media/network-isolation/architecture-diagram.png)

Özel bağlantı ve desteklenen Azure hizmetlerinin bir listesi hakkında daha fazla bilgi için bkz. [özel bağlantı nedir?](../private-link/private-link-overview.md)

## <a name="service-endpoints"></a>Hizmet uç noktaları
VNet hizmet uç noktası, Azure omurga ağı üzerinden iyileştirilmiş bir yol üzerinden Azure hizmetlerine güvenli ve doğrudan bağlantı sağlar. Uç noktalar kritik Azure hizmeti kaynaklarınızı sanal ağlarınızla sınırlayarak güvenliğini sağlamanıza imkan verir. Hizmet uç noktaları, VNet 'teki özel IP adreslerinin VNet üzerinde genel bir IP adresi gerekmeden bir Azure hizmeti uç noktasına ulaşmasını sağlar.

![Azure hizmetlerini sanal ağlar ile sınırlama](./media/virtual-network-service-endpoints-overview/VNet_Service_Endpoints_Overview.png)

Daha fazla bilgi için bkz. [sanal ağ hizmeti uç noktaları](virtual-network-service-endpoints-overview.md)

## <a name="service-tags"></a>Hizmet etiketleri

Hizmet etiketi, belirli bir Azure hizmetinden bir IP adresi önekleri grubunu temsil eder. Hizmet etiketlerini kullanarak ağ [güvenlik grupları](./network-security-groups-overview.md#security-rules) veya [Azure Güvenlik Duvarı](../firewall/service-tags.md)'nda ağ erişim denetimleri tanımlayabilirsiniz. Bir kuralın uygun kaynak veya hedef alanındaki hizmet etiketi adını (örneğin, AzureEventGrid) belirterek, karşılık gelen hizmet için trafiğe izin verebilir veya bu trafiği reddedebilirsiniz.

![Hizmet etiketleri kullanarak trafiğe izin ver veya Reddet](./media/network-isolation/service-tags.png)

Hizmet etiketlerini kullanarak, genel uç noktalarından oluşan Azure hizmetlerine erişirken ağ yalıtımı elde edebilir ve Azure kaynaklarınızı genel Internet üzerinden koruyabilirsiniz. **Internet** **'ten gelen** /giden trafiği reddetmek ve belirli Azure hizmetlerinin diğer [kullanılabilir hizmet etiketlerine](service-tags-overview.md#available-service-tags) /sayısına giden trafiğe izin vermek için gelen/giden ağ güvenlik grubu kuralları oluşturun.

Hizmet etiketleri ve bunları destekleyen Azure hizmetleri hakkında daha fazla bilgi için bkz. [hizmet etiketlerine genel bakış](service-tags-overview.md)

## <a name="next-steps"></a>Sonraki adımlar

- [Uygulamanızı bir Azure ağı ile tümleştirmeyi](../app-service/web-sites-integrate-with-vnet.md)öğrenin.
- [Hizmet etiketlerini kullanarak kaynaklara erişimi nasıl kısıtlayacağınızı](tutorial-restrict-network-access-to-resources.md)öğrenin.
- [Azure özel bağlantısını kullanarak bir Azure Cosmos hesabına özel olarak nasıl bağlanacağınızı](../private-link/tutorial-private-endpoint-cosmosdb-portal.md)öğrenin.