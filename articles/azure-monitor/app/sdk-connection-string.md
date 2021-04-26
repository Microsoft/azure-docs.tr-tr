---
title: Azure Application Insights bağlantı dizeleri | Microsoft Docs
description: Bağlantı dizelerini kullanma.
ms.topic: conceptual
author: timothymothra
ms.author: tilee
ms.date: 01/17/2020
ms.custom: devx-track-js, devx-track-csharp
ms.reviewer: mbullwin
ms.openlocfilehash: df87b060423aeff9fa5f83f21634395fe30e0bbb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "102486293"
---
# <a name="connection-strings"></a>Bağlantı dizeleri

## <a name="overview"></a>Genel Bakış

Bağlantı dizeleri, birden çok proxy ayarı gereksinimini ortadan kaldıran, Application Insights kullanıcılarına tek bir yapılandırma ayarı sağlar. Verileri izleme hizmetine göndermek isteyen intranet Web sunucuları, sogeign veya hibrit bulut ortamları için son derece kullanışlıdır.

Anahtar değer çiftleri, kullanıcıların her bir Application Insights (AI) hizmeti/ürünü için bir önek son eki birleşimini tanımlamak için kolay bir yol sağlar.

> [!IMPORTANT]
> Hem bağlantı dizesini hem de Izleme anahtarını ayarlamayı önermiyoruz. Bir kullanıcının her ikisini de ayarlaması durumunda, en son ayarlanan ilke öncelikli olur. 


## <a name="scenario-overview"></a>Senaryoya genel bakış 

Bu, en etkili olduğunu görselleştirdiğimiz müşteri senaryolarında:

- Güvenlik Duvarı özel durumları veya proxy yönlendirmeleri 

    Intranet web sunucusu için izlemenin gerekli olduğu durumlarda, daha önceki çözümümüzde müşterilerin yapılandırmanıza ayrı hizmet uç noktaları eklemesi istenir. Daha fazla bilgi için [buraya](../faq.md#can-i-monitor-an-intranet-web-server)bakın. 
    Bağlantı dizeleri, bu çabayı tek bir ayara azaltarak daha iyi bir alternatif sunar. Basit önek, sonek değişikliği, tüm uç noktaların doğru hizmetlere otomatik olarak doldurulmasını ve yeniden yönlendirilmesine olanak tanır. 

- Sogeign veya hibrit bulut ortamları

    Kullanıcılar, tanımlı bir [Azure Kamu bölgesine](../../azure-government/compare-azure-government-global-azure.md#application-insights)veri gönderebilir.
    Bağlantı dizeleri, intranet sunucularınız veya karma bulut ayarları için uç nokta ayarlarını tanımlamanızı sağlar. 

## <a name="getting-started"></a>Kullanmaya başlama

### <a name="finding-my-connection-string"></a>Bağlantı dizim mi bulunuyor?

Bağlantı dizeniz Application Insights kaynağınızın genel bakış dikey penceresinde görüntülenir.

![Genel Bakış dikey penceresinde bağlantı dizesi](media/overview-dashboard/overview-connection-string.png)

### <a name="schema"></a>Şema

#### <a name="max-length"></a>Uzunluk üst sınırı

Bağlantı desteklenen en fazla 4096 karakter uzunluğunda.

#### <a name="key-value-pairs"></a>Anahtar-değer çiftleri

Bağlantı dizesi, noktalı virgülle ayrılmış anahtar-değer çiftleri olarak temsil edilen ayarların bir listesini içerir: `key1=value1;key2=value2;key3=value3`

#### <a name="syntax"></a>Syntax

- `InstrumentationKey` (örn: 00000000-0000-0000-0000-000000000000)  Bağlantı dizesi **gerekli** bir alandır.
- `Authorization` (örn: Ikey) (Bugün yalnızca Ikey yetkilendirmesini desteklediğimiz için bu ayar isteğe bağlıdır.)
- `EndpointSuffix` (örn: applicationinsights.azure.cn) Uç nokta son ekinin ayarlanması, SDK 'Yı hangi Azure bulutunun bağlanacağı ile talimat verecektir. SDK, her bir hizmet için uç noktanın geri kalanını birleştirir.
- Açık uç noktalar.
  Bağlantı dizesinde, herhangi bir hizmet açıkça geçersiz kılınabilir.
   - `IngestionEndpoint` (örn: `https://dc.applicationinsights.azure.com` )
   - `LiveEndpoint` (örn: `https://live.applicationinsights.azure.com` )
   - `ProfilerEndpoint` (örn: `https://profiler.monitor.azure.com` )
   - `SnapshotEndpoint` (örn: `https://snapshot.monitor.azure.com` )

#### <a name="endpoint-schema"></a>Uç nokta şeması

`<prefix>.<suffix>`
- Ön ek: bir hizmeti tanımlar. 
- Sonek: ortak etki alanı adını tanımlar.

##### <a name="valid-suffixes"></a>Geçerli sonekler

Geçerli sonekler listesi aşağıdadır 
- applicationinsights.azure.cn
- applicationinsights.us


Ayrıca bkz: https://docs.microsoft.com/azure/azure-monitor/app/custom-endpoints#regions-that-require-endpoint-modification


##### <a name="valid-prefixes"></a>Geçerli ön ekler

- [Telemetri](./app-insights-overview.md)alımı: `dc`
- [Canlı ölçümler](./live-stream.md): `live`
- [Profil Oluşturucu](./profiler-overview.md): `profiler`
- [Anlık görüntü](./snapshot-debugger.md): `snapshot`



## <a name="connection-string-examples"></a>Bağlantı dizesi örnekleri


### <a name="minimal-valid-connection-string"></a>En az geçerli bağlantı dizesi

`InstrumentationKey=00000000-0000-0000-0000-000000000000;`

Bu örnekte, yalnızca Izleme anahtarı ayarlanmıştır.

- Yetkilendirme düzeni varsayılan olarak "Ikey" olarak belirlenmiştir 
- İzleme anahtarı: 00000000-0000-0000-0000-000000000000
- Bölgesel hizmet URI 'Leri, [SDK varsayılanlarına](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/BASE/src/Microsoft.ApplicationInsights/Extensibility/Implementation/Endpoints/Constants.cs) dayanır ve genel Global Azure 'a bağlanır:
   - Alımı `https://dc.services.visualstudio.com/`
   - Canlı ölçümler: `https://rt.services.visualstudio.com/`
   - Profil `https://profiler.monitor.azure.com/`
   - Sý `https://snapshot.monitor.azure.com/`



### <a name="connection-string-with-endpoint-suffix"></a>Uç nokta sonekine sahip bağlantı dizesi

`InstrumentationKey=00000000-0000-0000-0000-000000000000;EndpointSuffix=ai.contoso.com;`

Bu örnekte, bu bağlantı dizesi uç nokta sonekini belirtir ve SDK hizmet uç noktaları oluşturur.

- Yetkilendirme düzeni varsayılan olarak "Ikey" olarak belirlenmiştir 
- İzleme anahtarı: 00000000-0000-0000-0000-000000000000
- Bölgesel hizmet URI 'Leri, belirtilen uç nokta sonekine dayalıdır: 
   - Alımı `https://dc.ai.contoso.com`
   - Canlı ölçümler: `https://live.ai.contoso.com`
   - Profil `https://profiler.ai.contoso.com`
   - Sý `https://snapshot.ai.contoso.com`  



### <a name="connection-string-with-explicit-endpoint-overrides"></a>Açık uç nokta geçersiz kılmalarla bağlantı dizesi 

`InstrumentationKey=00000000-0000-0000-0000-000000000000;IngestionEndpoint=https://custom.com:111/;LiveEndpoint=https://custom.com:222/;ProfilerEndpoint=https://custom.com:333/;SnapshotEndpoint=https://custom.com:444/;`

Bu örnekte, bu bağlantı dizesi her hizmet için açık geçersiz kılmalar belirler. SDK, değişiklik yapılmadan belirtilen tam uç noktaları kullanır.

- Yetkilendirme düzeni varsayılan olarak "Ikey" olarak belirlenmiştir 
- İzleme anahtarı: 00000000-0000-0000-0000-000000000000
- Bölgesel hizmet URI 'Leri açık geçersiz kılma değerlerine dayalıdır: 
   - Alımı `https://custom.com:111/`
   - Canlı ölçümler: `https://custom.com:222/`
   - Profil `https://custom.com:333/`
   - Sý `https://custom.com:444/`  


## <a name="how-to-set-a-connection-string"></a>Bağlantı dizesi ayarlama

Bağlantı dizeleri aşağıdaki SDK sürümlerinde desteklenir:
- .NET ve .NET Core v 2.12.0
- Java v 2.5.1 ve Java 3,0
- JavaScript v 2.3.0
- NodeJS v 1.5.0
- Python v 1.0.0

Bir bağlantı dizesi, kod, ortam değişkeni veya yapılandırma dosyası aracılığıyla ayarlanabilir.



### <a name="environment-variable"></a>Ortam değişkeni

- Bağlantı dizesi: `APPLICATIONINSIGHTS_CONNECTION_STRING`

### <a name="code-samples"></a>Kod örnekleri

# <a name="netnetcore"></a>[.NET/. NetCore](#tab/net)

[TelemetryConfiguration. ConnectionString](https://github.com/microsoft/ApplicationInsights-dotnet/blob/add45ceed35a817dc7202ec07d3df1672d1f610d/BASE/src/Microsoft.ApplicationInsights/Extensibility/TelemetryConfiguration.cs#L271-L274) veya [Applicationınsightsserviceoptions. ConnectionString](https://github.com/microsoft/ApplicationInsights-dotnet/blob/81288f26921df1e8e713d31e7e9c2187ac9e6590/NETCORE/src/Shared/Extensions/ApplicationInsightsServiceOptions.cs#L66-L69) özelliğini ayarlayın

.NET açıkça ayarlanmış:
```csharp
var configuration = new TelemetryConfiguration
{
    ConnectionString = "InstrumentationKey=00000000-0000-0000-0000-000000000000;"
};
```

.NET yapılandırma dosyası:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
    <ConnectionString>InstrumentationKey=00000000-0000-0000-0000-000000000000</ConnectionString>
</ApplicationInsights>
```

NetCore açıkça ayarlanmış:
```csharp
public void ConfigureServices(IServiceCollection services)
{
    var options = new ApplicationInsightsServiceOptions { ConnectionString = "InstrumentationKey=00000000-0000-0000-0000-000000000000;" };
    services.AddApplicationInsightsTelemetry(options: options);
}
```

NetCore config.js: 

```json
{
  "ApplicationInsights": {
    "ConnectionString" : "InstrumentationKey=00000000-0000-0000-0000-000000000000;"
    }
  }
```


# <a name="java"></a>[Java](#tab/java)


Java (v 2.5. x) açıkça ayarlanmış:
```java
TelemetryConfiguration.getActive().setConnectionString("InstrumentationKey=00000000-0000-0000-0000-000000000000");
```

ApplicationInsights.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
    <ConnectionString>InstrumentationKey=00000000-0000-0000-0000-000000000000;</ConnectionString>
</ApplicationInsights>
```

# <a name="javascript"></a>[JavaScript](#tab/js)

Önemli: JavaScript ortam değişkenlerinin kullanımını desteklemez.

Kod parçacığını kullanma:

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
  connectionString:"InstrumentationKey=00000000-0000-0000-0000-000000000000;"
}});
</script>
```

> [!NOTE]
> Okunabilirliği sağlamak ve olası JavaScript hatalarını azaltmak için, tüm olası yapılandırma seçenekleri yukarıdaki kod parçacığı kodunda yeni bir satırda listelenir. Bu, bir açıklamalı çizginin değerini değiştirmek istemiyorsanız kaldırılabilir.

El ile kurulum:
```javascript
import { ApplicationInsights } from '@microsoft/applicationinsights-web'

const appInsights = new ApplicationInsights({ config: {
  connectionString: 'InstrumentationKey=00000000-0000-0000-0000-000000000000;'
  /* ...Other Configuration Options... */
} });
appInsights.loadAppInsights();
appInsights.trackPageView();
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
const appInsights = require("applicationinsights");
appInsights.setup("InstrumentationKey=00000000-0000-0000-0000-000000000000;");
appInsights.start();
```

# <a name="python"></a>[Python](#tab/python)

Kullanıcıların ortam değişkenini ayarlamanızı öneririz.

Bağlantı dizesini açıkça ayarlamak için:

```python
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

tracer = Tracer(exporter=AzureExporter(connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000'), sampler=ProbabilitySampler(1.0))
```

---

## <a name="next-steps"></a>Sonraki adımlar

Çalışma zamanında şunlarla kullanmaya başlayın:

* [Azure VM ve Azure sanal makine ölçek kümesi IIS tarafından barındırılan uygulamalar](./azure-vm-vmss-apps.md)
* [IIS sunucusu](./monitor-performance-live-website-now.md)
* [Azure Web Apps](./azure-web-apps.md)

Geliştirme zamanında şunlarla kullanmaya başlayın:

* [ASP.NET](./asp-net.md)
* [ASP.NET Core](./asp-net-core.md)
* [Java](./java-get-started.md)
* [Node.js](./nodejs.md)
* [Python](./opencensus-python.md)

