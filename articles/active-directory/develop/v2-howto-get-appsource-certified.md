---
title: Azure Active Directory için AppSource sertifikası alın | Microsoft Docs
description: Uygulamanızı Azure Active Directory için AppSource sertifikalı alma hakkında daha fazla bilgi edinin.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 07/23/2020
ms.author: ryanwi
ms.reviewer: jeedes
ms.custom: aaddev
ms.openlocfilehash: 7731aa2eb1a962d4674ed382911ba0058e1ebdaf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101647515"
---
# <a name="get-appsource-certified-for-azure-active-directory"></a>Azure Active Directory için AppSource sertifikası alın

[Microsoft AppSource](https://appsource.microsoft.com/) , iş kullanıcılarının Iş kolu SaaS uygulamalarını bulmasına, denemeye ve yönetmesine yönelik bir hedefdir (mevcut Microsoft SaaS ürünlerine tek başına SaaS ve eklenti).

AppSource 'ta tek başına bir SaaS uygulamasını listelemek için, uygulamanız Azure Active Directory (Azure AD) olan herhangi bir şirketten veya kuruluştan iş hesaplarından çoklu oturum açmayı kabul etmelidir. Oturum açma işlemi, [OpenID Connect](v2-protocols-oidc.md) veya [OAuth 2,0](v2-oauth2-auth-code-flow.md) protokollerini kullanmalıdır. AppSource sertifikası için SAML tümleştirme kabul edilmedi.

## <a name="multi-tenant-applications"></a>Çok kiracılı uygulamalar

*Çok kiracılı bir uygulama* , Azure AD 'ye sahip herhangi bir şirketten veya kuruluştan ayrı bir örnek, yapılandırma veya dağıtıma gerek kalmadan oturum açma işlemlerini kabul eden bir uygulamadır. AppSource, *tek* tıklamayla ücretsiz deneme deneyimini etkinleştirmek için uygulamaların çok kiracılı bir uygulama gerçekleştirmesini önerir.

Uygulamanızda birden çok kiratı etkinleştirmek için şu adımları izleyin:
1. `Multi-Tenanted`Özelliği `Yes` uygulamanızın kayıt bilgilerinde [Azure Portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps)olarak ayarlayın. Varsayılan olarak, Azure portal oluşturulan uygulamalar *[tek kiracılı](#single-tenant-applications)* olarak yapılandırılır.
1. İstekleri uç noktaya göndermek için kodunuzu güncelleştirin `common` . Bunu yapmak için bitiş noktasını öğesinden olarak güncelleştirin `https://login.microsoftonline.com/{yourtenant}` `https://login.microsoftonline.com/common*` .
1. ASP.NET gibi bazı platformlar için kodunuzu aynı zamanda birden çok verenler kabul edecek şekilde güncelleştirmeniz gerekir.

Çoklu kiracı hakkında daha fazla bilgi için bkz. [Multi-tenant uygulama modelini kullanarak herhangi bir Azure Active Directory (Azure AD) kullanıcısına kaydolma](howto-convert-app-to-be-multi-tenant.md).

### <a name="single-tenant-applications"></a>Tek kiracılı uygulamalar

*Tek kiracılı bir uygulama* , yalnızca tanımlı BIR Azure AD örneğinin kullanıcılarından gelen oturum açma işlemlerini kabul eden bir uygulamadır. Dış kullanıcılar (diğer kuruluşların iş veya okul hesapları dahil olmak üzere), her bir kullanıcıyı bir Konuk hesabı olarak uygulamanın kayıtlı olduğu Azure AD örneğine ekledikten sonra tek kiracılı bir uygulamada oturum açabilir. 

Azure AD [B2B işbirliği](../external-identities/what-is-b2b.md) aracılığıyla Azure AD 'ye Konuk hesabı olarak kullanıcı ekleyebilirsiniz ve bunu [programlı](../../active-directory-b2c/code-samples.md)bir şekilde yapabilirsiniz. B2B kullanılırken, kullanıcılar oturum açmak için davetiye gerektirmeyen bir Self Servis Portalı oluşturabilir. Daha fazla bilgi için bkz. [Azure AD B2B Için self servis portalı işbirliği kaydı](../external-identities/self-service-portal.md).

Tek kiracılı uygulamalar *Iletişim beni* deneyime etkinleştirebilir, ancak appsource 'un önerdiği tek tıklamayla/ücretsiz deneme deneyimini etkinleştirmek istiyorsanız, uygulamanızda çok kiracılı hale getirebilirsiniz.

## <a name="appsource-trial-experiences"></a>AppSource deneme deneyimleri

### <a name="free-trial-customer-led-trial-experience"></a>Ücretsiz deneme (müşteri tarafından LED deneme deneyimi)

Müşteri LED deneme sürümü, AppSource 'un uygulamanıza tek tıklamayla erişim sağladığından önerdiği deneyimdir. Aşağıdaki örnek, bu deneyimin nasıl göründüğünü göstermektedir:

1.  Bir Kullanıcı AppSource Web sitesinde uygulamanızı bulur, ardından **ücretsiz deneme** seçeneğini seçer.

    ![Müşteri tarafından açılan deneme deneyimi için ücretsiz denemeyi gösterir](./media/active-directory-devhowto-appsource-certified/customer-led-trial-step1.png)

2.  AppSource, kullanıcıyı Web sitenizdeki bir URL 'ye yönlendirir. Web siteniz *Çoklu oturum açma* işlemini otomatik olarak başlatır (sayfa yükleme sırasında).

    ![Kullanıcının Web sitenizdeki bir URL 'ye nasıl yönlendirildiğini gösterir](./media/active-directory-devhowto-appsource-certified/customer-led-trial-step2.png)

3.  Kullanıcı Microsoft oturum açma sayfasına yönlendirilir ve Kullanıcı oturum açmak için kimlik bilgileri sağlar.

    ![Microsoft oturum açma sayfasını gösterir](./media/active-directory-devhowto-appsource-certified/customer-led-trial-step3.png)

4. Kullanıcı uygulamanız için izin verir.

    ![Örnek: bir uygulama için onay sayfası](./media/active-directory-devhowto-appsource-certified/customer-led-trial-step4.png)

5.  Oturum açma tamamlanır ve Kullanıcı Web sitenize yeniden yönlendirilir.  Kullanıcı ücretsiz denemeyi başlatır.

    ![Sitenize geri yönlendirildiğinde kullanıcının gördüğü deneyimi gösterir](./media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png)

### <a name="contact-me-partner-led-trial-experience"></a>Benimle iletişim kurun (iş ortağı LED deneme deneyimi)

İş ortağı deneme deneyimini, Kullanıcı/şirket sağlamak için bir el ile veya uzun süreli bir işlemin gerçekleşmesi gerektiğinde kullanabilirsiniz. Örneğin, uygulamanızın tamamlanması çok zaman alan sanal makineler, veritabanı örnekleri veya işlemler sağlaması gerekir. Bu durumda, Kullanıcı **deneme denemesi** düğmesini seçip bir form doldurduktan sonra, appsource size kullanıcının iletişim bilgilerini gönderir. Bu bilgileri aldığınızda, ortamı hazırlar ve deneme deneyimine nasıl erişilecekleri kullanıcıya yönergeleri gönderebilirsiniz:<br/><br/>

1. Kullanıcı AppSource Web sitesinde uygulamanızı bulur ve sonra **benimle Iletişim kurun**' i seçer.

    ![İş ortağı LED deneme sürümü için benimle Iletişim kurun](./media/active-directory-devhowto-appsource-certified/partner-led-trial-step1.png)

2. Kullanıcı, iletişim bilgileriyle bir form doldurur.

    ![Kişi bilgilerine sahip bir örnek form gösterir](./media/active-directory-devhowto-appsource-certified/partner-led-trial-step2.png)

3. Kullanıcının bilgilerini alır, bir deneme örneği ayarlar ve uygulamanıza kullanıcıya erişmek için köprüyü gönderirsiniz.

    ![Kullanıcı bilgileri için yer tutucuyu gösterir](./media/active-directory-devhowto-appsource-certified/usercontact.png)

4. Kullanıcı uygulamanıza erişir ve çoklu oturum açma işlemini tamamlar.

    ![Uygulama oturum açma ekranını gösterir](./media/active-directory-devhowto-appsource-certified/partner-led-trial-step3.png)

5. Kullanıcı uygulamanız için izin verir.

    ![Bir uygulama için örnek onay sayfası gösterir](./media/active-directory-devhowto-appsource-certified/partner-led-trial-step4.png)

6. Oturum açma tamamlanır ve Kullanıcı Web sitenize yeniden yönlendirilir. Kullanıcı ücretsiz denemeyi başlatır.

    ![Sitenize geri yönlendirildiğinde kullanıcının gördüğü deneyimi gösterir](./media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png)

### <a name="more-information"></a>Daha fazla bilgi

AppSource deneme deneyimi hakkında daha fazla bilgi için [Bu videoya](https://aka.ms/trialexperienceforwebapps)bakın. 

## <a name="get-support"></a>Destek alın

Azure AD tümleştirmesi için, destek sağlamak üzere topluluk ile [Microsoft Q&bir](/answers/topics/azure-active-directory.html) kullanırız.

Sizden önce [Microsoft Q&](/answers/topics/azure-active-directory.html) sorularınızı sormanızı ve mevcut sorunlara gözatmanızı öneririz. Sorularınıza veya yorumlarınızın ile etiketlendiğinden emin olun [`[azure-active-directory]`](/answers/topics/azure-active-directory.html) .

Geri bildirim sağlamak ve içeriğimizi iyileştirmemizi ve şekillendirmemize yardımcı olmak için aşağıdaki açıklamalar bölümünü kullanın.

## <a name="next-steps"></a>Sonraki adımlar

- Azure AD oturum açma işlemlerini destekleyen uygulamalar oluşturma hakkında daha fazla bilgi için bkz. [Azure AD Için kimlik doğrulama senaryoları](authentication-flows-app-scenarios.md).
- SaaS uygulamanızı AppSource 'ta listeleme hakkında daha fazla bilgi için bkz. [Appsource Iş ortağı bilgileri](https://appsource.microsoft.com/partners)