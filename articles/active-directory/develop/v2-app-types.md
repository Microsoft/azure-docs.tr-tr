---
title: Microsoft Identity platform için uygulama türleri | Mavisi
description: Microsoft Identity platform (v 2.0) uç noktası tarafından desteklenen uygulama ve senaryoların türleri.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 494a06b8-0f9b-44e1-a7a2-d728cf2077ae
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/06/2019
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 21f1da8738946078cb625c76e11ce1bfd62d97ac
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73927168"
---
# <a name="application-types-for-microsoft-identity-platform"></a>Microsoft Identity platform için uygulama türleri

Microsoft Identity platform (v 2.0) uç noktası, çeşitli modern uygulama mimarilerinin kimlik doğrulamasını destekler. Bu, endüstri standardı protokoller [OAuth 2,0 veya OpenID Connect](active-directory-v2-protocols.md)' i temel alır. Bu makalede, tercih ettiğiniz dil veya platformdan bağımsız olarak Microsoft Identity platform kullanarak oluşturabileceğiniz uygulama türleri açıklanmaktadır. Bilgiler, [kodla çalışmaya](v2-overview.md#getting-started)başlamadan önce üst düzey senaryoları anlamanıza yardımcı olmak için tasarlanmıştır.

> [!NOTE]
> Microsoft Identity platform uç noktası, tüm Azure Active Directory (Azure AD) senaryolarını ve özelliklerini desteklemez. Microsoft Identity platform uç noktasını kullanmanız gerekip gerekmediğini öğrenmek için [Microsoft Identity platform sınırlamaları](active-directory-v2-limitations.md)hakkında bilgi edinin.

## <a name="the-basics"></a>Temel bilgiler

Yeni [uygulama kayıtları portalında](https://go.microsoft.com/fwlink/?linkid=2083908)Microsoft Identity platform uç noktasını kullanan her uygulamayı kaydetmeniz gerekir. Uygulama kayıt işlemi, uygulamanız için bu değerleri toplar ve atar:

* Uygulamanızı benzersiz bir şekilde tanımlayan bir **uygulama (istemci) kimliği**
* Yanıtları uygulamanıza geri yönlendirmek için kullanabileceğiniz bir **yeniden yönlendirme URI 'si**
* Desteklenen hesap türleri gibi diğer senaryoya özgü birkaç değer

Ayrıntılar için, [bir uygulamayı nasıl kaydedeceğinizi](quickstart-register-app.md)öğrenin.

Uygulama kaydedildikten sonra, uygulama uç noktaya istek göndererek Microsoft Identity platformu ile iletişim kurar. Bu isteklerin ayrıntılarını işleyen açık kaynaklı çerçeveler ve kitaplıklar sunuyoruz. Ayrıca, bu uç noktalara istek oluşturarak kimlik doğrulama mantığını kendiniz uygulama seçeneğiniz de vardır:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

## <a name="single-page-apps-javascript"></a>Tek sayfalı uygulamalar (JavaScript)

Birçok modern uygulama, birincil olarak JavaScript 'te yazılmış tek sayfalı bir uygulama ön ucuna sahiptir. Genellikle, angular, tepki veya Vue gibi bir çerçeve kullanılarak yazılmıştır. Microsoft Identity platform uç noktası, [OAuth 2,0 örtük akışını](v2-oauth2-implicit-grant-flow.md)kullanarak bu uygulamaları destekler.

Bu akışta, uygulama, herhangi bir sunucudan sunucuya değiş tokuş olmadan belirteçleri doğrudan Microsoft Identity platform yetkilendirme uç noktasından alır. Tüm kimlik doğrulama mantığı ve oturum işleme, ek sayfa yeniden yönlendirmeleri olmadan tamamen JavaScript istemcisinde gerçekleşir.

![Örtük kimlik doğrulama akışını gösterir](./media/v2-app-types/convergence-scenarios-implicit.svg)

Bu senaryoyu eylemde görmek için [Microsoft Identity platform Başlarken](v2-overview.md#getting-started) bölümündeki tek sayfalı uygulama kodu örneklerinden birini deneyin.

## <a name="web-apps"></a>Web uygulamaları

Kullanıcının bir tarayıcıdan eriştiği Web uygulamaları (.NET, PHP, Java, Ruby, Python, node) için Kullanıcı oturum açma için [OpenID Connect](active-directory-v2-protocols.md) kullanabilirsiniz. OpenID Connect 'te, Web uygulaması bir KIMLIK belirteci alır. KIMLIK belirteci, kullanıcının kimliğini doğrulayan ve talep biçiminde kullanıcı hakkında bilgi sağlayan bir güvenlik belirtecidir:

```
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

Microsoft Identity platform uç noktasında kullanılan farklı belirteç türlerinin diğer ayrıntıları, [erişim belirteci](access-tokens.md) başvurusunda ve [id_token başvurusuyla](id-tokens.md) kullanılabilir

Web sunucusu uygulamalarında, oturum açma kimlik doğrulama akışı şu üst düzey adımları alır:

![Web uygulaması kimlik doğrulama akışını gösterir](./media/v2-app-types/convergence-scenarios-webapp.svg)

KIMLIK belirtecini Microsoft Identity platform uç noktasından alınan bir ortak imzalama anahtarıyla doğrulayarak kullanıcının kimliğini sağlayabilirsiniz. Sonraki sayfa isteklerinde kullanıcıyı tanımlamak için kullanılabilen bir oturum tanımlama bilgisi ayarlanır.

Bu senaryoyu eylemde görmek için [Microsoft Identity platform Başlarken](v2-overview.md#getting-started) bölümündeki Web uygulaması oturum açma kodu örneklerinden birini deneyin.

Basit oturum açma 'ya ek olarak, bir Web sunucusu uygulamasının bir REST API gibi başka bir Web hizmetine erişmesi gerekebilir. Bu durumda, Web sunucusu uygulaması, [oauth 2,0 yetkilendirme kodu akışını](active-directory-v2-protocols.md)kullanarak birleştirilmiş bir OpenID Connect ve OAuth 2,0 akışında yer geçirir. Bu senaryo hakkında daha fazla bilgi için [Web uygulamaları ve Web API 'leri ile çalışmaya başlama](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md)konusunu okuyun.

## <a name="web-apis"></a>Web API'leri

Uygulamanızın daha fazla Web API 'SI gibi Web hizmetlerini güvenli hale getirmek için Microsoft Identity platform uç noktasını kullanabilirsiniz. Web API 'Leri çeşitli platformlarda ve dillerde uygulanabilir. Bunlar, Azure Işlevleri 'nde HTTP Tetikleyicileri kullanılarak da uygulanabilir. Bir Web API 'si, KIMLIK belirteçleri ve oturum tanımlama bilgileri yerine, verilerini güvenli hale getirmek ve gelen isteklerin kimliğini doğrulamak için bir OAuth 2,0 erişim belirteci kullanır. Bir Web API 'SI çağıranı, bir HTTP isteğinin yetkilendirme üst bilgisine, şunun gibi bir erişim belirteci ekler:

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

Web API 'SI, API çağıranın kimliğini doğrulamak ve erişim belirtecinde kodlanmış taleplerden arayan hakkında bilgi ayıklamak için erişim belirtecini kullanır. Microsoft Identity platform uç noktasında kullanılan farklı belirteç türlerinin diğer ayrıntıları, [erişim belirteci](access-tokens.md) başvurusunda ve [id_token başvurusuyla](id-tokens.md) kullanılabilir

Bir Web API 'SI, kullanıcıların belirli işlevleri veya verileri kabul etmek veya devre dışı bırakmak için [kapsam](v2-permissions-and-consent.md)olarak da bilinen izinleri verebilir. Çağıran bir uygulamanın bir kapsama izin almaları için, Kullanıcı bir akış sırasında kapsamı kabul etmelidir. Microsoft Identity platform uç noktası kullanıcıdan izin ister ve sonra Web API 'sinin aldığı tüm erişim belirteçlerinde izinleri kaydeder. Web API 'SI her çağrıda aldığı erişim belirteçlerini doğrular ve yetkilendirme denetimleri gerçekleştirir.

Web API 'SI, Web sunucusu uygulamaları, masaüstü ve mobil uygulamalar, tek sayfalı uygulamalar, sunucu tarafı Daemon 'ları ve hatta diğer Web API 'Leri dahil olmak üzere her türden uygulama için erişim belirteçleri alabilir. Bir Web API 'SI için üst düzey akış şöyle görünür:

![Web API kimlik doğrulama akışını gösterir](./media/v2-app-types/convergence-scenarios-webapi.svg)

OAuth2 erişim belirteçlerini kullanarak bir Web API 'sinin güvenliğini sağlama hakkında bilgi edinmek için [Microsoft Identity platform Başlarken](v2-overview.md#getting-started) bölümündeki Web API 'si kod örneklerine bakın.

Çoğu durumda, Web API 'Lerinin Microsoft Identity platform tarafından güvenliği sağlanmış diğer aşağı akış Web API 'Lerine giden istekler de yapması gerekir. Bunu yapmak için Web API 'Leri, Web API 'sinin giden isteklerde kullanılacak başka bir erişim belirtecinin gelen erişim belirtecini değiş tokuş etmesine olanak **tanıyan akışdan** faydalanabilir. Daha fazla bilgi için bkz. [Microsoft Identity platform ve OAuth 2,0-adına akışı](v2-oauth2-on-behalf-of-flow.md).

## <a name="mobile-and-native-apps"></a>Mobil ve yerel uygulamalar

Mobil ve Masaüstü uygulamaları gibi cihaza yüklenmiş uygulamalar, genellikle veri depolayan ve Kullanıcı adına işlevler gerçekleştiren arka uç hizmetlerine veya Web API 'Lerine erişmesi gerekir. Bu uygulamalar, [OAuth 2,0 yetkilendirme kodu akışını](v2-oauth2-auth-code-flow.md)kullanarak arka uç hizmetleri için oturum açma ve yetkilendirme ekleyebilirler.

Bu akışta, uygulama, Kullanıcı oturum açtığında Microsoft Identity platform uç noktasından bir yetkilendirme kodu alır. Yetkilendirme kodu, uygulamanın oturum açmış olan kullanıcı adına arka uç hizmetlerini çağırma iznini temsil eder. Uygulama, bir OAuth 2,0 erişim belirteci ve yenileme belirteci için arka planda yetkilendirme kodunu değiş tokuş edebilir. Uygulama, HTTP isteklerindeki Web API 'Lerinde kimlik doğrulaması yapmak için erişim belirtecini kullanabilir ve eski erişim belirteçlerinin süre dolduğunda yeni erişim belirteçleri almak için yenileme belirtecini kullanabilir.

![Yerel uygulama kimlik doğrulama akışını gösterir](./media/v2-app-types/convergence-scenarios-native.svg)

## <a name="daemons-and-server-side-apps"></a>Daemon 'ları ve sunucu tarafı uygulamalar

Uzun süre çalışan işlemlere sahip olan veya Kullanıcı etkileşimi olmadan çalışan uygulamalar, Web API 'Leri gibi güvenli kaynaklara erişmek için de bir yönteme ihtiyaç duyar. Bu uygulamalar, OAuth 2,0 istemci kimlik bilgileri akışı ile kullanıcının temsilci kimliği yerine uygulamanın kimliğini kullanarak belirteçleri doğrulayabilir ve belirteçlere alabilir. Uygulamanın kimliğini bir istemci gizli dizisi veya sertifikası kullanarak kanıtlayabilirsiniz. Daha fazla bilgi için bkz. [sertifikalarla birlikte Daemon uygulamalarında Microsoft Identity platformunda kimlik doğrulama](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential/).

Bu akışta, uygulama, erişim elde etmek için `/token` uç noktasıyla doğrudan etkileşime girer:

![Daemon uygulaması kimlik doğrulama akışını gösterir](./media/v2-app-types/convergence-scenarios-daemon.svg)

Bir Daemon uygulaması oluşturmak için [istemci kimlik bilgileri belgelerine](v2-oauth2-client-creds-grant-flow.md)bakın veya bir [.NET örnek uygulaması](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2)deneyin.
