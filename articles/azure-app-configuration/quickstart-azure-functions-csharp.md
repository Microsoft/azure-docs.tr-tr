---
title: Azure Işlevleri ile Azure Uygulama yapılandırması için hızlı başlangıç | Microsoft Docs
description: Bu hızlı başlangıçta Azure Uygulama yapılandırması ve C# ile bir Azure Işlevleri uygulaması oluşturun. Uygulama yapılandırma deposu oluşturun ve bu depolama alanına bağlanın. İşlevi yerel olarak test edin.
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
ms.topic: quickstart
ms.date: 09/28/2020
ms.author: alkemper
ms.openlocfilehash: 9d378b21132e6646329c459401255ef9a3ed9426
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98724256"
---
# <a name="quickstart-create-an-azure-functions-app-with-azure-app-configuration"></a>Hızlı başlangıç: Azure Uygulama yapılandırması ile Azure Işlevleri uygulaması oluşturma

Bu hızlı başlangıçta, Azure App Configuration hizmetini bir Azure Işlevleri uygulamasına, tüm uygulama ayarlarınızı kodınızdan ayrı olarak depolamayı ve yönetimini merkezileştirmek için kullanacaksınız.

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/dotnet)
- **Azure geliştirme** iş yüküyle [Visual Studio 2019](https://visualstudio.microsoft.com/vs) .
- [Azure Işlevleri araçları](../azure-functions/functions-develop-vs.md#check-your-tools-version)

## <a name="create-an-app-configuration-store"></a>Uygulama yapılandırma deposu oluşturma

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7.   >    >  Aşağıdaki anahtar-değer çiftlerini eklemek için yapılandırma Gezgini +**anahtar-değer** oluştur ' u seçin:

    | Anahtar | Değer |
    |---|---|
    | TestApp: ayarlar: Ileti | Azure Uygulama yapılandırmasından veriler |

    **Etiket** ve **içerik türü** şimdilik boş bırakın.

8. **Uygula**’yı seçin.

## <a name="create-a-functions-app"></a>Işlevler uygulaması oluşturma

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

## <a name="connect-to-an-app-configuration-store"></a>Uygulama yapılandırma deposuna bağlanma
Bu proje, [.net Azure işlevlerine bağımlılık ekleme](../azure-functions/functions-dotnet-dependency-injection.md) ve ek yapılandırma kaynağı olarak Azure Uygulama yapılandırması 'nı kullanacaktır.

1. Projenize sağ tıklayın ve **NuGet Paketlerini Yönet**' i seçin. **Araştır** sekmesinde, aşağıdaki NuGet paketlerini arayıp projenize ekleyin.
   - [Microsoft.Extensions.Configurlama. AzureAppConfiguration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureAppConfiguration/) sürümü 4.1.0 veya üzeri
   - [Microsoft. Azure. Functions. Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/) sürüm 1.1.0 veya üzeri 

2. Aşağıdaki kodla, *Startup. cs* adlı yeni bir dosya ekleyin. Soyut sınıfı uygulayan adlı bir sınıfı tanımlar `Startup` `FunctionsStartup` . Azure Işlevleri başlatılırken kullanılan tür adını belirtmek için bir derleme özniteliği kullanılır.

    `ConfigureAppConfiguration`Yöntemi geçersiz kılınır ve Azure uygulama yapılandırma sağlayıcısı, çağırarak ek bir yapılandırma kaynağı olarak eklenir `AddAzureAppConfiguration()` . `Configure`Bu noktada herhangi bir hizmeti kaydetmeniz gerekmiyorsa yöntem boş bırakılır.
    
    ```csharp
    using System;
    using Microsoft.Azure.Functions.Extensions.DependencyInjection;
    using Microsoft.Extensions.Configuration;

    [assembly: FunctionsStartup(typeof(FunctionApp.Startup))]

    namespace FunctionApp
    {
        class Startup : FunctionsStartup
        {
            public override void ConfigureAppConfiguration(IFunctionsConfigurationBuilder builder)
            {
                string cs = Environment.GetEnvironmentVariable("ConnectionString");
                builder.ConfigurationBuilder.AddAzureAppConfiguration(cs);
            }

            public override void Configure(IFunctionsHostBuilder builder)
            {
            }
        }
    }
    ```

3. *İşlev1. cs*' yi açın ve aşağıdaki ad alanını ekleyin.

    ```csharp
    using Microsoft.Extensions.Configuration;
    ```

   Bağımlılık ekleme aracılığıyla bir örneğini almak için kullanılan bir Oluşturucu ekleyin `IConfiguration` .

    ```csharp
    private readonly IConfiguration _configuration;

    public Function1(IConfiguration configuration)
    {
        _configuration = configuration;
    }
    ```

4. `Run`Yapılandırmadan değerleri okumak için yöntemini güncelleştirin.

    ```csharp
    public async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        string keyName = "TestApp:Settings:Message";
        string message = _configuration[keyName];

        return message != null
            ? (ActionResult)new OkObjectResult(message)
            : new BadRequestObjectResult($"Please create a key-value with the key '{keyName}' in App Configuration.");
    }
    ```

   `Function1`Sınıfı ve `Run` yöntemi statik olmamalıdır. Otomatik olarak `static` belirtilmişse değiştiriciyi kaldırın.

## <a name="test-the-function-locally"></a>İşlevi yerel olarak test etme

1. **ConnectionString** adlı bir ortam değişkeni ayarlayın ve uygulama yapılandırma deponuzu için erişim anahtarı olarak ayarlayın. Windows komut istemi 'ni kullanırsanız, aşağıdaki komutu çalıştırın ve değişikliğin etkili olması için komut istemi ' ni yeniden başlatın:

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

2. İşlevinizi test etmek için F5 tuşuna basın. İstenirse, **Azure Functions Core (CLI)** araçlarını indirmek ve yüklemek Için Visual Studio 'daki isteği kabul edin. Ayrıca, araçların HTTP isteklerini işleyebilmesi için bir güvenlik duvarı özel durumu etkinleştirmeniz gerekebilir.

3. Azure İşlevleri çalışma zamanı çıktısından işlevinizin URL'sini kopyalayın.

    ![VS 'de hızlı başlangıç Işlevi hata ayıklaması](./media/quickstarts/function-visual-studio-debugging.png)

4. HTTP isteğinin URL’sini tarayıcınızın adres çubuğuna yapıştırın. Aşağıdaki görüntüde, bu işlevin döndürdüğü yerel GET isteğine tarayıcıda yapılan yanıt gösterilmektedir.

    ![Hızlı başlangıç Işlevi yerel başlatma](./media/quickstarts/dotnet-core-function-launch-local.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, yeni bir uygulama yapılandırma deposu oluşturdunuz ve bunu [uygulama yapılandırma sağlayıcısı](/dotnet/api/Microsoft.Extensions.Configuration.AzureAppConfiguration)aracılığıyla bir Azure işlevleri uygulamasıyla kullandınız. Yapılandırmayı dinamik olarak yenilemek üzere Azure Işlevleri uygulamanızı güncelleştirme hakkında bilgi edinmek için sonraki öğreticiye geçin.

> [!div class="nextstepaction"]
> [Azure İşlevleri’nde dinamik yapılandırmayı etkinleştirme](./enable-dynamic-configuration-azure-functions-csharp.md)