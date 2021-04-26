---
title: Azure Işlevleri 'ni kullanarak özel kullanılabilirlik testleri oluşturma ve çalıştırma
description: Bu belge, TimerTrigger işlevinde verilen yapılandırmaya göre düzenli olarak çalışacak TrackAvailability () ile bir Azure Işlevi oluşturmayı kapsar. Bu testin sonuçları, kullanılabilirlik sonuçları verilerini sorgulayabilir ve uyarılabileceğiniz Application Insights kaynağına gönderilir. Özelleştirilmiş testler, Portal Kullanıcı arabirimini kullanarak mümkün olandan daha karmaşık kullanılabilirlik testleri yazmanızı, Azure VNET 'iniz içindeki bir uygulamayı izlemenizi, uç nokta adresini değiştirmenizi veya bölgenizde yoksa bir kullanılabilirlik testi oluşturmanızı sağlar.
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 98d9eaadb31ffdeabe85752f7c76bdd4f7c0d4f3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100589933"
---
# <a name="create-and-run-custom-availability-tests-using-azure-functions"></a>Azure Işlevleri 'ni kullanarak özel kullanılabilirlik testleri oluşturma ve çalıştırma

Bu makalede, kendi iş mantığınızla TimerTrigger işlevinde verilen yapılandırmaya göre düzenli aralıklarla çalışacak TrackAvailability () ile bir Azure Işlevi oluşturma işlemi ele alınacaktır. Bu testin sonuçları, kullanılabilirlik sonuçları verilerini sorgulayabilir ve uyarılabileceğiniz Application Insights kaynağına gönderilir. Bu, portalda [kullanılabilirlik izleme](./monitor-web-app-availability.md) aracılığıyla yapabileceklerinizle benzer özelleştirilmiş testler oluşturmanıza olanak sağlar. Özelleştirilmiş testler, Portal Kullanıcı arabirimini kullanarak mümkün olandan daha karmaşık kullanılabilirlik testleri yazmanızı, Azure VNET 'iniz içindeki bir uygulamayı izlemenizi, uç nokta adresini değiştirmenizi veya bu özellik bölgenizde mevcut olmasa bile bir kullanılabilirlik testi oluşturmanızı sağlar.

> [!NOTE]
> Bu örnek, yalnızca TrackAvailability () API çağrısının bir Azure Işlevi içinde nasıl çalıştığını gösteren bir mekanizması göstermek için tasarlanmıştır. Bunu tam işlevsel bir kullanılabilirlik testine dönüştürmek için gereken temel HTTP test kodu/iş mantığını yazma değil. Bu örnekte, varsayılan olarak, her zaman bir hata üretecek bir kullanılabilirlik testi oluşturacaksınız.

## <a name="create-timer-triggered-function"></a>Süreölçer tetikleme işlevi oluştur

- Bir Application Insights kaynağınız varsa:
    - Varsayılan olarak Azure Işlevleri bir Application Insights kaynağı oluşturur, ancak önceden oluşturulmuş kaynaklarınızın birini kullanmak istiyorsanız oluşturma sırasında bunu belirtmeniz gerekir.
    - Aşağıdaki seçimlerle [bir Azure işlevleri kaynağı oluşturma ve Zamanlayıcı ile tetiklenen işlev](../../azure-functions/functions-create-scheduled-function.md) (temizlemeden önce durdurma) hakkındaki yönergeleri izleyin.
        -  Üst taraftaki **izleme** sekmesini seçin.

            ![ Kendi App Insights kaynağınız ile bir Azure Işlevleri uygulaması oluşturma](media/availability-azure-functions/create-function-app.png)

        - Application Insights açılan kutusunu seçin ve kaynağınızın adını yazın veya seçin.

            ![Mevcut Application Insights kaynağı seçiliyor](media/availability-azure-functions/app-insights-resource.png)

        - **Gözden geçir + oluştur**’u seçin
- Zamanlayıcı tarafından tetiklenen işleviniz için henüz oluşturulmuş bir Application Insights kaynağınız yoksa:
    - Varsayılan olarak, Azure Işlevleri uygulamanızı oluştururken sizin için bir Application Insights kaynağı oluşturulur.
    - [Azure işlevleri kaynağı oluşturma ve Zamanlayıcı tarafından tetiklenen işlev](../../azure-functions/functions-create-scheduled-function.md) (temizlemeden önce durdurma) hakkındaki yönergeleri izleyin.

## <a name="sample-code"></a>Örnek kod

Aşağıdaki kodu Run. CSX dosyasına kopyalayın (Bu, önceden var olan kodun yerini alır). Bunu yapmak için Azure Işlevleri uygulamanıza gidin ve soldaki Zamanlayıcı tetikleyicisi işlevinizi seçin.

>[!div class="mx-imgBorder"]
>![Azure işlevinin Run. CSX Azure portal](media/availability-azure-functions/runcsx.png)

> [!NOTE]
> Uç nokta adresi için şunu kullanın: `EndpointAddress= https://dc.services.visualstudio.com/v2/track` . Kaynağınız Azure Kamu veya Azure Çin gibi bir bölgede yer almadığı takdirde, bu durumda [varsayılan uç noktaları geçersiz kılma](./custom-endpoints.md#regions-that-require-endpoint-modification) ve bölgeniz Için uygun telemetri kanalı uç noktasını seçme konusunda bu makaleye başvurun.

```C#
#load "runAvailabilityTest.csx&quot;
 
using System;
using System.Diagnostics;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
 
// The Application Insights Instrumentation Key can be changed by going to the overview page of your Function App, selecting configuration, and changing the value of the APPINSIGHTS_INSTRUMENTATIONKEY Application setting.
// DO NOT replace the code below with your instrumentation key, the key's value is pulled from the environment variable/application setting key/value pair.
private static readonly string instrumentationKey = Environment.GetEnvironmentVariable(&quot;APPINSIGHTS_INSTRUMENTATIONKEY");
 
//[CONFIGURATION_REQUIRED]
// If your resource is in a region like Azure Government or Azure China, change the endpoint address accordingly.
// Visit https://docs.microsoft.com/azure/azure-monitor/app/custom-endpoints#regions-that-require-endpoint-modification for more details.
private const string EndpointAddress = "https://dc.services.visualstudio.com/v2/track";
 
private static readonly TelemetryConfiguration telemetryConfiguration = new TelemetryConfiguration(instrumentationKey, new InMemoryChannel { EndpointAddress = EndpointAddress });
private static readonly TelemetryClient telemetryClient = new TelemetryClient(telemetryConfiguration);
 
public async static Task Run(TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"Entering Run at: {DateTime.Now}");
 
    if (myTimer.IsPastDue)
    {
        log.LogWarning($"[Warning]: Timer is running late! Last ran at: {myTimer.ScheduleStatus.Last}");
    }
 
    // [CONFIGURATION_REQUIRED] provide {testName} accordingly for your test function
    string testName = "AvailabilityTestFunction";
 
    // REGION_NAME is a default environment variable that comes with App Service
    string location = Environment.GetEnvironmentVariable("REGION_NAME");
 
    log.LogInformation($"Executing availability test run for {testName} at: {DateTime.Now}");
    string operationId = Guid.NewGuid().ToString("N");
 
    var availability = new AvailabilityTelemetry
    {
        Id = operationId,
        Name = testName,
        RunLocation = location,
        Success = false
    };
 
    var stopwatch = new Stopwatch();
    stopwatch.Start();
 
    try
    {
        await RunAvailbiltyTestAsync(log);
        availability.Success = true;
    }
    catch (Exception ex)
    {
        availability.Message = ex.Message;
 
        var exceptionTelemetry = new ExceptionTelemetry(ex);
        exceptionTelemetry.Context.Operation.Id = operationId;
        exceptionTelemetry.Properties.Add("TestName", testName);
        exceptionTelemetry.Properties.Add("TestLocation", location);
        telemetryClient.TrackException(exceptionTelemetry);
    }
    finally
    {
        stopwatch.Stop();
        availability.Duration = stopwatch.Elapsed;
        availability.Timestamp = DateTimeOffset.UtcNow;
 
        telemetryClient.TrackAvailability(availability);
        // call flush to ensure telemetry is sent
        telemetryClient.Flush();
    }
}

```

Dosya görüntüle ' nin altında, **Ekle**' yi seçin. Aşağıdaki yapılandırmaya sahip New **. proj** dosyasını çağırın.

```C#
<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <TargetFramework>netstandard2.0</TargetFramework>
    </PropertyGroup>
    <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights" Version="2.15.0" /> <!-- Ensure you’re using the latest version -->
    </ItemGroup>
</Project>

```

>[!div class="mx-imgBorder"]
>![Sağ Seç ' in ardından ekleyin. Dosyayı function. proj olarak adlandırın](media/availability-azure-functions/addfile.png)

Dosya görüntüle ' nin altında, **Ekle**' yi seçin. Aşağıdaki yapılandırmayla **Runkullanılabilirliği Bilitytest. CSX** adlı yeni dosyayı çağırın.

```C#
public async static Task RunAvailbiltyTestAsync(ILogger log)
{
    // Add your business logic here.
    throw new NotImplementedException();
}

```

## <a name="check-availability"></a>Kullanılabilirliği Denetle

Her şeyin çalıştığından emin olmak için Application Insights kaynağınızın kullanılabilirlik sekmesinde grafiğe bakabilirsiniz.

> [!NOTE]
> Runkullanılabilirliği Bilitytest. CSX içinde kendi iş mantığınızı uyguladıysanız, aşağıdaki ekran görüntülerinde olduğu gibi başarılı sonuçları görürsünüz. Bu durumda, başarısız sonuçları görürsünüz. İle oluşturulan testler `TrackAvailability()` , test adının yanında **özel** ile görünür.

>[!div class="mx-imgBorder"]
>![Başarılı sonuçlarla kullanılabilirlik sekmesi](media/availability-azure-functions/availability-custom.png)

Uçtan uca işlem ayrıntılarını görmek için detaya git altında **başarılı** veya **başarısız** ' ı seçin ve ardından bir örnek seçin. Ayrıca, grafikteki bir veri noktasını seçerek uçtan uca işlem ayrıntılarına de ulaşabilirsiniz.

>[!div class="mx-imgBorder"]
>![Örnek bir kullanılabilirlik testi seçin](media/availability-azure-functions/sample.png)

>[!div class="mx-imgBorder"]
>![Uçtan uca işlem ayrıntıları](media/availability-azure-functions/end-to-end.png)

Her şeyi olduğu gibi çalıştırdıysanız (iş mantığı eklemeden), testin başarısız olduğunu görürsünüz.

## <a name="query-in-logs-analytics"></a>Günlüklerde sorgulama (Analiz)

Kullanılabilirlik sonuçlarını, bağımlılıklarını ve daha fazlasını görüntülemek için günlükleri (Analiz) kullanabilirsiniz. Günlükler hakkında daha fazla bilgi edinmek için [günlük sorgusuna genel bakış](../logs/log-query-overview.md)sayfasını ziyaret edin.

>[!div class="mx-imgBorder"]
>![Kullanılabilirlik sonuçları](media/availability-azure-functions/availabilityresults.png)

>[!div class="mx-imgBorder"]
>![Ekran görüntüsünde, 50 ile sınırlı bağımlılıklar içeren yeni sorgu sekmesi görüntülenir.](media/availability-azure-functions/dependencies.png)

## <a name="next-steps"></a>Sonraki adımlar

- [Uygulama Eşlemesi](./app-map.md)
- [İşlem tanılamaları](./transaction-diagnostics.md)

