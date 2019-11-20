---
title: 'Hızlı başlangıç: Azure İzleyici Application Insights ile web sitelerini izleme | Microsoft Docs'
description: Azure İzleyici Application Insights hizmetiyle hızlı bir şekilde istemci/tarayıcı tarafı web sitesi izleme kurulumu gerçekleştirme yönergeleri
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: quickstart
author: mrbullwinkle
ms.author: mbullwin
ms.date: 07/15/2019
ms.custom: mvc
ms.openlocfilehash: eecc3d739cb5500abdbd04a9aaca1ddcf86e352b
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899262"
---
# <a name="start-monitoring-your-website"></a>Web sitenizi izlemeye başlama

Azure İzleyici Application Insights ile web sitenizi kullanılabilirlik, performans ve kullanım bakımından kolayca izleyebilirsiniz. Ayrıca, bir kullanıcının bildirmesini beklemeden uygulamanızdaki hataları hızlıca tanımlayıp tespit edebilirsiniz. Application Insights hem sunucu tarafı izleme hem de istemci/tarayıcı tarafı izleme özellikleri sunar.

Bu hızlı başlangıç, Web sitenize ziyaretçi için istemci/tarayıcı tarafı deneyimini anlamanıza olanak sağlayan [Açık kaynaklı Application Insights JavaScript SDK 'sını](https://github.com/Microsoft/ApplicationInsights-JS) ekleme konusunda size rehberlik eder.

## <a name="prerequisites"></a>Önkoşullar

Bu hızlı başlangıcı tamamlamak için:

- Bir Azure Aboneliğine sahip olmanız gerekir.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

[Azure Portal](https://portal.azure.com/)’ında oturum açın.

## <a name="enable-application-insights"></a>Application Insights'ı etkinleştirme

Application Insights, şirket içinde veya bulutta çalışan İnternet’e bağlı herhangi bir uygulamadan telemetri verilerini toplayabilir. Bu verileri görüntülemeyi başlatmak için aşağıdaki adımları kullanın.

1. **Kaynak oluştur** > **Yönetim araçları** > **Application Insights** seçeneğini belirleyin.

   > [!NOTE]
   >İlk kez bir Application Insights kaynağı oluşturuyorsanız [Application Insights kaynak oluştur](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) makalesini ziyaret ederek daha fazla bilgi edinebilirsiniz.

   Bir yapılandırma kutusu görünür. Giriş alanlarını doldurmak için aşağıdaki tabloyu kullanın.

    | Ayarlar        | Değer           | Açıklama  |
   | ------------- |:-------------|:-----|
   | **Adı**      | Genel Olarak Benzersiz Değer | İzlemekte olduğunuz uygulamayı tanımlayan ad |
   | **Kaynak Grubu**     | myResourceGroup      | App Insights verilerini barındıran yeni kaynak grubunun adı |
   | **Konum** | Doğu ABD | Yakınınızda bulunan veya uygulamanızın barındırıldığı konumun yakınında olan bir konum seçin |

2. **Oluştur**’a tıklayın.

## <a name="create-an-html-file"></a>HTML dosyası oluşturma

1. Yerel bilgisayarınızda ``hello_world.html`` adlı bir dosya oluşturun. Bu örnekte dosya, C: sürücüsünün köküne eklenecek ve ``C:\hello_world.html`` yoluna sahip olacaktır.
2. Aşağıdaki betiği ``hello_world.html`` dosyasına kopyalayın:

    ```html
    <!DOCTYPE html>
    <html>
    <head>
    <title>Azure Monitor Application Insights</title>
    </head>
    <body>
    <h1>Azure Monitor Application Insights Hello World!</h1>
    <p>You can use the Application Insights JavaScript SDK to perform client/browser-side monitoring of your website. To learn about more advanced JavaScript SDK configurations visit the <a href="https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md" title="API Reference">API reference</a>.</p>
    </body>
    </html>
    ```

## <a name="configure-app-insights-sdk"></a>App Insights SDK’sını Yapılandırma

1. **Genel Bakış** > **Temel Bilgiler**’i seçin > Uygulamanızın **İzleme Anahtarı**’nı kopyalayın.

   ![Yeni App Insights kaynağı formu](media/website-monitoring/instrumentation-key-001.png)

2. Aşağıdaki betiği ``hello_world.html`` dosyanıza, ``</head>`` kapanış etiketinin öncesine ekleyin:

   ```javascript
    <script type="text/javascript">
      var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t}(
      {
         instrumentationKey:"INSTRUMENTATION_KEY"
      }
      );window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
   </script>
   ```

3. ``hello_world.html`` dosyasını düzenleyerek izleme anahtarınızı ekleyin.

4. ``hello_world.html`` dosyasını yerel tarayıcı oturumunda açın. Tek sayfalı bir görünüm oluşturulur. Tarayıcınızı yenileyerek birden fazla test sayfası görünümü oluşturabilirsiniz.

## <a name="start-monitoring-in-the-azure-portal"></a>Azure portalında izlemeyi başlatma

1. Artık izleme anahtarınızı aldığınız Application Insights **Genel Bakış** sayfasını yeniden açarak o anda çalışan uygulamanıza ilişkin ayrıntıları görüntüleyebilirsiniz. Genel bakış sayfasındaki dört varsayılan grafik, sunucu tarafı uygulama verilerini kapsar. JavaScript SDK ile istemci/tarayıcı tarafı etkileşimleri izlediğimiz için sunucu tarafı SDK'sı yüklü olmadığı sürece bu görünüm işimize yaramayacaktır.

2. ![Uygulama Haritası simgesi](media/website-monitoring/006.png) **Analiz** öğesine tıklayın.  Bu işlem, Application Insights tarafından toplanan tüm verileri analiz etmeye yönelik zengin bir sorgu dili sağlayan **Analiz** sayfasını açar. İstemci tarafı tarayıcı istekleriyle ilgili verileri görüntülemek için aşağıdaki sorguyu çalıştırın:

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

   ![Belirli bir süre içindeki kullanıcı isteklerinin analiz grafiği](./media/website-monitoring/analytics-query.png)

3. **Genel Bakış** sayfasına geri gidin. **Araştır** başlığındaki **Tarayıcı** girişine tıklayın ve **Performans**'ı seçin. Burada web sitenizle ilgili ölçümleri ve performans verilerini görebilirsiniz. Ayrıca web sitenizdeki hataları ve özel durumları analiz etmek için kullanabileceğiniz bir görünüm de bulunur. **Örnekler**'e tıklayarak işlem ayrıntılarına inebilirsiniz. Açılan sayfadan [uçtan uca işlem ayrıntıları](../../azure-monitor/app/transaction-diagnostics.md) deneyimine ulaşabilirsiniz.

   ![Sunucu ölçüm grafiği](./media/website-monitoring/browser-performance.png)

4. [Kullanıcı davranışı analiz araçlarını](../../azure-monitor/app/usage-overview.md) keşfetmeye başlamak için ana Application Insights menüsündeki **Kullanım** başlığından [**Kullanıcılar**](../../azure-monitor/app/usage-segmentation.md) girişini seçin. Tek bir makineden test gerçekleştirdiğimiz için yalnızca bir kullanıcıya ait veriler görünecektir. Canlı bir web sitesi için kullanıcı dağılımı şu şekilde görünebilir:

     ![Kullanıcı grafiği](./media/website-monitoring/usage-users.png)

5. Birden fazla sayfaya sahip olan daha karmaşık bir web sitesini izliyor olsaydık [**Kullanıcı Akışları**](../../azure-monitor/app/usage-flows.md) aracından da faydalanabilirdik. **Kullanıcı Akışları** aracıyla ziyaretçilerin web sitenizin farklı bölümlerinde gerçekleştirdiği işlemleri görebilirsiniz.

   ![Kullanıcı Akışları görselleştirmesi](./media/website-monitoring/user-flows.png)

Web sitelerini izlemek için kullanabileceğiniz daha gelişmiş yapılandırmalar hakkında bilgi edinmek için [JavaScript SDK API başvurusunu](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md) inceleyin.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Sonraki hızlı başlangıçlar veya öğreticilerle devam etmeyi planlıyorsanız, bu hızlı başlangıçta oluşturulan kaynakları temizlemeyin. Devam etmeyi planlamıyorsanız Azure portalında bu hızlı başlangıç ile oluşturulan tüm kaynakları silmek için aşağıdaki adımları kullanın.

1. Azure portalında sol taraftaki menüden, **Kaynak grupları**’na tıklayın ve ardından **myResourceGroup**’a tıklayın.
2. Kaynak grubu sayfanızda, **Sil**’e tıklayın, metin kutusuna **myResourceGroup** yazın ve ardından **Sil**’e tıklayın.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Performans sorunlarını bulma ve tanılama](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)
