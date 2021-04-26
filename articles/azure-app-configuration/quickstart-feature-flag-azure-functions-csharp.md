---
title: Azure Işlevlerine özellik bayrakları eklemeye yönelik hızlı başlangıç | Microsoft Docs
description: Bu hızlı başlangıçta Azure App Configuration 'ın özellik bayrakları ile Azure Işlevleri 'ni kullanın ve işlevi yerel olarak test edin.
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
ms.topic: quickstart
ms.date: 8/26/2020
ms.author: alkemper
ms.openlocfilehash: 96efc0ea6300e482ddeeda8fa177847f02b7e126
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98724263"
---
# <a name="quickstart-add-feature-flags-to-an-azure-functions-app"></a>Hızlı başlangıç: Azure Işlevleri uygulamasına özellik bayrakları ekleme

Bu hızlı başlangıçta, bir Azure Işlevleri uygulaması oluşturup içindeki Özellik bayraklarını kullanacaksınız. Tüm özellik Bayraklarınızı merkezi olarak depolamak ve durumlarını denetlemek için Azure Uygulama yapılandırmasından Özellik yönetimini kullanırsınız.

.NET Özellik Yönetimi kitaplıkları çerçeveyi Özellik bayrağı desteğiyle genişletir. Bu kitaplıklar, .NET yapılandırma sisteminin üzerine kurulmuştur. Bunlar, .NET yapılandırma sağlayıcısı aracılığıyla uygulama yapılandırmasıyla tümleştirilir.

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/)
- **Azure geliştirme** iş yüküyle [Visual Studio 2019](https://visualstudio.microsoft.com/vs) .
- [Azure Işlevleri araçları](../azure-functions/functions-develop-vs.md#check-your-tools-version)

## <a name="create-an-app-configuration-store"></a>Uygulama yapılandırma deposu oluşturma

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. Özellik **Yöneticisi**  >  **+ Ekle** ' yi seçerek Özellik bayrağı ekleyin `Beta` .

    > [!div class="mx-imgBorder"]
    > ![Beta adlı özellik bayrağını etkinleştir](media/add-beta-feature-flag.png)

    `label`Şimdilik bırakın ve `Description` tanımsız olur.

8. Yeni özellik bayrağını kaydetmek için **Uygula** ' yı seçin.

## <a name="create-a-functions-app"></a>Işlevler uygulaması oluşturma

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

## <a name="connect-to-an-app-configuration-store"></a>Uygulama yapılandırma deposuna bağlanma

Bu proje, [.net Azure işlevlerinde bağımlılık ekleme](../azure-functions/functions-dotnet-dependency-injection.md)işlemini kullanacaktır. Azure uygulama yapılandırmasını, özellik bayraklarınızın depolandığı ek bir yapılandırma kaynağı olarak ekler.

1. Projenize sağ tıklayın ve **NuGet Paketlerini Yönet**' i seçin. **Araştır** sekmesinde, aşağıdaki NuGet paketlerini arayıp projenize ekleyin.
   - [Microsoft.Extensions.Configurlama. AzureAppConfiguration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureAppConfiguration/) sürümü 4.1.0 veya üzeri
   - [Microsoft. FeatureManagement](https://www.nuget.org/packages/Microsoft.FeatureManagement/) sürüm 2.2.0 veya üzeri
   - [Microsoft. Azure. Functions. Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/) sürüm 1.1.0 veya üzeri 

2. Aşağıdaki kodla, *Startup. cs* adlı yeni bir dosya ekleyin. Soyut sınıfı uygulayan adlı bir sınıfı tanımlar `Startup` `FunctionsStartup` . Azure Işlevleri başlatılırken kullanılan tür adını belirtmek için bir derleme özniteliği kullanılır.

    ```csharp
    using System;
    using Microsoft.Azure.Functions.Extensions.DependencyInjection;
    using Microsoft.Extensions.Configuration;
    using Microsoft.FeatureManagement;

    [assembly: FunctionsStartup(typeof(FunctionApp.Startup))]

    namespace FunctionApp
    {
        class Startup : FunctionsStartup
        {
            public override void ConfigureAppConfiguration(IFunctionsConfigurationBuilder builder)
            {
            }

            public override void Configure(IFunctionsHostBuilder builder)
            {
            }
        }
    }
    ```


3. Yöntemini güncelleştirin `ConfigureAppConfiguration` ve çağırarak Azure uygulama yapılandırma sağlayıcısını ek bir yapılandırma kaynağı olarak ekleyin `AddAzureAppConfiguration()` . 

   `UseFeatureFlags()`Yöntemi, sağlayıcıya Özellik bayraklarını yüklemesini söyler. Tüm özellik bayrakları, değişiklikler için yeniden denetlemeden önce 30 saniyelik bir varsayılan önbellek süresi. Süre sonu aralığı, `FeatureFlagsOptions.CacheExpirationInterval` yöntemine geçirilen özellik ayarlanarak güncelleştirilemeyebilir `UseFeatureFlags` . 

    ```csharp
    public override void ConfigureAppConfiguration(IFunctionsConfigurationBuilder builder)
    {
        builder.ConfigurationBuilder.AddAzureAppConfiguration(options =>
        {
            options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                   .Select("_")
                   .UseFeatureFlags();
        });
    }
    ```
   > [!TIP]
   > Özellik bayrakları dışında herhangi bir yapılandırmanın uygulamanıza yüklenmesini istemiyorsanız, `Select("_")` yalnızca var olmayan bir kukla anahtar "_" yüklemek için çağrı yapabilirsiniz. Varsayılan olarak, uygulama yapılandırma Deponuzdaki tüm yapılandırma anahtarı değerleri, hiçbir `Select` Yöntem çağrılmaması durumunda yüklenir.

4. `Configure`Azure uygulama yapılandırma Hizmetleri ve Özellik Yöneticisi 'ni bağımlılık ekleme yoluyla kullanılabilir hale getirmek için yöntemini güncelleştirin.

    ```csharp
    public override void Configure(IFunctionsHostBuilder builder)
    {
        builder.Services.AddAzureAppConfiguration();
        builder.Services.AddFeatureManagement();
    }
    ```

5. *İşlev1. cs*' yi açın ve aşağıdaki ad alanlarını ekleyin.

    ```csharp
    using System.Linq;
    using Microsoft.FeatureManagement;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

   `_featureManagerSnapshot`Bağımlılık ekleme yoluyla ve örneklerinin alınması için kullanılan bir Oluşturucu ekleyin `IConfigurationRefresherProvider` . İçinden `IConfigurationRefresherProvider` , örneğini elde edebilirsiniz `IConfigurationRefresher` .

    ```csharp
    private readonly IFeatureManagerSnapshot _featureManagerSnapshot;
    private readonly IConfigurationRefresher _configurationRefresher;

    public Function1(IFeatureManagerSnapshot featureManagerSnapshot, IConfigurationRefresherProvider refresherProvider)
    {
        _featureManagerSnapshot = featureManagerSnapshot;
        _configurationRefresher = refresherProvider.Refreshers.First();
    }
    ```

6. `Run`Özellik bayrağının durumuna bağlı olarak, görüntülenecek iletinin değerini değiştirmek için yöntemini güncelleştirin.

   `TryRefreshAsync`Yöntemi, özellik bayraklarını yenilemek Için işlev çağrısının başlangıcında çağrılır. Önbellek sona erme saati penceresine ulaşılırsa, bu işlem yapılmaz. `await`Özellik bayraklarının geçerli işlev çağrısını engellemeden yenilenmesini tercih ediyorsanız işleci kaldırın. Bu durumda, sonraki Işlev çağrıları güncelleştirilmiş değeri alır.

    ```csharp
    [FunctionName("Function1")]
    public async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
        ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        await _configurationRefresher.TryRefreshAsync();

        string message = await _featureManagerSnapshot.IsEnabledAsync("Beta")
                ? "The Feature Flag 'Beta' is turned ON"
                : "The Feature Flag 'Beta' is turned OFF";

        return (ActionResult)new OkObjectResult(message);
    }
    ```

## <a name="test-the-function-locally"></a>İşlevi yerel olarak test etme

1. **ConnectionString** adlı bir ortam değişkeni ayarlayın, burada değer, **erişim anahtarları** altında uygulama yapılandırma deponuzda daha önce aldığınız bağlantı dizesidir. Windows komut istemi 'ni kullanırsanız, aşağıdaki komutu çalıştırın ve değişikliğin etkili olması için komut istemi ' ni yeniden başlatın:

    ```cmd
        setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Windows PowerShell kullanıyorsanız şu komutu çalıştırın:

    ```azurepowershell
        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    MacOS veya Linux kullanıyorsanız şu komutu çalıştırın:

    ```bash
        export ConnectionString='connection-string-of-your-app-configuration-store'
    ```

1. İşlevinizi test etmek için F5 tuşuna basın. İstenirse, **Azure Functions Core (CLI)** araçlarını indirmek ve yüklemek Için Visual Studio 'daki isteği kabul edin. Ayrıca, araçların HTTP isteklerini işleyebilmesi için bir güvenlik duvarı özel durumu etkinleştirmeniz gerekebilir.

1. Azure İşlevleri çalışma zamanı çıktısından işlevinizin URL'sini kopyalayın.

    ![VS 'de hızlı başlangıç Işlevi hata ayıklaması](./media/quickstarts/function-visual-studio-debugging.png)

1. HTTP isteğinin URL’sini tarayıcınızın adres çubuğuna yapıştırın. Aşağıdaki görüntüde Özellik bayrağının `Beta` devre dışı bırakıldığını belirten yanıt gösterilmektedir. 

    ![Hızlı başlangıç Işlevi Özellik bayrağı devre dışı](./media/quickstarts/functions-launch-ff-disabled.png)

1. [Azure portalında](https://portal.azure.com) oturum açın. **Tüm kaynaklar**' ı seçin ve oluşturduğunuz uygulama yapılandırma deposunu seçin.

1. **Özellik Yöneticisi**' ni seçin ve **Beta** anahtarının durumunu **Açık** olarak değiştirin.

1. Tarayıcıyı birkaç kez yenileyin. Önbelleğe alınmış Özellik bayrağının süresi 30 saniye sonra dolarsa, `Beta` aşağıdaki görüntüde gösterildiği gibi, sayfanın Özellik bayrağının açık olduğunu belirtecek şekilde değiştirilmesi gerekir.
 
    ![Hızlı başlangıç Işlevi özelliği bayrağı etkin](./media/quickstarts/functions-launch-ff-enabled.png)

> [!NOTE]
> Bu öğreticide kullanılan örnek kod, [Azure Uygulama yapılandırması GitHub deposundan](https://github.com/Azure/AppConfiguration/tree/master/examples/DotNetCore/AzureFunction)indirilebilir.

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, bir özellik bayrağı oluşturdunuz ve [Microsoft. FeatureManagement](/dotnet/api/microsoft.featuremanagement) kitaplığı aracılığıyla bir Azure işlevleri uygulamasıyla kullandınız.

- [Özellik yönetimi](./concept-feature-management.md) hakkında daha fazla bilgi edinin
- [Özellik bayraklarını yönetme](./manage-feature-flags.md)
- [Koşullu özellik bayraklarını kullanma](./howto-feature-filters-aspnet-core.md)
- [Hedeflenen izleyiciler için özelliklerin aşamalı dağıtımını etkinleştir](./howto-targetingfilter-aspnet-core.md)
- [Azure Işlevleri uygulamasında dinamik yapılandırma kullanma](./enable-dynamic-configuration-azure-functions-csharp.md)