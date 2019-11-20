---
title: Ayrılmış kapasiteye sahip PostgreSQL için Azure veritabanı işlem kaynakları için ön ödeme
description: Ayrılmış kapasiteye sahip PostgreSQL için Azure veritabanı işlem kaynakları için ön ödeme
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: 753d962cbefd38ec991fb9e63805760fe07a8f2d
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123879"
---
# <a name="prepay-for-azure-database-for-postgresql-compute-resources-with-reserved-capacity"></a>Ayrılmış kapasiteye sahip PostgreSQL için Azure veritabanı işlem kaynakları için ön ödeme

PostgreSQL için Azure veritabanı artık, Kullandıkça Öde fiyatlarına kıyasla işlem kaynakları için ön ödeme yaparak paradan tasarruf etmenize yardımcı olur. PostgreSQL için Azure veritabanı ayrılmış kapasitesi ile, işlem maliyetlerine göre önemli bir indirim elde etmek üzere PostgreSQL sunucusu üzerinde bir yıllık bir dönem için ön taahhüt yapılır. PostgreSQL için Azure veritabanı 'na ayrılan kapasiteyi satın almak için Azure bölgesini, dağıtım türünü, performans katmanını ve terimi belirtmeniz gerekir. </br>

Ayırmayı, PostgreSQL için belirli Azure veritabanı sunucularına atamanız gerekmez. Zaten PostgreSQL için Azure veritabanı veya yeni dağıtılmış olanlar, ayrılmış fiyatlandırma avantajını otomatik olarak alır. Bir rezervasyon satın alarak, bir yıllık dönem için işlem maliyetleri için ön ödeme yaparsınız. Bir ayırma satın alarak, rezervasyon öznitelikleriyle eşleşen PostgreSQL için Azure veritabanı işlem ücretleri artık Kullandıkça Öde tarifelerine göre ücretlendirilir. Bir ayırma, PostgreSQL veritabanı sunucularıyla ilişkili yazılım, ağ veya depolama ücretlerini kapsamaz. Rezervasyon döneminin sonunda, faturalandırma avantajı sona erer ve PostgreSQL için Azure veritabanı, Kullandıkça Öde fiyatı üzerinden faturalandırılır. Ayırmalar otomatik olarak yenilemez. Fiyatlandırma bilgileri için bkz. [PostgreSQL Için Azure veritabanı ayrılmış kapasite teklifi](https://azure.microsoft.com/pricing/details/postgresql/). </br>

> [!IMPORTANT]
> Ayrılmış kapasite fiyatlandırması yalnızca PostgreSQL [Için](https://docs.microsoft.com/azure/postgresql/overview#azure-database-for-postgresql---single-server) Azure veritabanı için kullanılabilir ve [hiperscale Citus](https://docs.microsoft.com/azure/postgresql/overview#azure-database-for-postgresql---hyperscale-citus) dağıtımı için kullanılamaz.

PostgreSQL için Azure veritabanı 'nın [Azure Portal](https://portal.azure.com/)ayrılmış kapasitesini satın alabilirsiniz. Ayrılmış kapasiteyi satın almak için:

* Kullandıkça Öde tarifesine sahip en az bir kuruluş veya ayrı bir abonelik için sahip rolünde olmanız gerekir.
* Kurumsal abonelikler için, **EA portal**’da [Ayrılmış Örnek Ekle](https://ea.azure.com/) seçeneği etkinleştirilmelidir. Ya da bu ayar devre dışı bırakılırsa, abonelikte bir EA yöneticisi olmanız gerekir.
* Bulut çözümü sağlayıcısı (CSP) programı için, yalnızca yönetici aracıları veya satış aracıları, PostgreSQL için Azure veritabanı ayrılmış kapasitesini satın alabilir. </br>

Kurumsal müşterilerin ve kullandıkça öde müşterilerinin, rezervasyon satın alma işlemleri için nasıl ücretlendirilildiği hakkındaki ayrıntılar, bkz. [Kurumsal kaydınız Için Azure ayırma kullanımını anlama](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea) ve [Kullandıkça Öde için Azure rezervasyon kullanımını anlama abonelik](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage).


## <a name="determine-the-right-server-size-before-purchase"></a>Satın almadan önce doğru sunucu boyutunu belirle

Ayırma boyutu, belirli bir bölgedeki mevcut veya kısa süreli dağıtılan sunucular tarafından kullanılan ve aynı performans katmanını ve donanım üretimini kullanan toplam işlem miktarına bağlı olmalıdır.</br>

Örneğin, bir genel amaç, 5. nesil – 32 sanal çekirdek PostgreSQL veritabanı ve iki bellek için iyileştirilmiş, 5. nesil – 16 sanal çekirdek PostgreSQL veritabanlarını kullandığınızı varsayalım. Ayrıca, bir sonraki ay içinde, ek bir genel amaç, 5. nesil – 32 sanal çekirdek veritabanı sunucusu ve bir bellek için iyileştirilmiş, 5. nesil – 16 sanal çekirdek veritabanı sunucusu arasında dağıtmayı planladığınızı varsayalım. En az 1 yılda bu kaynaklara ihtiyacınız olacağını bildiğinizi varsayalım. Bu durumda, tek veritabanı belleği için iyileştirilmiş-5. nesil için 64 (2x32) sanal çekirdekleri, tek veritabanı için 1 yıl ayırma genel amaçlı-5. nesil ve 48 (2x16 + 16) sanal çekirdek 1 yıl ayırması satın almalısınız


## <a name="buy-azure-database-for-postgresql-reserved-capacity"></a>PostgreSQL için Azure veritabanı ayrılmış kapasitesi satın alın

1. [Azure portalında](https://portal.azure.com/) oturum açın.
2. **Tüm hizmetler** > **Rezervasyonlar**’ı seçin.
3. **Ekle** ' yi seçin ve ardından satın alma rezervasyonları bölmesinde PostgreSQL **için Azure veritabanı** ' nı seçerek PostgreSQL veritabanlarınız için yeni bir ayırma satın alın.
4. Gerekli alanları doldur. ' Yi seçtiğiniz özniteliklerle eşleşen mevcut veya yeni veritabanları, ayrılmış kapasite indirimi elde etmek için nitelendir ' ı seçin. İskontoyu alan PostgreSQL için Azure veritabanı sunucularınızın gerçek sayısı, seçilen kapsama ve miktara göre değişir.


![Ayrılmış fiyatlandırmaya genel bakış](media/concepts-reserved-pricing/postgresql-reserved-price.png)


Aşağıdaki tablo gerekli alanları açıklar.

| Alan | Açıklama |
| :------------ | :------- |
| Abonelik   | PostgreSQL için Azure veritabanı için ödeme yapmak üzere kullanılan abonelik ayrılmış kapasite ayırması. Abonelik üzerindeki ödeme yöntemi, PostgreSQL için Azure veritabanı ayrılmış kapasite rezervasyonuna ait ön maliyetlere göre ücretlendirilir. Abonelik türü bir kurumsal anlaşma (teklif numaraları: MS-AZR-0017P veya MS-AZR-0148P) veya kullandıkça öde fiyatlandırması (teklif numaraları: MS-AZR-0003P veya MS-AZR-0023P) içeren tek bir anlaşma olmalıdır. Kurumsal abonelik için ücretler kaydın maddi işlem bakiyesinden düşülür ve fazla kullanım olarak ücretlendirilir. Kullandıkça Öde fiyatlandırmasına sahip bireysel bir abonelik için ücretler, abonelik üzerindeki kredi kartına veya fatura ödeme yöntemine faturalandırılır.
| Kapsam | VCore rezervasyonunun kapsamı bir aboneliği veya birden çok aboneliği (paylaşılan kapsamı) kapsayabilir. Şunları seçerseniz: </br></br> **, Sanal**çekirdek ayırma indirimi, faturalandırma bağlamınızın içindeki aboneliklerde çalışan PostgreSQL sunucuları Için Azure veritabanı 'na uygulanır. Kurumsal müşteriler için, paylaşılan kapsam kayıt içindedir ve kayıt dahilindeki tüm abonelikleri içerir. Kullandıkça Öde müşterileri için paylaşılan kapsam, hesap yöneticisi tarafından oluşturulan tüm Kullandıkça Öde abonelikleridir.</br></br> **Tek abonelik**, sanal çekirdek ayırma indirimi Bu abonelikteki PostgreSQL Için Azure veritabanı sunucularına uygulanır. </br></br> **Tek kaynak grubu**, ayırma indirimi seçili abonelikteki PostgreSQL sunucuları Için Azure veritabanı ve bu abonelikteki seçili kaynak grubu için geçerlidir.
| Bölge | PostgreSQL için Azure veritabanı 'nın kapsamına giren Azure bölgesi ayrılmış kapasite ayırması.
| Dağıtım türü | Ayırmasını satın almak istediğiniz PostgreSQL için Azure veritabanı kaynak türü.
| Performans Katmanı | PostgreSQL için Azure veritabanı sunucularına yönelik hizmet katmanı.
| Sözleşme Dönemi | Bir yıl
| Miktar | PostgreSQL için Azure veritabanı 'nda satın alınan işlem kaynaklarının miktarı ayrılmış kapasite ayırması. Bu miktar, seçili Azure bölgesindeki ve performans katmanındaki, ayrılan ve fatura iskontosunu alacak olan sanal çekirdekler sayısıdır. Örneğin, Doğu ABD bölgesinde 5. nesil 16 sanal çekirdeklerinin toplam işlem kapasitesine sahip PostgreSQL için Azure veritabanı sunucuları çalıştırıyorsanız veya çalıştırmayı planlıyorsanız, tüm sunucuların avantajını en üst düzeye çıkarmak için miktarı 16 olarak belirtirsiniz.

## <a name="cancel-exchange-or-refund-reservations"></a>Rezervasyonları iptal etme, değiştirme veya para iadesi alma

Belirli sınırlamalarla rezervasyonları iptal edebilir, değiştirebilir veya para iadesi alabilirsiniz. Daha fazla bilgi için bkz. [Azure Ayrılmış Sanal Makine Örnekleri için self servis değişimler ve para iadeleri](https://docs.microsoft.com/azure/billing/billing-azure-reservations-self-service-exchange-and-refund).

## <a name="vcore-size-flexibility"></a>vCore boyutu esnekliği

vCore boyutu esnekliği, ayrılan kapasite avantajını kaybetmeden bir performans katmanı ve bölgesi içinde ölçeği büyütme veya küçültme olanakları sağlar. 

## <a name="need-help-contact-us"></a>Yardıma mı ihtiyacınız var? Bizimle iletişim kurun

Sorularınız varsa ya da yardıma gereksinim duyuyorsanız [destek isteği oluşturun](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Sonraki adımlar

VCore RESERVATION Discount, PostgreSQL için Azure veritabanı 'na ayrılmış kapasite ayırma kapsamı ve öznitelikleri ile eşleşen PostgreSQL için Azure veritabanı sunucularının sayısına otomatik olarak uygulanır. PostgreSQL için Azure veritabanı 'nın kapsamını Azure portal, PowerShell, CLı veya API aracılığıyla ayrılmış kapasite ayırma için güncelleştirebilirsiniz. </br></br>
PostgreSQL için Azure veritabanı 'na ayrılan kapasiteyi yönetme hakkında bilgi edinmek için bkz. PostgreSQL için Azure veritabanını yönetme ayrılmış kapasitesi.

Azure Ayrılmış Sanal Makine Örnekleri hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

* [Azure ayırmaları nelerdir](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)?
* [Azure Ayırmalarını yönetme](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)
* [Azure Ayrılmış Sanal Makine Örnekleri indirimini anlama](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges)
* [Kullandıkça Öde aboneliğiniz için rezervasyon kullanımını anlama](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges-postgresql)
* [Kurumsal kaydınız için rezervasyon kullanımını anlama](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)
* [İş Ortağı Merkezi Bulut Çözümü Sağlayıcısı (CSP) programındaki Azure Ayrılmış Sanal Makine Örnekleri](https://docs.microsoft.com/partner-center/azure-reservations)
