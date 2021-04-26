---
title: Bir Web API 'SI çağırmak için belirteç alma (mobil uygulamalar) | Mavisi
titleSuffix: Microsoft identity platform
description: Web API 'Lerini çağıran bir mobil uygulama oluşturmayı öğrenin. (Uygulama için bir belirteç alın.)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 6e6eda3d711710ea7450165ab02d7a260067bfcb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99582562"
---
# <a name="get-a-token-for-a-mobile-app-that-calls-web-apis"></a>Web API 'Lerini çağıran bir mobil uygulama için belirteç alın

Uygulamanız korunan Web API 'Lerini çağırabilmeniz için bir erişim belirtecine ihtiyaç duyuyor. Bu makalede, Microsoft kimlik doğrulama kitaplığı 'nı (MSAL) kullanarak bir belirteci almak için işlem adım adım açıklanmaktadır.

## <a name="define-a-scope"></a>Kapsam tanımlama

Bir belirteç istediğinizde, bir kapsam tanımlayın. Kapsam, uygulamanızın erişebileceği verileri belirler.

Kapsam tanımlamanın en kolay yolu, istenen Web API 'sini kapsam ile birleştirmenin bir yoludur `App ID URI` `.default` . Bu tanım, uygulamanızın portalda ayarlanan tüm kapsamları gerektirip gerektirmediğini Microsoft Identity platformunu söyler.

### <a name="android"></a>Android
```Java
String[] SCOPES = {"https://graph.microsoft.com/.default"};
```

### <a name="ios"></a>iOS
```swift
let scopes = ["https://graph.microsoft.com/.default"]
```

### <a name="xamarin"></a>Xamarin
```csharp
var scopes = new [] {"https://graph.microsoft.com/.default"};
```

## <a name="get-tokens"></a>Belirteçleri al

### <a name="acquire-tokens-via-msal"></a>Belirteçleri MSAL aracılığıyla alma

MSAL, uygulamaların belirteçleri sessizce ve etkileşimli olarak almasına izin verir. `AcquireTokenSilent()`Veya `AcquireTokenInteractive()` öğesini çağırdığınızda, msal istenen kapsamlar için bir erişim belirteci döndürür. Doğru model, sessiz bir istek yapmak ve sonra etkileşimli bir isteğe geri dönmelidir.

#### <a name="android"></a>Android

```Java
String[] SCOPES = {"https://graph.microsoft.com/.default"};
PublicClientApplication sampleApp = new PublicClientApplication(
                    this.getApplicationContext(),
                    R.raw.auth_config);

// Check if there are any accounts we can sign in silently.
// Result is in the silent callback (success or error).
sampleApp.getAccounts(new PublicClientApplication.AccountsLoadedCallback() {
    @Override
    public void onAccountsLoaded(final List<IAccount> accounts) {

        if (accounts.isEmpty() && accounts.size() == 1) {
            // TODO: Create a silent callback to catch successful or failed request.
            sampleApp.acquireTokenSilentAsync(SCOPES, accounts.get(0), getAuthSilentCallback());
        } else {
            /* No accounts or > 1 account. */
        }
    }
});

[...]

// No accounts found. Interactively request a token.
// TODO: Create an interactive callback to catch successful or failed requests.
sampleApp.acquireToken(getActivity(), SCOPES, getAuthInteractiveCallback());
```

#### <a name="ios"></a>iOS

Önce bir belirteci sessizce almaya çalışın:

```objc

NSArray *scopes = @[@"https://graph.microsoft.com/.default"];
NSString *accountIdentifier = @"my.account.id";

MSALAccount *account = [application accountForIdentifier:accountIdentifier error:nil];

MSALSilentTokenParameters *silentParams = [[MSALSilentTokenParameters alloc] initWithScopes:scopes account:account];
[application acquireTokenSilentWithParameters:silentParams completionBlock:^(MSALResult *result, NSError *error) {

    if (!error)
    {
        // You'll want to get the account identifier to retrieve and reuse the account
        // for later acquireToken calls
        NSString *accountIdentifier = result.account.identifier;

        // Access token to call the web API
        NSString *accessToken = result.accessToken;
    }

    // Check the error
    if (error && [error.domain isEqual:MSALErrorDomain] && error.code == MSALErrorInteractionRequired)
    {
        // Interactive auth will be required, call acquireTokenWithParameters:error:
        return;
    }
}];
```

```swift

let scopes = ["https://graph.microsoft.com/.default"]
let accountIdentifier = "my.account.id"

guard let account = try? application.account(forIdentifier: accountIdentifier) else { return }
let silentParameters = MSALSilentTokenParameters(scopes: scopes, account: account)
application.acquireTokenSilent(with: silentParameters) { (result, error) in

    guard let authResult = result, error == nil else {

    let nsError = error! as NSError

    if (nsError.domain == MSALErrorDomain &&
        nsError.code == MSALError.interactionRequired.rawValue) {

            // Interactive auth will be required, call acquireToken()
            return
         }
         return
     }

    // You'll want to get the account identifier to retrieve and reuse the account
    // for later acquireToken calls
    let accountIdentifier = authResult.account.identifier

    // Access token to call the web API
    let accessToken = authResult.accessToken
}
```

MSAL dönerse `MSALErrorInteractionRequired` belirteçleri etkileşimli olarak almaya çalışın:

```objc
UIViewController *viewController = ...; // Pass a reference to the view controller that should be used when getting a token interactively
MSALWebviewParameters *webParameters = [[MSALWebviewParameters alloc] initWithAuthPresentationViewController:viewController];
MSALInteractiveTokenParameters *interactiveParams = [[MSALInteractiveTokenParameters alloc] initWithScopes:scopes webviewParameters:webParameters];
[application acquireTokenWithParameters:interactiveParams completionBlock:^(MSALResult *result, NSError *error) {
    if (!error)
    {
        // You'll want to get the account identifier to retrieve and reuse the account
        // for later acquireToken calls
        NSString *accountIdentifier = result.account.identifier;

        NSString *accessToken = result.accessToken;
    }
}];
```

```swift
let viewController = ... // Pass a reference to the view controller that should be used when getting a token interactively
let webviewParameters = MSALWebviewParameters(authPresentationViewController: viewController)
let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopes, webviewParameters: webviewParameters)
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in

    guard let authResult = result, error == nil else {
        print(error!.localizedDescription)
        return
    }

    // Get access token from result
    let accessToken = authResult.accessToken
})
```

İOS ve macOS için MSAL, bir belirteci etkileşimli veya sessizce almak için çeşitli değiştiriciler destekler:
* [Belirteç almak için ortak parametreler](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALTokenParameters.html#/Configuration%20parameters)
* [Etkileşimli belirteç alma parametreleri](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALInteractiveTokenParameters.html#/Configuring%20MSALInteractiveTokenParameters)
* [Sessiz belirteç alma parametreleri](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALSilentTokenParameters.html)

#### <a name="xamarin"></a>Xamarin

Aşağıdaki örnek, bir belirteci etkileşimli olarak almak için minimum kodu gösterir. Örnek, kullanıcının profilini okumak için Microsoft Graph kullanır.

```csharp
string[] scopes = new string[] {"user.read"};
var app = PublicClientApplicationBuilder.Create(clientId).Build();
var accounts = await app.GetAccountsAsync();
AuthenticationResult result;
try
{
 result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
             .ExecuteAsync();
}
catch(MsalUiRequiredException)
{
 result = await app.AcquireTokenInteractive(scopes)
             .ExecuteAsync();
}
```

#### <a name="mandatory-parameters-in-msalnet"></a>MSAL.NET 'de zorunlu parametreler

`AcquireTokenInteractive` yalnızca bir zorunlu parametreye sahiptir: `scopes` . `scopes`Parametresi, bir belirtecin gerekli olduğu kapsamları tanımlayan dizeleri numaralandırır. Belirteç Microsoft Graph için ise, her bir Microsoft Graph API 'sinin API başvurusunda gerekli kapsamları bulabilirsiniz. Başvurunun "Izinler" bölümüne gidin.

Örneğin, [kullanıcının kişilerini listelemek](/graph/api/user-list-contacts)Için "User. Read", "Contacts. Read" kapsamını kullanın. Daha fazla bilgi için bkz. [Microsoft Graph izinleri başvurusu](/graph/permissions-reference).

Android 'de, kullanarak uygulamayı oluştururken üst etkinliği belirtebilirsiniz `PublicClientApplicationBuilder` . Bu sırada üst etkinliği belirtmezseniz, daha sonra `.WithParentActivityOrWindow` aşağıdaki bölümde olduğu gibi kullanarak belirtebilirsiniz. Üst etkinliği belirtirseniz, belirteç, etkileşimden sonra bu üst etkinliğe geri alınır. Bunu belirtmezseniz, `.ExecuteAsync()` çağrı bir özel durum oluşturur.

#### <a name="specific-optional-parameters-in-msalnet"></a>MSAL.NET içinde belirli isteğe bağlı parametreler

Aşağıdaki bölümlerde MSAL.NET içinde isteğe bağlı parametreler açıklanmaktadır.

##### <a name="withprompt"></a>WithPrompt

`WithPrompt()`Parametresi, bir istem belirterek kullanıcıyla etkileşimi denetler.

![Istem yapısındaki alanları gösteren resim. Bu sabit değerler, WithPrompt () parametresi tarafından gösterilecek istem türünü tanımlayarak kullanıcıyla etkileşimi denetler.](https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png)

Sınıfı aşağıdaki sabitleri tanımlar:

- `SelectAccount` güvenlik belirteci hizmetini (STS) hesap seçimi iletişim kutusunu sunacak şekilde zorlar. İletişim kutusu, kullanıcının oturumu olan hesapları içerir. Kullanıcının farklı kimlikler arasında seçim yapmasına izin vermek istediğinizde bu seçeneği kullanabilirsiniz. Bu seçenek, MSAL 'in `prompt=select_account` kimlik sağlayıcısına gönderilmesini sağlar.

    `SelectAccount`Bu varsayılan değerdir ve kullanılabilir bilgilere göre olası en iyi deneyimi etkin bir şekilde sunar. Kullanılabilir bilgiler hesap, Kullanıcı için bir oturumun varlığı ve bu şekilde devam edebilir. Bunu yapmak için iyi bir nedeniniz yoksa, bu varsayılanı değiştirmeyin.
- `Consent` daha önce izin verilse bile kullanıcıdan izin vermenizi ister. Bu durumda, MSAL `prompt=consent` kimlik sağlayıcısına gönderilir.

    Kuruluşun yönetimi, `Consent` kullanıcıların uygulamayı her kullandıklarında onay iletişim kutusunu görmesini gerektiren güvenlik odaklı uygulamalarda sabiti kullanmak isteyebilirsiniz.
- `ForceLogin` istem gerekli olmasa bile, hizmetin kullanıcıdan kimlik bilgilerini sormasını sağlar.

    Bu seçenek, belirteç alımı başarısız olursa ve kullanıcının yeniden oturum açmasını istiyorsanız yararlı olabilir. Bu durumda, MSAL `prompt=login` kimlik sağlayıcısına gönderilir. Bu seçeneği, kuruluş yönetimi 'nin, kullanıcının uygulamanın belirli bölümlerine her erişirken oturum açmasını gerektiren güvenlik odaklı uygulamalarda kullanmak isteyebilirsiniz.
- `Never` yalnızca .NET 4,5 ve Windows Çalışma Zamanı (WinRT) içindir. Bu sabit kullanıcıya sormaz, ancak gizli katıştırılmış Web görünümünde depolanan tanımlama bilgisini kullanmayı dener. Daha fazla bilgi için bkz. [msal.NET ile Web tarayıcıları kullanma](./msal-net-web-browsers.md).

    Bu seçenek başarısız olursa, `AcquireTokenInteractive` BIR UI etkileşiminin gerekli olduğunu bildirmek için bir özel durum oluşturur. Ardından başka bir `Prompt` parametre kullanın.
- `NoPrompt` kimlik sağlayıcısına bir istem göndermez.

    Bu seçenek yalnızca Azure Active Directory B2C düzenleme profili ilkeleri için yararlıdır. Daha fazla bilgi için bkz. [B2C özellikleri](https://aka.ms/msal-net-b2c-specificities).

##### <a name="withextrascopetoconsent"></a>Withextrascopetoonay

Değiştirici, `WithExtraScopeToConsent` kullanıcının birkaç kaynağa ön onay sağlamasını istediğiniz gelişmiş bir senaryoda kullanın. Bu değiştiriciyi, genellikle MSAL.NET veya Microsoft Identity platformu ile kullanılan artımlı izin kullanmak istemediğinizde kullanabilirsiniz. Daha fazla bilgi için, bkz. [birkaç kaynak için Kullanıcı onayını ön](scenario-desktop-production.md#have-the-user-consent-upfront-for-several-resources)alma.

Aşağıda bir kod örneği verilmiştir:

```csharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

##### <a name="other-optional-parameters"></a>Diğer isteğe bağlı parametreler

İçin diğer isteğe bağlı parametreler hakkında daha fazla bilgi edinmek için `AcquireTokenInteractive` bkz. [Acquiretokenınteractiveparameterbuilder için başvuru belgeleri](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder#methods).

### <a name="acquire-tokens-via-the-protocol"></a>Protokol aracılığıyla belirteçleri alma

Belirteçleri almak için protokolü doğrudan kullanmayı önermiyoruz. Bunu yaparsanız uygulama, çoklu oturum açma (SSO), cihaz yönetimi ve koşullu erişim ile ilgili bazı senaryoları desteklemez.

Mobil uygulamalar için belirteçleri almak üzere Protokolü kullandığınızda iki istek yapın:

* Yetkilendirme kodu alın.
* Belirteç için kodu Exchange.

#### <a name="get-an-authorization-code"></a>Yetkilendirme kodu al

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=<CLIENT_ID>
&response_type=code
&redirect_uri=<ENCODED_REDIRECT_URI>
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2F.default
&state=12345
```

#### <a name="get-access-and-refresh-the-token"></a>Erişim al ve belirteci Yenile

```HTTP
POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=<CLIENT_ID>
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=<ENCODED_REDIRECT_URI>
&grant_type=authorization_code
```

## <a name="next-steps"></a>Sonraki adımlar

Bu senaryonun [bir Web API 'Sini çağırarak bir](scenario-mobile-call-api.md)sonraki makaleye geçin.