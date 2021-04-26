---
title: SAML protokolünü kullanarak bir Salesforce SAML sağlayıcısı ile oturum açma ayarlama
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C 'de SAML protokolünü kullanarak bir Salesforce SAML sağlayıcısı ile oturum açma ayarlayın.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/15/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: cf2f0cdf8b7c540e569067d68374eef55d3479fe
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107028222"
---
# <a name="set-up-sign-in-with-a-salesforce-saml-provider-by-using-saml-protocol-in-azure-active-directory-b2c"></a>Azure Active Directory B2C 'de SAML protokolünü kullanarak bir Salesforce SAML sağlayıcısı ile oturum açma ayarlama

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"
[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Bu makalede, Azure Active Directory B2C (Azure AD B2C) ' de [özel ilkeler](custom-policy-overview.md) kullanılarak Salesforce kuruluştan kullanıcıların oturum açma özelliğini nasıl etkinleştireceğinizi gösterilmektedir. Özel ilkeye bir [SAML kimlik sağlayıcısı](identity-provider-generic-saml.md) ekleyerek oturum açmayı etkinleştirebilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]
- Daha önce yapmadıysanız, [ücretsiz bir geliştirici sürümü hesabına](https://developer.salesforce.com/signup)kaydolun. Bu makalede [Salesforce şimşek deneyimi](https://developer.salesforce.com/page/Lightning_Experience_FAQ)kullanılmaktadır.
- Salesforce kuruluşunuz için [bir etki alanım ayarlayın](https://help.salesforce.com/articleView?id=domain_name_setup.htm&language=en_US&type=0) .

## <a name="set-up-salesforce-as-an-identity-provider"></a>Salesforce 'ı bir kimlik sağlayıcısı olarak ayarlama

1. [Salesforce 'Ta oturum açın](https://login.salesforce.com/).
2. Sol taraftaki menüde **Ayarlar**' ın altında **kimlik**' i genişletin ve **kimlik sağlayıcısı**' nı seçin.
3. **Kimlik sağlayıcısını etkinleştir**' i seçin.
4. **Sertifikayı seçin** altında, Salesforce 'ın Azure AD B2C iletişim kurmak için kullanmasını istediğiniz sertifikayı seçin. Varsayılan sertifikayı kullanabilirsiniz.
5. **Kaydet**’e tıklayın.

### <a name="create-a-connected-app-in-salesforce"></a>Salesforce 'ta bağlantılı uygulama oluşturma

1. **Kimlik sağlayıcısı** sayfasında, **artık bağlı uygulamalar aracılığıyla hizmet sağlayıcıları oluşturuluyor ' i seçin. buraya tıklayın.**
2. **Temel bilgiler** altında, bağlı uygulamanız için gerekli değerleri girin.
3. **Web uygulaması ayarları** altında **SAML etkinleştir** kutusunu işaretleyin.
4. **VARLıK kimliği** alanına aşağıdaki URL 'yi girin. Değerini `your-tenant` Azure AD B2C kiracınızın adıyla değiştirdiğinizden emin olun.

      ```
      https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase
      ```

      [Özel bir etki alanı](custom-domain.md)kullanırken aşağıdaki biçimi kullanın:

      ```
      https://your-domain-name/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase
      ```

6. **ACS URL 'si** alanına aşağıdaki URL 'yi girin. Değerini `your-tenant` Azure AD B2C kiracınızın adıyla değiştirdiğinizden emin olun.

      ```
      https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer
      ```

      [Özel bir etki alanı](custom-domain.md)kullanırken aşağıdaki biçimi kullanın:

      ```
      https://your-domain-name/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer
      ```

7. Listenin en altına gidin ve **Kaydet**' e tıklayın.

### <a name="get-the-metadata-url"></a>Meta veri URL 'sini al

1. Bağlı uygulamanızın Genel Bakış sayfasında **Yönet**' e tıklayın.
2. **Meta veri bulma uç noktası** için değeri kopyalayın ve kaydedin. Bu makalenin ilerleyen bölümlerinde kullanacaksınız.

### <a name="set-up-salesforce-users-to-federate"></a>Salesforce kullanıcılarını federasyona kurmak için ayarlama

1. Bağlı uygulamanızın **Yönet** sayfasında **profilleri Yönet**' e tıklayın.
2. Azure AD B2C ile federasyona eklemek istediğiniz profilleri (veya Kullanıcı gruplarını) seçin. Bir sistem yöneticisi olarak, Salesforce hesabınızı kullanarak federasyona eklemek için **Sistem Yöneticisi** onay kutusunu seçin.

## <a name="create-a-self-signed-certificate"></a>Otomatik olarak imzalanan sertifika oluşturma

[!INCLUDE [active-directory-b2c-create-self-signed-certificate](../../includes/active-directory-b2c-create-self-signed-certificate.md)]

## <a name="create-a-policy-key"></a>İlke anahtarı oluşturma

Azure AD B2C kiracınızda oluşturduğunuz sertifikayı depolamanız gerekir.

1. [Azure portalında](https://portal.azure.com/) oturum açın.
2. Üst menüdeki **Dizin + abonelik** filtresini seçip kiracınızı içeren dizini seçerek Azure AD B2C kiracınızı içeren dizini kullandığınızdan emin olun.
3. Azure portal sol üst köşesindeki **tüm hizmetler** ' i seçin ve ardından **Azure AD B2C**' i arayıp seçin.
4. Genel Bakış sayfasında **kimlik deneyimi çerçevesi**' ni seçin.
5. **Ilke anahtarlarını** seçin ve ardından **Ekle**' yi seçin.
6. **Seçenekler** için öğesini seçin `Upload` .
7. İlke için bir **Ad** girin. Örneğin, SAMLSigningCert. Ön ek, `B2C_1A_` anahtarınızın adına otomatik olarak eklenir.
8. ' A gidin ve oluşturduğunuz B2CSigningCert. pfx sertifikasını seçin.
9. Sertifika için **parolayı** girin.
3. **Oluştur**’a tıklayın.

## <a name="add-a-claims-provider"></a>Talep sağlayıcısı ekleme

Kullanıcıların bir Salesforce hesabı kullanarak oturum açmasını istiyorsanız, hesabı Azure AD B2C bir uç nokta aracılığıyla iletişim kurabildiği bir talep sağlayıcı olarak tanımlamanız gerekir. Uç noktası, belirli bir kullanıcının kimliği doğrulandığını doğrulamak için Azure AD B2C tarafından kullanılan bir talep kümesi sağlar.

Bir Salesforce hesabını, ilkenizin uzantı dosyasındaki **Claimsproviders** öğesine ekleyerek bir talep sağlayıcı olarak tanımlayabilirsiniz. Daha fazla bilgi için bkz. [SAML kimlik sağlayıcısı tanımlama](identity-provider-generic-saml.md).

1. *TrustFrameworkExtensions.xml* açın.
1. **Claimsproviders** öğesini bulun. Yoksa, kök öğenin altına ekleyin.
1. Yeni bir **ClaimsProvider** 'ı aşağıdaki şekilde ekleyin:

    ```xml
    <ClaimsProvider>
      <Domain>salesforce.com</Domain>
      <DisplayName>Salesforce</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Salesforce-SAML2">
          <DisplayName>Salesforce</DisplayName>
          <Description>Login with your Salesforce account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="WantsSignedAssertions">false</Item>
            <Item Key="PartnerEntity">https://contoso-dev-ed.my.salesforce.com/.well-known/samlidp.xml</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="userId"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="username"/>
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication"/>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="salesforce.com" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-idp"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. **Partnerentity** değerini, daha önce kopyaladığınız Salesforce meta veri URL 'si ile güncelleştirin.
1. Her iki **Storagereferenceıd** örneğinin değerini imzalama sertifikanızın anahtarının adına güncelleştirin. Örneğin, B2C_1A_SAMLSigningCert.
1. Bölümünü bulun `<ClaimsProviders>` ve AŞAĞıDAKI XML kod parçacığını ekleyin. İlkeniz zaten `SM-Saml-idp` Teknik profili içeriyorsa, sonraki adıma atlayın. Daha fazla bilgi için bkz. [Çoklu oturum açma oturumu yönetimi](custom-policy-reference-sso.md).

    ```xml
    <ClaimsProvider>
      <DisplayName>Session Management</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="SM-Saml-idp">
          <DisplayName>Session Management Provider</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="IncludeSessionIndex">false</Item>
            <Item Key="RegisterServiceProviders">false</Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```
1. Dosyayı kaydedin.

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]

```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="SalesforceExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="SalesforceExchange" TechnicalProfileReferenceId="Salesforce-SAML2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>Özel ilkenizi test etme

1. Örneğin, bağlı olan taraf ilkenizi seçin `B2C_1A_signup_signin` .
1. **Uygulama** için, [daha önce kaydetmiş](tutorial-register-applications.md)olduğunuz bir Web uygulamasını seçin. **Yanıt URL 'si** gösterilmesi gerekir `https://jwt.ms` .
1. **Şimdi Çalıştır** düğmesini seçin.
1. Kaydolma veya oturum açma sayfasından Salesforce hesabı ile oturum **açmak için Salesforce** ' ı seçin.

Oturum açma işlemi başarılı olursa, tarayıcınız öğesine yönlendirilir `https://jwt.ms` ve bu, Azure AD B2C tarafından döndürülen belirtecin içeriğini görüntüler.

::: zone-end