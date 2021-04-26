---
title: "Azure ExpressRoute 'A genel bakış: özel bir bağlantı üzerinden Bağlan"
description: ExpressRoute’a Teknik Genel Bakış bölümünde, şirket içi ağınızı bir özel bağlantı üzerinden Azure’a genişletmek üzere ExpressRoute bağlantısının nasıl çalıştığı açıklanmaktadır.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: overview
ms.date: 10/05/2020
ms.author: duau
ms.openlocfilehash: e1cef56d15838d80079decc6e1da7ad830bd1cd9
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107029769"
---
# <a name="what-is-azure-expressroute"></a>Azure ExpressRoute nedir?
ExpressRoute, bir bağlantı sağlayıcının yardımıyla özel bağlantı üzerinden şirket içi ağlarınızı Microsoft bulutuna genişletmenizi sağlar. ExpressRoute'u kullanarak Microsoft Azure ve Microsoft 365 gibi Microsoft bulut hizmetleriyle bağlantı kurabilirsiniz.

Bağlantının kaynağı herhangi iki ağ (IP VPN), noktadan noktaya Ethernet ağı veya ortak barındırma tesisindeki bağlantı sağlayıcısı aracılığıyla sanal çapraz bağlantı olabilir. ExpressRoute bağlantıları, genel İnternet üzerinden geçmez. Bu da ExpressRoute bağlantılarının İnternet üzerinden yapılan tipik bağlantılara kıyasla daha güvenilir, daha hızlı, tutarlı gecikme sürelerine sahip ve daha yüksek güvenlikli olmasını sağlar. ExpressRoute kullanarak ağınızı Microsoft 'a bağlama hakkında bilgi için bkz. [ExpressRoute bağlantı modelleri](expressroute-connectivity-models.md).

![ExpressRoute bağlantısına genel bakış](./media/expressroute-introduction/expressroute-connection-overview.png)

> [!NOTE]
> ExpressRoute bağlamında Microsoft Edge, ExpressRoute bağlantı hattının Microsoft tarafındaki Edge yönlendiricilerini açıklar. Bu, ExpressRoute bağlantı hattının Microsoft 'un ağına giriş noktasıdır.
> 

## <a name="key-benefits"></a>Önemli avantajlar

* Bağlantı sağlayıcı üzerinden şirket içi ağınız ve Microsoft Cloud arasındaki Katman 3 bağlantısı. Herhangi bir ağdan herhangi bir (IPVPN) ağa, noktadan noktaya Ethernet bağlantısı veya Ethernet değişimi aracığıyla sanal çapraz bağlantısı üzerinden bağlantı olabilir.
* Coğrafi bölgedeki tüm bölgeler arasında Microsoft bulut hizmetlerine erişim.
* ExpressRoute premium eklentisine sahip tüm bölgelerde Microsoft hizmetlerine genel bağlantı.
* Ağınız ve Microsoft arasında BGP ile dinamik yönlendirme.
* Yüksek güvenilirlik için her eşleme konumunda yerleşik yedeklilik.
* Bağlantı çalışma süresi [SLA 'sı](https://azure.microsoft.com/support/legal/sla/).
* Skype Kurumsal için QoS.

Daha fazla bilgi için bkz. [ExpressRoute SSS](expressroute-faqs.md).

## <a name="features"></a>Özellikler

### <a name="layer-3-connectivity"></a>Katman 3 bağlantısı
Microsoft, şirket içi ağınız ile Azure ve Microsoft ortak adreslerinde bulunan örnekleriniz arasındaki yolları değiştirmek için endüstri standardı bir dinamik yönlendirme protokolü olan BGP'yi kullanır. Farklı trafik profilleri için ağınızda birden çok BGP oturumu oluştururuz. Daha fazla bilgi [ExpressRoute bağlantı hattı ve yönlendirme etki alanları](expressroute-circuit-peerings.md) makalesinde bulunabilir. 

### <a name="redundancy"></a>Yedeklilik
Her ExpressRoute bağlantı hattı, bağlantı sağlayıcısından/ağ ucunuzdaki bir [ExpressRoute konumundaki](./expressroute-locations.md#expressroute-locations) Iki Microsoft Enterprise Edge yönlendiricilerine (MSEE) iki bağlantı içerir. Microsoft, her MSEE için bir adet olmak üzere bağlantı sağlayıcısından veya ağınızın çıkış noktasından ikili BGP bağlantısı gerektirir. Kendi tarafınızdaki yedekli cihazlara veya Ethernet bağlantı hattına dağıtmamayı seçebilirsiniz. Ancak, bağlantı sağlayıcılar bağlantılarınızın yedekli olarak Microsoft’a devredildiğinden emin olmak için yedekli cihazlar kullanır. Yedekli Layer 3 bağlantı yapılandırması [SLA](https://azure.microsoft.com/support/legal/sla/)’mızın geçerli olması için bir gereksinimdir.

### <a name="connectivity-to-microsoft-cloud-services"></a>Microsoft bulut hizmetlerine bağlantı
ExpressRoute bağlantıları aşağıdaki hizmetlere erişim sağlar:
* Microsoft Azure hizmetleri
* Microsoft 365 hizmetleri

> [!NOTE]
> [!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]
> 

ExpressRoute üzerinde desteklenen hizmetlerin ayrıntılı listesi için [ExpressRoute SSS](expressroute-faqs.md) sayfasını ziyaret edebilirsiniz.

### <a name="connectivity-to-all-regions-within-a-geopolitical-region"></a>Coğrafi konumdaki tüm bölgelere bağlantı
[Eşleme konumlarımızdan](expressroute-locations.md) birinden Microsoft'a bağlanabilir ve coğrafi konum içindeki bölgelere erişebilirsiniz.

Örneğin, Microsoft 'a, ExpressRoute üzerinden Microsoft 'a bağlanıyorsanız. Kuzey ve Batı Avrupa 'da barındırılan tüm Microsoft bulut hizmetlerine erişebilirsiniz. Jeopolitik bölgeler, ilişkili Microsoft bulut bölgeleri ve karşılık gelen ExpressRoute eşleme konumlarına genel bakış için [ExpressRoute iş ortakları ve eşleme konumları](expressroute-locations.md) makalesine bakın.

### <a name="global-connectivity-with-expressroute-premium"></a>ExpressRoute Premium ile genel bağlantı
Gentik sınırlar arasında bağlantıyı genişletmek için [ExpressRoute Premium](expressroute-faqs.md) 'u etkinleştirebilirsiniz. Örneğin, Microsoft 'a Microsoft 'a ExpressRoute aracılığıyla bağlanıyorsanız, dünyanın tüm bölgelerinde barındırılan tüm Microsoft bulut hizmetlerine erişebilirsiniz. Güney Amerika veya Avustralya 'da dağıtılan hizmetlere de Kuzey ve Batı Avrupa bölgelerine erişirken aynı şekilde erişebilirsiniz. Ulusal bulutlar hariç tutulur.

### <a name="local-connectivity-with-expressroute-local"></a>ExpressRoute yerel ile yerel bağlantı
[Yerel SKU 'yu](expressroute-faqs.md)etkinleştirerek verileri uygun maliyetli bir şekilde aktarabilirsiniz. Yerel SKU ile verilerinizi istediğiniz Azure bölgesinin yakınında bir ExpressRoute konumuna taşıyabilirsiniz. Yerel ile veri aktarımı ExpressRoute bağlantı noktası ücretine dahil edilmiştir. 

### <a name="across-on-premises-connectivity-with-expressroute-global-reach"></a>ExpressRoute Global Reach ile şirket içi bağlantılar
ExpressRoute bağlantı hattınızı bağlayarak şirket içi siteleriniz arasında veri alışverişi yapmak için ExpressRoute Global Reach’i etkinleştirebilirsiniz. Örneğin, California 'da bir ExpressRoute devresine bağlı olan California 'da özel bir veri merkeziyse ve bu, Texas 'ta bir ExpressRoute devresine bağlı başka bir özel veri merkezinde ExpressRoute Global Reach ile, bu iki ExpressRoute devreninden özel veri merkezlerinizi birbirine bağlayabilirsiniz. Veri merkezleri arası trafiğiniz Microsoft ağı üzerinden geçer.

Daha fazla bilgi için bkz. [ExpressRoute Global Reach](expressroute-global-reach.md).
### <a name="rich-connectivity-partner-ecosystem"></a>Zengin bağlantı iş ortağı ekosistemi
ExpressRoute sürekli büyüyen bağlantı sağlayıcıları ve sistem tümleştirici ortakları ekosistemine sahiptir. En son bilgiler için bkz. [ExpressRoute ortakları ve eşleme konumları](expressroute-locations.md).

### <a name="connectivity-to-national-clouds"></a>Ulusal bulutlara bağlantı
Microsoft, özel coğrafi bölgeler ve müşteri kesimine yönelik yalıtılmış bulut ortamlarını çalıştırır. Ulusal bulutlar ve sağlayıcılar listesi için [ExpressRoute ortakları ve eşleme konumları](expressroute-locations.md) sayfasına başvurun.

### <a name="expressroute-direct"></a>ExpressRoute Direct
ExpressRoute Direct, müşterilere Microsoft'un dünya çapında stratejik noktalara yerleştirilmiş eşleme konumlarından oluşan küresel ağına doğrudan bağlanabilme fırsatı sunar. ExpressRoute Direct, ölçeklendirmekte etkin/etkin bağlantıyı destekleyen çift 100 Gbps bağlantı sağlar.

ExpressRoute Direct 'in sağladığı temel özellikler dahil, ancak bunlarla sınırlı değildir:

* Depolama ve Cosmos DB gibi hizmetler için Büyük Veri Alımı özelliği
* Bankacılık, kamu ve perakendecilik gibi denetime tâbi, adanmış ve yalıtılmış bağlantı gerektiren sektörler için fiziksel yalıtım
* Bağlantı hattı dağıtımının iş birimine dayalı detaylı denetimi

Daha fazla bilgi için bkz. [ExpressRoute Direct Hakkında](./expressroute-erdirect-about.md).

### <a name="bandwidth-options"></a>Bant genişliği seçenekleri
ExpressRoute bağlantı hattını çeşitli sayıda bant genişlikleriyle satın alabilirsiniz. Desteklenen bant genişlikleri, ardından aşağıda listelenmiştir. Destekledikleri bant genişliklerini belirlemek için bağlantı sağlayıcınıza başvurmayı unutmayın.

* 50 Mb/sn
* 100 Mb/sn
* 200 Mb/sn
* 500 Mb/sn
* 1 Gbps
* 2 Gbps
* 5 Gbps
* 10 Gbps

### <a name="dynamic-scaling-of-bandwidth"></a>Bant genişliğini dinamik ölçeklendirme
Bağlantınızı kesmeden ExpressRoute bağlantı hattı bant genişliğini (en iyi çaba ilkesine göre) artırabilirsiniz. Daha fazla bilgi için bkz. [ExpressRoute devresini değiştirme](expressroute-howto-circuit-portal-resource-manager.md#modify).

### <a name="flexible-billing-models"></a>Esnek faturalama modelleri
Size en uygun faturalama modelini seçin. Takip edilen faturalama modelleri arasında seçim yapın. Daha fazla bilgi için bkz. [ExpressRoute SSS](expressroute-faqs.md).

* **Sınırsız veri**. Aylık ücret üzerinden faturalandırılır; buna gelen ve giden tüm veri aktarımları ücretsiz dahildir.
* **Tarifeli veri**. Aylık ücret üzerinden faturalandırılır; gelen tüm veri aktarımları ücretsizdir. Giden veri aktarımı, her GB veri aktarımı için ücretlendirilir. Veri aktarımı bölgelere göre farklılık gösterir.
* **ExpressRoute premium eklentisi**. ExpressRoute premium, ExpressRoute bağlantı hattı için bir eklentidir. ExpressRoute premium eklentisi aşağıdaki yetenekleri sağlar: 
  * Azure ortak ve Azure özel eşleme için 4,000 yoldan 10,000 yola artırılmış yol sınırları.
  * Hizmetler için genel bağlantı. Herhangi bir bölgede (Ulusal bulutlar hariç) oluşturulan bir ExpressRoute devresi, dünyanın diğer bölgelerindeki kaynaklara erişebilir. Örneğin, Batı Avrupa’da oluşturulan bir sana ağa Silikon Vadisi’nde sağlanan bir ExpressRoute bağlantı hattı üzerinden erişilebilir.
  * Bağlantı hattı bağlantı genişliğine bağlı olarak 10’dan daha yüksek bir sınıra kadar artırılmış ExpressRoute bağlantı hattı başına VNet bağlantı sayısı.

## <a name="faq"></a>SSS
ExpressRoute hakkında sık sorulan sorular için bkz. [ExpressRoute SSS](expressroute-faqs.md).

## <a name="whats-new"></a><a name="new"></a>Yenilikler nelerdir?

RSS akışına abone olun ve [Azure Updates](https://azure.microsoft.com/updates/?category=networking&query=ExpressRoute) sayfasında en son ExpressRoute özelliği güncelleştirmelerini görüntüleyin.

## <a name="next-steps"></a>Sonraki adımlar
* Tüm önkoşulların sağlandığından emin olun. Bkz. [ExpressRoute önkoşulları](expressroute-prerequisites.md).
* [ExpressRoute bağlantı modelleri](expressroute-connectivity-models.md) hakkında bilgi edinin.
* Bir hizmet sağlayıcı bulun. Bkz. [ExpressRoute ortakları ve eşleme konumları](expressroute-locations.md).
