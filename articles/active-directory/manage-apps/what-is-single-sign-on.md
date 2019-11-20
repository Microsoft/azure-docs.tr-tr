---
title: Uygulamalar - Azure Active Directory çoklu oturum açma | Microsoft Docs
description: Uygulamaları Azure Active Directory (Azure AD) yapılandırma tek bir oturum açma yöntemi seçin öğrenin. Böylece kullanıcılar her uygulama için hatırlamak ve hesap yönetimi yönetimini basitleştirmek için çoklu oturum açma kullanın.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: mimart
ms.reviewer: arvindh, japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f8d1c9f53d08d017c6c07abf8e00ab77e6879e3
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74091407"
---
# <a name="single-sign-on-to-applications-in-azure-active-directory"></a>Azure Active Directory'de uygulamalar için çoklu oturum açma

Çoklu oturum açma (SSO), kullanıcılar Azure Active Directory (Azure AD) uygulamalarında oturum açtığında güvenlik ve kolaylık sağlar. Bu makalede, çoklu oturum açma yöntemleri açıklanmakta ve uygulamalarınızı yapılandırırken en uygun SSO yöntemini seçmenize yardımcı olur.

- **Çoklu oturum açma ile**, kullanıcıların etki alanına katılmış cihazlara erişmek için bir hesap ile bir kez oturum şirket kaynakları, yazılım olarak hizmet (SaaS) uygulamaları ve web uygulamaları. Kullanıcı, oturum açtıktan sonra Office 365 portalında veya Azure AD MyApps erişim paneli uygulamaları başlatabilir. Yöneticiler kullanıcı hesabı yönetimini merkezden gerçekleştirin ve otomatik olarak ekleyebilir veya grup üyeliğine dayalı uygulamalara kullanıcı erişimini kaldırabilirsiniz.

- **Çoklu oturum açma olmadan**, kullanıcılar uygulamaya özgü hatırlamak ve her uygulama için oturum açın. Office 365 kutusu ve Salesforce gibi her bir uygulama için kullanıcı hesaplarını oluşturmak için BT personeli gerekir. Kullanıcıların parolalarını unutmayın yanı sıra her bir uygulama için oturum açmak için kullanacağınız gerekir.

## <a name="choosing-a-single-sign-on-method"></a>Tek bir oturum açma yöntemi seçme

Bir uygulama için çoklu oturum açmayı yapılandırmak için birkaç yolu vardır. Çoklu oturum açma yönteminin seçilmesi, uygulamanın kimlik doğrulaması için nasıl yapılandırıldığına bağlıdır.

- Bulut uygulamaları, çoklu oturum açma için OpenID Connect, OAuth, SAML, parola tabanlı, bağlantılı veya devre dışı Yöntemler kullanabilir. 
- Şirket içi uygulamaları, parola tabanlı, tümleşik Windows kimlik doğrulaması, çoklu oturum açma için üst bilgi tabanlı, bağlantılı veya devre dışı yöntemleri kullanabilir. Uygulamalar, uygulama proxy'si için yapılandırıldığında, şirket içi seçenekler çalışır.

Bu akış, hangi çoklu oturum açma yöntemi, sizin durumunuz için en iyi olduğuna karar vermenize yardımcı olur.

![Çoklu oturum açma yöntemi için karar akış çizelgesi](./media/what-is-single-sign-on/choose-single-sign-on-method-040419.png)

Aşağıdaki tabloda, tek oturum açma yöntemleri özetler ve daha fazla ayrıntı için bağlantılar.

| Çoklu oturum açma yöntemi | Uygulama türleri | Kullanılması gereken durumlar |
| :------ | :------- | :----- |
| [OpenID Connect ve OAuth](#openid-connect-and-oauth) | yalnızca bulut | Yeni bir uygulama geliştirirken OpenID Connect ve OAuth kullanın. Bu protokol uygulama yapılandırmasını basitleştirir, kullanımı kolay SDK 'lara sahiptir ve uygulamanızın MS Graf kullanmasını sağlar.
| [SAML](#saml-sso) | Bulut ve şirket içi | OpenID Connect veya OAuth kullanmayan mevcut uygulamalar için mümkün olduğunda SAML 'yi seçin. SAML, SAML protokollerinden birini kullanarak kimlik doğrulaması yapan uygulamalar için geçerlidir.|
| [Parola tabanlı](#password-based-sso) | Bulut ve şirket içi | Uygulamanın Kullanıcı adı ve parolayla kimlik doğrulaması yaparken parola tabanlı ' yı seçin. Parola tabanlı çoklu oturum açma güvenli uygulama parola depolama ve bir web tarayıcısı uzantısı veya mobil uygulama kullanarak yeniden yürütme sağlar. Bu yöntem, uygulama tarafından sağlanan mevcut oturum açma işlemi kullanır, ancak yönetici parolaları yönetmek etkinleştirir. |
| [Bağlı](#linked-sign-on) | Bulut ve şirket içi | Uygulama, başka bir kimlik sağlayıcısı hizmetinde çoklu oturum açma için yapılandırıldığında, bağlantılı oturum açma seçeneğini belirleyin. Bu seçenek varsayılan olarak, uygulamayı çoklu oturum açma eklemez. Ancak, uygulamanın tek Active Directory Federasyon Hizmetleri gibi başka bir hizmet kullanılarak uygulanan oturum zaten olabilir.|
| [Devre dışı](#disabled-sso) | Bulut ve şirket içi | Uygulama çoklu oturum açma için yapılandırılmaya Hazırlanmadıysa, çoklu oturum açmayı devre dışı bırak seçeneğini belirleyin. Kullanıcıların, bunlar bu uygulamayı başlatmak her seferinde kullanıcı adı ve parola girmeniz gerekir.|
| [Tümleşik Windows kimlik doğrulaması (IWA)](#integrated-windows-authentication-iwa-sso) | yalnızca şirket içi | [Tümleşik Windows kimlik doğrulaması (IWA)](/aspnet/web-api/overview/security/integrated-windows-authentication)veya talep kullanan uygulamalar kullanan uygulamalar için IWA çoklu oturum açma seçeneğini belirleyin. IWA için uygulama proxy bağlayıcıları, kullanıcıların uygulama kimliğini doğrulamak için Kerberos kısıtlı temsilcisini (KCD) kullanır. |
| [Üst bilgi tabanlı](#header-based-sso) | yalnızca şirket içi | Uygulama için kimlik doğrulama üst bilgileri kullandığında üst bilgi tabanlı çoklu oturum açma kullanın. Üst bilgi tabanlı çoklu oturum açma, Azure AD için PingAccess gerektirir. Uygulama Ara sunucusu kullanıcının kimliğini doğrulamak için Azure AD kullanır ve ardından bağlayıcı hizmetini üzerinden geçen trafik geçirir.  |

## <a name="openid-connect-and-oauth"></a>OpenID Connect ve OAuth

Yeni uygulamalar geliştirirken, birden çok cihaz platformunda uygulamanız için en iyi çoklu oturum açma deneyimi elde etmek için OpenID Connect ve OAuth gibi modern protokolleri kullanın. OAuth, kullanıcıların veya yöneticilerin [Microsoft Graph](/graph/overview)gibi korumalı kaynaklara [izin vermesini](configure-user-consent.md) sağlar. Uygulamanız için [SDK 'ları](../develop/reference-v2-libraries.md) benimsemeyi ve ayrıca uygulamanız [Microsoft Graph](/graph/overview)kullanmaya hazırız.

Daha fazla bilgi için bkz.

- [OAuth 2.0](../develop/v2-oauth2-auth-code-flow.md)
- [OpenID Connect 1.0](../develop/v2-protocols-oidc.md)
- [Microsoft Identity platform Geliştirici Kılavuzu](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide).

## <a name="saml-sso"></a>SAML SSO

İle **SAML çoklu oturum açma**, Azure AD, kullanıcının Azure AD hesabı kullanarak uygulamaya kimliğini doğrular. Azure AD oturum açma bilgileri uygulamaya bir bağlantı protokolü üzerinden iletişim kurar. SAML tabanlı çoklu oturum açma sayesinde, SAML talepleriniz içinde tanımladığınız kurallara göre kullanıcıları belirli uygulama rolleriyle eşleyebilirsiniz.

Uygulama destekliyorsa SAML tabanlı çoklu oturum açma seçeneğini belirleyin.

SAML tabanlı çoklu oturum açma şu protokolden herhangi birini kullanan uygulamalar için desteklenir:

- SAML 2.0
- WS-Federation

SAML tabanlı çoklu oturum açma için bir SaaS uygulaması yapılandırmak üzere bkz. [SAML tabanlı çoklu oturum açmayı yapılandırma](configure-single-sign-on-non-gallery-applications.md). Ayrıca, hizmet olarak yazılım (SaaS) uygulamalarında, SAML tabanlı çoklu oturum açma yapılandırmasında size adım adım kılavuzluk eden [uygulamaya özgü bir öğretici](../saas-apps/tutorial-list.md) vardır.

Bir uygulamayı WS-Federation için yapılandırmak üzere, SAML tabanlı çoklu oturum açma için uygulamayı yapılandırmak üzere aynı kılavuzu izleyin, bkz. [SAML tabanlı çoklu oturum açmayı yapılandırma](configure-single-sign-on-non-gallery-applications.md). Uygulamayı Azure AD kullanacak şekilde yapılandırma adımında, WS-Federation uç noktası `https://login.microsoftonline.com/<tenant-ID>/wsfed`için Azure AD oturum açma URL 'sini değiştirmeniz gerekir.

SAML tabanlı çoklu oturum açma için şirket içi bir uygulamayı yapılandırmak için, bkz. [uygulama proxy 'si ile şirket içi uygulamalar Için SAML çoklu oturum açma](application-proxy-configure-single-sign-on-on-premises-apps.md).

SAML protokolü hakkında daha fazla bilgi için bkz. [Çoklu oturum açma SAML Protokolü](../develop/single-sign-on-saml-protocol.md).

## <a name="password-based-sso"></a>Parola tabanlı SSO

Parola tabanlı oturum açma sayesinde, kullanıcılar ilk kez erişirken Kullanıcı adı ve parola ile uygulama üzerinde oturum açabilirler. İlk oturum açma işleminden sonra Azure AD, uygulamaya Kullanıcı adı ve parola sağlar.

Parola tabanlı çoklu oturum açma uygulama tarafından sağlanan mevcut kimlik doğrulama işlemi kullanır. Parola çoklu oturum açma bir uygulama için etkinleştirdiğinizde, Azure AD toplar ve kullanıcı adları ve parolalar uygulama için güvenli bir şekilde depolar. Kullanıcı kimlik bilgilerini şifrelenmiş bir duruma dizininde depolanır.

Şu durumlarda parola tabanlı çoklu oturum açma seçin:

- Bir uygulama SAML çoklu oturum açma protokolünü desteklemez.
- Bir uygulama adı ve parola yerine erişim belirteçleri ve üst bilgileri ile kimlik doğrulaması yapar.

Parola tabanlı çoklu oturum açma bir HTML tabanlı oturum açma sayfası olan tüm bulut tabanlı uygulama için desteklenir. Kullanıcı aşağıdaki tarayıcılardan herhangi birini kullanabilirsiniz:

- Internet Explorer 11 Windows 7 veya üzeri
   > [!NOTE]
   > Internet Explorer sınırlı desteğe sahiptir ve artık yeni yazılım güncelleştirmeleri almaz. Microsoft Edge önerilen tarayıcıdır.

- Windows 10 yıldönümü sürümü veya sonraki sürümlerde Microsoft Edge
- İOS ve Android için Microsoft Edge
- Intune Managed Browser
- Chrome Windows 7 ve daha sonra ve MacOS x veya sonrası
- Firefox 26,0 veya daha sonra Windows XP SP2 veya üzeri ve Mac OS X 10.6 üzerinde veya üzeri

Parola tabanlı çoklu oturum açma için bir bulut uygulaması yapılandırmak üzere, bkz. [parola çoklu oturum açmayı yapılandırma](configure-password-single-sign-on-non-gallery-applications.md).

Bir şirket içi uygulama için uygulama proxy'si aracılığıyla çoklu oturum açmayı yapılandırmak için bkz [vaulting uygulama proxy'si ile çoklu oturum açma için parola](application-proxy-configure-single-sign-on-password-vaulting.md)

### <a name="how-authentication-works-for-password-based-sso"></a>Kimlik doğrulama, parola tabanlı SSO için nasıl kullanılır

Bir kullanıcının bir uygulamada kimliğini doğrulamak için Azure AD, kullanıcının kimlik bilgilerini dizinden alır ve uygulamanın oturum açma sayfasına girer.  Azure AD kullanıcı kimlik bilgilerini güvenli bir şekilde bir web tarayıcısı uzantısı veya mobil uygulama geçirir. Bu işlem, bir yönetici kullanıcı kimlik bilgilerini yönetmek etkinleştirir ve kullanıcıların parolalarını unutmayın gerektirmez.

> [!IMPORTANT]
> Otomatik oturum açma işlemi sırasında kimlik bilgileri Kullanıcı tarafından karartılmış hale getirilebilir. Ancak, kimlik bilgileri web hata ayıklama araçlarını kullanarak bulunabilir. Kullanıcıların ve yöneticilerin kimlik bilgilerini doğrudan kullanıcı tarafından girildiği gibi aynı güvenlik ilkeleri izlemeniz gerekir.

### <a name="managing-credentials-for-password-based-sso"></a>Parola tabanlı SSO için kimlik bilgilerini yönetme

Her uygulamanın ya da Azure AD Yöneticisi veya kullanıcılar tarafından yönetilebilir.

Ne zaman Azure AD Yöneticisi kimlik bilgilerini yönetir:  

- Kullanıcı sıfırlama veya kullanıcı adınızı ve parolanızı hatırlayacağınızdan gerekmez. Kullanıcı kendi erişim panellerine veya sağlanan bağlantı yoluyla tıklayarak uygulamaya erişebilir.
- Yönetici kimlik bilgileri yönetim görevlerini gerçekleştirebilirsiniz. Örneğin, yönetici kullanıcı grup üyeliklerini ve çalışan durumuna göre uygulama erişimi güncelleştirebilirsiniz.
- Yönetici, çok sayıda kullanıcı arasında paylaşılan uygulamalara erişim sağlamak için yönetici kimlik bilgilerini kullanabilirsiniz. Örneğin, yönetici bir sosyal medya veya belge paylaşımı uygulama erişimi için bir uygulama erişebilen herkesin izin verebilirsiniz.

Ne zaman son kullanıcı kimlik bilgilerini yönetir:

- Kullanıcılar parolalarını güncelleştirmek ya da gerektiği şekilde silerek yönetebilir.
- Yöneticiler uygulama için yeni kimlik bilgilerini ayarlamak hala mümkün.

## <a name="linked-sign-on"></a>Bağlantılı oturum açma
Bağlantılı oturum açma için çoklu oturum açmayı başka bir hizmet olarak zaten yapılandırılmış bir uygulama için çoklu oturum açma sağlamak için Azure AD'deki sağlar. Bağlı uygulama Office 365 portalında veya Azure AD MyApps portalında son kullanıcılara görünür. Örneğin, bir kullanıcı çoklu oturum açma Active Directory Federasyon Hizmetleri 2.0 (AD FS), Office 365 portalından için yapılandırılmış bir uygulama başlatabilirsiniz. Ek raporlama da Office 365 portalında veya Azure AD MyApps portalından başlatılan bağlantılı uygulamalar için kullanılabilir. Bir uygulamayı bağlantılı oturum açma için yapılandırmak üzere bkz. [bağlı oturum açmayı yapılandırma](configure-linked-sign-on.md).

### <a name="linked-sign-on-for-application-migration"></a>Uygulama geçişi için bağlantılı oturum açma

Bağlantılı oturum açma, uygulamaları bir süre boyunca geçirirken tutarlı bir kullanıcı deneyimi sağlayabilir. Uygulamaları Azure Active Directory geçiriyorsanız, geçiş yapmak istediğiniz tüm uygulamaların bağlantılarını hızlıca yayımlamak için bağlantılı oturum açma ' yı kullanabilirsiniz.  Kullanıcılar, tüm bağlantıları bulabilirsiniz [MyApps portalında](../user-help/active-directory-saas-access-panel-introduction.md) veya [Office 365 uygulama başlatıcısında](https://support.office.com/article/meet-the-office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a). Kullanıcılar, bağlı bir uygulama veya geçirilmiş bir uygulamayı eriştiğiniz bilemezsiniz.  

Bir kullanıcı ile bağlantılı bir uygulamaya kimlik doğrulaması yapıldıktan sonra hesap kaydı son kullanıcı çoklu oturum açma erişimi sağlanan önce oluşturulması gerekir. Bu hesap kaydı sağlama ya da otomatik olarak gerçekleşebileceği ya da bir yönetici tarafından el ile ortaya çıkabilir.

## <a name="disabled-sso"></a>Devre dışı SSO

Devre dışı moda uygulama için çoklu oturum açma kullanılmayan anlamına gelir. Çoklu oturum açma devre dışı bırakıldığında, kullanıcıların iki kez kimlik doğrulaması gerekebilir. İlk olarak, Azure AD ile kullanıcıların kimliğini doğrulamak ve bunlar uygulamaya oturum açın.

Çoklu oturum açma modunu devre dışı:

- Bu uygulamayı Azure AD çoklu oturum açma ile tümleştirmeye hazır değilseniz veya
- Uygulamanın diğer yönleri test ediyorsanız veya
- Bir şirket içi uygulamaya güvenlik katmanı olarak, kullanıcıların kimlik doğrulaması yapmasına gerek yoktur. Kullanıcı kimlik doğrulaması yapması, devre dışı.

## <a name="integrated-windows-authentication-iwa-sso"></a>Tümleşik Windows kimlik doğrulaması (IWA) SSO

[Uygulama proxy 'si](application-proxy.md) , [Tümleşik Windows kimlik doğrulaması (IWA)](/aspnet/web-api/overview/security/integrated-windows-authentication)veya talep kullanan uygulamalar kullanan uygulamalar için çoklu oturum açma (SSO) sağlar. Uygulamanız IWA kullanıyorsa, uygulama proxy'si uygulamaya, Kerberos Kısıtlı temsilci (KCD) kullanarak kimliğini doğrular. Kullanıcı zaten Azure AD'yi kullanarak kimliğinin çünkü Azure Active Directory tarafından güvenilen bir talep kullanan uygulama için çoklu oturum açma çalışır.

IWA ile kimlik doğrulayan şirket içi bir uygulamada çoklu oturum açma sağlamak için tümleşik Windows kimlik doğrulaması çoklu oturum açma modu ' nu seçin.

Şirket içi uygulama IWA için yapılandırmak üzere bkz [uygulamalarınıza uygulama proxy'si ile çoklu oturum açma için Kerberos kısıtlanmış temsil](application-proxy-configure-single-sign-on-with-kcd.md).

### <a name="how-single-sign-on-with-kcd-works"></a>KCD works ile nasıl çoklu oturum açma
Bir kullanıcı IWA kullanan şirket içi uygulamaya eriştiğinde, bu diyagramda akışı açıklanır.

![Microsoft Azure AD kimlik doğrulaması akış diyagramı](./media/application-proxy-configure-single-sign-on-with-kcd/AuthDiagram.png)

1. Kullanıcı, uygulama proxy 'si aracılığıyla şirket içi uygulamaya erişmek için URL 'YI girer.
1. Uygulama Ara sunucusu için Azure AD kimlik doğrulama hizmetleri preauthenticate için istek yönlendirir. Bu noktada, Azure AD, tüm geçerli kimlik doğrulama ve yetkilendirme ilkeleri, çok faktörlü kimlik doğrulaması gibi uygulanır. Kullanıcı doğrulanmış olması durumunda, Azure AD belirteç oluşturulur ve kullanıcıya gönderir.
1. Kullanıcı belirteci uygulama ara sunucusuna iletir.
1. Uygulama proxy'si, belirteci doğrular ve belirteçten kullanıcı asıl adı (UPN) alır. Bunu daha sonra istek, UPN ve hizmet asıl adı (SPN) bağlayıcı dually kimliği doğrulanmış ve güvenli bir kanal üzerinden gönderir.
1. Bağlayıcı, şirket içi AD ile Kerberos kısıtlı temsilcisi (KCD) anlaşması kullanır ve uygulamaya bir Kerberos belirteci almak için kullanıcıyı taklit edin.
1. Active Directory Uygulama bağlayıcısı için Kerberos belirteci gönderir.
1. Bağlayıcı AD'den alınan Kerberos belirteci kullanarak uygulama sunucusu, özgün istek gönderir.
1. Uygulama için uygulama proxy'si hizmeti ardından döndürülen bağlayıcı yanıta gönderir ve son kullanıcı.

## <a name="header-based-sso"></a>Üst bilgi tabanlı SSO

Üst bilgi tabanlı çoklu oturum açma, HTTP üst bilgileri için kimlik doğrulaması kullanan uygulamalar için çalışır. Bu oturum açma yöntemi PingAccess adlı bir üçüncü taraf kimlik doğrulama hizmeti kullanır. Bir kullanıcının yalnızca Azure AD'ye kimlik doğrulaması gerekir.

Uygulama proxy 'Si ve PingAccess uygulama için yapılandırıldığında üst bilgi tabanlı çoklu oturum açma seçeneğini belirleyin.

Üst bilgi tabanlı kimlik doğrulamasını yapılandırmak için bkz: [üst bilgi tabanlı kimlik doğrulaması için uygulama proxy'si ile çoklu oturum açma](application-proxy-configure-single-sign-on-with-ping-access.md).

### <a name="what-is-pingaccess-for-azure-ad"></a>Azure AD için PingAccess nedir?

Azure AD, kullanıcıları için PingAccess kullanarak erişim ve tek kimlik doğrulaması için üst bilgi kullanan uygulamalar için oturum. Uygulama Ara sunucusu, bu uygulamaları diğer, Azure AD kimlik doğrulaması yapmak için kullanarak ve ardından bağlayıcı hizmetini üzerinden trafiği geçirerek gibi davranır. PingAccess hizmeti Azure AD erişim belirteci kimlik doğrulaması gerçekleştikten sonra uygulamaya gönderilen bir üstbilgi biçimine çevirir.

Kurumsal uygulamalarınıza kullanmak oturum açtığında, kullanıcılar farklı bir şey fark olmaz. Bunlar yine de her yerde her cihazda çalışabilirsiniz. Uygulama Ara sunucusu bağlayıcıları doğrudan tüm uygulamalara uzaktan trafiği ve otomatik olarak yük dengelemesi devam edeceğiz.

### <a name="how-do-i-get-a-license-for-pingaccess"></a>Bir lisans için PingAccess nasıl alabilirim?

Bu senaryo Azure AD ve PingAccess arasındaki bir iş ortaklığı aracılığıyla sunulduğundan, her iki hizmet için de lisanslarına ihtiyacınız vardır. Ancak Azure AD Premium abonelikler, 20 ' ye kadar uygulamayı kapsayan temel bir PingAccess lisansını içerir. 20'den fazla üst bilgi tabanlı uygulamaları yayımlamak gerekiyorsa, ek bir PingAccess lisanstan elde edebilirsiniz.

Daha fazla bilgi için bkz. [Azure Active Directory sürümleri](../fundamentals/active-directory-whatis.md).

## <a name="related-articles"></a>İlgili makaleler:
* [SaaS uygulamalarını Azure Active Directory ile tümleştirme öğreticileri](../saas-apps/tutorial-list.md)
* [SAML tabanlı çoklu oturum açmayı yapılandırma](configure-single-sign-on-non-gallery-applications.md)
* [Parola tabanlı çoklu oturum açmayı yapılandırma](configure-password-single-sign-on-non-gallery-applications.md)
* [Bağlı oturum açmayı yapılandırma](configure-linked-sign-on.md)
* [Uygulamalara erişimi yönetme giriş](what-is-access-management.md)
* İndirme bağlantısı: [tek oturum açma dağıtım planı](https://aka.ms/SSODeploymentPlan).
