---
title: Özel alanı Azure Event Grid şemasına eşleyin
description: Bu makalede, olay veriniz Event Grid şemayla eşleşmediği zaman özel şemanızın Azure Event Grid şemasına nasıl dönüştürüleceği açıklanır.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 34381782c9337631b0aa04e47eb5897a8071139a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97109207"
---
# <a name="map-custom-fields-to-event-grid-schema"></a>Özel alanları Event Grid şemasına eşleme

Olay verileriniz beklenen [Event Grid şemasıyla](event-schema.md)eşleşmiyorsa, etkinliği abonelere yönlendirmek için Event Grid kullanmaya devam edebilirsiniz. Bu makalede, şemanızın Event Grid şemasına nasıl eşleneceğini açıklanmaktadır.

## <a name="original-event-schema"></a>Özgün olay şeması

Aşağıdaki biçimde olayları gönderen bir uygulamanız olduğunu varsayalım:

```json
[
  {
    "myEventTypeField":"Created",
    "resource":"Users/example/Messages/1000",
    "resourceData":{"someDataField1":"SomeDataFieldValue"}
  }
]
```

Bu biçim gerekli şemayla eşleşmez, ancak Event Grid alanları şemayla eşlemenizi sağlar. Ya da özgün şemadaki değerleri alabilirsiniz.

## <a name="create-custom-topic-with-mapped-fields"></a>Eşlenmiş alanlarla özel konu oluşturma

Özel bir konu oluştururken, özgün olayınızdan bulunan alanları Event Grid şemasına nasıl eşleneceğini belirtin. Eşlemeyi özelleştirmek için kullandığınız üç değer vardır:

* **Giriş şeması** değeri şemanın türünü belirtir. Kullanılabilir seçenekler CloudEvents şeması, özel olay şeması veya Event Grid şemadır. Varsayılan değer Event Grid şemadır. Şemanız ve olay Kılavuzu şeması arasında özel eşleme oluştururken özel olay şeması kullanın. Olaylar CloudEvents biçiminde olduğunda, CloudEvents şeması ' nı kullanın.

* **Mapping default Values** özelliği Event Grid şemasındaki alanlar için varsayılan değerleri belirtir. , Ve için varsayılan değerleri ayarlayabilirsiniz `subject` `eventtype` `dataversion` . Genellikle bu parametreyi, özel şemanız bu üç alandan birine karşılık gelen bir alan içermiyorsa kullanırsınız. Örneğin, veri sürümünün her zaman **1,0** olarak ayarlandığını belirtebilirsiniz.

* **Mapping Fields** değeri, şemadaki alanları Event Grid şemasına eşler. Değerleri boşlukla ayrılmış anahtar/değer çiftlerinde belirtirsiniz. Anahtar adı için Event Grid alanının adını kullanın. Değer için, alanın adını kullanın. ,,,, Ve için anahtar adlarını kullanabilirsiniz `id` `topic` `eventtime` `subject` `eventtype` `dataversion` .

Azure CLı ile özel bir konu oluşturmak için şunu kullanın:

```azurecli-interactive
az eventgrid topic create \
  -n demotopic \
  -l eastus2 \
  -g myResourceGroup \
  --input-schema customeventschema \
  --input-mapping-fields eventType=myEventTypeField \
  --input-mapping-default-values subject=DefaultSubject dataVersion=1.0
```

PowerShell için şunu kullanın:

```azurepowershell-interactive
New-AzEventGridTopic `
  -ResourceGroupName myResourceGroup `
  -Name demotopic `
  -Location eastus2 `
  -InputSchema CustomEventSchema `
  -InputMappingField @{eventType="myEventTypeField"} `
  -InputMappingDefaultValue @{subject="DefaultSubject"; dataVersion="1.0" }
```

## <a name="subscribe-to-event-grid-topic"></a>Event Grid 'e abone olma konusu

Özel konuya abone olurken, olayları almak için kullanmak istediğiniz şemayı belirtirsiniz. CloudEvents şeması, özel olay şeması veya Event Grid şemasını belirtirsiniz. Varsayılan değer Event Grid şemadır.

Aşağıdaki örnek bir Event Grid konusuna abone olur ve Event Grid şemasını kullanır. Azure CLI için şunu kullanın:

```azurecli-interactive
topicid=$(az eventgrid topic show --name demoTopic -g myResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name eventsub1 \
  --event-delivery-schema eventgridschema \
  --endpoint <endpoint_URL>
```

Sonraki örnek, olayın giriş şemasını kullanır:

```azurecli-interactive
az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name eventsub2 \
  --event-delivery-schema custominputschema \
  --endpoint <endpoint_URL>
```

Aşağıdaki örnek bir Event Grid konusuna abone olur ve Event Grid şemasını kullanır. PowerShell için şunu kullanın:

```azurepowershell-interactive
$topicid = (Get-AzEventGridTopic -ResourceGroupName myResourceGroup -Name demoTopic).Id

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName eventsub1 `
  -EndpointType webhook `
  -Endpoint <endpoint-url> `
  -DeliverySchema EventGridSchema
```

Sonraki örnek, olayın giriş şemasını kullanır:

```azurepowershell-interactive
New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName eventsub2 `
  -EndpointType webhook `
  -Endpoint <endpoint-url> `
  -DeliverySchema CustomInputSchema
```

## <a name="publish-event-to-topic"></a>Etkinliği konuya Yayımla

Artık özel konuya bir olay göndermeye hazır olursunuz ve eşlemenin sonucunu görebilirsiniz. [Örnek şemasında](#original-event-schema)bir olay göndermek için aşağıdaki komut dosyası:

Azure CLI için şunu kullanın:

```azurecli-interactive
endpoint=$(az eventgrid topic show --name demotopic -g myResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name demotopic -g myResourceGroup --query "key1" --output tsv)

event='[ { "myEventTypeField":"Created", "resource":"Users/example/Messages/1000", "resourceData":{"someDataField1":"SomeDataFieldValue"} } ]'

curl -X POST -H "aeg-sas-key: $key" -d "$event" $endpoint
```

PowerShell için şunu kullanın:

```azurepowershell-interactive
$endpoint = (Get-AzEventGridTopic -ResourceGroupName myResourceGroup -Name demotopic).Endpoint
$keys = Get-AzEventGridTopicKey -ResourceGroupName myResourceGroup -Name demotopic

$htbody = @{
    myEventTypeField="Created"
    resource="Users/example/Messages/1000"
    resourceData= @{
        someDataField1="SomeDataFieldValue"
    }
}

$body = "["+(ConvertTo-Json $htbody)+"]"
Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
```

Şimdi Web kancası uç noktanıza bakın. İki abonelik, farklı şemalarda olay teslim edildi.

İlk kullanılan abonelik olay Kılavuzu şeması. Teslim edilen olayın biçimi:

```json
{
  "id": "aa5b8e2a-1235-4032-be8f-5223395b9eae",
  "eventTime": "2018-11-07T23:59:14.7997564Z",
  "eventType": "Created",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "topic": "/subscriptions/<subscription-id>/resourceGroups/myResourceGroup/providers/Microsoft.EventGrid/topics/demotopic",
  "subject": "DefaultSubject",
  "data": {
    "myEventTypeField": "Created",
    "resource": "Users/example/Messages/1000",
    "resourceData": {
      "someDataField1": "SomeDataFieldValue"
    }
  }
}
```

Bu alanlar özel konudan eşlemeleri içerir. **Myeventtypefield** , **EventType** ile eşlendi. **Dataversion** ve **Subject** için varsayılan değerler kullanılır. **Veri** nesnesi, özgün olay şeması alanlarını içerir.

İkinci abonelik, giriş olay şemasını kullandı. Teslim edilen olayın biçimi:

```json
{
  "myEventTypeField": "Created",
  "resource": "Users/example/Messages/1000",
  "resourceData": {
    "someDataField1": "SomeDataFieldValue"
  }
}
```

Özgün alanların teslim edildiğini unutmayın.

## <a name="next-steps"></a>Sonraki adımlar

* Olay teslimi ve yeniden denemeler hakkında daha fazla bilgi için [Event Grid ileti teslimi ve yeniden deneyin](delivery-and-retry.md).
* Event Grid’e giriş için bkz. [Event Grid hakkında](overview.md).
* Event Grid kullanmaya hızlıca başlamak için bkz. [özel olayları oluşturma ve Azure Event Grid ile yönlendirme](custom-event-quickstart.md).
