---
title: include dosyası
description: include dosyası
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 32978257c3e209dc78a29c6e8ae0d1c4ae016a5b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95557486"
---
## <a name="update-resources"></a>Kaynakları güncelleştirme

Nelerin güncelleştirilemeyebilir hakkında bazı sınırlamalar vardır. Aşağıdaki öğeler güncelleştirilemeyebilir: 

Paylaşılan görüntü Galerisi:
- Description

Görüntü tanımı:
- Önerilen vCPU 'Lar
- Önerilen bellek
- Description
- Yaşam tarihi sonu

Görüntü sürümü:
- Bölgesel çoğaltma sayısı
- Hedef bölgeler
- En son dışarıda bırakma
- Yaşam tarihi sonu

Çoğaltma bölgelerini eklemeyi planlıyorsanız, kaynak yönetilen görüntüyü silmeyin. Kaynak yönetilen görüntü, görüntü sürümünü ek bölgelere çoğaltmak için gereklidir. 

Bir galerinin açıklamasını güncelleştirmek için [Update-AzGallery](/powershell/module/az.compute/update-azgallery)komutunu kullanın.

```azurepowershell-interactive
Update-AzGallery `
   -Name $gallery.Name ` 
   -ResourceGroupName $resourceGroup.Name
```

Bu örnek, görüntü tanımımız için son kullanım tarihini güncelleştirmek üzere [Update-Azgallerımagedefinition](/powershell/module/az.compute/update-azgalleryimagedefinition) öğesinin nasıl kullanılacağını gösterir.

```azurepowershell-interactive
Update-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -Name $galleryImage.Name `
   -ResourceGroupName $resourceGroup.Name `
   -EndOfLifeDate 01/01/2030
```

Bu örnek, bu görüntü sürümünün *en son* görüntü olarak kullanılmasını dışlamak için [Update-azgallerımageversion](/powershell/module/az.compute/update-azgalleryimageversion) ' ın nasıl kullanılacağını gösterir.

```azurepowershell-interactive
Update-AzGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name `
   -GalleryName $gallery.Name `
   -Name $galleryVersion.Name `
   -ResourceGroupName $resourceGroup.Name `
   -PublishingProfileExcludeFromLatest
```

Bu örnek, [Update-Azgallerımageversion](/powershell/module/az.compute/update-azgalleryimageversion) ' ın bu görüntü sürümünü *en son* görüntü için göz önünde bulundurmak için nasıl kullanılacağını gösterir.

```azurepowershell-interactive
Update-AzGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name `
   -GalleryName $gallery.Name `
   -Name $galleryVersion.Name `
   -ResourceGroupName $resourceGroup.Name `
   -PublishingProfileExcludeFromLatest:$false
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Kaynakları silerken, iç içe kaynaklardaki son öğeyle başlamanız gerekir-görüntü sürümü. Sürümler silindikten sonra, görüntü tanımını silebilirsiniz. Altındaki tüm kaynaklar silinene kadar galeriyi silemezsiniz.

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$gallery = "myGallery"
$imageDefinition = "myImageDefinition"
$imageVersion = "myImageVersion"

Remove-AzGalleryImageVersion `
   -GalleryImageDefinitionName $imageDefinition `
   -GalleryName $gallery `
   -Name $imageVersion `
   -ResourceGroupName $resourceGroup

Remove-AzGalleryImageDefinition `
   -ResourceGroupName $resourceGroup `
   -GalleryName $gallery `
   -GalleryImageDefinitionName $imageDefinition

Remove-AzGallery `
   -Name $gallery `
   -ResourceGroupName $resourceGroup

Remove-AzResourceGroup -Name $resourceGroup
```