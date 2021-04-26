---
title: Canlı Ölçüm Akışı ile tanılama-Azure Application Insights
description: Özel ölçümler sayesinde web uygulamanızı gerçek zamanlı olarak izleyin ve canlı arızaların, izlemelerin ve olayların bir akışı ile ilgili sorunları tanılayın.
ms.topic: conceptual
ms.date: 04/22/2019
ms.reviewer: sdash
ms.openlocfilehash: 865de94f1d9b4012a908643bbf87f38aeb8594a0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98679475"
---
# <a name="live-metrics-stream-monitor--diagnose-with-1-second-latency"></a>Canlı Ölçüm Akışı: Izleme & Tanıla, 1 saniyelik gecikme

[Application Insights](./app-insights-overview.md)' dan canlı ölçüm akışı (quickpulse olarak da bilinir) kullanarak canlı, üretim içi Web uygulamanızı izleyin. Hizmetinize herhangi bir olumsuz bakış olmadan, ölçümleri ve performans sayaçlarını gerçek zamanlı olarak izlemek için seçin ve filtreleyin. Örnek başarısız isteklerin ve özel durumların yığın izlemelerini inceleyin. [Profil Oluşturucu](./profiler.md) ve [anlık görüntü hata ayıklayıcısı](./snapshot-debugger.md)ile birlikte canlı ölçüm akışı, Canlı Web siteniz için güçlü ve olmayan bir tanılama aracı sağlar.

Canlı Ölçüm Akışı, şunları yapabilirsiniz:

* Performans ve hata sayılarını izleyerek, yayımlanırken bir sorunu doğrulayın.
* Test yüklerinin etkisini izleyin ve sorunları canlı olarak tanılayın.
* İzlemek istediğiniz ölçümleri seçip filtreleyerek, belirli test oturumlarına odaklayın veya bilinen sorunları filtreleyebilirsiniz.
* Özel durum izlemeleri gerçekleştikleri sürece alın.
* En ilgili KPI 'Ları bulmak için filtrelerle denemeler yapın.
* Tüm Windows performans sayaçlarını canlı izleyin.
* Sorun yaşayan bir sunucuyu kolayca tanımlayabilir ve tüm KPI/canlı beslemesini yalnızca o sunucuya filtreleyin.

![Canlı ölçümler sekmesi](./media/live-stream/live-metric.png)

Canlı ölçümler Şu anda ASP.NET, ASP.NET Core, Azure Işlevleri, Java ve Node.js uygulamaları için desteklenmektedir.

## <a name="get-started"></a>başlarken

1. Canlı ölçümleri etkinleştirmek için dile özgü yönergeleri izleyin.
   * [ASP.net](./asp-net.md) -canlı ölçümler varsayılan olarak etkindir.
   * [ASP.NET Core](./asp-net-core.md)-canlı ölçümler varsayılan olarak etkindir.
   * [.Net/.NET Core konsolu/Worker](./worker-service.md)-Live ölçümleri varsayılan olarak etkinleştirilmiştir.
   * [.NET uygulamaları-kod kullanarak etkinleştirin](#enable-livemetrics-using-code-for-any-net-application).
    * [Java](./java-in-process-agent.md) -canlı ölçümler varsayılan olarak etkindir.
   * [Node.js](./nodejs.md#live-metrics)

2. [Azure Portal](https://portal.azure.com), uygulamanız için Application Insights kaynağını açın ve ardından canlı akış ' yı açın.

3. Filtrelerinizin müşteri adları gibi hassas verileri kullanacaksanız [Denetim kanalını güvenli hale](#secure-the-control-channel) getirin.

### <a name="enable-livemetrics-using-code-for-any-net-application"></a>Herhangi bir .NET uygulaması için kod kullanarak Liveölçümlerini etkinleştirin

Canlı ölçümler, .NET uygulamaları için önerilen yönergeleri kullanarak ekleme sırasında varsayılan olarak etkin olsa da, aşağıdakiler canlı ölçümleri el ile nasıl ayarlayacağınız gösterilmiştir.

1. [Microsoft. ApplicationInsights. PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector) NuGet paketini yükler
2. Aşağıdaki örnek konsol uygulaması kodu, canlı ölçümleri ayarlamayı gösterir.

```csharp
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
using System;
using System.Threading.Tasks;

namespace LiveMetricsDemo
{
    class Program
    {
        static void Main(string[] args)
        {
            // Create a TelemetryConfiguration instance.
            TelemetryConfiguration config = TelemetryConfiguration.CreateDefault();
            config.InstrumentationKey = "INSTRUMENTATION-KEY-HERE";
            QuickPulseTelemetryProcessor quickPulseProcessor = null;
            config.DefaultTelemetrySink.TelemetryProcessorChainBuilder
                .Use((next) =>
                {
                    quickPulseProcessor = new QuickPulseTelemetryProcessor(next);
                    return quickPulseProcessor;
                })
                .Build();

            var quickPulseModule = new QuickPulseTelemetryModule();

            // Secure the control channel.
            // This is optional, but recommended.
            quickPulseModule.AuthenticationApiKey = "YOUR-API-KEY-HERE";
            quickPulseModule.Initialize(config);
            quickPulseModule.RegisterTelemetryProcessor(quickPulseProcessor);

            // Create a TelemetryClient instance. It is important
            // to use the same TelemetryConfiguration here as the one
            // used to setup Live Metrics.
            TelemetryClient client = new TelemetryClient(config);

            // This sample runs indefinitely. Replace with actual application logic.
            while (true)
            {
                // Send dependency and request telemetry.
                // These will be shown in Live Metrics stream.
                // CPU/Memory Performance counter is also shown
                // automatically without any additional steps.
                client.TrackDependency("My dependency", "target", "http://sample",
                    DateTimeOffset.Now, TimeSpan.FromMilliseconds(300), true);
                client.TrackRequest("My Request", DateTimeOffset.Now,
                    TimeSpan.FromMilliseconds(230), "200", true);
                Task.Delay(1000).Wait();
            }
        }
    }
}
```

Yukarıdaki örnek bir konsol uygulaması için olduğunda, aynı kod tüm .NET uygulamalarında kullanılabilir. Telemetriyi otomatik toplayan başka bir TelemetryModules etkinse, bu modüllerin başlatılmasına yönelik aynı yapılandırmanın de canlı ölçüm modülü için kullanıldığından emin olmak önemlidir.

## <a name="how-does-live-metrics-stream-differ-from-metrics-explorer-and-analytics"></a>Canlı Ölçüm Akışı Ölçüm Gezgini ve analiz 'den farklı midir?

| |Canlı Akış | Ölçüm Gezgini ve analiz |
|---|---|---|
|**Gecikme süresi**|Bir saniye içinde görünen veriler|Dakikada toplanan|
|**Saklama yok**|Veriler grafikte olduğu sırada devam ettirir ve sonra atılır|[90 gün boyunca tutulan veriler](./data-retention-privacy.md#how-long-is-the-data-kept)|
|**İsteğe bağlı**|Veriler yalnızca canlı ölçümler bölmesi açıkken akışlıdır |SDK her yüklendiğinde ve etkinleştirildiğinde veriler gönderilir|
|**Ücretsiz**|Canlı Akış verileri için ücret alınmaz|[Fiyatlandırmaya](./pricing.md) tabi
|**Örnekleme**|Tüm seçili ölçümler ve sayaçlar iletilir. Arızalar ve yığın izlemeleri örneklenir. |Olaylar [örneklenebilir](./api-filtering-sampling.md)|
|**Denetim kanalı**|Filtre denetim sinyalleri SDK 'ya gönderilir. Bu kanalın güvenli olmasını öneririz.|İletişim, portala tek bir yoldur|

## <a name="select-and-filter-your-metrics"></a>Ölçümlerinizi seçme ve filtreleme

(ASP.NET, ASP.NET Core ve Azure Işlevleri (v2) ile kullanılabilir.)

Portaldan herhangi bir Application Insights telemetrisine rastgele filtreler uygulayarak özel KPI 'ları izleyebilirsiniz. Grafiklerin herhangi birini fareyle fareyle gösteren filtre denetimine tıklayın. Aşağıdaki grafik, URL ve Duration özniteliklerinin filtrelerine sahip özel bir Istek sayısı KPI 'SI çizdirme. Herhangi bir zamanda belirttiğiniz ölçütlere uyan bir telemetri canlı akışını gösteren akış önizleme bölümüyle filtrelerinizi doğrulayın.

![Filtre isteği hızı](./media/live-stream/filter-request.png)

Count 'tan farklı bir değeri izleyebilirsiniz. Seçenekler, hiçbir Application Insights telemetri olabilecek akış türüne bağlıdır: istekler, bağımlılıklar, özel durumlar, izlemeler, olaylar veya ölçümler. Kendi [özel ölçümünüzün](./api-custom-events-metrics.md#properties)olabilir:

![Özel ölçüm ile istek hızında Sorgu Oluşturucu](./media/live-stream/query-builder-request.png)

Application Insights telemetriye ek olarak, akış seçeneklerinden birini seçerek ve performans sayacının adını sağlayarak herhangi bir Windows performans sayacını da izleyebilirsiniz.

Canlı ölçümler iki noktaya toplanır: yerel olarak her bir sunucuda ve sonra tüm sunucularda. Varsayılan ayarları, ilgili açılan kutudan diğer seçenekleri seçerek değiştirebilirsiniz.

## <a name="sample-telemetry-custom-live-diagnostic-events"></a>Örnek telemetri: özel canlı tanılama olayları
Varsayılan olarak, canlı olay akışı, başarısız isteklerin ve bağımlılık çağrılarının, özel durumların, olayların ve izlemelerin örneklerini gösterir. İstediğiniz zaman herhangi bir noktada uygulanan ölçütü görmek için filtre simgesine tıklayın.

![Filtre düğmesi](./media/live-stream/filter.png)

Ölçümlerde olduğu gibi, Application Insights telemetri türlerinden herhangi birine herhangi bir rastgele ölçüt de belirtebilirsiniz. Bu örnekte, belirli istek başarısızlıklarını ve olayları seçiyoruz.

![Sorgu Tasarımcısı](./media/live-stream/query-builder.png)

> [!NOTE]
> Şu anda, özel durum iletisi tabanlı ölçütler için en dıştaki özel durum iletisini kullanın. Yukarıdaki örnekte, iç özel durum iletisi ile zararsız özel durumunu filtrelemek için ("<--" sınırlayıcı) "istemcinin bağlantısı kesildi." bir ileti kullanın-"istek içeriği okunurken hata" ölçütü içerir.

Canlı akıştaki bir öğenin ayrıntılarına tıklayarak bakınız. **Duraklat** ' a tıklayarak veya yalnızca aşağı kaydırarak ya da bir öğeye tıklayarak akışı duraklatabilirsiniz. Canlı akış, başa doğru kaydırdıktan veya duraklatıldığında toplanan öğe sayaçından tıklandıktan sonra sürdürülecek.

![Ekran görüntüsü, bir özel durum seçili olan örnek telemetri penceresini ve pencerenin alt kısmında gösterilen özel durum ayrıntılarını gösterir.](./media/live-stream/sample-telemetry.png)

## <a name="filter-by-server-instance"></a>Sunucu örneğine göre filtrele

Belirli bir sunucu rolü örneğini izlemek isterseniz, sunucuya göre filtre uygulayabilirsiniz. Filtrelemek için *sunucular* altında sunucu adını seçin.

![Örneklenmiş canlı arızalar](./media/live-stream/filter-by-server.png)

## <a name="secure-the-control-channel"></a>Denetim kanalının güvenliğini sağlama

> [!NOTE]
> Şu anda yalnızca kod tabanlı izleme kullanarak kimliği doğrulanmış bir kanal ayarlayabilir ve kodsuz kullanacaksınız Attach kullanarak sunucuların kimliğini doğrulayamamaktadır.

Canlı ölçümler portalında belirttiğiniz özel filtreler ölçütü, Application Insights SDK 'sindeki canlı ölçümler bileşenine geri gönderilir. Filtreler potansiyel olarak CustomerIDs gibi hassas bilgileri içerebilir. İzleme anahtarına ek olarak, kanalı gizli bir API anahtarı ile güvenli hale getirebilirsiniz.

### <a name="create-an-api-key"></a>API anahtarı oluşturma

![API anahtarı > API anahtarı ](./media/live-stream/api-key.png)
 ![ oluşturma API anahtarı oluştur. "SDK denetim kanalını Doğrula" ve "anahtar oluştur" seçeneğini belirleyin.](./media/live-stream/create-api-key.png)

### <a name="add-api-key-to-configuration"></a>Yapılandırmaya API anahtarı Ekle

### <a name="aspnet"></a>ASP.NET

applicationinsights.config dosyasında, QuickPulseTelemetryModule öğesine AuthenticationApiKey değerini ekleyin:

```XML
<Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <AuthenticationApiKey>YOUR-API-KEY-HERE</AuthenticationApiKey>
</Add>
```

### <a name="aspnet-core"></a>ASP.NET Core

[ASP.NET Core](./asp-net-core.md) uygulamalar için aşağıdaki yönergeleri izleyin.

`ConfigureServices`Başlangıç. cs dosyanızı aşağıdaki gibi değiştirin:

Aşağıdaki ad alanını ekleyin.

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
```

Ardından `ConfigureServices` yöntemi aşağıda gösterildiği gibi değiştirin.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // existing code which include services.AddApplicationInsightsTelemetry() to enable Application Insights.
    services.ConfigureTelemetryModule<QuickPulseTelemetryModule> ((module, o) => module.AuthenticationApiKey = "YOUR-API-KEY-HERE");
}
```

ASP.NET Core uygulamalarını yapılandırma hakkında daha fazla bilgi, [ASP.NET Core telemetri modüllerini yapılandırma](./asp-net-core.md#configuring-or-removing-default-telemetrymodules)kılavuzumuza bulunabilir.

### <a name="workerservice"></a>WorkerService

[Workerservice](./worker-service.md) uygulamaları için aşağıdaki yönergeleri izleyin.

Aşağıdaki ad alanını ekleyin.

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
```

Sonra, çağrıdan önce aşağıdaki satırı ekleyin `services.AddApplicationInsightsTelemetryWorkerService` .

```csharp
    services.ConfigureTelemetryModule<QuickPulseTelemetryModule> ((module, o) => module.AuthenticationApiKey = "YOUR-API-KEY-HERE");
```

WorkerService uygulamalarını yapılandırma hakkında daha fazla bilgi için, [WorkerServices 'de telemetri modülleri yapılandırma](./worker-service.md#configuring-or-removing-default-telemetrymodules)kılavuzumuzdan bulunabilir.

### <a name="azure-function-apps"></a>Azure İşlev Uygulamaları

Azure Işlev uygulamaları (v2) için bir API anahtarı ile kanalın güvenliğinin sağlanması bir ortam değişkeniyle gerçekleştirilebilir.

Application Insights kaynağınız içinden bir API anahtarı oluşturun ve İşlev Uygulaması **ayarlar > yapılandırma** ' ya gidin. **Yeni uygulama ayarı** ' nı seçin ve bir ad `APPINSIGHTS_QUICKPULSEAUTHAPIKEY` ve API anahtarınıza karşılık gelen bir değer girin.

Bununla birlikte, tüm bağlı sunucuları tanıyor ve güveniyorsanız, kimlik doğrulamalı kanal olmadan özel filtreleri deneyebilirsiniz. Bu seçenek altı ay boyunca kullanılabilir. Bu geçersiz kılma her yeni oturum için veya yeni bir sunucu çevrimiçi olduğunda gereklidir.

![Canlı ölçümler kimlik doğrulama seçenekleri](./media/live-stream/live-stream-auth.png)

>[!NOTE]
>Filtre ölçütlerinde MüşteriNo gibi potansiyel hassas bilgileri girmeden önce kimliği doğrulanmış kanalı ayarlamanızı kesinlikle öneririz.
>

## <a name="supported-features-table"></a>Desteklenen Özellikler tablosu

| Dil                         | Temel ölçümler       | Performans ölçümleri | Özel filtreleme    | Örnek telemetri    | İşleme göre CPU bölme |
|----------------------------------|:--------------------|:--------------------|:--------------------|:--------------------|:---------------------|
| .NET Framework                   | Desteklenen (V 2.7.2 +) | Desteklenen (V 2.7.2 +) | Desteklenen (V 2.7.2 +) | Desteklenen (V 2.7.2 +) | Desteklenen (V 2.7.2 +)  |
| .NET Core (Target =. NET Framework)| Desteklenen (V 2.4.1 +) | Desteklenen (V 2.4.1 +) | Desteklenen (V 2.4.1 +) | Desteklenen (V 2.4.1 +) | Desteklenen (V 2.4.1 +)  |
| .NET Core (Target =. NET Core)     | Desteklenen (V 2.4.1 +) | Destekleniyor*          | Desteklenen (V 2.4.1 +) | Desteklenen (V 2.4.1 +) | **Desteklenmiyor**    |
| Azure Işlevleri v2               | Desteklenir           | Desteklenir           | Desteklenir           | Desteklenir           | **Desteklenmiyor**    |
| Java                             | Desteklenen (V 2.0.0 +) | Desteklenen (V 2.0.0 +) | **Desteklenmiyor**   | **Desteklenmiyor**   | **Desteklenmiyor**    |
| Node.js                          | Desteklenen (V 1.3.0 +) | Desteklenen (V 1.3.0 +) | **Desteklenmiyor**   | Desteklenen (V 1.3.0 +) | **Desteklenmiyor**    |

Temel ölçümler istek, bağımlılık ve özel durum oranını içerir. Performans ölçümleri (performans sayaçları) bellek ve CPU içerir. Örnek telemetri, başarısız istekler ve bağımlılıklar, özel durumlar, olaylar ve izlemelerle ilgili ayrıntılı bilgilerin akışını gösterir.

 \* PerfCounters desteği, .NET Core 'un .NET Framework hedefleyemediğinden biraz farklılık gösterir:

- PerfCounters ölçümleri, Windows için Azure App Service çalıştırılırken desteklenir. (AspNetCore SDK sürümü 2.4.1 veya üzeri)
- Uygulama herhangi bir Windows makinesinde (VM veya bulut hizmeti ya da şirket içi vb.) çalışırken PerfCounters desteklenir. (AspNetCore SDK Version 2.7.1 veya üzeri), ancak .NET Core 2,0 veya üstünü hedefleyen uygulamalar için.
- Uygulama, en son sürümlerde (örneğin, AspNetCore SDK sürümü 2.8.0 veya üzeri), ancak yalnızca .NET Core 2,0 veya üstünü hedefleyen uygulamalar için her yerde (Linux, Windows, Linux için App Service, kapsayıcılar vb.) çalışırken PerfCounters desteklenir.

## <a name="troubleshooting"></a>Sorun giderme

Canlı Ölçüm Akışı, diğer Application Insights telemetrisinden farklı IP adresleri kullanır. [Bu IP adreslerinin](./ip-addresses.md) güvenlik duvarınızdaki açık olduğundan emin olun. Ayrıca, [canlı ölçüm akışı giden bağlantı noktalarını](./ip-addresses.md#outgoing-ports) sunucularınızın güvenlik duvarında açık olduğunu kontrol edin.

## <a name="next-steps"></a>Sonraki adımlar

* [Application Insights ile kullanımı izleme](./usage-overview.md)
* [Tanılama aramasını kullanma](./diagnostic-search.md)
* [Profil Oluşturucu](./profiler.md)
* [Anlık görüntü hata ayıklayıcısı](./snapshot-debugger.md)