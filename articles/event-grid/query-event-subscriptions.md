---
title: Azure Event Grid aboneliklerini sorgulama
description: Azure Event Grid aboneliklerini listeleyin açıklar.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: spelluru
ms.openlocfilehash: 08fa9ada97c652ca609a499da1c8e87f7e4487db
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786619"
---
# <a name="query-event-grid-subscriptions"></a>Event Grid aboneliklerini sorgulama 

Bu makalede, Azure aboneliğinizdeki Event Grid abonelikleri listeleyin açıklar. Var olan, Event Grid aboneliklerini sorgulama sırasında abonelikler farklı türlerini anlamak önemlidir. Almak istediğiniz abonelik türüne göre farklı parametreler sağlamanız.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="resource-groups-and-azure-subscriptions"></a>Kaynak grupları ve Azure abonelikleri

Azure Abonelikleriniz ve kaynak gruplarınız Azure kaynaklarını değildir. Bu nedenle, event grid abonelikleri kaynak gruplarına veya Azure abonelikleri Azure kaynakları için event grid abonelikleri aynı özelliklere sahip değilsiniz. Kaynak grupları için Event grid abonelikleri veya Azure abonelikleri genel olarak kabul edilir.

Bir Azure aboneliği ve kaynak gruplarına için Event grid abonelikleri almak için parametreleri sağlamanız gerekmez. Sorgulamak istediğiniz Azure aboneliği seçtiğinizden emin olun. Aşağıdaki örnekler özel konular veya Azure kaynakları için event grid abonelikleri elde etmezsiniz.

Azure CLI için şunu kullanın:

```azurecli-interactive
az account set -s "My Azure Subscription"
az eventgrid event-subscription list
```

PowerShell için şunu kullanın:

```azurepowershell-interactive
Set-AzContext -Subscription "My Azure Subscription"
Get-AzEventGridSubscription
```

Bir Azure aboneliği için Event grid abonelikleri almak için konu türü sağlamak **Microsoft.Resources.Subscriptions**.

Azure CLI için şunu kullanın:

```azurecli-interactive
az eventgrid event-subscription list --topic-type-name "Microsoft.Resources.Subscriptions" --location global
```

PowerShell için şunu kullanın:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicTypeName "Microsoft.Resources.Subscriptions"
```

Event grid abonelikleri için bir Azure aboneliğinde tüm kaynak gruplarını almak için konu türü sağlamak **Microsoft.Resources.ResourceGroups**.

Azure CLI için şunu kullanın:

```azurecli-interactive
az eventgrid event-subscription list --topic-type-name "Microsoft.Resources.ResourceGroups" --location global
```

PowerShell için şunu kullanın:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicTypeName "Microsoft.Resources.ResourceGroups"
```

Belirtilen kaynak grubu için Event grid abonelikleri almak için bir parametre olarak kaynak grubunun adını sağlayın.

Azure CLI için şunu kullanın:

```azurecli-interactive
az eventgrid event-subscription list --resource-group myResourceGroup --location global
```

PowerShell için şunu kullanın:

```azurepowershell-interactive
Get-AzEventGridSubscription -ResourceGroupName myResourceGroup
```

## <a name="custom-topics-and-azure-resources"></a>Özel konu ve Azure kaynakları

Event grid özel konuları Azure kaynaklarıdır. Bu nedenle, event grid abonelikleri özel konu ve Blob Depolama hesabı gibi diğer kaynaklar için aynı şekilde sorgulayın. Özel konu için Event grid abonelikleri almak için kaynak tanımlamak veya kaynağın yerini belirlemek parametreleri sağlamanız gerekir. Azure aboneliğiniz geniş çapta sorgu event grid abonelikleri kaynaklar için filtrelenebilir değil.

Event grid abonelikleri özel konu ve diğer kaynaklar için bir konumda almak için konumun adını sağlayın.

Azure CLI için şunu kullanın:

```azurecli-interactive
az eventgrid event-subscription list --location westus2
```

PowerShell için şunu kullanın:

```azurepowershell-interactive
Get-AzEventGridSubscription -Location westus2
```

Özel konu için abonelik almak için bir konum için konum ve konu türünü sağlamak **Microsoft.EventGrid.Topics**.

Azure CLI için şunu kullanın:

```azurecli-interactive
az eventgrid event-subscription list --topic-type-name "Microsoft.EventGrid.Topics" --location "westus2"
```

PowerShell için şunu kullanın:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicTypeName "Microsoft.EventGrid.Topics" -Location westus2
```

Depolama hesapları için abonelikler için bir konum almak için konum ve konu türüne sağlayın **Microsoft.Storage.StorageAccounts**.

Azure CLI için şunu kullanın:

```azurecli-interactive
az eventgrid event-subscription list --topic-type "Microsoft.Storage.StorageAccounts" --location westus2
```

PowerShell için şunu kullanın:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicTypeName "Microsoft.Storage.StorageAccounts" -Location westus2
```

Bir özel konu için Event grid abonelikleri almak için özel bir konu adı ve kaynak grubu adını sağlayın.

Azure CLI için şunu kullanın:

```azurecli-interactive
az eventgrid event-subscription list --topic-name myCustomTopic --resource-group myResourceGroup
```

PowerShell için şunu kullanın:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicName myCustomTopic -ResourceGroupName myResourceGroup
```

Belirli bir kaynak için Event grid abonelikleri almak için kaynak kimliği sağlayın.

Azure CLI için şunu kullanın:

```azurecli-interactive
resourceid=$(az resource show -n mystorage -g myResourceGroup --resource-type "Microsoft.Storage/storageaccounts" --query id --output tsv)
az eventgrid event-subscription list --resource-id $resourceid
```

PowerShell için şunu kullanın:

```azurepowershell-interactive
$resourceid = (Get-AzResource -Name mystorage -ResourceGroupName myResourceGroup).ResourceId
Get-AzEventGridSubscription -ResourceId $resourceid
```

## <a name="next-steps"></a>Sonraki adımlar

* Olay teslimi ve yeniden deneme hakkında bilgi için [Event Grid iletiyi teslim ve yeniden deneme](delivery-and-retry.md).
* Event Grid’e giriş için bkz. [Event Grid hakkında](overview.md).
* Event Grid ile hızla çalışmaya başlamak için bkz: [Azure Event Grid ile özel olaylar oluşturma ve yönlendirme](custom-event-quickstart.md).
