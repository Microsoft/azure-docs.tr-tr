---
title: 'Hızlı başlangıç: Azure Izleyici ile Node.js Izleme Application Insights'
description: Azure Izleyici ile izleme için Node.js bir Web uygulamasını hızlıca ayarlamaya yönelik yönergeler sağlar Application Insights
ms.topic: quickstart
author: lgayhardt
ms.author: lagayhar
ms.date: 07/12/2019
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019, devx-track-js
ms.openlocfilehash: 96a60c88997226516d451124280053d87065e5b6
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106381952"
---
# <a name="quickstart-start-monitoring-your-nodejs-web-application-with-azure-application-insights"></a>Hızlı başlangıç: Node.js Web uygulamanızı Azure Application Insights izlemeye başlayın

Bu hızlı başlangıçta, mevcut bir Node.js Web uygulamasına Node.js için Application Insights SDK sürüm 0,22 ' i eklersiniz.

Azure Application Insights ile web uygulamanızı kullanılabilirlik, performans ve kullanım bakımından kolayca izleyebilirsiniz. Ayrıca, bir kullanıcının bildirmesini beklemeden uygulamanızdaki hataları hızlıca tanımlayıp tespit edebilirsiniz. Sürüm 0.20 SDK yayınından itibaren MongoDB, MySQL ve Redis dahil olmak üzere yaygın üçüncü taraf paketleri izleyebilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

* Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Çalışan bir Node.js uygulaması.

## <a name="enable-application-insights"></a>Application Insights'ı etkinleştirme

Application Insights, şirket içinde veya bulutta çalışıp çalışmadığını, internet 'e bağlı herhangi bir uygulamadan telemetri verilerini toplayabilir. Bu verileri görüntülemeyi başlatmak için aşağıdaki adımları kullanın.

1. [Azure portalında](https://portal.azure.com/) oturum açın.

2. **Kaynak oluştur**  >  **Geliştirici Araçları**  >  **Application Insights** seçin.

   ![Azure Application Insights kaynağı ekleme](./media/nodejs-quick-start/azure-app-insights-create-resource.png)

   > [!NOTE]
   >İlk kez bir Application Insights kaynağı oluşturuyorsanız [Application Insights kaynak oluştur belge oluştur](../app/create-new-resource.md) ' a giderek daha fazla bilgi edinebilirsiniz.

   Bir yapılandırma sayfası görüntülenir; giriş alanlarını doldurun için aşağıdaki tabloyu kullanın. 

    | Ayarlar        | Değer           | Açıklama  |
   | ------------- |:-------------|:-----|
   | **Ad**      | Genel Olarak Benzersiz Değer | İzlemekte olduğunuz uygulamayı tanımlayan ad |
   | **Kaynak Grubu**     | myResourceGroup      | Appınsights verilerini barındıracak yeni kaynak grubunun adı. Yeni bir kaynak grubu oluşturabilir veya var olan bir grup kullanabilirsiniz. |
   | **Konum** | Doğu ABD | Yakınınızda bulunan veya uygulamanızın barındırıldığı konumun yakınında olan bir konum seçin |

3. **Oluştur**’u seçin.

## <a name="configure-appinsights-sdk"></a>Appınsights SDK 'sını yapılandırma

1. **Genel bakış** ' ı seçin ve uygulamanızın **izleme anahtarını** kopyalayın.

   ![Application Insights Izleme anahtarını görüntüleme](./media/nodejs-quick-start/azure-app-insights-instrumentation-key.png)

2. Uygulamanıza Node.js için Application Insights SDK’sı ekleyin. Uygulamanızın kök klasörü çalıştırmasından:

   ```bash
   npm install applicationinsights --save
   ```

3. Uygulamanızın ilk *. js* dosyasını düzenleyin ve aşağıdaki iki satırı betiğinizin en üst kısmına ekleyin. [Node.js hızlı başlangıç uygulamasını](../../app-service/quickstart-nodejs.md)kullanıyorsanız, *index.js* dosyasını değiştirirsiniz. `<instrumentation_key>`Uygulamanızın Insight izleme anahtarıyla değiştirin. 

   ```JavaScript
   const appInsights = require('applicationinsights');
   appInsights.setup('<instrumentation_key>').start();
   ```

4. Uygulamanızı yeniden başlatın.

> [!NOTE]
> Verilerin portalda görünmesi 3-5 dakika sürer. Bu uygulama düşük trafikli bir test uygulaması ise, çoğu ölçümün yalnızca etkin istek veya gerçekleşen işlemler olduğunda yakalandığını aklınızda bulundurun.

## <a name="start-monitoring-in-the-azure-portal"></a>Azure portalında izlemeyi başlatın

1. Artık izleme anahtarınızı aldığınız Application Insights **Genel Bakış** sayfasını yeniden açarak o anda çalışan uygulamanıza ilişkin ayrıntıları görüntüleyebilirsiniz.

   ![Application Insights genel bakış menüsü](./media/nodejs-quick-start/azure-app-insights-overview-menu.png)

2. Uygulama bileşenleriniz arasındaki bağımlılık ilişkilerinin görsel düzeni için **uygulama Haritası** ' nı seçin. Her bileşen yük, performans, hatalar ve uyarılar gibi KPI'leri gösterir.

   ![Uygulama haritasını Application Insights](./media/nodejs-quick-start/azure-app-insights-application-map.png)

3. Analytics 'teki uygulama **Analizi** simgesi ![ uygulama Haritası simgesini seçin ](./media/nodejs-quick-start/azure-app-insights-analytics-icon.png) .  Bu eylem, Application Insights tarafından toplanan tüm verileri analiz etmek için zengin bir sorgu dili sağlayan **Application Insights Analytics**'i açar. Bu örnekte, istek sayısını grafik olarak işleyen bir sorgu oluşturulur. Diğer verileri çözümlemek için kendi sorgularınızı yazabilirsiniz.

   ![Application Insights Analytics grafikleri](./media/nodejs-quick-start/azure-app-insights-analytics-queries.png)

4. **Genel Bakış** sayfasına geri dönüp KPI graflarını inceleyin.  Bu pano, gelen istek sayısı, bu isteklerin süresi ve oluşan hatalar dahil olmak üzere uygulamanızın sistem durumu hakkında istatistikler sağlar.

   ![Application Insights sistem durumu genel bakış zaman çizelgesi grafikleri](./media/nodejs-quick-start/azure-app-insights-health-overview.png)

   **Sayfa Görünümü Yükleme Süresi** grafiğini **istemci tarafı telemetri** verileriyle doldurmak üzere etkinleştirmek için, bu betiği izlemek istediğiniz her sayfaya ekleyin:

   ```HTML
   <!-- 
   To collect user behavior analytics tools about your application, 
   insert the following script into each page you want to track.
   Place this code immediately before the closing </head> tag,
   and before any other scripts. Your first data will appear 
   automatically in just a few seconds.
   -->
   <script type="text/javascript">
     var appInsights=window.appInsights||function(config){
       function i(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},u=document,e=window,o="script",s="AuthenticatedUserContext",h="start",c="stop",l="Track",a=l+"Event",v=l+"Page",y=u.createElement(o),r,f;y.src=config.url||"https://az416426.vo.msecnd.net/scripts/a/ai.0.js";u.getElementsByTagName(o)[0].parentNode.appendChild(y);try{t.cookie=u.cookie}catch(p){}for(t.queue=[],t.version="1.0",r=["Event","Exception","Metric","PageView","Trace","Dependency"];r.length;)i("track"+r.pop());return i("set"+s),i("clear"+s),i(h+a),i(c+a),i(h+v),i(c+v),i("flush"),config.disableExceptionTracking||(r="onerror",i("_"+r),f=e[r],e[r]=function(config,i,u,e,o){var s=f&&f(config,i,u,e,o);return s!==!0&&t["_"+r](config,i,u,e,o),s}),t
       }({
           instrumentationKey:"<insert instrumentation key>"
       });
       
       window.appInsights=appInsights;
       appInsights.trackPageView();
   </script>
   ```

5. Sol tarafta **İzleyici**’yi seçin. Kaynağınızın sistem durumunu ve kullanımını araştırmak için ölçüm gezginini kullanın. Ek özel görünümler oluşturmak için **yeni grafik Ekle** ' yi veya var olan grafik türlerini, yüksekliğini, renk paletini, gruplandırmaları ve ölçümleri değiştirmek için **Düzenle** ' yi seçebilirsiniz. Örneğin, ölçümler açılan penceresinden "sayfa yükleme süresi" ni ve toplamasının "Ort" öğesini seçerek ortalama tarayıcı sayfa yükleme süresini görüntüleyen bir grafik yapabilirsiniz. Azure Ölçüm Gezgini hakkında daha fazla bilgi edinmek için [azure Ölçüm Gezgini](../essentials/metrics-getting-started.md)kullanmaya başlama makalesini ziyaret edin.

   ![Application Insights Server ölçümleri grafiği](./media/nodejs-quick-start/azure-app-insights-server-metrics.png)

İzleme Node.js hakkında daha fazla bilgi edinmek için [ek Appınsights Node.js belgelerine](../app/nodejs.md)göz atın.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Testi tamamladığınızda, kaynak grubunu ve tüm ilgili kaynakları silebilirsiniz. Bunu yapmak için aşağıdaki adımları izleyin.

> [!NOTE]
> Var olan bir kaynak grubunu kullandıysanız aşağıdaki yönergeler çalışmaz ve yalnızca tek bir Application Insights kaynağını silmeniz gerekir. Bir kaynak grubunu her sildiğinizde, bu grubun üyesi olan tüm kaynaklar silinir.

1. Azure portal sol taraftaki menüden **kaynak grupları** ' nı seçin ve ardından **myresourcegroup**' ı seçin.
2. Kaynak grubu sayfanızda **Sil**' i seçin, metin kutusuna **myresourcegroup** yazın ve ardından **Sil**' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Performans sorunlarını bulma ve tanılama](../logs/log-query-overview.md)

