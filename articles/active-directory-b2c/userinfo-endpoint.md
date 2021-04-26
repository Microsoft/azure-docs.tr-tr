---
title: UserInfo uç noktası | Microsoft Docs
description: Azure Active Directory B2C bir özel ilkede bir UserInfo uç noktası tanımlayın.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/09/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: c060a029b1cdbdd890ced96cab732966cb652de0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "102500589"
---
# <a name="userinfo-endpoint"></a>UserInfo uç noktası

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

UserInfo uç noktası, [OpenID Connect Standard](https://openid.net/specs/openid-connect-core-1_0.html#UserInfo) (OIDC) belirtiminin bir parçasıdır ve kimliği doğrulanmış kullanıcı hakkında talepler döndürecek şekilde tasarlanmıştır. UserInfo uç noktası, bağlı olan taraf ilkesinde [Endpoint](relyingparty.md#endpoints) öğesi kullanılarak tanımlanır.  

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="userinfo-endpoint-overview"></a>UserInfo uç noktasına genel bakış

Kullanıcı bilgileri Kullanıcı yolculuğu şunları belirtir:

- **Yetkilendirme**: UserInfo uç noktası bir taşıyıcı belirteci ile korunuyor. Verilen erişim belirteci, UserInfo uç noktası için yetkilendirme üst bilgisinde sunulur. İlke, gelen belirteci doğrulayan ve Kullanıcı ObjectID gibi talepleri çıkaran teknik profili belirler. Kullanıcının objectID, UserInfo uç noktası yolculuğunun yanıtında döndürülecek talepleri almak için kullanılır. 
- **Düzenleme adımı**: 
  - Kullanıcı hakkında bilgi toplamak için bir düzenleme adımı kullanılır. Kullanıcı yolculuğu, gelen erişim belirtecindeki talepler temelinde, kullanıcı hakkındaki verileri almak için bir [Azure Active Directory teknik profili](active-directory-technical-profile.md) çağırır, örneğin kullanıcıyı ObjectID tarafından okunuyor. 
  - **Isteğe bağlı düzenleme adımları** -Kullanıcı hakkında daha fazla bilgi almak için REST API teknik bir profil gibi daha fazla düzenleme adımı ekleyebilirsiniz. 
  - **UserInfo veren** -UserInfo uç noktasının döndürdüğü taleplerin listesini belirtir.

## <a name="create-a-userinfo-endpoint"></a>UserInfo uç noktası oluşturma

### <a name="1-add-the-token-issuer-technical-profile"></a>1. belirteç verenin teknik profilini ekleyin

1. *TrustFrameworkExtensions.xml* dosyasını açın.
1. Zaten mevcut değilse, BuildingBlocks öğesi altındaki ilk öğe olarak bir ClaimsProvider öğesi ve onun alt öğelerini ekleyin.
1. Aşağıdaki talep sağlayıcısını ekleyin:

    ```xml
    <!-- 
    <ClaimsProviders> -->
      <ClaimsProvider>
        <DisplayName>Token Issuer</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="UserInfoIssuer">
            <DisplayName>JSON Issuer</DisplayName>
            <Protocol Name="None" />
            <OutputTokenFormat>JSON</OutputTokenFormat>
            <CryptographicKeys>
              <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
            </CryptographicKeys>
            <!-- The Below claims are what will be returned on the UserInfo Endpoint if in the Claims Bag-->
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="objectId"/>
              <InputClaim ClaimTypeReferenceId="givenName"/>
              <InputClaim ClaimTypeReferenceId="surname"/>
              <InputClaim ClaimTypeReferenceId="displayName"/>
              <InputClaim ClaimTypeReferenceId="signInNames.emailAddress"/>
            </InputClaims>
          </TechnicalProfile>
          <TechnicalProfile Id="UserInfoAuthorization">
            <DisplayName>UserInfo authorization</DisplayName>
            <Protocol Name="None" />
            <InputTokenFormat>JWT</InputTokenFormat>
            <Metadata>
              <!-- Update the Issuer and Audience below -->
              <!-- Audience is optional, Issuer is required-->
              <Item Key="issuer">https://yourtenant.b2clogin.com/11111111-1111-1111-1111-111111111111/v2.0/</Item>
              <Item Key="audience">[ "22222222-2222-2222-2222-222222222222", "33333333-3333-3333-3333-333333333333" ]</Item>
              <Item Key="client_assertion_type">urn:ietf:params:oauth:client-assertion-type:jwt-bearer</Item>
            </Metadata>
            <CryptographicKeys>
              <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
            </CryptographicKeys>
            <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
              <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" PartnerClaimType="email"/>
              <!-- Optional claims to read from the access token. -->
              <!-- <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
                 <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
                 <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name"/> -->
            </OutputClaims>
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
    <!-- 
    </ClaimsProviders> -->
    ``` 

1. **Userınfoıssuer** Technical profile Içindeki ınputclaim bölümü, döndürmek istediğiniz öznitelikleri belirtir. Kullanıcı yolculuğunun sonunda Userınfoıssuer teknik profili çağırılır. 
1. **Userınfoauthorization** teknik profili imza, verenin adı ve belirteç kitlesini doğrular ve gelen belirteçten talebi ayıklar. Aşağıdaki meta verileri ortamınızı yansıtacak şekilde değiştirin:
    1. **veren** -bu değer `iss` , erişim belirteci talep kapsamındaki talebe özdeş olmalıdır. Azure AD B2C tarafından verilen belirteçler, biçimde veren kullanır `https://yourtenant.b2clogin.com/your-tenant-id/v2.0/` . [Belirteç özelleştirmesi](configure-tokens.md)hakkında daha fazla bilgi edinin.
    1. **Idtokenaudience** `aud` , erişim belirteci talep içindeki talebe özdeş olmalıdır. Azure AD B2C, `aud` talep bağlı olan taraf UYGULAMANıZıN kimliğidir. Bu değer bir koleksiyondur ve bir virgül sınırlayıcısı kullanarak birden çok değeri destekler.

        Aşağıdaki erişim belirtecinde, `iss` talep değeri `https://contoso.b2clogin.com/11111111-1111-1111-1111-111111111111/v2.0/` . `aud`Talep değeri `22222222-2222-2222-2222-222222222222` .

        ```json
        {
          "exp": 1605549468,
          "nbf": 1605545868,
          "ver": "1.0",
          "iss": "https://contoso.b2clogin.com/11111111-1111-1111-1111-111111111111/v2.0/",
          "sub": "44444444-4444-4444-4444-444444444444",
          "aud": "22222222-2222-2222-2222-222222222222",
          "acr": "b2c_1a_signup_signin",
          "nonce": "defaultNonce",
          "iat": 1605545868,
          "auth_time": 1605545868,
          "name": "John Smith",
          "given_name": "John",
          "family_name": "Smith",
          "tid": "11111111-1111-1111-1111-111111111111"
        }
        ```
    
1.  **Userınfoauthorization** teknik profilinin outputclaim öğesi, erişim belirtecinden okumak istediğiniz öznitelikleri belirtir. **ClaimTypeReferenceId** , bir talep türüne başvurudur. İsteğe bağlı **Partnerclaimtype** , erişim belirtecinde tanımlanan talebin adıdır.



### <a name="2-add-the-userjourney-element"></a>2. Useryolculuk öğesini ekleyin 

[Kullanıcıyolculuğu](userjourneys.md) öğesi, uygulamanız ile etkileşim kurarken kullanıcının aldığı yolu tanımlar. Kullanıcı tarafından tanımlanan **Kullanıcı yolculuğu** Ile yoksa **Userıt neys** öğesini ekleyin `UserInfoJourney` :

```xml
<!-- 
<UserJourneys> -->
  <UserJourney Id="UserInfoJourney" DefaultCpimIssuerTechnicalProfileReferenceId="UserInfoIssuer">
    <Authorization>
      <AuthorizationTechnicalProfiles>
        <AuthorizationTechnicalProfile ReferenceId="UserInfoAuthorization" />
      </AuthorizationTechnicalProfiles>
    </Authorization>
    <OrchestrationSteps >
      <OrchestrationStep Order="1" Type="ClaimsExchange">
        <Preconditions>
          <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
            <Value>objectId</Value>
            <Action>SkipThisOrchestrationStep</Action>
          </Precondition>
        </Preconditions>
        <ClaimsExchanges UserIdentity="false">
          <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
        </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="2" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="UserInfoIssuer" />
    </OrchestrationSteps>
  </UserJourney>
<!-- 
</UserJourneys> -->
```

### <a name="3-include-the-endpoint-to-the-relying-party-policy"></a>3. uç noktayı bağlı olan taraf ilkesine ekleyin

UserInfo uç noktasını bağlı olan taraf uygulamasına eklemek için, *SocialAndLocalAccounts/SignUpOrSignIn.xml* dosyasına bir [uç nokta](relyingparty.md#endpoints) öğesi ekleyin. 

```xml
<!--
<RelyingParty> -->
  <Endpoints>
    <Endpoint Id="UserInfo" UserJourneyReferenceId="UserInfoJourney" />
  </Endpoints>
<!-- 
</RelyingParty> -->
```

Tamamlanmış bağlı olan taraf öğesi aşağıdaki gibi olacaktır:

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<TrustFrameworkPolicy xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="http://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06" PolicySchemaVersion="0.3.0.0" TenantId="yourtenant.onmicrosoft.com" PolicyId="B2C_1A_signup_signin" PublicPolicyUri="http://yourtenant.onmicrosoft.com/B2C_1A_signup_signin">
  <BasePolicy>
    <TenantId>yourtenant.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>
  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <Endpoints>
      <Endpoint Id="UserInfo" UserJourneyReferenceId="UserInfoJourney" />
    </Endpoints>
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
</TrustFrameworkPolicy>
```

### <a name="4-upload-the-files"></a>4. dosyaları karşıya yükleyin

1. [Azure portalında](https://portal.azure.com/) oturum açın.
1. Üst menüdeki **Dizin + abonelik** filtresini seçip kiracınızı içeren dizini seçerek Azure AD B2C kiracınızı içeren dizini kullandığınızdan emin olun.
1. Azure portal sol üst köşesindeki **tüm hizmetler** ' i seçin ve ardından **Azure AD B2C**' i arayıp seçin.
1. **Kimlik deneyimi çerçevesini** seçin.
1. **Özel ilkeler** sayfasında, **özel ilkeyi karşıya yükle**' yi seçin.
1. **Zaten varsa özel Ilkenin üzerine yaz**' ı seçin ve ardından *TrustframeworkExtensions.xml* dosyasını arayıp seçin.
1. **Karşıya Yükle**'ye tıklayın.
1. *SignUpOrSignIn.xml* gibi bağlı olan taraf dosyası için 5 ile 7 arasındaki adımları yineleyin.

## <a name="calling-the-userinfo-endpoint"></a>UserInfo uç noktasını çağırma

UserInfo uç noktası, uygulamanız için bir belirteç alırken alınan erişim belirtecini kullanarak çağrılan standart OAuth2 taşıyıcı belirteç API 'sini kullanır. Kullanıcı hakkında talepler içeren bir JSON yanıtı döndürür. UserInfo uç noktası şurada Azure AD B2C barındırılır:

```http
https://yourtenant.b2clogin.com/yourtenant.onmicrosoft.com/policy-name/openid/v2.0/userinfo
```

/.Exe Configure endpoint (OpenID Connect bulgu belgesi) `userinfo_endpoint` alanını listeler. Şu adreste bulunan/. well-bilinen yapılandırma uç noktasını kullanarak UserInfo uç noktasını program aracılığıyla bulabilirsiniz: 

```http
https://yourtenant.b2clogin.com/yourtenant.onmicrosoft.com/policy-name/v2.0/.well-known/openid-configuration 
```

### <a name="test-the-policy"></a>İlkeyi test etme

1. **Özel ilkeler** altında, UserInfo uç noktasını tümleştirmiş olan ilkeyi seçin. Örneğin, *B2C_1A_SignUpOrSignIn*.
1. **Şimdi Çalıştır**' ı seçin. 
1. **Uygulama Seç**' in altında, önceden kaydolduğunuz uygulamanızı seçin. **Yanıt URL 'Si Seç** için öğesini seçin `https://jwt.ms` . Daha fazla bilgi için bkz. [Azure Active Directory B2C bir Web uygulamasını kaydetme](tutorial-register-applications.md).
1. Bir e-posta adresi veya sosyal hesap ile kaydolun veya oturum açın.
1. İd_token Web sitesinden kodlanmış biçiminde kopyalayın [https://jwt.ms](https://jwt.ms) . Bunu, UserInfo uç noktasına bir GET isteği göndermek ve Kullanıcı bilgilerini almak için kullanabilirsiniz.
1. UserInfo uç noktasına bir GET isteği gönderir ve Kullanıcı bilgilerini alın.

```http
GET /yourtenant.onmicrosoft.com/b2c_1a_signup_signin/openid/v2.0/userinfo
Host: b2cninja.b2clogin.com
Authorization: Bearer <your access token>
```

Başarılı bir yanıt şöyle görünür:

```json
{
    "objectId": "44444444-4444-4444-4444-444444444444",
    "givenName": "John",
    "surname": "Smith",
    "displayName": "John Smith",
    "signInNames.emailAddress": "john.s@contoso.com"
}
```

## <a name="next-steps"></a>Sonraki Adımlar

- [GitHub](https://github.com/azure-ad-b2c/samples/tree/master/policies/user-info-endpoint)'Da bir UserInfo uç nokta ilkesi örneği bulabilirsiniz.

::: zone-end
