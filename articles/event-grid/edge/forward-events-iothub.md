---
title: Event Grid olaylarını ıothub Azure Event Grid IoT Edge 'e ilet | Microsoft Docs
description: Event Grid olaylarını ıothub 'e ilet
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/29/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 073205b5bdc3f6de80bd7e347469c3f06aeb515b
ms.sourcegitcommit: b45ee7acf4f26ef2c09300ff2dba2eaa90e09bc7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73098670"
---
# <a name="tutorial-forward-events-to-iothub"></a>Öğretici: olayları ıothub 'e Ilet

Bu makalede, Event Grid olaylarını diğer IoT Edge modüllerine iletmek için gereken tüm adımlar gösterilmektedir, rotalar kullanılarak ıothub. Bunu aşağıdaki nedenlerle yapmak isteyebilirsiniz:

* EdgeHub 'ın yönlendirmesi ile zaten mevcut olan yatırımları kullanmaya devam edin
* Tüm olayları yalnızca IoT Hub aracılığıyla bir cihazdan yönlendirmeyi tercih et

Bu öğreticiyi tamamlayabilmeniz için aşağıdaki kavramları anlamanız gerekir:

- [Event Grid kavramlar](concepts.md)
- [IoT Edge hub 'ı](../../iot-edge/module-composition.md) 

## <a name="prerequisites"></a>Önkoşullar 
Bu öğreticiyi tamamlayabilmeniz için şunlar gerekir:

* **Azure aboneliği** -henüz bir [hesabınız yoksa ücretsiz bir hesap](https://azure.microsoft.com/free) oluşturun. 
* **Azure IoT Hub ve IoT Edge cihazı** -henüz yoksa [Linux](../../iot-edge/quickstart-linux.md) veya [Windows cihazlarında](../../iot-edge/quickstart.md) Hızlı Başlangıç bölümündeki adımları izleyin.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-deploy-iot-edge.md)]

## <a name="create-topic"></a>Konu Oluştur

Bir olayın yayımcısı olarak bir olay Kılavuzu konusu oluşturmanız gerekir. Bu konu, yayımcıların daha sonra olay gönderebileceği bir uç nokta anlamına gelir.

1. Aşağıdaki içeriğe sahip Topic4. JSON oluşturun. Yük hakkındaki ayrıntılar için [API belgelerimize](api.md) bakın.

   ```json
    {
          "name": "sampleTopic4",
          "properties": {
            "inputschema": "eventGridSchema"
          }
    }
    ```
1. Konuyu oluşturmak için aşağıdaki komutu çalıştırın. 200 Tamam HTTP durum kodu döndürülmelidir.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic4.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4?api-version=2019-01-01-preview
    ```

1. Konunun başarıyla oluşturulduğunu doğrulamak için şu komutu çalıştırın. 200 Tamam HTTP durum kodu döndürülmelidir.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4?api-version=2019-01-01-preview
    ```

   Örnek çıktı:

   ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic4",
            "name": "sampleTopic4",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/sampleTopic4/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
   ```

## <a name="create-event-subscription"></a>Olay aboneliği oluştur

Aboneler, bir konuya yayımlanan olaylara kaydolabilirler. Herhangi bir olay almak için, ilgilenilen bir konu başlığında bir olay Kılavuzu aboneliği oluşturulması gerekir.

1. Aşağıdaki içeriğe sahip subscription4. JSON oluşturun. Yük hakkındaki ayrıntılar için [API belgelerimize](api.md) bakın.

   ```json
    {
          "properties": {
                "destination": {
                      "endpointType": "edgeHub",
                      "properties": {
                            "outputName": "sampleSub4"
                      }
                }
          }
    }
   ```

   >[!NOTE]
   > `endpointType`, abonenin `edgeHub`belirtir. `outputName`, Event Grid modülünün bu abonelikle eşleşen olayları edgeHub 'a yönlendireceğini belirten çıktıyı belirtir. Örneğin, yukarıdaki abonelikle eşleşen olaylar `/messages/modules/eventgridmodule/outputs/sampleSub4`yazılır.
2. Aboneliği oluşturmak için aşağıdaki komutu çalıştırın. 200 Tamam HTTP durum kodu döndürülmelidir.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription4.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4/eventSubscriptions/sampleSubscription4?api-version=2019-01-01-preview
    ```
3. Aboneliğin başarıyla oluşturulduğunu doğrulamak için şu komutu çalıştırın. 200 Tamam HTTP durum kodu döndürülmelidir.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4/eventSubscriptions/sampleSubscription4?api-version=2019-01-01-preview
    ```

    Örnek çıktı:

   ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic4/eventSubscriptions/sampleSubscription4",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription4",
          "properties": {
            "Topic": "sampleTopic4",
            "destination": {
                      "endpointType": "edgeHub",
                      "properties": {
                            "outputName": "sampleSub4"
                      }
                }
          }
        }
    ```

## <a name="set-up-an-edge-hub-route"></a>Edge hub 'ı yolu ayarlama

Aşağıdaki gibi, ıothub 'e iletilmek üzere olay aboneliğinin olaylarını iletmek için Edge hub 'ın yolunu güncelleştirin:

1. [Azure portalda](https://ms.portal.azure.com) oturum açma
1. **IoT Hub**gidin.
1. Menüden **IoT Edge** seçin
1. Cihaz listesinden hedef cihazın KIMLIĞINI seçin.
1. **Modülleri Ayarlama**'yı seçin.
1. **İleri ' yi** ve rotalar bölümünü seçin.
1. Rotalarda yeni bir rota ekleyin

  ```sh
  "fromEventGridToIoTHub":"FROM /messages/modules/eventgridmodule/outputs/sampleSub4 INTO $upstream"
  ```

  Örneğin,

  ```json
  {
      "routes": {
        "fromEventGridToIoTHub": "FROM /messages/modules/eventgridmodule/outputs/sampleSub4 INTO $upstream"
      }
  }
  ```

   >[!NOTE]
   > Yukarıdaki yol, IoT Hub 'ına iletilmek üzere bu abonelikle eşleşen tüm olayları iletecektir. Daha fazla filtrelemek ve Event Grid olaylarını diğer IoT Edge modüllerine yönlendirmek için [Edge hub 'ı yönlendirme](../../iot-edge/module-composition.md) özelliklerini kullanabilirsiniz.

## <a name="setup-iot-hub-route"></a>IoT Hub yolunu ayarla

Event Grid modülünden iletilen olayları görüntüleyebilmeniz için IoT Hub 'ından bir yol ayarlamak üzere [IoT Hub yönlendirme öğreticisine](../../iot-hub/tutorial-routing.md) bakın. Öğreticiyi devam etmek için sorgu için `true` kullanın.  



## <a name="publish-an-event"></a>Olay yayımlama

1. Aşağıdaki içeriğe sahip event4. JSON oluşturun. Yük hakkındaki ayrıntılar için [API belgelerimize](api.md) bakın.

    ```json
        [
          {
            "id": "eventId-iothub-1",
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

1. Olayı yayımlamak için şu komutu çalıştırın:

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event4.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4/events?api-version=2019-01-01-preview
    ```

## <a name="verify-event-delivery"></a>Olay teslimini doğrulama

Olayları görüntüleme adımları için bkz. IoT Hub [yönlendirme öğreticisi](../../iot-hub/tutorial-routing.md) .

## <a name="cleanup-resources"></a>Kaynakları temizleme

* Konuyu ve tüm aboneliklerini kenarda silmek için aşağıdaki komutu çalıştırın:

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4?api-version=2019-01-01-preview
    ```
* Bulutta ıothub yönlendirmesi ayarlanırken oluşturulan tüm kaynakları silin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, bir olay Kılavuzu konusu, Edge hub aboneliği ve yayımlanan olaylar oluşturdunuz. Artık bir Edge hub 'ına iletmek için temel adımları öğrenmiş olduğunuza göre, aşağıdaki makalelere bakın:

* IoT Edge Azure Event Grid kullanmayla ilgili sorunları gidermek için bkz. [sorun giderme kılavuzu](troubleshoot.md).
* Olayları bölümlemek için [Edge hub](../../iot-edge/module-composition.md) yol filtrelerini kullanma
* [Linux](persist-state-linux.md) veya [Windows](persist-state-windows.md) üzerinde Event Grid modülünün kalıcılığını ayarlama
* İstemci kimlik doğrulamasını yapılandırmak için [belgeleri](configure-client-auth.md) izleyin
* Bu [öğreticiyi](forward-events-event-grid-cloud.md) izleyerek olayları buluta Azure Event Grid iletin
