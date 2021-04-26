---
title: Azure sanal ağ hizmet uç noktaları
titlesuffix: Azure Virtual Network
description: Hizmet uç noktalarını kullanarak bir sanal ağdan Azure kaynaklarına doğrudan erişimi etkinleştirmeyi öğrenin.
services: virtual-network
documentationcenter: na
author: sumeetmittal
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/08/2019
ms.author: sumi
ms.custom: ''
ms.openlocfilehash: 8926e99db926fc8182e98509c3deff0ccc3d1612
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99576676"
---
# <a name="virtual-network-service-endpoints"></a>Sanal Ağ hizmet uç noktaları

Sanal ağ (VNet) hizmeti uç noktası, Azure omurga ağı üzerinden iyileştirilmiş bir yol üzerinden Azure hizmetlerine güvenli ve doğrudan bağlantı sağlar. Uç noktalar kritik Azure hizmeti kaynaklarınızı sanal ağlarınızla sınırlayarak güvenliğini sağlamanıza imkan verir. Hizmet uç noktaları, VNet 'teki özel IP adreslerinin VNet üzerinde genel bir IP adresi gerekmeden bir Azure hizmeti uç noktasına ulaşmasını sağlar.

Bu özellik aşağıdaki Azure hizmetleri ve bölgeleri için kullanılabilir. *Microsoft. \** Resource parantez içinde. Hizmetiniz için hizmet uç noktalarını yapılandırırken bu kaynağı alt ağ tarafında etkinleştirin:

**Genel kullanıma sunuldu**

- **[Azure depolama](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network)** (*Microsoft. Storage*): tüm Azure bölgelerinde genel kullanıma sunulmuştur.
- **[Azure SQL veritabanı](../azure-sql/database/vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft. SQL*): tüm Azure bölgelerinde genel kullanıma sunulmuştur.
- **[Azure SYNAPSE Analytics](../azure-sql/database/vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft. SQL*): adanmış SQL HAVUZLARı (eskiden SQL DW) Için tüm Azure bölgelerinde genel kullanıma sunulmuştur.
- **[PostgreSQL Için Azure veritabanı sunucusu](../postgresql/howto-manage-vnet-using-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft. SQL*): veritabanı hizmetinin kullanılabildiği Azure bölgelerinde genel kullanıma sunulmuştur.
- **[MySQL Için Azure veritabanı sunucusu](../mysql/howto-manage-vnet-using-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft. SQL*): veritabanı hizmetinin kullanılabildiği Azure bölgelerinde genel kullanıma sunulmuştur.
- **[MariaDB Için Azure veritabanı](../mariadb/concepts-data-access-security-vnet.md)** (*Microsoft. SQL*): veritabanı hizmetinin kullanılabildiği Azure bölgelerinde genel kullanıma sunulmuştur.
- **[Azure Cosmos DB](../cosmos-db/how-to-configure-vnet-service-endpoint.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft. Azu, smosdb*): tüm Azure bölgelerinde genel olarak kullanılabilir.
- **[Azure Key Vault](../key-vault/general/overview-vnet-service-endpoints.md)** (*Microsoft. keykasası*): tüm Azure bölgelerinde genel kullanıma sunulmuştur.
- **[Azure Service Bus](../service-bus-messaging/service-bus-service-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft. ServiceBus*): tüm Azure bölgelerinde genel olarak kullanılabilir.
- **[Azure Event Hubs](../event-hubs/event-hubs-service-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft. EventHub*): tüm Azure bölgelerinde genel olarak kullanılabilir.
- **[Azure Data Lake Store Gen 1](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft. AzureActiveDirectory*): tüm Azure bölgelerinde genel olarak kullanılabilir ADLS 1..
- **[Azure App Service](../app-service/app-service-ip-restrictions.md)** (*Microsoft. Web*): App Service 'in kullanılabildiği tüm Azure bölgelerinde genel kullanıma sunulmuştur.
- **[Azure](../cognitive-services/cognitive-services-virtual-networks.md?tabs=portal)** bilişsel hizmetler (*Microsoft. Biliveservices*): bilişsel hizmetler 'in kullanılabildiği tüm Azure bölgelerinde genel kullanıma sunulmuştur.

**Genel Önizleme**

- **[Azure Container Registry](../container-registry/container-registry-vnet.md)** (*Microsoft. containerregistry*): Azure Container Registry kullanılabildiği sınırlı Azure bölgelerinde önizleme kullanılabilir.

En güncel bildirimler için [Azure Sanal Ağ güncelleştirmeleri](https://azure.microsoft.com/updates/?product=virtual-network) sayfasını inceleyin.

## <a name="key-benefits"></a>Önemli avantajlar

Hizmet uç noktaları aşağıdaki avantajları sağlar:

- **Azure hizmet kaynaklarınız Için geliştirilmiş güvenlik**: VNET özel adres alanları örtüşebilirler. VNet 'nizden kaynaklanan trafiği benzersiz şekilde tanımlamak için çakışan alanları kullanamazsınız. Hizmet uç noktaları, VNet kimliğini hizmete genişleterek sanal ağınıza Azure hizmet kaynaklarını güvenli hale getirme yeteneği sağlar. Sanal ağınızda hizmet uç noktalarını etkinleştirdikten sonra, Azure hizmet kaynaklarını sanal ağınıza güvenli hale getirmek için bir sanal ağ kuralı ekleyebilirsiniz. Kural ekleme, kaynaklara genel İnternet erişimini tamamen kaldırıp yalnızca sanal ağınızdan gelen trafiğe izin vererek geliştirilmiş güvenlik sağlar.
- **Sanal ağınızdan gelen Azure hizmet trafiği Için en iyi yönlendirme**: bugün, sanal ağınızda internet trafiğini şirket içi ve/veya sanal gereçleriniz için zorlayan tüm yollar Ayrıca, Azure hizmet trafiğini internet trafiğiyle aynı rotayı alacak şekilde zorlar. Hizmet uç noktaları Azure trafiği için en uygun rotayı sunar. 

  Uç noktalar her zaman hizmet trafiğini sanal ağınızdan doğrudan Microsoft Azure omurga ağındaki hizmete yönlendirir. Trafiğin Azure omurga ağında tutulması, zorlamalı tünel aracılığıyla hizmet trafiğini etkilemeden, giden İnternet trafiğini sanal ağlarınızdan denetlemeye ve izlemeye devam etmenize olanak sağlar. Kullanıcı tanımlı rotalar ve Zorlamalı tünel oluşturma hakkında daha fazla bilgi için bkz. [Azure sanal ağ trafiği yönlendirme](virtual-networks-udr-overview.md).
- **Kolay kurulum sayesinde daha az yönetim yükü**: Azure kaynaklarını IP güvenlik duvarı aracılığıyla güvenli hale getirmek için artık sanal ağınızda ayrılmış, ortak IP adresleri olması gerekmez. Hizmet uç noktalarını ayarlamak için gereken ağ adresi çevirisi (NAT) veya ağ geçidi cihazı yok. Hizmet uç noktalarını bir alt ağda basit bir tıklama aracılığıyla yapılandırabilirsiniz. Uç noktaların sürdürülmesi için ek yük yoktur.

## <a name="limitations"></a>Sınırlamalar

- Bu özellik yalnızca Azure Resource Manager dağıtım modeli üzerinden dağıtılmış olan sanal ağlarda kullanılabilir.
- Uç noktalar Azure sanal ağlarında yapılandırılmış olan alt ağlarda etkindir. Uç noktalar, şirket içi Azure hizmetlerine giden trafik için kullanılamaz. Daha fazla bilgi için bkz. [Şirket Içinden Azure hizmeti erişimini güvenli hale getirme](#secure-azure-services-to-virtual-networks)
- Azure SQL için, hizmet uç noktası yalnızca sanal ağ ile aynı bölgedeki Azure hizmeti trafiği için geçerlidir. Azure depolama için uç noktalar Ayrıca, sanal ağı Read-Access Geo-Redundant depolama (RA-GRS) ve Geo-Redundant depolama (GRS) trafiğini desteklemek üzere dağıttığınız eşleştirilmiş bölgeleri içerecek şekilde de genişletilir. Daha fazla bilgi için bkz. [Azure eşlenmiş bölgeler](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions).
- Azure Data Lake Storage (ADLS) Gen 1 için, VNet tümleştirme özelliği yalnızca aynı bölgedeki sanal ağlarda kullanılabilir. Ayrıca, ADLS 1. için sanal ağ tümleştirmesinin, erişim belirtecinde ek güvenlik talepleri oluşturmak için sanal ağınız ve Azure Active Directory (Azure AD) arasında sanal ağ hizmeti uç noktası güvenliğini kullandığını unutmayın. Ardından bu talepler sanal ağınız için Data Lake Storage 1. Nesil hesabınızda kimlik doğrulaması gerçekleştirme ve erişim izni verme amacıyla kullanılır. Hizmet uç noktalarını destekleme hizmetleri altında listelenen *Microsoft. AzureActiveDirectory* etiketi, yalnızca ADLS Gen 1 ' e hizmet uç noktalarını desteklemek için kullanılır. Azure AD, hizmet uç noktalarını yerel olarak desteklemez. Azure Data Lake Store Gen 1 VNet tümleştirmesi hakkında daha fazla bilgi için bkz. [Azure Data Lake Storage 1. ağ güvenliği](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="secure-azure-services-to-virtual-networks"></a>Azure hizmetlerini sanal ağlara güvenli hale getirme

- Sanal ağ hizmet uç noktası, Azure hizmetine sanal ağınızın kimliğini sağlar. Sanal ağınızda hizmet uç noktalarını etkinleştirdikten sonra, Azure hizmet kaynaklarını sanal ağınıza güvenli hale getirmek için bir sanal ağ kuralı ekleyebilirsiniz.
- Günümüzde, bir sanal ağdan gelen Azure hizmet trafiği, kaynak IP adresleri olarak ortak IP adreslerini kullanır. Hizmet uç noktaları ile, hizmet trafiği sanal ağınızdan Azure hizmetinize erişim sırasında kaynak IP adresleri olarak sanal ağ özel adreslerini kullanır. Bu anahtar IP güvenlik duvarlarında kullanılan ayrılmış ve genel IP adreslerini kullanmadan hizmetlere erişmenizi sağlar.

   >[!NOTE]
   > Hizmet uç noktaları ile alt ağdaki sanal makinelerin kaynak IP adresleri hizmet trafiği için genel IPv4 adresleri yerine özel IPv4 adreslerini kullanmaya başlar. Bu değişikliğin ardından Azure genel IP adreslerini kullanan mevcut Azure hizmeti güvenlik duvarı kuralları çalışmamaya başlar. Hizmet uç noktalarını ayarlamadan önce Azure hizmeti güvenlik duvarı kurallarının bu değişikliğe uygun olduğundan emin olun. Hizmet uç noktalarını yapılandırırken bu alt ağdan gelen hizmet trafiğinde geçici kesintiler de yaşayabilirsiniz. 
 
## <a name="secure-azure-service-access-from-on-premises"></a>Şirket içinden Azure hizmeti erişimini güvenli hale getirme

  Varsayılan olarak, sanal ağlarla güvenliği sağlanmış Azure hizmet kaynaklarına şirket içi ağlardan ulaşılabilir. Şirket içinden gelen trafiğe izin vermek istiyorsanız şirket içi veya ExpressRoute 'un genel (genellikle NAT) IP adreslerine de izin vermeniz gerekir. Bu IP adreslerini Azure hizmet kaynakları için IP güvenlik duvarı yapılandırması aracılığıyla ekleyebilirsiniz.

  ExpressRoute: şirket içinde genel eşleme veya Microsoft eşlemesi için [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) kullanıyorsanız, kullanmakta olduğunuz NAT IP adreslerini belirlemeniz gerekir. Genel eşleme için her bir ExpressRoute bağlantı hattı, trafik Microsoft Azure ağ omurgasına girdiğinde Azure hizmet trafiğine uygulanan iki NAT IP adresi kullanır. Microsoft eşlemesi için NAT IP adresleri müşteri tarafından sağlanır veya hizmet sağlayıcısı tarafından sağlanır. Hizmet kaynaklarınıza erişime izin vermek için, bu genel IP adreslerine kaynak IP güvenlik duvarı ayarında izin vermeniz gerekir. Ortak eşleme ExpressRoute bağlantı hattı IP adreslerinizi bulmak için Azure portalında [ExpressRoute ile bir destek bileti açın](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). ExpressRoute genel ve Microsoft eşlemesi için NAT hakkında daha fazla bilgi için bkz. [EXPRESSROUTE NAT gereksinimleri](../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering).

![Azure hizmetlerini sanal ağlar ile sınırlama](./media/virtual-network-service-endpoints-overview/VNet_Service_Endpoints_Overview.png)

### <a name="configuration"></a>Yapılandırma

- Sanal ağdaki bir alt ağda hizmet uç noktalarını yapılandırın. Uç noktalar ilgili alt ağ içinde çalışan tüm işlem örneği türleriyle birlikte çalışabilir.
- Desteklenen tüm Azure Hizmetleri (örneğin, Azure Storage veya Azure SQL veritabanı) için bir alt ağda birden fazla hizmet uç noktası yapılandırabilirsiniz.
- Azure SQL Veritabanı için, sanal ağların Azure hizmet kaynağıyla aynı bölgede bulunması gerekir. GRS ve RA-GRS Azure Depolama hesapları kullanılıyorsa, birincil hesap sanal ağ ile aynı bölgede olmalıdır. Tüm diğer hizmetler için, Azure hizmet kaynaklarını herhangi bir bölgedeki sanal ağlarla güvenli hale getirebilirsiniz. 
- Uç noktanın yapılandırıldığı sanal ağ, Azure hizmet kaynağıyla aynı veya ondan farklı abonelikte olabilir. Uç noktaları ayarlamak ve Azure hizmetlerinin güvenliğini sağlamak için gerekli olan izinler hakkında daha fazla bilgi için [Sağlama](#provisioning) bölümüne bakın.
- Desteklenen hizmetler için yeni veya mevcut kaynaklar ile sanal ağlar arasındaki güvenliği hizmet uç noktaları kullanarak sağlayabilirsiniz.

### <a name="considerations"></a>Dikkat edilmesi gerekenler

- Hizmet uç noktasını etkinleştirdikten sonra, kaynak IP adresleri genel IPv4 adreslerini kullanarak bu alt ağdaki hizmetle iletişim kurarken özel IPv4 adreslerini kullanmaya geçiş yapar. Hizmete giden mevcut açık TCP bağlantıları bu geçiş sırasında kapatılır. Bir alt ağ için hizmete yönelik hizmet uç noktasını etkinleştirmeden veya devre dışı bırakmadan önce çalışan kritik görev olmadığından emin olun. Ayrıca uygulamalarınızın IP adresi değişikliğinin ardından Azure hizmetlerine otomatik olarak bağlanabildiğinden emin olun.

  IP adresi değişiklikleri yalnızca sanal ağınızdan giden hizmet trafiğini etkiler. Sanal makinelerinize atanmış genel IPv4 adreslerinden veya bu adreslere yönelik diğer trafiğe hiçbir etkisi yoktur. Azure hizmetleri açısından, Azure genel IP adreslerini kullanan mevcut güvenlik duvarı kurallarınız varsa bu kurallar sanal ağ özel adresine geçiş yapıldığında çalışmaz.
- Hizmet uç noktaları ile Azure hizmetleri için DNS girişleri bugün olduğu gibi kalır ve Azure hizmetine atanan genel IP adreslerini çözümlemeye devam eder.

- Hizmet uç noktasına sahip ağ güvenlik grupları (NSG):
  - Varsayılan olarak, NSG 'ler giden internet trafiğine izin verir ve sanal ağınızdan Azure hizmetlerine giden trafiğe de izin verir. Bu trafik, hizmet uç noktaları olduğu gibi çalışmaya devam eder. 
  - Tüm giden internet trafiğini reddetmek ve yalnızca belirli Azure hizmetlerine giden trafiğe izin vermek istiyorsanız, NSG 'lerinizin [hizmet etiketlerini](./network-security-groups-overview.md#service-tags) kullanarak bunu yapabilirsiniz. Desteklenen Azure hizmetlerini NSG kurallarınızın hedefi olarak belirtebilirsiniz ve Azure, her bir etiketin temelindeki IP adreslerinin bakımını de sağlar. Daha fazla bilgi için bkz. [NSG'ler için Azure Hizmet etiketleri.](./network-security-groups-overview.md#service-tags) 

### <a name="scenarios"></a>Senaryolar

- **Eşlenmiş, bağlı veya birden çok sanal ağ**: Bir sanal ağ içindeki veya birden fazla sanal ağ üzerinde bulunan birden fazla alt ağdaki Azure hizmetlerinin güvenliğini sağlamak için, her bir alt ağdaki hizmet uç noktasını ayrı ayrı etkinleştirebilir ve bu alt ağlara giden Azure hizmet kaynaklarının güvenliğini sağlayabilirsiniz.
- **Bir sanal ağdan Azure hizmetlerine giden trafiği Filtreleme**: bir sanal ağdan bir Azure hizmetine gönderilen trafiği incelemek veya filtrelemek istiyorsanız, sanal ağ içinde bir ağ sanal gereci dağıtabilirsiniz. Ardından hizmet uç noktalarını ağ sanal gerecinin dağıtılmış olduğu alt ağa uygulayabilir ve Azure hizmet kaynağını yalnızca bu alt ağ ile sınırlayabilirsiniz. Bu senaryo, Azure hizmeti erişimini sanal ağınızdan yalnızca belirli Azure kaynaklarına kısıtlamak için ağ sanal gereç filtresi kullanmak istediğinizde yararlı olabilir. Daha fazla bilgi için bkz. [Ağ sanal gereçleri ile çıkış](/azure/architecture/reference-architectures/dmz/nva-ha).
- **Azure kaynaklarını doğrudan sanal ağlara dağıtılan hizmetlere güvenli hale getirme**: çeşitli Azure hizmetlerini bir sanal ağdaki belirli alt ağlara doğrudan dağıtabilirsiniz. Yönetilen hizmet alt ağında bir hizmet uç noktası kurarak Azure hizmet kaynaklarını [yönetilen hizmet](virtual-network-for-azure-services.md) alt ağlarına ayırabilirsiniz.
- **Azure sanal makinesinden gelen disk trafiği**: yönetilen ve yönetilmeyen diskler Için sanal makine disk trafiği, Azure depolama için hizmet uç noktası yönlendirme değişikliklerinden etkilenmez. Bu trafik, bağlama ve çıkarma gibi bir diskIO da içerir. Hizmet uç noktaları ve [Azure depolama ağı kuralları](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)aracılığıyla ağları seçmek için, sayfa BLOBLARıNA Rest erişimini sınırlayabilirsiniz. 

### <a name="logging-and-troubleshooting"></a>Günlüğe kaydetme ve sorun giderme

Hizmet uç noktalarını belirli bir hizmete yapılandırdıktan sonra, hizmet uç noktası yolunun şu şekilde geçerli olduğunu doğrulayın: 
 
- Hizmet tanılamada herhangi bir hizmet isteğinin kaynak IP adresini doğrulama. Hizmet uç noktalarına sahip tüm yeni isteklerin kaynak IP adresi değerinde isteği sanal ağınızdan gönderen istemciye atanmış olan sanal ağ özel IP adresi gösterilir. Uç noktası olmadığında bu adres Azure genel IP adreslerinden biri olur.
- Bir alt ağdaki herhangi bir ağ arabiriminin etkin yollarını görüntüleme. Hizmet yolu:
  - Her hizmetin adres ön eki aralıklarına daha belirli bir varsayılan yolu gösterir
  - nextHopType değeri *VirtualNetworkServiceEndpoint* olarak belirlenmiştir
  - Hizmete yönelik daha doğrudan bir bağlantının, zorunlu tünelleme rotalarıyla karşılaştırıldığında geçerli olduğunu belirtir

>[!NOTE]
> Hizmet uç noktası rotaları, Azure hizmeti olarak adres ön eki eşleşmesi için BGP veya UDR rotasını geçersiz kılar. Daha fazla bilgi için bkz. [geçerli yollarla sorun giderme](diagnose-network-routing-problem.md).

## <a name="provisioning"></a>Sağlama

Hizmet uç noktaları, bir sanal ağa yazma erişimi olan bir kullanıcı tarafından bağımsız olarak sanal ağlarda yapılandırılabilir. Azure hizmet kaynaklarını bir sanal ağa güvenli hale getirmek için, eklenen alt ağlar için kullanıcının *Microsoft. Network/virtualNetworks/alt ağları/Jodavetli Aserviceendpoint/Action* iznine sahip olması gerekir. Yerleşik hizmet yöneticisi rolleri varsayılan olarak bu izni içerir. Özel roller oluşturarak izini değiştirebilirsiniz.

Yerleşik roller hakkında daha fazla bilgi için bkz. [Azure yerleşik rolleri](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Özel rollere özel izinler atama hakkında daha fazla bilgi için bkz. [Azure özel roller](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Sanal ağlar ve Azure hizmet kaynakları aynı ağda veya farklı aboneliklerde olabilir. Azure depolama ve Azure Key Vault gibi belirli Azure Hizmetleri, farklı Active Directory (AD) kiracılarındaki hizmet uç noktalarını da destekler, ancak sanal ağ ve Azure hizmet kaynağı farklı Active Directory (AD) kiracılarda olabilir. Daha fazla ayrıntı için lütfen tek tek hizmet belgelerini denetleyin.  

## <a name="pricing-and-limits"></a>Fiyatlandırma ve limitler

Hizmet uç noktalarını kullanmak için ek ücret alınmaz. Azure Hizmetleri (Azure depolama, Azure SQL veritabanı vb.) için geçerli fiyatlandırma modeli bugün olarak geçerlidir.

Bir sanal ağdaki toplam hizmet uç noktası sayısı sınırı yoktur.

Azure depolama hesapları gibi belirli Azure Hizmetleri, kaynağın güvenliğini sağlamak için kullanılan alt ağ sayısı için sınırlara zorlayabilir. Ayrıntılar için [sonraki adımlar](#next-steps) bölümündeki çeşitli hizmetlere yönelik belgelere bakın.

## <a name="vnet-service-endpoint-policies"></a>VNet hizmeti uç noktası ilkeleri 

VNet hizmet uç noktası ilkeleri, sanal ağ trafiğini Azure hizmetlerine filtrelemenizi sağlar. Bu filtre, hizmet uç noktaları üzerinden yalnızca belirli Azure hizmet kaynaklarına izin verir. Hizmet uç noktası ilkeleri, Azure hizmetlerine yönelik sanal ağ trafiği için ayrıntılı erişim denetimi sağlar. Daha fazla bilgi için bkz. [sanal ağ hizmeti uç noktası ilkeleri](./virtual-network-service-endpoint-policies-overview.md).

## <a name="faqs"></a>SSS

SSS için bkz. [sanal ağ hizmeti uç noktası SSS](./virtual-networks-faq.md#virtual-network-service-endpoints).

## <a name="next-steps"></a>Sonraki adımlar

- [Sanal ağ hizmet uç noktalarını yapılandırma](tutorial-restrict-network-access-to-resources.md)
- [Bir Azure Depolama hesabını bir sanal ağa güvenli hale getirme](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure SQL veritabanını bir sanal ağ ile güvenli hale getirme](../azure-sql/database/vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Bir Azure SYNAPSE analizinin bir sanal ağ ile güvenliğini sağlama](../azure-sql/database/vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fsql-data-warehouse%2ftoc.json)
- [Sanal ağlarda Azure hizmet tümleştirmesi](virtual-network-for-azure-services.md)
- [Sanal ağ hizmeti uç noktası Ilkeleri](./virtual-network-service-endpoint-policies-overview.md)
- [Azure Resource Manager şablonu](https://azure.microsoft.com/resources/templates/201-vnet-2subnets-service-endpoints-storage-integration)
