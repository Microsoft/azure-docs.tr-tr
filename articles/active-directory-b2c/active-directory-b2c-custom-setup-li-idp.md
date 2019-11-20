---
title: Özel ilkeler kullanarak LinkedIn hesabı ile oturum açma ayarlama-Azure Active Directory B2C
description: Özel ilkeler kullanarak Azure Active Directory B2C LinkedIn hesabıyla oturum açma ayarlayın.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 6d7beae75e13ef797c9e80a9a012e9f48b69d407
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827190"
---
# <a name="set-up-sign-in-with-a-linkedin-account-using-custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C özel ilkeler kullanarak LinkedIn hesabı ile oturum açmayı ayarlama

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Bu makalede, Azure Active Directory B2C (Azure AD B2C) ' de [özel ilkeler](active-directory-b2c-overview-custom.md) kullanarak LinkedIn hesabından kullanıcılar için oturum açma özelliğini nasıl etkinleştireceğinizi gösterilmektedir.

## <a name="prerequisites"></a>Önkoşullar

- [Azure Active Directory B2C özel ilkeleri kullanmaya başlama](active-directory-b2c-get-started-custom.md)bölümündeki adımları uygulayın.
- LinkedIn hesabı-henüz yoksa [bir hesap oluşturun](https://www.linkedin.com/start/join).
- LinkedIn sayfası-sonraki bölümde oluşturduğunuz LinkedIn uygulamasıyla ilişkilendirmek için bir [LinkedIn sayfasına](https://www.linkedin.com/help/linkedin/answer/710/creating-a-linkedin-company-page) ihtiyacınız vardır.

## <a name="create-an-application"></a>Uygulama oluşturma

LinkedIn 'i Azure AD B2C bir kimlik sağlayıcısı olarak kullanmak için bir LinkedIn uygulaması oluşturmanız gerekir.

### <a name="create-app"></a>Uygulama oluştur

1. LinkedIn hesabı kimlik bilgilerinizle [LinkedIn uygulama yönetimi](https://www.linkedin.com/secure/developer?newapp=) Web sitesinde oturum açın.
1. **Uygulama oluştur**' u seçin.
1. Bir **uygulama adı**girin.
1. LinkedIn sayfa adına karşılık gelen bir **Şirket** adı girin. Henüz yoksa bir LinkedIn sayfası oluşturun.
1. Seçim **Gizlilik ilkesi URL 'si**girin. Geçerli bir URL olmalıdır, ancak ulaşılabilir bir uç nokta olması gerekmez.
1. Bir **iş e-postası**girin.
1. **Uygulama logosu** görüntüsünü karşıya yükleyin. Logo resminin kare olması ve boyutlarının en az 100x100 piksel olması gerekir.
1. **Ürünler** bölümünde varsayılan ayarları bırakın.
1. **Yasal koşullarda**sunulan bilgileri gözden geçirin. Koşulları kabul ediyorsanız, kutuyu işaretleyin.
1. **Uygulama oluştur**' u seçin.

### <a name="configure-auth"></a>Kimlik doğrulamasını yapılandırma

1. **AUTH** sekmesini seçin.
1. **ISTEMCI kimliğini**kaydedin.
1. **Istemci gizliliğini**açığa çıkar ve kaydedin.
1. **OAuth 2,0 ayarları**altında aşağıdaki **yeniden yönlendirme URL 'sini**ekleyin. @No__t-0 değerini kiracınızın adıyla değiştirin. Azure AD B2C büyük harfle tanımlansa bile kiracı adı için **tüm küçük harfleri** kullanın.

    `https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/oauth2/authresp`

## <a name="create-a-policy-key"></a>İlke anahtarı oluşturma

Daha önce Azure AD B2C kiracınızda kaydettiğiniz istemci gizli anahtarını depolamanız gerekir.

1. [Azure Portal](https://portal.azure.com/)’ında oturum açın.
2. Azure AD B2C kiracınızı içeren dizini kullandığınızdan emin olun. Üstteki menüden **Dizin + abonelik** filtresini seçin ve kiracınızı içeren dizini seçin.
3. Azure portal sol üst köşesindeki **tüm hizmetler** ' i seçin ve ardından **Azure AD B2C**' i arayıp seçin.
4. Genel Bakış sayfasında **kimlik deneyimi çerçevesi**' ni seçin.
5. **İlke anahtarlarını** seçin ve ardından **Ekle**' yi seçin.
6. **Seçenekler**için `Manual` ' i seçin.
7. İlke anahtarı için bir **ad** girin. Örneğin, `LinkedInSecret`. *B2C_1A_* ön eki, anahtarınızın adına otomatik olarak eklenir.
8. **Gizli**, daha önce kaydettiğiniz istemci gizli anahtarını girin.
9. **Anahtar kullanımı**için `Signature` ' i seçin.
10. **Oluştur**’a tıklayın.

## <a name="add-a-claims-provider"></a>Talep sağlayıcısı ekleme

Kullanıcıların bir LinkedIn hesabı kullanarak oturum açmasını istiyorsanız hesabı, Azure AD B2C bir uç nokta aracılığıyla iletişim kurabildiği bir talep sağlayıcı olarak tanımlamanız gerekir. Uç noktası, belirli bir kullanıcının kimliği doğrulandığını doğrulamak için Azure AD B2C tarafından kullanılan bir talep kümesi sağlar.

Bir LinkedIn hesabını, ilkenizin uzantısı dosyasındaki **Claimsproviders** öğesine ekleyerek bir talep sağlayıcı olarak tanımlayın.

1. Düzenleyicinizde *SocialAndLocalAccounts/* * TrustFrameworkExtensions. xml** * dosyasını açın. Bu dosya, önkoşullardan birinin bir parçası olarak indirdiğiniz [özel ilke başlangıç paketidir][starter-pack] .
1. **Claimsproviders** öğesini bulun. Yoksa, kök öğenin altına ekleyin.
1. Yeni bir **ClaimsProvider** 'ı aşağıdaki şekilde ekleyin:

    ```xml
    <ClaimsProvider>
      <Domain>linkedin.com</Domain>
      <DisplayName>LinkedIn</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="LinkedIn-OAUTH">
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
            <Item Key="UsePolicyInRedirectUri">0</Item>
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

LinkedIn Technical profile, **ExtractGivenNameFromLinkedInResponse** ve **ExtractSurNameFromLinkedInResponse** talep dönüştürmelerinin claimstransformaler listesine eklenmesini gerektirir. Dosyanızda tanımlanmış bir **Claimstransformations** öğesi yoksa, üst XML öğelerini aşağıda gösterildiği gibi ekleyin. Talep dönüştürmelerinin Ayrıca **Nullstringclaim**adlı yeni bir talep türü tanımlanmış olması gerekir.

*TrustFrameworkExtensions. xml* dosyasının en üstüne yakın olan **buildingblocks** öğesini ekleyin. Bir örnek için bkz. *TrustFrameworkBase. xml* .

```XML
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

### <a name="upload-the-extension-file-for-verification"></a>Uzantı dosyasını doğrulama için karşıya yükle

Artık Azure AD B2C LinkedIn hesabınızla nasıl iletişim kuracağını bilmesi için bir ilke yapılandırdınız. Şimdiye kadar herhangi bir sorun olmadığını doğrulamak için ilkenizin uzantı dosyasını karşıya yüklemeyi deneyin.

1. Azure AD B2C kiracınızdaki **özel ilkeler** sayfasında, **ilkeyi karşıya yükle**' yi seçin.
2. Varsa **Ilkenin üzerine yazmayı**etkinleştirin ve ardından *TrustFrameworkExtensions. xml* dosyasına gidip seçin.
3. **Karşıya Yükle**'ye tıklayın.

## <a name="register-the-claims-provider"></a>Talep sağlayıcısını Kaydet

Bu noktada, kimlik sağlayıcısı ayarlanmıştır, ancak kaydolma veya oturum açma ekranlarından hiçbirinde kullanılamaz. Kullanılabilir hale getirmek için, var olan bir şablon Kullanıcı yolculuğunun bir yinelemesini oluşturun ve ardından LinkedIn kimlik sağlayıcısı 'nı da içerecek şekilde değiştirin.

1. Starter paketindeki *TrustFrameworkBase. xml* dosyasını açın.
2. @No__t-1 içeren **Useryolculuney** öğesinin tüm içeriğini bulup kopyalayın.
3. *TrustFrameworkExtensions. xml* ' i açın ve **User, neys** öğesini bulun. Öğe yoksa, bir tane ekleyin.
4. **User, neys** öğesinin bir alt öğesi olarak kopyaladığınız **User, ney** öğesinin tüm içeriğini yapıştırın.
5. Kullanıcı yolculuğunun KIMLIĞINI yeniden adlandırın. Örneğin, `SignUpSignInLinkedIn`.

### <a name="display-the-button"></a>Düğmeyi görüntüleme

**Claimsproviderselection** öğesi, kaydolma veya oturum açma ekranındaki bir kimlik sağlayıcısı düğmesine benzer. LinkedIn hesabı için bir **Claimsproviderselection** öğesi eklerseniz, bir Kullanıcı sayfada yer alıyorsa yeni bir düğme görüntülenir.

1. Oluşturduğunuz Kullanıcı yolculuğunda `Order="1"` içeren **Orchestrationstep** öğesini bulun.
2. **Claimsproviderseçimleri**altında aşağıdaki öğeyi ekleyin. **Targetclaimsexchangeıd** değerini uygun bir değere ayarlayın, örneğin `LinkedInExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Düğmeyi bir eyleme bağlama

Artık bir düğmeye sahip olduğunuza göre, bunu bir eyleme bağlamanız gerekir. Bu durumda, Azure AD B2C bir, belirteç almak üzere bir LinkedIn hesabıyla iletişim kurmak için kullanılır.

1. Kullanıcı yolculuğunda `Order="2"` içeren bir düzenleyen **Tionstep** bulun.
2. **Targetclaimsexchangeıd**IÇIN kullandığınız kimlik için aynı değeri kullandığınızdan emin olmak Için aşağıdaki **claimsexchange** öğesini ekleyin:

    ```XML
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAUTH" />
    ```

    **TechnicalProfileReferenceId** değerini daha önce oluşturduğunuz teknık profilin kimliğiyle güncelleştirin. Örneğin, `LinkedIn-OAUTH`.

3. *TrustFrameworkExtensions. xml* dosyasını kaydedin ve doğrulama için yeniden yükleyin.

## <a name="create-an-azure-ad-b2c-application"></a>Azure AD B2C uygulaması oluşturma

Azure AD B2C ile iletişim, B2C kiracınıza kaydolmanızı sağlayan bir uygulama aracılığıyla oluşur. Bu bölümde, daha önce yapmadıysanız bir test uygulaması oluşturmak için tamamlayabildiğiniz isteğe bağlı adımlar listelenmektedir.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>Bağlı olan taraf dosyasını güncelleştirme ve test etme

Oluşturduğunuz Kullanıcı yolculuğunu başlatan bağlı olan taraf (RP) dosyasını güncelleştirin.

1. Çalışma dizininizde *Signuporsignın. xml* ' in bir kopyasını oluşturun ve yeniden adlandırın. Örneğin, bunu *Signupsignınlinkedın. xml*olarak yeniden adlandırın.
2. Yeni dosyayı açın ve **TrustFrameworkPolicy** Için **PolicyId** özniteliğinin değerini benzersiz bir değerle güncelleştirin. Örneğin, `SignUpSignInLinkedIn`.
3. **Publicpolicyuri** DEĞERINI ilke URI 'siyle güncelleştirin. Örneğin, `http://contoso.com/B2C_1A_signup_signin_linkedin`
4. **Defaultuseryolculuney** Içindeki **referenceıd** özniteliğinin değerini, oluşturduğunuz yeni Kullanıcı yolculuğu (signupsignlinkedın) kimliğiyle eşleşecek şekilde güncelleştirin.
5. Değişikliklerinizi kaydedin, dosyayı karşıya yükleyin ve ardından listeden yeni ilkeyi seçin.
6. Oluşturduğunuz Azure AD B2C uygulamasının **Uygulama Seç** alanında seçildiğinden emin olun ve **Şimdi Çalıştır**' a tıklayarak test edin.

## <a name="migration-from-v10-to-v20"></a>V 1.0 'dan v 2.0'a geçiş

LinkedIn [, API 'lerini son zamanlarda v 1.0 'dan v 2.0 'a güncelleştirmiştir](https://engineering.linkedin.com/blog/2018/12/developer-program-updates). Mevcut yapılandırmanızı yeni yapılandırmaya geçirmek için, teknik profildeki öğeleri güncelleştirmek üzere aşağıdaki bölümlerdeki bilgileri kullanın.

### <a name="replace-items-in-the-metadata"></a>Meta verilerdeki öğeleri değiştir

**Teknisyen**'in var olan **meta veri** öğesinde aşağıdaki **öğe** öğelerini ' den güncelleştirin:

```XML
<Item Key="ClaimsEndpoint">https://api.linkedin.com/v1/people/~:(id,first-name,last-name,email-address,headline)</Item>
<Item Key="scope">r_emailaddress r_basicprofile</Item>
```

Hedef:

```XML
<Item Key="ClaimsEndpoint">https://api.linkedin.com/v2/me</Item>
<Item Key="scope">r_emailaddress r_liteprofile</Item>
```

### <a name="add-items-to-the-metadata"></a>Meta verilere öğe ekleme

**Teknisyen**'In **meta verilerinde** aşağıdaki **öğe** öğelerini ekleyin:

```XML
<Item Key="external_user_identity_claim_id">id</Item>
<Item Key="BearerTokenTransmissionMethod">AuthorizationHeader</Item>
<Item Key="ResolveJsonPathsInJsonTokens">true</Item>
```

### <a name="update-the-outputclaims"></a>Outputclaim 'yi güncelleştirme

**Teknisyen**'In mevcut **outputclaim** öğelerinde aşağıdaki **outputclaim** öğelerini güncelleştirin:

```XML
<OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
<OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
```

Hedef:

```XML
<OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName.localized" />
<OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName.localized" />
```

### <a name="add-new-outputclaimstransformation-elements"></a>Yeni Outputclaimstrans, öğeleri ekle

**Teknisyen**tarafından belirlenen **Outputclaimsformations** öğelerini aşağıdaki **outputclaimstranssize** öğelerini ekleyin:

```XML
<OutputClaimsTransformation ReferenceId="ExtractGivenNameFromLinkedInResponse" />
<OutputClaimsTransformation ReferenceId="ExtractSurNameFromLinkedInResponse" />
```

### <a name="define-the-new-claims-transformations-and-claim-type"></a>Yeni talep dönüştürmelerini ve talep türünü tanımlayın

Son adımda, tanımlanması gereken yeni talep dönüşümleri eklediniz. Talep dönüşümlerini tanımlamak için bunları **Claimstransformations**listesine ekleyin. Dosyanızda tanımlanmış bir **Claimstransformations** öğesi yoksa, üst XML öğelerini aşağıda gösterildiği gibi ekleyin. Talep dönüştürmelerinin Ayrıca **Nullstringclaim**adlı yeni bir talep türü tanımlanmış olması gerekir.

**Buildingblocks** öğesi dosyanın en üstüne eklenmelidir. Örnek olarak *TrustframeworkBase. xml* ' ye bakın.

```XML
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
2. LinkedIn erişim belirtecini bir talebe kaydedin. [Buradaki yönergelere bakın](idp-pass-through-custom.md).
3. @No__t-0 API 'sine istek yapan aşağıdaki talep sağlayıcısını ekleyin. Bu isteği yetkilendirmek için LinkedIn erişim belirtecine ihtiyacınız vardır.

    ```XML
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

4. Kullanıcı yolculuğuna aşağıdaki düzenleme adımını ekleyin, böylece Kullanıcı LinkedIn kullanarak oturum açtığında API talep sağlayıcısı tetiklenir. @No__t-0 sayısını uygun şekilde güncelleştirdiğinizden emin olun. Bu adımı, LinkedIn teknik profilini tetikleyen düzenleme adımından hemen sonra ekleyin.

    ```XML
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
