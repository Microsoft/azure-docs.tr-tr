---
title: Azure Application Insights ile ilgili sorunları ve özel durumları tanılama
description: ASP.NET uygulamalarından gelen özel durumları, istek telemetriyle birlikte yakalayın.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 07/11/2019
ms.openlocfilehash: 926516075c7d43e6e800403a69ff3ab8f1233fcd
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105727000"
---
# <a name="diagnose-exceptions-in-your-web-apps-with-application-insights"></a>Application Insights ile web uygulamalarınızda özel durumları tanılama
Canlı Web uygulamanızdaki özel durumlar [Application Insights](./app-insights-overview.md)tarafından raporlanır. Nedenleri hızlı bir şekilde tanılamanıza olanak tanımak için hem istemci hem de sunucudaki başarısız istekleri özel durumlarla ve diğer olaylarla ilişkilendirebileceğinizi unutmayın.

## <a name="set-up-exception-reporting"></a>Özel durum raporlamayı ayarlama
* Sunucu uygulamanızdan bildirilen özel durumların olması için:
  * Azure Web Apps: [Application Insights uzantısını](./azure-web-apps.md) ekleyin
  * Azure VM ve Azure sanal makine ölçek kümesi IIS tarafından barındırılan uygulamalar: [uygulama Izleme uzantısını](./azure-vm-vmss-apps.md) ekleme
  * Uygulama kodunuza [APPLICATION INSIGHTS SDK](./asp-net.md) 'yı yükleyip
  * IIS Web sunucuları: [Application Insights aracıyı](./monitor-performance-live-website-now.md)Çalıştır; veya
  * Java Web Apps: [Java aracısını](./java-in-process-agent.md) etkinleştirme
* Tarayıcı özel durumlarını yakalamak için Web sayfalarınıza [JavaScript kod parçacığını](./javascript.md) yükler.
* Bazı uygulama çerçeveleri veya bazı ayarlarla daha fazla özel durum yakalamak için bazı ek adımlar gerçekleştirmeniz gerekir:
  * [Web formları](#web-forms)
  * [MVC](#mvc)
  * [Web API 1. *](#web-api-1x)
  * [Web API 2. *](#web-api-2x)
  * [WCF](#wcf)

  Bu makale, kod örneği perspektifinden .NET Framework uygulamalarına özel olarak odaklanılmıştır. .NET Framework için çalışan yöntemlerin bazıları .NET Core SDK artık kullanılmıyor. .NET Core uygulamanız varsa [.NET Core SDK belgelerine](./asp-net-core.md) bakın.

## <a name="diagnosing-exceptions-using-visual-studio"></a>Visual Studio kullanarak özel durumları tanılama
Hata ayıklamaya yardımcı olması için Visual Studio 'da uygulama çözümünü açın.

Uygulamanızı, sunucunuzda veya geliştirme makinenizde F5 kullanarak çalıştırın.

Visual Studio 'da Application Insights arama penceresini açın ve uygulamanızdan olayları görüntüleyecek şekilde ayarlayın. Hata ayıklaması yaparken, bunu yalnızca Application Insights düğmesine tıklayarak yapabilirsiniz.

![Projeye sağ tıklayın ve Application Insights, aç ' ı seçin.](./media/asp-net-exceptions/34.png)

Yalnızca özel durumları göstermek için raporu filtreleyebildiğinize dikkat edin.

*Özel durum gösterilmiyor mi? Bkz. [yakalama özel durumları](#exceptions).*

Yığın izlemesini göstermek için bir özel durum raporuna tıklayın.
İlgili kod dosyasını açmak için yığın izlemesinde bir satır başvurusuna tıklayın.

Kodda, CodeLens 'in özel durumlarla ilgili verileri gösterdiğine dikkat edin:

![Özel durumların CodeLens bildirimi.](./media/asp-net-exceptions/35.png)

## <a name="diagnosing-failures-using-the-azure-portal"></a>Azure portal kullanarak sorunları tanılama
Application Insights, izlenen uygulamalarınızda oluşan sorunları tanılamanıza yardımcı olması için seçkin bir APM deneyimiyle birlikte gelir. Başlamak için araştır bölümünde yer alan Application Insights kaynak menüsündeki sorunlar seçeneğine tıklayın.
İstekleriniz için hata oranı eğilimlerini, kaç tane başarısız olduğunu ve kaç kullanıcının etkilendiğini gösteren bir tam ekran görünümü görmeniz gerekir. Sağ tarafta, en iyi üç yanıt kodu, en çok üç özel durum türü ve başarısız olan en çok bağımlılık türü dahil olmak üzere, seçilen başarısız işleme özgü en yararlı dağıtımlardan bazılarını görürsünüz.

![Başarısız önceliklendirme görünümü (işlemler sekmesi)](./media/asp-net-exceptions/failures0719.png)

Tek bir tıklama içinde, bu işlem alt kümelerinin her biri için temsilci örneklerini gözden geçirebilirsiniz. Özellikle, özel durumları tanılamak için, belirli bir özel durumun sayısına tıklayarak aşağıdakiler gibi uçtan uca işlem ayrıntıları sekmesine tıklayabilirsiniz:

![Uçtan uca işlem ayrıntıları sekmesi](./media/asp-net-exceptions/end-to-end.png)

**Alternatif olarak,** belirli bir başarısız işlemin özel durumlarını aramak yerine, en üstteki özel durumlar sekmesine geçerek özel durumların genel görünümünden başlayabilirsiniz. Burada, izlenen uygulamanız için toplanan tüm özel durumları görebilirsiniz.

*Özel durum gösterilmiyor mi? Bkz. [yakalama özel durumları](#exceptions).*


## <a name="custom-tracing-and-log-data"></a>Özel izleme ve günlük verileri
Uygulamanıza özel tanılama verileri almak için kendi telemetri verilerinizi göndermek üzere kod ekleyebilirsiniz. Bu, istek, sayfa görünümü ve diğer otomatik olarak toplanan verilerin yanı sıra tanılama araması içinde görüntülenir.

Birkaç seçeneğiniz vardır:

* [Trackingkevent ()](./api-custom-events-metrics.md#trackevent) genellikle kullanım düzenlerini izlemek için kullanılır, ancak gönderdiği veriler, tanılama aramasında özel olaylar altında da görünür. Olaylar adlandırılır ve [Tanılama aramalarınızı filtreleyebileceğiniz](./diagnostic-search.md)dize özelliklerini ve sayısal ölçümleri taşıyabilir.
* [Tracktrace ()](./api-custom-events-metrics.md#tracktrace) , gönderi bilgileri gibi daha uzun veriler göndermenizi sağlar.
* [Trackexception ()](#exceptions) yığın izlemeleri gönderir. [Özel durumlar hakkında daha fazla](#exceptions)bilgi.
* Log4Net veya NLog gibi bir günlük çerçevesini zaten kullanıyorsanız, [Bu günlükleri yakalayabilir](asp-net-trace-logs.md) ve istek ve özel durum verilerinin yanı sıra tanılama araması içinde görebilirsiniz.

Bu olayları görmek için, sol menüden [Ara](./diagnostic-search.md) ' yı açın, açılan menü **olay türlerini** seçin ve ardından özel olay, izleme veya özel durum ' u seçin.

![Detaylandırma](./media/asp-net-exceptions/customevents.png)

> [!NOTE]
> Uygulamanız çok sayıda telemetri oluşturuyorsa, uyarlamalı örnekleme modülü olayların yalnızca bir temsilci fraksiyonunu göndererek portala gönderilen hacmi otomatik olarak azaltır. Aynı işlemin parçası olan olaylar, ilgili olaylar arasında gezinebilmeniz için Grup olarak seçilecek veya seçimden kaldırılacak. [Örnekleme hakkında bilgi edinin.](./sampling.md)
>
>

### <a name="how-to-see-request-post-data"></a>İstek SONRASı verileri nasıl görüntülenir
İstek ayrıntıları, uygulamanıza gönderilen verileri bir POST çağrısıyla içermez. Bu verilerin bildirilmesi için:

* [SDK 'yı](./asp-net.md) uygulama projenize yükler.
* [Microsoft. ApplicationInsights. TrackTrace ()](./api-custom-events-metrics.md#tracktrace)öğesini çağırmak için uygulamanıza kod ekleyin. POST verilerini ileti parametresine gönderin. İzin verilen boyut sınırı vardır. bu nedenle yalnızca gerekli verileri göndermeye çalışırsınız.
* Başarısız bir isteği araştırdığınızda, ilişkili izlemeleri bulun.

## <a name="capturing-exceptions-and-related-diagnostic-data"></a><a name="exceptions"></a> Özel durumları ve ilgili tanılama verilerini yakalama
İlk olarak, portalda hatalara neden olan tüm özel durumların portalda görmezsiniz. Herhangi bir tarayıcı özel durumu görürsünüz (Web sayfalarınızda [JavaScript SDK 'sını](./javascript.md) kullanıyorsanız). Ancak, çoğu sunucu özel durumu IIS tarafından yakalanır ve bunları görmek için bir kod yazmanız gerekir.

Seçenekleriniz şunlardır:

* Özel durumları raporlamak için özel durum işleyicilerinde kod ekleyerek **özel durumları açıkça günlüğe kaydedin** .
* ASP.NET çerçevesini yapılandırarak **özel durumları otomatik olarak yakalayın** . Gerekli eklemeler farklı çerçeve türleri için farklıdır.

## <a name="reporting-exceptions-explicitly"></a>Özel durumları açıkça raporlama
En kolay yol, bir özel durum işleyicisinde TrackException () çağrısı kullanmaktır.

```javascript
    try
    { ...
    }
    catch (ex)
    {
      appInsights.trackException(ex, "handler loc",
        {Game: currentGame.Name,
         State: currentGame.State.ToString()});
    }
```

```csharp
    var telemetry = new TelemetryClient();
    ...
    try
    { ...
    }
    catch (Exception ex)
    {
       // Set up some properties:
       var properties = new Dictionary <string, string>
         {{"Game", currentGame.Name}};

       var measurements = new Dictionary <string, double>
         {{"Users", currentGame.Users.Count}};

       // Send the exception telemetry:
       telemetry.TrackException(ex, properties, measurements);
    }
```

```VB
    Dim telemetry = New TelemetryClient
    ...
    Try
      ...
    Catch ex as Exception
      ' Set up some properties:
      Dim properties = New Dictionary (Of String, String)
      properties.Add("Game", currentGame.Name)

      Dim measurements = New Dictionary (Of String, Double)
      measurements.Add("Users", currentGame.Users.Count)

      ' Send the exception telemetry:
      telemetry.TrackException(ex, properties, measurements)
    End Try
```

Özellikler ve ölçümler parametreleri isteğe bağlıdır, ancak [filtreleme ve](./diagnostic-search.md) ek bilgi ekleme için faydalıdır. Örneğin, birkaç oyun çalıştırabileceğinizi bir uygulamanız varsa, belirli bir oyunla ilgili tüm özel durum raporlarını bulabilirsiniz. Her bir sözlüğe dilediğiniz kadar öğe ekleyebilirsiniz.

## <a name="browser-exceptions"></a>Tarayıcı özel durumları
Çoğu tarayıcı özel durumu raporlanır.

Web sayfanız, içerik teslim ağlarından veya diğer etki alanlarından betik dosyaları içeriyorsa, betik etiketinizin özniteliğe sahip olduğundan ```crossorigin="anonymous"``` ve sunucunun [CORS üst bilgilerini](https://enable-cors.org/)gönderdiğinden emin olun. Bu, bu kaynaklardan işlenmemiş JavaScript özel durumları için yığın izlemesi ve ayrıntı almanızı sağlar.

## <a name="reuse-your-telemetry-client"></a>Telemetri istemcinizi yeniden kullanma

> [!NOTE]
> TelemetryClient bir kez örneğinin oluşturulması ve bir uygulamanın ömrü boyunca yeniden kullanılması önerilir.

Aşağıda, TelemetryClient kullanarak doğru bir örnek verilmiştir.

```csharp
public class GoodController : ApiController
{
    // OK
    private static readonly TelemetryClient telemetryClient;

    static GoodController()
    {
        telemetryClient = new TelemetryClient();
    }
}
```


## <a name="web-forms"></a>Web formları
Web Forms için HTTP modülü, CustomErrors ile yapılandırılmış yeniden yönlendirme olmadığında özel durumları toplayabilecektir.

Ancak etkin yeniden yönlendirmelere sahipseniz, Global. asax. cs içindeki Application_Error işlevine aşağıdaki satırları ekleyin. (Henüz yoksa bir Global. asax dosyası ekleyin.)

```csharp
    void Application_Error(object sender, EventArgs e)
    {
      if (HttpContext.Current.IsCustomErrorEnabled && Server.GetLastError () != null)
      {
         var ai = new TelemetryClient(); // or re-use an existing instance

         ai.TrackException(Server.GetLastError());
      }
    }
```
## <a name="mvc"></a>MVC
Application Insights Web SDK 2,6 (Beta3 ve üzeri) sürümünden itibaren, Application Insights MVC 5 + denetleyiciler yöntemlerinde otomatik olarak oluşturulan işlenmeyen özel durumları toplar. Bu tür özel durumları izlemek için daha önce özel bir işleyici eklediyseniz (aşağıdaki örneklerde açıklandığı gibi), özel durumların çift izlemesini engellemek için bunu kaldırabilirsiniz.

Özel durum filtrelerinden işleyememesi gereken birkaç durum vardır. Örnek:

* Denetleyici oluşturucularından gelen özel durumlar.
* İleti işleyicilerinden oluşturulan özel durumlar.
* Yönlendirme sırasında oluşturulan özel durumlar.
* Yanıt içeriği serileştirmesi sırasında oluşturulan özel durumlar.
* Uygulama başlatma sırasında oluşturulan özel durum.
* Arka plan görevlerinde oluşturulan özel durum.

Uygulama tarafından *işlenen* tüm özel durumların yine de el ile izlenmesi gerekir.
Denetleyicilerden kaynaklanan işlenmemiş özel durumlar genellikle 500 "Iç sunucu hatası" yanıtı ile sonuçlanır. Bu tür bir yanıt işlenmiş özel durumun sonucu olarak el ile oluşturulursa (veya hiç özel durum yoksa), 500 ile ilgili istek telemetrisi içinde izlenir `ResultCode` , ancak APPLICATION INSIGHTS SDK karşılık gelen özel durumu izleyemiyor.

### <a name="prior-versions-support"></a>Önceki sürümler desteği
Web SDK 2,5 (ve öncesi) Application Insights MVC 4 (ve öncesi) kullanıyorsanız, özel durumları izlemek için aşağıdaki örneklere bakın.

[CustomErrors](/previous-versions/dotnet/netframework-4.0/h0hfz6fc(v=vs.100)) yapılandırması Ise `Off` , [http modülünün](/previous-versions/dotnet/netframework-3.0/ms178468(v=vs.85)) toplaması için özel durumlar kullanılabilir olacaktır. Ancak, `RemoteOnly` (varsayılan) veya ise, `On` özel durum temizlenir ve Application Insights otomatik olarak toplanacaktır. [System. Web. Mvc. HandleErrorAttribute sınıfını](/dotnet/api/system.web.mvc.handleerrorattribute)geçersiz kılarak ve AŞAĞıDAKI farklı MVC sürümleri ([GitHub kaynağı](https://github.com/AppInsightsSamples/Mvc2UnhandledExceptions/blob/master/MVC2App/Controllers/AiHandleErrorAttribute.cs)) için gösterildiği gibi geçersiz kılınan sınıfı uygulayarak bu hatayı çözebilirsiniz:

```csharp
    using System;
    using System.Web.Mvc;
    using Microsoft.ApplicationInsights;

    namespace MVC2App.Controllers
    {
      [AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, Inherited = true, AllowMultiple = true)]
      public class AiHandleErrorAttribute : HandleErrorAttribute
      {
        public override void OnException(ExceptionContext filterContext)
        {
            if (filterContext != null && filterContext.HttpContext != null && filterContext.Exception != null)
            {
                //If customError is Off, then AI HTTPModule will report the exception
                if (filterContext.HttpContext.IsCustomErrorEnabled)
                {   //or reuse instance (recommended!). see note above
                    var ai = new TelemetryClient();
                    ai.TrackException(filterContext.Exception);
                }
            }
            base.OnException(filterContext);
        }
      }
    }
```

#### <a name="mvc-2"></a>MVC 2
HandleError özniteliğini denetleyicilerinizdeki yeni öznitelikle değiştirin.

```csharp
    namespace MVC2App.Controllers
    {
        [AiHandleError]
        public class HomeController : Controller
        {
    ...
```

[Örnek](https://github.com/AppInsightsSamples/Mvc2UnhandledExceptions)

#### <a name="mvc-3"></a>MVC 3
`AiHandleErrorAttribute`Global. asax. cs dosyasında genel filtre olarak Kaydet:

```csharp
    public class MyMvcApplication : System.Web.HttpApplication
    {
      public static void RegisterGlobalFilters(GlobalFilterCollection filters)
      {
         filters.Add(new AiHandleErrorAttribute());
      }
     ...
```

[Örnek](https://github.com/AppInsightsSamples/Mvc3UnhandledExceptionTelemetry)

#### <a name="mvc-4-mvc5"></a>MVC 4, MVC5
AiHandleErrorAttribute 'i FilterConfig. cs içinde genel bir filtre olarak Kaydet:

```csharp
    public class FilterConfig
    {
      public static void RegisterGlobalFilters(GlobalFilterCollection filters)
      {
        // Default replaced with the override to track unhandled exceptions
        filters.Add(new AiHandleErrorAttribute());
      }
    }
```

[Örnek](https://github.com/AppInsightsSamples/Mvc5UnhandledExceptionTelemetry)

## <a name="web-api"></a>Web API
Application Insights Web SDK 2,6 (Beta3 ve üzeri) sürümünden başlayarak, Application Insights, denetleyici yöntemlerinde otomatik olarak oluşturulan işlenmeyen özel durumları, WebAPI 2 + için otomatik olarak toplar. Bu tür özel durumları izlemek için daha önce özel bir işleyici eklediyseniz (aşağıdaki örneklerde açıklandığı gibi), özel durumların çift izlemesini engellemek için bunu kaldırabilirsiniz.

Özel durum filtrelerinden işleyememesi gereken birkaç durum vardır. Örnek:

* Denetleyici oluşturucularından gelen özel durumlar.
* İleti işleyicilerinden oluşturulan özel durumlar.
* Yönlendirme sırasında oluşturulan özel durumlar.
* Yanıt içeriği serileştirmesi sırasında oluşturulan özel durumlar.
* Uygulama başlatma sırasında oluşturulan özel durum.
* Arka plan görevlerinde oluşturulan özel durum.

Uygulama tarafından *işlenen* tüm özel durumların yine de el ile izlenmesi gerekir.
Denetleyicilerden kaynaklanan işlenmemiş özel durumlar genellikle 500 "Iç sunucu hatası" yanıtı ile sonuçlanır. Bu tür bir yanıt işlenmiş özel durumun sonucu olarak el ile oluşturulursa (veya hiç özel durum yoksa), 500 ile ilgili bir istek telemetriyle izlenir `ResultCode` , ancak APPLICATION INSIGHTS SDK karşılık gelen özel durumu izleyemiyor.

### <a name="prior-versions-support"></a>Önceki sürümler desteği
Application Insights Web SDK 2,5 (ve öncesi) için WebAPI 1 (ve öncesi) kullanıyorsanız, özel durumları izlemek için aşağıdaki örneklere bakın.

#### <a name="web-api-1x"></a>Web API 1. x
System. Web. http. Filters. ExceptionFilterAttribute öğesini geçersiz kılın:

```csharp
    using System.Web.Http.Filters;
    using Microsoft.ApplicationInsights;

    namespace WebAPI.App_Start
    {
      public class AiExceptionFilterAttribute : ExceptionFilterAttribute
      {
        public override void OnException(HttpActionExecutedContext actionExecutedContext)
        {
            if (actionExecutedContext != null && actionExecutedContext.Exception != null)
            {  //or reuse instance (recommended!). see note above
                var ai = new TelemetryClient();
                ai.TrackException(actionExecutedContext.Exception);
            }
            base.OnException(actionExecutedContext);
        }
      }
    }
```

Bu geçersiz kılınan özniteliği belirli denetleyicilere ekleyebilir veya WebApiConfig sınıfındaki genel filtre yapılandırmasına ekleyebilirsiniz:

```csharp
    using System.Web.Http;
    using WebApi1.x.App_Start;

    namespace WebApi1.x
    {
      public static class WebApiConfig
      {
        public static void Register(HttpConfiguration config)
        {
            config.Routes.MapHttpRoute(name: "DefaultApi", routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional });
            ...
            config.EnableSystemDiagnosticsTracing();

            // Capture exceptions for Application Insights:
            config.Filters.Add(new AiExceptionFilterAttribute());
        }
      }
    }
```

[Örnek](https://github.com/AppInsightsSamples/WebApi_1.x_UnhandledExceptions)

#### <a name="web-api-2x"></a>Web API 2. x
Iexceptiongünlükçü uygulaması ekleme:

```csharp
    using System.Web.Http.ExceptionHandling;
    using Microsoft.ApplicationInsights;

    namespace ProductsAppPureWebAPI.App_Start
    {
      public class AiExceptionLogger : ExceptionLogger
      {
        public override void Log(ExceptionLoggerContext context)
        {
            if (context !=null && context.Exception != null)
            {//or reuse instance (recommended!). see note above
                var ai = new TelemetryClient();
                ai.TrackException(context.Exception);
            }
            base.Log(context);
        }
      }
    }
```

Bunu WebApiConfig içindeki hizmetlere ekleyin:

```csharp
    using System.Web.Http;
    using System.Web.Http.ExceptionHandling;
    using ProductsAppPureWebAPI.App_Start;

    namespace WebApi2WithMVC
    {
      public static class WebApiConfig
      {
        public static void Register(HttpConfiguration config)
        {
            // Web API configuration and services

            // Web API routes
            config.MapHttpAttributeRoutes();

            config.Routes.MapHttpRoute(
                name: "DefaultApi",
                routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional }
            );
            config.Services.Add(typeof(IExceptionLogger), new AiExceptionLogger());
        }
      }
     }
```

[Örnek](https://github.com/AppInsightsSamples/WebApi_2.x_UnhandledExceptions)

Alternatifler olarak şunları yapabilirsiniz:

1. Tek ExceptionHandler öğesini bir ıexceptionhandler özel uygulamasıyla değiştirin. Bu yalnızca çerçeve hangi yanıt iletisini gönderileceğini seçebilmeye devam ediyorsa çağrılır (örneğin, bağlantı iptal edildiğinde)
2. Özel durum filtreleri (yukarıdaki Web API 1. x denetleyicilerinin bölümünde açıklandığı gibi)-tüm durumlarda çağrılmaz.

## <a name="wcf"></a>WCF
Özniteliğini genişleten ve IErrorHandler ve IServiceProvider 'ı uygulayan bir sınıf ekleyin.

```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.ServiceModel.Description;
    using System.ServiceModel.Dispatcher;
    using System.Web;
    using Microsoft.ApplicationInsights;

    namespace WcfService4.ErrorHandling
    {
      public class AiLogExceptionAttribute : Attribute, IErrorHandler, IServiceBehavior
      {
        public void AddBindingParameters(ServiceDescription serviceDescription,
            System.ServiceModel.ServiceHostBase serviceHostBase,
            System.Collections.ObjectModel.Collection<ServiceEndpoint> endpoints,
            System.ServiceModel.Channels.BindingParameterCollection bindingParameters)
        {
        }

        public void ApplyDispatchBehavior(ServiceDescription serviceDescription,
            System.ServiceModel.ServiceHostBase serviceHostBase)
        {
            foreach (ChannelDispatcher disp in serviceHostBase.ChannelDispatchers)
            {
                disp.ErrorHandlers.Add(this);
            }
        }

        public void Validate(ServiceDescription serviceDescription,
            System.ServiceModel.ServiceHostBase serviceHostBase)
        {
        }

        bool IErrorHandler.HandleError(Exception error)
        {//or reuse instance (recommended!). see note above
            var ai = new TelemetryClient();

            ai.TrackException(error);
            return false;
        }

        void IErrorHandler.ProvideFault(Exception error,
            System.ServiceModel.Channels.MessageVersion version,
            ref System.ServiceModel.Channels.Message fault)
        {
        }
      }
    }

Add the attribute to the service implementations:

    namespace WcfService4
    {
        [AiLogException]
        public class Service1 : IService1
        {
         ...
```

[Örnek](https://github.com/AppInsightsSamples/WCFUnhandledExceptions)

## <a name="exception-performance-counters"></a>Özel durum performans sayaçları
Sunucunuza [Application Insights aracısını yüklediyseniz](./monitor-performance-live-website-now.md) , .NET tarafından ölçülen özel durum hızının bir grafiğini alabilirsiniz. Bu hem işlenmiş hem de işlenmemiş .NET özel durumlarını içerir.

Ölçüm Gezgini sekmesini açın, yeni bir grafik ekleyin ve performans sayaçları altında listelenen **özel durum oranı**' nı seçin.

.NET Framework, bir aralıktaki özel durumların sayısını sayarak ve aralığın uzunluğuna bölerek hızı hesaplar.

Bu, Application Insights Portal sayımı TrackException raporları tarafından hesaplanan ' Exceptions ' sayımından farklıdır. Örnekleme aralıkları farklıdır ve SDK, işlenmiş ve işlenmemiş özel durumlar için TrackException raporları göndermez.

## <a name="next-steps"></a>Sonraki adımlar
* [REST, SQL ve diğer bağımlılıklara yapılan çağrıları izleme](./asp-net-dependencies.md)
* [Sayfa yükleme sürelerini, tarayıcı özel durumlarını ve AJAX çağrılarını izleyin](./javascript.md)
* [Performans sayaçlarını izleme](./performance-counters.md)

