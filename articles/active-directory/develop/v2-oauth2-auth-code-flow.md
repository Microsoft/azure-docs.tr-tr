---
title: Microsoft Identity platform ve OAuth 2,0 yetkilendirme kodu akışı | Mavisi
titleSuffix: Microsoft identity platform
description: OAuth 2,0 kimlik doğrulama protokolünün Microsoft Identity platform uygulamasını kullanarak Web uygulamaları oluşturun.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: caa8f4efa60f8a42856f7cd8e78edf32fce956c6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105937150"
---
# <a name="microsoft-identity-platform-and-oauth-20-authorization-code-flow"></a>Microsoft Identity platform ve OAuth 2,0 yetkilendirme kodu akışı

OAuth 2,0 yetkilendirme kodu verme, Web API 'Leri gibi korumalı kaynaklara erişim kazanmak için bir cihaza yüklenen uygulamalarda kullanılabilir. OAuth 2,0 Microsoft Identity platform uygulamasını kullanarak, mobil ve Masaüstü uygulamalarınıza oturum açma ve API erişimi ekleyebilirsiniz.

Bu makalede, herhangi bir dili kullanarak uygulamanızdaki protokolde doğrudan programlanın nasıl yapılacağı açıklanır.  Mümkün olduğunda, [belirteçleri edinmek ve güvenli Web API 'lerini çağırmak](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)Için desteklenen Microsoft kimlik doğrulama KITAPLıKLARıNı (msal) kullanmanızı öneririz.  Ayrıca [, msal kullanan örnek uygulamalara](sample-v2-code.md)göz atın.

OAuth 2,0 yetkilendirme kodu akışı, [oauth 2,0 belirtiminin 4,1 bölümünde](https://tools.ietf.org/html/rfc6749)açıklanmaktadır. [Tek sayfalı uygulamalar](v2-app-types.md#single-page-apps-javascript), [Web uygulamaları](v2-app-types.md#web-apps)ve [yerel olarak yüklenen uygulamalar](v2-app-types.md#mobile-and-native-apps)dahil olmak üzere uygulama türlerinin çoğunda kimlik doğrulama ve yetkilendirme gerçekleştirmek için kullanılır. Flow, uygulamaların Microsoft Identity platformu tarafından güvenli hale getirilmiş kaynaklara erişmek için kullanılabilen access_tokens güvenli bir şekilde almasına olanak sağlar ve ek access_tokens almak için belirteçleri yenileme ve oturum açan kullanıcı için KIMLIK belirteçleri.

## <a name="protocol-diagram"></a>Protokol diyagramı

Yüksek düzeyde, bir uygulamanın tüm kimlik doğrulama akışı şuna benzer bir bit:

![OAuth kimlik doğrulama kod akışı](./media/v2-oauth2-auth-code-flow/convergence-scenarios-native.svg)

## <a name="redirect-uri-setup-required-for-single-page-apps"></a>Tek sayfalı uygulamalar için yeniden yönlendirme URI kurulumu gerekli

Tek sayfalı uygulamalar için yetkilendirme kodu akışı, bazı ek kurulum gerektirir.  Yeniden yönlendirme URI 'nizi CORS için etkin olarak işaretlemek üzere [tek sayfalı uygulamanızı oluşturmaya](scenario-spa-app-registration.md#redirect-uri-msaljs-20-with-auth-code-flow) yönelik yönergeleri izleyin. CORS 'yi etkinleştirmek üzere var olan bir yeniden yönlendirme URI 'sini güncelleştirmek için, bildirim düzenleyicisini açın ve `type` yeniden YÖNLENDIRME URI 'nizin alanını `spa` bölümünde olarak ayarlayın `replyUrlsWithType` . Ayrıca, kimlik doğrulama sekmesinin "Web" bölümünde yeniden yönlendirme URI 'sine tıklayıp, yetkilendirme kodu akışını kullanarak geçirmek istediğiniz URI 'Leri seçebilirsiniz.

`spa`Yeniden yönlendirme türü, örtük akışla geriye dönük olarak uyumludur. Belirteçleri almak için örtük akışı kullanan uygulamalar, `spa` yeniden YÖNLENDIRME URI 'sine sorun olmadan geçebilir ve dolaylı akışı kullanmaya devam edebilir.

Yetkilendirme kodu akışını kullanmaya çalışırsanız ve şu hatayı görebilirsiniz:

`access to XMLHttpRequest at 'https://login.microsoftonline.com/common/v2.0/oauth2/token' from origin 'yourApp.com' has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.`

Ardından, uygulama kaydınızı ziyaret edin ve uygulamanız için yeniden yönlendirme URI 'sini yazın `spa` .

## <a name="request-an-authorization-code"></a>Yetkilendirme kodu iste

Yetkilendirme kodu akışı, kullanıcıyı uç noktaya yönlendiren istemciyle başlar `/authorize` . Bu istekte istemci,, `openid` `offline_access` ve `https://graph.microsoft.com/mail.read ` izinlerini kullanıcıdan ister.  Bazı izinler yönetici kısıtlanıyor, örneğin kullanarak bir kuruluşun dizinine veri yazma `Directory.ReadWrite.All` . Uygulamanız bir kuruluş kullanıcısının bu izinlerinden birine erişim isterse, kullanıcı uygulamanızın izinlerini kabul etmek için yetkilendirilmediğini bildiren bir hata iletisi alır. Yönetici kısıtlı kapsamlara erişim istemek için, bunları doğrudan bir genel yöneticiden istemeniz gerekir.  Daha fazla bilgi için [Yönetici kısıtlı izinleri](v2-permissions-and-consent.md#admin-restricted-permissions)okuyun.

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read%20api%3A%2F%2F
&state=12345
&code_challenge=YTFjNjI1OWYzMzA3MTI4ZDY2Njg5M2RkNmVjNDE5YmEyZGRhOGYyM2IzNjdmZWFhMTQ1ODg3NDcxY2Nl
&code_challenge_method=S256
```

> [!TIP]
> Bu isteği yürütmek için aşağıdaki bağlantıya tıklayın! Oturum açtıktan sonra, tarayıcınız `https://localhost/myapp/` Adres çubuğunda bir ile yeniden yönlendirilmelidir `code` .
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=query&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&state=12345" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

| Parametre    | Gerekli/isteğe bağlı | Description |
|--------------|-------------|--------------|
| `tenant`    | gerekli    | `{tenant}`İsteğin yolundaki değeri, uygulamada kimlerin oturum açmasını denetlemek için kullanılabilir. İzin verilen değerler, `common` , `organizations` `consumers` ve kiracı tanımlayıcılarıdır. Daha fazla ayrıntı için bkz. [protokol temelleri](active-directory-v2-protocols.md#endpoints).  |
| `client_id`   | gerekli    | [Azure Portal – uygulama kayıtları](https://go.microsoft.com/fwlink/?linkid=2083908) deneyiminin uygulamanıza atandığı **uygulama (istemci) kimliği** .  |
| `response_type` | gerekli    | `code`, Yetkilendirme kodu akışı için içermelidir. `id_token` `token` [Karma Flow](#request-an-id-token-as-well-hybrid-flow)da dahil olabilir. |
| `redirect_uri`  | gerekli | Uygulamanızın, kimlik doğrulama yanıtlarının sizin uygulamanız tarafından gönderilebileceği ve alınabileceği redirect_uri. Portalın, URL kodlamalı olması dışında, portalda kaydettiğiniz redirect_uris biriyle tam olarak eşleşmesi gerekir. Yerel & Mobile Apps için önerilen değerlerden birini  `https://login.microsoftonline.com/common/oauth2/nativeclient` (katıştırılmış tarayıcıları kullanan uygulamalar için) veya `http://localhost` (sistem tarayıcılarını kullanan uygulamalar için) kullanmanız gerekir. |
| `scope`  | gerekli    | Kullanıcının onay vermesini istediğiniz [kapsamların](v2-permissions-and-consent.md) , boşlukla ayrılmış bir listesi.  `/authorize`İsteğin bacağı, bu birden fazla kaynağı kapsayabilir ve uygulamanızın çağırmak istediğiniz birden çok Web API 'si için onay almasını sağlar. |
| `response_mode`   | Önerilen | Elde edilen belirteci uygulamanıza geri göndermek için kullanılması gereken yöntemi belirtir. Aşağıdakilerden biri olabilir:<br/><br/>- `query`<br/>- `fragment`<br/>- `form_post`<br/><br/>`query` kodu, yeniden yönlendirme URI 'niz üzerinde bir sorgu dizesi parametresi olarak sağlar. Örtük akışı kullanarak bir KIMLIK belirteci isteğinde bulunduğsanız, `query` [OpenID belirtiminde](https://openid.net/specs/oauth-v2-multiple-response-types-1_0.html#Combinations)belirtilen şekilde kullanamazsınız. Yalnızca kod isteğinde bulunduğsanız, `query` `fragment` veya kullanabilirsiniz `form_post` . `form_post` yeniden yönlendirme URI 'nize kod içeren bir GÖNDERI yürütür. |
| `state`                 | Önerilen | İsteğin belirteç yanıtında de döndürülecek bir değer. Bu, istediğiniz herhangi bir içerik dizesi olabilir. Rastgele oluşturulan benzersiz bir değer genellikle [siteler arası istek sahteciliği saldırılarını önlemek](https://tools.ietf.org/html/rfc6749#section-10.12)için kullanılır. Bu değer Ayrıca, kullanıcının uygulamadaki durumuyla ilgili bilgileri, kimlik doğrulama isteği gerçekleştirilmeden önce (örneğin, bulunan sayfa veya Görünüm) kodlayabilir. |
| `prompt`  | isteğe bağlı    | Gerekli kullanıcı etkileşiminin türünü gösterir. Şu anda yalnızca geçerli değerler,, ve ' dir `login` `none` `consent` `select_account` .<br/><br/>- `prompt=login` , kullanıcıyı bu istek üzerine kimlik bilgilerini girmeye zorlar ve çoklu oturum açma 'yı yok eder.<br/>- `prompt=none` Bunun tersi, kullanıcının herhangi bir etkileşimli istem ile sunulmayacağını garanti eder. İstek, tek oturum açma yoluyla sessizce tamamlanamayacak, Microsoft Identity platform bir `interaction_required` hata döndürür.<br/>- `prompt=consent` Kullanıcı oturum açtıktan sonra OAuth onay iletişim kutusunu tetikler, böylece kullanıcıdan uygulamaya izin vermesini istenir.<br/>- `prompt=select_account` , oturum veya herhangi bir anımsanan hesap ya da başka bir hesap kullanmayı tamamen seçmek için bir seçenek veya herhangi bir hatırlanan hesap seçim deneyimi sunan çoklu oturum açmayı kesintiye uğratacaktır.<br/> |
| `login_hint`  | isteğe bağlı    | Kullanıcı adının bir süre önce bilinerek Kullanıcı için oturum açma sayfasının Kullanıcı adı/e-posta adresi alanını önceden doldurmanız için kullanılabilir. Genellikle uygulamalar bu parametreyi yeniden kimlik doğrulama sırasında kullanır ve Kullanıcı adını, talebi kullanarak önceki bir oturum açma işleminden zaten ayıklamış olur `preferred_username` .   |
| `domain_hint`  | isteğe bağlı    | Dahil edilmesi durumunda, kullanıcının oturum açma sayfasında yer aldığı e-posta tabanlı bulma işlemini atlar; Örneğin, bunları federe kimlik sağlayıcısına gönderebilirsiniz. Genellikle uygulamalar, `tid` önceki bir oturum açma işleminden çıkartarak bu parametreyi yeniden kimlik doğrulama sırasında kullanacaktır. `tid`Talep değeri ise `9188040d-6c67-4c5b-b112-36a304b66dad` , kullanmanız gerekir `domain_hint=consumers` . Aksi takdirde, kullanın `domain_hint=organizations` .  |
| `code_challenge`  | Önerilen/gerekli | Kod değişimi (PKCE) için kanıt anahtarı aracılığıyla yetkilendirme kodu yetkisini güvenli hale getirmek için kullanılır. Dahil ise gereklidir `code_challenge_method` . Daha fazla bilgi için bkz. [Pkce RFC](https://tools.ietf.org/html/rfc7636). Bu, tüm uygulama türleri için hem genel hem de gizli istemciler için önerilir ve [yetkilendirme kodu akışını kullanan tek sayfalı uygulamalar](reference-third-party-cookies-spas.md)için Microsoft Identity platformu için gereklidir. |
| `code_challenge_method` | Önerilen/gerekli | Parametresi için öğesini kodlamak için kullanılan yöntem `code_verifier` `code_challenge` . Bu  olmalıdır `S256` , ancak `plain` BIR nedenden dolayı istemcinin SHA256 destekleyememelidir. <br/><br/>Dışlanmazsa, varsa `code_challenge` düz metin olarak kabul edilir `code_challenge` . Microsoft Identity platformu hem hem de `plain` destekler `S256` . Daha fazla bilgi için bkz. [Pkce RFC](https://tools.ietf.org/html/rfc7636). Bu [, yetkilendirme kodu akışını kullanan tek sayfalı uygulamalar](reference-third-party-cookies-spas.md)için gereklidir.|


Bu noktada, kullanıcıdan kimlik bilgilerini girmesi ve kimlik doğrulamasını tamamlaması istenir. Microsoft Identity platformu, kullanıcının sorgu parametresinde belirtilen izinlere de sahip olduğundan emin olur `scope` . Kullanıcı bu izinlerden herhangi birine onay vermediyseniz, kullanıcıdan gerekli izinleri onaylaması istenir. [İzinlerin, izin ve çok kiracılı uygulamaların ayrıntıları burada verilmiştir](v2-permissions-and-consent.md).

Kullanıcı kimlik doğrulamasından ve izin verdiğinde, Microsoft Identity platformu, `redirect_uri` parametresinde belirtilen yöntemi kullanarak, belirtilen şekilde uygulamanıza bir yanıt döndürür `response_mode` .

#### <a name="successful-response"></a>Başarılı yanıt

Kullanarak başarılı bir yanıt `response_mode=query` şöyle görünür:

```HTTP
GET http://localhost?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=12345
```

| Parametre | Açıklama  |
|-----------|--------------|
| `code` | Uygulamanın istediği authorization_code. Uygulama, hedef kaynak için bir erişim belirteci istemek üzere yetkilendirme kodunu kullanabilir. Authorization_codes kısa süreli, genellikle yaklaşık 10 dakika sonra süresi dolacak. |
| `state` | İsteğe bir durum parametresi dahil edilir, yanıtta aynı değer görünmelidir. Uygulama, istek ve yanıtta durum değerlerinin özdeş olduğunu doğrulamalıdır. |

Ayrıca, istek yaptıysanız bir KIMLIK belirteci alabilir ve uygulama kaydnda örtülü izni etkinleştirebilirsiniz.  Bu bazen ["hibrit Flow"](#request-an-id-token-as-well-hybrid-flow)olarak adlandırılır ve ASP.NET gibi çerçeveler tarafından kullanılır.

#### <a name="error-response"></a>Hata yanıtı

Ayrıca, `redirect_uri` uygulamanın bunları uygun şekilde işleyebilmesi için hata yanıtları da gönderilebilir.

```HTTP
GET http://localhost?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parametre | Açıklama  |
|----------|------------------|
| `error`  | Oluşan hata türlerini sınıflandırmak için kullanılabilen ve hatalara yanıt vermek için kullanılabilen bir hata kodu dizesi. |
| `error_description` | Bir geliştiricinin kimlik doğrulama hatasının kök nedenini belirlemesine yardımcı olabilecek belirli bir hata iletisi. |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Yetkilendirme uç noktası hataları için hata kodları

Aşağıdaki tabloda, hata yanıtının parametresinde döndürülebilecek çeşitli hata kodları açıklanmaktadır `error` .

| Hata Kodu  | Description    | İstemci eylemi   |
|-------------|----------------|-----------------|
| `invalid_request` | Eksik gerekli bir parametre gibi protokol hatası. | İsteği onarın ve yeniden gönderin. Bu, genellikle ilk sınama sırasında yakalanan bir geliştirme hatasıdır. |
| `unauthorized_client` | İstemci uygulamasının bir yetkilendirme kodu istemesine izin verilmez. | Bu hata genellikle istemci uygulaması Azure AD 'de kayıtlı olmadığında veya kullanıcının Azure AD kiracısına eklenmediğinde oluşur. Uygulama kullanıcıya uygulamayı yükleme ve Azure AD 'ye ekleme yönergesini isteyebilir. |
| `access_denied`  | Kaynak sahibi reddedildi onayı  | İstemci uygulaması, kullanıcıya Kullanıcı tarafından bağlanmadığı takdirde devam edemediği konusunda bildirimde bulunabilir. |
| `unsupported_response_type` | Yetkilendirme sunucusu istekteki yanıt türünü desteklemiyor. | İsteği onarın ve yeniden gönderin. Bu, genellikle ilk sınama sırasında yakalanan bir geliştirme hatasıdır. [Karma akışta](#request-an-id-token-as-well-hybrid-flow)görüldüğünde, istemci uygulama kaydında kimlik belirteci örtük izin verme ayarını etkinleştirmeniz gerektiğini bildirir. |
| `server_error`  | Sunucu beklenmeyen bir hatayla karşılaştı.| İsteği yeniden deneyin. Bu hatalar geçici koşullardan kaynaklanabilir. İstemci uygulaması, yanıtı geçici bir hataya geciktiğinde kullanıcıya açıklayabilir. |
| `temporarily_unavailable`   | Sunucu, isteği işlemek için geçici olarak çok meşgul. | İsteği yeniden deneyin. İstemci uygulaması, geçici bir durum nedeniyle yanıtı geciktirildiği kullanıcıya açıklanmayabilir. |
| `invalid_resource`  | Hedef kaynak geçersiz, çünkü mevcut değil, Azure AD bu dosyayı bulamıyor veya doğru şekilde yapılandırılmamış. | Bu hata, varsa kaynağın kiracıda yapılandırılmamış olduğunu gösterir. Uygulama kullanıcıya uygulamayı yükleme ve Azure AD 'ye ekleme yönergesini isteyebilir. |
| `login_required` | Çok fazla veya hiç Kullanıcı bulunamadı | İstemci sessiz kimlik doğrulaması ( `prompt=none` ) istedi, ancak tek bir Kullanıcı bulunamadı. Bu, oturumda etkin olan veya Kullanıcı olmayan birden çok kullanıcı olduğu anlamına gelebilir. Bu, kiracının seçtiği hesabı alır (örneğin, iki Azure AD hesabı etkin ve bir Microsoft hesabı ve `consumers` seçilirse, sessiz kimlik doğrulama çalışır). |
| `interaction_required` | İstek, Kullanıcı etkileşimi gerektirir. | Ek bir kimlik doğrulama adımı veya onayı gerekir. İsteği olmadan yeniden deneyin `prompt=none` . |

### <a name="request-an-id-token-as-well-hybrid-flow"></a>KIMLIK belirteci iste (karma akış)

Kullanıcının bir yetkilendirme kodunu benimsemeden önce ne olduğunu öğrenmek için, uygulamaların yetkilendirme kodu istediklerinde bir KIMLIK belirteci istemesi yaygındır. Bu, *karma akış* olarak adlandırılır çünkü bu, yetkilendirme kodu akışına örtük izni karıştırmaktadır. Hibrit Flow genellikle, bir kullanıcı için bir sayfayı kod satın alma, özellikle de [ASP.net](quickstart-v2-aspnet-core-webapp.md)üzerinde engellenmeksizin işlemek isteyen Web uygulamalarında kullanılır. Hem tek sayfalı uygulamalar hem de geleneksel Web Apps, bu modeldeki gecikme süresinden daha fazla avantaj elde ediyor.

Karma akış, daha önce açıklanan yetkilendirme kodu akışıyla aynıdır, ancak bunların tümü bir KIMLIK belirteci istemek için gereklidir: yeni kapsamlar, yeni bir response_type ve yeni bir `nonce` sorgu parametresi.

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code%20id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=fragment
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fuser.read
&state=12345
&nonce=abcde
&code_challenge=YTFjNjI1OWYzMzA3MTI4ZDY2Njg5M2RkNmVjNDE5YmEyZGRhOGYyM2IzNjdmZWFhMTQ1ODg3NDcxY2Nl
&code_challenge_method=S256
```

| Güncelleştirilmiş parametre | Gerekli/isteğe bağlı | Açıklama |
|---------------|-------------|--------------|
|`response_type`| Gerekli | Eklenmesi, `id_token` uygulamanın uç noktadan yanıtta BIR kimlik belirteci benzediğini sunucuya gösterir `/authorize` .  |
|`scope`| Gerekli | KIMLIK belirteçleri için, KIMLIK belirteci kapsamlarını ( `openid` ve isteğe bağlı olarak ve) içerecek şekilde güncelleştirilmeleri `profile` gerekir `email` . |
|`nonce`| Gerekli|     Uygulama tarafından oluşturulan, sonuçta elde edilen id_token talep olarak dahil edilecek bir değer. Daha sonra uygulama, belirteç yeniden yürütme saldırılarını azaltmak için bu değeri doğrulayabilirler. Değer genellikle, isteğin kaynağını belirlemek için kullanılabilecek rastgele, benzersiz bir dizedir. |
|`response_mode`| Önerilen | Elde edilen belirteci uygulamanıza geri göndermek için kullanılması gereken yöntemi belirtir. `query`Yalnızca bir yetkilendirme kodu için varsayılandır, ancak `fragment` istek bir id_token içeriyorsa `response_type` .  Ancak, `form_post` özellikle `http:/localhost` yeniden yönlendirme URI 'si olarak kullanılırken uygulamaların kullanılması önerilir. |

`fragment`Yanıt modu olarak kullanılması, tarayıcıların parçayı Web sunucusuna geçirmemesi nedeniyle yeniden yönlendirmenin kodunu okuyan Web uygulamaları için sorunlara neden olur.  Bu durumlarda, uygulamalar `form_post` tüm verilerin sunucuya gönderildiğinden emin olmak için yanıt modunu kullanmalıdır. 

#### <a name="successful-response"></a>Başarılı yanıt

Kullanarak başarılı bir yanıt `response_mode=fragment` şöyle görünür:

```HTTP
GET https://login.microsoftonline.com/common/oauth2/nativeclient#
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&id_token=eYj...
&state=12345
```

| Parametre | Açıklama  |
|-----------|--------------|
| `code` | Uygulamanın istediği yetkilendirme kodu. Uygulama, hedef kaynak için bir erişim belirteci istemek üzere yetkilendirme kodunu kullanabilir. Yetkilendirme kodları kısa süreli olduğundan genellikle yaklaşık 10 dakika sonra dolar. |
| `id_token` | *Örtük izin* aracılığıyla verilen Kullanıcı IÇIN bir kimlik belirteci. `c_hash`Aynı istekte öğesinin karması olan özel bir talep içerir `code` . |
| `state` | İsteğe bir durum parametresi dahil edilir, yanıtta aynı değer görünmelidir. Uygulama, istek ve yanıtta durum değerlerinin özdeş olduğunu doğrulamalıdır. |

## <a name="request-an-access-token"></a>Erişim belirteci isteme

Artık bir authorization_code edindiniz ve Kullanıcı tarafından izin verildiğinden, `code` için öğesini `access_token` istenen kaynak için kullanabilirsiniz. Uç noktaya bir istek göndererek bunu yapın `POST` `/token` :

```HTTP
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=authorization_code
&code_verifier=ThisIsntRandomButItNeedsToBe43CharactersLong 
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps. This secret needs to be URL-Encoded.
```

> [!TIP]
> Bu isteği Postman 'da yürütmeyi deneyin! (Değiştirmeyi unutmayın `code` ) [ ![ Bu Isteği Postman 'da çalıştırmayı deneyin](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

| Parametre  | Gerekli/isteğe bağlı | Description     |
|------------|-------------------|----------------|
| `tenant`   | gerekli   | `{tenant}`İsteğin yolundaki değeri, uygulamada kimlerin oturum açmasını denetlemek için kullanılabilir. İzin verilen değerler, `common` , `organizations` `consumers` ve kiracı tanımlayıcılarıdır. Daha fazla ayrıntı için bkz. [protokol temelleri](active-directory-v2-protocols.md#endpoints).  |
| `client_id` | gerekli  | [Azure Portal – uygulama kayıtları](https://go.microsoft.com/fwlink/?linkid=2083908) sayfasının uygulamanıza atandığı uygulama (ISTEMCI) kimliği. |
| `grant_type` | gerekli   | `authorization_code`, Yetkilendirme kodu akışı için olmalıdır.   |
| `scope`      | isteğe bağlı   | Kapsamların boşlukla ayrılmış listesi. Kapsamların tümü tek bir kaynaktan olmalıdır ve OıDC kapsamları ( `profile` , `openid` ,) ile birlikte `email` . Kapsamların daha ayrıntılı bir açıklaması için [izinler, onay ve kapsamlar](v2-permissions-and-consent.md)' a bakın. Bu, yetkilendirme kodu akışına yönelik bir Microsoft uzantısıdır. Bu, uygulamaların belirteç satın alma sırasında belirtecin istedikleri kaynağı bildirmesine izin vermeyi amaçlar.|
| `code`          | gerekli  | Akışın ilk Bata elde ettiğiniz authorization_code. |
| `redirect_uri`  | gerekli  | Authorization_code elde etmek için kullanılan aynı redirect_uri değeri. |
| `client_secret` | gizli Web uygulamaları için gerekli | Uygulamanız için uygulama kayıt portalında oluşturduğunuz uygulama gizli anahtarı. Client_secrets cihazlarda veya Web sayfalarında güvenilir bir şekilde depolanamadığından, uygulama gizli anahtarını yerel bir uygulamada veya tek sayfalı uygulamada kullanmamanız gerekir. Client_secret sunucu tarafında güvenli bir şekilde depolayabilme özelliğine sahip Web uygulamaları ve Web API 'Leri için gereklidir.  Burada açıklanan tüm parametreler gibi, istemci sırrı gönderilmeden önce, genellikle SDK tarafından gerçekleştirilen bir adım olmak üzere URL kodlu olmalıdır. URI kodlaması hakkında daha fazla bilgi için bkz. [URI genel sözdizimi belirtimi](https://tools.ietf.org/html/rfc3986#page-12). |
| `code_verifier` | Önerilen  | Authorization_code elde etmek için kullanılan aynı code_verifier. Yetkilendirme kodu verme isteğinde PKCE kullanılmışsa gereklidir. Daha fazla bilgi için bkz. [Pkce RFC](https://tools.ietf.org/html/rfc7636). |

### <a name="successful-response"></a>Başarılı yanıt

Başarılı bir belirteç yanıtı şöyle görünür:

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```

| Parametre     | Açıklama   |
|---------------|------------------------------|
| `access_token`  | İstenen erişim belirteci. Uygulama, bir Web API 'SI gibi güvenli kaynak üzerinde kimlik doğrulaması yapmak için bu belirteci kullanabilir.  |
| `token_type`    | Belirteç türü değerini gösterir. Azure AD 'nin desteklediği tek tür taşıyıcı |
| `expires_in`    | Erişim belirtecinin geçerli olduğu süre (saniye cinsinden). |
| `scope`         | Access_token için geçerli olan kapsamlar. İsteğe bağlı-bu standart değildir ve atlanırsa belirteç akışın ilk bacağı tarafında istenen kapsamlar için olur. |
| `refresh_token` | Bir OAuth 2,0 yenileme belirteci. Uygulama bu belirteci kullanabilir ve geçerli erişim belirtecinin süresi dolduktan sonra ek erişim belirteçleri elde edebilir. Refresh_tokens uzun ömürlü ve uzun süre boyunca kaynaklara erişimi sürdürmek için kullanılabilir. Erişim belirtecini yenileme hakkında daha fazla ayrıntı için [aşağıdaki bölüme](#refresh-the-access-token)bakın. <br> **Note:** Yalnızca `offline_access` kapsam isteniyorsa sağlandı. |
| `id_token`      | Bir JSON Web Token (JWT). Uygulama, oturum açan kullanıcı hakkında bilgi istemek için bu belirtecin segmentlerinin kodunu çözebilir. Uygulama değerleri önbelleğe alabilir ve görüntüleyebilir ve gizli istemciler bunu yetkilendirme için kullanabilir. İd_tokens hakkında daha fazla bilgi için bkz [`id_token reference`](id-tokens.md) .. <br> **Note:** Yalnızca `openid` kapsam isteniyorsa sağlandı. |

### <a name="error-response"></a>Hata yanıtı

Hata yanıtları şöyle görünür:

```json
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| Parametre         | Açıklama    |
|-------------------|----------------|
| `error`       | Oluşan hata türlerini sınıflandırmak için kullanılabilen ve hatalara yanıt vermek için kullanılabilen bir hata kodu dizesi. |
| `error_description` | Bir geliştiricinin kimlik doğrulama hatasının kök nedenini belirlemesine yardımcı olabilecek belirli bir hata iletisi. |
| `error_codes` | Tanılamada yardımcı olabilecek STS 'ye özgü hata kodlarının bir listesi.  |
| `timestamp`   | Hatanın oluştuğu zaman. |
| `trace_id`    | Tanılamada yardımcı olabilecek istek için benzersiz bir tanımlayıcı. |
| `correlation_id` | İsteğe ait, bileşenler genelinde tanılamada yardımcı olabilecek benzersiz bir tanımlayıcı. |

### <a name="error-codes-for-token-endpoint-errors"></a>Belirteç uç noktası hataları için hata kodları

| Hata Kodu         | Description        | İstemci eylemi    |
|--------------------|--------------------|------------------|
| `invalid_request`  | Eksik gerekli bir parametre gibi protokol hatası. | İstek veya uygulama kaydını düzeltemedi ve isteği yeniden gönderin   |
| `invalid_grant`    | Yetkilendirme kodu veya PKCE kod doğrulayıcısı geçersiz veya süresi doldu. | Uç noktaya yeni bir istek deneyin `/authorize` ve code_verifier parametresinin doğru olduğunu doğrulayın.  |
| `unauthorized_client` | Kimliği doğrulanmış istemci, bu yetkilendirme verme türünü kullanma yetkisine sahip değil. | Bu durum genellikle istemci uygulaması Azure AD 'de kayıtlı olmadığında veya kullanıcının Azure AD kiracısına eklenmediğinde oluşur. Uygulama kullanıcıya uygulamayı yükleme ve Azure AD 'ye ekleme yönergesini isteyebilir. |
| `invalid_client` | İstemci kimlik doğrulaması başarısız oldu.  | İstemci kimlik bilgileri geçerli değil. Bu uygulamayı onarmak için, uygulama Yöneticisi kimlik bilgilerini güncelleştirir.   |
| `unsupported_grant_type` | Yetkilendirme sunucusu yetkilendirme verme türünü desteklemiyor. | İstekteki izin türünü değiştirin. Bu tür bir hata yalnızca geliştirme sırasında ve ilk test sırasında algılanarak gerçekleştirilmelidir. |
| `invalid_resource` | Hedef kaynak geçersiz, çünkü mevcut değil, Azure AD bu dosyayı bulamıyor veya doğru şekilde yapılandırılmamış. | Bu, varsa kaynağın kiracıda yapılandırılmamış olduğunu gösterir. Uygulama kullanıcıya uygulamayı yükleme ve Azure AD 'ye ekleme yönergesini isteyebilir.  |
| `interaction_required` | Standart olmayan, OıDC belirtimi yalnızca Endpoint üzerinde bunu çağırır `/authorize` . İstek, Kullanıcı etkileşimi gerektirir. Örneğin, ek bir kimlik doğrulama adımı gereklidir. | `/authorize`Aynı kapsamlarla isteği yeniden deneyin. |
| `temporarily_unavailable` | Sunucu, isteği işlemek için geçici olarak çok meşgul. | İsteği küçük bir gecikmeden sonra yeniden deneyin. İstemci uygulaması, geçici bir durum nedeniyle yanıtı geciktirildiği kullanıcıya açıklanmayabilir. |
|`consent_required` | İstek için Kullanıcı izni gerekiyor. Bu hata standart değildir, çünkü genellikle `/authorize` her OıDC belirtimleri için uç noktada döndürülür. `scope`Kod satın alma akışında, istemci uygulamanın istek için izni olmayan bir parametre kullanıldığında döndürülür.  | İstemcinin `/authorize` onay tetiklenmesi için kullanıcıyı doğru kapsam ile uç noktaya geri gönderebilmesi gerekir. |
|`invalid_scope` | Uygulama tarafından istenen kapsam geçersiz.  | Kimlik doğrulama isteğindeki kapsam parametresinin değerini geçerli bir değere güncelleştirin. |

> [!NOTE]
> Tek sayfalı uygulamalar `invalid_request` , yalnızca ' tek sayfalı uygulamanın ' istemci türü için, çıkış noktaları arası belirteç kullanımına izin verildiğini belirten bir hata alabilir.  Bu, belirteci istemek için kullanılan yeniden yönlendirme URI 'sinin `spa` yeniden yönlendirme URI 'si olarak işaretlenmediğini belirtir.  Bu akışın nasıl etkinleştirileceği hakkında [uygulama kayıt adımlarını](#redirect-uri-setup-required-for-single-page-apps) gözden geçirin.

## <a name="use-the-access-token"></a>Erişim belirtecini kullanma

Artık başarıyla edindiniz `access_token` , bu API 'yi üstbilgiye dahil ederek Web API 'lerine yönelik isteklerde kullanabilirsiniz `Authorization` :

> [!TIP]
> Bu isteği Postman 'da yürütün! ( `Authorization` İlk üstbilgiyi değiştir) [ ![ Bu isteği Postman 'da çalıştırmayı deneyin](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```HTTP
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-access-token"></a>Erişim belirtecini Yenile

Access_tokens kısa ömürlü ve kaynaklara erişmeye devam etmek için süreleri dolduktan sonra yenilemeniz gerekir. Bunu, uç noktasına başka bir istek göndererek yapabilirsiniz `POST` `/token` `refresh_token` `code` .  Yenileme belirteçleri, istemcinizin zaten onay aldığı tüm izinler için geçerlidir. bu nedenle, için bir istekte verilen yenileme belirteci `scope=mail.read` için yeni bir erişim belirteci istemek üzere kullanılabilir `scope=api://contoso.com/api/UseResource` .

Web uygulamaları ve yerel uygulamalar için yenileme belirteçleri, belirtilen ömürleri içermez. Genellikle, yenileme belirteçlerinin yaşam süreleri nispeten uzundur. Ancak, bazı durumlarda belirteçleri yenileme süre sonu, iptal etme veya istenen eylem için yeterli ayrıcalıklara sahip değil. Uygulamanızın [belirteç verme uç noktası tarafından döndürülen hataları](#error-codes-for-token-endpoint-errors) beklemesi ve işlemesi gerekir. Ancak tek sayfalı uygulamalar, 24 saatlik bir yaşam süresine sahip ve her gün yeni bir kimlik doğrulaması gerektiren bir belirteç alır.  Bu, 3. taraf tanımlama bilgileri etkinleştirildiğinde bir IFRAME içinde sessizce yapılabilir, ancak Safari gibi 3. taraf tanımlama bilgileri olmadan tarayıcılarda bir üst düzey çerçevede (tam sayfa gezintisi veya bir açılan pencere) yapılması gerekir.

Yeni erişim belirteçleri elde etmek için kullanıldığında yenileme belirteçleri iptal edilmese de, eski yenileme belirtecini atmayı bekliyorcaksınız. [OAuth 2,0 belirtimi](https://tools.ietf.org/html/rfc6749#section-6) şöyle diyor: "yetkilendirme sunucusu yeni bir yenileme belirteci verebilir, bu durumda istemcinin eski yenileme belirtecini atılması ve yeni yenileme belirteci Ile değiştirmeniz gerekir. Yetkilendirme sunucusu, istemciye yeni bir yenileme belirteci gönderdikten sonra eski yenileme belirtecini iptal edebılır. "

>[!IMPORTANT]
> Olarak kaydedilmiş bir yeniden yönlendirme URI 'sine gönderilen yenileme belirteçleri için `spa` yenileme belirtecinin süresi 24 saat sonra dolacak. İlk yenileme belirteci kullanılarak elde edilen ek yenileme belirteçleri, bu süre sonu zamanından sonra, her 24 saatte bir yeni yenileme belirteci almak için bir etkileşimli kimlik doğrulaması kullanarak yetkilendirme kodu akışını yeniden çalıştırmaya hazırlanmalıdır. Kullanıcılar, kimlik bilgilerini girmek zorunda kalmaz ve genellikle bir UX görmeyecektir, ancak uygulamanızın yeniden yüklenmesi, ancak tarayıcının oturum açma oturumunu görmek için üst düzey çerçevede oturum açma sayfasını ziyaret etmelidir.  Bunun nedeni, [tarayıcılarda üçüncü taraf tanımlama bilgilerini engelleyen gizlilik özellikleridir](reference-third-party-cookies-spas.md).

```HTTP

// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&grant_type=refresh_token
&client_secret=JqQX2PNo9bpM0uEihUPzyrh      // NOTE: Only required for web apps. This secret needs to be URL-Encoded
```

> [!TIP]
> Bu isteği Postman 'da yürütmeyi deneyin! (Değiştirmeyi unutmayın `refresh_token` ) [ ![ Bu Isteği Postman 'da çalıştırmayı deneyin](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)
>

| Parametre     | Tür           | Description        |
|---------------|----------------|--------------------|
| `tenant`        | gerekli     | `{tenant}`İsteğin yolundaki değeri, uygulamada kimlerin oturum açmasını denetlemek için kullanılabilir. İzin verilen değerler, `common` , `organizations` `consumers` ve kiracı tanımlayıcılarıdır. Daha fazla ayrıntı için bkz. [protokol temelleri](active-directory-v2-protocols.md#endpoints).   |
| `client_id`     | gerekli    | [Azure Portal – uygulama kayıtları](https://go.microsoft.com/fwlink/?linkid=2083908) deneyiminin uygulamanıza atandığı **uygulama (istemci) kimliği** . |
| `grant_type`    | gerekli    | `refresh_token`Yetkilendirme kodu akışının bu bacağı için olmalıdır. |
| `scope`         | gerekli    | Kapsamların boşlukla ayrılmış listesi. Bu Bata istenen kapsamlar, özgün authorization_code isteği balarında istenen kapsamların bir alt kümesiyle eşit olmalıdır. Bu istekte belirtilen kapsamlar birden çok kaynak sunucusuna yayılsa, Microsoft Identity platformu ilk kapsamda belirtilen kaynak için bir belirteç döndürür. Kapsamların daha ayrıntılı bir açıklaması için [izinler, onay ve kapsamlar](v2-permissions-and-consent.md)' a bakın. |
| `refresh_token` | gerekli    | Akışın ikinci Bada elde ettiğiniz refresh_token. |
| `client_secret` | Web uygulamaları için gerekli | Uygulamanız için uygulama kayıt portalında oluşturduğunuz uygulama gizli anahtarı. Client_secrets, cihazlarda güvenilir bir şekilde depolanamadığı için yerel bir uygulamada kullanılmamalıdır. Client_secret sunucu tarafında güvenli bir şekilde depolayabilme özelliğine sahip Web uygulamaları ve Web API 'Leri için gereklidir. Bu parolanın URL kodlamalı olması gerekir. Daha fazla bilgi için bkz. [URI genel sözdizimi belirtimi](https://tools.ietf.org/html/rfc3986#page-12). |

#### <a name="successful-response"></a>Başarılı yanıt

Başarılı bir belirteç yanıtı şöyle görünür:

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```

| Parametre     | Açıklama         |
|---------------|-------------------------------------------------------------|
| `access_token`  | İstenen erişim belirteci. Uygulama, bir Web API 'SI gibi güvenli kaynak üzerinde kimlik doğrulaması yapmak için bu belirteci kullanabilir. |
| `token_type`    | Belirteç türü değerini gösterir. Azure AD 'nin desteklediği tek tür taşıyıcı |
| `expires_in`    | Erişim belirtecinin geçerli olduğu süre (saniye cinsinden).   |
| `scope`         | Access_token için geçerli olan kapsamlar.    |
| `refresh_token` | Yeni bir OAuth 2,0 yenileme belirteci. Yenileme belirteçlerinizin mümkün olduğunca geçerli kalmasını sağlamak için eski yenileme belirtecini bu yeni alınan yenileme belirteciyle değiştirmelisiniz. <br> **Note:** Yalnızca `offline_access` kapsam isteniyorsa sağlandı.|
| `id_token`      | İşaretsiz bir JSON Web Token (JWT). Uygulama, oturum açan kullanıcı hakkında bilgi istemek için bu belirtecin segmentlerinin kodunu çözebilir. Uygulama değerleri önbelleğe alabilir ve görüntüleyebilir, ancak herhangi bir yetkilendirme veya güvenlik sınırları için bu değere dayanmamalıdır. İd_tokens hakkında daha fazla bilgi için bkz [`id_token reference`](id-tokens.md) .. <br> **Note:** Yalnızca `openid` kapsam isteniyorsa sağlandı. |

#### <a name="error-response"></a>Hata yanıtı

```json
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| Parametre         | Açıklama                                                                                        |
|-------------------|----------------------------------------------------------------------------------------------------|
| `error`           | Oluşan hata türlerini sınıflandırmak için kullanılabilen ve hatalara yanıt vermek için kullanılabilen bir hata kodu dizesi. |
| `error_description` | Bir geliştiricinin kimlik doğrulama hatasının kök nedenini belirlemesine yardımcı olabilecek belirli bir hata iletisi.           |
| `error_codes` |Tanılamada yardımcı olabilecek STS 'ye özgü hata kodlarının bir listesi. |
| `timestamp` | Hatanın oluştuğu zaman. |
| `trace_id` | Tanılamada yardımcı olabilecek istek için benzersiz bir tanımlayıcı. |
| `correlation_id` | İsteğe ait, bileşenler genelinde tanılamada yardımcı olabilecek benzersiz bir tanımlayıcı. |

Hata kodlarının ve önerilen istemci eyleminin açıklaması için bkz. [belirteç uç noktası hataları Için hata kodları](#error-codes-for-token-endpoint-errors).
