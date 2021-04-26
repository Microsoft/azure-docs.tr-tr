---
title: Çok kiracılı uygulamalar için otomatik Kullanıcı sağlamayı etkinleştirme-Azure AD
description: Otomatik sağlamayı etkinleştirmek için bağımsız yazılım satıcıları Kılavuzu
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: reference
ms.workload: identity
ms.date: 07/23/2019
ms.author: kenwith
ms.reviewer: zhchia
ms.openlocfilehash: 7bd0fc634109beb6cc674d89f56666e7035d33ef
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99255704"
---
# <a name="enable-automatic-user-provisioning-for-your-multi-tenant-application"></a>Çok kiracılı uygulamanız için otomatik Kullanıcı sağlamayı etkinleştirme

Otomatik Kullanıcı sağlama, hizmet olarak yazılım uygulamaları gibi hedef sistemlerdeki Kullanıcı kimliklerinin oluşturulmasını, bakımını ve kaldırılmasını otomatik hale getirme işlemidir.

## <a name="why-enable-automatic-user-provisioning"></a>Otomatik Kullanıcı sağlama neden etkinleştirilsin?

Kullanıcının ilk oturum açması Kullanıcı tarafından sağlanmadan önce uygulamada bir Kullanıcı kaydının mevcut olmasını gerektiren uygulamalar. Sizin için bir hizmet sağlayıcı olarak kullanabileceğiniz avantajlar ve müşterilerinizin avantajları vardır.

### <a name="benefits-to-you-as-the-service-provider"></a>Hizmet sağlayıcı olarak size faydalanır

* Microsoft Identity platformunu kullanarak uygulamanızın güvenliğini artırın.

* Uygulamanızı benimsemek için gerçek ve algılanan müşteri çabalarını azaltın.

* Etki alanları arası kimlik yönetimi (SCıM) tabanlı sağlama için sistem 'i kullanarak otomatik Kullanıcı sağlama için birden çok kimlik sağlayıcısıyla (IDPs) tümleştirmeye yönelik maliyetlerinizi azaltın.

* Müşterilerin Kullanıcı sağlama sorunlarını gidermenize yardımcı olmak için zengin Günlükler sunarak destek maliyetlerini azaltın.

* [Azure AD uygulama galerisinde](https://azuremarketplace.microsoft.com/marketplace/apps)uygulamanızın görünürlüğünü artırın.

* Uygulama öğreticileri sayfasında öncelikli bir liste alın.

### <a name="benefits-to-your-customers"></a>Müşterilerinizin avantajları

* Rolleri değiştirme veya kuruluştan uygulamanızı bırakma kullanıcıları için uygulamanıza erişimi otomatik olarak kaldırarak güvenliği artırın.

* El ile sağlama ile ilişkili insan hatasından ve yinelenen çalışmalardan kaçınarak uygulamanız için Kullanıcı yönetimini kolaylaştırın.

* Özel olarak geliştirilmiş sağlama çözümlerini barındırma ve sürdürme maliyetlerini azaltın.

## <a name="choose-a-provisioning-method"></a>Sağlama yöntemini seçin

Azure AD, uygulamanız için otomatik Kullanıcı sağlamayı etkinleştirmek üzere çeşitli Tümleştirme yolları sağlar.

* [Azure AD sağlama hizmeti](../app-provisioning/user-provisioning.md) , Azure AD 'den uygulamanıza (giden sağlama) ve UYGULAMANıZDAN Azure AD 'ye (gelen sağlama) yönelik sağlama ve sağlamayı kaldırmayı yönetir. Hizmet, uygulamanız tarafından sağlanmış olan etki alanları arası kimlik yönetimi (SCıM) Kullanıcı yönetimi API uç noktaları için sisteme bağlanır.

* [Microsoft Graph](/graph/)kullanılırken, UYGULAMANıZ Microsoft Graph API 'Sini SORGULAYARAK Azure AD 'den uygulamanıza yönelik kullanıcı ve grupların gelen ve giden sağlamasını yönetir.

* Uygulamanız Federasyon için SAML kullanıyorsa, tam zamanında Security Assertion Markup Language (SAML JıT) Kullanıcı sağlama etkinleştirilebilir. Kullanıcı sağlamak için SAML belirtecinde gönderilen talep bilgilerini kullanır.

Uygulamanız için hangi tümleştirme seçeneğinin kullanılacağını belirlemede yardımcı olması için üst düzey karşılaştırma tablosuna bakın ve sonra her bir seçenek hakkında daha ayrıntılı bilgi için bkz..

| Otomatik sağlama tarafından etkinleştirilen veya geliştirilmiş yetenekler| Azure AD sağlama hizmeti (SCıM 2,0)| Microsoft Graph API (OData v 4.0)| SAML JıT |
|---|---|---|---|
| Azure AD 'de Kullanıcı ve Grup Yönetimi| √| √| Yalnızca Kullanıcı |
| Şirket içi Active Directory eşitlenen kullanıcıları ve grupları yönetme| √*| √*| Yalnızca Kullanıcı * |
| Microsoft 365 verilerine (takımlar, SharePoint, e-posta, takvim, belgeler vb.) erişim sağlama sırasında kullanıcılar ve grupların ötesinde verilere erişin| X +| √| X |
| İş kurallarına göre Kullanıcı oluşturma, okuma ve güncelleştirme| √| √| √ |
| İş kurallarına göre kullanıcıları silme| √| √| X |
| Azure portal tüm uygulamalar için otomatik Kullanıcı sağlamasını yönetme| √| X| √ |
| Birden çok kimlik sağlayıcısını destekleme| √| X| √ |
| Konuk hesaplarını (B2B) destekleme| √| √| √ |
| Kurumsal olmayan hesapları destekleme (B2C)| X| √| √ |

<sup>*</sup> – Azure AD Connect kullanıcıları AD 'den Azure AD 'ye eşitlemek için kurulum gereklidir.  
<sup>+</sup >– Sağlama için SCıM kullanılması, uygulamanızı başka amaçlar için Microsoft Graph tümleştirmenizi gerektirmez.

## <a name="azure-ad-provisioning-service-scim"></a>Azure AD sağlama hizmeti (SCıM)

Azure AD sağlama hizmetleri, birçok kimlik sağlayıcısı (IDPs) ve uygulamalar (örn. bolluk, G Suite, Dropbox) tarafından desteklenen sağlama için endüstri standardı olan [SCIM](https://aka.ms/SCIMOverview)'i kullanır. Her türlü SCıM uyumlu IDP, SCıM uç noktanıza bağlanabildikleri için Azure AD 'nin yanı sıra IDPs 'yi desteklemek istiyorsanız, Azure AD sağlama hizmetini kullanmanızı öneririz. Basit bir/User uç noktası oluşturma, kendi eşitleme motorınızı sürdürmek zorunda kalmadan sağlamayı etkinleştirebilirsiniz. 

Azure AD sağlama hizmeti kullanıcılarının SCıM hakkında daha fazla bilgi için bkz.: 

* [SCıM standardı hakkında daha fazla bilgi edinin](https://aka.ms/SCIMOverview)

* [Kullanıcıları ve grupları Azure Active Directory uygulamalara otomatik olarak sağlamak için etki alanları arası kimlik yönetimi (SCıM) için sistem kullanma](../app-provisioning/use-scim-to-provision-users-and-groups.md)

* [Azure AD SCıM uygulamasını anlama](../app-provisioning/use-scim-to-provision-users-and-groups.md)

## <a name="microsoft-graph-for-provisioning"></a>Sağlama için Microsoft Graph

Sağlama için Microsoft Graph kullandığınızda, grafikte kullanılabilen tüm zengin Kullanıcı verilerine erişebilirsiniz. Kullanıcı ve grupların ayrıntılarına ek olarak, kullanıcının rolleri, Yöneticisi ve doğrudan raporları, sahip olunan cihazlar ve [Microsoft Graph](/graph/api/overview)bulunan yüzlerce diğer veri parçası gibi ek bilgiler de getirebilirsiniz. 

Microsoft 365, Microsoft Azure veya Enterprise Mobility Suite gibi Microsoft bulut hizmetlerine abone olurken, 15.000.000 ' den fazla kuruluş ve Fortune 500 şirketinin 90 ' i Azure AD 'yi kullanır. Uygulamanızı, çalışan ekleme (ve sonlandırma), profil bakımı ve daha fazlası gibi yönetim iş akışlarıyla tümleştirmek için Microsoft Graph kullanabilirsiniz. 

Sağlama için Microsoft Graph kullanma hakkında daha fazla bilgi edinin:

* [Microsoft Graph giriş sayfası](https://developer.microsoft.com/graph)

* [Microsoft Graph’a genel bakış](/graph/overview)

* [Microsoft Graph auth 'ye Genel Bakış](/graph/auth/)

* [Microsoft Graph kullanmaya başlama](https://developer.microsoft.com/graph/get-started)

## <a name="using-saml-jit-for-provisioning"></a>Sağlama için SAML JıT kullanma

Kullanıcıları yalnızca uygulamanızda ilk kez oturum açtığında sağlamak istiyorsanız ve kullanıcıların otomatik olarak sağlanması gerekmiyorsa, SAML JıT bir seçenektir. Uygulamanızın SAML JıT kullanmak için SAML 2,0 ' ü desteklemesi gerekir.

SAML JıT, uygulama içindeki kullanıcı bilgilerini oluşturmak ve güncelleştirmek için SAML belirtecindeki talep bilgilerini kullanır. Müşteriler gerektiğinde bu gerekli talepleri Azure AD uygulamasında yapılandırabilir. Bazen JıT sağlamanın uygulama tarafında etkinleştirilmesi gerekir, böylelikle müşteri bu özelliği kullanabilir. SAML JıT, Kullanıcı oluşturmak ve güncelleştirmek için yararlıdır, ancak uygulamadaki kullanıcıları silemez veya devre dışı bırakamıyorum.

## <a name="next-steps"></a>Sonraki Adımlar

* [Uygulamanız için çoklu oturum açmayı etkinleştirme](../develop/v2-howto-app-gallery-listing.md)

* Microsoft 'un sitesinde belge oluşturmak için [uygulama listelemeyi ve iş ortaklarınızı](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx) Microsoft Ile birlikte gönderebilirsiniz.

* [Microsoft iş ortağı ağı (ücretsiz) ekleyin ve pazara git planınızı oluşturun](https://partner.microsoft.com/explore/commercial).
