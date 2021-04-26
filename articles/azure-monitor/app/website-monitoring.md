---
title: 'Hızlı başlangıç: Azure Izleyici Application Insights Web sitelerini Izleme'
description: Bu hızlı başlangıçta, Azure Izleyici Application Insights ile istemci/tarayıcı tarafı Web sitesi izlemeyi ayarlamayı öğrenin.
ms.topic: quickstart
ms.date: 03/19/2021
ms.custom: mvc
ms.openlocfilehash: 0e10db39c8dbbf81087d696cfbb5b2ded1ae79ac
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104654943"
---
# <a name="quickstart-start-monitoring-your-website-with-azure-monitor-application-insights"></a>Hızlı başlangıç: Azure Izleyici Application Insights Web sitenizi izlemeye başlayın

Bu hızlı başlangıçta, Web sitenize açık kaynaklı Application Insights JavaScript SDK 'Sı eklemeyi öğreneceksiniz. Ayrıca, Web sitenize ziyaretçi için istemci/tarayıcı tarafı deneyimini nasıl daha iyi anlayacağınızı öğreneceksiniz.

Azure İzleyici Application Insights ile web sitenizi kullanılabilirlik, performans ve kullanım bakımından kolayca izleyebilirsiniz. Ayrıca, bir kullanıcının bildirmesini beklemeden uygulamanızdaki hataları hızlıca tanımlayıp tespit edebilirsiniz. Application Insights hem sunucu tarafı izleme hem de istemci/tarayıcı tarafı izleme özellikleri sağlar.

## <a name="prerequisites"></a>Önkoşullar

* Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* JavaScript SDK Application Insights ekleyebileceğiniz bir Web sitesi.

## <a name="enable-application-insights"></a>Application Insights'ı etkinleştirme

Application Insights, şirket içinde veya bulutta çalışan İnternet 'e bağlı herhangi bir uygulamadan telemetri verilerini toplayabilir. Bu verileri görüntülemek için aşağıdaki adımları kullanın:

1. [Azure portalında](https://portal.azure.com/) oturum açın.
1. Application Insights **kaynak**  >  **yönetimi araçları** oluştur ' u seçin  >  .

   > [!NOTE]
   >İlk kez bir Application Insights kaynağı oluşturuyorsanız, bkz. [Application Insights kaynağı oluşturma](./create-new-resource.md).
1. Yapılandırma kutusu göründüğünde, giriş alanlarını doldurmak için aşağıdaki tabloyu kullanın:

    | Ayarlar        | Değer           | Açıklama  |
   | ------------- |:-------------|:-----|
   | **Ad**      | Genel Olarak Benzersiz Değer | İzlemekte olduğunuz uygulamayı tanımlayan ad. |
   | **Kaynak Grubu**     | myResourceGroup      | Application Insights verileri barındıracak yeni kaynak grubunun adı. Yeni bir kaynak grubu oluşturabilir veya var olan bir grup kullanabilirsiniz. |
   | **Konum** | Doğu ABD | Size yakın bir konum seçin veya uygulamanızın nerede barındırıldığını görürsünüz. |
1. **Oluştur**’u seçin.

## <a name="create-an-html-file"></a>HTML dosyası oluşturma

1. Yerel bilgisayarınızda ``hello_world.html`` adlı bir dosya oluşturun. Bu örnek için, dosyayı C sürücüsünün kökünde oluşturun, böylece benzer şekilde görünür ``C:\hello_world.html`` .
1. Aşağıdaki betiği kopyalayıp içine yapıştırın ``hello_world.html`` :

    ```html
    <!DOCTYPE html>
    <html>
    <head>
    <title>Azure Monitor Application Insights</title>
    </head>
    <body>
    <h1>Azure Monitor Application Insights Hello World!</h1>
    <p>You can use the Application Insights JavaScript SDK to perform client/browser-side monitoring of your website. To learn about more advanced JavaScript SDK configurations, visit the <a href="https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md" title="API Reference">API reference</a>.</p>
    </body>
    </html>
    ```

## <a name="configure-application-insights-sdk"></a>SDK Application Insights yapılandırma

1. **Genel bakış** ' ı seçin ve ardından uygulamanızın **bağlantı dizesini** kopyalayın. Bu örnek için, bağlantı dizesinin yalnızca Izleme anahtarı bölümüne ihtiyacımız vardır `InstrumentationKey=00000000-0000-0000-0000-000000000000;` .

    :::image type="content" source="media/website-monitoring/keys.png" alt-text="İzleme anahtarı ve bağlantı dizesi içeren Genel Bakış sayfasının ekran görüntüsü.":::

1. Aşağıdaki betiği, ``hello_world.html`` kapatma etiketinden önce dosyanıza ekleyin ``</head>`` :

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
        connectionString:"InstrumentationKey=YOUR_INSTRUMENTATION_KEY_GOES_HERE;" 
        /* ...Other Configuration Options... */
    }});
    </script>
    ```

    > [!NOTE]
    > Geçerli kod parçacığı (yukarıda listelenen) sürümü "5", sürüm kod parçacığında ZF: "#" olarak kodlanır ve [geçerli sürüm ve yapılandırma ayrıntıları GitHub 'da kullanılabilir](https://go.microsoft.com/fwlink/?linkid=2156318).

1. ``hello_world.html`` dosyasını düzenleyerek izleme anahtarınızı ekleyin.

1. ``hello_world.html`` dosyasını yerel tarayıcı oturumunda açın. Bu eylem tek sayfalı bir görünüm oluşturur. Birden çok test-sayfa görünümü oluşturmak için tarayıcınızı yenileyebilirsiniz.

## <a name="monitor-your-website-in-the-azure-portal"></a>Web sitenizi Azure portal izleyin

1. Çalışmakta olan uygulamanızın ayrıntılarını görüntülemek için Azure portal Application Insights **genel bakış** sayfasını yeniden açın. **Genel bakış** sayfası, izleme anahtarınızı aldığınız yerdir.

   Genel bakış sayfasındaki dört varsayılan grafik, sunucu tarafı uygulama verilerini kapsar. JavaScript SDK 'Sı ile istemci/tarayıcı tarafı etkileşimlerini seçtiğimiz için, sunucu tarafı SDK 'Sı yüklü olmadığı müddetçe bu belirli görünüm uygulanmaz.

1. **Günlükleri** seçin.  Bu eylem, Application Insights tarafından toplanan tüm verileri analiz etmek için zengin bir sorgu dili sağlayan **günlükleri** açar. İstemci tarafı tarayıcı istekleriyle ilgili verileri görüntülemek için aşağıdaki sorguyu çalıştırın:

    ```kusto
    // average pageView duration by name
    let timeGrain=1s;
    let dataset=pageViews
    // additional filters can be applied here
    | where timestamp > ago(15m)
    | where client_Type == "Browser" ;
    // calculate average pageView duration for all pageViews
    dataset
    | summarize avg(duration) by bin(timestamp, timeGrain)
    | extend pageView='Overall'
    // render result in a chart
    | render timechart
    ```

   :::image type="content" source="media/website-monitoring/log-query.png" alt-text="Bir süre içinde kullanıcı isteklerinin Log Analytics grafiğinin ekran görüntüsü.":::

1. **Genel Bakış** sayfasına geri gidin. **Araştır** üst bilgisi altında **performans** ' ı seçin ve **tarayıcı** sekmesini seçin.  Web sitenizin performansıyla ilgili ölçümler görüntülenir. Web sitenizde hataların ve özel durumların çözümlenmesi için karşılık gelen bir görünüm vardır. [Uçtan uca işlem ayrıntılarına](./transaction-diagnostics.md)erişmek için **örnekler** seçebilirsiniz.

     :::image type="content" source="media/website-monitoring/performance.png" alt-text="Tarayıcı ölçümleri grafiğinin performans sekmesinin ekran görüntüsü.":::

1. Ana Application Insights menüsünde, **kullanım** üst bilgisi altında [**Kullanıcılar**](./usage-segmentation.md) ' ı seçerek [Kullanıcı davranışı analizi araçlarını](./usage-overview.md)keşfetmeye başlayabilirsiniz. Tek bir makineden test edildiğimiz için yalnızca bir kullanıcının verilerini görebiliriz.

1. Birden çok sayfa içeren daha karmaşık bir Web sitesi için, ziyaretçilerin web sitenizin çeşitli kısımlarını ele aldığı patika izlemek için [**Kullanıcı akışları**](./usage-flows.md) aracını kullanabilirsiniz.

Web sitelerini izlemeye yönelik daha gelişmiş yapılandırma hakkında daha fazla bilgi edinmek için bkz. [JAVASCRIPT SDK API başvurusu](./javascript.md).

## <a name="clean-up-resources"></a>Kaynakları temizleme

Ek hızlı başlangıçlarla veya öğreticilerle çalışmaya devam etmeyi planlıyorsanız, bu hızlı başlangıçta oluşturulan kaynakları temizlemeyin. Aksi takdirde, Azure portal bu hızlı başlangıç tarafından oluşturulan tüm kaynakları silmek için aşağıdaki adımları kullanın.

> [!NOTE]
> Var olan bir kaynak grubunu kullandıysanız, aşağıdaki yönergeler çalışmaz. Bunun yerine, yalnızca bireysel Application Insights kaynağını silebilirsiniz. Bir kaynak grubunu sildiğinizde, bu grubun üyesi olan tüm kaynakların da silindiğini unutmayın.

1. Azure portal sol menüsünde **kaynak grupları**' nı seçin ve ardından **myresourcegroup** ' ı veya geçici kaynak grubunuzun adını seçin.
1. Kaynak grubu sayfanızda **Sil**' i seçin, metin kutusuna **myresourcegroup** yazın ve ardından **Sil**' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Performans sorunlarını bulma ve tanılama](../logs/log-query-overview.md)

