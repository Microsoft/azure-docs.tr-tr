---
title: Azure rezervasyon önerileri
description: Azure rezervasyon önerileri hakkında bilgi edinin.
author: banders
ms.author: banders
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: d70580a34e832d6465571adbc8f0524abeba609a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767934"
---
# <a name="reservation-recommendations"></a>Rezervasyon önerileri

Azure ayrılmış örnek (RI) satın alma önerileri Azure Tüketimi [Rezervasyon Önerileri API’si](/rest/api/consumption/reservationrecommendations), [Azure Danışmanı](../../advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs) ve Azure portaldaki rezervasyon satın alma deneyimi aracılığıyla sağlanır.

Aşağıdaki adımlar önerilerin nasıl hesaplanacağını tanımlar:

1. Öneri altyapısı son 7, 30 ve 60 gün içinde verilen kapsamdaki kaynaklarınızın saatlik kullanımını değerlendirir.
2. Kullanım verilerine bağlı olarak altyapı, rezervasyonlar ile ve rezervasyonlar olmadan maliyetlerinizin simülasyonunu yapar.
3. Farklı miktarlar için maliyet simülasyonu yapılır ve tasarruf miktarını en üst düzeye çıkaran miktar önerilir.
4. Kaynaklarınız düzenli olarak kapatılıyorsa simülasyon hiçbir tasarruf olanağı bulamaz ve herhangi bir satın alma önerisi sağlanmaz.
5. Öneri hesaplamaları, isteğe bağlı kullanım hızlarınızda sahip olabileceğiniz özel indirimleri içerir.

## <a name="recommendations-in-the-azure-portal"></a>Azure portalındaki öneriler

Satın alma deneyiminde, Azure portalında rezervasyon satın alma önerileri gösterilir. Öneriler, **Önerilen Miktar** ile birlikte gösterilir. Azure'ın önerdiği miktar satın alındığında mümkün olan en yüksek tasarruf elde edilir. İstediğiniz miktarı satın alabilirsiniz, ancak farklı bir miktar satın alırsanız tasarruflar ideal olmayacaktır.

Şimdi bunun nedenini bazı örneklerle gösterelim.

Seçili öneriyi gösteren aşağıdaki örnek resimde Azure 6 adet satın alınmasını öneriyor.

:::image type="content" source="./media/reserved-instance-purchase-recommendations/recommended-quantity.png" alt-text="Rezervasyon satın alma önerisini gösteren örnek" lightbox="./media/reserved-instance-purchase-recommendations/recommended-quantity.png" :::

**Ayrıntıları görüntüle**' yi seçtiğinizde öneri hakkında daha fazla bilgi görüntülenir. Aşağıdaki resimde öneri hakkındaki ayrıntılar gösterilir. Önerilen miktar, mümkün olan en yüksek kullanım için hesaplanır ve geçmiş kullanımınıza göre belirlenir. Kullanımınız tutarsızsa, öneriniz %100 kullanım için hesaplanmamış olabilir. Örnekte, kullanımın zaman içinde dalgalanulduğuna dikkat edin. Rezervasyon maliyeti, olası tasarruflar ve kullanım yüzdesi gösterilir.

:::image type="content" source="./media/reserved-instance-purchase-recommendations/recommended-quantity-details.png" alt-text="Bir rezervasyon satın alma önerisi için ayrıntıları gösteren örnek " :::

Önerilen miktarı artırdığınızda grafik ve tahmini değerler değişir. Rezervasyon miktarını artırarak tasarrufunuzun azalmasıyla, daha az rezervasyon kullanımı azaltacak. Diğer bir deyişle tam olarak kullanılmayan rezervasyonlar için ödeme yaparsınız.

Rezervasyon miktarını düşürürseniz tasarruflarınız da azaltılır. Kullanımınız artsa da, rezervasyonlarınızın kullanımınızı tam olarak karşılamadığı dönemler olacaktır. Rezervasyon miktarınızı aşan kullanımda, daha pahalı olan kullandıkça öde kaynakları kullanılır. Aşağıdaki örnek resimde bu nokta gösterilir. Rezervasyon miktarını kendimiz 4'e düşürdük. Rezervasyon kullanımı artmıştır, ancak Kullandıkça Öde maliyetleri mevcut olduğundan genel tasarruf azalır.

:::image type="content" source="./media/reserved-instance-purchase-recommendations/recommended-quantity-details-changed.png" alt-text="Değişen rezervasyon satın alma önerisinin ayrıntılarını gösteren örnek" :::

Rezervasyonlardan yapılan tasarrufları en üst düzeye çıkarmak için mümkün olduğunca öneriye yakın miktarda rezervasyon satın almaya çalışın.

## <a name="recommendations-in-azure-advisor"></a>Azure Danışmanı’nda Öneriler

Rezervasyon satın alma önerileri Azure Danışmanı’nda kullanıma sunulmuştur. Aşağıdaki noktaları göz önünde bulundurun:

- Danışman, yalnızca tek abonelik kapsamında öneriler sunar. Tüm faturalandırma kapsamının (faturalandırma hesabı veya faturalandırma profili) önerilerini görmek isterseniz:
  -  Azure Portal, **rezervasyonlar**  >  **Ekle** ' ye gidin ve önerilerini görmek istediğiniz türü seçin.
- Danışmandaki öneriler, son 30 günlük kullanım eğilimi göz önünde bulundurun.
- Önerilerin miktarı ve tasarrufları, kullanılabilir olduğunda üç yıllık rezervasyon için geçerlidir. Hizmet için üç yıllık rezervasyon satılmazsa, öneri bir yıllık rezervasyon fiyatı kullanılarak hesaplanır.
- Öneri hesaplamaları, isteğe bağlı kullanım hızlarınızda sahip olabileceğiniz özel indirimleri içerir.
- Paylaşılan kapsam rezervasyonu satın alırsanız, danışman rezervasyon satın alma önerilerinin en fazla beş gün sürme önerisi olabilir.

## <a name="other-expected-api-behavior"></a>Diğer beklenen API davranışı

- Yedi günlük bir inceleme süresi kullanıldığında, VM’lerin bir günden fazla kapalı kalması durumunda öneri sunulmayabilir.

## <a name="next-steps"></a>Sonraki adımlar
- [REST API 'leri kullanarak rezervasyon önerilerini](/rest/api/consumption/reservationrecommendations/list)alın.
- [Azure rezervasyon indirimini sanal makinelere uygulama](../manage/understand-vm-reservation-charges.md) hakkında bilgi edinin.
