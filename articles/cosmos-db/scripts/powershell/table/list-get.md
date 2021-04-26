---
title: Azure Cosmos DB Tablo API'si işlemlerini listelemek ve almak için PowerShell betiği
description: Azure PowerShell betiği Azure Cosmos DB listesi ve Tablo API'si için işlemleri al
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: sample
ms.date: 07/31/2020
ms.author: mjbrown
ms.openlocfilehash: 6385a28ff976b206839a86d94f1ea61a9c6f11f1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98680223"
---
# <a name="list-and-get-tables-for-azure-cosmos-db---table-api"></a>Azure Cosmos DB Tablo API'si için tabloları listeleme ve edinme
[!INCLUDE[appliesto-table-api](../../../includes/appliesto-table-api.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

Bu örnek Azure PowerShell az 5.4.0 veya üstünü gerektirir. `Get-Module -ListAvailable Az`Hangi sürümlerin yüklü olduğunu görmek için ' i çalıştırın.
Yüklemeniz gerekiyorsa bkz. [ınstall Azure PowerShell Module](/powershell/azure/install-az-ps).

Azure 'da oturum açmak için [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) komutunu çalıştırın.

## <a name="sample-script"></a>Örnek betik

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/table/ps-table-list-get.ps1 "List or get tables for Table API")]

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
| [Get-AzCosmosDBAccount](/powershell/module/az.cosmosdb/get-azcosmosdbaccount) | Cosmos DB hesaplarını listeler veya belirtilen bir Cosmos DB hesabı alır. |
| [Get-AzCosmosDBTable](/powershell/module/az.cosmosdb/get-azcosmosdbtable) | Bir hesaptaki Tablo API'si tabloları listeler veya bir hesapta belirtilen Tablo API'si tabloyu alır. |
|**Azure Kaynak grupları**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Bir kaynak grubunu tüm iç içe geçmiş kaynaklar dahil siler. |
|||

## <a name="next-steps"></a>Sonraki adımlar

Azure PowerShell hakkında daha fazla bilgi için bkz. [Azure PowerShell belgeleri](/powershell/).