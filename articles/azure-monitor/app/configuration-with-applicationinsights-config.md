---
title: ApplicationInsights. config başvurusu-Azure | Microsoft Docs
description: Veri toplama modüllerini etkinleştirin veya devre dışı bırakın ve performans sayaçlarını ve diğer parametreleri ekleyin.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 05/22/2019
ms.reviewer: olegan
ms.openlocfilehash: 94ae9035c1657c1ce20c40234ddca95ae30d9edd
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72677531"
---
# <a name="configuring-the-application-insights-sdk-with-applicationinsightsconfig-or-xml"></a>ApplicationInsights.config veya .xml ile Application Insights SDK yapılandırma
Application Insights .NET SDK 'Sı bazı NuGet paketlerinden oluşur. [Çekirdek paket](https://www.nuget.org/packages/Microsoft.ApplicationInsights) , Application Insights telemetri göndermek için API sağlar. [Ek paketler](https://www.nuget.org/packages?q=Microsoft.ApplicationInsights) , uygulama ve bağlamınızdan Telemetriyi otomatik olarak izlemek için telemetri *modülleri* ve *başlatıcılar* sağlar. Yapılandırma dosyasını ayarlayarak telemetri modüllerini ve başlatıcıları etkinleştirebilir veya devre dışı bırakabilir ve bunların bazıları için parametreler ayarlayabilirsiniz.

Yapılandırma dosyası, uygulamanızın türüne göre `ApplicationInsights.config` veya `ApplicationInsights.xml` olarak adlandırılır. [SDK 'nın birçok sürümünü yüklediğinizde][start]projenize otomatik olarak eklenir. Varsayılan olarak, **> ekleme Application Insights telemetri**destekleyen Visual Studio şablon projelerinden otomatik deneyim kullanılırken, ApplicationInsights. config dosyası proje kök klasöründe oluşturulur ve bin klasörü. Ayrıca bir [IIS sunucusundaki durum İzleyicisi][redfield]tarafından bir Web uygulamasına da eklenir. Azure [Web sitesi için uzantı](azure-web-apps.md) veya [Azure VM için uzantı ve sanal makine ölçek kümesi](azure-vm-vmss-apps.md) kullanılıyorsa yapılandırma dosyası yok sayılır.

[Bir Web sayfasında SDK 'yı][client]denetlemek için eşdeğer bir dosya yok.

Bu belgede yapılandırma dosyasında gördüğünüz bölümler, SDK 'nın bileşenlerini nasıl denetdukları ve hangi NuGet paketlerinin bu bileşenleri yüklemesi açıklanmaktadır.

> [!NOTE]
> ApplicationInsights. config ve. xml yönergeleri .NET Core SDK uygulanmaz. .NET Core uygulamalarını yapılandırmak için [Bu](../../azure-monitor/app/asp-net-core.md) kılavuzu izleyin.

## <a name="telemetry-modules-aspnet"></a>Telemetri modülleri (ASP.NET)
Her telemetri modülü belirli bir veri türünü toplar ve verileri göndermek için çekirdek API kullanır. Modüller, aynı zamanda gerekli satırları. config dosyasına da ekleyen farklı NuGet paketleri tarafından yüklenir.

Her modülün yapılandırma dosyasında bir düğüm bulunur. Bir modülü devre dışı bırakmak için düğümü silin veya not edin.

### <a name="dependency-tracking"></a>Bağımlılık Izleme
[Bağımlılık izleme](../../azure-monitor/app/asp-net-dependencies.md) , uygulamanızın veritabanlarına ve dış hizmetlere ve veritabanlarına yaptığı çağrılar hakkında telemetri toplar. Bu modülün bir IIS sunucusunda çalışmasına izin vermek için [durum İzleyicisi yüklemeniz][redfield]gerekir.

Ayrıca, [TRACKDEPENDENCY API](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency)'sini kullanarak kendi bağımlılık izleme kodunuzu yazabilirsiniz.

* `Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule`
* [Microsoft. ApplicationInsights. DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) NuGet paketi.

Bağımlılıklar, aracı tabanlı (codeless) iliştirme kullanılarak kodunuzun değiştirilmeksizin otomatik olarak toplanabilir. Azure Web Apps 'te kullanmak için [Application Insights uzantısını](azure-web-apps.md)etkinleştirin. Azure VM 'de veya Azure sanal makine ölçek kümesinde kullanmak için, [VM ve sanal makine ölçek kümesi Için uygulama izleme uzantısını](azure-vm-vmss-apps.md)etkinleştirin.

### <a name="performance-collector"></a>Performans toplayıcısı
IIS yüklemelerinden CPU, bellek ve ağ yükü gibi [sistem performansı sayaçlarını toplar](../../azure-monitor/app/performance-counters.md) . Hangi sayaçların toplanacağını, sizin ayarladığınız performans sayaçlarını da dahil edebilirsiniz.

* `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule`
* [Microsoft. ApplicationInsights. PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector) NuGet paketi.

### <a name="application-insights-diagnostics-telemetry"></a>Application Insights tanılama telemetrisi
@No__t_0, Application Insights araçları kodundaki hataları raporlar. Örneğin, kod performans sayaçlarına erişemiyorsa veya bir `ITelemetryInitializer` özel durum oluşturursa. Bu modül tarafından izlenen izleme telemetrisi, [Tanılama aramasında][diagnostic]görüntülenir.

```
* `Microsoft.ApplicationInsights.Extensibility.Implementation.Tracing.DiagnosticsTelemetryModule`
* [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) NuGet package. If you only install this package, the ApplicationInsights.config file is not automatically created.
```

### <a name="developer-mode"></a>Geliştirici modu
`DeveloperModeWithDebuggerAttachedTelemetryModule`, uygulama işlemine bir hata ayıklayıcı eklendiğinde, Application Insights `TelemetryChannel` her seferinde bir telemetri öğesi olarak veri göndermesini zorlar. Bu, uygulamanızın Telemetriyi izlediği ve Application Insights portalında göründüğü süre arasındaki süreyi azaltır. CPU ve ağ bant genişliğinden önemli ölçüde ek yüke neden olur.

* `Microsoft.ApplicationInsights.WindowsServer.DeveloperModeWithDebuggerAttachedTelemetryModule`
* [Application Insights Windows Server](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) NuGet paketi

### <a name="web-request-tracking"></a>Web Isteği Izleme
HTTP isteklerinin [yanıt süresini ve sonuç kodunu](../../azure-monitor/app/asp-net.md) bildirir.

* `Microsoft.ApplicationInsights.Web.RequestTrackingTelemetryModule`
* [Microsoft. ApplicationInsights. Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) NuGet paketi

### <a name="exception-tracking"></a>Özel durum izleme
`ExceptionTrackingTelemetryModule` Web uygulamanızdaki işlenmemiş özel durumları izler. Bkz. [arızalar ve özel durumlar][exceptions].

* `Microsoft.ApplicationInsights.Web.ExceptionTrackingTelemetryModule`
* [Microsoft. ApplicationInsights. Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) NuGet paketi
* `Microsoft.ApplicationInsights.WindowsServer.UnobservedExceptionTelemetryModule`- [gözlemlenen görev özel durumlarını](https://blogs.msdn.com/b/pfxteam/archive/2011/09/28/task-exception-handling-in-net-4-5.aspx)izler.
* `Microsoft.ApplicationInsights.WindowsServer.UnhandledExceptionTelemetryModule`-çalışan rolleri, Windows Hizmetleri ve konsol uygulamaları için işlenmemiş özel durumları izler.
* [Application Insights Windows Server](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) NuGet paketi.

### <a name="eventsource-tracking"></a>EventSource Izleme
`EventSourceTelemetryModule`, bir Application Insights, izleme olarak gönderilecek EventSource olaylarını yapılandırmanızı sağlar. EventSource olaylarını izleme hakkında daha fazla bilgi için bkz. [EventSource olaylarını kullanma](../../azure-monitor/app/asp-net-trace-logs.md#use-eventsource-events).

* `Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule`
* [Microsoft. ApplicationInsights. EventSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener) 

### <a name="etw-event-tracking"></a>ETW olay Izleme
`EtwCollectorTelemetryModule`, ETW sağlayıcılarından izleme olarak Application Insights gönderilmek üzere olayları yapılandırmanıza olanak tanır. ETW olaylarını izleme hakkında daha fazla bilgi için bkz. [ETW olaylarını kullanma](../../azure-monitor/app/asp-net-trace-logs.md#use-etw-events).

* `Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule`
* [Microsoft. ApplicationInsights. EtwCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector) 

### <a name="microsoftapplicationinsights"></a>Microsoft. ApplicationInsights
Microsoft. ApplicationInsights paketi SDK 'nın [temel API](https://msdn.microsoft.com/library/mt420197.aspx) 'sini sağlar. Diğer telemetri modülleri bunu kullanır ve [bunu kendi telemetrinizi tanımlamak için](../../azure-monitor/app/api-custom-events-metrics.md)de kullanabilirsiniz.

* ApplicationInsights. config dosyasında giriş yok.
* [Microsoft. ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) NuGet paketi. Yalnızca bu NuGet 'i yüklüyorsanız, hiçbir. config dosyası oluşturulmaz.

## <a name="telemetry-channel"></a>Telemetri kanalı
[Telemetri kanalı](telemetry-channels.md) , Application Insights hizmetine telemetri arabelleğini ve aktarımını yönetir.

* `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel` Web uygulamaları için varsayılan kanaldır. Bellekte verileri arabelleğe alır ve daha güvenilir telemetri teslimi için yeniden deneme mekanizmaları ve yerel disk depolama alanı kullanır.
* `Microsoft.ApplicationInsights.InMemoryChannel`, başka bir kanal yapılandırılmamışsa kullanılan hafif bir telemetri kanaldır. 

## <a name="telemetry-initializers-aspnet"></a>Telemetri başlatıcıları (ASP.NET)
Telemetri başlatıcıları, her telemetri öğesiyle birlikte gönderilen bağlam özelliklerini ayarlar.

Bağlam özelliklerini ayarlamak için [kendi başlatıcılarınızı yazabilirsiniz](../../azure-monitor/app/api-filtering-sampling.md#add-properties) .

Standart başlatıcıların tümü Web veya WindowsServer NuGet paketleri tarafından ayarlanır:

* `AccountIdTelemetryInitializer` AccountID özelliğini ayarlar.
* `AuthenticatedUserIdTelemetryInitializer`, kimlik doğrulayan Teduserıd özelliğini JavaScript SDK 'Sı tarafından ayarlanmış olarak ayarlar.
* `AzureRoleEnvironmentTelemetryInitializer`, Azure çalışma zamanı ortamından ayıklanan bilgileri içeren tüm telemetri öğeleri için `Device` bağlamının `RoleName` ve `RoleInstance` özelliklerini güncelleştirir.
* `BuildInfoConfigComponentVersionTelemetryInitializer`, tüm telemetri öğeleri için `Component` bağlamının `Version` özelliğini MS Build tarafından üretilen `BuildInfo.config` dosyasından Ayıklanan değer ile güncelleştirir.
* `ClientIpHeaderTelemetryInitializer`, isteğin `X-Forwarded-For` HTTP üstbilgisine göre tüm telemetri öğelerinin `Location` bağlamının `Ip` özelliğini güncelleştirir.
* `DeviceTelemetryInitializer` tüm telemetri öğeleri için `Device` bağlamının aşağıdaki özelliklerini güncelleştirir.
  * `Type` "PC" olarak ayarlandı
  * `Id`, Web uygulamasının çalıştığı bilgisayarın etki alanı adına ayarlanır.
  * `OemName`, WMI kullanılarak `Win32_ComputerSystem.Manufacturer` alanından ayıklanan değere ayarlanır.
  * `Model`, WMI kullanılarak `Win32_ComputerSystem.Model` alanından ayıklanan değere ayarlanır.
  * `NetworkType`, `NetworkInterface` ayıklanan değere ayarlanır.
  * `Language`, `CurrentCulture` adına ayarlanır.
* `DomainNameRoleInstanceTelemetryInitializer`, tüm telemetri öğeleri için `Device` bağlamının `RoleInstance` özelliğini, Web uygulamasının çalıştığı bilgisayarın etki alanı adı ile güncelleştirir.
* `OperationNameTelemetryInitializer`, `RequestTelemetry` `Name` özelliğini ve HTTP yöntemine göre tüm telemetri öğelerinin `Operation` bağlamının `Name` özelliğinin ve isteği işlemek için çağrılan ASP.NET MVC denetleyicisinin ve eylemin adlarını güncelleştirir.
* `OperationIdTelemetryInitializer` veya `OperationCorrelationTelemetryInitializer`, bir isteği otomatik olarak oluşturulan `RequestTelemetry.Id` ile işlerken izlenen tüm telemetri öğelerinin `Operation.Id` bağlam özelliğini güncelleştirir.
* `SessionTelemetryInitializer`, Kullanıcı tarayıcısında çalışan ApplicationInsights JavaScript izleme kodu tarafından oluşturulan `ai_session` tanımlama bilgisinden ayıklanan değere sahip tüm telemetri öğeleri için `Session` bağlamının `Id` özelliğini güncelleştirir.
* `SyntheticTelemetryInitializer` veya `SyntheticUserAgentTelemetryInitializer`, bir kullanılabilirlik testi veya arama motoru bot gibi yapay bir kaynaktan gelen bir isteği işlerken izlenen tüm telemetri öğelerinin `User`, `Session` ve `Operation` bağlamlarının özelliklerini güncelleştirir. [Ölçüm Gezgini](../../azure-monitor/app/metrics-explorer.md) , varsayılan olarak yapay telemetri göstermez.

    İsteklerin özelliklerini tanımlayan `<Filters>`.
* `UserTelemetryInitializer`, tüm telemetri öğeleri için `User` bağlamının `Id` ve `AcquisitionDate` özelliklerini, Kullanıcı tarayıcısında çalışan `ai_user` JavaScript izleme kodu tarafından oluşturulan Application Insights tanımlama bilgisinden ayıklanan değerler ile güncelleştirir.
* `WebTestTelemetryInitializer`, [kullanılabilirlik testlerinden](../../azure-monitor/app/monitor-web-app-availability.md)gelen http isteklerine YÖNELIK Kullanıcı kimliği, oturum kimliği ve yapay kaynak özelliklerini ayarlar.
  İsteklerin özelliklerini tanımlayan `<Filters>`.

Service Fabric çalıştıran .NET uygulamaları için, `Microsoft.ApplicationInsights.ServiceFabric` NuGet paketini dahil edebilirsiniz. Bu paket, telemetri öğelerine Service Fabric özellikleri ekleyen bir `FabricTelemetryInitializer` içerir. Daha fazla bilgi için, bu NuGet paketi tarafından eklenen özellikler hakkında [GitHub sayfasına](https://github.com/Microsoft/ApplicationInsights-ServiceFabric/blob/master/README.md) bakın.

## <a name="telemetry-processors-aspnet"></a>Telemetri Işlemcileri (ASP.NET)
Telemetri Işlemcileri SDK 'dan portala gönderilmeden hemen önce her bir telemetri öğesini filtreleyebilir ve değiştirebilir.

[Kendi telemetri işlemclerinizi yazabilirsiniz](../../azure-monitor/app/api-filtering-sampling.md#filtering).

#### <a name="adaptive-sampling-telemetry-processor-from-200-beta3"></a>Uyarlamalı örnekleme telemetrisi Işlemcisi (2.0.0-Beta3)
Bu, varsayılan olarak etkindir. Uygulamanız çok sayıda telemetri gönderiyorsa, bu işlemci bazılarını kaldırır.

```xml

    <TelemetryProcessors>
      <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
        <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
      </Add>
    </TelemetryProcessors>

```

Parametresi, algoritmanın elde etmeye çalışacağı hedefi sağlar. SDK 'nın her örneği bağımsız olarak çalışır, bu nedenle sunucunuz birkaç makine kümesi ise, telemetri gerçek hacmi buna göre çarpılacak.

[Örnekleme hakkında daha fazla bilgi edinin](../../azure-monitor/app/sampling.md).

#### <a name="fixed-rate-sampling-telemetry-processor-from-200-beta1"></a>Sabit fiyat örnekleme telemetrisi Işlemcisi (2.0.0-Beta1)
Ayrıca standart bir [örnekleme telemetri işlemcisi](../../azure-monitor/app/api-filtering-sampling.md) de vardır (2.0.1 'ten):

```XML

    <TelemetryProcessors>
     <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">

     <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
     <SamplingPercentage>10</SamplingPercentage>
     </Add>
   </TelemetryProcessors>

```



## <a name="channel-parameters-java"></a>Kanal parametreleri (Java)
Bu parametreler, Java SDK 'sının topladığı telemetri verilerini nasıl depolayıp temizlemelidir.

#### <a name="maxtelemetrybuffercapacity"></a>MaxTelemetryBufferCapacity
SDK 'nın bellek içi depolamada depolanabilecek telemetri öğelerinin sayısı. Bu sayıya ulaşıldığında, telemetri arabelleği temizlenir; diğer bir deyişle telemetri öğeleri Application Insights sunucusuna gönderilir.

* En az: 1
* En fazla: 1000
* Varsayılan: 500

```

  <ApplicationInsights>
      ...
      <Channel>
       <MaxTelemetryBufferCapacity>100</MaxTelemetryBufferCapacity>
      </Channel>
      ...
  </ApplicationInsights>
```

#### <a name="flushintervalinseconds"></a>Flushıntervalınseconds
Bellek içi depolamada depolanan verilerin ne sıklıkta temizlendiğinden (Application Insights gönderilmesi) belirler.

* En az: 1
* En fazla: 300
* Varsayılan: 5

```

    <ApplicationInsights>
      ...
      <Channel>
        <FlushIntervalInSeconds>100</FlushIntervalInSeconds>
      </Channel>
      ...
    </ApplicationInsights>
```

#### <a name="maxtransmissionstoragecapacityinmb"></a>Maxtransmissionstoragecapacityınmb
Yerel diskteki kalıcı depolamaya ayrılan, MB cinsinden en büyük boyutu belirler. Bu depolama Application Insights uç noktasına aktarılamayan kalıcı telemetri öğeleri için kullanılır. Depolama boyutu karşılandığında yeni telemetri öğeleri atılır.

* En az: 1
* En fazla: 100
* Varsayılan: 10

```

   <ApplicationInsights>
      ...
      <Channel>
        <MaxTransmissionStorageCapacityInMB>50</MaxTransmissionStorageCapacityInMB>
      </Channel>
      ...
   </ApplicationInsights>
```

#### <a name="local-forwarder"></a>Yerel iletici

[Yerel iletici](opencensus-local-forwarder.md) , çeşitli SDK ve çerçevelerden Application Insights veya [opencensus](https://opencensus.io/) telemetrisini toplayan ve Application Insights yönlendiren bir aracıdır. Windows ve Linux altında çalıştırma yeteneğine sahiptir. Java SDK Application Insights ile birlikte kullanıldığında, yerel iletici [canlı ölçümler](../../azure-monitor/app/live-stream.md) ve Uyarlamalı örnekleme için tam destek sağlar.

```xml
<Channel type="com.microsoft.applicationinsights.channel.concrete.localforwarder.LocalForwarderTelemetryChannel">
<EndpointAddress><!-- put the hostname:port of your LocalForwarder instance here --></EndpointAddress>

<!-- The properties below are optional. The values shown are the defaults for each property -->

<FlushIntervalInSeconds>5</FlushIntervalInSeconds><!-- must be between [1, 500]. values outside the bound will be rounded to nearest bound -->
<MaxTelemetryBufferCapacity>500</MaxTelemetryBufferCapacity><!-- units=number of telemetry items; must be between [1, 1000] -->
</Channel>
```

SpringBoot Starter kullanıyorsanız, yapılandırma dosyanıza (Application. Properties) aşağıdakini ekleyin:

```yml
azure.application-insights.channel.local-forwarder.endpoint-address=<!--put the hostname:port of your LocalForwarder instance here-->
azure.application-insights.channel.local-forwarder.flush-interval-in-seconds=<!--optional-->
azure.application-insights.channel.local-forwarder.max-telemetry-buffer-capacity=<!--optional-->
```

Varsayılan değerler SpringBoot Application. Properties ve ApplicationInsights. xml yapılandırması için aynıdır.

## <a name="instrumentationkey"></a>ınstrumentationkey
Bu, verilerinizin göründüğü Application Insights kaynağını belirler. Genellikle uygulamalarınızın her biri için ayrı bir anahtarla ayrı bir kaynak oluşturursunuz.

Anahtarı dinamik olarak ayarlamak istiyorsanız, örneğin uygulamanızdaki sonuçları farklı kaynaklara göndermek istiyorsanız, yapılandırma dosyasından anahtarı atlayabilir ve bunun yerine kodda ayarlayabilirsiniz.

Standart telemetri modülleri de dahil olmak üzere tüm TelemetryClient örneklerinin anahtarını ayarlamak için, TelemetryConfiguration. Active içinde anahtarı ayarlayın. Bunu bir ASP.NET hizmetinde global.aspx.cs gibi bir başlatma yönteminde yapın:

```csharp

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey =
          // - for example -
          WebConfigurationManager.AppSettings["ikey"];
      //...
```

Yalnızca belirli bir olay kümesini farklı bir kaynağa göndermek istiyorsanız, belirli bir TelemetryClient için anahtarı ayarlayabilirsiniz:

```csharp

    var tc = new TelemetryClient();
    tc.Context.InstrumentationKey = "----- my key ----";
    tc.TrackEvent("myEvent");
    // ...

```

Yeni bir anahtar almak için [Application Insights portalında yeni bir kaynak oluşturun][new].



## <a name="applicationid-provider"></a>ApplicationId sağlayıcısı

_V 2.6.0 'dan başlayarak kullanılabilir_

Bu sağlayıcının amacı, bir Izleme anahtarına göre bir uygulama KIMLIĞI aramak için kullanılır. Uygulama KIMLIĞI Requesttelemetri ve Dependencytelemetri 'e dahildir ve portalda bağıntıyı belirlemede kullanılır.

Bu, kodda veya config içinde `TelemetryConfiguration.ApplicationIdProvider` ayarlanarak kullanılabilir.

### <a name="interface-iapplicationidprovider"></a>Arabirim: ıapplicationıdprovider

```csharp
public interface IApplicationIdProvider
{
    bool TryGetApplicationId(string instrumentationKey, out string applicationId);
}
```


[Microsoft. ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) SDK 'sında iki uygulama sunuyoruz: `ApplicationInsightsApplicationIdProvider` ve `DictionaryApplicationIdProvider`.

### <a name="applicationinsightsapplicationidprovider"></a>ApplicationInsightsApplicationIdProvider

Bu, profil API 'imizin etrafındaki bir sarmalayıcıdır. İstekleri ve önbellek sonuçlarını azaleder.

Bu sağlayıcı, [Microsoft. ApplicationInsights. DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) veya [Microsoft. ApplicationInsights. Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) 'i yüklediğinizde yapılandırma dosyanıza eklenir

Bu sınıf `ProfileQueryEndpoint` isteğe bağlı bir özelliğe sahiptir.
Bu, varsayılan olarak `https://dc.services.visualstudio.com/api/profiles/{0}/appId` olarak ayarlanmıştır.
Bu yapılandırma için bir ara sunucu yapılandırmanız gerekiyorsa, temel adresi ve "/api/Profiles/{0}/AppID" dahil olmak üzere proxy 'yi kullanmanızı öneririz. ' @No__t_0 ' ın, Izleme anahtarı ile istek başına çalışma zamanında yer aldığı unutulmamalıdır.

#### <a name="example-configuration-via-applicationinsightsconfig"></a>ApplicationInsights. config aracılığıyla örnek yapılandırma:
```xml
<ApplicationInsights>
    ...
    <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
        <ProfileQueryEndpoint>https://dc.services.visualstudio.com/api/profiles/{0}/appId</ProfileQueryEndpoint>
    </ApplicationIdProvider>
    ...
</ApplicationInsights>
```

#### <a name="example-configuration-via-code"></a>Kod aracılığıyla örnek yapılandırma:
```csharp
TelemetryConfiguration.Active.ApplicationIdProvider = new ApplicationInsightsApplicationIdProvider();
```

### <a name="dictionaryapplicationidprovider"></a>DictionaryApplicationIdProvider

Bu, yapılandırılmış Izleme anahtarınız/uygulama KIMLIĞI çiftlerine bağlı olan statik bir sağlayıcıdır.

Bu sınıf, bir sözlük < dize, uygulama KIMLIĞI çiftlerine yönelik Izleme anahtarının dize > olan bir özellik `Defined` sahiptir.

Bu sınıf, yapılandırmanızda bulunmayan bir Izleme anahtarı istendiğinde kullanılacak başka bir sağlayıcıyı yapılandırmak için kullanılabilen isteğe bağlı bir özellik `Next` sahiptir.

#### <a name="example-configuration-via-applicationinsightsconfig"></a>ApplicationInsights. config aracılığıyla örnek yapılandırma:
```xml
<ApplicationInsights>
    ...
    <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.DictionaryApplicationIdProvider, Microsoft.ApplicationInsights">
        <Defined>
            <Type key="InstrumentationKey_1" value="ApplicationId_1"/>
            <Type key="InstrumentationKey_2" value="ApplicationId_2"/>
        </Defined>
        <Next Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights" />
    </ApplicationIdProvider>
    ...
</ApplicationInsights>
```

#### <a name="example-configuration-via-code"></a>Kod aracılığıyla örnek yapılandırma:
```csharp
TelemetryConfiguration.Active.ApplicationIdProvider = new DictionaryApplicationIdProvider{
 Defined = new Dictionary<string, string>
    {
        {"InstrumentationKey_1", "ApplicationId_1"},
        {"InstrumentationKey_2", "ApplicationId_2"}
    }
};
```




## <a name="next-steps"></a>Sonraki adımlar
[API hakkında daha fazla bilgi edinin][api].

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[client]: ../../azure-monitor/app/javascript.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[exceptions]: ../../azure-monitor/app/asp-net-exceptions.md
[netlogs]: ../../azure-monitor/app/asp-net-trace-logs.md
[new]: ../../azure-monitor/app/create-new-resource.md 
[redfield]: ../../azure-monitor/app/monitor-performance-live-website-now.md
[start]: ../../azure-monitor/app/app-insights-overview.md
