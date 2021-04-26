---
title: Microsoft hesabı ile kaydolma ve oturum açma ayarlama
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C kullanarak uygulamalarınızda Microsoft hesaplarıyla oturum açma ve oturum açma sağlayın.
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
ms.openlocfilehash: 60a846d72c1760c7f9dddac891f36e834b8364f3
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107028171"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>Microsoft hesabı Azure Active Directory B2C kullanarak kaydolma ve oturum açma ayarlama

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-a-microsoft-account-application"></a>Microsoft hesabı uygulaması oluşturma

Azure Active Directory B2C (Azure AD B2C) Microsoft hesabı olan kullanıcılar için oturum açmayı etkinleştirmek üzere, [Azure Portal](https://portal.azure.com)içinde bir uygulama oluşturmanız gerekir. Daha fazla bilgi için bkz. [Microsoft Identity platformu ile uygulama kaydetme](../active-directory/develop/quickstart-register-app.md). Zaten bir Microsoft hesabı yoksa bir tane edinebilirsiniz [https://www.live.com/](https://www.live.com/) .

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Üst menüdeki **Dizin + abonelik** filtresini SEÇIP Azure AD kiracınızı içeren dizini seçerek Azure AD kiracınızı içeren dizini kullandığınızdan emin olun.
1. Azure portal sol üst köşesindeki **tüm hizmetler** ' i seçin ve ardından **uygulama kayıtları**' i arayıp seçin.
1. **Yeni kayıt** seçeneğini belirleyin.
1. Uygulamanız için bir **ad** girin. Örneğin, *MSAapp1*.
1. **Desteklenen hesap türleri** altında **herhangi bir kuruluş dizininde (HERHANGI bir Azure ad dizini-çoklu kiracı) ve kişisel Microsoft hesaplarından (örn. Skype, Xbox) hesaplar**' ı seçin.

   Farklı hesap türü seçimleri hakkında daha fazla bilgi için bkz. [hızlı başlangıç: Microsoft Identity platformu ile uygulama kaydetme](../active-directory/develop/quickstart-register-app.md).
1. **Yeniden yönlendirme URI 'si (isteğe bağlı)** altında **Web** ' i seçin ve girin `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` . [Özel bir etki alanı](custom-domain.md)kullanıyorsanız, girin `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp` . `your-tenant-name`, Kiracınızın adıyla ve `your-domain-name` özel etki alanınız ile değiştirin.
1. **Kaydol** ' u seçin
1. Uygulamaya Genel Bakış sayfasında gösterilen **uygulama (istemci) kimliğini** kaydedin. Sonraki bölümde kimlik sağlayıcısını yapılandırırken istemci KIMLIĞI gerekir.
1. **Sertifikaları & parolaları** seçin
1. **Yeni istemci gizli dizisine** tıklayın.
1. Gizli dizi için bir **Açıklama** girin, örneğin, *uygulama parolası 1* ve ardından **Ekle**' ye tıklayın.
1. **Değer** sütununda gösterilen uygulama parolasını kaydedin. Bir sonraki bölümde kimlik sağlayıcısını yapılandırırken istemci parolası gerekir.

::: zone pivot="b2c-user-flow"

## <a name="configure-microsoft-as-an-identity-provider"></a>Microsoft 'ı kimlik sağlayıcısı olarak yapılandırma

1. [Azure portalda](https://portal.azure.com/) Azure AD B2C kiracınızın genel yöneticisi olarak oturum açın.
1. Üst menüdeki **Dizin + abonelik** filtresini seçip kiracınızı içeren dizini seçerek Azure AD B2C kiracınızı içeren dizini kullandığınızdan emin olun.
1. Azure portalın sol üst köşesinde **Tüm hizmetler**’i seçin ve **Azure AD B2C**’yi arayıp seçin.
1. **Kimlik sağlayıcıları**' nı seçin ve ardından **Microsoft hesabı**' nı seçin.
1. Bir **ad** girin. Örneğin, *MSA*.
1. **ISTEMCI kimliği** için, daha önce oluşturduğunuz Azure AD uygulamasının uygulama (ISTEMCI) kimliğini girin.
1. **İstemci parolası** için, kaydettiğiniz istemci gizli anahtarını girin.
1. **Kaydet**’i seçin.

## <a name="add-microsoft-identity-provider-to-a-user-flow"></a>Bir Kullanıcı akışına Microsoft Identity Provider ekleme 

Bu noktada, Microsoft Identity Provider ayarlanmıştır, ancak oturum açma sayfalarında henüz mevcut değildir. Bir Kullanıcı akışına Microsoft Identity Provider eklemek için:

1. Azure AD B2C kiracınızda **Kullanıcı akışları**' nı seçin.
1. Microsoft Identity Provider 'ı eklemek istediğiniz kullanıcı akışına tıklayın.
1. **Sosyal kimlik sağlayıcıları** altında **Microsoft hesabı**' nı seçin.
1. **Kaydet**’i seçin.
1. İlkenizi test etmek için **Kullanıcı akışını Çalıştır**' ı seçin.
1. **Uygulama** için, daha önce kaydettiğiniz *testapp1* adlı Web uygulamasını seçin. **Yanıt URL 'si** gösterilmesi gerekir `https://jwt.ms` .
1. **Kullanıcı akışını Çalıştır** düğmesini seçin.
1. Kaydolma veya oturum açma sayfasından **Microsoft** 'u seçerek Microsoft hesabı oturum açın.

Oturum açma işlemi başarılı olursa, tarayıcınız öğesine yönlendirilir `https://jwt.ms` ve bu, Azure AD B2C tarafından döndürülen belirtecin içeriğini görüntüler.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="configuring-optional-claims"></a>İsteğe bağlı talepler yapılandırılıyor

`family_name` `given_name` Azure AD 'den ve taleplerini almak istiyorsanız, Azure Portal Kullanıcı arabirimi veya uygulama bildiriminde uygulamanız için isteğe bağlı talepler yapılandırabilirsiniz. Daha fazla bilgi için bkz. [Azure AD uygulamanıza isteğe bağlı talepler sağlama](../active-directory/develop/active-directory-optional-claims.md).

1. [Azure portalında](https://portal.azure.com) oturum açın. **Azure Active Directory**'yi bulun ve seçin.
1. **Yönet** bölümünden **uygulama kayıtları**' yi seçin.
1. Listede için isteğe bağlı talepler yapılandırmak istediğiniz uygulamayı seçin.
1. **Yönet** bölümünde **belirteç yapılandırması (Önizleme)** öğesini seçin.
1. **İsteğe bağlı talep Ekle**' yi seçin.
1. Yapılandırmak istediğiniz belirteç türünü seçin.
1. Eklenecek isteğe bağlı talepler ' i seçin.
1. **Ekle**'ye tıklayın.

## <a name="create-a-policy-key"></a>İlke anahtarı oluşturma

Uygulamayı Azure AD kiracınızda oluşturduğunuza göre, bu uygulamanın istemci gizliliğini Azure AD B2C kiracınızda depolamanız gerekir.

1. [Azure portalında](https://portal.azure.com/) oturum açın.
1. Azure AD B2C kiracınızı içeren dizini kullandığınızdan emin olun. Üstteki menüden **Dizin + abonelik** filtresini seçin ve kiracınızı içeren dizini seçin.
1. Azure portal sol üst köşesindeki **tüm hizmetler** ' i seçin ve ardından **Azure AD B2C**' i arayıp seçin.
1. Genel Bakış sayfasında **kimlik deneyimi çerçevesi**' ni seçin.
1. **Ilke anahtarlarını** seçin ve ardından **Ekle**' yi seçin.
1. **Seçenekler** için öğesini seçin `Manual` .
1. İlke anahtarı için bir **ad** girin. Örneğin, `MSASecret`. Ön ek, `B2C_1A_` anahtarınızın adına otomatik olarak eklenir.
1. **Gizli** dizi ' da, önceki bölümde kaydettiğiniz istemci gizli anahtarını girin.
1. **Anahtar kullanımı** için öğesini seçin `Signature` .
1. **Oluştur**’a tıklayın.

## <a name="configure-microsoft-as-an-identity-provider"></a>Microsoft 'ı kimlik sağlayıcısı olarak yapılandırma

Kullanıcıların bir Microsoft hesabı kullanarak oturum açmasını sağlamak için hesabı, Azure AD B2C bir uç nokta aracılığıyla iletişim kurabildiği bir talep sağlayıcı olarak tanımlamanız gerekir. Uç noktası, belirli bir kullanıcının kimliği doğrulandığını doğrulamak için Azure AD B2C tarafından kullanılan bir talep kümesi sağlar.

Azure AD 'yi, ilkenizin uzantı dosyasına **ClaimsProvider** öğesini ekleyerek bir talep sağlayıcı olarak tanımlayabilirsiniz.

1. *TrustFrameworkExtensions.xml* ilke dosyasını açın.
1. **Claimsproviders** öğesini bulun. Yoksa, kök öğenin altına ekleyin.
1. Yeni bir **ClaimsProvider** 'ı aşağıdaki şekilde ekleyin:

    ```xml
    <ClaimsProvider>
      <Domain>live.com</Domain>
      <DisplayName>Microsoft Account</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="MSA-MicrosoftAccount-OpenIdConnect">
          <DisplayName>Microsoft Account</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <Item Key="ProviderName">https://login.live.com</Item>
            <Item Key="METADATA">https://login.live.com/.well-known/openid-configuration</Item>
            <Item Key="response_types">code</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="scope">openid profile email</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="client_id">Your Microsoft application client ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_MSASecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="oid" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
            <OutputClaim ClaimTypeReferenceId="email" />
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

1. **Client_id** değerini, daha önce KAYDETTIĞINIZ Azure AD uygulamasının *uygulama (istemci) kimliğiyle* değiştirin.
1. Dosyayı kaydedin.

Artık ilkenizi, Azure AD 'de Microsoft hesabı uygulamanızla nasıl iletişim kuracağını Azure AD B2C bilmesini sağlayacak şekilde yapılandırdınız.

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="MicrosoftAccountExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="MicrosoftAccountExchange" TechnicalProfileReferenceId="MSA-MicrosoftAccount-OpenIdConnect" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>Özel ilkenizi test etme

1. Örneğin, bağlı olan taraf ilkenizi seçin `B2C_1A_signup_signin` .
1. **Uygulama** için, [daha önce kaydetmiş](tutorial-register-applications.md)olduğunuz bir Web uygulamasını seçin. **Yanıt URL 'si** gösterilmesi gerekir `https://jwt.ms` .
1. **Şimdi Çalıştır** düğmesini seçin.
1. Kaydolma veya oturum açma sayfasından **Microsoft** 'u seçerek Microsoft hesabı oturum açın.

Oturum açma işlemi başarılı olursa, tarayıcınız öğesine yönlendirilir `https://jwt.ms` ve bu, Azure AD B2C tarafından döndürülen belirtecin içeriğini görüntüler.

::: zone-end
