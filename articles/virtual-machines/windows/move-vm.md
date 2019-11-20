---
title: Azure 'da bir Windows VM kaynağını taşıma
description: Bir Windows sanal makinesini Kaynak Yöneticisi dağıtım modelindeki başka bir Azure aboneliğine veya kaynak grubuna taşıyın.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 4e383427-4aff-4bf3-a0f4-dbff5c6f0c81
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 07/03/2019
ms.author: cynthn
ms.openlocfilehash: 9264e36922cb88c541ba9fb2fe54a9606f371b72
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033170"
---
# <a name="move-a-windows-vm-to-another-azure-subscription-or-resource-group"></a>Windows VM 'yi başka bir Azure aboneliğine veya kaynak grubuna taşıma
Bu makalede, bir Windows sanal makinesini (VM) kaynak grupları veya abonelikler arasında taşıma işlemi adım adım gösterilmektedir. Bir kişisel abonelikte ilk olarak bir VM oluşturduysanız ve bundan sonra işinize devam etmek için bunu şirketinizin aboneliğine taşımak istiyorsanız abonelikler arasında geçiş yapmak yararlı olabilir. Sanal makineyi taşımak için başlatmanız gerekmez ve taşıma sırasında çalışmaya devam etmelidir.

> [!IMPORTANT]
>Yeni kaynak kimlikleri taşımanın bir parçası olarak oluşturulur. VM taşındıktan sonra, yeni kaynak kimliklerini kullanmak için araçlarınızı ve betikleri güncelleştirmeniz gerekecektir. 
> 
> 

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="use-powershell-to-move-a-vm"></a>VM 'yi taşımak için PowerShell 'i kullanma

Bir sanal makineyi başka bir kaynak grubuna taşımak için tüm bağımlı kaynakları da taşıdığınızdan emin olmanız gerekir. Bu kaynakların her birine ait kaynak KIMLIĞI ile bir liste almak için [Get-AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource) cmdlet 'ini kullanın.

```azurepowershell-interactive
 Get-AzResource -ResourceGroupName <sourceResourceGroupName> | Format-list -wrap -Property ResourceId 
```

Bir önceki komutun çıkışını, her kaynağı hedefe taşımak için [Move-AzResource](https://docs.microsoft.com/powershell/module/az.resources/move-azresource) Için kaynak kimliklerinin virgülle ayrılmış bir listesi olarak kullanabilirsiniz. 

```azurepowershell-interactive
Move-AzResource -DestinationResourceGroupName "<myDestinationResourceGroup>" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```
    
Kaynakları farklı aboneliğe taşımak için **-destinationsubscriptionıd** parametresini ekleyin. 

```azurepowershell-interactive
Move-AzResource -DestinationSubscriptionId "<myDestinationSubscriptionID>" `
    -DestinationResourceGroupName "<myDestinationResourceGroup>" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```


Belirtilen kaynakları taşımak istediğinizi onaylamanız istendiğinde, onaylamak için **Y** yazın.

## <a name="next-steps"></a>Sonraki adımlar
Kaynak grupları ve abonelikler arasında birçok farklı kaynak türünü taşıyabilirsiniz. Daha fazla bilgi için bkz. [kaynakları yeni bir kaynak grubuna veya aboneliğe taşıma](../../resource-group-move-resources.md).    

