---
title: Azure 'un uygulama teslim paketiyle Azure ön kapısının yük dengelemesi | Microsoft Docs
description: Bu makale, Azure 'un uygulama teslim paketiyle yük dengelemeyi nasıl önerdiği hakkında bilgi edinmenize yardımcı olur
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/06/2021
ms.author: duau
ms.openlocfilehash: 0a7e81c57552fdc24262522343a08fdabba71bfd
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106552589"
---
# <a name="load-balancing-with-azures-application-delivery-suite"></a>Azure uygulama teslim paketiyle yük dengeleme

## <a name="introduction"></a>Giriş
Microsoft Azure, ağ trafiğinizin nasıl dağıtılacağını ve yük dengelenmesi yönetmek için çeşitli küresel ve bölgesel hizmetler sağlar: 

* Application Gateway
* Front Door 
* Load Balancer  
* Traffic Manager

Azure 'un pek çok bölgesi ve bölgesel mimarisiyle birlikte, bu hizmetlerin birlikte kullanılması sağlam, ölçeklenebilir ve yüksek performanslı uygulamalar oluşturmanıza olanak sağlayabilir.

:::image type="content" source="./media/front-door-lb-with-azure-app-delivery-suite/application-delivery-figure1.png" alt-text="Uygulama teslim paketi":::
 
Bu hizmetler iki kategoride bozulur:
1. Traffic Manager ve ön kapı gibi **küresel Yük Dengeleme Hizmetleri** , son kullanıcılarınızın bulut genelindeki ve hatta karma şirket içi hizmetlerinizin yanı sıra bölgesel arka uçlarınız genelinde trafiği dağıtır. Küresel Yük Dengeleme, trafiğinizi en yakın hizmet arka ucunuza yönlendirir ve kullanıcılarınız için her zaman açık kullanılabilirlik ve yüksek performans sağlamak üzere hizmet güvenilirliğinde değişikliklere tepki verir. 
1. Yük dengeleyiciler ve uygulama ağ geçitleri gibi **Bölgesel Yük Dengeleme Hizmetleri** , bir bölgedeki sanal ağ (VNet) veya hizmet uç noktaları içindeki sanal makinelere (VM) trafik dağıtma yeteneği sağlar.

Bu genel ve bölgesel Hizmetleri birleştirdiğinizde, uygulamanız son kullanıcılarınızı IaaS, PaaS veya şirket içi hizmetlerinize gönderilen güvenilir ve güvenli uçtan uca trafikten yararlanır. Sonraki bölümde, bu hizmetlerin her birini açıklıyoruz.

## <a name="global-load-balancing"></a>Küresel Yük Dengeleme
**Traffic Manager** genel DNS yük dengelemesi sağlar. Müşterinin seçtiği yönlendirme ilkesini izleyerek gelen DNS isteklerine bakar ve sağlıklı bir arka uca yanıt verir. Yönlendirme yöntemleri seçenekleri şunlardır:
- **Performans yönlendirme** istekleri en düşük gecikme süresine sahip en yakın arka uca gönderir.
- **Öncelik yönlendirme** , tüm trafiği yedekleme olarak diğer arka uçlarla birlikte arka uca yönlendirir.
- **Ağırlıklı hepsini bir kez deneme yönlendirmesi** , her bir arka uca atanan ağırlığa göre trafiği dağıtır.
- **Coğrafi yönlendirme** , belirli coğrafi bölgelerden kaynakların kaynağını elde eden isteklerin bu bölgeler için eşlenmiş arka uçları tarafından işlenmesini sağlar. (Örneğin, Ispanya 'dan gelen tüm istekler Fransa Orta Azure bölgesine yönlendirilmelidir)
- **Alt ağ yönlendirme** , IP adresi aralıklarını arka uçlara eşlemenizi sağlar. böylece, bu IP 'ler için gelen isteklerin belirli arka uca gönderilmesi gerekir. (Örneğin, kurumsal HQ 'ın IP adresi aralığından bağlanan tüm kullanıcılar genel kullanıcılardan farklı web içerikleri almalıdır)

İstemci doğrudan bu arka uca bağlanır. Azure Traffic Manager, bir arka ucun sağlıksız olduğunu algılar ve sonra istemcileri başka bir sağlıklı örneğe yönlendirir. Hizmet hakkında daha fazla bilgi edinmek için [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) belgelerine bakın.

**Azure ön kapısı** , genel HTTP yük dengelemesi dahil olmak üzere dinamik Web sitesi HıZLANDıRMA (DSA) sağlar.  Gelen HTTP isteklerine, belirtilen ana bilgisayar adı, URL yolu ve yapılandırılmış kuralların en yakın hizmet arka ucuna/bölgesine bakar.  
Ön kapı, Microsoft 'un ağındaki HTTP isteklerini sonlandırır ve uygulama ya da altyapı durumunu veya gecikme süresini algılamak üzere etkin bir şekilde araştırmalar.  Ön kapı daha sonra her zaman trafiği en hızlı ve kullanılabilir (sağlıklı) arka uca yönlendirir. Hizmet hakkında daha fazla bilgi edinmek için ön kapıların [yönlendirme mimarisi](front-door-routing-architecture.md) ayrıntılarına ve [trafik yönlendirme yöntemlerine](front-door-routing-methods.md) bakın.

## <a name="regional-load-balancing"></a>Bölgesel yük dengeleme
Application Gateway, uygulamanız için çeşitli katman 7 yük dengeleme özellikleri sunan bir hizmet olarak uygulama teslim denetleyicisi (ADC) sağlar. Müşterilerin, CPU yoğun TLS sonlandırmasını uygulama ağ geçidine devrederek, Web grubu üretkenliğini iyileştirmelerine olanak tanır. Diğer ek katman 7 yönlendirme özellikleri, gelen trafik, tanımlama bilgisi tabanlı oturum benzeşimi, URL yolu tabanlı Yönlendirme ve tek bir Application Gateway arkasında birden fazla Web sitesini barındırma yeteneği de içerir. Application Gateway, Internet 'e yönelik bir uç nokta, yalnızca iç uç nokta veya her ikisinin bir birleşimi olarak yapılandırılabilir. Application Gateway, ölçeklenebilirlik ve yüksek oranda kullanılabilirlik sağlayan tam Azure yönetilir. Daha iyi yönetilebilirlik için zengin tanılama ve günlüğe kaydetme özellikleri sağlar.

Yük dengeleyiciler, tüm UDP ve TCP protokolleri için yüksek performanslı ve düşük gecikmeli katman 4 Yük Dengeleme hizmetleri sunan Azure SDN yığınının ayrılmaz bir parçasıdır. Arka uç havuzlarıyla gelen bağlantıları eşleştiren kuralları tanımlayarak ortak veya iç yük dengeli uç noktaları yapılandırabilirsiniz. TCP veya HTTPS kullanarak sistem durumu yoklama izlemeyle, hizmet kullanılabilirliğini yönetmenize yardımcı olabilir.

## <a name="choosing-a-global-load-balancer"></a>Küresel yük dengeleyici seçme
Küresel yönlendirme için Traffic Manager ile Azure ön kapısı arasında küresel bir yük dengeleyici seçerken, nelerin benzer olduğunu ve iki hizmet hakkında ne olduğunu göz önünde bulundurmanız gerekir.   Her iki hizmet de sağlar
- **Çoklu coğrafi yedeklilik:** Bir bölge hizmet dışına geçtiğinde trafik, uygulama sahibinden herhangi bir müdahale olmadan en yakın bölgeye sorunsuz bir şekilde yol açar.
- **En yakın bölge yönlendirme:** Trafik, en yakın bölgeye otomatik olarak yönlendirilir

</br>Aşağıdaki tabloda Traffic Manager ve Azure ön kapısı arasındaki farklar açıklanmaktadır:</br>

| Traffic Manager | Azure Front Door |
| --------------- | ------------------------ |
|**Herhangi bir protokol:** Traffic Manager DNS katmanında çalıştığından, herhangi bir ağ trafiği türünü yönlendirebilirsiniz; HTTP, TCP, UDP ve benzeri. | **Http hızlandırma:** Ön kapılı trafik, Microsoft ağı 'nın kenarına göre belirlenir. HTTP/S istekleri gecikme süresi ve aktarım hızı geliştirmelerini görmenizi sağlayacak ve bu da TLS anlaşması gecikmesini azaltır.|
|**Şirket içi yönlendirme:** Bir DNS katmanında yönlendirme sayesinde trafik her zaman noktadan noktaya gider.  Şube ofisinizden şirket içi veri merkezinize yönlendirme, doğrudan bir yol alabilir; Traffic Manager kullanarak kendi ağınızda bile. | **Bağımsız ölçeklenebilirlik:** Ön kapı HTTP isteğiyle çalıştığından, farklı URL yollarına yönelik istekler kurallara ve her bir uygulama mikro hizmetinin sistem durumuna göre farklı arka uç/bölgesel hizmet havuzlarına (mikro hizmetlere) yönlendirilebilir.|
|**Faturalama biçimi:** Kullanıcılarınız ve daha fazla kullanıcı içeren hizmetler için DNS tabanlı faturalandırma, daha yüksek kullanımlarda maliyeti azaltır. |**Satır içi güvenlik:** Ön kapı, trafiğin uygulamanıza ulaşmadan önce arka uçlarınızı korumanızı sağlamak için hız sınırlaması ve IP ACL 'leri gibi kurallara olanak sağlar. 

</br>HTTP 'nin ön kapıda kullanıldığı performans, çalışma ve güvenlik avantajları nedeniyle, müşterilerin HTTP iş yüklerinin ön kapısını kullanmasını öneririz. Traffic Manager ve ön kapı, uygulamanızın tüm trafiğini karşılamak için paralel olarak kullanılabilir. 

## <a name="building-with-azures-application-delivery-suite"></a>Azure 'un uygulama teslim paketiyle oluşturma 
Tüm Web sitelerinin, API 'Lerin, mümkün olduğunca en yakın konumdan kullanıcılara trafik sunabilmesi için coğrafi olarak yedekli olmasını öneririz.  Birden çok Yük Dengeleme hizmetini birleştirmek, güvenilirliği ve performansı en üst düzeye çıkarmak için coğrafi ve en iyi artıklık oluşturmanıza olanak sağlar.

Aşağıdaki diyagramda, genel bir Web hizmeti oluşturmak için bu hizmetlerin bir birleşimini kullanan örnek bir mimari açıklanır. Dosya ve nesne teslimi için trafiği genel arka uçlara yönlendirmek üzere kullanılan mimariyle Traffic Manager. Ön kapı kullanılırken,/Store/* düzeniyle eşleşen URL yollarını, App Service ' a geçirdikleri hizmete genel olarak yönlendirmek için. Son olarak, diğer tüm istekleri bölgesel uygulama ağ geçitlerine yönlendirme.

IaaS hizmetinin her bölgesinde, uygulama geliştiricisi/images/* düzeniyle eşleşen herhangi bir URL 'nin adanmış bir VM havuzundan sunulduğunu kararmıştır. Bu VM havuzu, Web grubunun geri kalanından farklıdır.

Ayrıca, dinamik içeriğe hizmet veren varsayılan VM havuzunun, yüksek kullanılabilirlik kümesinde barındırılan bir arka uç veritabanıyla iletişim sağlaması gerekir. Tüm dağıtım Azure Resource Manager aracılığıyla yapılandırılır.

Aşağıdaki diyagramda bu senaryonun mimarisi gösterilmektedir:

:::image type="content" source="./media/front-door-lb-with-azure-app-delivery-suite/application-delivery-figure2.png" alt-text="Uygulama teslim paketi ayrıntılı mimarisi":::

> [!NOTE]
> Bu örnek, Azure tarafından sunulan Yük Dengeleme hizmetlerinin olası birçok yapılandırmasından yalnızca biridir. Traffic Manager, ön kapı, Application Gateway ve Load Balancer, Yük Dengeleme gereksinimlerinize en iyi şekilde karışabilir ve eşleştirilebilir. Örneğin, TLS/SSL yük boşaltma veya katman 7 işleme gerekli değilse, Load Balancer Application Gateway yerine kullanılabilir.

## <a name="next-steps"></a>Sonraki Adımlar

- [Front Door oluşturmayı](quickstart-create-front-door.md) öğrenin.
- [Front Door’un nasıl çalıştığını](front-door-routing-architecture.md) öğrenin.
