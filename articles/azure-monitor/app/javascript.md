---
title: JavaScript Web uygulamaları için Azure Application Insights
description: Sayfa görüntüleme ve oturum sayıları, Web istemcisi verileri, tek sayfalı uygulamalar (SPA) ve kullanım desenlerini izleyin. JavaScript web sayfalarında özel durumları ve performans sorunlarını yakalayın.
ms.topic: conceptual
ms.date: 08/06/2020
ms.custom: devx-track-js
ms.openlocfilehash: 04cda044b002e226c49f8647d4705d7c0f2a514e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105565274"
---
# <a name="application-insights-for-web-pages"></a>Web sayfaları için Application Insights

Web sayfanızın veya uygulamanızın performansı ve kullanımı hakkında bilgi edinin. Sayfa betiğe [Application Insights](app-insights-overview.md) eklerseniz, sayfa YÜKLEMELERININ ve Ajax çağrılarının zamanlamalarının yanı sıra, tarayıcı özel DURUMLARıNıN ve Ajax hatalarının yanı sıra kullanıcıların ve oturum sayılarındaki ayrıntıları alırsınız. Bunların tümü sayfaya, istemci işletim sistemi ve tarayıcı sürümüne, coğrafi konuma ve başka boyutlara göre kesimlere ayrılmıştır. Hata sayısı veya yavaş sayfa yüklemesi hakkında uyarı ayarlayabilirsiniz. Ayrıca JavaScript kodunuza izleme çağrıları ekleyerek web sayfası uygulamanızın farklı özelliklerinin nasıl kullanıldığını izleyebilirsiniz.

Application Insights tüm web sayfalarıyla kullanılabilir; kısa bir JavaScript eklemeniz yeterlidir. Web hizmetiniz [Java](java-get-started.md) veya [ASP.net](asp-net.md)ise, uygulamanızın performansını uçtan uca anlamak için istemci tarafı JavaScript SDK 'sı Ile birlikte sunucu tarafı SDK 'larını kullanabilirsiniz.

## <a name="adding-the-javascript-sdk"></a>JavaScript SDK 'Sı ekleme

> [!IMPORTANT]
> Yeni Azure bölgeleri, izleme anahtarları yerine bağlantı dizelerinin kullanılmasını **gerektirir** . [Bağlantı dizesi](./sdk-connection-string.md?tabs=js) , telemetri verilerinizi ilişkilendirmek istediğiniz kaynağı tanımlar. Ayrıca, kaynağınızın telemetri için hedef olarak kullanacağı uç noktaları değiştirmenize de olanak tanır. Bağlantı dizesini kopyalamanız ve uygulamanızın koduna veya bir ortam değişkenine eklemeniz gerekir.

1. Önce bir Application Insights kaynağına ihtiyacınız vardır. Henüz bir kaynak ve izleme anahtarınız yoksa [Yeni kaynak oluştur yönergelerini](create-new-resource.md)izleyin.
2. JavaScript telemetrinizin gönderilmesini istediğiniz kaynak için ("Ikey" olarak da bilinir) _izleme anahtarını_ veya [bağlantı dizesini](#connection-string-setup) (adım 1 ' den) kopyalayın. Bunu, `instrumentationKey` `connectionString` Application Insights JavaScript SDK 'sının veya ayarına ekleyeceksiniz.
3. Aşağıdaki iki seçenekten birini kullanarak Web sayfanıza veya uygulamanıza Application Insights JavaScript SDK 'sını ekleyin:
    * [NPM kurulumu](#npm-based-setup)
    * [JavaScript kod parçacığı](#snippet-based-setup)

> [!IMPORTANT]
> Uygulamanıza JavaScript SDK 'Sı eklemek için yalnızca bir yöntem kullanın. NPM kurulumunu kullanırsanız, kod parçacığını kullanmayın ve tam tersi de geçerlidir.

> [!NOTE]
> NPM kurulumu, JavaScript SDK 'sını projenize bir bağımlılık olarak yükleyerek IntelliSense 'i etkinleştirir, ancak kod parçacığı, çalışma zamanında SDK 'Yı getirir. Her ikisi de aynı özellikleri destekler. Ancak, daha fazla özel olay ve yapılandırma isteyen geliştiriciler genellikle NPM kurulumu için kabul etirken, kod parçacığı için hazır web analizi kabul etme işlemini arayan kullanıcılar.

### <a name="npm-based-setup"></a>NPM tabanlı kurulum

NPM aracılığıyla yükler.

```sh
npm i --save @microsoft/applicationinsights-web
```

> [!Note]
> **Bu pakete dahil** edilmiştir, bu nedenle ayrı bir typler paketi **yüklemeniz gerekmez.**
    
```js
import { ApplicationInsights } from '@microsoft/applicationinsights-web'

const appInsights = new ApplicationInsights({ config: {
  instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE'
  /* ...Other Configuration Options... */
} });
appInsights.loadAppInsights();
appInsights.trackPageView(); // Manually call trackPageView to establish the current user/session/pageview
```

### <a name="snippet-based-setup"></a>Kod parçacığı tabanlı kurulum

Uygulamanız NPM kullanmıyorsa, bu kod parçacığını sayfalarınızın en üstüne yapıştırarak Web sayfalarınızı Application Insights doğrudan bırakabilirsiniz. Tercihen, `<head>` bağımlılıklarınızın tüm olası sorunlarını ve isteğe bağlı olarak tüm JavaScript hatalarını izleyebilmesi için, bölüminizdeki ilk betik olmalıdır. Blazor Server uygulaması kullanıyorsanız, bölümünde dosyanın en üstüne kod parçacığını ekleyin `_Host.cshtml` `<head>` .

Uygulamanızın hangi kod parçacığının kullandığını izlemeye yardımcı olmak için, 2.5.5 sürümünden başlayarak sayfa görünümü olayı, tanımlanan kod parçacığı sürümünü içerecek yeni bir "AI. Internal. parçacığının" etiketini içerecektir.

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
    instrumentationKey: "YOUR_INSTRUMENTATION_KEY_GOES_HERE"
    /* ...Other Configuration Options... */
}});
</script>
```

> [!NOTE]
> Okunabilirliği sağlamak ve olası JavaScript hatalarını azaltmak için, tüm olası yapılandırma seçenekleri yukarıdaki kod parçacığı kodunda yeni bir satırda listelenir. Bu, bir açıklamalı çizginin değerini değiştirmek istemiyorsanız kaldırılabilir.


#### <a name="reporting-script-load-failures"></a>Betik yükleme başarısızlıklarını bildirme

Kod parçacığının bu sürümü, Azure Izleyici portalına bir özel durum olarak SDK yükleme sırasında hata saptar ve bildirir (hata &gt; özel durumlar &gt; tarayıcısı altında), bu özel durum, uygulamanızın telemetri (veya diğer özel durumlar) beklenen şekilde bildirilmediğini bilmeniz için bu türden hatalara görünürlük sağlar. Bu sinyal, Telemetriyi kaybettiğiniz, SDK 'nın yüklenmediği veya başlamadığı için önemli bir ölçüdür.
- Kullanıcılarınızın, sitenizi nasıl kullandığını (veya kullanmaya nasıl çalıştığını) raporlamak;
- Son kullanıcılarınızın sitenizi nasıl kullandığını gösteren telemetri eksik;
- Son kullanıcılarınızı sitenizi kullanarak başarıyla engelliyor olabilecek JavaScript hataları eksik.

Bu özel durumla ilgili ayrıntılar için bkz. [SDK yükleme hatası](javascript-sdk-load-failure.md) sorun giderme sayfası.

Bu hatanın Portal için bir özel durum olarak bildirilmesi, Application Insights yapılandırmasından yapılandırma seçeneğini kullanmaz ```disableExceptionTracking``` ve bu nedenle bu hata oluşursa, Window. mak desteği devre dışı olduğunda bile her zaman kod parçacığı tarafından bildirilir.

SDK yükleme hatalarının raporlanması özellikle IE 8 (veya daha az) üzerinde desteklenmez. Bu, çoğu ortamın özel olarak IE 8 veya daha az olmadığı varsayıldığında, kod parçacığının küçültülmüş boyutunu azaltmaya yardımcı olur. Bu gereksinime sahipseniz ve bu özel durumları almak istiyorsanız, bir Fetch Poly dolgusu dahil etmeniz veya bunun yerine kullanan kendi kod parçacığı sürümünü oluşturmanız gerekir ```XDomainRequest``` ```XMLHttpRequest``` , ancak [belirtilen kod parçacığı kaynak kodunu](https://github.com/microsoft/ApplicationInsights-JS/blob/master/AISKU/snippet/snippet.js) başlangıç noktası olarak kullanmanız önerilir.

> [!NOTE]
> Kod parçacığının önceki bir sürümünü kullanıyorsanız, önceden bildirilmeyen sorunları alabilmeniz için en son sürüme güncelleştirmeniz önemle önerilir.

#### <a name="snippet-configuration-options"></a>Kod parçacığı yapılandırma seçenekleri

Tüm yapılandırma seçenekleri, yanlışlıkla SDK 'nın yüklenmesine neden olmayan JavaScript hatalarına neden olmamak, ancak hatanın raporlanmasını devre dışı bırakacağından, komut dosyasının sonuna doğru şekilde geçmektedir.

Her yapılandırma seçeneği, yukarıdaki yeni bir satırda gösterilir. [isteğe bağlı] olarak listelenen bir öğenin varsayılan değerini geçersiz kılmak istemiyorsanız, döndürülen sayfanızın elde edilen boyutunu en aza indirmek için bu satırı kaldırabilirsiniz.

Kullanılabilir yapılandırma seçenekleri şunlardır
 
| Ad | Tür | Description
|------|------|----------------
| src | dize **[gerekli]** | SDK 'nın yükleneceği yerin tam URL 'SI. Bu değer, dinamik olarak eklenen bir betiğin/etiketin "src" özniteliği için kullanılır &lt; &gt; . Genel CDN konumunu veya kendi özel olarak barındırılan birini kullanabilirsiniz.
| name | dize *[isteğe bağlı]* | Başlatılmış SDK için genel ad, varsayılan olarak olur `appInsights` . ```window.appInsights```Bu nedenle, başlatılmış örneğe bir başvuru olacaktır. Note: bir ad değeri sağlarsanız veya bir önceki örnek atanmak üzere görünüyorsa (Appınsi\dk genel adı aracılığıyla), bu ad değeri aynı zamanda genel ad alanında olarak tanımlanır ```window.appInsightsSDK=<name value>``` , bu da SDK başlatma kodunun doğru kod parçacığı çatısı ve proxy yöntemlerinin başlatılmasını ve güncelleştirilmesini sağlamak için gereklidir.
| Eski | MS olarak sayı *[isteğe bağlı]* | SDK 'Yı yüklemeyi denemeden önce beklenecek yük gecikmesini tanımlar. Varsayılan değer 0 ' dır ve herhangi bir negatif değer, sayfanın baş bölgesine hemen bir betik etiketi ekler ve &lt; &gt; Bu, betik yüklenene kadar sayfa yükleme olayını engeller (veya başarısız olur).
| useXhr | Boole *[isteğe bağlı]* | Bu ayar yalnızca SDK yükleme hatalarının bildirdiği için kullanılır. Raporlama ilk olarak, varsa Fetch () kullanmaya çalışır ve ardından XHR 'a geri dönüşse, bu değeri true olarak ayarlamak yalnızca getirme denetimini atlar. Bu değerin kullanılması yalnızca uygulamanız, alma işlemi hata olaylarını gönderemediği bir ortamda kullanılıyorsa gereklidir.
| Çapraz Sorigin | dize *[isteğe bağlı]* | Bu ayarı ekleyerek, SDK 'Yı indirmek için eklenen komut dosyası etiketi, bu dize değerine sahip çapraz Sorigin özniteliğini içerecektir. Tanımlanmadığında (varsayılan), hiçbir çapraz ekleme özniteliği eklenmez. Önerilen değerler tanımlı değil (varsayılan); ""; veya "Anonymous" (tüm geçerli değerler Için bkz [. html özniteliği `crossorigin` :](https://developer.mozilla.org/en-US/docs/Web/HTML/Attributes/crossorigin) belgeler)
| satırında | nesne **[gerekli]** | Yapılandırma, başlatma sırasında Application Insights SDK 'ya geçirildi.

### <a name="connection-string-setup"></a>Bağlantı dizesi kurulumu

NPM veya kod parçacığı kurulumu için, Application Insights örneğinizi bir bağlantı dizesi kullanarak da yapılandırabilirsiniz. Alanı yalnızca `instrumentationKey` `connectionString` alanla değiştirin.
```js
import { ApplicationInsights } from '@microsoft/applicationinsights-web'

const appInsights = new ApplicationInsights({ config: {
  connectionString: 'YOUR_CONNECTION_STRING_GOES_HERE'
  /* ...Other Configuration Options... */
} });
appInsights.loadAppInsights();
appInsights.trackPageView();
```

### <a name="sending-telemetry-to-the-azure-portal"></a>Azure portal telemetri gönderme

Varsayılan olarak Application Insights JavaScript SDK 'Sı, uygulamanızın sistem durumunu ve temel alınan kullanıcı deneyimini belirlemede yardımcı olan bir dizi telemetri öğesini oto toplar. Bu modüller şunlardır:

- Uygulamanızdaki bilgiler dahil **yakalanamayan özel durumlar**
    - Yığın izleme
    - Özel durum ayrıntıları ve hatayı eşlik eden ileti
    - Satır & sütun hatası sayısı
    - Hatanın oluşturulduğu URL
- Uygulamanızın yaptığı **ağ bağımlılığı Istekleri** **XHR** ve **getir** (getirme koleksiyonu varsayılan olarak devre dışıdır) istekleri, hakkında bilgi içerir
    - Bağımlılık kaynağının URL 'si
    - Bağımlılığı istemek için kullanılan komut & yöntemi
    - İstek süresi
    - İsteğin sonuç kodu ve başarı durumu
    - İstek yapan kullanıcının KIMLIĞI (varsa)
    - İsteğin yapıldığı bağıntı bağlamı (varsa)
- **Kullanıcı bilgileri** (örneğin, konum, ağ, IP)
- **Cihaz bilgileri** (örneğin, tarayıcı, işletim sistemi, sürüm, dil, model)
- **Oturum bilgileri**

### <a name="telemetry-initializers"></a>Telemetri başlatıcıları
Telemetri başlatıcıları, kullanıcının tarayıcısından gönderilmeden önce toplanan telemetrinin içeriğini değiştirmek için kullanılır. Ayrıca, belirli telemetrinin gönderilmesini engellemek için de kullanılabilir `false` . Application Insights örneğinize birden çok telemetri başlatıcıları eklenebilir ve bunlar ekleme sırasında yürütülür.

İçin giriş bağımsız değişkeni, `addTelemetryInitializer` bir [`ITelemetryItem`](https://github.com/microsoft/ApplicationInsights-JS/blob/master/API-reference.md#addTelemetryInitializer) bağımsız değişken olarak bir veya döndüren bir geri çağırma olur `boolean` `void` . Geri döntakdirde `false` telemetri öğesi gönderilmez, aksi takdirde bir sonraki telemetri başlatıcısına devam eder veya telemetri toplama uç noktasına gönderilir.

Telemetri başlatıcılarının kullanılmasına bir örnek:
```ts
var telemetryInitializer = (envelope) => {
  envelope.data.someField = 'This item passed through my telemetry initializer';
};
appInsights.addTelemetryInitializer(telemetryInitializer);
appInsights.trackTrace({message: 'This message will use a telemetry initializer'});

appInsights.addTelemetryInitializer(() => false); // Nothing is sent after this is executed
appInsights.trackTrace({message: 'this message will not be sent'}); // Not sent
```

## <a name="configuration"></a>Yapılandırma
Çoğu yapılandırma alanı, varsayılan olarak false olarak ayarlanabilecek şekilde adlandırılır. Tüm alanlar, hariç olarak isteğe bağlıdır `instrumentationKey` .

| Ad | Açıklama | Varsayılan |
|------|-------------|---------|
| ınstrumentationkey | **Gerekli**<br>Azure portal aldığınız izleme anahtarı. | string<br/>null |
| accountId | Uygulamanız kullanıcıları hesaplara gruplayan isteğe bağlı hesap KIMLIĞI. Boşluk, virgül, noktalı virgül, eşittir veya dikey çubuklar yok | string<br/>null |
| sessionRenewalMs | Kullanıcı bu süre boyunca devre dışı bırakılırsa, bir oturum günlüğe kaydedilir. | sayısal<br/>1800000<br/>(30 dakika) |
| sessionExpirationMs | Bu süre boyunca milisaniye cinsinden devam eden bir oturum günlüğe kaydedilir. | sayısal<br/>86400000<br/>(24 saat) |
| Maxbatchsizeınbytes | Telemetri toplu işinin en büyük boyutu. Bir toplu iş bu sınırı aşarsa, hemen gönderilir ve yeni bir toplu işlem başlatılır | sayısal<br/>10000 |
| Maxbatchınterval | Göndermeden önce toplu iş telemetrisi için ne kadar süre (milisaniye) | sayısal<br/>15000 |
| ExceptionTracking&#8203;devre dışı bırak | True ise, özel durumlar tekrar toplanmamıştır. | boolean<br/> yanlış |
| Disabletelemetri | True ise telemetri toplanmaz veya gönderilmez. | boolean<br/>yanlış |
| enableDebug | True ise, **iç** hata ayıklama VERILERI, SDK günlüğü ayarlarından bağımsız olarak, günlüğe kaydedilmesi **yerine** bir özel durum olarak oluşturulur. Varsayılan değer false’tur. <br>**_Note:_** Bu ayarın etkinleştirilmesi, bir iç hata oluştuğunda telemetri oluşmasına neden olur. Bu, yapılandırma veya SDK kullanımınız ile ilgili sorunları hızlı bir şekilde tanımlamak için yararlı olabilir. Hata ayıklama sırasında Telemetriyi kaybetmek istemiyorsanız, veya yerine kullanmayı düşünün `consoleLoggingLevel` `telemetryLoggingLevel` `enableDebug` . | boolean<br/>yanlış |
| loggingLevelConsole | **İç** Application Insights hatalarını konsola kaydeder. <br>0: kapalı, <br>1: yalnızca kritik hatalar, <br>2: her şey (hata & uyarı) | sayısal<br/> 0 |
| Loggingleveltelemetri | **İç** Application Insights hatalarını telemetri olarak gönderir. <br>0: kapalı, <br>1: yalnızca kritik hatalar, <br>2: her şey (hata & uyarı) | sayısal<br/> 1 |
| Diagnosticlogınterval | iç İç günlük kuyruğu için yoklama aralığı (MS cinsinden) | sayısal<br/> 10000 |
| samplingPercentage | Gönderilecek olayların yüzdesi. Varsayılan değer 100 ' dir, yani tüm olaylar gönderilir. Büyük ölçekli uygulamalar için veri ucunu korumak isterseniz bunu ayarlayın. | sayısal<br/>100 |
| Oto Trackpagevisittime | True ise, bir PageView üzerinde, önceki belgelenmiş sayfanın görünüm süresi izlenir ve telemetri olarak gönderilir ve geçerli PageView için yeni bir Zamanlayıcı başlatılır. | boolean<br/>yanlış |
| disableAjaxTracking | True ise, Ajax çağrıları tekrar toplanmamıştır. | boolean<br/> yanlış |
| disableFetchTracking | True ise, Fetch istekleri tekrar toplanmaz.|boolean<br/>true |
| overridePageViewDuration | True ise trackPageView 'ın varsayılan davranışı sayfa görüntüleme süresi aralığının sonuna, trackPageView çağrıldığında bir kayıt olarak değiştirilmiştir. Yanlış olursa ve trackPageView için özel süre sağlanmamışsa, sayfa görünümü performansı, gezinti zamanlaması API 'SI kullanılarak hesaplanır. |boolean<br/>
| maxAjaxCallsPerView | Varsayılan 500-sayfa görünümü başına kaç Ajax çağrısının izleneceğini denetler. Sayfadaki tüm (sınırsız) AJAX çağrılarını izlemek için-1 olarak ayarlayın. | sayısal<br/> 500 |
| disableDataLossAnalysis | Yanlışsa, iç telemetri gönderici arabellekleri henüz gönderilmemiş öğeler için başlangıçta denetlenir. | boolean<br/> true |
| &#8203;CorrelationHeaders 'i devre dışı bırak | Yanlış ise, SDK, sunucu tarafındaki ilgili isteklerle ilişkilendirmek üzere tüm bağımlılık isteklerine iki üst bilgi (' Istek-kimlik ' ve ' Istek-bağlam ') ekler. | boolean<br/> yanlış |
| correlationHeader&#8203;ExcludedDomains | Belirli etki alanları için bağıntı üstbilgilerini devre dışı bırak | String []<br/>tanımlayan |
| correlationHeader&#8203;ExcludePatterns desenleri | Normal ifadeler kullanarak bağıntı üstbilgilerini devre dışı bırakma | Regex []<br/>tanımlayan |
| correlationHeader&#8203;etki alanları | Belirli etki alanları için bağıntı üstbilgilerini etkinleştir | String []<br/>tanımlayan |
| disableFlush&#8203;OnBeforeUnload | True ise, onBeforeUnload olay tetikleyicileri sırasında temizleme yöntemi çağrılmayacak | boolean<br/> yanlış |
| enableSessionStorageBuffer | Doğru ise, tüm gönderilmemiş Telemetriyi içeren arabellek oturum depolama alanında depolanır. Arabellek sayfa yüküne geri yüklendi | boolean<br />true |
| Tanımlama bilgisi Ecfg | Varsayılan olarak tanımlama bilgisi kullanımı etkindir: tam varsayılanlar için [ıokiecfgconfig](#icookiemgrconfig) ayarları ' na bakın. | [Iokiecfgconfig](#icookiemgrconfig)<br>(2.6.0 beri)<br/>tanımlayan |
| ~~Idite ıeusedisabled~~<br>disableCookiesUsage | True ise SDK, tanımlama bilgilerinden herhangi bir veriyi depolamaz veya okummaz. Bunun, Kullanıcı ve oturum tanımlama bilgilerini devre dışı bıraktığını ve kullanım dikey pencerelerini ve deneyimlerin kullanılamaz olduğunu unutmayın. ıdıalsel ıeusedisable, her ikisi de disableCookiesUsage tarafından sağlansa da, disableCookiesUsage kullanım dışı bırakılmıştır.<br>(V 2.6.0 beri) `cookieCfg.enabled` Ayrıca tanımlıysa, bu değerlere göre öncelikli olur. GetCookie (). setEnabled (true) aracılığıyla başlatma sonrasında tanımlama bilgisi kullanımı yeniden etkinleştirilebilir. | için diğer ad [`cookieCfg.enabled`](#icookiemgrconfig)<br>yanlış |
| Pişirme etki alanı | Özel tanımlama bilgisi etki alanı. Bu, alt etki alanları arasında Application Insights tanımlama bilgilerini paylaştırmak istiyorsanız yararlı olur.<br>(V 2.6.0 beri) `cookieCfg.domain` Tanımlanmışsa, bu değerden öncelikli olur. | için diğer ad [`cookieCfg.domain`](#icookiemgrconfig)<br>null |
| Tanımlama, ıepath | Özel tanımlama bilgisi yolu. Bu, bir uygulama ağ geçidinin arkasında Application Insights tanımlama bilgilerini paylaştırmak istiyorsanız yararlı olur.<br>`cookieCfg.path`Tanımlanmışsa, bu değerden öncelikli olur. | için diğer ad [`cookieCfg.path`](#icookiemgrconfig)<br>(2.6.0 beri)<br/>null |
| ıretrydisabled | Yanlışsa, 206 (kısmi başarı), 408 (zaman aşımı), 429 (çok fazla istek), 500 (iç sunucu hatası), 503 (hizmet kullanılamıyor) ve 0 (yalnızca algılanırsa) üzerinde yeniden deneyin | boolean<br/>yanlış |
| ıstorageusedisabled | True ise, SDK yerel ve oturum depolamadan hiçbir veriyi depolamaz veya okummaz. | boolean<br/> yanlış |
| ıconlanapidisabled | False ise, SDK, [Işaret API](https://www.w3.org/TR/beacon) 'sini kullanarak tüm telemetrileri gönderir | boolean<br/>true |
| Onunloaddisableişaret | Sekme kapatıldığında SDK, [Işaret API](https://www.w3.org/TR/beacon) 'sini kullanarak kalan tüm telemetrileri gönderir | boolean<br/> yanlış |
| SDK uzantısı | SDK uzantısının adını ayarlar. Yalnızca alfabetik karakterlere izin verilir. Uzantı adı ' AI. Internal. sdkVersion ' etiketinin öneki olarak eklenir (örneğin, ' ext_javascript: 2.0.0 '). | string<br/> null |
| isBrowserLink&#8203;TrackingEnabled | Doğru ise, SDK tüm [tarayıcı bağlantısı](/aspnet/core/client-side/using-browserlink) isteklerini izler. | boolean<br/>yanlış |
| appId | Uygulama kimliği, istemci tarafında sunucu tarafı istekleri ile oluşan AJAX bağımlılıkları arasındaki bağıntı için kullanılır. Işaret API 'SI etkinleştirildiğinde, otomatik olarak kullanılamaz, ancak yapılandırmada el ile ayarlanabilir. |string<br/> null |
| &#8203;CorsCorrelation etkinleştir | Doğru ise, SDK giden AJAX bağımlılıklarını sunucu tarafındaki ilgili isteklerle ilişkilendirmek için tüm CORS isteklerine iki üst bilgi (' Istek-kimliği ' ve ' Istek-bağlam ') ekler. | boolean<br/>yanlış |
| namePrefix | LocalStorage ve tanımlama bilgisi adı için ad soneki olarak kullanılacak isteğe bağlı bir değer. | string<br/>tanımlayan |
| &#8203;oto&#8203;Izlemeyi etkinleştir | Rota değişikliklerini tek sayfalı uygulamalarda (SPA) otomatik olarak izleyin. True ise, her yol değişikliği Application Insights yeni bir PageView gönderir. Karma yol değişiklikleri ( `example.com/foo#bar` ) de yeni sayfa görünümleri olarak kaydedilir.| boolean<br/>yanlış |
| enableRequest&#8203;HeaderTracking | True ise, AJAX & getirme isteği üst bilgileri izlenir. | boolean<br/> yanlış |
| Enableresle&#8203;HeaderTracking | True ise, AJAX & getirme isteğinin yanıt üst bilgileri izlenir. | boolean<br/> yanlış |
| distributedTracingMode | Dağıtılmış izleme modunu ayarlar. AI_AND_W3C Mode veya W3C modu ayarlandıysa, W3C Trace bağlam üstbilgileri (traceparent/tracestate) oluşturulur ve tüm giden isteklere dahil edilir. AI_AND_W3C, eski Application Insights belgelenmiş hizmetlerle geri uyumluluk için sağlanır. Örneğe [buraya](./correlation.md#enable-w3c-distributed-tracing-support-for-web-apps)bakın.| `DistributedTracingModes`veya<br/>sayısal<br/>(V 2.6.0 beri) `DistributedTracingModes.AI_AND_W3C`<br />(v 2.5.11 veya önceki sürümler) `DistributedTracingModes.AI` |
| &#8203;AjaxErrorStatusText 'i etkinleştir | Doğru ise, başarısız AJAX isteklerinde bağımlılık olayına yanıt hatası veri metnini ekleyin. | boolean<br/> yanlış |
| &#8203;AjaxPerfTracking etkinleştirin |Ek tarayıcı penceresi için arama ve dahil etme özelliğini etkinleştirme bayrağı. bildirilen `ajax` (XHR ve fetch) bildirilen ölçümlerde performans zamanlamaları. | boolean<br/> yanlış |
| maxAjaxPerf&#8203;LookupAttempts | Pencerenin aranacağı en fazla sayı. performans zamanlamaları (varsa), tüm tarayıcılar pencereyi doldurmadığından bu gereklidir. XHR isteğinin sonunu ve getirme isteklerini raporlamadan önce bu, tamamlandıktan sonra eklenir.| sayısal<br/> 3 |
| ajaxPerfLookupDelay | Bir istek için Windows. Performance zamanlamalarını bulmayı yeniden denemeden önce beklenecek süre `ajax` (süre milisaniye cinsinden) ve doğrudan setTimeout () öğesine geçirilir. | sayısal<br/> 25 MS |
| Enableişlenmemiş&#8203;PromiseRejection&#8203;Izleme | Doğru ise, işlenmeyen Promise reddi sayısı, yeniden toplanacak ve bir JavaScript hatası olarak raporlanır. DisableExceptionTracking true olduğunda (özel durumları izlememeyin), yapılandırma değeri yok sayılır ve işlenmemiş Promise geri alma raporlanmaz. | boolean<br/> yanlış |
| &#8203;ınstrumentationkey&#8203;doğrulamayı devre dışı bırak | Doğru ise, izleme anahtarı doğrulama denetimi atlanır. | boolean<br/>yanlış |
| enablePerfMgr | Etkinleştirildiğinde (true), bu, perfEvents (doPerf () Yardımcısı aracılığıyla) yayma için işaretlenmiş kod için yerel bir perfEvents oluşturur. Bu, kullanımınız veya isteğe bağlı olarak kendi belgelenmiş kodunuzda SDK içindeki performans sorunlarını belirlemek için kullanılabilir. [Temel belgelerde daha fazla ayrıntı bulabilirsiniz](https://github.com/microsoft/ApplicationInsights-JS/blob/master/docs/PerformanceMonitoring.md). V 2.5.7 beri | boolean<br/>yanlış |
| perfEvtsSendAll | _Enableperfmgr_ etkin olduğunda ve [ıperfmanager](https://github.com/microsoft/ApplicationInsights-JS/blob/master/shared/AppInsightsCore/src/JavaScriptSDK.Interfaces/IPerfManager.ts) bir [ınocertificate](https://github.com/microsoft/ApplicationInsights-JS/blob/master/shared/AppInsightsCore/src/JavaScriptSDK.Interfaces/INotificationManager.ts). perfevent () tetiklendiğinde, bu bayrak tüm olaylar (true) veya yalnızca ' Parent ' olayları için bir olayın tetikedilip edilmeyeceğini (tüm dinleyicilerine gönderileceğini) belirler (yanlış &lt; varsayılan &gt; ).<br />Üst [ıperfevent](https://github.com/microsoft/ApplicationInsights-JS/blob/master/shared/AppInsightsCore/src/JavaScriptSDK.Interfaces/IPerfEvent.ts) , bu olayın oluşturulması sırasında başka bir ıperfevent hala çalıştırılmadığından ve _üst_ özelliği null veya tanımsız olmadığı bir olaydır. V 2.5.7 beri |  boolean<br />yanlış |
| ıdlength | Yeni rastgele oturum ve Kullanıcı kimliği değerleri oluşturmak için kullanılan varsayılan uzunluğu tanımlar. Varsayılan değer 22 ' dir, önceki varsayılan değer 5 ' tir (v 2.5.8 veya daha az), önceki en büyük uzunluğu tutmanız gerekiyorsa bu değeri 5 olarak ayarlamanız gerekir. |  sayısal<br />22 |

## <a name="cookie-handling"></a>Tanımlama bilgisi Işleme

2.6.0 sürümünden, tanımlama bilgisi yönetimi artık doğrudan örnekten kullanılabilir ve başlatma sonrasında devre dışı bırakılabilir ve yeniden etkinleştirilebilir.

Veya yapılandırması aracılığıyla başlatma sırasında devre dışı bırakılmışsa `disableCookiesUsage` `cookieCfg.enabled` , artık [ıokiemgr](https://github.com/microsoft/ApplicationInsights-JS/blob/master/shared/AppInsightsCore/src/JavaScriptSDK.Interfaces/ICookieMgr.ts) işlevi aracılığıyla yeniden etkinleştirebilirsiniz `setEnabled` .

Örnek tabanlı tanımlama bilgisi yönetimi,, ve ' nin önceki coreutils genel işlevlerinin yerini alır `disableCookies()` `setCookie(...)` `getCookie(...)` `deleteCookie(...)` . Ayrıca, sürüm 2.6.0 'nin bir parçası olarak da sunulan Tree-gerçekleşmesi geliştirmelerinden faydalanmak için, artık genel işlevleri kullanmamalısınız.

### <a name="icookiemgrconfig"></a>Iokiemgrconfig

Örnek tabanlı tanımlama bilgisi yönetimi için tanımlama bilgisi yapılandırması 2.6.0 sürümüne eklenmiştir.

| Ad | Açıklama | Tür ve varsayılan |
|------|-------------|------------------|
| enabled | SDK tarafından tanımlama bilgilerinin kullanılması geçerli örnek tarafından etkinleştirilip etkinleştirilmeyeceğini belirten bir Boole değeri. False ise, bu yapılandırma tarafından başlatılan SDK 'nın örneği, tanımlama bilgilerinden verileri depolamaz veya okummaz | boolean<br/> true |
| etki alanı | Özel tanımlama bilgisi etki alanı. Bu, alt etki alanları arasında Application Insights tanımlama bilgilerini paylaştırmak istiyorsanız yararlı olur. Sağlanmazsa, kök değerden değeri kullanır `cookieDomain` . | string<br/>null |
| path | Tanımlama bilgisi için kullanılacak yolu belirtir, sağlanmazsa kök değerden herhangi bir değer kullanacaktır `cookiePath` . | string <br/> / |
| getCookie | Adlandırılmış tanımlama bilgisi değerini getirmek için işlevi, sağlanmazsa iç tanımlama bilgisi ayrıştırma/önbelleğe alma işlemini kullanacaktır. | `(name: string) => string` <br/> null |
| setCookie | Adlandırılmış tanımlama bilgisini, yalnızca tanımlama bilgisi eklenirken veya güncelleştirilirken çağrılan belirtilen değerle ayarlamak için işlev. | `(name: string, value: string) => void` <br/> null |
| delCookie | Adlandırılmış tanımlama bilgisini, belirtilen değere sahip, setCookie ile ayrılmış olarak silmek için işlev, tanımlama bilgisinin eklenip eklenmeyeceğini veya kaldırılıp kaldırılmadığını belirleme gereksinimini ortadan kaldırmak için. Sağlanmazsa, iç tanımlama bilgisi ayrıştırma/önbelleğe alma kullanılır. | `(name: string, value: string) => void` <br/> null |

### <a name="simplified-usage-of-new-instance-cookie-manager"></a>Yeni örnek tanımlama bilgisi Yöneticisi 'nin Basitleştirilmiş kullanımı

- appınsights. [Gettanımlama bilgisi EMGR ()](https://github.com/microsoft/ApplicationInsights-JS/blob/master/shared/AppInsightsCore/src/JavaScriptSDK.Interfaces/ICookieMgr.ts). SetEnabled (true/false);
- appınsights. [GetCookie EMGR ()](https://github.com/microsoft/ApplicationInsights-JS/blob/master/shared/AppInsightsCore/src/JavaScriptSDK.Interfaces/ICookieMgr.ts). set ("MyCookie", "% 20encoded% 20value");
- appınsights. [GetCookie EMGR ()](https://github.com/microsoft/ApplicationInsights-JS/blob/master/shared/AppInsightsCore/src/JavaScriptSDK.Interfaces/ICookieMgr.ts). Get ("MyCookie");
- appınsights. [GetCookie EMGR ()](https://github.com/microsoft/ApplicationInsights-JS/blob/master/shared/AppInsightsCore/src/JavaScriptSDK.Interfaces/ICookieMgr.ts). del ("MyCookie");

## <a name="enable-time-on-page-tracking"></a>Sayfa üzerinde zamanı izlemeyi etkinleştir

Ayar olarak `autoTrackPageVisitTime: true` , bir kullanıcının her sayfada harcadığı zaman izlenir. Yeni bir sayfa görünümünde, *önceki* sayfada harcanan sürenin adlı [özel ölçüm](../essentials/metrics-custom-overview.md) olarak gönderildiği süre `PageVisitTime` . Bu özel ölçüm, [Ölçüm Gezgini](../essentials/metrics-getting-started.md) "günlük tabanlı ölçüm" olarak görüntülenebilir.

## <a name="enable-correlation"></a>Bağıntıyı etkinleştir

Bağıntı, dağıtılmış izlemeyi sağlayan ve [uygulama Haritası](../app/app-map.md), [uçtan uca işlem görünümü](../app/app-map.md#go-to-details)ve diğer tanılama araçlarını destekleyen verileri oluşturur ve gönderir.

Aşağıdaki örnek, aşağıdaki senaryoya özgü notlarla bağıntı sağlamak için gereken tüm olası konfigürasyonları göstermektedir:

```javascript
// excerpt of the config section of the JavaScript SDK snippet with correlation
// between client-side AJAX and server requests enabled.
cfg: { // Application Insights Configuration
    instrumentationKey: "YOUR_INSTRUMENTATION_KEY_GOES_HERE"
    disableFetchTracking: false,
    enableCorsCorrelation: true,
    enableRequestHeaderTracking: true,
    enableResponseHeaderTracking: true,
    correlationHeaderExcludedDomains: ['myapp.azurewebsites.net', '*.queue.core.windows.net']
    /* ...Other Configuration Options... */
}});
</script>

``` 

İstemcinin iletişim kurduğu üçüncü taraf sunuculardan herhangi biri `Request-Id` ve `Request-Context` üst bilgileri kabul edemez ve yapılandırmalarını güncelleştiremezsiniz, bunları yapılandırma özelliği aracılığıyla dışlama listesine koymanız gerekir `correlationHeaderExcludeDomains` . Bu özellik joker karakterleri destekler.

Sunucu tarafı, bu üst bilgilerle bağlantıları kabul edebilmelidir. `Access-Control-Allow-Headers`Sunucu tarafında yapılandırmaya bağlı olarak, genellikle ve el ile ekleyerek sunucu tarafı listesini genişletmek gereklidir `Request-Id` `Request-Context` .

Erişim-denetim-Izin-üst bilgiler: `Request-Id` , `Request-Context` , `<your header>`

> [!NOTE]
> 2020 veya sonraki sürümlerde yayınlanan OpenTelemtry veya Application Insights SDK 'Ları kullanıyorsanız, [WC3 TraceContext](https://www.w3.org/TR/trace-context/)kullanmanızı öneririz. Yapılandırma kılavuzunu [buradan](../app/correlation.md#enable-w3c-distributed-tracing-support-for-web-apps)görebilirsiniz.

## <a name="single-page-applications"></a>Tek sayfalı uygulamalar

Varsayılan olarak, bu SDK tek sayfalı uygulamalarda oluşan durum tabanlı yol **değiştirmeyi işlemez.** Tek sayfalı uygulamanız için otomatik yönlendirme değişikliği izlemeyi etkinleştirmek için, `enableAutoRouteTracking: true` Kurulum yapılandırmanıza ekleyebilirsiniz.

Şu anda, bu SDK ile başlatabilmeniz için ayrı bir yanıt verme [eklentisi](javascript-react-plugin.md)sunuyoruz. Ayrıca, sizin için yol değişikliği izlemeyi da gerçekleştirecek ve diğer tepki verme açısından özel telemetri toplayacaktır.
> [!NOTE]
> `enableAutoRouteTracking: true`Yalnızca tepki verme eklentisini kullanmıyorsanız  kullanın. Her ikisi de yol değiştiğinde yeni PageViews gönderebilir. Her ikisi de etkinse, yinelenen PageViews gönderilebilir.

## <a name="extensions"></a>Uzantılar

| Uzantılar |
|---------------|
| [React](javascript-react-plugin.md)|
| [React Native](javascript-react-native-plugin.md)|
| [Angular](javascript-angular-plugin.md)|
| [Analiz otomatik koleksiyonu ' na tıklayın](javascript-click-analytics-plugin.md)|

## <a name="explore-browserclient-side-data"></a>Tarayıcı/istemci tarafı verilerini keşfet

Bir tarayıcı/istemci tarafı verileri, **ölçümler** 'e giderek ve ilgilendiğiniz ölçümleri tek bir ekleyerek görüntüleyebilirsiniz:

![Bir Web uygulaması için ölçüm verilerinin grafik görünümünü gösteren Application Insights ölçümler sayfasının ekran görüntüsü.](./media/javascript/page-view-load-time.png)

Ayrıca, portaldaki tarayıcı deneyimi aracılığıyla JavaScript SDK 'sindeki verilerinizi görüntüleyebilirsiniz.

**Tarayıcı** ' yı seçin ve ardından **hatalara** veya **performans**' ı seçin.

![Web uygulamanız için görüntüleyebileceğiniz ölçülere tarayıcı hatalarının veya tarayıcı performansının nasıl ekleneceğini gösteren Application Insights tarayıcı sayfasının ekran görüntüsü.](./media/javascript/browser.png)

### <a name="performance"></a>Performans

![Bir Web uygulaması için Işlem ölçümlerinin grafik ekranların gösterildiği Application Insights performans sayfasının ekran görüntüsü.](./media/javascript/performance-operations.png)

### <a name="dependencies"></a>Bağımlılıklar

![Bir Web uygulaması için bağımlılık ölçümlerinin grafik görüntülenmesini gösteren Application Insights performans sayfasının ekran görüntüsü.](./media/javascript/performance-dependencies.png)

### <a name="analytics"></a>Analiz

JavaScript SDK 'Sı tarafından toplanan telemetrinizi sorgulamak için **Günlükler (Analiz) Içinde görüntüle** düğmesini seçin. Bir ifadesini ekleyerek `where` `client_Type == "Browser"` yalnızca JavaScript SDK 'dan verileri görürsünüz ve diğer SDK 'lar tarafından toplanan tüm sunucu tarafı telemetri hariç tutulur.
 
```kusto
// average pageView duration by name
let timeGrain=5m;
let dataset=pageViews
// additional filters can be applied here
| where timestamp > ago(1d)
| where client_Type == "Browser" ;
// calculate average pageView duration for all pageViews
dataset
| summarize avg(duration) by bin(timestamp, timeGrain)
| extend pageView='Overall'
// render result in a chart
| render timechart
```

### <a name="source-map-support"></a>Kaynak eşleme desteği

Özel durum telemetrinizin mini ' çağrı yığını 'i Azure Portal için küçültülmüş olabilir. Özel durum ayrıntıları panelindeki tüm mevcut tümleştirmeler, yeni bir küçültülmüş Callstack ile birlikte çalışacaktır.

#### <a name="link-to-blob-storage-account"></a>BLOB depolama hesabı bağlantısı

Çağrı yığınlarını otomatik olarak kaldırmak için Application Insights kaynağınızı kendi Azure Blob depolama kapsayıcınıza bağlayabilirsiniz. Başlamak için bkz. [otomatik kaynak eşleme desteği](./source-map-support.md).

### <a name="drag-and-drop"></a>Sürükleyip bırakma

1. "Uçtan uca işlem ayrıntılarını" görüntülemek için Azure portal bir özel durum telemetrisi öğesi seçin
2. Hangi kaynak haritalarının bu çağrı yığınına karşılık geldiğini belirler. Kaynak eşlemesinin bir yığın çerçevesinin kaynak dosyasıyla eşleşmesi gerekir, ancak şununla sondan düzeltildi `.map`
3. Kaynak haritalarını, ![ bir yapı klasöründen bir derleme klasöründen Azure Portal yığın üzerine çağrı yığınına sürükleyip bırakma hakkında bir animasyonlu görüntüde Azure Portal çağrı yığınına sürükleyip bırakın.](https://i.imgur.com/Efue9nU.gif)

### <a name="application-insights-web-basic"></a>Application Insights Web temel

Hafif bir deneyim için Application Insights temel sürümünü yükleyebilirsiniz
```
npm i --save @microsoft/applicationinsights-web-basic
```
Bu sürüm, en az özellik ve işlevlere sahiptir ve uygun gördüğünüz şekilde oluşturmak için size dayanır. Örneğin, hiçbir yeniden koleksiyon (yakalanamayan özel durumlar, AJAX vb.) yoktur. , Vb. gibi belirli telemetri türlerini göndermek için API 'Ler `trackTrace` `trackException` Bu sürüme dahil değildir, bu nedenle kendi sarmalayıcı sağlamanız gerekir. Kullanılabilir tek API `track` . [Örnek](https://github.com/Azure-Samples/applicationinsights-web-sample1/blob/master/testlightsku.html) burada bulunur.

## <a name="examples"></a>Örnekler

Çalıştırılabilir örnekler için bkz. [JAVASCRIPT SDK örnekleri Application Insights](https://github.com/Azure-Samples?q=applicationinsights-js-demo).

## <a name="upgrading-from-the-old-version-of-application-insights"></a>Application Insights eski sürümünden yükseltme

SDK v2 sürümündeki son değişiklikler:
- Daha iyi API imzaları sağlamak için, trackPageView ve trackException gibi bazı API çağrılarından bazıları güncelleştirilmiştir. Internet Explorer 8 ve tarayıcının önceki sürümlerinde çalıştırmak desteklenmez.
- Telemetri zarfı, veri şeması güncelleştirmeleri nedeniyle alan adı ve yapı değişikliklerine sahiptir.
- Taşındı `context.operation` `context.telemetryTrace` . Bazı alanlar da değiştirildi ( `operation.id`  -->  `telemetryTrace.traceID` ).
  - Geçerli sayfa görüntülemesi kimliğini (örneğin, spa uygulamalarında) el ile yenilemek için kullanın `appInsights.properties.context.telemetryTrace.traceID = Microsoft.ApplicationInsights.Telemetry.Util.generateW3CId()` .
    > [!NOTE]
    > Daha önce kullandığınız izleme KIMLIĞINI benzersiz tutmak için `Util.newId()` artık ' i kullanın `Util.generateW3CId()` . Her iki sonuç de işlem KIMLIĞI ' ni sonlandırın.

Geçerli Application Insights ÜRETIM SDK 'sını (1.0.20) kullanıyorsanız ve yeni SDK 'nın çalışma zamanında çalışıp çalışmadığını görmek istiyorsanız, URL 'YI geçerli SDK yükleme senaryonuza bağlı olarak güncelleştirin.

- CDN senaryosu aracılığıyla indir: Şu anda kullandığınız kod parçacığını aşağıdaki URL 'ye işaret etmek için güncelleştirin:
   ```
   "https://js.monitor.azure.com/scripts/b/ai.2.min.js"
   ```

- NPM senaryosu: `downloadAndSetup` CDN 'den tam ApplicationInsights betiğini indirme ve izleme anahtarıyla başlatma çağrısı:

   ```ts
   appInsights.downloadAndSetup({
     instrumentationKey: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx",
     url: "https://js.monitor.azure.com/scripts/b/ai.2.min.jss"
     });
   ```

İzleme telemetrinin beklendiği gibi çalıştığını doğrulamak için dahili ortamda test edin. Tümü çalışırsa, API imzalarınızı SDK v2 sürümüne uygun şekilde güncelleştirin ve üretim ortamlarınızda dağıtın.

## <a name="sdk-performanceoverhead"></a>SDK performansı/ek yükü

Yalnızca 36 KB gdaraltılmış ve yalnızca ~ 15 MS 'yi başlatmak için Application Insights, Web sitenize daha fazla sayıda loadtime ekler. Kod parçacığını kullanarak, kitaplığın minimum bileşenleri hızlı bir şekilde yüklenir. Bu sırada, tam komut dosyası arka planda indirilir.

Betik CDN 'den indirilirken, sayfanızın tüm izlenmesi sıraya alınır. İndirilen betik zaman uyumsuz olarak başlatıldıktan sonra, kuyruğa alınan tüm olaylar izlenir. Sonuç olarak, sayfanızın tüm yaşam döngüsü boyunca hiçbir Telemetriyi kaybetmezsiniz. Bu kurulum işlemi, sayfanıza, kullanıcılarınız için görünmeyen sorunsuz bir analiz sistemi sağlar.

> Özet:
> - ![NPM sürümü](https://badge.fury.io/js/%40microsoft%2Fapplicationinsights-web.svg)
> - ![gzip sıkıştırılmış boyutu](https://img.badgesize.io/https://js.monitor.azure.com/scripts/b/ai.2.min.js.svg?compression=gzip)
> - **15 MS** genel başlatma süresi
> - Sayfanın yaşam döngüsü boyunca **sıfır** izleme kaçırıldı

## <a name="browser-support"></a>Tarayıcı desteği

![Chrome](https://raw.githubusercontent.com/alrra/browser-logos/master/src/chrome/chrome_48x48.png) | ![Firefox](https://raw.githubusercontent.com/alrra/browser-logos/master/src/firefox/firefox_48x48.png) | ![IE](https://raw.githubusercontent.com/alrra/browser-logos/master/src/edge/edge_48x48.png) | ![Opera](https://raw.githubusercontent.com/alrra/browser-logos/master/src/opera/opera_48x48.png) | ![Safari](https://raw.githubusercontent.com/alrra/browser-logos/master/src/safari/safari_48x48.png)
--- | --- | --- | --- | --- |
Chrome en son ✔ |  Firefox en son ✔ | IE 9 + & Edge ✔<br>IE 8 uyumlu | Opera en son ✔ | Safari en son ✔ |

## <a name="es3ie8-compatibility"></a>ES3/ıE8 uyumluluğu

Bir SDK olarak, müşterilerinin kullandıkları tarayıcıları denetleyemeyebilecek çok sayıda kullanıcı vardır. Bu nedenle, bu SDK 'nın "iş" olmaya devam ettiğinden ve daha eski bir tarayıcı tarafından yüklendiğinde JS yürütmesini bozmadığından emin olunması gerekir. IE8 ve daha eski nesil (ES3) tarayıcılarını desteklememe konusunda ideal olmaya devam ederken, "iş" için sayfa gerektirmeye devam eden çok sayıda büyük müşteri/Kullanıcı vardır ve bu durum, son kullanıcıların kullanmak için hangi tarayıcıyı seçebileceğini denetleyebilir

Bu, yalnızca en düşük ortak özellik kümesini desteklenebiliyoruz; yalnızca ES3 kod uyumluluğunu sürdürmemiz ve yeni özellikler eklenirken, ES3 JavaScript ayrıştırmayı kesintiye uğramayacak ve isteğe bağlı bir özellik olarak eklenen bir şekilde eklenmeleri gerekir.

[IE8 desteğiyle ilgili tam Ayrıntılar için bkz. GitHub](https://github.com/Microsoft/ApplicationInsights-JS#es3ie8-compatibility)

## <a name="open-source-sdk"></a>Açık kaynaklı SDK

Application Insights JavaScript SDK 'Sı, kaynak kodu görüntülemek veya projeye katkıda bulunmak için açık kaynaktır ve [resmi GitHub deposunu](https://github.com/Microsoft/ApplicationInsights-JS)ziyaret edebilir. 

En son güncelleştirmeler ve hata düzeltmeleri için [sürüm notlarına bakın](./release-notes.md).

## <a name="next-steps"></a><a name="next"></a> Sonraki adımlar
* [Kullanımı İzleme](usage-overview.md)
* [Özel etkinlikler ve ölçümler](api-custom-events-metrics.md)
* [Build-measure-learn](usage-overview.md)
* [SDK yükleme hatası sorunlarını giderme](javascript-sdk-load-failure.md)
