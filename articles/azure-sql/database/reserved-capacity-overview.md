---
title: İşlem maliyetlerini ayrılmış kapasiteye göre Kaydet
titleSuffix: Azure SQL Database & SQL Managed Instance
description: İşlem maliyetlerinizi kaydetmek için Azure SQL veritabanı ve SQL yönetilen örnek ayrılmış kapasitesi satın almayı öğrenin.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein
ms.date: 10/13/2020
ms.openlocfilehash: cacd43502a01352c24f8fcfd85b12aac781dccbd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98602519"
---
# <a name="save-costs-for-resources-with-reserved-capacity---azure-sql-database--sql-managed-instance"></a>Ayrılmış kapasiteye sahip kaynakların maliyetlerini kaydetme-Azure SQL veritabanı & SQL yönetilen örneği
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)] 

Kullandıkça Öde fiyatlarıyla karşılaştırıldığında işlem kaynakları için bir ayırmaya girerek Azure SQL veritabanı ve SQL yönetilen örneği ile tasarruf edin. Ayrılmış kapasite ile, işlem maliyetlerine göre önemli bir indirim elde etmek için SQL veritabanı ve/veya SQL yönetilen örnek kullanımı için bir veya üç yıl boyunca bir taahhüt yaparsınız. Ayrılmış kapasiteyi satın almak için Azure bölgesini, dağıtım türünü, performans katmanını ve terimi belirtmeniz gerekir.

Ayırmayı belirli bir veritabanına veya yönetilen örneğe atamanız gerekmez. Zaten çalışmakta olan veya yeni dağıtılan mevcut dağıtımların eşleşmesi, avantajı otomatik olarak alır. Bir rezervasyon satın alarak, bir veya üç yıllık bir dönem için işlem maliyetlerine yönelik kullanıma de kayıt yapılır. Bir rezervasyon satın alarak, rezervasyon öznitelikleriyle eşleşen işlem ücretleri artık Kullandıkça Öde tarifelerine göre ücretlendirilir. 

Bir ayırma, hem birincil hem de faturalandırılabilir ikincil işlem çoğaltmaları için geçerlidir, ancak hizmetle ilişkili yazılım, ağ veya depolama ücretlerini kapsamaz. Rezervasyon döneminin sonunda, faturalandırma avantajı sona erer ve veritabanı ya da yönetilen örnek Kullandıkça Öde fiyatı üzerinden faturalandırılır. Ayırmalar otomatik olarak yenilemez. Fiyatlandırma bilgileri için bkz. [ayrılmış kapasite teklifi](https://azure.microsoft.com/pricing/details/sql-database/managed/).

Ayrılmış kapasiteyi [Azure Portal](https://portal.azure.com)satın alabilirsiniz. [Peşin olarak veya aylık ödemelerle](../../cost-management-billing/reservations/prepare-buy-reservation.md) rezervasyon ödemesi yapın. Ayrılmış kapasite satın almak için:

- Kullandıkça Öde tarifesine sahip en az bir kuruluş veya ayrı bir abonelik için sahip rolünde olmanız gerekir.
- Kurumsal abonelikler için, [EA portal](https://ea.azure.com)’da **Ayrılmış Örnek Ekle** seçeneği etkinleştirilmelidir. Ya da bu ayar devre dışı bırakılırsa, abonelikte bir EA yöneticisi olmanız gerekir. Ayrılmış kapasite.

Kurumsal müşterilerin ve kullandıkça öde müşterilerinin rezervasyon satın alma işlemleri için nasıl ücretlendirilildiği hakkında daha fazla bilgi için bkz. [Kurumsal kaydınız Için Azure ayırma kullanımını anlama](../../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) ve [Kullandıkça Öde aboneliğiniz için Azure rezervasyon kullanımını anlama](../../cost-management-billing/reservations/understand-reserved-instance-usage.md).

> [!NOTE]
> Satın alma ayrılmış kapasitesi, kullanımınız için belirli altyapı kaynaklarını (sanal makineler veya düğümler) önceden ayırmaz veya ayırmaz.

## <a name="determine-correct-size-before-purchase"></a>Satın almadan önce doğru boyutu belirleme

Ayırma boyutu, var olan veya hemen dağıtılmış bir veritabanı ya da belirli bir bölgedeki yönetilen örnek tarafından kullanılan ve aynı performans katmanını ve donanım üretimini kullanan toplam işlem miktarına bağlı olmalıdır.

Örneğin, bir genel amaç, 5. nesil – 16 sanal çekirdek elastik havuzu ve iki iş açısından kritik 5. nesil – 4 sanal çekirdek tek veritabanı çalıştırdığınızı varsayalım. Ayrıca, bir sonraki ayda, ek bir genel amaç 5. nesil – 16 sanal çekirdek elastik havuzu ve bir iş kritik 5. nesil – 32 sanal çekirdek elastik havuzu dahilinde dağıtmayı planladığınızı varsayalım. Ayrıca, bu kaynaklara en az 1 yıl boyunca ihtiyacınız olacağını bildiğinizi varsayalım. Bu durumda, tek veritabanı/elastik havuz genel amaçlı-5. nesil için bir 32 (2x16) Vçekirdekler 1 yıllık ayırma ve tek veritabanı/esnek havuz iş açısından kritik-5. nesil için bir 40 (2x4 + 32) sanal çekirdek 1 yıllık ayırma satın almalısınız.

## <a name="buy-reserved-capacity"></a>Ayrılmış kapasite satın alın

1. [Azure Portal](https://portal.azure.com) oturum açın.
2. **Tüm hizmetler** > **Rezervasyonlar**’ı seçin.
3. **Ekle** ' yi seçin ve ardından **satın alma rezervasyonları** **bölmesinde SQL veritabanı ' nı seçerek SQL** veritabanı için yeni bir ayırma satın alın.
4. Gerekli alanları girin. SQL veritabanı 'ndaki mevcut veritabanları ve SQL yönetilen örneği ile eşleşen ve seçtiğiniz özniteliklerle eşleşen, ayrılmış kapasite indirimi elde etmek için uygun olanı seçin. Gerçek veritabanı sayısı veya indirimi alan yönetilen örnekler, seçilen kapsama ve miktara göre değişir.

    ![Ayrılmış kapasite satın almayı göndermeden önce ekran görüntüsü](./media/reserved-capacity-overview/sql-reserved-vcores-purchase.png)

    Aşağıdaki tablo gerekli alanları açıklar.
    
    | Alan      | Açıklama|
    |------------|--------------|
    |Abonelik|Kapasite rezervasyonu için ödeme yapmak üzere kullanılan abonelik. Abonelikteki ödeme yöntemi, rezervasyonun ön maliyetlerine göre ücretlendirilir. Abonelik türü bir kurumsal anlaşma (teklif numarası MS-AZR-0017P veya MS-AZR-0148P) veya kullandıkça öde fiyatlandırması (teklif numarası MS-AZR-0003P veya MS-AZR-0023P) içeren tek bir anlaşma olmalıdır. Kurumsal abonelik için ücretler kaydın Azure Ön Ödemesinden (eski adıyla parasal taahhüt) düşülür ve fazla kullanım olarak ücretlendirilir. Kullandıkça Öde fiyatlandırmasına sahip bireysel bir abonelik için ücretler, abonelik üzerindeki kredi kartına veya fatura ödeme yöntemine faturalandırılır.|
    |Kapsam       |VCore rezervasyonunun kapsamı bir aboneliği veya birden çok aboneliği (paylaşılan kapsamı) kapsayabilir. Eğer  <br/><br/>**, Sanal** çekirdek ayırma indirimi, faturalama bağlamınızın içindeki herhangi bir aboneliklerde çalışan veritabanına veya yönetilen örneğe uygulanır. Kurumsal müşteriler için, paylaşılan kapsam kayıt içindedir ve kayıt dahilindeki tüm abonelikleri içerir. Kullandıkça Öde müşterileri için paylaşılan kapsam, hesap yöneticisi tarafından oluşturulan tüm Kullandıkça Öde abonelikleridir.<br/><br/>**Tek abonelik**, sanal çekirdek ayırma indirimi Bu abonelikteki veritabanlarına veya yönetilen örneklere uygulanır. <br/><br/>**Tek kaynak grubu**, rezervasyon indirimi, seçilen abonelikteki veritabanlarının örneklerine veya yönetilen örneklere ve bu abonelikte seçilen kaynak grubuna uygulanır.|
    |Region      |Kapasite rezervasyonu kapsamındaki Azure bölgesi.|
    |Dağıtım türü|Ayırmasını satın almak istediğiniz SQL kaynak türü.|
    |Performans katmanı|Veritabanları veya yönetilen örnekler için hizmet katmanı. |
    |Süre        |Bir yıl veya üç yıl.|
    |Miktar    |Kapasite ayırma içinde satın alınan işlem kaynakları miktarı. Bu miktar, seçili Azure bölgesindeki ve performans katmanındaki, ayrılan ve fatura iskontosunu alacak olan sanal çekirdekler sayısıdır. Örneğin, Doğu ABD bölgesinde 5. nesil 16 sanal çekirdeklerin toplam işlem kapasitesine sahip birden çok veritabanını çalıştırmayı planlıyorsanız veya planlıyorsanız, tüm veritabanlarının avantajını en üst düzeye çıkarmak için miktarı 16 olarak belirtirsiniz. |

1. **Maliyetler** bölümündeki kapasite rezervasyonunun maliyetini gözden geçirin.
1. **Satın al**'ı seçin.
1. Satın alımınızın durumunu görmek için **Bu ayırmayı görüntüle** ' yi seçin.

## <a name="cancel-exchange-or-refund-reservations"></a>Rezervasyonları iptal etme, değiştirme veya para iadesi alma

Belirli sınırlamalarla rezervasyonları iptal edebilir, değiştirebilir veya para iadesi alabilirsiniz. Daha fazla bilgi için bkz. [Azure Ayrılmış Sanal Makine Örnekleri için self servis değişimler ve para iadeleri](../../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="vcore-size-flexibility"></a>vCore boyutu esnekliği

vCore boyutu esnekliği, ayrılan kapasite avantajını kaybetmeden bir performans katmanı ve bölgesi içinde ölçeği büyütme veya küçültme olanakları sağlar. Ayrılmış kapasite Ayrıca, etkin veritabanlarınızı, esnek havuzlarınızın (aynı bölge ve performans katmanı içinde), ayrılan kapasite avantajını kaybetmeden normal işlemlerinizin bir parçası olarak geçici olarak ve dışarı taşıma esnekliği de sağlar. Ayırdığınızda bir uygulanmamış arabelleği tutarak, bütçenize aşmadan performans artışlarını etkin bir şekilde yönetebilirsiniz.

## <a name="limitation"></a>Sınırlama

SQL veritabanında DTU tabanlı (temel, standart veya Premium) veritabanları ayıramazsınız.

## <a name="need-help-contact-us"></a>Yardıma mı ihtiyacınız var? Bizimle iletişim kurun

Sorularınız varsa ya da yardıma gereksinim duyuyorsanız [destek isteği oluşturun](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Sonraki adımlar

VCore ayırma indirimi, kapasite ayırma kapsamı ve öznitelikleriyle eşleşen veritabanı sayısına veya yönetilen örneklere otomatik olarak uygulanır. Kapasite ayırmasının kapsamını [Azure Portal](https://portal.azure.com), PowerShell, Azure CLı veya API aracılığıyla güncelleştirebilirsiniz.

Kapasite ayırmayı yönetme hakkında bilgi edinmek için bkz. [ayrılmış kapasiteyi yönetme](../../cost-management-billing/reservations/manage-reserved-vm-instance.md).

Azure Ayrılmış Sanal Makine Örnekleri hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

- [Azure Ayrılmış Sanal Makine Örnekleri nedir?](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Azure Ayırmalarını yönetme](../../cost-management-billing/reservations/manage-reserved-vm-instance.md)
- [Azure Ayrılmış Sanal Makine Örnekleri indirimini anlama](../../cost-management-billing/reservations/understand-reservation-charges.md)
- [Kullandıkça Öde aboneliğiniz için rezervasyon kullanımını anlama](../../cost-management-billing/reservations/understand-reserved-instance-usage.md)
- [Kurumsal kaydınız için rezervasyon kullanımını anlama](../../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
- [İş Ortağı Merkezi Bulut Çözümü Sağlayıcısı (CSP) programındaki Azure Ayrılmış Sanal Makine Örnekleri](/partner-center/azure-reservations)