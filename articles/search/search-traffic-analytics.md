---
title: Arama trafiği analizi için telemetri
titleSuffix: Azure Cognitive Search
description: Azure Bilişsel Arama için arama trafiği analizini etkinleştirin, Application Insights kullanarak telemetri ve Kullanıcı tarafından başlatılan olayları toplayın ve ardından bir Power BI raporundaki bulguları çözümleyin.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 1/29/2021
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 89d067162dacb7a0ca25de826630e1986f1035e1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "102485478"
---
# <a name="collect-telemetry-data-for-search-traffic-analytics"></a>Arama trafiği analizi için telemetri verilerini toplama

Arama trafiği analizi, Kullanıcı tarafından başlatılan tıklama olayları ve klavye girişleri gibi Azure Bilişsel Arama uygulamanızla kullanıcı etkileşimleri hakkında telemetri toplamaya yönelik bir modeldir. Bu bilgileri kullanarak, popüler arama terimleri, tıklama ücreti ve hangi sorgu girişlerinin sıfır sonuçları elde etme gibi arama çözümünüzün verimliliğini belirleyebilirsiniz.

Bu model, Kullanıcı verilerini toplamak için [Application Insights](../azure-monitor/app/app-insights-overview.md) ( [Azure izleyici](../azure-monitor/index.yml)'nin bir özelliği) bir bağımlılığı alır. Bu makalede açıklandığı gibi, istemci kodunuza izleme eklemenizi gerektirir. Son olarak, verileri çözümlemek için bir raporlama mekanizmasına ihtiyacınız olacaktır. Power BI önerilir, ancak uygulama panosunu veya Application Insights bağlanan herhangi bir aracı kullanabilirsiniz.

> [!NOTE]
> Bu makalede açıklanan model, gelişmiş senaryolar ve istemcinizde eklediğiniz kod tarafından oluşturulan tıklama akışı verileri içindir. Buna karşılık, hizmet günlüklerinin ayarlanması kolaydır, bir dizi ölçüm sağlar ve kod gerekmeden portalda yapılabilir. Günlüğe kaydetmenin etkinleştirilmesi tüm senaryolar için önerilir. Daha fazla bilgi için bkz. [günlük verilerini toplama ve analiz etme](search-monitor-logs.md).

## <a name="identify-relevant-search-data"></a>İlgili arama verilerini tanımla

Arama trafiği analizi için yararlı ölçümler sağlamak üzere, arama uygulamanızın kullanıcılarından bazı sinyalleri günlüğe kaydetmek gerekir. Bu sinyaller, kullanıcıların ilgilendiği ve ilgili kabul ettikleri içerikleri işaret eder. Arama trafiği analizi için şunlar vardır:

+ Kullanıcı tarafından oluşturulan arama olayları: yalnızca bir kullanıcı tarafından başlatılan arama sorguları ilginç. Modelleri doldurmak veya iç bilgileri almak için kullanılanlar gibi diğer arama istekleri de önemli değildir. Sonuçlarınızda eğriliği veya farkı önlemek için yalnızca Kullanıcı tarafından başlatılan olayları işaretlemediğinizden emin olun.

+ Kullanıcı tarafından oluşturulan tıklama olayları: bir arama sonuçları sayfasında, bir tıklama olayı genellikle belgenin belirli bir arama sorgusunun ilgili bir sonucu olduğu anlamına gelir.

Arama ve tıklama olaylarına bir bağıntı KIMLIĞIYLE bağlanarak uygulamanızın arama işlevselliğinin ne kadar iyi bir şekilde çalıştığını daha ayrıntılı bir şekilde anlayacaksınız.

## <a name="add-search-traffic-analytics"></a>Arama trafiği analizi ekleme

Azure Bilişsel Arama hizmetiniz için [Portal](https://portal.azure.com) sayfasında, bu telemetri deseninin takip eden bir başvuru sayfasına erişmek için arama trafik analizi sayfasını açın. Bu sayfadan bir Application Insights kaynağı seçebilir veya oluşturabilir, izleme anahtarını alabilir, çözümünüz için uyarlayabileceğiniz kod parçacıklarını kopyalayabilir ve düzen içinde yansıtılan şema üzerinde oluşturulmuş bir Power BI raporu indirebilirsiniz.

![Portalda Trafik Analizi sayfasında ara](media/search-traffic-analytics/azuresearch-trafficanalytics.png "Portalda Trafik Analizi sayfasında ara")

## <a name="1---set-up-application-insights"></a>1-Application Insights ayarlama

Mevcut bir Application Insights kaynağını seçin veya henüz yoksa [bir tane oluşturun](../azure-monitor/app/create-new-resource.md) . Arama Trafik Analizi sayfasını kullanıyorsanız, uygulamanızın Application Insights bağlanması için gereken izleme anahtarını kopyalayabilirsiniz.

Application Insights bir kaynağınız olduğunda, uygulamanızı kaydetmek için [desteklenen diller ve platformlar için yönergeler](../azure-monitor/app/platforms.md) izleyebilirsiniz. Kayıt, ilişkilendirmeyi ayarlayan Application Insights izleme anahtarını kodunuza eklemektir. Mevcut bir kaynağı seçerken, anahtarı portalda veya arama Trafik Analizi sayfasından bulabilirsiniz.

Bazı Visual Studio proje türleri için çalışma için bir kısayol aşağıdaki adımlarda yansıtılır. Bir kaynak oluşturur ve uygulamanızı yalnızca birkaç tıklamayla kaydeder.

1. Visual Studio ve ASP.NET geliştirme için çözümünüzü açın ve **Proje**  >  **Ekle Application Insights telemetri**' yi seçin.

1. **Get Started** düğmesine tıklayın.

1. Microsoft hesabı, Azure aboneliği ve bir Application Insights kaynağı (varsayılan olarak yeni bir kaynak) sağlayarak uygulamanızı kaydedin. **Kaydet**’e tıklayın.

Bu noktada, uygulamanız uygulama izleme için ayarlanır. Bu, tüm sayfa yüklemelerinin varsayılan ölçümlerle izlendiği anlamına gelir. Önceki adımlar hakkında daha fazla bilgi için bkz. [sunucu tarafı telemetrisini Application Insights etkinleştirme](../azure-monitor/app/asp-net-core.md#enable-application-insights-server-side-telemetry-visual-studio).

## <a name="2---add-instrumentation"></a>2-izleme ekleme

Bu adım, yukarıdaki adımda oluşturduğunuz Application Insights kaynağını kullanarak kendi arama uygulamanızı planladığınız yerdir. Bu işlemin bir telemetri istemcisi oluşturmayla başlayarak dört adımı vardır.

### <a name="step-1-create-a-telemetry-client"></a>1. Adım: telemetri istemcisi oluşturma

Application Insights olayları gönderen bir nesne oluşturun. Sunucu tarafı uygulama kodunuza veya bir tarayıcıda çalışan istemci tarafı koduna, burada C# ve JavaScript çeşitlemeleri olarak ifade edilen araçlar ekleyebilirsiniz (diğer diller için, [Desteklenen platformların ve](../azure-monitor/app/platforms.md)çerçevelerin tam listesine bakın. Size istenen bilgi derinliğini sağlayan yaklaşımı seçin.

Sunucu tarafı telemetrisi, uygulama katmanında ölçümleri, örneğin bulutta bir Web hizmeti olarak çalışan uygulamalarda veya kurumsal ağdaki şirket içi bir uygulama olarak yakalar. Sunucu tarafı telemetrisi, arama ve tıklama olayları, sonuçlar halinde bir belgenin konumu ve sorgu bilgileri yakalar, ancak veri koleksiyonunuz bu katmanda kullanılabilen bilgilerin kapsamına alınır.

İstemcide sorgu girdilerini işleyen, gezinme ekleyen veya bağlamı içeren (örneğin, bir giriş sayfasından bir ürün sayfasına karşı başlatılan sorgular) ek kodunuz olabilir. Çözümünüzü tarif ediyorsanız, telemetrinizin ek ayrıntıyı yansıtması için istemci tarafı araçları 'nı tercih edebilirsiniz. Bu ek ayrıntıların toplanması, bu düzenin kapsamının ötesinde olur, ancak daha fazla yön için [Web sayfalarına yönelik Application Insights](../azure-monitor/app/javascript.md#explore-browserclient-side-data) gözden geçirebilirsiniz. 

**C# kullanma**

C# için, ASP.NET olduğunda, **ınstrumentationkey** , uygulama yapılandırmanızda tanımlanmalıdır (appsettings.jsgibi). Anahtar konumundan emin değilseniz kayıt yönergelerine geri bakın.

```csharp
private static TelemetryClient _telemetryClient;

// Add a constructor that accepts a telemetry client:
public HomeController(TelemetryClient telemetry)
{
    _telemetryClient = telemetry;
}
```

**JavaScript kullanma**

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
    instrumentationKey: "<YOUR INSTRUMENTATION KEY>"
}});
</script>
```

> [!NOTE]
> Okunabilirliği sağlamak ve olası JavaScript hatalarını azaltmak için, tüm olası yapılandırma seçenekleri yukarıdaki kod parçacığı kodunda yeni bir satırda listelenir. Bu, bir açıklamalı çizginin değerini değiştirmek istemiyorsanız kaldırılabilir.


### <a name="step-2-request-a-search-id-for-correlation"></a>2. Adım: bağıntı için bir arama KIMLIĞI Isteyin

Arama isteklerini tıklama ile ilişkilendirmek için, bu iki farklı olayı içeren bir bağıntı KIMLIĞI olması gerekir. Azure Bilişsel Arama, bir HTTP üstbilgisiyle istek yaptığınızda bir arama KIMLIĞI sağlar.

Arama KIMLIĞINE sahip olmak, isteğin kendisi için Azure Bilişsel Arama tarafından oluşturulan ölçümlerin bağıntı almasına izin verir ve Application Insights oturum açmak için kullandığınız özel ölçümler vardır.

**C# kullanın (daha yeni V11 SDK)**

En son SDK, üstbilgiyi bu [örnekte](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/core/Azure.Core/samples/Pipeline.md#implementing-a-syncronous-policy)ayrıntılı olarak ayarlamak Için bir HTTP işlem hattının kullanılmasını gerektirir.

```csharp
// Create a custom policy to add the correct headers
public class SearchIdPipelinePolicy : HttpPipelineSynchronousPolicy
{
    public override void OnSendingRequest(HttpMessage message)
    {
        message.Request.Headers.SetValue("x-ms-azs-return-searchid", "true");
    }
}
```

```csharp
// This sample uses the .NET SDK https://www.nuget.org/packages/Azure.Search.Documents

SearchClientOptions clientOptions = new SearchClientOptions();
clientOptions.AddPolicy(new SearchIdPipelinePolicy(), HttpPipelinePosition.PerCall);

var client = new SearchClient("<SearchServiceName>", "<IndexName>", new AzureKeyCredential("<QueryKey>"), options: clientOptions);

Response<SearchResults<SearchDocument>> response = await client.SearchAsync<SearchDocument>(searchText: searchText, searchOptions: options);
string searchId = string.Empty;
if (response.GetRawResponse().Headers.TryGetValues("x-ms-azs-searchid", out IEnumerable<string> headerValues))
{
    searchId = headerValues.FirstOrDefault();
}
```

**C# kullanın (eski ile v10 arasındaki SDK)**

```csharp
// This sample uses the .NET SDK https://www.nuget.org/packages/Microsoft.Azure.Search

var client = new SearchIndexClient(<SearchServiceName>, <IndexName>, new SearchCredentials(<QueryKey>));

// Use HTTP headers so that you can get the search ID from the response
var headers = new Dictionary<string, List<string>>() { { "x-ms-azs-return-searchid", new List<string>() { "true" } } };
var response = await client.Documents.SearchWithHttpMessagesAsync(searchText: searchText, searchParameters: parameters, customHeaders: headers);
string searchId = string.Empty;
if (response.Response.Headers.TryGetValues("x-ms-azs-searchid", out IEnumerable<string> headerValues))
{
    searchId = headerValues.FirstOrDefault();
}
```

**JavaScript kullanma (REST API 'Leri çağırma)**

```javascript
request.setRequestHeader("x-ms-azs-return-searchid", "true");
request.setRequestHeader("Access-Control-Expose-Headers", "x-ms-azs-searchid");
var searchId = request.getResponseHeader('x-ms-azs-searchid');
```

### <a name="step-3-log-search-events"></a>3. Adım: günlük arama olayları

Bir kullanıcı tarafından her bir arama isteği verildiğinde, bir Application Insights özel olayında aşağıdaki şemaya sahip bir arama olayı olarak oturum açmanız gerekir. Yalnızca Kullanıcı tarafından oluşturulan arama sorgularının günlüğe kaydetmeyi unutmayın.

+ **SearchServiceName**: (dize) arama hizmeti adı
+ **Searchıd**: (GUID) arama sorgusunun benzersiz tanımlayıcısı (arama yanıtında gelir)
+ **IndexName**: (dize) Sorgulanacak arama hizmeti dizini
+ **Queryterms**: (dize) Kullanıcı tarafından girilen arama terimleri
+ **Resultcount**: (int) döndürülen belge sayısı (arama yanıtında gelir)
+ **ScoringProfile**: (dize) kullanılan Puanlama profili adı

> [!NOTE]
> Arama sorgunuza $count = true ekleyerek kullanıcı tarafından oluşturulan sorguların sayısını isteyin. Daha fazla bilgi için bkz. [arama belgeleri (REST)](/rest/api/searchservice/search-documents#query-parameters).
>

**C# kullanma**

```csharp
var properties = new Dictionary <string, string> 
{
    {"SearchServiceName", <service name>},
    {"SearchId", <search Id>},
    {"IndexName", <index name>},
    {"QueryTerms", <search terms>},
    {"ResultCount", <results count>},
    {"ScoringProfile", <scoring profile used>}
};
_telemetryClient.TrackEvent("Search", properties);
```

**JavaScript kullanma**

```javascript
appInsights.trackEvent("Search", {
  SearchServiceName: <service name>,
  SearchId: <search id>,
  IndexName: <index name>,
  QueryTerms: <search terms>,
  ResultCount: <results count>,
  ScoringProfile: <scoring profile used>
});
```

### <a name="step-4-log-click-events"></a>4. Adım: günlük tıklama olayları

Bir kullanıcının bir belgeyi tıkladığı her seferinde, arama analizi amacıyla günlüğe kaydedilecek bir sinyal olan bu bir sinyaldir. Bu olayları aşağıdaki şemayla günlüğe kaydetmek için Application Insights özel olaylar kullanın:

+ **ServiceName**: (dize) arama hizmeti adı
+ **Searchıd**: (GUID) ilgili arama sorgusunun benzersiz tanımlayıcısı
+ **Docid eşleyicisinde**: (dize) belge tanımlayıcısı
+ **Konum**: (int) arama sonuçları sayfasında belgenin derecesi

> [!NOTE]
> Konum, uygulamanızdaki önemli bir sıraya başvurur. Bu numarayı, her zaman aynı olduğu sürece, karşılaştırmaya izin verecek şekilde ayarlayabilirsiniz.
>

**C# kullanma**

```csharp
var properties = new Dictionary <string, string> 
{
    {"SearchServiceName", <service name>},
    {"SearchId", <search id>},
    {"ClickedDocId", <clicked document id>},
    {"Rank", <clicked document position>}
};
_telemetryClient.TrackEvent("Click", properties);
```

**JavaScript kullanma**

```javascript
appInsights.trackEvent("Click", {
    SearchServiceName: <service name>,
    SearchId: <search id>,
    ClickedDocId: <clicked document id>,
    Rank: <clicked document position>
});
```

## <a name="3---analyze-in-power-bi"></a>3-Power BI 'de çözümle

Uygulamanızı doldurduktan ve uygulamanızın Application Insights doğru şekilde bağlandığını doğruladıktan sonra, Power BI masaüstündeki verileri çözümlemek için önceden tanımlanmış bir rapor şablonu indirirler. Rapor, arama trafiği analizi için yakalanan ek verileri çözümlemek için yararlı olan grafikleri ve tabloları içerir.

1. Azure Bilişsel Arama panosu sol gezinti bölmesinde, **Ayarlar**' ın altında, **Trafik Analizi ara**' ya tıklayın.

1. **Arama trafiği analizi** sayfasında, adım 3 ' te, Power BI yüklemek Için **Power BI Desktop al** ' a tıklayın.

   ![Power BI raporlarını al](./media/search-traffic-analytics/get-use-power-bi.png "Power BI raporlarını al")

1. Aynı sayfada **Power BI raporu indir**' e tıklayın.

1. Rapor Power BI Desktop açılır ve Application Insights bağlanmanız ve kimlik bilgilerini sağlamanız istenir. Application Insights kaynağınızın Azure portal sayfalarında bağlantı bilgilerini bulabilirsiniz. Kimlik bilgileri için, Portal oturum açma için kullandığınız kullanıcı adını ve parolayı belirtin.

   ![Application Insights'a Bağlan](./media/search-traffic-analytics/connect-to-app-insights.png "Application Insights'a Bağlan")

1. **Yükle**' ye tıklayın.

Rapor, arama performansınızı ve ilginizi geliştirmek için daha bilinçli kararlar almanıza yardımcı olan grafikleri ve tabloları içerir.

Ölçümler aşağıdaki öğeleri içerir:

+ Ara birim ve en popüler terim-belge çiftleri: tıklamalar tarafından sıralanan, aynı belge ile sonuçlanan terimler.
+ Tıklama olmadan arar: tıklamadan kaydeden en iyi sorgular için terimler

Aşağıdaki ekran görüntüsünde, tüm şema öğelerini kullandıysanız bir yerleşik raporun nasıl görünebileceğini gösterilmektedir.

![Azure Bilişsel Arama için Power BI panosu](./media/search-traffic-analytics/azuresearch-powerbi-dashboard.png "Azure Bilişsel Arama için Power BI panosu")

## <a name="next-steps"></a>Sonraki adımlar

Arama hizmetinize yönelik güçlü ve öngörülü veriler almak için arama uygulamanızı işaretleyin.

[Application Insights](../azure-monitor/app/app-insights-overview.md) hakkında daha fazla bilgi bulabilir ve farklı hizmet katmanları hakkında daha fazla bilgi edinmek için [fiyatlandırma sayfasını](https://azure.microsoft.com/pricing/details/application-insights/) ziyaret edebilirsiniz.

Harika raporlar oluşturma hakkında daha fazla bilgi edinin. Ayrıntılar için bkz. [Power BI Desktop kullanmaya](/power-bi/fundamentals/desktop-getting-started) başlama.
