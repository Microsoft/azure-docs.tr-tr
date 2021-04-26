---
title: Mobil uygulamadan bir Web API 'SI çağırma | Mavisi
titleSuffix: Microsoft identity platform
description: Web API 'Lerini çağıran bir mobil uygulama oluşturmayı öğrenin. (Bir Web API 'SI çağırın.)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/18/2020
ms.author: jmprieur
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 656cb167fb47e44c28922afed75d8c46a460aaf1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99584406"
---
# <a name="call-a-web-api-from-a-mobile-app"></a>Mobil uygulamadan bir Web API 'SI çağırma

Uygulamanız bir kullanıcıya kaydolduktan ve belirteçleri aldıktan sonra, Microsoft kimlik doğrulama kitaplığı (MSAL) Kullanıcı, kullanıcının ortamı ve verilen belirteçler hakkında bilgi sunar. Uygulamanız, bu değerleri bir Web API 'SI çağırmak veya kullanıcıya bir hoş geldiniz iletisi göstermek için kullanabilir.

Bu makalede, ilk olarak MSAL sonucuna bakacağız. Daha sonra `AuthenticationResult` `result` korumalı BIR Web API 'sini çağırmak için veya ' den erişim belirtecinin nasıl kullanılacağını inceleyeceğiz.

## <a name="msal-result"></a>MSAL sonucu
MSAL aşağıdaki değerleri sağlar: 

- `AccessToken` HTTP taşıyıcı isteğinde korunan Web API 'Lerini çağırır.
- `IdToken` oturum açmış kullanıcıyla ilgili yararlı bilgiler içerir. Bu bilgiler kullanıcının adını, ana kiracısını ve depolama için benzersiz bir tanımlayıcıyı içerir.
- `ExpiresOn` belirtecin sona erme zamanıdır. MSAL, bir uygulamanın otomatik yenilemesini işler.
- `TenantId` kullanıcının oturum açmadığı kiracının tanımlayıcısıdır. Azure Active Directory (Azure AD) B2B 'daki Konuk kullanıcılar için bu değer, kullanıcının oturum açmakta olduğu kiracıyı tanımlar. Değer, kullanıcının ana kiracısını tanımlamaz.  
- `Scopes` belirtecinizle verilen kapsamları gösterir. Verilen kapsamlar, istediğiniz kapsamların bir alt kümesi olabilir.

MSAL Ayrıca bir değer için bir soyutlama sağlar `Account` . Bir `Account` değer, geçerli kullanıcının oturum açmış olan hesabını temsil eder:

- `HomeAccountIdentifier` kullanıcının ana kiracısını tanımlar.
- `UserName` kullanıcının tercih ettiği kullanıcı adıdır. Azure AD B2C kullanıcılar için bu değer boş olabilir.
- `AccountIdentifier` oturum açmış kullanıcıyı tanımlar. Çoğu durumda, `HomeAccountIdentifier` Kullanıcı başka bir kiracıda Konuk olmadığı sürece bu değer değeri ile aynıdır.

## <a name="call-an-api"></a>API çağırma

Erişim belirtecine sahip olduktan sonra, bir Web API 'SI çağırabilirsiniz. Uygulamanız bir HTTP isteği oluşturmak için belirteci kullanır ve sonra isteği çalıştırır.

### <a name="android"></a>Android

```Java
        RequestQueue queue = Volley.newRequestQueue(this);
        JSONObject parameters = new JSONObject();

        try {
            parameters.put("key", "value");
        } catch (Exception e) {
            // Error when constructing.
        }
        JsonObjectRequest request = new JsonObjectRequest(Request.Method.GET, MSGRAPH_URL,
                parameters,new Response.Listener<JSONObject>() {
            @Override
            public void onResponse(JSONObject response) {
                // Successfully called Graph. Process data and send to UI.
            }
        }, new Response.ErrorListener() {
            @Override
            public void onErrorResponse(VolleyError error) {
                // Error.
            }
        }) {
            @Override
            public Map<String, String> getHeaders() throws AuthFailureError {
                Map<String, String> headers = new HashMap<>();
                
                // Put access token in HTTP request.
                headers.put("Authorization", "Bearer " + authResult.getAccessToken());
                return headers;
            }
        };

        request.setRetryPolicy(new DefaultRetryPolicy(
                3000,
                DefaultRetryPolicy.DEFAULT_MAX_RETRIES,
                DefaultRetryPolicy.DEFAULT_BACKOFF_MULT));
        queue.add(request);
```

### <a name="msal-for-ios-and-macos"></a>iOS ve macOS için MSAL

Belirteçleri elde etmek için yöntemler bir `MSALResult` nesne döndürür. `MSALResult` bir `accessToken` özellik sunar. `accessToken`Bir Web API 'sini çağırmak için ' i kullanabilirsiniz. Korumalı Web API 'sine erişmek için çağrı yapmadan önce bu özelliği HTTP yetkilendirme üstbilgisine ekleyin.

```objc
NSMutableURLRequest *urlRequest = [NSMutableURLRequest new];
urlRequest.URL = [NSURL URLWithString:"https://contoso.api.com"];
urlRequest.HTTPMethod = @"GET";
urlRequest.allHTTPHeaderFields = @{ @"Authorization" : [NSString stringWithFormat:@"Bearer %@", accessToken] };
        
NSURLSessionDataTask *task =
[[NSURLSession sharedSession] dataTaskWithRequest:urlRequest
     completionHandler:^(NSData * _Nullable data, NSURLResponse * _Nullable response, NSError * _Nullable error) {}];
[task resume];
```

```swift
let urlRequest = NSMutableURLRequest()
urlRequest.url = URL(string: "https://contoso.api.com")!
urlRequest.httpMethod = "GET"
urlRequest.allHTTPHeaderFields = [ "Authorization" : "Bearer \(accessToken)" ]
     
let task = URLSession.shared.dataTask(with: urlRequest as URLRequest) { (data: Data?, response: URLResponse?, error: Error?) in }
task.resume()
```

### <a name="xamarin"></a>Xamarin

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

## <a name="make-several-api-requests"></a>Birkaç API isteği yapın

Aynı API 'yi birkaç kez çağırmak veya birden çok API 'yi çağırmak için, uygulamanızı oluştururken aşağıdaki konuları göz önünde bulundurun:

- **Artımlı izin**: Microsoft Identity platform, uygulamaların başlangıçta değil, izin gerektiğinde kullanıcı onayı almasına izin verir. Uygulamanız bir API 'yi çağırmaya her seferinde, yalnızca ihtiyaç duyacağı kapsamları istemesi gerekir.

- **Koşullu erişim**: birkaç API isteği yaptığınızda, bazı senaryolarda ek koşullu erişim gereksinimlerini karşılamanız gerekebilir. Bu şekilde, ilk isteğin koşullu erişim ilkeleri yoksa ve uygulamanız koşullu erişim gerektiren yeni bir API 'ye sessizce erişmeyi denediğinde gereksinimler bu şekilde artabilir. Bu sorunu gidermek için, sessiz isteklerden gelen hataları yakalamalı ve etkileşimli bir istek oluşturmak için hazırlandığınızdan emin olun.  Daha fazla bilgi için bkz. [koşullu erişim Için rehberlik](../azuread-dev/conditional-access-dev-guide.md).

## <a name="call-several-apis-by-using-incremental-consent-and-conditional-access"></a>Artımlı onay ve koşullu erişim kullanarak birkaç API çağırın

Aynı kullanıcı için birden çok API 'yi çağırmak için, Kullanıcı için bir belirteç aldıktan sonra, daha sonra `AcquireTokenSilent` bir belirteç almak üzere çağırarak kullanıcıdan kimlik bilgilerini tekrar tekrar sormaktan kaçınabilirsiniz:

```csharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
```

Şu durumlarda etkileşim gerekir:

- Kullanıcı ilk API 'ye onay verdi, ancak şimdi daha fazla kapsam için onay gerektirir. Bu durumda, artımlı onay kullanırsınız.
- İlk API [çok faktörlü kimlik doğrulaması](../authentication/concept-mfa-howitworks.md)gerektirmez, ancak sonraki API.

```csharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

try
{
 result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
}
catch(MsalUiRequiredException ex)
{
 result = await app.AcquireTokenInteractive("scopeApi2")
                  .WithClaims(ex.Claims)
                  .ExecuteAsync();
}
```

## <a name="next-steps"></a>Sonraki adımlar

Bu senaryodaki sonraki makaleye geçin, [üretime geçin](scenario-mobile-production.md).
