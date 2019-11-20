---
title: Azure CLı betik örneği-Redsıs için Azure önbelleği için konak adı, bağlantı noktaları ve anahtarlar alın
description: Azure CLI betik örneği - bir Azure önbelleği için Redis örneği için ana bilgisayar adı, bağlantı noktalarını ve anahtarları alma
author: yegu-ms
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.author: yegu
ms.openlocfilehash: 604ec6de3b95a4bc289176d54d9c7b0a6c42eae6
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74122521"
---
# <a name="get-the-hostname-ports-and-keys-for-azure-cache-for-redis"></a>Ana bilgisayar adı, bağlantı noktalarını ve anahtarları, Azure önbelleği için Redis için Al

Bu senaryoda, ana bilgisayar adı, bağlantı noktaları ve bir Azure önbelleği için Redis örneği bağlanmak için kullanılan anahtarlarını alma hakkında bilgi edinin.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Örnek betik

[!code-azurecli[main](../../../cli_scripts/redis-cache/cache-keys-ports/cache-keys-ports.sh "Azure Cache for Redis")]


## <a name="script-explanation"></a>Betik açıklaması

Bu betik, ana bilgisayar adı, anahtarlar ve bağlantı noktaları, bir Azure önbelleği için Redis örneği almak için aşağıdaki komutları kullanır. Tablodaki her komut, komuta özgü belgelere yönlendirir.

| Komut | Notlar |
|---|---|
| [az redis show](https://docs.microsoft.com/cli/azure/redis) | Bir Azure önbelleği için Redis örneğinin ayrıntılarını alın. |
| [az redis anahtarları-Listele](https://docs.microsoft.com/cli/azure/redis) | Bir Azure önbelleği için Redis örneği için erişim anahtarlarını alır. |


## <a name="next-steps"></a>Sonraki adımlar

Azure CLI hakkında daha fazla bilgi için bkz. [Azure CLI belgeleri](https://docs.microsoft.com/cli/azure).

Ek Azure önbelleği için Redis CLI betiği örnekleri, içinde bulunabilir [Azure önbelleği için Redis belgeleri](../cli-samples.md).
