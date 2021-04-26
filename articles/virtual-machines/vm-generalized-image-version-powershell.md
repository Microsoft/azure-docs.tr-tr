---
title: Genelleştirilmiş bir görüntüden VM oluşturma
description: Paylaşılan görüntü galerisinde Genelleştirilmiş görüntü kullanarak bir VM oluşturun.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: dbe1e31a9b56f2f60edbebd8286bf0bfdeaa327e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102560982"
---
# <a name="create-a-vm-using-a-generalized-image"></a>Genelleştirilmiş görüntü kullanarak VM oluşturma 

Paylaşılan görüntü galerisinde depolanan Genelleştirilmiş görüntüden bir VM oluşturun. Özel bir görüntü kullanarak bir VM oluşturmak istiyorsanız, bkz. [özel bir GÖRÜNTÜDEN VM oluşturma](vm-specialized-image-version-powershell.md).

Genelleştirilmiş görüntü sürümüne sahip olduktan sonra bir veya daha fazla yeni VM oluşturabilirsiniz. [New-AzVM](/powershell/module/az.compute/new-azvm) cmdlet 'ini kullanma. 

Bu örnekte, yeni VM 'nizin bir görüntünün en son sürümünü kullanmasını sağlamak için görüntü tanımı KIMLIĞI kullanıyoruz. Ayrıca, için görüntü sürümü KIMLIĞINI kullanarak belirli bir sürümü kullanabilirsiniz `Set-AzVMSourceImage -Id` . Örneğin, görüntü sürümü *1.0.0* türünü kullanmak için: `Set-AzVMSourceImage -Id "/subscriptions/<subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0"` . 

Belirli bir görüntü sürümü kullanmanın, belirli görüntü sürümü bölgede silindiği ya da kaldırıldığı için kullanılabilir durumda olmaması durumunda Otomasyon başarısız olduğunu unutmayın. Belirli bir görüntü sürümü gerekli değilse, yeni VM 'nizi oluşturmak için görüntü tanımı KIMLIĞI kullanmanızı öneririz.

Kaynak adlarını bu örneklerde gereken şekilde değiştirin. 

## <a name="simplified-parameter-set"></a>Basitleştirilmiş parametre kümesi

Bir görüntüden hızlı bir şekilde VM oluşturmak için Basitleştirilmiş parametre kümesini kullanabilirsiniz. Basitleştirilmiş parametre kümesi, sanal ağ ve genel IP adresi gibi gerekli kaynakları otomatik olarak oluşturmak için VM adını kullanır. 

```azurepowershell-interactive
# Create some variables for the new VM 
$resourceGroup = "myResourceGroup"
$location = "South Central US"
$vmName = "myVMfromImage"

# Get the image. Replace the name of your resource group, gallery, and image definition. This will create the VM from the latest image version available.

$imageDefinition = Get-AzGalleryImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myResourceGroup `
   -Name myImageDefinition

# Create user object
$cred = Get-Credential `
   -Message "Enter a username and password for the virtual machine."

# Create a resource group
New-AzResourceGroup `
   -Name $resourceGroup `
   -Location $location

New-AzVM `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -Name $vmName `
   -Image $imageDefinition.Id
   -Credential $cred
```



## <a name="full-parameter-set"></a>Tam parametre kümesi

Tam parametre kümesini kullanarak belirli kaynakları kullanarak bir VM oluşturabilirsiniz.

```azurepowershell-interactive
# Create some variables for the new VM 
$resourceGroup = "myResourceGroup"
$location = "South Central US"
$vmName = "myVMfromImage"

# Get the image. Replace the name of your resource group, gallery, and image definition. This will create the VM from the latest image version available.

$imageDefinition = Get-AzGalleryImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myResourceGroup `
   -Name myImageDefinition

# Create user object
$cred = Get-Credential `
   -Message "Enter a username and password for the virtual machine."

# Create a resource group
New-AzResourceGroup `
   -Name $resourceGroup `
   -Location $location

# Network pieces
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name mySubnet `
   -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -Name MYvNET `
   -AddressPrefix 192.168.0.0/16 `
   -Subnet $subnetConfig
$pip = New-AzPublicIpAddress `
   -ResourceGroupName $resourceGroup `
   -Location $location `
  -Name "mypublicdns$(Get-Random)" `
  -AllocationMethod Static `
  -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig `
   -Name myNetworkSecurityGroupRuleRDP  `
   -Protocol Tcp `
  -Direction Inbound `
   -Priority 1000 `
   -SourceAddressPrefix * `
   -SourcePortRange * `
   -DestinationAddressPrefix * `
   -DestinationPortRange 3389 `
   -Access Allow
$nsg = New-AzNetworkSecurityGroup `
   -ResourceGroupName $resourceGroup `
   -Location $location `
  -Name myNetworkSecurityGroup `
  -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface `
   -Name myNic `
   -ResourceGroupName $resourceGroup `
   -Location $location `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id `
  -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration using $imageDefinition.Id to use the latest image version.
$vmConfig = New-AzVMConfig `
   -VMName $vmName `
   -VMSize Standard_D1_v2 | `
   Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
   Set-AzVMSourceImage -Id $imageDefinition.Id | `
   Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzVM `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -VM $vmConfig
```


## <a name="next-steps"></a>Sonraki adımlar
[Azure görüntü Oluşturucu (Önizleme)](./image-builder-overview.md) , görüntü sürümü oluşturmayı otomatikleştirmenize yardımcı olabilir, hatta [mevcut bir görüntü sürümünden yeni bir görüntü sürümünü](./windows/image-builder-gallery-update-image-version.md)güncelleştirmek ve oluşturmak için de kullanabilirsiniz. 

Ayrıca, şablonlar kullanarak paylaşılan görüntü Galerisi kaynağı da oluşturabilirsiniz. Çeşitli Azure hızlı başlangıç şablonları mevcuttur: 

- [Paylaşılan Görüntü Galerisi Oluşturma](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Paylaşılan Görüntü Galerisinde Görüntü Tanımı Oluşturma](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Paylaşılan Görüntü Galerisinde Görüntü Sürümü Oluşturma](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Görüntü Sürümünden Sanal Makine Oluşturma](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

Paylaşılan görüntü galerileri hakkında daha fazla bilgi için bkz. [genel bakış](./shared-image-galleries.md). Sorunlarla karşılaşırsanız bkz. [paylaşılan görüntü galerilerine sorun giderme](troubleshooting-shared-images.md).