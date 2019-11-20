---
title: Visual Studio App Center ve Azure hizmetleriyle mobil uygulamalarınıza kimlik doğrulaması ekleme
description: Kullanıcı kimlik doğrulamasını ayarlamaya ve mobil uygulamaların sosyal hesaplar, Azure Active Directory ve özel kimlik doğrulamasıyla kimlik doğrulaması yapmasını sağlayan App Center gibi hizmetler hakkında bilgi edinin.
author: elamalani
ms.assetid: 34a8a070-2222-4faf-9090-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 8885f6cd91179f4dee6456277d47581f68c21723
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795763"
---
# <a name="add-authentication-and-manage-user-identities-in-your-mobile-apps"></a>Mobil uygulamalarınızdaki kimlik doğrulaması ve Kullanıcı kimliklerini yönetme

Kullanıcı görünümünü ve uygulamanızda davranışlarını, geliştiricilerin kendilerine yönelik özel deneyimler oluşturarak daha iyi bir şekilde devreye girmesini sağlar. Bir uygulama geliştiricisi, kuruluşunuzdaki kullanıcılar için işbirliği uygulaması oluşturup bir sonraki sosyal ağ platformunu oluştururken, kullanıcıların kimliğini doğrulamak ve Kullanıcı kimliklerini yönetmek için bir yönteme ihtiyaç duyarsınız. Bir kimlik yönetimi hizmetinin kullanılması, bir mobil arka uç hizmetinin en önemli özelliklerinden biridir.

Mobil uygulamalarınızda Kullanıcı kimlik doğrulamasını etkinleştirmek için aşağıdaki hizmetleri kullanın.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center auth](/appcenter/auth/) , geliştiricilerin kullanıcıların kimliğini doğrulamasını ve Kullanıcı kimliklerini yönetmesini sağlayan bulut tabanlı bir kimlik yönetimi hizmetidir. App Center kimlik doğrulaması Ayrıca, geliştiricilerin diğer hizmetlerde [Kullanıcı verilerini görüntülemek](/appcenter/data/index) ve hatta [tek tek cihazlar yerine kullanıcılara anında iletme bildirimleri göndermek](/appcenter/push/push-to-user#app-center-auth-set-identity)için kullanıcı kimliğinden yararlanmasını sağlayan App Center diğer bölümleriyle tümleştirilir. 

**Temel Özellikler**
- **Azure Active Directory B2C tarafından destekleniyor (Azure AD B2C)** 
    - Kurumsal sınıf.
    - Yüksek oranda kullanılabilir.
    - Güvenli ve küresel hizmet.
- **Kendi kimliğinizi taşıyın** ve Auth0 ve Firebase gibi diğer popüler kimlik ve erişim yönetimi sağlayıcılarını kullanın.
- **AAD desteği** 
    - Mevcut AAD kiracılarını bağlayın. 
    - Şirket etki alanında kimlik doğrulamasını etkinleştirin.
    - Gizli verilere erişimi yönetin.
- App Center SDK ile MSAL kitaplığı sarmalayarak **Basit Kullanıcı deneyimi** ve MıKNATıIK SDK deneyimi.
- **Platform desteği** -IOS, Android, Xamarin, yerel olarak tepki verme.

**Başvur**
- [App Center kaydolun](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs) 
- [App Center kimlik doğrulaması ile çalışmaya başlama](/appcenter/auth/)

## <a name="azure-active-directory-b2c"></a>Azure Active Directory B2C
[Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c/) , geliştiricilerin müşterilerinin kimliğini doğrulamasını sağlayan işletmeden müşteriye kimlik yönetimi hizmetidir. Bu teknik etiket Hizmeti, geliştiricilerin Web, Masaüstü, mobil veya tek sayfalı uygulamalarla nasıl güvenli bir şekilde etkileşim kuracağını özelleştirip denetlemesine olanak tanır. Azure AD B2C kullanarak, kullanıcılar kaydolabilir, oturum açabilir, parolaları sıfırlayabilir ve profilleri düzenleyebilir. Azure AD B2C, OpenID Connect ve OAuth 2,0 protokollerinin bir formunu uygular. 

**Temel Özellikler**
- Tercih edilen kimlik sağlayıcısıyla müşterilerin kimliklerini güvenli bir şekilde doğrulayın.
- **Müşteri kimliği ve erişim yönetimi**.
- Facebook, GitHub, Google, LinkedIn, Twitter, WeChat, Weibo gibi sosyal oturum açmalar desteklenir.
- Çeşitli platformlarda kimlik yönetimine olanak sağlamak için OpenID Connect veya SAML gibi **sektör standardı protokollerini** kullanarak kullanıcı hesaplarınıza bağlanın.
- Markalı kayıt ve oturum açma deneyimleri sağlayın.
- CRM veritabanları, pazarlama analizi araçları ve hesap doğrulama sistemleriyle kolayca tümleştirin.
- Müşteriler için oturum açma, tercih ve dönüştürme verilerini yakalayın.

**Başvur**
- [Azure portalda](https://portal.azure.com/)
- [Azure AD B2C belgeleri](/azure/active-directory-b2c/)
- [Hızlı başlangıçlar](/azure/active-directory-b2c/active-directory-b2c-quickstarts-web-app)
- [Örnekler](/azure/active-directory-b2c/code-samples)

## <a name="azure-active-directory"></a>Azure Active Directory
[Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) , Microsoft 'un, şirketinizin oturum açmasını ve erişimini sağlayan bulut tabanlı kimlik ve erişim yönetimi hizmetidir.
- Microsoft Office 365, Azure portal ve binlerce diğer SaaS uygulaması gibi dış kaynaklar.
- Şirket ağınızdaki ve intranetinizdeki uygulamalar gibi iç kaynaklar, kendi kuruluşunuz tarafından geliştirilen tüm bulut uygulamalarıyla birlikte.

**Temel Özellikler**
- Kullanıcıları ihtiyaç duydukları uygulamalara bağlayarak **sorunsuz, yüksek güvenlikli erişim** .
- Kullanıcı, konum, cihaz, veri ve uygulama bağlamına göre kimlikler ve erişim için **kapsamlı kimlik koruması** ve gelişmiş güvenlik.
- Hem ticari hem de Office 365, Salesforce.com ve Box gibi özel uygulamalar **binlerce ön tümleşik uygulama** .
- **Erişimi ölçeğe göre yönetin**.

**Başvur**
- [Azure portalda](https://portal.azure.com/)
- [Azure AD nedir?](/azure/active-directory/fundamentals/active-directory-whatis)
- [Azure Active Directory ile çalışmaya başlama](/azure/active-directory/fundamentals/active-directory-whatis)
- [Hızlı başlangıçlar](/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)