---
title: Azure yönetilen kimliklerle tümleştirin | Microsoft Docs
description: Azure yönetilen kimliklerini kullanarak kimlik doğrulaması yapma ve Azure Uygulama yapılandırmasına erişim kazanma hakkında bilgi edinin
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: na
ms.topic: conceptual
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: 3a5517c31cdac0bf6f5ea386a8614d15521d4479
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72035542"
---
# <a name="integrate-with-azure-managed-identities"></a>Azure yönetilen kimliklerle tümleştirin

Azure Active Directory [Yönetilen kimlikler](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) , bulut uygulamanız için gizli dizi yönetimini basitleştirmeye yardımcı olur. Yönetilen bir kimlikle kodunuzu, üzerinde çalıştığı Azure işlem hizmeti için oluşturulan hizmet sorumlusunu kullanacak şekilde ayarlayabilirsiniz. Azure Key Vault veya yerel bağlantı dizesinde depolanan ayrı kimlik bilgileri yerine yönetilen bir kimlik kullanırsınız. 

Azure Uygulama yapılandırması ve .NET Core, .NET ve Java Spring istemci kitaplıkları, içinde yerleşik olarak bulunan yönetilen hizmet kimliği (MSI) desteğiyle birlikte gelir. Bunu kullanmanız gerekmese de, MSI, gizli dizileri içeren bir erişim belirteci gereksinimini ortadan kaldırır. Kodunuz, yalnızca hizmet uç noktasını kullanarak uygulama yapılandırma deposuna erişebilir. Bu URL 'YI, herhangi bir parolayı açığa çıkarmak gerekmeden doğrudan kodunuza katıştırabilirsiniz.

Bu öğreticide, uygulama yapılandırmasına erişmek için MSI 'den nasıl yararlanabilmeniz gösterilmektedir. Hızlı başlangıçlarda tanıtılan web uygulamasında oluşturulur. Devam etmeden önce, önce [uygulama yapılandırması ile bir ASP.NET Core uygulaması oluşturun](./quickstart-aspnet-core-app.md) .

Ayrıca, bu öğretici isteğe bağlı olarak, MSI 'yi uygulama yapılandırmasının Key Vault başvurularıyla birlikte nasıl kullanabileceğinizi gösterir. Bu, Key Vault depolanan gizli dizileri ve uygulama yapılandırmasındaki yapılandırma değerlerini sorunsuz bir şekilde erişmenizi sağlar. Bu özelliği araştırmak isterseniz, önce [ASP.NET Core Key Vault başvuruları kullanın](./use-key-vault-references-dotnet-core.md) .

Bu öğreticideki adımları uygulamak için herhangi bir kod düzenleyicisi kullanabilirsiniz. [Visual Studio Code](https://code.visualstudio.com/) , Windows, MacOS ve Linux platformlarında kullanılabilen harika bir seçenektir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Uygulama yapılandırmasına yönetilen bir kimlik erişimi verin.
> * Uygulamanızı, uygulama yapılandırmasına bağlandığınızda yönetilen bir kimlik kullanacak şekilde yapılandırın.
> * İsteğe bağlı olarak, uygulama yapılandırma Key Vault başvurusu aracılığıyla Key Vault bağlandığınızda Uygulamanızı yönetilen bir kimlik kullanacak şekilde yapılandırın.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için aşağıdakiler gereklidir:

* [.NET Core SDK](https://www.microsoft.com/net/download/windows).
* [Azure Cloud Shell yapılandırıldı](https://docs.microsoft.com/azure/cloud-shell/quickstart).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="add-a-managed-identity"></a>Yönetilen kimlik ekleme

Portalda yönetilen bir kimlik ayarlamak için ilk olarak bir uygulama oluşturun ve sonra özelliği etkinleştirin.

1. [Azure Portal](https://portal.azure.com) , normalde yaptığınız gibi bir App Services örneği oluşturun. Portalda buna gidin.

2. Sol bölmedeki **Ayarlar** grubuna aşağı kaydırın ve **kimlik**' i seçin.

3. **Sistem atandı** sekmesinde **durumu** **Açık** olarak değiştirin ve **Kaydet**' i seçin.

4. Sistem tarafından atanan yönetilen kimliği etkinleştirmek isteyip istemediğiniz sorulduğunda **Evet** yanıtını verin.

    ![App Service yönetilen kimliği ayarlama](./media/set-managed-identity-app-service.png)

## <a name="grant-access-to-app-configuration"></a>Uygulama yapılandırmasına erişim izni verme

1. [Azure Portal](https://portal.azure.com), **tüm kaynaklar** ' ı seçin ve hızlı başlangıçta oluşturduğunuz uygulama yapılandırma deposunu seçin.

1. **Erişim denetimi (IAM)** öğesini seçin.

1. **Erişim denetimi** sekmesinde, **rol atama** kartı Kullanıcı arabiriminde **Ekle** ' yi seçin.

1. **Rol**altında **katkıda bulunan**' ı seçin. **Erişim ata**' nın altında, **sistem tarafından atanan yönetilen kimlik**altında **App Service** ' yi seçin.

1. **Abonelik**altında Azure aboneliğinizi seçin. Uygulamanız için App Service kaynağını seçin.

1. **Kaydet**’i seçin.

    ![Yönetilen kimlik ekleme](./media/add-managed-identity.png)

1. İsteğe bağlı: Key Vault erişim vermek istiyorsanız, [yönetilen bir kimlikle Key Vault kimlik doğrulaması sağlama](https://docs.microsoft.com/azure/key-vault/managed-identity)bölümündeki yönergeleri izleyin.

## <a name="use-a-managed-identity"></a>Yönetilen kimlik kullanma

1. Azure portal yapılandırma ekranına gidip **erişim tuşları** sekmesine tıklayarak uygulama yapılandırma DEPONUZDAKI URL 'yi bulun.

1. *AppSettings. JSON*dosyasını açın ve aşağıdaki betiği ekleyin. Parantez dahil *\<service_endpoint >* , uygulama yapılandırma deponuzu URL 'siyle değiştirin. 

    ```json
    "AppConfig": {
        "Endpoint": "<service_endpoint>"
    }
    ```

1. Yalnızca uygulama yapılandırmasında doğrudan depolanan değerlere erişmek istiyorsanız, *program.cs*' yi açın ve `CreateWebHostBuilder` metodunu `config.AddAzureAppConfiguration()` metodunu değiştirerek güncelleştirin.

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(options =>
                    options.ConnectWithManagedIdentity(settings["AppConfig:Endpoint"]));
            })
            .UseStartup<Startup>();
    ```

1. Uygulama yapılandırma değerlerinin yanı sıra Key Vault başvurular kullanmak istiyorsanız, *program.cs*açın ve `CreateWebHostBuilder` metodunu aşağıda gösterildiği gibi güncelleştirin. Bu, bir `AzureServiceTokenProvider` kullanarak yeni `KeyVaultClient` oluşturur ve bu başvuruyu `UseAzureKeyVault` yöntemine bir çağrıya geçirir.

    ```csharp
        public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    var settings = config.Build();
                    AzureServiceTokenProvider azureServiceTokenProvider = new AzureServiceTokenProvider();
                    KeyVaultClient kvClient = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
                    
                    config.AddAzureAppConfiguration(options => options.ConnectWithManagedIdentity(settings["AppConfig:Endpoint"])).UseAzureKeyVault(kvClient));
                })
                .UseStartup<Startup>();
    ```

    Artık diğer uygulama yapılandırma anahtarı gibi Key Vault başvurularına erişebilirsiniz. Yapılandırma sağlayıcısı, Key Vault için kimlik doğrulamak üzere yapılandırdığınız `KeyVaultClient` kullanır ve değeri alır.

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-from-local-git"></a>Yerel Git’ten dağıtım

Kudu yapı sunucusuyla uygulamanız için yerel git dağıtımını etkinleştirmenin en kolay yolu Azure Cloud Shell kullanmaktır.

### <a name="configure-a-deployment-user"></a>Dağıtım kullanıcısı yapılandırma

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="enable-local-git-with-kudu"></a>Kudu ile yerel git 'i etkinleştirme
Uygulamanız için yerel bir git deponuz yoksa, bir tane açmanız gerekir. Bunu yapmak için, uygulamanızın proje dizininden aşağıdaki komutları çalıştırın:

```cmd
git init
git add .
git commit -m "Initial version"
```

Kudu yapı sunucusuyla uygulamanız için yerel git dağıtımını etkinleştirmek üzere Cloud Shell [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git) ' i çalıştırın.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group <group_name>
```

Bunun yerine git özellikli bir uygulama oluşturmak için, Cloud Shell `--deployment-local-git` parametresiyle [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) ' i çalıştırın.

```azurecli-interactive
az webapp create --name <app_name> --resource-group <group_name> --plan <plan_name> --deployment-local-git
```

@No__t-0 komutu aşağıdaki çıktıya benzer bir işlem sağlar:

```json
Local git is configured with url of 'https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="deploy-your-project"></a>Projenizi dağıtın

_Yerel terminal penceresine_ dönüp yerel Git deponuza bir Azure uzak deposu ekleyin. _@No__t-1url >_ , [uygulamanız Için git 'i etkinleştirin](#enable-local-git-with-kudu)öğesinden aldığınız git uzak URL 'si ile değiştirin.

```bash
git remote add azure <url>
```

Aşağıdaki komutla uygulamanızı dağıtmak için Azure uzak deposuna gönderin. Bir parola istendiğinde, [dağıtım kullanıcısı yapılandırma](#configure-a-deployment-user)bölümünde oluşturduğunuz parolayı girin. Azure portal oturum açmak için kullandığınız parolayı kullanmayın.

```bash
git push azure master
```

Çıkışta ASP.NET için MSBuild, Node. js için `npm install` ve Python için `pip install` gibi çalışma zamanına özgü Otomasyon görebilirsiniz.

### <a name="browse-to-the-azure-web-app"></a>Azure web uygulamasına göz atma

İçeriğin dağıtıldığını doğrulamak için tarayıcı kullanarak Web uygulamanıza gidin.

```bash
http://<app_name>.azurewebsites.net
```

![App Service 'de çalışan uygulama](../app-service/media/app-service-web-tutorial-dotnetcore-sqldb/azure-app-in-browser.png)

## <a name="use-managed-identity-in-other-languages"></a>Yönetilen kimliği diğer dillerde kullanma

.NET Framework ve Java Spring uygulama yapılandırma sağlayıcılarının yönetilen kimlik için yerleşik desteği de vardır. Bu gibi durumlarda, bir sağlayıcıyı yapılandırırken, tam bağlantı dizesi yerine uygulama yapılandırma deponuzinin URL uç noktasını kullanın. Örneğin, hızlı başlangıçta oluşturulan .NET Framework konsol uygulaması için *app. config* dosyasında aşağıdaki ayarları belirtin:

```xml
    <configSections>
        <section name="configBuilders" type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" restartOnExternalChanges="false" requirePermission="false" />
    </configSections>

    <configBuilders>
        <builders>
            <add name="MyConfigStore" mode="Greedy" endpoint="${Endpoint}" type="Microsoft.Configuration.ConfigurationBuilders.AzureAppConfigurationBuilder, Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration" />
            <add name="Environment" mode="Greedy" type="Microsoft.Configuration.ConfigurationBuilders.EnvironmentConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Environment" />
        </builders>
    </configBuilders>

    <appSettings configBuilders="Environment,MyConfigStore">
        <add key="AppName" value="Console App Demo" />
        <add key="Endpoint" value ="Set via an environment variable - for example, dev, test, staging, or production endpoint." />
    </appSettings>
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [CLI örnekleri](./cli-samples.md)
