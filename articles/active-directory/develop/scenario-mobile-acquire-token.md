---
title: Web API 'Lerini çağıran mobil uygulama-uygulama için belirteç alma
titleSuffix: Microsoft identity platform
description: Web API 'Lerini çağıran bir mobil uygulama oluşturmayı öğrenin (uygulama için belirteç alma)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b1d6a0e4bfffaaf4ed0fa836bcf62a7f1104e6a
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803773"
---
# <a name="mobile-app-that-calls-web-apis---get-a-token"></a>Web API 'Lerini çağıran mobil uygulama-belirteç al

Korumalı Web API 'Lerini çağırmaya başlamadan önce, uygulamanız bir erişim belirtecine sahip olacak. Bu makalede, Microsoft kimlik doğrulama kitaplığı 'nı (MSAL) kullanarak bir belirteç alma sürecinde adım adım açıklanır.

## <a name="scopes-to-request"></a>İstek için kapsamlar

Bir belirteç istediğinizde, bir kapsam tanımlamanız gerekir. Kapsam, uygulamanızın erişebileceği verileri belirler.  

En kolay yaklaşım, istenen Web API 'sinin `App ID URI` kapsam `.default`birleştirmenin bir yoludur. Bunun yapılması, Microsoft Identity platformunu uygulamanızın portalda ayarlanmış tüm kapsamları gerektirdiğini söyler.

#### <a name="android"></a>Android
```Java
String[] SCOPES = {"https://graph.microsoft.com/.default"};
```

#### <a name="ios"></a>iOS
```swift
let scopes = ["https://graph.microsoft.com/.default"]
```

#### <a name="xamarin"></a>Xamarin
```CSharp 
var scopes = new [] {"https://graph.microsoft.com/.default"};
```

## <a name="get-tokens"></a>Belirteçleri al

### <a name="acquire-tokens-via-msal"></a>Belirteçleri MSAL aracılığıyla alma

MSAL, uygulamaların belirteçleri sessizce ve etkileşimli olarak almasına izin verir. Yalnızca bu yöntemleri çağırın ve MSAL istenen kapsamlar için bir erişim belirteci döndürür. Doğru model, sessiz bir istek gerçekleştirmek ve etkileşimli bir isteğe geri dönememektedir.

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
// TODO: Create an interactive callback to catch successful or failed request.
sampleApp.acquireToken(getActivity(), SCOPES, getAuthInteractiveCallback());        
```

#### <a name="ios"></a>iOS

**Önce bir belirteci sessizce almaya çalışın:**

Amaç-C:

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
            
        // Access token to call the Web API
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
 
SWIFT

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
            
    // Access token to call the Web API
    let accessToken = authResult.accessToken
}
```

**MSAL `MSALErrorInteractionRequired`döndürürse belirteçleri etkileşimli olarak almaya çalışın:**

Amaç-C:

```objc
UIViewController *viewController = ...; // Pass a reference to the view controller that should be used when getting a token interactively
MSALWebviewParameters *webParameters = [[MSALWebviewParameters alloc] initWithParentViewController:viewController];
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

SWIFT

```swift
let viewController = ... // Pass a reference to the view controller that should be used when getting a token interactively
let webviewParameters = MSALWebviewParameters(parentViewController: viewController)
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

İOS ve macOS için MSAL, bir belirteci etkileşimli veya sessizce alırken çeşitli değiştiriciler destekler.
* [Belirteç alırken ortak parametreler](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALTokenParameters.html#/Configuration%20parameters)
* [Etkileşimli belirteç alımı parametreleri](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALInteractiveTokenParameters.html#/Configuring%20MSALInteractiveTokenParameters)
* [Sessiz belirteç alımı parametreleri](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALSilentTokenParameters.html)

#### <a name="xamarin"></a>Xamarin

Aşağıdaki örnek, Microsoft Graph ile kullanıcının profilini okumak üzere bir belirteci etkileşimli olarak almak için minimum kodu gösterir.

```CSharp
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

`AcquireTokenInteractive`, belirtecin gerekli olduğu kapsamları tanımlayan dizelerin bir listesini içeren ``scopes``yalnızca bir zorunlu parametre içerir. Belirteç Microsoft Graph için ise, gereken kapsamlar, "Permissions" adlı bölümdeki her bir Microsoft Graph API 'sinin API başvurusunda bulunabilir. Örneğin, [kullanıcının kişilerini listelemek](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_contacts)için, "User. Read", "Contacts. Read" kapsamının kullanılması gerekecektir. Ayrıca bkz. [Microsoft Graph izinleri başvurusu](https://developer.microsoft.com/graph/docs/concepts/permissions_reference).

Uygulamayı oluştururken belirtininiz varsa, Android 'de ana etkinliği (`.WithParentActivityOrWindow`kullanarak) da belirtmeniz gerekir. böylece, belirtecin etkileşime geçtikten sonra bu üst etkinliğe geri dönmesi sağlanır. Belirtmezseniz, `.ExecuteAsync()`çağrılırken bir özel durum oluşturulur.

#### <a name="specific-optional-parameters-in-msalnet"></a>MSAL.NET içinde belirli isteğe bağlı parametreler

##### <a name="withprompt"></a>WithPrompt

`WithPrompt()` bir Istem belirterek kullanıcıyla etkileşimi denetlemek için kullanılır

<img src="https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png" width="25%" />

Sınıfı aşağıdaki sabitleri tanımlar:

- ``SelectAccount``: STS 'nin, kullanıcının oturumu olan hesapları içeren hesap seçme iletişim kutusunu sunmasını zorlayacaktır. Bu seçenek, uygulamalar geliştiriciler kullanıcıların farklı kimlikler arasında seçim yapmasına izin vermek istediğinizde yararlıdır. Bu seçenek, kimlik sağlayıcısına ``prompt=select_account`` göndermek için MSAL. Bu seçenek varsayılandır ve kullanılabilir bilgilere (hesap, Kullanıcı için bir oturumun varlığına vb.) göre olası en iyi deneyimi sağlamanın iyi bir işidir. ...). Bunu yapmak için iyi bir nedeniniz olmadığı müddetçe değiştirmeyin.
- ``Consent``: daha önce izin verilse bile, uygulama geliştiricisinin kullanıcıyı onay istenmesini sağlar. Bu durumda, MSAL kimlik sağlayıcısına `prompt=consent` gönderir. Bu seçenek, kuruluşun yönetilebileceği bazı güvenlik odaklı uygulamalarda, uygulamanın her kullanıldığı her seferinde kullanıcıdan onay iletişim kutusunu sunduğunu talep etmek için kullanılabilir.
- ``ForceLogin``: Bu Kullanıcı istemi gerekli olmasa bile, uygulama geliştiricisinin kullanıcıya hizmet tarafından kimlik bilgileri istenmesini sağlar. Bu seçenek, kullanıcının yeniden oturum açmasını sağlamak için bir belirteç alma başarısız olursa yararlı olabilir. Bu durumda, MSAL kimlik sağlayıcısına `prompt=login` gönderir. Bu durumda, kuruluşun idare ettiği bazı güvenlik odaklı uygulamalarda, uygulamanın belirli bölümlerine her erişirken kullanıcı tarafından yeniden oturum açtığı görüldü.
- ``Never`` (yalnızca .NET 4,5 ve WinRT için) kullanıcıya sormaz, bunun yerine gizli katıştırılmış Web görünümünde depolanan tanımlama bilgisini kullanmayı dener (bkz. MSAL.NET 'de Web views). Bu seçeneğin kullanılması başarısız olabilir ve bu durumda `AcquireTokenInteractive`, Kullanıcı arabirimi etkileşiminin gerekli olduğunu bildirmek için bir özel durum oluşturur ve başka bir `Prompt` parametresi kullanmanız gerekir.
- ``NoPrompt``: kimlik sağlayıcısına hiçbir istem göndermez. Bu seçenek yalnızca Azure AD B2C düzenleme profili ilkeleri için yararlıdır ( [B2C özelliklerine](https://aka.ms/msal-net-b2c-specificities)bakın).

##### <a name="withextrascopetoconsent"></a>Withextrascopetoonay

Bu değiştirici, kullanıcının birden fazla kaynağa ön onay vermesini istediğiniz (ve normalde MSAL.NET/Microsoft Identity platform v 2.0 ile kullanılan artımlı onayı kullanmak istemediğiniz) gelişmiş bir senaryoda kullanılır. Ayrıntılar için bkz. [nasıl yapılır: Kullanıcı onayını birkaç kaynağın önüne](scenario-desktop-production.md#how-to-have--the-user-consent-upfront-for-several-resources)alma.

```CSharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

##### <a name="other-optional-parameters"></a>Diğer isteğe bağlı parametreler

[Acquiretokenınteractiveparameterbuilder](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder?view=azure-dotnet-preview#methods) başvuru belgelerindeki `AcquireTokenInteractive` için diğer isteğe bağlı parametreler hakkında daha fazla bilgi edinin

### <a name="acquire-tokens-via-the-protocol"></a>Protokol aracılığıyla belirteçleri alma

Protokolü doğrudan kullanmanızı önermiyoruz. Bunu yaparsanız uygulama, bazı çoklu oturum açma (SSO), cihaz yönetimi ve koşullu erişim senaryolarını desteklemez.

Mobil uygulamalar için belirteçleri almak üzere Protokolü kullandığınızda iki istek yapmanız gerekir: bir yetkilendirme kodu alın ve belirteç için Exchange 'i değiştirin.

#### <a name="get-authorization-code"></a>Yetkilendirme kodunu al

```Text
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=<CLIENT_ID>
&response_type=code
&redirect_uri=<ENCODED_REDIRECT_URI>
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2F.default
&state=12345
```

#### <a name="get-access-and-refresh-token"></a>Erişim ve yenileme belirtecini al

```Text
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

> [!div class="nextstepaction"]
> [Web API 'SI çağırma](scenario-mobile-call-api.md)
