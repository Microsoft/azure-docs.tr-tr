---
title: 'Azure ExpressRoute: yönlendirmeyi Iyileştirme'
description: Bu sayfa, Microsoft ve şirket ağınız arasında bağlanan birden fazla ExpressRoute devresine sahip olduğunda yönlendirmenin nasıl iyileştirileceği hakkında ayrıntılı bilgi sağlar.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: conceptual
ms.date: 07/11/2019
ms.author: charwen
ms.openlocfilehash: dcbae103933167c583bf0f73dc2fa09178c38bd5
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74080129"
---
# <a name="optimize-expressroute-routing"></a>ExpressRoute Yönlendirmeyi En iyi Duruma Getirme
Birden çok ExpressRoute bağlantı hattına sahip olduğunuzda, Microsoft'a bağlanmak için birden fazla yolunuz vardır. Sonuç olarak, yetersiz yönlendirme olabilir, diğer bir deyişle, trafiğinizin Microsoft’a ulaşması ve Microsoft’un ağınıza ulaşması daha uzun bir yol alabilir. Ne kadar uzun ağ yolu, o kadar yüksek gecikme. Gecikmenin uygulama performansı ve kullanıcı deneyimi üzerinde doğrudan etkisi vardır. Bu makale, bu sorunu gösterir ve standart yönlendirme teknolojilerini kullanarak yönlendirmenin nasıl iyileştirileceğini açıklar.

## <a name="path-selection-on-microsoft-and-public-peerings"></a>Microsoft ve genel eşlerde yol seçimi
Bir ya da daha fazla ExpressRoute devreniz varsa ve Internet 'e bir Internet Exchange (x) veya Internet servis sağlayıcısı (ISS) aracılığıyla Internet 'e giden yollar varsa, trafiğin istenen yolun üzerine akmasını sağlamak önemlidir. BGP, en uzun ön ek eşleşmesi (LPM) dahil olmak üzere bir dizi etkene dayanarak en iyi yol seçim algoritmasını kullanır. Microsoft veya genel eşleme aracılığıyla Azure 'a yönelik trafiğin ExpressRoute yolundan geçiş yapıldığından emin olmak için, müşterilerin ExpressRoute üzerinde her zaman tercih edildiğini sağlamak üzere *yerel tercih* özniteliğini uygulaması gerekir. 

> [!NOTE]
> Varsayılan yerel tercih genellikle 100 ' dir. Daha yüksek yerel Tercihler daha tercih edilir. 
>
>

Aşağıdaki örnek senaryoyu göz önünde bulundurun:

![ExpressRoute Vaka 1 sorunu: Müşteriden Microsoft’a yetersiz yönlendirme](./media/expressroute-optimize-routing/expressroute-localPreference.png)

Yukarıdaki örnekte, ExpressRoute yollarını tercih etmek için aşağıdaki gibi yerel tercihi yapılandırın. 

**R1 perspektifinden Cisco IOS-XE yapılandırması:**

    R1(config)#route-map prefer-ExR permit 10
    R1(config-route-map)#set local-preference 150

    R1(config)#router BGP 345
    R1(config-router)#neighbor 1.1.1.2 remote-as 12076
    R1(config-router)#neighbor 1.1.1.2 activate
    R1(config-router)#neighbor 1.1.1.2 route-map prefer-ExR in

**R1 perspektifinden Junos yapılandırması:**

    user@R1# set protocols bgp group ibgp type internal
    user@R1# set protocols bgp group ibgp local-preference 150



## <a name="suboptimal-routing-from-customer-to-microsoft"></a>Müşteriden Microsoft’a yetersiz yönlendirme
Bir örnekle yönlendirme problemine daha yakından bir bakalım. Biri Los Angeles ve diğeri New York’ta olmak üzere ABD’de iki ofisiniz olduğunu düşünün. Ofisleriniz, kendi omurga ağınız veya hizmet sağlayıcınızın IP VPN’i olabilen bir Geniş Alan Ağı (WAN) üzerine bağlıdır. Ayrıca, WAN’da bağlı biri ABD Batı ve diğeri ABD Doğu’da olmak üzere iki ExpressRoute bağlantı hattınız vardır. Belli ki, Microsoft ağına bağlanmak için iki yolunuz vardır. Şimdi, hem ABD Batı hem de ABD Doğu’da Azure dağıtımına (örneğin, Azure Uygulama Hizmeti) sahip olduğunuzu düşünün. Hizmet yöneticiniz en iyi deneyimler için her ofisteki kullanıcıların yakınlardaki Azure hizmetlerine erişimini tanıttığından, amacınız Los Angeles’taki kullanıcılarınızı Azure ABD Batı’ya ve New York’taki kullanıcılarınızı Azure ABD Doğu’ya bağlamaktır. Ne yazık ki, doğu yakası kullanıcıları için plan yolunda giderken batı yakası kullanıcıları için yolunda değildir. Sorunun nedeni aşağıda verilmiştir. Her bir ExpressRoute bağlantı hattında, hem Azure ABD Doğu (23.100.0.0/16) hem de Azure ABD Batı’daki (13.100.0.0/16) önekini size tanıtırız. Hangi önekin hangi bölgeden olduğunu bilmiyorsanız, farklı şekilde ele almanız mümkün değildir. WAN ağınız her iki önekin de ABD Batı’dan daha çok ABD Doğu’ya yakın olduğunu düşünebilir ve bu nedenle, iki ofis kullanıcılarını da ABD Doğu’daki ExpressRoute bağlantı hattına yönlendirebilir. Sonunda, Los Angeles ofisinde birçok mutsuz kullanıcınız olacaktır.

![ExpressRoute Vaka 1 sorunu: Müşteriden Microsoft’a yetersiz yönlendirme](./media/expressroute-optimize-routing/expressroute-case1-problem.png)

### <a name="solution-use-bgp-communities"></a>Çözüm: BGP Toplulukları’nı kullanın
Her iki ofis kullanıcıları için yönlendirmeyi en iyi hale getirmek için, hangi önekin Azure Batı ABD ve Azure Doğu ABD olduğunu bilmeniz gerekir. Bu bilgileri [BGP Topluluğu değerlerini](expressroute-routing.md) kullanarak kodlarız. Her bir Azure bölgesine benzersiz bir BGP topluluk değeri atadık. Örneğin, ABD Batı için "12076:51004", "12076:51006" ABD Doğu. Artık hangi önekin hangi Azure bölgesinden olduğunu bildiğinize göre, tercih edilmesi gereken ExpressRoute bağlantı hattını yapılandırabilirsiniz. Yönlendirme bilgilerinin değişimi için BGP kullandığımızdan, yönlendirmeyi etkilemek için BGP'nin Yerel Tercihini kullanabilirsiniz. Bizim örneğimizde, ABD Batı’daki 13.100.0.0/16 için ABD Doğu’dan daha yüksek bir yerel tercih değeri atayabilir ve benzer şekilde, ABD Doğu’daki 23.100.0.0/16 için ABD Batı’dan daha yüksek bir yerel tercih değeri atayabilirsiniz. Bu yapılandırma her iki Microsoft yolunun da kullanılabilir olduğunda, New York’taki kullanıcılarınız ABD Doğu’dan Azure ABD Doğu’ya ExpressRoute bağlantı hattını kullanırken Los Angeles’taki kullanıcılarınızın Azure ABD Batı’ya bağlanması için ABD Batı’daki ExpressRoute bağlantı hattının kullanıldığından emin olacaktır. Her iki tarafta da yönlendirme en iyi duruma getirilmiştir. 

![ExpressRoute Vaka 1 çözümü: BGP Toplulukları’nı kullanın](./media/expressroute-optimize-routing/expressroute-case1-solution.png)

> [!NOTE]
> Yerel Tercih kullanılarak aynı teknik müşteriden Azure Sanal Ağına yönlendirme için uygulanabilir. BGP Topluluğu değeri, Azure’dan ağınıza tanıtılan ön eklere etiketlenmez. Ancak, ofisinize en yakın Sanal Ağ dağıtımını siz bildiğiniz için, bir ExpressRoute devresini diğerine tercih etmek üzere yönlendiricilerinizi yapılandırabilirsiniz.
>
>

## <a name="suboptimal-routing-from-microsoft-to-customer"></a>Microsoft'tan müşteriye yetersiz yönlendirme
Burada Microsoft bağlantılarının ağınıza ulaşmasına için daha uzun bir yol aldığı başka bir örnek gösterilmektedir. Bu durumda, [karma bir ortamda](https://technet.microsoft.com/library/jj200581%28v=exchg.150%29.aspx) şirket içi Exchange sunucularını ve Exchange Online kullanın. Ofisleriniz bir WAN’a bağlıdır. İki ExpressRoute bağlantı hattı aracılığıyla her iki ofisinizdeki şirket için sunucuların öneklerini Microsoft’a tanıtırsınız. Exchange Online, posta kutusu geçişi gibi durumlarda şirket içi sunuculara bağlantılar başlatır. Ne yazık ki, Los Angeles ofisinizin bağlantısı batı yakasına dönerken tüm kıtadan geçmeden önce ABD Doğu’daki ExpressRoute bağlantı hattına yönlendirilir. Sorunun nedeni birinciye benzemektedir. Herhangi bir ipucu olmadan, Microsoft ağı hangi müşteri önekinin ABD Doğu'ya veya ABD Batı’ya yakın olduğunu bildiremez. Los Angeles’taki ofisiniz için yanlış yolu seçmiş olabilirsiniz.

![ExpressRoute Vaka 2: Microsoft'tan müşteriye yetersiz yönlendirme](./media/expressroute-optimize-routing/expressroute-case2-problem.png)

### <a name="solution-use-as-path-prepending"></a>Çözüm: AS YOLU eklenmesini kullanın
Bu sorunun iki çözümü vardır. Birinci yol Los Angeles ofisiniz için ABD Batı’daki ExpressRoute bağlantı hattındaki 177.2.0.0/31 şirket içi öneki ve New York ofisiniz için ABD Doğu’daki ExpressRoute bağlantı hattındaki 177.2.0.2/31 şirket içi önekini tanıtmanızdır. Sonuç olarak, Microsoft’un ofislerinizin her birine bağlanması için yalnızca bir yol vardır. Belirsizlik olmaz ve yönlendirme en iyi duruma getirilir. Bu tasarımla, yük devretme stratejinizi düşünmeniz gerekir. ExpressRoute aracılığıyla Microsoft yolunun bozuk olması durumunda, Exchange Online’ın yine de şirket içi sunucularına bağlandığından emin olmanız gerekir. 

İkinci çözüm, her iki ExpressRoute bağlantı hattında iki öneki de tanıtmaya devam etmeniz ve buna ek olarak, hangi önekin hangi ofisinize daha yakın olduğu ile ilgili bize ipucu vermenizdir. BGP AS Yolu eklenmesini desteklediğimizden, yönlendirmeyi etkilemek için önekiniz için AS Yolu’nu yapılandırabilirsiniz. Bu örnekte, bu önek (ağımız bu önek yolunun batıdan daha kısa olacağını düşüneceğinden) için hedeflenen trafikte ABD Batı’daki ExpressRoute bağlantı hattını tercih edeceğimizden, ABD Doğu’daki 172.2.0.0/31 AS YOLU’nu uzatabilirsiniz. Benzer şekilde, ABD Doğu ExpressRoute bağlantı hattını tercih edeceğimiz için, ABD Batı’daki 172.2.0.2/31 için AS YOLU’nu uzatabilirsiniz. Her iki ofis için de yönlendirme en iyi duruma getirilmiştir. Bu tasarımla, bir ExpressRoute bağlantı hattı bozuk ise, Exchange Online başka bir ExpressRoute bağlantı hattı ve WAN’ınız aracılığıyla yine de size ulaşabilir. 

> [!IMPORTANT]
> Özel bir AS numarası kullanarak eşleme yaparken Microsoft eşlemelerinde alınan öneklerin AS YOLUNDAKI özel AS numaralarını kaldırdık. Microsoft eşlemesi için yönlendirmeyi etkilemek üzere AS yolunda ortak bir AS ile eş ve genel AS numaraları eklemeniz gerekir.
> 
> 

![ExpressRoute Vaka 2 çözümü: AS YOLU eklenmesini kullanın](./media/expressroute-optimize-routing/expressroute-case2-solution.png)

> [!NOTE]
> Burada verilen örnekler Microsoft ve Ortak eşlemelere yöneliktir, ancak Özel eşleme için de aynı özellikleri destekleriz. Ayrıca AS Yolu ekleme, birincil ve ikincil yolların seçimini etkilemek için tek bir ExpressRoute devresi içinde çalışır.
> 
> 

## <a name="suboptimal-routing-between-virtual-networks"></a>Sanal ağlar arasında yetersiz yönlendirme
ExpressRoute ile Sanal Ağdan Sanal Ağa (aynı zamanda "VNet" olarak bilinir) iletişimi bir ExpressRoute devresine bağlayarak etkinleştirebilirsiniz. Birden fazla ExpressRoute devresine bağladığınızda, sanal ağlar arasında yetersiz yönlendirme gerçekleşebilir. Bir örnek düşünelim. Biri ABD Batı ve diğeri ABD Doğu’da olmak üzere iki ExpressRoute devreniz vardır. Her bölgede, iki sanal ağınız vardır. Web sunucularınız bir sanal ağa, uygulama sunucularınız diğerine dağıtılmaktadır. Artıklık için her bölgedeki iki sanal ağı hem yerel ExpressRoute devresine hem de uzak ExpressRoute devresine bağlarsınız. Aşağıda görülebileceği gibi, her sanal ağdan diğer sanal ağa iki yol vardır. Sanal ağlar, hangi ExpressRoute devresinin yerel, hangisinin uzak olduğunu bilmez. Sonuç olarak, sanal ağlar arası yük dengelemesi için Eşit Maliyetli Çoklu Yol (ECMP) yönlendirmesi yaptıkları için, bazı trafik akışları daha uzun yoldan gider ve uzak ExpressRoute devresine yönlendirilir.

![ExpressRoute Vaka 3: Sanal ağlar arasında yetersiz yönlendirme](./media/expressroute-optimize-routing/expressroute-case3-problem.png)

### <a name="solution-assign-a-high-weight-to-local-connection"></a>Çözüm: yerel bağlantıya yüksek ağırlık atayın
Çözüm basittir. Sanal ağların ve bağlantı hatlarının nerede olduğunu bildiğiniz için, her bir sanal ağın hangi yolu tercih etmesi gerektiğini siz belirleyebilirsiniz. Özellikle bu örnek için, yerel bağlantıya atadığınız ağırlık, uzak bağlantıya atadığınız ağırlıktan daha yüksek olmalıdır ([buradaki](expressroute-howto-linkvnet-arm.md#modify-a-virtual-network-connection) yapılandırma örneğine bakın). Bir sanal ağ, birden fazla bağlantıda diğer sanal ağın ön ekini alırsa, bu ön eki hedefleyen trafiği göndermek için ağırlık değeri en fazla olan bağlantıyı tercih eder.

![ExpressRoute Vaka 3 çözümü - yerel bağlantıya yüksek ağırlık atayın](./media/expressroute-optimize-routing/expressroute-case3-solution.png)

> [!NOTE]
> Ayrıca, birden fazla ExpressRoute devreniz varsa, yukarıdaki örnekte açıklanan AS YOLU eklenme tekniğini uygulamak yerine ağırlığı yapılandırarak sanal ağınızdan şirket içi ağınıza yönlendirmeyi etkileyebilirsiniz. Her ön ek için, trafiğin nasıl gönderileceğine karar verirken AS Yolu uzunluğundan önce mutlaka bağlantı ağırlığına bakmanız gerekir.
>
>
