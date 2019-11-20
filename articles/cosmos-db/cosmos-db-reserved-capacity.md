---
title: Ayrılmış kapasiteye sahip Azure Cosmos DB kaynaklarının maliyetini iyileştirin
description: İşlem maliyetlerinizi kaydetmek için Azure Cosmos DB ayrılmış kapasiteyi satın almayı öğrenin.
author: bandersmsft
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: banders
ms.reviewer: sngun
ms.openlocfilehash: 0ee43fe0996c05f4e59f6107ba52fac19b83cdef
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72756947"
---
# <a name="optimize-cost-with-reserved-capacity-in-azure-cosmos-db"></a>Azure Cosmos DB ' de ayrılmış kapasiteyle maliyeti iyileştirin

Azure Cosmos DB ayrılmış kapasite, bir yıl veya üç yıl boyunca Azure Cosmos DB kaynakları için bir ayırmaya ayırarak paradan tasarruf etmenize yardımcı olur. Azure Cosmos DB ayrılmış kapasiteyle, Cosmos DB kaynaklar için sağlanan aktarım hızına ilişkin bir indirim elde edebilirsiniz. Kaynak örnekleri veritabanları ve kapsayıcılardır (tablolar, koleksiyonlar ve grafikler).

Azure Cosmos DB ayrılmış kapasite, bir yıllık veya üç yıllık ön taahhüt ile normal fiyatlarla 65 &mdash;up Cosmos DB maliyetlerinizi önemli ölçüde azaltabilir. Ayrılmış kapasite bir faturalandırma indirimi sağlar ve Azure Cosmos DB kaynaklarınızın çalışma zamanı durumunu etkilemez.

Azure Cosmos DB ayrılmış kapasite, kaynaklarınız için sağlanan aktarım hızını içerir. Depolama ve ağ ücretlerini kapsamaz. Bir rezervasyon satın alarak, rezervasyon öznitelikleriyle eşleşen aktarım hızı ücretleri artık Kullandıkça Öde tarifelerine göre ücretlendirilir. Ayırmalar hakkında daha fazla bilgi için bkz. [Azure ayırmaları](../billing/billing-save-compute-costs-reservations.md) makalesi.

[Azure portal](https://portal.azure.com)Azure Cosmos DB ayrılmış kapasiteyi satın alabilirsiniz. [Peşin olarak veya aylık ödemelerle](../billing/billing-monthly-payments-reservations.md) rezervasyon ödemesi yapın. Ayrılmış kapasite satın almak için:

* Kullandıkça Öde tarifesine sahip en az bir kuruluş veya ayrı bir abonelik için sahip rolünde olmanız gerekir.  
* Kurumsal abonelikler için, [EA portal](https://ea.azure.com)’da **Ayrılmış Örnek Ekle** seçeneği etkinleştirilmelidir. Ya da bu ayar devre dışı bırakılırsa, abonelikte bir EA yöneticisi olmanız gerekir.
* Bulut çözümü sağlayıcısı (CSP) programı için, yalnızca yönetici aracıları veya satış aracıları Azure Cosmos DB ayrılmış kapasiteyi satın alabilir.

## <a name="determine-the-required-throughput-before-purchase"></a>Satın almadan önce gereken aktarım hızını belirleme

Ayırma boyutu, mevcut veya hemen dağıtılmış Azure Cosmos DB kaynakların kullanacağı toplam aktarım hızı miktarına bağlı olmalıdır. Gerekli aktarım hızını aşağıdaki yollarla belirleyebilirsiniz:

* Tüm bölgelerde Azure Cosmos DB hesaplarınız, veritabanları ve Koleksiyonlarınızda sağlanan toplam verimlilik için geçmiş verileri alın. Örneğin, günlük kullanım deyiminizi `https://account.azure.com` 'ten indirerek günlük ortalama sağlanan aktarım hızını değerlendirebilirsiniz.

* Bir Kurumsal Anlaşma (EA) müşterisiyseniz, Azure Cosmos DB üretilen iş ayrıntılarını almak için kullanım dosyanızı indirebilirsiniz. Kullanım dosyasının **ek bilgi** bölümündeki **hizmet türü** değerine bakın.

* Azure Cosmos DB hesaplarınızdaki tüm iş yükleri için, bir sonraki bir veya üç yılda çalıştırmayı düşündüğünüz ortalama aktarım hızını belirleyebilirsiniz. Daha sonra bu miktarı rezervasyon için kullanabilirsiniz.

## <a name="buy-azure-cosmos-db-reserved-capacity"></a>Ayrılmış Azure Cosmos DB kapasitesi satın alın

1. [Azure Portal](https://portal.azure.com)’ında oturum açın.  

2. **Ekle** >  **tüm hizmetler**  > **ayırmalar** ' ı seçin.  

3. **Satın alma rezervasyonları** bölmesinden yeni bir ayırma satın almak için **Azure Cosmos DB** seçin.  

4. Gerekli alanları aşağıdaki tabloda açıklandığı gibi girin:

   ![Ayrılmış kapasite formunu doldur](./media/cosmos-db-reserved-capacity/fill-reserved-capacity-form.png)

   |Alan  |Açıklama  |
   |---------|---------|
   |Kapsam   |   Ayırma ile ilişkili fatura avantajını kaç tane aboneliğin kullanabileceğinizi denetleyen seçenektir. Ayrıca, rezervasyonun belirli aboneliklere nasıl uygulanacağını de denetler. <br/><br/>  **Paylaşılan**' i seçerseniz, rezervasyon indirimi faturalandırma bağlamınızın içindeki herhangi bir abonelikte çalışan Azure Cosmos DB örneklerine uygulanır. Faturalandırma bağlamı, Azure 'a kaydolmanızı temel alır. Kurumsal müşteriler için, paylaşılan kapsam kayıt içindedir ve kayıt dahilindeki tüm abonelikleri içerir. Kullandıkça Öde müşterileri için, paylaşılan kapsam, hesap yöneticisi tarafından oluşturulan Kullandıkça Öde tarifesine sahip bireysel aboneliklerdir.  <br/><br/>  **Tek abonelik**' ı seçerseniz, rezervasyon indirimi seçili abonelikteki Azure Cosmos DB örneklerine uygulanır. <br/><br/> **Tek kaynak grubu**' nu seçerseniz, rezervasyon indirimi seçili abonelikteki Azure Cosmos DB örneklerine ve bu abonelikte seçilen kaynak grubuna uygulanır. <br/><br/> Ayrılan kapasiteyi satın aldıktan sonra rezervasyon kapsamını değiştirebilirsiniz.  |
   |Abonelik  |   Azure Cosmos DB ayrılmış kapasite için ödeme yapmak üzere kullanılan abonelik. Seçili abonelikteki ödeme yöntemi, maliyetleri borçlandırmak üzere kullanılır. Abonelik aşağıdaki türlerden biri olmalıdır: <br/><br/>  Kurumsal Anlaşma (teklif numaraları: MS-AZR-0017P veya MS-AZR-0148P): kurumsal bir abonelik Için ücretler, kayıt parasal taahhüt bakiyesinden düşülür veya fazla kullanım olarak ücretlendirilir. <br/><br/> Kullandıkça Öde tarifesine sahip tek tek abonelik (teklif numaraları: MS-AZR-0003P veya MS-AZR-0023P): Kullandıkça Öde tarifesine sahip bireysel bir abonelik Için ücretler, abonelik üzerindeki kredi kartına veya fatura ödeme yöntemine faturalandırılır.    |
   | Kaynak Grubu | Ayrılmış kapasite iskontosunun uygulandığı kaynak grubu. |
   |Sözleşme Dönemi  |   Bir yıl veya üç yıl.   |
   |Aktarım hızı türü   |  Aktarım hızı, istek birimleri olarak sağlanır. Her iki kurulum-tek bölge yazma işlemlerinin yanı sıra birden çok bölgeye yazma için sağlanan aktarım hızı için bir rezervasyon satın alabilirsiniz. Verimlilik türü arasından seçim yapabileceğiniz iki değer vardır: Saat başına 100 RU/sn ve saat başına 100 Multi-Master RU/s.|
   | Ayrılmış Kapasite birimleri| Ayırmak istediğiniz üretilen iş miktarı. Bölge başına tüm Cosmos DB kaynaklarınız (örneğin, veritabanları veya kapsayıcılar) için gereken aktarım hızını belirleyerek bu değeri hesaplayabilirsiniz. Daha sonra Cosmos veritabanınızla ilişkilendirdiğiniz bölge sayısına göre çarpmanız gerekir. Örneğin: her bölgede 1.000.000 RU/sn ile beş bölge varsa, rezervasyon kapasitesi satın alma için 5.000.000 RU/sn seçeneğini belirleyin. |


5. Formu doldurduktan sonra, ayrılan kapasiteyi satın almak için gereken fiyat hesaplanır. Çıktı Ayrıca, seçilen seçeneklerle aldığınız indirimin yüzdesini gösterir. Sonra **Seç** ' e tıklayın

6. **Satın alma rezervasyonları** bölmesinde, rezervasyonun iskontosunu ve fiyatını gözden geçirin. Bu rezervasyon fiyatı, tüm bölgelerde sağlanan aktarım hızına sahip Azure Cosmos DB kaynakları için geçerlidir.  

   ![Ayrılmış kapasite Özeti](./media/cosmos-db-reserved-capacity/reserved-capacity-summary.png)

7. **Gözden geçir + satın al** ' ı seçin ve **Şimdi satın alın**. Satın alma işlemi başarılı olduğunda aşağıdaki sayfayı görürsünüz:

Bir ayırma satın aldıktan sonra, rezervasyon koşullarına uyan mevcut Azure Cosmos DB kaynaklarına hemen uygulanır. Mevcut Azure Cosmos DB kaynağınız yoksa, rezervasyon koşullarına uyan yeni bir Cosmos DB örneği dağıttığınızda rezervasyon uygulanır. Her iki durumda da rezervasyon dönemi, başarılı bir satın alma işleminden hemen sonra başlar.

Rezervasyonunuzun süresi dolmuşsa Azure Cosmos DB örneklerinizin çalıştırılması devam eder ve normal Kullandıkça Öde tarifesine göre faturalandırılır.

## <a name="cancel-exchange-or-refund-reservations"></a>Rezervasyonları iptal etme, değiştirme veya para iadesi alma

Doğru ayrılmış kapasiteyi tanımlamaya yönelik yardım için bkz. [rezervasyon iskontosunun Azure Cosmos DB nasıl uygulanacağını anlayın](../billing/billing-understand-cosmosdb-reservation-charges.md).

Belirli sınırlamalarla rezervasyonları iptal edebilir, değiştirebilir veya para iadesi alabilirsiniz. Daha fazla bilgi için bkz. [Azure Ayrılmış Sanal Makine Örnekleri için self servis değişimler ve para iadeleri](../billing/billing-azure-reservations-self-service-exchange-and-refund.md).

## <a name="next-steps"></a>Sonraki adımlar

Rezervasyon indirimi, ayırma kapsamı ve öznitelikleriyle eşleşen Azure Cosmos DB kaynaklarına otomatik olarak uygulanır. Azure portal, PowerShell, Azure CLı veya API aracılığıyla rezervasyon kapsamını güncelleştirebilirsiniz.

*  Ayrılmış kapasite iskontolarının Azure Cosmos DB nasıl uygulanacağını öğrenmek için bkz. [Azure rezervasyon ıskontosunu anlama](../billing/billing-understand-cosmosdb-reservation-charges.md).

* Azure rezervasyonları hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

   * [Azure ayırmaları nelerdir?](../billing/billing-save-compute-costs-reservations.md)  
   * [Azure ayırmalarını yönetme](../billing/billing-manage-reserved-vm-instance.md)  
   * [Kurumsal kaydınız için rezervasyon kullanımını anlama](../billing/billing-understand-reserved-instance-usage-ea.md)  
   * [Kullandıkça Öde aboneliğiniz için rezervasyon kullanımını anlama](../billing/billing-understand-reserved-instance-usage.md)
   * [Iş Ortağı Merkezi CSP programında Azure ayırmaları](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Yardım mı gerekiyor? Bizimle iletişim kurun.

Sorularınız varsa ya da yardıma gereksinim duyuyorsanız [destek isteği oluşturun](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
