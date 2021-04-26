---
title: Bir Google hesabı ile kaydolma ve oturum açma ayarlama
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C kullanarak uygulamalarınızda Google hesabı bulunan müşterilere kaydolma ve oturum açma sağlayın.
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
ms.openlocfilehash: e4dee196d3ff0796802d2552f073446ad6912663
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107028273"
---
# <a name="set-up-sign-up-and-sign-in-with-a-google-account-using-azure-active-directory-b2c"></a>Azure Active Directory B2C kullanarak bir Google hesabı ile kaydolma ve oturum açma ayarlama

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


## <a name="create-a-google-application"></a>Google uygulaması oluşturma

Azure Active Directory B2C (Azure AD B2C) içinde Google hesabı olan kullanıcılar için oturum açmayı etkinleştirmek üzere [Google geliştiricileri konsolu](https://console.developers.google.com/)'nda bir uygulama oluşturmanız gerekir. Daha fazla bilgi için bkz. [OAuth 2,0 'Yi ayarlama](https://support.google.com/googleapi/answer/6158849). Henüz bir Google hesabınız yoksa kaydolabilirsiniz [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp) .

1. Google [Developers konsolunda](https://console.developers.google.com/) Google hesabı kimlik bilgilerinizle oturum açın.
1. Sayfanın sol üst köşesinde proje listesini seçin ve ardından **Yeni proje**' yi seçin.
1. Bir **Proje adı** girin, **Oluştur**' u seçin.
1. Ekranın sol üst kısmındaki proje açılır listesini seçerek yeni projeyi kullandığınızdan emin olun. Projeyi ada göre seçip **Aç**' ı seçin.
1. Sol taraftaki menüden **OAuth onay ekranını** seçin, **dış**' i seçin ve ardından **Oluştur**' u seçin.
Uygulamanız için bir **ad** girin. **Yetkili etki alanları** bölümüne *B2clogin.com* girin ve **Kaydet**' i seçin.
1. Sol menüden **kimlik** bilgileri ' ni seçin ve **kimlik bilgileri oluştur**  >  **OAuth istemci kimliği**' ni seçin.
1. **Uygulama türü** altında **Web uygulaması**' nı seçin.
    1. Uygulamanız için bir **ad** girin.
    1. **Yetkilendirilmiş JavaScript kaynakları** için girin `https://your-tenant-name.b2clogin.com` . [Özel bir etki alanı](custom-domain.md)kullanıyorsanız, girin `https://your-domain-name` .
    1. **Yetkili yeniden yönlendirme URI 'leri** için girin `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` . [Özel bir etki alanı](custom-domain.md)kullanıyorsanız, girin `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp` . `your-domain-name`Özel etki alanınız ile ve `your-tenant-name` kiracınızın adıyla değiştirin. Kiracı, Azure AD B2C büyük harfle tanımlansa bile kiracı adınızı girerken tüm küçük harfleri kullanın.
1. **Oluştur**’a tıklayın.
1. **ISTEMCI kimliği** ve **istemci parolası** değerlerini kopyalayın. Google 'ı kiracınızda bir kimlik sağlayıcısı olarak yapılandırmak için her ikisine de ihtiyacınız olacak. **İstemci parolası** önemli bir güvenlik kimlik bilgileridir.

::: zone pivot="b2c-user-flow"

## <a name="configure-google-as-an-identity-provider"></a>Google 'ı bir kimlik sağlayıcısı olarak yapılandırma

1. [Azure portalda](https://portal.azure.com/) Azure AD B2C kiracınızın genel yöneticisi olarak oturum açın.
1. Üst menüdeki **Dizin + abonelik** filtresini seçip kiracınızı içeren dizini seçerek Azure AD B2C kiracınızı içeren dizini kullandığınızdan emin olun.
1. Azure portalın sol üst köşesinde **Tüm hizmetler**’i seçin ve **Azure AD B2C**’yi arayıp seçin.
1. **Kimlik sağlayıcıları**' nı seçin ve ardından **Google**' ı seçin.
1. Bir **ad** girin. Örneğin, *Google*.
1. **ISTEMCI kimliği** için, daha önce oluşturduğunuz Google UYGULAMASıNıN istemci kimliğini girin.
1. **İstemci parolası** için, kaydettiğiniz istemci gizli anahtarını girin.
1. **Kaydet**’i seçin.

## <a name="add-google-identity-provider-to-a-user-flow"></a>Kullanıcı akışına Google Identity sağlayıcısı ekleme 

Bu noktada, Google Identity sağlayıcısı ayarlanmıştır, ancak oturum açma sayfalarında henüz mevcut değildir. Google Identity sağlayıcısını bir Kullanıcı akışına eklemek için:


1. Azure AD B2C kiracınızda **Kullanıcı akışları**' nı seçin.
1. Google Identity sağlayıcısını eklemek istediğiniz kullanıcı akışına tıklayın.
1. **Sosyal kimlik sağlayıcıları** altında **Google**' ı seçin.
1. **Kaydet**’i seçin.
1. İlkenizi test etmek için **Kullanıcı akışını Çalıştır**' ı seçin.
1. **Uygulama** için, daha önce kaydettiğiniz *testapp1* adlı Web uygulamasını seçin. **Yanıt URL 'si** gösterilmesi gerekir `https://jwt.ms` .
1. **Kullanıcı akışını Çalıştır** düğmesini seçin.
1. Kaydolma veya oturum açma **sayfasından Google hesabı ' nı seçerek Google** hesabıyla oturum açın.

Oturum açma işlemi başarılı olursa, tarayıcınız öğesine yönlendirilir `https://jwt.ms` ve bu, Azure AD B2C tarafından döndürülen belirtecin içeriğini görüntüler.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>İlke anahtarı oluşturma

Daha önce Azure AD B2C kiracınızda kaydettiğiniz istemci gizli anahtarını depolamanız gerekir.

1. [Azure portalında](https://portal.azure.com/) oturum açın.
2. Azure AD B2C kiracınızı içeren dizini kullandığınızdan emin olun. Üstteki menüden **Dizin + abonelik** filtresini seçin ve kiracınızı içeren dizini seçin.
3. Azure portal sol üst köşesindeki **tüm hizmetler** ' i seçin ve ardından **Azure AD B2C**' i arayıp seçin.
4. Genel Bakış sayfasında **kimlik deneyimi çerçevesi**' ni seçin.
5. **Ilke anahtarlarını** seçin ve ardından **Ekle**' yi seçin.
6. **Seçenekler** için öğesini seçin `Manual` .
7. İlke anahtarı için bir **ad** girin. Örneğin, `GoogleSecret`. Ön ek, `B2C_1A_` anahtarınızın adına otomatik olarak eklenir.
8. **Gizli**, daha önce kaydettiğiniz istemci gizli anahtarını girin.
9. **Anahtar kullanımı** için öğesini seçin `Signature` .
10. **Oluştur**’a tıklayın.

## <a name="configure-google-as-an-identity-provider"></a>Google 'ı bir kimlik sağlayıcısı olarak yapılandırma

Kullanıcıların bir Google hesabı kullanarak oturum açmasını sağlamak için hesabı, Azure AD B2C bir uç nokta aracılığıyla iletişim kurabildiği bir talep sağlayıcı olarak tanımlamanız gerekir. Uç noktası, belirli bir kullanıcının kimliği doğrulandığını doğrulamak için Azure AD B2C tarafından kullanılan bir talep kümesi sağlar.

Bir Google hesabını, ilkenizin uzantı dosyasındaki **Claimsproviders** öğesine ekleyerek bir talep sağlayıcı olarak tanımlayabilirsiniz.

1. *TrustFrameworkExtensions.xml* açın.
2. **Claimsproviders** öğesini bulun. Yoksa, kök öğenin altına ekleyin.
3. Yeni bir **ClaimsProvider** 'ı aşağıdaki şekilde ekleyin:

    ```xml
    <ClaimsProvider>
      <Domain>google.com</Domain>
      <DisplayName>Google</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Google-OAuth2">
          <DisplayName>Google</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">google</Item>
            <Item Key="authorization_endpoint">https://accounts.google.com/o/oauth2/auth</Item>
            <Item Key="AccessTokenEndpoint">https://accounts.google.com/o/oauth2/token</Item>
            <Item Key="ClaimsEndpoint">https://www.googleapis.com/oauth2/v1/userinfo</Item>
            <Item Key="scope">email profile</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="client_id">Your Google application ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_GoogleSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="id" />
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="google.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
          </OutputClaims>
          <OutputClaimsTransformations>
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

4. Uygulama kaydından uygulama KIMLIĞINE **client_id** ayarlayın.
5. Dosyayı kaydedin.

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="GoogleExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAuth2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>Özel ilkenizi test etme

1. Örneğin, bağlı olan taraf ilkenizi seçin `B2C_1A_signup_signin` .
1. **Uygulama** için, [daha önce kaydetmiş](tutorial-register-applications.md)olduğunuz bir Web uygulamasını seçin. **Yanıt URL 'si** gösterilmesi gerekir `https://jwt.ms` .
1. **Şimdi Çalıştır** düğmesini seçin.
1. Kaydolma veya oturum açma **sayfasından Google hesabı ' nı seçerek Google** hesabıyla oturum açın.

Oturum açma işlemi başarılı olursa, tarayıcınız öğesine yönlendirilir `https://jwt.ms` ve bu, Azure AD B2C tarafından döndürülen belirtecin içeriğini görüntüler.

::: zone-end

## <a name="next-steps"></a>Sonraki adımlar

[Google belirtecini uygulamanıza nasıl geçirebileceğinizi](idp-pass-through-user-flow.md)öğrenin.