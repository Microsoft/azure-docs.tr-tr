---
title: include dosyası
description: include dosyası
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/24/2020
ms.author: cynthn
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: fc35f277bfafa80f6239ef807f1a83591646a503
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107799907"
---
## <a name="create-an-image-gallery"></a>Görüntü galerisi oluşturma 

Görüntü Galerisi, görüntü paylaşımını etkinleştirmek için kullanılan birincil kaynaktır. 

Galeri adı için izin verilen karakterler büyük veya küçük harflerden, rakamlardan, noktalardan ve noktalardan oluşur. Galeri adı tire içeremez.   Galeri adları, aboneliğiniz dahilinde benzersiz olmalıdır. 

[Az Sig Create](/cli/azure/sig#az_sig_create)kullanarak bir görüntü galerisi oluşturun. Aşağıdaki örnek, *Doğu ABD* Içinde *mygallerrg* adlı Galeri adlı bir kaynak grubu ve *MyGallery* adlı bir galeri oluşturur.

```azurecli-interactive
az group create --name myGalleryRG --location eastus
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

## <a name="share-the-gallery"></a>Galeriyi paylaşma

Role-Based Access Control (RBAC) kullanarak abonelikler arasında görüntü paylaşabilirsiniz. Galeri, görüntü tanımı veya görüntü sürümü düzeyinde görüntü paylaşabilirsiniz. Abonelikler arasında bile, görüntü sürümü üzerinde okuma izinlerine sahip olan tüm kullanıcılar, görüntü sürümünü kullanarak bir sanal makine dağıtacaktır.

Galeri düzeyindeki diğer kullanıcılarla paylaşmanızı öneririz. Galerinizin nesne KIMLIĞINI almak için [az SIG Show](/cli/azure/sig#az_sig_show)' ı kullanın.

```azurecli-interactive
az sig show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --query id
```

Nesne KIMLIĞINI bir e-posta adresi ile birlikte kullanın ve bir kullanıcıya paylaşılan görüntü galerisine erişim sağlamak için [az role atama oluştur](/cli/azure/role/assignment#az_role_assignment_create) ' u kullanın. `<email-address>`Ve `<gallery iD>` bilgilerini kendi bilgileriniz ile değiştirin.

```azurecli-interactive
az role assignment create \
   --role "Reader" \
   --assignee <email address> \
   --scope <gallery ID>
```

RBAC kullanarak kaynakları paylaşma hakkında daha fazla bilgi için bkz. [RBAC ve Azure CLI kullanarak erişimi yönetme](../articles/role-based-access-control/role-assignments-cli.md).