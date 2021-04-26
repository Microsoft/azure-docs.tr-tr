---
title: Microsoft Identity platform için kod örnekleri
description: Senaryoya göre düzenlenmiş, kullanılabilir Microsoft Identity platform kod örneklerinin bir dizinini sağlar.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: sample
ms.workload: identity
ms.date: 11/04/2020
ms.author: marsma
ms.reviewer: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: a19ca14a37bd24e499fdf0681b3510e9a4a8ea3f
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106075041"
---
# <a name="microsoft-identity-platform-code-samples-v20-endpoint"></a>Microsoft Identity platform kodu örnekleri (v 2.0 uç noktası)

Microsoft Identity platformunu kullanarak şunları yapabilirsiniz:

- Web uygulamalarınıza ve Web API 'Lerine kimlik doğrulaması ve yetkilendirme ekleyin.
- Korumalı bir Web API 'sine erişmek için erişim belirteci gerektir.

Bu makalede kısaca Microsoft Identity platformu örneklerine yönelik bağlantılar sunulmaktadır. Bu örnekler, bunun nasıl yapıldığını gösterir ve uygulamalarınızda kullanabileceğiniz kod parçacıklarını da sağlar. Kod örneği sayfasında gereksinimler, yükleme ve Kuruluma yardımcı olacak ayrıntılı Benioku konuları bulacaksınız. Kod içindeki açıklamalar kritik bölümleri anlamanıza yardımcı olur.

Her örnek tür için temel senaryoyu anlamak üzere bkz. [Microsoft Identity platform Için uygulama türleri](v2-app-types.md).

GitHub 'daki örneklere da katkıda bulunabilirsiniz. Nasıl yapılacağını öğrenmek için bkz. [Microsoft Azure Active Directory örnekleri ve belgeleri](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="single-page-applications"></a>Tek sayfalı uygulamalar

Bu örnekler, Microsoft Identity platform ile güvenliği sağlanmış tek sayfalı bir uygulamanın nasıl yazılacağını gösterir. Bu örnekler MSAL.js sahip olan türleri kullanır.

| Platform | Description | Bağlantı |
| -------- | --------------------- | -------- |
| ![Bu resimde JavaScript logosu ](media/sample-v2-code/logo_js.png) [javascript (MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) gösterilmektedir | SPA çağrıları Microsoft Graph |[JavaScript-graphapı-v2](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2) |
| ![Bu resimde JavaScript logosu ](media/sample-v2-code/logo_js.png) [javascript (MSAL.js 2,0)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser) gösterilmektedir | SPA, kimlik doğrulama kod akışı kullanan Microsoft Graph çağırır/PKI CE |[JavaScript-v2](https://github.com/Azure-Samples/ms-identity-javascript-v2) |
| ![Bu resimde JavaScript logosu ](media/sample-v2-code/logo_js.png) [javascript (MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) gösterilmektedir | SPA çağrıları B2C |[B2C-JavaScript-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp) |
| ![Bu resimde JavaScript logosu ](media/sample-v2-code/logo_js.png) [javascript (MSAL.js 2,0)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser) gösterilmektedir | SPA, kimlik doğrulama kod akışı kullanarak B2C 'yi çağırır |[B2C-JavaScript-Spa](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa) |
| ![Bu resimde JavaScript logosu ](media/sample-v2-code/logo_js.png) [javascript (MSAL.js 2,0)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser) gösterilmektedir | SPA, sırasıyla çağrı yaptığı özel Web API 'sini çağırır Microsoft Graph  | [MS-Identity-JavaScript-öğretici-chapter4-OBO](https://github.com/Azure-Samples/ms-identity-javascript-tutorial/tree/main/4-AdvancedGrants/1-call-api-graph) |
| ![Bu resimde angular logosu ](media/sample-v2-code/logo_angular.png) [(msal angular)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) gösterilmektedir| SPA çağrıları Microsoft Graph  | [Active-Directory-JavaScript-singlepageapp-angular](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular) |
| ![Bu resimde angular logosu ](media/sample-v2-code/logo_angular.png) [(msal angular 2,0)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) gösterilmektedir| SPA, kimlik doğrulama kod akışı kullanan Microsoft Graph çağırır/PKI CE | [MS-Identity-JavaScript-angular-Spa](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa) |
| ![Bu resimde angular logosu ](media/sample-v2-code/logo_angular.png) [(msal angular 2,0)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) gösterilmektedir| SPA özel Web API 'SI çağırır | [MS-Identity-JavaScript-angular-Spa-aspnetcore-WebApi](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnetcore-webapi) |
| ![Bu resimde angular logosu ](media/sample-v2-code/logo_angular.png) [(msal angular)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) gösterilmektedir | SPA çağrıları B2C |[Active-Directory-B2C-JavaScript-angular-Spa](https://github.com/Azure-Samples/active-directory-b2c-javascript-angular-spa) |
| ![Bu resimde angular logosu ](media/sample-v2-code/logo_angular.png) [(msal angular 2,0)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) gösterilmektedir | SPA, uygulama rolleri ve güvenlik grupları ile özel Web API 'SI çağırır |[MS-Identity-JavaScript-angular-Spa-dotnetcore-WebApi-Roles-Groups](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-dotnetcore-webapi-roles-groups) |
| ![Bu görüntüde yanıt verme logosunun yanıt verme ](media/sample-v2-code/logo_react.png) [(msal tepki verme)](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-react) gösterilmektedir| SPA, kimlik doğrulama kod akışı kullanan Microsoft Graph çağırır/PKI CE | [MS-Identity-JavaScript-tepki-Spa](https://github.com/Azure-Samples/ms-identity-javascript-react-spa) |
| ![Bu görüntüde yanıt verme logosunun yanıt verme ](media/sample-v2-code/logo_react.png) [(msal tepki verme)](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-react) gösterilmektedir| SPA özel Web API 'SI çağırır | [MS-Identity-JavaScript-tepki verme-öğretici](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/3-Authorization-II/1-call-api) |
| ![Bu görüntüde tepki verme logosu yanıt verir ](media/sample-v2-code/logo_react.png) [(MSAL.js 2,0)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core)| SPA, sırasıyla çağrı yaptığı özel Web API 'sini çağırır Microsoft Graph  | [MS-Identity-JavaScript-tepki-Spa-dotnetcore-WebApi-OBO](https://github.com/Azure-Samples/ms-identity-javascript-react-spa-dotnetcore-webapi-obo) |
| ![Bu görüntüde Blazor logosu ](media/sample-v2-code/logo-blazor.png) [Blazor WebAssembly (MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser) gösterilmektedir | Kullanıcı oturum açma ve API 'Leri Azure Active Directory ile çağırmak için Blazor WebAssembly öğreticisi |[MS-Identity-blazor-baym](https://github.com/Azure-Samples/ms-identity-blazor-wasm) |

## <a name="web-applications"></a>Web uygulamaları

Aşağıdaki örneklerde, kullanıcıların oturum açmasını sağlayan Web uygulamaları gösterilmektedir. Bazı örnekler ayrıca Microsoft Graph çağıran uygulamayı ya da kullanıcının kimliğiyle kendi Web API 'nizi gösterir.

| Platform | Yalnızca kullanıcılar oturum açar | Kullanıcılar ve çağrılar Microsoft Graph imzalar |
| -------- | ------------------- | --------------------------------- |
| ![Bu görüntüde ASP.NET Core logosu gösterilmektedir](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core | [ASP.NET Core WebApp oturum açan kullanıcılar öğreticisi](https://aka.ms/aspnetcore-webapp-sign-in) | [ASP.NET Core Web uygulaması çağrılarındaki](https://aka.ms/aspnetcore-webapp-call-msgraph) aynı örnek Microsoft Graph aşaması</p>[Arka plan uygulamaları, API 'ler ve hizmetlerden gelen, oturum açmış kullanıcının belirteç önbelleğine erişen](https://github.com/Azure-Samples/ms-identity-dotnet-advanced-token-cache) gelişmiş örnek |
| ![Bu görüntüde ASP.NET Framework logosu gösterilmektedir](media/sample-v2-code/logo_NETframework.png)</p>ASP.NET Core | Geliştiricilerin Active Directory Federasyon Hizmetleri (AD FS) (AD FS) ile tümleştirilmiş uygulamalarınızı Azure Active Directory (Azure AD) ile güvenli ve güvenli bir şekilde nasıl geçirebileceğinizi öğrenmek için [Azure AD uygulama geçişi PlayBook 'a AD FS](https://github.com/Azure-Samples/ms-identity-dotnet-adfs-to-aad) | |
| ![Bu görüntüde ASP.NET Framework logosu gösterilmektedir](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET | [ASP.NET hızlı başlangıç](https://github.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) </p> [DotNet-WebApp-openıdconnect-v2](https://github.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2)  |  [DotNet-Yönetici-kısıtlı-kapsamlar-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) </p> [MSGraph-eğitim-aspnetmvcapp](https://github.com/microsoftgraph/msgraph-training-aspnetmvcapp) |
| ![Bu görüntüde Java logosu gösterilir](media/sample-v2-code/logo_java.png)  |[Java Servlet öğreticisi-bölüm 1,1](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/blob/main/README.md) AAD ile oturum açma| |
| ![Bu görüntüde Java logosu gösterilir](media/sample-v2-code/logo_java.png)  |[Java Servlet öğreticisi-bölüm 1,2](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/blob/main/README.md) B2C ile oturum açın |
| ![Bu görüntüde Java logosu gösterilir](media/sample-v2-code/logo_java.png)  | | [Java Servlet öğreticisi-bölüm 2,1](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/blob/main/README.md) AAD ile oturum açma ve çağrı grafı|
| ![Bu görüntüde Java logosu gösterilir](media/sample-v2-code/logo_java.png)  |[Java Servlet öğreticisi-bölüm 3,1](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/blob/main/README.md) AAD ile oturum açma ve rol talebine erişimi denetleme| |
| ![Bu görüntüde Java logosu gösterilir](media/sample-v2-code/logo_java.png)  | | [Java Servlet öğreticisi-bölüm 3,2](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/blob/main/README.md) AAD ile oturum açma ve grup talebine erişimi denetleme|
| ![Bu görüntüde Java logosu gösterilir](media/sample-v2-code/logo_java.png) | |[Java Servlet öğreticisi-bölüm 4,1](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/blob/main/README.md) Azure App Service dağıtma|
| ![Bu görüntüde Java logosu gösterilir](media/sample-v2-code/logo_java.png)  | | [MS-Identity-Java-WebApp](https://github.com/Azure-Samples/ms-identity-java-webapp) |
| ![Bu görüntüde Java logosu gösterilir](media/sample-v2-code/logo_java.png)  | [MS-Identity-B2C-Java-Servlet-WebApp-Authentication](https://github.com/Azure-Samples/ms-identity-b2c-java-servlet-webapp-authentication)|  |
| ![Bu görüntüde Node.js logosu gösterilmektedir](media/sample-v2-code/logo_nodejs.png)</p>Node.js (MSAL node) | [Express web uygulaması oturum açan kullanıcılar öğreticisi](https://github.com/Azure-Samples/ms-identity-node) | |
| ![Bu görüntüde Python logosu gösterilmektedir](media/sample-v2-code/logo_python.png)  | [Python Flask öğreticisi-bölüm 1,1](https://github.com/Azure-Samples/ms-identity-python-flask-tutorial/blob/main/README.md) AAD ile oturum açma  |  |
| ![Bu görüntüde Python logosu gösterilmektedir](media/sample-v2-code/logo_python.png)  | [Python Flask öğreticisi-bölüm 1,2](https://github.com/Azure-Samples/ms-identity-python-flask-tutorial/blob/main/README.md) B2C ile oturum açın                    |  |
| ![Bu görüntüde Python logosu gösterilmektedir](media/sample-v2-code/logo_python.png)  | | [Python Flask öğreticisi-bölüm 2,1](https://github.com/Azure-Samples/ms-identity-python-flask-tutorial/blob/main/README.md) AAD ile oturum açma ve çağrı grafı |
| ![Bu görüntüde Python logosu gösterilmektedir](media/sample-v2-code/logo_python.png)  | |[Python Flask öğreticisi-bölüm 3,1](https://github.com/Azure-Samples/ms-identity-python-flask-tutorial/blob/main/README.md) Azure App Service dağıtma  |
| ![Bu görüntüde Python logosu gösterilmektedir](media/sample-v2-code/logo_python.png)  | [Python Docgo öğreticisi-bölüm 1,1](https://github.com/Azure-Samples/ms-identity-python-django-tutorial/blob/main/README.md)   AAD ile oturum açma  | |
| ![Bu görüntüde Python logosu gösterilmektedir](media/sample-v2-code/logo_python.png)  | [Python Docgo öğreticisi-bölüm 1,2](https://github.com/Azure-Samples/ms-identity-python-django-tutorial/blob/main/README.md) B2C ile oturum açın                    |  |
| ![Bu görüntüde Python logosu gösterilmektedir](media/sample-v2-code/logo_python.png)  | | [Python Docgo öğreticisi-bölüm 2,1](https://github.com/Azure-Samples/ms-identity-python-django-tutorial/blob/main/README.md)  AAD ile oturum açma ve çağrı grafı|
| ![Bu görüntüde Python logosu gösterilmektedir](media/sample-v2-code/logo_python.png)  | | [Python Docgo öğreticisi-bölüm 3,1](https://github.com/Azure-Samples/ms-identity-python-django-tutorial/blob/main/README.md) Azure App Service dağıtma                    |
| ![Bu görüntüde Python logosu gösterilmektedir](media/sample-v2-code/logo_python.png)  | | [Python Flask web uygulaması](https://github.com/Azure-Samples/ms-identity-python-webapp) |
| ![Bu görüntüde Ruby logosu gösterilmektedir](media/sample-v2-code/logo_ruby.png) |                   | [MSGraph-eğitim-rubyrailsapp](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp) |
| ![Bu görüntüde Blazor logosu gösterilmektedir](media/sample-v2-code/logo-blazor.png)</p>Blazor Server | [Blazor sunucusu uygulama oturum açması-kullanıcılar öğreticisi](https://github.com/Azure-Samples/ms-identity-blazor-server/tree/main/WebApp-OIDC) | [Blazor sunucusu uygulama çağrıları Microsoft Graph](https://github.com/Azure-Samples/ms-identity-blazor-server/tree/main/WebApp-graph-user/Call-MSGraph)</p>Bölümterwise öğreticisi: [Azure Active Directory kullanıcılara oturum açmak ve API 'leri çağırmak Için Blazor Server uygulaması](https://github.com/Azure-Samples/ms-identity-blazor-server) |

## <a name="desktop-and-mobile-public-client-apps"></a>Masaüstü ve mobil ortak istemci uygulamaları

Aşağıdaki örneklerde, Microsoft Graph API 'sine veya bir kullanıcı adına kendi Web API 'sine erişen ortak istemci uygulamaları (masaüstü veya mobil uygulamalar) gösterilmektedir. *Masaüstü (konsol) Ile WAM* örneği dışında, tüm bu Istemci uygulamaları Microsoft kimlik doğrulama kitaplığı 'Nı (msal) kullanır.

| İstemci uygulaması | Platform | Flow/ver | Çağrılar Microsoft Graph | ASP.NET Core Web API 'sini çağırır |
| ------------------ | -------- |  ----------| ---------- | ------------------------- |
| Masaüstü öğreticisi (.NET Core)-Isteğe bağlı olarak:</p>-platformlar arası belirteç önbelleği</p>-Özel Web Kullanıcı arabirimi | ![Bu görüntüde .NET/C# logosu gösterilmektedir](media/sample-v2-code/logo_NETcore.png) | [Yetkilendirme kodu](msal-authentication-flows.md#authorization-code)| [MS-Identity-DotNet-Desktop-öğretici](https://github.com/azure-samples/ms-identity-dotnet-desktop-tutorial) | |
| Masaüstü (WPF)      | ![Bu görüntüde .NET Desktop/C# logosu gösterilmektedir](media/sample-v2-code/logo_NET.png) | [Yetkilendirme kodu](msal-authentication-flows.md#authorization-code)| [DotNet-masaüstü-MSGraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | [DotNet-Native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi) |
| Masaüstü (konsol)   | ![.NET/C# (Masaüstü) logosunu gösteren resim](media/sample-v2-code/logo_NET.png) | [Tümleşik Windows Kimlik Doğrulaması](msal-authentication-flows.md#integrated-windows-authentication) | [DotNet-IWA-v2](https://github.com/azure-samples/active-directory-dotnet-iwa-v2) |  |
| Masaüstü (konsol)   | ![Bu görüntüde Java logosu gösterilir](media/sample-v2-code/logo_java.png) | [Tümleşik Windows Kimlik Doğrulaması](msal-authentication-flows.md#integrated-windows-authentication) |[MS-Identity-Java-masaüstü](https://github.com/Azure-Samples/ms-identity-java-desktop/) |  |
| Masaüstü (konsol)   | ![Bu .NET/C# (Masaüstü) ambledir](media/sample-v2-code/logo_NETcore.png) | [Kullanıcı Adı/Parola](msal-authentication-flows.md#usernamepassword) |[dotnetcore-up-v2](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2) |  |
| WAM ile masaüstü (konsol)  | ![Bu, .NET/C# logosu (Masaüstü)](media/sample-v2-code/logo_NETcore.png) | [Web hesabı Yöneticisi](/windows/uwp/security/web-account-manager) (WAM) ile etkileşimli |[DotNet-yerel-UWP-WAM](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) |  |
| Masaüstü (konsol)   | ![Bu görüntüde Java logosu gösterilir](media/sample-v2-code/logo_java.png) | [Kullanıcı Adı/Parola](msal-authentication-flows.md#usernamepassword) |[MS-Identity-Java-masaüstü](https://github.com/Azure-Samples/ms-identity-java-desktop/) |  |
| Masaüstü (konsol)   | ![Bu görüntüde Python logosu gösterilmektedir](media/sample-v2-code/logo_python.png) | [Kullanıcı Adı/Parola](msal-authentication-flows.md#usernamepassword) |[MS-Identity-Python-Masaüstü](https://github.com/Azure-Samples/ms-identity-python-desktop) |  |
| Masaüstü (elektron)   | ![Bu görüntüde Node.js logosu gösterilmektedir](media/sample-v2-code/logo_nodejs.png)</p>Node.js (MSAL node) | [Yetkilendirme kodu (PKCE)](msal-authentication-flows.md#authorization-code) |[MS-Identity-JavaScript-NodeJS-Masaüstü](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-desktop) |  |
| Mobil (Android, iOS, UWP)   | ![Bu görüntüde .NET/C# (Xamarin) logosu gösterilir](media/sample-v2-code/logo_xamarin.png) | [Yetkilendirme kodu](msal-authentication-flows.md#authorization-code) |[Xamarin-Native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) |  |
| Mobil (iOS)       | ![Bu görüntüde iOS/amaç-C veya Swift gösterilmektedir](media/sample-v2-code/logo_iOS.png) | [Yetkilendirme kodu](msal-authentication-flows.md#authorization-code) |[iOS-Swift-ObjC-Native-v2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) </p> [iOS-Native-nxoauth2-v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) |  |
| Masaüstü (macOS)       | Mac OS | [Yetkilendirme kodu](msal-authentication-flows.md#authorization-code) |[macOS-Swift-ObjC-Native-v2](https://github.com/Azure-Samples/ms-identity-macOS-swift-objc) |  |
| Mobil (Android-Java)   | ![Bu resimde Android logosu gösterilir](media/sample-v2-code/logo_Android.png) | [Yetkilendirme kodu](msal-authentication-flows.md#authorization-code) |  [Android-Java](https://github.com/Azure-Samples/ms-identity-android-java) |  |
| Mobil (Android-Kotlin)   | ![Bu resimde Android logosu gösterilir](media/sample-v2-code/logo_Android.png) | [Yetkilendirme kodu](msal-authentication-flows.md#authorization-code) |  [Android-Kotlin](https://github.com/Azure-Samples/ms-identity-android-kotlin) |  |

## <a name="daemon-applications"></a>Daemon uygulamaları

Aşağıdaki örneklerde, Microsoft Graph API 'sine kendi kimliğiyle (Kullanıcı olmadan) erişen bir uygulama gösterilmektedir.

| İstemci uygulaması | Platform | Flow/ver | Çağrılar Microsoft Graph |
| ------------------ | -------- | ---------- | -------------------- |
| Konsol | ![Bu görüntüde .NET Core logosu gösterilmektedir](media/sample-v2-code/logo_NETcore.png)</p> ASP.NET  | [İstemci kimlik bilgileri](msal-authentication-flows.md#client-credentials) | [dotnetcore-Daemon-v2](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2) |
| Web uygulaması | ![ASP.NET logosunu gösteren ekran görüntüsü.](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET  | [İstemci kimlik bilgileri](msal-authentication-flows.md#client-credentials) | [DotNet-Daemon-v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2) |
| Konsol | ![Bu görüntüde Java logosu gösterilir](media/sample-v2-code/logo_java.png) | [İstemci kimlik bilgileri](msal-authentication-flows.md#client-credentials) | [MS-Identity-Java-Daemon](https://github.com/Azure-Samples/ms-identity-java-daemon) |
| Konsol | ![Bu görüntüde Node.js logosu gösterilmektedir](media/sample-v2-code/logo_nodejs.png)</p>Node.js (MSAL node)| [İstemci kimlik bilgileri](msal-authentication-flows.md#client-credentials) | [MS-Identity-JavaScript-NodeJS-konsol](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-console) |
| Konsol | ![Bu görüntüde Python logosu gösterilmektedir](media/sample-v2-code/logo_python.png) | [İstemci kimlik bilgileri](msal-authentication-flows.md#client-credentials) | [MS-Identity-Python-Daemon](https://github.com/Azure-Samples/ms-identity-python-daemon) |

## <a name="headless-applications"></a>Gözetimsiz uygulamalar

Aşağıdaki örnek, bir Web tarayıcısı olmadan cihazda çalışan ortak bir istemci uygulamasını gösterir. Uygulama, bir komut satırı aracı, Linux veya Mac üzerinde çalışan bir uygulama ya da bir IoT uygulaması olabilir. Örnek, başka bir cihazda (cep telefonu gibi) etkileşimli oturum açan bir kullanıcının adında Microsoft Graph API 'sine erişen bir uygulama sunar. Bu istemci uygulaması Microsoft kimlik doğrulama kitaplığı 'nı (MSAL) kullanır.

| İstemci uygulaması | Platform | Flow/ver | Çağrılar Microsoft Graph |
| ------------------ | -------- |  ----------| ---------- |
| Masaüstü (konsol)   | ![Bu görüntüde .NET/C# (Masaüstü) logosu gösterilir](media/sample-v2-code/logo_NETcore.png) | [Cihaz kod akışı](msal-authentication-flows.md#device-code) |[dotnetcore-devicecodeflow-v2](https://github.com/azure-samples/active-directory-dotnetcore-devicecodeflow-v2) |
| Masaüstü (konsol)   | ![Bu görüntüde Java logosu gösterilir](media/sample-v2-code/logo_java.png) | [Cihaz kod akışı](msal-authentication-flows.md#device-code) |[MS-Identity-Java-devicecodeflow](https://github.com/Azure-Samples/ms-identity-java-devicecodeflow) |
| Masaüstü (konsol)   | ![Bu görüntüde Python logosu gösterilmektedir](media/sample-v2-code/logo_python.png) | [Cihaz kod akışı](msal-authentication-flows.md#device-code) |[MS-Identity-Python-devicecodeflow](https://github.com/Azure-Samples/ms-identity-python-devicecodeflow) |

## <a name="multi-tenant-saas-applications"></a>Çok kiracılı SaaS uygulamaları

Aşağıdaki örneklerde, uygulamanızın herhangi bir Azure Active Directory (Azure AD) kiracısından oturum açma işlemlerini kabul etmek üzere nasıl yapılandırılacağı gösterilmektedir. Uygulamanızı *çok kiracılı* olarak yapılandırmak, birçok kuruluşa bir **hizmet olarak yazılım** (SaaS) uygulaması sunabileceğiniz, kullanıcıların onay sağladıktan sonra uygulamanızda oturum açabilmesine izin veren anlamına gelir.

| Platform | Description | Bağlantı |
| -------- | --------------------- | -------- |
| ![Bu resimde angular logosu ](media/sample-v2-code/logo_angular.png) [(msal angular 2,0)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) gösterilmektedir | Çok kiracılı SPA çağrıları Graph API |[MS-Identity-JavaScript-angular-Spa-ASPNET-WebApi-Multitenant](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnet-webapi-multitenant/tree/master/Chapter1) |
| ![Bu resimde angular logosu ](media/sample-v2-code/logo_angular.png) [(msal angular 2,0)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) gösterilmektedir | Çok kiracılı SPA, çok kiracılı özel Web API 'sini çağırır |[MS-Identity-JavaScript-angular-Spa-ASPNET-WebApi-Multitenant](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnet-webapi-multitenant/tree/master/Chapter2) |
| ![Bu görüntüde ASP.NET Core logo ](media/sample-v2-code/logo_NETcore.png) [.NET Core (msal.net)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) gösterilmektedir | ASP.NET Core MVC web uygulaması çağrıları Graph API |[Active-Directory-aspnetcore-WebApp-openıdconnect-v2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-3-Multi-Tenant) |
| ![Bu görüntüde ASP.NET Core logo ](media/sample-v2-code/logo_NETcore.png) [.NET Core (msal.net)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) gösterilmektedir | Web API ASP.NET Core ASP.NET Core MVC web uygulaması çağrıları |[Active-Directory-aspnetcore-WebApp-openıdconnect-v2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/4-WebApp-your-API/4-3-AnyOrg) |

## <a name="web-apis"></a>Web API'leri

Aşağıdaki örneklerde, bir Web API 'sinin Microsoft Identity platformu ile nasıl korunacağı ve Web API 'sinden bir aşağı akış API 'sinin nasıl çağrılacağını gösterilmektedir.

| Platform | Örnek |
| -------- | ------------------- |
| ![Bu görüntüde ASP.NET Core logosu gösterilmektedir](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core | [DotNet-Native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi-calls-msgraph) ASP.NET Core Web API (hizmeti)  |
| ![Bu görüntüde ASP.NET logosu gösterilmektedir](media/sample-v2-code/logo_NET.png)</p>ASP.NET MVC | [MS-Identity-ASPNET-WebApi-OnBehalfOf](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof) Web API (hizmeti) |
| ![Bu görüntüde Java logosu gösterilir](media/sample-v2-code/logo_java.png) | [MS-Identity-Java-WebApi](https://github.com/Azure-Samples/ms-identity-java-webapi) Web API (hizmeti) |
| ![Bu görüntüde Node.js logosu gösterilmektedir](media/sample-v2-code/logo_nodejs.png)</p>Node.js (Passport.js)| [Active-Directory-JavaScript-NodeJS-WebApi-v2](https://github.com/Azure-Samples/active-directory-javascript-nodejs-webapi-v2) Web API (hizmeti) |
| ![Bu görüntüde Node.js logosu gösterilmektedir](media/sample-v2-code/logo_nodejs.png)</p>Node.js (Passport.js)| B2C Web API (hizmeti) [Active-Directory-B2C-JavaScript-NodeJS-WebApi](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) |

## <a name="azure-functions-as-web-apis"></a>Web API 'Leri olarak Azure Işlevleri

Aşağıdaki örneklerde, HttpTrigger kullanarak bir Azure Işlevinin nasıl korunacağı ve Microsoft Identity platformu ile bir Web API 'sinin nasıl yapılacağı ve Web API 'sinden bir aşağı akış API 'sinin nasıl çağrılacağını gösterilmektedir.

| Platform | Örnek |
| -------- | ------------------- |
| ![Bu görüntüde ASP.NET Core logosu gösterilmektedir](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core | ASP.NET Core Web API (hizmet) Azure Işlevi [DotNet-Native-aspnetcore-v2](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions)  |
| ![Bu görüntüde Python logosu gösterilmektedir](media/sample-v2-code/logo_python.png)</p>Python | [Python](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions) 'un Web API 'si (hizmeti) |
| ![Bu görüntüde Node.js logosu gösterilmektedir](media/sample-v2-code/logo_nodejs.png)</p>Node.js (Passport.js)| Node.js ve Passport için Web API (hizmeti) [ -Azure-AD](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions) |
| ![Bu görüntüde Node.js logosu gösterilmektedir](media/sample-v2-code/logo_nodejs.png)</p>Node.js (Passport.js)| Node.js ve Passport için Web API (hizmeti) [ -Azure-AD adına using](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions) |

## <a name="other-microsoft-graph-samples"></a>Diğer Microsoft Graph örnekleri

Azure AD ile kimlik doğrulaması da dahil olmak üzere Microsoft Graph API 'SI için farklı kullanım desenleri gösteren [örnekler](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet) ve öğreticiler hakkında bilgi edinmek için bkz. [Microsoft Graph topluluk örnekleri & öğreticiler](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>Ayrıca bkz.

[Microsoft Graph API kavramsal ve başvuru](/graph/use-the-api?context=graph%2fapi%2fbeta&view=graph-rest-beta&preserve-view=true)
