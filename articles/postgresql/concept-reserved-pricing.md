---
title: Ayrılmış işlem fiyatlandırması-PostgreSQL için Azure veritabanı-tek sunucu
description: Ayrılmış kapasiteye sahip PostgreSQL için Azure veritabanı işlem kaynakları için ön ödeme
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/16/2020
ms.openlocfilehash: 9b8dafa4a69358b3f6f09551ac426b908750e2f4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98735481"
---
# <a name="prepay-for-azure-database-for-postgresql---single-server-compute-resources-with-reserved-capacity"></a>PostgreSQL için Azure veritabanı-ayrılmış kapasiteye sahip tek sunuculu işlem kaynakları için ön ödeme

PostgreSQL için Azure veritabanı artık, Kullandıkça Öde fiyatlarına kıyasla işlem kaynakları için ön ödeme yaparak paradan tasarruf etmenize yardımcı olur. PostgreSQL için Azure veritabanı ile ayrılmış kapasite ile, işlem maliyetlerine göre önemli bir indirim elde etmek üzere bir veya üç yıllık bir dönem için PostgreSQL sunucusu üzerinde bir ön taahhüt yaparsınız. PostgreSQL için Azure veritabanı 'na ayrılan kapasiteyi satın almak için Azure bölgesini, dağıtım türünü, performans katmanını ve terimi belirtmeniz gerekir. </br>

Ayırmayı, PostgreSQL için belirli Azure veritabanı sunucularına atamanız gerekmez. Zaten PostgreSQL için Azure veritabanı (veya yeni dağıtılmış olanlar), ayrılmış fiyatlandırma avantajını otomatik olarak alır. Bir rezervasyon satın alarak, bir veya üç yıllık bir dönem için işlem maliyetleri için ön ödeme yaparsınız. Bir ayırma satın alarak, rezervasyon öznitelikleriyle eşleşen PostgreSQL için Azure veritabanı işlem ücretleri artık Kullandıkça Öde tarifelerine göre ücretlendirilir. Bir ayırma, PostgreSQL veritabanı sunucularıyla ilişkili yazılım, ağ veya depolama ücretlerini kapsamaz. Rezervasyon döneminin sonunda, faturalandırma avantajı sona erer ve PostgreSQL için Azure veritabanı, Kullandıkça Öde fiyatı üzerinden faturalandırılır. Ayırmalar otomatik olarak yenilemez. Fiyatlandırma bilgileri için bkz. [PostgreSQL Için Azure veritabanı ayrılmış kapasite teklifi](https://azure.microsoft.com/pricing/details/postgresql/). </br>

> [!IMPORTANT]
> [Tek sunucu](./overview.md#azure-database-for-postgresql---single-server) ve [hyperscale Citus](./overview.md#azure-database-for-postgresql--hyperscale-citus) dağıtım seçeneklerinde, PostgreSQL için Azure veritabanı için ayrılmış kapasite fiyatlandırması kullanılabilir. Hiper ölçekte RI fiyatlandırması hakkında bilgi için (Citus), [Bu sayfaya](concepts-hyperscale-reserved-pricing.md)bakın.

PostgreSQL için Azure veritabanı 'nın [Azure Portal](https://portal.azure.com/)ayrılmış kapasitesini satın alabilirsiniz. [Peşin olarak veya aylık ödemelerle](../cost-management-billing/reservations/prepare-buy-reservation.md) rezervasyon ödemesi yapın. Ayrılmış kapasiteyi satın almak için:

* Kullandıkça Öde tarifesine sahip en az bir kuruluş veya ayrı bir abonelik için sahip rolünde olmanız gerekir.
* Kurumsal abonelikler için, [EA portal](https://ea.azure.com/)’da **Ayrılmış Örnek Ekle** seçeneği etkinleştirilmelidir. Ya da bu ayar devre dışı bırakılırsa, abonelikte bir EA yöneticisi olmanız gerekir.
* Bulut çözümü sağlayıcısı (CSP) programı için, yalnızca yönetici aracıları veya satış aracıları, PostgreSQL için Azure veritabanı ayrılmış kapasitesini satın alabilir. </br>

Kurumsal müşterilerin ve kullandıkça öde müşterilerinin, rezervasyon satın alma işlemleri için nasıl ücretlendirilildiği hakkındaki ayrıntılar, bkz. [Kurumsal kaydınız Için Azure ayırma kullanımını anlama](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) ve [Kullandıkça Öde aboneliğiniz için Azure rezervasyon kullanımını anlama](../cost-management-billing/reservations/understand-reserved-instance-usage.md).


## <a name="determine-the-right-server-size-before-purchase"></a>Satın almadan önce doğru sunucu boyutunu belirle

Ayırma boyutu, belirli bir bölgedeki mevcut veya kısa süreli dağıtılan sunucular tarafından kullanılan ve aynı performans katmanını ve donanım üretimini kullanan toplam işlem miktarına bağlı olmalıdır.</br>

Örneğin, bir genel amaçlı 5. nesil – 32 sanal çekirdek PostgreSQL veritabanı ve bellek için iyileştirilmiş iki 5. nesil – 16 sanal çekirdek PostgreSQL veritabanı kullandığınızı varsayalım. Ayrıca, bir sonraki ay içinde ek bir genel amaçlı 5. nesil – 8 sanal çekirdek veritabanı sunucusu ve bir bellek için iyileştirilmiş 5. nesil – 32 sanal çekirdek veritabanı sunucusu dağıtmayı planlamanız gerekir. Bu kaynaklara en az bir yıl boyunca ihtiyacınız olacağını bildiğinizi varsayalım. Bu durumda, tek veritabanı bellek için iyileştirilmiş-5. nesil için bir yıllık tek veritabanı genel amaçlı-5. nesil ve 64 (2x16 + 32) sanal çekirdekleri için bir yıllık 40 (32 + 8) sanal çekirdek satın almalısınız.


## <a name="buy-azure-database-for-postgresql-reserved-capacity"></a>PostgreSQL için Azure veritabanı ayrılmış kapasitesi satın alın

1. [Azure Portal](https://portal.azure.com/) oturum açın.
2. **Tüm hizmetler** > **Rezervasyonlar**’ı seçin.
3. **Ekle** ' yi seçin ve ardından satın alma rezervasyonları bölmesinde PostgreSQL **için Azure veritabanı** ' nı seçerek PostgreSQL veritabanlarınız için yeni bir ayırma satın alın.
4. Gerekli alanları girin. ' Yi seçtiğiniz özniteliklerle eşleşen mevcut veya yeni veritabanları, ayrılmış kapasite indirimi elde etmek için nitelendir ' ı seçin. İskontoyu alan PostgreSQL için Azure veritabanı sunucularınızın gerçek sayısı, seçilen kapsama ve miktara göre değişir.


:::image type="content" source="media/concepts-reserved-pricing/postgresql-reserved-price.png" alt-text="Ayrılmış fiyatlandırmaya genel bakış":::


Aşağıdaki tablo gerekli alanları açıklar.

| Alan | Açıklama |
| :------------ | :------- |
| Abonelik   | PostgreSQL için Azure veritabanı için ödeme yapmak üzere kullanılan abonelik ayrılmış kapasite ayırması. Abonelik üzerindeki ödeme yöntemi, PostgreSQL için Azure veritabanı ayrılmış kapasite rezervasyonuna ait ön maliyetlere göre ücretlendirilir. Abonelik türü bir kurumsal anlaşma (teklif numaraları: MS-AZR-0017P veya MS-AZR-0148P) veya kullandıkça öde fiyatlandırması (teklif numaraları: MS-AZR-0003P veya MS-AZR-0023P) içeren tek bir anlaşma olmalıdır. Kurumsal abonelik için ücretler kaydın Azure Ön Ödemesinden (eski adıyla parasal taahhüt) düşülür ve fazla kullanım olarak ücretlendirilir. Kullandıkça Öde fiyatlandırmasına sahip bireysel bir abonelik için ücretler, abonelik üzerindeki kredi kartına veya fatura ödeme yöntemine faturalandırılır.
| Kapsam | VCore rezervasyonunun kapsamı bir aboneliği veya birden çok aboneliği (paylaşılan kapsamı) kapsayabilir. Şunları seçerseniz: </br></br> **, Sanal** çekirdek ayırma indirimi, faturalandırma bağlamınızın içindeki aboneliklerde çalışan PostgreSQL sunucuları Için Azure veritabanı 'na uygulanır. Kurumsal müşteriler için, paylaşılan kapsam kayıt içindedir ve kayıt dahilindeki tüm abonelikleri içerir. Kullandıkça Öde müşterileri için paylaşılan kapsam, hesap yöneticisi tarafından oluşturulan tüm Kullandıkça Öde abonelikleridir.</br></br> **Tek abonelik**, sanal çekirdek ayırma indirimi Bu abonelikteki PostgreSQL Için Azure veritabanı sunucularına uygulanır. </br></br> **Tek kaynak grubu**, ayırma indirimi seçili abonelikteki PostgreSQL sunucuları Için Azure veritabanı ve bu abonelikteki seçili kaynak grubu için geçerlidir.
| Region | PostgreSQL için Azure veritabanı 'nın kapsamına giren Azure bölgesi ayrılmış kapasite ayırması.
| Dağıtım türü | Ayırmasını satın almak istediğiniz PostgreSQL için Azure veritabanı kaynak türü.
| Performans katmanı | PostgreSQL için Azure veritabanı sunucularına yönelik hizmet katmanı.
| Süre | Bir yıl
| Miktar | PostgreSQL için Azure veritabanı 'nda satın alınan işlem kaynaklarının miktarı ayrılmış kapasite ayırması. Bu miktar, seçili Azure bölgesindeki ve performans katmanındaki, ayrılan ve fatura iskontosunu alacak olan sanal çekirdekler sayısıdır. Örneğin, Doğu ABD bölgesinde 5. nesil 16 sanal çekirdeklerinin toplam işlem kapasitesine sahip PostgreSQL için Azure veritabanı sunucuları çalıştırıyorsanız veya çalıştırmayı planlıyorsanız, tüm sunucuların avantajını en üst düzeye çıkarmak için miktarı 16 olarak belirtirsiniz.

## <a name="cancel-exchange-or-refund-reservations"></a>Rezervasyonları iptal etme, değiştirme veya para iadesi alma

Belirli sınırlamalarla rezervasyonları iptal edebilir, değiştirebilir veya para iadesi alabilirsiniz. Daha fazla bilgi için bkz. [Azure Ayrılmış Sanal Makine Örnekleri için self servis değişimler ve para iadeleri](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="vcore-size-flexibility"></a>vCore boyutu esnekliği

vCore boyutu esnekliği, ayrılan kapasite avantajını kaybetmeden bir performans katmanı ve bölgesi içinde ölçeği büyütme veya küçültme olanakları sağlar. Ayrılmış kapasiteniz dışında daha yüksek sanal çekirdekler için ölçeklendirirseniz, Kullandıkça Öde fiyatlandırması kullanılarak fazla sanal çekirdekler için faturalandırılırsınız.


## <a name="need-help-contact-us"></a>Yardıma mı ihtiyacınız var? Bizimle iletişim kurun

Sorularınız varsa ya da yardıma gereksinim duyuyorsanız [destek isteği oluşturun](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Sonraki adımlar

VCore RESERVATION Discount, PostgreSQL için Azure veritabanı 'na ayrılmış kapasite ayırma kapsamı ve öznitelikleri ile eşleşen PostgreSQL için Azure veritabanı sunucularının sayısına otomatik olarak uygulanır. PostgreSQL için Azure veritabanı 'nın kapsamını Azure portal, PowerShell, CLı veya API aracılığıyla ayrılmış kapasite ayırma için güncelleştirebilirsiniz.

Azure Ayrılmış Sanal Makine Örnekleri hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

* [Azure ayırmaları nelerdir](../cost-management-billing/reservations/save-compute-costs-reservations.md)?
* [Azure Ayırmalarını yönetme](../cost-management-billing/reservations/manage-reserved-vm-instance.md)
* [Azure Ayrılmış Sanal Makine Örnekleri indirimini anlama](../cost-management-billing/reservations/understand-reservation-charges.md)
* [Kullandıkça Öde aboneliğiniz için rezervasyon kullanımını anlama](../cost-management-billing/reservations/understand-reservation-charges-postgresql.md)
* [Kurumsal kaydınız için rezervasyon kullanımını anlama](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
* [İş Ortağı Merkezi Bulut Çözümü Sağlayıcısı (CSP) programındaki Azure Ayrılmış Sanal Makine Örnekleri](/partner-center/azure-reservations)