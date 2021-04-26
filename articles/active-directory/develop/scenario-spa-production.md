---
title: Tek sayfalı uygulamayı üretime taşıyın
titleSuffix: Microsoft identity platform
description: Tek sayfalı bir uygulama oluşturmayı öğrenin (üretime geçin)
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 9656da8be086724482f129efab323e02b73e117e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98954978"
---
# <a name="single-page-application-move-to-production"></a>Tek sayfalı uygulama: üretime taşı

Artık, Web API 'Lerini çağırmak için bir belirteç edinmeyi öğrenmiş olduğunuza göre, uygulamanızı üretime taşırken göz önünde bulundurmanız gereken bazı şeyler aşağıda verilmiştir.

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="deploy-your-app"></a>Uygulamanızı dağıtın

SPA ve Web API projelerinizi, sırasıyla Azure Storage ve Azure Uygulama Hizmetleri ile dağıtmayı öğrenmek için bir [dağıtım örneğine](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnet-webapi-multitenant/tree/master/Chapter3) göz atın. 

## <a name="code-samples"></a>Kod örnekleri

Bu kod örnekleri, tek sayfalı bir uygulama için birkaç temel işlemi gösterir.
- [ASP.net arka ucu olan Spa](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnetcore-webapi): **MSAL.js** kullanarak kendı arka uç web API 'niz (ASP.NET Core) için belirteçler alma.

- [Node.js Web API 'si (Azure AD](https://github.com/Azure-Samples/active-directory-javascript-nodejs-webapi-v2): **Passport-Azure-AD** kullanarak arka uç Web API 'niz için erişim belirteçlerini doğrulama (Node.js).

- [Azure AD B2C Ile Spa](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp): **Azure Active Directory B2C** (Azure AD B2C) ile kaydedilmiş bir uygulamadaki kullanıcılara oturum açmak için **MSAL.js** kullanma.

- [Node.js Web API 'si (Azure AD B2C)](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi): **Azure Active Directory B2C** (Azure AD B2C) ile kaydedilen uygulamalar için erişim belirteçlerini doğrulamak üzere **Passport-Azure-AD** ' i kullanma.

## <a name="next-steps"></a>Sonraki adımlar

- [JAVASCRIPT Spa öğreticisi](./tutorial-v2-javascript-spa.md): kullanıcıların oturum açma ve **MSAL.js** kullanarak **Microsoft Graph API** 'sini çağırmak için bir erişim belirteci alma hakkında ayrıntılı bilgi.