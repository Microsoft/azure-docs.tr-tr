---
title: Yazılım planları için ön ödeme-Azure ayırmaları
description: Kullandıkça Öde maliyetlerinizden tasarruf etmek için yazılım planlarına ön ödeme yapma hakkında bilgi edinin.
author: bandersmsft
manager: yashesvi
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: e4cab8963e3083d265d0254c81b78fbabb84a269
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70091532"
---
# <a name="prepay-for-azure-software-plans"></a>Azure yazılım planları için ön ödeme yapma

Azure 'da SUSE ve Redhsize yazılım kullanımınız için ön ödeme yaptığınızda, Kullandıkça Öde maliyetlerinizden tasarruf edebilirsiniz. İndirimler, sanal makine kullanımında değil, yalnızca SUSE ve RedHat ölçümleri için geçerlidir. Daha fazla tasarruf için sanal makineler için ayırmaları ayrı olarak satın alabilirsiniz.

Azure portal SUSE ve RedHat yazılım planlarını satın alabilirsiniz. Bir plan satın almak için:

- Kullandıkça Öde fiyatlandırmasına sahip en az bir kurumsal veya ayrı bir abonelik için sahip rolüne sahip olmanız gerekir.
- Kurumsal abonelikler için, **ayrılmış örnekler Ekle** seçeneğinin [EA portalında](https://ea.azure.com/)etkinleştirilmiş olması gerekir. Ayar devre dışı bırakılmışsa, abonelik için bir EA yöneticisi olmanız gerekir.
- Bulut çözümü sağlayıcısı (CSP) programı için, yönetici aracıları veya satış aracıları yazılım planlarını satın alabilir.

## <a name="buy-a-software-plan"></a>Yazılım planı satın alın

1. Azure portal oturum açın ve [ayırmalar](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)' a gidin.
2. **Ekle** ' ye tıklayın ve ardından satın almak istediğiniz yazılım planını seçin.
Gerekli alanları girin. Satın alma yaptığınız özelliklerin öznitelikleriyle eşleşen SUSE Linux VM veya RedHat VM 'si indirimi alır. İndirimin alınacağı gerçek dağıtım sayısı, seçilen kapsama ve miktara bağlıdır.
3. Bir abonelik seçin. Plan için ödeme yapmak üzere kullanılır.
Abonelik ödeme yöntemi, rezervasyonun ön maliyetlerine göre ücretlendirilir. Abonelik türü bir Kurumsal Anlaşma olmalıdır (teklif numaraları: MS-AZR-0017P veya MS-AZR-0148P) veya kullandıkça öde fiyatlandırmasıyla bireysel anlaşma (teklif numaraları: MS-AZR-0003P veya MS-AZR-0023P).
    - Kurumsal abonelik için ücretler kaydın maddi işlem bakiyesinden düşülür ve fazla kullanım olarak ücretlendirilir.
    - Kullandıkça Öde fiyatlandırmasına sahip bireysel bir abonelik için ücretler, aboneliğin kredi kartına veya fatura ödeme yöntemine faturalandırılır.
4. Kapsam seçin. Kapsam bir aboneliği veya birden çok aboneliği (paylaşılan kapsamı) kapsayabilir.
    - Tek abonelik-plan indirimi, aboneliğin eşleşen kullanımı için uygulanır.
    - Paylaşılan-plan indirimi, faturalandırma bağlamındaki herhangi bir abonelikte bulunan eşleşen örneklere uygulanır. Kurumsal müşteriler için, faturalandırma bağlamı kayıt olur ve kayıt içindeki tüm abonelikleri içerir. Kullandıkça Öde fiyatlandırma müşterileri içeren bireysel bir plan için faturalandırma bağlamı, hesap yöneticisi tarafından oluşturulan Kullandıkça Öde fiyatlandırma abonelikleriyle tek tek plandır.
5. VM boyutunu ve görüntü türünü seçmek için bir ürün seçin. İndirim yalnızca seçili VM boyutu için geçerlidir.
6. Bir yıllık veya üç yıllık bir dönem seçin.
7. Fatura iskontosunu elde eden ön ödemeli sanal makine örneklerinin sayısı olan bir miktar seçin.
8. Ürünü alışveriş sepetine ekleyin, gözden geçirin ve satın alın.

Rezervasyon indirimi, için önceden ödeyiniz olan yazılım ölçesine otomatik olarak uygulanır. VM işlem ücretleri plan kapsamında değildir. VM ayırmalarını ayrı olarak satın alabilirsiniz.

## <a name="discount-applies-to-different-suse-vm-sizes"></a>İndirim farklı SUSE VM boyutlarına uygulanır

Ayrılmış VM örnekleri gibi SUSE Linux planları örnek boyutu esnekliği sunar. Satın aldığınız SUSE planından farklı boyutta bir VM dağıtırken bile indirim uygulanır. Daha fazla bilgi için bkz. [yazılım planı iskontosunun nasıl uygulandığını anlama](../../billing/billing-understand-suse-reservation-charges.md).

## <a name="redhat-plan-discount"></a>RedHat planı indirimi

Planlar yalnızca Red Hat Enterprise Linux sanal makineler için kullanılabilir. İndirim, RedHat Enterprise Linux SAP HANA VM 'Leri veya RedHat Enterprise Linux SAP Business Apps VM 'Leri için uygulanmaz.

RedHat planı indirimleri yalnızca satın alma sırasında seçtiğiniz VM boyutu için geçerlidir. RHEL planları satın alma işleminden sonra iade edilemez veya alışverişi yapılamıyor.


## <a name="cancellation-and-exchanges-not-allowed"></a>İptal ve değişimler izin verilmiyor

Satın aldığınız bir SUSE veya RedHat planını iptal edemezsiniz veya görüntüleyemezsiniz. Kullanımınızı denetleyerek doğru planı satın aldığınızdan emin olun. Ne satın alınacağını belirleme konusunda yardım için bkz. [yazılım planı iskontosunun nasıl uygulandığını anlayın](../../billing/billing-understand-suse-reservation-charges.md).

## <a name="need-help-contact-us"></a>Yardım mı gerekiyor? Bizimle iletişim kurun.

Sorularınız varsa veya yardıma ihtiyacınız [bir destek isteği oluşturma](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Sonraki adımlar

Bir ayırmayı yönetme hakkında bilgi edinmek için bkz. [Azure ayırmalarını yönetme](../../billing/billing-manage-reserved-vm-instance.md).

Daha fazla bilgi için aşağıdaki makalelere bakın:

- [Azure ayırmaları nelerdir?](../../billing/billing-save-compute-costs-reservations.md)
- [Azure 'da ayırmaları yönetme](../../billing/billing-manage-reserved-vm-instance.md)
- [SUSE rezervasyon iskontosunun nasıl uygulanacağını anlayın](../../billing/billing-understand-suse-reservation-charges.md)
- [Kullandıkça Öde aboneliğiniz için rezervasyon kullanımını anlayın](../../billing/billing-understand-reserved-instance-usage.md)
- [Kurumsal kaydınız için rezervasyon kullanımını anlayın](../../billing/billing-understand-reserved-instance-usage-ea.md)
