---
title: Azure Application Insights geçersiz kılma varsayılan SDK uç noktaları
description: Azure Kamu gibi bölgeler için varsayılan Azure Izleyici Application Insights SDK uç noktalarını değiştirin.
ms.topic: conceptual
ms.date: 07/26/2019
ms.custom: references_regions, devx-track-js
ms.openlocfilehash: 13470017281ecfa616715777e78e446e71a08b0e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "102489812"
---
# <a name="application-insights-overriding-default-endpoints"></a>Varsayılan uç noktaları geçersiz kılmak Application Insights

Application Insights verileri belirli bölgelere göndermek için varsayılan uç nokta adreslerini geçersiz kılmanız gerekir. Her SDK, hepsi bu makalede açıklanan biraz farklı değişiklik gerektirir. Bu değişiklikler, örnek kodu ayarlamayı ve,, ve için yer tutucu değerlerini `QuickPulse_Endpoint_Address` , `TelemetryChannel_Endpoint_Address` `Profile_Query_Endpoint_address` belirli bölgeniz için gerçek uç nokta adresleriyle değiştirmeyi gerektirir. Bu makalenin sonunda, bu yapılandırmanın gerekli olduğu bölgelere yönelik uç nokta adreslerinin bağlantıları bulunur.

> [!NOTE]
> [Bağlantı dizeleri](./sdk-connection-string.md?tabs=net) Application Insights içindeki özel uç noktaları ayarlamanın yeni tercih edilen yöntemidir.

---

## <a name="sdk-code-changes"></a>SDK kodu değişiklikleri

# <a name="net"></a>[.NET](#tab/net)

> [!NOTE]
> applicationinsights.config dosyası, bir SDK yükseltmesi gerçekleştirildiğinde otomatik olarak üzerine yazılır. SDK yükseltmesini gerçekleştirdikten sonra bölgeye özgü uç nokta değerlerini yeniden girdiğinizden emin olun.

```xml
<ApplicationInsights>
  ...
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <QuickPulseServiceEndpoint>Custom_QuickPulse_Endpoint_Address</QuickPulseServiceEndpoint>
    </Add>
  </TelemetryModules>
    ...
  <TelemetryChannel>
     <EndpointAddress>TelemetryChannel_Endpoint_Address</EndpointAddress>
  </TelemetryChannel>
  ...
  <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
    <ProfileQueryEndpoint>Profile_Query_Endpoint_address</ProfileQueryEndpoint>
  </ApplicationIdProvider>
  ...
</ApplicationInsights>
```

# <a name="net-core"></a>[.NET Core](#tab/netcore)

Ana uç noktayı ayarlamak için projenizdeki appsettings.jsaşağıdaki şekilde değiştirin:

```json
"ApplicationInsights": {
    "InstrumentationKey": "instrumentationkey",
    "TelemetryChannel": {
      "EndpointAddress": "TelemetryChannel_Endpoint_Address"
    }
  }
```

Canlı ölçümler ve profil sorgu uç noktası değerleri yalnızca kod aracılığıyla ayarlanabilir. Tüm uç nokta değerlerinin varsayılan değerlerini kod aracılığıyla geçersiz kılmak için, dosyanın yönteminde aşağıdaki değişiklikleri yapın `ConfigureServices` `Startup.cs` :

```csharp
using Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId;
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse; //Place at top of Startup.cs file

   services.ConfigureTelemetryModule<QuickPulseTelemetryModule>((module, o) => module.QuickPulseServiceEndpoint="QuickPulse_Endpoint_Address");

   services.AddSingleton<IApplicationIdProvider, ApplicationInsightsApplicationIdProvider>(_ => new ApplicationInsightsApplicationIdProvider() { ProfileQueryEndpoint = "Profile_Query_Endpoint_address" });

   services.AddSingleton<ITelemetryChannel>(_ => new ServerTelemetryChannel() { EndpointAddress = "TelemetryChannel_Endpoint_Address" });

    //Place in the ConfigureServices method. Place this before services.AddApplicationInsightsTelemetry("instrumentation key"); if it's present
```

# <a name="azure-functions"></a>[Azure İşlevleri](#tab/functions)

Azure Işlevleri için artık Işlevin uygulama ayarlarında ayarlanan [bağlantı dizelerini](./sdk-connection-string.md?tabs=net) kullanmanız önerilir. İşlevinizin uygulama ayarlarına işlevler bölmesinde erişmek için **Ayarlar**  >  **yapılandırma**  >  **uygulama ayarları**' nı seçin. 

Ad: `APPLICATIONINSIGHTS_CONNECTION_STRING` değer: `Connection String Value`

# <a name="java"></a>[Java](#tab/java)

Varsayılan uç nokta adresini değiştirmek için applicationinsights.xml dosyasını değiştirin.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <InstrumentationKey>ffffeeee-dddd-cccc-bbbb-aaaa99998888</InstrumentationKey>
  <TelemetryModules>
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebSessionTrackingTelemetryModule"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebUserTrackingTelemetryModule"/>
  </TelemetryModules>
  <TelemetryInitializers>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>
  </TelemetryInitializers>
  <!--Add the following Channel value to modify the Endpoint address-->
  <Channel type="com.microsoft.applicationinsights.channel.concrete.inprocess.InProcessTelemetryChannel">
  <EndpointAddress>TelemetryChannel_Endpoint_Address</EndpointAddress>
  </Channel>
</ApplicationInsights>
```

### <a name="spring-boot"></a>Spring Boot

Dosyayı değiştirin `application.properties` ve ekleyin:

```yaml
azure.application-insights.channel.in-process.endpoint-address= TelemetryChannel_Endpoint_Address
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY');
appInsights.defaultClient.config.endpointUrl = "TelemetryChannel_Endpoint_Address"; // ingestion
appInsights.defaultClient.config.profileQueryEndpoint = "Profile_Query_Endpoint_address"; // appid/profile lookup
appInsights.defaultClient.config.quickPulseHost = "QuickPulse_Endpoint_Address"; //live metrics
appInsights.Configuration.start();
```

Uç noktalar, ortam değişkenleri aracılığıyla da yapılandırılabilir:

```
Instrumentation Key: "APPINSIGHTS_INSTRUMENTATIONKEY"
Profile Endpoint: "Profile_Query_Endpoint_address"
Live Metrics Endpoint: "QuickPulse_Endpoint_Address"
```

# <a name="javascript"></a>[JavaScript](#tab/js)

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
    instrumentationKey:"INSTRUMENTATION_KEY",
    endpointUrl: "TelemetryChannel_Endpoint_Address"
}});
</script>
```

> [!NOTE]
> Okunabilirliği sağlamak ve olası JavaScript hatalarını azaltmak için, tüm olası yapılandırma seçenekleri yukarıdaki kod parçacığı kodunda yeni bir satırda listelenir. Bu, bir açıklamalı çizginin değerini değiştirmek istemiyorsanız kaldırılabilir.

# <a name="python"></a>[Python](#tab/python)

Opencensus-Python SDK 'sının alma uç noktasını değiştirme Kılavuzu için, [opencensus-Python deposu](https://github.com/census-instrumentation/opencensus-python/blob/af284a92b80bcbaf5db53e7e0813f96691b4c696/contrib/opencensus-ext-azure/opencensus/ext/azure/common/__init__.py) ' na başvurun.

---

## <a name="regions-that-require-endpoint-modification"></a>Uç nokta değişikliği gerektiren bölgeler

Şu anda yalnızca uç nokta değişiklikleri gerektiren bölgeler [Azure Kamu](../../azure-government/compare-azure-government-global-azure.md#application-insights) ve [Azure Çin](/azure/china/resources-developer-guide)' dir.

|Region |  Uç nokta adı | Değer |
|-----------------|:------------|:-------------|
| Azure Çin | Telemetri kanalı | `https://dc.applicationinsights.azure.cn/v2/track` |
| Azure Çin | QuickPulse (canlı ölçümler) |`https://live.applicationinsights.azure.cn/QuickPulseService.svc` |
| Azure Çin | Profil sorgusu |`https://dc.applicationinsights.azure.cn/api/profiles/{0}/appId`  |
| Azure Kamu | Telemetri kanalı |`https://dc.applicationinsights.us/v2/track` |
| Azure Kamu | QuickPulse (canlı ölçümler) |`https://quickpulse.applicationinsights.us/QuickPulseService.svc` |
| Azure Kamu | Profil sorgusu |`https://dc.applicationinsights.us/api/profiles/{0}/appId` |

Şu anda ' api.applicationinsights.io ' aracılığıyla erişilen [Application Insights REST API](https://dev.applicationinsights.io/
) kullanıyorsanız, bölgeniz için yerel bir uç nokta kullanmanız gerekir:

|Region |  Uç nokta adı | Değer |
|-----------------|:------------|:-------------|
| Azure Çin | REST API | `api.applicationinsights.azure.cn` |
| Azure Kamu | REST API | `api.applicationinsights.us`|

> [!NOTE]
> Azure Uygulama Hizmetleri için codeless Aracısı/uzantısı tabanlı izleme şu **anda** bu bölgelerde desteklenmiyor. Bu işlevsellik kullanılabilir hale geldiğinde, bu makale güncelleştirilir.

## <a name="next-steps"></a>Sonraki adımlar

- Azure Kamu ile ilgili özel değişiklikler hakkında daha fazla bilgi edinmek için [Azure izleme ve yönetimine](../../azure-government/compare-azure-government-global-azure.md#application-insights)yönelik ayrıntılı kılavuza başvurun.
- Azure Çin hakkında daha fazla bilgi edinmek için [Azure Çin PlayBook](/azure/china/)' a bakın.
