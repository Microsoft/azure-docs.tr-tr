---
title: Application Insights Profiler ile Azure Linux Web Apps ASP.NET Core profili | Microsoft Docs
description: Application Insights Profiler kullanımı hakkında kavramsal genel bakış ve adım adım öğretici.
ms.topic: conceptual
ms.custom: devx-track-csharp
author: cweining
ms.author: cweining
ms.date: 02/23/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 4c208a80a1f701cc3e0c1c5cd08a999f2f15815e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104889085"
---
# <a name="profile-aspnet-core-azure-linux-web-apps-with-application-insights-profiler"></a>Application Insights Profiler ile Azure Linux Web Apps ASP.NET Core profili

Bu özellik şu anda önizleme sürümündedir.

[Application Insights](./app-insights-overview.md)kullanırken canlı Web uygulamanızın her yönteminde ne kadar zaman harcandığını öğrenin. Application Insights Profiler artık Azure App Service Linux 'ta barındırılan ASP.NET Core Web uygulamaları için kullanılabilir. Bu kılavuz, ASP.NET Core Linux Web Apps için profil oluşturucu izlemelerinin nasıl toplanabilecek hakkında adım adım yönergeler sağlar.

Bu yönergeyi tamamladıktan sonra, uygulamanız görüntüde gösterilen izlemeler gibi profil oluşturucu izlemeleri toplayabilir. Bu örnekte, profil oluşturucu izlemesi belirli bir Web isteğinin, beklerken geçen süre nedeniyle yavaş olduğunu gösterir. Uygulamanın yavaştığı koddaki *etkin yol* bir alev simgesiyle işaretlenir. **HomeController** bölümündeki **hakkında** yöntemi, yöntemi **Thread. Sleep** işlevini çağırırken Web uygulamasını yavaşlatmakta.

![Profil Oluşturucu izlemeleri](./media/profiler-aspnetcore-linux/profiler-traces.png)

## <a name="prerequisites"></a>Önkoşullar
Aşağıdaki yönergeler tüm Windows, Linux ve Mac geliştirme ortamları için geçerlidir:

* [.NET Core SDK 2.1.2 'yi veya üstünü](https://dotnet.microsoft.com/download/archives)yükler.
* Çalışmaya başlama ve [Git yükleme](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)bölümündeki yönergeleri izleyerek git ' i yükleme.

## <a name="set-up-the-project-locally"></a>Projeyi yerel olarak ayarlama

1. Makinenizde bir komut Istemi penceresi açın. Aşağıdaki yönergeler tüm Windows, Linux ve Mac geliştirme ortamlarında çalışır.

1. ASP.NET Core MVC web uygulaması oluşturma:

   ```console
   dotnet new mvc -n LinuxProfilerTest
   ```

1. Çalışma dizinini projenin kök klasörüyle değiştirin.

1. Profil Oluşturucu izlemelerini toplamak için NuGet paketini ekleyin:

   ```console
   dotnet add package Microsoft.ApplicationInsights.Profiler.AspNetCore
   ```

1. Başlangıçta Application Insights ve profil oluşturucuyu etkinleştirin. CS:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry(); // Add this line of code to enable Application Insights.
        services.AddServiceProfiler(); // Add this line of code to Enable Profiler
        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
    }
    ```

1. Birkaç saniye rastgele gecikme yapmak için **HomeController. cs** bölümüne bir kod satırı ekleyin:

    ```csharp
    using System.Threading;
    ...

    public IActionResult About()
        {
            Random r = new Random();
            int delay = r.Next(5000, 10000);
            Thread.Sleep(delay);
            return View();
        }
    ```

1. Değişikliklerinizi yerel depoya kaydedin ve işleyin:

    ```console
    git init
    git add .
    git commit -m "first commit"
    ```

## <a name="create-the-linux-web-app-to-host-your-project"></a>Projenizi barındırmak için Linux Web uygulaması oluşturma

1. Linux üzerinde App Service kullanarak Web uygulaması ortamını oluşturun:

    ![Linux Web uygulaması oluşturma](./media/profiler-aspnetcore-linux/create-linux-appservice.png)

2. Dağıtım kimlik bilgilerini oluşturun:

    > [!NOTE]
    > Web uygulamanızı dağıttığınızda daha sonra kullanmak için parolanızı kaydedin.

    ![Dağıtım kimlik bilgilerini oluşturma](./media/profiler-aspnetcore-linux/create-deployment-credentials.png)

3. Dağıtım seçeneklerini belirleyin. Azure portal yönergeleri izleyerek Web uygulamasında yerel bir git deposu ayarlayın. Git deposu otomatik olarak oluşturulur.

    ![Git deposunu ayarlama](./media/profiler-aspnetcore-linux/setup-git-repo.png)

Daha fazla dağıtım seçeneği için [App Service belgelerine](../../app-service/index.yml)bakın.

## <a name="deploy-your-project"></a>Projenizi dağıtma

1. Komut Istemi penceresinde, projeniz için kök klasöre gidin. App Service depoyu işaret etmek için bir git uzak deposu ekleyin:

    ```console
    git remote add azure https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git
    ```

    * Dağıtım kimlik bilgilerini oluşturmak için kullandığınız **Kullanıcı adını** kullanın.
    * Linux üzerinde App Service kullanarak Web uygulaması oluşturmak için kullandığınız **uygulama adını** kullanın.

2. Değişiklikleri Azure 'a göndererek projeyi dağıtın:

    ```console
    git push azure main
    ```

    Aşağıdaki örneğe benzer bir çıktı görmeniz gerekir:

    ```output
    Counting objects: 9, done.
    Delta compression using up to 8 threads.
    Compressing objects: 100% (8/8), done.
    Writing objects: 100% (9/9), 1.78 KiB | 911.00 KiB/s, done.
    Total 9 (delta 3), reused 0 (delta 0)
    remote: Updating branch 'main'.
    remote: Updating submodules.
    remote: Preparing deployment for commit id 'd7369a99d7'.
    remote: Generating deployment script.
    remote: Running deployment command...
    remote: Handling ASP.NET Core Web Application deployment.
    remote: ......
    remote:   Restoring packages for /home/site/repository/EventPipeExampleLinux.csproj...
    remote: .
    remote:   Installing Newtonsoft.Json 10.0.3.
    remote:   Installing Microsoft.ApplicationInsights.Profiler.Core 1.1.0-LKG
    ...
    ```

## <a name="add-application-insights-to-monitor-your-web-apps"></a>Web uygulamalarınızı izlemek için Application Insights ekleyin

1. [Application Insights kaynağı oluşturun](./create-new-resource.md).

2. Application Insights kaynağın **Ikey** değerini kopyalayın ve Web uygulamalarınızda aşağıdaki ayarları ayarlayın:

    `APPINSIGHTS_INSTRUMENTATIONKEY: [YOUR_APPINSIGHTS_KEY]`

    Uygulama ayarları değiştirildiğinde, site otomatik olarak yeniden başlatılır. Yeni ayarlar uygulandıktan sonra, profil oluşturucu hemen iki dakika boyunca çalışır. Profil Oluşturucu daha sonra saatte iki dakika çalışır.

3. Web sitenizde bazı trafik oluşturun. Site **hakkında** sayfayı birkaç kez yenileyerek trafik oluşturabilirsiniz.

4. Olayların Application Insights olması için iki ila beş dakika bekleyin.

5. Azure portal Application Insights **performans** bölmesine gidin. Pano 'nun sağ alt kısmındaki profil oluşturucu izlerini görüntüleyebilirsiniz.

    ![Profil Oluşturucu izlemelerini görüntüle](./media/profiler-aspnetcore-linux/view-traces.png)



## <a name="next-steps"></a>Sonraki adımlar
Azure App Service tarafından barındırılan özel kapsayıcılar kullanıyorsanız, [ kapsayıcılı bir ASP.NET Core uygulaması için](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/tree/master/examples/EnableServiceProfilerForContainerApp) Application Insights Profiler etkinleştirmek üzere Service Profiler etkinleştir bölümündeki yönergeleri izleyin.

Application Insights GitHub deposuna tüm sorunları veya önerileri bildirin: [ApplicationInsights-Profiler-AspNetCore: sorunlar](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/issues).
