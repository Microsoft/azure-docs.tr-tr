---
title: SAML kimlik sağlayıcısı ile kaydolma ve oturum açma ayarlama
titleSuffix: Azure Active Directory B2C
description: Azure Active Directory B2C içinde herhangi bir SAML kimlik sağlayıcısı (IDP) ile kaydolma ve oturum açma ayarlayın.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/08/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 944eff6e76f4e5759f70105fe9d09aa61093917f
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107028307"
---
# <a name="set-up-sign-up-and-sign-in-with-saml-identity-provider-using-azure-active-directory-b2c"></a>Azure Active Directory B2C kullanarak, SAML kimlik sağlayıcısı ile kaydolma ve oturum açma ayarlama

Azure Active Directory B2C (Azure AD B2C) SAML 2,0 kimlik sağlayıcılarıyla Federasyonu destekler. Bu makalede, bir SAML kimlik sağlayıcısı kullanıcı hesabıyla oturum açmayı etkinleştirme, kullanıcıların [ADFS](./identity-provider-adfs.md) ve [Salesforce](identity-provider-salesforce-saml.md)gibi mevcut sosyal ya da kurumsal kimliklerle oturum açmasına izin verme konusu gösterilmektedir.

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="scenario-overview"></a>Senaryoya genel bakış

Azure AD B2C, kullanıcıların uygulamanızda dış sosyal veya kurumsal SAML kimlik sağlayıcılarının (IDP) kimlik bilgileriyle oturum açmalarına izin verecek şekilde yapılandırabilirsiniz. Azure AD B2C SAML kimlik sağlayıcısı ile federe olduğunda, SAML **kimlik sağlayıcısına** bir SAML isteği başlatan bir **hizmet sağlayıcı** olarak çalışır ve bir SAML yanıtı bekler. Aşağıdaki diyagramda:

1. Uygulama Azure AD B2C bir yetkilendirme isteği başlatır. Uygulama bir [OAuth 2,0](protocols-overview.md) veya [OpenID Connect](openid-connect.md) uygulaması ya da bir [SAML hizmet sağlayıcısı](saml-service-provider.md)olabilir. 
1. Azure AD B2C oturum açma sayfasında, Kullanıcı bir SAML kimlik sağlayıcısı hesabıyla oturum açmayı seçer (örneğin, *contoso*). Azure AD B2C, bir SAML yetkilendirme isteği başlatır ve oturum açma işlemini tamamlaması için kullanıcıyı SAML Identity sağlayıcısına alır.
1. SAML kimlik sağlayıcısı bir SAML yanıtı döndürüyor.
1. Azure AD B2C SAML belirtecini doğrular, talepleri ayıklar, kendi belirtecini yayınlar ve kullanıcıyı uygulamaya geri alır.  

![SAML Identity Provider Flow ile oturum açın](./media/identity-provider-generic-saml/sign-in-with-saml-identity-provider-flow.png)

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="components-of-the-solution"></a>Çözümün bileşenleri

Bu senaryo için gerekli olan aşağıdaki bileşenler şunlardır:

* Azure AD B2C 'den SAML isteklerini alma, kodunu çözme ve yanıtlama yeteneğine sahip bir SAML **kimlik sağlayıcısı** .
* Kimlik sağlayıcınız için herkese açık bir SAML **meta veri uç noktası** .
* [Azure AD B2C kiracısı](tutorial-create-tenant.md).
 

## <a name="create-a-policy-key"></a>İlke anahtarı oluşturma

Azure AD B2C ile SAML kimlik sağlayıcınız arasında güven oluşturmak için, özel anahtarla geçerli bir x509 sertifikası sağlamanız gerekir. Azure AD B2C, sertifikanın özel anahtarını kullanarak SAML isteklerini imzalar. Kimlik sağlayıcısı, sertifikanın ortak anahtarını kullanarak isteği doğrular. Ortak anahtara teknik profil meta verileri aracılığıyla erişilebilir. Alternatif olarak,. cer dosyasını SAML Identity sağlayıcınıza el ile yükleyebilirsiniz.

Kendinden imzalı bir sertifika çoğu senaryo için kabul edilebilir. Üretim ortamları için, bir sertifika yetkilisi tarafından verilen bir x509 sertifikası kullanılması önerilir. Ayrıca, bu belgenin ilerleyen kısımlarında açıklandığı gibi, üretim dışı ortamlar için her iki tarafta da SAML imzalamayı devre dışı bırakabilirsiniz.

### <a name="obtain-a-certificate"></a>Sertifika edinme

[!INCLUDE [active-directory-b2c-create-self-signed-certificate](../../includes/active-directory-b2c-create-self-signed-certificate.md)]

### <a name="upload-the-certificate"></a>Sertifikayı güncelleştirin

Sertifikanızı Azure AD B2C kiracınızda depolamanız gerekir.

1. [Azure portalında](https://portal.azure.com/) oturum açın.
1. Azure AD B2C kiracınızı içeren dizini kullandığınızdan emin olun. Üstteki menüden **Dizin + abonelik** filtresini seçin ve kiracınızı içeren dizini seçin.
1. Azure portal sol üst köşesindeki **tüm hizmetler** ' i seçin ve ardından **Azure AD B2C**' i arayıp seçin.
1. Genel Bakış sayfasında **kimlik deneyimi çerçevesi**' ni seçin.
1. **Ilke anahtarlarını** seçin ve ardından **Ekle**' yi seçin.
1. **Seçenekler** için öğesini seçin `Upload` .
1. İlke anahtarı için bir **ad** girin. Örneğin, `SAMLSigningCert`. Ön ek, `B2C_1A_` anahtarınızın adına otomatik olarak eklenir.
1. Özel anahtarla Certificate. pfx dosyanıza gidin ve bu dosyayı seçin.
1. **Oluştur**’a tıklayın.

## <a name="configure-the-saml-technical-profile"></a>SAML teknik profilini yapılandırma

SAML kimlik sağlayıcısını, ilkenizin uzantı dosyasındaki **Claimsproviders** öğesine ekleyerek tanımlayın. Talep sağlayıcıları, SAML kimlik sağlayıcısı ile iletişim kurmak için gereken uç noktaları ve protokolleri belirleyen bir SAML teknik profili içerir. SAML teknik profiliyle bir talep sağlayıcısı eklemek için:

1. *TrustFrameworkExtensions.xml* açın.
1. **Claimsproviders** öğesini bulun. Yoksa, kök öğenin altına ekleyin.
1. Yeni bir **ClaimsProvider** 'ı aşağıdaki şekilde ekleyin:

    ```xml
    <ClaimsProvider>
      <Domain>Contoso.com</Domain>
      <DisplayName>Contoso</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Contoso-SAML2">
          <DisplayName>Contoso</DisplayName>
          <Description>Login with your SAML identity provider account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="PartnerEntity">https://your-AD-FS-domain/federationmetadata/2007-06/federationmetadata.xml</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="assertionSubjectName" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="first_name" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="last_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="http://schemas.microsoft.com/identity/claims/displayname" />
            <OutputClaim ClaimTypeReferenceId="email"  />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-idp"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

Aşağıdaki XML öğelerini ilgili değerle güncelleştirin:

|XML öğesi  |Değer  |
|---------|---------|
|ClaimsProvider\Domain  | [Doğrudan oturum açma](direct-signin.md?pivots=b2c-custom-policy#redirect-sign-in-to-a-social-provider)için kullanılan etki alanı adı. Doğrudan oturum açma bölümünde kullanmak istediğiniz etki alanı adını girin. Örneğin, *contoso.com*. |
|Teknisyen \ DisplayName|Bu değer, oturum açma ekranınızdaki oturum açma düğmesinde görüntülenir. Örneğin, *contoso*. |
|Metadata\PartnerEntity|SAML kimlik sağlayıcısı meta verisinin URL 'SI. Ya da kimlik sağlayıcısı meta verilerini kopyalayabilir ve bunu CDATA öğesinin içine ekleyebilirsiniz `<![CDATA[Your IDP metadata]]>` .|

## <a name="map-the-claims"></a>Talepleri eşleme

**Outputclaim** Öğesı, SAML kimlik sağlayıcısı tarafından döndürülen taleplerin bir listesini içerir. İlkenizde tanımlanan talebin adını kimlik sağlayıcısında tanımlanan onaylama adı ile eşleyin. Talepler listesi (onaylama) için kimlik sağlayıcınızı denetleyin. Daha fazla bilgi için bkz. [talep eşleme](identity-provider-generic-saml-options.md#claims-mapping).

Yukarıdaki örnekte, *contoso-SAML2* , bir SAML kimlik sağlayıcısı tarafından döndürülen talepleri içerir:

* **Issueruserıd** Claim, **assertionSubjectName** talebine eşlenir.
* **First_name** **talebi, bu talep ile** eşlendi.
* **Last_name** talebi, **Soyadı** talebine eşlenir.
* **DisplayName** talebi, `http://schemas.microsoft.com/identity/claims/displayname` talebe eşlendi.
* Ad eşleştirmesi olmayan **e-posta** talebi.

Teknik profil, kimlik sağlayıcısı tarafından döndürülmeyen talepleri de döndürür:

* Kimlik sağlayıcısının adını içeren **IdentityProvider** talebi.
* Varsayılan bir **Socialidpauthentication** değeri olan **authenticationsource** talebi.
 
### <a name="add-the-saml-session-technical-profile"></a>SAML oturumu teknik profilini ekleyin

`SM-Saml-idp`SAML oturumu teknik profiliniz yoksa uzantı ilkenize bir tane ekleyin. Bölümünü bulun `<ClaimsProviders>` ve AŞAĞıDAKI XML kod parçacığını ekleyin. İlkeniz zaten `SM-Saml-idp` Teknik profili içeriyorsa, sonraki adıma atlayın. Daha fazla bilgi için bkz. [Çoklu oturum açma oturumu yönetimi](custom-policy-reference-sso.md).

```xml
<ClaimsProvider>
  <DisplayName>Session Management</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SM-Saml-idp">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="IncludeSessionIndex">false</Item>
        <Item Key="RegisterServiceProviders">false</Item>
      </Metadata>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-create-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="configure-your-saml-identity-provider"></a>SAML kimlik sağlayıcınızı yapılandırma

İlkeniz yapılandırıldıktan sonra SAML kimlik sağlayıcınızı Azure AD B2C SAML meta verileri ile yapılandırmanız gerekir. SAML meta verileri, ilkenizin yapılandırmasını göstermek için SAML protokolünde kullanılan bilgiler, **hizmet sağlayıcısı**. Oturum açma ve oturum kapatma, sertifikalar, oturum açma yöntemi ve daha fazlası gibi hizmetlerin konumunu tanımlar.

Her SAML kimlik sağlayıcısı, bir hizmet sağlayıcısını ayarlamak için farklı adımlara sahiptir. Bazı SAML kimlik sağlayıcıları Azure AD B2C meta verileri ister, diğer bir deyişle, meta veri dosyasından el ile gitmeniz ve bilgileri sağlamanız gerekir. Rehberlik için kimlik sağlayıcınızın belgelerine bakın.

Aşağıdaki örnekte, bir Azure AD B2C teknik profilinin SAML meta verileri için bir URL adresi gösterilmektedir:

```
https://<your-tenant-name>.b2clogin.com/<your-tenant-name>.onmicrosoft.com/<your-policy>/samlp/metadata?idptp=<your-technical-profile>
```

[Özel bir etki alanı](custom-domain.md)kullanırken aşağıdaki biçimi kullanın:

```
https://your-domain-name/<your-tenant-name>.onmicrosoft.com/<your-policy>/samlp/metadata?idptp=<your-technical-profile>
```

Aşağıdaki değerleri değiştirin:

- Kiracı **adınız,** örneğin Your-Tenant.onmicrosoft.com gibi.
- login.contoso.com gibi özel etki alanı adınızla **etki alanı adınız** .
- ilke adınızla **ilkenize** . Örneğin, B2C_1A_signup_signin_adfs.
- SAML kimlik sağlayıcısı teknik profilinizin adı ile **Teknik profiliniz** . Örneğin, contoso-SAML2.

Bir tarayıcı açın ve URL 'ye gidin. Doğru URL 'YI yazdığınızdan ve XML meta veri dosyasına erişiminiz olduğundan emin olun.

## <a name="test-your-custom-policy"></a>Özel ilkenizi test etme

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Portal araç çubuğunda **Dizin + abonelik** simgesini seçin ve ardından Azure AD B2C kiracınızı içeren dizini seçin.
1. Azure portal, araması yapın ve **Azure AD B2C** seçin.
1. **İlkeler** altında **kimlik deneyimi çerçevesi** ' ni seçin.
1. Örneğin, bağlı olan taraf ilkenizi seçin `B2C_1A_signup_signin` .
1. **Uygulama** için, [daha önce kaydetmiş](tutorial-register-applications.md)olduğunuz bir Web uygulamasını seçin. **Yanıt URL 'si** gösterilmesi gerekir `https://jwt.ms` .
1. **Şimdi Çalıştır** düğmesini seçin.
1. Kayıt veya oturum açma sayfasından contoso hesabıyla oturum açmak için **contoso** ' u seçin.

Oturum açma işlemi başarılı olursa, tarayıcınız öğesine yönlendirilir `https://jwt.ms` ve bu, Azure AD B2C tarafından döndürülen belirtecin içeriğini görüntüler.

## <a name="next-steps"></a>Sonraki adımlar

- [SAML kimlik sağlayıcısı seçeneklerini Azure Active Directory B2C ile yapılandırma](identity-provider-generic-saml-options.md)

::: zone-end