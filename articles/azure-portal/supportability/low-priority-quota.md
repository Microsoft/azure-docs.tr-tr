---
title: Sanal makine kotasını spot
description: Azure 'un VM 'Leri gerektiği şekilde kaldırmasına izin vermek için Exchange 'de daha düşük maliyetleri kabul etmenize olanak tanıyan bir Azure kullanımı modeli sağlayan, spot VM 'Ler için kota sınırlarını artırın.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: how-to
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: c8b9a2251d7923fe1919b7b934f6c97877cd5b37
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96745494"
---
# <a name="spot-quota-increase-limits-for-all-vm-series"></a>Spot kota: tüm VM serileri için sınırları artır

Spot sanal makineler (VM 'Ler), farklı bir Azure kullanımı modeli sağlar. Kullandıkça Öde veya ayrılmış VM örneği dağıtımları için gerektiğinde Azure 'un sanal makinelerini kaldırmasına izin vermek üzere Exchange 'de daha düşük maliyetleriniz olduğunu varsayalım. Spot VM 'Ler hakkında daha fazla bilgi için bkz. [Sanal Makine Ölçek Kümeleri Için Azure spot VM 'ler](../../virtual-machine-scale-sets/use-spot.md).

Azure Resource Manager sanal makineler için iki tür vCPU kotasını destekler:

* *Kullandıkça Öde VM 'leri* ve *ayrılmış VM örnekleri* , *Standart bir vCPU kotasına* tabidir.
* *Spot VM 'ler* bir *spot vCPU kotasına* tabidir.

Nokta vCPU kota türü için Kaynak Yöneticisi vCPU kotaları, tüm kullanılabilir sanal makine serileri genelinde tek bir bölgesel sınır olarak zorlanır.

Yeni bir spot VM 'yi her dağıttığınızda, tüm spot sanal makine örneklerinin toplam yeni ve var olan vCPU kullanımı, onaylanan nokta vCPU kota sınırını aşmamalıdır. Spot kota aşılırsa, spot VM dağıtımına izin verilmez.

Bu makalede, Azure portal kullanarak, nokta vCPU kota limiti artışının nasıl isteneceğini ele alınmaktadır.

Standart vCPU kotaları hakkında daha fazla bilgi edinmek için bkz. [sanal makine vCPU kotaları](../../virtual-machines/windows/quotas.md) ve [Azure aboneliği ve hizmet limitleri, Kotalar ve kısıtlamalar](../../azure-resource-manager/management/azure-subscription-service-limits.md).

Bölgeye göre vCPU sınırını artırma hakkında bilgi edinmek için bkz. [Standart kota: bölgeye göre sınırları artırma](regional-quota-requests.md).

## <a name="request-a-quota-limit-increase-from-help--support"></a>Yardım + Destek 'ten bir kota limiti isteği artırın

**Yardım + Destek** kullanarak tüm sanal makine dizileri için bir spot kota sınırı artışı istemek için:

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

1. **Kota ayrıntıları**' nda aşağıdaki adımları uygulayın:

   1. **Dağıtım modeli** için uygun modeli seçin ve **konumlar** için bir konum seçin.

      ![Ek kota ayrıntıları sağlayın](./media/resource-manager-core-quotas-request/quota-details-deployment-locations.png)

   1. Seçilen konum için **türler** altında, **tür seçin** bölümünde, **nokta**' ı seçin.

      ![Nokta türü seçin](./media/resource-manager-core-quotas-request/select-spot-type.png)

       **Türler** altında, çoklu seçim desteğiyle tek bir destek durumundan hem standart hem de spot kota türleri isteyebilirsiniz.

       Daha fazla bilgi için bkz. [Standart kota: VM serisine göre limitleri artırma](per-vm-quota-requests.md).

   1. Bu abonelik için istediğiniz yeni kota sınırını girin.

      ![Spot VM için yeni bir kota seçin](./media/resource-manager-core-quotas-request/spot-set-new-quota.png)

1. Birden fazla konum için bir kota artışı istemek için, **konumlarda** ek bir konum seçin ve ardından uygun bir VM türü seçin. Daha sonra ek konum için geçerli olan bir sınır girebilirsiniz.

   ![Kota ayrıntılarında ek konumlar belirtin](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. Destek isteği oluşturmaya devam etmek için **Kaydet ' i seçin ve devam edin** .

## <a name="request-a-quota-limit-increase-from-the-subscriptions-pane"></a>Abonelik bölmesinden bir kota sınırı artışı iste

**Abonelikler** BÖLMESINDEN tüm VM serileri için bir spot kota sınırı artışı istemek için:

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

1. **İleri ' yi seçin:** **Sorun ayrıntılarını** açma çözümleri. Ek bilgi girmek için **Ayrıntılar sağla** ' yı seçin. **Kota ayrıntıları**' nda aşağıdaki bilgileri girin:

   1. **Dağıtım modeli** için uygun modeli seçin ve **konumlar** için bir konum seçin.

      ![Kota ayrıntılarını sağlayın](./media/resource-manager-core-quotas-request/quota-details-deployment-locations.png)

   1. Seçilen konum için **türler** altında, **tür seçin** bölümünde, **nokta**' ı seçin.

      ![Nokta türü seçin](./media/resource-manager-core-quotas-request/select-spot-type.png)

      Daha fazla bilgi için bkz. [Standart kota: VM serisine göre limitleri artırma](per-vm-quota-requests.md).

   1. Bu abonelik için istediğiniz yeni kota sınırını girin.

      ![VCPU sınırı için yeni bir değer girin](./media/resource-manager-core-quotas-request/spot-set-new-quota.png)

1. Birden fazla konum için bir kota artışı istemek için, **konumlarda** ek bir konum seçin ve ardından uygun bir VM türü seçin. Daha sonra ek konum için geçerli olan bir sınır girebilirsiniz.

   ![Kota ayrıntılarında ek konumlar seçin](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. Destek isteği oluşturmaya devam etmek için **Kaydet ' i seçin ve devam edin** .
