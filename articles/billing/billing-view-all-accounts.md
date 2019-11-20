---
title: Azure portalında ödeme hesaplarınızı görüntüleme | Microsoft Docs
description: Azure portalında ödeme hesaplarınızın nasıl görüntüleneceğini öğrenin.
services: ''
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 6df787dbc06f7ee56ba9a26c8382396bf3b6e74a
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71719589"
---
# <a name="view-billing-accounts-in-azure-portal"></a>Azure portalında ödeme hesaplarını görüntüleme  

Azure’ı kullanmak için kaydolduğunuzda bir ödeme hesabı oluşturulur. Faturalarınızı, ödemelerinizi yönetmek ve maliyetleri izlemek için ödeme hesabınızı kullanırsınız. Birden çok ödeme hesabına erişiminiz olabilir. Örneğin, kişisel projeleriniz için Azure’a kaydolmuş olabilirsiniz. Ayrıca kuruluşunuzun Kurumsal Anlaşması veya Microsoft Müşteri Sözleşmesi aracılığıyla da erişiminiz olabilir. Bu senaryoların her biri için ayrı bir ödeme hesabınız olur.

Azure portal şu anda aşağıdaki ödeme hesapları türünü destekler:

- **Microsoft Online Services Programı**: Azure web sitesi üzerinden Azure’a kaydolduğunuzda, Microsoft Online Services Programı için bir ödeme hesabı oluşturulur. Örneğin, bir [Ücretsiz Azure Hesabına](https://azure.microsoft.com/offers/ms-azr-0044p/), [kullandıkça öde fiyatlarına tabi hesaba](https://azure.microsoft.com/offers/ms-azr-0003p/) veya [Visual Studio abonesi](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/) olarak kaydolduğunuzda.

- **Kurumsal Anlaşma**: Kuruluşunuz Azure’ı kullanmak için bir [Kurumsal Anlaşma (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) imzaladığında Kurumsal Anlaşma için bir ödeme hesabı oluşturulur.

- **Microsoft Müşteri Sözleşmesi**: Kuruluşunuz bir Microsoft Müşteri Sözleşmesi imzalamak için Microsoft temsilcisiyle çalıştığında Microsoft Müşteri Sözleşmesi için bir ödeme hesabı oluşturulur. Azure web sitesinden [kullandıkça öde fiyatlarına tabi hesaba](https://azure.microsoft.com/offers/ms-azr-0003p/) kaydolan veya [Ücretsiz Azure Hesabını](https://azure.microsoft.com/offers/ms-azr-0044p/) yükselten, belirli bölgelerdeki bazı müşterilerin Microsoft Müşteri Sözleşmesi için ödeme hesabı da olabilir. Daha fazla bilgi için bkz. [Microsoft Müşteri Sözleşmesi için ödeme hesabınızı kullanmaya başlama](billing-mca-overview.md).

<!--Todo Add section to identify the type of accounts -->

## <a name="scopes-for-billing-accounts"></a>Ödeme hesapları için kapsamlar
Kapsam, kullanıcıların faturalamayı görüntülemek ve yönetmek için kullandığı ödeme hesabı içindeki bir düğümdür. Kullanıcılar bu kapsam dahilinde faturalama verilerini, ödemeleri, faturaları yönetir ve genel hesap yönetimi gerçekleştirir. 

### <a name="microsoft-online-services-program"></a>Microsoft Online Services Programı

|Kapsam  |Tanım  |
|---------|---------|
|Fatura hesabı     | Bir veya daha fazla Azure aboneliği için tek bir sahibi (Hesap yöneticisi) temsil eder. Hesap Yöneticisi, abonelik oluşturma, faturaları görüntüleme veya aboneliklerin faturalamasını değiştirme gibi çeşitli faturalama görevlerini gerçekleştirme yetkisine sahiptir.  |
|Abonelik     |  Bir Azure kaynakları gruplamasını temsil eder. Fatura bu kapsamda oluşturulur. Faturasını ödemek için kullanılan kendi ödeme yöntemlerine sahiptir.|


### <a name="enterprise-agreement"></a>Kurumsal Anlaşma

|Kapsam  |Tanım  |
|---------|---------|
|Fatura hesabı    | Bir Kurumsal Anlaşma kaydını temsil eder. Fatura bu kapsamda oluşturulur. Bölümler ve kayıt hesapları kullanılarak yapılandırılır.  |
|Bölüm     |  İsteğe bağlı kayıt hesapları gruplaması.      |
|Kayıt hesabı     |  Tek bir hesap sahibini temsil eder. Azure abonelikleri bu kapsam altında oluşturulur.  |


### <a name="microsoft-customer-agreement"></a>Microsoft Müşteri Sözleşmesi

|Kapsam  |Görevler  |
|---------|---------|
|Fatura hesabı     |   Birden çok Microsoft ürünü ve hizmeti için bir müşteri sözleşmesini temsil eder. Faturalama profilleri ve fatura bölümleri kullanılarak yapılandırılır.   |
|Faturalama profili     |  Bir faturayı ve ödeme yöntemlerini temsil eder. Fatura bu kapsamda oluşturulur. Birden çok fatura bölümüne sahip olabilir.      |
|Fatura bölümü     |   Faturadaki bir maliyet grubunu temsil eder. Abonelikler ve diğer satın alma işlemleri bu kapsamla ilişkilidir.    |


## <a name="switch-billing-scope-in-the-azure-portal"></a>Azure portalında faturalama kapsamını değiştirme


1. [Azure Portal](https://portal.azure.com) oturum açın.

2. **Maliyet Yönetimi + Faturalama** araması yapın.

   ![Azure portalı aramasını gösteren ekran görüntüsü](./media/billing-view-all-accounts/billing-search-cost-management-billing.png)

3. Soldaki **Tüm faturalama kapsamları**’nı seçin.

   ![Tüm faturalama kapsamlarını gösteren ekran görüntüsü](./media/billing-view-all-accounts/billing-list-of-accounts.png)

   ** Yalnızca bir kapsama erişiminiz varsa **Tüm faturalama kapsamlarını** görmezsiniz.

4. Ayrıntıları görüntülemek için bir kapsam seçin.



## <a name="need-help-contact-us"></a>Yardıma mı ihtiyacınız var? Bize ulaşın.

Sorularınız varsa ya da yardıma gereksinim duyuyorsanız [destek isteği oluşturun](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Sonraki adımlar
- [Maliyetlerinizi analiz etmeye](../cost-management/quick-acm-cost-analysis.md) nasıl başlayacağınızı öğrenin.