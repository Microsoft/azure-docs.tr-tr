---
title: Microsoft Müşteri Sözleşmesi faturanızdaki ücretleri anlama - Azure
description: Faturanızdaki ücretleri okumayı ve anlamayı öğrenin.
author: jureid
manager: jureid
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 8ab6cf061531a1ef3c72b2f36d25932c7498d291
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71345276"
---
# <a name="understand-charges-on-your-microsoft-customer-agreement-invoice"></a>Microsoft Müşteri Sözleşmesi faturanızdaki ücretleri anlama

Tek tek işlemleri analiz ederek faturanızdaki ücretleri anlayabilirsiniz. Microsoft Müşteri Sözleşmesi'nin ödeme hesabında, her faturalama profili için ayda bir fatura oluşturulur. Fatura önceki aya ait tüm ücretleri içerir. Faturalarınızı Azure portalında görüntüleyebilirsiniz. Daha fazla bilgi için bkz. [Microsoft Müşteri Sözleşmesi için faturaları indirme](billing-download-azure-invoice-daily-usage-date.md#download-invoices-for-a-microsoft-customer-agreement).

Bu makale, Microsoft Müşteri Sözleşmesi’ne ilişkin ödeme hesabı için geçerlidir. [Microsoft Müşteri Sözleşmesi’ne erişiminiz olup olmadığını denetleyin](#check-access-to-a-microsoft-customer-agreement).

## <a name="view-transactions-for-an-invoice-in-the-azure-portal"></a>Azure portalında faturanın işlemlerini görüntüleme

1. [Azure Portal](https://www.azure.com) oturum açın.

2. **Maliyet Yönetimi + Faturalama** araması yapın.

    ![Azure portalında maliyet yönetimi + faturalama aramasını gösteren ekran görüntüsü](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. Sayfanın sol tarafında **Tüm işlemler**'i seçin. Erişiminize bağlı olarak, bir ödeme hesabı, faturalama profili veya fatura bölümü seçmeniz ve sonra da **Tüm işlemler** seçeneğini belirtmeniz gerekebilir.

4. Tüm işlemler sayfasında aşağıdaki bilgiler görüntülenir:

    ![Faturalanan işlemlerin listesini gösteren ekran görüntüsü](./media/billing-understand-your-bill-mca/mca-billed-transactions-list.png)

    |Sütun  |Tanım  |
    |---------|---------|
    |Tarih     | İşlem tarihi  |
    |Fatura kimliği     | İşlemin faturalandığı faturanın tanımlayıcısı. Destek isteği gönderiyorsanız, destek isteğinizi hızlandırmak için Azure desteğiyle bu kimliği paylaşın |
    |İşlem türü     |  Satın alma, iptal ve kullanım ücretleri gibi işlemin türü  |
    |Ürün ailesi     | Sanal makine için işlem veya Azure SQL veritabanı için veritabanı gibi ürünün kategorisi|
    |Ürün sku'su     | Ürününüzün örneğini tanımlayan benzersiz kod |
    |Miktar     |  İşlem miktarı      |
    |Fatura bölümü     | İşlem faturalama profili faturasının bu bölümünde gösterilir |
    |Faturalama profili     | İşlem bu faturalama profili faturasında gösterilir |

5. Faturanın işlemlerini filtrelemek için fatura kimliği araması yapın.

### <a name="view-transactions-by-invoice-sections"></a>Fatura bölümlerine göre işlemleri görüntüleme

Fatura bölümleri bir faturalama profili faturasında maliyetleri düzenlemenize yardımcı olur. Daha fazla bilgi için bkz. [Fatura bölümünü anlama](billing-mca-overview.md#invoice-sections). Fatura oluşturulduğunda faturalama profilindeki tüm bölümlere ilişkin ücretler faturada gösterilir.

Aşağıdaki resimde örnek faturanın Accounting Dept fatura bölümüne ilişkin ücretler gösterilir.

![Fatura bölümü bilgilerine göre ayrıntıların gösterildiği örnek resim](./media/billing-understand-your-bill-mca/invoicesection-details.png)

Fatura bölümünün ücretlerini belirlediğinizde, ücretleri anlamak için Azure portalında işlemleri görüntüleyebilirsiniz.

1. Faturanın işlemlerini görüntülemek için Azure portalında Tüm işlemler sayfasına gidin. Daha fazla bilgi için bkz. [Azure portalında faturanın işlemlerini görüntüleme](#view-transactions-for-an-invoice-in-the-azure-portal).

2. İşlemleri görmek için fatura bölümü adına göre filtreleyin.

## <a name="review-pending-charges-to-estimate-your-next-invoice"></a>Sonraki faturanızı tahmin etmek için bekleyen ücretleri gözden geçirme

Microsoft Müşteri Sözleşmesinin ödeme hesabında ücretler faturalanana kadar tahminidir ve beklemede kabul edilir. Bir sonraki faturanızı tahmin etmek için Azure portalında bekleyen ücretleri görüntüleyebilirsiniz. Bekleyen ücretler tahminidir ve vergileri içermez. Sonraki faturanızın gerçek ücretleri bekleyen ücretlerden farklı olacaktır.

### <a name="view-summary-of-pending-charges"></a>Bekleyen ücretlerin özetini görüntüleme

1. [Azure Portal](https://portal.azure.com) oturum açın.

2. **Maliyet Yönetimi + Faturalama** araması yapın.

   ![Azure portalında maliyet yönetimi + faturalama aramasını gösteren ekran görüntüsü](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. Faturalama profilini seçin. Erişiminize bağlı olarak, bir ödeme hesabı seçmeniz gerekebilir. Ödeme hesabında **Faturalama profilleri**'ni ve ardından bir faturalama profili seçin.

4. Ekranın üst kısmında **Çalıştır**’ı seçin.

5. Ücretler bölümünde ay başından bugüne kadarki ücretlerle geçen ayın ücretleri görüntülenir.

   ![Azure portalında maliyet yönetimi + faturalama aramasını gösteren ekran görüntüsü](./media/billing-understand-your-bill-mca/mca-billing-profile-summary.png)

Ayın başından bugüne kadarki ücretler geçerli ay için bekleyen ücretlerdir ve aya ait fatura oluşturulduğunda faturalandırılır. Geçen ayın faturası henüz oluşturulmadıysa geçen ayın ücretleri de beklemededir ve sonraki faturada gösterilir.

### <a name="view-pending-transactions"></a>Bekleyen işlemleri görüntüleme

Bekleyen ücretleri belirlediğinizde, ücretlere katkıda bulunan tek tek işlemleri analiz ederek ücretleri anlayabilirsiniz. Bu noktada, Tüm işlemler sayfasında bekleyen kullanım ücretleri görüntülenmez. Bekleyen kullanım ücretlerini Azure abonelikler sayfasında görüntüleyebilirsiniz. Daha fazla bilgi için bkz. [Bekleyen kullanım ücretlerini görüntüleme](#view-pending-usage-charges)

1. [Azure Portal](https://portal.azure.com) oturum açın.

2. **Maliyet Yönetimi + Faturalama** araması yapın.

   ![Azure portalında maliyet yönetimi + faturalama aramasını gösteren ekran görüntüsü](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. Faturalama profilini seçin. Erişiminize bağlı olarak, bir ödeme hesabı seçmeniz gerekebilir. Ödeme hesabında **Faturalama profilleri**'ni ve ardından bir faturalama profili seçin.

4. Sayfanın sol tarafında **Tüm işlemler**'i seçin.

5. *Bekleyen* terimi için arama yapın. Geçerli ayın veya geçen ayın bekleyen ücretlerini görüntülemek için **Zaman Aralığı** filtresini kullanın.

   ![Bekleyen işlemlerin listesini gösteren ekran görüntüsü](./media/billing-understand-your-bill-mca/mca-pending-transactions-list.png)

### <a name="view-pending-usage-charges"></a>Bekleyen kullanım ücretlerini görüntüleme

1. [Azure Portal](https://portal.azure.com) oturum açın.

2. *Maliyet Yönetimi + Faturalama* araması yapın.

   ![Azure portalında maliyet yönetimi + faturalama aramasını gösteren ekran görüntüsü](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. Faturalama profilini seçin. Erişiminize bağlı olarak, bir ödeme hesabı seçmeniz gerekebilir. Ödeme hesabında **Faturalama profilleri**'ni ve ardından bir faturalama profili seçin.

4. Sayfanın sol tarafında **Tüm abonelikler**'i seçin.

5. Azure abonelikleri sayfasında, faturalama profilindeki her abonelik için geçerli ayın ve geçen ayın ücretleri görüntülenir. Ayın başından bugüne kadarki ücretler geçerli ay için bekleyen ücretlerdir ve aya ait fatura oluşturulduğunda faturalandırılır. Geçen ayın faturası henüz oluşturulmadıysa geçen ayın ücretleri de beklemededir.

    ![Faturalama profili için Azure abonelikleri listesini gösteren ekran görüntüsü](./media/billing-understand-your-bill-mca/mca-billing-profile-subscriptions-list.png)

## <a name="analyze-your-azure-usage-charges"></a>Azure kullanım ücretlerinizi analiz etme

Kullanıma dayalı ücretlerinizi analiz etmek için Azure kullanımı ve ücretleri CSV dosyasını kullanın. Bu dosyayı bir fatura için veya bekleyen ücretler için indirebilirsiniz. Daha fazla bilgi için bkz. [Azure faturanızı ve günlük kullanım verilerinizi edinme](billing-download-azure-invoice-daily-usage-date.md).

### <a name="view-detailed-usage-by-invoice-section"></a>Fatura bölümüne göre ayrıntılı kullanımı görüntüleme

Fatura bölümlerinizdeki kullanım ücretlerinde mutabakat sağlamak için Azure kullanımı ve ücretleri dosyasına filtre uygulayabilirsiniz.

Aşağıdaki adımlarda Accounting Dept fatura bölümünün işlem ücretlerinde mutabakat sağlama işleminde size yol gösterilir:

![Fatura bölümü bilgilerine göre ayrıntıların gösterildiği örnek resim](./media/billing-understand-your-bill-mca/invoicesection-details.png)

 | Fatura PDF dosyası | Azure kullanımı ve ücretleri CSV dosyası |
 | --- | --- |
 |Accounting Dept |invoiceSectionName |
 |Kullanım Ücretleri - Microsoft Azure Planı |productOrderName |
 |İşlem |serviceFamily |

1. CSV dosyasındaki **invoiceSectionName** sütununa **Accounting Dept** filtresi uygulayın.
2. CSV dosyasındaki **productOrderName** sütununa **Microsoft Azure Planı** filtresi uygulayın.
3. CSV dosyasındaki **serviceFamily** sütununa **Microsoft.Compute** filtresi uygulayın.

![Fatura bölümüne göre filtrelenmiş kullanım ve ücretler dosyasını gösteren ekran görüntüsü](./media/billing-understand-your-bill-mca/billing-usage-file-filtered-by-invoice-section.png)


### <a name="view-detailed-usage-by-subscription"></a>Aboneliğe göre ayrıntılı kullanımı görüntüleme

Aboneliklerinizde kullanım ücretlerinde mutabakat sağlamak için Azure kullanımı ve ücretleri CSV dosyasına filtre uygulayabilirsiniz. Faturalama profilindeki tüm abonelikleri görüntülemek için bkz. [Bekleyen kullanım ücretlerini görüntüleme](#view-pending-usage-charges).

Abonelik için ücretleri belirledikten sonra, bu ücretleri analiz etmek için Azure kullanımı ve ücretleri CSV dosyasını kullanın.

Aşağıdaki resimde Azure portalında abonelik listesi gösterilir.

![Faturalama profili için Azure abonelikleri listesini gösteren ekran görüntüsü](./media/billing-understand-your-bill-mca/mca-billing-profile-subscriptions-list-highlighted.png)

WA_Subscription aboneliğinin ayrıntılı kullanım ücretlerini görüntülemek için Azure kullanımı ve ücretleri CSV dosyasının **subscriptionName** sütununa **WA_Subscription** filtresi uygulayın.

![Aboneliğe göre filtrelenmiş kullanım ve ücretler dosyasını gösteren ekran görüntüsü](./media/billing-understand-your-bill-mca/billing-usage-file-filtered-by-subscription.png)

## <a name="pay-your-bill"></a>Faturanızı ödeme

Faturanızı ödeme yönergeleri faturanın alt kısmında gösterilir. [Nasıl ödeneceğini öğrenin](billing-mca-understand-your-invoice.md#how-to-pay).

Faturanızı zaten ödediyseniz Azure portalındaki Faturalar sayfasında ödeme durumunu denetleyebilirsiniz.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft Müşteri Sözleşmesi’ne erişimi denetleme
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Yardıma mı ihtiyacınız var? Bize ulaşın.

Sorularınız varsa ya da yardıma gereksinim duyuyorsanız [destek isteği oluşturun](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Sonraki adımlar

Faturanız ve ayrıntılı kullanım hakkında daha fazla bilgi edinmek için bkz:

- [Azure faturanızı ve günlük kullanım verilerinizi edinme](billing-download-azure-invoice-daily-usage-date.md)
- [Microsoft Müşteri Sözleşmesi faturanızdaki terimleri anlama](billing-mca-understand-your-invoice.md)
- [Microsoft Müşteri Sözleşmesi kullanım CSV dosyanızdaki terimleri anlama](billing-mca-understand-your-usage.md)
