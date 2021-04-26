---
title: Uygulamanıza bir kimlik sağlayıcısı erişim belirteci geçirin
titleSuffix: Azure AD B2C
description: OAuth 2,0 kimlik sağlayıcıları için bir erişim belirtecini Azure Active Directory B2C bir Kullanıcı akışında talep olarak nasıl geçitireceğinizi öğrenin.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/15/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: a99d41f5f9fc9538aaf563bd3ae56075d269c94a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97584655"
---
# <a name="pass-an-identity-provider-access-token-to-your-application-in-azure-active-directory-b2c"></a>Azure Active Directory B2C bir kimlik sağlayıcısı erişim belirtecini uygulamanıza geçirin

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Azure Active Directory B2C (Azure AD B2C) bir [Kullanıcı akışı](user-flow-overview.md) , uygulamanızın kullanıcılarına bir kimlik sağlayıcısına kaydolma veya oturum açma olanağı sağlar. Yolculuğun başladığı zaman, Azure AD B2C kimlik sağlayıcısından bir [erişim belirteci](tokens-overview.md) alır. Azure AD B2C, kullanıcı hakkındaki bilgileri almak için bu belirteci kullanır. Kullanıcı akışındaki bir talebi, belirteci, Azure AD B2C kaydettiğinizde bulunan uygulamalara geçirmek için etkinleştirirsiniz.

::: zone pivot="b2c-user-flow"

Azure AD B2C, [Facebook](identity-provider-facebook.md) ve [Google](identity-provider-google.md)dahil olmak üzere [OAuth 2,0](add-identity-provider.md) kimlik sağlayıcılarının erişim belirtecinin geçirilmesini destekler. Diğer tüm kimlik sağlayıcıları için talep boş döndürülür.

::: zone-end

::: zone pivot="b2c-custom-policy"

Azure AD B2C, [OAuth 2,0](authorization-code-flow.md) ve [OpenID Connect](openid-connect.md) kimlik sağlayıcılarının erişim belirtecinin geçirilmesini destekler. Diğer tüm kimlik sağlayıcıları için talep boş döndürülür.

::: zone-end

Aşağıdaki diyagramda bir kimlik sağlayıcısı belirtecinin uygulamanıza nasıl döndürdüğü gösterilmektedir: 

![Kimlik sağlayıcısı geçiş akışı](./media/idp-pass-through-user-flow/identity-provider-pass-through-flow.png)

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

::: zone pivot="b2c-user-flow"

## <a name="enable-the-claim"></a>Talebi etkinleştir

1. [Azure portalda](https://portal.azure.com/) Azure AD B2C kiracınızın genel yöneticisi olarak oturum açın.
2. Azure AD B2C kiracınızı içeren dizini kullandığınızdan emin olun. Üstteki menüden **Dizin + abonelik** filtresini seçin ve kiracınızı içeren dizini seçin.
3. Azure portalın sol üst köşesinde **Tüm hizmetler**’i seçin ve **Azure AD B2C**’yi arayıp seçin.
4. **Kullanıcı akışları ' nı (ilkeler)** seçin ve ardından Kullanıcı akışınızı seçin. Örneğin, **B2C_1_signupsignin1**.
5. **Uygulama talepleri**’ni seçin.
6. **Kimlik sağlayıcısı erişim belirteci** talebini etkinleştirin.

    ![Kimlik sağlayıcısı erişim belirteci talebini etkinleştir](./media/idp-pass-through-user-flow/identity-provider-pass-through-app-claim.png)

7. Kullanıcı akışını kaydetmek için **Kaydet** ' e tıklayın.

## <a name="test-the-user-flow"></a>Kullanıcı akışını test etme

Uygulamalarınızın Azure AD B2C test edilirken, içindeki talepleri gözden geçirmek için Azure AD B2C belirtecinin döndürüldüğünden yararlı olabilir `https://jwt.ms` .

1. Kullanıcı akışının genel bakış sayfasında, **Kullanıcı akışını Çalıştır**' ı seçin.
2. **Uygulama** için, daha önce kaydetmiş olduğunuz uygulamanızı seçin. Aşağıdaki örnekteki belirteci görmek için, **yanıt URL 'sinin** gösterilmesi gerekir `https://jwt.ms` .
3. **Kullanıcı akışını Çalıştır**' a tıklayın ve ardından hesap kimlik bilgilerinizle oturum açın. **İdp_access_token** talebinde kimlik sağlayıcısının erişim belirtecini görmeniz gerekir.

    Aşağıdaki örneğe benzer bir şey görmeniz gerekir:

    ![İdp_access_token bloğu vurgulanmış şekilde jwt.ms içinde kodu çözülmüş belirteç](./media/idp-pass-through-user-flow/identity-provider-pass-through-token.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="add-the-claim-elements"></a>Talep öğelerini ekleme

1. *TrustframeworkExtensions.xml* dosyanızı açın ve aşağıdaki **ClaimType** öğesini `identityProviderAccessToken` **claimsschema** öğesine tanıtıcısı ile ekleyin:

    ```xml
    <BuildingBlocks>
      <ClaimsSchema>
        <ClaimType Id="identityProviderAccessToken">
          <DisplayName>Identity Provider Access Token</DisplayName>
          <DataType>string</DataType>
          <AdminHelpText>Stores the access token of the identity provider.</AdminHelpText>
        </ClaimType>
        ...
      </ClaimsSchema>
    </BuildingBlocks>
    ```

2. Erişim belirtecinin istediğiniz her OAuth 2,0 kimlik sağlayıcısı için, **Outputclaim** öğesini, **teknisyen** öğesine ekleyin. Aşağıdaki örnekte, Facebook teknik profiline eklenen öğe gösterilmektedir:

    ```xml
    <ClaimsProvider>
      <DisplayName>Facebook</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Facebook-OAUTH">
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="identityProviderAccessToken" PartnerClaimType="{oauth2:access_token}" />
          </OutputClaims>
          ...
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

3. *TrustframeworkExtensions.xml* dosyasını kaydedin.
4. *SignUpOrSignIn.xml* gibi, bağlı olan taraf ilkesi dosyanızı açın ve bu **profil** için **outputclaim** öğesini ekleyin:

    ```xml
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
      <TechnicalProfile Id="PolicyProfile">
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="identityProviderAccessToken" PartnerClaimType="idp_access_token"/>
        </OutputClaims>
        ...
      </TechnicalProfile>
    </RelyingParty>
    ```

5. İlke dosyasını kaydedin.

## <a name="test-your-policy"></a>İlkenizi test etme

Azure AD B2C ' de Uygulamalarınızı sınarken, `https://jwt.ms` içindeki talepleri gözden geçirebilmek için Azure AD B2C belirtecinin geri döndürüldüğünden yararlı olabilir.

### <a name="upload-the-files"></a>Dosyaları karşıya yükleme

1. [Azure portalında](https://portal.azure.com/) oturum açın.
2. Üst menüdeki **Dizin + abonelik** filtresi ' ne tıklayarak ve kiracınızı içeren dizini seçerek Azure AD B2C kiracınızı içeren dizini kullandığınızdan emin olun.
3. Azure portal sol üst köşesindeki **tüm hizmetler** ' i seçin ve ardından **Azure AD B2C**' i arayıp seçin.
4. **Kimlik deneyimi çerçevesini** seçin.
5. Özel Ilkeler sayfasında, **Ilkeyi karşıya yükle**' ye tıklayın.
6. Varsa **Ilkenin üzerine yaz**' ı seçin ve ardından *TrustframeworkExtensions.xml* dosyasını arayıp seçin.
7. **Karşıya Yükle**’yi seçin.
8. *SignUpOrSignIn.xml* gibi bağlı olan taraf dosyası için 5 ile 7 arasındaki adımları yineleyin.

### <a name="run-the-policy"></a>İlkeyi çalıştırma

1. Değiştirdiğiniz ilkeyi açın. Örneğin, *B2C_1A_signup_signin*.
2. **Uygulama** için, daha önce kaydetmiş olduğunuz uygulamanızı seçin. Aşağıdaki örnekteki belirteci görmek için, **yanıt URL 'sinin** gösterilmesi gerekir `https://jwt.ms` .
3. **Şimdi Çalıştır**' ı seçin.

    Aşağıdaki örneğe benzer bir şey görmeniz gerekir:

    ![İdp_access_token bloğu vurgulanmış şekilde jwt.ms içinde kodu çözülmüş belirteç](./media/idp-pass-through-user-flow/identity-provider-pass-through-token-custom.png)

::: zone-end

## <a name="next-steps"></a>Sonraki adımlar

[Azure AD B2C belirteçlerine genel bakış](tokens-overview.md)hakkında daha fazla bilgi edinin.
