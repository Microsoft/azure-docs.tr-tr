---
title: 'Öğretici: Event Grid tümleştirme örneklerine Azure Service Bus'
description: 'Öğretici: Bu makalede Service Bus mesajlaşma ve Event Grid tümleştirme örnekleri sunulmaktadır.'
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: f99766cb-8f4b-4baf-b061-4b1e2ae570e4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: tutorial
ms.date: 11/05/2019
ms.author: spelluru
ms.openlocfilehash: d3f523bbc0236536734f21870474492a61532ada
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73719012"
---
# <a name="tutorial-respond-to-azure-service-bus-events-received-via-azure-event-grid-by-using-azure-functions-and-azure-logic-apps"></a>Öğretici: Azure Işlevleri ve Azure Logic Apps kullanarak Azure Event Grid aracılığıyla alınan Azure Service Bus olaylarına yanıt verme
Bu öğreticide, Azure Işlevleri ve Azure Logic Apps kullanarak Azure Event Grid aracılığıyla alınan Azure Service Bus olaylarına nasıl yanıt verileceğini öğreneceksiniz. Aşağıdaki adımları uygulayın:
 
- Hata ayıklama ve Event Grid ilk olay akışını görüntülemek için bir test Azure işlevi oluşturun.
- Event Grid olaylarına göre Azure Service Bus iletileri almak ve işlemek için bir Azure işlevi oluşturun.
- Event Grid olaylarına yanıt vermek için bir mantıksal uygulama oluşturma

Service Bus, Event Grid, Azure Işlevleri ve Logic Apps yapıtları oluşturduktan sonra, aşağıdaki işlemleri yapabilirsiniz: 

1. Service Bus bir konuya ileti gönderin. 
2. Konunun aboneliklerinin bu iletileri aldığını doğrulayın
3. Olaya abone olan işlevin veya mantıksal uygulamanın olayı aldığını doğrulayın. 

## <a name="create-a-service-bus-namespace"></a>Service Bus ad alanı oluşturma
Bu öğreticideki yönergeleri izleyin: [hızlı başlangıç: Azure Portal kullanarak aşağıdaki görevleri yapmak için konuya bir Service Bus konu ve abonelik oluşturun](service-bus-quickstart-topics-subscriptions-portal.md) :

- **Premium** Service Bus ad alanı oluşturun. 
- Bağlantı dizesini alın. 
- Service Bus konu başlığı oluşturun.
- Konuya iki abonelik oluşturun. 

## <a name="prepare-a-sample-application-to-send-messages"></a>İleti göndermek için örnek bir uygulama hazırlama
Service Bus konunuza ileti göndermek için herhangi bir yöntemi kullanabilirsiniz. Bu yordamın sonundaki örnek kod, Visual Studio 2017 kullandığınızı varsayar.

1. [GitHub azure-service-bus deposunu](https://github.com/Azure/azure-service-bus/) kopyalayın.
2. Visual Studio’da *\samples\DotNet\Microsoft.ServiceBus.Messaging\ServiceBusEventGridIntegration* klasörüne gidin ve *SBEventGridIntegration.sln* dosyasını açın.
3. **MessageSender** projesine gidin ve **Program.cs** öğesini seçin.
4. Service Bus konu adınızı ve önceki adımdan aldığınız bağlantı dizesini girin:

    ```CSharp
    const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
    const string TopicName = "YOUR TOPIC NAME";
    ```
5. Service Bus konusuna test iletileri göndermek için programı derleyin ve çalıştırın. 

## <a name="set-up-a-test-function-on-azure"></a>Azure 'da bir test işlevi ayarlama 
Tüm senaryonun üzerinde çalışmadan önce, en az bir küçük test işlevi ayarlayın ve bu, akan olayları gözlemlemek ve bunları izlemek için kullanabilirsiniz. Aşağıdaki görevleri yapmak için [Azure Portal ilk işlevinizi oluşturma](../azure-functions/functions-create-first-azure-function.md) makalesindeki yönergeleri izleyin: 

1. Bir işlev uygulaması oluşturun.
2. HTTP ile tetiklenen bir işlev oluşturun. 

Ardından, aşağıdaki adımları uygulayın: 


# <a name="azure-functions-v2tabv2"></a>[Azure Işlevleri v2](#tab/v2)

1. Ağaç görünümündeki **işlevleri** genişletin ve işlevinizi seçin. İşlevin kodunu şu kodla değiştirin: 

    ```CSharp
    #r "Newtonsoft.Json"
    
    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;
    
    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");
        var content = req.Body;
        string jsonContent = await new StreamReader(content).ReadToEndAsync();
        log.LogInformation($"Received Event with payload: {jsonContent}");
    
        IEnumerable<string> headerValues;
        headerValues = req.Headers.GetCommaSeparatedValues("Aeg-Event-Type");
    
        if (headerValues.Count() != 0)
        {
            var validationHeaderValue = headerValues.FirstOrDefault();
            if(validationHeaderValue == "SubscriptionValidation")
            {
                var events = JsonConvert.DeserializeObject<GridEvent[]>(jsonContent);
                var code = events[0].Data["validationCode"];
                log.LogInformation("Validation code: {code}");
                return (ActionResult) new OkObjectResult(new { validationResponse = code });
            }
        }
    
        return jsonContent == null
            ? new BadRequestObjectResult("Please pass a name on the query string or in the request body")
            : (ActionResult)new OkObjectResult($"Hello, {jsonContent}");
    }
    
    public class GridEvent
    {
        public string Id { get; set; }
        public string EventType { get; set; }
        public string Subject { get; set; }
        public DateTime EventTime { get; set; }
        public Dictionary<string, string> Data { get; set; }
        public string Topic { get; set; }
    }
    
    ```
2. **Kaydet ve çalıştır**’ı seçin.

    ![İşlev uygulaması çıkışı](./media/service-bus-to-event-grid-integration-example/function-run-output.png)
3. **İşlev URL 'Sini al** ' ı seçin ve URL 'yi aklınızda yapın. 

    ![İşlev URL 'sini al](./media/service-bus-to-event-grid-integration-example/get-function-url.png)

# <a name="azure-functions-v1tabv1"></a>[Azure Işlevleri v1](#tab/v1)

1. İşlevi **v1** sürümünü kullanacak şekilde yapılandırın: 
    1. Ağaç görünümünde işlev uygulamanızı seçin ve **işlev uygulama ayarları**' nı seçin. 

        ![İşlev uygulaması ayarları]()./Media/Service-Bus-to-Event-Grid-Integration-example/Function-App-settings.png)
    2. **Çalışma zamanı sürümü**için **~ 1** seçin. 
2. Ağaç görünümündeki **işlevleri** genişletin ve işlevinizi seçin. İşlevin kodunu şu kodla değiştirin: 

    ```CSharp
    #r "Newtonsoft.Json"
    using System.Net;
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;
    
    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {
        log.Info("C# HTTP trigger function processed a request.");
        // parse query parameter
        var content = req.Content;
    
        string jsonContent = await content.ReadAsStringAsync(); 
        log.Info($"Received Event with payload: {jsonContent}");
    
        IEnumerable<string> headerValues;
        if (req.Headers.TryGetValues("Aeg-Event-Type", out headerValues))
        {
            var validationHeaderValue = headerValues.FirstOrDefault();
            if(validationHeaderValue == "SubscriptionValidation")
            {
            var events = JsonConvert.DeserializeObject<GridEvent[]>(jsonContent);
                 var code = events[0].Data["validationCode"];
                 return req.CreateResponse(HttpStatusCode.OK,
                 new { validationResponse = code });
            }
        }
    
        return jsonContent == null
        ? req.CreateResponse(HttpStatusCode.BadRequest, "Pass a name on the query string or in the request body")
        : req.CreateResponse(HttpStatusCode.OK, "Hello " + jsonContent);
    }
    
    public class GridEvent
    {
        public string Id { get; set; }
        public string EventType { get; set; }
        public string Subject { get; set; }
        public DateTime EventTime { get; set; }
        public Dictionary<string, string> Data { get; set; }
        public string Topic { get; set; }
    }
    ```
4. **Kaydet ve çalıştır**’ı seçin.

    ![İşlev uygulaması çıkışı](./media/service-bus-to-event-grid-integration-example/function-run-output.png)
4. **İşlev URL 'Sini al** ' ı seçin ve URL 'yi aklınızda yapın. 

    ![İşlev URL 'sini al](./media/service-bus-to-event-grid-integration-example/get-function-url.png)

---

## <a name="connect-the-function-and-namespace-via-event-grid"></a>Event Grid aracılığıyla işlev ve ad alanını bağlama
Bu bölümde, Azure portal kullanarak işlevini ve Service Bus ad alanını birbirine bağlamaktır. 

Azure Event Grid aboneliği oluşturmak için aşağıdaki adımları izleyin:

1. Azure portal ad alanına gidin ve ardından sol bölmedeki **Olaylar**' ı seçin. Ad alanı pencereniz açılır ve sağ bölmede iki Event Grid aboneliği görüntülenir. 
    
    ![Service Bus Olaylar sayfası](./media/service-bus-to-event-grid-integration-example/service-bus-events-page.png)
2. Araç çubuğunda **+ olay aboneliği** ' ni seçin. 
3. **Olay aboneliği oluştur** sayfasında, aşağıdaki adımları uygulayın:
    1. Abonelik için bir **ad** girin. 
    2. **Uç nokta türü**Için **Web kancası** seçin. 

        ![Service Bus Event Grid aboneliği](./media/service-bus-to-event-grid-integration-example/event-grid-subscription-page.png)
    3. **Uç nokta seç**' i seçin, Işlev URL 'sini yapıştırın ve **Seçimi Onayla**' yı seçin. 

        ![İşlev-uç noktayı seçme](./media/service-bus-to-event-grid-integration-example/function-select-endpoint.png)
    4. **Filtreler** sekmesine geçin, daha önce oluşturduğunuz Service Bus konuya **ilk aboneliğin** adını girin ve ardından **Oluştur** düğmesini seçin. 

        ![Olay abonelik filtresi](./media/service-bus-to-event-grid-integration-example/event-subscription-filter.png)
4. Olay aboneliğini listede görtığınızdan emin olun.

    ![Listedeki olay aboneliği](./media/service-bus-to-event-grid-integration-example/event-subscription-in-list.png)

## <a name="send-messages-to-the-service-bus-topic"></a>Service Bus konuya ileti gönderin
1. Service Bus konusuna iletiler C# Gönderen .NET uygulamasını çalıştırın. 

    ![Konsol uygulaması çıkışı](./media/service-bus-to-event-grid-integration-example/console-app-output.png)
1. Azure işlevi uygulamanızın sayfasında **işlevler**' i genişletin, **işlevinizi**genişletin ve **İzle**' yi seçin. 

    ![Monitor işlevi](./media/service-bus-to-event-grid-integration-example/function-monitor.png)

## <a name="receive-messages-by-using-azure-functions"></a>Azure İşlevleri’ni kullanarak ileti alma
Önceki bölümde, basit bir test ve hata ayıklama senaryosuna baktınız ve olayların akışa alındığından emin oldunuz. 

Bu bölümde, bir olay aldıktan sonra nasıl ileti alınacağını ve işleneceğini öğreneceksiniz.

### <a name="publish-a-function-from-visual-studio"></a>Visual Studio 'dan bir işlev yayımlama
1. Açtığınız Visual Studio çözümünde (**sbeventgridıntegration**), **sbeventgridıntegration** projesinde **ReceiveMessagesOnEvent.cs** öğesini seçin. 
2. Aşağıdaki kodda Service Bus Bağlantı dizenizi girin:

    ```Csharp
    const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
    ```
3. İşlevin **Yayımlama profilini** indirin:
    1. İşlev uygulamanızı seçin. 
    2. Henüz seçili değilse **genel bakış** sekmesini seçin. 
    3. Araç çubuğunda **Yayımlama profilini al** ' ı seçin. 

        ![İşlev için Yayımlama profili al](./media/service-bus-to-event-grid-integration-example/function-download-publish-profile.png)
    4. Dosyayı projenizin klasörüne kaydedin. 
4. Visual Studio’da **SBEventGridIntegration** öğesine sağ tıklayın ve **Yayımla**’yı seçin. 
5. **Yayımla** sayfasında **Başlat** ' ı seçin. 
6. **Bir yayımlama hedefi seçin** sayfasında, aşağıdaki adımları uygulayın ve **profili içeri aktar**' ı seçin. 

    ![Visual Studio-profil Içeri aktarma düğmesi](./media/service-bus-to-event-grid-integration-example/visual-studio-import-profile-button.png)
7. Daha önce indirdiğiniz **Yayımlama profili dosyasını** seçin. 
8. **Yayımla** sayfasında **Yayımla** ' yı seçin. 

    ![Visual Studio-Yayımla](./media/service-bus-to-event-grid-integration-example/select-publish.png)
9. Yeni Azure işlevi **Receiveiletionevent**' i görtığınızdan emin olun. Gerekirse sayfayı yenileyin. 

    ![Yeni işlevin oluşturulduğunu onaylayın](./media/service-bus-to-event-grid-integration-example/function-receive-messages.png)
10. Yeni işleve URL 'YI alın ve bu işlemi yapın. 

### <a name="event-grid-subscription"></a>Event Grid aboneliği

1. Mevcut Event Grid aboneliğini Sil:
    1. **Service Bus ad alanı** sayfasında, sol taraftaki menüden **Olaylar** ' ı seçin. 
    2. Mevcut olay aboneliğini seçin. 
    3. **Olay aboneliği** sayfasında **Sil**' i seçin.
2. Yeni işlev URL 'sini kullanarak bir Event Grid aboneliği oluşturmak için [işlev ve ad alanını Event Grid aracılığıyla bağlama](#connect-the-function-and-namespace-via-event-grid) bölümündeki yönergeleri izleyin.
3. Konuya ileti göndermek ve işlevi izlemek için, [Service Bus Ileti gönderme](#send-messages-to-the-service-bus-topic) bölümündeki yönergeleri izleyin. 

## <a name="receive-messages-by-using-logic-apps"></a>Logic Apps’i kullanarak ileti alma
Aşağıdaki adımları izleyerek bir mantıksal uygulamayı Azure Service Bus ve Azure Event Grid bağlayın:

1. Azure portal bir mantıksal uygulama oluşturun.
    1. **+ Kaynak oluştur**' u seçin, **tümleştirme**' i seçin ve ardından **mantıksal uygulama**' yı seçin. 
    2. **Mantıksal uygulama-oluştur** sayfasında, mantıksal uygulama için bir **ad** girin.
    3. Azure **aboneliğinizi** seçin. 
    4. **Kaynak grubu**için **var olanı kullan** ' ı seçin ve daha önce oluşturduğunuz diğer kaynaklar (Azure işlevi, Service Bus ad alanı gibi) için kullandığınız kaynak grubunu seçin. 
    5. Mantıksal uygulamanın **konumunu** seçin. 
    6. Mantıksal uygulamayı oluşturmak için **Oluştur** ' u seçin. 
2. **Logic Apps tasarımcı** sayfasında, **Şablonlar**altında **boş mantıksal uygulama** ' yı seçin. 
3. Tasarımcıda aşağıdaki adımları uygulayın:
    1. **Event Grid**arayın. 
    2. **Kaynak olayı gerçekleştiğinde (Önizleme)-Azure Event Grid**seçin. 

        ![Logic Apps Tasarımcısı-Event Grid tetikleyiciyi Seç](./media/service-bus-to-event-grid-integration-example/logic-apps-event-grid-trigger.png)
4. **Oturum aç**' ı seçin, Azure kimlik bilgilerinizi girin ve **erişime izin ver**' i seçin. 
5. **Kaynak olayı gerçekleştiğinde** , aşağıdaki adımları uygulayın:
    1. Azure aboneliğinizi seçin. 
    2. **Kaynak türü**için **Microsoft. ServiceBus. Namespaces**' i seçin. 
    3. **Kaynak adı**için Service Bus ad alanınızı seçin. 
    4. **Yeni parametre Ekle**' yi seçin ve **sonek filtresi**' ni seçin. 
    5. **Sonek filtresi**için ikinci Service Bus konu aboneliğinizin adını girin. 
        ![Logic Apps Tasarımcısı-olay](./media/service-bus-to-event-grid-integration-example/logic-app-configure-event.png) yapılandırma
6. Tasarımcıda **+ yeni adım** ' ı seçin ve aşağıdaki adımları uygulayın:
    1. **Service Bus**arayın.
    2. Listeden **Service Bus** seçin. 
    3. **Eylemler** listesinden **iletileri al** ' ı seçin. 
    4. **Bir konu aboneliğinden (Peek-kilit) Ileti al**' ı seçin. 

        ![Logic Apps Designer-iletileri al eylemi](./media/service-bus-to-event-grid-integration-example/service-bus-get-messages-step.png)
    5. **Bağlantı için bir ad**girin. Örneğin: **Konu aboneliğinden Iletiler alın**ve Service Bus ad alanını seçin. 

        ![Logic Apps Tasarımcısı-Service Bus ad alanını seçin](./media/service-bus-to-event-grid-integration-example/logic-apps-select-namespace.png) 
    6. **RootManageSharedAccessKey**öğesini seçin.

        ![Logic Apps Tasarımcısı-paylaşılan erişim anahtarını seçin](./media/service-bus-to-event-grid-integration-example/logic-app-shared-access-key.png) 
    7. **Oluştur**'u seçin. 
    8. Konuyu ve aboneliğinizi seçin. 
    
        ![Logic Apps tasarımcı-Service Bus konu ve aboneliğinizi seçin](./media/service-bus-to-event-grid-integration-example/logic-app-select-topic-subscription.png)
7. **+ Yeni adım**' ı seçin ve aşağıdaki adımları uygulayın: 
    1. **Service Bus**'ı seçin.
    2. Eylem listesinden **bir konu aboneliğindeki Iletiyi doldurun '** ı seçin. 
    3. Service Bus **konu başlığını**seçin.
    4. Konunun ikinci **aboneliğini** seçin.
    5. **Iletinin kilit belirteci**için **dinamik içerikten** **belirteci kilitle** ' yi seçin. 

        ![Logic Apps tasarımcı-Service Bus konu ve aboneliğinizi seçin](./media/service-bus-to-event-grid-integration-example/logic-app-complete-message.png)
8. Mantıksal uygulamayı kaydetmek için Logic Apps tasarımcısında araç çubuğundan **Kaydet** ' i seçin. 
9. Konuya ileti göndermek için, [Service Bus Ileti gönderme](#send-messages-to-the-service-bus-topic) bölümündeki yönergeleri izleyin. 
10. Mantıksal uygulamanızın **genel bakış** sayfasına geçin. Mantıksal uygulamanın, gönderilen iletiler için **çalıştırmalar geçmişinde** çalıştığını görürsünüz.

    ![Logic Apps Designer-Logic App çalıştırmaları](./media/service-bus-to-event-grid-integration-example/logic-app-runs.png)

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/) hakkında daha fazla bilgi edinin.
* [Azure İşlevleri](https://docs.microsoft.com/azure/azure-functions/) hakkında daha fazla bilgi edinin.
* [Azure App Service’in Logic Apps özelliği](https://docs.microsoft.com/azure/logic-apps/) hakkında daha fazla bilgi edinin.
* [Azure Service Bus](https://docs.microsoft.com/azure/service-bus/) hakkında daha fazla bilgi edinin.


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
