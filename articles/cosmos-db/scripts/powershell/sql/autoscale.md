---
title: Otomatik ölçeklendirme ile Azure Cosmos DB SQL API veritabanı ve kapsayıcısı oluşturmak için PowerShell betiği
description: Azure PowerShell betiği-Azure Cosmos DB otomatik ölçeklendirme ile SQL API veritabanı ve kapsayıcısı oluşturma
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 07/30/2020
ms.author: mjbrown
ms.openlocfilehash: 42407f3ebb0b7f3a726c0031a0af5effc338c880
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98678045"
---
# <a name="create-a-database-and-container-with-autoscale-for-azure-cosmos-db---sql-api"></a>Azure Cosmos DB-SQL API için otomatik ölçeklendirme ile veritabanı ve kapsayıcı oluşturma
[!INCLUDE[appliesto-sql-api](../../../includes/appliesto-sql-api.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

Bu örnek Azure PowerShell az 5.4.0 veya üstünü gerektirir. `Get-Module -ListAvailable Az`Hangi sürümlerin yüklü olduğunu görmek için ' i çalıştırın.
Yüklemeniz gerekiyorsa bkz. [ınstall Azure PowerShell Module](/powershell/azure/install-az-ps).

Azure 'da oturum açmak için [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) komutunu çalıştırın.

## <a name="sample-script"></a>Örnek betik

Bu betik, oturum düzeyi tutarlılığı, bir veritabanı ve otomatik ölçeklendirme üretilen iş ve varsayılan dizin ilkesiyle bir kapsayıcı olan iki bölgede çekirdek (SQL) API 'SI için Cosmos hesabı oluşturur.

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/sql/ps-sql-autoscale.ps1 "Create an account, database, and container with autoscale for Core (SQL) API")]

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
| [New-AzCosmosDBAccount](/powershell/module/az.cosmosdb/new-azcosmosdbaccount) | Cosmos DB hesabı oluşturur. |
| [New-AzCosmosDBSqlDatabase](/powershell/module/az.cosmosdb/new-azcosmosdbsqldatabase) | Cosmos DB bir SQL veritabanı oluşturur. |
| [New-AzCosmosDBSqlContainer](/powershell/module/az.cosmosdb/new-azcosmosdbsqlcontainer) | Yeni bir Cosmos DB SQL kapsayıcısı oluşturur. |
|**Azure Kaynak grupları**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Bir kaynak grubunu tüm iç içe geçmiş kaynaklar dahil siler. |
|||

## <a name="next-steps"></a>Sonraki adımlar

Azure PowerShell hakkında daha fazla bilgi için bkz. [Azure PowerShell belgeleri](/powershell/).
