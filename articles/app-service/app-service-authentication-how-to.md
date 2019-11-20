---
title: Kimlik doğrulama ve yetkilendirme gelişmiş kullanımı-Azure App Service | Microsoft Docs
description: App Service kimlik doğrulaması ve yetkilendirmeyi özelleştirmeyi ve Kullanıcı taleplerini ve farklı belirteçleri almayı gösterir.
services: app-service
documentationcenter: ''
author: cephalin
manager: gwallace
editor: ''
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/24/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: f453a0276a3448273964a589112e21ca5665c2d2
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900129"
---
# <a name="advanced-usage-of-authentication-and-authorization-in-azure-app-service"></a>Azure App Service 'da gelişmiş kimlik doğrulama ve yetkilendirme kullanımı

Bu makalede, [App Service ' de yerleşik kimlik doğrulama ve yetkilendirmeyi](overview-authentication-authorization.md)nasıl özelleştireceğinizi ve uygulamanızdan kimlik yönetimi açıklanmaktadır. 

Hızlıca kullanmaya başlamak için aşağıdaki öğreticilerden birine bakın:

* [Öğretici: kullanıcıların Azure App Service (Windows) ile uçtan uca kimlik doğrulama ve yetkilendirme](app-service-web-tutorial-auth-aad.md)
* [Öğretici: Linux için Azure App Service Kullanıcı tarafından uçtan uca kimlik doğrulama ve yetkilendirme](containers/tutorial-auth-aad.md)
* [Uygulamanızı Azure Active Directory oturum açma bilgilerini kullanacak şekilde yapılandırma](configure-authentication-provider-aad.md)
* [Uygulamanızı Facebook oturum açma bilgilerini kullanacak şekilde yapılandırma](configure-authentication-provider-facebook.md)
* [Uygulamanızı Google oturum açma bilgilerini kullanacak şekilde yapılandırma](configure-authentication-provider-google.md)
* [Uygulamanızı Microsoft Hesabı oturum açma bilgilerini kullanacak şekilde yapılandırma](configure-authentication-provider-microsoft.md)
* [Uygulamanızı Twitter oturum açma bilgilerini kullanacak şekilde yapılandırma](configure-authentication-provider-twitter.md)

## <a name="use-multiple-sign-in-providers"></a>Çoklu oturum açma sağlayıcılarını kullanma

Portal Yapılandırması, kullanıcılarınıza birden çok oturum açma sağlayıcısı (Facebook ve Twitter gibi) sunmak için bir anahtar açma yöntemi sunmaz. Ancak, işlevselliği uygulamanıza eklemek zor değildir. Adımlar aşağıdaki şekilde özetlenmiştir:

İlk olarak, Azure portal **kimlik doğrulama/yetkilendirme** sayfasında, etkinleştirmek istediğiniz her bir kimlik sağlayıcısını yapılandırın.

**İsteğin kimliği doğrulanmamış olduğunda gerçekleştirilecek eylem Için** **anonim isteklere izin ver (eylem yok)** seçeneğini belirleyin.

Oturum açma sayfasında veya gezinti çubuğunda veya uygulamanızın herhangi bir yerinde, etkinleştirdiğiniz sağlayıcıların her birine bir oturum açma bağlantısı ekleyin (`/.auth/login/<provider>`). Örnek:

```HTML
<a href="/.auth/login/aad">Log in with Azure AD</a>
<a href="/.auth/login/microsoftaccount">Log in with Microsoft Account</a>
<a href="/.auth/login/facebook">Log in with Facebook</a>
<a href="/.auth/login/google">Log in with Google</a>
<a href="/.auth/login/twitter">Log in with Twitter</a>
```

Kullanıcı bağlantılardan birine tıkladığında, kullanıcının oturum açması için ilgili oturum açma sayfası açılır.

Kullanıcı oturum açma sonrası eklentisini özel bir URL 'ye yönlendirmek için, `post_login_redirect_url` sorgu dizesi parametresini kullanın (kimlik sağlayıcısı yapılandırmanızda yeniden yönlendirme URI 'SI ile karıştırılmamalıdır). Örneğin, oturum açtıktan sonra Kullanıcı `/Home/Index` gezinmek için aşağıdaki HTML kodunu kullanın:

```HTML
<a href="/.auth/login/<provider>?post_login_redirect_url=/Home/Index">Log in</a>
```

## <a name="validate-tokens-from-providers"></a>Sağlayıcılardan belirteçleri doğrula

İstemci ile yönlendirilen bir oturum açma bölümünde, uygulama kullanıcıdan sağlayıcıya el ile oturum açar ve ardından kimlik doğrulama belirtecini doğrulama için App Service (bkz. [kimlik doğrulama akışı](overview-authentication-authorization.md#authentication-flow)) gönderir. Bu doğrulamanın kendisi, istenen uygulama kaynaklarına erişim hakkı vermez, ancak başarılı bir doğrulama size uygulama kaynaklarına erişmek için kullanabileceğiniz bir oturum belirteci verecektir. 

Sağlayıcı belirtecini doğrulamak için App Service uygulamasının öncelikle istenen sağlayıcıyla yapılandırılması gerekir. Çalışma zamanında, sağlayıcınızdan kimlik doğrulama belirtecini aldıktan sonra, doğrulama için `/.auth/login/<provider>` belirteç gönderin. Örnek: 

```
POST https://<appname>.azurewebsites.net/.auth/login/aad HTTP/1.1
Content-Type: application/json

{"id_token":"<token>","access_token":"<token>"}
```

Belirteç biçimi sağlayıcıya göre biraz farklılık gösterir. Ayrıntılar için aşağıdaki tabloya bakın:

| Sağlayıcı değeri | İstek gövdesinde gerekli | Yorumlar |
|-|-|-|
| `aad` | `{"access_token":"<access_token>"}` | |
| `microsoftaccount` | `{"access_token":"<token>"}` | `expires_in` özelliği isteğe bağlıdır. <br/>Canlı hizmetlerden belirteç istenirken `wl.basic` kapsamını her zaman isteyin. |
| `google` | `{"id_token":"<id_token>"}` | `authorization_code` özelliği isteğe bağlıdır. Belirtildiğinde, isteğe bağlı olarak `redirect_uri` özelliği de olabilir. |
| `facebook`| `{"access_token":"<user_access_token>"}` | Facebook 'tan geçerli bir [Kullanıcı erişim belirteci](https://developers.facebook.com/docs/facebook-login/access-tokens) kullanın. |
| `twitter` | `{"access_token":"<access_token>", "access_token_secret":"<acces_token_secret>"}` | |
| | | |

Sağlayıcı belirteci başarıyla doğrulandıktan sonra, API, yanıt gövdesinde, oturum belirteciniz olan bir `authenticationToken` döndürür. 

```json
{
    "authenticationToken": "...",
    "user": {
        "userId": "sid:..."
    }
}
```

Bu oturum belirtecine sahip olduğunuzda, HTTP isteklerinize `X-ZUMO-AUTH` üst bilgisini ekleyerek korumalı uygulama kaynaklarına erişebilirsiniz. Örnek: 

```
GET https://<appname>.azurewebsites.net/api/products/1
X-ZUMO-AUTH: <authenticationToken_value>
```

## <a name="sign-out-of-a-session"></a>Oturum kapatma

Kullanıcılar, uygulamanın `/.auth/logout` uç noktasına `GET` isteği göndererek oturumu kapatma işlemini başlatabilir. `GET` isteği aşağıdakileri yapar:

- Geçerli oturumdan kimlik doğrulama tanımlama bilgilerini temizler.
- Geçerli kullanıcının belirteçlerini belirteç deposundan siler.
- Azure Active Directory ve Google için, kimlik sağlayıcısında sunucu tarafında oturum kapatma gerçekleştirir.

Bir Web sayfasındaki basit bir oturum açma bağlantısı aşağıda verilmiştir:

```HTML
<a href="/.auth/logout">Sign out</a>
```

Varsayılan olarak, başarılı bir oturum kapatma istemciyi URL 'ye yeniden yönlendirir `/.auth/logout/done`. `post_logout_redirect_uri` sorgu parametresini ekleyerek, oturum kapatma sonrası yeniden yönlendirme sayfasını değiştirebilirsiniz. Örnek:

```
GET /.auth/logout?post_logout_redirect_uri=/index.html
```

`post_logout_redirect_uri`değerini [kodlamanız](https://wikipedia.org/wiki/Percent-encoding) önerilir.

Tam nitelikli URL 'Ler kullanılırken, URL aynı etki alanında barındırılıyor ya da uygulamanız için izin verilen bir dış yeniden yönlendirme URL 'SI olarak yapılandırılmış olmalıdır. Aşağıdaki örnekte, aynı etki alanında barındırılmayan `https://myexternalurl.com` yeniden yönlendirmek için:

```
GET /.auth/logout?post_logout_redirect_uri=https%3A%2F%2Fmyexternalurl.com
```

[Azure Cloud Shell](../cloud-shell/quickstart.md)aşağıdaki komutu çalıştırın:

```azurecli-interactive
az webapp auth update --name <app_name> --resource-group <group_name> --allowed-external-redirect-urls "https://myexternalurl.com"
```

## <a name="preserve-url-fragments"></a>URL parçalarını koruma

Kullanıcılar uygulamanızda oturum açtıktan sonra, bu, genellikle `/wiki/Main_Page#SectionZ`gibi aynı sayfanın aynı bölümüne yönlendirilmek ister. Bununla birlikte, [URL parçaları](https://wikipedia.org/wiki/Fragment_identifier) (örneğin, `#SectionZ`) sunucuya hiçbir şekilde gönderilmediğinden, OAuth oturum açma tamamlandıktan sonra varsayılan olarak korunmaz ve uygulamanıza yeniden yönlendirilir. Kullanıcılar daha sonra istediğiniz yere gitmek gerektiğinde bir daha iyi deneyim yaşar. Bu sınırlama, tüm sunucu tarafı kimlik doğrulama çözümleri için geçerlidir.

App Service kimlik doğrulaması ' nda, OAuth oturum açma genelinde URL parçalarını koruyabilirsiniz. Bunu yapmak için `WEBSITE_AUTH_PRESERVE_URL_FRAGMENT` adlı bir uygulama ayarı `true`olarak ayarlayın. Bunu [Azure Portal](https://portal.azure.com)yapabilir veya [Azure Cloud Shell](../cloud-shell/quickstart.md)yalnızca aşağıdaki komutu çalıştırabilirsiniz:

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group <group_name> --settings WEBSITE_AUTH_PRESERVE_URL_FRAGMENT="true"
```

## <a name="access-user-claims"></a>Kullanıcı taleplerine erişin

App Service, özel üstbilgiler kullanarak Kullanıcı taleplerini uygulamanıza geçirir. Dış isteklerin bu üst bilgileri ayarlama izni yoktur, bu nedenle yalnızca App Service tarafından ayarlandıysa mevcut olmaları gerekir. Bazı örnek üstbilgileri şunlardır:

* X-MS-CLIENT-PRINCIPAL-NAME
* X-MS-CLIENT-PRINCIPAL-ıD

Herhangi bir dilde veya çerçevede yazılan kod, bu üst bilgilerden ihtiyaç duymakta olan bilgileri alabilir. ASP.NET 4,6 uygulamaları için, **ClaimsPrincipal** otomatik olarak uygun değerlerle ayarlanır. Ancak ASP.NET Core, App Service Kullanıcı taleplerini tümleştiren bir kimlik doğrulama ara yazılımı sağlamaz. Geçici bir çözüm için bkz. [Maximerouiller. Azure. AppService. EasyAuth](https://github.com/MaximRouiller/MaximeRouiller.Azure.AppService.EasyAuth).

Uygulamanız Ayrıca `/.auth/me`çağırarak kimliği doğrulanmış kullanıcı hakkında ek ayrıntılar elde edebilir. Mobile Apps Server SDK 'Ları, bu verilerle çalışmak için yardımcı yöntemler sağlar. Daha fazla bilgi için bkz. [azure Mobile Apps Node. js SDK 'sını kullanma](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-getidentity)ve [Azure Mobile Apps için .net arka uç sunucu SDK 'sı ile çalışma](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#user-info).

## <a name="retrieve-tokens-in-app-code"></a>Uygulama kodundaki belirteçleri al

Sunucu kodunuzda sağlayıcıya özgü belirteçler istek üstbilgisine eklenir, bu sayede kolayca erişebilirsiniz. Aşağıdaki tabloda olası belirteç üstbilgisi adları gösterilmektedir:

| Sağlayıcı | Üst bilgi adları |
|-|-|
| Azure Active Directory | `X-MS-TOKEN-AAD-ID-TOKEN` <br/> `X-MS-TOKEN-AAD-ACCESS-TOKEN` <br/> `X-MS-TOKEN-AAD-EXPIRES-ON`  <br/> `X-MS-TOKEN-AAD-REFRESH-TOKEN` |
| Facebook belirteci | `X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN` <br/> `X-MS-TOKEN-FACEBOOK-EXPIRES-ON` |
| Google | `X-MS-TOKEN-GOOGLE-ID-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-ACCESS-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-EXPIRES-ON` <br/> `X-MS-TOKEN-GOOGLE-REFRESH-TOKEN` |
| Microsoft Hesabı | `X-MS-TOKEN-MICROSOFTACCOUNT-ACCESS-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-EXPIRES-ON` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-AUTHENTICATION-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-REFRESH-TOKEN` |
| Twitter | `X-MS-TOKEN-TWITTER-ACCESS-TOKEN` <br/> `X-MS-TOKEN-TWITTER-ACCESS-TOKEN-SECRET` |
|||

İstemci kodınızdan (bir mobil uygulama veya tarayıcı içi JavaScript gibi), `/.auth/me`için bir HTTP `GET` isteği gönderin. Döndürülen JSON, sağlayıcıya özgü belirteçlere sahiptir.

> [!NOTE]
> Erişim belirteçleri sağlayıcı kaynaklarına erişmek için kullanılır, bu nedenle yalnızca sağlayıcınızı bir istemci gizli anahtarı ile yapılandırırsanız vardır. Yenileme belirteçlerinin nasıl alınacağını görmek için bkz. erişim belirteçlerini yenileme.

## <a name="refresh-identity-provider-tokens"></a>Kimlik sağlayıcısı belirteçlerini Yenile

Sağlayıcınızın erişim belirtecinin ( [oturum belirteci](#extend-session-token-expiration-grace-period)değil) süresi dolmuşsa, bu belirteci yeniden kullanmadan önce kullanıcıyı yeniden kimlik doğrulaması yapmanız gerekir. Uygulamanızın `/.auth/refresh` uç noktasına `GET` çağrısı yaparak belirteç süre sonundan kaçınabilirsiniz. Çağrıldığında, App Service kimliği doğrulanmış kullanıcı için belirteç deposundaki erişim belirteçlerini otomatik olarak yeniler. Uygulama kodunuzun belirteçleri için sonraki istekleri yenilenen belirteçleri alır. Ancak, belirteç yenilemenin çalışması için, belirteç deposu sağlayıcınız için [yenileme belirteçleri](https://auth0.com/learn/refresh-tokens/) içermelidir. Yenileme belirteçlerini almanın yöntemi her sağlayıcı tarafından belgelenmiştir, ancak aşağıdaki liste kısa bir özettir:

- **Google**: `/.auth/login/google` API çağra `access_type=offline` bir sorgu dizesi parametresi ekleyin. Mobile Apps SDK kullanıyorsanız, parametresini `LogicAsync` aşırı yüklerden birine ekleyebilirsiniz (bkz. [Google Refresh belirteçleri](https://developers.google.com/identity/protocols/OpenIDConnect#refresh-tokens)).
- **Facebook**: yenileme belirteçleri sağlamıyor. Uzun süreli belirteçlerin süresi 60 gün içinde doluyor (bkz. [Facebook süre sonu ve erişim belirteçleri uzantısı](https://developers.facebook.com/docs/facebook-login/access-tokens/expiration-and-extension)).
- **Twitter**: erişim belirteçlerinin süre sonu yok (bkz. [Twitter OAuth SSS](https://developer.twitter.com/en/docs/basics/authentication/FAQ)).
- **Microsoft hesabı**: [Microsoft hesabı kimlik doğrulaması ayarlarını yapılandırırken](configure-authentication-provider-microsoft.md)`wl.offline_access` kapsamını seçin.
- **Azure Active Directory**: [https://resources.azure.com](https://resources.azure.com)aşağıdaki adımları uygulayın:
    1. Sayfanın üst kısmında **oku/yaz**' ı seçin.
    2. Sol tarayıcıda > **abonelikler** ' e gidin **_\<abonelik\_ad_**  > **ResourceGroups** > \< **_kaynak\_grubu\_adı >_** ** >  > sağlayıcılar** , **Microsoft. Web** > **siteleri** > \_**config** > **authsettings öğesine tıklayın** >  **_\<App > Name_** . 
    3. **Düzenle**’ye tıklayın.
    4. Aşağıdaki özelliği değiştirin. _\<app\_ıd >_ , erişmek istediğiniz hizmetin Azure ACTIVE DIRECTORY uygulama kimliğiyle değiştirin.

        ```json
        "additionalLoginParams": ["response_type=code id_token", "resource=<app_id>"]
        ```

    5. **Yerleştir**' e tıklayın. 

Sağlayıcınız yapılandırıldıktan sonra, belirteç deposundaki [erişim belirtecinin yenileme belirtecini ve sona erme zamanını bulabilirsiniz](#retrieve-tokens-in-app-code) . 

Erişim belirtecinizi dilediğiniz zaman yenilemek için `/.auth/refresh` her dilde çağırmanız yeterlidir. Aşağıdaki kod parçacığı bir JavaScript istemcisinden erişim belirteçlerinizi yenilemek için jQuery kullanır.

```JavaScript
function refreshTokens() {
  let refreshUrl = "/.auth/refresh";
  $.ajax(refreshUrl) .done(function() {
    console.log("Token refresh completed successfully.");
  }) .fail(function() {
    console.log("Token refresh failed. See application logs for details.");
  });
}
```

Bir Kullanıcı uygulamanıza verilen izinleri iptal eder, `/.auth/me` çağrısı `403 Forbidden` bir yanıt vererek başarısız olabilir. Hataları tanılamak için, Ayrıntılar için uygulama günlüklerinizi denetleyin.

## <a name="extend-session-token-expiration-grace-period"></a>Oturum belirteci süre sonu yetkisiz kullanım süresini uzat

Kimliği doğrulanmış oturumun süresi 8 saat sonra dolar. Kimliği doğrulanmış bir oturumun süresi dolduktan sonra, varsayılan olarak 72 saatlik bir yetkisiz kullanım süresi vardır. Bu yetkisiz kullanım süresi içinde, Kullanıcı yeniden kimlik doğrulaması yapmadan oturum belirtecini App Service yenilemeye izin verilir. Yalnızca oturum belirteciniz geçersiz hale geldiğinde `/.auth/refresh` çağırabilir ve belirtecin süre sonunu kendiniz izlemeniz gerekmez. 72 saatlik yetkisiz kullanım süresi eşitlendikten sonra, Kullanıcı geçerli bir oturum belirteci almak için yeniden oturum açması gerekir.

Bu süre boyunca 72 saat yeterli değilse, bu süre sonu penceresini genişletebilirsiniz. Süre sonunu uzun bir süre boyunca uzatmak önemli güvenlik etkilerine (örneğin, bir kimlik doğrulama belirtecinin sızma veya çalındığı zaman) sahip olabilir. Bu nedenle, bunu varsayılan 72 saat olarak bırakmanız veya uzantı süresini en küçük değere ayarlamanız gerekir.

Varsayılan süre sonu penceresini genişletmek için [Cloud Shell](../cloud-shell/overview.md)aşağıdaki komutu çalıştırın.

```azurecli-interactive
az webapp auth update --resource-group <group_name> --name <app_name> --token-refresh-extension-hours <hours>
```

> [!NOTE]
> Yetkisiz kullanım süresi, kimlik sağlayıcılarındaki belirteçleri değil, yalnızca App Service kimliği doğrulanmış oturum için geçerlidir. Süresi geçen sağlayıcı belirteçleri için yetkisiz kullanım süresi yoktur. 
>

## <a name="limit-the-domain-of-sign-in-accounts"></a>Oturum açma hesaplarının etki alanını sınırlayın

Hem Microsoft hesabı hem de Azure Active Directory birden çok etki alanından oturum açmanızı sağlar. Örneğin, Microsoft hesabı _Outlook.com_, _Live.com_ve _hotmail.com_ hesaplarına izin verir. Azure AD, oturum açma hesapları için herhangi bir sayıda özel etki alanı sağlar. Ancak, kullanıcılarınızı kendi markalı Azure AD oturum açma sayfanız (örneğin, `contoso.com`) için doğrudan hızlandırmayı isteyebilirsiniz. Oturum açma hesaplarının etki alanı adını önermek için aşağıdaki adımları izleyin.

[https://resources.azure.com](https://resources.azure.com)' de, **abonelikler** >  **_\< abonelik\_ adı_**  > **ResourceGroups** > \< **_kaynak\_ grubu\_ adı_** ** >  >  sağlayıcılar** , **Microsoft. Web** > **siteleri** > \_**config** > **authsettings öğesine tıklayın** >  **_\< App >  Name_** . 

**Düzenle**' ye tıklayın, aşağıdaki özelliği değiştirin ve ardından **Yerleştir**' e tıklayın. _\<etki alanı\_adı >_ istediğiniz etki alanıyla değiştirdiğinizden emin olun.

```json
"additionalLoginParams": ["domain_hint=<domain_name>"]
```

Bu ayar, `domain_hint` sorgu dizesi parametresini, oturum açma yeniden yönlendirme URL 'sine ekler. 

> [!IMPORTANT]
> İstemcinin yeniden yönlendirme URL 'sini aldıktan sonra `domain_hint` parametresini kaldırması ve sonra farklı bir etki alanı ile oturum açması mümkündür. Bu işlev kullanışlı olsa da, bir güvenlik özelliği değildir.
>

## <a name="authorize-or-deny-users"></a>Kullanıcılara yetki verme veya reddetme

App Service en basit yetkilendirme durumu (yani, kimliği doğrulanmamış istekleri reddetme) ile ilgilenirken, uygulamanız yalnızca belirli bir kullanıcı grubuyla erişimi sınırlandırma gibi daha ayrıntılı yetkilendirme davranışı gerektirebilir. Belirli durumlarda, oturum açmış kullanıcıya izin vermek veya erişimi reddetmek için özel uygulama kodu yazmanız gerekir. Diğer durumlarda, App Service veya kimlik sağlayıcınız kod değişikliği gerektirmeden yardım edebilir.

- [Sunucu düzeyi](#server-level-windows-apps-only)
- [Kimlik sağlayıcısı düzeyi](#identity-provider-level)
- [Uygulama düzeyi](#application-level)

### <a name="server-level-windows-apps-only"></a>Sunucu düzeyi (yalnızca Windows uygulamaları)

Herhangi bir Windows uygulaması için, *Web. config* dosyasını düzenleyerek IIS Web sunucusunun yetkilendirme davranışını tanımlayabilirsiniz. Linux uygulamaları IIS kullanmaz ve *Web. config*üzerinden yapılandırılamaz.

1. `https://<app-name>.scm.azurewebsites.net/DebugConsole` git

1. App Service dosyalarınızın tarayıcı Gezgini ' nde, *site/Wwwroot ' ya*gidin. Bir *Web. config* yoksa, **+**  > **yeni dosya**' yı seçerek oluşturun. 

1. *Web. config dosyasını* düzenlemek için kurşun kalem ' i seçin. Aşağıdaki yapılandırma kodunu ekleyin ve **Kaydet**' e tıklayın. *Web. config* zaten mevcutsa, `<authorization>` öğesini içindeki her şeyi eklemeniz yeterlidir. `<allow>` öğesinde izin vermek istediğiniz hesapları ekleyin.

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <configuration>
       <system.web>
          <authorization>
            <allow users="user1@contoso.com,user2@contoso.com"/>
            <deny users="*"/>
          </authorization>
       </system.web>
    </configuration>
    ```

### <a name="identity-provider-level"></a>Kimlik sağlayıcısı düzeyi

Kimlik sağlayıcısı, belirli bir anahtar yetkilendirme sağlayabilir. Örnek:

- [Azure App Service](configure-authentication-provider-aad.md)için, [Kurumsal düzeyde ERIŞIMI](../active-directory/manage-apps/what-is-access-management.md) doğrudan Azure AD 'de yönetebilirsiniz. Yönergeler için bkz. [kullanıcının bir uygulamaya erişimini kaldırma](../active-directory/manage-apps/methods-for-removing-user-access.md).
- [Google](configure-authentication-provider-google.md)için, bir [kuruluşa](https://cloud.google.com/resource-manager/docs/cloud-platform-resource-hierarchy#organizations) ait Google API projeleri yalnızca kuruluşunuzdaki kullanıcılara erişime izin verecek şekilde yapılandırılabilir (bkz. [Google 'ın **OAuth 2,0** destek sayfasını ayarlama](https://support.google.com/cloud/answer/6158849?hl=en)).

### <a name="application-level"></a>Uygulama düzeyi

Diğer düzeylerin herhangi biri ihtiyacınız olan yetkilendirmeyi sağlamıyorsa veya platformunuz veya kimlik sağlayıcınız desteklenmiyorsa, [Kullanıcı taleplerine](#access-user-claims)göre kullanıcılara yetki vermek için özel kod yazmanız gerekir.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Öğretici: kullanıcıların uçtan uca (Windows) kimlik doğrulaması ve yetkilendirme](app-service-web-tutorial-auth-aad.md)
> [öğreticisi: kullanıcılara kimlik doğrulama ve yetkilendirme (Linux)](containers/tutorial-auth-aad.md)
