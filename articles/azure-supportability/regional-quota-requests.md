---
title: Azure bölgesel kota artışı istekleri | Microsoft Docs
description: Bölgesel kota artışı istekleri
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: e73f22b0e617ad8f20b98c3bb0fb1647bf5fe61d
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72249086"
---
# <a name="total-regional-vcpu-limit-increase"></a>Toplam bölgesel vCPU sınır artışı 

Sanal makineler ve sanal makine ölçek kümeleri için Kaynak Yöneticisi vCPU kotaları, her bir bölgede her bir abonelik için iki katmanda zorlanır. 

İlk katman **Toplam bölgesel vCPU sayısı sınırı** (tüm VM serileri genelinde) ve ıkıncı katman **VM Serisi vCPU sayısı sınırı** (D serisi vCPU 'lar gibi). Yeni bir VM 'nin dağıtılması her seferinde, bu VM Serisi için yeni ve var olan vCPU 'ların toplamı, söz konusu VM Serisi için onaylanan vCPU kotasını aşmamalıdır. Ayrıca, tüm VM dizileri genelinde dağıtılan toplam yeni ve var olan vCPU sayısı, abonelik için onaylanan toplam bölgesel vCPU kotasını aşmamalıdır. Bu kotalardan biri aşılırsa, VM dağıtımına izin verilmez.
Azure portal VM Serisi için vCPU kota sınırı artışı isteyebilirsiniz. VM Serisi kotasının bir artışı toplam bölgesel vCPU sayısını aynı tutara göre otomatik olarak arttırır. 

Yeni bir abonelik oluşturulduğunda, varsayılan toplam bölgesel sanal CPU 'Lar, tüm tek VM serileri için varsayılan vCPU kotaları toplamına eşit olmayabilir. Bu, dağıtmak istediğiniz her bir VM Serisi için yeterli kotaya sahip bir aboneliğe neden olabilir, ancak tüm dağıtımlar için toplam bölgesel vCPU 'Lara yetecek kadar kota elde etmez. Bu durumda, toplam bölgesel vCPU sınırını açıkça artırmak için bir istek göndermeniz gerekir. Toplam bölgesel vCPU sayısı, bölgenin tüm VM serileri genelinde onaylanan kotanın toplamını aşamaz.

[Sanal makine vCPU kotaları sayfasında](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) ve [Azure aboneliği ve hizmet limitleri](https://aka.ms/quotalimits) sayfasında kotalar hakkında daha fazla bilgi edinin. 

Artık **Yardım + Destek** dikey penceresi veya portalda **kullanımlar + kota** dikey penceresi aracılığıyla bir artış isteğinde bulunabilir. 

## <a name="request-total-regional-vcpus-quota-increase-at-subscription-level-using-the-help--support-blade"></a>**Yardım + Destek** dikey penceresini kullanarak abonelik düzeyinde toplam bölgesel vCPU kotası artışı iste

Azure portal Azure 'un ' yardım + destek ' dikey penceresi aracılığıyla bir destek isteği oluşturmak için aşağıdaki yönergeleri izleyin. 

1. @No__t-0 ' dan **Yardım + Destek**' i seçin.

![Yardım ve destek](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  **Yeni destek isteği**’ni seçin. 

![Yeni destek isteği](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. Sorun türü açılır listesinde **hizmet ve abonelik limitleri (kotalar)** öğesini seçin.

![Sorun türü açılan kutusu](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. Kotasını artırmanız gereken aboneliği seçin.

![Abonelik newSR 'yi seçin](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. **Kota türü** açılan listesinde **diğer istekleri** seçin.

![QuotaType](./media/resource-manager-core-quotas-request/regional-quotatype.png)

6. **Ayrıntılar** bölmesinde, isteğinizi işleme ve servis talebi oluşturmaya devam etme hakkında daha fazla bilgi için aşağıdaki örneğe göre ek bilgiler sağlayın. 
    1.  **Dağıtım modeli** : ' Kaynak Yöneticisi ' belirtin
    2.  **İstenen bölge** – gerekli bölgenizi belirtin, örn. Doğu ABD 2
    3.  **Yeni sınır değeri** : yeni bölge sınırı belirtin. Bu abonelik için tek tek SKU aileleri için onaylanan kotanın toplamı aşmamalıdır

![QuotaDetails](./media/resource-manager-core-quotas-request/regional-details.png)

## <a name="request-total-regional-vcpus-quota-increase-at-subscription-level-using-the-usages--quota-blade"></a>**Kullanımlar + kota** dikey penceresini kullanarak abonelik düzeyindeki toplam bölgesel vCPU kotası artışı iste

Azure 'un ' kullanım + kota ' dikey penceresi aracılığıyla Azure portal kullanılabilir bir destek isteği oluşturmak için aşağıdaki yönergeleri izleyin. 

1. @No__t-0 ' dan **abonelikler**' i seçin.

![Abonelikler](./media/resource-manager-core-quotas-request/subscriptions.png)

2. Kotasını artırmanız gereken aboneliği seçin.

![Abonelik seçme](./media/resource-manager-core-quotas-request/select-subscription.png)

3. **Kullanım + kotalar** ' ı seçin

![Kullanım ve kotaları seçin](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. Sağ üst köşede **istek artışı**' nı seçin.

![İstek artışı](./media/resource-manager-core-quotas-request/request-increase.png)

5. **Kota türü** açılan listesinde **diğer istekleri** seçin.

![QuotaType](./media/resource-manager-core-quotas-request/regional-quotatype.png)

6. **Ayrıntılar** bölmesinde, isteğinizi işleme ve servis talebi oluşturmaya devam etme hakkında daha fazla bilgi için aşağıdaki örneğe göre ek bilgiler sağlayın. 
    1.  **Dağıtım modeli** : ' Kaynak Yöneticisi ' belirtin
    2.  **İstenen bölge** – gerekli bölgenizi belirtin, örn. Doğu ABD 2
    3.  **Yeni sınır değeri** : yeni bölge sınırı belirtin. Bu abonelik için tek tek SKU aileleri için onaylanan kotanın toplamı aşmamalıdır

![QuotaDetails](./media/resource-manager-core-quotas-request/regional-details.png)



