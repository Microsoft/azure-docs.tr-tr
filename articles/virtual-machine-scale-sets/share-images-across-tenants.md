---
title: Kiracılar genelinde Galeri görüntülerini paylaşma
description: Paylaşılan görüntü galerileri kullanılarak Azure kiracılar genelinde paylaşılan görüntüleri kullanarak ölçek kümeleri oluşturmayı öğrenin.
author: cynthn
ms.author: cynthn
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: shared-image-gallery
ms.date: 04/05/2019
ms.reviewer: akjosh
ms.custom: akjosh, devx-track-azurecli
ms.openlocfilehash: b6b5e48997da03c7f180c62a9142c99b05c95b46
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106444046"
---
# <a name="share-images-across-tenants-with-shared-image-gallery"></a>Paylaşılan görüntü galerisiyle kiracılar arasında görüntü paylaşma

[!INCLUDE [virtual-machines-share-images-across-tenants](../../includes/virtual-machines-share-images-across-tenants.md)]


## <a name="create-a-scale-set-using-azure-cli"></a>Azure CLI kullanarak ölçek kümesi oluşturma

AppID, uygulama anahtarı ve kiracı 1 KIMLIĞI kullanarak kiracı 1 için hizmet sorumlusu 'nda oturum açın. `az account show --query "tenantId"`Gerekirse kiracı kimliklerini almak için ' i kullanabilirsiniz.

```azurecli-interactive
az account clear
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 1 ID>'
az account get-access-token 
```
 
AppID, uygulama anahtarı ve kiracı 2 ' nin KIMLIĞINI kullanarak kiracı 2 için hizmet sorumlusu 'nda oturum açın:

```azurecli-interactive
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 2 ID>'
az account get-access-token
```

Ölçek kümesini oluşturun. Örnekteki bilgileri kendi ile değiştirin.

```azurecli-interactive
az vmss create \
  -g myResourceGroup \
  -n myScaleSet \
  --image "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>" \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="next-steps"></a>Sonraki adımlar

Herhangi bir sorunla karşılaşırsanız [paylaşılan görüntü galerilerine sorun giderebilirsiniz](../virtual-machines/troubleshooting-shared-images.md).
