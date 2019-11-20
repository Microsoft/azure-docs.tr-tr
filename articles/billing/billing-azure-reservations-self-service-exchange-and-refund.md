---
title: Azure Ayrılmış Sanal Makine Örnekleri için self servis değişimler ve para iadeleri
description: Azure Ayrılmış Sanal Makine Örnekleri’ni nasıl değiştirebileceğinizi veya para iadesi alabileceğinizi öğrenin.
author: yashesvi
manager: yashesvi
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/05/2019
ms.author: banders
ms.openlocfilehash: 9e3c999de1804684e77a6224330b2d7077046964
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73052165"
---
# <a name="self-service-exchanges-and-refunds-for-azure-reservations"></a>Azure Ayrılmış Sanal Makine Örnekleri için self servis değişimler ve para iadeleri

Azure Ayrılmış Sanal Makine Örnekleri, değişen ihtiyaçlarınızın karşılanmasına yardımcı olma esnekliği sağlar. Bir rezervasyonu aynı türdeki başka bir rezervasyonla değiştirebilirsiniz. İhtiyaç duymadığınız rezervasyonları da iade ederek yılda 50.000 ABD dolarına kadar para iadesi alabilirsiniz.

Self servis değişim ve iptal özelliği US Government Kurumsal Anlaşma müşterileri tarafından kullanılamaz. Kullandıkça Öde ve CSP dahil olmak üzere diğer US Government abonelik türleri desteklenir.

Mevcut rezervasyonu değiştirmek veya para iadesi almak için Rezervasyon Siparişi üzerinde sahip erişiminiz olmalıdır.

## <a name="exchange-an-existing-reserved-instance"></a>Var olan ayrılmış örneği değiştirme

[Azure portalında](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) üç hızlı adımla rezervasyonunuzu değiştirebilirsiniz.

1. Para iadesi almak istediğiniz rezervasyonları seçin ve **Değiştir**’e tıklayın.  
    ![Döndürülecek rezervasyonları gösteren örnek resim](./media/billing-azure-reservations-self-service-exchange-and-refund/exchange-refund-return.png)
2. Satın almak istediğiniz VM ürününü seçip miktar girin. Yeni satın alma toplamının, iade toplamından fazla olduğundan emin olun. [Satın almadan önce doğru boyutu belirleyin](../virtual-machines/windows/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy).  
    ![Değişim ile satın alınacak sanal makine ürününü gösteren örnek resim](./media/billing-azure-reservations-self-service-exchange-and-refund/exchange-refund-select-purchase.png)
3. İşlemi gözden geçirin ve tamamlayın.  
    ![İadenin tamamlandığı, değişim ile satın alınacak sanal makine ürününü gösteren örnek resim](./media/billing-azure-reservations-self-service-exchange-and-refund/exchange-refund-confirm-exchange.png)

Rezervasyon para iadesi almak için **Rezervasyon Ayrıntıları** bölümüne gidip **Para İadesi**’ne tıklayın.

## <a name="how-transactions-are-processed"></a>İşlemler nasıl işlenir?

İlk olarak Microsoft, mevcut rezervasyonu iptal eder ve o rezervasyon için eşit olarak dağıtılan tutarı iade eder. Bir değişim varsa yeni satın alma işleme alınır. Microsoft, hesap türünüze ve ödeme yönteminize bağlı olarak aşağıdaki yöntemlerden birini kullanarak para iadelerini işleme alır:

### <a name="enterprise-agreement-customers"></a>Kurumsal anlaşma müşterileri

İlk satın alma, değişim veya para iadesi kullanılarak yapıldıysa, değişim ve para iadeleri için parasal taahhüde para eklenir. Parasal taahhüdün kullanıldığından emin olmak için, ilk satın almadan itibaren gerçekleşen fazla kullanım faturaları yeniden açılır ve yeniden oranlanır. Satın alınan rezervasyonu kullanan parasal taahhüt dönemi artık etkin değilse kredi, geçerli kurumsal anlaşma parasal taahhüt döneminize eklenir. Kredi, iade tarihinden itibaren 90 gün geçerlidir. Kullanılmayan kredilerin süresi 90 günün sonunda dolar.

İlk satın alma, fazla kullanım olarak gerçekleştiyse Microsoft bir alacak faturası düzenler.

### <a name="pay-as-you-go-invoice-payments-and-csp-program"></a>Kullandıkça öde fatura ödemeleri ve CSP programı

İlk rezervasyon satın alma faturası iptal edilir ve sonra para iadesi için yeni bir fatura oluşturulur. Değişimler için yeni faturada para iadesi ve yeni satın alma işlemi gösterilir. Para iadesi tutarı, satın almaya göre ayarlanır. Yalnızca bir rezervasyon para iadesi aldıysanız, eşit olarak dağıtılan tutar Microsoft’ta kalır ve gelecekteki rezervasyon satın alımına karşı ayarlanır.

### <a name="pay-as-you-go-credit-card-customers"></a>Kullandıkça öde kredi kartı müşterileri

Özgün fatura iptal edilir ve yeni bir fatura oluşturulur. Para, ilk satın almada kullanılan kredi kartına iade edilir. Kartınızı değiştirdiyseniz, [desteğe başvurun](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="cancel-exchange-and-refund-policies"></a>İptal, değişim ve para iadesi ilkeleri

Azure, iptaller, değişimler ve para iadeleri için aşağıdaki ilkelere sahiptir.

**Değişim ilkeleri**

- Aynı türde yeni bir rezervasyon satın almak için birden fazla mevcut rezervasyonu iade edebilirsiniz. Bir türdeki rezervasyonu başka bir türle değiştiremezsiniz. Örneğin, bir SQL rezervasyonu satın almak için sanal makine rezervasyonu iade edemezsiniz.
- Yalnızca rezervasyon sahipleri bir değişimi işleme alabilir. [Rezervasyonu yönetebilecek kullanıcıları ekleme veya değiştirme hakkında bilgi edinin](billing-manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).
- Değişim, para iadesi veya yeniden satın alma olarak işleme alınır. İptal ve yeniden satın alma için farklı işlemler oluşturulur. Eşit olarak dağıtılan rezervasyon tutarı, takas yaptığınız rezervasyonlar için iade edilir. Yeni satın alma için tam ücret size yansıtılır. Eşit olarak dağıtılan rezervasyon tutarı, iade edilen rezervasyonun günlere eşit olarak dağıtılan kalan değeridir.
- Rezervasyonu satın almak için kullanılan kurumsal anlaşmanın süresi dolup yeni bir anlaşma olarak yenilenmiş olsa da rezervasyonları değiştiremez veya para iadesi alamazsınız.
- Boyut, bölge, miktar ve süre gibi herhangi bir rezervasyon özelliğini değişim ile değiştirebilirsiniz.
- Yeni satın alma toplamı, iade edilen tutarla aynı veya daha fazla olmalıdır.
- Değişim kapsamında satın alınan yeni rezervasyonlar, değişim anından başlayan yeni bir döneme sahip olur.
- Değişimler için ceza veya yıllık limit yoktur.

**Para iadesi ilkeleri**
- Bir rezervasyonu iptal ederseniz %12 erken sonlandırma ücreti tahsil edilebilir.
- İptal için alacağınız para iadesi, kalan eşit olarak dağıtılan bakiyeden %12 erken sonlandırma ücreti kesilerek elde edilir. İptal etmek için Azure portalında rezervasyona gidin ve **Para iadesi** seçeneğini belirleyin.
- Toplam para iadesi tutarı, 12 aylık zaman penceresinde 50.000 ABD dolarını aşamaz.
- Para iadeleri, satın aldığınız fiyattan veya geçerli rezervasyon fiyatından düşük olana göre hesaplanır.
- Yalnızca rezervasyon sahipleri para iadesini işleme alabilir. [Rezervasyonu yönetebilecek kullanıcıları ekleme veya değiştirme hakkında bilgi edinin](billing-manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).
- Microsoft, tüm iadeler için %12 ceza uygulama hakkını saklı tutar. Ceza şu anda alınmaz, ancak gelecekte ücretlendirilecektir.

## <a name="exchange-non-premium-storage-for-premium-storage"></a>Premium depolama için premium olmayan depolama değişimi

Premium depolamayı desteklemeyen bir sanal makine boyutu için satın alınan bir rezervasyonu, Premium depolamayı destekleyen ilgili bir sanal makine boyutuyla değiştirebilirsiniz. Örneğin, _F1_ ile _F1s_. Değişim yapmak için Rezervasyon Ayrıntıları’na gidin ve **Değiştir**’e tıklayın. Değiştir işlemi sonucunda, ayrılmış örneğin süresi sıfırlanmaz veya yeni bir işlem oluşturulmaz.

## <a name="need-help-contact-us"></a>Yardıma mı ihtiyacınız var? Bize ulaşın.

Sorularınız varsa ya da yardıma gereksinim duyuyorsanız [destek isteği oluşturun](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Sonraki adımlar

- Rezervasyonu yönetme hakkında bilgi edinmek için bkz. [Azure Ayrılmış Sanal Makine Örnekleri’ni Yönetme](billing-manage-reserved-vm-instance.md).
- Azure Ayrılmış Sanal Makine Örnekleri hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:
    - [Azure Ayrılmış Sanal Makine Örnekleri nedir?](billing-save-compute-costs-reservations.md)
    - [Azure’da Rezervasyonları Yönetme](billing-manage-reserved-vm-instance.md)
    - [Rezervasyon indiriminin nasıl uygulandığını anlama](billing-understand-vm-reservation-charges.md)
    - [Kullandıkça Öde aboneliğiniz için rezervasyon kullanımını anlama](billing-understand-reserved-instance-usage.md)
    - [Kurumsal kaydınız için rezervasyon kullanımını anlama](billing-understand-reserved-instance-usage-ea.md)
    - [Rezervasyonlara dahil olmayan Windows yazılımı maliyetleri](billing-reserved-instance-windows-software-costs.md)
    - [İş Ortağı Merkezi Bulut Çözümü Sağlayıcısı (CSP) programındaki Azure Ayrılmış Sanal Makine Örnekleri](/partner-center/azure-reservations)
