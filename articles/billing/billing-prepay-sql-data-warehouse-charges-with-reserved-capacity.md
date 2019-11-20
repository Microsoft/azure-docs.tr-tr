---
title: Azure ayrılmış kapasitesi ile SQL Veri Ambarı ücretlerinden maliyet tasarrufu sağlama
description: Para tasarrufu sağlamak için ayrılmış kapasite ile SQL Veri Ambarı ücretleri için nasıl maliyet tasarrufu yapacağınızı öğrenin.
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: banders
ms.openlocfilehash: 381a709c74f5fcf6bb1f89f07ad84d5e3af0c5e0
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70806286"
---
# <a name="save-costs-for-sql-data-warehouse-charges-with-reserved-capacity"></a>Ayrılmış kapasite ile SQL Veri Ambarı ücretlerinden maliyet tasarrufu sağlama

Bir ile üç yıl arasındaki cDWU kullanımınız için bir rezervasyon taahhüdü vererek Azure SQL Veri Ambarı’nda tasarruf elde edebilirsiniz. SQL Veri Ambarı ayrılmış kapasitesi satın almak için, Azure bölgesini ve dönemi seçmeniz gerekir. Sonra SQL Veri Ambarı SKU’sunu sepetinize ekleyin ve satın almak istediğiniz cDWU birimi miktarını seçin.

Rezervasyon satın aldığınızda, rezervasyonun öznitelikleriyle eşleşen SQL Veri Ambarı kullanımı artık kullandıkça öde fiyatlarıyla ücretlendirilmez.

SQL Veri Ambarı’nın kullanımıyla ilişkili depolama ve ağ ücretleri rezervasyonun kapsamında değildir.

Ayrılmış kapasitenin süresi dolduğunda SQL Veri Ambarı örnekleri çalışmaya devam eder, ancak kullandıkça öde fiyatlarıyla faturalanır. Rezervasyonlar otomatik olarak yenilenmez.

Fiyatlandırma bilgileri için bkz [SQL Veri Ambarı ayrılmış kapasite teklifi](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/).

[Azure portalında](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) Azure SQL Veri Ambarı ayrılmış kapasitesi satın alabilirsiniz. [Peşin olarak veya aylık ödemelerle](billing-monthly-payments-reservations.md) rezervasyon ödemesi yapın. Ayrılmış kapasite satın almak için:

- En az bir kurumsal veya Kullandıkça Öde aboneliği için sahip rolüne sahip olmanız gerekir.
- Kurumsal abonelikler için, [EA portal](https://ea.azure.com/)’da **Ayrılmış Örnek Ekle** seçeneği etkinleştirilmelidir. Ayar devre dışı bırakılırsa EA Yöneticisi olmanız gerekir.
- Bulut Çözümü Sağlayıcısı (CSP) programı için yalnızca yönetici aracılar veya satış temsilcileri, SQL Veri Ambarı ayrılmış kapasitesi satın alabilir.

Kurumsal müşteriler ve Kullandıkça Öde müşterilerinin, rezervasyon satın alımları için nasıl ücretlendirildiği hakkında daha fazla bilgi için bkz. [Kurumsal kaydınız için Azure rezervasyon kullanımını anlama](billing-understand-reserved-instance-usage-ea.md) ve [Kullandıkça Öde aboneliğiniz için Azure rezervasyon kullanımını anlama](billing-understand-reserved-instance-usage.md).

## <a name="choose-the-right-size-before-purchase"></a>Satın almadan önce doğru boyutu seçme

SQL Veri Ambarı rezervasyon boyutu, kullandığınız toplam işlem veri ambarı birimlerini (cDWU) temel almalıdır. Satın almalar, 100’er cDWU artımlarla gerçekleştirilir.

Örneğin, toplam SQL Veri Ambarı tüketiminizin DW3000c olduğunu varsayın. Tümü için ayrılmış kapasite satın almak istiyorsunuz. Bu nedenle 30 birim cDWU ayrılmış kapasitesi satın almanız gerekir.

## <a name="buy-sql-data-warehouse-reserved-capacity"></a>SQL Veri Ambarı ayrılmış kapasitesi satın alma

1. [Azure Portal](https://portal.azure.com/) oturum açın.
2. **Tüm hizmetler** > **Rezervasyonlar**’ı seçin.
3. Bir abonelik seçin. Ayrılmış kapasitenin ödemesini yapmak için kullanılan aboneliği seçmek amacıyla Abonelik listesini kullanın. Ayrılmış kapasite maliyetleri, aboneliğin ödeme yöntemiyle ücretlendirilir. Abonelik türü bir kurumsal anlaşma (teklif numarası: MS-AZR-0017P veya MS-AZR-0148P) ya da Kullandıkça Öde (teklif numarası: MS-AZR-0003P veya MS-AZR-0023P).
   - Kurumsal abonelik için ücretler kaydın maddi işlem bakiyesinden düşülür ve fazla kullanım olarak ücretlendirilir.
   - Kullandıkça Öde aboneliğinde ücretler, aboneliğin kredi kartı veya fatura ödeme yöntemi ile faturalandırılır.
4. Bir kapsam seçin. Kapsam listesini kullanarak bir abonelik kapsamı seçin.
   - **Tek kaynak grubu kapsamı**: Yalnızca seçilen kaynak grubunda eşleşen kaynaklara rezervasyon indirimini uygular.
   - **Tek abonelik kapsamı**: Yalnızca seçilen abonelikte eşleşen kaynaklara rezervasyon indirimini uygular.
   - **Paylaşılan kapsam**: Faturalama bağlamında bulunan uygun aboneliklerdeki eşleşen kaynaklara rezervasyon indirimini uygular. Kurumsal Anlaşma müşterileri için faturalama bağlamı kayıttır. Kullandıkça öde fiyatlarına tabi bireysel abonelikler için faturalama kapsamı, hesap yöneticisi tarafından oluşturulan tüm uygun aboneliklerdir.
   - Kurumsal müşteriler için faturalama bağlamı, EA kaydıdır.
   - Kullandıkça Öde müşterileri için paylaşılan kapsam, hesap yöneticisi tarafından oluşturulan tüm Kullandıkça Öde abonelikleridir.
5. Ayrılmış kapasite kapsamındaki bir Azure bölgesi seçmek için bir bölge seçin.
6. Bir miktar seçin. Satın almak istediğiniz 100 Veri Ambarı birimi (cDWU) miktarını girin.    
   Örneğin, 30 miktarı size her saat 3.000 cDWU ayrılmış kapasite sunar.
7. **Maliyetler** bölümünde SQL Veri Ambarı ayrılmış kapasite rezervasyonunu gözden geçirin.
8. **Satın al**'ı seçin.
9. Satın alma durumunuzu görmek için **Bu Rezervasyonu Görüntüle** seçeneğini belirleyin.

## <a name="cancel-exchange-or-refund-reservations"></a>Rezervasyonları iptal etme, değiştirme veya para iadesi alma

Belirli sınırlamalarla rezervasyonları iptal edebilir, değiştirebilir veya para iadesi alabilirsiniz. Daha fazla bilgi için bkz. [Azure Ayrılmış Sanal Makine Örnekleri için self servis değişimler ve para iadeleri](billing-azure-reservations-self-service-exchange-and-refund.md).

SQL Veri Ambarı ayrılmış kapasite kapsamı ve bölgesi ile eşleşen SQL Veri Ambarı örneklerine otomatik olarak bir rezervasyon indirimi uygulanır. [Azure portalı](https://portal.azure.com/), PowerShell, CLI ile veya API aracılığıyla SQL Veri Ambarı ayrılmış kapasitesinin kapsamını güncelleştirebilirsiniz.

## <a name="need-help-contact-us"></a>Yardıma mı ihtiyacınız var? Bizimle iletişim kurun

Sorularınız varsa ya da yardıma gereksinim duyuyorsanız [destek isteği oluşturun](https://portal.azure.com/).

## <a name="next-steps"></a>Sonraki adımlar

- Azure SQL Veri Ambarı’na rezervasyon indirimlerinin nasıl uygulanacağı hakkında daha fazla bilgi edinmek için bkz. [Azure SQL Veri Ambarı için rezervasyon indirimleri nasıl uygulanır?](billing-prepay-sql-data-warehouse-charges-with-reserved-capacity.md).

- Azure Ayrılmış Sanal Makine Örnekleri hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:
  - [Azure Ayrılmış Sanal Makine Örnekleri nedir?](billing-save-compute-costs-reservations.md)
  - [Azure Ayırmalarını yönetme](billing-manage-reserved-vm-instance.md)
  - [Azure Ayrılmış Sanal Makine Örnekleri indirimini anlama](billing-understand-reservation-charges.md)
  - [Kullandıkça Öde aboneliğiniz için rezervasyon kullanımını anlama](billing-understand-reserved-instance-usage.md)
  - [Kurumsal kaydınız için rezervasyon kullanımını anlama](billing-understand-reserved-instance-usage-ea.md)
