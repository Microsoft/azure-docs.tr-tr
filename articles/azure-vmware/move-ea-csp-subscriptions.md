---
title: EA ve CSP Azure VMware Çözüm aboneliklerini taşıma
description: Özel bulutu bir abonelikten diğerine taşımayı öğrenin. Taşıma, faturalandırma gibi çeşitli nedenlerle yapılabilir.
ms.topic: how-to
ms.date: 03/15/2021
ms.openlocfilehash: 608f46dbd84d6bb899a3e7fcd1f8a63b3a5e85fb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103555749"
---
# <a name="move-ea-and-csp-azure-vmware-solution-subscriptions"></a>EA ve CSP Azure VMware Çözüm aboneliklerini taşıma

Bu makalede, özel bulutu bir abonelikten diğerine taşımayı öğreneceksiniz. Taşıma, faturalandırma gibi çeşitli nedenlerle yapılabilir. 

>[!IMPORTANT]
>Hem kaynak hem de hedef abonelikler üzerinde en az katkıda bulunan haklara sahip olmanız gerekir. VNet ve VNet ağ geçidi bir abonelikten diğerine taşınamaz. Ayrıca, aboneliklerinizin taşınması, vCenter, NSX ve iş yükü sanal makineleri gibi yönetim ve iş yüklerini etkilemez.

1. Azure portal oturum açın ve taşımak istediğiniz özel bulutu seçin.

1. **Abonelik (değiştir)** bağlantısını seçin.

   :::image type="content" source="media/private-cloud-overview-subscription-id.png" alt-text="Özel bulut ayrıntılarını gösteren ekran görüntüsü.":::

1. **Hedef** için abonelik ayrıntılarını girip **İleri ' yi** seçin.

   :::image type="content" source="media/move-resources-subscription-target.png" alt-text="Hedef kaynağın ekran görüntüsü." lightbox="media/move-resources-subscription-target.png":::

1. Taşımak üzere seçtiğiniz kaynakların doğrulanmasını onaylayın ve **İleri ' yi** seçin. 

   :::image type="content" source="media/confirm-move-resources-subscription-target.png" alt-text="Taşınan kaynağı gösteren ekran görüntüsü." lightbox="media/confirm-move-resources-subscription-target.png":::

1. Yeni kaynak kimliklerini kullanmak üzere güncelleştirene kadar, ilişkili araçların ve betiklerin çalışmayacağını anladığınızı belirten onay kutusunu seçin. Sonra **Taşı**' yı seçin.

   :::image type="content" source="media/review-move-resources-subscription-target.png" alt-text="Taşınan seçili kaynağın özetini gösteren ekran görüntüsü. " lightbox="media/review-move-resources-subscription-target.png":::

   Kaynak taşıma işlemi tamamlandıktan sonra bir bildirim görüntülenir. Yeni abonelik özel buluta genel bakış bölümünde görüntülenir.

   :::image type="content" source="media/moved-subscription-target.png" alt-text="Yeni bir aboneliği gösteren ekran görüntüsü." lightbox="media/moved-subscription-target.png":::

