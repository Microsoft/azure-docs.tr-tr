---
title: Azure Event Grid olay işleyicileri
description: Azure Event Grid için desteklenen olay işleyicilerini açıklar
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: spelluru
ms.openlocfilehash: 279d7f2ac6481f3aa3ebd8e5a18a52b9e52f6201
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74169313"
---
# <a name="event-handlers-in-azure-event-grid"></a>Azure Event Grid içindeki olay işleyicileri

Olay işleyicisi, olayın gönderildiği yerdir. İşleyici, olayı işlemek için başka bir eylem gerçekleştirir. Çeşitli Azure Hizmetleri, olayları işleyecek şekilde otomatik olarak yapılandırılır. Olayları işlemek için herhangi bir Web kancasını da kullanabilirsiniz. Web kancasının olayları işlemek için Azure 'da barındırılması gerekmez. Event Grid yalnızca HTTPS Web kancası uç noktalarını destekler.

Bu makale, her olay işleyicisi için içerik bağlantıları sağlar.

## <a name="azure-automation"></a>Azure Otomasyonu

Otomatik runbook 'larla olayları işlemek için Azure Otomasyonu 'nu kullanın.

|Başlık  |Açıklama  |
|---------|---------|
|[Öğretici: Event Grid ve Microsoft ekipleriyle Azure Otomasyonu](ensure-tags-exists-on-new-virtual-machines.md) |Bir olay gönderen bir sanal makine oluşturun. Olay, sanal makineyi etiketleyen bir Otomasyon Runbook 'unu tetikler ve bir Microsoft ekipleri kanalına gönderilen bir ileti tetikler. |

## <a name="azure-functions"></a>Azure İşlevleri

Olaylara sunucusuz yanıt vermek için Azure Işlevlerini kullanın.

İşleyici olarak Azure İşlevleri’ni kullanırken, genel HTTP tetikleyicileri yerine Event Grid tetikleyicisini kullanın. Event Grid, Event Grid İşlevi tetikleyicilerini otomatik olarak doğrular. Genel HTTP tetikleyicileri ile [doğrulama yanıtını](security-authentication.md#webhook-event-delivery) uygulamanız gerekir.

|Başlık  |Açıklama  |
|---------|---------|
| [Hızlı başlangıç: işlev ile olayları Işleme](custom-event-to-function.md) | İşlemek için bir işleve özel bir olay gönderir. |
| [Azure Işlevleri için Event Grid tetikleyicisi](../azure-functions/functions-bindings-event-grid.md) | Işlevlerde Event Grid tetikleyiciyi kullanmaya genel bakış. |
| [Öğretici: Event Grid kullanarak karşıya yüklenen görüntüleri yeniden boyutlandırmayı otomatikleştirme](resize-images-on-storage-blob-upload-event.md) | Kullanıcılar, görüntüleri Web uygulamasına depolama hesabına yükler. Bir Depolama Blobu oluşturulduğunda, Event Grid işlev uygulamasına bir olay göndererek karşıya yüklenen görüntüyü yeniden boyutlandırır. |
| [Öğretici: veri ambarına büyük veri akışı](event-grid-event-hubs-integration.md) | Event Hubs bir yakalama dosyası oluşturduğunda, Event Grid bir işlev uygulamasına bir olay gönderir. Uygulama yakalama dosyasını alır ve verileri bir veri ambarına geçirir. |
| [Öğretici: Azure Event Grid tümleştirme örneklerine Azure Service Bus](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid, Service Bus konudan işlev uygulaması ve mantıksal uygulama 'a ileti gönderir. |

## <a name="event-hubs"></a>Event Hubs

Çözümünüz olayları işleyebileceğinden daha hızlı olay aldığında Event Hubs kullanın. Uygulamanız Event Hubs olaylarını kendi zamanlamaya göre işler. Gelen olayları işlemek için olay işlemenizi ölçeklendirebilirsiniz.

Event Hubs, bir olay kaynağı veya olay işleyicisi olarak davranabilir. Aşağıdaki makalede Event Hubs bir işleyici olarak nasıl kullanılacağı gösterilmektedir.

|Başlık  |Açıklama  |
|---------|---------|
| [Hızlı başlangıç: Azure CLı ve Event Grid ile Azure Event Hubs özel olayları yönlendirme](custom-event-to-eventhub.md) | Bir uygulama tarafından işlenmek üzere bir olay hub 'ına özel bir olay gönderir. |
| [Kaynak Yöneticisi şablonu: özel konu ve Event Hubs uç noktası](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Özel konu için abonelik oluşturan Kaynak Yöneticisi şablonu. Olayları bir Azure Event Hubs gönderir. |

Kaynak olarak Event Hubs örnekleri için bkz. [Event Hubs kaynak](event-sources.md#event-hubs).

## <a name="hybrid-connections"></a>Karma Bağlantılar

Olayları kurumsal ağ içinde olan uygulamalara göndermek için Azure Relay Karma Bağlantılar kullanın ve genel olarak erişilebilen bir uç nokta yoktur.

|Başlık  |Açıklama  |
|---------|---------|
| [Öğretici: olayları karma bağlantıya gönder](custom-event-to-hybrid-connection.md) | Bir dinleyici uygulaması tarafından işlenmek üzere var olan karma bağlantıya özel bir olay gönderir. |

## <a name="logic-apps"></a>Logic Apps

Olaylara yanıt vermek için iş süreçlerini otomatikleştirmek için Logic Apps kullanın.

|Başlık  |Açıklama  |
|---------|---------|
| [Öğretici: Azure Event Grid ve Logic Apps ile sanal makine değişikliklerini izleme](monitor-virtual-machine-changes-event-grid-logic-app.md) | Mantıksal uygulama, bir sanal makinedeki değişiklikleri izler ve bu değişiklikler hakkında e-posta gönderir. |
| [Öğretici: Logic Apps kullanarak Azure IoT Hub olaylarıyla ilgili e-posta bildirimleri gönderme](publish-iot-hub-events-to-logic-apps.md) | Logic App bir cihaz, IoT Hub 'ınıza her eklendiğinde bir bildirim e-postası gönderir. |
| [Öğretici: Azure Event Grid tümleştirme örneklerine Azure Service Bus](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid, Service Bus konudan işlev uygulaması ve mantıksal uygulama 'a ileti gönderir. |

## <a name="service-bus"></a>Service Bus

### <a name="service-bus-queues"></a>Service Bus kuyrukları

Event Grid olayları, ara belleğe almak veya komut & Denetim senaryolarında kurumsal uygulamalarda kullanmak üzere Service Bus sıralara doğrudan yönlendirebilirsiniz.

Azure portal, bir olay aboneliği oluştururken, uç nokta türü olarak "Service Bus kuyruğu" öğesini seçin ve ardından bir Service Bus kuyruğu seçmek için "Seç ve uç nokta" a tıklayın.

#### <a name="using-cli-to-add-a-service-bus-queue-handler"></a>Service Bus kuyruğu işleyicisi eklemek için CLı kullanma

Azure CLı için aşağıdaki örnek abone olur ve bir olay Kılavuzu konusunu Service Bus kuyruğuna bağlar:

```azurecli-interactive
# If you haven't already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid event-subscription create \
    --name <my-event-subscription> \
    --source-resource-id /subscriptions/{SubID}/resourceGroups/{RG}/providers/Microsoft.EventGrid/topics/topic1 \
    --endpoint-type servicebusqueue \
    --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.ServiceBus/namespaces/ns1/queues/queue1
```

### <a name="service-bus-topics"></a>Hizmet Veri Yolu konuları

Service Bus konular ile Azure sistem olaylarını işlemek veya komut & Denetim mesajlaşma senaryoları için Event Grid olayları doğrudan Service Bus konularına yönlendirebilirsiniz.

Azure portal, bir olay aboneliği oluştururken, uç nokta türü olarak "Service Bus konu" öğesini seçin ve ardından bir Service Bus konu başlığı seçmek için "Seç ve uç nokta" seçeneğine tıklayın.

#### <a name="using-cli-to-add-a-service-bus-topic-handler"></a>Service Bus konu işleyicisi eklemek için CLı kullanma

Azure CLı için aşağıdaki örnek abone olur ve bir olay Kılavuzu konusunu Service Bus kuyruğuna bağlar:

```azurecli-interactive
# If you haven't already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid event-subscription create \
    --name <my-event-subscription> \
    --source-resource-id /subscriptions/{SubID}/resourceGroups/{RG}/providers/Microsoft.EventGrid/topics/topic1 \
    --endpoint-type servicebustopic \
    --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.ServiceBus/namespaces/ns1/topics/topic1
```

## <a name="queue-storage"></a>Kuyruk Depolama

Çekilmesi gereken olayları almak için kuyruk depolama alanını kullanın. Yanıt vermeyi çok uzun süren uzun süre çalışan bir işleminiz varsa kuyruk depolama alanını kullanabilirsiniz. Uygulama kuyruk depolamaya olay göndererek olayları kendi zamanlamaya göre çekebilir ve işleyebilir.

|Başlık  |Açıklama  |
|---------|---------|
| [Hızlı başlangıç: Azure CLı ve Event Grid ile Azure kuyruk depolama 'ya özel olayları yönlendirme](custom-event-to-queue-storage.md) | Bir kuyruk depolamasına özel olayların nasıl gönderileceğini açıklar. |

## <a name="webhooks"></a>WebHooks

Olaylara yanıt veren özelleştirilebilir uç noktalar için Web kancalarını kullanın.

|Başlık  |Açıklama  |
|---------|---------|
| Hızlı başlangıç: [Azure CLI](custom-event-quickstart.md), [PowerShell](custom-event-quickstart-powershell.md)ve [Portal](custom-event-quickstart-portal.md)ile özel olaylar oluşturun ve yönlendirin. | Web kancasına nasıl özel olaylar gönderileceğini gösterir. |
| Hızlı başlangıç: Blob Depolama olaylarını- [Azure CLI](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json), [PowerShell](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json)ve [Portal](blob-event-quickstart-portal.md)ile özel bir Web uç noktasına yönlendirin. | Blob Storage olaylarının bir Web kancasına nasıl gönderileceğini gösterir. |
| [Hızlı başlangıç: kapsayıcı kayıt defteri olayları gönderme](../container-registry/container-registry-event-grid-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Container Registry olaylarını göndermek için Azure CLı 'nın nasıl kullanılacağını gösterir. |
| [Genel Bakış: HTTP uç noktasına olay alma](receive-events.md) | Bir olay aboneliğinden olayları almak ve olayları almak ve seri durumdan çıkarmak için bir HTTP uç noktasının nasıl doğrulanacağını açıklar. |

## <a name="next-steps"></a>Sonraki adımlar

* Event Grid’e giriş için bkz. [Event Grid hakkında](overview.md).
* Event Grid ile hızla çalışmaya başlamak için bkz: [Azure Event Grid ile özel olaylar oluşturma ve yönlendirme](custom-event-quickstart.md).
