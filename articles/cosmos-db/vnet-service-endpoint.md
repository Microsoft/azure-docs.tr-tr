---
title: Azure sanal ağ hizmeti uç noktası kullanarak bir Azure Cosmos DB hesabına güvenli erişim
description: Bu belge, bir Azure Cosmos hesabı için sanal ağ ve alt ağ erişim denetimi hakkında açıklanmaktadır.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 791821fbfe5854c27b7e3e6927a56a66ac1f1dc2
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819094"
---
# <a name="access-azure-cosmos-db-from-virtual-networks-vnet"></a>Sanal ağlardan (VNet) Azure Cosmos DB erişim

Azure Cosmos hesabını yalnızca belirli bir sanal ağ (VNet) alt ağından erişime izin verecek şekilde yapılandırabilirsiniz. [Hizmet uç noktasının](../virtual-network/virtual-network-service-endpoints-overview.md) bir sanal ağ içindeki alt ağda Azure Cosmos DB erişmesini etkinleştirerek, bu alt ağdan gelen trafik, alt ağın ve sanal ağın kimliğiyle Azure Cosmos DB gönderilir. Azure Cosmos DB hizmeti uç noktası etkinleştirildikten sonra, Azure Cosmos hesabınıza ekleyerek alt ağa erişimi sınırlayabilirsiniz.

Varsayılan olarak, isteğe geçerli bir yetkilendirme belirteciyle eşlik eden bir Azure Cosmos hesabına herhangi bir kaynaktan erişilebilir. VNET 'ler içinde bir veya daha fazla alt ağ eklediğinizde, yalnızca bu alt ağlardan kaynaklanan istekler geçerli bir yanıt alır. Diğer kaynaklardan gelen istekler, 403 (yasak) yanıtı alır. 

## <a name="frequently-asked-questions"></a>Sık sorulan sorular

Aşağıda, sanal ağlardan erişimi yapılandırma hakkında sık sorulan bazı sorular verilmiştir:

### <a name="can-i-specify-both-virtual-network-service-endpoint-and-ip-access-control-policy-on-an-azure-cosmos-account"></a>Azure Cosmos hesabında hem sanal ağ hizmeti uç noktası hem de IP erişim denetim ilkesi belirtebilir miyim? 

Azure Cosmos hesabınızda hem sanal ağ hizmeti uç noktasını hem de bir IP erişim denetimi ilkesini (aka güvenlik duvarı) etkinleştirebilirsiniz. Bu iki özellik tamamlayıcı ve Azure Cosmos hesabınızın yalıtımı ve güvenliğini topluca garanti altına alınır. IP güvenlik duvarının kullanılması, statik IP 'Lerin hesabınıza erişmesini sağlar. 

### <a name="how-do-i-limit-access-to-subnet-within-a-virtual-network"></a>Nasıl yaparım? bir sanal ağ içindeki alt ağa erişimi mi sınırlayın? 

Azure Cosmos hesabına erişimi bir alt ağdan sınırlamak için gereken iki adım vardır. İlk olarak, alt ağdan gelen trafiğin alt ağını ve sanal ağ kimliğini Azure Cosmos DB olarak taşıması için izin verebilirsiniz. Bu işlem, alt ağdaki Azure Cosmos DB için hizmet uç noktası etkinleştirilerek yapılır. Daha sonra, bu alt ağı, hesaba erişilebilen bir kaynak olarak belirten Azure Cosmos hesabına bir kural ekleniyor.

### <a name="will-virtual-network-acls-and-ip-firewall-reject-requests-or-connections"></a>Sanal ağ ACL 'Leri ve IP güvenlik duvarı istekleri veya bağlantıları reddeder mi? 

IP güvenlik duvarı veya sanal ağ erişim kuralları eklendiğinde, yalnızca izin verilen kaynaklardan gelen istekler geçerli yanıtlar alır. Diğer istekler 403 (yasak) ile reddedilir. Azure Cosmos hesabının güvenlik duvarını bir bağlantı düzeyi güvenlik duvarıyla ayırt etmek önemlidir. Kaynak hizmete hala bağlanabilir ve bağlantılar reddedilmez.

### <a name="my-requests-started-getting-blocked-when-i-enabled-service-endpoint-to-azure-cosmos-db-on-the-subnet-what-happened"></a>Hizmet uç noktasını, alt ağda Azure Cosmos DB için etkinleştirdiğimde, isteklerim engellendi almaya başladı. Ne oldu?

Bir alt ağda Azure Cosmos DB için hizmet uç noktası etkinleştirildikten sonra, hesap anahtarlarına genel IP 'den sanal ağa ve alt ağa ulaşan trafik kaynağıdır. Azure Cosmos hesabınızda yalnızca IP tabanlı güvenlik duvarı varsa, hizmet etkin alt ağdan gelen trafik artık IP güvenlik duvarı kurallarıyla eşleşmez ve bu nedenle reddedilir. IP tabanlı güvenlik duvarından sanal ağ tabanlı erişim denetimine sorunsuz bir şekilde geçiş yapmak için adımlara gidin.

### <a name="do-the-peered-virtual-networks-also-have-access-to-azure-cosmos-account"></a>Eşlenen sanal ağların Azure Cosmos hesabına erişimi de var mı? 
Yalnızca sanal ağın ve Azure Cosmos hesabına eklenen alt ağlarının erişimi vardır. Eşlenen sanal ağlarda bulunan alt ağlar hesaba eklenene kadar, eşlenen VNET 'ler hesaba erişemez.

### <a name="what-is-the-maximum-number-of-subnets-allowed-to-access-a-single-cosmos-account"></a>Tek bir Cosmos hesabına erişim izni verilen en fazla alt ağ sayısı nedir? 
Şu anda, bir Azure Cosmos hesabı için en fazla 64 alt ağa izin verilebilir.

### <a name="can-i-enable-access-from-vpn-and-express-route"></a>VPN ve Express rotalarından erişimi etkinleştirebilir miyim? 
Şirket içindeki Express Route üzerinden Azure Cosmos hesabına erişim için Microsoft eşlemesini etkinleştirmeniz gerekir. IP güvenlik duvarı veya sanal ağ erişim kuralları ekledikten sonra, Azure Cosmos hesabı IP güvenlik duvarınızdaki Microsoft eşlemesi için kullanılan genel IP adreslerini, şirket içi hizmetlere Azure Cosmos hesabına erişim sağlamak için ekleyebilirsiniz. 

### <a name="do-i-need-to-update-the-network-security-groups-nsg-rules"></a>Ağ güvenlik grupları (NSG) kurallarını güncelleştirmem gerekiyor mu? 
NSG kuralları, sanal ağı olan bir alt ağ ile bağlantıyı sınırlamak için kullanılır. Alt ağa Azure Cosmos DB için hizmet uç noktası eklediğinizde, Azure Cosmos hesabınız için NSG 'de giden bağlantıyı açmaya gerek yoktur. 

### <a name="are-service-endpoints-available-for-all-vnets"></a>Hizmet uç noktaları tüm VNET 'ler için kullanılabilir mi?
Hayır, yalnızca Azure Resource Manager sanal ağlarda hizmet uç noktası etkin olabilir. Klasik sanal ağlar hizmet uç noktalarını desteklemez.

### <a name="can-i-accept-connections-from-within-public-azure-datacenters-when-service-endpoint-access-is-enabled-for-azure-cosmos-db"></a>Hizmet uç noktası erişimi Azure Cosmos DB için etkinleştirildiğinde, "Genel Azure veri merkezlerinden gelen bağlantıları kabul edebilir" miyim?  
Bu, yalnızca Azure Cosmos DB hesabınıza Azure Data Factory, Azure Bilişsel Arama veya belirtilen Azure bölgesinde dağıtılan herhangi bir hizmet gibi diğer Azure birinci taraf hizmetleri tarafından erişilmesini istediğinizde gereklidir.


## <a name="next-steps"></a>Sonraki adımlar

* [Sanal ağlardaki alt ağlara Azure Cosmos hesabı erişimi nasıl sınırlandırmalar](how-to-configure-vnet-service-endpoint.md)
* [Azure Cosmos hesabınız için IP güvenlik duvarını yapılandırma](how-to-configure-firewall.md)

