---
title: Azure 'da bir VHD 'nin anlık görüntüsünü oluşturma
description: Bir Azure sanal makinesinin yedekleme veya sorun giderme sorunları için kullanılacak bir kopyasını oluşturmayı öğrenin.
documentationcenter: ''
author: roygara
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: 15eb778e-fc07-45ef-bdc8-9090193a6d20
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 10/08/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: b564e20ca8aa5acd7fbd4ea69ac2b1cd72e66d5e
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075331"
---
# <a name="create-a-snapshot"></a>Anlık görüntü oluşturma

Anlık görüntü, bir sanal sabit sürücünün (VHD) tam, salt okunurdur bir kopyasıdır. Yedekleme olarak kullanmak veya sanal makine (VM) sorunlarını gidermek için bir işletim sistemi veya veri diski VHD 'sinin anlık görüntüsünü alabilirsiniz.

Yeni bir VM oluşturmak için anlık görüntüyü kullanacaksanız, devam eden işlemlerin tümünü temizlemek için anlık görüntüyü almadan önce VM 'yi düzgün bir şekilde kapatmanız önerilir.

## <a name="use-the-azure-portal"></a>Azure portalı kullanma 

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. Sol menüden **kaynak oluştur**' u seçin ve ardından **anlık görüntü**' i arayıp seçin.
3. **Anlık görüntü** penceresinde **Oluştur**' u seçin. **Anlık görüntü oluştur** penceresi görüntülenir.
4. Anlık görüntü için bir **ad** girin.
5. Mevcut bir kaynak grubunu seçin veya yeni bir [kaynak grubu](../../azure-resource-manager/resource-group-overview.md#resource-groups) adı girin. 
6. Azure veri merkezi **Konumu** seçin.  
7. **Kaynak disk**için, anlık görüntü yapılacak yönetilen diski seçin.
8. Anlık görüntüyü depolamak için kullanılacak **hesap türünü** seçin. Anlık görüntünün yüksek performanslı bir diskte depolanması gerekmiyorsa **Standard_HDD**' yi seçin.
9. **Oluştur**'u seçin.

## <a name="use-powershell"></a>PowerShell kullanma

Aşağıdaki adımlarda, [New-AzSnapshot](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshot) cmdlet 'INI kullanarak VHD diskinin nasıl kopyalanacağı, anlık görüntü yapılandırması oluşturulması ve diskin anlık görüntüsünü alma işlemleri gösterilmektedir. 

 

1. Bazı parametreleri ayarla: 

   ```azurepowershell-interactive
   $resourceGroupName = 'myResourceGroup' 
   $location = 'eastus' 
   $vmName = 'myVM'
   $snapshotName = 'mySnapshot'  
   ```

2. VM 'yi al:

   ```azurepowershell-interactive
   $vm = get-azvm `
   -ResourceGroupName $resourceGroupName 
   -Name $vmName
   ```

3. Anlık görüntü yapılandırmasını oluşturun. Bu örnekte, anlık görüntü işletim sistemi diski olur:

   ```azurepowershell-interactive
   $snapshot =  New-AzSnapshotConfig 
   -SourceUri $vm.StorageProfile.OsDisk.ManagedDisk.Id 
   -Location $location 
   -CreateOption copy
   ```
   
   > [!NOTE]
   > Anlık görüntüsünü bölge dayanıklı depolamada depolamak isterseniz, [kullanılabilirlik alanlarını](../../availability-zones/az-overview.md) destekleyen bir bölgede oluşturun ve `-SkuName Standard_ZRS` parametresini ekleyin.   
   
4. Anlık görüntüyü al:

   ```azurepowershell-interactive
   New-AzSnapshot 
   -Snapshot $snapshot 
   -SnapshotName $snapshotName 
   -ResourceGroupName $resourceGroupName 
   ```


## <a name="next-steps"></a>Sonraki adımlar

Bir anlık görüntüden yönetilen disk oluşturarak ve ardından yeni yönetilen diski işletim sistemi diski olarak ekleyerek bir anlık görüntüden sanal makine oluşturun. Daha fazla bilgi için, [PowerShell ile anlık GÖRÜNTÜDEN VM oluşturma](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json)' daki örneğe bakın.
