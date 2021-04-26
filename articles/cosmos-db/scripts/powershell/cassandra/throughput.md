---
title: Azure Cosmos DB Cassandra API kaynakları için üretilen iş (RU/s) işlemleri için PowerShell betikleri
description: Azure Cosmos DB Cassandra API kaynakları için üretilen iş (RU/s) işlemleri için PowerShell betikleri
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: sample
ms.date: 10/07/2020
ms.author: mjbrown
ms.openlocfilehash: 72c35cba7c058928a7b087dd5f26f741e73a202c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98684398"
---
# <a name="throughput-rus-operations-with-powershell-for-a-keyspace-or-table-for-azure-cosmos-db---cassandra-api"></a>Azure Cosmos DB için anahtar uzayı veya tablo için PowerShell ile işleme (RU/s) işlemleri Cassandra API
[!INCLUDE[appliesto-cassandra-api](../../../includes/appliesto-cassandra-api.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

Bu örnek Azure PowerShell az 5.4.0 veya üstünü gerektirir. `Get-Module -ListAvailable Az`Hangi sürümlerin yüklü olduğunu görmek için ' i çalıştırın.
Yüklemeniz gerekiyorsa bkz. [ınstall Azure PowerShell Module](/powershell/azure/install-az-ps).

Azure 'da oturum açmak için [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) komutunu çalıştırın.

## <a name="get-throughput"></a>Aktarım hızı alma

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/cassandra/ps-cassandra-ru-get.ps1 "Get throughput on a keyspace or table for Cassandra API")]

## <a name="update-throughput"></a>Üretimi Güncelleştir

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/cassandra/ps-cassandra-ru-update.ps1 "Update throughput on a keyspace or table for Cassandra API")]

## <a name="migrate-throughput"></a>Aktarım hızını geçir

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/cassandra/ps-cassandra-ru-migrate.ps1 "Migrate between standard and autoscale throughput on a keyspace or table for Cassandra API")]

## <a name="clean-up-deployment"></a>Dağıtımı temizleme

Betik örneği çalıştırıldıktan sonra, kaynak grubunu ve onunla ilişkili tüm kaynakları kaldırmak için aşağıdaki komut kullanılabilir.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Betik açıklaması

Bu betik aşağıdaki komutları kullanır. Tablodaki her komut, komuta özgü belgelere yönlendirir.

| Komut | Notlar |
|---|---|
|**Azure Cosmos DB**| |
| [Get-Azcosmosdbcassandrakeyspaceverimini](/powershell/module/az.cosmosdb/get-azcosmosdbcassandrakeyspacethroughput) | Cassandra API keyspace 'in üretilen iş değerini alır. |
| [Get-Azcosmosdbcassandratableverimini](/powershell/module/az.cosmosdb/get-azcosmosdbcassandratablethroughput) | Cassandra API tablosunun üretilen iş değerini alır. |
| [Update-Azcosmosdbcassandrakeyspaceverimini](/powershell/module/az.cosmosdb/update-azcosmosdbcassandrakeyspacethroughput) | Cassandra API keyspace 'in üretilen iş değerini güncelleştirir. |
| [Update-Azcosmosdbcassandratableverimini](/powershell/module/az.cosmosdb/update-azcosmosdbcassandratablethroughput) | Cassandra API tablosunun üretilen iş değerini güncelleştirir. |
| [Invoke-AzCosmosDBCassandraKeyspaceThroughputMigration](/powershell/module/az.cosmosdb/invoke-azcosmosdbcassandrakeyspacethroughputmigration) | Cassandra API bir keyspace için aktarım hızını geçirin. |
| [Invoke-AzCosmosDBCassandraTableThroughputMigration](/powershell/module/az.cosmosdb/invoke-azcosmosdbcassandratablethroughputmigration) | Cassandra API tablosu için aktarım hızını geçirin. |
|**Azure Kaynak grupları**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Bir kaynak grubunu tüm iç içe geçmiş kaynaklar dahil siler. |
|||

## <a name="next-steps"></a>Sonraki adımlar

Azure PowerShell hakkında daha fazla bilgi için bkz. [Azure PowerShell belgeleri](/powershell/).