---
title: API Management istemci yanıtı zaman aşımları ve hatalarıyla ilgili sorunları giderme
description: API Management 'da aralıklı bağlantı hatalarını ve ilgili gecikme sorunlarını giderme
author: vladvino
ms.topic: troubleshooting
ms.date: 12/04/2020
ms.author: apimpm
ms.service: api-management
ms.openlocfilehash: 6cace4a02c8d45cacbbc34e9778b5c4a78ada27f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100576521"
---
# <a name="troubleshooting-client-response-timeouts-and-errors-with-api-management"></a>API Management istemci yanıtı zaman aşımları ve hatalarıyla ilgili sorunları giderme

Bu makale, [Azure API Management](./api-management-key-concepts.md)'da aralıklı bağlantı hatalarını ve ilgili gecikme sorunlarını gidermenize yardımcı olur. Özellikle bu makale, kaynak adresi ağ çevirisi (SNAT) bağlantı noktalarının tükenmesi için bilgi ve sorun giderme bilgileri sağlar. Daha fazla yardıma ihtiyacınız varsa Azure [topluluk desteği](https://azure.microsoft.com/support/community/) ' nde Azure uzmanlarıyla iletişime geçin veya [Azure desteği](https://azure.microsoft.com/support/options/)ile destek isteği yapın.

## <a name="symptoms"></a>Belirtiler

API Management (APıM) hizmetiniz aracılığıyla API 'Leri çağıran istemci uygulamaları aşağıdaki belirtilerden birini veya daha fazlasını gösterebilir:

* Aralıklı HTTP 500 hataları
* Zaman aşımı hata iletileri

Bu belirti bildirimi, `BackendConnectionFailure` [Azure izleyici kaynak günlüklerinizin](../azure-monitor/essentials/resource-logs.md)örnekleri olarak izlenir.

## <a name="cause"></a>Nedeni

Bu belirti, genellikle APıM hizmetinize ağ adresi çevirisi (SNAT) bağlantı noktası sınırları nedeniyle oluşur.

Bir istemci APıM API 'lerinizin birini çağırdığında Azure API Management hizmeti, arka uç API 'nize erişmek için bir SNAT bağlantı noktası açar. Azure, [Azure 'Daki giden bağlantılarda](../load-balancer/load-balancer-outbound-connections.md)anlatıldığı gibi, genel IP adresi alanında Azure dışındaki uç noktalarıyla iletişim kurmak için kaynak ağ adresi ÇEVIRISI (SNAT) ve bir Load Balancer (müşterilere gösterilmez) [kullanır.](../virtual-network/virtual-network-service-endpoints-overview.md) Bu durum yalnızca genel IP 'lerde kullanıma sunulan arka uç API 'Leri için geçerlidir.

API Management hizmetinin her örneğine başlangıçta önceden ayrılmış SNAT bağlantı noktası sayısı verilir. Bu sınır, aynı konağa ve bağlantı noktası birleşimine yönelik bağlantıların açılmasını etkiler. SNAT bağlantı noktaları, aynı adrese ve bağlantı noktası birleşimine yinelenen çağrılar olduğunda kullanılır. Bir SNAT bağlantı noktası yayımlandıktan sonra, bağlantı noktası gerektiğinde yeniden kullanılabilir. Azure ağ yükü dengeleyici, yalnızca dört dakika bekledikten sonra, kapalı bağlantılardan gelen SNAT bağlantı noktalarını geri kazanır.

İstemci isteklerinin hızlı bir şekilde her ikisi de, bu bağlantı noktaları kapanmıyorsa ve yeterince hızlı geri dönüştürülüp, APıM hizmetinizin istemci isteklerini zamanında işlemesini önlemek için önceden ayrılmış SNAT bağlantı noktalarının kotasını tüketebilir.

## <a name="mitigations-and-solutions"></a>Azaltıcı Etkenler ve çözümler

Önce SNAT bağlantı noktası tükenmesi sorununa yönelik adresleme, arka uç hizmetlerinizin performansını tanılamaya ve iyileştirmesine gerek duyar.

SNAT bağlantı noktası tükenmesi için genel stratejiler, *Azure Load Balancer* belgelerinden [giden bağlantı hatalarında sorun giderme](../load-balancer/troubleshoot-outbound-connection.md) konusunda ele alınmıştır. Bu stratejilerin API Management için şunlar geçerlidir.

### <a name="scale-your-apim-instance"></a>APıM örneğinizi ölçeklendirin

Her API Management örneğine APıM birimlerine göre bir dizi SNAT bağlantı noktası ayrılır. API Management örneğinizi ek birimlerle ölçeklendirerek ek SNAT bağlantı noktaları ayırabilirsiniz. Daha fazla bilgi için bkz. [API Management hizmetinizi ölçeklendirme](upgrade-and-scale.md#scale-your-api-management-service)

> [!NOTE]
> SNAT bağlantı noktası kullanımı Şu anda otomatik ölçeklendirme API Management birimleri için bir ölçüm olarak kullanılamıyor.

### <a name="use-multiple-ips-for-your-backend-urls"></a>Arka uç URL 'niz için birden çok IP kullanma

APıM örneğinden aynı hedef IP 'ye ve arka uç hizmetinizin hedef bağlantı noktasına olan her bağlantı, ayrı bir trafik akışını sürdürmek için bir SNAT bağlantı noktası kullanır. Arka plan hizmetinizden gelen dönüş trafiği için farklı SNAT bağlantı noktaları olmadan APıM 'in bir yanıtı diğerinden ayırabilmenin bir yolu yoktur.

Hedef IP veya hedef bağlantı noktası farklı olduğunda SNAT bağlantı noktaları yeniden kullanılabilmesi için, SNAT bağlantı noktası tükenmesi 'nın arka uç hizmeti URL 'Lerinde birden çok IP kullanmasını önlemek için başka bir yol.

Daha fazla bilgi için bkz. [giden proxy Azure Load Balancer](../load-balancer/load-balancer-outbound-connections.md).

### <a name="place-your-apim-and-backend-service-in-the-same-vnet"></a>APıM ve arka uç hizmetinizi aynı VNet 'e yerleştirme

Arka uç API 'niz App Service gibi *hizmet uç noktalarını* destekleyen bir Azure hizmetinde barındırılıyorsa, APIM örneğinizi ve arka uç hizmetinizi aynı sanal ağa yerleştirip [hizmet uç noktaları](../virtual-network/virtual-network-service-endpoints-overview.md) veya [Özel uç noktalar](../private-link/private-endpoint-overview.md)aracılığıyla ortaya çıkarmak için SNAT bağlantı noktası tükenmesi sorunlarından kaçınabilirsiniz. Ortak VNet kullandığınızda ve tümleştirme alt ağına hizmet uç noktaları yerleştirdiğinizde, APıM örneğinden bu hizmetlere giden trafik interneti atlar ve bu nedenle SNAT bağlantı noktası kısıtlamalarını önler. Benzer şekilde, VNet ve özel uç noktalar kullanıyorsanız, o hedefe giden bir SNAT bağlantı noktası sorununuz olmayacaktır.

Ayrıntılar için bkz. [azure API Management sanal ağlarla kullanma](api-management-using-with-vnet.md) ve [App Service bir Azure sanal ağı ile tümleştirme](../app-service/web-sites-integrate-with-vnet.md).

### <a name="place-your-apim-in-a-virtual-network-and-route-outbound-calls-to-azure-firewall"></a>APıM 'nizi bir sanal ağa yerleştirin ve giden çağrıları Azure Güvenlik Duvarı 'na yönlendirin

APıM ve arka uç hizmetlerinizi bir sanal ağa yerleştirmeye benzer şekilde, Azure Güvenlik duvarını APıM hizmetinize sahip bir VNet 'te kullanabilir ve sonra giden APıM çağrılarını Azure Güvenlik Duvarı 'na yönlendirebilirsiniz. APıM ve Azure Güvenlik duvarı arasında (aynı VNet 'te olması gerekir), SNAT bağlantı noktası gerekmez. Azure Güvenlik duvarında, arka uç hizmetlerinize yönelik SNAT bağlantıları için, APıM örneklerine ayrılan çok daha yüksek bir miktar 64.000 bulunmaktadır.

Daha fazla bilgi için [Azure Güvenlik Duvarı](../firewall/overview.md) belgelerine bakın.

### <a name="consider-response-caching-and-other-backend-performance-tuning"></a>Yanıt önbelleğe alma ve diğer arka uç performansı ayarlamayı göz önünde bulundurun

Göz önünde bulundurulması gereken başka bir risk, arka uç API 'leriniz için işleme sürelerini geliştirmektir Bunu yapmanın bir yolu, API 'nizi ve APıM arka uç yüklerinizi çağıran istemci uygulamalar arasındaki gecikmeyi azaltmak için yanıt önbelleğe alma ile belirli API 'Leri yapılandırmaktır.

Daha fazla bilgi için bkz. [Azure API Management performansı artırmak için önbelleğe alma ekleme](api-management-howto-cache.md).

### <a name="consider-implementing-access-restriction-policies"></a>Erişim kısıtlama ilkeleri uygulamayı düşünün

İş senaryonuz için anlamlı bir işlem yapıyorsa, API Management ürününüz için erişim kısıtlama ilkeleri uygulayabilirsiniz. Örneğin, ilke, `rate-limit-by-key` belirli bir süre boyunca çağrı hızını sınırlayarak, her anahtar IÇIN API kullanım artışlarını engellemek için kullanılabilir.

Daha fazla bilgi için bkz. [API Management erişim kısıtlama ilkeleri](api-management-access-restriction-policies.md) .

## <a name="see-also"></a>Ayrıca bkz.

* [Azure Load Balancer: giden bağlantı hatalarıyla Ilgili sorunları giderme](../load-balancer/troubleshoot-outbound-connection.md)
* [Azure App Service: aralıklı giden bağlantı hatalarıyla Ilgili sorunları giderme](../app-service/troubleshoot-intermittent-outbound-connection-errors.md)
