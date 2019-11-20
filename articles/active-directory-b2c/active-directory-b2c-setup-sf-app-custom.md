---
title: Azure Active Directory B2C içinde özel ilkeler kullanarak Salesforce SAML sağlayıcısı ile oturum açma ayarlama
description: Azure Active Directory B2C içinde özel ilkeler kullanarak Salesforce SAML sağlayıcısı ile oturum açma ayarlayın.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 2922aa32bab2d9d7146a03757850d4b724ad7570
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71822263"
---
# <a name="set-up-sign-in-with-a-salesforce-saml-provider-by-using-custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C içinde özel ilkeler kullanarak Salesforce SAML sağlayıcısı ile oturum açma ayarlama

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Bu makalede, Azure Active Directory B2C (Azure AD B2C) ' de [özel ilkeler](active-directory-b2c-overview-custom.md) kullanılarak Salesforce kuruluştan kullanıcıların oturum açma özelliğini nasıl etkinleştireceğinizi gösterilmektedir. Özel ilkeye bir [SAML teknik profili](saml-technical-profile.md) ekleyerek oturum açmayı etkinleştirebilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

- [Azure Active Directory B2C özel ilkeleri kullanmaya başlama](active-directory-b2c-get-started-custom.md)bölümündeki adımları uygulayın.
- Daha önce yapmadıysanız, [ücretsiz bir geliştirici sürümü hesabına](https://developer.salesforce.com/signup)kaydolun. Bu makalede [Salesforce şimşek deneyimi](https://developer.salesforce.com/page/Lightning_Experience_FAQ)kullanılmaktadır.
- Salesforce kuruluşunuz için [bir etki alanım ayarlayın](https://help.salesforce.com/articleView?id=domain_name_setup.htm&language=en_US&type=0) .

### <a name="set-up-salesforce-as-an-identity-provider"></a>Salesforce 'ı bir kimlik sağlayıcısı olarak ayarlama

1. [Salesforce 'Ta oturum açın](https://login.salesforce.com/).
2. Sol taraftaki menüde **Ayarlar**' ın altında **kimlik**' i genişletin ve **kimlik sağlayıcısı**' nı seçin.
3. **Kimlik sağlayıcısını etkinleştir**' i seçin.
4. **Sertifikayı seçin**altında, Salesforce 'ın Azure AD B2C iletişim kurmak için kullanmasını istediğiniz sertifikayı seçin. Varsayılan sertifikayı kullanabilirsiniz.
5. **Kaydet** düğmesine tıklayın.

### <a name="create-a-connected-app-in-salesforce"></a>Salesforce 'ta bağlantılı uygulama oluşturma

1. **Kimlik sağlayıcısı** sayfasında, **artık bağlı uygulamalar aracılığıyla hizmet sağlayıcıları ' nı seçin. Buraya tıklayın.**
2. **Temel bilgiler**altında, bağlı uygulamanız için gerekli değerleri girin.
3. **Web uygulaması ayarları**altında **SAML etkinleştir** kutusunu işaretleyin.
4. **VARLıK kimliği** alanına aşağıdaki URL 'yi girin. @No__t-0 değerini Azure AD B2C kiracınızın adıyla değiştirdiğinizden emin olun.

      ```
      https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase
      ```

6. **ACS URL 'si** alanına aşağıdaki URL 'yi girin. @No__t-0 değerini Azure AD B2C kiracınızın adıyla değiştirdiğinizden emin olun.

      ```
      https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer
      ```
7. Listenin en altına gidin ve **Kaydet**' e tıklayın.

### <a name="get-the-metadata-url"></a>Meta veri URL 'sini al

1. Bağlı uygulamanızın Genel Bakış sayfasında **Yönet**' e tıklayın.
2. **Meta veri bulma uç noktası**için değeri kopyalayın ve kaydedin. Bu makalenin ilerleyen bölümlerinde kullanacaksınız.

### <a name="set-up-salesforce-users-to-federate"></a>Salesforce kullanıcılarını federasyona kurmak için ayarlama

1. Bağlı uygulamanızın **Yönet** sayfasında **profilleri Yönet**' e tıklayın.
2. Azure AD B2C ile federasyona eklemek istediğiniz profilleri (veya Kullanıcı gruplarını) seçin. Bir sistem yöneticisi olarak, Salesforce hesabınızı kullanarak federasyona eklemek için **Sistem Yöneticisi** onay kutusunu seçin.

## <a name="generate-a-signing-certificate"></a>İmza sertifikası oluşturma

Salesforce 'a gönderilen isteklerin Azure AD B2C tarafından imzalanması gerekir. İmza sertifikası oluşturmak için Azure PowerShell açın ve ardından aşağıdaki komutları çalıştırın.

> [!NOTE]
> Kiracı adını ve parolayı en üstteki iki satırda güncelleştirdiğinizden emin olun.

```powershell
$tenantName = "<YOUR TENANT NAME>.onmicrosoft.com"
$pwdText = "<YOUR PASSWORD HERE>"

$Cert = New-SelfSignedCertificate -CertStoreLocation Cert:\CurrentUser\My -DnsName "SamlIdp.$tenantName" -Subject "B2C SAML Signing Cert" -HashAlgorithm SHA256 -KeySpec Signature -KeyLength 2048

$pwd = ConvertTo-SecureString -String $pwdText -Force -AsPlainText

Export-PfxCertificate -Cert $Cert -FilePath .\B2CSigningCert.pfx -Password $pwd
```

## <a name="create-a-policy-key"></a>İlke anahtarı oluşturma

Azure AD B2C kiracınızda oluşturduğunuz sertifikayı depolamanız gerekir.

1. [Azure Portal](https://portal.azure.com/)’ında oturum açın.
2. Üst menüdeki **Dizin + abonelik** filtresini seçip kiracınızı içeren dizini seçerek Azure AD B2C kiracınızı içeren dizini kullandığınızdan emin olun.
3. Azure portal sol üst köşesindeki **tüm hizmetler** ' i seçin ve ardından **Azure AD B2C**' i arayıp seçin.
4. Genel Bakış sayfasında **kimlik deneyimi çerçevesi**' ni seçin.
5. **Ilke anahtarlarını** seçin ve ardından **Ekle**' yi seçin.
6. **Seçenekler**için `Upload` ' i seçin.
7. İlke için bir **Ad** girin. Örneğin, SAMLSigningCert. @No__t-0 öneki, anahtarınızın adına otomatik olarak eklenir.
8. ' A gidin ve oluşturduğunuz B2CSigningCert. pfx sertifikasını seçin.
9. Sertifika için **parolayı** girin.
3. **Oluştur**’a tıklayın.

## <a name="add-a-claims-provider"></a>Talep sağlayıcısı ekleme

Kullanıcıların bir Salesforce hesabı kullanarak oturum açmasını istiyorsanız, hesabı Azure AD B2C bir uç nokta aracılığıyla iletişim kurabildiği bir talep sağlayıcı olarak tanımlamanız gerekir. Uç noktası, belirli bir kullanıcının kimliği doğrulandığını doğrulamak için Azure AD B2C tarafından kullanılan bir talep kümesi sağlar.

Bir Salesforce hesabını, ilkenizin uzantı dosyasındaki **Claimsproviders** öğesine ekleyerek bir talep sağlayıcı olarak tanımlayabilirsiniz.

1. *TrustFrameworkExtensions. xml*' i açın.
2. **Claimsproviders** öğesini bulun. Yoksa, kök öğenin altına ekleyin.
3. Yeni bir **ClaimsProvider** 'ı aşağıdaki şekilde ekleyin:

    ```XML
    <ClaimsProvider>
      <Domain>salesforce</Domain>
      <DisplayName>Salesforce</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="salesforce">
          <DisplayName>Salesforce</DisplayName>
          <Description>Login with your Salesforce account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="WantsSignedAssertions">false</Item>
            <Item Key="PartnerEntity">https://contoso-dev-ed.my.salesforce.com/.well-known/samlidp.xml</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="userId"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="username"/>
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication"/>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="SAMLIdp" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. **Partnerentity** değerini, daha önce kopyaladığınız Salesforce meta veri URL 'si ile güncelleştirin.
5. Her iki **Storagereferenceıd** örneğinin değerini imzalama sertifikanızın anahtarının adına güncelleştirin. Örneğin, B2C_1A_SAMLSigningCert.

### <a name="upload-the-extension-file-for-verification"></a>Uzantı dosyasını doğrulama için karşıya yükle

Şimdi, ilkenizi Azure AD B2C Salesforce hesabınızla nasıl iletişim kuracağını öğrenmek için yapılandırdığınıza ulaştınız. Şu ana kadar herhangi bir sorun olmadığını doğrulamak için, ilkenizin uzantı dosyasını karşıya yüklemeyi deneyin.

1. Azure AD B2C kiracınızdaki **özel ilkeler** sayfasında, **ilkeyi karşıya yükle**' yi seçin.
2. Varsa **Ilkenin üzerine yazmayı**etkinleştirin ve ardından *TrustFrameworkExtensions. xml* dosyasına gidip seçin.
3. **Karşıya Yükle**'ye tıklayın.

## <a name="register-the-claims-provider"></a>Talep sağlayıcısını Kaydet

Bu noktada, kimlik sağlayıcısı ayarlanmıştır, ancak kaydolma veya oturum açma ekranlarından hiçbirinde kullanılamaz. Kullanılabilir hale getirmek için, var olan bir şablon Kullanıcı yolculuğunun bir yinelemesini oluşturun ve ardından bunu, Salesforce kimlik sağlayıcısına da sahip olacak şekilde değiştirin.

1. *TrustFrameworkBase. xml* dosyasını başlangıç paketinden açın.
2. @No__t-1 içeren **Useryolculuney** öğesinin tüm içeriğini bulup kopyalayın.
3. *TrustFrameworkExtensions. xml* ' i açın ve **User, neys** öğesini bulun. Öğe yoksa, bir tane ekleyin.
4. **User, neys** öğesinin bir alt öğesi olarak kopyaladığınız **User, ney** öğesinin tüm içeriğini yapıştırın.
5. Kullanıcı yolculuğunun KIMLIĞINI yeniden adlandırın. Örneğin, `SignUpSignInSalesforce`.

### <a name="display-the-button"></a>Düğmeyi görüntüleme

**Claimsproviderselection** öğesi, kaydolma veya oturum açma ekranındaki bir kimlik sağlayıcısı düğmesine benzer. LinkedIn hesabı için bir **Claimsproviderselection** öğesi eklerseniz, bir Kullanıcı sayfada yer alıyorsa yeni bir düğme görüntülenir.

1. Az önce oluşturduğunuz Kullanıcı yolculuğunda `Order="1"` içeren **Orchestrationstep** öğesini bulun.
2. **Claimsproviderseçilir**altında aşağıdaki öğeyi ekleyin. **Targetclaimsexchangeıd** değerini uygun bir değere ayarlayın, örneğin `SalesforceExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="SalesforceExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Düğmeyi bir eyleme bağlama

Artık bir düğmeye sahip olduğunuza göre, bunu bir eyleme bağlamanız gerekir. Bu durumda, bir belirteç almak için Salesforce hesabıyla iletişim kurmak Azure AD B2C.

1. Kullanıcı yolculuğunda `Order="2"` içeren bir düzenleyen **Tionstep** bulun.
2. **Targetclaimsexchangeıd**Için kullandığınız **ID** için aynı değeri kullandığınızdan emin olmak Için aşağıdaki **claimsexchange** öğesini ekleyin:

    ```XML
    <ClaimsExchange Id="SalesforceExchange" TechnicalProfileReferenceId="salesforce" />
    ```

    **TechnicalProfileReferenceId** değerini daha önce oluşturduğunuz teknik profilin **kimliğiyle** güncelleştirin. Örneğin, `LinkedIn-OAUTH`.

3. *TrustFrameworkExtensions. xml* dosyasını kaydedin ve doğrulama için yeniden yükleyin.

## <a name="create-an-azure-ad-b2c-application"></a>Azure AD B2C uygulaması oluşturma

Azure AD B2C ile iletişim, B2C kiracınıza kaydolmanızı sağlayan bir uygulama aracılığıyla oluşur. Bu bölümde, daha önce yapmadıysanız bir test uygulaması oluşturmak için tamamlayabildiğiniz isteğe bağlı adımlar listelenmektedir.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>Bağlı olan taraf dosyasını güncelleştirme ve test etme

Yeni oluşturduğunuz Kullanıcı yolculuğunu başlatan bağlı olan taraf (RP) dosyasını güncelleştirin:

1. Çalışma dizininizde *Signuporsignın. xml* ' in bir kopyasını oluşturun ve yeniden adlandırın. Örneğin, bunu *Signupsignınsalesforce. xml*olarak yeniden adlandırın.
2. Yeni dosyayı açın ve **TrustFrameworkPolicy** Için **PolicyId** özniteliğinin değerini benzersiz bir değerle güncelleştirin. Örneğin, `SignUpSignInSalesforce`.
3. **Publicpolicyuri** DEĞERINI ilke URI 'siyle güncelleştirin. Örneğin, `http://contoso.com/B2C_1A_signup_signin_salesforce`
4. **Defaultuseryolculuney** Içindeki **referenceıd** özniteliğinin değerini, oluşturduğunuz yenı Kullanıcı yolculuğunun kimliğiyle eşleşecek şekilde güncelleştirin (Signupsignınsalesforce).
5. Değişikliklerinizi kaydedin, dosyayı karşıya yükleyin ve ardından listeden yeni ilkeyi seçin.
6. Oluşturduğunuz Azure AD B2C uygulamasının **Uygulama Seç** alanında seçildiğinden emin olun ve **Şimdi Çalıştır**' a tıklayarak test edin.
