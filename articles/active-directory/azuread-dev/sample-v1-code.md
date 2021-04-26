---
title: Azure Active Directory v 1.0 için kod örnekleri | Microsoft Docs
description: Senaryo tarafından düzenlenen Azure Active Directory (v 1.0 uç noktası) kod örneklerinin bir dizinini sağlar.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: sample
ms.workload: identity
ms.date: 07/15/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 4d2b1a60856877c24d61379a688ea630e506a848
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106075245"
---
# <a name="azure-active-directory-code-samples-v10-endpoint"></a>Azure Active Directory kodu örnekleri (v 1.0 uç noktası)

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Web uygulamalarınıza ve Web API 'lerinize kimlik doğrulaması ve yetkilendirme eklemek için Microsoft Azure Active Directory (Azure AD) kullanabilirsiniz.

Bu bölüm, Azure AD v 1.0 uç noktası hakkında daha fazla bilgi edinmek için kullanabileceğiniz örneklere bağlantılar sağlar. Bu örnekler, uygulamalarınızda kullanabileceğiniz kod parçacıkları ile birlikte nasıl yapıldığını gösterir. Kod örneği sayfasında, gereksinimler, yükleme ve kurulum konusunda yardımcı olacak ayrıntılı okuma konuları bulacaksınız. Ve kod, önemli bölümleri anlamanıza yardımcı olacak şekilde açıklama eklenir.

> [!NOTE]
> Azure AD v2 kod örnekleri ile ilgileniyorsanız, [senaryoya göre v 2.0 kod örnekleri](../develop/sample-v2-code.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)bölümüne bakın.

Her örnek tür için temel senaryoyu anlamak üzere bkz. [Azure AD Için kimlik doğrulama senaryoları](v1-authentication-scenarios.md).

Ayrıca, GitHub 'daki örneklerimize katkıda bulunabilirsiniz. Nasıl yapılacağını öğrenmek için bkz. [Microsoft Azure Active Directory örnekleri ve belgeleri](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="single-page-applications"></a>Tek sayfalı uygulamalar

Bu örnek, Azure AD ile güvenli bir şekilde tek sayfalı uygulamanın nasıl yazılacağını gösterir.

| Platform | Kendi API 'sini çağırır | Başka bir Web API 'SI çağırır |
|--|--|--|
| ![Bu resimde JavaScript logosu gösterilir](media/sample-v2-code/logo-js.png) | [JavaScript-singlepageapp](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |
| ![Bu görüntüde angular JS logosu gösterilmektedir](media/sample-v2-code/logo-angular.png) | [AngularJS-singlepageapp](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) | [AngularJS-singlepageapp-CORS](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp-dotnet-webapi) |

## <a name="web-applications"></a>Web Uygulamaları

### <a name="web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity"></a>Kullanıcılara oturum açan Web uygulamaları, Microsoft Graph veya bir Web API 'sini kullanıcının kimliğiyle çağırma

Aşağıdaki örneklerde, kullanıcıları imzalayan Web uygulamaları gösterilmektedir. Bu uygulamalardan bazıları Ayrıca, oturum açmış kullanıcının adında Microsoft Graph veya kendi Web API 'nizi de çağırır.

| Platform | Yalnızca kullanıcılar oturum açar | Çağrılar Microsoft Graph | Başka bir ASP.NET veya ASP.NET Core 2,0 Web API 'sini çağırır |
|--|--|--|--|
| ![Bu görüntüde ASP.NET Core logosu gösterilmektedir](media/sample-v2-code/logo-netcore.png)</p>ASP.NET Core 2,0 | [DotNet-WebApp-openıdconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore) | [WEBAPP-WebApi-Multitenant-openıdconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-webapp-webapi-multitenant-openidconnect-aspnetcore/) </p>(AAD grafiği) | [DotNet-WebApp-WebApi-openıdconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-openidconnect-aspnetcore) |
| ![Bu görüntüde ASP.NET Framework logosu gösterilmektedir](media/sample-v2-code/logo-netframework.png)</p> ASP.NET 4.5 | </p> [WEBAPP-Wsfederatıon-dotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation) </p> [DotNet-WebApp-WebApi-OAuth2-UserIdentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-useridentity) | [DotNet-WebApp-Multitenant-openıdconnect](https://github.com/Azure-Samples/active-directory-dotnet-webapp-multitenant-openidconnect)</p> (AAD grafiği) |
| ![Bu görüntüde Python logosu gösterilmektedir](media/sample-v2-code/logo-python.png) |  | [Python-WebApp-graphapı](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi) |
| ![Bu görüntüde Java günlüğü gösterilmektedir](media/sample-v2-code/logo-java.png) |  | [Java-WebApp-openıdconnect](https://github.com/azure-samples/active-directory-java-webapp-openidconnect) |
| ![Bu görüntüde PHP logosu gösterilmektedir](media/sample-v2-code/logo-php.png) |  | [php-graphapı-Web](https://github.com/Azure-Samples/active-directory-php-graphapi-web) |

### <a name="web-applications-demonstrating-role-based-access-control-authorization"></a>Rol tabanlı erişim denetimi (yetkilendirme) gösteren Web uygulamaları

Aşağıdaki örnekler rol tabanlı erişim denetiminin (RBAC) nasıl uygulanacağını gösterir. RBAC, bir Web uygulamasındaki belirli özelliklerin izinlerini belirli kullanıcılara kısıtlamak için kullanılır. Kullanıcılar bir **Azure AD grubuna** ait olup olmadığınıza veya belirli bir uygulama **rolüne** sahip olmasına bağlı olarak yetkilendirilir.

| Platform | Örnek | Açıklama |
|--|--|--|
| ![Bu görüntüde ASP.NET Framework logosu gösterilmektedir](media/sample-v2-code/logo-netframework.png)</p> ASP.NET 4.5 | [DotNet-WebApp-groupclaim](https://github.com/Azure-Samples/active-directory-dotnet-webapp-groupclaims) </p>  [DotNet-WebApp-roleclaim](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) | Yetkilendirme için Azure AD **rolleri** kullanan bir .NET 4,5 MVC web uygulaması |

## <a name="desktop-and-mobile-public-client-applications-calling-microsoft-graph-or-a-web-api"></a>Microsoft Graph veya bir Web API 'sini çağıran masaüstü ve mobil ortak istemci uygulamaları

Aşağıdaki örneklerde, bir kullanıcı adına Microsoft Graph veya bir Web API 'sine erişen ortak istemci uygulamaları (masaüstü/mobil uygulamalar) gösterilmektedir. Uygulamalar, cihazlara ve platformlara bağlı olarak, kullanıcılar tarafından farklı yollarla oturum açabilirler (akışlar/izin verir):

- Biriyle
- Sessizce (Windows üzerinde tümleşik Windows kimlik doğrulaması veya Kullanıcı adı/parola ile)
- Etkileşimli oturum açmayı başka bir cihaza devrederden (Web denetimleri sağlamayan cihazlarda kullanılan cihaz kod akışı)

İstemci uygulaması | Platform | Flow/ver | Çağrılar Microsoft Graph | Bir ASP.NET veya ASP.NET Core 2. x Web API 'SI çağırır
------------------ | -------- | ---------- | -------------------- | -------------------------
Masaüstü (WPF)           | ![Bu görüntüde .NET/C# logosu gösterilmektedir](media/sample-v2-code/logo-net.png)  | Etkileşimli | [DotNet-Native-MultiTarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) 'ın parçası | [DotNet-Yerel Masaüstü](https://github.com/Azure-Samples/active-directory-dotnet-native-desktop) </p> [DotNet-yerel-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore/)</p> [DotNet-WebApi-Manual-JWT-doğrulama](https://github.com/azure-samples/active-directory-dotnet-webapi-manual-jwt-validation)
Mobil (UWP)            | .![Bu görüntüde .NET/C#/UWP gösterilmektedir](media/sample-v2-code/logo-windows.png)   | Etkileşimli | [DotNet-yerel-UWP-WAM](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) </p> Bu örnek, [adal.net](https://aka.ms/adalnet) değil, [WAM](/windows/uwp/security/web-account-manager)kullanır|  [DotNet-Windows-Store](https://github.com/Azure-Samples/active-directory-dotnet-windows-store) (tek kiracılı web API 'sini çağırmak için adal.NET kullanan UWP uygulaması) </p> [DotNet-WebApi-Multitenant-Windows-Store](https://github.com/Azure-Samples/active-directory-dotnet-webapi-multitenant-windows-store) (bir çok kiracılı web API 'sini çağırmak için adal.NET kullanan UWP uygulaması)|
Mobil (Android, iOS, UWP)   | ![Bu görüntüde .NET/C# (Xamarin) gösterilmektedir](media/sample-v2-code/logo-xamarin.png) | Etkileşimli | [DotNet-Native-MultiTarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) |
Mobil (Android)           | ![Bu resimde Android logosu gösterilir](media/sample-v2-code/logo-android.png) | Etkileşimli |   [Android](https://github.com/Azure-Samples/active-directory-android) |
Mobil (iOS)           | ![Bu görüntüde iOS/amaç C veya Swift gösterilmektedir](media/sample-v2-code/logo-ios.png) | Etkileşimli |   [nativeClient-iOS](https://github.com/azureadquickstarts/nativeclient-ios) |
Masaüstü (konsol)          | ![Bu görüntüde .NET/C# logosu gösterilmektedir](media/sample-v2-code/logo-net.png) | Kullanıcı adı/parola </p>  Tümleşik Windows Kimlik Doğrulaması | | [DotNet-yerel olarak gözetimsiz](https://github.com/azure-samples/active-directory-dotnet-native-headless)
Masaüstü (konsol)          | ![Bu görüntüde Java logosu gösterilir](media/sample-v2-code/logo-java.png) | Kullanıcı adı/parola | | [Java-Native-gözetimsiz](https://github.com/Azure-Samples/active-directory-java-native-headless)
Masaüstü (konsol)           | ![Bu görüntüde .NET Core/C# logosu gösterilmektedir](media/sample-v2-code/logo-netcore.png) | Cihaz kod akışı | | [DotNet-deviceprofile](https://github.com/Azure-Samples/active-directory-dotnet-deviceprofile)

## <a name="daemon-applications-accessing-web-apis-with-the-applications-identity"></a>Daemon uygulamaları (uygulamanın kimliğiyle Web API 'Lerine erişme)

Aşağıdaki örnekler, Microsoft Graph veya Kullanıcı olmayan (uygulama kimliği ile) bir Web API 'sine erişen masaüstü veya Web uygulamalarını gösterir.

İstemci uygulaması | Platform | Flow/ver | ASP.NET veya ASP.NET Core 2,0 Web API 'sini çağırır
------------------ | -------- | ---------- | -------------------- 
Daemon uygulaması (konsol)          | ![Bu görüntüde .NET Framework logosu gösterilmektedir](media/sample-v2-code/logo-netframework.png) | Uygulama gizli anahtarı veya sertifikasıyla istemci kimlik bilgileri | [DotNet-Daemon](https://github.com/azure-samples/active-directory-dotnet-daemon)</p> [DotNet-Daemon-sertifika-kimlik bilgileri](https://github.com/azure-samples/active-directory-dotnet-daemon-certificate-credential)
Daemon uygulaması (konsol)         | ![Bu görüntüde .NET Core logosu gösterilmektedir](media/sample-v2-code/logo-netcore.png) | Sertifika ile istemci kimlik bilgileri| [dotnetcore-Daemon-sertifika-kimlik bilgisi](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-certificate-credential)
ASP.NET Web uygulaması  | ![Bu görüntüde .NET Framework logosu gösterilmektedir](media/sample-v2-code/logo-netframework.png) | İstemci kimlik bilgileri | [DotNet-WebApp-WebApi-OAuth2-appıdentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-appidentity)

## <a name="web-apis"></a>Web API'leri

### <a name="web-api-protected-by-azure-active-directory"></a>Azure Active Directory tarafından korunan Web API 'SI

Aşağıdaki örnek, Azure AD ile node.js Web API 'sinin nasıl korunacağını göstermektedir.

Bu makalenin önceki bölümlerinde, bir ASP.NET veya ASP.NET Core **Web API 'sini** **çağıran** bir istemci uygulamasını gösteren diğer örnekleri de bulabilirsiniz. Bu örneklere bu bölümde bir daha bahsedilmez, ancak bunları yukarıdaki veya alttaki tabloların son sütununda bulabilirsiniz

| Platform | Örnek |
|--------|-------------------|
| ![Bu görüntüde Node.js logosu gösterilmektedir](media/sample-v2-code/logo-nodejs.png)  | [düğüm-WebApi](https://github.com/Azure-Samples/active-directory-node-webapi) |

### <a name="web-api-calling-microsoft-graph-or-another-web-api"></a>Microsoft Graph veya başka bir Web API çağıran Web API 'SI

Aşağıdaki örneklerde, başka bir Web API 'sini çağıran bir Web API 'SI gösterilmektedir. İkinci örnek, Koşullu erişimin nasıl işleneceğini gösterir.

| Platform |  Çağrılar Microsoft Graph | Başka bir ASP.NET veya ASP.NET Core 2,0 Web API 'sini çağırır |
| -------- |  --------------------- | ------------------------- |
| ![Bu görüntüde ASP.NET Framework logosu gösterilmektedir](media/sample-v2-code/logo-netframework.png)</p> ASP.NET 4.5 | [DotNet-WebApi-OnBehalfOf](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) </p> [DotNet-WebApi-OnBehalfOf-CA](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca) | [DotNet-WebApi-OnBehalfOf](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) </p> [DotNet-WebApi-OnBehalfOf-CA](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca) |

## <a name="other-microsoft-graph-samples"></a>Diğer Microsoft Graph örnekleri

Azure AD ile kimlik doğrulaması da dahil olmak üzere Microsoft Graph API 'SI için farklı kullanım desenleri gösteren örnekler ve öğreticiler için, bkz. [Microsoft Graph topluluk örnekleri & öğreticiler](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>Ayrıca bkz.

- [Geliştirici Kılavuzu Azure Active Directory](v1-overview.md)
- [Azure Active Directory kimlik doğrulama kitaplıkları](active-directory-authentication-libraries.md)
- [Microsoft Graph API kavramsal ve başvuru](/graph/use-the-api)
