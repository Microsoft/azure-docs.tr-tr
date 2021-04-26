---
title: LinkedIn hesabı ile kaydolma ve oturum açma ayarlama
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C kullanarak uygulamalarınızda LinkedIn hesapları olan kaydolma ve oturum açma bilgilerini sağlayın.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/17/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 51a9635d42b07eb27b05312d292ca890c7963b4f
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107028239"
---
# <a name="set-up-sign-up-and-sign-in-with-a-linkedin-account-using-azure-active-directory-b2c"></a>Azure Active Directory B2C kullanarak bir LinkedIn hesabı ile kaydolma ve oturum açma ayarlama

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-a-linkedin-application"></a>LinkedIn uygulaması oluşturma

Azure Active Directory B2C (Azure AD B2C) ' de LinkedIn hesabı olan kullanıcılar için oturum açmayı etkinleştirmek üzere [LinkedIn geliştiricileri Web sitesinde](https://www.developer.linkedin.com/)bir uygulama oluşturmanız gerekir. Daha fazla bilgi için bkz. [yetkilendirme kodu akışı](/linkedin/shared/authentication/authorization-code-flow). Zaten bir LinkedIn hesabınız yoksa, ' de kaydolabilirsiniz [https://www.linkedin.com/](https://www.linkedin.com/) .

1. LinkedIn hesabı kimlik bilgilerinizle [LinkedIn geliştiricileri Web sitesinde](https://www.developer.linkedin.com/) oturum açın.
1. **Uygulamalarım**' ı seçin ve ardından **uygulama oluştur**' a tıklayın.
1. **Uygulama adı**, **LinkedIn sayfası**, **Gizlilik ilkesi URL 'si** ve **uygulama logosu** girin.
1. LinkedIn **API kullanım koşullarını** kabul edin ve **uygulama oluştur**' a tıklayın.
1. **AUTH** sekmesini seçin. **Kimlik doğrulama anahtarları**' nın altında, **Istemci kimliği** ve **istemci parolası** değerlerini kopyalayın. Bunları kiracınızda bir kimlik sağlayıcısı olarak yapılandırmak için her ikisine de ihtiyacınız olacak. **Istemci parolası** önemli bir güvenlik kimlik bilgileridir.
1. **Uygulamanızın yetkili yeniden yönlendirme URL 'lerinin** yanındaki Düzenle kalem ' i seçin ve ardından **yeniden yönlendirme URL 'si Ekle**' yi seçin. `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` yazın. [Özel bir etki alanı](custom-domain.md)kullanıyorsanız, girin `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp` . `your-tenant-name`, Kiracınızın adıyla ve `your-domain-name` özel etki alanınız ile değiştirin. Kiracı, Azure AD B2C büyük harfle tanımlansa bile kiracı adınızı girerken tüm küçük harfleri kullanmanız gerekir. **Güncelleştir**’i seçin.
1. Varsayılan olarak, LinkedIn uygulamanız oturum açma ile ilgili kapsamlar için onaylanmamış değildir. Bir gözden geçirme istemek için, **Ürünler** sekmesini seçin ve ardından **LinkedIn ile oturum aç**' ı seçin. İnceleme tamamlandığında, uygulamanıza gereken kapsamlar eklenir.
   > [!NOTE]
   > Uygulamanız için şu anda izin verilen kapsamları, **OAuth 2,0 kapsamları** bölümündeki **AUTH** sekmesinde görebilirsiniz.

::: zone pivot="b2c-user-flow"

## <a name="configure-linkedin-as-an-identity-provider"></a>LinkedIn 'i kimlik sağlayıcısı olarak yapılandırma

1. [Azure portalda](https://portal.azure.com/) Azure AD B2C kiracınızın genel yöneticisi olarak oturum açın.
1. Üst menüdeki **Dizin + abonelik** filtresini seçip kiracınızı içeren dizini seçerek Azure AD B2C kiracınızı içeren dizini kullandığınızdan emin olun.
1. Azure portalın sol üst köşesinde **Tüm hizmetler**’i seçin ve **Azure AD B2C**’yi arayıp seçin.
1. **Kimlik sağlayıcıları**' nı seçin ve **LinkedIn**' i seçin.
1. Bir **ad** girin. Örneğin, *LinkedIn*.
1. **ISTEMCI kimliği** için, daha önce oluşturduğunuz LinkedIn UYGULAMASıNıN istemci kimliğini girin.
1. **İstemci parolası** için, kaydettiğiniz istemci gizli anahtarını girin.
1. **Kaydet**’i seçin.

## <a name="add-linkedin-identity-provider-to-a-user-flow"></a>Kullanıcı akışına LinkedIn kimlik sağlayıcısı ekleme 

Bu noktada, LinkedIn kimlik sağlayıcısı ayarlanmıştır, ancak oturum açma sayfalarında henüz mevcut değildir. LinkedIn kimlik sağlayıcısını bir Kullanıcı akışına eklemek için:

1. Azure AD B2C kiracınızda **Kullanıcı akışları**' nı seçin.
1. LinkedIn kimlik sağlayıcısını eklemek istediğiniz kullanıcı akışına tıklayın.
1. **Sosyal kimlik sağlayıcıları** altında **LinkedIn**' i seçin.
1. **Kaydet**’i seçin.
1. İlkenizi test etmek için **Kullanıcı akışını Çalıştır**' ı seçin.
1. **Uygulama** için, daha önce kaydettiğiniz *testapp1* adlı Web uygulamasını seçin. **Yanıt URL 'si** gösterilmesi gerekir `https://jwt.ms` .
1. **Kullanıcı akışını Çalıştır** düğmesini seçin.
1. Kaydolma veya oturum açma sayfasından LinkedIn hesabı ile oturum **açmak için LinkedIn** ' i seçin.

Oturum açma işlemi başarılı olursa, tarayıcınız öğesine yönlendirilir `https://jwt.ms` ve bu, Azure AD B2C tarafından döndürülen belirtecin içeriğini görüntüler.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>İlke anahtarı oluşturma

Daha önce Azure AD B2C kiracınızda kaydettiğiniz istemci gizli anahtarını depolamanız gerekir.

1. [Azure portalında](https://portal.azure.com/) oturum açın.
2. Azure AD B2C kiracınızı içeren dizini kullandığınızdan emin olun. Üstteki menüden **Dizin + abonelik** filtresini seçin ve kiracınızı içeren dizini seçin.
3. Azure portal sol üst köşesindeki **tüm hizmetler** ' i seçin ve ardından **Azure AD B2C**' i arayıp seçin.
4. Genel Bakış sayfasında **kimlik deneyimi çerçevesi**' ni seçin.
5. **İlke anahtarlarını** seçin ve ardından **Ekle**' yi seçin.
6. **Seçenekler** için öğesini seçin `Manual` .
7. İlke anahtarı için bir **ad** girin. Örneğin, `LinkedInSecret`. *B2C_1A_* ön eki, anahtarınızın adına otomatik olarak eklenir.
8. **Gizli**, daha önce kaydettiğiniz istemci gizli anahtarını girin.
9. **Anahtar kullanımı** için öğesini seçin `Signature` .
10. **Oluştur**’a tıklayın.

## <a name="configure-linkedin-as-an-identity-provider"></a>LinkedIn 'i kimlik sağlayıcısı olarak yapılandırma

Kullanıcıların bir LinkedIn hesabı kullanarak oturum açmasını sağlamak için hesabı, Azure AD B2C bir uç nokta aracılığıyla iletişim kurabildiği bir talep sağlayıcı olarak tanımlamanız gerekir. Uç noktası, belirli bir kullanıcının kimliği doğrulandığını doğrulamak için Azure AD B2C tarafından kullanılan bir talep kümesi sağlar.

Bir LinkedIn hesabını, ilkenizin uzantısı dosyasındaki **Claimsproviders** öğesine ekleyerek bir talep sağlayıcı olarak tanımlayın.

1. Düzenleyicinizde *SocialAndLocalAccounts/* * TrustFrameworkExtensions.xml** * dosyasını açın. Bu dosya, önkoşullardan birinin bir parçası olarak indirdiğiniz [özel ilke başlangıç paketidir][starter-pack] .
1. **Claimsproviders** öğesini bulun. Yoksa, kök öğenin altına ekleyin.
1. Yeni bir **ClaimsProvider** 'ı aşağıdaki şekilde ekleyin:

    ```xml
    <ClaimsProvider>
      <Domain>linkedin.com</Domain>
      <DisplayName>LinkedIn</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="LinkedIn-OAuth2">
          <DisplayName>LinkedIn</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">linkedin</Item>
            <Item Key="authorization_endpoint">https://www.linkedin.com/oauth/v2/authorization</Item>
            <Item Key="AccessTokenEndpoint">https://www.linkedin.com/oauth/v2/accessToken</Item>
            <Item Key="ClaimsEndpoint">https://api.linkedin.com/v2/me</Item>
            <Item Key="scope">r_emailaddress r_liteprofile</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="external_user_identity_claim_id">id</Item>
            <Item Key="BearerTokenTransmissionMethod">AuthorizationHeader</Item>
            <Item Key="ResolveJsonPathsInJsonTokens">true</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="client_id">Your LinkedIn application client ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_LinkedInSecret" />
          </CryptographicKeys>
          <InputClaims />
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="id" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName.localized" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName.localized" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="linkedin.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="ExtractGivenNameFromLinkedInResponse" />
            <OutputClaimsTransformation ReferenceId="ExtractSurNameFromLinkedInResponse" />
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId" />
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. **Client_id** değerini, daha önce kaydettiğiniz LinkedIn UYGULAMASıNıN istemci kimliği ile değiştirin.
1. Dosyayı kaydedin.

### <a name="add-the-claims-transformations"></a>Talep dönüşümlerini ekleyin

LinkedIn Technical profile, **ExtractGivenNameFromLinkedInResponse** ve **ExtractSurNameFromLinkedInResponse** talep dönüştürmelerinin claimstransformaler listesine eklenmesini gerektirir. Dosyanızda tanımlanmış bir **Claimstransformations** öğesi yoksa, üst XML öğelerini aşağıda gösterildiği gibi ekleyin. Talep dönüştürmelerinin Ayrıca **Nullstringclaim** adlı yeni bir talep türü tanımlanmış olması gerekir.

*TrustFrameworkExtensions.xml* dosyasının en üstüne yakın olan **buildingblocks** öğesini ekleyin. Bir örnek için bkz. *TrustFrameworkBase.xml* .

```xml
<BuildingBlocks>
  <ClaimsSchema>
    <!-- Claim type needed for LinkedIn claims transformations -->
    <ClaimType Id="nullStringClaim">
      <DisplayName>nullClaim</DisplayName>
      <DataType>string</DataType>
      <AdminHelpText>A policy claim to store output values from ClaimsTransformations that aren't useful. This claim should not be used in TechnicalProfiles.</AdminHelpText>
      <UserHelpText>A policy claim to store output values from ClaimsTransformations that aren't useful. This claim should not be used in TechnicalProfiles.</UserHelpText>
    </ClaimType>
  </ClaimsSchema>

  <ClaimsTransformations>
    <!-- Claim transformations needed for LinkedIn technical profile -->
    <ClaimsTransformation Id="ExtractGivenNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
    <ClaimsTransformation Id="ExtractSurNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="surname" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="surname" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
  </ClaimsTransformations>
</BuildingBlocks>
```

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAuth2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>Özel ilkenizi test etme

1. Örneğin, bağlı olan taraf ilkenizi seçin `B2C_1A_signup_signin` .
1. **Uygulama** için, [daha önce kaydetmiş](tutorial-register-applications.md)olduğunuz bir Web uygulamasını seçin. **Yanıt URL 'si** gösterilmesi gerekir `https://jwt.ms` .
1. **Şimdi Çalıştır** düğmesini seçin.
1. Kaydolma veya oturum açma sayfasından LinkedIn hesabı ile oturum **açmak için LinkedIn** ' i seçin.

Oturum açma işlemi başarılı olursa, tarayıcınız öğesine yönlendirilir `https://jwt.ms` ve bu, Azure AD B2C tarafından döndürülen belirtecin içeriğini görüntüler.

## <a name="migration-from-v10-to-v20"></a>V 1.0 'dan v 2.0'a geçiş

LinkedIn [, API 'lerini son zamanlarda v 1.0 'dan v 2.0 'a güncelleştirmiştir](https://engineering.linkedin.com/blog/2018/12/developer-program-updates). Mevcut yapılandırmanızı yeni yapılandırmaya geçirmek için, teknik profildeki öğeleri güncelleştirmek üzere aşağıdaki bölümlerdeki bilgileri kullanın.

### <a name="replace-items-in-the-metadata"></a>Meta verilerdeki öğeleri değiştir

**Teknisyen**'in var olan **meta veri** öğesinde aşağıdaki **öğe** öğelerini ' den güncelleştirin:

```xml
<Item Key="ClaimsEndpoint">https://api.linkedin.com/v1/people/~:(id,first-name,last-name,email-address,headline)</Item>
<Item Key="scope">r_emailaddress r_basicprofile</Item>
```

Hedef:

```xml
<Item Key="ClaimsEndpoint">https://api.linkedin.com/v2/me</Item>
<Item Key="scope">r_emailaddress r_liteprofile</Item>
```

### <a name="add-items-to-the-metadata"></a>Meta verilere öğe ekleme

**Teknisyen**'In **meta verilerinde** aşağıdaki **öğe** öğelerini ekleyin:

```xml
<Item Key="external_user_identity_claim_id">id</Item>
<Item Key="BearerTokenTransmissionMethod">AuthorizationHeader</Item>
<Item Key="ResolveJsonPathsInJsonTokens">true</Item>
```

### <a name="update-the-outputclaims"></a>Outputclaim 'yi güncelleştirme

**Teknisyen**'In mevcut **outputclaim** öğelerinde aşağıdaki **outputclaim** öğelerini güncelleştirin:

```xml
<OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
<OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
```

Hedef:

```xml
<OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName.localized" />
<OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName.localized" />
```

### <a name="add-new-outputclaimstransformation-elements"></a>Yeni Outputclaimstrans, öğeleri ekle

**Teknisyen** tarafından belirlenen **Outputclaimsformations** öğelerini aşağıdaki **outputclaimstranssize** öğelerini ekleyin:

```xml
<OutputClaimsTransformation ReferenceId="ExtractGivenNameFromLinkedInResponse" />
<OutputClaimsTransformation ReferenceId="ExtractSurNameFromLinkedInResponse" />
```

### <a name="define-the-new-claims-transformations-and-claim-type"></a>Yeni talep dönüştürmelerini ve talep türünü tanımlayın

Son adımda, tanımlanması gereken yeni talep dönüşümleri eklediniz. Talep dönüşümlerini tanımlamak için bunları **Claimstransformations** listesine ekleyin. Dosyanızda tanımlanmış bir **Claimstransformations** öğesi yoksa, üst XML öğelerini aşağıda gösterildiği gibi ekleyin. Talep dönüştürmelerinin Ayrıca **Nullstringclaim** adlı yeni bir talep türü tanımlanmış olması gerekir.

**Buildingblocks** öğesi dosyanın en üstüne eklenmelidir. Örnek olarak *TrustframeworkBase.xml* bakın.

```xml
<BuildingBlocks>
  <ClaimsSchema>
    <!-- Claim type needed for LinkedIn claims transformations -->
    <ClaimType Id="nullStringClaim">
      <DisplayName>nullClaim</DisplayName>
      <DataType>string</DataType>
      <AdminHelpText>A policy claim to store unuseful output values from ClaimsTransformations. This claim should not be used in a TechnicalProfiles.</AdminHelpText>
      <UserHelpText>A policy claim to store unuseful output values from ClaimsTransformations. This claim should not be used in a TechnicalProfiles.</UserHelpText>
    </ClaimType>
  </ClaimsSchema>

  <ClaimsTransformations>
    <!-- Claim transformations needed for LinkedIn technical profile -->
    <ClaimsTransformation Id="ExtractGivenNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
    <ClaimsTransformation Id="ExtractSurNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="surname" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="surname" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
  </ClaimsTransformations>
</BuildingBlocks>
```

### <a name="obtain-an-email-address"></a>E-posta adresi alın

V 1.0 ile v 2.0 arasında LinkedIn geçişinin bir parçası olarak, e-posta adresini elde etmek için başka bir API 'ye ek bir çağrı gerekir. Kaydolma sırasında e-posta adresini edinmeniz gerekiyorsa şunları yapın:

1. Kullanıcının oturum açmasını sağlamak için Azure AD B2C LinkedIn ile federasyona eklemek için yukarıdaki adımları uygulayın. Federasyonun bir parçası olarak, Azure AD B2C LinkedIn 'e erişim belirtecini alır.
1. LinkedIn erişim belirtecini bir talebe kaydedin. [Buradaki yönergelere bakın](idp-pass-through-user-flow.md).
1. LinkedIn API 'sine istek yapan aşağıdaki talep sağlayıcısını ekleyin `/emailAddress` . Bu isteği yetkilendirmek için LinkedIn erişim belirtecine ihtiyacınız vardır.

    ```xml
    <ClaimsProvider>
      <DisplayName>REST APIs</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="API-LinkedInEmail">
          <DisplayName>Get LinkedIn email</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
              <Item Key="ServiceUrl">https://api.linkedin.com/v2/emailAddress?q=members&amp;projection=(elements*(handle~))</Item>
              <Item Key="AuthenticationType">Bearer</Item>
              <Item Key="UseClaimAsBearerToken">identityProviderAccessToken</Item>
              <Item Key="SendClaimsIn">Url</Item>
              <Item Key="ResolveJsonPathsInJsonTokens">true</Item>
          </Metadata>
          <InputClaims>
              <InputClaim ClaimTypeReferenceId="identityProviderAccessToken" />
          </InputClaims>
          <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="elements[0].handle~.emailAddress" />
          </OutputClaims>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Kullanıcı yolculuğuna aşağıdaki düzenleme adımını ekleyin, böylece Kullanıcı LinkedIn kullanarak oturum açtığında API talep sağlayıcısı tetiklenir. Sayıyı uygun şekilde güncelleştirdiğinizden emin olun `Order` . Bu adımı, LinkedIn teknik profilini tetikleyen düzenleme adımından hemen sonra ekleyin.

    ```xml
    <!-- Extra step for LinkedIn to get the email -->
    <OrchestrationStep Order="3" Type="ClaimsExchange">
      <Preconditions>
        <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
          <Value>identityProvider</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="false">
          <Value>identityProvider</Value>
          <Value>linkedin.com</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
      </Preconditions>
      <ClaimsExchanges>
        <ClaimsExchange Id="GetEmail" TechnicalProfileReferenceId="API-LinkedInEmail" />
      </ClaimsExchanges>
    </OrchestrationStep>
    ```

Kaydolma sırasında LinkedIn 'den e-posta adresini almak isteğe bağlıdır. LinkedIn 'den e-postayı edinmeyi, ancak kayıt sırasında bir tane gerektirmeyi seçerseniz, kullanıcının e-posta adresini el ile girmesi ve doğrulaması gerekir.

LinkedIn kimlik sağlayıcısını kullanan bir ilkenin tam bir örneği için bkz. [özel Ilke başlangıç paketi](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/linkedin-identity-provider).

<!-- Links - EXTERNAL -->
[starter-pack]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack

::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="migration-from-v10-to-v20"></a>V 1.0 'dan v 2.0'a geçiş

LinkedIn [, API 'lerini son zamanlarda v 1.0 'dan v 2.0 'a güncelleştirmiştir](https://engineering.linkedin.com/blog/2018/12/developer-program-updates). Geçişin bir parçası olarak, Azure AD B2C yalnızca kaydolma sırasında LinkedIn kullanıcısının tam adını elde edebilir. Kayıt sırasında toplanan özniteliklerden biri e-posta adresi ise, kullanıcının e-posta adresini el ile girmesi ve doğrulaması gerekir.

::: zone-end