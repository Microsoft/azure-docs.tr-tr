---
title: Web API 'Lerini çağıran bir Web API 'SI için belirteç alın | Mavisi
titleSuffix: Microsoft identity platform
description: Uygulama için belirteç alma gerektiren Web API 'Lerini çağıran bir Web API 'SI oluşturmayı öğrenin.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/15/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 9f9758ec765ad34e5ef5d8b4d4e0a420a6701b6e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98756386"
---
# <a name="a-web-api-that-calls-web-apis-acquire-a-token-for-the-app"></a>Web API 'Lerini çağıran bir Web API 'SI: uygulama için belirteç alma

Bir istemci uygulama nesnesi oluşturduktan sonra, bir Web API 'sini çağırmak için kullanabileceğiniz bir belirteç almak için bu uygulamayı kullanın.

## <a name="code-in-the-controller"></a>Denetleyicideki kod

### <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

*Microsoft. Identity. Web* , Microsoft Graph veya bir aşağı akış Web API 'sine çağrı yapmak için kullanışlı hizmetler sağlayan uzantı yöntemleri ekler. Bu yöntemler, [Web API 'lerini çağıran bir Web API](scenario-web-api-call-api-call-api.md)'sinde ayrıntılı olarak AÇıKLANMıŞTıR: API çağrısı. Bu yardımcı yöntemlerle el ile belirteç almanız gerekmez.

Ancak, bir belirteci el ile almak istiyorsanız aşağıdaki kod, bir API denetleyicisinde bunu yapmak için *Microsoft. Identity. Web* kullanımına ilişkin bir örnek gösterir. *ToDoList* adlı bir aşağı akış API 'si çağırır.
Aşağı akış API 'sini çağırmak için bir belirteç almak üzere, `ITokenAcquisition` hizmet programını denetleyicinin yapıcısına (veya Blazor kullanıyorsanız sayfa yapıcısına) ekleyerek, `GetAccessTokenForUserAsync` `GetAccessTokenForAppAsync` bir Daemon senaryosu olması durumunda bu hizmeti, Kullanıcı () veya uygulamanın kendisi () için bir belirteç alarak, denetleyici eylemlerinde kullanabilirsiniz.

```csharp
[Authorize]
public class MyApiController : Controller
{
    /// <summary>
    /// The web API will accept only tokens 1) for users, 2) that have the `access_as_user` scope for
    /// this API.
    /// </summary>
    static readonly string[] scopeRequiredByApi = new string[] { "access_as_user" };

     static readonly string[] scopesToAccessDownstreamApi = new string[] { "api://MyTodolistService/access_as_user" };

    private readonly ITokenAcquisition _tokenAcquisition;

    public MyApiController(ITokenAcquisition tokenAcquisition)
    {
        _tokenAcquisition = tokenAcquisition;
    }

    public IActionResult Index()
    {
        HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);

        string accessToken = _tokenAcquisition.GetAccessTokenForUserAsync(scopesToAccessDownstreamApi);
        return await callTodoListService(accessToken);
    }
}
```

Yöntemi hakkında daha fazla bilgi için `callTodoListService` bkz.  [Web API 'Leri çağıran BIR Web API 'SI: API çağırma](scenario-web-api-call-api-call-api.md).

### <a name="java"></a>[Java](#tab/java)

API denetleyicilerinin eylemlerinde çağrılan kod örneği aşağıda verilmiştir. Aşağı akış API 'sini Microsoft Graph çağırır.

```java
@RestController
public class ApiController {

    @Autowired
    MsalAuthHelper msalAuthHelper;

    @RequestMapping("/graphMeApi")
    public String graphMeApi() throws MalformedURLException {

        String oboAccessToken = msalAuthHelper.getOboToken("https://graph.microsoft.com/.default");

        return callMicrosoftGraphMeEndpoint(oboAccessToken);
    }

}
```

### <a name="python"></a>[Python](#tab/python)
 
Python Web API 'SI, istemciden alınan taşıyıcı belirtecini doğrulamak için ara yazılım kullanılmasını gerektirir. Web API 'si daha sonra yöntemi çağırarak MSAL Python kitaplığını kullanarak bir aşağı akış API 'sine ait erişim belirtecini alabilir [`acquire_token_on_behalf_of`](https://msal-python.readthedocs.io/en/latest/?badge=latest#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) .
 
Yöntemi ve Flask çerçevesini kullanarak bir erişim belirteci alan kod örneği aşağıda verilmiştir `acquire_token_on_behalf_of` . Azure Yönetim abonelikleri uç noktası olan aşağı akış API 'sini çağırır.
 
```python
def get(self):
 
        _scopes = ["https://management.azure.com/user_impersonation"]
        _azure_management_subscriptions_uri = "https://management.azure.com/subscriptions?api-version=2020-01-01"
 
        current_access_token = request.headers.get("Authorization", None)
        
        #This example only uses the default memory token cache and should not be used for production
        msal_client = msal.ConfidentialClientApplication(
                client_id=os.environ.get("CLIENT_ID"),
                authority=os.environ.get("AUTHORITY"),
                client_credential=os.environ.get("CLIENT_SECRET"))
 
        #acquire token on behalf of the user that called this API
        arm_resource_access_token = msal_client.acquire_token_on_behalf_of(
            user_assertion=current_access_token.split(' ')[1],
            scopes=_scopes
        )
 
        headers = {'Authorization': arm_resource_access_token['token_type'] + ' ' + arm_resource_access_token['access_token']}
 
        subscriptions_list = req.get(_azure_management_subscriptions_uri), headers=headers).json()
 
        return jsonify(subscriptions_list)
```

## <a name="advanced-accessing-the-signed-in-users-token-cache-from-background-apps-apis-and-services"></a>Ileri Arka plan uygulamaları, API 'Ler ve hizmetlerden oturum açmış kullanıcının belirteç önbelleğine erişme

[!INCLUDE [advanced-token-caching](../../../includes/advanced-token-cache.md)]

---

## <a name="next-steps"></a>Sonraki adımlar

Bu senaryodaki bir sonraki makaleye geçin, [BIR API çağırın](scenario-web-api-call-api-call-api.md).
