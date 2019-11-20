---
title: Microsoft Identity platform kimlik doğrulama kitaplıkları | Microsoft Docs
description: Microsoft Identity platform uç noktası için ilgili kitaplık, kaynak ve örnek bağlantılarla birlikte uyumlu istemci kitaplıkları ve sunucu ara yazılım kitaplıkları.
services: active-directory
documentationcenter: ''
author: negoe
manager: CelesteDG
editor: ''
ms.assetid: 19cec615-e51f-4141-9f8c-aaf38ff9f746
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/25/2019
ms.author: negoe
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a09f782e9a4d31385868e8383b9b7be83d85d0f0
ms.sourcegitcommit: fa5ce8924930f56bcac17f6c2a359c1a5b9660c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/31/2019
ms.locfileid: "73200307"
---
# <a name="microsoft-identity-platform-authentication-libraries"></a>Microsoft Identity platform kimlik doğrulama kitaplıkları

[Microsoft Identity platform uç noktası](active-directory-v2-compare.md) , sektör standardı OAuth 2,0 ve OpenID Connect 1,0 protokollerini destekler. Microsoft kimlik doğrulama kitaplığı (MSAL), Microsoft Identity platform uç noktasıyla çalışacak şekilde tasarlanmıştır. OAuth 2,0 ve OpenID Connect 1,0 ' i destekleyen açık kaynaklı kitaplıkları da kullanabilirsiniz.

Güvenlik geliştirme yaşam döngüsü (SDL) metodolojisini izleyen protokol etki alanı uzmanları tarafından yazılmış kitaplıkları kullanmanızı öneririz. Bu tür yöntemler [, Microsoft 'un izlediği bir][Microsoft-SDL]yöntem içerir. Protokoller için kod kullandıysanız, Microsoft SDL gibi bir metodolojiyi izlemelisiniz. Her protokol için standartlar belirtimlerinde güvenlik açısından dikkat edilmesi gereken noktaları ödeyin.

> [!NOTE]
> Azure Active Directory kimlik doğrulaması kitaplığı (ADAL) arıyor musunuz? [Adal kitaplığı kılavuzuna](active-directory-authentication-libraries.md)göz atın.

## <a name="types-of-libraries"></a>Kitaplık türleri

Microsoft Identity platform uç noktası iki tür kitaplık ile çalışmaktadır:

* **İstemci kitaplıkları**: yerel istemciler ve sunucular, Microsoft Graph gibi bir kaynağı çağırmak için erişim belirteçleri elde etmek üzere istemci kitaplıklarını kullanır.
* **Sunucu ara yazılım kitaplıkları**: Web Apps, Kullanıcı oturum açma için sunucu ara yazılım kitaplıklarını kullanır. Web API 'Leri, yerel istemciler veya diğer sunucular tarafından gönderilen belirteçleri doğrulamak için sunucu ara yazılım kitaplıklarını kullanır.

## <a name="library-support"></a>Kitaplık desteği

Kitaplıklar iki destek kategorisinde gelir:

* **Microsoft tarafından desteklenen**: Microsoft, bu kitaplıklara yönelik düzeltmeler sağlar ve bu kitaplıklarda, bu kitaplıklara yönelik olarak bir işlem yapılmakta.
* **Uyumlu**: Microsoft bu kitaplıkları temel senaryolarda test etmiştir ve Microsoft Identity platform uç noktasıyla çalıştığını doğruladı. Microsoft bu kitaplıklara yönelik düzeltmeler sağlamaz ve bu kitaplıkların gözden geçirilmesini gerçekleştirmemiştir. Sorunlar ve özellik istekleri kitaplığın açık kaynaklı projesine yönlendirilmelidir.

Microsoft Identity platform uç noktasıyla çalışan kitaplıkların listesi için aşağıdaki bölümlere bakın.

## <a name="microsoft-supported-client-libraries"></a>Microsoft tarafından desteklenen istemci kitaplıkları

Korumalı bir Web API 'SI çağırmak için bir belirteç almak üzere istemci kimlik doğrulama kitaplıklarını kullanın.

| Platform | Kitaplık | İndirin | Kaynak kodu | Örnek | Başvuru | Kavramsal belge | Yol haritası |
| --- | --- | --- | --- | --- | --- | --- | --- |
| ![JavaScript](media/sample-v2-code/logo_js.png) | MSAL.js  | [NPM](https://www.npmjs.com/package/msal) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/README.md) |  [Tek sayfalı uygulama](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) | [Başvuru](https://htmlpreview.github.io/?https://raw.githubusercontent.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core/docs/classes/_useragentapplication_.useragentapplication.html) | [Kavramsal belgeler](msal-overview.md)| [Yol Haritası](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki#roadmap)
|![Angular JS](media/sample-v2-code/logo_angular.png) | MSAL angular JS | [NPM](https://www.npmjs.com/package/@azure/msal-angularjs) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs/README.md) |  |  | |
![Angular](media/sample-v2-code/logo_angular.png) | MSAL angular (Önizleme) | [NPM](https://www.npmjs.com/package/@azure/msal-angular) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | | | |
| ![.NET Framework](media/sample-v2-code/logo_NET.png) ![UWP](media/sample-v2-code/logo_windows.png) ![Xamarin](media/sample-v2-code/logo_xamarin.png) | MSAL.NET  |[NuGet](https://www.nuget.org/packages/Microsoft.Identity.Client) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | [Masaüstü uygulaması](guidedsetups/active-directory-mobileanddesktopapp-windowsdesktop-intro.md) | [MSAL.NET](https://docs.microsoft.com/dotnet/api/microsoft.identity.client?view=azure-dotnet-preview) |[Kavramsal belgeler](msal-overview.md) | [Yol Haritası](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki#roadmap)
| ![Python](media/sample-v2-code/logo_python.png) | MSAL Python (Önizleme) | [Pypı](https://pypi.org/project/msal) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-python) | [Örnekler](https://github.com/AzureAD/microsoft-authentication-library-for-python/tree/dev/sample) | [Belgeleri Readthe](https://msal-python.rtfd.io/) | [Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki) | [Yol Haritası](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki/Roadmap)
| ![Java](media/sample-v2-code/logo_java.png) | MSAL Java (Önizleme) | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/msal4j) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-java) | [Örnekler](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples) | [Başvuru](https://javadoc.io/doc/com.microsoft.azure/msal4j/0.6.0-preview/index.html) | [Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki) | [Yol Haritası](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki)
| iOS & macOS | MSAL iOS ve macOS | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [iOS uygulaması](https://github.com/Azure-Samples/ms-identity-mobile-apple-swift-objc), [MacOS uygulaması](https://github.com/Azure-Samples/ms-identity-macOS-swift-objc) | [Başvuru](https://azuread.github.io/microsoft-authentication-library-for-objc/index.html)  | [Kavramsal belgeler](msal-overview.md) | |
|![Android/Java](media/sample-v2-code/logo_Android.png) | MSAL Android | [Merkezi depo](https://repo1.maven.org/maven2/com/microsoft/identity/client/msal/) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android) | [Android uygulaması](quickstart-v2-android.md) | [JavaDocs](https://javadoc.io/doc/com.microsoft.identity.client/msal) | [Kavramsal belgeler](msal-overview.md) |[Yol Haritası](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki/Roadmap)

## <a name="microsoft-supported-server-middleware-libraries"></a>Microsoft tarafından desteklenen sunucu ara yazılım kitaplıkları

Web uygulamalarını ve Web API 'Lerini korumaya yardımcı olması için ara yazılım kitaplıklarını kullanın. ASP.NET veya ASP.NET Core yazılmış Web uygulamaları veya Web API 'Leri, ara yazılım kitaplıklarını kullanır.

| Platform | Kitaplık | İndirin | Kaynak kodu | Örnek | Başvuru
| --- | --- | --- | --- | --- | --- |
| ![.NET](media/sample-v2-code/logo_NET.png) ![.NET Core](media/sample-v2-code/logo_NETcore.png) | ASP.NET güvenliği |[NuGet](https://www.nuget.org/packages/Microsoft.AspNet.Mvc/) |[GitHub](https://github.com/aspnet/AspNetCore) |[MVC uygulaması](quickstart-v2-aspnet-webapp.md) |[ASP.NET API başvurusu](https://docs.microsoft.com/dotnet/api/?view=aspnetcore-2.0) |
| ![.NET](media/sample-v2-code/logo_NET.png)| .NET için IdentityModel uzantıları| |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | [MVC uygulaması](quickstart-v2-aspnet-webapp.md) |[Başvuru](https://docs.microsoft.com/dotnet/api/overview/azure/activedirectory/client?view=azure-dotnet) |
| ![Node.js](media/sample-v2-code/logo_nodejs.png) | Azure AD Passport |[NPM](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [Web uygulaması](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs) | |

## <a name="microsoft-supported-libraries-by-os--language"></a>İşletim sistemine/dile göre desteklenen Microsoft kitaplıkları

Desteklenen işletim sistemleri vs dilleri söz konusu olduğunda, eşleme aşağıdaki gibi olur:

|             | Windows    | Linux      | macOS      | iOS | Android    |
|-------------|------------|------------|------------|------------|------------|
| ![JavaScript](media/sample-v2-code/logo_js.png)  |  MSAL.js | MSAL.js | MSAL.js | MSAL.js |  MSAL.js |
| <img alt="C#" src="../../cognitive-services/speech-service/media/index/logo_csharp.svg" width="64px" height="64px" /> | ASP.NET, ASP.NET Core, MSAL.Net (.NET ILT, Core, UWP)| ASP.NET Core, MSAL.Net (.NET Core) | ASP.NET Core, MSAL.Net (MacOS)       | MSAL.Net (Xamarin. iOS) | MSAL.Net (Xamarin. Android)|
| Swift <br> Objective-C |            |            | [İOS ve macOS için MSAL](msal-overview.md) | [İOS ve macOS için MSAL](msal-overview.md) |            |
| ![Java](media/sample-v2-code/logo_java.png) Java | msal4j | msal4j | msal4j | | MSAL Android |
| ![Python](media/sample-v2-code/logo_python.png) Python | MSAL Python | MSAL Python | MSAL Python |
| ![Node. js](media/sample-v2-code/logo_nodejs.png) Node.JS | Passport. Node | Passport. Node | Passport. Node |

Ayrıca bkz. [Desteklenen platformlar ve dillere göre senaryolar](authentication-flows-app-scenarios.md#scenarios-and-supported-platforms-and-languages)

## <a name="compatible-client-libraries"></a>Uyumlu istemci kitaplıkları

| Platform | Kitaplık adı | Test edilen sürüm | Kaynak kodu | Örnek |
|:---:|:---:|:---:|:---:|:---:|
|![JavaScript](media/sample-v2-code/logo_js.png)|[Merhaba. js](https://adodson.com/hello.js/) | Sürüm 1.13.5 |[Merhaba. js](https://github.com/MrSwitch/hello.js) |[Star](https://github.com/Azure-Samples/active-directory-javascript-graphapi-web-v2) |
| ![Java](media/sample-v2-code/logo_java.png) | [Java](https://github.com/scribejava/scribejava) | [Sürüm 3.2.0](https://github.com/scribejava/scribejava/releases/tag/scribejava-3.2.0) | [Karalama Java](https://github.com/scribejava/scribejava/) | |
| ![Java](media/sample-v2-code/logo_java.png) | [Gluu OpenID Connect kitaplığı](https://github.com/GluuFederation/oxAuth) | [Sürüm 3.0.2](https://github.com/GluuFederation/oxAuth/releases/tag/3.0.2) | [Gluu OpenID Connect kitaplığı](https://github.com/GluuFederation/oxAuth) | |
| ![Python](media/sample-v2-code/logo_python.png) | [İstekler-OAuthlib](https://github.com/requests/requests-oauthlib) | [Sürüm 1.2.0](https://github.com/requests/requests-oauthlib/releases/tag/v1.2.0) | [İstekler-OAuthlib](https://github.com/requests/requests-oauthlib) | |
| ![Node.js](media/sample-v2-code/logo_nodejs.png) | [OpenID-istemci](https://github.com/panva/node-openid-client) | [Sürüm 2.4.5](https://github.com/panva/node-openid-client/releases/tag/v2.4.5) | [OpenID-istemci](https://github.com/panva/node-openid-client) | |
| ![PHP](media/sample-v2-code/logo_php.png) | [PHP League OAuth2-Client](https://github.com/thephpleague/oauth2-client) | [Sürüm 1.4.2](https://github.com/thephpleague/oauth2-client/releases/tag/1.4.2) | [OAuth2-istemci](https://github.com/thephpleague/oauth2-client/) | |
| ![Ruby](media/sample-v2-code/logo_ruby.png) |[OmniAuth](https://github.com/omniauth/omniauth/wiki) |omniauth: 1.3.1<br />omniauth-OAuth2:1.4.0 |[OmniAuth](https://github.com/omniauth/omniauth)<br />[OmniAuth OAuth2](https://github.com/intridea/omniauth-oauth2) |  |
| iOS, macOS, & Android  | [Yerel uygulama kimlik doğrulamasını tepki verme](https://github.com/FormidableLabs/react-native-app-auth) | [Sürüm 4.2.0](https://github.com/FormidableLabs/react-native-app-auth/releases/tag/v4.2.0) | [Yerel uygulama kimlik doğrulamasını tepki verme](https://github.com/FormidableLabs/react-native-app-auth) | |

Standartlarla uyumlu herhangi bir kitaplık için Microsoft Identity platform uç noktasını kullanabilirsiniz. Destek için Nereye gidebileceğinizi bilmemiz önemlidir:

* Kitaplık kodundaki sorunlar ve yeni özellik istekleri için kitaplık sahibine başvurun.
* Hizmet tarafı protokol uygulamasındaki sorunlar ve yeni özellik istekleri için Microsoft ile iletişim kurun.
* Protokolde görmek istediğiniz ek özellikler için [bir özellik isteği](https://feedback.azure.com/forums/169401-azure-active-directory) yapın.
* Microsoft Identity platform uç noktasının OAuth 2,0 veya OpenID Connect 1,0 ile uyumlu olmadığı bir sorun bulursanız [bir destek Isteği oluşturun](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) .

## <a name="related-content"></a>İlgili içerik

Microsoft Identity platform uç noktası hakkında daha fazla bilgi için bkz. [Microsoft Identity platform 'a genel bakış][AAD-App-Model-V2-Overview].

<!--Image references-->

<!--Reference style links -->
[AAD-App-Model-V2-Overview]: v2-overview.md
[ClientLib-NET-Lib]: https://www.nuget.org/packages/Microsoft.Identity.Client
[ClientLib-NET-Repo]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[ClientLib-NET-Sample]: active-directory-v2-devquickstarts-wpf.md
[ClientLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ClientLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad
[ClientLib-Node-Sample]:/
[ClientLib-Iosmac-Lib]:/
[ClientLib-Iosmac-Repo]:/
[ClientLib-Iosmac-Sample]:/
[ClientLib-Android-Lib]:/
[ClientLib-Android-Repo]:/
[ClientLib-Android-Sample]:/
[ClientLib-Js-Lib]:/
[ClientLib-Js-Repo]:/
[ClientLib-Js-Sample]:/

[Microsoft-SDL]: https://www.microsoft.com/sdl/default.aspx
[ServerLib-Net4-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/
[ServerLib-Net4-Owin-Oidc-Repo]: https://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oidc-Sample]: active-directory-v2-devquickstarts-dotnet-web.md
[ServerLib-Net4-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OAuth/
[ServerLib-Net4-Owin-Oauth-Repo]: https://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oauth-Sample]: https://azure.microsoft.com/documentation/articles/active-directory-v2-devquickstarts-dotnet-api/
[ServerLib-Net-Jwt-Lib]: https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt
[ServerLib-Net-Jwt-Repo]: https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet
[ServerLib-Net-Jwt-Sample]:/
[ServerLib-NetCore-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OpenIdConnect/
[ServerLib-NetCore-Owin-Oidc-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oidc-Sample]: https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore-v2
[ServerLib-NetCore-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OAuth/
[ServerLib-NetCore-Owin-Oauth-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oauth-Sample]:/
[ServerLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ServerLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad/
[ServerLib-Node-Sample]: https://azure.microsoft.com/documentation/articles/active-directory-v2-devquickstarts-node-web/
