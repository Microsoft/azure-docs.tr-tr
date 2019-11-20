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
ms.date: 08/15/2018
ms.author: sumi
ms.custom: ''
ms.openlocfilehash: 8420142e67fe4af12045a2b6fe7f7461ef384f81
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70164483"
---
# <a name="virtual-network-service-endpoints"></a>Sanal Ağ Hizmeti Uç Noktaları

Sanal Ağ hizmet uç noktaları, sanal ağ özel adres alanınızı ve sanal ağınızın kimliğini doğrudan bağlantı üzerinden Azure hizmetlerine genişletir. Uç noktalar kritik Azure hizmeti kaynaklarınızı sanal ağlarınızla sınırlayarak güvenliğini sağlamanıza imkan verir. Sanal ağınızdan Azure hizmetine giden trafik her zaman Microsoft Azure omurga ağında kalır.

Bu özellik aşağıdaki Azure hizmetleri ve bölgeleri için kullanılabilir ve ayrıca, hizmetinize yönelik hizmet uç noktalarını yapılandırırken alt ağ tarafında etkinleştirilmesi gereken parantez içinde Microsoft. * kaynağını da bulacaksınız:

**Genel kullanıma sunuldu**

- **[Azure depolama](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network)** (Microsoft. Storage): Tüm Azure bölgelerinde genel kullanıma sunulmuştur.
- **[Azure SQL veritabanı](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (Microsoft. SQL): Tüm Azure bölgelerinde genel kullanıma sunulmuştur.
- **[Azure SQL veri ambarı](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (Microsoft. SQL): Tüm Azure bölgelerinde genel kullanıma sunulmuştur.
- **[PostgreSQL Için Azure veritabanı sunucusu](../postgresql/howto-manage-vnet-using-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (Microsoft. SQL): Veritabanı hizmetinin kullanılabildiği Azure bölgelerinde genel kullanıma sunulmuştur.
- **[MySQL Için Azure veritabanı sunucusu](../mysql/howto-manage-vnet-using-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (Microsoft. SQL): Veritabanı hizmetinin kullanılabildiği Azure bölgelerinde genel kullanıma sunulmuştur.
- **[MariaDB Için Azure veritabanı](https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-vnet)** (Microsoft. SQL): Veritabanı hizmetinin kullanılabildiği Azure bölgelerinde genel kullanıma sunulmuştur.
- **[Azure Cosmos DB](../cosmos-db/vnet-service-endpoint.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (Microsoft. Azu, Smosdb): Tüm Azure bölgelerinde genel kullanıma sunulmuştur.
- **[Azure Key Vault](../key-vault/key-vault-overview-vnet-service-endpoints.md)** (Microsoft. Keykasası): Tüm Azure bölgelerinde genel kullanıma sunulmuştur.
- **[Azure Service Bus](../service-bus-messaging/service-bus-service-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (Microsoft. ServiceBus): Tüm Azure bölgelerinde genel kullanıma sunulmuştur.
- **[Azure Event Hubs](../event-hubs/event-hubs-service-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (Microsoft. EventHub): Tüm Azure bölgelerinde genel kullanıma sunulmuştur.
- **[Azure Data Lake Store Gen 1](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (Microsoft. AzureActiveDirectory): ADLS 1. kullanılabildiği tüm Azure bölgelerinde genel kullanıma sunulmuştur.
- **[Azure App Service](https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions)** : App Service 'in kullanılabildiği tüm Azure bölgelerinde genel kullanıma sunuldu

**Genel Önizleme**

- **[Azure Container Registry](../container-registry/container-registry-vnet.md)** (Microsoft. ContainerRegistry): Azure Container Registry kullanılabildiği tüm Azure bölgelerinde önizleme kullanılabilir.

En güncel bildirimler için [Azure Sanal Ağ güncelleştirmeleri](https://azure.microsoft.com/updates/?product=virtual-network) sayfasını inceleyin.

## <a name="key-benefits"></a>Önemli avantajlar

Hizmet uç noktaları aşağıdaki avantajları sağlar:

- **Azure hizmet kaynaklarınız Için geliştirilmiş güvenlik**: VNet özel adres alanı örtülemez ve bu nedenle sanal ağınızdan kaynaklanan trafiği benzersiz şekilde tanımlamak için kullanılamaz. Hizmet uç noktaları, sanal ağ kimliğini hizmete taşıyarak Azure hizmeti kaynaklarının sanal ağınıza sunulmasını sağlar. Hizmet uç noktaları sanal ağınızda etkinleştirdikten sonra, kaynaklara bir sanal ağ kuralı ekleyerek sanal ağınıza bağlı olan Azure hizmet kaynaklarının güvenliğini sağlayabilirsiniz. Bu sayede kaynaklara genel İnternet erişimini tamamen kaldırıp yalnızca sanal ağınızdan gelen trafiğe izin vererek güvenliği artırabilirsiniz.
- **Sanal ağınızdan gelen Azure hizmet trafiği Için en iyi yönlendirme**: Günümüzde, sanal ağınızda Internet trafiğini şirket içi ve/veya sanal gereçlere zorlayan ve Zorlamalı tünel olarak bilinen tüm yollar, Azure hizmet trafiğini Internet trafiğiyle aynı rotayı alacak şekilde zorlar. Hizmet uç noktaları Azure trafiği için en uygun rotayı sunar. 

  Uç noktalar her zaman hizmet trafiğini sanal ağınızdan doğrudan Microsoft Azure omurga ağındaki hizmete yönlendirir. Trafiğin Azure omurga ağında tutulması, zorlamalı tünel aracılığıyla hizmet trafiğini etkilemeden, giden İnternet trafiğini sanal ağlarınızdan denetlemeye ve izlemeye devam etmenize olanak sağlar. [Kullanıcı tanımlı rotalar ve zorlamalı tünel oluşturma](virtual-networks-udr-overview.md) hakkında daha fazla bilgi edinin.
- **Daha az yönetim yükü ile ayarlama basittir**: Azure kaynaklarını IP güvenlik duvarı aracılığıyla güvenli hale getirmek için sanal ağlarınızda artık ayrılmış, genel IP adresleri gerekmez. Hizmet uç noktalarını ayarlamak için herhangi bir NAT veya ağ geçidi cihazı gerekmez. Hizmet uç noktaları alt ağda tek tıklamayla yapılandırılabilir. Uç noktaların bakımını yapma yükü ortadan kalkar.

## <a name="limitations"></a>Sınırlamalar

- Bu özellik yalnızca Azure Resource Manager dağıtım modeli üzerinden dağıtılmış olan sanal ağlarda kullanılabilir.
- Uç noktalar Azure sanal ağlarında yapılandırılmış olan alt ağlarda etkindir. Uç noktalar şirket içi ortamdan Azure hizmetlerine giden trafik için kullanılamaz. Daha fazla bilgi için bkz. [Azure hizmetine erişimi şirket içi ortamla sınırlama](#securing-azure-services-to-virtual-networks).
- Azure SQL için, hizmet uç noktası yalnızca sanal ağ ile aynı bölgedeki Azure hizmeti trafiği için geçerlidir. Azure Depolama için, RA-GRS ve GRS trafiğinin desteklenmesi için özelliğin kapsamı sanal ağın dağıtılmış olduğu eşleştirilmiş bölgeye uzatılır. [Azure eşleştirilmiş bölgeleri](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions) hakkında daha fazla bilgi edinin.
- ADLS Gen 1 için, VNet tümleştirme özelliği yalnızca aynı bölgedeki sanal ağlarda kullanılabilir. Ayrıca, Azure Data Lake Storage 1. sanal ağ tümleştirmesinin, erişim belirtecinde ek güvenlik talepleri oluşturmak için sanal ağınız ve Azure Active Directory (Azure AD) arasındaki sanal ağ hizmeti uç noktası güvenliğini kullanmasını da unutmayın. Ardından bu talepler sanal ağınız için Data Lake Storage 1. Nesil hesabınızda kimlik doğrulaması gerçekleştirme ve erişim izni verme amacıyla kullanılır. Hizmet uç noktalarını destekleme hizmetleri altında listelenen "Microsoft. AzureActiveDirectory" etiketi yalnızca ADLS Gen 1 ' e hizmet uç noktalarını desteklemek için kullanılır. Azure Active Directory (Azure AD) yerel olarak hizmet uç noktalarını desteklemez. [Azure Data Lake Store Gen 1 VNET tümleştirmesi](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)hakkında daha fazla bilgi edinin.

## <a name="securing-azure-services-to-virtual-networks"></a>Azure hizmetlerini sanal ağlar ile sınırlama

- Sanal ağ hizmet uç noktası, Azure hizmetine sanal ağınızın kimliğini sağlar. Hizmet uç noktaları sanal ağınızda etkinleştirdikten sonra, kaynaklara bir sanal ağ kuralı ekleyerek sanal ağınıza bağlı olan Azure hizmet kaynaklarının güvenliğini sağlayabilirsiniz.
- Günümüzde, bir sanal ağdan gelen Azure hizmet trafiği, kaynak IP adresleri olarak ortak IP adreslerini kullanır. Hizmet uç noktaları ile, hizmet trafiği sanal ağınızdan Azure hizmetinize erişim sırasında kaynak IP adresleri olarak sanal ağ özel adreslerini kullanır. Bu anahtar IP güvenlik duvarlarında kullanılan ayrılmış ve genel IP adreslerini kullanmadan hizmetlere erişmenizi sağlar.

>[!NOTE]
> Hizmet uç noktaları ile alt ağdaki sanal makinelerin kaynak IP adresleri hizmet trafiği için genel IPv4 adresleri yerine özel IPv4 adreslerini kullanmaya başlar. Bu değişikliğin ardından Azure genel IP adreslerini kullanan mevcut Azure hizmeti güvenlik duvarı kuralları çalışmamaya başlar. Hizmet uç noktalarını ayarlamadan önce Azure hizmeti güvenlik duvarı kurallarının bu değişikliğe uygun olduğundan emin olun. Hizmet uç noktalarını yapılandırırken bu alt ağdan gelen hizmet trafiğinde geçici kesintiler de yaşayabilirsiniz. 
 
- __Azure hizmetine erişimi şirket içi ortamla sınırlama__:

  Varsayılan olarak sanal ağlara ayrılmış olan Azure hizmeti kaynaklarına şirket içi ağlardan erişmek mümkün değildir. Şirket içinden gelen trafiğe izin vermek istiyorsanız şirket içi ortamdan veya ExpressRoute üzerinden genel (genelde NAT) IP adreslerine de izin vermeniz gerekir. Azure hizmet kaynakları için bu IP adresleri IP güvenlik duvarı yapılandırma adımından eklenebilir.

  ExpressRoute: Şirket içinde genel eşleme veya Microsoft eşlemesi için [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) kullanıyorsanız, kullanılan NAT IP adreslerini belirlemeniz gerekir. Ortak eşleme için, her bir ExpressRoute varsayılan olarak bağlantı hattında trafik Microsoft Azure omurga ağına girdiğinde Azure hizmet trafiğine uygulanan iki NAT IP adresi kullanılır. Microsoft eşlemesi için, kullanılan NAT IP adresleri müşteri tarafından sağlanır veya hizmet sağlayıcısı tarafından sağlanır. Hizmet kaynaklarınıza erişime izin vermek için, bu genel IP adreslerine kaynak IP güvenlik duvarı ayarında izin vermeniz gerekir. Ortak eşleme ExpressRoute bağlantı hattı IP adreslerinizi bulmak için [ExpressRoute ile bir destek bileti açın](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) Azure portal aracılığıyla. [ExpressRoute genel ve Microsoft eşlemesi için NAT](../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering) hakkında daha fazla bilgi edinin.

![Azure hizmetlerini sanal ağlar ile sınırlama](./media/virtual-network-service-endpoints-overview/VNet_Service_Endpoints_Overview.png)

### <a name="configuration"></a>Yapılandırma

- Hizmet uç noktaları bir sanal ağ içindeki alt ağ üzerinde yapılandırılır. Uç noktalar ilgili alt ağ içinde çalışan tüm işlem örneği türleriyle birlikte çalışabilir.
- Bir alt ağ üzerindeki desteklenen tüm Azure hizmetleri (örneğin Azure Depolama veya Azure SQL Veritabanı) için birden fazla hizmet uç noktası yapılandırabilirsiniz.
- Azure SQL Veritabanı için, sanal ağların Azure hizmet kaynağıyla aynı bölgede bulunması gerekir. GRS ve RA-GRS Azure Depolama hesapları kullanılıyorsa, birincil hesap sanal ağ ile aynı bölgede olmalıdır. Diğer tüm hizmetler için, Azure hizmet kaynakları herhangi bir bölgedeki sanal ağlar ile güvenli hale getirilebilir. 
- Uç noktanın yapılandırıldığı sanal ağ, Azure hizmet kaynağıyla aynı veya ondan farklı abonelikte olabilir. Uç noktaları ayarlamak ve Azure hizmetlerinin güvenliğini sağlamak için gerekli olan izinler hakkında daha fazla bilgi için [Sağlama](#provisioning) bölümüne bakın.
- Desteklenen hizmetler için yeni veya mevcut kaynaklar ile sanal ağlar arasındaki güvenliği hizmet uç noktaları kullanarak sağlayabilirsiniz.

### <a name="considerations"></a>Dikkat edilmesi gerekenler

- Bir hizmet uç noktası etkinleştirildikten sonra alt ağdan hizmet ile iletişim kurarken, ilgili alt ağdaki sanal makinelerin kaynak IP adresleri genel IPv4 adreslerinden özel IPv4 adreslerine döner. Hizmete giden mevcut açık TCP bağlantıları bu geçiş sırasında kapatılır. Bir alt ağ için hizmete yönelik hizmet uç noktasını etkinleştirmeden veya devre dışı bırakmadan önce çalışan kritik görev olmadığından emin olun. Ayrıca uygulamalarınızın IP adresi değişikliğinin ardından Azure hizmetlerine otomatik olarak bağlanabildiğinden emin olun.

  IP adresi değişiklikleri yalnızca sanal ağınızdan giden hizmet trafiğini etkiler. Sanal makinelerinize atanmış genel IPv4 adreslerinin gelen/giden trafiği üzerinde herhangi bir etki görülmez. Azure hizmetleri açısından, Azure genel IP adreslerini kullanan mevcut güvenlik duvarı kurallarınız varsa bu kurallar sanal ağ özel adresine geçiş yapıldığında çalışmaz.
- Hizmet uç noktaları kullanıldığında, Azure hizmetlerine yönelik DNS girişleri güncel durumdaki halde kalır ve Azure hizmetine atanmış olan ortak IP adreslerini çözümlemeye devam eder.

- Hizmet uç noktasına sahip ağ güvenlik grupları (NSG):
  - Varsayılan olarak NSG'ler giden İnternet trafiğine ve dolayısıyla sanal ağınızdan Azure hizmetlerine giden trafiğe izin verir. Bu durum hizmet uç noktalarında da aynı şekilde çalışmaya devam eder. 
  - Giden İnternet trafiğinin tümünü reddetmek ve yalnızca belirli Azure hizmetlerine giden trafiğe izin vermek istiyorsanız NSG'lerinizde [hizmet etiketlerini](security-overview.md#service-tags) kullanabilirsiniz. Desteklenen Azure hizmetlerini NSG kurallarında hedef olarak belirtebilir ve her etikete ait IP adreslerinin bakımının Azure tarafından yapılmasını sağlayabilirsiniz. Daha fazla bilgi için bkz. [NSG'ler için Azure Hizmet etiketleri.](security-overview.md#service-tags) 

### <a name="scenarios"></a>Senaryolar

- Eşlenen **, bağlı veya birden çok sanal ağ**: Azure hizmetlerinin bir sanal ağ veya birden çok sanal ağ içindeki birden çok alt ağa güvenliğini sağlamak için alt ağların her birinde hizmet uç noktalarını bağımsız olarak etkinleştirebilir ve Azure hizmet kaynaklarını tüm alt ağlarla güvenli hale getirebilirsiniz.
- **Bir sanal ağdan Azure hizmetlerine giden trafiği Filtreleme**: Bir sanal ağdan bir Azure hizmetine giden trafiği incelemek veya filtrelemek istiyorsanız, sanal ağ içinde bir ağ sanal gereci dağıtabilirsiniz. Ardından hizmet uç noktalarını ağ sanal gerecinin dağıtılmış olduğu alt ağa uygulayabilir ve Azure hizmet kaynağını yalnızca bu alt ağ ile sınırlayabilirsiniz. Bu senaryo, Azure hizmet erişimini ağ sanal gereci filtresi kullanarak sanal ağınızdan yalnızca belirli Azure kaynaklarına gidecek şekilde sınırlamak istediğinizde yararlı olabilir. Daha fazla bilgi için bkz. [Ağ sanal gereçleri ile çıkış](/azure/architecture/reference-architectures/dmz/nva-ha).
- **Azure kaynaklarını doğrudan sanal ağlara dağıtılan hizmetlere güvenli hale getirme**: Çeşitli Azure Hizmetleri, bir sanal ağdaki belirli alt ağlara doğrudan dağıtılabilir. Yönetilen hizmet alt ağında bir hizmet uç noktası kurarak Azure hizmet kaynaklarını [yönetilen hizmet](virtual-network-for-azure-services.md) alt ağlarına ayırabilirsiniz.
- **Bir Azure sanal makinesinden disk trafiği**: Yönetilen/yönetilmeyen diskler için sanal makine disk trafiği (Mount ve çıkarma dahil), Azure depolama için hizmet uç noktası yönlendirme değişikliklerinden etkilenmez. Ağ seçmek için hizmet uç noktaları ve [Azure Depolama ağ kuralları](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json) aracılığıyla REST erişimini sayfa bloblarına sınırlayabilirsiniz. 

### <a name="logging-and-troubleshooting"></a>Günlüğe kaydetme ve sorun giderme

Hizmet uç noktaları belirli bir hizmet için yapılandırıldıktan sonra geçerli olan hizmet uç noktasını aşağıdaki şekilde doğrulayabilirsiniz: 
 
- Hizmet tanılamada herhangi bir hizmet isteğinin kaynak IP adresini doğrulama. Hizmet uç noktalarına sahip tüm yeni isteklerin kaynak IP adresi değerinde isteği sanal ağınızdan gönderen istemciye atanmış olan sanal ağ özel IP adresi gösterilir. Uç noktası olmadığında bu adres Azure genel IP adreslerinden biri olur.
- Bir alt ağdaki herhangi bir ağ arabiriminin etkin yollarını görüntüleme. Hizmet yolu:
  - Her hizmetin adres ön eki aralıklarına daha belirli bir varsayılan yolu gösterir
  - nextHopType değeri *VirtualNetworkServiceEndpoint* olarak belirlenmiştir
  - Zorlamalı tünel rotalarıyla kıyaslandığında geçerli hizmete daha doğrudan bir bağlantı belirtir

>[!NOTE]
> Hizmet uç noktası rotaları, Azure hizmeti olarak adres ön eki eşleşmesi için BGP veya UDR rotasını geçersiz kılar. [Geçerli rotalarla sorun giderme](diagnose-network-routing-problem.md) hakkında daha fazla bilgi edinin

## <a name="provisioning"></a>Sağlama

Hizmet uç noktaları, bir sanal ağda yazma erişimine sahip bir kullanıcı tarafından sanal ağlarda birbirinden bağımsız olarak yapılandırılabilir. Azure hizmet kaynaklarını bir sanal ağa güvenli hale getirmek için, eklenmekte olan alt ağlar için *Microsoft. Network/virtualNetworks/alt ağları/Jodavetli Aserviceendpoint/Action* iznine sahip olmalıdır. Bu izin varsayılan olarak yerleşik hizmet yöneticisi rollerinde mevcuttur ve özel roller oluşturularak değiştirilebilir.

[Yerleşik roller](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ve [özel rollere](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) belirli izinlerin atanması hakkında daha fazla bilgi edinin.

Sanal ağlar ve Azure hizmet kaynakları aynı ağda veya farklı aboneliklerde olabilir. Sanal ağ ve Azure hizmet kaynaklarının farklı aboneliklerde olması halinde kaynakların aynı Active Directory (AD) kiracısı altında bulunması gerekir. 

## <a name="pricing-and-limits"></a>Fiyatlandırma ve limitler

Hizmet uç noktalarının kullanımından ek ücret alınmaz. Azure hizmetleri (Azure Depolama, Azure SQL Veritabanı vb.) için güncel fiyatlandırma modeli uygulanır.

Bir sanal ağ içinde sınırsız sayıda hizmet uç noktası oluşturulabilir.

Azure depolama hesapları gibi belirli Azure Hizmetleri, kaynağın güvenliğini sağlamak için kullanılan alt ağ sayısı için sınırlara zorlayabilir. Ayrıntılar için [Sonraki adımlar](#next-steps) bölümündeki hizmet belgelerini inceleyin.

## <a name="virtual-network-service-endpoint-policies"></a>Sanal ağ hizmet uç noktası İlkesi 

Sanal ağ hizmet uç noktası ilkelerini yalnızca belirli bir Azure hizmet kaynakları, hizmet uç noktaları izin vererek, Azure Hizmetleri için sanal ağ trafiğini filtrelemenize olanak tanır. Hizmet uç noktası ilkeleri, Azure Hizmetleri için sanal ağ trafiği için ayrıntılı erişim denetimi sağlar. Daha fazla bilgi: [Sanal ağ hizmeti uç noktası Ilkeleri](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview)

## <a name="faqs"></a>SSS

Sık sorulan sorular için bakmak [sanal ağ hizmet uç noktası SSS](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq#virtual-network-service-endpoints)

## <a name="next-steps"></a>Sonraki adımlar

- [Sanal ağ hizmet uç noktalarını nasıl yapılandıracağınızı](tutorial-restrict-network-access-to-resources.md) öğrenin
- [Bir Azure Depolama hesabını bir sanal ağ ile nasıl sınırlandıracağınızı](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json) öğrenin
- [Bir Azure SQL Veritabanını bir sanal ağ ile nasıl sınırlandıracağınızı](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) öğrenin
- Bilgi edinmek için nasıl [güvenli bir sanal ağ için bir Azure SQL veri ambarı](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fsql-data-warehouse%2ftoc.json)
- [Sanal ağlar için Azure hizmet tümleştirmesi](virtual-network-for-azure-services.md) hakkında bilgi edinin
- Hakkında bilgi edinin [sanal ağ hizmet uç noktası İlkesi](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview)
-  Hızlı başlangıç: [Azure Resource Manager şablonu](https://azure.microsoft.com/resources/templates/201-vnet-2subnets-service-endpoints-storage-integration) bir sanal ağın alt ağında hizmet uç noktası ayarlamak ve bir Azure Depolama hesabını bu alt ağa güvenli hale getirmek için.

