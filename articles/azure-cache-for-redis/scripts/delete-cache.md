---
title: Redsıs için Azure önbelleğini silme-Azure CLı
description: Bu Azure CLı kod örneği, az redin Delete komutunu kullanarak Redsıs örneği için bir Azure önbelleğinin nasıl silineceğini gösterir.
author: yegu-ms
ms.author: yegu
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.custom: devx-track-azurecli
ms.openlocfilehash: 9ce6e20e5e4866bc4daa3f331fa2a8612ac6c260
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96184170"
---
# <a name="delete-an-azure-cache-for-redis"></a>Redsıs için Azure önbelleğini silme

Bu senaryoda, redin için bir Azure önbelleğinin nasıl silineceğini öğrenirsiniz.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Örnek betik

[!code-azurecli[main](../../../cli_scripts/redis-cache/delete-cache/delete-cache.sh "Azure Cache for Redis")]

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>Betik açıklaması

Bu betik, Redsıs örneği için bir Azure önbelleğini silmek üzere aşağıdaki komutları kullanır. Tablodaki her komut, komuta özgü belgelere yönlendirir.

| Komut | Notlar |
|---|---|
| [az redsıs Delete](/cli/azure/redis) | Redsıs örneği için Azure önbelleğini silin. |


## <a name="next-steps"></a>Sonraki adımlar

Azure CLI hakkında daha fazla bilgi için bkz. [Azure CLI belgeleri](/cli/azure).

Redsıs CLı betik örnekleri için ek Azure önbelleği, [redin belgeleri Için Azure önbelleğinde](../cli-samples.md)bulunabilir.