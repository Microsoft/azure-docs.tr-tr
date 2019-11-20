---
title: Application Gateway bileşenleri
description: Bu makale, bir uygulama ağ geçidinde çeşitli bileşenler hakkında bilgi sağlar
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/20/2019
ms.author: absha
ms.openlocfilehash: 73b5c86030d9e106cb3ea24d3100faa56e323815
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71348934"
---
# <a name="application-gateway-components"></a>Application Gateway bileşenleri

 Uygulama ağ geçidi, istemciler için tek bir iletişim noktası işlevi görür. Azure VM 'Leri, sanal makine ölçek kümeleri, Azure App Service ve şirket içi/dış sunucuları içeren birden fazla arka uç havuzunda gelen uygulama trafiğini dağıtır. Bir uygulama ağ geçidi, trafiği dağıtmak için bu makalede açıklanan çeşitli bileşenleri kullanır.

![Uygulama ağ geçidinde kullanılan bileşenler](./media/application-gateway-components/application-gateway-components.png)

## <a name="frontend-ip-addresses"></a>Ön uç IP adresleri

Ön uç IP adresi, bir uygulama ağ geçidiyle ilişkili IP adresidir. Bir uygulama ağ geçidini genel IP adresi, özel bir IP adresi veya her ikisine de sahip olacak şekilde yapılandırabilirsiniz. Uygulama ağ geçidi, bir genel veya bir özel IP adresini destekler. Sanal ağınız ve genel IP adresiniz, uygulama ağ geçidiniz ile aynı konumda olmalıdır. Oluşturulduktan sonra bir ön uç IP adresi bir dinleyiciyle ilişkilendirilir.

### <a name="static-versus-dynamic-public-ip-address"></a>Statik ve dinamik genel IP adresi karşılaştırması

Azure Application Gateway v2 SKU 'SU hem statik iç IP adresini hem de statik genel IP adresini ya da yalnızca statik genel IP adresini destekleyecek şekilde yapılandırılabilir. Yalnızca statik iç IP adresini destekleyecek şekilde yapılandırılamaz.

V1 SKU 'SU statik veya dinamik iç IP adresini ve dinamik genel IP adresini destekleyecek şekilde yapılandırılabilir. Application Gateway dinamik IP adresi çalışan bir ağ geçidinde değişmez. Yalnızca ağ geçidini durdurduğunuzda veya başlattığınızda değişiklik yapabilir. Sistem hatalarında, güncelleştirmelerde, Azure ana bilgisayar güncelleştirmelerinde vb. değişmez. 

Bir uygulama ağ geçidi ile ilişkilendirilen DNS adı, ağ geçidinin yaşam döngüsü boyunca değişmez. Sonuç olarak, bir CNAME diğer adı kullanmalı ve uygulama ağ geçidinin DNS adresine işaret etmelisiniz.

## <a name="listeners"></a>Dinleyiciler

Dinleyici, gelen bağlantı isteklerini denetleyen mantıksal bir varlıktır. Bir dinleyici, istekle ilişkilendirilen protokol, bağlantı noktası, ana bilgisayar adı ve IP adresi, dinleyici yapılandırmasıyla ilişkili öğelerle eşleşiyorsa bir isteği kabul eder.

Bir Application Gateway kullanmadan önce en az bir dinleyici eklemeniz gerekir. Bir uygulama ağ geçidine eklenmiş birden fazla dinleyici olabilir ve aynı protokol için kullanılabilirler.

Bir dinleyici istemcilerden gelen istekleri algıladıktan sonra, uygulama ağ geçidi bu istekleri kuralda yapılandırılmış arka uç havuzundaki üyelere yönlendirir.

Dinleyiciler aşağıdaki bağlantı noktalarını ve protokolleri destekler.

### <a name="ports"></a>Bağlantı Noktaları

Bir bağlantı noktası, bir dinleyicinin istemci isteğini dinlediği yerdir. V1 SKU 'SU için 1 ile 65502 arasında ve v2 SKU 'SU için 1 ile 65199 arasında bir bağlantı noktası yapılandırabilirsiniz.

### <a name="protocols"></a>Protokoller

Application Gateway dört protokolü destekler: HTTP, HTTPS, HTTP/2 ve WebSocket:
>[!NOTE]
>HTTP/2 protokol desteği yalnızca uygulama ağ geçidi dinleyicilerine bağlanan istemciler tarafından kullanılabilir. Arka uç sunucu havuzlarıyla iletişim, her zaman HTTP/1.1 üzerinden yapılır. HTTP/2 desteği varsayılan olarak devre dışıdır. Etkinleştirmeyi seçebilirsiniz.

- Dinleyici yapılandırmasındaki HTTP ve HTTPS protokolleri arasında belirtin.
- [WebSockets ve http/2 protokolleri](https://docs.microsoft.com/azure/application-gateway/overview#websocket-and-http2-traffic) için destek yerel olarak sağlanır ve [WebSocket desteği](https://docs.microsoft.com/azure/application-gateway/application-gateway-websocket) varsayılan olarak etkindir. WebSocket desteğini isteğe bağlı olarak etkinleştirmek veya devre dışı bırakmak için kullanıcı tarafından yapılandırılabilen bir ayar yoktur. HTTP ve HTTPS dinleyicilerine sahip WebSockets kullanın.

SSL sonlandırma için HTTPS dinleyicisi kullanın. Bir HTTPS dinleyicisi şifreleme ve şifre çözme işini uygulama ağ geçidinize yükler, bu nedenle web sunucularınız, ek yük tarafından aşırı alınamaz.

### <a name="custom-error-pages"></a>Özel hata sayfaları

Application Gateway, varsayılan hata sayfalarını görüntülemek yerine özel hata sayfaları oluşturmanızı sağlar. Özel hata sayfası sayesinde kendi logonuzu ve sayfa düzeninizi kullanabilirsiniz. Application Gateway, bir istek arka uca ulaşamadığınızda özel bir hata sayfası görüntüler.

Daha fazla bilgi için bkz. [Application Gateway Için özel hata sayfaları](https://docs.microsoft.com/azure/application-gateway/custom-error).

### <a name="types-of-listeners"></a>Dinleyici türleri

İki tür dinleyici vardır:

- **Temel**. Bu tür bir dinleyici, uygulama ağ geçidinin IP adresine tek bir DNS eşlemesinin bulunduğu tek bir etki alanı sitesini dinler. Bu dinleyici yapılandırması, bir uygulama ağ geçidinin arkasında tek bir site barındırdığınızda gereklidir.

- **Çok**siteli. Aynı uygulama ağ geçidi örneğinde birden fazla Web uygulaması yapılandırdığınızda, bu dinleyici yapılandırması gerekir. Bir Application Gateway 'e en çok 100 web sitesi ekleyerek dağıtımlarınız için daha verimli bir topoloji yapılandırmanıza olanak tanır. Her web sitesi, kendi arka uç havuzuna yönlendirilebilir. Örneğin, üç alt etki alanları, abc.contoso.com, xyz.contoso.com ve pqr.contoso.com, uygulama ağ geçidinin IP adresine işaret edin. Üç adet çok siteli dinleyici oluşturup her dinleyiciyi ilgili bağlantı noktası ve protokol ayarı için yapılandırırsınız.

    Daha fazla bilgi için bkz. [Çoklu site barındırma](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview).

Bir dinleyici oluşturduktan sonra, bunu bir istek yönlendirme kuralıyla ilişkilendirirsiniz. Bu kural, dinleyicide alınan isteğin arka uca yönlendirilme şeklini belirler.

Application Gateway, dinleyicileri gösterildiği sırada işler. Temel dinleyici gelen bir istekle eşleşiyorsa, önce işlenir. Trafiği doğru arka uca yönlendirmek için, temel bir dinleyiciye göre çok siteli bir dinleyici yapılandırın.

## <a name="request-routing-rules"></a>İstek yönlendirme kuralları

İstek yönlendirme kuralı, bir uygulama ağ geçidinin anahtar bileşenidir ve bu, trafiğin dinleyicide nasıl yönlendirileceğini belirler. Kural dinleyiciyi, arka uç sunucu havuzunu ve arka uç HTTP ayarlarını bağlar.

Bir dinleyici bir isteği kabul ettiğinde, istek yönlendirme kuralı isteği arka uca iletir veya başka bir yere yeniden yönlendirir. İstek arka uca iletilirse istek yönlendirme kuralı, bu dosyayı hangi arka uç sunucu havuzunun ileteceğini tanımlar. İstek yönlendirme kuralı, istekteki üstbilgilerin yeniden mi yeniden yazılması gerektiğini de belirler. Bir dinleyici tek bir kurala eklenebilir.

İki tür istek yönlendirme kuralı vardır:

- **Temel**. İlişkili dinleyicide bulunan tüm istekler (örneğin, blog.contoso.com/*) ilişkili HTTP ayarı kullanılarak ilişkili arka uç havuzuna iletilir.

- **Yol tabanlı**. Bu yönlendirme kuralı, istekteki URL 'yi temel alarak, ilişkili dinleyicide istekleri belirli bir arka uç havuzuna yönlendirmenizi sağlar. Bir istekteki URL yolu yol tabanlı bir kuralda yol düzeniyle eşleşiyorsa, kural bu isteği yönlendirir. Yol modelini yalnızca URL yoluna uygular, Sorgu parametrelerine uygulanmaz. Bir dinleyici isteğindeki URL yolu yol tabanlı kurallarla eşleşmezse, isteği varsayılan arka uç havuzuna ve HTTP ayarlarına yönlendirir.

Daha fazla bilgi için bkz. [URL tabanlı yönlendirme](https://docs.microsoft.com/azure/application-gateway/url-route-overview).

### <a name="redirection-support"></a>Yeniden yönlendirme desteği

İstek yönlendirme kuralı, uygulama ağ geçidinde trafiği yeniden yönlendirmenize de olanak tanır. Bu, genel bir yeniden yönlendirme mekanizmasıdır ve kuralları kullanarak tanımladığınız herhangi bir bağlantı noktasını yeniden yönlendirebilir.

Yeniden yönlendirme hedefini başka bir dinleyici olacak şekilde seçebilirsiniz (Bu, otomatik HTTP ile HTTPS yönlendirmesi etkinleştirebilir) veya bir dış site olabilir. Yeniden yönlendirmenin geçici veya kalıcı olmasını ya da URI yolunu ve sorgu dizesini yeniden yönlendirilen URL 'ye eklenmesini seçebilirsiniz.

Daha fazla bilgi için bkz. [Application Gateway uygulamanızdaki trafiği yeniden yönlendirme](https://docs.microsoft.com/azure/application-gateway/redirect-overview).

### <a name="rewrite-http-headers"></a>HTTP üst bilgilerini yeniden üretme

İstek yönlendirme kurallarını kullanarak, istek ve yanıt paketleri uygulama ağ geçidi aracılığıyla istemci ve arka uç havuzları arasında taşındığında, HTTP (S) istek ve yanıt üst bilgilerini ekleyebilir, kaldırabilir veya güncelleyebilirsiniz.

Üst bilgiler statik değerlere veya diğer üst bilgilere ve sunucu değişkenlerine ayarlanabilir. Bu, istemci IP adreslerini ayıklama, arka uç hakkındaki hassas bilgileri kaldırma, daha fazla güvenlik ekleme vb. gibi önemli kullanım durumlarının sağlanmasına yardımcı olur.

Daha fazla bilgi için bkz. [Application Gateway 'de HTTP üstbilgilerini yeniden yazma](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers).

## <a name="http-settings"></a>HTTP ayarları

Bir uygulama ağ geçidi, trafiği bağlantı noktası numarası, protokol ve bu bileşende açıklanan diğer ayarları kullanarak arka uç sunucularına (HTTP ayarları dahil istek yönlendirme kuralında belirtilen) yönlendirir.

HTTP ayarlarında kullanılan bağlantı noktası ve protokol, uygulama ağ geçidi ile arka uç sunucuları arasındaki trafiğin şifrelenip şifrelenmeyeceğini (uçtan uca SSL sağlar) veya şifrelenmemiş olduğunu belirtir.

Bu bileşen için de kullanılır:

- Bir kullanıcı oturumunun, [tanımlama bilgisi tabanlı oturum benzeşimi](https://docs.microsoft.com/azure/application-gateway/overview#session-affinity)kullanılarak aynı sunucuda tutulup tutulmayacağını belirleme.

- [Bağlantı boşaltma](https://docs.microsoft.com/azure/application-gateway/overview#connection-draining)kullanarak arka uç havuzu üyelerini dikkatlice kaldırın.

- Arka uç durumunu izlemek için özel bir araştırma ilişkilendirin, istek zaman aşımı aralığını ayarlayın, istekteki ana bilgisayar adını ve yolu geçersiz kılın ve App Service arka ucunun ayarlarını belirtmek için tek tıklamayla kolaylık sağlayın.

## <a name="backend-pools"></a>Arka uç havuzları

Arka uç havuzu, isteği sunan arka uç sunucularına yönelik istekleri yönlendirir. Arka uç havuzları şunları içerebilir:

- NIC’ler
- Sanal makine ölçek kümeleri
- Genel IP adresleri
- İç IP adresleri
- FQDN
- Çok kiracılı arka uçlar (App Service gibi)

Application Gateway arka uç havuzu üyeleri bir kullanılabilirlik kümesine bağlı değildir. Bir uygulama ağ geçidi, içinde bulunduğu sanal ağ dışındaki örneklerle iletişim kurabilir. Sonuç olarak, arka uç havuzlarının üyeleri, IP bağlantısı olduğu sürece kümeler arasında, veri merkezlerinde veya Azure dışında olabilir.

Arka uç havuzu üyeleri olarak iç IP 'Leri kullanırsanız, [sanal ağ eşlemesi](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) veya bir [VPN ağ geçidi](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)kullanmanız gerekir. Sanal ağ eşlemesi, diğer sanal ağlardaki Yük Dengeleme trafiği için desteklenir ve faydalıdır.

Bir uygulama ağ geçidi, trafiğe izin veriliyorsa Azure ExpressRoute veya VPN tünellerinin bağlı olduğu durumlarda şirket içi sunucularla da iletişim kurabilir.

Farklı istek türleri için farklı arka uç havuzları oluşturabilirsiniz. Örneğin, genel istekler için bir arka uç havuzu oluşturun ve daha sonra uygulamanız için mikro hizmetlere istekler için bir arka uç havuzu oluşturun.

## <a name="health-probes"></a>Durum araştırmaları

Varsayılan olarak, bir uygulama ağ geçidi, arka uç havuzundaki tüm kaynakların sistem durumunu izler ve sağlıksız olanları otomatik olarak kaldırır. Ardından, sağlıksız örnekleri izler ve kullanılabilir hale geldiğinde ve sistem durumu araştırmalarına yanıt verdiklerinde yeniden sağlıklı arka uç havuzuna ekler.

Varsayılan sistem durumu araştırması izlemeyi kullanmanın yanı sıra, sistem durumu araştırmasını uygulamanızın gereksinimlerine uyacak şekilde de özelleştirebilirsiniz. Özel yoklamalar, sistem durumu izleme üzerinde daha ayrıntılı denetim sağlar. Özel yoklamalar kullanırken, araştırma aralığını, test edilecek URL 'yi ve yolu, arka uç havuzu örneği sağlıksız olarak işaretlenmeden önce kaç tane başarısız yanıt kabul edeceğini yapılandırabilirsiniz. Her bir arka uç havuzunun sistem durumunu izlemek için özel araştırmaları yapılandırmanızı öneririz.

Daha fazla bilgi için bkz. [uygulama ağ geçidinizin durumunu izleme](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview).

## <a name="next-steps"></a>Sonraki adımlar

Uygulama ağ geçidi oluşturma:

* [Azure portal](quick-create-portal.md)
* [Azure PowerShell kullanarak](quick-create-powershell.md)
* [Azure CLı kullanarak](quick-create-cli.md)
