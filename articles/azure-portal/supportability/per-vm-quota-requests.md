---
title: Azure VM Serisi başına vCPU kota limitlerinin artışını isteyin
description: Toplam bölgesel vCPU limitini aynı tutara göre artıran Azure portal bir VM Serisi için vCPU kota sınırında artış isteme.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: how-to
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 5db3e538a64e275313e1e0ab01f6cc6350eabb77
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96745443"
---
# <a name="standard-quota-increase-limits-by-vm-series"></a>Standart kota: VM serisine göre sınırları artırma 

Azure Resource Manager sanal makineler için iki tür vCPU kotasını destekler:

* *Kullandıkça Öde VM 'leri* ve *ayrılmış VM örnekleri* , *Standart bir vCPU kotasına* tabidir.
* *Spot VM 'ler* bir *spot vCPU kotasına* tabidir.

Kullandıkça öde ve ayrılmış sanal makine örnekleri için standart vCPU kotası, her bölgedeki her bir abonelik için iki katmanda zorlanır:

* İlk katman, tüm VM serileri genelinde *Toplam bölgesel vCPU sayısı*.
* İkinci katman, Dv3 serisi vCPU 'Lar gibi *VM başına seri vCPU sayısı sınırı* olur.

Yeni bir spot VM 'yi her dağıttığınızda, tüm spot sanal makine örneklerinin toplam yeni ve var olan vCPU kullanımı, onaylanan nokta vCPU kota sınırını aşmamalıdır. Spot kota aşılırsa, spot VM dağıtımına izin verilmez.

Azure portal kullanarak VM Serisi için vCPU kota sınırı artışını isteyebilirsiniz. VM Serisi kotasının bir artışı toplam bölgesel vCPU limitini aynı tutara göre otomatik olarak arttırır.

Standart vCPU kotaları hakkında daha fazla bilgi edinmek için bkz. [sanal makine vCPU kotaları](../../virtual-machines/windows/quotas.md) ve [Azure aboneliği ve hizmet limitleri](./classic-deployment-model-quota-increase-requests.md).

Standart kota için bölgeye göre vCPU sınırını artırma hakkında bilgi edinmek için bkz. [Standart kota: bölgeye göre sınırları artırma](regional-quota-requests.md).

Nokta VM vCPU sınırlarının artırılması hakkında daha fazla bilgi için bkz. [spot kota: tüm VM serileri için sınırları artırma](low-priority-quota.md).

Aşağıdaki bölümlerde açıklandığı gibi, her iki şekilde de VM Serisi başına standart vCPU kota limitlerinin artışını isteyebilirsiniz.

## <a name="request-a-standard-quota-increase-from-help--support"></a>Yardım + Destek 'ten standart bir kota artışı isteyin

**Yardım + Destek**'ten VM başına standart vCPU kotası artışı istemek için:

> [!NOTE]
> Ayrıca, tek bir destek talebi aracılığıyla birden çok bölge için bir kota sınırı artışı isteyebilirsiniz. Ayrıntılar için bkz. 8. adım.

1. [Azure Portal](https://portal.azure.com) menüsünde **Yardım + Destek**' i seçin.

   ![Yardım + destek bağlantısı](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. **Yardım + Destek** bölümünde **Yeni destek isteği**' ni seçin.

    ![Yeni bir destek isteği oluşturun](./media/resource-manager-core-quotas-request/new-support-request.png)

1. **Sorun türü** için **hizmet ve abonelik sınırları (kotalar)** öğesini seçin.

   ![Sorun türü seçin](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. **Abonelik** için, kotasını artırmak istediğiniz aboneliği seçin.

   ![Daha fazla kota için bir abonelik seçin](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. **Kota türü** Için, **işlem-VM (çekirdekler-vCPU 'lar) abonelik sınırı artışları**' ni seçin.

   ![Kota türü seçin](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. **İleri ' yi seçin:** **Sorun ayrıntılarını** açma çözümleri. Ek bilgi girmek için **Ayrıntılar sağla** ' yı seçin.

   !["Ayrıntıları sağla" bağlantısı](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. **Kota ayrıntılarında** aşağıdaki adımları uygulayın:

   ![Tek kota ayrıntıları sağlayın](./media/resource-manager-core-quotas-request/quota-details-deployment-rm-locations.png)

   1. **Dağıtım modeli** için uygun modeli seçin.

   1. **Konumlar** için bir konum seçin. Seçilen konum için **türler** altında, **tür seçin** bölümünde **Standart**' ı seçin.

      ![Kota ayrıntıları-kota türleri](./media/resource-manager-core-quotas-request/quota-details-select-standard-type.png)

      **Türler** altında, çoklu seçim desteğiyle tek bir destek durumundan hem standart hem de spot kota türleri isteyebilirsiniz.

      Spot kota sınırlarını artırma hakkında daha fazla bilgi için bkz. [Sanal Makine Ölçek Kümeleri Için Azure spot VM 'ler](../../virtual-machine-scale-sets/use-spot.md).

   1. **Standart** olarak, daha fazla kota için SKU serisini seçin.

      ![Kota ayrıntıları-SKU serisi](./media/resource-manager-core-quotas-request/quota-details-standard-select-series.png)

   1. Bu abonelik için istediğiniz yeni kota sınırlarını girin. Listenizden bir SKU 'YU kaldırmak için SKU 'nun yanındaki onay kutusunu temizleyin veya atma "X" simgesini seçin.

      ![Yeni bir vCPU sınırı seçin](./media/resource-manager-core-quotas-request/quota-details-standard-set-vcpu-limit.png)

1. Birden fazla konum için bir kota artışı istemek için, **konumlarda** ek bir konum seçin ve ardından uygun bir VM türü seçin. Daha sonra ek konum için geçerli olan bir sınır girebilirsiniz.

   ![Kota ayrıntılarında ek konumlar belirtin](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. Destek isteği oluşturmaya devam etmek için **Kaydet ' i seçin ve devam edin** .

## <a name="request-a-standard-quota-increase-from-subscriptions"></a>Aboneliklerden standart bir kota artışı isteyin

**Aboneliklerden** VM Serisi başına standart bir vCPU kotası artışı istemek için:

> [!NOTE]
> Ayrıca, tek bir destek talebi aracılığıyla birden çok bölge için bir kota sınırı artışı isteyebilirsiniz. Ayrıntılar için bkz. 7. adım.

1. [Azure Portal](https://portal.azure.com), **abonelikleri** arayıp seçin.

   ![Azure portal arama içindeki abonelikler](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. Kotasını artırmak istediğiniz aboneliği seçin.

   ![Değişiklik için seçilecek abonelikler](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. Sol bölmede **kullanım + kotalar**' ı seçin.

   !["Kullanım + kotalar" bağlantısı](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. Sağ üst köşede, **istek artışı**' nı seçin.

   ![Kotayı artırmak için seçin](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. **Kota türü** Için, **işlem-VM (çekirdekler-vCPU 'lar) abonelik sınırı artışları**' ni seçin.

   ![Kota türü seçin](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. **Kota ayrıntılarında** aşağıdaki adımları uygulayın:

   1. **Dağıtım modeli** için uygun modeli seçin ve **konumlar** için bir konum seçin.

      ![Kota ayrıntılarını sağlayın](./media/resource-manager-core-quotas-request/quota-details-deployment-rm-locations.png)

   1. Seçilen konum için **türler** altında **bir tür Seç**' i seçin ve **Standart**' ı seçin.

      ![Standart tür seçin](./media/resource-manager-core-quotas-request/quota-details-select-standard-type.png)

      **Türler** altında, çoklu seçim desteğiyle tek bir destek durumundan hem standart hem de spot kota türleri isteyebilirsiniz.

      Spot kota sınırlarını artırma hakkında daha fazla bilgi için bkz. [Sanal Makine Ölçek Kümeleri Için Azure spot VM 'ler](../../virtual-machine-scale-sets/use-spot.md).

   1. **Standart** için kotaları ARTıRMAK istediğiniz SKU serisini seçin.

      ![Kota ayrıntıları-SKU serisi](./media/resource-manager-core-quotas-request/quota-details-standard-select-series.png)

   1. Bu abonelik için istediğiniz yeni kota sınırlarını girin. Listeden bir SKU 'YU kaldırmak için SKU 'nun yanındaki onay kutusunun işaretini kaldırın veya atma "X" simgesini seçin.

      ![Yeni bir vCPU sınırı seçin](./media/resource-manager-core-quotas-request/quota-details-standard-set-vcpu-limit.png)

1. Birden fazla konum için bir kota artışı istemek için, **konumlarda** ek bir konum seçin ve ardından uygun bir VM türü seçin.

   Bu adım, önceki konumlar için seçtiğiniz SKU serisini önceden yükler. Ek seriye uygulamak istediğiniz kota sınırlarını girin.

   ![Kota ayrıntılarında ek konumlar seçin](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. Destek isteği oluşturmaya devam etmek için **Kaydet ' i seçin ve devam edin** .