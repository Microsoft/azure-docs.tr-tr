---
title: Microsoft Identity platform için uygulama türleri | Mavisi
description: Microsoft Identity platformu tarafından desteklenen uygulama ve senaryoların türleri.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 11/13/2020
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev, fasttrack-edit, contperf-fy21q2
ms.openlocfilehash: 7ec309f016e73642262399bd75e7b5146bc5e497
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98752780"
---
# <a name="application-types-for-the-microsoft-identity-platform"></a>Microsoft Identity platformu için uygulama türleri

Microsoft Identity platformu, çeşitli modern uygulama mimarilerinin kimlik doğrulamasını destekler. Bu, endüstri standardı protokoller [OAuth 2,0 veya OpenID Connect](active-directory-v2-protocols.md)' i temel alır. Bu makalede, tercih ettiğiniz dil veya platformdan bağımsız olarak Microsoft Identity platform kullanarak oluşturabileceğiniz uygulama türleri açıklanmaktadır. Bilgiler, [uygulama senaryolarında](authentication-flows-app-scenarios.md#application-scenarios)kodla çalışmaya başlamadan önce üst düzey senaryoları anlamanıza yardımcı olmak için tasarlanmıştır.

## <a name="the-basics"></a>Temel bilgiler

Azure portal [uygulama kayıtları](https://go.microsoft.com/fwlink/?linkid=2083908)Microsoft Identity platformunu kullanan her uygulamayı kaydetmeniz gerekir. Uygulama kayıt işlemi, uygulamanız için bu değerleri toplar ve atar:

* Uygulamanızı benzersiz bir şekilde tanımlayan bir **uygulama (istemci) kimliği**
* Yanıtları uygulamanıza geri yönlendirmek için kullanabileceğiniz bir **yeniden yönlendirme URI 'si**
* Desteklenen hesap türleri gibi diğer senaryoya özgü birkaç değer

Ayrıntılar için, [bir uygulamayı nasıl kaydedeceğinizi](quickstart-register-app.md)öğrenin.

Uygulama kaydedildikten sonra, uygulama uç noktaya istek göndererek Microsoft Identity platformu ile iletişim kurar. Bu isteklerin ayrıntılarını işleyen açık kaynaklı çerçeveler ve kitaplıklar sunuyoruz. Ayrıca, bu uç noktalara istek oluşturarak kimlik doğrulama mantığını kendiniz uygulama seçeneğiniz de vardır:

```HTTP
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

## <a name="single-page-apps-javascript"></a>Tek sayfalı uygulamalar (JavaScript)

Birçok modern uygulamanın, özellikle JavaScript 'te, genellikle angular, yanıt verme veya Vue gibi bir çerçeve ile yazılmış tek sayfalı bir uygulama ön ucu vardır. Microsoft Identity platformu, kimlik doğrulaması için [OpenID Connect](v2-protocols-oidc.md) protokolünü ve [OAuth 2,0 örtük verme akışı](v2-oauth2-implicit-grant-flow.md) 'nı veya yetkilendirme için daha yeni [OAuth 2,0 yetkilendirme kodu + PCE akışını](v2-oauth2-auth-code-flow.md) kullanarak bu uygulamaları destekler (aşağıya bakın).

Aşağıdaki akış diyagramı, uygulamanın Microsoft Identity platform uç noktasından bir kod aldığı `authorize` ve belirteçleri ve siteler arası Web istekleri kullanarak belirteçleri yenilediğini, OAuth 2,0 yetkilendirme kodu yetkisini (PKI CE ile ilgili ayrıntılarla birlikte) gösterir. Yenileme belirteci her 24 saatte bir dolar ve uygulamanın başka bir kod istemesi gerekir. Erişim belirtecinin yanı sıra, `id_token` istemci uygulamasına oturum açmış kullanıcıyı temsil eden bir, genellikle aynı Flow ve/veya ayrı bir OpenID Connect isteği aracılığıyla da istenir (burada gösterilmez).

:::image type="content" source="media/v2-oauth-auth-code-spa/active-directory-oauth-code-spa.svg" alt-text="Tek sayfalı bir uygulama ve güvenlik belirteci hizmeti uç noktası arasındaki OAuth 2 yetkilendirme kodu akışını gösteren diyagram." border="false":::

Bu senaryoyu eylemde görmek için [öğreticiye göz atın: kullanıcılar oturum açın ve kimlik doğrulama kod akışı kullanarak bir JAVASCRIPT Spa 'dan MICROSOFT Graph API 'sini çağırın](tutorial-v2-javascript-auth-code.md).

### <a name="authorization-code-flow-vs-implicit-flow"></a>Yetkilendirme kodu akışı ve örtük akış karşılaştırması

OAuth 2,0 geçmişinin çoğu için [örtük akış](v2-oauth2-implicit-grant-flow.md) , tek sayfalı uygulamalar oluşturmak için önerilen yoldur. [Üçüncü taraf tanımlama bilgilerinin](reference-third-party-cookies-spas.md) kaldırılmasına ve [daha fazla dikkat etmeniz](https://tools.ietf.org/html/draft-ietf-oauth-security-topics-14) sayesinde, örtülü Flow 'daki güvenlik sorunlarına yönelik olarak, tek sayfalı uygulamalar için yetkilendirme kodu akışına taşındık.

Safari 'de uygulamanızın uyumluluğuna ve diğer gizliliğe duyarlı tarayıcılarda uyumluluğunu sağlamak için artık örtük akışın kullanımını önermiyoruz ve bunun yerine yetkilendirme kodu akışını öneririz.

## <a name="web-apps"></a>Web uygulamaları

Kullanıcının bir tarayıcıdan eriştiği Web uygulamaları (.NET, PHP, Java, Ruby, Python, node) için Kullanıcı oturum açma için [OpenID Connect](active-directory-v2-protocols.md) kullanabilirsiniz. OpenID Connect 'te, Web uygulaması bir KIMLIK belirteci alır. KIMLIK belirteci, kullanıcının kimliğini doğrulayan ve talep biçiminde kullanıcı hakkında bilgi sağlayan bir güvenlik belirtecidir:

```JSON
// Partial raw ID token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded ID token
{
    "name": "John Smith",
    "email": "john.smith@gmail.com",
    "oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
    ...
}
```

Microsoft Identity platformunda kullanılan farklı belirteç türlerinin diğer ayrıntıları, [erişim belirteci](access-tokens.md) başvurusunda ve [id_token başvurusuyla](id-tokens.md) kullanılabilir

Web sunucusu uygulamalarında, oturum açma kimlik doğrulama akışı şu üst düzey adımları alır:

![Web uygulaması kimlik doğrulama akışını gösterir](./media/v2-app-types/convergence-scenarios-webapp.svg)

KIMLIK belirtecini Microsoft Identity platformundan alınan bir ortak imzalama anahtarıyla doğrulayarak kullanıcının kimliğini sağlayabilirsiniz. Sonraki sayfa isteklerinde kullanıcıyı tanımlamak için kullanılabilen bir oturum tanımlama bilgisi ayarlanır.

Bu senaryoyu eylemde görmek için, [Kullanıcılar senaryosunda oturum açan Web](scenario-web-app-sign-user-overview.md)uygulamasındaki kod örneklerini deneyin.

Basit oturum açma 'ya ek olarak, bir Web sunucusu uygulamasının bir REST API gibi başka bir Web hizmetine erişmesi gerekebilir. Bu durumda, Web sunucusu uygulaması, [oauth 2,0 yetkilendirme kodu akışını](v2-oauth2-auth-code-flow.md)kullanarak birleştirilmiş bir OpenID Connect ve OAuth 2,0 akışında yer geçirir. Bu senaryo hakkında daha fazla bilgi için [Web uygulamaları ve Web API 'leri ile çalışmaya başlama](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIDConnect-DotNet)konusunu okuyun.

## <a name="web-apis"></a>Web API'leri

Uygulamanızın daha fazla Web API 'SI gibi Web hizmetlerini güvenli hale getirmek için Microsoft Identity platformunu kullanabilirsiniz. Web API 'Leri çeşitli platformlarda ve dillerde uygulanabilir. Bunlar, Azure Işlevleri 'nde HTTP Tetikleyicileri kullanılarak da uygulanabilir. Bir Web API 'si, KIMLIK belirteçleri ve oturum tanımlama bilgileri yerine, verilerini güvenli hale getirmek ve gelen isteklerin kimliğini doğrulamak için bir OAuth 2,0 erişim belirteci kullanır. Bir Web API 'SI çağıranı, bir HTTP isteğinin yetkilendirme üst bilgisine, şunun gibi bir erişim belirteci ekler:

```HTTP
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

Web API 'SI, API çağıranın kimliğini doğrulamak ve erişim belirtecinde kodlanmış taleplerden arayan hakkında bilgi ayıklamak için erişim belirtecini kullanır. Microsoft Identity platformunda kullanılan farklı belirteç türlerinin diğer ayrıntıları, [erişim belirteci](access-tokens.md) başvurusu ve [id_token](id-tokens.md) başvurusu ' nda bulunabilir.

Bir Web API 'SI, kullanıcıların belirli işlevleri veya verileri kabul etmek veya devre dışı bırakmak için [kapsam](v2-permissions-and-consent.md)olarak da bilinen izinleri verebilir. Çağıran bir uygulamanın bir kapsama izin almaları için, Kullanıcı bir akış sırasında kapsamı kabul etmelidir. Microsoft Identity platformu kullanıcıdan izin ister ve ardından Web API 'sinin aldığı tüm erişim belirteçlerinde izinleri kaydeder. Web API 'SI her çağrıda aldığı erişim belirteçlerini doğrular ve yetkilendirme denetimleri gerçekleştirir.

Web API 'SI, Web sunucusu uygulamaları, masaüstü ve mobil uygulamalar, tek sayfalı uygulamalar, sunucu tarafı Daemon 'ları ve hatta diğer Web API 'Leri dahil olmak üzere her türden uygulama için erişim belirteçleri alabilir. Bir Web API 'SI için üst düzey akış şöyle görünür:

![Web API kimlik doğrulama akışını gösterir](./media/v2-app-types/convergence-scenarios-webapi.svg)

OAuth2 erişim belirteçlerini kullanarak bir Web API 'sinin güvenliğini sağlama hakkında bilgi edinmek için, [korumalı Web API 'si senaryosundaki](scenario-protected-web-api-overview.md)Web API kodu örneklerine bakın.

Çoğu durumda, Web API 'Lerinin Microsoft Identity platform tarafından güvenliği sağlanmış diğer aşağı akış Web API 'Lerine giden istekler de yapması gerekir. Bunu yapmak için Web API 'Leri, Web API 'sinin giden isteklerde kullanılacak başka bir erişim belirtecinin gelen erişim belirtecini değiş tokuş etmesine olanak **tanıyan akışdan** faydalanabilir. Daha fazla bilgi için bkz. [Microsoft Identity platform ve OAuth 2,0-adına akışı](v2-oauth2-on-behalf-of-flow.md).

## <a name="mobile-and-native-apps"></a>Mobil ve yerel uygulamalar

Mobil ve Masaüstü uygulamaları gibi cihaza yüklenmiş uygulamalar, genellikle veri depolayan ve Kullanıcı adına işlevler gerçekleştiren arka uç hizmetlerine veya Web API 'Lerine erişmesi gerekir. Bu uygulamalar, [OAuth 2,0 yetkilendirme kodu akışını](v2-oauth2-auth-code-flow.md)kullanarak arka uç hizmetleri için oturum açma ve yetkilendirme ekleyebilirler.

Bu akışta, Kullanıcı oturum açtığında uygulama Microsoft Identity platformundan bir yetkilendirme kodu alır. Yetkilendirme kodu, uygulamanın oturum açmış olan kullanıcı adına arka uç hizmetlerini çağırma iznini temsil eder. Uygulama, bir OAuth 2,0 erişim belirteci ve yenileme belirteci için arka planda yetkilendirme kodunu değiş tokuş edebilir. Uygulama, HTTP isteklerindeki Web API 'Lerinde kimlik doğrulaması yapmak için erişim belirtecini kullanabilir ve eski erişim belirteçlerinin süre dolduğunda yeni erişim belirteçleri almak için yenileme belirtecini kullanabilir.

![Yerel uygulama kimlik doğrulama akışını gösterir](./media/v2-app-types/convergence-scenarios-native.svg)

> [!NOTE]
> Uygulama varsayılan System WebView 'u kullanıyorsa, [Azure AD kimlik doğrulaması ve yetkilendirme hata kodlarında](reference-aadsts-error-codes.md)"oturum açma sayfamı Onayla" işlevlerinden ve hata kodu AADSTS50199 hakkındaki bilgileri denetleyin.

## <a name="daemons-and-server-side-apps"></a>Daemon 'ları ve sunucu tarafı uygulamalar

Uzun süre çalışan işlemlere sahip olan veya Kullanıcı etkileşimi olmadan çalışan uygulamalar, Web API 'Leri gibi güvenli kaynaklara erişmek için de bir yönteme ihtiyaç duyar. Bu uygulamalar, OAuth 2,0 istemci kimlik bilgileri akışı ile kullanıcının temsilci kimliği yerine uygulamanın kimliğini kullanarak belirteçleri doğrulayabilir ve belirteçlere alabilir. Uygulamanın kimliğini bir istemci gizli dizisi veya sertifikası kullanarak kanıtlayabilirsiniz. Daha fazla bilgi için bkz. [Microsoft Identity platform kullanarak .NET Core Daemon konsol uygulaması](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2).

Bu akışta, uygulama `/token` erişim sağlamak için uç noktayla doğrudan etkileşime girer:

![Daemon uygulaması kimlik doğrulama akışını gösterir](./media/v2-app-types/convergence-scenarios-daemon.svg)

Bir Daemon uygulaması oluşturmak için [istemci kimlik bilgileri belgelerine](v2-oauth2-client-creds-grant-flow.md)bakın veya bir [.NET örnek uygulaması](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2)deneyin.

## <a name="next-steps"></a>Sonraki adımlar

Artık Microsoft Identity platformu tarafından desteklenen uygulama türleri hakkında bilgi edinmiş olduğunuza göre, farklı senaryolar tarafından kullanılan protokol bileşenlerini anlamak için [OAuth 2,0 ve OpenID Connect](active-directory-v2-protocols.md) hakkında daha fazla bilgi edinin.
