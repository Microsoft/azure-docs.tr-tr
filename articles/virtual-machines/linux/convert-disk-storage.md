---
title: Azure CLı kullanarak farklı disk türleri arasında yönetilen diskler depolamayı dönüştürme
description: Azure CLı kullanarak Azure yönetilen diskleri farklı diskler türleri arasında dönüştürme.
author: roygara
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 02/13/2021
ms.author: albecker
ms.subservice: disks
ms.openlocfilehash: 35ba681ae35e60d7cd275c9e649b2463d2dc30a0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102558466"
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-or-premium-to-standard"></a>Azure yönetilen diskler depolamasını standart 'ten Premium veya Premium 'a standart olarak dönüştürme

Azure tarafından yönetilen disklerin dört disk türü vardır: Azure Ultra diskler, Premium SSD, standart SSD ve standart HDD. Premium SSD, standart SSD ve standart HDD arasında performans gereksinimlerinize göre geçiş yapabilirsiniz. Ya da bir ultra diske geçiş yapamazsınız, yeni bir tane dağıtmanız gerekir.

Bu işlev, yönetilmeyen diskler için desteklenmez. Ancak, disk türleri arasında geçiş yapabilmesi için, [yönetilmeyen bir diski yönetilen bir diske kolayca dönüştürebilirsiniz](convert-unmanaged-to-managed-disks.md) .

Bu makalede, Azure CLı kullanılarak yönetilen disklerin bir disk türünden diğerine nasıl dönüştürüleceği gösterilmektedir. Aracı yüklemek veya yükseltmek için bkz. [Azure CLI 'Yı yüklemek](/cli/azure/install-azure-cli).

## <a name="before-you-begin"></a>Başlamadan önce

* Disk dönüştürme, sanal makinenin (VM) yeniden başlatılmasını gerektirir, bu nedenle önceden var olan bir bakım penceresi sırasında disk depolamanın geçişini zamanlayın.
* Yönetilmeyen diskler için, depolama seçenekleri arasında geçiş yapabilmeniz için önce [yönetilen disklere dönüştürmeniz](convert-unmanaged-to-managed-disks.md) gerekir.


## <a name="switch-all-managed-disks-of-a-vm-between-from-one-account-to-another"></a>Bir VM 'nin tüm yönetilen disklerini bir hesaptan diğerine değiştirme

Bu örnek, bir sanal makinenin disklerinin tümünün Premium depolamaya nasıl dönüştürüleceğini gösterir. Ancak, bu örnekteki SKU değişkenini değiştirerek, sanal makinenin disk türünü standart SSD veya standart HDD 'ye dönüştürebilirsiniz. Premium yönetilen diskleri kullanmak Için, sanal makinenizin Premium depolamayı destekleyen bir [VM boyutu](../sizes.md) kullanması gerektiğini lütfen unutmayın. Bu örnek ayrıca Premium depolamayı destekleyen bir boyuta geçiş yapar.

 ```azurecli

#resource group that contains the virtual machine
rgName='yourResourceGroup'

#Name of the virtual machine
vmName='yourVM'

#Premium capable size 
#Required only if converting from Standard to Premium
size='Standard_DS2_v2'

#Choose between Standard_LRS, StandardSSD_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Deallocate the VM before changing the size of the VM
az vm deallocate --name $vmName --resource-group $rgName

#Change the VM size to a size that supports Premium storage 
#Skip this step if converting storage from Premium to Standard
az vm resize --resource-group $rgName --name $vmName --size $size

#Update the SKU of all the data disks 
az vm show -n $vmName -g $rgName --query storageProfile.dataDisks[*].managedDisk -o tsv \
 | awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

#Update the SKU of the OS disk
az vm show -n $vmName -g $rgName --query storageProfile.osDisk.managedDisk -o tsv \
| awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

az vm start --name $vmName --resource-group $rgName

```
## <a name="switch-individual-managed-disks-from-one-disk-type-to-another"></a>Ayrı ayrı yönetilen diskleri bir disk türünden diğerine değiştirme

Geliştirme ve test iş yükünüz için, maliyetlerinizi azaltmak üzere standart ve Premium disklerin bir karışımını elde etmek isteyebilirsiniz. Yalnızca daha iyi performansa ihtiyacı olan diskleri yükseltmeyi tercih edebilirsiniz. Bu örnekte, tek bir VM diskinin standart 'dan Premium depolamaya nasıl dönüştürüleceği gösterilmektedir. Ancak, bu örnekteki SKU değişkenini değiştirerek, sanal makinenin disk türünü standart SSD veya standart HDD 'ye dönüştürebilirsiniz. Premium yönetilen diskleri kullanmak için, sanal makinenizin Premium depolamayı destekleyen bir [VM boyutu](../sizes.md) kullanması gerekir. Bu örnek ayrıca Premium depolamayı destekleyen bir boyuta geçiş yapar.

 ```azurecli

#resource group that contains the managed disk
rgName='yourResourceGroup'

#Name of your managed disk
diskName='yourManagedDiskName'

#Premium capable size 
#Required only if converting from Standard to Premium
size='Standard_DS2_v2'

#Choose between Standard_LRS, StandardSSD_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Get the parent VM Id 
vmId=$(az disk show --name $diskName --resource-group $rgName --query managedBy --output tsv)

#Deallocate the VM before changing the size of the VM
az vm deallocate --ids $vmId 

#Change the VM size to a size that supports Premium storage 
#Skip this step if converting storage from Premium to Standard
az vm resize --ids $vmId --size $size

# Update the SKU
az disk update --sku $sku --name $diskName --resource-group $rgName 

az vm start --ids $vmId 
```

## <a name="switch-managed-disks-from-one-disk-type-to-another"></a>Yönetilen diskleri bir disk türünden diğerine değiştirme

Şu adımları izleyin:

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. **Sanal makineler** listesinden VM 'yi seçin.
3. VM durdurulmamışsa, VM **'ye genel bakış** bölmesinin üst kısmında **Durdur** ' u seçin ve VM 'nin durdurulmasını bekleyin.
4. VM 'nin bölmesinde, menüden **diskler** ' i seçin.
5. Dönüştürmek istediğiniz diski seçin.
6. Menüden **yapılandırma** ' yı seçin.
7. **Hesap türünü** özgün disk türünden istenen disk türüne değiştirin.
8. **Kaydet**' i seçin ve disk bölmesini kapatın.

Disk türü güncelleştirmesi anında gerçekleşir. Dönüştürmeden sonra sanal makinenizin yeniden başlatılmasını sağlayabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

[Anlık görüntüler](snapshot-copy-managed-disk.md)kullanarak VM 'nin salt okunurdur bir kopyasını oluşturun.