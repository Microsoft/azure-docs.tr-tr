---
title: Azure aboneliğinin faturalama sahipliğini başka bir hesaba aktarma | Microsoft Docs
description: Bir Azure aboneliğinin faturalama sahipliğini başka bir hesaba aktarma işlemini ve işlemle ilgili bazı sık sorulan soruları (SSS) açıklar
keywords: azure aboneliğini aktarma, azure aboneliği aktarma, azure aboneliğini başka bir hesaba taşıma, azure, abonelik sahibini değiştirme, azure aboneliğini başka bir hesaba aktarma, azure faturalandırma aktarma
author: bandersmsft
manager: amberb
tags: billing,top-support-issue
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: beef37b0ece9cf57c86927b9f57d85ad4a25dbd1
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73746324"
---
# <a name="transfer-billing-ownership-of-an-azure-subscription-to-another-account"></a>Azure aboneliğinin faturalama sahipliğini başka bir hesaba aktarma

Kuruluşunuzdan ayrılacaksanız veya aboneliğinizin faturasının başka bir hesaba yansıtılmasını istiyorsanız Azure aboneliğinizin faturalama sahipliğini aktarmak isteyebilirsiniz. Faturalama sahipliğini başka bir hesaba aktarmak, yeni hesaptaki yöneticilere ödeme yöntemini değiştirme, ücretleri görüntüleme ve aboneliği iptal etme gibi faturalandırma görevlerini gerçekleştirmek için izinler sağlar.

Faturalama sahipliğini değiştirmeyip aboneliğinizin türünü değiştirmek istiyorsanız bkz. [Azure aboneliğinizi başka bir teklifle değiştirme](billing-how-to-switch-azure-offer.md). Abonelikteki kaynakları kimlerin yönetebileceğini denetlemek istiyorsanız bkz. [Azure kaynakları için yerleşik roller](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

Kurumsal Anlaşma (EA) müşterisiyseniz kurumsal yöneticileriniz aboneliklerinizin faturalama sahipliğini hesaplar arasında aktarabilir. Daha fazla bilgi için bkz. [Kurumsal Anlaşma (EA) aboneliklerinin faturalama sahipliğini aktarma](#transfer-billing-ownership-of-enterprise-agreement-ea-subscriptions).

## <a name="transfer-billing-ownership-of-an-azure-subscription"></a>Azure aboneliğinin faturalama sahipliğini aktarma

1. [Azure portalında](https://portal.azure.com), aktarmak istediğiniz aboneliği içeren ödeme hesabının yöneticisi olarak oturum açın. Yönetici olup olmadığınızı öğrenmek için bkz. [Sık sorulan sorular](#faq).

1. **Maliyet Yönetimi + Faturalama** araması yapın.

   ![Azure portalı aramasını gösteren ekran görüntüsü](./media/billing-subscription-transfer/billing-search-cost-management-billing.png)

1. Sol bölmeden **Abonelikler**’i seçin. Erişim izninize bağlı olarak faturalama kapsamını ve ardından **Abonelikler**'i veya **Azure abonelikleri**'ni seçmeniz gerekebilir.

1. Aktarmak istediğiniz abonelik için **Faturalama sahipliğini aktar**'ı seçin.

   ![Aktarılacak aboneliği seçin](./media/billing-subscription-transfer/billing-select-subscription-to-transfer.png)

1. Aboneliğin yeni sahibi olacak hesabın faturalama yöneticisi olan kullanıcının e-posta adresini girin.

1. Aboneliğinizi başka bir Azure AD kiracısındaki bir hesaba aktarıyorsanız, aboneliği yeni hesabın kiracısına taşımak isteyip istemediğinizi seçin. Daha fazla bilgi için bkz. [Aboneliği başka Azure AD kiracısındaki bir hesaba aktarma](#transferring-subscription-to-an-account-in-another-azure-ad-tenant)

    > [!IMPORTANT]
    >
    > Aboneliği yeni hesabın Azure AD kiracısına taşımayı seçerseniz, abonelikteki kaynakları yönetmeye yönelik tüm [rol tabanlı erişim denetimi (RBAC)](../role-based-access-control/overview.md) atamaları kalıcı olarak kaldırılır. Yalnızca aktarım isteğinizi kabul eden yeni hesaptaki kullanıcı, abonelikteki kaynakları yönetmek için erişime sahip olur. Daha fazla bilgi için bkz. [Aboneliği başka Azure AD kiracısındaki bir kullanıcıya aktarma](../active-directory/managed-identities-azure-resources/known-issues.md). Alternatif olarak, aboneliği yeni hesabın kiracısına taşımadan faturalama sahipliğini aktarmak için Abonelik Azure AD kiracısının kutusunun işaretini kaldırabilirsiniz. Bunu yaparsanız, Azure kaynaklarını yönetmek için mevcut RBAC izinleri korunur.

    ![Aktarım gönder sayfası](./media/billing-subscription-transfer/billing-send-transfer-request.PNG)

1. **Aktarım isteği gönder**’i seçin.

1. Kullanıcı, aktarma isteğinizi gözden geçirme yönergelerini içeren bir e-posta alır.

   ![Alıcıya gönderilen abonelik aktarım e-postası](./media/billing-subscription-transfer/billing-receiver-email.png)

1. Kullanıcı, aktarma isteğini onaylamak için e-postadaki bağlantıyı seçer ve yönergeleri izler. Kullanıcının abonelik ödemesini yapmak için kullanılacak bir ödeme yöntemi seçmesi gerekir. Ayrıca, kullanıcının bir Azure hesabı yoksa yeni bir hesap için kaydolması gerekir.

   ![İlk abonelik aktarımı web sayfası](./media/billing-subscription-transfer/billing-accept-ownership-step1.png)

   ![İkinci abonelik aktarımı web sayfası](./media/billing-subscription-transfer/billing-accept-ownership-step2.png)

   ![İkinci abonelik aktarımı web sayfası](./media/billing-subscription-transfer/billing-accept-ownership-step3.png)

1. Başarılı! Abonelik aktarıldı.

## <a name="transferring-subscription-to-an-account-in-another-azure-ad-tenant"></a>Aboneliği başka Azure AD kiracısındaki bir hesaba aktarma

Azure'a kaydolduğunuzda sizin için bir Azure Active Directory (AD) kiracısı oluşturulur. Kiracı, hesabınızı temsil eder. Kiracıyı, aboneliklerinize ve kaynaklarınıza erişimi yönetmek için kullanırsınız.

Yeni bir abonelik oluşturduğunuzda hesabınızın Azure AD kiracısında barındırılır. Aboneliğinize veya kaynaklarınıza erişmesi için başkalarına erişim izni vermek isterseniz kiracınıza katılmaya davet etmeniz gerekir. Bunun yapılması, aboneliklerinize ve kaynaklarınıza erişimi denetlemenize yardımcı olur.

Aboneliğinizin faturalama sahipliğini başka bir Azure AD kiracısındaki bir hesaba aktarırken aboneliği yeni hesabın kiracısına taşıyabilirsiniz. Bunu yaparsanız, abonelikleri ve kaynaklarını yönetmek için [rol tabanlı erişim (RBAC)](../role-based-access-control/role-assignments-portal.md) izni olan tüm kullanıcılar, gruplar veya hizmet sorumluları erişimlerini kaybeder. Yalnızca aktarım isteğinizi kabul eden yeni hesaptaki kullanıcı, kaynakları yönetmek için erişime sahip olur. Başlangıçta erişim izni olan kullanıcılara erişim izni vermek için yeni sahibin [bu kullanıcıları aboneliğe elle eklemesi](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) gerekir.


## <a name="transferring-visual-studio-mpn-and-pay-as-you-go-devtest-subscriptions"></a>Visual Studio, MPN ve Kullandıkça Öde Geliştirme ve Test aboneliklerini aktarma

Visual Studio ve Microsoft İş Ortağı Ağı abonelikleriyle ilişkili aylık yinelenen Azure kredisi vardır. Bu abonelikleri aktardığınızda krediniz hedef ödeme hesabında kullanılamaz. Abonelik, hedef ödeme hesabındaki krediyi kullanır. Örneğin, Kemal bir Visual Studio Enterprise aboneliğini 9 Eylül tarihinde Gamze’nin hesabına aktardı ve Gamze aktarımı kabul etti. Aktarım tamamlandıktan sonra abonelik Gamze'nin hesabındaki krediyi kullanmaya başlar. Kredi her ayın 9. günü sıfırlanır.


<a id="EA"></a>

## <a name="transfer-billing-ownership-of-enterprise-agreement-ea-subscriptions"></a>Kurumsal Anlaşma (EA) aboneliklerinin faturalama sahipliğini aktarma

Kuruluş Yöneticisi, bir kayıt içindeki hesaplar arasında aboneliklerin sahipliğini farklı kişilere aktarabilir. Daha fazla bilgi için EA portalında [Hesap Sahipliğini Aktarma](https://ea.azure.com/helpdocs/changeAccountOwnerForASubscription) bölümüne bakın.

## <a name="next-steps-after-accepting-billing-ownership"></a>Faturalama sahipliğini kabul ettikten sonraki adımlar

Bir Azure aboneliğinin faturalama sahipliğini kabul ettiyseniz, sonraki adımları incelemenizi öneririz:

1. Hizmet Yöneticisi, Ortak Yöneticiler ve diğer RBAC rollerini gözden geçirin ve güncelleştirin. Daha fazla bilgi edinmek için bkz. [Azure abonelik yöneticileri ekleme veya değiştirme](billing-add-change-azure-subscription-administrator.md) ve [RBAC ve Azure portalını kullanarak erişimi yönetme](../role-based-access-control/role-assignments-portal.md).
1. Bu aboneliğin hizmetleriyle ilişkili kimlik bilgilerini şu şekilde güncelleştirin:
   1. Abonelik kaynaklarına kullanıcı yöneticisi hakları veren yönetim sertifikaları. Daha fazla bilgi için bkz. [Azure için yönetim sertifikası oluşturma ve karşıya yükleme](../cloud-services/cloud-services-certs-create.md)
   1. Depolama gibi hizmetler için erişim anahtarları. Daha fazla bilgi için bkz. [Azure depolama hesapları hakkında](../storage/common/storage-create-storage-account.md)
   1. Azure Sanal Makineleri gibi hizmetler için Uzaktan Erişim kimlik bilgileri.
1. Bir iş ortağıyla çalışıyorsanız, bu abonelikteki iş ortağı kimliğini güncelleştirmeyi göz önünde bulundurun. [Azure portalından](https://portal.azure.com) iş ortağı kimliğini güncelleştirebilirsiniz. Daha fazla bilgi için bkz. [İş ortağı kimliğini Azure hesaplarınıza bağlama](billing-partner-admin-link-started.md)

<a id="supported"></a>

## <a name="supported-subscription-types"></a>Desteklenen abonelik türleri

Azure portalında abonelik aktarımı aşağıda listelenen abonelik türleri için yapılabilir. Şu anda [Ücretsiz Deneme](https://azure.microsoft.com/offers/ms-azr-0044p/) veya [Open Lisansı ile Azure (AIO)](https://azure.microsoft.com/offers/ms-azr-0111p/) abonelikleri için aktarım desteklenmemektedir. Geçici bir çözüm için bkz. [Kaynakları yeni kaynak grubuna veya aboneliğe taşıma](../azure-resource-manager/resource-group-move-resources.md). [Sponsorluk](https://azure.microsoft.com/offers/ms-azr-0036p/) gibi diğer abonelikleri veya destek planlarını aktarmak için [Azure Desteği ile iletişime geçin](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

- [Kurumsal Anlaşma (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)\*
- [Microsoft İş Ortağı Ağı](https://azure.microsoft.com/offers/ms-azr-0025p/)  
- [Visual Studio Enterprise (MPN) aboneleri](https://azure.microsoft.com/offers/ms-azr-0029p/)
- [MSDN Platformları](https://azure.microsoft.com/offers/ms-azr-0062p/)  
- [Kullandıkça Öde](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Kullandıkça Öde Geliştirme ve Test](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)
- [Visual Studio Enterprise: BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p/)
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)
- [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)
- [Microsoft Azure Planı](https://azure.microsoft.com/offers/ms-azr-0017g/)\*\*

\* [EA portalı aracılığıyla](#EA).

\*\* Yalnızca Azure web sitesine kaydolurken oluşturulan hesaplar için desteklenir.

<a id="faq"></a>

## <a name="frequently-asked-questions-faq-for-senders"></a>Gönderenler ile ilgili sık sorulan sorular (SSS)

Bu sık sorulan sorular, bir Azure aboneliğinin faturalama sahipliğini başka bir hesaba aktaran kullanıcılar için geçerlidir.

### <a name="whoisaa"></a> Hesabın faturalama yöneticisi kimdir?

Faturalama yöneticisi, bir hesabın faturalandırmasını yönetme iznine sahip olan kişidir. [Azure portalında](https://portal.azure.com) faturalandırma işlemine erişmek ve abonelik oluşturma, faturaları görüntüleme ve ödeme ya da ödeme yöntemlerini güncelleştirme gibi çeşitli faturalama görevlerini gerçekleştirmek için yetkilidir.

Faturalama yöneticisi olduğunuz hesapları belirlemek için aşağıdaki adımları kullanın:

1. [Azure portalında Maliyet Yönetimi + Faturalama sayfasını](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/Overview) ziyaret edin.
1. Sol bölmeden **Tüm faturalama kapsamları**’nı seçin.
1. Abonelikler sayfasında faturalama yöneticisi olduğunuz tüm abonelikler listelenir.

Bir aboneliğin hesap yöneticisinin kim olduğunu bilmiyorsanız aşağıdaki adımları izleyerek bulabilirsiniz.

1. [Azure portalda Abonelikler sayfasını](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) ziyaret edin.
1. Denetlemek istediğiniz aboneliği seçin ve **Ayarlar** bölümüne bakın.
1. **Özellikler**’i seçin. Aboneliğin hesap yöneticisi, **Hesap Yöneticisi** kutusunda görüntülenir.

### <a name="does-everything-transfer-including-resource-groups-vms-disks-and-other-running-services"></a>Her şey aktarılır mı? Kaynak grupları, VM'ler, diskler ve diğer çalışan hizmetler de dahil mi?

Sanal Makineler, diskler ve web siteleri gibi tüm kaynaklarınız yeni hesaba aktarılır. Bununla birlikte, aboneliği başka bir Azure AD kiracısındaki bir hesaba aktarırsanız, abonelikteki hiçbir [yönetici rolü](billing-add-change-azure-subscription-administrator.md) ve [Rol Tabanlı Erişim Denetimi (RBAC)](../role-based-access-control/role-assignments-portal.md) ataması [aktarılmaz](#transferring-subscription-to-an-account-in-another-azure-ad-tenant). Ayrıca, [uygulama kayıtları](../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md) ve kiracıya özgü diğer hizmetler abonelikle birlikte aktarılmaz.

### <a name="can-i-transfer-ownership-to-an-account-in-another-country"></a>Sahipliği başka bir ülkedeki bir hesaba aktarabilir miyim?
Ne yazık ki, Azure portalında ülkeler arası aktarımlar gerçekleştirilemez. Aboneliğinizi ülkeler arasında aktarmak için [desteğe başvurun](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="i-am-an-administrator-on-two-accounts-can-i-transfer-a-subscription-from-one-of-my-accounts-to-another"></a>İki hesabın yöneticisiyim. Bir hesabımdan diğerine abonelik aktarabilir miyim?
Evet, hesaplarınız arasında abonelik aktarabilirsiniz. Hesaplarınız kavramsal olarak iki farklı kullanıcının hesabı olarak kabul edildiği için, yukarıdaki adımları kullanarak hesaplarınız arasında abonelikleri aktarabilirsiniz.

### <a name="does-a-subscription-transfer-result-in-any-service-downtime"></a>Abonelik aktarımı herhangi bir hizmette kesintiye neden olur mu?

Aboneliği aynı Azure AD kiracısında yer alan bir hesaba aktarırsanız abonelikte çalışan kaynaklar üzerinde hiçbir etkisi olmaz. Öte yandan aboneliği başka bir kiracıdaki bir hesaba aktarıp aboneliği kiracıya taşımaya karar verirseniz, abonelikteki kaynakları yönetmek için [rol tabanlı erişim denetimine (RBAC)](../role-based-access-control/overview.md) sahip olan tüm kullanıcılar, gruplar ve hizmet sorumluları erişimi kaybeder. Bunun yapılması hizmette kesintiye yol açabilir.

### <a name="do-users-in-new-account-have-access-to-usage-and-billing-history"></a>Yeni hesaptaki kullanıcılar kullanım ve fatura geçmişine erişebilir mi?

Yeni hesapta kullanıcılara yalnızca aboneliğinize ilişkin geçen ayın maliyet bilgileri sağlanır. Kullanım ve fatura geçmişinin kalan bölümü abonelikle birlikte aktarılmaz

### <a name="how-do-i-migrate-data-and-services-for-my-azure-subscription-to-new-subscription"></a>Azure aboneliğimin verilerini ve hizmetlerini nasıl yeni aboneliğe geçirebilirim?

Abonelik sahipliğini aktaramıyorsanız kaynaklarınızı elle geçirebilirsiniz. Bkz. [Kaynakları yeni kaynak grubuna veya aboneliğe taşıma](../azure-resource-manager/resource-group-move-resources.md).

### <a name="if-i-transfer-a-visual-studio-or-microsoft-partner-network-subscription-does-my-credit-carry-forward-with-the-subscription-in-the-new-account"></a>Bir Visual Studio veya Microsoft İş Ortağı Ağı aboneliğini aktarırsam kredim yeni hesaptaki abonelikle birlikte taşınır mı?

Hayır, krediniz yeni hesapta kullanılamaz. Aktarım isteğini kabul eden kullanıcının, aktarım isteğini kabul etmesi için bir Visual Studio lisansına sahip olması gerekir. Abonelik, kullanıcının hesabında bulunan Visual Studio kredisini kullanır. Daha fazla bilgi için bkz. [Visual Studio, Microsoft İş Ortağı Ağı (MPN) ve Kullandıkça Öde Geliştirme ve Test abonelikleri](#transferring-visual-studio-mpn-and-pay-as-you-go-devtest-subscriptions).


## <a name="frequently-asked-questions-faq-for-recipients"></a>Alıcılar ile ilgili sık sorulan sorular (SSS)

Bu sık sorulan sorular, bir Azure aboneliğinin faturalama sahipliğini başka bir hesaptan kabul eden kullanıcılar için geçerlidir.

### <a name="if-i-take-over-billing-ownership-of-a-subscription-from-another-account-do-users-in-that-account-continue-to-have-access-to-my-resources"></a>Başka bir hesaptan aboneliğin faturalama sahipliğini devralırsam, söz konusu hesaptaki kullanıcılar kaynaklarıma erişmeye devam eder mi?

Evet. Ancak, hesabınız aboneliğin kiracısından farklı bir Azure AD kiracısındaysa ve aktarım isteğini gönderen kullanıcı aboneliği sizin hesabınızın kiracısına taşırsa, tüm [yönetici rolleri](billing-add-change-azure-subscription-administrator.md) ve [Rol Tabanlı Erişim Denetimi (RBAC)](../role-based-access-control/role-assignments-portal.md) atamaları kaldırılır. Abonelikteki kaynakları yönetmek için [rol tabanlı erişim (RBAC)](../role-based-access-control/overview.md) izni olan kullanıcıları görüntülemek için aşağıdaki adımları kullanın:

1. [Azure portalında Abonelikler sayfasını](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) ziyaret edin.
1. Denetlemek istediğiniz aboneliği seçin ve ardından sol bölmedeki **Erişim denetimi (IAM)** seçeneğini belirleyin.
1. Sayfanın üst kısmından **Rol atamaları**'nı seçin. Rol atamaları sayfasında, abonelikte RBAC erişimi olan tüm kullanıcılar listelenir.

Aktarım sırasında [Rol Tabanlı Erişim Denetimi (RBAC)](../role-based-access-control/role-assignments-portal.md) atamaları kaldırılsa bile, ilk sahip hesabındaki kullanıcılar aşağıdaki gibi bazı güvenlik mekanizmaları üzerinden aboneliğe erişmeye devam edebilir:

* Abonelik kaynaklarına kullanıcı yöneticisi hakları veren yönetim sertifikaları. Daha fazla bilgi için bkz. [Azure için Yönetim Sertifikası Oluşturma ve Karşıya Yükleme](../cloud-services/cloud-services-certs-create.md).
* Depolama gibi hizmetler için erişim anahtarları. Daha fazla bilgi için bkz. [Azure depolama hesapları hakkında](../storage/common/storage-create-storage-account.md).
* Azure Sanal Makineleri gibi hizmetler için Uzaktan Erişim kimlik bilgileri.

Alıcının kaynaklarına erişimi kısıtlaması gerekiyorsa, hizmetle ilişkili tüm gizli dizileri güncelleştirmeyi düşünmelidir. Çoğu kaynak aşağıdaki adımlar kullanılarak güncelleştirilebilir:

  1. [Azure Portal](https://portal.azure.com) oturum açın.
  2. Merkez menüsünde **Tüm kaynaklar**'ı seçin.
  3. Kaynağı seçin.
  4. Kaynak sayfasında **Ayarlar**'a tıklayın. Buradan mevcut gizli dizileri görüntüleyebilir ve güncelleştirebilirsiniz.

### <a name="if-i-take-over-the-billing-ownership-of-a-subscription-in-the-middle-of-the-billing-cycle-do-i-have-to-pay-for-the-entire-billing-cycle"></a>Faturalama döneminin ortasında bir aboneliğin faturalama sahipliğini devralırsam tüm faturalama dönemi için ödeme yapmam gerekir mi?

Hesabınız aktarım zamanından itibaren raporlanan tüm kullanımlar için ödeme yapmaktan sorumludur. Aktarımdan önce gerçekleşen ancak daha sonra bildirilen kullanımlar olabilir. Kullanım, hesabınızın faturasına dahil edilir.

### <a name="can-i-use-a-different-payment-method"></a>Farklı bir ödeme yöntemi kullanabilir miyim?

Evet. Aktarım isteğini kabul ederken hesabınıza bağlı mevcut bir ödeme yöntemini seçebilir veya yeni bir ödeme yöntemi ekleyebilirsiniz.

### <a name="how-can-i-transfer-ownership-of-my-enterprise-agreement-ea-subscription-account-ownership-if-the-original-account-owner-is-no-longer-with-the-organization"></a>Özgün hesap sahibinin kuruluştan ayrılmış olması durumunda Kurumsal Anlaşma (EA) aboneliğimin sahipliğini nasıl aktarabilirim?

Özgün hesap sahibi kuruluştan ayrılmış olsa dahi Kuruluş Yöneticisi, tüm hesapların sahiplik durumunu güncelleştirebilir. Bunun için EA Portal'daki [Tüm Abonelikler İçin Hesap Sahipliğini Aktarma](https://ea.azure.com/helpdocs/changeAccountOwnerForASubscription) yönergelerini izleyin.

## <a name="troubleshooting"></a>Sorun giderme

### <a id="no-button"></a> "Aboneliği aktar" düğmesini neden görmüyorum?

Ödeme hesabınız için self servis abonelik aktarımı kullanılamamaktadır. Şu anda, Azure portalın Kurumsal Anlaşma (EA) hesaplarındaki aboneliklerin faturalama sahipliğinin aktarımını desteklemiyoruz. Ayrıca, Microsoft temsilcisiyle çalışırken oluşturulan Microsoft Müşteri Sözleşmesi hesapları faturalama sahipliğinin aktarılmasını desteklemez.

### <a id="no-button"></a> Aboneliğimin türü neden aktarımı desteklemiyor?

Abonelik türlerinin hepsi faturalama sahipliğinin aktarımını desteklemez. Aktarımları destekleyen abonelik türlerinin listesini görüntülemek için bkz. [Desteklenen abonelik türleri](#supported-subscription-types)

### <a id="no-button"></a> Bir aboneliğin faturalama sahipliğini aktarmaya çalıştığımda neden erişim reddedildi hatası alıyorum?

Bir Microsoft Azure planı aboneliğini aktarmaya çalışıyorsanız ve gerekli izniniz yoksa bu hatayı görürsünüz. Bir Microsoft Azure planı aboneliğini aktarmak için, aboneliğin faturalandırıldığı fatura bölümünde bir sahip veya katkıda bulunan olmanız gerekir. Daha fazla bilgi için bkz. [Fatura bölümleri için abonelikleri yönetme](billing-understand-mca-roles.md#manage-subscriptions-for-invoice-section).


## <a name="need-help-contact-us"></a>Yardıma mı ihtiyacınız var? Bize ulaşın.

Sorularınız varsa ya da yardıma gereksinim duyuyorsanız [destek isteği oluşturun](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Sonraki adımlar

- Hizmet Yöneticisi, Ortak Yöneticiler ve diğer RBAC rollerini gözden geçirin ve güncelleştirin. Daha fazla bilgi edinmek için bkz. [Azure abonelik yöneticileri ekleme veya değiştirme](billing-add-change-azure-subscription-administrator.md) ve [RBAC ve Azure portalını kullanarak erişimi yönetme](../role-based-access-control/role-assignments-portal.md).
