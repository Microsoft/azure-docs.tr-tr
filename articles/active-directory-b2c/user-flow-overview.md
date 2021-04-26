---
title: Azure Active Directory B2C | Kullanıcı akışları ve özel ilkeler | Microsoft Docs
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C yerleşik Kullanıcı akışları ve özel ilke Genişletilebilir ilke çerçevesi hakkında daha fazla bilgi edinin.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/08/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2e4dbc5178bec3a5b1f0931267465879f604f36f
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107226017"
---
# <a name="user-flows-and-custom-policies-overview"></a>Kullanıcı akışları ve özel ilkelere genel bakış

Azure AD B2C, kullanıcıların uygulamanıza erişim kazanmak için izlediği iş mantığını tanımlayabilirsiniz. Örneğin, kullanıcıların oturum açtıklarında takip ettikleri, bir profil düzenlediğiniz veya bir parolayı sıfırlayabilecekleri adımların sırasını belirleyebilirsiniz. Sırası tamamladıktan sonra Kullanıcı bir belirteç alır ve uygulamanıza erişim kazanır. 

Azure AD B2C, kimlik kullanıcı deneyimlerini sağlamanın iki yolu vardır:

* **Kullanıcı akışları** , sağladığımız önceden tanımlanmış, yerleşik ve yapılandırılabilir ilkeleridir. bu sayede, dakikalar içinde kaydolma, oturum açma ve ilke düzenlemeyle ilgili deneyimler oluşturabilirsiniz.

* **Özel ilkeler** , karmaşık kimlik deneyimi senaryoları için kendi Kullanıcı yolculukları oluşturmanızı sağlar.

Aşağıdaki ekran görüntüsünde, Kullanıcı akış ayarları kullanıcı ARABIRIMI ve özel ilke yapılandırma dosyaları gösterilmektedir.

![Ekran görüntüsü, Kullanıcı akış ayarları Kullanıcı ARABIRIMINI ve özel ilke yapılandırma dosyalarını gösterir.](media/user-flow-overview/user-flow-vs-custom-policy.png)

Bu makalede Kullanıcı akışları ve özel ilkeler hakkında kısa bir genel bakış sağlanır ve iş gereksinimleriniz için en iyi şekilde hangi yöntemin çalışacağına karar vermenize yardımcı olur.

## <a name="user-flows"></a>Kullanıcı akışları

En yaygın kimlik görevlerini ayarlamak için Azure portal, *Kullanıcı akışları* adlı birkaç önceden tanımlı ve yapılandırılabilir ilke içerir.

Uygulamalarınızda kimlik deneyimi davranışlarını denetlemek için aşağıdaki gibi Kullanıcı akış ayarlarını yapılandırabilirsiniz:

* Facebook gibi sosyal hesaplar veya oturum açma için bir e-posta adresi ve parola kullanan yerel hesaplar gibi oturum açma için kullanılan hesap türleri
* Tüketiciden toplanacak öznitelikler (örneğin, ilk ad, posta kodu veya bir yerden ülke/bölge)
* Azure AD Multi-Factor Authentication (MFA)
* Kullanıcı arabiriminin özelleştirilmesi
* Kullanıcı Kullanıcı akışını tamamladıktan sonra uygulamanızın aldığı bir belirteçte talepler kümesi
* Oturum yönetimi
* ... ve daha fazlası

Uygulamalar için ortak kimlik senaryolarının çoğu kullanıcı akışlarıyla birlikte tanımlanabilir ve etkili bir şekilde uygulanabilir. Özel ilkelerin tam esnekliğini gerektiren karmaşık Kullanıcı yolculuğu senaryolarınız yoksa yerleşik Kullanıcı akışlarını kullanmanızı öneririz.

## <a name="custom-policies"></a>Özel ilkeler

Özel ilkeler, Azure AD B2C Kiracı Kullanıcı deneyiminizin davranışını tanımlayan yapılandırma dosyalarıdır. Kullanıcı akışları, en yaygın kimlik görevlerinin Azure AD B2C portalında önceden tanımlanmış olsa da, özel ilkeler birçok farklı görevi tamamlayabilmesi için bir kimlik geliştiricisi tarafından tamamen düzenlenebilir.

Özel bir ilke tamamen yapılandırılabilir ve ilke odaklı olur. Standart protokollerde varlıklar arasındaki güveni düzenler. Örneğin, OpenID Connect, OAuth, SAML ve standart olmayan birkaç farklı şekilde, örneğin REST API tabanlı sistemden sisteme talep alışverişi. Framework, Kullanıcı dostu, beyaz etiketli deneyimler oluşturur.

Özel ilke, herhangi bir adım birleşimiyle Kullanıcı yolculukları oluşturma olanağı sağlar. Örnek:

* Diğer kimlik sağlayıcılarıyla federasyona ekleme
* Birinci ve üçüncü taraf Multi-Factor Authentication (MFA) sorunları
* Herhangi bir kullanıcı girişini toplayın
* REST API iletişimini kullanarak dış sistemlerle tümleştirme

Her Kullanıcı yolculuğu bir ilke tarafından tanımlanır. Kuruluşunuz için en iyi kullanıcı deneyimini etkinleştirmek üzere ihtiyacınız olan sayıda veya daha fazla ilke oluşturabilirsiniz.

![IEF tarafından etkinleştirilen karmaşık Kullanıcı yolculuğunun bir örneğini gösteren diyagram](media/user-flow-overview/custom-policy-diagram.png)

Özel bir ilke, hiyerarşik bir zincirde birbirlerine başvuran birkaç XML dosyası tarafından tanımlanır. XML öğeleri, talep şemasını, talep dönüştürmelerini, içerik tanımlarını, talep sağlayıcılarını, teknik profilleri, Kullanıcı yolculuğu düzenleme adımlarını ve kimlik deneyiminin diğer yönlerini tanımlar.

Özel ilkelerin güçlü esnekliği en iyi şekilde karmaşık kimlik senaryoları oluşturmanız gerektiğinde uygundur. Özel ilkeleri yapılandıran geliştiriciler, her bir kimlik sağlayıcısı için gerektiğinde meta veri uç noktalarını, tam talep istekleri Exchange tanımlarını ve parolaları, anahtarları ve sertifikaları yapılandırmak için güvenilir ilişkileri dikkatle tanımlamalıdır.

[Azure Active Directory B2C özel ilkelerindeki](custom-policy-overview.md)özel ilkeler hakkında daha fazla bilgi edinin.

## <a name="comparing-user-flows-and-custom-policies"></a>Kullanıcı akışlarını ve özel ilkeleri karşılaştırma

Aşağıdaki tabloda, Azure AD B2C Kullanıcı akışları ve özel ilkeyle ilgili olarak kullanabileceğiniz senaryolara ilişkin ayrıntılı bir karşılaştırma sunulmaktadır.

| Bağlam | Kullanıcı akışları | Özel ilkeler |
|-|-------------------|-----------------|
| Hedef kullanıcılar | Kimlik uzmanlığına sahip veya olmayan tüm uygulama geliştiricileri. | Kimlik uzmanları, sistem tümleştiricileri, danışmanları ve şirket içi kimlik takımları. OpenID Connect akışlarıyla rahat ve kimlik sağlayıcılarını ve talep tabanlı kimlik doğrulamasını anlayın. |
| Yapılandırma yöntemi | Kullanıcı dostu Kullanıcı arabirimi (UI) ile Azure portal. | XML dosyalarını doğrudan düzenleyip Azure portal karşıya yükleyin. |
| UI özelleştirmesi | HTML, CSS ve [JavaScript](javascript-and-page-layout.md)gıbı [tam UI özelleştirmesi](customize-ui-with-html.md) .<br><br>Özel dizelerle [çok dilli destek](language-customization.md) . | Aynı |
| Öznitelik özelleştirmesi | Standart ve özel öznitelikler. | Aynı |
| Belirteç ve oturum yönetimi | [Belirteçleri](configure-tokens.md) ve [oturum davranışlarını](session-behavior.md)özelleştirin. | Aynı |
| Kimlik Sağlayıcıları | Azure Active Directory kiracılar ile Federasyon gibi [önceden tanımlanmış yerel](identity-provider-local.md) veya [sosyal sağlayıcı](add-identity-provider.md). | Standartlara dayalı OıDC, OAUTH ve SAML.  REST API 'lerle tümleştirme kullanılarak da kimlik doğrulaması mümkündür. |
| Kimlik görevleri | Yerel veya çok sayıda sosyal hesapla kaydolma [veya oturum açma](add-sign-up-and-sign-in-policy.md) .<br><br>[Self servis parola sıfırlama](add-password-reset-policy.md).<br><br>[Profil düzenleme](add-profile-editing-policy.md).<br><br>Multi-Factor Authentication.<br><br>Belirteç akışlarına erişin. | Özel kimlik sağlayıcılarını kullanarak Kullanıcı akışlarıyla aynı görevleri tamamlayıp özel kapsamlar kullanın.<br><br>Kayıt sırasında başka bir sistemde bir kullanıcı hesabı sağlayın.<br><br>Kendi e-posta hizmet sağlayıcınızı kullanarak bir hoş geldiniz e-postası gönderin.<br><br>Azure AD B2C dışında bir kullanıcı deposu kullanın.<br><br>Bir API kullanarak güvenilir bir sistemle Kullanıcı tarafından sağlanmış bilgileri doğrulayın. |

## <a name="application-integration"></a>Uygulama tümleştirme

Kiracınızda farklı türlerde birçok kullanıcı akışı veya özel ilke oluşturabilir ve bunları gerektiği şekilde uygulamalarınızda kullanabilirsiniz. Her iki Kullanıcı akışı ve özel ilke, uygulamalar arasında yeniden kullanılabilir. Bu esneklik, kodunuzda en az değişiklik yapmadan kimlik deneyimlerini tanımlamanızı ve değiştirmenizi sağlar. 

Bir Kullanıcı uygulamanızda oturum açmak istediğinde, uygulama bir Kullanıcı akışına veya özel ilkeye göre belirtilen uç noktaya yetkilendirme isteği başlatır. Kullanıcı akışı veya özel ilke, kullanıcının deneyimini tanımlar ve denetler. Bir kullanıcı akışını tamamlarsa, Azure AD B2C bir belirteç oluşturur ve kullanıcıyı uygulamanıza geri yönlendirir.

![Azure AD B2C oturum açma sayfası arasında akışı gösteren oklar içeren mobil uygulama](media/user-flow-overview/app-integration.png)

Birden çok uygulama aynı kullanıcı akışını veya özel ilkeyi kullanabilir. Tek bir uygulama, birden çok Kullanıcı akışı veya özel ilke kullanabilir.

Örneğin, bir uygulamada oturum açmak için uygulama *kaydolma veya oturum açma* Kullanıcı akışını kullanır. Kullanıcı oturum açtıktan sonra, profillerini düzenlemek isteyebilir. Profili düzenlemek için, uygulama başka bir yetkilendirme isteği başlatır, bu kez *Profil düzenleme* Kullanıcı akışı ' nı kullanmaktır.

Uygulamanız, kullanıcı akışını veya özel ilke adını içeren standart bir HTTP kimlik doğrulama isteği kullanarak bir Kullanıcı akışı tetikler. Özelleştirilmiş bir [belirteç](tokens-overview.md) yanıt olarak alınır.


## <a name="next-steps"></a>Sonraki adımlar

- Önerilen Kullanıcı akışlarını oluşturmak için [öğretici: Kullanıcı akışı oluşturma](tutorial-create-user-flows.md)' daki yönergeleri izleyin.
- [Azure AD B2C içindeki Kullanıcı akışı sürümleri](user-flow-versions.md)hakkında bilgi edinin.
- [Azure AD B2C özel ilkeler](custom-policy-overview.md)hakkında daha fazla bilgi edinin.
