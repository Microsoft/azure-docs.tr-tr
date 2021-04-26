---
title: Microsoft Identity Web kimlik doğrulaması kitaplığına genel bakış
titleSuffix: Microsoft identity platform
description: Azure Active Directory, Azure AD B2C ve Microsoft Graph ve diğer Web API 'Leri ile tümleştirilen ASP.NET Core uygulamalar için Microsoft Identity Web, bir kimlik doğrulama ve yetkilendirme Kitaplığı hakkında bilgi edinin.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/09/2020
ms.author: jmprieur
ms.reviewer: marsma
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 8bc9773fa7e1b3db2e249e803f4d2a3cf39edb48
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100102555"
---
# <a name="microsoft-identity-web-authentication-library"></a>Microsoft Identity Web kimlik doğrulaması kitaplığı

Microsoft Identity Web, Microsoft Identity platformu ile tümleştirerek Web uygulamalarına ve Web API 'Lerine kimlik doğrulama ve yetkilendirme desteği eklemeyi kolaylaştıran bir ASP.NET Core kitaplıkları kümesidir. .NET için ASP.NET Core, kimlik doğrulama ara yazılımını ve [Microsoft kimlik doğrulama kitaplığı 'nı (msal)](https://github.com/azuread/microsoft-authentication-library-for-dotnet)birbirine bağlayan tek yüzeyli bir API kolay bir katman sağlar.

## <a name="supported-application-scenarios"></a>Desteklenen uygulama senaryoları

ASP.NET Core Web uygulamaları veya Web API 'Leri oluşturuyorsanız ve kimlik ve erişim yönetimi (ıAM) için Azure Active Directory (Azure AD) veya Azure AD B2C kullanmak istiyorsanız, bu senaryoların tümünde Microsoft Identity Web kullanmanızı öneririz:

- [Kullanıcılar için oturum açan web uygulaması](scenario-web-app-sign-user-overview.md)
- [Kullanıcılara oturum açan ve kendi adına bir Web API 'SI çağıran Web uygulaması](scenario-web-app-call-api-overview.md)
- [Yalnızca kimliği doğrulanmış kullanıcıların erişebileceği korumalı Web API 'SI](scenario-protected-web-api-overview.md)
- [Oturum açmış kullanıcı adına başka bir (aşağı akış) Web API 'SI çağıran korumalı Web API 'SI](scenario-web-api-call-api-overview.md)

## <a name="get-the-library"></a>Kitaplığı al

[NuGet](#nuget), [.NET Core proje şablonları](#project-templates)ve [GitHub](#github)'dan Microsoft Identity Web 'i edinebilirsiniz.

#### <a name="nuget"></a>NuGet

Microsoft Identity Web,, uygulamanızın ihtiyaçlarını temel alan modüler işlevselliği sağlayan bir dizi paket olarak NuGet 'de kullanılabilir. `dotnet add`Projeniz için uygun olan paketleri yüklemek için .net CLI 'nın komutunu veya Visual Studio 'Nun **NuGet Paket Yöneticisi 'ni** kullanın:

- [Microsoft. Identity. Web](https://www.nuget.org/packages/Microsoft.Identity.Web) -ana paket. Microsoft Identity Web kullanan tüm uygulamalar için gereklidir.
- [Microsoft. Identity. Web. UI](https://www.nuget.org/packages/Microsoft.Identity.Web.UI) -isteğe bağlı. Kullanıcı oturum açma ve oturum kapatma için kullanıcı ARABIRIMI ve Web uygulamaları için ilişkili bir denetleyici ekler.
- [Microsoft. Identity. Web. MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraph) -isteğe bağlı. Microsoft Graph API ile basitleştirilmiş etkileşim sağlar.
- [Microsoft. Identity. Web. MicrosoftGraphBeta](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraphBeta) -isteğe bağlı. Microsoft Graph API [Beta uç noktasıyla](/graph/api/overview?view=graph-rest-beta&preserve-view=true)Basitleştirilmiş etkileşim sağlar.

#### <a name="project-templates"></a>Proje şablonları

Microsoft Identity Web projesi şablonları .NET 5,0 ' ye dahildir ve ASP.NET Core 3,1 projeleri için indirilebilir.

ASP.NET Core 3,1 kullanıyorsanız, .NET CLı ile şablonları yükleyebilirsiniz:

```dotnetcli
dotnet new --install Microsoft.Identity.Web.ProjectTemplates::1.0.0
```

Aşağıdaki diyagramda, desteklenen uygulama türlerinin ve ilgili bağımsız değişkenlerinin üst düzey bir görünümü gösterilmektedir:

:::image type="content" source="media/microsoft-identity-web-overview/diagram-microsoft-identity-web-templates.png" lightbox="media/microsoft-identity-web-overview/diagram-microsoft-identity-web-templates.png" alt-text="Microsoft Identity Web için kullanılabilir dot net CLı proje şablonlarının diyagramı":::
<br /><sup><b>*</b></sup>`MultiOrg`, ile desteklenmez `webapi2` , ancak kiracı ya da olarak ayarlanarak *appsettings.jsüzerinde* etkinleştirilebilir. `common``organizations`
<br /><sup><b>**</b></sup>`--calls-graph`Azure AD B2C için desteklenmez

[Blazor Server öğreticimizden](tutorial-blazor-server.md)alınan bu örnek .net CLI komutu, doğru paketleri ve Başlatıcı kodunu (yer tutucu değerleri gösterilen) içeren yeni bir Blazor Server projesi oluşturur:

```dotnetcli
dotnet new blazorserver2 --auth SingleOrg --calls-graph --client-id "00000000-0000-0000-0000-000000000000" --tenant-id "11111111-1111-1111-1111-111111111111" --output my-blazor-app
```

#### <a name="github"></a>GitHub

Microsoft Identity Web, GitHub: <a href="https://github.com/AzureAD/microsoft-identity-web" target="_blank">Azuread/Microsoft-Identity-Web</a> üzerinde barındırılan açık kaynaklı bir projem

[Depo wiki](https://github.com/AzureAD/microsoft-identity-web/wiki) ek belgeler içerir ve yardıma ihtiyacınız varsa veya bir hatayı buldıysanız [bir sorun](https://github.com/AzureAD/microsoft-identity-web/issues)oluşturabilirsiniz.

## <a name="features"></a>Özellikler

Varsayılan ASP.NET 3,1 proje şablonlarını kullanırsanız Microsoft Identity Web, sağlanmayan birçok özelliği içerir.

| Özellik                                                                                  | ASP.NET Core 3,1                                                     | Microsoft Identity Web                                                                                  |
|------------------------------------------------------------------------------------------|----------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------|
| Web uygulamalarında [oturum açma kullanıcıları](scenario-web-app-sign-user-app-configuration.md)             | <li>İş veya okul hesapları<li>Sosyal kimlikler (Azure AD B2C ile) | <li>İş veya okul hesapları<li>Kişisel Microsoft hesapları<li>Sosyal kimlikler (Azure AD B2C ile)     |
| [Web API 'Lerini koruma](scenario-protected-web-api-app-configuration.md#microsoftidentityweb) | <li>İş veya okul hesapları<li>Sosyal kimlikler (Azure AD B2C ile) | <li>İş veya okul hesapları<li>Kişisel Microsoft hesapları<li>Sosyal kimlikler (Azure AD B2C ile)     |
| Çok kiracılı uygulamalarda veren doğrulaması                                                   | No                                                                   | Evet, [Tüm bulutlar](authentication-national-cloud.md) ve [Azure AD B2C](../../active-directory-b2c/index.yml) |
| Web uygulaması/API [Microsoft Graph 'ı çağırır] [senaryo-API-Call-Graph]                             | Hayır                                                                   | Yes                                                                                                     |
| Web uygulaması/API [çağıran Web API] [senaryo-API-Call-API]                                       | Hayır                                                                   | Yes                                                                                                     |
| Sertifika kimlik bilgilerini destekler                                                         | No                                                                   | Evet, Azure Key Vault dahil                                                                          |
| Web uygulamalarında artımlı onay ve koşullu erişim desteği                           | No                                                                   | Evet, MVC, Razor sayfaları ve Blazor                                                                    |
| Web API 'Lerinde belirteç şifreleme sertifikaları                                                | Hayır                                                                   | Yes                                                                                                     |
| [Kapsamlar/uygulama rolü doğrulaması] [senaryo-API-doğrulama] Web API 'Lerinde                        | Hayır                                                                   | Yes                                                                                                     |
| `WWW-Authenticate` Web API 'Lerinde üst bilgi oluşturma                                         | Hayır                                                                   | Evet                                                                                                     |

## <a name="next-steps"></a>Sonraki adımlar

Microsoft Identity Web 'i çalışır durumda görmek için Blazor sunucu Öğreticimizi deneyin:

[Öğretici: kimlik doğrulaması için Microsoft Identity platformunu kullanan bir Blazor Server uygulaması oluşturma](tutorial-blazor-server.md)

GitHub 'daki Microsoft Identity Web wiki, kitaplığın çeşitli yönleri için kapsamlı başvuru belgeleri içerir. Örneğin, sertifika kullanımı, artımlı izin ve koşullu erişim başvurusu şurada bulunabilir:

- <a href="https://github.com/AzureAD/microsoft-identity-web/wiki/Using-certificates" target="_blank">Sertifikaları Microsoft. Identity. Web</a> (GitHub) ile kullanma
- <a href="https://github.com/AzureAD/microsoft-identity-web/wiki/Managing-incremental-consent-and-conditional-access" target="_blank">Artımlı izin ve koşullu erişim</a> (GitHub)

<!-- LINKS -->
<!--  [miw-certs]: microsoft-identity-web-certificates.md  -->
<!--  [miw-certs-decrypt]: microsoft-identity-web-certificates.md#decryption-certificates  -->
<!--  [miw-inc-consent-ca-header]: microsoft-identity-web-consent-conditional-access.md#handling-incremental-consent-or-conditional-access-in-web-apis  -->
<!--  [miw-inc-consent-ca]: microsoft-identity-web-consent-conditional-access.md  -->
[senaryo-API-Call-API]: senaryo-Web-API-Call-api-Call-API. MD # Option-1-Call-Microsoft-Graph-with-SDK  
[senaryo-api-Call-Graph]: senaryo-Web-API-Call-api-Call-API. MD # Option-1-Call-Microsoft-Graph-with-SDK  
[senaryo-api-doğrulama]: scenario-protected-web-api-verification-scope-app-roles.md  
