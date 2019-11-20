---
title: Uygulamanıza özel bir ilke aracılığıyla bir erişim belirteci geçirin Azure Active Directory B2C
description: OAuth 2.0 kimlik sağlayıcıları için bir erişim belirtecini, Azure Active Directory B2C uygulamanıza özel bir ilke aracılığıyla talep olarak nasıl geçirebileceğinizi öğrenin.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/17/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: b6795af0829a288c36cad5b848fed50a99dc1bfc
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/15/2019
ms.locfileid: "69510135"
---
# <a name="pass-an-access-token-through-a-custom-policy-to-your-application-in-azure-active-directory-b2c"></a>Uygulamanıza özel bir ilke aracılığıyla bir erişim belirteci geçirin Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) içinde [özel bir ilke](active-directory-b2c-get-started-custom.md) , uygulamanızın kullanıcılarına bir kimlik sağlayıcısına kaydolma veya oturum açma olanağı sağlar. Bu durumda, Azure AD B2C kimlik sağlayıcısından bir [erişim belirteci](active-directory-b2c-reference-tokens.md) alır. Azure AD B2C, kullanıcı hakkındaki bilgileri almak için bu belirteci kullanır. Belirteci, Azure AD B2C kaydedileceği uygulamalara iletmek için özel ilkenize bir talep türü ve çıkış talebi eklersiniz.

Azure AD B2C, [OAuth 2,0](active-directory-b2c-reference-oauth-code.md) ve [OpenID Connect](active-directory-b2c-reference-oidc.md) kimlik sağlayıcılarının erişim belirtecinin geçirilmesini destekler. Diğer tüm kimlik sağlayıcıları için talep boş döndürülür.

## <a name="prerequisites"></a>Önkoşullar

* Özel ilkeniz bir OAuth 2,0 veya OpenID Connect kimlik sağlayıcısı ile yapılandırılır.

## <a name="add-the-claim-elements"></a>Talep öğelerini ekleme

1. *TrustframeworkExtensions. xml* dosyanızı açın ve aşağıdaki **ClaimType** öğesini `identityProviderAccessToken` **claimsschema** öğesine tanıtıcısı ile ekleyin:

    ```XML
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

    ```XML
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

3. *TrustframeworkExtensions. xml* dosyasını kaydedin.
4. *Signuporsignın. xml*gibi bağlı olan taraf ilkesi dosyanızı açın ve bir **deneme profili**öğesine **outputclaim** öğesini ekleyin:

    ```XML
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

Azure AD B2C ' de Uygulamalarınızı sınarken, içindeki talepleri gözden geçirebilmek `https://jwt.ms` için Azure AD B2C belirtecinin geri döndürüldüğünden yararlı olabilir.

### <a name="upload-the-files"></a>Dosyaları karşıya yükleme

1. [Azure Portal](https://portal.azure.com/) oturum açın.
2. Üst menüdeki **Dizin + abonelik** filtresi ' ne tıklayarak ve kiracınızı içeren dizini seçerek Azure AD B2C kiracınızı içeren dizini kullandığınızdan emin olun.
3. Azure portal sol üst köşesindeki **tüm hizmetler** ' i seçin ve ardından **Azure AD B2C**' i arayıp seçin.
4. **Kimlik deneyimi çerçevesini**seçin.
5. Özel Ilkeler sayfasında, **Ilkeyi karşıya yükle**' ye tıklayın.
6. Varsa **Ilkenin üzerine yaz**' ı seçin ve ardından *TrustframeworkExtensions. xml* dosyasını bulun ve seçin.
7. **Karşıya Yükle**’yi seçin.
8. *Signuporsignın. xml*gibi bağlı olan taraf dosyası için 5 ile 7 arasındaki adımları yineleyin.

### <a name="run-the-policy"></a>İlkeyi çalıştır

1. Değiştirdiğiniz ilkeyi açın. Örneğin, *B2C_1A_signup_signin*.
2. **Uygulama**için, daha önce kaydetmiş olduğunuz uygulamanızı seçin. Aşağıdaki örnekteki belirteci görmek için, **yanıt URL 'sinin** gösterilmesi `https://jwt.ms`gerekir.
3. **Şimdi Çalıştır**' ı seçin.

    Aşağıdaki örneğe benzer bir şey görmeniz gerekir:

    ![İdp_access_token bloğu vurgulanmış şekilde jwt.ms içinde kodu çözülmüş belirteç](./media/idp-pass-through-custom/idp-pass-through-custom-token.PNG)

## <a name="next-steps"></a>Sonraki adımlar

[Azure Active Directory B2C belirteç başvurusunda](active-directory-b2c-reference-tokens.md)belirteçler hakkında daha fazla bilgi edinin.
