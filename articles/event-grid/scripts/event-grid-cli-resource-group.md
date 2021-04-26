---
title: Azure CLI betik örneği - Kaynak grubuna abone olma | Microsoft Docs
description: Bu makalede, bir kaynak grubu için Azure Event Grid olaylarına nasıl abone olunacağı gösteren örnek bir Azure CLı betiği sunulmaktadır.
ms.devlang: azurecli
ms.topic: sample
ms.date: 07/08/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 31309ffc6397fec942b48209c7fd7c950e0fdb97
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107871354"
---
# <a name="subscribe-to-events-for-a-resource-group-with-azure-cli"></a>Azure CLI ile bir kaynak grubu için olaylara abone olma

Bu betik, bir kaynak grubu için olaylara bir Event Grid aboneliği oluşturur. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Önizleme örnek betiği için Event Grid uzantısı gerekir. Yüklemek için `az extension add --name eventgrid` komutunu çalıştırın.

## <a name="sample-script---stable"></a>Örnek betik - kararlı

[!code-azurecli[main](../../../cli_scripts/event-grid/subscribe-to-resource-group/subscribe-to-resource-group.sh "Subscribe to resource group")]

## <a name="sample-script---preview-extension"></a>Örnek betik - önizleme uzantısı

[!code-azurecli[main](../../../cli_scripts/event-grid/subscribe-to-resource-group-preview/subscribe-to-resource-group-preview.sh "Subscribe to resource group")]

## <a name="script-explanation"></a>Betik açıklaması

Bu betik, olay aboneliğini oluşturmak için aşağıdaki komutu kullanır. Tablodaki her komut, komuta özgü belgelere yönlendirir.

| Komut | Notlar |
|---|---|
| [az eventgrid event-subscription create](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_create) | Event Grid aboneliği oluşturun. |
| [az eventgrid event-subscription create](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_create) - uzantı sürümü | Event Grid aboneliği oluşturun. |

## <a name="next-steps"></a>Sonraki adımlar

* Abonelikleri sorgulama hakkında bilgi edinmek için bkz. [Event Grid aboneliklerini sorgulama](../query-event-subscriptions.md).
* Azure CLI hakkında daha fazla bilgi için bkz. [Azure CLI belgeleri](/cli/azure).
