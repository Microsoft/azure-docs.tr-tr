---
title: VM’den görüntü oluşturma
description: Azure 'da var olan bir sanal makineden paylaşılan bir görüntü galerisinde görüntü oluşturmak için Azure PowerShell nasıl kullanacağınızı öğrenin.
author: cynthn
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: f7afc671dbb3d4ef8aa30222c4f235c9f7869d02
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102556851"
---
# <a name="create-an-image-from-a-vm"></a>VM’den görüntü oluşturma

Birden çok, özdeş VM oluşturmak için kullanmak istediğiniz mevcut bir VM varsa, Azure PowerShell kullanarak paylaşılan görüntü galerisinde görüntü oluşturmak için bu VM 'yi kullanabilirsiniz. Ayrıca, [Azure CLI](image-version-vm-cli.md)kullanarak bir VM 'den görüntü oluşturabilirsiniz.

Azure PowerShell kullanarak, hem [özel hem de Genelleştirilmiş](./shared-image-galleries.md#generalized-and-specialized-images) VM 'lerden bir görüntü yakalayabilirsiniz. 

Görüntü galerisindeki görüntülerin iki bileşeni vardır ve bu örnekte oluşturacağız:
- **Görüntü tanımı** , görüntü ve kullanma gereksinimleri hakkında bilgi taşır. Bu, görüntünün Windows veya Linux, özelleştirilmiş veya Genelleştirilmiş, sürüm notları ve en düşük ve en yüksek bellek gereksinimlerini içerir. Bu, bir görüntü türünün tanımıdır. 
- **Görüntü sürümü** , paylaşılan görüntü GALERISI kullanılırken VM oluşturmak için kullanılır. Ortamınız için gerektiğinde bir görüntünün birden fazla sürümüne sahip olabilirsiniz. Bir VM oluşturduğunuzda, sanal makine için yeni diskler oluşturmak üzere görüntü sürümü kullanılır. Görüntü sürümleri birden çok kez kullanılabilir.


## <a name="before-you-begin"></a>Başlamadan önce

Bu makaleyi tamamlayabilmeniz için, var olan bir paylaşılan görüntü galeriniz ve Azure 'da kaynak olarak kullanmak için mevcut bir sanal makine olması gerekir. 

VM 'ye eklenmiş bir veri diski varsa, veri diski boyutu 1 TB 'den fazla olamaz.

Bu makalede çalışırken, kaynak adlarını gereken yerde değiştirin.


## <a name="get-the-gallery"></a>Galeriyi al

Galerilerde ve görüntü tanımlarının tümünü ada göre listeleyebilirsiniz. Sonuçlar biçimindedir `gallery\image definition\image version` .

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Compute/galleries | Format-Table
```

Doğru Galeri ve görüntü tanımlarını bulduktan sonra, bunları daha sonra kullanmak üzere bir değişken oluşturun. Bu örnek *Myresourcegroup* kaynak grubundaki *MyGallery* adlı galeriyi alır.

```azurepowershell-interactive
$gallery = Get-AzGallery `
   -Name myGallery `
   -ResourceGroupName myResourceGroup
```

## <a name="get-the-vm"></a>VM 'yi al

[Get-azvm](/powershell/module/az.compute/get-azvm)kullanarak bir kaynak grubunda kullanılabilir olan sanal makinelerin listesini görebilirsiniz. VM adını ve içindeki kaynak grubunu öğrendikten sonra, `Get-AzVM` VM nesnesini almak ve daha sonra kullanmak üzere bir değişkende depolamak için yeniden kullanabilirsiniz. Bu örnek, "myResourceGroup" kaynak grubundan *sourcevm* ADLı bir VM 'yi alır ve *$sourceVm* değişkenine atar. 

```azurepowershell-interactive
$sourceVm = Get-AzVM `
   -Name sourceVM `
   -ResourceGroupName myResourceGroup
```

[Stop-AzVM](/powershell/module/az.compute/stop-azvm)kullanarak bir görüntü oluşturmadan önce VM 'yi durdurmak için en iyi uygulamadır.

```azurepowershell-interactive
Stop-AzVM `
   -ResourceGroupName $sourceVm.ResourceGroupName `
   -Name $sourceVm.Name `
   -Force
```

## <a name="create-an-image-definition"></a>Görüntü tanımı oluşturma 

Görüntü tanımları görüntüler için bir mantıksal gruplama oluşturur. Bunlar görüntüyle ilgili bilgileri yönetmek için kullanılır. Görüntü tanımı adları büyük veya küçük harflerden, rakamlardan, noktalardan, çizgilerden ve noktalardan oluşabilir. 

Görüntü tanımınızı yaparken, doğru bilgilerin tümünün bulunduğundan emin olun. VM 'yi genelleştirdiğinizde (Windows için Sysprep veya Linux için waagent-deprovision), kullanarak bir görüntü tanımı oluşturmanız gerekir `-OsState generalized` . VM 'yi genelleştirmediyseniz, kullanarak bir görüntü tanımı oluşturun `-OsState specialized` .

Bir görüntü tanımı için belirtebileceğiniz değerler hakkında daha fazla bilgi için bkz. [görüntü tanımları](./shared-image-galleries.md#image-definitions).

[New-Azgallerımagedefinition](/powershell/module/az.compute/new-azgalleryimageversion)kullanarak görüntü tanımını oluşturun. 

Bu örnekte, görüntü tanımı *Myımagedefinition* olarak adlandırılır ve Windows çalıştıran özel bir sanal makine içindir. Linux kullanarak görüntü tanımı oluşturmak için kullanın `-OsType Linux` . 

```azurepowershell-interactive
$imageDefinition = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState specialized `
   -OsType Windows `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```


## <a name="create-an-image-version"></a>Görüntü sürümü oluşturma

[New-Azgallerımageversion](/powershell/module/az.compute/new-azgalleryimageversion)kullanarak bir görüntü sürümü oluşturun. 

Görüntü sürümü için izin verilen karakterler rakamlardan ve dönemlerdir. Sayılar 32 bitlik bir tamsayı aralığında olmalıdır. Biçim: *MajorVersion*. *MinorVersion*. *Düzeltme Eki*.

Bu örnekte, görüntü sürümü *1.0.0* ve hem *Orta Batı ABD* hem de *Orta Güney ABD* veri merkezlerine çoğaltılır. Çoğaltma için hedef bölge seçerken, *kaynak* bölgeyi çoğaltma için hedef olarak da dahil etmeniz gerektiğini unutmayın.

VM 'den bir görüntü sürümü oluşturmak için, için kullanın `$vm.Id.ToString()` `-SourceImageId` .

```azurepowershell-interactive
   $region1 = @{Name='South Central US';ReplicaCount=1}
   $region2 = @{Name='East US';ReplicaCount=2}
   $targetRegions = @($region1,$region2)

$job = $imageVersion = New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $imageDefinition.Name`
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -TargetRegion $targetRegions  `
   -SourceImageId $sourceVm.Id.ToString() `
   -PublishingProfileEndOfLifeDate '2020-12-01' `  
   -asJob 
```

Görüntünün tüm hedef bölgelere çoğaltılması biraz zaman alabilir, bu nedenle ilerlemeyi izleyebilmemiz için bir iş oluşturduk. İşin ilerlemesini görmek için, yazın `$job.State` .

```azurepowershell-interactive
$job.State
```

> [!NOTE]
> Farklı bir görüntü sürümü oluşturmak için aynı yönetilen görüntüyü kullanabilmeniz için görüntü sürümünün oluşturulması ve çoğaltılması tamamen bitmesini beklemeniz gerekir.
>
> Görüntünüzü `-StorageAccountType Premium_LRS` , görüntü sürümünü oluştururken ekleyerek ekleyebilir veya coğrafi olarak [yedekli depolama](../storage/common/storage-redundancy.md) alanı ekleyerek, Premium depolamada da saklayabilirsiniz `-StorageAccountType Standard_ZRS` .
>

## <a name="next-steps"></a>Sonraki adımlar

Yeni görüntü sürümünün düzgün çalıştığını doğruladıktan sonra, bir VM oluşturabilirsiniz. [Özel bir görüntü sürümünden](vm-specialized-image-version-powershell.md) veya [GENELLEŞTIRILMIŞ görüntü sürümünden](vm-generalized-image-version-powershell.md)VM oluşturma.

Satın alma planı bilgilerini sağlama hakkında daha fazla bilgi için bkz. [görüntü oluştururken Azure Marketi satın alma planı bilgilerini sağlama](marketplace-images.md).