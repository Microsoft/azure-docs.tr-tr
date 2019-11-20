---
title: Microsoft İş Ortağı Sözleşmesi (MPA) için Azure aboneliklerinin fatura sahipliğini alma
description: Diğer kullanıcılardan Azure aboneliklerinin fatura sahipliğini istemeyi öğrenin.
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2019
ms.author: banders
ms.openlocfilehash: 312808517c80041cb45e2e8ac46566c1f59884e1
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72376799"
---
# <a name="get-billing-ownership-of-azure-subscriptions-to-your-mpa-account"></a>Azure aboneliklerinin fatura sahipliğini MPA hesabınıza alma

Yönetilen hizmetler ve Azure kullanımı için tek bir birleşik fatura sağlamak amacıyla, Bulut Çözümü Sağlayıcısı (CSP) Azure aboneliklerinin fatura sahipliğini Kurumsal Anlaşma’ya (EA) sahip müşterilerinden devralabilir.

Bu özellik yalnızca [Azure Uzmanı MSP](https://partner.microsoft.com/membership/azure-expert-msp) olduğu onaylanmış CSP Doğrudan Fatura İş Ortakları tarafından kullanılabilir. Microsoft idare ve ilkelerine tabidir ve belirli müşteriler için gözden geçirme ve onay gerekebilir.

Fatura sahipliğini istemek için **Genel Yönetici** veya **Yönetici Aracıları** rolüne sahip olmanız gerekir. Daha fazla bilgi için bkz. [İş Ortağı Merkezi - Kullanıcı rollerini ve izinleri atama](https://docs.microsoft.com/partner-center/permissions-overview).

Bu makale, Microsoft İş Ortağı Sözleşmesi ödeme hesapları için geçerlidir. Bu hesaplar, Bulut çözümü Sağlayıcılarının (CSP) yeni ticaret deneyiminde müşterilerinin faturalarını yönetmesi için oluşturulmuştur. Yeni deneyim yalnızca bir Microsoft Müşteri Sözleşmesini (MCA) kabul etmiş ve bir Azure Planına sahip olan en az bir müşteriye sahip iş ortakları tarafından kullanılabilir. [Microsoft İş Ortağı Sözleşmesi’ne erişiminizin olup olmadığını denetleyin](#check-access-to-a-microsoft-partner-agreement).

## <a name="prerequisites"></a>Ön koşullar

1. Müşteriyle [kurumsal bayi ilişkisi](https://docs.microsoft.com/partner-center/request-a-relationship-with-a-customer) oluşturun. Hem müşterinin hem de İş Ortağı kiracısının aynı yetkili bölgelerde bulunduğundan emin olmak için [CSP Bölgesel Yetkilerine Genel Bakış](https://docs.microsoft.com/partner-center/regional-authorization-overview) makalesine göz atın.  

2. [Müşterinin Microsoft Müşteri Sözleşmesini kabul ettiğiniz doğrulayın](https://docs.microsoft.com/partner-center/confirm-customer-agreement).

3. Müşteri için [Azure planı](https://docs.microsoft.com/partner-center/purchase-azure-plan) oluşturun. Müşteri birden çok kurumsal bayi üzerinden satın alıyorsa, her müşteri ve kurumsal bayi birleşimi için ayrı bir Azure planı oluşturmanız gerekir.

## <a name="request-billing-ownership"></a>Fatura sahipliğini isteme

1. CSP Yönetici Aracısı kimlik bilgilerini kullanarak [Azure portalında](https://portal.azure.com) oturum açın.

2. **Maliyet Yönetimi + Faturalama** araması yapın.

   ![Azure portalında maliyet yönetimi + faturalama aramasını gösteren ekran görüntüsü](./media/mpa-request-ownership/search-cmb.png)

3. Sol taraftan **Müşteriler**’i seçin ve sonra listeden bir müşteri belirleyin.
   
   ![Müşterilerin seçilmesini gösteren ekran görüntüsü](./media/mpa-request-ownership/mpa-select-customers.png)        

4. Sol alt köşeden **Aktarım istekleri**’ni ve ardından **Yeni istek ekle**’yi seçin.
 
   ![Aktarım isteklerinin seçilmesini gösteren ekran görüntüsü](./media/mpa-request-ownership/mpa-select-transfer-requests.png)

5. Müşteri kuruluşunda, aktarım isteğini kabul edecek kullanıcının e-posta adresini girin. Kullanıcının bir Kurumsal Anlaşma hesap sahibi olması gerekir. **Aktarım isteği gönder**’i seçin.

   ![Aktarım isteği gönderme işlemini gösteren ekran görüntüsü](./media/mpa-request-ownership/mpa-send-transfer-requests.png)

6. Kullanıcı, aktarma isteğinizi gözden geçirme yönergelerini içeren bir e-posta alır.

   ![Aktarım isteğini gözden geçirme e-postasını gösteren ekran görüntüsü](./media/mpa-request-ownership/mpa-review-transfer-request-email.png)

7. Kullanıcı, aktarma isteğini onaylamak için e-postadaki bağlantıyı seçer ve yönergeleri izler.

    ![Aktarım isteğini gözden geçirme e-postasını gösteren ekran görüntüsü](./media/mpa-request-ownership/mpa-review-transfer-request.png)

## <a name="check-the-transfer-request-status"></a>Aktarım isteğinin durumunu denetleme

1. [Azure Portal](https://portal.azure.com) oturum açın.

2. **Maliyet Yönetimi + Faturalama** araması yapın.

   ![Azure portalında maliyet yönetimi + faturalama aramasını gösteren ekran görüntüsü](./media/billing-mca-request-billing-ownership/billing-search-cost-management-billing.png)

3. Sol taraftan **Müşteriler**’i seçin.

   ![Müşterilerin seçilmesini gösteren ekran görüntüsü](./media/mpa-request-ownership/mpa-select-customers.png)

4. Listeden aktarım isteğini gönderdiğiniz müşteriyi seçin.

5. Sol alt taraftaki **Aktarım istekleri**'ni seçin. Aktarım istekleri sayfasında aşağıdaki bilgiler görüntülenir:

    ![Aktarım isteklerinin listesini gösteren ekran görüntüsü](./media/mpa-request-ownership/mpa-select-transfer-requests-for-status.png)

   |Sütun|Tanım|
   |---------|---------|
   |İstek tarihi|Aktarım isteğinin gönderildiği tarih|
   |Alıcı|Fatura sahipliğini aktarması için istek gönderdiğiniz kullanıcının e-posta adresi|
   |Son kullanma tarihi|İsteğin süresinin dolacağı tarih|
   |Durum|Aktarım isteğinin durumu|

    Aktarım isteğinin durumu aşağıdakilerden biri olabilir:

   |Durum|Tanım|
   |---------|---------|
   |Devam ediyor|Kullanıcı aktarım isteğini kabul etmedi|
   |İşleniyor|Kullanıcı aktarım isteğini onayladı. Kullanıcının seçtiği aboneliklerin ödemesi sizin hesabınıza aktarılıyor|
   |Tamamlandı| Kullanıcının seçtiği aboneliklerin ödemesi sizin hesabınıza aktarılır|
   |Hatalarla tamamlandı|İstek tamamlandı ama kullanıcının seçtiği aboneliklerden bazılarının ödemesi aktarılamadı|
   |Süresi doldu|Kullanıcı belirtilen süre içinde isteği kabul etmediği için isteğin süresi doldu|
   |İptal edildi|Aktarım isteğine erişimi olan birisi isteği iptal etti|
   |Reddedildi|Kullanıcı aktarım isteğini reddetti|

6. Ayrıntılarını görüntülemek için bir aktarım isteği seçin. Aktarım ayrıntıları sayfasında aşağıdaki bilgiler görüntülenir: ![Aktarılan aboneliklerin listesini gösteren ekran görüntüsü](./media/mpa-request-ownership/mpa-transfer-completed.png)

   |Sütun  |Tanım|
   |---------|---------|
   |Aktarım isteği kimliği|Aktarım isteğinizin benzersiz kimliği. Destek isteği gönderiyorsanız, destek isteğinizi hızlandırmak için Azure desteğiyle bu kimliği paylaşın|
   |Aktarım istenme tarihi|Aktarım isteğinin gönderildiği tarih|
   |Aktarım şu kişi tarafından istendi|Aktarım isteğini gönderen kullanıcının e-posta adresi|
   |Aktarım İsteğinin son kullanma tarihi| Aktarım isteğinin süresinin dolacağı tarih|
   |Alıcının e-posta adresi|Fatura sahipliğini aktarması için istek gönderdiğiniz kullanıcının e-posta adresi|
   |Alıcıya gönderilen aktarım bağlantısı|Aktarım isteğini gözden geçirmesi için kullanıcıya gönderilen URL|

## <a name="supported-subscription-types"></a>Desteklenen abonelik türleri

Aşağıda listelenen abonelik türlerinin fatura sahipliğini isteyebilirsiniz.

- [Kurumsal Geliştirme ve Test](https://azure.microsoft.com/offers/ms-azr-0148p/)\* 
- [Microsoft Kurumsal Anlaşma](https://azure.microsoft.com/pricing/enterprise-agreement/)

\* Kurumsal DevTest aboneliği, aktarım sonrasındaki kullandıkça öde fiyatından faturalandırılır ve Kurumsal DevTest teklifi ile müşterinin Kurumsal Anlaşmasında sunulan indirimler CSP iş ortağı tarafından kullanılamaz.

## <a name="additional-information"></a>Ek bilgiler

Aşağıdaki bölümde, abonelik aktarma hakkında ek bilgiler sağlanır.

### <a name="no-service-downtime"></a>Hizmet kapalı kalma süresi yoktur

Abonelikteki Azure hizmetleri kesintisiz olarak çalışmaya devam eder. Yalnızca kullanıcının aktarmayı seçtiği Azure aboneliklerinin faturalama ilişkisini geçiririz.

### <a name="disabled-subscriptions"></a>Devre dışı bırakılan abonelikler

Devre dışı bırakılan abonelikler aktarılamaz. Fatura sahipliğini aktarmak için aboneliğin etkin durumda olması gerekir.

### <a name="azure-resources-transfer"></a>Azure kaynakları aktarımı

VM’ler, diskler ve web siteleri aktarımı gibi aboneliklerden gelen tüm kaynaklar.

### <a name="azure-marketplace-products-transfer"></a>Azure Market ürünleri aktarımı

Bulut Çözümü Sağlayıcıları (CSP) tarafından yönetilen abonelikler için kullanılabilen Azure Market ürünleri, ilgili abonelikleriyle birlikte aktarılır. CSP'ler için etkinleştirilmemiş Azure Market ürünlerini içeren abonelikler aktarılamaz.

### <a name="azure-reservations-transfer"></a>Azure rezervasyonları aktarımı

Azure rezervasyonları abonelikle birlikte otomatik olarak taşınmaz. Rezervasyonları taşımak için [Azure desteğine başvurun](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="access-to-azure-services"></a>Azure hizmetlerine erişim

Mevcut kullanıcılar, gruplar veya hizmet sorumlularına [Azure RBAC (rol tabanlı erişim denetimi)](../role-based-access-control/overview.md) kullanılarak atanan erişim, aktarım sırasında etkilenmez. İş ortağı, aboneliklere yeni bir RBAC erişimi elde etmez.  

İş ortakları, aboneliklerle erişim elde etmek için müşteriyle birlikte çalışmalıdır.  İş ortaklarının [Adına Yönetici - AOBO](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) veya [Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/concepts/cloud-solution-provider) erişimi açık destek biletlerini alması gerekir.

### <a name="azure-support-plan"></a>Azure destek planınızı

Azure desteği aboneliklerle birlikte aktarılmaz. Kullanıcı tüm Azure aboneliklerini aktarıyorsa, destek planlarını iptal etmelerini isteyin. Aktarımdan sonra, destekten CSP iş ortağı sorumludur. Müşterinin herhangi bir destek isteği için CSP iş ortağı ile birlikte çalışması gerekir.  

### <a name="charges-for-transferred-subscription"></a>Aktarılan aboneliğin ücretleri

Aboneliklerin başlangıçtaki fatura sahibi, aktarımın tamamlandığı noktaya kadar bildirilen tüm ücretlerden sorumludur. Aktarım yapıldıktan sonra bildirilen ücretler sizin sorumluluğunuzdadır. Aktarımdan önce gerçekleşen ancak daha sonra bildirilen ücretler olabilir. Bu ücretler faturanızda gösterilir.

### <a name="cancel-a-transfer-request"></a>Aktarım isteğini iptal etme

Aktarım isteğini, istek onaylanana veya reddedilene kadar iptal edebilirsiniz. Aktarım isteğini iptal etmek için [aktarım ayrıntıları sayfasına](#check-the-transfer-request-status) gidin ve sayfanın altından iptal düğmesini seçin.

### <a name="software-as-a-service-saas-transfer"></a>Hizmet Olarak Yazılım (SaaS) aktarımı

SaaS ürünleri aboneliklerle birlikte aktarılmaz. SaaS ürünlerinin fatura sahipliğini aktarmak için kullanıcının [Azure desteğine başvurmasını](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) isteyin. Kullanıcı, fatura sahipliğiyle birlikte kaynak sahipliğini de aktarabilir. Kaynak sahipliği, ürünü silme ve ayrıntılarını görüntüleme gibi yönetim işlemleri gerçekleştirmenizi sağlar. Kullanıcının kaynak sahipliğini aktarabilmesi için SaaS ürününde kaynak sahibi olması gerekir.

### <a name="additional-approval-for-certain-customers"></a>Belirli müşteriler için ek onay

Müşteri geçiş isteklerinden bazıları, müşterinin geçerli kurumsal kayıt yapısının niteliği nedeniyle Microsoft ile birlikte ek bir gözden geçirme işlemi gerektirebilir. Müşterilere davet göndermeye çalışılırken bu gereksinimler iş ortağına bildirilir. İş ortaklarının bu gözden geçirme işlemini tamamlaması için İş Ortağı Geliştirme Yöneticisi ve Müşterinin hesap ekibiyle birlikte çalışması istenir.

### <a name="azure-subscription-directory"></a>Azure aboneliği dizini 
Aktarılan Azure aboneliklerinin dizini, CSP ilişkisi oluşturulurken seçilen müşterinin diziniyle eşleşmelidir. 

Bu iki dizin eşleşmezse abonelikler aktarılamaz. Azure aboneliklerinin dizinini seçerek veya Azure aboneliklerinin dizinini müşteri CSP ilişkisi diziniyle eşleşecek şekilde değiştirerek müşteriyle yeni bir CSP satıcı ilişkisi oluşturmanız gerekir. Daha fazla bilgi için bkz. [Var olan bir aboneliği Azure AD dizininizle ilişkilendirme](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-how-subscriptions-associated-directory#to-associate-an-existing-subscription-to-your-azure-ad-directory).


## <a name="check-access-to-a-microsoft-partner-agreement"></a>Microsoft İş Ortağı Sözleşmesi’ne erişimi denetleme
[!INCLUDE [billing-check-mpa](../../includes/billing-check-mpa.md)]

## <a name="need-help-contact-support"></a>Yardıma mı ihtiyacınız var? Desteğe başvurun

Yardıma ihtiyacınız varsa sorununuzun hızla çözülmesini sağlamak için [desteğe başvurun](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="next-steps"></a>Sonraki adımlar

- Azure aboneliklerinin fatura sahipliği size aktarıldı. Bu aboneliklerin ücretlerini [Azure portalından](https://portal.azure.com) takip edin.

- Aktarılan Azure aboneliklerine erişim elde etmek için müşteriyle birlikte çalışın. [RBAC kullanarak Azure kaynaklarına erişimi yönetme](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).
