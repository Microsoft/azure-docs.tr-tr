---
title: Azure AD 'ye bağlandığınızda MVC projesinde yapılan değişiklikler
description: Visual Studio bağlı hizmetler 'i kullanarak Azure AD 'ye bağlandığınızda MVC projenizde ne olacağını açıklar.
author: ghogen
manager: jillfra
ms.assetid: 8b24adde-547e-4ffe-824a-2029ba210216
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c4328ea6145d32616f1784d94976dab29216fbc
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2019
ms.locfileid: "68852000"
---
# <a name="what-happened-to-my-mvc-project-visual-studio-azure-active-directory-connected-service"></a>MVC projem 'e ne oldu (Visual Studio Azure Active Directory bağlı hizmeti)?

> [!div class="op_single_selector"]
> - [Başlarken](vs-active-directory-dotnet-getting-started.md)
> - [Ne oldu](vs-active-directory-dotnet-what-happened.md)

Bu makalede ASP.NET MVC proje eklerken tam değişikliklerinin tanımlayan [Azure Active Directory bağlı Visual Studio kullanarak hizmet](vs-active-directory-add-connected-service.md).

Bağlı hizmetle çalışma hakkında daha fazla bilgi için bkz. [](vs-active-directory-dotnet-getting-started.md)Başlarken.

## <a name="added-references"></a>Eklenen başvurular

Proje dosyasını *. NET başvuruları) ve `packages.config` (NuGet başvuruları) etkiler.

| Type | Başvuru |
| --- | --- |
| NET NuGet | Microsoft.IdentityModel.Protocol.Extensions |
| NET NuGet | Microsoft.Owin |
| NET NuGet | Microsoft.Owin.Host.SystemWeb |
| NET NuGet | Microsoft.Owin.Security |
| NET NuGet | Microsoft. Owin. Security. Cookies |
| NET NuGet | Microsoft.Owin.Security.OpenIdConnect |
| NET NuGet | Owın |
| .NET        | System.IdentityModel |
| NET NuGet | System.IdentityModel.Tokens.Jwt |
| .NET        | System.Runtime.Serialization |

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
| .NET        | Microsoft. IdentityModel. clients. ActiveDirectory. WindowsForms (yalnızca Visual Studio 2015) |
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
        <add key="ida:AADInstance" value="https://login.microsoftonline.com/" />
        <add key="ida:Domain" value="<your selected Azure domain>" />
        <add key="ida:TenantId" value="<the Id of your selected Azure AD tenant>" />
        <add key="ida:PostLogoutRedirectUri" value="<project start page, such as https://localhost:44335>" />
    </appSettings>
    ```

- `<dependentAssembly>` `<runtime><assemblyBinding>` Ve için`System.IdentityModel.Tokens.Jwt`düğümününaltınaöğeeklendi. `Microsoft.IdentityModel.Protocol.Extensions`

**Dizin verilerini oku** seçeneğini belirlediyseniz ek değişiklikler:

- Aşağıdaki yapılandırma girişi altına `<appSettings>`eklendi:

    ```xml
    <add key="ida:ClientSecret" value="<Azure AD app's new client secret>" />
    ```

- Aşağıdaki öğeler altına `<configuration>`eklendi; proje-MDF-File ve proje-Catalog-id değerleri değişir:

    ```xml
    <configSections>
      <!-- For more information on Entity Framework configuration, visit https://go.microsoft.com/fwlink/?LinkID=237468 -->
      <section name="entityFramework" type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=6.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" requirePermission="false" />
    </configSections>

    <connectionStrings>
      <add name="DefaultConnection" connectionString="Data Source=(localdb)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\<project-mdf-file>.mdf;Initial Catalog=<project-catalog-id>;Integrated Security=True" providerName="System.Data.SqlClient" />
    </connectionStrings>

    <entityFramework>
      <defaultConnectionFactory type="System.Data.Entity.Infrastructure.LocalDbConnectionFactory, EntityFramework">
        <parameters>
          <parameter value="mssqllocaldb" />
        </parameters>
      </defaultConnectionFactory>
      <providers>
        <provider invariantName="System.Data.SqlClient" type="System.Data.Entity.SqlServer.SqlProviderServices, EntityFramework.SqlServer" />
      </providers>
    </entityFramework>
    ```

- `<dependentAssembly>` `<runtime><assemblyBinding>` ,`Microsoft.Data.Services.Client`Ve için düğümü altına eklenen öğeler. `Microsoft.Data.Edm` `Microsoft.Data.OData`

## <a name="code-changes-and-additions"></a>Kod değişiklikleri ve eklemeler

- `[Authorize]` Özniteliğivediğer`Controllers/HomeController.cs` mevcut denetleyicilere eklendi.

- Azure AD kimlik doğrulaması için başlangıç `App_Start/Startup.Auth.cs`mantığını içeren bir kimlik doğrulama başlangıç sınıfı eklendi. **Dizin verilerini oku** seçeneğini belirlediyseniz, bu dosya Ayrıca bir OAuth kodu alacak kodu içerir ve bir erişim belirteci için onu değiş tokuş sağlar.

- `Controllers/AccountController.cs`, `SignIn` Ve yöntemleri`SignOut` içeren bir denetleyici sınıfı eklendi.

- Ve `Views/Shared/_LoginPartial.cshtml` `SignIn` için bireylembağlantısıiçerenkısmibirgörünümeklendi.`SignOut`

- Oturum kapatma Kullanıcı arabirimi için `Views/Account/SignoutCallback.cshtml`HTML içeren kısmi bir görünüm eklendi.

- Sınıfı zaten mevcutsa öğesine `ConfigureAuth(app)` bir çağrı eklemek için `Startup` yöntemigüncelleştirildi;Aksihaldeyöntemiçağıranbirsınıfeklediniz.`Startup.Configuration`

- Bağlı `Connected Services/AzureAD/ConnectedService.json` hizmetin eklenmesini izlemek için Visual Studio `Service References/Azure AD/ConnectedService.json` tarafından kullanılan bilgileri içeren (Visual Studio 2017) veya (Visual Studio 2015) eklendi.

- **Dizin verilerini oku** seçeneğini seçtiyseniz, `Models/ADALTokenCache.cs` `Models/ApplicationDbContext.cs` belirteç önbelleğe almayı destekler. Ayrıca, Azure Graph API 'leri kullanarak Kullanıcı profili bilgilerine erişimi göstermek için ek bir denetleyici ve Görünüm `Controllers/UserProfileController.cs`de `Views/UserProfile/Index.cshtml`eklenmiştir:, ve`Views/UserProfile/Relogin.cshtml`

### <a name="file-backup-visual-studio-2015"></a>Dosya yedekleme (Visual Studio 2015)

Bağlı hizmet eklenirken, Visual Studio 2015, değiştirilen ve kaldırılan dosyaları yedekler. Etkilenen tüm dosyalar klasörüne `Backup/AzureAD`kaydedilir. Visual Studio 2017 ve üzeri yedeklemeler oluşturmaz.

- `Startup.cs`
- `App_Start\IdentityConfig.cs`
- `App_Start\Startup.Auth.cs`
- `Controllers\AccountController.cs`
- `Controllers\ManageController.cs`
- `Models\IdentityModels.cs`
- `Models\ManageViewModels.cs`
- `Views\Shared\_LoginPartial.cshtml`

## <a name="changes-on-azure"></a>Azure 'daki değişiklikler

- Bağlı hizmeti eklerken seçtiğiniz etki alanında bir Azure AD uygulaması oluşturuldu.
- Uygulama, bu seçenek seçildiyse, **Dizin verilerini oku** iznini içerecek şekilde güncelleştirildi.

[Azure Active Directory hakkında daha fazla bilgi edinin](https://azure.microsoft.com/services/active-directory/).

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Active Directory için kimlik doğrulama senaryoları](authentication-scenarios.md)
- [Microsoft 'a bir ASP.NET Web uygulamasına oturum açma ekleme](quickstart-v1-aspnet-webapp.md)
