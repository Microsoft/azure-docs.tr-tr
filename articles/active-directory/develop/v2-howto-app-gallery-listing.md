---
title: Uygulamanızı Azure Active Directory Uygulama Galerisine yayımlayın
description: Azure Active Directory Uygulama galerisinde çoklu oturum açmayı destekleyen bir uygulamayı nasıl listeleyeceğinizi öğrenin.
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 12/14/2020
ms.author: kenwith
ms.reviewer: jeedes
ms.custom: aaddev
ms.openlocfilehash: f2a04a341239b62290eecac8c0e1ce70b7c927c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99988913"
---
# <a name="publish-your-app-to-the-azure-ad-app-gallery"></a>Uygulamanızı Azure AD uygulama galerisine yayımlayın

Uygulamanızı Azure AD uygulama galerisinde yayımlayabilirsiniz. Uygulamanız yayımlandığında, kiracıya uygulama eklerken müşteriler için bir seçenek olarak görünür. 

Uygulamanızı Azure AD galerisine eklemenin avantajlarından bazıları şunlardır:

- Müşteriler, uygulamanız için mümkün olan en iyi çoklu oturum açma deneyimini bulur.
- Uygulamanın yapılandırması basit ve en düşüktür.
- Hızlı arama, uygulamanızı galeride bulur.
- Ücretsiz, temel ve Premium Azure AD müşterilerinin hepsi bu tümleştirmeyi kullanabilir.
- Karşılıklı müşteriler, adım adım bir yapılandırma öğreticisini alır.

Ayrıca, müşterilerinizin uygulamanız için bir kimlik sağlayıcısı olarak Azure AD 'yi kullanması durumunda birçok avantaj de vardır. Bunlardan bazıları:

- Kullanıcılarınız için çoklu oturum açma sağlayın. SSO ile müşterilerinizin çoklu oturum açma ile daha kolay olmasını sağlayarak destek maliyetlerini azaltabilirsiniz. Tek tıklamayla SSO etkinse, müşterilerinizin BT yöneticileri, uygulamanızın kuruluşunuzda kullanılmak üzere nasıl yapılandırılacağını öğrenmek zorunda kalmaz. Çoklu oturum açma hakkında daha fazla bilgi edinmek için bkz. [Çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).
- Uygulamanız Microsoft 365 App Gallery, Microsoft 365 uygulama başlatıcısı ve Microsoft Search içinde Office.com üzerinde bulunabilir. 
- Tümleşik uygulama yönetimi. Azure AD 'de uygulama yönetimi hakkında daha fazla bilgi edinmek için bkz. [uygulama yönetimi nedir?](../manage-apps/what-is-application-management.md).
- Uygulamanız, Microsoft ekosisteminde Kullanıcı üretkenliğini yönlendiren verilere erişmek için [Graph API](/graph/) kullanabilir.
- Karşılıklı müşterilerimiz için Azure AD ekibiyle birlikte üretilmiş uygulamaya özgü belgeler benimseme kolaylaştırır.
- Müşterilerinizin kendi çalışan ve konuk kimliklerinin kimlik doğrulama ve yetkilendirme işlemlerini tamamen yönetmesine olanak sağlarsınız.
- Tüm hesap yönetimi ve uyumluluk sorumluluğunun bu kimliklerin müşteri sahibine yerleştirilmesi.
- Belirli kimlik sağlayıcıları, gruplar veya kullanıcılar için kendi iş ihtiyaçlarını karşılayacak SSO 'yu etkinleştirme veya devre dışı bırakma olanağı sağlar.
- Pazardan uyumluluk ve benimseme düzeyini artırırsınız. Birçok büyük kuruluş, çalışanlarının tüm uygulamalarda sorunsuz SSO deneyimleri yaşamalarına (veya amaçlayın) gerek duyar. SSO 'yu kolay hale getirmek önemlidir.
- Son Kullanıcı artışını azaltırsınız. Bu, Son Kullanıcı kullanımını artırabilir ve gelirinizi artırabilir.
- Etki alanları arası kimlik yönetimi ([SCIM](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010)) için sistemi kullanan müşteriler aynı uygulama için sağlama kullanabilir.
- Kullanıcılar, Azure AD SSO kullanarak uygulamalara oturum açtığında ve ayrı kimlik bilgileri gereksinimini kaldırarak güvenlik ve kolaylık sağlar.

> [!TIP]
> Uygulamanızı bir satın alma veya abonelik aracılığıyla diğer şirketler tarafından kullanılmak üzere sunmanızdan sonra, uygulamanızı kendi Azure kiracılarındaki müşteriler için kullanılabilir hale getirebilirsiniz. Bu, çok kiracılı bir uygulama oluşturma olarak bilinir. Bu kavram hakkında genel bir bakış için bkz. [Azure 'Da çok kiracılı uygulamalar](../../dotnet-develop-multitenant-applications.md) ve [Azure Active Directory içinde Kiralama](single-and-multi-tenant-apps.md).

> [!IMPORTANT]
> Uygulamanızı Azure AD galerisinde yayımlamak için belirli hüküm ve koşulları kabul etmeniz gerekir. Başlamadan önce [hüküm ve koşulları](https://azure.microsoft.com/support/legal/active-directory-app-gallery-terms/)okuduğunuzdan ve kabul ettiğinizden emin olun.

Uygulamanızı Azure AD uygulama galerisinde yayımlama adımları şunlardır:
1. Uygulamanız için sağ çoklu oturum açma standardını seçin.
2. Uygulamanızda çoklu oturum açma uygulayın.
3. Azure kiracınızı oluşturun ve uygulamanızı test edin.
4. Belge oluşturun ve yayımlayın.
5. Uygulamanızı gönderebilirsiniz.
6. Microsoft iş ortağı ağı ' na katın.

## <a name="what-is-the-azure-ad-application-gallery"></a>Azure AD Uygulama Galerisi nedir?

- Müşteriler olası en iyi çoklu oturum açma deneyimini bulur.
- Uygulamanın yapılandırması basit ve en düşüktür.
- Hızlı arama, uygulamanızı galeride bulur.
- Ücretsiz, temel ve Premium Azure AD müşterilerinin hepsi bu tümleştirmeyi kullanabilir.
- Karşılıklı müşteriler, adım adım bir yapılandırma öğreticisini alır.
- Etki alanları arası kimlik yönetimi ([SCIM](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010)) için sistemi kullanan müşteriler aynı uygulama için sağlama kullanabilir.

## <a name="prerequisites"></a>Önkoşullar

En az iki Kullanıcı kayıtlı olarak test için kalıcı bir hesaba ihtiyacınız vardır.

- Federasyon uygulamaları (açık KIMLIK ve SAML/WS-beslenir) için, uygulamanın Azure AD uygulama galerisinde listelenen hizmet olarak yazılım (SaaS) modelini desteklemesi gerekir. Kurumsal Galeri uygulamaları, belirli bir müşteriyi değil birden çok müşteri yapılandırmasını desteklemelidir.
- Açık KIMLIK bağlantısı için, uygulamanın çok kiracılı olması ve [Azure AD onay çerçevesinin](../develop/consent-framework.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) uygulama için uygun şekilde uygulanması gerekir. Kullanıcı, herhangi bir müşterinin uygulamaya onay sağlayabilmesi için, oturum açma isteğini ortak bir uç noktaya gönderebilir. Kullanıcı erişimini, kiracı KIMLIĞINE ve Kullanıcı belirtecine göre belirteçte alınan UPN 'yi kontrol edebilirsiniz.
- SAML 2.0/WS-beslenir için, uygulamanızın SP veya ıDP modunda SAML/WS-Beslilik tümleştirmesi tümleştirme yeteneği olmalıdır. İsteği göndermeden önce bu özelliğin düzgün çalıştığından emin olun.
- Parola SSO 'SU için, uygulamanızın form kimlik doğrulamasını desteklediğinden emin olun ve bu sayede, tek oturum açma için beklenen şekilde çalışmaya başlayın.
- En az iki Kullanıcı kayıtlı olarak test için kalıcı bir hesaba ihtiyacınız vardır.

**Geliştiriciler için Azure AD nasıl alınır?**

Tüm Premium Azure AD özellikleriyle 90 gün ücretsiz olan ücretsiz bir test hesabı alabilir ve geliştirme yaparken sizinle birlikte çalışırken genişletilebilir. https://docs.microsoft.com/office/developer-program/office-365-developer-program

## <a name="step-1---choose-the-right-single-sign-on-standard-for-your-app"></a>1. adım-uygulamanız için sağ çoklu oturum açma standardını seçme

Azure AD uygulama galerisinde bir uygulamayı listelemek için desteklenen çoklu oturum açma seçeneklerinden en az birini uygulayın. Çoklu oturum açma seçeneklerini ve müşterilerin bunları Azure AD 'de nasıl yapılandıracağınızı anlamak için bkz. [SSO seçenekleri](../manage-apps/sso-options.md).

Aşağıdaki tabloda ana standartlar karşılaştırılmaktadır: OpenID Connect (OıDC), Security Assertion Markup Language (SAML) ve Web Hizmetleri Federasyonu (WS-beslenir) ile açık kimlik doğrulama 2,0 (OAuth 2,0).

| Özellik| OAuth/OıDC| SAML/WS-Fed |
| - |-|-|
| Web tabanlı çoklu oturum açma| √| √ |
| Web tabanlı çoklu oturum kapatma| √| √ |
| Mobil tabanlı çoklu oturum açma| √| √* |
| Mobil tabanlı çoklu oturum açma| √| √* |
| Mobil uygulamalar için koşullu erişim ilkeleri| √| √* |
| Mobil uygulamalar için sorunsuz MFA deneyimi| √| √* |
| SCıM sağlama| √| √ |
| Erişim Microsoft Graph| √| X |

* Olası, ancak Microsoft örnek veya rehberlik sağlamıyor.

### <a name="oauth-20-and-openid-connect"></a>OAuth 2.0 ve OpenID Connect
OAuth 2,0, yetkilendirme için [sektör standardı](https://oauth.net/2/) bir protokoldür. OpenID Connect (OıDC), OAuth 2,0 protokolünün üstünde oluşturulmuş bir [sektör standart](https://openid.net/connect/) kimlik doğrulama katmanıdır. 

**OAuth/OıDC seçme nedenleri**
- Bu protokollerde bulunan yetkilendirme, uygulamanızın Microsoft Graph API aracılığıyla zengin Kullanıcı ve kurumsal verilere erişmesini ve bunları tümleştirmesini sağlar.
- , Uygulamanız için SSO 'yu benimsediği zaman müşterilerinizin Son Kullanıcı deneyimini basitleştirir. Gerekli olan izin kümelerini kolayca tanımlayabilir, bu, yönetici veya son kullanıcı tarafından otomatik olarak temsil edilir.
- Bu protokollerin kullanılması, müşterilerinizin uygulamalara erişimi denetlemek için koşullu erişim ve Multi-Factor Authentication (MFA) ilkelerini kullanmasına olanak sağlar. 
- Microsoft, geliştirmeye yardımcı olmak için [birden çok teknoloji platformunda kitaplıklar ve kod örnekleri](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Samples) sunar.  

**Dikkate alınması gereken noktalar**
- Uygulamanız için SAML tabanlı çoklu oturum açmayı zaten uyguladıysanız, uygulamanızı galeride almak için yeni bir standart uygulamayı tercih edemeyebilirsiniz.

### <a name="saml-20-or-ws-fed"></a>SAML 2,0 veya WS-Fed

SAML, Web uygulamaları için çok büyük ve yaygın olarak benimsenmiş, [Çoklu oturum açma standardıdır](https://www.oasis-open.org/standards#samlv2.0) . Azure 'un SAML kullanma hakkında daha fazla bilgi edinmek için bkz. [Azure 'un SAML protokolünü nasıl kullandığı](active-directory-saml-protocol-reference.md). 

Web Hizmetleri Federasyonu (WS-beslenir), .NET platformu kullanılarak geliştirilen Web uygulamaları için genellikle kullanılan bir [sektör standardıdır](https://docs.oasis-open.org/wsfed/federation/v1.2/ws-federation.html) .

**SAML seçme nedenleri**
- SAML 2,0, yetişkinlere yönelik bir standarttır ve çoğu teknoloji platformu SAML 2,0 için açık kaynaklı kitaplıkları destekler. 
- Müşterilerinizin SAML SSO 'yu yapılandırmak için bir yönetim arabirimi sağlayabilirsiniz. Microsoft Azure AD için SAML SSO ve SAML 'yi destekleyen diğer kimlik sağlayıcıları yapılandırabilir.

**Dikkate alınması gereken noktalar**
- Mobil uygulamalar için SAML 2,0 veya Wsbesli protokoller kullanırken, çok faktörlü kimlik doğrulaması (MFA) dahil olmak üzere bazı koşullu erişim ilkelerinin düzeyi düşürülmüş bir deneyimle karşılaşacaktır.
- Microsoft Graph erişmek istiyorsanız, gerekli belirteçleri oluşturmak için OAuth 2,0 aracılığıyla yetkilendirmeyi uygulamanız gerekir. 

### <a name="password-based"></a>Parola tabanlı
Parola oluşturma olarak da bilinen parola tabanlı SSO, Kimlik Federasyonu desteklemeyen Web uygulamalarına Kullanıcı erişimini ve parolalarını yönetmenizi sağlar. Ayrıca, çeşitli kullanıcıların, kuruluşunuzun sosyal medya uygulaması hesapları gibi tek bir hesabı paylaşması gereken senaryolar için de kullanışlıdır.


## <a name="step-2---implement-single-sign-on-in-your-app"></a>2. adım-uygulamanızda çoklu oturum açmayı uygulama
Galerideki her uygulamanın desteklenen çoklu oturum açma seçeneklerinden birini uygulaması gerekir. Desteklenen seçenekler hakkında daha fazla bilgi edinmek için bkz. [SSO seçenekleri](../manage-apps/sso-options.md).

OAuth ve OıDC için bkz. [kimlik doğrulama desenleri](v2-app-types.md) ve [Azure Active Directory kod örnekleri](sample-v2-code.md)hakkındaki yönergeler.

SAML ve WS-beslenir için uygulamanızın SP veya ıDP modunda SSO tümleştirmesi yapma özelliği olmalıdır. İsteği göndermeden önce bu özelliğin düzgün çalıştığından emin olun.

Kimlik doğrulaması hakkında daha fazla bilgi edinmek için bkz. [kimlik doğrulaması nedir?](../azuread-dev/v1-authentication-scenarios.md).

> [!IMPORTANT]
> Federasyon uygulamaları (OpenID ve SAML/WS-beslenir) için, uygulamanın hizmet olarak yazılım (SaaS) modelini desteklemesi gerekir. Azure AD Galeri uygulamaları birden çok müşteri yapılandırmasını desteklemelidir ve tek bir müşteriye özgü olmamalıdır.

### <a name="implement-oauth-20-and-openid-connect"></a>OAuth 2,0 ve OpenID Connect uygulama

OpenID Connect için, uygulamanın çok kiracılı olması ve [Azure AD onay çerçevesinin](consent-framework.md) uygulama için düzgün şekilde uygulanması gerekir. Kullanıcı, herhangi bir müşterinin uygulamaya onay sağlayabilmesi için, oturum açma isteğini ortak bir uç noktaya gönderebilir. Kullanıcı erişimini, kiracı KIMLIĞINE ve Kullanıcı belirtecine göre belirteçte alınan UPN 'yi kontrol edebilirsiniz.

Belirli örnekleri gözden geçirmek için bkz. [Microsoft Identity platform kodu örnekleri](sample-v2-code.md). 

Mobil özel örnekleri gözden geçirmek için bkz.: 
* [Android](quickstart-v2-android.md)
* [iOS](quickstart-v2-ios.md)
* [Evrensel Windows Platformu](quickstart-v2-uwp.md)

### <a name="implement-saml-20"></a>SAML 2,0 uygulama

Uygulamanız SAML 2,0 ' yi destekliyorsa, bunu doğrudan bir Azure AD kiracısı ile tümleştirebilirsiniz. Azure AD ile SAML yapılandırması hakkında daha fazla bilgi edinmek için bkz. [SAML tabanlı çoklu oturum açmayı yapılandırma](../manage-apps/configure-saml-single-sign-on.md).

Microsoft, SAML uygulamalarına yönelik kitaplıklar sağlamaz veya önermemektedir. Kullanılabilir çok sayıda açık kaynak kitaplığı vardır.

### <a name="implement-ws-fed"></a>WS-Fed Uygula
ASP.NET Core WS-Fed hakkında daha fazla bilgi edinmek için bkz. [ASP.NET Core WS-Federation ile kullanıcıların kimliğini doğrulama](/aspnet/core/security/authentication/ws-federation).

### <a name="implement-password-vaulting"></a>Parola ululturma

HTML oturum açma sayfasına sahip bir Web uygulaması oluşturun. Uygulamanızın, beklenen şekilde çalışması için çoklu oturum açmayı sağlamak üzere parola oluşturma işlemi yapılabilmesi için, uygulamanızın form kimlik doğrulamasını desteklediğinden emin olun.

## <a name="step-3---implement-scim-user-provisioning-in-your-app"></a>3. adım-uygulamanızda SCıM Kullanıcı sağlamasını uygulama
[SCIM](https://aka.ms/scimoverview) sağlamasını destekleme, isteğe bağlıdır, ancak uygulamanızı oluşturma konusunda çok önerilmiş bir adımdır. SCıM standardını desteklemek kolaydır ve müşterilerin, CSV dosyalarını karşıya yüklemek gibi el ile işlemlere bağlı kalmadan uygulamanızdaki Kullanıcı hesaplarını otomatik olarak oluşturmasına ve güncelleştirmesine izin verir. Ayrıca, müşteriler, SAML JıT gibi bir çözümle gerçekleştirilmeyebilir ve grup üyeliklerini eşitlenmiş halde tutmaya otomatik hale getirebilir. 

### <a name="learn-about-scim"></a>SCıM hakkında bilgi edinin
Müşterilerinizin SCıM standartları ve avantajları hakkında daha fazla bilgi edinmek için bkz. [SCIM ile sağlama-](https://aka.ms/scimoverview)Başlarken.

### <a name="understand-the-azure-ad-scim-implementation"></a>Azure AD SCıM uygulamasını anlama
Azure AD SCıM uygulamasıyla ilgili daha fazla bilgi edinmek için bkz. [SCIM uç noktası oluşturma ve Azure AD ile Kullanıcı sağlamayı yapılandırma](../app-provisioning/use-scim-to-provision-users-and-groups.md).

### <a name="implement-scim"></a>SCıM 'i uygulama
Azure AD, bir SCıM uç noktası oluşturmanıza yardımcı olacak [başvuru kodu](https://aka.ms/scimoverview) sağlar. GitHub 'da bulabileceğiniz birçok üçüncü taraf kitaplığı/başvurusu da vardır.  

## <a name="step-4---create-your-azure-tenant-and-test-your-app"></a>4. adım-Azure kiracınızı oluşturun ve uygulamanızı test edin

Uygulamanızı test etmek için bir Azure AD kiracısına sahip olmanız gerekir. Geliştirme ortamınızı ayarlamak için bkz. [hızlı başlangıç: kiracı ayarlama](quickstart-create-new-tenant.md).

Alternatif olarak, Azure AD kiracısı her Microsoft 365 abonelikle birlikte gelir. Ücretsiz bir Microsoft 365 geliştirme ortamı ayarlamak için, bkz. [Microsoft 365 Geliştirici programına ekleme](/office/developer-program/microsoft-365-developer-program).

Bir kiracınız olduktan sonra, çoklu oturum açma ve [sağlama](../app-provisioning/use-scim-to-provision-users-and-groups.md#integrate-your-scim-endpoint-with-the-aad-scim-client)işlemlerini test edin. 

**OıDC veya Oath uygulamaları için** uygulamanızı çok kiracılı bir uygulama olarak [kaydedin](quickstart-register-app.md) . Desteklenen hesap türlerinde herhangi bir kurumsal dizin ve kişisel Microsoft hesabı seçeneğinde bulunan hesapları seçin.

**SAML ve WS-Bessel tabanlı uygulamalar için**, Azure AD 'de genel bir SAML şablonu kullanarak [SAML tabanlı çoklu oturum açma uygulamalarını yapılandırırsınız](../manage-apps/configure-saml-single-sign-on.md) .

Ayrıca, gerekirse [tek kiracılı bir uygulamayı birden çok kiracıya dönüştürebilirsiniz](howto-convert-app-to-be-multi-tenant.md) .


## <a name="step-5---create-and-publish-documentation"></a>5. adım-belge oluşturma ve yayımlama

### <a name="documentation-on-your-site"></a>Sitenizdeki belgeler

Benimseme kolaylığı, kurumsal yazılım kararlarında önemli bir faktördür. Kolayca takip eden belgeleri Temizleme, müşterilerinizin benimseme yolculuğunda müşterilerinizi destekler ve destek maliyetlerini azaltır. Binlerce yazılım satıcısı ile birlikte çalışarak, Microsoft nelerin çalıştığını gördük.

Sitenizdeki tüm belgelerinize, en azından aşağıdaki öğeleri eklemesi önerilir.

* SSO işlevselliğine giriş
  * Desteklenen protokoller
  * Sürüm ve SKU
  * Belge bağlantılarıyla desteklenen kimlik sağlayıcıları listesi
* Uygulamanız için lisans bilgileri
* SSO 'yu yapılandırmak için rol tabanlı erişim denetimi
* SSO yapılandırma adımları
  * Sağlayıcıdan beklenen değerler içeren SAML için Kullanıcı arabirimi yapılandırma öğeleri
  * Kimlik sağlayıcılarına geçirilecek hizmet sağlayıcı bilgileri
* Eğer OıDC/OAuth ise
  * Business gerekçeler ile onay için gereken izinlerin listesi
* Pilot kullanıcılar için test adımları
* Hata kodları ve iletileri dahil sorun giderme bilgileri
* Müşteriler için destek mekanizmaları
* Desteklenen kaynaklar ve öznitelikler de dahil olmak üzere SCıM uç noktanız hakkındaki ayrıntılar

### <a name="documentation-on-the-microsoft-site"></a>Microsoft sitesindeki belgeler

Uygulamanızı Azure Marketi 'nde da yayımlayan Azure Active Directory Uygulama galerisinde, Microsoft, adım adım işlemi açıklayan, karşılıklı müşterilerimiz için belge oluşturacaktır. [Burada](../saas-apps/tutorial-list.md)bir örnek görebilirsiniz. Bu belge, galeriye gönderiminiz temel alınarak oluşturulur ve GitHub hesabınızı kullanarak uygulamanızda değişiklik yaparsanız onu kolayca güncelleştirebilirsiniz.


## <a name="step-6---submit-your-app"></a>6. adım-uygulamanızı gönderme

Uygulama tümleştirmesinin Azure AD ile çalışıp çalışmadığını test ettikten sonra, uygulama isteğinizi [Microsoft Application Network portalında](https://microsoft.sharepoint.com/teams/apponboarding/Apps)gönderebilirsiniz.

Portalda ilk kez oturum açmaya çalıştığınızda, iki ekranda bir tane görüntülenir. 

"İşe yaramadı" iletisini alırsanız, [Azure AD SSO tümleştirme ekibine](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)başvurmanız gerekecektir. İsteği göndermek için kullanmak istediğiniz e-posta hesabını belirtin. Gibi bir iş e-posta adresi `name@yourbusiness.com` tercih edilir. Azure AD ekibi, hesabı Microsoft uygulama ağı portalına ekler.

"Erişim ıste" sayfası görürseniz, iş gerekçe ' nı doldurup **erişim iste**' yi seçin.

Hesap eklendikten sonra, Microsoft uygulama ağı portalında oturum açabilir ve giriş sayfasında **Isteği gönder (ISV)** kutucuğunu seçerek isteği gönderebilirsiniz.

![Giriş sayfasında Istek (ISV) kutucuğunu gönder](./media/howto-app-gallery-listing/homepage.png)

### <a name="issues-on-logging-into-portal"></a>Portalda oturum açma sorunları

Oturum açarken bu hatayı görüyorsanız, sorun hakkında ayrıntılı bilgi ve bu sorunu nasıl giderebilirim?

* Oturum açma bilgileriniz aşağıda gösterildiği gibi engellenmişse:

  ![Galerideki uygulamayı çözümleyen sorunlar](./media/howto-app-gallery-listing/blocked.png)

**Ne oluyor:**

Konuk Kullanıcı aynı zamanda bir Azure AD olan bir giriş kiracıya federe olur. Konuk Kullanıcı yüksek risk altında. Microsoft, yüksek riskli kullanıcıların kaynaklarına erişmelerine izin vermez. Tüm yüksek riskli kullanıcılar (çalışanlar veya konuklar/satıcılar) Microsoft kaynaklarına erişmek için riskleri düzeltmeli/kapatacaktır. Konuk kullanıcılar için bu kullanıcı riski, ana kiracıdan gelir ve ilke, kaynak kiracısından gelir (Bu durumda Microsoft).
 
**Güvenli çözümler:**

* MFA kayıtlı Konuk kullanıcılar kendi Kullanıcı riskini düzeltir. Bu işlem, Konuk Kullanıcı tarafından güvenli bir parola değişikliği veya sıfırlama (ana https://aka.ms/sspr) KIRACıSıNDA MFA ve SSPR gerekir) gerçekleştiriliyor tarafından yapılabilir. Güvenli parola değiştirme veya sıfırlama özelliği, Azure AD 'de başlatılmalıdır ve şirket içi değil.

* Konuk kullanıcıların yöneticileri, riskini düzeltir. Bu durumda, yönetici bir parola sıfırlama işlemi gerçekleştirir (geçici parola oluşturma). Bu, kimlik koruması gerektirmez. Konuk kullanıcının Yöneticisi ' ne gidebilir https://aka.ms/RiskyUsers ve ' Parolayı Sıfırla ' öğesine tıklayabilirsiniz.

* Konuk kullanıcıların yöneticileri, riskini kapatır/kapatabilir. Bu, kimlik korumasına gerek kalmaz. Yönetici öğesine gidebilir https://aka.ms/RiskyUsers ve ' Kullanıcı riskini Kapat ' seçeneğine tıklayabilir. Ancak, bu, Kullanıcı riskini kapatmadan önce bunun yanlış bir pozitif risk değerlendirmesi olduğundan emin olmak için yöneticinin, süresi dolan bir süre olması gerekir. Aksi takdirde, araştırma yapmadan risk değerlendirmesi ortadan kaldırarak ve Microsoft 'un kaynaklarını riske sokuyor.

> [!NOTE]
> Erişim ile ilgili herhangi bir sorununuz varsa [Azure AD SSO tümleştirme ekibine](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)başvurun.

### <a name="implementation-specific-options"></a>Uygulamaya özgü seçenekler
Uygulamanızı OpenID Connect kullanarak galerinin listesine eklemek istiyorsanız, gösterildiği gibi **OpenID connect & OAuth 2,0** ' ı seçin.

![Galerideki bir OpenID Connect uygulamasını listeleme](./media/howto-app-gallery-listing/openid.png)

**Saml 2,0** veya **WS-besu** kullanarak uygulamanızı Galeriye eklemek Istiyorsanız, gösterildiği gıbı **SAML 2.0/WS-beslenir** ' i seçin.

![Bir SAML 2,0 veya WS-Fed uygulamasını galeride listeleme](./media/howto-app-gallery-listing/saml.png)

Uygulamanızı parola SSO kullanarak galerideki listeye eklemek istiyorsanız, gösterilen şekilde **parola SSO (Kullanıcı adı & parola)** seçeneğini belirleyin.

![Galerideki parola SSO uygulamasını listeleme](./media/howto-app-gallery-listing/passwordsso.png)

Kullanıcı hazırlama için bir [SCIM](../app-provisioning/use-scim-to-provision-users-and-groups.md) 2,0 uç noktası uygulamadıysanız gösterilen seçeneği belirleyin. Ekleme isteğinde şema sağlanırken, şemanızı indirmek için lütfen [buradaki](../app-provisioning/export-import-provisioning-configuration.md) yönergeleri izleyin. Galeri uygulamasını oluşturmak için Galeri dışı uygulamayı test ederken yapılandırdığınız şemayı kullanacağız. 

   ![Kullanıcı hazırlama isteği](./media/howto-app-gallery-listing/user-provisioning.png)

### <a name="update-or-remove-an-existing-listing"></a>Mevcut bir listeyi güncelleştirme veya kaldırma

Mevcut Galeri uygulamasını [Microsoft uygulama ağı portalında](https://microsoft.sharepoint.com/teams/apponboarding/Apps)güncelleştirebilir veya kaldırabilirsiniz.

![Galerideki bir SAML uygulamasını listeleme](./media/howto-app-gallery-listing/updateorremove.png)

> [!NOTE]
> Erişim ile ilgili herhangi bir sorununuz varsa, hesabınızı oluşturmak için önceki bölüme bakın. Bu işe yaramazsa [Azure AD SSO tümleştirme ekibine](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)başvurun.

### <a name="list-requests-by-customers"></a>İstekleri müşterilere göre Listele

Müşteriler, **müşterilerin**  >  **yeni istek göndermesi** için uygulama istekleri seçerek bir uygulamayı listeleme isteği gönderebilir.

![Müşteri tarafından istenen uygulamalar kutucuğunu gösterir](./media/howto-app-gallery-listing/customer-submit-request.png)

Müşteri tarafından istenen uygulamaların akışı aşağıda verilmiştir.

![Müşteri tarafından istenen uygulamalar akışını gösterir](./media/howto-app-gallery-listing/customer-request-2.png)


### <a name="timelines"></a>Zaman çizelgeleri

Galerideki bir SAML 2,0 veya WS-Fed uygulamasını listeleme işleminin zaman çizelgesi 7 ila 10 iş gündür.

![Galerideki bir SAML uygulamasını listelemek için zaman çizelgesi](./media/howto-app-gallery-listing/timeline.png)

Galerideki bir OpenID Connect uygulamasını listeleme işleminin zaman çizelgesi 2 ile 5 iş gününe kadar olur.

![Galerideki bir OpenID Connect uygulamasını listelemek için zaman çizelgesi](./media/howto-app-gallery-listing/timeline2.png)

### <a name="escalations"></a>Yükseltmeleri

Tüm yürüyen değişiklikler için [Azure AD SSO tümleştirme ekibine](mailto:SaaSApplicationIntegrations@service.microsoft.com)e-posta gönderin ve mümkün olan en kısa sürede yanıt vereceğiz.


## <a name="step-7---join-the-microsoft-partner-network"></a>7. adım-Microsoft iş ortağı ağı 'nı birleştirin
Microsoft İş Ortağı Ağı, özel kaynaklara, programlara, araçlara ve bağlantılara anında erişim sağlar. Ağa katılarak ve pazara git planınızı oluşturmak için bkz. [ticari müşterilere ulaşın](https://partner.microsoft.com/explore/commercial#gtm).


## <a name="next-steps"></a>Sonraki adımlar
* [Bir SCıM uç noktası oluşturun ve Kullanıcı sağlamasını yapılandırın](../app-provisioning/use-scim-to-provision-users-and-groups.md)
* [Azure AD için kimlik doğrulama senaryoları](authentication-flows-app-scenarios.md)
