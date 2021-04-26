---
title: MongoDB kaynakları için Azure Cosmos DB API 'SI için üretilen iş (RU/s) işlemleri için Azure CLı betikleri
description: MongoDB kaynakları için Azure Cosmos DB API 'SI için üretilen iş (RU/s) işlemleri için Azure CLı betikleri
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 10/07/2020
ms.openlocfilehash: fc477fdd842025722a2bcfcd0172ab9d57fb0d4e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790727"
---
# <a name="throughput-rus-operations-with-azure-cli-for-a-database-or-graph-for-azure-cosmos-db-api-for-mongodb"></a>MongoDB için Azure Cosmos DB API 'sine yönelik bir veritabanı veya grafik için Azure CLı ile üretilen iş (RU/s) işlemleri
[!INCLUDE[appliesto-mongodb-api](../../../includes/appliesto-mongodb-api.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- Bu makale, Azure CLı 'nin sürüm 2.12.1 veya üstünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür.

## <a name="sample-script"></a>Örnek betik

Bu betik, paylaşılan aktarım hızı ile bir MongoDB veritabanı oluşturur ve ayrılmış iş hacmi olan koleksiyonu, ardından her ikisi için de üretilen işi güncelleştirir. Komut dosyası daha sonra standart 'den otomatik ölçeklendirme aktarım hızına geçirilir ve sonra otomatik ölçeklendirme üretilen işinin değerini geçiş yapıldıktan sonra okur.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/mongodb/throughput.sh "Throughput operations for Azure Cosmos DB API for MongoDB.")]

## <a name="clean-up-deployment"></a>Dağıtımı temizleme

Betik örneği çalıştırıldıktan sonra, kaynak grubunu ve onunla ilişkili tüm kaynakları kaldırmak için aşağıdaki komut kullanılabilir.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>Betik açıklaması

Bu betik aşağıdaki komutları kullanır. Tablodaki her komut, komuta özgü belgelere yönlendirir.

| Komut | Notlar |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Tüm kaynakların depolandığı bir kaynak grubu oluşturur. |
| [az cosmosdb create](/cli/azure/cosmosdb#az_cosmosdb_create) | Azure Cosmos DB hesabı oluşturur. |
| [az cosmosdb MongoDB veritabanı oluşturma](/cli/azure/cosmosdb/mongodb/database#az_cosmosdb_mongodb_database_create) | Azure Cosmos MongoDB API veritabanı oluşturur. |
| [az cosmosdb MongoDB koleksiyon oluşturma](/cli/azure/cosmosdb/mongodb/collection#az_cosmosdb_mongodb_collection_create) | Azure Cosmos MongoDB API 'SI koleksiyonu oluşturur. |
| [az cosmosdb MongoDB veritabanı aktarım hızı güncelleştirmesi](/cli/azure/cosmosdb/mongodb/database/throughput#az_cosmosdb_mongodb_database_throughput_update) | Azure Cosmos MongoDB API veritabanı için ru güncelleştirme. |
| [az cosmosdb MongoDB koleksiyon verimlilik güncelleştirmesi](/cli/azure/cosmosdb/mongodb/collection/throughput#az_cosmosdb_mongodb_collection_throughput_update) | Azure Cosmos MongoDB API koleksiyonu için ru güncelleştirme. |
| [az cosmosdb MongoDB veritabanı verimlilik geçişi](/cli/azure/cosmosdb/mongodb/database/throughput#az_cosmosdb_mongodb_database_throughput_migrate) | Bir veritabanı için aktarım hızını geçirin. |
| [az cosmosdb MongoDB koleksiyon verimlilik geçişi](/cli/azure/cosmosdb/mongodb/collection/throughput#az_cosmosdb_mongodb_collection_throughput-migrate) | Bir koleksiyon için aktarım hızını geçirin. |
| [az group delete](/cli/azure/resource#az_resource_delete) | Bir kaynak grubunu tüm iç içe geçmiş kaynaklar dahil siler. |

## <a name="next-steps"></a>Sonraki adımlar

CLı Azure Cosmos DB hakkında daha fazla bilgi için bkz. [Azure Cosmos DB CLI belgeleri](/cli/azure/cosmosdb).

Tüm Azure Cosmos DB CLı betiği örnekleri [Azure Cosmos DB CLI GitHub deposunda](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)bulunabilir.
