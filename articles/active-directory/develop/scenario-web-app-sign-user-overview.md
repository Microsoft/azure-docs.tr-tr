---
title: Web uygulamasından Kullanıcı oturum açma | Mavisi
titleSuffix: Microsoft identity platform
description: Kullanıcılara oturum açan bir Web uygulaması oluşturma hakkında bilgi edinin (genel bakış)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 0bbc799f946d318c305a96d9cb8c6831d9242ff6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104578303"
---
# <a name="scenario-web-app-that-signs-in-users"></a>Senaryo: kullanıcılarda oturum açan Web uygulaması

Kullanıcıların oturum açması için Microsoft Identity platformunu kullanan bir Web uygulaması oluşturmak için ihtiyacınız olan tüm kullanıcıları öğrenin.

## <a name="getting-started"></a>Kullanmaya başlama

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Kullanıcılar tarafından oturum açan ilk taşınabilir (ASP.NET Core) Web uygulamanızı oluşturmak istiyorsanız bu hızlı başlangıcı izleyin:

[Hızlı başlangıç: kullanıcılarda oturum açan Web uygulamasını ASP.NET Core](quickstart-v2-aspnet-core-webapp.md)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Mevcut bir ASP.NET Web uygulamasına oturum açma ekleme hakkında bilgi edinmek istiyorsanız, aşağıdaki hızlı başlangıcı deneyin:

[Hızlı başlangıç: kullanıcılar oturum açan ASP.NET Web uygulaması](quickstart-v2-aspnet-webapp.md)

# <a name="java"></a>[Java](#tab/java)

Java geliştiricisiyseniz aşağıdaki hızlı başlangıcı deneyin:

[Hızlı başlangıç: Microsoft 'a Java Web uygulamasına oturum açma ekleme](quickstart-v2-java-webapp.md)

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Node.js geliştirici iseniz aşağıdaki hızlı başlangıcı deneyin:

[Hızlı başlangıç: Microsoft 'a Node.js Web uygulamasına oturum açma ekleme](quickstart-v2-nodejs-webapp-msal.md)

# <a name="python"></a>[Python](#tab/python)

Python ile geliştirirseniz, aşağıdaki hızlı başlangıcı deneyin:

[Hızlı başlangıç: Microsoft 'a Python web uygulamasına oturum açma ekleme](quickstart-v2-python-webapp.md)

---

## <a name="overview"></a>Genel Bakış

Web uygulamanıza, kullanıcıların oturum açmasını sağlamak için kimlik doğrulaması eklersiniz. Kimlik doğrulaması eklemek, Web uygulamanızın, Kullanıcı deneyimini özelleştirmek için sınırlı profil bilgilerine erişmesini sağlar.

Web Apps bir kullanıcının kimliğini bir Web tarayıcısında doğrular. Bu senaryoda, Web uygulaması, kullanıcının tarayıcısını Azure Active Directory (Azure AD) içinde oturum açmak üzere yönlendirir. Azure AD, kullanıcının tarayıcısı aracılığıyla bir güvenlik belirtecinde Kullanıcı hakkında talepler içeren bir oturum açma yanıtı döndürür. Kullanıcıların oturum açması, ara yazılım [kitaplıkları](scenario-web-app-sign-user-app-configuration.md#microsoft-libraries-supporting-web-apps)kullanılarak Basitleştirilen [Açık kimlik Connect](./v2-protocols-oidc.md) standart protokolünden yararlanır.

![Kullanıcılarda Web uygulaması işaretleri](./media/scenario-webapp/scenario-webapp-signs-in-users.svg)

İkinci bir aşamada, uygulamanızı oturum açmış kullanıcı adına Web API 'Leri çağırabilmesini sağlayabilirsiniz. Bu sonraki aşama, Web [API 'lerini çağıran Web](scenario-web-app-call-api-overview.md)uygulamasında bulacağınız farklı bir senaryodur.

> [!NOTE]
> Web uygulamasına oturum açma ekleme, Web uygulamasını koruma ve  **Ara yazılım** kitaplıklarının yaptığı bir kullanıcı belirtecini doğrulama ile ilgilidir. .NET söz konusu olduğunda, bu senaryo, korumalı API 'Leri çağırmak için bir belirteç almak üzere Microsoft kimlik doğrulama kitaplığı 'nı (MSAL) henüz gerektirmez. Web uygulamasının Web API 'Lerini çağırması gerektiğinde, .NET için kimlik doğrulama kitaplıkları, izleme senaryosunda tanıtılacaktır.

## <a name="specifics"></a>Özelliklerini

- Uygulama kaydı sırasında, bir veya birkaç (uygulamanızı birkaç konuma dağıtırsanız) yanıt URI 'Lerini sağlamanız gerekir. Bazı durumlarda (ASP.NET ve ASP.NET Core), KIMLIK belirtecini etkinleştirmeniz gerekir. Son olarak, uygulamanızın kullanıcı oturumunu açmasını sağlayacak bir oturum açma URI 'SI ayarlamak isteyeceksiniz.
- Uygulamanızın kodunda, Web uygulamanızın temsilci olarak çalıştırılacağı yetkiyi sağlamanız gerekir. Belirteç doğrulamayı (özellikle de iş ortağı senaryolarında) özelleştirmek isteyebilirsiniz.
- Web uygulamaları tüm hesap türlerini destekler. Daha fazla bilgi için bkz. [Desteklenen hesap türleri](v2-supported-account-types.md).

## <a name="recommended-reading"></a>Önerilen okuma

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>Sonraki adımlar

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Bu senaryonun [uygulama kaydı](./scenario-web-app-sign-user-app-registration.md?tabs=aspnetcore)olan sonraki makaleye geçin.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Bu senaryonun [uygulama kaydı](./scenario-web-app-sign-user-app-registration.md?tabs=aspnet)olan sonraki makaleye geçin.

# <a name="java"></a>[Java](#tab/java)

Bu senaryonun [uygulama kaydı](./scenario-web-app-sign-user-app-registration.md?tabs=java)olan sonraki makaleye geçin.

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Bu senaryonun [uygulama kaydı](./scenario-web-app-sign-user-app-registration.md?tabs=nodejs)olan sonraki makaleye geçin.

# <a name="python"></a>[Python](#tab/python)

Bu senaryonun [uygulama kaydı](./scenario-web-app-sign-user-app-registration.md?tabs=python)olan sonraki makaleye geçin.

---
