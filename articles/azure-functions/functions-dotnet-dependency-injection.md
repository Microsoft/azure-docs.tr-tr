---
title: .NET Azure İşlevleri'nde bağımlılık eklemeyi kullanma
description: .NET işlevlerinde Hizmetleri kaydetmek ve kullanmak için bağımlılık ekleme eklemeyi nasıl kullanacağınızı öğrenin
author: ggailey777
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 03/24/2021
ms.author: glenga
ms.reviewer: jehollan
ms.openlocfilehash: 32cd2760eadc94466cdf55883611c78ac0cf24e6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105608128"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>.NET Azure İşlevleri'nde bağımlılık eklemeyi kullanma

Azure Işlevleri, sınıflar ve bunların bağımlılıkları arasında [denetimin (IOC) bir Iç sürümünü](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) elde etmek için bir tekniktir olan bağımlılık ekleme (dı) yazılım tasarım modelini destekler.

- Azure Işlevlerine bağımlılık ekleme, .NET Core bağımlılığı ekleme özellikleri üzerine kurulmuştur. [.NET Core bağımlılığı ekleme](/aspnet/core/fundamentals/dependency-injection) konusunda benzerlik yapmanız önerilir. Bağımlılıkları geçersiz kılma ve yapılandırma değerlerinin tüketim planında Azure Işlevleri ile nasıl okunduğu konusunda farklılıklar vardır.

- Bağımlılık ekleme desteği, Azure Işlevleri 2. x ile başlar.

- Bağımlılık ekleme desenleri, C# işlevlerinizin [işlem içinde](functions-dotnet-class-library.md) veya [işlem dışı](dotnet-isolated-process-guide.md)çalışmasına bağlı olarak farklılık gösterir.  

> [!IMPORTANT]
> Bu makaledeki kılavuz yalnızca çalışma zamanında işlem içinde çalışan [C# sınıf kitaplığı işlevleri](functions-dotnet-class-library.md)için geçerlidir. Bu özel bağımlılık ekleme modeli, .NET 5,0 işlevlerini işlem dışı çalıştırmanıza olanak sağlayan [.net yalıtılmış işlevleri](dotnet-isolated-process-guide.md)için uygulanmaz. .NET yalıtılmış işlem modeli, normal ASP.NET Core bağımlılığı ekleme desenlerine dayanır. Daha fazla bilgi edinmek için bkz. .NET yalıtılmış işlem kılavuzu 'nda [bağımlılık ekleme](dotnet-isolated-process-guide.md#dependency-injection) .

## <a name="prerequisites"></a>Önkoşullar

Bağımlılık ekleme 'yi kullanabilmeniz için aşağıdaki NuGet paketlerini yüklemelisiniz:

- [Microsoft. Azure. Functions. uzantıları](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/)

- [Microsoft. net. SDK. Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) paket sürümü 1.0.28 veya üzeri

- [Microsoft. Extensions. Dependencyınjection](https://www.nuget.org/packages/Microsoft.Extensions.DependencyInjection/) (Şu anda yalnızca sürüm 3. x ve daha önceki sürümleri desteklenir)

## <a name="register-services"></a>Hizmetleri Kaydet

Hizmetleri kaydetmek için bir örneğe bileşen yapılandırmak ve eklemek üzere bir yöntem oluşturun `IFunctionsHostBuilder` .  Azure Işlevleri ana makinesi bir örneği oluşturur `IFunctionsHostBuilder` ve doğrudan kendi yönteğinize geçirir.

Yöntemi kaydetmek için `FunctionsStartup` Başlangıç sırasında kullanılan tür adını belirten derleme özniteliğini ekleyin.

```csharp
using Microsoft.Azure.Functions.Extensions.DependencyInjection;
using Microsoft.Extensions.DependencyInjection;

[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddHttpClient();

            builder.Services.AddSingleton<IMyService>((s) => {
                return new MyService();
            });

            builder.Services.AddSingleton<ILoggerProvider, MyLoggerProvider>();
        }
    }
}
```

Bu örnek, bir başlangıç kaydı için gereken [Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) paketini kullanır `HttpClient` .

### <a name="caveats"></a>Uyarılar

Çalışma zamanı başlangıç sınıfını işleyerek önce ve sonra çalıştırılan bir dizi kayıt adımı. Bu nedenle, aşağıdaki öğeleri aklınızda bulundurun:

- *Başlangıç sınıfı yalnızca kurulum ve kayıt için tasarlanmıştır.* Başlangıç işlemi sırasında başlangıçta kayıtlı hizmetleri kullanmaktan kaçının. Örneğin, başlatma sırasında kaydedilen bir günlükçüde bir iletiyi günlüğe almaya çalışmayın. Kayıt sürecinin bu noktası, hizmetlerinizin kullanıma hazır olması için çok erken bir işlemdir. Yöntem çalıştırıldıktan sonra `Configure` işlevler çalışma zamanı, hizmetlerinizin nasıl çalıştığını etkileyebilecek ek bağımlılıklar kaydetmeye devam eder.

- *Bağımlılık ekleme kapsayıcısı yalnızca açık olarak kayıtlı türleri barındırır*. Yalnızca Injectable türleri olarak kullanılabilen hizmetler, yönteminde kurulum olan şeydir `Configure` . Sonuç olarak, gibi IŞLEVLERE özgü türler `BindingContext` `ExecutionContext` Kurulum sırasında veya Injectable türleri olarak kullanılamaz.

## <a name="use-injected-dependencies"></a>Eklenen bağımlılıkları kullan

Oluşturucu Ekleme, bağımlılıklarınızı bir işlevde kullanılabilir hale getirmek için kullanılır. Oluşturucu Ekleme kullanımı, eklenen hizmetler veya işlev sınıflarınız için statik sınıflar kullanmanıza gerek duyar.

Aşağıdaki örnek, `IMyService` ve `HttpClient` bağımlılıklarının http ile tetiklenen bir işleve nasıl eklendiğini gösterir.

```csharp
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Extensions.Logging;
using System.Net.Http;
using System.Threading.Tasks;

namespace MyNamespace
{
    public class MyHttpTrigger
    {
        private readonly HttpClient _client;
        private readonly IMyService _service;

        public MyHttpTrigger(HttpClient httpClient, IMyService service)
        {
            this._client = httpClient;
            this._service = service;
        }

        [FunctionName("MyHttpTrigger")]
        public async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req,
            ILogger log)
        {
            var response = await _client.GetAsync("https://microsoft.com");
            var message = _service.GetMessage();

            return new OkObjectResult("Response from function with injected dependencies.");
        }
    }
}
```

Bu örnek, bir başlangıç kaydı için gereken [Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) paketini kullanır `HttpClient` .

## <a name="service-lifetimes"></a>Hizmet yaşam süreleri

Azure Işlevleri uygulamaları, [ASP.net bağımlılığı ekleme](/aspnet/core/fundamentals/dependency-injection#service-lifetimes)ile aynı hizmet yaşam sürelerini sağlar. Işlevler uygulaması için farklı hizmet yaşam süreleri aşağıdaki gibi davranır:

- **Geçici**: geçici hizmetler, hizmetin her çözümlenme sonrasında oluşturulur.
- **Kapsam**: kapsamlı hizmet ömrü bir işlev yürütme ömrü ile eşleşir. Kapsamlı hizmetler, işlev yürütmesi başına bir kez oluşturulur. Yürütme sırasında o hizmetin sonraki istekleri, var olan hizmet örneğini yeniden kullanır.
- Tekil **: tek** hizmet ömrü, ana bilgisayar ömrü ile eşleşir ve bu örnekteki işlev Yürütmelerinde yeniden kullanılır. Tek ömür Hizmetleri, bağlantılar ve istemciler için, örneğin `DocumentClient` veya örnekler için önerilir `HttpClient` .

GitHub üzerinde [farklı hizmet yaşam sürelerinin bir örneğini](https://github.com/Azure/azure-functions-dotnet-extensions/tree/main/src/samples/DependencyInjection/Scopes) görüntüleyin veya indirin.

## <a name="logging-services"></a>Günlük hizmetleri

Kendi günlük sağlayıcınıza ihtiyacınız varsa, bir özel türü [`ILoggerProvider`](/dotnet/api/microsoft.extensions.logging.iloggerfactory) [Microsoft. Extensions. Logging. soyutlamalar](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/) NuGet paketi aracılığıyla kullanılabilen bir örneği olarak kaydedin.

Application Insights, Azure Işlevleri tarafından otomatik olarak eklenir.

> [!WARNING]
> - `AddApplicationInsightsTelemetry()`Ortam tarafından sunulan hizmetlerle çakışan Hizmetleri kaydeden hizmetler koleksiyonuna eklemeyin.
> - Kendi kendinize kaydolmayın `TelemetryConfiguration` veya `TelemetryClient` yerleşik Application Insights işlevini kullanıyorsanız. Kendi örneğinizi yapılandırmanız gerekiyorsa `TelemetryClient` , `TelemetryConfiguration` [C# işlevlerinde günlük özel telemetri](functions-dotnet-class-library.md?tabs=v2%2Ccmd#log-custom-telemetry-in-c-functions)bölümünde gösterildiği gibi eklenen ile bir tane oluşturun.

### <a name="iloggert-and-iloggerfactory"></a>ILogger <T> ve ıloggerfactory

Ana bilgisayar, `ILogger<T>` `ILoggerFactory` oluşturucular halinde ve hizmetlerdeki Hizmetleri barındırır.  Bununla birlikte, varsayılan olarak bu yeni günlük filtreleri işlev günlüklerinden filtrelenerek yapılır.  `host.json`Ek filtreler ve kategoriler için kabul etmek üzere dosyayı değiştirmeniz gerekir.

Aşağıdaki örnek, konağa açık olan günlüklerin nasıl ekleneceğini gösterir `ILogger<HttpTrigger>` .

```csharp
namespace MyNamespace
{
    public class HttpTrigger
    {
        private readonly ILogger<HttpTrigger> _log;

        public HttpTrigger(ILogger<HttpTrigger> log)
        {
            _log = log;
        }

        [FunctionName("HttpTrigger")]
        public async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req)
        {
            _log.LogInformation("C# HTTP trigger function processed a request.");

            // ...
    }
}
```

Aşağıdaki örnek `host.json` dosya günlük filtresini ekler.

```json
{
    "version": "2.0",
    "logging": {
        "applicationInsights": {
            "samplingSettings": {
                "isEnabled": true,
                "excludedTypes": "Request"
            }
        },
        "logLevel": {
            "MyNamespace.HttpTrigger": "Information"
        }
    }
}
```

Günlük düzeyleri hakkında daha fazla bilgi için bkz. [günlük düzeylerini yapılandırma](configure-monitoring.md#configure-log-levels).

## <a name="function-app-provided-services"></a>İşlev uygulaması tarafından sunulan hizmetler

İşlev Konağı birçok hizmeti kaydeder. Aşağıdaki hizmetler uygulamanızda bir bağımlılık olarak ele alınır:

|Hizmet Türü|Ömür|Description|
|--|--|--|
|`Microsoft.Extensions.Configuration.IConfiguration`|Adet|Çalışma zamanı yapılandırması|
|`Microsoft.Azure.WebJobs.Host.Executors.IHostIdProvider`|Adet|Konak örneğinin KIMLIĞINI sağlamaktan sorumlu|

Bağımlılığı almak istediğiniz başka hizmetler varsa, [bir sorun oluşturun ve bunları GitHub 'da önerin](https://github.com/azure/azure-functions-host).

### <a name="overriding-host-services"></a>Konak hizmetlerini geçersiz kılma

Konak tarafından belirtilen geçersiz kılma Hizmetleri şu anda desteklenmiyor.  Geçersiz kılmak istediğiniz hizmetler varsa, [bir sorun oluşturun ve bunları GitHub 'da önerin](https://github.com/azure/azure-functions-host).

## <a name="working-with-options-and-settings"></a>Seçeneklerle ve ayarlarla çalışma

[Uygulama ayarlarında](./functions-how-to-use-azure-function-app-settings.md#settings) tanımlanan değerler `IConfiguration` , başlangıç sınıfındaki uygulama ayarları değerlerini okumanızı sağlayan bir örnekte kullanılabilir.

`IConfiguration`Örnekten değerleri özel bir türe ayıklayabilirsiniz. Uygulama ayarları değerlerini özel bir türe kopyalamak, bu değerleri tablo haline getirerek, hizmetlerinizi test etmelerini kolaylaştırır. Yapılandırma örneğine okunan ayarların basit anahtar/değer çiftleri olması gerekir.

Uygulama ayarıyla tutarlı adlı bir özellik içeren aşağıdaki sınıfı göz önünde bulundurun:

```csharp
public class MyOptions
{
    public string MyCustomSetting { get; set; }
}
```

Ve `local.settings.json` özel ayarı aşağıdaki gibi yapılandırabilecek bir dosya:
```json
{
  "IsEncrypted": false,
  "Values": {
    "MyOptions:MyCustomSetting": "Foobar"
  }
}
```

`Startup.Configure`Yöntemi içinde, `IConfiguration` aşağıdaki kodu kullanarak örnekten değerleri özel türüne ayıklayabilirsiniz:

```csharp
builder.Services.AddOptions<MyOptions>()
    .Configure<IConfiguration>((settings, configuration) =>
    {
        configuration.GetSection("MyOptions").Bind(settings);
    });
```

Çağırma `Bind` özelliği, eşleşen özellik adlarına sahip değerleri özel örneğe kopyalar. Options örneği artık bir işleve eklemek için IoC kapsayıcısında kullanılabilir.

Options nesnesi, genel arabirimin bir örneği olarak işlevine eklenir `IOptions` . `Value`Yapılandırmanızda bulunan değerlere erişmek için özelliğini kullanın.

```csharp
using System;
using Microsoft.Extensions.Options;

public class HttpTrigger
{
    private readonly MyOptions _settings;

    public HttpTrigger(IOptions<MyOptions> options)
    {
        _settings = options.Value;
    }
}
```

Seçeneklerle çalışma hakkında daha fazla ayrıntı için [ASP.NET Core Içindeki seçenekler düzenine](/aspnet/core/fundamentals/configuration/options) bakın.

## <a name="using-aspnet-core-user-secrets"></a>ASP.NET Core Kullanıcı gizli dizileri kullanma

Yerel olarak geliştirilirken, ASP.NET Core gizli bilgileri proje kökü dışında depolamanızı sağlayan bir [gizli dizi Yöneticisi aracı](/aspnet/core/security/app-secrets#secret-manager) sağlar. Parolaların yanlışlıkla kaynak denetimine kaydedilmesine daha az neden olur. Azure Functions Core Tools (sürüm 3.0.3233 veya üzeri), ASP.NET Core gizli Yöneticisi tarafından oluşturulan gizli dizileri otomatik olarak okur.

Bir .NET Azure Işlevleri projesini Kullanıcı gizli dizilerini kullanacak şekilde yapılandırmak için, proje kökünde aşağıdaki komutu çalıştırın.

```bash
dotnet user-secrets init
```

Ardından, `dotnet user-secrets set` gizli dizileri oluşturmak veya güncelleştirmek için komutunu kullanın.

```bash
dotnet user-secrets set MySecret "my secret value"
```

İşlev uygulaması kodunuzda Kullanıcı gizli dizileri değerlerine erişmek için `IConfiguration` veya kullanın `IOptions` .

## <a name="customizing-configuration-sources"></a>Yapılandırma kaynaklarını özelleştirme

> [!NOTE]
> Yapılandırma kaynağı özelleştirmesi, Azure Işlevleri ana bilgisayar sürümleri 2.0.14192.0 ve 3.0.14191.0 ' den başlayarak kullanılabilir.

Ek yapılandırma kaynakları belirtmek için, `ConfigureAppConfiguration` işlev uygulamanızın sınıfındaki yöntemi geçersiz kılın `StartUp` .

Aşağıdaki örnek, temel ve isteğe bağlı ortama özgü uygulama ayarları dosyalarından yapılandırma değerlerini ekler.

```csharp
using System.IO;
using Microsoft.Azure.Functions.Extensions.DependencyInjection;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;

[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void ConfigureAppConfiguration(IFunctionsConfigurationBuilder builder)
        {
            FunctionsHostBuilderContext context = builder.GetContext();

            builder.ConfigurationBuilder
                .AddJsonFile(Path.Combine(context.ApplicationRootPath, "appsettings.json"), optional: true, reloadOnChange: false)
                .AddJsonFile(Path.Combine(context.ApplicationRootPath, $"appsettings.{context.EnvironmentName}.json"), optional: true, reloadOnChange: false)
                .AddEnvironmentVariables();
        }
    }
}
```

Özelliğine yapılandırma sağlayıcıları ekleyin `ConfigurationBuilder` `IFunctionsConfigurationBuilder` . Yapılandırma sağlayıcılarını kullanma hakkında daha fazla bilgi için [ASP.NET Core yapılandırma](/aspnet/core/fundamentals/configuration/#configuration-providers)konusuna bakın.

`FunctionsHostBuilderContext`, Öğesinden elde edilir `IFunctionsConfigurationBuilder.GetContext()` . Bu bağlamı, geçerli ortam adını almak ve işlev uygulama klasörünüzdeki yapılandırma dosyalarının konumunu çözümlemek için kullanın.

Varsayılan olarak, *appsettings.js* gibi yapılandırma dosyaları, işlev uygulamasının çıkış klasörüne otomatik olarak kopyalanmaz. Dosyaların kopyalandığından emin olmak için *. csproj* dosyanızı aşağıdaki örnekle eşleşecek şekilde güncelleştirin.

```xml
<None Update="appsettings.json">
    <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>      
</None>
<None Update="appsettings.Development.json">
    <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    <CopyToPublishDirectory>Never</CopyToPublishDirectory>
</None>
```

> [!IMPORTANT]
> Tüketim veya Premium planlarında çalışan işlev uygulamaları için, Tetiklerde kullanılan yapılandırma değerlerinde yapılan değişiklikler ölçeklendirme hatalarına neden olabilir. Sınıf tarafından bu özelliklerde yapılan tüm değişiklikler, `FunctionsStartup` işlev uygulaması başlatma hatasıyla sonuçlanır.

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için aşağıdaki kaynaklara bakın:

- [İşlev uygulamanızı izleme](functions-monitoring.md)
- [İşlevler için en iyi uygulamalar](functions-best-practices.md)
