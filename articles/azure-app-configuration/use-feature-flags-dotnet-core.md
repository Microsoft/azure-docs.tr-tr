---
title: Özellik bayraklarını kullanarak bir .NET Core uygulaması için öğretici | Microsoft Docs
description: Bu öğreticide, .NET Core uygulamalarında özellik bayraklarını uygulama konusunda bilgi edinin.
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 04/19/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 99559c0c77c3e4b29badec1c0be2d741df1f0621
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798369"
---
# <a name="tutorial-use-feature-flags-in-an-aspnet-core-app"></a>Öğretici: Özellik bayrakları ASP.NET Core uygulaması kullanın.

.NET Core özellik yönetim kitaplıkları, .NET veya ASP.NET Core uygulamasında özellik bayraklarını uygulamak için kullanılan deyimsel desteği sağlar. Bu kitaplıklar, tüm yazmak zorunda kalmazsınız bildirimli olarak özellik bayraklarını kodunuza eklemenize izin `if` deyimleri için bunları el ile.

Özellik Yönetim kitaplıkları da özellik bayrağı yaşam döngüleri arka planda yönetin. Örneğin, kitaplıkları, yenileme ve bayrağı durumları önbelleğe veya bir bayrak durumunu bir talep çağrısı sırasında sabit olmasını garanti. Ayrıca, ASP.NET Core kitaplığı MVC denetleyici eylemleri, görünümler, yollar ve ara yazılım içeren, kullanıma hazır Tümleştirmelerin sunar.

[ASP.NET Core uygulaması Hızlı Başlangıç için özellik bayraklarını ekleme](./quickstart-feature-flag-aspnet-core.md) özellik bayrakları bir ASP.NET Core uygulaması eklemek için birçok yol gösterir. Bu öğreticide bu yöntemleri ayrıntılı olarak açıklanmaktadır. Tam başvuru için bkz: [ASP.NET Core özellik yönetim belgelerine](https://go.microsoft.com/fwlink/?linkid=2091410).

Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:

> [!div class="checklist"]
> * Özellik bayrakları, anahtar Özellik kullanılabilirliği kontrol etmek için uygulamanızın parçalarını içinde ekleyin.
> * Özellik bayraklarını yönetmek için kullanıyorsanız, uygulama yapılandırması ile tümleştirin.

## <a name="set-up-feature-management"></a>Özellik yönetimini ayarlama

.NET Core özellik Yöneticisi `IFeatureManager` framework'ün yerel yapılandırma sistemden özellik bayraklarını alır. .NET Core destekler, yerel dahil olmak üzere herhangi bir yapılandırma kaynağını kullanarak, uygulamanızın özellik bayraklarını sonuç olarak, tanımlayabilirsiniz *appsettings.json* dosya veya ortam değişkenleri. `IFeatureManager` .NET Core üzerinde bağımlılık ekleme kullanır. Özellik Yönetimi Hizmetleri, standart kurallarını kullanarak kaydedebilirsiniz:

```csharp
using Microsoft.FeatureManagement;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddFeatureManagement();
    }
}
```

Varsayılan olarak, özellik Yöneticisi özellik bayraklarını alır `"FeatureManagement"` .NET Core yapılandırma verilerini bölümü. Aşağıdaki örnekte adlı farklı bir bölümünden okumak için özellik Yöneticisi bildiren `"MyFeatureFlags"` bunun yerine:

```csharp
using Microsoft.FeatureManagement;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddFeatureManagement(options =>
        {
                options.UseConfiguration(Configuration.GetSection("MyFeatureFlags"));
        });
    }
}
```

Filtreler, özellik bayrakları kullanıyorsanız, ek bir kitaplık içerir ve kaydetmek gerekir. Aşağıdaki örnekte adlı bir yerleşik özellik filtresini kullanma işlemi gösterilmektedir `PercentageFilter`:

```csharp
using Microsoft.FeatureManagement;
using Microsoft.FeatureManagement.FeatureFilters;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddFeatureManagement()
                .AddFeatureFilter<PercentageFilter>();
    }
}
```

Özellik bayrakları uygulama dışında tutun ve ayrı olarak yönetme öneririz. Bunun yapılması, dilediğiniz zaman bayrağı durumları değiştirebilir ve bu değişiklikleri uygulamaya hemen etkili sahip olanak tanır. Uygulama yapılandırması, düzenleme ve adanmış bir portal kullanıcı Arabirimi üzerinden tüm özellik bayraklarını denetleme için merkezi bir yerde sağlar. Uygulama yapılandırması da bayrakları uygulamanıza doğrudan aracılığıyla, .NET Core istemci kitaplıkları sunar.

Yapılandırma sağlayıcısı uygulama yapılandırması için ASP.NET Core uygulamanızı bağlamak için en kolay yolu olan `Microsoft.Azure.AppConfiguration.AspNetCore`. Bu NuGet paketi kullanmak için aşağıdaki adımları izleyin.

1. Açık *Program.cs* dosyasını açıp aşağıdaki kodu ekleyin.

   ```csharp
   using Microsoft.Extensions.Configuration.AzureAppConfiguration;

   public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
       WebHost.CreateDefaultBuilder(args)
              .ConfigureAppConfiguration((hostingContext, config) => {
                  var settings = config.Build();
                  config.AddAzureAppConfiguration(options => {
                      options.Connect(settings["ConnectionStrings:AppConfig"])
                             .UseFeatureFlags();
                   });
              })
              .UseStartup<Startup>();
   ```

2. Açık *Startup.cs* ve güncelleştirme `Configure` web uygulamasına ASP.NET Core sırasında yinelenen aralıklarla yenilenmesi için özellik bayrağı değerleri izin vermek için bir ara yazılım ekleme yöntemi devam isteklerini almak.

   ```csharp
   public void Configure(IApplicationBuilder app, IHostingEnvironment env)
   {
       app.UseAzureAppConfiguration();
       app.UseMvc();
   }
   ```

Özellik bayrağı değerleri, Zamanla değişen beklenir. Varsayılan olarak, ara yazılım bir istek aldığında tetiklenen bir yenileme işlemi değeri güncelleştiririz, bu önbelleğe alınan değeri süresi dolana kadar olmayan şekilde özellik bayrağı değerleri 30 saniyelik bir süre için önbelleğe alınır. Aşağıdaki kod önbellek sona erme zamanı veya yoklama aralığı için 5 dakika içinde nasıl değiştirileceğini gösterir `options.UseFeatureFlags()` çağırın.

```csharp
config.AddAzureAppConfiguration(options => {
    options.Connect(settings["ConnectionStrings:AppConfig"])
           .UseFeatureFlags(featureFlagOptions => {
                featureFlagOptions.CacheExpirationTime = TimeSpan.FromMinutes(5);
           });
});
```

## <a name="feature-flag-declaration"></a>Özellik bayrağı bildirimi

Her bir özellik bayrağını iki bölümden oluşur: bir ad ve bir özelliğin durumunu olup olmadığını değerlendirmek için kullanılan bir veya daha fazla filtrelerinin listesi *üzerinde* (diğer bir deyişle, değeri olduğunda `True`). Bir özellik açık olmalıdır, bir kullanım örneği için bir filtre tanımlar.

Bir özellik bayrağını birden çok filtre varsa, filtrenin özelliği etkinleştirilmiş filtrelerden birini belirler kadar sırayla geçiş yapılır. Bu noktada, özellik bayrağı olduğu *üzerinde*, ve kalan herhangi bir filtre sonuç atlanır. Filtre özelliği etkinleştirilmelidir gösteriyorsa, özellik bayrağı olduğu *kapalı*.

Özellik Yöneticisi destekler *appsettings.json* özellik bayrakları için bir yapılandırma kaynağı olarak. Aşağıdaki örnek, bir JSON dosyasında özellik bayraklarını ayarlayın gösterilmektedir:

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

Kural olarak, `FeatureManagement` bu JSON belgesinin bölümü için özellik bayrağı ayarları kullanılır. Önceki örnekte tanımlanan kendi filtrelerle üç özellik bayraklarını gösterir `EnabledFor` özelliği:

* `FeatureA` olan *üzerinde*.
* `FeatureB` olan *kapalı*.
* `FeatureC` adlı bir filtre belirtir `Percentage` ile bir `Parameters` özelliği. `Percentage` yapılandırılabilir bir filtredir. Bu örnekte, `Percentage` için yüzde 50 olasılığını belirtir `FeatureC` bayrağı olmasını *üzerinde*.

## <a name="feature-flag-references"></a>Özellik bayrağı başvuruları

Özellik bayraklarını kodda kolayca başvurabilir, böylece bunları olarak tanımlamalıdır `enum` değişkenleri:

```csharp
public enum MyFeatureFlags
{
    FeatureA,
    FeatureB,
    FeatureC
}
```

## <a name="feature-flag-checks"></a>Özellik bayrağı denetler

Özellik Yönetim temel düzeni özellik bayrağı ayarlandıysa ilk Denetlenecek olan *üzerinde*. Bu nedenle, özellik Yöneticisi ardından eylemleri çalıştıran özelliği içerir. Örneğin:

```csharp
IFeatureManager featureManager;
...
if (featureManager.IsEnabled(nameof(MyFeatureFlags.FeatureA)))
{
    // Run the following code
}
```

## <a name="dependency-injection"></a>Bağımlılık ekleme

ASP.NET Core MVC, özellik Yöneticisi erişebileceğiniz `IFeatureManager` aracılığıyla bağımlılık ekleme:

```csharp
public class HomeController : Controller
{
    private readonly IFeatureManager _featureManager;

    public HomeController(IFeatureManager featureManager)
    {
        _featureManager = featureManager;
    }
}
```

## <a name="controller-actions"></a>Denetleyici eylemleri

MVC denetleyicileri kullandığınız `FeatureGate` tüm denetleyici sınıfı veya belirli bir eylem etkin olup olmadığını denetlemek için özniteliği. Aşağıdaki `HomeController` denetleyici gerektirir `FeatureA` olmasını *üzerinde* denetleyicisi sınıfını içeren herhangi bir eylem yürütülmeden önce:

```csharp
[FeatureGate(MyFeatureFlags.FeatureA)]
public class HomeController : Controller
{
    ...
}
```

Aşağıdaki `Index` eylem gerektiren `FeatureA` olmasını *üzerinde* çalıştırılmadan önce:

```csharp
[FeatureGate(MyFeatureFlags.FeatureA)]
public IActionResult Index()
{
    return View();
}
```

Ne zaman bir MVC denetleyici veya eylemin denetleme özelliği bayrağı olduğu için engellendi *kapalı*, kayıtlı bir `IDisabledFeaturesHandler` arabirimi çağrılır. Varsayılan `IDisabledFeaturesHandler` arabirimi istemciye hiçbir yanıt gövdesi ile bir 404 durum kodu döndürür.

## <a name="mvc-views"></a>MVC görünümleri

MVC görünümleri, kullandığınız bir `<feature>` etiket içeriğini işlemek için temel bir özellik bayrağını etkinleştirilip etkinleştirilmediği üzerinde:

```html
<feature name="FeatureA">
    <p>This can only be seen if 'FeatureA' is enabled.</p>
</feature>
```

Gereksinimler karşılanmadı alternatif içerik görüntülemek için `negate` özniteliği kullanılabilir.

```html
<feature name="FeatureA" negate="true">
    <p>This will be shown if 'FeatureA' is disabled.</p>
</feature>
```

Özellik `<feature>` etiketi de kullanılabilir varsa içeriğini göstermek için veya bir listedeki tüm özellikler etkinleştirildi.

```html
<feature name="FeatureA, FeatureB" requirement="All">
    <p>This can only be seen if 'FeatureA' and 'FeatureB' are enabled.</p>
</feature>
<feature name="FeatureA, FeatureB" requirement="Any">
    <p>This can be seen if 'FeatureA', 'FeatureB', or both are enabled.</p>
</feature>
```

## <a name="mvc-filters"></a>MVC filtreleri

Bir özellik bayrağını durumuna göre etkin MVC filtreleri ayarlayabilirsiniz. Aşağıdaki kod, adlandırılmış bir MVC filtre ekler `SomeMvcFilter`. Bu filtre, MVC işlem hattı yalnızca Eğer içinde tetiklenir `FeatureA` etkinleştirilir.

```csharp
using Microsoft.FeatureManagement.FeatureFilters;

IConfiguration Configuration { get; set;}

public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc(options => {
        options.Filters.AddForFeature<SomeMvcFilter>(nameof(MyFeatureFlags.FeatureA));
    });
}
```

## <a name="routes"></a>Yollar

Özellik bayraklarını rotaları dinamik olarak kullanıma sunmak için kullanabilirsiniz. Aşağıdaki kod, ayarlar bir rota ekler `Beta` varsayılan denetleyici olarak yalnızca `FeatureA` etkinleştirilir:

```csharp
app.UseMvc(routes => {
    routes.MapRouteForFeature(nameof(MyFeatureFlags.FeatureA), "betaDefault", "{controller=Beta}/{action=Index}/{id?}");
});
```

## <a name="middleware"></a>Ara yazılım

Özellik bayrakları, uygulama dalları ve ara yazılım koşullu olarak eklemek için de kullanabilirsiniz. Bir ara yazılım bileşeni istek işlem hattı yalnızca aşağıdaki kodu ekler `FeatureA` etkinleştirilir:

```csharp
app.UseMiddlewareForFeature<ThirdPartyMiddleware>(nameof(MyFeatureFlags.FeatureA));
```

Bu kod, daha genel bir özellik bayrağını dayalı tüm uygulama dallanma yeteneği devre dışı oluşturur:

```csharp
app.UseForFeature(featureName, appBuilder => {
    appBuilder.UseMiddleware<T>();
});
```

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, özellik bayraklarını kullanarak ASP.NET Core uygulamanızı uygulama öğrendiniz `Microsoft.FeatureManagement` kitaplıkları. ASP.NET Core ve uygulama yapılandırma özelliği yönetimi desteği hakkında daha fazla bilgi için aşağıdaki kaynaklara bakın:

* [ASP.NET Core özellik bayrağını örnek kod](/azure/azure-app-configuration/quickstart-feature-flag-aspnet-core)
* [Microsoft.FeatureManagement belgeleri](https://docs.microsoft.com/dotnet/api/microsoft.featuremanagement)
* [Özellik bayraklarını yönetme](./manage-feature-flags.md)
