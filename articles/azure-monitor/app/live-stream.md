---
title: Azure Application Insights özel ölçümler ve Tanılamalar ile Canlı Ölçüm Akışı | Microsoft Docs
description: Özel ölçümler sayesinde web uygulamanızı gerçek zamanlı olarak izleyin ve canlı arızaların, izlemelerin ve olayların bir akışı ile ilgili sorunları tanılayın.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/22/2019
ms.reviewer: sdash
ms.openlocfilehash: 69aaa61bb0be9a5f07de85ff4ef81b28a86aefaa
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73575609"
---
# <a name="live-metrics-stream-monitor--diagnose-with-1-second-latency"></a>Canlı Ölçüm Akışı: Izleme & Tanıla, 1 saniyelik gecikme

[Application Insights](../../azure-monitor/app/app-insights-overview.md)canlı ölçüm akışı kullanarak canlı, üretim dışı Web uygulamanızın beyana olan bölümünü araştırma. Hizmetinize herhangi bir olumsuz bakış olmadan, ölçümleri ve performans sayaçlarını gerçek zamanlı olarak izlemek için seçin ve filtreleyin. Örnek başarısız isteklerin ve özel durumların yığın izlemelerini inceleyin. [Profil Oluşturucu](../../azure-monitor/app/profiler.md), [anlık görüntü hata ayıklayıcısı](../../azure-monitor/app/snapshot-debugger.md)ile birlikte. Canlı Ölçüm Akışı, Canlı Web siteniz için güçlü ve yerleşik olmayan bir tanılama aracı sağlar.

Canlı Ölçüm Akışı, şunları yapabilirsiniz:

* Performans ve hata sayılarını izleyerek, yayımlanırken bir sorunu doğrulayın.
* Test yüklerinin etkisini izleyin ve sorunları canlı olarak tanılayın. 
* İzlemek istediğiniz ölçümleri seçip filtreleyerek, belirli test oturumlarına odaklayın veya bilinen sorunları filtreleyebilirsiniz.
* Özel durum izlemeleri gerçekleştikleri sürece alın.
* En ilgili KPI 'Ları bulmak için filtrelerle denemeler yapın.
* Tüm Windows performans sayaçlarını canlı izleyin.
* Sorun yaşayan bir sunucuyu kolayca tanımlayabilir ve tüm KPI/canlı beslemesini yalnızca o sunucuya filtreleyin.

[![Canlı Ölçüm Akışı videosu](./media/live-stream/youtube.png)](https://www.youtube.com/watch?v=zqfHf1Oi5PY)

Canlı ölçümler Şu anda ASP.NET, ASP.NET Core, Azure Işlevleri, Java ve Node. js uygulamaları için desteklenmektedir.

## <a name="get-started"></a>Başlarken

1. Henüz [Application Insights](../../azure-monitor/azure-monitor-app-hub.md) Web uygulamanıza yüklemediyseniz, bunu şimdi yapın.
2. Standart Application Insights paketlerine ek olarak, canlı ölçüm akışını etkinleştirmek için [Microsoft. ApplicationInsights. PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) gereklidir.
3. Application Insights paketinin **en son sürümüne güncelleştirin** . Visual Studio 'da projenize sağ tıklayın ve **NuGet Paketlerini Yönet**' i seçin. **Güncelleştirmeler** sekmesini açın ve tüm Microsoft. ApplicationInsights. * paketlerini seçin.

    Uygulamanızı yeniden dağıtın.

3. [Azure Portal](https://portal.azure.com), uygulamanız için Application Insights kaynağını açın ve ardından canlı akış ' yı açın.

4. Filtrelerinizin müşteri adları gibi hassas verileri kullanacaksanız [Denetim kanalını güvenli hale](#secure-the-control-channel) getirin.

### <a name="no-data-check-your-server-firewall"></a>Veri yok mu? Sunucu güvenlik duvarınızı denetleyin

[Canlı ölçüm akışı giden bağlantı noktalarını](../../azure-monitor/app/ip-addresses.md#outgoing-ports) , sunucularınızın güvenlik duvarında açık olduğunu kontrol edin.

## <a name="how-does-live-metrics-stream-differ-from-metrics-explorer-and-analytics"></a>Canlı Ölçüm Akışı Ölçüm Gezgini ve analiz 'den farklı midir?

| |Canlı Akış | Ölçüm Gezgini ve analiz |
|---|---|---|
|Gecikme süresi|Bir saniye içinde görünen veriler|Dakikada toplanan|
|Saklama yok|Veriler grafikte olduğu sırada devam ettirir ve sonra atılır|[90 gün boyunca tutulan veriler](../../azure-monitor/app/data-retention-privacy.md#how-long-is-the-data-kept)|
|İsteğe bağlı|Canlı ölçümleri açtığınızda veriler akışa kaydedilir|SDK her yüklendiğinde ve etkinleştirildiğinde veriler gönderilir|
|Ücretsiz|Canlı Akış verileri için ücret alınmaz|[Fiyatlandırmaya](../../azure-monitor/app/pricing.md) tabi
|Örnekleme|Tüm seçili ölçümler ve sayaçlar iletilir. Arızalar ve yığın izlemeleri örneklenir. TelemetryProcessors uygulanmıyor.|Olaylar [örneklenebilir](../../azure-monitor/app/api-filtering-sampling.md)|
|Denetim kanalı|Filtre denetim sinyalleri SDK 'ya gönderilir. Bu kanalın güvenli olmasını öneririz.|İletişim, portala tek bir yoldur|

## <a name="select-and-filter-your-metrics"></a>Ölçümlerinizi seçme ve filtreleme

(ASP.NET, ASP.NET Core ve Azure Işlevleri (v2) ile kullanılabilir.)

Portaldan herhangi bir Application Insights telemetrisine rastgele filtreler uygulayarak özel KPI 'ları izleyebilirsiniz. Grafiklerin herhangi birini fareyle fareyle gösteren filtre denetimine tıklayın. Aşağıdaki grafik, URL ve Duration özniteliklerinin filtrelerine sahip özel bir Istek sayısı KPI 'SI çizdirme. Herhangi bir zamanda belirttiğiniz ölçütlere uyan bir telemetri canlı akışını gösteren akış önizleme bölümüyle filtrelerinizi doğrulayın.

![Özel Istek KPI 'si](./media/live-stream/live-stream-filteredMetric.png)

Count 'tan farklı bir değeri izleyebilirsiniz. Seçenekler, hiçbir Application Insights telemetri olabilecek akış türüne bağlıdır: istekler, bağımlılıklar, özel durumlar, izlemeler, olaylar veya ölçümler. Kendi [özel ölçümünüzün](../../azure-monitor/app/api-custom-events-metrics.md#properties)olabilir:

![Değer seçenekleri](./media/live-stream/live-stream-valueoptions.png)

Application Insights telemetriye ek olarak, akış seçeneklerinden birini seçerek ve performans sayacının adını sağlayarak herhangi bir Windows performans sayacını da izleyebilirsiniz.

Canlı ölçümler iki noktaya toplanır: yerel olarak her bir sunucuda ve sonra tüm sunucularda. Varsayılan ayarları, ilgili açılan kutudan diğer seçenekleri seçerek değiştirebilirsiniz.

## <a name="sample-telemetry-custom-live-diagnostic-events"></a>Örnek telemetri: özel canlı tanılama olayları
Varsayılan olarak, canlı olay akışı, başarısız isteklerin ve bağımlılık çağrılarının, özel durumların, olayların ve izlemelerin örneklerini gösterir. İstediğiniz zaman herhangi bir noktada uygulanan ölçütü görmek için filtre simgesine tıklayın. 

![Varsayılan canlı akış](./media/live-stream/live-stream-eventsdefault.png)

Ölçümlerde olduğu gibi, Application Insights telemetri türlerinden herhangi birine herhangi bir rastgele ölçüt de belirtebilirsiniz. Bu örnekte, belirli istek başarısızlıklarını, izlemeleri ve olayları seçiyoruz. Ayrıca tüm özel durumlar ve bağımlılık arızaları de seçeceğiz.

![Özel canlı akış](./media/live-stream/live-stream-events.png)

Note: Şu anda, özel durum ileti tabanlı ölçütler için en dıştaki özel durum iletisini kullanın. Yukarıdaki örnekte, iç özel durum iletisi ile zararsız özel durumunu filtrelemek için ("<--" sınırlayıcı) "istemcinin bağlantısı kesildi." bir ileti kullanın-"istek içeriği okunurken hata" ölçütü içerir.

Canlı akıştaki bir öğenin ayrıntılarına tıklayarak bakınız. **Duraklat** ' a tıklayarak veya yalnızca aşağı kaydırarak ya da bir öğeye tıklayarak akışı duraklatabilirsiniz. Canlı akış, başa doğru kaydırdıktan veya duraklatıldığında toplanan öğe sayaçından tıklandıktan sonra sürdürülecek.

![Örneklenmiş canlı arızalar](./media/live-stream/live-metrics-eventdetail.png)

## <a name="filter-by-server-instance"></a>Sunucu örneğine göre filtrele

Belirli bir sunucu rolü örneğini izlemek isterseniz, sunucuya göre filtre uygulayabilirsiniz.

![Örneklenmiş canlı arızalar](./media/live-stream/live-stream-filter.png)

## <a name="secure-the-control-channel"></a>Denetim kanalının güvenliğini sağlama
Belirttiğiniz özel filtreler ölçütü, Application Insights SDK 'sindeki canlı ölçümler bileşenine geri gönderilir. Filtreler potansiyel olarak CustomerIDs gibi hassas bilgileri içerebilir. İzleme anahtarına ek olarak, kanalı gizli bir API anahtarı ile güvenli hale getirebilirsiniz.
### <a name="create-an-api-key"></a>API anahtarı oluşturma

![API anahtarı oluştur](./media/live-stream/live-metrics-apikeycreate.png)

### <a name="add-api-key-to-configuration"></a>Yapılandırmaya API anahtarı Ekle

### <a name="classic-aspnet"></a>Klasik ASP.NET

ApplicationInsights. config dosyasında, QuickPulseTelemetryModule öğesine AuthenticationApiKey ekleyin:
``` XML

<Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <AuthenticationApiKey>YOUR-API-KEY-HERE</AuthenticationApiKey>
</Add>

```
Veya kodda, QuickPulseTelemetryModule üzerinde ayarlayın:

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
using Microsoft.ApplicationInsights.Extensibility;

             TelemetryConfiguration configuration = new TelemetryConfiguration();
            configuration.InstrumentationKey = "YOUR-IKEY-HERE";

            QuickPulseTelemetryProcessor processor = null;

            configuration.TelemetryProcessorChainBuilder
                .Use((next) =>
                {
                    processor = new QuickPulseTelemetryProcessor(next);
                    return processor;
                })
                        .Build();

            var QuickPulse = new QuickPulseTelemetryModule()
            {

                AuthenticationApiKey = "YOUR-API-KEY"
            };
            QuickPulse.Initialize(configuration);
            QuickPulse.RegisterTelemetryProcessor(processor);
            foreach (var telemetryProcessor in configuration.TelemetryProcessors)
                {
                if (telemetryProcessor is ITelemetryModule telemetryModule)
                    {
                    telemetryModule.Initialize(configuration);
                    }
                }

```

### <a name="azure-function-apps"></a>Azure Işlevi uygulamaları

Azure Işlev uygulamaları (v2) için bir API anahtarı ile kanalın güvenliğinin sağlanması bir ortam değişkeniyle gerçekleştirilebilir.

Application Insights kaynağınız içinden bir API anahtarı oluşturun ve İşlev Uygulaması **uygulama ayarları** ' na gidin. **Yeni ayar Ekle** ' yi seçin ve bir `APPINSIGHTS_QUICKPULSEAUTHAPIKEY` adı ve API anahtarınıza karşılık gelen bir değer girin.

### <a name="aspnet-core-requires-application-insights-aspnet-core-sdk-230-beta-or-greater"></a>ASP.NET Core (Application Insights ASP.NET Core SDK 2.3.0-Beta veya daha üstü) gerektirir

Startup.cs dosyanızı aşağıdaki şekilde değiştirin:

İlk ekleme

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
```

Ardından, ConfigureServices yönteminin içinde şunu ekleyin:

```csharp
services.ConfigureTelemetryModule<QuickPulseTelemetryModule> ((module, o) => module.AuthenticationApiKey = "YOUR-API-KEY-HERE");
```

Bununla birlikte, tüm bağlı sunucuları tanıyor ve güveniyorsanız, kimlik doğrulamalı kanal olmadan özel filtreleri deneyebilirsiniz. Bu seçenek altı ay boyunca kullanılabilir. Bu geçersiz kılma her yeni oturum için veya yeni bir sunucu çevrimiçi olduğunda gereklidir.

![Canlı ölçümler kimlik doğrulama seçenekleri](./media/live-stream/live-stream-auth.png)

>[!NOTE]
>Filtre ölçütlerinde MüşteriNo gibi potansiyel hassas bilgileri girmeden önce kimliği doğrulanmış kanalı ayarlamanızı kesinlikle öneririz.
>

## <a name="supported-features-table"></a>Desteklenen Özellikler tablosu

| Dil                         | Temel ölçümler       | Performans ölçümleri | Özel filtreleme    | Örnek telemetri    | İşleme göre CPU bölme |
|----------------------------------|:--------------------|:--------------------|:--------------------|:--------------------|:---------------------|
| .NET                             | Desteklenen (V 2.7.2 +) | Desteklenen (V 2.7.2 +) | Desteklenen (V 2.7.2 +) | Desteklenen (V 2.7.2 +) | Desteklenen (V 2.7.2 +)  |
| .NET Core (Target =. NET Framework)| Desteklenen (V 2.4.1 +) | Desteklenen (V 2.4.1 +) | Desteklenen (V 2.4.1 +) | Desteklenen (V 2.4.1 +) | Desteklenen (V 2.4.1 +)  |
| .NET Core (Target =. NET Core)     | Desteklenen (V 2.4.1 +) | Destekleniyor*          | Desteklenen (V 2.4.1 +) | Desteklenen (V 2.4.1 +) | **Desteklenmiyor**    |
| Azure Işlevleri v2               | Destekleniyor           | Destekleniyor           | Destekleniyor           | Destekleniyor           | **Desteklenmiyor**    |
| Java                             | Desteklenen (V 2.0.0 +) | Desteklenen (V 2.0.0 +) | **Desteklenmiyor**   | **Desteklenmiyor**   | **Desteklenmiyor**    |
| Node.js                          | Desteklenen (V 1.3.0 +) | Desteklenen (V 1.3.0 +) | **Desteklenmiyor**   | Desteklenen (V 1.3.0 +) | **Desteklenmiyor**    |

Temel ölçümler istek, bağımlılık ve özel durum oranını içerir. Performans ölçümleri (performans sayaçları) bellek ve CPU içerir. Örnek telemetri, başarısız istekler ve bağımlılıklar, özel durumlar, olaylar ve izlemelerle ilgili ayrıntılı bilgilerin akışını gösterir.

 \* PerfCounters desteği, .NET Core 'un .NET Framework hedefleyemayan sürümlerinde biraz farklılık gösterir:

- PerfCounters ölçümleri, Windows için Azure App Service çalıştırılırken desteklenir. (AspNetCore SDK sürümü 2.4.1 veya üzeri)
- Uygulama herhangi bir Windows makinesinde (VM veya bulut hizmeti ya da şirket içi vb.) çalışırken PerfCounters desteklenir. (AspNetCore SDK Version 2.7.1 veya üzeri), ancak .NET Core 2,0 veya üstünü hedefleyen uygulamalar için.
- Uygulama her yerde (Linux, Windows, Linux için App Service, kapsayıcılar, vb.) en son beta sürümünde çalışırken PerfCounters desteklenir (ör. AspNetCore SDK Version 2.8.0-Beta1 veya üzeri), ancak .NET Core 2,0 veya üstünü hedefleyen uygulamalar için.

Varsayılan olarak, canlı ölçümler Node. js SDK 'sında devre dışıdır. Canlı ölçümleri etkinleştirmek için SDK 'Yı başlatırken [yapılandırma yöntemlerinizi](https://github.com/Microsoft/ApplicationInsights-node.js#configuration) `setSendLiveMetrics(true)` ekleyin.

## <a name="troubleshooting"></a>Sorun giderme

Veri yok mu? Uygulamanız korumalı bir ağda ise: Canlı Ölçüm Akışı diğer Application Insights telemetrisinden farklı IP adresleri kullanır. [Bu IP adreslerinin](../../azure-monitor/app/ip-addresses.md) güvenlik duvarınızdaki açık olduğundan emin olun.

## <a name="next-steps"></a>Sonraki adımlar
* [Application Insights ile kullanımı izleme](../../azure-monitor/app/usage-overview.md)
* [Tanılama aramasını kullanma](../../azure-monitor/app/diagnostic-search.md)
* [Profil Oluşturucu](../../azure-monitor/app/profiler.md)
* [Anlık görüntü hata ayıklayıcısı](../../azure-monitor/app/snapshot-debugger.md)
