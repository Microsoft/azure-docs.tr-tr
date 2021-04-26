---
title: Azure Cosmos DB Gremlin API 'SI için üretilen iş (RU/s) işlemleri için PowerShell betikleri
description: Gremlin API 'SI için üretilen iş (RU/s) işlemleri için PowerShell betikleri
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: sample
ms.date: 10/07/2020
ms.author: mjbrown
ms.openlocfilehash: 50f3f05bd06101caa096b8126225c7b49ef1f3fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98675513"
---
# <a name="throughput-rus-operations-with-powershell-for-a-database-or-graph-for-azure-cosmos-db---gremlin-api"></a>Azure Cosmos DB-Gremlin API 'sine yönelik bir veritabanı veya grafik için PowerShell ile işleme (RU/s) işlemleri
[!INCLUDE[appliesto-gremlin-api](../../../includes/appliesto-gremlin-api.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

Bu örnek Azure PowerShell az 5.4.0 veya üstünü gerektirir. `Get-Module -ListAvailable Az`Hangi sürümlerin yüklü olduğunu görmek için ' i çalıştırın.
Yüklemeniz gerekiyorsa bkz. [ınstall Azure PowerShell Module](/powershell/azure/install-az-ps).

Azure 'da oturum açmak için [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) komutunu çalıştırın.

## <a name="get-throughput"></a>Aktarım hızı alma

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/gremlin/ps-gremlin-ru-get.ps1 "Get throughput on a database or graph for Gremlin API")]

## <a name="update-throughput"></a>Üretimi Güncelleştir

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/gremlin/ps-gremlin-ru-update.ps1 "Update throughput on a database or graph for Gremlin API")]

## <a name="migrate-throughput"></a>Aktarım hızını geçir

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/gremlin/ps-gremlin-ru-migrate.ps1 "Migrate between standard and autoscale throughput on a database or graph for Gremlin API")]

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
| [Get-Azcosmosdbgremlindatabaseüretilen Iş](/powershell/module/az.cosmosdb/get-azcosmosdbgremlindatabasethroughput) | Gremlin API veritabanının üretilen iş değerini alır. |
| [Get-Azcosmosdbgremlingraphüretilen Iş](/powershell/module/az.cosmosdb/get-azcosmosdbgremlingraphthroughput) | Gremlin API grafiğinin üretilen iş değerini alır. |
| [Update-Azcosmosdbgremlindatabaseüretilen Iş](/powershell/module/az.cosmosdb/update-azcosmosdbgremlindatabasethroughput) | Gremlin API veritabanının üretilen iş değerini güncelleştirir. |
| [Update-Azcosmosdbgremlingraphverimini](/powershell/module/az.cosmosdb/update-azcosmosdbgremlingraphthroughput) | Gremlin API grafiğinin üretilen iş değerini güncelleştirir. |
| [Invoke-AzCosmosDBGremlinDatabaseThroughputMigration](/powershell/module/az.cosmosdb/invoke-azcosmosdbgremlindatabasethroughputmigration) | Gremlin API veritabanının aktarım hızını geçirin. |
| [Invoke-AzCosmosDBGremlinGraphThroughputMigration](/powershell/module/az.cosmosdb/invoke-azcosmosdbgremlingraphthroughputmigration) | Gremlin API grafiğinin aktarım hızını geçirin. |
|**Azure Kaynak grupları**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Bir kaynak grubunu tüm iç içe geçmiş kaynaklar dahil siler. |
|||

## <a name="next-steps"></a>Sonraki adımlar

Azure PowerShell hakkında daha fazla bilgi için bkz. [Azure PowerShell belgeleri](/powershell/).