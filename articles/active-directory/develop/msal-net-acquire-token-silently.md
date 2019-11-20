---
title: Sessizce belirteç alma (.NET için Microsoft kimlik doğrulama kitaplığı)
titleSuffix: Microsoft identity platform
description: .NET için Microsoft kimlik doğrulama kitaplığı 'nı (MSAL.NET) kullanarak bir erişim belirtecini sessizce (belirteç önbelleğinden) nasıl edinebileceğinizi öğrenin.
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
ms.date: 07/16/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1dc573c37aab95006f647509080fb9883d8650c7
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72802930"
---
# <a name="get-a-token-from-the-token-cache-using-msalnet"></a>MSAL.NET kullanarak belirteç önbelleğinden belirteç alma

.NET için Microsoft kimlik doğrulama kitaplığı 'nı (MSAL.NET) kullanarak bir erişim belirteci aldığınızda, belirteç önbelleğe alınır. Uygulamanın bir belirtece ihtiyacı olduğunda, kabul edilebilir bir belirtecin önbellekte olup olmadığını doğrulamak için öncelikle `AcquireTokenSilent` yöntemini çağırmalıdır. Çoğu durumda, önbellekteki bir belirtece göre daha fazla kapsam içeren başka bir belirteç elde etmek mümkündür. Ayrıca, kullanım süresi dolmak üzere (belirteç önbelleği de bir yenileme belirteci içerdiğinden) bir belirteci yenilemek mümkündür.

Önerilen model öncelikle `AcquireTokenSilent` yöntemini çağırmalıdır.  `AcquireTokenSilent` başarısız olursa, diğer yöntemleri kullanarak bir belirteç alın.

Aşağıdaki örnekte, uygulama ilk olarak belirteç önbelleğinden bir belirteç edinmeye çalışır.  `MsalUiRequiredException` bir özel durum oluşturulursa, uygulama bir belirteci etkileşimli olarak alır. 

```csharp
AuthenticationResult result = null;
var accounts = await app.GetAccountsAsync();

try
{
 result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
        .ExecuteAsync();
}
catch (MsalUiRequiredException ex)
{
 // A MsalUiRequiredException happened on AcquireTokenSilent.
 // This indicates you need to call AcquireTokenInteractive to acquire a token
 System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

 try
 {
    result = await app.AcquireTokenInteractive(scopes)
          .ExecuteAsync();
 }
 catch (MsalException msalex)
 {
    ResultText.Text = $"Error Acquiring Token:{System.Environment.NewLine}{msalex}";
 }
}
catch (Exception ex)
{
 ResultText.Text = $"Error Acquiring Token Silently:{System.Environment.NewLine}{ex}";
 return;
}

if (result != null)
{
 string accessToken = result.AccessToken;
 // Use the token
}
```
