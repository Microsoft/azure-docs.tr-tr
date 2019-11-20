---
title: Uygulama ağ geçidi nasıl kullanılır?
description: Bu makalede, bir uygulama ağ geçidinin gelen istekleri kabul etme ve arka uca yönlendirme hakkında bilgi sağlanır.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 11/16/2019
ms.author: absha
ms.openlocfilehash: d9d7ae3bc321f1f000fac1a875589df352077f33
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74129816"
---
# <a name="how-an-application-gateway-works"></a>Uygulama ağ geçidi nasıl kullanılır?

Bu makalede, bir uygulama ağ geçidinin gelen istekleri nasıl kabul ettiğini ve arka uca nasıl yönlendirdiğini açıklanmaktadır.

![Uygulama ağ geçidi bir isteği kabul etme](./media/how-application-gateway-works/how-application-gateway-works.png)

## <a name="how-an-application-gateway-accepts-a-request"></a>Uygulama ağ geçidi bir isteği kabul etme

1. Bir istemci bir uygulama ağ geçidine istek göndermeden önce, bir etki alanı ad sistemi (DNS) sunucusu kullanarak uygulama ağ geçidinin etki alanı adını çözer. Tüm uygulama ağ geçitleri azure.com etki alanında olduğundan Azure DNS girdisini denetler.

2. Azure DNS, uygulama ağ geçidinin ön uç IP adresi olan istemciye IP adresini döndürür.

3. Uygulama ağ geçidi, bir veya daha fazla dinleyiciyle gelen trafiği kabul eder. Dinleyici, bağlantı isteklerini denetleyen mantıksal bir varlıktır. İstemcilerden uygulama ağ geçidine bağlantılar için bir ön uç IP adresi, protokol ve bağlantı noktası numarası ile yapılandırılır.

4. Bir Web uygulaması güvenlik duvarı (WAF) kullanılıyorsa, Application Gateway, WAF kurallarına karşı istek üst bilgilerini ve varsa gövdesini denetler. Bu eylem, isteğin geçerli istek mi yoksa bir güvenlik tehdidi mı olduğunu belirler. İstek geçerliyse, arka uca yönlendirilir. İstek geçerli değilse ve WAF önleme modundaysa, güvenlik tehdidi olarak engellenir. Algılama modundaysa, istek değerlendirilir ve günlüğe kaydedilir, ancak yine de arka uç sunucusuna iletilir.

Azure Application Gateway, iç uygulama yük dengeleyici olarak veya internet 'e yönelik bir uygulama yük dengeleyici olarak kullanılabilir. İnternet 'e yönelik bir uygulama ağ geçidi, genel IP adreslerini kullanır. İnternet 'e yönelik bir uygulama ağ geçidinin DNS adı genel IP adresi ile genel olarak çözülebilir. Sonuç olarak, internet 'e yönelik uygulama ağ geçitleri istemci isteklerini internet 'e yönlendirebilir.

İç uygulama ağ geçitleri yalnızca özel IP adreslerini kullanır. Özel veya [özel DNS bir bölge](https://docs.microsoft.com/azure/dns/private-dns-overview)kullanıyorsanız, etki alanı adının APPLICATION Gateway özel IP adresi ile dahili olarak çözümlenebilmelidir. Bu nedenle, iç yük dengeleyiciler yalnızca uygulama ağ geçidi için bir sanal ağa erişimi olan istemcilerden gelen istekleri yönlendirebilir.

## <a name="how-an-application-gateway-routes-a-request"></a>Uygulama ağ geçidi bir isteği nasıl yönlendirir

Bir istek geçerli ve WAF tarafından engellenmemişse, Application Gateway, dinleyiciyle ilişkili istek yönlendirme kuralını değerlendirir. Bu eylem, isteğin yönlendirileceği arka uç havuzunu belirler.

İstek yönlendirme kuralına bağlı olarak, uygulama ağ geçidi, dinleyicide yapılan tüm isteklerin belirli bir arka uç havuzuna yönlendirilip yönlendirmeyeceğini, URL yoluna göre istekleri farklı arka uç havuzlarına yönlendirmeyi veya istekleri başka bir bağlantı noktasına veya dış siteye yeniden yönlendirmeyi belirler.
>[!NOTE]
>Kurallar, v1 SKU 'SU için portalda listelendikleri sırada işlenir. 

Uygulama ağ geçidi arka uç havuzunu seçtiğinde, isteği havuzdaki sağlıklı arka uç sunucularından birine gönderir (y. y. y. y). Sunucunun sistem durumu bir sistem durumu araştırmasına göre belirlenir. Arka uç havuzu birden çok sunucu içeriyorsa, uygulama ağ geçidi istekleri sağlıklı sunucular arasında yönlendirmek için hepsini bir kez deneme algoritması kullanır. Bu yük, sunuculardaki istekleri dengeler.

Application Gateway, arka uç sunucusunu belirledikten sonra, HTTP ayarlarına dayalı olarak arka uç sunucusuyla yeni bir TCP oturumu açar. HTTP ayarları, arka uç sunucusuyla yeni bir oturum oluşturmak için gereken protokol, bağlantı noktası ve diğer yönlendirmeyle ilgili ayarları belirtir.

HTTP ayarlarında kullanılan bağlantı noktası ve protokol, uygulama ağ geçidi ve arka uç sunucuları arasındaki trafiğin şifrelenip şifrelenmediğini (Bu nedenle uçtan uca SSL 'nin yerine getirmeye mi olduğunu) veya şifrelenmemiş olduğunu belirtir.

Bir Application Gateway, arka uç sunucusuna özgün isteği gönderdiğinde, ana bilgisayar adını, yolu ve Protokolü geçersiz kılma ile ilgili HTTP ayarlarında yapılan özel bir yapılandırmaya sahiptir. Bu eylem, tanımlama bilgisi tabanlı oturum benzeşimi, bağlantı boşaltma, ana bilgisayardan konak adı seçimi ve bu şekilde devam eder.

 >[!NOTE]
>Arka uç havuzu:
> - , **Genel bir uç nokta olan**uygulama ağ geçidi, sunucuya ulaşmak için ön uç genel IP 'sini kullanır. Ön uç genel IP adresi yoksa, giden dış bağlantı için bir tane atanır.
> - **Dahili olarak çözümlenebilen BIR FQDN veya özel bır IP adresi içerdiğinde**, Application Gateway, isteğin örnek özel IP adreslerini kullanarak isteği arka uç sunucusuna yönlendirir.
> - **Bir dış uç nokta veya harici çözümlenebilen BIR FQDN içerir**, Application Gateway, ön uç genel IP adresini kullanarak isteği arka uç sunucusuna yönlendirir. DNS çözümlemesi, yapılandırıldıysa özel bir DNS bölgesine veya özel DNS sunucusuna dayalıdır veya varsayılan olarak Azure tarafından sağlanmış DNS 'i kullanır. Ön uç genel IP adresi yoksa, giden dış bağlantı için bir tane atanır.

### <a name="modifications-to-the-request"></a>İstekte yapılan değişiklikler

Uygulama ağ geçidi, istekleri arka uca iletmadan önce tüm isteklere dört ek üst bilgi ekler. Bu üstbilgiler x-iletilmiş-, x-iletilen-Proto, x-iletilmiş-Port ve x-orjinal-Host ' a yöneliktir. X-iletilmiş-for üstbilgisinin biçimi, IP: bağlantı noktasının virgülle ayrılmış listesidir.

X-iletilen-proto için geçerli değerler HTTP veya HTTPS 'DIR. X ile iletilen bağlantı noktası, isteğin uygulama ağ geçidine ulaştığı bağlantı noktasını belirtir. X-orijinal-ana bilgisayar üstbilgisi, isteğin ulaştığı orijinal ana bilgisayar üst bilgisini içerir. Bu üstbilgi, trafik arka uca yönlendirilmeden önce gelen ana bilgisayar üstbilgisinin değiştirildiği Azure Web sitesi tümleştirmesinde yararlı olur. Oturum benzeşimi bir seçenek olarak etkinleştirilmişse, ağ geçidi ile yönetilen bir benzeşim tanımlama bilgisi ekler.

Application Gateway 'i, [HTTP üstbilgilerini yeniden yaz](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers) veya bir yol geçersiz kılma AYARı kullanarak URI yolunu değiştirmek üzere üstbilgileri değiştirmek için yapılandırabilirsiniz. Bununla birlikte, bunu yapmak için yapılandırılmadığı takdirde, tüm gelen istekler arka uca alınır.

## <a name="next-steps"></a>Sonraki adımlar

[Application Gateway bileşenleri hakkında bilgi edinin](application-gateway-components.md)
