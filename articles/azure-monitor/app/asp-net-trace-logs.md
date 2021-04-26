---
title: Application Insights 'de .NET izleme günlüklerini keşfet
description: Trace, NLog veya Log4Net tarafından oluşturulan arama günlükleri.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 05/08/2019
ms.openlocfilehash: 083ddbd06561550f89e414d6c679cdc6433fa338
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98937556"
---
# <a name="explore-netnet-core-and-python-trace-logs-in-application-insights"></a>Application Insights 'de .NET/.NET Core ve Python izleme günlüklerini keşfet

ILogger, NLog, log4Net veya System. Diagnostics. Trace 'ten [Azure Application Insights][start]Için ASP.NET/ASP.NET Core uygulamanız için Tanılama izleme günlükleri gönderin. Python uygulamaları için, Azure Izleyici için OpenCensus Python 'da AzureLogHandler kullanarak Tanılama izleme günlükleri gönderin. Daha sonra bunları keşfedebilir ve arayabilirsiniz. Bu Günlükler, uygulamanızdaki diğer günlük dosyalarıyla birleştirilir, böylece her Kullanıcı isteğiyle ilişkili izlemeleri tanımlayabilir ve bunları diğer olaylar ve özel durum raporlarıyla ilişkilendirebilir.

> [!NOTE]
> Günlük yakalama modülüne mi ihtiyacınız var? Bu, üçüncü taraf Günlükçüler için kullanışlı bir bağdaştırıcıdır. Ancak zaten NLog, log4Net veya System. Diagnostics. Trace kullanmıyorsanız, yalnızca [**Application Insights TrackTrace ()**](./api-custom-events-metrics.md#tracktrace) yöntemini doğrudan çağırmayı düşünün.
>
>
## <a name="install-logging-on-your-app"></a>Uygulamanıza günlük yüklemesi
Seçtiğiniz günlük çerçevesini projenize yükleyerek app.config veya web.config bir girişin oluşmasına neden olmalıdır.

```XML
 <configuration>
  <system.diagnostics>
    <trace>
      <listeners>
        <add name="myAppInsightsListener" type="Microsoft.ApplicationInsights.TraceListener.ApplicationInsightsTraceListener, Microsoft.ApplicationInsights.TraceListener" />
      </listeners>
    </trace>
  </system.diagnostics>
</configuration>
```

## <a name="configure-application-insights-to-collect-logs"></a>Günlükleri toplamak için Application Insights yapılandırma
Henüz yapmadıysanız [projenize Application Insights ekleyin](./asp-net.md) . Günlük toplayıcıyı dahil etmek için bir seçenek göreceksiniz.

Veya **Application Insights yapılandırmak** için Çözüm Gezgini ' de projenize sağ tıklayın. **İzleme koleksiyonunu Yapılandır** seçeneğini belirleyin.

> [!NOTE]
> Application Insights menü veya günlük Toplayıcı seçeneği yok mu? [Sorun gidermeyi](#troubleshooting)deneyin.

## <a name="manual-installation"></a>El ile yükleme
Proje türü Application Insights yükleyicisi (örneğin, bir Windows Masaüstü Projesi) tarafından desteklenmiyorsa bu yöntemi kullanın.

1. Log4Net veya NLog kullanmayı planlıyorsanız, bunu projenize yükleyebilirsiniz.
2. Çözüm Gezgini, projenize sağ tıklayın ve **NuGet Paketlerini Yönet**' i seçin.
3. "Application Insights" araması yapın.
4. Aşağıdaki paketlerden birini seçin:

   - ILogger için: [Microsoft. Extensions. Logging. ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/) 
 [ ![ NuGet ILogger başlığı](https://img.shields.io/nuget/vpre/Microsoft.Extensions.Logging.ApplicationInsights.svg)](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/)
   - NLog için: [Microsoft. ApplicationInsights. nlogtarget](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/) 
 [ ![ NuGet NLog başlığı](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.NLogTarget.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
   - Log4Net için: [Microsoft. ApplicationInsights. Log4NetAppender](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/) 
 [ ![ NuGet Log4Net başlık](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.Log4NetAppender.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
   - System. Diagnostics: [Microsoft. ApplicationInsights. TraceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/) 
 [ ![ NuGet System. Diagnostics başlığı](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.TraceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/) için
   - [Microsoft. ApplicationInsights. DiagnosticSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/) 
 [ ![ NuGet tanılama kaynağı dinleyicisi başlığı](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.DiagnosticSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/)
   - [Microsoft. ApplicationInsights. EtwCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/) 
 [ ![ NuGet ETW toplayıcı başlığı](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EtwCollector.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/)
   - [Microsoft. ApplicationInsights. EventSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/) 
 [ ![ NuGet olay kaynağı dinleyicisi başlığı](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EventSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/)

NuGet paketi gerekli derlemeleri yükleyip web.config veya varsa app.config değiştirir.

## <a name="ilogger"></a>ILogger

Application Insights Illogger uygulamasının konsol uygulamaları ve ASP.NET Core ile kullanılması örnekleri için bkz. [.NET Core ıllogger Için Applicationınsightsloggerprovider](ilogger.md).

## <a name="insert-diagnostic-log-calls"></a>Tanılama günlüğü çağrıları Ekle
System. Diagnostics. Trace kullanırsanız, tipik bir çağrı şöyle olur:

```csharp
System.Diagnostics.Trace.TraceWarning("Slow response - database01");
```

Log4net veya NLog tercih ediyorsanız şunu kullanın:

```csharp
    logger.Warn("Slow response - database01");
```

## <a name="use-eventsource-events"></a>EventSource olaylarını kullanma
[System. Diagnostics. Tracing. EventSource](/dotnet/api/system.diagnostics.tracing.eventsource) olaylarını, izleme olarak Application Insights gönderilmek üzere yapılandırabilirsiniz. İlk olarak, `Microsoft.ApplicationInsights.EventSourceListener` NuGet paketini yüklemeniz gerekir. Sonra `TelemetryModules` [ApplicationInsights.config](./configuration-with-applicationinsights-config.md) dosyasının bölümünü düzenleyin.

```xml
    <Add Type="Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule, Microsoft.ApplicationInsights.EventSourceListener">
      <Sources>
        <Add Name="MyCompany" Level="Verbose" />
      </Sources>
    </Add>
```

Her kaynak için aşağıdaki parametreleri ayarlayabilirsiniz:
 * **Name** toplanacak EventSource öğesinin adını belirtir.
 * **Düzey** toplanacak günlüğe kaydetme düzeyini belirtir: *Critical*, *Error*, *bilgilendirici*, *LogAlways*, *verbose* veya *Warning*.
 * **Anahtar sözcükler** (isteğe bağlı) kullanılacak anahtar sözcük birleşimlerinin tamsayı değerini belirtir.

## <a name="use-diagnosticsource-events"></a>DiagnosticSource olaylarını kullanma
[System. Diagnostics. DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) olaylarını, izleme olarak Application Insights gönderilmek üzere yapılandırabilirsiniz. İlk olarak, [`Microsoft.ApplicationInsights.DiagnosticSourceListener`](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener) NuGet paketini yüklemeniz gerekir. Sonra [ApplicationInsights.config](./configuration-with-applicationinsights-config.md) dosyanın "TelemetryModules" bölümünü düzenleyin.

```xml
    <Add Type="Microsoft.ApplicationInsights.DiagnosticSourceListener.DiagnosticSourceTelemetryModule, Microsoft.ApplicationInsights.DiagnosticSourceListener">
      <Sources>
        <Add Name="MyDiagnosticSourceName" />
      </Sources>
    </Add>
```

İzlemek istediğiniz her bir DiagnosticSource için, **Name** özniteliğiyle diagnosticsource adına ayarlanmış bir giriş ekleyin.

## <a name="use-etw-events"></a>ETW olaylarını kullanma
Windows için olay Izleme (ETW) olaylarını, izleme olarak Application Insights gönderilmek üzere yapılandırabilirsiniz. İlk olarak, `Microsoft.ApplicationInsights.EtwCollector` NuGet paketini yüklemeniz gerekir. Sonra [ApplicationInsights.config](./configuration-with-applicationinsights-config.md) dosyanın "TelemetryModules" bölümünü düzenleyin.

> [!NOTE] 
> ETW olayları yalnızca, SDK 'Yı barındıran işlem, performans günlüğü kullanıcılarının veya yöneticilerinin üyesi olan bir kimlik altında çalışıyorsa toplanabilir.

```xml
    <Add Type="Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule, Microsoft.ApplicationInsights.EtwCollector">
      <Sources>
        <Add ProviderName="MyCompanyEventSourceName" Level="Verbose" />
      </Sources>
    </Add>
```

Her kaynak için aşağıdaki parametreleri ayarlayabilirsiniz:
 * **ProviderName** toplanacak ETW sağlayıcısının adıdır.
 * **ProviderGuid** toplanacak ETW sağlayıcısının GUID değerini belirtir. Yerine kullanılabilir `ProviderName` .
 * **Düzey** , toplanacak günlüğe kaydetme düzeyini ayarlar. *Kritik*, *hata*, *bilgilendirici*, *LogAlways*, *verbose* veya *Uyarı* olabilir.
 * **Anahtar sözcükler** (isteğe bağlı) kullanılacak anahtar sözcük birleşimlerinin tamsayı değerini ayarlayın.

## <a name="use-the-trace-api-directly"></a>Trace API 'sini doğrudan kullanma
Application Insights Trace API 'sine doğrudan çağrı yapabilirsiniz. Günlüğe kaydetme bağdaştırıcıları bu API 'YI kullanır.

Örnek:

```csharp
TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();
var telemetryClient = new TelemetryClient(configuration);
telemetry.TrackTrace("Slow response - database01");
```

TrackTrace 'in avantajı, oldukça uzun verileri iletiye koyacağınızdır. Örneğin, veri Gönder ' i burada bulabilirsiniz.

İletinize önem düzeyi de ekleyebilirsiniz. Diğer telemetri gibi, farklı izleme kümelerini filtrelemek veya aramak için özellik değerleri ekleyebilirsiniz. Örnek:

  ```csharp
  TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();
  var telemetryClient = new TelemetryClient(configuration);
  telemetryClient.TrackTrace("Slow database response",
                              SeverityLevel.Warning,
                              new Dictionary<string, string> { { "database", "db.ID" } });
  ```

Bu, belirli bir veritabanıyla ilgili belirli bir önem derecesindeki tüm iletileri [aramanızı][diagnostic] kolayca filtrelemenizi sağlar.

## <a name="azureloghandler-for-opencensus-python"></a>OpenCensus Python için AzureLogHandler
Azure Izleyici günlüğü Işleyicisi, Python günlüklerini Azure Izleyici 'ye vermenize olanak tanır.

Azure Izleyici için [Opencensus Python SDK 'sını](./opencensus-python.md) kullanarak uygulamanızı işaretleyin.

Bu örnek, Azure Izleyici 'ye bir uyarı düzeyi günlüğünün nasıl gönderileceğini gösterir.

```python
import logging

from opencensus.ext.azure.log_exporter import AzureLogHandler

logger = logging.getLogger(__name__)
logger.addHandler(AzureLogHandler(connection_string='InstrumentationKey=<your-instrumentation_key-here>'))
logger.warning('Hello, World!')
```

## <a name="explore-your-logs"></a>Günlüklerinizi araştırma
Uygulamanızı hata ayıklama modunda çalıştırın veya canlı olarak dağıtın.

Uygulamanızın genel bakış bölmesinde [Application Insights portalında][portal] [Ara][diagnostic]' yı seçin.

Örneğin:

* Günlük izlemelerinde veya belirli özelliklere sahip öğelerde filtre uygulayın.
* Belirli bir öğeyi ayrıntılı olarak inceleyin.
* Aynı kullanıcı isteğiyle ilgili diğer sistem günlüğü verilerini bulun (aynı operationId değerine sahiptir).
* Bir sayfanın yapılandırmasını sık kullanılan olarak kaydedin.

> [!NOTE]
>Uygulamanız çok miktarda veri gönderirse ve ASP.NET Version 2.0.0-Beta3 veya üzeri için Application Insights SDK kullanıyorsanız, *Uyarlamalı örnekleme* özelliği yalnızca telemetrinizin bir kısmını kullanabilir ve gönderebilir. [Örnekleme hakkında daha fazla bilgi edinin.](./sampling.md)
>

## <a name="troubleshooting"></a>Sorun giderme
### <a name="how-do-i-do-this-for-java"></a>Bunu Java için Nasıl yaparım? misiniz?
Java kodsuz kullanacaksınız araçları 'nda (önerilir) Günlükler kutudan alınır ve [Java 3,0 Aracısı](./java-in-process-agent.md)' nı kullanın.

Java SDK 'sını kullanıyorsanız, [Java günlük bağdaştırıcılarını](./java-trace-logs.md)kullanın.

### <a name="theres-no-application-insights-option-on-the-project-context-menu"></a>Proje bağlam menüsünde Application Insights seçeneği yoktur
* Developer Analytics Tools geliştirme makinesinde yüklü olduğundan emin olun. Visual Studio **araçları**  >  **uzantıları ve güncelleştirmelerinde**, **Developer Analytics Tools** bakın. **Yüklü** sekmesinde yoksa, **çevrimiçi** sekmesini açın ve yükleme.
* Bu, Developer Analytics Tools desteklemediği bir proje türü olabilir. [El ile yükleme](#manual-installation)kullanın.

### <a name="theres-no-log-adapter-option-in-the-configuration-tool"></a>Yapılandırma aracında günlük bağdaştırıcısı seçeneği yok
* Önce günlüğe kaydetme çerçevesini yükler.
* System. Diagnostics. Trace kullanıyorsanız, [ *web.config*' de yapılandırdığınızdan](/dotnet/api/system.diagnostics.eventlogtracelistener)emin olun.
* Application Insights en son sürümüne sahip olduğunuzdan emin olun. Visual Studio 'da **Araçlar**  >  **Uzantılar ve güncelleştirmeler**' e gidin ve **güncelleştirmeler** sekmesini açın. **Developer Analytics Tools** , güncelleştirmek için seçin.

### <a name="i-get-the-instrumentation-key-cannot-be-empty-error-message"></a><a name="emptykey"></a>"Izleme anahtarı boş olamaz" hata iletisini alıyorum
Büyük olasılıkla Application Insights yüklemeden günlük bağdaştırıcısı NuGet paketini yüklediniz. Çözüm Gezgini ' de, *ApplicationInsights.config*' a sağ tıklayın ve **Application Insights Güncelleştir**' i seçin. Azure 'da oturum açmanız ve bir Application Insights kaynağı oluşturmanız veya var olan bir kaynağın yeniden kullanılması istenir. Bu, sorunu çözmelidir.

### <a name="i-can-see-traces-but-not-other-events-in-diagnostic-search"></a>İzleme, tanılama aramasında diğer olayları görebiliyorum
İşlem hattını almak için tüm olaylar ve isteklerin zaman alabilir.

### <a name="how-much-data-is-retained"></a><a name="limits"></a>Ne kadar veri tutuluyor?
Birçok etken, tutulan veri miktarını etkiler. Daha fazla bilgi için, müşteri olay ölçümleri sayfasının [sınırlar](./api-custom-events-metrics.md#limits) bölümüne bakın.

### <a name="i-dont-see-some-log-entries-that-i-expected"></a>Beklendiğim bazı günlük girdilerini göremiyorum
Uygulamanız Voluminous miktarda veri gönderiyorsa ve ASP.NET sürümü 2.0.0-Beta3 veya üzeri için Application Insights SDK kullanıyorsanız, uyarlamalı örnekleme özelliği yalnızca telemetrinizin bir kısmını kullanabilir ve gönderebilir. [Örnekleme hakkında daha fazla bilgi edinin.](./sampling.md)

## <a name="next-steps"></a><a name="add"></a>Sonraki adımlar

* [ASP.NET içinde sorunları ve özel durumları tanılama][exceptions]
* [Arama hakkında daha fazla bilgi edinin][diagnostic]
* [Kullanılabilirlik ve yanıt verme testlerini ayarlama][availability]
* [Sorun giderme][qna]

<!--Link references-->

[availability]: ./monitor-web-app-availability.md
[diagnostic]: ./diagnostic-search.md
[exceptions]: asp-net-exceptions.md
[portal]: https://portal.azure.com/
[qna]: ../faq.md
[start]: ./app-insights-overview.md

