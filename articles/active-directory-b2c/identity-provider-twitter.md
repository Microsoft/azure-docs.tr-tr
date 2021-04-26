---
title: Twitter hesabı ile kaydolma ve oturum açma ayarlama
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C kullanarak uygulamalarınızda Twitter hesapları bulunan müşterilere kaydolma ve oturum açma sağlayın.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/06/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 97a8134e858112d7e1deff6744b5555c172692f2
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107028188"
---
# <a name="set-up-sign-up-and-sign-in-with-a-twitter-account-using-azure-active-directory-b2c"></a>Azure Active Directory B2C kullanarak bir Twitter hesabıyla kaydolma ve oturum açma ayarlama

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]
::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-an-application"></a>Uygulama oluşturma

Azure AD B2C bir Twitter hesabı olan kullanıcılar için oturum açmayı etkinleştirmek üzere bir Twitter uygulaması oluşturmanız gerekir. Henüz bir Twitter hesabınız yoksa kaydolabilirsiniz [https://twitter.com/signup](https://twitter.com/signup) . [Geliştirici hesabı için de uygulamanız](https://developer.twitter.com/en/apply/user.html)gerekir. Daha fazla bilgi için bkz. [erişim Için uygulama](https://developer.twitter.com/en/apply-for-access).

1. Twitter hesabı kimlik bilgilerinizle [Twitter Geliştirici Portalında](https://developer.twitter.com/portal/projects-and-apps) oturum açın.
1. **Tek başına uygulamalar** altında **+ uygulama oluştur**' u seçin.
1. Bir **uygulama adı** girin ve ardından **Tamam**' ı seçin.
1. **Uygulama anahtarı** ve **API anahtar gizli** anahtarı değerlerini kopyalayın.  Her ikisini de, kiracınızda bir kimlik sağlayıcısı olarak Twitter yapılandırmak için kullanabilirsiniz. 
1. **Uygulamanızı ayarlama** bölümünde **uygulama ayarları**' nı seçin.
1. **Kimlik doğrulama ayarları** altında **Düzenle** ' yi seçin.
    1. **3-lelenmiş OAuth** onay kutusunu Etkinleştir ' i seçin.
    1. **Kullanıcılardan e-posta adresini iste** onay kutusunu seçin.
    1. **Geri çağırma URL 'leri** için girin `https://your-tenant.b2clogin.com/your-tenant-name.onmicrosoft.com/your-user-flow-Id/oauth1/authresp` .  [Özel bir etki alanı](custom-domain.md)kullanıyorsanız, girin `https://your-domain-name/your-tenant-name.onmicrosoft.com/your-user-flow-Id/oauth1/authresp` . Azure AD B2C büyük harfle tanımlansalar bile kiracı adınızı ve Kullanıcı akış KIMLIĞINIZI girerken tüm küçük harfleri kullanın. Değiştir:
        - `your-tenant-name` , kiracı adınızın adı ile.
        - `your-domain-name` Özel etki alanınız ile.
        - `your-user-flow-Id` Kullanıcı akışlarınızın tanımlayıcısıyla. Örneğin, `b2c_1a_signup_signin_twitter`. 
    
    1. **Web sitesi URL 'si** için girin `https://your-tenant.b2clogin.com` . `your-tenant`Kiracınızın adıyla değiştirin. Örneğin, `https://contosob2c.b2clogin.com`. [Özel bir etki alanı](custom-domain.md)kullanıyorsanız, girin `https://your-domain-name` .
    1. **Hizmet koşulları** IÇIN bir URL girin (örneğin,) `http://www.contoso.com/tos` . İlke URL 'SI, uygulamanız için hüküm ve koşullar sağlamak üzere tuttuğunuz bir sayfasıdır.
    1. **Gizlilik ilkesi** IÇIN bir URL girin, örneğin `http://www.contoso.com/privacy` . İlke URL 'SI, uygulamanız için gizlilik bilgilerini sağlamak üzere tuttuğunuz bir sayfasıdır.
    1. **Kaydet**’i seçin.

::: zone pivot="b2c-user-flow"

## <a name="configure-twitter-as-an-identity-provider"></a>Twitter 'ı bir kimlik sağlayıcısı olarak yapılandırma

1. [Azure portalda](https://portal.azure.com/) Azure AD B2C kiracınızın genel yöneticisi olarak oturum açın.
1. Üst menüdeki **Dizin + abonelik** filtresini seçip kiracınızı içeren dizini seçerek Azure AD B2C kiracınızı içeren dizini kullandığınızdan emin olun.
1. Azure portalın sol üst köşesinde **Tüm hizmetler**’i seçin ve **Azure AD B2C**’yi arayıp seçin.
1. **Kimlik sağlayıcıları**' nı ve ardından **Twitter**' ı seçin.
1. Bir **ad** girin. Örneğin, *Twitter*.
1. **ISTEMCI kimliği** için, daha önce oluşturduğunuz Twitter uygulamasının *API anahtarını* girin.
1. **İstemci parolası** için, kaydettiğiniz *API anahtarı gizli anahtarını* girin.
1. **Kaydet**’i seçin.

## <a name="add-twitter-identity-provider-to-a-user-flow"></a>Kullanıcı akışına Twitter kimlik sağlayıcısı ekleme 

Bu noktada Twitter kimlik sağlayıcısı ayarlanmıştır, ancak oturum açma sayfalarında henüz mevcut değildir. Twitter kimlik sağlayıcısını bir Kullanıcı akışına eklemek için:

1. Azure AD B2C kiracınızda **Kullanıcı akışları**' nı seçin.
1. Twitter kimlik sağlayıcısını eklemek istediğiniz kullanıcı akışını seçin.
1. **Sosyal kimlik sağlayıcıları** altında **Twitter**' ı seçin.
1. **Kaydet**’i seçin.
1. İlkenizi test etmek için **Kullanıcı akışını Çalıştır**' ı seçin.
1. **Uygulama** için, daha önce kaydettiğiniz *testapp1* adlı Web uygulamasını seçin. **Yanıt URL 'si** gösterilmesi gerekir `https://jwt.ms` .
1. **Kullanıcı akışını Çalıştır** düğmesini seçin.
1. Kayıt veya oturum açma sayfasından Twitter hesabıyla oturum açmak için **Twitter** ' ı seçin.

Oturum açma işlemi başarılı olursa, tarayıcınız öğesine yönlendirilir `https://jwt.ms` ve bu, Azure AD B2C tarafından döndürülen belirtecin içeriğini görüntüler.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>İlke anahtarı oluşturma

Daha önce Azure AD B2C kiracınızda kaydettiğiniz gizli anahtarı depolamanız gerekir.

1. [Azure portalında](https://portal.azure.com/) oturum açın.
2. Azure AD B2C kiracınızı içeren dizini kullandığınızdan emin olun. Üstteki menüden **Dizin + abonelik** filtresini seçin ve kiracınızı içeren dizini seçin.
3. Azure portal sol üst köşesindeki **tüm hizmetler** ' i seçin ve ardından **Azure AD B2C**' i arayıp seçin.
4. Genel Bakış sayfasında **kimlik deneyimi çerçevesi**' ni seçin.
5. **Ilke anahtarlarını** seçin ve ardından **Ekle**' yi seçin.
6. **Seçenekler** için öğesini seçin `Manual` .
7. İlke anahtarı için bir **ad** girin. Örneğin, `TwitterSecret`. Ön ek, `B2C_1A_` anahtarınızın adına otomatik olarak eklenir.
8. **Gizli**, daha önce kaydettiğiniz istemci gizli anahtarını girin.
9. **Anahtar kullanımı** için öğesini seçin `Encryption` .
10. **Oluştur**’a tıklayın.

## <a name="configure-twitter-as-an-identity-provider"></a>Twitter 'ı bir kimlik sağlayıcısı olarak yapılandırma

Kullanıcıların bir Twitter hesabı kullanarak oturum açmasını sağlamak için hesabı, Azure AD B2C bir uç nokta aracılığıyla iletişim kurabildiği bir talep sağlayıcı olarak tanımlamanız gerekir. Uç noktası, belirli bir kullanıcının kimliği doğrulandığını doğrulamak için Azure AD B2C tarafından kullanılan bir talep kümesi sağlar.

Bir Twitter hesabını, ilkenizin uzantı dosyasındaki **Claimsproviders** öğesine ekleyerek bir talep sağlayıcı olarak tanımlayabilirsiniz.

1. *TrustFrameworkExtensions.xml* açın.
2. **Claimsproviders** öğesini bulun. Yoksa, kök öğenin altına ekleyin.
3. Yeni bir **ClaimsProvider** 'ı aşağıdaki şekilde ekleyin:

    ```xml
    <ClaimsProvider>
      <Domain>twitter.com</Domain>
      <DisplayName>Twitter</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Twitter-OAuth1">
          <DisplayName>Twitter</DisplayName>
          <Protocol Name="OAuth1" />
          <Metadata>
            <Item Key="ProviderName">Twitter</Item>
            <Item Key="authorization_endpoint">https://api.twitter.com/oauth/authenticate</Item>
            <Item Key="access_token_endpoint">https://api.twitter.com/oauth/access_token</Item>
            <Item Key="request_token_endpoint">https://api.twitter.com/oauth/request_token</Item>
            <Item Key="ClaimsEndpoint">https://api.twitter.com/1.1/account/verify_credentials.json?include_email=true</Item>
            <Item Key="ClaimsResponseFormat">json</Item>
            <Item Key="client_id">Your Twitter application API key</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_TwitterSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="user_id" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="screen_name" />
            <OutputClaim ClaimTypeReferenceId="email" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="twitter.com" />
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

4. **Client_id** değerini, daha önce kaydettiğiniz *API anahtarı sırrı* ile değiştirin.
5. Dosyayı kaydedin.

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="TwitterExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="TwitterExchange" TechnicalProfileReferenceId="Twitter-OAuth1" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>Özel ilkenizi test etme

1. Örneğin, bağlı olan taraf ilkenizi seçin `B2C_1A_signup_signin` .
1. **Uygulama** için, [daha önce kaydetmiş](tutorial-register-applications.md)olduğunuz bir Web uygulamasını seçin. **Yanıt URL 'si** gösterilmesi gerekir `https://jwt.ms` .
1. **Şimdi Çalıştır** düğmesini seçin.
1. Kayıt veya oturum açma sayfasından Twitter hesabıyla oturum açmak için **Twitter** ' ı seçin.

Oturum açma işlemi başarılı olursa, tarayıcınız öğesine yönlendirilir `https://jwt.ms` ve bu, Azure AD B2C tarafından döndürülen belirtecin içeriğini görüntüler.

::: zone-end
