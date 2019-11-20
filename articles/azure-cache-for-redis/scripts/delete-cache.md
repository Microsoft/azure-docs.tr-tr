---
title: Azure CLI betik örneği - bir Azure önbelleği için Redis Delete
description: Azure CLI betik örneği - bir Azure önbelleği için Redis Delete
author: yegu-ms
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.author: yegu
ms.openlocfilehash: a2884fd326b6091680b8d81a905f3ee3320a2740
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74121209"
---
# <a name="delete-an-azure-cache-for-redis"></a>Bir Azure önbelleği için Redis Sil

Bu senaryoda, bir Azure önbelleği için Redis silme hakkında bilgi edinin.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Örnek betik

[!code-azurecli[main](../../../cli_scripts/redis-cache/delete-cache/delete-cache.sh "Azure Cache for Redis")]

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>Betik açıklaması

Bu betik, bir Azure önbelleği için Redis örneği silmek için aşağıdaki komutları kullanır. Tablodaki her komut, komuta özgü belgelere yönlendirir.

| Komut | Notlar |
|---|---|
| [az redis delete](https://docs.microsoft.com/cli/azure/redis) | Azure Cache, Redis örneği için silin. |


## <a name="next-steps"></a>Sonraki adımlar

Azure CLI hakkında daha fazla bilgi için bkz. [Azure CLI belgeleri](https://docs.microsoft.com/cli/azure).

Ek Azure önbelleği için Redis CLI betiği örnekleri, içinde bulunabilir [Azure önbelleği için Redis belgeleri](../cli-samples.md).
