---
title: Azure Işlevleri 'ni kullanarak Event Grid aracılığıyla Service Bus olaylarını işleme
description: Bu makalede, Azure Işlevleri kullanılarak Event Grid aracılığıyla Service Bus olaylarını işlemeye yönelik adımlar sağlanmaktadır.
documentationcenter: .net
author: spelluru
ms.topic: tutorial
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: devx-track-csharp
ms.openlocfilehash: afc0a5bf9b83363d1f4baab955b55148fe3a8498
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95819460"
---
# <a name="tutorial-respond-to-azure-service-bus-events-received-via-azure-event-grid-by-using-azure-functions"></a>Öğretici: Azure Işlevleri kullanılarak Azure Event Grid aracılığıyla alınan Azure Service Bus olaylarına yanıt verme
Bu öğreticide, Azure Işlevleri ve Azure Logic Apps kullanarak Azure Event Grid aracılığıyla alınan Azure Service Bus olaylarına nasıl yanıt verileceğini öğreneceksiniz. 

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:
> [!div class="checklist"]
> * Service Bus ad alanı oluşturma
> * İleti göndermek için örnek bir uygulama hazırlama
> * Service Bus konuya ileti gönderin
> * Logic Apps’i kullanarak ileti alma
> * Azure 'da bir test işlevi ayarlama
> * Event Grid aracılığıyla işlev ve ad alanını bağlama
> * Azure İşlevleri’ni kullanarak ileti alma

[!INCLUDE [service-bus-event-grid-prerequisites](../../includes/service-bus-event-grid-prerequisites.md)]

## <a name="additional-prerequisites"></a>Ek önkoşullar
[Visual Studio 2019](https://www.visualstudio.com/vs) ' i yükleyip **Azure geliştirme** iş yükünü dahil edin. Bu iş yükü, Visual Studio 'da Azure Işlevleri projelerini oluşturmanız, derlemeniz ve dağıtmanız için ihtiyacınız olan **Azure Işlev araçlarını** içerir. 

## <a name="deploy-the-function-app"></a>İşlev uygulamasını dağıtma 

>[!NOTE]
> Azure Işlevleri uygulaması oluşturma ve dağıtma hakkında daha fazla bilgi edinmek için bkz. [Visual Studio kullanarak Azure Işlevleri geliştirme](../azure-functions/functions-develop-vs.md)

1. **Sbeventgridıntegration. sln** çözümünün **FunctionApp1** projesinden **receiveiletionevent. cs** dosyasını açın. 
1. `<SERCICE BUS NAMESPACE - CONNECTION STRING>`Service Bus ad alanınız için bağlantı dizesiyle değiştirin. Aynı çözüm içindeki **Iletileyici** projesinin **program. cs** dosyasında kullandığınız ile aynı olmalıdır. 
1. **FunctionApp1** öğesine sağ tıklayın ve **Yayımla**' yı seçin. 
1. **Yayımla** sayfasında **Başlat**' ı seçin. Bu adımlar, gördüklerinize göre farklılık gösterebilir, ancak yayımlama işlemi benzer olmalıdır. 
1. **Yayımla** sihirbazında, **hedef** sayfasında, **hedef** için **Azure** ' u seçin. 
1. **Belirli hedef** sayfasında **Azure işlev uygulaması (Windows)** öğesini seçin. 
1. **İşlevler örneği** sayfasında **Yeni bir Azure işlevi oluştur**' u seçin. 
1. **İşlev uygulaması (Windows)** sayfasında, aşağıdaki adımları izleyin:
    1. İşlev uygulaması için bir **ad** girin.
    1. Bir Azure **aboneliği** seçin.
    1. Var olan bir **kaynak grubunu** seçin veya yeni bir kaynak grubu oluşturun. Bu öğretici için Service Bus ad alanına sahip kaynak grubunu seçin. 
    1. App Service için bir **plan türü** seçin.
    1. Bir **konum** seçin. Service Bus ad alanıyla aynı konumu seçin. 
    1. Mevcut bir **Azure depolama alanı** seçin veya işlevler uygulaması tarafından kullanılacak yeni bir depolama hesabı oluşturmak için **Yeni** ' yi seçin. 
    1. Işlevler uygulamasını oluşturmak için **Oluştur** ' u seçin. 
1. **Yayımla** sihirbazının **işlevler örneği** sayfasına dönün, **son**' u seçin. 
1. Visual Studio 'daki **Yayımla** sayfasında, Işlevler uygulamasını Azure 'da yayımlamak için **Yayımla** ' yı seçin. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/publish-function-app.png" alt-text="Işlevleri Yayımla uygulaması":::    
1. **Çıkış** penceresinde, derleme ve yayımlama içindeki iletilere bakın ve bunların her ikisinin de başarılı olduğunu doğrulayın. 
1. Şimdi **Yayımla** sayfasında, **Azure Portal Yönet**' i seçin. 
1. Azure portal, **işlev uygulaması** sayfasında, Sol menüdeki **işlevler** ' i seçin ve iki işlev görtığınızdan emin olun: 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/functions.png" alt-text="Event Grid tetikleyicisi ve HTTP tetikleyicisine yönelik iki işlev":::

    > [!NOTE]
    > , Bir `EventGridTriggerFunction` [Event Grid tetikleyicisi](../azure-functions/functions-bindings-event-grid-trigger.md) kullanır ve `HTTPTriggerFunction` bir [http tetikleyicisi](../azure-functions/functions-bindings-http-webhook-trigger.md)kullanır.
1. Listeden **Eventgridtriggerfunction** ' ı seçin. HTTP tetikleyicisini kullanarak birkaç avantaj elde etmek için Event Grid tetikleyiciyi Azure Işlevleri ile kullanmanızı öneririz. Ayrıntılar için bkz. [Azure işlevi Event Grid olayları için olay işleyicisi olarak](../event-grid/handler-functions.md).
1. **Eventgridtriggerfunction** **işlev** sayfasında, sol taraftaki menüden **izleyici** ' yi seçin. 
1. Çağırma günlüğünü yakalamak için Application Insights yapılandırmak üzere **Yapılandır** ' ı seçin. Bu sayfayı, Event Grid Service Bus olaylarını alırken işlev yürütmelerini izlemek için kullanın. 
1. **Application Insights** sayfasında, kaynak için bir ad girin, kaynak için bir **konum** seçin ve ardından **Tamam**' ı seçin. 
1. **İşlev** sayfasına geri dönmek için üstteki (içerik haritası menüsü) **eventgridtriggerfunction** işlevini seçin. 
1. **İzleyici** sayfasında istediğinizi onaylayın. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/monitor-before.png" alt-text="İşlev etkinleştirmeleri öncesinde işlev için izleyici sayfası":::
    
    Web tarayıcınızda bu sayfayı bir sekmede açık tutun. Bu işlev için çağırmaları daha sonra görmek üzere bu sayfayı yenileyeceksiniz.

## <a name="connect-the-function-and-namespace-via-event-grid"></a>Event Grid aracılığıyla işlev ve ad alanını bağlama
Bu bölümde, Azure portal kullanarak işlevini ve Service Bus ad alanını birbirine bağlamaktır. 

Azure Event Grid aboneliği oluşturmak için aşağıdaki adımları izleyin:

1. Azure portal ad alanına gidin ve ardından sol bölmedeki **Olaylar**' ı seçin. Ad alanı pencereniz açılır ve sağ bölmede iki Event Grid aboneliği görüntülenir. 
    
    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/service-bus-events-page.png" alt-text="Service Bus Olaylar sayfası":::
2. Araç çubuğunda **+ olay aboneliği** ' ni seçin. 
3. **Olay aboneliği oluştur** sayfasında, aşağıdaki adımları uygulayın:
    1. Abonelik için bir **ad** girin. 
    2. **Sistem konusu** için bir **ad** girin. Sistem konuları, Azure depolama hesabı ve Azure Service Bus gibi Azure kaynakları için oluşturulan konulardır. Sistem konuları hakkında daha fazla bilgi için bkz. [sistem konularına genel bakış](../event-grid/system-topics.md).
    2. **Uç nokta türü** Için **Azure işlevi** ' ni seçin ve **uç nokta seç**' e tıklayın. 

        ![Service Bus Event Grid aboneliği](./media/service-bus-to-event-grid-integration-example/event-grid-subscription-page.png)
    3. **Azure Işlevi Seç** sayfasında, abonelik, kaynak grubu, işlev uygulaması, yuva ve işlev ' ı seçin ve ardından **Seçimi Onayla**' yı seçin. 

        ![İşlev-uç noktayı seçme](./media/service-bus-to-event-grid-integration-example/function-select-endpoint.png)
    4. **Olay aboneliği oluştur** sayfasında, **Filtreler** sekmesine geçin ve aşağıdaki görevleri yapın:
        1. **Konu filtrelemeyi etkinleştir** ' i seçin
        2. Daha önce oluşturduğunuz Service Bus konusuna (**S1**) abonelik adını girin.
        3. **Oluştur** düğmesini seçin. 

            ![Olay abonelik filtresi](./media/service-bus-to-event-grid-integration-example/event-subscription-filter.png)
4. **Olaylar** sayfasının **olay abonelikleri** sekmesine geçin ve listede olay aboneliğini görtığınızdan emin olun.

    ![Listedeki olay aboneliği](./media/service-bus-to-event-grid-integration-example/event-subscription-in-list.png)

## <a name="monitor-the-functions-app"></a>Işlevler uygulamasını izleme
Daha önce Service Bus konusuna gönderdiğiniz iletiler aboneliğe (S1) iletilir. Event Grid, abonelikteki iletileri Azure işlevine iletir. Öğreticinin bu adımında, işlevin çağrılmasını ve günlüğe kaydedilen bilgilendirici iletileri görüntülemeyi onaylamanız gerekir. 

1. Azure işlevi uygulamanızın sayfasında, zaten etkin değilse **izleyici** sekmesine geçin. Service Bus konusuna gönderilen her ileti için bir giriş görmeniz gerekir. Bunları görmüyorsanız, birkaç dakika bekledikten sonra sayfayı yenileyin. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/function-monitor.png" alt-text="Çağırma sonrasında işlevin izleme sayfası":::
2. Ayrıntıları görmek için listeden çağrıyı seçin. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/invocation-details.png" alt-text="İşlev çağırma ayrıntıları" lightbox="./media/service-bus-to-event-grid-integration-example/invocation-details.png":::

    İletiler gönderilirken günlüğe kaydetme bilgilerini görmek için **izleyici** sayfasının **Günlükler** sekmesini de kullanabilirsiniz. Bir gecikme olabilir, bu nedenle günlüğe kaydedilen iletileri görmek için birkaç dakika bekleyin. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/function-logs.png" alt-text="İşlev günlükleri" lightbox="./media/service-bus-to-event-grid-integration-example/function-logs.png":::

## <a name="troubleshoot"></a>Sorun giderme
Bir süre bekledikten ve yenilemeden sonra herhangi bir işlev çağırma görmüyorsanız, şu adımları izleyin: 

1. İletilerin Service Bus konusuna ulaştığından emin olun. **Service Bus konu** sayfasındaki **gelen iletiler** sayacından bakın. Bu durumda, **Iletileyici** uygulamasını iki kez çalıştırdım, bu nedenle 10 ileti görüyorum (her çalıştırma için 5 ileti).

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/topic-incoming-messages.png" alt-text="Service Bus konu sayfası-gelen iletiler":::    
1. Service Bus abonelikte **etkin ileti** bulunmadığını doğrulayın. 
    Bu sayfada herhangi bir olay görmüyorsanız, **Service Bus abonelik** sayfasının **etkin ileti sayısını** göstermediğini doğrulayın. Bu sayacın sayısı sıfırdan büyükse, abonelikteki iletiler bir nedenle işleyici işlevine (olay aboneliği işleyicisi) iletilmez. Olay aboneliğini doğru şekilde ayarladığınızı doğrulayın. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/subscription-active-message-count.png" alt-text="Service Bus aboneliğindeki etkin ileti sayısı":::    
1. Ayrıca, Service Bus ad alanının **Olaylar** sayfasında **teslim edilen olayları** görürsünüz. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/event-subscription-page.png" alt-text="Olaylar sayfasına teslim edilen olaylar" lightbox="./media/service-bus-to-event-grid-integration-example/invocation-details.png":::
1. Olayların **olay aboneliği** sayfasında teslim edildiğini de görebilirsiniz. **Olaylar** sayfasında olay aboneliğini seçerek bu sayfaya ulaşabilirsiniz. 
    
    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/event-subscription-delivered-events.png" alt-text="Olay aboneliği sayfasına teslim edilen olaylar":::
    


## <a name="next-steps"></a>Sonraki adımlar

* [Azure Event Grid](../event-grid/index.yml) hakkında daha fazla bilgi edinin.
* [Azure İşlevleri](../azure-functions/index.yml) hakkında daha fazla bilgi edinin.
* [Azure App Service’in Logic Apps özelliği](../logic-apps/index.yml) hakkında daha fazla bilgi edinin.
* [Azure Service Bus](/azure/service-bus/) hakkında daha fazla bilgi edinin.


[2]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid2.png
[3]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid3.png
[7]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid7.png
[8]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid8.png
[9]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid9.png
[10]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid10.png
[11]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid11.png
[12]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12.png
[12-1]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12-1.png
[12-2]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12-2.png
[13]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid13.png
[14]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid14.png
[15]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid15.png
[16]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid16.png
[17]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid17.png
[18]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid18.png
[20]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal.png
[21]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal2.png