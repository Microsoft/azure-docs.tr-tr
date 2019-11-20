---
title: Bir SharePoint sitesini Application Insights ile izleme
description: Yeni bir izleme anahtarı ile yeni bir uygulamayı izlemeye başlama
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 07/11/2018
ms.openlocfilehash: 271ef590d7cdbefc824a7f80aef110b0c08b6d6d
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899877"
---
# <a name="monitor-a-sharepoint-site-with-application-insights"></a>Bir SharePoint sitesini Application Insights ile izleme
Azure Application Insights, uygulamalarınızın kullanılabilirliğini, performansını ve kullanımını izler. Burada, bir SharePoint sitesi için nasıl ayarlayacağınızı öğreneceksiniz.

## <a name="create-an-application-insights-resource"></a>Application Insights kaynağı oluşturma
[Azure portalında](https://portal.azure.com) yeni bir Application Insights kaynağı oluşturun. Uygulama türü olarak ASP.NET’i seçin.

![Özellikler'e tıklayın, anahtarı seçin ve ctrl + C tuşlarına basın](./media/sharepoint/001.png)

Uygulamanızla ilgili performans ve kullanım verilerini açılan pencerede görürsünüz. Azure’da bir sonraki oturum açışınızda bu pencereye dönmek için başlangıç ekranında bunun bir kutucuğunu bulmanız gerekir. Alternatif olarak, pencereyi bulmak için Gözat'a tıklayın.

## <a name="add-the-script-to-your-web-pages"></a>Betiği web sayfalarınıza ekleme

```HTML
<!-- 
To collect user behavior analytics tools about your application, 
insert the following script into each page you want to track.
Place this code immediately before the closing </head> tag,
and before any other scripts. Your first data will appear 
automatically in just a few seconds.
-->
<script type="text/javascript">
var appInsights=window.appInsights||function(a){
  function b(a){c[a]=function(){var b=arguments;c.queue.push(function(){c[a].apply(c,b)})}}var c={config:a},d=document,e=window;setTimeout(function(){var b=d.createElement("script");b.src=a.url||"https://az416426.vo.msecnd.net/scripts/a/ai.0.js",d.getElementsByTagName("script")[0].parentNode.appendChild(b)});try{c.cookie=d.cookie}catch(a){}c.queue=[];for(var f=["Event","Exception","Metric","PageView","Trace","Dependency"];f.length;)b("track"+f.pop());if(b("setAuthenticatedUserContext"),b("clearAuthenticatedUserContext"),b("startTrackEvent"),b("stopTrackEvent"),b("startTrackPage"),b("stopTrackPage"),b("flush"),!a.disableExceptionTracking){f="onerror",b("_"+f);var g=e[f];e[f]=function(a,b,d,e,h){var i=g&&g(a,b,d,e,h);return!0!==i&&c["_"+f](a,b,d,e,h),i}}return c
  }({
      instrumentationKey:"<your instrumentation key>"
  });
  
window.appInsights=appInsights,appInsights.queue&&0===appInsights.queue.length&&appInsights.trackPageView();
</script>
```

Komut dosyasını, izlemek istediğiniz her sayfanın &lt;/Head&gt; etiketinden hemen önce ekleyin. Web sitenizde bir ana sayfa varsa, betiği buraya yerleştirebilirsiniz. Örneğin, bir ASP.NET MVC projesinde View\Shared\_Layout.cshtml’ye koyarsınız

Betikte, telemetriyi Application Insights kaynağınıza yönlendiren izleme anahtarı bulunur.

### <a name="add-the-code-to-your-site-pages"></a>Kodu site sayfalarınıza ekleme
#### <a name="on-the-master-page"></a>Ana sayfada
Sitenin ana sayfasını düzenleyebiliyorsanız, bu sayede sitedeki her sayfa için izleme olanağına sahip olursunuz.

Ana sayfayı inceleyin ve SharePoint Designer’ı veya başka bir düzenleyiciyi kullanarak düzenleyin.

![](./media/sharepoint/03-master.png)

Kodu </head> etiketinin hemen önüne ekleyin. 

![](./media/sharepoint/04-code.png)

#### <a name="or-on-individual-pages"></a>Veya belirli sayfalarda
Sınırlı sayıda sayfayı izlemek için betiği her sayfaya ayrı ayrı ekleyin. 

Bir web parçası ekleyip kod parçacığını buna ekleyin.

![](./media/sharepoint/05-page.png)

## <a name="view-data-about-your-app"></a>Uygulamanızla ilgili verileri görüntüleme
Uygulamanızı yeniden dağıtın.

Uygulamanızın [Azure portalındaki](https://portal.azure.com) dikey penceresine dönün.

İlk olaylar Arama’da görünür. 

![](./media/sharepoint/09-search.png)

Daha fazla veri bekliyorsanız, birkaç saniye geçtikten sonra Yenile’ye tıklayın.

## <a name="capturing-user-id"></a>Kullanıcı Kimliğini Yakalama
Standart web sayfası kod parçacığı SharePoint’ten kullanıcı kimliğini yakalamaz, ancak küçük bir değişiklikle bunu yapabilirsiniz.

1. Application Insights’taki Temel Bileşenler açılan penceresinden uygulamanızın izleme anahtarını kopyalayın. 

    ![](./media/sharepoint/02-props.png)

1. Aşağıdaki kod parçacığında geçen 'XXXX' ifadesini izleme anahtarıyla değiştirin. 
2. Betiği portaldan edindiğiniz kod parçacığı yerine SharePoint uygulamanıza ekleyin.

```


<SharePoint:ScriptLink ID="ScriptLink1" name="SP.js" runat="server" localizable="false" loadafterui="true" /> 
<SharePoint:ScriptLink ID="ScriptLink2" name="SP.UserProfiles.js" runat="server" localizable="false" loadafterui="true" /> 

<script type="text/javascript"> 
var personProperties; 

// Ensure that the SP.UserProfiles.js file is loaded before the custom code runs. 
SP.SOD.executeOrDelayUntilScriptLoaded(getUserProperties, 'SP.UserProfiles.js'); 

function getUserProperties() { 
    // Get the current client context and PeopleManager instance. 
    var clientContext = new SP.ClientContext.get_current(); 
    var peopleManager = new SP.UserProfiles.PeopleManager(clientContext); 

    // Get user properties for the target user. 
    // To get the PersonProperties object for the current user, use the 
    // getMyProperties method. 

    personProperties = peopleManager.getMyProperties(); 

    // Load the PersonProperties object and send the request. 
    clientContext.load(personProperties); 
    clientContext.executeQueryAsync(onRequestSuccess, onRequestFail); 
} 

// This function runs if the executeQueryAsync call succeeds. 
function onRequestSuccess() { 
var appInsights=window.appInsights||function(config){
function s(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},r=document,f=window,e="script",o=r.createElement(e),i,u;for(o.src=config.url||"//az416426.vo.msecnd.net/scripts/a/ai.0.js",r.getElementsByTagName(e)[0].parentNode.appendChild(o),t.cookie=r.cookie,t.queue=[],i=["Event","Exception","Metric","PageView","Trace"];i.length;)s("track"+i.pop());return config.disableExceptionTracking||(i="onerror",s("_"+i),u=f[i],f[i]=function(config,r,f,e,o){var s=u&&u(config,r,f,e,o);return s!==!0&&t["_"+i](config,r,f,e,o),s}),t
    }({
        instrumentationKey:"XXXX"
    });
    window.appInsights=appInsights;
    appInsights.trackPageView(document.title,window.location.href, {User: personProperties.get_displayName()});
} 

// This function runs if the executeQueryAsync call fails. 
function onRequestFail(sender, args) { 
} 
</script> 


```



## <a name="next-steps"></a>Sonraki Adımlar
* Sitenizin kullanılabilirliğini izlemek için [web testleri](../../azure-monitor/app/monitor-web-app-availability.md).
* Diğer uygulama türleri için [Application Insights](../../azure-monitor/app/app-insights-overview.md).

<!--Link references-->


