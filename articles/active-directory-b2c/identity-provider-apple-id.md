---
title: Bir Apple KIMLIĞIYLE kaydolma ve oturum açma ayarlama
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C kullanarak uygulamalarınızda Apple KIMLIĞI bulunan müşterilere kaydolma ve oturum açma sağlayın.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/22/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: f5e4ff93b90c7644c1d1498a14c8b9954041cb34
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107028375"
---
# <a name="set-up-sign-up-and-sign-in-with-an-apple-id--using-azure-active-directory-b2c-preview"></a>Azure Active Directory B2C kullanarak bir Apple KIMLIĞIYLE kaydolma ve oturum açma ayarlama (Önizleme)

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-an-apple-id-application"></a>Apple KIMLIĞI uygulaması oluşturma

Azure Active Directory B2C (Azure AD B2C) Apple KIMLIĞI olan kullanıcılar için oturum açmayı etkinleştirmek üzere, içinde bir uygulama oluşturmanız gerekir [https://developer.apple.com](https://developer.apple.com) . Daha fazla bilgi için bkz. [Apple Ile oturum açma](https://developer.apple.com/sign-in-with-apple/get-started/). Zaten bir Apple geliştirici hesabınız yoksa, [Apple geliştirici programı](https://developer.apple.com/programs/enroll/)' na kaydolabilirsiniz.

1. [Apple Geliştirici Portalında](https://developer.apple.com/account) hesap kimlik bilgilerinizle oturum açın.
1. Menüden **Sertifikalar, kimlikler & profiller**' i seçin ve ardından **(+)** öğesini seçin.
1. **Yeni tanımlayıcıyı kaydet** Için **uygulama kimlikleri**' ni seçin ve ardından **devam**' ı seçin.
1. **Tür seçin** için **uygulama**' yı seçin ve ardından **devam**' ı seçin.
1. **Uygulama kimliğini kaydetme** için:
    1. Bir **Açıklama** girin 
    1. **Paket kimliğini** girin, örneğin `com.contoso.azure-ad-b2c` . 
    1. **Yetenekler** için, yetenekler listesinden **Apple ile oturum aç** ' ı seçin. 
    1. Bu adımdan uygulama KIMLIĞI ön ekini (ekip KIMLIĞI) bir yere göz atın. Buna daha sonra ihtiyacınız olacak.
    1. **Devam** ' ı ve ardından **Kaydet**' i seçin.
1. Menüden **Sertifikalar, kimlikler & profiller**' i seçin ve ardından **(+)** öğesini seçin.
1. **Yeni tanımlayıcıyı kaydet** Için, **hizmet kimlikleri**' ni seçin ve ardından **devam**' ı seçin.
1. **Register a SERVICE ID**:
    1. Bir **Açıklama** girin. Açıklama kullanıcıya onay ekranında gösterilir.
    1. **Tanımlayıcıyı** girin, örneğin `com.consoto.azure-ad-b2c-service` . Tanımlayıcı, OpenID Connect Flow için istemci KIMLIĞINIZ olur.
    1. **Devam**' ı seçin ve ardından **Kaydet**' i seçin.
1. **Tanımlayıcılardan** oluşturduğunuz tanımlayıcıyı seçin.
1. **Apple Ile oturum aç**' ı seçin ve ardından **Yapılandır**' ı seçin.
    1. Apple ile oturum açmayı yapılandırmak istediğiniz **birincil uygulama kimliğini** seçin.
    1. **Etki alanları ve alt etki** alanlarında, girin `your-tenant-name.b2clogin.com` . Kiracı adınızı kiracınızın adıyla değiştirin. [Özel bir etki alanı](custom-domain.md)kullanıyorsanız, girin `https://your-domain-name` .
    1. **Dönüş URL 'lerinde**, girin `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` . [Özel bir etki alanı](custom-domain.md)kullanıyorsanız, girin `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp` . `your-tenant-name`, Kiracınızın adıyla ve `your-domain-name` özel etki alanınız ile değiştirin.
    1. **İleri**' yi ve ardından **bitti**' yi seçin.
    1. Açılır pencere kapalıyken **devam**' ı seçin ve ardından **Kaydet**' i seçin.

## <a name="creating-an-apple-client-secret"></a>Apple istemci parolası oluşturma

1. Apple geliştirici portalı menüsünde **anahtarlar**' ı seçin ve ardından **(+)** öğesini seçin.
1. **Yeni anahtarı kaydet** için:
    1. **Anahtar adı** yazın.
    1. **Apple Ile oturum aç**' ı seçin ve ardından **Yapılandır**' ı seçin.
    1. **Birincil uygulama kimliği** için, daha önce oluşturduğunuz uygulamayı ve ardından **Kaydet**' i seçin.
    1. **Yapılandır**' ı seçin ve ardından **Kaydet** ' i seçerek anahtar kayıt işlemini sona erdirin.
1. **Anahtarınızı indirmek** için, anahtarınızı içeren bir. P8 dosyasını Indirmek için **İndir** ' i seçin.


::: zone pivot="b2c-user-flow"

## <a name="configure-apple-as-an-identity-provider"></a>Apple 'ı bir kimlik sağlayıcısı olarak yapılandırma

1. [Azure portal](https://portal.azure.com/) Azure AD B2C kiracınızın genel yöneticisi olarak oturum açın.
1. Üstteki menüden **Dizin + abonelik** filtresini seçin ve Azure AD B2C kiracınızı içeren dizini seçin.
1. **Azure hizmetleri** altında **Azure AD B2C**' yi seçin. İsterseniz arama kutusunu kullanarak **Azure AD B2C** bulun ve seçin.
1. **Kimlik sağlayıcıları**' nı seçin ve ardından **Apple (Önizleme)** öğesini seçin.
1. Bir **ad** girin. Örneğin, *Apple*.
1. **Apple GELIŞTIRICI kimliğini (takım kimliği)** girin.
1. **Apple HIZMET kimliğini (ISTEMCI kimliği)** girin.
1. **Apple anahtar kimliğini** girin.
1. **Apple sertifika verilerini** seçin ve karşıya yükleyin.
1. **Kaydet**’i seçin.


> [!IMPORTANT] 
> - Apple ile oturum açmak, yöneticinin her 6 ayda bir istemci gizliliğini yenilemesini gerektirir. 
> - Bu özelliğin genel önizlemesi sırasında, süresi dolarsa Apple istemci gizli anahtarını el ile yenilemeniz gerekir. Apple Identity Providers 'ın sosyal ıDP 'yi yapılandırma sayfasında bir uyarı görüntülenir, ancak kendi hatırlatmanızı ayarlamanızı öneririz. 
> - Parolayı yenilemeniz gerekiyorsa, Azure Portal Azure AD B2C açın, **kimlik sağlayıcıları**  >  **Apple**' a gidin ve **gizli anahtarı Yenile**' yi seçin.

## <a name="add-the-apple-identity-provider-to-a-user-flow"></a>Apple Identity Provider 'ı bir Kullanıcı akışına ekleme

Kullanıcıların bir Apple KIMLIĞI kullanarak oturum açmasını sağlamak için, Apple Identity Provider 'ı bir Kullanıcı akışına eklemeniz gerekir. Apple ile oturum açma, yalnızca **Önerilen** Kullanıcı akışı sürümü için yapılandırılabilir. Apple kimlik sağlayıcısını bir Kullanıcı akışına eklemek için:

1. Azure AD B2C kiracınızda **Kullanıcı akışları**' nı seçin.
1. Apple Identity Provider 'ı eklemek istediğiniz bir Kullanıcı akışı seçin. 
1. **Sosyal kimlik sağlayıcıları** altında **Apple (Önizleme)** öğesini seçin.
1. **Kaydet**’i seçin.
1. İlkenizi test etmek için **Kullanıcı akışını Çalıştır**' ı seçin.
1. **Uygulama** için, daha önce kaydettiğiniz *testapp1* adlı Web uygulamasını seçin. **Yanıt URL 'si** gösterilmesi gerekir `https://jwt.ms` .
1. **Kullanıcı akışını Çalıştır** düğmesini seçin.
1. Kaydolma veya oturum açma sayfasından Apple KIMLIĞI ile oturum açmak için **Apple** ' ı seçin.

Oturum açma işlemi başarılı olursa, tarayıcınız öğesine yönlendirilir `https://jwt.ms` ve bu, Azure AD B2C tarafından döndürülen belirtecin içeriğini görüntüler.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="signing-the-client-secret"></a>İstemci parolasını imzalama

İstemci gizliliğini bir JWT belirtecine imzalamak için daha önce indirdiğiniz. P8 dosyasını kullanın. Sizin için JWT oluşturup imzalayabilmeniz gereken birçok kitaplık vardır. Sizin için [bir belirteç oluşturan Azure işlevini](https://github.com/azure-ad-b2c/samples/tree/master/policies/sign-in-with-apple/azure-function) kullanın. 

1. [Azure işlevi](../azure-functions/functions-create-function-app-portal.md)oluşturun.
1. **Geliştirici** altında **kod + test**' i seçin. 
1. [Run. CSX](https://github.com/azure-ad-b2c/samples/blob/master/policies/sign-in-with-apple/azure-function/run.csx) dosyasının içeriğini kopyalayın ve düzenleyiciye yapıştırın.
1. **Kaydet**’i seçin.
1. Bir HTTP `POST` isteği oluşturun ve aşağıdaki bilgileri sağlayın:

    - **appleTeamId**: Apple GELIŞTIRICI ekibi kimliğiniz
    - **appleServiceId**: Apple hizmet kimliği (aynı zamanda istemci kimliği)
    - **p8key**: pek biçim anahtarı. Bunu,. P8 dosyasını bir metin düzenleyicisinde açıp `-----BEGIN PRIVATE KEY-----` satır sonları arasındaki ve olmayan her şeyi kopyalayarak elde edebilirsiniz `-----END PRIVATE KEY-----` .
 
Aşağıdaki JSON, Azure işlevine yapılan bir çağrının örneğidir:

```json
{
    "appleTeamId": "ABC123DEFG",
    "appleKeyId": "URKEYID001",
    "appleServiceId": "com.yourcompany.app1",
    "p8key": "MIGTAgEAMBMGByqGSM49AgEGCCqGSM49AwEHBHkwdwIBAQQg+s07NiAcuGEu8rxsJBG7ttupF6FRe3bXdHxEipuyK82gCgYIKoZIzj0DAQehRANCAAQnR1W/KbbaihTQayXH3tuAXA8Aei7u7Ij5OdRy6clOgBeRBPy1miObKYVx3ki1msjjG2uGqRbrc1LvjLHINWRD"
}
```

Azure işlevi, yanıt olarak düzgün şekilde biçimlendirilen ve imzalı bir istemci gizli JWT ile yanıt verir, örneğin:

```json
{
    "token": "eyJhbGciOiJFUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiJjb20ueW91cmNvbXBhbnkuYXBwMSIsIm5iZiI6MTU2MDI2OTY3NSwiZXhwIjoxNTYwMzU2MDc1LCJpc3MiOiJBQkMxMjNERUZHIiwiYXVkIjoiaHR0cHM6Ly9hcHBsZWlkLmFwcGxlLmNvbSJ9.Dt9qA9NmJ_mk6tOqbsuTmfBrQLFqc9BnSVKR6A-bf9TcTft2XmhWaVODr7Q9w1PP3QOYShFXAnNql5OdNebB4g"
}
```

## <a name="create-a-policy-key"></a>İlke anahtarı oluşturma

Daha önce Azure AD B2C kiracınızda kaydettiğiniz istemci gizli anahtarını depolamanız gerekir.

1. [Azure portalında](https://portal.azure.com/) oturum açın.
1. Üstteki menüden **Dizin + abonelik** filtresini seçin ve Azure AD B2C kiracınızı içeren dizini seçin.
2. **Azure hizmetleri** altında **Azure AD B2C**' yi seçin. İsterseniz arama kutusunu kullanarak **Azure AD B2C** bulun ve seçin.
1. **Genel bakış** sayfasında **kimlik deneyimi çerçevesi**' ni seçin.
1. **Ilke anahtarlarını** seçin ve ardından **Ekle**' yi seçin.
1. **Seçenekler** Için **el ile**' yi seçin.
1. İlke anahtarı için bir **ad** girin. Örneğin, "AppleSecret". "B2C_1A_" ön eki, anahtarınızın adına otomatik olarak eklenir.
1. **Gizli** dizi ' da, Azure işlevi tarafından döndürülen belirtecin değerini girin (bir JWT belirteci).
1. **Anahtar kullanımı** için **imza**' yı seçin.
1. **Oluştur**’u seçin.

> [!IMPORTANT] 
> - Apple ile oturum açmak, yöneticinin her 6 ayda bir istemci gizliliğini yenilemesini gerektirir.
> - Süresi dolmuşsa Apple istemci gizli anahtarını el ile yenilemeniz ve yeni değeri ilke anahtarına depolamanız gerekir.
> - Yeni bir istemci parolası oluşturmak için 6 ay içinde kendi anımsatıcısını ayarlamanızı öneririz. 

## <a name="configure-apple-as-an-identity-provider"></a>Apple 'ı bir kimlik sağlayıcısı olarak yapılandırma

Kullanıcıların bir Apple KIMLIĞI kullanarak oturum açmasını sağlamak için hesabı, Azure AD B2C bir uç nokta aracılığıyla iletişim kurabildiği bir talep sağlayıcı olarak tanımlamanız gerekir. Uç noktası, belirli bir kullanıcının kimliğinin doğrulandığını doğrulamak için Azure AD B2C tarafından kullanılan bir talep kümesi sağlar.

Bir Apple KIMLIĞINI, ilkenizin uzantı dosyasındaki **Claimsproviders** öğesine ekleyerek bir talep sağlayıcı olarak tanımlayabilirsiniz.

1. *TrustFrameworkExtensions.xml* açın.
2. **Claimsproviders** öğesini bulun. Yoksa, kök öğenin altına ekleyin.
3. Yeni bir **ClaimsProvider** 'ı aşağıdaki şekilde ekleyin:

    ```xml
    <ClaimsProvider>
      <Domain>apple.com</Domain>
      <DisplayName>Apple</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Apple-OIDC">
          <DisplayName>Apple</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <Item Key="ProviderName">apple</Item>
            <Item Key="authorization_endpoint">https://appleid.apple.com/auth/authorize</Item>
            <Item Key="AccessTokenEndpoint">https://appleid.apple.com/auth/token</Item>
            <Item Key="JWKS">https://appleid.apple.com/auth/keys</Item>
            <Item Key="issuer">https://appleid.apple.com</Item>
            <Item Key="scope">name email openid</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="response_types">code</Item>
            <Item Key="external_user_identity_claim_id">sub</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="ReadBodyClaimsOnIdpRedirect">user.name.firstName user.name.lastName user.email</Item>
            <Item Key="client_id">You Apple ID</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_AppleSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="https://appleid.apple.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="user.name.firstName"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="user.name.lastName"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="user.email"/>
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. Hizmet tanımlayıcısına **client_id** ayarlayın. Örneğin, `com.consoto.azure-ad-b2c-service`.
5. Dosyayı kaydedin.

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="AppleExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="AppleExchange" TechnicalProfileReferenceId="Apple-OIDC" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>Özel ilkenizi test etme

1. Örneğin, bağlı olan taraf ilkenizi seçin `B2C_1A_signup_signin` .
1. **Uygulama** için, [daha önce kaydetmiş](tutorial-register-applications.md)olduğunuz bir Web uygulamasını seçin. **Yanıt URL 'si** gösterilmesi gerekir `https://jwt.ms` .
1. **Şimdi Çalıştır** düğmesini seçin.
1. Kaydolma veya oturum açma sayfasından Apple KIMLIĞI ile oturum açmak için **Apple** ' ı seçin.

Oturum açma işlemi başarılı olursa, tarayıcınız öğesine yönlendirilir `https://jwt.ms` ve bu, Azure AD B2C tarafından döndürülen belirtecin içeriğini görüntüler.

::: zone-end
