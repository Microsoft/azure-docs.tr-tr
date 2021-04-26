---
title: Azure bölgesel vCPU kota limitlerinin artışını isteyin
description: Azure portal bir bölgenin vCPU kota sınırında artış isteme.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: how-to
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: eadf740c6b5caccbf678a1238f993d4ec0b34095
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96745426"
---
# <a name="standard-quota-increase-limits-by-region"></a>Standart kota: bölgelere göre limitleri artırma

Azure Resource Manager sanal makineler için iki tür vCPU kotasını destekler:

* *Kullandıkça Öde VM 'leri* ve *ayrılmış VM örnekleri* , *Standart bir vCPU kotasına* tabidir.
* *Spot VM 'ler* bir *spot vCPU kotasına* tabidir.

Kullandıkça öde ve ayrılmış sanal makine örnekleri için standart vCPU kotası, her bölgedeki her bir abonelik için iki katmanda zorlanır:

* İlk katman, tüm VM serileri genelinde *Toplam bölgesel vCPU sayısı*.
* İkinci katman, D serisi vCPU 'Lar gibi *VM başına seri vCPU sayısı sınırı* olur.

Yeni bir spot VM dağıttığınızda, bu VM Serisi için yeni ve mevcut vCPU kullanımı, söz konusu VM Serisi için onaylanan vCPU kotasını aşmamalıdır. Ayrıca, tüm VM dizileri arasında dağıtılan yeni ve var olan vCPU 'ların toplam sayısı, abonelik için onaylanan toplam bölgesel vCPU kotasını aşmamalıdır. Bu kotalardan biri aşılırsa, VM dağıtımına izin verilmez.

Azure portal kullanarak VM Serisi için vCPU kota sınırı artışını isteyebilirsiniz. VM Serisi kotasının bir artışı toplam bölgesel vCPU limitini aynı tutara göre otomatik olarak arttırır.

Yeni bir abonelik oluşturduğunuzda, varsayılan toplam bölgesel sanal CPU sayısı, tüm tek VM serileri için toplam varsayılan vCPU kotasına eşit olmayabilir. Bu tutarsızlık, dağıtmak istediğiniz her bir VM Serisi için yeterince kota içeren bir aboneliğe neden olabilir. Ancak tüm dağıtımlar için toplam bölgesel vCPU 'Lara uyum sağlamak için yeterli kota olmayabilir. Bu durumda, toplam bölgesel vCPU sayısı sınırını açıkça artırmak için bir istek göndermeniz gerekir. Toplam bölgesel vCPU sınırı, bölgenin tüm VM serileri genelinde onaylanan toplam kotayı aşamaz.

Standart vCPU kotaları hakkında daha fazla bilgi edinmek için bkz. [sanal makine vCPU kotaları](../../virtual-machines/windows/quotas.md) ve [Azure aboneliği ve hizmet limitleri, Kotalar ve kısıtlamalar](../../azure-resource-manager/management/azure-subscription-service-limits.md).

Nokta VM vCPU sınırlarının artırılması hakkında daha fazla bilgi için bkz. [spot kota: tüm VM serileri için sınırları artırma](low-priority-quota.md).

Bölgeye göre vCPU standart kota sınırınıza iki şekilde bir artış isteğinde buluntırabilirsiniz.

## <a name="request-a-quota-increase-by-region-from-help--support"></a>Yardım + Destek 'ten bölgeye göre kota artışı isteyin

**Yardım + Destek**'ten bölgeye göre vCPU kotası artışı istemek için:

1. [Azure Portal](https://portal.azure.com) menüsünde **Yardım + Destek**' i seçin.

   !["Yardım + destek" bağlantısı](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. **Yardım + Destek** bölümünde **Yeni destek isteği**' ni seçin.

    ![Yeni destek isteği](./media/resource-manager-core-quotas-request/new-support-request.png)

1. **Sorun türü** için **hizmet ve abonelik sınırları (kotalar)** öğesini seçin.

   ![Sorun türü seçin](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. **Abonelik** için, kotasını artırmak istediğiniz aboneliği seçin.

   ![Abonelik seçme](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. **Kota türü** Için **diğer istekler**' i seçin.

   ![Kota türü seçin](./media/resource-manager-core-quotas-request/regional-quotatype.png)

1. **İleri ' yi seçin:** **Sorun ayrıntılarını** açma çözümleri. **Açıklama**' da aşağıdaki bilgileri sağlayın:

    1. **Dağıtım modeli** için **Kaynak Yöneticisi** belirtin.  
    1. **Bölge** için gerekli bölgenizi belirtin, örneğin **Doğu ABD 2**.  
    1. **Yeni sınır** için bölge için yeni bir vCPU sınırı belirtin. Bu değer, bu abonelik için ayrı SKU serisi için onaylanan kotaların toplamını aşmamalıdır.

    ![Kota isteğine ilişkin ayrıntıları girin](./media/resource-manager-core-quotas-request/regional-details.png)

1. Destek isteği oluşturmaya devam etmek için **gözden geçir + oluştur** ' u seçin.

## <a name="request-a-quota-increase-by-region-from-subscriptions"></a>Aboneliklerden bölgeye göre kota artışı isteyin

**Aboneliklerden** bölgeye göre vCPU kotası artışı istemek için:

1. [Azure Portal](https://portal.azure.com), **abonelikleri** arayıp seçin.

   ![Azure portal abonelikler 'e git](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. Kotasını artırmak istediğiniz aboneliği seçin.

   ![Değiştirilecek aboneliği seçin](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. Sol bölmede **kullanım + kotalar**' ı seçin.

   ![Kullanım ve Kotalar bağlantısını izleyin](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. Sağ üst köşede, **istek artışı**' nı seçin.

   ![Kotayı artırmak için seçin](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. **Kota türü**' nden **diğer istekler**' i seçin.

   ![Kota türünü seçin](./media/resource-manager-core-quotas-request/regional-quotatype.png)

1. **İleri ' yi seçin:** **Sorun ayrıntılarını** açma çözümleri. **Açıklama** kutusunda, aşağıdaki ek bilgileri belirtin:

    1. **Dağıtım modeli** için **Kaynak Yöneticisi** belirtin.  
    1. **Bölge** için gerekli bölgenizi belirtin, örneğin **Doğu ABD 2**.  
    1. **Yeni sınır** için bölge için yeni bir vCPU sınırı belirtin. Bu değer, bu abonelik için ayrı SKU serisi için onaylanan kotaların toplamını aşmamalıdır.

    ![Ayrıntılara bilgi girin](./media/resource-manager-core-quotas-request/regional-details.png)

1. Destek isteği oluşturmaya devam etmek için **gözden geçir + oluştur** ' u seçin.
