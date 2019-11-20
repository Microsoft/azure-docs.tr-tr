---
title: Azure kullanımını ve ücretlerini Görüntüleme ve İndirme
description: Günlük Azure kullanımınızın ve ücretlerinizin nasıl indirileceğini veya görüntüleneceğini açıklar.
keywords: faturalama kullanımı, kullanım ücretleri, kullanım indirmesi, kullanımı görüntüleme, azure faturası, azure kullanımı
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: e7d1947b2194c04bb5269887b73e2f4fa13df6e7
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72375724"
---
# <a name="view-and-download-your-azure-usage-and-charges"></a>Azure kullanımınızı ve ücretlerinizi görüntüleme ve indirme

Azure kullanımı ve ücretlerinizin günlük bir dökümünü Azure portalından indirebilirsiniz. Yalnızca Hesap Yöneticisi veya Kuruluş Yöneticisi gibi belirli rollerin Azure kullanım bilgilerini alma izni vardır. Faturalama bilgilerine erişme hakkında daha fazla bilgi için bkz. [Rolleri kullanarak Azure faturalamasına erişimi yönetme](billing-manage-access.md).

Microsoft Müşteri Sözleşmeniz (MCA) varsa, Azure kullanımınızı ve ücretlerinizi görüntülemek için faturalama profili Sahibi, Katkıda Bulunanı, Okuyucusu veya Fatura yöneticisi olmanız gerekir.  Microsoft İş ortağı Sözleşmeniz (MPA) varsa, Azure kullanımı ve ücretlerini yalnızca Microsoft iş ortağı kuruluşundaki Genel Yönetici ve Yönetici Aracısı rolü görüntüleyip indirebilir. [Azure portalında ödeme hesabı türünü denetleyin](#check-your-billing-account-type).

## <a name="download-usage-from-the-azure-portal-csv"></a>Azure portalından kullanımı indirin (.csv)

1. [Azure Portal](https://portal.azure.com) oturum açın.
1. *Maliyet Yönetimi + Faturalama* araması yapın.

    ![Azure portalı aramasını gösteren ekran görüntüsü](./media/billing-download-azure-usage/portal-cm-billing-search.png)

1. Erişiminize bağlı olarak, bir Ödeme hesabı veya Faturalama profili seçmeniz gerekebilir.
1. Soldaki menüde **Faturalama** altındaki **Faturalar**’ı seçin.
1. Fatura kılavuzunda, indirmek istediğiniz kullanıma karşılık gelen faturalama döneminin satırını bulun.
1. Sağ taraftaki indir simgesine veya üç noktaya (`...`) tıklayın.
1. İndirme menüsünden **Azure kullanımı ve ücretlerini indir**’i seçin.

## <a name="download-usage-for-ea-customers"></a>Kurumsal Anlaşma müşterileri için kullanımı indirme

Kurumsal Anlaşma müşterisi olarak kullanım verilerinizi görüntülemek veya indirmek için Kuruluş Yöneticisi, Hesap Sahibi veya ücretleri görüntüleme ilkesi etkinleştirilmiş Bölüm Yöneticisi olmanız gerekir.

1. [Azure Portal](https://portal.azure.com) oturum açın.
1. *Maliyet Yönetimi + Faturalama* araması yapın.

    ![Azure portalı aramasını gösteren ekran görüntüsü](./media/billing-download-azure-usage/portal-cm-billing-search.png)

1. **Kullanım + ücretler**’i seçin.
1. İndirmek istediğiniz ay için **İndir**’i seçin.

## <a name="download-usage-for-pending-charges"></a>Beklemedeki ücretler için kullanım bilgilerini indirme

Microsoft Müşteri Sözleşmeniz varsa, geçerli faturalama dönemi için ay içindeki kullanımı indirebilirsiniz. Henüz faturalanmamış kullanım ücretleri.

1. [Azure Portal](https://portal.azure.com) oturum açın.
2. *Maliyet Yönetimi + Faturalama* araması yapın.
3. Faturalama profilini seçin. Erişiminize bağlı olarak önce bir ödeme hesabı seçmeniz gerekebilir.
4. **Genel Bakış** alanında, ay başından bugüne kadarki ücretlerin altında indirme bağlantılarını bulun.
5. **Azure kullanımı ve ücretleri**’ni seçin.

    ![Genel Bakış’tan indirme seçeneğini gösteren ekran görüntüsü](./media/billing-download-azure-usage/open-usage.png)

## <a name="check-your-billing-account-type"></a>Ödeme hesabınızın türünü denetleme
[!INCLUDE [billing-check-account-type](../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>Yardıma mı ihtiyacınız var? Bize ulaşın.

Sorularınız varsa ya da yardıma gereksinim duyuyorsanız [destek isteği oluşturun](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Sonraki adımlar

Faturanız ve kullanım ücretleri hakkında daha fazla bilgi edinmek için bkz:

- [Ayrıntılı Microsoft Azure kullanım bilgilerinizdeki terimleri anlama](billing-understand-your-usage.md)
- [Microsoft Azure faturanızı anlama](billing-understand-your-bill.md)
- [Microsoft Azure faturanızı görüntüleme ve indirme](billing-download-azure-invoice.md)
- [Kuruluşunuzun Azure fiyatlandırmasını görüntüleme ve indirme](billing-ea-pricing.md)

Microsoft Müşteri Sözleşmeniz varsa bkz:

- [Microsoft Müşteri Sözleşmesi ayrıntılı Azure kullanım bilgilerinizdeki terimleri anlama](billing-mca-understand-your-usage.md)
- [Microsoft Müşteri Sözleşmesi faturanızdaki ücretleri anlama](billing-mca-understand-your-bill.md)
- [Microsoft Azure faturanızı görüntüleme ve indirme](billing-download-azure-invoice.md)
- [Microsoft Müşteri Sözleşmeniz için vergi belgelerini görüntüleme ve indirme](billing-mca-download-tax-document.md)
- [Kuruluşunuzun Azure fiyatlandırmasını görüntüleme ve indirme](billing-ea-pricing.md)
