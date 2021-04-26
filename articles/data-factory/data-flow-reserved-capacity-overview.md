---
title: İşlem maliyetlerini ayrılmış kapasiteye göre Kaydet
description: İşlem maliyetlerinizi kaydetmek için Azure Data Factory veri akışı ayrılmış kapasitesini satın almayı öğrenin.
ms.topic: conceptual
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.date: 02/05/2021
ms.openlocfilehash: 26a4692603d8e8a80a52ea77bdd56617131cea5d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104593909"
---
# <a name="save-costs-for-resources-with-reserved-capacity---azure-data-factory-data-flows"></a>Ayrılmış kapasite Azure Data Factory veri akışları olan kaynaklar için maliyetleri kaydetme

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Kullandıkça Öde fiyatlarıyla karşılaştırıldığında işlem kaynakları için bir ayırmaya girerek Azure Data Factory veri akışı maliyetleriyle tasarruf edin. Ayrılmış kapasite ile, işlem maliyetlerine göre önemli bir indirim elde etmek için bir veya üç yıllık bir dönem için ADF veri akışı kullanımı için bir taahhüt yaparsınız. Ayrılmış kapasiteyi satın almak için Azure bölgesini, işlem türünü, çekirdek sayısı miktarını ve terimi belirtmeniz gerekir.

Ayırmayı belirli bir fabrika veya tümleştirme çalışma zamanına atamanız gerekmez. Mevcut fabrikalar veya yeni dağıtılan fabrikalar otomatik olarak avantajı alır. Bir rezervasyon satın alarak, bir veya üç yıllık bir dönem için veri akışı işlem maliyetlerine yönelik kullanıma de kayıt yapılır. Bir rezervasyon satın alarak, rezervasyon öznitelikleriyle eşleşen işlem ücretleri artık Kullandıkça Öde tarifelerine göre ücretlendirilir. 

[Ayrılan kapasiteyi](https://portal.azure.com) [ön ödemeli veya aylık ödemeler](../cost-management-billing/reservations/prepare-buy-reservation.md)' i seçerek satın alabilirsiniz. Ayrılmış kapasite satın almak için:

- Kullandıkça Öde tarifesine sahip en az bir kuruluş veya ayrı bir abonelik için sahip rolünde olmanız gerekir.
- Kurumsal abonelikler için, [EA portal](https://ea.azure.com)’da **Ayrılmış Örnek Ekle** seçeneği etkinleştirilmelidir. Ya da bu ayar devre dışı bırakılırsa, abonelikte bir EA yöneticisi olmanız gerekir. Ayrılmış kapasite.

Kurumsal müşterilerin ve kullandıkça öde müşterilerinin rezervasyon satın alma işlemleri için nasıl ücretlendirilildiği hakkında daha fazla bilgi için bkz. [Kurumsal kaydınız Için Azure ayırma kullanımını anlama](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) ve [Kullandıkça Öde aboneliğiniz için Azure rezervasyon kullanımını anlama](../cost-management-billing/reservations/understand-reserved-instance-usage.md).

> [!NOTE]
> Satın alma ayrılmış kapasitesi, kullanımınız için belirli altyapı kaynaklarını (sanal makineler veya kümeler) önceden ayırmaz veya ayırmaz.

## <a name="determine-proper-azure-ir-sizes-needed-before-purchase"></a>Satın almadan önce gereken uygun Azure IR boyutlarını belirleme

Ayırma boyutu, aynı işlem katmanını kullanan mevcut veya hemen dağıtılmış veri akışları tarafından kullanılan işlem Toplam miktarına bağlı olmalıdır.

Örneğin, 32 çekirdekle İyileştirilmiş bellek kullanarak saatlik bir işlem hattı yürütülediğinizi varsayalım. Ayrıca, bir sonraki ay içinde, genel amaçlı 64 çekirdekleri kullanan ek bir işlem hattı içinde dağıtmayı planladığınızı varsayalım. Ayrıca, bu kaynaklara en az 1 yıl boyunca ihtiyacınız olacağını bildiğinizi varsayalım. Bu durumda, her bir işlem türü için gereken çekirdek sayısını 1 saat için girin. Azure portalında ayırmalar ' ı arayın. Data Factory > veri akışları ' nı seçin, daha sonra genel amaçlı bellek için iyileştirilmiş ve 64 32 girin.

## <a name="buy-reserved-capacity"></a>Ayrılmış kapasite satın alın

1. [Azure Portal](https://portal.azure.com) oturum açın.
2. **Tüm hizmetler** > **Rezervasyonlar**’ı seçin.
3. **Ekle** ' yi seçin ve ardından **satın alma rezervasyonları** bölmesinde, ADF veri akışları için yeni bir ayırma satın almak üzere **ADF veri akışları** ' nı seçin.
4. Ayrılan kapasite iskontosunu almak için, seçtiğiniz gerekli alanları ve öznitelikleri girin. İndirimin alınacağı gerçek veri akışı sayısı, seçilen kapsama ve miktara bağlıdır.
5. **Maliyetler** bölümündeki kapasite rezervasyonunun maliyetini gözden geçirin.
6. **Satın al**'ı seçin.
7. Satın alımınızın durumunu görmek için **Bu ayırmayı görüntüle** ' yi seçin.

## <a name="cancel-exchange-or-refund-reservations"></a>Rezervasyonları iptal etme, değiştirme veya para iadesi alma

Belirli sınırlamalarla rezervasyonları iptal edebilir, değiştirebilir veya para iadesi alabilirsiniz. Daha fazla bilgi için bkz. [Azure Ayrılmış Sanal Makine Örnekleri için self servis değişimler ve para iadeleri](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="need-help-contact-us"></a>Yardıma mı ihtiyacınız var? Bizimle iletişim kurun

Sorularınız varsa ya da yardıma gereksinim duyuyorsanız [destek isteği oluşturun](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Sonraki adımlar

Azure Ayrılmış Sanal Makine Örnekleri hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

- [Azure Ayrılmış Sanal Makine Örnekleri indirimini anlama](data-flow-understand-reservation-charges.md)
