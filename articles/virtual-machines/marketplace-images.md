---
title: Azure PowerShell kullanarak Market satın alma planı bilgilerini belirtme
description: Paylaşılan bir görüntü galerisinde görüntü oluştururken Azure Marketi satın alma planı ayrıntılarını belirtmeyi öğrenin.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 07/07/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 9df1f6b02e6572c8f2153016c0142912e24fcfe8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102562546"
---
# <a name="supply-azure-marketplace-purchase-plan-information-when-creating-images"></a>Görüntü oluştururken Azure Marketi satın alma planı bilgilerini sağlama

Paylaşılan bir galeride, başlangıçta Azure Marketi görüntüsünden oluşturulmuş bir kaynak kullanarak bir görüntü oluşturuyorsanız, satın alma planı bilgilerini izlemeniz gerekebilir. Bu makalede bir VM için satın alma planı bilgilerinin nasıl bulunacağı gösterilir ve ardından bu bilgiler bir görüntü tanımı oluşturulurken kullanılır. Ayrıca, bir görüntü için VM oluştururken satın alma planı bilgilerini sağlamayı kolaylaştırmak için görüntü tanımındaki bilgileri de ele alıyoruz.

Market görüntülerini bulma ve kullanma hakkında daha fazla bilgi için bkz. [Azure Marketi görüntülerini bulma ve kullanma](./windows/cli-ps-findimage.md).


## <a name="get-the-source-vm-information"></a>Kaynak VM bilgilerini al
Hala orijinal VM varsa, Get-AzVM ' yi kullanarak plan adı, yayımcı ve ürün bilgilerini buradan edinebilirsiniz. Bu örnek *Myresourcegroup* kaynak grubundaki *myvm* adlı bir VM 'YI alır ve ardından VM için satın alma planı bilgilerini görüntüler.

```azurepowershell-interactive
$vm = Get-azvm `
   -ResourceGroupName myResourceGroup `
   -Name myVM
$vm.Plan
```

## <a name="create-the-image-definition"></a>Görüntü tanımını oluşturma

Görüntüyü depolamak için kullanmak istediğiniz görüntü galerisini alın. Önce tüm galerileri listeleyebilirsiniz.

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Compute/galleries | Format-Table
```

Ardından, kullanmak istediğiniz Galeri için değişkenler oluşturun. Bu örnekte, `$gallery` *Mygallerrg* kaynak grubunda *MyGallery* adlı bir değişken oluşturacağız.

```azurepowershell-interactive
$gallery = Get-AzGallery `
   -Name myGallery `
   -ResourceGroupName myGalleryRG
```

, Ve parametrelerini kullanarak görüntü tanımını oluşturun  `-PurchasePlanPublisher` `-PurchasePlanProduct` `-PurchasePlanName` .

```azurepowershell-interactive
 $imageDefinition = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState specialized `
   -OsType Linux `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU' `
   -PurchasePlanPublisher $vm.Plan.Publisher `
   -PurchasePlanProduct $vm.Plan.Product `
   -PurchasePlanName  $vm.Plan.Name
```

Ardından [New-Azgallerımageversion](/powershell/module/az.compute/new-azgalleryimageversion)kullanarak görüntü sürümünüzü oluşturun. [VM](image-version-vm-powershell.md#create-an-image-version), [yönetilen görüntü](image-version-managed-image-powershell.md#create-an-image-version), [vhd\snapshot](image-version-snapshot-powershell.md#create-an-image-version)veya [başka bir görüntü sürümünden](image-version-another-gallery-powershell.md#create-the-image-version)görüntü sürümü oluşturabilirsiniz. 


## <a name="create-the-vm"></a>Sanal makineyi oluşturma

Görüntüden bir VM oluşturmaya gittiğinizde, [set-AzVMPlan](/powershell/module/az.compute/set-azvmplan)kullanarak yayımcı bilgilerini geçirmek için görüntü tanımındaki bilgileri kullanabilirsiniz.


```azurepowershell-interactive
# Create some variables for the new VM.
$resourceGroup = "mySIGPubVM"
$location = "West Central US"
$vmName = "mySIGPubVM"

# Create a resource group
New-AzResourceGroup -Name $resourceGroup -Location $location

# Create the network resources.
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
   -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -Name myNetworkSecurityGroup `
   -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface `
   -Name $vmName `
   -ResourceGroupName $resourceGroup `
   -Location $location `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id `
  -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration using Set-AzVMSourceImage -Id $imageDefinition.Id to use the latest available image version. Set-AZVMPlan is used to pass the plan information in for the VM.

$vmConfig = New-AzVMConfig `
   -VMName $vmName `
   -VMSize Standard_D1_v2   | `
   Set-AzVMSourceImage -Id $imageDefinition.Id | `
   Set-AzVMPlan `
     -Publisher $imageDefinition.PurchasePlan.Publisher `
     -Product $imageDefinition.PurchasePlan.Product `
     -Name $imageDefinition.PurchasePlan.Name | `
   Add-AzVMNetworkInterface -Id $nic.Id

# Create the virtual machine
New-AzVM `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -VM $vmConfig
```

## <a name="next-steps"></a>Sonraki adımlar

Market görüntülerini bulma ve kullanma hakkında daha fazla bilgi için bkz. [Azure Marketi görüntülerini bulma ve kullanma](./windows/cli-ps-findimage.md).
