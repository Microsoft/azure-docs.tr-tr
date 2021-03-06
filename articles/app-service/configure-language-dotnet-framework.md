---
title: ASP.NET uygulamalarını yapılandırma
description: Azure App Service içinde bir ASP.NET uygulamasının nasıl yapılandırılacağını öğrenin. Bu makalede en yaygın yapılandırma görevlerine yer verilmiştir.
ms.devlang: dotnet
ms.custom: devx-track-csharp, devx-track-azurecli
ms.topic: article
ms.date: 06/02/2020
ms.openlocfilehash: 8ed6835583cc4881b19eee14ed392b193324535e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92744156"
---
# <a name="configure-an-aspnet-app-for-azure-app-service"></a>Azure App Service için bir ASP.NET uygulaması yapılandırma

> [!NOTE]
> ASP.NET Core için bkz. [Azure App Service için ASP.NET Core uygulamasını yapılandırma](configure-language-dotnetcore.md)

ASP.NET uygulamalar, derlenmiş ikili dosyalar olarak Azure App Service dağıtılmalıdır. Visual Studio yayımlama aracı çözümü oluşturur ve ardından derlenmiş ikilileri doğrudan dağıtır, ancak App Service dağıtım motoru önce kod deposunu dağıtır ve ardından ikilileri derler.

Bu kılavuz, ASP.NET geliştiricileri için temel kavramlar ve yönergeler sağlar. Azure App Service hiç kullanmadıysanız, önce [SQL veritabanı öğreticisi ile](app-service-web-tutorial-dotnet-sqldatabase.md) [ASP.net hızlı başlangıç](quickstart-dotnet-framework.md) ve ASP.net izleyin.

## <a name="show-supported-net-framework-runtime-versions"></a>Desteklenen .NET Framework çalışma zamanı sürümlerini göster

App Service, Windows örneklerinin desteklenen tüm .NET Framework sürümleri zaten yüklüdür. Kullanabileceğiniz .NET Framework çalışma zamanını ve SDK sürümlerini göstermek için, ' a gidin `https://<app-name>.scm.azurewebsites.net/DebugConsole` ve tarayıcı tabanlı konsolda uygun komutu çalıştırın:

CLR 4 çalışma zamanı sürümleri için (.NET Framework 4 ve üzeri):

```CMD
ls "D:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETFramework\"
```

En son .NET Framework sürümü hemen kullanılamıyor olabilir.

CLR 2 çalışma zamanı sürümleri için (.NET Framework 3,5 ve altı):

```CMD
ls "D:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\"
```

## <a name="show-current-net-framework-runtime-version"></a>Geçerli .NET Framework çalışma zamanı sürümünü göster

[Cloud Shell](https://shell.azure.com)aşağıdaki komutu çalıştırın:

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query netFrameworkVersion
```

Bir değeri, `v4.0` en son CLR 4 sürümünün (.NET Framework 4. x) kullanıldığı anlamına gelir. Bir değeri, `v2.0` clr 2 sürümünün (.NET Framework 3,5) kullanıldığı anlamına gelir.

## <a name="set-net-framework-runtime-version"></a>.NET Framework çalışma zamanı sürümünü ayarla

Varsayılan olarak, App Service ASP.NET uygulamanızı çalıştırmak için desteklenen en son .NET Framework sürümünü kullanır. Uygulamanızı .NET Framework 3,5 kullanarak çalıştırmak için [Cloud Shell](https://shell.azure.com) aşağıdaki komutu çalıştırın (v 2.0, CLR 2 ' yi belirtir):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --net-framework-version v2.0
```

## <a name="access-environment-variables"></a> Ortam değişkenlerine erişme

App Service, uygulama ayarlarınızı ve bağlantı dizelerini uygulama kodunuzun dışında [ayarlayabilirsiniz](configure-common.md#configure-app-settings) . Ardından, standart ASP.NET modelini kullanarak herhangi bir sınıfta bunlara erişebilirsiniz:

```csharp
using System.Configuration;
...
// Get an app setting
ConfigurationManager.AppSettings["MySetting"];
// Get a connection string
ConfigurationManager.ConnectionStrings["MyConnection"];
}
```

App Service ve *web.config* aynı ada sahip bir uygulama ayarı yapılandırırsanız App Service değeri *web.config* değere göre önceliklidir. Yerel *web.config* değeri uygulamada yerel olarak hata ayıklamanıza olanak tanır, ancak App Service değeri, uygulamayı üretim ayarları ile birlikte çalıştırmaya olanak sağlar. Bağlantı dizeleri aynı şekilde çalışır. Bu şekilde, uygulamanızın gizli dizilerini kod deponuzun dışında tutabilir ve kodunuzda değişiklik yapmadan uygun değerlere erişebilirsiniz.

## <a name="deploy-multi-project-solutions"></a>Çoklu proje çözümlerini dağıtma

Visual Studio çözümü birden çok proje içerdiğinde, Visual Studio Publish işlemi dağıtılacak projeyi seçmeyi zaten içerir. Git ile veya ZIP dağıtımı ile olduğu gibi App Service dağıtım altyapısına dağıtırken, derleme Otomasyonu açıkken, App Service dağıtım altyapısı App Service uygulama olarak bulduğu ilk Web sitesini veya Web uygulaması projesini seçer. Uygulama ayarını belirterek App Service hangi projenin kullanılması gerektiğini belirtebilirsiniz `PROJECT` . Örneğin, [Cloud Shell](https://shell.azure.com)aşağıdakileri çalıştırın:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings PROJECT="<project-name>/<project-name>.csproj"
```

## <a name="get-detailed-exceptions-page"></a>Ayrıntılı özel durumlar sayfası al

ASP.NET uygulamanız Visual Studio hata ayıklayıcısında bir özel durum oluşturduğunda, tarayıcıda ayrıntılı bir özel durum sayfası görüntülenir, ancak bu sayfa App Service bir genel hata iletisiyle değiştirilmiştir. Ayrıntılı özel durum sayfasını App Service göstermek için *Web.config* dosyasını açın ve öğesini `<customErrors mode="Off"/>` öğesinin altına ekleyin `<system.web>` . Örnek:

```xml
<system.web>
    <customErrors mode="Off"/>
</system.web>
```

Güncelleştirilmiş *Web.config* uygulamanızı yeniden dağıtın. Artık aynı ayrıntılı özel durum sayfasını görmeniz gerekir.

## <a name="access-diagnostic-logs"></a>Tanılama günlüklerine erişim

[System. Diagnostics. Trace](/dotnet/api/system.diagnostics.trace)kullanarak uygulama kodunuzda tanılama iletileri ekleyebilirsiniz. Örnek: 

```csharp
Trace.TraceError("Record not found!"); // Error trace
Trace.TraceWarning("Possible data loss"); // Warning trace
Trace.TraceInformation("GET /Home/Index"); // Information trace
```

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Öğretici: Azure'da SQL Veritabanı ile ASP.NET uygulaması oluşturma](app-service-web-tutorial-dotnet-sqldatabase.md)
