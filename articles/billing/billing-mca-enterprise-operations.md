---
title: Microsoft Müşteri Sözleşmesi'ndeki Kurumsal Anlaşma görevlerini tamamlama - Azure
description: Yeni ödeme hesabınızda Kurumsal Anlaşma görevlerini tamamlamayı öğrenin.
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: ca0f83628edeef9e55ebc81798d0229791b80a7a
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72375495"
---
# <a name="complete-enterprise-agreement-tasks-in-your-billing-account-for-a-microsoft-customer-agreement"></a>Microsoft Müşteri Sözleşmesi için ödeme hesabınızda Kurumsal Anlaşma görevlerini tamamlama

Kuruluşunuz Kurumsal Anlaşma kaydınızı yenilemek için bir Microsoft Müşteri Sözleşmesi imzaladıysa, anlaşma için yeni bir ödeme hesabı oluşturulur. Yeni hesabınızda ödemeler Kurumsal Anlaşmanızdan farklı düzenlenir. Bu makalede Kurumsal Anlaşmada gerçekleştirdiğiniz görevleri yeni ödeme hesabını kullanarak nasıl gerçekleştireceğiniz açıklanır.

## <a name="billing-organization-in-the-new-account"></a>Yeni hesapta faturalama kuruluşu

Aşağıdaki diyagramda yeni ödeme hesabınızda faturalamanın nasıl düzenlendiği açıklanır.

![ea-mca-post-transition-hierarchy resmi](./media/mca-setup-account/mca-post-transition-hierarchy.png)

| Kurumsal Anlaşma   | Microsoft Müşteri Sözleşmesi    |
|------------------------|--------------------------------------------------------|
| Kayıt            | Kuruluşunuzda faturalamayı yönetmek için Kurumsal Anlaşma kaydınıza benzer bir faturalama profili kullanırsınız. Kuruluş yöneticileri faturalama profilinin sahibi olur. Faturalama profilleri hakkında daha fazla bilgi edinmek için bkz. [Faturalama profillerini anlama](billing-mca-overview.md#billing-profiles).
| Bölüm            | Maliyetlerinizi düzenlemek için Kurumsal Anlaşma kaydınızdaki bölümlere benzer bir fatura bölümü kullanırsınız. Bölümler fatura bölümlerine ve bölüm yöneticileri de ilgili fatura bölümlerinin sahiplerine dönüşür. Fatura bölümleri hakkında daha fazla bilgi edinmek için bkz. [Fatura bölümlerini anlama](billing-mca-overview.md#invoice-sections). |
| Hesap               | Kurumsal Anlaşmanızda oluşturulan hesaplar yeni ödeme hesabında desteklenmez. Hesabın abonelikleri, kendi bölümleri için ilgili fatura bölümüne aittir. Hesap sahipleri kendi fatura bölümleri için abonelikleri oluşturabilir ve yönetebilir. |

## <a name="changes-for-enterprise-administrators"></a>Kuruluş yöneticileri için değişiklikler

Aşağıdaki değişiklikler Microsoft Müşteri Sözleşmesi'ne yenilenmiş olan Kurumsal Anlaşmanın kuruluş yöneticileri için geçerlidir.

- Kaydınız için bir faturalama profili oluşturulur. Kuruluşunuzda faturalamayı yönetmek için Kurumsal Anlaşma kaydınıza benzer bir faturalama profili kullanacaksınız. Faturalama profilleri hakkında daha fazla bilgi edinmek için [faturalama profillerini anlama](billing-mca-overview.md#billing-profiles) konusuna bakın.
- Kurumsal Anlaşma kaydınızdaki her bölüm için bir fatura bölümü oluşturulur. Kuruluşunuzdaki bölümleri yönetmek için fatura bölümlerini kullanacaksınız. Ek bölümleri ayarlamak için yeni fatura bölümleri oluşturabilirsiniz. Fatura bölümleri hakkında daha fazla bilgi edinmek için [fatura bölümlerini anlama](billing-mca-overview.md#invoice-sections) konusuna bakın.
- Kurumsal Anlaşma kaydında oluşturulan hesaplar gibi, diğerlerine Azure aboneliği oluşturma izni vermek için fatura bölümlerinde Azure aboneliği oluşturucusu rolünü kullanacaksınız.
- Kuruluşunuzun faturalamasını yönetmek için Azure EA portalı yerine [Azure portalını](https://portal.azure.com) kullanacaksınız.

Yeni ödeme hesabında size aşağıdaki roller verilir:

**Faturalama profili sahibi** - Sözleşme imzalandığında oluşturulmuş olan faturalama profili üzerinde size faturalama profili sahibi rolü atanır. Bu rol kuruluşunuzda faturalamayı yönetmenize olanak tanır. Ödemeleri ve faturaları görüntüleyebilir, faturadaki maliyetleri düzenleyebilir, ödeme yöntemlerini yönetebilir ve kuruluşunuzun faturalamasına erişimi denetleyebilirsiniz.

**Fatura bölümü sahibi** - Kurumsal Anlaşma kaydınızdaki bölümler için oluşturulan tüm fatura bölümlerinde size fatura bölümü sahibi rolü atanır. Bu rol kimlerin Azure abonelikleri oluşturabileceğini ve başka ürünler satın alabileceğini denetlemenize olanak tanır.

### <a name="view-charges-and-credits-balance-for-your-organization"></a>Kuruluşunuzun ödemelerini ve kredi bakiyesini görüntüleme

Kuruluşunuzda ücretleri ve Azure kredi bakiyenizi izlemek için Kurumsal Anlaşma kaydınıza benzer biçimde faturalama profilinizi kullanırsınız.

Faturalama profilinin kredi bakiyesini görüntülemeyi öğrenmek için bkz. [Faturalama profiliniz için Azure kredi bakiyesini izleme](billing-mca-check-azure-credits-balance.md).

Faturalama profilinin ücretlerini görüntülemeyi öğrenmek için bkz. [Microsoft Müşteri Sözleşmesi faturanızdaki ücretleri anlama](billing-mca-understand-your-bill.md).

### <a name="view-charges-for-a-department"></a>Bölüm için ücretleri görüntüleme

Daha önce Kurumsal Anlaşmanızda bulunan her bölüm için bir fatura bölümü oluşturulur. Azure portalında fatura bölümünün ücretlerini görüntüleyebilirsiniz. Daha fazla bilgi için bkz. [Fatura bölümlerine göre işlemleri görüntüleme](billing-mca-understand-your-bill.md#view-transactions-by-invoice-sections).

### <a name="view-charges-for-an-account"></a>Hesap için ücretleri görüntüleme

Kurumsal Anlaşma kaydınızda oluşturulmuş olan hesaplar yeni ödeme hesabında desteklenmez. Hesabın abonelikleri, kendi bölümleri için ilgili fatura bölümüne aittir. Hesap sahipleri kendi fatura bölümleri için abonelikleri oluşturabilir ve yönetebilir.

Hesaba ait olan aboneliklerin toplam maliyetini görüntülemek için her aboneliğe bir maliyet merkezi ayarlamalısınız. Ardından Azure kullanım ve ücretler csv dosyasını kullanarak abonelikleri maliyet merkezine göre filtreleyebilirsiniz.

### <a name="download-usage-and-charges-csv-price-sheet-and-tax-documents"></a>Kullanım ve ücretler csv dosyasını, fiyat listesini ve vergi belgelerini indirme

Ödeme hesabınızdaki her faturalama profili için bir aylık fatura oluşturulur. Her fatura için Azure kullanım ve ücretler csv dosyasını, fiyat listesini ve vergi belgesini (varsa) indirebilirsiniz. Ayrıca geçerli ayın ücretleri için de Azure kullanım ve ücretler csv dosyasını indirebilirsiniz.

Azure kullanım ve ücretler csv dosyasını indirmeyi öğrenmek için bkz. [Microsoft Müşteri Sözleşmeniz için kullanımı indirme](billing-download-azure-daily-usage.md).

Fiyat listesini indirmeyi öğrenmek için bkz. [Microsoft Müşteri Sözleşmeniz için fiyatlandırmayı indirme](billing-ea-pricing.md).

Vergi belgelerini indirmeyi öğrenmek için bkz. [Microsoft Müşteri Sözleşmeniz için vergi belgelerini görüntüleme](billing-mca-download-tax-document.md#view-and-download-tax-documents).

### <a name="add-an-additional-enterprise-administrator"></a>Bir kuruluş yöneticisi daha ekleme

Kullanıcılara kuruluşunuzda faturalamayı görüntülemeleri ve yönetmeleri için faturalama profiline erişim verin. Erişim vermek için Azure portalındaki **Erişim Denetimi (IAM)** sayfasını kullanabilirsiniz.  Faturalama profili rolleri hakkında daha fazla bilgi edinmek için bkz. [Faturalama profili rolleri ve görevleri](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

Faturalama profilinize erişim sağlamayı öğrenmek için bkz. [Azure portalında faturalama rollerini yönetme](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

### <a name="create-a-new-department"></a>Yeni bölüm oluşturma

Gereksinimleriniz temelinde maliyetlerinizi düzenlemek için Kurumsal Anlaşma kaydınızdaki bölümlere benzer bir fatura bölümü oluşturun. Yeni fatura bölümünü Azure portalında oluşturabilirsiniz. Daha fazla bilgi edinmek için bkz. [Maliyetlerinizi düzenlemek için faturanızda bölümler oluşturma](billing-mca-section-invoice.md).

### <a name="create-a-new-account"></a>Yeni hesap oluşturma

Kurumsal Anlaşma kaydında oluşturulan hesaplar gibi, kullanıcılara Azure aboneliği oluşturma izni vermek için fatura bölümleri üzerinde Azure aboneliği oluşturma rolü atayın. Rol atama hakkında daha fazla bilgi için bkz. [Azure portalındaki faturalandırma rollerini yönetme](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)

## <a name="changes-for-department-administrators"></a>Bölüm yöneticileri açısından değişiklikler

Aşağıdaki değişiklikler Microsoft Müşteri Sözleşmesi'ne yenilenmiş olan Kurumsal Anlaşmanın bölüm yöneticileri için geçerlidir.

- Kurumsal Anlaşma kaydınızdaki her bölüm için bir fatura bölümü oluşturulur. Kuruluşunuzdaki bölümleri yönetmek için fatura bölümlerini kullanacaksınız. Fatura bölümleri hakkında daha fazla bilgi edinmek için [fatura bölümlerini anlama](billing-mca-overview.md#invoice-sections) konusuna bakın.
- Kurumsal Anlaşma kaydında oluşturulan hesaplar gibi, diğerlerine Azure aboneliği oluşturma izni vermek için fatura bölümünde Azure aboneliği oluşturma rolünü kullanacaksınız.
- Kuruluşunuzun faturalamasını yönetmek için Azure EA portalı yerine Azure portalını kullanacaksınız.

Yeni ödeme hesabında size aşağıdaki roller verilir:

**Fatura bölümü sahibi** - Kurumsal Anlaşmanızdaki bölümler için oluşturulan fatura bölümünde size fatura bölümü sahibi rolü atanır. Bu rol, fatura bölümü için ücretleri görüntülemenizi ve izlemenizi, kimlerin Azure abonelikleri oluşturabileceğini ve başka ürünler satın alabileceğini denetlemenizi sağlar.

### <a name="view-charges-for-your-department"></a>Bölümünüz için ücretleri görüntüleme

Azure portalının [Maliyet Yönetimi + Faturalama sayfasında](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/Overview) bölümünüz için oluşturulmuş fatura bölümünün ücretlerini görüntüleyebilirsiniz.

### <a name="add-an-additional-department-administrator"></a>Bir bölüm yöneticisi daha ekleme

Daha önce Kurumsal Anlaşmanızda bulunan her bölüm için bir fatura bölümü oluşturulur. Azure portalının **Erişim Denetimi (IAM)** sayfasını kullanarak diğer kullanıcılara fatura bölümünü görüntüleme ve yönetme erişimi verebilirsiniz. Fatura bölümü rolleri hakkında daha fazla bilgi edinmek için bkz. [Fatura bölümü rolleri ve görevleri](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).

Fatura bölümünüze erişim sağlamayı öğrenmek için bkz. [Azure portalında faturalama rollerini yönetme](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

### <a name="create-a-new-account-in-your-department"></a>Bölümünüzde yeni hesap oluşturma

Bölümünüz için oluşturulan fatura bölümü üzerinde kullanıcılara Azure aboneliği oluşturucu rolü atayın. Rol atama hakkında daha fazla bilgi için bkz. [Azure portalındaki faturalandırma rollerini yönetme](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)

### <a name="view-charges-for-accounts-in-your-departments"></a>Bölümlerinizdeki hesaplar için ücretleri görüntüleme

Kurumsal Anlaşma kaydınızda oluşturulmuş olan hesaplar yeni ödeme hesabında desteklenmez. Hesabın abonelikleri, kendi bölümleri için ilgili fatura bölümüne aittir. Hesap sahipleri kendi fatura bölümleri için abonelikleri oluşturabilir ve yönetebilir.

Bölümünüzdeki bir hesaba ait olan aboneliklerin toplam maliyetini görüntülemek için her aboneliğe bir maliyet merkezi ayarlamalısınız. Ardından Azure kullanım ve ücretler dosyasını kullanarak abonelikleri maliyet merkezine göre filtreleyebilirsiniz.

## <a name="changes-for-account-owners"></a>Hesap sahipleri için değişiklikler

Kurumsal Anlaşmadaki hesap sahipleri yeni ödeme hesabında Azure abonelikleri oluşturma izni alır. Mevcut Azure abonelikleriniz, bölümünüz için oluşturulan fatura bölümüne aittir. Hesabınız bir bölüme ait değilse abonelikleriniz Varsayılan fatura bölümü olarak adlandırılan fatura bölümüne ait olur.

Ek Azure abonelikleri oluşturmak için yeni ödeme hesabında size aşağıdaki rol verilir.

**Azure aboneliği oluşturucusu** - Kurumsal Anlaşmada bölümünüz için oluşturulan fatura bölümü üzerinde size Azure aboneliği oluşturucusu rolü atanır. Hesabınız bir bölüme ait değilse Varsayılan fatura bölümü olarak adlandırılan bölüm üzerinde Azure aboneliği oluşturucusu rolü alırsınız. Bu rol fatura bölümü için Azure abonelikleri oluşturmanızı sağlar.

### <a name="create-an-azure-subscription"></a>Azure aboneliği oluşturma

Azure portalında fatura bölümünüz için Azure abonelikleri oluşturabilirsiniz. Daha fazla bilgi için bkz. [Microsoft Müşteri Sözleşmesi için ek Azure aboneliği oluşturma](billing-mca-create-subscription.md)

### <a name="view-charges-for-your-account"></a>Hesabınız için ücretleri görüntüleme

Hesaba ait aboneliklerin ücretlerini görüntülemek için Azure portalında [abonelikler sayfasına](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) gidin. Abonelikler sayfasında tüm aboneliğiniz için ücretler görüntülenir.

### <a name="view-charges-for-a-subscription"></a>Aboneliğin ücretlerini görüntüleme

Aboneliğin ücretlerini [abonelikler sayfasında](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) veya Azure maliyet analizinde görüntüleyebilirsiniz. Azure maliyet analiziyle ilgili daha fazla bilgi için [Maliyet analiziyle maliyetleri keşfetme ve analiz etme](../cost-management/quick-acm-cost-analysis.md) konusuna bakın.

## <a name="need-help-contact-support"></a>Yardıma mı ihtiyacınız var? Desteğe başvurun

Yardıma ihtiyacınız varsa sorununuzun hızla çözülmesini sağlamak için [desteğe başvurun](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="next-steps"></a>Sonraki adımlar

- [Microsoft Müşteri Sözleşmesi için ödeme hesabını anlama](billing-mca-overview.md)
- [Faturanızı anlama](billing-understand-your-bill.md)
- [Faturanızı anlama](billing-understand-your-invoice.md)
- [Diğer kullanıcılardan Azure aboneliklerinin fatura sahipliğini alma](billing-mca-request-billing-ownership.md)
