---
title: Azure CLı kullanarak özelleştirilmiş görüntü sürümünden ölçek kümesi oluşturma
description: Azure CLı kullanarak paylaşılan görüntü galerisinde özelleştirilmiş görüntü sürümünü kullanarak bir ölçek kümesi oluşturun.
author: cynthn
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/01/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.custom: devx-track-azurecli
ms.openlocfilehash: 5fc88c00d548c0a034984976557d316fdac7620f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107792354"
---
# <a name="create-a-scale-set-using-a-specialized-image-version-with-the-azure-cli"></a>Azure CLı ile özel görüntü sürümü kullanarak ölçek kümesi oluşturma

Paylaşılan görüntü galerisinde depolanan [özelleştirilmiş görüntü sürümünden](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images) bir ölçek kümesi oluşturun. Genelleştirilmiş görüntü sürümünü kullanarak bir ölçek kümesi oluşturmak istiyorsanız, bkz. [Genelleştirilmiş görüntüden ölçek kümesi oluşturma](instance-generalized-image-version-cli.md).

CLı 'yi yerel olarak yükleyip kullanmayı tercih ederseniz bu öğreticide, Azure CLı sürüm 2.4.0 veya üstünü çalıştırıyor olmanız gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme]( /cli/azure/install-azure-cli).

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

[`az vmss create`](/cli/azure/vmss#az_vmss_create) `--specialized` Görüntünün özelleşmiş bir görüntü olduğunu göstermek için parametresini kullanarak bir ölçek kümesi oluşturun.

`--image`Kullanılabilir görüntünün en son sürümünden ölçek kümesi örnekleri oluşturmak için görüntü tanımı kimliği ' ni kullanın. Ayrıca, için görüntü sürümü KIMLIĞINI sağlayarak belirli bir sürümden ölçek kümesi örnekleri de oluşturabilirsiniz `--image` . Belirli bir görüntü sürümü kullanmanın, belirli görüntü sürümü bölgede silindiği ya da kaldırıldığı için kullanılabilir durumda olmaması durumunda Otomasyon başarısız olduğunu unutmayın. Belirli bir görüntü sürümü gerekli değilse, yeni VM 'nizi oluşturmak için görüntü tanımı KIMLIĞI kullanmanızı öneririz.

Bu örnekte, *Myımagedefinition* resminin en son sürümünden örnekler oluşturacağız.

```azurecli
az group create --name myResourceGroup --location eastus
az vmss create \
   --resource-group myResourceGroup \
   --name myScaleSet \
   --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
   --specialized
```


## <a name="next-steps"></a>Sonraki adımlar
[Azure görüntü Oluşturucu (Önizleme)](../virtual-machines/image-builder-overview.md) , görüntü sürümü oluşturmayı otomatikleştirmenize yardımcı olabilir, hatta [mevcut bir görüntü sürümünden yeni bir görüntü sürümünü](../virtual-machines/linux/image-builder-gallery-update-image-version.md)güncelleştirmek ve oluşturmak için de kullanabilirsiniz. 

Ayrıca, şablonlar kullanarak paylaşılan görüntü Galerisi kaynağı da oluşturabilirsiniz. Çeşitli Azure hızlı başlangıç şablonları mevcuttur: 

- [Paylaşılan Görüntü Galerisi Oluşturma](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Paylaşılan Görüntü Galerisinde Görüntü Tanımı Oluşturma](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Paylaşılan Görüntü Galerisinde Görüntü Sürümü Oluşturma](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)