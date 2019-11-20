---
title: Microsoft kimlik doğrulama kitaplığı (MSAL) hakkında bilgi edinin
titleSuffix: Microsoft identity platform
description: Microsoft kimlik doğrulama kitaplığı (MSAL), uygulama geliştiricilerinin güvenli Web API 'Lerini çağırmak için belirteçleri almasına olanak sağlar. Bu Web API 'Leri Microsoft Graph, diğer Microsoft API 'leri, üçüncü taraf Web API 'Leri veya kendi Web API 'niz olabilir. MSAL birden çok uygulama mimarilerini ve platformunu destekler.
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
ms.date: 10/30/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: dedd9f3458118117546a900aa1a2c8c5aa627100
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73473731"
---
# <a name="overview-of-microsoft-authentication-library-msal"></a>Microsoft kimlik doğrulama kitaplığı 'na genel bakış (MSAL)
Microsoft kimlik doğrulama kitaplığı (MSAL), geliştiricilerin güvenli Web API 'Lerine erişmek için Microsoft Identity platform uç noktasından [belirteç](developer-glossary.md#security-token) almasına olanak sağlar. Bu Web API 'Leri Microsoft Graph, diğer Microsoft API 'leri, üçüncü taraf Web API 'Leri veya kendi Web API 'niz olabilir. MSAL, birçok farklı uygulama mimarilerini ve platformunu destekleyen .NET, JavaScript, Android ve iOS için kullanılabilir.

MSAL, çeşitli platformlar için tutarlı bir API ile belirteç almanın birçok yolunu sağlar. MSAL kullanmak aşağıdaki avantajları sağlar:

* Uygulamanızdaki protokolde OAuth kitaplıklarını veya kodu doğrudan kullanmaya gerek yoktur.
* Belirteçleri bir kullanıcı adına veya bir uygulama adına (platforma uygun olduğunda) alır.
* Belirteç önbelleğini korur ve süreleri sona ermek üzereyken sizin için belirteçleri yeniler. Belirteç süre sonunu kendi kendinize uygulamanız gerekmez.
* Uygulamanızın hangi izleyiciyi istediğini (Kuruluşunuz, çeşitli org, iş ve okul ve Microsoft kişisel hesaplarınız, Azure AD B2C ile sosyal kimlikler, Sovereign ve ulusal bulutlar) için hangi izleyiciyi istediğinizi belirtmenize yardımcı olur.
* Uygulamanızı yapılandırma dosyalarından ayarlamanıza yardımcı olur.
* Eyleme dönüştürülebilir özel durumlar, günlüğe kaydetme ve telemetri sunarak uygulamanızın sorunlarını gidermenize yardımcı olur.

## <a name="application-types-and-scenarios"></a>Uygulama türleri ve senaryolar
MSAL kullanarak bir belirteç birçok uygulama türünden alınabilir: Web uygulamaları, Web API 'Leri, tek sayfalı uygulamalar (JavaScript), mobil ve yerel uygulamalar, Daemon 'ları ve sunucu tarafı uygulamalar.

MSAL, aşağıdakiler dahil birçok uygulama senaryosunda kullanılabilir:

* [Tek sayfalı uygulamalar (JavaScript)](scenario-spa-overview.md)
* [Kullanıcılara Web uygulaması oturumu açma](scenario-web-app-sign-user-overview.md)
* [Web uygulaması oturumu açma ve Kullanıcı adına Web API 'SI çağırma](scenario-web-app-call-api-overview.md)
* [Yalnızca kimliği doğrulanmış kullanıcıların erişebilmesi için Web API 'sini koruma](scenario-protected-web-api-overview.md)
* [Web API 'SI oturum açmış kullanıcı adına başka bir aşağı akış Web API 'SI çağırma](scenario-web-api-call-api-overview.md)
* [Oturum açmış kullanıcı adına bir Web API 'SI çağıran masaüstü uygulaması](scenario-desktop-overview.md)
* [Etkileşimli olarak oturum açan kullanıcı adına bir Web API 'si çağıran mobil uygulama](scenario-mobile-overview.md).
* [Web API 'sini kendi adına çağıran masaüstü/hizmet Daemon uygulaması](scenario-daemon-overview.md)

## <a name="languages-and-frameworks"></a>Diller ve çerçeveler

| Kitaplık | Desteklenen platformlar ve çerçeveler|
| --- | --- |
| [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)| .NET Framework, .NET Core, Xamarin Android, Xamarin iOS, Evrensel Windows Platformu|
| [MSAL. js](https://github.com/AzureAD/microsoft-authentication-library-for-js)| AngularJS, Ember. js veya Durandal. js gibi JavaScript/TypeScript çerçeveleri|
| [Android için MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-android)|Android|
| [İOS ve macOS için MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-objc)|iOS ve macOS|
| [MSAL Java (Önizleme)](https://github.com/AzureAD/microsoft-authentication-library-for-java)|Java|
| [MSAL Python (Önizleme)](https://github.com/AzureAD/microsoft-authentication-library-for-python)|Python|

## <a name="differences-between-adal-and-msal"></a>ADAL ve MSAL arasındaki farklar

Active Directory Authentication Library (ADAL), MSAL Microsoft Identity platform (v 2.0) uç noktasıyla tümleştirilebilen geliştiriciler için Azure AD (v 1.0) uç noktası ile tümleşir. V 1.0 uç noktası, kişisel hesapları değil, iş hesaplarını destekler. V 2.0 uç noktası, Microsoft kişisel hesaplarının ve iş hesaplarının tek bir kimlik doğrulama sisteminde bir birleşme noktasıdır. Ayrıca, MSAL ile Azure AD B2C için kimlik doğrulamaları da alabilirsiniz.

Daha ayrıntılı bilgi için [adal.net adresinden msal.net 'e geçme](msal-net-migration.md) ve [adal. js ' den msal. js ' ye](msal-compare-msal-js-and-adal-js.md)geçme hakkında bilgi edinin.
