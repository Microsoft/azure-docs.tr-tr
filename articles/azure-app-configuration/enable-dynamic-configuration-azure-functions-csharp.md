---
title: Azure Işlevleri uygulamasında Azure Uygulama yapılandırması dinamik yapılandırmasını kullanma öğreticisi | Microsoft Docs
description: Bu öğreticide, Azure Işlevleri uygulamaları için yapılandırma verilerini dinamik olarak güncelleştirme hakkında bilgi edineceksiniz.
services: azure-app-configuration
documentationcenter: ''
author: zhenlan
manager: qingye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 11/17/2019
ms.author: zhenlwa
ms.custom: devx-track-csharp, azure-functions
ms.tgt_pltfrm: Azure Functions
ms.openlocfilehash: add4b54adb02db09536f4e56a7f039c46245c182
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97963573"
---
# <a name="tutorial-use-dynamic-configuration-in-an-azure-functions-app"></a>Öğretici: Azure Işlevleri uygulamasında dinamik yapılandırma kullanma

Uygulama yapılandırması .NET yapılandırma sağlayıcısı, uygulama etkinliğine göre dinamik olarak yönetilen yapılandırmayı önbelleğe almayı ve yenilemeyi destekler. Bu öğreticide, kodunuzda dinamik yapılandırma güncelleştirmelerini nasıl uygulayabileceğinizi gösterir. Hızlı başlangıçlarda tanıtılan Azure Işlevleri uygulamasında oluşturulur. Devam etmeden önce, önce [Azure Uygulama yapılandırması ile bir Azure işlevleri uygulaması oluşturun](./quickstart-azure-functions-csharp.md) .

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Bir uygulama yapılandırma deposundaki değişikliklere yanıt olarak yapılandırmasını güncelleştirmek için Azure Işlevleri uygulamanızı ayarlayın.
> * Azure Işlevleri çağrılarınız için en son yapılandırmayı ekleme.

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/)
- **Azure geliştirme** iş yüküyle [Visual Studio 2019](https://visualstudio.microsoft.com/vs)
- [Azure Işlevleri araçları](../azure-functions/functions-develop-vs.md#check-your-tools-version)
- Son hızlı başlangıç [Azure Uygulama yapılandırması ile bir Azure işlevleri uygulaması oluşturma](./quickstart-azure-functions-csharp.md)

## <a name="reload-data-from-app-configuration"></a>Uygulama yapılandırmasından verileri yeniden yükleme

1. *Startup. cs*' yi açın ve `ConfigureAppConfiguration` yöntemini güncelleştirin. 

   `ConfigureRefresh`Yöntemi, uygulama içinde bir yenileme tetiklendiğinde bir ayarı değişiklikler için denetlenecek bir ayar kaydeder ve bu, daha sonra eklenirken daha sonraki adımda gerçekleştirilir `_configurationRefresher.TryRefreshAsync()` . `refreshAll`Parametresi, kayıtlı ayarda her değişiklik algılandığında uygulama yapılandırma sağlayıcısına tüm yapılandırmayı yeniden yüklemesi talimatını verir.

    Yenileme için kaydedilen tüm ayarlar varsayılan önbellek süresi olan 30 saniyedir. Yöntemi çağırarak güncelleştirilemeyebilir `AzureAppConfigurationRefreshOptions.SetCacheExpiration` .

    ```csharp
    public override void ConfigureAppConfiguration(IFunctionsConfigurationBuilder builder)
    {
        builder.ConfigurationBuilder.AddAzureAppConfiguration(options =>
        {
            options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                   // Load all keys that start with `TestApp:`
                   .Select("TestApp:*")
                   // Configure to reload configuration if the registered 'Sentinel' key is modified
                   .ConfigureRefresh(refreshOptions =>
                      refreshOptions.Register("TestApp:Settings:Sentinel", refreshAll: true));
        });
    }
    ```

   > [!TIP]
   > Uygulama yapılandırmasında birden çok anahtar değeri güncelleştirirken, uygulamanızın tüm değişiklikler yapılmadan önce yapılandırmayı yeniden yüklenmesini istemezsiniz. **Sentinel** anahtarını kaydedebilir ve yalnızca diğer tüm yapılandırma değişiklikleri tamamlandığında onu güncelleştirebilirsiniz. Bu, uygulamanızda yapılandırmanın tutarlılığını sağlamaya yardımcı olur.

2. `Configure`Azure uygulama yapılandırma Hizmetleri 'ni bağımlılık ekleme yoluyla kullanılabilir hale getirmek için yöntemini güncelleştirin.

    ```csharp
    public override void Configure(IFunctionsHostBuilder builder)
    {
        builder.Services.AddAzureAppConfiguration();
    }
    ```

3. *İşlev1. cs*' yi açın ve aşağıdaki ad alanlarını ekleyin.

    ```csharp
    using System.Linq;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

   `IConfigurationRefresherProvider`Örneğini elde ettiğiniz bağımlılık ekleme yoluyla örneğini almak için oluşturucuyu güncelleştirin `IConfigurationRefresher` .

    ```csharp
    private readonly IConfiguration _configuration;
    private readonly IConfigurationRefresher _configurationRefresher;

    public Function1(IConfiguration configuration, IConfigurationRefresherProvider refresherProvider)
    {
        _configuration = configuration;
        _configurationRefresher = refresherProvider.Refreshers.First();
    }
    ```

4. `Run`İşlev çağrısının başlangıcında yöntemi kullanarak yapılandırmayı yenilemek için yöntemi ve sinyali güncelleştirin `TryRefreshAsync` . Önbellek sona erme saati penceresine ulaşılırsa, bu işlem yapılmaz. `await`Yapılandırmanın geçerli işlev çağrısını engellemeden yenilenmesini tercih ediyorsanız işleci kaldırın. Bu durumda, sonraki Işlev çağrıları güncelleştirilmiş değeri alır.

    ```csharp
    public async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        await _configurationRefresher.TryRefreshAsync(); 

        string keyName = "TestApp:Settings:Message";
        string message = _configuration[keyName];
            
        return message != null
            ? (ActionResult)new OkObjectResult(message)
            : new BadRequestObjectResult($"Please create a key-value with the key '{keyName}' in App Configuration.");
    }
    ```

## <a name="test-the-function-locally"></a>İşlevi yerel olarak test etme

1. **ConnectionString** adlı bir ortam değişkeni ayarlayın ve uygulama yapılandırma deponuzu için erişim anahtarı olarak ayarlayın. Windows komut istemi 'ni kullanırsanız, aşağıdaki komutu çalıştırın ve değişikliğin etkili olması için komut istemi ' ni yeniden başlatın:

    ```console
    setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Windows PowerShell kullanıyorsanız şu komutu çalıştırın:

    ```powershell
    $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    MacOS veya Linux kullanıyorsanız şu komutu çalıştırın:

    ```console
    export ConnectionString='connection-string-of-your-app-configuration-store'
    ```

2. İşlevinizi test etmek için F5’e basın. İstenirse, **Azure Functions Core (CLI)** araçlarını indirmek ve yüklemek Için Visual Studio 'daki isteği kabul edin. Ayrıca, araçların HTTP isteklerini işleyebilmesi için bir güvenlik duvarı özel durumu etkinleştirmeniz gerekebilir.

3. Azure İşlevleri çalışma zamanı çıktısından işlevinizin URL'sini kopyalayın.

    ![VS 'de hızlı başlangıç Işlevi hata ayıklaması](./media/quickstarts/function-visual-studio-debugging.png)

4. HTTP isteğinin URL’sini tarayıcınızın adres çubuğuna yapıştırın. Aşağıdaki görüntüde, bu işlevin döndürdüğü yerel GET isteğine tarayıcıda yapılan yanıt gösterilmektedir.

    ![Hızlı başlangıç Işlevi yerel başlatma](./media/quickstarts/dotnet-core-function-launch-local.png)

5. [Azure portalında](https://portal.azure.com) oturum açın. **Tüm kaynaklar**' ı seçin ve hızlı başlangıçta oluşturduğunuz uygulama yapılandırma deposunu seçin.

6. **Yapılandırma Gezgini**' ni seçin ve aşağıdaki anahtarın değerini güncelleştirin:

    | Anahtar | Değer |
    |---|---|
    | TestApp: ayarlar: Ileti | Azure Uygulama yapılandırmasından alınan veriler-güncelleştirildi |

   Daha sonra Sentinel anahtarını oluşturun veya zaten mevcutsa değeri değiştirin, örneğin

    | Anahtar | Değer |
    |---|---|
    | TestApp: ayarlar: Sentinel | v1 |


7. Tarayıcıyı birkaç kez yenileyin. Önbelleğe alınan ayarın süresi 30 saniye sonra dolarsa, sayfa, güncelleştirilmiş değer ile Işlev çağrısının yanıtını gösterir.

    ![Hızlı başlangıç Işlevi yerel olarak Yenile](./media/quickstarts/dotnet-core-function-refresh-local.png)

> [!NOTE]
> Bu öğreticide kullanılan örnek kod, [uygulama yapılandırması GitHub](https://github.com/Azure/AppConfiguration/tree/master/examples/DotNetCore/AzureFunction)deposundan indirilebilir.

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, uygulama yapılandırmasından yapılandırma ayarlarını dinamik olarak yenilemek için Azure Işlevleri uygulamanızı etkinleştirdiniz. Azure yönetilen kimliğin uygulama yapılandırmasına erişimi kolaylaştırmak için nasıl kullanılacağını öğrenmek için bir sonraki öğreticiye geçin.

> [!div class="nextstepaction"]
> [Yönetilen kimlik tümleştirmesi](./howto-integrate-azure-managed-service-identity.md)
