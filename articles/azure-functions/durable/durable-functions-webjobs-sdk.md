---
title: Dayanıklı İşlevler Web Işleri olarak çalıştırma-Azure
description: WebJobs SDK kullanarak Web Işlerinde çalıştırmak üzere Dayanıklı İşlevler nasıl kodleyeceğinizi ve yapılandıracağınızı öğrenin.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 04/25/2018
ms.author: azfuncdf
ms.openlocfilehash: 06f2019dbaff390e88c73d1aae7a635a34a64721
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73614619"
---
# <a name="how-to-run-durable-functions-as-webjobs"></a>Web Işleri olarak Dayanıklı İşlevler çalıştırma

Dayanıklı İşlevler, varsayılan olarak, özellikleri barındırmak için Azure Işlevleri çalışma zamanı 'nı kullanır. Ancak, olayları dinleyen kod üzerinde daha fazla denetime ihtiyaç duyduğunuz bazı senaryolar olabilir. Bu makalede, Web Işleri SDK 'sını kullanarak Orchestration uygulamanızı nasıl uygulayacağınızı gösterilmektedir. Işlevler ve Web Işleri arasında daha ayrıntılı bir karşılaştırma görmek için bkz. [karşılaştırma işlevleri ve Web işleri](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs).

[Azure işlevleri](../functions-overview.md) ve [dayanıklı işlevler](durable-functions-overview.md) uzantısı, [Web İşleri SDK 'sı](../../app-service/webjobs-sdk-how-to.md)üzerine kurulmuştur. WebJobs SDK 'daki iş Konağı, Azure Işlevlerinde çalışma zamanı ' dır. Davranışı Azure Işlevlerinde mümkün olmadığı yollarla denetmeniz gerekiyorsa, WebJobs SDK 'sını kullanarak Dayanıklı İşlevler geliştirip çalıştırabilirsiniz.

Web Işleri SDK 'sının 3. x sürümünde, ana bilgisayar `IHost`uygulamasıdır ve sürüm 2. x içinde `JobHost` nesnesini kullanırsınız.

Zincirleme Dayanıklı İşlevler örneği bir WebJobs SDK 2. x sürümünde bulunabilir: [dayanıklı işlevler deposunu](https://github.com/azure/azure-functions-durable-extension/)indirip kopyalayıp klonlayın ve *örneklere\\webjobssdk\\zincirleme* klasörüne gidin.

## <a name="prerequisites"></a>Ön koşullar

Bu makalede WebJobs SDK 'nın temel bilgileri, C# Azure işlevleri için sınıf kitaplığı geliştirme ve dayanıklı işlevler hakkında bilgi sahibi olduğunuz varsayılmaktadır. Bu konulara giriş yapmanız gerekiyorsa, aşağıdaki kaynaklara bakın:

* [Web Işleri SDK 'sını kullanmaya başlama](../../app-service/webjobs-sdk-get-started.md)
* [Visual Studio kullanarak ilk işlevinizi oluşturma](../functions-create-your-first-function-visual-studio.md)
* [Dayanıklı İşlevler](durable-functions-sequence.md)

Bu makaledeki adımları gerçekleştirmek için:

* **Azure geliştirme** iş yüküyle [Visual Studio 2019 ' ü yükler](https://docs.microsoft.com/visualstudio/install/) .

  Zaten Visual Studio 'Ya sahipseniz, ancak bu iş yüküne sahip değilseniz Araçlar **ve Özellikler al** > **Araçlar** ' ı seçerek iş yükünü ekleyin.

  (Bunun yerine [Visual Studio Code](https://code.visualstudio.com/) kullanabilirsiniz, ancak bazı yönergeler Visual Studio 'ya özeldir.)

* [Azure Storage öykünücüsü](../../storage/common/storage-use-emulator.md) sürüm 5,2 veya üstünü yükleyip çalıştırın. Alternatif olarak, *app. config* dosyasını bir Azure depolama bağlantı dizesiyle güncelleştirin.

## <a name="webjobs-sdk-versions"></a>WebJobs SDK sürümleri

Bu makalede bir WebJobs SDK 2. x projesi geliştirme (Azure Işlevleri sürüm 1. x ile eşdeğer) açıklanmaktadır. Sürüm 3. x hakkında daha fazla bilgi için bu makalenin ilerleyen kısımlarında bulunan [WebJobs SDK 3. x](#webjobs-sdk-3x) bölümüne bakın.

## <a name="create-a-console-app"></a>Konsol uygulaması oluşturma

Dayanıklı İşlevler Web Işleri olarak çalıştırmak için, önce bir konsol uygulaması oluşturmanız gerekir. Bir WebJobs SDK projesi, uygun NuGet paketlerinin yüklü olduğu bir konsol uygulaması projem.

Visual Studio **Yeni proje** Iletişim kutusunda **Windows Klasik Masaüstü** > konsol uygulaması ' nı **(.NET Framework)** seçin. Proje dosyasında, `TargetFrameworkVersion` `v4.6.1`olmalıdır.

Visual Studio 'da Ayrıca **Azure WebJob (.NET Framework)**  > **Cloud** ' ı seçerek kullanabileceğiniz bir WebJob proje şablonu vardır. Bu şablon, bazıları ihtiyaç duymayan birçok paketi de yüklüyor.

## <a name="install-nuget-packages"></a>NuGet paketlerini yükleme

WebJobs SDK, çekirdek bağlamaları, günlük çerçevesi ve dayanıklı görev uzantısı için NuGet paketlerine ihtiyacınız vardır. Bu paket için **Paket Yöneticisi konsol** komutları, bu makalenin yazıldığı tarih itibarıyla en son kararlı sürüm numaralarıyla birlikte verilmiştir:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions -version 2.2.0
Install-Package Microsoft.Extensions.Logging -version 2.0.1
Install-Package Microsoft.Azure.WebJobs.Extensions.DurableTask -version 1.8.3
```

Ayrıca günlüğe kaydetme sağlayıcılarının olması gerekir. Aşağıdaki komutlar Azure Application Insights sağlayıcısını ve `ConfigurationManager`yükler. `ConfigurationManager`, uygulama ayarlarından Application Insights izleme anahtarını almanızı sağlar.

```powershell
Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -version 2.2.0
Install-Package System.Configuration.ConfigurationManager -version 4.4.1
```

Aşağıdaki komut konsol sağlayıcısını yüklemektedir:

```powershell
Install-Package Microsoft.Extensions.Logging.Console -version 2.0.1
```

## <a name="jobhost-code"></a>JobHost kodu

Konsol uygulamasını oluşturup ihtiyacınız olan NuGet paketlerini yükle, Dayanıklı İşlevler kullanmaya hazırsınız. Bunu JobHost Code kullanarak yapabilirsiniz.

Dayanıklı İşlevler uzantısını kullanmak için `Main` yönteminizin `JobHostConfiguration` nesnesinde `UseDurableTask` çağırın:

```cs
var config = new JobHostConfiguration();
config.UseDurableTask(new DurableTaskExtension
{
    HubName = "MyTaskHub",
};
```

`DurableTaskExtension` nesnesinde ayarlayabilmeniz gereken özelliklerin listesi için bkz. [Host. JSON](../functions-host-json.md#durabletask).

`Main` yöntemi, günlük sağlayıcılarını ayarlamak için de yer vardır. Aşağıdaki örnek, konsolu ve Application Insights sağlayıcılarını yapılandırır.

```cs
static void Main(string[] args)
{
    using (var loggerFactory = new LoggerFactory())
    {
        var config = new JobHostConfiguration();

        config.DashboardConnectionString = "";

        var instrumentationKey =
            ConfigurationManager.AppSettings["APPINSIGHTS_INSTRUMENTATIONKEY"];

        config.LoggerFactory = loggerFactory
            .AddApplicationInsights(instrumentationKey, null)
            .AddConsole();

        config.UseTimers();
        config.UseDurableTask(new DurableTaskExtension
        {
            HubName = "MyTaskHub",
        });
        var host = new JobHost(config);
        host.RunAndBlock();
    }
}
```

## <a name="functions"></a>İşlevler

WebJobs bağlamındaki Dayanıklı İşlevler, Azure Işlevleri bağlamında Dayanıklı İşlevler biraz farklılık gösterir. Kodunuzu yazarken farkları bilmeniz önemlidir.

WebJobs SDK aşağıdaki Azure Işlevleri özelliklerini desteklemez:

* [Fonksiyonadı özniteliği](#functionname-attribute)
* [HTTP tetikleyicisi](#http-trigger)
* [Dayanıklı İşlevler HTTP yönetim API 'SI](#http-management-api)

### <a name="functionname-attribute"></a>Fonksiyonadı özniteliği

Bir WebJobs SDK projesinde, işlevin Yöntem adı işlev adıdır. `FunctionName` özniteliği yalnızca Azure Işlevlerinde kullanılır.

### <a name="http-trigger"></a>HTTP tetikleyicisi

WebJobs SDK 'sının HTTP tetikleyicisi yok. Örnek projenin düzenleme istemcisi bir Zamanlayıcı tetikleyicisi kullanır:

```cs
public static async Task CronJob(
    [TimerTrigger("0 */2 * * * *")] TimerInfo timer,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger logger)
{
  ...
}
```

### <a name="http-management-api"></a>HTTP yönetim API 'SI

HTTP tetikleyicisi olmadığından, WebJobs SDK 'sının [http yönetim API 'si](durable-functions-http-api.md)yoktur.

Bir WebJobs SDK projesinde, HTTP istekleri göndermek yerine Orchestration istemci nesnesi üzerinde yöntemler çağırabilirsiniz. Aşağıdaki yöntemler, HTTP yönetim API 'SI ile gerçekleştirebileceğiniz üç göreve karşılık gelir:

* `GetStatusAsync`
* `RaiseEventAsync`
* `TerminateAsync`

Örnek projedeki Orchestration Client işlevi Orchestrator işlevini başlatır ve sonra her 2 saniyede bir `GetStatusAsync` çağıran bir döngüye geçer:

```cs
string instanceId = await client.StartNewAsync(nameof(HelloSequence), input: null);
logger.LogInformation($"Started new instance with ID = {instanceId}.");

DurableOrchestrationStatus status;
while (true)
{
    status = await client.GetStatusAsync(instanceId);
    logger.LogInformation($"Status: {status.RuntimeStatus}, Last update: {status.LastUpdatedTime}.");

    if (status.RuntimeStatus == OrchestrationRuntimeStatus.Completed ||
        status.RuntimeStatus == OrchestrationRuntimeStatus.Failed ||
        status.RuntimeStatus == OrchestrationRuntimeStatus.Terminated)
    {
        break;
    }

    await Task.Delay(TimeSpan.FromSeconds(2));
}
```

## <a name="run-the-sample"></a>Örneği çalıştırma

WebJob olarak çalışacak Dayanıklı İşlevler oluşturdunuz ve artık bunun, tek başına Azure Işlevleri olarak Dayanıklı İşlevler çalıştırmanın nasıl farklı olacağını anlamış oldunuz. Bu noktada, bir örnek içinde çalıştığını görmek faydalı olabilir.

Bu bölüm, [örnek projenin](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/webjobssdk/chaining)nasıl çalıştırılacağını gösteren bir genel bakış sunar. Bir WebJobs SDK projesini yerel olarak çalıştırmayı ve bir Azure WebJob 'a dağıtmayı açıklayan ayrıntılı yönergeler için bkz. [WebJobs SDK ile çalışmaya başlama](../../app-service/webjobs-sdk-get-started.md#deploy-as-a-webjob).

### <a name="run-locally"></a>Yerel olarak çalıştırma

1. Depolama öykünücüsünün çalıştığından emin olun (bkz. [Önkoşullar](#prerequisites)).

1. Projeyi yerel olarak çalıştırdığınızda Application Insights günlükleri görmek istiyorsanız:

    a. Bir Application Insights kaynağı oluşturun ve bunun için **genel** uygulama türünü kullanın.

    b. İzleme anahtarını *app. config* dosyasına kaydedin.

1. Projeyi çalıştırın.

### <a name="run-in-azure"></a>Azure 'da Çalıştır

1. Bir Web uygulaması ve depolama hesabı oluşturun.

1. Web uygulamasında, depolama bağlantı dizesini `AzureWebJobsStorage`adlı bir uygulama ayarına kaydedin.

1. Bir Application Insights kaynağı oluşturun ve bunun için **genel** uygulama türünü kullanın.

1. İzleme anahtarını `APPINSIGHTS_INSTRUMENTATIONKEY`adlı bir uygulama ayarına kaydedin.

1. WebJob olarak dağıtın.

## <a name="webjobs-sdk-3x"></a>WebJobs SDK 3. x

Bu makalede bir WebJobs SDK 2. x projesinin nasıl geliştirilmesi açıklanmaktadır. Bir [WebJobs SDK 3. x](../../app-service/webjobs-sdk-get-started.md) projesi geliştiriyorsanız, bu bölüm farkları anlamanıza yardımcı olur.

Sunulan ana değişiklik, .NET Core 'un .NET Framework yerine kullanılması. Bir WebJobs SDK 3. x projesi oluşturmak için, bu özel durumlarla birlikte yönergeler aynıdır:

1. .NET Core konsol uygulaması oluşturun. Visual Studio **Yeni proje** iletişim kutusunda, **.NET Core** > **konsol uygulaması (.NET Core)** seçeneğini belirleyin. Proje dosyası `TargetFramework` `netcoreapp2.x`belirtir.

1. Aşağıdaki paketlerin sürüm web Işleri SDK 3. x sürümünü seçin:

    * `Microsoft.Azure.WebJobs.Extensions`
    * `Microsoft.Azure.WebJobs.Extensions.Storage`
    * `Microsoft.Azure.WebJobs.Logging.ApplicationInsights`

1. .NET Core yapılandırma çerçevesini kullanarak, bir *appSettings. JSON* dosyasındaki depolama bağlantı dizesini ve Application Insights izleme anahtarını ayarlayın. Bir örneği aşağıda verilmiştir:

    ```json
        {
            "AzureWebJobsStorage": "<replace with storage connection string>",
            "APPINSIGHTS_INSTRUMENTATIONKEY": "<replace with Application Insights instrumentation key>"
        }
    ```

1. `Main` yöntemi kodunu değiştirerek bunu yapın. Bir örneği aşağıda verilmiştir:

   ```cs
   static void Main(string[] args)
   {
        var hostBuilder = new HostBuilder()
            .ConfigureWebJobs(config =>
            {
                config.AddAzureStorageCoreServices();
                config.AddAzureStorage();
                config.AddTimers();
                config.AddDurableTask(options =>
                {
                    options.HubName = "MyTaskHub";
                    options.AzureStorageConnectionStringName = "AzureWebJobsStorage";
                });
            })
            .ConfigureLogging((context, logging) =>
            {
                logging.AddConsole();
                logging.AddApplicationInsights(config =>
                {
                    config.InstrumentationKey = context.Configuration["APPINSIGHTS_INSTRUMENTATIONKEY"];
                });
            })
            .UseConsoleLifetime();

        var host = hostBuilder.Build();

        using (host)
        {
            host.Run();
        }
   }
   ```

## <a name="next-steps"></a>Sonraki adımlar

Web Işleri SDK 'Sı hakkında daha fazla bilgi edinmek için bkz. [WebJobs SDK 'sını kullanma](../../app-service/webjobs-sdk-how-to.md).
