---
title: Yazılım planları için ön ödeme-Azure ayırmaları
description: Kullandıkça Öde maliyetlerinizden tasarruf etmek için yazılım planlarına ön ödeme yapma hakkında bilgi edinin.
author: bandersmsft
manager: yashesvi
ms.service: virtual-machines
ms.subservice: reserved-instances
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: banders
ms.openlocfilehash: 136207afef3fef9266fadcea1e67bb0aa6a38a32
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102549762"
---
# <a name="prepay-for-azure-software-plans"></a>Azure yazılım planları için ön ödeme yapma

Azure 'da SUSE ve Redhsize yazılım kullanımınız için ön ödeme yaptığınızda, Kullandıkça Öde maliyetlerinizden tasarruf edebilirsiniz. İndirimler, sanal makine kullanımında değil, yalnızca SUSE ve RedHat ölçümleri için geçerlidir. Daha fazla tasarruf için sanal makineler için ayırmaları ayrı olarak satın alabilirsiniz.

Azure portal SUSE ve RedHat yazılım planlarını satın alabilirsiniz. Bir plan satın almak için:

- Kullandıkça Öde fiyatlandırmasına sahip en az bir kurumsal veya ayrı bir abonelik için sahip rolüne sahip olmanız gerekir.
- Kurumsal abonelikler için, [EA portal](https://ea.azure.com/)’da **Ayrılmış Örnek Ekle** seçeneği etkinleştirilmelidir. Ayar devre dışı bırakılmışsa, abonelik için bir EA yöneticisi olmanız gerekir.
- Bulut çözümü sağlayıcısı (CSP) programı için, yönetici aracıları veya satış aracıları yazılım planlarını satın alabilir.

## <a name="buy-a-software-plan"></a>Yazılım planı satın alın

1. Azure portal oturum açın ve [ayırmalar](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)' a gidin.
2. **Ekle** ' ye tıklayın ve ardından satın almak istediğiniz yazılım planını seçin.
Gerekli alanları girin. Satın alma yaptığınız özelliklerin öznitelikleriyle eşleşen SUSE Linux VM veya RedHat VM 'si indirimi alır. İndirimin alınacağı gerçek dağıtım sayısı, seçilen kapsama ve miktara bağlıdır.
3. Bir abonelik seçin. Plan için ödeme yapmak üzere kullanılır.
Abonelik ödeme yöntemi, rezervasyonun ön maliyetlerine göre ücretlendirilir. Abonelik türü bir Kurumsal Anlaşma (teklif numaraları: MS-AZR-0017P veya MS-AZR-0148P) veya kullandıkça öde fiyatlandırması (teklif numaraları: MS-AZR-0003P veya MS-AZR-0023P) içeren tek bir anlaşma olmalıdır.
    - Kurumsal abonelik için ücretler kaydın Azure Ön Ödemesinden (eski adıyla parasal taahhüt) düşülür ve fazla kullanım olarak ücretlendirilir.
    - Kullandıkça Öde fiyatlandırmasına sahip bireysel bir abonelik için ücretler, aboneliğin kredi kartına veya fatura ödeme yöntemine faturalandırılır.
4. Bir kapsam seçin. Kapsam bir aboneliği veya birden çok aboneliği (paylaşılan kapsamı) kapsayabilir.
    - Tek abonelik-plan indirimi, aboneliğin eşleşen kullanımı için uygulanır.
    - Paylaşılan-plan indirimi, faturalandırma bağlamındaki herhangi bir abonelikte bulunan eşleşen örneklere uygulanır. Kurumsal müşteriler için, faturalandırma bağlamı kayıt olur ve kayıt içindeki tüm abonelikleri içerir. Kullandıkça Öde fiyatlandırma müşterileri içeren bireysel bir plan için faturalandırma bağlamı, hesap yöneticisi tarafından oluşturulan Kullandıkça Öde fiyatlandırma abonelikleriyle tek tek plandır.
    - Tek kaynak grubu-rezervasyon iskontosunu yalnızca seçili kaynak grubundaki eşleşen kaynaklara uygular.
5. VM boyutunu ve görüntü türünü seçmek için bir ürün seçin. İndirim yalnızca seçili VM boyutu için geçerlidir.
6. Bir yıllık veya üç yıllık bir dönem seçin.
7. Fatura iskontosunu elde eden ön ödemeli sanal makine örneklerinin sayısı olan bir miktar seçin.
8. Ürünü alışveriş sepetine ekleyin, gözden geçirin ve satın alın.

Rezervasyon indirimi, için önceden ödeyiniz olan yazılım ölçesine otomatik olarak uygulanır. VM işlem ücretleri plan kapsamında değildir. VM ayırmalarını ayrı olarak satın alabilirsiniz.

## <a name="discount-applies-to-different-suse-vm-sizes"></a>İndirim farklı SUSE VM boyutlarına uygulanır

Ayrılmış VM örnekleri gibi SUSE Linux planları örnek boyutu esnekliği sunar. Satın aldığınız SUSE planından farklı boyutta bir VM dağıtırken bile indirim uygulanır. Daha fazla bilgi için bkz. [yazılım planı iskontosunun nasıl uygulandığını anlama](../../cost-management-billing/reservations/understand-suse-reservation-charges.md).

## <a name="redhat-plan-discount"></a>RedHat planı indirimi

Planlar yalnızca Red Hat Enterprise Linux sanal makineler için kullanılabilir. İndirim, RedHat Enterprise Linux SAP HANA VM 'Leri veya RedHat Enterprise Linux SAP Business Apps VM 'Leri için uygulanmaz.

RedHat planı indirimleri yalnızca satın alma sırasında seçtiğiniz VM boyutu için geçerlidir. RHEL planları satın alma işleminden sonra iade edilemez veya alışverişi yapılamıyor.


## <a name="cancellation-and-exchanges-not-allowed"></a>İptal ve değişimler izin verilmiyor

Satın aldığınız bir SUSE veya RedHat planını iptal edemezsiniz veya görüntüleyemezsiniz. Kullanımınızı denetleyerek doğru planı satın aldığınızdan emin olun. Ne satın alınacağını belirleme konusunda yardım için bkz. [yazılım planı iskontosunun nasıl uygulandığını anlayın](../../cost-management-billing/reservations/understand-suse-reservation-charges.md).

## <a name="need-help-contact-us"></a>Yardıma mı ihtiyacınız var? Bize ulaşın.

Sorularınız varsa ya da yardıma gereksinim duyuyorsanız [destek isteği oluşturun](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Sonraki adımlar

Bir ayırmayı yönetme hakkında bilgi edinmek için bkz. [Azure ayırmalarını yönetme](../../cost-management-billing/reservations/manage-reserved-vm-instance.md).

Daha fazla bilgi için aşağıdaki makalelere bakın:

- [Azure Ayrılmış Sanal Makine Örnekleri nedir?](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Azure’da Rezervasyonları Yönetme](../../cost-management-billing/reservations/manage-reserved-vm-instance.md)
- [SUSE rezervasyon iskontosunun nasıl uygulanacağını anlayın](../../cost-management-billing/reservations/understand-suse-reservation-charges.md)
- [Kullandıkça Öde aboneliğiniz için rezervasyon kullanımını anlama](../../cost-management-billing/reservations/understand-reserved-instance-usage.md)
- [Kurumsal kaydınız için rezervasyon kullanımını anlama](../../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
