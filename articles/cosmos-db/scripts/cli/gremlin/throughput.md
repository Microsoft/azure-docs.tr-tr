---
title: Azure Cosmos DB Gremlin API kaynakları için üretilen iş (RU/s) işlemleri için Azure CLı betikleri
description: Azure Cosmos DB Gremlin API kaynakları için üretilen iş (RU/s) işlemleri için Azure CLı betikleri
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: sample
ms.date: 10/07/2020
ms.openlocfilehash: ac8f915a1e9cc73c076879ea8e5ee487c3fe0c3f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770642"
---
# <a name="throughput-rus-operations-with-azure-cli-for-a-database-or-graph-for-azure-cosmos-db---gremlin-api"></a>Azure Cosmos DB-Gremlin API 'SI için bir veritabanı veya grafik için Azure CLı ile üretilen iş (RU/s) işlemleri
[!INCLUDE[appliesto-gremlin-api](../../../includes/appliesto-gremlin-api.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- Bu makale, Azure CLı 'nin sürüm 2.12.1 veya üstünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür.

## <a name="sample-script"></a>Örnek betik

Bu betik, paylaşılan aktarım hızı ve bir Gremlin grafiğine sahip bir Gremlin veritabanını, ayrılmış aktarım hızı ile oluşturur ve ardından hem veritabanı hem de grafik için üretilen işi günceller. Komut dosyası daha sonra standart 'den otomatik ölçeklendirme aktarım hızına geçirilir ve sonra otomatik ölçeklendirme üretilen işinin değerini geçiş yapıldıktan sonra okur.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/gremlin/throughput.sh "Throughput operations for a Gremlin database and graph.")]

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
| [az cosmosdb Gremlin veritabanı oluşturma](/cli/azure/cosmosdb/gremlin/database#az_cosmosdb_gremlin_database_create) | Azure Cosmos Gremlin veritabanı oluşturur. |
| [az cosmosdb Gremlin Graf Create](/cli/azure/cosmosdb/gremlin/graph#az_cosmosdb_gremlin_graph_create) | Azure Cosmos Gremlin grafiği oluşturur. |
| [az cosmosdb Gremlin veritabanı aktarım hızı güncelleştirmesi](/cli/azure/cosmosdb/gremlin/database/throughput#az_cosmosdb_gremlin_database_throughput_update) | Azure Cosmos Gremlin veritabanı için RU/sn 'yi güncelleştirin. |
| [az cosmosdb Gremlin grafik aktarım hızı güncelleştirmesi](/cli/azure/cosmosdb/gremlin/graph/throughput#az_cosmosdb_gremlin_graph_throughput_update) | Azure Cosmos Gremlin grafiğinde RU/s 'yi güncelleştirin. |
| [az cosmosdb Gremlin veritabanı aktarım hızı geçişi](/cli/azure/cosmosdb/gremlin/database/throughput#az_cosmosdb_gremlin_database_throughput_migrate) | Azure Cosmos Gremlin veritabanı için aktarım hızını geçirin. |
| [az cosmosdb Gremlin grafik işleme geçişi](/cli/azure/cosmosdb/gremlin/graph/throughput#az_cosmosdb_gremlin_graph_throughput_migrate) | Azure Cosmos Gremlin grafiğinde aktarım hızını geçirin. |
| [az group delete](/cli/azure/resource#az_resource_delete) | Bir kaynak grubunu tüm iç içe geçmiş kaynaklar dahil siler. |

## <a name="next-steps"></a>Sonraki adımlar

CLı Azure Cosmos DB hakkında daha fazla bilgi için bkz. [Azure Cosmos DB CLI belgeleri](/cli/azure/cosmosdb).

Tüm Azure Cosmos DB CLı betiği örnekleri [Azure Cosmos DB CLI GitHub deposunda](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)bulunabilir.
