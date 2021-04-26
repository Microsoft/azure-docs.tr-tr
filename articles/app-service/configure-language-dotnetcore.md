---
title: ASP.NET Core uygulamalarını yapılandırma
description: Yerel Windows örneklerinde veya önceden oluşturulmuş bir Linux kapsayıcısında, Azure App Service bir ASP.NET Core uygulamasının nasıl yapılandırılacağını öğrenin. Bu makalede en yaygın yapılandırma görevlerine yer verilmiştir.
ms.devlang: dotnet
ms.custom: devx-track-csharp, devx-track-azurecli
ms.topic: article
ms.date: 06/02/2020
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: 5e4351529fb7b6a66f554182a195bc26f79c0e2b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104889493"
---
# <a name="configure-an-aspnet-core-app-for-azure-app-service"></a>Azure App Service için bir ASP.NET Core uygulaması yapılandırma

> [!NOTE]
> .NET Framework 'de ASP.NET için bkz. [Azure App Service için bir ASP.NET uygulaması yapılandırma](configure-language-dotnet-framework.md)

ASP.NET Core uygulamalar, derlenmiş ikili dosyalar olarak Azure App Service dağıtılacak olmalıdır. Visual Studio yayımlama aracı çözümü oluşturur ve ardından derlenmiş ikilileri doğrudan dağıtır, ancak App Service dağıtım motoru önce kod deposunu dağıtır ve ardından ikilileri derler.

Bu kılavuz ASP.NET Core geliştiricilere yönelik temel kavramlar ve yönergeler sağlar. Azure App Service hiç kullanmadıysanız, önce [ASP.NET Core hızlı başlangıç](quickstart-dotnetcore.md) ve [ASP.NET Core SQL veritabanı öğreticisiyle](tutorial-dotnetcore-sqldb-app.md) izleyin.

::: zone pivot="platform-windows"  

## <a name="show-supported-net-core-runtime-versions"></a>Desteklenen .NET Core çalışma zamanı sürümlerini göster

App Service, Windows örnekleri için desteklenen tüm .NET Core sürümleri zaten yüklüdür. Kullanabileceğiniz .NET Core çalışma zamanı ve SDK sürümlerini göstermek için, sayfasına gidin `https://<app-name>.scm.azurewebsites.net/DebugConsole` ve tarayıcı tabanlı konsolunda aşağıdaki komutu çalıştırın:

```azurecli-interactive
dotnet --info
```

::: zone-end

::: zone pivot="platform-linux"

## <a name="show-net-core-version"></a>.NET Core sürümünü göster

Geçerli .NET Core sürümünü göstermek için [Cloud Shell](https://shell.azure.com)aşağıdaki komutu çalıştırın:

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Desteklenen tüm .NET Core sürümlerini göstermek için [Cloud Shell](https://shell.azure.com)aşağıdaki komutu çalıştırın:

```azurecli-interactive
az webapp list-runtimes --linux | grep DOTNETCORE
```

::: zone-end

## <a name="set-net-core-version"></a>.NET Core sürümünü ayarla

::: zone pivot="platform-windows"  

ASP.NET Core projeniz için proje dosyasında hedef Framework 'ü ayarlayın. Daha fazla bilgi için bkz. .NET Core belgelerinde [kullanılacak .NET Core sürümünü seçme](/dotnet/core/versions/selection) .

::: zone-end

::: zone pivot="platform-linux"

.NET Core sürümünü 3,1 olarak ayarlamak için [Cloud Shell](https://shell.azure.com) aşağıdaki komutu çalıştırın:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "DOTNETCORE|3.1"
```

::: zone-end

::: zone pivot="platform-linux"

## <a name="customize-build-automation"></a>Derleme Otomasyonu 'nu özelleştirme

Uygulamanızı, derleme Otomasyonu açıkken git veya ZIP paketleri kullanarak dağıtırsanız, App Service aşağıdaki sırayla Otomasyon adımları oluşturun:

1. Tarafından belirtilmişse özel betiği çalıştırın `PRE_BUILD_SCRIPT_PATH` .
1. `dotnet restore`NuGet bağımlılıklarını geri yüklemek için ' i çalıştırın.
1. `dotnet publish`Üretim için bir ikili oluşturmak üzere ' i çalıştırın.
1. Tarafından belirtilmişse özel betiği çalıştırın `POST_BUILD_SCRIPT_PATH` .

`PRE_BUILD_COMMAND` ve `POST_BUILD_COMMAND` Varsayılan olarak boş olan ortam değişkenleridir. Oluşturma öncesi komutları çalıştırmak için, tanımlayın `PRE_BUILD_COMMAND` . Oluşturma sonrası komutları çalıştırmak için, tanımlayın `POST_BUILD_COMMAND` .

Aşağıdaki örnek, virgülle ayrılmış bir dizi komuta iki değişkeni belirtir.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

Derleme Otomasyonu 'nu özelleştirmek için ek ortam değişkenleri için bkz. [Oryx Configuration](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md).

App Service nasıl çalıştığı ve Linux 'ta ASP.NET Core uygulamalar derleme hakkında daha fazla bilgi için bkz. [Oryx belgeleri: .NET Core uygulamaları nasıl algılanır ve oluşturulur](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/dotnetcore.md).

::: zone-end

## <a name="access-environment-variables"></a> Ortam değişkenlerine erişme

App Service'te uygulama kodunuzun dışında [uygulama ayarlarını düzenleyebilirsiniz](configure-common.md#configure-app-settings). Daha sonra, standart ASP.NET Core bağımlılığı ekleme modelini kullanarak herhangi bir sınıfta bunlara erişebilirsiniz:

```csharp
using Microsoft.Extensions.Configuration;

namespace SomeNamespace 
{
    public class SomeClass
    {
        private IConfiguration _configuration;
    
        public SomeClass(IConfiguration configuration)
        {
            _configuration = configuration;
        }
    
        public SomeMethod()
        {
            // retrieve nested App Service app setting
            var myHierarchicalConfig = _configuration["My:Hierarchical:Config:Data"];
            // retrieve App Service connection string
            var myConnString = _configuration.GetConnectionString("MyDbConnection");
        }
    }
}
```

App Service ve *appsettings.jsüzerinde* aynı ada sahip bir uygulama ayarı yapılandırırsanız, örneğin App Service değeri değeri *appsettings.js* üzerinden önceliklidir. Değer *üzerindeki yerelappsettings.js* , uygulamada yerel olarak hata ayıklamanıza olanak tanır, ancak App Service değeri, uygulamayı üretim ayarları ile üretimde çalıştırmanıza olanak sağlar. Bağlantı dizeleri aynı şekilde çalışır. Bu şekilde, uygulamanızın gizli dizilerini kod deponuzun dışında tutabilir ve kodunuzda değişiklik yapmadan uygun değerlere erişebilirsiniz.

> [!NOTE]
> *appsettings.jsüzerindeki* [Hiyerarşik yapılandırma verilerine](/aspnet/core/fundamentals/configuration/#hierarchical-configuration-data) `:` .NET Core için standart olan sınırlayıcı kullanılarak erişildiğini unutmayın. App Service belirli bir hiyerarşik yapılandırma ayarını geçersiz kılmak için, uygulama ayarı adını anahtarda aynı ayrılmış biçimle ayarlayın. [Cloud Shell](https://shell.azure.com)aşağıdaki örneği çalıştırabilirsiniz:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings My:Hierarchical:Config:Data="some value"
```

## <a name="deploy-multi-project-solutions"></a>Çoklu proje çözümlerini dağıtma

Visual Studio çözümü birden çok proje içerdiğinde, Visual Studio Publish işlemi dağıtılacak projeyi seçmeyi zaten içerir. Git ile veya ZIP dağıtımı ile olduğu gibi App Service dağıtım altyapısına dağıtırken, derleme Otomasyonu açıkken, App Service dağıtım altyapısı App Service uygulama olarak bulduğu ilk Web sitesini veya Web uygulaması projesini seçer. Uygulama ayarını belirterek App Service hangi projenin kullanılması gerektiğini belirtebilirsiniz `PROJECT` . Örneğin, [Cloud Shell](https://shell.azure.com)aşağıdakileri çalıştırın:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings PROJECT="<project-name>/<project-name>.csproj"
```

## <a name="access-diagnostic-logs"></a>Tanılama günlüklerine erişim

ASP.NET Core, [App Service için yerleşik bir günlük oluşturma sağlayıcısı](/aspnet/core/fundamentals/logging/#azure-app-service)sağlar. Projenizin *. cs* ' de, `ConfigureLogging` Aşağıdaki örnekte gösterildiği gibi, sağlayıcıyı uzantı yöntemi aracılığıyla uygulamanıza ekleyin:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureLogging(logging =>
        {
            logging.AddAzureWebAppDiagnostics();
        })
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

Daha sonra [Standart .NET Core düzeniyle](/aspnet/core/fundamentals/logging)günlükleri yapılandırabilir ve oluşturabilirsiniz.

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

App Service ASP.NET Core uygulamalar hakkında sorun giderme hakkında daha fazla bilgi için bkz. [ASP.NET Core Azure App Service ve IIS 'de sorun giderme](/aspnet/core/test/troubleshoot-azure-iis)

## <a name="get-detailed-exceptions-page"></a>Ayrıntılı özel durumlar sayfası al

ASP.NET Core uygulamanız Visual Studio hata ayıklayıcısında bir özel durum oluşturduğunda, tarayıcıda ayrıntılı bir özel durum sayfası görüntülenir, ancak bu sayfanın App Service bir genel **HTTP 500** hatası ile değiştirildiğini veya **isteğiniz işlenirken bir hata meydana geldi.** iletisi döndürmektedir. Ayrıntılı özel durum sayfasını App Service göstermek için, `ASPNETCORE_ENVIRONMENT` <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>aşağıdaki komutu çalıştırarak uygulama ayarını uygulamanıza ekleyin.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASPNETCORE_ENVIRONMENT="Development"
```

## <a name="detect-https-session"></a>HTTPS oturumunu Algıla

App Service, [SSL sonlandırması](https://wikipedia.org/wiki/TLS_termination_proxy) ağ yükü dengeleyicilerde gerçekleşinceye kadar, tüm https istekleri UYGULAMANıZA şifrelenmemiş HTTP istekleri olarak ulaşacak. Uygulama mantığınızın kullanıcı isteklerinin şifrelenip şifrelenmediğini bilmeleri gerekiyorsa, Iletilen üstbilgiler ara yazılımını *Başlangıç. cs*' de yapılandırın:

- İçindeki ve üst bilgilerini iletmek için, yazılım yazılımını [Forwardedheadersoptions](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions) ile yapılandırın `X-Forwarded-For` `X-Forwarded-Proto` `Startup.ConfigureServices` .
- Bilinen ağlara özel IP adresi aralıkları ekleyin, böylece ara yazılım App Service yük dengeleyiciye güvenebilirler.
- Diğer ara yazılım çağrılmadan önce içinde [Useforwardedheaders](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions.useforwardedheaders) yöntemini çağırın `Startup.Configure` .

Üç öğeyi birlikte koymak, kodunuz aşağıdaki örneğe benzer şekilde görünür:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders =
            ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
        // These three subnets encapsulate the applicable Azure subnets. At the moment, it's not possible to narrow it down further.
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:10.0.0.0"), 104));
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:192.168.0.0"), 112));
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:172.16.0.0"), 108));
    });
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseForwardedHeaders();

    ...

    app.UseMvc();
}
```

Daha fazla bilgi için bkz. [proxy sunucularıyla ve yük dengeleyicilerle çalışacak ASP.NET Core yapılandırma](/aspnet/core/host-and-deploy/proxy-load-balancer).

::: zone pivot="platform-linux"

## <a name="open-ssh-session-in-browser"></a>Tarayıcıda SSH oturumu açma

[!INCLUDE [Open SSH session in browser](../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

::: zone-end

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Öğretici: SQL veritabanı ile uygulama ASP.NET Core](tutorial-dotnetcore-sqldb-app.md)

::: zone pivot="platform-linux"

> [!div class="nextstepaction"]
> [App Service Linux Hakkında SSS](faq-app-service-linux.md)

::: zone-end
