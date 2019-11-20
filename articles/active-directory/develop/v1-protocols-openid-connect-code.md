---
title: Azure AD 'de OpenID Connect kimlik doğrulama kod akışını anlayın | Microsoft Docs
description: Bu makalede, Azure Active Directory ve OpenID Connect kullanarak kiracınızdaki Web uygulamalarına ve Web API 'Lerine erişim yetkisi vermek için HTTP iletilerinin nasıl kullanılacağı açıklanır.
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 29142f7e-d862-4076-9a1a-ecae5bcd9d9b
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7c2e80f80ea5d7e7d5ee26eee8b26506386a6e2f
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70389783"
---
# <a name="authorize-access-to-web-applications-using-openid-connect-and-azure-active-directory"></a>OpenID Connect ve Azure Active Directory kullanarak Web uygulamalarına erişim yetkisi verme

[OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) , OAuth 2,0 protokolünün üzerine inşa olan basit bir kimlik katmanıdır. OAuth 2,0, korumalı kaynaklara erişmek için [**erişim belirteçleri**](access-tokens.md) alma ve kullanma mekanizmalarını tanımlar, ancak kimlik bilgilerini sağlamak için standart yöntemler tanımlamaz. OpenID Connect, kimlik doğrulamasını OAuth 2,0 yetkilendirme işlemine bir uzantı olarak uygular. Son Kullanıcı hakkında, kullanıcının kimliğini doğrulayan ve Kullanıcı hakkında temel profil [`id_token`](id-tokens.md) bilgileri sağlayan bir biçiminde bilgiler sağlar.

OpenID Connect, sunucuda barındırılan ve bir tarayıcı aracılığıyla erişilebilen bir Web uygulaması oluşturuyorsanız önerimiz.


[!INCLUDE [active-directory-protocols-getting-started](../../../includes/active-directory-protocols-getting-started.md)] 

## <a name="authentication-flow-using-openid-connect"></a>OpenID Connect kullanarak kimlik doğrulama akışı

En temel oturum açma akışı aşağıdaki adımları içerir ve her biri aşağıda ayrıntılı olarak açıklanmıştır.

![OpenID Connect kimlik doğrulama akışı](./media/v1-protocols-openid-connect-code/active-directory-oauth-code-flow-web-app.png)

## <a name="openid-connect-metadata-document"></a>OpenID Connect meta veri belgesi

OpenID Connect, bir uygulamanın oturum açma işlemini gerçekleştirmesi için gereken bilgilerin çoğunu içeren bir meta veri belgesi tanımlar. Bu, kullanılacak URL 'Ler ve hizmetin ortak imzalama anahtarlarının konumu gibi bilgileri içerir. OpenID Connect meta veri belgesi şurada bulunabilir:

```
https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration
```
Meta veriler basit bir JavaScript Nesne Gösterimi (JSON) belgesidir. Örnek için aşağıdaki kod parçacığına bakın. Kod parçacığının içeriği [OpenID Connect belirtiminde](https://openid.net)tamamen açıklanmıştır. Yukarıdaki {Tenant} yerine bir Kiracı kimliği `common` sağlamanın, döndürülen JSON nesnesinde kiracıya özgü URI 'ler oluşmasına neden olacağını unutmayın.

```
{
    "authorization_endpoint": "https://login.microsoftonline.com/{tenant}/oauth2/authorize",
    "token_endpoint": "https://login.microsoftonline.com/{tenant}/oauth2/token",
    "token_endpoint_auth_methods_supported":
    [
        "client_secret_post",
        "private_key_jwt",
        "client_secret_basic"
    ],
    "jwks_uri": "https://login.microsoftonline.com/common/discovery/keys"
    "userinfo_endpoint":"https://login.microsoftonline.com/{tenant}/openid/userinfo",
    ...
}
```

Uygulamanızda, [talep eşleme](active-directory-claims-mapping.md) özelliğini kullanmanın bir sonucu olarak özel İmzalama anahtarları varsa, uygulamanızın imzalama anahtarı bilgilerine `appid` `jwks_uri` işaret etmek için uygulama kimliğini içeren bir sorgu parametresi eklemeniz gerekir. Örneğin: `https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration?appid=6731de76-14a6-49ae-97bc-6eba6914391e` `jwks_uri` biriçerir.`https://login.microsoftonline.com/{tenant}/discovery/keys?appid=6731de76-14a6-49ae-97bc-6eba6914391e`

## <a name="send-the-sign-in-request"></a>Oturum açma isteğini gönder

Web uygulamanızın kimlik doğrulaması yapması gerektiğinde, kullanıcıyı `/authorize` uç noktaya yönlendirmelidir. Bu istek, bazı önemli ayrımlarla [OAuth 2,0 yetkilendirme kodu akışının](v1-protocols-oauth-code.md)ilk bamasına benzer:

* İstek, `openid` `scope` parametresine kapsamı içermelidir.
* `response_type` Parametrenin içermesi`id_token`gerekir.
* İstek, `nonce` parametresini içermelidir.

Bu nedenle bir örnek istek şöyle görünür:

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%3a12345
&response_mode=form_post
&scope=openid
&state=12345
&nonce=7362CAEA-9CA5-4B43-9BA3-34D7C303EBA7
```

| Parametre |  | Açıklama |
| --- | --- | --- |
| tenant |gerekli |İsteğin `{tenant}` yolundaki değeri, uygulamada kimlerin oturum açmasını denetlemek için kullanılabilir. İzin verilen değerler kiracı tanımlayıcılarıdır, örneğin `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` `common` veya `contoso.onmicrosoft.com` kiracı bağımsız belirteçler için |
| client_id |gerekli |Azure AD ile kaydettiğinizde uygulamanıza atanan uygulama KIMLIĞI. Bunu Azure portal bulabilirsiniz. **Azure Active Directory**' ye tıklayın, uygulama **kayıtları**' na tıklayın, uygulamayı seçin ve uygulama sayfasında uygulama kimliği ' ni bulun. |
| response_type |gerekli |OpenID Connect oturum açma içiniçermelidir.`id_token` Ayrıca, `code` veya `token`gibi diğer response_types de içerebilir. |
| scope | Önerilen | OpenID Connect belirtimi için, izin Kullanıcı `openid`arabirimindeki "oturum aç" iznine çeviren kapsam gerekir. Bu ve diğer OıDC kapsamları v 1.0 uç noktasında yok sayılır, ancak standartlarla uyumlu istemciler için en iyi uygulamadır. |
| nonce |gerekli |Talep olarak ortaya çıkan `id_token` , uygulama tarafından oluşturulan, istek içinde içerilen bir değer. Daha sonra uygulama, belirteç yeniden yürütme saldırılarını azaltmak için bu değeri doğrulayabilirler. Değer genellikle, isteğin kaynağını belirlemek için kullanılabilecek rastgele bir, benzersiz dize veya GUID 'dir. |
| redirect_uri | Önerilen |Uygulamanızın kimlik doğrulama yanıtlarının gönderilebileceği ve alınabileceği, uygulamanızın redirect_uri 'ı. Portalın, URL kodlamalı olması dışında, portala kaydettiğiniz redirect_uris biriyle tam olarak eşleşmesi gerekir. Eksik ise, Kullanıcı Aracısı, rastgele bir uygulama için kaydedilen yeniden yönlendirme URI 'lerinden birine geri gönderilir. Maksimum uzunluk 255 bayttır |
| response_mode |isteğe bağlı |Elde edilen authorization_code, uygulamanıza geri göndermek için kullanılması gereken yöntemi belirtir. `form_post` Desteklenen değerler *http form gönderi* ve `fragment` *URL parçası*içindir. Web uygulamaları için, belirteçlerin uygulamanıza `response_mode=form_post` en güvenli şekilde aktarılmasını sağlamak üzere kullanmanızı öneririz. İd_token dahil olmak üzere herhangi bir akış için varsayılan `fragment`değer.|
| state |Önerilen |Belirteç yanıtında döndürülen isteğe eklenen bir değer. Bu, istediğiniz herhangi bir içerik dizesi olabilir. Rastgele oluşturulan benzersiz bir değer genellikle [siteler arası istek sahteciliği saldırılarını önlemek](https://tools.ietf.org/html/rfc6749#section-10.12)için kullanılır. Durum Ayrıca, kullanıcının uygulamadaki durumu hakkında bilgi kodlamak için kullanılır; Örneğin, bulunan sayfa veya görünüm gibi kimlik doğrulama isteği gerçekleştirilmeden önce. |
| isteme |isteğe bağlı |Gerekli kullanıcı etkileşiminin türünü gösterir. Şu anda yalnızca geçerli değerler ' login', ' none ' ve ' onay ' değerleridir. `prompt=login`kullanıcıyı bu istek üzerine kimlik bilgilerini girmeye zorlar, tek oturum açma işlemini negatifler. `prompt=none`bunun tersidir, kullanıcının herhangi bir etkileşimli istem ile sunulmamasını sağlar. İstek, tek oturum açma yoluyla sessizce tamamlanamaz, uç nokta bir hata döndürür. `prompt=consent`Kullanıcı oturum açtıktan sonra, kullanıcıdan uygulamaya izin vermesini isteyen OAuth onay iletişim kutusunu tetikler. |
| login_hint |isteğe bağlı |Kullanıcı adının bir süre önce bilinerek Kullanıcı için oturum açma sayfasının Kullanıcı adı/e-posta adresi alanını önceden doldurmanız için kullanılabilir. Genellikle uygulamalar bu parametreyi yeniden kimlik doğrulama sırasında kullanır ve Kullanıcı adını, `preferred_username` talebi kullanarak önceki bir oturum açma işleminden zaten ayıklamış olur. |

Bu noktada, kullanıcıdan kimlik bilgilerini girmesi ve kimlik doğrulamasını tamamlaması istenir.

### <a name="sample-response"></a>Örnek yanıt

Kullanıcının kimliği doğrulandıktan sonra oturum açma isteğinde `redirect_uri` belirtilen ' A gönderilen örnek bir yanıt şuna benzeyebilir:

```
POST / HTTP/1.1
Host: localhost:12345
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| Parametre | Açıklama |
| --- | --- |
| id_token |`id_token` Uygulamanın istediği. Kullanıcının kimliğini doğrulamak ve `id_token` kullanıcıyla oturum başlatmak için öğesini kullanabilirsiniz. |
| state |İstekte bulunan, belirteç yanıtında de döndürülen bir değer. Rastgele oluşturulan benzersiz bir değer genellikle [siteler arası istek sahteciliği saldırılarını önlemek](https://tools.ietf.org/html/rfc6749#section-10.12)için kullanılır. Durum Ayrıca, kullanıcının uygulamadaki durumu hakkında bilgi kodlamak için kullanılır; Örneğin, bulunan sayfa veya görünüm gibi kimlik doğrulama isteği gerçekleştirilmeden önce. |

### <a name="error-response"></a>Hata yanıtı

Ayrıca, `redirect_uri` uygulamanın bunları uygun şekilde işleyebilmesi için hata yanıtları da gönderilebilir.

```
POST / HTTP/1.1
Host: localhost:12345
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parametre | Açıklama |
| --- | --- |
| hata |Oluşan hata türlerini sınıflandırmak için kullanılabilen ve hatalara yanıt vermek için kullanılabilen bir hata kodu dizesi. |
| error_description |Bir geliştiricinin kimlik doğrulama hatasının kök nedenini belirlemesine yardımcı olabilecek belirli bir hata iletisi. |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Yetkilendirme uç noktası hataları için hata kodları

Aşağıdaki tabloda, hata yanıtının `error` parametresinde döndürülebilecek çeşitli hata kodları açıklanmaktadır.

| Hata Kodu | Açıklama | İstemci eylemi |
| --- | --- | --- |
| invalid_request |Eksik gerekli bir parametre gibi protokol hatası. |İsteği onarın ve yeniden gönderin. Bu bir geliştirme hatasıdır ve genellikle ilk test sırasında yakalanır. |
| unauthorized_client |İstemci uygulamasının bir yetkilendirme kodu istemesine izin verilmiyor. |Bu durum genellikle istemci uygulaması Azure AD 'ye kaydedilmediğinde veya kullanıcının Azure AD kiracısına eklenmediğinde oluşur. Uygulama kullanıcıya uygulamayı yükleme ve Azure AD 'ye ekleme yönergesini isteyebilir. |
| access_denied |Kaynak sahibi reddedildi onayı |İstemci uygulaması, kullanıcıya Kullanıcı tarafından bağlanmadığı takdirde devam edemediği konusunda bildirimde bulunabilir. |
| unsupported_response_type |Yetkilendirme sunucusu istekteki yanıt türünü desteklemiyor. |İsteği onarın ve yeniden gönderin. Bu bir geliştirme hatasıdır ve genellikle ilk test sırasında yakalanır. |
| server_error |Sunucu beklenmeyen bir hatayla karşılaştı. |İsteği yeniden deneyin. Bu hatalar geçici koşullardan kaynaklanabilir. İstemci uygulaması, isteği geçici bir hata nedeniyle geciktirildiği kullanıcıya açıklayabilir. |
| temporarily_unavailable |Sunucu, isteği işlemek için geçici olarak çok meşgul. |İsteği yeniden deneyin. İstemci uygulaması, yanıtı, geçici bir durum nedeniyle geciktiğinde kullanıcıya açıklayabilir. |
| invalid_resource |Hedef kaynak geçersiz, çünkü mevcut değil, Azure AD bu dosyayı bulamıyor veya doğru şekilde yapılandırılmamış. |Bu, varsa kaynağın kiracıda yapılandırılmamış olduğunu gösterir. Uygulama kullanıcıya uygulamayı yükleme ve Azure AD 'ye ekleme yönergesini isteyebilir. |

## <a name="validate-the-id_token"></a>İd_token doğrulama

Yalnızca bir `id_token` Kullanıcı kimliğini doğrulamak yeterli değildir; imzayı doğrulamanız ve uygulamanızın gereksinimlerine `id_token` göre talepleri doğrulamanız gerekir. Azure AD uç noktası belirteçleri imzalamak ve bunların geçerli olduğunu doğrulamak için JSON Web belirteçleri (JWTs) ve ortak anahtar şifrelemesi kullanır.

İstemci kodunda doğrulamayı `id_token` seçebilirsiniz, ancak bunu bir arka uç sunucusuna `id_token` göndermek ve doğrulama gerçekleştirmek için sık kullanılan bir uygulamadır. 

Senaryonuza bağlı olarak ek talepler de doğrulamak isteyebilirsiniz. Bazı ortak doğrulamalar şunları içerir:

* Kullanıcı/kuruluşun uygulama için kaydolmasını sağlama.
* Kullanıcının `wids` veya`roles` taleplerini kullanarak uygun yetkilere/ayrıcalıklara sahip olduğundan emin olma. 
* Çok faktörlü kimlik doğrulaması gibi belirli bir kimlik doğrulaması kuvvetinin gerçekleştiği doğrulanıyor.

`id_token`' İ doğrulandıktan sonra, Kullanıcı ile oturum başlatabilir ve uygulamanızdaki Kullanıcı hakkında bilgi edinmek `id_token` için içindeki talepleri kullanabilirsiniz. Bu bilgiler, görüntüleme, kayıtlar, kişiselleştirme vb. için kullanılabilir. Ve talepleri hakkında `id_tokens` daha fazla bilgi için [AAD id_tokens](id-tokens.md)makalesini okuyun.

## <a name="send-a-sign-out-request"></a>Oturum kapatma isteği gönder

Uygulamayı uygulamanın dışına imzalamak istediğinizde, uygulamanızın tanımlama bilgilerini temizlemek veya oturumu Kullanıcı ile sonlandırmak yeterli değildir. Oturumu açmak `end_session_endpoint` için kullanıcıyı da ' a yönlendirmeniz gerekir. Bunu yapmazsanız, Kullanıcı Azure AD uç noktası ile geçerli bir çoklu oturum açma oturumu açacağından, kimlik bilgilerini tekrar girmeden uygulamanız için yeniden kimlik doğrulaması yapabilecektir.

Kullanıcıyı OpenID Connect meta verileri belgesinde `end_session_endpoint` listelenen öğesine yönlendirebilirsiniz.

```
GET https://login.microsoftonline.com/common/oauth2/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F

```

| Parametre |  | Açıklama |
| --- | --- | --- |
| post_logout_redirect_uri |Önerilen |Başarılı oturum kapatıldıktan sonra kullanıcının yeniden yönlendirilmesi gereken URL.  Bu URL, uygulama kayıt portalı 'nda uygulamanız için kayıtlı olan yeniden yönlendirme URI 'lerinden biriyle aynı olmalıdır.  *Post_logout_redirect_uri* dahil edilmezse, kullanıcıya genel bir ileti gösterilir. |

## <a name="single-sign-out"></a>Çoklu oturum kapatma

Kullanıcıyı uygulamasına `end_session_endpoint`yönlendirirseniz, Azure AD kullanıcının oturumunu tarayıcıdan temizler. Ancak Kullanıcı, kimlik doğrulaması için Azure AD kullanan diğer uygulamalarda oturum açmış olabilir. Bu uygulamaların kullanıcıyı aynı anda imzalamasını sağlamak için Azure AD, kullanıcının şu anda oturum açmış olduğu tüm uygulamalara kayıtlı `LogoutUrl` bir http get isteği gönderir. Uygulamalar, kullanıcıyı tanıtan ve `200` yanıt döndüren tüm oturumları temizleyerek bu isteğe yanıt vermelidir. Uygulamanızda çoklu oturum açmayı desteklemek istiyorsanız, uygulamanızın kodunda böyle bir `LogoutUrl` tür uygulamanız gerekir. Öğesini Azure Portal ayarlayabilirsiniz `LogoutUrl` :

1. [Azure portalına](https://portal.azure.com) gidin.
2. Sayfanın sağ üst köşesindeki hesabınıza tıklayarak Active Directory seçin.
3. Sol taraftaki Gezinti panelinden **Azure Active Directory**' yi seçin ve sonra **uygulama kayıtları** ' i seçin ve uygulamanızı seçin.
4. **Ayarlar**' a ve ardından **Özellikler** ' e tıklayın ve **oturum kapatma URL 'si** metin kutusunu bulun. 

## <a name="token-acquisition"></a>Belirteç alımı
Birçok Web uygulamasının ' de kullanıcının oturumu açması gerekmez, ancak aynı zamanda OAuth kullanarak bu kullanıcı adına bir Web hizmetine erişir. Bu senaryo, `authorization_code` [OAuth yetkilendirme kodu akışını](v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token) `access_tokens` kullanmak için kullanılabilecek aynı anda bir veri alırken, OpenID Connect 'i Kullanıcı kimlik doğrulaması için birleştirir.

## <a name="get-access-tokens"></a>Erişim belirteçleri al
Erişim belirteçleri almak için, yukarıdaki oturum açma isteğini değiştirmeniz gerekir:

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e        // Your registered Application ID
&response_type=id_token+code
&redirect_uri=http%3A%2F%2Flocalhost%3a12345          // Your registered Redirect Uri, url encoded
&response_mode=form_post                              // `form_post' or 'fragment'
&scope=openid
&resource=https%3A%2F%2Fservice.contoso.com%2F        // The identifier of the protected resource (web API) that your application needs access to
&state=12345                                          // Any value, provided by your app
&nonce=678910                                         // Any value, provided by your app
```

İstek kapsamlarını istekte ve kullanarak `response_type=code+id_token`ekleyerek `authorize` , uç nokta kullanıcının `scope` sorgu parametresinde belirtilen izinlere onay almasını ve uygulamanızı Exchange 'e yönelik bir yetkilendirme kodu döndürmesini sağlar erişim belirteci.

### <a name="successful-response"></a>Başarılı yanıt

`redirect_uri` Kullanılarak`response_mode=form_post`gönderilen başarılı bir yanıt, şöyle görünür:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&state=12345
```

| Parametre | Açıklama |
| --- | --- |
| id_token |`id_token` Uygulamanın istediği. Kullanıcının kimliğini doğrulamak ve `id_token` kullanıcıyla oturum başlatmak için öğesini kullanabilirsiniz. |
| code |Uygulamanın istediği authorization_code. Uygulama, hedef kaynak için bir erişim belirteci istemek üzere yetkilendirme kodunu kullanabilir. Authorization_codes kısa süreli ve genellikle yaklaşık 10 dakika sonra sona erer. |
| state |İsteğe bir durum parametresi dahil edilir, yanıtta aynı değer görünmelidir. Uygulama, istek ve yanıtta durum değerlerinin özdeş olduğunu doğrulamalıdır. |

### <a name="error-response"></a>Hata yanıtı

Ayrıca, `redirect_uri` uygulamanın bunları uygun şekilde işleyebilmesi için hata yanıtları da gönderilebilir.

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parametre | Açıklama |
| --- | --- |
| hata |Oluşan hata türlerini sınıflandırmak için kullanılabilen ve hatalara yanıt vermek için kullanılabilen bir hata kodu dizesi. |
| error_description |Bir geliştiricinin kimlik doğrulama hatasının kök nedenini belirlemesine yardımcı olabilecek belirli bir hata iletisi. |

Olası hata kodlarının ve bunların önerilen istemci eyleminin açıklaması için bkz. [Yetkilendirme uç noktası hataları Için hata kodları](#error-codes-for-authorization-endpoint-errors).

Bir yetkilendirmeyi `code` ve bir `id_token`kez aldıktan sonra, ' de kullanıcıyı imzalayabilir ve adına [erişim belirteçleri](access-tokens.md) alabilirsiniz. Kullanıcıyı ' de imzalamak için yukarıda açıklanan şekilde `id_token` tam olarak doğrulamanız gerekir. Erişim belirteçleri almak için, [OAuth kod akışı belgelerimizin](v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token)"bir erişim belirteci istemek için yetkilendirme kodunu kullanma" bölümünde açıklanan adımları izleyebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

* [Erişim belirteçleri](access-tokens.md)hakkında daha fazla bilgi edinin.
* [ `id_token` Ve talepleri](id-tokens.md)hakkında daha fazla bilgi edinin.
