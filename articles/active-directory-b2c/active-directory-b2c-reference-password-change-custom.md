---
title: Azure Active Directory B2C | özel ilkeleri kullanarak parola değişikliğini yapılandırma | Microsoft Docs
description: Kullanıcıların Azure Active Directory B2C özel ilkeleri kullanarak parolalarını değiştirmesini nasıl sağlayacağınızı öğrenin.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 0775920e1d6572223253edbfc066123a515b5480
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71065517"
---
# <a name="configure-password-change-using-custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C özel ilkeleri kullanarak parola değişikliğini yapılandırma

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) ' de, yerel bir hesapla oturum açan kullanıcıların, kendi kimlik doğrulamasını e-posta doğrulamaya göre kanıtlamaları gerekmeden parolalarını değiştirmesine olanak sağlayabilirsiniz. Oturumun süresi dolduğunda Kullanıcı parola değiştirme akışına geçtiğinde, yeniden oturum açması istenir. Bu makalede, [özel ilkelerde](active-directory-b2c-overview-custom.md)parola değişikliğini yapılandırma gösterilmektedir. Kullanıcı akışları için [self servis parola sıfırlamayı](active-directory-b2c-reference-sspr.md) yapılandırmak da mümkündür.

## <a name="prerequisites"></a>Önkoşullar

[Active Directory B2C özel ilkeleri kullanmaya başlama](active-directory-b2c-get-started-custom.md)bölümündeki adımları uygulayın.

## <a name="add-the-elements"></a>Öğeleri ekleyin

1. *TrustframeworkExtensions. xml* dosyanızı açın ve aşağıdaki **ClaimType** öğesini `oldPassword` [claimsschema](claimsschema.md) öğesine tanıtıcısı ile ekleyin:

    ```XML
    <BuildingBlocks>
      <ClaimsSchema>
        <ClaimType Id="oldPassword">
          <DisplayName>Old Password</DisplayName>
          <DataType>string</DataType>
          <UserHelpText>Enter password</UserHelpText>
          <UserInputType>Password</UserInputType>
        </ClaimType>
      </ClaimsSchema>
    </BuildingBlocks>
    ```

2. Bir [ClaimsProvider](claimsproviders.md) öğesi, kullanıcının kimliğini doğrulayan teknik profili içerir. Aşağıdaki talep sağlayıcılarını **Claimsproviders** öğesine ekleyin:

    ```XML
    <ClaimsProviders>
      <ClaimsProvider>
        <DisplayName>Local Account SignIn</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="login-NonInteractive-PasswordChange">
            <DisplayName>Local Account SignIn</DisplayName>
            <Protocol Name="OpenIdConnect" />
            <Metadata>
              <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">We can't seem to find your account</Item>
              <Item Key="UserMessageIfInvalidPassword">Your password is incorrect</Item>
              <Item Key="UserMessageIfOldPasswordUsed">Looks like you used an old password</Item>
              <Item Key="ProviderName">https://sts.windows.net/</Item>
              <Item Key="METADATA">https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration</Item>
              <Item Key="authorization_endpoint">https://login.microsoftonline.com/{tenant}/oauth2/token</Item>
              <Item Key="response_types">id_token</Item>
              <Item Key="response_mode">query</Item>
              <Item Key="scope">email openid</Item>
              <Item Key="UsePolicyInRedirectUri">false</Item>
              <Item Key="HttpBinding">POST</Item>
              <Item Key="client_id">ProxyIdentityExperienceFrameworkAppId</Item>
              <Item Key="IdTokenAudience">IdentityExperienceFrameworkAppId</Item>
            </Metadata>
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="signInName" PartnerClaimType="username" Required="true" />
              <InputClaim ClaimTypeReferenceId="oldPassword" PartnerClaimType="password" Required="true" />
              <InputClaim ClaimTypeReferenceId="grant_type" DefaultValue="password" />
              <InputClaim ClaimTypeReferenceId="scope" DefaultValue="openid" />
              <InputClaim ClaimTypeReferenceId="nca" PartnerClaimType="nca" DefaultValue="1" />
              <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="ProxyIdentityExperienceFrameworkAppID" />
              <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="IdentityExperienceFrameworkAppID" />
            </InputClaims>
            <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="oid" />
              <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid" />
              <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
              <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
              <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
              <OutputClaim ClaimTypeReferenceId="userPrincipalName" PartnerClaimType="upn" />
              <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
            </OutputClaims>
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
      <ClaimsProvider>
        <DisplayName>Local Account Password Change</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="LocalAccountWritePasswordChangeUsingObjectId">
            <DisplayName>Change password (username)</DisplayName>
            <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
            <Metadata>
              <Item Key="ContentDefinitionReferenceId">api.selfasserted</Item>
            </Metadata>
            <CryptographicKeys>
              <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
            </CryptographicKeys>
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="objectId" />
            </InputClaims>
            <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="oldPassword" Required="true" />
              <OutputClaim ClaimTypeReferenceId="newPassword" Required="true" />
              <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
            </OutputClaims>
            <ValidationTechnicalProfiles>
              <ValidationTechnicalProfile ReferenceId="login-NonInteractive-PasswordChange" />
              <ValidationTechnicalProfile ReferenceId="AAD-UserWritePasswordUsingObjectId" />
            </ValidationTechnicalProfiles>
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
    </ClaimsProviders>
    ```

    Önkoşul `IdentityExperienceFrameworkAppId` öğreticisinde oluşturduğunuz IdentityExperienceFramework uygulamasının uygulama kimliğiyle değiştirin. Daha `ProxyIdentityExperienceFrameworkAppId` önce oluşturduğunuz ProxyIdentityExperienceFramework uygulamasının uygulama kimliğiyle değiştirin.

3. [Kullanıcıyolculuğu](userjourneys.md) öğesi, uygulamanız ile etkileşim kurarken kullanıcının aldığı yolu tanımlar. Kullanıcı tarafından tanımlanan `PasswordChange` **Kullanıcı yolculuğu** Ile yoksa **userıt neys** öğesini ekleyin:

    ```XML
    <UserJourneys>
      <UserJourney Id="PasswordChange">
        <OrchestrationSteps>
          <OrchestrationStep Order="1" Type="ClaimsProviderSelection" ContentDefinitionReferenceId="api.idpselections">
            <ClaimsProviderSelections>
              <ClaimsProviderSelection TargetClaimsExchangeId="LocalAccountSigninEmailExchange" />
            </ClaimsProviderSelections>
          </OrchestrationStep>
          <OrchestrationStep Order="2" Type="ClaimsExchange">
            <ClaimsExchanges>
              <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="3" Type="ClaimsExchange">
            <ClaimsExchanges>
              <ClaimsExchange Id="NewCredentials" TechnicalProfileReferenceId="LocalAccountWritePasswordChangeUsingObjectId" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="4" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
        </OrchestrationSteps>
        <ClientDefinition ReferenceId="DefaultWeb" />
      </UserJourney>
    </UserJourneys>
    ```

4. *TrustFrameworkExtensions. xml* ilke dosyasını kaydedin.
5. Başlangıç paketiyle indirdiğiniz *Profileedit. xml* dosyasını kopyalayın ve *Profileeditpasswordchange. xml*olarak adlandırın.
6. Yeni dosyayı açın ve **PolicyId** özniteliğini benzersiz bir değerle güncelleştirin. Bu değer, ilkenizin adıdır. Örneğin, *B2C_1A_profile_edit_password_change*.
7. ' `<DefaultUserJourney>` Deki **referenceıd** özniteliğini, oluşturduğunuz yeni Kullanıcı yolculuğunun kimliğiyle eşleşecek şekilde değiştirin. Örneğin, *PasswordChange*.
8. Yaptığınız değişiklikleri kaydedin.

Örnek ilkeyi [burada](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/password-change)bulabilirsiniz.

## <a name="test-your-policy"></a>İlkenizi test etme

Azure AD B2C ' de Uygulamalarınızı sınarken, içindeki talepleri gözden geçirebilmek `https://jwt.ms` için Azure AD B2C belirtecinin geri döndürüldüğünden yararlı olabilir.

### <a name="upload-the-files"></a>Dosyaları karşıya yükleme

1. [Azure Portal](https://portal.azure.com/) oturum açın.
2. Üst menüdeki **Dizin + abonelik** filtresini seçip kiracınızı içeren dizini seçerek Azure AD B2C kiracınızı içeren dizini kullandığınızdan emin olun.
3. Azure portal sol üst köşesindeki **tüm hizmetler** ' i seçin ve ardından **Azure AD B2C**' i arayıp seçin.
4. **Kimlik deneyimi çerçevesini**seçin.
5. Özel Ilkeler sayfasında, **Ilkeyi karşıya yükle**' ye tıklayın.
6. Varsa **Ilkenin üzerine yaz**' ı seçin ve ardından *TrustframeworkExtensions. xml* dosyasını bulun ve seçin.
7. **Karşıya Yükle**'ye tıklayın.
8. Bağlı olan taraf dosyası için *Profileeditpasswordchange. xml*gibi 5 ' ten 7 ' ye kadar olan adımları yineleyin.

### <a name="run-the-policy"></a>İlkeyi çalıştır

1. Değiştirdiğiniz ilkeyi açın. Örneğin, *B2C_1A_profile_edit_password_change*.
2. **Uygulama**için, daha önce kaydetmiş olduğunuz uygulamanızı seçin. Belirteci görmek için, **yanıt URL 'sinin** gösterilmesi `https://jwt.ms`gerekir.
3. **Şimdi çalıştır**’a tıklayın. Daha önce oluşturduğunuz acouyeniden bağlama ile oturum açın. Şimdi parolayı değiştirme fırsatına sahip olmanız gerekir.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Active Directory B2C özel ilkeleri kullanarak parola karmaşıklığını nasıl yapılandırabileceğinizi](active-directory-b2c-reference-password-complexity-custom.md)öğrenin.
