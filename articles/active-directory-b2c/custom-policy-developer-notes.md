---
title: Özel ilkeler için geliştirici notları
titleSuffix: Azure AD B2C
description: Özel ilkelerle Azure AD B2C yapılandırma ve sürdürme hakkında geliştiricilere yönelik notlar.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 05/19/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c175a6d225be268f27854b9ab63886892cf029fb
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105557284"
---
# <a name="developer-notes-for-custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C özel ilkeler için geliştirici notları

Azure Active Directory B2C özel ilke yapılandırması genel kullanıma sunulmuştur. Bu yapılandırma yöntemi, karmaşık kimlik çözümleri oluşturan gelişmiş kimlik geliştiricilerine yöneliktir. Özel ilkeler, kimlik deneyimi çerçevesinin gücünü Azure AD B2C kiracılarda kullanılabilir hale getirir.
Özel ilkeleri kullanan gelişmiş kimlik geliştiricileri, yürüme-kılavuzlarına ve başvuru belgelerini okumayı tamamlamak için bir süre yatırmaya planlanmalıdır.

Kullanılabilir özel ilke seçeneklerinin büyük bir kısmında genel kullanıma sunuldu olsa da, teknik profil türleri ve yazılım yaşam döngüsünün farklı aşamalarında bulunan içerik tanımı API 'Leri gibi temel yetenekler vardır. Çok daha fazlası geliyor. Aşağıdaki tabloda daha ayrıntılı bir düzeyde kullanılabilirlik düzeyi belirtilir.

## <a name="features-that-are-generally-available"></a>Genel kullanıma açık olan özellikler

- Özel ilkeler kullanarak özel kimlik doğrulama Kullanıcı yolculukları yazın ve karşıya yükleyin.
    - Talep sağlayıcılar arasında değişim olarak Kullanıcı yolculukları adımını açıkla.
    - Kullanıcı yolculukları 'nda koşullu dallanma tanımlayın.
- Özel kimlik doğrulama Kullanıcı yolculukları 'nda REST API özellikli hizmetlerle birlikte çalışır.
- Openıdconnect protokolüyle uyumlu kimlik sağlayıcılarıyla federasyona bağlayın.
- SAML 2,0 protokolüne bağlı kimlik sağlayıcılarıyla federasyona ayırın.

## <a name="responsibilities-of-custom-policy-feature-set-developers"></a>Özel ilke özelliğinin sorumlulukları-geliştiricilerin set

El ile ilke yapılandırması, Azure AD B2C temel alınan platforma alt düzey erişim sağlar ve benzersiz bir güven çerçevesinin oluşturulmasına neden olur. Özel kimlik sağlayıcılarının, güven ilişkilerinin, dış hizmetlerle tümleştirmelerin ve adım adım iş akışlarının birçok olası permütasyon, tasarım ve yapılandırma için methodical bir yaklaşım gerektirir.

Özel ilke özelliği kümesini kullanan geliştiriciler aşağıdaki yönergelere uymalıdır:

- Özel ilkelerin ve anahtar/gizli dizi yönetiminin yapılandırma dili hakkında bilgi sahibi olun. Daha fazla bilgi için bkz. [TrustFrameworkPolicy](trustframeworkpolicy.md).
- Senaryoların ve özel tümleştirmelerin sahipliğini alın. Çalışmanızı belgeleyin ve canlı site kuruluşunuza bildirin.
- Methodical senaryo testi gerçekleştirin.
- Yazılım geliştirme ve en iyi hazırlama uygulamalarını izleyin. En az bir geliştirme ve test ortamı önerilir.
- İle tümleştirdiğinizde, kimlik sağlayıcılarının ve hizmetlerin yeni geliştirmeleri hakkında bilgi sahibi olun. Örneğin, gizli değişiklikler ve hizmette zamanlanan ve zamanlanmamış değişiklikler üzerinde değişiklik takip edin.
- Etkin izlemeyi ayarlayın ve üretim ortamlarının yanıt hızını izleyin. Application Insights ile tümleştirme hakkında daha fazla bilgi için bkz. [Azure Active Directory B2C: günlükleri toplama](analytics-with-application-insights.md).
- İletişim e-posta adreslerini Azure aboneliğinde güncel tutun ve Microsoft canlı site ekibi e-postalarına yanıt vermeye devam edin.
- Microsoft Live-site ekibi tarafından bunu yapmanız önerilir.

## <a name="terms-for-features-in-public-preview"></a>Genel önizlemede özellikler için terimler

- Genel Önizleme özelliklerini yalnızca değerlendirme amacıyla kullanmanızı öneririz.
- Hizmet düzeyi sözleşmeleri (SLA 'Lar) Genel Önizleme özellikleri için geçerlidir.
- Genel Önizleme özelliklerine yönelik destek istekleri, normal destek kanalları aracılığıyla dosyalanır.

## <a name="features-by-stage-and-known-issues"></a>Aşamalara ve bilinen sorunlara göre Özellikler

Özel ilke özellikleri, sabit geliştirme aşamasındadır. Aşağıdaki tablo, özelliklerin ve bileşen kullanılabilirliğinin bir dizinidir.


### <a name="protocols-and-authorization-flows"></a>Protokoller ve yetkilendirme akışları

| Özellik | Geliştirme | Önizleme | GA | Notlar |
|-------- | :-----------: | :-------: | :--: | ----- |
| [OAuth2 yetkilendirme kodu](authorization-code-flow.md) |  |  | X |  |
| OAuth2 yetkilendirme kodu, PKI CE |  |  | X | [Ortak istemciler ve tek sayfalı uygulamalar](authorization-code-flow.md)  |
| [OAuth2 örtük akış](implicit-flow-single-page-application.md) |  |  | X |  |
| [OAuth2 kaynak sahibi parolası kimlik bilgileri](add-ropc-policy.md) |  | X |  |  |
| [OıDC Connect](openid-connect.md) |  |  | X |  |
| [SAML2](saml-service-provider.md)  |  |  |X  | Bağlamaları GÖNDERIN ve yeniden yönlendirin. |
| OAuth1 |  |  |  | Desteklenmez. |
| Wsbes | X |  |  |  |

### <a name="identify-providers-federation"></a>Sağlayıcıları federasyonunu tanımla 

| Özellik | Geliştirme | Önizleme | GA | Notlar |
|-------- | :-----------: | :-------: | :--: | ----- |
| [OpenID Connect](openid-connect-technical-profile.md) |  |  | X | Örneğin, Google +.  |
| [OAuth2](oauth2-technical-profile.md) |  |  | X | Örneğin, Facebook.  |
| [OAuth1](oauth1-technical-profile.md) |  | X |  | Örneğin, Twitter. |
| [SAML2](identity-provider-generic-saml.md) |  |   | X | Örneğin, Salesforce, ADFS. |
| Wsbes| X |  |  |  |


### <a name="rest-api-integration"></a>REST API tümleştirme

| Özellik | Geliştirme | Önizleme | GA | Notlar |
|-------- | :-----------: | :-------: | :--: | ----- |
| [Temel kimlik doğrulaması ile REST API](secure-rest-api.md#http-basic-authentication) |  |  | X |  |
| [İstemci sertifikası kimlik doğrulaması ile REST API](secure-rest-api.md#https-client-certificate-authentication) |  |  | X |  |
| [OAuth2 taşıyıcı kimlik doğrulaması ile REST API](secure-rest-api.md#oauth2-bearer-authentication) |  | X |  |  |

### <a name="component-support"></a>Bileşen desteği

| Özellik | Geliştirme | Önizleme | GA | Notlar |
| ------- | :-----------: | :-------: | :--: | ----- |
| [Telefon faktörü kimlik doğrulaması](phone-factor-technical-profile.md) |  |  | X |  |
| [Azure AD MFA kimlik doğrulaması](multi-factor-auth-technical-profile.md) |  | X |  |  |
| [Tek kullanımlık parola](one-time-password-technical-profile.md) |  | X |  |  |
| Yerel Dizin olarak [Azure Active Directory](active-directory-technical-profile.md) |  |  | X |  |
| Eposta doğrulaması için Azure e-posta alt sistemi |  |  | X |  |
| [Üçüncü taraf e-posta hizmeti sağlayıcıları](custom-email-mailjet.md) |  |X  |  |  |
| [Çoklu dil desteği](localization.md)|  |  | X |  |
| [Koşul doğrulamaları](predicates.md) |  |  | X | Örneğin, parola karmaşıklığı. |
| [Görüntüleme denetimleri](display-controls.md) |  |X  |  |  |


### <a name="app-ief-integration"></a>App-ıEF tümleştirmesi

| Özellik | Geliştirme | Önizleme | GA | Notlar |
| ------- | :-----------: | :-------: | :--: | ----- |
| Sorgu dizesi parametresi `domain_hint` |  |  | X | Talep olarak kullanılabilir, ıDP 'ye geçirilebilir. |
| Sorgu dizesi parametresi `login_hint` |  |  | X | Talep olarak kullanılabilir, ıDP 'ye geçirilebilir. |
| JSON ile Kullanıcı yolculuğuna ekleme `client_assertion` | X |  |  | Kullanım dışı olacaktır. |
| JSON 'ı Kullanıcı yolculuğuna ekleme `id_token_hint` |  | X |  | JSON geçişine git-ilet yaklaşımı. |
| [Kimlik sağlayıcısı belirtecini uygulamaya geçir](idp-pass-through-user-flow.md) |  | X |  | Örneğin, Facebook 'tan uygulamaya. |


### <a name="session-management"></a>Oturum yönetimi

| Özellik | Geliştirme | Önizleme | GA | Notlar |
| ------- | :-----------: | :-------: | :--: | ----- |
| [Varsayılan SSO oturum sağlayıcısı](custom-policy-reference-sso.md#defaultssosessionprovider) |  |  | X |  |
| [Dış oturum açma oturumu sağlayıcısı](custom-policy-reference-sso.md#externalloginssosessionprovider) |  |  | X |  |
| [SAML SSO oturum sağlayıcısı](custom-policy-reference-sso.md#samlssosessionprovider) |  |  | X |  |
| [OAuthSSOSessionProvider](custom-policy-reference-sso.md#oauthssosessionprovider)  |  | X |  |  |
| [Çoklu oturum kapatma](session-behavior.md#sign-out)  |  | X |  |  |

### <a name="security"></a>Güvenlik

| Özellik | Geliştirme | Önizleme | GA | Notlar |
|-------- | :-----------: | :-------: | :--: | ----- |
| İlke anahtarları-oluştur, El Ile, karşıya yükle |  |  | X |  |
| İlke anahtarları-RSA/CERT, gizlilikler |  |  | X |  |


### <a name="developer-interface"></a>Geliştirici arabirimi

| Özellik | Geliştirme | Önizleme | GA | Notlar |
| ------- | :-----------: | :-------: | :--: | ----- |
| Azure portalı-ıEF UX |  |  | X |  |
| İlke karşıya yükleme |  |  | X |  |
| [Application Insights Kullanıcı yolculuğu günlükleri](troubleshoot-with-application-insights.md) |  | X |  | Geliştirme sırasında sorun giderme için kullanılır.  |
| [Olay günlüklerini Application Insights](analytics-with-application-insights.md) |  | X |  | Üretimde Kullanıcı akışlarını izlemek için kullanılır. |


## <a name="next-steps"></a>Sonraki adımlar

- [Microsoft Graph Azure AD B2C için kullanılabilir işlemleri](microsoft-graph-operations.md) denetleyin
- [Özel ilkeler ve Kullanıcı akışlarının farkları](custom-policy-overview.md)hakkında daha fazla bilgi edinin.
