---
title: Azure CDN-Verizon Premium kuralları altyapısı ile HTTP davranışını geçersiz kılma
description: Kural altyapısı, belirli içerik türlerinin teslimini engelleme, önbelleğe alma ilkesi tanımlama ve HTTP üstbilgilerini değiştirme gibi Verizon Premium 'dan HTTP isteklerinin nasıl Azure CDN işlendiğini özelleştirmenizi sağlar.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: aa0606eafb8fe4c517b0c18e0137058a120115ba
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082951"
---
# <a name="override-http-behavior-using-the-azure-cdn-from-verizon-premium-rules-engine"></a>Verizon Premium kural altyapısından Azure CDN kullanarak HTTP davranışını geçersiz kılın

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Genel Bakış

Azure CDN Rules altyapısı, HTTP isteklerinin işlenme biçimini özelleştirmenize olanak sağlar. Örneğin, belirli içerik türlerinin teslimini engelleme, önbelleğe alma ilkesi tanımlama veya bir HTTP üst bilgisini değiştirme. Bu öğreticide, CDN varlıklarının önbelleğe alma davranışını değiştiren bir kuralın nasıl oluşturulacağı gösterilmektedir. Kural altyapısı sözdizimi hakkında daha fazla bilgi için bkz. [Azure CDN kuralları altyapısı başvurusu](cdn-verizon-premium-rules-engine-reference.md).

## <a name="access"></a>Access

Kural altyapısına erişmek için, önce Azure CDN yönetim sayfasına erişmek üzere **CDN profili** sayfasının en üstünden **Yönet** ' i seçmeniz gerekir. Uç noktanızın dinamik site hızlandırma (DSA) için iyileştirildiğine bağlı olarak, kurallar altyapısına uç nokta türü için uygun kurallar kümesiyle erişirsiniz:

- Genel Web tesliminde veya diğer DSA olmayan iyileştirmede iyileştirilmiş uç noktalar:
    
    **Http büyük** sekmesini seçin ve ardından **Rules Engine**' i seçin.

    ![HTTP için kural altyapısı](./media/cdn-rules-engine/cdn-http-rules-engine.png)

- DSA için iyileştirilmiş uç noktalar:
    
    **ADN** sekmesini seçin ve ardından **Rules Engine**' i seçin.
    
    ADN, Verizon tarafından DSA içeriğini belirtmek için kullanılan bir terimdir. Burada oluşturduğunuz kurallar, profilinizde DSA için iyileştirilmiş olmayan herhangi bir uç nokta tarafından yok sayılır.

    ![DSA için kural altyapısı](./media/cdn-rules-engine/cdn-dsa-rules-engine.png)

## <a name="tutorial"></a>Öğretici

1. **CDN profili** sayfasında **Yönet**' i seçin.
   
    ![CDN profili Yönet düğmesi](./media/cdn-rules-engine/cdn-manage-btn.png)
   
    CDN yönetim portalı açılır.

2. **Http büyük** sekmesini seçin ve ardından **Rules Engine**' i seçin.
   
    Yeni bir kural için seçenekler görüntülenir.
   
    ![CDN yeni kural seçenekleri](./media/cdn-rules-engine/cdn-new-rule.png)
   
   > [!IMPORTANT]
   > Birden çok kuralın listelenme sırası, bunların nasıl işlendiğini etkiler. Sonraki bir kural, önceki bir kural tarafından belirtilen eylemleri geçersiz kılabilir.
   >

3. **Ad/açıklama** metin kutusuna bir ad girin.

4. Kuralın geçerli olduğu isteklerin türünü belirler. **Her zaman**varsayılan eşleşme koşulunu kullanın.
   
   ![CDN kuralı eşleşme koşulu](./media/cdn-rules-engine/cdn-request-type.png)
   
   > [!NOTE]
   > Açılan listede birden fazla eşleşme koşulu vardır. Şu anda seçili olan eşleştirme koşulu hakkında daha fazla bilgi için, sol tarafında mavi bilgilendirici simgesini seçin.
   >
   >  Koşullu ifadelerin ayrıntılı bir listesi için bkz. [Rules Engine Koşullu ifadeler](cdn-verizon-premium-rules-engine-reference-match-conditions.md).
   >  
   > Eşleşme koşullarının ayrıntılı bir listesi için bkz. [kural altyapısı eşleştirme koşulları](cdn-verizon-premium-rules-engine-reference-match-conditions.md).
   >
   >

5. Yeni bir özellik eklemek için **Özellikler**' in yanındaki **+** düğmesini seçin.  Sol taraftaki açılan listede, **en yüksek kullanım süresini zorla**' yı seçin.  Görüntülenen metin kutusuna **300**girin. Kalan varsayılan değerleri değiştirmeyin.
   
   ![CDN kural özelliği](./media/cdn-rules-engine/cdn-new-feature.png)
   
   > [!NOTE]
   > Açılan listede birden çok özellik bulunur. Şu anda seçili olan özellik hakkında daha fazla bilgi için, sol tarafında mavi bilgilendirici simgesini seçin.
   >
   > **Zorlamalı Iç maksimum yaş**IÇIN, CDN Edge düğümünün varlığı kaynaktan yenilediğinde, varlığın `Cache-Control` ve `Expires` üst bilgileri denetlemek için geçersiz kılınır. Bu örnekte, CDN Edge düğümü, varlığı kaynağın kaynağından yenilemelerinden önce 300 saniye veya 5 dakika boyunca varlığını önbelleğe alır.
   >
   > Özelliklerin ayrıntılı bir listesi için bkz. [Rules Engine özellikleri](cdn-verizon-premium-rules-engine-reference-features.md).
   >
   >

6. Yeni kuralı kaydetmek için **Ekle** ' yi seçin.  Yeni kural artık onay bekliyor. Onaylandıktan sonra durum, **bekleyen XML** 'den **etkin XML**'e değişir.
   
   > [!IMPORTANT]
   > Kuralların Azure CDN yayılması 10 dakika kadar sürebilir.
   >
   >

## <a name="see-also"></a>Ayrıca bkz.

- [Azure CDN genel bakış](cdn-overview.md)
- [Kural altyapısı başvurusu](cdn-verizon-premium-rules-engine-reference.md)
- [Kural altyapısı eşleştirme koşulları](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Kural altyapısı koşullu ifadeleri](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Kural altyapısı özellikleri](cdn-verizon-premium-rules-engine-reference-features.md)
- [Azure Fridays: Azure CDN güçlü yeni Premium özellikler](https://azure.microsoft.com/documentation/videos/azure-cdns-powerful-new-premium-features/) (video)