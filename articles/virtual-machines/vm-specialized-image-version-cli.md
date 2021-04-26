---
title: Azure CLı kullanarak özelleştirilmiş bir görüntü sürümünden VM oluşturma
description: Azure CLı kullanarak paylaşılan görüntü galerisinde özelleştirilmiş görüntü sürümünü kullanarak bir VM oluşturun.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 04/23/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.custom: devx-track-azurecli
ms.openlocfilehash: b3498037f3d2088459784ab066b8e94ba344a275
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107792192"
---
# <a name="create-a-vm-using-a-specialized-image-version-with-the-azure-cli"></a>Azure CLı ile özel görüntü sürümünü kullanarak VM oluşturma

Paylaşılan görüntü galerisinde depolanan [özelleştirilmiş görüntü sürümünden](./shared-image-galleries.md#generalized-and-specialized-images) bir VM oluşturun. Genelleştirilmiş görüntü sürümü kullanarak bir VM oluşturmak istiyorsanız, bkz. [Genelleştirilmiş görüntü sürümünden VM oluşturma](vm-generalized-image-version-cli.md).

Kaynak adlarını bu örnekte gereken şekilde değiştirin. 

Tanımların adını ve KIMLIĞINI görmek için [az Sig Image-Definition List](/cli/azure/sig/image-definition#az_sig_image_definition_list) öğesini kullanarak bir galerideki görüntü tanımlarını listeleyin.

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
az sig image-definition list \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --query "[].[name, id]" \
   --output tsv
```

Görüntünün özelleşmiş bir görüntü olduğunu göstermek için,--özelleşmiş parametresini kullanarak [az VM Create](/cli/azure/vm#az_vm_create) kullanarak VM oluşturun. 

`--image`Kullanılabilir görüntünün en son sürümünden VM oluşturmak için görüntü tanımı kimliği ' ni kullanın. Ayrıca, için görüntü sürüm KIMLIĞI sağlayarak VM 'yi belirli bir sürümden da oluşturabilirsiniz `--image` . 

Bu örnekte, *Myımagedefinition* görüntüsünün en son sürümünden bir VM oluşturacağız.

```azurecli
az group create --name myResourceGroup --location eastus
az vm create --resource-group myResourceGroup \
    --name myVM \
    --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
    --specialized
```


## <a name="next-steps"></a>Sonraki adımlar
[Azure görüntü Oluşturucu (Önizleme)](./image-builder-overview.md) , görüntü sürümü oluşturmayı otomatikleştirmenize yardımcı olabilir, hatta [mevcut bir görüntü sürümünden yeni bir görüntü sürümünü](./linux/image-builder-gallery-update-image-version.md)güncelleştirmek ve oluşturmak için de kullanabilirsiniz. 

Ayrıca, şablonlar kullanarak paylaşılan görüntü Galerisi kaynağı da oluşturabilirsiniz. Çeşitli Azure hızlı başlangıç şablonları mevcuttur: 

- [Paylaşılan Görüntü Galerisi Oluşturma](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Paylaşılan Görüntü Galerisinde Görüntü Tanımı Oluşturma](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Paylaşılan Görüntü Galerisinde Görüntü Sürümü Oluşturma](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Görüntü Sürümünden Sanal Makine Oluşturma](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)