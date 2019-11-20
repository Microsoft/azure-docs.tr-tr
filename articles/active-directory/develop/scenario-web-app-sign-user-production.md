---
title: Kullanıcıları oturum açan Web uygulaması (üretime geçme)-Microsoft Identity platform
description: Kullanıcılara oturum açan bir Web uygulaması oluşturmayı öğrenin (üretime geçin)
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
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd6717c132d32d54c16e7678695a09e35181a057
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71086543"
---
# <a name="web-app-that-signs-in-users---move-to-production"></a>Kullanıcılara oturum açan Web uygulaması-üretime geçme

Artık, Web API 'Lerini çağırmak için bir belirteç edinmeyi öğrenmiş olduğunuza göre, bunu üretime taşımayı öğrenin.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Sonraki adımlar

### <a name="calling-web-apis-scenario"></a>Web API 'Leri çağırma senaryosu

Web uygulamanız kullanıcılar oturum açtığında, oturum açmış kullanıcılar adına Web API 'Leri çağırabilir. Web API 'Lerinden Web API 'Leri çağırmak aşağıdaki senaryonun nesnesidir:

> [!div class="nextstepaction"]
> [Web API'lerini çağıran web uygulaması](scenario-web-app-call-api-overview.md)

### <a name="deep-dive---aspnet-core-web-app-tutorial"></a>Derin derinlemesine bakış-ASP.NET Core Web uygulaması öğreticisi

ASP.NET Core öğreticisi ile oturum açma kullanıcılarının diğer yolları hakkında bilgi edinin: [MS-Identity-aspnetcore-WebApp-öğreticisi](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial). Bu örnek, ' deki hesaplara oturum açma ekleme dahil olmak üzere, bir Web uygulaması için üretime hazırlı kod içeren aşamalı bir öğreticidir:

- Kuruluşunuz,
- birden çok kuruluş,
- iş veya okul hesapları ya da kişisel Microsoft hesabı,
- [Azure AD B2C](https://aka.ms/aadb2c),
- ya da ulusal bulutlarda.

### <a name="sample-code---java-web-app"></a>Örnek kod-Java Web uygulaması

GitHub 'daki örnekten Java Web uygulaması hakkında daha fazla bilgi edinin: [Kullanıcılara Microsoft Identity platformu ve çağrıları ile oturum açan bir Java Web uygulaması Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp)
