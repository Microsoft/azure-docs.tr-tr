---
title: Başka bir Azure AD B2C kiracısından Azure AD B2C hesabıyla kaydolma ve oturum açma ayarlama
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C kullanarak uygulamalarınızda bulunan başka bir kiracıdan Azure AD B2C hesaplarıyla müşterilere kaydolma ve oturum açma sağlayın.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/15/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit, project-no-code
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 3aacc6c50024cfdca3af639e7c6d14f69ed00519
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107029055"
---
# <a name="set-up-sign-up-and-sign-in-with-an-azure-ad-b2c-account-from-another-azure-ad-b2c-tenant"></a>Başka bir Azure AD B2C kiracısından Azure AD B2C hesabıyla kaydolma ve oturum açma ayarlama

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="overview"></a>Genel Bakış

Bu makalede, başka bir Azure AD B2C kiracısıyla bir Federasyonun nasıl ayarlanacağı açıklanır. Uygulamalarınız Azure AD B2C korumalı olduğunda, bu, diğer Azure AD B2C's kullanıcıların mevcut hesaplarıyla oturum açmasına olanak tanır. Aşağıdaki diyagramda, kullanıcılar, *fabrikam*'ın Azure AD B2C kiracısıyla yönetilen bir hesapla *contoso* Azure AD B2C tarafından korunan bir uygulamada oturum açabilirler 

![Başka bir Azure AD B2C kiracısıyla Azure AD B2C Federasyonu](./media/identity-provider-azure-ad-b2c/azure-ad-b2c-federation.png)


## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-an-azure-ad-b2c-application"></a>Azure AD B2C uygulaması oluşturma

Başka bir Azure AD B2C kiracısından (örneğin, Fabrikam) bir hesabı olan kullanıcılar için oturum açmayı etkinleştirmek için, Azure AD B2C (örneğin, Contoso):

1. Bir [Kullanıcı akışı](tutorial-create-user-flows.md)veya [özel bir ilke](custom-policy-get-started.md)oluşturun.
1. Ardından, bu bölümde açıklandığı gibi Azure AD B2C bir uygulama oluşturun. 

Bir uygulama oluşturmak için.

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Diğer Azure AD B2C kiracınızı içeren dizini kullandığınızdan emin olun (örneğin, Fabrikam.com).
1. Azure portal, araması yapın ve **Azure AD B2C** seçin.
1. **Uygulama kayıtları** öğesini seçin ve ardından **Yeni kayıt**' ı seçin.
1. Uygulama için bir **ad** girin. Örneğin, *Contosoapp*.
1. **Desteklenen hesap türleri** altında, **herhangi bir kimlik sağlayıcısında veya kuruluş dizininde (Kullanıcı akışları olan kullanıcıların kimliğini doğrulamak için) hesaplar**' ı seçin.
1. **Yeniden yönlendirme URI 'si** altında **Web**' i seçin ve ardından aşağıdaki URL 'yi, `your-B2C-tenant-name` Azure AD B2C kiracınızın adı (örneğin, Contoso) ile değiştirildiği tüm küçük harflerle girin.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Örneğin, `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.

    [Özel bir etki alanı](custom-domain.md)kullanıyorsanız, girin `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp` . `your-domain-name`Özel etki alanınız ile ve `your-tenant-name` kiracınızın adıyla değiştirin.

1. Izinler altında, **OpenID ve offline_access izinleri için yönetici Izni ver** onay kutusunu seçin.
1. **Kaydet**’i seçin.
1. **Azure AD B2C-uygulama kayıtları** sayfasında, oluşturduğunuz uygulamayı (örneğin *contosoapp*) seçin.
1. Uygulamaya Genel Bakış sayfasında gösterilen **uygulama (istemci) kimliğini** kaydedin. Bu, sonraki bölümde kimlik sağlayıcısını yapılandırırken gerekir.
1. Sol taraftaki menüde, **Yönet** altında, **Sertifikalar & gizlilikler**' ı seçin.
1. **Yeni istemci gizli dizisi**’ni seçin.
1. **Açıklama** kutusuna istemci parolası için bir açıklama girin. Örneğin, *clientsecret1*.
1. **Süre sonu** altında, parolasının geçerli olduğu bir süre seçin ve ardından **Ekle**' yi seçin.
1. Gizli dizi **değerini** kaydedin. Bu, sonraki bölümde kimlik sağlayıcısını yapılandırırken gerekir.


::: zone pivot="b2c-user-flow"

## <a name="configure-azure-ad-b2c-as-an-identity-provider"></a>Kimlik sağlayıcısı olarak Azure AD B2C yapılandırma

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Federasyonu yapılandırmak istediğiniz Azure AD B2C kiracıyı içeren dizini kullandığınızdan emin olun (örneğin, Contoso). Üstteki menüden **Dizin + abonelik** filtresini seçin ve Azure AD B2C kiracınızı (örneğin, Contoso) içeren dizini seçin.
1. Azure portal sol üst köşesindeki **tüm hizmetler** ' i seçin ve ardından **Azure AD B2C**' i arayıp seçin.
1. **Kimlik sağlayıcıları**' nı seçin ve ardından **Yeni OpenID Connect sağlayıcısı**' nı seçin.
1. Bir **ad** girin. Örneğin, *fabrikam* girin.
1. **Meta veri URL 'si** için, `{tenant}` Azure AD B2C kiracınızın etki alanı adıyla birlikte aşağıdaki URL 'yi girin (örneğin, Fabrikam). Öğesini, `{policy}` diğer kiracıda yapılandırdığınız ilke adıyla değiştirin:

    ```
    https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/v2.0/.well-known/openid-configuration
    ```

    Örneğin, `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/B2C_1_susi/v2.0/.well-known/openid-configuration`.

1. **ISTEMCI kimliği** için, daha önce KAYDETTIĞINIZ uygulama kimliğini girin.
1. **İstemci parolası** için, daha önce kaydettiğiniz istemci gizli anahtarını girin.
1. **Kapsam** için, girin `openid` .
1. **Yanıt türü** ve **Yanıt modu** için varsayılan değerleri bırakın.
1. Seçim **Etki alanı ipucu** için, [doğrudan oturum açma](direct-signin.md#redirect-sign-in-to-a-social-provider)için kullanmak istediğiniz etki alanı adını girin. Örneğin, *fabrikam.com*.
1. **Kimlik sağlayıcısı talep eşlemesi** altında aşağıdaki talepleri seçin:

    - **Kullanıcı kimliği**: *Sub*
    - **Görünen ad**: *ad*
    - **Verilen ad**: *given_name*
    - **Soyadı**: *family_name*
    - **Eposta**: *e-posta*

1. **Kaydet**’i seçin.

## <a name="add-azure-ad-b2c-identity-provider-to-a-user-flow"></a>Kullanıcı akışına Azure AD B2C Identity sağlayıcısı ekleme 

1. Azure AD B2C kiracınızda **Kullanıcı akışları**' nı seçin.
1. Azure AD B2C kimlik sağlayıcısını eklemek istediğiniz kullanıcı akışına tıklayın.
1. **Sosyal kimlik sağlayıcıları** altında **fabrikam**' ı seçin.
1. **Kaydet**’i seçin.
1. İlkenizi test etmek için **Kullanıcı akışını Çalıştır**' ı seçin.
1. **Uygulama** için, daha önce kaydettiğiniz *testapp1* adlı Web uygulamasını seçin. **Yanıt URL 'si** gösterilmesi gerekir `https://jwt.ms` .
1. **Kullanıcı akışını Çalıştır** düğmesini seçin.
1. Kaydolma veya oturum açma sayfasından **fabrikam** ' ı seçerek diğer Azure AD B2C kiracısıyla oturum açın.

Oturum açma işlemi başarılı olursa, tarayıcınız öğesine yönlendirilir `https://jwt.ms` ve bu, Azure AD B2C tarafından döndürülen belirtecin içeriğini görüntüler.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>İlke anahtarı oluşturma

Daha önce Azure AD B2C kiracınızda oluşturduğunuz uygulama anahtarını depolamanız gerekir.

1. Federasyonu yapılandırmak istediğiniz Azure AD B2C kiracıyı içeren dizini kullandığınızdan emin olun (örneğin, Contoso). Üstteki menüden **Dizin + abonelik** filtresini seçin ve Azure AD B2C kiracınızı (örneğin, Contoso) içeren dizini seçin.
1. Azure portal sol üst köşesindeki **tüm hizmetler** ' i seçin ve ardından **Azure AD B2C**' i arayıp seçin.
1. **İlkeler** altında **kimlik deneyimi çerçevesi**' ni seçin.
1. **İlke anahtarlarını** seçin ve ardından **Ekle**' yi seçin.
1. **Seçenekler** için öğesini seçin `Manual` .
1. İlke anahtarı için bir **ad** girin. Örneğin, `FabrikamAppSecret`.  Ön ek, `B2C_1A_` oluşturulduğu sırada anahtarınızın adına otomatik olarak eklenir, bu nedenle aşağıdaki BÖLÜMDEKI XML başvurusu *B2C_1A_FabrikamAppSecret*.
1. **Gizli**, daha önce kaydettiğiniz istemci gizli anahtarını girin.
1. **Anahtar kullanımı** için öğesini seçin `Signature` .
1. **Oluştur**’u seçin.

## <a name="configure-azure-ad-b2c-as-an-identity-provider"></a>Kimlik sağlayıcısı olarak Azure AD B2C yapılandırma

Kullanıcıların başka bir Azure AD B2C kiracısından (Fabrikam) bir hesap kullanarak oturum açmasını sağlamak için diğer Azure AD B2C, Azure AD B2C bir uç nokta aracılığıyla iletişim kurabildiği bir talep sağlayıcısı olarak tanımlamanız gerekir. Uç noktası, belirli bir kullanıcının kimliği doğrulandığını doğrulamak için Azure AD B2C tarafından kullanılan bir talep kümesi sağlar.

Azure AD B2C, ilkenizin uzantısı dosyasındaki **ClaimsProvider** öğesine Azure AD B2C ekleyerek bir talep sağlayıcı olarak tanımlayabilirsiniz.

1. *TrustFrameworkExtensions.xml* dosyasını açın.
1. **Claimsproviders** öğesini bulun. Yoksa, kök öğenin altına ekleyin.
1. Yeni bir **ClaimsProvider** 'ı aşağıdaki şekilde ekleyin:
    ```xml
    <ClaimsProvider>
      <Domain>fabrikam.com</Domain>
      <DisplayName>Federation with Fabrikam tenant</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AzureADB2CFabrikam-OpenIdConnect">
        <DisplayName>Fabrikam</DisplayName>
        <Protocol Name="OpenIdConnect"/>
        <Metadata>
          <!-- Update the Client ID below to the Application ID -->
          <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
          <!-- Update the metadata URL with the other Azure AD B2C tenant name and policy name -->
          <Item Key="METADATA">https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/v2.0/.well-known/openid-configuration</Item>
          <Item Key="UsePolicyInRedirectUri">false</Item>
          <Item Key="response_types">code</Item>
          <Item Key="scope">openid</Item>
          <Item Key="response_mode">form_post</Item>
          <Item Key="HttpBinding">POST</Item>
        </Metadata>
        <CryptographicKeys>
          <Key Id="client_secret" StorageReferenceId="B2C_1A_FabrikamAppSecret"/>
        </CryptographicKeys>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
          <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
          <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name" />
          <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
          <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
          <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss"  />
          <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
          <OutputClaim ClaimTypeReferenceId="otherMails" PartnerClaimType="emails"/>    
        </OutputClaims>
        <OutputClaimsTransformations>
          <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
          <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
          <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
        </OutputClaimsTransformations>
        <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin"/>
      </TechnicalProfile>
     </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Aşağıdaki XML öğelerini ilgili değerle güncelleştirin:

    |XML öğesi  |Değer  |
    |---------|---------|
    |ClaimsProvider\Domain  | [Doğrudan oturum açma](direct-signin.md?pivots=b2c-custom-policy#redirect-sign-in-to-a-social-provider)için kullanılan etki alanı adı. Doğrudan oturum açma bölümünde kullanmak istediğiniz etki alanı adını girin. Örneğin, *fabrikam.com*. |
    |Teknisyen \ DisplayName|Bu değer, oturum açma ekranınızdaki oturum açma düğmesinde görüntülenir. Örneğin, *fabrikam*. |
    |Meta veri \ client_id|Kimlik sağlayıcısının uygulama tanımlayıcısı. Istemci KIMLIĞINI diğer Azure AD B2C kiracısında daha önce oluşturduğunuz uygulama KIMLIĞIYLE güncelleştirin.|
    |Metadata\METADATA|OpenID ile bilinen yapılandırma uç noktası olarak da bilinen bir OpenID Connect kimlik sağlayıcısı yapılandırma belgesine işaret eden bir URL. `{tenant}`Diğer Azure AD B2C kiracının (Fabrikam) etki alanı adıyla birlikte aşağıdaki URL 'yi girin. Öğesini, `{tenant}` diğer kiracıda yapılandırdığınız ilke adıyla ve `{policy]` ilke adıyla değiştirin `https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/v2.0/.well-known/openid-configuration` . Örneğin, `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/B2C_1_susi/v2.0/.well-known/openid-configuration`.|
    |CryptographicKeys| **Storagereferenceıd** değerini, daha önce oluşturduğunuz ilke anahtarının adı olarak güncelleştirin. Örneğin, `B2C_1A_FabrikamAppSecret`.| 
    

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADB2CFabrikamExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="AzureADB2CFabrikamExchange" TechnicalProfileReferenceId="AzureADB2CFabrikam-OpenIdConnect" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]


## <a name="test-your-custom-policy"></a>Özel ilkenizi test etme

1. Örneğin, bağlı olan taraf ilkenizi seçin `B2C_1A_signup_signin` .
1. **Uygulama** için, [daha önce kaydetmiş](tutorial-register-applications.md)olduğunuz bir Web uygulamasını seçin. **Yanıt URL 'si** gösterilmesi gerekir `https://jwt.ms` .
1. **Şimdi Çalıştır** düğmesini seçin.
1. Kaydolma veya oturum açma sayfasından **fabrikam** ' ı seçerek diğer Azure AD B2C kiracısıyla oturum açın.

Oturum açma işlemi başarılı olursa, tarayıcınız öğesine yönlendirilir `https://jwt.ms` ve bu, Azure AD B2C tarafından döndürülen belirtecin içeriğini görüntüler.

::: zone-end

## <a name="next-steps"></a>Sonraki adımlar

[Diğer Azure AD B2C belirtecini uygulamanıza nasıl geçirebileceğinizi](idp-pass-through-user-flow.md)öğrenin.
