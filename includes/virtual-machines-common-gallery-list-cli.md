---
title: include dosyası
description: include dosyası
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 01/28/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 83d70a8d4c5806120ddb4ea776a8f4a6f7e63857
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107800061"
---
## <a name="list-information"></a>Liste bilgileri

[Az SIG List](/cli/azure/sig#az_sig_list)kullanarak kullanılabilir görüntü galerileriyle ilgili konum, durum ve diğer bilgileri alın.

```azurecli-interactive 
az sig list -o table
```

İşletim sistemi türü ve durumu hakkında bilgiler de dahil olmak üzere, bir galerisindeki görüntü tanımlarını listeleyin; [az Sig Image-Definition List](/cli/azure/sig/image-definition#az_sig_image_definition_list)kullanılarak.

```azurecli-interactive 
az sig image-definition list --resource-group myGalleryRG --gallery-name myGallery -o table
```

Paylaşılan görüntü sürümlerini [az Sig Image-Version List](/cli/azure/sig/image-version#az_sig_image_version_list)kullanarak bir galeride listeleyin.

```azurecli-interactive
az sig image-version list --resource-group myGalleryRG --gallery-name myGallery --gallery-image-definition myImageDefinition -o table
```

[Az Sig Image-Version Show](/cli/azure/sig/image-version#az_sig_image_version_show)kullanarak bir görüntü sürümünün kimliğini alın.

```azurecli-interactive
az sig image-version show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --query "id"
```
