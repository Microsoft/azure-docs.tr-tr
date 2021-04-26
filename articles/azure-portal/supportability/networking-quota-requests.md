---
title: Ağ sınırını artırma
description: Ağ sınırını artırma
author: anavinahar
ms.author: anavin
ms.date: 01/23/2020
ms.topic: how-to
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 801f0424e7ec15fbde58f35975f4c7eca4c9a5de
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775574"
---
# <a name="networking-limit-increase"></a>Ağ sınırını artırma

Ağ kotanızı artırmak için [Azure Portal](https://portal.azure.com) kullanın.

Geçerli ağ kullanımınızı ve kotayı Azure portal görüntülemek için aboneliğinizi açın ve ardından **kullanımlar + kotalar**' ı seçin. Ağ kullanımınızı ve limitlerinizi görüntülemek için aşağıdaki seçenekleri de kullanabilirsiniz.

* [Kullanım CLı](/cli/azure/network#az_network_list_usages)
* [PowerShell](/powershell/module/azurerm.network/get-azurermnetworkusage)
* [Ağ kullanım API 'SI](/rest/api/virtualnetwork/virtualnetworks/listusage)

**Yardım + Destek** veya portalda **kullanımlar + kotalar** ' i kullanarak artış isteyebilirsiniz.

> [!Note]
> **Genel IP ön eklerinin** varsayılan boyutunu değiştirmek için, açılan listeden **en az genel IP ağlar arası önek uzunluğu** ' nu seçin.

## <a name="request-networking-quota-increase-at-subscription-level-using-help--support"></a>Yardım + destek kullanarak abonelik düzeyinde ağ kotası artışı iste

Azure portal **Yardım + Destek** kullanarak bir destek isteği oluşturmak için aşağıdaki yönergeleri izleyin.

1. [Azure Portal](https://portal.azure.com)oturum açın ve sonra Azure Portal menüsünden **Yardım + Destek** ' i seçin veya **Yardım + Destek**' i arayıp seçin.

    ![Yardım + Destek](./media/networking-quota-request/help-plus-support.png)

1. **Yeni destek isteği**’ni seçin.

    ![Yeni destek isteği](./media/networking-quota-request/new-support-request.png)

1. **Sorun türü** için **hizmet ve abonelik sınırları (kotalar)** öğesini seçin.

    ![Sorun türü açılır listesinden abonelik sınırlarını seçin](./media/networking-quota-request/select-quota-issue-type.png)

1. Kotasını artırmanız gereken aboneliği seçin.

    ![Abonelik newSR 'yi seçin](./media/networking-quota-request/select-subscription-support-request.png)

1. **Kota türü** altında **ağ**' ı seçin. **İleri ' yi seçin: çözümler**.

    ![Kota türünü seçin](./media/networking-quota-request/select-quota-type-network.png)

1. **Sorun ayrıntıları**' nda **ayrıntıları sağla** ' yı seçin ve isteğinizi işlemeye yardımcı olması için ek bilgi girin.

    ![Ayrıntıları belirtin](./media/networking-quota-request/provide-details-link.png)

1. **Kota ayrıntıları** panelinde, bir dağıtım modeli, konum ve isteğinize dahil edilecek kaynakları seçin.

    ![Kota ayrıntıları DM](./media/networking-quota-request/quota-details-network.png)

1. Abonelikte istediğiniz yeni limitleri girin. Bir satırı kaldırmak için **kaynaklar** menüsünden kaynağın seçimini kaldırın veya atma "x" simgesini seçin. Her bir kaynağın kotasını girdikten sonra Kaydet ' i seçin **ve** destek isteği oluşturmaya devam edin.

    ![Yeni sınırlar](./media/networking-quota-request/network-new-limits.png)

## <a name="request-networking-quota-increase-at-subscription-level-using-usages--quotas"></a>Kullanımlar ve Kotalar kullanarak abonelik düzeyinde istek ağ kotası artışı

Azure portal **kullanım + kota** kullanarak bir destek isteği oluşturmak için bu yönergeleri izleyin.

1. İçinden https://portal.azure.com , **abonelik** arayın ve seçin.

    ![Abonelikler](./media/networking-quota-request/search-for-suscriptions.png)

1. Kotasını artırmanız gereken aboneliği seçin.

    ![Abonelik seçme](./media/networking-quota-request/select-subscription-change-quota.png)

1. **Kullanım + kotalar** ' ı seçin

    ![Kullanım ve kotaları seçin](./media/networking-quota-request/select-usage-plus-quotas.png)

1. Sağ üst köşede **istek artışı**' nı seçin.

    ![İstek artışı](./media/networking-quota-request/request-increase-from-subscription.png)

1. [Abonelik düzeyinde, Istek ağ kotası artışını](#request-networking-quota-increase-at-subscription-level-using-help--support)adım 3 ' te başlayan adımları izleyin.

## <a name="about-networking-limits"></a>Ağ sınırları hakkında

Ağ sınırları hakkında daha fazla bilgi edinmek için sınırlar sayfasının [ağ bölümüne](../../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) veya ağ SıNıRLARıMıZıN SSS bölümüne bakın.
