---
title: Azure Cosmos DB SQL API veritabanı veya kapsayıcısı için üretilen iş (RU/s) işlemleri için PowerShell betikleri
description: Azure Cosmos DB SQL API veritabanı veya kapsayıcısı için üretilen iş (RU/s) işlemleri için PowerShell betikleri
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 10/07/2020
ms.author: mjbrown
ms.openlocfilehash: 73fdfa06fab42a5687e3aa354e3ba92937b92605
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98684300"
---
# <a name="throughput-rus-operations-with-powershell-for-a-database-or-container-for-azure-cosmos-db-core-sql-api"></a>Azure Cosmos DB Core (SQL) API 'SI için bir veritabanı veya kapsayıcı için PowerShell ile işleme (RU/s) işlemleri
[!INCLUDE[appliesto-sql-api](../../../includes/appliesto-sql-api.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

Bu örnek Azure PowerShell az 5.4.0 veya üstünü gerektirir. `Get-Module -ListAvailable Az`Hangi sürümlerin yüklü olduğunu görmek için ' i çalıştırın.
Yüklemeniz gerekiyorsa bkz. [ınstall Azure PowerShell Module](/powershell/azure/install-az-ps).

Azure 'da oturum açmak için [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) komutunu çalıştırın.

## <a name="get-throughput"></a>Aktarım hızı alma

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/sql/ps-sql-ru-get.ps1 "Get throughput (RU/s) for Azure Cosmos DB Core (SQL) API database or container")]

## <a name="update-throughput"></a>Üretimi Güncelleştir

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/sql/ps-sql-ru-update.ps1 "Update throughput (RU/s) for an Azure Cosmos DB Core (SQL) API database or container")]

## <a name="migrate-throughput"></a>Aktarım hızını geçir

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/sql/ps-sql-ru-migrate.ps1 "Migrate between standard and autoscale throughput on an Azure Cosmos DB Core (SQL) API database or container")]

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
| [Get-Azcosmosdbsqldatabaseüretilen Işi](/powershell/module/az.cosmosdb/get-azcosmosdbsqldatabasethroughput) | Azure Cosmos DB Çekirdek (SQL) API veritabanında sağlanan aktarım hızını alır. |
| [Get-Azcosmosdbsqlcontainerüretilen Işi](/powershell/module/az.cosmosdb/get-azcosmosdbsqlcontainerthroughput) | Azure Cosmos DB Çekirdek (SQL) API kapsayıcısı üzerinde sağlanan aktarım hızını alın. |
| [Update-Azcosmosdbsqldatabaseverimini](/powershell/module/az.cosmosdb/get-azcosmosdbsqldatabase) | Azure Cosmos DB Core (SQL) API veritabanının üretilen iş değerini güncelleştirir. |
| [Update-Azcosmosdbsqlcontainerverimini](/powershell/module/az.cosmosdb/get-azcosmosdbsqlcontainer) | Azure Cosmos DB Core (SQL) API kapsayıcısının üretilen iş değerini güncelleştirir. |
| [Invoke-AzCosmosDBSqlDatabaseThroughputMigration](/powershell/module/az.cosmosdb/invoke-azcosmosdbsqldatabasethroughputmigration) | Azure Cosmos DB Core (SQL) API veritabanının verimini geçirin. |
| [Invoke-AzCosmosDBSqlContainerThroughputMigration](/powershell/module/az.cosmosdb/invoke-azcosmosdbsqlcontainerthroughputmigration) | Azure Cosmos DB Core (SQL) API kapsayıcısının aktarım hızını geçirin. |
|**Azure Kaynak grupları**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Bir kaynak grubunu tüm iç içe geçmiş kaynaklar dahil siler. |
|||

## <a name="next-steps"></a>Sonraki adımlar

Azure PowerShell hakkında daha fazla bilgi için bkz. [Azure PowerShell belgeleri](/powershell/).