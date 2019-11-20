---
title: Bulut çözüm sağlayıcıları - Azure için ExpressRoute | Microsoft Docs
description: Bu makalede tekliflerini Azure hizmetlerini birleştirmek istediğiniz bulut çözüm sağlayıcıları ve ExpressRoute bilgileri sağlar.
services: expressroute
author: richcar
ms.service: expressroute
ms.topic: article
ms.date: 10/10/2016
ms.author: ricarlse
ms.custom: seodec18
ms.openlocfilehash: a3bd48f32dfcee1a666ff842cfcab2384a5459ec
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849254"
---
# <a name="expressroute-for-cloud-solution-providers-csp"></a>Bulut Çözüm Sağlayıcıları (CSP) için ExpressRoute
Microsoft, geleneksel satıcılar veya dağıtımcıların (CSP), yeni hizmetler geliştirmeye yatırım yapmaya gerek kalmadan müşterileriniz için hızlı bir şekilde yeni hizmetler ve çözümler sağlayabilmesi amacıyla hiper ölçekli hizmetler sağlar. Bulut Çözüm Sağlayıcısının (CSP) bu hizmetleri doğrudan yönetebilmesini sağlamak için Microsoft, CSP’nin Microsoft Azure kaynaklarını müşterilerinizin adına yönetebilmesine olanak sağlayan programlar ve API’ler sunar. Bu kaynaklardan biri de ExpressRoute’dur. ExpressRoute, CSP’nin var olan Azure hizmetlerine bağlanmasına olanak sağlar. ExpressRoute, Azure’daki hizmetlere yüksek hızlı özel iletişim bağlantısıdır. 

ExpressRoute, yüksek kullanılabilirlik için birden fazla müşteri tarafından paylaşılamayan tek bir müşteri aboneliğine bağlı bir çift bağlantı hattından oluşur. Yüksek kullanılabilirliği sürdürmek için her bağlantı hattı farklı bir yönlendiricide sonlandırılmalıdır.

> [!NOTE]
> ExpressRoute’da bant genişliği ve bağlantı sınırları vardır, yani büyük/karmaşık uygulamalar tek bir müşteri için birden fazla bağlantı hattı gerektirir.
> 
> 

Microsoft Azure tarafından sağlanan ve müşterilerinize sunabileceğiniz hizmetlerin sayısı gün geçtikçe artıyor. ExpressRoute, Microsoft Azure ortamına yüksek hızlı ve düşük gecikmeli erişim sağlayarak, sizin ve müşterilerinizin bu hizmetlerden en iyi şekilde yararlanmasına yardımcı olur.

## <a name="microsoft-azure-management"></a>Microsoft Azure yönetimi
Microsoft, CSP’lere kendi hizmet yönetim sistemlerinizle programlı tümleştirme yapmanıza olanak sağlayarak Azure müşteri aboneliklerini yönetmeleri için API’ler sağlar. Desteklenen yönetim özelliklerini [burada](https://msdn.microsoft.com/library/partnercenter/dn974944.aspx) bulabilirsiniz.

## <a name="microsoft-azure-resource-management"></a>Microsoft Azure kaynak yönetimi
Müşterinizle aranızdaki sözleşmeye bağlı olarak aboneliğin nasıl yönetileceği belirlenir. CSP, kaynakların oluşturulmasını ve bakımını doğrudan yönetebilir veya müşteri, Microsoft Azure aboneliğinin kontrolünü sağlayarak Azure kaynaklarını gereksinim duydukları gibi oluşturabilir. Müşteriniz Microsoft Azure aboneliklerinde kaynak oluşturmayı yönetirse şu iki modelden birini kullanır: "Doğrudan*bağlantı*" modeli veya "*doğrudan*" modeli. Bu modeller aşağıdaki bölümlerde ayrıntılı olarak açıklanmıştır.  

### <a name="connect-through-model"></a>Aracılı bağlantı modeli
![alternatif metin](./media/expressroute-for-cloud-solution-providers/connect-through.png)  

Aracılı bağlantı modelinde CSP, veri merkeziniz ile müşterinizin Azure aboneliği arasında doğrudan bir bağlantı oluşturur. Bu doğrudan bağlantı, ExpressRoute kullanılarak oluşturulur ve ağınızı Azure’a bağlar. Ardından, müşteriniz ağınıza bağlanır. Bu senaryo, müşterinin Azure hizmetlerine ulaşmak için CSP ağından geçmesini gerektirir. 

Müşterinizin sizin tarafınızdan yönetilmeyen başka Azure abonelikleri varsa, CSP olmayan aboneliklerinde sağlanan hizmetlere bağlanmak için ortak İnterneti veya kendi özel bağlantılarını kullanırlar. 

CSP tarafından yönetilen Azure hizmetlerinde CSP’nin daha önce oluşturulmuş bir müşteri kimliği deposuna sahip olduğu varsayılır; daha sonra bu depo, CSP aboneliklerinin Yerine Yönetim (AOBO) üzerinden yönetilmesi için Azure Active Directory’ye çoğaltılır. Bu senaryo için temel faktörler şunlardır: Belirli bir iş ortağı veya hizmet sağlayıcısı, müşteriyle ilişki içinde. Burada müşteri, sağlayıcı hizmetlerini zaten kullanıyor veya iş ortağı, müşteriye hem sağlayıcı tarafından barındırılan çözümleri hem de Azure tarafından barındırılan çözümleri sunarak esneklik sağlamak ve CSP’nin tek başına çözemeyeceği müşteri sorunlarını çözmek istiyor. Bu model aşağıdaki **Şekilde** gösterilmiştir.

![alternatif metin](./media/expressroute-for-cloud-solution-providers/connect-through-model.png)

### <a name="connect-to-model"></a>Doğrudan bağlantı modeli
![alternatif metin](./media/expressroute-for-cloud-solution-providers/connect-to.png)

Doğrudan bağlantı modelinde hizmet sağlayıcısı, müşterinin veri merkezi ile müşterinin (müşteri) ağı üzerinden ExpressRoute kullanılarak CSP tarafından sağlanan Azure abonelikleri arasında doğrudan bir bağlantı oluşturur.

> [!NOTE]
> ExpressRoute için müşterinin ExpressRoute bağlantı hattını oluşturması ve yönetmesi gerekir.  
> 
> 

Bu bağlantı senaryosu, müşterinin CSP ile yönetilen Azure aboneliğine doğrudan bir müşteri ağı üzerinden; tümüyle veya kısmen müşterinin oluşturduğu, sahip olduğu ve yönettiği bir doğrudan ağ kullanarak bağlanmasını gerektirir. Bu müşteriler için sağlayıcının daha önce oluşturulmuş bir müşteri kimliği deposuna sahip olmadığı ve sağlayıcının müşteriye aboneliğinin AOBO üzerinden yönetilmesi için var olan kimlik deposunu Azure Active Directory’ye çoğaltmasında yardımcı olacağı varsayılır. Bu senaryo için temel etkenler; belirtilen iş ortağı ya da hizmet sağlayıcının müşteriyle iletişimi olduğu, müşterinin şu anda hizmet sağlayıcının hizmetlerini kullandığı ya da iş ortağının mevcut bir sağlayıcı veri merkezi ya da altyapısına ihtiyaç duymadan sadece Azure’de bulunan çözümleri sunma isteği duyduğu durumları içerir.

![alternatif metin](./media/expressroute-for-cloud-solution-providers/connect-to-model.png)

Bu iki seçenekten hangisinin seçileceği, müşterinizin gereksinimlerine ve şu anda sağlamanız gereken Azure hizmet ihtiyaçlarına göre belirlenir. Bu modellere ve ilişkili rol tabanlı erişim denetimine, ağlara ve kimlik tasarımı desenlerine ilişkin ayrıntılar aşağıdaki linklerde ele alınmaktadır:

* **Rol Tabanlı Erişim Denetimi (RBAC)** – RBAC, Azure Active Directory’i temel alır.  Azure RBAC hakkında daha fazla bilgi için [buraya](../role-based-access-control/role-assignments-portal.md) bakın.
* **Ağlar** – Microsoft Azure’da ağlarla ilgili çeşitli konuları kapsar.
* **Azure Active Directory (Azure AD)** – Azure AD, Microsoft Azure’da ve üçüncü taraf SaaS uygulamalarında kimlik yönetimini sağlar. Azure AAD hakkında daha fazla bilgi için [ buraya](https://azure.microsoft.com/documentation/services/active-directory/) bakın.  

## <a name="network-speeds"></a>Ağ hızları
ExpressRoute, 50 Mb/s ile 10Gb/s arası ağ hızlarını destekler. Bu, müşterilerin benzersiz ortamları için ihtiyaç duydukları miktarda ağ bant genişliğini satın almalarına olanak sağlar.

> [!NOTE]
> Ağ bant genişliği, gerektiğinde iletişimleri bozmadan artırılabilir ancak ağ hızını düşürmek için bağlantı hattının kaldırılması ve daha düşük ağ hızında yeniden oluşturulması gerekir.  
> 
> 

ExpressRoute, yüksek hızlı bağlantıların daha iyi kullanımı için birden fazla sanal ağın tek bir ExpressRoute bağlantı hattına bağlanmasını destekler. Tek bir ExpressRoute bağlantı hattı, aynı müşteriye ait birden fazla Azure aboneliği arasında paylaşılabilir.

## <a name="configuring-expressroute"></a>ExpressRoute’u yapılandırma
ExpressRoute, tek bir ExpressRoute bağlantı hattı üzerinden üç tür trafiği ([yönlendirme etki alanları](#expressroute-routing-domains)) destekleyecek şekilde yapılandırılabilir. Bu trafik; Microsoft eşliği, Azure genel eşliği ve özel eşliği olarak ayrılır. Tek bir ExpressRoute bağlantı hattı üzerinden gönderilmek üzere trafik türlerinden birini veya hepsini seçebilirsiniz veya ExpressRoute bağlantı hattının boyutuna ve müşteriniz tarafından gerekli görülen yalıtıma bağlı olarak birden çok ExpressRoute bağlantı hattı kullanabilirsiniz. Müşterinizin güvenlik yaklaşımı, genel ve özel trafiğin aynı bağlantı hattı üzerinden çapraz geçiş yapmalarına izin vermeyebilir.

### <a name="connect-through-model"></a>Aracılı bağlantı modeli
Aracılı bağlantı yapılandırmasında, tüm ağ desteklerinin müşterinizin veri merkezi kaynaklarını Azure’de barındırılan aboneliklere bağlamasından sorumlu olacaksınız. Azure özelliklerini kullanmak isteyen müşterilerinizin her birinin kendi ExpressRoute bağlantısına ihtiyacı olacak ve bu bağlantılar tarafınızdan yönetilecek. Müşterinin ExpressRoute bağlantı hattını edinmede yararlanacağı aynı yöntemleri kullanacaksınız. Bağlantı hattı hazırlama ve bağlantı hattı durumları için [ExpressRoute iş akışları](expressroute-workflows.md) makalesinde özetlenen aynı adımları izleyeceksiniz. Daha sonra Sınır Ağ Geçidi (BGP) protokolü yollarını yapılandırarak şirket içi ağ ve Azure sanal ağı arasındaki trafiği kontrol edeceksiniz.

### <a name="connect-to-model"></a>Doğrudan bağlantı modeli
Doğrudan bağlantı yapılandırmasında müşterinizin Azure ile zaten bir bağlantısı vardır ya da müşteriniz, sizin veri merkezinizden değil, kendi veri merkezinden Azure’a ExpressRoute İnternet servis sağlayıcısına bir bağlantı oluşturacaktır. Hazırlama sürecine başlamak için müşteriniz, yukarıda, Aracılı Bağlantı modeli bölümünde belirtilen adımları takip edecektir. Bağlantı hattı kurulduktan sonra müşterinizin, kendi ağınıza ve Azure sanal ağına bağlanabilmek için şirket içi yönlendiricileri yapılandırması gerekecektir.

Yolların kendi veri merkez(ler)inizdeki kaynakların, kendi veri merkezinizdeki müşteri kaynaklarıyla ya da Azure’de barındırılan kaynaklarla iletişim kurabilmesi için bağlantıyı kurma ve yolları yapılandırma sürecinde yardımcı olabilirsiniz.

## <a name="expressroute-routing-domains"></a>ExpressRoute yönlendirme etki alanları
ExpressRoute üç yönlendirme etki alanı sunar: Genel, özel ve Microsoft eşlemesi. Her bir yönlendirme etki alanına, yüksek kullanılabilirlik için aynı yönlendiricilerle aktif-aktif yapılandırma tanımlanır. ExpressRoute yönlendirme etki alanları hakkında daha fazla ayrıntı için [buraya](expressroute-circuit-peerings.md) göz atın.

Sadece istediğiniz veya ihtiyaç duyduğunuz yol(lar)a izin verecek özel yol filtreleri tanımlayabilirsiniz. Daha fazla bilgi veya bu değişikliklerin nasıl yapılacağını görmek için bkz. makalesi: Yönlendirme filtreleri hakkında daha fazla ayrıntı için [PowerShell kullanarak bir ExpressRoute bağlantı hattı için yönlendirme oluşturma ve değiştirme](expressroute-howto-routing-classic.md) .

> [!NOTE]
> Microsoft Eşlemesi ve Genel Eşleme için bağlantı, müşteri ya da CSP tarafından sahip olunan genel bir IP adresinden sağlanmalıdır ve tüm tanımlı kurallara uymalıdır. Daha fazla bilgi için bkz. [ExpressRoute ](expressroute-prerequisites.md).  
> 
> 

## <a name="routing"></a>Yönlendirme
ExpressRoute, Azure ağlarına Azure Virtual Network Gateway üzerinden bağlanır. Ağ geçitleri, Azure sanal ağlarına yönlendirme sağlar.

Azure Sanal Ağları’nı oluşturma, ayrıca Sanal Ağdan doğrudan trafiğe çift yönlü sanal ağ alt ağı için varsayılan bir yönlendirme tablosu oluşturur. Varsayılan rota tablosu çözüm için yetersiz kalıyorsa, giden trafiği özel uygulamalara yönlendirecek ya da yolların özel alt ağlara ya da dış ağlara erişimini engelleyecek özel yollar oluşturulabilir.

### <a name="default-routing"></a>Varsayılan yönlendirme
Varsayılan rota tablosu aşağıdaki rotaları içerir:

* Bir alt ağ içinde yönlendirme
* Sanal ağ içinde alt ağdan alt ağa yönlendirme
* İnternet’e yönlendirme
* VPN ağ geçidi kullanarak sanal ağdan sanal ağa yönlendirme
* Bir VPN ya da ExpressRoute ağ geçidi kullanarak sanal ağdan şirket içi ağına yönlendirme

![alternatif metin](./media/expressroute-for-cloud-solution-providers/default-routing.png)  

### <a name="user-defined-routing-udr"></a>Kullanıcı tanımlı yönlendirme (UDR)
Kullanıcı tanımlı yollar, sanal ağda veya diğer önceden tanımlanmış ağ geçitlerinde (ExpressRoute; İnternet veya VPN) tanımlanmış alt ağdan diğer alt ağlara giden trafik akışının kontrolüne olanak tanır. Varsayılan sistem yönlendirme tablosu, varsayılan sistem yönlendirme tablosunu özel yollarla değiştiren kullanıcı tanımlı yönlendirme tablosu ile değiştirilebilir. Kullanıcı tanımlı yönlendirme ile müşteriler güvenlik duvarları gibi uygulamalara veya izinsiz giriş algılama gereçlerine belirli yollar oluşturabilir veya belirli alt ağlara kullanıcı tanımlı yolu barındıran alt ağdan erişimi engeller. Kullanıcı Tanımlı Yollara genel bir bakış için [buraya](../virtual-network/virtual-networks-udr-overview.md) göz atın. 

## <a name="security"></a>Güvenlik
Doğrudan veya Aracılı bağlantı modellerinden hangisinin kullanıldığına bağlı olarak, müşteriniz kendi sanal ağında güvenlik ilkeleri tanımlar ya da CSP’ye kendi sanal ağlarına tanımlaması için güvenlik ilkesi gereksinimleri sağlar. Aşağıdaki güvenlik ölçütleri tanımlanabilir:

1. **Müşteri Yalıtımı** — Azure platformu, Müşteri Kimliği ve sanal ağ bilgisini her müşterinin trafik bilgisini bir GRE tünelinde yalıtan güvenli bir veritabanında saklayarak müşteri yalıtımı sağlar.
2. **Ağ Güvenlik Grubu (NSG)** kuralları, izin verilen trafiğin Azure içindeki alt ağlara çift yönlü olarak tanımlanmasında kullanılan kurallardır. NSG, varsayılan olarak, İnternet’ten sanal ağa trafik akışını engelleyecek Engelleme kuralları içerir ve sanal ağ içinde trafik akışı kurallarına izin verir. Ağ Güvenlik Grupları hakkında daha fazla bilgi için [buraya](https://azure.microsoft.com/blog/network-security-groups/) göz atın.
3. **Zorlamalı tünel** — Bu seçenek, Azure’den kaynaklanan internete bağlı trafiği, şirket içi veri merkezine ExpressRoute bağlantısı üzerinden yeniden yönlendirir. Zorlamalı tünel görünümü hakkında daha fazla bilgi için[buraya](expressroute-routing.md#advertising-default-routes) göz atın.  
4. **Şifreleme** — ExpressRoute bağlantı hatları belirli bir müşteriye ayrılmış olsa da, ağ sağlayıcısının ihlal edilme olasılığı bulunmaktadır ve bu, izinsiz giriş yapanların paket trafiğini incelemesi riskini doğurur. Bir müşteri veya CSP, şirket içi kaynaklar ile Azure kaynakları arasında akan tüm trafik için IPSec tünel modu ilkeleri tanımlayarak bağlantı üzerinden trafiği şifreleyebilir (Şekil 1 için isteğe bağlı tünel modu IPSec 'e bakın) e ExpressRoute güvenliği, üstü). İkinci seçenek ise ExpressRoute bağlantı hattının her ucunda bir güvenlik duvarı uygulaması kullanmak olabilir. Bu, ExpressRoute bağlantı hattının tüm trafiğini şifrelemek için her iki uca 3. parti güvenlik duvarı sanal makineleri/uygulamaları kurulmasını gerektirecektir.

![alternatif metin](./media/expressroute-for-cloud-solution-providers/expressroute-security.png)  

## <a name="next-steps"></a>Sonraki adımlar
Bulut Çözümü Sağlayıcısı hizmeti, sizlere pahalı altyapı ve özelliklere ihtiyaç duymadan müşterilerinizin gözünde değerinizi artıracak bir yol sunarken, ana dış kaynak sağlayıcısı olarak konumunuzu korur. Microsoft Azure ile sorunsuz tümleştirme, Microsoft Azure yönetimini mevcut yönetim altyapınızın sınırları içinde tümleştirecek CSP API’si aracılığıyla gerçekleştirilebilir.  

Aşağıdaki bağlantılarda ek bilgiler bulunabilir:

[Azure Bulut Çözümü Sağlayıcısı programı](https://docs.microsoft.com/azure/cloud-solution-provider).  
[Bir Bulut Çözümü Sağlayıcısı olmaya hazırlanma](https://partner.microsoft.com/en-us/solutions/cloud-reseller-pre-launch).  
[Microsoft Bulut Çözümü Sağlayıcısı kaynakları](https://partner.microsoft.com/en-us/solutions/cloud-reseller-resources).
