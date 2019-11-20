---
title: Azure Application Insights bağımlılık Izleme | Microsoft Docs
description: Şirket içi veya Microsoft Azure Web uygulamanızdan gelen bağımlılık çağrılarını Application Insights ile izleyin.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 06/25/2019
ms.openlocfilehash: 7b23da81143a4ae66d9f25cd953c4a3952f27455
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72678377"
---
# <a name="dependency-tracking-in-azure-application-insights"></a>Azure Application Insights 'de bağımlılık Izleme 

*Bağımlılık* , uygulamanız tarafından çağrılan bir dış bileşendir. Genellikle HTTP veya bir veritabanı ya da bir dosya sistemi kullanılarak adlandırılan bir hizmettir. [Application Insights](../../azure-monitor/app/app-insights-overview.md) , bağımlılık çağrılarının süresini ölçer, başarısız olup olmadığı ve bağımlılık adı gibi ek bilgilerle birlikte ölçer. Belirli bağımlılık çağrılarını araştırabilir ve bunları isteklerle ve özel durumlarla ilişkilendirmenize olanak sağlayabilirsiniz.

## <a name="automatically-tracked-dependencies"></a>Otomatik olarak izlenen bağımlılıklar

.NET ve .NET Core için Application Insights SDK 'Ları, otomatik olarak bağımlılıkları toplayan bir telemetri modülü olan `DependencyTrackingTelemetryModule` ile birlikte gelir. Bu bağımlılık koleksiyonu, bağlantılı resmi belgelere göre yapılandırıldığında [ASP.net](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) ve [ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) uygulamaları için otomatik olarak etkinleştirilir.  `DependencyTrackingTelemetryModule`, [Bu](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) NuGet paketi olarak gönderilir ve `Microsoft.ApplicationInsights.Web` veya `Microsoft.ApplicationInsights.AspNetCore` NuGet paketlerinden birini kullanırken otomatik olarak getirilir.

 `DependencyTrackingTelemetryModule` Şu anda aşağıdaki bağımlılıkları otomatik olarak izler:

|Bağımlılıklar |Ayrıntılar|
|---------------|-------|
|Http/https | Yerel veya uzak http/https çağrıları |
|WCF çağrıları| Yalnızca HTTP tabanlı bağlamalar kullanılıyorsa otomatik olarak izlenir.|
|SQL | @No__t_0 yapılan çağrılar. SQL sorgusunu yakalamak için [bunu](#advanced-sql-tracking-to-get-full-sql-query) inceleyin.  |
|[Azure depolama (blob, tablo, kuyruk)](https://www.nuget.org/packages/WindowsAzure.Storage/) | Azure Storage Istemcisi ile yapılan çağrılar. |
|[EventHub Istemci SDK 'Sı](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | Sürüm 1.1.0 ve üstü. |
|[ServiceBus Istemci SDK 'Sı](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus)| Sürüm 3.0.0 ve üstü. |
|Azure Cosmos DB | Yalnızca HTTP/HTTPS kullanılıyorsa otomatik olarak izlenir. TCP modu Application Insights tarafından yakalanmayacaktır. |

Bir bağımlılığı eksik ise veya farklı bir SDK kullanıyorsanız, [otomatik olarak toplanan bağımlılıklar](https://docs.microsoft.com/azure/application-insights/auto-collect-dependencies)listesinde olduğundan emin olun. Bağımlılık otomatik olarak toplanmazsa, bir [izleme bağımlılığı çağrısıyla](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackdependency)el ile izleyebilirsiniz.

## <a name="setup-automatic-dependency-tracking-in-console-apps"></a>Konsol uygulamalarında otomatik bağımlılık izlemeyi ayarla

.NET/.NET Core konsol uygulamalarından gelen bağımlılıkları otomatik olarak izlemek için, NuGet paketini `Microsoft.ApplicationInsights.DependencyCollector` yükleyip `DependencyTrackingTelemetryModule` şu şekilde başlatın:

```csharp
    DependencyTrackingTelemetryModule depModule = new DependencyTrackingTelemetryModule();
    depModule.Initialize(TelemetryConfiguration.Active);
```

### <a name="how-automatic-dependency-monitoring-works"></a>Otomatik bağımlılık izleme nasıl çalışıyor?

Bağımlılıklar, aşağıdaki tekniklerden biri kullanılarak otomatik olarak toplanır:

* Select metotları etrafında Byte Code izleme kullanma. (StatusMonitor ya da Azure Web App uzantısından ınstrumentationengine)
* EventSource geri çağırmaları
* DiagnosticSource geri çağırmaları (en son .NET/.NET Core SDK 'lerinde)

## <a name="manually-tracking-dependencies"></a>Bağımlılıkları el ile izleme

Aşağıda, otomatik olarak toplanmayan ve bu nedenle el ile izleme gerektiren bazı bağımlılıklar örnekleri verilmiştir.

* Azure Cosmos DB yalnızca [http/https](../../cosmos-db/performance-tips.md#networking) kullanılıyorsa otomatik olarak izlenir. TCP modu Application Insights tarafından yakalanmayacaktır.
* Redis

SDK tarafından otomatik olarak toplanmayan bağımlılıklar için, standart otomatik toplama modülleri tarafından kullanılan [Trackdependency API](api-custom-events-metrics.md#trackdependency) 'sini kullanarak bunları el ile izleyebilirsiniz.

Örneğin, kodunuzun kendinize yazmadığınız bir derlemeyle derlenmesi durumunda, yanıt süreleriniz için hangi katkıyı yaptığını öğrenmek için tüm çağrıları zaman içinde oluşturabilirsiniz. Bu verilerin Application Insights içindeki bağımlılık grafiklerde görüntülenmesini sağlamak için `TrackDependency` kullanarak gönderin.

```csharp

    var startTime = DateTime.UtcNow;
    var timer = System.Diagnostics.Stopwatch.StartNew();
    try
    {
        // making dependency call
        success = dependency.Call();
    }
    finally
    {
        timer.Stop();
        telemetryClient.TrackDependency("myDependencyType", "myDependencyCall", "myDependencyData",  startTime, timer.Elapsed, success);
    }
```

Alternatif olarak `TelemetryClient`, [burada](custom-operations-tracking.md#outgoing-dependencies-tracking) gösterildiği gibi, bağımlılıkları el ile izlemek için kullanılabilecek `StartOperation` ve `StopOperation` uzantı yöntemleri sağlar

Standart bağımlılık izleme modülünü devre dışı bırakmak istiyorsanız, ASP.NET uygulamaları için [ApplicationInsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) içindeki DependencyTrackingTelemetryModule başvurusunu kaldırın. ASP.NET Core uygulamalar için [buradaki](asp-net-core.md#configuring-or-removing-default-telemetrymodules)yönergeleri izleyin.

## <a name="tracking-ajax-calls-from-web-pages"></a>Web sayfalarından AJAX çağrılarını izleme

Web sayfaları için Application Insights JavaScript SDK 'Sı otomatik olarak AJAX çağrılarını bağımlılık olarak toplar.

## <a name="advanced-sql-tracking-to-get-full-sql-query"></a>Tam SQL sorgusu almak için gelişmiş SQL izleme

SQL çağrıları için sunucu ve veritabanının adı her zaman toplanır ve toplanan `DependencyTelemetry` adı olarak depolanır. Tam SQL sorgu metnini içerebilen ' Data ' adlı ek bir alan var.

ASP.NET Core uygulamalar için, tam SQL sorgusunu almak için ek bir adım gerekmez.

ASP.NET uygulamalar için, tam SQL sorgusu, izleme altyapısı gerektiren Byte Code Instrumentation yardımıyla toplanır. Aşağıda açıklandığı gibi platforma özgü ek adımlar gereklidir.

| Platform | Tam SQL sorgusu almak için gereken adımlar |
| --- | --- |
| Azure Web Uygulaması |Web uygulaması denetim masasında [Application Insights dikey penceresini açın](../../azure-monitor/app/azure-web-apps.md) ve .net altında SQL komutlarını etkinleştirin |
| IIS sunucusu (Azure VM, şirket içi vb.) | Durum İzleyicisi PowerShell modülünü kullanarak [Izleme altyapısını YÜKLEYIP](../../azure-monitor/app/status-monitor-v2-api-enable-instrumentation-engine.md) IIS 'yi yeniden başlatın. |
| Azure Cloud Service | [StatusMonitor 'ı yüklemek için başlangıç görevi](../../azure-monitor/app/cloudservices.md#set-up-status-monitor-to-collect-full-sql-queries-optional) ekleme <br> [ASP.net](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) veya [ASP.NET Core uygulamalarına](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) yönelik NuGet paketlerini yükleyerek uygulamanızın derleme zamanında eklendi to ApplicationInsights SDK 'sı olması gerekir |
| IIS Express | Desteklenmiyor

Yukarıdaki durumlarda, izleme altyapısının doğru şekilde doğrulandığının doğru şekilde doğrulanması, toplanan `DependencyTelemetry` SDK sürümünün ' rddp ' olduğunu doğrulamakdır. ' rdddsd ' veya ' rddf ', bağımlılıkların DiagnosticSource veya EventSource Callbacks aracılığıyla toplanacağını ve bu nedenle tam SQL sorgusunun yakalanmayacağını gösterir.

## <a name="where-to-find-dependency-data"></a>Bağımlılık verilerinin nerede bulunacağı

* [Uygulama Haritası](app-map.md) , uygulamanız ve komşu bileşenler arasındaki bağımlılıkları görselleştirir.
* [Işlem tanılama](transaction-diagnostics.md) Birleşik, bağıntılı sunucu verilerini gösterir.
* [Tarayıcılar sekmesi](javascript.md) kullanıcılarınızın TARAYıCıLARıNDAKI Ajax çağrılarını gösterir.
* Bağımlılık çağrılarını denetlemek için yavaş veya başarısız olan isteklerden öğesine tıklayın.
* [Analiz](#logs-analytics) , bağımlılık verilerini sorgulamak için kullanılabilir.

## <a name="diagnosis"></a>Yavaş istekleri tanılama

Her istek olayı, uygulamanız isteği işlerken bağımlılık çağrıları, özel durumlar ve izlenen diğer olaylarla ilişkilendirilir. Bu nedenle, bazı istekler hatalı çalışıyorsa, bir bağımlılıktan yavaş yanıt olup olmadığını öğrenebilirsiniz.

### <a name="tracing-from-requests-to-dependencies"></a>İsteklerden bağımlılıklara kadar izleme

**Performans** sekmesini açın ve işlemler yanındaki üst kısımdaki **Bağımlılıklar** sekmesine gidin.

Genel altında bir **bağımlılık adına** tıklayın. Bir bağımlılığı seçtikten sonra, bağımlılık sürelerinin süreleri için bir grafik sağda görünür.

![Performans sekmesinde, üstteki bağımlılık sekmesine tıklayın ve ardından grafikte bir bağımlılık adı](./media/asp-net-dependencies/2-perf-dependencies.png)

Uçtan uca işlem ayrıntılarını görmek için sağ alt köşedeki mavi **örnekler** düğmesine ve ardından bir örneğe tıklayın.

![Uçtan uca işlem ayrıntılarını görmek için bir örneğe tıklayın](./media/asp-net-dependencies/3-end-to-end.png)

### <a name="profile-your-live-site"></a>Canlı sitenizin profilini yapın

Saatin nerede gitcei? [Application Insights Profiler](../../azure-monitor/app/profiler.md) , canlı sitenize http çağrılarını izler ve kodunuzda en uzun süreyi geçen işlevleri gösterir.

## <a name="failed-requests"></a>Başarısız istekler

Başarısız olan istekler, bağımlılıklara yönelik başarısız çağrılar ile de ilişkilendirilebilir.

Soldaki **sorunlar** sekmesine gidebilir ve sonra üstteki **Bağımlılıklar** sekmesine tıklayabilirsiniz.

![Başarısız istekler grafiğine tıklayın](./media/asp-net-dependencies/4-fail.png)

Burada, başarısız bağımlılık sayısını görebileceksiniz. Alt tablodaki bir bağımlılık adına tıklatmaya çalışırken başarısız bir oluşum hakkında daha fazla bilgi almak için. Uçtan uca işlem ayrıntılarını almak için sağ alt köşedeki mavi **Bağımlılıklar** düğmesine tıklayabilirsiniz.

## <a name="logs-analytics"></a>Günlükler (Analiz)

[Kusto sorgu dilinde](/azure/kusto/query/)bağımlılıkları izleyebilirsiniz. Bazı örnekler aşağıda verilmiştir.

* Başarısız bağımlılık çağrılarını bulun:

``` Kusto

    dependencies | where success != "True" | take 10
```

* AJAX çağrılarını bul:

``` Kusto

    dependencies | where client_Type == "Browser" | take 10
```

* İsteklerle ilişkili bağımlılık çağrılarını bul:

``` Kusto

    dependencies
    | where timestamp > ago(1d) and  client_Type != "Browser"
    | join (requests | where timestamp > ago(1d))
      on operation_Id  
```


* Sayfa görünümleriyle ilişkili AJAX çağrılarını bulun:

``` Kusto 

    dependencies
    | where timestamp > ago(1d) and  client_Type == "Browser"
    | join (browserTimings | where timestamp > ago(1d))
      on operation_Id
```

## <a name="frequently-asked-questions"></a>Sık sorulan sorular

### <a name="how-does-automatic-dependency-collector-report-failed-calls-to-dependencies"></a>*Otomatik bağımlılık toplayıcı raporu bağımlılıklara nasıl çağrı başarısız oldu?*

* Başarısız bağımlılık çağrılarında ' success ' alanı false olarak ayarlanmalıdır. `DependencyTrackingTelemetryModule` `ExceptionTelemetry` raporlamaz. Bağımlılık için tam veri modeli [burada](data-model-dependency-telemetry.md)açıklanmıştır.

## <a name="open-source-sdk"></a>Açık kaynaklı SDK
Her Application Insights SDK gibi bağımlılık koleksiyonu modülü de açık kaynaktır. [Resmi GitHub](https://github.com/Microsoft/ApplicationInsights-dotnet-server)deposunda kodu okuyun ve katkıda bulunun veya sorunları raporla.

## <a name="next-steps"></a>Sonraki adımlar

* [Özel Durumlar](../../azure-monitor/app/asp-net-exceptions.md)
* [Kullanıcı & sayfası verileri](../../azure-monitor/app/javascript.md)
* [Kullanılabilirlik](../../azure-monitor/app/monitor-web-app-availability.md)
