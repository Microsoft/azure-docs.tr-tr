---
title: Web API 'Lerini çağıran mobil uygulama-bir Web API 'SI çağırma
titleSuffix: Microsoft identity platform
description: Web API 'Leri çağıran bir mobil uygulama oluşturmayı öğrenin (Web API 'SI çağırma)
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
ms.openlocfilehash: 9e70b828219fc497fc07e2bc128eb480a532a176
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72802553"
---
# <a name="mobile-app-that-calls-web-apis---call-a-web-api"></a>Web API 'Lerini çağıran mobil uygulama-bir Web API 'SI çağırma

Uygulamanız bir kullanıcıya kaydolduktan ve belirteçleri aldıktan sonra, MSAL Kullanıcı, kullanıcının ortamı ve verilen belirteçler hakkında çeşitli bilgi parçalarını gösterir. Uygulamanız, bu değerleri bir Web API 'SI çağırmak veya kullanıcıya bir hoş geldiniz iletisi göstermek için kullanabilir.

İlk olarak MSAL sonucuna bakacağız. Daha sonra, `AuthenticationResult` bir erişim belirtecinin nasıl kullanılacağını veya korumalı bir Web API 'sini çağırmak için `result` bakacağız.

## <a name="msal-result"></a>MSAL sonucu
MSAL aşağıdaki değerleri sağlar: 

- `AccessToken`: bir HTTP taşıyıcı isteğindeki korumalı Web API 'Lerini çağırmak için kullanılır.
- `IdToken`: kullanıcının adı, ev kiracısı ve depolama için benzersiz bir tanımlayıcı gibi oturum açmış kullanıcıyla ilgili yararlı bilgiler Içerir.
- `ExpiresOn`: belirtecin sona erme saati. MSAL, uygulamalar için otomatik yenilemeyi işler.
- `TenantId`: kullanıcının oturum açmasından kiracının tanımlayıcısı. Konuk kullanıcılar (Azure Active Directory B2B) için, bu değer kullanıcının giriş kiracısına değil, kullanıcının oturum açmasından kiracıyı belirler.  
- `Scopes`: belirtecinizle verilen kapsamlar. Verilen kapsamlar, istediğiniz kapsamların bir alt kümesi olabilir.

MSAL Ayrıca `Account`için bir soyutlama sağlar. `Account`, geçerli kullanıcının oturum açmış olan hesabını temsil eder.

- `HomeAccountIdentifier`: kullanıcının ana kiracının tanımlayıcısı.
- `UserName`: kullanıcının tercih ettiği Kullanıcı adı. Bu, Azure Active Directory B2C kullanıcılar için boş olabilir.
- `AccountIdentifier`: oturum açan kullanıcının tanımlayıcısı. Bu değer, Kullanıcı başka bir kiracıda Konuk olmadığı sürece çoğu durumda `HomeAccountIdentifier` değeriyle aynı olacaktır.

## <a name="call-an-api"></a>API çağırma

Erişim belirtecine sahip olduktan sonra, bir Web API 'SI çağırmak kolaydır. Uygulamanız bir HTTP isteği oluşturmak için belirteci kullanır ve sonra isteği çalıştırır.

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

Belirteçleri elde etmek için yöntemler bir `MSALResult` nesnesi döndürür. `MSALResult`, bir Web API 'sini çağırmak için kullanılabilen bir `accessToken` özelliğini kullanıma sunar. Korunan Web API 'sine erişmek için çağrı yapılmadan önce, erişim belirtecinin HTTP yetkilendirme üstbilgisine eklenmesi gerekir.

Amaç-C:

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

SWIFT

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

## <a name="making-several-api-requests"></a>Birkaç API isteği oluşturma

Aynı API 'yi birkaç kez çağırmanız gerekiyorsa veya birden çok API çağırmanız gerekiyorsa, uygulamanızı oluştururken aşağıdakileri dikkate alın:

- **Artımlı izin**: Microsoft Identity platform, uygulamaların başlangıçta değil, kullanıcı onayı almasına izin verir. Uygulamanız bir API 'yi çağırmaya her seferinde yalnızca kullanması gereken kapsamları istemesi gerekir.
- **Koşullu erişim**: bazı senaryolarda, birkaç API isteği yaptığınızda ek koşullu erişim gereksinimleri alabilirsiniz. Bu durum, ilk isteğin uygulanmış koşullu erişim ilkesi yoksa ve uygulamanız koşullu erişim gerektiren yeni bir API 'ye sessizce erişmeye çalışırsa meydana gelebilir. Bu senaryoyu işlemek için sessiz isteklerden gelen hataları yakalamalı ve etkileşimli bir istek oluşturmak için hazırlandığınızdan emin olun.  Daha fazla bilgi için bkz. [koşullu erişim Için rehberlik](conditional-access-dev-guide.md).

## <a name="calling-several-apis-in-xamarin-or-uwp---incremental-consent-and-conditional-access"></a>Xamarin veya UWP 'de birkaç API çağırma-artımlı onay ve koşullu erişim

Aynı kullanıcı için birden çok API çağrısı yapmanız gerekiyorsa, bir kullanıcı için bir belirteç aldıktan sonra, bir belirteci almak için `AcquireTokenSilent` daha sonra çağırarak kullanıcıdan kimlik bilgilerini tekrar tekrar sormaktan kaçınabilirsiniz.

```CSharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
```

Etkileşimin gerekli olduğu durumlar şunlardır:

- Kullanıcı ilk API 'yi kabul etmiş, ancak artık daha fazla kapsam için onay gerektirir (artımlı onay)
- İlk API çok faktörlü kimlik doğrulaması gerektirmez, ancak bir sonraki tane.

```CSharp
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

> [!div class="nextstepaction"]
> [Üretime taşı](scenario-mobile-production.md)
