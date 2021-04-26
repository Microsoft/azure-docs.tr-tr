---
title: Belirteç önbelleğini temizle (MSAL.NET) | Mavisi
titleSuffix: Microsoft identity platform
description: .NET için Microsoft kimlik doğrulama kitaplığı 'nı (MSAL.NET) kullanarak belirteç önbelleğini temizleme hakkında bilgi edinin.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 05/07/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 3049a1213b8b92153fc0fce96b2dadace01a4ca8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98064768"
---
# <a name="clear-the-token-cache-using-msalnet"></a>MSAL.NET kullanarak belirteç önbelleğini temizleme

.NET için Microsoft kimlik doğrulama kitaplığı 'nı (MSAL.NET) kullanarak [bir erişim belirteci](msal-acquire-cache-tokens.md) aldığınızda, belirteç önbelleğe alınır. Uygulamanın bir belirtece ihtiyacı olduğunda, öncelikle `AcquireTokenSilent` kabul edilebilir bir belirtecin önbellekte olup olmadığını doğrulamak için yöntemini çağırmalıdır. 

Hesapları önbellekten kaldırarak önbelleğin temizlenmesi sağlanır. Bu, tarayıcıda bulunan oturum tanımlama bilgisini kaldırmaz, ancak.  Aşağıdaki örnek bir ortak istemci uygulaması örnekleyen, uygulamanın hesaplarını alır ve hesapları kaldırır.

```csharp
private readonly IPublicClientApplication _app;
private static readonly string ClientId = ConfigurationManager.AppSettings["ida:ClientId"];
private static readonly string Authority = string.Format(CultureInfo.InvariantCulture, AadInstance, Tenant);

_app = PublicClientApplicationBuilder.Create(ClientId)
                .WithAuthority(Authority)
                .Build();

var accounts = (await _app.GetAccountsAsync()).ToList();

// clear the cache
while (accounts.Any())
{
   await _app.RemoveAsync(accounts.First());
   accounts = (await _app.GetAccountsAsync()).ToList();
}

```

Belirteçleri edinme ve önbelleğe alma hakkında daha fazla bilgi edinmek için, [erişim belirteci alma](msal-acquire-cache-tokens.md)makalesini okuyun.
