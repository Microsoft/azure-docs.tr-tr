---
title: Öğretici-Azure PowerShell ile özel VM görüntüleri oluşturma
description: Bu öğreticide, Azure Paylaşılan görüntü galerisinde depolanan bir Windows özel sanal makine görüntüsü oluşturmak için Azure PowerShell kullanmayı öğreneceksiniz.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 05/01/2020
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 145b7300272243099cfccad0c99d8f69e83e6762
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102555831"
---
# <a name="tutorial-create-windows-vm-images-with-azure-powershell"></a>Öğretici: Azure PowerShell ile Windows VM görüntüleri oluşturma

Görüntüler, dağıtımları önyüklemek ve birden çok VM arasında tutarlılık sağlamak için kullanılabilir. Bu öğreticide, PowerShell kullanarak bir Azure sanal makinesinin özelleştirilmiş görüntünüzü oluşturun ve paylaşılan bir görüntü galerisinde saklayın. Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

> [!div class="checklist"]
> * Paylaşılan Görüntü Galerisi Oluşturma
> * Görüntü tanımı oluşturma
> * Görüntü sürümü oluşturma
> * Görüntüden VM oluşturma 
> * Resim galerisini paylaşma



## <a name="before-you-begin"></a>Başlamadan önce

Aşağıdaki adımlar, mevcut bir VM 'nin nasıl alınacağını ve yeni VM 'Ler oluşturmak için kullanabileceğiniz yeniden kullanılabilir bir özel görüntüye nasıl ekleneceğini ayrıntılandırır.

Bu öğreticideki örneği tamamlamak için, mevcut bir sanal makinenizin olması gerekir. Gerekirse, bu öğreticide kullanmak üzere bir VM oluşturmak için [PowerShell hızlı](quick-create-powershell.md) başlangıcını görebilirsiniz. Öğreticide çalışırken, kaynak adlarını gereken yerde değiştirin.

## <a name="overview"></a>Genel Bakış

[Paylaşılan görüntü Galerisi](../shared-image-galleries.md) , kuruluşunuz genelinde özel görüntü paylaşımını basitleştirir. Özel görüntüler market görüntüleri gibidir, ancak bunları kendiniz oluşturursunuz. Özel görüntüler, uygulamaları, uygulama yapılandırmalarını ve diğer işletim sistemi yapılandırmalarını önceden yükleme gibi yapılandırmaları önyüklemek için kullanılabilir. 

Paylaşılan görüntü Galerisi, özel VM görüntülerinizi başkalarıyla paylaşmanıza olanak sağlar. Hangi görüntüleri paylaşmak istediğinizi, içinde hangi bölgelerin kullanılabilir olmasını istediğinizi ve bunları ile paylaşmak istediğinizi seçin. 

Paylaşılan görüntü Galerisi özelliği birden çok kaynak türüne sahiptir:

[!INCLUDE [virtual-machines-shared-image-gallery-resources](../../../includes/virtual-machines-shared-image-gallery-resources.md)]


## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell’i başlatma

Azure Cloud Shell, bu makaledeki adımları çalıştırmak için kullanabileceğiniz ücretsiz bir etkileşimli kabuktur. Yaygın Azure araçları, kabuğa önceden yüklenmiştir ve kabuk, hesabınızla birlikte kullanılacak şekilde yapılandırılmıştır. 

Cloud Shell'i açmak için kod bloğunun sağ üst köşesinden **Deneyin**'i seçmeniz yeterlidir. Ayrıca, ' a giderek ayrı bir tarayıcı sekmesinde Cloud Shell de başlatabilirsiniz [https://shell.azure.com/powershell](https://shell.azure.com/powershell) . **Kopyala**’yı seçerek kod bloğunu kopyalayın, Cloud Shell’e yapıştırın ve Enter tuşuna basarak çalıştırın.

## <a name="get-the-vm"></a>VM 'yi al

[Get-azvm](/powershell/module/az.compute/get-azvm)kullanarak bir kaynak grubunda kullanılabilir olan sanal makinelerin listesini görebilirsiniz. VM adını ve kaynak grubunu öğrendikten sonra, `Get-AzVM` VM nesnesini almak ve daha sonra kullanmak üzere bir değişkende depolamak için yeniden kullanabilirsiniz. Bu örnek, "myResourceGroup" kaynak grubundan *sourcevm* ADLı bir VM 'yi alır ve *$sourceVM* değişkenine atar. 

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

Görüntü tanımları görüntüler için bir mantıksal gruplama oluşturur. Bunlar içinde oluşturulan görüntü sürümleri hakkındaki bilgileri yönetmek için kullanılır. Görüntü tanımı adları büyük veya küçük harflerden, rakamlardan, noktalardan, çizgilerden ve noktalardan oluşabilir. Bir görüntü tanımı için belirtebileceğiniz değerler hakkında daha fazla bilgi için bkz. [görüntü tanımları](../shared-image-galleries.md#image-definitions).

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


## <a name="create-a-vm"></a>VM oluşturma 

Özelleştirilmiş bir görüntünüz olduktan sonra bir veya daha fazla yeni VM oluşturabilirsiniz. [New-AzVM](/powershell/module/az.compute/new-azvm) cmdlet 'ini kullanma. Görüntüyü kullanmak için, öğesini kullanın `Set-AzVMSourceImage` ve `-Id` en son görüntü sürümünü her zaman kullanmak için görüntü tanımı kimliğine (bu durumda $GalleryImage. ID) ayarlayın. 

Kaynak adlarını bu örnekte gereken şekilde değiştirin. 

```azurepowershell-interactive
# Create some variables for the new VM.
$resourceGroup = "myResourceGroup"
$location = "South Central US"
$vmName = "mySpecializedVM"

# Create a resource group
New-AzResourceGroup -Name $resourceGroup -Location $location

# Create the network resources.
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork -ResourceGroupName $resourceGroup -Location $location `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig
$pip = New-AzPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface -Name $vmName -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration using $imageVersion.Id to specify the image version.
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1_v2 | `
Set-AzVMSourceImage -Id $galleryImage.Id | `
Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```


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

# Delete the VM
Remove-AzResourceGroup -Name myResoureceGroup
```

## <a name="azure-image-builder"></a>Azure Görüntü Oluşturucusu

Azure Ayrıca, Packer, [Azure VM Image Builder](../image-builder-overview.md)üzerine inşa olan bir hizmet sunar. Özelleştirmeleri bir şablonda açıklamanız yeterlidir ve görüntü oluşturma işleme alınacaktır. 

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, özel bir VM görüntüsü oluşturdunuz. Şunları öğrendiniz:

> [!div class="checklist"]
> * Paylaşılan Görüntü Galerisi Oluşturma
> * Görüntü tanımı oluşturma
> * Görüntü sürümü oluşturma
> * Görüntüden VM oluşturma 
> * Resim galerisini paylaşma

Yüksek oranda kullanılabilir sanal makineler oluşturma hakkında bilgi edinmek için sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [Yüksek oranda kullanılabilir VM’ler oluşturma](tutorial-availability-sets.md)