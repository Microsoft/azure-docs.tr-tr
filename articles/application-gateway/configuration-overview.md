---
title: Azure Application Gateway yapılandırmasına genel bakış
description: Bu makalede, Azure Application Gateway bileşenlerini yapılandırma açıklanmaktadır
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/15/2019
ms.author: absha
ms.openlocfilehash: 38d86a9ed82c3a242364e788cce371f83575c1ea
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74108730"
---
# <a name="application-gateway-configuration-overview"></a>Application Gateway yapılandırmaya genel bakış

Azure Application Gateway, farklı senaryolar için çeşitli şekillerde yapılandırabileceğiniz çeşitli bileşenlerden oluşur. Bu makalede, her bir bileşenin nasıl yapılandırılacağı gösterilir.

![Application Gateway bileşenleri akış grafiği](./media/configuration-overview/configuration-overview1.png)

Bu görüntüde, üç dinleyici içeren bir uygulama gösterilmektedir. İlk ikisi sırasıyla `http://acme.com/*` ve `http://fabrikam.com/*`için çok siteli dinleyiclardır. Her ikisi de 80 numaralı bağlantı noktasını dinler. Üçüncü, uçtan uca Güvenli Yuva Katmanı (SSL) sonlandırmasına sahip temel bir dinleyiciye sahiptir.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Önkoşullar

### <a name="azure-virtual-network-and-dedicated-subnet"></a>Azure sanal ağı ve adanmış alt ağ

Uygulama ağ geçidi, sanal ağınızdaki adanmış bir dağıtımdır. Sanal ağınızda, uygulama ağ geçidi için adanmış bir alt ağ gerekir. Bir alt ağda belirli bir uygulama ağ geçidi dağıtımının birden fazla örneğine sahip olabilirsiniz. Ayrıca, alt ağdaki diğer uygulama ağ geçitlerini da dağıtabilirsiniz. Ancak uygulama ağ geçidi alt ağında başka herhangi bir kaynak dağıtamazsınız.

> [!NOTE]
> Aynı alt ağda Standard_v2 ve standart Azure Application Gateway karıştırılamaz.

#### <a name="size-of-the-subnet"></a>Alt ağın boyutu

Application Gateway, örnek başına 1 özel IP adresi ve özel ön uç IP 'si yapılandırılmışsa başka bir özel IP adresi tüketir.

Azure, iç kullanım için her alt ağda 5 IP adresini de ayırır: ilk 4 ve son IP adresleri. Örneğin, özel ön uç IP 'si olmayan 15 Application Gateway örnekleri göz önünde bulundurun. Bu alt ağ için en az 20 IP adresiniz olması gerekir: iç kullanım için 5 ve Application Gateway örnekleri için 15. Bu nedenle,/27 veya daha büyük bir alt ağ boyutu gerekir.

27 Application Gateway örnekleri ve bir özel ön uç IP 'si için IP adresi olan bir alt ağ düşünün. Bu durumda, 33 IP adresine sahip olmanız gerekir: Application Gateway örnekleri için 27, özel ön uç için 1, iç kullanım için 5. Bu nedenle,/26 alt ağ boyutuna veya daha büyük bir ağa ihtiyacınız vardır.

En az/28 olan bir alt ağ boyutu kullanmanızı öneririz. Bu boyut size 11 kullanılabilir IP adresi sağlar. Uygulamanızın yüklemesi 10 ' dan fazla Application Gateway örneği gerektiriyorsa, bir/27 veya/26 alt ağ boyutu düşünün.

#### <a name="network-security-groups-on-the-application-gateway-subnet"></a>Application Gateway alt ağındaki ağ güvenlik grupları

Ağ güvenlik grupları (NSG 'ler) Application Gateway desteklenir. Ancak çeşitli kısıtlamalar vardır:

- Application Gateway v1 SKU 'SU için 65503-65534 TCP bağlantı noktalarında gelen Internet trafiğine izin vermeniz gerekir ve v2 SKU 'SU için TCP bağlantı noktaları 65200-65535, hedef alt ağa sahip *olmalıdır.* Bu bağlantı noktası aralığı, Azure altyapı iletişimi için gereklidir. Bu bağlantı noktaları Azure sertifikaları tarafından korunur (kilitlidir). Bu ağ geçitlerinin müşterileri de dahil olmak üzere dış varlıklar, uygun sertifikalara sahip olmayan bu uç noktalar üzerinde değişiklik başlatamaz.

- Giden internet bağlantısı engellenmiyor. NSG 'deki varsayılan giden kurallar internet bağlantısına izin verir. Şunları yapmanızı öneririz:

  - Varsayılan giden kurallarını kaldırmayın.
  - Giden internet bağlantısını reddeden diğer çıkış kuralları oluşturmayın.

- **AzureLoadBalancer** etiketinin trafiğine izin verilmelidir.

##### <a name="allow-application-gateway-access-to-a-few-source-ips"></a>Birkaç kaynak IP 'ye Application Gateway erişime izin ver

Bu senaryo için Application Gateway alt ağında NSG 'leri kullanın. Aşağıdaki kısıtlamaları alt ağa bu öncelik sırasına göre yerleştirin:

1. Kaynak IP veya IP aralığından gelen trafiğe ve hedefin tüm Application Gateway alt ağı ya da yapılandırılmış özel ön uç IP 'si olarak izin verir. NSG ortak IP üzerinde çalışmaz.
2. Tüm kaynaklardan gelen isteklere Application Gateway v1 SKU 'SU için 65503-65534 bağlantı noktasına ve [arka uç durumu iletişimi](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)IÇIN v2 SKU 'su için 65200-65535 bağlantı noktalarına izin verin. Bu bağlantı noktası aralığı, Azure altyapı iletişimi için gereklidir. Bu bağlantı noktaları Azure sertifikaları tarafından korunur (kilitlidir). Uygun sertifikalar yerine, dış varlıklar bu uç noktalar üzerinde değişiklik başlatamaz.
3. [Ağ güvenlik grubundaki](https://docs.microsoft.com/azure/virtual-network/security-overview)gelen Azure Load Balancer Araştırmaları (*AzureLoadBalancer* Tag) ve gelen sanal ağ trafiğine (*VirtualNetwork* etiketi) izin verin.
4. Engelle-All kuralını kullanarak diğer tüm gelen trafiği engelleyin.
5. Tüm hedefler için internet 'e giden trafiğe izin verin.

#### <a name="user-defined-routes-supported-on-the-application-gateway-subnet"></a>Application Gateway alt ağında desteklenen Kullanıcı tanımlı yollar

V1 SKU 'SU için, Kullanıcı tanımlı yollar (UDRs), uçtan uca istek/yanıt iletişimini değiştirmediği sürece Application Gateway alt ağında desteklenir. Örneğin, paket incelemesi için bir güvenlik duvarı gereci işaret etmek üzere Application Gateway alt ağında bir UDR ayarlayabilirsiniz. Ancak, inceleme sonrasında paketin amaçlanan hedefe ulaşabildiğinizden emin olmanız gerekir. Bunun başarısız olması, sistem durumu araştırmasına veya trafik yönlendirme davranışına yol açabilir. Bu, Azure ExpressRoute veya sanal ağdaki VPN ağ geçitleri tarafından yayılan, öğrenilen yolları veya varsayılan 0.0.0.0/0 yollarını içerir.

V2 SKU 'SU için UDRs Application Gateway alt ağında desteklenmez. Daha fazla bilgi için bkz. [Azure Application Gateway v2 SKU 'su](application-gateway-autoscaling-zone-redundant.md#differences-with-v1-sku).

> [!NOTE]
> UDRs, v2 SKU 'SU için desteklenmez.  UDRs 'ye ihtiyacınız varsa v1 SKU 'SU dağıtmaya devam etmelisiniz.

> [!NOTE]
> Application Gateway alt ağında UDRs kullanmak, [arka uç sistem durumu görünümündeki](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#back-end-health) sistem durumunun "bilinmiyor" olarak görünmesine neden olur. Ayrıca, Application Gateway günlüklerin ve ölçümlerin oluşturulmasına neden olur. Arka uç sistem durumunu, günlükleri ve ölçümleri görüntüleyebilmeniz için Application Gateway alt ağında UDRs 'yi kullanmanızı öneririz.

## <a name="front-end-ip"></a>Ön uç IP 'si

Uygulama ağ geçidini genel IP adresi, özel bir IP adresi veya her ikisine de sahip olacak şekilde yapılandırabilirsiniz. İstemcilerin internet 'e yönelik bir sanal IP (VIP) ile internet üzerinden erişmesi gereken bir arka ucu barındırdığınızda genel IP gereklidir. 

İnternet 'e açık olmayan bir iç uç nokta için genel IP gerekli değildir. Bu, *iç yük dengeleyici* (ILB) uç noktası veya özel ön uç IP 'si olarak bilinir. Bir Application Gateway ıLB, internet 'e açık olmayan iç iş kolu uygulamaları için yararlıdır. Ayrıca, internet 'e açık olmayan ancak hepsini bir kez deneme yük dağıtımı, oturum sürekliliği veya SSL sonlandırması gerektiren bir güvenlik sınırı içinde çok katmanlı bir uygulamadaki hizmetler ve katmanlar için de kullanışlıdır.

Yalnızca 1 genel IP adresi veya 1 özel IP adresi desteklenir. Uygulama ağ geçidini oluştururken ön uç IP 'sini seçersiniz.

- Genel IP için, yeni bir genel IP adresi oluşturabilir veya uygulama ağ geçidi ile aynı konumda bulunan bir genel IP 'yi kullanabilirsiniz. Daha fazla bilgi için bkz. [statik ve dinamik genel IP adresi](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#static-versus-dynamic-public-ip-address).

- Özel bir IP için, uygulama ağ geçidinin oluşturulduğu alt ağdan özel bir IP adresi belirtebilirsiniz. Bir tane belirtmezseniz, alt ağdan rastgele bir IP adresi otomatik olarak seçilir. Seçtiğiniz IP adresi türü (statik veya dinamik) daha sonra değiştirilemez. Daha fazla bilgi için bkz. [iç yük dengeleyiciye sahip bir uygulama ağ geçidi oluşturma](https://docs.microsoft.com/azure/application-gateway/application-gateway-ilb-arm).

Ön uç IP adresi, ön uç IP üzerindeki gelen istekleri denetleyen bir *dinleyiciyle*ilişkilendirilir.

## <a name="listeners"></a>Dinleyiciler

Dinleyici, bağlantı noktası, protokol, konak ve IP adresini kullanarak gelen bağlantı isteklerini denetleyen mantıksal bir varlıktır. Dinleyiciyi yapılandırdığınızda, bunlar için ağ geçidinde gelen istekteki karşılık gelen değerlerle eşleşen değerler girmeniz gerekir.

Azure portal kullanarak bir uygulama ağ geçidi oluşturduğunuzda, dinleyici için protokolü ve bağlantı noktasını seçerek de varsayılan bir dinleyici oluşturursunuz. HTTP2 desteğinin dinleyicide etkinleştirilip etkinleştirilmeyeceğini seçebilirsiniz. Uygulama ağ geçidini oluşturduktan sonra, bu varsayılan dinleyicinin (*Appgatewayhttplistener*) ayarlarını düzenleyebilir veya yeni dinleyiciler oluşturabilirsiniz.

### <a name="listener-type"></a>Dinleyici türü

Yeni bir dinleyici oluşturduğunuzda [ *temel* ve *Çoklu site*](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#types-of-listeners)arasında seçim yapabilirsiniz.

- Tüm isteklerinizin (herhangi bir etki alanı için) kabul edilmesini ve arka uç havuzlara iletilmesini istiyorsanız temel ' yı seçin. [Temel dinleyiciyle bir uygulama ağ geçidi oluşturmayı](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)öğrenin.

- İstekleri *konak* üstbilgisine veya ana bilgisayar adına göre farklı arka uç havuzlara iletmek istiyorsanız, gelen istekle eşleşen bir ana bilgisayar adı da belirtmeniz gereken çok siteli dinleyici ' i seçin. Bunun nedeni, Application Gateway aynı genel IP adresi ve bağlantı noktasında birden fazla Web sitesini barındırmak için HTTP 1,1 ana bilgisayar üst bilgilerini temel alır.

#### <a name="order-of-processing-listeners"></a>İşleme dinleyicileri sırası

V1 SKU 'SU için, istekler kuralların sırasına ve dinleyici türüne göre eşleştirilir. Temel dinleyiciye sahip bir kural sırayla ilk kez geliyorsa, önce işlenir ve bu bağlantı noktası ve IP birleşimi için tüm istekleri kabul eder. Bunu önlemek için, kuralları çok siteli dinleyiciyle yapılandırın ve kuralı temel dinleyiciyle listenin en son bölümüne gönderin.

V2 SKU 'SU için, çoklu site dinleyicileri temel dinleyicilerine göre işlenir.

### <a name="front-end-ip"></a>Ön uç IP 'si

Bu dinleyiciyle ilişkilendirmeyi planladığınız ön uç IP adresini seçin. Dinleyici, bu IP üzerindeki gelen istekleri dinleyecektir.

### <a name="front-end-port"></a>Ön uç bağlantı noktası

Ön uç bağlantı noktasını seçin. Mevcut bir bağlantı noktasını seçin veya yeni bir bağlantı noktası oluşturun. [İzin verilen bağlantı noktası aralığından](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#ports)herhangi bir değer seçin. 80 ve 443 gibi yalnızca iyi bilinen bağlantı noktalarını, ancak uygun olan tüm özel bağlantı noktalarını kullanabilirsiniz. Bir bağlantı noktası, genel kullanıma yönelik dinleyiciler veya özel kullanıma yönelik dinleyiciler için kullanılabilir.

### <a name="protocol"></a>Protokol

HTTP veya HTTPS seçin:

- HTTP ' yi seçerseniz, istemci ve uygulama ağ geçidi arasındaki trafik şifrelenmemiş olur.

- [SSL sonlandırma](https://docs.microsoft.com/azure/application-gateway/overview#secure-sockets-layer-ssltls-termination) veya [uçtan uca SSL şifrelemeyi](https://docs.microsoft.com/azure/application-gateway/ssl-overview)istiyorsanız https seçeneğini belirleyin. İstemci ile uygulama ağ geçidi arasındaki trafik şifrelenir. Ve SSL bağlantısı uygulama ağ geçidinde sonlanır. Uçtan uca SSL şifrelemesi istiyorsanız, HTTPS 'yi seçmeniz ve **arka uç http** ayarını yapılandırmanız gerekir. Bu, uygulama ağ geçidinden arka uca seyahat edildiğinde trafiğin yeniden şifrelenmesini sağlar.

SSL sonlandırma ve uçtan uca SSL şifrelemesini yapılandırmak için, uygulama ağ geçidinin bir simetrik anahtar türetmesini sağlamak üzere dinleyiciye bir sertifika eklemeniz gerekir. Bu, SSL protokol belirtimi tarafından belirlenir. Simetrik anahtar, ağ geçidine gönderilen trafiği şifrelemek ve şifresini çözmek için kullanılır. Ağ Geçidi sertifikası kişisel bilgi değişimi (PFX) biçiminde olmalıdır. Bu biçim, ağ geçidinin trafiği şifrelemek ve şifresini çözmek için kullandığı özel anahtarı dışa aktarmanıza olanak tanır.

#### <a name="supported-certificates"></a>Desteklenen sertifikalar

Bkz. [SSL sonlandırma için desteklenen sertifikalar](https://docs.microsoft.com/azure/application-gateway/ssl-overview#certificates-supported-for-ssl-termination).

### <a name="additional-protocol-support"></a>Ek protokol desteği

#### <a name="http2-support"></a>HTTP2 desteği

HTTP/2 protokol desteği yalnızca uygulama ağ geçidi dinleyicilerine bağlanan istemciler tarafından kullanılabilir. Arka uç sunucu havuzlarıyla iletişim HTTP/1.1 üzerinden yapılır. HTTP/2 desteği varsayılan olarak devre dışıdır. Aşağıdaki Azure PowerShell kod parçacığı bunun nasıl etkinleştirileceğini göstermektedir:

```azurepowershell
$gw = Get-AzApplicationGateway -Name test -ResourceGroupName hm

$gw.EnableHttp2 = $true

Set-AzApplicationGateway -ApplicationGateway $gw
```

#### <a name="websocket-support"></a>WebSocket desteği

WebSocket desteği varsayılan olarak etkindir. Etkinleştirmek veya devre dışı bırakmak için Kullanıcı tarafından yapılandırılabilir bir ayar yoktur. WebSockets 'i hem HTTP hem de HTTPS dinleyicilerine sahip olarak kullanabilirsiniz.

### <a name="custom-error-pages"></a>Özel hata sayfaları

Genel düzeyde veya dinleyici düzeyinde özel hata tanımlayabilirsiniz. Ancak Azure portal genel düzey özel hata sayfaları oluşturma şu anda desteklenmiyor. Dinleyici düzeyinde 403 Web uygulaması güvenlik duvarı hatası veya 502 bakım sayfası için özel hata sayfası yapılandırabilirsiniz. Ayrıca, belirtilen hata durum kodu için genel olarak erişilebilir bir blob URL 'SI belirtmeniz gerekir. Daha fazla bilgi için bkz. [Application Gateway özel hata sayfası oluşturma](https://docs.microsoft.com/azure/application-gateway/custom-error).

![Application Gateway hata kodları](https://docs.microsoft.com/azure/application-gateway/media/custom-error/ag-error-codes.png)

Genel özel hata sayfasını yapılandırmak için, bkz. [Azure PowerShell Configuration](https://docs.microsoft.com/azure/application-gateway/custom-error#azure-powershell-configuration).

### <a name="ssl-policy"></a>SSL ilkesi

Bir arka uç sunucu grubu için SSL sertifika yönetimini merkezileştirmek ve şifreleme şifre çözme ek yükünü azaltabilirsiniz. Merkezi SSL işleme, güvenlik gereksinimlerinize uygun bir merkezi SSL ilkesi belirtmenize de olanak tanır. *Varsayılan*, *önceden tanımlanmış*veya *özel* SSL ilkesi seçebilirsiniz.

SSL ilkesini SSL protokolü sürümlerini denetlemek için yapılandırırsınız. Bir uygulama ağ geçidini TLS 1.0, TLS 1.1 ve TLS 1.2 ile TLS el sıkışmaları için minimum protokol sürümünü kullanacak şekilde yapılandırabilirsiniz. Varsayılan olarak, SSL 2,0 ve 3,0 devre dışıdır ve yapılandırılamaz. Daha fazla bilgi için bkz. [SSL ilkesine genel bakış Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview).

Bir dinleyici oluşturduktan sonra, bunu bir istek yönlendirme kuralıyla ilişkilendirirsiniz. Bu kural, dinleyicide alınan isteklerin arka uca nasıl yönlendirildiğini belirler.

## <a name="request-routing-rules"></a>İstek yönlendirme kuralları

Azure portal kullanarak bir uygulama ağ geçidi oluşturduğunuzda, varsayılan bir kural (*rule1*) oluşturursunuz. Bu kural varsayılan dinleyiciyi (*Appgatewayhttplistener*) varsayılan arka uç Havuzu (*appgatewaybackendpool*) ve varsayılan arka uç http ayarları (*Appgatewaybackendhttpsettings*) ile bağlar. Ağ geçidini oluşturduktan sonra, varsayılan kuralın ayarlarını düzenleyebilir veya yeni kurallar oluşturabilirsiniz.

### <a name="rule-type"></a>Kural türü

Bir kural oluşturduğunuzda [ *temel* ve *yol tabanlı*](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#request-routing-rules)arasında seçim yapabilirsiniz.

- İlişkili dinleyicide tüm istekleri (örneğin, *Blog<i></i>. contoso.com/\*)* tek bir arka uç havuzuna iletmek istiyorsanız temel ' yı seçin.
- Belirli URL yollarındaki istekleri belirli arka uç havuzlarına yönlendirmek istiyorsanız yol tabanlı ' i seçin. Yol deseninin Sorgu parametrelerine değil yalnızca URL 'nin yoluna uygulanması.

#### <a name="order-of-processing-rules"></a>İşleme kuralları sırası

V1 SKU 'SU için, gelen isteklerin düzen eşleşmesi, yolların yol tabanlı kuralın URL yol haritasında listelendiği sırada işlenir. Bir istek, yol eşlemesindeki iki veya daha fazla yoldaki Düzenle eşleşiyorsa, önce listelenen yol eşleştirilir. Ve istek bu yol ile ilişkili arka uca iletilir.

V2 SKU 'SU için, tam eşleşme URL yol eşlemesindeki yol sıraından daha yüksek önceliktir. Bir istek iki veya daha fazla yoldaki Düzenle eşleşiyorsa, istek, istekle tam olarak eşleşen yol ile ilişkili arka uca iletilir. Gelen istekteki yol haritadaki herhangi bir yol ile tam olarak eşleşmiyorsa, isteğin düzen eşleşmesi yol tabanlı kural için yol eşleme sırası listesinde işlenir.

### <a name="associated-listener"></a>İlişkili dinleyici

Dinleyiciyle ilişkilendirilen *istek yönlendirme kuralı* , isteği yönlendiren arka uç havuzunu belirleyecek şekilde değerlendirilmek için bir dinleyiciyi kuralla ilişkilendirin.

### <a name="associated-back-end-pool"></a>İlişkili arka uç havuzu

Dinleyicinin aldığı isteklere sunan arka uç hedeflerini içeren arka uç havuzunun bulunduğu kuralla ilişkilendirin.

 - Temel bir kural için yalnızca bir arka uç havuzuna izin verilir. İlişkili dinleyicide bulunan tüm istekler bu arka uç havuzuna iletilir.

 - Yol tabanlı bir kural için, her bir URL yoluna karşılık gelen birden fazla arka uç havuzu ekleyin. Girilen URL yoluyla eşleşen istekler karşılık gelen arka uç havuzuna iletilir. Ayrıca, bir varsayılan arka uç havuzu ekleyin. Kuraldaki hiçbir URL yoluyla eşleşmeyen istekler bu havuza iletilir.

### <a name="associated-back-end-http-setting"></a>İlişkili arka uç HTTP ayarı

Her kural için bir arka uç HTTP ayarı ekleyin. İstekler, bu ayarda belirtilen bağlantı noktası numarası, protokol ve diğer bilgileri kullanarak uygulama ağ geçidinden arka uç hedeflerine yönlendirilir.

Temel bir kural için yalnızca bir arka uç HTTP ayarına izin verilir. İlişkili dinleyicide bulunan tüm istekler, bu HTTP ayarı kullanılarak karşılık gelen arka uç hedeflerine iletilir.

Yol tabanlı bir kural için, her bir URL yoluna karşılık gelen birden fazla arka uç HTTP ayarı ekleyin. Bu ayarda URL yoluyla eşleşen istekler, her bir URL yoluna karşılık gelen HTTP ayarları kullanılarak ilgili arka uç hedeflerine iletilir. Ayrıca, varsayılan bir HTTP ayarı ekleyin. Bu kuraldaki hiçbir URL yoluyla eşleşmeyen istekler varsayılan HTTP ayarı kullanılarak varsayılan arka uç havuzuna iletilir.

### <a name="redirection-setting"></a>Yeniden yönlendirme ayarı

Bir temel kural için yeniden yönlendirme yapılandırılırsa, ilişkili dinleyicinin tüm istekleri hedefe yeniden yönlendirilir. Bu, *genel* yeniden yönlendirme 'dir. Yol tabanlı bir kural için yeniden yönlendirme yapılandırılırsa, yalnızca belirli bir site alanındaki istekler yeniden yönlendirilir. Örnek, */cart/\** tarafından belirtilen bir alışveriş sepeti alanıdır. Bu, *yol tabanlı* yeniden yönlendirme 'dir.

Yeniden yönlendirmeler hakkında daha fazla bilgi için bkz. [Application Gateway yönlendirmeye genel bakış](https://docs.microsoft.com/azure/application-gateway/redirect-overview).

#### <a name="redirection-type"></a>Yeniden yönlendirme türü

Gereken yeniden yönlendirme türünü seçin: *kalıcı (301)* , *geçici (.)* , *bulunan (302*) veya *diğer (303*).

#### <a name="redirection-target"></a>Yeniden yönlendirme hedefi

Yeniden yönlendirme hedefi olarak başka bir dinleyici veya dış site seçin.

##### <a name="listener"></a>Oluşturulurken

Trafiği bir dinleyicisinden ağ geçidinde diğerine yeniden yönlendirmek için yeniden yönlendirme hedefi olarak dinleyici ' i seçin. HTTP-HTTPS yeniden yönlendirmeyi etkinleştirmek istediğinizde bu ayar gereklidir. Gelen HTTP isteklerini, gelen HTTPS isteklerini denetleyen hedef dinleyiciye denetleyen kaynak dinleyicisinden trafiği yeniden yönlendirir. Ayrıca, yeniden yönlendirme hedefine iletilen istekteki özgün istekten sorgu dizesini ve yolunu eklemeyi de tercih edebilirsiniz.

![Application Gateway bileşenleri iletişim kutusu](./media/configuration-overview/configure-redirection.png)

HTTP-HTTPS yeniden yönlendirmesi hakkında daha fazla bilgi için bkz.:
- [Azure portal kullanarak HTTP-HTTPS yönlendirmesi](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-portal)
- [PowerShell kullanarak HTTP-to-HTTPS yönlendirmesi](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-powershell)
- [Azure CLı kullanarak HTTP-HTTPS yönlendirmesi](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-cli)

##### <a name="external-site"></a>Dış site

Bu kuralla ilişkilendirilen dinleyicide trafiği bir dış siteye yönlendirmek istediğinizde dış site ' yi seçin. Sorgu dizesini, yeniden yönlendirme hedefine iletilen istekteki özgün istekten eklemeyi seçebilirsiniz. Yolu özgün istekte bulunan dış siteye iletmeyin.

Yeniden yönlendirme hakkında daha fazla bilgi için bkz.
- [PowerShell kullanarak trafiği dış siteye yönlendirme](https://docs.microsoft.com/azure/application-gateway/redirect-external-site-powershell)
- [CLı kullanarak trafiği dış siteye yönlendirme](https://docs.microsoft.com/azure/application-gateway/redirect-external-site-cli)

#### <a name="rewrite-the-http-header-setting"></a>HTTP üstbilgisi ayarını yeniden yazın

Bu ayar, istek ve yanıt paketleri istemci ile arka uç havuzları arasında hareket ederken HTTP isteği ve yanıt üst bilgilerini ekler, kaldırır veya günceller. Daha fazla bilgi için bkz.

 - [HTTP üstbilgilerini yeniden yazma genel bakış](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers)
 - [HTTP üstbilgisini yeniden yazmayı yapılandırma](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers-portal)

## <a name="http-settings"></a>HTTP ayarları

Application Gateway, burada belirttiğiniz yapılandırmayı kullanarak trafiği arka uç sunucularına yönlendirir. Bir HTTP ayarı oluşturduktan sonra, bir veya daha fazla istek yönlendirme kuralıyla ilişkilendirmeniz gerekir.

### <a name="cookie-based-affinity"></a>Tanımlama bilgisi tabanlı benzeşim

Bu özellik, bir kullanıcı oturumunu aynı sunucuda tutmak istediğinizde yararlıdır. Ağ Geçidi ile yönetilen tanımlama bilgileri, Application Gateway 'in sonraki trafiği işlenmek üzere bir kullanıcı oturumundan aynı sunucuya yönlendirmesine izin verir. Oturum durumu, bir Kullanıcı oturumu için sunucuda yerel olarak kaydedildiğinde bu önemlidir. Uygulama, tanımlama bilgisi tabanlı benzeşimi işleyemez, bu özelliği kullanamazsınız. Bunu kullanmak için, istemcilerin tanımlama bilgilerini desteklemesini sağlayın.

### <a name="connection-draining"></a>Bağlantı boşaltma

Bağlantı boşaltma, planlı hizmet güncelleştirmeleri sırasında arka uç havuz üyelerini dikkatlice kaldırmanıza yardımcı olur. Bu ayarı, kural oluşturma sırasında bir arka uç havuzunun tüm üyelerine uygulayabilirsiniz. Bir arka uç havuzunun tüm kayıt kaldırma örneklerinin yeni istek almamasını sağlar. Bu arada, mevcut isteklerin yapılandırılmış bir süre sınırı içinde tamamlanmasını izin verilir. Bağlantı boşaltma, arka uç havuzundan açıkça kaldırılan arka uç örnekleri için geçerlidir.

### <a name="protocol"></a>Protokol

Application Gateway, arka uç sunucularına yönlendirme istekleri için hem HTTP hem de HTTPS 'yi destekler. HTTP ' yi seçerseniz, arka uç sunucularının trafiği şifrelenmemiş olur. Şifrelenmemiş iletişim kabul edilebilir değilse, HTTPS seçeneğini belirleyin.

Dinleyicide HTTPS ile birleştirilmiş bu ayar [uçtan uca SSL](https://docs.microsoft.com/azure/application-gateway/ssl-overview)'yi destekler. Bu, arka uca şifrelenmiş hassas verileri güvenli bir şekilde aktarmanıza olanak tanır. Uçtan uca SSL etkin olan arka uç havuzundaki her bir arka uç sunucusu, güvenli iletişime izin vermek için bir sertifikayla yapılandırılmalıdır.

### <a name="port"></a>Bağlantı noktası

Bu ayar, arka uç sunucularının uygulama ağ geçidinden gelen trafiği dinleyebileceği bağlantı noktasını belirtir. 1 ile 65535 arasında bir bağlantı noktası yapılandırabilirsiniz.

### <a name="request-timeout"></a>İstek zaman aşımı

Bu ayar, uygulama ağ geçidinin arka uç sunucusundan yanıt almak için beklediği saniye sayısıdır.

### <a name="override-back-end-path"></a>Arka uç yolunu geçersiz kıl

Bu ayar, istek arka uca iletildiğinde kullanılacak isteğe bağlı bir özel iletme yolu yapılandırmanıza olanak tanır. Gelen yolun, **arka uç yolunu geçersiz kılma** alanındaki özel yol ile eşleşen herhangi bir bölümü, iletilen yola kopyalanır. Aşağıdaki tabloda bu özelliğin nasıl çalıştığı gösterilmektedir:

- HTTP ayarı temel bir istek yönlendirme kuralına eklendiğinde:

  | Özgün istek  | Arka uç yolunu geçersiz kıl | İstek arka uca iletildi |
  | ----------------- | --------------------- | ---------------------------- |
  | /Home            | /override seçeneğiyle birlikte            | /override/Home/              |
  | /Home/Secondhome/ | /override seçeneğiyle birlikte            | /override/Home/Secondhome/   |

- HTTP ayarı yol tabanlı bir istek yönlendirme kuralına eklendiğinde:

  | Özgün istek           | Yol kuralı       | Arka uç yolunu geçersiz kıl | İstek arka uca iletildi |
  | -------------------------- | --------------- | --------------------- | ---------------------------- |
  | /pathrule/Home/            | pathrule      | /override seçeneğiyle birlikte            | /override/Home/              |
  | /pathrule/Home/Secondhome/ | pathrule      | /override seçeneğiyle birlikte            | /override/Home/Secondhome/   |
  | /Home                     | pathrule      | /override seçeneğiyle birlikte            | /override/Home/              |
  | /Home/Secondhome/          | pathrule      | /override seçeneğiyle birlikte            | /override/Home/Secondhome/   |
  | /pathrule/Home/            | /pathrule/Home * | /override seçeneğiyle birlikte            | /override seçeneğiyle birlikte                   |
  | /pathrule/Home/Secondhome/ | /pathrule/Home * | /override seçeneğiyle birlikte            | /override/Secondhome/        |
  | pathrule                 | pathrule      | /override seçeneğiyle birlikte            | /override seçeneğiyle birlikte                   |

### <a name="use-for-app-service"></a>App Service için kullanın

Bu yalnızca Azure App Service arka ucu için gereken iki ayarı seçen bir UI kısayoludur. **Ana bilgisayar adının arka uç adresinden seçim**yapmasına izin verebilir ve henüz yoksa yeni bir özel araştırma oluşturur. (Daha fazla bilgi için bu makalenin [arka uç adres ayarından konak adını seçin](#pick) bölümüne bakın.) Yeni bir araştırma oluşturulur ve araştırma üst bilgisi arka uç üyesinin adresinden çekilir.

### <a name="use-custom-probe"></a>Özel araştırma kullan

Bu ayar özel bir [araştırmayı](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#custom-health-probe) bir http ayarıyla ilişkilendirir. HTTP ayarıyla yalnızca bir özel araştırma ilişkilendirebilirsiniz. Özel bir araştırmayı açık bir şekilde ilişkilendirmezseniz, arka ucun sistem durumunu izlemek için [varsayılan araştırma](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#default-health-probe-settings) kullanılır. Arka uçlarınızın sistem durumu izlemesi üzerinde daha fazla denetim için özel bir araştırma oluşturmanız önerilir.

> [!NOTE]
> Özel araştırma, karşılık gelen HTTP ayarı açıkça bir dinleyiciyle ilişkilendirilmediği takdirde arka uç havuzunun sistem durumunu izlemez.

### arka uç adresinden <a id="pick"/></a>konak adını seçin

Bu yetenek, istekteki *ana bilgisayar* üst bilgisini arka uç havuzunun ana bilgisayar adına dinamik olarak ayarlar. Bir IP adresi veya FQDN kullanır.

Bu özellik, arka ucun etki alanı adının uygulama ağ geçidinin DNS adından farklı olması ve arka ucunun doğru uç noktaya çözülmesi için belirli bir ana bilgisayar üst bilgisine dayanmasına yardımcı olur.

Örnek olarak, arka uç olarak çok kiracılı hizmetler yer aldığı bir durumdur. App Service, tek bir IP adresi ile paylaşılan bir alan kullanan çok kiracılı bir hizmettir. Bu nedenle, bir App Service 'e yalnızca özel etki alanı ayarlarında yapılandırılmış ana bilgisayar adları aracılığıyla erişilebilir.

Varsayılan olarak, özel etki alanı adı *example.azurewebsites.net*' dir. App Service 'e açıkça kayıtlı olmayan bir ana bilgisayar adı aracılığıyla veya uygulama ağ geçidinin FQDN 'SI aracılığıyla uygulama hizmetinize erişmek için, özgün istekteki ana bilgisayar adını App Service 'in ana bilgisayar adına geçersiz kılarsınız. Bunu yapmak için, **arka uç adres ayarından seçim ana bilgisayar adını** etkinleştirin.

Var olan özel DNS adı App Service 'e eşlenmiş özel bir etki alanı için bu ayarı etkinleştirmeniz gerekmez.

> [!NOTE]
> Bu ayar, adanmış bir dağıtım olan App Service Ortamı için gerekli değildir.

### <a name="host-name-override"></a>Ana bilgisayar adı geçersiz kılma

Bu özellik, uygulama ağ geçidinde gelen istekteki *ana bilgisayar* üstbilgisinin yerine belirttiğiniz ana bilgisayar adını koyar.

Örneğin, *www.contoso.com* **konak adı** ayarında belirtilmişse, istek arka uç sunucusuna iletildiğinde, * https://appgw.eastus.cloudapp.azure.com/path1 özgün isteği * https://www.contoso.com/path1 olarak değiştirilir.

## <a name="back-end-pool"></a>Arka uç havuzu

Arka uç havuzunu dört tür arka uç üyesine işaret edebilirsiniz: belirli bir sanal makine, bir sanal makine ölçek kümesi, IP adresi/FQDN veya App Service. Her bir arka uç havuzu aynı türdeki birden çok üyeye işaret edebilir. Aynı arka uç havuzundaki farklı türlerin üyelerine işaret etmek desteklenmez.

Bir arka uç havuzu oluşturduktan sonra, bir veya daha fazla istek yönlendirme kuralıyla ilişkilendirmeniz gerekir. Ayrıca, uygulama ağ geçidinizdeki her bir arka uç havuzu için sistem durumu araştırmalarını da yapılandırmanız gerekir. İstek yönlendirme kuralı koşulu karşılandığında, Application Gateway trafiği ilgili arka uç havuzundaki sağlıklı sunuculara (sistem durumu araştırmaları tarafından belirlendiği şekilde) iletir.

## <a name="health-probes"></a>Sistem durumu araştırmaları

Bir Application Gateway, varsayılan olarak arka uçtaki tüm kaynakların sistem durumunu izler. Ancak sistem durumu izleme üzerinde daha fazla denetim sağlamak için her bir arka uç HTTP ayarı için özel bir araştırma oluşturmanız önemle tavsiye ederiz. Özel bir araştırmanın nasıl yapılandırılacağını öğrenmek için bkz. [özel durum araştırma ayarları](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#custom-health-probe-settings).

> [!NOTE]
> Özel bir sistem durumu araştırması oluşturduktan sonra, bir arka uç HTTP ayarıyla ilişkilendirmeniz gerekir. Karşılık gelen HTTP ayarı bir kural kullanan bir dinleyiciyle açıkça ilişkilendirilmediği takdirde, özel bir araştırma arka uç havuzunun sistem durumunu izlemez.

## <a name="next-steps"></a>Sonraki adımlar

Artık Application Gateway bileşenleri hakkında bilgi edineceğimize göre şunları yapabilirsiniz:

- [Azure portal bir uygulama ağ geçidi oluşturun](quick-create-portal.md)
- [PowerShell kullanarak uygulama ağ geçidi oluşturma](quick-create-powershell.md)
- [Azure CLı kullanarak uygulama ağ geçidi oluşturma](quick-create-cli.md)
