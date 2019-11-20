---
title: Web API 'Lerini çağıran Web uygulaması (oturum açma)-Microsoft Identity platform
description: Web API 'Lerini çağıran bir Web uygulaması oluşturma (oturum açma) hakkında bilgi edinin
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
ms.date: 09/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2cb0190f76ddce79012a5bf97e2d813f40f9f018
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/18/2019
ms.locfileid: "72596373"
---
# <a name="web-app-that-calls-web-apis---sign-in"></a>Web API 'Lerini çağıran Web uygulaması-oturum açma

Web uygulamanıza nasıl oturum açma ekleneceğini zaten biliyoruz. Web uygulamasında, [kullanıcıların oturum açması için oturum açma eklemesi](scenario-web-app-sign-user-sign-in.md)gerektiğini öğrenirsiniz.

Burada, Kullanıcı oturumu kapatmışsa, bu uygulamadan veya herhangi bir uygulamadan, Kullanıcı ile ilişkili belirteçlerin bulunduğu belirteç önbelleğinden kaldırmak istediğiniz zaman farklıdır.

## <a name="intercepting-the-callback-after-sign-out---single-sign-out"></a>Oturum kapatıldıktan sonra geri çağırma işlemini kesintiye uğratan ve çoklu oturum kapatma

Uygulamanız, bir sonraki `logout` olayını ele geçirebilir, örneğin, oturum açan hesapla ilişkili belirteç önbelleğinin girişini temizlemek için. Web uygulaması, Kullanıcı için erişim belirteçlerini önbellekte depolar. @No__t_0 geri çağırma sonrasında, Web uygulamanızın kullanıcı belirtecini belirteç önbelleğinden kaldırmasını sağlar.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Bu mekanizma, [WebAppServiceCollectionExtensions. cs # L151-L157](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/db7f74fd7e65bab9d21092ac1b98a00803e5ceb2/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L157) `AddMsal()` yönteminde gösterilmiştir

Uygulamanız için kaydettiğiniz **oturum kapatma URL 'si** , çoklu oturum kapatma uygulamanıza olanak sağlar. Microsoft Identity platform `logout` uç noktası, uygulamanıza kayıtlı **oturum kapatma URL** 'sini çağıracaktır. Bu çağrı, oturum kapatma Web uygulamanızdan veya başka bir Web uygulamasından ya da tarayıcıdan başlatılmışsa oluşur. Daha fazla bilgi için bkz. [Çoklu oturum kapatma](v2-protocols-oidc.md#single-sign-out).

```CSharp
public static class WebAppServiceCollectionExtensions
{
 public static IServiceCollection AddMsal(this IServiceCollection services, IConfiguration configuration, IEnumerable<string> initialScopes, string configSectionName = "AzureAd")
 {
  // Code omitted here

  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
   // Code omitted here

   // Handling the sign-out: removing the account from MSAL.NET cache
   options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
   {
    // Remove the account from MSAL.NET token cache
    var tokenAcquisition = context.HttpContext.RequestServices.GetRequiredService<ITokenAcquisition>();
    await tokenAcquisition.RemoveAccountAsync(context).ConfigureAwait(false);
   };
  });
  return services;
 }
}
```

RemoveAccountAsync kodu, [Microsoft. Identity. Web/Tokenalımı. cs # L264-L288](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/db7f74fd7e65bab9d21092ac1b98a00803e5ceb2/Microsoft.Identity.Web/TokenAcquisition.cs#L264-L288)' den edinilebilir.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET örneği genel oturum kapatma sırasında önbellekten hesap kaldırmaz

# <a name="javatabjava"></a>[Java](#tab/java)

Java örneği genel oturum kapatma sırasında önbellekten hesap kaldırmaz

# <a name="pythontabpython"></a>[Python](#tab/python)

Python örneği genel oturum kapatma sırasında önbellekten hesap kaldırmaz

---

## <a name="next-steps"></a>Sonraki adımlar

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [Web uygulaması için bir belirteç alınıyor](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=aspnetcore)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [Web uygulaması için bir belirteç alınıyor](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=aspnet)

# <a name="javatabjava"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Web uygulaması için bir belirteç alınıyor](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=java)

# <a name="pythontabpython"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Web uygulaması için bir belirteç alınıyor](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=python)

---
