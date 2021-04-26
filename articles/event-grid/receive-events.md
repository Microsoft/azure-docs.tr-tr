---
title: Azure Event Grid bir HTTP uç noktasına olay alma
description: Bir HTTP uç noktasının nasıl doğrulanacağını, sonra Azure Event Grid olayların nasıl alınacağını ve seri durumdan kaldırılacağını açıklar
ms.topic: conceptual
ms.date: 11/19/2020
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 75c80fb85d39298f1130537971bc700897c039d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96023744"
---
# <a name="receive-events-to-an-http-endpoint"></a>HTTP uç noktasına olayları alma

Bu makalede bir olay aboneliğinden olayları almak ve sonra olayları almak ve seri durumdan çıkarmak için [BIR HTTP uç noktasının nasıl doğrulanacağı](webhook-event-delivery.md) açıklanır. Bu makalede tanıtım amaçlı olarak bir Azure Işlevi kullanılmaktadır, ancak aynı kavramlar uygulamanın nerede barındırıldığından bağımsız olarak geçerlidir.

> [!NOTE]
> Event Grid ile bir Azure Işlevi tetiklendiğinde [Event Grid tetikleyicisi](../azure-functions/functions-bindings-event-grid.md) kullanmanız **önemle** önerilir. Burada genel Web kancası tetikleyicisi kullanımı gösterim amaçlıdır ' dir.

## <a name="prerequisites"></a>Önkoşullar

HTTP ile tetiklenen bir işlev içeren bir işlev uygulamasına ihtiyacınız vardır.

## <a name="add-dependencies"></a>Bağımlılık Ekle

.NET sürümünde geliştirme yapıyorsanız NuGet paketi için işlevinizin [bir bağımlılığını ekleyin](../azure-functions/functions-reference-csharp.md#referencing-custom-assemblies) `Microsoft.Azure.EventGrid` [](https://www.nuget.org/packages/Microsoft.Azure.EventGrid). Bu makaledeki örneklerde sürüm 1.4.0 veya üzeri bir sürüm gerekir.

Diğer dillere yönelik SDK 'lar, [SDK 'Ları Yayımla](./sdk-overview.md#data-plane-sdks) başvurusu aracılığıyla kullanılabilir. Bu paketler,, ve gibi yerel olay türleri için modeller `EventGridEvent` vardır `StorageBlobCreatedEventData` `EventHubCaptureFileCreatedEventData` .

Azure işlevinizdeki "dosyaları görüntüle" bağlantısına tıklayın (Azure işlevleri portalındaki en sağdaki bölme) ve project.jsadlı bir dosya oluşturun. Aşağıdaki içeriği `project.json` dosyaya ekleyin ve kaydedin:

 ```json
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "Microsoft.Azure.EventGrid": "2.0.0"
      }
    }
   }
}
```

![NuGet paketi eklendi](./media/receive-events/add-dependencies.png)

## <a name="endpoint-validation"></a>Uç nokta doğrulaması

Yapmak istediğiniz ilk şey `Microsoft.EventGrid.SubscriptionValidationEvent` olayları işler. Her biri bir olaya abone olduğunda, Event Grid veri yükünde bir olan bir doğrulama olayını uç noktaya gönderir `validationCode` . Uç noktanın [geçerli olduğunu ve size ait olduğunu kanıtlamak](webhook-event-delivery.md)için bu uç noktanın yanıt gövdesinde geri yankılanması gerekir. Web kancası tarafından tetiklenen bir Işlev yerine [Event Grid tetikleyicisi](../azure-functions/functions-bindings-event-grid.md) kullanıyorsanız, uç nokta doğrulaması sizin için işlenir. Üçüncü taraf bir API hizmeti kullanıyorsanız ( [Zapier](https://zapier.com/home) veya [ifttt](https://ifttt.com/)gibi), doğrulama kodunu programlama yoluyla yankılanmayabilir. Bu hizmetler için abonelik doğrulama olayında gönderilen doğrulama URL 'sini kullanarak aboneliği el ile doğrulayabilirsiniz. Bu URL 'YI özellikte kopyalayın `validationUrl` ve bır Rest istemcisi veya Web tarayıcınız aracılığıyla BIR get isteği gönderin.

C# ' de, `DeserializeEventGridEvents()` işlev Event Grid olaylarını serileştirir. Olay verilerini StorageBlobCreatedEventData gibi uygun tür olarak serileştirir. `Microsoft.Azure.EventGrid.EventTypes`Desteklenen olay türlerini ve adlarını almak için sınıfını kullanın.

Doğrulama kodunu programlı bir şekilde yankılama için aşağıdaki kodu kullanın. [Event Grid tüketici örneğinde](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/tree/master/EventGridConsumer)ilgili örnekleri bulabilirsiniz.

```csharp
using System.IO;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.EventGrid;
namespace Function1
{
    public static class Function1
    {
        [FunctionName("Function1")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            string response = string.Empty;
            string requestContent = await new StreamReader(req.Body).ReadToEndAsync();
            log.LogInformation($"Received events: {requestContent}");

            EventGridSubscriber eventGridSubscriber = new EventGridSubscriber();

            EventGridEvent[] eventGridEvents = eventGridSubscriber.DeserializeEventGridEvents(requestContent);

            foreach (EventGridEvent eventGridEvent in eventGridEvents)
            {
                if (eventGridEvent.Data is SubscriptionValidationEventData)
                {
                    var eventData = (SubscriptionValidationEventData)eventGridEvent.Data;
                    log.LogInformation($"Got SubscriptionValidation event data, validation code: {eventData.ValidationCode}, topic: {eventGridEvent.Topic}");
                    // Do any additional validation (as required) and then return back the below response

                    var responseData = new SubscriptionValidationResponse()
                    {
                        ValidationResponse = eventData.ValidationCode
                    };
                    return new OkObjectResult(responseData);
                }
            }
            return new OkObjectResult(response);
        }
   }
}
```

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function begun');
    var validationEventType = "Microsoft.EventGrid.SubscriptionValidationEvent";

    for (var events in req.body) {
        var body = req.body[events];
        // Deserialize the event data into the appropriate type based on event type
        if (body.data && body.eventType == validationEventType) {
            context.log("Got SubscriptionValidation event data, validation code: " + body.data.validationCode + " topic: " + body.topic);

            // Do any additional validation (as required) and then return back the below response
            var code = body.data.validationCode;
            context.res = { status: 200, body: { "ValidationResponse": code } };
        }
    }
    context.done();
};
```

### <a name="test-validation-response"></a>Test doğrulama yanıtı

İşlevin test alanına örnek olayı yapıştırarak doğrulama yanıtı işlevini test edin:

```json
[{
  "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "subject": "",
  "data": {
    "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
  },
  "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
  "eventTime": "2018-01-25T22:12:19.4556811Z",
  "metadataVersion": "1",
  "dataVersion": "1"
}]
```

Çalıştır ' a tıkladığınızda, çıktının 200 OK ve gövdesinde olması gerekir `{"validationResponse":"512d38b6-c7b8-40c8-89fe-f46f9e9622b6"}` :

:::image type="content" source="./media/receive-events/validation-request.png" alt-text="Doğrulama isteği":::

:::image type="content" source="./media/receive-events/validation-output.png" alt-text="Doğrulama çıktısı":::

## <a name="handle-blob-storage-events"></a>Blob Depolama olaylarını işle

Şimdi, işlevi işleyecek şekilde genişletelim `Microsoft.Storage.BlobCreated` :

```cs
using System.IO;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using Microsoft.Azure.EventGrid;
using Microsoft.Azure.EventGrid.Models;

namespace FunctionApp1
{
    public static class Function1
    {
        [FunctionName("Function1")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req,
            ILogger log)
        {
            log.LogInformation($"C# HTTP trigger function begun");
            string response = string.Empty;

            string requestContent = await new StreamReader(req.Body).ReadToEndAsync();
            log.LogInformation($"Received events: {requestContent}");

            EventGridSubscriber eventGridSubscriber = new EventGridSubscriber();

            EventGridEvent[] eventGridEvents = eventGridSubscriber.DeserializeEventGridEvents(requestContent);

            foreach (EventGridEvent eventGridEvent in eventGridEvents)
            {
                if (eventGridEvent.Data is SubscriptionValidationEventData)
                {
                    var eventData = (SubscriptionValidationEventData)eventGridEvent.Data;
                    log.LogInformation($"Got SubscriptionValidation event data, validation code: {eventData.ValidationCode}, topic: {eventGridEvent.Topic}");
                    // Do any additional validation (as required) and then return back the below response

                    var responseData = new SubscriptionValidationResponse()
                    {
                        ValidationResponse = eventData.ValidationCode
                    };

                    return new OkObjectResult(responseData);
                }
            }

            return new OkObjectResult(response);
        }
    }
}
```

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function begun');
    var validationEventType = "Microsoft.EventGrid.SubscriptionValidationEvent";
    var storageBlobCreatedEvent = "Microsoft.Storage.BlobCreated";

    for (var events in req.body) {
        var body = req.body[events];
        // Deserialize the event data into the appropriate type based on event type  
        if (body.data && body.eventType == validationEventType) {
            context.log("Got SubscriptionValidation event data, validation code: " + body.data.validationCode + " topic: " + body.topic);

            // Do any additional validation (as required) and then return back the below response
            var code = body.data.validationCode;
            context.res = { status: 200, body: { "ValidationResponse": code } };
        }

        else if (body.data && body.eventType == storageBlobCreatedEvent) {
            var blobCreatedEventData = body.data;
            context.log("Relaying received blob created event payload:" + JSON.stringify(blobCreatedEventData));
        }
    }
    context.done();
};

```

### <a name="test-blob-created-event-handling"></a>Test blobu oluşturulan olay işleme

Test alanına bir [BLOB depolama olayı](./event-schema-blob-storage.md#example-event) yerleştirerek ve çalıştıran işlevin yeni işlevlerini test edin:

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/xstoretestaccount",
  "subject": "/blobServices/default/containers/testcontainer/blobs/testfile.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "0x8D4BCC2E4835CD0",
    "contentType": "text/plain",
    "contentLength": 524288,
    "blobType": "BlockBlob",
    "url": "https://example.blob.core.windows.net/testcontainer/testfile.txt",
    "sequencer": "00000000000004420000000000028963",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

Blob URL çıkışını işlev günlüğünde görmeniz gerekir:

![Çıktı günlüğü](./media/receive-events/blob-event-response.png)

Ayrıca, bir BLOB depolama hesabı veya Genel Amaçlı v2 (GPv2) depolama hesabı oluşturarak, [olay aboneliği ekleyerek ve](../storage/blobs/storage-blob-event-quickstart.md)bitiş noktasını işlev URL 'sine ayarlayarak da test edebilirsiniz:

![İşlev URL'si](./media/receive-events/function-url.png)

## <a name="handle-custom-events"></a>Özel olayları işle

Son olarak, aynı zamanda özel olayları işleyebilmesi için işlevi bir kez daha genişletmenize izin verir. 

C# ' de SDK, olay türü adını olay veri türüne eşlemeyi destekler. `AddOrUpdateCustomEventMapping()`Özel olayı eşlemek için işlevini kullanın.

Olaylarınız için bir denetim ekleyin `Contoso.Items.ItemReceived` . Son kodunuzun şöyle görünmesi gerekir:

```cs
using System.IO;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.EventGrid;
using Newtonsoft.Json;

namespace FunctionApp1
{
    class ContosoItemReceivedEventData
    {
        [JsonProperty("itemSku")]
        public string ItemSku { get; set; }
    }
    public static class Function1
    {
        [FunctionName("Function1")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req,
            ILogger log)
        {
            log.LogInformation($"C# HTTP trigger function begun");
            string response = string.Empty;

            string requestContent = await new StreamReader(req.Body).ReadToEndAsync();
            log.LogInformation($"Received events: {requestContent}");

            EventGridSubscriber eventGridSubscriber = new EventGridSubscriber();
            eventGridSubscriber.AddOrUpdateCustomEventMapping("Contoso.Items.ItemReceived", typeof(ContosoItemReceivedEventData));
            EventGridEvent[] eventGridEvents = eventGridSubscriber.DeserializeEventGridEvents(requestContent);

            foreach (EventGridEvent eventGridEvent in eventGridEvents)
            {
                if (eventGridEvent.Data is SubscriptionValidationEventData)
                {
                    var eventData = (SubscriptionValidationEventData)eventGridEvent.Data;
                    log.LogInformation($"Got SubscriptionValidation event data, validation code: {eventData.ValidationCode}, topic: {eventGridEvent.Topic}");
                    // Do any additional validation (as required) and then return back the below response

                    var responseData = new SubscriptionValidationResponse()
                    {
                        ValidationResponse = eventData.ValidationCode
                    };

                    return new OkObjectResult(responseData);
                }
                else if (eventGridEvent.Data is StorageBlobCreatedEventData)
                {
                    var eventData = (StorageBlobCreatedEventData)eventGridEvent.Data;
                    log.LogInformation($"Got BlobCreated event data, blob URI {eventData.Url}");
                }
                else if (eventGridEvent.Data is ContosoItemReceivedEventData)
                {
                    var eventData = (ContosoItemReceivedEventData)eventGridEvent.Data;
                    log.LogInformation($"Got ContosoItemReceived event data, item SKU {eventData.ItemSku}");
                }
            }

            return new OkObjectResult(response);
        }
    }
}
```

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function begun');
    var validationEventType = "Microsoft.EventGrid.SubscriptionValidationEvent";
    var storageBlobCreatedEvent = "Microsoft.Storage.BlobCreated";
    var customEventType = "Contoso.Items.ItemReceived";

    for (var events in req.body) {
        var body = req.body[events];
        // Deserialize the event data into the appropriate type based on event type
        if (body.data && body.eventType == validationEventType) {
            context.log("Got SubscriptionValidation event data, validation code: " + body.data.validationCode + " topic: " + body.topic);

            // Do any additional validation (as required) and then return back the below response
            var code = body.data.validationCode;
            context.res = { status: 200, body: { "ValidationResponse": code } };
        }

        else if (body.data && body.eventType == storageBlobCreatedEvent) {
            var blobCreatedEventData = body.data;
            context.log("Relaying received blob created event payload:" + JSON.stringify(blobCreatedEventData));
        }

        else if (body.data && body.eventType == customEventType) {
            var payload = body.data;
            context.log("Relaying received custom payload:" + JSON.stringify(payload));
        }
    }
    context.done();
};
```

### <a name="test-custom-event-handling"></a>Test özel olay işleme

Son olarak, işlevinizin artık özel olay türünü işleyebileceğini test edin:

```json
[{
    "subject": "Contoso/foo/bar/items",
    "eventType": "Contoso.Items.ItemReceived",
    "eventTime": "2017-08-16T01:57:26.005121Z",
    "id": "602a88ef-0001-00e6-1233-1646070610ea",
    "data": { 
            "itemSku": "Standard"
            },
    "dataVersion": "",
    "metadataVersion": "1"
}]
```

Ayrıca [, portaldan kıvrımlı ile özel bir olay göndererek](./custom-event-quickstart-portal.md) veya [Postman](https://www.getpostman.com/)gıbı bir uç noktaya nakledebileceğiniz herhangi bir hizmeti ya da uygulamayı kullanarak [özel bir konuya](./post-to-custom-topic.md) gönderim yaparak bu işlevselliği canlı olarak test edebilirsiniz. Işlev URL 'SI olarak ayarlanan uç nokta ile bir özel konu ve olay aboneliği oluşturun.

[!INCLUDE [event-grid-message-headers](../../includes/event-grid-message-headers.md)]

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Event Grid yönetimini keşfet ve SDK 'Ları Yayımla](./sdk-overview.md)
* [Özel bir konuya gönderi](./post-to-custom-topic.md) yapmayı öğrenin
* Derinlemesine Event Grid ve Işlev öğreticilerinin [BLOB depolamaya yüklenen görüntüleri yeniden boyutlandırma](resize-images-on-storage-blob-upload-event.md) gibi öğreticilerden birini deneyin
