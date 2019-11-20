---
title: Windows sanal makinesini yönetilmeyen disklerden yönetilen disklere dönüştürme-Azure yönetilen diskler
description: Windows VM 'yi, Kaynak Yöneticisi dağıtım modelinde PowerShell kullanarak yönetilmeyen disklerden yönetilen disklere dönüştürme
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 07/12/2018
ms.author: rogarana
ms.openlocfilehash: 92f70cbfcf261c218ea43b159ed49126f7b2ecd5
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033908"
---
# <a name="convert-a-windows-virtual-machine-from-unmanaged-disks-to-managed-disks"></a>Windows sanal makinesini yönetilmeyen disklerden yönetilen disklere dönüştürme

Yönetilmeyen diskler kullanan mevcut Windows sanal makinelerinizi (VM) varsa, [Azure yönetilen diskler](managed-disks-overview.md) hizmeti aracılığıyla VM 'leri yönetilen diskleri kullanacak şekilde dönüştürebilirsiniz. Bu işlem hem işletim sistemi diskini hem de bağlı veri disklerini dönüştürür.

 

## <a name="before-you-begin"></a>Başlamadan önce


* [Yönetilen disklere geçiş planını](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks)gözden geçirin.

* [Yönetilen disklere geçiş hakkında SSS 'yi](faq-for-disks.md#migrate-to-managed-disks)gözden geçirin.

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]

* Özgün VHD’ler ve dönüştürme öncesinde VM tarafından kullanılan depolama hesabı silinmez. Ücretler uygulanmaya devam eder. Bunlar için ücret alınmasını önlemek istiyorsanız, dönüştürmenin tamamlandığını doğruladıktan sonra özgün VHD bloblarını silin. Bunları silmek için eklenmemiş olan diskleri bulmanız gerekiyorsa, [eklenmemiş Azure yönetilen ve yönetilmeyen disklerini bulma ve silme](find-unattached-disks.md)makalemize bakın.


## <a name="convert-single-instance-vms"></a>Tek örnekli VM 'Leri dönüştürme
Bu bölümde, tek örnekli Azure VM 'lerinin yönetilmeyen disklerden yönetilen disklere nasıl dönüştürüleceği ele alınmaktadır. (Sanal makinelerleriniz bir kullanılabilirlik kümesinde ise, sonraki bölüme bakın.) 

1. [Stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm) cmdlet 'ini kullanarak VM 'yi serbest bırakın. Aşağıdaki örnek, `myResourceGroup`adlı kaynak grubunda `myVM` adlı VM 'yi kaldırır: 

   ```azurepowershell-interactive
   $rgName = "myResourceGroup"
   $vmName = "myVM"
   Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
   ```

2. [ConvertTo-AzVMManagedDisk](https://docs.microsoft.com/powershell/module/az.compute/convertto-azvmmanageddisk) cmdlet 'INI kullanarak VM 'yi yönetilen disklere dönüştürün. Aşağıdaki işlem, işletim sistemi diski ve veri diskleri dahil olmak üzere önceki VM 'yi dönüştürür ve sanal makineyi başlatır:

   ```azurepowershell-interactive
   ConvertTo-AzVMManagedDisk -ResourceGroupName $rgName -VMName $vmName
   ```



## <a name="convert-vms-in-an-availability-set"></a>Kullanılabilirlik kümesindeki VM 'Leri dönüştürme

Yönetilen disklere dönüştürmek istediğiniz VM 'Ler bir kullanılabilirlik kümesinde ise, önce kullanılabilirlik kümesini yönetilen bir kullanılabilirlik kümesine dönüştürmeniz gerekir.

1. [Update-AzAvailabilitySet](https://docs.microsoft.com/powershell/module/az.compute/update-azavailabilityset) cmdlet 'ini kullanarak kullanılabilirlik kümesini dönüştürün. Aşağıdaki örnek, `myResourceGroup`adlı kaynak grubunda `myAvailabilitySet` adlı kullanılabilirlik kümesini güncelleştirir:

   ```azurepowershell-interactive
   $rgName = 'myResourceGroup'
   $avSetName = 'myAvailabilitySet'

   $avSet = Get-AzAvailabilitySet -ResourceGroupName $rgName -Name $avSetName
   Update-AzAvailabilitySet -AvailabilitySet $avSet -Sku Aligned 
   ```

   Kullanılabilirlik kümesi 'nin bulunduğu bölge yalnızca 2 yönetilen hata etki alanına sahipse, ancak yönetilmeyen hata etki alanlarının sayısı 3 ise, bu komut "belirtilen hata etki alanı sayısı 3 ' e kadar 2 ' ye denk gelmelidir" hatasıyla benzer bir hata gösterir. Hatayı gidermek için, hata etki alanını 2 ' ye güncelleştirin ve `Sku` `Aligned` aşağıdaki şekilde güncelleştirin:

   ```azurepowershell-interactive
   $avSet.PlatformFaultDomainCount = 2
   Update-AzAvailabilitySet -AvailabilitySet $avSet -Sku Aligned
   ```

2. Kullanılabilirlik kümesindeki VM 'Leri serbest bırakın ve dönüştürün. Aşağıdaki betik, [stop-azvm](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm) cmdlet 'ini kullanarak her VM 'yi ayırır, [ConvertTo-AzVMManagedDisk](https://docs.microsoft.com/powershell/module/az.compute/convertto-azvmmanageddisk)kullanarak dönüştürür ve dönüştürme işleminin ayrı olarak otomatik olarak yeniden başlatır:

   ```azurepowershell-interactive
   $avSet = Get-AzAvailabilitySet -ResourceGroupName $rgName -Name $avSetName

   foreach($vmInfo in $avSet.VirtualMachinesReferences)
   {
     $vm = Get-AzVM -ResourceGroupName $rgName | Where-Object {$_.Id -eq $vmInfo.id}
     Stop-AzVM -ResourceGroupName $rgName -Name $vm.Name -Force
     ConvertTo-AzVMManagedDisk -ResourceGroupName $rgName -VMName $vm.Name
   }
   ```


## <a name="troubleshooting"></a>Sorun giderme

Dönüştürme sırasında bir hata oluşursa veya bir VM önceki bir dönüşümdeki sorunlar nedeniyle başarısız durumdaysa, `ConvertTo-AzVMManagedDisk` cmdlet 'ini yeniden çalıştırın. Basit bir yeniden deneme genellikle durumun engellemesini kaldırır.
Dönüştürmeden önce, tüm VM uzantılarının ' sağlama başarılı ' durumunda olduğundan emin olun veya dönüştürme 409 hata koduyla başarısız olur.

## <a name="convert-using-the-azure-portal"></a>Azure portal kullanarak Dönüştür

Azure portal kullanarak, yönetilmeyen diskleri yönetilen disklere de dönüştürebilirsiniz.

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. Portaldaki VM 'Ler listesinden VM 'yi seçin.
3. VM 'nin dikey penceresinde, menüden **diskler** ' i seçin.
4. **Diskler** dikey penceresinin üst kısmında, **yönetilen disklere geçir**' i seçin.
5. VM 'niz bir kullanılabilirlik kümesi içinde ise, önce kullanılabilirlik kümesini dönüştürmeniz gereken **yönetilen disklere geçiş** dikey penceresinde bir uyarı olacaktır. Uyarı, kullanılabilirlik kümesini dönüştürmek için tıklabileceğiniz bir bağlantıya sahip olmalıdır. Kullanılabilirlik kümesi dönüştürüldükten veya VM 'niz bir kullanılabilirlik kümesinde değilse, disklerinizi yönetilen disklere geçirme işlemini başlatmak için **Taşı** ' ya tıklayın.

Geçiş tamamlandıktan sonra VM durdurulur ve yeniden başlatılır.

## <a name="next-steps"></a>Sonraki adımlar

[Standart yönetilen diskleri Premium 'a Dönüştür](convert-disk-storage.md)

[Anlık görüntüler](snapshot-copy-managed-disk.md)kullanarak VM 'nin salt okunurdur bir kopyasını alın.

