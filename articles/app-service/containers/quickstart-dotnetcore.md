---
title: Linux üzerinde ASP.NET Core uygulama oluşturma-Azure App Service | Microsoft Docs
description: Linux’ta ilk .NET Core Merhaba Dünya uygulamanızı App Service’e birkaç dakika içinde dağıtın.
keywords: azure app service, web uygulaması, dotnet, core, linux, oss
services: app-service
documentationCenter: ''
author: cephalin
manager: jeconnoc
editor: ''
ms.assetid: c02959e6-7220-496a-a417-9b2147638e2e
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: linux
ms.topic: quickstart
ms.date: 03/27/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 43771392adcbf49c9103ee7ebc4b3febbac11b05
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70071227"
---
# <a name="create-an-aspnet-core-app-in-app-service-on-linux"></a>Linux üzerinde App Service ASP.NET Core uygulama oluşturma

> [!NOTE]
> Bu makalede bir uygulamanın Linux üzerinde App Service'e dağıtımı yapılır. _Windows_üzerinde App Service dağıtmak için bkz. [Azure 'Da ASP.NET Core uygulaması oluşturma](../app-service-web-get-started-dotnet.md).
>

[Linux’ta App Service](app-service-linux-intro.md) Linux işletim sistemini kullanan yüksek oranda ölçeklenebilir, otomatik olarak düzeltme eki uygulayan bir web barındırma hizmeti sağlar. Bu hızlı başlangıçta Linux üzerinde App Service’te [.NET Core](https://docs.microsoft.com/aspnet/core/) uygulaması oluşturma gösterilmektedir. Uygulamayı [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)kullanarak oluşturur ve .NET Core kodunu uygulamaya dağıtmak için git 'i kullanırsınız.

![Azure'da çalışan örnek uygulama](media/quickstart-dotnetcore/dotnet-browse-azure.png)

Mac, Windows veya Linux makinesi kullanarak bu makaledeki adımları izleyebilirsiniz.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Önkoşullar

Bu hızlı başlangıcı tamamlamak için:

* <a href="https://git-scm.com/" target="_blank">Git'i yükleyin</a>
* <a href="https://www.microsoft.com/net/core/" target="_blank">.NET Core’u yükleme</a>

## <a name="create-the-app-locally"></a>Uygulamayı yerel olarak oluşturma

Makinenizde bir terminal penceresinde, `hellodotnetcore` adlı bir dizin oluşturup geçerli dizin olarak değiştirin.

```bash
mkdir hellodotnetcore
cd hellodotnetcore
```

Yeni bir .NET Core uygulaması oluşturun.

```bash
dotnet new web
```

## <a name="run-the-app-locally"></a>Uygulamayı yerel olarak çalıştırma

Azure'a dağıttığınızda nasıl görüneceğini görmek için uygulamayı yerel olarak çalıştırın. 

NuGet paketlerini geri yükleyip uygulamayı çalıştırın.

```bash
dotnet run
```

Bir web tarayıcısı açın ve `http://localhost:5000` konumundaki uygulamaya gidin.

Sayfada gösterilen örnek uygulamada **Hello World** iletisini görebilirsiniz.

![Tarayıcı ile test etme](media/quickstart-dotnetcore/dotnet-browse-local.png)

Terminal pencerenizde **Ctrl+C** tuşlarına basarak web sunucusundan çıkın. .NET Core projesi için bir Git deposu başlatın.

```bash
git init
git add .
git commit -m "first commit"
```

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app"></a>Web uygulaması oluşturma

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-dotnetcore-linux-no-h.md)]

Yeni oluşturduğunuz uygulamanıza gidin. _&lt;App-name >_ değerini uygulamanızın adıyla değiştirin.

```bash
http://<app-name>.azurewebsites.net
```

Yeni uygulamanızın şöyle görünmesi gerekir:

![Boş uygulama sayfası](media/quickstart-dotnetcore/dotnet-browse-created.png)

[!INCLUDE [Push to Azure](../../../includes/app-service-web-git-push-to-azure.md)] 

```bash
Counting objects: 22, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (18/18), done.
Writing objects: 100% (22/22), 51.21 KiB | 3.94 MiB/s, done.
Total 22 (delta 1), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '741f16d1db'.
remote: Generating deployment script.
remote: Project file path: ./hellodotnetcore.csproj
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling ASP.NET Core Web Application deployment.
remote: ...............................................................................................
remote:   Restoring packages for /home/site/repository/hellodotnetcore.csproj...
remote: ....................................
remote:   Installing System.Xml.XPath 4.0.1.
remote:   Installing System.Diagnostics.Tracing 4.1.0.
remote:   Installing System.Threading.Tasks.Extensions 4.0.0.
remote:   Installing System.Reflection.Emit.ILGeneration 4.0.1.
remote:   ...
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
To https://cephalin-dotnetcore.scm.azurewebsites.net/cephalin-dotnetcore.git
 * [new branch]      master -> master
```

## <a name="browse-to-the-app"></a>Uygulamaya göz atma

Web tarayıcınızı kullanarak, dağıtılan uygulamanın konumuna gidin.

```bash
http://<app_name>.azurewebsites.net
```

.NET Core örnek kodu, yerleşik bir görüntüyle Linux üzerinde App Service çalışmaktadır.

![Azure'da çalışan örnek uygulama](media/quickstart-dotnetcore/dotnet-browse-azure.png)

**Tebrikler!** Linux’ta App Service’e ilk .NET Core uygulamanızı dağıttınız.

## <a name="update-and-redeploy-the-code"></a>Kodu güncelleştirme ve yeniden dağıtma

Yerel dizinde, _Startup.cs_ dosyasını açın. `context.Response.WriteAsync` yöntem çağrısında metinde küçük bir değişiklik yapın:

```csharp
await context.Response.WriteAsync("Hello Azure!");
```

Değişikliklerinizi Git’e işleyin ve ardından kod değişikliklerini Azure’a gönderin.

```bash
git commit -am "updated output"
git push azure master
```

Dağıtım tamamlandıktan sonra **Uygulamaya göz atma** adımında açılan tarayıcı penceresine dönüp yenile öğesine dokunun.

![Azure'da çalışan güncelleştirilmiş örnek uygulama](media/quickstart-dotnetcore/dotnet-browse-azure-updated.png)

## <a name="manage-your-new-azure-app"></a>Yeni Azure uygulamanızı yönetin

Oluşturduğunuz uygulamayı yönetmek için <a href="https://portal.azure.com" target="_blank">Azure Portal</a> gidin.

Sol menüden **uygulama hizmetleri**' ne ve ardından Azure uygulamanızın adına tıklayın.

![Azure uygulamasına portal gezintisi](./media/quickstart-dotnetcore/portal-app-service-list.png)

Uygulamanızın genel bakış sayfasını görürsünüz. Buradan göz atma, durdurma, başlatma, yeniden başlatma ve silme gibi temel yönetim görevlerini gerçekleştirebilirsiniz. 

![Azure portalında App Service sayfası](media/quickstart-dotnetcore/portal-app-overview.png)

Soldaki menü, uygulamanızı yapılandırmak için farklı sayfalar sağlar. 

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Öğretici: SQL veritabanı ile ASP.NET Core uygulaması](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [ASP.NET Core uygulamasını yapılandırma](configure-language-dotnetcore.md)
