---
title: SSS-Azure ExpressRoute | Microsoft Docs
description: ExpressRoute SSS, desteklenen Azure Hizmetleri, maliyet, veri ve bağlantılar, SLA, sağlayıcılar ve konumlar, bant genişliği ve ek teknik ayrıntılar hakkında bilgiler içerir.
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: jaredro
ms.custom: seodec18
ms.openlocfilehash: f27a6df86ebbe2b07b73016f304ac364e88664bb
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73891047"
---
# <a name="expressroute-faq"></a>ExpressRoute SSS

## <a name="what-is-expressroute"></a>ExpressRoute nedir?

ExpressRoute, Microsoft veri merkezleri ile şirket içinde veya bir birlikte bulundurma tesisinde bulunan altyapı arasında özel bağlantılar oluşturmanızı sağlayan bir Azure hizmetidir. ExpressRoute bağlantıları, genel Internet üzerinden geçmez ve Internet üzerinden tipik bağlantılardan daha düşük gecikme süreleriyle daha yüksek güvenlik, güvenilirlik ve hız sunar.

### <a name="what-are-the-benefits-of-using-expressroute-and-private-network-connections"></a>ExpressRoute ve özel ağ bağlantılarını kullanmanın avantajları nelerdir?

ExpressRoute bağlantıları ortak İnternet üzerinden geçmemektedir. Internet üzerinden tipik bağlantılardan daha düşük ve tutarlı gecikme süreleriyle daha yüksek güvenlik, güvenilirlik ve hız sunar. Bazı durumlarda, şirket içi cihazlar ve Azure arasında veri aktarmak için ExpressRoute bağlantılarını kullanmak önemli maliyet avantajları sağlayabilir.

### <a name="where-is-the-service-available"></a>Hizmet nerede kullanılabilir?

Hizmet konumu ve kullanılabilirliği için bu sayfaya bakın: [ExpressRoute iş ortakları ve konumları](expressroute-locations.md).

### <a name="how-can-i-use-expressroute-to-connect-to-microsoft-if-i-dont-have-partnerships-with-one-of-the-expressroute-carrier-partners"></a>ExpressRoute-taşıyıcı iş ortaklarından biriyle ortaklığı yoksa Microsoft 'a bağlanmak için ExpressRoute 'ı nasıl kullanabilirim?

Desteklenen Exchange sağlayıcısı konumlarından birine bir bölgesel taşıyıcı ve Land Ethernet bağlantısı seçebilirsiniz. Daha sonra sağlayıcı konumunda Microsoft ile eş aktarabilirsiniz. Hizmet sağlayıcınızın Exchange konumlarından birinde mevcut olup olmadığını görmek için [ExpressRoute iş ortaklarının ve konumlarının](expressroute-locations.md) son bölümünü denetleyin. Daha sonra Azure 'a bağlanmak için hizmet sağlayıcı aracılığıyla bir ExpressRoute bağlantı hattı sıralaması yapabilirsiniz.

### <a name="how-much-does-expressroute-cost"></a>ExpressRoute maliyeti ne kadar sürer?

Fiyatlandırma bilgileri için [fiyatlandırma ayrıntılarına](https://azure.microsoft.com/pricing/details/expressroute/) bakın.

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-does-the-vpn-connection-i-purchase-from-my-network-service-provider-have-to-be-the-same-speed"></a>Belirli bir bant genişliği için bir ExpressRoute bağlantı hattı ödediğimde, ağ hizmeti sağlayıcımdan satın dığım VPN bağlantısının aynı hızda olması gerekir mi?

Hayır. Hizmet sağlayıcınızdan herhangi bir hızda VPN bağlantısı satın alabilirsiniz. Ancak Azure bağlantınız, satın aldığınız ExpressRoute bağlantı hattı bant genişliği ile sınırlıdır.

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-do-i-have-the-ability-to-burst-up-to-higher-speeds-if-necessary"></a>Belirli bir bant genişliği için bir ExpressRoute bağlantı hattı ödediğimde, gerekirse daha yüksek hızlara veri bloğu oluşturma olanağına sahip mıyım?

Evet. ExpressRoute devreleri, ek ücret ödemeden temin bant genişliği sınırının en fazla iki katına çıkmasına olanak tanımak üzere yapılandırılır. Bu özelliği desteklediklerinden emin olmak için hizmet sağlayıcınıza başvurun. Bu, devamlı bir süre için değildir ve garanti edilmez.  Trafik bir ExpressRoute ağ geçidiyle akar, SKU 'nun bant genişliği sabittir ve Burstable değildir.

### <a name="can-i-use-the-same-private-network-connection-with-virtual-network-and-other-azure-services-simultaneously"></a>Aynı anda sanal ağ ve diğer Azure hizmetleriyle aynı özel ağ bağlantısını kullanabilir miyim?

Evet. Bir ExpressRoute bağlantı hattı ayarlandığında, bir sanal ağ ve diğer Azure Hizmetleri içindeki hizmetlere aynı anda erişmenize izin verir. Özel eşleme yolu üzerinden sanal ağlara ve Microsoft eşleme yolu üzerinden diğer hizmetlere bağlanırsınız.

### <a name="does-expressroute-offer-a-service-level-agreement-sla"></a>ExpressRoute bir Hizmet Düzeyi Sözleşmesi (SLA) sunuyor mu?

Bilgi için bkz. [ExpressRoute SLA](https://azure.microsoft.com/support/legal/sla/) sayfası.

## <a name="supported-services"></a>Desteklenen hizmetler

ExpressRoute çeşitli türlerde hizmetler için [üç yönlendirme etki alanını](expressroute-circuit-peerings.md) destekler: özel eşleme, Microsoft eşlemesi ve genel eşleme.

### <a name="private-peering"></a>Özel eşleme

* Tüm sanal makineler ve bulut hizmetleri dahil sanal ağlar

### <a name="microsoft-peering"></a>Microsoft eşlemesi

* [Office 365](https://aka.ms/ExpressRouteOffice365)
* Power BI-Azure bölgesel bir topluluk aracılığıyla kullanılabilir Power BI kiracınızın bölgesini bulma hakkında bilgi için [buraya](https://docs.microsoft.com/power-bi/service-admin-where-is-my-tenant-located) bakın.
* Azure Active Directory
* [Windows sanal masaüstü](https://azure.microsoft.com/services/virtual-desktop/)
* [Azure DevOps](https://blogs.msdn.microsoft.com/devops/2018/10/23/expressroute-for-azure-devops/) (Azure küresel hizmetler topluluğu)
* Azure hizmetlerinin çoğu desteklenir. Lütfen desteği doğrulamak için kullanmak istediğiniz hizmeti doğrudan denetleyin.<br><br>**Aşağıdaki hizmetler desteklenmez**:
    * CDN
    * Azure Front Door
    * Multi-Factor Authentication sunucusu (eski)
    * Traffic Manager

### <a name="public-peering"></a>Ortak eşleme

>[!NOTE]
>Genel eşleme, yeni ExpressRoute devreleri üzerinde devre dışı bırakıldı. Azure Hizmetleri Microsoft eşlemesi ' nde kullanılabilir.
>

* Power BI
* Azure hizmetlerinin çoğu desteklenir. Lütfen desteği doğrulamak için kullanmak istediğiniz hizmeti doğrudan denetleyin.<br><br>
  **Aşağıdaki hizmetler desteklenmez**:
    * CDN
    * Azure Front Door
    * Multi-Factor Authentication sunucusu (eski)
    * Traffic Manager

### <a name="why-i-see-advertised-public-prefixes-status-as-validation-needed-while-configuring-microsoft-peering"></a>' Doğrulama gerekli ' olarak ' tanıtılan genel ön ekler ' durumunu neden görmem, Microsoft eşlemesi yapılandırılırken?

Microsoft, belirtilen ' tanıtılan genel ön eklerin ' ve ' eşdüzey ASN ' (veya ' müşteri ASN ') tarafından Internet yönlendirme kayıt defterinde size atanıp atanmadığını doğrular. Diğer bir varlıktan ortak ön ekleri alıyorsanız ve atama, yönlendirme kayıt defteriyle birlikte kaydedilmetiyse, otomatik doğrulama tamamlanmaz ve el ile doğrulama gerektirir. Otomatik doğrulama başarısız olursa, ' doğrulama gerekiyor ' iletisini görürsünüz.

' Doğrulama gerekli ' iletisini görürseniz, genel önekleri gösteren belge (ler) i, yönlendirme kayıt defterindeki ön eklerin sahibi olarak listelenen varlık tarafından kuruluşunuza atanır ve bu belgeleri el ile doğrulama için aşağıda gösterildiği gibi bir destek bileti açılıyor.

![](./media/expressroute-faqs/ticket-portal-msftpeering-prefix-validation.png)

### <a name="is-dynamics-365-supported-on-expressroute"></a>Dynamics 365 ExpressRoute 'da destekleniyor mu?

Dynamics 365 ve Common Data Service (CD) ortamları Azure üzerinde barındırılır ve bu nedenle müşteriler Azure kaynakları için temeldeki ExpressRoute desteğinden faydalanır. Yönlendirici filtreniz, Dynamics 365/CD ortamlarınızın barındırıldığı Azure bölgelerini içeriyorsa, hizmet uç noktalarına bağlanabilirsiniz.

> [!NOTE]
> [ExpressRoute Premium](https://docs.microsoft.com/azure/expressroute/expressroute-faqs#expressroute-premium) , Azure ExpressRoute üzerinden Dynamics 365 bağlantısı için gerekli **değildir** .

## <a name="data-and-connections"></a>Veriler ve bağlantılar

### <a name="are-there-limits-on-the-amount-of-data-that-i-can-transfer-using-expressroute"></a>ExpressRoute kullanarak aktaradığım veri miktarına yönelik sınırlamalar var mı?

Veri aktarımı miktarı için bir sınır ayarlanmıyoruz. Bant genişliği ücretleri hakkında bilgi edinmek için [fiyatlandırma ayrıntılarına](https://azure.microsoft.com/pricing/details/expressroute/) bakın.

### <a name="what-connection-speeds-are-supported-by-expressroute"></a>ExpressRoute tarafından hangi bağlantı hızları desteklenir?

Desteklenen bant genişliği teklifleri:

50 Mb/sn, 100 Mb/sn, 200 Mb/sn, 500 Mb/sn, 1 Gb/sn, 2 Gb/sn, 5 Gb/sn, 10 Gb/sn

### <a name="which-service-providers-are-available"></a>Hangi hizmet sağlayıcıları kullanılabilir?

Hizmet sağlayıcılarının ve konumların listesi için bkz. [ExpressRoute iş ortakları ve konumları](expressroute-locations.md) .

## <a name="technical-details"></a>Teknik ayrıntılar

### <a name="what-are-the-technical-requirements-for-connecting-my-on-premises-location-to-azure"></a>Şirket içi konumumu Azure 'a bağlamak için teknik gereksinimler nelerdir?

Gereksinimler için bkz. [ExpressRoute önkoşulları sayfası](expressroute-prerequisites.md) .

### <a name="are-connections-to-expressroute-redundant"></a>ExpressRoute bağlantısı yedekli mi?

Evet. Her bir ExpressRoute bağlantı hattı, yüksek kullanılabilirlik sağlamak üzere yapılandırılmış, fazladan bir çapraz bağlantı çiftliğine sahiptir.

### <a name="will-i-lose-connectivity-if-one-of-my-expressroute-links-fail"></a>ExpressRoute bağlantılarınızdan biri başarısız olursa bağlantıyı kaybedecek mıyım?

Çapraz bağlantılardan biri başarısız olursa bağlantınız kaybedilmez. Ağınızın yükünü desteklemek ve ExpressRoute bağlantı hattınızı yüksek oranda sağlamak için yedekli bir bağlantı mevcuttur. Ayrıca, devre düzeyi esnekliği sağlamak için farklı bir eşleme konumunda bir devre oluşturabilirsiniz.

### <a name="how-do-i-implement-redundancy-on-private-peering"></a>Özel eşleme üzerinde artıklık Nasıl yaparım? mi?

Farklı eşleme konumlarından birden fazla ExpressRoute devresi, tek bir devrenin kullanılamaz hale gelmesi durumunda yüksek kullanılabilirlik sağlamak için aynı sanal ağa bağlanabilir. Ardından, belirli bir devreyi tercih etmek için yerel bağlantıya [daha yüksek ağırlıklar atayabilirsiniz](https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-assign-a-high-weight-to-local-connection) . Müşterilerin tek hata noktalarından kaçınmak için en az iki ExpressRoute devrenini kurulumu önemle önerilir. 

Yüksek [kullanılabilirlik ve olağanüstü](https://docs.microsoft.com/azure/expressroute/designing-for-disaster-recovery-with-expressroute-privatepeering) durum kurtarma için tasarlamak üzere [buraya](https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute) bakın.  

### <a name="how-i-do-implement-redundancy-on-microsoft-peering"></a>Microsoft eşlemesiyle yedeklilik nasıl uygulanır?

Müşteriler, Azure depolama veya Azure SQL gibi Azure genel hizmetlerine erişmek için Microsoft eşlemesi 'ni kullanırken ve Office 365 için Microsoft eşlemesi 'ni kullanan müşterilerin farklı eşlemede birden çok devreleri uyguladıkları durumlarda kesinlikle önerilir tek hata noktalarından kaçınmak için konumlar. Müşteriler her iki devrede aynı öneki verebilir ve şirket içi yolu belirlemede farklı ön [bekleyen olarak](https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-use-as-path-prepending) kullanabilir ya da farklı ön ekleri duyurur.

Yüksek kullanılabilirlik için tasarlamak üzere [buraya](https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute) bakın.

### <a name="how-do-i-ensure-high-availability-on-a-virtual-network-connected-to-expressroute"></a>ExpressRoute 'a bağlı bir sanal ağ üzerinde yüksek kullanılabilirlik Nasıl yaparım? emin misiniz?

ExpressRoute devreleri farklı eşleme konumlarında (örneğin, Singapur, Singapur2) sanal ağınıza bağlayarak yüksek kullanılabilirlik elde edebilirsiniz. Bir ExpressRoute bağlantı hattı devre dışı bırakılırsa, bağlantı başka bir ExpressRoute devresine yük devreder. Varsayılan olarak, Sanal ağınızı bırakma trafiği, eşit maliyetli çoklu yol yönlendirmesi (ECMP) temelinde yönlendirilir. Bağlantı ağırlığı ' nu kullanarak bir devreyi başka bir şekilde tercih edebilirsiniz. Daha fazla bilgi için bkz. [ExpressRoute yönlendirmeyi iyileştirme](expressroute-optimize-routing.md).

### <a name="how-do-i-ensure-that-my-traffic-destined-for-azure-public-services-like-azure-storage-and-azure-sql-on-microsoft-or-public-peering-is-preferred-on-the-expressroute-path"></a>Nasıl yaparım? Azure depolama ve Microsoft 'un Azure SQL veya genel eşleme gibi Azure genel hizmetlerine ait trafimin ExpressRoute yolunda tercih edildiği konusunda emin misiniz?

Şirket içinden Azure 'a giden yolun ExpressRoute bağlantı hattınızdan her zaman tercih edildiğini sağlamak için yönlendiricilerinizde *yerel tercih* özniteliğini uygulamanız gerekir.

Daha [fazla ayrıntı IÇIN](https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#path-selection-on-microsoft-and-public-peerings) BGP yolu seçimi ve ortak yönlendirici yapılandırması bölümüne bakın. 

### <a name="onep2plink"></a>Bir bulut Exchange 'de birlikte bulundurmadığım ve hizmet sağlayıcımın noktadan noktaya bağlantı sağladığından, şirket içi ağ ile Microsoft arasında iki fiziksel bağlantı sipariş etmem gerekir mi?

Hizmet sağlayıcınız fiziksel bağlantı üzerinde iki adet Ethernet sanal devre kuramazsa yalnızca bir fiziksel bağlantınız olması gerekir. Fiziksel bağlantı (örneğin, bir optik fiber) Katman 1 (L1) cihazında (görüntüye bakın) sonlandırılır. İki Ethernet sanal devresi, biri birincil devre ve diğeri ikincil için olmak üzere farklı VLAN kimlikleriyle etiketlenebilir. Bu VLAN kimlikleri dıştaki 802.1 Q Ethernet üst bilgisinde bulunur. İç 802.1 Q Ethernet üst bilgisi (gösterilmez), belirli bir [ExpressRoute yönlendirme etki alanına](expressroute-circuit-peerings.md)eşlenir.

![](./media/expressroute-faqs/expressroute-p2p-ref-arch.png)

### <a name="can-i-extend-one-of-my-vlans-to-azure-using-expressroute"></a>ExpressRoute kullanarak VLAN 'lerimin birini Azure 'a genişletebilirim miyim?

Hayır. Katman 2 bağlantı uzantılarını Azure 'da desteklemiyoruz.

### <a name="can-i-have-more-than-one-expressroute-circuit-in-my-subscription"></a>Aboneliğimde birden fazla ExpressRoute devresine sahip olabilir miyim?

Evet. Aboneliğinizde birden fazla ExpressRoute devresine sahip olabilirsiniz. Varsayılan sınır 10 olarak ayarlanır. Gerekirse sınırı artırmak için Microsoft Desteği ile iletişim sağlayabilirsiniz.

### <a name="can-i-have-expressroute-circuits-from-different-service-providers"></a>Farklı hizmet sağlayıcılarından ExpressRoute devrelerine sahip olabilir miyim?

Evet. Birçok hizmet sağlayıcısı ile ExpressRoute devrelerine sahip olabilirsiniz. Her ExpressRoute devresi yalnızca bir hizmet sağlayıcısıyla ilişkilendirilir. 

### <a name="i-see-two-expressroute-peering-locations-in-the-same-metro-for-example-singapore-and-singapore2-which-peering-location-should-i-choose-to-create-my-expressroute-circuit"></a>Aynı metro 'da iki ExpressRoute eşleme konumu görüyorum (örneğin, Singapur ve Singapur2). ExpressRoute devresini oluşturmak için hangi eşleme konumunu seçmem gerekir?
Hizmet sağlayıcınız her iki sitede de ExpressRoute sunuyorsa, sağlayıcınızda çalışabilir ve ExpressRoute 'u ayarlamak için siteyi seçebilirsiniz. 

### <a name="can-i-have-multiple-expressroute-circuits-in-the-same-metro-can-i-link-them-to-the-same-virtual-network"></a>Aynı metro 'da birden fazla ExpressRoute devresine sahip olabilir miyim? Aynı sanal ağa bağlanabilir miyim?

Evet. Aynı veya farklı hizmet sağlayıcılarına sahip birden fazla ExpressRoute devresine sahip olabilirsiniz. Metro 'da birden fazla ExpressRoute eşleme konumu varsa ve devreler farklı eşleme konumlarında oluşturulduysa, bunları aynı sanal ağa bağlayabilirsiniz. Devreler aynı eşleme konumunda oluşturulduysa, aynı sanal ağa 4 adede kadar bağlantı bağlayabilirsiniz.

### <a name="how-do-i-connect-my-virtual-networks-to-an-expressroute-circuit"></a>Sanal ağlarımı bir ExpressRoute devresine bağlama Nasıl yaparım?

Temel adımlar şunlardır:

* Bir ExpressRoute bağlantı hattı oluşturun ve hizmet sağlayıcının etkinleştirmesini etkinleştirin.
* Siz veya sağlayıcı BGP eşlemesini yapılandırmalıdır.
* Sanal ağı ExpressRoute devresine bağlayın.

Daha fazla bilgi için bkz. [devre sağlama ve devre durumları Için ExpressRoute iş akışları](expressroute-workflows.md).

### <a name="are-there-connectivity-boundaries-for-my-expressroute-circuit"></a>ExpressRoute devreniz için bağlantı sınırları var mı?

Evet. [ExpressRoute iş ortakları ve konumları](expressroute-locations.md) makalesi, bir ExpressRoute bağlantı hattının bağlantı sınırlarına genel bir bakış sağlar. ExpressRoute bağlantı hattı bağlantısı, tek bir geopolitik bölge ile sınırlıdır. ExpressRoute Premium özelliğini etkinleştirerek bağlantı, çapraz politiksel bölgelere genişletilebilir.

### <a name="can-i-link-to-more-than-one-virtual-network-to-an-expressroute-circuit"></a>Bir ExpressRoute devresine birden fazla sanal ağa bağlanabilir miyim?

Evet. Standart bir ExpressRoute bağlantı hattı üzerinde en fazla 10 sanal ağ bağlantınız olabilir ve [Premium ExpressRoute devresi](#expressroute-premium)üzerinde 100 ' e varan bir bağlantı oluşturabilirsiniz. 

### <a name="i-have-multiple-azure-subscriptions-that-contain-virtual-networks-can-i-connect-virtual-networks-that-are-in-separate-subscriptions-to-a-single-expressroute-circuit"></a>Sanal ağlar içeren birden çok Azure aboneliğiniz var. Ayrı aboneliklerdeki sanal ağlara tek bir ExpressRoute devresine bağlanabilir miyim?

Evet. Tek bir ExpressRoute bağlantı hattını kullanarak, devre veya farklı aboneliklerle aynı abonelikte en fazla 10 sanal ağ bağlayabilirsiniz. Bu sınır, ExpressRoute Premium özelliği etkinleştirilerek artırılabilir.

Daha fazla bilgi için bkz. [birden çok abonelik arasında bir ExpressRoute devresini paylaşma](expressroute-howto-linkvnet-arm.md).

### <a name="i-have-multiple-azure-subscriptions-associated-to-different-azure-active-directory-tenants-or-enterprise-agreement-enrollments-can-i-connect-virtual-networks-that-are-in-separate-tenants-and-enrollments-to-a-single-expressroute-circuit-not-in-the-same-tenant-or-enrollment"></a>Farklı Azure Active Directory kiracılar veya Kurumsal Anlaşma kayıtları ile ilişkili birden çok Azure aboneliğiniz var. Ayrı kiracılar ve kayıtlar 'daki sanal ağları aynı kiracı ya da kayıt içinde olmayan tek bir ExpressRoute devresine bağlanabilir miyim?

Evet. ExpressRoute yetkilendirmeleri, ek yapılandırma gerekmeden abonelik, kiracı ve kayıt sınırlarını kapsayabilir. 

Daha fazla bilgi için bkz. [birden çok abonelik arasında bir ExpressRoute devresini paylaşma](expressroute-howto-linkvnet-arm.md).

### <a name="are-virtual-networks-connected-to-the-same-circuit-isolated-from-each-other"></a>Sanal ağlar birbirleriyle yalıtılmış aynı bağlantı hattına mı bağlı?

Hayır. Yönlendirme perspektifinden, aynı ExpressRoute bağlantı hattına bağlı tüm sanal ağlar aynı yönlendirme etki alanının parçasıdır ve birbirinden yalıtılmaz. Yol yalıtımına ihtiyacınız varsa, ayrı bir ExpressRoute devresi oluşturmanız gerekir.

### <a name="can-i-have-one-virtual-network-connected-to-more-than-one-expressroute-circuit"></a>Birden fazla ExpressRoute devresine bağlı bir sanal ağa sahip olabilir miyim?

Evet. Tek bir sanal ağı, aynı veya farklı eşleme konumlarında en fazla dört ExpressRoute devreni ile bağlayabilirsiniz. 

### <a name="can-i-access-the-internet-from-my-virtual-networks-connected-to-expressroute-circuits"></a>ExpressRoute devrelerine bağlı sanal ağlarım üzerinden Internet 'e erişebilir miyim?

Evet. BGP oturumu aracılığıyla varsayılan yolları (0.0.0.0/0) veya Internet Route öneklerini tanıdıysanız, bir ExpressRoute devresine bağlı bir sanal ağdan Internet 'e bağlanabilirsiniz.

### <a name="can-i-block-internet-connectivity-to-virtual-networks-connected-to-expressroute-circuits"></a>ExpressRoute devrelerine bağlı sanal ağlara Internet bağlantısını engelleyebilir miyim?

Evet. Bir sanal ağ içinde dağıtılan sanal makinelere yönelik tüm Internet bağlantılarını engellemek ve tüm trafiği ExpressRoute devresi aracılığıyla yönlendirmek için varsayılan yolları (0.0.0.0/0) duyurtabilirsiniz.

Varsayılan yolları tanılarsanız, Microsoft eşlemesi (Azure depolama ve SQL DB gibi) üzerinden sunulan hizmetlere trafiği şirket içine geri zorlıyoruz. Yönlendiricilerinizi, Microsoft eşleme yolu veya Internet üzerinden Azure 'a trafik döndürecek şekilde yapılandırmanız gerekecektir. Hizmet için bir hizmet uç noktası etkinleştirdiyseniz, hizmete giden trafik, şirket içine zorlanmaz. Trafik, Azure omurga ağı içinde kalır. Hizmet uç noktaları hakkında daha fazla bilgi için bkz. [sanal ağ hizmeti uç noktaları](../virtual-network/virtual-network-service-endpoints-overview.md?toc=%2fazure%2fexpressroute%2ftoc.json)

### <a name="can-virtual-networks-linked-to-the-same-expressroute-circuit-talk-to-each-other"></a>Aynı ExpressRoute bağlantı hattına bağlı sanal ağlar birbirleriyle iletişim kurabilir mi?

Evet. Aynı ExpressRoute devresine bağlı sanal ağlarda dağıtılan sanal makineler birbirleriyle iletişim kurabilir.

### <a name="can-i-use-site-to-site-connectivity-for-virtual-networks-in-conjunction-with-expressroute"></a>ExpressRoute ile birlikte sanal ağlar için siteden siteye bağlantı kullanabilir miyim?

Evet. ExpressRoute, siteden siteye VPN 'Ler ile birlikte çalışabilir. Bkz. [ExpressRoute ve siteden siteye birlikte bulunan bağlantıları yapılandırma](expressroute-howto-coexist-resource-manager.md).

### <a name="why-is-there-a-public-ip-address-associated-with-the-expressroute-gateway-on-a-virtual-network"></a>Bir sanal ağ üzerindeki ExpressRoute ağ geçidiyle ilişkili bir genel IP adresi neden var?

Genel IP adresi yalnızca iç yönetim için kullanılır ve sanal ağınızın güvenlik açısından bir etkilenme yapmaz.

### <a name="are-there-limits-on-the-number-of-routes-i-can-advertise"></a>Duyurabileceğim yol sayısı için sınırlamalar var mı?

Evet. Microsoft eşlemesi için özel eşleme ve 200 için en fazla 4000 rota öneki kabul ediyoruz. ExpressRoute Premium özelliğini etkinleştirirseniz bu, özel eşleme için 10.000 rotasıyla artırılabilir.

### <a name="are-there-restrictions-on-ip-ranges-i-can-advertise-over-the-bgp-session"></a>BGP oturumu üzerinde tanıtım edediğim IP aralıklarında kısıtlamalar var mı?

Microsoft eşleme BGP oturumu için özel ön ekleri (RFC1918) kabul etmedik. Hem Microsoft hem de özel eşleme üzerinde herhangi bir ön ek boyutu (/32) kabul ediyoruz.

### <a name="what-happens-if-i-exceed-the-bgp-limits"></a>BGP sınırlarını aşsam ne olur?

BGP oturumları bırakılacak. Ön ek sayısı sınırın altına gittiğinde sıfırlanacaktır.

### <a name="what-is-the-expressroute-bgp-hold-time-can-it-be-adjusted"></a>ExpressRoute BGP tutma süresi nedir? Ayarlanabilir mi?

Tutma süresi 180 ' dir. Etkin tut iletileri her 60 saniyede bir gönderilir. Bunlar, Microsoft tarafında değiştirilemeyen sabit ayarlardır. Farklı zamanlayıcılar yapılandırmanız mümkündür ve BGP oturum parametreleri uygun şekilde anlaşacaktır.

### <a name="can-i-change-the-bandwidth-of-an-expressroute-circuit"></a>Bir ExpressRoute devresinin bant genişliğini değiştirebilir miyim?

Evet, Azure portal ExpressRoute bağlantı hattının genişliğini veya PowerShell 'i kullanarak artırmayı deneyebilirsiniz. Devreniz üzerinde oluşturulduğu fiziksel bağlantı noktasında kapasite varsa, değişiklikleriniz başarılı olur. 

Yaptığınız değişiklik başarısız olursa, geçerli bağlantı noktasında yeterli kapasite yok demektir ve daha yüksek bant genişliğine sahip yeni bir ExpressRoute devresi oluşturmanız veya bu konumda ek bir kapasite olmaması gerekir, bu durumda bant genişliği. 

Ayrıca, bant genişliği artışını desteklemek üzere ağları içindeki kısıtlamalarını güncelleştirdiklerinden emin olmak için bağlantı sağlayıcınızla birlikte izlemeniz gerekir. Ancak, ExpressRoute bağlantı hattının bant genişliğini azaltabilirsiniz. Daha düşük bant genişliğine sahip yeni bir ExpressRoute devresi oluşturmanız ve eski devreyi silmeniz gerekir.

### <a name="how-do-i-change-the-bandwidth-of-an-expressroute-circuit"></a>ExpressRoute devresine ait bant genişliğini değiştirmek Nasıl yaparım??

REST API veya PowerShell cmdlet 'ini kullanarak ExpressRoute bağlantı hattının bant genişliğini güncelleştirebilirsiniz.

## <a name="expressroute-premium"></a>ExpressRoute Premium

### <a name="what-is-expressroute-premium"></a>ExpressRoute Premium nedir?

ExpressRoute Premium, aşağıdaki özelliklerin bir koleksiyonudur:

* 4000 rotasındaki yönlendirme tablosu sınırı, özel eşleme için 10.000 rotalara kadar artar.
* ExpressRoute bağlantı hattı üzerinde etkinleştirilenebilir VNET 'ler ve ExpressRoute Global Reach bağlantıları (varsayılan değer 10 ' dur). Daha fazla bilgi için bkz. [ExpressRoute limitleri](#limits) tablosu.
* Office 365 bağlantısı
* Microsoft Core ağı üzerinden genel bağlantı. Artık başka bir bölgedeki bir ExpressRoute bağlantı hattına sahip bir sanal ağı tek bir geopolitik bölgede bağlayabilirsiniz.<br>
    **Örnekler**

    *  Avrupa Batı 'da oluşturulan bir sanal ağı, Silicon Valley içinde oluşturulan bir ExpressRoute devresine bağlayabilirsiniz. 
    *  Microsoft eşlemesi ' nde, diğer geopolitik bölgelerden gelen önekler, örneğin, kuzeybatı batı 'daki SQL Azure, örneğin, Silicon Valley 'teki bir bağlantı hattı üzerinden tanıtılabilir.


### <a name="limits"></a>ExpressRoute Premium 'u etkinleştirdiğimde ExpressRoute devresi üzerinde kaç VNET ve ExpressRoute Global Reach bağlantısı etkinleştirebilirim?

Aşağıdaki tablolar ExpressRoute bağlantı hattı başına ExpressRoute sınırlarını ve sanal ağlar ve ExpressRoute Global Reach bağlantı sayısını gösterir:

[!INCLUDE [ExpressRoute limits](../../includes/expressroute-limits.md)]

### <a name="how-do-i-enable-expressroute-premium"></a>ExpressRoute Premium 'u etkinleştirmek Nasıl yaparım? mı?

ExpressRoute Premium özellikleri özellik etkinleştirildiğinde etkinleştirilebilir ve devre durumu güncelleştirilerek kapatılabilir. Devre oluşturma sırasında ExpressRoute Premium 'u etkinleştirebilir veya REST API/PowerShell cmdlet 'ini çağırabilir.

### <a name="how-do-i-disable-expressroute-premium"></a>ExpressRoute Premium 'u devre dışı Nasıl yaparım?.

REST API veya PowerShell cmdlet 'ini çağırarak ExpressRoute Premium 'u devre dışı bırakabilirsiniz. ExpressRoute Premium 'u devre dışı bırakmadan önce bağlantı ihtiyaçlarınızı, varsayılan limitleri karşılayacak şekilde ölçeklendirdiğinizden emin olmanız gerekir. Kullanım için varsayılan limitlerin ötesinde ölçek varsa, ExpressRoute Premium 'u devre dışı bırakma isteği başarısız olur.

### <a name="can-i-pick-and-choose-the-features-i-want-from-the-premium-feature-set"></a>Premium Özellik kümesinden istediğim özellikleri seçip seçebilir miyim?

Hayır. Özellikleri seçemezsiniz. ExpressRoute Premium 'u açtığınızda tüm özellikleri etkinleştik.

### <a name="how-much-does-expressroute-premium-cost"></a>ExpressRoute Premium maliyeti ne kadar sürer?

Maliyet için [fiyatlandırma ayrıntılarına](https://azure.microsoft.com/pricing/details/expressroute/) bakın.

### <a name="do-i-pay-for-expressroute-premium-in-addition-to-standard-expressroute-charges"></a>ExpressRoute Premium için standart ExpressRoute ücretlerine ek olarak ücret ödemem gerekiyor mu?

Evet. ExpressRoute Premium ücretleri, bağlantı sağlayıcısı tarafından istenen ExpressRoute devre ücretleri ve ücretlerine uygulanır.

## <a name="expressroute-local"></a>ExpressRoute yerel
### <a name="what-is-expressroute-local"></a>ExpressRoute yerel nedir?
ExpressRoute yerel, standart SKU ve Premium SKU 'ya ek olarak ExpressRoute bağlantı hattının bir SKU 'SU. Yerel bir anahtar özelliği olan ExpressRoute eşleme konumundaki yerel bir devrenin, yalnızca aynı metro 'daki veya bu iki Azure bölgesine erişim sağlar. Buna karşılık, standart bir devre, coğrafi olarak tüm Azure bölgelerine ve tüm Azure bölgelerinin küresel bir devresine genel olarak erişmenizi sağlar. 

### <a name="what-are-the-benefits-of-expressroute-local"></a>ExpressRoute yerel 'in avantajları nelerdir?
Standart veya Premium ExpressRoute devreniz için çıkış veri aktarımını ödemeniz gerektiğinde, ExpressRoute yerel devresi için çıkış veri aktarımını ayrı olarak ödemezsiniz. Diğer bir deyişle, ExpressRoute yerel fiyatı, veri aktarımı ücretleri içerir. ExpressRoute yerel, aktarılacak büyük miktarda veriniz varsa daha ekonomik bir çözümdür ve verilerinizi istediğiniz Azure bölgelerindeki bir ExpressRoute eşleme konumuna özel bir bağlantı üzerinden taşıyabilirsiniz. 

### <a name="what-features-are-available-and-what-are-not-on-expressroute-local"></a>Hangi özellikler kullanılabilir ve ExpressRoute yerel üzerinde değil?
Standart bir ExpressRoute devresine kıyasla, yerel bir bağlantı hattı aşağıdakiler dışında aynı özellik kümesine sahiptir:
* Yukarıda açıklandığı gibi Azure bölgelerine erişim kapsamı
* ExpressRoute Global Reach yerel üzerinde kullanılamıyor

ExpressRoute yerel Ayrıca kaynaklar üzerinde aynı sınırlara sahiptir (ör. devre başına VNET sayısı) standart olarak. 

### <a name="where-is-expressroute-local-available-and-which-azure-regions-is-each-peering-location-mapped-to"></a>ExpressRoute yerel olarak kullanılabilir ve hangi Azure bölgelerinin her eşleme konumu ile eşlenir?
ExpressRoute yerel, bir veya iki Azure bölgesinin kapalı olduğu eşleme konumlarında kullanılabilir. Bu eyalet veya ülke veya ülke içinde Azure bölgesi olmayan bir eşleme konumunda kullanılamaz. Lütfen [konumlar sayfasında](expressroute-locations-providers.md)tam eşlemelere bakın.  

## <a name="expressroute-for-office-365"></a>Office 365 için ExpressRoute

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

### <a name="how-do-i-create-an-expressroute-circuit-to-connect-to-office-365-services"></a>Office 365 hizmetlerine bağlanmak için bir ExpressRoute bağlantı hattı oluşturma Nasıl yaparım??

1. Gereksinimleri karşıladığınızdan emin olmak için [ExpressRoute önkoşulları sayfasını](expressroute-prerequisites.md) gözden geçirin.
2. Bağlantı gereksinimlerinizin karşılandığından emin olmak için [ExpressRoute iş ortakları ve konumlar](expressroute-locations.md) makalesindeki hizmet sağlayıcılarının ve konumların listesini gözden geçirin.
3. [Office 365 Için ağ planlama ve performans ayarlamayı](https://aka.ms/tune/)inceleyerek kapasite gereksinimlerinizi planlayın.
4. [Devre sağlama ve devre durumları için bağlantı ExpressRoute iş akışlarını](expressroute-workflows.md)ayarlamak üzere iş akışlarında listelenen adımları izleyin.

> [!IMPORTANT]
> Office 365 hizmetlerine bağlantı yapılandırırken ExpressRoute Premium eklentisini etkinleştirdiğinizden emin olun.
> 
> 

### <a name="can-my-existing-expressroute-circuits-support-connectivity-to-office-365-services"></a>Mevcut ExpressRoute bağlantı hattı Office 365 hizmetlerine yönelik bağlantıyı destekliyor mu?

Evet. Mevcut ExpressRoute bağlantı hattı, Office 365 hizmetlerine bağlantıyı destekleyecek şekilde yapılandırılabilir. Office 365 hizmetlerine bağlanmak için yeterli kapasiteye sahip olduğunuzdan ve Premium eklentisinin etkin olduğundan emin olun. [Office 365 Için ağ planlama ve performans ayarlaması](https://aka.ms/tune/) , bağlantı ihtiyaçlarınızı planlamanıza yardımcı olur. Ayrıca bkz. [bir ExpressRoute bağlantı hattı oluşturma ve değiştirme](expressroute-howto-circuit-classic.md).

### <a name="what-office-365-services-can-be-accessed-over-an-expressroute-connection"></a>ExpressRoute bağlantısı üzerinden hangi Office 365 hizmetlerine erişilebilir?

ExpressRoute üzerinde desteklenen hizmetlerin güncel listesi için [Office 365 URL 'leri ve IP adres aralıkları](https://aka.ms/o365endpoints) sayfasına bakın.

### <a name="how-much-does-expressroute-for-office-365-services-cost"></a>Office 365 hizmetleri maliyeti için ExpressRoute ne kadar sürer?

Office 365 Hizmetleri, Premium eklentisinin etkinleştirilmesini gerektirir. Maliyetler için [fiyatlandırma ayrıntıları sayfasına](https://azure.microsoft.com/pricing/details/expressroute/) bakın.

### <a name="what-regions-is-expressroute-for-office-365-supported-in"></a>Office 365 için ExpressRoute hangi bölgelerde desteklenir?

Bilgi için bkz. [ExpressRoute iş ortakları ve konumları](expressroute-locations.md) .

### <a name="can-i-access-office-365-over-the-internet-even-if-expressroute-was-configured-for-my-organization"></a>Kuruluşum için ExpressRoute yapılandırılmış olsa bile Office 365 ' i Internet üzerinden erişebilir miyim?

Evet. Office 365 hizmet uç noktalarına, ExpressRoute ağınız için yapılandırılmış olsa bile Internet üzerinden erişilebilir. Konumunuzda bulunan ağ, ExpressRoute aracılığıyla Office 365 hizmetlerine bağlanacak şekilde yapılandırıldıysa kuruluşunuzun ağ ekibine başvurun.

### <a name="how-can-i-plan-for-high-availability-for-office-365-network-traffic-on-azure-expressroute"></a>Azure ExpressRoute 'da Office 365 ağ trafiği için yüksek kullanılabilirliği nasıl planlayabilirim?
[Azure ExpressRoute Ile yüksek kullanılabilirlik ve yük devretme](https://aka.ms/erhighavailability) önerilerine bakın

### <a name="can-i-access-office-365-us-government-community-gcc-services-over-an-azure-us-government-expressroute-circuit"></a>Azure US kamu ExpressRoute devresi üzerinden Office 365 ABD kamu topluluğu (GCC) hizmetlerine erişebilir miyim?

Evet. Office 365 GCC hizmet uç noktalarına Azure ABD kamu ExpressRoute aracılığıyla erişilebilir. Ancak, Microsoft 'a tanıtmak istediğiniz önekleri sağlamak için öncelikle Azure portal bir destek bileti açmanız gerekir. Office 365 GCC Hizmetleri bağlantınız, destek bileti çözümlendikten sonra kurulacaktır. 

## <a name="route-filters-for-microsoft-peering"></a>Microsoft eşlemesi için rota filtreleri

### <a name="i-am-turning-on-microsoft-peering-for-the-first-time-what-routes-will-i-see"></a>Microsoft eşlemesini ilk kez kullanıyorum, hangi yollar görüntülenir?

Hiçbir yol görmezsiniz. Ön ek bildirilerini başlatmak için bağlantı hattına bir yol filtresi eklemeniz gerekir. Yönergeler için bkz. [Microsoft eşlemesi için yol filtrelerini yapılandırma](how-to-routefilter-powershell.md).

### <a name="i-turned-on-microsoft-peering-and-now-i-am-trying-to-select-exchange-online-but-it-is-giving-me-an-error-that-i-am-not-authorized-to-do-it"></a>Microsoft eşlemesini etkinleştirdim ve şimdi Exchange Online 'ı seçiyorum, ancak bunu yapmak için yetkilendirilmediğini belirten bir hata veriyor.

Rota filtreleri kullanılırken, tüm müşteriler Microsoft eşlemesini açabilir. Bununla birlikte, Office 365 hizmetlerini tüketmesi için yine de Office 365 ' den yetki almanız gerekir.

### <a name="i-enabled-microsoft-peering-prior-to-august-1-2017-how-can-i-take-advantage-of-route-filters"></a>1 Ağustos 2017 ' den önce Microsoft eşlemeyi etkinleştirdim, yol filtrelerinden nasıl yararlanabilirim?

Mevcut bağlantı hattı, Office 365 ön eklerini duyurarak devam edecektir. Aynı Microsoft eşlemesi üzerinden Azure genel ön ek bildirimleri eklemek istiyorsanız, bir rota filtresi oluşturabilir, tanıtılmak istediğiniz hizmetleri (ihtiyacınız olan Office 365 hizmetleri dahil) seçebilir ve filtreyi Microsoft eşlemenize ekleyebilirsiniz. Yönergeler için bkz. [Microsoft eşlemesi için yol filtrelerini yapılandırma](how-to-routefilter-powershell.md).

### <a name="i-have-microsoft-peering-at-one-location-now-i-am-trying-to-enable-it-at-another-location-and-i-am-not-seeing-any-prefixes"></a>Tek bir konumda Microsoft eşlemem var, şimdi bunu başka bir konumda etkinleştirmeye çalışıyorum ve herhangi bir ön ek görmüyorum.

* 1 Ağustos 2017 ' den önce yapılandırılmış ExpressRoute bağlantı hattı Microsoft eşlemesi, yol filtreleri tanımlanmasa bile Microsoft eşlemesi aracılığıyla tanıtılan tüm hizmet öneklerini alacak.

* 1 Ağustos 2017 ' de veya sonrasında yapılandırılan ExpressRoute devrelerinin Microsoft eşlemesi, bir yol filtresi devresine iliştirilene kadar tanıtılan öneklere sahip olmayacaktır. Varsayılan olarak ön ek yok görürsünüz.

## <a name="expressRouteDirect"></a>ExpressRoute Direct

[!INCLUDE [ExpressRoute Direct](../../includes/expressroute-direct-faq-include.md)]

## <a name="globalreach"></a>Global Reach

[!INCLUDE [Global Reach](../../includes/expressroute-global-reach-faq-include.md)]
