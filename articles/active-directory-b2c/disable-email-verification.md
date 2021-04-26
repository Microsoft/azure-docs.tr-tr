---
title: Müşteri kaydı sırasında e-posta doğrulamayı devre dışı bırakma
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C ' de müşteri kaydı sırasında e-posta doğrulamayı devre dışı bırakmayı öğrenin.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/10/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 0f70c8d501a7d56f4bc29e0f2b065760cad625e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97585029"
---
# <a name="disable-email-verification-during-customer-sign-up-in-azure-active-directory-b2c"></a>Azure Active Directory B2C Müşteri kaydı sırasında e-posta doğrulamayı devre dışı bırakma

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Varsayılan olarak, Azure Active Directory B2C (Azure AD B2C), müşterinin e-posta adresini yerel hesaplar için doğrular (e-posta adresi veya Kullanıcı adı ile kaydolan kullanıcılar için hesaplar). Azure AD B2C, müşterilerin kaydolma işlemi sırasında bunları doğrulamasını zorunlu kılarak geçerli e-posta adreslerini sağlar. Ayrıca, kötü amaçlı aktörlerin uygulamalarınızda sahte hesaplar oluşturmak için otomatik süreçler kullanmalarını da engeller.

Bazı uygulama geliştiricileri, kaydolma işlemi sırasında e-posta doğrulamasını atlamayı tercih eder ve bunun yerine müşterilerin e-posta adreslerini daha sonra doğrular. Bunu desteklemek için Azure AD B2C, e-posta doğrulamasını devre dışı bırakacak şekilde yapılandırılabilir. Bunun yapılması daha sorunsuz bir kaydolma işlemi oluşturur ve geliştiricilere, e-posta adreslerini doğrulayan müşterileri, kendilerine ait olmayan müşterilerin ayırt edilmesine yönelik esneklik sağlar.

> [!WARNING]
> Kaydolma işleminde e-posta doğrulamasını devre dışı bırakmak istenmeyen postalara neden olabilir. Varsayılan Azure AD B2C tarafından belirtilen e-posta doğrulamasını devre dışı bırakırsanız, bir değiştirme doğrulama sistemi uygulamanızı öneririz.

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]
## <a name="disable-email-verification"></a>E-posta doğrulamayı devre dışı bırakma

::: zone pivot="b2c-user-flow"

E-posta doğrulamayı devre dışı bırakmak için şu adımları izleyin:

1. [Azure portalda](https://portal.azure.com) oturum açma
1. Azure AD B2C kiracınızı içeren dizini seçmek için üst menüdeki **Dizin + abonelik** filtresini kullanın.
1. Sol menüden **Azure AD B2C**' yi seçin. Ya da **tüm hizmetler** ' i seçin ve **Azure AD B2C** seçin.
1. **Kullanıcı akışları ' nı** seçin.
1. E-posta doğrulamasını devre dışı bırakmak istediğiniz kullanıcı akışını seçin. Örneğin, *B2C_1_signinsignup*.
1. **Sayfa düzenlerini** seçin.
1. **Yerel hesap kaydolma sayfası**' nı seçin.
1. **Kullanıcı öznitelikleri** altında **e-posta adresi**' ni seçin.
1. **Doğrulama gerektirir** açılır penceresinde **Hayır**' ı seçin.
1. **Kaydet**’i seçin. E-posta doğrulaması artık bu kullanıcı akışı için devre dışı bırakıldı.

::: zone-end

::: zone pivot="b2c-custom-policy"
**Localaccountsignupwithlogonemail** teknik profili, kaydolma akışı sırasında çağrılan, [kendiliğinden onaylanan](self-asserted-technical-profile.md)bir değerdir. E-posta doğrulamasını devre dışı bırakmak için `EnforceEmailVerification` meta verileri false olarak ayarlayın. Uzantı dosyasındaki LocalAccountSignUpWithLogonEmail teknik profillerini geçersiz kılın. 

1. İlkenizin uzantıları dosyasını açın. Örneğin, <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .
1. Öğesini bulun `ClaimsProviders` . Öğe yoksa, ekleyin.
1. Aşağıdaki talep sağlayıcısını `ClaimsProviders` öğesine ekleyin:

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <Metadata>
        <Item Key="EnforceEmailVerification">false</Item>
      </Metadata>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```
::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="test-your-policy"></a>İlkenizi test etme 

1. [Azure portalda](https://portal.azure.com) oturum açma
1. Azure AD B2C kiracınızı içeren dizini seçmek için üst menüdeki **Dizin + abonelik** filtresini kullanın.
1. Sol menüden **Azure AD B2C**' yi seçin. Ya da **tüm hizmetler** ' i seçin ve **Azure AD B2C** seçin.
1. **Kullanıcı akışları ' nı** seçin.
1. E-posta doğrulamasını devre dışı bırakmak istediğiniz kullanıcı akışını seçin. Örneğin, *B2C_1_signinsignup*.
1. İlkenizi test etmek için **Kullanıcı akışını Çalıştır**' ı seçin.
1. **Uygulama** için, daha önce kaydettiğiniz *testapp1* adlı Web uygulamasını seçin. **Yanıt URL 'si** gösterilmesi gerekir `https://jwt.ms` .
1. **Kullanıcı akışını Çalıştır** 'a tıklayın
1. Doğrulama olmadan bir e-posta adresi kullanarak kaydolabilirsiniz.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="update-and-test-the-relying-party-file"></a>Bağlı olan taraf dosyasını güncelleştirme ve test etme

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Üst menüdeki **Dizin + abonelik** filtresini SEÇIP Azure AD kiracınızı içeren dizini seçerek Azure AD kiracınızı içeren dizini kullandığınızdan emin olun.
1. Azure portal sol üst köşesindeki **tüm hizmetler** ' i seçin ve ardından **uygulama kayıtları**' i arayıp seçin.
1. **Kimlik deneyimi çerçevesini** seçin.
1. **Özel Ilkeyi karşıya yükle**' yi seçin ve ardından değiştirdiğiniz iki ilke dosyasını karşıya yükleyin.
1. Karşıya yüklediğiniz kaydolma veya oturum açma ilkesini seçin ve **Şimdi Çalıştır** düğmesine tıklayın.
1. Doğrulama olmadan bir e-posta adresi kullanarak kaydolabilirsiniz.

::: zone-end


## <a name="next-steps"></a>Sonraki adımlar

- [Azure Active Directory B2C içinde Kullanıcı arabirimini özelleştirmeyi](customize-ui-with-html.md) öğrenin

