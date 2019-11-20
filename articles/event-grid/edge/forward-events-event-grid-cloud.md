---
title: Event Grid bulut Azure Event Grid IoT Edge için Edge olaylarını ilet | Microsoft Docs
description: Edge olaylarını Event Grid buluta ilet
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/29/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 502a495bad4115daf9f0f4ffed276a307adf1fc4
ms.sourcegitcommit: b45ee7acf4f26ef2c09300ff2dba2eaa90e09bc7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73100647"
---
# <a name="tutorial-forward-events-to-event-grid-cloud"></a>Öğretici: olayları Event Grid buluta Iletme

Bu makalede, Edge olaylarını Azure bulutundaki Event Grid iletmek için gereken tüm adımlar açıklanmaktadır. Bunu aşağıdaki nedenlerle yapmak isteyebilirsiniz:

* Bulutta Edge olaylarına tepki verin.
* Olayları bulutta Event Grid iletmek ve Azure Event Hubs ya da Azure depolama kuyruklarını kullanarak olayları bulutta işlemeden önce arabelleğe almak için kullanın.

Bu öğreticiyi tamamlayabilmeniz için [Edge](concepts.md) ve [Azure](../concepts.md)'da Event Grid kavramlarının anlaşılmasına ihtiyacınız vardır.

## <a name="prerequisites"></a>Önkoşullar 
Bu öğreticiyi tamamlayabilmeniz için şunlar gerekir:

* **Azure aboneliği** -henüz bir [hesabınız yoksa ücretsiz bir hesap](https://azure.microsoft.com/free) oluşturun. 
* **Azure IoT Hub ve IoT Edge cihazı** -henüz yoksa [Linux](../../iot-edge/quickstart-linux.md) veya [Windows cihazlarında](../../iot-edge/quickstart.md) Hızlı Başlangıç bölümündeki adımları izleyin.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-deploy-iot-edge.md)] 
## <a name="create-event-grid-topic-and-subscription-in-cloud"></a>Bulutta olay Kılavuzu konusu ve abonelik oluşturma

[Bu öğreticiyi](../custom-event-quickstart-portal.md)izleyerek bulutta bir olay Kılavuzu konusu ve abonelik oluşturun. Öğreticide daha sonra kullanacağınız yeni oluşturulan konunun `topicURL`, `sasKey`ve `topicName` unutmayın.

Örneğin, Batı ABD `testegcloudtopic` adlı bir konu oluşturduysanız, değerler şöyle görünür:

* **Topicurl**: `https://testegcloudtopic.westus2-1.eventgrid.azure.net/api/events`
* **TopicName**: `testegcloudtopic`
* **Saskey**: konunun **AccessKey** altında bulunabilir. **KEY1**kullanın.

## <a name="create-event-grid-topic-at-the-edge"></a>Kenarda olay kılavuzu oluşturma konusu

1. Aşağıdaki içeriğe sahip Topic3. JSON oluşturun. Yük hakkındaki ayrıntılar için [API belgelerimize](api.md) bakın.

    ```json
        {
          "name": "sampleTopic3",
          "properties": {
            "inputschema": "eventGridSchema"
          }
        }
    ```
1. Konuyu oluşturmak için aşağıdaki komutu çalıştırın. 200 Tamam HTTP durum kodu döndürülmelidir.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic3.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3?api-version=2019-01-01-preview
    ```
1. Konunun başarıyla oluşturulduğunu doğrulamak için şu komutu çalıştırın. 200 Tamam HTTP durum kodu döndürülmelidir.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3?api-version=2019-01-01-preview
    ```

   Örnek çıktı:

   ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic3",
            "name": "sampleTopic3",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/sampleTopic3/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
   ```
  
## <a name="create-event-grid-subscription-at-the-edge"></a>Kenarda Event Grid abonelik oluşturma


1. Aşağıdaki içeriğe sahip subscription3. JSON oluşturun. Yük hakkındaki ayrıntılar için [API belgelerimize](api.md) bakın.

   ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventGrid",
              "properties": {
                        "endpointUrl": "<your-event-grid-cloud-topic-endpoint-url>?api-version=2018-01-01",
                        "sasKey": "<your-event-grid-topic-saskey>",
                        "topicName": null
              }
            }
          }
    }
   ```

   >[!NOTE]
   > **EndpointUrl** , bulutta Event Grid konusunun URL 'sini belirtir. **Saskey** Event Grid bulut konusunun anahtarına başvurur. **TopicName** içindeki değer tüm giden olayları Event Grid için damgalamak üzere kullanılacaktır. Bu, bir Event Grid etki alanı konusuna naklederken yararlı olabilir. Event Grid etki alanı konusu hakkında daha fazla bilgi için bkz. [olay etki alanları](../event-domains.md)

    Örneğin,
  
    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventGrid",
              "properties": {
                "endpointUrl": "https://testegcloudtopic.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01",
                 "sasKey": "<your-event-grid-topic-saskey>",
                 "topicName": null
              }
            }
          }
        }
    ```

2. Aboneliği oluşturmak için aşağıdaki komutu çalıştırın. 200 Tamam HTTP durum kodu döndürülmelidir.

     ```sh
     curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription3.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3/eventSubscriptions/sampleSubscription3?api-version=2019-01-01-preview
     ```

3. Aboneliğin başarıyla oluşturulduğunu doğrulamak için şu komutu çalıştırın. 200 Tamam HTTP durum kodu döndürülmelidir.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3/eventSubscriptions/sampleSubscription3?api-version=2019-01-01-preview
    ```

    Örnek çıktı:

    ```json
         {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic3/eventSubscriptions/sampleSubscription3",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription3",
          "properties": {
            "Topic": "sampleTopic3",
            "destination": {
              "endpointType": "eventGrid",
              "properties": {
                "endpointUrl": "https://testegcloudtopic.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01",
                "sasKey": "<your-event-grid-topic-saskey>",
                "topicName": null
              }
            }
          }
        }
    ```

## <a name="publish-an-event-at-the-edge"></a>Bir olayı kenarda yayımlayın

1. Aşağıdaki içeriğe sahip event3. JSON oluşturun. Yük hakkındaki ayrıntılar için bkz. [API belgeleri](api.md) .

    ```json
        [
          {
            "id": "eventId-egcloud-0",
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

1. Şu komutu çalıştırın:

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event3.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3/events?api-version=2019-01-01-preview
    ```

## <a name="verify-edge-event-in-cloud"></a>Bulutta kenar olayını doğrulama

Bulut konusu tarafından sunulan olayları görüntüleme hakkında daha fazla bilgi için [öğreticiye](../custom-event-quickstart-portal.md)bakın.

## <a name="cleanup-resources"></a>Kaynakları temizleme

* Konuyu ve tüm aboneliklerini silmek için aşağıdaki komutu çalıştırın

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3?api-version=2019-01-01-preview
    ```

* Bulutta (Azure Event Grid) oluşturulan konu ve abonelikleri de silin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, kenarda bir olay yayımladınız ve Azure bulutu 'nda Event Grid iletilir. Artık, bulutta Event Grid iletmek için temel adımları öğrenmiş olduğunuza göre:

* IoT Edge Azure Event Grid kullanmayla ilgili sorunları gidermek için bkz. [sorun giderme kılavuzu](troubleshoot.md).
* Bu [öğreticiyi](forward-events-iothub.md) Izleyerek olayları ıothub 'e iletin
* Bu [öğreticiyi](pub-sub-events-webhook-cloud.md) izleyerek etkinlikleri bulutta Web kancasına iletin
