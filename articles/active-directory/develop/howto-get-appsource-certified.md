---
title: Azure Active Directory için AppSource sertifikası alma | Microsoft Docs
description: Uygulamanızı Azure Active Directory için AppSource sertifikalı alma hakkında ayrıntılı bilgi.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 21206407-49f8-4c0b-84d1-c25e17cd4183
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/21/2018
ms.author: ryanwi
ms.reviewer: andret
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 139c7c2e6736eeb3e78ac0aab913378ac84160e1
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72374087"
---
# <a name="how-to-get-appsource-certified-for-azure-active-directory"></a>Azure Active Directory için AppSource sertifikası alma

[Microsoft AppSource](https://appsource.microsoft.com/) , iş kullanıcılarının Iş kolu SaaS uygulamalarını bulmasına, denemeye ve yönetmesine yönelik bir hedefdir (mevcut Microsoft SaaS ürünlerine tek başına SaaS ve eklenti).

AppSource 'ta tek başına bir SaaS uygulamasını listelemek için, uygulamanız Azure Active Directory (Azure AD) olan herhangi bir şirketten veya kuruluştan iş hesaplarından çoklu oturum açmayı kabul etmelidir. Oturum açma işlemi, [OpenID Connect](v1-protocols-openid-connect-code.md) veya [OAuth 2,0](v1-protocols-oauth-code.md) protokollerini kullanmalıdır. AppSource sertifikası için SAML tümleştirme kabul edilmedi.

## <a name="guides-and-code-samples"></a>Kılavuzlar ve kod örnekleri

Açık KIMLIK bağlantısı kullanarak uygulamanızı Azure AD ile tümleştirme hakkında daha fazla bilgi edinmek istiyorsanız, [Azure Active Directory geliştirici kılavuzundaki](v1-overview.md#get-started "Geliştiriciler için Azure AD ile çalışmaya başlama")kılavuzlarımızı ve kod örneklerimizi izleyin.

## <a name="multi-tenant-applications"></a>Çok kiracılı uygulamalar

*Çok kiracılı bir uygulama* , Azure AD 'ye sahip herhangi bir şirketten veya kuruluştan ayrı bir örnek, yapılandırma veya dağıtıma gerek kalmadan oturum açma işlemlerini kabul eden bir uygulamadır. AppSource, *tek* tıklamayla ücretsiz deneme deneyimini etkinleştirmek için uygulamaların çok kiracılı bir uygulama gerçekleştirmesini önerir.

Uygulamanızda birden çok kiratı etkinleştirmek için şu adımları izleyin:
1. Uygulama kayıt bilgilerinde [Azure portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps)`Multi-Tenanted` özelliğini `Yes` olarak ayarlayın. Varsayılan olarak, Azure portal oluşturulan uygulamalar *[tek kiracılı](#single-tenant-applications)* olarak yapılandırılır.
1. @No__t-0 uç noktasına istek göndermek için kodunuzu güncelleştirin. Bunu yapmak için uç noktayı `https://login.microsoftonline.com/{yourtenant}` ' dan `https://login.microsoftonline.com/common*` ' e güncelleştirin.
1. ASP .NET gibi bazı platformlar için kodunuzu aynı zamanda birden çok verenler kabul edecek şekilde güncelleştirmeniz gerekir.

Çoklu kiracı hakkında daha fazla bilgi için bkz. [Multi-tenant uygulama modelini kullanarak herhangi bir Azure Active Directory (Azure AD) kullanıcısına kaydolma](howto-convert-app-to-be-multi-tenant.md).

### <a name="single-tenant-applications"></a>Tek kiracılı uygulamalar

*Tek kiracılı bir uygulama* , yalnızca tanımlı BIR Azure AD örneğinin kullanıcılarından gelen oturum açma işlemlerini kabul eden bir uygulamadır. Dış kullanıcılar (diğer kuruluşların iş veya okul hesapları dahil olmak üzere), her bir kullanıcıyı bir Konuk hesabı olarak uygulamanın kayıtlı olduğu Azure AD örneğine ekledikten sonra tek kiracılı bir uygulamada oturum açabilir. 

Azure AD [B2B işbirliği](../b2b/what-is-b2b.md) aracılığıyla Azure AD 'ye Konuk hesabı olarak kullanıcı ekleyebilirsiniz ve bunu [programlı](../../active-directory-b2c/code-samples.md)bir şekilde yapabilirsiniz. B2B kullanılırken, kullanıcılar oturum açmak için davetiye gerektirmeyen bir Self Servis Portalı oluşturabilir. Daha fazla bilgi için bkz. [Azure AD B2B Için self servis portalı işbirliği kaydı](https://docs.microsoft.com/azure/active-directory/b2b/self-service-portal).

Tek kiracılı uygulamalar *Iletişim beni* deneyime etkinleştirebilir, ancak appsource 'un önerdiği tek tıklamayla/ücretsiz deneme deneyimini etkinleştirmek istiyorsanız, uygulamanızda çok kiracılı hale getirebilirsiniz.

## <a name="appsource-trial-experiences"></a>AppSource deneme deneyimleri

### <a name="free-trial-customer-led-trial-experience"></a>Ücretsiz deneme (müşteri tarafından LED deneme deneyimi)

Müşteri LED deneme sürümü, AppSource 'un uygulamanıza tek tıklamayla erişim sağladığından önerdiği deneyimdir. Aşağıdaki örnek, bu deneyimin nasıl göründüğünü göstermektedir:

<table >
<tr>
    <td valign="top" width="33%">1.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step1.png" width="85%" alt-text="Shows Free trial for customer-led trial experience"/><ul><li>Kullanıcı uygulamanızı AppSource Web sitesinde bulur</li><li>' Ücretsiz deneme ' seçeneğini belirler</li></ul></td>
    <td valign="top" width="33%">2.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step2.png" width="85%" alt-text="Shows how user is redirected to a URL in your web site"/><ul><li>AppSource kullanıcıyı Web sitenizdeki bir URL 'ye yeniden yönlendirir</li><li>Web siteniz <i>Çoklu oturum açma</i> işlemini otomatik olarak başlatır (sayfa yükleme sırasında)</li></ul></td>
    <td valign="top" width="33%">3.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step3.png" width="85%" alt-text="Shows the Microsoft sign-in page"/><ul><li>Kullanıcı Microsoft oturum açma sayfasına yönlendirildi</li><li>Kullanıcı, oturum açmak için kimlik bilgilerini sağlar</li></ul></td>
</tr>
<tr>
    <td valign="top" width="33%">4.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step4.png" width="85%" alt-text="Example: Consent page for an application"/><ul><li>Kullanıcı, uygulamanız için izin verir</li></ul></td>
    <td valign="top" width="33%">5.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png" width="85%" alt-text="Shows the experience the user sees when redirected back to your site"/><ul><li>Oturum açma işlemi tamamlandı ve Kullanıcı Web sitenize geri yönlendirildi</li><li>Kullanıcı ücretsiz denemeyi başlatır</li></ul></td>
    <td></td>
</tr>
</table>

### <a name="contact-me-partner-led-trial-experience"></a>Benimle iletişim kurun (iş ortağı LED deneme deneyimi)

İş ortağı deneme deneyimini, Kullanıcı/şirket sağlamak için bir el ile veya uzun süreli bir işlemin gerçekleşmesi gerektiğinde kullanabilirsiniz. Örneğin, uygulamanızın tamamlanması çok zaman alan sanal makineler, veritabanı örnekleri veya işlemler sağlaması gerekir. Bu durumda, Kullanıcı **deneme denemesi** düğmesini seçip bir form doldurduktan sonra, appsource size kullanıcının iletişim bilgilerini gönderir. Bu bilgileri aldığınızda, ortamı hazırlar ve deneme deneyimine nasıl erişilecekleri kullanıcıya yönergeleri gönderebilirsiniz:<br/><br/>

<table valign="top">
<tr>
    <td valign="top" width="33%">1.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step1.png" width="85%" alt-text="Shows Contact me for partner-led trial experience"/><ul><li>Kullanıcı uygulamanızı AppSource Web sitesinde bulur</li><li>' Benimle Iletişim ' seçeneğini belirler</li></ul></td>
    <td valign="top" width="33%">2.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step2.png" width="85%" alt-text="Shows an example form with contact info"/><ul><li>İletişim bilgileriyle bir form doldurur</li></ul></td>
     <td valign="top" width="33%">3.<br/><br/>
        <table bgcolor="#f7f7f7">
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/UserContact.png" width="55%" alt-text="Shows placeholder for user information"/></td>
            <td>Kullanıcı bilgileri alıyorsunuz</td>
        </tr>
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/SetupEnv.png" width="55%" alt-text="Shows placeholder for setup environment info"/></td>
            <td>Kurulum ortamı</td>
        </tr>
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/ContactCustomer.png" width="55%" alt-text="Shows placeholder for trial info"/></td>
            <td>Deneme bilgileriyle kullanıcıyla iletişim kurun</td>
        </tr>
        </table><br/><br/>
        <ul><li>Kullanıcının bilgilerini ve kurulum deneme örneğini alıyorsunuz</li><li>Uygulamanıza Kullanıcı erişimi için köprü gönderirsiniz</li></ul>
    </td>
</tr>
<tr>
    <td valign="top" width="33%">4.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step3.png" width="85%" alt-text="Shows the application sign-in screen"/><ul><li>Kullanıcı uygulamanıza erişiyor ve çoklu oturum açma işlemini tamamlayacak</li></ul></td>
    <td valign="top" width="33%">5.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step4.png" width="85%" alt-text="Shows an example consent page for an application"/><ul><li>Kullanıcı, uygulamanız için izin verir</li></ul></td>
    <td valign="top" width="33%">6.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png" width="85%" alt-text="Shows the experience the user sees when redirected back to your site"/><ul><li>Oturum açma işlemi tamamlandı ve Kullanıcı Web sitenize geri yönlendirildi</li><li>Kullanıcı ücretsiz denemeyi başlatır</li></ul></td>
   
</tr>
</table>

### <a name="more-information"></a>Daha fazla bilgi

AppSource deneme deneyimi hakkında daha fazla bilgi için [Bu videoya](https://aka.ms/trialexperienceforwebapps)bakın. 

## <a name="next-steps"></a>Sonraki Adımlar

- Azure AD oturum açma işlemlerini destekleyen uygulamalar oluşturma hakkında daha fazla bilgi için bkz. [Azure AD Için kimlik doğrulama senaryoları](https://docs.microsoft.com/azure/active-directory/develop/v1-authentication-scenarios).
- SaaS uygulamanızı AppSource 'ta listeleme hakkında daha fazla bilgi için bkz. [Appsource Iş ortağı bilgileri](https://appsource.microsoft.com/partners)

## <a name="get-support"></a>Destek alın

Azure AD tümleştirmesi için, destek sağlamak üzere topluluk ile [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-active-directory+appsource) kullanırız.

İlk olarak Stack Overflow sorularınızı sormak ve mevcut sorunlara gözatmanız önerilir. Sorularınıza veya yorumlarınızın [`[azure-active-directory]` ve `[appsource]`](https://stackoverflow.com/questions/tagged/azure-active-directory+appsource)ile etiketlendiğinden emin olun.

Geri bildirim sağlamak ve içeriğimizi iyileştirmemizi ve şekillendirmemize yardımcı olmak için aşağıdaki açıklamalar bölümünü kullanın.

<!--Reference style links -->
[AAD-Auth-Scenarios]:v1-authentication-scenarios.md
[AAD-Auth-Scenarios-Browser-To-WebApp]:v1-authentication-scenarios.md#web-browser-to-web-application
[AAD-Dev-Guide]: v1-overview.md
[AAD-Howto-Multitenant-Overview]: howto-convert-app-to-be-multi-tenant.md
[AAD-QuickStart-Web-Apps]: v1-overview.md#get-started

<!--Image references-->
