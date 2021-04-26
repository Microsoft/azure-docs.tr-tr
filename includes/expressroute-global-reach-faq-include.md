---
title: include dosyası
description: include dosyası
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 03/19/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 317a480c13c5c6e00653fd61878a379df3f65ac4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "67188107"
---
### <a name="what-is-expressroute-global-reach"></a>ExpressRoute Global Reach nedir?

ExpressRoute Global Reach, şirket içi ağlarınızı Microsoft 'un küresel ağı üzerinden ExpressRoute Hizmeti aracılığıyla bağlayan bir Azure hizmetidir. Örneğin, Microsoft 'un, Expressvalley 'teki ExpressRoute 'a bağlı bir özel veri merkezinizi ve ExpressRoute Global Reach ile ExpressRoute 'a bağlı başka bir özel veri merkezi varsa, iki ExpressRoute bağlantısı aracılığıyla özel veri merkezlerinizi birbirine bağlayabilirsiniz ve çapraz veri merkezi trafiğiniz Microsoft 'un ağ omurgası üzerinden gezinecektir.

### <a name="how-do-i-enable-or-disable-expressroute-global-reach"></a>Nasıl yaparım? ExpressRoute Global Reach etkinleştirilsin veya devre dışı bırakılır mi?

ExpressRoute devrelerinizi birbirine bağlayarak ExpressRoute Global Reach etkinleştirirsiniz. Devrelerin bağlantısını keserek özelliği devre dışı bırakabilirsiniz. Bkz. [yapılandırma](../articles/expressroute/expressroute-howto-set-global-reach.md).

### <a name="do-i-need-expressroute-premium-for-expressroute-global-reach"></a>ExpressRoute Global Reach için ExpressRoute Premium 'A ihtiyacım var mı?

ExpressRoute bağlantı hatlarınız aynı geopolitik bölgedeyse, bunları birbirine bağlamak için ExpressRoute Premium 'a ihtiyacınız yoktur. İki ExpressRoute bağlantı hattı farklı geopolitik bölgelerde ise aralarında bağlantıyı etkinleştirmek için her iki devrede ExpressRoute Premium 'a ihtiyacınız vardır. 

### <a name="how-will-i-be-charged-for-expressroute-global-reach"></a>ExpressRoute Global Reach nasıl ücretlendirilirim?

ExpressRoute, şirket içi ağınızdan Microsoft bulut hizmetleri arasında bağlantı sağlar. ExpressRoute Global Reach, Microsoft 'un küresel ağını kullanarak, mevcut ExpressRoute bağlantı hatlarınız aracılığıyla kendi şirket içi ağlarınızla bağlantı imkanı sunar. ExpressRoute Global Reach, mevcut ExpressRoute hizmetinden ayrı olarak faturalandırılır. Her ExpressRoute devresi üzerinde bu özelliği etkinleştirmeye yönelik bir eklenti ücreti vardır. ExpressRoute Global Reach tarafından etkinleştirilen şirket içi ağlarınız arasındaki trafik, kaynaktaki çıkış ücreti üzerinden ve hedefteki giriş oranına göre faturalandırılır. Ücretler, devrelerin bulunduğu bölgeye göre yapılır.

### <a name="where-is-expressroute-global-reach-supported"></a>ExpressRoute Global Reach desteklenir.

ExpressRoute Global Reach, [seçim ülkeleri/bölgeleri veya yerleri](../articles/expressroute/expressroute-global-reach.md)için desteklenir. ExpressRoute devreleri, bu ülkelerin/bölgelerin veya yerlerin eşleme konumlarında oluşturulmalıdır.

### <a name="i-have-more-than-two-on-premises-networks-each-connected-to-an-expressroute-circuit-can-i-enable-expressroute-global-reach-to-connect-all-of-my-on-premises-networks-together"></a>Her biri bir ExpressRoute devresine bağlanan ikiden fazla şirket içi ağ var. ExpressRoute Global Reach tüm şirket içi ağlarımı birbirine bağlamak için etkinleştirebilir miyim?

Evet, devrelerin desteklenen ülkelerde/bölgelerde olduğu sürece yapabilirsiniz. Her seferinde iki ExpressRoute bağlantı hattı bağlamanız gerekir. Tam olarak bir ağ oluşturmak için, tüm devre çiftlerini numaralandırın ve yapılandırmayı tekrarlamanız gerekir. 

### <a name="can-i-enable-expressroute-global-reach-between-two-expressroute-circuits-at-the-same-peering-location"></a>Aynı eşleme konumunda iki ExpressRoute bağlantı hattı arasında ExpressRoute Global Reach etkinleştirebilir miyim?

Hayır. İki devrein farklı eşleme konumlarından olması gerekir. Desteklenen bir ülkede/bölgede bulunan bir metro 'un birden fazla ExpressRoute eşleme konumu varsa, bu Metro 'daki farklı eşleme konumlarında oluşturulan ExpressRoute devrelerine bir araya getirebilirsiniz. 

### <a name="if-expressroute-global-reach-is-enabled-between-circuit-x-and-circuit-y-and-between-circuit-y-and-circuit-z-will-my-on-premises-networks-connected-to-circuit-x-and-circuit-z-talk-to-each-other-via-microsofts-network"></a>ExpressRoute Global Reach, devre X ve devre Y arasında etkinleştirildiyse ve devre Y ve devre Z arasında, devre X ve devre Z 'ye bağlı olan şirket içi ağlarım Microsoft 'un ağı aracılığıyla birbirleriyle konuşacak mı?

Hayır. Şirket içi ağlarınızdan herhangi ikisi arasında bağlantıyı etkinleştirmek için, ilgili ExpressRoute devresine açıkça bağlanmanız gerekir. Yukarıdaki örnekte, devre X ve devre Z 'yi bağlamanız gerekir. 

### <a name="what-is-the-network-throughput-i-can-expect-between-my-on-premises-networks-after-i-enable-expressroute-global-reach"></a>ExpressRoute Global Reach etkinleştirdikten sonra şirket içi ağlarım arasında beklediğim ağ aktarım hızı nedir?

ExpressRoute Global Reach tarafından etkinleştirilen şirket içi ağlarınızla ağ aktarım hızı, iki ExpressRoute devreninin daha küçük bir tarafında bulunur. Şirket içinden Azure 'a trafiği ve şirket içi trafiği aynı devreyi paylaşır ve aynı bant genişliği üst sınırına tabidir. 

### <a name="with-expressroute-global-reach-what-are-the-limits-on-the-number-of-routes-i-can-advertise-and-the-number-of-routes-i-will-receive"></a>ExpressRoute Global Reach ile, duyurabileceğim yolların sayısı ve alacağınız yolların sayısı arasındaki sınırlar nelerdir?

Azure özel eşleme 'de Microsoft 'a duyurmanız için bir yol sayısı standart bir bağlantı noktasında 4000 ' de veya Premium bir devreye göre 10000 ' de kalır. Azure özel eşleme 'de Microsoft 'tan alacağınız yolların sayısı, Azure sanal ağlarınızdaki yolların toplamı ve ExpressRoute Global Reach ile bağlantılı diğer şirket içi ağlarınızdan gelen yollardır. Lütfen şirket içi yönlendiricinizde uygun bir en yüksek önek sınırı ayarladığınızdan emin olun. 

### <a name="what-is-the-sla-for-expressroute-global-reach"></a>ExpressRoute Global Reach SLA 'Sı nedir?

ExpressRoute Global Reach, düzenli ExpressRoute Hizmeti ile aynı [kullanılabilirlik SLA 'sını](https://azure.microsoft.com/support/legal/sla/expressroute/v1_3/) sağlar.
