---
title: .NET Core uygulamasında Özellik bayraklarını kullanma öğreticisi | Microsoft Docs
description: Bu öğreticide, .NET Core uygulamalarında Özellik bayraklarını nasıl uygulayacağınızı öğreneceksiniz.
services: azure-app-configuration
documentationcenter: ''
author: AlexandraKemperMS
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 09/17/2020
ms.author: alkemper
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 4d54e1ff07b250b5595d2f8aee5f022bd2359721
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105729516"
---
# <a name="tutorial-use-feature-flags-in-an-aspnet-core-app"></a>Öğretici: ASP.NET Core uygulamasında Özellik bayraklarını kullanma

.NET Core Özellik Yönetimi kitaplıkları, .NET veya ASP.NET Core uygulamasında Özellik bayraklarını uygulamak için ıdimatik destek sağlar. Bu kitaplıklar, deyimlerle özellikleri etkinleştirmek veya devre dışı bırakmak için el ile kod yazmak zorunda kalmazsınız, böylece kodlarınıza bildirimli olarak özellik bayrakları eklemenize olanak tanır `if` .

Özellik Yönetimi kitaplıkları Ayrıca arka planda özellik bayrakları yaşam döngülerini yönetir. Örneğin, kitaplıklar yenileme ve önbellek bayrak durumlarını veya bir istek çağrısı sırasında bayrak durumunun sabit olmasını garanti eder. Ayrıca, ASP.NET Core kitaplığı MVC denetleyici eylemleri, görünümler, rotalar ve ara yazılım gibi kullanıma hazır tümleştirmeler sunmaktadır.

[ASP.NET Core uygulama hızlı başlangıçlarına özellik ekleme bayrakları](./quickstart-feature-flag-aspnet-core.md) , bir ASP.NET Core uygulamasında Özellik bayraklarının nasıl kullanılacağına ilişkin basit bir örnek gösterir. Bu öğretici, özellik yönetimi kitaplıklarının ek kurulum seçeneklerini ve yeteneklerini gösterir. Bu öğreticide gösterilen örnek kodu denemek için hızlı başlangıçta oluşturulan örnek uygulamayı kullanabilirsiniz. 

ASP.NET Core Feature Management API başvuru belgeleri için bkz. [Microsoft. Featuremanagement ad alanı](/dotnet/api/microsoft.featuremanagement).

Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:

> [!div class="checklist"]
> * Özellik kullanılabilirliğini denetlemek için uygulamanızın önemli bölümlerine özellik bayrakları ekleyin.
> * Özellik bayraklarını yönetmek için kullandığınızda uygulama yapılandırmasıyla tümleştirin.

## <a name="set-up-feature-management"></a>Özellik yönetimini ayarlama

.NET Core Özellik Yöneticisi 'ne erişmek için uygulamanızın NuGet paketine yönelik başvuruları olmalıdır `Microsoft.FeatureManagement.AspNetCore` .

.NET Core Özellik Yöneticisi, Framework 'ün yerel yapılandırma sisteminden yapılandırılır. Sonuç olarak, dosyanın veya ortam değişkenlerinin yerel *appsettings.js* de dahil olmak üzere .NET Core tarafından desteklenen herhangi bir yapılandırma kaynağını kullanarak uygulamanızın Özellik bayrağı ayarlarını tanımlayabilirsiniz.

Varsayılan olarak, Özellik Yöneticisi `"FeatureManagement"` .NET Core yapılandırma verilerinin bölümünden Özellik bayrağı yapılandırmasını alır. Varsayılan yapılandırma konumunu kullanmak için, **Başlangıç** sınıfının **ConfigureServices** metoduna geçirilen **ıvicecollection** 'ın [addfeaturemanagement](/dotnet/api/microsoft.featuremanagement.servicecollectionextensions.addfeaturemanagement) yöntemini çağırın.


```csharp
using Microsoft.FeatureManagement;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        ...
        services.AddFeatureManagement();
    }
}
```

[Yapılandırma. GetSection öğesini](/dotnet/api/microsoft.web.administration.configuration.getsection) çağırarak ve istenen bölümün adını geçirerek, özellik yönetimi yapılandırmasının farklı bir yapılandırma bölümünden alınması gerektiğini belirtebilirsiniz. Aşağıdaki örnek, özellik yöneticisini bunun yerine farklı bir bölümden okumasını söyler `"MyFeatureFlags"` :

```csharp
using Microsoft.FeatureManagement;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        ...
        services.AddFeatureManagement(Configuration.GetSection("MyFeatureFlags"));
    }
}
```


Özellik bayraklarınız içinde filtreler kullanıyorsanız, [Microsoft. FeatureManagement. featurefilters](/dotnet/api/microsoft.featuremanagement.featurefilters) ad alanını dahil etmeniz ve yöntemin genel türü olarak kullanmak istediğiniz filtrenin tür adını belirten [addfeaturefilters](/dotnet/api/microsoft.featuremanagement.ifeaturemanagementbuilder.addfeaturefilter) öğesine bir çağrı eklemeniz gerekir. İşlevselliği dinamik olarak etkinleştirmek ve devre dışı bırakmak için özellik filtrelerini kullanma hakkında daha fazla bilgi için bkz. [hedeflenen izleyiciler için özelliklerin aşamalı dağıtımını etkinleştirme](./howto-targetingfilter-aspnet-core.md).

Aşağıdaki örnek, adlı yerleşik bir özellik filtresinin nasıl kullanılacağını gösterir `PercentageFilter` :



```csharp
using Microsoft.FeatureManagement;
using Microsoft.FeatureManagement.FeatureFilters;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        ...
        services.AddFeatureManagement()
                .AddFeatureFilter<PercentageFilter>();
    }
}
```

Özellik Bayraklarınızı uygulamanıza sabit olarak kodlamak yerine, özellik bayraklarını uygulamanın dışında tutmanızı ve ayrı olarak yönetmenizi öneririz. Bunun yapılması, bayrak durumlarını dilediğiniz zaman değiştirmenize ve bu değişikliklerin uygulamada hemen etkili olmasını sağlar. Azure uygulama yapılandırma hizmeti, tüm özellik Bayraklarınızı yönetmek için adanmış bir Portal Kullanıcı arabirimi sağlar. Azure uygulama yapılandırma hizmeti Ayrıca özellik bayraklarını uygulamanıza doğrudan .NET Core istemci kitaplıkları aracılığıyla da sunar.

ASP.NET Core uygulamanızı uygulama yapılandırmasına bağlanmanın en kolay yolu, NuGet paketine dahil edilen yapılandırma sağlayıcıdır `Microsoft.Azure.AppConfiguration.AspNetCore` . Pakete bir başvuru eklendikten sonra, bu NuGet paketini kullanmak için aşağıdaki adımları izleyin.

1. *Program. cs* dosyasını açın ve aşağıdaki kodu ekleyin.
    > [!IMPORTANT]
    > `CreateHostBuilder``CreateWebHostBuilder`.NET Core 3. x içindeki yerini alır. Ortamınıza göre doğru söz dizimini seçin.

    ### <a name="net-5x"></a>[.NET 5. x](#tab/core5x)
    
    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
                webBuilder.ConfigureAppConfiguration(config =>
                {
                    var settings = config.Build();
                    config.AddAzureAppConfiguration(options =>
                        options.Connect(settings["ConnectionStrings:AppConfig"]).UseFeatureFlags());
                }).UseStartup<Startup>());
    ```

    ### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)
    
    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;

    public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration(config =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(options =>
                    options.Connect(settings["ConnectionStrings:AppConfig"]).UseFeatureFlags());
            }).UseStartup<Startup>());
    ```
        
    ### <a name="net-core-2x"></a>[.NET Core 2. x](#tab/core2x)
    
    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                    .ConfigureAppConfiguration(config =>
                    {
                        var settings = config.Build();
                        config.AddAzureAppConfiguration(options =>
                            options.Connect(settings["ConnectionStrings:AppConfig"]).UseFeatureFlags());
                    }).UseStartup<Startup>();
    ```
    ---

2. *Başlangıç. cs* ' i açın ve `Configure` `ConfigureServices` adlı yerleşik ara yazılımı eklemek için ve metodunu güncelleştirin `UseAzureAppConfiguration` . Bu ara yazılım, ASP.NET Core Web uygulaması istekleri almaya devam ederken, özellik bayrağı değerlerinin yinelenen bir aralıkta yenilenmesini sağlar.



    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        app.UseAzureAppConfiguration();
    }
    ```

   ```csharp
   public void ConfigureServices(IServiceCollection services)
   {
       services.AddAzureAppConfiguration();
   }
   ```
   
Tipik bir senaryoda, uygulamanızın dağıtımı ve etkinleştirilme ve farklı özellikleri gibi özellik bayrak değerlerinizi düzenli aralıklarla güncelleştirecektir. Varsayılan olarak, özellik bayrağı değerleri 30 saniyelik bir süre için önbelleğe alınır, bu nedenle, ara yazılım bir istek aldığında tetiklenen yenileme işlemi, önbelleğe alınan değerin süresi dolana kadar değeri güncelleştirmez. Aşağıdaki kod, **Usefeatureflags** çağrısında [cacheexpirationınterval](/dotnet/api/microsoft.extensions.configuration.azureappconfiguration.featuremanagement.featureflagoptions.cacheexpirationinterval) ayarlanarak önbelleğin süre sonu zamanının veya yoklama aralığının 5 dakikaya nasıl değiştirileceğini gösterir.


    
```csharp
config.AddAzureAppConfiguration(options =>
    options.Connect(settings["ConnectionStrings:AppConfig"]).UseFeatureFlags(featureFlagOptions => {
        featureFlagOptions.CacheExpirationInterval = TimeSpan.FromMinutes(5);
    }));
});
```


## <a name="feature-flag-declaration"></a>Özellik bayrağı bildirimi

Her özellik bayrağı bildiriminde iki bölüm bulunur: bir ad ve bir özelliğin durumunun *Açık* olup olmadığını değerlendirmek için kullanılan bir veya daha fazla filtre listesi (yani, değeri olduğunda `True` ). Filtre, bir özelliğin açılması gereken zaman için bir ölçüt tanımlar.

Bir özellik bayrağının birden çok filtresi olduğunda, filtrenin etkin olması gerektiğini belirlerken filtre listesine bir düzen eklenir. Bu noktada, özellik bayrağı *Açık* ve kalan filtre sonuçları atlanır. Filtre yoksa, özelliğin etkinleştirilmesi gerektiğini gösteriyorsa, özellik bayrağı *kapalıdır*.

Özellik Yöneticisi özellik bayrakları için yapılandırma kaynağı olarak *appsettings.js* destekler. Aşağıdaki örnekte, bir JSON dosyasında özellik bayraklarının nasıl ayarlanacağı gösterilmektedir:

```JSON
"FeatureManagement": {
    "FeatureA": true, // Feature flag set to on
    "FeatureB": false, // Feature flag set to off
    "FeatureC": {
        "EnabledFor": [
            {
                "Name": "Percentage",
                "Parameters": {
                    "Value": 50
                }
            }
        ]
    }
}
```

Kurala göre, `FeatureManagement` Bu JSON belgesinin bölümü Özellik bayrağı ayarları için kullanılır. Önceki örnekte, içinde kendi filtreleri tanımlanmış olan üç özellik bayrakları gösterilmektedir `EnabledFor` :

* `FeatureA`*Açık*.
* `FeatureB`*kapalıdır*.
* `FeatureC` özelliği olan adlı bir filtre belirtir `Percentage` `Parameters` . `Percentage` , yapılandırılabilir bir filtredir. Bu örnekte, `Percentage` `FeatureC` bayrağın *Açık* olması için yüzde 50 olasılık değerini belirtir. Özellik filtrelerini kullanma hakkında nasıl yapılır Kılavuzu için bkz. [koşullu Özellik bayraklarını etkinleştirmek için özellik filtrelerini kullanma](./howto-feature-filters-aspnet-core.md).




## <a name="use-dependency-injection-to-access-ifeaturemanager"></a>Ieaturemanager 'a erişmek için bağımlılık ekleme 'yi kullanma 

Özellik bayrağı değerlerini el ile denetleme gibi bazı işlemlerde, [Ifeaturemanager](/dotnet/api/microsoft.featuremanagement.ifeaturemanager?preserve-view=true&view=azure-dotnet-preview)'ın bir örneğini almanız gerekir. ASP.NET Core MVC 'de, bağımlılık ekleme aracılığıyla Özellik yöneticisine erişebilirsiniz `IFeatureManager` . Aşağıdaki örnekte, `IFeatureManager` bir denetleyicinin oluşturucusunun imzasına bir tür bağımsız değişkeni eklenir. Çalışma zamanı başvuruyu otomatik olarak çözer ve oluşturucuyu çağırırken bir arabirim sağlar. Denetleyicinin zaten oluşturucuda bir veya daha fazla bağımlılık ekleme bağımsız değişkenine sahip olduğu bir uygulama şablonu kullanıyorsanız (gibi) `ILogger` , yalnızca `IFeatureManager` ek bir bağımsız değişken olarak ekleyebilirsiniz:

### <a name="net-5x"></a>[.NET 5. x](#tab/core5x)
    
```csharp
using Microsoft.FeatureManagement;

public class HomeController : Controller
{
    private readonly IFeatureManager _featureManager;

    public HomeController(ILogger<HomeController> logger, IFeatureManager featureManager)
    {
        _featureManager = featureManager;
    }
}
```

### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)

```csharp
using Microsoft.FeatureManagement;

public class HomeController : Controller
{
    private readonly IFeatureManager _featureManager;

    public HomeController(ILogger<HomeController> logger, IFeatureManager featureManager)
    {
        _featureManager = featureManager;
    }
}
```
    
### <a name="net-core-2x"></a>[.NET Core 2. x](#tab/core2x)

```csharp
using Microsoft.FeatureManagement;

public class HomeController : Controller
{
    private readonly IFeatureManager _featureManager;

    public HomeController(IFeatureManager featureManager)
    {
        _featureManager = featureManager;
    }
}
```

---

## <a name="feature-flag-references"></a>Özellik bayrağı başvuruları

Koddan başvurmak için özellik bayraklarını dize değişkenleri olarak tanımlayın:

```csharp
public static class MyFeatureFlags
{
    public const string FeatureA = "FeatureA";
    public const string FeatureB = "FeatureB";
    public const string FeatureC = "FeatureC";
}
```

## <a name="feature-flag-checks"></a>Özellik bayrağı denetimleri

Özellik yönetiminin ortak bir özelliği, bir özellik bayrağının *Açık* olarak ayarlanmış olup olmadığını denetme ve bu durumda kodun bir bölümünü çalıştırma. Örnek:

```csharp
IFeatureManager featureManager;
...
if (await featureManager.IsEnabledAsync(MyFeatureFlags.FeatureA))
{
    // Run the following code
}
```

## <a name="controller-actions"></a>Denetleyici eylemleri

MVC denetleyicileri sayesinde, `FeatureGate` bir denetleyici sınıfının veya belirli bir eylemin etkin olup olmadığını denetlemek için özniteliğini kullanabilirsiniz. Aşağıdaki `HomeController` denetleyicinin, `FeatureA` Denetleyici sınıfı içeren  herhangi bir eylemde yürütülmesi gerekir:

```csharp
using Microsoft.FeatureManagement.Mvc;

[FeatureGate(MyFeatureFlags.FeatureA)]
public class HomeController : Controller
{
    ...
}
```

Aşağıdaki `Index` eylemin `FeatureA` çalıştırılabilmesi için önce *Açık* olması gerekir:

```csharp
using Microsoft.FeatureManagement.Mvc;

[FeatureGate(MyFeatureFlags.FeatureA)]
public IActionResult Index()
{
    return View();
}
```

Denetim özelliği bayrağı *kapalı* olduğu IÇIN bir MVC denetleyicisi veya eylemi engellendiğinde, kayıtlı bir [ıdisabledfeatureshandler](/dotnet/api/microsoft.featuremanagement.mvc.idisabledfeatureshandler?preserve-view=true&view=azure-dotnet-preview) arabirimi çağırılır. Varsayılan `IDisabledFeaturesHandler` arabirim, yanıt gövdesi olmayan istemciye 404 durum kodu döndürür.

## <a name="mvc-views"></a>MVC görünümleri

*Görünümler* dizininde *_ViewImports. cshtml* dosyasını açın ve Feature Manager etiketi yardımcısını ekleyin:

```html
@addTagHelper *, Microsoft.FeatureManagement.AspNetCore
```

MVC görünümlerinde, bir `<feature>` özellik bayrağının etkin olup olmadığına bağlı olarak içerik işlemek için bir etiketi kullanabilirsiniz:

```html
<feature name="FeatureA">
    <p>This can only be seen if 'FeatureA' is enabled.</p>
</feature>
```

Gereksinimler karşılanmazsa, diğer içerikleri göstermek için `negate` özniteliği kullanılabilir.

```html
<feature name="FeatureA" negate="true">
    <p>This will be shown if 'FeatureA' is disabled.</p>
</feature>
```

Özellik `<feature>` etiketi, bir listedeki herhangi bir özellik veya tüm özelliklerin etkinleştirilmesi halinde içerik göstermek için de kullanılabilir.

```html
<feature name="FeatureA, FeatureB" requirement="All">
    <p>This can only be seen if 'FeatureA' and 'FeatureB' are enabled.</p>
</feature>
<feature name="FeatureA, FeatureB" requirement="Any">
    <p>This can be seen if 'FeatureA', 'FeatureB', or both are enabled.</p>
</feature>
```

## <a name="mvc-filters"></a>MVC filtreleri

MVC filtrelerini bir özellik bayrağının durumuna göre etkinleştirilecek şekilde ayarlayabilirsiniz. Bu özellik [ıasyncactionfilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncactionfilter)uygulayan filtrelerle sınırlıdır. Aşağıdaki kod adlı bir MVC filtresi ekler `ThirdPartyActionFilter` . Bu filtre, yalnızca etkinse MVC işlem hattı içinde tetiklenir `FeatureA` .  

```csharp
using Microsoft.FeatureManagement.FeatureFilters;

IConfiguration Configuration { get; set;}

public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc(options => {
        options.Filters.AddForFeature<ThirdPartyActionFilter>(MyFeatureFlags.FeatureA);
    });
}
```

## <a name="middleware"></a>Ara yazılım

Özellik bayraklarını, uygulama dallarını ve ara yazılımı koşullu olarak eklemek için de kullanabilirsiniz. Aşağıdaki kod yalnızca etkin olduğunda istek ardışık düzenine bir ara yazılım bileşeni ekler `FeatureA` :

```csharp
app.UseMiddlewareForFeature<ThirdPartyMiddleware>(MyFeatureFlags.FeatureA);
```

Bu kod, bir özellik bayrağını temel alarak tüm uygulamayı dallandırmak için daha genel özelliği devre dışı bırakır:

```csharp
app.UseForFeature(featureName, appBuilder => {
    appBuilder.UseMiddleware<T>();
});
```

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, kitaplıkları kullanarak ASP.NET Core uygulamanızda Özellik bayraklarını nasıl uygulayacağınızı öğrendiniz `Microsoft.FeatureManagement` . ASP.NET Core ve uygulama yapılandırmasındaki özellik yönetimi desteği hakkında daha fazla bilgi için aşağıdaki kaynaklara bakın:

* [ASP.NET Core Özellik bayrağı örnek kodu](./quickstart-feature-flag-aspnet-core.md)
* [Microsoft. FeatureManagement belgeleri](/dotnet/api/microsoft.featuremanagement)
* [Özellik bayraklarını yönetme](./manage-feature-flags.md)