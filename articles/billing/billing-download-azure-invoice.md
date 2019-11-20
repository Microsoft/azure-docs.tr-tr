---
title: Microsoft Azure faturanızı görüntüleme ve indirme
description: Microsoft Azure faturanızı görüntüleme ve indirme işlemleri açıklanır.
keywords: fatura, fatura indirme, azure faturası, azure kullanımı
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
ms.openlocfilehash: 80ec40a7411a370460d663084f9f7034b28e1a2e
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72375753"
---
# <a name="view-and-download-your-microsoft-azure-invoice"></a>Microsoft Azure faturanızı görüntüleme ve indirme

Aboneliklerin çoğunda faturanızı [Azure portalından ](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)indirebilir veya e-postayla gönderilmesini sağlayabilirsiniz. Kurumsal Anlaşmalı bir Azure müşterisiyseniz (EA müşterisi), kuruluşunuzun faturalarını indiremezsiniz. Faturalar, kayıt için faturaları alacak kişi olarak ayarlanan kişiye gönderilir.

Yalnızca belirli rollerin fatura görüntüleme izni vardır. Örneğin, Hesap Yöneticisi veya Kuruluş Yöneticisi. Faturalama bilgilerine erişme hakkında daha fazla bilgi için bkz. [Rolleri kullanarak Azure faturalamasına erişimi yönetme](billing-manage-access.md).

Microsoft Müşteri Sözleşmeniz (MCA) varsa faturalarınızı almak için aşağıdaki rollerden birine sahip olmanız gerekir:

- Faturalama profili sahibi
- Katılımcı
- Okuyucu
- Fatura yöneticisi

Microsoft İş ortağı Sözleşmeniz (MPA) varsa, Azure faturalarını görüntüleyip indirmek için iş ortağı kuruluşunda Genel Yönetici ve Yönetici Aracısı rolüne sahip olmanız gerekir. Gerekli izinleri öğrenmek için [Ödeme hesabınızın türünü denetleyin](#check-your-billing-account-type). 

<!-- For more information about billing roles for Microsoft Customer Agreements, see [Billing profile roles and tasks](billing-understand-mca-roles.md#billing-profile-roles-and-tasks). -->

## <a name="noinvoice"></a> Fatura alamamanızın nedeni nedir?

Bir faturayı görmemenizin birden fazla nedeni olabilir:

- Azure'a abone olmanızın üzerinden 30 gün geçmemiştir.

- Azure sizi fatura döneminizin sonunda faturalar. Bu nedenle fatura henüz oluşturulmamış olabilir. Fatura döneminin sonuna kadar bekleyin.

- Faturaları görüntüleme izniniz yoktur. MCA veya MPA’nız varsa faturalama profilinin Sahibi, Katkıda Bulunanı, Okuyucusu veya Fatura yöneticisi olmanız gerekir. Diğer abonelikler için Hesap Yöneticisi değilseniz eski faturaları göremeyebilirsiniz. Faturalama bilgilerine erişme hakkında daha fazla bilgi için bkz. [Rolleri kullanarak Azure faturalamasına erişimi yönetme](billing-manage-access.md).

- Aboneliğinizde Ücretsiz Deneme veya aylık kredi miktarı varsa, yalnızca aylık kredi miktarını aştığınızda faturalandırılırsınız. Microsoft Müşteri Sözleşmeniz veya Microsoft İş Ortağı Sözleşmeniz varsa her zaman fatura alırsınız. 

## <a name="download-invoices-in-the-azure-portal"></a>Azure portalında fatura indirme

1. [Azure Portal](https://portal.azure.com) oturum açın.
1. *Maliyet Yönetimi + Faturalama* araması yapın.
1. Erişiminize bağlı olarak, bir ödeme hesabı veya faturalama profili seçmeniz gerekebilir.
1. Soldaki menüde **Faturalama** altındaki **Faturalar**’ı seçin.
1. Fatura kılavuzunda, indirmek istediğiniz fatura satırını bulun.
1. İndirme simgesine veya satırın sonundaki üç noktaya (`...`) tıklayın.
1. İndirme menüsünde **Fatura**’yı seçin.

Faturanız hakkında daha fazla bilgi için bkz. [Microsoft Azure faturanızı anlama](billing-understand-your-bill.md). Maliyetlerinizi yönetme konusunda yardım için bkz. [Azure fatura ve maliyet yönetimi ile beklenmeyen maliyetleri engelleme](billing-getting-started.md).

## <a name="get-your-subscriptions-invoices-in-email"></a>Faturalama profili faturalarınızı e-postayla alma

Azure faturanızı e-postayla almaları için ek alıcıları kabul edebilir ve yapılandırabilirsiniz. Bu özellik destek teklifleri, Kurumsal Anlaşmalar veya Open ile Azure gibi belirli aboneliklerde kullanılamayabilir.

1. [Abonelikler sayfasında](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) aboneliğinizi seçin. Kabul etme işlemini sahip olduğunuz her abonelik için ayrıca gerçekleştirin. **Faturalar**'a ve **Faturamı e-posta ile gönder**’e tıklayın.

    ![Kabul etme akışını gösteren ekran görüntüsü](./media/billing-download-azure-invoice-daily-usage-date/InvoicesDeepLink.PNG)

2. **Kabul et**’e tıklayın ve koşulları kabul edin.

    ![Kabul etme akışının 2. adımını gösteren ekran görüntüsü](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep2.PNG)

3. Sözleşmeyi kabul ettiğinizde ek alıcıları yapılandırabilirsiniz. Bir alıcı kaldırıldığında artık e-posta adresi depolanmaz. Fikrinizi değiştirirseniz adresi yeniden eklemeniz gerekir.

    ![Kabul etme akışının 3. adımını gösteren ekran görüntüsü](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep3.PNG)

Adımları izledikten sonra e-posta gönderilmediyse [profilinizin iletişim tercihleri sayfasındaki](https://account.windowsazure.com/profile) e-posta adresinin doğru olduğundan emin olun.

## <a name="opt-out-of-getting-your-subscriptions-invoices-in-email"></a>Aboneliğinizin faturalarını e-postayla almayı geri çevirme

Faturanızı e-postayla almayı geri çevirmek için önceki adımları izleyin ve **Faturaların e-posta ile gönderilmesini geri çevir**’e tıklayın. Bu seçenek, faturaların e-posta ile gönderilmesi için girilmiş olan e-posta adreslerini kaldırır. Yeniden e-posta almalarını isterseniz alıcıları yeniden yapılandırabilirsiniz.

 ![Geri çevirme akışını gösteren ekran görüntüsü](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep4.PNG)

<!-- Does following section apply to MPA too? -->
## <a name="get-your-microsoft-customer-agreement-invoices-in-email"></a>Microsoft Müşteri Sözleşmesi faturalarınızı e-postayla alma

Microsoft Müşteri Sözleşmeniz varsa faturanızı e-postayla almayı kabul edebilirsiniz. Tüm faturalama profili Sahipleri, Katkıda Bulunanları, Okuyucuları ve Fatura yöneticileri faturalarını e-postayla alır. Okuyucular e-postayla fatura alma tercihini güncelleştiremez.

1. [Azure Portal](https://portal.azure.com) oturum açın.
1. **Maliyet Yönetimi + Faturalama** araması yapın.
1. Faturalama profilini seçin. Erişiminize bağlı olarak önce bir ödeme hesabı seçmeniz gerekebilir.
1. **Ayarlar** bölümünde **Özellikler**'i seçin.
1. **Faturayı e-posta ile gönder**’in altında **E-posta fatura tercihini güncelleştir**’i seçin.

    ![Faturayı e-posta ile gönderme özelliklerini gösteren ekran görüntüsü](./media/billing-download-azure-invoice/billingprofile-email.png)

1. **Kabul et**’i seçin.
1. **Güncelleştir**’e tıklayın.

## <a name="opt-out-of-getting-your-microsoft-customer-agreement-invoices-in-email"></a>Microsoft Müşteri Sözleşmesi faturalarınızı e-postayla almayı geri çevirme

Faturanızı e-postayla almayı geri çevirmek için önceki adımları izleyin ve **Geri çevir**’e tıklayın. Tüm Sahipler, Katkıda bulunanlar, Okuyucular ve Fatura yöneticileri de faturalarını e-postayla almayı geri çevirmiş olur. Okuyucuysanız e-postayla fatura alma tercihini değiştiremezsiniz.

## <a name="check-your-billing-account-type"></a>Ödeme hesabınızın türünü denetleme
[!INCLUDE [billing-check-account-type](../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>Yardıma mı ihtiyacınız var? Bize ulaşın.

Sorularınız varsa ya da yardıma gereksinim duyuyorsanız [destek isteği oluşturun](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Sonraki adımlar

Faturanız ve ücretleriniz hakkında daha fazla bilgi edinmek için bkz:

- [Microsoft Azure kullanımınızı ve ücretlerinizi görüntüleme ve indirme](billing-download-azure-daily-usage.md)
- [Microsoft Azure faturanızı anlama](billing-understand-your-bill.md)
- [Azure faturanızdaki terimleri anlama](billing-understand-your-invoice.md)
- [Ayrıntılı Microsoft Azure kullanım bilgilerinizdeki terimleri anlama](billing-understand-your-usage.md)
- [Kuruluşunuzun Azure fiyatlandırmasını görüntüleme](billing-ea-pricing.md)

Microsoft Müşteri Sözleşmeniz varsa bkz:

- [Faturalama profilinizin faturasındaki ücretleri anlama](billing-mca-understand-your-bill.md)
- [Faturalama profilinizin faturasındaki terimleri anlama](billing-mca-understand-your-invoice.md)
- [Faturalama profilinizin Azure kullanımı ve ücretleri dosyasını anlama](billing-mca-understand-your-usage.md)
- [Faturalama profiliniz için vergi belgelerini görüntüleme ve indirme](billing-mca-download-tax-document.md)
- [Kuruluşunuzun Azure fiyatlandırmasını görüntüleme](billing-ea-pricing.md)
