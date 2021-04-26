---
title: Azure’da Microsoft Müşteri Sözleşmesi faturanızı anlama
description: Azure’da Microsoft Müşteri Sözleşmesi faturanızı okuma ve anlama hakkında bilgi edinin
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 04/08/2021
ms.author: banders
ms.openlocfilehash: ff53131f3078b33b7e7d853c1fca891b0b86d792
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484613"
---
# <a name="terms-in-your-microsoft-customer-agreement-invoice"></a>Microsoft Müşteri Sözleşmesi faturanızdaki koşullar

Bu makale, Microsoft Müşteri Sözleşmesi’ne ilişkin Azure ödeme hesabı için geçerlidir. [Microsoft Müşteri Sözleşmesi’ne erişiminiz olup olmadığını denetleyin](#check-access-to-a-microsoft-customer-agreement).

Faturanızda ücretlerinizin özeti ve ödeme yönergeleri yer alır. [Azure portalından](https://portal.azure.com/) Taşınabilir Belge Biçiminde (.pdf) indirilebilir veya e-postayla gönderilebilir. Daha fazla bilgi için bkz. [Microsoft Azure faturanızı görüntüleme ve indirme](download-azure-invoice.md).

Faturanızla ilgili bilgi edinmek ve bu faturadaki ücretleri çözümlemek için [Microsoft müşteri anlaşması faturanızı](https://www.youtube.com/watch?v=e2LGZZ7GubA) öğrenin.

>[!VIDEO https://www.youtube.com/embed/e2LGZZ7GubA]

## <a name="billing-period"></a>Faturalama dönemi

Aylık olarak faturalandırılırsınız. [Azure portalında](https://portal.azure.com/) faturalama profili özellikleri bölümünde *fatura tarihi* bilgisini kontrol ederek faturaları ayın hangi günü alacağınızı öğrenebilirsiniz. Faturalama döneminin sonu ile fatura tarihi arasında gerçekleşen ücretler, sonraki faturalama döneminde yer aldığından sonraki ayın faturasına dahil edilir. Her bir fatura için faturalama dönemi başlangıç ve bitiş tarihleri, faturanın PDF dosyasında **Faturalama Özeti** bölümünün yukarısında listelenir.

Bir EA 'dan Microsoft Müşteri sözleşmesine geçiş yapıyorsanız, geçiş tarihine kadar EA 'niz için faturalar almaya devam edersiniz. Microsoft Müşteri anlaşmanız için yeni fatura, geçiş işleminden sonraki ayın beşinci gününde oluşturulur. İlk faturada geçiş tarihinden itibaren kısmi bir ücret gösterilir. Sonraki faturalar her ay oluşturulur ve her ayın tüm ücretlerini gösterir.

### <a name="changes-for-pay-as-you-go-subscriptions"></a>Kullandıkça Öde aboneliklerine yönelik değişiklikler

Bir abonelik geçirilir, aktarıldığında veya iptal edildiğinde, oluşturulan son faturada önceki fatura dönemine ve yeni tamamlanmamış fatura döngüsüne yönelik ücretler bulunur.

Örnek:

Kullandıkça Öde aboneliğiniz fatura döngünüzün her ayın 8 ile 7 güne kadar olduğunu varsayalım. Abonelik, 16 Kasım 'da bir Microsoft Müşteri sözleşmesine aktarıldı. Son Kullandıkça Öde faturanızda, 8 Ekim 2020 ' de 7 Kasım 2020 ' ye kadar ücret alınır. Ayrıca, 8 Kasım 2020 ' den 16 Kasım 2020 ' ye kadar Microsoft Müşteri Sözleşmesi için yeni kısmi faturalandırma döngüsünün ücretlendirisini de içerir. Burada bir örnek resim verilmiştir.

:::image type="content" source="./media/mca-understand-your-invoice/last-invoice-billing-cycle.png" alt-text="Son fatura döngüsünü gösteren bir faturanın örnek resmi." lightbox="./media/mca-understand-your-invoice/last-invoice-billing-cycle.png" :::

## <a name="invoice-terms-and-descriptions"></a>Fatura terimleri ve açıklamaları

Aşağıdaki bölümlerde, faturanızda gördüğünüz önemli terimler ve her bir terimin açıklaması listelenmektedir.

### <a name="invoice-summary"></a>Fatura özeti

**Fatura Özeti**, birinci sayfanın en üstünde yer alır ve faturalama profiliniz ve ödeme şekliniz ile ilgili bilgileri gösterir.

![Fatura özeti bölümü](./media/mca-understand-your-invoice/invoicesummary.png)

| Süre | Açıklama |
| --- | --- |
| Kime Satıldı |Ödeme hesabı özelliklerinde bulunan tüzel kişiliğinizin adresi|
| Fatura adresi |Faturalama profili özelliklerinde bulunan, faturayı alan faturalama profilinin fatura adresi|
| Faturalama Profili |Faturayı alan faturalama profilinin adı |
| P.O. sayı |İzleme için atadığınız isteğe bağlı bir satınalma siparişi numarası |
| Fatura numarası |İzleme amacıyla kullanılan, Microsoft tarafından oluşturulan benzersiz bir fatura numarası |
| Fatura tarihi |Faturanın oluşturulduğu tarih, genellikle Faturalama döneminin sona erdiği günden beş ila 12 gün sonrasıdır. Faturalama profili özelliklerinden fatura tarihinizi kontrol edebilirsiniz.|
| Ödeme koşulları |Microsoft faturanız için ödeme şekliniz. *Net 30 gün*, fatura tarihinden sonra 30 gün içinde ödeme yapacağınız anlamına gelir. |

### <a name="billing-summary"></a>Faturalama özeti

**Faturalama Özeti**, önceki faturalama döneminden bu yana faturalama dönemine karşı oluşan ücretleri, uygulanan kredileri, vergiyi ve ödenmesi gereken toplam tutarı gösterir.

![Faturalama özeti bölümü](./media/mca-understand-your-invoice/billingsummary.png)

| Süre | Açıklama |
| --- | --- |
| Ücretler|Son faturalama döneminden bu yana bu faturalama profili için oluşan toplam Microsoft ücreti sayısı |
| Krediler |İadelerden aldığınız krediler |
| Uygulanan Azure kredileri | Her faturalama döneminde Azure ücretlerine otomatik olarak uygulanan Azure kredileri |
| Ara toplam |Ödenmesi gereken vergi öncesi tutar |
| Vergi |Faturalama profilinizin ülke/bölgesine bağlı olarak, ödediğiniz verginin türü ve tutarı. Vergi ödemeniz gerekmiyorsa faturanızda vergiyi görmezsiniz. |
| Tahmini toplam tasarruf |Geçerli indirimlerden kazandığınız tahmini toplam tutar. Varsa, geçerli indirimli fiyatlar, Faturaya Göre Ayrıntılar Bölümünde satın alma kalemleri bölümünün altında listelenir. |

### <a name="invoice-sections"></a>Fatura bölümleri

Faturalama profilinizin altındaki her bir fatura bölümü için, ücretleri, uygulanan Azure kredisi tutarını, vergiyi ve ödenmesi gereken toplam tutarı görürsünüz.

`Total = Charges - Azure Credit + Tax`

### <a name="details-by-invoice-section"></a>Faturaya göre ayrıntılar bölümü

Ayrıntılar, her bir fatura bölümü için, ürün siparişine göre dökümü alınmış maliyeti gösterir. Her ürün siparişi içinde, hizmet türüne göre maliyetin dökümü gösterilir. Azure portalında ve Azure kullanım bilgilerini ve ücretlerini içeren CSV dosyasında ürünleriniz ve hizmetleriniz için günlük ücretleri bulabilirsiniz. Daha fazla bilgi edinmek için bkz. [Microsoft Müşteri Sözleşmesi faturanızdaki ücretleri anlama](review-customer-agreement-bill.md).

Her bir hizmet için ödenmesi gereken toplam tutar, *Krediler/ücretler* tutarından *Azure kredileri* düşülüp *Vergi* eklenerek hesaplanır:


![Faturaya göre ayrıntılar bölümü](./media/mca-understand-your-invoice/invoicesectiondetails.png)

| Süre |Açıklama |
| --- | --- |
| Birim fiyat | Kullanımı fiyatlandırmak için kullanılan hizmetin geçerli birim fiyatı (fiyatlandırma para birimi cinsinden). Ürün, hizmet ailesi, ölçüm ve teklif için bu benzersizdir. |
| Miktar | Faturalama döneminde satın alınan veya kullanılan miktar |
| Ücretler/Krediler | Krediler/para iadeleri uygulandıktan sonraki net ücret tutarı |
| Azure Kredisi | Ücretlere/Kredilere uygulanan Azure kredileri tutarı|
| Vergi oranı | Ülke/bölgeye göre vergi oranları |
| Vergi tutarı | Vergi oranına göre satın alma işlemine uygulanan vergi tutarı |
| Toplam | Satın alma için ödenmesi gereken toplam tutar |

### <a name="how-to-pay"></a>Ödeme şekli

Faturanın alt kısmında, faturanızı ödeme yönergeleri yer alır. Çekle, havale yoluyla veya çevrimiçi ödeme yapabilirsiniz. Çevrimiçi ödeme yaparsanız, varsa kredi kartı veya Azure kredilerini kullanabilirsiniz.

### <a name="publisher-information"></a>Yayımcı bilgileri

Faturanızda üçüncü taraf hizmetleriniz varsa, her yayımcının adı ve adresi faturanızın en altında listelenir.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft Müşteri Sözleşmesi’ne erişimi denetleme
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Yardıma mı ihtiyacınız var? Bize ulaşın.

Sorularınız varsa ya da yardıma gereksinim duyuyorsanız [destek isteği oluşturun](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Sonraki adımlar

- [Faturalama profilinizin faturasındaki ücretleri anlama](review-customer-agreement-bill.md)
- [Azure faturanızı ve günlük kullanım verilerinizi edinme](../manage/download-azure-invoice-daily-usage-date.md)
- [Kuruluşunuzun Azure fiyatlandırmasını görüntüleme](../manage/ea-pricing.md)
- [Faturalama profiliniz için vergi belgelerini görüntüleme](mca-download-tax-document.md)
