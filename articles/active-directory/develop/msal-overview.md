---
title: MSAL hakkında bilgi edinin | Mavisi
titleSuffix: Microsoft identity platform
description: Microsoft kimlik doğrulama kitaplığı (MSAL), uygulama geliştiricilerinin güvenli Web API 'Lerini çağırmak için belirteçleri almasına olanak sağlar. Bu Web API 'Leri Microsoft Graph, diğer Microsoft API 'leri, üçüncü taraf Web API 'Leri veya kendi Web API 'niz olabilir. MSAL birden çok uygulama mimarilerini ve platformunu destekler.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 90e81e30e26846bf05032dddf1441e8428bc7158
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106550991"
---
# <a name="overview-of-the-microsoft-authentication-library-msal"></a>Microsoft kimlik doğrulama kitaplığı 'na genel bakış (MSAL)
Microsoft kimlik doğrulama kitaplığı (MSAL), geliştiricilerin kullanıcıların kimliğini doğrulamak ve güvenli Web API 'Lerine erişmek için Microsoft Identity platformundan [belirteçler](developer-glossary.md#security-token) almasına olanak sağlar. Microsoft Graph, diğer Microsoft API 'Leri, üçüncü taraf Web API 'Lerine veya kendi Web API 'nize güvenli erişim sağlamak için kullanılabilir. MSAL, .NET, JavaScript, Java, Python, Android ve iOS dahil birçok farklı uygulama mimarilerini ve platformunu destekler.

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
| [Android için MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-android)|Android|
| [MSAL angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angular)| Angular ve Angular.js çerçeveler içeren tek sayfalı uygulamalar|
| [iOS ve macOS için MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-objc)|iOS ve macOS|
| [MSAL Go (Önizleme)](https://github.com/AzureAD/microsoft-authentication-library-for-go)|Windows, macOS, Linux|
| [MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java)|Windows, macOS, Linux|
| [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)| Vue.js, Ember.js veya Durandal.js gibi JavaScript/TypeScript çerçeveleri|
| [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)| .NET Framework, .NET Core, Xamarin Android, Xamarin iOS, Evrensel Windows Platformu|
| [MSAL düğümü](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node)|Express ile Web Apps, elektron özellikli masaüstü uygulamaları, platformlar arası konsol uygulamaları|
| [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)|Windows, macOS, Linux|
| [MSAL tepki verme](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-react)| Tepki verme ve tepki tabanlı kitaplıklar içeren tek sayfalı uygulamalar (Next.js Gatsby.js)|

## <a name="differences-between-adal-and-msal"></a>ADAL ve MSAL arasındaki farklar

Active Directory Authentication Library (ADAL), MSAL Microsoft Identity platformu ile tümleştirilebilen geliştiriciler için Azure AD (v 1.0) uç noktası ile tümleşir. V 1.0 uç noktası, kişisel hesapları değil, iş hesaplarını destekler. V 2.0 uç noktası, Microsoft kişisel hesaplarının ve iş hesaplarının tek bir kimlik doğrulama sisteminde bir birleşme noktasıdır. Ayrıca, MSAL ile Azure AD B2C için kimlik doğrulamaları da alabilirsiniz.

Daha ayrıntılı bilgi için, [adal.net adresinden msal.net 'e geçme](msal-net-migration.md) ve [ADAL.jsMSAL.js geçiş ](msal-compare-msal-js-and-adal-js.md)hakkında bilgi edinin.
