---
title: Özel ilkeler kullanarak parola değişikliğini yapılandırma
titleSuffix: Azure AD B2C
description: Kullanıcıların Azure Active Directory B2C özel ilkeleri kullanarak parolalarını değiştirmesini nasıl sağlayacağınızı öğrenin.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/22/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: cd63144074577d4ff3564da41e672dd1ca226dcb
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107257169"
---
# <a name="configure-password-change-using-custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C özel ilkeleri kullanarak parola değişikliğini yapılandırma

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Azure Active Directory B2C (Azure AD B2C) ' de, bir yerel hesapla oturum açan kullanıcıların, kimliğini bir e-posta doğrulaması aracılığıyla kanıtlamaları gerekmeden parolalarını değiştirmesine olanak sağlayabilirsiniz. Parola değiştirme akışı aşağıdaki adımları içerir:

1. Kullanıcı kendi yerel hesabında oturum açar. Oturum hala etkinse, Azure AD B2C kullanıcıyı yetkilendirir ve sonraki adıma atlar.
1. Kullanıcı **eski parolayı** doğrular ve ardından **Yeni parolayı** oluşturur ve onaylar.

![Parola değiştirme akışı](./media/add-password-change-policy/password-change-flow.png)  

> [!TIP]
> Parola değiştirme akışı, kullanıcıların parolasını yalnızca Kullanıcı parolasını öğrendiğinde ve değiştirmek istediğinde parolalarını değiştirmesine izin verir. Ayrıca, kullanıcının parolasını unutması için [self servis parola sıfırlamayı](add-password-reset-policy.md) da etkinleştirmenizi öneririz.

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="prerequisites"></a>Önkoşullar

* [Active Directory B2C özel ilkeleri kullanmaya başlama](tutorial-create-user-flows.md?pivots=b2c-custom-policy)bölümündeki adımları uygulayın.
* Henüz yapmadıysanız, [bir Web uygulamasını Azure Active Directory B2C kaydedin](tutorial-register-applications.md).

## <a name="add-the-elements"></a>Öğeleri ekleyin

1. *TrustframeworkExtensions.xml* dosyanızı açın ve aşağıdaki **ClaimType** öğesini `oldPassword` [claimsschema](claimsschema.md) öğesine tanıtıcısı ile ekleyin:

    ```xml
    <BuildingBlocks>
      <ClaimsSchema>
        <ClaimType Id="oldPassword">
          <DisplayName>Old Password</DisplayName>
          <DataType>string</DataType>
          <UserHelpText>Enter your old password</UserHelpText>
          <UserInputType>Password</UserInputType>
        </ClaimType>
      </ClaimsSchema>
    </BuildingBlocks>
    ```

2. Bir [ClaimsProvider](claimsproviders.md) öğesi, kullanıcının kimliğini doğrulayan teknik profili içerir. Aşağıdaki talep sağlayıcılarını **Claimsproviders** öğesine ekleyin:

    ```xml
    <ClaimsProviders>
      <ClaimsProvider>
        <DisplayName>Local Account SignIn</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="login-NonInteractive-PasswordChange">
            <DisplayName>Local Account SignIn</DisplayName>
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="oldPassword" PartnerClaimType="password" Required="true" />
              </InputClaims>
            <IncludeTechnicalProfile ReferenceId="login-NonInteractive" />
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

3. [Kullanıcıyolculuğu](userjourneys.md) öğesi, uygulamanız ile etkileşim kurarken kullanıcının aldığı yolu tanımlar. Kullanıcı tarafından tanımlanan **Kullanıcı yolculuğu** Ile yoksa **Userıt neys** öğesini ekleyin `PasswordChange` :

    ```xml
    <UserJourneys>
      <UserJourney Id="PasswordChange">
        <OrchestrationSteps>
          <OrchestrationStep Order="1" Type="ClaimsProviderSelection" ContentDefinitionReferenceId="api.signuporsignin">
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
          <OrchestrationStep Order="4" Type="ClaimsExchange">
            <ClaimsExchanges>
              <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="5" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
        </OrchestrationSteps>
        <ClientDefinition ReferenceId="DefaultWeb" />
      </UserJourney>
    </UserJourneys>
    ```

4. *TrustFrameworkExtensions.xml* ilke dosyasını kaydedin.
5. Yüklediğiniz *ProfileEdit.xml* dosyasını başlangıç paketiyle kopyalayın ve *ProfileEditPasswordChange.xml* adlandırın.
6. Yeni dosyayı açın ve **PolicyId** özniteliğini benzersiz bir değerle güncelleştirin. Bu değer, ilkenizin adıdır. Örneğin, *B2C_1A_profile_edit_password_change*.
7. ' Deki **Referenceıd** özniteliğini, `<DefaultUserJourney>` oluşturduğunuz yeni Kullanıcı yolculuğunun kimliğiyle eşleşecek şekilde değiştirin. Örneğin, *PasswordChange*.
8. Yaptığınız değişiklikleri kaydedin.

## <a name="upload-and-test-the-policy"></a>İlkeyi karşıya yükleme ve test etme

1. [Azure portalında](https://portal.azure.com/) oturum açın.
2. Üst menüdeki **Dizin + abonelik** filtresini seçip kiracınızı içeren dizini seçerek Azure AD B2C kiracınızı içeren dizini kullandığınızdan emin olun.
3. Azure portal sol üst köşesindeki **tüm hizmetler** ' i seçin ve ardından **Azure AD B2C**' i arayıp seçin.
4. **Kimlik deneyimi çerçevesini** seçin.
5. Özel Ilkeler sayfasında, **Ilkeyi karşıya yükle**' ye tıklayın.
6. Varsa **Ilkenin üzerine yaz**' ı seçin ve ardından *TrustframeworkExtensions.xml* dosyasını arayıp seçin.
7. **Karşıya Yükle**'ye tıklayın.
8. *ProfileEditPasswordChange.xml* gibi bağlı olan taraf dosyası için 5 ile 7 arasındaki adımları yineleyin.

### <a name="run-the-policy"></a>İlkeyi çalıştırma

1. Değiştirdiğiniz ilkeyi açın. Örneğin, *B2C_1A_profile_edit_password_change*.
2. **Uygulama** için, daha önce kaydetmiş olduğunuz uygulamanızı seçin. Belirteci görmek için, **yanıt URL 'sinin** gösterilmesi gerekir `https://jwt.ms` .
3. **Şimdi çalıştır**’a tıklayın. Daha önce oluşturduğunuz hesapla oturum açın. Şimdi parolayı değiştirme fırsatına sahip olmanız gerekir.

## <a name="next-steps"></a>Sonraki adımlar

- [GitHub](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/password-change)'da örnek ilkeyi bulun.
- [Azure AD B2C 'de parola karmaşıklığını nasıl yapılandırabileceğinizi](password-complexity.md)öğrenin.
- [Parola sıfırlama akışı](add-password-reset-policy.md)ayarlayın.

::: zone-end
