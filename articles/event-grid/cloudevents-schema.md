---
title: CloudEvents şemasında olaylarla Azure Event Grid kullanma
description: Azure Event Grid olaylar için CloudEvents şemasının nasıl kullanılacağını açıklar. Hizmet, CloudEvents JSON uygulamasındaki olayları destekler.
ms.topic: conceptual
ms.date: 11/10/2020
ms.custom: devx-track-js, devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 0ee816663a385601d4a31edbf87f8c787ea5aa91
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389511"
---
# <a name="use-cloudevents-v10-schema-with-event-grid"></a>Event Grid ile CloudEvents v 1.0 şeması kullanma
Azure Event Grid, [varsayılan olay şemasına](event-schema.md)ek olarak, [cloudevents v 1.0](https://github.com/cloudevents/spec/blob/v1.0/json-format.md) ve [http protokol bağlamasının](https://github.com/cloudevents/spec/blob/v1.0/http-protocol-binding.md)JSON uygulamasındaki olayları yerel olarak destekler. [Cloudevents](https://cloudevents.io/) , olay verilerini tanımlamaya yönelik [açık bir belirtimdir](https://github.com/cloudevents/spec/blob/v1.0/spec.md) .

CloudEvents, bulut tabanlı olayları yayımlamak ve kullanmak için ortak bir olay şeması sağlayarak birlikte çalışabilirliği basitleştirir. Bu şema, tek düzen araç ve olayları yönlendirme ve işleme için standart yollar ve dış olay şemasının serisini kaldırma için evrensel yollar sağlar. Ortak bir şema ile, iş platformları arasında daha kolay bir şekilde tümleşebilir.

CloudEvents, [bulut Yerel Bilgi Işlem altyapısı](https://www.cncf.io/)aracılığıyla Microsoft gibi birkaç [ortak](https://github.com/cloudevents/spec/blob/master/community/contributors.md)şekilde oluşturulmuştur. Şu anda sürüm 1,0 olarak sunulmaktadır.

Bu makalede, Event Grid ile CloudEvents şemasının nasıl kullanılacağı açıklanır.

## <a name="cloudevent-schema"></a>CloudEvent şeması

CloudEvents biçiminde Azure Blob depolama olayına bir örnek aşağıda verilmiştir:

``` JSON
{
    "specversion": "1.0",
    "type": "Microsoft.Storage.BlobCreated",  
    "source": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-account}",
    "id": "9aeb0fdf-c01e-0131-0922-9eb54906e209",
    "time": "2019-11-18T15:13:39.4589254Z",
    "subject": "blobServices/default/containers/{storage-container}/blobs/{new-file}",
    "dataschema": "#",
    "data": {
        "api": "PutBlockList",
        "clientRequestId": "4c5dd7fb-2c48-4a27-bb30-5361b5de920a",
        "requestId": "9aeb0fdf-c01e-0131-0922-9eb549000000",
        "eTag": "0x8D76C39E4407333",
        "contentType": "image/png",
        "contentLength": 30699,
        "blobType": "BlockBlob",
        "url": "https://gridtesting.blob.core.windows.net/testcontainer/{new-file}",
        "sequencer": "000000000000000000000000000099240000000000c41c18",
        "storageDiagnostics": {
            "batchId": "681fe319-3006-00a8-0022-9e7cde000000"
        }
    }
}
```

Kullanılabilir alanlar, türleri ve tanımlarının ayrıntılı bir açıklaması için bkz. [Cloudevents v 1.0](https://github.com/cloudevents/spec/blob/v1.0/spec.md#required-attributes).

CloudEvents şemasında teslim edilen olaylar ve Event Grid şeması için üst bilgi değerleri, hariç aynıdır `content-type` . CloudEvents şeması için bu üst bilgi değeri olur `"content-type":"application/cloudevents+json; charset=utf-8"` . Event Grid şeması için bu üst bilgi değeri olur `"content-type":"application/json; charset=utf-8"` .

## <a name="configure-event-grid-for-cloudevents"></a>CloudEvents için Event Grid yapılandırma

CloudEvents şemasında olay girişi ve çıktısı için Event Grid kullanabilirsiniz. Aşağıdaki tabloda olası dönüşümler açıklanmaktadır:

 Event Grid kaynağı | Giriş şeması       | Teslim şeması
|---------------------|-------------------|---------------------
| Sistem konu başlıkları       | Olay Kılavuz şeması | Event Grid şeması veya CloudEvents şeması
| Özel konular/etki alanları | Olay Kılavuz şeması | Event Grid şeması veya CloudEvents şeması
| Özel konular/etki alanları | CloudEvents şeması | CloudEvents şeması
| Özel konular/etki alanları | Özel şema     | Özel şema, Event Grid şeması veya CloudEvents şeması
| İş ortağı konuları       | CloudEvents şeması | CloudEvents şeması

Tüm olay şemaları için, Event Grid bir konuya yayımlarken ve bir olay aboneliği oluştururken Event Grid doğrulama gerektirir.

Daha fazla bilgi için bkz. [güvenlik ve kimlik doğrulaması Event Grid](security-authentication.md).

### <a name="input-schema"></a>Giriş şeması

Özel konu başlığı oluştururken özel konu için giriş şemasını ayarlarsınız.

Azure CLı için şunu kullanın:

```azurecli-interactive
az eventgrid topic create \
  --name <topic_name> \
  -l westcentralus \
  -g gridResourceGroup \
  --input-schema cloudeventschemav1_0
```

PowerShell için şunu kullanın:

```azurepowershell-interactive
New-AzEventGridTopic `
  -ResourceGroupName gridResourceGroup `
  -Location westcentralus `
  -Name <topic_name> `
  -InputSchema CloudEventSchemaV1_0
```

### <a name="output-schema"></a>Çıktı şeması

Olay aboneliğini oluştururken çıktı şemasını ayarlarsınız.

Azure CLı için şunu kullanın:

```azurecli-interactive
topicID=$(az eventgrid topic show --name <topic-name> -g gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --name <event_subscription_name> \
  --source-resource-id $topicID \
  --endpoint <endpoint_URL> \
  --event-delivery-schema cloudeventschemav1_0
```

PowerShell için şunu kullanın:
```azurepowershell-interactive
$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name <topic-name>).Id

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -DeliverySchema CloudEventSchemaV1_0
```

 Şu anda, olay CloudEvents şemasında teslim edildiğinde Azure Işlevleri uygulaması için Event Grid tetikleyicisi kullanamazsınız. HTTP tetikleyicisi kullanın. CloudEvents şemasında olayları alan bir HTTP tetikleyicisi uygulama örnekleri için bkz. [Azure işlevleri Ile Cloudevents kullanma](#azure-functions).

## <a name="endpoint-validation-with-cloudevents-v10"></a>CloudEvents v 1.0 ile uç nokta doğrulaması

Event Grid zaten biliyorsanız, uygunsuz kullanımı önlemek için uç nokta doğrulama elsıkışmasının farkında olabilirsiniz. CloudEvents v 1.0, HTTP SEÇENEKLERI metodunu kullanarak kendi [uygunsuz kullanım koruma semantiğini](webhook-event-delivery.md) uygular. Bunun hakkında daha fazla bilgi edinmek için bkz. [olay teslimi-sürüm 1,0 Için HTTP 1,1 Web kancaları](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection). Çıktı için CloudEvents şemasını kullandığınızda Event Grid, Event Grid doğrulama olay mekanizması yerine CloudEvents v 1.0 kötüye kullanımı korumasını kullanır.

<a name="azure-functions"></a>

## <a name="use-with-azure-functions"></a>Azure Işlevleri ile kullanma

[Azure işlevleri Event Grid bağlama](../azure-functions/functions-bindings-event-grid.md) , cloudevents ' ı yerel olarak desteklemez, bu nedenle, http tarafından tetiklenen Işlevler, cloudevents iletilerini okumak için kullanılır. CloudEvents 'i okumak için bir HTTP tetikleyicisi kullandığınızda Event Grid tetikleyicisinin otomatik olarak yaptığı kod yazmanız gerekir:

* [Abonelik doğrulama isteğine](../event-grid/webhook-event-delivery.md) bir doğrulama yanıtı gönderir
* İstek gövdesinde bulunan olay dizisinin öğesi başına işlevi bir kez çağırır

İşlevi yerel olarak veya Azure 'da çalıştırıldığında kullanılacak URL hakkında daha fazla bilgi için bkz. [http tetikleyici bağlama başvurusu belgeleri](../azure-functions/functions-bindings-http-webhook.md).

Bir HTTP tetikleyicisi için aşağıdaki örnek C# kodu, Event Grid tetikleme davranışının benzetimini yapar. CloudEvents şemasında teslim edilen olaylar için bu örneği kullanın.

```csharp
[FunctionName("HttpTrigger")]
public static async Task<HttpResponseMessage> Run([HttpTrigger(AuthorizationLevel.Anonymous, "post", "options", Route = null)]HttpRequestMessage req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");
    if (req.Method == HttpMethod.Options)
    {
        // If the request is for subscription validation, send back the validation code
        
        var response = req.CreateResponse(HttpStatusCode.OK);
        response.Headers.Add("Webhook-Allowed-Origin", "eventgrid.azure.net");

        return response;
    }

    var requestmessage = await req.Content.ReadAsStringAsync();
    var message = JToken.Parse(requestmessage);

    // The request isn't for subscription validation, so it's for an event.
    // CloudEvents schema delivers one event at a time.
    log.LogInformation($"Source: {message["source"]}");
    log.LogInformation($"Time: {message["eventTime"]}");
    log.LogInformation($"Event data: {message["data"].ToString()}");

    return req.CreateResponse(HttpStatusCode.OK);
}
```

Bir HTTP tetikleyicisi için aşağıdaki örnek JavaScript kodu, Event Grid tetikleme davranışının benzetimini yapar. CloudEvents şemasında teslim edilen olaylar için bu örneği kullanın.

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');
    
    if (req.method == "OPTIONS") {
        // If the request is for subscription validation, send back the validation code
        
        context.log('Validate request received');
        context.res = {
            status: 200,
            headers: {
                'Webhook-Allowed-Origin': 'eventgrid.azure.net',
            },
         };
    }
    else
    {
        var message = req.body;
        
        // The request isn't for subscription validation, so it's for an event.
        // CloudEvents schema delivers one event at a time.
        var event = JSON.parse(message);
        context.log('Source: ' + event.source);
        context.log('Time: ' + event.eventTime);
        context.log('Data: ' + JSON.stringify(event.data));
    }
 
    context.done();
};
```

## <a name="next-steps"></a>Sonraki adımlar

* Olay teslimatlarını izleme hakkında bilgi için bkz. [izleyici Event Grid ileti teslimi](monitor-event-delivery.md).
* CloudEvents üzerinde test, yorum ve [katkıda bulunmanızı](https://github.com/cloudevents/spec/blob/master/community/CONTRIBUTING.md)öneririz.
* Azure Event Grid aboneliği oluşturma hakkında daha fazla bilgi için bkz. [Event Grid abonelik şeması](subscription-creation-schema.md).
