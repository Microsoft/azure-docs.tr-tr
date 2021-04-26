---
title: Azure Active Directory B2B işbirliği nedir?
description: Azure Active Directory B2B işbirliği, kaynakları güvenli bir şekilde paylaşmak ve şirket dışındaki ortaklarla işbirliği yapmak için konuk kullanıcı erişimini destekler.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: overview
ms.date: 03/19/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 231d3ab81c2376aa8a2a4bf182e1b5e9799e51d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104669789"
---
# <a name="what-is-guest-user-access-in-azure-active-directory-b2b"></a>Azure Active Directory B2B’de konuk kullanıcı erişimi nedir?

Azure Active Directory (Azure AD) işletmeden işletmeye (B2B) işbirliği, Konuk kullanıcıları kuruluşunuzla işbirliği yapmaya davet etmenize olanak tanıyan dış kimliklerden oluşan bir özelliktir. B2B işbirliğiyle, şirketinizin uygulamalarını ve hizmetlerini başka bir kuruluştan Konuk kullanıcılarla güvenli bir şekilde paylaşabilir ve kendi şirket verileriniz üzerinde denetim sağlayabilirsiniz. Azure AD veya bir BT departmanı olmasa da şirket dışındaki ortaklarla güvenli ve emniyetli şekilde çalışın. Basit bir davet ve kullanım işlemi, ortakların şirketinizin kaynaklarına erişmek için kendi kimliklerini kullanmasına olanak sağlar. Geliştiriciler, self servis kayıt portalları gibi uygulamalar yazmak veya davet işlemini özelleştirmek için Azure AD işletmeler arası API’leri kullanabilir. Konuk kullanıcılarla ilgili lisanslama ve fiyatlandırma bilgileri için [Azure Active Directory fiyatlandırmasına](https://azure.microsoft.com/pricing/details/active-directory/)bakın.  

> [!IMPORTANT]
> - **4 ocak 2021 tarihinden itibaren** Google, [WebView oturum açma desteğini kullanımdan](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html)kaldırır. Gmail ile Google Federasyonu veya self servis kaydolma kullanıyorsanız, [iş kolu yerel uygulamalarınızı uyumluluk için test](google-federation.md#deprecation-of-webview-sign-in-support)etmeniz gerekir.
> - Microsoft, **2021 Ekim 'Den itibaren**, B2B işbirliği senaryoları Için YÖNETILMEYEN Azure AD hesapları ve kiracılar oluşturarak artık davetlerin kullanımını desteklememektedir. Hazırlık aşamasında, müşterilerin [e-posta bir kerelik geçiş kodu kimlik doğrulamasını](one-time-passcode.md)kabul etmelerini öneririz. Bu genel önizleme özelliğiyle ilgili geri bildirimlerinize hoş geldiniz ve işbirliği yapmak için daha fazla yol oluşturmak heyecanlıyız.

## <a name="collaborate-with-any-partner-using-their-identities"></a>Kimliklerini kullanarak herhangi bir ortak ile işbirliği yapma

Azure AD B2B ile ortaklar kendi kimlik yönetimi çözümlerini kullanır; bu nedenle kuruluşunuz için harici bir yönetim iş yükü söz konusu değildir. Konuk kullanıcılar, kendi iş, okul veya sosyal kimlikleriyle uygulama ve hizmetlerinizde oturum açar.

- İş ortağı kendi kimliklerini ve kimlik bilgilerini kullanır. Azure AD gerekli değildir.
- Harici hesapları veya parolaları yönetmeniz gerekmez.
- Hesapları eşitlemeniz veya hesap yaşam döngülerini yönetmeniz gerekmez.  

## <a name="easily-invite-guest-users-from-the-azure-ad-portal"></a>Konuk kullanıcılarını Azure AD portalından kolayca davet etme

Yönetici olarak, Azure portalda kuruluşunuza kolayca konuk kullanıcılar ekleyebilirsiniz.

- Azure AD 'de yeni bir kullanıcı eklemeye benzer şekilde [Yeni bir Konuk kullanıcı oluşturun](b2b-quickstart-add-guest-users-portal.md) .
- Konuk kullanıcıları uygulamalara veya gruplara atayın.
- Kullanım bağlantısı içeren bir davet e-postası gönderin veya paylaşmak istediğiniz bir uygulamaya doğrudan bağlantı gönderin.

![Yeni Konuk Kullanıcı daveti giriş sayfasını gösteren ekran görüntüsü](media/what-is-b2b/add-a-b2b-user-to-azure-portal.png)

- Konuk kullanıcılar oturum açmak için birkaç basit kullanım [adımını](redemption-experience.md) izler.

![İzinleri gözden geçir sayfasını gösteren ekran görüntüsü](media/what-is-b2b/consentscreen.png)


## <a name="use-policies-to-securely-share-your-apps-and-services"></a>Uygulama ve hizmetlerinizi güvenli bir şekilde paylaşmak için ilkeleri kullanma

Şirket içeriğinizi korumak için yetkilendirme ilkeleri kullanabilirsiniz. Multi-Factor Authentication gibi koşullu erişim ilkeleri zorlanabilir:

- Kiracı düzeyinde.
- Uygulama düzeyinde.
- Belirli konuk kullanıcıların kurumsal uygulamalarını ve verilerini koruması için.

![Koşullu erişim seçeneğini gösteren ekran görüntüsü](media/what-is-b2b/tutorial-mfa-policy-2.png)



## <a name="let-application-and-group-owners-manage-their-own-guest-users"></a>Uygulama ve grup sahiplerinin kendi konuk kullanıcılarını yönetmesine izin verme

Uygulama sahiplerinin, Microsoft uygulaması olsun olmasın, paylaşmak istedikleri herhangi bir uygulamaya doğrudan konuk kullanıcı ekleyebilmeleri için uygulama sahiplerine konuk kullanıcı yönetimi yetkisini devredebilirsiniz.

- Yöneticiler, self servis uygulama ve grup yönetimini ayarlar.
- Yönetici olmayan kullanıcılar, uygulamalara veya gruplara konuk kullanıcılar eklemek için [Erişim Paneli](https://myapps.microsoft.com)’ni kullanır.

![Konuk Kullanıcı için erişim panelini gösteren ekran görüntüsü](media/what-is-b2b/access-panel-manage-app.png)

## <a name="customize-the-onboarding-experience-for-b2b-guest-users"></a>B2B Konuk kullanıcıları için ekleme deneyimini özelleştirme

Dış iş ortaklarınızı, kuruluşunuzun ihtiyaçlarına göre özelleştirilmiş yollarla Pano 'ya taşıyın.

- [Dış kullanıcılara erişimi yöneten](../governance/entitlement-management-external-users.md#how-access-works-for-external-users)ilkeleri yapılandırmak IÇIN [Azure AD Yetkilendirme Yönetimi](../governance/entitlement-management-overview.md) 'ni kullanın.
- Ekleme deneyimlerinizi özelleştirmek için [B2B işbirliği davetiyesi API 'lerini](/graph/api/resources/invitation) kullanın.

## <a name="integrate-with-identity-providers"></a>Kimlik sağlayıcılarıyla tümleştirin

Azure AD, Facebook, Microsoft hesapları, Google veya kurumsal kimlik sağlayıcıları gibi dış kimlik sağlayıcılarını destekler. Kimlik sağlayıcılarıyla Federasyonu ayarlayabilirsiniz. böylece, dış kullanıcılarınızın yalnızca uygulamanız için yeni bir hesap oluşturmak yerine mevcut sosyal veya kurumsal hesaplarıyla oturum açabilirler. [Dış kimlikler için kimlik sağlayıcıları](identity-providers.md)hakkında daha fazla bilgi edinin.

![Kimlik sağlayıcıları sayfasını gösteren ekran görüntüsü](media/what-is-b2b/identity-providers.png)


## <a name="create-a-self-service-sign-up-user-flow"></a>Self Servis kaydolma Kullanıcı akışı oluşturma

Self Servis kaydolma Kullanıcı akışıyla, uygulamalarınıza erişmek isteyen dış kullanıcılar için bir kaydolma deneyimi oluşturabilirsiniz. Kaydolma akışının bir parçası olarak, farklı sosyal veya kurumsal kimlik sağlayıcılarının seçeneklerini sağlayabilir ve Kullanıcı hakkında bilgi toplayabilirsiniz. [Self servis kaydolma ve nasıl ayarlanacağı](self-service-sign-up-overview.md)hakkında bilgi edinin.

Ayrıca, self servis kaydolma Kullanıcı akışlarınızı dış bulut sistemleriyle bütünleştirmek için [API bağlayıcıları](api-connectors-overview.md) kullanabilirsiniz. Özel onay iş akışlarıyla bağlanabilir, kimlik doğrulaması gerçekleştirebilir, Kullanıcı tarafından belirtilen bilgileri doğrulayabilir ve daha fazlasını yapabilirsiniz.

![Kullanıcı akışları sayfasını gösteren ekran görüntüsü](media/what-is-b2b/self-service-sign-up-user-flow-overview.png)
<!--TODO: Add screenshot with API connectors -->

## <a name="next-steps"></a>Sonraki adımlar

- [Dış Kimlikler fiyatlandırması](external-identities-pricing.md)
- [Portalda B2B işbirliği konuk kullanıcıları ekleme](add-users-administrator.md)
- [Davet kullanımı işlemini anlama](redemption-experience.md)