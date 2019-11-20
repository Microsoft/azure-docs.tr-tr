---
title: Azure Application Gateway nedir?
description: Uygulamanıza web trafiğini yönetmek için bir Azure uygulama ağ geçidini nasıl kullanabileceğinizi öğrenin.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: overview
ms.custom: mvc
ms.date: 11/05/2019
ms.author: victorh
ms.openlocfilehash: a51276cd54a88c5e566afef0d06673f14b5862cf
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73572027"
---
# <a name="what-is-azure-application-gateway"></a>Azure Application Gateway nedir?

Azure Application Gateway, web uygulamalarınıza trafiği yönetmenizi sağlayan bir web trafiği yük dengeleyicisidir. Geleneksel yük dengeleyiciler aktarım katmanında (OSI katman 4 - TCP ve UDP) çalışır ve trafiği kaynak IP adresi ve bağlantı noktasına göre hedef bir IP adresi ve bağlantı noktasına yönlendirir.

![Application Gateway kavramsal](media/overview/figure1-720.png)

Application Gateway ile, URI yolu veya konak üstbilgileri gibi bir HTTP isteğinin ek özniteliklerine dayalı yönlendirme kararları alabilirsiniz. Örneğin, gelen URL’yi temel alarak trafiği yönlendirebilirsiniz. Yani `/images` gelen URL’deyse, trafiği görüntüler için yapılandırılmış belirli bir sunucu kümesine (havuz olarak da bilinir) yönlendirebilirsiniz. URL 'de `/video`, bu trafik, videolar için en iyi duruma getirilmiş başka bir havuza yönlendirilir.

![imageURLroute](./media/application-gateway-url-route-overview/figure1-720.png)

Bu yönlendirme türü, uygulama katmanı (OSI katman 7) yük dengelemesi olarak bilinir. Azure Application Gateway, URL tabanlı yönlendirmeyi ve daha fazlasını yapabilir.

Azure Application Gateway aşağıdaki özellikleri içerir:

## <a name="secure-sockets-layer-ssltls-termination"></a>Güvenli Yuva Katmanı (SSL/TLS) sonlandırma

Application Gateway, ağ geçidinde SSL/TLS sonlandırmasını destekler ve bu sayede trafik genellikle arka uç sunucularına şifrelenmemiş olarak akar. Bu özellik, web sunucularının maliyetli şifreleme ve şifre çözme ek yükünden kurtulmasını sağlar. Ancak bazen sunucularla şifrelenmemiş iletişim kabul edilebilir bir seçenek değildir. Bunun nedeni güvenlik gereksinimleri, uyumluluk gereksinimleri veya uygulamanın yalnızca güvenli bir bağlantıyı kabul etmesi olabilir. Bu uygulamalar için Application Gateway, uçtan uca SSL/TLS şifrelemesini destekler.

## <a name="autoscaling"></a>Otomatik ölçeklendirme

Standard_v2 veya WAF_v2 SKU altında bulunan Application Gateway veya WAF dağıtımları otomatik ölçeklendirmeyi destekler ve değişen trafik yükü desenlerine göre ölçeği değiştirebilir veya azaltabilirsiniz. Otomatik ölçeklendirme ayrıca sağlama sırasında dağıtım boyutu veya örnek sayısı seçme gereksinimini de ortadan kaldırır. Application Gateway Standard_v2 ve WAF_v2 özellikleri hakkında daha fazla bilgi için bkz. [Otomatik ölçeklendirme v2 SKU 'su](application-gateway-autoscaling-zone-redundant.md).

## <a name="zone-redundancy"></a>Bölge artıklığı

Standard_v2 veya WAF_v2 SKU altındaki bir Application Gateway veya WAF dağıtımları birden fazla Kullanılabilirlik Alanları yayarak, daha iyi hataya dayanıklılık sunar ve her bölgede ayrı uygulama ağ geçitleri sağlama gereksinimini ortadan kaldırabilir.

## <a name="static-vip"></a>Statik VIP

Standard_v2 veya WAF_v2 SKU 'SU üzerinde Application Gateway VIP yalnızca statik VIP türünü destekler. Bu, uygulama ağ geçidi ile ilişkili VIP 'nin Application Gateway ömrü boyunca bile değişmemesini sağlar.

## <a name="web-application-firewall"></a>Web uygulaması güvenlik duvarı

Web uygulaması güvenlik duvarı (WAF), Web uygulamalarınızın genel güvenlik açıklarından ve güvenlik açıklarından merkezi bir şekilde korunmasını sağlayan bir hizmettir. WAF, [OWASP (açık Web uygulaması güvenlik projesi) çekirdek kural kümeleri](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3,1 (yalnızca WAF_v2), 3,0 ve 2.2.9 içindeki kurallara dayalıdır. 

Web uygulamaları, bilinen yaygın güvenlik açıklarından yararlanan kötü amaçlı saldırıların giderek daha fazla hedefi olmaktadır. Bu açıklardan yararlanma örnekleri arasında SQL ekleme saldırıları, siteler arası komut dosyası saldırıları yaygındır. Uygulama kodunda bu tür saldırıların önlenmesi zor olabilir ve uygulama topolojisinin birçok katmanında ayrıntılı bakım, düzeltme eki uygulama ve izleme işlemleri gerektirebilir. Merkezi bir web uygulaması güvenlik duvarı, güvenlik yönetimini çok daha kolay hale getirir ve yetkisiz erişim ya da izinsiz giriş tehditlerine karşı uygulama yöneticilerine daha iyi güvence verir. Bir WAF çözümü, bilinen bir güvenlik açığına merkezi bir konumda düzeltme eki uygulayarak güvenlik tehdidine karşı, web uygulamalarının her birinin güvenliğini sağlamaya göre daha hızlı tepki verebilir. Var olan uygulama ağ geçitleri, web uygulaması güvenlik duvarı bulunan bir uygulama ağ geçidine kolaylıkla dönüştürülebilir.

Daha fazla bilgi için bkz. [Azure Web uygulaması güvenlik duvarı nedir?](../web-application-firewall/overview.md).

## <a name="ingress-controller-for-aks"></a>AKS için giriş denetleyicisi
Application Gateway giriş denetleyicisi (AGIC), bir [Azure Kubernetes hizmeti (AKS)](https://azure.microsoft.com/services/kubernetes-service/) kümesi için giriş olarak Application Gateway kullanmanıza olanak sağlar. 

Giriş denetleyicisi AKS kümesi içinde bir pod olarak çalışır ve [Kubernetes giriş kaynaklarını](https://kubernetes.io/docs/concepts/services-networking/ingress/) kullanır ve ağ geçidinin Kubernetes pods 'ye yük dengelemesi yapmasına izin veren bir Application Gateway yapılandırmasına dönüştürür. Giriş denetleyicisi yalnızca Application Gateway v2 SKU 'sunu destekler. 

Daha fazla bilgi için bkz. [Application Gateway giriş denetleyicisi (AGIC)](ingress-controller-overview.md).

## <a name="url-based-routing"></a>URL tabanlı yönlendirme

URL Yolu Tabanlı Yönlendirme, trafiği isteğin URL Yollarına göre arka uç sunucu havuzlarına yönlendirmenizi sağlar. Senaryolardan biri, farklı içerik türleri için istekleri farklı havuzlara yönlendirmektir.

Örneğin, `http://contoso.com/video/*` için istekler VideoServerPool’a ve `http://contoso.com/images/*` için istekler ImageServerPool’a yönlendirilir. Yol desenlerinden hiçbiri eşleşmiyorsa DefaultServerPool seçilir.

Daha fazla bilgi için bkz. [Application Gateway Ile URL tabanlı yönlendirme](https://docs.microsoft.com/azure/application-gateway/url-route-overview).

## <a name="multiple-site-hosting"></a>Birden çok site barındırma

Birden çok site barındırma, aynı uygulama ağ geçidi örneğinde birden fazla web sitesi yapılandırmanızı sağlar. Bu özellik, bir Application Gateway veya WAF için 40 (en iyi performans için) 100 web sitesi ekleyerek dağıtımlarınız için daha verimli bir topoloji yapılandırmanıza olanak tanır. Her web sitesi kendi havuzuna yönlendirilebilir. Örneğin, uygulama ağ geçidi ContosoServerPool ve FabrikamServerPool adlı iki sunucu havuzundan `contoso.com` ve `fabrikam.com` için trafik sunabilir.

`http://contoso.com` için istekler ContosoServerPool’a ve `http://fabrikam.com` için istekler FabrikamServerPool’a yönlendirilir.

Benzer şekilde, aynı üst etki alanının iki alt etki alanı, aynı uygulama ağ geçidi dağıtımında barındırılabilir. Alt etki alanı kullanım örnekleri, tek bir uygulama ağ geçidi dağıtımında barındırılan `http://blog.contoso.com` ve `http://app.contoso.com` öğelerini içerebilir.

Daha fazla bilgi için bkz. [Application Gateway ile birden çok site barındırma](https://docs.microsoft.com/azure/application-gateway/multiple-site-overview).

## <a name="redirection"></a>Yönlendirme

Birçok web uygulaması için yaygın bir senaryo, bir uygulama ile kullanıcıları arasındaki tüm iletişimin şifrelenmiş bir yol üzerinden yapıldığından emin olmak için otomatik HTTP’yi HTTPS’ye dönüştürme yeniden yönlendirmesini desteklemektir.

Geçmişte, tek amacı, HTTP üzerinde aldığı istekleri HTTPS 'ye yeniden yönlendirmek üzere olan adanmış havuz oluşturma gibi teknikler kullanmış olabilirsiniz. Uygulama ağ geçidi, Application Gateway’de trafiği yeniden yönlendirme özelliğini destekler. Bu uygulama yapılandırmasını basitleştirir, kaynak kullanımını en iyi duruma getirir ve genel ve yol tabanlı yeniden yönlendirme dahil yeni yeniden yönlendirme senaryolarını destekler. Application Gateway yeniden yönlendirme desteği, tek başına HTTP ile HTTPS yönlendirmesi ile sınırlı değildir. Bu, kuralları kullanarak tanımladığınız herhangi bir bağlantı noktasından ve bağlantı noktasına yeniden yönlendirebilmeniz için genel bir yeniden yönlendirme mekanizmasıdır. Ayrıca, bir dış siteye yönlendirmeyi de destekler.

Application Gateway yeniden yönlendirme desteği aşağıdaki özellikleri sunar:

- Ağ Geçidi üzerindeki bir bağlantı noktasından başka bir bağlantı noktasına genel yeniden yönlendirme. Bu özellik, bir sitede HTTP’den HTTPS’ye yeniden yönlendirmeyi sağlar.
- Yol tabanlı yönlendirme. Bu tür yeniden yönlendirmeler, HTTP’den HTTPS’ye yeniden yönlendirmeyi yalnızca belirli bir site alanında (örneğin `/cart/*` tarafından belirtilen bir alışveriş sepetinde) etkinleştirir.
- Dış siteye yeniden yönlendirme.

Daha fazla bilgi için bkz. Application Gateway ile [trafiği yeniden yönlendirme](https://docs.microsoft.com/azure/application-gateway/redirect-overview) .

## <a name="session-affinity"></a>Oturum benzeşimi

Tanımlama bilgilerine dayalı oturum benzeşimi özelliği, bir kullanıcı oturumunu aynı sunucuda tutmak istediğinizde kullanışlıdır. Ağ geçidi ile yönetilen tanımlama bilgilerini kullanan Application Gateway, sonraki trafiği işleme amacıyla bir kullanıcı oturumundan aynı sunucuya yönlendirebilir. Bu, oturum durumunun bir kullanıcı oturumuna ait sunucuya yerel olarak kaydedildiği durumlarda önemlidir.

## <a name="websocket-and-http2-traffic"></a>Websocket ve HTTP/2 trafiği

Application Gateway, WebSocket ve HTTP/2 protokolleri için yerel destek sağlar. WebSocket desteğini isteğe bağlı olarak etkinleştirmek veya devre dışı bırakmak için kullanıcı tarafından yapılandırılabilen bir ayar yoktur.

WebSocket ve HTTP/2 protokolleri, uzun süre çalışan bir TCP bağlantısı üzerinden bir sunucu ile bir istemci arasında tam çift yönlü iletişimi etkinleştirir. Bu, web sunucusu ile istemci arasında HTTP tabanlı uygulamalarda gerektiği gibi yoklama olmadan çift yönlü olabilen daha etkileşimli bir iletişime olanak sağlar. Bu protokollerin, HTTP 'den farklı olarak düşük bir yükü vardır ve birden çok istek/yanıt için aynı TCP bağlantısını daha verimli bir kaynak kullanımına neden olabilir. Bu protokoller, geleneksel HTTP bağlantı noktaları 80 ve 443 üzerinden çalışmak üzere tasarlanmıştır.

Daha fazla bilgi için bkz. [WebSocket desteği](https://docs.microsoft.com/azure/application-gateway/application-gateway-websocket) ve [http/2 desteği](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http2-support).

## <a name="connection-draining"></a>Bağlantı boşaltma

Bağlantı boşaltma, planlı hizmet güncelleştirmeleri sırasında arka uç havuzu üyelerinin normal bir şekilde kapatılmasına yardımcı olur. Bu ayar bir arka uç http ayarıyla etkinleştirilir ve kural oluşturma sırasında bir arka uç havuzunun tüm üyelerine uygulanabilir. Etkinleştirildikten sonra Application Gateway, bir arka uç havuzunun tüm kayıt kaldırma örneklerinin yeni bir istek almamasını sağlarken, mevcut isteklerin yapılandırılmış bir süre sınırı içinde tamamlanmasını sağlar. Bu, arka uç havuzundan bir API çağrısıyla açıkça kaldırılan arka uç örnekleri için ve sistem durumu araştırmaları tarafından belirlendiği şekilde sağlıksız olarak bildirilen arka uç örnekleri için geçerlidir.

Daha fazla bilgi için [Application Gateway yapılandırmasına genel bakış](https://docs.microsoft.com/azure/application-gateway/configuration-overview#connection-draining)' ın bağlantı boşaltma bölümüne bakın.

## <a name="custom-error-pages"></a>Özel hata sayfaları

Application Gateway, varsayılan hata sayfalarını göstermek yerine özel hata sayfaları oluşturmanızı sağlar. Özel hata sayfası sayesinde kendi logonuzu ve sayfa düzeninizi kullanabilirsiniz.

Daha fazla bilgi için bkz. [özel hatalar](custom-error.md).

## <a name="rewrite-http-headers"></a>HTTP üst bilgilerini yeniden üretme

HTTP üstbilgileri, istemci ve sunucunun istek veya Yanıt ile ek bilgi geçmesine izin verir. Bu HTTP üstbilgilerini yeniden yazmak, aşağıdaki gibi çeşitli önemli senaryolar gerçekleştirmenize yardımcı olur:

- HSTS/X-XSS-Protection gibi güvenlikle ilgili üst bilgi alanları ekleme.
- Hassas bilgileri açığa çıkartan yanıt üst bilgisi alanlarını kaldırma.
- Üst bilgiler Için X-Iletilen bağlantı noktası bilgilerini yakdırın.

Application Gateway, HTTP isteği ve yanıt üst bilgilerini ekleme, kaldırma veya güncelleştirme özelliğini destekler, ancak istek ve yanıt paketleri istemci ile arka uç havuzları arasında taşınır. Ayrıca, belirtilen üstbilgilerin yalnızca belirli koşullar karşılandığında yeniden yazılabilir olmasını sağlamak için koşullar ekleme özelliği de sağlar.

Daha fazla bilgi için bkz. [HTTP üstbilgilerini yeniden yazma](rewrite-http-headers.md).

## <a name="sizing"></a>Boyutlandırma

Application Gateway Standard_v2 ve WAF_v2 SKU, otomatik ölçeklendirme veya sabit boyutlu dağıtımlar için yapılandırılabilir. Bu SKU 'Lar farklı örnek boyutları sunmaz. V2 performansı ve fiyatlandırması hakkında daha fazla bilgi için bkz. [Otomatik ölçeklendirme v2 SKU 'su](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant#pricing).

Application Gateway standart ve WAF SKU Şu anda üç boyutta sunulmaktadır: **küçük**, **Orta**ve **büyük**. Küçük örnek boyutları, geliştirme ve test senaryolarına yöneliktir.

Application Gateway limitlerinin tam listesi için bkz. [Application Gateway hizmet limitleri](../azure-subscription-service-limits.md?toc=%2fazure%2fapplication-gateway%2ftoc.json#application-gateway-limits).

Aşağıdaki tabloda SSL boşaltması etkin olan her bir Application Gateway v1 örneği için ortalama performans performansı gösterilmektedir:

| Ortalama arka uç sayfa yanıt boyutu | Küçük | Orta | Büyük |
| --- | --- | --- | --- |
| 6 KB |7,5 Mbps |13 Mbps |50 Mbps |
| 100 KB |35 Mbps |100 Mb/sn |200 Mb/sn |

> [!NOTE]
> Bu değerler bir uygulama ağ geçidi verimliliği için yaklaşık değerlerdir. Gerçek verimlilik; ortalama sayfa boyutu, arka uç örneklerinin konumu ve bir sayfaya hizmet etmek için işleme süresi gibi çeşitli ortam ayrıntılarına bağlıdır. Tam performans rakamlarına ulaşmak için kendi testlerinizi çalıştırmanız gerekir. Bu değerler yalnızca kapasite planlama konusunda yardımcı olmak için verilmiştir.

## <a name="next-steps"></a>Sonraki adımlar

Gereksinimleriniz ve ortamınıza bağlı olarak, Azure portalı, Azure PowerShell veya Azure CLI kullanarak bir test Application Gateway oluşturabilirsiniz:

- [Hızlı başlangıç: Azure Application Gateway ile doğrudan web trafiği-Azure portal](quick-create-portal.md)
- [Hızlı Başlangıç: Azure Application Gateway ile web trafiğini yönlendirme - Azure PowerShell](quick-create-powershell.md)
- [Hızlı Başlangıç: Azure Application Gateway ile web trafiğini yönlendirme - Azure CLI](quick-create-cli.md)
