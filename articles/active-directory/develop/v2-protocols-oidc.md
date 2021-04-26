---
title: Microsoft Identity platform ve OpenID Connect Protocol | Mavisi
titleSuffix: Microsoft identity platform
description: OpenID Connect kimlik doğrulama protokolünün Microsoft Identity platform uygulamasını kullanarak Web uygulamaları oluşturun.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 05/22/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: de1fcdc259de3f72e35feb411bcc836354352eb4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98752592"
---
# <a name="microsoft-identity-platform-and-openid-connect-protocol"></a>Microsoft Identity platform ve OpenID Connect Protokolü

OpenID Connect (OıDC), bir kullanıcıyı bir uygulamada güvenli bir şekilde oturum açmak için kullanabileceğiniz, OAuth 2,0 üzerinde oluşturulmuş bir kimlik doğrulama protokolüdür. Microsoft Identity platformunun OpenID Connect uygulamasını kullandığınızda uygulamalarınıza oturum açma ve API erişimi ekleyebilirsiniz. Bu makalede, bu dilden bağımsız olarak nasıl yapılacağı ve herhangi bir [Microsoft açık kaynak kitaplığı](reference-v2-libraries.md)kullanmadan http iletileri gönderme ve alma işlemlerinin nasıl yapılacağı açıklanır.

[OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) , OAuth 2,0 *Yetkilendirme* protokolünü *kimlik doğrulama* protokolü olarak kullanılmak üzere genişletir. böylece, OAuth kullanarak çoklu oturum açma yapabilirsiniz. OpenID Connect, istemcinin kullanıcı kimliğini doğrulamasına izin veren bir güvenlik belirteci olan *kimlik belirteci* kavramını tanıtır. KIMLIK belirteci, kullanıcı hakkındaki temel profil bilgilerini de alır. Ayrıca, Kullanıcı hakkında bilgi döndüren bir API olan [UserInfo uç noktasını](userinfo.md)da tanıtır. 


## <a name="protocol-diagram-sign-in"></a>Protokol diyagramı: oturum açma

En temel oturum açma akışı, sonraki diyagramda gösterilen adımlara sahiptir. Her adım bu makalede ayrıntılı olarak açıklanmaktadır.

![OpenID Connect protokolü: oturum açma](./media/v2-protocols-oidc/convergence-scenarios-webapp.svg)

## <a name="fetch-the-openid-connect-metadata-document"></a>OpenID Connect meta veri belgesini getir

OpenID Connect bir uygulamanın oturum açması için gereken bilgilerin çoğunu içeren bir meta veri belgesi [(RFC)](https://openid.net/specs/openid-connect-discovery-1_0.html) tanımlar. Bu, kullanılacak URL 'Ler ve hizmetin ortak imzalama anahtarlarının konumu gibi bilgileri içerir. Bu belgeyi, yetkilendirme URL 'sine bulma belge yolunu ekleyerek bulabilirsiniz:

Keşif belgesi yolu: `/.well-known/openid-configuration`

İniz `https://login.microsoftonline.com/{tenant}/v2.0`

`{tenant}`Dört değerden birini alabilir:

| Değer | Açıklama |
| --- | --- |
| `common` |Azure AD 'den hem kişisel Microsoft hesabı hem de iş veya okul hesabı olan kullanıcılar uygulamada oturum açabilir. |
| `organizations` |Yalnızca Azure AD 'den iş veya okul hesabı olan kullanıcılar uygulamada oturum açabilir. |
| `consumers` |Yalnızca kişisel Microsoft hesabı kullanıcılar uygulamada oturum açabilir. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` veya `contoso.onmicrosoft.com` | Yalnızca belirli bir Azure AD kiracısından gelen kullanıcılar (iş veya okul hesabı ile dizinde üye olup olmadıkları veya bir kişisel Microsoft hesabı olan dizinde konuklardır) uygulamada oturum açabilir. Azure AD kiracının kolay etki alanı adı ya da kiracının GUID tanımlayıcısı kullanılabilir. Ayrıca, kiracının yerine tüketici kiracısını de kullanabilirsiniz `9188040d-6c67-4c5b-b112-36a304b66dad` `consumers` .  |

Yetkili, Ulusal bulutlar arasında farklılık gösterir. Örneğin `https://login.microsoftonline.de` , Azure AD Almanya örneği. Genel bulutu kullanmıyorsanız, uygun olanı bulmak için lütfen [Ulusal bulut uç noktalarını](authentication-national-cloud.md#azure-ad-authentication-endpoints) gözden geçirin. `/v2.0/`Uç noktanın v 2.0 sürümünü kullanabilmeniz için kiracınızın ve isteğiniz içinde bulunduğundan emin olun.

> [!TIP]
> Deneyin! [https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration](https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration)Yapılandırmayı görmek için tıklayın `common` .

### <a name="sample-request"></a>Örnek istek

Genel bulutta ortak yetkili için UserInfo uç noktasını çağırmak üzere aşağıdakileri kullanın:

```http
GET /common/v2.0/.well-known/openid-configuration
Host: login.microsoftonline.com
```

### <a name="sample-response"></a>Örnek yanıt

Meta veriler basit bir JavaScript Nesne Gösterimi (JSON) belgesidir. Örnek için aşağıdaki kod parçacığına bakın. İçerik, [OpenID Connect belirtiminde](https://openid.net/specs/openid-connect-discovery-1_0.html#rfc.section.4.2)tamamen açıklanmıştır.

```json
{
  "authorization_endpoint": "https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize",
  "token_endpoint": "https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token",
  "token_endpoint_auth_methods_supported": [
    "client_secret_post",
    "private_key_jwt"
  ],
  "jwks_uri": "https://login.microsoftonline.com/{tenant}/discovery/v2.0/keys",
  "userinfo_endpoint": "https://graph.microsoft.com/oidc/userinfo",
  "subject_types_supported": [
      "pairwise"
  ],
  ...

}
```

Uygulamanızda, [talep eşleme](active-directory-claims-mapping.md) özelliğini kullanmanın bir sonucu olarak özel İmzalama anahtarları varsa, `appid` `jwks_uri` uygulamanızın imzalama anahtarı bilgilerine Işaret etmek için uygulama kimliğini içeren bir sorgu parametresi eklemeniz gerekir. Örneğin: `https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration?appid=6731de76-14a6-49ae-97bc-6eba6914391e` bir içerir `jwks_uri` `https://login.microsoftonline.com/{tenant}/discovery/v2.0/keys?appid=6731de76-14a6-49ae-97bc-6eba6914391e` .

Genellikle, bu meta veri belgesini bir OpenID Connect kitaplığı veya SDK 'Sı yapılandırmak için kullanacaksınız; Kitaplık, işini yapmak için meta verileri kullanır. Ancak, önceden oluşturulmuş bir OpenID Connect kitaplığı kullanmıyorsanız, Microsoft Identity platformunu kullanarak bir Web uygulamasında oturum açmak için bu makalenin geri kalanında bulunan adımları izleyebilirsiniz.

## <a name="send-the-sign-in-request"></a>Oturum açma isteğini gönder

Web uygulamanızın kimlik doğrulaması yapması gerektiğinde, kullanıcıyı `/authorize` uç noktaya yönlendirebilir. Bu istek, [OAuth 2,0 yetkilendirme kodu akışının](v2-oauth2-auth-code-flow.md)ilk bamasına benzer ve bu önemli farklılıklarla benzerdir:

* İstek, `openid` parametresine kapsamı içermelidir `scope` .
* `response_type`Parametrenin içermesi gerekir `id_token` .
* İstek, `nonce` parametresini içermelidir.

> [!IMPORTANT]
> /Authorization uç noktasından bir KIMLIK belirteci istemek için, [kayıt portalındaki](https://portal.azure.com) uygulama kaydının kimlik doğrulama sekmesinde ( `oauth2AllowIdTokenImplicitFlow` [uygulama bildiriminde](reference-app-manifest.md) bayrağını olarak ayarlayan) örtük id_tokens etkin izni olması gerekir `true` . Etkin değilse, bir `unsupported_response` hata döndürülür: "' response_type ' giriş parametresi için sağlanan değere bu istemci için izin verilmiyor. Beklenen değer ' Code ' "

Örnek:

```HTTP
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=form_post
&scope=openid
&state=12345
&nonce=678910
```

| Parametre | Koşul | Açıklama |
| --- | --- | --- |
| `tenant` | Gerekli | `{tenant}`Uygulamanın oturum açmasını denetlemek için isteğin yolundaki değeri kullanabilirsiniz. İzin verilen değerler, `common` , `organizations` `consumers` ve kiracı tanımlayıcılarıdır. Daha fazla bilgi için bkz. [protokol temelleri](active-directory-v2-protocols.md#endpoints). |
| `client_id` | Gerekli | [Azure Portal – uygulama kayıtları](https://go.microsoft.com/fwlink/?linkid=2083908) deneyiminin uygulamanıza atandığı **uygulama (istemci) kimliği** . |
| `response_type` | Gerekli | `id_token`OpenID Connect oturum açma için içermelidir. Ayrıca, gibi diğer değerleri de içerebilir `response_type` `code` . |
| `redirect_uri` | Önerilen | Uygulamanızın kimlik doğrulama yanıtlarının gönderilebileceği ve alınabileceği, uygulamanızın yeniden yönlendirme URI 'SI. Portalın, URL kodlamalı olması dışında, portala kaydettiğiniz yeniden yönlendirme URI 'lerinden biriyle tam olarak eşleşmesi gerekir. Mevcut değilse, uç nokta, kullanıcıyı öğesine geri göndermek için bir kayıtlı redirect_uri rastgele olarak seçer. |
| `scope` | Gerekli | Kapsamların boşlukla ayrılmış listesi. OpenID Connect için, `openid` izin Kullanıcı arabirimindeki "oturum aç" iznine çeviren kapsamı içermelidir. Bu istekte izin istemek için diğer kapsamları da dahil edebilirsiniz. |
| `nonce` | Gerekli | Talep olarak sonuçta elde edilen id_token değerine dahil edilecek, uygulama tarafından oluşturulan, isteğe dahil edilen bir değer. Uygulama, belirteç yeniden yürütme saldırılarını azaltmak için bu değeri doğrulayabilirler. Değer genellikle, isteğin kaynağını belirlemek için kullanılabilecek rastgele, benzersiz bir dizedir. |
| `response_mode` | Önerilen | Elde edilen yetkilendirme kodunu uygulamanıza geri göndermek için kullanılması gereken yöntemi belirtir. `form_post` veya `fragment` olabilir. Web uygulamaları için, `response_mode=form_post` belirteçlerin uygulamanıza en güvenli şekilde aktarılmasını sağlamak üzere kullanmanızı öneririz. |
| `state` | Önerilen | İstekte bulunan ve belirteç yanıtında de döndürülen bir değer. İstediğiniz herhangi bir içerik dizesi olabilir. Genellikle, [siteler arası istek sahteciliği saldırıları engellemek](https://tools.ietf.org/html/rfc6749#section-10.12)için rastgele oluşturulan benzersiz bir değer kullanılır. Durum Ayrıca, kullanıcının uygulamadaki durumuyla ilgili bilgileri, örneğin, Kullanıcı veya görüntüleme gibi kimlik doğrulama isteği yapılmadan önce kodlamak için de kullanılır. |
| `prompt` | İsteğe Bağlı | Gerekli kullanıcı etkileşiminin türünü gösterir. Şu anda yalnızca geçerli değerler `login` , ve ' dir `none` `consent` . `prompt=login`Talep, kullanıcıyı bu istek üzerine kimlik bilgilerini girmeye zorlar ve çoklu oturum açma işlemini geçersiz kılar. `prompt=none`Talep, tersidir. Bu talep, kullanıcının ' de etkileşimli bir istem ile sunulmamasını sağlar. İstek çoklu oturum açma yoluyla sessizce tamamlanamayacak, Microsoft Identity platform bir hata döndürür. `prompt=consent`Talep, Kullanıcı oturum açtıktan sonra OAuth onay iletişim kutusunu tetikler. İletişim kutusu kullanıcıdan uygulamaya izin vermesini ister. |
| `login_hint` | İsteğe Bağlı | Bu parametreyi, kullanıcının Kullanıcı adı ve e-posta adresi alanını daha önce biliyorsanız, Kullanıcı için oturum açma sayfasının Kullanıcı adı ve e-posta adresi alanını önceden doldurmanız için kullanabilirsiniz. Uygulamalar genellikle bu parametreyi, talebi kullanarak daha önceki bir oturum açma işleminden zaten ayıklandıktan sonra yeniden kimlik doğrulama sırasında kullanır `preferred_username` . |
| `domain_hint` | İsteğe Bağlı | Bir Federasyon dizinindeki kullanıcının bölgesi.  Bu, kullanıcının oturum açma sayfasında, biraz daha kolay bir kullanıcı deneyimi için gittiği e-posta tabanlı bulma işlemini atlar. AD FS gibi şirket içi bir dizin aracılığıyla federe olan kiracılar için, bu genellikle mevcut oturum açma oturumu nedeniyle sorunsuz bir oturum açma ile sonuçlanır. |

Bu noktada kullanıcıdan kimlik bilgilerini girmesi ve kimlik doğrulamasını tamamlaması istenir. Microsoft Identity platformu, kullanıcının sorgu parametresinde belirtilen izinlere onay verildiğini doğrular `scope` . Kullanıcı bu izinlerden birine onay vermediyse, Microsoft Identity platform kullanıcıdan gerekli izinleri vermesini ister. [İzinler, onay ve çok kiracılı uygulamalar](v2-permissions-and-consent.md)hakkında daha fazla bilgi edinebilirsiniz.

Kullanıcı kimlik doğrulamasından ve izin verdikten sonra, Microsoft Identity platformu, parametresinde belirtilen yöntemi kullanarak, belirtilen yeniden yönlendirme URI 'sindeki uygulamanıza bir yanıt döndürür `response_mode` .

### <a name="successful-response"></a>Başarılı yanıt

Kullanırken başarılı bir yanıt `response_mode=form_post` Şu şekilde görünür:

```HTTP
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| Parametre | Açıklama |
| --- | --- |
| `id_token` | Uygulamanın istediği KIMLIK belirteci. `id_token`Kullanıcının kimliğini doğrulamak ve kullanıcıyla oturum başlatmak için parametresini kullanabilirsiniz. KIMLIK belirteçleri ve içerikleri hakkında daha fazla bilgi için bkz. [ `id_tokens` başvuru](id-tokens.md). |
| `state` | İsteğe bir `state` parametre dahil ise, yanıtta aynı değer görünmelidir. Uygulama, istek ve yanıtta durum değerlerinin özdeş olduğunu doğrulamalıdır. |

### <a name="error-response"></a>Hata yanıtı

Uygulamanın bunları işleyebilmesi için yeniden yönlendirme URI 'sine de hata yanıtları gönderilebilir. Bir hata yanıtı şuna benzer:

```HTTP
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parametre | Açıklama |
| --- | --- |
| `error` | Oluşan hata türlerini sınıflandırmak ve hatalara tepki vermek için kullanabileceğiniz bir hata kodu dizesi. |
| `error_description` | Kimlik doğrulama hatasının temel nedenini belirlemenize yardımcı olabilecek belirli bir hata iletisi. |

### <a name="error-codes-for-authorization-endpoint-errors"></a>Yetkilendirme uç noktası hataları için hata kodları

Aşağıdaki tabloda hata yanıtının parametresinde döndürülebilecek hata kodları açıklanmaktadır `error` :

| Hata kodu | Description | İstemci eylemi |
| --- | --- | --- |
| `invalid_request` | Eksik, gerekli bir parametre gibi protokol hatası. |İsteği onarın ve yeniden gönderin. Bu, genellikle ilk sınama sırasında yakalanan bir geliştirme hatasıdır. |
| `unauthorized_client` | İstemci uygulaması bir yetkilendirme kodu isteğinde bulunamıyor. |Bu durum genellikle istemci uygulaması Azure AD 'de kayıtlı olmadığında veya kullanıcının Azure AD kiracısına eklenmediğinde oluşur. Uygulama kullanıcıya uygulamayı yüklemek ve Azure AD 'ye eklemek için yönergeler isteyebilir. |
| `access_denied` | Kaynak sahibi onay reddedildi. |İstemci uygulaması, kullanıcıya Kullanıcı tarafından bağlanmadığı takdirde devam edemediği konusunda bildirimde bulunabilir. |
| `unsupported_response_type` |Yetkilendirme sunucusu istekteki yanıt türünü desteklemiyor. |İsteği onarın ve yeniden gönderin. Bu, genellikle ilk sınama sırasında yakalanan bir geliştirme hatasıdır. |
| `server_error` | Sunucu beklenmeyen bir hatayla karşılaştı. |İsteği yeniden deneyin. Bu hatalar geçici koşullardan kaynaklanabilir. İstemci uygulaması, geçici bir hata nedeniyle yanıtı geciktirildiği kullanıcıya açıklanmayabilir. |
| `temporarily_unavailable` | Sunucu, isteği işlemek için geçici olarak çok meşgul. |İsteği yeniden deneyin. İstemci uygulaması, geçici bir durum nedeniyle yanıtı geciktirildiği kullanıcıya açıklanmayabilir. |
| `invalid_resource` | Hedef kaynak geçersiz, çünkü mevcut değil, Azure AD bu dosyayı bulamıyor veya doğru şekilde yapılandırılmamış. |Bu, varsa kaynağın kiracıda yapılandırılmadığı anlamına gelir. Uygulama kullanıcıya uygulamayı yükleme ve Azure AD 'ye ekleme yönergelerini isteyebilir. |

## <a name="validate-the-id-token"></a>KIMLIK belirtecini doğrulama

Yalnızca id_token alma, kullanıcının kimliğini doğrulamak için her zaman yeterli değildir; Ayrıca, id_token imzasını doğrulamanız ve uygulamanızın gereksinimleri uyarınca belirteçteki talepleri doğrulamanız gerekebilir. Tüm OıDC platformları gibi, Microsoft Identity platformu KIMLIK belirteçlerini imzalamak ve bunların geçerli olduğunu doğrulamak için [JSON Web belirteçleri (JWTs)](https://tools.ietf.org/html/rfc7519) ve ortak anahtar şifrelemesi kullanır.

Tüm uygulamalar, KIMLIK belirtecinin yerel uygulamalarını ve tek sayfa uygulamalarını doğrulamadan faydalanır, örneğin, KIMLIK belirtecini doğrulamadan nadiren yarar vardır.  Cihaza (veya tarayıcıya) fiziksel erişimi olan birisi, doğrulama mantığını atlamak için uygulamada hata ayıklamaya yönelik sahte belirteçler ve anahtarlar sağlamak üzere Web trafiğini cihaza düzenlemekten birçok şekilde doğrulamayı atlayabilir.  Diğer yandan, bir KIMLIK belirtecini yetkilendirme için kullanan Web uygulamaları ve API 'Lerin, verilere erişimi ele aldıklarından KIMLIK belirtecini dikkatle doğrulaması gerekir.

İd_token imzasını doğruladıktan sonra, doğrulamanız gereken birkaç talep vardır. [Belirteçleri doğrulama](id-tokens.md#validating-an-id_token) ve [anahtar geçişi Imzalama hakkındaki önemli bilgileri](active-directory-signing-key-rollover.md)içeren daha fazla bilgi için [ `id_token` başvuruya](id-tokens.md) bakın. Belirteçleri ayrıştırma ve doğrulama için bir kitaplık kullanmanızı öneririz. çoğu dil ve platform için en az bir kullanılabilir.

Senaryonuza bağlı olarak ek talepler de doğrulamak isteyebilirsiniz. Bazı ortak doğrulamalar şunları içerir:

* Kullanıcı/kuruluşun uygulama için kaydolmasını sağlama.
* Kullanıcının uygun yetkilere/ayrıcalıklara sahip olmasını sağlama
* [Çok faktörlü kimlik doğrulaması](../authentication/concept-mfa-howitworks.md)gibi belirli bir kimlik doğrulaması kuvvetinin gerçekleştiği doğrulanıyor.

İd_token doğrulandıktan sonra, Kullanıcı ile oturum başlatabilir ve uygulamanızdaki Kullanıcı hakkında bilgi edinmek için id_token talepleri kullanabilirsiniz. Bu bilgiler, görüntüleme, kayıtlar, kişiselleştirme vb. için kullanılabilir.

## <a name="protocol-diagram-access-token-acquisition"></a>Protokol diyagramı: erişim belirteci alma

Birçok Web uygulamasının ' de kullanıcıyı imzalayıp Kullanıcı adına OAuth kullanarak bir Web hizmetine erişmesi gerekir. Bu senaryo, OAuth yetkilendirme kodu akışını kullanıyorsanız erişim belirteçlerini almak için kullanabileceğiniz bir yetkilendirme kodu elde ederken, Kullanıcı kimlik doğrulaması için OpenID Connect 'i birleştirir.

Tam OpenID Connect oturum açma ve belirteç alma akışı, sonraki diyagrama benzer şekilde görünür. Makalenin sonraki bölümlerinde her adımın ayrıntılı olarak anlatıldığı anlatılmaktadır.

![OpenID Connect protokolü: belirteç alımı](./media/v2-protocols-oidc/convergence-scenarios-webapp-webapi.svg)

## <a name="get-an-access-token-to-call-userinfo"></a>UserInfo çağrısı için bir erişim belirteci alın

OıDC UserInfo uç noktası için bir belirteç almak üzere oturum açma isteğini değiştirin:

```HTTP
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e        // Your registered Application ID
&response_type=id_token%20token                       // this will return both an id_token and an access token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F       // Your registered redirect URI, URL encoded
&response_mode=form_post                              // 'form_post' or 'fragment'
&scope=openid+profile+email                           // `openid` is required.  `profile` and `email` provide additional information in the UserInfo endpoint the same way they do in an ID token. 
&state=12345                                          // Any value, provided by your app
&nonce=678910                                         // Any value, provided by your app
```

Uygulamanızın belirtecini almak için [yetkilendirme kodu akışını](v2-oauth2-auth-code-flow.md), [cihaz kodu akışını](v2-oauth2-device-code.md)veya yerine bir [yenileme belirtecini](v2-oauth2-auth-code-flow.md#refresh-the-access-token) de kullanabilirsiniz `response_type=token` .

> [!TIP]
> Bu isteği yürütmek için aşağıdaki bağlantıya tıklayın. Oturum açtıktan sonra, tarayıcınız `https://localhost/myapp/` BIR kimlik belirteci ve adres çubuğunda bir belirteç ile yeniden yönlendirilir. Bu isteğin `response_mode=fragment` yalnızca tanıtım amacıyla kullandığını unutmayın; bir WebApp için `form_post` mümkün olduğunda ek güvenlik için kullanmanızı öneririz. 
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token%20token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=fragment&scope=openid+profile+email&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

### <a name="successful-token-response"></a>Başarılı belirteç yanıtı

Kullanarak başarılı bir yanıt `response_mode=form_post` şöyle görünür:

```HTTP
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded
 access_token=eyJ0eXAiOiJKV1QiLCJub25jZSI6I....
 &token_type=Bearer
 &expires_in=3598
 &scope=email+openid+profile
 &id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI....
 &state=12345
```

Yanıt parametreleri, bunları elde etmek için kullanılan akıştan bağımsız olarak aynı şeyi ifade ederler.

| Parametre | Açıklama |
| --- | --- |
| `access_token` | UserInfo uç noktasını çağırmak için kullanılacak belirteç.|
| `token_type` | Her zaman "taşıyıcı" |
| `expires_in`| Erişim belirtecinin süresi dolana kadar geçen süre (saniye cinsinden). |
| `scope` | Erişim belirtecinde verilen izinler.  UserInfo uç noktasının MS Graph üzerinde barındırıldığından, daha önce uygulamaya verilmiş olmaları durumunda burada listelenen ek grafik kapsamları (ör. User. Read) olabileceğini unutmayın.  Bunun nedeni, belirli bir kaynağa yönelik belirtecin her zaman istemciye verilen her izni içermesinden kaynaklanır.  |
| `id_token` | Uygulamanın istediği KIMLIK belirteci. KIMLIK belirtecini, kullanıcının kimliğini doğrulamak ve kullanıcıyla oturum başlatmak için kullanabilirsiniz. Kimliğin kimlik belirteçleri ve içerikleri hakkında daha fazla ayrıntı [ `id_tokens` bulacaksınız.](id-tokens.md) |
| `state` | İsteğe bir durum parametresi dahil edilir, yanıtta aynı değer görünmelidir. Uygulama, istek ve yanıtta durum değerlerinin özdeş olduğunu doğrulamalıdır. |

### <a name="error-response"></a>Hata yanıtı

Ayrıca, uygulamanın bunları uygun şekilde işleyebilmesi için yeniden yönlendirme URI 'sine de hata yanıtları gönderilebilir. Bir hata yanıtı şuna benzer:

```HTTP
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parametre | Açıklama |
| --- | --- |
| `error` | Oluşan hata türlerini sınıflandırmak ve hatalara tepki vermek için kullanabileceğiniz bir hata kodu dizesi. |
| `error_description` | Kimlik doğrulama hatasının temel nedenini belirlemenize yardımcı olabilecek belirli bir hata iletisi. |

Olası hata kodlarının ve önerilen istemci yanıtlarının açıklaması için bkz. [Yetkilendirme uç noktası hataları Için hata kodları](#error-codes-for-authorization-endpoint-errors).

Bir yetkilendirme kodunuz ve bir KIMLIK belirteciniz varsa, Kullanıcı oturumunu açabilir ve adına erişim belirteçleri alabilirsiniz. Kullanıcıyı ' de imzalamak için KIMLIK belirtecini [tam olarak açıklandığı gibi](id-tokens.md#validating-an-id_token)doğrulamanız gerekir. Erişim belirteçlerini almak için, [OAuth kod akışı belgelerinde](v2-oauth2-auth-code-flow.md#request-an-access-token)açıklanan adımları izleyin.

### <a name="calling-the-userinfo-endpoint"></a>UserInfo uç noktasını çağırma

UserInfo uç noktasını bu belirteçle çağırma hakkında bilgi almak için [UserInfo belgelerini](userinfo.md#calling-the-api) gözden geçirin.

## <a name="send-a-sign-out-request"></a>Oturum kapatma isteği gönder

Kullanıcının uygulamanızdan oturumu kapatmak istediğinizde, uygulamanızın tanımlama bilgilerini temizlemek veya Kullanıcı oturumunu sonlandırmak yeterli değildir. Oturumu kapatmak için kullanıcıyı Microsoft Identity platformuna yönlendirmeniz de gerekir. Bunu yapmazsanız, Kullanıcı Microsoft Identity platformu ile geçerli bir çoklu oturum açma oturumuna sahip olacağından, kimlik bilgilerini tekrar girmeden uygulamanızı yeniden doğrular.

Kullanıcıyı `end_session_endpoint` OpenID Connect meta veri belgesinde listelenen öğesine yeniden yönlendirebilirsiniz:

```HTTP
GET https://login.microsoftonline.com/common/oauth2/v2.0/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
```

| Parametre | Koşul | Description |
| ----------------------- | ------------------------------- | ------------ |
| `post_logout_redirect_uri` | Önerilen | Başarıyla oturum kapatıldıktan sonra kullanıcının yeniden yönlendirildiği URL. Parametresi dahil edilmemişse, kullanıcıya Microsoft Identity platform tarafından oluşturulan genel bir ileti gösterilir. Bu URL, uygulama kayıt portalı 'nda uygulamanız için kayıtlı olan yeniden yönlendirme URI 'lerinden biriyle aynı olmalıdır. |

## <a name="single-sign-out"></a>Çoklu oturum kapatma

Kullanıcıyı uygulamasına yönlendirirseniz `end_session_endpoint` , Microsoft Identity platform kullanıcının oturumunu tarayıcıdan temizler. Ancak Kullanıcı, kimlik doğrulaması için Microsoft hesapları kullanan diğer uygulamalarda oturum açmış olabilir. Bu uygulamaların kullanıcıyı aynı anda imzalamasını sağlamak için, Microsoft Identity platformu, `LogoutUrl` kullanıcının şu anda oturum açmış olduğu tüm uygulamalara kayıtlı BIR http get isteği gönderir. Uygulamalar, kullanıcıyı tanıtan ve yanıt döndüren tüm oturumları temizleyerek bu isteğe yanıt vermelidir `200` . Uygulamanızda çoklu oturum açmayı desteklemek istiyorsanız, uygulamanızın kodunda böyle bir uygulama uygulamanız gerekir `LogoutUrl` . `LogoutUrl`Uygulamasını uygulama kayıt portalından ayarlayabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

* [UserInfo belgelerini](userinfo.md) gözden geçirin
* [Bir belirteçteki değerleri](active-directory-claims-mapping.md) şirket içi sistemlerinizdeki verilerle özelleştirmeyi öğrenin. 
* [Belirteçlere ek standart talepler eklemeyi](active-directory-optional-claims.md)öğrenin.  
