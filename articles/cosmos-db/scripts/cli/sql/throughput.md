---
title: Azure Cosmos DB Core (SQL) API kaynakları için üretilen iş (RU/s) işlemleri için Azure CLı betikleri
description: Azure Cosmos DB Core (SQL) API kaynakları için üretilen iş (RU/s) işlemleri için Azure CLı betikleri
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 10/07/2020
ms.openlocfilehash: 2df31e5903785b6e25ea79a107a53084849c66fe
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789096"
---
# <a name="throughput-rus-operations-with-azure-cli-for-a-database-or-container-for-azure-cosmos-db-core-sql-api"></a>Azure Cosmos DB Core (SQL) API 'sine yönelik bir veritabanı veya kapsayıcı için Azure CLı ile üretilen iş (RU/s) işlemleri
[!INCLUDE[appliesto-sql-api](../../../includes/appliesto-sql-api.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- Bu makale, Azure CLı 'nin sürüm 2.12.1 veya üstünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür.

## <a name="sample-script"></a>Örnek betik

Bu betik, paylaşılan aktarım hızı ile bir çekirdek (SQL) API veritabanını ve adanmış işleme sahip bir çekirdek (SQL) API kapsayıcısını oluşturur, ardından hem veritabanı hem de kapsayıcı için aktarım hızını günceller. Komut dosyası daha sonra standart 'den otomatik ölçeklendirme aktarım hızına geçirilir ve sonra otomatik ölçeklendirme üretilen işinin değerini geçiş yapıldıktan sonra okur.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/sql/throughput.sh "Throughput operations for a SQL database and container.")]

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
| [az cosmosdb SQL veritabanı oluşturma](/cli/azure/cosmosdb/sql/database#az_cosmosdb_sql_database_create) | Azure Cosmos Core (SQL) veritabanı oluşturur. |
| [az cosmosdb SQL kapsayıcı oluşturma](/cli/azure/cosmosdb/sql/container#az_cosmosdb_sql_container_create) | Bir Azure Cosmos Core (SQL) kapsayıcısı oluşturur. |
| [az cosmosdb SQL veritabanı aktarım hızı güncelleştirmesi](/cli/azure/cosmosdb/sql/database/throughput#az_cosmosdb_sql_database_throughput_update) | Azure Cosmos Core (SQL) veritabanı için üretilen işi güncelleştirin. |
| [az cosmosdb SQL Container üretilen iş güncelleştirmesi](/cli/azure/cosmosdb/sql/container/throughput#az_cosmosdb_sql_container_throughput_update) | Azure Cosmos Core (SQL) kapsayıcısının aktarım hızını güncelleştirin. |
| [az cosmosdb SQL veritabanı aktarım hızı geçişi](/cli/azure/cosmosdb/sql/database/throughput#az_cosmosdb_sql_database_throughput_migrate) | Azure Cosmos Core (SQL) veritabanı için aktarım hızını geçirin. |
| [az cosmosdb SQL Container verimlilik geçişi](/cli/azure/cosmosdb/sql/container/throughput#az_cosmosdb_sql_container_throughput_migrate) | Azure Cosmos Core (SQL) kapsayıcısı için aktarım hızını geçirin. |
| [az group delete](/cli/azure/resource#az_resource_delete) | Bir kaynak grubunu tüm iç içe geçmiş kaynaklar dahil siler. |

## <a name="next-steps"></a>Sonraki adımlar

CLı Azure Cosmos DB hakkında daha fazla bilgi için bkz. [Azure Cosmos DB CLI belgeleri](/cli/azure/cosmosdb).

Tüm Azure Cosmos DB CLı betiği örnekleri [Azure Cosmos DB CLI GitHub deposunda](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)bulunabilir.
