---
title: Cloud-Azure Event Grid IoT Edge yayımlayın, olaylara abone olun | Microsoft Docs
description: IoT Edge Event Grid ile Web kancasını kullanarak bulutta olaylara abone olun, yayımlayın
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/29/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 5fb6cab4bfeea4308873210fb5f9122b37b61dcd
ms.sourcegitcommit: b45ee7acf4f26ef2c09300ff2dba2eaa90e09bc7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73100318"
---
# <a name="tutorial-publish-subscribe-to-events-in-cloud"></a>Öğretici: bulutta olaylara abone olma

Bu makalede, IoT Edge Event Grid kullanarak olayları yayımlamak ve bunlara abone olmak için gereken tüm adımlar açıklanmaktadır.

Devam etmeden önce bir olay Kılavuzu konusunun ve aboneliğinin ne olduğunu anlamak için bkz. [Event Grid kavramları](concepts.md) .

## <a name="prerequisites"></a>Önkoşullar 
Bu öğreticiyi tamamlayabilmeniz için şunlar gerekir:

* **Azure aboneliği** -henüz bir [hesabınız yoksa ücretsiz bir hesap](https://azure.microsoft.com/free) oluşturun. 
* **Azure IoT Hub ve IoT Edge cihazı** -henüz yoksa [Linux](../../iot-edge/quickstart-linux.md) veya [Windows cihazlarında](../../iot-edge/quickstart.md) Hızlı Başlangıç bölümündeki adımları izleyin.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-deploy-iot-edge.md)]

## <a name="create-an-azure-function-in-the-azure-portal"></a>Azure portal bir Azure işlevi oluşturun

Azure işlevi oluşturmak için [öğreticide](../../azure-functions/functions-create-first-azure-function.md) özetlenen adımları izleyin. 

Kod parçacığını aşağıdaki kodla değiştirin:

```csharp
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;
using Newtonsoft.Json;

public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);

    log.LogInformation($"C# HTTP trigger received {data}.");
    return data != null
        ? (ActionResult)new OkResult()
        : new BadRequestObjectResult("Please pass in the request body");
}
```

Yeni işlevinizde, sağ üst köşedeki **işlev URL 'Sini al** ' ı seçin, varsayılan (**işlev anahtarı**) seçeneğini belirleyin ve ardından **Kopyala**' yı seçin. İşlevin URL 'SI değerini daha sonra öğreticide kullanacaksınız.

> [!NOTE]
> EventGrid olay tetikleyicilerini kullanarak olaylara yeniden davranmaya yönelik daha fazla örnek ve öğretici için [Azure işlevleri](../../azure-functions/functions-overview.md) belgelerine bakın.

## <a name="create-a-topic"></a>Konu başlığı oluşturma

Bir olayın yayımcısı olarak bir olay Kılavuzu konusu oluşturmanız gerekir. Konu, yayımcıların olayları gönderebileceği bir uç nokta anlamına gelir.

1. Aşağıdaki içeriğe sahip Topic2. JSON oluşturun. Yük hakkındaki ayrıntılar için [API belgelerimize](api.md) bakın.

    ```json
         {
          "name": "sampleTopic2",
          "properties": {
            "inputschema": "eventGridSchema"
          }
        }
    ```
1. Konuyu oluşturmak için aşağıdaki komutu çalıştırın. 200 Tamam HTTP durum kodu döndürülmelidir.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic2.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2?api-version=2019-01-01-preview
    ```
1. Konunun başarıyla oluşturulduğunu doğrulamak için şu komutu çalıştırın. 200 Tamam HTTP durum kodu döndürülmelidir.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2?api-version=2019-01-01-preview
    ```

   Örnek çıktı:

   ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic2",
            "name": "sampleTopic2",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/sampleTopic2/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
   ```

## <a name="create-an-event-subscription"></a>Olay aboneliği oluşturma

Aboneler, bir konuya yayımlanan olaylara kaydolabilirler. Herhangi bir olay almak için, abonelerin ilgilendiğiniz konu başlığında bir olay Kılavuzu aboneliği oluşturması gerekir.

1. Aşağıdaki içeriğe sahip subscription2. JSON oluşturun. Yük hakkındaki ayrıntılar için [API belgelerimize](api.md) bakın.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-az-func-cloud-url>"
              }
            }
          }
        }
    ```

   >[!NOTE]
   > **EndpointType** , abonenin bir Web kancası olduğunu belirtir.  **EndpointUrl** , abonenin olayları dinlediği URL 'yi belirtir. Bu URL, daha önce kurulum yaptığınız Azure Işlev örneğine karşılık gelir.
2. Aboneliği oluşturmak için aşağıdaki komutu çalıştırın. 200 Tamam HTTP durum kodu döndürülmelidir.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription2.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2/eventSubscriptions/sampleSubscription2?api-version=2019-01-01-preview
    ```
3. Aboneliğin başarıyla oluşturulduğunu doğrulamak için şu komutu çalıştırın. 200 Tamam HTTP durum kodu döndürülmelidir.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2/eventSubscriptions/sampleSubscription2?api-version=2019-01-01-preview
    ```

    Örnek çıktı:

   ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic2/eventSubscriptions/sampleSubscription2",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription2",
          "properties": {
            "Topic": "sampleTopic2",
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-az-func-cloud-url>"
              }
            }
          }
        }
    ```

## <a name="publish-an-event"></a>Olay yayımlama

1. Aşağıdaki içeriğe sahip EVENT2. JSON oluşturun. Yük hakkındaki ayrıntılar için [API belgelerimize](api.md) bakın.

    ```json
        [
          {
            "id": "eventId-func-1",
            "eventType": "recordInserted",
            "subject": "myapp/vehicles/motorcycles",
            "eventTime": "2019-07-28T21:03:07+00:00",
            "dataVersion": "1.0",
            "data": {
              "make": "Ducati",
              "model": "Monster"
            }
          }
        ]
    ```
1. Olayı yayımlamak için şu komutu çalıştırın

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event2.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2/events?api-version=2019-01-01-preview
    ```

## <a name="verify-event-delivery"></a>Olay teslimini doğrulama

İşlevin **izleyici** seçeneğinde Azure Portal teslim edilen olayı görüntüleyebilirsiniz.

## <a name="cleanup-resources"></a>Kaynakları temizleme

* Konuyu ve tüm aboneliklerini silmek için aşağıdaki komutu çalıştırın

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2?api-version=2019-01-01-preview
    ```

* Azure portal oluşturulan Azure işlevini silin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, bir olay Kılavuzu konusu, abonelik ve yayımlanan olaylar oluşturdunuz. Artık temel adımları bildiğinize göre aşağıdaki makalelere bakın:

* IoT Edge Azure Event Grid kullanmayla ilgili sorunları gidermek için bkz. [sorun giderme kılavuzu](troubleshoot.md).
* [Filtrelerle](advanced-filtering.md)abonelik oluşturun/güncelleştirin.
* [Linux](persist-state-linux.md) veya [Windows](persist-state-windows.md) üzerinde Event Grid modülünün kalıcılığını ayarlama
* İstemci kimlik doğrulamasını yapılandırmak için [belgeleri](configure-client-auth.md) izleyin
* Bu [öğreticiyi](forward-events-event-grid-cloud.md) izleyerek olayları buluta Azure Event Grid iletin
