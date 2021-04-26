---
title: Özelleştirilmiş görüntüden ölçek kümesi oluşturma
description: Paylaşılan görüntü galerisinde özelleştirilmiş bir görüntü kullanarak ölçek kümesi oluşturma.
author: cynthn
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: b47498127996698d53946eb2aaf54368132ae7f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98877994"
---
# <a name="create-a-scale-set-from-a-specialized-image-using-powershell"></a>PowerShell kullanarak özelleştirilmiş görüntüden ölçek kümesi oluşturma 

Azure PowerShell kullanarak [paylaşılan görüntü galerisinde](../virtual-machines/shared-image-galleries.md) depolanan özelleştirilmiş görüntü sürümünden bir VM oluşturun. Genelleştirilmiş görüntü sürümünü kullanarak bir ölçek kümesi oluşturmak istiyorsanız, bkz. [Genelleştirilmiş görüntü sürümünden ölçek kümesi örnekleri oluşturma](instance-generalized-image-version-powershell.md).

Galerinizdeki özelleştirilmiş bir görüntünüz olduğunda, [New-AzVmss](/powershell/module/az.compute/new-azvmss) cmdlet 'ini kullanarak bir sanal makine ölçek kümesi oluşturabilirsiniz. 

Bu örnekte, yeni VM 'nizin bir görüntünün en son sürümünü kullanmasını sağlamak için görüntü tanımı KIMLIĞI kullanıyoruz. Ayrıca, için görüntü sürümü KIMLIĞINI kullanarak belirli bir sürümü kullanabilirsiniz `-ImageReferenceId` . Örneğin, görüntü sürümü *1.0.0* türünü kullanmak için: `-ImageReferenceId "/subscriptions/<subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0"` . 

Belirli bir görüntü sürümü kullanmanın, belirli görüntü sürümü bölgede silindiği ya da kaldırıldığı için kullanılabilir durumda olmaması durumunda Otomasyon başarısız olduğunu unutmayın. Belirli bir görüntü sürümü gerekli değilse, yeni VM 'nizi oluşturmak için görüntü tanımı KIMLIĞI kullanmanızı öneririz.

Aşağıdaki örneklerde, *myScaleSet* adlı bir ölçek kümesi, *Myvmssrg* kaynak grubunda, *Güneydoğu ABD* konumunda oluşturulur. Ölçek kümesi *Myımagedefinition* görüntüsünden, *mygallerrg* kaynak grubundaki *MyGallery* görüntü galerisinde oluşturulur. İstendiğinde, ölçek kümesindeki sanal makine örnekleri için kendi yönetici kimlik bilgilerinizi ayarlayın.



```azurepowershell-interactive
# Get the image definition

$imageDefinition = Get-AzGalleryImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myGalleryRG `
   -Name myImageDefinition
   
# Define variables for the scale set
$resourceGroupName = "myVMSSRG"
$scaleSetName = "myScaleSet"
$location = "South Central US"

# Create a resource group
New-AzResourceGroup -ResourceGroupName $resourceGroupName -Location $location

# Create a networking pieces
$subnet = New-AzVirtualNetworkSubnetConfig `
  -Name "mySubnet" `
  -AddressPrefix 10.0.0.0/24
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName $resourceGroupName `
  -Name "myVnet" `
  -Location $location `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $subnet
$publicIP = New-AzPublicIpAddress `
  -ResourceGroupName $resourceGroupName `
  -Location $location `
  -AllocationMethod Static `
  -Name "myPublicIP"
$frontendIP = New-AzLoadBalancerFrontendIpConfig `
  -Name "myFrontEndPool" `
  -PublicIpAddress $publicIP
$backendPool = New-AzLoadBalancerBackendAddressPoolConfig -Name "myBackEndPool"
$inboundNATPool = New-AzLoadBalancerInboundNatPoolConfig `
  -Name "myRDPRule" `
  -FrontendIpConfigurationId $frontendIP.Id `
  -Protocol TCP `
  -FrontendPortRangeStart 50001 `
  -FrontendPortRangeEnd 50010 `
  -BackendPort 3389
# Create the load balancer and health probe
$lb = New-AzLoadBalancer `
  -ResourceGroupName $resourceGroupName `
  -Name "myLoadBalancer" `
  -Location $location `
  -FrontendIpConfiguration $frontendIP `
  -BackendAddressPool $backendPool `
  -InboundNatPool $inboundNATPool
Add-AzLoadBalancerProbeConfig -Name "myHealthProbe" `
  -LoadBalancer $lb `
  -Protocol TCP `
  -Port 80 `
  -IntervalInSeconds 15 `
  -ProbeCount 2
Add-AzLoadBalancerRuleConfig `
  -Name "myLoadBalancerRule" `
  -LoadBalancer $lb `
  -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
  -BackendAddressPool $lb.BackendAddressPools[0] `
  -Protocol TCP `
  -FrontendPort 80 `
  -BackendPort 80 `
  -Probe (Get-AzLoadBalancerProbeConfig -Name "myHealthProbe" -LoadBalancer $lb)
Set-AzLoadBalancer -LoadBalancer $lb

# Create IP address configurations
$ipConfig = New-AzVmssIpConfig `
  -Name "myIPConfig" `
  -LoadBalancerBackendAddressPoolsId $lb.BackendAddressPools[0].Id `
  -LoadBalancerInboundNatPoolsId $inboundNATPool.Id `
  -SubnetId $vnet.Subnets[0].Id

# Create a configuration 
$vmssConfig = New-AzVmssConfig `
    -Location $location `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode "Automatic"

# Reference the image version
Set-AzVmssStorageProfile $vmssConfig `
  -OsDiskCreateOption "FromImage" `
  -ImageReferenceId $imageDefinition.Id

# Complete the configuration
 
Add-AzVmssNetworkInterfaceConfiguration `
  -VirtualMachineScaleSet $vmssConfig `
  -Name "network-config" `
  -Primary $true `
  -IPConfiguration $ipConfig

# Create the scale set 
New-AzVmss `
  -ResourceGroupName $resourceGroupName `
  -Name $scaleSetName `
  -VirtualMachineScaleSet $vmssConfig
```

Tüm ölçek kümesi kaynaklarının ve VM'lerin oluşturulup yapılandırılması birkaç dakika sürer.

## <a name="next-steps"></a>Sonraki adımlar
[Azure görüntü Oluşturucu (Önizleme)](../virtual-machines/image-builder-overview.md) , görüntü sürümü oluşturmayı otomatikleştirmenize yardımcı olabilir, hatta [mevcut bir görüntü sürümünden yeni bir görüntü sürümünü](../virtual-machines/linux/image-builder-gallery-update-image-version.md)güncelleştirmek ve oluşturmak için de kullanabilirsiniz.  

Ayrıca, şablonlar kullanarak paylaşılan görüntü Galerisi kaynağı da oluşturabilirsiniz. Çeşitli Azure hızlı başlangıç şablonları mevcuttur: 

- [Paylaşılan Görüntü Galerisi Oluşturma](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Paylaşılan Görüntü Galerisinde Görüntü Tanımı Oluşturma](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Paylaşılan Görüntü Galerisinde Görüntü Sürümü Oluşturma](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)

Paylaşılan görüntü galerileri hakkında daha fazla bilgi için bkz. [genel bakış](../virtual-machines/shared-image-galleries.md). Sorunlarla karşılaşırsanız bkz. [paylaşılan görüntü galerilerine sorun giderme](../virtual-machines/troubleshooting-shared-images.md).