---
title: Gremlin veritabanı için kaynak kilidi oluşturma ve Azure Cosmos DB için grafik oluşturma
description: Gremlin veritabanı için kaynak kilidi oluşturma ve Azure Cosmos DB için grafik oluşturma
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: 19809d7150c8a461f97282d1583d0d870d6af8bb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770687"
---
# <a name="create-a-resource-lock-for-azure-cosmos-gremlin-api-database-and-graph-using-azure-cli"></a>Azure CLı kullanarak Azure Cosmos Gremlin API veritabanı ve grafik için bir kaynak kilidi oluşturma
[!INCLUDE[appliesto-gremlin-api](../../../includes/appliesto-gremlin-api.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- Bu makale, Azure CLı 'nin sürüm 2.9.1 veya üstünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür.

> [!IMPORTANT]
> Kaynak kilitleri, Cosmos DB hesap ilk olarak etkinleştirilmiş özelliği ile kilitlenmediği takdirde, herhangi bir Gremlin SDK veya Azure portalını kullanarak bağlanan kullanıcılar tarafından yapılan değişiklikler için çalışmaz `disableKeyBasedMetadataWriteAccess` . Bu özelliğin nasıl etkinleştirileceği hakkında daha fazla bilgi edinmek için bkz. [SDK 'lardan değişiklikleri engellemeyi](../../../role-based-access-control.md#prevent-sdk-changes).

## <a name="sample-script"></a>Örnek betik

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/gremlin/lock.sh "Create a resource lock for an Azure Cosmos DB Gremlin API database and graph.")]

## <a name="script-explanation"></a>Betik açıklaması

Bu betik aşağıdaki komutları kullanır. Tablodaki her komut, komuta özgü belgelere yönlendirir.

| Komut | Notlar |
|---|---|
| [az kilitle oluştur](/cli/azure/lock#az_lock_create) | Bir kilit oluşturur. |
| [az Lock List](/cli/azure/lock#az_lock_list) | Kilit bilgilerini listeleyin. |
| [az Lock Show](/cli/azure/lock#az_lock_show) | Kilidin özelliklerini göster. |
| [az Lock Delete](/cli/azure/lock#az_lock_delete) | Bir kilidi siler. |

## <a name="next-steps"></a>Sonraki adımlar

- [Beklenmeyen değişiklikleri önlemek için kaynakları kilitleme](../../../../azure-resource-manager/management/lock-resources.md)

- [Azure Cosmos DB CLI belgeleri](/cli/azure/cosmosdb).

- [Azure Cosmos DB CLI GitHub deposu](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
