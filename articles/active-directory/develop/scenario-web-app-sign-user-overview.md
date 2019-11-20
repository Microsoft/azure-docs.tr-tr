---
title: Kullanıcılara oturum açan Web uygulaması (genel bakış)-Microsoft Identity platform
description: Kullanıcılara oturum açan bir Web uygulaması oluşturma hakkında bilgi edinin (genel bakış)
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
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 68d47d4233aec62ec5f1955e52025b0d55221af8
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/18/2019
ms.locfileid: "72596710"
---
# <a name="scenario-web-app-that-signs-in-users"></a>Senaryo: kullanıcılarda oturum açan Web uygulaması

Kullanıcıları Microsoft Identity platformu ile oturum açan bir Web uygulaması oluşturmak için ihtiyacınız olan her şey hakkında bilgi edinin.

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Başlangıç

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Kullanıcıların oturum açmasını sağlayan ilk taşınabilir (ASP.NET Core) Web uygulamalarınızı oluşturmak istiyorsanız bu hızlı başlangıcı izleyin:

> [!div class="nextstepaction"]
> [Hızlı başlangıç: kullanıcıları oturum açan ASP.NET Core Web uygulaması](quickstart-v2-aspnet-core-webapp.md)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

f eski bir ASP.NET Web uygulamasına nasıl oturum ekleneceğini anlamak için aşağıdaki öğreticiyi deneyin:

> [!div class="nextstepaction"]
> [Hızlı başlangıç: kullanıcıları oturum açan ASP.NET Web uygulaması](quickstart-v2-aspnet-webapp.md)

# <a name="javatabjava"></a>[Java](#tab/java)

Java geliştiricisiyseniz aşağıdaki hızlı başlangıcı deneyin:

> [!div class="nextstepaction"]
> [Hızlı başlangıç: Microsoft 'a Java Web uygulamasına oturum açma ekleme](quickstart-v2-java-webapp.md)

# <a name="pythontabpython"></a>[Python](#tab/python)

Python ile geliştirirseniz, deneyin:

> [!div class="nextstepaction"]
> [Hızlı başlangıç: Microsoft 'a Python web uygulamasına oturum açma ekleme](quickstart-v2-python-webapp.md)

---

## <a name="overview"></a>Genel Bakış

Web uygulamanıza kimlik doğrulaması ekleyerek kullanıcıların oturum açmasını sağlayabilirsiniz. Kimlik doğrulaması eklemek, Web uygulamanızın sınırlı profil bilgilerine erişmesini sağlar ve örneğin kullanıcılarına sunduğunuz deneyimi özelleştirmek için. Web Apps bir kullanıcının kimliğini bir Web tarayıcısında doğrular. Bu senaryoda Web uygulaması, kullanıcının tarayıcısını Azure AD 'de oturum açmak üzere yönlendirir. Azure AD, kullanıcının tarayıcısı aracılığıyla bir güvenlik belirtecinde Kullanıcı hakkında talepler içeren bir oturum açma yanıtı döndürür. Oturum açma kullanıcıları, ara yazılım [kitaplıklarının](scenario-web-app-sign-user-app-configuration.md#libraries-used-to-protect-web-apps)kullanımı Ile BASITLEŞTIRILMIŞ [Açık kimlik Connect](./v2-protocols-oidc.md) standart protokolünü kullanır.

![Web uygulaması oturum açan kullanıcılar](./media/scenario-webapp/scenario-webapp-signs-in-users.svg)

İkinci bir aşamada, uygulamanızı oturum açmış kullanıcı adına Web API 'Leri çağırmak için de etkinleştirebilirsiniz. Bu sonraki aşama, Web [uygulaması çağrılarında Web API 'lerinde](scenario-web-app-call-api-overview.md) bulacağınız farklı bir senaryodur.

> [!NOTE]
> Web uygulamasına oturum açma ekleme, Web uygulamasını koruma ve **Ara yazılım** kitaplıklarının yaptığı bir kullanıcı belirtecini doğrulama ile ilgilidir. .NET durumunda bu senaryo, korumalı API 'Leri çağırmak için bir belirteç almak üzere, henüz Microsoft kimlik doğrulama kitaplıklarını (MSAL) gerektirmez. Kimlik doğrulama kitaplıkları yalnızca Web uygulamasının Web API 'Lerini çağırması gerektiğinde izleme senaryosunda tanıtılacaktır.

## <a name="specifics"></a>Özelliklerini

- Uygulama kaydı sırasında, bir veya birkaç tane (uygulamanızı birkaç konuma dağıtırsanız) yanıt URI 'Lerini sağlamanız gerekir. Bazı durumlarda (ASP.NET/ASP.NET Core), KIMLIK belirtecini etkinleştirmeniz gerekir. Son olarak, uygulamanızın kullanıcı oturumu açmasını yeniden yapabilmesi için bir oturum açma URI 'SI ayarlamak isteyeceksiniz.
- Uygulamanızın kodunda, Web uygulamasının temsilci olarak oturum açma yetkisini sağlamanız gerekir. Belirteç doğrulamasını özelleştirmek isteyebilirsiniz (özellikle ISV senaryolarında).
- Web uygulamaları tüm hesap türlerini destekler. Daha fazla bilgi için bkz. [Desteklenen hesap türleri](v2-supported-account-types.md).

## <a name="next-steps"></a>Sonraki adımlar

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [Uygulama kaydı](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=aspnetcore?tabs=aspnetcore)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [Uygulama kaydı](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=aspnet)

# <a name="javatabjava"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Uygulama kaydı](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=java)

# <a name="pythontabpython"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Uygulama kaydı](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=python)

---
