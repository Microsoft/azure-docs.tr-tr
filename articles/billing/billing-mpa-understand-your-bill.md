---
title: Microsoft İş Ortağı Sözleşmesi faturanızdaki ücretleri anlama - Azure
description: Faturanızdaki ücretleri okumayı ve anlamayı öğrenin.
author: jureid
manager: jureid
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/10/2019
ms.author: banders
ms.openlocfilehash: 579a052f8bde780ac33de85c5a08d9b3e79d3096
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72515773"
---
# <a name="understand-charges-on-your-microsoft-partner-agreement-invoice"></a>Microsoft İş Ortağı Sözleşmesi faturanızdaki ücretleri anlama

 Microsoft İş Ortağı Sözleşmesi'nin ödeme hesabında, her faturalama profili için ayda bir fatura oluşturulur. Fatura önceki aya ait tüm müşteri ücretlerini içerir. Azure portalında tek tek işlemleri analiz ederek faturanızdaki ücretleri anlayabilirsiniz. Faturalarınızı da Azure portalında görüntüleyebilirsiniz. Daha fazla bilgi için bkz. [Azure portalından faturaları indirme](billing-download-azure-invoice.md).

Bu makale, Microsoft İş Ortağı Sözleşmesi’ne ait bir ödeme hesabı için geçerlidir. [Microsoft İş Ortağı Sözleşmesi’ne erişiminizin olup olmadığını denetleyin](#check-access-to-a-microsoft-partner-agreement).

## <a name="view-transactions-for-an-invoice-in-the-azure-portal"></a>Azure portalında faturanın işlemlerini görüntüleme

1. [Azure Portal](https://www.azure.com) oturum açın.

2. *Maliyet Yönetimi + Faturalama* araması yapın.

   <!--  ![Screenshot that shows Azure portal search for cost management + billing](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png) -->

3. Sayfanın sol tarafında **Tüm işlemler**'i seçin. Erişiminize bağlı olarak, bir ödeme hesabı, faturalama profili veya müşteri seçmeniz ve sonra da **Tüm işlemler** seçeneğini belirlemeniz gerekebilir.

4. Tüm işlemler sayfasında aşağıdaki bilgiler görüntülenir:

    <!-- ![Screenshot that shows the billed transactions list](./media/billing-mpa-understand-your-bill/mpa-billing-profile-all-transactions.png) -->

    |Sütun  |Tanım  |
    |---------|---------|
    |Tarih     | İşlem tarihi  |
    |Fatura kimliği     | İşlemin faturalandığı faturanın tanımlayıcısı. Destek isteği gönderiyorsanız, destek isteğinizi hızlandırmak için Azure desteğiyle bu kimliği paylaşın |
    |İşlem türü     |  Satın alma, iptal ve kullanım ücretleri gibi işlemin türü  |
    |Ürün ailesi     | Sanal makine için işlem veya Azure SQL veritabanı için veritabanı gibi ürünün kategorisi|
    |Ürün sku'su     | Ürününüzün örneğini tanımlayan benzersiz kod |
    |Miktar     |  İşlem miktarı      |
    |Faturalama profili     | İşlem bu faturalama profili faturasında gösterilir |

5. Faturanın işlemlerini filtrelemek için fatura kimliği araması yapın.

## <a name="review-pending-charges-to-estimate-your-next-invoice"></a>Sonraki faturanızı tahmin etmek için bekleyen ücretleri gözden geçirme

Ücretler tahmin edilir ve fatura edilene kadar beklemede olarak kabul edilir. Bir sonraki faturanızı tahmin etmek için Azure portalında Microsoft İş Ortağı Sözleşmesi faturalama profilinizin beklemedeki ücretlerini görüntüleyebilirsiniz. Bekleyen ücretler tahminidir ve vergileri içermez. Sonraki faturanızın gerçek ücretleri bekleyen ücretlerden farklı olacaktır.

### <a name="view-pending-transactions"></a>Bekleyen işlemleri görüntüleme

Bekleyen ücretleri belirlediğinizde, ücretlere katkıda bulunan tek tek işlemleri analiz ederek ücretleri anlayabilirsiniz. Bu noktada, Tüm işlemler sayfasında bekleyen kullanım ücretleri görüntülenmez. Bekleyen kullanım ücretlerini Azure abonelikler sayfasında görüntüleyebilirsiniz. Daha fazla bilgi için bkz. [Bekleyen kullanım ücretlerini görüntüleme](#view-pending-usage-charges)

1. [Azure Portal](https://portal.azure.com) oturum açın.

2. *Maliyet Yönetimi + Faturalama* araması yapın.

   <!-- ![Screenshot that shows Azure portal search for cost management + billing](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png) -->

3. Faturalama profilini seçin. Erişiminize bağlı olarak, bir ödeme hesabı seçmeniz gerekebilir. Ödeme hesabında **Faturalama profilleri**'ni ve ardından bir faturalama profili seçin.

4. Sayfanın sol tarafında **Tüm işlemler**'i seçin.

5. *Bekleyen* terimi için arama yapın. Geçerli ayın veya geçen ayın bekleyen ücretlerini görüntülemek için **Zaman Aralığı** filtresini kullanın.

   <!-- ![Screenshot that shows the pending transactions list](./media/billing-mpa-understand-your-bill/mpa-billing-profile-pending-transactions.png) -->

### <a name="view-pending-charges-by-customer"></a>Müşteriye göre beklemedeki ücretleri görüntüleme

1. [Azure Portal](https://portal.azure.com) oturum açın.

2. *Maliyet Yönetimi + Faturalama* araması yapın.

3. Faturalama profilini seçin. Erişiminize bağlı olarak, bir ödeme hesabı seçmeniz gerekebilir. Ödeme hesabında **Faturalama profilleri**'ni ve ardından bir faturalama profili seçin.

4. Sayfanın sol tarafından **Müşteriler**’i seçin.

    <!-- ![screenshot of billing profile customers list](./media/billing-mpa-understand-your-bill/mpa-billing-profile-customers.png) -->

5. Müşteriler sayfasında, faturalama profili ile ilişkili her müşteri için geçerli ayın ve önceki ayın ücretleri görüntülenir. Ayın başından bugüne kadarki ücretler geçerli ay için bekleyen ücretlerdir ve aya ait fatura oluşturulduğunda faturalandırılır. Geçen ayın faturası henüz oluşturulmadıysa geçen ayın ücretleri de beklemededir.

### <a name="view-pending-usage-charges"></a>Bekleyen kullanım ücretlerini görüntüleme

1. [Azure Portal](https://portal.azure.com) oturum açın.

2. *Maliyet Yönetimi + Faturalama* araması yapın.

3. Faturalama profilini seçin. Erişiminize bağlı olarak, bir ödeme hesabı seçmeniz gerekebilir. Ödeme hesabında **Faturalama profilleri**'ni ve ardından bir faturalama profili seçin.

4. Sayfanın sol tarafından **Azure abonelikleri**'ni seçin.

5. Azure abonelikleri sayfasında, faturalama profilindeki her abonelik için geçerli ayın ve geçen ayın ücretleri görüntülenir. Ayın başından bugüne kadarki ücretler geçerli ay için bekleyen ücretlerdir ve aya ait fatura oluşturulduğunda faturalandırılır. Geçen ayın faturası henüz oluşturulmadıysa geçen ayın ücretleri de beklemededir.

<!--     ![Screenshot that shows the Azure subscriptions list for MPA billing profile](./media/billing-mpa-understand-your-bill/mpa-billing-profile-subscriptions-list.png) -->

## <a name="analyze-your-azure-usage-charges"></a>Azure kullanım ücretlerinizi analiz etme

Kullanıma dayalı ücretlerinizi analiz etmek için Azure kullanımı ve ücretleri CSV dosyasını kullanın. [Azure kullanım ve ücretleri CSV dosyanızı Azure portalından indirebilirsiniz](billing-download-azure-daily-usage.md).

Fatura PDF dosyasında listelenen her bir ürünün kullanım ücretlerinde mutabakat sağlamak için Azure kullanımı ve ücretleri dosyasına filtre uygulayabilirsiniz. Belirli bir ürünün ayrıntılı kullanım ücretlerini görüntülemek için, Azure kullanımı ve ücretleri CSV dosyasındaki **product** sütununa yalnızca söz konusu ürünün adını içerecek şekilde filtre uygulayın.

Ayrıca, Azure kullanımı ve ücretleri CSV dosyasındaki **customerName** sütununa filtre uygulayarak her müşterinizin günlük kullanım ücretlerini görüntüleyebilirsiniz. Günlük kullanım ücretlerini Azure aboneliğine göre görüntülemek istiyorsanız **subscriptionName** sütununu filtreleyin.


## <a name="pay-your-bill"></a>Faturanızı ödeme

Faturanızı ödeme yönergeleri faturanın alt kısmında gösterilir. Fatura tarihinizden sonraki 60 gün içinde banka havalesi veya çekle ödeme yapabilirsiniz.

Faturanızı zaten ödediyseniz Azure portalındaki Faturalar sayfasında ödeme durumunu denetleyebilirsiniz.

## <a name="check-access-to-a-microsoft-partner-agreement"></a>Microsoft İş Ortağı Sözleşmesi’ne erişimi denetleme
[!INCLUDE [billing-check-mpa](../../includes/billing-check-mpa.md)]

## <a name="need-help-contact-us"></a>Yardıma mı ihtiyacınız var? Bize ulaşın.

Sorularınız varsa ya da yardıma gereksinim duyuyorsanız [destek isteği oluşturun](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Sonraki adımlar

Faturanız ve ayrıntılı kullanım hakkında daha fazla bilgi edinmek için bkz:
