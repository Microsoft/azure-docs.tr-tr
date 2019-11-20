---
title: Azure AD 'ye bağlandığınızda bir WebAPI projesinde yapılan değişiklikler
description: Visual Studio kullanarak Azure AD 'ye bağlandığınızda, WebAPI projenizde ne olacağını açıklar.
author: ghogen
manager: jillfra
ms.assetid: 57630aee-26a2-4326-9dbb-ea2a66daa8b0
ms.workload: azure-vs
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.collection: M365-identity-device-management
ms.openlocfilehash: 32fc72d22d3f739018df22c315d7a1a3124c8823
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2019
ms.locfileid: "68851797"
---
# <a name="what-happened-to-my-webapi-project-visual-studio-azure-active-directory-connected-service"></a>WebAPI projem 'e ne oldu (Visual Studio Azure Active Directory bağlı hizmeti)

> [!div class="op_single_selector"]
> - [Başlarken](vs-active-directory-webapi-getting-started.md)
> - [Ne oldu](vs-active-directory-webapi-what-happened.md)

Bu makalede, [Visual Studio kullanarak Azure Active Directory bağlı hizmeti](vs-active-directory-add-connected-service.md)eklenirken ASP.net WebAPI, ASP.net tek sayfalı uygulama ve ASP.net Azure API projelerinde yapılan tam değişiklikler tanımlanmaktadır. Visual Studio 2015 ' de ASP.NET Azure mobil hizmet projeleri için de geçerlidir.

Bağlı hizmetle çalışma hakkında daha fazla bilgi için bkz. [](vs-active-directory-webapi-getting-started.md)Başlarken.

## <a name="added-references"></a>Eklenen başvurular

Proje dosyasını *. NET başvuruları) ve `packages.config` (NuGet başvuruları) etkiler.

| Type | Başvuru |
| --- | --- |
| NET NuGet | Microsoft.Owin |
| NET NuGet | Microsoft.Owin.Host.SystemWeb |
| NET NuGet | Microsoft.Owin.Security |
| NET NuGet | Microsoft. Owin. Security. ActiveDirectory |
| NET NuGet | Microsoft.Owin.Security.Jwt |
| NET NuGet | Microsoft. Owin. Security. OAuth |
| NET NuGet | Owın |
| NET NuGet | System.IdentityModel.Tokens.Jwt |

**Dizin verilerini oku** seçeneğini belirlediyseniz ek başvurular:

| Type | Başvuru |
| --- | --- |
| NET NuGet | EntityFramework |
| .NET        | EntityFramework. SqlServer (yalnızca Visual Studio 2015) |
| NET NuGet | Microsoft. Azure. ActiveDirectory. GraphClient |
| NET NuGet | Microsoft.Data.Edm |
| NET NuGet | Microsoft.Data.OData |
| NET NuGet | Microsoft.Data.Services.Client |
| NET NuGet | Microsoft. IdentityModel. clients. ActiveDirectory |
| .NET        | Microsoft. IdentityModel. clients. ActiveDirectory. WindowsForms<br>(Yalnızca Visual Studio 2015) |
| NET NuGet | System. uzamsal |

Aşağıdaki başvurular kaldırılır (Visual Studio 2015 ' de olduğu gibi, yalnızca ASP.NET 4 projeleri):

| Type | Başvuru |
| --- | --- |
| NET NuGet | Microsoft.AspNet.Identity.Core |
| NET NuGet | Microsoft.AspNet.Identity.EntityFramework |
| NET NuGet | Microsoft.AspNet.Identity.Owin |

## <a name="project-file-changes"></a>Proje dosyası değişiklikleri

- Özelliği `IISExpressSSLPort` ayrı bir sayı olarak ayarlayın.
- Özelliği `WebProject_DirectoryAccessLevelKey` 0 olarak ayarlayın veya **Dizin verilerini oku** seçeneğini belirlediyseniz 1 yapın.
- `IISUrl` `https://localhost:<port>/` Özelliğini değeri`IISExpressSSLPort` eşleşen olarak ayarlayın. `<port>`

## <a name="webconfig-or-appconfig-changes"></a>Web. config veya App. config değişiklikleri

- Aşağıdaki yapılandırma girdileri eklendi:

    ```xml
    <appSettings>
        <add key="ida:ClientId" value="<ClientId from the new Azure AD app>" />
        <add key="ida:Tenant" value="<your selected Azure domain>" />
        <add key="ida:Audience" value="<your selected domain + / + project name>" />
    </appSettings>
    ```

- Yalnızca Visual Studio 2017: Ayrıca, aşağıdaki girişi altına `<appSettings>`de eklendi "

    ```xml
    <add key="ida:MetadataAddress" value="<domain URL + /federationmetadata/2007-06/federationmetadata.xml>" />
    ```

- İçin `<dependentAssembly>` düğümününaltınaöğe`<runtime><assemblyBinding>`eklendi. `System.IdentityModel.Tokens.Jwt`

- **Dizin verilerini oku** seçeneğini belirlediyseniz aşağıdaki yapılandırma girişi altına `<appSettings>`eklendi:

    ```xml
    <add key="ida:Password" value="<Your Azure AD app's new password>" />
    ```

## <a name="code-changes-and-additions"></a>Kod değişiklikleri ve eklemeler

- `[Authorize]` Özniteliğivediğer`Controllers/ValueController.cs` mevcut denetleyicilere eklendi.

- Azure AD kimlik doğrulaması için başlangıç `App_Start/Startup.Auth.cs`mantığını içeren veya uygun şekilde değiştirilen bir kimlik doğrulama başlangıç sınıfı eklendi. **Dizin verilerini oku** seçeneğini belirlediyseniz, bu dosya Ayrıca bir OAuth kodu alacak kodu içerir ve bir erişim belirteci için onu değiş tokuş sağlar.

- (Yalnızca ASP.NET 4 uygulama içeren Visual Studio 2015) Kaldırıldı `App_Start/IdentityConfig.cs` ve eklendi `Controllers/AccountController.cs`, `Models/IdentityModel.cs`ve .`Providers/ApplicationAuthProvider.cs`

- Bağlı `Connected Services/AzureAD/ConnectedService.json` hizmetin eklenmesini izlemek için Visual Studio `Service References/Azure AD/ConnectedService.json` tarafından kullanılan bilgileri içeren (Visual Studio 2017) veya (Visual Studio 2015) eklendi.

### <a name="file-backup-visual-studio-2015"></a>Dosya yedekleme (Visual Studio 2015)

Bağlı hizmet eklenirken, Visual Studio 2015, değiştirilen ve kaldırılan dosyaları yedekler. Etkilenen tüm dosyalar klasörüne `Backup/AzureAD`kaydedilir. Visual Studio 2017, yedeklemeler oluşturmaz.

- `Startup.cs`
- `App_Start\IdentityConfig.cs`
- `App_Start\Startup.Auth.cs`
- `Controllers\AccountController.cs`
- `Controllers\ManageController.cs`
- `Models\IdentityModels.cs`
- `Models\ApplicationOAuthProvider.cs`

## <a name="changes-on-azure"></a>Azure 'daki değişiklikler

- Bağlı hizmeti eklerken seçtiğiniz etki alanında bir Azure AD uygulaması oluşturuldu.
- Uygulama, bu seçenek seçildiyse, **Dizin verilerini oku** iznini içerecek şekilde güncelleştirildi.

[Azure Active Directory hakkında daha fazla bilgi edinin](https://azure.microsoft.com/services/active-directory/).

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Active Directory için kimlik doğrulama senaryoları](authentication-scenarios.md)
- [Microsoft 'a bir ASP.NET Web uygulamasına oturum açma ekleme](quickstart-v1-aspnet-webapp.md)
