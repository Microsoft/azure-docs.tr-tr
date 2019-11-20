---
title: Ayrılmış kapasiteye sahip MySQL için Azure veritabanı işlem kaynakları için ön ödeme
description: Ayrılmış kapasiteye sahip MySQL için Azure veritabanı işlem kaynakları için ön ödeme
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: 595a96d07cc1804d3ee0830618a70557b768a3b5
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74067101"
---
# <a name="prepay-for-azure-database-for-mysql-compute-resources-with-reserved-capacity"></a>Ayrılmış kapasiteye sahip MySQL için Azure veritabanı işlem kaynakları için ön ödeme

MySQL için Azure veritabanı artık, Kullandıkça Öde fiyatlarına kıyasla işlem kaynakları için ön ödeme yaparak paradan tasarruf etmenize yardımcı olur. MySQL için Azure veritabanı kapasitesi ile, işlem maliyetlerine göre önemli bir indirim elde etmek üzere MySQL Server 'da bir yıllık dönem için bir ön taahhüt yaparsınız. MySQL için Azure veritabanı 'na ayrılan kapasiteyi satın almak için Azure bölgesini, dağıtım türünü, performans katmanını ve terimi belirtmeniz gerekir. </br>

Ayırmayı, MySQL için belirli Azure veritabanı sunucularına atamanız gerekmez. Zaten bir MySQL için Azure veritabanı veya yeni dağıtılmış olanlar, ayrılmış fiyatlandırma avantajını otomatik olarak alır. Bir rezervasyon satın alarak, bir yıllık dönem için işlem maliyetleri için ön ödeme yaparsınız. Bir rezervasyon satın alarak, rezervasyon öznitelikleriyle eşleşen MySQL için Azure veritabanı işlem ücretleri artık Kullandıkça Öde tarifelerine göre ücretlendirilir. Bir ayırma, MySQL veritabanı sunucusuyla ilişkili yazılım, ağ veya depolama ücretlerini kapsamaz. Rezervasyon döneminin sonunda, faturalandırma avantajı sona erer ve MySQL için Azure veritabanı, Kullandıkça Öde fiyatı üzerinden faturalandırılır. Ayırmalar otomatik olarak yenilemez. Fiyatlandırma bilgileri için bkz. [MySQL Için Azure veritabanı ayrılmış kapasite teklifi](https://azure.microsoft.com/pricing/details/mysql/). </br>

MySQL için Azure veritabanı ayrılmış kapasitesini [Azure Portal](https://portal.azure.com/)satın alabilirsiniz. Ayrılmış kapasiteyi satın almak için:

* Kullandıkça Öde tarifesine sahip en az bir kuruluş veya ayrı bir abonelik için sahip rolünde olmanız gerekir.
* Kurumsal abonelikler için, **EA portal**’da [Ayrılmış Örnek Ekle](https://ea.azure.com/) seçeneği etkinleştirilmelidir. Ya da bu ayar devre dışı bırakılırsa, abonelikte bir EA yöneticisi olmanız gerekir.
* Bulut çözümü sağlayıcısı (CSP) programı için, yalnızca yönetici aracıları veya satış aracıları, MySQL için Azure veritabanı ayrılmış kapasitesini satın alabilir. </br>

Kurumsal müşterilerin ve kullandıkça öde müşterilerinin, rezervasyon satın alma işlemleri için nasıl ücretlendirilildiği hakkındaki ayrıntılar, bkz. [Kurumsal kaydınız Için Azure ayırma kullanımını anlama](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea) ve [Kullandıkça Öde için Azure rezervasyon kullanımını anlama abonelik](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage).


## <a name="determine-the-right-database-size-before-purchase"></a>Satın almadan önce doğru veritabanı boyutunu belirleme

Ayırma boyutu, belirli bir bölgedeki mevcut veya kısa süreli dağıtılan sunucu tarafından kullanılan ve aynı performans katmanını ve donanım üretimini kullanan toplam işlem miktarına bağlı olmalıdır.</br>

Örneğin, bir genel amaç, 5. nesil – 32 sanal çekirdek MySQL veritabanı ve iki bellek için iyileştirilmiş, 5. nesil – 16 sanal çekirdek MySQL veritabanları kullandığınızı varsayalım. Ayrıca, bir sonraki ay içinde, ek bir genel amaç, 5. nesil – 32 sanal çekirdek veritabanı sunucusu ve bir bellek için iyileştirilmiş, 5. nesil – 16 sanal çekirdek veritabanı sunucusu arasında dağıtmayı planladığınızı varsayalım. En az 1 yılda bu kaynaklara ihtiyacınız olacağını bildiğinizi varsayalım. Bu durumda, tek veritabanı belleği için iyileştirilmiş-5. nesil için 64 (2x32) sanal çekirdekleri, tek veritabanı için 1 yıl ayırma genel amaçlı-5. nesil ve 48 (2x16 + 16) sanal çekirdek 1 yıl ayırması satın almalısınız


## <a name="buy-azure-database-for-mysql-reserved-capacity"></a>MySQL için Azure veritabanı ayrılmış kapasitesi satın alın

1. [Azure portalında](https://portal.azure.com/) oturum açın.
2. **Tüm hizmetler** > **Rezervasyonlar**’ı seçin.
3. **Ekle** ' yi seçin ve ardından satın alma rezervasyonları bölmesinde MySQL **için Azure veritabanı** ' nı seçerek MySQL veritabanlarınız için yeni bir ayırma satın alabilirsiniz.
4. Gerekli alanları doldur. ' Yi seçtiğiniz özniteliklerle eşleşen mevcut veya yeni veritabanları, ayrılmış kapasite indirimi elde etmek için nitelendir ' ı seçin. İskontoyu alan MySQL için Azure veritabanı sunucularınızın gerçek sayısı, seçilen kapsama ve miktara bağlıdır.


![Ayrılmış fiyatlandırmaya genel bakış](media/concepts-reserved-pricing/mysql-reserved-price.png)


Aşağıdaki tablo gerekli alanları açıklar.

| Alan | Açıklama |
| :------------ | :------- |
| Abonelik   | MySQL için Azure veritabanı ayrılmış kapasite ayırması için ödeme yapmak üzere kullanılan abonelik. Abonelikteki ödeme yöntemi, MySQL için Azure veritabanı ayrılmış kapasite rezervasyonu için ön maliyetler üzerinden ücretlendirilir. Abonelik türü bir kurumsal anlaşma (teklif numaraları: MS-AZR-0017P veya MS-AZR-0148P) veya kullandıkça öde fiyatlandırması (teklif numaraları: MS-AZR-0003P veya MS-AZR-0023P) içeren tek bir anlaşma olmalıdır. Kurumsal abonelik için ücretler kaydın maddi işlem bakiyesinden düşülür ve fazla kullanım olarak ücretlendirilir. Kullandıkça Öde fiyatlandırmasına sahip bireysel bir abonelik için ücretler, abonelik üzerindeki kredi kartına veya fatura ödeme yöntemine faturalandırılır.
| Kapsam | VCore rezervasyonunun kapsamı bir aboneliği veya birden çok aboneliği (paylaşılan kapsamı) kapsayabilir. Şunları seçerseniz: </br></br> **, Sanal**çekirdek ayırma indirimi, faturalandırma bağlamınızın içindeki aboneliklerde çalışan MySQL sunucuları Için Azure veritabanı 'na uygulanır. Kurumsal müşteriler için, paylaşılan kapsam kayıt içindedir ve kayıt dahilindeki tüm abonelikleri içerir. Kullandıkça Öde müşterileri için paylaşılan kapsam, hesap yöneticisi tarafından oluşturulan tüm Kullandıkça Öde abonelikleridir.</br></br> **Tek abonelik**, sanal çekirdek ayırma indirimi Bu abonelikteki MySQL sunucuları Için Azure veritabanı 'na uygulanır. </br></br> **Tek kaynak grubu**, rezervasyon indirimi seçili abonelikteki MySQL sunucuları Için Azure veritabanı ve bu abonelikteki seçili kaynak grubu için geçerlidir.
| Bölge | MySQL için Azure veritabanı ayrılmış kapasite rezervasyonu kapsamındaki Azure bölgesi.
| Dağıtım türü | Ayırmasını satın almak istediğiniz MySQL için Azure veritabanı kaynak türü.
| Performans Katmanı | MySQL için Azure veritabanı sunucuları için hizmet katmanı.
| Sözleşme Dönemi | Bir yıl
| Miktar | MySQL için Azure veritabanı için satın alınan işlem kaynakları miktarı ayrılmış kapasite ayırması. Bu miktar, seçili Azure bölgesindeki ve performans katmanındaki, ayrılan ve fatura iskontosunu alacak olan sanal çekirdekler sayısıdır. Örneğin, Doğu ABD bölgesinde 5. nesil 16 sanal çekirdeklerinin toplam işlem kapasitesine sahip bir MySQL için Azure veritabanı sunucusunu çalıştırıyorsanız veya çalıştırmayı planlıyorsanız, tüm sunucuların avantajını en üst düzeye çıkarmak için miktarı 16 olarak belirtirsiniz.

## <a name="cancel-exchange-or-refund-reservations"></a>Rezervasyonları iptal etme, değiştirme veya para iadesi alma

Belirli sınırlamalarla rezervasyonları iptal edebilir, değiştirebilir veya para iadesi alabilirsiniz. Daha fazla bilgi için bkz. [Azure Ayrılmış Sanal Makine Örnekleri için self servis değişimler ve para iadeleri](https://docs.microsoft.com/azure/billing/billing-azure-reservations-self-service-exchange-and-refund).

## <a name="vcore-size-flexibility"></a>vCore boyutu esnekliği

vCore boyutu esnekliği, ayrılan kapasite avantajını kaybetmeden bir performans katmanı ve bölgesi içinde ölçeği büyütme veya küçültme olanakları sağlar. 

## <a name="need-help--contact-us"></a>Yardım mı gerekiyor? Bizimle iletişim kurun

Sorularınız varsa ya da yardıma gereksinim duyuyorsanız [destek isteği oluşturun](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Sonraki adımlar

VCore ayırma indirimi, MySQL için Azure veritabanı 'na ayrılan kapasite ayırma kapsamı ve öznitelikleri ile eşleşen MySQL sunucularının Azure veritabanı sayısına otomatik olarak uygulanır. MySQL için Azure veritabanı 'nın kapsamını Azure portal, PowerShell, CLı veya API aracılığıyla ayrılmış kapasite ayırma için güncelleştirebilirsiniz. </br></br>
MySQL için Azure veritabanı 'na ayrılan kapasiteyi yönetme hakkında bilgi edinmek için bkz. MySQL için Azure veritabanı ayrılmış kapasitesi.

Azure Ayrılmış Sanal Makine Örnekleri hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

* [Azure ayırmaları nelerdir](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)?
* [Azure Ayırmalarını yönetme](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)
* [Azure Ayrılmış Sanal Makine Örnekleri indirimini anlama](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges)
* [Kullandıkça Öde aboneliğiniz için rezervasyon kullanımını anlama](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges-mysql)
* [Kurumsal kaydınız için rezervasyon kullanımını anlama](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)
* [İş Ortağı Merkezi Bulut Çözümü Sağlayıcısı (CSP) programındaki Azure Ayrılmış Sanal Makine Örnekleri](https://docs.microsoft.com/partner-center/azure-reservations)

