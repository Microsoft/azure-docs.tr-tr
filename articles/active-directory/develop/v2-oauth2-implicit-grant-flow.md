---
title: OAuth 2,0 örtük izin verme akışı-Microsoft Identity platform | Mavisi
description: Microsoft Identity platform örtük akışını kullanarak tek sayfalı uygulamaları güvenli hale getirin.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: f3598c6f072d09d7e427db66dcfbf8721b92a3a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99226497"
---
# <a name="microsoft-identity-platform-and-implicit-grant-flow"></a>Microsoft Identity platform ve örtük verme akışı

Microsoft Identity platformu, OAuth [2,0 belirtiminde](https://tools.ietf.org/html/rfc6749#section-4.2)açıklandığı şekilde OAuth 2,0 örtük izin akışını destekler. Örtük izin tanımlama özelliği, belirteçlerin (KIMLIK belirteçleri veya erişim belirteçleri)/Token uç noktası yerine doğrudan/Yetkilendir uç noktasından döndürüldüğünden oluşur. Bu, genellikle [yetkilendirme kodu akışının](v2-oauth2-auth-code-flow.md)bir parçası olarak kullanılır. Bu, "karma akış" olarak adlandırılır ve bir yetkilendirme koduyla birlikte/Yetkilendir isteğine kimlik belirteci alınıyor.

[!INCLUDE [suggest-msal-from-protocols](includes/suggest-msal-from-protocols.md)]

## <a name="prefer-the-auth-code-flow"></a>Kimlik doğrulama kod akışını tercih etme

[Üçüncü taraf tanımlama bilgilerinin tarayıcılardan kaldırılmasına](reference-third-party-cookies-spas.md)yönelik planlar sayesinde, **örtük verme akışı artık uygun bir kimlik doğrulama yöntemi değildir**.  Örtük akışın [sessız SSO özellikleri](#getting-access-tokens-silently-in-the-background) , üçüncü taraf tanımlama bilgileri olmadan çalışmaz ve yeni bir belirteç almaya çalıştıklarında uygulamaların bozulmasına neden olur. Tüm yeni uygulamaların, artık örtük akış yerine tek sayfalı uygulamaları destekleyen [yetkilendirme kodu akışını](v2-oauth2-auth-code-flow.md) kullanmasını ve [var olan tek sayfalı uygulamaların yetkilendirme kodu akışına geçirmeye başlamasını](migrate-spa-implicit-to-auth-code.md) önemle öneririz.

## <a name="suitable-scenarios-for-the-oauth2-implicit-grant"></a>OAuth2 örtük verme için uygun senaryolar

Örtük izin yalnızca oturum açma akışınızın ilk ve etkileşimli bölümü için güvenilir, burada [üçüncü taraf tanımlama bilgilerinin](reference-third-party-cookies-spas.md) olmaması uygulamanızı etkilemez. Bu sınırlama, uygulamanızın bir kod ve yetkilendirme uç noktasından bir belirteç istediği karma akışın bir parçası olarak özel olarak kullanılması gerektiği anlamına gelir. Bu, uygulamanızın yenileme belirteci için kullanılabilecek bir kod almasını sağlar, böylece uygulamanızın oturum açma oturumunun zaman içinde geçerli kalmasını sağlamaktır.

## <a name="protocol-diagram"></a>Protokol diyagramı

Aşağıdaki diyagramda, tüm örtük oturum açma akışının nasıl göründüğü ve izleyen bölümlerin her adımı daha ayrıntılı bir şekilde açıklaması gösterilmektedir.

![Örtük oturum açma akışını gösteren diyagram](./media/v2-oauth2-implicit-grant-flow/convergence-scenarios-implicit.svg)

## <a name="send-the-sign-in-request"></a>Oturum açma isteğini gönder

Kullanıcıyı uygulamanızda ilk kez imzalamak için, bir [OpenID Connect](v2-protocols-oidc.md) kimlik doğrulama isteği gönderebilir ve `id_token` Microsoft Identity platformundan bir bağlantı alabilirsiniz.

> [!IMPORTANT]
> KIMLIK belirteci ve/veya erişim belirtecini başarıyla istemek için, [Azure portal uygulama kayıtları](https://go.microsoft.com/fwlink/?linkid=2083908) sayfasındaki uygulama kaydında, **örtük izin verme ve karma akışlar** bölümünde **Kimlik belirteçleri** ve **erişim belirteçleri** seçilerek, karşılık gelen örtük izin akışı etkinleştirilmiş olmalıdır. Etkin değilse bir `unsupported_response` hata döndürülür: `The provided value for the input parameter 'response_type' is not allowed for this client. Expected value is 'code'`

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=openid
&response_mode=fragment
&state=12345
&nonce=678910
```

> [!TIP]
> Örtük akışı kullanarak oturum açmayı test etmek için öğesine tıklayın <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank"> https://login.microsoftonline.com/common/oauth2/v2.0/authorize.. .</a> Oturum açtıktan sonra, tarayıcınız `https://localhost/myapp/` Adres çubuğunda bir ile yeniden yönlendirilmelidir `id_token` .
>

| Parametre | Tür | Description |
| --- | --- | --- |
| `tenant` | gerekli |`{tenant}`İsteğin yolundaki değeri, uygulamada kimlerin oturum açmasını denetlemek için kullanılabilir. İzin verilen değerler, `common` , `organizations` `consumers` ve kiracı tanımlayıcılarıdır. Daha fazla ayrıntı için bkz. [protokol temelleri](active-directory-v2-protocols.md#endpoints). |
| `client_id` | gerekli | [Azure portal uygulama kayıtları](https://go.microsoft.com/fwlink/?linkid=2083908) sayfasının uygulamanıza atadığı uygulama (ISTEMCI) kimliği. |
| `response_type` | gerekli |`id_token`OpenID Connect oturum açma için içermelidir. Ayrıca, response_type de içerebilir `token` . `token`Burada kullanılması, uygulamanızın yetkilendirme uç noktasına ikinci bir istek yapmadan yetkilendirme uç noktasından hemen erişim belirteci almasına izin verir. `token`Response_type kullanırsanız, `scope` parametrenin belirtecin hangi kaynakla ilgili olduğunu belirten bir kapsam içermesi gerekir (örneğin, Microsoft Graph üzerinde User. Read). Ayrıca, yetkilendirme kodu `code` `token` [akışında](v2-oauth2-auth-code-flow.md)kullanılmak üzere bir yetkilendirme kodu sağlamak için yerinde de bulunabilir. Bu id_token + kod yanıtı karma akış olarak adlandırılır.  |
| `redirect_uri` | Önerilen |Uygulamanızın, kimlik doğrulama yanıtlarının sizin uygulamanız tarafından gönderilebileceği ve alınabileceği redirect_uri. Portalın, URL kodlamalı olması dışında, portalda kaydettiğiniz redirect_uris biriyle tam olarak eşleşmesi gerekir. |
| `scope` | gerekli |[Kapsamların](v2-permissions-and-consent.md)boşlukla ayrılmış listesi. OpenID Connect (id_tokens) için, `openid` izin Kullanıcı arabirimindeki "oturum aç" iznine çeviren kapsamı içermelidir. İsteğe bağlı olarak, `email` `profile` ek kullanıcı verilerine erişim kazanmak için ve kapsamlarını da dahil etmek isteyebilirsiniz. Ayrıca, bir erişim belirteci isteniyorsa, çeşitli kaynaklara izin istemek için bu istekteki diğer kapsamları da dahil edebilirsiniz. |
| `response_mode` | isteğe bağlı |Elde edilen belirteci uygulamanıza geri göndermek için kullanılması gereken yöntemi belirtir. Yalnızca bir erişim belirtecinin sorgulaması yapılır, ancak istek bir id_token içeriyorsa parçalara ayırma olur. |
| `state` | Önerilen |İsteğin belirteç yanıtında de döndürülecek bir değer. Bu, istediğiniz herhangi bir içerik dizesi olabilir. Rastgele oluşturulan benzersiz bir değer genellikle [siteler arası istek sahteciliği saldırılarını önlemek](https://tools.ietf.org/html/rfc6749#section-10.12)için kullanılır. Durum Ayrıca, kullanıcının uygulamadaki durumu hakkında bilgi kodlamak için kullanılır; Örneğin, bulunan sayfa veya görünüm gibi kimlik doğrulama isteği gerçekleştirilmeden önce. |
| `nonce` | gerekli |Uygulama tarafından oluşturulan, sonuçta elde edilen id_token talep olarak dahil edilecek bir değer. Daha sonra uygulama, belirteç yeniden yürütme saldırılarını azaltmak için bu değeri doğrulayabilirler. Değer genellikle, isteğin kaynağını belirlemek için kullanılabilecek rastgele, benzersiz bir dizedir. Yalnızca bir id_token istendiğinde gereklidir. |
| `prompt` | isteğe bağlı |Gerekli kullanıcı etkileşiminin türünü gösterir. Şu anda yalnızca geçerli değerler: ' login', ' none ', ' select_account ' ve ' onay '. `prompt=login` , kullanıcıyı bu istek üzerine kimlik bilgilerini girmeye zorlar ve çoklu oturum açma 'yı yok eder. `prompt=none` Bunun tersi, kullanıcının herhangi bir etkileşimli istem ile sunulmayacağını garanti eder. İstek, tek oturum açma yoluyla sessizce tamamlanamayacak, Microsoft Identity platform bir hata döndürür. `prompt=select_account` kullanıcıyı, oturumda hatırlanan tüm hesapların görüneceği bir hesap seçicisine gönderir. `prompt=consent` Kullanıcı oturum açtıktan sonra OAuth onay iletişim kutusunu tetikler, böylece kullanıcıdan uygulamaya izin vermesini istenir. |
| `login_hint`  |isteğe bağlı |Kullanıcı adını zaman önce biliyorsanız, Kullanıcı için oturum açma sayfasının Kullanıcı adı/e-posta adresi alanını önceden doldurabilirsiniz. Genellikle bu parametre, talebi kullanarak önceki bir oturum açma işleminden zaten ayıklanan uygulamalar, yeniden kimlik doğrulama sırasında kullanılır `preferred_username` .|
| `domain_hint` | isteğe bağlı |Dahil edilse, kullanıcının oturum açma sayfasında, daha kolay bir kullanıcı deneyimi için önde gelen e-posta tabanlı bulma işlemini atlar. Bu parametre genellikle tek bir kiracıya çalışan Iş kolu uygulamaları için kullanılır. burada, belirli bir kiracıda bir etki alanı adı sunduklarında, kullanıcıyı söz konusu kiracıya ait Federasyon sağlayıcısına ileten.  Bu ipucunun konukların bu uygulamada oturum açmasını önlediği ve FIDO gibi bulut kimlik bilgilerinin kullanımını sınırlayan unutulmamalıdır.  |

Bu noktada, kullanıcıdan kimlik bilgilerini girmesi ve kimlik doğrulamasını tamamlaması istenir. Microsoft Identity platformu, kullanıcının sorgu parametresinde belirtilen izinlere de sahip olduğundan emin olur `scope` . Kullanıcı bu izinlerden **hiçbirine** izin vermişse, kullanıcıdan gerekli izinleri vermesini ister. Daha fazla bilgi için bkz. [izinler, onay ve çok kiracılı uygulamalar](v2-permissions-and-consent.md).

Kullanıcı kimlik doğrulamasından ve izin verdiğinde, Microsoft Identity platformu, `redirect_uri` parametresinde belirtilen yöntemi kullanarak, belirtilen şekilde uygulamanıza bir yanıt döndürür `response_mode` .

#### <a name="successful-response"></a>Başarılı yanıt

Kullanılarak başarılı bir yanıt `response_mode=fragment` `response_type=id_token+code` , aşağıdaki gibi görünür (okunabilirliği için satır sonları ile):

```HTTP
GET https://localhost/myapp/#
code=0.AgAAktYV-sfpYESnQynylW_UKZmH-C9y_G1A
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
```

| Parametre | Açıklama |
| --- | --- |
| `code` | Dahil ise `response_type` dahildir `code` . Bu, [yetkilendirme kodu akışında](v2-oauth2-auth-code-flow.md)kullanılmak üzere uygun bir yetkilendirme kodudur.  |
| `access_token` |Dahil ise `response_type` dahildir `token` . Uygulamanın istediği erişim belirteci. Erişim belirtecinin kodu çözülemedi veya bunun için bir donuk dize olarak değerlendirilmemelidir. |
| `token_type` |Dahil ise `response_type` dahildir `token` . Her zaman olacak `Bearer` . |
| `expires_in`|Dahil ise `response_type` dahildir `token` . Önbelleğe alma amacıyla belirtecin geçerli olduğu saniye sayısını gösterir. |
| `scope` |Dahil ise `response_type` dahildir `token` . Access_token geçerli olacağı kapsam (ler) i gösterir. Kullanıcı için geçerli olmadıklarında istenen tüm kapsamları içermeyebilir (oturum açmak için bir kişisel hesap kullanıldığında yalnızca Azure AD kapsamları isteniyorsa). |
| `id_token` | İmzalı bir JSON Web Token (JWT). Uygulama, oturum açan kullanıcı hakkında bilgi istemek için bu belirtecin segmentlerinin kodunu çözebilir. Uygulama, değerleri önbelleğe alabilir ve görüntüleyebilir, ancak herhangi bir yetkilendirme veya güvenlik sınırları için bu değere güvenmemelidir. İd_tokens hakkında daha fazla bilgi için bkz [`id_token reference`](id-tokens.md) .. <br> **Note:** Yalnızca `openid` kapsam isteniyorsa ve `response_type` dahil edildiyse sağlanır `id_tokens` . |
| `state` |İsteğe bir durum parametresi dahil edilir, yanıtta aynı değer görünmelidir. Uygulama, istek ve yanıtta durum değerlerinin özdeş olduğunu doğrulamalıdır. |

#### <a name="error-response"></a>Hata yanıtı

Ayrıca, `redirect_uri` uygulamanın bunları uygun şekilde işleyebilmesi için hata yanıtları da gönderilebilir.

```HTTP
GET https://localhost/myapp/#
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parametre | Açıklama |
| --- | --- |
| `error` |Oluşan hata türlerini sınıflandırmak için kullanılabilen ve hatalara yanıt vermek için kullanılabilen bir hata kodu dizesi. |
| `error_description` |Bir geliştiricinin kimlik doğrulama hatasının kök nedenini belirlemesine yardımcı olabilecek belirli bir hata iletisi. |

## <a name="getting-access-tokens-silently-in-the-background"></a>Arka planda sessizce erişim belirteçleri alma

> [!Important]
> Örtük akışın bu bölümü, [üçüncü taraf tanımlama bilgilerinin varsayılan olarak kaldırılması](reference-third-party-cookies-spas.md)nedeniyle farklı tarayıcılarda kullanıldığı için uygulamanız için büyük olasılıkla çalışmayabilir.  Bu, şu anda ınbilito 'da olmayan Kmıum tabanlı tarayıcılarda çalışırken, geliştiricilerin akışın bu bölümünü kullanmayı yeniden düşünmelidir. Üçüncü taraf tanımlama bilgilerini desteklemeyen tarayıcılarda, oturum açma sayfasının oturum tanımlama bilgileri tarayıcı tarafından kaldırıldığı için hiçbir kullanıcının oturum açmadığını belirten bir hata alırsınız. 

Kullanıcıyı tek sayfalı uygulamanıza imzaladığınıza göre, [Microsoft Graph](https://developer.microsoft.com/graph)gibi Microsoft Identity platform tarafından güvenliği sağlanmış Web API 'lerini çağırmak için sessizce erişim belirteçleri alabilirsiniz. Response_type kullanarak zaten bir belirteç almış olsanız bile `token` , kullanıcıyı yeniden oturum açmak üzere yeniden yönlendirmek zorunda kalmadan ek kaynaklara belirteç almak için bu yöntemi kullanabilirsiniz.

Normal OpenID Connect/OAuth akışında, Microsoft Identity platform uç noktasına bir istek yaparak bunu yapabilirsiniz `/token` . Diğer Web API 'Lerine yönelik yeni belirteçleri almak için isteği gizli bir iframe 'de yapabilirsiniz:

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read
&response_mode=fragment
&state=12345
&nonce=678910
&prompt=none
&login_hint=myuser@mycompany.com
```

URL 'deki sorgu parametreleriyle ilgili ayrıntılar için bkz. [oturum açma isteğini gönderme](#send-the-sign-in-request).

> [!TIP]
> Aşağıdaki isteği bir tarayıcı sekmesine yapıştırmayı & kopyalamayı deneyin! ( `login_hint` Değerleri, Kullanıcı için doğru değerle değiştirmeyi unutmayın)
>
>`https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read&response_mode=fragment&state=12345&nonce=678910&prompt=none&login_hint={your-username}`
>
> Bunu bir iframe içinde açmak yerine doğrudan bir tarayıcı çubuğuna girtiğinizden, bu, üçüncü taraf tanımlama bilgisi desteği olmayan tarayıcılarda bile çalışacaktır. 

`prompt=none`Bu parametre sayesinde, bu istek hemen başarılı veya başarısız olur ve uygulamanıza geri döner. Yanıt, `redirect_uri` parametresinde belirtilen yöntemi kullanılarak, belirtilen şekilde uygulamanıza gönderilir `response_mode` .

#### <a name="successful-response"></a>Başarılı yanıt

Kullanarak başarılı bir yanıt `response_mode=fragment` şöyle görünür:

```HTTP
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fdirectory.read
```

| Parametre | Açıklama |
| --- | --- |
| `access_token` |Dahil ise `response_type` dahildir `token` . Uygulamanın istediği erişim belirteci, Microsoft Graph için bu durumda. Erişim belirtecinin kodu çözülemedi veya bunun için bir donuk dize olarak değerlendirilmemelidir. |
| `token_type` | Her zaman olacak `Bearer` . |
| `expires_in` | Önbelleğe alma amacıyla belirtecin geçerli olduğu saniye sayısını gösterir. |
| `scope` | Access_token geçerli olacağı kapsam (ler) i gösterir. Kullanıcı için geçerli olmadıklarında istenen tüm kapsamları içermeyebilir (oturum açmak için bir kişisel hesap kullanıldığında yalnızca Azure AD kapsamları isteniyorsa). |
| `id_token` | İmzalı bir JSON Web Token (JWT). Dahil ise `response_type` dahildir `id_token` . Uygulama, oturum açan kullanıcı hakkında bilgi istemek için bu belirtecin segmentlerinin kodunu çözebilir. Uygulama, değerleri önbelleğe alabilir ve görüntüleyebilir, ancak herhangi bir yetkilendirme veya güvenlik sınırları için bu değere güvenmemelidir. İd_tokens hakkında daha fazla bilgi için bkz. [ `id_token` başvuru](id-tokens.md). <br> **Note:** Yalnızca `openid` kapsam isteniyorsa sağlandı. |
| `state` |İsteğe bir durum parametresi dahil edilir, yanıtta aynı değer görünmelidir. Uygulama, istek ve yanıtta durum değerlerinin özdeş olduğunu doğrulamalıdır. |

#### <a name="error-response"></a>Hata yanıtı

Ayrıca, `redirect_uri` uygulamanın bunları uygun şekilde işleyebilmesi için hata yanıtları da gönderilebilir. `prompt=none`Bu durumda, beklenen bir hata şu şekilde olur:

```HTTP
GET https://localhost/myapp/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Parametre | Açıklama |
| --- | --- |
| `error` |Oluşan hata türlerini sınıflandırmak için kullanılabilen ve hatalara yanıt vermek için kullanılabilen bir hata kodu dizesi. |
| `error_description` |Bir geliştiricinin kimlik doğrulama hatasının kök nedenini belirlemesine yardımcı olabilecek belirli bir hata iletisi. |

İframe isteğinde bu hatayı alırsanız, Kullanıcı yeni bir belirteci almak için etkileşimli olarak yeniden oturum açması gerekir. Bu durumu uygulamanız için anlamlı hale getiren her türlü şekilde yapmayı tercih edebilirsiniz.

## <a name="refreshing-tokens"></a>Belirteçleri yenileme

Örtük izin yenileme belirteçleri sağlamıyor. Her iki `id_token` s ve `access_token` s kısa bir süre sonra sona erer, bu nedenle uygulamanızın bu belirteçleri düzenli aralıklarla yenilemeye hazırlanması gerekir. Her iki tür belirteci de yenilemek için, `prompt=none` kimlik platformunun davranışını denetlemek üzere parametresini kullanarak yukarıdaki aynı gizli iframe isteğini de gerçekleştirebilirsiniz. Yeni bir almak istiyorsanız, `id_token` `id_token` ve ' de ' de kullandığınızdan emin olun `response_type` `scope=openid` `nonce` .

Üçüncü taraf tanımlama bilgilerini desteklemeyen tarayıcılarda bu, kullanıcının oturum açmadığını belirten bir hatayla sonuçlanır. 

## <a name="send-a-sign-out-request"></a>Oturum kapatma isteği gönder

OpenID Connect, `end_session_endpoint` uygulamanızın bir kullanıcının oturumunu sona erdirmek ve Microsoft Identity platformu tarafından ayarlanan tanımlama bilgilerini temizlemek Için Microsoft Identity platformu 'na bir istek göndermesini sağlar. Bir kullanıcıyı bir Web uygulamasından tam olarak imzalamak için, uygulamanız kullanıcıyla kendi oturumunu sonlandırmalıdır (genellikle bir belirteç önbelleğini temizleyerek veya tanımlama bilgilerini bırakarak) ve ardından tarayıcıyı şuraya yönlendirmelidir:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/logout?post_logout_redirect_uri=https://localhost/myapp/
```

| Parametre | Tür | Description |
| --- | --- | --- |
| `tenant` |gerekli |`{tenant}`İsteğin yolundaki değeri, uygulamada kimlerin oturum açmasını denetlemek için kullanılabilir. İzin verilen değerler, `common` , `organizations` `consumers` ve kiracı tanımlayıcılarıdır. Daha fazla ayrıntı için bkz. [protokol temelleri](active-directory-v2-protocols.md#endpoints). |
| `post_logout_redirect_uri` | Önerilen | Oturum kapatma tamamlandıktan sonra kullanıcının geri döndürülmesi gereken URL. Bu değer, uygulama için kaydedilmiş yeniden yönlendirme URI 'lerinden biriyle aynı olmalıdır. Dahil edilmezse, kullanıcıya Microsoft Identity platformu tarafından genel bir ileti gösterilir. |

## <a name="next-steps"></a>Sonraki adımlar

* Kodlamaya başlamak için [msal js örneklerinin](sample-v2-code.md) üzerine gidin.
* [Yetkilendirme kodu akışını](v2-oauth2-auth-code-flow.md) daha yeni, örtük izin daha iyi bir alternatif olarak gözden geçirin. 
