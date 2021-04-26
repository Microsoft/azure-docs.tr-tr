---
title: Azure yönetilen diskler için Paylaşılan diskleri etkinleştir
description: Birden çok VM arasında paylaşabileceğiniz bir Azure yönetilen diskini paylaşılan disklerle yapılandırın
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 09/30/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: e8bb97196fcceea0c86f58fe4a63146e681c427e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96500742"
---
# <a name="enable-shared-disk"></a>Paylaşılan diski etkinleştirme

Bu makalede, Azure yönetilen diskler için paylaşılan diskler özelliğinin nasıl etkinleştirileceği ele alınmaktadır. Azure paylaşılan diskler, yönetilen bir diski birden çok sanal makineye (VM) aynı anda iliştirmenizi sağlayan, Azure yönetilen disklere yönelik yeni bir özelliktir. Yönetilen bir diskin birden çok VM 'ye eklenmesi, yeni bir dağıtım veya mevcut kümelenmiş uygulamaları Azure 'a geçirmenize olanak sağlar. 

Paylaşılan diskler etkinleştirilmiş yönetilen diskler hakkında kavramsal bilgiler arıyorsanız bkz. [Azure paylaşılan diskler](disks-shared.md).

## <a name="limitations"></a>Sınırlamalar

[!INCLUDE [virtual-machines-disks-shared-limitations](../../includes/virtual-machines-disks-shared-limitations.md)]

## <a name="supported-operating-systems"></a>Desteklenen işletim sistemleri

Paylaşılan diskler çeşitli işletim sistemlerini destekler. Desteklenen işletim sistemleri için kavramsal makalenin [Windows](./disks-shared.md#windows) ve [Linux](./disks-shared.md#linux) bölümlerine bakın.

## <a name="disk-sizes"></a>Disk boyutları

[!INCLUDE [virtual-machines-disks-shared-sizes](../../includes/virtual-machines-disks-shared-sizes.md)]

## <a name="deploy-shared-disks"></a>Paylaşılan diskleri dağıtma

### <a name="deploy-a-premium-ssd-as-a-shared-disk"></a>Premium SSD 'yi paylaşılan disk olarak dağıtma

Paylaşılan disk özelliği etkinken yönetilen bir disk dağıtmak için yeni özelliği kullanın `maxShares` ve 1 ' den büyük bir değer tanımlayın. Bu, diski birden çok VM arasında paylaşılabilir hale getirir.

> [!IMPORTANT]
> Değeri `maxShares` yalnızca bir disk tüm VM 'lerden çıkarıldığınızda ayarlanabilir veya değiştirilebilir. İçin izin verilen değerler için [disk boyutlarına](#disk-sizes) bakın `maxShares` .

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az disk create -g myResourceGroup -n mySharedDisk --size-gb 1024 -l westcentralus --sku Premium_LRS --max-shares 2
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$dataDiskConfig = New-AzDiskConfig -Location 'WestCentralUS' -DiskSizeGB 1024 -AccountType Premium_LRS -CreateOption Empty -MaxSharesCount 2

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'mySharedDisk' -Disk $dataDiskConfig
```

# <a name="resource-manager-template"></a>[Kaynak Yöneticisi şablonu](#tab/azure-resource-manager)

Aşağıdaki şablonu kullanmadan önce,,, `[parameters('dataDiskName')]` `[resourceGroup().location]` `[parameters('dataDiskSizeGB')]` ve `[parameters('maxShares')]` değerlerini kendi değerlerinizle değiştirin.

[Premium SSD paylaşılan disk şablonu](https://aka.ms/SharedPremiumDiskARMtemplate)

---

### <a name="deploy-an-ultra-disk-as-a-shared-disk"></a>Bir ultra diski paylaşılan disk olarak dağıtma

Paylaşılan disk özelliği etkinken yönetilen bir disk dağıtmak için, `maxShares` parametreyi 1 ' den büyük bir değere değiştirin. Bu, diski birden çok VM arasında paylaşılabilir hale getirir.

> [!IMPORTANT]
> Değeri `maxShares` yalnızca bir disk tüm VM 'lerden çıkarıldığınızda ayarlanabilir veya değiştirilebilir. İçin izin verilen değerler için [disk boyutlarına](#disk-sizes) bakın `maxShares` .


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

##### <a name="regional-disk-example"></a>Bölgesel disk örneği

```azurecli
#Creating an Ultra shared Disk 
az disk create -g rg1 -n clidisk --size-gb 1024 -l westus --sku UltraSSD_LRS --max-shares 5 --disk-iops-read-write 2000 --disk-mbps-read-write 200 --disk-iops-read-only 100 --disk-mbps-read-only 1

#Updating an Ultra shared Disk 
az disk update -g rg1 -n clidisk --disk-iops-read-write 3000 --disk-mbps-read-write 300 --set diskIopsReadOnly=100 --set diskMbpsReadOnly=1

#Show shared disk properties:
az disk show -g rg1 -n clidisk
```

##### <a name="zonal-disk-example"></a>Zonal disk örneği

Bu örnek, kullanılabilirlik bölge 1 ' de bir disk oluşturmasının dışında, öncekiyle neredeyse aynıdır.

```azurecli
#Creating an Ultra shared Disk 
az disk create -g rg1 -n clidisk --size-gb 1024 -l westus --sku UltraSSD_LRS --max-shares 5 --disk-iops-read-write 2000 --disk-mbps-read-write 200 --disk-iops-read-only 100 --disk-mbps-read-only 1 --zone 1

#Updating an Ultra shared Disk 
az disk update -g rg1 -n clidisk --disk-iops-read-write 3000 --disk-mbps-read-write 300 --set diskIopsReadOnly=100 --set diskMbpsReadOnly=1

#Show shared disk properties:
az disk show -g rg1 -n clidisk
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

##### <a name="regional-disk-example"></a>Bölgesel disk örneği

```azurepowershell-interactive
$datadiskconfig = New-AzDiskConfig -Location 'WestCentralUS' -DiskSizeGB 1024 -AccountType UltraSSD_LRS -CreateOption Empty -DiskIOPSReadWrite 2000 -DiskMBpsReadWrite 200 -DiskIOPSReadOnly 100 -DiskMBpsReadOnly 1 -MaxSharesCount 5

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'mySharedDisk' -Disk $datadiskconfig
```

##### <a name="zonal-disk-example"></a>Zonal disk örneği

Bu örnek, kullanılabilirlik bölge 1 ' de bir disk oluşturmasının dışında, öncekiyle neredeyse aynıdır.

```azurepowershell-interactive
$datadiskconfig = New-AzDiskConfig -Location 'WestCentralUS' -DiskSizeGB 1024 -AccountType UltraSSD_LRS -CreateOption Empty -DiskIOPSReadWrite 2000 -DiskMBpsReadWrite 200 -DiskIOPSReadOnly 100 -DiskMBpsReadOnly 1 -MaxSharesCount 5 -Zone 1

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'mySharedDisk' -Disk $datadiskconfig
```

# <a name="resource-manager-template"></a>[Kaynak Yöneticisi şablonu](#tab/azure-resource-manager)

##### <a name="regional-disk-example"></a>Bölgesel disk örneği

Aşağıdaki şablonu kullanmadan önce,,,,,, `[parameters('dataDiskName')]` `[resourceGroup().location]` `[parameters('dataDiskSizeGB')]` `[parameters('maxShares')]` `[parameters('diskIOPSReadWrite')]` `[parameters('diskMBpsReadWrite')]` `[parameters('diskIOPSReadOnly')]` ve `[parameters('diskMBpsReadOnly')]` değerlerini kendi değerlerinizle değiştirin.

[Bölgesel paylaşılan Ultra diskler şablonu](https://aka.ms/SharedUltraDiskARMtemplateRegional)

##### <a name="zonal-disk-example"></a>Zonal disk örneği

Aşağıdaki şablonu kullanmadan önce,,,,,, `[parameters('dataDiskName')]` `[resourceGroup().location]` `[parameters('dataDiskSizeGB')]` `[parameters('maxShares')]` `[parameters('diskIOPSReadWrite')]` `[parameters('diskMBpsReadWrite')]` `[parameters('diskIOPSReadOnly')]` ve `[parameters('diskMBpsReadOnly')]` değerlerini kendi değerlerinizle değiştirin.

[Zikzak paylaşılan Ultra diskler şablonu](https://aka.ms/SharedUltraDiskARMtemplateZonal)

---

## <a name="using-azure-shared-disks-with-your-vms"></a>Sanal makinelerinizdeki Azure Paylaşılan disklerini kullanma

İle paylaşılan bir disk dağıttıktan sonra `maxShares>1` , diski sanal makinelerinizdeki bir veya daha fazlasına bağlayabilirsiniz.

> [!NOTE]
> Bir ultra disk dağıtıyorsanız, gerekli gereksinimlerle eşleştiğinden emin olun. Ayrıntılar için bkz. [Azure Ultra disk kullanımı](disks-enable-ultra-ssd.md) .

```azurepowershell-interactive

$resourceGroup = "myResourceGroup"
$location = "WestCentralUS"

$vm = New-AzVm -ResourceGroupName $resourceGroup -Name "myVM" -Location $location -VirtualNetworkName "myVnet" -SubnetName "mySubnet" -SecurityGroupName "myNetworkSecurityGroup" -PublicIpAddressName "myPublicIpAddress"

$dataDisk = Get-AzDisk -ResourceGroupName $resourceGroup -DiskName "mySharedDisk"

$vm = Add-AzVMDataDisk -VM $vm -Name "mySharedDisk" -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun 0

update-AzVm -VM $vm -ResourceGroupName $resourceGroup
```

## <a name="supported-scsi-pr-commands"></a>Desteklenen SCSI PR komutları

Paylaşılan diski kümenize sanal makinelerinize bağladıktan sonra, çekirdek oluşturabilir ve SCSI PR kullanarak diske okuma/yazma yapabilirsiniz. Azure paylaşılan diskler kullanılırken aşağıdaki PR komutları kullanılabilir:

Diskle etkileşimde bulunmak için kalıcı rezervasyon eylem listesiyle başlayın:

```
PR_REGISTER_KEY 

PR_REGISTER_AND_IGNORE 

PR_GET_CONFIGURATION 

PR_RESERVE 

PR_PREEMPT_RESERVATION 

PR_CLEAR_RESERVATION 

PR_RELEASE_RESERVATION 
```

PR_RESERVE, PR_PREEMPT_RESERVATION veya PR_RELEASE_RESERVATION kullanırken, şu kalıcı rezervasyon türünden birini sağlayın:

```
PR_NONE 

PR_WRITE_EXCLUSIVE 

PR_EXCLUSIVE_ACCESS 

PR_WRITE_EXCLUSIVE_REGISTRANTS_ONLY 

PR_EXCLUSIVE_ACCESS_REGISTRANTS_ONLY 

PR_WRITE_EXCLUSIVE_ALL_REGISTRANTS 

PR_EXCLUSIVE_ACCESS_ALL_REGISTRANTS 
```

PR_RESERVE, PR_REGISTER_AND_IGNORE, PR_REGISTER_KEY, PR_PREEMPT_RESERVATION, PR_CLEAR_RESERVATION veya PR_RELEASE AYıRMAYı kullanırken de kalıcı rezervasyon anahtarı sağlamanız gerekir.


## <a name="next-steps"></a>Sonraki adımlar

Disketinizi dağıtmak için Azure Resource Manager şablonlarını kullanmayı tercih ediyorsanız, aşağıdaki örnek şablonlar kullanılabilir:
- [Premium SSD](https://aka.ms/SharedPremiumDiskARMtemplate)
- [Bölgesel Ultra diskler](https://aka.ms/SharedUltraDiskARMtemplateRegional)
- [Zonal Ultra diskler](https://aka.ms/SharedUltraDiskARMtemplateZonal)