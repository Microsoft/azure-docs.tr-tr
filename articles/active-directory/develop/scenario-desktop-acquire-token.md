---
title: Bir Web API 'SI (masaüstü uygulaması) çağırmak için belirteç alma | Mavisi
titleSuffix: Microsoft identity platform
description: Uygulama için bir belirteç almak üzere Web API 'Lerini çağıran bir masaüstü uygulaması oluşturmayı öğrenin
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/06/2021
ms.author: jmprieur
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: d45c40bb6878da80f68fff9642b55da68706743a
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107305846"
---
# <a name="desktop-app-that-calls-web-apis-acquire-a-token"></a>Web API 'Lerini çağıran masaüstü uygulaması: belirteç alma

Ortak istemci uygulamasının bir örneğini oluşturduktan sonra, bunu kullanarak bir Web API 'sini çağırmak için kullanacağınız bir belirteç elde edersiniz.

## <a name="recommended-pattern"></a>Önerilen model

Web API 'SI tarafından tanımlanır `scopes` . Uygulamanızda sağladığınız deneyim ne olursa olsun kullanılacak desenler:

- Çağırarak, çağırarak belirteç önbelleğinden bir belirteç almayı deneyin `AcquireTokenSilent` .
- Bu çağrı başarısız olursa, `AcquireToken` kullanmak istediğiniz akışı kullanın, burada tarafından gösterilir `AcquireTokenXX` .

# <a name="net"></a>[.NET](#tab/dotnet)

### <a name="in-msalnet"></a>MSAL.NET içinde

```csharp
AuthenticationResult result;
var accounts = await app.GetAccountsAsync();
IAccount account = ChooseAccount(accounts); // for instance accounts.FirstOrDefault
                                            // if the app manages is at most one account
try
{
 result = await app.AcquireTokenSilent(scopes, account)
                   .ExecuteAsync();
}
catch(MsalUiRequiredException ex)
{
  result = await app.AcquireTokenXX(scopes, account)
                    .WithOptionalParameterXXX(parameter)
                    .ExecuteAsync();
}
```

# <a name="java"></a>[Java](#tab/java)

```java

Set<IAccount> accountsInCache = pca.getAccounts().join();
// Take first account in the cache. In a production application, you would filter
// accountsInCache to get the right account for the user authenticating.
IAccount account = accountsInCache.iterator().next();

IAuthenticationResult result;
try {
    SilentParameters silentParameters =
            SilentParameters
                    .builder(SCOPE, account)
                    .build();

    // try to acquire token silently. This call will fail since the token cache
    // does not have any data for the user you are trying to acquire a token for
    result = pca.acquireTokenSilently(silentParameters).join();
} catch (Exception ex) {
    if (ex.getCause() instanceof MsalException) {

        InteractiveRequestParameters parameters = InteractiveRequestParameters
                .builder(new URI("http://localhost"))
                .scopes(SCOPE)
                .build();

        // Try to acquire a token interactively with system browser. If successful, you should see
        // the token and account information printed out to console
        result = pca.acquireToken(parameters).join();
    } else {
        // Handle other exceptions accordingly
        throw ex;
    }
}
return result;

```

# <a name="macos"></a>[macOS](#tab/macOS)

### <a name="in-msal-for-ios-and-macos"></a>İOS ve macOS için MSAL içinde

Amaç-C:

```objc
MSALAccount *account = [application accountForIdentifier:accountIdentifier error:nil];

MSALSilentTokenParameters *silentParams = [[MSALSilentTokenParameters alloc] initWithScopes:scopes account:account];
[application acquireTokenSilentWithParameters:silentParams completionBlock:^(MSALResult *result, NSError *error) {

    // Check the error
    if (error && [error.domain isEqual:MSALErrorDomain] && error.code == MSALErrorInteractionRequired)
    {
        // Interactive auth will be required, call acquireTokenWithParameters:error:
    }
}];
```
SWIFT

```swift
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
}
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

MSAL düğümünde, kod değişimi (PKCE) için kanıt anahtarı ile yetkilendirme kodu akışı aracılığıyla belirteçleri elde edersiniz. MSAL düğümü, önbellekte herhangi bir kullanıcı hesabı olup olmadığını görmek için bellek içi belirteç önbelleği kullanır. Varsa, `acquireTokenSilent()` bir önbelleğe alınmış erişim belirtecini almak için hesap nesnesi yöntemine geçirilebilir.

```JavaScript

const msal = require("@azure/msal-node");

const msalConfig = {
    auth: {
        clientId: "your_client_id_here",
        authority: "your_authority_here",
    }
};

const pca = new msal.PublicClientApplication(msalConfig);
const msalTokenCache = pca.getTokenCache();

let accounts = await msalTokenCache.getAllAccounts();

    if (accounts.length > 0) {

        const silentRequest = {
            account: accounts[0], // Index must match the account that is trying to acquire token silently
            scopes: ["user.read"],
        };
    
        pca.acquireTokenSilent(silentRequest).then((response) => {
            console.log("\nSuccessful silent token acquisition");
            console.log("\nResponse: \n:", response);
            res.sendStatus(200);
        }).catch((error) => console.log(error));
    } else {
        const {verifier, challenge} = await msal.cryptoProvider.generatePkceCodes();

        const authCodeUrlParameters = {
            scopes: ["User.Read"],
            redirectUri: "your_redirect_uri",
            codeChallenge: challenge, // PKCE Code Challenge
            codeChallengeMethod: "S256" // PKCE Code Challenge Method 
        };
        
        // get url to sign user in and consent to scopes needed for application
        pca.getAuthCodeUrl(authCodeUrlParameters).then((response) => {
            console.log(response);
        
            const tokenRequest = {
                code: response["authorization_code"],
                codeVerifier: verifier // PKCE Code Verifier 
                redirectUri: "your_redirect_uri",
                scopes: ["User.Read"],
            };
            
            // acquire a token by exchanging the code
            pca.acquireTokenByCode(tokenRequest).then((response) => {
                console.log("\nResponse: \n:", response);
            }).catch((error) => {
                console.log(error);
            });
        }).catch((error) => console.log(JSON.stringify(error)));
    }
```

# <a name="python"></a>[Python](#tab/python)

```Python
result = None

# Firstly, check the cache to see if this end user has signed in before
accounts = app.get_accounts(username=config["username"])
if accounts:
    result = app.acquire_token_silent(config["scope"], account=accounts[0])

if not result:
    result = app.acquire_token_by_xxx(scopes=config["scope"])
```
---

Bir masaüstü uygulamasında belirteçleri edinmenin çeşitli yolları aşağıda verilmiştir.

## <a name="acquire-a-token-interactively"></a>Bir belirteci etkileşimli olarak alma

Aşağıdaki örnek, Microsoft Graph ile kullanıcının profilini okumak üzere bir belirteci etkileşimli olarak almak için minimum kodu gösterir.

# <a name="net"></a>[.NET](#tab/dotnet)

### <a name="in-msalnet"></a>MSAL.NET içinde

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

### <a name="mandatory-parameters"></a>Zorunlu parametreler

`AcquireTokenInteractive` yalnızca bir zorunlu parametreye sahiptir, bu, ``scopes`` belirtecin gerekli olduğu kapsamları tanımlayan dizelerin bir listesini içerir. Belirteç Microsoft Graph için ise, gerekli kapsamlar, "Permissions" adlı bölümdeki her bir Microsoft Graph API 'sinin API başvurusunda bulunabilir. Örneğin, [kullanıcının kişilerini listelemek](/graph/api/user-list-contacts)için, "User. Read", "Contacts. Read" kapsamının kullanılması gerekir. Daha fazla bilgi için bkz. [Microsoft Graph izinleri başvurusu](/graph/permissions-reference).

Android 'de, ayrıca gösterildiği gibi ana etkinliği de belirtmeniz gerekir. bu `.WithParentActivityOrWindow` sayede, belirtecin etkileşimden sonra bu üst etkinliğe geri dönmesi sağlanır. Bunu belirtmezseniz, çağrılırken bir özel durum oluşturulur `.ExecuteAsync()` .

### <a name="specific-optional-parameters-in-msalnet"></a>MSAL.NET içinde belirli isteğe bağlı parametreler

#### <a name="withparentactivityorwindow"></a>WithParentActivityOrWindow

Etkileşimli olduğundan, Kullanıcı arabirimi önemlidir. `AcquireTokenInteractive` , kendisini destekleyen platformlar için üst Kullanıcı arabirimini belirten belirli bir isteğe bağlı parametreye sahiptir. Bir masaüstü uygulamasında kullanıldığında, `.WithParentActivityOrWindow` platforma bağlı olan farklı bir türü vardır. Alternatif olarak, oturum açma iletişim kutusunun ekranda nerede göründüğünü denetlemek istemediğiniz bir pencere oluşturmak için isteğe bağlı üst pencere parametresini atlayabilirsiniz. Bu durum, komut satırı tabanlı uygulamalar için geçerli olur, başka bir arka uç hizmetine çağrı geçirmek için kullanılır ve herhangi bir Windows for User etkileşimi gerekmez.

```csharp
// net45
WithParentActivityOrWindow(IntPtr windowPtr)
WithParentActivityOrWindow(IWin32Window window)

// Mac
WithParentActivityOrWindow(NSWindow window)

// .NET Standard (this will be on all platforms at runtime, but only on NetStandard at build time)
WithParentActivityOrWindow(object parent).
```

Açıklamalarının

- .NET Standard, Android 'de, `object` iOS 'ta, `Activity` `UIViewController` `NSWindow` Mac 'te ve `IWin32Window` veya `IntPr` Windows üzerinde bekleniyor.
- Windows 'da, `AcquireTokenInteractive` katıştırılmış tarayıcının uygun UI Eşitleme bağlamını alması IÇIN UI iş parçacığından çağırmanız gerekir. UI iş parçacığından çağrılmayan iletiler, Kullanıcı arabirimi ile düzgün şekilde ve kilitlenme senaryolarıyla karşılıklı olarak alınmamasına neden olabilir. Kullanıcı ARABIRIMI iş parçacığında yoksa, WPF üzerinde yoksa, Kullanıcı arabirimi iş parçacığından Microsoft kimlik doğrulama kitaplıklarını (MSALs) çağırmanın bir yolu `Dispatcher` .
- WPF kullanıyorsanız, WPF denetiminden bir pencere almak için `WindowInteropHelper.Handle` sınıfını kullanabilirsiniz. Sonra çağrı bir WPF denetiminden ( `this` ) yapılır:

  ```csharp
  result = await app.AcquireTokenInteractive(scopes)
                    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
                    .ExecuteAsync();
  ```

#### <a name="withprompt"></a>WithPrompt

`WithPrompt()` bir istem belirterek kullanıcıyla etkileşimi denetlemek için kullanılır.

![Istem yapısındaki alanları gösteren resim. Bu sabit değerler, WithPrompt () yöntemi tarafından gösterilecek istem türünü tanımlayarak kullanıcıyla etkileşimi denetler.](https://user-images.githubusercontent.com/34331512/112267137-3f1c3a00-8c32-11eb-97fb-33604311329a.png)

Sınıfı aşağıdaki sabitleri tanımlar:

- ``SelectAccount`` STS 'yi, kullanıcının oturumu olan hesapları içeren hesap seçimi iletişim kutusunu sunacak şekilde zorlar. Bu seçenek, uygulama geliştiricileri kullanıcıların farklı kimlikler arasında seçim yapmasına izin vermek istediğinizde yararlıdır. Bu seçenek, MSAL 'in ``prompt=select_account`` kimlik sağlayıcısına gönderilmesini sağlar. Bu seçenek varsayılandır. Hesap ve Kullanıcı için bir oturumun varlığı gibi kullanılabilir bilgileri temel alan mümkün olan en iyi deneyimi sağlamanın iyi bir işi vardır. Bunu yapmak için iyi bir nedeniniz olmadığı müddetçe değiştirmeyin.
- ``Consent`` daha önce izin verilse bile, uygulama geliştiricisinin kullanıcıyı izin sorulmasını zorunlu hale getirir. Bu durumda, MSAL `prompt=consent` kimlik sağlayıcısına gönderilir. Bu seçenek, kuruluşun idare ettiği bazı güvenlik odaklı uygulamalarda, uygulamanın her kullanıldığı her seferinde kullanıcının izin iletişim kutusuyla sunulmasını talep ettiği durumlarda kullanılabilir.
- ``ForceLogin`` uygulama geliştiricisinin, bu kullanıcı istemi gerekli olmasa bile, kullanıcıya hizmet tarafından kimlik bilgileri istenmesini sağlar. Bu seçenek, bir belirteci almak başarısız olursa kullanıcının yeniden oturum açmasını sağlamak için yararlı olabilir. Bu durumda, MSAL `prompt=login` kimlik sağlayıcısına gönderilir. Bazen, kuruluşun idare ettiği güvenlik odaklı uygulamalarda, uygulamanın belirli bölümlerine her erişirken kullanıcının yeniden kaydolmasını talep ettiği durumlarda kullanılır.
- ``Create`` , kimlik sağlayıcısına göndererek dış kimlikler için kullanılan bir kaydolma deneyimini tetikler `prompt=create` . Bu istem Azure AD B2C uygulamalar için gönderilmemelidir. Daha fazla bilgi için bkz. [bir uygulamaya self servis kaydolma Kullanıcı akışı ekleme](https://aka.ms/msal-net-prompt-create).
- ``Never`` (yalnızca .NET 4,5 ve WinRT için) kullanıcıya sormaz, bunun yerine gizli katıştırılmış Web görünümünde depolanan tanımlama bilgisini kullanmayı dener. Daha fazla bilgi için bkz. MSAL.NET içindeki Web views. Bu seçeneğin kullanılması başarısız olabilir. Bu durumda, `AcquireTokenInteractive` BIR UI etkileşiminin gerekli olduğunu bildirmek için bir özel durum oluşturur. Başka bir parametre kullanmanız gerekir `Prompt` .
- ``NoPrompt`` kimlik sağlayıcısına hiçbir istem göndermez. Bu seçenek yalnızca Azure Active Directory (Azure AD) B2C düzenleme profili ilkeleri için yararlıdır. Daha fazla bilgi için bkz. [Azure AD B2C özellikleri](https://aka.ms/msal-net-b2c-specificities).

#### <a name="withuseembeddedwebview"></a>WithUseEmbeddedWebView

Bu yöntem, ekli bir Web görünümü veya sistem Web görünümü kullanımını zorlamak istediğinizi belirtmenize olanak sağlar (varsa). Daha fazla bilgi için bkz. [Web tarayıcıları kullanımı](msal-net-web-browsers.md).

 ```csharp
 var result = await app.AcquireTokenInteractive(scopes)
                   .WithUseEmbeddedWebView(true)
                   .ExecuteAsync();
  ```

#### <a name="withextrascopetoconsent"></a>Withextrascopetoonay

Bu değiştirici, kullanıcının birkaç kaynağa ön onay vermesini istediğiniz ve normal olarak MSAL.NET/the Microsoft Identity platform ile kullanılan artımlı onay kullanmak istemediğiniz gelişmiş bir senaryoda kullanılır. Daha fazla bilgi için, bkz. [birkaç kaynak için Kullanıcı onayını ön](scenario-desktop-production.md#have-the-user-consent-upfront-for-several-resources)alma.

```csharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

#### <a name="withcustomwebui"></a>WithCustomWebUi

Web Kullanıcı arabirimi, tarayıcıyı çağırmak için bir mekanizmadır. Bu mekanizma, adanmış bir UI WebBrowser denetimi veya tarayıcıyı açmayı devretmek için bir yol olabilir.
MSAL çoğu platform için Web ARABIRIMI uygulamaları sağlar, ancak tarayıcıyı kendiniz barındırmak isteyebileceğiniz durumlar vardır:

- MSAL tarafından açıkça kapsanmayan platformlar, örneğin, Blazor, Unity ve mono masaüstleri.
- Uygulamanızı test etmek ve Selenium ile kullanılabilecek bir otomatik tarayıcı kullanmak istiyorsunuz.
- Tarayıcı ve MSAL çalıştıran uygulama ayrı işlemlerdir.

##### <a name="at-a-glance"></a>Bir bakışta

Bunu başarmak için, `start Url` son kullanıcının Kullanıcı adı gibi öğeler girebilmesi için bir tarayıcıda gösterilmesi gereken msal ' a izin verirsiniz.
Kimlik doğrulaması tamamlandıktan sonra, uygulamanızın `end Url` Azure AD tarafından sunulan bir kodu IÇEREN msal 'e geri geçirmesi gerekir.
Konağı `end Url` her zaman `redirectUri` . `end Url`Bunu yapmak için, aşağıdaki işlemlerden birini yapın:

- Bu, tarayıcıya kadar tarayıcı yeniden yönlendirmelerini izler `redirect Url` .
- Tarayıcının, izlediğiniz bir URL 'ye yönlendirilmesini sağlayabilirsiniz.

##### <a name="withcustomwebui-is-an-extensibility-point"></a>WithCustomWebUi bir genişletilebilirlik noktasıdır

`WithCustomWebUi` , genel istemci uygulamalarında kendi Kullanıcı arabiriminizi sağlamak için kullanabileceğiniz bir genişletilebilirlik noktasıdır. Ayrıca, kullanıcının kimlik sağlayıcısının/Yetkilendir uç noktasında dolaşmasına ve oturum açmasını ve izin vermesini sağlayabilirsiniz. MSAL.NET daha sonra kimlik doğrulama kodunu kullanabilir ve bir belirteç alabilir. Örneğin, Visual Studio 'da, elektriler uygulamalarının (örneğin, Visual Studio geri bildirimi) Web etkileşimini sağlaması için kullanılır, ancak işin büyük bir bölümünü yapmak için MSAL.NET olarak bırakın. UI Otomasyonu sağlamak isterseniz de kullanabilirsiniz. Ortak istemci uygulamalarında MSAL.NET, güvenliğin sağlandığından emin olmak için kod değişimi (PKCE) standardı için düzeltme anahtarını kullanır. Kodu yalnızca MSAL.NET kullanabilir. Daha fazla bilgi için bkz. [RFC 7636-OAuth genel istemcileri tarafından kod alışverişi Için düzeltme anahtarı](https://tools.ietf.org/html/rfc7636).

  ```csharp
  using Microsoft.Identity.Client.Extensions;
  ```

##### <a name="use-withcustomwebui"></a>WithCustomWebUi kullanma

Kullanmak için `.WithCustomWebUI` aşağıdaki adımları izleyin.

  1. `ICustomWebUi` arabirimini gerçekleştirin. Daha fazla bilgi için [Bu Web sitesine](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/src/Microsoft.Identity.Client/Extensibility/ICustomWebUI.cs#L32-L70)bakın. Tek bir `AcquireAuthorizationCodeAsync` Yöntem uygulayın ve msal.NET tarafından hesaplanan yetkilendirme kodu URL 'sini kabul edin. Daha sonra kullanıcının kimlik sağlayıcısı ile etkileşimle gezinmesini sağlar ve kimlik sağlayıcısının, yetkilendirme koduyla birlikte uygulamanızı geri çağıracağından URL 'YI geri döndürmesidir. Sorunlarınız varsa, uygulamanız `MsalExtensionException` msal ile sorunsuz bir şekilde çalışmak için bir özel durum oluşturması gerekir.
  2. `AcquireTokenInteractive`Çağrın, `.WithCustomUI()` özel Web UI 'nizin örneğini geçirme değiştiricisini kullanın.

     ```csharp
     result = await app.AcquireTokenInteractive(scopes)
                       .WithCustomWebUi(yourCustomWebUI)
                       .ExecuteAsync();
     ```

##### <a name="examples-of-implementation-of-icustomwebui-in-test-automation-seleniumwebui"></a>Test Otomasyonu 'nda ıcustomwebui 'ın uygulama örnekleri: SeleniumWebUI

MSAL.NET ekibi, bu genişletilebilirlik mekanizmasını kullanmak için UI testlerini yeniden yazıldı. İlgileniyorsanız, MSAL.NET kaynak kodundaki [Seleniumwebui](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/tests/Microsoft.Identity.Test.Integration/Infrastructure/SeleniumWebUI.cs#L15-L160) sınıfına bakın.

##### <a name="provide-a-great-experience-with-systemwebviewoptions"></a>SystemWebViewOptions ile harika bir deneyim sağlayın

MSAL.NET 4,1 ' den şunları [`SystemWebViewOptions`](/dotnet/api/microsoft.identity.client.systemwebviewoptions) belirtebilirsiniz:

- `BrowserRedirectError` `HtmlMessageError` Sistem Web tarayıcısında oturum açma veya onay hataları durumunda görüntülenecek URI () veya HTML parçası ().
- `BrowserRedirectSuccess` `HtmlMessageSuccess` Başarılı oturum açma veya onay durumunda görüntülenecek URI () veya HTML parçası ().
- Sistem tarayıcısını başlatmak için çalıştırılacak eylem. Temsilciyi ayarlayarak kendi uygulamanızı sağlayabilirsiniz `OpenBrowserAsync` . Sınıfı iki tarayıcı için de varsayılan bir uygulama sağlar: `OpenWithEdgeBrowserAsync` ve `OpenWithChromeEdgeBrowserAsync` Microsoft Edge ve [Microsoft Edge for kmıum üzerinde](https://www.windowscentral.com/faq-edge-chromium)sırasıyla.

Bu yapıyı kullanmak için aşağıdaki örneğe benzer bir şey yazın:

```csharp
IPublicClientApplication app;
...

options = new SystemWebViewOptions
{
 HtmlMessageError = "<b>Sign-in failed. You can close this tab ...</b>",
 BrowserRedirectSuccess = "https://contoso.com/help-for-my-awesome-commandline-tool.html"
};

var result = app.AcquireTokenInteractive(scopes)
                .WithEmbeddedWebView(false)       // The default in .NET Core
                .WithSystemWebViewOptions(options)
                .Build();
```

#### <a name="other-optional-parameters"></a>Diğer isteğe bağlı parametreler

İçin diğer tüm isteğe bağlı parametreler hakkında daha fazla bilgi edinmek için `AcquireTokenInteractive` bkz. [Acquiretokenınteractiveparameterbuilder](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder#methods).

# <a name="java"></a>[Java](#tab/java)

```java
private static IAuthenticationResult acquireTokenInteractive() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();

        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            InteractiveRequestParameters parameters = InteractiveRequestParameters
                    .builder(new URI("http://localhost"))
                    .scopes(SCOPE)
                    .build();

            // Try to acquire a token interactively with system browser. If successful, you should see
            // the token and account information printed out to console
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="macos"></a>[macOS](#tab/macOS)

### <a name="in-msal-for-ios-and-macos"></a>İOS ve macOS için MSAL içinde

Amaç-C:

```objc
MSALInteractiveTokenParameters *interactiveParams = [[MSALInteractiveTokenParameters alloc] initWithScopes:scopes webviewParameters:[MSALWebviewParameters new]];
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
let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopes, webviewParameters: MSALWebviewParameters())
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in

    guard let authResult = result, error == nil else {
        print(error!.localizedDescription)
        return
    }

    // Get access token from result
    let accessToken = authResult.accessToken
})
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

MSAL düğümünde, kod değişimi (PKCE) için kanıt anahtarı ile yetkilendirme kodu akışı aracılığıyla belirteçleri elde edersiniz. İşlemin iki adımı vardır: ilk olarak, uygulama bir yetkilendirme kodu oluşturmak için kullanılabilen bir URL 'YI alır. Bu URL, kullanıcının kimlik bilgilerini girebileceğiniz bir tarayıcıda açılabilir ve `redirectUri` bir yetkilendirme koduyla (uygulama kaydı sırasında kaydedilir) yeniden yönlendirilir. İkincisi, uygulama, alınan yetkilendirme kodunu `acquireTokenByCode()` bir erişim belirteci için değiş tokuş eden yönteme geçirir.

```JavaScript
const msal = require("@azure/msal-node");

const msalConfig = {
    auth: {
        clientId: "your_client_id_here",
        authority: "your_authority_here",
    }
};

const pca = new msal.PublicClientApplication(msalConfig);

const {verifier, challenge} = await msal.cryptoProvider.generatePkceCodes();

const authCodeUrlParameters = {
    scopes: ["User.Read"],
    redirectUri: "your_redirect_uri",
    codeChallenge: challenge, // PKCE Code Challenge
    codeChallengeMethod: "S256" // PKCE Code Challenge Method 
};

// get url to sign user in and consent to scopes needed for application
pca.getAuthCodeUrl(authCodeUrlParameters).then((response) => {
    console.log(response);

    const tokenRequest = {
        code: response["authorization_code"],
        codeVerifier: verifier // PKCE Code Verifier 
        redirectUri: "your_redirect_uri",
        scopes: ["User.Read"],
    };
    
    // acquire a token by exchanging the code
    pca.acquireTokenByCode(tokenRequest).then((response) => {
        console.log("\nResponse: \n:", response);
    }).catch((error) => {
        console.log(error);
    });
}).catch((error) => console.log(JSON.stringify(error)));
```

# <a name="python"></a>[Python](#tab/python)

MSAL Python doğrudan bir etkileşimli alma belirteci yöntemi sağlamaz. Bunun yerine, bir yetkilendirme kodu almak için uygulamanın kullanıcı etkileşimi akışı uygulamasına bir yetkilendirme isteği göndermesini gerektirir. Daha sonra bu kod, `acquire_token_by_authorization_code` belirteci almak için yöntemine geçirilebilir.

```Python
result = None

# Firstly, check the cache to see if this end user has signed in before
accounts = app.get_accounts(username=config["username"])
if accounts:
    result = app.acquire_token_silent(config["scope"], account=accounts[0])

if not result:
    result = app.acquire_token_by_authorization_code(
         request.args['code'],
         scopes=config["scope"])

```
---

## <a name="integrated-windows-authentication"></a>Tümleşik Windows Kimlik Doğrulaması

Bir etki alanında veya Azure AD 'ye katılmış makinede bir etki alanı kullanıcısının oturum açması için tümleşik Windows kimlik doğrulaması (ıWA) kullanın.

### <a name="constraints"></a>Kısıtlamalar

- Tümleşik Windows kimlik doğrulaması yalnızca *federe +* Kullanıcı, diğer bir deyişle, Active Directory ve Azure AD tarafından desteklenen kullanıcılar için kullanılabilir. *Yönetilen* kullanıcılar olarak bilinen Active Directory yedekleme olmadan doğrudan Azure AD 'de oluşturulan kullanıcılar bu kimlik doğrulama akışını kullanamaz. Bu sınırlama, Kullanıcı adı ve parola akışını etkilemez.
- IWA, .NET Framework, .NET Core ve Evrensel Windows Platformu (UWP) platformları için yazılan uygulamalar içindir.
- IWA [Multi-Factor Authentication 'ı (MFA)](../authentication/concept-mfa-howitworks.md)atlamaz. MFA yapılandırıldıysa, MFA Kullanıcı etkileşimi gerektirdiğinden, bir MFA sınaması gerekliyse ıWA başarısız olabilir.
  
    IWA etkileşimli değil, ancak MFA Kullanıcı etkileşimi gerektirir. Kimlik sağlayıcısının MFA istediğinde, kiracı yöneticisinin ne zaman yapılacağını kontrol etmeyin. Farklı bir ülke/bölgeden oturum açtığınızda MFA, bir kurumsal ağa VPN aracılığıyla bağlı olmadığında ve bazen VPN aracılığıyla bağlandığında bile gereklidir. Belirleyici bir kural kümesi beklenmeyin. Azure AD, MFA 'nın gerekli olup olmadığını sürekli olarak öğrenmek için AI 'yi kullanır. IWA başarısız olursa etkileşimli kimlik doğrulama veya cihaz kodu akışı gibi bir Kullanıcı istemine geri dönün.

- Geçirilen yetkilinin `PublicClientApplicationBuilder` olması gerekir:
  - Form, `https://login.microsoftonline.com/{tenant}/` `tenant` Kiracı kimliğini veya kiracı ile ilişkili bir etki alanını temsıl eden GUID 'dir.
  - Tüm iş ve okul hesapları için: `https://login.microsoftonline.com/organizations/` .
  - Microsoft kişisel hesapları desteklenmez. Sık karşılaşılan veya/tüketicilere kiracılar kullanamazsınız.

- Tümleşik Windows kimlik doğrulaması, sessiz bir akışdır:
  - Uygulamanızın kullanıcısının, uygulamayı kullanmak için önceden verilmiş olması gerekir.
  - Ya da, kiracı yöneticisinin uygulamayı kullanabilmesi için Kiracıdaki tüm kullanıcılara daha önce sahip olması gerekir.
  - Diğer bir deyişle:
    - Geliştirici, Azure portal için **Izin ver** düğmesini seçti.
    - Ya da bir kiracı yöneticisi, uygulamanın kaydının **API izinleri** sekmesindeki **{kiracı etki alanı} Için yönetici onayını ver/iptal et** ' i seçti. Daha fazla bilgi için bkz. [Web API 'nize erişmek için Izin ekleme](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api).
    - Ya da kullanıcıların uygulamayı onaylaması için bir yol sağladınız. Daha fazla bilgi için bkz. [bireysel kullanıcı Izni isteme](./v2-permissions-and-consent.md#requesting-individual-user-consent).
    - Ya da, kiracı yöneticisinin uygulamayı onaylaması için bir yol sağladınız. Daha fazla bilgi için bkz. [yönetici onayı](./v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant).

- Bu akış .NET Masaüstü, .NET Core ve UWP uygulamaları için etkinleştirilmiştir.

Onay hakkında daha fazla bilgi için bkz. [Microsoft Identity platform izinleri ve onayı](./v2-permissions-and-consent.md).

### <a name="learn-how-to-use-it"></a>Nasıl kullanacağınızı öğrenin

# <a name="net"></a>[.NET](#tab/dotnet)

MSAL.NET ' de şunu kullanın:

```csharp
AcquireTokenByIntegratedWindowsAuth(IEnumerable<string> scopes)
```

Normalde yalnızca bir parametreye () ihtiyacınız vardır `scopes` . Windows yöneticinizin ilkeleri ayarlama şekline bağlı olarak, Windows makinenizde bulunan uygulamaların oturum açan kullanıcıyı arama izni olmayabilir. Bu durumda, ikinci bir yöntemi kullanın `.WithUsername()` ve oturum açmış kullanıcının Kullanıcı adını UPN biçimi olarak (örneğin,) geçirin `joe@contoso.com` .

Aşağıdaki örnek, alabileceğiniz özel durumların ve bunların azaltmaları hakkındaki açıklamaları içeren en güncel durumu gösterir.

```csharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app = PublicClientApplicationBuilder
      .Create(clientId)
      .WithAuthority(authority)
      .Build();

 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
      .ExecuteAsync();
 }
 else
 {
  try
  {
   result = await app.AcquireTokenByIntegratedWindowsAuth(scopes)
      .ExecuteAsync(CancellationToken.None);
  }
  catch (MsalUiRequiredException ex)
  {
   // MsalUiRequiredException: AADSTS65001: The user or administrator has not consented to use the application
   // with ID '{appId}' named '{appName}'.Send an interactive authorization request for this user and resource.

   // you need to get user consent first. This can be done, if you are not using .NET Core (which does not have any Web UI)
   // by doing (once only) an AcquireToken interactive.

   // If you are using .NET core or don't want to do an AcquireTokenInteractive, you might want to suggest the user to navigate
   // to a URL to consent: https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read

   // AADSTS50079: The user is required to use multi-factor authentication.
   // There is no mitigation - if MFA is configured for your tenant and AAD decides to enforce it,
   // you need to fallback to an interactive flows such as AcquireTokenInteractive or AcquireTokenByDeviceCode
   }
   catch (MsalServiceException ex)
   {
    // Kind of errors you could have (in ex.Message)

    // MsalServiceException: AADSTS90010: The grant type is not supported over the /common or /consumers endpoints. Please use the /organizations or tenant-specific endpoint.
    // you used common.
    // Mitigation: as explained in the message from Azure AD, the authority needs to be tenanted or otherwise organizations

    // MsalServiceException: AADSTS70002: The request body must contain the following parameter: 'client_secret or client_assertion'.
    // Explanation: this can happen if your application was not registered as a public client application in Azure AD
    // Mitigation: in the Azure portal, edit the manifest for your application and set the `allowPublicClient` to `true`
   }
   catch (MsalClientException ex)
   {
      // Error Code: unknown_user Message: Could not identify logged in user
      // Explanation: the library was unable to query the current Windows logged-in user or this user is not AD or AAD
      // joined (work-place joined users are not supported).

      // Mitigation 1: on UWP, check that the application has the following capabilities: Enterprise Authentication,
      // Private Networks (Client and Server), User Account Information

      // Mitigation 2: Implement your own logic to fetch the username (e.g. john@contoso.com) and use the
      // AcquireTokenByIntegratedWindowsAuth form that takes in the username

      // Error Code: integrated_windows_auth_not_supported_managed_user
      // Explanation: This method relies on an a protocol exposed by Active Directory (AD). If a user was created in Azure
      // Active Directory without AD backing ("managed" user), this method will fail. Users created in AD and backed by
      // AAD ("federated" users) can benefit from this non-interactive method of authentication.
      // Mitigation: Use interactive authentication
   }
 }


 Console.WriteLine(result.Account.Username);
}
```

AcquireTokenByIntegratedWindowsAuthentication üzerindeki olası değiştiricilerin listesi için bkz. [Acquiretokenbyıntegratedwindowsauthparameterbuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyintegratedwindowsauthparameterbuilder#methods).

# <a name="java"></a>[Java](#tab/java)

Bu ayıklama [msal Java dev örneklerinden](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/).

```Java
private static IAuthenticationResult acquireTokenIwa() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();

        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            IntegratedWindowsAuthenticationParameters parameters =
                    IntegratedWindowsAuthenticationParameters
                            .builder(SCOPE, USER_NAME)
                            .build();

            // Try to acquire a IWA. You will need to generate a Kerberos ticket.
            // If successful, you should see the token and account information printed out to
            // console
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="macos"></a>[macOS](#tab/macOS)

Bu akış macOS için geçerlidir.

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Bu akış MSAL düğümünde henüz desteklenmiyor.

# <a name="python"></a>[Python](#tab/python)

Bu akış henüz MSAL Python 'da desteklenmiyor.

---

## <a name="username-and-password"></a>Kullanıcı adı ve parola

Ayrıca, Kullanıcı adı ve parola sağlayarak bir belirteç elde edebilirsiniz. Bu akış sınırlı ve önerilmez, ancak gerekli olduğu durumlarda hala kullanım durumları vardır.

### <a name="this-flow-isnt-recommended"></a>Bu akış önerilmez

Kullanıcı adı ve parola akışı *önerilmez* , çünkü uygulamanızın bir kullanıcıdan parolasını güvenli hale getirmesini isteyin. Daha fazla bilgi için bkz [. parolaların büyüyen sorunlu çözümü nedir?](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/) Windows etki alanına katılmış makinelerde sessizce belirteç almak için tercih edilen akış, [Windows kimlik doğrulaması ' nı tümleştirilmiştir](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Integrated-Windows-Authentication). [Cihaz kod akışını](https://aka.ms/msal-net-device-code-flow)da kullanabilirsiniz.

Bir Kullanıcı adı ve parola kullanmak, DevOps senaryoları gibi bazı durumlarda faydalıdır. Ancak, kendi Kullanıcı arabiriminizi sağladığınız Etkileşimli senaryolarda Kullanıcı adı ve parola kullanmak istiyorsanız, bunu nasıl uzaklaşmanız gerektiğini düşünün. Bir Kullanıcı adı ve parola kullanarak birkaç şey sağlarsınız:

- Modern kimliğin temel anları. Paylaşılan bir gizli dizi yakalanabileceğinden, bir parola, biraz ele alınabilir ve yeniden çalınabilir. Passwordless ile uyumsuz.
- MFA yapması gereken kullanıcılar etkileşime sahip olmadığından oturum açamıyor.
- Kullanıcılar çoklu oturum açma (SSO) yapamazlar.

### <a name="constraints"></a>Kısıtlamalar

Aşağıdaki kısıtlamalar da geçerlidir:

- Kullanıcı adı ve parola akışı, koşullu erişim ve çok faktörlü kimlik doğrulamasıyla uyumlu değildir. Sonuç olarak, uygulamanız kiracı yöneticisinin çok faktörlü kimlik doğrulaması gerektirdiği bir Azure AD kiracısında çalışıyorsa, bu akışı kullanamazsınız. Birçok kuruluş bu şekilde yapılır.
- Yalnızca iş ve okul hesapları için çalışır (MSA değil).
- Flow, .NET masaüstü ve .NET Core 'ta mevcuttur, UWP 'de değildir.

### <a name="b2c-specifics"></a>B2C özellikleri

Daha fazla bilgi için bkz. [B2C Ile kaynak sahibi parola kimlik bilgileri (ROPC)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics#resource-owner-password-credentials-ropc-with-b2c).

### <a name="use-it"></a>Kullanın

# <a name="net"></a>[.NET](#tab/dotnet)

`IPublicClientApplication`yöntemini içerir `AcquireTokenByUsernamePassword` .

Aşağıdaki örnek, Basitleştirilmiş bir durum gösterir.

```csharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app;
 app = PublicClientApplicationBuilder.Create(clientId)
       .WithAuthority(authority)
       .Build();
 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
                    .ExecuteAsync();
 }
 else
 {
  try
  {
   var securePassword = new SecureString();
   foreach (char c in "dummy")        // you should fetch the password
    securePassword.AppendChar(c);  // keystroke by keystroke

   result = await app.AcquireTokenByUsernamePassword(scopes,
                                                    "joe@contoso.com",
                                                     securePassword)
                      .ExecuteAsync();
  }
  catch(MsalException)
  {
   // See details below
  }
 }
 Console.WriteLine(result.Account.Username);
}
```

Aşağıdaki örnek, alabileceğiniz özel durumların ve bunların azaltmaları hakkındaki açıklamaları içeren en güncel durumu gösterir.

```csharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app;
 app = PublicClientApplicationBuilder.Create(clientId)
                                   .WithAuthority(authority)
                                   .Build();
 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
                    .ExecuteAsync();
 }
 else
 {
  try
  {
   var securePassword = new SecureString();
   foreach (char c in "dummy")        // you should fetch the password keystroke
    securePassword.AppendChar(c);  // by keystroke

   result = await app.AcquireTokenByUsernamePassword(scopes,
                                                    "joe@contoso.com",
                                                    securePassword)
                    .ExecuteAsync();
  }
  catch (MsalUiRequiredException ex) when (ex.Message.Contains("AADSTS65001"))
  {
   // Here are the kind of error messages you could have, and possible mitigations

   // ------------------------------------------------------------------------
   // MsalUiRequiredException: AADSTS65001: The user or administrator has not consented to use the application
   // with ID '{appId}' named '{appName}'. Send an interactive authorization request for this user and resource.

   // Mitigation: you need to get user consent first. This can be done either statically (through the portal),
   /// or dynamically (but this requires an interaction with Azure AD, which is not possible with
   // the username/password flow)
   // Statically: in the portal by doing the following in the "API permissions" tab of the application registration:
   // 1. Click "Add a permission" and add all the delegated permissions corresponding to the scopes you want (for instance
   // User.Read and User.ReadBasic.All)
   // 2. Click "Grant/revoke admin consent for <tenant>") and click "yes".
   // Dynamically, if you are not using .NET Core (which does not have any Web UI) by
   // calling (once only) AcquireTokenInteractive.
   // remember that Username/password is for public client applications that is desktop/mobile applications.
   // If you are using .NET core or don't want to call AcquireTokenInteractive, you might want to:
   // - use device code flow (See https://aka.ms/msal-net-device-code-flow)
   // - or suggest the user to navigate to a URL to consent: https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read
   // ------------------------------------------------------------------------

   // ------------------------------------------------------------------------
   // ErrorCode: invalid_grant
   // SubError: basic_action
   // MsalUiRequiredException: AADSTS50079: The user is required to use multi-factor authentication.
   // The tenant admin for your organization has chosen to oblige users to perform multi-factor authentication.
   // Mitigation: none for this flow
   // Your application cannot use the Username/Password grant.
   // Like in the previous case, you might want to use an interactive flow (AcquireTokenInteractive()),
   // or Device Code Flow instead.
   // Note this is one of the reason why using username/password is not recommended;
   // ------------------------------------------------------------------------

   // ------------------------------------------------------------------------
   // ex.ErrorCode: invalid_grant
   // subError: null
   // Message = "AADSTS70002: Error validating credentials.
   // AADSTS50126: Invalid username or password
   // In the case of a managed user (user from an Azure AD tenant opposed to a
   // federated user, which would be owned
   // in another IdP through ADFS), the user has entered the wrong password
   // Mitigation: ask the user to re-enter the password
   // ------------------------------------------------------------------------

   // ------------------------------------------------------------------------
   // ex.ErrorCode: invalid_grant
   // subError: null
   // MsalServiceException: ADSTS50034: To sign into this application the account must be added to
   // the {domainName} directory.
   // or The user account does not exist in the {domainName} directory. To sign into this application,
   // the account must be added to the directory.
   // The user was not found in the directory
   // Explanation: wrong username
   // Mitigation: ask the user to re-enter the username.
   // ------------------------------------------------------------------------
  }
  catch (MsalServiceException ex) when (ex.ErrorCode == "invalid_request")
  {
   // ------------------------------------------------------------------------
   // AADSTS90010: The grant type is not supported over the /common or /consumers endpoints.
   // Please use the /organizations or tenant-specific endpoint.
   // you used common.
   // Mitigation: as explained in the message from Azure AD, the authority you use in the application needs
   // to be tenanted or otherwise "organizations". change the
   // "Tenant": property in the appsettings.json to be a GUID (tenant Id), or domain name (contoso.com)
   // if such a domain is registered with your tenant
   // or "organizations", if you want this application to sign-in users in any Work and School accounts.
   // ------------------------------------------------------------------------

  }
  catch (MsalServiceException ex) when (ex.ErrorCode == "unauthorized_client")
  {
   // ------------------------------------------------------------------------
   // AADSTS700016: Application with identifier '{clientId}' was not found in the directory '{domain}'.
   // This can happen if the application has not been installed by the administrator of the tenant or consented
   // to by any user in the tenant.
   // You may have sent your authentication request to the wrong tenant
   // Cause: The clientId in the appsettings.json might be wrong
   // Mitigation: check the clientId and the app registration
   // ------------------------------------------------------------------------
  }
  catch (MsalServiceException ex) when (ex.ErrorCode == "invalid_client")
  {
   // ------------------------------------------------------------------------
   // AADSTS70002: The request body must contain the following parameter: 'client_secret or client_assertion'.
   // Explanation: this can happen if your application was not registered as a public client application in Azure AD
   // Mitigation: in the Azure portal, edit the manifest for your application and set the `allowPublicClient` to `true`
   // ------------------------------------------------------------------------
  }
  catch (MsalServiceException)
  {
   throw;
  }

  catch (MsalClientException ex) when (ex.ErrorCode == "unknown_user_type")
  {
   // Message = "Unsupported User Type 'Unknown'. Please see https://aka.ms/msal-net-up"
   // The user is not recognized as a managed user, or a federated user. Azure AD was not
   // able to identify the IdP that needs to process the user
   throw new ArgumentException("U/P: Wrong username", ex);
  }
  catch (MsalClientException ex) when (ex.ErrorCode == "user_realm_discovery_failed")
  {
   // The user is not recognized as a managed user, or a federated user. Azure AD was not
   // able to identify the IdP that needs to process the user. That's for instance the case
   // if you use a phone number
   throw new ArgumentException("U/P: Wrong username", ex);
  }
  catch (MsalClientException ex) when (ex.ErrorCode == "unknown_user")
  {
   // the username was probably empty
   // ex.Message = "Could not identify the user logged into the OS. See https://aka.ms/msal-net-iwa for details."
   throw new ArgumentException("U/P: Wrong username", ex);
  }
  catch (MsalClientException ex) when (ex.ErrorCode == "parsing_wstrust_response_failed")
  {
   // ------------------------------------------------------------------------
   // In the case of a Federated user (that is owned by a federated IdP, as opposed to a managed user owned in an Azure AD tenant)
   // ID3242: The security token could not be authenticated or authorized.
   // The user does not exist or has entered the wrong password
   // ------------------------------------------------------------------------
  }
 }

 Console.WriteLine(result.Account.Username);
}
```

Uygulanabilecek tüm değiştiriciler hakkında daha fazla bilgi için `AcquireTokenByUsernamePassword` bkz. [Acquiretokenbyusernamepasswordparameterbuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyusernamepasswordparameterbuilder#methods).

# <a name="java"></a>[Java](#tab/java)

Aşağıdaki ayıklama, [msal Java dev örneklerinden](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/).

```Java
private static IAuthenticationResult acquireTokenUsernamePassword() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();
        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            UserNamePasswordParameters parameters =
                    UserNamePasswordParameters
                            .builder(SCOPE, USER_NAME, USER_PASSWORD.toCharArray())
                            .build();
            // Try to acquire a token via username/password. If successful, you should see
            // the token and account information printed out to console
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="macos"></a>[macOS](#tab/macOS)

Bu akış, macOS için MSAL üzerinde desteklenmez.

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Bu ayıklama [msal node dev örneklerinden](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/samples/msal-node-samples/username-password). Aşağıdaki kod parçacığında Kullanıcı adı ve parola yalnızca çizim amaçlıdır. Bu, üretimde kaçınılmalıdır. Bunun yerine, kullanıcıdan Kullanıcı adını/parolasını girmesini isteyen temel bir kullanıcı arabirimi önerilir. 

```JavaScript
const msal = require("@azure/msal-node");

const msalConfig = {
    auth: {
        clientId: "your_client_id_here",
        authority: "your_authority_here",
    }
};

const pca = new msal.PublicClientApplication(msalConfig);

// For testing, enter your username and password below.
// In production, replace this with a UI prompt instead.
const usernamePasswordRequest = {
    scopes: ["user.read"],
    username: "", // Add your username here
    password: "", // Add your password here
};

pca.acquireTokenByUsernamePassword(usernamePasswordRequest).then((response) => {
    console.log("acquired token by password grant");
}).catch((error) => {
    console.log(error);
});
```

# <a name="python"></a>[Python](#tab/python)

Bu ayıklama [msal Python dev örneklerinden](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/dev/sample/).

```Python
# Create a preferably long-lived app instance which maintains a token cache.
app = msal.PublicClientApplication(
    config["client_id"], authority=config["authority"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )

# The pattern to acquire a token looks like this.
result = None

# Firstly, check the cache to see if this end user has signed in before
accounts = app.get_accounts(username=config["username"])
if accounts:
    logging.info("Account(s) exists in cache, probably with token too. Let's try.")
    result = app.acquire_token_silent(config["scope"], account=accounts[0])

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    # See this page for constraints of Username Password Flow.
    # https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki/Username-Password-Authentication
    result = app.acquire_token_by_username_password(
        config["username"], config["password"], scopes=config["scope"])
```

---

## <a name="command-line-tool-without-a-web-browser"></a>Web tarayıcısı olmayan komut satırı aracı

### <a name="device-code-flow"></a>Cihaz kod akışı

Web denetimlerine sahip olmayan bir komut satırı aracı yazıyorsanız ve önceki akışları kullanmak istemiyorsanız, cihaz kod akışını kullanın.

Azure AD ile etkileşimli kimlik doğrulaması için bir Web tarayıcısı gerekir. Daha fazla bilgi için bkz. [Web tarayıcıları kullanımı](https://aka.ms/msal-net-uses-web-browser). Bir Web tarayıcısı sağlamayan cihazlarda veya işletim sistemlerinde kullanıcıların kimliğini doğrulamak için, cihaz kodu akışı kullanıcının etkileşimli olarak oturum açmak için bilgisayar veya cep telefonu gibi başka bir cihaz kullanmasına olanak sağlar. Uygulama, cihaz kod akışını kullanarak belirteçleri bu cihazlar veya işletim sistemleri için tasarlanan iki adımlı bir işlemle edinir. Bu uygulamaların örnekleri, IoT veya komut satırı araçlarında (CLı) çalışan uygulamalardır. Fikir şu şekilde olur:

1. Kullanıcı kimlik doğrulaması gerekli olduğunda, uygulama kullanıcı için bir kod sağlar. Kullanıcıdan, örneğin, bir URL 'ye gitmek için Internet 'e bağlı bir akıllı telefonu gibi başka bir cihaz kullanması istenir `https://microsoft.com/devicelogin` . Ardından kullanıcıdan kodu girmesi istenir. Bu işlem yapıldığında, Web sayfası, gerekirse izin istemlerini ve çok faktörlü kimlik doğrulamasını içeren normal bir kimlik doğrulama deneyimi aracılığıyla kullanıcıya müşteri adayı doğurur.

2. Başarılı kimlik doğrulamasından sonra, komut satırı uygulaması, gerekli belirteçleri bir arka kanal aracılığıyla alır ve bunları gereken Web API çağrılarını gerçekleştirmek için kullanır.

### <a name="use-it"></a>Kullanın

# <a name="net"></a>[.NET](#tab/dotnet)

`IPublicClientApplication`adlı bir yöntemi içerir `AcquireTokenWithDeviceCode` .

```csharp
 AcquireTokenWithDeviceCode(IEnumerable<string> scopes,
                            Func<DeviceCodeResult, Task> deviceCodeResultCallback)
```

Bu yöntem parametre olarak alır:

- `scopes`İçin bir erişim belirteci istemek için.
- Öğesini alan bir geri çağırma `DeviceCodeResult` .

  ![DeviceCodeResult özellikleri](https://user-images.githubusercontent.com/13203188/56024968-7af1b980-5d11-11e9-84c2-5be2ef306dc5.png)

Aşağıdaki örnek kod, en güncel durumların Özeti, alabileceğiniz özel durumların türüyle ilgili açıklamaları ve bunların hafifliklerini gösterir. Tam işlevli bir kod örneği için bkz. GitHub üzerinde [Active-Directory-dotnetcore-devicecodeflow-v2](https://github.com/azure-samples/active-directory-dotnetcore-devicecodeflow-v2) .

```csharp
private const string ClientId = "<client_guid>";
private const string Authority = "https://login.microsoftonline.com/contoso.com";
private readonly string[] scopes = new string[] { "user.read" };

static async Task<AuthenticationResult> GetATokenForGraph()
{
    IPublicClientApplication pca = PublicClientApplicationBuilder
            .Create(ClientId)
            .WithAuthority(Authority)
            .WithDefaultRedirectUri()
            .Build();

    var accounts = await pca.GetAccountsAsync();

    // All AcquireToken* methods store the tokens in the cache, so check the cache first
    try
    {
        return await pca.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
            .ExecuteAsync();
    }
    catch (MsalUiRequiredException ex)
    {
        // No token found in the cache or AAD insists that a form interactive auth is required (e.g. the tenant admin turned on MFA)
        // If you want to provide a more complex user experience, check out ex.Classification

        return await AcquireByDeviceCodeAsync(pca);
    }
}

private static async Task<AuthenticationResult> AcquireByDeviceCodeAsync(IPublicClientApplication pca)
{
    try
    {
        var result = await pca.AcquireTokenWithDeviceCode(scopes,
            deviceCodeResult =>
            {
                    // This will print the message on the console which tells the user where to go sign-in using
                    // a separate browser and the code to enter once they sign in.
                    // The AcquireTokenWithDeviceCode() method will poll the server after firing this
                    // device code callback to look for the successful login of the user via that browser.
                    // This background polling (whose interval and timeout data is also provided as fields in the
                    // deviceCodeCallback class) will occur until:
                    // * The user has successfully logged in via browser and entered the proper code
                    // * The timeout specified by the server for the lifetime of this code (typically ~15 minutes) has been reached
                    // * The developing application calls the Cancel() method on a CancellationToken sent into the method.
                    //   If this occurs, an OperationCanceledException will be thrown (see catch below for more details).
                    Console.WriteLine(deviceCodeResult.Message);
                return Task.FromResult(0);
            }).ExecuteAsync();

        Console.WriteLine(result.Account.Username);
        return result;
    }

    // TODO: handle or throw all these exceptions depending on your app
    catch (MsalServiceException ex)
    {
        // Kind of errors you could have (in ex.Message)

        // AADSTS50059: No tenant-identifying information found in either the request or implied by any provided credentials.
        // Mitigation: as explained in the message from Azure AD, the authoriy needs to be tenanted. you have probably created
        // your public client application with the following authorities:
        // https://login.microsoftonline.com/common or https://login.microsoftonline.com/organizations

        // AADSTS90133: Device Code flow is not supported under /common or /consumers endpoint.
        // Mitigation: as explained in the message from Azure AD, the authority needs to be tenanted

        // AADSTS90002: Tenant <tenantId or domain you used in the authority> not found. This may happen if there are
        // no active subscriptions for the tenant. Check with your subscription administrator.
        // Mitigation: if you have an active subscription for the tenant this might be that you have a typo in the
        // tenantId (GUID) or tenant domain name.
    }
    catch (OperationCanceledException ex)
    {
        // If you use a CancellationToken, and call the Cancel() method on it, then this *may* be triggered
        // to indicate that the operation was cancelled.
        // See https://docs.microsoft.com/dotnet/standard/threading/cancellation-in-managed-threads
        // for more detailed information on how C# supports cancellation in managed threads.
    }
    catch (MsalClientException ex)
    {
        // Possible cause - verification code expired before contacting the server
        // This exception will occur if the user does not manage to sign-in before a time out (15 mins) and the
        // call to `AcquireTokenWithDeviceCode` is not cancelled in between
    }
}
```

# <a name="java"></a>[Java](#tab/java)

Bu ayıklama [msal Java dev örneklerinden](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/).

```java
private static IAuthenticationResult acquireTokenDeviceCode() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();

        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            Consumer<DeviceCode> deviceCodeConsumer = (DeviceCode deviceCode) ->
                    System.out.println(deviceCode.message());

            DeviceCodeFlowParameters parameters =
                    DeviceCodeFlowParameters
                            .builder(SCOPE, deviceCodeConsumer)
                            .build();

            // Try to acquire a token via device code flow. If successful, you should see
            // the token and account information printed out to console, and the sample_cache.json
            // file should have been updated with the latest tokens.
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="macos"></a>[macOS](#tab/macOS)

Bu akış macOS için geçerlidir.

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Bu ayıklama [msal node dev örneklerinden](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/samples/msal-node-samples/device-code).

```JavaScript
const msal = require('@azure/msal-node');

const msalConfig = {
    auth: {
        clientId: "your_client_id_here",
        authority: "your_authority_here",
    }
};

const pca = new msal.PublicClientApplication(msalConfig);

const deviceCodeRequest = {
    deviceCodeCallback: (response) => (console.log(response.message)),
    scopes: ["user.read"],
    timeout: 20,
};

pca.acquireTokenByDeviceCode(deviceCodeRequest).then((response) => {
    console.log(JSON.stringify(response));
}).catch((error) => {
    console.log(JSON.stringify(error));
});
```

# <a name="python"></a>[Python](#tab/python)

Bu ayıklama [msal Python dev örneklerinden](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/dev/sample/).

```Python
# Create a preferably long-lived app instance which maintains a token cache.
app = msal.PublicClientApplication(
    config["client_id"], authority=config["authority"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )

# The pattern to acquire a token looks like this.
result = None

# Note: If your device-flow app does not have any interactive ability, you can
#   completely skip the following cache part. But here we demonstrate it anyway.
# We now check the cache to see if we have some end users signed in before.
accounts = app.get_accounts()
if accounts:
    logging.info("Account(s) exists in cache, probably with token too. Let's try.")
    print("Pick the account you want to use to proceed:")
    for a in accounts:
        print(a["username"])
    # Assuming the end user chose this one
    chosen = accounts[0]
    # Now let's try to find a token in cache for this account
    result = app.acquire_token_silent(config["scope"], account=chosen)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")

    flow = app.initiate_device_flow(scopes=config["scope"])
    if "user_code" not in flow:
        raise ValueError(
            "Fail to create device flow. Err: %s" % json.dumps(flow, indent=4))

    print(flow["message"])
    sys.stdout.flush()  # Some terminal needs this to ensure the message is shown

    # Ideally you should wait here, in order to save some unnecessary polling
    # input("Press Enter after signing in from another device to proceed, CTRL+C to abort.")

    result = app.acquire_token_by_device_flow(flow)  # By default it will block
        # You can follow this instruction to shorten the block time
        #    https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_device_flow
        # or you may even turn off the blocking behavior,
        # and then keep calling acquire_token_by_device_flow(flow) in your own customized loop
```

---

## <a name="file-based-token-cache"></a>Dosya tabanlı belirteç önbelleği

MSAL.NET ' de, bir bellek içi belirteç önbelleği varsayılan olarak sağlanır.

### <a name="serialization-is-customizable-in-windows-desktop-apps-and-web-apps-or-web-apis"></a>Serileştirme, Windows Masaüstü uygulamaları ve Web uygulamaları veya Web API 'Lerinde özelleştirilebilir

.NET Framework ve .NET Core durumunda, ek bir şey yapmazsanız, bellek içi belirteç önbelleği uygulama süresince sürer. Serileştirme 'in kutudan neden olmadığını anlamak için, MSAL .NET masaüstü veya .NET Core uygulamalarının konsol veya Windows uygulamaları (dosya sistemine erişimi olan), *Ayrıca* Web uygulamaları veya Web API 'leri olabileceğini unutmayın. Bu Web uygulamaları ve Web API 'Leri veritabanları, dağıtılmış önbellekler ve Redıs önbellekler gibi bazı belirli önbellek mekanizmalarını kullanabilir. .NET masaüstü veya .NET Core 'ta kalıcı bir belirteç önbelleği uygulamasına sahip olmak için Serileştirmeyi özelleştirmeniz gerekir.

Belirteç önbelleği serileştirmesi ile ilgili sınıflar ve arabirimler aşağıdaki türlerdir:

- ``ITokenCache``, belirteç önbelleği serileştirme isteklerine abone olacak olayları ve farklı biçimlerde (ADAL v 3.0, MSAL 2. x ve MSAL 3. x = ADAL v 5.0) önbelleği serileştirme veya serisini kaldırma yöntemlerini tanımlar.
- ``TokenCacheCallback`` , serileştirme işlemini işleyebilmeniz için olaylara geçirilmiş bir geri çağırma işlemi. Bunlar, türündeki bağımsız değişkenlerle çağırılır ``TokenCacheNotificationArgs`` .
- ``TokenCacheNotificationArgs`` yalnızca uygulamayı ``ClientId`` ve belirtecin kullanılabildiği kullanıcıya bir başvuru sağlar.

  ![Belirteç önbelleği serileştirme diyagramı](https://user-images.githubusercontent.com/13203188/56027172-d58d1480-5d15-11e9-8ada-c0292f1800b3.png)

> [!IMPORTANT]
> MSAL.NET sizin için belirteç önbellekleri oluşturur ve `IToken` bir uygulamanın ve özelliklerini çağırdığınızda önbellek sağlar `UserTokenCache` `AppTokenCache` . Arabirimi kendiniz uygulamanız gerekmez. Özel bir belirteç önbelleği serileştirmesi uyguladığınızda, sorumluluğu şu şekilde olur:
>
> - `BeforeAccess`Ve `AfterAccess` olayları ya da *zaman uyumsuz* karşılamalarını tepki verir. Bu `BeforeAccess` temsilci, önbelleğin serisini kaldırmada sorumludur. Bu `AfterAccess` temsilci, önbelleğin serileştirilmesinden sorumludur.
> - Bu olayların bir bölümünün, olay bağımsız değişkeni aracılığıyla istediğiniz depolama alanına geçirildiği blob 'ları veya yükleme bloblarını anlayın.

Bir masaüstü veya Web uygulaması veya Web API 'SI ya da bir Daemon uygulaması gibi bir gizli istemci uygulaması için bir belirteç önbelleği serileştirme yazıyorsanız, stratejileri farklılık açıktır.

MSAL v2. x olduğundan, birkaç seçeneğiniz vardır. Tercih ettiğiniz değişiklik, önbelleği yalnızca MSAL ile ortak olan Birleşik bir biçim önbelleği olan MSAL.NET biçiminde seri hale getirmek isteyip istemediğinize bağlıdır. Ayrıca, ADAL v3 'nin [eski](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization) belirteç önbelleği serileştirmesini desteklemek de isteyebilirsiniz.

ADAL.NET 3. x, ADAL.NET 5. x ve MSAL.NET arasındaki SSO durumunu paylaşmak için belirteç önbelleği serileştirme özelleştirmesi, [Active-Directory-DotNet-v1-v1-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2)örnek bölümünde açıklanmaktadır.

### <a name="simple-token-cache-serialization-msal-only"></a>Basit belirteç önbelleği serileştirme (yalnızca MSAL)

Aşağıdaki örnek, masaüstü uygulamaları için bir belirteç önbelleğinin özel serileştirilmesi Naïve uygulamasıdır. Burada, kullanıcı belirteci önbelleği uygulamayla aynı klasörde veya uygulamanın [paketlenmiş bir masaüstü uygulaması](/windows/msix/desktop/desktop-to-uwp-behind-the-scenes)olduğu durumda her uygulama için Kullanıcı başına bir dosya içinde bulunur. Tam kod için aşağıdaki örneğe bakın: [Active-Directory-DotNet-Desktop-MSGraph-v2](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2).

Uygulamayı oluşturduktan sonra, ``TokenCacheHelper.EnableSerialization()`` uygulamayı çağırarak ve geçirerek serileştirme etkinleştirilir `UserTokenCache` .

```csharp
app = PublicClientApplicationBuilder.Create(ClientId)
    .Build();
TokenCacheHelper.EnableSerialization(app.UserTokenCache);
```

Bu yardımcı sınıf aşağıdaki kod parçacığı gibi görünür:

```csharp
static class TokenCacheHelper
 {
  public static void EnableSerialization(ITokenCache tokenCache)
  {
   tokenCache.SetBeforeAccess(BeforeAccessNotification);
   tokenCache.SetAfterAccess(AfterAccessNotification);
   try
   {
    // For packaged desktop apps (MSIX packages) the executing assembly folder is read-only. 
    // In that case we need to use Windows.Storage.ApplicationData.Current.LocalCacheFolder.Path + "\msalcache.bin" 
    // which is a per-app read/write folder for packaged apps.
    // See https://docs.microsoft.com/windows/msix/desktop/desktop-to-uwp-behind-the-scenes
    CacheFilePath = System.IO.Path.Combine(Windows.Storage.ApplicationData.Current.LocalCacheFolder.Path, "msalcache.bin3");
   }
   catch (System.InvalidOperationException)
   {
    // Fall back for an un-packaged desktop app
    CacheFilePath = System.Reflection.Assembly.GetExecutingAssembly().Location + ".msalcache.bin";
   }
  }

  /// <summary>
  /// Path to the token cache
  /// </summary>
  public static string CacheFilePath { get; private set; }

  private static readonly object FileLock = new object();

  private static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeMsalV3(File.Exists(CacheFilePath)
            ? ProtectedData.Unprotect(File.ReadAllBytes(CacheFilePath),
                                      null,
                                      DataProtectionScope.CurrentUser)
            : null);
   }
  }

  private static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     // reflect changesgs in the persistent store
     File.WriteAllBytes(CacheFilePath,
                         ProtectedData.Protect(args.TokenCache.SerializeMsalV3(),
                                                 null,
                                                 DataProtectionScope.CurrentUser)
                         );
    }
   }
  }
 }
```

Windows, Mac ve Linux üzerinde çalışan masaüstü uygulamalarına yönelik ortak istemci uygulamalarına yönelik bir ürün kalitesi belirteç önbelleği dosya tabanlı serileştiricinin Önizlemesi [Microsoft. Identity. Client. Extensions. msal](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Msal) açık kaynak kitaplığından bulunabilir. Bunu uygulamalarınıza şu NuGet paketinden dahil edebilirsiniz: [Microsoft. Identity. Client. Extensions. msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/).

> [!NOTE]
> Vazgeçme: Microsoft. Identity. Client. Extensions. msal kitaplığı, MSAL.NET üzerinde bir uzantıdır. Bu kitaplıklardaki sınıflar, MSAL.NET veya son değişiklikler ile, gelecekte bu şekilde bir şekilde.

### <a name="dual-token-cache-serialization-msal-unified-cache--adal-v3"></a>Çift belirteç önbelleği serileştirme (MSAL Birleşik önbellek + ADAL v3)

Birleşik önbellek biçimiyle belirteç önbelleği serileştirme uygulamak isteyebilirsiniz. Bu biçim, ADAL.NET 4. x ve MSAL.NET 2. x ile aynı platformda ve daha eski diğer Msallarla aynı platformda ortaktır. Aşağıdaki kodla ilham alın:

```csharp
string appLocation = Path.GetDirectoryName(Assembly.GetEntryAssembly().Location;
string cacheFolder = Path.GetFullPath(appLocation) + @"..\..\..\..");
string adalV3cacheFileName = Path.Combine(cacheFolder, "cacheAdalV3.bin");
string unifiedCacheFileName = Path.Combine(cacheFolder, "unifiedCache.bin");

IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
                                    .Build();
FilesBasedTokenCacheHelper.EnableSerialization(app.UserTokenCache,
                                               unifiedCacheFileName,
                                               adalV3cacheFileName);

```

Bu kez yardımcı sınıfı aşağıdaki kod gibi görünür:

```csharp
using System;
using System.IO;
using System.Security.Cryptography;
using Microsoft.Identity.Client;

namespace CommonCacheMsalV3
{
 /// <summary>
 /// Simple persistent cache implementation of the dual cache serialization (ADAL V3 legacy
 /// and unified cache format) for a desktop applications (from MSAL 2.x)
 /// </summary>
 static class FilesBasedTokenCacheHelper
 {
  /// <summary>
  /// Get the user token cache
  /// </summary>
  /// <param name="adalV3CacheFileName">File name where the cache is serialized with the
  /// ADAL V3 token cache format. Can
  /// be <c>null</c> if you don't want to implement the legacy ADAL V3 token cache
  /// serialization in your MSAL 2.x+ application</param>
  /// <param name="unifiedCacheFileName">File name where the cache is serialized
  /// with the Unified cache format, common to
  /// ADAL V4 and MSAL V2 and above, and also across ADAL/MSAL on the same platform.
  ///  Should not be <c>null</c></param>
  /// <returns></returns>
  public static void EnableSerialization(ITokenCache cache, string unifiedCacheFileName, string adalV3CacheFileName)
  {
   UnifiedCacheFileName = unifiedCacheFileName;
   AdalV3CacheFileName = adalV3CacheFileName;

   cache.SetBeforeAccess(BeforeAccessNotification);
   cache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// File path where the token cache is serialized with the unified cache format
  /// (ADAL.NET V4, MSAL.NET V3)
  /// </summary>
  public static string UnifiedCacheFileName { get; private set; }

  /// <summary>
  /// File path where the token cache is serialized with the legacy ADAL V3 format
  /// </summary>
  public static string AdalV3CacheFileName { get; private set; }

  private static readonly object FileLock = new object();

  public static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeAdalV3(ReadFromFileIfExists(AdalV3CacheFileName));
    try
    {
     args.TokenCache.DeserializeMsalV3(ReadFromFileIfExists(UnifiedCacheFileName));
    }
    catch(Exception ex)
    {
     // Compatibility with the MSAL v2 cache if you used one
     args.TokenCache.DeserializeMsalV2(ReadFromFileIfExists(UnifiedCacheFileName));
    }
   }
  }

  public static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     WriteToFileIfNotNull(UnifiedCacheFileName, args.TokenCache.SerializeMsalV3());
     if (!string.IsNullOrWhiteSpace(AdalV3CacheFileName))
     {
      WriteToFileIfNotNull(AdalV3CacheFileName, args.TokenCache.SerializeAdalV3());
     }
    }
   }
  }

  /// <summary>
  /// Read the content of a file if it exists
  /// </summary>
  /// <param name="path">File path</param>
  /// <returns>Content of the file (in bytes)</returns>
  private static byte[] ReadFromFileIfExists(string path)
  {
   byte[] protectedBytes = (!string.IsNullOrEmpty(path) && File.Exists(path))
       ? File.ReadAllBytes(path) : null;
   byte[] unprotectedBytes = encrypt ?
       ((protectedBytes != null) ? ProtectedData.Unprotect(protectedBytes, null, DataProtectionScope.CurrentUser) : null)
       : protectedBytes;
   return unprotectedBytes;
  }

  /// <summary>
  /// Writes a blob of bytes to a file. If the blob is <c>null</c>, deletes the file
  /// </summary>
  /// <param name="path">path to the file to write</param>
  /// <param name="blob">Blob of bytes to write</param>
  private static void WriteToFileIfNotNull(string path, byte[] blob)
  {
   if (blob != null)
   {
    byte[] protectedBytes = encrypt
      ? ProtectedData.Protect(blob, null, DataProtectionScope.CurrentUser)
      : blob;
    File.WriteAllBytes(path, protectedBytes);
   }
   else
   {
    File.Delete(path);
   }
  }

  // Change if you want to test with an un-encrypted blob (this is a json format)
  private static bool encrypt = true;
 }
}
```

## <a name="advanced-accessing-the-users-cached-tokens-in-background-apps-and-services"></a>Ileri Arka plan uygulamaları ve hizmetlerinde kullanıcının önbelleğe alınmış belirteçlerine erişme

[!INCLUDE [advanced-token-caching](../../../includes/advanced-token-cache.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu senaryodaki bir sonraki makaleye geçin, [masaüstü uygulamasından bir Web API 'Si çağırın](scenario-desktop-call-api.md).
