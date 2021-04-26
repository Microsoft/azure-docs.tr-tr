---
title: PowerShell-paylaşılan görüntü galerisinde anlık görüntüden veya yönetilen diskten görüntü oluşturma
description: PowerShell kullanarak paylaşılan görüntü galerisinde bir anlık görüntü veya yönetilen diskten görüntü oluşturmayı öğrenin.
author: cynthn
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure
ms.date: 06/30/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 70ef194ab7f7403f31ad431eba25db5f71dfb580
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102556912"
---
# <a name="create-an-image-from-a-managed-disk-or-snapshot-in-a-shared-image-gallery-using-powershell"></a>PowerShell kullanarak paylaşılan bir görüntü galerisinde yönetilen diskten veya anlık görüntüden görüntü oluşturma

Paylaşılan görüntü galerisine geçirmek istediğiniz mevcut bir anlık görüntü veya yönetilen diskiniz varsa, doğrudan yönetilen diskten veya anlık görüntüden paylaşılan bir görüntü Galerisi görüntüsü oluşturabilirsiniz. Yeni görüntünüzü sınadıktan sonra, kaynak yönetilen diski veya anlık görüntüyü silebilirsiniz. Ayrıca, [Azure CLI](image-version-snapshot-cli.md)kullanarak paylaşılan bir görüntü galerisinde yönetilen bir diskten veya anlık görüntüden görüntü oluşturabilirsiniz.

Görüntü galerisindeki görüntülerin iki bileşeni vardır ve bu örnekte oluşturacağız:
- **Görüntü tanımı** , görüntü ve kullanma gereksinimleri hakkında bilgi taşır. Bu, görüntünün Windows veya Linux, özelleştirilmiş veya Genelleştirilmiş, sürüm notları ve en düşük ve en yüksek bellek gereksinimlerini içerir. Bu, bir görüntü türünün tanımıdır. 
- **Görüntü sürümü** , paylaşılan görüntü GALERISI kullanılırken VM oluşturmak için kullanılır. Ortamınız için gerektiğinde bir görüntünün birden fazla sürümüne sahip olabilirsiniz. Bir VM oluşturduğunuzda, sanal makine için yeni diskler oluşturmak üzere görüntü sürümü kullanılır. Görüntü sürümleri birden çok kez kullanılabilir.


## <a name="before-you-begin"></a>Başlamadan önce

Bu makaleyi tamamlayabilmeniz için bir anlık görüntü veya yönetilen diskiniz olmalıdır. 

Veri diski eklemek istiyorsanız, veri diski boyutu 1 TB 'tan fazla olamaz.

Bu makalede çalışırken, kaynak adlarını gereken yerde değiştirin.


## <a name="get-the-snapshot-or-managed-disk"></a>Anlık görüntü veya yönetilen disk alma

[Get-azsnapshot](/powershell/module/az.compute/get-azsnapshot)kullanarak bir kaynak grubunda kullanılabilir olan anlık görüntülerin listesini görebilirsiniz. 

```azurepowershell-interactive
get-azsnapshot | Format-Table -Property Name,ResourceGroupName
```

Anlık görüntü adını ve içindeki kaynak grubunu öğrendikten sonra, `Get-AzSnapshot` anlık görüntü nesnesini almak ve daha sonra kullanmak üzere bir değişkende depolamak için yeniden kullanabilirsiniz. Bu örnek, "myResourceGroup" kaynak grubundan *Kapsamım* adlı bir anlık görüntü alır ve onu *$Source* değişkenine atar. 

```azurepowershell-interactive
$source = Get-AzSnapshot `
   -SnapshotName mySnapshot `
   -ResourceGroupName myResourceGroup
```

Anlık görüntü yerine yönetilen disk de kullanabilirsiniz. Yönetilen bir disk almak için [Get-AzDisk](/powershell/module/az.compute/get-azdisk)' i kullanın. 

```azurepowershell-interactive
Get-AzDisk | Format-Table -Property Name,ResourceGroupName
```

Ardından yönetilen diski alın ve `$source` değişkenine atayın.

```azurepowershell-interactive
$source = Get-AzDisk `
   -Name myDisk
   -ResourceGroupName myResourceGroup
```

Yansımanıza dahil etmek istediğiniz tüm veri disklerini almak için aynı cmdlet 'leri kullanabilirsiniz. Bunları değişkenlere atayın ve sonra görüntü sürümünü oluştururken bu değişkenleri kullanın.


## <a name="get-the-gallery"></a>Galeriyi al

Galerilerde ve görüntü tanımlarının tümünü ada göre listeleyebilirsiniz. Sonuçlar biçimindedir `gallery\image definition\image version` .

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Compute/galleries | Format-Table
```

Doğru Galeri 'yi bulduktan sonra, daha sonra kullanmak için bir değişken oluşturun. Bu örnek *Myresourcegroup* kaynak grubundaki *MyGallery* adlı galeriyi alır.

```azurepowershell-interactive
$gallery = Get-AzGallery `
   -Name myGallery `
   -ResourceGroupName myResourceGroup
```


## <a name="create-an-image-definition"></a>Görüntü tanımı oluşturma 

Görüntü tanımları görüntüler için bir mantıksal gruplama oluşturur. Bunlar görüntüyle ilgili bilgileri yönetmek için kullanılır. Görüntü tanımı adları büyük veya küçük harflerden, rakamlardan, noktalardan, çizgilerden ve noktalardan oluşabilir. 

Görüntü tanımınızı yaparken, doğru bilgilerin tümünün bulunduğundan emin olun. Bu örnekte, anlık görüntü veya yönetilen diskin kullanımda olan ve genelleştirilmiş bir VM 'den olduğunu varsayıyoruz. Yönetilen disk veya anlık görüntü genelleştirilmiş bir işletim sistemi (Windows için [Sysprep veya](https://github.com/Azure/WALinuxAgent) `-deprovision` `-deprovision+user` Linux için veya Linux için) aldıysanız, öğesini `-OsState` olarak değiştirin `generalized` . 

Bir görüntü tanımı için belirtebileceğiniz değerler hakkında daha fazla bilgi için bkz. [görüntü tanımları](./shared-image-galleries.md#image-definitions).

[New-Azgallerımagedefinition](/powershell/module/az.compute/new-azgalleryimageversion)kullanarak görüntü tanımını oluşturun. Bu örnekte, görüntü tanımı *Myımagedefinition* olarak adlandırılır ve özelleştirilmiş bir Windows işletim sistemi içindir. Linux işletim sistemi kullanan görüntülerin tanımını oluşturmak için kullanın `-OsType Linux` . 

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

### <a name="purchase-plan-information"></a>Satın alma planı bilgileri

Bazı durumlarda, Azure Marketi görüntüsünü temel alan görüntüden bir VM oluştururken satın alma planı bilgilerini geçirmeniz gerekir. Bu gibi durumlarda, görüntü tanımına satın alma planı bilgilerini eklemeniz önerilir. Bu durumda, bkz. [görüntü oluştururken Azure Marketi satın alma planı bilgilerini sağlama](marketplace-images.md).


## <a name="create-an-image-version"></a>Görüntü sürümü oluşturma

[New-Azgallerımageversion](/powershell/module/az.compute/new-azgalleryimageversion)kullanarak anlık görüntüden bir görüntü sürümü oluşturun. 

Görüntü sürümü için izin verilen karakterler rakamlardan ve dönemlerdir. Sayılar 32 bitlik bir tamsayı aralığında olmalıdır. Biçim: *MajorVersion*. *MinorVersion*. *Düzeltme Eki*.

Görüntünüzün bir veri diski içermesini istiyorsanız, işletim sistemi diskine ek olarak, `-DataDiskImage` parametresini ekleyin ve veri diski anlık görüntüsünün veya yönetilen DISKIN kimliğine ayarlayın.

Bu örnekte, görüntü sürümü *1.0.0* ve hem *Orta Batı ABD* hem de *Orta Güney ABD* veri merkezlerine çoğaltılır. Çoğaltma için hedef bölge seçerken, *kaynak* bölgeyi çoğaltma için hedef olarak da dahil etmeniz gerektiğini unutmayın.


```azurepowershell-interactive
$region1 = @{Name='South Central US';ReplicaCount=1}
$region2 = @{Name='West Central US';ReplicaCount=2}
$targetRegions = @($region1,$region2)
$job = $imageVersion = New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $imageDefinition.Name `
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -TargetRegion $targetRegions  `
   -OSDiskImage @{Source = @{Id=$source.Id}; HostCaching = "ReadOnly" } `
   -PublishingProfileEndOfLifeDate '2025-01-01' `
   -asJob 
```

Görüntünün tüm hedef bölgelere çoğaltılması biraz zaman alabilir, bu nedenle ilerlemeyi izleyebilmemiz için bir iş oluşturduk. İşin ilerlemesini görmek için, yazın `$job.State` .

```azurepowershell-interactive
$job.State
```

> [!NOTE]
> Farklı bir görüntü sürümü oluşturmak için aynı anlık görüntüyü kullanabilmeniz için görüntü sürümünün oluşturulması ve çoğaltılması tamamen bitmesini beklemeniz gerekir. 
>
> Görüntü sürümünü oluştururken ekleyerek görüntü sürümünüzü [bölge yedekli depolama](../storage/common/storage-redundancy.md) alanında da saklayabilirsiniz `-StorageAccountType Standard_ZRS` .
>

## <a name="delete-the-source"></a>Kaynağı Sil

Yeni görüntü sürümünün düzgün çalıştığını doğruladıktan sonra, [Remove-AzSnapshot](/powershell/module/Az.Compute/Remove-AzSnapshot) veya [Remove-azdisk](/powershell/module/az.compute/remove-azdisk)ile görüntünün kaynağını silebilirsiniz.


## <a name="next-steps"></a>Sonraki adımlar

Çoğaltmanın tamamlandığını doğruladıktan sonra, [özelleştirilmiş görüntüden](vm-specialized-image-version-powershell.md)bir VM oluşturabilirsiniz.

Satın alma planı bilgilerini sağlama hakkında daha fazla bilgi için bkz. [görüntü oluştururken Azure Marketi satın alma planı bilgilerini sağlama](marketplace-images.md).