---
title: Windows masaüstü uygulamaları için kullanımı ve performansı izleme
description: Application Insights ile Windows masaüstü uygulamanızın kullanımını ve performansını analiz edin.
ms.topic: conceptual
ms.date: 06/11/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 15331494b40021f10c162fba75abf6fe88d2d419
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100583327"
---
# <a name="monitoring-usage-and-performance-in-classic-windows-desktop-apps"></a>Klasik Windows Masaüstü uygulamalarında kullanımı ve performansı izleme

Şirket içinde, Azure’da ve diğer bulutlarda barındırılan tüm uygulamalar Application Insights’tan faydalanabilir. Tek sınırlama Application Insights hizmetine [iletişim izni verme](./ip-addresses.md) gerekliliğidir. Evrensel Windows Platformu (UWP) uygulamalarını izlemek için [Visual Studio App Center](../app/mobile-center-quickstart.md)’ı öneririz.

## <a name="to-send-telemetry-to-application-insights-from-a-classic-windows-application"></a>Bir Klasik Windows uygulamasından Application Insights’a telemetri göndermek için
1. [Azure portalında](https://portal.azure.com)[bir Application Insights kaynağı oluşturun](./create-new-resource.md). 
2. İzleme Anahtarının bir kopyasını oluşturun.
3. Visual Studio’da uygulama projenizin NuGet paketlerini düzenleyin ve şunu ekleyin: Microsoft.ApplicationInsights.WindowsServer. (Veya temel API 'yi yalnızca standart telemetri koleksiyonu modülleri olmadan istiyorsanız Microsoft. ApplicationInsights ' ı seçin.)
4. İzleme anahtarını kodunuzda ayarlayın:
   
    `TelemetryConfiguration.Active.InstrumentationKey = "`*anahtarınız*`";`
   
    veya ApplicationInsights.config öğesinde ayarlayın (standart telemetri paketlerinden birini yüklediyseniz):
   
    `<InstrumentationKey>`*anahtarınız*`</InstrumentationKey>` 
   
    ApplicationInsights.config dosyasını kullanırsanız, bunun özelliklerinin **Build Action = Content, Copy to Output Directory = Copy** olarak ayarlandığından emin olun.
5. Telemetri göndermek için [API’yi kullanın](./api-custom-events-metrics.md).
6. Uygulamanızı çalıştırın ve Azure portal oluşturduğunuz kaynakta Telemetriyi görüntüleyin.

## <a name="example-code"></a><a name="telemetry"></a>Örnek kod

```csharp
using Microsoft.ApplicationInsights;

    public partial class Form1 : Form
    {
        private TelemetryClient tc = new TelemetryClient();
        ...
        private void Form1_Load(object sender, EventArgs e)
        {
            // Alternative to setting ikey in config file:
            tc.InstrumentationKey = "key copied from portal";

            // Set session data:
            tc.Context.Session.Id = Guid.NewGuid().ToString();
            tc.Context.Device.OperatingSystem = Environment.OSVersion.ToString();

            // Log a page view:
            tc.TrackPageView("Form1");
            ...
        }

        protected override void OnFormClosing(System.Windows.Forms.FormClosingEventArgs e)
        {
            if (tc != null)
            {
                tc.Flush(); // only for desktop apps

                // Allow time for flushing:
                System.Threading.Thread.Sleep(1000);
            }
            base.OnFormClosing(e);
        }
        
        ...
```

## <a name="override-storage-of-computer-name"></a>Bilgisayar adının depolanmasını geçersiz kıl

Varsayılan olarak, bu SDK sistem yayma telemetrinin bilgisayar adını toplayıp depolar.

Bilgisayar adı, dahili fatura amaçları için Application Insights [eski Kurumsal (düğüm başına) Fiyatlandırma Katmanı](./pricing.md#legacy-enterprise-per-node-pricing-tier) tarafından kullanılır. Varsayılan olarak, geçersiz kılmak için bir telemetri başlatıcısı kullanırsanız `telemetry.Context.Cloud.RoleInstance` , `ai.internal.nodeName` bilgisayar adı değerini de içerecek şekilde ayrı bir özellik gönderilir. Bu değer, Application Insights telemetriyle birlikte depolanmayacak, ancak eski düğüm tabanlı faturalandırma modeliyle geriye dönük uyumluluk sağlamak için dahili olarak kullanılır.

[Eski Kurumsal (düğüm başına) fiyatlandırma katmanındaysa](./pricing.md#legacy-enterprise-per-node-pricing-tier) ve yalnızca bilgisayar adının depolanmasını geçersiz kılmanız gerekiyorsa, bir telemetri başlatıcısı kullanın:

**Aşağıdaki gibi özel Telemetryınitializer yazın.**

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;

namespace CustomInitializer.Telemetry
{
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleInstance))
            {
                // Set custom role name here. Providing an empty string will result
                // in the computer name still be sent via this property.
                  telemetry.Context.Cloud.RoleInstance = "Custom RoleInstance";
            }
        }
    }
}
```

`Program.cs` `Main()` İzleme anahtarını ayarlamak için aşağıdaki yöntemde başlatıcıyı oluşturun:

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;

   static void Main()
        {
            TelemetryConfiguration.Active.InstrumentationKey = "{Instrumentation-key-here}";
            TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
            //...
        }
```

## <a name="override-transmission-of-computer-name"></a>Bilgisayar adının aktarımını geçersiz kıl

[Eski Kurumsal (düğüm başına) fiyatlandırma katmanında](./pricing.md#legacy-enterprise-per-node-pricing-tier) değilseniz ve bilgisayar adı içeren herhangi bir telemetrinin gönderilmesini tamamen engellemek isterseniz, bir telemetri işlemcisi kullanmanız gerekir.

### <a name="telemetry-processor"></a>Telemetri işlemcisi

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;


namespace WindowsFormsApp2
{
    public class CustomTelemetryProcessor : ITelemetryProcessor
    {
        private readonly ITelemetryProcessor _next;

        public CustomTelemetryProcessor(ITelemetryProcessor next)
        {
            _next = next;
        }

        public void Process(ITelemetry item)
        {
            if (item != null)
            {
                item.Context.Cloud.RoleInstance = string.Empty;
            }

            _next.Process(item);
        }
    }
}
```

`Program.cs` `Main()` İzleme anahtarını ayarlamak için aşağıdaki yöntemde telemetri işlemcisini oluşturun:

```csharp
using Microsoft.ApplicationInsights.Extensibility;

namespace WindowsFormsApp2
{
    static class Program
    {
        static void Main()
        {
            TelemetryConfiguration.Active.InstrumentationKey = "{Instrumentation-key-here}";
            var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
            builder.Use((next) => new CustomTelemetryProcessor(next));
            builder.Build();
            //...
        }
    }
}

```

> [!NOTE]
> [Eski Kurumsal (düğüm başına) fiyatlandırma katmanında](./pricing.md#legacy-enterprise-per-node-pricing-tier)olsanız bile, daha önce açıklandığı gibi bir telemetri işlemcisi de kullanabilirsiniz. Bu, düğüm başına fiyatlandırma için düğümleri doğru bir şekilde ayırt edememe nedeniyle, yüksek faturalandırma için olası bir işlem oluşmasına neden olur.

## <a name="next-steps"></a>Sonraki adımlar
* [Pano oluşturma](./overview-dashboard.md)
* [Tanılama Araması](./diagnostic-search.md)
* [Ölçümleri keşfetme](../essentials/metrics-charts.md)
* [Analytics sorguları yazma](../logs/log-query-overview.md)

