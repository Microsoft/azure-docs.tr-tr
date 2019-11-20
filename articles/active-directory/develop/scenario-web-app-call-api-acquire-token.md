---
title: Web API 'Lerini çağıran Web uygulaması (uygulama için bir belirteç edinin)-Microsoft Identity platform
description: Web API 'Lerini çağıran bir Web uygulaması oluşturmayı öğrenin (uygulama için bir belirteç alma)
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
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a259fbcf3fde84edccafbcd2fd6594ddb623edfd
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73175336"
---
# <a name="web-app-that-calls-web-apis---acquire-a-token-for-the-app"></a>Web API 'Lerini çağıran Web uygulaması-uygulama için bir belirteç alın

Artık size istemci uygulama nesnesi oluşturduğunuza göre, bir Web API 'SI çağırmak için bir belirteç almak üzere onu kullanacaksınız. ASP.NET veya ASP.NET Core içinde, bir Web API 'SI çağrısı daha sonra denetleyicide yapılır. Şu şekilde olur:

- Belirteç önbelleğini kullanarak Web API 'SI için belirteç alma. Bu belirteci almak için `AcquireTokenSilent` ' ı çağırın.
- Korumalı API 'yi erişim belirteciyle çağırma.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Denetleyici yöntemleri, kullanıcıların kimlik doğrulamasından geçen ve Web uygulamasını kullanmasına zorlayan bir `[Authorize]` özniteliği tarafından korunur. Microsoft Graph çağıran kod aşağıda verilmiştir.

```CSharp
[Authorize]
public class HomeController : Controller
{
 readonly ITokenAcquisition tokenAcquisition;

 public HomeController(ITokenAcquisition tokenAcquisition)
 {
  this.tokenAcquisition = tokenAcquisition;
 }

 // Code for the controller actions(see code below)

}
```

`ITokenAcquisition` hizmeti bağımlılık ekleme yoluyla ASP.NET tarafından eklenir.


Aşağıda, Microsoft Graph çağırmak için bir belirteç alan HomeController eyleminin basit bir kodu verilmiştir.

```CSharp
public async Task<IActionResult> Profile()
{
 // Acquire the access token
 string[] scopes = new string[]{"user.read"};
 string accessToken = await tokenAcquisition.GetAccessTokenOnBehalfOfUserAsync(scopes);

 // use the access token to call a protected web API
 HttpClient client = new HttpClient();
 client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
 string json = await client.GetStringAsync(url);
}
```

Bu senaryo için gereken kodu daha kapsamlı olarak anlamak için, [MS-Identity-aspnetcore-WebApp-öğretici](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) öğreticisinin 2. aşama ([2-1-Web uygulaması çağrı Microsoft Graph](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph)) adımına bakın.

Şöyle birçok ek karmaşıklık vardır:

- Çeşitli API 'Ler çağırma,
- Artımlı onay ve koşullu erişim işleniyor.

Bu gelişmiş adımlar, [3. WebApp-WebApp-çoklu API](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/3-WebApp-multi-APIs) 'lerin Bölüm 3 ' te işlenir

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET ' de benzer şeyler:

- Bir [Yetkilendir] özniteliğiyle korunan bir denetleyici eylemi, denetleyicinin `ClaimsPrincipal` üyesinin kiracı KIMLIĞINI ve kullanıcı KIMLIĞINI ayıklar. (ASP.NET `HttpContext.User` kullanır.)
- Buradan, bir MSAL.NET `IConfidentialClientApplication`oluşturur.
- Son olarak, gizli istemci uygulamasının `AcquireTokenSilent` yöntemini çağırır.

Kod, ASP.NET Core gösterilen koda benzerdir.

# <a name="javatabjava"></a>[Java](#tab/java)

Java örneğinde, bir API çağıran kod, getUsersFromGraph yönteminde [Authpagecontroller. Java # L62](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L62).

`getAuthResultBySilentFlow`çağırmaya çalışır. Kullanıcının daha fazla kapsam onaylaması gerekiyorsa, kod, kullanıcıyı zorluk `MsalInteractionRequiredException` ' ı işler.

```java
@RequestMapping("/msal4jsample/graph/me")
public ModelAndView getUserFromGraph(HttpServletRequest httpRequest, HttpServletResponse response)
        throws Throwable {

    IAuthenticationResult result;
    ModelAndView mav;
    try {
        result = authHelper.getAuthResultBySilentFlow(httpRequest, response);
    } catch (ExecutionException e) {
        if (e.getCause() instanceof MsalInteractionRequiredException) {

            // If silent call returns MsalInteractionRequired, then redirect to Authorization endpoint
            // so user can consent to new scopes
            String state = UUID.randomUUID().toString();
            String nonce = UUID.randomUUID().toString();

            SessionManagementHelper.storeStateAndNonceInSession(httpRequest.getSession(), state, nonce);

            String authorizationCodeUrl = authHelper.getAuthorizationCodeUrl(
                    httpRequest.getParameter("claims"),
                    "User.Read",
                    authHelper.getRedirectUriGraph(),
                    state,
                    nonce);

            return new ModelAndView("redirect:" + authorizationCodeUrl);
        } else {

            mav = new ModelAndView("error");
            mav.addObject("error", e);
            return mav;
        }
    }

    if (result == null) {
        mav = new ModelAndView("error");
        mav.addObject("error", new Exception("AuthenticationResult not found in session."));
    } else {
        mav = new ModelAndView("auth_page");
        setAccountInfo(mav, httpRequest);

        try {
            mav.addObject("userInfo", getUserInfoFromGraph(result.accessToken()));

            return mav;
        } catch (Exception e) {
            mav = new ModelAndView("error");
            mav.addObject("error", e);
        }
    }
    return mav;
}
// Code omitted here.
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Python örneğinde Microsoft Graph çağırma kodu [app. Sip # L53-L62](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/48637475ed7d7733795ebeac55c5d58663714c60/app.py#L53-L62).

Belirteç önbelleğinden bir belirteç almaya çalışır ve sonra yetkilendirme üst bilgisini ayarladıktan sonra EB API 'sini çağırır. Bu durumda, kullanıcıya yeniden oturum açar.

```python
@app.route("/graphcall")
def graphcall():
    token = _get_token_from_cache(app_config.SCOPE)
    if not token:
        return redirect(url_for("login"))
    graph_data = requests.get(  # Use token to call downstream service
        app_config.ENDPOINT,
        headers={'Authorization': 'Bearer ' + token['access_token']},
        ).json()
    return render_template('display.html', result=graph_data)
```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Web API çağrısı](scenario-web-app-call-api-call-api.md)
