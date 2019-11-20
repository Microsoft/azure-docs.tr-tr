---
title: Azure Güvenlik Merkezi 'nde güvenlik önerileri | Microsoft Docs
description: Bu belge, Azure Güvenlik Merkezi 'ndeki önerilerin Azure kaynaklarınızı korumanıza ve güvenlik ilkeleriyle uyumlu kalmanıza nasıl yardımcı olduğunu gösterir.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 86c50c9f-eb6b-4d97-acb3-6d599c06133e
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/29/2019
ms.author: memildin
ms.openlocfilehash: 6a01ddbc733ce52c02e9abef2569adbfbd19c6a8
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73520728"
---
# <a name="security-recommendations-in-azure-security-center"></a>Azure Güvenlik Merkezi'nde güvenlik önerileri 
Bu konu başlığı altında, Azure Güvenlik Merkezi 'nde Azure kaynaklarınızı korumanıza yardımcı olan önerilerin nasıl görüntüleneceği ve anlayabileceği açıklanmaktadır.

> [!NOTE]
> Bu belge, örnek bir dağıtım kullanarak hizmeti tanıtır.  Bu belge adım adım kılavuz değildir.
>

## <a name="what-are-security-recommendations"></a>Güvenlik önerileri nelerdir?

Önerileriniz kaynaklarınızın güvenliğini sağlamak için yapmanız gereken eylemlerdir.

Güvenlik Merkezi, olası güvenlik açıklarını belirlemek için Azure kaynaklarınızın güvenlik durumunu düzenli olarak analiz edin. Daha sonra bunları nasıl kaldırabilmeniz için öneriler sağlar.

Her öneri şunları sağlar:

- Neyin önerilmekte olduğuna ilişkin kısa bir açıklama.
- Öneriyi uygulamak için gerçekleştirilecek düzeltme adımları. <!-- In some cases, Quick Fix remediation is available. -->
- Üzerinde önerilen eylemi gerçekleştirmek için gerekli olan kaynakları kullanabilirsiniz.
- Bu öneriyi uygularsanız, güvenli puanınızın gidebileceği miktar olan **güvenli puan etkisi**.

## İzleme önerileri<a name="monitor-recommendations"></a>

Güvenlik Merkezi, olası güvenlik açıklarını belirlemek için kaynaklarınızın güvenlik durumunu analiz eder. **Genel bakış** bölümündeki **öneriler** kutucuğu, Güvenlik Merkezi tarafından tanımlanan toplam öneri sayısını gösterir.

![Güvenlik Merkezi 'ne genel bakış](./media/security-center-recommendations/asc-overview.png)

1. **Genel bakış**' ın altında **öneriler kutucuğunu** seçin. **Öneriler** listesi açılır.

      ![Önerileri görüntüleme](./media/security-center-recommendations/view-recommendations.png)

    Önerilere filtre uygulayabilirsiniz. Önerilere filtre uygulamak için **öneriler** dikey penceresinde **filtre** ' yi seçin. **Filtre** dikey penceresi açılır ve görmek istediğiniz önem derecesini ve durum değerlerini seçersiniz.

   * **Öneriler**: öneri.
   * **GÜVENLI puan etkisi**: güvenlik, güvenlik önerilerinizi kullanarak ve her öneriye ne kadar önemli olduğunu anlamak için gelişmiş algoritmalar uygulayarak oluşturulan bir puandır. Daha fazla bilgi için bkz. [güvenli puan hesaplaması](security-center-secure-score.md#secure-score-calculation).
   * **Kaynak**: Bu önerinin uygulandığı kaynakları listeler.
   * **Durum çubukları**: söz konusu önerinin önem derecesini açıklar:
       * **Yüksek (kırmızı)** : anlamlı bir kaynakta (örneğin, bir uygulama, VM veya ağ güvenlik grubu) bir güvenlik açığı bulunur ve dikkat edilmesi gerekir.
       * **Orta (turuncu)** : bir güvenlik açığı bulunur ve kritik olmayan ya da bir işlemi tamamlamaya yönelik ek adımlar gerekir.
       * **Düşük (mavi)** : giderilmesi gereken ancak anında ilgilenilmesi gerekmeyen bir güvenlik açığı var. (Varsayılan olarak, düşük öneriler sunulmaz, ancak bunları görmek istiyorsanız düşük önerilere filtre uygulayabilirsiniz.) 
       * **Sağlıklı (yeşil)** :
       * **Kullanılamıyor (gri)** :

1. Her bir önerinin ayrıntılarını görüntülemek için, öneriye tıklayın.

    ![Öneri ayrıntıları](./media/security-center-recommendations/recommendation-details.png)

>[!NOTE] 
> Bkz. Azure kaynakları için [Klasik ve Kaynak Yöneticisi dağıtım modelleri](../azure-classic-rm.md) .
 
## <a name="next-steps"></a>Sonraki adımlar

Bu belgede, güvenlik merkezi 'nde güvenlik önerilerine sunulmuştur. Önerileri düzeltme hakkında bilgi edinmek için:

* [Önerileri](security-center-remediate-recommendations.md) düzeltme — Azure abonelikleriniz ve kaynak gruplarınız için güvenlik ilkelerini yapılandırma hakkında bilgi edinin.
