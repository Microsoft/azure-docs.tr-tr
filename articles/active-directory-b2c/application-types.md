---
title: Azure AD B2C tarafından desteklenen uygulama türleri
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C ile kullanabileceğiniz uygulama türleri hakkında bilgi edinin.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/24/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 07897823a3ba3b83e240e8e8dc005ea13b036fce
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94952055"
---
# <a name="application-types-that-can-be-used-in-active-directory-b2c"></a>Active Directory B2C kullanılabilecek uygulama türleri
 
Azure Active Directory B2C (Azure AD B2C), çeşitli modern uygulama mimarilerinin kimlik doğrulamasını destekler. Bunların tümü [OAuth 2.0](protocols-overview.md) veya [OpenID Connect](protocols-overview.md) endüstri standardı protokollerine dayalıdır. Bu makalede, tercih ettiğiniz dil veya platformdan bağımsız olarak oluşturabileceğiniz uygulama türleri açıklanmaktadır. Uygulama oluşturmaya başlamadan önce üst düzey senaryoları anlamanıza da yardımcı olur.

Azure AD B2C kullanan her uygulamanın [Azure Portal](https://portal.azure.com/)kullanılarak [Azure AD B2C kiracınızda](tutorial-create-tenant.md) kayıtlı olması gerekir. Uygulama kayıt işlemi, değerleri toplar ve atar, örneğin:

* Uygulamanızı benzersiz bir şekilde tanımlayan bir **uygulama kimliği** .
* Yanıtları uygulamanıza geri yönlendirmek için kullanılabilen bir **yanıt URL 'si** .

Azure AD B2C gönderilen her istek, bir **Kullanıcı akışı** (yerleşik bir ilke) veya Azure AD B2C davranışını denetleyen **özel bir ilke** belirtir. Her iki ilke türü de yüksek düzeyde özelleştirilebilir bir kullanıcı deneyimi kümesi oluşturmanızı sağlar.

Her uygulamanın etkileşimini benzer bir üst düzey deseni izler:

1. Uygulama, bir [ilkeyi](user-flow-overview.md)yürütmek için kullanıcıyı v 2.0 uç noktasına yönlendirir.
2. Kullanıcı, ilkeyi ilke tanımına göre tamamlar.
3. Uygulama, v 2.0 uç noktasından bir güvenlik belirteci alır.
4. Uygulama, korunan bilgilere veya korunan kaynağa erişmek için güvenlik belirtecini kullanır.
5. Kaynak sunucu, erişim izni verilebileceğini doğrulamak için güvenlik belirtecini doğrular.
6. Uygulama, güvenlik belirtecini düzenli aralıklarla yeniler.

Bu adımlar, oluşturmakta olduğunuz uygulamanın türüne göre biraz farklılık gösterebilir.

## <a name="web-applications"></a>Web uygulamaları

Sunucuda barındırılan ve bir tarayıcıdan erişilen Web uygulamaları (.NET, PHP, Java, Ruby, Python ve Node.js dahil) için, tüm kullanıcı deneyimleri için [OpenID Connect](protocols-overview.md) 'i destekler Azure AD B2C. OpenID Connect 'in Azure AD B2C uygulamasında, Web uygulamanız Azure AD 'ye kimlik doğrulama istekleri vererek kullanıcı deneyimlerini başlatır. İstek sonucu `id_token` şeklindedir. Bu güvenlik belirteci, kullanıcının kimliğini temsil eder. Ayrıca kullanıcı hakkındaki bilgileri talep biçiminde sağlar:

```json
// Partial raw id_token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded id_token
{
    "name": "John Smith",
    "email": "john.smith@gmail.com",
    "oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
    ...
}
```

[Azure AD B2C belirteç başvurusunda](tokens-overview.md)bir uygulama için kullanılabilir belirteç türleri ve talepler hakkında daha fazla bilgi edinin.

Bir Web uygulamasında, bir [ilkenin](user-flow-overview.md) her yürütülmesi şu üst düzey adımları alır:

1. Kullanıcı Web uygulamasına göz atar.
2. Web uygulaması, çalıştırılacak ilkeyi belirten Azure AD B2C kullanıcıyı yeniden yönlendirir.
3. Kullanıcı ilkeyi tamamlar.
4. Azure AD B2C tarayıcıya döndürür `id_token` .
5. , `id_token` Yeniden YÖNLENDIRME URI 'sine gönderilir.
6. , `id_token` Onaylanır ve bir oturum tanımlama bilgisi ayarlanır.
7. Kullanıcıya güvenli bir sayfa döndürülür.

Azure AD'den alınan bir ortak imzalama anahtarı kullanarak `id_token` doğrulaması yapma, kullanıcının kimliğini doğrulamak için yeterlidir. Bu işlem, sonraki sayfa isteklerinde kullanıcıyı tanımlamak için kullanılabilecek bir oturum tanımlama bilgisi de ayarlar.

Bu senaryoyu eylemde görmek için [Başlarken bölümümüzde](overview.md)Web uygulaması oturum açma kodu örneklerinden birini deneyin.

Basit oturum açmayı kolaylaştırmanın yanı sıra, bir Web sunucusu uygulamasının bir arka uç Web hizmetine erişmesi de gerekebilir. Bu durumda, Web uygulaması biraz farklı bir [OpenID Connect akışı](openid-connect.md) gerçekleştirebilir ve yetkilendirme kodları ve yenileme belirteçleri kullanarak belirteçleri alabilir. Bu senaryo, aşağıdaki [Web API'leri bölümünde](#web-apis) belirtilmiştir.

## <a name="single-page-applications"></a>Tek sayfalı uygulamalar
Birçok modern web uygulaması, istemci tarafı tek sayfalı uygulamalar ("maça 'Lar") olarak oluşturulmuştur. Geliştiriciler bunları JavaScript veya angular, Vue ve tepki verme gibi bir SPA çerçevesi kullanarak yazar. Bu uygulamalar bir Web tarayıcısında çalışır ve geleneksel sunucu tarafı Web uygulamalarından farklı kimlik doğrulama özelliklerine sahiptir.

Azure AD B2C, tek sayfalı uygulamaların kullanıcılara oturum açmasını ve arka uç hizmetlerine veya Web API 'Lerine erişim belirteçleri almasını sağlamak için **iki** seçenek sunar:

### <a name="authorization-code-flow-with-pkce"></a>Yetkilendirme kodu akışı (PKCE ile)
- [OAuth 2,0 yetkilendirme kodu akışı (PKCE ile)](./authorization-code-flow.md). Yetkilendirme kodu akışı, uygulamanın **kimlik** belirteçleri için bir yetkilendirme kodu gönderip, korunan API 'leri aramak için gereken kimliği doğrulanmış kullanıcı ve **erişim** belirteçlerini temsil etmesine olanak tanır. Buna ek olarak, kullanıcılar adına bu kullanıcılarla etkileşime gerek kalmadan kaynaklara uzun süreli erişim sağlayan **yenileme** belirteçlerini döndürür. 

Bu, **Önerilen** yaklaşımdır. Sınırlı ömür yenileme belirteçlerinin olması, uygulamanızın Safari ıOP gibi [modern tarayıcı tanımlama bilgisi gizlilik kısıtlamalarına](../active-directory/develop/reference-third-party-cookies-spas.md)uyum sağlamasına yardımcı olur.

Bu akıştan faydalanmak için, uygulamanız [MSAL.js 2. x](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)gibi onu destekleyen bir kimlik doğrulama kitaplığı kullanabilir.

<!-- ![Single-page applications-auth](./media/tutorial-single-page-app/spa-app-auth.svg) -->
![Tek sayfalı uygulamalar-kimlik doğrulama](./media/tutorial-single-page-app/active-directory-oauth-code-spa.png)

### <a name="implicit-grant-flow"></a>Örtük verme akışı
- [OAuth 2,0 örtük akış](implicit-flow-single-page-application.md). [MSAL.js 1. x](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-core)gibi bazı çerçeveler yalnızca örtük izin akışını destekler. Örtük verme akışı, uygulamanın **kimlik** ve **erişim** belirteçleri almasına izin verir. Yetkilendirme kodu akışından farklı olarak, örtük verme akışı bir **yenileme belirteci** döndürmez. 

Bu kimlik doğrulama akışı, elektron ve tepki verme gibi platformlar arası JavaScript çerçeveleri kullanan uygulama senaryoları içermez. Bu senaryolar, yerel platformlarla etkileşim için daha fazla yetenek gerektirir.

## <a name="web-apis"></a>Web API'leri

Uygulamanızın tekrar Web API 'SI gibi Web hizmetlerini güvenli hale getirmek için Azure AD B2C kullanabilirsiniz. Web API’leri belirteçleri kullanarak gelen HTTP isteklerinin kimliğini doğrulama yoluyla verilerinin güvenliğini sağlamak üzere OAuth 2.0 kullanabilir. Web API'si çağıranı, HTTP isteğinin yetkilendirme üst bilgisine bir belirteç ekler:

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
``` 

Web API'si daha sonra, API çağıranının kimliğini doğrulamak ve belirteçte kodlanmış taleplerden çağıran hakkında bilgi ayıklamak için belirteci kullanabilir. [Azure AD B2C belirteç başvurusunda](tokens-overview.md) bir uygulama için kullanılabilir talepler ve belirteç türleri hakkında bilgi edinin.

Web API 'SI, Web uygulamaları, masaüstü ve mobil uygulamalar, tek sayfa uygulamaları, sunucu tarafı Daemon 'ları ve diğer Web API 'Leri dahil olmak üzere birçok tür istemciden belirteç alabilir. Web API 'sini çağıran bir Web uygulaması için akışın tamamına yönelik bir örnek aşağıda verilmiştir:

1. Web uygulaması bir ilkeyi yürütür ve Kullanıcı Kullanıcı deneyimini tamamlar.
2. Azure AD B2C tarayıcıya bir (OpenID Connect) `id_token` ve yetkilendirme kodu döndürür.
3. Tarayıcı, `id_token` ve yetkilendirme kodunu yeniden YÖNLENDIRME URI 'sine gönderir.
4. Web sunucusu, `id_token` bir oturum tanımlama bilgisini doğrular ve belirler.
5. Web sunucusu, `access_token` kimlik doğrulama kodunu, uygulama ISTEMCI kimliğini ve istemci kimlik bilgilerini sağlayarak Azure AD B2C ister.
6. `access_token`Ve, `refresh_token` Web sunucusuna döndürülür.
7. Web API 'SI, `access_token` bir yetkilendirme üst bilgisinde ile çağrılır.
8. Web API 'SI belirteci doğrular.
9. Güvenli veriler Web uygulamasına döndürülür.

Yetkilendirme kodları, yenileme belirteçleri ve belirteç alma adımları hakkında daha fazla bilgi edinmek için [OAuth 2.0 protokolünü](authorization-code-flow.md) okuyun.

Azure AD B2C'yi kullanarak bir web API'sini nasıl güvence altına alacağınızı öğrenmek için [Başlarken bölümümüzdeki](overview.md) web API'si eğitimlerine bakın.

## <a name="mobile-and-native-applications"></a>Mobil ve yerel uygulamalar

Mobil ve Masaüstü uygulamaları gibi cihazlarda yüklü olan uygulamaların, genellikle kullanıcı adına arka uç hizmetlerine veya Web API 'Lerine erişmesi gerekir. Yerel uygulamalarınıza özelleştirilmiş kimlik yönetimi deneyimleri ekleyebilir ve Azure AD B2C ve [OAuth 2,0 yetkilendirme kodu akışını](authorization-code-flow.md)kullanarak arka uç hizmetlerini güvenle çağırabilirsiniz.

Bu akışta, uygulama [ilkeleri](user-flow-overview.md) yürütür ve `authorization_code` Kullanıcı ilkeyi tamamladıktan sonra bir Azure AD 'den alır. , `authorization_code` Uygulamanın, oturum açmış olan kullanıcı adına arka uç hizmetlerini çağırma iznini temsil eder. Uygulama daha sonra `authorization_code` bir ve için arka planda öğesini değiştirebilir `access_token` `refresh_token` .  Uygulama, `access_token` http isteklerinde bir arka uç Web API 'sine kimlik doğrulaması yapmak için kullanabilir. Ayrıca eskisinin süresi dolduğunda yeni bir `access_token` almak için `refresh_token` öğesini kullanabilir.

## <a name="current-limitations"></a>Geçerli sınırlamalar

### <a name="unsupported-application-types"></a>Desteklenmeyen uygulama türleri

#### <a name="daemonsserver-side-applications"></a>Daemon 'ları/sunucu tarafı uygulamalar

Uzun süre çalışan süreçler içeren veya bir kullanıcının varlığı olmadan çalışan uygulamalar, Web API 'Leri gibi güvenli kaynaklara erişmek için bir yol da gerekir. Bu uygulamalar, uygulamanın kimliğini (kullanıcının Temsilcili kimliği yerine) kullanarak ve OAuth 2,0 istemci kimlik bilgileri akışını kullanarak belirteçlerin kimliğini doğrulayabilir ve alabilir. İstemci kimlik bilgileri akışı, sunucu-sunucu kimlik doğrulaması için kullanılmamalıdır ve şirket içi akışla aynı değildir.

OAuth 2,0 istemci kimlik bilgileri verme akışı şu anda Azure AD B2C kimlik doğrulama hizmeti tarafından doğrudan desteklenmese de, Azure AD 'yi kullanarak istemci kimlik bilgileri akışını ve Azure AD B2C kiracınızdaki bir uygulama için Microsoft Identity platform/Token uç noktasını ayarlayabilirsiniz. Azure AD B2C kiracı, Azure AD kurumsal kiracılar ile bazı işlevleri paylaşır.

İstemci kimlik bilgileri akışını ayarlamak için, bkz. [Azure Active Directory v 2.0 ve OAuth 2,0 istemci kimlik bilgileri akışı](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md). Başarılı bir kimlik doğrulaması, Azure AD [belirteç başvurusunda](../active-directory/develop/id-tokens.md)açıklandığı gıbı Azure AD tarafından kullanılabilmesi için biçimlendirilen belirtecin alınmasına neden olur.

Bir yönetim uygulamasını kaydetme hakkında yönergeler için bkz. [Microsoft Graph Azure AD B2C yönetme](microsoft-graph-get-started.md).

#### <a name="web-api-chains-on-behalf-of-flow"></a>Web API'si zincirleri (temsili akış)

Çoğu mimari başka bir aşağı akış web API'si çağırmayı gerektiren bir web API'si içerir; her iki API de Azure AD B2C tarafından güvence altına alınır. Bu senaryo, bir Web API 'SI arka ucuna sahip olan yerel istemcilerde ortaktır ve Microsoft Graph API gibi bir Microsoft Online hizmetini çağırır.

Bu zincirli web API'si senaryosu, temsili akış olarak da bilinen OAuth 2.0 JWT taşıyıcı kimlik bilgisi yetkisi kullanılarak desteklenebilir.  Ancak temsili akış şu anda Azure AD B2C’de uygulanmamıştır.

### <a name="faulted-apps"></a>Hatalı uygulamalar

Azure AD B2C uygulamalarını şu yollarla düzenlemeyin:

- [Uygulama Kayıt Portalı](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) gibi diğer uygulama yanıt portallarında.
- Graph API veya PowerShell kullanma.

Azure AD B2C uygulamayı Azure portal dışında düzenlerseniz, bu, hatalı bir uygulama haline gelir ve Azure AD B2C artık kullanılamaz. Uygulamayı silin ve yeniden oluşturun.

Uygulamayı silmek için [uygulama kayıt portalı](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) ' na gidin ve uygulamayı orada silin. Uygulamanın görünür olması için uygulamanın sahibi olmanız (ve yalnızca kiracının yöneticisi olmamanız) gerekir.

## <a name="next-steps"></a>Sonraki adımlar

[Azure Active Directory B2C Içindeki Kullanıcı akışları](user-flow-overview.md)tarafından sunulan yerleşik ilkeler hakkında daha fazla bilgi edinin.