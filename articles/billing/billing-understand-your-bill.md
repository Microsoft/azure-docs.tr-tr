---
title: Azure faturanızı anlama
description: Azure aboneliğinizin kullanımını ve faturasını okuma ve anlama hakkında bilgi edinin.
author: bandersmsft
manager: dougeby
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: ceeacf216b1d3258aa73d86c613d28e00e9480bb
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71718625"
---
# <a name="understand-your-microsoft-azure-bill"></a>Microsoft Azure faturanızı anlama
Azure faturalarınızı anlamak için Azure portalında faturanızı ayrıntılı günlük kullanım dosyası ve maliyet yönetimi raporlarıyla karşılaştırabilirsiniz.

Bu makale aşağıdaki müşterilere yönelik değildir:
- Kurumsal Anlaşması olan Azure müşterileri (EA müşterileri). EA müşterisiyseniz bkz.[Kurumsal Anlaşma sahibi Azure müşterileri için faturaları anlama](billing-understand-your-bill-ea.md).
- [Microsoft Müşteri Sözleşmesi](#check-access-to-a-microsoft-customer-agreement) olan Azure müşterileri. Microsoft Müşteri Sözleşmeniz varsa bkz. [Microsoft Müşteri Sözleşmesi faturanızdaki Azure ücretlerini anlama](billing-mca-understand-your-bill.md).

Fatura dönemi, fiyatlandırma ve kullanım dahil olmak üzere Azure Bulut Çözüm Sağlayıcısı (Azure CSP) programında faturalama işlemlerinin açıklaması için bkz. [Azure CSP Faturalarına Genel Bakış](/azure/cloud-solution-provider/billing/azure-csp-billing-overview/).

## <a name="charges"></a>Ücretlerinizi gözden geçirme

>[!VIDEO https://www.youtube.com/embed/3YegFD769Pk]

Faturanızda daha fazla bilgi edinmek istediğiniz bir ücretlendirme varsa kullanım dosyasıyla veya Azure portalı ile kullanım ve maliyetleri karşılaştırabilirsiniz.

### <a name="option-1-compare-usage-and-costs-with-usage-file"></a>1\. Seçenek: Kullanım dosyasıyla kullanımı ve maliyetleri karşılaştırma

Ayrıntılı kullanım CSV dosyası faturalama dönemine ve günlük kullanıma göre ücretlerinizi gösterir. Dosyayı indirmek veya görüntülemek için bkz. [Azure faturanızı ve günlük kullanım verilerinizi alma](billing-download-azure-invoice-daily-usage-date.md).

Kullanım ücretleriniz ölçüm düzeyinde görüntülenir. Aşağıdaki terimler hem faturada hem de ayrıntılı kullanım dosyasında aynı anlama gelir. Örneğin faturadaki faturalama dönemi, ayrıntılı kullanım dosyasındaki faturalama dönemiyle aynıdır.

 | Fatura (PDF) | Ayrıntılı kullanım (CSV)|
 | --- | --- |
|Fatura döngüsü | Fatura Dönemi |
 |Adı |Ölçüm Kategorisi |
 |Tür |Ölçüm Alt Kategorisi |
 |Kaynak |Ölçüm Adı |
 |Bölge |Ölçüm Bölgesi |
 |Kullanılan |Kullanılan Miktar |
 |Dahil |Dahil Edilen Miktar |
 |Faturalanabilir |Kapasite Aşım Miktarı |

Faturanızın **Kullanım Ücretleri** bölümü, faturalama döneminiz boyunca kullanılan her ölçümün toplam değerini gösterir. Örneğin, aşağıdaki resimde Azure Scheduler hizmetinin kullanım ücreti gösterilir.

![Fatura kullanım ücretleri](./media/billing-understand-your-bill/1.png)

Ayrıntılı kullanım CSV dosyanızın **Hesap özeti** bölümü aynı ücreti gösterir. *Tüketilen* miktarıyla *Değer*, faturayla eşleşir.

![CSV kullanım ücretleri](./media/billing-understand-your-bill/2.png)

Ücretin günlük dökümünü görmek için CSV dosyasının **Günlük Kullanım** bölümüne gidin. *Ölçüm Kategorisi*’nin altında *Scheduler* terimini filtreleyin. Ölçümün hangi günlerde kullanıldığını ve ne kadarının tüketildiğini görebilirsiniz. *Kaynak* ve *Kaynak grubu* bilgileri de karşılaştırma için gösterilir. *Tüketilen* değerler toplandığında faturada gösterilen değerlerle eşleşmelidir.

![CSV dosyasının Günlük Kullanım bölümü](./media/billing-understand-your-bill/3.png)

**Deyim** bölümündeki *Tüketilen* miktarları *Fiyat* ile çarparak günlük maliyeti bulabilirsiniz.

Daha fazla bilgi için bkz:

- [Azure faturanızı anlama](billing-understand-your-invoice.md)
- [Azure ayrıntılı kullanımınızı anlama](billing-understand-your-invoice.md)

### <a name="option-2-compare-the-usage-and-costs-in-the-azure-portal"></a>2\. Seçenek: Azure portalında kullanımı ve maliyetleri karşılaştırma

Azure portalı ücretlerinizi doğrulamanıza da yardımcı olabilir. Faturalanmış kullanımınıza ve ücretlerinize hızlı bir genel bakış için maliyet yönetimi grafiklerini görüntüleyin.

1. Azure portalında [Abonelikler](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) sayfasına gidin.
1. Aboneliğinizi > **Maliyet analizi**'ni seçin.
1. **Zaman aralığı**’na göre filtreleyin.
1. Önceki örnekten devam edersek, Azure Scheduler hizmeti için bir kullanım ücreti görürsünüz.

   ![Azure portalında maliyet analizi görünümü](./media/billing-understand-your-bill/4.png)

1. Günlük maliyet dökümünü görmek için ücreti gösteren satırı seçin.

   ![Azure portalında maliyet geçmişi görünümü](./media/billing-understand-your-bill/5.png)

Daha fazla bilgi edinmek için bkz. [Azure faturasında oluşan beklenmeyen maliyetleri önleme ve maliyet yönetimi](billing-getting-started.md#costs).

## <a name="external"></a>Dış hizmetler ayrı faturalanır

Dış hizmetler veya market ücretleri, üçüncü taraf yazılımı satıcıları tarafından oluşturulan kaynaklar için geçerlidir. Bu kaynakları Azure Market’ten sağlayabilirsiniz. Örneğin Barracuda Güvenlik Duvarı, üçüncü tarafın kullanıma sunduğu bir Azure Market kaynağıdır. Güvenlik duvarının tüm ücretleri ve bunlara karşılık gelen ölçümler dış hizmet ücretleri olarak görünür.

Dış hizmetlerin ücretleri ayrı faturalanır. Ücretler Azure faturanızda gösterilmez. Daha fazla bilgi edinmek için bkz. [Azure dış hizmet ücretlerinizi anlama](billing-understand-your-azure-marketplace-charges.md).

## <a name="resources-billed-by-usage-meters"></a>Kullanım ölçümleri tarafından faturalanan kaynaklar

Azure doğrudan kaynak maliyetine göre faturalama yapmaz. Kaynağın ücretleri bir veya birden fazla ölçüm kullanılarak hesaplanır. Ölçümler, kaynağın yaşam süresi boyunca tüketilen kaynak kullanımını izlemek için kullanılır. Bu ölçümler daha sonra faturayı hesaplamak için kullanılır.

Örneğin, sanal makine gibi tek bir Azure kaynağı oluşturduğunuzda bu kaynağın bir veya birden fazla ölçüm örneği oluşturulur. Ölçümler, kaynağın zaman içinde kullanımını izlemek için kullanılır. Her ölçüm, Azure tarafından faturayı hesaplamak için kullanılan kullanım kayıtlarını üretir.

Örneğin, Azure’da oluşturulan tek bir sanal makinenin (VM) kullanımını izlemek için aşağıdaki ölçümler oluşturulmuş olabilir:

- İşlem Saatleri
- IP Adresi Saatleri
- Gelen Veri Aktarımı
- Giden Veri Aktarımı
- Standart Yönetilen Diskler
- Standart Yönetilen Disk İşlemleri
- Standart GÇ-Disk
- Standart GÇ-Blok Blobu Okuma
- Standart GÇ-Blok Blobu Yazma
- Standart GÇ-Blok Blobu Silme

VM oluşturulduğunda her ölçüm kullanım kayıtları üretmeye başlar. Bu kullanım ve ölçümün fiyatı Azure ölçüm sisteminde izlenir.

## <a name="payment"></a>Faturanızı ödeme

Ödeme yönteminiz olarak bir kredi kartı ayarladıysanız ödeme faturalama döneminin bitişinden sonraki 10 gün içinde otomatik olarak ücretlendirilir. Kredi kartı hesap özetinizde satır öğesi **MSFT Azure** olarak görünür.

Ücretlendirilen kredi kartını değiştirmek için bkz. [Azure’da kredi kartı ekleme, güncelleştirme veya kaldırma](billing-how-to-change-credit-card.md).

[Fatura ile ödemeyi](billing-how-to-pay-by-invoice.md) seçerseniz, ödemenizi faturanızın en altında bulunan konuma gönderin.

Ödemenizin durumunu denetlemek için [bir destek bileti oluşturun](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).


## <a name="tips-for-cost-management"></a>Maliyet yönetimi ipuçları

- Şunları kullanarak maliyetleri tahmin edin:
  - [Azure fiyatlandırma hesaplayıcısı](https://azure.microsoft.com/pricing/calculator/)
  - [Toplam sahip olma maliyeti hesaplayıcısı](https://aka.ms/azure-tco-calculator)
  - [Her hizmetin ayrıntılı fiyatlandırma bilgileri](https://azure.microsoft.com/pricing/)
- [Azure portalında kullanımınızı ve maliyetlerinizi düzenli olarak gözden geçirin](billing-getting-started.md#costs)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft Müşteri Sözleşmesi’ne erişimi denetleme
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Yardıma mı ihtiyacınız var? Bize ulaşın.

Sorularınız varsa ya da yardıma gereksinim duyuyorsanız [destek isteği oluşturun](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="learn-more"></a>Daha fazla bilgi edinin

- [Azure faturanızı ve günlük kullanım verilerinizi edinme](billing-download-azure-invoice-daily-usage-date.md)
- [Microsoft Azure faturanızdaki terimleri anlayın](billing-understand-your-invoice.md)
- [Ayrıntılı Microsoft Azure kullanım bilgilerinizdeki terimleri anlama](billing-understand-your-usage.md)
- [Azure portalı maliyet yönetimi](https://docs.microsoft.com/azure/billing/billing-getting-started)
- [Azure faturasında oluşan beklenmeyen maliyetleri önleme ve maliyet yönetimi](billing-getting-started.md#costs)
