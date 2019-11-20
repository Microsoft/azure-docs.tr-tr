---
title: Özel ilkeler kullanarak Azure Active Directory B2C Azure Active Directory hesapla oturum açma ayarlama
description: Özel ilkeler kullanarak Azure Active Directory B2C Azure Active Directory hesapla oturum açma ayarlayın.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: a221d55d942e6140c12f2ebfb64428b8ec7be74b
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73643566"
---
# <a name="set-up-sign-in-with-an-azure-active-directory-account-using-custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C içindeki özel ilkeleri kullanarak Azure Active Directory hesabı ile oturum açmayı ayarlama

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Bu makalede, Azure Active Directory B2C (Azure AD B2C) [özel ilkelerini](active-directory-b2c-overview-custom.md) kullanarak bir Azure Active Directory (Azure AD) kuruluştan kullanıcıların oturum açma özelliğini nasıl etkinleştireceğinizi gösterilmektedir.

## <a name="prerequisites"></a>Ön koşullar

[Azure Active Directory B2C özel ilkeleri kullanmaya başlama](active-directory-b2c-get-started-custom.md)bölümündeki adımları uygulayın.

## <a name="register-an-application"></a>Bir uygulamayı kaydetme

Kullanıcıların belirli bir Azure AD kuruluştan oturum açmasını etkinleştirmek için, uygulamayı kurumsal Azure AD kiracısında kaydetmeniz gerekir.

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Kuruluşunuzun Azure AD kiracınızı içeren dizini kullandığınızdan emin olun (örneğin, contoso.com). Üst menüden **Dizin + abonelik filtresi** ' ni seçin ve ardından Azure AD kiracınızı içeren dizini seçin.
1. Azure portal sol üst köşesindeki **tüm hizmetler** ' i seçin ve ardından **uygulama kayıtları**' i arayıp seçin.
1. **Yeni kayıt**seçeneğini belirleyin.
1. Uygulamanız için bir **ad** girin. Örneğin, `Azure AD B2C App`.
1. Bu **kuruluş dizinindeki hesapların** varsayılan seçimini yalnızca bu uygulama için kabul edin.
1. **Yeniden yönlendirme URI 'si**Için, **Web**'in değerini kabul edin ve aşağıdaki URL 'yi tüm küçük harflerle girin; burada `your-B2C-tenant-name` Azure AD B2C kiracınızın adı ile değiştirilmiştir.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Örneğin, `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.

1. **Kaydol**’u seçin. Daha sonraki bir adımda kullanmak üzere **uygulama (istemci) kimliğini** kaydedin.
1. **Sertifikalar & sertifikalar**' ı seçin ve ardından **yeni istemci parolası**' nı seçin.
1. Gizli dizi için bir **Açıklama** girin, bir süre sonu seçin ve ardından **Ekle**' yi seçin. Daha sonraki bir adımda kullanmak için gizli dizi **değerini** kaydedin.

## <a name="create-a-policy-key"></a>İlke anahtarı oluşturma

Azure AD B2C kiracınızda oluşturduğunuz uygulama anahtarını depolamanız gerekir.

1. Azure AD B2C kiracınızı içeren dizini kullandığınızdan emin olun. Üst menüden **Dizin + abonelik filtresi** ' ni seçin ve ardından Azure AD B2C kiracınızı içeren dizini seçin.
1. Azure portal sol üst köşesindeki **tüm hizmetler** ' i seçin ve ardından **Azure AD B2C**' i arayıp seçin.
1. **İlkeler**altında **kimlik deneyimi çerçevesi**' ni seçin.
1. **İlke anahtarlarını** seçin ve ardından **Ekle**' yi seçin.
1. **Seçenekler**için `Manual`seçin.
1. İlke anahtarı için bir **ad** girin. Örneğin, `ContosoAppSecret`.  Ön ek `B2C_1A_`, oluşturulduğu sırada anahtarınızın adına otomatik olarak eklenir, bu nedenle aşağıdaki bölümdeki XML başvurusu *B2C_1A_ContosoAppSecret*' dir.
1. **Gizli**, daha önce kaydettiğiniz istemci gizli anahtarını girin.
1. **Anahtar kullanımı**için `Signature`' yi seçin.
1. **Oluştur**'u seçin.

## <a name="add-a-claims-provider"></a>Talep sağlayıcısı ekleme

Kullanıcıların Azure AD 'yi kullanarak oturum açmasını istiyorsanız, Azure AD 'yi Azure AD B2C bir uç nokta aracılığıyla iletişim kurabildiği bir talep sağlayıcı olarak tanımlamanız gerekir. Uç noktası, belirli bir kullanıcının kimliği doğrulandığını doğrulamak için Azure AD B2C tarafından kullanılan bir talep kümesi sağlar.

Azure AD 'yi, ilkenizin uzantısı dosyasındaki **ClaimsProvider** öğesine ekleyerek bir talep sağlayıcı olarak tanımlayabilirsiniz.

1. *TrustFrameworkExtensions. xml* dosyasını açın.
2. **Claimsproviders** öğesini bulun. Yoksa, kök öğenin altına ekleyin.
3. Yeni bir **ClaimsProvider** 'ı aşağıdaki şekilde ekleyin:

    ```XML
    <ClaimsProvider>
      <Domain>Contoso</Domain>
      <DisplayName>Login using Contoso</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="ContosoProfile">
          <DisplayName>Contoso Employee</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="OpenIdConnect"/>
          <Metadata>
            <Item Key="METADATA">https://login.windows.net/your-AD-tenant-name.onmicrosoft.com/.well-known/openid-configuration</Item>
            <Item Key="ProviderName">https://sts.windows.net/00000000-0000-0000-0000-000000000000/</Item>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid</Item>
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

4. **ClaimsProvider** öğesi altında, **etki alanı** değerini diğer kimlik sağlayıcılarından ayırt etmek için kullanılabilecek benzersiz bir değer olarak güncelleştirin. Örneğin, `Contoso`. Bu etki alanı ayarının sonuna bir `.com` yerleştirmezsiniz.
5. **ClaimsProvider** öğesi altında, **DisplayName** için değeri, talep sağlayıcı için kolay bir ad olarak güncelleştirin. Bu değer şu anda kullanılmıyor.

### <a name="update-the-technical-profile"></a>Teknik profili güncelleştirme

Azure AD uç noktasından bir belirteç almak için Azure AD B2C Azure AD ile iletişim kurmak için kullanması gereken protokolleri tanımlamanız gerekir. Bu işlem, **ClaimsProvider**'ın **teknisyen** öğesi içinde yapılır.

1. **Teknisyen** öğesinin kimliğini güncelleştirin. Bu KIMLIK, ilkenin diğer bölümlerinden bu teknik profile başvurmak için kullanılır.
1. **DisplayName**için değeri güncelleştirin. Bu değer, oturum açma ekranınızdaki oturum açma düğmesinde görüntülenir.
1. **Açıklama**değerini güncelleştirin.
1. Azure AD, OpenID Connect protokolünü kullanır, bu nedenle **protokol** değerinin `OpenIdConnect`olduğundan emin olun.
1. **Meta verilerin** değerini, `your-AD-tenant-name` Azure AD kiracı adınızın `https://login.windows.net/your-AD-tenant-name.onmicrosoft.com/.well-known/openid-configuration`olarak ayarlayın. Örneğin, `https://login.windows.net/fabrikam.onmicrosoft.com/.well-known/openid-configuration`
1. Tarayıcınızı açın ve yeni güncelleştirdiğiniz **meta veri** URL 'sine gidin, **veren** nesnesini arayın ve ardından değeri kopyalayıp XML dosyasındaki **ProviderName** değerine yapıştırın.
1. Uygulama kaydından **client_id** ÖĞESINI uygulama kimliği olarak ayarlayın.
1. **Cryptographickeys**altında, **Storagereferenceıd** değerini daha önce oluşturduğunuz ilke anahtarının adı olarak güncelleştirin. Örneğin, `B2C_1A_ContosoAppSecret`.

### <a name="upload-the-extension-file-for-verification"></a>Uzantı dosyasını doğrulama için karşıya yükle

Şimdi, ilkenizi Azure AD dizininizle nasıl iletişim kuracağını öğrenmek için Azure AD B2C ilkenizi yapılandırdınız. Şu ana kadar herhangi bir sorun olmadığını doğrulamak için, ilkenizin uzantı dosyasını karşıya yüklemeyi deneyin.

1. Azure AD B2C kiracınızdaki **özel ilkeler** sayfasında, **ilkeyi karşıya yükle**' yi seçin.
1. Varsa **Ilkenin üzerine yazmayı**etkinleştirin ve ardından *TrustFrameworkExtensions. xml* dosyasına gidip seçin.
1. **Karşıya Yükle**'ye tıklayın.

## <a name="register-the-claims-provider"></a>Talep sağlayıcısını Kaydet

Bu noktada, kimlik sağlayıcısı ayarlanmıştır, ancak kaydolma/oturum açma sayfalarında henüz mevcut değildir. Kullanılabilir hale getirmek için, var olan bir şablon Kullanıcı yolculuğunun bir yinelemesini oluşturun ve ardından Azure AD kimlik sağlayıcısı 'nı da içerecek şekilde değiştirin:

1. *TrustFrameworkBase. xml* dosyasını başlangıç paketinden açın.
1. `Id="SignUpOrSignIn"`içeren **Useryolculuney** öğesinin tüm içeriğini bulup kopyalayın.
1. *TrustFrameworkExtensions. xml* ' i açın ve **User, neys** öğesini bulun. Öğe yoksa, bir tane ekleyin.
1. **User, neys** öğesinin bir alt öğesi olarak kopyaladığınız **User, ney** öğesinin tüm içeriğini yapıştırın.
1. Kullanıcı yolculuğunun KIMLIĞINI yeniden adlandırın. Örneğin, `SignUpSignInContoso`.

### <a name="display-the-button"></a>Düğmeyi görüntüleme

**Claimsproviderselection** öğesi, kaydolma/oturum açma sayfasındaki bir kimlik sağlayıcısı düğmesine benzer. Azure AD için bir **Claimsproviderselection** öğesi eklerseniz, bir Kullanıcı sayfada yer alıyorsa yeni bir düğme görüntülenir.

1. *TrustFrameworkExtensions. xml*içinde oluşturduğunuz Kullanıcı yolculuğunda `Order="1"` Içeren **orchestrationstep** öğesini bulun.
1. **Claimsproviderseçimleri**altında aşağıdaki öğeyi ekleyin. **Targetclaimsexchangeıd** değerini uygun bir değere ayarlayın, örneğin `ContosoExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Düğmeyi bir eyleme bağlama

Artık bir düğmeye sahip olduğunuza göre, bunu bir eyleme bağlamanız gerekir. Bu durumda Azure AD B2C eylem, Azure AD ile iletişim kurmak için bir belirteç almak üzere kullanılır. Azure AD talep sağlayıcınızla ilgili teknik profili bağlayarak düğmeyi bir eyleme bağlayın:

1. Kullanıcı yolculuğunda `Order="2"` içeren **Orchestrationstep** öğesini bulun.
1. **Targetclaimsexchangeıd**Için kullandığınız **ID** için aynı değeri kullandığınızdan emin olmak Için aşağıdaki **claimsexchange** öğesini ekleyin:

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="ContosoProfile" />
    ```

    **TechnicalProfileReferenceId** değerini daha önce oluşturduğunuz teknik profilin **kimliğiyle** güncelleştirin. Örneğin, `ContosoProfile`.

1. *TrustFrameworkExtensions. xml* dosyasını kaydedin ve doğrulama için yeniden yükleyin.

## <a name="create-an-azure-ad-b2c-application"></a>Azure AD B2C uygulaması oluşturma

Azure AD B2C ile iletişim, B2C kiracınıza kaydolmanızı sağlayan bir uygulama aracılığıyla oluşur. Bu bölümde, daha önce yapmadıysanız bir test uygulaması oluşturmak için tamamlayabildiğiniz isteğe bağlı adımlar listelenmektedir.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>Bağlı olan taraf dosyasını güncelleştirme ve test etme

Oluşturduğunuz Kullanıcı yolculuğunu başlatan bağlı olan taraf (RP) dosyasını güncelleştirin.

1. Çalışma dizininizde *Signuporsignın. xml* ' in bir kopyasını oluşturun ve yeniden adlandırın. Örneğin, bunu *Signupsignıncontoso. xml*olarak yeniden adlandırın.
1. Yeni dosyayı açın ve **TrustFrameworkPolicy** Için **PolicyId** özniteliğinin değerini benzersiz bir değerle güncelleştirin. Örneğin, `SignUpSignInContoso`.
1. **Publicpolicyuri** DEĞERINI ilke URI 'siyle güncelleştirin. Örneğin, `http://contoso.com/B2C_1A_signup_signin_contoso`.
1. **Defaultuseryolculuney** Içindeki **referenceıd** özniteliğinin değerini, daha önce oluşturduğunuz Kullanıcı yolculuğunun kimliğiyle eşleşecek şekilde güncelleştirin. Örneğin, *Signupsignıncontoso*.
1. Değişikliklerinizi kaydedin ve dosyayı karşıya yükleyin.
1. **Özel ilkeler**altında, listeden yeni ilkeyi seçin.
1. **Uygulama Seç** açılan penceresinde, daha önce oluşturduğunuz Azure AD B2C uygulamayı seçin. Örneğin, *testapp1*.
1. **Şimdi Çalıştır uç noktasını** kopyalayın ve özel bir tarayıcı penceresinde açın. Örneğin, Google Chrome 'Da veya Microsoft Edge 'de bir InPrivate penceresinde. Özel bir tarayıcı penceresinde açmak, şu anda önbelleğe alınmış Azure AD kimlik bilgilerini kullanarak tüm Kullanıcı yolculuğunu test etmenize olanak tanır.
1. Azure AD oturum açma düğmesini (örneğin, *contoso çalışanı*) seçin ve ardından Azure AD kuruluş kiracınızdaki bir kullanıcının kimlik bilgilerini girin. Uygulamayı yetkilendirmeniz ve sonra profilinizin bilgilerini girmeniz istenir.

Oturum açma işlemi başarılı olursa, tarayıcınız, Azure AD B2C tarafından döndürülen belirtecin içeriğini görüntüleyen `https://jwt.ms`olarak yönlendirilir.

## <a name="next-steps"></a>Sonraki adımlar

Özel ilkelerle çalışırken, bazen bir ilke geliştirme sırasında sorun giderirken ek bilgilere ihtiyaç duyabilirsiniz.

Sorunları tanılamaya yardımcı olmak için ilkeyi geçici olarak "geliştirici moduna" koyabilirsiniz ve Azure Application Insights ile Günlükler toplayabilirsiniz. [Azure Active Directory B2C: günlükleri toplama](active-directory-b2c-troubleshoot-custom.md)hakkında bilgi edinin.
