---
title: Azure Cosmos DB için bir SQL (çekirdek) API veritabanı ve kapsayıcısı için RU/sn 'yi Güncelleştir
description: Azure Cosmos DB için bir SQL (çekirdek) API veritabanı ve kapsayıcısı için RU/sn 'yi Güncelleştir
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 9/25/2019
ms.openlocfilehash: cde0615a5312372992d2604f32809a983c248a2b
ms.sourcegitcommit: a6718e2b0251b50f1228b1e13a42bb65e7bf7ee2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71275398"
---
# <a name="update-rus-for-a-sql-core-api-database-and-container-for-azure-cosmos-db-using-azure-cli"></a>Azure CLı kullanarak Azure Cosmos DB bir SQL (çekirdek) API veritabanı ve kapsayıcısı için RU/sn 'yi güncelleştirme

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

CLı 'yi yerel olarak yükleyip kullanmayı tercih ederseniz bu konu başlığı altında, Azure CLı sürüm 2.0.73 veya üstünü çalıştırıyor olmanız gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekiyorsa bkz. [Azure CLI'yı yükleme](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Örnek betik

Bu betik, paylaşılan aktarım hızına sahip bir SQL (Core) API veritabanı ve adanmış aktarım hızına sahip bir SQL (Core) API kapsayıcısı oluşturur, ardından hem veritabanı hem de kapsayıcı için üretilen işi günceller.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/sql/throughput.sh "Update RU/s for a SQL database and container.")]

## <a name="clean-up-deployment"></a>Dağıtımı temizleme

Betik örneği çalıştırıldıktan sonra, kaynak grubunu ve onunla ilişkili tüm kaynakları kaldırmak için aşağıdaki komut kullanılabilir.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>Betik açıklaması

Bu betik aşağıdaki komutları kullanır. Tablodaki her komut, komuta özgü belgelere yönlendirir.

| Komut | Notlar |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Tüm kaynakların depolandığı bir kaynak grubu oluşturur. |
| [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create) | Azure Cosmos DB hesabı oluşturur. |
| [az cosmosdb SQL veritabanı oluşturma](/cli/azure/cosmosdb/sql/database#az-cosmosdb-sql-database-create) | Azure Cosmos SQL (Core) veritabanı oluşturur. |
| [az cosmosdb SQL kapsayıcı oluşturma](/cli/azure/cosmosdb/sql/container#az-cosmosdb-sql-container-create) | Azure Cosmos SQL (Core) kapsayıcısı oluşturur. |
| [az cosmosdb SQL veritabanı aktarım hızı güncelleştirmesi](/cli/azure/cosmosdb/sql/database/throughput#az-cosmosdb-sql-database-throughput-update) | Azure Cosmos SQL (Core) veritabanı için RU/s 'yi güncelleştirin. |
| [az cosmosdb SQL Container üretilen iş güncelleştirmesi](/cli/azure/cosmosdb/sql/container/throughput#az-cosmosdb-sql-container-throughput-update) | Azure Cosmos SQL (Core) kapsayıcısı için RU/s 'yi güncelleştirin. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Bir kaynak grubunu tüm iç içe geçmiş kaynaklar dahil siler. |

## <a name="next-steps"></a>Sonraki adımlar

CLı Azure Cosmos DB hakkında daha fazla bilgi için bkz. [Azure Cosmos DB CLI belgeleri](/cli/azure/cosmosdb).

Tüm Azure Cosmos DB CLı betiği örnekleri [Azure Cosmos DB CLI GitHub deposunda](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)bulunabilir.
