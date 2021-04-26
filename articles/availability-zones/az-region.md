---
title: Kullanılabilirlik Alanlarını destekleyen Azure Hizmetleri
description: Azure 'da yüksek oranda kullanılabilir ve dayanıklı uygulamalar oluşturmak için, kaynaklarınızı çalıştırmak için kullanabileceğiniz fiziksel olarak ayrı konumlar sağlayın Kullanılabilirlik Alanları.
author: prsandhu
ms.service: azure
ms.topic: conceptual
ms.date: 04/21/2021
ms.author: prsandhu
ms.reviewer: cynthn
ms.custom: fasttrack-edit, mvc, references_regions
ms.openlocfilehash: 4c592c2d67df1e792200cc36449a6268807bbb56
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816269"
---
# <a name="azure-services-that-support-availability-zones"></a>Kullanılabilirlik Alanlarını destekleyen Azure Hizmetleri

Microsoft Azure küresel altyapı, müşterilerine en yüksek düzeyde artıklık ve dayanıklılık sağlamak için her katmanda tasarlanır ve oluşturulur. Azure altyapısı, bir başarısızlık yarıçapını sınırlayan ve bu nedenle müşteri uygulamalarına ve verilerine potansiyel etkiyi sınırlayan coğrafi bölgeler, bölgeler ve Kullanılabilirlik Alanları oluşur. Azure Kullanılabilirlik Alanları yapısı, veri merkezi hatalarıyla korunmaya yönelik bir yazılım ve ağ çözümü sağlamak ve müşterilerimiz için yüksek kullanılabilirlik (HA) sağlamak üzere geliştirilmiştir.

Kullanılabilirlik Alanları, Azure bölgesi içinde fiziksel olarak benzersiz konumlardır. Her bölge, bağımsız güç, soğutma ve ağ ile bir veya daha fazla veri merkezinden oluşur. Bir bölgedeki kullanılabilirlik alanları fiziksel ayrımı, büyük ölçekli taşımalar, büyük fırtınalarını 'ler ve süper fırtınalarını gibi bölge hatalarından ve site erişimini, güvenli akışını, genişletilmiş yardımcı programları ve kaynakların kullanılabilirliğini kesintiye uğratabilecek diğer olayları kısıtlar. Kullanılabilirlik Alanları ve ilişkili veri merkezleri, bir bölgenin güvenliği tehlikeye girerse, hizmetler, kapasite ve kullanılabilirlik bölgesindeki diğer Kullanılabilirlik Alanları tarafından desteklenir.

Tüm Azure Yönetim Hizmetleri bölge düzeyindeki hatalardan dayanıklı olacak şekilde tasarlanmıştır. Başarısızlık kapsamında, bir bölgedeki bir veya daha fazla kullanılabilirlik bölgesi hatası, tüm bölge arızasına kıyasla daha küçük bir hata Radius içermelidir. Azure, bir bölgedeki yönetim hizmetlerinin bölge düzeyindeki başarısızlığından kurtudan kurtarır. Azure, bir bölgedeki Kullanılabilirlik Alanları arasında dağıtılan müşteri kaynaklarını etkileyen tüm sorunları engellemek için bir bölge içinde tek seferde kritik bakım gerçekleştirir.


![3 bölge içeren bir Azure bölgesinin kavramsal görünümü](./media/az-region/azure-region-availability-zones.png)


Kullanılabilirlik alanları destekleyen Azure hizmetleri üç kategoride yer almalıdır: **Bölgesel**, bölgesel olarak **yedekli** ve **bölgesel olmayan** hizmetler. Müşteri iş yükleri, uygulama performansını ve dayanıklılığı karşılamak üzere bu mimari senaryolarından herhangi birini kullanacak şekilde kategorilere ayrılmıştır.

- **ZGen Hizmetleri** – daha sıkı gecikme veya performans gereksinimlerine ulaşmak için bir kaynak belirli, otomatik olarak seçilen bir kullanılabilirlik bölgesine dağıtılabilir.  Dayanıklılık, uygulamalar ve veriler bölge içindeki bir veya daha fazla bölgeye çoğaltılarak kendi kendine tasarlanmıştır.  Kaynaklar belirli bir bölgeye sabitlenebilir. Örneğin, sanal makineler, yönetilen diskler veya standart IP adresleri belirli bir bölgeye sabitlenebilir ve bu sayede bir veya daha fazla kaynak örneği bölgelere yayılarak artan esnekliği.

- Bölgesel olarak **yedekli hizmetler** – kaynaklar, bölgeler arasında otomatik olarak çoğaltılır veya dağıtılır. Örneğin ZRS, bir bölge hatası verilerin HA 'sini etkilemeyecek şekilde verileri üç bölge arasında çoğaltır.  

- **Bölgesel olmayan hizmetler** – hizmetler Azure coğrafi bölgeler tarafından her zaman kullanılabilir ve bölge genelinde kesintiler ve bölge genelinde kesintilere karşı dayanıklıdır. 


Azure 'da kapsamlı iş sürekliliği elde etmek için Azure bölge çiftleriyle Kullanılabilirlik Alanları birleşimini kullanarak uygulama mimarinizi geliştirin. Uygulamalarınızı ve verilerinizi Azure bölgesindeki Kullanılabilirlik Alanları kullanarak yüksek kullanılabilirlik ve olağanüstü durum kurtarma koruması için Azure bölgelerinde zaman uyumsuz olarak çoğaltma yapabilirsiniz. Daha fazla bilgi edinmek için [kullanılabilirlik alanları kullanarak yüksek kullanılabilirlik için çözüm oluşturma](/azure/architecture/high-availability/building-solutions-for-high-availability)makalesini okuyun. 

## <a name="azure-services-supporting-availability-zones"></a>Kullanılabilirlik Alanları destekleyen Azure hizmetleri

 - Eski nesil sanal makineler listelenmez. Daha fazla bilgi için bkz. [önceki nesil sanal makine boyutları](../virtual-machines/sizes-previous-gen.md).
 - [Azure 'Daki bölgelerde ve kullanılabilirlik alanları](az-overview.md)bölümünde belirtildiği gibi, bazı hizmetler bölgesel değildir. Bu hizmetlerin belirli bir Azure bölgesine bağımlılığı yoktur, bu nedenle bölge genelinde kesintiler ve bölge genelinde kesintiler vardır.  Bölgesel olmayan hizmetlerin listesi, [bölgeye göre kullanılabilir ürünler](https://azure.microsoft.com/global-infrastructure/services/)' de bulunabilir.


## <a name="azure-regions-with-availability-zones"></a>Kullanılabilirlik Alanları olan Azure bölgeleri
 

| Kuzey ve Güney Amerika           | Avrupa               | Afrika              | Asya Pasifik   |
|--------------------|----------------------|---------------------|----------------|
|                    |                      |                     |                |
| Güney Brezilya       | Orta Fransa       | Güney Afrika Kuzey * | Doğu Avustralya |
| Orta Kanada     | Almanya Orta Batı |                     | Orta Hindistan * |
| Central US         | Kuzey Avrupa         |                     | Doğu Japonya     |
| Doğu ABD            | Güney Birleşik Krallık             |                     | Kore Orta * |
| Doğu ABD 2          | West Europe          |                     | Güneydoğu Asya |
| Orta Güney ABD |                      |                     |                |
| US Gov Virginia    |                      |                     |                |
| Batı ABD 2        |                      |                     |                |
| Batı ABD 3 *       |                      |                     |                |

\* Bu bölgelerde Kullanılabilirlik Alanları ve kullanılabilir Hizmetler desteği hakkında daha fazla bilgi edinmek için Microsoft Sales veya müşteri temsilcinizle iletişime geçin. Kullanılabilirlik Alanları destekleyecek yaklaşan bölgeler için bkz. [Azure geographiler](https://azure.microsoft.com/en-us/global-infrastructure/geographies/).


## <a name="azure-services-supporting-availability-zones"></a>Kullanılabilirlik Alanları destekleyen Azure hizmetleri

- Daha eski nesil sanal makineler aşağıda listelenmez. Daha fazla bilgi için bkz. [önceki nesil sanal makine boyutları](../virtual-machines/sizes-previous-gen.md).

- Bazı hizmetler bölgesel değildir; daha fazla bilgi için bkz. [Azure 'Da bölgeler ve kullanılabilirlik alanları](az-overview.md) . Bu hizmetlerin, belirli bir Azure bölgesine bağımlılığı yoktur, bu da bölge genelinde kesintiler ve bölge genelinde kesintiler için esnek hale getirir.  Bölgesel olmayan hizmetlerin listesi, [bölgeye göre kullanılabilir ürünler](https://azure.microsoft.com/global-infrastructure/services/)' de bulunabilir.


### <a name="zone-resilient-services"></a>Bölge dayanıklı Hizmetleri 

: globe_with_meridians: bölgesel olmayan hizmetler-hizmetler Azure coğrafi bölgeler tarafından her zaman kullanılabilir ve bölge genelinde kesintiler ve bölge genelinde kesintiler sağlar.

: large_blue_diamond: bölge genelinde kesintiler için esnek 

**Temel hizmetler**

|     Ürünler                                                    | Dayanıklılık             |
|-----------------------------------------------------------------|:----------------------------:|
|     [Application Gateway (v2)](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant)                                  | : large_blue_diamond:  |
|     [Azure Backup](https://docs.microsoft.com/azure/backup/backup-create-rs-vault#set-storage-redundancy)                                                | : large_blue_diamond:  |
|     [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/high-availability#availability-zone-support)                                           | : large_blue_diamond:  |
|     [Azure Data Lake Storage Gen 2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction)                             | : large_blue_diamond:  |
|     [Azure Express Route](https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute)                                       | : large_blue_diamond:  |
|     [Azure genel IP](https://docs.microsoft.com/azure/virtual-network/public-ip-addresses)                                           | : large_blue_diamond:  |
|     Azure SQL veritabanı ([genel amaçlı katmanı](https://docs.microsoft.com/azure/azure-sql/database/high-availability-sla))                 | : large_blue_diamond:  |
|     Azure SQL veritabanı ([Premium & iş açısından kritik katmanı](https://docs.microsoft.com/azure/azure-sql/database/high-availability-sla))     | : large_blue_diamond:  |
|     [Disk Depolama](https://docs.microsoft.com/azure/storage/common/storage-redundancy)                                                | : large_blue_diamond:  |
|     [Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr#availability-zones)                                                  | : large_blue_diamond:  |
|     [Anahtar Kasası](https://docs.microsoft.com/azure/key-vault/general/disaster-recovery-guidance)                                                   | : large_blue_diamond:  |
|     [Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones)                                               | : large_blue_diamond:  |
|     [Service Bus](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-geo-dr#availability-zones)                                                 | : large_blue_diamond:  |
|     [Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cross-availability-zones#:~:text=An%20Availability%20Zone%20is%20a%20unique%20physical%20location,zones.%20This%20will%20ensure%20high-availability%20of%20your%20applications)                                            | : large_blue_diamond:  |
|     [Depolama hesabı](https://docs.microsoft.com/azure/storage/common/storage-redundancy)                                           | : large_blue_diamond:  |
|     Depolama:   [sık/seyrek erişimli BLOB depolama katmanları](https://docs.microsoft.com/azure/storage/common/storage-redundancy)                      | : large_blue_diamond:  |
|     Depolama:   [yönetilen diskler](https://docs.microsoft.com/azure/virtual-machines/managed-disks-overview)                                    | : large_blue_diamond:  |
|     [Sanal Makine Ölçek Kümeleri](https://docs.microsoft.com/azure/virtual-machine-scale-sets/scripts/cli-sample-zone-redundant-scale-set)                               | : large_blue_diamond:  |
|     [Sanal makineler](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                                          | : large_blue_diamond:  |
|     Sanal makineler: [AV2-Series](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                              | : large_blue_diamond:  |
|     Sanal makineler: [BS serisi](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                               | : large_blue_diamond:  |
|     Sanal makineler: [DSv2-Series](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                             | : large_blue_diamond:  |
|     Sanal makineler: [DSv3-Series](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                            | : large_blue_diamond:  |
|     Sanal makineler: [dv2-Series](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                             | : large_blue_diamond:  |
|     Sanal makineler: [Dv3-Series](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                              | : large_blue_diamond:  |
|     Sanal makineler: [ESv3-Series](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                             | : large_blue_diamond:  |
|     Sanal makineler: [Ev3-Series](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                              | : large_blue_diamond:  |
|     Sanal makineler: [F serisi](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                                | : large_blue_diamond:  |
|     Sanal makineler: [FS Serisi](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                               | : large_blue_diamond:  |
|     Sanal makineler: [paylaşılan görüntü Galerisi](https://docs.microsoft.com/azure/virtual-machines/shared-image-galleries#make-your-images-highly-available) | : large_blue_diamond:  |
|     [Sanal ağ](https://docs.microsoft.com/azure/vpn-gateway/create-zone-redundant-vnet-gateway)                                         | : large_blue_diamond:  |
|     [VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/about-zone-redundant-vnet-gateways)                                             | : large_blue_diamond:  |


**Temel hizmetler**


|     Ürünler                                                    | Dayanıklılık             |
|-----------------------------------------------------------------|:----------------------------:|
|     [App Service ortamları](https://docs.microsoft.com/azure/app-service/environment/zone-redundancy)                                    | : large_blue_diamond:  |
|     [Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/overview)                      | : large_blue_diamond:  |
|     [Azure API Management](https://docs.microsoft.com/azure/api-management/zone-redundancy)                      | : large_blue_diamond:  |
|     [Azure Bastion](https://docs.microsoft.com/azure/bastion/bastion-overview)                                               | : large_blue_diamond:  |
|     [Redis için Azure Önbelleği](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-high-availability)                              | : large_blue_diamond:  |
|     [Azure Bilişsel Arama](https://docs.microsoft.com/azure/search/search-performance-optimization#availability-zones)               | : large_blue_diamond:  |
|     Azure bilişsel hizmetler: [metin analizi](https://docs.microsoft.com/azure/cognitive-services/text-analytics/)                    | : large_blue_diamond:  |
|     [Azure Veri Gezgini](https://docs.microsoft.com/azure/data-explorer/create-cluster-database-portal)                               | : large_blue_diamond:  |
|     MySQL için Azure veritabanı – [esnek sunucu](https://docs.microsoft.com/azure/mysql/flexible-server/concepts-high-availability)                  | : large_blue_diamond:  |
|     PostgreSQL için Azure veritabanı – [esnek sunucu](https://docs.microsoft.com/azure/postgresql/flexible-server/overview)             | : large_blue_diamond:  |
|     [Azure DDoS Koruması](https://docs.microsoft.com/azure/ddos-protection/ddos-faq)                                       | : large_blue_diamond:  |
|     [Azure Disk Şifrelemesi](https://docs.microsoft.com/azure/virtual-machines/disks-redundancy)                                       | : large_blue_diamond:  |
|     [Azure Güvenlik Duvarı](https://docs.microsoft.com/azure/firewall/deploy-availability-zone-powershell#:~:text=For%20more%20information%20about%20Azure%20Firewall%20Availability%20Zones%2C,This%20creates%20a%20zone-redundant%20IP%20address%20by%20default)                                              | : large_blue_diamond:  |
|     [Azure Güvenlik Duvarı Yöneticisi](https://docs.microsoft.com/azure/firewall-manager/quick-firewall-policy)                                      | : large_blue_diamond:  |
|     [Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/availability-zones)                              | : large_blue_diamond:  |
|     [Azure Özel Bağlantı](https://docs.microsoft.com/azure/private-link/private-link-overview)                                          | : large_blue_diamond:  |
|     [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery)                                         | : large_blue_diamond:  |
|     Azure SQL: [sanal makine](https://docs.microsoft.com/azure/azure-sql/database/high-availability-sla)                                  | : large_blue_diamond:  |
|     [Azure Web Uygulaması Güvenlik Duvarı](https://docs.microsoft.com/azure/firewall/deploy-availability-zone-powershell#:~:text=For%20more%20information%20about%20Azure%20Firewall%20Availability%20Zones%2C,This%20creates%20a%20zone-redundant%20IP%20address%20by%20default)                              | : large_blue_diamond:  |
|     [Container Registry](https://docs.microsoft.com/azure/container-registry/zone-redundancy)                                          | : large_blue_diamond:  |
|     [Event Grid](https://docs.microsoft.com/azure/event-grid/overview)                                                  | : large_blue_diamond:  |
|     [Ağ İzleyicisi](https://docs.microsoft.com/azure/network-watcher/frequently-asked-questions#service-availability-and-redundancy)                                             | : large_blue_diamond:  |
|     Ağ Izleyicisi: [Trafik Analizi](https://docs.microsoft.com/azure/network-watcher/frequently-asked-questions#service-availability-and-redundancy)                          | : large_blue_diamond:  |
|     [Power BI Embedded](https://docs.microsoft.com/power-bi/admin/service-admin-failover#what-does-high-availability)                                           | : large_blue_diamond:  |
|     [Premium blob depolaması](https://docs.microsoft.com/azure/storage/blobs/storage-blob-performance-tiers#:~:text=Table%201%20%20%20%20Area%20%20,%20%20Currently%20supports%20only%20locally-redundan%20...%20)                                        | : large_blue_diamond:  |
|     Depolama: [Azure Premium dosyaları](https://docs.microsoft.com/azure/storage/files/storage-files-planning)                                | : large_blue_diamond:  |
|     Sanal makineler: [Azure adanmış ana bilgisayar](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                     | : large_blue_diamond:  |
|     Sanal makineler: [Ddsv4-Series](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                              | : large_blue_diamond:  |
|     Sanal makineler: [Ddv4-Series](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                               | : large_blue_diamond:  |
|     Sanal makineler: [Dsv4-Series](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                               | : large_blue_diamond:  |
|     Sanal makineler: [dv4-Series](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                                | : large_blue_diamond:  |
|     Sanal makineler: [Edsv4-Series](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                              | : large_blue_diamond:  |
|     Sanal makineler: [Edv4-Series](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                               | : large_blue_diamond:  |
|     Sanal makineler: [Esv4-Series](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                               | : large_blue_diamond:  |
|     Sanal makineler: [Ev4-Series](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                                | : large_blue_diamond:  |
|     Sanal makineler: [Fsv2-Series](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                               | : large_blue_diamond:  |
|     Sanal makineler: [a serisi](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                                  | : large_blue_diamond:  |
|     [Sanal WAN](https://docs.microsoft.com/azure/virtual-wan/virtual-wan-about#how-are-availability-zones-and-resiliency-handled-in-virtual-wan)                                                 | : large_blue_diamond:  |
|     Sanal WAN: [ExpressRoute](https://docs.microsoft.com/azure/virtual-wan/virtual-wan-about#how-are-availability-zones-and-resiliency-handled-in-virtual-wan)                                   | : large_blue_diamond:  |
|     Sanal WAN: [Noktadan siteye VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/about-zone-redundant-vnet-gateways)                      | : large_blue_diamond:  |
|     Sanal WAN: [siteden siteye VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/about-zone-redundant-vnet-gateways)                       | : large_blue_diamond:  |


**Özel hizmetler**

|     Ürünler                                                    | Dayanıklılık             |
|-----------------------------------------------------------------|:----------------------------:|
|     Azure Red Hat OpenShift                                     | : large_blue_diamond:  |
|     Bilişsel hizmetler: anomali algılayıcısı                        | : large_blue_diamond:  |
|     Bilişsel hizmetler: form tanıyıcı                         | : large_blue_diamond:  |
|     Depolama: Ultra disk                                         | : large_blue_diamond:  |


**Bölgesel olmayan**

|     Ürünler                                                    | Dayanıklılık             |
|-----------------------------------------------------------------|:----------------------------:|
|     Azure DNS                                                   | : globe_with_meridians: |
|     Azure Active Directory                                    | : globe_with_meridians: |
|     Azure Gelişmiş Tehdit Koruması                            | : globe_with_meridians: |
|     Azure Danışmanı                                               | : globe_with_meridians: |
|     Azure Blueprints                                            | : globe_with_meridians: |
|     Azure Bot Hizmetleri                                          | : globe_with_meridians: |
|     Azure Front Door                                            | : globe_with_meridians: |
|     IoT için Azure Defender                                    | : globe_with_meridians: |
|     Azure Front Door                                            | : globe_with_meridians: |
|     Azure Information Protection                              | : globe_with_meridians: |
|     Azure hafif kullanım                                          | : globe_with_meridians: |
|     Azure yönetilen uygulamalar                                | : globe_with_meridians: |
|     Azure Haritalar                                                  | : globe_with_meridians: |
|     Azure Performans Tanılama                               | : globe_with_meridians: |
|     Azure İlkesi                                                | : globe_with_meridians: |
|     Azure Kaynak Grafiği                                      | : globe_with_meridians: |
|     Azure Sentinel                                              | : globe_with_meridians: |
|     Azure Stack                                                 | : globe_with_meridians: |
|     Azure Stack uç                                          | : globe_with_meridians: |
|     Cloud Shell                                                 | : globe_with_meridians: |
|     Content Delivery Network                                    | : globe_with_meridians: |
|     Maliyet Yönetimi                                             | : globe_with_meridians: |
|     Microsoft Azure için Müşteri Kasası                      | : globe_with_meridians: |
|     Intune                                                      | : globe_with_meridians: |
|     Microsoft Azure eşleme hizmeti                           | : globe_with_meridians: |
|     Microsoft Azure portal                                    | : globe_with_meridians: |
|     Microsoft Cloud App Security                                | : globe_with_meridians: |
|     Microsoft Graph                                             | : globe_with_meridians: |
|     Güvenlik Merkezi                                           | : globe_with_meridians: |
|     Traffic Manager                                           | : globe_with_meridians: |


## <a name="pricing-for-vms-in-availability-zones"></a>Kullanılabilirlik Alanları VM 'Leri için fiyatlandırma

Azure Kullanılabilirlik Alanları Azure aboneliğiniz ile kullanılabilir. Daha fazla bilgi için [bant genişliği fiyatlandırma sayfasına](https://azure.microsoft.com/pricing/details/bandwidth/)tıklayın.


## <a name="get-started-with-availability-zones"></a>Kullanılabilirlik Alanları kullanmaya başlayın

- [Sanal makine oluşturma](../virtual-machines/windows/create-portal-availability-zone.md)
- [PowerShell kullanarak yönetilen disk ekleme](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
- [Bölge yedekli sanal makine ölçek kümesi oluşturma](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
- [Bölgesel olarak yedekli bir ön uç ile bir Standart Load Balancer kullanarak VM 'lerde Yük Dengeleme](../load-balancer/quickstart-load-balancer-standard-public-cli.md?tabs=option-1-create-load-balancer-standard)
- [Bir bölge içindeki VM 'leri, bölgesel ön ucu ile standart Load Balancer kullanarak yük dengeleme](../load-balancer/quickstart-load-balancer-standard-public-cli.md?tabs=option-1-create-load-balancer-standard)
- [Alanlar arası yedekli depolama](../storage/common/storage-redundancy.md)
- [SQL veritabanı genel amaçlı katmanı](../azure-sql/database/high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)
- [Event Hubs coğrafi olağanüstü durum kurtarma](../event-hubs/event-hubs-geo-dr.md#availability-zones)
- [Service Bus coğrafi olağanüstü durum kurtarma](../service-bus-messaging/service-bus-geo-dr.md#availability-zones)
- [Alanlar arası yedekli sanal ağ geçidi oluşturma](../vpn-gateway/create-zone-redundant-vnet-gateway.md)
- [Azure Cosmos DB için bölge yedekli bölgesi ekleme](../cosmos-db/high-availability.md#availability-zone-support)
- [Redsıs için Azure önbelleği Başlarken Kullanılabilirlik Alanları](https://gist.github.com/JonCole/92c669ea482bbb7996f6428fb6c3eb97#file-redisazgettingstarted-md)
- [Azure Active Directory Domain Services örneği oluşturma](../active-directory-domain-services/tutorial-create-instance.md)
- [Kullanılabilirlik Alanları kullanan bir Azure Kubernetes hizmeti (AKS) kümesi oluşturma](../aks/availability-zones.md)


## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure’daki Bölgeler ve Kullanılabilirlik Alanları](az-overview.md)
