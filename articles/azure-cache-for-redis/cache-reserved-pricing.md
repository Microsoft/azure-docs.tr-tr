---
title: Ayrılmış kapasiteye sahip işlem için ön ödeme-Redsıs için Azure önbelleği
description: Ayrılmış kapasiteye sahip Redsıs işlem kaynakları için Azure önbelleği için ön ödeme
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 2f3472aa495042749410bc0b9635f0924a02e1fd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98598561"
---
# <a name="prepay-for-azure-cache-for-redis-compute-resources-with-reserved-capacity"></a>Ayrılmış kapasiteye sahip Redsıs işlem kaynakları için Azure önbelleği için ön ödeme

Redo için Azure Cache, Kullandıkça Öde fiyatlarıyla karşılaştırıldığında işlem kaynakları için ön ödeme yaparak paradan tasarruf etmenize yardımcı olur. Redin ayrılmış kapasitesi için Azure önbelleği ile, işlem maliyetlerine göre önemli bir indirim elde etmek için bir veya üç yıllık dönemde ön ödemeli bir taahhüt yaparsınız. Redsıs ayrılmış kapasitesi için Azure önbelleği satın almak üzere Azure bölgesini, hizmet katmanını ve terimi belirtmeniz gerekir.

Redsıs örnekleri için ayırmayı belirli Azure önbelleğine atamanız gerekmez. Zaten Redsıs veya yeni dağıtılan bir Azure önbelleği, ayrılmış önbelleğin büyüklüğüne kadar, ayrılmış fiyatlandırma avantajını otomatik olarak alır. Bir rezervasyon satın alarak, bir veya üç yıllık bir dönem için işlem maliyetleri için ön ödeme yaparsınız. Bir rezervasyon satın alarak, rezervasyon öznitelikleriyle eşleşen Redsıs işlem ücretleri için Azure önbelleği artık Kullandıkça Öde tarifelerine göre ücretlendirilir. Bir ayırma, önbellekle ilişkili ağ veya depolama ücretlerini kapsamaz. Rezervasyon döneminin sonunda, faturalandırma avantajı sona erer ve redin için Azure önbelleği, Kullandıkça Öde fiyatı üzerinden faturalandırılır. Ayırmalar otomatik olarak yenilemez. Fiyatlandırma bilgileri için bkz. [Red, ayrılmış kapasite teklifi Için Azure önbelleği](https://azure.microsoft.com/pricing/details/cache).

[Azure Portal](https://portal.azure.com/)redsıs ayrılmış kapasitesi Için Azure önbelleği satın alabilirsiniz. Ayrılmış kapasiteyi satın almak için:

* Kullandıkça Öde tarifesine sahip en az bir kuruluş veya ayrı bir abonelik için sahip rolünde olmanız gerekir.
* Kurumsal abonelikler için, [EA portal](https://ea.azure.com/)’da **Ayrılmış Örnek Ekle** seçeneği etkinleştirilmelidir. Ya da bu ayar devre dışı bırakılırsa, abonelikte bir EA yöneticisi olmanız gerekir.
* Bulut çözümü sağlayıcısı (CSP) programı için yalnızca yönetici aracıları veya satış aracıları, Red, ayrılmış kapasite için Azure önbelleği satın alabilir.

Kurumsal müşterilerin ve kullandıkça öde müşterilerinin rezervasyon satın alma işlemleri için nasıl ücretlendirilildiği hakkındaki ayrıntılar için bkz. [Kurumsal kaydınız Için Azure ayırma kullanımını anlayın](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) ve [Kullandıkça Öde aboneliğiniz için Azure rezervasyon kullanımını anlayın](../cost-management-billing/reservations/understand-reserved-instance-usage.md).


## <a name="determine-the-right-cache-size-before-purchase"></a>Satın almadan önce doğru önbellek boyutunu belirle

Ayırma boyutu, belirli bir bölgedeki mevcut veya hemen dağıtılmış önbellek tarafından kullanılan ve aynı hizmet katmanını kullanan toplam bellek boyutu miktarına bağlı olmalıdır.

Örneğin, 13 GB ve diğeri de 26 GB olmak üzere iki önbellek kullandığınızı varsayalım. Her ikisine de en az bir yıl gerek duyarsınız. Ayrıca, bir ay boyunca mevcut 13 GB önbellekler için, dönemsel taleplerinizi karşılayacak şekilde ölçeklendirediğinizi ve sonra geri ölçeklendirmenizi varsayalım. Bu durumda, tasarrufları en üst düzeye çıkarmak için 1 P2 Cache ve 1 P3 Cache veya 3 P2 önbellekler tek yıllık bir ayırma üzerinde satın alabilirsiniz. Bu miktarın önbelleğiniz arasında nasıl ayrılacağını bağımsız olarak, ayırmış olduğunuz toplam önbellek belleği miktarına göre indirim alacaksınız.


## <a name="buy-azure-cache-for-redis-reserved-capacity"></a>Redsıs ayrılmış kapasitesi için Azure önbelleği satın alın

[Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/)ayrılmış bir sanal makine örneği satın alabilirsiniz. [Peşin olarak veya aylık ödemelerle](../cost-management-billing/reservations/prepare-buy-reservation.md) rezervasyon ödemesi yapın.

1. [Azure Portal](https://portal.azure.com/) oturum açın.
2. **Tüm hizmetler** > **Rezervasyonlar**’ı seçin.
3. **Ekle** ' yi seçin ve ardından satın alma rezervasyonları bölmesinde önbelleğiniz için yeni bir ayırma satın almak üzere **Redsıs için Azure önbelleği** ' ni seçin.
4. Gerekli alanları doldur. ' Yi seçtiğiniz özniteliklerle eşleşen mevcut veya yeni veritabanları, ayrılmış kapasite indirimi elde etmek için nitelendir ' ı seçin. İndirimin alınacağı Redsıs örnekleri için Azure önbelleğinizin gerçek sayısı, seçilen kapsama ve miktara bağlıdır.


![Ayrılmış fiyatlandırmaya genel bakış](media/cache-reserved-pricing/cache-reserved-price.png)


Aşağıdaki tablo gerekli alanları açıklar.

| Alan | Açıklama |
| :------------ | :------- |
| Abonelik   | Reda için Azure önbelleği için ödeme yapmak üzere kullanılan abonelik, ayrılmış kapasite ayırması. Abonelik üzerindeki ödeme yöntemi, Red, ayrılmış kapasite rezervasyonu için Azure önbelleğinin ön maliyetlerini ücretlendirilmiştir. Abonelik türü bir kurumsal anlaşma (teklif numaraları: MS-AZR-0017P veya MS-AZR-0148P) veya kullandıkça öde fiyatlandırması (teklif numaraları: MS-AZR-0003P veya MS-AZR-0023P) içeren tek bir anlaşma olmalıdır. Kurumsal abonelik için ücretler kaydın Azure Ön Ödemesinden (eski adıyla parasal taahhüt) düşülür ve fazla kullanım olarak ücretlendirilir. Kullandıkça Öde fiyatlandırmasına sahip bireysel bir abonelik için ücretler, abonelik üzerindeki kredi kartına veya fatura ödeme yöntemine faturalandırılır.
| Kapsam | Ayırma kapsamı bir aboneliği veya birden çok aboneliği (paylaşılan kapsamı) kapsayabilir. Şunları seçerseniz: </br></br> **, Rezervasyon** indirimi, faturalandırma bağlamınızın içindeki aboneliklerde çalışan redsıs örnekleri Için Azure önbelleğine uygulanır. Kurumsal müşteriler için, paylaşılan kapsam kayıt içindedir ve kayıt dahilindeki tüm abonelikleri içerir. Kullandıkça Öde müşterileri için paylaşılan kapsam, hesap yöneticisi tarafından oluşturulan tüm Kullandıkça Öde abonelikleridir.</br></br> **Tek abonelik**, rezervasyon indirimi Bu abonelikteki redsıs örnekleri Için Azure önbelleğine uygulanır. </br></br> **Tek kaynak grubu**, ayırma indirimi seçili abonelikteki redsıs örnekleri ve bu abonelik içindeki seçili kaynak grubu Için Azure önbelleğine uygulanır.
| Region | Reddir için Azure önbelleğinin kapsamına giren Azure bölgesi ayrılmış kapasite ayırması.
| Fiyatlandırma katmanı | Redsıs sunucuları için Azure önbelleğinin hizmet katmanı.
| Süre | Bir yıl veya üç yıl
| Miktar | Reda için Azure önbelleği içinde satın alınan işlem kaynakları miktarı, ayrılmış kapasite ayırması. Bu miktar, seçili Azure bölgesindeki ve hizmet katmanındaki ayrılan ve fatura iskontosunu alacak olan bir dizi önbelleklerdir. Örneğin, Doğu ABD bölgesinde toplam önbellek kapasitesi 16 GB olan Redsıs sunucuları için bir Azure önbelleği çalıştırmayı planlıyorsanız veya, tüm önbellekler için avantajı en üst düzeye çıkarmak üzere 26 GB eşdeğerini sağlayan miktarı belirtirsiniz. Bu 1 P3 veya 2 P2 önbellekler olabilir.

## <a name="cancel-exchange-or-refund-reservations"></a>Rezervasyonları iptal etme, değiştirme veya para iadesi alma

Belirli sınırlamalarla rezervasyonları iptal edebilir, değiştirebilir veya para iadesi alabilirsiniz. Daha fazla bilgi için bkz. [Azure Ayrılmış Sanal Makine Örnekleri için self servis değişimler ve para iadeleri](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="cache-size-flexibility"></a>Önbellek boyutu esnekliği

Önbellek boyutu esnekliği, ayrılmış kapasite avantajını kaybetmeden bir hizmet katmanı ve bölgesi dahilinde ölçeği büyütme veya küçültme olanakları sağlar.

## <a name="need-help-contact-us"></a>Yardıma mı ihtiyacınız var? Bizimle iletişim kurun

Sorularınız varsa ya da yardıma gereksinim duyuyorsanız [destek isteği oluşturun](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Sonraki adımlar

Rezervasyon indirimi, ayırma kapsamı ve öznitelikleriyle eşleşen Redsıs örnekleri için Azure önbelleğine otomatik olarak uygulanır. Azure portal, PowerShell, Azure CLı veya API aracılığıyla rezervasyon kapsamını güncelleştirebilirsiniz.

*  Ayrılmış kapasite iskontolarının redin için Azure önbelleğine nasıl uygulanacağını öğrenmek için bkz [. Azure rezervasyon ıskontosunu anlama](../cost-management-billing/reservations/understand-azure-cache-for-redis-reservation-charges.md)

* Azure Ayrılmış Sanal Makine Örnekleri hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

    * [Azure Ayrılmış Sanal Makine Örnekleri nedir?](../cost-management-billing/reservations/save-compute-costs-reservations.md)
    * [Azure Ayırmalarını yönetme](../cost-management-billing/reservations/manage-reserved-vm-instance.md)
    * [Azure Ayrılmış Sanal Makine Örnekleri indirimini anlama](../cost-management-billing/reservations/understand-reservation-charges.md)
    * [Kullandıkça Öde aboneliğiniz için rezervasyon kullanımını anlama](../cost-management-billing/reservations/understand-reservation-charges-mysql.md)
    * [Kurumsal kaydınız için rezervasyon kullanımını anlama](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
    * [İş Ortağı Merkezi Bulut Çözümü Sağlayıcısı (CSP) programındaki Azure Ayrılmış Sanal Makine Örnekleri](/partner-center/azure-reservations)