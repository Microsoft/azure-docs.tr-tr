---
title: Azure ağ hizmetleri 'ni kullanarak uzaktan çalışma
description: Bu sayfa, uzaktan çalışmayı etkinleştirmek için kullanılabilen Azure ağ hizmetleri 'ni nasıl kullanabileceğinizi ve uzaktan çalışan artan sayıda kişiden kaynaklanan trafik sorunlarını nasıl azaltabileceğinizi açıklar.
services: networking
author: rambk
ms.service: virtual-network
ms.topic: article
ms.date: 03/26/2020
ms.author: rambala
ms.openlocfilehash: 835be5b867826d75732c9482743ad6a4ca2900e7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98231851"
---
# <a name="working-remotely-using-azure-networking-services"></a>Azure ağ hizmetleri 'ni kullanarak uzaktan çalışma

>[!NOTE]
> Bu makalede, Azure Ağ Hizmetleri, Microsoft ağı ve Azure iş ortağı ekosisteminden yararlanarak uzaktan nasıl çalışabileceğinizi ve COVı-19 çapraz olduğu için kullanıma sunulacak ağ sorunlarını azaltmayı nasıl kullanabileceğiniz açıklanmaktadır.

Bu makalede, kuruluşların kullanıcıları için uzaktan erişimi ayarlama veya mevcut çözümlerini, en yüksek kullanım dönemleri sırasında ek kapasiteye göre kullanma amacıyla kullanılabilen seçenekler açıklanmaktadır. Ağ mimarları aşağıdaki güçlüklerle başlatılacaktır:

- Ağ kullanımında bir artış ele.
- Şirketlerinin ve müşterilerinin daha fazla çalışanı için güvenilir güvenli bağlantı sağlayın.
- Dünya genelindeki uzak konumlara bağlantı sağlayın.

Tüm ağlar (örneğin, Özel WAN ve şirket çekirdeği ağları), yoğun uzak çalışan yüklerinden oluşan sıkışıklık yaşar. Performans sorunları genellikle yalnızca ev geniş bant ağlarında ve şirket içi şirketlerin ağları için VPN ağ geçitlerinde raporlanır.

Ağ planlayıcıları, farklı trafik türlerinin farklı ağ işleme önceliklerinin ve bazı akıllı yük yeniden yönlendirme/dağıtım tarafından gerekli olduğundan emin olmak için performans sorunlarını kolaylaştırma ve ağ tıkanıklığını hafifletmek için yardımcı olabilir. Örneğin, doktor-hasta etkileşiminin gerçek zamanlı tele-medecine trafiği, yüksek öneme sahip ve gecikme/değişim duyarlı. Öte yandan, aynı trafiğin çoğaltılmalarına göre çoğaltılması duyarlı değildir. Eski trafik, daha yüksek hizmet kalitesiyle en iyi ağ yoluyla yönlendirilmelidir; daha sonraki trafiği, en iyi alt yön aracılığıyla yönlendirmek kabul edilebilir.



## <a name="sharing-our-best-practices---azure-network-is-designed-for-elasticity-and-high-availability"></a>En iyi uygulamalarınızı paylaşma-Azure ağı, esneklik ve yüksek kullanılabilirlik için tasarlanmıştır

Azure, kaynakların kullanımında ani değişiklikler yapmak için tasarlanmıştır ve en yoğun kullanım dönemlerinde büyük ölçüde yardımcı olabilir. Ayrıca, Microsoft, World ' ı en büyük ağlardan birini korur ve çalışır. Microsoft 'un ağı, farklı türlerde hataları geçersiz kılacak yüksek kullanılabilirlik için tasarlandı: tek bir ağ öğesi hatasından bir bölgenin tamamına yönelik hata verebilir.

Microsoft ağı, gereksinimleri karşılayacak şekilde tasarlanmıştır ve Skype ve takımlar, CDN, gerçek zamanlı büyük veri analizi, Azure depolama, Bing ve Xbox için hassas multimedya trafiğini geciktir gibi farklı türlerde ağ trafiği için en iyi performansı sağlar. Farklı türlerde trafik için en iyi performansı sağlamak üzere Microsoft ağı, trafiğin kaynağına mümkün olduğunca yakın sürede geçiş yapmak için gereken tüm trafiği onaylar.

>[!NOTE] 
>Aşağıda açıklanan Azure ağ özelliklerinin kullanılması, daha iyi bir müşteri ağ deneyimi sağlamak üzere Microsoft Global Network 'ün trafik kaybı davranışını kullanır. Microsoft ağı 'nın trafik atması davranışı, en yoğun kullanım dönemlerinde sıkışıklık yaşayan ilk/son mil ağlardan mümkün olan en kısa sürede trafiği yüklemeye yardımcı olur.
>

## <a name="enable-employees-to-work-remotely"></a>Çalışanların uzaktan çalışmasını sağlama

Azure VPN Gateway, hem Noktadan siteye (P2S) hem de siteden siteye (S2S) VPN bağlantılarını destekler. Azure VPN ağ geçidini kullanarak, hem Azure dağıtılan kaynaklarınıza hem de şirket içi kaynaklarınıza güvenli bir şekilde erişmek için çalışanların bağlantılarını ölçeklendirebilirsiniz. Daha fazla bilgi için bkz. [Kullanıcıların uzaktan çalışmasını sağlama](../vpn-gateway/work-remotely-support.md). 

Güvenli Yuva Tünel Protokolü (SSTP) kullanıyorsanız, eşzamanlı bağlantı sayısı 128 ile sınırlıdır. Daha yüksek sayıda bağlantı almak için, OpenVPN veya Ikev2 'e geçiş yapmanız önerilir. Daha fazla bilgi için bkz. [SSTP 'Den OpenVPN protokolüne veya Ikev2 'ye geçiş](../vpn-gateway/ikev2-openvpn-from-sstp.md
).

Azure 'da dağıtılan kaynaklarınıza erişmek için uzak geliştiriciler, erişilen VM 'lerde genel IP 'Ler gerekmeden güvenli kabuk erişimi (RDP veya SSH) almak üzere VPN bağlantısı yerine Azure savunma çözümünü kullanabilir. Daha fazla bilgi için bkz. [Azure savunma kullanarak uzaktan çalışma](../bastion/work-remotely-support.md).

Büyük ölçekli VPN bağlantısını toplayarak, farklı şirket içi küresel konumlarda, farklı bölgesel hub ve bağlı bileşen sanal ağlarında kaynaklar arasındaki herhangi bir bağlantıyı desteklemek ve birden çok ev geniş bant ağının kullanımını iyileştirmek için Azure sanal WAN kullanabilirsiniz. Daha fazla bilgi için bkz. [Ev gereksinimlerden işe yaramaz mi? Azure sanal WAN ' ın yardım edebilir](../virtual-wan/work-remotely-support.md).

Uzak bir iş gücünü desteklemeye yönelik başka bir yol da, Azure sanal ağınızda barındırılan bir sanal masaüstü altyapısı (VDı) dağıtmaktır ve bu sayede Azure güvenlik duvarıyla korunmuş olur. Örneğin, Windows sanal masaüstü (WVD), Azure 'da çalışan bir masaüstü ve uygulama sanallaştırma hizmetidir. Windows sanal masaüstü sayesinde, ek ağ geçidi sunucuları çalıştırmaya gerek kalmadan Azure aboneliğinizde ölçeklenebilir ve esnek bir ortam ayarlayabilirsiniz. Yalnızca sanal ağınızdaki WVD sanal makinelerle sorumludur. Daha fazla bilgi için bkz. [Azure Güvenlik Duvarı Uzaktan çalışma desteği](../firewall/remote-work-support.md). 

Azure 'da ayrıca zengin bir ekonomik sistem iş ortakları kümesi de vardır. Azure 'daki iş ortakları ağ sanal gereçleri, VPN bağlantısının ölçeklendirilmesine de yardımcı olabilir. Daha fazla bilgi için bkz. [Uzaktan çalışma Için ağ sanal gereci (NVA) konuları](../vpn-gateway/nva-work-remotely-support.md).

## <a name="extend-employees-connection-to-access-globally-distributed-resources"></a>Çalışanların bağlantısını küresel olarak dağıtılan kaynaklara erişecek şekilde uzat

Aşağıdaki Azure Hizmetleri, çalışanların küresel olarak dağıtılan kaynaklarınıza erişmesini sağlamaya yardımcı olabilir. Kaynaklarınız Azure bölgelerinde, şirket içi ağlarda veya hatta diğer genel veya özel bulutlarda olabilir. 

- **Azure sanal ağ eşlemesi**: kaynaklarınızı birden fazla Azure bölgesinde dağıtıp/veya birden fazla sanal ağ kullanarak uzaktan çalışan çalışanların bağlantısını topladıysanız, sanal ağ eşlemesi kullanarak birden çok Azure sanal ağı arasında bağlantı kurabilirsiniz. Daha fazla bilgi için bkz. [sanal ağ eşlemesi][VNet-peer].

- **Azure VPN tabanlı çözüm**: Azure 'a P2S veya S2S VPN aracılığıyla bağlanan uzak çalışanlarınız için, şirket içi ağlarınız ve Azure VPN ağ geçidiniz arasında S2S VPN 'yi yapılandırarak şirket içi ağlara erişimi etkinleştirebilirsiniz. Daha fazla bilgi için bkz. [siteden siteye bağlantı oluşturma][S2S].

- **ExpressRoute**: ExpressRoute özel eşlemesini kullanarak Azure dağıtımlarınız ile şirket içi altyapınız veya bir ortak konum tesisinde altyapınızın arasında özel bağlantı sağlayabilirsiniz. ExpressRoute, Microsoft eşlemesi aracılığıyla Microsoft içindeki genel uç noktalara, şirket içi ağınızdan de erişilmesine izin verir. ExpressRoute bağlantıları ortak İnternet üzerinden geçmemektedir. Internet üzerinden tipik bağlantılardan daha düşük ve tutarlı gecikme süreleriyle güvenli bağlantı, güvenilirlik, daha yüksek performans sağlar. Daha fazla bilgi için bkz. [ExpressRoute'a genel bakış][ExR]. [ExpressRoute iş ortağımız ekosistemimizin][ExR-eco] zaten bir parçası olan mevcut ağ sağlayıcınızı kullanmak, Microsoft 'a büyük bant genişliği bağlantıları almanın süresini azaltmaya yardımcı olabilir.  [ExpressRoute Direct][ExR-D] kullanarak şirket içi ağınızı doğrudan Microsoft omurgasına bağlayabilirsiniz. ExpressRoute Direct, iki 10 Gbps veya 100 Gbps için iki farklı satır hızı seçeneği sunar. 

- **Azure sanal WAN**: Azure sanal WAN, VPN bağlantılarınız Ile ExpressRoute devreleri arasında kesintisiz birlikte çalışabilirlik sağlar. Daha önce belirtildiği gibi, Azure sanal WAN, farklı bölgesel hub ve bağlı bileşen sanal ağlarında farklı şirket içi küresel konumlarda bulunan kaynaklar arasındaki herhangi bir bağlantıyı da destekler

## <a name="scale-customer-connectivity-to-frontend-resources"></a>Ön uç kaynaklarıyla müşterinin bağlantısını ölçeklendirin

Daha fazla kişinin çevrimiçi olduğu durumlarda, birçok kurumsal web sitesi müşteri trafiğinden daha fazla deneyim yaşar. Azure Application Gateway, bu Artılmış ön uç iş yükünü yönetmeye yardımcı olabilir. Daha fazla bilgi için bkz. [Application Gateway yüksek trafik desteği](../application-gateway/high-traffic-support.md).

## <a name="microsoft-support-for-multi-cloud-traffic"></a>Çoklu bulut trafiği için Microsoft desteği

Diğer genel bulutlardaki dağıtımlarınız için, Microsoft genel bağlantı sağlayabilir. Azure sanal WAN, VPN veya ExpressRoute bu konuda yardımcı olabilir. Azure 'dan diğer bulutlara bağlantıyı genişletmek için, iki bulut arasında S2S VPN 'yi yapılandırabilirsiniz. Ayrıca ExpressRoute kullanarak Azure 'dan diğer genel bulutlara bağlantı kurabilirsiniz. Oracle bulutu, ExpressRoute iş ortağı ekosisteminin bir parçasıdır. [Azure Ile Oracle bulut altyapısı arasında doğrudan bir iç][Az-OCI]bağlantı kurabilirsiniz. ExpressRoute iş ortağı ekosisteminin bir parçası olan hizmet sağlayıcılarının çoğu ayrıca diğer genel bulutlara özel bağlantı sağlar. Bu hizmet sağlayıcılarından yararlanarak ExpressRoute aracılığıyla Azure ve diğer bulutlarınızla dağıtımlarınız arasında özel bağlantı kurabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Aşağıdaki makalelerde, farklı Azure ağ özelliklerinin, kullanıcıları uzaktan çalışacak şekilde ölçeklendirmek için nasıl kullanılabileceği açıklanmaktadır:

| **Makale** | **Açıklama** |
| --- | --- |
| [Kullanıcıların uzaktan çalışmasını sağlama](../vpn-gateway/work-remotely-support.md) | Kullanıcılara uzaktan erişim ayarlamak veya mevcut çözümlerini kuruluşunuz için ek kapasiteye göre tamamlamak için kullanılabilir seçenekleri gözden geçirin.|
| [Ev gereksinimlerden işe yaramaz mi? Burada Azure sanal WAN ' ın yardım edebilir](../virtual-wan/work-remotely-support.md) | Kuruluşunuzun uzak bağlantı ihtiyaçlarını karşılamak için Azure sanal WAN 'ı kullanın.|
| [Application Gateway yüksek trafik desteği](../application-gateway/high-traffic-support.md) | Web uygulamalarınıza yönelik trafiği yönetmenin ölçeklenebilir ve güvenli bir yolu için Web uygulaması güvenlik duvarı (WAF) ile Application Gateway kullanın. |
| [Uzaktan çalışma için ağ sanal gereci (NVA) konuları](../vpn-gateway/nva-work-remotely-support.md)|Uzaktan erişim çözümleri sağlamak için Azure 'da NVA 'lar kullanma hakkındaki kılavuzu inceleyin. |
| [SSTP 'den OpenVPN protokolüne veya Ikev2 'e geçiş](../vpn-gateway/ikev2-openvpn-from-sstp.md) | OpenVPN protokolüne veya Ikev2 'e geçiş yaparak SSTP 'nin 128 eşzamanlı bağlantı sınırı aşılır.|
| [Azure savunma kullanarak uzaktan çalışma](../bastion/work-remotely-support.md) | Genel bir IP adresi kullanmadan doğrudan Azure portal Azure sanal ağı içindeki sanal makinelere güvenli ve sorunsuz RDP/SSH bağlantısı sağlayın. |
| [Uzak kullanıcıları desteklemek üzere karma bağlantı oluşturmak için Azure ExpressRoute kullanma](../expressroute/work-remotely-support.md) | Kuruluşunuzdaki kullanıcıların uzaktan çalışmasını sağlamak için karma bağlantı için ExpressRoute kullanın.|
| [Azure Güvenlik Duvarı Uzaktan çalışma desteği](../firewall/remote-work-support.md)|Azure Güvenlik Duvarı 'nı kullanarak Azure sanal ağ kaynaklarınızı koruyun. |

<!--Link References-->
[VNet-peer]: ../virtual-network/virtual-network-peering-overview.md
[S2S]: ../vpn-gateway/tutorial-site-to-site-portal.md
[ExR]: ../expressroute/expressroute-introduction.md
[ExR-eco]: ../expressroute/expressroute-locations.md
[ExR-D]: ../expressroute/expressroute-erdirect-about.md
[Az-OCI]: ../virtual-machines/workloads/oracle/configure-azure-oci-networking.md