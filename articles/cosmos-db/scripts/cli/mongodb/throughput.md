---
title: Azure Cosmos DB için MongoDB API 'si için bir veritabanı ve koleksiyon için RU/sn 'yi Güncelleştir
description: Azure Cosmos DB için MongoDB API 'si için bir veritabanı ve koleksiyon için RU/sn 'yi Güncelleştir
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 9/25/2019
ms.openlocfilehash: 20516a66fe664e415b97e40beacd77c34c7ccaea
ms.sourcegitcommit: a6718e2b0251b50f1228b1e13a42bb65e7bf7ee2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71275510"
---
# <a name="update-rus-for-a-database-and-collection-for-mongodb-api-for-azure-cosmos-db-using-azure-cli"></a>Azure CLı kullanarak Azure Cosmos DB için MongoDB API 'sine yönelik bir veritabanı ve koleksiyon için RU/sn 'yi güncelleştirme

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

CLı 'yi yerel olarak yükleyip kullanmayı tercih ederseniz bu konu başlığı altında, Azure CLı sürüm 2.0.73 veya üstünü çalıştırıyor olmanız gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekiyorsa bkz. [Azure CLI'yı yükleme](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Örnek betik

Bu betik, bir paylaşılan aktarım hızı ve MongoDB API 'SI için Azure Cosmos DB için adanmış aktarım hızı olan bir koleksiyon oluşturur ve ardından hem veritabanı hem de koleksiyon için üretilen işi günceller.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/mongodb/throughput.sh "Update RU/s for an Azure Cosmos DB MongoDB API database and collection.")]

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
| [az cosmosdb MongoDB veritabanı oluşturma](/cli/azure/cosmosdb/mongodb/database#az-cosmosdb-mongodb-database-create) | Azure Cosmos MongoDB API veritabanı oluşturur. |
| [az cosmosdb MongoDB koleksiyon oluşturma](/cli/azure/cosmosdb/mongodb/collection#az-cosmosdb-mongodb-collection-create) | Azure Cosmos MongoDB API 'SI koleksiyonu oluşturur. |
| [az cosmosdb MongoDB veritabanı aktarım hızı güncelleştirmesi](/cli/azure/cosmosdb/mongodb/database/throughput#az-cosmosdb-mongodb-database-throughput-update) | Azure Cosmos MongoDB API veritabanı için ru güncelleştirme. |
| [az cosmosdb MongoDB koleksiyon verimlilik güncelleştirmesi](/cli/azure/cosmosdb/mongodb/collection/throughput#az-cosmosdb-mongodb-collection-throughput-update) | Azure Cosmos MongoDB API koleksiyonu için ru güncelleştirme. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Bir kaynak grubunu tüm iç içe geçmiş kaynaklar dahil siler. |

## <a name="next-steps"></a>Sonraki adımlar

CLı Azure Cosmos DB hakkında daha fazla bilgi için bkz. [Azure Cosmos DB CLI belgeleri](/cli/azure/cosmosdb).

Tüm Azure Cosmos DB CLı betiği örnekleri [Azure Cosmos DB CLI GitHub deposunda](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)bulunabilir.
