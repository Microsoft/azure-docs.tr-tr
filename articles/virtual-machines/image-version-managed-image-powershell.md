---
title: Yönetilen bir görüntüyü paylaşılan görüntü galerisine kopyalama
description: Yönetilen bir görüntüyü paylaşılan görüntü galerisindeki görüntü sürümüne kopyalamak için Azure PowerShell nasıl kullanacağınızı öğrenin.
author: cynthn
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: cec4f258cfaa0584c24f2cfc92ec1a536f6277cc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102556989"
---
# <a name="clone-a-managed-image-to-a-shared-image-gallery-image"></a>Yönetilen bir görüntüyü paylaşılan görüntü Galerisi görüntüsüne kopyalama

Kopyalamak ve paylaşılan bir görüntü galerisine taşımak istediğiniz mevcut bir yönetilen görüntünüz varsa, doğrudan yönetilen görüntüden paylaşılan bir görüntü Galerisi görüntüsü oluşturabilirsiniz. Yeni görüntünüzü sınadıktan sonra, kaynak yönetilen görüntüyü silebilirsiniz. Ayrıca, [Azure CLI](image-version-managed-image-cli.md)kullanarak yönetilen bir görüntüden paylaşılan görüntü galerisine geçiş yapabilirsiniz.

Görüntü galerisindeki görüntülerin iki bileşeni vardır ve bu örnekte oluşturacağız:
- **Görüntü tanımı** , görüntü ve kullanma gereksinimleri hakkında bilgi taşır. Bu, görüntünün Windows veya Linux, özelleştirilmiş veya Genelleştirilmiş, sürüm notları ve en düşük ve en yüksek bellek gereksinimlerini içerir. Bu, bir görüntü türünün tanımıdır. 
- **Görüntü sürümü** , paylaşılan görüntü GALERISI kullanılırken VM oluşturmak için kullanılır. Ortamınız için gerektiğinde bir görüntünün birden fazla sürümüne sahip olabilirsiniz. Bir VM oluşturduğunuzda, sanal makine için yeni diskler oluşturmak üzere görüntü sürümü kullanılır. Görüntü sürümleri birden çok kez kullanılabilir.


## <a name="before-you-begin"></a>Başlamadan önce

Bu makaleyi tamamlayabilmeniz için, mevcut bir yönetilen görüntünüz olmalıdır. Yönetilen görüntü bir veri diski içeriyorsa, veri diski boyutu 1 TB 'den fazla olamaz.

Bu makalede çalışırken, kaynak grubu ve VM adlarını gerektiği yerde değiştirin.

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

Görüntü tanımınızı yaparken, doğru bilgilerin tümünün bulunduğundan emin olun. Yönetilen görüntüler her zaman genelleştirildiğinden, öğesini ayarlamanız gerekir `-OsState generalized` . 

Bir görüntü tanımı için belirtebileceğiniz değerler hakkında daha fazla bilgi için bkz. [görüntü tanımları](./shared-image-galleries.md#image-definitions).

[New-Azgallerımagedefinition](/powershell/module/az.compute/new-azgalleryimageversion)kullanarak görüntü tanımını oluşturun. Bu örnekte, görüntü tanımı *Myımagedefinition* olarak adlandırılır ve genelleştirilmiş bir Windows işletim sistemi içindir. Linux işletim sistemi kullanan görüntülerin tanımını oluşturmak için kullanın `-OsType Linux` . 

```azurepowershell-interactive
$imageDefinition = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState generalized `
   -OsType Windows `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```

## <a name="get-the-managed-image"></a>Yönetilen görüntüyü al

[Get-Azımage](/powershell/module/az.compute/get-azimage)kullanarak bir kaynak grubunda kullanılabilen görüntülerin listesini görebilirsiniz. Görüntü adını ve içindeki kaynak grubunu öğrendikten sonra, `Get-AzImage` görüntü nesnesini almak ve daha sonra kullanmak üzere bir değişkende depolamak için yeniden kullanabilirsiniz. Bu örnek, "myResourceGroup" kaynak grubundan *MyImage* adlı bir görüntü alır ve onu *$managedImage* değişkenine atar. 

```azurepowershell-interactive
$managedImage = Get-AzImage `
   -ImageName myImage `
   -ResourceGroupName myResourceGroup
```


## <a name="create-an-image-version"></a>Görüntü sürümü oluşturma

[Yeni-Azgallerımageversion](/powershell/module/az.compute/new-azgalleryimageversion)kullanarak yönetilen görüntüden bir görüntü sürümü oluşturun. 

Görüntü sürümü için izin verilen karakterler rakamlardan ve dönemlerdir. Sayılar 32 bitlik bir tamsayı aralığında olmalıdır. Biçim: *MajorVersion*. *MinorVersion*. *Düzeltme Eki*.

Bu örnekte, görüntü sürümü *1.0.0* ve hem *Orta Batı ABD* hem de *Orta Güney ABD* veri merkezlerine çoğaltılır. Çoğaltma için hedef bölge seçerken, *kaynak* bölgeyi çoğaltma için hedef olarak da dahil etmeniz gerektiğini unutmayın. 


```azurepowershell-interactive
$region1 = @{Name='South Central US';ReplicaCount=1}
$region2 = @{Name='West Central US';ReplicaCount=2}
$targetRegions = @($region1,$region2)
$job = $imageVersion = New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $imageDefinition.Name `
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $imageDefinition.ResourceGroupName `
   -Location $imageDefinition.Location `
   -TargetRegion $targetRegions  `
   -SourceImageId $managedImage.Id.ToString() `
   -PublishingProfileEndOfLifeDate '2020-12-31' `
   -asJob 
```

Görüntünün tüm hedef bölgelere çoğaltılması biraz zaman alabilir, bu nedenle ilerlemeyi izleyebilmemiz için bir iş oluşturduk. İlerleme durumunu görmek için yazın `$job.State` .

```azurepowershell-interactive
$job.State
```


> [!NOTE]
> Farklı bir görüntü sürümü oluşturmak için aynı yönetilen görüntüyü kullanabilmeniz için görüntü sürümünün oluşturulması ve çoğaltılması tamamen bitmesini beklemeniz gerekir. 
>
> Görüntünüzü `-StorageAccountType Premium_LRS` , görüntü sürümünü oluştururken ekleyerek ekleyebilir veya coğrafi olarak [yedekli depolama](../storage/common/storage-redundancy.md) alanı ekleyerek, Premium depolamada da saklayabilirsiniz `-StorageAccountType Standard_ZRS` .
>

## <a name="delete-the-managed-image"></a>Yönetilen görüntüyü silme

Yeni görüntü sürümünün düzgün çalıştığını doğruladıktan sonra, yönetilen görüntüyü silebilirsiniz.

```azurepowershell-interactive
Remove-AzImage `
   -ImageName $managedImage.Name `
   -ResourceGroupName $managedImage.ResourceGroupName
```

## <a name="next-steps"></a>Sonraki adımlar

Çoğaltmanın tamamlandığını doğruladıktan sonra [Genelleştirilmiş görüntüden](vm-generalized-image-version-powershell.md)bir VM oluşturabilirsiniz.

Satın alma planı bilgilerini sağlama hakkında daha fazla bilgi için bkz. [görüntü oluştururken Azure Marketi satın alma planı bilgilerini sağlama](marketplace-images.md).