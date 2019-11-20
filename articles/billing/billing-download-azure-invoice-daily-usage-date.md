---
title: Azure faturasını ve günlük kullanım verilerini indirme | Microsoft Docs
description: Azure faturanızın ve günlük kullanım verilerinizin nasıl indirileceğini veya görüntüleneceğini açıklar.
keywords: fatura, fatura indirme, azure faturası, azure kullanımı
services: billing
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: banders
ms.openlocfilehash: 2c65696a37e33cd6ced93639677659e110a1db6d
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/19/2019
ms.locfileid: "71121793"
---
# <a name="download-or-view-your-azure-billing-invoice-and-daily-usage-data"></a>Azure faturanızı ve günlük kullanım verilerinizi indirme veya görüntüleme

Aboneliklerin çoğunda faturanızı [Azure portalından](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) indirebilir veya e-postayla gönderilmesini sağlayabilirsiniz. Kurumsal Anlaşmalı bir Azure müşterisiyseniz (EA müşterisi), kuruluşunuzun faturalarını indiremezsiniz. Faturalar, kayıt için faturaları alacak kişi olarak ayarlanan kişiye gönderilir.

Kurumsal Anlaşma müşterisiyseniz veya [Microsoft Müşteri Sözleşmeniz](#check-access-to-a-microsoft-customer-agreement) varsa, [Azure portalından](https://portal.azure.com/) kullanım bilgilerini indirebilirsiniz.

Yalnızca Hesap Yöneticisi veya Kuruluş Yöneticisi gibi belirli rollerin fatura ve kullanım bilgilerini alma izni vardır. Faturalama bilgilerine erişme hakkında daha fazla bilgi için bkz. [Rolleri kullanarak Azure faturalamasına erişimi yönetme](billing-manage-access.md).

Microsoft Müşteri Sözleşmeniz varsa, faturalama ve kullanım bilgilerini görüntülemek için faturalama profili Sahibi, Katkıda Bulunanı, Okuyucusu veya Fatura yöneticisi olmanız gerekir. Microsoft Müşteri Sözleşmelerine ilişkin faturalama rolleri hakkında daha fazla bilgi edinmek için bkz. [Faturalama profili rolleri ve görevleri](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="download-your-azure-invoices-pdf"></a>Azure faturalarınızı indirme (.pdf)

Aboneliklerin çoğunda faturanızı Azure portalından indirebilirsiniz. Microsoft Müşteri Sözleşmeniz varsa bkz. Faturalama profili için faturaları indirme.

### <a name="download-invoices-for-an-individual-subscription"></a>Bireysel abonelik için faturaları indirme

1. [Faturalara erişim izni olan bir kullanıcı](billing-manage-access.md) olarak Azure portalındaki [Abonelikler sayfasından](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) aboneliğinizi seçin.

2. **Faturalar**'ı seçin.

    ![Faturalama ve kullanım seçeneğini gösteren ekran görüntüsü](./media/billing-download-azure-invoice-daily-usage-date/billingandusage.png)

3. PDF faturanızın kopyasını indirmek için indirme düğmesine tıklayın ve **Faturayı indir**'i seçin. **Kullanılamıyor** iletisiyle karşılaşıyorsanız bkz. [Son fatura dönemine ait faturamı neden göremiyorum?](#noinvoice)

    ![Faturalama dönemini, indirme seçeneğini ve her faturalama dönemi için toplam ücretleri gösteren ekran görüntüsü](./media/billing-download-azure-invoice-daily-usage-date/downloadinvoice.png)

4. Ayrıca **CSV dosyasını indir**'e tıklayarak günlük tüketilen miktar dökümünüzü ve tahmini ücretleri de indirebilirsiniz.

    ![Faturayı indir'i ve kullanım sayfasını gösteren ekran görüntüsü](./media/billing-download-azure-invoice-daily-usage-date/usageandinvoice.png)

Faturanız hakkında daha fazla bilgi için bkz. [Microsoft Azure faturanızı anlama](billing-understand-your-bill.md). Maliyetlerinizin yönetimi konusunda yardım için bkz. [Azure fatura ve maliyet yönetimi ile beklenmeyen maliyetleri engelleme](billing-getting-started.md).

### <a name="download-invoices-for-a-microsoft-customer-agreement"></a>Microsoft Müşteri Sözleşmesi için faturaları indirme

Faturalar, Microsoft Müşteri Sözleşmesi’ndeki her [faturalama profili](billing-mca-overview.md#billing-profiles) için oluşturulur. Faturaları Azure portalından indirmek için faturalama profili Sahibi, Katkıda Bulunanı, Okuyucusu veya Fatura yöneticisi olmanız gerekir.

1. **Maliyet Yönetimi + Faturalama** araması yapın.
2. Bir faturalama profili seçin.
3. **Faturalar**'ı seçin.
4. Fatura kılavuzunda, indirmek istediğiniz fatura satırını bulun.
5. Satırın en sonundaki indirme düğmesine tıklayın.
6. İndirme açılır menüsünde **Fatura**’yı seçin.

Son faturalama dönemine ait bir faturayı görmüyorsanız bkz. **Ek bilgiler**. <!-- Fix this -->
### <a name="noinvoice"></a> Son faturalama dönemine ait faturamı neden göremiyorum?

Bir faturayı görmemenizin birden fazla nedeni olabilir:

- Azure'a abone olmanızın üzerinden 30 gün geçmemiştir.

- Fatura henüz düzenlenmemiştir. Fatura döneminin sonuna kadar bekleyin.

- Faturaları görüntüleme izniniz yoktur. Microsoft Müşteri Sözleşmeniz varsa faturalama profili Sahibi, Katkıda Bulunanı, Okuyucusu veya Fatura yöneticisi olmanız gerekir. Diğer abonelikler için Hesap Yöneticisi değilseniz eski faturaları göremeyebilirsiniz. Faturalama bilgilerine erişme hakkında daha fazla bilgi için bkz. [Rolleri kullanarak Azure faturalamasına erişimi yönetme](billing-manage-access.md).

- Aboneliğinizde Ücretsiz Deneme sürümünüz veya aşmadığınız bir aylık kredi tutarınız varsa, Microsoft Müşteri Sözleşmeniz olmadığı sürece bir fatura almazsınız.

## <a name="get-your-invoice-in-email-pdf"></a>Faturanızı e-postayla alma (.pdf)

Azure faturanızı e-postayla almaları için ek alıcıları kabul edebilir ve yapılandırabilirsiniz. Bu özellik destek teklifleri, Kurumsal Anlaşmalar veya Open ile Azure gibi belirli aboneliklerde kullanılamayabilir. Microsoft Müşteri sözleşmeniz varsa bkz. Faturalama profili faturalarınızı e-postayla alma.

### <a name="get-your-subscriptions-invoices-in-email"></a>Faturalama profili faturalarınızı e-postayla alma

1. [Abonelikler sayfasından](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) aboneliğinizi seçin. Kabul etme işlemini sahip olduğunuz her abonelik için ayrıca gerçekleştirin. **Faturalar**'a ve **Faturamı e-posta ile gönder**’e tıklayın.

    ![Kabul etme akışını gösteren ekran görüntüsü](./media/billing-download-azure-invoice-daily-usage-date/InvoicesDeepLink.PNG)

2. **Kabul et**’e tıklayın ve koşulları kabul edin.

    ![Kabul etme akışının 2. adımını gösteren ekran görüntüsü](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep2.PNG)

3. Sözleşmeyi kabul ettiğinizde ek alıcıları yapılandırabilirsiniz. Bir alıcı kaldırıldığında artık e-posta adresi depolanmaz. Fikrinizi değiştirirseniz adresi yeniden eklemeniz gerekir.

    ![Kabul etme akışının 3. adımını gösteren ekran görüntüsü](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep3.PNG)

Adımları izledikten sonra e-posta gönderilmediyse [profilinizin iletişim tercihleri sayfasındaki](https://account.windowsazure.com/profile) e-posta adresinin doğru olduğundan emin olun.

### <a name="opt-out-of-getting-your-subscriptions-invoices-in-email"></a>Aboneliğinizin faturalarını e-postayla almayı geri çevirme

Yukarıdaki adımları izleyip **Faturaların e-posta ile gönderilmesini geri çevir** seçeneğine tıklayarak faturanızı e-postayla almayı geri çevirebilirsiniz. Bu seçenek, faturaların e-posta ile gönderilmesi için girilmiş olan e-posta adreslerini kaldırır. Yeniden e-posta almalarını isterseniz alıcıları yeniden yapılandırabilirsiniz.

 ![Geri çevirme akışını gösteren ekran görüntüsü](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep4.PNG)

### <a name="get-your-microsoft-customer-agreement-invoices-in-email"></a>Microsoft Müşteri Sözleşmesi faturalarınızı e-postayla alma

Microsoft Müşteri Sözleşmeniz varsa faturanızı e-postayla almayı kabul edebilirsiniz. Tüm faturalama profili Sahipleri, Katkıda Bulunanları, Okuyucuları ve Fatura yöneticileri faturalarını e-postayla alır. Okuyucular e-postayla fatura alma tercihini güncelleştiremez.

1. **Maliyet Yönetimi + Faturalama** araması yapın.
1. Faturalama profilini seçin.
1. **Ayarlar** bölümünde **Özellikler**’i seçin.
1. **Faturayı e-posta ile gönder**’in altında **E-posta fatura tercihini güncelleştir**’i seçin.
1. **Kabul et**’i seçin.
1. **Güncelleştir**’e tıklayın.

### <a name="opt-out-of-getting-your-billing-profile-invoices-in-email"></a>Faturalama profili faturalarınızı e-postayla almayı geri çevirme

Yukarıdaki adımları izleyip **Geri çevir** seçeneğine tıklayarak faturanızı e-postayla almayı geri çevirebilirsiniz. Tüm Sahipler, Katkıda Bulunanlar, Okuyucular ve Fatura yöneticileri de faturalarını e-postayla almayı geri çevirmiş olur. Okuyucuysanız e-postayla fatura alma tercihini değiştiremezsiniz.

## <a name="download-usage-in-azure-portal"></a>Azure portalında kullanımı indirme

 Çoğu abonelik için, şu adımları izleyerek günlük kullanımınızı bulabilirsiniz:

1. [Faturalara erişim izni olan bir kullanıcı](billing-manage-access.md) olarak Azure portalındaki [Abonelikler sayfasından](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) aboneliğinizi seçin.

2. **Faturalar**'ı seçin.

    ![Faturalama ve kullanım seçeneğini gösteren ekran görüntüsü](./media/billing-download-azure-invoice-daily-usage-date/billingandusage.png)

3. Denetlemek istediğiniz fatura döneminin indirme düğmesine tıklayın.

4. **CSV dosyasını indir**'e tıklayarak günlük tüketilen miktar dökümünü ve tahmini ücretleri indirin.  CSV dosyasının hazırlanması birkaç dakika sürebilir.

### <a name="download-usage-for-ea-customers"></a>Kurumsal Anlaşma müşterileri için kullanımı indirme

Kurumsal Anlaşma müşterisi olarak kullanım verilerinizi görüntülemek veya indirmek için Kuruluş Yöneticisi, Hesap Sahibi veya ücretleri görüntüleme ilkesi etkinleştirilmiş Bölüm Yöneticisi olmanız gerekir.

1. [Azure Portal](https://portal.azure.com) oturum açın.
1. *Maliyet Yönetimi + Faturalama* araması yapın.

    ![Azure portalı aramasını gösteren ekran görüntüsü](./media/billing-download-azure-invoice-daily-usage-date/portal-cm-billing-search.png)

1. **Kullanım + ücretler**’i seçin.
1. İndirmek istediğiniz ay için **İndir**’i seçin.

### <a name="download-usage-for-your-microsoft-customer-agreement"></a>Microsoft Müşteri Sözleşmeniz için kullanımı indirme

Faturalama profiline ilişkin kullanım verilerini görüntülemek ve indirmek için faturalama profili Sahibi, Katkıda Bulunanı, Okuyucusu veya Fatura yöneticisi olmanız gerekir.

#### <a name="download-usage-for-billed-charges"></a>Faturalanmış ücretler için kullanım bilgilerini indirme

1. **Maliyet Yönetimi + Faturalama** araması yapın.
2. Bir faturalama profili seçin.
3. **Faturalar**'ı seçin.
4. Fatura kılavuzunda, indirmek istediğiniz kullanıma karşılık gelen fatura satırını bulun.
5. Satırın sonundaki üç noktaya (`...`) tıklayın.
6. İndirme açılır menüsünde **Azure kullanımı ve ücretleri**‘ni seçin.

#### <a name="download-usage-for-open-charges"></a>Açık ücretler için kullanım bilgilerini indirme

Ayrıca, geçerli faturalama dönemi için ay başından bugüne kadarki kullanım bilgilerini de indirebilirsiniz; başka bir deyişle ücretler henüz faturalanmamıştır.

1. **Maliyet Yönetimi + Faturalama** araması yapın.
2. Faturalama profilini seçin.
3. **Genel Bakış** dikey penceresinde **Azure kullanımı ve ücretlerini indir**’e tıklayın.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft Müşteri Sözleşmesi’ne erişimi denetleme
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Yardıma mı ihtiyacınız var? Bizimle iletişim kurun

Sorularınız varsa ya da yardıma gereksinim duyuyorsanız [destek isteği oluşturun](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Sonraki adımlar

Faturanız ve ücretleriniz hakkında daha fazla bilgi edinmek için bkz:

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
