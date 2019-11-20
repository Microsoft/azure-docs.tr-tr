---
title: Application Insights 'da performans sayaçları | Microsoft Docs
description: Application Insights 'de sistem ve özel .NET performans sayaçlarını izleyin.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 12/13/2018
ms.openlocfilehash: 229216ee873ade9418574141017aaf88235ba9e4
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72820703"
---
# <a name="system-performance-counters-in-application-insights"></a>Application Insights 'de sistem performans sayaçları

Windows tarafından CPU doluluğu, bellek, disk ve ağ kullanımı gibi şeylere yönelik birçok çeşit [performans sayacı](https://docs.microsoft.com/windows/desktop/PerfCtrs/about-performance-counters) sunulur. Ayrıca, kendi performans Sayaçlarınızı tanımlayabilirsiniz. Performans sayaçları koleksiyonu, uygulamanız şirket içi bir konakta veya yönetim erişiminizin olduğu sanal makinede çalıştığı sürece desteklenir. Azure Web Apps olarak çalışan uygulamaların performans sayaçlarına doğrudan erişimi olmasa da, kullanılabilir sayaçların bir alt kümesi Application Insights tarafından toplanır.

## <a name="view-counters"></a>Sayaçları görüntüle

Ölçümler bölmesi varsayılan performans sayacı kümesini gösterir.

![Application Insights bildirilen performans sayaçları](./media/performance-counters/performance-counters.png)

ASP.NET/ASP.NET Core Web uygulamaları için toplanmaya yapılandırılmış geçerli varsayılan sayaçlar şunlardır:
- % İşlem\\Işlemci zamanı
- % İşlem\\Işlemci zamanı normalleştirmesi
- Bellek\\kullanılabilir bayt sayısı
- ASP.NET Istek/sn
- Oluşturulan .NET CLR özel durumları/sn
- ASP.NET ApplicationsRequest yürütme süresi
- İşlem\\özel baytlar
- İşlem\\GÇ veri baytı/sn
- Uygulama kuyruğundaki Istekleri\\ASP.NET uygulamalar
- İşlemci (_Total)\\% Işlemci zamanı

## <a name="add-counters"></a>Sayaç Ekle

İstediğiniz performans sayacı ölçüm listesine dahil değilse, ekleyebilirsiniz.

1. Yerel sunucuda bu PowerShell komutunu kullanarak sunucunuzda hangi sayaçların kullanılabildiğini öğrenin:

    `Get-Counter -ListSet *`

    (Bkz. [`Get-Counter`](https://technet.microsoft.com/library/hh849685.aspx).)
2. ApplicationInsights. config dosyasını açın.

   * Geliştirme sırasında uygulamanıza Application Insights eklediyseniz, projenizdeki ApplicationInsights. config dosyasını düzenleyin ve ardından sunucularınıza yeniden dağıtın.
3. Performans toplayıcı yönergesini düzenleyin:

    ```XML

        <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
          <Counters>
            <Add PerformanceCounter="\Objects\Processes"/>
            <Add PerformanceCounter="\Sales(photo)\# Items Sold" ReportAs="Photo sales"/>
          </Counters>
        </Add>
    ```

> [!NOTE]
> ASP.NET Core uygulamalar `ApplicationInsights.config`sahip değildir ve bu nedenle yukarıdaki yöntem ASP.NET Core uygulamalar için geçerli değildir.

Hem standart sayaçları hem de uyguladığınızı yakalayabilirsiniz. `\Objects\Processes`, tüm Windows sistemlerinde kullanılabilen standart bir sayaca bir örnektir. `\Sales(photo)\# Items Sold`, bir Web hizmetinde uygulanabilir olabilecek özel bir sayaca örnektir.

Biçim `\Category(instance)\Counter"`veya örnekleri olmayan kategoriler için yalnızca `\Category\Counter`.

`[a-zA-Z()/-_ \.]+` eşleşmeyen sayaç adları için `ReportAs` gereklidir; diğer bir deyişle, şu kümeler içinde olmayan karakterler içerirler: harfler, yuvarlak köşeli ayraç, eğik çizgi, tire, alt çizgi, boşluk, nokta.

Bir örnek belirtirseniz, bildirilen ölçümün "CounterInstanceName" boyutu olarak toplanır.

### <a name="collecting-performance-counters-in-code-for-aspnet-web-applications-or-netnet-core-console-applications"></a>ASP.NET Web uygulamaları veya .NET/.NET Core konsol uygulamaları için kodda performans sayaçlarını toplama
Sistem performans sayaçlarını toplamak ve bunları Application Insights göndermek için aşağıdaki kod parçacığını uyarlayabilirsiniz:


```csharp
    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\Process([replace-with-application-process-name])\Page Faults/sec", "PageFaultsPerfSec")));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```

Veya oluşturduğunuz özel ölçümlerle aynı şeyi yapabilirsiniz:

```csharp
    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\Sales(photo)\# Items Sold", "Photo sales"));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```

### <a name="collecting-performance-counters-in-code-for-aspnet-core-web-applications"></a>ASP.NET Core Web uygulamaları için kodda performans sayaçlarını toplama

`Startup.cs` sınıfınıza `ConfigureServices` yöntemi aşağıda gösterildiği gibi değiştirin.

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // The following configures PerformanceCollectorModule.
  services.ConfigureTelemetryModule<PerformanceCollectorModule>((module, o) =>
            {
                // the application process name could be "dotnet" for ASP.NET Core self-hosted applications.
                module.Counters.Add(new PerformanceCounterCollectionRequest(
    @"\Process([replace-with-application-process-name])\Page Faults/sec", "DotnetPageFaultsPerfSec"));
            });
    }
```

## <a name="performance-counters-in-analytics"></a>Analytics 'te performans sayaçları
[Analiz](../../azure-monitor/app/analytics.md)içinde performans sayacı raporlarını arayabilir ve görüntüleyebilirsiniz.

**PerformanceCounters** şeması, her bir performans sayacının `category`, `counter` adını ve `instance` adını gösterir.  Her uygulama için telemetri bölümünde yalnızca bu uygulama için sayaçları görürsünüz. Örneğin, hangi sayaçların kullanılabildiğini görmek için: 

![Application Insights Analytics 'te performans sayaçları](./media/performance-counters/analytics-performance-counters.png)

(' Örnek ' burada rol veya sunucu makinesi örneği değil, performans sayacı örneğine başvurur. Performans sayacı örneği adı genellikle işlem veya uygulamanın adına göre işlemci zamanı gibi sayaçları kesimler.)

En son dönemde kullanılabilir bellek grafiğini almak için: 

![Application Insights Analytics 'te bellek zaman grafiği](./media/performance-counters/analytics-available-memory.png)

Diğer telemetri gibi, **PerformanceCounters** da uygulamanızın üzerinde çalıştığı ana bilgisayar sunucusu örneğinin kimliğini belirten `cloud_RoleInstance` bir sütun içerir. Örneğin, farklı makinelerdeki uygulamanızın performansını karşılaştırmak için: 

![Application Insights Analytics 'te rol örneğine göre kesimli performans](./media/performance-counters/analytics-metrics-role-instance.png)

## <a name="aspnet-and-application-insights-counts"></a>ASP.NET ve Application Insights sayıları

*Özel durum oranı ve özel durumlar ölçümleri arasındaki fark nedir?*

* *Özel durum oranı* bir sistem performans sayacıdır. CLR, oluşturulan tüm işlenmiş ve işlenmemiş özel durumları sayar ve bir örnekleme aralığındaki toplamı aralığın uzunluğuna böler. Application Insights SDK bu sonucu toplayıp portala gönderir.

* *Özel durumlar* , grafiğin örnekleme aralığı içinde Portal tarafından alınan trackexception raporlarının sayılarıdır. Yalnızca kodunuzda TrackException çağrılarını yazdığınız ve [işlenmemiş özel durumları](../../azure-monitor/app/asp-net-exceptions.md)içermeyen işlenmiş özel durumları içerir. 

## <a name="performance-counters-for-applications-running-in-azure-web-apps"></a>Azure Web Apps çalışan uygulamalar için performans sayaçları

Hem ASP.NET hem de Azure Web Apps dağıtılan ASP.NET Core uygulamaları özel bir korumalı ortamda çalışır. Bu ortam sistem performans sayaçlarına doğrudan erişime izin vermez. Ancak, sayaçların sınırlı bir alt kümesi [burada](https://github.com/projectkudu/kudu/wiki/Perf-Counters-exposed-as-environment-variables)açıklandığı gibi ortam değişkenleri olarak sunulur. ASP.NET için SDK Application Insights ve ASP.NET Core, bu özel ortam değişkenlerinden Azure Web Apps performans sayaçlarını toplar. Bu ortamda yalnızca bir sayaç alt kümesi kullanılabilir ve tam liste [burada bulunabilir.](https://github.com/microsoft/ApplicationInsights-dotnet-server/blob/develop/Src/PerformanceCollector/Perf.Shared/Implementation/WebAppPerformanceCollector/CounterFactory.cs)

## <a name="performance-counters-in-aspnet-core-applications"></a>ASP.NET Core uygulamalarında performans sayaçları

ASP.NET Core performans sayaçları için destek sınırlıdır:

* [SDK](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) sürümleri 2.4.1 ve üzeri, uygulama Azure Web Apps (Windows) üzerinde çalışıyorsa performans sayaçlarını toplar.
* SDK sürümleri 2.7.1 ve üzeri, uygulama Windows 'da çalışıyorsa ve `NETSTANDARD2.0` veya daha sonra hedeflerse performans sayaçlarını toplar.
* .NET Framework hedefleyen uygulamalar için SDK 'nın tüm sürümleri performans sayaçlarını destekler.
* SDK sürümleri 2.8.0 ve üzeri, Linux 'ta CPU/bellek sayacını destekler. Linux 'ta başka bir sayaç desteklenmez. Linux 'ta (ve diğer Windows dışı ortamlarda) sistem sayaçlarını almanın önerilen yolu [Eventcounters](eventcounters.md) kullanmaktır

## <a name="alerts"></a>Uyarılar
Diğer ölçümler gibi, bir performans sayacı belirttiğiniz sınırın dışında kaldığında sizi uyarmak üzere [bir uyarı ayarlayabilirsiniz](../../azure-monitor/app/alerts.md) . Uyarılar bölmesini açın ve uyarı Ekle ' ye tıklayın.

## <a name="next"></a>Sonraki adımlar

* [Bağımlılık izleme](../../azure-monitor/app/asp-net-dependencies.md)
* [Özel durum izleme](../../azure-monitor/app/asp-net-exceptions.md)

