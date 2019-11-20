---
title: Atılacak mektup ve yeniden deneme ilkeleri-Azure Event Grid
description: Event Grid için olay teslim seçeneklerini özelleştirmeyi açıklar. Bir atılacak mektup hedefi ayarlayın ve teslimin ne kadar süreyle yeniden deneneceğini belirtin.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: spelluru
ms.openlocfilehash: caed3c077b4df5da5fd8541b2f7e85ef119604b0
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72794031"
---
# <a name="dead-letter-and-retry-policies"></a>Atılacak mektup ve yeniden deneme ilkeleri

Olay aboneliği oluştururken, olay teslimi ayarlarını özelleştirebilirsiniz. Bu makalede, bir atılacak mektup konumunun nasıl ayarlanacağı ve yeniden deneme ayarlarını nasıl özelleştireceğiniz gösterilmektedir. Bu özellikler hakkında daha fazla bilgi için bkz. [Event Grid ileti teslimi ve yeniden deneme](delivery-and-retry.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="set-dead-letter-location"></a>Atılacak mektup konumunu ayarla

Atılacak bir harf konumu ayarlamak için bir uç noktaya teslim edilemeyen olayları tutmak üzere bir depolama hesabı gerekir. Örneklerde mevcut bir depolama hesabının kaynak KIMLIĞI alınır. Bu depolama hesabında, atılacak ileti uç noktası için bir kapsayıcı kullanan bir olay aboneliği oluşturur.

> [!NOTE]
> - Bu makaledeki komutları çalıştırmadan önce depolamada bir depolama hesabı ve bir blob kapsayıcısı oluşturun.
> - Event Grid hizmeti bu kapsayıcıda Bloblar oluşturur. Blobların adları, büyük harfli tüm harflerle Event Grid abonelik adına sahip olur. Örneğin, aboneliğin adı-blob-aboneliğim ise, atılacak mektup bloblarının adları-BLOB-ABONELIĞIM (myblobcontainer/MY-BLOB-SUBSCRIPTION/2019/8/8/5/111111111-1111-1111-1111 -111111111111. JSON) olacaktır. Bu davranış, Azure hizmetleri arasında durum işlemede farklılık açısından korunmamıza olanak sağlar.


### <a name="azure-cli"></a>Azure CLI

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

Etkin olmayan devre dışı bırakmak için, olay aboneliği oluşturmak için komutunu yeniden çalıştırın ancak `deadletter-endpoint`için bir değer sağlayın. Olay aboneliğini silmeniz gerekmez.

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

Etkin olmayan devre dışı bırakmak için, olay aboneliği oluşturmak için komutunu yeniden çalıştırın ancak `DeadLetterEndpoint`için bir değer sağlayın. Olay aboneliğini silmeniz gerekmez.

> [!NOTE]
> Yerel makinenizde Azure PEShell kullanıyorsanız, Azure PowerShell Version 1.1.0 veya üstünü kullanın. [Azure indirmelerinde](https://azure.microsoft.com/downloads/)en son Azure PowerShell indirin ve yükleyin.

## <a name="set-retry-policy"></a>Yeniden deneme ilkesini ayarla

Event Grid bir abonelik oluştururken, olayın ne kadar süreyle teslim etmesi gerektiğini Event Grid için değerler ayarlayabilirsiniz. Varsayılan olarak, Event Grid 24 saat (1440 dakika) veya 30 kez çalışır. Olay Kılavuzu aboneliğiniz için bu değerlerden birini ayarlayabilirsiniz. Etkinliğin yaşam süresi değeri 1 ile 1440 arasında bir tamsayı olmalıdır. En fazla yeniden deneme değeri, 1 ile 30 arasında bir tamsayı olmalıdır.

[Yeniden deneme zamanlamasını](delivery-and-retry.md#retry-schedule-and-duration)yapılandıramazsınız.

### <a name="azure-cli"></a>Azure CLI

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

Hem `event-ttl` hem de `max-deliver-attempts`ayarlarsanız, Event Grid olay tesliminin ne zaman durdurulacağını anlamak için ilk sona erme tarihi kullanılır.

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

Hem `EventTtl` hem de `MaxDeliveryAttempt`ayarlarsanız, Event Grid olay tesliminin ne zaman durdurulacağını anlamak için ilk sona erme tarihi kullanılır.

## <a name="next-steps"></a>Sonraki adımlar

* Kullanılmayan mektup olaylarını işlemek için bir Azure Işlev uygulaması kullanan örnek bir uygulama için bkz. [.NET için Azure Event Grid atılacak harf örnekleri](https://azure.microsoft.com/resources/samples/event-grid-dotnet-handle-deadlettered-events/).
* Olay teslimi ve yeniden denemeler hakkında daha fazla bilgi için [Event Grid ileti teslimi ve yeniden deneyin](delivery-and-retry.md).
* Event Grid’e giriş için bkz. [Event Grid hakkında](overview.md).
* Event Grid kullanmaya hızlıca başlamak için bkz. [özel olayları oluşturma ve Azure Event Grid ile yönlendirme](custom-event-quickstart.md).
