---
title: CloudEvents şemasında olaylarla Azure Event Grid kullanma
description: Azure Event Grid olaylar için CloudEvents şemasının nasıl ayarlanacağını açıklar.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 11/07/2018
ms.author: babanisa
ms.openlocfilehash: 8925110511f6c63a42dd9b121429ac7264cd4aa4
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74170239"
---
# <a name="use-cloudevents-v10-schema-with-event-grid"></a>Event Grid ile CloudEvents v 1.0 şeması kullanma

Azure Event Grid, [varsayılan olay şemasına](event-schema.md)ek olarak, [cloudevents v 1.0](https://github.com/cloudevents/spec/blob/v1.0/json-format.md) ve [http protokol bağlamasının](https://github.com/cloudevents/spec/blob/v1.0/http-protocol-binding.md)JSON uygulamasındaki olayları yerel olarak destekler. [Cloudevents](https://cloudevents.io/) , olay verilerini tanımlamaya yönelik [açık bir belirtimdir](https://github.com/cloudevents/spec/blob/v1.0/spec.md) .

CloudEvents, yayımlama için ortak bir olay şeması sağlayarak ve bulut tabanlı olayları tüketerek birlikte çalışabilirliği basitleştirir. Bu şema, tek düzen araçları, & işleme olaylarını yönlendirme için standart yolları ve dış olay şemasının serisini kaldırma evrensel yollarını sağlar. Ortak bir şema ile, iş platformları arasında daha kolay bir şekilde tümleşebilir.

CloudEvents, [bulut Yerel Bilgi Işlem altyapısı](https://www.cncf.io/)aracılığıyla Microsoft gibi birkaç [ortak](https://github.com/cloudevents/spec/blob/master/community/contributors.md)şekilde oluşturulmuştur. Şu anda sürüm 1,0 olarak sunulmaktadır.

Bu makalede, Event Grid ile CloudEvents şemasının nasıl kullanılacağı açıklanır.

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

## <a name="install-preview-feature"></a>Önizleme özelliğini yükle

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

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

Kullanılabilir alanlar, bunların türleri ve CloudEvents v 0.1 içindeki tanımlarda ayrıntılı bir açıklama [burada bulunabilir](https://github.com/cloudevents/spec/blob/v1.0/spec.md#required-attributes).

CloudEvents şeması ve Event Grid şeması 'nda teslim edilen olaylar için üst bilgi değerleri, `content-type`hariç aynıdır. CloudEvents şeması için, bu üst bilgi değeri `"content-type":"application/cloudevents+json; charset=utf-8"`. Event Grid şeması için, bu üst bilgi değeri `"content-type":"application/json; charset=utf-8"`.

## <a name="configure-event-grid-for-cloudevents"></a>CloudEvents için Event Grid yapılandırma

CloudEvents şemasında olay girişi ve çıktısı için Event Grid kullanabilirsiniz. BLOB depolama olayları ve IoT Hub olayları ve özel olaylar gibi sistem olayları için CloudEvents kullanabilirsiniz. Ayrıca, bu olayları tel ve ileri doğru şekilde dönüştürebilir.


| Giriş şeması       | Çıktı şeması
|--------------------|---------------------
| CloudEvents biçimi | CloudEvents biçimi
| Event Grid biçimi  | CloudEvents biçimi
| CloudEvents biçimi | Event Grid biçimi
| Event Grid biçimi  | Event Grid biçimi

Tüm olay şemaları için Event Grid, bir olay kılavuzu konusuna yayımlarken ve olay aboneliği oluştururken doğrulama gerektirir. Daha fazla bilgi için [Event Grid güvenliğini ve kimlik doğrulaması](security-authentication.md).

### <a name="input-schema"></a>Giriş şeması

Özel konu başlığı oluştururken özel konu için giriş şemasını ayarlarsınız.

Azure CLI için şunu kullanın:

```azurecli-interactive
# If you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid topic create \
  --name <topic_name> \
  -l westcentralus \
  -g gridResourceGroup \
  --input-schema cloudeventv01schema
```

PowerShell için şunu kullanın:

```azurepowershell-interactive
# If you have not already installed the module, do it now.
# This module is required for preview features.
Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery

New-AzureRmEventGridTopic `
  -ResourceGroupName gridResourceGroup `
  -Location westcentralus `
  -Name <topic_name> `
  -InputSchema CloudEventV01Schema
```

CloudEvents 'in geçerli sürümü olayların toplu işlemeyi desteklemez. CloudEvent şeması ile olayları bir konuya yayımlamak için, her olayı ayrı ayrı yayımlayın.

### <a name="output-schema"></a>Çıktı şeması

Olay aboneliğini oluştururken çıktı şemasını ayarlarsınız.

Azure CLI için şunu kullanın:

```azurecli-interactive
topicID=$(az eventgrid topic show --name <topic-name> -g gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --name <event_subscription_name> \
  --source-resource-id $topicID \
  --endpoint <endpoint_URL> \
  --event-delivery-schema cloudeventv01schema
```

PowerShell için şunu kullanın:
```azurepowershell-interactive
$topicid = (Get-AzureRmEventGridTopic -ResourceGroupName gridResourceGroup -Name <topic-name>).Id

New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -DeliverySchema CloudEventV01Schema
```

 Şu anda, olay CloudEvents şemasında teslim edildiğinde Azure Işlevleri uygulaması için Event Grid tetikleyicisi kullanamazsınız. HTTP tetikleyicisi kullanın. CloudEvents şemasında olayları alan bir HTTP tetikleyicisi uygulama örnekleri için bkz. [http tetikleyicisini Event Grid tetikleyicisi olarak kullanma](../azure-functions/functions-bindings-event-grid.md#use-an-http-trigger-as-an-event-grid-trigger).

## <a name="next-steps"></a>Sonraki adımlar

* Olay teslimatlarını izleme hakkında bilgi için bkz. [izleyici Event Grid ileti teslimi](monitor-event-delivery.md).
* CloudEvents üzerinde test, yorum ve [katkıda bulunmanızı](https://github.com/cloudevents/spec/blob/master/CONTRIBUTING.md) öneririz.
* Azure Event Grid aboneliği oluşturma hakkında daha fazla bilgi için bkz. [Event Grid abonelik şeması](subscription-creation-schema.md).
