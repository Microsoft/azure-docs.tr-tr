---
title: Portalı veya PowerShell 'i kullanarak bir sanal sabit sürücünün anlık görüntüsünü oluşturma
description: Azure VM 'nin bir kopyasını yedekleme veya portal veya PowerShell kullanarak sorun giderme sorunları için nasıl oluşturacağınızı öğrenin.
author: roygara
manager: twooley
ms.service: virtual-machines
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 10/08/2018
ms.author: rogarana
ms.openlocfilehash: 9070b69ac4c6b85791ff3dd4662273e75a3cd22c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102556069"
---
# <a name="create-a-snapshot-using-the-portal-or-powershell"></a>Portal veya PowerShell kullanarak anlık görüntü oluşturma

Anlık görüntü, bir sanal sabit sürücünün (VHD) tam, salt okunurdur bir kopyasıdır. Yedekleme olarak kullanmak veya sanal makine (VM) sorunlarını gidermek için bir işletim sistemi veya veri diski VHD 'sinin anlık görüntüsünü alabilirsiniz.

Yeni bir VM oluşturmak için anlık görüntüyü kullanacaksanız, devam eden işlemlerin tümünü temizlemek için anlık görüntüyü almadan önce VM 'yi düzgün bir şekilde kapatmanız önerilir.

## <a name="use-the-azure-portal"></a>Azure portalını kullanma 

Anlık görüntü oluşturmak için aşağıdaki adımları izleyin: 
1.  [Azure Portal](https://portal.azure.com) **kaynak oluştur**' u seçin.
2. **Anlık görüntü** arayın ve seçin.
3. **Anlık görüntü** penceresinde **Oluştur**' u seçin. **Anlık görüntü oluştur** penceresi görüntülenir.
4. Anlık görüntü için bir **ad** girin.
5. Mevcut bir kaynak grubunu seçin veya yeni bir [kaynak grubu](../../azure-resource-manager/management/overview.md#resource-groups) adı girin. 
6. Azure veri merkezi **Konumu** seçin.  
7. **Kaynak disk** için, anlık görüntü yapılacak yönetilen diski seçin.
8. Anlık görüntüyü depolamak için kullanılacak **hesap türünü** seçin. Anlık görüntünün yüksek performanslı bir diskte depolanması gerekmiyorsa **Standard_HDD**' yi seçin.
9. **Oluştur**’u seçin.

## <a name="use-powershell"></a>PowerShell kullanma

Aşağıdaki adımlarda, VHD diskinin nasıl kopyalanacağı ve anlık görüntü yapılandırmasının nasıl oluşturulacağı gösterilmektedir. Ardından [New-AzSnapshot](/powershell/module/az.compute/new-azsnapshot) cmdlet 'ini kullanarak diskin anlık görüntüsünü alabilirsiniz. 

 

1. Bazı parametreleri ayarla: 

   ```azurepowershell-interactive
   $resourceGroupName = 'myResourceGroup' 
   $location = 'eastus' 
   $vmName = 'myVM'
   $snapshotName = 'mySnapshot'  
   ```

2. VM 'yi al:

   ```azurepowershell-interactive
   $vm = Get-AzVM `
       -ResourceGroupName $resourceGroupName `
       -Name $vmName
   ```

3. Anlık görüntü yapılandırmasını oluşturun. Bu örnekte, anlık görüntü işletim sistemi diski olur:

   ```azurepowershell-interactive
   $snapshot =  New-AzSnapshotConfig `
       -SourceUri $vm.StorageProfile.OsDisk.ManagedDisk.Id `
       -Location $location `
       -CreateOption copy
   ```
   
   > [!NOTE]
   > Anlık görüntüsünü bölge dayanıklı depolamada depolamak istiyorsanız, [kullanılabilirlik alanlarını](../../availability-zones/az-overview.md) destekleyen bir bölgede oluşturun ve `-SkuName Standard_ZRS` parametresini ekleyin.   
   
4. Anlık görüntüyü al:

   ```azurepowershell-interactive
   New-AzSnapshot `
       -Snapshot $snapshot `
       -SnapshotName $snapshotName `
       -ResourceGroupName $resourceGroupName 
   ```


## <a name="next-steps"></a>Sonraki adımlar

Bir anlık görüntüden yönetilen disk oluşturarak ve ardından yeni yönetilen diski işletim sistemi diski olarak ekleyerek bir anlık görüntüden sanal makine oluşturun. Daha fazla bilgi için, [PowerShell ile anlık GÖRÜNTÜDEN VM oluşturma](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot)' daki örneğe bakın.