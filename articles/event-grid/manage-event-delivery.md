---
title: Atılacak mektup ve yeniden deneme ilkeleri-Azure Event Grid
description: Event Grid için olay teslim seçeneklerini özelleştirmeyi açıklar. Bir atılacak mektup hedefi ayarlayın ve teslimin ne kadar süreyle yeniden deneneceğini belirtin.
ms.topic: conceptual
ms.date: 07/20/2020
ms.openlocfilehash: 7d8cd74ccfb77bcec45d06071a4f46fb2a640cf8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92460946"
---
# <a name="set-dead-letter-location-and-retry-policy"></a>Teslim edilemeyen ileti konumu ve yeniden deneme ilkesi ayarlama

Olay aboneliği oluştururken, olay teslimi ayarlarını özelleştirebilirsiniz. Bu makalede, bir atılacak mektup konumunun nasıl ayarlanacağı ve yeniden deneme ayarlarını nasıl özelleştireceğiniz gösterilmektedir. Bu özellikler hakkında daha fazla bilgi için bkz. [Event Grid ileti teslimi ve yeniden deneme](delivery-and-retry.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!NOTE]
> İleti teslimi, yeniden denemeler ve atılacak alma hakkında bilgi edinmek için bkz. kavramsal Makale: [Event Grid ileti teslimi ve yeniden deneme](delivery-and-retry.md).

## <a name="set-dead-letter-location"></a>Atılacak mektup konumunu ayarla

Atılacak bir harf konumu ayarlamak için bir uç noktaya teslim edilemeyen olayları tutmak üzere bir depolama hesabı gerekir. Örneklerde mevcut bir depolama hesabının kaynak KIMLIĞI alınır. Bu depolama hesabında, atılacak ileti uç noktası için bir kapsayıcı kullanan bir olay aboneliği oluşturur.

> [!NOTE]
> - Bu makaledeki komutları çalıştırmadan önce depolamada bir depolama hesabı ve bir blob kapsayıcısı oluşturun.
> - Event Grid hizmeti bu kapsayıcıda Bloblar oluşturur. Blobların adları, büyük harfli tüm harflerle Event Grid abonelik adına sahip olur. Örneğin, aboneliğin adı-blob-aboneliğim ise, atılacak mektup bloblarının adları-BLOB-ABONELIĞIM (myblobcontainer/MY-BLOB-SUBSCRIPTION/2019/8/8/5/111111111-1111-1111-1111-111111111111.js) olacaktır. Bu davranış, Azure hizmetleri arasında durum işlemede farklılık açısından korunmamıza olanak sağlar.


### <a name="azure-cli"></a>Azure CLI’si

```azurecli-interactive
containername=testcontainer

topicid=$(az eventgrid topic show --name demoTopic -g gridResourceGroup --query id --output tsv)
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --deadletter-endpoint $storageid/blobServices/default/containers/$containername
```

Etkin olmayan devre dışı bırakmak için, olay aboneliği oluşturmak için komutunu yeniden çalıştırın, ancak için bir değer sağlayın `deadletter-endpoint` . Olay aboneliğini silmeniz gerekmez.

> [!NOTE]
> Yerel makinenizde Azure CLı kullanıyorsanız, Azure CLı sürüm 2.0.56 veya üstünü kullanın. Azure CLı 'nın en son sürümünü yükleme yönergeleri için bkz. [Azure CLI 'Yı yükleme](/cli/azure/install-azure-cli).

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
$containername = "testcontainer"

$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id
$storageid = (Get-AzStorageAccount -ResourceGroupName gridResourceGroup -Name demostorage).Id

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -DeadLetterEndpoint "$storageid/blobServices/default/containers/$containername"
```

Etkin olmayan devre dışı bırakmak için, olay aboneliği oluşturmak için komutunu yeniden çalıştırın, ancak için bir değer sağlayın `DeadLetterEndpoint` . Olay aboneliğini silmeniz gerekmez.

> [!NOTE]
> Yerel makinenizde Azure PEShell kullanıyorsanız, Azure PowerShell Version 1.1.0 veya üstünü kullanın. [Azure indirmelerinde](https://azure.microsoft.com/downloads/)en son Azure PowerShell indirin ve yükleyin.

## <a name="set-retry-policy"></a>Yeniden deneme ilkesini ayarla

Event Grid bir abonelik oluştururken, olayın ne kadar süreyle teslim etmesi gerektiğini Event Grid için değerler ayarlayabilirsiniz. Varsayılan olarak, Event Grid 24 saat (1440 dakika) veya 30 kez çalışır. Olay Kılavuzu aboneliğiniz için bu değerlerden birini ayarlayabilirsiniz. Etkinliğin yaşam süresi değeri 1 ile 1440 arasında bir tamsayı olmalıdır. En fazla yeniden deneme değeri, 1 ile 30 arasında bir tamsayı olmalıdır.

[Yeniden deneme zamanlamasını](delivery-and-retry.md#retry-schedule-and-duration)yapılandıramazsınız.

### <a name="azure-cli"></a>Azure CLI’si

Olayı 1440 dakikadan farklı bir değere yaşam süresi olarak ayarlamak için şunu kullanın:

```azurecli-interactive
az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --event-ttl 720
```

En fazla yeniden deneme sayısını 30 ' dan farklı bir değere ayarlamak için şunu kullanın:

```azurecli-interactive
az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --max-delivery-attempts 18
```

> [!NOTE]
> Hem hem de ayarlarsanız `event-ttl` `max-deliver-attempts` , Event Grid olay tesliminin ne zaman durdurulacağını öğrenmek için ilk sona erme tarihi kullanır. Örneğin, yaşam süresi (TTL) ve en fazla 10 teslim girişimi olarak 30 dakika ayarlarsanız. 30 dakika sonra bir olay teslim edilmediğinde (veya) 10 denemeden sonra, bu durum ilk olarak gerçekleşirse, olay atılacak olur.  

### <a name="powershell"></a>PowerShell

Olayı 1440 dakikadan farklı bir değere yaşam süresi olarak ayarlamak için şunu kullanın:

```azurepowershell-interactive
$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -EventTtl 720
```

En fazla yeniden deneme sayısını 30 ' dan farklı bir değere ayarlamak için şunu kullanın:

```azurepowershell-interactive
$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -MaxDeliveryAttempt 18
```

> [!NOTE]
> Hem hem de ayarlarsanız `event-ttl` `max-deliver-attempts` , Event Grid olay tesliminin ne zaman durdurulacağını öğrenmek için ilk sona erme tarihi kullanır. Örneğin, yaşam süresi (TTL) ve en fazla 10 teslim girişimi olarak 30 dakika ayarlarsanız. 30 dakika sonra bir olay teslim edilmediğinde (veya) 10 denemeden sonra, bu durum ilk olarak gerçekleşirse, olay atılacak olur.  

## <a name="next-steps"></a>Sonraki adımlar

* Kullanılmayan mektup olaylarını işlemek için bir Azure Işlev uygulaması kullanan örnek bir uygulama için bkz. [.NET için Azure Event Grid atılacak harf örnekleri](https://azure.microsoft.com/resources/samples/event-grid-dotnet-handle-deadlettered-events/).
* Olay teslimi ve yeniden denemeler hakkında daha fazla bilgi için [Event Grid ileti teslimi ve yeniden deneyin](delivery-and-retry.md).
* Event Grid’e giriş için bkz. [Event Grid hakkında](overview.md).
* Event Grid kullanmaya hızlıca başlamak için bkz. [özel olayları oluşturma ve Azure Event Grid ile yönlendirme](custom-event-quickstart.md).
