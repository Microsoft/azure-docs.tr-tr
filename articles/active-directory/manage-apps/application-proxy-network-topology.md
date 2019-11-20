---
title: Azure AD Uygulama Ara Sunucusu için ağ topolojisi konuları | Microsoft Docs
description: Azure AD uygulama ara sunucusu kullanırken, ağ topolojisi konuları kapsar.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a7320df63885f562b4724285a3ca5c3cf6ea2a52
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68381454"
---
# <a name="network-topology-considerations-when-using-azure-active-directory-application-proxy"></a>Azure Active Directory Uygulama proxy'si kullanılırken ağ topolojisi hakkında önemli noktalar

Bu makalede, Azure Active Directory (Azure AD) uygulama proxy'si yayımlama ve uygulamalarınızı uzaktan erişim için kullanılırken ağ topolojisi hakkında önemli noktalar açıklanmaktadır.

## <a name="traffic-flow"></a>Trafik akışı

Azure AD uygulama proxy'si aracılığıyla uygulama yayınlandıktan sonra uygulamaları kullanıcılardan gelen trafiği üç bağlantıları üzerinden akar:

1. Azure AD uygulama proxy'si hizmeti genel uç noktaya azure'da kullanıcı bağlanır.
1. Uygulama proxy'si hizmeti için uygulama Proxy Bağlayıcısı bağlanır.
1. Hedef uygulama için uygulama Proxy Bağlayıcısı bağlanır

![Trafik akışı, hedef uygulamaya kullanıcıdan gösteren diyagram](./media/application-proxy-network-topology/application-proxy-three-hops.png)

## <a name="tenant-location-and-application-proxy-service"></a>Kiracı konumu ve uygulama proxy'si hizmeti

Bir Azure AD kiracısına kaydolduğunuzda, kiracınızın bölgesi belirttiğiniz ülke/bölge tarafından belirlenir. Uygulama Ara sunucusunu etkinleştirme, kiracınız için uygulama proxy'si hizmeti örnekleri seçilen veya Azure AD kiracınızla aynı bölgede ya da kendisine en yakın bölgede oluşturulur.

Örneğin, Birleşik Krallık, Azure AD kiracınızın ülke veya bölge ise, tüm uygulama ara sunucusu bağlayıcıları AB veri merkezlerinde hizmet örneklerini kullanın. Kullanıcılar erişiminizi yayımlanan uygulamaları, uygulama proxy'si hizmeti örnekleri bu konumda trafiklerini geçer.

## <a name="considerations-for-reducing-latency"></a>Gecikme süresini azaltmak için dikkat edilmesi gerekenler

Tüm proxy çözümleri ağ bağlantınızı gecikmelere neden. Hangi Ara sunucu veya VPN çözümü uzaktan erişim çözümünüzü seçtiğiniz ne olursa olsun, her zaman kurumsal ağınızdaki bağlantısını etkinleştirme sunucular kümesi içerir.

Kuruluşlar, genellikle kendi çevre ağında sunucu uç noktaları içerir. Azure AD uygulama proxy'si ile bağlayıcıları şirket ağınızda bulunması sırasında trafik ancak, bulut proxy hizmeti aracılığıyla akar. Çevre ağ gereklidir.

Sonraki bölümlerde daha gecikme süresini azaltmanıza yardımcı olmak üzere ek öneriler içerir. 

### <a name="connector-placement"></a>Bağlayıcı yerleştirme

Uygulama proxy'si, Kiracı konumlarına göre sizin için örnekleri konumunu seçer. Ancak, bağlayıcı'yı yüklemeye karar ağ trafiğinizin gecikme özelliklerini tanımlamak için faaliyetlerine sahip olursunuz.

Uygulama proxy'si hizmeti ayarlanıyor, için aşağıdaki soruları sormaya:

- Uygulamayı nerede bulunur?
- Uygulama erişim çoğu kullanıcılar nerede bulunuyor?
- Uygulama Ara sunucusu örneği nerede bulunur?
- Zaten bir Azure veri merkezleri, Azure ExpressRoute veya benzer bir VPN gibi ayarlama Adanmış ağ bağlantısı gerekiyor?

Bağlayıcı, hem Azure hem de uygulamalarınızı (Adım 2 ve 3. trafik akışı diyagramı) ile bağlayıcı etkiler yerleşimini bu iki bağlantı gecikme süresini şekilde iletişim kurması gerekir. Bağlayıcı yerleşimini değerlendirirken, aşağıdaki noktaları göz önünde bulundurun:

- Kerberos Kısıtlı temsilci (KCD) için çoklu oturum açmayı kullanmak istiyorsanız, bağlayıcı görebilmesi için bir veri merkezi gerekir. Ayrıca, bağlayıcı sunucunun etki alanına katılmış olması gerekir.  
- Şüpheye düştüğünüzde uygulamaya yakın bağlayıcısı yükleyin.

### <a name="general-approach-to-minimize-latency"></a>Gecikme süresini en aza indirmek için genel yaklaşım

Her ağ bağlantısı iyileştirerek uçtan uca trafik gecikme süresini en aza indirebilirsiniz. Her bağlantı iyileştirilebilir:

- Atlama iki ucunu arasındaki uzaklığı azaltır.
- Geçiş için doğru ağ seçme. Örneğin, genel Internet yerine özel bir ağa geçiş nedeniyle ayrılmış bağlantılar daha hızlı olabilir.

Kurumsal ağınız ile Azure arasında adanmış bir VPN veya ExpressRoute bağlantısı varsa, kullanmak isteyebilirsiniz.

## <a name="focus-your-optimization-strategy"></a>En iyi duruma getirme stratejinizi odaklanın

Kullanıcılarınızın uygulama proxy'si hizmeti arasında bağlantı kontrol etmek için yapabileceğiniz çok az yoktur. Kullanıcılar, uygulamalarınıza bir ev ağı, kahve dükkanı veya farklı bir ülke/bölge aracılığıyla erişebilir. Bunun yerine, uygulama proxy'si hizmeti uygulaması Ara sunucusu bağlayıcıları bağlantılara iyileştirebilirsiniz. Ortamınızı şu desenlerden eklemeyi göz önünde bulundurun.

### <a name="pattern-1-put-the-connector-close-to-the-application"></a>1\. Model: Bağlayıcıyı uygulamaya kapat

Müşteri ağ yakın hedef uygulama Bağlayıcısı'nı koyun. Bu yapılandırma bağlayıcı ve uygulamayı Kapat olduğundan topografi diyagram 3 adımda en aza indirir.

Bağlayıcınızı görebilmesi için etki alanı denetleyicisi gerekiyorsa, bu düzen avantajlıdır. Çoğu senaryo için iyi çalıştığı için müşterilerimizin çoğu bu düzeni kullanın. Bu düzen Ayrıca hizmet Bağlayıcısı'nı arasındaki trafiğin iyileştirilmesi 2 deseni ile birleştirilebilir.

### <a name="pattern-2-take-advantage-of-expressroute-with-microsoft-peering"></a>2\. Model: Microsoft eşlemesiyle ExpressRoute avantajlarından yararlanın

Microsoft eşlemesi ile ayarlanan ExpressRoute varsa, uygulama ara sunucusu Bağlayıcısı'nı arasındaki trafik için daha hızlı ExpressRoute bağlantısı kullanabilirsiniz. Bağlayıcı, uygulamanın yakın ağınıza açık durumdadır.

### <a name="pattern-3-take-advantage-of-expressroute-with-private-peering"></a>3\. Model: Özel eşleme ile ExpressRoute avantajlarından yararlanın

Adanmış bir VPN veya ExpressRoute Azure ve şirket ağınız arasında özel eşlemesi ile ayarlanan varsa, başka bir seçeneğiniz vardır. Bu yapılandırmada, Azure sanal ağında genellikle bir şirket ağına uzantı olarak kabul edilir. Bu nedenle Azure veri merkezinde bağlayıcıyı yükleyin ve yine de bağlayıcı uygulama bağlantının düşük gecikme süresi gereksinimleri karşılaması.

Trafik, ayrılmış bir bağlantı üzerinden akan çünkü gecikme gizliliğinin tehlikeye atılmasını. Bir Azure veri merkezinde, Azure AD Kiracı konumu yakın Bağlayıcısı yüklü olmadığından uygulama proxy'si Hizmeti Bağlayıcısı gecikme süresini kısaltmak de alırsınız.

![Azure veri merkezinde yüklü bağlayıcı gösteren diyagram](./media/application-proxy-network-topology/application-proxy-expressroute-private.png)

### <a name="other-approaches"></a>Diğer yaklaşımlar

Bu makalenin odak bağlayıcı yerleştirme olsa da, daha iyi gecikme özelliklerini elde etmek için uygulamaya yerleşimini de değiştirebilirsiniz.

Giderek, kuruluşların kendi ağları barındırılan ortamlara taşınıyor. Bu uygulamalarını aynı zamanda, Kurumsal ağın parçası olan barındırılan bir ortamda yerleştirin ve etki alanı içinde devam etmelerini sağlar. Bu durumda, önceki bölümlerde ele desenleri, yeni uygulama konumuna uygulanabilir. Bu seçeneği değerlendirmeyi planlamaktadır olup [Azure AD Domain Services](../../active-directory-domain-services/overview.md).

Ayrıca, bağlayıcılarınızı kullanarak düzenleme göz önünde bulundurun [bağlayıcı grupları](application-proxy-connector-groups.md) farklı konumlara ve ağları olan hedef uygulamalar için.

## <a name="common-use-cases"></a>Genel kullanım örnekleri

Bu bölümde, bazı yaygın senaryolar üzerinden inceleyeceğiz. Varsayımında Azure AD kiracısı (ve bu nedenle proxy hizmet uç noktası) Amerika Birleşik Devletleri (ABD) bulunur. Çalışmalarını diğer bölgelerle dünya için de geçerlidir. Bu kullanımı ele konuları.

Bu senaryolar için her bağlantı bir "durak" arayın ve bunları daha kolay tartışmak için sayı:

- **Atlama 1**: Kullanıcı, uygulama proxy 'Si hizmeti
- **Atlama 2**: Uygulama proxy hizmeti için uygulama ara sunucusu Bağlayıcısı
- **Atlama 3**: Hedef uygulamaya uygulama proxy Bağlayıcısı 

### <a name="use-case-1"></a>Kullanım örneği 1

**Senaryon** Uygulama, ABD 'deki bir kuruluşun ağında, aynı bölgedeki kullanıcılarla birlikte bulunur. Herhangi bir ExpressRoute veya VPN kurumsal ağ ve Azure veri merkezi arasında yok.

**Önerilen** Önceki bölümde açıklanan 1. kalıbı izleyin. İçin gecikme süresini kısaltmak, ExpressRoute kullanılarak gerekirse göz önünde bulundurun.

Bu basit bir desendir. Atlama 3, bağlayıcı uygulama yakın yerleştirerek iyileştirin. Bağlayıcı genellikle görebilmesi KCD işlemleri gerçekleştirmek için bir veri merkezine ve uygulama ile birlikte yüklenir ayrıca doğal bir seçim olmasıdır.

![Kullanıcıları, proxy, bağlayıcıyı ve uygulamayı gösteren diyagram BIZIMLE](./media/application-proxy-network-topology/application-proxy-pattern1.png)

### <a name="use-case-2"></a>Kullanım örneği 2

**Senaryon** Uygulama, ABD 'deki bir kuruluşun ağında, kullanıcıların küresel olarak yayılmaktadır. Herhangi bir ExpressRoute veya VPN kurumsal ağ ve Azure veri merkezi arasında yok.

**Önerilen** Önceki bölümde açıklanan 1. kalıbı izleyin.

Yeniden yaygın atlama 3, en iyi duruma getirme Bağlayıcısı'nı uygulama yakın yerleştirdiğiniz modelidir. Atlama 3 tümü aynı bölge içinde ise genellikle pahalı değil. Ancak, dünya genelinde kullanıcılar uygulama ara Sunucusu örneğinde ABD erişmeniz gerekir çünkü atlama 1 kullanıcının olduğu bağlı olarak, daha pahalı olabilir. Bu, herhangi bir proxy çözümüne genel dağılmış kullanıcılar ile ilgili benzer özelliklere sahip olduğunu hatalarının ayıklanabileceğini belirtmekte yarar.

![Kullanıcılar Global olarak yayılırlar, ancak diğer her şey ABD 'de](./media/application-proxy-network-topology/application-proxy-pattern2.png)

### <a name="use-case-3"></a>Kullanım örneği 3

**Senaryon** Uygulama, ABD 'deki bir kuruluşun ağında. Microsoft eşlemesi ile ExpressRoute, Azure ve şirket ağı arasında yok.

**Önerilen** Önceki bölümde açıklanan 1. ve 2. desenleri izleyin.

İlk olarak, bağlayıcı olabildiğince uygulamaya mümkün olduğunca yakın yerleştirin. Ardından, sistem otomatik olarak 2 atlama için Expressroute'u kullanır.

Microsoft eşleme ExpressRoute bağlantı kullanıyorsanız, proxy ve bağlayıcı arasındaki trafik, bağlantı üzerinden akar. Atlama 2, gecikme süresi en iyi hale getirdi.

![ExpressRoute proxy ve bağlayıcı arasında gösteren diyagram](./media/application-proxy-network-topology/application-proxy-pattern3.png)

### <a name="use-case-4"></a>Kullanım örneği 4

**Senaryon** Uygulama, ABD 'deki bir kuruluşun ağında. ExpressRoute özel eşlemesi, Azure ve şirket ağı arasında yok.

**Önerilen** Önceki bölümde açıklanan 3. kalıbı izleyin.

Azure veri merkezine ExpressRoute özel eşlemesi üzerinden şirket ağına bağlı Bağlayıcısı'nı koyun.

Bağlayıcısı Azure veri merkezinde yerleştirilebilir. Bağlayıcı görebilmesi için uygulama ve özel ağ üzerinden veri merkezi hala olduğundan, atlama 3 en iyi duruma getirilmiş kalır. Ayrıca, atlama 2 daha da İyileştirildi.

![Azure veri merkezinde bağlayıcı, bağlayıcı ve uygulama arasında ExpressRoute](./media/application-proxy-network-topology/application-proxy-pattern4.png)

### <a name="use-case-5"></a>Kullanım örneği 5

**Senaryon** Uygulama, uygulama proxy 'Si örneği ve ABD 'deki çoğu kullanıcı ile AB 'deki bir kuruluşun ağında bulunur.

**Önerilen** Bağlayıcıyı uygulamanın yanına yerleştirin. Uygulama Ara sunucusu örneği aynı bölgede olması erişen ABD kullanıcı için atlama 1 çok pahalı değil. Atlama 3 optimize edilmiştir. Atlama 2 iyileştirmek için ExpressRoute kullanmayı düşünün.

![Diyagram, Avrupa 'daki ABD, bağlayıcı ve uygulamadaki kullanıcıları ve proxy 'yi gösterir](./media/application-proxy-network-topology/application-proxy-pattern5b.png)

Ayrıca, bu durumda bir değişken kullanarak göz önünde bulundurun. Sonra büyük olasılıkla, kuruluştaki kullanıcıların çoğu ABD'de varsa, ağınızı ABD için genişletir. ABD'de bağlayıcı yerleştirin ve uygulamaya ayrılmış iç kurumsal ağa satırı AB'de kullanın. 2\. ve 3 Bu şekilde atlama en iyi duruma getirilir.

![Diyagramda, AB 'de bulunan ve ABD 'deki kullanıcılar, proxy ve bağlayıcı gösterilmektedir](./media/application-proxy-network-topology/application-proxy-pattern5c.png)

## <a name="next-steps"></a>Sonraki adımlar

- [Uygulama Ara sunucusunu etkinleştirme](application-proxy-add-on-premises-application.md)
- [Çoklu oturum açmayı etkinleştirme](application-proxy-configure-single-sign-on-with-kcd.md)
- [Koşullu erişimi etkinleştir](application-proxy-integrate-with-sharepoint-server.md)
- [Uygulama Ara sunucusu ile ilgili sorunları giderme](application-proxy-troubleshoot.md)
