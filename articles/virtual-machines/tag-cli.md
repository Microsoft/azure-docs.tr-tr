---
title: Azure CLı kullanarak bir Azure sanal makinesini etiketleme
description: Azure CLı kullanarak bir sanal makineyi etiketleme hakkında bilgi edinin.
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/11/2020
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: 20bb4ab622a01646bcc61d0f691c514a25a06edc
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502615"
---
# <a name="how-to-tag-a-vm-using-the-azure-cli"></a>Azure CLı kullanarak bir VM 'yi etiketleme

Bu makalede, Azure CLı kullanarak bir VM 'nin nasıl etiketleneceğini açıklanmaktadır. Etiketler, doğrudan bir kaynağa veya bir kaynak grubuna yerleştirilebilecek Kullanıcı tanımlı anahtar/değer çiftleridir. Azure Şu anda kaynak ve kaynak grubu başına en fazla 50 etiketi desteklemektedir. Etiketler, oluşturma sırasında veya var olan bir kaynağa eklenen bir kaynağa yerleştirilebilir. Ayrıca, bir sanal makineyi Azure [PowerShell](tag-powershell.md)kullanarak etiketleyebilirsiniz.


Belirli bir VM 'nin, kullanarak etiketleri de içeren tüm özelliklerini görüntüleyebilirsiniz `az vm show` .

```azurecli-interactive
az vm show --resource-group myResourceGroup --name myVM --query tags
```

Azure CLı aracılığıyla yeni bir VM etiketi eklemek için `azure vm update` komutunu Tag parametresiyle birlikte kullanabilirsiniz `--set` :

```azurecli-interactive
az vm update \
    --resource-group myResourceGroup \
    --name myVM \
    --set tags.myNewTagName1=myNewTagValue1 tags.myNewTagName2=myNewTagValue2
```

Etiketleri kaldırmak için `--remove` komutunda parametresini kullanabilirsiniz `azure vm update` .

```azurecli-interactive
az vm update \
   --resource-group myResourceGroup \
   --name myVM \
   --remove tags.myNewTagName1
```

Kaynaklarımıza Azure CLı ve Portal için Etiketler uyguladığımıza göre, şimdi de fatura portalındaki etiketleri görmek için kullanım ayrıntılarına göz atalım.

### <a name="next-steps"></a>Sonraki adımlar

- Azure kaynaklarınızın etiketlenmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Resource Manager genel bakış](../azure-resource-manager/management/overview.md) ve [etiketleri kullanarak Azure kaynaklarınızı düzenleme](../azure-resource-manager/management/tag-resources.md).
- Etiketlerin Azure kaynakları kullanımını yönetmenize nasıl yardımcı olduğunu görmek için bkz. [Azure Faturanızı Anlama](../cost-management-billing/understand/review-individual-bill.md).
