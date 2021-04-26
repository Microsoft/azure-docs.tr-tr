---
title: OpenID Connect ile Web 'de oturum açma-Azure Active Directory B2C
description: Azure Active Directory B2C 'de OpenID Connect kimlik doğrulama protokolünü kullanarak Web uygulamaları oluşturun.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 87415fc98bbcc9331ae4ff6282a65c85b570042d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104579782"
---
# <a name="web-sign-in-with-openid-connect-in-azure-active-directory-b2c"></a>Azure Active Directory B2C 'de OpenID Connect ile Web oturumu açma

OpenID Connect, OAuth 2,0 üzerinde oluşturulan ve kullanıcıları Web uygulamalarına güvenli bir şekilde imzalamak için kullanılabilen bir kimlik doğrulama protokolüdür. OpenID Connect 'in Azure Active Directory B2C (Azure AD B2C) uygulamasını kullanarak, Web uygulamalarınızda bulunan kaydolma, oturum açma ve diğer kimlik yönetimi deneyimlerini Azure Active Directory (Azure AD) için dış olarak aktarabilirsiniz. Bu kılavuzda, bunu dilden bağımsız bir şekilde nasıl yapabileceğiniz gösterilmektedir. Açık kaynaklı kitaplıklarımızın hiçbirini kullanmadan HTTP iletilerinin nasıl gönderileceğini ve alınacağını açıklar.

> [!NOTE]
> Açık kaynaklı kimlik doğrulama kitaplıklarının çoğu uygulamanızın JWT belirteçlerini elde edin ve doğrular. Kendi kodunuzu uygulamak yerine bu seçenekleri araştırmayı öneririz. Daha fazla bilgi için bkz. [Microsoft kimlik doğrulama kitaplığı (msal)](../active-directory/develop/msal-overview.md)ve [Microsoft Identity Web kimlik doğrulaması kitaplığı](../active-directory/develop/microsoft-identity-web.md)'na genel bakış.

[OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) , OAuth 2,0 *Yetkilendirme* protokolünü bir *kimlik doğrulama* protokolü olarak kullanılmak üzere genişletir. Bu kimlik doğrulama protokolü, çoklu oturum açma gerçekleştirmenize olanak tanır. İstemcinin kullanıcı kimliğini doğrulamasını ve Kullanıcı hakkında temel profil bilgilerini almasını sağlayan bir *kimlik belirteci* kavramını tanıtır.

OAuth 2,0 ' i genişlettiğinden, uygulamaların *erişim belirteçlerini* güvenli bir şekilde almasına de olanak sağlar. Bir [yetkilendirme sunucusu](protocols-overview.md)tarafından güvenliği sağlanmış kaynaklara erişmek için erişim belirteçlerini kullanabilirsiniz. Sunucuda barındırılan ve bir tarayıcıdan erişilen bir Web uygulaması oluşturuyorsanız OpenID Connect önerilir. Belirteçler hakkında daha fazla bilgi için bkz. [Azure Active Directory B2C belirteçlere genel bakış](tokens-overview.md)

Azure AD B2C, standart OpenID Connect protokolünü basit kimlik doğrulaması ve yetkilendirmeden daha fazlasını yapmak için genişletir. Uygulamanıza kaydolma, oturum açma ve profil yönetimi gibi kullanıcı deneyimleri eklemek için OpenID Connect 'i kullanmanızı sağlayan [Kullanıcı akışı parametresini](user-flow-overview.md)tanıtır.

## <a name="send-authentication-requests"></a>Kimlik doğrulama isteklerini gönder

Web uygulamanızın kullanıcı kimliğini doğrulaması ve bir Kullanıcı akışı çalıştırması gerektiğinde, kullanıcıyı `/authorize` uç noktaya yönlendirebilir. Kullanıcı, Kullanıcı akışına bağlı olarak eylem gerçekleştirir.

Bu istekte istemci, parametresindeki kullanıcıdan almaları gereken izinleri gösterir `scope` ve çalıştırılacak Kullanıcı akışını belirtir. İsteğin nasıl çalıştığına ilişkin bir fikir almak için, isteği bir tarayıcıya yapıştırmayı ve çalıştırmayı deneyin. `{tenant}`Kiracınızın adıyla değiştirin. `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6`Daha önce kiracınıza kaydettiğiniz uygulamanın uygulama kimliğiyle değiştirin. Ayrıca, ilke adını ( `{policy}` ) kiracınızda bulunan ilke adıyla (örneğin,) değiştirin `b2c_1_sign_in` .

```http
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
```

| Parametre | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| Kiracı | Yes | Azure AD B2C kiracınızın adı |
| ilkesinin | Yes | Çalıştırılacak Kullanıcı akışı. Azure AD B2C kiracınızda oluşturduğunuz Kullanıcı akışının adını belirtin. Örneğin: `b2c_1_sign_in` , `b2c_1_sign_up` , veya `b2c_1_edit_profile` . |
| client_id | Yes | [Azure Portal](https://portal.azure.com/) uygulamanıza atanan uygulama kimliği. |
| nonce | Yes | İstek olarak ortaya çıkan KIMLIK belirtecine dahil edilen isteğe (uygulama tarafından oluşturulan) dahil bir değer. Daha sonra uygulama, belirteç yeniden yürütme saldırılarını azaltmak için bu değeri doğrulayabilirler. Değer genellikle, isteğin kaynağını belirlemek için kullanılan rastgele benzersiz bir dizedir. |
| response_type | Yes | OpenID Connect için bir KIMLIK belirteci içermelidir. Web uygulamanız, bir Web API 'SI çağırmak için belirteçler de gerekiyorsa, kullanabilirsiniz `code+id_token` . |
| scope | Yes | Kapsamların boşlukla ayrılmış listesi. `openid`Kapsam, kullanıcıya oturum açma ve kimlik belirteçleri biçimindeki Kullanıcı hakkında veri edinme iznini gösterir. `offline_access`Kapsam, Web uygulamaları için isteğe bağlıdır. Uygulamanızın, kaynaklara genişletilmiş erişim için *yenileme belirtecine* sahip olacağını belirtir. |
| isteme | No | Gerekli Kullanıcı etkileşimi türü. Şu anda geçerli olan tek değer `login` , kullanıcıyı bu istek üzerine kimlik bilgilerini girmeye zorlayan olur. |
| redirect_uri | No | Uygulamanızın `redirect_uri` kimlik doğrulama yanıtlarının gönderilebileceği ve alınabileceği, uygulamanızın parametresi. `redirect_uri`URL kodlamalı olması dışında, Azure Portal kaydettiğiniz parametrelerden biriyle tam olarak eşleşmesi gerekir. |
| response_mode | No | Elde edilen yetkilendirme kodunu uygulamanıza geri göndermek için kullanılan yöntem. Ya da olabilir `query` `form_post` `fragment` .  `form_post`En iyi güvenlik için yanıt modu önerilir. |
| state | No | İsteğin belirteç yanıtında de döndürülen bir değeri. İstediğiniz herhangi bir içerik dizesi olabilir. Rastgele oluşturulan benzersiz bir değer genellikle siteler arası istek sahteciliği saldırılarını önlemek için kullanılır. Durum Ayrıca, kullanıcının uygulamadaki durumuyla ilgili bilgileri, üzerinde bulundukları sayfa gibi kimlik doğrulama isteği yapılmadan önce kodlamak için de kullanılır. |
| login_hint | No| , Oturum açma sayfasının oturum açma adı alanını önceden dolduraiçin kullanılabilir. Daha fazla bilgi için bkz. [oturum açma adını önceden girme](direct-signin.md#prepopulate-the-sign-in-name).  |
| domain_hint | No| Oturum açma için kullanılması gereken sosyal kimlik sağlayıcısı hakkında Azure AD B2C için bir ipucu sağlar. Geçerli bir değer dahil edilse, Kullanıcı doğrudan kimlik sağlayıcısı oturum açma sayfasına gider.  Daha fazla bilgi için bkz. [oturum açma oturumunu bir sosyal sağlayıcıya yeniden yönlendirme](direct-signin.md#redirect-sign-in-to-a-social-provider). |
| Özel parametreler | No| [Özel ilkelerle](custom-policy-overview.md)kullanılabilecek özel parametreler. Örneğin, [dinamik özel sayfa içerik URI 'si](customize-ui-with-html.md?pivots=b2c-custom-policy#configure-dynamic-custom-page-content-uri)veya [anahtar-değer talep çözücüler](claim-resolver-overview.md#oauth2-key-value-parameters). |

Bu noktada, kullanıcıdan iş akışını tamamlaması istenir. Kullanıcının Kullanıcı adı ve parolasını girmesi, sosyal kimlik ile oturum açması veya dizine kaydolmanız gerekebilir. Kullanıcı akışının nasıl tanımlandığına bağlı olarak başka herhangi bir sayıda adım olabilir.

Kullanıcı Kullanıcı akışını tamamladıktan sonra, `redirect_uri` parametresinde belirtilen yöntemi kullanarak, belirtilen parametrede uygulamanıza bir yanıt döndürülür `response_mode` . Yanıt, Kullanıcı akışından bağımsız olarak, önceki durumların her biri için aynıdır.

Kullanarak başarılı bir yanıt `response_mode=fragment` şöyle görünür:

```http
GET https://aadb2cplayground.azurewebsites.net/#
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parametre | Açıklama |
| --------- | ----------- |
| id_token | Uygulamanın istediği KIMLIK belirteci. KIMLIK belirtecini, kullanıcının kimliğini doğrulamak ve kullanıcıyla oturum başlatmak için kullanabilirsiniz. |
| kod | Kullandıysanız, uygulamanın istediği yetkilendirme kodu `response_type=code+id_token` . Uygulama, bir hedef kaynak için erişim belirteci istemek üzere yetkilendirme kodunu kullanabilir. Yetkilendirme kodları genellikle yaklaşık 10 dakika sonra sona erer. |
| state | İsteğe bir `state` parametre dahil ise, yanıtta aynı değer görünmelidir. Uygulamanın, `state` istek ve yanıt değerlerinin özdeş olduğunu doğrulaması gerekir. |

Ayrıca, `redirect_uri` uygulamanın bunları uygun şekilde işleyebilmesi için hataya yanıt, parametreye gönderilebilir:

```http
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parametre | Açıklama |
| --------- | ----------- |
| error | Oluşan hata türlerini sınıflandırmak için kullanılabilen bir kod. |
| error_description | Bir kimlik doğrulama hatasının temel nedenini belirlemenize yardımcı olabilecek belirli bir hata iletisi. |
| state | İsteğe bir `state` parametre dahil ise, yanıtta aynı değer görünmelidir. Uygulamanın, `state` istek ve yanıt değerlerinin özdeş olduğunu doğrulaması gerekir. |

## <a name="validate-the-id-token"></a>KIMLIK belirtecini doğrulama

Yalnızca bir KIMLIK belirtecinin alınması, kullanıcının kimliğini doğrulamak için yeterli değil. KIMLIK belirtecinin imzasını doğrulayın ve uygulamanızın gereksinimlerine göre belirteçteki talepleri doğrulayın. Azure AD B2C, belirteçleri imzalamak ve bunların geçerli olduğunu doğrulamak için [JSON Web belirteçleri (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) ve ortak anahtar şifrelemesi kullanır. 

> [!NOTE]
> Açık kaynaklı kimlik doğrulama kitaplıklarının çoğu, uygulamanız için JWT belirteçlerini doğrular. Kendi doğrulama mantığınızı uygulamak yerine bu seçenekleri araştırmayı öneririz. Daha fazla bilgi için bkz. [Microsoft kimlik doğrulama kitaplığı (msal)](../active-directory/develop/msal-overview.md)ve [Microsoft Identity Web kimlik doğrulaması kitaplığı](../active-directory/develop/microsoft-identity-web.md)'na genel bakış.

Azure AD B2C, bir uygulamanın çalışma zamanında Azure AD B2C hakkında bilgi almasına izin veren bir OpenID Connect meta veri uç noktası vardır. Bu bilgiler uç noktaları, belirteç içerikleri ve belirteç imzalama anahtarlarını içerir. B2C kiracınızdaki her Kullanıcı akışı için bir JSON meta veri belgesi vardır. Örneğin, içinde Kullanıcı akışı için meta veri belgesi `b2c_1_sign_in` `fabrikamb2c.onmicrosoft.com` şu konumda bulunur:

```http
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/v2.0/.well-known/openid-configuration
```

Bu yapılandırma belgesinin özelliklerinden biri `jwks_uri` , aynı kullanıcı akışı değeri şöyle olacaktır:

```http
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/discovery/v2.0/keys
```

Bir KIMLIK belirtecini imzalarken (ve meta verileri nereden alacağınız) hangi Kullanıcı akışının kullanıldığını anlamak için iki seçeneğiniz vardır. İlk olarak, Kullanıcı akış adı, `acr` kimlik belirtecindeki talebe dahil edilir. Diğer bir seçenek de, isteği keserken Kullanıcı akışını parametre değerinde kodlamak `state` ve sonra hangi Kullanıcı akışının kullanıldığını anlamak için onu çözmez. Her iki yöntem de geçerlidir.

Meta veri belgesini OpenID Connect meta veri uç noktasından aldıktan sonra, KIMLIK belirtecinin imzasını doğrulamak için RSA 256 ortak anahtarlarını kullanabilirsiniz. Bu uç noktada, her biri bir talep tarafından tanımlanan birden fazla anahtar olabilir `kid` . KIMLIK belirtecinin üstbilgisi de bir `kid` talep içerir, bu da kimlik belirtecini imzalamak için bu anahtarların hangisinin kullanıldığını gösterir.

Azure AD B2C belirteçleri doğrulamak için üs (e) ve mod (n) kullanarak ortak anahtarı oluşturmanız gerekir. Bunun nasıl yapılacağını ilgili programlama dilinizde uygun şekilde belirlemeniz gerekir. RSA protokolüyle ortak anahtar oluşturmayla ilgili resmi belgeler şurada bulunabilir: https://tools.ietf.org/html/rfc3447#section-3.1

KIMLIK belirtecinin imzasını doğruladıktan sonra doğrulamanız gereken birkaç talep vardır. Örneğin:

- Belirteç yeniden `nonce` yürütme saldırılarını önleme talebini doğrulayın. Değeri, oturum açma isteğinde belirtidikleriniz olmalıdır.
- `aud`Kimlik belirtecinin uygulamanız için verildiğinden emin olmak için talebi doğrulayın. Değeri uygulamanızın uygulama KIMLIĞI olmalıdır.
- `iat` `exp` Kimlik belirtecinin zaman aşımına ermediğinden emin olmak için ve taleplerini doğrulayın.

Ayrıca gerçekleştirmeniz gereken birkaç doğrulama daha vardır. Doğrulamalar, [OpenID Connect Core belirtiminde](https://openid.net/specs/openid-connect-core-1_0.html)ayrıntılı olarak açıklanmıştır. Senaryonuza bağlı olarak ek talepler de doğrulamak isteyebilirsiniz. Bazı ortak doğrulamalar şunları içerir:

- Kullanıcı/kuruluşun uygulama için kaydolmasını sağlama.
- Kullanıcının uygun yetkilendirme/ayrıcalıklara sahip olduğundan emin olma.
- Azure AD Multi-Factor Authentication gibi belirli bir kimlik doğrulama gücünün meydana geldiğinden emin olma.

KIMLIK belirteci doğrulandıktan sonra kullanıcıyla bir oturum başlatabilirsiniz. Uygulamanızdaki Kullanıcı hakkında bilgi edinmek için KIMLIK belirtecindeki talepler ' i kullanabilirsiniz. Bu bilgiler için kullanımları görüntüleme, kayıtlar ve yetkilendirme içerir.

## <a name="get-a-token"></a>Belirteç al

Web uygulamanızın yalnızca Kullanıcı akışlarını çalıştırması gerekiyorsa, sonraki birkaç bölümü atlayabilirsiniz. Bu bölümler yalnızca bir Web API 'sine kimliği doğrulanmış çağrılar yapması gereken Web uygulamaları için geçerlidir ve ayrıca Azure AD B2C tarafından korunur.

`response_type=code+id_token`Uç noktaya istek göndererek istenen kaynağa bir belirteç için aldığınız yetkilendirme kodunu (kullanarak) kullanabilirsiniz `POST` `/token` . Azure AD B2C, istek içinde kapsamlarını belirterek [diğer API 'ler için her zamanki gibi erişim belirteçleri isteyebilirsiniz](access-tokens.md#request-a-token) .

Ayrıca, uygulamanın istemci KIMLIĞINI istenen kapsam olarak kullanma kuralına göre uygulamanızın kendi arka uç Web API 'SI için bir erişim belirteci isteyebilirsiniz (Bu, "hedef kitle" olarak bu istemci KIMLIĞINE sahip bir erişim belirtecine neden olur):

```http
POST {tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1
Host: {tenant}.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| Parametre | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| Kiracı | Yes | Azure AD B2C kiracınızın adı |
| ilkesinin | Yes | Yetkilendirme kodunu almak için kullanılan Kullanıcı akışı. Bu istekte farklı bir Kullanıcı akışı kullanamazsınız. Bu parametreyi, POST gövdesine değil sorgu dizesine ekleyin. |
| client_id | Yes | [Azure Portal](https://portal.azure.com/) uygulamanıza atanan uygulama kimliği. |
| client_secret | Evet, Web Apps | [Azure Portal](https://portal.azure.com/)oluşturulan uygulama gizli dizisi. İstemci gizli dizileri, istemcinin güvenli bir şekilde istemci gizli depolayabileceği Web uygulaması senaryolarında Bu akışta kullanılır. Yerel uygulama (genel istemci) senaryolarında, istemci gizli dizileri güvenli bir şekilde saklanamaz, bu nedenle bu akışta kullanılmaz. İstemci parolası kullanılıyorsa, lütfen düzenli aralıklarla değiştirin. |
| kod | Yes | Kullanıcı akışının başlangıcında elde ettiğiniz yetkilendirme kodu. |
| grant_type | Yes | Yetkilendirme kodu akışı için olması gereken izin türü `authorization_code` . |
| redirect_uri | Yes | `redirect_uri`Yetkilendirme kodunu aldığınız uygulamanın parametresi. |
| scope | No | Kapsamların boşlukla ayrılmış listesi. `openid`Kapsam, kullanıcının oturum açma iznini gösterir ve id_token parametreleri biçiminde kullanıcı hakkında veri alabilir. Uygulamanın kendi arka uç Web API 'sine, istemcisiyle aynı uygulama KIMLIĞIYLE temsil edilen belirteçleri almak için kullanılabilir. `offline_access`Kapsam, uygulamanızın kaynaklara genişletilmiş erişim için bir yenileme belirteci gerektiğini gösterir. |

Başarılı bir belirteç yanıtı şöyle görünür:

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
| --------- | ----------- |
| not_before | Belirtecin geçerli kabul edildiği zaman (dönem süresi). |
| token_type | Belirteç türü değeri. `Bearer` desteklenen tek türdür. |
| access_token | İstediğiniz imzalı JWT belirteci. |
| scope | Belirtecin geçerli olduğu kapsamlar. |
| expires_in | Erişim belirtecinin geçerli olduğu sürenin uzunluğu (saniye cinsinden). |
| refresh_token | Bir OAuth 2,0 yenileme belirteci. Uygulama, geçerli belirtecin süresi dolduktan sonra ek belirteçler almak için bu belirteci kullanabilir. Yenileme belirteçleri, kaynakların uzun süreleriyle erişiminin korunması için kullanılabilir. Kapsam, `offline_access` yenileme belirteci almak için hem yetkilendirme hem de belirteç isteklerinde kullanılmış olmalıdır. |

Hata yanıtları şöyle görünür:

```json
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parametre | Açıklama |
| --------- | ----------- |
| error | Oluşan hata türlerini sınıflandırmak için kullanılan kod. |
| error_description | Kimlik doğrulama hatasının temel nedenini belirlemenize yardımcı olabilecek bir ileti. |

## <a name="use-the-token"></a>Belirteci kullanma

Bir erişim belirtecini başarıyla edindiniz, artık, arka uç Web API 'lerinize yönelik isteklerindeki belirteçleri üstbilgiye ekleyerek kullanabilirsiniz `Authorization` :

```http
GET /tasks
Host: mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-token"></a>Belirteci Yenile

KIMLIK belirteçlerinin kısa bir süre içinde süresi doluyor. Kaynaklara erişmek için süre dolduktan sonra belirteçleri yenileyin. Bir belirteci `POST` , uç noktaya başka bir istek göndererek yenileyebilirsiniz `/token` . Bu kez parametre `refresh_token` yerine parametresini sağlayın `code` :

```http
POST {tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1
Host: {tenant}.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=openid offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| Parametre | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| Kiracı | Yes | Azure AD B2C kiracınızın adı |
| ilkesinin | Yes | Özgün yenileme belirtecini almak için kullanılan Kullanıcı akışı. Bu istekte farklı bir Kullanıcı akışı kullanamazsınız. Bu parametreyi, POST gövdesine değil sorgu dizesine ekleyin. |
| client_id | Yes | [Azure Portal](https://portal.azure.com/) uygulamanıza atanan uygulama kimliği. |
| client_secret | Evet, Web Apps | [Azure Portal](https://portal.azure.com/)oluşturulan uygulama gizli dizisi. İstemci gizli dizileri, istemcinin güvenli bir şekilde istemci gizli depolayabileceği Web uygulaması senaryolarında Bu akışta kullanılır. Yerel uygulama (genel istemci) senaryolarında, istemci gizli dizileri güvenli bir şekilde saklanamaz, bu nedenle bu çağrıda kullanılmaz. İstemci parolası kullanılıyorsa, lütfen düzenli aralıklarla değiştirin. |
| grant_type | Yes | `refresh_token`Yetkilendirme kodu akışının bu bölümü için olması gereken izin türü. |
| refresh_token | Yes | Akışın ikinci bölümünde alınan orijinal yenileme belirteci. `offline_access`Bir yenileme belirteci almak için kapsamın hem yetkilendirme hem de belirteç isteklerinde kullanılması gerekir. |
| redirect_uri | No | `redirect_uri`Yetkilendirme kodunu aldığınız uygulamanın parametresi. |
| scope | No | Kapsamların boşlukla ayrılmış listesi. `openid`Kapsam, kullanıcıya oturum açma ve kimlik belirteçleri biçimindeki Kullanıcı hakkında veri edinme iznini gösterir. Uygulamanın kendi arka uç Web API 'sine belirteç göndermek için, istemcisiyle aynı uygulama KIMLIĞIYLE temsil edilen belirteçleri kullanabilirsiniz. `offline_access`Kapsam, uygulamanızın kaynaklara genişletilmiş erişim için bir yenileme belirteci gerektiğini gösterir. |

Başarılı bir belirteç yanıtı şöyle görünür:

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
| --------- | ----------- |
| not_before | Belirtecin geçerli kabul edildiği zaman (dönem süresi). |
| token_type | Belirteç türü değeri. `Bearer` desteklenen tek türdür. |
| access_token | İstenen işaretli JWT belirteci. |
| scope | Belirtecin geçerli olduğu kapsam. |
| expires_in | Erişim belirtecinin geçerli olduğu sürenin uzunluğu (saniye cinsinden). |
| refresh_token | Bir OAuth 2,0 yenileme belirteci. Uygulama, geçerli belirtecin süresi dolduktan sonra ek belirteçler almak için bu belirteci kullanabilir. Yenileme belirteçleri, kaynakların uzun süreleriyle erişiminin korunması için kullanılabilir. |

Hata yanıtları şöyle görünür:

```json
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parametre | Açıklama |
| --------- | ----------- |
| error | Oluşan hata türlerini sınıflandırmak için kullanılan kod. |
| error_description | Kimlik doğrulama hatasının temel nedenini belirlemenize yardımcı olabilecek bir ileti. |

## <a name="send-a-sign-out-request"></a>Oturum kapatma isteği gönder

Kullanıcının uygulamadan oturum açmasını istediğinizde, uygulamanın tanımlama bilgilerini temizlemek veya oturumu Kullanıcı ile sonlandırmak yeterli değildir. Oturumu kapatmak için kullanıcıyı Azure AD B2C yönlendirin. Bunu yapmazsanız, Kullanıcı kimlik bilgilerini tekrar girmeden uygulamanız için yeniden kimlik doğrulaması yapabilir. Daha fazla bilgi için bkz. [Azure AD B2C oturum](session-behavior.md).

Kullanıcının oturumunu kapatmak için kullanıcıyı `end_session` daha önce açıklanan OpenID Connect meta veri belgesinde listelenen uç noktaya yönlendirin:

```http
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/logout?post_logout_redirect_uri=https%3A%2F%2Fjwt.ms%2F
```

| Parametre | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| Kiracı | Yes | Azure AD B2C kiracınızın adı |
| ilkesinin | Yes | Kullanıcıyı uygulamanızda imzalamak için kullanmak istediğiniz kullanıcı akışı. |
| id_token_hint| No | Son kullanıcının istemci ile geçerli kimlik doğrulamalı oturum hakkında bir ipucu olarak oturum kapatma uç noktasına geçirilecek daha önceden verilen bir KIMLIK belirteci. , `id_token_hint` `post_logout_redirect_uri` Uygulamasının Azure AD B2C uygulama ayarlarınızda kayıtlı bir yanıt URL 'si olmasını sağlar. Daha fazla bilgi için bkz. [oturum kapatma yeniden yönlendirmeye güvenli hale getirme](#secure-your-logout-redirect). |
| client_id | Eşleşen | [Azure Portal](https://portal.azure.com/) uygulamanıza atanan uygulama kimliği.<br><br>\**Bu, `Application` yalıtım SSO yapılandırması kullanılırken gereklidir ve oturum kapatma ISTEĞINDE _kimlik belirteci gerektir_ olarak ayarlanır `No` .* |
| post_logout_redirect_uri | No | Başarılı oturum kapatıldıktan sonra kullanıcının yeniden yönlendirilmesi gereken URL. Dahil değilse, kullanıcıya genel bir ileti gösterir Azure AD B2C. Bir sağlamazsanız `id_token_hint` , bu URL 'yi Azure AD B2C uygulama ayarlarınıza bir yanıt URL 'si olarak kaydetmemelisiniz. |
| state | No | İsteğe bir `state` parametre dahil ise, yanıtta aynı değer görünmelidir. Uygulamanın, `state` istek ve yanıt değerlerinin özdeş olduğunu doğrulaması gerekir. |

### <a name="secure-your-logout-redirect"></a>Logout yeniden yönlendirmenizi güvenli hale getirin

Oturum kapatıldıktan sonra, `post_logout_redirect_uri` uygulama için belirtilen yanıt URL 'lerinden bağımsız olarak, Kullanıcı parametresinde BELIRTILEN URI 'ye yönlendirilir. Ancak, geçerli bir `id_token_hint` değer geçirilirse ve **oturum kapatma ISTEKLERINDEKI kimlik belirteci gerektir** seçeneği açıksa Azure AD B2C, öğesinin `post_logout_redirect_uri` yeniden yönlendirmeyi gerçekleştirmeden önce uygulamanın yapılandırılmış yeniden yönlendirme URI 'lerinden biriyle eşleştiğini doğrular. Uygulama için eşleşen bir yanıt URL 'SI yapılandırılmamışsa, bir hata iletisi görüntülenir ve Kullanıcı yeniden yönlendirilmez.

Oturum kapatma isteklerindeki gerekli KIMLIK belirtecini ayarlamak için, bkz. [Azure Active Directory B2C oturum davranışını yapılandırma](session-behavior.md#secure-your-logout-redirect).

## <a name="next-steps"></a>Sonraki adımlar

- [Azure AD B2C oturum](session-behavior.md)hakkında daha fazla bilgi edinin.