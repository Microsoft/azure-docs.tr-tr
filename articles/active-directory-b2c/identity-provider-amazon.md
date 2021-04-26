---
title: Bir Amazon hesabı ile kaydolma ve oturum açma ayarlama
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C kullanarak uygulamalarınızda Amazon hesaplarına sahip olan ve müşterilere kaydolma ve oturum açma sağlayın.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.custom: project-no-code
ms.date: 03/17/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: b6c0d9d5430d84006b208c50e78b8d875c95b8ac
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107028392"
---
# <a name="set-up-sign-up-and-sign-in-with-an-amazon-account-using-azure-active-directory-b2c"></a>Azure Active Directory B2C kullanarak bir Amazon hesabı ile kaydolma ve oturum açma ayarlama

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-an-app-in-the-amazon-developer-console"></a>Amazon geliştirici konsolunda uygulama oluşturma

Azure Active Directory B2C (Azure AD B2C) ' de bir Amazon hesabı olan kullanıcılar için oturum açma 'yı etkinleştirmek üzere, [Amazon Geliştirici Hizmetleri ve teknolojilerinde](https://developer.amazon.com)bir uygulama oluşturmanız gerekir. Daha fazla bilgi için bkz. [Amazon Ile oturum açma Için kaydolma](https://developer.amazon.com/docs/login-with-amazon/register-web.html). Zaten bir Amazon hesabınız yoksa kaydolabilirsiniz [https://www.amazon.com/](https://www.amazon.com/) .

1. Amazon hesabı kimlik bilgilerinizle [Amazon Developer konsolunda](https://developer.amazon.com/dashboard) oturum açın.
1. Daha önce yapmadıysanız **kaydolun**' ı seçin, geliştirici kayıt adımlarını izleyin ve ardından ilkeyi kabul edin.
1. Panodan, **Amazon Ile oturum aç**' ı seçin.
1. **Yeni bir güvenlik profili oluştur**' u seçin.
1. Bir **güvenlik profili adı**, **güvenlik profili açıklaması** ve **onay gizlilik bildirimi URL 'si** girin, örneğin `https://www.contoso.com/privacy` Gizlilik bildirimi URL 'si, kullanıcılara gizlilik bilgileri sağlayan bir sayfasıdır. Daha sonra **Kaydet**'e tıklayın.
1. **Amazon Configurations Ile oturum aç** bölümünde, oluşturduğunuz **güvenlik profili adını** seçin, **Yönet** simgesini seçin ve ardından **Web ayarları**' nı seçin.
1. **Web ayarları** bölümünde, **istemci kimliği** değerlerini kopyalayın. İstemci parolasını almak için **gizli göster** ' i seçin ve sonra kopyalayın. Bir Amazon hesabını kiracınızda kimlik sağlayıcısı olarak yapılandırmak için her iki değere de ihtiyacınız vardır. **Istemci parolası** önemli bir güvenlik kimlik bilgileridir.
1. **Web ayarları** bölümünde **Düzenle**' yi seçin. 
    1. **Izin verilen çıkış noktaları**' nda, girin `https://your-tenant-name.b2clogin.com` . `your-tenant-name`Kiracınızın adıyla değiştirin. [Özel bir etki alanı](custom-domain.md)kullanıyorsanız, girin `https://your-domain-name` .
    1.  **Izin verilen dönüş URL 'leri** girin `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` .  [Özel bir etki alanı](custom-domain.md)kullanıyorsanız, girin `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp` .  `your-tenant-name`, Kiracınızın adıyla ve `your-domain-name` özel etki alanınız ile değiştirin.
1. **Kaydet**’i seçin.

::: zone pivot="b2c-user-flow"

## <a name="configure-amazon-as-an-identity-provider"></a>Amazon 'ı bir kimlik sağlayıcısı olarak yapılandırma

1. [Azure portalda](https://portal.azure.com/) Azure AD B2C kiracınızın genel yöneticisi olarak oturum açın.
1. Üst menüdeki **Dizin + abonelik** filtresini seçip kiracınızı içeren dizini seçerek Azure AD B2C kiracınızı içeren dizini kullandığınızdan emin olun.
1. Azure portalın sol üst köşesinde **Tüm hizmetler**’i seçin ve **Azure AD B2C**’yi arayıp seçin.
1. **Kimlik sağlayıcıları**' nı seçin ve ardından **Amazon**' ı seçin.
1. Bir **ad** girin. Örneğin, *Amazon*.
1. **ISTEMCI kimliği** için, daha önce oluşturduğunuz Amazon UYGULAMASıNıN istemci kimliğini girin.
1. **İstemci parolası** için, kaydettiğiniz istemci gizli anahtarını girin.
1. **Kaydet**’i seçin.

## <a name="add-amazon-identity-provider-to-a-user-flow"></a>Bir Kullanıcı akışına Amazon Identity sağlayıcısı ekleme 

Bu noktada, Amazon Identity sağlayıcısı ayarlanmıştır, ancak oturum açma sayfalarında henüz mevcut değildir. Amazon Identity sağlayıcısını bir Kullanıcı akışına eklemek için:

1. Azure AD B2C kiracınızda **Kullanıcı akışları**' nı seçin.
1. Amazon Identity sağlayıcısını eklemek istediğiniz kullanıcı akışına tıklayın.
1. **Sosyal kimlik sağlayıcıları** altında **Amazon**' ı seçin.
1. **Kaydet**’i seçin.
1. İlkenizi test etmek için **Kullanıcı akışını Çalıştır**' ı seçin.
1. **Uygulama** için, daha önce kaydettiğiniz *testapp1* adlı Web uygulamasını seçin. **Yanıt URL 'si** gösterilmesi gerekir `https://jwt.ms` .
1. **Kullanıcı akışını Çalıştır** düğmesini seçin.
1. Kaydolma veya oturum açma **sayfasından Amazon hesabıyla oturum açmak için Amazon** ' ı seçin.

Oturum açma işlemi başarılı olursa, tarayıcınız öğesine yönlendirilir `https://jwt.ms` ve bu, Azure AD B2C tarafından döndürülen belirtecin içeriğini görüntüler.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>İlke anahtarı oluşturma

Daha önce Azure AD B2C kiracınızda kaydettiğiniz istemci gizli anahtarını depolamanız gerekir.

1. [Azure portalında](https://portal.azure.com/) oturum açın.
2. Üst menüdeki **Dizin + abonelik** filtresini seçip kiracınızı içeren dizini seçerek Azure AD B2C kiracınızı içeren dizini kullandığınızdan emin olun.
3. Azure portal sol üst köşesindeki **tüm hizmetler** ' i seçin ve ardından **Azure AD B2C**' i arayıp seçin.
4. Genel Bakış sayfasında **kimlik deneyimi çerçevesi**' ni seçin.
5. **Ilke anahtarlarını** seçin ve ardından **Ekle**' yi seçin.
6. **Seçenekler** için öğesini seçin `Manual` .
7. İlke anahtarı için bir **ad** girin. Örneğin, `AmazonSecret`. Ön ek, `B2C_1A_` anahtarınızın adına otomatik olarak eklenir.
8. **Gizli**, daha önce kaydettiğiniz istemci gizli anahtarını girin.
9. **Anahtar kullanımı** için öğesini seçin `Signature` .
10. **Oluştur**’a tıklayın.

## <a name="configure-amazon-as-an-identity-provider"></a>Amazon 'ı bir kimlik sağlayıcısı olarak yapılandırma

Kullanıcıların bir Amazon hesabı kullanarak oturum açmasını sağlamak için hesabı bir talep sağlayıcı olarak tanımlamanız gerekir. Bu Azure AD B2C bir uç nokta ile iletişim kurabilir. Uç noktası, belirli bir kullanıcının kimliği doğrulandığını doğrulamak için Azure AD B2C tarafından kullanılan bir talep kümesi sağlar.

Bir Amazon hesabını, ilkenizin uzantı dosyasındaki **Claimsproviders** öğesine ekleyerek bir istek sağlayıcısı olarak tanımlayabilirsiniz.


1. *TrustFrameworkExtensions.xml* açın.
2. **Claimsproviders** öğesini bulun. Yoksa, kök öğenin altına ekleyin.
3. Yeni bir **ClaimsProvider** 'ı aşağıdaki şekilde ekleyin:

    ```xml
    <ClaimsProvider>
      <Domain>amazon.com</Domain>
      <DisplayName>Amazon</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Amazon-OAuth2">
        <DisplayName>Amazon</DisplayName>
        <Protocol Name="OAuth2" />
        <Metadata>
          <Item Key="ProviderName">amazon</Item>
          <Item Key="authorization_endpoint">https://www.amazon.com/ap/oa</Item>
          <Item Key="AccessTokenEndpoint">https://api.amazon.com/auth/o2/token</Item>
          <Item Key="ClaimsEndpoint">https://api.amazon.com/user/profile</Item>
          <Item Key="scope">profile</Item>
          <Item Key="HttpBinding">POST</Item>
          <Item Key="UsePolicyInRedirectUri">false</Item>
          <Item Key="client_id">Your Amazon application client ID</Item>
        </Metadata>
        <CryptographicKeys>
          <Key Id="client_secret" StorageReferenceId="B2C_1A_AmazonSecret" />
        </CryptographicKeys>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="user_id" />
          <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
          <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
          <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="amazon.com" />
          <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
        </OutputClaims>
          <OutputClaimsTransformations>
          <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
          <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
          <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
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
    <ClaimsProviderSelection TargetClaimsExchangeId="AmazonExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="AmazonExchange" TechnicalProfileReferenceId="Amazon-OAuth2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>Özel ilkenizi test etme

1. Örneğin, bağlı olan taraf ilkenizi seçin `B2C_1A_signup_signin` .
1. **Uygulama** için, [daha önce kaydetmiş](tutorial-register-applications.md)olduğunuz bir Web uygulamasını seçin. **Yanıt URL 'si** gösterilmesi gerekir `https://jwt.ms` .
1. **Şimdi Çalıştır** düğmesini seçin.
1. Kaydolma veya oturum açma **sayfasından Amazon hesabıyla oturum açmak için Amazon** ' ı seçin.

Oturum açma işlemi başarılı olursa, tarayıcınız öğesine yönlendirilir `https://jwt.ms` ve bu, Azure AD B2C tarafından döndürülen belirtecin içeriğini görüntüler.

::: zone-end
