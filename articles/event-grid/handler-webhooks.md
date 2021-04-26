---
title: Azure Event Grid olaylar için olay işleyicileri olarak Web kancaları
description: Web kancalarını Azure Event Grid olayları için olay işleyicileri olarak nasıl kullanabileceğinizi açıklar. Azure Otomasyonu runbook 'ları ve Logic Apps, Web kancaları aracılığıyla olay işleyicileri olarak desteklenir.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: aa20c75789a18b93f787134dffe165e60ff1ab6f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91875810"
---
# <a name="webhooks-automation-runbooks-logic-apps-as-event-handlers-for-azure-event-grid-events"></a>Web kancaları, Otomasyon Runbook 'ları Azure Event Grid olaylar için olay işleyicileri olarak Logic Apps
Olay işleyicisi, olayın gönderildiği yerdir. İşleyici, olayı işlemek için başka bir eylem gerçekleştirir. Çeşitli Azure Hizmetleri, olayları işleyecek şekilde otomatik olarak yapılandırılır. Olayları işlemek için herhangi bir Web kancasını da kullanabilirsiniz. Web kancasının olayları işlemek için Azure 'da barındırılması gerekmez. Event Grid yalnızca HTTPS Web kancası uç noktalarını destekler.

> [!NOTE]
> - Azure Otomasyonu runbook 'ları ve Logic Apps, Web kancaları aracılığıyla olay işleyicileri olarak desteklenir. 
> - Bir Azure işlevini olay işleyicisi olarak yapılandırmak için **Web kancasını** bir **uç nokta türü** olarak kullanabilirsiniz, ancak bir uç nokta türü olarak **Azure işlevini** kullanın. Daha fazla bilgi için bkz. [Azure işlevi bir olay işleyicisi olarak](handler-functions.md).

## <a name="webhooks"></a>Web Kancaları
Web kancalarını olay işleyicileri olarak kullanmanın bir özeti ve örnekleri için aşağıdaki makalelere bakın. 

|Başlık  |Açıklama  |
|---------|---------|
| Hızlı başlangıç: [Azure CLI](custom-event-quickstart.md), [PowerShell](custom-event-quickstart-powershell.md)ve [Portal](custom-event-quickstart-portal.md)ile özel olaylar oluşturun ve yönlendirin. | Web kancasına nasıl özel olaylar gönderileceğini gösterir. |
| Hızlı başlangıç: Blob Depolama olaylarını- [Azure CLI](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json), [PowerShell](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json)ve [Portal](blob-event-quickstart-portal.md)ile özel bir Web uç noktasına yönlendirin. | Blob Storage olaylarının bir Web kancasına nasıl gönderileceğini gösterir. |
| [Hızlı başlangıç: kapsayıcı kayıt defteri olayları gönderme](../container-registry/container-registry-event-grid-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Container Registry olaylarını göndermek için Azure CLı 'nın nasıl kullanılacağını gösterir. |
| [Genel Bakış: HTTP uç noktasına olay alma](receive-events.md) | Bir olay aboneliğinden olayları almak ve olayları almak ve seri durumdan çıkarmak için bir HTTP uç noktasının nasıl doğrulanacağını açıklar. |


## <a name="azure-automation"></a>Azure Otomasyonu
Azure Otomasyonu runbook 'ları kullanarak olayları işleyebilirsiniz. Otomatikleştirilmiş runbook 'lar kullanılarak olayların işlenmesi Web kancaları aracılığıyla desteklenir. Runbook için bir Web kancası oluşturun ve ardından Web kancası işleyicisini kullanın. Örnek için aşağıdaki öğreticiye bakın: 

|Başlık  |Açıklama  |
|---------|---------|
|[Öğretici: Event Grid ve Microsoft ekipleriyle Azure Otomasyonu](ensure-tags-exists-on-new-virtual-machines.md) |Bir olay gönderen bir sanal makine oluşturun. Olay, sanal makineyi etiketleyen bir Otomasyon Runbook 'unu tetikler ve bir Microsoft ekipleri kanalına gönderilen bir ileti tetikler. |


## <a name="logic-apps"></a>Logic Apps
Event Grid olaylarını işlemek için iş süreçlerini uygulamak üzere **Logic Apps** kullanın. Bu senaryoda açıkça bir Web kancası oluşturmazsınız. Mantıksal uygulamayı Event Grid olayları işleyecek şekilde yapılandırdığınızda Web kancası sizin için otomatik olarak oluşturulur. Örnekler için aşağıdaki öğreticilere bakın: 

|Başlık  |Açıklama  |
|---------|---------|
| [Öğretici: Azure Event Grid ve Logic Apps ile sanal makine değişikliklerini Izleme](monitor-virtual-machine-changes-event-grid-logic-app.md) | Mantıksal uygulama, bir sanal makinedeki değişiklikleri izler ve bu değişiklikler hakkında e-posta gönderir. |
| [Öğretici: Logic Apps kullanarak Azure IoT Hub olaylarıyla ilgili e-posta bildirimleri gönderme](publish-iot-hub-events-to-logic-apps.md) | Logic App bir cihaz, IoT Hub 'ınıza her eklendiğinde bir bildirim e-postası gönderir. |
| [Öğretici: Azure Işlevleri ve Azure Logic Apps kullanarak Azure Event Grid aracılığıyla alınan Azure Service Bus olaylarına yanıt verme](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid, Service Bus konudan işlev uygulaması ve mantıksal uygulama 'a ileti gönderir. |

## <a name="rest-example-for-put"></a>REST örneği (PUT için)

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "WebHook",
            "properties": 
            {
                "endpointUrl": "<WEB HOOK URL>",
                "maxEventsPerBatch": 1,
                "preferredBatchSizeInKilobytes": 64
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

## <a name="next-steps"></a>Sonraki adımlar
Desteklenen olay işleyicilerinin bir listesi için bkz. [olay işleyicileri](event-handlers.md) makalesi. 
