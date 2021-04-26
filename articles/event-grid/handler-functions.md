---
title: Azure 'da Azure Event Grid olayları için olay işleyicisi olarak bir işlev kullanma
description: Azure Işlevleri tarafından Event Grid olayları için olay işleyicileri olarak oluşturulan ve barındırılan işlevleri nasıl kullanabileceğinizi açıklar.
ms.topic: conceptual
ms.date: 03/15/2021
ms.openlocfilehash: f547b09fe7e62eb3fa9e02bd17298a936350f871
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103496550"
---
# <a name="use-a-function-as-an-event-handler-for-event-grid-events"></a>Event Grid olaylar için bir işlevi olay işleyicisi olarak kullanma

Olay işleyicisi, olayın gönderildiği yerdir. İşleyici, olayı işlemek için bir eylem gerçekleştirir. Birkaç Azure hizmeti, olayları işleyecek şekilde otomatik olarak yapılandırılır ve **Azure işlevleri** bunlardan biridir. 


Olaylar için bir işleyici olarak Azure 'da bir işlevi kullanmak için şu yaklaşımlardan birini izleyin: 

-   [Event Grid tetikleyicisi](../azure-functions/functions-bindings-event-grid-trigger.md)kullanın.  **Uç nokta türü** olarak **Azure işlevini** belirtin. Ardından, işlev uygulamasını ve olayları işleyecek işlevi belirtin. 
-   [Http tetikleyicisini](../azure-functions/functions-bindings-http-webhook.md)kullanın.  **Uç nokta türü** olarak **Web kancası** belirtin. Ardından, olayları işleyecek işlevin URL 'sini belirtin. 

İkinci yaklaşımda aşağıdaki avantajları sunan ilk yaklaşımı (Event Grid tetikleyicisi) kullanmanızı öneririz:
-   Event Grid Event Grid Tetikleyicileri otomatik olarak doğrular. Genel HTTP Tetikleyicileri ile [doğrulama yanıtını](webhook-event-delivery.md) kendiniz uygulamanız gerekir.
-   Event Grid, işlevin olayları işleyebileceği algılanan ücret temelinde, olayların bir Event Grid olayı tarafından tetiklenen bir işleve teslim edileceği oranı otomatik olarak ayarlar. Bu oran, işlevin olay işleme hızı zamana göre farklılık gösterebileceğinden, bir işlevin olaylarını işlemek için bir işlevin değil, özellik averts dağıtım hatalarıyla eşleşir. Yüksek performans verimliliği artırmak için olay aboneliğinde toplu işlemeyi etkinleştirin. Daha fazla bilgi için bkz. [toplu Işlemeyi etkinleştirme](#enable-batching).

    > [!NOTE]
    > Şu anda, olay **Cloudevents** şemasında teslim edildiğinde bir işlev uygulaması için Event Grid tetikleyicisi kullanamazsınız. Bunun yerine, bir HTTP tetikleyicisi kullanın.

## <a name="tutorials"></a>Öğreticiler

|Başlık  |Açıklama  |
|---------|---------|
| [Hızlı başlangıç: işlev ile olayları Işleme](custom-event-to-function.md) | İşlemek için bir işleve özel bir olay gönderir. |
| [Öğretici: Event Grid kullanarak karşıya yüklenen görüntüleri yeniden boyutlandırmayı otomatikleştirme](resize-images-on-storage-blob-upload-event.md) | Kullanıcılar, görüntüleri Web uygulamasına depolama hesabına yükler. Bir Depolama Blobu oluşturulduğunda, Event Grid işlev uygulamasına bir olay göndererek karşıya yüklenen görüntüyü yeniden boyutlandırır. |
| [Öğretici: veri ambarına büyük veri akışı](event-grid-event-hubs-integration.md) | Event Hubs bir yakalama dosyası oluşturduğunda, Event Grid bir işlev uygulamasına bir olay gönderir. Uygulama yakalama dosyasını alır ve verileri bir veri ambarına geçirir. |
| [Öğretici: Azure Event Grid tümleştirme örneklerine Azure Service Bus](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid, Service Bus konu başlığından bir işlev uygulamasına ve Logic App 'e ileti gönderir. |

## <a name="rest-example-for-put"></a>REST örneği (PUT için)

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "AzureFunction",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Web/sites/<FUNCTION APP NAME>/functions/<FUNCTION NAME>",
                "maxEventsPerBatch": 10,
                "preferredBatchSizeInKilobytes": 6400
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

## <a name="enable-batching"></a>Toplu işlemeyi etkinleştir
Daha yüksek bir verimlilik için abonelikte toplu işlemeyi etkinleştirin. Azure portal kullanıyorsanız, toplu iş başına en fazla olay ve cihazının saniyede bayt olarak tercih edilen toplu iş boyutunu bir abonelik oluşturma sırasında veya oluşturma işleminden sonra ayarlayabilirsiniz. 

Batch ayarlarını Azure portal, PowerShell, CLı veya Kaynak Yöneticisi şablonunu kullanarak yapılandırabilirsiniz. 

### <a name="azure-portal"></a>Azure portalı
Kullanıcı arabiriminde abonelik oluşturma sırasında, **olay aboneliği oluştur** sayfasında, **Gelişmiş Özellikler** sekmesine geçin ve **toplu iş başına en fazla olay** ve **tercih edilen toplu iş boyutu için değerleri kilobayt cinsinden** ayarlayın. 
    
:::image type="content" source="./media/custom-event-to-function/enable-batching.png" alt-text="Abonelik oluşturma sırasında toplu işlemeyi etkinleştir":::

Bu değerleri, **Event Grid konu** sayfasının **Özellikler** sekmesinde varolan bir abonelik için güncelleştirebilirsiniz. 

:::image type="content" source="./media/custom-event-to-function/features-batch-settings.png" alt-text="Oluşturulduktan sonra toplu işlemeyi etkinleştir":::

### <a name="azure-resource-manager-template"></a>Azure Resource Manager şablonu
Azure Resource Manager şablonunda **Maxeventsperbatch** ve **Preferredbatchsizeınkilobayt** ayarlayabilirsiniz. Daha fazla bilgi için bkz. [Microsoft. EventGrid Eventabonelikler şablon başvurusu](/azure/templates/microsoft.eventgrid/eventsubscriptions).

### <a name="azure-cli"></a>Azure CLI’si
Aşağıdaki parametreleri kullanarak Batch ile ilgili ayarları yapılandırmak için [az eventgrid Event-Subscription Create](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_create&preserve-view=true) veya [az eventgrid Event-Subscription Update](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_update&preserve-view=true) komutunu kullanabilirsiniz: `--max-events-per-batch` veya `--preferred-batch-size-in-kilobytes` .

### <a name="azure-powershell"></a>Azure PowerShell
Aşağıdaki parametreleri kullanarak Batch ile ilgili ayarları yapılandırmak için [New-azeventgridsubscription](/powershell/module/az.eventgrid/new-azeventgridsubscription) veya [Update-azeventgridsubscription](/powershell/module/az.eventgrid/update-azeventgridsubscription) cmdlet 'ini kullanabilirsiniz: `-MaxEventsPerBatch` veya `-PreferredBatchSizeInKiloBytes` .

> [!NOTE]
> Event Grid tetikleyicisi kullandığınızda, Event Grid hizmeti hedef Azure işlevi için istemci gizli anahtarını getirir ve Azure işlevine olayları göndermek için onu kullanır. Azure işlevinizi Azure Active Directory bir uygulamayla koruduğunuzda, genel Web kancası yaklaşımını alıp HTTP tetikleyicisini kullanmanız gerekir.

## <a name="next-steps"></a>Sonraki adımlar
Desteklenen olay işleyicilerinin bir listesi için bkz. [olay işleyicileri](event-handlers.md) makalesi.
