---
title: Uygulama Yapılandırması’na erişmek için yönetilen kimlikleri kullanma
titleSuffix: Azure App Configuration
description: Yönetilen kimlikleri kullanarak Azure Uygulama yapılandırmasında kimlik doğrulama
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.custom: devx-track-csharp, fasttrack-edit
ms.topic: conceptual
ms.date: 04/08/2021
ms.openlocfilehash: 7a9eb992ff0cb98fdae2920da2beeda0bbd8941b
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107877543"
---
# <a name="use-managed-identities-to-access-app-configuration"></a>Uygulama Yapılandırması’na erişmek için yönetilen kimlikleri kullanma

Azure Active Directory [Yönetilen kimlikler](../active-directory/managed-identities-azure-resources/overview.md) , bulut uygulamanız için gizli dizi yönetimini basitleştirir. Yönetilen bir kimlikle, kodunuz üzerinde çalıştığı Azure hizmeti için oluşturulan hizmet sorumlusunu kullanabilir. Azure Key Vault veya yerel bağlantı dizesinde depolanan ayrı kimlik bilgileri yerine yönetilen bir kimlik kullanırsınız.

Azure Uygulama yapılandırması ve .NET Core, .NET Framework ve Java Spring istemci kitaplıklarında yerleşik olarak bulunan yönetilen kimlik desteği vardır. Bunu kullanmanız gerekmese de, yönetilen kimlik gizli dizileri içeren bir erişim belirteci gereksinimini ortadan kaldırır. Kodunuz, yalnızca hizmet uç noktasını kullanarak uygulama yapılandırma deposuna erişebilir. Bu URL 'YI, herhangi bir gizli dizi olmadan doğrudan kodunuza katıştırabilirsiniz.

Bu makalede, uygulama yapılandırmasına erişmek için yönetilen kimliğin nasıl avantajlarından faydalanabilirsiniz. Hızlı başlangıçlarda tanıtılan web uygulamasında oluşturulur. Devam etmeden önce  [uygulama yapılandırmasına sahip bir ASP.NET Core uygulaması oluşturun](./quickstart-aspnet-core-app.md) .

> [!NOTE]
> Bu makale bir örnek olarak Azure App Service kullanır, ancak aynı kavram yönetilen kimliği destekleyen diğer tüm Azure hizmetleri için de geçerlidir. Örneğin, [Azure Kubernetes hizmeti](../aks/use-azure-ad-pod-identity.md), [Azure sanal makinesi](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)ve [Azure Container Instances](../container-instances/container-instances-managed-identity.md). İş yükünüz bu hizmetlerden birinde barındırılıyorsa, hizmetin yönetilen kimlik desteğinden de yararlanabilirsiniz.

Bu makalede ayrıca yönetilen kimliği, uygulama yapılandırmasının Key Vault başvurularıyla birlikte nasıl kullanabileceğiniz gösterilmektedir. Tek bir yönetilen kimlik ile, uygulama yapılandırmasından Key Vault ve yapılandırma değerlerinden her iki sırda sorunsuzca erişebilirsiniz. Bu özelliği araştırmak isterseniz, önce [ASP.NET Core Key Vault başvuruları kullanın](./use-key-vault-references-dotnet-core.md) .

Bu öğreticideki adımları uygulamak için herhangi bir kod düzenleyicisi kullanabilirsiniz. [Visual Studio Code](https://code.visualstudio.com/) , Windows, MacOS ve Linux platformlarında kullanılabilen harika bir seçenektir.

Bu makalede şunları öğreneceksiniz:

> [!div class="checklist"]
> * Uygulama yapılandırmasına yönetilen bir kimlik erişimi verin.
> * Uygulamanızı, uygulama yapılandırmasına bağlandığınızda yönetilen bir kimlik kullanacak şekilde yapılandırın.
> * İsteğe bağlı olarak, uygulama yapılandırma Key Vault başvurusu aracılığıyla Key Vault bağlandığınızda Uygulamanızı yönetilen bir kimlik kullanacak şekilde yapılandırın.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için aşağıdakiler gereklidir:

* [.NET Core SDK](https://dotnet.microsoft.com/download).
* [Azure Cloud Shell yapılandırıldı](../cloud-shell/quickstart.md).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="add-a-managed-identity"></a>Yönetilen kimlik ekleme

Portalda yönetilen bir kimlik ayarlamak için, önce bir uygulama oluşturun ve sonra özelliği etkinleştirin.

1. [Azure Portal](https://portal.azure.com) , normalde yaptığınız gibi bir App Services örneği oluşturun. Portalda buna gidin.

1. Sol bölmedeki **Ayarlar** grubuna aşağı kaydırın ve **kimlik**' i seçin.

1. **Sistem atandı** sekmesinde **durumu** **Açık** olarak değiştirin ve **Kaydet**' i seçin.

1. Sistem tarafından atanan yönetilen kimliği etkinleştirmek isteyip istemediğiniz sorulduğunda **Evet** yanıtını verin.

    ![App Service yönetilen kimliği ayarlama](./media/set-managed-identity-app-service.png)

## <a name="grant-access-to-app-configuration"></a>Uygulama yapılandırmasına erişim izni verme

1. [Azure Portal](https://portal.azure.com), **tüm kaynaklar** ' ı seçin ve hızlı başlangıçta oluşturduğunuz uygulama yapılandırma deposunu seçin.

1. **Erişim denetimi (IAM)** öğesini seçin.

1. **Erişim denetimi** sekmesinde, **rol atama** kartı Kullanıcı arabiriminde **Ekle** ' yi seçin.

1. **Rol** altında **uygulama yapılandırma veri okuyucusu**' nu seçin. **Erişim ata**' nın altında, **sistem tarafından atanan yönetilen kimlik** altında **App Service** ' yi seçin.

1. **Abonelik** altında Azure aboneliğinizi seçin. Uygulamanız için App Service kaynağını seçin.

1. **Kaydet**’i seçin.

    ![Yönetilen kimlik ekleme](./media/add-managed-identity.png)

1. İsteğe bağlı: Key Vault erişim vermek istiyorsanız, [Key Vault erişim Ilkesi atama](../key-vault/general/assign-access-policy-portal.md)bölümündeki yönergeleri izleyin.

## <a name="use-a-managed-identity"></a>Yönetilen kimlik kullanma

1. *Azure. Identity* paketine bir başvuru ekleyin:

    ```bash
    dotnet add package Azure.Identity
    ```

1. Uygulama yapılandırma deponuzda uç noktayı bulun. Bu URL, Azure portal mağazanın **erişim tuşları** sekmesinde listelenir.

1. *appsettings.js* açın ve aşağıdaki betiği ekleyin. *\<service_endpoint>* Köşeli ayraçları dahil, uygulama yapılandırma deponuzu URL 'siyle değiştirin.

    ```json
    "AppConfig": {
        "Endpoint": "<service_endpoint>"
    }
    ```

1. *Program. cs*' yi açın ve `Azure.Identity` ve ad alanlarına bir başvuru ekleyin `Microsoft.Azure.Services.AppAuthentication` :

    ```csharp-interactive
    using Azure.Identity;
    ```

1. Yalnızca uygulama yapılandırmasında doğrudan depolanan değerlere erişmek istiyorsanız yöntemini `CreateWebHostBuilder` değiştirerek yöntemi güncelleştirin `config.AddAzureAppConfiguration()` (Bu `Microsoft.Azure.AppConfiguration.AspNetCore` pakette bulunur).

    > [!IMPORTANT]
    > `CreateHostBuilder``CreateWebHostBuilder`, .NET Core 3,0 ' de yer alır.  Ortamınıza göre doğru söz dizimini seçin.

    ### <a name="net-core-2x"></a>[.NET Core 2. x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
               .ConfigureAppConfiguration((hostingContext, config) =>
               {
                   var settings = config.Build();
                   config.AddAzureAppConfiguration(options =>
                       options.Connect(new Uri(settings["AppConfig:Endpoint"]), new ManagedIdentityCredential()));
               })
               .UseStartup<Startup>();
    ```

    ### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
                {
                    var settings = config.Build();
                    config.AddAzureAppConfiguration(options =>
                        options.Connect(new Uri(settings["AppConfig:Endpoint"]), new ManagedIdentityCredential()));
                });
            })
            .UseStartup<Startup>());
    ```
    ---

    > [!NOTE]
    > **Kullanıcı tarafından atanan bir yönetilen kimlik** kullanmak Istiyorsanız, [Managedıdentitycredential](/dotnet/api/azure.identity.managedidentitycredential)oluştururken ClientID değerini belirttiğinizden emin olun.
    >```
    >config.AddAzureAppConfiguration(options =>
    >   options.Connect(new Uri(settings["AppConfig:Endpoint"]), new ManagedIdentityCredential(<your_clientId>)));
    >```
    >[Azure kaynakları Için Yönetilen kimlikler SSS](../active-directory/managed-identities-azure-resources/managed-identities-faq.md#what-identity-will-imds-default-to-if-dont-specify-the-identity-in-the-request)bölümünde açıklandığı gibi, hangi yönetilen kimliğin kullanıldığını çözümlemek için varsayılan bir yol vardır. Bu durumda, Azure kimlik kitaplığı, gelecekte çok fazla çalışma zamanı sorunlarından kaçınmak için (örneğin, yeni bir kullanıcı tarafından atanan yönetilen kimlik eklenirse veya sistem tarafından atanan yönetilen kimlik etkinse) istenen kimliği belirtmenizi zorunlu kılar. Bu nedenle, yalnızca bir kullanıcı tarafından atanan yönetilen kimlik tanımlanmış olsa da, sistem tarafından atanan yönetilen kimlik yoksa, ClientID belirtmeniz gerekecektir.


1. Hem uygulama yapılandırma değerlerini hem de başvurularını Key Vault kullanmak için *program. cs* 'yi aşağıda gösterildiği gibi güncelleştirin. Bu kod `SetCredential` `ConfigureKeyVault` , yapılandırma sağlayıcısına Key Vault kimlik doğrulaması yapılırken hangi kimlik bilgilerinin kullanılacağını bildirmek için bir parçası olarak çağırır.

    ### <a name="net-core-2x"></a>[.NET Core 2. x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
               .ConfigureAppConfiguration((hostingContext, config) =>
               {
                   var settings = config.Build();
                   var credentials = new ManagedIdentityCredential();

                   config.AddAzureAppConfiguration(options =>
                   {
                       options.Connect(new Uri(settings["AppConfig:Endpoint"]), credentials)
                              .ConfigureKeyVault(kv =>
                              {
                                 kv.SetCredential(credentials);
                              });
                   });
               })
               .UseStartup<Startup>();
    ```

    ### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
                {
                    var settings = config.Build();
                    var credentials = new ManagedIdentityCredential();

                    config.AddAzureAppConfiguration(options =>
                    {
                        options.Connect(new Uri(settings["AppConfig:Endpoint"]), credentials)
                               .ConfigureKeyVault(kv =>
                               {
                                   kv.SetCredential(credentials);
                               });
                    });
                });
            })
            .UseStartup<Startup>());
    ```
    ---

    Artık diğer uygulama yapılandırma anahtarı gibi Key Vault başvurularına erişebilirsiniz. Yapılandırma sağlayıcısı, `ManagedIdentityCredential` Key Vault kimlik doğrulaması yapmak ve değeri almak için öğesini kullanır.

    > [!NOTE]
    > `ManagedIdentityCredential`Yalnızca, yönetilen kimlik kimlik doğrulamasını destekleyen hizmetlerin Azure ortamlarında kullanılabilir. Yerel ortamda çalışmaz. [`DefaultAzureCredential`](/dotnet/api/azure.identity.defaultazurecredential)Yönetilen kimlik gibi birkaç kimlik doğrulama seçeneklerine geri dönecektir, kodun hem yerel hem de Azure ortamlarında çalışması için kullanın.
    > 
    > Azure 'a dağıtıldığında, **Kullanıcı tarafından imzalanmış bir yönetilen kimlik** kullanmak Istiyorsanız `DefaultAzureCredential` [ClientID değerini belirtin](/dotnet/api/overview/azure/identity-readme#specifying-a-user-assigned-managed-identity-with-the-defaultazurecredential).

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="deploy-from-local-git"></a>Yerel Git’ten dağıtım

Kudu yapı sunucusuyla uygulamanız için yerel git dağıtımını etkinleştirmenin en kolay yolu [Azure Cloud Shell](https://shell.azure.com)kullanmaktır.

### <a name="configure-a-deployment-user"></a>Dağıtım kullanıcısı yapılandırma

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="enable-local-git-with-kudu"></a>Kudu ile yerel git 'i etkinleştirme
Uygulamanız için yerel bir git deponuz yoksa, bir tane açmanız gerekir. Yerel bir Git deposunu başlatmak için uygulamanızın proje dizininden aşağıdaki komutları çalıştırın:

```cmd
git init
git add .
git commit -m "Initial version"
```

Kudu yapı sunucusuyla uygulamanız için yerel git dağıtımını etkinleştirmek üzere [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/#az_webapp_deployment_source_config_local_git) Cloud Shell ' de çalıştırın.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group <group_name>
```

Bu komut, aşağıdaki çıktıya benzer bir işlem sağlar:

```json
{
  "url": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git"
}
```

### <a name="deploy-your-project"></a>Projenizi dağıtma

_Yerel Terminal penceresinde_ yerel git deponuza bir Azure uzak ekleyin. ' _\<url>_ In [kudu Ile yerel git 'i etkinleştirdiğinizden](#enable-local-git-with-kudu)aldığınız git uzak URL 'si ile değiştirin.

```bash
git remote add azure <url>
```

Aşağıdaki komutla uygulamanızı dağıtmak için Azure uzak deposuna gönderin. Bir parola istendiğinde, [dağıtım kullanıcısı yapılandırma](#configure-a-deployment-user)bölümünde oluşturduğunuz parolayı girin. Azure portal oturum açmak için kullandığınız parolayı kullanmayın.

```bash
git push azure main
```

Çıkışta çalışma zamanına özgü otomasyon (örneğin, ASP.NET için MSBuild, `npm install` Node.js için ve `pip install` Python için) görebilirsiniz.

### <a name="browse-to-the-azure-web-app"></a>Azure web uygulamasına göz atma

İçeriğin dağıtıldığını doğrulamak için tarayıcı kullanarak Web uygulamanıza gidin.

```bash
http://<app_name>.azurewebsites.net
```

## <a name="use-managed-identity-in-other-languages"></a>Yönetilen kimliği diğer dillerde kullanma

.NET Framework ve Java Spring uygulama yapılandırma sağlayıcılarının yönetilen kimlik için yerleşik desteği de vardır. Bu sağlayıcılardan birini yapılandırdığınızda, deponun URL uç noktasını tam bağlantı dizesi yerine kullanabilirsiniz.

Örneğin, *App.config* dosyasında aşağıdaki ayarları belirtmek için hızlı başlangıçta oluşturulan .NET Framework konsol uygulamasını güncelleştirebilirsiniz:

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
Bu öğreticide, uygulama yapılandırmasına erişimi kolaylaştırmak ve uygulamanız için kimlik bilgisi yönetimini geliştirmek üzere bir Azure yönetilen kimliği ekledik. Uygulama yapılandırmasını kullanma hakkında daha fazla bilgi için Azure CLı örneklerine devam edin.

> [!div class="nextstepaction"]
> [CLı örnekleri](./cli-samples.md)