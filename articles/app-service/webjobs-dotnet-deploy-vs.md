---
title: Visual Studio kullanarak Web Işleri geliştirme ve dağıtma-Azure
description: Visual Studio 'Yu kullanarak Azure App Service Azure Web Işleri geliştirmeyi ve dağıtmayı öğrenin.
author: ggailey777
manager: gwallace
ms.assetid: a3a9d320-1201-4ac8-9398-b4c9535ba755
ms.service: app-service
ms.topic: conceptual
ms.custom: vs-azure
ms.date: 02/18/2019
ms.author: glenga
ms.reviewer: david.ebbo;suwatch;pbatum;naren.soni
ms.openlocfilehash: ac458b01135be8628fbf939e310f8bda02b8d290
ms.sourcegitcommit: 9858ab651a520c26f0ed18215e650efbf1fc5de9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/14/2019
ms.locfileid: "72303557"
---
# <a name="develop-and-deploy-webjobs-using-visual-studio---azure-app-service"></a>Visual Studio kullanarak Web Işleri geliştirme ve dağıtma-Azure App Service

Bu makalede, bir konsol uygulama projesini [Azure WebJob](https://go.microsoft.com/fwlink/?LinkId=390226)olarak [App Service](overview.md) bir Web uygulamasına dağıtmak için Visual Studio 'nun nasıl kullanılacağı açıklanmaktadır. [Azure Portal](https://portal.azure.com)kullanarak WebJobs dağıtma hakkında daha fazla bilgi için bkz. [WebJobs ile arka plan görevleri çalıştırma](webjobs-create.md).

Birden çok Web Işini tek bir Web uygulamasına yayımlayabilirsiniz. Web uygulamasındaki her WebJob 'un benzersiz bir ada sahip olduğundan emin olun.

[Azure WebJobs SDK 'sının](webjobs-sdk-how-to.md) 3. x sürümü, .NET Core uygulamaları veya .NET Framework uygulamaları olarak çalışan Web işleri geliştirmenize olanak sağlarken, sürüm 2. x yalnızca .NET Framework destekler. WebJobs projesi dağıtmanın yolu, .NET Core projeleri için .NET Framework farklıdır.

## <a name="webjobs-as-net-core-console-apps"></a>.NET Core konsol uygulamaları olarak Web Işleri

WebJobs 'un 3. x sürümünü kullanırken, Web Işlerini .NET Core konsol uygulamaları olarak oluşturup yayımlayabilirsiniz. .NET Core konsol uygulamasını bir Web Işi olarak Azure 'da oluşturup yayımlamaya yönelik adım adım yönergeler için bkz. [olay odaklı arka plan işleme Için Azure WebJobs SDK ile çalışmaya başlama](webjobs-sdk-get-started.md).

> [!NOTE]
> .NET Core Web Işleri Web projeleriyle bağlanamaz. WebJob uygulamanızı bir Web uygulamasıyla dağıtmanız gerekiyorsa, [WebJob uygulamanızı bir .NET Framework konsol uygulaması olarak oluşturmanız](#webjobs-as-net-framework-console-apps)gerekir.  

### <a name="deploy-to-azure-app-service"></a>Azure App Service’e dağıtma

.NET Core WebJob 'u Visual Studio 'dan App Service yayımlamak, bir ASP.NET Core uygulaması yayımlamayla aynı araçları kullanır.

[!INCLUDE [webjobs-publish-net-core](../../includes/webjobs-publish-net-core.md)] 

### <a name="webjob-types"></a>WebJob türleri

Varsayılan olarak, .NET Core konsol projesinden yayımlanan bir WebJob yalnızca tetiklendiğinde veya istek üzerine geldiğinde çalışır. Ayrıca, projeyi [bir zamanlamaya göre çalışacak](#scheduled-execution) şekilde güncelleştirebilir veya sürekli olarak çalıştırabilirsiniz.

[!INCLUDE [webjobs-alwayson-note](../../includes/webjobs-always-on-note.md)]

#### <a name="scheduled-execution"></a>Zamanlanan yürütme

Azure 'da bir .NET Core konsol uygulaması yayımladığınızda, projeye yeni bir *ayarlar. iş* dosyası eklenir. WebJob için bir yürütme zamanlaması ayarlamak üzere bu dosyayı kullanın. Daha fazla bilgi için bkz. [tetiklenen WebJob zamanlama](#scheduling-a-triggered-webjob).

#### <a name="continuous-execution"></a>Sürekli yürütme

Azure 'da her zaman açık etkinleştirildiğinde WebJob 'u sürekli çalışacak şekilde değiştirmek için Visual Studio 'Yu kullanabilirsiniz.

1. Daha önce yapmadıysanız, [projeyi Azure 'da yayımlayın](#deploy-to-azure-app-service).

1. **Çözüm Gezgini**'nde projeye sağ tıklayın ve **Yayımla**'yı seçin.

1. **Yayımla** sekmesinde **Ayarlar**' ı seçin. 

1. **Profil ayarları** iletişim kutusunda, **WebJob türü**için **sürekli** ' i seçin ve **Kaydet**' i seçin.

    ![WebJob için ayarları Yayımla iletişim kutusu](./media/webjobs-dotnet-deploy-vs/publish-settings.png)

1. Web işini güncelleştirilmiş ayarlarla yeniden yayımlamak için **Yayımla** ' yı seçin.

## <a name="webjobs-as-net-framework-console-apps"></a>Web Işleri .NET Framework konsol uygulamaları olarak  

Visual Studio, bir WebJobs özellikli .NET Framework konsol uygulama projesi dağıttığında, çalışma zamanı dosyalarını Web uygulamasındaki uygun klasöre (*App_Data/işler/* sürekli Web işleri için sürekli ve *App_Data/işler/tetiklenen* planlanmış veya isteğe bağlı Web Işleri).

Web Işleri etkin bir proje, öğesine eklenen aşağıdaki öğeleri içerir:

* [Microsoft. Web. WebJobs.](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) NuGet paketini yayımlayın.
* Dağıtım ve zamanlayıcı ayarlarını içeren bir [WebJob-Publish-Settings. JSON](#publishsettings) dosyası. 

![Bir Web Işi olarak dağıtımı etkinleştirmek üzere konsol uygulamasına nelerin ekleneceğini gösteren diyagram](./media/webjobs-dotnet-deploy-vs/convert.png)

Bu öğeleri varolan bir konsol uygulaması projesine ekleyebilir veya yeni bir Web Işleri etkinleştirilmiş konsol uygulaması projesi oluşturmak için şablon kullanabilirsiniz. 

Bir projeyi bir Web Işi olarak dağıtabilir veya Web projesini her dağıttığınızda otomatik olarak dağıtması için bir web projesine bağlayabilirsiniz. Visual Studio, projeleri bağlamak için Web projesindeki bir [WebJobs-List. JSON](#webjobslist) dosyasındaki WebJobs özellikli projenin adını içerir.

![Web projesine yönelik WebJob projesi bağlantısını gösteren diyagram](./media/webjobs-dotnet-deploy-vs/link.png)

### <a name="prerequisites"></a>Önkoşullar

Visual Studio 2015 kullanıyorsanız [.net Için Azure SDK 'sını (Visual studio 2015)](https://azure.microsoft.com/downloads/)yükleyebilirsiniz.

Visual Studio 2017 kullanıyorsanız, [Azure geliştirme iş yükünü](https://docs.microsoft.com/visualstudio/install/install-visual-studio#step-4---choose-workloads)yükleyebilirsiniz.

### <a id="convert"></a>Mevcut bir konsol uygulaması projesi için Web Işleri dağıtımını etkinleştir

İki seçeneğiniz vardır:

* [Bir Web projesiyle otomatik dağıtımı etkinleştirin](#convertlink).

  Bir Web projesi dağıtırken, var olan bir konsol uygulaması projesini otomatik olarak WebJob olarak dağıtması için yapılandırın. WebJob uygulamanızı ilgili Web uygulamasını çalıştırdığınız Web uygulamasında çalıştırmak istediğinizde bu seçeneği kullanın.

* [Web projesi olmadan dağıtımı etkinleştirin](#convertnolink).

  Mevcut bir konsol uygulaması projesini, bir web projesine bağlantı olmadan, kendi başına WebJob olarak dağıtılacak şekilde yapılandırın. Web uygulamasında Web uygulaması üzerinde çalışan bir Web uygulaması olmadan, bir Web uygulamasında bir WebJob çalıştırmak istediğinizde bu seçeneği kullanın. Web uygulaması kaynaklarınızdan bağımsız olarak WebJob kaynaklarınızı ölçeklendirebilmek için bunu yapmak isteyebilirsiniz.

#### <a id="convertlink"></a>Web projesi ile otomatik Web Işleri dağıtımını etkinleştirme

1. **Çözüm Gezgini**web projesine sağ tıklayın ve ardından  > **mevcut projeyi Azure WebJob olarak** **Ekle**' ye tıklayın.
   
    ![Azure WebJob olarak mevcut proje](./media/webjobs-dotnet-deploy-vs/eawj.png)
   
    [Azure WebJob Ekle](#configure) iletişim kutusu görüntülenir.
2. **Proje adı** açılır listesinde, WebJob olarak eklenecek konsol uygulama projesini seçin.
   
    ![Azure WebJob Ekle iletişim kutusunda proje seçiliyor](./media/webjobs-dotnet-deploy-vs/aaw1.png)
3. [Azure WebJob Ekle](#configure) iletişim kutusunu doldurun ve ardından **Tamam**' a tıklayın. 

#### <a id="convertnolink"></a>Web projesi olmadan WebJobs dağıtımını etkinleştirme
1. **Çözüm Gezgini**' de konsol uygulaması projesine sağ tıklayın ve ardından **Azure WebJob olarak Yayımla...** ' ya tıklayın. 
   
    ![Azure WebJob olarak Yayımla](./media/webjobs-dotnet-deploy-vs/paw.png)
   
    **Proje adı** kutusunda seçilen proje Ile [Azure WebJob Ekle](#configure) iletişim kutusu görünür.
2. [Azure WebJob Ekle](#configure) iletişim kutusunu doldurun ve ardından **Tamam**' a tıklayın.
   
   **Web 'ı Yayımla** Sihirbazı görüntülenir.  Hemen yayınlamak istemiyorsanız, Sihirbazı kapatın. Girdiğiniz ayarlar, [projeyi dağıtmak](#deploy)istediğinizde için kaydedilir.

### <a id="create"></a>Yeni bir Web Işleri etkin proje oluşturun
Yeni bir Web Işleri etkin proje oluşturmak için konsol uygulaması proje şablonunu kullanabilir ve [önceki bölümde](#convert)açıklandığı gibi Web işleri dağıtımını etkinleştirebilirsiniz. Alternatif olarak, Web Işleri yeni-proje şablonunu kullanabilirsiniz:

* [Bağımsız bir WebJob için WebJobs yeni-proje şablonunu kullanın](#createnolink)
  
    Bir proje oluşturun ve bir Web projesi bağlantısı olmadan kendisini bir WebJob olarak dağıtmak üzere yapılandırın. Web uygulamasında Web uygulaması üzerinde çalışan bir Web uygulaması olmadan, bir Web uygulamasında bir WebJob çalıştırmak istediğinizde bu seçeneği kullanın. Web uygulaması kaynaklarınızdan bağımsız olarak WebJob kaynaklarınızı ölçeklendirebilmek için bunu yapmak isteyebilirsiniz.
* [Web projesine bağlı bir WebJob için WebJobs yeni-proje şablonunu kullanın](#createlink)
  
    Aynı çözümde bir Web projesi dağıtıldığında, WebJob olarak otomatik olarak dağıtılacak şekilde yapılandırılan bir proje oluşturun. WebJob uygulamanızı ilgili Web uygulamasını çalıştırdığınız Web uygulamasında çalıştırmak istediğinizde bu seçeneği kullanın.

> [!NOTE]
> Web Işleri yeni-proje şablonu, NuGet paketlerini otomatik olarak kurar ve [Web İşleri SDK 'sı](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs)için *program.cs* içinde kod içerir. WebJobs SDK 'sını kullanmak istemiyorsanız, *program.cs*içinde `host.RunAndBlock` ifadesini kaldırın veya değiştirin.
> 
> 

#### <a id="createnolink"></a>Bağımsız bir WebJob için WebJobs yeni-proje şablonunu kullanın
1. **Dosya** > **Yeni proje**' ye tıklayın ve ardından **yeni proje** Iletişim kutusunda **bulut** > **Azure WebJob (.NET Framework)** seçeneğine tıklayın.
   
    ![WebJob şablonunu gösteren yeni proje iletişim kutusu](./media/webjobs-dotnet-deploy-vs/np.png)
2. [Konsol uygulaması projesini bağımsız bir WebJobs projesi haline getirmek](#convertnolink)için daha önce gösterilen yönergeleri izleyin.

#### <a id="createlink"></a>Web projesine bağlı bir WebJob için WebJobs yeni-proje şablonunu kullanın
1. **Çözüm Gezgini**web projesine sağ tıklayın ve ardından  > **yeni Azure WebJob projesi** **Ekle**' ye tıklayın.
   
    ![Yeni Azure WebJob projesi menü girdisi](./media/webjobs-dotnet-deploy-vs/nawj.png)
   
    [Azure WebJob Ekle](#configure) iletişim kutusu görüntülenir.
2. [Azure WebJob Ekle](#configure) iletişim kutusunu doldurun ve ardından **Tamam**' a tıklayın.

### <a id="configure"></a>Azure WebJob Ekle iletişim kutusu
**Azure WebJob Ekle** iletişim kutusu, WebJob için WebJob adı ve çalıştırma modu ayarını girmenize olanak sağlar. 

![Azure WebJob iletişim kutusu Ekle](./media/webjobs-dotnet-deploy-vs/aaw2.png)

Bu iletişim kutusundaki alanlar, Azure portal **WebJob Ekle** iletişim kutusunda bulunan alanlara karşılık gelir. Daha fazla bilgi için bkz. [WebJobs Ile arka plan görevleri çalıştırma](webjobs-create.md).

> [!NOTE]
> * Komut satırı dağıtımı hakkında daha fazla bilgi için bkz. [Azure WebJobs 'un komut satırı veya sürekli teslimini etkinleştirme](https://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/).
> * Bir WebJob dağıtırsanız ve sonra WebJob ve yeniden dağıtma türünü değiştirmek istediğinize karar verirseniz, *WebJobs-Publish-Settings. JSON* dosyasını silmeniz gerekir. Bu, Visual Studio 'nun yayımlama seçeneklerini yeniden göstermesini sağlayacak ve bu sayede WebJob türünü değiştirebilirsiniz.
> * Bir WebJob dağıtırsanız ve daha sonra çalışma modunu sürekli olarak sürekli olmayan veya tam tersi olarak değiştirirseniz, Visual Studio yeniden dağıtırken Azure 'da yeni bir WebJob oluşturur. Diğer zamanlama ayarlarını değiştirir ancak çalıştırma modunu aynı veya zamanlanmış ve Isteğe bağlı olarak bırakırsanız, Visual Studio yeni bir tane oluşturmak yerine mevcut işi güncelleştirir.
> 
> 

### <a id="publishsettings"></a>WebJob-Publish-Settings. JSON
Web Işleri dağıtımı için bir konsol uygulaması yapılandırdığınızda, Visual Studio [Microsoft. Web. WebJobs.](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) NuGet paketini yayımlar ve zamanlama bilgilerini projedeki *WebJob-Publish-Settings. json dosyasında depolar* WebJobs projesinin Özellikler klasörü. Bu dosyaya bir örnek aşağıda verilmiştir:

        {
          "$schema": "http://schemastore.org/schemas/json/webjob-publish-settings.json",
          "webJobName": "WebJob1",
          "startTime": "null",
          "endTime": "null",
          "jobRecurrenceFrequency": "null",
          "interval": null,
          "runMode": "Continuous"
        }

Bu dosyayı doğrudan düzenleyebilirsiniz ve Visual Studio IntelliSense sağlar. Dosya şeması [https://schemastore.org](https://schemastore.org/schemas/json/webjob-publish-settings.json) ' de depolanır ve burada görüntülenebilir.  

### <a id="webjobslist"></a>WebJobs-List. JSON
WebJobs özellikli bir projeyi bir web projesine bağladığınızda, Visual Studio WebJobs projesinin adını Web projesinin *Özellikler* klasöründeki bir *WebJobs-List. JSON* dosyasında depolar. Listede, aşağıdaki örnekte gösterildiği gibi birden çok WebJobs projesi bulunabilir:

        {
          "$schema": "http://schemastore.org/schemas/json/webjobs-list.json",
          "WebJobs": [
            {
              "filePath": "../ConsoleApplication1/ConsoleApplication1.csproj"
            },
            {
              "filePath": "../WebJob1/WebJob1.csproj"
            }
          ]
        }

Bu dosyayı doğrudan düzenleyebilirsiniz ve Visual Studio IntelliSense sağlar. Dosya şeması [https://schemastore.org](https://schemastore.org/schemas/json/webjobs-list.json) ' de depolanır ve burada görüntülenebilir.

### <a id="deploy"></a>WebJobs projesi dağıtma
Web projesine bağladığınız bir WebJobs projesi, Web projesi ile otomatik olarak dağıtılır. Web projesi dağıtımı hakkında daha fazla bilgi için bkz. **nasıl yapılır kılavuzu** >  uygulamayı sol gezinmede**dağıtma** .

Bir WebJobs projesini kendi kendine dağıtmak için **Çözüm Gezgini** ' de projeye sağ tıklayın ve **Azure WebJob olarak Yayımla...** öğesine tıklayın. 

![Azure WebJob olarak Yayımla](./media/webjobs-dotnet-deploy-vs/paw.png)

Bağımsız bir WebJob için Web projeleri için kullanılan aynı **Web yayımlama** Sihirbazı görüntülenir, ancak daha az ayarı değiştirilebilir.

## <a name="scheduling-a-triggered-webjob"></a>Tetiklenen bir WebJob zamanlama

Web Işleri bir WebJob 'un ne zaman çalıştırılacağını anlamak için bir *Settings. job* dosyası kullanır. WebJob için bir yürütme zamanlaması ayarlamak üzere bu dosyayı kullanın. Aşağıdaki örnek, 9 ' dan 5 ' e kadar her saat çalışır:

```json
{
    "schedule": "0 0 9-17 * * *"
}
```

Bu dosya, Web işi klasörünün kökünde, örneğin `wwwroot\app_data\jobs\triggered\{job name}` veya `wwwroot\app_data\jobs\continuous\{job name}` gibi. Visual Studio 'dan bir WebJob dağıttığınızda, `settings.job` dosya özelliklerinizi **daha yeniyse kopyala**olarak işaretleyin. 

[Azure Portal bir WebJob oluşturduğunuzda](webjobs-create.md), Settings. job dosyası sizin için oluşturulur.

[!INCLUDE [webjobs-alwayson-note](../../includes/webjobs-always-on-note.md)]

### <a name="cron-expressions"></a>CRON ifadeleri

WebJobs, Azure Işlevlerinde Zamanlayıcı tetikleyicisi olarak zamanlama için aynı CRON ifadelerini kullanır. CRON desteği hakkında daha fazla bilgi edinmek için bkz. [süreölçer tetikleme başvurusu makalesi](../azure-functions/functions-bindings-timer.md#ncrontab-expressions).

[!INCLUDE [webjobs-cron-timezone-note](../../includes/webjobs-cron-timezone-note.md)]

### <a name="settingjob-reference"></a>ayar. iş başvurusu

Web Işleri tarafından aşağıdaki ayarlar desteklenir:

| **Ayar** | **Tür**  | **Açıklama** |
| ----------- | --------- | --------------- |
| `is_in_place` | Tümü | İşin geçici bir klasöre kopyalanmadan yerinde çalışmasına izin verir. Daha fazla bilgi için bkz. [WebJobs çalışma dizini](https://github.com/projectkudu/kudu/wiki/WebJobs#webjob-working-directory). |
| `is_singleton` | Sürekli | Web Işlerini yalnızca ölçeklendirildiğinde tek bir örnek üzerinde çalıştırın. Daha fazla bilgi edinmek için bkz. [tek bir iş olarak sürekli olarak ayarlama](https://github.com/projectkudu/kudu/wiki/WebJobs-API#set-a-continuous-job-as-singleton). |
| `schedule` | Diğini | WebJob 'u bir CRON tabanlı zamanlamaya göre çalıştırın. Daha fazla bilgi edinmek IÇIN [Zamanlayıcı tetikleyici başvurusu makalesine](../azure-functions/functions-bindings-timer.md#ncrontab-expressions)bakın. |
| `stopping_wait_time`| Tümü | , Kapatılma davranışının denetimine izin verir. Daha fazla bilgi için bkz. [düzgün kapanma](https://github.com/projectkudu/kudu/wiki/WebJobs#graceful-shutdown). |

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Web Işleri SDK 'Sı hakkında daha fazla bilgi edinin](webjobs-sdk-how-to.md)
