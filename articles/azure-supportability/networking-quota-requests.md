---
title: Ağ sınırı artışı | Microsoft Docs
description: Ağ sınırını artırma
author: anavinahar
ms.author: anavin
ms.date: 06/19/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 5cadd0479a6ddc97cc7ee7d9b5aee1bbffba02bb
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74120974"
---
# <a name="networking-limit-increase"></a>Ağ sınırını artırma

Geçerli ağ kullanımınızı ve kotayı görüntülemek için Azure portal **kullanımları + kota** dikey penceresini ziyaret edebilirsiniz. Ağ kullanımınızı ve limitlerinizi görüntülemek için kullanım [CLI](https://docs.microsoft.com//cli/azure/network?view=azure-cli-latest#az-network-list-usages), [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermnetworkusage?view=azurermps-6.13.0) veya [ağ kullanımı API](https://docs.microsoft.com/rest/api/virtualnetwork/virtualnetworks/listusage) 'sini de kullanabilirsiniz.

**Yardım + Destek** dikey penceresinde veya portalda **kullanımlar + kota** dikey penceresinde artış isteyebilirsiniz.

> [!Note]
> Genel IP ön ekinin varsayılan boyutunu değiştirmek için, açılan listeden "En düşük genel IP ağlar arası önek uzunluğu" seçeneğini belirleyin.

## <a name="request-networking-quota-increase-at-subscription-level-using-the-help--support-blade"></a>**Yardım + Destek** dikey penceresini kullanarak abonelik düzeyinde istek ağ kotası artışı

Azure portal Azure 'un ' yardım + destek ' dikey penceresi aracılığıyla bir destek isteği oluşturmak için aşağıdaki yönergeleri izleyin. 

1. https://portal.azure.com, **Yardım + Destek**' i seçin.

    ![Yardım ve destek](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  **Yeni destek isteği**’ni seçin. 

    ![Yeni destek isteği](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. Sorun türü açılır listesinde **hizmet ve abonelik limitleri (kotalar)** öğesini seçin.

    ![Sorun türü açılan kutusu](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. Kotasını artırmanız gereken aboneliği seçin.

    ![Abonelik newSR 'yi seçin](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. **Kota türü** açılan listesinde **ağ** ' ı seçin. 

    ![Kota türünü seçin](./media/networking-quota-request/select-quota-type-network.png)

6. **Sorun ayrıntıları**' nda, **Ayrıntılar sağla**' ya tıklayarak isteğinizi işlemeye yardımcı olacak ek bilgiler sağlayın.

    ![Ayrıntıları belirtin](./media/resource-manager-core-quotas-request/provide-details.png)

7. **Kota ayrıntıları** panelinde, dağıtım modeli, bir konum ve artış Istemek istediğiniz kaynaklar ' ı seçin.

    ![Kota ayrıntıları DM](./media/networking-quota-request/quota-details-network.png)

8.  Abonelikte istediğiniz yeni limitleri girin. Bir satırı kaldırmak için kaynak açılan menüsünden kaynağın işaretini kaldırın veya atma "x" simgesine tıklayın. Her kaynak için istenen kotayı girdikten sonra, destek isteği oluşturmaya devam etmek için kota ayrıntıları panelinde Kaydet ' e tıklayın **ve devam edin** .

    ![Yeni sınırlar](./media/networking-quota-request/network-new-limits.png)


## <a name="request-networking-quota-increase-at-subscription-level-using-usages--quota-blade"></a>**Kullanımlar + kota** dikey penceresini kullanarak abonelik düzeyinde ağ kotası artışı iste

Azure 'un ' kullanım + kota ' dikey penceresi aracılığıyla Azure portal kullanılabilir bir destek isteği oluşturmak için aşağıdaki yönergeleri izleyin. 

1. https://portal.azure.com, **abonelikler**' i seçin.

    ![Abonelikler](./media/resource-manager-core-quotas-request/subscriptions.png)

2. Kotasını artırmanız gereken aboneliği seçin.

    ![Abonelik seçme](./media/resource-manager-core-quotas-request/select-subscription.png)

3. **Kullanım + kotalar** ' ı seçin

    ![Kullanım ve kotaları seçin](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. Sağ üst köşede **istek artışı**' nı seçin.

    ![İstek artışı](./media/resource-manager-core-quotas-request/request-increase.png)

5. **Yardım + Destek** dikey penceresini kullanarak *abonelik düzeyindeki istek ağı kota artışı* bölümünde bulunan adım 3 ' ten başlayarak adımları izleyin.

## <a name="about-networking-limits"></a>Ağ sınırları hakkında

Ağ sınırları hakkında daha fazla bilgi edinmek için sınırlar sayfasının [ağ bölümüne](../azure-subscription-service-limits.md#networking-limits) veya ağ SıNıRLARıMıZıN SSS bölümüne bakın
