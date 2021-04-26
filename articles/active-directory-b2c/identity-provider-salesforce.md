---
title: Kayıt ayarlama ve Salesforce hesabı ile oturum açma
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C kullanarak uygulamalarınızda Salesforce hesabı bulunan müşterilere kaydolma ve oturum açma sağlayın.
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
ms.openlocfilehash: 008d47fe7086322ea0bb7ef26a6c4b449f4269d2
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107028834"
---
# <a name="set-up-sign-up-and-sign-in-with-a-salesforce-account-using-azure-active-directory-b2c"></a>Azure Active Directory B2C kullanarak bir Salesforce hesabı ile kaydolma ve oturum açma ayarlama

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


## <a name="create-a-salesforce-application"></a>Salesforce uygulaması oluşturma

Azure Active Directory B2C (Azure AD B2C) ' de Salesforce hesabı olan kullanıcılar için oturum açmayı etkinleştirmek üzere Salesforce [App Manager](https://login.salesforce.com/)'da bir uygulama oluşturmanız gerekir. Daha fazla bilgi için bkz. [temel bağlı uygulama ayarlarını yapılandırma](https://help.salesforce.com/articleView?id=connected_app_create_basics.htm)ve [API Tümleştirmesi Için OAuth ayarlarını etkinleştirme](https://help.salesforce.com/articleView?id=connected_app_create_api_integration.htm)

1. [Salesforce 'Ta oturum açın](https://login.salesforce.com/).
1. Menüden **Kurulum**' u seçin.
1.  **Uygulamalar**' ı genişletin ve ardından **App Manager**' ı seçin.
1. **Yeni bağlı uygulama**' yı seçin.
1. **Temel bilgiler** altında şunu girin:
    1. **Bağlı uygulama adı** -bağlı uygulama adı App Manager 'Da ve uygulama başlatıcısı kutucuğunda görüntülenir. Ad, kuruluşunuz dahilinde benzersiz olmalıdır. 
    1. **API adı** 
    1. **Iletişim e-postası** -Salesforce için iletişim e-postası
1. **API (OAuth ayarlarını etkinleştir)** altında **OAuth ayarlarını etkinleştir** ' i seçin.
    1. **Geri çağırma URL 'si** için girin `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` . [Özel bir etki alanı](custom-domain.md)kullanıyorsanız, girin `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp` . `your-tenant-name`, Kiracınızın adıyla ve `your-domain-name` özel etki alanınız ile değiştirin. Kiracı, Azure AD B2C büyük harfle tanımlansa bile kiracı adınızı girerken tüm küçük harfleri kullanmanız gerekir.
    1. **Seçili OAuth kapsamlarında**, **temel bilgilere (kimlik, profil, e-posta, adres, telefon) erişin** ve **benzersiz tanımlayıcısına (OpenID) erişim izni verin**.
    1. **Web sunucusu akışı Için gizli iste**' yi seçin.
1. **Kimlik belirtecini Yapılandır** ' ı seçin 
    1. Belirteç 5 dakika **geçerli** olacak şekilde ayarlayın.
    1. **Standart talepleri içer**' i seçin.
1. **Kaydet**’e tıklayın.
1. **Tüketici anahtarı** ve **Tüketici parolası** değerlerini kopyalayın. Salesforce 'ı kiracınızda bir kimlik sağlayıcısı olarak yapılandırmak için her ikisine de ihtiyacınız olacak. **İstemci parolası** önemli bir güvenlik kimlik bilgileridir.

::: zone pivot="b2c-user-flow"

## <a name="configure-salesforce-as-an-identity-provider"></a>Salesforce 'ı bir kimlik sağlayıcısı olarak yapılandırma

1. Azure AD B2C kiracı içeren dizini kullandığınızdan emin olun. Üstteki menüden **Dizin + abonelik** filtresini seçin ve Azure AD B2C kiracınızı içeren dizini seçin.
1. Azure portal sol üst köşesindeki **tüm hizmetler** ' i seçin ve ardından **Azure AD B2C**' i arayıp seçin.
1. **Kimlik sağlayıcıları**' nı seçin ve ardından **Yeni OpenID Connect sağlayıcısı**' nı seçin.
1. Bir **ad** girin. Örneğin *Salesforce* girin.
1. **Meta veri URL 'si** Için [Salesforce OpenID Connect yapılandırma belgesinin](https://help.salesforce.com/articleView?id=remoteaccess_using_openid_discovery_endpoint.htm)URL 'sini girin. Bir korumalı alan için login.salesforce.com, test.salesforce.com ile değiştirilmiştir. Bir topluluk için login.salesforce.com, username.force.com/.well-known/openid-configuration gibi topluluk URL 'siyle değiştirilmiştir. URL 'nin HTTPS olması gerekir.

    ```
    https://login.salesforce.com/.well-known/openid-configuration
    ```

1. **ISTEMCI kimliği** için, daha önce KAYDETTIĞINIZ uygulama kimliğini girin.
1. **İstemci parolası** için, daha önce kaydettiğiniz istemci gizli anahtarını girin.
1. **Kapsam** için, girin `openid id profile email` .
1. **Yanıt türü** ve **Yanıt modu** için varsayılan değerleri bırakın.
1. Seçim **Etki alanı ipucu** için girin `contoso.com` . Daha fazla bilgi için bkz. [Azure Active Directory B2C kullanarak doğrudan oturum açma ayarlama](direct-signin.md#redirect-sign-in-to-a-social-provider).
1. **Kimlik sağlayıcısı talep eşlemesi** altında aşağıdaki talepleri seçin:

    - **Kullanıcı kimliği**: *Sub*
    - **Görünen ad**: *ad*
    - **Verilen ad**: *given_name*
    - **Soyadı**: *family_name*
    - **Eposta**: *e-posta*

1. **Kaydet**’i seçin.

## <a name="add-salesforce-identity-provider-to-a-user-flow"></a>Bir Kullanıcı akışına Salesforce kimlik sağlayıcısı ekleme 

Bu noktada Salesforce kimlik sağlayıcısı ayarlanmıştır, ancak oturum açma sayfalarında henüz mevcut değildir. Salesforce kimlik sağlayıcısını bir Kullanıcı akışına eklemek için:

1. Azure AD B2C kiracınızda **Kullanıcı akışları**' nı seçin.
1. Salesforce kimlik sağlayıcısı eklemek istediğiniz kullanıcı akışına tıklayın.
1. **Sosyal kimlik sağlayıcıları** altında **Salesforce**' ı seçin.
1. **Kaydet**’i seçin.
1. İlkenizi test etmek için **Kullanıcı akışını Çalıştır**' ı seçin.
1. **Uygulama** için, daha önce kaydettiğiniz *testapp1* adlı Web uygulamasını seçin. **Yanıt URL 'si** gösterilmesi gerekir `https://jwt.ms` .
1. **Kullanıcı akışını Çalıştır** düğmesini seçin.
1. Kaydolma veya oturum açma sayfasından Salesforce hesabı ile oturum **açmak için Salesforce** ' ı seçin.

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
7. İlke anahtarı için bir **ad** girin. Örneğin, `SalesforceSecret`. Ön ek, `B2C_1A_` anahtarınızın adına otomatik olarak eklenir.
8. **Gizli**, daha önce kaydettiğiniz istemci gizli anahtarını girin.
9. **Anahtar kullanımı** için öğesini seçin `Signature` .
10. **Oluştur**’a tıklayın.

## <a name="configure-salesforce-as-an-identity-provider"></a>Salesforce 'ı bir kimlik sağlayıcısı olarak yapılandırma

Kullanıcıların bir Salesforce hesabı kullanarak oturum açmasını sağlamak için hesabı, Azure AD B2C bir uç nokta aracılığıyla iletişim kurabildiği bir talep sağlayıcı olarak tanımlamanız gerekir. Uç noktası, belirli bir kullanıcının kimliği doğrulandığını doğrulamak için Azure AD B2C tarafından kullanılan bir talep kümesi sağlar.

Bir Salesforce hesabını, ilkenizin uzantı dosyasındaki **Claimsproviders** öğesine ekleyerek bir talep sağlayıcı olarak tanımlayabilirsiniz.

1. *TrustFrameworkExtensions.xml* açın.
2. **Claimsproviders** öğesini bulun. Yoksa, kök öğenin altına ekleyin.
3. Yeni bir **ClaimsProvider** 'ı aşağıdaki şekilde ekleyin:

    ```xml
    <ClaimsProvider>
      <Domain>salesforce.com</Domain>
      <DisplayName>Salesforce</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Salesforce-OpenIdConnect">
          <DisplayName>Salesforce</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <Item Key="METADATA">https://login.salesforce.com/.well-known/openid-configuration</Item>
            <Item Key="response_types">code</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="scope">openid id profile email</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">Your Salesforce application ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_SalesforceSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="salesforce.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
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

4. **Meta veriler** [Salesforce OpenID Connect yapılandırma belgesinin](https://help.salesforce.com/articleView?id=remoteaccess_using_openid_discovery_endpoint.htm)URL 'sine ayarlanır. Bir korumalı alan için login.salesforce.com, test.salesforce.com ile değiştirilmiştir. Bir topluluk için login.salesforce.com, username.force.com/.well-known/openid-configuration gibi topluluk URL 'siyle değiştirilmiştir. URL 'nin HTTPS olması gerekir.
5. Uygulama kaydından uygulama KIMLIĞINE **client_id** ayarlayın.
6. Dosyayı kaydedin.

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="SalesforceExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="SalesforceExchange" TechnicalProfileReferenceId="Salesforce-OpenIdConnect" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>Özel ilkenizi test etme

1. Örneğin, bağlı olan taraf ilkenizi seçin `B2C_1A_signup_signin` .
1. **Uygulama** için, [daha önce kaydetmiş](tutorial-register-applications.md)olduğunuz bir Web uygulamasını seçin. **Yanıt URL 'si** gösterilmesi gerekir `https://jwt.ms` .
1. **Şimdi Çalıştır** düğmesini seçin.
1. Kaydolma veya oturum açma sayfasından Salesforce hesabı ile oturum **açmak için Salesforce** ' ı seçin.

Oturum açma işlemi başarılı olursa, tarayıcınız öğesine yönlendirilir `https://jwt.ms` ve bu, Azure AD B2C tarafından döndürülen belirtecin içeriğini görüntüler.


::: zone-end

## <a name="next-steps"></a>Sonraki adımlar

[Salesforce belirtecini uygulamanıza nasıl geçirebileceğinizi](idp-pass-through-user-flow.md)öğrenin.
