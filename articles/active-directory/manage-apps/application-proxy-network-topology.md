---
title: Azure Active Directory Uygulama Ara Sunucusu için ağ topolojisi konuları
description: Azure Active Directory Uygulama Ara Sunucusu kullanırken ağ topolojisi konularını ele alır.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 2873bd9668bfba887ad9add061e68f36a747d5b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103492328"
---
# <a name="optimize-traffic-flow-with-azure-active-directory-application-proxy"></a>Trafik akışını Azure Active Directory Uygulama Ara Sunucusu iyileştirin

Bu makalede, uygulamalarınızı uzaktan yayımlamak ve bunlara erişmek için Azure Active Directory (Azure AD) uygulama proxy 'Si kullanılırken trafik akışını ve ağ topolojisi ile ilgili önemli noktaların nasıl iyileştirileceği açıklanır.

## <a name="traffic-flow"></a>Trafik akışı

Bir uygulama Azure AD Uygulama Ara Sunucusu aracılığıyla yayımlandığında, kullanıcılardan uygulamalara olan trafik üç bağlantıyla akar:

1. Kullanıcı Azure 'da Azure AD Uygulama Ara Sunucusu hizmeti genel uç noktasına bağlanıyor
1. Uygulama proxy 'si hizmeti, uygulama proxy 'Si bağlayıcısına bağlanır
1. Uygulama proxy Bağlayıcısı, hedef uygulamaya bağlanır

:::image type="content" source="./media/application-proxy-network-topology/application-proxy-three-hops.png" alt-text="Kullanıcıdan hedef uygulamaya trafik akışını gösteren diyagram." lightbox="./media/application-proxy-network-topology/application-proxy-three-hops.png":::

## <a name="optimize-connector-groups-to-use-closest-application-proxy-cloud-service-preview"></a>En yakın uygulama proxy 'Si bulut hizmetini kullanmak için bağlayıcı gruplarını iyileştirin (Önizleme)

Bir Azure AD kiracısına kaydolduğunuzda, kiracınızın bölgesi belirttiğiniz ülke/bölge tarafından belirlenir. Uygulama proxy 'Sini etkinleştirdiğinizde, kiracınız için **varsayılan** uygulama proxy 'si bulut hizmeti örnekleri, Azure AD kiracınızla aynı bölgede veya en yakın bölgede seçilir.

Örneğin, Azure AD kiracınızın ülkesi veya Bölgesi Birleşik Krallık ise, **varsayılan** olarak tüm uygulama proxy Bağlayıcılarınız Avrupa veri merkezlerinde hizmet örnekleri kullanmak üzere atanır. Kullanıcılarınız yayımlanmış uygulamalara erişirken, trafiği bu konumdaki uygulama proxy 'Si bulut hizmeti örneklerinden geçer.

Varsayılan bölgenizde farklı olan bölgelerde bağlayıcılar yüklüyse, bu uygulamalara erişim performansını artırmak için bağlayıcı grubunuzun en iyi hale getirildiği bölgeyi değiştirmek faydalı olabilir. Bağlayıcı grubu için bir bölge belirtildiğinde, belirtilen bölgedeki uygulama proxy 'Si bulut hizmetlerine bağlanır.

Trafik akışını iyileştirmek ve bağlayıcı grubuna gecikme süresini azaltmak için bağlayıcı grubunu en yakın bölgeye atayın. Bir bölge atamak için:

> [!IMPORTANT]
> Bağlayıcılar bu özelliği kullanmak için en az sürüm 1.5.1975.0 kullanıyor olmalıdır.

1. Uygulama proxy 'Si kullanan dizinin uygulama Yöneticisi olarak [Azure Portal](https://portal.azure.com/) oturum açın. Örneğin, kiracı etki alanı contoso.com ise yönetici admin@contoso.com ya da bu etki alanında başka bir yönetici diğer adı olmalıdır.
1. Sağ üst köşedeki Kullanıcı adınızı seçin. Uygulama proxy 'Si kullanan bir dizine oturum açtığınızdan emin olun. Dizinleri değiştirmeniz gerekiyorsa, **dizini** Değiştir ' i seçin ve uygulama proxy 'si kullanan bir dizin seçin.
1. Sol gezinti panelinde **Azure Active Directory**' yi seçin.
1. **Yönet** altında **uygulama proxy 'si**' ni seçin.
1. **Yeni bağlayıcı grubu**' nu seçin, bağlayıcı grubu Için bir **ad** sağlayın.
1. Ardından, **Gelişmiş ayarlar** ' ın altında, belirli bir bölge için iyileştirin ' in altındaki açılan seçimi seçin ve bağlayıcılara en yakın bölgeyi seçin.
1. **Oluştur**’u seçin.
    
    :::image type="content" source="./media/application-proxy-network-topology/geo-routing.png" alt-text="Yeni bir bağlayıcı grubu yapılandırın." lightbox="./media/application-proxy-network-topology/geo-routing.png":::

1. Yeni bağlayıcı grubu oluşturulduktan sonra, bu bağlayıcı grubuna atanacak bağlayıcıları seçebilirsiniz. 
   - Bağlayıcıları yalnızca varsayılan bölgeyi kullanan bir bağlayıcı grubununununbağlayıcı grubunuza taşıyabilirsiniz. En iyi yaklaşım, "varsayılan gruba" yerleştirilmiş bağlayıcılarınız ile her zaman başlamak ve uygun bağlayıcı grubuna taşımadır.
   - Bir bağlayıcı grubunun bölgesini, kendisine atanmış bir **bağlayıcı yoksa veya** kendisine atanan uygulamalar yoksa değiştirebilirsiniz.
1. Ardından bağlayıcı grubunu uygulamalarınıza atayın. Uygulamalara erişirken, trafik artık bağlayıcı grubunun en iyi duruma getirildiği bölgedeki uygulama proxy 'Si bulut hizmetine gitmelidir.

## <a name="considerations-for-reducing-latency"></a>Gecikme süresini azaltma konuları

Tüm proxy çözümleri, ağ bağlantınıza gecikme sağlar. Uzaktan erişim çözümünüz olarak hangi proxy veya VPN çözümünü seçtiğiniz, her zaman kurumsal ağınız içinde bağlantı sağlayan bir sunucular kümesi içerir.

Kuruluşlar genellikle çevre ağındaki sunucu uç noktalarını içerir. Bununla birlikte Azure AD Uygulama Ara Sunucusu ile, bağlayıcılar kurumsal ağınızda yer aldığı sırada ağ üzerindeki proxy hizmeti üzerinden trafik akar. Çevre ağı gerekli değildir.

Sonraki bölümlerde, gecikme süresini daha da azaltmanıza yardımcı olacak ek öneriler bulunur. 

### <a name="connector-placement"></a>Bağlayıcı yerleşimi

Uygulama proxy 'Si, kiracı konumunuza göre sizin için örneklerin konumunu seçer. Bununla birlikte, bağlayıcıyı nereye yükleyeceğinize karar verirsiniz ve ağ trafiğinizin gecikme süresini tanımlamak için size güç kazandırın.

Uygulama proxy 'Si hizmetini ayarlarken aşağıdaki soruları sorun:

- Uygulama nerede bulunuyor?
- Uygulamaya erişen çoğu kullanıcı nerede bulunur?
- Uygulama proxy örneği nerede bulunur?
- Azure ExpressRoute veya benzer bir VPN gibi Azure veri merkezlerine ayarlanmış bir özel ağ bağlantınız zaten var mı?

Bağlayıcının hem Azure hem de uygulamalarınızla iletişim kurması gerekir (trafik akış diyagramında adım 2 ve 3), bu nedenle bağlayıcının yerleştirilmesi bu iki bağlantının gecikmesini etkiler. Bağlayıcının yerleşimini değerlendirirken aşağıdaki noktaları göz önünde bulundurun:

- Çoklu oturum açma için Kerberos kısıtlı temsilcisini (KCD) kullanmak istiyorsanız, bağlayıcının bir veri merkezine bir görüş satırı olması gerekir. Ayrıca, bağlayıcı sunucusunun etki alanına katılmış olması gerekir.  
- Şüpheli olduğunda, bağlayıcıyı uygulamaya yaklaştırın.

### <a name="general-approach-to-minimize-latency"></a>Gecikme süresini en aza indirmek için genel yaklaşım

Her ağ bağlantısını en iyi duruma getirerek uçtan uca trafiğin gecikme süresini en aza indirmenize olanak sağlayabilirsiniz. Her bağlantı şu şekilde iyileştirilebilir:

- Atlamanın iki ucu arasındaki mesafeyi azaltma.
- Geçiş yapılacak doğru ağ seçiliyor. Örneğin, genel Internet yerine özel bir ağa geçiş yapmak, adanmış bağlantılar nedeniyle daha hızlı olabilir.

Azure ile kurumsal ağınız arasında adanmış bir VPN veya ExpressRoute bağlantısı varsa, bunu kullanmak isteyebilirsiniz.

## <a name="focus-your-optimization-strategy"></a>En iyi duruma getirme stratejinizi odaklayın

Kullanıcılarınız ile uygulama proxy hizmeti arasındaki bağlantıyı denetlemek için yapabileceğiniz çok şey vardır. Kullanıcılar, uygulamalarınıza bir ev ağı, kahve dükkanı veya farklı bir ülke/bölge aracılığıyla erişebilir. Bunun yerine, uygulamalar için uygulama proxy 'si hizmetindeki bağlantıları uygulama proxy 'Si bağlayıcılarına en iyileştirebilirsiniz. Ortamınıza aşağıdaki desenleri dahil etmeyi düşünün.

### <a name="pattern-1-put-the-connector-close-to-the-application"></a>1. Model: bağlayıcıyı uygulamaya yakın yerleştirme

Bağlayıcıyı, müşteri ağındaki hedef uygulamaya yakın yere yerleştirin. Bu yapılandırma, bağlayıcı ve uygulama kapandığı için topografi diyagramında 3. adımı en aza indirir.

Bağlayıcının etki alanı denetleyicisine bir görüş satırı olması gerekiyorsa, bu model avantajlıdır. Çoğu senaryo için iyi şekilde çalıştığı için müşterilerimizin çoğu bu kalıbı kullanır. Bu model, hizmet ve bağlayıcı arasındaki trafiği iyileştirmek için de, 2. düzende birleştirilebilir.

### <a name="pattern-2-take-advantage-of-expressroute-with-microsoft-peering"></a>2. Model: Microsoft eşlemesiyle ExpressRoute avantajlarından yararlanın

ExpressRoute 'u Microsoft eşlemesi ile ayarladıysanız, uygulama proxy 'Si ile bağlayıcı arasındaki trafik için daha hızlı ExpressRoute bağlantısı kullanabilirsiniz. Bağlayıcı hala ağınızda, uygulamaya yakın.

### <a name="pattern-3-take-advantage-of-expressroute-with-private-peering"></a>3. Model: özel eşleme ile ExpressRoute avantajlarından yararlanın

Azure ile kurumsal ağınız arasında özel eşleme ile ayrılmış bir VPN veya ExpressRoute ayarladıysanız, başka bir seçeneğiniz vardır. Bu yapılandırmada, Azure 'daki sanal ağ genellikle kurumsal ağın bir uzantısı olarak kabul edilir. Bu sayede bağlayıcıyı Azure veri merkezine yükleyebilir ve bağlayıcı-uygulama bağlantısının düşük gecikme süresine sahip olmaya devam edebilirsiniz.

Trafik adanmış bir bağlantı üzerinden akdığı için gecikme süresi aşılmış değildir. Ayrıca bağlayıcı, Azure AD kiracı konumunuza yakın bir Azure veri merkezinde yüklü olduğundan, geliştirilmiş uygulama proxy 'Si hizmetten bağlayıcıya gecikme süresi de alırsınız.

:::image type="content" source="./media/application-proxy-network-topology/application-proxy-expressroute-private.png" alt-text="Bir Azure veri merkezinde yüklü bağlayıcıyı gösteren diyagram" lightbox="./media/application-proxy-network-topology/application-proxy-expressroute-private.png":::

### <a name="other-approaches"></a>Diğer yaklaşımlar

Bu makalenin odağı bağlayıcı yerleşimi olsa da, daha iyi gecikme süresi özellikleri almak için uygulamanın yerleşimini de değiştirebilirsiniz.

Giderek, kuruluşlar ağları barındırılan ortamlara taşıyor. Bu sayede, uygulamalarını şirket ağının parçası olan barındırılan bir ortama yerleştirmeleri ve yine de etki alanı dahilinde olması sağlanır. Bu durumda, önceki bölümlerde ele alınan desenler yeni uygulama konumuna uygulanabilir. Bu seçeneği düşünüyorsanız, bkz. [Azure AD Domain Services](../../active-directory-domain-services/overview.md).

Ayrıca, farklı konumlarda ve ağlarda bulunan uygulamaları hedeflemek için [bağlayıcı gruplarını](application-proxy-connector-groups.md) kullanarak bağlayıcılarınızı düzenlemeyi göz önünde bulundurun.

## <a name="common-use-cases"></a>Genel kullanım örnekleri

Bu bölümde, birkaç yaygın senaryoya kılavuzluk ederiz. Azure AD kiracısının (ve dolayısıyla proxy hizmeti uç noktasının) Birleşik Devletler (US) içinde bulunduğunu varsayın. Bu kullanım durumlarında bahsedilen noktalar, dünyanın dört bir yanındaki diğer bölgelere de uygulanır.

Bu senaryolarda, her bağlantıyı bir "atlama" olarak çağırıyoruz ve daha kolay tartışmak için bu bağlantı sayısını sunuyoruz:

- **Atlama 1**: kullanıcının uygulama proxy 'si hizmeti
- **Atlama 2**: uygulama proxy 'Si hizmetine uygulama proxy 'si Bağlayıcısı
- **Atlama 3**: hedef uygulamaya uygulama proxy Bağlayıcısı 

### <a name="use-case-1"></a>Kullanım durumu 1

**Senaryo:** Uygulama, ABD 'deki bir kuruluşun ağında, aynı bölgedeki kullanıcılarla birlikte bulunur. Azure veri merkezi ve şirket ağı arasında ExpressRoute veya VPN yok.

**Öneri:** Önceki bölümde açıklanan 1. kalıbı izleyin. İyileştirilmiş gecikme için, gerekirse ExpressRoute kullanmayı düşünün.

Bu basit bir modeldir. Bağlayıcıyı uygulamanın yanına yerleştirerek atlama 3 ' ü iyileştirebilirsiniz. Bağlayıcı genellikle uygulamaya ve veri merkezine KCD işlemleri gerçekleştirmeye yönelik bir görüş satırı ile yüklendiği için bu da doğal bir seçimdir.

:::image type="content" source="./media/application-proxy-network-topology/application-proxy-pattern1.png" alt-text="Kullanıcıları, proxy, bağlayıcıyı ve uygulamayı gösteren diyagram, bızım için de kullanılır." lightbox="./media/application-proxy-network-topology/application-proxy-pattern1.png":::

### <a name="use-case-2"></a>Kullanım durumu 2

**Senaryo:** Uygulama, ABD 'deki bir kuruluşun ağında, kullanıcıların küresel olarak yayılmaktadır. Azure veri merkezi ve şirket ağı arasında ExpressRoute veya VPN yok.

**Öneri:** Önceki bölümde açıklanan 1. kalıbı izleyin.

Daha sonra, bağlayıcıyı uygulamanın yanına yerleştirdiğiniz atlama 3 ' ü optimize etmek de yaygın bir modeldir. Atlama 3 genellikle aynı bölgedeyse pahalı değildir. Ancak, 1. atlama, kullanıcının olduğu yere bağlı olarak daha pahalı olabilir, çünkü dünyanın içindeki kullanıcılar, ABD 'deki uygulama proxy örneğine erişebilmelidir. Tüm proxy çözümünün, kullanıcılar tarafından genel olarak yayılmakta olduğu benzer özelliklere sahip olduğunu belirtmekte de dikkat edin.

:::image type="content" source="./media/application-proxy-network-topology/application-proxy-pattern2.png" alt-text="Kullanıcılar Global olarak yayılırlar, ancak diğer her şey ABD 'de" lightbox="./media/application-proxy-network-topology/application-proxy-pattern2.png":::

### <a name="use-case-3"></a>Kullanım durumu 3

**Senaryo:** Uygulama, ABD 'deki bir kuruluşun ağında. Azure ile kurumsal ağ arasında Microsoft eşlemesi ile ExpressRoute var.

**Öneri:** Önceki bölümde açıklanan 1. ve 2. desenleri izleyin.

İlk olarak, bağlayıcıyı uygulamaya olabildiğince yakın yere yerleştirin. Ardından, sistem otomatik olarak ExpressRoute for atlama 2 ' yi kullanır.

ExpressRoute bağlantısı Microsoft eşlemesi kullanıyorsa, proxy ve bağlayıcı arasındaki trafik o bağlantının üzerinden akar. Atlama 2 ' nin en iyi duruma getirilmiş gecikmesi.

:::image type="content" source="./media/application-proxy-network-topology/application-proxy-pattern3.png" alt-text="Proxy ve bağlayıcı arasında ExpressRoute gösteren diyagram" lightbox="./media/application-proxy-network-topology/application-proxy-pattern3.png":::

### <a name="use-case-4"></a>Kullanım durumu 4

**Senaryo:** Uygulama, ABD 'deki bir kuruluşun ağında. Azure ile kurumsal ağ arasında özel eşleme içeren ExpressRoute var.

**Öneri:** Önceki bölümde açıklanan 3. kalıbı izleyin.

Bağlayıcıyı, ExpressRoute özel eşlemesi aracılığıyla kurumsal ağa bağlı Azure veri merkezine yerleştirin.

Bağlayıcı, Azure veri merkezinde yer alabilir. Bağlayıcının uygulama ve veri merkezinde özel ağ aracılığıyla hala bir görüş satırı olduğundan, atlama 3 en iyi şekilde kalır. Ayrıca, atlama 2 daha fazla iyileştirilmiştir.

:::image type="content" source="./media/application-proxy-network-topology/application-proxy-pattern4.png" alt-text="Azure veri merkezinde bağlayıcı, bağlayıcı ve uygulama arasında ExpressRoute" lightbox="./media/application-proxy-network-topology/application-proxy-pattern4.png":::

### <a name="use-case-5"></a>Kullanım durumu 5

**Senaryo:** Uygulama, Avrupa 'daki bir kuruluşun ağında, Avrupa 'daki çoğu kullanıcı ile varsayılan kiracı bölgesi ABD

**Öneri:** Bağlayıcıyı uygulamanın yanına yerleştirin. Avrupa uygulama proxy 'Si hizmet örnekleri kullanmak için en iyi duruma getirilmesi için bağlayıcı grubunu güncelleştirin. Adımlar için bkz. [en yakın uygulama proxy 'si bulut hizmetini kullanmak için bağlayıcı gruplarını iyileştirin](application-proxy-network-topology.md#optimize-connector-groups-to-use-closest-application-proxy-cloud-service-preview).

Avrupa kullanıcıları aynı bölgede yer alan bir uygulama proxy örneğine eriştiği için, atlama 1 pahalı değildir. Atlama 3 en iyi duruma getirilmiştir. Atlama 2 ' i iyileştirmek için ExpressRoute kullanmayı düşünün.

### <a name="use-case-6"></a>Kullanım durumu 6

**Senaryo:** Uygulama, Avrupa 'daki bir kuruluşun ağında, ABD 'deki çoğu kullanıcı ile varsayılan kiracı bölgesi bızım için.

**Öneri:** Bağlayıcıyı uygulamanın yanına yerleştirin. Avrupa uygulama proxy 'Si hizmet örnekleri kullanmak için en iyi duruma getirilmesi için bağlayıcı grubunu güncelleştirin. Adımlar için bkz. [en yakın uygulama proxy 'si bulut hizmetini kullanmak için bağlayıcı gruplarını iyileştirin](application-proxy-network-topology.md#optimize-connector-groups-to-use-closest-application-proxy-cloud-service-preview). Tüm ABD kullanıcıları Avrupa 'daki uygulama proxy 'Si örneğine erişmesi gerektiğinden atlama 1 daha pahalı olabilir.

Ayrıca, bu durumda başka bir değişken kullanmayı da düşünebilirsiniz. Kuruluştaki çoğu kullanıcı ABD 'de yer alıyorsa, ağınız da bıze genişlemektedir. Bağlayıcıyı bıze yerleştirin, bağlayıcı gruplarınız için varsayılan ABD bölgesini kullanmaya devam edin ve şirket içi kurumsal ağ hattını Avrupa 'daki uygulamaya kullanın. Bu şekilde atlama 2 ve 3 en iyi duruma getirilmiştir.

:::image type="content" source="./media/application-proxy-network-topology/application-proxy-pattern5c.png" alt-text="Diyagramda, Avrupa 'daki ABD 'deki kullanıcılar, proxy ve bağlayıcı gösterilmektedir." lightbox="./media/application-proxy-network-topology/application-proxy-pattern5c.png":::

## <a name="next-steps"></a>Sonraki adımlar

- [Uygulama Ara Sunucusunu etkinleştirme](application-proxy-add-on-premises-application.md)
- [Çoklu oturum açmayı etkinleştirme](application-proxy-configure-single-sign-on-with-kcd.md)
- [Koşullu erişimi etkinleştir](application-proxy-integrate-with-sharepoint-server.md)
- [Uygulama Ara Sunucusu ile ilgili sorunları giderme](application-proxy-troubleshoot.md)
