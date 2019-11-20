---
title: Azure PowerShell betiği-yük devretme önceliğini değiştirme veya bir Azure Cosmos hesabı için yük devretmeyi tetikleme
description: Azure PowerShell betik örneği-Azure Cosmos hesabı için yük devretme önceliğini değiştirme veya yük devretmeyi tetikleme
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 09/20/2019
ms.author: mjbrown
ms.openlocfilehash: e4406124a7ea4eac213d830d0e5960e76fb6d364
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/20/2019
ms.locfileid: "71155397"
---
# <a name="change-failover-priority-or-trigger-failover-for-an-azure-cosmos-account-using-powershell"></a>PowerShell kullanarak bir Azure Cosmos hesabı için yük devretme önceliğini değiştirme veya yük devretmeyi tetikleme

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Örnek betik

> [!NOTE]
> Bir bölgedeki `failoverPriority=0` herhangi bir değişiklik, el ile yük devretme tetikler ve yalnızca el ile yük devretme için yapılandırılmış bir hesaba yapılabilir. Diğer tüm bölgelerde yapılan değişiklikler yalnızca Cosmos hesabının yük devretme önceliğini değiştirir.
> [!NOTE]
> Bu örnekte bir SQL (Core) API hesabı kullanılması gösterilmektedir. Bu örneği diğer API 'Ler için kullanmak üzere ilgili özellikleri kopyalayın ve API 'ye özel betiğe uygulayın

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/common/ps-account-failover-priority-update.ps1 "Update failover priority for an Azure Cosmos account or trigger a manual failover")]

## <a name="clean-up-deployment"></a>Dağıtımı temizleme

Betik örneği çalıştırıldıktan sonra, kaynak grubunu ve onunla ilişkili tüm kaynakları kaldırmak için aşağıdaki komut kullanılabilir.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Betik açıklaması

Bu betik aşağıdaki komutları kullanır. Tablodaki her komut, komuta özgü belgelere yönlendirir.

| Komut | Notlar |
|---|---|
|**Azure kaynakları**| |
| [Invoke-AzResourceAction](https://docs.microsoft.com/powershell/module/az.resources/invoke-azresourceaction) | Kaynak üzerinde bir eylemi çağırır. |
|**Azure Kaynak grupları**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Bir kaynak grubunu tüm iç içe geçmiş kaynaklar dahil siler. |
|||

## <a name="next-steps"></a>Sonraki adımlar

Azure PowerShell hakkında daha fazla bilgi için bkz. [Azure PowerShell belgeleri](https://docs.microsoft.com/powershell/).

Ek Azure Cosmos DB PowerShell betiği örnekleri, [Azure Cosmos DB PowerShell betiklerinde](../../../powershell-samples.md) bulunabilir.
