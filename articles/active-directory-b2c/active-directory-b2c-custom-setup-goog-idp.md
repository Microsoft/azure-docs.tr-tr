---
title: Azure Active Directory B2C özel ilkeleri kullanarak bir Google hesabıyla oturum açma ayarlama | Microsoft Docs
description: Özel ilkeler kullanarak Azure Active Directory B2C bir Google hesabıyla oturum açma ayarlayın.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: cc7e7b334791194cd4f8ebbd2038e9c1877eb297
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72240210"
---
# <a name="set-up-sign-in-with-a-google-account-using-custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C özel ilkeleri kullanarak bir Google hesabı ile oturum açmayı ayarlama

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Bu makalede, Azure Active Directory B2C (Azure AD B2C) içinde [özel ilkeler](active-directory-b2c-overview-custom.md) kullanarak Google hesabı olan kullanıcılar için oturum açma 'nın nasıl etkinleştirileceği gösterilmektedir.

## <a name="prerequisites"></a>Prerequisites

- [Active Directory B2C içindeki özel ilkeleri kullanmaya başlama](active-directory-b2c-get-started-custom.md)adımlarını izleyin.
- Zaten bir Google hesabınız yoksa, [Google hesabınızı oluşturma](https://accounts.google.com/SignUp)' da bir tane oluşturun.

## <a name="register-the-application"></a>Uygulamayı kaydetme

Kullanıcıların bir Google hesabından oturum açmasını etkinleştirmek için bir Google Application projesi oluşturmanız gerekir.

1. [Google Developers konsolunda](https://console.developers.google.com/) hesap kimlik bilgilerinizle oturum açın.
2. Bir **Proje adı**girin, **Oluştur**' a tıklayın ve ardından yeni projeyi kullandığınızdan emin olun.
3. Sol menüden **kimlik** bilgileri ' ni seçin ve ardından **Oauth istemci kimliği > kimlik bilgileri oluştur**' u seçin.
4. **Onay ekranını Yapılandır**' ı seçin.
5. Geçerli bir **e-posta adresi**seçin veya belirtin, kullanıcılara gösterilen bir **ürün adı** sağlayın, **yetkili etki alanlarında**`b2clogin.com` girin ve **Kaydet**' e tıklayın.
6. **Uygulama türü**altında **Web uygulaması**' nı seçin.
7. Uygulamanız için bir **ad** girin.
8. **Yetkili JavaScript kaynakları**' nda `https://your-tenant-name.b2clogin.com` girin ve **yetkili yeniden yönlendirme URI 'lerinde**`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` girin. Kiracı adınızı kiracınızın adıyla değiştirin. Kiracı, Azure AD B2C büyük harfle tanımlansa bile kiracı adınızı girerken tüm küçük harfleri kullanmanız gerekir.
8. **Oluştur**'u tıklatın.
9. **ISTEMCI kimliği** ve **istemci parolası**değerlerini kopyalayın. Google 'ı kiracınızda bir kimlik sağlayıcısı olarak yapılandırmak için her ikisine de ihtiyacınız olacak. İstemci parolası önemli bir güvenlik kimlik bilgileridir.

## <a name="create-a-policy-key"></a>İlke anahtarı oluşturma

Daha önce Azure AD B2C kiracınızda kaydettiğiniz istemci gizli anahtarını depolamanız gerekir.

1. [Azure Portal](https://portal.azure.com/)oturum açın.
2. Azure AD B2C kiracınızı içeren dizini kullandığınızdan emin olun. Üstteki menüden **Dizin + abonelik** filtresini seçin ve kiracınızı içeren dizini seçin.
3. Azure portal sol üst köşesindeki **tüm hizmetler** ' i seçin ve ardından **Azure AD B2C**' i arayıp seçin.
4. Genel Bakış sayfasında **kimlik deneyimi çerçevesi**' ni seçin.
5. **Ilke anahtarlarını** seçin ve ardından **Ekle**' yi seçin.
6. **Seçenekler**için `Manual` ' i seçin.
7. İlke anahtarı için bir **ad** girin. Örneğin, `GoogleSecret`. @No__t-0 öneki, anahtarınızın adına otomatik olarak eklenir.
8. **Gizli**, daha önce kaydettiğiniz istemci gizli anahtarını girin.
9. **Anahtar kullanımı**için `Signature` ' i seçin.
10. **Oluştur**'u tıklatın.

## <a name="add-a-claims-provider"></a>Talep sağlayıcısı ekleme

Kullanıcıların bir Google hesabı kullanarak oturum açmasını istiyorsanız, hesabı Azure AD B2C bir uç nokta aracılığıyla iletişim kurabildiği bir talep sağlayıcı olarak tanımlamanız gerekir. Uç noktası, belirli bir kullanıcının kimliği doğrulandığını doğrulamak için Azure AD B2C tarafından kullanılan bir talep kümesi sağlar.

Bir Google hesabını, ilkenizin uzantı dosyasındaki **Claimsproviders** öğesine ekleyerek bir talep sağlayıcı olarak tanımlayabilirsiniz.

1. *TrustFrameworkExtensions. xml*' i açın.
2. **Claimsproviders** öğesini bulun. Yoksa, kök öğenin altına ekleyin.
3. Yeni bir **ClaimsProvider** 'ı aşağıdaki şekilde ekleyin:

    ```xml
    <ClaimsProvider>
      <Domain>google.com</Domain>
      <DisplayName>Google</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Google-OAUTH">
          <DisplayName>Google</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">google</Item>
            <Item Key="authorization_endpoint">https://accounts.google.com/o/oauth2/auth</Item>
            <Item Key="AccessTokenEndpoint">https://accounts.google.com/o/oauth2/token</Item>
            <Item Key="ClaimsEndpoint">https://www.googleapis.com/oauth2/v1/userinfo</Item>
            <Item Key="scope">email profile</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
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

4. Uygulama kaydından **client_id** ÖĞESINI uygulama kimliği olarak ayarlayın.
5. Dosyayı kaydedin.

### <a name="upload-the-extension-file-for-verification"></a>Uzantı dosyasını doğrulama için karşıya yükle

Şimdi, ilkenizi Azure AD dizininizle nasıl iletişim kuracağını öğrenmek için Azure AD B2C ilkenizi yapılandırdınız. Şu ana kadar herhangi bir sorun olmadığını doğrulamak için, ilkenizin uzantı dosyasını karşıya yüklemeyi deneyin.

1. Azure AD B2C kiracınızdaki **özel ilkeler** sayfasında, **ilkeyi karşıya yükle**' yi seçin.
2. Varsa **Ilkenin üzerine yazmayı**etkinleştirin ve ardından *TrustFrameworkExtensions. xml* dosyasına gidip seçin.
3. **Karşıya yükle**' ye tıklayın.

## <a name="register-the-claims-provider"></a>Talep sağlayıcısını Kaydet

Bu noktada, kimlik sağlayıcısı ayarlanmıştır, ancak kaydolma/oturum açma ekranlarından hiçbirinde kullanılamaz. Kullanılabilir hale getirmek için, var olan bir şablon Kullanıcı yolculuğunun bir yinelemesini oluşturun ve ardından Azure AD kimlik sağlayıcısı 'nı da içerecek şekilde değiştirin.

1. *TrustFrameworkBase. xml* dosyasını başlangıç paketinden açın.
2. @No__t-1 içeren **Useryolculuney** öğesinin tüm içeriğini bulup kopyalayın.
3. *TrustFrameworkExtensions. xml* ' i açın ve **User, neys** öğesini bulun. Öğe yoksa, bir tane ekleyin.
4. **User, neys** öğesinin bir alt öğesi olarak kopyaladığınız **User, ney** öğesinin tüm içeriğini yapıştırın.
5. Kullanıcı yolculuğunun KIMLIĞINI yeniden adlandırın. Örneğin, `SignUpSignInGoogle`.

### <a name="display-the-button"></a>Düğmeyi görüntüleme

**Claimsproviderselection** öğesi, kaydolma/oturum açma ekranındaki bir kimlik sağlayıcısı düğmesine benzer. Bir Google hesabı için bir **Claimsproviderselection** öğesi eklerseniz, bir Kullanıcı sayfada yer alıyorsa yeni bir düğme görüntülenir.

1. Oluşturduğunuz Kullanıcı yolculuğunda `Order="1"` içeren **Orchestrationstep** öğesini bulun.
2. **Claimsproviderseçilir**altında aşağıdaki öğeyi ekleyin. **Targetclaimsexchangeıd** değerini uygun bir değere ayarlayın, örneğin `GoogleExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="GoogleExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Düğmeyi bir eyleme bağlama

Artık bir düğmeye sahip olduğunuza göre, bunu bir eyleme bağlamanız gerekir. Bu durumda, bir belirteç almak için bir Google hesabıyla iletişim kurmak Azure AD B2C.

1. Kullanıcı yolculuğunda `Order="2"` içeren bir düzenleyen **Tionstep** bulun.
2. **Targetclaimsexchangeıd**IÇIN kullandığınız ID için aynı değeri kullandığınızdan emin olmak Için aşağıdaki **claimsexchange** öğesini ekleyin:

    ```XML
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAuth" />
    ```

    **TechnicalProfileReferenceId** değerini daha önce oluşturduğunuz teknık profilin kimliğiyle güncelleştirin. Örneğin, `Google-OAuth`.

3. *TrustFrameworkExtensions. xml* dosyasını kaydedin ve doğrulama için yeniden yükleyin.

## <a name="create-an-azure-ad-b2c-application"></a>Azure AD B2C uygulaması oluşturma

Azure AD B2C ile iletişim, B2C kiracınıza kaydolmanızı sağlayan bir uygulama aracılığıyla oluşur. Bu bölümde, daha önce yapmadıysanız bir test uygulaması oluşturmak için tamamlayabildiğiniz isteğe bağlı adımlar listelenmektedir.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>Bağlı olan taraf dosyasını güncelleştirme ve test etme

Oluşturduğunuz Kullanıcı yolculuğunu başlatan bağlı olan taraf (RP) dosyasını güncelleştirin.

1. Çalışma dizininizde *Signuporsignın. xml* ' in bir kopyasını oluşturun ve yeniden adlandırın. Örneğin, bunu *Signupsigningoogle. xml*olarak yeniden adlandırın.
2. Yeni dosyayı açın ve **TrustFrameworkPolicy** Için **PolicyId** özniteliğinin değerini benzersiz bir değerle güncelleştirin. Örneğin, `SignUpSignInGoogle`.
3. **Publicpolicyuri** DEĞERINI ilke URI 'siyle güncelleştirin. Örneğin, `http://contoso.com/B2C_1A_signup_signin_google`
4. **Defaultuseryolculuney** Içindeki **referenceıd** özniteliğinin değerini, oluşturduğunuz yeni Kullanıcı yolculuğu (signupsigngoogle) kimliğiyle eşleşecek şekilde güncelleştirin.
5. Değişikliklerinizi kaydedin, dosyayı karşıya yükleyin ve ardından listeden yeni ilkeyi seçin.
6. Oluşturduğunuz Azure AD B2C uygulamasının **Uygulama Seç** alanında seçildiğinden emin olun ve **Şimdi Çalıştır**' a tıklayarak test edin.
