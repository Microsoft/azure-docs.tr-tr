---
title: Azure 'da ağ güvenlik kavramları ve gereksinimleri | Microsoft Docs
description: Bu makalede, çekirdek ağ güvenlik kavramları ve gereksinimleri hakkında temel açıklamalar ve bu alanların her birinde Azure 'un sunduğu bilgiler sağlanmaktadır.
services: security
documentationcenter: na
author: TomShinder
manager: barbkess
editor: TomSh
ms.assetid: bedf411a-0781-47b9-9742-d524cf3dbfc1
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/29/2018
ms.author: terrylan
ms.openlocfilehash: 746994585dfa968a8d8d982908ad424b08c06066
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68726911"
---
# <a name="azure-network-security-overview"></a>Azure ağ güvenliğine genel bakış

Ağ güvenliği, ağ trafiğine denetimler uygulanarak kaynakları yetkisiz erişime veya saldırılara karşı koruma süreci olarak tanımlanabilir. Amaç yalnızca meşru trafiğe izin verildiğinden emin olmaktır. Azure, uygulamanızı ve hizmet bağlantı gereksinimlerinizi desteklemek için sağlam bir ağ altyapısı içerir. Azure 'da bulunan kaynaklar arasında, şirket içi ve Azure 'da barındırılan kaynaklar arasında ve internet ve Azure arasında ağ bağlantısı kurulabilir.

Bu makalede, Azure 'un ağ güvenliği alanında sunduğu bazı seçenekler ele alınmaktadır. Hakkında bilgi edinebilirsiniz:

* Azure ağı
* Ağ erişim denetimi
* Azure Güvenlik Duvarı
* Güvenli uzaktan erişim ve şirketler arası bağlantı
* Kullanılabilirlik
* Ad çözümlemesi
* Çevre ağı (DMZ) mimarisi
* Azure DDoS koruması
* Azure ön kapısı
* Traffic manager
* İzleme ve tehdit algılama

## <a name="azure-networking"></a>Azure ağı

Azure, sanal makinelerin bir Azure sanal ağına bağlanmasını gerektirir. Sanal ağ, fiziksel Azure ağ dokusunun üzerine oluşturulan mantıksal bir yapıdır. Her sanal ağ, diğer tüm sanal ağlardan yalıtılmıştır. Bu, dağıtımlarınızdaki ağ trafiğine diğer Azure müşterilerinin erişimine açık olmamasını sağlamaya yardımcı olur.

Daha fazla bilgi:

* [Sanal ağa genel bakış](../../virtual-network/virtual-networks-overview.md)

## <a name="network-access-control"></a>Ağ erişim denetimi

Ağ erişim denetimi, bir sanal ağ içindeki belirli cihazlarla veya alt ağlardan gelen bağlantıları sınırlama işlemidir. Ağ erişim denetimi hedefi, sanal makinelerinize ve hizmetlerinize erişimi onaylanan kullanıcılar ve cihazlarla sınırlandırmaktır. Erişim denetimleri, sanal makinenize veya hizmetinize bağlantı verme veya reddetme kararlarını temel alır.

Azure, çeşitli ağ erişim denetimi türlerini destekler, örneğin:

* Ağ katmanı denetimi
* Yönlendirme denetimi ve Zorlamalı tünel
* Sanal ağ güvenlik gereçleri

### <a name="network-layer-control"></a>Ağ katmanı denetimi

Herhangi bir güvenli dağıtım için bazı ağ erişim denetimi ölçüsü gerekir. Ağ erişim denetimi hedefi, sanal makine iletişimini gerekli sistemlerle kısıtlamadır. Diğer iletişim denemeleri engellenir.

> [!NOTE]
> Depolama güvenlik duvarları, [Azure depolama güvenliğine genel bakış](storage-overview.md) makalesinde ele alınmıştır

#### <a name="network-security-rules-nsgs"></a>Ağ güvenlik kuralları (NSG 'ler)

Temel ağ düzeyi erişim denetimine (IP adresi ve TCP veya UDP protokollerine göre) ihtiyacınız varsa ağ güvenlik gruplarını (NSG 'ler) kullanabilirsiniz. NSG, temel, durum bilgisi olan, paket filtreleme güvenlik duvarıdır ve [5 demet](https://www.techopedia.com/definition/28190/5-tuple)temelinde erişimi denetlemenize olanak sağlar. NSG 'ler yönetimi basitleştirecek ve yapılandırma hataları olasılığını azaltmaya yönelik işlevsellik içerir:

* **Genişletilmiş güvenlik kuralları** NSG kural tanımını basitleştirir ve aynı sonuca ulaşmak için birden çok basit kural oluşturmak yerine karmaşık kurallar oluşturmanıza olanak sağlar.
* **Hizmet etiketleri** , bir IP adresi grubunu temsil eden Microsoft tarafından oluşturulan etiketlerdir. Bunlar, etikete dahil edilen koşullara uyan IP aralıklarını içerecek şekilde dinamik olarak güncelleştirirler. Örneğin, Doğu bölgesindeki tüm Azure depolama için geçerli olan bir kural oluşturmak istiyorsanız Storage. EastUS kullanabilirsiniz
* **Uygulama güvenlik grupları** , uygulama gruplarına kaynakları dağıtmanıza ve bu uygulama gruplarını kullanan kurallar oluşturarak bu kaynaklara erişimi denetlemenize olanak tanır. Örneğin, ' webservers ' uygulama grubuna dağıtılmış web sunucularınız varsa, Internet 'ten gelen 443 trafiğe ' webservers ' uygulama grubundaki tüm sistemlere izin veren bir NSG uygulayan bir kural oluşturabilirsiniz.

NSG 'ler uygulama katmanı denetimi veya kimliği doğrulanmış erişim denetimleri sağlamaz.

Daha fazla bilgi:

* [Ağ güvenlik grupları](../../virtual-network/security-overview.md)

#### <a name="asc-just-in-time-vm-access"></a>ASC tam zamanında VM erişimi

[Azure Güvenlik Merkezi](../../security-center/security-center-intro.md) VM 'Lerde NSG 'leri yönetebilir ve uygun rol tabanlı erişim denetimi [RBAC](/azure/role-based-access-control/overview) izinleri istediğinde, VM 'ye erişimi kilitler. Kullanıcı başarıyla yetkilendirildiğinde, belirtilen süre boyunca seçilen bağlantı noktalarına erişime izin vermek için NSG 'lerdeki değişiklikler yapar. Süre sona erdiğinde NSG 'ler önceki güvenli durumlarına geri yüklenir.

Daha fazla bilgi:

* [Azure Güvenlik Merkezi tam zamanında erişim](../../security-center/security-center-just-in-time.md)

#### <a name="service-endpoints"></a>Hizmet uç noktaları

Hizmet uç noktaları, trafiğiniz üzerinde denetim uygulamak için başka bir yoldur. Desteklenen hizmetlerle iletişimi doğrudan bir bağlantı üzerinden yalnızca sanal ağlarınız ile sınırlayabilirsiniz. Sanal ağınızdan belirtilen Azure hizmetine giden trafik Microsoft Azure omurga ağında kalır.  

Daha fazla bilgi:

* [Hizmet uç noktaları](../../virtual-network/virtual-network-service-endpoints-overview.md#securing-azure-services-to-virtual-networks)

### <a name="route-control-and-forced-tunneling"></a>Yönlendirme denetimi ve Zorlamalı tünel

Sanal ağlarınızdaki yönlendirme davranışını denetleme özelliği kritik öneme sahiptir. Yönlendirme yanlış yapılandırılmışsa, sanal makinenizde barındırılan uygulamalar ve hizmetler, olası saldırganlar tarafından sahip olunan ve çalıştırılan sistemler dahil olmak üzere yetkisiz cihazlara bağlanabilir.

Azure ağ iletişimi, sanal ağlarınızdaki ağ trafiği için yönlendirme davranışını özelleştirme yeteneğini destekler. Bu, sanal ağınızdaki varsayılan yönlendirme tablosu girişlerini değiştirmenizi sağlar. Yönlendirme davranışının denetimi, belirli bir cihazdan veya cihaz grubundan gelen tüm trafiğin Sanal ağınızı belirli bir konum üzerinden girdiği veya bırakdıklarından emin olmanıza yardımcı olur.

Örneğin, sanal ağınızda sanal ağ güvenlik gereci olabilir. Sanal ağınızdan gelen ve giden tüm trafiğin bu sanal güvenlik gerecinden geçerek olduğundan emin olmak istiyorsunuz. Bunu, Azure 'da [Kullanıcı tanımlı yolları](../../virtual-network/virtual-networks-udr-overview.md) (udrs) yapılandırarak yapabilirsiniz.

[Zorlamalı tünel oluşturma](https://www.petri.com/azure-forced-tunneling) , hizmetlerinizin Internet 'teki cihazlara bağlantı başlatmasına izin verilmediğinden emin olmak için kullanabileceğiniz bir mekanizmadır. Bunun, gelen bağlantıları kabul etmesinin ve daha sonra onlara yanıt verdiğinden farklı olduğunu unutmayın. Ön uç Web sunucularının Internet konaklarındaki isteklere yanıt vermesi gerekir ve bu nedenle, internet kaynaklı trafiğe bu Web sunucularına gelen ve Web sunucularının yanıt vermesine izin verilir.

İzin vermek istemediğiniz Özellikler bir ön uç Web sunucusudur ve bir giden istek başlatabilir. Bu bağlantılar kötü amaçlı yazılım indirmek için kullanılabilir olduğundan, bu tür istekler bir güvenlik riskini temsil edebilir. Bu ön uç sunucularının internet 'e giden istekleri başlatmasını istiyor olsanız bile, şirket içi Web proxy 'leriniz arasında çalışmaya zorlamak isteyebilirsiniz. Bu, URL filtrelemeden ve günlüğün avantajlarından yararlanmanızı sağlar.

Bunun yerine, bunu engellemek için Zorlamalı tünel kullanmak istersiniz. Zorlamalı tüneli etkinleştirdiğinizde, internet 'e yönelik tüm bağlantılar şirket içi ağ geçidiniz aracılığıyla zorlanır. UDRs 'nin avantajlarından yararlanarak Zorlamalı tünel oluşturma yapılandırabilirsiniz.

Daha fazla bilgi:

* [Kullanıcı tanımlı yollar ve IP Iletimi nedir?](../../virtual-network/virtual-networks-udr-overview.md)

### <a name="virtual-network-security-appliances"></a>Sanal ağ güvenlik gereçleri

NSG 'ler, UDRs ve Zorlamalı tünel, [OSI modelinin](https://en.wikipedia.org/wiki/OSI_model)ağ ve aktarım katmanlarındaki güvenlik düzeyini sağlar. Ayrıca, ağınızı ağdan daha yüksek düzeylerde etkinleştirmek isteyebilirsiniz.

Örneğin, güvenlik gereksinimleriniz şunları içerebilir:

* Uygulamanıza erişim izni vermeden önce kimlik doğrulaması ve yetkilendirme
* Yetkisiz giriş algılama ve yetkisiz giriş yanıtı
* Üst düzey protokoller için uygulama katmanı incelemesi
* URL filtreleme
* Ağ düzeyinde virüsten koruma ve kötü amaçlı yazılımdan koruma
* Bot koruması
* Uygulama erişim denetimi
* Ek DDoS koruması (Azure Fabric tarafından sunulan DDoS korumasının üzerinde)

Azure iş ortağı çözümünü kullanarak bu gelişmiş ağ güvenliği özelliklerine erişebilirsiniz. [Azure Marketi](https://azure.microsoft.com/marketplace/)' ni ziyaret ederek ve "güvenlik" ve "ağ güvenliği" için arama yaparak en güncel Azure iş ortağı ağ güvenlik çözümlerini bulabilirsiniz.

## <a name="azure-firewall"></a>Azure Güvenlik Duvarı

Azure Güvenlik Duvarı, Azure Sanal Ağ kaynaklarınızı koruyan yönetilen, bulut tabanlı bir güvenlik hizmetidir. Yerleşik yüksek kullanılabilirlik oranı ve kısıtlamasız bulut ölçeklenebilirliğiyle hizmet olarak tam durum bilgisi olan bir güvenlik duvarıdır. Bazı özellikler şunlardır:

* Yüksek kullanılabilirlik
* Bulut ölçeklenebilirliği
* Uygulama FQDN filtreleme kuralları
* Ağ trafiği filtreleme kuralları

Daha fazla bilgi:

* [Azure Güvenlik Duvarı 'na genel bakış](/azure/firewall/overview)

## <a name="secure-remote-access-and-cross-premises-connectivity"></a>Güvenli uzaktan erişim ve şirketler arası bağlantı

Azure kaynaklarınızın kurulum, yapılandırma ve yönetiminin uzaktan yapılması gerekir. Ayrıca, şirket içinde ve Azure genel bulutunda bileşenlere sahip [karma BT](https://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx) çözümlerini dağıtmak isteyebilirsiniz. Bu senaryolar güvenli uzaktan erişim gerektirir.

Azure ağ, aşağıdaki güvenli uzaktan erişim senaryolarını destekler:

* Bireysel iş istasyonlarını bir sanal ağa bağlama
* Şirket içi ağınızı VPN ile bir sanal ağa bağlama
* Şirket içi ağınızı adanmış WAN bağlantısı olan bir sanal ağa bağlama
* Sanal ağları birbirine bağlama

### <a name="connect-individual-workstations-to-a-virtual-network"></a>Bireysel iş istasyonlarını bir sanal ağa bağlama

Azure 'daki sanal makineleri ve hizmetleri yönetmek için bireysel geliştiricilere veya operasyon personeline izin vermek isteyebilirsiniz. Örneğin, bir sanal ağ üzerindeki bir sanal makineye erişmeniz gerektiğini varsayalım. Ancak güvenlik ilkeniz, tek tek sanal makinelere RDP veya SSH uzaktan erişimine izin vermez. Bu durumda, [noktadan sıteye VPN](../../vpn-gateway/point-to-site-about.md) bağlantısı kullanabilirsiniz.

Noktadan siteye VPN bağlantısı, Kullanıcı ve sanal ağ arasında özel ve güvenli bir bağlantı ayarlamanıza olanak sağlar. VPN bağlantısı oluşturulduğunda, Kullanıcı sanal ağdaki herhangi bir sanal makineye VPN bağlantısı üzerinden RDP veya SSH gönderebilir. (Bu, kullanıcının kimlik doğrulayabilecek ve yetkilendirildiği varsayılmaktadır.) Noktadan siteye VPN şunları destekler:

* Özel bir SSL tabanlı VPN protokolü olan Güvenli Yuva Tünel Protokolü (SSTP). Çoğu güvenlik duvarı SSL tarafından kullanılan TCP bağlantı noktası 443 ' ü kullandığından, bir SSL VPN çözümü, güvenlik duvarlarını sıztı. SSTP yalnızca Windows cihazlarında desteklenir. Azure, SSTP (Windows 7 ve üzeri) olan tüm Windows sürümlerini destekler.

* IKEv2 VPN, standart tabanlı bir IPsec VPN çözümüdür. IKEv2 VPN, Mac cihazlardan (OSX sürüm 10.11 ve üzeri) bağlantı kurmak için kullanılabilir.

* [OpenVPN](https://azure.microsoft.com/updates/openvpn-support-for-azure-vpn-gateways/)

Daha fazla bilgi:

* [PowerShell kullanarak bir sanal ağa Noktadan siteye bağlantı yapılandırma](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="connect-your-on-premises-network-to-a-virtual-network-with-a-vpn"></a>Şirket içi ağınızı VPN ile bir sanal ağa bağlama

Tüm Şirket ağınızı veya bunun bölümlerini bir sanal ağa bağlamak isteyebilirsiniz. Bu, kuruluşların [Şirket içi veri merkezini Azure 'a genişlettiğinde](https://gallery.technet.microsoft.com/Datacenter-extension-687b1d84)karma BT senaryolarında yaygındır. Birçok durumda, kuruluşlar Azure 'da bir hizmetin parçalarını ve şirket içi parçaları barındırır. Örneğin, bir çözüm Azure 'da ön uç Web sunucuları ve şirket içi veritabanları içerdiğinde bu durum oluşabilir. Bu "şirketler arası" bağlantılar, Azure 'da bulunan kaynakların yönetimini daha güvende hale getirir ve Active Directory etki alanı denetleyicilerini Azure 'a genişletme gibi senaryoları etkinleştirir.

Bunu gerçekleştirmenin bir yolu, [siteden sıteye VPN](https://www.techopedia.com/definition/30747/site-to-site-vpn)kullanmaktır. Siteden siteye VPN ve Noktadan siteye VPN arasındaki fark, ikinci bir sanal ağa tek bir cihazı bağlamanızdır. Siteden siteye VPN, bir ağın tamamını (Şirket içi ağınız gibi) bir sanal ağa bağlar. Bir sanal ağ için siteden siteye VPN 'Ler, yüksek oranda güvenli IPSec tünel modu VPN protokolünü kullanır.

Daha fazla bilgi:

* [Azure portal kullanarak siteden siteye VPN bağlantısı ile Kaynak Yöneticisi VNet oluşturma](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [VPN Gateway hakkında](../../vpn-gateway/vpn-gateway-about-vpngateways.md)

### <a name="connect-your-on-premises-network-to-a-virtual-network-with-a-dedicated-wan-link"></a>Şirket içi ağınızı adanmış WAN bağlantısı olan bir sanal ağa bağlama

Noktadan siteye ve siteden siteye VPN bağlantıları, şirketler arası bağlantıları etkinleştirmek için geçerlidir. Bununla birlikte, bazı kuruluşlar bunları aşağıdaki dezavantajlara sahip olacak şekilde ele alalım:

* VPN bağlantıları verileri internet üzerinden taşır. Bu, verileri ortak bir ağ üzerinden taşımayla ilgili olası güvenlik sorunlarına yönelik bu bağlantıları gösterir. Ayrıca, internet bağlantıları için güvenilirlik ve kullanılabilirlik garanti edilemez.
* Sanal ağlara VPN bağlantıları, 200 Mbps 'Lik bir süre içinde en fazla yer aldığı için bazı uygulamalar ve amaçlar için bant genişliğine sahip olmayabilir.

Şirket içi bağlantıları için en yüksek düzeyde güvenlik ve kullanılabilirlik gerektiren kuruluşlar genellikle uzak sitelere bağlanmak için adanmış WAN bağlantıları kullanır. Azure, şirket içi ağınızı bir sanal ağa bağlamak için kullanabileceğiniz adanmış bir WAN bağlantısı kullanma olanağı sunar. Azure ExpressRoute, hızlı rota doğrudan ve hızlı rota genel erişim bu ayarı etkinleştirir.

Daha fazla bilgi:

* [ExpressRoute teknik genel bakış](../../expressroute/expressroute-introduction.md)
* [ExpressRoute Direct](../../expressroute/expressroute-erdirect-about.md)
* [Express Route küresel erişim](../../expressroute/expressroute-global-reach.md)

### <a name="connect-virtual-networks-to-each-other"></a>Sanal ağları birbirine bağlama

Dağıtımlarınız için birçok sanal ağ kullanmak mümkündür. Bunu yapmanız gerekebilecek çeşitli nedenler vardır. Yönetimi basitleştirmek isteyebilirsiniz veya daha fazla güvenlik sağlayabilirsiniz. Farklı sanal ağlara kaynak koyma konusunda ne olursa olsun, ağların her birindeki kaynakların birbirleriyle bağlanmasını istediğiniz zaman olabilir.

Bir seçenek, bir sanal ağ üzerindeki hizmetlere, başka bir sanal ağ üzerinde bulunan hizmetlere bağlanmak için, internet üzerinden "geri döngü" yaparak bir seçenektir. Bağlantı bir sanal ağda başlatılır, internet üzerinden geçer ve ardından hedef sanal ağa geri gelir. Bu seçenek, herhangi bir internet tabanlı iletişimde bulunan güvenlik sorunlarıyla bağlantı sunar.

İki sanal ağ arasında bağlanan bir siteden siteye VPN oluşturmak daha iyi bir seçenek olabilir. Bu yöntem, yukarıda bahsedilen şirket içi siteden siteye VPN bağlantısıyla aynı [IPSec tünel modu](https://technet.microsoft.com/library/cc786385.aspx) protokolünü kullanır.

Bu yaklaşımın avantajı, VPN bağlantısının Internet üzerinden bağlanmak yerine Azure ağ dokusunda kurulduğu bir avantajdır. Bu, internet üzerinden bağlanan siteden siteye VPN 'lerle karşılaştırıldığında ek bir güvenlik katmanı sağlar.

Daha fazla bilgi:

* [Azure Resource Manager ve PowerShell kullanarak VNet-VNet bağlantısı yapılandırma](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

Sanal ağlarınızı bağlamak için başka bir yöntem de [VNET eşlemedir](../../virtual-network/virtual-network-peering-overview.md). Bu özellik, iki Azure ağını, aralarında iletişim kurmak zorunda kalmadan Microsoft omurga altyapısı üzerinden meydana gelecek şekilde bağlamanıza olanak tanır. VNET eşlemesi, Azure bölgelerinde aynı bölgedeki iki sanal ağı veya iki sanal ağı birbirine bağlayabilirler. NSG 'ler, farklı alt ağlar veya sistemler arasındaki bağlantıyı sınırlamak için kullanılabilir.

## <a name="availability"></a>Kullanılabilirlik

Kullanılabilirlik, herhangi bir güvenlik programının önemli bir bileşenidir. Kullanıcılarınız ve sistemleriniz ağ üzerinden erişmek için gereken değerlere erişegerekmiyorsa, hizmet tehlikede düşünülebilir. Azure, aşağıdaki yüksek kullanılabilirlik mekanizmalarını destekleyen ağ teknolojilerine sahiptir:

* HTTP tabanlı yük dengeleme
* Ağ düzeyinde yük dengeleme
* Küresel Yük Dengeleme

Yük Dengeleme, birden çok cihaz arasında bağlantıları eşit bir şekilde dağıtmak için tasarlanan bir mekanizmadır. Yük Dengeleme amaçları şunlardır:

* Kullanılabilirliği artırmak için. Birden çok cihazda bağlantıları dengelemek istediğinizde, bir veya daha fazla cihaz hizmetten ödün vermeden kullanılamaz hale gelebilir. Kalan çevrimiçi cihazlarda çalışan hizmetler, hizmetten içeriğe hizmet vermeye devam edebilir.
* Performansını artırmak için. Birden çok cihazda bağlantıları dengelemek istediğinizde, tek bir cihazın tüm işlemleri işlemesi gerekmez. Bunun yerine, içeriğe hizmet vermeye yönelik işleme ve bellek talepleri birden çok cihaza yayılır.

### <a name="http-based-load-balancing"></a>HTTP tabanlı yük dengeleme

Web tabanlı hizmetler çalıştıran kuruluşlar, genellikle bu Web hizmetlerinin önünde HTTP tabanlı yük dengeleyici olmasını ister. Bu, yeterli performans ve yüksek kullanılabilirlik düzeyinin sağlanmasına yardımcı olur. Geleneksel, ağ tabanlı yük dengeleyiciler ağ ve Aktarım katmanı protokollerini kullanır. HTTP tabanlı yük dengeleyiciler, diğer yandan, HTTP protokolünün özelliklerine göre kararlar alır.

Azure Application Gateway, Web tabanlı hizmetleriniz için HTTP tabanlı yük dengelemesi sağlar. Application Gateway şunları destekler:

* Tanımlama bilgisi tabanlı oturum benzeşimi. Bu özellik, söz konusu yük dengeleyicinin arkasındaki sunuculardan birine kurulan bağlantıların, istemci ve sunucu arasında bozulmadan kalmasını sağlar. Bu işlem kararlılığı sağlar.
* SSL boşaltması. İstemci yük dengeleyiciye bağlandığında, bu oturum HTTPS (SSL) protokolü kullanılarak şifrelenir. Bununla birlikte, performansı artırmak için HTTP (şifrelenmemiş) protokolünü kullanarak yük dengeleyicinin arkasındaki Web sunucusu arasında bağlantı yapabilirsiniz. Bu, yük dengeleyicinin arkasındaki Web sunucuları şifrelemeyle ilgili işlemci ek yüküne neden olmadığı için "SSL yük boşaltma" olarak adlandırılır. Web sunucuları, bu nedenle istekleri daha hızlı bir şekilde hizmet edebilir.
* URL tabanlı içerik yönlendirme. Bu özellik, yük dengeleyicinin hedef URL 'ye göre bağlantıların nereye iletileceği hakkında kararlar almasına olanak tanır. Bu, IP adreslerine göre yük dengeleme kararları veren çözümlerden çok daha fazla esneklik sağlar.

Daha fazla bilgi:

* [Application Gateway genel bakış](/azure/application-gateway/application-gateway-introduction)

### <a name="network-level-load-balancing"></a>Ağ düzeyinde yük dengeleme

HTTP tabanlı yük dengelemenin aksine, ağ düzeyinde yük dengeleme, IP adresi ve bağlantı noktası (TCP veya UDP) numaralarına göre kararlar verir.
Azure Load Balancer kullanarak Azure 'da ağ düzeyinde yük dengelemenin avantajlarını elde edebilirsiniz. Load Balancer 'nin bazı önemli özellikleri şunlardır:

* IP adresini ve bağlantı noktası numaralarını temel alan ağ düzeyinde yük dengeleme.
* Herhangi bir uygulama katmanı protokolü için destek.
* Azure sanal makinelerine ve bulut Hizmetleri rol örneklerine yönelik bakiyeleri yükleyin.
* İnternet 'e yönelik (dış yük dengeleme) ve internet 'e yönelik olmayan (İç Yük Dengeleme) uygulamalar ve sanal makineler için kullanılabilir.
* Yük dengeleyicinin arkasındaki hizmetlerden herhangi birinin kullanılamaz hale geldiğini belirlemede kullanılan uç nokta izleme.

Daha fazla bilgi:

* [Birden çok sanal makine veya hizmet arasında Internet 'e yönelik yük dengeleyici](/azure/load-balancer/load-balancer-internet-overview)
* [İç Yük Dengeleyiciye genel bakış](/azure/load-balancer/load-balancer-internal-overview)

### <a name="global-load-balancing"></a>Küresel Yük Dengeleme

Bazı kuruluşlar mümkün olan en yüksek düzeyde kullanılabilirliği ister. Bu hedefe ulaşmak için bir yol, uygulamaları küresel olarak dağıtılan veri merkezlerinde barındırmaktır. Bir uygulama, dünyanın tamamında bulunan veri merkezlerinde barındırılıyorsa, tüm coğrafi bölge bölgesinin kullanılamaz hale gelmesi ve uygulamanın çalışır durumda olması mümkündür.

Bu yük dengeleme stratejisi, performans avantajlarını da sağlayabilir. Hizmeti isteklerini isteği yapan cihaza en yakın veri merkezine yönlendirebilirsiniz.

Azure 'da Azure Traffic Manager kullanarak küresel yük dengelemenin avantajlarını elde edebilirsiniz.

Daha fazla bilgi:

* [Traffic Manager nedir?](../../traffic-manager/traffic-manager-overview.md)

## <a name="name-resolution"></a>Ad çözümlemesi

Ad çözümlemesi, Azure 'da ana bilgisayar oluşturduğunuz tüm hizmetler için kritik bir işlevdir. Bir güvenlik açısından, ad çözümleme işlevinin güvenliği, sitelerinizden bir saldırganın sitesine istekleri yeniden yönlendirmesine olanak tanıyabilir. Güvenli ad çözümlemesi, tüm bulut barındırılan hizmetlerinizin bir gereksinimidir.

Bilmeniz gereken iki tür ad çözümlemesi vardır:

* İç ad çözümlemesi. Bu, sanal ağlarınızdaki hizmetler, şirket içi ağlarınız veya her ikisi tarafından kullanılır. İç ad çözümlemesi için kullanılan adlara internet üzerinden erişilemiyor. En iyi güvenlik için, iç ad çözümleme şemanızın dış kullanıcılar tarafından erişilemez olması önemlidir.
* Dış ad çözümlemesi. Bu, şirket içi ağlarınız ve sanal ağlarınız dışındaki kişiler ve cihazlar tarafından kullanılır. Bunlar Internet 'e görünür olan adlardır ve bulut tabanlı hizmetlerinize doğrudan bağlantı sağlamak için kullanılır.

İç ad çözümlemesi için iki seçeneğiniz vardır:

* Bir sanal ağ DNS sunucusu. Yeni bir sanal ağ oluşturduğunuzda, sizin için bir DNS sunucusu oluşturulur. Bu DNS sunucusu, bu sanal ağ üzerinde bulunan makinelerin adlarını çözümleyebilir. Bu DNS sunucusu yapılandırılamaz, Azure Fabric Manager tarafından yönetilir ve bu nedenle ad çözümleme çözümünüzü güvenli hale getirmenize yardımcı olabilir.
* Kendi DNS sunucunuzu getirin. Sanal ağınıza kendi seçtiğiniz bir DNS sunucusunu koyma seçeneğiniz vardır. Bu DNS sunucusu, Azure Marketi 'nden edinebileceğiniz bir Active Directory tümleşik DNS sunucusu veya bir Azure iş ortağı tarafından sunulan adanmış bir DNS sunucusu çözümüdür.

Daha fazla bilgi:

* [Sanal ağa genel bakış](../../virtual-network/virtual-networks-overview.md)
* [Bir sanal ağ tarafından kullanılan DNS sunucularını yönetme](../../virtual-network/manage-virtual-network.md#change-dns-servers)

Dış ad çözümlemesi için iki seçeneğiniz vardır:

* Şirket içinde kendi dış DNS sunucunuzu barındırın.
* Kendi dış DNS sunucunuzu bir hizmet sağlayıcısıyla barındırın.

Birçok büyük kuruluş, kendi DNS sunucularını şirket içinde barındırır. Ağ uzmanlığına ve küresel varlığına sahip olduklarından bunu yapabilir.

Çoğu durumda, DNS ad çözümleme hizmetlerinizi bir hizmet sağlayıcısıyla barındırmak daha iyidir. Bu hizmet sağlayıcılarının, ad çözümleme hizmetleriniz için çok yüksek kullanılabilirlik sağlamak üzere ağ uzmanlığına ve küresel varlığına sahip olması gerekir. Kullanılabilirlik, DNS hizmetleri için gereklidir, çünkü ad çözümleme hizmetleriniz başarısız olursa hiçbir kimse internet 'e yönelik hizmetlere erişemeyecektir.

Azure, Azure DNS biçiminde yüksek düzeyde kullanılabilir ve yüksek performanslı bir dış DNS çözümü sağlar. Bu dış ad çözümleme çözümü, dünya çapındaki Azure DNS altyapısından yararlanır. Diğer Azure hizmetlerinizle aynı kimlik bilgilerini, API 'Leri, araçları ve faturalandırmayı kullanarak etki alanınızı Azure 'da barındırmanıza olanak tanır. Azure 'un bir parçası olarak, platformda yerleşik olarak bulunan güçlü güvenlik denetimlerini de devralır.

Daha fazla bilgi:

* [Azure DNS genel bakış](../../dns/dns-overview.md)
* [Azure DNS özel bölgeler](../../dns/private-dns-overview.md) , özel bir DNS çözümü eklemeye gerek kalmadan otomatik olarak atanan adlar yerine Azure kaynakları IÇIN özel DNS adlarını yapılandırmanıza olanak tanır.

## <a name="perimeter-network-architecture"></a>Çevre ağ mimarisi

Birçok büyük kuruluş, ağlarını segmentlere ayırmak için çevre ağları kullanır ve internet ile hizmetleri arasında bir arabellek bölgesi oluşturur. Ağın çevre bölümü düşük güvenlik bölgesi olarak değerlendirilir ve bu ağ kesimine hiçbir yüksek değerli varlık yerleştirilmez. Genellikle çevre ağ kesiminde bir ağ arabirimine sahip olan ağ güvenlik cihazlarını görürsünüz. Başka bir ağ arabirimi, internet 'ten gelen bağlantıları kabul eden sanal makineler ve hizmetler içeren bir ağa bağlıdır.

Çevre ağlarını çeşitli şekillerde tasarlayabilirsiniz. Bir çevre ağı dağıtma kararı ve ardından bir tane kullanmaya karar verirseniz kullanılacak çevre ağı türü, ağ güvenliği gereksinimlerinize bağlıdır.

Daha fazla bilgi:

* [Microsoft Bulut Hizmetleri ve ağ güvenliği](network-best-practices.md)

## <a name="azure-ddos-protection"></a>Azure DDoS koruması

Dağıtılmış hizmet engelleme (DDoS) saldırıları, uygulamalarını buluta taşıyan müşterilerin karşılaştığı en büyük kullanılabilirlik ve güvenlik sorunlarından biridir. DDoS saldırısı, uygulamanın kaynaklarını tüketmeye çalışır ve uygulamayı meşru kullanıcılar için kullanılamaz hale getirir. DDoS saldırıları internet üzerinden genel olarak erişilebilen herhangi bir uç noktasını hedefleyebilir.
Microsoft, Azure platformunun bir parçası olarak **temel** olarak bilinen DDoS koruması sağlar. Bu, ücretsiz olarak gelir ve genel ağ düzeyi saldırılarına karşı her zaman izleme ve gerçek zamanlı risk azaltma içerir. DDoS Protection **Basic** 'e dahil olan korumaların yanı sıra **Standart** seçeneğini de etkinleştirebilirsiniz. DDoS koruması standart özellikleri şunlardır:

* **Yerel platform tümleştirmesi:** Azure ile yerel olarak tümleşiktir. Azure portal aracılığıyla yapılandırmayı içerir. DDoS koruma standardı, kaynaklarınızı ve kaynak yapılandırmanızı anlamıştır.
* **Anahtar korumasını aç:** Basitleştirilmiş yapılandırma, DDoS koruma standardı etkin olduğu anda bir sanal ağ üzerindeki tüm kaynakları hemen korur. Müdahale veya Kullanıcı tanımı gerekli değildir. DDoS koruma standardı, algılandıktan sonra hızla ve otomatik olarak saldırıyı azaltır.
* **Her zaman açık trafik izleme:** Uygulama trafik desenleriniz günde 24 saat, haftada 7 gün, DDoS saldırıları için göstergeler aranıyor. Koruma ilkeleri aşıldığında risk azaltma gerçekleştirilir.
* **Saldırı riski azaltma raporları** Saldırı risk azaltma raporları, kaynaklarınıza hedeflenmiş saldırılar hakkında ayrıntılı bilgi sağlamak için toplanmış ağ akışı verilerini kullanır.
* **Saldırıya karşı risk azaltma akış günlükleri** Saldırı risk azaltma akış günlükleri, etkin bir DDoS saldırısı sırasında bırakılan trafiği, iletilen trafiği ve diğer saldırı verilerini neredeyse gerçek zamanlı olarak incelemenizi sağlar.
* **Uyarlamalı ayarlama:** Akıllı trafik profili oluşturma, uygulamanızın zaman içindeki trafiğini öğrenir ve hizmetiniz için en uygun profili seçer ve güncelleştirir. Profil zaman içinde trafik değişikliği olarak ayarlanır. Katman 3-katman 7 koruması: Bir Web uygulaması güvenlik duvarıyla birlikte kullanıldığında, tam yığın koruması sağlar.
* **Kapsamlı risk azaltma ölçeği:** 60 üzerinde farklı saldırı türleri, en büyük bilinen DDoS saldırılarına karşı koruma sağlamak için küresel kapasiteyle birlikte azaltılabilir.
* **Saldırı ölçümleri:** Her bir saldırıya ait özetlenen ölçümler Azure Izleyici aracılığıyla erişilebilir.
* **Saldırı Uyarısı:** Uyarılar, yerleşik saldırı ölçümleri kullanılarak saldırı başlangıcında ve durdurulduğunda ve saldırının süresi boyunca yapılandırılabilir. Uyarılar, Microsoft Azure Izleme günlükleri, splunk, Azure depolama, e-posta ve Azure portal gibi işletimsel yazılımınızla tümleştirilir.
* **Maliyet garantisi:**  Veri aktarımı ve belgelenmiş DDoS saldırıları için uygulama genişleme hizmeti kredileri.
* **DDoS hızlı yanıt** verme DDoS koruması standart müşterileri artık etkin bir saldırı sırasında hızlı yanıt ekibine erişebilir. DRR, saldırı sırasında özel azaltmalarda ve saldırı sonrası analizine yardımcı olabilir.


Daha fazla bilgi:

* [DDOS korumasına genel bakış](../../virtual-network/ddos-protection-overview.md)

## <a name="azure-front-door"></a>Azure ön kapısı

Azure ön kapı hizmeti, Web trafiğiniz için küresel yönlendirmeyi tanımlamanıza, yönetmenize ve izlemenize olanak sağlar. En iyi performans ve yüksek kullanılabilirlik için trafiğinizi yönlendirmeyi iyileştirir. Azure Front Door, HTTP/HTTPS iş yükünüzü istemci IP adreslerine, ülke koduna ve http parametrelerine dayalı istismardan korumaya yönelik erişim denetimi için özel web uygulaması güvenlik duvarı (WAF) kuralları yazmanıza olanak tanır. Ayrıca, ön kapı, kötü amaçlı bot trafiğini değerlendirmek için hız sınırlaması kuralları oluşturmanıza olanak tanıdığından, SSL boşaltma ve HTTP/HTTPS isteği, uygulama katmanı işleme de dahildir.

Ön kapı platformu, Azure DDoS Protection temel tarafından korunur. Korumayı artırmak için, sanal ağlarınızda Azure DDoS Koruması Standart etkinleştirilebilir ve kaynakları ağ katmanı (TCP/UDP) saldırılarına karşı otomatik ayar ve risk azaltma yoluyla koruma altına alır. Ön kapı, katman 7 ters ara sunucu, yalnızca Web trafiğinin arka uç sunucularına geçmesini sağlar ve varsayılan olarak diğer trafik türlerini engeller.

Daha fazla bilgi:

* Tüm Azure ön kapı özellikleri kümesi hakkında daha fazla bilgi için [Azure ön kapısına genel bakış ' ı](../../frontdoor/front-door-overview.md) gözden geçirebilirsiniz

## <a name="azure-traffic-manager"></a>Azure Traffic Manager

Azure Traffic Manager, trafiği farklı Azure bölgelerindeki hizmetlere en uygun şekilde dağıtırken yüksek kullanılabilirlik ve yanıtlama hızı sağlayan DNS tabanlı bir trafik yük dengeleyicidir. Traffic Manager, trafik yönlendirme yöntemine ve uç noktaların sistem durumuna bağlı olarak istemci isteklerini en uygun hizmet uç noktasına yönlendirmek için DNS hizmetini kullanır. Uç nokta, Azure içinde veya dışında barındırılan İnternet'e yönelik bir hizmettir. Traffic Manager bitiş noktalarını izler ve trafiği kullanılamayan uç noktalara yönlendirmez.

Daha fazla bilgi:

* [Azure Traffic Manager 'a genel bakış](../../traffic-manager/traffic-manager-overview.md)

## <a name="monitoring-and-threat-detection"></a>İzleme ve tehdit algılama

Azure, bu anahtar alanında, erken algılama, izleme ve ağ trafiğini toplama ve gözden geçirme olanakları sunan yetenekler sağlar.

### <a name="azure-network-watcher"></a>Azure ağ Izleyicisi

Azure ağ Izleyicisi, sorun gidermenize yardımcı olabilir ve güvenlik sorunlarının tanımlanmasına yardımcı olacak bir bütün yeni araç kümesi sağlar.

[Güvenlik grubu görünümü](../../network-watcher/network-watcher-security-group-view-overview.md) , sanal makinelerin denetim ve güvenlik uyumluluğuna yardımcı olur. Kuruluşunuz tarafından tanımlanan ana hat ilkelerini, sanal makinelerinizin her biri için geçerli kurallara göre karşılaştıran programlama denetimleri gerçekleştirmek için bu özelliği kullanın. Bu, herhangi bir yapılandırma Drii belirlemenize yardımcı olabilir.

[Paket yakalama](../../network-watcher/network-watcher-packet-capture-overview.md) , sanal makineden gelen ve giden ağ trafiğini yakalamanızı sağlar. Ağ istatistiklerini toplayabilir ve uygulama sorunlarını giderebilirsiniz, bu da ağ yetkisiz erişimlere karşı çok değerli olabilir. Ayrıca, belirli Azure uyarılarına yanıt olarak ağ yakalamalarını başlatmak için bu özelliği Azure Işlevleri ile birlikte kullanabilirsiniz.

Ağ Izleyicisi hakkında daha fazla bilgi ve laboratuvarlarınızın bazı işlevlerinden nasıl başlayayapılacağı hakkında daha fazla bilgi için bkz. [Azure Ağ İzleyicisi izlemeye genel bakış](../../network-watcher/network-watcher-monitoring-overview.md).

> [!NOTE]
> Bu hizmetin kullanılabilirliği ve durumuyla ilgili en güncel bildirimler için, [Azure Updates sayfasını](https://azure.microsoft.com/updates/?product=network-watcher)inceleyin.

### <a name="azure-security-center"></a>Azure Güvenlik Merkezi

Azure Güvenlik Merkezi, tehditleri önlemenize, algılamanıza ve yanıtlamanıza yardımcı olur ve Azure kaynaklarınızın güvenliğini ve denetimini artırabilir ve üzerinde denetim sağlar. Azure aboneliklerinizde tümleşik güvenlik izleme ve ilke yönetimi sağlar, aksi takdirde fark edilmemiş tehditleri algılamaya yardımcı olur ve büyük bir güvenlik çözümleri kümesiyle birlikte çalışabilir.

Güvenlik Merkezi, ağ güvenliğini iyileştirmenize ve izlemenize yardımcı olur:

* Ağ güvenlik önerileri sağlama.
* Ağ güvenliği yapılandırmanızın durumunu izleme.
* Ağ tabanlı tehditler için hem uç noktada hem de ağ düzeylerinde sizi uyarır.

Daha fazla bilgi:

* [Azure Güvenlik Merkezi'ne Giriş](../../security-center/security-center-intro.md)

### <a name="virtual-network-tap"></a>Sanal Ağ TAP

Azure sanal ağ TAP (Terminal erişim noktası), sanal makine ağ trafiğinizi bir ağ paketi toplayıcısına veya analiz aracına sürekli olarak akışla kullanmanıza olanak sağlar. Toplayıcı veya Analiz Aracı bir ağ sanal gereç ortağı tarafından sağlanır. Aynı veya farklı aboneliklerdeki birden çok ağ arabiriminden gelen trafiği toplamak için aynı sanal ağ TAP kaynağını kullanabilirsiniz.

Daha fazla bilgi:

* [Sanal ağ dokunma](../../virtual-network/virtual-network-tap-overview.md)

### <a name="logging"></a>Günlüğe Kaydetme

Ağ düzeyinde günlüğe kaydetme, herhangi bir ağ güvenliği senaryosu için bir anahtar işlevdir. Azure 'da, ağ düzeyinde günlüğe kaydetme bilgilerini almak için NSG 'ler için edinilen bilgileri günlüğe kaydedebilirsiniz. NSG günlüğe kaydetme sayesinde, şuradan bilgi alırsınız:

* [Etkinlik günlükleri](../../azure-monitor/platform/activity-logs-overview.md). Azure aboneliklerinize gönderilen tüm işlemleri görüntülemek için bu günlükleri kullanın. Bu günlükler varsayılan olarak etkindir ve Azure portal içinde kullanılabilir. Daha önce denetim veya işlem günlükleri olarak bilinirdi.
* Olay günlükleri. Bu Günlükler, hangi NSG kurallarının uygulandığı hakkında bilgi sağlar.
* Sayaç günlükleri. Bu Günlükler, her NSG kuralının trafiği reddetme veya izin verme için kaç kez uygulanacağını size sağlar.

Bu günlükleri görüntülemek ve analiz etmek için güçlü bir veri görselleştirme aracı olan [Microsoft Power BI](https://powerbi.microsoft.com/what-is-power-bi/)de kullanabilirsiniz.
Daha fazla bilgi:

* [Ağ güvenlik grupları (NSG 'ler) için Azure Izleyici günlükleri](../../virtual-network/virtual-network-nsg-manage-log.md)
