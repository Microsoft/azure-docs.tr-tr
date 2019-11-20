---
title: Para tasarrufu için Azure Veri Gezgini işaretleme için ön ödeme yapın
description: Azure Veri Gezgini maliyetlerinizi kaydetmek için Azure Veri Gezgini ayrılmış kapasitesini nasıl satın alacağınızı öğrenin.
author: avnera
ms.author: avnera
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/03/2019
ms.openlocfilehash: 66c5644df7d796669105693d08788548334ae93a
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73681633"
---
# <a name="prepay-for-azure-data-explorer-markup-units-with-azure-data-explorer-reserved-capacity"></a>Azure Veri Gezgini ayrılmış kapasitesi ile Azure Veri Gezgini işaretleme birimleri için ön ödeme

Kullandıkça Öde fiyatlarına kıyasla işaretleme birimleri için ön ödeme yaparak Azure Veri Gezgini ile tasarruf edin. Azure Veri Gezgini ayrılmış kapasitesinde Azure Veri Gezgini işaretleme maliyetlerinde önemli bir indirim elde etmek için Azure Veri Gezgini kullanım taahhüdüne bir veya üç yıl boyunca bir taahhüt yaparsınız. Azure Veri Gezgini ayrılmış kapasitesi satın almak için yalnızca terimi belirtmeniz yeterlidir, tüm bölgelerde tüm Azure Veri Gezgini dağıtımları için geçerlidir.

Bir rezervasyon satın alarak, bir veya üç yılda bir dönem için işaretleme maliyetleri için ön ödeme yaparsınız. Bir rezervasyon satın alarak, rezervasyon öznitelikleriyle eşleşen Azure Veri Gezgini işaretleme ücretleri artık Kullandıkça Öde tarifelerine göre ücretlendirilir. Zaten çalışmakta olan veya yeni dağıtılan Azure Veri Gezgini kümeleri otomatik olarak avantaja sahip olur. Bu rezervasyon, kümelerle ilişkili işlem, ağ veya depolama ücretlerini kapsamaz. Bu kaynaklar için ayrılan kapasitenin ayrı olarak satın alınması gerekir. Rezervasyon döneminin sonunda, faturalandırma avantajı sona erer ve Azure Veri Gezgini biçimlendirme birimleri Kullandıkça Öde fiyatı üzerinden faturalandırılır. Ayırmalar otomatik olarak yenilemez. Fiyatlandırma bilgileri için bkz. [Azure Veri Gezgini fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/data-explorer/).

Azure Veri Gezgini ayrılmış kapasitesini [Azure Portal](https://portal.azure.com)satın alabilirsiniz. Azure Veri Gezgini ayrılmış kapasitesini satın almak için:

* En az bir kurumsal veya Kullandıkça Öde aboneliğine sahip olmanız gerekir.
* Kurumsal abonelikler için, **EA portal**’da [Ayrılmış Örnek Ekle](https://ea.azure.com) seçeneği etkinleştirilmelidir. Alternatif olarak, bu ayar devre dışıysa abonelikte bir EA yöneticisi olmanız gerekir.
* Bulut çözümü sağlayıcısı (CSP) programı için, yalnızca yönetici aracıları veya satış aracıları Azure Veri Gezgini ayrılmış kapasitesi satın alabilir.

Kurumsal müşterilerin ve kullandıkça öde müşterilerinin rezervasyon satın alma işlemleri için nasıl ücretlendirilildiği hakkında daha fazla bilgi için bkz.:
* [Kurumsal kaydınız için Azure ayırma kullanımını anlayın](../billing/billing-understand-reserved-instance-usage-ea.md) 
* [Kullandıkça Öde aboneliğiniz Için Azure rezervasyon kullanımını anlayın](../billing/billing-understand-reserved-instance-usage.md).

## <a name="determine-the-right-markup-usage-before-purchase"></a>Satın almadan önce doğru biçimlendirme kullanımını belirleme

Ayırma boyutu, mevcut veya hemen dağıtılmış Azure Veri Gezgini kümeleri tarafından kullanılan Azure Veri Gezgini işaretleme birimlerinin toplam sayısını temel almalıdır. Biçimlendirme birimi sayısı, üretimde Azure Veri Gezgini Engine küme çekirdekleri sayısına eşittir (geliştirme/test SKU 'SU dahil değildir). 

## <a name="buy-azure-data-explorer-reserved-capacity"></a>Azure Veri Gezgini ayrılmış kapasitesi satın alın

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. **Şimdi satın al** > **tüm hizmetler** > **ayırmaları** ' i seçin. **Ekle**’yi seçin
1. **Ürün türü seç** bölmesinde Azure **Veri Gezgini** ' yi seçerek Azure Veri Gezgini işaretleme birimleri için yeni bir ayırma satın alın. 
1. **Satın al** 'ı seçin
1. Gerekli alanları girin. 

    ![Satın alma sayfası](media/pricing-reserved-capacity/purchase-page.png)

1. **Maliyetler** bölümündeki Azure Veri Gezgini işaretleme ayrılmış kapasite rezervasyonunun maliyetini gözden geçirin.
1. **Satın al**'ı seçin.
1. Satın alımınızın durumunu görmek için **Bu ayırmayı görüntüle** ' yi seçin.

## <a name="cancellations-and-exchanges"></a>İptaller ve değişimler

Azure Veri Gezgini ayrılmış kapasite ayırmayı iptal etmeniz gerekirse, %12 erken sonlandırma ücreti olabilir. Para iadesi, satın alma fiyatının en düşük fiyatını veya rezervasyonun geçerli fiyatını temel alır. Para iadesi yıllık 50.000 ABD doları ile sınırlıdır. Yapılacak para iadesi, %12 erken sonlandırma ücreti kesildikten sonra kalan kullanım süresine göre hesaplanır. İptal istemek için Azure portal rezervasyonuna gidin ve bir destek isteği oluşturmak için **para iadesi** ' ni seçin.

Azure Veri Gezgini ayrılmış kapasite ayırmayı başka bir terime değiştirmeniz gerekiyorsa, bu değeri eşit veya daha büyük bir değer olan başka bir rezervasyon için değiş tokuş edebilirsiniz. Yeni ayırma işleminin başlangıç tarihi değiştirilen ayırma işleminin başlangıç tarihiyle aynı olmaz. 1 veya 3 yıllık dönem, yeni ayırmayı oluştururken ' den başlar. Bir Exchange istemek için Azure portal ayırma bölümüne gidin ve bir destek isteği oluşturmak için **Exchange** ' i seçin.

Rezervasyonları Exchange veya para iadesi hakkında daha fazla bilgi için bkz. [rezervasyon alışverişi ve para](../billing/billing-azure-reservations-self-service-exchange-and-refund.md)iadesi.

## <a name="manage-your-reserved-capacity-reservation"></a>Ayrılmış kapasite ayırmayı yönetme

Azure Veri Gezgini biçimlendirme birimleri ayırma indirimi, Azure Veri Gezgini ayrılmış kapasite ayırma kapsamı ve öznitelikleriyle eşleşen biçimlendirme birimi sayısına otomatik olarak uygulanır. 


> [!NOTE]
> * Azure Veri Gezgini ayrılmış kapasite ayırmasının kapsamını [Azure Portal](https://portal.azure.com), POWERSHELL, CLı veya API aracılığıyla güncelleştirebilirsiniz.
> * Azure Veri Gezgini ayrılmış kapasite ayırmayı yönetme hakkında bilgi edinmek için bkz. [azure Veri Gezgini ayrılmış kapasitesini yönetme](../billing/billing-understand-kusto-azuredataexplorer-reservation-charges.md).

## <a name="next-steps"></a>Sonraki adımlar

Azure Ayrılmış Sanal Makine Örnekleri hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

* [Azure Ayrılmış Sanal Makine Örnekleri nedir?](../billing/billing-save-compute-costs-reservations.md)
* [Azure Ayırmalarını yönetme](../billing/billing-manage-reserved-vm-instance.md)
* [Azure Ayrılmış Sanal Makine Örnekleri indirimini anlama](../billing/billing-understand-reservation-charges.md)
* [Kullandıkça Öde aboneliğiniz için rezervasyon kullanımını anlama](../billing/billing-understand-reserved-instance-usage.md)
* [Kurumsal kaydınız için rezervasyon kullanımını anlama](../billing/billing-understand-reserved-instance-usage-ea.md)
* [İş Ortağı Merkezi Bulut Çözümü Sağlayıcısı (CSP) programındaki Azure Ayrılmış Sanal Makine Örnekleri](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Yardım mı gerekiyor? Bizimle iletişim kurun

Sorularınız varsa ya da yardıma gereksinim duyuyorsanız [destek isteği oluşturun](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
