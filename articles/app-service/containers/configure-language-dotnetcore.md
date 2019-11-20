---
title: ASP.NET Core uygulamalarını Yapılandırma-Azure App Service | Microsoft Docs
description: ASP.NET Core uygulamalarını Azure App Service içinde çalışacak şekilde nasıl yapılandıracağınızı öğrenin
services: app-service
documentationcenter: ''
author: cephalin
manager: gwallace
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/13/2019
ms.author: cephalin
ms.openlocfilehash: b05120148d3b82829c465effbcdc948da950aaf0
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68990254"
---
# <a name="configure-a-linux-aspnet-core-app-for-azure-app-service"></a>Azure App Service için bir Linux ASP.NET Core uygulaması yapılandırma

ASP.NET Core uygulamalar derlenmiş ikili dosyalar olarak dağıtılmalıdır. Visual Studio yayımlama aracı çözümü oluşturur ve ardından derlenmiş ikilileri doğrudan dağıtır, ancak App Service dağıtım motoru önce kod deposunu dağıtır ve ardından ikilileri derler.

Bu kılavuzda, App Service yerleşik bir Linux kapsayıcısını kullanan ASP.NET Core geliştiricilere yönelik temel kavramlar ve yönergeler sağlanmaktadır. Azure App Service hiç kullanmadıysanız, önce [ASP.NET Core hızlı başlangıç](quickstart-dotnetcore.md) ve [ASP.NET Core SQL veritabanı öğreticisiyle](tutorial-dotnetcore-sqldb-app.md) izleyin.

## <a name="show-net-core-version"></a>.NET Core sürümünü göster

Geçerli .NET Core sürümünü göstermek için [Cloud Shell](https://shell.azure.com)aşağıdaki komutu çalıştırın:

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Desteklenen tüm .NET Core sürümlerini göstermek için [Cloud Shell](https://shell.azure.com)aşağıdaki komutu çalıştırın:

```azurecli-interactive
az webapp list-runtimes --linux | grep DOTNETCORE
```

## <a name="set-net-core-version"></a>.NET Core sürümünü ayarla

.NET Core sürümünü 2,1 olarak ayarlamak için [Cloud Shell](https://shell.azure.com) aşağıdaki komutu çalıştırın:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "DOTNETCORE|2.1"
```

## <a name="access-environment-variables"></a>Ortam değişkenlerine erişin

App Service, uygulama ayarlarınızı uygulama kodunuzun dışında [ayarlayabilirsiniz](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) . Daha sonra, standart ASP.NET Core bağımlılığı ekleme modelini kullanarak herhangi bir sınıfta bunlara erişebilirsiniz:

```csharp
include Microsoft.Extensions.Configuration;

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
            // retrieve App Service app setting
            var myAppSetting = _configuration["MySetting"];
            // retrieve App Service connection string
            var myConnString = _configuration.GetConnectionString("MyDbConnection");
        }
    }
}
```

App Service ve *appSettings. JSON*' de aynı ada sahip bir uygulama ayarı yapılandırırsanız, örneğin, App Service değeri *appSettings. JSON* değerine göre önceliklidir. Yerel *appSettings. JSON* değeri, uygulamada yerel olarak hata ayıklamanıza olanak tanır, ancak App Service değeri, uygulamayı üretim ayarları ile birlikte çalıştırmanıza olanak sağlar. Bağlantı dizeleri aynı şekilde çalışır. Bu şekilde, uygulamanızın gizli dizilerini kod deponuzun dışında tutabilir ve kodunuzda değişiklik yapmadan uygun değerlere erişebilirsiniz.

## <a name="get-detailed-exceptions-page"></a>Ayrıntılı özel durumlar sayfası al

ASP.NET uygulamanız Visual Studio hata ayıklayıcısında bir özel durum oluşturduğunda, tarayıcıda ayrıntılı bir özel durum sayfası görüntülenir, ancak bu sayfa App Service bir genel **HTTP 500** hatası ile değiştirilirken veya **isteğiniz işlenirken bir hata oluştu.** İleti. Ayrıntılı özel durum sayfasını App Service göstermek için, <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>aşağıdaki komutu `ASPNETCORE_ENVIRONMENT` çalıştırarak uygulama ayarını uygulamanıza ekleyin.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASPNETCORE_ENVIRONMENT="Development"
```

## <a name="detect-https-session"></a>HTTPS oturumunu Algıla

App Service, [SSL sonlandırması](https://wikipedia.org/wiki/TLS_termination_proxy) ağ yükü dengeleyicilerde gerçekleşinceye kadar, tüm https istekleri UYGULAMANıZA şifrelenmemiş HTTP istekleri olarak ulaşacak. Uygulama mantığınızın kullanıcı isteklerinin şifrelenip şifrelenmediğini bilmeleri gerekiyorsa, Iletilen üstbilgiler ara yazılımını *Startup.cs*içinde yapılandırın:

- İçindeki `X-Forwarded-For` veüst`X-Forwarded-Proto` bilgilerini iletmek için, yazılım yazılımını [forwardedheadersoptions ile](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions) yapılandırın`Startup.ConfigureServices`.
- Bilinen ağlara özel IP adresi aralıkları ekleyin, böylece ara yazılım App Service yük dengeleyiciye güvenebilirler.
- Diğer middlewares çağrılmadan önce içinde `Startup.Configure` [useforwardedheaders](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions.useforwardedheaders) yöntemini çağırın.

Üç öğeyi birlikte koymak, kodunuz aşağıdaki örneğe benzer şekilde görünür:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders =
            ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
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

Daha fazla bilgi için [proxy sunucuları ile çalışma ve yük Dengeleyiciler için ASP.NET Core yapılandırma](https://docs.microsoft.com/aspnet/core/host-and-deploy/proxy-load-balancer).

## <a name="deploy-multi-project-solutions"></a>Çoklu proje çözümlerini dağıtma

Dağıtım altyapısına kök dizinde bir *. csproj* dosyası ile bir ASP.net deposu dağıttığınızda, motor projeyi dağıtır. Kök dizinde *. sln* dosyası ile bir ASP.net deposu dağıttığınızda, altyapı, App Service uygulaması olarak bulduğu Ilk Web sitesini veya Web uygulaması projesini seçer. Altyapının istediğiniz projeyi seçmeme olasılığı vardır.

Çoklu projeli bir çözümü dağıtmak için, App Service ' de kullanmak üzere projeyi iki farklı şekilde belirtebilirsiniz:

### <a name="using-deployment-file"></a>. Deployment dosyasını kullanma

Depo köküne bir *. Deployment* dosyası ekleyin ve aşağıdaki kodu ekleyin:

```
[config]
project = <project-name>/<project-name>.csproj
```

### <a name="using-app-settings"></a>Uygulama ayarlarını kullanma

<a target="_blank" href="https://shell.azure.com">Azure Cloud Shell</a>, aşağıdaki CLI komutunu çalıştırarak App Service uygulamanıza bir uygulama ayarı ekleyin. *\<App-name >* ,  *\<Resource-Group-name >* ve  *\<proje-name >* uygun değerlerle değiştirin.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PROJECT="<project-name>/<project-name>.csproj"
```

## <a name="access-diagnostic-logs"></a>Tanılama günlüklerine erişim

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>SSH oturumunu tarayıcıda aç

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Öğretici: SQL veritabanı ile ASP.NET Core uygulaması](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [App Service Linux SSS](app-service-linux-faq.md)