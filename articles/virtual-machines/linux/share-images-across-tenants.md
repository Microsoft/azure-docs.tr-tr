---
title: Azure 'daki kiracılar genelinde Galeri görüntülerini paylaşma
description: Paylaşılan görüntü galerileri kullanarak Azure kiracılarının tamamında VM görüntülerini paylaşmayı öğrenin.
services: virtual-machines-linux
author: cynthn
manager: gwallace
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 04/05/2019
ms.author: cynthn
ms.openlocfilehash: 18337620a6f9506e402149909667026e4a8ba7eb
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034984"
---
# <a name="share-gallery-vm-images-across-azure-tenants"></a>Azure kiracılar genelinde Galeri VM görüntülerini paylaşma

Paylaşılan görüntü galerileri, RBAC kullanarak görüntüleri paylaşmanıza olanak sağlar. RBAC kullanarak kiracınızdaki görüntüleri paylaşabilir ve hatta kiracınızın dışındaki bireyler ekleyebilirsiniz. Bu basit paylaşım seçeneği hakkında daha fazla bilgi için [Galeriyi paylaşma](/azure/virtual-machines/linux/shared-images-portal#share-the-gallery)bölümüne bakın.

[!INCLUDE [virtual-machines-share-images-across-tenants](../../../includes/virtual-machines-share-images-across-tenants.md)]

> [!IMPORTANT]
> Başka bir Azure kiracısındaki görüntüden bir VM dağıtmak için portalını kullanamazsınız. Kiracılar arasında paylaşılan bir görüntüden VM oluşturmak için Azure CLı veya [PowerShell](../windows/share-images-across-tenants.md)kullanmanız gerekir.

## <a name="create-a-vm-using-azure-cli"></a>Azure CLı kullanarak VM oluşturma

AppID, uygulama anahtarı ve kiracı 1 KIMLIĞI kullanarak kiracı 1 için hizmet sorumlusu 'nda oturum açın. Gerekirse kiracı kimliklerini almak için `az account show --query "tenantId"` kullanabilirsiniz.

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

VM 'yi oluşturun. Örnekteki bilgileri kendi ile değiştirin.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>" \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="next-steps"></a>Sonraki adımlar

Herhangi bir sorunla karşılaşırsanız [paylaşılan görüntü galerilerine sorun giderebilirsiniz](troubleshooting-shared-images.md).