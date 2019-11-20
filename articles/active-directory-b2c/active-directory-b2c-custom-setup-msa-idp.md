---
title: Azure Active Directory B2C özel ilkeleri kullanarak Microsoft hesabı 'nı (MSA) kimlik sağlayıcısı olarak ekleme
description: OpenID Connect (OıDC) protokolünü kullanarak Microsoft as Identity Provider kullanarak örnek.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 1f068b624b5a8f580f61e9eb2ed0d197f05aa1b0
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73643654"
---
# <a name="set-up-sign-in-with-a-microsoft-account-using-custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C içindeki özel ilkeleri kullanarak Microsoft hesabı oturum açma ayarlama

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Bu makalede, Azure Active Directory B2C (Azure AD B2C) [özel ilkelerini](active-directory-b2c-overview-custom.md) kullanarak kullanıcıların Microsoft hesabı oturum açma özelliğini nasıl etkinleştireceğinizi gösterilmektedir.

## <a name="prerequisites"></a>Ön koşullar

- [Azure Active Directory B2C özel ilkeleri kullanmaya başlama](active-directory-b2c-get-started-custom.md)bölümündeki adımları uygulayın.
- Zaten bir Microsoft hesabı yoksa, [https://www.live.com/](https://www.live.com/)bir tane oluşturun.

## <a name="add-an-application"></a>Uygulama ekleme

Microsoft hesabı kullanıcıların oturum açma özelliğini etkinleştirmek için Azure AD kiracısı içinde bir uygulamayı kaydetmeniz gerekir. Azure AD kiracısı Azure AD B2C kiracınızla aynı değildir.

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Üst menüdeki **Dizin + abonelik** filtresini SEÇIP Azure AD kiracınızı içeren dizini seçerek Azure AD kiracınızı içeren dizini kullandığınızdan emin olun.
1. Azure portal sol üst köşesindeki **tüm hizmetler** ' i seçin ve ardından **uygulama kayıtları**' i arayıp seçin.
1. **Yeni kayıt**seçeneğini belirleyin.
1. Uygulamanız için bir **ad** girin. Örneğin, *MSAapp1*.
1. **Desteklenen hesap türleri**altında, **herhangi bir kurumsal dizin ve kişisel Microsoft hesabında (ör. Skype, Xbox, Outlook.com) hesaplar**' ı seçin.
1. **Yeniden yönlendirme URI 'si (isteğe bağlı)** altında **Web** ' i seçin ve metin kutusuna `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` girin. `your-tenant-name` Azure AD B2C kiracı adınızla değiştirin.
1. **Kaydol** ' u seçin
1. Uygulamaya Genel Bakış sayfasında gösterilen **uygulama (istemci) kimliğini** kaydedin. Daha sonraki bir bölümde talep sağlayıcıyı yapılandırırken buna ihtiyacınız vardır.
1. **Sertifikaları & parolaları** seçin
1. **Yeni istemci parolası** ' na tıklayın
1. Gizli anahtar için bir **Açıklama** girin, örneğin *MSA uygulama istemci parolası*ve ardından **Ekle**' ye tıklayın.
1. **Değer** sütununda gösterilen uygulama parolasını kaydedin. Sonraki bölümde bu değeri kullanırsınız.

## <a name="create-a-policy-key"></a>İlke anahtarı oluşturma

Uygulamayı Azure AD kiracınızda oluşturduğunuza göre, bu uygulamanın istemci gizliliğini Azure AD B2C kiracınızda depolamanız gerekir.

1. [Azure portalında](https://portal.azure.com/) oturum açın.
1. Azure AD B2C kiracınızı içeren dizini kullandığınızdan emin olun. Üstteki menüden **Dizin + abonelik** filtresini seçin ve kiracınızı içeren dizini seçin.
1. Azure portal sol üst köşesindeki **tüm hizmetler** ' i seçin ve ardından **Azure AD B2C**' i arayıp seçin.
1. Genel Bakış sayfasında **kimlik deneyimi çerçevesi**' ni seçin.
1. **Ilke anahtarlarını** seçin ve ardından **Ekle**' yi seçin.
1. **Seçenekler**için `Manual`seçin.
1. İlke anahtarı için bir **ad** girin. Örneğin, `MSASecret`. `B2C_1A_` ön eki, anahtarınızın adına otomatik olarak eklenir.
1. **Gizli**dizi ' da, önceki bölümde kaydettiğiniz istemci gizli anahtarını girin.
1. **Anahtar kullanımı**için `Signature`' yi seçin.
1. **Oluştur**'a tıklayın.

## <a name="add-a-claims-provider"></a>Talep sağlayıcısı ekleme

Kullanıcılarınızın bir Microsoft hesabı kullanarak oturum açmasını sağlamak için hesabı, Azure AD B2C bir uç nokta aracılığıyla iletişim kurabildiği bir talep sağlayıcı olarak tanımlamanız gerekir. Uç noktası, belirli bir kullanıcının kimliği doğrulandığını doğrulamak için Azure AD B2C tarafından kullanılan bir talep kümesi sağlar.

Azure AD 'yi, ilkenizin uzantı dosyasına **ClaimsProvider** öğesini ekleyerek bir talep sağlayıcı olarak tanımlayabilirsiniz.

1. *TrustFrameworkExtensions. xml* ilke dosyasını açın.
1. **Claimsproviders** öğesini bulun. Yoksa, kök öğenin altına ekleyin.
1. Yeni bir **ClaimsProvider** 'ı aşağıdaki şekilde ekleyin:

    ```xml
    <ClaimsProvider>
      <Domain>live.com</Domain>
      <DisplayName>Microsoft Account</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="MSA-OIDC">
          <DisplayName>Microsoft Account</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <Item Key="ProviderName">https://login.live.com</Item>
            <Item Key="METADATA">https://login.live.com/.well-known/openid-configuration</Item>
            <Item Key="response_types">code</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="scope">openid profile email</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <Item Key="client_id">Your Microsoft application client ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_MSASecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="live.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
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

### <a name="upload-the-extension-file-for-verification"></a>Uzantı dosyasını doğrulama için karşıya yükle

Devam etmeden önce, şu ana kadar herhangi bir sorun olmadığını doğrulamak için değiştirilen ilkeyi karşıya yükleyin.

1. Azure portal Azure AD B2C kiracınıza giderek **kimlik deneyimi çerçevesi**' ni seçin.
1. **Özel ilkeler** sayfasında, **özel ilkeyi karşıya yükle**' yi seçin.
1. Varsa **Ilkenin üzerine yazmayı**etkinleştirin ve ardından *TrustFrameworkExtensions. xml* dosyasına gidip seçin.
1. **Karşıya Yükle**'ye tıklayın.

Portalda herhangi bir hata görüntülenmiyorsa, sonraki bölüme geçin.

## <a name="register-the-claims-provider"></a>Talep sağlayıcısını Kaydet

Bu noktada, kimlik sağlayıcısını ayarlamış olursunuz, ancak kaydolma veya oturum açma ekranlarından hiçbirinde henüz kullanılabilir değildir. Kullanılabilir hale getirmek için, var olan bir şablon Kullanıcı yolculuğu oluşturun ve ardından Microsoft hesabı kimlik sağlayıcısına da sahip olacak şekilde değiştirin.

1. *TrustFrameworkBase. xml* dosyasını başlangıç paketinden açın.
1. `Id="SignUpOrSignIn"`içeren **Useryolculuney** öğesinin tüm içeriğini bulup kopyalayın.
1. *TrustFrameworkExtensions. xml* ' i açın ve **User, neys** öğesini bulun. Öğe yoksa, bir tane ekleyin.
1. **User, neys** öğesinin bir alt öğesi olarak kopyaladığınız **User, ney** öğesinin tüm içeriğini yapıştırın.
1. Kullanıcı yolculuğunun KIMLIĞINI yeniden adlandırın. Örneğin, `SignUpSignInMSA`.

### <a name="display-the-button"></a>Düğmeyi görüntüleme

**Claimsproviderselection** öğesi, kaydolma veya oturum açma ekranındaki bir kimlik sağlayıcısı düğmesine benzer. Bir Microsoft hesabı için bir **Claimsproviderselection** öğesi eklerseniz, bir Kullanıcı sayfada yer alıyorsa yeni bir düğme görüntülenir.

1. *TrustFrameworkExtensions. xml* dosyasında, oluşturduğunuz Kullanıcı yolculuğunda `Order="1"` Içeren **orchestrationstep** öğesini bulun.
1. **Claimsproviderseçilir**altında aşağıdaki öğeyi ekleyin. **Targetclaimsexchangeıd** değerini uygun bir değere ayarlayın, örneğin `MicrosoftAccountExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="MicrosoftAccountExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Düğmeyi bir eyleme bağlama

Artık bir düğmeye sahip olduğunuza göre, bunu bir eyleme bağlamanız gerekir. Bu durumda, Azure AD B2C bir belirteci almak için Microsoft hesabı ile iletişim kurmak için kullanılır.

1. Kullanıcı yolculuğunda `Order="2"` içeren **Orchestrationstep** öğesini bulun.
1. **Targetclaimsexchangeıd**IÇIN kullandığınız kimlik için aynı değeri kullandığınızdan emin olmak Için aşağıdaki **claimsexchange** öğesini ekleyin:

    ```xml
    <ClaimsExchange Id="MicrosoftAccountExchange" TechnicalProfileReferenceId="MSA-OIDC" />
    ```

    **TechnicalProfileReferenceId** değerini, daha önce eklediğiniz talep sağlayıcısının **teknisyen** öğesindeki `Id` değeri ile eşleşecek şekilde güncelleştirin. Örneğin, `MSA-OIDC`.

1. *TrustFrameworkExtensions. xml* dosyasını kaydedin ve doğrulama için yeniden yükleyin.

## <a name="create-an-azure-ad-b2c-application"></a>Azure AD B2C uygulaması oluşturma

Azure AD B2C ile iletişim, B2C kiracınıza kaydolmanızı sağlayan bir uygulama aracılığıyla oluşur. Bu bölümde, daha önce yapmadıysanız bir test uygulaması oluşturmak için tamamlayabildiğiniz isteğe bağlı adımlar listelenmektedir.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>Bağlı olan taraf dosyasını güncelleştirme ve test etme

Oluşturduğunuz Kullanıcı yolculuğunu başlatan bağlı olan taraf (RP) dosyasını güncelleştirin.

1. Çalışma dizininizde *Signuporsignın. xml* ' in bir kopyasını oluşturun ve yeniden adlandırın. Örneğin, bunu *Signupsignınmsa. xml*olarak yeniden adlandırın.
1. Yeni dosyayı açın ve **TrustFrameworkPolicy** Için **PolicyId** özniteliğinin değerini benzersiz bir değerle güncelleştirin. Örneğin, `SignUpSignInMSA`.
1. **Publicpolicyuri** DEĞERINI ilke URI 'siyle güncelleştirin. Örneğin,`http://contoso.com/B2C_1A_signup_signin_msa`
1. **Defaultuseryolculuney** Içindeki **referenceıd** özniteliğinin değerini, daha önce oluşturduğunuz Kullanıcı yolculuğunun kimliğiyle eşleşecek şekilde güncelleştirin (Signupsignınmsa).
1. Değişikliklerinizi kaydedin, dosyayı karşıya yükleyin ve ardından listeden yeni ilkeyi seçin.
1. Önceki bölümde oluşturduğunuz Azure AD B2C uygulamasının (veya önkoşulları tamamlayarak, örneğin *WebApp1* veya *Testapp1*) **Uygulama Seç** alanında seçili olduğundan emin olun ve ardından **Şimdi Çalıştır ' a tıklayarak test edin** .
1. **Microsoft hesabı** düğmesini seçin ve oturum açın.

    Oturum açma işlemi başarılı olursa, kodu çözülen belirteci görüntüleyen ve şuna benzeyen `jwt.ms` yönlendirilirsiniz:

    ```json
    {
      "typ": "JWT",
      "alg": "RS256",
      "kid": "<key-ID>"
    }.{
      "exp": 1562365200,
      "nbf": 1562361600,
      "ver": "1.0",
      "iss": "https://your-b2c-tenant.b2clogin.com/10000000-0000-0000-0000-000000000000/v2.0/",
      "sub": "20000000-0000-0000-0000-000000000000",
      "aud": "30000000-0000-0000-0000-000000000000",
      "acr": "b2c_1a_signupsigninmsa",
      "nonce": "defaultNonce",
      "iat": 1562361600,
      "auth_time": 1562361600,
      "idp": "live.com",
      "name": "Azure User",
      "email": "azureuser@contoso.com",
      "tid": "6fc3b573-7b38-4c0c-b627-2e8684f6c575"
    }.[Signature]
    ```
