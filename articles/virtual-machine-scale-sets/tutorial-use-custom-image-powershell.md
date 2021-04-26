---
title: Öğretici-Azure PowerShell bir ölçek kümesinde özel bir VM görüntüsü kullanma
description: Azure PowerShell kullanarak, sanal makine ölçek kümesini dağıtmak için kullanabileceğiniz bir özel sanal makine görüntüsünün nasıl oluşturulacağını öğrenin
author: cynthn
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.topic: tutorial
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.custom: akjosh
ms.openlocfilehash: 803bbf485d783fc160d1f2644a56e1339f73690e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98678313"
---
# <a name="tutorial-create-and-use-a-custom-image-for-virtual-machine-scale-sets-with-azure-powershell"></a>Öğretici: Azure PowerShell ile sanal makine ölçek kümeleri için özel görüntü oluşturma ve kullanma

Ölçek kümesi oluşturduğunuzda, sanal makine örnekleri dağıtılırken kullanılacak bir görüntü belirtirsiniz. Sanal makine örnekleri dağıtıldıktan sonraki görev sayısını azaltmak için özel bir sanal makine görüntüsünü kullanabilirsiniz. Bu özel sanal makine görüntüsü, gerekli uygulama yüklemelerini veya yapılandırmalarını içerir. Ölçek kümesinde oluşturulan tüm sanal makine örnekleri, özel sanal makine görüntüsünü kullanır ve uygulama trafiğinizi sunmaya hazır olur. Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:

> [!div class="checklist"]
> * Paylaşılan Görüntü Galerisi Oluşturma
> * Görüntü tanımı oluşturma
> * Görüntü sürümü oluşturma
> * Görüntüden ölçek kümesi oluşturma 
> * Resim galerisini paylaşma

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="before-you-begin"></a>Başlamadan önce

Aşağıdaki adımlar, var olan bir VM 'nin nasıl alınacağını ve bir ölçek kümesi oluşturmak için kullanabileceğiniz yeniden kullanılabilir bir özel görüntüye nasıl kullanılacağına ilişkin ayrıntılı bilgiler sağlar.

Bu öğreticideki örneği tamamlamak için, mevcut bir sanal makinenizin olması gerekir. Gerekirse, bu öğreticide kullanmak üzere bir VM oluşturmak için [PowerShell hızlı](quick-create-powershell.md) başlangıcını görebilirsiniz. Öğreticide çalışırken, kaynak adlarını gereken yerde değiştirin.

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell’i başlatma

Azure Cloud Shell, bu makaledeki adımları çalıştırmak için kullanabileceğiniz ücretsiz bir etkileşimli kabuktur. Yaygın Azure araçları, kabuğa önceden yüklenmiştir ve kabuk, hesabınızla birlikte kullanılacak şekilde yapılandırılmıştır. 

Cloud Shell'i açmak için kod bloğunun sağ üst köşesinden **Deneyin**'i seçmeniz yeterlidir. Ayrıca, ' a giderek ayrı bir tarayıcı sekmesinde Cloud Shell de başlatabilirsiniz [https://shell.azure.com/powershell](https://shell.azure.com/powershell) . **Kopyala**’yı seçerek kod bloğunu kopyalayın, Cloud Shell’e yapıştırın ve Enter tuşuna basarak çalıştırın.


## <a name="get-the-vm"></a>VM 'yi al

[Get-azvm](/powershell/module/az.compute/get-azvm)kullanarak bir kaynak grubunda kullanılabilir olan sanal makinelerin listesini görebilirsiniz. VM adını ve kaynak grubunu öğrendikten sonra, `Get-AzVM` VM nesnesini almak ve daha sonra kullanmak üzere bir değişkende depolamak için yeniden kullanabilirsiniz. Bu örnek, "myResourceGroup" kaynak grubundan *sourcevm* ADLı bir VM 'yi alır ve *$VM* değişkenine atar. 

```azurepowershell-interactive
$sourceVM = Get-AzVM `
   -Name sourceVM `
   -ResourceGroupName myResourceGroup
```
## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) komutuyla bir kaynak grubu oluşturun.

Azure kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır. Aşağıdaki örnekte, *EastUS* bölgesinde *mygallerrg* adlı bir kaynak grubu oluşturulur:

```azurepowershell-interactive
$resourceGroup = New-AzResourceGroup `
   -Name 'myGalleryRG' `
   -Location 'EastUS'
```

## <a name="create-an-image-gallery"></a>Görüntü galerisi oluşturma 

Görüntü Galerisi, görüntü paylaşımını etkinleştirmek için kullanılan birincil kaynaktır. Galeri adı için izin verilen karakterler büyük veya küçük harflerden, rakamlardan, noktalardan ve noktalardan oluşur. Galeri adı tire içeremez. Galeri adları, aboneliğiniz dahilinde benzersiz olmalıdır. 

[New-AzGallery](/powershell/module/az.compute/new-azgallery)kullanarak bir görüntü galerisi oluşturun. Aşağıdaki örnek *Mygallerrg* kaynak grubunda *MyGallery* adlı bir galeri oluşturur.

```azurepowershell-interactive
$gallery = New-AzGallery `
   -GalleryName 'myGallery' `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -Description 'Shared Image Gallery for my organization'  
```


## <a name="create-an-image-definition"></a>Görüntü tanımı oluşturma 

Görüntü tanımları görüntüler için bir mantıksal gruplama oluşturur. Bunlar içinde oluşturulan görüntü sürümleri hakkındaki bilgileri yönetmek için kullanılır. Görüntü tanımı adları büyük veya küçük harflerden, rakamlardan, noktalardan, çizgilerden ve noktalardan oluşabilir. Bir görüntü tanımı için belirtebileceğiniz değerler hakkında daha fazla bilgi için bkz. [görüntü tanımları](../virtual-machines/shared-image-galleries.md#image-definitions).

[New-Azgallerımagedefinition](/powershell/module/az.compute/new-azgalleryimageversion)kullanarak görüntü tanımını oluşturun. Bu örnekte, Galeri görüntüsü *Mygallerımage* olarak adlandırılır ve özelleştirilmiş bir görüntü için oluşturulur. 

```azurepowershell-interactive
$galleryImage = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState specialized `
   -OsType Windows `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```


## <a name="create-an-image-version"></a>Görüntü sürümü oluşturma

[New-Azgallerımageversion](/powershell/module/az.compute/new-azgalleryimageversion)kullanarak bir VM 'den görüntü sürümü oluşturma. 

Görüntü sürümü için izin verilen karakterler rakamlardan ve dönemlerdir. Sayılar 32 bitlik bir tamsayı aralığında olmalıdır. Biçim: *MajorVersion*. *MinorVersion*. *Düzeltme Eki*.

Bu örnekte, görüntü sürümü *1.0.0* ve hem *Doğu ABD* hem de *Orta Güney ABD* veri merkezlerine çoğaltılır. Çoğaltma için hedef bölge seçerken, *kaynak* bölgeyi çoğaltma için hedef olarak eklemeniz gerekir.

VM 'den bir görüntü sürümü oluşturmak için, için kullanın `$vm.Id.ToString()` `-Source` .

```azurepowershell-interactive
$region1 = @{Name='South Central US';ReplicaCount=1}
$region2 = @{Name='East US';ReplicaCount=2}
$targetRegions = @($region1,$region2)

New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name`
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -TargetRegion $targetRegions  `
   -Source $sourceVM.Id.ToString() `
   -PublishingProfileEndOfLifeDate '2020-12-01'
```

Görüntünün tüm hedef bölgelere çoğaltılması biraz zaman alabilir.

## <a name="create-a-scale-set-from-the-image"></a>Görüntüden ölçek kümesi oluşturma
Şimdi, önceki adımda oluşturulan özel VM görüntüsünü tanımlamak için parametresini kullanan [New-AzVmss](/powershell/module/az.compute/new-azvmss) ile bir ölçek kümesi oluşturun `-ImageName` . Her bir sanal makine örneklerine trafiği dağıtmak için bir yük dengeleyici de oluşturulur. Yük dengeleyici hem 80 numaralı TCP bağlantı noktasında trafiği dağıtmak hem de 3389 numaralı TCP bağlantı noktasında uzak masaüstü trafiğine ve 5985 numaralı TCP bağlantı noktasında PowerShell uzaktan iletişimine olanak tanımak için kurallar içerir. İstendiğinde, ölçek kümesindeki sanal makine örnekleri için kendi istediğiniz yönetici kimlik bilgilerini sağlayın:

```azurepowershell-interactive
# Define variables for the scale set
$resourceGroupName = "myVMSSRG3"
$scaleSetName = "myScaleSet3"
$location = "East US"

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
  -ImageReferenceId $galleryImage.Id

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


## <a name="share-the-gallery"></a>Galeriyi paylaşma

Görüntü Galerisi düzeyinde erişimi paylaşmanızı öneririz. Kullanıcının nesne KIMLIĞINI almak için bir e-posta adresi ve [Get-AzADUser](/powershell/module/az.resources/get-azaduser) cmdlet 'ini kullanın, ardından galeriye erişim sağlamak için [New-azroleatama](/powershell/module/Az.Resources/New-AzRoleAssignment) kullanın. Örnek e-postayı, alinne_montes@contoso.com Bu örnekte kendi bilgileriniz ile değiştirin.

```azurepowershell-interactive
# Get the object ID for the user
$user = Get-AzADUser -StartsWith alinne_montes@contoso.com
# Grant access to the user for our gallery
New-AzRoleAssignment `
   -ObjectId $user.Id `
   -RoleDefinitionName Reader `
   -ResourceName $gallery.Name `
   -ResourceType Microsoft.Compute/galleries `
   -ResourceGroupName $resourceGroup.ResourceGroupName
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) cmdlet 'ini kullanarak kaynak grubunu ve tüm ilgili kaynakları kaldırabilirsiniz:

```azurepowershell-interactive
# Delete the gallery 
Remove-AzResourceGroup -Name myGalleryRG

# Delete the scale set resource group
Remove-AzResourceGroup -Name myResoureceGroup
```

## <a name="azure-image-builder"></a>Azure Görüntü Oluşturucusu

Azure Ayrıca, Packer, [Azure VM Image Builder](../virtual-machines/image-builder-overview.md)üzerine inşa olan bir hizmet sunar. Özelleştirmeleri bir şablonda açıklamanız yeterlidir ve görüntü oluşturma işleme alınacaktır. 

## <a name="next-steps"></a>Sonraki adımlar
Bu öğreticide, Azure PowerShell ile ölçek kümeleriniz için özel sanal makine görüntüsü oluşturma ve kullanma işleminin nasıl yapılacağını öğrendiniz:

> [!div class="checklist"]
> * Paylaşılan Görüntü Galerisi Oluşturma
> * Görüntü tanımı oluşturma
> * Görüntü sürümü oluşturma
> * Görüntüden ölçek kümesi oluşturma 
> * Resim galerisini paylaşma

Uygulamaların ölçek kümenize nasıl dağıtılacağını öğrenmek için sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [Ölçek kümelerinize uygulama dağıtma](tutorial-install-apps-powershell.md)