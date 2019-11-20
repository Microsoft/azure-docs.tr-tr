---
title: Microsoft Identity platform için kod örnekleri | Microsoft Docs
description: Senaryoya göre düzenlenmiş kullanılabilir Microsoft Identity platform (v 2.0 uç noktası) kod örneklerinin bir dizinini sağlar.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: a242a5ff-7300-40c2-ba83-fb6035707433
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/15/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 84c94675ac5c69055cd2811df206f81af55d3c1e
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73927202"
---
# <a name="microsoft-identity-platform-code-samples-v20-endpoint"></a>Microsoft Identity platform kodu örnekleri (v 2.0 uç noktası)

Microsoft Identity platformunu kullanarak şunları yapabilirsiniz:

- Web uygulamalarınıza ve Web API 'Lerine kimlik doğrulaması ve yetkilendirme ekleyin.
- Korumalı bir Web API 'sine erişmek için erişim belirteci gerektir.

Bu makalede kısaca Microsoft Identity platform uç noktası için örneklere bağlantılar sunulmaktadır. Bu örnekler, bunun nasıl yapıldığını gösterir ve uygulamalarınızda kullanabileceğiniz kod parçacıklarını da sağlar. Kod örneği sayfasında gereksinimler, yükleme ve Kuruluma yardımcı olacak ayrıntılı Benioku konuları bulacaksınız. Kod içindeki açıklamalar kritik bölümleri anlamanıza yardımcı olur.

> [!NOTE]
> V 1.0 örnekleri ile ilgileniyorsanız, bkz. [Azure AD kod örnekleri (v 1.0 uç noktası)](sample-v1-code.md).

Her örnek tür için temel senaryoyu anlamak üzere bkz. [Microsoft Identity platform uç noktası Için uygulama türleri](v2-app-types.md).

GitHub 'daki örneklere da katkıda bulunabilirsiniz. Nasıl yapılacağını öğrenmek için bkz. [Microsoft Azure Active Directory örnekleri ve belgeleri](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="single-page-applications"></a>Tek sayfalı uygulamalar

Bu örnekler, Microsoft Identity platform ile güvenliği sağlanmış tek sayfalı bir uygulamanın nasıl yazılacağını gösterir. Bu örnekler MSAL. js ' nin türlerini kullanır.

| Platform | Açıklama | Bağlantı |
| -------- | --------------------- | -------- |
| Bu görüntüde ![JavaScript logosu](media/sample-v2-code/logo_js.png) [JavaScript (msal. js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) gösterilmektedir | Çağrılar Microsoft Graph |[JavaScript-graphapı-Web-v2](https://github.com/Azure-Samples/active-directory-javascript-graphapi-web-v2) |
| Bu görüntüde ![JavaScript logosu](media/sample-v2-code/logo_js.png) [JavaScript (msal. js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) gösterilmektedir | B2C çağırır |[B2C-JavaScript-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp) |
| Bu görüntüde ![JavaScript logosu](media/sample-v2-code/logo_js.png) [JavaScript (msal. js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) gösterilmektedir | Kendi Web API 'sini çağırır |[JavaScript-singlepageapp-DotNet-WebApi-v2](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) |
| Bu görüntüde ![angular JS logosu [JavaScript](media/sample-v2-code/logo_angular.png) (msal AngularJS)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs) gösterilmektedir| Çağrılar Microsoft Graph  | [MsalAngularjsDemoApp](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/samples/MsalAngularjsDemoApp)
| Bu görüntüde ![, [JavaScript](media/sample-v2-code/logo_angular.png) JavaScript (msal angular)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) gibi angular logosu gösterilmektedir| Çağrılar Microsoft Graph  | [MSALAngularDemoApp](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/samples/MSALAngularDemoApp) |

## <a name="web-applications"></a>Web uygulamaları

Aşağıdaki örneklerde, kullanıcıların oturum açmasını sağlayan Web uygulamaları gösterilmektedir. Bazı örnekler ayrıca Microsoft Graph çağıran uygulamayı ya da kullanıcının kimliğiyle kendi Web API 'nizi gösterir.

| Platform | Yalnızca kullanıcılar oturum açar | Kullanıcılar ve çağrılar Microsoft Graph imzalar |
| -------- | ------------------- | --------------------------------- |
| ![Bu görüntüde ASP.NET Core logosu gösterilmektedir](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core 2,2 | [ASP.NET Core WebApp oturum açan kullanıcılar öğreticisi](https://aka.ms/aspnetcore-webapp-sign-in) | [ASP.NET Core Web uygulaması çağrılarındaki](https://aka.ms/aspnetcore-webapp-call-msgraph) aynı örnek Microsoft Graph aşaması |
| ![Bu görüntüde ASP.NET logosu gösterilmektedir](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET | [ASP.NET hızlı başlangıç](https://github.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) </p> [DotNet-WebApp-openıdconnect-v2](https://github.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2)  |  [DotNet-Yönetici-kısıtlı-kapsamlar-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) </p> |[MSGraph-eğitim-aspnetmvcapp](https://github.com/microsoftgraph/msgraph-training-aspnetmvcapp)
| ![Bu görüntüde Java logosu gösterilir](media/sample-v2-code/logo_java.png)  |                   | [MS-Identity-Java-WebApp](https://github.com/Azure-Samples/ms-identity-java-webapp): Microsoft Graph ÇAĞıRAN bir MSAL4J Web uygulaması |
| ![Bu görüntüde Node. js logosu gösterilmektedir](media/sample-v2-code/logo_nodejs.png)  |                   | [Node. js hızlı başlangıç](https://github.com/azureadquickstarts/appmodelv2-webapp-openidconnect-nodejs) |
| ![Bu görüntüde Ruby logosu gösterilmektedir](media/sample-v2-code/logo_ruby.png) |                   | [MSGraph-eğitim-rubyrailsapp](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp) |

## <a name="desktop-and-mobile-public-client-apps"></a>Masaüstü ve mobil ortak istemci uygulamaları

Aşağıdaki örneklerde, Microsoft Graph API 'sine veya bir kullanıcı adına kendi Web API 'sine erişen ortak istemci uygulamaları (masaüstü veya mobil uygulamalar) gösterilmektedir. Tüm bu istemci uygulamaları Microsoft kimlik doğrulama kitaplığı 'nı (MSAL) kullanır.

| İstemci uygulaması | Platform | Flow/ver | Çağrılar Microsoft Graph | ASP.NET Core 2,0 Web API 'sini çağırır |
| ------------------ | -------- |  ----------| ---------- | ------------------------- |
| Masaüstü (WPF)      | ![Bu görüntüde .NET/C# logo gösterilmektedir](media/sample-v2-code/logo_NET.png) | [etkileşimli](msal-authentication-flows.md#interactive)| [DotNet-masaüstü-MSGraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | [DotNet-Native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi) |
| Masaüstü (konsol)   | ![Bu görüntüde .NET/C# (Masaüstü) logosu gösterilir](media/sample-v2-code/logo_NET.png) | [Tümleşik Windows kimlik doğrulaması](msal-authentication-flows.md#integrated-windows-authentication) | [DotNet-IWA-v2](https://github.com/azure-samples/active-directory-dotnet-iwa-v2) |  |
| Masaüstü (konsol)   | ![Bu görüntüde .NET/C# (Masaüstü) logosu gösterilir](media/sample-v2-code/logo_NETcore.png) | [Kullanıcı Adı/Parola](msal-authentication-flows.md#usernamepassword) |[dotnetcore-up-v2](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2) |  |
| Mobil (Android, iOS, UWP)   | ![Bu görüntüde .NET/C# (Xamarin) logosu gösterilir](media/sample-v2-code/logo_xamarin.png) | [etkileşimli](msal-authentication-flows.md#interactive) |[Xamarin-Native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) |  |
| Mobil (iOS)       | ![Bu görüntüde iOS/amaç-C veya Swift gösterilmektedir](media/sample-v2-code/logo_iOS.png) | [etkileşimli](msal-authentication-flows.md#interactive) |[iOS-Swift-ObjC-Native-v2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) </p> [iOS-Native-nxoauth2-v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) |  |
| Masaüstü (macOS)       | macOS | [etkileşimli](msal-authentication-flows.md#interactive) |[macOS-Swift-ObjC-Native-v2](https://github.com/Azure-Samples/ms-identity-macOS-swift-objc) |  |
| Mobil (Android-Java)   | ![Bu resimde Android logosu gösterilir](media/sample-v2-code/logo_Android.png) | [etkileşimli](msal-authentication-flows.md#interactive) |  [Android-Java](https://github.com/Azure-Samples/ms-identity-android-java) |  |
| Mobil (Android-Kotlin)   | ![Bu resimde Android logosu gösterilir](media/sample-v2-code/logo_Android.png) | [etkileşimli](msal-authentication-flows.md#interactive) |  [Android-Kotlin](https://github.com/Azure-Samples/ms-identity-android-kotlin) |  |

## <a name="daemon-applications"></a>Daemon uygulamaları

Aşağıdaki örneklerde, Microsoft Graph API 'sine kendi kimliğiyle (Kullanıcı olmadan) erişen bir uygulama gösterilmektedir.

| İstemci uygulaması | Platform | Flow/ver | Çağrılar Microsoft Graph |
| ------------------ | -------- | ---------- | -------------------- |
| Konsol | ![Bu görüntüde .NET Core logosu gösterilmektedir](media/sample-v2-code/logo_NETcore.png)</p> ASP.NET  | [İstemci kimlik bilgileri](msal-authentication-flows.md#client-credentials) | [dotnetcore-Daemon-v2](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2) |
| Web uygulaması | ![Bu görüntüde ASP.NET logosu gösterilmektedir](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET  | [İstemci kimlik bilgileri](msal-authentication-flows.md#client-credentials) | [DotNet-Daemon-v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2) |

## <a name="headless-applications"></a>Gözetimsiz uygulamalar

Aşağıdaki örnek, bir Web tarayıcısı olmadan cihazda çalışan ortak bir istemci uygulamasını gösterir. Uygulama, bir komut satırı aracı, Linux veya Mac üzerinde çalışan bir uygulama ya da bir IoT uygulaması olabilir. Örnek, başka bir cihazda (cep telefonu gibi) etkileşimli oturum açan bir kullanıcının adında Microsoft Graph API 'sine erişen bir uygulama sunar. Bu istemci uygulaması Microsoft kimlik doğrulama kitaplığı 'nı (MSAL) kullanır.

| İstemci uygulaması | Platform | Flow/ver | Çağrılar Microsoft Graph |
| ------------------ | -------- |  ----------| ---------- |
| Masaüstü (konsol)   | ![Bu görüntüde .NET/C# (Masaüstü) logosu gösterilir](media/sample-v2-code/logo_NETcore.png) | [Cihaz kod akışı](msal-authentication-flows.md#device-code) |[dotnetcore-devicecodeflow-v2](https://github.com/azure-samples/active-directory-dotnetcore-devicecodeflow-v2) |

## <a name="web-apis"></a>Web API'leri

Aşağıdaki örneklerde, bir Web API 'sinin Microsoft Identity platform uç noktası ile nasıl korunacağı ve Web API 'sinden bir aşağı akış API 'sinin nasıl çağrılacağını gösterilmektedir.

| Platform | Örnek |
| -------- | ------------------- |
| ![Bu görüntüde ASP.NET Core logosu gösterilmektedir](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core 2,2 | [DotNet-Native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi-calls-msgraph) ASP.NET Core Web API (hizmeti)  |
| ![Bu görüntüde ASP.NET logosu gösterilmektedir](media/sample-v2-code/logo_NET.png)</p>ASP.NET MVC | [MS-Identity-ASPNET-WebApi-OnBehalfOf](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof) Web API (hizmeti) |

## <a name="azure-functions-as-web-apis"></a>Web API 'Leri olarak Azure Işlevleri

Aşağıdaki örneklerde HttpTrigger kullanılarak bir Azure Işlevinin nasıl korunacağı ve Microsoft Identity platform uç noktasıyla bir Web API 'sinin nasıl yapılacağı ve Web API 'sinden bir aşağı akış API 'sinin nasıl çağrılacağını gösterilmektedir.

| Platform | Örnek |
| -------- | ------------------- |
| ![Bu görüntüde ASP.NET Core logosu gösterilmektedir](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core 2,2 | ASP.NET Core Web API (hizmet) Azure Işlevi [DotNet-Native-aspnetcore-v2](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions)  |
| ![Bu görüntüde Node. js logosu gösterilmektedir](media/sample-v2-code/logo_nodejs.png)</p>NodeJS | NodeJS ve Passport Web API (hizmeti) [-Azure-AD](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions) |
| ![Bu görüntüde Python logosu gösterilmektedir](media/sample-v2-code/logo_python.png)</p>Python | [Python](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions) 'un Web API 'si (hizmeti) |
| ![Bu görüntüde Node. js logosu gösterilmektedir](media/sample-v2-code/logo_nodejs.png)</p>NodeJS | NodeJS ve Passport için Web API (hizmeti) [-Azure-AD 'nin adına using](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions) |


## <a name="other-microsoft-graph-samples"></a>Diğer Microsoft Graph örnekleri

Azure AD ile kimlik doğrulaması da dahil olmak üzere Microsoft Graph API 'SI için farklı kullanım desenleri gösteren [örnekler](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet) ve öğreticiler hakkında bilgi edinmek için bkz. [Microsoft Graph topluluk örnekleri & Öğreticiler](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>Ayrıca bkz.

- [Azure Active Directory (v 1.0) Geliştirici Kılavuzu](v1-overview.md)
- [Azure AD Graph API kavramsal ve başvuru](https://msdn.microsoft.com/library/azure/hh974476.aspx)
- [Azure AD Graph API Yardımcısı kitaplığı](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)
