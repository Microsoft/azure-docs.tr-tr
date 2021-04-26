---
title: Görüntü sürümünden yönetilen disk oluşturma
description: Paylaşılan görüntü galerisinde bir görüntü sürümünden yönetilen disk oluşturun.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/27/2020
ms.author: cynthn
ms.reviewer: olayemio
ms.openlocfilehash: 359816bbf3fb93a4ec2bfa3092adbb2236ae2f07
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102562563"
---
# <a name="create-a-managed-disk-from-an-image-version"></a>Görüntü sürümünden yönetilen disk oluşturma

Gerekirse, bir görüntü sürümünden işletim sistemini veya tek bir veri diskini paylaşılan görüntü galerisinde depolanan bir görüntü sürümünden yönetilen disk olarak dışarı aktarabilirsiniz.


## <a name="cli"></a>CLI

Görüntü sürümlerini [az Sig Image-Version List](/cli/azure/sig/image-version#az_sig_image_version_list)kullanarak bir galeride listeleyin. Bu örnekte, *MyImage* görüntü galerisinde *myımagedefinition* görüntü tanımının bir parçası olan tüm görüntü sürümlerini arıyoruz.

```azurecli-interactive
az sig image-version list \
   --resource-group myResourceGroup\
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   -o table
```

`source`Değişkeni görüntü sürümünün kimliği olarak ayarlayın, ardından, yönetilen diski oluşturmak için [az disk Create](/cli/azure/disk#az_disk_create) kullanın. 

Bu örnekte, *EastUS* bölgesinde *myresourcegroup* adlı bir kaynak grubunda yer alan *mymanagedosdisk* adlı bir yönetilen disk oluşturmak için görüntü sürümünün işletim sistemi diskini dışarı aktardık. 

```azurecli-interactive
source="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<galleryImageDefinition>/versions/<imageVersion>"

az disk create --resource-group myResourceGroup --location EastUS --name myManagedOSDisk --gallery-image-reference $source 
```



Görüntü sürümünden bir veri diski dışarı aktarmak istiyorsanız, `--gallery-image-reference-lun` dışarı aktarılacak veri DISKININ LUN konumunu belirtmek için öğesini ekleyin. 

Bu örnekte, *EastUS* bölgesinde *myresourcegroup* adlı bir kaynak grubunda yer alan *mymanageddatadisk* adlı bir YÖNETILEN disk oluşturmak için görüntü sürümünün LUN 0 ' daki veri diski dışarı aktardık. 

```azurecli-interactive
source="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<galleryImageDefinition>/versions/<imageVersion>"

az disk create --resource-group myResourceGroup --location EastUS --name myManagedDataDisk --gallery-image-reference $source --gallery-image-reference-lun 0
``` 

## <a name="powershell"></a>PowerShell

[Get-AzResource](/powershell/module/az.resources/get-azresource)kullanarak bir galerideki görüntü sürümlerini listeleyin. 

```azurepowershell-interactive
Get-AzResource `
   -ResourceType Microsoft.Compute/galleries/images/versions | `
   Format-Table -Property Name,ResourceId,ResourceGroupName
```

İhtiyacınız olan tüm bilgilere sahip olduktan sonra, kullanmak istediğiniz kaynak görüntü sürümünü almak ve bir değişkene atamak için [Get-Azgallerımageversion](/powershell/module/az.compute/get-azgalleryimageversion) kullanabilirsiniz. Bu örnekte, kaynak `1.0.0` `myImageDefinition` galerisindeki tanımın görüntü sürümünü `myGallery` `myResourceGroup` kaynak grubunda alıyoruz.

```azurepowershell-interactive
$sourceImgVer = Get-AzGalleryImageVersion `
   -GalleryImageDefinitionName myImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myResourceGroup `
   -Name 1.0.0
```

`source`Değişkeni görüntü sürümünün kimliğine ayarladıktan sonra, diski oluşturmak üzere bir disk yapılandırması ve [Yeni-azdisk](/powershell/module/az.compute/new-azdisk) oluşturmak Için [New-azdiskconfig](/powershell/module/az.compute/new-azdiskconfig) komutunu kullanın. 

Bu örnekte, *EastUS* bölgesinde *myresourcegroup* adlı bir kaynak grubunda yer alan *mymanagedosdisk* adlı bir yönetilen disk oluşturmak için görüntü sürümünün işletim sistemi diskini dışarı aktardık. 

Disk yapılandırması oluşturun.
```azurepowershell-interactive
$diskConfig = New-AzDiskConfig `
   -Location EastUS `
   -CreateOption FromImage `
   -GalleryImageReference @{Id = $sourceImgVer.Id}
```

Diski oluşturun.

```azurepowershell-interactive
New-AzDisk -Disk $diskConfig `
   -ResourceGroupName myResourceGroup `
   -DiskName myManagedOSDisk
```

Görüntü sürümünde bir veri diski dışarı aktarmak istiyorsanız, dışarı aktarılacak veri diskinin LUN konumunu belirtmek için disk yapılandırmasına bir LUN KIMLIĞI ekleyin. 

Bu örnekte, *EastUS* bölgesinde *myresourcegroup* adlı bir kaynak grubunda yer alan *mymanageddatadisk* adlı bir YÖNETILEN disk oluşturmak için görüntü sürümünün LUN 0 ' daki veri diski dışarı aktardık. 

Disk yapılandırması oluşturun.
```azurepowershell-interactive
$diskConfig = New-AzDiskConfig `
   -Location EastUS `
   -CreateOption FromImage `
   -GalleryImageReference @{Id = $sourceImgVer.Id; Lun=0}
```

Diski oluşturun.

```azurepowershell-interactive
New-AzDisk -Disk $diskConfig `
   -ResourceGroupName myResourceGroup `
   -DiskName myManagedDataDisk
```

## <a name="next-steps"></a>Sonraki adımlar

Ayrıca, [Azure CLI](image-version-managed-image-cli.md) veya [PowerShell](image-version-managed-image-powershell.md)kullanarak yönetilen bir diskten görüntü sürümü de oluşturabilirsiniz.


