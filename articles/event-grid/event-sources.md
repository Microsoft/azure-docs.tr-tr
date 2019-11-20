---
title: Olay kaynaklarını Azure Event Grid
description: Azure Event Grid için desteklenen olay kaynaklarını açıklar
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: spelluru
ms.openlocfilehash: 8c9258f099587ba4dbdfe3a7e46f72cd7a825024
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73603830"
---
# <a name="event-sources-in-azure-event-grid"></a>Azure Event Grid içindeki olay kaynakları

Olay kaynağı, olayın gerçekleştiği yerdir. Çeşitli Azure Hizmetleri, olayları gönderecek şekilde otomatik olarak yapılandırılır. Ayrıca, olay gönderen özel uygulamalar da oluşturabilirsiniz. Özel uygulamaların olay dağıtımı için Event Grid kullanabilmesi için Azure 'da barındırılması gerekmez.

Bu makale, her olay kaynağı için içerik bağlantıları sağlar.

## <a name="azure-subscriptions"></a>Azure abonelikleri

Azure aboneliği genelinde kaynaklardaki değişikliklere yanıt vermek için Azure abonelikleri olaylarına abone olun.

|Başlık |Açıklama  |
|---------|---------|
| [Öğretici: Event Grid ve Microsoft ekipleriyle Azure Otomasyonu](ensure-tags-exists-on-new-virtual-machines.md) |Bir olay gönderen bir sanal makine oluşturun. Olay, sanal makineyi etiketleyen bir Otomasyon Runbook 'unu tetikler ve bir Microsoft ekipleri kanalına gönderilen bir ileti tetikler. |
| [Nasıl yapılır: Portal üzerinden olaylara abone olma](subscribe-through-portal.md) | Bir Azure aboneliği için olaylara abone olmak üzere portalı kullanın. |
| [Azure CLı: Azure aboneliği için olaylara abone olma](./scripts/event-grid-cli-azure-subscription.md) |Bir Azure aboneliğine Event Grid aboneliği oluşturan ve bir Web kancasına olay gönderen örnek komut dosyası. |
| [PowerShell: bir Azure aboneliği için olaylara abone olma](./scripts/event-grid-powershell-azure-subscription.md)| Bir Azure aboneliğine Event Grid aboneliği oluşturan ve bir Web kancasına olay gönderen örnek komut dosyası. |
| [Olay şeması](event-schema-subscriptions.md) | Azure abonelik olaylarında alanları gösterir. |

## <a name="container-registry"></a>Container Kayıt Defteri

Görüntülerdeki değişikliklere yanıt vermek için Container Registry olaylarına abone olun.

|Başlık |Açıklama  |
|---------|---------|
| [Hızlı başlangıç: kapsayıcı kayıt defteri olayları gönderme](../container-registry/container-registry-event-grid-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Container Registry olaylarını göndermek için Azure CLı 'nın nasıl kullanılacağını gösterir. |
| [Olay şeması](event-schema-container-registry.md) | Container Registry olaylardaki alanları gösterir. |

## <a name="custom-topics"></a>Özel Konular

Uygulama olaylarına yanıt vermek için özel konulara abone olun.

|Başlık  |Açıklama  |
|---------|---------|
| [Hızlı başlangıç: Azure CLı ile özel olaylar oluşturma ve yönlendirme](custom-event-quickstart.md) | Özel olayları göndermek için Azure CLı 'nın nasıl kullanılacağını gösterir. |
| [Hızlı başlangıç: Azure PowerShell özel olaylar oluşturma ve yönlendirme](custom-event-quickstart-powershell.md) | Özel olayları göndermek için Azure PowerShell nasıl kullanacağınızı gösterir. |
| [Hızlı başlangıç: Azure portal özel olaylar oluşturma ve yönlendirme](custom-event-quickstart-portal.md) | Portalın özel olayları göndermek için nasıl kullanılacağını gösterir. |
| [Hızlı başlangıç: özel olayları Azure kuyruk depolama 'ya yönlendirme](custom-event-to-queue-storage.md) | Bir kuyruk depolamasına özel olayların nasıl gönderileceğini açıklar. |
| [Nasıl yapılır: özel konuya gönderi](post-to-custom-topic.md) | Bir olayın özel bir konuya nasıl nakledileceğini gösterir. |
| [Azure CLı: Event Grid özel konu oluştur](./scripts/event-grid-cli-create-custom-topic.md)|Özel bir konu oluşturan örnek komut dosyası. Betik, uç noktayı ve bir anahtarı alır.|
| [Azure CLı: özel konu için olaylara abone olma](./scripts/event-grid-cli-subscribe-custom-topic.md)|Özel konu için abonelik oluşturan örnek komut dosyası. Olayları bir Web kancasına gönderir.|
| [PowerShell: Event Grid özel konu oluştur](./scripts/event-grid-powershell-create-custom-topic.md)|Özel bir konu oluşturan örnek komut dosyası. Betik, uç noktayı ve bir anahtarı alır.|
| [PowerShell: özel konu için olaylara abone olma](./scripts/event-grid-powershell-subscribe-custom-topic.md)|Özel konu için abonelik oluşturan örnek komut dosyası. Olayları bir Web kancasına gönderir.|
| [Kaynak Yöneticisi şablonu: özel konu ve Web kancası uç noktası](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid) | Bu özel konu için özel konu ve abonelik oluşturan Kaynak Yöneticisi şablonu. Olayları bir Web kancasına gönderir. |
|
| [Kaynak Yöneticisi şablonu: özel konu ve Event Hubs uç noktası](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Özel konu için abonelik oluşturan Kaynak Yöneticisi şablonu. Olayları bir Azure Event Hubs gönderir. |
| [Olay şeması](event-schema.md) | Özel olaylardaki alanları gösterir. |

## <a name="event-hubs"></a>Event Hubs

Yakalama dosyası olaylarına yanıt vermek için Event Hubs olaylarına abone olun. Event Hubs, bir olay kaynağı veya olay işleyicisi olarak davranabilir. Aşağıdaki makalelerde Event Hubs kaynak olarak nasıl kullanılacağı gösterilmektedir.

|Başlık  |Açıklama  |
|---------|---------|
| [Öğretici: veri ambarına büyük veri akışı](event-grid-event-hubs-integration.md) | Event Hubs bir yakalama dosyası oluşturduğunda, Event Grid bir işlev uygulamasına bir olay gönderir. Uygulama yakalama dosyasını alır ve verileri bir veri ambarına geçirir. |
| [Olay şeması](event-schema-event-hubs.md) | Event Hubs olaylardaki alanları gösterir. |

İşleyici olarak Event Hubs örnekleri için bkz. [Event Hubs Handler](event-handlers.md#event-hubs).

## <a name="iot-hub"></a>IoT Hub’ı

Oluşturulan, silinen, bağlı, bağlantısı kesilen ve telemetri olaylarına yanıt vermek için IoT Hub olaylarına abone olun.

|Başlık  |Açıklama  |
|---------|---------|
| [Logic Apps kullanarak Azure IoT Hub olaylarıyla ilgili e-posta bildirimleri gönderin](publish-iot-hub-events-to-logic-apps.md) | Mantıksal uygulama, IoT Hub bir cihaz her eklendiğinde bir bildirim e-postası gönderir. |
| [Eylemleri tetiklemek için Event Grid kullanarak IoT Hub olaylara tepki verme](../iot-hub/iot-hub-event-grid.md) | IoT Hub Event Grid tümleştirilmesine genel bakış. |
| [Olay şeması](event-schema-iot-hub.md) | IoT Hub olaylardaki alanları gösterir. |
| [Cihaza bağlı ve cihaz bağlantısı kesilen olayları Sırala](../iot-hub/iot-hub-how-to-order-connection-state-events.md) | Cihaz bağlantısı durum olaylarının nasıl sipariş alınacağını gösterir. |

## <a name="key-vault-preview"></a>Key Vault (Önizleme)

Event Grid ile tümleştirme Key Vault Şu anda önizleme aşamasındadır. 

Bir parolanın süresi dolduğunda, gizli dizi süresinin dolması veya gizli dizinin kullanılabilir yeni bir sürümü var olduğunda bildirim almak için Key Vault olaylarına abone olun. 

|Başlık  |Açıklama  |
|---------|---------|
| [Azure Event Grid ile Key Vault olaylarını izleme](../key-vault/event-grid-overview.md) | Key Vault Event Grid tümleştirilmesine genel bakış. |
| [Öğretici: Event Grid ile Key Vault olayları oluşturma ve izleme](../key-vault/event-grid-tutorial.md) | Key Vault için Event Grid bildirimleri ayarlamayı öğrenin. |
| [Olay şeması](event-schema-key-vault.md) | Key Vault olaylardaki alanları gösterir. |

## <a name="media-services"></a>Media Services

İş durumu olaylarına yanıt vermek için Media Services olaylara abone olun.

|Başlık  |Açıklama  |
|---------|---------|
| [Genel Bakış: Media Services olaylara yeniden işlem](../media-services/latest/reacting-to-media-services-events.md) | Media Services Event Grid tümleştirilmesine genel bakış. |
| [Öğretici: CLı kullanarak özel bir Web uç noktasına Azure Media Services olaylarını yönlendirme](../media-services/latest/job-state-events-cli-how-to.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Media Services olaylarının nasıl gönderileceğini gösterir. |
| [Olay şeması](../media-services/latest/media-services-event-schemas.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Media Services olaylardaki alanları gösterir. |

## <a name="resource-groups"></a>Kaynak grupları

Kaynak grubu üzerinde kaynaklardaki değişikliklere yanıt vermek için kaynak grubu olaylarına abone olun.

|Başlık  |Açıklama  |
|---------|---------|
| [Öğretici: Azure Event Grid ve Logic Apps ile sanal makine değişikliklerini izleme](monitor-virtual-machine-changes-event-grid-logic-app.md) | Mantıksal uygulama, bir sanal makinedeki değişiklikleri izler ve bu değişiklikler hakkında e-posta gönderir. |
| [Azure CLı: bir kaynak grubu için olaylara abone olma](./scripts/event-grid-cli-resource-group.md)| Bir kaynak grubu için olaylara abone olan örnek komut dosyası. Olayları bir Web kancasına gönderir. |
| [Azure CLı: bir kaynak grubu için olaylara abone olma ve kaynak için filtre uygulama](./scripts/event-grid-cli-resource-group-filter.md) | Bir kaynak grubu için olaylara abone olan ve bir kaynak için olayları filtreleyen örnek komut dosyası. |
| [PowerShell: bir kaynak grubu için olaylara abone olma](./scripts/event-grid-powershell-resource-group.md) | Bir kaynak grubu için olaylara abone olan örnek komut dosyası. Olayları bir Web kancasına gönderir. |
| [PowerShell: bir kaynak grubu için olaylara abone olma ve kaynak için filtre uygulama](./scripts/event-grid-powershell-resource-group-filter.md) | Bir kaynak grubu için olaylara abone olan ve bir kaynak için olayları filtreleyen örnek komut dosyası. |
| [Kaynak Yöneticisi şablonu: kaynak aboneliği](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-resource-events-to-webhook) | Bir Azure aboneliği veya kaynak grubu için olaylara abone olur. Olayları bir Web kancasına gönderir. |
| [Olay Şeması](event-schema-resource-groups.md) | Kaynak grubu olaylardaki alanları gösterir. |

## <a name="service-bus"></a>Service Bus

Etkin bir dinleyici olmadan iletilere yanıt vermek için Service Bus olaylarına abone olun.

|Başlık  |Açıklama  |
|---------|---------|
| [Öğretici: Azure Event Grid tümleştirme örneklerine Azure Service Bus](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid, Service Bus konudan işlev uygulaması ve mantıksal uygulama 'a ileti gönderir. |
| [Genel Bakış: Event Grid tümleştirmeye Azure Service Bus](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md) | Service Bus Event Grid tümleştirilmesine genel bakış. |
| [Olay şeması](event-schema-service-bus.md) | Service Bus olaylardaki alanları gösterir. |

## <a name="storage"></a>Depolama

Oluşturulan blob ve silinen olaylara yanıt vermek için BLOB depolama olaylarına abone olun.

|Başlık  |Açıklama  |
|---------|---------|
| [Hızlı başlangıç: Blob Depolama olaylarını Azure CLı ile özel bir Web uç noktasına yönlendirme](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Blob Depolama olaylarını bir Web kancasına göndermek için Azure CLı 'nın nasıl kullanılacağını gösterir. |
| [Hızlı başlangıç: Blob Depolama olaylarını PowerShell ile özel bir Web uç noktasına yönlendirme](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Blob Depolama olaylarını bir Web kancasına göndermek için Azure PowerShell nasıl kullanacağınızı gösterir. |
| [Hızlı başlangıç: Azure portal Blob Depolama olaylarını oluşturun ve yönlendirin](blob-event-quickstart-portal.md) | Portal 'ın Blob Depolama olaylarını bir Web kancasına göndermek için nasıl kullanılacağını gösterir. |
| [Azure CLı: bir BLOB depolama hesabı için olaylara abone olma](./scripts/event-grid-cli-blob.md) | BLOB depolama hesabı için olaya abone olan örnek komut dosyası. Olayı bir Web kancasına gönderir. |
| [PowerShell: bir BLOB depolama hesabı için olaylara abone olma](./scripts/event-grid-powershell-blob.md) | BLOB depolama hesabı için olaya abone olan örnek komut dosyası. Olayı bir Web kancasına gönderir. |
| [Kaynak Yöneticisi şablonu: BLOB depolama ve abonelik oluşturma](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-subscription-and-storage) | Bir Azure Blob depolama hesabı dağıtır ve o depolama hesabı için olaylara abone olur. Olayları bir Web kancasına gönderir. |
| [Genel Bakış: BLOB depolama olaylarına yeniden davranıyor](../storage/blobs/storage-blob-event-overview.md) | Blob depolamayı Event Grid tümleştirmeyle ilgili genel bakış. |
| [Olay şeması](event-schema-blob-storage.md) | Blob Storage olaylardaki alanları gösterir. |

## <a name="maps"></a>Haritalar
Bölge olaylarına yanıt vermek için Azure Maps olaylarına abone olun. Örneğin, bir uygulama, bir cihaz bölge alanı girdiğinde veya her çıktığında bir e-posta bildirimi sunabilir.

|Başlık  |Açıklama  |
|---------|---------|
| [Event Grid kullanarak Azure Maps olaylarına tepki verme](../azure-maps/azure-maps-event-grid-integration.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid ile Azure haritalarını tümleştirmeyle ilgili genel bakış. |
| [Öğretici: bölge kümesi ayarlama](../azure-maps/tutorial-geofence.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Bu öğretici, Azure haritalar 'ı kullanarak bölge sınırlaması ayarlama konusunda size kılavuzluk eder. Bölge sonuçlarının akışını yapmak ve bölge sonuçlarına dayalı bir bildirim ayarlamak için Azure Event Grid kullanırsınız. |
| [Olay şeması](event-schema-azure-maps.md) | Azure haritalar olaylardaki alanları gösterir. |

## <a name="app-configuration"></a>Uygulama Yapılandırması
Anahtar-değer değiştirme olaylarına yanıt vermek için Azure uygulama yapılandırma olaylarına abone olun.

|Başlık | Açıklama |
|---------|---------|
| [Event Grid kullanarak Azure uygulama yapılandırma olaylarına tepki verme](../azure-app-configuration/concept-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid ile Azure uygulama yapılandırmasını tümleştirmeyle ilgili genel bakış. |
| [Hızlı başlangıç: Azure CLı ile Azure uygulama yapılandırma olaylarını özel bir Web uç noktasına yönlendirme](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Azure CLı kullanarak Azure uygulama yapılandırma olaylarını bir Web kancasına göndermek için nasıl kullanacağınızı gösterir. |
| [Olay şeması](event-schema-app-configuration.md) | Azure uygulama yapılandırma olaylarında alanları gösterir. |

## <a name="azure-signalr"></a>Azure SignalR
İstemci bağlantı olaylarına yanıt vermek için Azure SignalR hizmet olaylarına abone olun.

|Başlık | Açıklama |
|---------|---------|
| [Event Grid kullanarak Azure SignalR hizmeti olaylarına tepki verme](../azure-signalr/signalr-concept-event-grid-integration.md) | Event Grid ile Azure SignalR hizmeti tümleştirilmesine genel bakış. |
| [Event Grid için Azure SignalR hizmeti olaylarını gönderme](../azure-signalr/signalr-howto-event-grid-integration.md) | Event Grid aracılığıyla Azure SignalR hizmeti olaylarının bir uygulamaya nasıl gönderileceğini gösterir. |
| [Olay şeması](event-schema-azure-signalr.md) | Azure SignalR hizmeti olaylarında alanları gösterir. |

## <a name="azure-machine-learning"></a>Azure Machine Learning

Model kaydına yanıt vermek için Azure Machine Learning çalışma alanı olaylarına abone olun.

| Başlık | Açıklama |
| ----- | ----- |
| [Azure Machine Learning olaylarını tüketme](../machine-learning/service/concept-event-grid-integration.md) | Azure Machine Learning Event Grid tümleştirilmesine genel bakış. |
| [Azure Machine Learning için Azure Event Grid olay şeması](event-schema-machine-learning.md) | Azure Machine Learning olaylardaki alanları gösterir. |

## <a name="next-steps"></a>Sonraki adımlar

* Event Grid’e giriş için bkz. [Event Grid hakkında](overview.md).
* Event Grid kullanmaya hızlıca başlamak için bkz. [özel olayları oluşturma ve Azure Event Grid ile yönlendirme](custom-event-quickstart.md).
