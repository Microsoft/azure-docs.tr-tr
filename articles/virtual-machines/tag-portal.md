---
title: Azure portal kullanarak bir VM 'yi etiketleme
description: Azure portal kullanarak bir sanal makineyi etiketleme hakkında bilgi edinin.
ms.topic: how-to
ms.workload: infrastructure-services
author: cynthn
ms.service: virtual-machines
ms.date: 11/11/2020
ms.author: cynthn
ms.openlocfilehash: 9c220814ae1f714e9eac0c0c11a50d9cf68a621d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98897417"
---
# <a name="tagging-a-vm-using-the-portal"></a>Portalı kullanarak VM etiketleme

Bu makalede Portal kullanılarak bir VM 'ye nasıl etiket ekleneceği açıklanmaktadır. Etiketler, doğrudan bir kaynağa veya bir kaynak grubuna yerleştirilebilecek Kullanıcı tanımlı anahtar/değer çiftleridir. Azure Şu anda kaynak ve kaynak grubu başına en fazla 50 etiketi desteklemektedir. Etiketler, oluşturma sırasında veya var olan bir kaynağa eklenen bir kaynağa yerleştirilebilir.


1. Portalda sanal makinenize gidin.
1. **Temel parçalar** bölümünde **etiket eklemek Için buraya tıklayın ' ı** seçin.

    :::image type="content" source="media/tag/azure-portal-tag.png" alt-text="VM sayfasının Essentials bölümünün ekran görüntüsü.":::

1. **Ad** ve **değer** için bir değer ekleyin ve ardından **Kaydet**' i seçin.

    :::image type="content" source="media/tag/key-value.png" alt-text="Etiket olarak anahtar değer çifti ekleme sayfasının ekran görüntüsü.":::

### <a name="next-steps"></a>Sonraki adımlar

- Azure kaynaklarınızın etiketlenmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Resource Manager genel bakış](../azure-resource-manager/management/overview.md) ve [etiketleri kullanarak Azure kaynaklarınızı düzenleme](../azure-resource-manager/management/tag-resources.md).
- Etiketlerin Azure kaynakları kullanımını yönetmenize nasıl yardımcı olduğunu görmek için bkz. [Azure Faturanızı Anlama](../cost-management-billing/understand/review-individual-bill.md).
