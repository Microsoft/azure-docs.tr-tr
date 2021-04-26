---
title: AuthN/AuthZ gelişmiş kullanımı
description: Farklı senaryolar için App Service kimlik doğrulaması ve yetkilendirme özelliğini özelleştirmeyi ve Kullanıcı taleplerini ve farklı belirteçleri almayı öğrenin.
ms.topic: article
ms.date: 03/29/2021
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 9335bb62e494fab50f7beadf3d7bbc423d80cf14
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775736"
---
# <a name="advanced-usage-of-authentication-and-authorization-in-azure-app-service"></a>Azure App Service 'da gelişmiş kimlik doğrulama ve yetkilendirme kullanımı

Bu makalede, [App Service ' de yerleşik kimlik doğrulama ve yetkilendirmeyi](overview-authentication-authorization.md)nasıl özelleştireceğinizi ve uygulamanızdan kimlik yönetimi açıklanmaktadır. 

Hızlıca kullanmaya başlamak için aşağıdaki öğreticilerden birine bakın:

* [Öğretici: Azure App Service'te kullanıcıların kimliğini doğrulama ve kullanıcıları uçtan uca yetkilendirme](tutorial-auth-aad.md)
* [Uygulamanızı Microsoft Identity Platform oturum açma bilgilerini kullanacak şekilde yapılandırma](configure-authentication-provider-aad.md)
* [Uygulamanızı Facebook oturum açma bilgilerini kullanacak şekilde yapılandırma](configure-authentication-provider-facebook.md)
* [Uygulamanızı Google oturum açma bilgilerini kullanacak şekilde yapılandırma](configure-authentication-provider-google.md)
* [Uygulamanızı Twitter oturum açma bilgilerini kullanacak şekilde yapılandırma](configure-authentication-provider-twitter.md)
* [Uygulamanızı bir OpenID Connect sağlayıcısı kullanarak oturum açmak üzere yapılandırma (Önizleme)](configure-authentication-provider-openid-connect.md)
* [Uygulamanızı Apple ile oturum açma kullanarak oturum açmak üzere yapılandırma (Önizleme)](configure-authentication-provider-apple.md)

## <a name="use-multiple-sign-in-providers"></a>Çoklu oturum açma sağlayıcılarını kullanma

Portal Yapılandırması, kullanıcılarınıza birden çok oturum açma sağlayıcısı (Facebook ve Twitter gibi) sunmak için bir anahtar açma yöntemi sunmaz. Ancak, işlevselliği uygulamanıza eklemek zor değildir. Adımlar aşağıdaki şekilde özetlenmiştir:

İlk olarak, Azure portal **kimlik doğrulama/yetkilendirme** sayfasında, etkinleştirmek istediğiniz her bir kimlik sağlayıcısını yapılandırın.

**İsteğin kimliği doğrulanmamış olduğunda gerçekleştirilecek eylem Için** **anonim isteklere izin ver (eylem yok)** seçeneğini belirleyin.

Oturum açma sayfasında veya gezinti çubuğunda veya uygulamanızın herhangi bir yerinde, etkinleştirdiğiniz her bir sağlayıcının () bir oturum açma bağlantısını ekleyin `/.auth/login/<provider>` . Örnek:

```html
<a href="/.auth/login/aad">Log in with the Microsoft Identity Platform</a>
<a href="/.auth/login/facebook">Log in with Facebook</a>
<a href="/.auth/login/google">Log in with Google</a>
<a href="/.auth/login/twitter">Log in with Twitter</a>
<a href="/.auth/login/apple">Log in with Apple</a>
```

Kullanıcı bağlantılardan birine tıkladığında, kullanıcının oturum açması için ilgili oturum açma sayfası açılır.

Kullanıcı oturum açma sonrası eklentisini özel bir URL 'ye yönlendirmek için `post_login_redirect_url` sorgu dizesi parametresini kullanın (kimlik sağlayıcısı yapılandırmanızda yeniden yönlendirme URI 'si ile karıştırılmamalıdır). Örneğin, oturum açtıktan sonra kullanıcıya gitmek için `/Home/Index` AŞAĞıDAKI HTML kodunu kullanın:

```html
<a href="/.auth/login/<provider>?post_login_redirect_url=/Home/Index">Log in</a>
```

## <a name="validate-tokens-from-providers"></a>Sağlayıcılardan belirteçleri doğrula

İstemci ile yönlendirilen bir oturum açma bölümünde, uygulama kullanıcıdan sağlayıcıya el ile oturum açar ve ardından kimlik doğrulama belirtecini doğrulama için App Service (bkz. [kimlik doğrulama akışı](overview-authentication-authorization.md#authentication-flow)) gönderir. Bu doğrulamanın kendisi, istenen uygulama kaynaklarına erişim hakkı vermez, ancak başarılı bir doğrulama size uygulama kaynaklarına erişmek için kullanabileceğiniz bir oturum belirteci verecektir. 

Sağlayıcı belirtecini doğrulamak için App Service uygulamasının öncelikle istenen sağlayıcıyla yapılandırılması gerekir. Çalışma zamanında, sağlayıcınızdan kimlik doğrulama belirtecini aldıktan sonra, `/.auth/login/<provider>` doğrulama için belirteci gönderin. Örnek: 

```
POST https://<appname>.azurewebsites.net/.auth/login/aad HTTP/1.1
Content-Type: application/json

{"id_token":"<token>","access_token":"<token>"}
```

Belirteç biçimi sağlayıcıya göre biraz farklılık gösterir. Ayrıntılar için aşağıdaki tabloya bakın:

| Sağlayıcı değeri | İstek gövdesinde gerekli | Yorumlar |
|-|-|-|
| `aad` | `{"access_token":"<access_token>"}` | |
| `microsoftaccount` | `{"access_token":"<token>"}` | `expires_in`Özelliği isteğe bağlıdır. <br/>Canlı hizmetlerden belirteç istenirken, her zaman `wl.basic` kapsam isteyin. |
| `google` | `{"id_token":"<id_token>"}` | `authorization_code`Özelliği isteğe bağlıdır. Belirtildiğinde, isteğe bağlı olarak özelliği de kullanılabilir `redirect_uri` . |
| `facebook`| `{"access_token":"<user_access_token>"}` | Facebook 'tan geçerli bir [Kullanıcı erişim belirteci](https://developers.facebook.com/docs/facebook-login/access-tokens) kullanın. |
| `twitter` | `{"access_token":"<access_token>", "access_token_secret":"<acces_token_secret>"}` | |
| | | |

Sağlayıcı belirteci başarıyla doğrulandıktan sonra, API, `authenticationToken` yanıt gövdesinde, oturum belirteciniz olan bir ile birlikte döndürür. 

```json
{
    "authenticationToken": "...",
    "user": {
        "userId": "sid:..."
    }
}
```

Bu oturum belirtecine sahip olduğunuzda, `X-ZUMO-AUTH` http isteklerinize üst bilgi ekleyerek korumalı uygulama kaynaklarına erişebilirsiniz. Örnek: 

```
GET https://<appname>.azurewebsites.net/api/products/1
X-ZUMO-AUTH: <authenticationToken_value>
```

## <a name="sign-out-of-a-session"></a>Oturum kapatma

Kullanıcılar `GET` , uygulamanın uç noktasına bir istek göndererek oturumu kapatma işlemini başlatabilir `/.auth/logout` . `GET`İstek şunları yapar:

- Geçerli oturumdan kimlik doğrulama tanımlama bilgilerini temizler.
- Geçerli kullanıcının belirteçlerini belirteç deposundan siler.
- Azure Active Directory ve Google için, kimlik sağlayıcısında sunucu tarafında oturum kapatma gerçekleştirir.

Web sayfasında basit bir oturum kapatma bağlantısı şöyle olabilir:

```html
<a href="/.auth/logout">Sign out</a>
```

Varsayılan olarak, başarılı bir oturum kapatma istemciyi URL 'ye yeniden yönlendirir `/.auth/logout/done` . Sorgu parametresini ekleyerek, oturum kapatma sonrası yeniden yönlendirme sayfasını değiştirebilirsiniz `post_logout_redirect_uri` . Örnek:

```
GET /.auth/logout?post_logout_redirect_uri=/index.html
```

Değerini [kodlamanız](https://wikipedia.org/wiki/Percent-encoding) önerilir `post_logout_redirect_uri` .

Tam nitelikli URL 'Ler kullanılırken, URL aynı etki alanında barındırılıyor ya da uygulamanız için izin verilen bir dış yeniden yönlendirme URL 'SI olarak yapılandırılmış olmalıdır. Aşağıdaki örnekte, `https://myexternalurl.com` aynı etki alanında barındırılmayan öğesine yeniden yönlendirmek için:

```
GET /.auth/logout?post_logout_redirect_uri=https%3A%2F%2Fmyexternalurl.com
```

[Azure Cloud Shell](../cloud-shell/quickstart.md)aşağıdaki komutu çalıştırın:

```azurecli-interactive
az webapp auth update --name <app_name> --resource-group <group_name> --allowed-external-redirect-urls "https://myexternalurl.com"
```

## <a name="preserve-url-fragments"></a>URL parçalarını koruma

Kullanıcılar uygulamanızda oturum açtıktan sonra, genellikle gibi aynı sayfanın aynı bölümüne yönlendirilmek isterler `/wiki/Main_Page#SectionZ` . Bununla birlikte, [URL parçaları](https://wikipedia.org/wiki/Fragment_identifier) (örneğin, `#SectionZ` ) sunucuya hiçbir şekilde gönderilmediğinden, OAuth oturum açma tamamlandıktan sonra varsayılan olarak korunmaz ve uygulamanıza yeniden yönlendirilir. Kullanıcılar daha sonra istediğiniz yere gitmek gerektiğinde bir daha iyi deneyim yaşar. Bu sınırlama, tüm sunucu tarafı kimlik doğrulama çözümleri için geçerlidir.

App Service kimlik doğrulaması ' nda, OAuth oturum açma genelinde URL parçalarını koruyabilirsiniz. Bunu yapmak için olarak adlandırılan bir uygulama ayarı belirleyin `WEBSITE_AUTH_PRESERVE_URL_FRAGMENT` `true` . Bunu [Azure Portal](https://portal.azure.com)yapabilir veya [Azure Cloud Shell](../cloud-shell/quickstart.md)yalnızca aşağıdaki komutu çalıştırabilirsiniz:

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group <group_name> --settings WEBSITE_AUTH_PRESERVE_URL_FRAGMENT="true"
```

## <a name="access-user-claims"></a>Kullanıcı taleplerine erişin

App Service, özel üstbilgiler kullanarak Kullanıcı taleplerini uygulamanıza geçirir. Dış isteklerin bu üst bilgileri ayarlama izni yoktur, bu nedenle yalnızca App Service tarafından ayarlandıysa mevcut olmaları gerekir. Bazı örnek üstbilgileri şunlardır:

* X-MS-CLIENT-PRINCIPAL-NAME
* X-MS-CLIENT-PRINCIPAL-ıD

Herhangi bir dilde veya çerçevede yazılan kod, bu üst bilgilerden ihtiyaç duymakta olan bilgileri alabilir. ASP.NET 4,6 uygulamaları için, **ClaimsPrincipal** otomatik olarak uygun değerlerle ayarlanır. Ancak ASP.NET Core, App Service Kullanıcı taleplerini tümleştiren bir kimlik doğrulama ara yazılımı sağlamaz. Geçici bir çözüm için bkz. [Maximerouiller. Azure. AppService. EasyAuth](https://github.com/MaximRouiller/MaximeRouiller.Azure.AppService.EasyAuth).

Uygulamanız için [belirteç deposu](overview-authentication-authorization.md#token-store) etkinleştirilmişse, ' i çağırarak kimliği doğrulanmış kullanıcı hakkında ek ayrıntılar da alabilirsiniz `/.auth/me` . Mobile Apps Server SDK 'Ları, bu verilerle çalışmak için yardımcı yöntemler sağlar. Daha fazla bilgi için bkz. [azure Mobile Apps Node.js SDK 'sını kullanma](/previous-versions/azure/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk#howto-tables-getidentity)ve [Azure Mobile Apps için .net arka uç sunucu SDK 'sı ile çalışma](/previous-versions/azure/app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk#user-info).

## <a name="retrieve-tokens-in-app-code"></a>Uygulama kodundaki belirteçleri al

Sunucu kodunuzda sağlayıcıya özgü belirteçler istek üstbilgisine eklenir, bu sayede kolayca erişebilirsiniz. Aşağıdaki tabloda olası belirteç üstbilgisi adları gösterilmektedir:

| Sağlayıcı | Üst bilgi adları |
|-|-|
| Azure Active Directory | `X-MS-TOKEN-AAD-ID-TOKEN` <br/> `X-MS-TOKEN-AAD-ACCESS-TOKEN` <br/> `X-MS-TOKEN-AAD-EXPIRES-ON`  <br/> `X-MS-TOKEN-AAD-REFRESH-TOKEN` |
| Facebook belirteci | `X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN` <br/> `X-MS-TOKEN-FACEBOOK-EXPIRES-ON` |
| Google | `X-MS-TOKEN-GOOGLE-ID-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-ACCESS-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-EXPIRES-ON` <br/> `X-MS-TOKEN-GOOGLE-REFRESH-TOKEN` |
| Twitter | `X-MS-TOKEN-TWITTER-ACCESS-TOKEN` <br/> `X-MS-TOKEN-TWITTER-ACCESS-TOKEN-SECRET` |
|||

İstemci kodunuzda (bir mobil uygulama veya tarayıcı içi JavaScript gibi) bir HTTP `GET` isteği gönderin `/.auth/me` ([belirteç deposu](overview-authentication-authorization.md#token-store) etkinleştirilmelidir). Döndürülen JSON, sağlayıcıya özgü belirteçlere sahiptir.

> [!NOTE]
> Erişim belirteçleri sağlayıcı kaynaklarına erişmek için kullanılır, bu nedenle yalnızca sağlayıcınızı bir istemci gizli anahtarı ile yapılandırırsanız vardır. Yenileme belirteçlerinin nasıl alınacağını görmek için bkz. erişim belirteçlerini yenileme.

## <a name="refresh-identity-provider-tokens"></a>Kimlik sağlayıcısı belirteçlerini Yenile

Sağlayıcınızın erişim belirtecinin ( [oturum belirteci](#extend-session-token-expiration-grace-period)değil) süresi dolmuşsa, bu belirteci yeniden kullanmadan önce kullanıcıyı yeniden kimlik doğrulaması yapmanız gerekir. `GET`Uygulamanızın uç noktasına bir çağrı yaparak belirteç süre sonundan kaçınabilirsiniz `/.auth/refresh` . Çağrıldığında, App Service kimliği doğrulanmış kullanıcı için [belirteç deposundaki](overview-authentication-authorization.md#token-store) erişim belirteçlerini otomatik olarak yeniler. Uygulama kodunuzun belirteçleri için sonraki istekleri yenilenen belirteçleri alır. Ancak, belirteç yenilemenin çalışması için, belirteç deposu sağlayıcınız için [yenileme belirteçleri](https://auth0.com/learn/refresh-tokens/) içermelidir. Yenileme belirteçlerini almanın yöntemi her sağlayıcı tarafından belgelenmiştir, ancak aşağıdaki liste kısa bir özettir:

- **Google**: `access_type=offline` API çağrın sorgu dizesi parametresini ekleyin `/.auth/login/google` . Mobile Apps SDK kullanıyorsanız, `LogicAsync` aşırı yüklerden birine parametreyi ekleyebilirsiniz (bkz. [Google Refresh belirteçleri](https://developers.google.com/identity/protocols/OpenIDConnect#refresh-tokens)).
- **Facebook**: yenileme belirteçleri sağlamıyor. Uzun süreli belirteçlerin süresi 60 gün içinde doluyor (bkz. [Facebook süre sonu ve erişim belirteçleri uzantısı](https://developers.facebook.com/docs/facebook-login/access-tokens/expiration-and-extension)).
- **Twitter**: erişim belirteçlerinin süre sonu yok (bkz. [Twitter OAuth SSS](https://developer.twitter.com/en/docs/authentication/faq)).
- **Azure Active Directory**: içinde [https://resources.azure.com](https://resources.azure.com) , aşağıdaki adımları uygulayın:
    1. Sayfanın üst kısmında **oku/yaz**' ı seçin.
    2. Sol tarayıcıda, **abonelikler** > * *_\<subscription\_name_** > **ResourceGroups** > * *_ \<resource\_group\_name> _* * > **sağlayıcıları**  >  **Microsoft. Web**  >  **Sites** > * *_ \<app\_name> _ * * > **config**  >  **authsettings öğesine tıklayın**' e gidin. 
    3. **Düzenle**’ye tıklayın.
    4. Aşağıdaki özelliği değiştirin. _\<app\_id>_ Erişmek istediğiniz hizmetin Azure Active Directory uygulama kimliğiyle değiştirin.

        ```json
        "additionalLoginParams": ["response_type=code id_token", "resource=<app_id>"]
        ```

    5. **Yerleştir**' e tıklayın. 

Sağlayıcınız yapılandırıldıktan sonra, belirteç deposundaki [erişim belirtecinin yenileme belirtecini ve sona erme zamanını bulabilirsiniz](#retrieve-tokens-in-app-code) . 

Erişim belirtecinizi dilediğiniz zaman yenilemek için `/.auth/refresh` herhangi bir dilde çağrı yapmanız yeterlidir. Aşağıdaki kod parçacığı bir JavaScript istemcisinden erişim belirteçlerinizi yenilemek için jQuery kullanır.

```javascript
function refreshTokens() {
  let refreshUrl = "/.auth/refresh";
  $.ajax(refreshUrl) .done(function() {
    console.log("Token refresh completed successfully.");
  }) .fail(function() {
    console.log("Token refresh failed. See application logs for details.");
  });
}
```

Bir Kullanıcı uygulamanıza verilen izinleri iptal ederseniz, çağrısı `/.auth/me` bir yanıt vererek başarısız olabilir `403 Forbidden` . Hataları tanılamak için, Ayrıntılar için uygulama günlüklerinizi denetleyin.

## <a name="extend-session-token-expiration-grace-period"></a>Oturum belirteci süre sonu yetkisiz kullanım süresini uzat

Kimliği doğrulanmış oturumun süresi 8 saat sonra dolar. Kimliği doğrulanmış bir oturumun süresi dolduktan sonra, varsayılan olarak 72 saatlik bir yetkisiz kullanım süresi vardır. Bu yetkisiz kullanım süresi içinde, Kullanıcı yeniden kimlik doğrulaması yapmadan oturum belirtecini App Service yenilemeye izin verilir. Yalnızca `/.auth/refresh` oturum belirtecinizin geçersiz hale geldiği zaman çağrı yapabilir ve belirteç kullanım süresini kendiniz izlemeniz gerekmez. 72 saatlik yetkisiz kullanım süresi eşitlendikten sonra, Kullanıcı geçerli bir oturum belirteci almak için yeniden oturum açması gerekir.

Bu süre boyunca 72 saat yeterli değilse, bu süre sonu penceresini genişletebilirsiniz. Süre sonunu uzun bir süre boyunca uzatmak önemli güvenlik etkilerine (örneğin, bir kimlik doğrulama belirtecinin sızma veya çalındığı zaman) sahip olabilir. Bu nedenle, bunu varsayılan 72 saat olarak bırakmanız veya uzantı süresini en küçük değere ayarlamanız gerekir.

Varsayılan süre sonu penceresini genişletmek için [Cloud Shell](../cloud-shell/overview.md)aşağıdaki komutu çalıştırın.

```azurecli-interactive
az webapp auth update --resource-group <group_name> --name <app_name> --token-refresh-extension-hours <hours>
```

> [!NOTE]
> Yetkisiz kullanım süresi, kimlik sağlayıcılarındaki belirteçleri değil, yalnızca App Service kimliği doğrulanmış oturum için geçerlidir. Süresi geçen sağlayıcı belirteçleri için yetkisiz kullanım süresi yoktur. 
>

## <a name="limit-the-domain-of-sign-in-accounts"></a>Oturum açma hesaplarının etki alanını sınırlayın

Hem Microsoft hesabı hem de Azure Active Directory birden çok etki alanından oturum açmanızı sağlar. Örneğin, Microsoft hesabı _Outlook.com_, _Live.com_ ve _hotmail.com_ hesaplarına izin verir. Azure AD, oturum açma hesapları için herhangi bir sayıda özel etki alanı sağlar. Ancak, kullanıcılarınızı kendi markalı Azure AD oturum açma sayfanıza (örneğin,) doğrudan hızlandırmak isteyebilirsiniz `contoso.com` . Oturum açma hesaplarının etki alanı adını önermek için aşağıdaki adımları izleyin.

İçinde [https://resources.azure.com](https://resources.azure.com) , **abonelikler** > * *_\<subscription\_name_** > **ResourceGroups** > * *_ \<resource\_group\_name> _* * > **sağlayıcıları**  >  **Microsoft. Web**  >  **Sites** > * *_ \<app\_name> _ * * > **config**  >  **authsettings öğesine tıklayın**' e gidin. 

**Düzenle**' ye tıklayın, aşağıdaki özelliği değiştirin ve ardından **Yerleştir**' e tıklayın. İstediğiniz _\<domain\_name>_ etki alanı ile değiştirdiğinizden emin olun.

```json
"additionalLoginParams": ["domain_hint=<domain_name>"]
```

Bu ayar, `domain_hint` sorgu dizesi parametresini, oturum açma yeniden YÖNLENDIRME URL 'sine ekler. 

> [!IMPORTANT]
> `domain_hint`Yeniden YÖNLENDIRME URL 'sini aldıktan sonra istemcinin parametresini kaldırması ve sonra farklı bir etki alanı ile oturum açması mümkündür. Bu işlev kullanışlı olsa da, bir güvenlik özelliği değildir.
>

## <a name="authorize-or-deny-users"></a>Kullanıcılara yetki verme veya reddetme

App Service en basit yetkilendirme durumu (yani, kimliği doğrulanmamış istekleri reddetme) ile ilgilenirken, uygulamanız yalnızca belirli bir kullanıcı grubuyla erişimi sınırlandırma gibi daha ayrıntılı yetkilendirme davranışı gerektirebilir. Belirli durumlarda, oturum açmış kullanıcıya izin vermek veya erişimi reddetmek için özel uygulama kodu yazmanız gerekir. Diğer durumlarda, App Service veya kimlik sağlayıcınız kod değişikliği gerektirmeden yardım edebilir.

- [Sunucu düzeyi](#server-level-windows-apps-only)
- [Kimlik sağlayıcısı düzeyi](#identity-provider-level)
- [Uygulama düzeyi](#application-level)

### <a name="server-level-windows-apps-only"></a>Sunucu düzeyi (yalnızca Windows uygulamaları)

Herhangi bir Windows uygulaması için, *Web.config* dosyasını düzenleyerek IIS Web sunucusunun yetkilendirme davranışını tanımlayabilirsiniz. Linux uygulamaları IIS kullanmaz ve *Web.config* aracılığıyla yapılandırılamaz.

1. `https://<app-name>.scm.azurewebsites.net/DebugConsole` sayfasına gidin

1. App Service dosyalarınızın tarayıcı Gezgini ' nde, *site/Wwwroot ' ya* gidin. *Web.config* yoksa, **+**  >  **yeni dosya**' yı seçerek oluşturun. 

1. *Web.config* düzenlemek için kurşun kalem ' i seçin. Aşağıdaki yapılandırma kodunu ekleyin ve **Kaydet**' e tıklayın. *Web.config* zaten varsa, `<authorization>` öğeyi içindeki her şeyi eklemeniz yeterlidir. Öğesinde izin vermek istediğiniz hesapları ekleyin `<allow>` .

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

## <a name="updating-the-configuration-version"></a>Yapılandırma sürümü güncelleştiriliyor

Kimlik doğrulama/yetkilendirme özelliği için yönetim API 'sinin iki sürümü vardır. V2 sürümü, Azure portal "kimlik doğrulama" deneyimi için gereklidir. Zaten v1 API 'sini kullanan bir uygulama, birkaç değişiklik yapıldıktan sonra v2 sürümüne yükseltilebilir. Özellikle gizli yapılandırma, yuva Yapışkan uygulama ayarlarına taşınmalıdır. Bu, uygulamanızın portalın "kimlik doğrulama" bölümünden otomatik olarak yapılabilir.

> [!WARNING]
> V2 'ye geçiş, uygulamanız için App Service kimlik doğrulama/yetkilendirme özelliğinin yönetimini devre dışı bırakır. Örneğin, Azure portal, Azure CLı ve Azure PowerShell var olan deneyimi gibi bazı istemciler. Bu işlem geri alınamaz.

V2 API 'SI, Microsoft hesabı 'nın v1 'de yapıldığından farklı bir sağlayıcı olarak oluşturulmasını veya düzenlenmesinin yapılmasını desteklemez. Bunun yerine, hem Azure AD hem de kişisel Microsoft hesaplarıyla Kullanıcı oturumu açmak için yakınsanmış [Microsoft Identity platformunu](../active-directory/develop/v2-overview.md) kullanır. V2 API 'sine geçiş yaparken, v1 Azure Active Directory yapılandırması Microsoft Identity platform sağlayıcısını yapılandırmak için kullanılır. V1 Microsoft hesabı sağlayıcısı geçiş işleminde ileri taşınır ve normal olarak çalışmaya devam eder, ancak yeni Microsoft Identity platform modeline taşımanız önerilir. Daha fazla bilgi edinmek için bkz. [Microsoft hesap sağlayıcısı kayıtları Için destek](#support-for-microsoft-account-provider-registrations) .

Otomatik geçiş işlemi, sağlayıcı gizli dizilerini uygulama ayarlarına taşıyacak ve sonra yapılandırmanın geri kalanını yeni biçime dönüştürmeyecektir. Otomatik geçişi kullanmak için:

1. Portalda uygulamanıza gidin ve **kimlik doğrulaması** menü seçeneğini belirleyin.
1. Uygulama v1 modeli kullanılarak yapılandırıldıysa, bir **yükseltme** düğmesi görürsünüz.
1. Onay isteminde açıklamayı Gözden geçirin. Geçiş işlemini gerçekleştirmeye hazırsanız, sorulduğunda **Yükselt** ' e tıklayın.

### <a name="manually-managing-the-migration"></a>Geçişi el ile yönetme

Aşağıdaki adımlar, yukarıda belirtilen otomatik sürümü kullanmak istemiyorsanız, uygulamayı v2 API 'sine el ile geçirmenize olanak sağlayacak.

#### <a name="moving-secrets-to-application-settings"></a>Gizli dizileri uygulama ayarlarına taşıma

1. V1 API 'sini kullanarak mevcut yapılandırmanızı alın:

   ```azurecli
   # For Web Apps
   az webapp auth show -g <group_name> -n <site_name>

   # For Azure Functions
   az functionapp auth show -g <group_name> -n <site_name>
   ```

   Sonuçta elde edilen JSON yükünde, yapılandırdığınız her sağlayıcı için kullanılan gizli değeri unutmayın:

   * AAD `clientSecret`
   * Google `googleClientSecret`
   * 'A `facebookAppSecret`
   * Twitter `twitterConsumerSecret`
   * Microsoft hesabı: `microsoftAccountClientSecret`

   > [!IMPORTANT]
   > Gizli anahtar değerleri önemli güvenlik kimlik bilgileridir ve dikkatle işlenmelidir. Bu değerleri paylaşmayın veya yerel bir makinede kalıcı hale getirin.

1. Her gizli değer için yuva Yapışkan uygulama ayarları oluşturun. Her uygulama ayarının adını seçebilirsiniz. Bu değer, önceki adımda elde ettiğiniz şekilde veya bu değerle oluşturduğunuz [Key Vault bir gizli](./app-service-key-vault-references.md?toc=/azure/azure-functions/toc.json) dizi ile eşleşmelidir.

   Ayarı oluşturmak için Azure portal kullanabilir veya her bir sağlayıcı için aşağıdakilerin bir çeşidini çalıştırabilirsiniz:

   ```azurecli
   # For Web Apps, Google example    
   az webapp config appsettings set -g <group_name> -n <site_name> --slot-settings GOOGLE_PROVIDER_AUTHENTICATION_SECRET=<value_from_previous_step>

   # For Azure Functions, Twitter example
   az functionapp config appsettings set -g <group_name> -n <site_name> --slot-settings TWITTER_PROVIDER_AUTHENTICATION_SECRET=<value_from_previous_step>
   ```

   > [!NOTE]
   > Bu yapılandırma için uygulama ayarları yuva yapışkan olarak işaretlenmelidir, yani bir [yuva değiştirme işlemi](./deploy-staging-slots.md)sırasında ortamlar arasında hareket etmez. Bunun nedeni, kimlik doğrulama yapılandırmanızın ortama bağlı olması. 

1. Adlı yeni bir JSON dosyası oluşturun `authsettings.json` . Daha önce aldığınız çıktıyı alın ve her gizli değeri bundan kaldırın. Gizli anahtar bulunmadığından emin olmak için geri kalan çıktıyı dosyaya yazın. Bazı durumlarda, yapılandırma boş dizeler içeren dizilere sahip olabilir. Olmadığından emin olun `microsoftAccountOAuthScopes` ve varsa, bu değere geçiş yapın `null` .

1. `authsettings.json`Her sağlayıcı için daha önce oluşturduğunuz uygulama ayarı adına işaret eden bir özellik ekleyin:
 
   * AAD `clientSecretSettingName`
   * Google `googleClientSecretSettingName`
   * 'A `facebookAppSecretSettingName`
   * Twitter `twitterConsumerSecretSettingName`
   * Microsoft hesabı: `microsoftAccountClientSecretSettingName`

   Bu işlemden sonra örnek bir dosya aşağıdakine benzer görünebilir, bu durumda yalnızca AAD için yapılandırılır:

   ```json
   {
       "id": "/subscriptions/00d563f8-5b89-4c6a-bcec-c1b9f6d607e0/resourceGroups/myresourcegroup/providers/Microsoft.Web/sites/mywebapp/config/authsettings",
       "name": "authsettings",
       "type": "Microsoft.Web/sites/config",
       "location": "Central US",
       "properties": {
           "enabled": true,
           "runtimeVersion": "~1",
           "unauthenticatedClientAction": "AllowAnonymous",
           "tokenStoreEnabled": true,
           "allowedExternalRedirectUrls": null,
           "defaultProvider": "AzureActiveDirectory",
           "clientId": "3197c8ed-2470-480a-8fae-58c25558ac9b",
           "clientSecret": "",
           "clientSecretSettingName": "MICROSOFT_IDENTITY_AUTHENTICATION_SECRET",
           "clientSecretCertificateThumbprint": null,
           "issuer": "https://sts.windows.net/0b2ef922-672a-4707-9643-9a5726eec524/",
           "allowedAudiences": [
               "https://mywebapp.azurewebsites.net"
           ],
           "additionalLoginParams": null,
           "isAadAutoProvisioned": true,
           "aadClaimsAuthorization": null,
           "googleClientId": null,
           "googleClientSecret": null,
           "googleClientSecretSettingName": null,
           "googleOAuthScopes": null,
           "facebookAppId": null,
           "facebookAppSecret": null,
           "facebookAppSecretSettingName": null,
           "facebookOAuthScopes": null,
           "gitHubClientId": null,
           "gitHubClientSecret": null,
           "gitHubClientSecretSettingName": null,
           "gitHubOAuthScopes": null,
           "twitterConsumerKey": null,
           "twitterConsumerSecret": null,
           "twitterConsumerSecretSettingName": null,
           "microsoftAccountClientId": null,
           "microsoftAccountClientSecret": null,
           "microsoftAccountClientSecretSettingName": null,
           "microsoftAccountOAuthScopes": null,
           "isAuthFromFile": "false"
       }   
   }
   ```

1. Bu dosyayı uygulamanız için yeni kimlik doğrulama/yetkilendirme yapılandırması olarak gönder:

   ```azurecli
   az rest --method PUT --url "/subscriptions/<subscription_id>/resourceGroups/<group_name>/providers/Microsoft.Web/sites/<site_name>/config/authsettings?api-version=2020-06-01" --body @./authsettings.json
   ```

1. Bu hareket sonrasında uygulamanızın beklendiği gibi hala çalışıyor olduğunu doğrulayın.

1. Önceki adımlarda kullanılan dosyayı silin.

Artık uygulamayı, kimlik sağlayıcısı gizli dizilerini uygulama ayarları olarak depolayacak şekilde geçirdiniz.

#### <a name="support-for-microsoft-account-provider-registrations"></a>Microsoft hesabı sağlayıcı kayıtları için destek

Mevcut yapılandırmanız bir Microsoft hesabı sağlayıcısı içeriyorsa ve bir Azure Active Directory sağlayıcısı içermiyorsa, yapılandırmayı Azure Active Directory sağlayıcısına değiştirebilir ve sonra geçişi gerçekleştirebilirsiniz. Bunu yapmak için:

1. Azure portal [**uygulama kayıtları**](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) gidin ve Microsoft hesap sağlayıcınızla ilişkili kaydı bulun. "Kişisel hesap uygulamaları" başlığı altında olabilir.
1. Kayıt için "kimlik doğrulama" sayfasına gidin. "Yeniden yönlendirme URI 'Leri" altında, bir girişi görmeniz gerekir `/.auth/login/microsoftaccount/callback` . Bu URI 'yi kopyalayın.
1. Yeni kopyaladığınız bir URI 'yi ekleyin, bunun yerine içinde sona erdiğinden `/.auth/login/aad/callback` . Bu, kaydın App Service kimlik doğrulaması/yetkilendirme yapılandırması tarafından kullanılmasına izin verir.
1. Uygulamanız için App Service kimlik doğrulaması/yetkilendirme yapılandırmasına gidin.
1. Microsoft hesabı sağlayıcısı için yapılandırmayı toplayın.
1. Önceki adımda topladığınız istemci KIMLIĞI ve istemci gizli değerlerini sağlayarak, Azure Active Directory sağlayıcıyı "Gelişmiş" yönetim modunu kullanarak yapılandırın. Veren URL 'SI için kullan ' ı kullanın `<authentication-endpoint>/<tenant-id>/v2.0` ve *\<authentication-endpoint>* [bulut ortamınız için kimlik doğrulama uç noktasıyla](../active-directory/develop/authentication-national-cloud.md#azure-ad-authentication-endpoints) değiştirin (örneğin, " https://login.microsoftonline.com Genel Azure için), ayrıca *\<tenant-id>* **Dizin (kiracı) kimliğiniz** ile değiştirin.
1. Yapılandırmayı kaydettikten sonra, tarayıcınızda `/.auth/login/aad` sitenizdeki uç noktaya giderek ve oturum açma akışını tamamladıktan sonra oturum açma akışını test edin.
1. Bu noktada yapılandırmayı başarıyla kopyaladınız, ancak mevcut Microsoft hesap sağlayıcısı yapılandırması kalmaya devam eder. Kaldırmadan önce, uygulamanızın tüm bölümlerinin Azure Active Directory sağlayıcıya oturum açma bağlantıları aracılığıyla başvurduğunuzdan emin olun. Uygulamanızın tüm bölümlerinin beklendiği gibi çalıştığını doğrulayın.
1. Her şeyin AAD Azure Active Directory sağlayıcısına karşı çalıştığını doğrulandıktan sonra, Microsoft hesap sağlayıcısı yapılandırmasını kaldırabilirsiniz.

> [!WARNING]
> AAD uygulama kaydı için [Desteklenen hesap türlerini](../active-directory/develop/supported-accounts-validation.md) değiştirerek iki kaydı yakınlaşmak mümkündür. Bununla birlikte, bu, Microsoft hesabı kullanıcıları için yeni bir onay istemi zorlamasına ve bu kullanıcıların kimlik talepleri yapıda farklı olabilir ve bu da `sub` Yeni bir uygulama kimliği kullanımda olduğundan değerleri özellikle değiştiriyor. Bu yaklaşım, iyice anlaşılmadığı takdirde önerilmez. Bunun yerine v2 API yüzeyinde iki kayıt desteğini beklemeniz gerekir.

#### <a name="switching-to-v2"></a>V2 'ye geçiliyor

Yukarıdaki adımlar gerçekleştirildikten sonra, Azure portal uygulamaya gidin. "Kimlik doğrulama (Önizleme)" bölümünü seçin. 

Alternatif olarak, `config/authsettingsv2` site kaynağı altındaki kaynağa karşı bır PUT isteği de yapabilirsiniz. Yük şeması, [dosya kullanarak yapılandırma](#config-file) bölümünde yakalanan ile aynıdır.

## <a name="configure-using-a-file-preview"></a><a name="config-file"> </a>Dosya kullanarak yapılandırma (Önizleme)

Kimlik doğrulama ayarlarınız isteğe bağlı olarak, dağıtımınız tarafından sağlanmış bir dosya aracılığıyla yapılandırılabilir. Bu, App Service kimlik doğrulaması/yetkilendirme için bazı Önizleme özellikleri tarafından gerekli olabilir.

> [!IMPORTANT]
> Uygulama yükünüzü ve bu dosyanın, [yuvalarda](./deploy-staging-slots.md)olduğu gibi ortamlar arasında hareket edebilir olduğunu unutmayın. Büyük olasılıkla her bir yuvaya sabitlenmiş farklı bir uygulama kaydı yapmak isteyebilirsiniz ve bu durumlarda yapılandırma dosyasını kullanmak yerine standart yapılandırma yöntemini kullanmaya devam etmelisiniz.

### <a name="enabling-file-based-configuration"></a>Dosya tabanlı yapılandırmayı etkinleştirme

> [!CAUTION]
> Önizleme süresince dosya tabanlı yapılandırmayı etkinleştirmek, Azure portal, Azure CLı ve Azure PowerShell gibi bazı istemciler aracılığıyla uygulamanız için App Service kimlik doğrulaması/yetkilendirme özelliğinin yönetimini devre dışı bırakır.

1. Projenizin kökündeki yapılandırmanız için yeni bir JSON dosyası oluşturun (Web/işlev uygulamanızda D:\home\site\wwwroot dosyasına dağıtılır). [Dosya tabanlı yapılandırma başvurusuna](#configuration-file-reference)göre istediğiniz yapılandırmayı girin. Mevcut bir Azure Resource Manager yapılandırmasını değiştiriyorsanız, koleksiyonda yakalanan özellikleri yapılandırma dosyanıza çevirdiğinizden emin olun `authsettings` .

2. Altındaki [Azure Resource Manager](../azure-resource-manager/management/overview.md) API 'lerinde yakalanan varolan yapılandırmayı değiştirin `Microsoft.Web/sites/<siteName>/config/authsettings` . Bunu değiştirmek için [Azure Resource Manager şablonu](../azure-resource-manager/templates/overview.md) veya [Azure Kaynak Gezgini](https://resources.azure.com/)gibi bir aracı kullanabilirsiniz. Authsettings öğesine tıklayın koleksiyonu içinde üç özellik ayarlamanız gerekir (ve diğerlerini kaldırabilir):

    1.  `enabled`"True" olarak ayarlayın
    2.  `isAuthFromFile`"True" olarak ayarlayın
    3.  `authFilePath`Dosyanın adına ayarlanır (örneğin, "auth.json")

> [!NOTE]
> İçin biçimi `authFilePath` platformlar arasında farklılık gösterir. Windows 'ta, hem göreli hem de mutlak yollar desteklenir. Göreli olarak önerilir. Linux için şu anda yalnızca mutlak yollar desteklenir, bu nedenle ayarın değeri "/Home/site/Wwwroot/auth.json" veya benzer olmalıdır.

Bu yapılandırma güncelleştirmesini yaptıktan sonra, bu site için App Service kimlik doğrulaması/yetkilendirme davranışını tanımlamak üzere dosyanın içeriği kullanılacaktır. Azure Resource Manager yapılandırmaya geri dönmek isterseniz, bunu `isAuthFromFile` "false" olarak ayarlayarak yapabilirsiniz.

### <a name="configuration-file-reference"></a>Yapılandırma dosyası başvurusu

Yapılandırma dosyanızda başvurulacak tüm gizli dizi, [uygulama ayarları](./configure-common.md#configure-app-settings)olarak depolanmalıdır. Ayarları istediğiniz şekilde adlandırın. Yalnızca yapılandırma dosyasındaki başvuruların aynı anahtarları kullandığından emin olun.

Aşağıdakiler dosya içinde olası yapılandırma seçeneklerini tüketmektedir:

```json
{
    "platform": {
        "enabled": <true|false>
    },
    "globalValidation": {
        "unauthenticatedClientAction": "RedirectToLoginPage|AllowAnonymous|Return401|Return403",
        "redirectToProvider": "<default provider alias>",
        "excludedPaths": [
            "/path1",
            "/path2"
        ]
    },
    "httpSettings": {
        "requireHttps": <true|false>,
        "routes": {
            "apiPrefix": "<api prefix>"
        },
        "forwardProxy": {
            "convention": "NoProxy|Standard|Custom",
            "customHostHeaderName": "<host header value>",
            "customProtoHeaderName": "<proto header value>"
        }
    },
    "login": {
        "routes": {
            "logoutEndpoint": "<logout endpoint>"
        },
        "tokenStore": {
            "enabled": <true|false>,
            "tokenRefreshExtensionHours": "<double>",
            "fileSystem": {
                "directory": "<directory to store the tokens in if using a file system token store (default)>"
            },
            "azureBlobStorage": {
                "sasUrlSettingName": "<app setting name containing the sas url for the Azure Blob Storage if opting to use that for a token store>"
            }
        },
        "preserveUrlFragmentsForLogins": <true|false>,
        "allowedExternalRedirectUrls": [
            "https://uri1.azurewebsites.net/",
            "https://uri2.azurewebsites.net/",
            "url_scheme_of_your_app://easyauth.callback"
        ],
        "cookieExpiration": {
            "convention": "FixedTime|IdentityDerived",
            "timeToExpiration": "<timespan>"
        },
        "nonce": {
            "validateNonce": <true|false>,
            "nonceExpirationInterval": "<timespan>"
        }
    },
    "identityProviders": {
        "azureActiveDirectory": {
            "enabled": <true|false>,
            "registration": {
                "openIdIssuer": "<issuer url>",
                "clientId": "<app id>",
                "clientSecretSettingName": "APP_SETTING_CONTAINING_AAD_SECRET",
            },
            "login": {
                "loginParameters": [
                    "paramName1=value1",
                    "paramName2=value2"
                ]
            },
            "validation": {
                "allowedAudiences": [
                    "audience1",
                    "audience2"
                ]
            }
        },
        "facebook": {
            "enabled": <true|false>,
            "registration": {
                "appId": "<app id>",
                "appSecretSettingName": "APP_SETTING_CONTAINING_FACEBOOK_SECRET"
            },
            "graphApiVersion": "v3.3",
            "login": {
                "scopes": [
                    "public_profile",
                    "email"
                ]
            },
        },
        "gitHub": {
            "enabled": <true|false>,
            "registration": {
                "clientId": "<client id>",
                "clientSecretSettingName": "APP_SETTING_CONTAINING_GITHUB_SECRET"
            },
            "login": {
                "scopes": [
                    "profile",
                    "email"
                ]
            }
        },
        "google": {
            "enabled": true,
            "registration": {
                "clientId": "<client id>",
                "clientSecretSettingName": "APP_SETTING_CONTAINING_GOOGLE_SECRET"
            },
            "login": {
                "scopes": [
                    "profile",
                    "email"
                ]
            },
            "validation": {
                "allowedAudiences": [
                    "audience1",
                    "audience2"
                ]
            }
        },
        "twitter": {
            "enabled": <true|false>,
            "registration": {
                "consumerKey": "<consumer key>",
                "consumerSecretSettingName": "APP_SETTING_CONTAINING TWITTER_CONSUMER_SECRET"
            }
        },
        "apple": {
            "enabled": <true|false>,
            "registration": {
                "clientId": "<client id>",
                "clientSecretSettingName": "APP_SETTING_CONTAINING_APPLE_SECRET"
            },
            "login": {
                "scopes": [
                    "profile",
                    "email"
                ]
            }
        },
        "openIdConnectProviders": {
            "<providerName>": {
                "enabled": <true|false>,
                "registration": {
                    "clientId": "<client id>",
                    "clientCredential": {
                        "clientSecretSettingName": "<name of app setting containing client secret>"
                    },
                    "openIdConnectConfiguration": {
                        "authorizationEndpoint": "<url specifying authorization endpoint>",
                        "tokenEndpoint": "<url specifying token endpoint>",
                        "issuer": "<url specifying issuer>",
                        "certificationUri": "<url specifying jwks endpoint>",
                        "wellKnownOpenIdConfiguration": "<url specifying .well-known/open-id-configuration endpoint - if this property is set, the other properties of this object are ignored, and authorizationEndpoint, tokenEndpoint, issuer, and certificationUri are set to the corresponding values listed at this endpoint>"
                    }
                },
                "login": {
                    "nameClaimType": "<name of claim containing name>",
                    "scopes": [
                        "openid",
                        "profile",
                        "email"
                    ],
                    "loginParameterNames": [
                        "paramName1=value1",
                        "paramName2=value2"
                    ],
                }
            },
            //...
        }
    }
}
```

## <a name="pin-your-app-to-a-specific-authentication-runtime-version"></a>Uygulamanızı belirli bir kimlik doğrulaması çalışma zamanı sürümüne sabitleme

Kimlik doğrulama/yetkilendirmeyi etkinleştirdiğinizde, platform ara yazılımı, [özellik genel bakışı](overview-authentication-authorization.md#how-it-works)bölümünde AÇıKLANDıĞı gibi http istek işlem hattınızla eklenir. Bu platform ara yazılımı, rutin platform güncelleştirmelerinin bir parçası olarak yeni özellikler ve geliştirmeler ile düzenli olarak güncelleştirilir. Varsayılan olarak, Web veya işlev uygulamanız bu platform ara yazılımı 'nın en son sürümünde çalışır. Bu otomatik güncelleştirmeler her zaman geriye dönük olarak uyumludur. Ancak, bu otomatik güncelleştirmenin Web veya işlev uygulamanız için bir çalışma zamanı sorunu tanıtıldığı nadir bir olayda, önceki bir ara yazılım sürümüne geçici olarak geri dönebilirsiniz. Bu makalede, bir uygulamanın kimlik doğrulama ara yazılımı 'nın belirli bir sürümüne geçici olarak nasıl sabitleneceği açıklanır.

### <a name="automatic-and-manual-version-updates"></a>Otomatik ve el ile sürüm güncelleştirmeleri 

Uygulama için bir ayar ayarlayarak uygulamanızı, platform ara yazılımı 'nın belirli bir sürümüne sabitleyebilirsiniz `runtimeVersion` . Uygulamanızı belirli bir sürüme açıkça sabitlemeyi seçmediğiniz müddetçe, uygulamanız her zaman en son sürümde çalışır. Tek seferde desteklenen birkaç sürüm olacaktır. Artık desteklenmeyen geçersiz bir sürüme PIN yaparsanız, uygulamanız bunun yerine en son sürümü kullanacaktır. En son sürümü her zaman çalıştırmak için `runtimeVersion` ~ 1 olarak ayarlayın. 

### <a name="view-and-update-the-current-runtime-version"></a>Geçerli çalışma zamanı sürümünü görüntüle ve Güncelleştir

Uygulamanız tarafından kullanılan çalışma zamanı sürümünü değiştirebilirsiniz. Yeni çalışma zamanı sürümü, uygulama yeniden başlatıldıktan sonra devreye girer. 

#### <a name="view-the-current-runtime-version"></a>Geçerli çalışma zamanı sürümünü görüntüle

Azure CLı kullanarak veya uygulamanızdaki yerleşik sürüm HTTP uç noktalarından biri aracılığıyla platform kimlik doğrulama ara yazılımı 'nın geçerli sürümünü görüntüleyebilirsiniz.

##### <a name="from-the-azure-cli"></a>Azure CLı 'dan

Azure CLı 'yı kullanarak, [az WebApp auth Show](/cli/azure/webapp/auth#az_webapp_auth_show) komutuyla geçerli ara yazılım sürümünü görüntüleyin.

```azurecli-interactive
az webapp auth show --name <my_app_name> \
--resource-group <my_resource_group>
```

Bu kodda, değerini `<my_app_name>` uygulamanızın adıyla değiştirin. Ayrıca `<my_resource_group>` , uygulamanızın kaynak grubu adıyla değiştirin.

`runtimeVersion`CLI çıkışında alanını görürsünüz. Bu, açıklık açısından kısaltıldı ve aşağıdaki örnek çıktıya benzeyecektir: 
```output
{
  "additionalLoginParams": null,
  "allowedAudiences": null,
    ...
  "runtimeVersion": "1.3.2",
    ...
}
```

##### <a name="from-the-version-endpoint"></a>Sürüm uç noktasından

Ayrıca, uygulamanın üzerinde çalıştığı geçerli ara yazılım sürümünü görüntülemek için bir uygulamadaki/. auth/Version uç noktasını da vurun. Aşağıdaki örnek çıktıya benzeyecektir:
```output
{
"version": "1.3.2"
}
```

#### <a name="update-the-current-runtime-version"></a>Geçerli çalışma zamanı sürümünü güncelleştirme

Azure CLı 'yı kullanarak, `runtimeVersion` [az WebApp auth Update](/cli/azure/webapp/auth#az_webapp_auth_update) komutuyla uygulamadaki ayarı güncelleştirebilirsiniz.

```azurecli-interactive
az webapp auth update --name <my_app_name> \
--resource-group <my_resource_group> \
--runtime-version <version>
```

`<my_app_name>`Uygulamanızın adıyla değiştirin. Ayrıca `<my_resource_group>` , uygulamanızın kaynak grubu adıyla değiştirin. Ayrıca, `<version>` 1. x çalışma zamanının geçerli bir sürümüyle veya `~1` en son sürüm ile değiştirin. Sürüm notlarını farklı çalışma zamanı sürümlerinde bulabilirsiniz [buraya] ( https://github.com/Azure/app-service-announcements) sabitlenemeyecek sürümü belirlemenize yardımcı olmak için).

Yukarıdaki kod örneğinde **deneyin** ' i seçerek bu komutu [Azure Cloud Shell](../cloud-shell/overview.md) çalıştırabilirsiniz. Ayrıca, oturum açmak için [az Login](/cli/azure/reference-index#az_login) komutunu çalıştırdıktan sonra bu komutu yürütmek IÇIN [Azure CLI 'yı yerel olarak](/cli/azure/install-azure-cli) da kullanabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Öğretici: Uçtan uca kullanıcıların kimliğini doğrulama ve kullanıcıları yetkilendirme](tutorial-auth-aad.md)
