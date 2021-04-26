---
title: Ayrılmış işlem fiyatlandırması-PostgreSQL için Azure veritabanı-hiper ölçek (Citus)
description: PostgreSQL için Azure veritabanı-hiper ölçek (Citus) ayrılmış kapasiteye sahip işlem kaynakları için ön ödeme.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 06/15/2020
ms.openlocfilehash: 35d5d196eccb222bf17e0a129fe4e4041b1f6053
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98600729"
---
# <a name="prepay-for-azure-database-for-postgresql---hyperscale-citus-compute-resources-with-reserved-capacity"></a>PostgreSQL için Azure veritabanı-hiper ölçek (Citus) için ön ödeme ayrılmış kapasiteye sahip işlem kaynakları

PostgreSQL için Azure veritabanı – Hyperscale (Citus), Kullandıkça Öde fiyatlarına kıyasla işlem kaynakları için ön ödeme yaparak paradan tasarruf etmenize yardımcı olur. Hyperscale (Citus) ayrılmış kapasitesi ile, işlem maliyetlerine göre önemli bir indirim elde etmek için bir veya üç yıllık dönem için Hiperscale (Citus) sunucu grubu üzerinde ön taahhüt taahhüdünü yaparsınız. Hiperscale (Citus) ayrılmış kapasitesini satın almak için Azure bölgesini, rezervasyon terimini ve faturalandırma sıklığını belirtmeniz gerekir.

> [!IMPORTANT]
> Bu makale, PostgreSQL için Azure veritabanı – Hyperscale (Citus) için ayrılmış kapasiteye yöneliktir. PostgreSQL için Azure veritabanı – tek sunucu için ayrılmış kapasite hakkında daha fazla bilgi için bkz. [PostgreSQL Için Azure veritabanı Için ön ödeme – ayrılmış kapasiteye sahip tek sunuculu işlem kaynakları](./concept-reserved-pricing.md).

Ayırmayı belirli bir hiper ölçek (Citus) sunucu gruplarına atamanız gerekmez. Zaten çalışan bir hiper ölçek (Citus) sunucu grubu veya yeni dağıtılan bir ayrılmış fiyatlandırma avantajını otomatik olarak alır. Bir rezervasyon satın alarak, bir yıl veya üç yılda işlem maliyetleri için önceden ödeme yaparsınız. Bir ayırma satın alarak, rezervasyon öznitelikleriyle eşleşen Hiperscale (Citus) işlem ücretleri, Kullandıkça Öde tarifesine göre ücretlendirilir. 

Bir ayırma, Hyperscale (Citus) sunucu gruplarıyla ilişkili yazılım, ağ veya depolama ücretlerini kapsamaz. Rezervasyon döneminin sonunda, faturalandırma avantajı sona erer ve Hiperscale (Citus) sunucu grupları Kullandıkça Öde fiyatı üzerinden faturalandırılır. Rezervasyonlar autorenew değildir. Fiyatlandırma bilgileri için bkz. [PostgreSQL Için Azure veritabanı – Hyperscale (Citus) ayrılmış kapasite teklifi](https://azure.microsoft.com/pricing/details/postgresql/hyperscale-citus/).

[Azure Portal](https://portal.azure.com/)hiper ölçek (Citus) ayrılmış kapasitesini satın alabilirsiniz. [Peşin olarak veya aylık ödemelerle](../cost-management-billing/reservations/prepare-buy-reservation.md) rezervasyon ödemesi yapın. Ayrılmış kapasiteyi satın almak için:

* En az bir Kurumsal Anlaşma (EA) ya da Kullandıkça Öde tarifesine sahip tek bir abonelik için sahip rolünde olmanız gerekir.
* Kurumsal Anlaşma abonelikler için, **ayrılmış örneklerin eklenmesi** [EA portalında](https://ea.azure.com/)etkinleştirilmelidir. Ya da bu ayar devre dışıysa, abonelikte Kurumsal Anlaşma yönetici olmanız gerekir.
* Bulut çözümü sağlayıcısı (CSP) programı için, yalnızca yönetici aracıları veya satış aracıları, hiper ölçek (Citus) ayrılmış kapasitesini satın alabilir.

Kurumsal Anlaşma müşteriler ve kullandıkça öde müşterilerinin rezervasyon satın alma işlemleri için nasıl ücretlendirilildiği hakkında bilgi için bkz.:
- [Kurumsal Anlaşma kaydınız için Azure ayırma kullanımını anlayın](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
- [Kullandıkça Öde aboneliğiniz için Azure rezervasyon kullanımını anlayın](../cost-management-billing/reservations/understand-reserved-instance-usage.md)

## <a name="determine-the-right-server-group-size-before-purchase"></a>Satın almadan önce doğru sunucu grubu boyutunu belirle

Ayırma boyutu, belirli bir bölgedeki Hiperscale (Citus) sunucu gruplarındaki mevcut veya hemen dağıtılmış düzenleyici ve çalışan düğümleri tarafından kullanılan toplam işlem miktarına bağlıdır.

Örneğin, 16 sanal çekirdek Düzenleyicisi ve 3 8 sanal çekirdek çalışan düğümleri ile bir hiper ölçek (Citus) sunucu grubu kullandığınızı varsayalım. Ayrıca, bir sonraki ay içinde, 32 sanal çekirdek Düzenleyicisi ve 2 4 sanal çekirdek çalışan düğümleri ile ek bir hiper ölçek (Citus) sunucu grubu içinde dağıtmayı planladığınızı varsayalım. Ayrıca, bu kaynakların en az bir yıl boyunca gerekli olduğunu varsayalım.

Bu durumda, için bir yıllık rezervasyon satın alın:

* Total 16 Vçekirdeler + 32 sanal çekirdek = 48 sanal çekirdekler düğümleri
* Toplam 3 düğüm x 8 sanal çekirdek + 2 düğüm x 4 sanal çekirdek = 24 + 8 = 32 sanal çekirdek çalışan düğümleri

## <a name="buy-azure-database-for-postgresql-reserved-capacity"></a>PostgreSQL için Azure veritabanı ayrılmış kapasitesi satın alın

1. [Azure Portal](https://portal.azure.com/) oturum açın.
1. **Tüm hizmetler** > **Rezervasyonlar**’ı seçin.
1. **Add (Ekle)** seçeneğini belirleyin. **Satın alma rezervasyonları** bölmesinde PostgreSQL **için Azure veritabanı** ' nı seçerek PostgreSQL veritabanlarınız için yeni bir ayırma satın alın.
1. Satın almak için **Hiperscale (Citus) işlem** türünü seçin ve **Seç**' e tıklayın.
1. **Ürünler** sekmesinde seçilen işlem türü için miktarı gözden geçirin.
1. Satın alımınızın bitmesini sağlamak için **satın al + gözden geçir** sekmesine geçin.

Aşağıdaki tablo gerekli alanları açıklar.

| Alan        | Açıklama                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
|--------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Abonelik | PostgreSQL için Azure veritabanı için ödeme yapmak üzere kullanılan abonelik ayrılmış kapasite ayırması. Abonelik üzerindeki ödeme yöntemi, PostgreSQL için Azure veritabanı ayrılmış kapasite rezervasyonuna ait ön maliyetlere göre ücretlendirilir. Abonelik türü bir Kurumsal Anlaşma (teklif numaraları: MS-AZR-0017P veya MS-AZR-0148P) veya kullandıkça öde fiyatlandırmasına sahip tek bir anlaşmada (teklif numaraları: MS-AZR-0003P veya MS-AZR-0023P) olmalıdır. Kurumsal Anlaşma abonelik için ücretler, kaydın Azure ön ödemesinin (daha önce parasal taahhüt olarak adlandırılır) bakiyesinden düşülür veya fazla kullanım olarak ücretlendirilir. Kullandıkça Öde fiyatlandırmasına sahip bireysel bir abonelik için ücretler, abonelik üzerindeki kredi kartına veya fatura ödeme yöntemine faturalandırılır.                                                                                  |
| Kapsam        | VCore rezervasyonunun kapsamı bir aboneliği veya birden çok aboneliği (paylaşılan kapsamı) kapsayabilir. **Paylaşılan**' i seçerseniz, sanal çekirdek ayırma indirimi, faturalandırma bağlamınızın içindeki aboneliklerde çalışan hiper ölçek (Citus) sunucu gruplarına uygulanır. Kurumsal Anlaşma müşteriler için, paylaşılan kapsam kayıt içindedir ve kayıt dahilindeki tüm abonelikleri içerir. Kullandıkça Öde müşterileri için, paylaşılan kapsam, hesap yöneticisi tarafından oluşturulan tüm Kullandıkça Öde aboneliklerdir. **Tek bir abonelik** seçerseniz, sanal çekirdek ayırma indirimi Bu abonelikteki hiper ölçek (Citus) sunucu gruplarına uygulanır. **Tek kaynak grubu**' nu seçerseniz, ayırma indirimi seçili abonelikteki hiper ölçek (Citus) sunucu gruplarına ve bu abonelik içindeki seçili kaynak grubuna uygulanır. |
| Region       | PostgreSQL için Azure veritabanı – Hyperscale (Citus) ayrılmış kapasite rezervasyonu kapsamındaki Azure bölgesi.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Süre         | Bir yıl veya üç yıl.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Miktar     | Hiperscale (Citus) ayrılmış kapasite rezervasyonu içinde satın alınan işlem kaynakları miktarı. Özellikle, ayrılan ve fatura iskontosunu alacak olan seçili Azure bölgesindeki düzenleyici veya çalışan düğümü sanal çekirdekleri sayısı. Örneğin, Doğu ABD bölgesinde 64 Düzenleyici düğüm sanal çekirdekleri ve 32 çalışan düğümü sanal çekirdekleri için toplam işlem kapasitesine sahip hiper ölçek (veya çalıştırmayı planlayın) sunucu grupları kullanıyorsanız, tüm sunucuların avantajını en üst düzeye çıkarmak için sırasıyla, Koordinatör ve çalışan düğümleri için, sırasıyla 64 ve 32 olarak bir sayı belirtin.                                                                                                                                                                                                                                                     |



## <a name="cancel-exchange-or-refund-reservations"></a>Rezervasyonları iptal etme, değiştirme veya para iadesi alma

Belirli sınırlamalarla rezervasyonları iptal edebilir, değiştirebilir veya para iadesi alabilirsiniz. Daha fazla bilgi için bkz. [Azure ayırmaları Için self servis değişimlerinin ve para iadesi](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="vcore-size-flexibility"></a>vCore boyutu esnekliği

vCore boyutu esnekliği, ayrılmış kapasite avantajını kaybetmeden bir bölgedeki düzenleyiciyi ve çalışan düğümlerini ölçeklendirmenize yardımcı olur.

## <a name="need-help-contact-us"></a>Yardıma mı ihtiyacınız var? Bizimle iletişim kurun

Sorularınız varsa ya da yardıma gereksinim duyuyorsanız [destek isteği oluşturun](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Sonraki adımlar

VCore ayırma indirimi, PostgreSQL için Azure veritabanı ayrılmış kapasite ayırma kapsamı ve öznitelikleri ile eşleşen hiper ölçek (Citus) sunucu grubu sayısına otomatik olarak uygulanır. PostgreSQL için Azure veritabanı – Hyperscale (Citus) ayrılmış kapasite ayırmasının kapsamını Azure portal, PowerShell, Azure CLı veya API aracılığıyla güncelleştirebilirsiniz.

Azure rezervasyonları hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

* [Azure rezervasyonları nedir?](../cost-management-billing/reservations/save-compute-costs-reservations.md)
* [Azure rezervasyonlarını yönetme](../cost-management-billing/reservations/manage-reserved-vm-instance.md)
* [Azure rezervasyon iskontosunu anlama](../cost-management-billing/reservations/understand-reservation-charges.md)
* [Kullandıkça Öde aboneliğiniz için rezervasyon kullanımını anlayın](../cost-management-billing/reservations/understand-reservation-charges-postgresql.md)
* [Kurumsal Anlaşma kaydınız için rezervasyon kullanımını anlayın](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
* [Iş Ortağı Merkezi bulut çözümü sağlayıcısı programındaki Azure ayırmaları](/partner-center/azure-reservations)