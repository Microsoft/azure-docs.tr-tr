---
title: Kaynak sahibi parola kimlik bilgileri akışı ayarlama
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C ' de kaynak sahibi parola kimlik bilgileri (ROPC) akışını ayarlamayı öğrenin.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/11/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 3a7c93bb0e0dcc51e35bc27fa0799d8410e66df6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104581890"
---
# <a name="set-up-a-resource-owner-password-credentials-flow-in-azure-active-directory-b2c"></a>Azure Active Directory B2C bir kaynak sahibi parola kimlik bilgileri akışı ayarlama

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Azure Active Directory B2C (Azure AD B2C) ' de, kaynak sahibi parola kimlik bilgileri (ROPC) akışı, bir OAuth standart kimlik doğrulama akışsıdır. Bu akışta, bağlı olan taraf olarak da bilinen bir uygulama, belirteçler için geçerli kimlik bilgilerini değiş tokuş eder. Kimlik bilgileri bir kullanıcı KIMLIĞI ve parola içerir. Döndürülen belirteçler bir KIMLIK belirteci, erişim belirteci ve yenileme belirteci.

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]


## <a name="ropc-flow-notes"></a>ROPC akış notları

Azure Active Directory B2C (Azure AD B2C) ' de, aşağıdaki seçenekler desteklenir:

- **Yerel istemci**: kimlik doğrulaması sırasında Kullanıcı etkileşimi, kod bir Kullanıcı tarafı cihazda çalıştırıldığında oluşur. Cihaz, Android ve iOS gibi yerel bir işletim sisteminde çalışan bir mobil uygulama olabilir.
- **Ortak istemci akışı**: API çağrısında yalnızca bir uygulama tarafından toplanan kullanıcı kimlik bilgileri gönderilir. Uygulamanın kimlik bilgileri gönderilmez.
- **Yeni talepler Ekle**: kimlik belirteci içerikleri yeni talepler eklemek için değiştirilebilir.

Aşağıdaki akışlar desteklenmez:

- **Sunucudan sunucuya**: kimlik koruma sisteminin, etkileşimin bir parçası olarak çağırandan (yerel istemci) toplanan GÜVENILIR bir IP adresi olması gerekir. Sunucu tarafı API çağrısında yalnızca sunucunun IP adresi kullanılır. Başarısız kimlik doğrulamaları için dinamik bir eşik aşılırsa, kimlik koruma sistemi bir saldırgan olarak yinelenen bir IP adresini tanımlayabilir.
- **Gizli istemci akışı**: uygulama istemci kimliği onaylanır, ancak uygulama gizli anahtarı doğrulanmaz.

ROPC akışını kullanırken aşağıdakileri göz önünde bulundurun:

- Kimlik doğrulama akışında Kullanıcı etkileşimi gerektiren herhangi bir kesinti olduğunda ROPC çalışmaz. Örneğin, bir parolanın süresi dolduğunda veya değiştirilmesi gerektiğinde, [çok faktörlü kimlik doğrulaması](multi-factor-authentication.md) gerekir ya da oturum açma sırasında daha fazla bilginin toplanması gerekir (örneğin, kullanıcı onayı).
- ROPC yalnızca yerel hesapları destekler. Kullanıcılar Microsoft, Google +, Twitter, AD-FS veya Facebook gibi [Federal Kimlik sağlayıcılarıyla](add-identity-provider.md) oturum açamaz.
- Oturumumu [Açık bırak (KMSI)](session-behavior.md#enable-keep-me-signed-in-kmsi)dahil olmak üzere [oturum yönetimi](session-behavior.md)geçerli değildir.


## <a name="register-an-application"></a>Bir uygulamayı kaydetme

[!INCLUDE [active-directory-b2c-appreg-ropc](../../includes/active-directory-b2c-appreg-ropc.md)]

::: zone pivot="b2c-user-flow"

##  <a name="create-a-resource-owner-user-flow"></a>Kaynak sahibi Kullanıcı akışı oluşturma

1. Azure portalda Azure AD B2C kiracınızın genel yöneticisi olarak oturum açın.
2. Azure AD B2C kiracınıza geçiş yapmak için portalın sağ üst köşesindeki B2C dizinini seçin.
3. **Kullanıcı akışları**' nı seçin ve **Yeni Kullanıcı akışı**' nı seçin.
4. **Kaynak sahibi parola kimlik bilgilerini (ROPC) kullanarak oturum aç '** ı seçin.
5. **Sürüm** altında **önizlemenin** seçili olduğundan emin olun ve ardından **Oluştur**' u seçin.
7. Kullanıcı akışı için *ROPC_Auth* gibi bir ad girin.
8. **Uygulama talepleri** bölümünde **daha fazla göster**' e tıklayın.
9. Uygulamanız için gereken görünen ad, e-posta adresi ve kimlik sağlayıcısı gibi uygulama taleplerini seçin.
10. **Tamam**’ı ve ardından **Oluştur**’u seçin.

::: zone-end

::: zone pivot="b2c-custom-policy"

##  <a name="create-a-resource-owner-policy"></a>Kaynak sahibi ilkesi oluşturma

1. *TrustFrameworkExtensions.xml* dosyasını açın.
2. Zaten mevcut değilse, **Buildingblocks** öğesi altındaki ilk öğe olarak bir **Claimsschema** öğesi ve onun alt öğelerini ekleyin:

    ```xml
    <ClaimsSchema>
      <ClaimType Id="logonIdentifier">
        <DisplayName>User name or email address that the user can use to sign in</DisplayName>
        <DataType>string</DataType>
      </ClaimType>
      <ClaimType Id="resource">
        <DisplayName>The resource parameter passes to the ROPC endpoint</DisplayName>
        <DataType>string</DataType>
      </ClaimType>
      <ClaimType Id="refreshTokenIssuedOnDateTime">
        <DisplayName>An internal parameter used to determine whether the user should be permitted to authenticate again using their existing refresh token.</DisplayName>
        <DataType>string</DataType>
      </ClaimType>
      <ClaimType Id="refreshTokensValidFromDateTime">
        <DisplayName>An internal parameter used to determine whether the user should be permitted to authenticate again using their existing refresh token.</DisplayName>
        <DataType>string</DataType>
      </ClaimType>
    </ClaimsSchema>
    ```

3. **Claimsschema**'dan sonra, **buildingblocks** öğesine bir **claimstransformations** öğesi ve onun alt öğelerini ekleyin:

    ```xml
    <ClaimsTransformations>
      <ClaimsTransformation Id="CreateSubjectClaimFromObjectID" TransformationMethod="CreateStringClaim">
        <InputParameters>
          <InputParameter Id="value" DataType="string" Value="Not supported currently. Use oid claim." />
        </InputParameters>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="sub" TransformationClaimType="createdClaim" />
        </OutputClaims>
      </ClaimsTransformation>

      <ClaimsTransformation Id="AssertRefreshTokenIssuedLaterThanValidFromDate" TransformationMethod="AssertDateTimeIsGreaterThan">
        <InputClaims>
          <InputClaim ClaimTypeReferenceId="refreshTokenIssuedOnDateTime" TransformationClaimType="leftOperand" />
          <InputClaim ClaimTypeReferenceId="refreshTokensValidFromDateTime" TransformationClaimType="rightOperand" />
        </InputClaims>
        <InputParameters>
          <InputParameter Id="AssertIfEqualTo" DataType="boolean" Value="false" />
          <InputParameter Id="AssertIfRightOperandIsNotPresent" DataType="boolean" Value="true" />
        </InputParameters>
      </ClaimsTransformation>
    </ClaimsTransformations>
    ```

4. **DisplayName** olan **ClaimsProvider** öğesini bulun `Local Account SignIn` ve aşağıdaki teknik profili ekleyin:

    ```xml
    <TechnicalProfile Id="ResourceOwnerPasswordCredentials-OAUTH2">
      <DisplayName>Local Account SignIn</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <Metadata>
        <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">We can't seem to find your account</Item>
        <Item Key="UserMessageIfInvalidPassword">Your password is incorrect</Item>
        <Item Key="UserMessageIfOldPasswordUsed">Looks like you used an old password</Item>
        <Item Key="DiscoverMetadataByTokenIssuer">true</Item>
        <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/</Item>
        <Item Key="METADATA">https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration</Item>
        <Item Key="authorization_endpoint">https://login.microsoftonline.com/{tenant}/oauth2/token</Item>
        <Item Key="response_types">id_token</Item>
        <Item Key="response_mode">query</Item>
        <Item Key="scope">email openid</Item>
        <Item Key="grant_type">password</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="logonIdentifier" PartnerClaimType="username" Required="true" DefaultValue="{OIDC:Username}"/>
        <InputClaim ClaimTypeReferenceId="password" Required="true" DefaultValue="{OIDC:Password}" />
        <InputClaim ClaimTypeReferenceId="grant_type" DefaultValue="password" />
        <InputClaim ClaimTypeReferenceId="scope" DefaultValue="openid" />
        <InputClaim ClaimTypeReferenceId="nca" PartnerClaimType="nca" DefaultValue="1" />
        <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="ProxyIdentityExperienceFrameworkAppId" />
        <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="IdentityExperienceFrameworkAppId" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="oid" />
        <OutputClaim ClaimTypeReferenceId="userPrincipalName" PartnerClaimType="upn" />
      </OutputClaims>
      <OutputClaimsTransformations>
        <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromObjectID" />
      </OutputClaimsTransformations>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
    ```

    **Client_id** **DefaultValue** değerini, önkoşul öğreticisinde oluşturduğunuz ProxyIdentityExperienceFramework uygulamasının uygulama kimliğiyle değiştirin. Ardından, önkoşul öğreticisinde oluşturduğunuz IdentityExperienceFramework uygulamasının uygulama KIMLIĞIYLE **Resource_id** **DefaultValue** değerini değiştirin.

5. Aşağıdaki **ClaimsProvider** öğelerini, **claimsproviders** öğesine teknik profilleriyle birlikte ekleyin:

    ```xml
    <ClaimsProvider>
      <DisplayName>Azure Active Directory</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AAD-UserReadUsingObjectId-CheckRefreshTokenDate">
          <Metadata>
            <Item Key="Operation">Read</Item>
            <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
          </Metadata>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
          </InputClaims>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="objectId" />
            <OutputClaim ClaimTypeReferenceId="refreshTokensValidFromDateTime" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="AssertRefreshTokenIssuedLaterThanValidFromDate" />
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromObjectID" />
          </OutputClaimsTransformations>
          <IncludeTechnicalProfile ReferenceId="AAD-Common" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>

    <ClaimsProvider>
      <DisplayName>Session Management</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="SM-RefreshTokenReadAndSetup">
          <DisplayName>Trustframework Policy Engine Refresh Token Setup Technical Profile</DisplayName>
          <Protocol Name="None" />
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="objectId" />
            <OutputClaim ClaimTypeReferenceId="refreshTokenIssuedOnDateTime" />
          </OutputClaims>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>

    <ClaimsProvider>
      <DisplayName>Token Issuer</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="JwtIssuer">
          <Metadata>
            <!-- Point to the redeem refresh token user journey-->
            <Item Key="RefreshTokenUserJourneyId">ResourceOwnerPasswordCredentials-RedeemRefreshToken</Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

6. **TrustFrameworkPolicy** öğesine bir **Userbir neys** öğesi ve onun alt öğelerini ekleyin:

    ```xml
    <UserJourney Id="ResourceOwnerPasswordCredentials">
      <PreserveOriginalAssertion>false</PreserveOriginalAssertion>
      <OrchestrationSteps>
        <OrchestrationStep Order="1" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="ResourceOwnerFlow" TechnicalProfileReferenceId="ResourceOwnerPasswordCredentials-OAUTH2" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="2" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="3" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
      </OrchestrationSteps>
    </UserJourney>
    <UserJourney Id="ResourceOwnerPasswordCredentials-RedeemRefreshToken">
      <PreserveOriginalAssertion>false</PreserveOriginalAssertion>
      <OrchestrationSteps>
        <OrchestrationStep Order="1" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="RefreshTokenSetupExchange" TechnicalProfileReferenceId="SM-RefreshTokenReadAndSetup" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="2" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="CheckRefreshTokenDateFromAadExchange" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId-CheckRefreshTokenDate" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="3" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
      </OrchestrationSteps>
    </UserJourney>
    ```

7. Azure AD B2C kiracınızdaki **özel ilkeler** sayfasında, **ilkeyi karşıya yükle**' yi seçin.
8. Varsa **Ilkenin üzerine yazmayı** etkinleştirin ve sonra *TrustFrameworkExtensions.xml* dosyasına gidip seçin.
9. **Karşıya Yükle**'ye tıklayın.

## <a name="create-a-relying-party-file"></a>Bağlı olan taraf dosyası oluşturma

Sonra, oluşturduğunuz Kullanıcı yolculuğunu başlatan bağlı olan taraf dosyasını güncelleştirin:

1. Çalışma dizininizde *SignUpOrSignin.xml* dosyanın bir kopyasını oluşturun ve *ROPC_Auth.xml* olarak yeniden adlandırın.
2. Yeni dosyayı açın ve **TrustFrameworkPolicy** Için **PolicyId** özniteliğinin değerini benzersiz bir değere değiştirin. İlke KIMLIĞI, ilkenizin adıdır. Örneğin, **B2C_1A_ROPC_Auth**.
3. **Defaultuseryolculuney** Içindeki **referenceıd** özniteliğinin değerini olarak değiştirin `ResourceOwnerPasswordCredentials` .
4. **Outputclaim** öğesini yalnızca aşağıdaki talepleri içerecek şekilde değiştirin:

    ```xml
    <OutputClaim ClaimTypeReferenceId="sub" />
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="displayName" DefaultValue="" />
    <OutputClaim ClaimTypeReferenceId="givenName" DefaultValue="" />
    <OutputClaim ClaimTypeReferenceId="surname" DefaultValue="" />
    ```

5. Azure AD B2C kiracınızdaki **özel ilkeler** sayfasında, **ilkeyi karşıya yükle**' yi seçin.
6. Varsa **Ilkenin üzerine yazmayı** etkinleştirin ve sonra *ROPC_Auth.xml* dosyasına gidip seçin.
7. **Karşıya Yükle**'ye tıklayın.


::: zone-end

## <a name="test-the-ropc-flow"></a>ROPC akışını test etme

Bir API çağrısı oluşturmak için en sevdiğiniz API Geliştirme uygulamanızı kullanın ve ilkenizde hata ayıklama yanıtı ' nı gözden geçirin. POST isteğinin gövdesi olarak aşağıdaki bilgilerle bu örnek gibi bir çağrı oluşturun:

`https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/B2C_1A_ROPC_Auth/oauth2/v2.0/token`

- `<tenant-name>`Azure AD B2C kiracınızın adıyla değiştirin.
- `B2C_1A_ROPC_Auth`Kaynak sahibi parola kimlik bilgileri ilkenizin tam adıyla değiştirin.

| Anahtar | Değer |
| --- | ----- |
| username | `user-account` |
| password | `password1` |
| grant_type | password |
| scope | OpenID `application-id` offline_access |
| client_id | `application-id` |
| response_type | belirteç id_token |

- `user-account`Kiracınızdaki bir kullanıcı hesabının adıyla değiştirin.
- `password1`Kullanıcı hesabının parolasıyla değiştirin.
- `application-id` *ROPC_Auth_app* kaydında uygulama kimliğiyle değiştirin.
- Yenileme belirteci almak istiyorsanız *Offline_access* isteğe bağlıdır.

Gerçek GÖNDERI isteği aşağıdaki örneğe benzer şekilde görünür:

```https
POST /<tenant-name>.onmicrosoft.com/B2C_1A_ROPC_Auth/oauth2/v2.0/token HTTP/1.1
Host: <tenant-name>.b2clogin.com
Content-Type: application/x-www-form-urlencoded

username=contosouser.outlook.com.ws&password=Passxword1&grant_type=password&scope=openid+bef22d56-552f-4a5b-b90a-1988a7d634ce+offline_access&client_id=bef22d56-552f-4a5b-b90a-1988a7d634ce&response_type=token+id_token
```

Çevrimdışı erişime sahip başarılı bir yanıt aşağıdaki örneğe benzer şekilde görünür:

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlki...",
    "token_type": "Bearer",
    "expires_in": "3600",
    "refresh_token": "eyJraWQiOiJacW9pQlp2TW5pYVc2MUY0TnlfR3REVk1EVFBLbUJLb0FUcWQ1ZWFja1hBIiwidmVyIjoiMS4wIiwiemlwIjoiRGVmbGF0ZSIsInNlciI6Ij...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlki..."
}
```

## <a name="redeem-a-refresh-token"></a>Yenileme belirteci kullanma

Burada gösterilenler gibi bir GÖNDERI çağrısı oluşturun. İsteğin gövdesi olarak aşağıdaki tablodaki bilgileri kullanın:

`https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/B2C_1A_ROPC_Auth/oauth2/v2.0/token`

- `<tenant-name>`Azure AD B2C kiracınızın adıyla değiştirin.
- `B2C_1A_ROPC_Auth`Kaynak sahibi parola kimlik bilgileri ilkenizin tam adıyla değiştirin.

| Anahtar | Değer |
| --- | ----- |
| grant_type | refresh_token |
| response_type | id_token |
| client_id | `application-id` |
| kaynak | `application-id` |
| refresh_token | `refresh-token` |

- `application-id` *ROPC_Auth_app* kaydında uygulama kimliğiyle değiştirin.
- `refresh-token`Önceki yanıtta geri gönderilen **refresh_token** değiştirin.

Başarılı bir yanıt aşağıdaki örneğe benzer şekilde görünür:

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrNHh5b2pORnVtMWtsMll0djhkbE5QNC1jNTdkTzZRR1RWQndhT...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrNHh5b2pORnVtMWtsMll0djhkbE5QNC1jNTdkTzZRR1RWQn...",
    "token_type": "Bearer",
    "not_before": 1533672990,
    "expires_in": 3600,
    "expires_on": 1533676590,
    "resource": "bef2222d56-552f-4a5b-b90a-1988a7d634c3",
    "id_token_expires_in": 3600,
    "profile_info": "eyJ2ZXIiOiIxLjAiLCJ0aWQiOiI1MTZmYzA2NS1mZjM2LTRiOTMtYWE1YS1kNmVlZGE3Y2JhYzgiLCJzdWIiOm51bGwsIm5hbWUiOiJEYXZpZE11IiwicHJlZmVycmVkX3VzZXJuYW1lIjpudWxsLCJpZHAiOiJMb2NhbEFjY291bnQifQ",
    "refresh_token": "eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMCIsInppcCI6IkRlZmxhdGUiLCJzZXIiOiIxLjAi...",
    "refresh_token_expires_in": 1209600
}
```

## <a name="use-a-native-sdk-or-app-auth"></a>Yerel SDK veya App-Auth kullanın

Azure AD B2C, genel istemci kaynak sahibi parola kimlik bilgileri için OAuth 2,0 standartlarını karşılar ve çoğu istemci SDK 'Sı ile uyumlu olmalıdır. En son bilgiler için bkz. [OAuth Için yerel uygulama SDK 'sı 2,0 ve OpenID Connect modern en iyi uygulamaları uygulama](https://appauth.io/).

## <a name="next-steps"></a>Sonraki adımlar

Android ve [iOS](https://aka.ms/aadb2ciosappauthropc) [için](https://aka.ms/aadb2cappauthropc) GitHub 'dan Azure AD B2C ile kullanılmak üzere yapılandırılmış çalışma örneklerini indirin.
