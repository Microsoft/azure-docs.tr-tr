---
title: Peşin veya aylık ödemelerle Azure rezervasyonları satın alma
description: Peşin veya aylık ödemelerle Azure rezervasyonlarını nasıl satın alabileceğinizi öğrenin.
services: billing
author: bandersmsft
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 11/01/2019
ms.author: banders
ms.openlocfilehash: b1cc034b4d397f170b57e873cb9a5d74c91f83f4
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73746452"
---
# <a name="purchase-reservations-with-monthly-payments"></a>Aylık ödemelerle rezervasyonlar satın alma

Şu ana kadar Azure rezervasyonları için peşin ödeme gerekiyordu. Şimdiyse aylık ödemelerle rezervasyonlar için ödeme yapabilirsiniz. Tüm tutarı ödediğiniz peşin satın almadan farklı olarak aylık ödeme seçeneği, rezervasyonun toplam maliyetini dönemin her ayına eşit olarak böler. Peşin ve aylık rezervasyonların toplam maliyeti aynıdır ve aylık ödemeyi seçtiğinizde ekstra ücret ödemezsiniz.

Aylık ödeme tutarınız, yerel para biriminiz için güncel ayın döviz kuruna bağlı olarak değişiklik gösterebilir.

Şunlar için aylık ödemeler kullanılabilir:

- Sanal makineler
- Azure Storage
- SQL Veritabanı
- SQL Veri Ambarı
- Cosmos DB
- App Service damga pulu ücreti

[Azure portalında](https://portal.azure.com/?Microsoft_Azure_Reservations_EnableMultiCart=true&amp;paymentPlan=true#blade/Microsoft_Azure_Reservations/CreateBlade) rezervasyon satın alın.

![Rezervasyon satın almayı gösteren örnek](./media/billing-monthly-payments-reservations/purchase-reservation.png)

Bir rezervasyon satın alma işlemi yaparken ödeme planını görüntüleyebilirsiniz. **Tam ödeme planını görüntüle** seçeneğine tıklayın.

![Rezervasyon ödeme planını gösteren örnek](./media/billing-monthly-payments-reservations/prepurchase-schedule.png)

Satın alma işleminden sonra ödeme planını görüntülemek için bir rezervasyon seçin, **Rezervasyon sipariş kimliği**‘ne ve sonra **Ödemeler** sekmesine tıklayın.

## <a name="view-payments-made"></a>Yapılan ödemeleri görüntüleme

API’ler, kullanım verileri ve maliyet analizi kullanılarak yapılan ödemeleri görüntüleyebilirsiniz. Aylık olarak ödenen rezervasyonlar için sıklık değeri, kullanım verileri ve Rezervasyon Ücretleri API’sinde **yinelenen** olarak gösterilir. Peşin ödenen rezervasyonlar için değer, **tek seferlik** olarak gösterilir.

Maliyet analizi, varsayılan görünümde aylık satın almaları gösterir. Tüm satın almaları görmek istiyorsanız, **Masraf türü** için **satın alma** filtresini ve **Sıklık** için **yinelenen** filtresini uygulayın. Yalnızca rezervasyonları görüntülemek istiyorsanız, **Rezervasyon** için bir filtre uygulayın.

![Maliyet analizinde rezervasyon satın alma maliyetlerini gösteren örnek](./media/billing-monthly-payments-reservations/cost-analysis.png)

## <a name="switch-to-monthly-payments-at-renewal"></a>Yenileme sırasında aylık ödemelere geçme

Bir rezervasyonu yenilediğinizde, faturalama sıklığını aylık olarak değiştirebilirsiniz.

## <a name="exchange-and-refunds"></a>Değiştirme ve para iadeleri

Diğer rezervasyonlar gibi, aylık faturalama ile satın alınan rezervasyonları değiştirebilir veya para iadesi alabilirsiniz. Şu anda aylık faturalama ile satın alınan bir rezervasyon için değişim veya para iadesi başlatmak amacıyla bir destek isteği gönderebilirsiniz.

Aylık olarak ödenen bir rezervasyonu değiştirdiğinizde, yeni satın almanın toplam yaşam süresi maliyeti, iade edilen rezervasyon için iptal edilen kalan ödemelerden fazla olmalıdır. Değişimler için başka bir limit veya ücret yoktur. Aylık olarak faturalandırılan yeni bir rezervasyon satın almak için peşin ödenen bir rezervasyonu değiştirebilirsiniz. Ancak yeni rezervasyonun yaşam süresi değeri, iade edilen rezervasyonun eşit olarak dağıtılan değerinden büyük olmalıdır.

Aylık olarak ödenen bir rezervasyon iptal ediliyorsa Microsoft, iptal edilen gelecekteki taahhüt edilen ödemelere bir iptal ücreti uygulayabilir. Kalan taahhüt edilen ödemeler 50.000 ABD Doları para iadesi limitine tahakkuk eder.

Değişim ve para iadeleri hakkında daha fazla bilgi için bkz. [Azure Ayrılmış Sanal Makine Örnekleri için self servis değişimler ve para iadeleri](billing-azure-reservations-self-service-exchange-and-refund.md).

## <a name="next-steps"></a>Sonraki adımlar

- Rezervasyonlar hakkında daha fazla bilgi edinmek için bkz. [Azure Ayrılmış Sanal Makine Örnekleri nedir?](billing-save-compute-costs-reservations.md)
- Rezervasyon satın almadan önce gerçekleştirmeniz gereken görevler hakkında bilgi edinmek için bkz. [Satın almadan önce doğru sanal makine boyutunu belirleme](../virtual-machines/windows/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy)
