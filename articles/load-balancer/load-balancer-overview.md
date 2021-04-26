---
title: Azure Load Balancer nedir?
titleSuffix: Azure Load Balancer
description: Azure Load Balancer özelliklerine, mimarisine ve uygulamasına genel bakış. Load Balancer nasıl çalıştığını ve bulutta nasıl kullanacağınızı öğrenin.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: overview
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 1/25/2021
ms.author: allensu
ms.openlocfilehash: f847ef4f4490b64212e4cdcce3f9a89e94c7cd37
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106058604"
---
# <a name="what-is-azure-load-balancer"></a>Azure Load Balancer nedir?

*Yük Dengeleme* , bir arka uç kaynak veya sunucu grubu genelinde yük devretme yükünü (gelen ağ trafiği) eşit bir şekilde dağıtmaktadır. 

Azure Load Balancer, açık sistemler arası (OSı) modelin katman 4 ' te çalışır. Bu, istemcilerle ilgili tek iletişim noktasıdır. Yük dengeleyici, yük dengeleyicinin ön ucuna, arka uç havuzu örneklerine ulaşan gelen akışları dağıtır. Bu akışlar yapılandırılmış Yük Dengeleme kurallarına ve sistem durumu araştırmalara göre yapılır. Arka uç havuzu örnekleri, bir sanal makine ölçek kümesindeki Azure sanal makineleri veya örnekleri olabilir.

**[Ortak yük dengeleyici](./components.md#frontend-ip-configurations)** , sanal ağınız içindeki sanal makineler (VM) için giden bağlantılar sağlayabilir. Bu bağlantılar, özel IP adresleri genel IP adreslerine çevrilirken gerçekleştirilir. Ortak yük dengeleyiciler, sanal makinelerinize internet trafiğinin yükünü dengelemek için kullanılır.

Yalnızca ön uç üzerinde özel IP 'Lerin gerekli olduğu bir **[iç (veya özel) yük dengeleyici](./components.md#frontend-ip-configurations)** kullanılır. İç yük dengeleyiciler, bir sanal ağ içindeki trafiğin yükünü dengelemek için kullanılır. Bir yük dengeleyici ön uca bir karma senaryoda şirket içi ağdan erişilebilir.

<p align="center">
  <img src="./media/load-balancer-overview/load-balancer.svg" alt="Figure depicts both public and internal load balancers directing traffic to port 80 on multiple servers on a Web tier and port 443 on multiple servers on a business tier." width="512" title="Azure Load Balancer">
</p>

*Şekil: çok katmanlı uygulamaları hem genel hem de dahili Load Balancer kullanarak Dengeleme*

Ayrı yük dengeleyici bileşenleri hakkında daha fazla bilgi için bkz. [Azure Load Balancer Components](./components.md).

>[!NOTE]
> Azure, senaryolarınız için tam olarak yönetilen yük dengeleme çözümleri sunar. 
> * DNS tabanlı küresel yönlendirme yapmak istiyorsanız ve Aktarım Katmanı Güvenliği (TLS) protokolü sonlandırma ("SSL yük boşaltma"), HTTP/HTTPS isteği veya uygulama katmanı işleme gereksinimlerine sahip **değilseniz** [Traffic Manager](../traffic-manager/traffic-manager-overview.md)gözden geçirin. 
> * Uygulama katmanındaki bir bölgedeki sunucularınız arasında yük dengelemesi yapmak istiyorsanız [Application Gateway](../application-gateway/overview.md)gözden geçirin.
> * Web trafiğinizi küresel yönlendirmeyi iyileştirmeniz ve hızlı genel yük devretme ile en üst katman Son Kullanıcı performansını ve güvenilirliğini iyileştirmek için bkz. [ön kapı](../frontdoor/front-door-overview.md).
> 
> Uçtan uca senaryolarınız, bu çözümlerin gerektiğinde birleştirilmesinin avantajlarından yararlanabilir.
> Azure yük dengeleme seçenekleri karşılaştırması için bkz. [Azure 'da Yük Dengeleme seçeneklerine genel bakış](/azure/architecture/guide/technology-choices/load-balancing-overview).


## <a name="why-use-azure-load-balancer"></a>Neden Azure Load Balancer kullanmalıyım?
Azure Load Balancer, uygulamalarınızı ölçeklendirebilir ve yüksek oranda kullanılabilir hizmetler oluşturabilirsiniz. Yük dengeleyici hem gelen hem de giden senaryoları destekler. Yük dengeleyici, düşük gecikme süresi ve yüksek aktarım hızı sağlar ve tüm TCP ve UDP uygulamaları için milyonlarca akışa kadar ölçeklendirir.

Azure Standart Load Balancer kullanarak gerçekleştirebileceğiniz önemli senaryolar şunlardır:

- Azure sanal makinelerine **[iç](./quickstart-load-balancer-standard-internal-portal.md)** ve **[dış](./quickstart-load-balancer-standard-public-portal.md)** trafik yükünü dengeleyin.

- **[Kaynakları bölgeler](./tutorial-load-balancer-standard-public-zonal-portal.md)** arasında ve bölgeler **[arasında](./tutorial-load-balancer-standard-public-zone-redundant-portal.md)** dağıtarak kullanılabilirliği artırın.

- Azure sanal makineleri için **[giden bağlantıyı](./load-balancer-outbound-connections.md)** yapılandırın.

- Yük dengeli kaynakları izlemek için **[sistem durumu araştırmalarını](./load-balancer-custom-probe-overview.md)** kullanın.

- Bir sanal ağdaki sanal makinelere genel IP adresi ve bağlantı noktasıyla erişmek için **[bağlantı noktası iletme](./tutorial-load-balancer-port-forwarding-portal.md)** işlemini yapın.

- **[IPv6](../virtual-network/ipv6-overview.md)** **[Yük Dengelemesi](../virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md)** için desteği etkinleştirin.

- Standart yük dengeleyici, [Azure izleyici](../azure-monitor/overview.md)aracılığıyla çok boyutlu ölçümler sağlar.  Bu ölçümler, belirli bir boyut için filtrelenebilir, gruplandırılabilir ve parçalanılabilir.  Bu kişiler, hizmetinizin performansı ve durumuyla ilgili güncel ve geçmiş öngörüler sağlar. [Azure Load Balancer](./load-balancer-insights.md) içgörüler, bu ölçümler için kullanışlı görselleştirmelerle önceden yapılandırılmış bir pano sunar.  Kaynak Durumu de desteklenir. Daha fazla ayrıntı için **[Standart yük dengeleyici tanılamayı](load-balancer-standard-diagnostics.md)** inceleyin.

- **[Birden çok bağlantı noktası, birden çok IP adresi veya her ikisinde](./load-balancer-multivip-overview.md)** Yük Dengeleme Hizmetleri.

- Azure bölgeleri arasında **[iç](./move-across-regions-internal-load-balancer-portal.md)** ve **[dış](./move-across-regions-external-load-balancer-portal.md)** yük dengeleyici kaynaklarını taşıyın.

- Tüm bağlantı noktalarında TCP ve UDP Flow 'u aynı anda **[ha bağlantı noktalarını](./load-balancer-ha-ports-overview.md)** kullanarak yük dengeleyin.

### <a name="secure-by-default"></a><a name="securebydefault"></a>Varsayılan olarak güvenli

* Standart yük dengeleyici, sıfır güvenli ağ güvenlik modelinde oluşturulmuştur.

* Standart Load Balancer, varsayılan olarak ve sanal ağınızın bir parçası olarak güvenlidir. Sanal ağ özel ve yalıtılmış bir ağ.  

* Standart yük dengeleyiciler ve standart genel IP adresleri, ağ güvenlik grupları tarafından açılmadıkları takdirde gelen bağlantılara kapalıdır. NSG 'ler, izin verilen trafiğe açıkça izin vermek için kullanılır.  Sanal makine kaynağınızın alt ağında veya NIC 'inde bir NSG 'niz yoksa trafiğin bu kaynağa erişmesine izin verilmez. NSG 'ler ve senaryonuza nasıl uygulayacakları hakkında bilgi edinmek için bkz. [ağ güvenlik grupları](../virtual-network/network-security-groups-overview.md).

* Temel yük dengeleyici varsayılan olarak Internet 'e açıktır. 

* Yük dengeleyici müşteri verilerini depolamaz.

## <a name="pricing-and-sla"></a>Fiyatlandırma ve SLA

Standart yük dengeleyici fiyatlandırma bilgileri için bkz. [yük dengeleyici fiyatlandırması](https://azure.microsoft.com/pricing/details/load-balancer/).
Temel yük dengeleyici ücretsiz olarak sunulur.
Bkz. [yük dengeleyici Için SLA](https://aka.ms/lbsla). Temel yük dengeleyicinin SLA 'sı yok.

## <a name="whats-new"></a>Yenilikler

RSS akışına abone olun ve [Azure Updates](https://azure.microsoft.com/updates/?category=networking&query=load%20balancer) sayfasında en son Azure Load Balancer Özellik güncelleştirmelerini görüntüleyin.

## <a name="next-steps"></a>Sonraki adımlar

Yük dengeleyici kullanmaya başlamak için bkz. [Genel Standart yük dengeleyici oluşturma](quickstart-load-balancer-standard-public-portal.md) .

Azure Load Balancer sınırlamaları ve bileşenleri hakkında daha fazla bilgi için bkz. [Azure Load Balancer bileşenler](./components.md) ve [Azure Load Balancer kavramları](./concepts.md)