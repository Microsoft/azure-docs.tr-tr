---
title: Verilerinizi Azure Event Hubs 'ye aktarın ve Azure Service Bus | Microsoft Docs
description: Azure IoT Central uygulamanızdan Azure Event Hubs ve Azure Service Bus verileri dışarı aktarma
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 07/09/2019
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: 4a0959c7562c3868825e26366ae08e9b87f92319
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73177218"
---
# <a name="export-your-data-in-azure-iot-central"></a>Azure IoT Central verilerinizi dışarı aktarın

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

*Bu konu, Yöneticiler için geçerlidir.*

Bu makalede verilerinizi kendi **azure Event Hubs**ve **Azure Service Bus** örneklerine aktarmak için Azure IoT Central sürekli veri dışa aktarma özelliğinin nasıl kullanılacağı açıklanır. Normal yol öngörüleri ve analizler için **ölçümleri**, **cihazları**ve **cihaz şablonlarını** kendi Hedefinizdeki dışa aktarabilirsiniz. Bu, Azure Stream Analytics özel kuralları tetiklemeyi, Azure Logic Apps özel iş akışlarını tetiklemeyi veya verileri dönüştürmeyi ve Azure Işlevleri aracılığıyla geçirmeyi içerir. 

> [!Note]
> Bir kez daha, sürekli veri dışarı aktarmayı açtığınızda, yalnızca o andan itibaren verileri alırsınız. Şu anda, sürekli veri dışa aktarma kapalı olduğunda veriler bir saat için alınamaz. Daha fazla geçmiş verileri sürdürmek için sürekli veri dışa aktarmayı erken açın.


## <a name="prerequisites"></a>Önkoşullar

- IoT Central uygulamanızda yönetici olmanız gerekir

## <a name="set-up-export-destination"></a>Dışarı aktarma hedefini ayarla

Uygulamasına dışarı aktarmak için mevcut bir Event Hubs/Service Bus yoksa, oluşturmak için aşağıdaki adımları izleyin:

### <a name="create-event-hubs-namespace"></a>Event Hubs ad alanı oluştur

1. [Azure Portal yeni bir Event Hubs ad alanı](https://ms.portal.azure.com/#create/Microsoft.EventHub)oluşturun. [Azure Event Hubs docs](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)'ta daha fazla bilgi edinebilirsiniz.
2. Bir abonelik seçin. 

    > [!Note] 
    > Artık verileri, Kullandıkça Öde IoT Central uygulamanızla **aynı olmayan** diğer aboneliklerle dışarı aktarabilirsiniz. Bu durumda bir bağlantı dizesi kullanarak bağlanacaksınız.
3. Event Hubs ad alanında bir olay hub 'ı oluşturun. Ad alanına gidin ve bir olay hub 'ı örneği oluşturmak için en üstteki **+ Olay Hub 'ını** seçin.

### <a name="create-service-bus-namespace"></a>Service Bus ad alanı oluştur

1. [Azure Portal yeni bir Service Bus ad alanı](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5) oluşturun. [Azure Service Bus docs](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-create-namespace-portal)' de daha fazla bilgi edinebilirsiniz.
2. Bir abonelik seçin. 

    > [!Note] 
    > Artık verileri, Kullandıkça Öde IoT Central uygulamanızla **aynı olmayan** diğer aboneliklerle dışarı aktarabilirsiniz. Bu durumda bir bağlantı dizesi kullanarak bağlanacaksınız.

3. Service Bus ad alanına gidin ve en üstteki **+ kuyruk** veya **+ Konu** başlığını seçerek dışarı aktarılacak bir kuyruk veya konu oluşturun.


## <a name="set-up-continuous-data-export"></a>Sürekli veri dışarı aktarma ayarlama

Verilerin dışarı aktarılacağı Event Hubs/Service Bus hedefine sahip olduğunuza göre, sürekli veri dışa aktarma ayarlamak için aşağıdaki adımları izleyin. 

1. IoT Central uygulamanızda oturum açın.

2. Sol bölmede **sürekli veri dışarı aktarma**' yı seçin.

    > [!Note]
    > Sol bölmede sürekli veri dışa aktarma görmüyorsanız, uygulamanızda yönetici değilsiniz demektir. Verilerin dışarı aktarılmasını ayarlamak için bir yöneticiye danışın.

3. Sağ üst köşedeki **+ Yeni** düğmesini seçin. Dışarı aktarmanın hedefi olarak **Azure Event Hubs** veya **Azure Service Bus** birini seçin. 

    > [!NOTE] 
    > Uygulama başına en fazla dışarı aktarma sayısı beştir. 

    ![Yeni sürekli veri dışa aktarma oluştur](media/howto-export-data/export-new2.png)

4. Aşağı açılan liste kutusunda **Event Hubs ad alanı/Service Bus ad alanını**seçin. Ayrıca, listede **bir bağlantı dizesi girerek**son seçeneği de seçebilirsiniz. 

    > [!NOTE] 
    > Depolama hesapları/Event Hubs ad alanları/Service Bus ad alanlarını **IoT Central uygulamanız ile aynı abonelikte**görürsünüz. Bu aboneliğin dışında bir hedefe aktarmak istiyorsanız, **bir bağlantı dizesi girin** ' i seçin ve 5. adıma bakın.

    > [!NOTE] 
    > 7 günlük deneme uygulamaları için sürekli veri vermeyi yapılandırmanın tek yolu bir bağlantı dizesidir. Bunun nedeni 7 günlük deneme uygulamalarının ilişkili bir Azure aboneliğine sahip olmaması olabilir.

    ![Yeni CDE Olay Hub 'ı oluştur](media/howto-export-data/export-eh.png)

5. Seçim **Bir bağlantı dizesi girin**' i seçerseniz, Bağlantı dizenizi yapıştırmanız için yeni bir kutu belirir. İçin bağlantı dizesini almak için:
    - Event Hubs veya Service Bus, Azure portal ad alanına gidin.
        - **Ayarlar**altında, **paylaşılan erişim ilkeleri** ' ni seçin.
        - Varsayılan **RootManageSharedAccessKey** seçin veya yeni bir tane oluşturun
        - Birincil veya ikincil bağlantı dizesini kopyalayın
 
6. Açılan liste kutusundan bir olay hub 'ı/kuyruğu veya konu başlığını seçin.

7. **Dışarı aktarılacak veriler**' in altında, türü **üzerine**ayarlayarak dışarı aktarılacak her bir veri türünü belirtin.

8. Sürekli veri dışa aktarmayı açmak için, **veri dışa aktarma** geçişi 'nin **Açık**olduğundan emin olun. **Kaydet**’i seçin.

9. Birkaç dakika sonra verileriniz seçtiğiniz hedefte görüntülenir.


## <a name="data-format"></a>Veri biçimi

Ölçümler, cihazlar ve cihaz şablonları verileri, Olay Hub 'ınıza veya Service Bus kuyruğuna veya konuya neredeyse gerçek zamanlı olarak verilir. Aktarılmış ölçüm verileri, cihazlarınızın yalnızca ölçümlerin değerlerini değil, IoT Central gönderilen iletinin tamamını içerir. Aktarılmış cihazlar verileri tüm cihazların özelliklerine ve ayarlarına yapılan değişiklikleri içerir ve aktarılmış cihaz şablonları tüm cihaz şablonlarında değişiklikler içerir. "Body" özelliği olan ve JSON biçiminde olan veriler.

> [!NOTE]
> Dışarı aktarma hedefi olarak bir Service Bus seçerken, kuyruklar ve konuların **oturumları veya yinelenen algılama özelliği etkin olmalıdır**. Bu seçeneklerden biri etkinleştirilirse, bazı iletiler kuyruğunuza veya konuya ulaşmayacaktır.

### <a name="measurements"></a>Ölçümler

IoT Central bir cihazdan ileti aldıktan sonra yeni bir ileti hızlı bir şekilde verilir. Event Hubs ve Service Bus içindeki her bir dışarıya gönderilen ileti, cihazın JSON biçimindeki "Body" özelliğinde gönderildiği tam iletiyi içerir. 

> [!NOTE]
> Ölçümleri gönderen cihazlar cihaz kimliklerine göre temsil edilir (aşağıdaki bölümlere bakın). Cihazların adlarını almak için, cihaz verilerini dışa aktarın ve cihaz iletisinin **DeviceID** 'Siyle eşleşen **connectiondeviceıd** 'yi kullanarak her bir ileti ile bağıntı kurun.

Aşağıdaki örnek, Olay Hub 'ında veya Service Bus kuyruğu veya konusunda alınan ölçüm verileri hakkında bir ileti gösterir.

```json
{
  "body": {
    "humidity": 29.06963648666288,
    "temp": 8.4503795661685,
    "pressure": 1075.8334910110093,
    "magnetometerX": 408.6966458887116,
    "magnetometerY": -532.8809796603962,
    "magnetometerZ": 174.70653875528205,
    "accelerometerX": 1481.546749013788,
    "accelerometerY": -1139.4316656437406,
    "accelerometerZ": 811.6928695575307,
    "gyroscopeX": 442.19879163299856,
    "gyroscopeY": 123.23710975717177,
    "gyroscopeZ": 708.5397575786151,
    "deviceState": "DANGER"
  },
  "annotations": {
    "iothub-connection-device-id": "<connectionDeviceId>",
    "iothub-connection-auth-method": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "iothub-connection-auth-generation-id": "<generationId>",
    "iothub-enqueuedtime": 1539381029965,
    "iothub-message-source": "Telemetry",
    "x-opt-sequence-number": 25325,
    "x-opt-offset": "<offset>",
    "x-opt-enqueued-time": 1539381030200
  },
  "sequenceNumber": 25325,
  "enqueuedTimeUtc": "2018-10-02T21:50:30.200Z",
  "offset": "<offset>",
  "properties": {
    "content_type": "application/json",
    "content_encoding": "utf-8"
  }
}
```

### <a name="devices"></a>Cihazlar

Cihaz verileri içeren iletiler, her birkaç dakikada bir olay hub 'ınıza veya Service Bus kuyruğuna veya konuya gönderilir. Bu, birkaç dakika içinde, bir dizi iletinin bir toplu iş hakkında verilerle birlikte geldiğini
- Eklenen yeni cihazlar
- Değiştirilen özellik ve ayar değerleri içeren cihazlar

Her ileti, son dışarıya alınan iletiden bu yana bir cihazdaki bir veya daha fazla değişikliği temsil eder. Her iletide gönderilecek bilgiler şunları içerir:
- IoT Central cihaz `id`
- Cihazın `name`
- [cihaz sağlama hizmeti](/azure/iot-central/core/howto-connect-nodejs) 'nden `deviceId`
- Cihaz şablonu bilgileri
- Özellik değerleri
- Ayar değerleri

> [!NOTE]
> Son Batch, bu yana silinen cihazlar tarafından silindi. Şu anda, silinen cihazlar için, aktarılmış iletilerde hiçbir gösterge yok.
>
> Her cihazın ait olduğu cihaz şablonu, bir cihaz şablonu KIMLIĞI ile temsil edilir. Cihaz şablonunun adını almak için, cihaz şablonu verilerini de dışarı aktardığınızdan emin olun.

Aşağıdaki örnek, Olay Hub 'ındaki veya Service Bus kuyruğu veya konusunun cihaz verileri hakkında bir ileti gösterir:


```json
{
  "body": {
    "id": "<id>",
    "name": "<deviceName>",
    "simulated": true,
    "deviceId": "<deviceId>",
    "deviceTemplate": {
      "id": "<templateId>",
      "version": "1.0.0"
    },
    "properties": {
      "cloud": {
        "location": "Seattle"
      },
      "device": {
        "dieNumber": 5445.5862873026645
      }
    },
    "settings": {
      "device": {
        "fanSpeed": 0
      }
    }
  },
  "annotations": {
    "iotcentral-message-source": "devices",
    "x-opt-partition-key": "<partitionKey>",
    "x-opt-sequence-number": 39740,
    "x-opt-offset": "<offset>",
    "x-opt-enqueued-time": 1539274959654
  },
  "partitionKey": "<partitionKey>",
  "sequenceNumber": 39740,
  "enqueuedTimeUtc": "2018-10-02T16:22:39.654Z",
  "offset": "<offset>",
}
```

### <a name="device-templates"></a>Cihaz şablonları

Cihaz şablonu verilerini içeren iletiler, her birkaç dakikada bir olay hub 'ınıza veya Service Bus kuyruğuna veya konuya gönderilir. Bu, birkaç dakika içinde, bir dizi iletinin bir toplu iş hakkında verilerle birlikte geldiğini
- Eklenen yeni cihaz şablonları
- Değiştirilen ölçümler, özellik ve ayar tanımlarına sahip cihaz şablonları

Her ileti, son dışarıya alınan iletiden bu yana bir cihaz şablonunda yapılan bir veya daha fazla değişikliği temsil eder. Her iletide gönderilecek bilgiler şunları içerir:
- cihaz şablonunun `id`
- cihaz şablonunun `name`
- cihaz şablonunun `version`
- Ölçüm veri türleri ve min/max değerleri
- Özellik veri türleri ve varsayılan değerler
- Veri türlerini ve varsayılan değerleri ayarlama

> [!NOTE]
> Son Batch 'in aktarılmasından bu yana cihaz şablonları silindi. Şu anda, Silinen cihaz şablonları için, aktarılmış iletilerde hiçbir gösterge yok.

Aşağıdaki örnek, Olay Hub 'ında veya Service Bus kuyruğu veya konusunun cihaz şablonları verileri hakkında bir ileti gösterir:

```json
{ 
  "body":{ 
    "id":"<id>",
    "version":"1.0.0",
    "name":"<templateName>",
    "measurements":{ 
      "telemetry":{ 
        "humidity":{ 
          "dataType":"double",
          "name":"humidity"
        },
        "pressure":{ 
          "dataType":"double",
          "name":"pressure"
        },
        "temp":{ 
          "dataType":"double",
          "name":"temperature"
        }
      }
    },
    "properties":{ 
      "cloud":{ 
        "location":{ 
          "dataType":"string",
          "name":"Location"
        }
      },
      "device":{ 
        "dieNumber":{ 
          "dataType":"double",
          "name":"Die Number"
        }
      }
    },
    "settings":{ 
      "device":{ 
        "fanSpeed":{ 
          "dataType":"double",
          "name":"Fan Speed",
          "initialValue":0
        }
      }
    }
  },
  "annotations":{ 
    "iotcentral-message-source":"deviceTemplates",
    "x-opt-partition-key":"<partitionKey>",
    "x-opt-sequence-number":25315,
    "x-opt-offset":"<offset>",
    "x-opt-enqueued-time":1539274985085
  },
  "partitionKey":"<partitionKey>",
  "sequenceNumber":25315,
  "enqueuedTimeUtc":"2018-10-02T16:23:05.085Z",
  "offset":"<offset>"
}
```

## <a name="next-steps"></a>Sonraki adımlar

Artık verilerinizi Azure Event Hubs ve Azure Service Bus dışarı aktarmayı öğrenmiş olduğunuza göre, bir sonraki adımla devam edin:

> [!div class="nextstepaction"]
> [Azure Işlevleri 'ni tetikleme](howto-trigger-azure-functions.md)
