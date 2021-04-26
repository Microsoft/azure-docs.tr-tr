---
title: Azure Logic Apps kullanarak Event Grid aracılığıyla Service Bus olaylarını işleme
description: Bu makalede Azure Logic Apps kullanarak Event Grid aracılığıyla Service Bus olaylarını işlemeye yönelik adımlar sağlanmaktadır.
documentationcenter: .net
author: spelluru
ms.topic: tutorial
ms.date: 10/16/2020
ms.author: spelluru
ms.custom: devx-track-csharp
ms.openlocfilehash: 93375f6047fbe4eda2132e024dab0e067e83ccf1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95999066"
---
# <a name="tutorial-respond-to-azure-service-bus-events-received-via-azure-event-grid-by-using-azure-logic-apps"></a>Öğretici: Azure Logic Apps kullanarak Azure Event Grid aracılığıyla alınan Azure Service Bus olaylarına yanıt verme
Bu öğreticide, Azure Logic Apps kullanarak Azure Event Grid aracılığıyla alınan Azure Service Bus olaylarına nasıl yanıt verileceğini öğreneceksiniz. 

[!INCLUDE [service-bus-event-grid-prerequisites](../../includes/service-bus-event-grid-prerequisites.md)]

## <a name="receive-messages-by-using-logic-apps"></a>Logic Apps’i kullanarak ileti alma
Bu adımda, Azure Event Grid aracılığıyla Service Bus olaylarını alan bir Azure mantıksal uygulaması oluşturacaksınız. 

1. Azure portal bir mantıksal uygulama oluşturun.
    1. **+ Kaynak oluştur**' u seçin, **tümleştirme**' i seçin ve ardından **mantıksal uygulama**' yı seçin. 
    2. **Mantıksal uygulama-oluştur** sayfasında, mantıksal uygulama için bir **ad** girin.
    3. Azure **aboneliğinizi** seçin. 
    4. **Kaynak grubu** için **var olanı kullan** ' ı seçin ve daha önce oluşturduğunuz diğer kaynaklar (Azure işlevi, Service Bus ad alanı gibi) için kullandığınız kaynak grubunu seçin. 
    5. Mantıksal uygulamanın **konumunu** seçin. 
    6. **Gözden geçir + Oluştur**’u seçin. 
    1. Mantıksal uygulamayı oluşturmak için **gözden geçir + oluştur** sayfasında **Oluştur** ' u seçin. 
1. **Logic Apps tasarımcı** sayfasında, **Şablonlar** altında **boş mantıksal uygulama** ' yı seçin. 
1. Tasarımcıda aşağıdaki adımları uygulayın:
    1. **Event Grid** arayın. 
    2. **Kaynak olayının ne zaman gerçekleşeceğini seçin-Azure Event Grid**. 

        ![Logic Apps Tasarımcısı-Event Grid tetikleyiciyi Seç](./media/service-bus-to-event-grid-integration-example/logic-apps-event-grid-trigger.png)
4. **Oturum aç**' ı seçin, Azure kimlik bilgilerinizi girin ve **erişime izin ver**' i seçin. 
5. **Kaynak olayı gerçekleştiğinde** , aşağıdaki adımları uygulayın:
    1. Azure aboneliğinizi seçin. 
    2. **Kaynak türü** için **Microsoft. ServiceBus. Namespaces**' i seçin. 
    3. **Kaynak adı** için Service Bus ad alanınızı seçin. 
    4. **Yeni parametre Ekle**' yi seçin ve **sonek filtresi**' ni seçin. 
    5. **Sonek filtresi** için ikinci Service Bus konu aboneliğinizin adını girin. 
        ![Logic Apps Tasarımcısı-olayı yapılandırma](./media/service-bus-to-event-grid-integration-example/logic-app-configure-event.png)
6. Tasarımcıda **+ yeni adım** ' ı seçin ve aşağıdaki adımları uygulayın:
    1. **Service Bus** arayın.
    2. Listeden **Service Bus** seçin. 
    3. **Eylemler** listesinden **iletileri al** ' ı seçin. 
    4. **Bir konu aboneliğinden (Peek-kilit) Ileti al**' ı seçin. 

        ![Logic Apps Designer-iletileri al eylemi](./media/service-bus-to-event-grid-integration-example/service-bus-get-messages-step.png)
    5. **Bağlantı için bir ad** girin. Örneğin: **Konu aboneliğinden Iletiler alın** ve Service Bus ad alanını seçin. 

        ![Logic Apps Tasarımcısı-Service Bus ad alanını seçin](./media/service-bus-to-event-grid-integration-example/logic-apps-select-namespace.png) 
    6. **RootManageSharedAccessKey** öğesini seçin ve ardından **Oluştur**' u seçin.

        ![Logic Apps Tasarımcısı-paylaşılan erişim anahtarını seçin](./media/service-bus-to-event-grid-integration-example/logic-app-shared-access-key.png) 
    8. **Konuyu** ve **aboneliğinizi** seçin. 
    
        ![Konuyu ve aboneliğinizi seçtiğiniz yeri gösteren ekran görüntüsü.](./media/service-bus-to-event-grid-integration-example/logic-app-select-topic-subscription.png)
7. **+ Yeni adım**' ı seçin ve aşağıdaki adımları uygulayın: 
    1. **Service Bus**'ı seçin.
    2. Eylem listesinden **bir konu aboneliğindeki Iletiyi doldurun '** ı seçin. 
    3. Service Bus **konu başlığını** seçin.
    4. Konunun ikinci **aboneliğini** seçin.
    5. **Iletinin kilit belirteci** için **dinamik içerikten** **belirteci kilitle** ' yi seçin. 

        ![Logic Apps Tasarımcısı-iletiyi doldurun](./media/service-bus-to-event-grid-integration-example/logic-app-complete-message.png)
8. Mantıksal uygulamayı kaydetmek için Logic Apps tasarımcısında araç çubuğundan **Kaydet** ' i seçin. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/save-logic-app.png" alt-text="Mantıksal uygulamayı Kaydet":::
1. Konuya zaten test iletileri göndermediyse, konuya ileti göndermek için [Service Bus e-posta gönderme](#send-messages-to-the-service-bus-topic) bölümündeki yönergeleri izleyin. 
1. Mantıksal uygulamanızın **genel bakış** sayfasına geçin. Mantıksal uygulamanın, gönderilen iletiler için **çalıştırmalar geçmişinde** çalıştığını görürsünüz. Mantıksal uygulama çalıştırmalarını görebilmeniz için birkaç dakika sürebilir. Sayfayı yenilemek için araç çubuğunda **Yenile** ' yi seçin. 

    ![Logic Apps Designer-Logic App çalıştırmaları](./media/service-bus-to-event-grid-integration-example/logic-app-runs.png)
1. Ayrıntıları görmek için bir mantıksal uygulama çalıştırması seçin. For döngüsünde 5 ileti işlediğine dikkat edin. 
    
    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/logic-app-run-details.png" alt-text="Mantıksal uygulama çalıştırma ayrıntıları":::    

## <a name="troubleshoot"></a>Sorun giderme
Bir süre bekledikten ve yenilemeden sonra herhangi bir çağırma görmüyorsanız, şu adımları izleyin: 

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