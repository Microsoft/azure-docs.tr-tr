---
title: Chrome tarayıcısında SameSite tanımlama bilgisi değişikliklerini işleme | Mavisi
titleSuffix: Microsoft identity platform
description: Chrome tarayıcısında SameSite tanımlama bilgisi değişikliklerini nasıl işleyeceğinizi öğrenin.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 01/27/2020
ms.author: jmprieur
ms.reviewer: kkrishna
ms.custom: aaddev
ms.openlocfilehash: 30c4f054259aa7c3f2a9fdfaeeadd64f26dd9bea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94444920"
---
# <a name="handle-samesite-cookie-changes-in-chrome-browser"></a>Chrome tarayıcısında SameSite tanımlama bilgisi değişikliklerini işleme

## <a name="what-is-samesite"></a>SameSite nedir?

`SameSite` , Web uygulamalarında siteler arası Istek sahteciliği (CSRF) saldırılarını engellemek için HTTP tanımlama bilgilerinde ayarlanabilecek bir özelliktir:

- , `SameSite` **LAX** olarak ayarlandığında, tanımlama bilgisi aynı site içindeki isteklere ve diğer sitelerden alınan isteklere gönderilir. Etki alanları arası olan GET isteklerinde gönderilmez.
- **Katı** değeri, tanımlama bilgisinin yalnızca aynı site içindeki isteklere gönderilmesini sağlar.

Varsayılan olarak, `SameSite` Bu değer tarayıcılarda AYARLı değildir ve isteklerde gönderilen tanımlama bilgilerinde hiçbir kısıtlama yoktur. Bir uygulamanın, gereksinimleri uyarınca **LAX** veya **katı** ayarlayarak CSRF korumasını kabul etmeniz gerekir.

## <a name="samesite-changes-and-impact-on-authentication"></a>SameSite değişiklikleri ve kimlik doğrulaması üzerindeki etki

[SameSite standartlarına yönelik son güncelleştirmeler](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00) , `SameSite` hiçbir değer LAX olarak ayarlanmamışsa varsayılan davranışı yaparak uygulamaları korumayı önerin. Bu hafifletme, diğer sitelerden yapılan GET dışındaki HTTP isteklerinde tanımlama bilgilerinin kısıtlandığı anlamına gelir. Ayrıca, gönderilen tanımlama bilgilerinde kısıtlamaları kaldırmak için **none** değeri de eklenmiştir. Bu güncelleştirmeler yakında Chrome tarayıcısının gelecek bir sürümünde yayımlanacak.

Web Apps, "form_post" Yanıt modunu kullanarak Microsoft Identity platformu ile kimlik doğrulaması yaparken, oturum açma sunucusu, belirteçleri veya kimlik doğrulama kodunu göndermek için HTTP POST kullanarak uygulamaya yanıt verir. Bu istek bir çapraz etki alanı isteği ( `login.microsoftonline.com` Örneğin, etki alanına `https://contoso.com/auth` ) olduğundan, uygulamanız tarafından ayarlanan tanımlama bilgileri artık Chrome 'daki yeni kurallara girer. Siteler arası senaryolarda kullanılması gereken tanımlama bilgileri, oturum açma isteğinde de gönderilen *durum* ve *nonce* değerlerini tutan tanımlama bilgileriylardır. Oturumu tutmak için Azure AD tarafından bırakılan diğer tanımlama bilgileri vardır.

Web uygulamalarınızı güncelleştirmemeniz durumunda, bu yeni davranış kimlik doğrulama hatalarıyla sonuçlanır.

## <a name="mitigation-and-samples"></a>Risk azaltma ve örnekler

Kimlik doğrulama başarısızlıklarını aşmak için, Microsoft Identity platformunda kimlik doğrulaması yapan web uygulamaları, `SameSite` `None` Chrome tarayıcısı üzerinde çalışırken etki alanları arası senaryolarda kullanılan tanımlama bilgileri için özelliğini olarak ayarlayabilir.
Diğer tarayıcılar (tüm liste için [buraya](https://www.chromium.org/updates/same-site/incompatible-clients) bakın) önceki davranışını izleyin `SameSite` ve ayarlandıysa tanımlama bilgilerini içermez `SameSite=None` .
Bu nedenle, birden çok tarayıcıda kimlik doğrulamasını desteklemek için Web uygulamalarında `SameSite` değeri `None` yalnızca Chrome üzerinde olarak ayarlamak ve diğer tarayıcılarda değeri boş bırakmak gerekecektir.

Bu yaklaşım aşağıdaki kod örneklerimizde gösterilmiştir.

# <a name="net"></a>[.NET](#tab/dotnet)

Aşağıdaki tabloda, ASP.NET ve ASP.NET Core örneklerimizde, SameSite değişiklikleri etrafında çalışan çekme istekleri gösterilmektedir.

| Örnek | Çekme isteği |
| ------ | ------------ |
|  [ASP.NET Core Web uygulaması artımlı öğreticisi](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)  |  [Aynı site tanımlama bilgisi onarımı #261](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/pull/261)  |
|  [ASP.NET MVC web uygulaması örneği](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect)  |  [Aynı site tanımlama bilgisi onarımı #35](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/pull/35)  |
|  [Active-Directory-DotNet-admin-Restricted-Scopes-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2)  |  [Aynı site tanımlama bilgisi onarımı #28](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2/pull/28)  |

ASP.NET ve ASP.NET Core ' de SameSite tanımlama bilgilerinin nasıl işleneceği hakkında ayrıntılı bilgi için bkz:

- [ASP.NET Core ' de SameSite tanımlama bilgileriyle çalışın](/aspnet/core/security/samesite) .
- [ASP.NET blogu, SameSite sorunu](https://devblogs.microsoft.com/aspnet/upcoming-samesite-cookie-changes-in-asp-net-and-asp-net-core/)

# <a name="python"></a>[Python](#tab/python)

| Örnek |
| ------ |
|  [MS-Identity-Python-WebApp](https://github.com/Azure-Samples/ms-identity-python-webapp)  |

# <a name="java"></a>[Java](#tab/java)

| Örnek | Çekme isteği |
| ------ | ------------ |
|  [MS-Identity-Java-WebApp](https://github.com/Azure-Samples/ms-identity-java-webapp)  | [Aynı site tanımlama bilgisi onarımı #24](https://github.com/Azure-Samples/ms-identity-java-webapp/pull/24)
|  [MS-Identity-Java-WebApi](https://github.com/Azure-Samples/ms-identity-java-webapi)  | [Aynı site tanımlama bilgisi onarımı #4](https://github.com/Azure-Samples/ms-identity-java-webapi/pull/4)

---

## <a name="next-steps"></a>Sonraki adımlar

SameSite ve Web uygulaması senaryosu hakkında daha fazla bilgi edinin:

- [Google Chrome 'un SameSite hakkında SSS](https://www.chromium.org/updates/same-site/faq)

- [Kmıum SameSite sayfası](https://www.chromium.org/updates/same-site)

- [Senaryo: kullanıcılarda oturum açan Web uygulaması](scenario-web-app-sign-user-overview.md)