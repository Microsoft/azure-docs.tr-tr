---
title: "Azure ExpressRoute 'A genel bakış: özel bir bağlantı üzerinden Bağlan"
description: ExpressRoute’a Teknik Genel Bakış bölümünde, şirket içi ağınızı bir özel bağlantı üzerinden Azure’a genişletmek üzere ExpressRoute bağlantısının nasıl çalıştığı açıklanmaktadır.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: overview
ms.date: 09/18/2019
ms.author: mialdrid
ms.openlocfilehash: db728de120a50461257d8e85e708aa4c7f4d6190
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083418"
---
# <a name="expressroute-overview"></a>ExpressRoute'a genel bakış
ExpressRoute, bağlantı sağlayıcı tarafından kolaylaştırılan özel bağlantı üzerinden şirket içi ağlarınızı Microsoft bulutuna genişletmenizi sağlar. ExpressRoute ile Microsoft Azure ve Office 365 gibi Microsoft bulut hizmetleriyle bağlantı kurabilirsiniz.

Ortak yerleşim tesisinde bağlantı sağlayıcısı üzerinden herhangi bir ağdan herhangi bir ağa (IP VPN), noktadan noktaya Ethernet ağı veya sanal çapraz bağlantısından bağlantı olabilir. ExpressRoute bağlantıları ortak İnternet üzerinden geçmemektedir. Bu, ExpressRoute bağlantılarına Internet üzerinden tipik bağlantılardan daha fazla güvenilirlik, daha hızlı hız, tutarlı gecikme süreleri ve daha yüksek güvenlik sunma olanağı sağlar. ExpressRoute kullanarak ağınızı Microsoft’a bağlama hakkında bilgi için bkz. [ExpressRoute bağlantı modelleri](expressroute-connectivity-models.md).

![ExpressRoute bağlantısına genel bakış](./media/expressroute-introduction/expressroute-connection-overview.png)

## <a name="key-benefits"></a>Önemli avantajlar

* Bağlantı sağlayıcı üzerinden şirket içi ağınız ve Microsoft Cloud arasındaki Katman 3 bağlantısı. Herhangi bir ağdan herhangi bir (IPVPN) ağa, noktadan noktaya Ethernet bağlantısı veya Ethernet değişimi aracığıyla sanal çapraz bağlantısı üzerinden bağlantı olabilir.
* Coğrafi bölgedeki tüm bölgeler arasında Microsoft bulut hizmetlerine erişim.
* ExpressRoute premium eklentisine sahip tüm bölgelerde Microsoft hizmetlerine genel bağlantı.
* Ağınız ve Microsoft arasında BGP ile dinamik yönlendirme.
* Yüksek güvenilirlik için her eşleme konumunda yerleşik yedeklilik.
* Bağlantı çalışma süresi [SLA](https://azure.microsoft.com/support/legal/sla/).
* Skype Kurumsal için QoS.

Daha fazla bilgi için bkz. [ExpressRoute SSS](expressroute-faqs.md).

## <a name="features"></a>Özellikler

### <a name="layer-3-connectivity"></a>Katman 3 bağlantısı
Microsoft, şirket içi ağınız ile Azure ve Microsoft ortak adreslerinde bulunan örnekleriniz arasındaki yolları değiştirmek için endüstri standardı bir dinamik yönlendirme protokolü olan BGP'yi kullanır. Farklı trafik profilleri için ağınızda çoklu BGP oturumları kuruyoruz. Daha fazla bilgi [ExpressRoute bağlantı hattı ve yönlendirme etki alanları](expressroute-circuit-peerings.md) makalesinde bulunabilir.

### <a name="redundancy"></a>Yedeklilik
Her ExpressRoute bağlantı hattı, bağlantı sağlayıcısından veya ağınızın kenarından Microsoft Kurumsal kenar yönlendiricilerine (MSEEs) yapılan iki bağlantıdan oluşur. Microsoft, her MSEE için bir adet olmak üzere bağlantı sağlayıcısından veya ağınızın çıkış noktasından ikili BGP bağlantısı gerektirir. Kendi tarafınızdaki yedekli cihazlara veya Ethernet bağlantı hattına dağıtmamayı seçebilirsiniz. Ancak, bağlantı sağlayıcılar bağlantılarınızın yedekli olarak Microsoft’a devredildiğinden emin olmak için yedekli cihazlar kullanır. Yedekli Layer 3 bağlantı yapılandırması [SLA](https://azure.microsoft.com/support/legal/sla/)’mızın geçerli olması için bir gereksinimdir.

### <a name="connectivity-to-microsoft-cloud-services"></a>Microsoft bulut hizmetlerine bağlantı
ExpressRoute bağlantıları aşağıdaki hizmetlere erişim sağlar:
* Microsoft Azure hizmetleri
* Microsoft Office 365 hizmetleri

> [!NOTE]
> [!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]
> 

ExpressRoute üzerinde desteklenen hizmetlerin ayrıntılı listesi için [ExpressRoute SSS](expressroute-faqs.md) sayfasını ziyaret edebilirsiniz.

### <a name="connectivity-to-all-regions-within-a-geopolitical-region"></a>Coğrafi konumdaki tüm bölgelere bağlantı
[Eşleme konumlarımızdan](expressroute-locations.md) birinden Microsoft'a bağlanabilir ve coğrafi konum içindeki bölgelere erişebilirsiniz.

Örneğin, Microsoft'a Amsterdam'da ExpressRoute aracılığıyla bağlandıysanız Kuzey ve Batı Avrupa'da barındırılan tüm Microsoft bulut hizmetlerine erişiminiz olur. Jeopolitik bölgeler, ilişkili Microsoft bulut bölgeleri ve karşılık gelen ExpressRoute eşleme konumlarına genel bakış için [ExpressRoute iş ortakları ve eşleme konumları](expressroute-locations.md) makalesine bakın.

### <a name="global-connectivity-with-expressroute-premium"></a>ExpressRoute Premium ile genel bağlantı
Gentik sınırlar arasında bağlantıyı genişletmek için [ExpressRoute Premium](expressroute-faqs.md) 'u etkinleştirebilirsiniz. Örneğin, Microsoft'a Amsterdam'da ExpressRoute aracılığıyla bağlanırsanız dünyadaki tüm bölgelerde barındırılan tüm Microsoft bulut hizmetlerine (ulusal bulutlar dışında) erişiminiz olur. Güney Amerika ve Avustralya'da dağıtılan hizmetlere Kuzey ve Batı Avrupa bölgeleriyle aynı şekilde erişebilirsiniz.

### <a name="local-connectivity-with-expressroute-local"></a>ExpressRoute yerel ile yerel bağlantı
Verilerinizi istediğiniz Azure bölgenize yakın bir ExpressRoute konumuna getirebiliyorsanız, [Yerel SKU 'yu](expressroute-faqs.md) etkinleştirerek verileri uygun maliyetli bir şekilde aktarabilirsiniz. Yerel ile veri aktarımı ExpressRoute bağlantı noktası ücretine dahil edilmiştir. 

### <a name="across-on-premises-connectivity-with-expressroute-global-reach"></a>ExpressRoute Global Reach ile şirket içi bağlantılar
ExpressRoute bağlantı hattınızı bağlayarak şirket içi siteleriniz arasında veri alışverişi yapmak için ExpressRoute Global Reach’i etkinleştirebilirsiniz. Örneğin Kaliforniya’da, Silikon Vadisi’ndeki ExpressRoute’a bağlanmış olan özel bir veri merkeziniz ve Teksas'ta, Dallas'taki ExpressRoute'a bağlanmış bir başka özel veri merkeziniz varsa ExpressRoute Global Reach ile özel veri merkezlerinizi iki ExpressRoute bağlantı hattı aracılığıyla birbirlerine bağlayabilirsiniz. Veri merkezleri arası trafiğiniz Microsoft ağı üzerinden geçer.

Daha fazla bilgi için bkz. [ExpressRoute Global Reach](expressroute-global-reach.md).
### <a name="rich-connectivity-partner-ecosystem"></a>Zengin bağlantı iş ortağı ekosistemi
ExpressRoute sürekli büyüyen bağlantı sağlayıcıları ve sistem tümleştirici ortakları ekosistemine sahiptir. En son bilgiler için bkz. [ExpressRoute ortakları ve eşleme konumları](expressroute-locations.md).

### <a name="connectivity-to-national-clouds"></a>Ulusal bulutlara bağlantı
Microsoft, özel coğrafi bölgeler ve müşteri kesimine yönelik yalıtılmış bulut ortamlarını çalıştırır. Ulusal bulutlar ve sağlayıcılar listesi için [ExpressRoute ortakları ve eşleme konumları](expressroute-locations.md) sayfasına başvurun.

### <a name="expressroute-direct"></a>ExpressRoute Direct
ExpressRoute Direct, müşterilere Microsoft'un dünya çapında stratejik noktalara yerleştirilmiş eşleme konumlarından oluşan küresel ağına doğrudan bağlanabilme fırsatı sunar. ExpressRoute Direct, ölçeklendirilebilir ve Aktif/Aktif bağlantıyı destekleyen çift 100 Gbps bağlantı sunar.

ExpressRoute Direct'in başlıca özellikleri arasında diğerlerinin yanı sıra şunlar bulunur:

* Depolama ve Cosmos DB gibi hizmetler için Büyük Veri Alımı özelliği
* Bankacılık, kamu ve perakendecilik gibi denetime tâbi, adanmış ve yalıtılmış bağlantı gerektiren sektörler için fiziksel yalıtım
* Bağlantı hattı dağıtımının iş birimine dayalı detaylı denetimi

Daha fazla bilgi için bkz. [ExpressRoute Direct Hakkında](https://go.microsoft.com/fwlink/?linkid=2022973).

### <a name="bandwidth-options"></a>Bant genişliği seçenekleri
ExpressRoute bağlantı hattını çeşitli sayıda bant genişlikleriyle satın alabilirsiniz. Desteklenen bant genişlikleri aşağıda listelenmiştir. Destekledikleri bant genişliklerini belirlemek için bağlantı sağlayıcınıza başvurmayı unutmayın.

* 50 Mbps
* 100 Mb/sn
* 200 Mbps
* 500 Mb/sn
* 1 Gbps
* 2 Gbps
* 5 Gbps
* 10 Gbps

### <a name="dynamic-scaling-of-bandwidth"></a>Bant genişliğini dinamik ölçeklendirme
Bağlantınızı kesmeden ExpressRoute bağlantı hattı bant genişliğini (en iyi çaba ilkesine göre) artırabilirsiniz. Daha fazla bilgi için bkz. [ExpressRoute bağlantı hattı değiştirme](expressroute-howto-circuit-portal-resource-manager.md#modify).

### <a name="flexible-billing-models"></a>Esnek faturalama modelleri
Size en uygun faturalama modelini seçin. Aşağıda listelenen faturalama modelleri listesinden seçin. Daha fazla bilgi için bkz. [ExpressRoute SSS](expressroute-faqs.md).

* **Sınırsız veri**. Aylık ücret üzerinden faturalandırılır; buna gelen ve giden tüm veri aktarımları ücretsiz dahildir.
* **Tarifeli veri**. Aylık ücret üzerinden faturalandırılır; gelen tüm veri aktarımları ücretsizdir. Giden veri aktarımı, her GB veri aktarımı için ücretlendirilir. Veri aktarımı bölgelere göre farklılık gösterir.
* **ExpressRoute premium eklentisi**. ExpressRoute premium, ExpressRoute bağlantı hattı için bir eklentidir. ExpressRoute premium eklentisi aşağıdaki yetenekleri sağlar: 
  * Azure ortak ve Azure özel eşleme için 4,000 yoldan 10,000 yola artırılmış yol sınırları.
  * Hizmetler için genel bağlantı. Herhangi bir bölgede oluşturulan ExpressRoute bağlantı hattı dünyada bulunan tüm diğer bölgelerdeki kaynaklara erişime sahip olur. Örneğin, Batı Avrupa’da oluşturulan bir sana ağa Silikon Vadisi’nde sağlanan bir ExpressRoute bağlantı hattı üzerinden erişilebilir.
  * Bağlantı hattı bağlantı genişliğine bağlı olarak 10’dan daha yüksek bir sınıra kadar artırılmış ExpressRoute bağlantı hattı başına VNet bağlantı sayısı.

## <a name="faq"></a>SSS
ExpressRoute hakkında sık sorulan sorular için bkz. [ExpressRoute SSS](expressroute-faqs.md).

## <a name="next-steps"></a>Sonraki adımlar
* [ExpressRoute bağlantı modelleri](expressroute-connectivity-models.md) hakkında bilgi edinin.
* ExpressRoute bağlantıları ve yönlendirme etki alanları hakkında bilgi edinin. Bkz. [ExpressRoute bağlantı hatları ve etki alanlarını yönlendirme](expressroute-circuit-peerings.md).
* Bir hizmet sağlayıcı bulun. Bkz. [ExpressRoute ortakları ve eşleme konumları](expressroute-locations.md).
* Tüm önkoşulların sağlandığından emin olun. Bkz. [ExpressRoute önkoşulları](expressroute-prerequisites.md).
* [Yönlendirme](expressroute-routing.md), [NAT](expressroute-nat.md) ve [QoS](expressroute-qos.md) için gereksinimlere bakın.
* ExpressRoute bağlantınızı yapılandırın.
  * [ExpressRoute bağlantı hattı oluşturma ve değiştirme](expressroute-howto-circuit-portal-resource-manager.md)
  * [Bir ExpressRoute bağlantı hattı için eşlemeyi oluşturma ve değiştirme](expressroute-howto-routing-portal-resource-manager.md)
  * [Bir sanal ağı ExpressRoute bağlantı hattına bağlama](expressroute-howto-linkvnet-portal-resource-manager.md)
* Azure'un diğer başlıca [ağ özelliklerinden](../networking/networking-overview.md) bazıları hakkında bilgi edinin.
