---
title: Microsoft Identity platform örtük akışını kullanarak tek sayfalı uygulamaları güvenli hale getirme | Mavisi
description: Tek sayfalı uygulamalar için örtük akışın Microsoft Identity platform uygulamasını kullanarak Web uygulamaları oluşturma.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 3605931f-dc24-4910-bb50-5375defec6a8
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 136a018e3ac66e2f3fd928a786a24652b99ea040
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73600988"
---
# <a name="microsoft-identity-platform-and-implicit-grant-flow"></a>Microsoft Identity platform ve örtük verme akışı

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Microsoft Identity platform uç noktası ile, Microsoft 'un hem kişisel hem de iş veya okul hesaplarıyla kullanıcıları tek sayfalı uygulamalarınıza imzalayabilirsiniz. Birincil olarak bir tarayıcıda çalışan tek sayfalı ve diğer JavaScript uygulamaları, kimlik doğrulamasından geldiği sırada birkaç ilginç zorluk sunar:

* Bu uygulamaların güvenlik özellikleri geleneksel sunucu tabanlı Web uygulamalarından önemli ölçüde farklıdır.
* Birçok yetkilendirme sunucusu ve kimlik sağlayıcısı CORS isteklerini desteklemez.
* Uygulamanın dışından tam sayfa tarayıcı yeniden yönlendirmeleri, Kullanıcı deneyimine özellikle karşı bir inme gelir.

Bu uygulamalar (AngularJS, Ember. js, tepki. js vb.) için Microsoft Identity platform, OAuth 2,0 örtük Izin akışını destekler. Örtük akış, [OAuth 2,0 belirtiminde](https://tools.ietf.org/html/rfc6749#section-4.2)açıklanmaktadır. Birincil avantajı, uygulamanın arka uç sunucu kimlik bilgisi değişimi gerçekleştirmeden Microsoft Identity platformundan belirteçler almasına izin vermekle aynıdır. Bu, uygulamanın kullanıcıya oturum açmasını, oturum korumasını ve diğer Web API 'Lerine yönelik belirteçleri istemci JavaScript kodu içinde almasını sağlar. Özellikle [istemci](https://tools.ietf.org/html/rfc6749#section-10.3) ve [kullanıcı kimliğine bürünme](https://tools.ietf.org/html/rfc6749#section-10.3)etrafında örtük akışı kullanırken dikkate alınması gereken birkaç önemli güvenlik konusu vardır.

JavaScript uygulamanıza kimlik doğrulaması eklemek için örtük akış ve Microsoft Identity platformunu kullanmak istiyorsanız, [msal. js](https://github.com/AzureAD/microsoft-authentication-library-for-js)açık kaynaklı JavaScript kitaplığını kullanmanızı öneririz.

Ancak, tek sayfalı uygulamanızdaki bir kitaplığı kullanmayı ve protokol iletilerini kendiniz göndermenizi tercih ediyorsanız aşağıdaki genel adımları izleyin.

> [!NOTE]
> Tüm Azure Active Directory (Azure AD) senaryoları ve özellikleri Microsoft Identity platform uç noktası tarafından desteklenmez. Microsoft Identity platform uç noktasını kullanmanız gerekip gerekmediğini öğrenmek için [Microsoft Identity platform sınırlamaları](active-directory-v2-limitations.md)hakkında bilgi edinin.

## <a name="protocol-diagram"></a>Protokol diyagramı

Aşağıdaki diyagramda, tüm örtük oturum açma akışının nasıl göründüğü ve izleyen bölümlerin her adımı daha ayrıntılı bir şekilde açıklaması gösterilmektedir.

![Örtük oturum açma akışını gösteren diyagram](./media/v2-oauth2-implicit-grant-flow/convergence-scenarios-implicit.svg)

## <a name="send-the-sign-in-request"></a>Oturum açma isteğini gönder

Kullanıcıyı uygulamanızda ilk kez imzalamak için, bir [OpenID Connect](v2-protocols-oidc.md) kimlik doğrulama isteği gönderebilir ve Microsoft Identity platform uç noktasından bir `id_token` alabilirsiniz.

> [!IMPORTANT]
> Bir KIMLIK belirtecini ve/veya erişim belirtecini başarıyla istemek için, [Azure portal uygulama kayıtları](https://go.microsoft.com/fwlink/?linkid=2083908) sayfasındaki uygulama kaydında, **Kimlik belirteçleri** ve ' nin altındaki **belirteç** belirteçleri ' ni seçerek karşılık gelen örtük izin akışı etkinleştirilmiş olmalıdır. **örtük verme** bölümü. Etkinleştirilmemişse `unsupported_response` bir hata döndürülür: **' response_type ' giriş parametresi için sağlanan değere bu istemci için izin verilmiyor. Beklenen değer ' Code '**

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
> Örtük akışı kullanarak oturum açmayı test etmek için https://login.microsoftonline.com/common/oauth2/v2.0/authorize..' ye tıklayın <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank">.</a> Oturum açtıktan sonra tarayıcınızın, adres çubuğunda bir `id_token` `https://localhost/myapp/` yeniden yönlendirilmesi gerekir.
>

| Parametre |  | Açıklama |
| --- | --- | --- |
| `tenant` | Gerekli |İsteğin yolundaki `{tenant}` değeri, uygulamada kimlerin oturum açmasını denetlemek için kullanılabilir. İzin verilen değerler `common`, `organizations`, `consumers`ve kiracı tanımlayıcılarıdır. Daha fazla ayrıntı için bkz. [protokol temelleri](active-directory-v2-protocols.md#endpoints). |
| `client_id` | Gerekli | [Azure portal uygulama kayıtları](https://go.microsoft.com/fwlink/?linkid=2083908) sayfasının uygulamanıza atadığı uygulama (ISTEMCI) kimliği. |
| `response_type` | Gerekli |OpenID Connect oturum açma `id_token` içermelidir. Ayrıca, response_type `token`de içerebilir. Burada `token` kullanmak, uygulamanızın yetkilendirme uç noktasına ikinci bir istek yapmadan yetkilendirme uç noktasından hemen erişim belirteci almasına izin verir. `token` response_type kullanıyorsanız `scope` parametresi, belirteci hangi kaynağın (örneğin, Kullanıcı. Read for Microsoft Graph) kullanacağınızı gösteren bir kapsam içermelidir.  |
| `redirect_uri` | Önerilen |Uygulamanızın kimlik doğrulama yanıtlarının gönderilebileceği ve alınabileceği, uygulamanızın redirect_uri 'ı. Portalın, URL kodlamalı olması dışında, portala kaydettiğiniz redirect_uris biriyle tam olarak eşleşmesi gerekir. |
| `scope` | Gerekli |[Kapsamların](v2-permissions-and-consent.md)boşlukla ayrılmış listesi. OpenID Connect (id_tokens) için, izin Kullanıcı arabirimindeki "oturum aç" iznine çeviren `openid`kapsam içermelidir. İsteğe bağlı olarak, ek kullanıcı verilerine erişim kazanmak için `email` ve `profile` kapsamlarını da dahil etmek isteyebilirsiniz. Ayrıca, bir erişim belirteci isteniyorsa, çeşitli kaynaklara izin istemek için bu istekteki diğer kapsamları da dahil edebilirsiniz. |
| `response_mode` | Seçim |Elde edilen belirteci uygulamanıza geri göndermek için kullanılması gereken yöntemi belirtir. Yalnızca bir erişim belirtecinin sorgulaması yapılır, ancak istek bir id_token içeriyorsa parçalara ayırma olur. |
| `state` | Önerilen |İsteğin belirteç yanıtında de döndürülecek bir değer. Bu, istediğiniz herhangi bir içerik dizesi olabilir. Rastgele oluşturulan benzersiz bir değer genellikle [siteler arası istek sahteciliği saldırılarını önlemek](https://tools.ietf.org/html/rfc6749#section-10.12)için kullanılır. Durum Ayrıca, kullanıcının uygulamadaki durumu hakkında bilgi kodlamak için kullanılır; Örneğin, bulunan sayfa veya görünüm gibi kimlik doğrulama isteği gerçekleştirilmeden önce. |
| `nonce` | Gerekli |Uygulama tarafından oluşturulan, sonuçta elde edilen id_token talep olarak dahil edilecek bir değer. Daha sonra uygulama, belirteç yeniden yürütme saldırılarını azaltmak için bu değeri doğrulayabilirler. Değer genellikle, isteğin kaynağını belirlemek için kullanılabilecek rastgele, benzersiz bir dizedir. Yalnızca bir id_token istendiğinde gereklidir. |
| `prompt` | Seçim |Gerekli kullanıcı etkileşiminin türünü gösterir. Şu anda yalnızca geçerli değerler: ' login', ' none ', ' select_account ' ve ' onay '. `prompt=login`, kullanıcıyı bu istek üzerine kimlik bilgilerini girmeye zorlar ve çoklu oturum açma 'yı yok eder. `prompt=none` bunun tersidir; kullanıcının herhangi bir etkileşimli istem ile sunulmayacağını garanti eder. İstek, tek oturum açma yoluyla sessizce tamamlanamayacak, Microsoft Identity platform uç noktası bir hata döndürür. `prompt=select_account` kullanıcıyı, oturumda hatırlanan tüm hesapların görüneceği bir hesap seçicisine gönderir. `prompt=consent`, Kullanıcı oturum açtıktan sonra kullanıcıdan uygulamaya izin vermesini isteyen OAuth onay iletişim kutusunu tetikleyecektir. |
| `login_hint`  |Seçim |Kullanıcı adını zaman önce biliyorsanız, Kullanıcı için oturum açma sayfasının Kullanıcı adı/e-posta adresi alanını önceden doldurabilirsiniz. Genellikle uygulamalar bu parametreyi yeniden kimlik doğrulama sırasında kullanır ve `preferred_username` talebini kullanarak önceki bir oturum açma işleminden zaten Kullanıcı adını ayıklamış olur.|
| `domain_hint` | Seçim |Dahil edilse, kullanıcının oturum açma sayfasında, daha kolay bir kullanıcı deneyimi için önde gelen e-posta tabanlı bulma işlemini atlar. Bu, genellikle belirli bir kiracıda bir etki alanı adı sundukları tek bir kiracıya çalışan Iş kolu uygulamaları için kullanılır.  Bu işlem, kullanıcıyı bu kiracının Federasyon sağlayıcısına iletir.  Bunun, konukların bu uygulamada oturum açmasını önleyebileceğini unutmayın.  |

Bu noktada, kullanıcıdan kimlik bilgilerini girmesi ve kimlik doğrulamasını tamamlaması istenir. Microsoft Identity platform uç noktası ayrıca kullanıcının `scope` sorgu parametresinde belirtilen izinlere onay aldığından emin olur. Kullanıcı bu izinlerden **hiçbirine** izin vermişse, kullanıcıdan gerekli izinleri vermesini ister. Daha fazla bilgi için bkz. [izinler, onay ve çok kiracılı uygulamalar](v2-permissions-and-consent.md).

Kullanıcı kimlik doğrulamasından ve izin verdiğinde, Microsoft Identity platform uç noktası, `response_mode` parametresinde belirtilen yöntemi kullanarak belirtilen `redirect_uri`uygulamanıza bir yanıt döndürür.

#### <a name="successful-response"></a>Başarılı yanıt

`response_mode=fragment` ve `response_type=id_token+token` kullanarak başarılı bir yanıt aşağıdakine benzer (okunabilirliği için satır sonları ile):

```
GET https://localhost/myapp/#
&token_type=Bearer
&expires_in=3599
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
```

| Parametre | Açıklama |
| --- | --- |
| `access_token` |`response_type` `token`içeriyorsa dahildir. Uygulamanın istediği erişim belirteci. Erişim belirtecinin kodu çözülemedi veya bunun için bir donuk dize olarak değerlendirilmemelidir. |
| `token_type` |`response_type` `token`içeriyorsa dahildir. Her zaman `Bearer`olacaktır. |
| `expires_in`|`response_type` `token`içeriyorsa dahildir. Önbelleğe alma amacıyla belirtecin geçerli olduğu saniye sayısını gösterir. |
| `scope` |`response_type` `token`içeriyorsa dahildir. Access_token geçerli olacak kapsam (ler) i gösterir. Kullanıcı için geçerli olmadıklarında istenen tüm kapsamları içermeyebilir (oturum açmak için bir kişisel hesap kullanıldığında yalnızca Azure AD kapsamları isteniyorsa). |
| `id_token` | İmzalı bir JSON Web Token (JWT). Uygulama, oturum açan kullanıcı hakkında bilgi istemek için bu belirtecin segmentlerinin kodunu çözebilir. Uygulama, değerleri önbelleğe alabilir ve görüntüleyebilir, ancak herhangi bir yetkilendirme veya güvenlik sınırları için bu değere güvenmemelidir. İd_tokens hakkında daha fazla bilgi için [`id_token reference`](id-tokens.md)bakın. <br> **Note:** Yalnızca `openid` kapsam isteniyorsa sağlandı. |
| `state` |İsteğe bir durum parametresi dahil edilir, yanıtta aynı değer görünmelidir. Uygulama, istek ve yanıtta durum değerlerinin özdeş olduğunu doğrulamalıdır. |

#### <a name="error-response"></a>Hata yanıtı

Ayrıca, uygulamanın bunları uygun şekilde işleyebilmesi için `redirect_uri` hata yanıtları da gönderilebilir:

```
GET https://localhost/myapp/#
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parametre | Açıklama |
| --- | --- |
| `error` |Oluşan hata türlerini sınıflandırmak için kullanılabilen ve hatalara yanıt vermek için kullanılabilen bir hata kodu dizesi. |
| `error_description` |Bir geliştiricinin kimlik doğrulama hatasının kök nedenini belirlemesine yardımcı olabilecek belirli bir hata iletisi. |

## <a name="getting-access-tokens-silently-in-the-background"></a>Arka planda sessizce erişim belirteçleri alma

Kullanıcıyı tek sayfalı uygulamanıza imzaladığınıza göre, [Microsoft Graph](https://developer.microsoft.com/graph)gibi Microsoft Identity platform tarafından güvenliği sağlanmış Web API 'lerini çağırmak için sessizce erişim belirteçleri alabilirsiniz. `token` response_type kullanarak zaten bir belirteç almış olsanız bile, bu yöntemi, kullanıcıyı yeniden oturum açmak için yeniden yönlendirmenize gerek kalmadan ek kaynaklara belirteç elde etmek için kullanabilirsiniz.

Normal OpenID Connect/OAuth akışında, Microsoft Identity platform `/token` uç noktasına bir istek yaparak bunu yapabilirsiniz. Ancak, Microsoft Identity platform uç noktası CORS isteklerini desteklemez, bu nedenle belirteçleri alma ve yenileme için AJAX çağrılarını gerçekleştirmek söz konusu değildir. Bunun yerine, diğer Web API 'Leri için yeni belirteçler almak üzere bir gizli iframe 'de örtük akışı kullanabilirsiniz: 

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
> Aşağıdaki isteği bir tarayıcı sekmesine yapıştırmayı & kopyalamayı deneyin! (`login_hint` değerlerini, Kullanıcı için doğru değerle değiştirmeyi unutmayın)
>
>`https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=https%3A%2F%2Fgraph.microsoft.com%2user.read&response_mode=fragment&state=12345&nonce=678910&prompt=none&login_hint={your-username}`
>

`prompt=none` parametresi sayesinde, bu istek hemen başarılı veya başarısız olur ve uygulamanıza geri döner. `response_mode` parametresinde belirtilen yöntem kullanılarak, belirtilen `redirect_uri`uygulamanıza başarılı bir yanıt gönderilir.

#### <a name="successful-response"></a>Başarılı yanıt

`response_mode=fragment` kullanarak başarılı bir yanıt şöyle görünür:

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read
```

| Parametre | Açıklama |
| --- | --- |
| `access_token` |`response_type` `token`içeriyorsa dahildir. Uygulamanın istediği erişim belirteci, Microsoft Graph için bu durumda. Erişim belirtecinin kodu çözülemedi veya bunun için bir donuk dize olarak değerlendirilmemelidir. |
| `token_type` | Her zaman `Bearer`olacaktır. |
| `expires_in` | Önbelleğe alma amacıyla belirtecin geçerli olduğu saniye sayısını gösterir. |
| `scope` | Access_token geçerli olacak kapsam (ler) i gösterir. Kullanıcı için geçerli olmadıklarında istenen tüm kapsamları içermeyebilir (oturum açmak için bir kişisel hesap kullanıldığında yalnızca Azure AD kapsamları isteniyorsa). |
| `id_token` | İmzalı bir JSON Web Token (JWT). `response_type` `id_token`içeriyorsa dahildir. Uygulama, oturum açan kullanıcı hakkında bilgi istemek için bu belirtecin segmentlerinin kodunu çözebilir. Uygulama, değerleri önbelleğe alabilir ve görüntüleyebilir, ancak herhangi bir yetkilendirme veya güvenlik sınırları için bu değere güvenmemelidir. İd_tokens hakkında daha fazla bilgi için [`id_token` başvurusuna](id-tokens.md)bakın. <br> **Note:** Yalnızca `openid` kapsam isteniyorsa sağlandı. |
| `state` |İsteğe bir durum parametresi dahil edilir, yanıtta aynı değer görünmelidir. Uygulama, istek ve yanıtta durum değerlerinin özdeş olduğunu doğrulamalıdır. |

#### <a name="error-response"></a>Hata yanıtı

Ayrıca, uygulamanın bunları uygun şekilde işleyebilmesi için `redirect_uri` hata yanıtları da gönderilebilir. `prompt=none`durumunda, beklenen bir hata şu şekilde olur:

```
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

Örtük izin yenileme belirteçleri sağlamıyor. `id_token`s ve `access_token`s 'nin her ikisi de kısa bir süre sonra sona erer, bu nedenle uygulamanızın bu belirteçleri düzenli aralıklarla yenilemeye hazırlanması gerekir. Her iki tür belirteci yenilemek için, kimlik platformunun davranışını denetlemek üzere `prompt=none` parametresini kullanarak yukarıdaki aynı gizli iframe isteğini de gerçekleştirebilirsiniz. Yeni bir `id_token`almak istiyorsanız, `response_type` ve `scope=openid`ve `nonce` bir parametre `id_token` kullandığınızdan emin olun.

## <a name="send-a-sign-out-request"></a>Oturum kapatma isteği gönder

OpenID Connect `end_session_endpoint`, uygulamanızın bir kullanıcının oturumunu sona erdirmek ve Microsoft Identity platform uç noktası tarafından ayarlanan tanımlama bilgilerini temizlemek için Microsoft Identity platform uç noktasına bir istek göndermesini sağlar. Bir kullanıcıyı bir Web uygulamasından tam olarak imzalamak için, uygulamanız kullanıcıyla kendi oturumunu sonlandırmalıdır (genellikle bir belirteç önbelleğini temizleyerek veya tanımlama bilgilerini bırakarak) ve ardından tarayıcıyı şuraya yönlendirmelidir:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/logout?post_logout_redirect_uri=https://localhost/myapp/
```

| Parametre |  | Açıklama |
| --- | --- | --- |
| `tenant` |Gerekli |İsteğin yolundaki `{tenant}` değeri, uygulamada kimlerin oturum açmasını denetlemek için kullanılabilir. İzin verilen değerler `common`, `organizations`, `consumers`ve kiracı tanımlayıcılarıdır. Daha fazla ayrıntı için bkz. [protokol temelleri](active-directory-v2-protocols.md#endpoints). |
| `post_logout_redirect_uri` | Önerilen | Oturum kapatma tamamlandıktan sonra kullanıcının geri döndürülmesi gereken URL. Bu değer, uygulama için kaydedilmiş yeniden yönlendirme URI 'lerinden biriyle aynı olmalıdır. Dahil edilmezse, kullanıcıya Microsoft Identity platform uç noktası tarafından genel bir ileti gösterilir. |

## <a name="next-steps"></a>Sonraki adımlar

* Kodlamaya başlamak için [msal js örneklerinin](sample-v2-code.md) üzerine gidin.
