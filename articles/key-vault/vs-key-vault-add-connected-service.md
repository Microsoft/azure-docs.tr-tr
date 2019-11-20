---
title: Visual Studio kullanarak ASP.NET projenize Key Vault desteği ekleme-Azure Key Vault | Microsoft Docs
description: Bir ASP.NET veya ASP.NET Core Web uygulamasına Key Vault desteğinin nasıl ekleneceğini öğrenmenize yardımcı olması için bu öğreticiyi kullanın.
services: key-vault
author: ghogen
manager: jillfra
ms.service: key-vault
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: ghogen
ms.openlocfilehash: 4cbc4044b5d1270cecd1a271d2a1db02801650dd
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74012763"
---
# <a name="add-key-vault-to-your-web-application-by-using-visual-studio-connected-services"></a>Visual Studio bağlı hizmetler 'i kullanarak Web uygulamanıza Key Vault ekleme

Bu öğreticide, ASP.NET Core veya herhangi bir tür ASP.NET projesi kullanıp kullanmayacağınızı, Visual Studio 'da Web projeleri için gizli dizileri yönetmek üzere Azure Key Vault kullanmaya başlamak için gereken her şeyi kolayca eklemeyi öğreneceksiniz. Visual Studio 'daki bağlı hizmetler özelliğini kullanarak, Visual Studio 'Nun Azure 'daki Key Vault bağlanmak için ihtiyacınız olan tüm NuGet paketlerini ve yapılandırma ayarlarını otomatik olarak eklemesini sağlayabilirsiniz.

Bağlı hizmetlerin projenizde Key Vault sağlamak üzere yaptığı değişikliklerle ilgili ayrıntılar için, bkz. [Key Vault bağlı hizmet-ASP.NET 4.7.1 projem 'A ne oldu](#how-your-aspnet-framework-project-is-modified) veya [Key Vault bağlı Service-ASP.NET Core projem Için ne oldu?](#how-your-aspnet-core-project-is-modified).

## <a name="prerequisites"></a>Önkoşullar

- **Bir Azure aboneliği**. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/pricing/free-trial/)için kaydolun.
- **Web geliştirme** iş yükü yüklüyken **Visual studio 2019 sürüm 16,3 Preview 1** veya üzeri ya da **Visual Studio 2017 sürüm 15,7** . [Şimdi indir](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).
- Visual Studio 2017 ile ASP.NET (Core değil) için, varsayılan olarak yüklenmeyen .NET Framework 4.7.1 veya sonraki bir geliştirme araçlarına sahip olmanız gerekir. Bunları yüklemek için Visual Studio Yükleyicisi başlatın, **Değiştir**' i seçin ve ardından **tek tek bileşenler**' i seçin, ardından sağ tarafta **ASP.net ve Web geliştirme**' yi genişletin ve **.NET Framework 4.7.1 geliştirme araçları ' nı seçin.** .
- Bir ASP.NET 4.7.1 veya üzeri ya da ASP.NET Core 2,0 Web projesi açık.

## <a name="add-key-vault-support-to-your-project"></a>Projenize Key Vault desteği ekleyin

Başlamadan önce, Visual Studio 'da oturum açtığınızdan emin olun. Azure aboneliğiniz için kullandığınız hesapla oturum açın. Sonra bir ASP.NET 4.7.1 veya üzeri veya ASP.NET Core 2,0 Web projesi açın ve aşağıdaki adımları uygulayın:

1. **Çözüm Gezgini**' de, Anahtar Kasası desteğini eklemek istediğiniz projeye sağ tıklayın ve > **bağlı hizmet** **Ekle** ' yi seçin.
   Projenize ekleyebileceğiniz hizmetlerle birlikte Bağlı Hizmet sayfası görüntülenir.
1. Kullanılabilir hizmetler menüsünde **Azure Key Vault Ile güvenli gizli**dizileri seçin.

   !["Azure Key Vault Ile güvenli gizli dizileri" seçin](media/vs-key-vault-add-connected-service/KeyVaultConnectedService1.PNG)

1. Kullanmak istediğiniz aboneliği seçin ve ardından yeni veya mevcut bir Key Vault seçin. Yeni Key Vault seçerseniz bir **düzenleme** bağlantısı görüntülenir. Yeni Key Vault yapılandırmak için seçin.

   ![Aboneliğinizi seçme](media/vs-key-vault-add-connected-service/key-vault-connected-service-select-vault.png)

1. **Azure Key Vault Düzenle**' de, Key Vault için kullanmak istediğiniz adı girin.

1. Var olan bir **kaynak grubunu**seçin veya otomatik olarak oluşturulan benzersiz bir adla yeni bir tane oluşturmak için seçin.  Farklı bir ada sahip yeni bir grup oluşturmak istiyorsanız, [Azure Portal](https://portal.azure.com)kullanabilir ve sonra da sayfayı kapatıp yeniden başlatarak kaynak grupları listesini yeniden yükleyebilirsiniz.
1. Key Vault oluşturulacağı **konumu** seçin. Web uygulamanız Azure 'da barındırılıyorsa, en iyi performans için Web uygulamasını barındıran bölgeyi seçin.
1. **Fiyatlandırma katmanı**seçin. Ayrıntılar için bkz. [Key Vault fiyatlandırması](https://azure.microsoft.com/pricing/details/key-vault/).
1. Yapılandırma seçimlerini kabul etmek için **Tamam ' ı** seçin.
1. Mevcut bir Key Vault seçtikten veya yeni bir Key Vault yapılandırdıktan sonra, bağlı hizmeti eklemek için Visual Studio 'nun **Azure Key Vault** sekmesinde **Ekle** ' yi seçin.
1. Key Vault için **gizli** dizi sayfasını açmak üzere **Bu Key Vault depolanan gizli dizileri Yönet** bağlantısını seçin. Sayfayı veya projeyi kapattıysanız, **tüm hizmetler** ' i seçip **güvenlik**' in altında **Key Vault**' i seçip Key Vault ' yı seçerek [Azure Portal](https://portal.azure.com) buna gidebilirsiniz.
1. Oluşturduğunuz anahtar kasasının Key Vault bölümünde **gizli**dizileri, sonra **Oluştur/içeri aktar**' ı seçin.

   ![Gizli dizi oluştur/Içeri aktar](media/vs-key-vault-add-connected-service/azure-generate-secrets.png)

1. *MySecret* gibi bir gizli dizi girin ve bir test olarak herhangi bir dize değeri verin, **Oluştur** düğmesini seçin.

   ![Gizli anahtar oluşturma](media/vs-key-vault-add-connected-service/azure-create-a-secret.png)

1. seçim Başka bir gizli dizi girin, ancak bu süre, bu kez gizli anahtar adlandırarak bir kategoriye konur *--MySecret*. Bu söz dizimi, gizli dizi "MySecret" içeren bir "gizli dizi" kategorisini belirtir.

Artık kod içinde gizli dizilerimize erişebilirsiniz. Sonraki adımlar, ASP.NET 4.7.1 veya ASP.NET Core kullanıyor olmanıza bağlı olarak farklılık belirtir.

## <a name="access-your-secrets-in-code"></a>Koddaki gizli dizilerinizdeki verilerinize erişin

1. Çözüm Gezgini, projenize sağ tıklayın ve **NuGet Paketlerini Yönet**' i seçin. **Araştır** sekmesinde şu iki NuGet paketini bulun ve yükleme: [Microsoft. Azure. Services. appauthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) ve [Microsoft. Azure. keykasası](https://www.nuget.org/packages/Microsoft.Azure.KeyVault).

1. `Program.cs` sekmesini seçin ve program sınıfını aşağıdaki kodla değiştirin:

   ```csharp
    public class Program
    {
        public static void Main(string[] args)
        {
            BuildWebHost(args).Run();
        }

        public static IWebHost BuildWebHost(string[] args) =>
           WebHost.CreateDefaultBuilder(args)
               .ConfigureAppConfiguration((ctx, builder) =>
               {
                   var keyVaultEndpoint = GetKeyVaultEndpoint();
                   if (!string.IsNullOrEmpty(keyVaultEndpoint))
                   {
                       var azureServiceTokenProvider = new AzureServiceTokenProvider();
                       var keyVaultClient = new KeyVaultClient(
                           new KeyVaultClient.AuthenticationCallback(
                               azureServiceTokenProvider.KeyVaultTokenCallback));
                       builder.AddAzureKeyVault(
                           keyVaultEndpoint, keyVaultClient, new DefaultKeyVaultSecretManager());
                   }
               }
            ).UseStartup<Startup>()
             .Build();

        private static string GetKeyVaultEndpoint() => "https://<YourKeyVaultName>.vault.azure.net";
    }
   ```

1. Daha sonra `About.cshtml.cs` dosyası açın ve aşağıdaki kodu yazın:
   1. Bu using ifadesiyle `Microsoft.Extensions.Configuration` bir başvuru ekleyin:

       ```csharp
       using Microsoft.Extensions.Configuration
       ```

   1. Bu oluşturucuyu ekleyin:

       ```csharp
       public AboutModel(IConfiguration configuration)
       {
           _configuration = configuration;
       }
       ```

   1. `OnGet` yöntemini güncelleştirin. Burada gösterilen yer tutucu değerini yukarıdaki komutlarda oluşturduğunuz parola adıyla güncelleştirin.

       ```csharp
       public void OnGet()
       {
           //Message = "Your application description page.";
           Message = "My key val = " + _configuration["<YourSecretNameThatWasCreatedAbove>"];
       }
       ```

Hakkında sayfasına giderek uygulamayı yerel olarak çalıştırın. Gizli değerinin alındığını görmeniz gerekir.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse kaynak grubunu silin. Bu, Key Vault ve ilgili kaynakları siler. Kaynak grubunu portal aracılığıyla silmek için:

1. Portalın üst kısmındaki Arama kutusuna kaynak grubunuzun adını girin. Bu hızlı başlangıçta kullanılan kaynak grubunu arama sonuçlarında gördüğünüzde seçin.
2. **Kaynak grubunu sil**'i seçin.
3. **Kaynak grubu adını yazın:** kutusuna kaynak grubunun adını girin ve **Sil**' i seçin.

## <a name="troubleshooting"></a>Sorun giderme

Anahtar kasanızın, Visual Studio 'da oturum açtığınızdan farklı bir Microsoft hesabı çalışıyorsa (örneğin, Anahtar Kasası iş hesabınızda çalışıyorsa, ancak Visual Studio özel hesabınızı kullanıyorsa) Program.cs dosyanızda bir hata alırsınız , bu Visual Studio anahtar kasasına erişim alamıyor. Bu sorunu düzeltmek için:

1. [Azure Portal](https://portal.azure.com) gidin ve Key Vault açın.

1. **Erişim ilkeleri**' ni seçin, sonra **erişim ilkesi ekleyin**ve oturum açtığınız hesabı sorumlu olarak seçin.

1. Visual Studio 'da **dosya** > **Hesap ayarları**' nı seçin.
**Tüm hesap** bölümünden **Hesap Ekle** ' yi seçin. Erişim ilkenizin sorumlusu olarak seçtiğiniz hesapla oturum açın.

1. **Araçlar** > **Seçenekler**' i seçin ve **Azure hizmeti kimlik doğrulaması**' nı arayın. Ardından, Visual Studio 'ya yeni eklediğiniz hesabı seçin.

Artık uygulamanızda hata ayıklarken, Visual Studio anahtar kasanızın bulunduğu hesaba bağlanır.

## <a name="how-your-aspnet-core-project-is-modified"></a>ASP.NET Core projeniz nasıl değiştirilir

Bu bölümde, Key Vault bağlı hizmeti Visual Studio kullanılarak eklenirken bir ASP.NET projesinde yapılan tam değişiklikler tanımlanmaktadır.

### <a name="added-references-for-aspnet-core"></a>ASP.NET Core için başvurular eklendi

, .NET başvuruları ve NuGet paket başvurularını proje dosyası etkiler.

| Tür | Başvuru |
| --- | --- |
| NuGet | Microsoft. AspNetCore. AzureKeyVault. HostingStartup |

### <a name="added-files-for-aspnet-core"></a>ASP.NET Core için dosyalar eklendi

- Bağlı hizmet sağlayıcısı, sürüm ve belge bağlantısıyla ilgili bazı bilgileri kaydeden `ConnectedService.json` eklendi.

### <a name="project-file-changes-for-aspnet-core"></a>ASP.NET Core için proje dosyası değişiklikleri

- Bağlı hizmetler ItemGroup ve `ConnectedServices.json` dosyası eklendi.

### <a name="launchsettingsjson-changes-for-aspnet-core"></a>launchsettings. JSON değişiklikleri ASP.NET Core için

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
| NET NuGet | Microsoft. Azure. Keykasası |
| NET NuGet | Microsoft. Azure. Keykasası. WebKey |
| NET NuGet | Microsoft.Rest.ClientRuntime |
| NET NuGet | Microsoft.Rest.ClientRuntime.Azure |

### <a name="added-files-for-aspnet-framework"></a>ASP.NET Framework için dosyalar eklendi

- Bağlı hizmet sağlayıcısıyla ilgili bazı bilgileri, sürümü ve belgelerin bağlantısını kaydeden `ConnectedService.json` eklendi.

### <a name="project-file-changes-for-aspnet-framework"></a>ASP.NET Framework için proje dosya değişiklikleri

- Bağlı hizmetler ItemGroup ve ConnectedServices. JSON dosyası eklendi.
- [Eklenen başvurular](#added-references-for-aspnet-framework) bölümünde açıklanan .NET derlemelerine başvurular.

### <a name="webconfig-or-appconfig-changes"></a>Web. config veya App. config değişiklikleri

- Aşağıdaki yapılandırma girdileri eklendi:

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

### <a name="changes-on-azure-for-aspnet-framework"></a>ASP.NET Framework için Azure değişiklikleri

- Bir kaynak grubu oluşturdunuz (veya mevcut bir tane kullanıldı).
- Belirtilen kaynak grubunda bir Key Vault oluşturuldu.

## <a name="next-steps"></a>Sonraki adımlar

[Key Vault geliştirici kılavuzunu](key-vault-developers-guide.md)okuyarak Key Vault geliştirme hakkında daha fazla bilgi edinin.
