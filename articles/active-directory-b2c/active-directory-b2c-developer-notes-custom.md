---
title: Özel ilkeler için geliştirici notları-Azure Active Directory B2C | Microsoft Docs
description: Özel ilkelerle Azure AD B2C yapılandırma ve sürdürme hakkında geliştiricilere yönelik notlar.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 3f8d1ac217647ee292338da875671ef8bd3f79db
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227206"
---
# <a name="developer-notes-for-custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C özel ilkeler için geliştirici notları

Azure Active Directory B2C özel ilke yapılandırması genel kullanıma sunulmuştur. Bu yapılandırma yöntemi, karmaşık kimlik çözümleri oluşturan gelişmiş kimlik geliştiricilerine yöneliktir. Özel ilkeler, kimlik deneyimi çerçevesinin gücünü Azure AD B2C kiracılarda kullanılabilir hale getirir.
Özel ilkeleri kullanan gelişmiş kimlik geliştiricileri, yürüme-kılavuzlarına ve başvuru belgelerini okumayı tamamlamak için bir süre yatırmaya planlanmalıdır.

Kullanılabilir özel ilke seçeneklerinin büyük bir kısmında genel kullanıma sunuldu olsa da, teknik profil türleri ve yazılım yaşam döngüsünün farklı aşamalarında bulunan içerik tanımı API 'Leri gibi temel yetenekler vardır. Çok daha fazlası geliyor. Aşağıdaki tabloda daha ayrıntılı bir düzeyde kullanılabilirlik düzeyi belirtilir.

## <a name="features-that-are-generally-available"></a>Genel kullanıma açık olan özellikler

- Özel ilkeler kullanarak özel kimlik doğrulama Kullanıcı yolculukları yazın ve karşıya yükleyin.
    - Talep sağlayıcıları arasındaki değişimleri adım adım kabul eden kullanıcı hakkındaki adımları açıklama.
    - Kullanıcı yolculukları 'nda koşullu dallanma tanımlayın.
- Özel kimlik doğrulama Kullanıcı yolculukları 'nda REST API özellikli hizmetlerle birlikte çalışır.
- Openıdconnect protokolüyle uyumlu kimlik sağlayıcılarıyla federasyona bağlayın.
- SAML 2,0 protokolüne bağlı kimlik sağlayıcılarıyla federasyona ayırın.

## <a name="responsibilities-of-custom-policy-feature-set-developers"></a>Özel ilke özelliğinin sorumlulukları-geliştiricilerin set

El ile ilke yapılandırması, Azure AD B2C temel alınan platforma alt düzey erişim sağlar ve benzersiz bir güven çerçevesinin oluşturulmasına neden olur. Özel kimlik sağlayıcılarının, güven ilişkilerinin, dış hizmetlerle tümleştirmelerin yanı sıra adım adım iş akışlarının tasarımı ve yapılandırması için methodical yaklaşımı olması gerekir.

Özel ilke özelliği kümesini kullanan geliştiriciler aşağıdaki yönergelere uymalıdır:

- Özel ilkelerin ve anahtar/gizli dizi yönetiminin yapılandırma dili hakkında bilgi sahibi olun. Daha fazla bilgi için bkz. [TrustFrameworkPolicy](trustframeworkpolicy.md).
- Senaryoların ve özel tümleştirmelerin sahipliğini alın. Çalışmanızı belgeleyin ve canlı site kuruluşunuza bildirin.
- Methodical senaryo testi gerçekleştirin.
- En az bir geliştirme ve test ortamı ve bir üretim ortamı ile yazılım geliştirme ve hazırlama en iyi uygulamalarını izleyin.
- İle tümleştirdiğinizde, kimlik sağlayıcılarının ve hizmetlerin yeni geliştirmeleri hakkında bilgi sahibi olun. Örneğin, gizli değişiklikler ve hizmette zamanlanan ve zamanlanmamış değişiklikler üzerinde değişiklik takip edin.
- Etkin izlemeyi ayarlayın ve üretim ortamlarının yanıt hızını izleyin. Application Insights tümleştirme hakkında daha fazla bilgi için bkz [. Azure Active Directory B2C: Günlükler](active-directory-b2c-custom-guide-eventlogger-appins.md)toplanıyor.
- İletişim e-posta adreslerini Azure aboneliğinde güncel tutun ve Microsoft canlı site ekibi e-postalarına yanıt vermeye devam edin.
- Microsoft Live-site ekibi tarafından bunu yapmanız önerilir.

## <a name="terms-for-features-in-public-preview"></a>Genel önizlemede özellikler için terimler

- Genel Önizleme özelliklerini yalnızca değerlendirme amacıyla kullanmanızı öneririz.
- Hizmet düzeyi sözleşmeleri (SLA 'Lar) Genel Önizleme özellikleri için geçerlidir.
- Genel Önizleme özelliklerine yönelik destek istekleri, normal destek kanalları aracılığıyla dosyalanır.

## <a name="features-by-stage-and-known-issues"></a>Aşamalara ve bilinen sorunlara göre Özellikler

Özel ilke/kimlik deneyimi çerçevesi özellikleri sabit ve hızlı geliştirme aşamasındadır. Aşağıdaki tablo, özelliklerin ve bileşen kullanılabilirliğinin bir dizinidir.

### <a name="identity-providers-tokens-protocols"></a>Kimlik sağlayıcıları, belirteçler, protokoller

| Özellik | Geliştirme | Önizleme | GA | Notlar |
|-------- | :-----------: | :-------: | :--: | ----- |
| IDP-Openıdconnect |  |  | X | Örneğin, Google +.  |
| IDP-OAUTH2 |  |  | X | Örneğin, Facebook.  |
| IDP-OAUTH1 (Twitter) |  | X |  | Örneğin, Twitter. |
| IDP-OAUTH1 (ex-Twitter) |  |  |  | Desteklenmiyor |
| IDP-SAML |  |   | X | Örneğin, Salesforce, ADFS. |
| IDP-WSBES | X |  |  |  |
| Bağlı olan taraf OAUTH1 |  |  |  | Desteklenmiyor. |
| Bağlı olan taraf OAUTH2 |  |  | X |  |
| Bağlı olan taraf OıDC |  |  | X |  |
| Bağlı olan taraf SAML | X |  |  |  |
| Bağlı olan taraf wsbes | X |  |  |  |
| Temel ve sertifika kimlik doğrulaması ile REST API |  |  | X | Örneğin, Azure Logic Apps. |

### <a name="component-support"></a>Bileşen desteği

| Özellik | Geliştirme | Önizleme | GA | Notlar |
| ------- | :-----------: | :-------: | :--: | ----- |
| Azure Multi Factor Authentication |  |  | X |  |
| Yerel Dizin olarak Azure Active Directory |  |  | X |  |
| Eposta doğrulaması için Azure e-posta alt sistemi |  |  | X |  |
| Çoklu dil desteği|  |  | X |  |
| Koşul doğrulamaları |  |  | X | Örneğin, parola karmaşıklığı. |
| Üçüncü taraf e-posta hizmeti sağlayıcılarını kullanma | X |  |  |  |

### <a name="content-definition"></a>İçerik tanımı

| Özellik | Geliştirme | Önizleme | GA | Notlar |
| ------- | :-----------: | :-------: | :--: | ----- |
| Hata sayfası, api. Error |  |  | X |  |
| IDP seçim sayfası, api. ıdpseçimlerin |  |  | X |  |
| Kaydolma, api. ıdpseçimlerin. Signup için ıDP seçimi |  |  | X |  |
| Parolayı unuttum, api. localaccountpasswordreset |  |  | X |  |
| Yerel hesap oturum açma, api. localaccountsignın |  |  | X |  |
| Yerel hesap kaydolma, api. localaccountsignup |  |  | X |  |
| MFA sayfası, API. phonefactor |  |  | X |  |
| Kendi kendini onaylanan sosyal hesap kaydolma, API. selfasted |  |  | X |  |
| Kendi kendini onaylanan profil güncelleştirmesi, api. selfasserted. profileUpdate |  |  | X |  |
| Birleşik kaydolma veya oturum açma sayfası, api. signuporsignın "disableSignup" parametresiyle |  |  | X |  |
| JavaScript/sayfa düzeni |  | X |  |  |

### <a name="app-ief-integration"></a>App-ıEF tümleştirmesi

| Özellik | Geliştirme | Önizleme | GA | Notlar |
| ------- | :-----------: | :-------: | :--: | ----- |
| Sorgu dizesi parametre domain_hint |  |  | X | Talep olarak kullanılabilir, ıDP 'ye geçirilebilir. |
| Sorgu dizesi parametre login_hint |  |  | X | Talep olarak kullanılabilir, ıDP 'ye geçirilebilir. |
| Client_assertion aracılığıyla Kullanıcı yolculuğuna JSON ekleyin | X |  |  | Kullanım dışı olacaktır. |
| JSON 'ı id_token_hint olarak Useryolculuğa Ekle |  | X |  | JSON geçişine git-ilet yaklaşımı. |
| IDP BELIRTECINI uygulamaya geçir |  | X |  | Örneğin, Facebook 'tan uygulamaya. |

### <a name="session-management"></a>Oturum yönetimi

| Özellik | Geliştirme | Önizleme | GA | Notlar |
| ------- | :-----------: | :-------: | :--: | ----- |
| SSO oturum sağlayıcısı |  |  | X |  |
| Dış oturum açma oturumu sağlayıcısı |  |  | X |  |
| SAML SSO oturum sağlayıcısı |  |  | X |  |
| Varsayılan SSO oturum sağlayıcısı |  |  | X |  |

### <a name="security"></a>Güvenlik

| Özellik | Geliştirme | Önizleme | GA | Notlar |
|-------- | :-----------: | :-------: | :--: | ----- |
| İlke anahtarları-oluştur, El Ile, karşıya yükle |  |  | X |  |
| İlke anahtarları-RSA/CERT, gizlilikler |  |  | X |  |
| İlke karşıya yükleme |  |  | X |  |

### <a name="developer-interface"></a>Geliştirici arabirimi

| Özellik | Geliştirme | Önizleme | GA | Notlar |
| ------- | :-----------: | :-------: | :--: | ----- |
| Azure portalı-ıEF UX |  |  | X |  |
| Application Insights Kullanıcı yolculuğu günlükleri |  | X |  | Geliştirme sırasında sorun giderme için kullanılır.  |
| Olay günlüklerini Application Insights (düzenleme adımları aracılığıyla) |  | X |  | Üretimde Kullanıcı akışlarını izlemek için kullanılır. |

## <a name="next-steps"></a>Sonraki adımlar

[Özel ilkeler ve Kullanıcı akışlarının farkları](active-directory-b2c-overview-custom.md)hakkında daha fazla bilgi edinin.
