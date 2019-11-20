---
title: Azure Işlevleri 'ni kullanarak özel kullanılabilirlik testleri oluşturma ve çalıştırma
description: Bu belge, TimerTrigger işlevinde verilen yapılandırmaya göre düzenli olarak çalışacak TrackAvailability () ile bir Azure Işlevi oluşturmayı kapsar. Bu testin sonuçları, kullanılabilirlik sonuçları verilerini sorgulayabilir ve uyarılabileceğiniz Application Insights kaynağına gönderilir. Özelleştirilmiş testler, Portal Kullanıcı arabirimini kullanarak mümkün olandan daha karmaşık kullanılabilirlik testleri yazmanızı, Azure VNET 'iniz içindeki bir uygulamayı izlemenizi, uç nokta adresini değiştirmenizi veya bölgenizde yoksa bir kullanılabilirlik testi oluşturmanızı sağlar.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: morgangrobin
ms.author: mogrobin
ms.date: 10/11/2019
ms.openlocfilehash: 900228e1f9bdf9d367fa37b9ec90a6148faec656
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72880245"
---
# <a name="create-and-run-custom-availability-tests-using-azure-functions"></a>Azure Işlevleri 'ni kullanarak özel kullanılabilirlik testleri oluşturma ve çalıştırma

Bu makalede, TimerTrigger işlevinde verilen yapılandırmaya göre düzenli olarak çalışacak TrackAvailability () ile bir Azure Işlevi oluşturma işlemi ele alınmaktadır. Bu testin sonuçları, kullanılabilirlik sonuçları verilerini sorgulayabilir ve uyarılabileceğiniz Application Insights kaynağına gönderilir. Bu, portalda [kullanılabilirlik izleme](../../azure-monitor/app/monitor-web-app-availability.md) aracılığıyla yapabileceklerinizle benzer özelleştirilmiş testler oluşturmanıza olanak sağlar. Özelleştirilmiş testler, Portal Kullanıcı arabirimini kullanarak mümkün olandan daha karmaşık kullanılabilirlik testleri yazmanızı, Azure VNET 'iniz içindeki bir uygulamayı izlemenizi, uç nokta adresini değiştirmenizi veya bu özellik bölgenizde mevcut olmasa bile bir kullanılabilirlik testi oluşturmanızı sağlar.


## <a name="create-timer-triggered-function"></a>Süreölçer tetikleme işlevi oluştur

- Bir Application Insights kaynağınız varsa:
    - Varsayılan olarak Azure Işlevleri bir Application Insights kaynağı oluşturur, ancak önceden oluşturulmuş kaynaklarınızın birini kullanmak istiyorsanız oluşturma sırasında bunu belirtmeniz gerekir.
    - Aşağıdaki seçimlerle [bir Azure işlevleri kaynağı oluşturma ve Zamanlayıcı ile tetiklenen işlev](https://docs.microsoft.com/azure/azure-functions/functions-create-scheduled-function) (temizlemeden önce durdurma) hakkındaki yönergeleri izleyin.
        -  **Oluştur**' u seçmeden önce Application Insights bölümüne tıklayın.

            ![ Kendi App Insights kaynağınız ile bir Azure Işlevleri uygulaması oluşturma](media/availability-azure-functions/create-function-app.png)

        - **Mevcut kaynağı seç** ' e tıklayın ve kaynağınızın adını yazın. **Uygula** ' yı seçin

            ![Mevcut Application Insights kaynağı seçiliyor](media/availability-azure-functions/app-insights-resource.png)

        - **Oluştur**’u seçin
- Zamanlayıcı tarafından tetiklenen işleviniz için henüz oluşturulmuş bir Application Insights kaynağınız yoksa:
    - Varsayılan olarak, Azure Işlevleri uygulamanızı oluştururken sizin için bir Application Insights kaynağı oluşturulur.
    - [Azure işlevleri kaynağı oluşturma ve Zamanlayıcı tarafından tetiklenen işlev](https://docs.microsoft.com/azure/azure-functions/functions-create-scheduled-function) (temizlemeden önce durdurma) hakkındaki yönergeleri izleyin.

## <a name="sample-code"></a>Örnek kod

Aşağıdaki kodu Run. CSX dosyasına kopyalayın (Bu, önceden var olan kodun yerini alır). Bunu yapmak için Azure Işlevleri uygulamanıza gidin ve soldaki Zamanlayıcı tetikleyicisi işlevinizi seçin.

![Azure işlevinin Run. CSX Azure portal](media/availability-azure-functions/runcsx.png)

> [!NOTE]
> Kullandığınız uç nokta adresi için: `EndpointAddress= https://dc.services.visualstudio.com/v2/track`. Kaynağınız Azure Kamu veya Azure Çin gibi bir bölgede yer almadığı takdirde, bu durumda [varsayılan uç noktaları geçersiz kılma](https://docs.microsoft.com/azure/azure-monitor/app/custom-endpoints#regions-that-require-endpoint-modification) ve bölgeniz Için uygun telemetri kanalı uç noktasını seçme konusunda bu makaleye başvurun.

```C#
using System;
using System.Diagnostics;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

// [CONFIGURATION_REQUIRED] configure test timeout accordingly for which your request should run
private static readonly HttpClient HttpClient = new HttpClient { Timeout = TimeSpan.FromSeconds(30) };

// The Application Insights Instrumentation Key can be changed by going to the overview page of your Function App, selecting configuration, and changing the value of the APPINSIGHTS_INSTRUMENTATIONKEY Application setting.
//DO NOT replace the code below with your instrumentation key, the key's value is pulled from the environment variable/application setting key/value pair.
private static readonly string InstrumentationKey = Environment.GetEnvironmentVariable("APPINSIGHTS_INSTRUMENTATIONKEY");

// [CONFIGURATION_REQUIRED] Configure EndpointAddress
private static readonly TelemetryConfiguration TelemetryConfiguration = new TelemetryConfiguration(InstrumentationKey, new ServerTelemetryChannel() { EndpointAddress = "<EndpointAddress>" });
private static readonly TelemetryClient TelemetryClient = new TelemetryClient(TelemetryConfiguration);

[FunctionName("Function")]
public static async void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, ILogger log)
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

    // [CONFIGURATION_REQUIRED] configure {uri} and {contentMatch} accordingly for your web app. {uri} is the website that you are testing the availability of, make sure to include http:// ot https:// in your url. If {contentMatch} is present on the page, the test will succeed, otherwise it will fail.  
    await AvailabilityTestRun(
        name: testName,
        location: location,
        uri: "<http://example.com>",
        contentMatch: "<Enter a short string of text that is present  in the body of the page your are testing>",
        log: log
    );
}

private static async Task AvailabilityTestRun(string name, string location, string uri, string contentMatch, ILogger log)
{
    log.LogInformation($"Executing availability test run for {name} at: {DateTime.Now}");

    string operationId = Guid.NewGuid().ToString("N");

    var availability = new AvailabilityTelemetry
    {
        Id = operationId,
        Name = name,
        RunLocation = location,
        Success = false
    };

    var stopwatch = new Stopwatch();
    stopwatch.Start();
    bool isMonitoringFailure = false;

    try
    {
        using (var httpResponse = await HttpClient.GetAsync(uri))
        {
            // add test results to availability telemetry property
            availability.Properties.Add("HttpResponseStatusCode", Convert.ToInt32(httpResponse.StatusCode).ToString());

            // check if response content contains specific text
            string content = httpResponse.Content != null ? await httpResponse.Content.ReadAsStringAsync() : "";
            if (httpResponse.IsSuccessStatusCode && content.Contains(contentMatch))
            {
                availability.Success = true;
                availability.Message = $"Test succeeded with response: {httpResponse.StatusCode}";
                log.LogTrace($"[Verbose]: {availability.Message}");
            }
            else if (!httpResponse.IsSuccessStatusCode)
            {
                availability.Message = $"Test failed with response: {httpResponse.StatusCode}";
                log.LogWarning($"[Warning]: {availability.Message}");
            }
            else
            {
                availability.Message = $"Test content does not contain: {contentMatch}";
                log.LogWarning($"[Warning]: {availability.Message}");
            }
        }
    }
    catch (TaskCanceledException e)
    {
        availability.Message = $"Test timed out: {e.Message}";
        log.LogWarning($"[Warning]: {availability.Message}");
    }
    catch (Exception ex)
    {
        // track exception when unable to determine the state of web app
        isMonitoringFailure = true;
        var exceptionTelemetry = new ExceptionTelemetry(ex);
        exceptionTelemetry.Context.Operation.Id = operationId;
        exceptionTelemetry.Properties.Add("TestName", name);
        exceptionTelemetry.Properties.Add("TestLocation", location);
        exceptionTelemetry.Properties.Add("TestUri", uri);
        TelemetryClient.TrackException(exceptionTelemetry);
        log.LogError($"[Error]: {ex.Message}");

        // optional - throw to fail the function
        //throw;
    }
    finally
    {
        stopwatch.Stop();
        availability.Duration = stopwatch.Elapsed;
        availability.Timestamp = DateTimeOffset.UtcNow;

        // do not make an assumption as to the state of the web app when monitoring failures occur
        if (!isMonitoringFailure)
        {
            TelemetryClient.TrackAvailability(availability);
            log.LogInformation($"Availability telemetry for {name} is sent.");
        }

        // call flush to ensure telemetries are sent
        TelemetryClient.Flush();
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
        <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.6.1" />
    </ItemGroup>
</Project>

```

![Sağ Seç ' in ardından ekleyin. Dosyayı function. proj olarak adlandırın](media/availability-azure-functions/addfile.png)

## <a name="check-availability"></a>Kullanılabilirliği Denetle

Her şeyin çalıştığından emin olmak için Application Insights kaynağınızın kullanılabilirlik sekmesinde grafiğe bakabilirsiniz.

![Başarılı sonuçlarla kullanılabilirlik sekmesi](media/availability-azure-functions/availtab.png)

Azure Işlevleri 'ni kullanarak testinizi ayarlarken, kullanılabilirlik sekmesinde **Test Ekle** ' yi kullanmaktan farklı olarak, testinizin adının görünmediğine ve bununla etkileşime giremeyeceksiniz. Sonuçlar görselleştirilir, ancak Portal aracılığıyla bir kullanılabilirlik testi oluştururken alacağınız ayrıntılı görünüm yerine bir Özet görünümü alırsınız.

Uçtan uca işlem ayrıntılarını görmek için detaya git altında **başarılı** veya **başarısız** ' ı seçin ve ardından bir örnek seçin. Ayrıca, grafikteki bir veri noktasını seçerek uçtan uca işlem ayrıntılarına de ulaşabilirsiniz.

![Örnek bir kullanılabilirlik testi seçin](media/availability-azure-functions/sample.png)

![Uçtan uca işlem ayrıntıları](media/availability-azure-functions/end-to-end.png)

## <a name="query-in-logs-analytics"></a>Günlüklerde sorgulama (Analiz)

Kullanılabilirlik sonuçlarını, bağımlılıklarını ve daha fazlasını görüntülemek için günlükleri (Analiz) kullanabilirsiniz. Günlükler hakkında daha fazla bilgi edinmek için [günlük sorgusuna genel bakış](../../azure-monitor/log-query/log-query-overview.md)sayfasını ziyaret edin.

![Kullanılabilirlik sonuçları](media/availability-azure-functions/availabilityresults.png)

![Bağımlılıklar](media/availability-azure-functions/dependencies.png)

## <a name="next-steps"></a>Sonraki adımlar

- [Uygulama Eşlemesi](../../azure-monitor/app/app-map.md)
- [İşlem tanılama](../../azure-monitor/app/transaction-diagnostics.md)
