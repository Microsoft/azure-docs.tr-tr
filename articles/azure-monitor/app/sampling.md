---
title: Azure Application Insights telemetri örnekleme | Microsoft Docs
description: Denetim altında telemetri hacmini tutma.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: cijothomas
ms.author: cithomas
ms.date: 03/14/2019
ms.reviewer: vitalyg
ms.openlocfilehash: c124e6c433f83212c0db815a2fd06cfcfdf86253
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73884721"
---
# <a name="sampling-in-application-insights"></a>Application Insights’ta örnekleme

Örnekleme, [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md)'deki bir özelliktir. Uygulama verilerinin istatistiksel olarak doğru analizini korurken telemetri trafiğini ve depolamayı azaltmak için önerilen yoldur. Filtre, ilgili öğeleri seçer. bu sayede, tanılama araştırmalar yaparken öğeler arasında gezinebilirsiniz.
Portalda ölçüm sayıları sunulduklarında, bunlar hesap örneklemesi içine alınır. Bunun yapılması, istatistiklerdeki etkileri en aza indirir.

Örnekleme, trafiği ve veri maliyetlerini azaltır ve azaltmaktan kaçınmanıza yardımcı olur.

## <a name="in-brief"></a>Kısaca:

* Örnekleme, *n* kayıt halinde 1 tutar ve geri kalanı atar. Örneğin, beş olaydan oluşan ve örnekleme oranı %20 olan bir örnek olabilir. 
* Uyarlamalı örnekleme, ASP.NET ve ASP.NET Core yazılım geliştirme setlerinin (SDK) tüm en son sürümlerinde varsayılan olarak etkindir.
* Ayrıca, örneklemesi el ile de ayarlayabilirsiniz. Bu, Portal 'da *kullanım ve tahmini maliyetler sayfasında*, ASP.NET SDK 'Daki ApplicationInsights. config dosyasında, kod aracılığıyla ASP.NET Core SDK 'Daki ve ApplicationInsights. xml dosyasındaki Java SDK 'sında yapılandırılabilir.
* Özel olayları günlüğe kaydeder ve bir olay kümesinin birlikte tutulup tutulmayacağından emin olmanız gerekiyorsa, olayların aynı operationId değerine sahip olması gerekir.
* "İstek sayısı" veya "olay sayısı" kolay adı altında görüntülenen, özellik `itemCount`her bir kayıtta örnekleme *böleçini* rapor edilir. örnekleme işlem içinde olmadığında `itemCount==1`.
* Analiz sorguları yazarsanız, [örnekleme hesabı](../../azure-monitor/log-query/aggregations.md)almalısınız. Yalnızca kayıtları saymak yerine `summarize sum(itemCount)`kullanmanız gerekir.

## <a name="types-of-sampling"></a>Örnekleme türleri

Üç alternatif örnekleme yöntemi vardır:

* **Uyarlamalı örnekleme** , ASP.NET/ASP.NET Core uygulamanızda SDK 'dan gönderilen telemetri hacmini otomatik olarak ayarlar. Bu, ASP.NET Web SDK v 2.0.0-Beta3 onmalları ve Microsoft. ApplicationInsights. AspNetCore SDK v 2.2.0-Beta1 onmallarından varsayılan örnekleme olur.  Uyarlamalı örnekleme Şu anda yalnızca ASP.NET sunucu tarafı telemetri için kullanılabilir.

* **Sabit fiyat örnekleme** , hem ASP.net, hem de ASP.NET Core ya da Java sunucunuzun ve kullanıcılarınızın tarayıcılarınızdan gönderilen telemetri hacmini azaltır. Oranı ayarlarsınız. İstemci ve sunucu, arama sırasında ilgili sayfa görünümleri ve istekleri arasında gezinebilmeniz için örneklemesini eşitler.

* Giriş **örnekleme** Azure portal işe yarar. Sizin ayarladığınız örnekleme ücretine göre uygulamanızdan gelen telemetrinin bir kısmını atar. Uygulamanızdan gönderilen telemetri trafiğini azaltmaz, ancak aylık kotasında tutmanıza yardımcı olur. Alım örnekleme 'nın başlıca avantajı, örnekleme hızını uygulamanızı yeniden dağıtmaya gerek kalmadan ayarlayabilmektir. Alım örneklemesi tüm sunucular ve istemciler için tek bir şekilde çalışmaktadır.

Uyarlamalı veya sabit oran örnekleme işlemi kullanılıyorsa, alma örnekleme devre dışı bırakılır.


## <a name="adaptive-sampling-in-your-aspnetaspnet-core-web-applications"></a>ASP.NET/ASP.NET Core Web uygulamalarınızda Uyarlamalı örnekleme

Uyarlamalı örnekleme, ASP.NET v 2.0.0-Beta3 ve üzeri, Microsoft. ApplicationInsights. AspNetCore SDK v 2.2.0-Beta1 ve üzeri için Application Insights SDK 'Sı için kullanılabilir ve varsayılan olarak etkindir.

Uyarlamalı örnekleme, Web sunucusu uygulamanızdan Application Insights hizmeti uç noktasına gönderilen telemetri hacmini etkiler. Birim, belirtilen maksimum trafik oranı içinde tutulacak şekilde otomatik olarak ayarlanır ve `MaxTelemetryItemsPerSecond`ayarı aracılığıyla denetlenir. Uygulama, hata ayıklama sırasında veya düşük kullanım nedeniyle düşük miktarda telemetri üretirse, birim `MaxTelemetryItemsPerSecond`altında olduğu sürece, öğeler örnekleme işlemcisi tarafından atılamaz. Telemetri hacmi arttıkça örnekleme oranı, hedef birime ulaşmak üzere ayarlanır.

Hedef birime ulaşmak için, oluşturulan telemetrinin bazıları atılır. Ancak, diğer örnekleme türleri gibi algoritma ilgili telemetri öğelerini korur. Örneğin, aramada Telemetriyi incelerken belirli bir özel durumla ilgili isteği bulabilirsiniz.

İstek hızı ve özel durum oranı gibi ölçüm sayıları örnekleme hızının dengelemeye göre ayarlanır, böylece Ölçüm Gezgini 'nde yaklaşık doğru değerler gösterilir.

## <a name="configuring-adaptive-sampling-for-aspnet-applications"></a>ASP.NET uygulamaları için uyarlamalı örnekleme yapılandırma

ASP.NET Core uygulamalar için uyarlamalı örnekleme yapılandırma hakkında [bilgi edinin](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications) . 

[ApplicationInsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md)dosyasında `AdaptiveSamplingTelemetryProcessor` düğümündeki çeşitli parametreleri ayarlayabilirsiniz. Gösterilen rakamlar varsayılan değerlerdir:

* `<MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>`
  
    Uyarlamalı algoritmanın **her bir sunucu konağı üzerinde**için kullandığı hedef hız. Web uygulamanız birçok ana bilgisayarda çalışıyorsa, bu değeri, Application Insights portalındaki hedef trafik ücretine devam etmek için küçültün.
* `<EvaluationInterval>00:00:15</EvaluationInterval>` 
  
    Geçerli telemetri hızının yeniden değerlendirilme aralığı. Değerlendirme, hareketli ortalama olarak gerçekleştirilir. Telemetriyi ani bursts 'e tabi olursa bu aralığı kısaltmak isteyebilirsiniz.
* `<SamplingPercentageDecreaseTimeout>00:02:00</SamplingPercentageDecreaseTimeout>`
  
    Örnekleme yüzdesi değeri değiştiğinde, daha kısa bir süre sonra örnekleme yüzdesini daha az veri yakalamaya izin vereceğiz.
* `<SamplingPercentageIncreaseTimeout>00:15:00</SamplingPercentageIncreaseTimeout>`
  
    Örnekleme yüzdesi değeri değiştiğinde, daha fazla veri yakalamak için örnekleme yüzdesini yeniden arttırmaya izin veririz.
* `<MinSamplingPercentage>0.1</MinSamplingPercentage>`
  
    Örnekleme yüzdesi farklılık gösterdiği gibi, ayarlamaya izin verilen en düşük değer nedir?
* `<MaxSamplingPercentage>100.0</MaxSamplingPercentage>`
  
    Örnekleme yüzdesi farklılık gösterdiğinden, ayarlamaya izin verilen en büyük değer nedir?
* `<MovingAverageRatio>0.25</MovingAverageRatio>` 
  
    Hareketli ortalama hesaplamasında, en son değere atanan ağırlık. 1 ' den küçük veya buna eşit bir değer kullanın. Daha küçük değerler, algoritmayı ani değişikliklere karşı daha az reaktif hale getirir.
* `<InitialSamplingPercentage>100</InitialSamplingPercentage>`
  
    Uygulama yeni başlatıldığında atanan değer. Hata ayıklarken değeri küçültmeyin.

* `<ExcludedTypes>Trace;Exception</ExcludedTypes>`
  
    Örneklemeyi istemediğiniz türlerin noktalı virgülle ayrılmış listesi. Tanınan türler şunlardır: bağımlılık, olay, özel durum, PageView, Istek, Izleme. Belirtilen türlerin tüm örnekleri iletilir; Belirtilmemiş Türler örneklenir.

* `<IncludedTypes>Request;Dependency</IncludedTypes>`
  
    Örneklemeyi yapmak istediğiniz türlerin noktalı virgülle ayrılmış listesi. Tanınan türler şunlardır: bağımlılık, olay, özel durum, PageView, Istek, Izleme. Belirtilen türler örneklenir; diğer türlerin tüm örnekleri her zaman iletilir.


Uyarlamalı örnekleme **devre dışı bırakmak için** , AdaptiveSamplingTelemetryProcessor düğümlerini ApplicationInsights-config öğesinden kaldırın.

### <a name="alternative-configure-adaptive-sampling-in-code"></a>Alternatif: kodda Uyarlamalı örnekleme yapılandırma

. Config dosyasında örnekleme parametresini ayarlamak yerine, bu değerleri programlı olarak ayarlayabilirsiniz.

1. Tüm `AdaptiveSamplingTelemetryProcessor` düğümlerini. config dosyasından kaldırın.
2. Uyarlamalı örnekleme yapılandırmak için aşağıdaki kod parçacığını kullanın.

*C#*

```csharp

    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.Channel.Implementation;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    ...

    var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;
    // If you are on ApplicationInsights SDK v 2.8.0-beta2 or higher, use the following line instead
    // var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;

    // Enable AdaptiveSampling so as to keep overall telemetry volume to 5 items per second.
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5);

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

([Telemetri işlemcileri hakkında bilgi edinin](../../azure-monitor/app/api-filtering-sampling.md#filtering).)

Ayrıca, her bir telemetri türü için örnekleme hızını tek tek ayarlayabilir veya belirli türleri hatta herhangi bir örneklemeye dahil edebilirsiniz. 

*C#*

```csharp
    // The following configures adaptive sampling with 5 items per second, and also excludes DependencyTelemetry from being subject to sampling.
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5, excludedTypes: "Dependency");
```

## <a name="configuring-adaptive-sampling-for-aspnet-core-applications"></a>ASP.NET Core uygulamalar için uyarlamalı örnekleme yapılandırma.

ASP.NET Core uygulamalar için `ApplicationInsights.Config` yoktur, bu nedenle her yapılandırma kod aracılığıyla yapılır.
Uyarlamalı örnekleme, tüm ASP.NET Core uygulamalar için varsayılan olarak etkindir. Örnekleme davranışını devre dışı bırakabilir veya özelleştirebilirsiniz.

### <a name="turning-off-adaptive-sampling"></a>Uyarlamalı örnekleme kapatılıyor

Application Insights hizmeti eklenirken varsayılan örnekleme özelliği devre dışı bırakılabilir. Bu, `Startup.cs` dosyası içinde ```ApplicationInsightsServiceOptions``` kullanarak Yöntem ```ConfigureServices```.

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

Yukarıdaki kod, örnekleme özelliğini devre dışı bırakacak. Daha fazla özelleştirme seçeneği ile örnekleme eklemek için aşağıdaki adımları izleyin.

### <a name="configure-sampling-settings"></a>Örnekleme ayarlarını yapılandırma

Örnekleme davranışını özelleştirmek için aşağıda gösterildiği gibi ```TelemetryProcessorChainBuilder``` uzantı yöntemlerini kullanın.

> [!IMPORTANT]
> Örnekleme yapılandırmak için bu yöntemi kullanırsanız, lütfen aiOptions 'ı kullandığınızdan emin olun. Enableadaptiveörneklemesi = false; Addadpplicationınsi, Stelemetry () ile ayarlar.

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, TelemetryConfiguration configuration)
{
    var builder = configuration.TelemetryProcessorChainBuilder;
    // version 2.5.0-beta2 and above should use the following line instead of above. (https://github.com/Microsoft/ApplicationInsights-aspnetcore/blob/develop/CHANGELOG.md#version-250-beta2)
    // var builder = configuration.DefaultTelemetrySink.TelemetryProcessorChainBuilder;

    // Using adaptive sampling
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5);

    // Alternately, the following configures adaptive sampling with 5 items per second, and also excludes DependencyTelemetry from being subject to sampling.
    // builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5, excludedTypes: "Dependency");

    builder.Build();

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));
    // ...
}

```

**Örnekleme yapılandırmak için yukarıdaki yöntemi kullanıyorsanız, Addavpplicationınsi, Stelemetry () ile ```aiOptions.EnableAdaptiveSampling = false;``` ayarlarını kullandığınızdan emin olun.**

## <a name="fixed-rate-sampling-for-aspnet-aspnet-core-java-websites-and-python-applications"></a>ASP.NET, ASP.NET Core, Java Web siteleri ve Python uygulamaları için sabit fiyat örnekleme

Sabit fiyat örnekleme, Web sunucunuz ve Web tarayıcılardan gönderilen trafiği azaltır. Uyarlamalı örnekleme aksine, sizin için karar verdiğiniz sabit bir hızda Telemetriyi azaltır. Ayrıca, ilgili öğelerin korunması için istemci ve sunucu örneklemesi eşitlenir. Örneğin, aramada bir sayfa görünümüne baktığınızda ilgili isteği bulabilirsiniz.

Diğer örnekleme teknikleri gibi, bu da ilgili öğeleri de korur. Her HTTP isteği olayı için, istek ve ilgili olayları atılır ya da birlikte iletilir.

Ölçüm Gezgini, istek ve özel durum sayıları gibi ücretler örnekleme hızının dengelenerek yaklaşık doğru olmaları için bir faktörle çarpılır.

### <a name="configuring-fixed-rate-sampling-in-aspnet"></a>ASP.NET 'de sabit fiyat örneklemesi yapılandırma

1. **Uyarlamalı örneklemeyi devre dışı bırak**: [ApplicationInsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md)dosyasında `AdaptiveSamplingTelemetryProcessor` düğümünü kaldırın veya not alın.

    ```xml

    <TelemetryProcessors>

    <!-- Disabled adaptive sampling:
      <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
        <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
      </Add>
    -->
    ```

2. **Sabit fiyat örnekleme modülünü etkinleştirin.** Bu kod parçacığını [ApplicationInsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md)dosyasına ekleyin:
   
    ```XML
   
    <TelemetryProcessors>
     <Add  Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
   
      <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
      <SamplingPercentage>10</SamplingPercentage>
      </Add>
    </TelemetryProcessors>
   
    ```
   ### <a name="alternative-enable-fixed-rate-sampling-in-your-server-code"></a>Alternatif: sunucu kodunuzda sabit fiyat örneklemeyi etkinleştirin
    
    . Config dosyasında örnekleme parametresini ayarlamak yerine, bu değerleri programlı olarak ayarlayabilirsiniz. 

*C#*

```csharp

    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    ...

    var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;
    // If you are on ApplicationInsights SDK v 2.8.0-beta2 or higher, use the following line instead
    // var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;

    builder.UseSampling(10.0); // percentage

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```
([Telemetri işlemcileri hakkında bilgi edinin](../../azure-monitor/app/api-filtering-sampling.md#filtering).)

### <a name="configuring-fixed-rate-sampling-in-aspnet-core"></a>ASP.NET Core 'de sabit fiyat örneklemesi yapılandırma

1. **Uyarlamalı örneklemeyi devre dışı bırak**: ```ConfigureServices```, ```ApplicationInsightsServiceOptions```kullanılarak bu yöntem için değişiklikler yapılabilir:

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

2. **Sabit fiyat örnekleme modülünü etkinleştirin.** Aşağıdaki kod parçacığında gösterildiği gibi Yöntem ```Configure``` değişiklik yapılabilir:

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        var configuration = app.ApplicationServices.GetService<TelemetryConfiguration>();

        var builder = configuration.TelemetryProcessorChainBuilder;
        // version 2.5.0-beta2 and above should use the following line instead of above. (https://github.com/Microsoft/ApplicationInsights-aspnetcore/blob/develop/CHANGELOG.md#version-250-beta2)
        // var builder = configuration.DefaultTelemetrySink.TelemetryProcessorChainBuilder;

        // Using fixed rate sampling   
        double fixedSamplingPercentage = 10;
        builder.UseSampling(fixedSamplingPercentage);

        builder.Build();

        // ...
    }

    ```

### <a name="configuring-fixed-rate-sampling-in-java"></a>JAVA 'da sabit hızlı örnekleme yapılandırma ###

1. Web uygulamanızı en son [Application Insights Java SDK 'sı](../../azure-monitor/app/java-get-started.md) ile indirme ve yapılandırma

2. Aşağıdaki kod parçacığını ApplicationInsights. xml dosyasına ekleyerek **sabit fiyat örnekleme modülünü etkinleştirin** .

    ```XML
        <TelemetryProcessors>
            <BuiltInProcessors>
                <Processor type = "FixedRateSamplingTelemetryProcessor">
                    <!-- Set a percentage close to 100/N where N is an integer. -->
                    <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
                    <Add name = "SamplingPercentage" value = "50" />
                </Processor>
            </BuiltInProcessors>
        </TelemetryProcessors>
    ```

3. "FixedRateSamplingTelemetryProcessor" Işlemci etiketi içindeki aşağıdaki etiketleri kullanarak örnekleme içinden belirli telemetri türlerini dahil edebilir veya dışlayabilirsiniz
    ```XML
        <ExcludedTypes>
            <ExcludedType>Request</ExcludedType>
        </ExcludedTypes>

        <IncludedTypes>
            <IncludedType>Exception</IncludedType>
        </IncludedTypes>
    ```

Örneklemeye dahil edilebilir ya da hariç tutulacak telemetri türleri şunlardır: bağımlılık, olay, özel durum, PageView, Istek ve Izleme.

> [!NOTE]
> Örnekleme yüzdesi için, N 'nin bir tamsayı olduğu, 100/N 'e yakın bir yüzde seçin.  Şu anda örnekleme diğer değerleri desteklemiyor.
> 
> 

<a name="other-web-pages"></a>

### <a name="configuring-fixed-rate-sampling-in-opencensus-python"></a>OpenCensus Python 'da sabit hızlı örnekleme yapılandırma ###

1. Uygulamanızı en son [Opencensus Azure izleyici dışarı layıcılarını](../../azure-monitor/app/opencensus-python.md)kullanarak işaretleyin.

> [!NOTE]
> Sabit fiyat örnekleme yalnızca izleme Dışarı Aktarıcı kullanılarak kullanılabilir. Bu, gelen ve giden isteklerin örnekleme yapılandırılabileceği tek türdeki telemetri olduğu anlamına gelir.
> 
> 

2. `sampler` yapılandırmanız kapsamında bir `Tracer` belirtmelisiniz. Açık bir örnekleyiciyi sağlanmazsa, varsayılan olarak, olasılık örnekleyici örneği kullanılacaktır. Tüm 10000 isteklerinin bir tanesi Application Insights için varsayılan olarak, olasılık 1/10000 bir hız kullanır. Örnekleme oranı belirtmek isterseniz aşağıya bakın.

3. Örnekleyici belirtirken `Tracer` öğesinin örnekleme oranı 0,0 ile 1,0 (1,0 dahil) arasında olan bir örnekleyici belirttiğinden emin olun. 1,0 örnekleme oranı %100 ' i temsil eder, ancak tüm istekleriniz Application Insights telemetri olarak gönderilir.

    ```python
    tracer = Tracer(
        exporter=AzureExporter(
            instrumentation_key='00000000-0000-0000-0000-000000000000',
        ),
        sampler=ProbabilitySampler(1.0),
    )
    ```

## <a name="ingestion-sampling"></a>Giriş örnekleme

Bu örnekleme biçimi, Web sunucunuz, tarayıcılarınız ve cihazlarınızdaki telemetrinin Application Insights hizmet uç noktasına ulaştığı noktada çalışır. Uygulamanızdan gönderilen telemetri trafiğini düşürmese de, Application Insights göre işlenen ve tutulan miktarı azaltır (ve için ücretlendirilir).

Uygulamanız genellikle aylık kotasına geçtiğinde bu örnekleme türünü kullanın ve SDK tabanlı örnekleme türlerinden birini kullanma seçeneğiniz yoktur. 

Örnekleme hızını kullanım ve tahmini maliyetler sayfasında ayarlayın:

![Uygulamaya genel bakış dikey penceresinde ayarlar, kota, örnekler ' e tıklayın, ardından bir örnekleme hızı seçin ve Güncelleştir ' e tıklayın.](./media/sampling/04.png)

Diğer örnekleme türleri gibi, algoritma ilgili telemetri öğelerini korur. Örneğin, aramada Telemetriyi incelerken belirli bir özel durumla ilgili isteği bulabilirsiniz. İstek hızı ve özel durum oranı gibi ölçüm sayımları doğru tutulur.

Örnekleme tarafından atılan veri noktaları, [sürekli dışarı aktarma](../../azure-monitor/app/export-telemetry.md)gibi Application Insights hiçbir özellik için kullanılamaz.

SDK tabanlı Uyarlamalı veya sabit oran örneklemesi çalışırken Alım örnekleme işlemi çalışmıyor. Uyarlamalı örnekleme, ASP.NET/ASP.NET Core SDK, Visual Studio 'da etkinleştirildiğinde veya Azure Web App Extensions veya Durum İzleyicisi kullanılarak etkinleştirildiğinde varsayılan olarak etkindir ve alma örneklemesi devre dışı bırakılır. SDK 'daki örnekleme oranı %100 ' den küçükse (ör. öğeler örnekleniyor), ayarladığınız Alım örnekleme hızı yok sayılır.

> [!WARNING]
> Kutucukta gösterilen değer, alma örneklemesi için ayarladığınız değeri gösterir. SDK örneklemesi işlem halinde ise gerçek örnekleme oranını temsil etmez.
>
>
## <a name="sampling-for-web-pages-with-javascript"></a>JavaScript ile Web sayfaları için örnekleme
Web sayfalarını, herhangi bir sunucudan sabit hızlı örnekleme için yapılandırabilirsiniz. 

[Application Insights için Web sayfalarını yapılandırdığınızda](../../azure-monitor/app/javascript.md), Application Insights portalından aldığınız JavaScript kod parçacığını değiştirin. (ASP.NET uygulamalarında, kod parçacığı genellikle _Layout. cshtml 'ye gider.)  İzleme anahtarından önce `samplingPercentage: 10,` gibi bir satır ekleyin:

    <script>
    var appInsights= ... 
    }({ 


    // Value must be 100/N where N is an integer.
    // Valid examples: 50, 25, 20, 10, 5, 1, 0.1, ...
    samplingPercentage: 10, 

    instrumentationKey:...
    }); 

    window.appInsights=appInsights; 
    appInsights.trackPageView(); 
    </script> 

Örnekleme yüzdesi için, N 'nin bir tamsayı olduğu, 100/N 'e yakın bir yüzde seçin.  Şu anda örnekleme diğer değerleri desteklemiyor.

Ayrıca, sunucuda sabit hızlı örnekleme de yapılandırırsanız, istemci ve sunucu eşitlenir, bu sayede arama bölümünde ilgili sayfa görünümleri ve istekleri arasında gezinebilirsiniz.

## <a name="when-to-use-sampling"></a>Örnekleme ne zaman kullanılır?

Uyarlamalı örnekleme, en son .NET ve .NET Core SDK 'lerinde otomatik olarak etkinleştirilir. Kullandığınız SDK sürümünden bağımsız olarak, toplanan verileri örneklemek Application Insights izin vermek için alma örneklemesini etkinleştirebilirsiniz.

Varsayılan olarak, Java SDK 'da hiçbir örnekleme etkinleştirilmez. Şu anda yalnızca sabit fiyat örneklemesini destekler. Uyarlamalı örnekleme, Java SDK 'da desteklenmez.

Genel olarak, çoğu küçük ve orta büyüklükte uygulamalar için örnekleme gerekmez. En yararlı tanılama bilgileri ve en doğru istatistikler, tüm Kullanıcı etkinliklerinizde veri toplanarak elde edilir. 

Örnekleme 'nın başlıca avantajları şunlardır:

* Uygulamanız kısa zaman aralığında çok yüksek düzeyde telemetri gönderdiğinde, hizmet bırakmaları ("kısıtlar") veri noktalarını Application Insights. 
* Fiyatlandırma katmanınız için veri noktalarının [kotası](../../azure-monitor/app/pricing.md) içinde tutulması. 
* Telemetri koleksiyonundan ağ trafiğini azaltmak için. 

### <a name="which-type-of-sampling-should-i-use"></a>Ne tür örnekleme kullanmalıyım?

**Şu durumlarda giriş örnekleme kullan:**

* Genellikle aylık telemetri kotasına gidebilirsiniz.
* SDK 'nın örnekleme desteği olmayan bir sürümünü kullanıyorsunuz. Örneğin, 2 ' den önceki ASP.NET sürümleri.
* Kullanıcılarınızın web tarayıcılarınızdan çok fazla telemetri elde edersiniz.

**Şu durumlarda sabit fiyat örnekleme kullan:**

* ASP.NET Web Hizmetleri sürüm 2.0.0 veya üzeri ya da Java SDK v 2.0.1'i veya üzeri için Application Insights SDK 'sını kullanıyorsunuz ve
* İstemci ve sunucu arasında eşitlenmiş örnekleme istiyorsunuz. bu sayede, [aramada](../../azure-monitor/app/diagnostic-search.md)olayları araştırdığınızda, istemci ve sunucu üzerindeki, sayfa görünümleri ve http istekleri gibi ilgili olaylar arasında gezinebilirsiniz.
* Uygulamanız için uygun örnekleme yüzdesinden emin olursunuz. Doğru ölçümleri almak için yeterince yüksek olmalıdır, ancak fiyatlandırma kotanızı ve azaltma limitlerini aşan oranın altında. 

**Uyarlamalı örnekleme kullan:**

Diğer örnekleme formlarını kullanma koşulları geçerli değilse, uyarlamalı örnekleme önerilir. Bu ayar, ASP.NET/ASP.NET Core Server SDK 'da varsayılan olarak etkindir. Belirli bir minimum hıza ulaşılana kadar trafiği azaltmayacak, bu nedenle düşük kullanım siteleri etkilenmez.

## <a name="how-do-i-know-whether-sampling-is-in-operation"></a>Örnekleme işleminin işleme mi olduğunu Nasıl yaparım? mı?

Gerçek örnekleme oranını nerede uygulandığını fark etmeksizin öğrenmek için, şöyle bir [analiz sorgusu](../../azure-monitor/app/analytics.md) kullanın:

```
union requests,dependencies,pageViews,browserTimings,exceptions,traces
| where timestamp > ago(1d)
| summarize RetainedPercentage = 100/avg(itemCount) by bin(timestamp, 1h), itemType
```

Herhangi bir tür için RetainedPercentage 100 'den küçükse, bu öğe örneklenir.

**Application Insights, yukarıda açıklanan herhangi bir örnekleme teknimesindeki oturum, ölçüm ve performans sayacı telemetri türlerini örneklemez. Kesinliği azaltma bu telemetri türleri için son derece istenmeyen bir şekilde istenirken, bu türler her zaman örneklemeye dahil edilmez**

## <a name="how-does-sampling-work"></a>Örnekleme nasıl çalışır?

2\.0.0 ve Java SDK 'Sı sürüm 2.0.1 ve sonraki sürümlerinde SDK 'nın ASP.NET sürümlerindeki sabit fiyat örnekleme özelliği. Uyarlamalı örnekleme, 2.0.0 ile ASP.NET sürümlerindeki SDK 'nın bir özelliğidir. Alma örnekleme, Application Insights hizmetin bir özelliğidir ve SDK örnekleme gerçekleştirmediğinden işlem içinde olabilir.

Örnekleme algoritması, hangi telemetri öğelerinin atılanacağına ve hangilerinin tutulacağını (SDK 'da veya Application Insights hizmetinde olsun) belirler. Örnekleme kararı, tüm birbiriyle ilişkili tüm veri noktalarını korumayı hedefleyen çeşitli kurallara dayanır. Bu, daha az bir veri kümesiyle aynı şekilde işlem yapılabilir ve güvenilir Application Insights bir tanılama deneyimi sağlar. Örneğin, uygulamanız başarısız bir istek için ek telemetri öğeleri (Bu istekten günlüğe kaydedilen özel durum ve izleme gibi) gönderdiğinde örnekleme bu isteği ve diğer Telemetriyi bölmeyecektir. Bu, hepsi bir arada tutar ya da bırakır. Sonuç olarak, Application Insights istek ayrıntılarına baktığınızda, isteği ilişkili telemetri öğeleriyle birlikte her zaman görebilirsiniz. 

Örnekleme kararı, isteğin işlem KIMLIĞINE dayalıdır, bu da belirli bir işleme ait tüm telemetri öğelerinin korunduğu veya bıraktığı anlamına gelir. İşlem KIMLIĞI ayarlanmamış telemetri öğeleri için (örneğin, http bağlamı olmayan zaman uyumsuz iş parçacıklarından bildirilen telemetri öğeleri) örnekleme, her türün telemetri öğelerinin yüzdesini yakalar. .NET SDK 2.5.0 ve 2.2.0-Beta3 ASP.NET Core SDK ' dan önce, örnekleme kararı "Kullanıcı" (diğer bir deyişle, en tipik Web uygulamaları) tanımlayan uygulamalar için Kullanıcı KIMLIĞI karmasını temel alır. Kullanıcıları tanımlamayan uygulama türleri (örneğin, Web Hizmetleri) için örnekleme kararı isteğin işlem KIMLIĞINE dayalıdır.

Telemetriyi size geri sunarken, Application Insights hizmeti, eksik veri noktalarının dengelemesini sağlamak için koleksiyon sırasında kullanılan aynı örnekleme yüzdesine göre ölçümleri ayarlar. Bu nedenle, Application Insights Telemetriyi ararken, kullanıcılar gerçek sayılara çok yakın olan istatistiksel olarak doğru yaklaşık olarak görülür.

Yaklaşık büyük ölçüde doğruluk, yapılandırılan örnekleme yüzdesine bağlıdır. Ayrıca, çok sayıda kullanıcıdan oluşan, genellikle benzer isteklerin büyük bir hacmini işleyen uygulamalar için doğruluk artar. Öte yandan, önemli bir yük ile çalışmayan uygulamalar için, bu uygulamalar genellikle veri kaybını azaltmadan kota içinde kalarak tüm telemetrilerini gönderebilmeleri için örnekleme gerekmez. 

> [!WARNING]
> Application Insights, ölçüm ve oturum telemetri türlerini örneklemez. Duyarlığın azaltılması, bu telemetri türleri için yüksek ölçüde istenmeyen olabilir.
> 

### <a name="adaptive-sampling"></a>Uyarlamalı örnekleme

Uyarlamalı örnekleme, SDK 'dan geçerli iletim hızını izleyen bir bileşen ekler ve hedef en yüksek ücret içinde kalmak için örnekleme yüzdesini ayarlar. Ayarlama, düzenli aralıklarla yeniden hesaplanır ve giden aktarım hızının hareketli ortalamasını temel alır.

## <a name="sampling-and-the-javascript-sdk"></a>Örnekleme ve JavaScript SDK 'Sı

İstemci tarafı (JavaScript) SDK 'Sı, sabit hızlı örneklemeye sunucu tarafı SDK 'Sı ile birlikte katılır. Araçlı sayfalar yalnızca sunucu tarafında "örneğe örnek olarak" kararına yaptığı aynı kullanıcılardan istemci tarafı telemetri gönderir. Bu mantık, istemci ve sunucu taraflarındaki Kullanıcı oturumunun bütünlüğünü sürdürmek için tasarlanmıştır. Sonuç olarak, Application Insights belirli bir telemetri öğesinden bu kullanıcı veya oturum için diğer tüm telemetri öğelerini bulabilirsiniz. 

*İstemci ve sunucu tarafı telemetrisi, yukarıda açıklandığı gibi Eşgüdümlü örnekler göstermez.*

* Hem sunucu hem de istemci üzerinde sabit hızlı örnekleme 'yi etkinleştirdiğinizi doğrulayın.
* SDK sürümünün 2,0 veya üzeri olduğundan emin olun.
* Hem istemcide hem de sunucuda aynı örnekleme yüzdesini ayarlamış olun.

### <a name="sampling-in-azure-functions"></a>Azure Işlevleri 'nde örnekleme

Azure Işlevlerinde çalışan uygulamaların örneklemesini yapılandırmak için [Bu](https://docs.microsoft.com/azure/azure-functions/functions-monitoring#configure-sampling) yönergeleri izleyin.

## <a name="frequently-asked-questions"></a>Sık Sorulan Sorular

*ASP.NET ve ASP.NET Core SDK 'daki varsayılan örnekleme davranışı nedir?*

* Yukarıdaki SDK 'nın en son sürümlerinden birini kullanıyorsanız, uyarlamalı örnekleme varsayılan olarak saniyede beş telemetri öğesi ile etkinleştirilir.
  Varsayılan olarak 2 AdaptiveSamplingTelemetryProcessors eklenmiştir ve biri örneklemede olay türü içerir ve diğer olay türü örneklemeyi içermez. Bu yapılandırma, SDK 'nın telemetri öğelerini olay türlerindeki beş telemetri öğesiyle sınırlandırmaya çalışacağı anlamına gelir ve diğer tüm türlerin beş telemetri öğesi birleştirilir ve bu sayede olayların diğer telemetri türlerinden ayrı olarak örneklendiği doğrulanıyor. Olaylar genellikle iş telemetri için kullanılır ve büyük olasılıkla tanılama telemetri birimlerinden etkilenmemelidir.
  
  Aşağıda, oluşturulan varsayılan ApplicationInsights. config dosyası gösterilmektedir. Açıklandığı gibi, iki ayrı AdaptiveSamplingTelemetryProcessor düğümü eklenir, biri olay türü hariç ve diğeri de dahil olmak üzere. ASP.NET Core, kodda tam olarak aynı varsayılan davranış etkinleştirilmiştir. Bu varsayılan davranışı değiştirmek için belgenin önceki bölümündeki örnekleri kullanın.

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

* Hayır. SamplingTelemetryProcessors, öğe zaten örneklenirken örnekleme hususlarından öğeleri yoksayar. Aynı değer, aynı zamanda SDK 'nın kendisinde zaten örneklendiği öğelere örnekleme uygulamayatıon örnekleme için de geçerlidir. '

*Neden basit bir "her telemetri türünün X 'i toplayın" örneklenmiyor?*

* Bu örnekleme yaklaşımı ölçüm yaklaşımında yüksek düzeyde bir duyarlığa sahip olsa da, Tanılama için kritik olan Kullanıcı, oturum ve istek başına tanılama verilerini ilişkilendirme özelliğini keser. Bu nedenle örnekleme, "Uygulama kullanıcılarının X için tüm telemetri öğelerini topla" veya "uygulama isteklerinin X için tüm telemetrisini topla" mantığıyla daha iyi sonuç verir. İsteklerle ilişkili olmayan telemetri öğeleri (örneğin, arka planda zaman uyumsuz işlem) için, geri dönüş "her telemetri türü için tüm öğelerin yüzde X 'ini toplayın". 

*Örnekleme yüzdesi zaman içinde değişsin mi?*

* Evet, uyarlamalı örnekleme, telemetri yüzdesinin, geçerli olarak gözlemlenen hacme hacmine göre yavaş şekilde değiştirir.

*Sabit fiyat örneklemesi kullandığımda, hangi örnekleme yüzdesinin uygulamam için en iyi şekilde çalıştığını nasıl anlarım?*

* Tek bir yol, uyarlamalı örnekleme ile başlamak, hangi hızların bulunduğunu öğrenmek (Yukarıdaki soruya bakın) ve ardından bu oranı kullanarak sabit hızlı örneklemeye geçiş yapmanızı sağlar. 
  
    Aksi takdirde, tahmin etmeniz gerekir. Application Insights ' de geçerli telemetri kullanımınızı çözümleyin, oluşan herhangi bir daraltma gözlemleyin ve toplanan telemetri hacmini tahmin edin. Seçtiğiniz fiyatlandırma katmanınız ile birlikte bu üç giriş, toplanan Telemetriyi ne kadar azaltmak istediğinizi tavsiye edebilir. Ancak, kullanıcılarınızın sayısında veya telemetri hacminde başka bir vardiya artışı tahmininizi geçersiz kılabilir.

*Örnekleme yüzdesini çok düşük bir şekilde yapılandırdığımda ne olur?*

* Aşırı düşük örnekleme yüzdesi (aşırı olmayan örnekleme), Application Insights veri hacmi azalmasıyla ilgili verilerin görselleştirmesini dengelemek istediğinde yaklaşık olarak en yakın doğruluk düzeyini azaltır. Ayrıca, sık başarısız olan veya yavaş isteklerin bazıları örneklendiği için tanılama deneyimi olumsuz etkilenebilir.

*Örnekleme yüzdesini çok yüksek olarak yapılandırdığımda ne olur?*

* Çok yüksek örnekleme yüzdesini (yeterince ısrarlı değil) yapılandırmak, toplanan Telemetriyi birimde yetersiz azalmayla sonuçlanır. Azaltma ile ilgili telemetri veri kaybını yaşamaya devam edebilir ve Application Insights kullanım maliyeti, fazla kullanım ücretleri nedeniyle planlanmasından daha yüksek olabilir.

*Hangi platformlarda örnekleme kullanabilirim?*

* SDK örnekleme gerçekleştirmediğinden, belirli bir birimin üzerindeki herhangi bir telemetri için alma örneklemesi otomatik olarak gerçekleşebilir. Bu yapılandırma, örneğin, daha eski bir ASP.NET SDK sürümü veya Java SDK 'sının önceki bir sürümü (1.0.10 veya öncesi) kullanıyorsanız çalışır.
* ASP.NET SDK sürümleri 2.0.0 ve üzeri ya da ASP.NET CORE SDK sürümü 2.2.0 ve üzeri (Azure 'da veya kendi sunucunuzda barındırılan) kullanıyorsanız, varsayılan olarak Uyarlamalı örnekleme alırsınız, ancak yukarıda açıklanan şekilde sabit ücret ile geçiş yapabilirsiniz. Sabit hızlı örnekleme ile, tarayıcı SDK 'Sı, örnek ilgili olaylarla otomatik olarak eşitlenir. 
* Java SDK 'Sı 2.0.1 veya üstünü kullanıyorsanız, ApplicationInsights. xml ' i sabit fiyat örneklemesini açmak için yapılandırabilirsiniz. Örnekleme varsayılan olarak kapalıdır. Sabit hızlı örnekleme ile, tarayıcı SDK 'Sı, örnek ilgili olaylarla otomatik olarak eşitlenir.

*Her zaman görmek istediğim bazı nadir olaylar vardır. Örnekleme modülünü nasıl geçmiş bir şekilde alabilirim?*

* Bunu başarmanın en iyi yolu, aşağıda gösterildiği gibi, `SamplingPercentage`, korunması istediğiniz telemetri öğesinde 100 olarak ayarlayan özel bir [telemetryınitializer](../../azure-monitor/app/api-filtering-sampling.md#addmodify-properties-itelemetryinitializer)yazmaktır. Başlatıcılar telemetri işlemcilerinin (örnekleme dahil) önce çalıştırılmasının garanti edilir. Bu, tüm örnekleme tekniklerinin bu öğeyi herhangi bir örnekleme ile yoksaymasını sağlar.

```csharp
     public class MyTelemetryInitializer : ITelemetryInitializer
      {
         public void Initialize(ITelemetry telemetry)
        {
            if(somecondition)
            {
                ((ISupportSampling)item).SamplingPercentage = 100;
            }
        }
      }
```

## <a name="next-steps"></a>Sonraki adımlar

* [Filtreleme](../../azure-monitor/app/api-filtering-sampling.md) , SDK 'nizin ne kadar sıkı bir şekilde denetim sağlayabilir.
* Geliştirici ağı makalesini okuyun [Application Insights Ile Telemetriyi iyileştirin](https://msdn.microsoft.com/magazine/mt808502.aspx).
