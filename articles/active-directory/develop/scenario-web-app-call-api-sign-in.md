---
title: Oturum açarken belirteç önbelleğinden hesapları kaldır | Mavisi
titleSuffix: Microsoft identity platform
description: Oturum kapatma sırasında bir hesabı belirteç önbelleğinden kaldırma hakkında bilgi edinin
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 9fc271dfa9edbedac8527009dd2b2180b7c5e7cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98756247"
---
# <a name="a-web-app-that-calls-web-apis-remove-accounts-from-the-token-cache-on-global-sign-out"></a>Web API 'Lerini çağıran bir Web uygulaması: genel oturum açma sırasında belirteç önbelleğinden hesapları kaldırma

Web uygulamasında oturum açma ve oturum kapatma için [Kullanıcı oturumunu açan](scenario-web-app-sign-user-sign-in.md)Web uygulamasına oturum açma eklemeyi öğrendiniz.

Oturum kapatma, Web API 'lerini çağıran bir Web uygulaması için farklıdır. Kullanıcı uygulamanızdan veya herhangi bir uygulamadan oturumu kapattığında, bu kullanıcıyla ilişkili belirteçleri belirteç önbelleğinden kaldırmanız gerekir.

## <a name="intercept-the-callback-after-single-sign-out"></a>Çoklu oturum kapatıldıktan sonra geri çağırma işlemini kesme

Oturum açan hesapla ilişkili belirteç önbelleği girişini temizlemek için, uygulamanız After olayını ele geçirebilir `logout` . Web Apps, bir belirteç önbelleğindeki her bir kullanıcı için erişim belirteçlerini depolar. `logout`Geri aramadan sonra, Web uygulamanız kullanıcıyı önbellekten kaldırabilir.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Microsoft. Identity. Web, oturum kapatma işlemini sizin yerinize uygulamayı gerçekleştirir. Ayrıntılar için bkz. [Microsoft. Identity. Web kaynak kodu](https://github.com/AzureAD/microsoft-identity-web/blob/c29f1a7950b940208440bebf0bcb524a7d6bee22/src/Microsoft.Identity.Web/WebAppExtensions/WebAppCallsWebApiAuthenticationBuilderExtensions.cs#L168-L176)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET örneği genel oturum kapatma üzerindeki önbellekten hesapları kaldırmaz.

# <a name="java"></a>[Java](#tab/java)

Java örneği, genel oturum kapatma üzerindeki önbellekten hesapları kaldırmaz.

# <a name="python"></a>[Python](#tab/python)

Python örneği genel oturum kapatma üzerindeki önbellekten hesapları kaldırmaz.

---

## <a name="next-steps"></a>Sonraki adımlar

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Bu senaryodaki bir sonraki makaleye geçin, [Web uygulaması için bir belirteç alın](./scenario-web-app-call-api-acquire-token.md?tabs=aspnetcore).

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Bu senaryodaki bir sonraki makaleye geçin, [Web uygulaması için bir belirteç alın](./scenario-web-app-call-api-acquire-token.md?tabs=aspnet).

# <a name="java"></a>[Java](#tab/java)

Bu senaryodaki bir sonraki makaleye geçin, [Web uygulaması için bir belirteç alın](./scenario-web-app-call-api-acquire-token.md?tabs=java).

# <a name="python"></a>[Python](#tab/python)

Bu senaryodaki bir sonraki makaleye geçin, [Web uygulaması için bir belirteç alın](./scenario-web-app-call-api-acquire-token.md?tabs=python).

---