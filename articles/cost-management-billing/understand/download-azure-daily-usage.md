---
title: Azure kullanımını ve ücretlerini Görüntüleme ve İndirme
description: Azure günlük kullanımınızı ve ücretlerinizi indirmeyi veya görüntülemeyi öğrenin, kullanılabilir ek kaynaklara bakın.
keywords: faturalama kullanımı, kullanım ücretleri, kullanım indirmesi, kullanımı görüntüleme, azure faturası, azure kullanımı
author: bandersmsft
ms.author: banders
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.openlocfilehash: 141da3dfab9996110952ec266733271582e66ff9
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91439192"
---
# <a name="view-and-download-your-azure-usage-and-charges"></a>Azure kullanımınızı ve ücretlerinizi görüntüleme ve indirme

Azure kullanımı ve ücretlerinizin günlük bir dökümünü Azure portalından indirebilirsiniz. Yalnızca Hesap Yöneticisi veya Kuruluş Yöneticisi gibi belirli rollerin Azure kullanım bilgilerini alma izni vardır. Faturalama bilgilerine erişme hakkında daha fazla bilgi için bkz. [Rolleri kullanarak Azure faturalamasına erişimi yönetme](../manage/manage-billing-access.md).

Microsoft Müşteri Sözleşmeniz (MCA) varsa, Azure kullanımınızı ve ücretlerinizi görüntülemek için faturalama profili Sahibi, Katkıda Bulunanı, Okuyucusu veya Fatura yöneticisi olmanız gerekir.  Microsoft İş ortağı Sözleşmeniz (MPA) varsa, Azure kullanımı ve ücretlerini yalnızca Microsoft iş ortağı kuruluşundaki Genel Yönetici ve Yönetici Aracısı rolü görüntüleyip indirebilir. [Azure portalında ödeme hesabı türünü denetleyin](#check-your-billing-account-type).

Kullandığınız aboneliğin türüne bağlı olarak kullanımınızı ve ücretlerinizi indirme seçenekleri değişir.

## <a name="download-usage-from-the-azure-portal-csv"></a>Azure portalından kullanımı indirin (.csv)

1. [Azure Portal](https://portal.azure.com) oturum açın.
1. *Maliyet Yönetimi + Faturalama* araması yapın.  
    ![Ekran görüntüsü Azure portalda Maliyet Yönetimi + Faturalama aramasını gösterir.](./media/download-azure-daily-usage/portal-cm-billing-search.png)
1. Erişiminize bağlı olarak, bir Ödeme hesabı veya Faturalama profili seçmeniz gerekebilir.
1. Soldaki menüde **Faturalama** altındaki **Faturalar**’ı seçin.
1. Fatura kılavuzunda, indirmek istediğiniz kullanıma karşılık gelen faturalama döneminin satırını bulun.
1. Sağ taraftaki **indir simgesini** veya üç noktayı (`...`) seçin.  
  ![Ekran görüntüsü indirme seçeneğinin bulunduğu Maliyet Yönetimi + Faturalama Faturalar sayfasını gösterir.](./media/download-azure-daily-usage/download-usage-others.png)  
1. İndirme bölmesi sağ tarafta açılır. **Kullanım Ayrıntıları** bölümünde **İndir** seçeneğini belirleyin.  

## <a name="download-usage-for-ea-customers"></a>Kurumsal Anlaşma müşterileri için kullanımı indirme

Kurumsal Anlaşma müşterisi olarak kullanım verilerinizi görüntülemek veya indirmek için Kuruluş Yöneticisi, Hesap Sahibi veya ücretleri görüntüleme ilkesi etkinleştirilmiş Bölüm Yöneticisi olmanız gerekir.

1. [Azure Portal](https://portal.azure.com) oturum açın.
1. *Maliyet Yönetimi + Faturalama* araması yapın.  
    ![Ekran görüntüsü Azure portal aramasını gösterir.](./media/download-azure-daily-usage/portal-cm-billing-search.png)
1. **Kullanım + ücretler**’i seçin.
1. İndirmek istediğiniz ay için **İndir**’i seçin.  
    ![Ekran görüntüsü E A müşterileri için Maliyet Yönetimi + Faturalama Faturalar sayfasını gösterir.](./media/download-azure-daily-usage/download-usage-ea.png)

## <a name="download-usage-for-pending-charges"></a>Beklemedeki ücretler için kullanım bilgilerini indirme

Microsoft Müşteri Sözleşmeniz varsa, geçerli faturalama dönemi için ay içindeki kullanımı indirebilirsiniz. Henüz faturalanmamış kullanım ücretleri.

1. [Azure Portal](https://portal.azure.com) oturum açın.
2. *Maliyet Yönetimi + Faturalama* araması yapın.
3. Faturalama profilini seçin. Erişiminize bağlı olarak önce bir ödeme hesabı seçmeniz gerekebilir.
4. **Genel Bakış** alanında, son ücretlerin altında indirme bağlantılarını bulabilirsiniz.
5. **Kullanımı ve fiyatları indir**'i seçin.  
    :::image type="content" source="./media/download-azure-daily-usage/open-usage01.png" alt-text="Genel Bakış’tan indirme seçeneğini gösteren ekran görüntüsü" lightbox="./media/download-azure-daily-usage/open-usage01.png" :::

## <a name="check-your-billing-account-type"></a>Ödeme hesabınızın türünü denetleme
[!INCLUDE [billing-check-account-type](../../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>Yardıma mı ihtiyacınız var? Bize ulaşın.

Sorularınız varsa ya da yardıma gereksinim duyuyorsanız [destek isteği oluşturun](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Sonraki adımlar

Faturanız ve kullanım ücretleri hakkında daha fazla bilgi edinmek için bkz:

- [Ayrıntılı Microsoft Azure kullanım bilgilerinizdeki terimleri anlama](understand-usage.md)
- [Microsoft Azure faturanızı anlama](review-individual-bill.md)
- [Microsoft Azure faturanızı görüntüleme ve indirme](download-azure-invoice.md)
- [Kuruluşunuzun Azure fiyatlandırmasını görüntüleme ve indirme](../manage/ea-pricing.md)

Microsoft Müşteri Sözleşmeniz varsa bkz:

- [Microsoft Müşteri Sözleşmesi ayrıntılı Azure kullanım bilgilerinizdeki terimleri anlama](mca-understand-your-usage.md)
- [Microsoft Müşteri Sözleşmesi faturanızdaki ücretleri anlama](review-customer-agreement-bill.md)
- [Microsoft Azure faturanızı görüntüleme ve indirme](download-azure-invoice.md)
- [Microsoft Müşteri Sözleşmeniz için vergi belgelerini görüntüleme ve indirme](mca-download-tax-document.md)
- [Kuruluşunuzun Azure fiyatlandırmasını görüntüleme ve indirme](../manage/ea-pricing.md)
