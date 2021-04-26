---
title: Azure Application Insights telemetri örnekleme | Microsoft Docs
description: Denetim altında telemetri hacmini tutma.
ms.topic: conceptual
ms.date: 01/17/2020
ms.reviewer: vitalyg
ms.custom: fasttrack-edit
ms.openlocfilehash: ba7892c8afbe8e557c7dcf9aa3bd663f53a5728f
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834759"
---
# <a name="sampling-in-application-insights"></a>Application Insights’ta örnekleme

Örnekleme, [Azure Application Insights](./app-insights-overview.md)'deki bir özelliktir. Uygulama verilerinin istatistiksel olarak doğru analizini korurken telemetri trafiğini, veri maliyetlerini ve depolama maliyetlerini azaltmak için önerilen yoldur. Örnekleme, telemetrinizi azaltmayı Application Insights önlemeye de yardımcı olur. Örnekleme filtresi, tanılama araştırmalar yaparken öğeler arasında gezinebilmeniz için ilgili öğeleri seçer.

Portalda ölçüm sayıları sunulduklarında, bunlar hesap örneklemesi içine alınır. Bunun yapılması, istatistiklerdeki etkileri en aza indirir.

## <a name="brief-summary"></a>Kısa Özet

* Üç farklı örnekleme türü vardır: Uyarlamalı örnekleme, sabit fiyat örnekleme ve Alım örnekleme.
* Uyarlamalı örnekleme, Application Insights ASP.NET ve ASP.NET Core yazılım geliştirme setlerinin (SDK) en son sürümlerinde varsayılan olarak etkindir. [Azure işlevleri](../../azure-functions/functions-overview.md)tarafından da kullanılır.
* Sabit hızlı örnekleme, ASP.NET, ASP.NET Core, Java (hem aracı hem de SDK) ve Python için Application Insights SDK 'larının son sürümlerinde kullanılabilir.
* Alma örnekleme, Application Insights hizmeti uç noktasında çalışmaktadır. Yalnızca başka bir örnekleme etkin olmadığında geçerlidir. SDK, telemetrinizi örnekleyerek alma örneklemesini devre dışı bırakılır.
* Web uygulamaları için, özel olayları günlüğe kaydeder ve bir olay kümesinin birlikte tutulup tutulmayacağından emin olmanız gerekiyorsa, olayların aynı değere sahip olması gerekir `OperationId` .
* Analiz sorguları yazarsanız, [örnekleme hesabı](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#aggregations)almalısınız. Özellikle de kayıtları saymak yerine, kullanmanız gerekir `summarize sum(itemCount)` .
* Performans ölçümleri ve özel ölçümler de dahil olmak üzere bazı telemetri türleri, örnekleme 'nın etkin olup olmamasına bakılmaksızın her zaman tutulur.

Aşağıdaki tabloda her SDK ve uygulama türü için kullanılabilir örnekleme türleri özetlenmektedir:

| Application Insights SDK | Uyarlamalı örnekleme destekleniyor | Sabit hızlı örnekleme destekleniyor | Alım örnekleme destekleniyor |
|-|-|-|-|
| ASP.NET | [Evet (varsayılan olarak açık)](#configuring-adaptive-sampling-for-aspnet-applications) | [Evet](#configuring-fixed-rate-sampling-for-aspnet-applications) | Yalnızca başka bir örnekleme geçerli değilse |
| ASP.NET Core | [Evet (varsayılan olarak açık)](#configuring-adaptive-sampling-for-aspnet-core-applications) | [Evet](#configuring-fixed-rate-sampling-for-aspnet-core-applications) | Yalnızca başka bir örnekleme geçerli değilse |
| Azure İşlevleri | [Evet (varsayılan olarak açık)](#configuring-adaptive-sampling-for-azure-functions) | No | Yalnızca başka bir örnekleme geçerli değilse |
| Java | No | [Evet](#configuring-fixed-rate-sampling-for-java-applications) | Yalnızca başka bir örnekleme geçerli değilse |
| Node.JS | No | [Evet](./nodejs.md#sampling) | Yalnızca başka bir örnekleme geçerli değilse
| Python | No | [Evet](#configuring-fixed-rate-sampling-for-opencensus-python-applications) | Yalnızca başka bir örnekleme geçerli değilse |
| Tüm diğerleri | Hayır | Hayır | [Evet](#ingestion-sampling) |

> [!NOTE]
> Bu sayfanın büyük bir yanındaki bilgiler Application Insights SDK 'ların güncel sürümleri için geçerlidir. SDK 'ların eski sürümleri hakkında bilgi için [aşağıdaki bölüme bakın](#older-sdk-versions).

## <a name="types-of-sampling"></a>Örnekleme türleri

Üç farklı örnekleme yöntemi vardır:

* **Uyarlamalı örnekleme** , ASP.NET/ASP.NET Core uygulamanızda ve Azure IŞLEVLERI 'nden SDK 'dan gönderilen telemetri hacmini otomatik olarak ayarlar. Bu, ASP.NET veya ASP.NET Core SDK kullandığınızda varsayılan örneklemeyle belirlenir. Uyarlamalı örnekleme Şu anda yalnızca ASP.NET sunucu tarafı telemetri ve Azure Işlevleri için kullanılabilir.

* **Sabit fiyat örnekleme** , hem ASP.net, hem de ASP.NET Core ya da Java sunucunuzun ve kullanıcılarınızın tarayıcılarınızdan gönderilen telemetri hacmini azaltır. Oranı ayarlarsınız. İstemci ve sunucu, arama sırasında ilgili sayfa görünümleri ve istekleri arasında gezinebilmeniz için örneklemesini eşitler.

* Alma **örnekleme** , Application Insights hizmeti uç noktasında gerçekleşir. Sizin ayarladığınız örnekleme ücretine göre uygulamanızdan gelen telemetrinin bir kısmını atar. Uygulamanızdan gönderilen telemetri trafiğini azaltmaz, ancak aylık kotasında tutmanıza yardımcı olur. Alım örnekleme 'nın başlıca avantajı, örnekleme hızını uygulamanızı yeniden dağıtmaya gerek kalmadan ayarlayabilmektir. Alım örneklemesi tüm sunucular ve istemciler için tek bir işlem yapar, ancak başka herhangi bir örnekleme türü işlem sırasında uygulanmaz.

> [!IMPORTANT]
> Bir telemetri türü için uyarlamalı veya sabit oran örnekleme yöntemleri etkinleştirilmişse, bu telemetri için alma örnekleme devre dışı bırakılır. Bununla birlikte, SDK düzeyinde örnekleme dışında bırakılan telemetri türleri, portalda ayarlanan ücret örneklemeye tabi olmaya devam edecektir.

## <a name="adaptive-sampling"></a>Uyarlamalı örnekleme

Uyarlamalı örnekleme, Web sunucusu uygulamanızdan Application Insights hizmeti uç noktasına gönderilen telemetri hacmini etkiler.

> [!TIP]
> Uyarlamalı örnekleme, ASP.NET SDK veya ASP.NET Core SDK kullandığınızda varsayılan olarak etkindir ve ayrıca Azure Işlevleri için varsayılan olarak etkinleştirilmiştir.

Birim, belirtilen maksimum trafik oranı içinde tutulacak şekilde otomatik olarak ayarlanır ve ayarı aracılığıyla denetlenir `MaxTelemetryItemsPerSecond` . Uygulama, hata ayıklama sırasında veya düşük kullanım nedeniyle düşük miktarda telemetri üretirse, birim aşağıda olduğu sürece, öğeler örnekleme işlemcisi tarafından atılamaz `MaxTelemetryItemsPerSecond` . Telemetri hacmi arttıkça örnekleme hızı, hedef birime ulaşmak üzere ayarlanır. Ayarlama, düzenli aralıklarla yeniden hesaplanır ve giden aktarım hızının hareketli ortalamasını temel alır.

Hedef birime ulaşmak için, oluşturulan telemetrinin bazıları atılır. Ancak, diğer örnekleme türleri gibi algoritma ilgili telemetri öğelerini korur. Örneğin, aramada Telemetriyi incelerken belirli bir özel durumla ilgili isteği bulabilirsiniz.

İstek hızı ve özel durum oranı gibi ölçüm sayıları örnekleme hızının dengelemeye göre ayarlanır, böylece Ölçüm Gezgini 'nde yaklaşık doğru değerler gösterilir.

### <a name="configuring-adaptive-sampling-for-aspnet-applications"></a>ASP.NET uygulamaları için uyarlamalı örnekleme yapılandırma

> [!NOTE]
> Bu bölüm, uygulamaları ASP.NET Core için değil, ASP.NET uygulamaları için geçerlidir. [Bu belgede daha sonra ASP.NET Core uygulamalar için uyarlamalı örnekleme yapılandırma hakkında bilgi edinin.](#configuring-adaptive-sampling-for-aspnet-core-applications)

İçinde [`ApplicationInsights.config`](./configuration-with-applicationinsights-config.md) , düğümdeki çeşitli parametreleri ayarlayabilirsiniz `AdaptiveSamplingTelemetryProcessor` . Gösterilen rakamlar varsayılan değerlerdir:

* `<MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>`
  
    Uyarlamalı algoritmaların **her bir sunucu konağında** toplanmasını sağlayan [mantıksal işlemlerin](./correlation.md#data-model-for-telemetry-correlation) hedef oranı. Web uygulamanız birçok ana bilgisayarda çalışıyorsa, bu değeri, Application Insights portalındaki hedef trafik ücretine devam etmek için küçültün.

* `<EvaluationInterval>00:00:15</EvaluationInterval>` 
  
    Geçerli telemetri hızının yeniden değerlendirilme aralığı. Değerlendirme, hareketli ortalama olarak gerçekleştirilir. Telemetriyi ani bursts 'e tabi olursa bu aralığı kısaltmak isteyebilirsiniz.

* `<SamplingPercentageDecreaseTimeout>00:02:00</SamplingPercentageDecreaseTimeout>`
  
    Örnekleme yüzdesi değeri değiştiğinde, daha az veri yakalamak için örnekleme yüzdesini yeniden düşürmenize ne kadar yakında izin vereceğiz?

* `<SamplingPercentageIncreaseTimeout>00:15:00</SamplingPercentageIncreaseTimeout>`
  
    Örnekleme yüzdesi değeri değiştiğinde, daha kısa süre sonra örnekleme yüzdesini daha fazla veri yakalamak için yeniden arttırıyoruz.

* `<MinSamplingPercentage>0.1</MinSamplingPercentage>`
  
    Örnekleme yüzdesi farklılık gösterdiğinden, ayarlamaya izin verilen en küçük değer ne olur?

* `<MaxSamplingPercentage>100.0</MaxSamplingPercentage>`
  
    Örnekleme yüzdesi farklılık gösterdiğinden, ayarlamaya izin verilen en büyük değer ne olur?

* `<MovingAverageRatio>0.25</MovingAverageRatio>` 
  
    Hareketli ortalama hesaplamasında bu, en son değere atanması gereken ağırlığı belirtir. 1 ' den küçük veya buna eşit bir değer kullanın. Daha küçük değerler, algoritmayı ani değişikliklere karşı daha az reaktif hale getirir.

* `<InitialSamplingPercentage>100</InitialSamplingPercentage>`
  
    Uygulama yeni başladığında örnek telemetri miktarı. Hata ayıklarken bu değeri küçültmeyin.

* `<ExcludedTypes>Trace;Exception</ExcludedTypes>`
  
    Örneklemeye tabi olmasını istemediğiniz türlerin noktalı virgülle ayrılmış listesi. Tanınan türler şunlardır: `Dependency` , `Event` , `Exception` , `PageView` , `Request` , `Trace` . Belirtilen türlerin tüm telemetrisi iletilir; Belirtilmemiş Türler örneklenir.

* `<IncludedTypes>Request;Dependency</IncludedTypes>`
  
    Örneklemeye tabi olmak istediğiniz türlerin noktalı virgülle ayrılmış listesi. Tanınan türler şunlardır: `Dependency` , `Event` , `Exception` , `PageView` , `Request` , `Trace` . Belirtilen türler örneklenir; diğer türlerin tüm telemetrisi her zaman iletilir.

Uyarlamalı örnekleme **devre dışı bırakmak için** `AdaptiveSamplingTelemetryProcessor` düğümleri öğesinden kaldırın `ApplicationInsights.config` .

#### <a name="alternative-configure-adaptive-sampling-in-code"></a>Alternatif: kodda Uyarlamalı örnekleme yapılandırma

Dosyasında örnekleme parametresini ayarlamak yerine `.config` , bu değerleri programlı olarak ayarlayabilirsiniz.

1. Tüm `AdaptiveSamplingTelemetryProcessor` düğümleri `.config` dosyadan kaldırın.
2. Uyarlamalı örnekleme yapılandırmak için aşağıdaki kod parçacığını kullanın:

    ```csharp
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.Channel.Implementation;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    
    // ...

    var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
    // For older versions of the Application Insights SDK, use the following line instead:
    // var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;

    // Enable AdaptiveSampling so as to keep overall telemetry volume to 5 items per second.
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5);

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();
    ```

    ([Telemetri işlemcileri hakkında bilgi edinin](./api-filtering-sampling.md#filtering).)

Ayrıca, her bir telemetri türü için örnekleme hızını tek tek ayarlayabilir veya belirli türleri, hatta tüm örneklerden bağımsız olarak dışarıda bırakabilirsiniz:

```csharp
// The following configures adaptive sampling with 5 items per second, and also excludes Dependency telemetry from being subjected to sampling.
builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5, excludedTypes: "Dependency");
```

### <a name="configuring-adaptive-sampling-for-aspnet-core-applications"></a>ASP.NET Core uygulamalar için uyarlamalı örnekleme yapılandırma

`ApplicationInsights.config`ASP.NET Core uygulama yok, bu nedenle tüm yapılandırma kod aracılığıyla yapılır.
Uyarlamalı örnekleme, tüm ASP.NET Core uygulamalar için varsayılan olarak etkindir. Örnekleme davranışını devre dışı bırakabilir veya özelleştirebilirsiniz.

#### <a name="turning-off-adaptive-sampling"></a>Uyarlamalı örnekleme kapatılıyor

Varsayılan örnekleme özelliği, yöntemi içinde Application Insights hizmeti eklenirken devre dışı bırakılabilir `ConfigureServices` `ApplicationInsightsServiceOptions` `Startup.cs` :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ...

    var aiOptions = new Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions();
    aiOptions.EnableAdaptiveSampling = false;
    services.AddApplicationInsightsTelemetry(aiOptions);

    //...
}
```

Yukarıdaki kod, uyarlamalı örneklemeyi devre dışı bırakacak. Daha fazla özelleştirme seçeneği ile örnekleme eklemek için aşağıdaki adımları izleyin.

#### <a name="configure-sampling-settings"></a>Örnekleme ayarlarını yapılandırma

`TelemetryProcessorChainBuilder`Örnekleme davranışını özelleştirmek için aşağıda gösterildiği gibi uzantı yöntemlerini kullanın.

> [!IMPORTANT]
> Örnekleme yapılandırmak için bu yöntemi kullanırsanız, lütfen `aiOptions.EnableAdaptiveSampling` çağrılırken özelliğini olarak ayarladığınızdan emin olun `false` `AddApplicationInsightsTelemetry()` . Bu değişikliği yaptıktan sonra, özelleştirmelerinizi yerinde, uyarlamalı örneklemeyi yeniden etkinleştirmek için aşağıdaki kod bloğundaki yönergeleri **tam olarak** izlemeniz gerekir. Bunun yapılmaması, aşırı veri alımı oluşmasına neden olabilir. Her zaman değişen örnekleme ayarlarını test edin ve maliyetlerinizi denetlemeye yardımcı olması için uygun [günlük veri ucunu](pricing.md#set-the-daily-cap) ayarlayın.

```csharp
using Microsoft.ApplicationInsights.Extensibility

public void Configure(IApplicationBuilder app, IHostingEnvironment env, TelemetryConfiguration configuration)
{
    var builder = configuration.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
    // For older versions of the Application Insights SDK, use the following line instead:
    // var builder = configuration.TelemetryProcessorChainBuilder;

    // Using adaptive sampling
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5);

    // Alternately, the following configures adaptive sampling with 5 items per second, and also excludes DependencyTelemetry from being subject to sampling.
    // builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5, excludedTypes: "Dependency");

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));
    
    builder.Build();

    // ...
}
```

### <a name="configuring-adaptive-sampling-for-azure-functions"></a>Azure Işlevleri için uyarlamalı örnekleme yapılandırma

Azure Işlevleri 'nde çalışan uygulamalar için uyarlamalı örnekleme yapılandırmak üzere [Bu sayfadaki](../../azure-functions/configure-monitoring.md#configure-sampling) yönergeleri izleyin.

## <a name="fixed-rate-sampling"></a>Sabit fiyat örnekleme

Sabit fiyat örnekleme, Web sunucunuz ve Web tarayıcılardan gönderilen trafiği azaltır. Uyarlamalı örnekleme aksine, sizin için karar verdiğiniz sabit bir hızda Telemetriyi azaltır. ASP.NET, ASP.NET Core, Java ve Python uygulamaları için sabit fiyat örneklemesi kullanılabilir.

Diğer örnekleme teknikleri gibi, bu da ilgili öğeleri de korur. Ayrıca, ilgili öğelerin korunması için istemci ve sunucu örneklemesi eşitlenir. Örneğin, aramada bir sayfa görünümüne baktığınızda ilgili sunucu isteklerini bulabilirsiniz. 

Ölçüm Gezgini, istek ve özel durum sayıları gibi ücretler örnekleme hızının dengelenerek yaklaşık doğru olmaları için bir faktörle çarpılır.

### <a name="configuring-fixed-rate-sampling-for-aspnet-applications"></a>ASP.NET uygulamaları için sabit hız örneklemesi yapılandırma

1. **Uyarlamalı örneklemeyi devre dışı bırak**: içinde [`ApplicationInsights.config`](./configuration-with-applicationinsights-config.md) , düğümü kaldırın veya not edin `AdaptiveSamplingTelemetryProcessor` .

    ```xml
    <TelemetryProcessors>
        <!-- Disabled adaptive sampling:
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
        </Add>
        -->
    ```

2. **Sabit fiyat örnekleme modülünü etkinleştirin.** Bu kod parçacığını şu şekilde ekleyin [`ApplicationInsights.config`](./configuration-with-applicationinsights-config.md) :
   
    ```XML
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <!-- Set a percentage close to 100/N where N is an integer. -->
            <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
            <SamplingPercentage>10</SamplingPercentage>
        </Add>
    </TelemetryProcessors>
    ```

      Alternatif olarak, dosyadaki örnekleme parametresini ayarlamak yerine `ApplicationInsights.config` , programlı olarak bu değerleri ayarlayabilirsiniz:

    ```csharp
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

    // ...

    var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
    // For older versions of the Application Insights SDK, use the following line instead:
    // var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;

    builder.UseSampling(10.0); // percentage

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();
    ```

    ([Telemetri işlemcileri hakkında bilgi edinin](./api-filtering-sampling.md#filtering).)

### <a name="configuring-fixed-rate-sampling-for-aspnet-core-applications"></a>ASP.NET Core uygulamalar için sabit fiyat örneklemesi yapılandırma

1. **Uyarlamalı örneklemeyi devre dışı bırak**: şu `ConfigureServices` kullanılarak, yöntemde değişiklik yapılabilir `ApplicationInsightsServiceOptions` :

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        // ...

        var aiOptions = new Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions();
        aiOptions.EnableAdaptiveSampling = false;
        services.AddApplicationInsightsTelemetry(aiOptions);

        //...
    }
    ```

2. **Sabit fiyat örnekleme modülünü etkinleştirin.** `Configure`Aşağıdaki kod parçacığında gösterildiği gibi yönteminde değişiklik yapılabilir:

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        var configuration = app.ApplicationServices.GetService<TelemetryConfiguration>();

        var builder = configuration.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
        // For older versions of the Application Insights SDK, use the following line instead:
        // var builder = configuration.TelemetryProcessorChainBuilder;

        // Using fixed rate sampling
        double fixedSamplingPercentage = 10;
        builder.UseSampling(fixedSamplingPercentage);

        builder.Build();

        // ...
    }
    ```

### <a name="configuring-fixed-rate-sampling-for-java-applications"></a>Java uygulamaları için sabit hız örneklemesi yapılandırma

Varsayılan olarak, Java Aracısı ve SDK 'da hiçbir örnekleme etkinleştirilmez. Şu anda yalnızca sabit fiyat örneklemesi destekleniyor. Uyarlamalı örnekleme, Java 'da desteklenmez.

#### <a name="configuring-java-agent"></a>Java aracısını yapılandırma

1. [Applicationinsights-Agent-3.0.0-PREVIEW. 5. jar](https://github.com/microsoft/ApplicationInsights-Java/releases/download/3.0.0-PREVIEW.5/applicationinsights-agent-3.0.0-PREVIEW.5.jar) dosyasını indirin

1. Örneklemeyi etkinleştirmek için dosyanıza aşağıdakini ekleyin `applicationinsights.json` :

```json
{
  "sampling": {
    "percentage": 10 //this is just an example that shows you how to enable only 10% of transaction 
  }
}
```

#### <a name="configuring-java-sdk"></a>Java SDK 'sını yapılandırma

1. Web uygulamanızı en son [Application Insights Java SDK 'sı](./java-get-started.md)ile indirin ve yapılandırın.

2. Aşağıdaki kod parçacığını dosyaya ekleyerek **sabit fiyat örnekleme modülünü etkinleştirin** `ApplicationInsights.xml` :

    ```XML
    <TelemetryProcessors>
        <BuiltInProcessors>
            <Processor type="FixedRateSamplingTelemetryProcessor">
                <!-- Set a percentage close to 100/N where N is an integer. -->
                <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
                <Add name="SamplingPercentage" value="50" />
            </Processor>
        </BuiltInProcessors>
    </TelemetryProcessors>
    ```

3. Etiketin içindeki aşağıdaki etiketleri kullanarak örnekleme türünden belirli telemetri türlerini dahil edebilir veya dışlayabilirsiniz `Processor` `FixedRateSamplingTelemetryProcessor` :
   
    ```XML
    <ExcludedTypes>
        <ExcludedType>Request</ExcludedType>
    </ExcludedTypes>

    <IncludedTypes>
        <IncludedType>Exception</IncludedType>
    </IncludedTypes>
    ```

Örneklemeye dahil edilebilir ya da hariç tutulacak telemetri türleri şunlardır: `Dependency` , `Event` ,, `Exception` `PageView` , `Request` ve `Trace` .

> [!NOTE]
> Örnekleme yüzdesi için, N 'nin bir tamsayı olduğu, 100/N 'e yakın bir yüzde seçin.  Şu anda örnekleme diğer değerleri desteklemiyor.

### <a name="configuring-fixed-rate-sampling-for-opencensus-python-applications"></a>OpenCensus Python uygulamaları için sabit fiyat örneklemesi yapılandırma

Uygulamanızı en son [Opencensus Azure izleyici dışarı layıcılarını](./opencensus-python.md)kullanarak işaretleyin.

> [!NOTE]
> Ölçüm Dışarı Aktarıcı için sabit fiyat örnekleme kullanılamaz. Bu, örneklemenin yapılandırılamadığını tek tür telemetri olan özel ölçümleri gösterir. Ölçüm dışarı aktarıcı, izlediği tüm Telemetriyi gönderir.

#### <a name="fixed-rate-sampling-for-tracing"></a>İzleme için sabit fiyat örnekleme ####
`Tracer` yapılandırmanız kapsamında bir `sampler` belirtmelisiniz. Açık bir örnekleyiciyi sağlanmazsa, `ProbabilitySampler` Varsayılan olarak kullanılacaktır. , `ProbabilitySampler` Varsayılan olarak 1/10000 oranını kullanır, yani her 10000 isteğin bir tanesi Application Insights gönderilir. Örnekleme oranı belirtmek isterseniz aşağıya bakın.

Örnekleme hızını belirtmek için, `Tracer` 0,0 ve 1,0 dahil olmak üzere örnekleme oranına sahip bir örnekleyiciyi belirttiğinizden emin olun. 1,0 örnekleme oranı %100 ' i temsil eder, ancak tüm istekleriniz Application Insights telemetri olarak gönderilir.

```python
tracer = Tracer(
    exporter=AzureExporter(
        instrumentation_key='00000000-0000-0000-0000-000000000000',
    ),
    sampler=ProbabilitySampler(1.0),
)
```

#### <a name="fixed-rate-sampling-for-logs"></a>Günlükler için sabit fiyat örnekleme ####
`AzureLogHandler`İsteğe bağlı bağımsız değişkeni değiştirerek, için sabit fiyat örneklemesi yapılandırabilirsiniz `logging_sampling_rate` . Herhangi bir bağımsız değişken sağlanmazsa, 1,0 örnekleme ücreti kullanılacaktır. 1,0 örnekleme oranı %100 ' i temsil eder, ancak tüm istekleriniz Application Insights telemetri olarak gönderilir.

```python
handler = AzureLogHandler(
    instrumentation_key='00000000-0000-0000-0000-000000000000',
    logging_sampling_rate=0.5,
)
```

### <a name="configuring-fixed-rate-sampling-for-web-pages-with-javascript"></a>JavaScript ile Web sayfaları için sabit hızlı örnekleme yapılandırma

JavaScript tabanlı Web sayfaları, Application Insights kullanacak şekilde yapılandırılabilir. Telemetri, kullanıcının tarayıcısı içinde çalışan istemci uygulamasından gönderilir ve sayfalar herhangi bir sunucudan barındırılabilir.

[JavaScript tabanlı Web sayfalarınızı Application Insights için yapılandırdığınızda](javascript.md), Application Insights portalından aldığınız JavaScript kod parçacığını değiştirin.

> [!TIP]
> JavaScript içeren ASP.NET uygulamalarında, kod parçacığı genellikle ' a gider `_Layout.cshtml` .

`samplingPercentage: 10,`İzleme anahtarından önce bir satır ekleyin:

```xml
<script>
    var appInsights = // ... 
    ({ 
      // Value must be 100/N where N is an integer.
      // Valid examples: 50, 25, 20, 10, 5, 1, 0.1, ...
      samplingPercentage: 10, 

      instrumentationKey: ...
    }); 

    window.appInsights = appInsights; 
    appInsights.trackPageView(); 
</script>
```

Örnekleme yüzdesi için, N 'nin bir tamsayı olduğu, 100/N 'e yakın bir yüzde seçin. Şu anda örnekleme diğer değerleri desteklemiyor.

#### <a name="coordinating-server-side-and-client-side-sampling"></a>Sunucu tarafı ve istemci tarafı örneklemesi koordine etme

İstemci tarafı JavaScript SDK 'Sı, sunucu tarafı SDK 'Sı ile birlikte sabit hızlı örneklemeye katılır. Araçlı sayfalar yalnızca, sunucu tarafı SDK 'sının örneklemeye dahil etme kararlarını yaptığı aynı kullanıcıdan gelen istemci tarafı telemetri gönderir. Bu mantık, istemci ve sunucu tarafı uygulamalardaki kullanıcı oturumlarının bütünlüğünü sürdürmek için tasarlanmıştır. Sonuç olarak, Application Insights belirli bir telemetri öğesinden bu kullanıcı veya oturum için diğer tüm telemetri öğelerini bulabilir ve arama bölümünde ilgili sayfa görünümleri ve istekleri arasında gezinebilirsiniz.

İstemciniz ve sunucu tarafı telemetri, Eşgüdümlü örnekleri göstermiyor:

* Hem sunucu hem de istemcide örnekleme 'yi etkinleştirdiğinizi doğrulayın.
* Hem istemcide hem de sunucuda aynı örnekleme yüzdesini ayarlamış olun.
* SDK sürümünün 2,0 veya üzeri olduğundan emin olun.

## <a name="ingestion-sampling"></a>Giriş örnekleme

Alma örnekleme, Web sunucunuz, tarayıcılarınız ve cihazlarınızdaki telemetri Application Insights hizmeti uç noktasına ulaştığında çalışır. Uygulamanızdan gönderilen telemetri trafiğini düşürmese de, Application Insights göre işlenen ve tutulan miktarı azaltır (ve için ücretlendirilir).

Uygulamanız genellikle aylık kotasına geçtiğinde bu örnekleme türünü kullanın ve SDK tabanlı örnekleme türlerinden birini kullanma seçeneğiniz yoktur. 

Örnekleme hızını kullanım ve tahmini maliyetler sayfasında ayarlayın:

![Uygulamanın genel bakış dikey penceresinde ayarlar, kota, örnekler ' e tıklayın, ardından bir örnekleme hızı seçin ve Güncelleştir ' e tıklayın.](./media/sampling/data-sampling.png)

Diğer örnekleme türleri gibi, algoritma ilgili telemetri öğelerini korur. Örneğin, aramada Telemetriyi incelerken belirli bir özel durumla ilgili isteği bulabilirsiniz. İstek hızı ve özel durum oranı gibi ölçüm sayımları doğru tutulur.

Örnekleme tarafından atılan veri noktaları, [sürekli dışarı aktarma](./export-telemetry.md)gibi Application Insights hiçbir özellik için kullanılamaz.

Uyarlamalı veya sabit oran örnekleme işlemi sırasında giriş örnekleme işlemi çalışmıyor. ASP.NET SDK veya ASP.NET Core SDK kullanılırken veya [Azure App Service ](azure-web-apps.md) ya da Durum İzleyicisi kullanılarak Application Insights etkinleştirildiğinde Uyarlamalı örnekleme varsayılan olarak etkindir. Telemetri Application Insights hizmet uç noktası tarafından alındığında, telemetri incelenir ve örnekleme hızının %100 ' den (Telemetriyi örneklendirmekte olduğunu gösterir), ayarladığınız Alım örnekleme hızının yok sayılır.

> [!WARNING]
> Portal kutucuğunda gösterilen değer, alma örneklemesi için ayarladığınız değeri gösterir. Herhangi bir SDK örnekleme (Uyarlamalı veya sabit oran örnekleme) işleminde varsa, gerçek örnekleme hızını temsil etmez.

## <a name="when-to-use-sampling&quot;></a>Örnekleme ne zaman kullanılır?

Genel olarak, çoğu küçük ve orta büyüklükte uygulamalar için örnekleme gerekmez. En yararlı tanılama bilgileri ve en doğru istatistikler, tüm Kullanıcı etkinliklerinizde veri toplanarak elde edilir. 

Örnekleme 'nın başlıca avantajları şunlardır:

* Uygulamanız kısa bir süre içinde çok yüksek düzeyde telemetri gönderdiğinde, hizmet bırakmaları (&quot;kısıtlar") veri noktalarını Application Insights. Örnekleme, uygulamanızın azaltma işleminin oluşma olasılığını azaltır.
* Fiyatlandırma katmanınız için veri noktalarının [kotası](pricing.md) içinde tutulması. 
* Telemetri koleksiyonundan ağ trafiğini azaltmak için. 

### <a name="which-type-of-sampling-should-i-use"></a>Ne tür örnekleme kullanmalıyım?

**Şu durumlarda giriş örnekleme kullan:**

* Genellikle aylık telemetri kotayı kullanırsınız.
* Kullanıcılarınızın web tarayıcılarınızdan çok fazla telemetri elde edersiniz.
* SDK 'nın örnekleme desteği olmayan bir sürümünü kullanıyorsunuz. Örneğin, 2 ' den önceki ASP.NET sürümleri.

**Şu durumlarda sabit fiyat örnekleme kullan:**

* İstemci ve sunucu arasında eşitlenmiş örnekleme istiyorsunuz; böylece, [aramada](./diagnostic-search.md)olayları araştırdığınızda, istemci ve sunucu üzerinde, sayfa GÖRÜNÜMLERI ve http istekleri gibi ilgili olaylar arasında gezinebilirsiniz.
* Uygulamanız için uygun örnekleme yüzdesinden emin olursunuz. Doğru ölçümleri almak için yeterince yüksek olmalıdır, ancak fiyatlandırma kotanızı ve azaltma limitlerini aşan oranın altında.

**Uyarlamalı örnekleme kullan:**

Diğer örnekleme formlarını kullanma koşulları geçerli değilse, uyarlamalı örnekleme önerilir. Bu ayar, ASP.NET/ASP.NET Core SDK 'sında varsayılan olarak etkinleştirilmiştir. Belirli bir minimum hıza ulaşılana kadar trafiği azaltmaz, bu nedenle düşük-kullanılan siteler büyük olasılıkla hiç örneklenemez.

## <a name="knowing-whether-sampling-is-in-operation"></a>Örneklemenin çalışıp çalışmadığını öğrenme

Gerçek örnekleme oranını nerede uygulandığını fark etmeksizin öğrenmek için, şöyle bir [analiz sorgusu](../logs/log-query-overview.md) kullanın:

```kusto
union requests,dependencies,pageViews,browserTimings,exceptions,traces
| where timestamp > ago(1d)
| summarize RetainedPercentage = 100/avg(itemCount) by bin(timestamp, 1h), itemType
```

`RetainedPercentage`Herhangi bir tür için 100 ' den küçük olduğunu görürseniz, bu telemetri türü örneklenir.

> [!IMPORTANT]
> Application Insights, örnekleme tekniklerinden herhangi birinde örnek oturum, ölçümler (özel ölçümler dahil) veya performans sayacı telemetri türleri değildir. Kesinlik azalmasıyla ilgili bir azaltma, bu telemetri türleri için son derece istenmeyen bir şekilde istenirken bu türler her zaman örnekleme dışında tutulur.

## <a name="how-sampling-works"></a>Örnekleme nasıl kullanılır?

Örnekleme algoritması hangi telemetri öğelerinin atılanacağına ve hangilerinin tutulacağını belirler. Bu, örnekleme 'nın SDK veya Application Insights hizmetinde gerçekleştirilip yapılmayacağı için geçerlidir. Örnekleme kararı, tüm birbiriyle ilişkili tüm veri noktalarını korumayı hedefleyen çeşitli kurallara dayanır. Bu, daha az bir veri kümesiyle aynı şekilde işlem yapılabilir ve güvenilir Application Insights bir tanılama deneyimi sağlar. Örneğin, uygulamanızda bir örneğe eklenen başarısız bir istek varsa, ek telemetri öğeleri (Bu istek için günlüğe kaydedilen özel durumlar ve izlemeler gibi) korunur. Örnekleme, bunların tümünü birlikte tutar veya bırakır. Sonuç olarak, Application Insights istek ayrıntılarına baktığınızda, isteği ilişkili telemetri öğeleriyle birlikte her zaman görebilirsiniz.

Örnekleme kararı, isteğin işlem KIMLIĞINE dayalıdır, bu da belirli bir işleme ait tüm telemetri öğelerinin korunduğu veya bıraktığı anlamına gelir. Bir işlem KIMLIĞI kümesi olmayan telemetri öğeleri için (HTTP bağlamı olmayan zaman uyumsuz iş parçacıklarından raporlanan telemetri öğeleri) örnekleme, her türün telemetri öğelerinin yüzdesini yakalar.

Telemetriyi size geri sunarken, Application Insights hizmeti, eksik veri noktalarının dengelemesini sağlamak için koleksiyon sırasında kullanılan aynı örnekleme yüzdesine göre ölçümleri ayarlar. Bu nedenle, Application Insights Telemetriyi ararken, kullanıcılar gerçek sayılara çok yakın olan istatistiksel olarak doğru yaklaşık olarak görülür.

Yaklaşık büyük ölçüde doğruluk, yapılandırılan örnekleme yüzdesine bağlıdır. Ayrıca, çok sayıda kullanıcıdan oluşan, genellikle benzer isteklerin büyük bir hacmini işleyen uygulamalar için doğruluk artar. Öte yandan, önemli bir yük ile çalışmayan uygulamalar için, bu uygulamalar genellikle veri kaybını azaltmadan kota içinde kalarak tüm telemetrilerini gönderebilmeleri için örnekleme gerekmez. 

## <a name="frequently-asked-questions"></a>Sık sorulan sorular

*ASP.NET ve ASP.NET Core SDK 'larda varsayılan örnekleme davranışı nedir?*

* Yukarıdaki SDK 'nın en son sürümlerinden birini kullanıyorsanız, uyarlamalı örnekleme varsayılan olarak saniyede beş telemetri öğesi ile etkinleştirilir.
  `AdaptiveSamplingTelemetryProcessor`Varsayılan olarak iki düğüm eklenmiştir ve biri `Event` örneklemede türü içerir, diğeri ise `Event` türü örneklemeye dahil eder. Bu yapılandırma, SDK 'nın telemetri öğelerini türlerin beş telemetri öğesiyle sınırlandırmaya çalışacağı anlamına gelir `Event` ve diğer tüm türlerin beş telemetri öğesi birleştirilir ve bu sayede `Events` diğer telemetri türlerinden ayrı olarak örneklenir. Olaylar genellikle iş telemetri için kullanılır ve büyük olasılıkla tanılama telemetri birimlerinden etkilenmemelidir.
  
  Aşağıda oluşturulan varsayılan dosya gösterilmektedir `ApplicationInsights.config` . ASP.NET Core, kodda aynı varsayılan davranış etkinleştirilmiştir. Bu varsayılan davranışı değiştirmek için [bu sayfanın önceki bölümündeki örnekleri](#configuring-adaptive-sampling-for-aspnet-core-applications) kullanın.

    ```xml
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
            <ExcludedTypes>Event</ExcludedTypes>
        </Add>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
            <IncludedTypes>Event</IncludedTypes>
        </Add>
    </TelemetryProcessors>
    ```

*Telemetri birden çok kez örneklenebilir mi?*

* Hayır. SamplingTelemetryProcessors, öğe zaten örneklenirken örnekleme hususlarından öğeleri yoksayar. Aynı değer, aynı zamanda SDK 'nın kendisinde zaten örneklendiği öğelere örnekleme uygulamayatıon örnekleme için de geçerlidir.

*Neden basit bir "her telemetri türünün X 'i toplayın" örneklenmiyor?*

* Bu örnekleme yaklaşımının ölçüm yaklaşımında yüksek düzeyde bir duyarlık sağlaması sayesinde, Tanılama için kritik olan Kullanıcı, oturum ve istek başına tanılama verilerini ilişkilendirme özelliğini keser. Bu nedenle örnekleme, "Uygulama kullanıcılarının X için tüm telemetri öğelerini topla" veya "uygulama isteklerinin X için tüm telemetrisi topla" gibi ilkelerle daha iyi bir şekilde çalışmaktadır. İsteklerle ilişkili olmayan telemetri öğeleri (örneğin, arka planda zaman uyumsuz işlem) için, geri dönüş "her telemetri türü için tüm öğelerin yüzde X 'ini toplayın". 

*Örnekleme yüzdesi zaman içinde değişsin mi?*

* Evet, uyarlamalı örnekleme, telemetri yüzdesinin, geçerli olarak gözlemlenen hacme hacmine göre yavaş şekilde değiştirir.

*Sabit fiyat örneklemesi kullandığımda, hangi örnekleme yüzdesinin uygulamam için en iyi şekilde çalıştığını nasıl anlarım?*

* Tek bir yol, uyarlamalı örnekleme ile başlamak, hangi hızların bulunduğunu öğrenmek (Yukarıdaki soruya bakın) ve ardından bu oranı kullanarak sabit hızlı örneklemeye geçiş yapmanızı sağlar. 
  
    Aksi takdirde, tahmin etmeniz gerekir. Application Insights ' de geçerli telemetri kullanımınızı çözümleyin, oluşan herhangi bir daraltma gözlemleyin ve toplanan telemetri hacmini tahmin edin. Seçtiğiniz fiyatlandırma katmanınız ile birlikte bu üç giriş, toplanan Telemetriyi ne kadar azaltmak istediğinizi tavsiye edebilir. Ancak, kullanıcılarınızın sayısında veya telemetri hacminde başka bir vardiya artışı tahmininizi geçersiz kılabilir.

*Örnekleme yüzdesini çok düşük olacak şekilde yapılandırdığımda ne olur?*

* Aşırı düşük örnekleme yüzdeleri, agresif örneklemenin oluşmasına neden olur ve Application Insights veri hacmi azalmasıyla ilgili verilerin görselleştirmesini dengelemek istediğinde yaklaşık olarak en yakın doğruluk düzeyini azaltır. Ayrıca, sık sık başarısız olan veya yavaş isteklerin örneklendiği için tanılama deneyiminiz olumsuz etkilenebilir.

*Örnekleme yüzdesini çok yüksek olacak şekilde yapılandırdığımda ne olur?*

* Çok yüksek bir örnekleme yüzdesini (yeterince ısrarlı değil) yapılandırmak, toplanan Telemetriyi birimde yetersiz azalmayla sonuçlanır. Azaltma ile ilgili telemetri veri kaybını yaşamaya devam edebilir ve Application Insights kullanım maliyeti, fazla kullanım ücretleri nedeniyle planlanmasından daha yüksek olabilir.

*Hangi platformlarda örnekleme kullanabilirim?*

* SDK örnekleme gerçekleştirmediğinden, belirli bir birimin üzerindeki herhangi bir telemetri için alma örneklemesi otomatik olarak gerçekleşebilir. Bu yapılandırma, örneğin, ASP.NET SDK veya Java SDK 'sının eski bir sürümünü kullanıyorsanız çalışır.
* Geçerli ASP.NET veya ASP.NET Core SDK 'larını kullanıyorsanız (Azure 'da veya kendi sunucunuzda barındırılan), varsayılan olarak Uyarlamalı örnekleme alırsınız, ancak yukarıda açıklanan şekilde sabit ücret ile geçiş yapabilirsiniz. Sabit hızlı örnekleme ile, tarayıcı SDK 'Sı, örnek ilgili olaylarla otomatik olarak eşitlenir. 
* Geçerli Java aracısını kullanıyorsanız, `applicationinsights.json` `ApplicationInsights.xml` sabit fiyat örneklemeyi açmak Için (Java SDK 'sı için, yapılandırma) yapılandırabilirsiniz. Örnekleme varsayılan olarak kapalıdır. Sabit fiyat örneklemeyle, tarayıcı SDK 'Sı ve sunucu, örnek ilgili olaylarla otomatik olarak eşitlenir.

*Her zaman görmek istediğim bazı nadir olaylar vardır. Örnekleme modülünü nasıl geçmiş bir şekilde alabilirim?*

* Bunu gerçekleştirmenin en iyi yolu, [](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer) `SamplingPercentage` aşağıda gösterildiği gibi, korunmayı istediğiniz telemetri öğesinde 100 olarak ayarlayan özel bir telemetryınitializer yazmaktır. Başlatıcılar telemetri işlemcilerinin (örnekleme dahil) önce çalıştırılmasının garanti edilir. Bu, tüm örnekleme tekniklerinin bu öğeyi herhangi bir örnekleme ile yoksaymasını sağlar. Özel telemetri başlatıcıları, ASP.NET SDK, ASP.NET Core SDK, JavaScript SDK ve Java SDK ' da kullanılabilir. Örneğin, ASP.NET SDK kullanarak bir telemetri başlatıcısı yapılandırabilirsiniz:

    ```csharp
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            if(somecondition)
            {
                ((ISupportSampling)telemetry).SamplingPercentage = 100;
            }
        }
    }
    ```

## <a name="older-sdk-versions"></a>Eski SDK sürümleri

Uyarlamalı örnekleme, ASP.NET v 2.0.0-Beta3 ve üzeri, Microsoft. ApplicationInsights. AspNetCore SDK v 2.2.0-Beta1 ve üzeri için Application Insights SDK 'Sı için kullanılabilir ve varsayılan olarak etkindir.

Sabit fiyat örnekleme, 2.0.0 ve Java SDK sürüm 2.0.1 ve sonraki sürümlerin ASP.NET sürümlerindeki SDK 'nin bir özelliğidir.

ASP.NET SDK 'nın v 2.5.0-Beta2 ve ASP.NET Core SDK 'nın v 2.2.0-Beta3 öncesinde, örnekleme kararı "Kullanıcı" (diğer bir deyişle, en tipik Web uygulamaları) tanımlayan uygulamalar için Kullanıcı KIMLIĞININ karmasını temel alır. Kullanıcıları tanımlamayan uygulama türleri (örneğin, Web Hizmetleri) için örnekleme kararı isteğin işlem KIMLIĞINE dayalıdır. ASP.NET ve ASP.NET Core SDK 'larının son sürümleri, örnekleme kararının işlem KIMLIğINI kullanır.

## <a name="next-steps"></a>Sonraki adımlar

* [Filtreleme](./api-filtering-sampling.md) , SDK 'nizin ne kadar sıkı bir şekilde denetim sağlayabilir.
* Geliştirici ağı makalesini okuyun [Application Insights Ile Telemetriyi iyileştirin](/archive/msdn-magazine/2017/may/devops-optimize-telemetry-with-application-insights).

