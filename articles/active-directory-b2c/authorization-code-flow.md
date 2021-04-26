---
title: Yetkilendirme kodu akışı-Azure Active Directory B2C | Microsoft Docs
description: Azure AD B2C ve OpenID Connect kimlik doğrulama protokolünü kullanarak Web uygulamaları oluşturmayı öğrenin.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: a6a993fdf4fd266afb9459fedd13412d8796e0a5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102611513"
---
# <a name="oauth-20-authorization-code-flow-in-azure-active-directory-b2c"></a>Azure Active Directory B2C 'de OAuth 2,0 yetkilendirme kodu akışı

Web API 'Leri gibi korumalı kaynaklara erişim kazanmak için bir cihazda yüklü olan uygulamalarda OAuth 2,0 yetkilendirme kodu yetkisini kullanabilirsiniz. OAuth 2,0 Azure Active Directory B2C (Azure AD B2C) uygulamasını kullanarak, tek sayfalı, mobil ve Masaüstü uygulamalarınıza kaydolma, oturum açma ve diğer kimlik yönetimi görevlerini ekleyebilirsiniz. Bu makale, dilden bağımsızdır. Makalesinde, herhangi bir açık kaynak kitaplığı kullanmadan HTTP iletilerinin nasıl gönderileceğini ve alınacağını anladık. Mümkün olduğunda desteklenen Microsoft kimlik doğrulama kitaplıklarını (MSAL) kullanmanızı öneririz. [Msal kullanan örnek uygulamalara](code-samples.md)göz atın.

OAuth 2,0 yetkilendirme kodu akışı, [oauth 2,0 belirtiminin 4,1 bölümünde](https://tools.ietf.org/html/rfc6749)açıklanmaktadır. Web uygulamaları, tek sayfalı uygulamalar ve yerel olarak yüklenen uygulamalar dahil olmak üzere çoğu [uygulama türünde](application-types.md)kimlik doğrulama ve yetkilendirme için kullanabilirsiniz. OAuth 2,0 yetkilendirme kodu akışını, bir [yetkilendirme sunucusu](protocols-overview.md)tarafından güvenliği sağlanmış olan kaynaklara erişmek için kullanılabilecek, uygulamalarınıza yönelik erişim belirteçlerini ve yenileme belirteçlerini güvenli bir şekilde almak için kullanabilirsiniz.  Yenileme belirteci, erişim belirtecinin süresi dolduktan sonra, genellikle bir saatten sonra istemcinin yeni erişim (ve yenileme) belirteçleri almasına izin verir.

Bu makale, **genel istemciler** OAuth 2,0 yetkilendirme kodu akışına odaklanır. Ortak istemci, gizli bir parolanın bütünlüğünü güvenli bir şekilde korumak için güvenilir olmayan tüm istemci uygulamasıdır. Bu, tek sayfalı uygulamaları, mobil uygulamaları, masaüstü uygulamaları ve aslında bir sunucuda çalıştırmayan tüm uygulamaları içerir.

> [!NOTE]
> Azure AD B2C kullanarak bir Web uygulamasına kimlik yönetimi eklemek için, OAuth 2,0 yerine [OpenID Connect](openid-connect.md) kullanın.

Azure AD B2C, basit kimlik doğrulaması ve yetkilendirmeden daha fazlasını yapmak için standart OAuth 2,0 akışlarını genişletir. [Kullanıcı akışını](user-flow-overview.md)tanıtır. Kullanıcı akışları ile, uygulamanıza kaydolma, oturum açma ve profil yönetimi gibi kullanıcı deneyimleri eklemek için OAuth 2,0 kullanabilirsiniz. OAuth 2,0 protokolünü kullanan kimlik sağlayıcıları [Amazon](identity-provider-amazon.md), [Azure Active Directory](identity-provider-azure-ad-single-tenant.md), [Facebook](identity-provider-facebook.md), [GitHub](identity-provider-github.md), [Google](identity-provider-google.md)ve [LinkedIn](identity-provider-linkedin.md)' i içerir.

Bu makaledeki HTTP isteklerini denemek için:

1. `{tenant}`Azure AD B2C kiracınızın adıyla değiştirin.
1. `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6`Daha önce Azure AD B2C kiracınıza kaydettiğiniz uygulamanın uygulama kimliğiyle değiştirin.
1. `{policy}`Kiracınızda oluşturduğunuz bir ilkenin adıyla değiştirin (örneğin,) `b2c_1_sign_in` .

## <a name="redirect-uri-setup-required-for-single-page-apps"></a>Tek sayfalı uygulamalar için yeniden yönlendirme URI kurulumu gerekli

Tek sayfalı uygulamalar için yetkilendirme kodu akışı, bazı ek kurulum gerektirir.  Yeniden yönlendirme URI 'nizi CORS için etkin olarak işaretlemek üzere [tek sayfalı uygulamanızı oluşturmaya](tutorial-register-spa.md) yönelik yönergeleri izleyin. CORS 'yi etkinleştirmek üzere var olan bir yeniden yönlendirme URI 'sini güncelleştirmek için, **uygulama kaydının** **kimlik doğrulama** sekmesinin "Web" bölümünde geçiş istemine tıklayabilirsiniz. Alternatif olarak, **uygulama kayıtları bildirim düzenleyicisini** açabilir ve `type` yeniden yönlendirme URI 'si alanını bölümünde olarak ayarlayabilirsiniz `spa` `replyUrlsWithType` .

`spa`Yeniden yönlendirme türü, örtük akışla geriye dönük olarak uyumludur. Belirteçleri almak için örtük akışı kullanan uygulamalar, `spa` yeniden YÖNLENDIRME URI 'sine sorun olmadan geçebilir ve dolaylı akışı kullanmaya devam edebilir.

## <a name="1-get-an-authorization-code"></a>1. bir yetkilendirme kodu alın
Yetkilendirme kodu akışı, kullanıcıyı uç noktaya yönlendiren istemciyle başlar `/authorize` . Bu, akışın kullanıcının işlem yapacağı etkileşimli kısmıdır. Bu istekte istemci, `scope` kullanıcıdan almaları gereken izinleri parametrede gösterir. Aşağıdaki üç örnek (okunabilirlik için satır sonları ile) her biri farklı bir Kullanıcı akışı kullanır.


```http
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&code_challenge=YTFjNjI1OWYzMzA3MTI4ZDY2Njg5M2RkNmVjNDE5YmEyZGRhOGYyM2IzNjdmZWFhMTQ1ODg3NDcxY2Nl
&code_challenge_method=S256
```

| Parametre | Gerekli mi? | Description |
| --- | --- | --- |
|Kiracı| Gerekli | Azure AD B2C kiracınızın adı|
| ilkesinin | Gerekli | Çalıştırılacak Kullanıcı akışı. Azure AD B2C kiracınızda oluşturduğunuz Kullanıcı akışının adını belirtin. Örneğin: `b2c_1_sign_in` , `b2c_1_sign_up` , veya `b2c_1_edit_profile` . |
| client_id |Gerekli |[Azure Portal](https://portal.azure.com)uygulamanıza atanan uygulama kimliği. |
| response_type |Gerekli |`code`Yetkilendirme kodu akışı için içermesi gereken yanıt türü. |
| redirect_uri |Gerekli |Uygulamanızın kimlik doğrulama yanıtlarının gönderildiği ve alındığı, uygulamanızın yeniden yönlendirme URI 'SI. Portalın, URL kodlamalı olması dışında, portalda kaydettiğiniz yeniden yönlendirme URI 'lerinden biriyle tam olarak eşleşmesi gerekir. |
| scope |Gerekli |Kapsamların boşlukla ayrılmış listesi. Tek bir kapsam değeri, İstenen izinlerin her ikisi de Azure Active Directory (Azure AD) gösterir. İstemci KIMLIĞINI kapsam olarak kullanmak, uygulamanızın aynı istemci KIMLIĞIYLE temsil edilen kendi hizmetinize veya Web API 'niz için kullanılabilecek bir erişim belirteci gerektiğini gösterir.  `offline_access`Kapsam, uygulamanızın kaynaklara uzun süreli erişim için yenileme belirteci gerektiğini gösterir. Ayrıca, `openid` Azure AD B2C BIR kimlik belirteci istemek için kapsamını da kullanabilirsiniz. |
| response_mode |Önerilen |Elde edilen yetkilendirme kodunu uygulamanıza geri göndermek için kullandığınız yöntem. `query`, `form_post` Veya olabilir `fragment` . |
| state |Önerilen |İstekte bulunan, kullanmak istediğiniz herhangi bir içerik dizesi olabilecek bir değer. Genellikle, siteler arası istek sahteciliği saldırıları engellemek için rastgele oluşturulmuş benzersiz bir değer kullanılır. Durum Ayrıca, kimlik doğrulama isteği gerçekleştirilmeden önce kullanıcının uygulamasındaki durumu hakkında bilgi kodlamak için de kullanılır. Örneğin, kullanıcının açık olduğu sayfa veya yürütülmekte olan Kullanıcı akışı. |
| isteme |İsteğe Bağlı |Gerekli Kullanıcı etkileşimi türü. Şu anda geçerli olan tek değer, `login` kullanıcıyı bu istek üzerine kimlik bilgilerini girmeye zorlayan olur. Çoklu oturum açma etkili olmayacaktır. |
| code_challenge  | Önerilen/gerekli | Kod değişimi (PKCE) için kanıt anahtarı aracılığıyla yetkilendirme kodu yetkisini güvenli hale getirmek için kullanılır. Dahil ise gereklidir `code_challenge_method` . Daha fazla bilgi için bkz. [Pkce RFC](https://tools.ietf.org/html/rfc7636). Bu artık, Web Apps gibi tüm uygulama türleri için yerel uygulamalar, maça 'Lar ve gizli istemciler için önerilir. | 
| `code_challenge_method` | Önerilen/gerekli | Parametresi için öğesini kodlamak için kullanılan yöntem `code_verifier` `code_challenge` . Bu  olmalıdır `S256` , ancak `plain` BIR nedenden dolayı istemcinin SHA256 destekleyememelidir. <br/><br/>Dışlanmazsa, varsa `code_challenge` düz metin olarak kabul edilir `code_challenge` . Microsoft Identity platform hem hem de destekler `plain` `S256` . Daha fazla bilgi için bkz. [Pkce RFC](https://tools.ietf.org/html/rfc7636). Bu [, yetkilendirme kodu akışını kullanan tek sayfalı uygulamalar](tutorial-register-spa.md)için gereklidir.|
| login_hint | No| , Oturum açma sayfasının oturum açma adı alanını önceden dolduraiçin kullanılabilir. Daha fazla bilgi için bkz. [oturum açma adını önceden girme](direct-signin.md#prepopulate-the-sign-in-name).  |
| domain_hint | No| Oturum açma için kullanılması gereken sosyal kimlik sağlayıcısı hakkında Azure AD B2C için bir ipucu sağlar. Geçerli bir değer dahil edilse, Kullanıcı doğrudan kimlik sağlayıcısı oturum açma sayfasına gider.  Daha fazla bilgi için bkz. [oturum açma oturumunu bir sosyal sağlayıcıya yeniden yönlendirme](direct-signin.md#redirect-sign-in-to-a-social-provider). |
| Özel parametreler | No| [Özel ilkelerle](custom-policy-overview.md)kullanılabilecek özel parametreler. Örneğin, [dinamik özel sayfa içerik URI 'si](customize-ui-with-html.md?pivots=b2c-custom-policy#configure-dynamic-custom-page-content-uri)veya [anahtar-değer talep çözücüler](claim-resolver-overview.md#oauth2-key-value-parameters). |

Bu noktada kullanıcıdan Kullanıcı akışının iş akışını tamamlaması istenir. Bu, kullanıcının Kullanıcı adını ve parolasını girmesini, sosyal kimlikle oturum açmasını, dizin için kaydolmasını veya başka birçok adımı de içerebilir. Kullanıcı eylemleri, Kullanıcı akışının nasıl tanımlandığına bağlıdır.

Kullanıcı Kullanıcı akışını tamamladıktan sonra, Azure AD, için kullandığınız değerde uygulamanıza bir yanıt döndürür `redirect_uri` . Parametresinde belirtilen yöntemini kullanır `response_mode` . Yanıt, yürütülen Kullanıcı akışından bağımsız olarak her kullanıcı eylemi senaryosu için tam olarak aynıdır.

Tarafından kullanılan başarılı bir yanıt `response_mode=query` Şuna benzer:

```http
GET urn:ietf:wg:oauth:2.0:oob?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...        // the authorization_code, truncated
&state=arbitrary_data_you_can_receive_in_the_response                // the value provided in the request
```

| Parametre | Açıklama |
| --- | --- |
| kod |Uygulamanın istediği yetkilendirme kodu. Uygulama, bir hedef kaynak için erişim belirteci istemek üzere yetkilendirme kodunu kullanabilir. Yetkilendirme kodları çok kısa süreli. Genellikle, yaklaşık 10 dakika sonra süresi dolacak. |
| state |Yukarıdaki bölümde bulunan tablodaki tam açıklamaya bakın. İsteğe bir `state` parametre dahil ise, yanıtta aynı değer görünmelidir. Uygulamanın, `state` istek ve yanıt değerlerinin özdeş olduğunu doğrulaması gerekir. |

Ayrıca, uygulamanın uygun şekilde işleyebilmesi için yeniden yönlendirme URI 'sine de hata yanıtları gönderilebilir:

```http
GET urn:ietf:wg:oauth:2.0:oob?
error=access_denied
&error_description=The+user+has+cancelled+entering+self-asserted+information
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parametre | Açıklama |
| --- | --- |
| error |Oluşan hata türlerini sınıflandırmak için kullanabileceğiniz bir hata kodu dizesi. Ayrıca, hatalara yanıt vermek için dizesini de kullanabilirsiniz. |
| error_description |Kimlik doğrulama hatasının temel nedenini belirlemenize yardımcı olabilecek belirli bir hata iletisi. |
| state |Yukarıdaki tabloda bulunan tam açıklamaya bakın. İsteğe bir `state` parametre dahil ise, yanıtta aynı değer görünmelidir. Uygulamanın, `state` istek ve yanıt değerlerinin özdeş olduğunu doğrulaması gerekir. |

## <a name="2-get-an-access-token"></a>2. erişim belirteci al
Artık bir yetkilendirme kodu edindiniz, `code` uç noktaya BIR post isteği göndererek hedeflenen kaynağa belirteç için öğesini kullanabilirsiniz `/token` . Azure AD B2C, istek içinde kapsamlarını belirterek diğer API 'ler için her zamanki gibi [erişim belirteçleri isteyebilirsiniz](access-tokens.md#request-a-token) .

Ayrıca, uygulamanın istemci KIMLIĞINI istenen kapsam olarak kullanma kuralına göre uygulamanızın kendi arka uç Web API 'SI için bir erişim belirteci isteyebilirsiniz (Bu, "hedef kitle" olarak bu istemci KIMLIĞINE sahip bir erişim belirtecine neden olur):

```http
POST https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1

Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&code_verifier=ThisIsntRandomButItNeedsToBe43CharactersLong 
```

| Parametre | Gerekli mi? | Description |
| --- | --- | --- |
|Kiracı| Gerekli | Azure AD B2C kiracınızın adı|
|ilkesinin| Gerekli| Yetkilendirme kodunu almak için kullanılan Kullanıcı akışı. Bu istekte farklı bir Kullanıcı akışı kullanamazsınız. |
| client_id |Gerekli |[Azure Portal](https://portal.azure.com)uygulamanıza atanan uygulama kimliği.|
| client_secret | Evet, Web Apps | [Azure Portal](https://portal.azure.com/)oluşturulan uygulama gizli dizisi. İstemci gizli dizileri, istemcinin güvenli bir şekilde istemci gizli depolayabileceği Web uygulaması senaryolarında Bu akışta kullanılır. Yerel uygulama (genel istemci) senaryolarında, istemci gizli dizileri güvenli bir şekilde saklanamaz ve bu nedenle bu çağrıda kullanılmaz. Bir istemci gizli anahtarı kullanırsanız, lütfen düzenli aralıklarla değiştirin. |
| grant_type |Gerekli |İzin türü. Yetkilendirme kodu akışı için, izin türü olmalıdır `authorization_code` . |
| scope |Önerilen |Kapsamların boşlukla ayrılmış listesi. Tek bir kapsam değeri, Azure AD 'ye, İstenen izinlerin her ikisi de belirtir. İstemci KIMLIĞINI kapsam olarak kullanmak, uygulamanızın aynı istemci KIMLIĞIYLE temsil edilen kendi hizmetinize veya Web API 'niz için kullanılabilecek bir erişim belirteci gerektiğini gösterir.  `offline_access`Kapsam, uygulamanızın kaynaklara uzun süreli erişim için yenileme belirteci gerektiğini gösterir.  Ayrıca, `openid` Azure AD B2C BIR kimlik belirteci istemek için kapsamını da kullanabilirsiniz. |
| kod |Gerekli |Akışın ilk baındaki elde ettiğiniz yetkilendirme kodu. |
| redirect_uri |Gerekli |Yetkilendirme kodunu aldığınız uygulamanın yeniden yönlendirme URI 'SI. |
| code_verifier | Önerilen | Authorization_code elde etmek için kullanılan aynı code_verifier. Yetkilendirme kodu verme isteğinde PKCE kullanılmışsa gereklidir. Daha fazla bilgi için bkz. [Pkce RFC](https://tools.ietf.org/html/rfc7636). |

Başarılı bir belirteç yanıtı şuna benzer:

```json
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```
| Parametre | Açıklama |
| --- | --- |
| not_before |Belirtecin geçerli kabul edildiği zaman (dönem süresi). |
| token_type |Belirteç türü değeri. Azure AD 'nin desteklediği tek tür taşıyıcı. |
| access_token |İstediğiniz imzalı JSON Web Token (JWT). |
| scope |Belirtecin geçerli olduğu kapsamlar. Ayrıca, daha sonra kullanmak üzere belirteçleri önbelleğe almak için kapsamları kullanabilirsiniz. |
| expires_in |Belirtecin geçerli olduğu sürenin uzunluğu (saniye cinsinden). |
| refresh_token |Bir OAuth 2,0 yenileme belirteci. Uygulama, geçerli belirtecin süresi dolduktan sonra ek belirteçler almak için bu belirteci kullanabilir. Yenileme belirteçleri uzun süreli. Bunları, uzun süreli kaynaklara erişimi sürdürmek için kullanabilirsiniz. Daha fazla bilgi için [Azure AD B2C belirteç başvurusuna](tokens-overview.md)bakın. |

Hata yanıtları şuna benzer:

```json
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parametre | Açıklama |
| --- | --- |
| error |Oluşan hata türlerini sınıflandırmak için kullanabileceğiniz bir hata kodu dizesi. Ayrıca, hatalara yanıt vermek için dizesini de kullanabilirsiniz. |
| error_description |Kimlik doğrulama hatasının temel nedenini belirlemenize yardımcı olabilecek belirli bir hata iletisi. |

## <a name="3-use-the-token"></a>3. belirteci kullanın
Bir erişim belirtecini başarıyla edindiniz, artık, arka uç Web API 'lerinize yönelik isteklerindeki belirteçleri üstbilgiye ekleyerek kullanabilirsiniz `Authorization` :

```http
GET /tasks
Host: mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="4-refresh-the-token"></a>4. belirteci yenileyin
Erişim belirteçleri ve KIMLIK belirteçleri kısa ömürlü. Süreleri dolduktan sonra, kaynaklara erişmeye devam etmek için bunları yenilemeniz gerekir. Bunu yapmak için uç noktaya başka bir POST isteği gönderin `/token` . Bu kez, yerine şunu sağlayın `refresh_token` `code` :

```http
POST https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1

Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| Parametre | Gerekli mi? | Description |
| --- | --- | --- |
|Kiracı| Gerekli | Azure AD B2C kiracınızın adı|
|ilkesinin |Gerekli |Özgün yenileme belirtecini almak için kullanılan Kullanıcı akışı. Bu istekte farklı bir Kullanıcı akışı kullanamazsınız. |
| client_id |Gerekli |[Azure Portal](https://portal.azure.com)uygulamanıza atanan uygulama kimliği. |
| client_secret | Evet, Web Apps | [Azure Portal](https://portal.azure.com/)oluşturulan uygulama gizli dizisi. İstemci gizli dizileri, istemcinin güvenli bir şekilde istemci gizli depolayabileceği Web uygulaması senaryolarında Bu akışta kullanılır. Yerel uygulama (genel istemci) senaryolarında, istemci gizli dizileri güvenli bir şekilde saklanamaz ve bu nedenle bu çağrıda kullanılmaz. Bir istemci gizli anahtarı kullanırsanız, lütfen düzenli aralıklarla değiştirin. |
| grant_type |Gerekli |İzin türü. Yetkilendirme kodu akışının bu bacağı için, izin türü olmalıdır `refresh_token` . |
| scope |Önerilen |Kapsamların boşlukla ayrılmış listesi. Tek bir kapsam değeri, Azure AD 'ye, İstenen izinlerin her ikisi de belirtir. İstemci KIMLIĞINI kapsam olarak kullanmak, uygulamanızın aynı istemci KIMLIĞIYLE temsil edilen kendi hizmetinize veya Web API 'niz için kullanılabilecek bir erişim belirteci gerektiğini gösterir.  `offline_access`Kapsam, uygulamanızın kaynaklara uzun süreli erişim için yenileme belirtecine sahip olacağını gösterir.  Ayrıca, `openid` Azure AD B2C BIR kimlik belirteci istemek için kapsamını da kullanabilirsiniz. |
| redirect_uri |İsteğe Bağlı |Yetkilendirme kodunu aldığınız uygulamanın yeniden yönlendirme URI 'SI. |
| refresh_token |Gerekli |Akışın ikinci Bada aldığınız orijinal yenileme belirteci. |

Başarılı bir belirteç yanıtı şuna benzer:

```json
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```
| Parametre | Açıklama |
| --- | --- |
| not_before |Belirtecin geçerli kabul edildiği zaman (dönem süresi). |
| token_type |Belirteç türü değeri. Azure AD 'nin desteklediği tek tür taşıyıcı. |
| access_token |İstediğiniz imzalı JWT. |
| scope |Belirtecin geçerli olduğu kapsamlar. Ayrıca kapsamları, daha sonra kullanmak üzere belirteçleri önbelleğe almak için de kullanabilirsiniz. |
| expires_in |Belirtecin geçerli olduğu sürenin uzunluğu (saniye cinsinden). |
| refresh_token |Bir OAuth 2,0 yenileme belirteci. Uygulama, geçerli belirtecin süresi dolduktan sonra ek belirteçler almak için bu belirteci kullanabilir. Belirteçleri yenileme uzun süreli olduğundan, uzun süre boyunca kaynaklara erişimi sürdürmek için kullanılabilir. Daha fazla bilgi için [Azure AD B2C belirteç başvurusuna](tokens-overview.md)bakın. |

Hata yanıtları şuna benzer:

```json
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parametre | Açıklama |
| --- | --- |
| error |Oluşan hata türlerini sınıflandırmakta kullanabileceğiniz bir hata kodu dizesi. Ayrıca, hatalara yanıt vermek için dizesini de kullanabilirsiniz. |
| error_description |Kimlik doğrulama hatasının temel nedenini belirlemenize yardımcı olabilecek belirli bir hata iletisi. |

## <a name="use-your-own-azure-ad-b2c-directory"></a>Kendi Azure AD B2C dizininizi kullanın
Bu istekleri kendiniz denemek için aşağıdaki adımları izleyin. Bu makalede kullandığımız örnek değerleri kendi değerlerinizle değiştirin.

1. [Azure AD B2C bir dizin oluşturun](tutorial-create-tenant.md). İsteklerde dizininizin adını kullanın.
2. Uygulama KIMLIĞI ve yeniden yönlendirme URI 'SI elde etmek için [bir uygulama oluşturun](tutorial-register-applications.md) . Uygulamanıza yerel bir istemci ekleyin.
3. Kullanıcı akış adlarınızı almak için [Kullanıcı akışlarınızı oluşturun](user-flow-overview.md) .
