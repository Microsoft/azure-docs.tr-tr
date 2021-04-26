---
title: Application Insights 'de olay sayaçları | Microsoft Docs
description: Application Insights 'de sistem ve özel .NET/.NET Core EventCounters ' i izleyin.
ms.topic: conceptual
ms.date: 09/20/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: d1ae0937c25a68798acd87fe8b2a0a54aa765b35
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100579524"
---
# <a name="eventcounters-introduction"></a>EventCounters giriş

[`EventCounter`](/dotnet/core/diagnostics/event-counters) , sayaçlar veya istatistikler yayımlamak ve kullanmak için .NET/.NET Core mekanizmasıdır. EventCounters tüm işletim sistemi platformlarında desteklenir-Windows, Linux ve macOS. Yalnızca Windows sistemlerinde desteklenen [PerformanceCounters](/dotnet/api/system.diagnostics.performancecounter) için platformlar arası eşdeğer olarak düşünülebilir.

Kullanıcılar ihtiyaçlarını karşılamak için özel bir yayım yapabilir `EventCounters` , ancak .NET Core 3,0 ve üzeri çalışma zamanı varsayılan olarak bu sayaçların bir kümesini yayımlar. Bu belge, `EventCounters` Azure Application Insights içinde toplamak ve görüntülemek (sistem tanımlı veya Kullanıcı tanımlı) için gerekli adımları gösterecektir.

## <a name="using-application-insights-to-collect-eventcounters"></a>EventCounters toplamak için Application Insights kullanma

Application Insights `EventCounters` `EventCounterCollectionModule` , yeni yayınlanan NuGet paketi [Microsoft. ApplicationInsights. eventcountercollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventCounterCollector)'ın bir parçası olan ile toplamayı destekler. `EventCounterCollectionModule` , [Aspnetcore](asp-net-core.md) veya [workerservice](worker-service.md)kullanılırken otomatik olarak etkinleştirilir. `EventCounterCollectionModule` yapılandırılabilir olmayan bir koleksiyon frekansı 60 saniye olan sayaçları toplar. EventCounters toplamak için gerekli özel izinler yoktur.

## <a name="default-counters-collected"></a>Toplanan varsayılan sayaçlar

[Aspnetcore SDK](asp-net-core.md) veya [workerservice SDK 'sının](worker-service.md)2.15.0 sürümünden başlayarak, hiçbir sayaç varsayılan olarak toplanmaz. Modülün kendisi etkinleştirilir, böylece kullanıcılar bunları toplamak için istenen sayaçları ekleyebilir.

.NET çalışma zamanı tarafından yayımlanan iyi bilinen sayaçların bir listesini almak için, bkz. [kullanılabilir sayaçlar](/dotnet/core/diagnostics/event-counters#available-counters) belgesi.

## <a name="customizing-counters-to-be-collected"></a>Toplanacak sayaçları özelleştirme

Aşağıdaki örnek, sayaçların nasıl ekleneceğini/kaldırılacağını gösterir. Bu özelleştirme, `ConfigureServices` veya kullanarak Application Insights telemetri koleksiyonu etkinleştirildikten sonra uygulamanızın yönteminde yapılır `AddApplicationInsightsTelemetry()` `AddApplicationInsightsWorkerService()` . Aşağıda bir ASP.NET Core uygulamasından örnek bir kod verilmiştir. Diğer uygulama türleri için [Bu](worker-service.md#configuring-or-removing-default-telemetrymodules) belgeye başvurun.

```csharp
    using Microsoft.ApplicationInsights.Extensibility.EventCounterCollector;
    using Microsoft.Extensions.DependencyInjection;

    public void ConfigureServices(IServiceCollection services)
    {
        //... other code...

        // The following code shows how to configure the module to collect
        // additional counters.
        services.ConfigureTelemetryModule<EventCounterCollectionModule>(
            (module, o) =>
            {
                // This removes all default counters, if any.
                module.Counters.Clear();

                // This adds a user defined counter "MyCounter" from EventSource named "MyEventSource"
                module.Counters.Add(new EventCounterCollectionRequest("MyEventSource", "MyCounter"));

                // This adds the system counter "gen-0-size" from "System.Runtime"
                module.Counters.Add(new EventCounterCollectionRequest("System.Runtime", "gen-0-size"));
            }
        );
    }
```

## <a name="disabling-eventcounter-collection-module"></a>EventCounter koleksiyon modülü devre dışı bırakılıyor

`EventCounterCollectionModule` kullanılarak devre dışı bırakılabilir `ApplicationInsightsServiceOptions` . Aşağıda ASP.NET Core SDK kullanılırken bir örnek gösterilmektedir.

```csharp
    using Microsoft.ApplicationInsights.AspNetCore.Extensions;
    using Microsoft.Extensions.DependencyInjection;

    public void ConfigureServices(IServiceCollection services)
    {
        //... other code...

        var applicationInsightsServiceOptions = new ApplicationInsightsServiceOptions();
        applicationInsightsServiceOptions.EnableEventCounterCollectionModule = false;
        services.AddApplicationInsightsTelemetry(applicationInsightsServiceOptions);
    }
```

Aynı yaklaşım, WorkerService SDK 'Sı için de kullanılabilir, ancak aşağıdaki örnekte gösterildiği gibi ad alanı değiştirilmelidir.

```csharp
    using Microsoft.ApplicationInsights.WorkerService;
    using Microsoft.Extensions.DependencyInjection;

    var applicationInsightsServiceOptions = new ApplicationInsightsServiceOptions();
    applicationInsightsServiceOptions.EnableEventCounterCollectionModule = false;
    services.AddApplicationInsightsTelemetryWorkerService(applicationInsightsServiceOptions);
```

## <a name="event-counters-in-metric-explorer"></a>Ölçüm Gezgini 'nde olay sayaçları

[Ölçüm Gezgini](../essentials/metrics-charts.md)'Nde EventCounter ölçümlerini görüntülemek için Application Insights kaynak ' ı seçin ve ölçüm ad alanı olarak günlük tabanlı ölçümler ' i seçin. Daha sonra EventCounter ölçümleri özel kategori altında gösterilir.

> [!div class="mx-imgBorder"]
> ![Application Insights ölçüm Gezgininde bildirilen olay sayaçları](./media/event-counters/metrics-explorer-counter-list.png)

## <a name="event-counters-in-analytics"></a>Analiz içindeki olay sayaçları

Ayrıca, **Customölçümler** tablosunda [analiz](../logs/log-query-overview.md)' de olay sayacı raporlarını arayabilir ve görüntüleyebilirsiniz.

Örneğin, hangi sayaçların toplandığını ve sorgu için kullanılabilir olduğunu görmek için aşağıdaki sorguyu çalıştırın:

```Kusto
customMetrics | summarize avg(value) by name
```

> [!div class="mx-imgBorder"]
> ![Application Insights Analytics 'te bildirilen olay sayaçları](./media/event-counters/analytics-event-counters.png)

Belirli bir sayacın (örneğin:) bir grafiğini `ThreadPool Completed Work Item Count` son döneme almak için aşağıdaki sorguyu çalıştırın.

```Kusto
customMetrics 
| where name contains "System.Runtime|ThreadPool Completed Work Item Count"
| where timestamp >= ago(1h)
| summarize  avg(value) by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```
> [!div class="mx-imgBorder"]
> ![Application Insights tek bir sayaca sohbet etme](./media/event-counters/analytics-completeditems-counters.png)

Diğer telemetri gibi, **Customölçümleri** de `cloud_RoleInstance` uygulamanızın çalıştırıldığı ana bilgisayar sunucusu örneğinin kimliğini belirten bir sütun içerir. Yukarıdaki sorgu, örnek başına sayaç değerini gösterir ve farklı sunucu örneklerinin performansını karşılaştırmak için kullanılabilir.

## <a name="alerts"></a>Uyarılar
Diğer ölçümler gibi, bir olay sayacı belirttiğiniz sınırın dışında kaldığında sizi uyarmak üzere [bir uyarı ayarlayabilirsiniz](../alerts/alerts-log.md) . Uyarılar bölmesini açın ve uyarı Ekle ' ye tıklayın.

## <a name="frequently-asked-questions"></a>Sık sorulan sorular

### <a name="can-i-see-eventcounters-in-live-metrics"></a>Canlı ölçümlerde EventCounters görebilir miyim?

Canlı ölçümler, bugün itibariyle EventCounters göstermez. Telemetriyi görmek için ölçüm Gezgini 'ni veya analizlerini kullanın.

### <a name="i-have-enabled-application-insights-from-azure-web-app-portal-but-i-cant-see-eventcounters"></a>Azure Web App Portal 'dan Application Insights etkinleştirdim. Ancak EventCounters göremiyorum.?

 ASP.NET Core için [Application Insights uzantısı](./azure-web-apps.md) henüz bu özelliği desteklemez. Bu özellik destekleniyorsa, bu belge güncelleştirilecektir.

## <a name="next-steps"></a><a name="next"></a>Sonraki adımlar

* [Bağımlılık izleme](./asp-net-dependencies.md)

