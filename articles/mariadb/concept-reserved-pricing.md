---
title: Ayrılmış kapasiteye sahip işlem için ön ödeme-MariaDB için Azure veritabanı
description: Ayrılmış kapasiteye sahip MariaDB işlem kaynakları için Azure veritabanı için ön ödeme
author: mksuni
ms.author: sumuth
ms.service: mariadb
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: 462ba0ccbd5d7e7048c2c7fcb9c5bece04adaebe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98661772"
---
# <a name="prepay-for-azure-database-for-mariadb-compute-resources-with-reserved-capacity"></a>Ayrılmış kapasiteye sahip MariaDB işlem kaynakları için Azure veritabanı için ön ödeme

MariaDB için Azure veritabanı artık, Kullandıkça Öde fiyatlarına kıyasla işlem kaynakları için ön ödeme yaparak paradan tasarruf etmenize yardımcı olur. MariaDB için Azure veritabanı ile ayrılmış kapasite ile, işlem maliyetlerine göre önemli bir indirim elde etmek üzere MariaDB sunucularında bir veya üç yıllık dönem için ön taahhüt yaparsınız. MariaDB için Azure veritabanı 'na ayrılmış kapasite satın almak için Azure bölgesini, dağıtım türünü, performans katmanını ve terimi belirtmeniz gerekir. </br>

Bu ayırmayı, MariaDB sunucuları için belirli Azure veritabanına atamanız gerekmez. MariaDB için Azure veritabanı veya yeni dağıtılan olanlar, ayrılmış fiyatlandırma avantajını otomatik olarak alır. Bir rezervasyon satın alarak, bir veya üç yıllık bir dönem için işlem maliyetleri için ön ödeme yaparsınız. Bir ayırma satın alarak, rezervasyon öznitelikleriyle eşleşen MariaDB işlem ücretleri için Azure veritabanı, artık Kullandıkça Öde tarifelerine göre ücretlendirilir. Bir ayırma, MariaDB veritabanı sunucusuyla ilişkili yazılım, ağ veya depolama ücretlerini kapsamaz. Rezervasyon döneminin sonunda, faturalandırma avantajı sona erer ve MariaDB için Azure veritabanı, Kullandıkça Öde fiyatı üzerinden faturalandırılır. Ayırmalar otomatik olarak yenilemez. Fiyatlandırma bilgileri için bkz. [MariaDB Için Azure veritabanı ayrılmış kapasite teklifi](https://azure.microsoft.com/pricing/details/mariadb/). </br>

MariaDB için Azure veritabanı ayrılmış kapasitesi [Azure Portal](https://portal.azure.com/)satın alabilirsiniz. [Peşin olarak veya aylık ödemelerle](../cost-management-billing/reservations/prepare-buy-reservation.md) rezervasyon ödemesi yapın. Ayrılmış kapasiteyi satın almak için:

* Kullandıkça Öde tarifesine sahip en az bir kuruluş veya ayrı bir abonelik için sahip rolünde olmanız gerekir.
* Kurumsal abonelikler için, [EA portal](https://ea.azure.com/)’da **Ayrılmış Örnek Ekle** seçeneği etkinleştirilmelidir. Ya da bu ayar devre dışı bırakılırsa, abonelikte bir EA yöneticisi olmanız gerekir.
* Bulut çözümü sağlayıcısı (CSP) programı için, yalnızca yönetici aracıları veya satış aracıları, MariaDB için Azure veritabanı ayrılmış kapasitesi için satın alabilir. </br>

Kurumsal müşterilerin ve kullandıkça öde müşterilerinin, rezervasyon satın alma işlemleri için nasıl ücretlendirilildiği hakkındaki ayrıntılar, bkz. [Kurumsal kaydınız Için Azure ayırma kullanımını anlama](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) ve [Kullandıkça Öde aboneliğiniz için Azure rezervasyon kullanımını anlama](../cost-management-billing/reservations/understand-reserved-instance-usage.md).


## <a name="determine-the-right-server-size-before-purchase"></a>Satın almadan önce doğru sunucu boyutunu belirle

Ayırma boyutu, belirli bir bölgedeki mevcut veya hemen dağıtılmış veritabanları örneği tarafından kullanılan ve aynı performans katmanını ve donanım üretimini kullanan toplam işlem miktarına bağlı olmalıdır.</br>

Örneğin, bir genel amaç, 5. nesil – 32 vCore MariaDB veritabanı ve iki bellek için iyileştirilmiş, 5. nesil – 16 sanal çekirdek MariaDB veritabanlarını çalıştırdığınızı varsayalım. Ayrıca, bir sonraki ay içinde, ek bir genel amaç, 5. nesil – 32 sanal çekirdek veritabanı sunucusu ve bir bellek için iyileştirilmiş, 5. nesil – 16 sanal çekirdek veritabanı sunucusu arasında dağıtmayı planladığınızı varsayalım. En az 1 yılda bu kaynaklara ihtiyacınız olacağını bildiğinizi varsayalım. Bu durumda, tek veritabanı belleği için iyileştirilmiş-5. nesil için 64 (2x32) sanal çekirdekleri, tek veritabanı için 1 yıl ayırma genel amaçlı-5. nesil ve 48 (2x16 + 16) sanal çekirdek 1 yıl ayırması satın almalısınız


## <a name="buy-azure-database-for-mariadb-reserved-capacity"></a>MariaDB için Azure veritabanı ayrılmış kapasitesi satın alın

1. [Azure Portal](https://portal.azure.com/) oturum açın.
2. **Tüm hizmetler** > **Rezervasyonlar**’ı seçin.
3.  **Ekle** ' yi seçin ve ardından satın alma rezervasyonları bölmesinde, MariaDB veritabanlarınızı Için **Azure veritabanı** ' nı seçin.
4.  Gerekli alanları doldur. ' Yi seçtiğiniz özniteliklerle eşleşen mevcut veya yeni veritabanları, ayrılmış kapasite indirimi elde etmek için nitelendir ' ı seçin. İndirimi alan MariaDB sunucuları için Azure veritabanınızın gerçek sayısı, seçilen kapsama ve miktara göre değişir.


![Ayrılmış fiyatlandırmaya genel bakış](media/concepts-reserved-pricing/mariadb-reserved-price.png)


Aşağıdaki tablo gerekli alanları açıklar.

| Alan | Açıklama |
| :------------ | :------- |
| Abonelik   | MariaDB için Azure veritabanı ayrılmış kapasite ayırması için ödeme yapmak üzere kullanılan abonelik. Abonelik üzerindeki ödeme yöntemi, MariaDB için Azure veritabanı ayrılmış kapasite rezervasyonu için ön maliyetler üzerinden ücretlendirilir. Abonelik türü bir kurumsal anlaşma (teklif numaraları: MS-AZR-0017P veya MS-AZR-0148P) veya kullandıkça öde fiyatlandırması (teklif numaraları: MS-AZR-0003P veya MS-AZR-0023P) içeren tek bir anlaşma olmalıdır. Kurumsal abonelik için ücretler kaydın Azure Ön Ödemesinden (eski adıyla parasal taahhüt) düşülür ve fazla kullanım olarak ücretlendirilir. Kullandıkça Öde fiyatlandırmasına sahip bireysel bir abonelik için ücretler, abonelik üzerindeki kredi kartına veya fatura ödeme yöntemine faturalandırılır.
| Kapsam | VCore rezervasyonunun kapsamı bir aboneliği veya birden çok aboneliği (paylaşılan kapsamı) kapsayabilir. Şunları seçerseniz: </br></br> **, Sanal** çekirdek ayırma indirimi, faturalandırma bağlamınızın içindeki aboneliklerde çalışan MariaDB sunucuları Için Azure veritabanı 'na uygulanır. Kurumsal müşteriler için, paylaşılan kapsam kayıt içindedir ve kayıt dahilindeki tüm abonelikleri içerir. Kullandıkça Öde müşterileri için paylaşılan kapsam, hesap yöneticisi tarafından oluşturulan tüm Kullandıkça Öde abonelikleridir.</br></br> **Tek abonelik**, sanal çekirdek ayırma indirimi Bu abonelikteki MariaDB sunucuları Için Azure veritabanı 'na uygulanır. </br></br> **Tek kaynak grubu**, rezervasyon indirimi seçili abonelikteki MariaDB sunucuları Için Azure veritabanı ve bu abonelikteki seçili kaynak grubu için geçerlidir.
| Region | MariaDB için Azure veritabanı ayrılmış kapasite rezervasyonu kapsamındaki Azure bölgesi.
| Dağıtım türü | Ayırmasını satın almak istediğiniz MariaDB için Azure veritabanı kaynak türü.
| Performans katmanı | MariaDB sunucuları için Azure veritabanı hizmet katmanı.
| Süre | Bir yıl
| Miktar | MariaDB için Azure veritabanı 'nda satın alınan işlem kaynaklarının miktarı ayrılmış kapasite ayırması. Bu miktar, seçili Azure bölgesindeki ve performans katmanındaki, ayrılan ve fatura iskontosunu alacak olan sanal çekirdekler sayısıdır. Örneğin, Doğu ABD bölgesinde 5. nesil 16 sanal çekirdeklerinin toplam işlem kapasitesine sahip MariaDB sunucuları için Azure veritabanı 'nı çalıştırıyorsanız veya çalıştırmayı planlıyorsanız, tüm sunucuların avantajını en üst düzeye çıkarmak için miktarı 16 olarak belirtirsiniz.

## <a name="cancel-exchange-or-refund-reservations"></a>Rezervasyonları iptal etme, değiştirme veya para iadesi alma

Belirli sınırlamalarla rezervasyonları iptal edebilir, değiştirebilir veya para iadesi alabilirsiniz. Daha fazla bilgi için bkz. [Azure Ayrılmış Sanal Makine Örnekleri için self servis değişimler ve para iadeleri](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="vcore-size-flexibility"></a>vCore boyutu esnekliği

vCore boyutu esnekliği, ayrılan kapasite avantajını kaybetmeden bir performans katmanı ve bölgesi içinde ölçeği büyütme veya küçültme olanakları sağlar. 

## <a name="need-help-contact-us"></a>Yardıma mı ihtiyacınız var? Bizimle iletişim kurun

Sorularınız varsa ya da yardıma gereksinim duyuyorsanız [destek isteği oluşturun](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Sonraki adımlar

VCore ayırma indirimi, MariaDB için Azure veritabanı için Azure veritabanı 'na ayrılmış kapasite ayırma kapsamı ve öznitelikleri için Azure veritabanı sayısına otomatik olarak uygulanır. MariaDB için Azure veritabanı 'nın kapsamını Azure portal, PowerShell, CLı veya API aracılığıyla ayrılmış kapasite ayırması için güncelleştirebilirsiniz. </br></br>
MariaDB için Azure veritabanı 'nın ayrılmış kapasitesini yönetme hakkında bilgi edinmek için bkz. MariaDB için Azure veritabanı ayrılmış kapasitesi.

Azure Ayrılmış Sanal Makine Örnekleri hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

* [Azure ayırmaları nelerdir](../cost-management-billing/reservations/save-compute-costs-reservations.md)?
* [Azure Ayırmalarını yönetme](../cost-management-billing/reservations/manage-reserved-vm-instance.md)
* [Azure Ayrılmış Sanal Makine Örnekleri indirimini anlama](../cost-management-billing/reservations/understand-reservation-charges.md)
* [Kullandıkça Öde aboneliğiniz için rezervasyon kullanımını anlama](../cost-management-billing/reservations/understand-reservation-charges-mariadb.md)
* [Kurumsal kaydınız için rezervasyon kullanımını anlama](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
* [İş Ortağı Merkezi Bulut Çözümü Sağlayıcısı (CSP) programındaki Azure Ayrılmış Sanal Makine Örnekleri](/partner-center/azure-reservations)