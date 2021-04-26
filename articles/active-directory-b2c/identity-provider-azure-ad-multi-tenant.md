---
title: Özel ilkeler tarafından çok kiracılı Azure AD için oturum açma ayarla
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C özel ilkeleri kullanarak çok kiracılı bir Azure AD kimlik sağlayıcısı ekleyin.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/15/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 9d9ba7e3e898da8d5b7d1bb4fbc69554ee205147
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107028345"
---
# <a name="set-up-sign-in-for-multi-tenant-azure-active-directory-using-custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C içindeki özel ilkeleri kullanarak çok kiracılı Azure Active Directory için oturum açma ayarlayın

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

Bu makalede, Azure Active Directory (Azure AD) için çoklu kiracı uç noktasını kullanan kullanıcılar için oturum açma 'nın nasıl etkinleştirileceği gösterilmektedir. Birden çok Azure AD kiracısından kullanıcıların, her kiracı için bir kimlik sağlayıcısı yapılandırmaya gerek kalmadan Azure AD B2C kullanarak oturum açmasına izin verme. Ancak, bu kiracılardan herhangi birinde bulunan konuk üyeleri **oturum açamaz.** Bu şekilde, [her bir kiracıyı tek tek yapılandırmanız](identity-provider-azure-ad-single-tenant.md)gerekir.

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="register-an-application"></a>Bir uygulamayı kaydetme

Azure Active Directory B2C (Azure AD B2C) ' de bir Azure AD hesabı olan kullanıcılar için oturum açmayı etkinleştirmek üzere, [Azure Portal](https://portal.azure.com)'de bir uygulama oluşturmanız gerekir. Daha fazla bilgi için bkz. [Microsoft Identity platformu ile uygulama kaydetme](../active-directory/develop/quickstart-register-app.md).


1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Kuruluşunuzun Azure AD kiracınızı içeren dizini kullandığınızdan emin olun (örneğin, contoso.com). Üst menüden **Dizin + abonelik filtresi** ' ni seçin ve ardından kiracınızı içeren dizini seçin.
1. Azure portal sol üst köşesindeki **tüm hizmetler** ' i seçin ve ardından **uygulama kayıtları**' i arayıp seçin.
1. **Yeni kayıt** seçeneğini belirleyin.
1. Uygulamanız için bir **ad** girin. Örneğin, `Azure AD B2C App`.
1. Bu uygulama için **herhangi bir kuruluş dizininde (herhangi bir Azure ad dizini – Multitenant) hesaplar '** ı seçin.
1. **Yeniden yönlendirme URI 'si** Için, **Web**'in değerini kabul edin ve aşağıdaki URL 'yi tüm küçük harflerle, burada `your-B2C-tenant-name` Azure AD B2C kiracınızın adıyla birlikte girin.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Örneğin, `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`.

    [Özel bir etki alanı](custom-domain.md)kullanıyorsanız, girin `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp` . `your-domain-name`Özel etki alanınız ile ve `your-tenant-name` kiracınızın adıyla değiştirin.

1. **Kaydet**’i seçin. Daha sonraki bir adımda kullanmak üzere **uygulama (istemci) kimliğini** kaydedin.
1. **Sertifikalar & sertifikalar**' ı seçin ve ardından **yeni istemci parolası**' nı seçin.
1. Gizli dizi için bir **Açıklama** girin, bir süre sonu seçin ve ardından **Ekle**' yi seçin. Daha sonraki bir adımda kullanmak için gizli dizi **değerini** kaydedin.

## <a name="configuring-optional-claims"></a>İsteğe bağlı talepler yapılandırılıyor

`family_name` `given_name` Azure AD 'den ve taleplerini almak isterseniz, uygulamanız için isteğe bağlı talepler Azure Portal Kullanıcı arabirimi veya uygulama bildiriminde yapılandırabilirsiniz. Daha fazla bilgi için bkz. [Azure AD uygulamanıza isteğe bağlı talepler sağlama](../active-directory/develop/active-directory-optional-claims.md).

1. [Azure portalında](https://portal.azure.com) oturum açın. **Azure Active Directory**'yi bulun ve seçin.
1. **Yönet** bölümünden **uygulama kayıtları**' yi seçin.
1. Listede için isteğe bağlı talepler yapılandırmak istediğiniz uygulamayı seçin.
1. **Yönet** bölümünde **belirteç yapılandırması**' nı seçin.
1. **İsteğe bağlı talep Ekle**' yi seçin.
1. **Belirteç türü** için **kimlik**' i seçin.
1. , Ve eklemek için isteğe bağlı talepler ' i seçin `family_name` `given_name` .
1. **Ekle**'ye tıklayın.

## <a name="create-a-policy-key"></a>İlke anahtarı oluşturma

Azure AD B2C kiracınızda oluşturduğunuz uygulama anahtarını depolamanız gerekir.

1. Azure AD B2C kiracınızı içeren dizini kullandığınızdan emin olun. Üst menüden **Dizin + abonelik filtresi** ' ni seçin ve ardından Azure AD B2C kiracınızı içeren dizini seçin.
1. Azure portal sol üst köşesindeki **tüm hizmetler** ' i seçin ve ardından **Azure AD B2C**' i arayıp seçin.
1. **İlkeler** altında **kimlik deneyimi çerçevesi**' ni seçin.
1. **İlke anahtarlarını** seçin ve ardından **Ekle**' yi seçin.
1. **Seçenekler** için öğesini seçin `Manual` .
1. İlke anahtarı için bir **ad** girin. Örneğin, `AADAppSecret`.  Ön ek, `B2C_1A_` oluşturulduğu sırada anahtarınızın adına otomatik olarak eklenir, bu nedenle aşağıdaki BÖLÜMDEKI XML başvurusu *B2C_1A_AADAppSecret*.
1. **Gizli**, daha önce kaydettiğiniz istemci gizli anahtarını girin.
1. **Anahtar kullanımı** için öğesini seçin `Signature` .
1. **Oluştur**’u seçin.

## <a name="configure-azure-ad-as-an-identity-provider"></a>Azure AD 'yi kimlik sağlayıcısı olarak yapılandırma

Kullanıcıların bir Azure AD hesabı kullanarak oturum açmasını sağlamak için Azure AD 'yi Azure AD B2C bir uç nokta aracılığıyla iletişim kurabildiği bir talep sağlayıcı olarak tanımlamanız gerekir. Uç noktası, belirli bir kullanıcının kimliği doğrulandığını doğrulamak için Azure AD B2C tarafından kullanılan bir talep kümesi sağlar.

Azure AD 'yi, ilkenizin uzantısı dosyasındaki **ClaimsProvider** öğesine ekleyerek bir talep sağlayıcı olarak tanımlayabilirsiniz.

1. *TrustFrameworkExtensions.xml* dosyasını açın.
1. **Claimsproviders** öğesini bulun. Yoksa, kök öğenin altına ekleyin.
1. Yeni bir **ClaimsProvider** 'ı aşağıdaki şekilde ekleyin:

    ```xml
    <ClaimsProvider>
      <Domain>commonaad</Domain>
      <DisplayName>Common AAD</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AADCommon-OpenIdConnect">
          <DisplayName>Multi-Tenant AAD</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="OpenIdConnect"/>
          <Metadata>
            <Item Key="METADATA">https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration</Item>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid profile</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="DiscoverMetadataByTokenIssuer">true</Item>
            <!-- The key below allows you to specify each of the Azure AD tenants that can be used to sign in. Update the GUIDs below for each tenant. -->
            <Item Key="ValidTokenIssuerPrefixes">https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000,https://login.microsoftonline.com/11111111-1111-1111-1111-111111111111</Item>
            <!-- The commented key below specifies that users from any tenant can sign-in. Uncomment if you would like anyone with an Azure AD account to be able to sign in. -->
            <!-- <Item Key="ValidTokenIssuerPrefixes">https://login.microsoftonline.com/</Item> -->
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_AADAppSecret"/>
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

1. **ClaimsProvider** öğesi altında, **etki alanı** değerini diğer kimlik sağlayıcılarından ayırt etmek için kullanılabilecek benzersiz bir değer olarak güncelleştirin.
1. **Teknisyen** öğesinin altında, **DisplayName** için değeri (örneğin,) güncelleştirin `Contoso Employee` . Bu değer, oturum açma sayfanızda oturum açma düğmesinde görüntülenir.
1. Daha önce kaydettiğiniz Azure AD çok kiracılı uygulamasının uygulama KIMLIĞINE **client_id** ayarlayın.
1. **Cryptographickeys** altında, **Storagereferenceıd** değerini daha önce oluşturulmuş ilke anahtarının adı olarak güncelleştirin. Örneğin, `B2C_1A_AADAppSecret`.

### <a name="restrict-access"></a>Erişimi kısıtlama

`https://login.microsoftonline.com/` **Validtokenıssueröneklerini** değer olarak kullanmak tüm Azure AD kullanıcılarının uygulamanızda oturum açmasını sağlar. Geçerli belirteç verenler listesini güncelleştirin ve erişimi, oturum açabilen belirli bir Azure AD kiracı kullanıcıları listesiyle sınırlayın.

Değerleri almak için, kullanıcıların oturum açmasını istediğiniz her bir Azure AD kiracısından oluşan OpenID Connect bulgu meta verilerine bakın. Meta veri URL 'sinin biçimi şuna benzerdir; `https://login.microsoftonline.com/your-tenant/v2.0/.well-known/openid-configuration` burada `your-tenant` Azure AD kiracı adınız bulunur. Örnek:

`https://login.microsoftonline.com/fabrikam.onmicrosoft.com/v2.0/.well-known/openid-configuration`

Oturum açmak için kullanılması gereken her Azure AD kiracısı için aşağıdaki adımları gerçekleştirin:

1. Tarayıcınızı açın ve kiracı için OpenID Connect meta veri URL 'sine gidin. **Veren** nesnesini bulun ve değerini kaydedin. Şuna benzer görünmelidir `https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000/` .
1. Değeri, **Validtokenıssuerönekler** anahtarına kopyalayıp yapıştırın. Birden çok verenler virgülle ayırın. Önceki xml örneğinde iki veren bir örnek görüntülenir `ClaimsProvider` .

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADCommonExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="AzureADCommonExchange" TechnicalProfileReferenceId="AADCommon-OpenIdConnect" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>Özel ilkenizi test etme

1. Örneğin, bağlı olan taraf ilkenizi seçin `B2C_1A_signup_signin` .
1. **Uygulama** için, [daha önce kaydetmiş](tutorial-register-applications.md)olduğunuz bir Web uygulamasını seçin. **Yanıt URL 'si** gösterilmesi gerekir `https://jwt.ms` .
1. **Şimdi Çalıştır** düğmesini seçin.
1. Kaydolma veya oturum açma sayfasından Azure AD hesabıyla oturum açmak için **ortak AAD** ' yi seçin.

Çok kiracılı oturum açma özelliğini test etmek için, başka bir Azure AD kiracısı olan bir kullanıcının kimlik bilgilerini kullanarak son iki adımı gerçekleştirin. **Şimdi Çalıştır uç noktasını** kopyalayın ve özel bir tarayıcı penceresinde açın. Örneğin, Google Chrome 'Da veya Microsoft Edge 'de bir InPrivate penceresinde. Özel bir tarayıcı penceresinde açmak, şu anda önbelleğe alınmış Azure AD kimlik bilgilerini kullanarak tüm Kullanıcı yolculuğunu test etmenize olanak tanır.

Oturum açma işlemi başarılı olursa, tarayıcınız öğesine yönlendirilir `https://jwt.ms` ve bu, Azure AD B2C tarafından döndürülen belirtecin içeriğini görüntüler.

## <a name="next-steps"></a>Sonraki adımlar

Özel ilkelerle çalışırken, bazen bir ilke geliştirme sırasında sorun giderirken ek bilgilere ihtiyaç duyabilirsiniz.

Sorunları tanılamaya yardımcı olmak için ilkeyi geçici olarak "geliştirici moduna" koyabilirsiniz ve Azure Application Insights ile Günlükler toplayabilirsiniz. [Azure Active Directory B2C: günlükleri toplama](troubleshoot-with-application-insights.md)hakkında bilgi edinin.

::: zone-end
