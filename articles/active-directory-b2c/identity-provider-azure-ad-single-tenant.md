---
title: Azure AD kuruluşu için oturum açma ayarlama
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C belirli bir Azure Active Directory organizasyonu için oturum açma ayarlayın.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/17/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit, project-no-code
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 9a31fe8046e6bb0a933ade78ee2c7f4b208767a7
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107028358"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>Azure Active Directory B2C içinde belirli bir Azure Active Directory kuruluş için oturum açma ayarlayın

Bu makalede, Azure AD B2C bir Kullanıcı akışı kullanarak belirli bir Azure AD kuruluştan kullanıcılar için oturum açmayı etkinleştirme konusu gösterilmektedir.

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="register-an-azure-ad-app"></a>Azure AD uygulamasını kaydetme

Belirli bir Azure AD kuruluştan Azure AD hesabı olan kullanıcılar için oturum açmayı etkinleştirmek üzere, Azure Active Directory B2C (Azure AD B2C) ' de bir uygulama oluşturmanız gerekir [Azure Portal](https://portal.azure.com). Daha fazla bilgi için bkz. [Microsoft Identity platformu ile uygulama kaydetme](../active-directory/develop/quickstart-register-app.md).

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Kuruluşunuzun Azure AD kiracınızı içeren dizini kullandığınızdan emin olun (örneğin, contoso.com). Üst menüden **Dizin + abonelik filtresi** ' ni seçin ve ardından Azure AD kiracınızı içeren dizini seçin.
1. Azure portal sol üst köşesindeki **tüm hizmetler** ' i seçin ve ardından **uygulama kayıtları**' i arayıp seçin.
1. **Yeni kayıt** seçeneğini belirleyin.
1. Uygulamanız için bir **ad** girin. Örneğin, `Azure AD B2C App`.
1. Bu **kuruluş dizinindeki hesapların** varsayılan seçimini yalnızca bu uygulama için kabul edin.
1. **Yeniden yönlendirme URI 'si** Için, **Web**'in değerini kabul edin ve aşağıdaki URL 'yi tüm küçük harflerle, burada `your-B2C-tenant-name` Azure AD B2C kiracınızın adıyla birlikte girin.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Örneğin, `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`.

    [Özel bir etki alanı](custom-domain.md)kullanıyorsanız, girin `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp` . `your-domain-name`Özel etki alanınız ile ve `your-tenant-name` kiracınızın adıyla değiştirin.

1. **Kaydet**’i seçin. Daha sonraki bir adımda kullanmak üzere **uygulama (istemci) kimliğini** kaydedin.
1. **Sertifikalar & sertifikalar**' ı seçin ve ardından **yeni istemci parolası**' nı seçin.
1. Gizli dizi için bir **Açıklama** girin, bir süre sonu seçin ve ardından **Ekle**' yi seçin. Daha sonraki bir adımda kullanmak için gizli dizi **değerini** kaydedin.

### <a name="configuring-optional-claims"></a>İsteğe bağlı talepler yapılandırılıyor

`family_name` `given_name` Azure AD 'den ve taleplerini almak istiyorsanız, Azure Portal Kullanıcı arabirimi veya uygulama bildiriminde uygulamanız için isteğe bağlı talepler yapılandırabilirsiniz. Daha fazla bilgi için bkz. [Azure AD uygulamanıza isteğe bağlı talepler sağlama](../active-directory/develop/active-directory-optional-claims.md).

1. [Azure portalında](https://portal.azure.com) oturum açın. **Azure Active Directory**'yi bulun ve seçin.
1. **Yönet** bölümünden **uygulama kayıtları**' yi seçin.
1. Listede için isteğe bağlı talepler yapılandırmak istediğiniz uygulamayı seçin.
1. **Yönet** bölümünde **belirteç yapılandırması**' nı seçin.
1. **İsteğe bağlı talep Ekle**' yi seçin.
1. **Belirteç türü** için **kimlik**' i seçin.
1. Eklemek için isteğe bağlı talepler ' i `family_name` seçin `given_name` .
1. **Ekle**'ye tıklayın.

::: zone pivot="b2c-user-flow"

## <a name="configure-azure-ad-as-an-identity-provider"></a>Azure AD 'yi kimlik sağlayıcısı olarak yapılandırma

1. Azure AD B2C kiracı içeren dizini kullandığınızdan emin olun. Üstteki menüden **Dizin + abonelik** filtresini seçin ve Azure AD B2C kiracınızı içeren dizini seçin.
1. Azure portal sol üst köşesindeki **tüm hizmetler** ' i seçin ve ardından **Azure AD B2C**' i arayıp seçin.
1. **Kimlik sağlayıcıları**' nı seçin ve ardından **Yeni OpenID Connect sağlayıcısı**' nı seçin.
1. Bir **ad** girin. Örneğin, *contoso Azure AD* yazın.
1. **Meta veri URL 'si** Için, `{tenant}` Azure AD kiracınızın etki alanı adıyla birlikte aşağıdaki URL 'yi girin:

    ```
    https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration
    ```

    Örneğin, `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`.
    Örneğin, `https://login.microsoftonline.com/contoso.com/v2.0/.well-known/openid-configuration`.

1. **ISTEMCI kimliği** için, daha önce KAYDETTIĞINIZ uygulama kimliğini girin.
1. **İstemci parolası** için, daha önce kaydettiğiniz istemci gizli anahtarını girin.
1. **Kapsam** için girin `openid profile` .
1. **Yanıt türü** ve **Yanıt modu** için varsayılan değerleri bırakın.
1. Seçim **Etki alanı ipucu** için girin `contoso.com` . Daha fazla bilgi için bkz. [Azure Active Directory B2C kullanarak doğrudan oturum açma ayarlama](direct-signin.md#redirect-sign-in-to-a-social-provider).
1. **Kimlik sağlayıcısı talep eşlemesi** altında aşağıdaki talepleri seçin:

    - **Kullanıcı kimliği**: *OID*
    - **Görünen ad**: *ad*
    - **Verilen ad**: *given_name*
    - **Soyadı**: *family_name*
    - **E-posta**: *preferred_username*

1. **Kaydet**’i seçin.

## <a name="add-azure-ad-identity-provider-to-a-user-flow"></a>Azure AD kimlik sağlayıcısını bir Kullanıcı akışına ekleme 

Bu noktada Azure AD kimlik sağlayıcısı ayarlanmıştır, ancak oturum açma sayfalarında henüz mevcut değildir. Azure AD kimlik sağlayıcısını bir Kullanıcı akışına eklemek için:

1. Azure AD B2C kiracınızda **Kullanıcı akışları**' nı seçin.
1. Azure AD kimlik sağlayıcısı eklemek istediğiniz kullanıcı akışına tıklayın.
1. **Sosyal kimlik sağlayıcıları** altında **contoso Azure AD**' yi seçin.
1. **Kaydet**’i seçin.
1. İlkenizi test etmek için **Kullanıcı akışını Çalıştır**' ı seçin.
1. **Uygulama** için, daha önce kaydettiğiniz *testapp1* adlı Web uygulamasını seçin. **Yanıt URL 'si** gösterilmesi gerekir `https://jwt.ms` .
1. **Kullanıcı akışını Çalıştır** düğmesini seçin.
1. Kaydolma veya oturum açma sayfasından Azure AD contoso hesabıyla oturum açmak için **contoso Azure AD** ' yi seçin.

Oturum açma işlemi başarılı olursa, tarayıcınız öğesine yönlendirilir `https://jwt.ms` ve bu, Azure AD B2C tarafından döndürülen belirtecin içeriğini görüntüler.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>İlke anahtarı oluşturma

Azure AD B2C kiracınızda oluşturduğunuz uygulama anahtarını depolamanız gerekir.

1. Azure AD B2C kiracınızı içeren dizini kullandığınızdan emin olun. Üst menüden **Dizin + abonelik filtresi** ' ni seçin ve ardından Azure AD B2C kiracınızı içeren dizini seçin.
1. Azure portal sol üst köşesindeki **tüm hizmetler** ' i seçin ve ardından **Azure AD B2C**' i arayıp seçin.
1. **İlkeler** altında **kimlik deneyimi çerçevesi**' ni seçin.
1. **İlke anahtarlarını** seçin ve ardından **Ekle**' yi seçin.
1. **Seçenekler** için öğesini seçin `Manual` .
1. İlke anahtarı için bir **ad** girin. Örneğin, `ContosoAppSecret`.  Ön ek, `B2C_1A_` oluşturulduğu sırada anahtarınızın adına otomatik olarak eklenir, bu nedenle aşağıdaki BÖLÜMDEKI XML başvurusu *B2C_1A_ContosoAppSecret*.
1. **Gizli**, daha önce kaydettiğiniz istemci gizli anahtarını girin.
1. **Anahtar kullanımı** için öğesini seçin `Signature` .
1. **Oluştur**’u seçin.

## <a name="configure-azure-ad-as-an-identity-provider"></a>Azure AD 'yi kimlik sağlayıcısı olarak yapılandırma

Kullanıcıların bir Azure AD hesabı kullanarak oturum açmasını sağlamak için Azure AD 'yi Azure AD B2C bir uç nokta aracılığıyla iletişim kurabildiği bir talep sağlayıcı olarak tanımlamanız gerekir. Uç noktası, belirli bir kullanıcının kimliği doğrulandığını doğrulamak için Azure AD B2C tarafından kullanılan bir talep kümesi sağlar.

Azure AD 'yi, ilkenizin uzantısı dosyasındaki **ClaimsProvider** öğesine ekleyerek bir talep sağlayıcı olarak tanımlayabilirsiniz.

1. *TrustFrameworkExtensions.xml* dosyasını açın.
2. **Claimsproviders** öğesini bulun. Yoksa, kök öğenin altına ekleyin.
3. Yeni bir **ClaimsProvider** 'ı aşağıdaki şekilde ekleyin:
    ```xml
    <ClaimsProvider>
      <Domain>Contoso</Domain>
      <DisplayName>Login using Contoso</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AADContoso-OpenIdConnect">
          <DisplayName>Contoso Employee</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="OpenIdConnect"/>
          <Metadata>
            <Item Key="METADATA">https://login.microsoftonline.com/tenant-name.onmicrosoft.com/v2.0/.well-known/openid-configuration</Item>
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid profile</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_ContosoAppSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="oid"/>
            <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. **ClaimsProvider** öğesi altında, **etki alanı** değerini diğer kimlik sağlayıcılarından ayırt etmek için kullanılabilecek benzersiz bir değer olarak güncelleştirin. Örneğin, `Contoso`. `.com`Bu etki alanı ayarının sonuna bir yerleştirmezsiniz.
5. **ClaimsProvider** öğesi altında, **DisplayName** için değeri, talep sağlayıcı için kolay bir ad olarak güncelleştirin. Bu değer şu anda kullanılmıyor.

### <a name="update-the-technical-profile"></a>Teknik profili güncelleştirme

Azure AD uç noktasından bir belirteç almak için Azure AD B2C Azure AD ile iletişim kurmak için kullanması gereken protokolleri tanımlamanız gerekir. Bu işlem, **ClaimsProvider**'ın **teknisyen** öğesi içinde yapılır.

1. **Teknisyen** öğesinin kimliğini güncelleştirin. Bu KIMLIK, örneğin, ilkenin diğer bölümlerinden bu teknik profile başvurmak için kullanılır `AADContoso-OpenIdConnect` .
1. **DisplayName** için değeri güncelleştirin. Bu değer, oturum açma ekranınızdaki oturum açma düğmesinde görüntülenir.
1. **Açıklama** değerini güncelleştirin.
1. Azure AD, OpenID Connect protokolünü kullanır, bu nedenle **protokol** değerinin olduğundan emin olun `OpenIdConnect` .
1. **Meta verilerin** değerini olarak ayarlayın `https://login.microsoftonline.com/tenant-name.onmicrosoft.com/v2.0/.well-known/openid-configuration` , burada `tenant-name` Azure AD kiracı adınız. Örneğin, `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`
1. Uygulama kaydından uygulama KIMLIĞINE **client_id** ayarlayın.
1. **Cryptographickeys** altında, **Storagereferenceıd** değerini daha önce oluşturduğunuz ilke anahtarının adı olarak güncelleştirin. Örneğin, `B2C_1A_ContosoAppSecret`.


[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADContosoExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="AzureADContosoExchange" TechnicalProfileReferenceId="AADContoso-OpenIdConnect" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>Özel ilkenizi test etme

1. Örneğin, bağlı olan taraf ilkenizi seçin `B2C_1A_signup_signin` .
1. **Uygulama** için, [daha önce kaydetmiş](tutorial-register-applications.md)olduğunuz bir Web uygulamasını seçin. **Yanıt URL 'si** gösterilmesi gerekir `https://jwt.ms` .
1. **Şimdi Çalıştır** düğmesini seçin.
1. Kaydolma veya oturum açma sayfasından Azure AD contoso hesabıyla oturum açmak için **contoso çalışanı** ' nı seçin.

Oturum açma işlemi başarılı olursa, tarayıcınız öğesine yönlendirilir `https://jwt.ms` ve bu, Azure AD B2C tarafından döndürülen belirtecin içeriğini görüntüler.

## <a name="next-steps"></a>Sonraki adımlar

Özel ilkelerle çalışırken, bazen bir ilke geliştirme sırasında sorun giderirken ek bilgilere ihtiyaç duyabilirsiniz.

Sorunları tanılamaya yardımcı olmak için ilkeyi geçici olarak "geliştirici moduna" koyabilirsiniz ve Azure Application Insights ile Günlükler toplayabilirsiniz. [Azure Active Directory B2C: günlükleri toplama](troubleshoot-with-application-insights.md)hakkında bilgi edinin.

::: zone-end
