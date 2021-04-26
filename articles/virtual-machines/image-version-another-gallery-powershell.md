---
title: PowerShell kullanarak başka bir galerinin görüntüsünü kopyalama
description: Azure PowerShell kullanarak başka bir galerinin görüntüsünü kopyalayın.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 35346836767bc1da8c498e23fd3b42afe7a9c350
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531198"
---
# <a name="copy-an-image-from-another-gallery-using-powershell"></a>PowerShell kullanarak başka bir galerinin görüntüsünü kopyalama

Kuruluşunuzda birden çok Galeri varsa, diğer galerilerinde depolanan görüntülerden görüntü oluşturabilirsiniz. Örneğin, yeni görüntüleri oluşturmak ve test etmek için bir geliştirme ve test galeriniz olabilir. Üretimde kullanılmak üzere hazırsanız, bu örneği kullanarak bunları bir üretim galerisine kopyalayabilirsiniz. Ayrıca, [Azure CLI](image-version-another-gallery-cli.md)kullanarak başka bir galerinin görüntüsünden bir görüntü oluşturabilirsiniz.


## <a name="before-you-begin"></a>Başlamadan önce

Bu makaleyi tamamlayabilmeniz için, var olan bir kaynak galerisine, görüntü tanımına ve görüntü sürümüne sahip olmanız gerekir. Ayrıca bir hedef galeriniz olmalıdır. 

Kaynak görüntü sürümü hedef galerinizin bulunduğu bölgeye çoğaltılmalıdır. 

Hedef galeriniz içinde yeni bir görüntü tanımı ve görüntü sürümü oluşturacağız.


Bu makalede çalışırken, kaynak adlarını gereken yerde değiştirin.


## <a name="get-the-source-image"></a>Kaynak görüntüyü al 

Hedef Galerinizdeki bir kopyasını oluşturabilmeniz için kaynak görüntü tanımından bilgilere ihtiyacınız olacak.

[Get-AzResource](/powershell/module/az.resources/get-azresource) cmdlet 'ini kullanarak var olan galerilerle ilgili bilgileri, görüntü tanımlarını ve görüntü sürümlerini listeleyin.

Sonuçlar biçimindedir `gallery\image definition\image version` .

```azurepowershell-interactive
Get-AzResource `
   -ResourceType Microsoft.Compute/galleries/images/versions | `
   Format-Table -Property Name,ResourceGroupName
```

İhtiyacınız olan tüm bilgilere sahip olduktan sonra [Get-Azgallerımageversion](/powershell/module/az.compute/get-azgalleryimageversion)kullanarak kaynak görüntü sürümünün kimliğini alabilirsiniz. Bu örnekte, kaynak `1.0.0` `myImageDefinition` galerisindeki tanımın görüntü sürümünü `myGallery` `myResourceGroup` kaynak grubunda alıyoruz.

```azurepowershell-interactive
$sourceImgVer = Get-AzGalleryImageVersion `
   -GalleryImageDefinitionName myImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myResourceGroup `
   -Name 1.0.0
```


## <a name="create-the-image-definition"></a>Görüntü tanımını oluşturma 

Kaynağınızın görüntü tanımıyla eşleşen yeni bir görüntü tanımı oluşturmanız gerekir. [Get-Azgallerımagedefinition](/powershell/module/az.compute/get-azgalleryimagedefinition)kullanarak görüntü tanımını yeniden oluşturmak için ihtiyacınız olan tüm bilgileri görebilirsiniz.

```azurepowershell-interactive
Get-AzGalleryImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myResourceGroup `
   -Name myImageDefinition
```


Çıkış şuna benzer olacaktır:

```output
{
  "description": null,
  "disallowed": null,
  "endOfLifeDate": null,
  "eula": null,
  "hyperVgeneration": "V1",
  "id": "/subscriptions/1111abcd-1a23-4b45-67g7-1234567de123/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition",
  "identifier": {
    "offer": "myOffer",
    "publisher": "myPublisher",
    "sku": "mySKU"
  },
  "location": "eastus",
  "name": "myImageDefinition",
  "osState": "Specialized",
  "osType": "Windows",
  "privacyStatementUri": null,
  "provisioningState": "Succeeded",
  "purchasePlan": null,
  "recommended": null,
  "releaseNoteUri": null,
  "resourceGroup": "myGalleryRG",
  "tags": null,
  "type": "Microsoft.Compute/galleries/images"
}
```

[Yeni-Azgallerımagedefinition](/powershell/module/az.compute/new-azgalleryimageversion) cmdlet 'ini ve yukarıdaki çıktıdaki bilgileri kullanarak hedef galerinizin yeni bir görüntü tanımını oluşturun.


Bu örnekte, görüntü tanımı *Mydestinationgallery* adlı galerideki *Mydestinationımgdef* olarak adlandırılmıştır.


```azurepowershell-interactive
$destinationImgDef  = New-AzGalleryImageDefinition `
   -GalleryName myDestinationGallery `
   -ResourceGroupName myDestinationRG `
   -Location WestUS `
   -Name 'myDestinationImgDef' `
   -OsState specialized `
   -OsType Windows `
   -HyperVGeneration v1 `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```


## <a name="create-the-image-version"></a>Görüntü sürümü oluşturma

[New-Azgallerımageversion](/powershell/module/az.compute/new-azgalleryimageversion)kullanarak bir görüntü sürümü oluşturun. `-Source`Hedef Galerinizdeki görüntü sürümünü oluşturmak için parametresindeki kaynak görüntünün kimliğini geçirmeniz gerekecektir. 

Görüntü sürümü için izin verilen karakterler rakamlardan ve dönemlerdir. Sayılar 32 bitlik bir tamsayı aralığında olmalıdır. Biçim: *MajorVersion*. *MinorVersion*. *Düzeltme Eki*.

Bu örnekte, hedef Galeri, *Batı ABD* konumundaki *mydestinationrg* kaynak grubunda *mydestinationgallery* olarak adlandırılmıştır. Görüntüimizin sürümü *1.0.0* ve *Orta Güney ABD* bölgesinde 1 çoğaltma ve *Batı ABD* bölgesinde 2 çoğaltma oluşturacağız. 


```azurepowershell-interactive
$region1 = @{Name='South Central US';ReplicaCount=1}
$region2 = @{Name='West US';ReplicaCount=2}
$targetRegions = @($region1,$region2)

$job = $imageVersion = New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $destinationImgDef.Name`
   -GalleryImageVersionName '1.0.0' `
   -GalleryName myDestinationGallery `
   -ResourceGroupName myDestinationRG `
   -Location WestUS `
   -TargetRegion $targetRegions  `
   -Source $sourceImgVer.Id.ToString() `
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
> Görüntünüzü `-StorageAccountType Premium_LRS` , görüntü sürümünü oluştururken ekleyerek bir ekleme veya bölgesel olarak [yedekli depolama alanı](../storage/common/storage-redundancy.md) tarafından Premiun depolamada da saklayabilirsiniz `-StorageAccountType Standard_ZRS` .
>


## <a name="next-steps"></a>Sonraki adımlar

[Genelleştirilmiş](vm-generalized-image-version-powershell.md) veya [özel](vm-specialized-image-version-powershell.md) BIR görüntü sürümünden bir VM oluşturun.

[Azure görüntü Oluşturucu (Önizleme)](./image-builder-overview.md) , görüntü sürümü oluşturmayı otomatikleştirmenize yardımcı olabilir, hatta [mevcut bir görüntü sürümünden yeni bir görüntü sürümünü](./linux/image-builder-gallery-update-image-version.md)güncelleştirmek ve oluşturmak için de kullanabilirsiniz. 

Satın alma planı bilgilerini sağlama hakkında daha fazla bilgi için bkz. [görüntü oluştururken Azure Marketi satın alma planı bilgilerini sağlama](marketplace-images.md).
