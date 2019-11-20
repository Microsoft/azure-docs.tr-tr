---
title: Ayrılmış kapasiteye sahip MariaDB işlem kaynakları için Azure veritabanı için ön ödeme
description: Ayrılmış kapasiteye sahip MariaDB işlem kaynakları için Azure veritabanı için ön ödeme
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: 61736d4f7e6b17c6037837bfdfe6d9cb8a00c5f6
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74076862"
---
# <a name="prepay-for-azure-database-for-mariadb-compute-resources-with-reserved-capacity"></a>Ayrılmış kapasiteye sahip MariaDB işlem kaynakları için Azure veritabanı için ön ödeme

MariaDB için Azure veritabanı artık, Kullandıkça Öde fiyatlarına kıyasla işlem kaynakları için ön ödeme yaparak paradan tasarruf etmenize yardımcı olur. MariaDB için Azure veritabanı ile ayrılmış kapasite ile, işlem maliyetlerine göre önemli bir indirim elde etmek üzere MariaDB sunucularında bir yıllık bir dönem için ön taahhüt yaparsınız. MariaDB için Azure veritabanı 'na ayrılmış kapasite satın almak için Azure bölgesini, dağıtım türünü, performans katmanını ve terimi belirtmeniz gerekir. </br>

Bu ayırmayı, MariaDB sunucuları için belirli Azure veritabanına atamanız gerekmez. MariaDB için Azure veritabanı veya yeni dağıtılan olanlar, ayrılmış fiyatlandırma avantajını otomatik olarak alır. Bir rezervasyon satın alarak, bir yıllık dönem için işlem maliyetleri için ön ödeme yaparsınız. Bir ayırma satın alarak, rezervasyon öznitelikleriyle eşleşen MariaDB işlem ücretleri için Azure veritabanı, artık Kullandıkça Öde tarifelerine göre ücretlendirilir. Bir ayırma, MariaDB veritabanı sunucusuyla ilişkili yazılım, ağ veya depolama ücretlerini kapsamaz. Rezervasyon döneminin sonunda, faturalandırma avantajı sona erer ve MariaDB için Azure veritabanı, Kullandıkça Öde fiyatı üzerinden faturalandırılır. Ayırmalar otomatik olarak yenilemez. Fiyatlandırma bilgileri için bkz. [MariaDB Için Azure veritabanı ayrılmış kapasite teklifi](https://azure.microsoft.com/pricing/details/mariadb/). </br>

MariaDB için Azure veritabanı ayrılmış kapasitesi [Azure Portal](https://portal.azure.com/)satın alabilirsiniz. Ayrılmış kapasiteyi satın almak için:

* Kullandıkça Öde tarifesine sahip en az bir kuruluş veya ayrı bir abonelik için sahip rolünde olmanız gerekir.
* Kurumsal abonelikler için, **EA portal**’da [Ayrılmış Örnek Ekle](https://ea.azure.com/) seçeneği etkinleştirilmelidir. Ya da bu ayar devre dışı bırakılırsa, abonelikte bir EA yöneticisi olmanız gerekir.
* Bulut çözümü sağlayıcısı (CSP) programı için, yalnızca yönetici aracıları veya satış aracıları, MariaDB için Azure veritabanı ayrılmış kapasitesi için satın alabilir. </br>

Kurumsal müşterilerin ve kullandıkça öde müşterilerinin, rezervasyon satın alma işlemleri için nasıl ücretlendirilildiği hakkındaki ayrıntılar, bkz. [Kurumsal kaydınız Için Azure ayırma kullanımını anlama](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea) ve [Kullandıkça Öde için Azure rezervasyon kullanımını anlama abonelik](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage).


## <a name="determine-the-right-server-size-before-purchase"></a>Satın almadan önce doğru sunucu boyutunu belirle

Ayırma boyutu, belirli bir bölgedeki mevcut veya hemen dağıtılmış veritabanları örneği tarafından kullanılan ve aynı performans katmanını ve donanım üretimini kullanan toplam işlem miktarına bağlı olmalıdır.</br>

Örneğin, bir genel amaç, 5. nesil – 32 vCore MariaDB veritabanı ve iki bellek için iyileştirilmiş, 5. nesil – 16 sanal çekirdek MariaDB veritabanlarını çalıştırdığınızı varsayalım. Ayrıca, bir sonraki ay içinde, ek bir genel amaç, 5. nesil – 32 sanal çekirdek veritabanı sunucusu ve bir bellek için iyileştirilmiş, 5. nesil – 16 sanal çekirdek veritabanı sunucusu arasında dağıtmayı planladığınızı varsayalım. En az 1 yılda bu kaynaklara ihtiyacınız olacağını bildiğinizi varsayalım. Bu durumda, tek veritabanı belleği için iyileştirilmiş-5. nesil için 64 (2x32) sanal çekirdekleri, tek veritabanı için 1 yıl ayırma genel amaçlı-5. nesil ve 48 (2x16 + 16) sanal çekirdek 1 yıl ayırması satın almalısınız


## <a name="buy-azure-database-for-mariadb-reserved-capacity"></a>MariaDB için Azure veritabanı ayrılmış kapasitesi satın alın

1. [Azure portalında](https://portal.azure.com/) oturum açın.
2. **Tüm hizmetler** > **Rezervasyonlar**’ı seçin.
3.  **Ekle** ' yi seçin ve ardından satın alma rezervasyonları bölmesinde, MariaDB veritabanlarınızı Için **Azure veritabanı** ' nı seçin.
4.  Gerekli alanları doldur. ' Yi seçtiğiniz özniteliklerle eşleşen mevcut veya yeni veritabanları, ayrılmış kapasite indirimi elde etmek için nitelendir ' ı seçin. İndirimi alan MariaDB sunucuları için Azure veritabanınızın gerçek sayısı, seçilen kapsama ve miktara göre değişir.


![Ayrılmış fiyatlandırmaya genel bakış](media/concepts-reserved-pricing/mariadb-reserved-price.png)


Aşağıdaki tablo gerekli alanları açıklar.

| Alan | Açıklama |
| :------------ | :------- |
| Abonelik   | MariaDB için Azure veritabanı ayrılmış kapasite ayırması için ödeme yapmak üzere kullanılan abonelik. Abonelik üzerindeki ödeme yöntemi, MariaDB için Azure veritabanı ayrılmış kapasite rezervasyonu için ön maliyetler üzerinden ücretlendirilir. Abonelik türü bir kurumsal anlaşma (teklif numaraları: MS-AZR-0017P veya MS-AZR-0148P) veya kullandıkça öde fiyatlandırması (teklif numaraları: MS-AZR-0003P veya MS-AZR-0023P) içeren tek bir anlaşma olmalıdır. Kurumsal abonelik için ücretler kaydın maddi işlem bakiyesinden düşülür ve fazla kullanım olarak ücretlendirilir. Kullandıkça Öde fiyatlandırmasına sahip bireysel bir abonelik için ücretler, abonelik üzerindeki kredi kartına veya fatura ödeme yöntemine faturalandırılır.
| Kapsam | VCore rezervasyonunun kapsamı bir aboneliği veya birden çok aboneliği (paylaşılan kapsamı) kapsayabilir. Şunları seçerseniz: </br></br> **, Sanal**çekirdek ayırma indirimi, faturalandırma bağlamınızın içindeki aboneliklerde çalışan MariaDB sunucuları Için Azure veritabanı 'na uygulanır. Kurumsal müşteriler için, paylaşılan kapsam kayıt içindedir ve kayıt dahilindeki tüm abonelikleri içerir. Kullandıkça Öde müşterileri için paylaşılan kapsam, hesap yöneticisi tarafından oluşturulan tüm Kullandıkça Öde abonelikleridir.</br></br> **Tek abonelik**, sanal çekirdek ayırma indirimi Bu abonelikteki MariaDB sunucuları Için Azure veritabanı 'na uygulanır. </br></br> **Tek kaynak grubu**, rezervasyon indirimi seçili abonelikteki MariaDB sunucuları Için Azure veritabanı ve bu abonelikteki seçili kaynak grubu için geçerlidir.
| Bölge | MariaDB için Azure veritabanı ayrılmış kapasite rezervasyonu kapsamındaki Azure bölgesi.
| Dağıtım türü | Ayırmasını satın almak istediğiniz MariaDB için Azure veritabanı kaynak türü.
| Performans Katmanı | MariaDB sunucuları için Azure veritabanı hizmet katmanı.
| Sözleşme Dönemi | Bir yıl
| Miktar | MariaDB için Azure veritabanı 'nda satın alınan işlem kaynaklarının miktarı ayrılmış kapasite ayırması. Bu miktar, seçili Azure bölgesindeki ve performans katmanındaki, ayrılan ve fatura iskontosunu alacak olan sanal çekirdekler sayısıdır. Örneğin, Doğu ABD bölgesinde 5. nesil 16 sanal çekirdeklerinin toplam işlem kapasitesine sahip MariaDB sunucuları için Azure veritabanı 'nı çalıştırıyorsanız veya çalıştırmayı planlıyorsanız, tüm sunucuların avantajını en üst düzeye çıkarmak için miktarı 16 olarak belirtirsiniz.

## <a name="cancel-exchange-or-refund-reservations"></a>Rezervasyonları iptal etme, değiştirme veya para iadesi alma

Belirli sınırlamalarla rezervasyonları iptal edebilir, değiştirebilir veya para iadesi alabilirsiniz. Daha fazla bilgi için bkz. [Azure Ayrılmış Sanal Makine Örnekleri için self servis değişimler ve para iadeleri](https://docs.microsoft.com/azure/billing/billing-azure-reservations-self-service-exchange-and-refund).

## <a name="vcore-size-flexibility"></a>vCore boyutu esnekliği

vCore boyutu esnekliği, ayrılan kapasite avantajını kaybetmeden bir performans katmanı ve bölgesi içinde ölçeği büyütme veya küçültme olanakları sağlar. 

## <a name="need-help-contact-us"></a>Yardıma mı ihtiyacınız var? Bizimle iletişim kurun

Sorularınız varsa ya da yardıma gereksinim duyuyorsanız [destek isteği oluşturun](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Sonraki adımlar

VCore ayırma indirimi, MariaDB için Azure veritabanı için Azure veritabanı 'na ayrılmış kapasite ayırma kapsamı ve öznitelikleri için Azure veritabanı sayısına otomatik olarak uygulanır. MariaDB için Azure veritabanı 'nın kapsamını Azure portal, PowerShell, CLı veya API aracılığıyla ayrılmış kapasite ayırması için güncelleştirebilirsiniz. </br></br>
MariaDB için Azure veritabanı 'nın ayrılmış kapasitesini yönetme hakkında bilgi edinmek için bkz. MariaDB için Azure veritabanı ayrılmış kapasitesi.

Azure Ayrılmış Sanal Makine Örnekleri hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

* [Azure ayırmaları nelerdir](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)?
* [Azure Ayırmalarını yönetme](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)
* [Azure Ayrılmış Sanal Makine Örnekleri indirimini anlama](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges)
* [Kullandıkça Öde aboneliğiniz için rezervasyon kullanımını anlama](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges-mariadb)
* [Kurumsal kaydınız için rezervasyon kullanımını anlama](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)
* [İş Ortağı Merkezi Bulut Çözümü Sağlayıcısı (CSP) programındaki Azure Ayrılmış Sanal Makine Örnekleri](https://docs.microsoft.com/partner-center/azure-reservations)
