---
title: Azure Danışmanı için öneri Özeti
description: Etkin önerileriniz için düzenli Özet alın
ms.topic: article
ms.date: 03/16/2020
ms.author: sagupt
ms.openlocfilehash: 1c82abf3c8bea07941f860b94fcfe92954fb7641
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100579820"
---
# <a name="configure-periodic-summary-for-recommendations"></a>Öneriler için düzenli Özet yapılandırma

Danışman **önerisi** öngörüleri, farklı kategorilerde etkin önerilerinizi en üst düzeyde takip etmenin kolay ve proaktif bir yolunu sunar. Özelliği, farklı kategorilerde tüm etkin önerileriniz Özet için düzenli bildirimleri yapılandırma olanağı sağlar. İşlem gruplarını kullanarak e-posta, SMS veya diğerleri gibi bildirimler için istediğiniz kanalı seçebilirsiniz. Bu makalede, Advisor önerileriniz için bir **öneri özetleri** ayarlama gösterilmektedir.


## <a name="setting-up-your-recommendation-digest"></a>Öneri özetinizi ayarlama 

**Öneri Özeti** oluşturma deneyimi, Özeti yapılandırmanıza yardımcı olur. Yapılandırma parametreleri için aşağıdaki parametreleri seçebilirsiniz:
1. Kategori: maliyet, yüksek kullanılabilirlik, performans ve işlemsel üstün kullanım gibi öneriler kategorileridir. Yetenek henüz güvenlik önerileri için kullanılamaz.
2. Özet sıklığı: Özet bildirimlerin sıklığı haftalık, iki haftada bir ve aylık olabilir.
3. Eylem grubu: mevcut bir eylem grubunu seçebilir ya da yeni bir eylem grubu oluşturabilirsiniz. Eylem grupları hakkında daha fazla bilgi edinmek için bkz. [eylem grupları oluşturma ve yönetme](../azure-monitor/alerts/action-groups.md).
4. Özet için dil
5. Öneri Özeti adı: daha iyi izleme ve izleme için Kullanıcı dostu bir dize kullanabilirsiniz.

## <a name="steps-to-create-recommendation-digest-in-azure-portal"></a>Azure portal öneri Özeti oluşturma adımları

**Öneri Özeti** oluşturma adımları aşağıda verilmiştir:
* **1. Adım:** Azure portal **Advisor** ' a gidin ve **izleme** bölümünde **öneri Özeti** ' ni seçin. 

   ![Öneri Özeti giriş noktası](./media/digest-0.png)

* **2. Adım:** Üstteki çubuktan **yeni öneri Özeti ' ni** aşağıda gösterildiği gibi seçin:

   ![Öneri Özeti oluşturma](./media/digest-5.png)

* **Adım 3:** **Kapsam** bölümünde, Özet için **abonelik** seçin

   ![Öneri Özeti girişleri sağlama](./media/digest-1.png)

* **4. Adım:** **Koşul** bölümünde **Kategori**, **Sıklık** ve **dil** gibi konfigürasyonları seçin

   ![Öneri Özeti giriş koşulları sağlama](./media/digest-2.png)

* **5. Adım:** **Eylem grubu** bölümünde, Özet için **Eylem grubunu** seçin. Buradan daha fazla bilgi edinebilirsiniz- [eylem grupları oluşturabilir ve yönetebilirsiniz](../azure-monitor/alerts/action-groups.md)

   ![Öneri Özeti giriş eylemi grubu sağlama](./media/digest-3.png)

* **Adım 6:** **Özet ayrıntıları** için bu son bölümde, öneri özetinizdeki adı ve durumu atayabilirsiniz. Kurulumu tamamladıktan sonra **öneri Özeti oluştur** ' a basın.
   ![Tüm öneri Özeti oluşturma](./media/digest-4.png)

## <a name="next-steps"></a>Sonraki adımlar

Danışman önerileri hakkında daha fazla bilgi için bkz.
* [Azure Danışmanı’na Giriş](advisor-overview.md)
* [Danışman'ı kullanmaya başlama](advisor-get-started.md)
* [Danışman maliyet önerileri](advisor-cost-recommendations.md)
* [Advisor performans önerileri](advisor-performance-recommendations.md)
* [Danışman güvenlik önerileri](advisor-security-recommendations.md)
* [Danışman Işletimsel üstün öneriler](advisor-operational-excellence-recommendations.md)
* [Danışman REST API](/rest/api/advisor/)
