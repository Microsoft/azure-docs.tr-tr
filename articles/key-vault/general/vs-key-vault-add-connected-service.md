---
title: Visual Studio kullanarak ASP.NET projenize Key Vault desteği ekleme-Azure Key Vault | Microsoft Docs
description: Bir ASP.NET veya ASP.NET Core Web uygulamasına Key Vault desteğinin nasıl ekleneceğini öğrenmenize yardımcı olması için bu öğreticiyi kullanın.
services: key-vault
author: ghogen
manager: jillfra
ms.service: key-vault
ms.custom: vs-azure, devx-track-csharp
ms.topic: how-to
ms.date: 08/07/2019
ms.author: ghogen
ms.openlocfilehash: 9c62534acdbfbff7fd4e718bad1f07a92c641626
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92792404"
---
# <a name="add-key-vault-to-your-web-application-by-using-visual-studio-connected-services"></a>Visual Studio bağlı hizmetler 'i kullanarak Web uygulamanıza Key Vault ekleme

Bu öğreticide, ASP.NET Core veya herhangi bir tür ASP.NET projesi kullanıp kullanmayacağınızı, Visual Studio 'da Web projeleri için gizli dizileri yönetmek üzere Azure Key Vault kullanmaya başlamak için gereken her şeyi kolayca eklemeyi öğreneceksiniz. Visual Studio 'daki bağlı hizmetler özelliğini kullanarak, Visual Studio 'Nun Azure 'daki Key Vault bağlanmak için ihtiyacınız olan tüm NuGet paketlerini ve yapılandırma ayarlarını otomatik olarak eklemesini sağlayabilirsiniz.

Bağlı hizmetlerin projenizde Key Vault sağlamak üzere yaptığı değişikliklerle ilgili ayrıntılar için, bkz. [Key Vault bağlı hizmet-ASP.NET 4.7.1 projem 'A ne oldu](#how-your-aspnet-framework-project-is-modified) veya [Key Vault bağlı Service-ASP.NET Core projem Için ne oldu?](#how-your-aspnet-core-project-is-modified).

## <a name="prerequisites"></a>Önkoşullar

- **Bir Azure aboneliği**. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/pricing/free-trial/)için kaydolun.
- **Visual Studio 2019 sürüm 16,3** veya sonraki sürümleri [Şimdi indirin](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).


## <a name="add-key-vault-support-to-your-project"></a>Projenize Key Vault desteği ekleyin

Başlamadan önce, Visual Studio 'da oturum açtığınızdan emin olun. Azure aboneliğiniz için kullandığınız hesapla oturum açın. Sonra bir ASP.NET 4.7.1 veya üzeri veya ASP.NET Core 2,0 Web projesi açın ve aşağıdaki adımları uygulayın:

1. **Çözüm Gezgini**' de, Key Vault desteğini eklemek istediğiniz projeye sağ tıklayın ve   >  **bağlı hizmet**  >  **Ekle** Ekle ' yi seçin.
   Projenize ekleyebileceğiniz hizmetlerle birlikte Bağlı Hizmet sayfası görüntülenir.
1. Kullanılabilir hizmetler menüsünde **Azure Key Vault** öğesini seçin ve **İleri**' ye tıklayın.

   !["Azure Key Vault" öğesini seçin](../media/vs-key-vault-add-connected-service/key-vault-connected-service.png)

1. Kullanmak istediğiniz aboneliği seçin ve ardından mevcut bir Key Vault seçip **son**' a tıklayın. 

   ![Aboneliğinizi seçin](../media/vs-key-vault-add-connected-service/key-vault-connected-service-select-vault.png)

Şimdi Key Vault bağlantısı oluşturulur ve kod içinde gizli dizilerimize erişebilirsiniz. Sonraki adımlar, ASP.NET 4.7.1 veya ASP.NET Core kullanıyor olmanıza bağlı olarak farklılık belirtir.

## <a name="access-your-secrets-in-code-aspnet-core"></a>Koddaki gizli dizilerinizdeki erişimi (ASP.NET Core)

1. *Dizin. cshtml. cs* gibi sayfa dosyalarından birini açın ve aşağıdaki kodu yazın:
   1. Bu using yönergesini kullanarak bir başvuru ekleyin `Microsoft.Extensions.Configuration` :

       ```csharp
       using Microsoft.Extensions.Configuration;
       ```

   1. Yapılandırma değişkenini ekleyin.

      ```csharp
      private static IConfiguration _configuration;
      ```

   1. Bu oluşturucuyu ekleyin veya var olan oluşturucuyu bununla değiştirin:

       ```csharp
       public IndexModel(IConfiguration configuration)
       {
           _configuration = configuration;
       }
       ```

   1. `OnGet` yöntemini güncelleştirin. Burada gösterilen yer tutucu değerini yukarıdaki komutlarda oluşturduğunuz parola adıyla güncelleştirin.

       ```csharp
       public void OnGet()
       {
           ViewData["Message"] = "My key val = " + _configuration["<YourSecretNameStoredInKeyVault>"];
       }
       ```

   1. Çalışma zamanında değeri doğrulamak için, `ViewData["Message"]` parolayı bir ileti içinde göstermek üzere *. cshtml* dosyasına görüntülenecek kodu ekleyin.

      ```cshtml
          <p>@ViewData["Message"]</p>
      ```

Parolanın Key Vault başarıyla alınmadığını doğrulamak için uygulamayı yerel olarak çalıştırabilirsiniz.

## <a name="access-your-secrets-aspnet"></a>Gizli dizilerinizdeki erişimi (ASP.NET)
Yapılandırmayı, web.config dosyası `appSettings` çalışma zamanında true değeri ile değiştirilmiş öğede bir kukla değere sahip olacak şekilde ayarlayabilirsiniz. Daha sonra bunu veri yapısı aracılığıyla erişebilirsiniz `ConfigurationManager.AppSettings` .

1. Çözüm Gezgini, projenize sağ tıklayın ve NuGet Paketlerini Yönet ' i seçin. Araştır sekmesinde [Microsoft.Configuration.ConfigUrationoluşturucular. Azure](https://www.nuget.org/packages/Microsoft.Configuration.ConfigurationBuilders.Azure/) ' u bulun ve çalıştırın.
 
1. web.config dosyanızı açın ve aşağıdaki kodu yazın:
    1. Ekle `configSections` ve `configBuilders` şunları yapın:
        ```xml
         <configSections>
            <section
                name="configBuilders"
                type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a"
                restartOnExternalChanges="false"
                requirePermission="false" />
         </configSections>
         <configBuilders>
            <builders>
            <add
                    name="AzureKeyVault"
                    vaultName="vaultname"
                    type="Microsoft.Configuration.ConfigurationBuilders.AzureKeyVaultConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Azure, Version=1.0.0.0, Culture=neutral"
                    vaultUri="https://vaultname.vault.azure.net" />
            </builders>
         </configBuilders>
        ```
    1. AppSettings etiketini bulun, bir öznitelik ekleyin `configBuilders="AzureKeyVault"` ve bir satır ekleyin:
        ```xml
         <add key="<secretNameInYourKeyVault>" value="dummy"/>
        ```

1. `About`Onay değerini göstermek Için *HomeController. cs* içindeki yöntemi düzenleyin.

   ```csharp
   public ActionResult About()
   {
       ViewBag.Message = "Key vault value = " + ConfigurationManager.AppSettings["<secretNameInYourKeyVault>"];
   }
   ```
1. Uygulamayı hata ayıklayıcı altında yerel olarak çalıştırın, **hakkında** sekmesine geçin ve Key Vault değerin görüntülendiğini doğrulayın.

## <a name="troubleshooting"></a>Sorun giderme

Key Vault, Visual Studio 'da oturum açtığınızdan farklı bir Microsoft hesabı çalışıyorsa (örneğin, Key Vault iş hesabınızda çalışıyor, ancak Visual Studio özel hesabınızı kullanıyorsa), Visual Studio 'nun Key Vault erişim izni alamazsanız program. cs dosyasında bir hata alırsınız. Bu sorunu düzeltmek için:

1. [Azure Portal](https://portal.azure.com) gidin ve Key Vault açın.

1. **Erişim ilkeleri**' ni seçin, sonra **erişim ilkesi ekleyin** ve oturum açtığınız hesabı sorumlu olarak seçin.

1. Visual Studio 'da **Dosya**  >  **hesabı ayarları**' nı seçin.
**Tüm hesap** bölümünden **Hesap Ekle** ' yi seçin. Erişim ilkenizin sorumlusu olarak seçtiğiniz hesapla oturum açın.

1. **Araçlar**  >  **Seçenekler**' i seçin ve **Azure hizmeti kimlik doğrulaması**' nı arayın. Ardından, Visual Studio 'ya yeni eklediğiniz hesabı seçin.

Artık uygulamanızda hata ayıklarken, Visual Studio Key Vault bulunduğu hesaba bağlanır.

## <a name="how-your-aspnet-core-project-is-modified"></a>ASP.NET Core projeniz nasıl değiştirilir

Bu bölümde, Key Vault bağlı hizmeti Visual Studio kullanılarak eklenirken bir ASP.NET projesinde yapılan tam değişiklikler tanımlanmaktadır.

### <a name="added-references-for-aspnet-core"></a>ASP.NET Core için başvurular eklendi

, .NET başvuruları ve NuGet paket başvurularını proje dosyası etkiler.

| Tür | Başvuru |
| --- | --- |
| NuGet | Microsoft. AspNetCore. AzureKeyVault. HostingStartup |

### <a name="added-files-for-aspnet-core"></a>ASP.NET Core için dosyalar eklendi

- `ConnectedService.json` Bağlı hizmet sağlayıcısı, sürüm ve belge bağlantısıyla ilgili bazı bilgileri kaydeden eklendi.

### <a name="project-file-changes-for-aspnet-core"></a>ASP.NET Core için proje dosyası değişiklikleri

- Bağlı hizmetler ItemGroup ve dosyası eklendi `ConnectedServices.json` .

### <a name="launchsettingsjson-changes-for-aspnet-core"></a>ASP.NET Core değişikliklerle ilgili launchsettings.js

- Hem IIS Express profile hem de Web proje adınızla eşleşen profile aşağıdaki ortam değişkeni girdileri eklendi:

    ```json
      "environmentVariables": {
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONENABLED": "true",
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONVAULT": "<your keyvault URL>"
      }
    ```

### <a name="changes-on-azure-for-aspnet-core"></a>ASP.NET Core için Azure 'da yapılan değişiklikler

- Bir kaynak grubu oluşturdunuz (veya mevcut bir tane kullanıldı).
- Belirtilen kaynak grubunda bir Key Vault oluşturuldu.

## <a name="how-your-aspnet-framework-project-is-modified"></a>ASP.NET Framework projeniz nasıl değiştirilir

Bu bölümde, Key Vault bağlı hizmeti Visual Studio kullanılarak eklenirken bir ASP.NET projesinde yapılan tam değişiklikler tanımlanmaktadır.

### <a name="added-references-for-aspnet-framework"></a>ASP.NET Framework için başvurular eklendi

, Proje dosyası .NET başvurularını ve `packages.config` (NuGet başvuruları) etkiler.

| Tür | Başvuru |
| --- | --- |
| NET NuGet | Azure. Identity |
| NET NuGet | Azure. Security. Keykasa. Keys |
| NET NuGet | Azure. Security. Keykasa. gizli dizileri |

> [!IMPORTANT] 
> Varsayılan olarak, Azure. Identity 1.1.1, Visual Studio kimlik bilgisini desteklemeyen yüklüdür. Paket başvurusunu 1.2 + ile Visual Studio kimlik bilgilerini kullanacak şekilde el ile güncelleştirebilirsiniz.

### <a name="added-files-for-aspnet-framework"></a>ASP.NET Framework için dosyalar eklendi

- `ConnectedService.json` Bağlı hizmet sağlayıcısı, sürümü ve belgelerinin bağlantısı hakkında bazı bilgileri kaydeden eklendi.

### <a name="project-file-changes-for-aspnet-framework"></a>ASP.NET Framework için proje dosya değişiklikleri

- Bağlı hizmetler ItemGroup ve dosyaya ConnectedServices.jseklendi.
- [Eklenen başvurular](#added-references-for-aspnet-framework) bölümünde açıklanan .NET derlemelerine başvurular.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticiyi izlediyseniz, Key Vault izinleriniz kendi Azure aboneliğinizle çalışacak şekilde ayarlanır, ancak bu, bir üretim senaryosu için istenmeyebilir. Uygulamanıza yönelik Key Vault erişimini yönetmek için yönetilen bir kimlik oluşturabilirsiniz. [Key Vault Için kimlik doğrulama](./authentication.md) ve [Key Vault erişim ilkesi atama](./assign-access-policy-portal.md)konusuna bakın.

[Key Vault geliştirici kılavuzunu](developers-guide.md)okuyarak Key Vault geliştirme hakkında daha fazla bilgi edinin.