---
title: Kapsamları ve uygulama rollerini korunan Web API 'sini doğrulama | Mavisi
titleSuffix: Microsoft identity platform
description: API 'nin yalnızca doğru kapsamları olan kullanıcılar adına uygulamalar tarafından ve doğru uygulama rollerinin bulunduğu Daemon uygulamaları tarafından çağrıldığından emin olun.
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
ms.openlocfilehash: 6d737f107e7a9b1476fdf86ac0320931ea137671
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94442914"
---
# <a name="protected-web-api-verify-scopes-and-app-roles"></a>Korumalı Web API 'SI: kapsamları ve uygulama rollerini doğrulama

Bu makalede, Web API 'nize nasıl yetkilendirme ekleyebileceğiniz açıklanır. Bu koruma, API 'nin yalnızca şu şekilde çağrılmasına sağlar:

- Doğru kapsamları olan kullanıcılar adına uygulamalar.
- Doğru uygulama rollerine sahip uygulamaları Daemon.

> [!NOTE]
> Bu makaledeki kod parçacıkları GitHub 'daki aşağıdaki kod örneklerinden ayıklanır:
>
> - [ASP.NET Core Web API 'SI artımlı öğreticisi](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/master/1.%20Desktop%20app%20calls%20Web%20API/TodoListService/Controllers/TodoListController.cs)
> - [ASP.NET Web API 'SI örneği](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof/blob/master/TodoListService/Controllers/TodoListController.cs)

Bir ASP.NET veya ASP.NET Core Web API 'sini korumak için, `[Authorize]` aşağıdaki öğelerden birine özniteliğini eklemeniz gerekir:

- Tüm denetleyici eylemlerinin korunmasını istiyorsanız denetleyicinin kendisi
- API 'niz için bireysel denetleyici eylemi

```csharp
    [Authorize]
    public class TodoListController : Controller
    {
     ...
    }
```

Ancak bu koruma yeterli değildir. Yalnızca bu ASP.NET ve ASP.NET Core belirtecini doğrular. API 'nizin, API 'YI çağırmak için kullanılan belirtecin beklenen taleplerle istendiğini doğrulaması gerekir. Bu talepler belirli bir doğrulamaya gerek duyar:

- Bir kullanıcı adına API çağrılırsa *kapsamlar* .
- API bir Daemon uygulamasından çağrılabilecek ise *uygulama rolleri* .

## <a name="verify-scopes-in-apis-called-on-behalf-of-users"></a>API 'lerde Kullanıcı adına çağrılan kapsamları doğrulama

İstemci uygulaması, API 'nizi bir kullanıcı adına çağırırsa, API 'nin API için belirli kapsamları olan bir taşıyıcı belirteç istemesi gerekir. Daha fazla bilgi için bkz. [kod yapılandırma | Taşıyıcı belirteci](scenario-protected-web-api-app-configuration.md#bearer-token).

### <a name="net-core"></a>.NET Core

#### <a name="verify-the-scopes-on-each-controller-action"></a>Her denetleyici eyleminde kapsamları doğrulama

```csharp
[Authorize]
public class TodoListController : Controller
{
    /// <summary>
    /// The web API will accept only tokens 1) for users, 2) that have the `access_as_user` scope for
    /// this API.
    /// </summary>
    static readonly string[] scopeRequiredByApi = new string[] { "access_as_user" };

    // GET: api/values
    [HttpGet]
    public IEnumerable<TodoItem> Get()
    {
         HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);
        // Do the work and return the result.
        // ...
    }
...
}
```

`VerifyUserHasAnyAcceptedScope`Yöntemi aşağıdaki adımlara benzer bir şekilde yapılır:

- Veya adlı bir talep olduğunu doğrulayın `http://schemas.microsoft.com/identity/claims/scope` `scp` .
- Talebin API tarafından beklenen kapsamı içeren bir değeri olduğunu doğrulayın.


#### <a name="verify-the-scopes-more-globally"></a>Kapsamları daha küresel olarak doğrulayın

Web API 'niz için ayrıntılı kapsamları tanımlama ve her denetleyicideki kapsamların doğrulanması önerilen yaklaşımdır. Ancak, ASP.NET Core kullanarak, uygulama veya bir denetleyici düzeyinde kapsamları doğrulamak da mümkündür. Ayrıntılar için ASP.NET Core belgelerindeki [talep tabanlı yetkilendirme](/aspnet/core/security/authorization/claims) bölümüne bakın.

### <a name="net-mvc"></a>.NET MVC

ASP.NET için yalnızca ile değiştirin `HttpContext.User` `ClaimsPrincipal.Current` ve talep türünü `"http://schemas.microsoft.com/identity/claims/scope"` ile değiştirin `"scp"` . Ayrıca, bu makalenin ilerleyen kısımlarında kod parçacığına bakın.

## <a name="verify-app-roles-in-apis-called-by-daemon-apps"></a>Daemon uygulamaları tarafından çağrılan API 'lerde uygulama rollerini doğrulama

Web API 'niz bir [Daemon uygulaması](scenario-daemon-overview.md)tarafından çağrılırsa, bu UYGULAMANıN Web API 'niz için uygulama izni olması gerekir. [Uygulama izinlerini (uygulama rolleri) gösterme](./scenario-protected-web-api-app-registration.md#exposing-application-permissions-app-roles)bölümünde gösterildiği gıbı, API 'niz bu izinleri gösterir. Uygulama rolü bir örnektir `access_as_application` .

Şimdi API 'nizin aldığı belirtecin `roles` talebi içerdiğini ve bu talebin beklenen değere sahip olduğunu doğrulaması gerekir. Doğrulama kodu, izin verilen izinleri doğrulayan koda benzerdir, ancak denetleyici eylemi kapsamlar yerine roller için test eder:

### <a name="aspnet-core"></a>ASP.NET Core

```csharp
[Authorize]
public class TodoListController : ApiController
{
    public IEnumerable<TodoItem> Get()
    {
        HttpContext.ValidateAppRole("access_as_application");
        ...
    }
```

`ValidateAppRole`Yöntemi, Microsoft. Identity. Web Içinde [Rolesrequiredhttpcontexlersions. cs](https://github.com/AzureAD/microsoft-identity-web/blob/d2ad0f5f830391a34175d48621a2c56011a45082/src/Microsoft.Identity.Web/Resource/RolesRequiredHttpContextExtensions.cs#L28)içinde tanımlanmıştır.

### <a name="aspnet-mvc"></a>ASP.NET MVC

```csharp
private void ValidateAppRole(string appRole)
{
    //
    // The `role` claim tells you what permissions the client application has in the service.
    // In this case, we look for a `role` value of `access_as_application`.
    //
    Claim roleClaim = ClaimsPrincipal.Current.FindFirst("roles");
    if (roleClaim == null || !roleClaim.Value.Split(' ').Contains(appRole))
    {
        throw new HttpResponseException(new HttpResponseMessage
        { StatusCode = HttpStatusCode.Unauthorized,
            ReasonPhrase = $"The 'roles' claim does not contain '{appRole}' or was not found"
        });
    }
}
}
```

### <a name="accepting-app-only-tokens-if-the-web-api-should-be-called-only-by-daemon-apps"></a>Web API 'sinin yalnızca Daemon uygulamaları tarafından çağrılması gerekiyorsa yalnızca uygulama belirteçlerini kabul etme

Kullanıcılar ayrıca, [nasıl yapılır: uygulamanızda uygulama rolleri ekleme ve bunları belirteçte alma](howto-add-app-roles-in-azure-ad-apps.md)bölümünde gösterildiği gibi kullanıcı atama desenlerinde roller taleplerini da kullanabilir. Roller her ikisine de atanabilir ise, rolleri denetlemek uygulamaların kullanıcılar ve kullanıcılar olarak oturum açmalarına olanak tanır. Bu karışıklık oluşmasını önlemek için kullanıcılar ve uygulamalar için farklı roller bildirmeniz önerilir.

Yalnızca Daemon uygulamalarının Web API 'nizi çağırmasını istiyorsanız, uygulama rolünü doğruladığınızda, belirtecin yalnızca uygulama belirteci olduğu koşulu ekleyin.

```csharp
string oid = ClaimsPrincipal.Current.FindFirst("oid")?.Value;
string sub = ClaimsPrincipal.Current.FindFirst("sub")?.Value;
bool isAppOnlyToken = oid == sub;
```

Ters koşulun denetlenmesi, yalnızca bir kullanıcının API 'nizi çağırması için oturum açmasını sağlar.

## <a name="next-steps"></a>Sonraki adımlar

Bu senaryodaki sonraki makaleye geçin, [üretime geçin](scenario-protected-web-api-production.md).
