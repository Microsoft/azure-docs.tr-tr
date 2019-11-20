---
title: Seçeneklerle bir gizli istemci uygulaması örneğini oluşturma (.NET için Microsoft kimlik doğrulama kitaplığı)
titleSuffix: Microsoft identity platform
description: .NET için Microsoft kimlik doğrulama kitaplığı 'nı (MSAL.NET) kullanarak yapılandırma seçenekleriyle gizli bir istemci uygulamasını nasıl örnekleyeceğinizi öğrenin.
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/30/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e382a8d0b5d6f08eafc5621d0e7591111a5e286b
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72802819"
---
# <a name="instantiate-a-confidential-client-application-with-configuration-options-using-msalnet"></a>MSAL.NET kullanarak yapılandırma seçenekleriyle bir gizli istemci uygulaması örneği oluşturma

Bu makalede, .NET için Microsoft kimlik doğrulama kitaplığı 'nı (MSAL.NET) kullanarak bir [Gizli istemci uygulamasının](msal-client-applications.md) örneğini oluşturma açıklanmaktadır.  Uygulama, bir ayar dosyasında tanımlanan yapılandırma seçenekleriyle birlikte oluşturulur.

Uygulamayı başlatmadan önce, uygulamanızın Microsoft Identity platformu ile tümleştirilebilmesi için öncelikle [kaydetmeniz](quickstart-register-app.md) gerekir. Kayıttan sonra, aşağıdaki bilgiler (Azure portal bulunabilir) gerekebilir:

- İstemci KIMLIĞI (bir GUID 'YI temsil eden dize)
- Kimlik sağlayıcısı URL 'SI (örnek olarak adlandırılır) ve uygulamanız için oturum açma hedef kitlesi. Bu iki parametre, her topluca yetkili olarak bilinir.
- Yalnızca kuruluşunuz için bir iş kolu uygulaması yazıyorsanız (tek kiracılı uygulama olarak da adlandırılır) kiracı KIMLIĞI.
- Gizli bir istemci uygulaması ise, uygulama gizli anahtarı (istemci gizli dizisi) veya sertifika (X509Certificate2 türünde).
- Web uygulamaları için ve bazen genel istemci uygulamaları için (uygulamanızın bir aracı kullanması gerektiğinde), kimlik sağlayıcısının güvenlik belirteçleriyle uygulamanızı geri yükleyeceğim yeniden yönlendirilebilir.

## <a name="configure-the-application-from-the-config-file"></a>Uygulamayı yapılandırma dosyasından yapılandırma
MSAL.NET içindeki seçeneklerin özelliklerinin adı, ASP.NET Core `AzureADOptions` özelliklerinin adı ile eşleşir, bu nedenle herhangi bir birleştirici kodu yazmanıza gerek kalmaz.

Bir ASP.NET Core uygulama yapılandırması, bir *appSettings. JSON* dosyasında açıklanmıştır:

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "[Enter the domain of your tenant, e.g. contoso.onmicrosoft.com]",
    "TenantId": "[Enter 'common', or 'organizations' or the Tenant Id (Obtained from the Azure portal. Select 'Endpoints' from the 'App registrations' blade and use the GUID in any of the URLs), e.g. da41245a5-11b3-996c-00a8-4d99re19f292]",
    "ClientId": "[Enter the Client Id (Application ID obtained from the Azure portal), e.g. ba74781c2-53c2-442a-97c2-3d60re42f403]",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-callback-oidc",

    "ClientSecret": "[Copy the client secret added to the app from the Azure portal]"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

MSAL.NET v3. x sürümünden başlayarak, gizli istemci uygulamanızı yapılandırma dosyasından yapılandırabilirsiniz.

Uygulamanızı yapılandırmak ve örneklerinizi başlatmak istediğiniz sınıfta bir `ConfidentialClientApplicationOptions` nesnesi bildirmeniz gerekir.  [Microsoft. Extensions. Configuration. Ciltçi NuGet paketindeki](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder)`IConfigurationRoot.Bind()` yöntemi kullanılarak kaynaktan okunan yapılandırmayı (appconfig. JSON dosyası dahil) uygulama seçeneklerinin örneğine bağlayın:

```csharp
using Microsoft.Identity.Client;

private ConfidentialClientApplicationOptions _applicationOptions;
_applicationOptions = new ConfidentialClientApplicationOptions();
configuration.Bind("AzureAD", _applicationOptions);
```

Bu, *appSettings. JSON* dosyasının "azuread" bölümünün içeriğinin `ConfidentialClientApplicationOptions` nesnesinin karşılık gelen özelliklerine bağlanmasını sağlar.  Sonra bir `ConfidentialClientApplication` nesnesi oluşturun:

```csharp
IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
        .Build();
```

## <a name="add-runtime-configuration"></a>Çalışma Zamanı Yapılandırması Ekle
Gizli bir istemci uygulamasında, genellikle Kullanıcı başına bir önbelleğiniz olur. Bu nedenle, kullanıcıyla ilişkili önbelleği almanız ve uygulamayı kullanmak istediğiniz uygulama oluşturucusunu bildirmeniz gerekecektir. Aynı şekilde, dinamik olarak hesaplanmış bir yeniden yönlendirme URI 'SI olabilir. Bu durumda, kod aşağıdaki gibi olur:

```csharp
IConfidentialClientApplication app;
var request = httpContext.Request;
var currentUri = UriHelper.BuildAbsolute(request.Scheme, request.Host, request.PathBase, _azureAdOptions.CallbackPath ?? string.Empty);
app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
       .WithRedirectUri(currentUri)
       .Build();
TokenCache userTokenCache = _tokenCacheProvider.SerializeCache(app.UserTokenCache,httpContext, claimsPrincipal);
```

