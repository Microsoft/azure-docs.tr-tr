---
title: HTTP üstbilgilerini Azure Application Gateway yeniden yazın | Microsoft Docs
description: Bu makalede, Azure Application Gateway HTTP üstbilgilerini yeniden yazma hakkında genel bir bakış sunulmaktadır
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 08/08/2019
ms.author: absha
ms.openlocfilehash: b6f26eca0592017306eaefd3f5fecb544dc6fb36
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68932203"
---
# <a name="rewrite-http-headers-with-application-gateway"></a>HTTP üstbilgilerini Application Gateway yeniden yazın

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

HTTP üstbilgileri, bir istemci ve sunucunun bir istek veya Yanıt ile ek bilgi geçmesini sağlar. Bu üstbilgileri yeniden yazarak, HSTS/X-XSS-Protection gibi güvenlikle ilgili üst bilgi alanlarını ekleme, hassas bilgileri açığa çıkarmış olan yanıt üst bilgisi alanlarını kaldırma ve bağlantı noktası bilgilerini kaldırma gibi önemli görevleri gerçekleştirebilirsiniz. X-Iletildi-üstbilgiler Için.

Application Gateway, istek ve yanıt paketleri istemci ile arka uç havuzları arasında gidip gelirken HTTP istek ve yanıt üst bilgileri eklemenize ya da mevcut bilgileri kaldırmanıza veya güncelleştirmenize olanak tanır. Ayrıca, ancak bazı koşulların yerine getirilmesi durumunda belirtilen üst bilgilerin yeniden yazılmasını sağlamak için koşullar eklemenize de olanak tanır.

Application Gateway Ayrıca istekler ve yanıtlar hakkında ek bilgi depolamanıza yardımcı olan çeşitli [sunucu değişkenlerini](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers#server-variables) destekler. Bu, güçlü yeniden yazma kuralları oluşturmanızı kolaylaştırır.

> [!NOTE]
>
> HTTP üst bilgisi yeniden yazma desteği yalnızca [Standard_V2 ve WAF_v2 SKU 'su](application-gateway-autoscaling-zone-redundant.md)için kullanılabilir.

![Üstbilgileri yeniden yazma](media/rewrite-http-headers/rewrite-headers.png)

## <a name="supported-headers"></a>Desteklenen üstbilgiler

Konak, bağlantı ve yükseltme üstbilgileri dışında tüm üst bilgileri istek ve yanıtlara yeniden yazabilirsiniz. Ayrıca, uygulama ağ geçidini kullanarak özel üstbilgiler oluşturabilir ve bunlara yönlendirilmekte olan isteklere ve yanıtlara ekleyebilirsiniz.

## <a name="rewrite-conditions"></a>Yeniden yazma koşulları

HTTP (S) isteklerinin ve yanıtlarının içeriğini değerlendirmek ve yalnızca bir veya daha fazla koşul karşılandığında bir üstbilgi yeniden yazma işlemi gerçekleştirmek için yeniden yazma koşullarını kullanabilirsiniz. Application Gateway, HTTP (S) isteklerinin ve yanıtlarının içeriğini değerlendirmek için bu tür değişkenleri kullanır:

- İstekteki HTTP üst bilgileri.
- Yanıttaki HTTP üst bilgileri.
- Sunucu değişkenlerini Application Gateway.

Belirtilen değişkenin mevcut olup olmadığını, belirtilen değişkenin belirli bir değerle eşleşip eşleşmediğini veya belirtilen değişkenin belirli bir Düzenle eşleşip eşleşmediğini değerlendirmek için bir koşul kullanabilirsiniz. Koşullardaki normal ifade deseninin eşleşmesini ayarlamak için [Perl uyumlu normal ifadeler (PCRE) kitaplığını](https://www.pcre.org/) kullanırsınız. Normal ifade söz dizimi hakkında bilgi edinmek için bkz. [Perl normal ifadeler ana sayfası](https://perldoc.perl.org/perlre.html).

## <a name="rewrite-actions"></a>Yeniden yazma eylemleri

Yeniden yazmak istediğiniz istek ve yanıt üst bilgilerini ve üst bilgilerin yeni değerini belirtmek için yeniden yazma eylemleri kullanın. Yeni bir üst bilgi oluşturabilir, var olan bir üst bilginin değerini değiştirebilir veya var olan bir üst bilgiyi silebilirsiniz. Yeni bir üst bilgi veya var olan bir üst bilgi, bu değer türlerine ayarlanabilir:

- Metinleri.
- İstek üst bilgisi. Bir istek üst bilgisi belirtmek için {http_req_*HeaderName*} sözdizimini kullanmanız gerekir.
- Yanıt üst bilgisi. Yanıt üst bilgisi belirtmek için {http_resp_*HeaderName*} sözdizimini kullanmanız gerekir.
- Sunucu değişkeni. Bir sunucu değişkeni belirtmek için, {var_*Servervariable*} sözdizimini kullanmanız gerekir.
- Metin, istek üst bilgisi, yanıt üst bilgisi ve sunucu değişkeni birleşimi.

## <a name="server-variables"></a>Sunucu değişkenleri

Application Gateway sunucu, istemciyle bağlantı ve bağlantıdaki geçerli istek hakkındaki yararlı bilgileri depolamak için sunucu değişkenlerini kullanır. Depolanan bilgilere örnek olarak istemcinin IP adresi ve Web tarayıcı türü dahildir. Sunucu değişkenleri dinamik olarak değişir; Örneğin, yeni bir sayfa yüklendiğinde veya bir form gönderildiğinde. Yeniden yazma koşullarını değerlendirmek ve üstbilgileri yeniden yazmak için bu değişkenleri kullanabilirsiniz.

Application Gateway bu sunucu değişkenlerini destekler:

| Değişken adı | Açıklama                                                  |
| -------------------------- | :----------------------------------------------------------- |
| add_x_forwarded_for_proxy  | IP1, IP2, IP3, vb. biçiminde bu `client_ip` değişkene eklenen (Bu tablodaki açıklamaya bakın) X-iletilmiş istemci isteği üst bilgisi alanı. X-iletilmiş-for alanı istemci isteği üstbilgisinde yoksa, `add_x_forwarded_for_proxy` değişken `$client_ip` değişkenine eşittir. Bu değişken özellikle, üstbilginin yalnızca bağlantı noktası bilgisi olmadan yalnızca IP adresini içermesi için, Application Gateway tarafından ayarlanmış X-Iletilmiş-for üst bilgisini yeniden yazmak istediğinizde yararlıdır. |
| ciphers_supported          | İstemci tarafından desteklenen şifrelemelerin listesi.          |
| ciphers_used               | Kurulan bir SSL bağlantısı için kullanılan şifre dizeleri. |
| client_ip                  | Uygulama ağ geçidinin isteği aldığı istemcinin IP adresi. Uygulama ağ geçidi ve kaynak istemciden önce ters bir ara sunucu varsa, *client_ip* ters proxy 'nin IP adresini döndürür. |
| client_port                | İstemci bağlantı noktası.                                                  |
| client_tcp_rtt             | İstemci TCP bağlantısıyla ilgili bilgiler. TCP_INFO yuva seçeneğini destekleyen sistemlerde kullanılabilir. |
| client_user                | HTTP kimlik doğrulaması kullanıldığında, kimlik doğrulaması için sağlanan Kullanıcı adı. |
| host                       | Öncelik sırasına göre: istek satırından ana bilgisayar adı, konak istek üst bilgisi alanından ana bilgisayar adı veya bir istekle eşleşen sunucu adı. |
| cookie_*adı*              | *Ad* tanımlama bilgisi.                                            |
| http_method                | URL isteğini yapmak için kullanılan yöntem. Örneğin, GET veya POST. |
| http_status                | Oturum durumu. Örneğin, 200, 400 veya 403.                       |
| http_version               | İstek Protokolü. Genellikle HTTP/1.0, HTTP/1.1 veya HTTP/2.0. |
| query_string               | İstenen URL 'de "?" öğesini izleyen değişken/değer çiftleri listesi. |
| received_bytes             | İsteğin uzunluğu (istek satırı, üst bilgi ve istek gövdesi dahil). |
| request_query              | İstek satırındaki bağımsız değişkenler.                                |
| request_scheme             | İstek düzeni: http veya https.                            |
| request_uri                | Tüm özgün istek URI 'SI (bağımsız değişkenlerle).                   |
| sent_bytes                 | Bir istemciye gönderilen bayt sayısı.                             |
| server_port                | Bir isteği kabul eden sunucunun bağlantı noktası.                 |
| ssl_connection_protocol    | Kurulan bir SSL bağlantısının protokolü.        |
| ssl_enabled                | Bağlantı SSL modunda çalışıyorsa "açık". Aksi takdirde, boş bir dize. |

## <a name="rewrite-configuration"></a>Yeniden yazma yapılandırması

HTTP üstbilgisini yeniden yazmayı yapılandırmak için, bu adımları gerçekleştirmeniz gerekir.

1. HTTP üstbilgisi yeniden yazma için gereken nesneleri oluşturun:

   - **Yeniden yazma eylemi**: Yeniden yazmak istediğiniz istek ve istek üst bilgisi alanlarını ve üst bilgilerin yeni değerini belirtmek için kullanılır. Yeniden yazma eylemiyle bir veya daha fazla yeniden yazma koşulu ilişkilendirebilirsiniz.

   - **Yeniden yazma koşulu**: İsteğe bağlı bir yapılandırma. Yeniden yazma koşulları, HTTP (S) isteklerinin ve yanıtlarının içeriğini değerlendirir. HTTP (S) isteği veya yanıtı yeniden yazma koşuluyla eşleşiyorsa, yeniden yazma eylemi gerçekleşir.

     Birden fazla koşulu bir eylemle ilişkilendirirseniz, eylem yalnızca tüm koşullar karşılandığında oluşur. Diğer bir deyişle, işlem mantıksal ve işlemdir.

   - **Yeniden yazma kuralı**: Birden çok yeniden yazma eylemi/yeniden yazma koşulu birleşimleri içerir.

   - **Kural sırası**: Yeniden yazma kurallarının yürütme sırasını belirlemesine yardımcı olur. Bu yapılandırma, bir yeniden yazma kümesinde birden fazla yeniden yazma kuralına sahip olduğunuzda yararlıdır. Daha düşük bir kural sırası değeri olan bir yeniden yazma kuralı önce çalışır. Aynı kural sırasını iki yeniden yazma kuralına atarsanız, yürütme sırası belirleyici değildir.

   - **Yeniden yazma kümesi**: İstek yönlendirme kuralıyla ilişkilendirilecek çoklu yeniden yazma kuralları içerir.

2. Yeniden yazma kümesini (*Rewriterutaset*) bir yönlendirme kuralına ekleyin. Yeniden yazma yapılandırması, kaynak dinleyicisine yönlendirme kuralı aracılığıyla eklenir. Temel bir yönlendirme kuralı kullandığınızda, üst bilgi yeniden yazma yapılandırması bir kaynak dinleyicisi ile ilişkilendirilir ve genel üst bilgi yeniden yazma işlemi olur. Yol tabanlı bir yönlendirme kuralı kullandığınızda, üst bilgi yeniden yazma yapılandırması URL yol eşlemesinde tanımlanmıştır. Bu durumda, yalnızca bir sitenin belirli yol alanı için geçerlidir.
   > [!NOTE]
   > URL yeniden yazma, üstbilgileri Değiştir; yolun URL 'sini değiştirmez.

Birden çok HTTP üst bilgisi yeniden yazma kümesi oluşturabilir ve her bir yeniden yazma kümesini birden çok dinleyiciyle uygulayabilirsiniz. Ancak belirli bir dinleyiciye yalnızca bir yeniden yazma kümesi uygulayabilirsiniz.

## <a name="common-scenarios"></a>Genel senaryolar

Üst bilgi yeniden yazma kullanımına yönelik bazı yaygın senaryolar aşağıda verilmiştir.

### <a name="remove-port-information-from-the-x-forwarded-for-header"></a>Bağlantı noktası bilgilerini X-Iletilmiş-for üst bilgisinden kaldır

Application Gateway, istekleri arka uca iletmeden önce tüm isteklere X-Iletilmiş bir üst bilgi ekler. Bu üst bilgi, IP bağlantı noktalarının virgülle ayrılmış listesidir. Arka uç sunucularının yalnızca IP adreslerini içermesi için üstbilgilere ihtiyacı olan senaryolar olabilir. Bağlantı noktası bilgilerini X-Iletilmiş-for üst bilgisinden kaldırmak için üstbilgi yeniden yazma kullanabilirsiniz. Bunu yapmanın bir yolu, üst bilgiyi add_x_forwarded_for_proxy sunucu değişkenine ayarlamaya yönelik bir yoldur:

![Bağlantı noktasını kaldır](media/rewrite-http-headers/remove-port.png)

### <a name="modify-a-redirection-url"></a>Yeniden yönlendirme URL 'sini değiştirme

Bir arka uç uygulaması yeniden yönlendirme yanıtı gönderdiğinde, istemciyi arka uç uygulaması tarafından belirtilene göre farklı bir URL 'ye yönlendirmek isteyebilirsiniz. Örneğin, bir uygulama hizmeti bir uygulama ağ geçidinin arkasında barındırılıyorsa ve istemcinin göreli yoluna yeniden yönlendirme yapması için bunu yapmak isteyebilirsiniz. (Örneğin, contoso.azurewebsites.net/path1 öğesinden contoso.azurewebsites.net/path2 'e yeniden yönlendirme.)

App Service çok kiracılı bir hizmet olduğundan, isteği doğru uç noktaya yönlendirmek için istekteki ana bilgisayar üst bilgisini kullanır. Uygulama Hizmetleri, Application Gateway 'in etki alanı adından (deyin contoso.com) farklı olan *. azurewebsites.net (deyin contoso.azurewebsites.net) varsayılan etki alanı adına sahiptir. İstemciden gelen özgün istek ana bilgisayar adı olarak uygulama ağ geçidinin etki alanı adına (contoso.com) sahip olduğundan, Application Gateway ana bilgisayar adını contoso.azurewebsites.net olarak değiştirir. Bu değişiklik, App Service 'in isteği doğru uç noktaya yönlendirebilmesi için yapar.

App Service bir yeniden yönlendirme yanıtı gönderdiğinde, uygulamanın konum üstbilgisindeki ana bilgisayar adını uygulama ağ geçidinden aldığı istekte olduğu gibi kullanır. Bu nedenle, istemci, uygulama ağ geçidi (contoso.com/path2) boyunca değil, isteği doğrudan contoso.azurewebsites.net/path2 'a yapar. Application Gateway 'i atlamak istenmez.

Konum üstbilgisindeki ana bilgisayar adını Application Gateway 'in etki alanı adına ayarlayarak bu sorunu çözebilirsiniz.

Burada, ana bilgisayar adını değiştirme adımları verilmiştir:

1. Yanıttaki konum üstbilgisinin azurewebsites.net içerip içermeyeceğini değerlendiren bir koşul içeren bir yeniden yazma kuralı oluşturun. Kalıbı `(https?):\/\/.*azurewebsites\.net(.*)$`girin.
1. Uygulama ağ geçidinin ana bilgisayar adına sahip olması için konum başlığını yeniden yazmak üzere bir eylem gerçekleştirin. Bunu, üst bilgi `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` değeri olarak girerek yapın.

![Konum üst bilgisini Değiştir](media/rewrite-http-headers/app-service-redirection.png)

### <a name="implement-security-http-headers-to-prevent-vulnerabilities"></a>Güvenlik açıklarını engellemek için güvenlik HTTP üst bilgilerini uygulayın

Uygulama yanıtında gerekli üst bilgileri uygulayarak çeşitli güvenlik açıklarını giderebilirsiniz. Bu güvenlik üstbilgileri, X-XSS-Protection, katı aktarım güvenliği ve Content-Security-Policy ' i içerir. Tüm yanıtlar için bu üst bilgileri ayarlamak üzere Application Gateway kullanabilirsiniz.

![Güvenlik üst bilgisi](media/rewrite-http-headers/security-header.png)

### <a name="delete-unwanted-headers"></a>İstenmeyen üstbilgileri Sil

Bir HTTP yanıtından hassas bilgileri açığa çıkarmak için üstbilgileri kaldırmak isteyebilirsiniz. Örneğin, arka uç sunucu adı, işletim sistemi veya kitaplık ayrıntıları gibi bilgileri kaldırmak isteyebilirsiniz. Bu üst bilgileri kaldırmak için Application Gateway kullanabilirsiniz:

![Üst bilgi siliniyor](media/rewrite-http-headers/remove-headers.png)

### <a name="check-for-the-presence-of-a-header"></a>Üstbilginin varolup olmadığını denetle

Bir üst bilgi veya sunucu değişkeni varlığı için bir HTTP isteği veya yanıt üst bilgisini değerlendirebilirsiniz. Bu değerlendirme yalnızca belirli bir başlık mevcut olduğunda bir üstbilgi yeniden yazma işlemi gerçekleştirmek istediğinizde faydalıdır.

![Üst bilgi bulunup bulunmadığını denetleme](media/rewrite-http-headers/check-presence.png)

## <a name="limitations"></a>Sınırlamalar

- Bir yanıtta aynı ada sahip birden fazla üst bilgi varsa, bu üst bilgilerden birinin değerini yeniden yazmak yanıttaki diğer üstbilgilerin bırakılmasına neden olur. Bu, genellikle bir yanıtta birden fazla set-Cookie üst bilgisine sahip olabileceğinizden, set-Cookie üst bilgisinde meydana gelebilir. Bu tür bir senaryo, uygulama ağ geçidi ile uygulama hizmeti kullanırken ve uygulama ağ geçidinde tanımlama bilgisi tabanlı oturum benzeşimi yapılandırmış olduğunuz bir senaryodur. Bu durumda, yanıt iki set-Cookie üst bilgisi içerir: bir App Service tarafından kullanılan, örneğin: `Set-Cookie: ARRAffinity=ba127f1caf6ac822b2347cc18bba0364d699ca1ad44d20e0ec01ea80cda2a735;Path=/;HttpOnly;Domain=sitename.azurewebsites.net` ve uygulama ağ geçidi benzeşimi için bir diğeri (örneğin, `Set-Cookie: ApplicationGatewayAffinity=c1a2bd51lfd396387f96bl9cc3d2c516; Path=/`). Bu senaryodaki set-Cookie başlıklarından birini yeniden yazmak, diğer set-Cookie üst bilgisinin yanıttan kaldırılmasına neden olabilir.

- Bağlantı, yükseltme ve ana bilgisayar üst bilgilerini yeniden yazma Şu anda desteklenmiyor.

- Üst bilgi adları, [RFC 7230](https://tools.ietf.org/html/rfc7230#page-27)' de tanımlanan alfasayısal karakterleri ve belirli sembolleri içerebilir. Şu anda üstbilgi adlarında alt çizgi (\_) özel karakterini desteklemiyoruz.

## <a name="next-steps"></a>Sonraki adımlar

HTTP üstbilgilerini yeniden yazmayı öğrenmek için bkz.:

- [Azure portal kullanarak HTTP üstbilgilerini yeniden yazma](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers-portal)
- [Azure PowerShell kullanarak HTTP üstbilgilerini yeniden yazma](add-http-header-rewrite-rule-powershell.md)
