---
title: Azure Application Insights Kullanım Analizi | Microsoft docs
description: Kullanıcılarınıza ve uygulamalarınızla neler olduğunu anlayın.
ms.topic: conceptual
ms.date: 03/25/2019
ms.openlocfilehash: d9de1e10363f2100b9dfe557dc12e0be951ce6b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "102489047"
---
# <a name="usage-analysis-with-application-insights"></a>Application Insights ile kullanım analizi

Web veya mobil uygulamanızın hangi özellikleri en popüler? Kullanıcılarınız uygulamanızla hedeflerine ulaşsın mı? Belirli noktalarda kullanıma hazır olurlar ve daha sonra geri dönesin mi?  [Azure Application Insights](./app-insights-overview.md) kişilerin uygulamanızı nasıl kullandıkları hakkında güçlü Öngörüler elde etmenize yardımcı olur. Uygulamanızı her güncelleştirdiğinizde, kullanıcılar için ne kadar iyi çalışma olduğunu değerlendirebilirsiniz. Bu bilgi ile, sonraki geliştirme döngüleriniz hakkında veri odaklı kararlar verebilirsiniz.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4Cijb]

## <a name="send-telemetry-from-your-app"></a>Uygulamanızdan telemetri gönderin

En iyi deneyim, hem App Server kodunuzda hem de Web sayfalarınızda Application Insights yüklenerek elde edilir. Uygulamanızın istemci ve sunucu bileşenleri, analiz için telemetri Azure portal geri gönderir.

1. **Sunucu kodu:** [ASP.net](./asp-net.md), [Azure](./app-insights-overview.md), [Java](./java-get-started.md), [Node.js](./nodejs.md)veya [başka](./platforms.md) bir uygulamanız için uygun modülü yükler.

    * *Sunucu kodunu yüklemek istemiyor musunuz? Yalnızca [bir Azure Application Insights kaynağı oluşturun](./create-new-resource.md).*

2. **Web sayfası kodu:** Kapatmadan önce Web sayfanıza aşağıdaki betiği ekleyin ``</head>`` . İzleme anahtarını Application Insights kaynağınız için uygun değerle değiştirin:
    
    Geçerli kod parçacığı (aşağıda listelenmiştir) "5" sürümüdür, sürüm kod parçacığında ZF: "#" olarak kodlanır ve [geçerli sürüm GitHub 'da da kullanılabilir](https://go.microsoft.com/fwlink/?linkid=2156318).

    ```html
    <script type="text/javascript">
    !function(T,l,y){var S=T.location,k="script",D="instrumentationKey",C="ingestionendpoint",I="disableExceptionTracking",E="ai.device.",b="toLowerCase",w="crossOrigin",N="POST",e="appInsightsSDK",t=y.name||"appInsights";(y.name||T[e])&&(T[e]=t);var n=T[t]||function(d){var g=!1,f=!1,m={initialize:!0,queue:[],sv:"5",version:2,config:d};function v(e,t){var n={},a="Browser";return n[E+"id"]=a[b](),n[E+"type"]=a,n["ai.operation.name"]=S&&S.pathname||"_unknown_",n["ai.internal.sdkVersion"]="javascript:snippet_"+(m.sv||m.version),{time:function(){var e=new Date;function t(e){var t=""+e;return 1===t.length&&(t="0"+t),t}return e.getUTCFullYear()+"-"+t(1+e.getUTCMonth())+"-"+t(e.getUTCDate())+"T"+t(e.getUTCHours())+":"+t(e.getUTCMinutes())+":"+t(e.getUTCSeconds())+"."+((e.getUTCMilliseconds()/1e3).toFixed(3)+"").slice(2,5)+"Z"}(),iKey:e,name:"Microsoft.ApplicationInsights."+e.replace(/-/g,"")+"."+t,sampleRate:100,tags:n,data:{baseData:{ver:2}}}}var h=d.url||y.src;if(h){function a(e){var t,n,a,i,r,o,s,c,u,p,l;g=!0,m.queue=[],f||(f=!0,t=h,s=function(){var e={},t=d.connectionString;if(t)for(var n=t.split(";"),a=0;a<n.length;a++){var i=n[a].split("=");2===i.length&&(e[i[0][b]()]=i[1])}if(!e[C]){var r=e.endpointsuffix,o=r?e.location:null;e[C]="https://"+(o?o+".":"")+"dc."+(r||"services.visualstudio.com")}return e}(),c=s[D]||d[D]||"",u=s[C],p=u?u+"/v2/track":d.endpointUrl,(l=[]).push((n="SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details)",a=t,i=p,(o=(r=v(c,"Exception")).data).baseType="ExceptionData",o.baseData.exceptions=[{typeName:"SDKLoadFailed",message:n.replace(/\./g,"-"),hasFullStack:!1,stack:n+"\nSnippet failed to load ["+a+"] -- Telemetry is disabled\nHelp Link: https://go.microsoft.com/fwlink/?linkid=2128109\nHost: "+(S&&S.pathname||"_unknown_")+"\nEndpoint: "+i,parsedStack:[]}],r)),l.push(function(e,t,n,a){var i=v(c,"Message"),r=i.data;r.baseType="MessageData";var o=r.baseData;return o.message='AI (Internal): 99 message:"'+("SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details) ("+n+")").replace(/\"/g,"")+'"',o.properties={endpoint:a},i}(0,0,t,p)),function(e,t){if(JSON){var n=T.fetch;if(n&&!y.useXhr)n(t,{method:N,body:JSON.stringify(e),mode:"cors"});else if(XMLHttpRequest){var a=new XMLHttpRequest;a.open(N,t),a.setRequestHeader("Content-type","application/json"),a.send(JSON.stringify(e))}}}(l,p))}function i(e,t){f||setTimeout(function(){!t&&m.core||a()},500)}var e=function(){var n=l.createElement(k);n.src=h;var e=y[w];return!e&&""!==e||"undefined"==n[w]||(n[w]=e),n.onload=i,n.onerror=a,n.onreadystatechange=function(e,t){"loaded"!==n.readyState&&"complete"!==n.readyState||i(0,t)},n}();y.ld<0?l.getElementsByTagName("head")[0].appendChild(e):setTimeout(function(){l.getElementsByTagName(k)[0].parentNode.appendChild(e)},y.ld||0)}try{m.cookie=l.cookie}catch(p){}function t(e){for(;e.length;)!function(t){m[t]=function(){var e=arguments;g||m.queue.push(function(){m[t].apply(m,e)})}}(e.pop())}var n="track",r="TrackPage",o="TrackEvent";t([n+"Event",n+"PageView",n+"Exception",n+"Trace",n+"DependencyData",n+"Metric",n+"PageViewPerformance","start"+r,"stop"+r,"start"+o,"stop"+o,"addTelemetryInitializer","setAuthenticatedUserContext","clearAuthenticatedUserContext","flush"]),m.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4};var s=(d.extensionConfig||{}).ApplicationInsightsAnalytics||{};if(!0!==d[I]&&!0!==s[I]){var c="onerror";t(["_"+c]);var u=T[c];T[c]=function(e,t,n,a,i){var r=u&&u(e,t,n,a,i);return!0!==r&&m["_"+c]({message:e,url:t,lineNumber:n,columnNumber:a,error:i}),r},d.autoExceptionInstrumented=!0}return m}(y.cfg);function a(){y.onInit&&y.onInit(n)}(T[t]=n).queue&&0===n.queue.length?(n.queue.push(a),n.trackPageView({})):a()}(window,document,{
    src: "https://js.monitor.azure.com/scripts/b/ai.2.min.js", // The SDK URL Source
    // name: "appInsights", // Global SDK Instance name defaults to "appInsights" when not supplied
    // ld: 0, // Defines the load delay (in ms) before attempting to load the sdk. -1 = block page load and add to head. (default) = 0ms load after timeout,
    // useXhr: 1, // Use XHR instead of fetch to report failures (if available),
    crossOrigin: "anonymous", // When supplied this will add the provided value as the cross origin attribute on the script tag
    // onInit: null, // Once the application insights instance has loaded and initialized this callback function will be called with 1 argument -- the sdk instance (DO NOT ADD anything to the sdk.queue -- As they won't get called)
    cfg: { // Application Insights Configuration
      instrumentationKey:"INSTRUMENTATION_KEY"
    }});
    </script>
    ```

    Web sitelerini izlemeye yönelik daha gelişmiş yapılandırma hakkında daha fazla bilgi edinmek için [JAVASCRIPT SDK başvuru makalesine](./javascript.md)göz atın.

3. **Mobil uygulama kodu:** Uygulamanızdaki olayları toplamak için App Center SDK 'sını kullanın, ardından [Bu kılavuzu izleyerek](../app/mobile-center-quickstart.md)bu olayların kopyalarını analiz için Application Insights gönderin.

4. **Telemetri al:** Projenizi birkaç dakika boyunca hata ayıklama modunda çalıştırın ve ardından Application Insights genel bakış dikey penceresinde sonuçları arayın.

    Uygulamanızın performansını izlemek için uygulamanızı yayımlayın ve kullanıcılarınızın uygulamanızda ne yaptığını öğrenin.

## <a name="explore-usage-demographics-and-statistics"></a>Kullanım demografgrafiklerini ve istatistiklerini keşfet
Kullanıcılarınızın uygulamanızı ne zaman kullandığı, ne kadar ilgilendiğiniz ve hangi tarayıcıların kullandıkları, hangi tarayıcıların ve işletim sistemlerinin bulunduğu hakkında bilgi edinin. 

Kullanıcılar ve oturumlar, verilerinizi sayfalara veya özel olaylara göre filtreleyip konum, ortam ve sayfa gibi özelliklere göre segmentlere ayırabilir. Kendi filtrelerinizi de ekleyebilirsiniz.

![Ekran yakalama, kurgusal bir şirket için kullanıcılara genel bakış sayfasını gösterir.](./media/usage-overview/users.png)  

Veri kümesindeki ilginç desenler hakkında bilgi edinin.  

* **Kullanıcılar** raporu, seçtiğiniz zaman dönemlerinizde sayfalarınıza erişen benzersiz kullanıcıların sayısını sayar. Web Apps için kullanıcılar tanımlama bilgileri kullanılarak sayılır. Siteniz farklı tarayıcılarla veya istemci makinelerle erişiyorsa veya kendi tanımlama bilgilerini temizlerse, birden çok kez sayılır.
* **Oturumlar** raporu, sitenize erişen kullanıcı oturumlarının sayısını sayar. Oturum, bir kullanıcı tarafından, bir saatten daha uzun bir süredir işlem yapılmadan daha fazla süre sonunda sonlandırılmış bir etkinlik sürecinde.

[Kullanıcılar, oturumlar ve olaylar araçları hakkında daha fazla bilgi](usage-segmentation.md)  

## <a name="retention---how-many-users-come-back"></a>Bekletme-kaç Kullanıcı geri geliyor?

Saklama, kullanıcılarınızın, belirli bir zaman aralığı boyunca bazı iş eylemlerini gerçekleştiren kullanıcıların, uygulamalarını kullanmak için ne sıklıkta geri dönebileceğini anlamanıza yardımcı olur. 

- Kullanıcıların diğerlerinden daha fazla geri dönmesine neden olan belirli özellikleri anlayın 
- Gerçek kullanıcı verilerine göre hipotezleri formu 
- Üretim, ürününüzün bir sorun olup olmadığını belirleme 

![Ekran yakalama, kullanıcıların uygulamalarını kullanmak için ne sıklıkta geri döndürdüğü hakkında bilgi görüntüleyen bekletme genel bakış sayfasını gösterir.](./media/usage-overview/retention.png) 

En üstteki bekletme denetimleri, saklama süresini hesaplamak için belirli olayları ve zaman aralığını tanımlamanızı sağlar. Ortadaki grafik, belirtilen zaman aralığına göre genel bekletme yüzdesinin görsel bir gösterimini sağlar. Alt kısımdaki grafik belirli bir dönemde tek tek bekletme 'yi temsil eder. Bu ayrıntı düzeyi, kullanıcılarınızın ne yaptığını ve daha ayrıntılı bir ayrıntı düzeyi üzerinde Kullanıcı döndürmeyi neyin etkileyebileceğini anlamanıza olanak tanır.  

[Bekletme aracı hakkında daha fazla bilgi](usage-retention.md)

## <a name="custom-business-events"></a>Özel iş olayları

Uygulamanızdaki kullanıcıların neler yaptığını net bir şekilde anlamak için, özel olayları günlüğe kaydetmek üzere kod satırları eklemek yararlıdır. Bu olaylar, belirli düğmeleri tıklatmak gibi ayrıntılı Kullanıcı eylemlerinden herhangi bir şeyi izleyerek satın alma veya oyun kazanma gibi daha önemli iş olaylarına sahip olabilir.

Özel olayları toplamak için [analiz otomatik toplama eklentisi ' ni](javascript-click-analytics-plugin.md) de kullanabilirsiniz.

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

[Özel olaylar](./api-custom-events-metrics.md#trackevent) ve [Özellikler](./api-custom-events-metrics.md#properties)hakkında daha fazla bilgi edinin.

### <a name="slice-and-dice-events"></a>Dilim ve zar olayları

Kullanıcılar, oturumlar ve olaylar araçlarında, özel olayları Kullanıcı, olay adı ve özelliklere göre dilimleyerek ve zar aktarabilirsiniz.
![Ekran yakalama, kurgusal bir şirket için kullanıcılara genel bakış sayfasını gösterir.](./media/usage-overview/users.png)  
  
## <a name="design-the-telemetry-with-the-app"></a>Uygulamayla telemetri tasarlama

Uygulamanızın her bir özelliğini tasarlarken, kullanıcılarınızın başarısını nasıl ölçmenize dikkat edin. Kayıt yapmanız gereken iş olaylarını belirleyin ve bu olaylar için izleme çağrılarını, başlangıçtan itibaren uygulamanıza kodlayın.

## <a name="a--b-testing"></a>A | B testi
Bir özelliğin hangi çeşidinin daha başarılı olacağını bilmiyorsanız, her ikisini de serbest bırakın, her ikisini birden farklı kullanıcı için erişilebilir hale getirir. Her birinin başarısını ölçün ve ardından birleştirilmiş bir sürüme taşıyın.

Bu teknik için, uygulamanızın her bir sürümü tarafından gönderilen tüm telemetrisine ayrı özellik değerleri iliştirebilirsiniz. Bunu, etkin TelemetryContext özellikleri tanımlayarak yapabilirsiniz. Bu varsayılan özellikler, uygulamanın gönderdiği her telemetri iletisine (yalnızca özel mesajlarınızı değil, standart Telemetriyi) eklenir.

Application Insights portalında, farklı sürümleri karşılaştırmak için, bu verileri özellik değerlerine göre filtreleyin ve ayırın.

Bunu yapmak için [bir telemetri başlatıcısı ayarlayın](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer):

**ASP.NET uygulamaları**

```csharp
    // Telemetry initializer class
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry item)
            {
                var itemProperties = item as ISupportProperties;
                if (itemProperties != null && !itemProperties.Properties.ContainsKey("AppVersion"))
                {
                    itemProperties.Properties["AppVersion"] = "v2.1";
                }
            }
    }
```

Web uygulaması başlatıcısında Global. asax. cs gibi:

```csharp

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
         .Add(new MyTelemetryInitializer());
    }
```

**ASP.NET Core uygulamaları**

> [!NOTE]
> `ApplicationInsights.config`Veya kullanarak Başlatıcı eklemek `TelemetryConfiguration.Active` ASP.NET Core uygulamaları için geçerli değildir. 

[ASP.NET Core](asp-net-core.md#adding-telemetryinitializers) uygulamalar için, aşağıda gösterildiği gibi, yeni bir ekleme `TelemetryInitializer` işlemi bağımlılık ekleme kapsayıcısına eklenerek yapılır. Bu, `ConfigureServices` sınıfınızın yönteminde yapılır `Startup.cs` .

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
   - [Deposuna](usage-retention.md)
   - [Kullanıcı Akışları](usage-flows.md)
   - [Çalışma Kitapları](../visualize/workbooks-overview.md)
