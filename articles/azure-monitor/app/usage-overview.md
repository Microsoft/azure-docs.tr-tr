---
title: Azure Application Insights Kullanım Analizi | Microsoft docs
description: Kullanıcılarınıza ve uygulamalarınızla neler olduğunu anlayın.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/19/2019
ms.openlocfilehash: aa37717c5037294c2b5ec61f7815b007cbf74992
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73884819"
---
# <a name="usage-analysis-with-application-insights"></a>Application Insights ile kullanım analizi

Web veya mobil uygulamanızın hangi özellikleri en popüler? Kullanıcılarınız uygulamanızla hedeflerine ulaşsın mı? Belirli noktalarda kullanıma hazır olurlar ve daha sonra geri dönesin mi?  [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) kişilerin uygulamanızı nasıl kullandıkları hakkında güçlü Öngörüler elde etmenize yardımcı olur. Uygulamanızı her güncelleştirdiğinizde, kullanıcılar için ne kadar iyi çalışma olduğunu değerlendirebilirsiniz. Bu bilgi ile, sonraki geliştirme döngüleriniz hakkında veri odaklı kararlar verebilirsiniz.

## <a name="send-telemetry-from-your-app"></a>Uygulamanızdan telemetri gönderin

En iyi deneyim, hem App Server kodunuzda hem de Web sayfalarınızda Application Insights yüklenerek elde edilir. Uygulamanızın istemci ve sunucu bileşenleri, analiz için telemetri Azure portal geri gönderir.

1. **Sunucu kodu:** [ASP.net](../../azure-monitor/app/asp-net.md), [Azure](../../azure-monitor/app/app-insights-overview.md), [Java](../../azure-monitor/app/java-get-started.md), [Node. js](../../azure-monitor/app/nodejs.md)veya [diğer](../../azure-monitor/app/platforms.md) uygulamalar için uygun modülü yükler.

    * *Sunucu kodunu yüklemek istemiyor musunuz? Yalnızca [bir Azure Application Insights kaynağı oluşturun](../../azure-monitor/app/create-new-resource.md ).*

2. **Web sayfası kodu:** Kapatma ``</head>``önce Web sayfanıza aşağıdaki betiği ekleyin. İzleme anahtarını Application Insights kaynağınız için uygun değerle değiştirin:
    
    ```html
    <script type="text/javascript">
    var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t}(
    {
      instrumentationKey:"INSTRUMENTATION_KEY"
    }
    );window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
    </script>
    ```

    Web sitelerini izlemeye yönelik daha gelişmiş yapılandırma hakkında daha fazla bilgi edinmek için [JAVASCRIPT SDK başvuru makalesine](https://docs.microsoft.com/azure/azure-monitor/app/javascript)göz atın.

3. **Mobil uygulama kodu:** Uygulamanızdaki olayları toplamak için App Center SDK 'sını kullanın, ardından [Bu kılavuzu izleyerek](../../azure-monitor/learn/mobile-center-quickstart.md)bu olayların kopyalarını analiz için Application Insights gönderin.

4. **Telemetri al:** Projenizi birkaç dakika boyunca hata ayıklama modunda çalıştırın ve ardından Application Insights genel bakış dikey penceresinde sonuçları arayın.

    Uygulamanızın performansını izlemek için uygulamanızı yayımlayın ve kullanıcılarınızın uygulamanızda ne yaptığını öğrenin.

## <a name="include-user-and-session-id-in-your-telemetry"></a>Telemetrinize Kullanıcı ve oturum KIMLIĞINI dahil etme
Kullanıcılara zaman içinde izlemek için Application Insights bunları tanımlamak için bir yol gerekir. Olaylar Aracı, bir kullanıcı KIMLIĞI veya oturum KIMLIĞI gerektirmeyen tek kullanım aracıdır.

[Bu işlemi](https://docs.microsoft.com/azure/application-insights/app-insights-usage-send-user-context)kullanarak Kullanıcı ve oturum kimlikleri göndermeye başlayın.

## <a name="explore-usage-demographics-and-statistics"></a>Kullanım demografgrafiklerini ve istatistiklerini keşfet
Kullanıcılarınızın uygulamanızı ne zaman kullandığı, ne kadar ilgilendiğiniz ve hangi tarayıcıların kullandıkları, hangi tarayıcıların ve işletim sistemlerinin bulunduğu hakkında bilgi edinin. 

Kullanıcılar ve oturumlar, verilerinizi sayfalara veya özel olaylara göre filtreleyip konum, ortam ve sayfa gibi özelliklere göre segmentlere ayırabilir. Kendi filtrelerinizi de ekleyebilirsiniz.

![Kullanıcılar](./media/usage-overview/users.png)  

Veri kümesindeki ilginç desenler hakkında bilgi edinin.  

* **Kullanıcılar** raporu, seçtiğiniz zaman dönemlerinizde sayfalarınıza erişen benzersiz kullanıcıların sayısını sayar. Web Apps için kullanıcılar tanımlama bilgileri kullanılarak sayılır. Siteniz farklı tarayıcılarla veya istemci makinelerle erişiyorsa veya kendi tanımlama bilgilerini temizlerse, birden çok kez sayılır.
* **Oturumlar** raporu, sitenize erişen kullanıcı oturumlarının sayısını sayar. Oturum, bir kullanıcı tarafından, bir saatten daha uzun bir süredir işlem yapılmadan daha fazla süre sonunda sonlandırılmış bir etkinlik sürecinde.

[Kullanıcılar, oturumlar ve olaylar araçları hakkında daha fazla bilgi](usage-segmentation.md)  

## <a name="retention---how-many-users-come-back"></a>Bekletme-kaç Kullanıcı geri geliyor?

Saklama, kullanıcılarınızın, belirli bir zaman aralığı boyunca bazı iş eylemlerini gerçekleştiren kullanıcıların, uygulamalarını kullanmak için ne sıklıkta geri dönebileceğini anlamanıza yardımcı olur. 

- Kullanıcıların diğerlerinden daha fazla geri dönmesine neden olan belirli özellikleri anlayın 
- Gerçek kullanıcı verilerine göre hipotezleri formu 
- Üretim, ürününüzün bir sorun olup olmadığını belirleme 

![Bekletme](./media/usage-overview/retention.png) 

En üstteki bekletme denetimleri, saklama süresini hesaplamak için belirli olayları ve zaman aralığını tanımlamanızı sağlar. Ortadaki grafik, belirtilen zaman aralığına göre genel bekletme yüzdesinin görsel bir gösterimini sağlar. Alt kısımdaki grafik belirli bir dönemde tek tek bekletme 'yi temsil eder. Bu ayrıntı düzeyi, kullanıcılarınızın ne yaptığını ve daha ayrıntılı bir ayrıntı düzeyi üzerinde Kullanıcı döndürmeyi neyin etkileyebileceğini anlamanıza olanak tanır.  

[Bekletme aracı hakkında daha fazla bilgi](usage-retention.md)

## <a name="custom-business-events"></a>Özel iş olayları

Uygulamanızdaki kullanıcıların neler yaptığını net bir şekilde anlamak için, özel olayları günlüğe kaydetmek üzere kod satırları eklemek yararlıdır. Bu olaylar, belirli düğmeleri tıklatmak gibi ayrıntılı Kullanıcı eylemlerinden herhangi bir şeyi izleyerek satın alma veya oyun kazanma gibi daha önemli iş olaylarına sahip olabilir. 

Bazı durumlarda, sayfa görünümleri faydalı olayları temsil edebilir, ancak genel olarak doğru değildir. Bir Kullanıcı ürünü satın almadan bir ürün sayfasını açabilir. 

Belirli iş olayları sayesinde kullanıcılarınızın ilerlemesini sitenizde grafik olarak ekleyebilirsiniz. Farklı seçeneklere yönelik tercihlerini bulabilir ve nerede yaşanıyorsa veya zorluklara sahip olabilirler. Bu bilgi ile, geliştirme kapsamınızdaki öncelikler hakkında bilinçli kararlar verebilirsiniz.

Olaylar, uygulamanın istemci tarafından günlüğe kaydedilebilir:

```JavaScript

    appInsights.trackEvent("ExpandDetailTab", {DetailTab: tabName});
```

Ya da sunucu tarafında:

```csharp
    var tc = new Microsoft.ApplicationInsights.TelemetryClient();
    tc.TrackEvent("CreatedAccount", new Dictionary<string,string> {"AccountType":account.Type}, null);
    ...
    tc.TrackEvent("AddedItemToCart", new Dictionary<string,string> {"Item":item.Name}, null);
    ...
    tc.TrackEvent("CompletedPurchase");
```

Bu olaylara özellik değerleri iliştirebilirsiniz, böylece olayları portalda incelemenize sonra filtreleyebileceğiniz veya bölünebilmenizi sağlayabilirsiniz. Ayrıca, tek bir kullanıcının etkinliklerini izlemenize olanak sağlayan anonim kullanıcı KIMLIĞI gibi her bir olaya standart bir özellikler kümesi iliştirilir.

[Özel olaylar](../../azure-monitor/app/api-custom-events-metrics.md#trackevent) ve [Özellikler](../../azure-monitor/app/api-custom-events-metrics.md#properties)hakkında daha fazla bilgi edinin.

### <a name="slice-and-dice-events"></a>Dilim ve zar olayları

Kullanıcılar, oturumlar ve olaylar araçlarında, özel olayları Kullanıcı, olay adı ve özelliklere göre dilimleyerek ve zar aktarabilirsiniz.
![Kullanıcılar](./media/usage-overview/users.png)  
  
## <a name="design-the-telemetry-with-the-app"></a>Uygulamayla telemetri tasarlama

Uygulamanızın her bir özelliğini tasarlarken, kullanıcılarınızın başarısını nasıl ölçmenize dikkat edin. Kayıt yapmanız gereken iş olaylarını belirleyin ve bu olaylar için izleme çağrılarını, başlangıçtan itibaren uygulamanıza kodlayın.

## <a name="a--b-testing"></a>A | B testi
Bir özelliğin hangi çeşidinin daha başarılı olacağını bilmiyorsanız, her ikisini de serbest bırakın, her ikisini birden farklı kullanıcı için erişilebilir hale getirir. Her birinin başarısını ölçün ve ardından birleştirilmiş bir sürüme taşıyın.

Bu teknik için, uygulamanızın her bir sürümü tarafından gönderilen tüm telemetrisine ayrı özellik değerleri iliştirebilirsiniz. Bunu, etkin TelemetryContext özellikleri tanımlayarak yapabilirsiniz. Bu varsayılan özellikler, uygulamanın gönderdiği her telemetri iletisine (yalnızca özel mesajlarınızı değil, standart Telemetriyi) eklenir.

Application Insights portalında, farklı sürümleri karşılaştırmak için, bu verileri özellik değerlerine göre filtreleyin ve ayırın.

Bunu yapmak için [bir telemetri başlatıcısı ayarlayın](../../azure-monitor/app/api-filtering-sampling.md#addmodify-properties-itelemetryinitializer):

**ASP.NET uygulamaları**

```csharp
    // Telemetry initializer class
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize (ITelemetry telemetry)
        {
            telemetry.Properties["AppVersion"] = "v2.1";
        }
    }
```

Global.asax.cs gibi Web uygulaması başlatıcısında:

```csharp

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
         .Add(new MyTelemetryInitializer());
    }
```

**ASP.NET Core uygulamalar**

> [!NOTE]
> `ApplicationInsights.config` veya `TelemetryConfiguration.Active` kullanarak Başlatıcı ekleme ASP.NET Core uygulamaları için geçerli değildir. 

[ASP.NET Core](asp-net-core.md#adding-telemetryinitializers) uygulamalar için, aşağıda gösterildiği gibi, yeni bir `TelemetryInitializer` eklemek, bağımlılık ekleme kapsayıcısına eklenerek yapılır. Bu, `Startup.cs` sınıfınızın `ConfigureServices` yönteminde yapılır.

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```

Tüm yeni TelemetryClients, belirttiğiniz özellik değerini otomatik olarak ekler. Tek tek telemetri olayları varsayılan değerleri geçersiz kılabilir.

## <a name="next-steps"></a>Sonraki adımlar
   - [Kullanıcılar, Oturumlar, Etkinlikler](usage-segmentation.md)
   - [Huniler](usage-funnels.md)
   - [Bekletme](usage-retention.md)
   - [Kullanıcı Akışları](usage-flows.md)
   - [Çalışma kitapları](../../azure-monitor/app/usage-workbooks.md)
   - [Kullanıcı bağlamı Ekle](usage-send-user-context.md)
