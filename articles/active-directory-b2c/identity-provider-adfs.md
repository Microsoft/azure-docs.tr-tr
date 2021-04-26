---
title: Özel ilkeler kullanarak SAML kimlik sağlayıcısı olarak AD FS ekleme
titleSuffix: Azure AD B2C
description: SAML protokolünü ve Azure Active Directory B2C içindeki özel ilkeleri kullanarak AD FS 2016 ayarlayın
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
ms.openlocfilehash: 292a244a4804f97e8622d6841c33b153af373290
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103489177"
---
# <a name="add-ad-fs-as-a-saml-identity-provider-using-custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C içindeki özel ilkeleri kullanarak SAML kimlik sağlayıcısı olarak AD FS ekleyin

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Bu makalede, Azure Active Directory B2C (Azure AD B2C) [özel ilkelerini](custom-policy-overview.md) kullanarak AD FS Kullanıcı hesabı için oturum açma 'nın nasıl etkinleştirileceği gösterilmektedir. Özel ilkeye bir [SAML kimlik sağlayıcısı](identity-provider-generic-saml.md) ekleyerek oturum açmayı etkinleştirebilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="create-a-self-signed-certificate"></a>Otomatik olarak imzalanan sertifika oluşturma

[!INCLUDE [active-directory-b2c-create-self-signed-certificate](../../includes/active-directory-b2c-create-self-signed-certificate.md)]

## <a name="create-a-policy-key"></a>İlke anahtarı oluşturma

Sertifikanızı Azure AD B2C kiracınızda depolamanız gerekir.

1. [Azure portalında](https://portal.azure.com/) oturum açın.
2. Azure AD B2C kiracınızı içeren dizini kullandığınızdan emin olun. Üstteki menüden **Dizin + abonelik** filtresini seçin ve kiracınızı içeren dizini seçin.
3. Azure portal sol üst köşesindeki **tüm hizmetler** ' i seçin ve ardından **Azure AD B2C**' i arayıp seçin.
4. Genel Bakış sayfasında **kimlik deneyimi çerçevesi**' ni seçin.
5. **Ilke anahtarlarını** seçin ve ardından **Ekle**' yi seçin.
6. **Seçenekler** için öğesini seçin `Upload` .
7. İlke anahtarı için bir **ad** girin. Örneğin, `SAMLSigningCert`. Ön ek, `B2C_1A_` anahtarınızın adına otomatik olarak eklenir.
8. Özel anahtarla Certificate. pfx dosyanıza gidin ve bu dosyayı seçin.
9. **Oluştur**’a tıklayın.

## <a name="add-a-claims-provider"></a>Talep sağlayıcısı ekleme

Kullanıcıların bir AD FS hesabı kullanarak oturum açmasını istiyorsanız hesabı, Azure AD B2C bir uç nokta aracılığıyla iletişim kurabildiği bir talep sağlayıcı olarak tanımlamanız gerekir. Uç noktası, belirli bir kullanıcının kimliği doğrulandığını doğrulamak için Azure AD B2C tarafından kullanılan bir talep kümesi sağlar.

Bir AD FS hesabını, ilkenizin uzantısı dosyasındaki **Claimsproviders** öğesine ekleyerek bir talep sağlayıcı olarak tanımlayabilirsiniz. Daha fazla bilgi için bkz. [SAML kimlik sağlayıcısı tanımlama](identity-provider-generic-saml.md).

1. *TrustFrameworkExtensions.xml* açın.
1. **Claimsproviders** öğesini bulun. Yoksa, kök öğenin altına ekleyin.
1. Yeni bir **ClaimsProvider** 'ı aşağıdaki şekilde ekleyin:

    ```xml
    <ClaimsProvider>
      <Domain>contoso.com</Domain>
      <DisplayName>Contoso</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Contoso-SAML2">
          <DisplayName>Contoso</DisplayName>
          <Description>Login with your AD FS account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="PartnerEntity">https://your-AD-FS-domain/federationmetadata/2007-06/federationmetadata.xml</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="userPrincipalName" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name"/>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication"/>
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

1. `your-AD-FS-domain`AD FS etki alanı adıyla değiştirin ve **IdentityProvider** çıkış talebinin değerini DNS ile değiştirin (etki alanınızı gösteren rastgele değer).

1. Bölümünü bulun `<ClaimsProviders>` ve AŞAĞıDAKI XML kod parçacığını ekleyin. İlkeniz zaten `SM-Saml-idp` Teknik profili içeriyorsa, sonraki adıma atlayın. Daha fazla bilgi için bkz. [Çoklu oturum açma oturumu yönetimi](custom-policy-reference-sso.md).

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

1. Dosyayı kaydedin.

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

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="configure-an-ad-fs-relying-party-trust"></a>AD FS bağlı olan taraf güvenini yapılandırma

Azure AD B2C bir kimlik sağlayıcısı olarak AD FS kullanmak için, Azure AD B2C SAML meta verileriyle AD FS bağlı olan taraf güveni oluşturmanız gerekir. Aşağıdaki örnekte, bir Azure AD B2C teknik profilinin SAML meta verilerine yönelik bir URL adresi gösterilmektedir:

```
https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/your-policy/samlp/metadata?idptp=your-technical-profile
```

[Özel bir etki alanı](custom-domain.md)kullanırken aşağıdaki biçimi kullanın:

```
https://your-domain-name/your-tenant-name.onmicrosoft.com/your-policy/samlp/metadata?idptp=your-technical-profile
```

Aşağıdaki değerleri değiştirin:

- Kiracı **adınız,** örneğin Your-Tenant.onmicrosoft.com gibi.
- login.contoso.com gibi özel etki alanı adınızla **etki alanı adınız** .
- ilke adınızla **ilkenize** . Örneğin, B2C_1A_signup_signin_adfs.
- SAML kimlik sağlayıcısı teknik profilinizin adı ile **Teknik profiliniz** . Örneğin, contoso-SAML2.

Bir tarayıcı açın ve URL 'ye gidin. Doğru URL 'YI yazdığınızdan ve XML meta veri dosyasına erişiminiz olduğundan emin olun. AD FS Yönetimi ek bileşenini kullanarak yeni bir bağlı olan taraf güveni eklemek ve ayarları el ile yapılandırmak için, bir federasyon sunucusu üzerinde aşağıdaki yordamı gerçekleştirin. Bu yordamı gerçekleştirmek için gereken en düşük düzeyde **Yöneticiler** veya yerel bilgisayar üzerinde üyelik gereklidir.

1. Sunucu Yöneticisi **Araçlar**' ı seçin ve **AD FS Yönetim**' i seçin.
2. **Bağlı olan taraf güveni Ekle**' yi seçin.
3. **Hoş geldiniz** sayfasında **talep duyarlı**' i seçin ve ardından **Başlat**' a tıklayın.
4. **Veri kaynağı seç** sayfasında, **bağlı olan taraf hakkında verileri içeri aktar ' ı seçin veya yerel bir ağ üzerinde**, Azure AD B2C meta veri URL 'Nizi girin ve ardından **İleri**' ye tıklayın.
5. **Görünen ad belirtin** sayfasında, bir **görünen ad** girin, **Notlar** bölümüne bu bağlı olan taraf güveni Için bir açıklama girin ve ardından **İleri**' ye tıklayın.
6. **Access Control Ilkesi seçin** sayfasında, bir ilke seçin ve ardından **İleri**' ye tıklayın.
7. **Güven eklemeye hazırlanma** sayfasında, ayarları gözden geçirin ve ardından bağlı olan taraf güven bilgilerinizi kaydetmek için **İleri** ' ye tıklayın.
8. **Son** sayfasında, **Kapat**' a tıklayın, bu eylem **talep kurallarını Düzenle** iletişim kutusunu otomatik olarak görüntüler.
9. **Kural Ekle**' yi seçin.
10. **Talep kuralı şablonu**' nda, **LDAP özniteliklerini talep olarak gönder**' i seçin.
11. Bir **talep kuralı adı** belirtin. **Öznitelik deposu** Için **Active Directory Seç**' i seçin, aşağıdaki talepleri ekleyin ve ardından **son** ' a ve **Tamam**' a tıklayın.

    | LDAP özniteliği | Giden talep türü |
    | -------------- | ------------------- |
    | Kullanıcı Asıl Adı | userPrincipalName |
    | Soyadı | family_name |
    | Given-Name | given_name |
    | E-posta adresi | e-posta |
    | Display-Name | name |

    Bu adların, giden talep türü açılan listesinde görüntülemediğine unutmayın. Bunları el ile yazmanız gerekir. (Açılan menü aslında düzenlenebilir).

12.  Sertifika türü temelinde, karma algoritmayı ayarlamanız gerekebilir. Bağlı olan taraf güveni (B2C tanıtımı) Özellikler penceresinde **Gelişmiş** sekmesini seçin ve **güvenli karma algoritmasını** olarak değiştirin `SHA-256` ve **Tamam**' a tıklayın.
13. Sunucu Yöneticisi **Araçlar**' ı seçin ve **AD FS Yönetim**' i seçin.
14. Oluşturduğunuz bağlı olan taraf güvenini seçin, **Federasyon meta verilerinden Güncelleştir**' i seçin ve ardından **Güncelleştir**' e tıklayın.

## <a name="test-your-custom-policy"></a>Özel ilkenizi test etme

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Portal araç çubuğunda **Dizin + abonelik** simgesini seçin ve ardından Azure AD B2C kiracınızı içeren dizini seçin.
1. Azure portal, araması yapın ve **Azure AD B2C** seçin.
1. **İlkeler** altında **kimlik deneyimi çerçevesi** ' ni seçin.
1. Örneğin, bağlı olan taraf ilkenizi seçin `B2C_1A_signup_signin` .
1. **Uygulama** için, [daha önce kaydetmiş](tutorial-register-applications.md)olduğunuz bir Web uygulamasını seçin. **Yanıt URL 'si** gösterilmesi gerekir `https://jwt.ms` .
1. **Şimdi Çalıştır** düğmesini seçin.
1. Kayıt veya oturum açma sayfasından Contoso AD FS kimlik sağlayıcısı ile oturum açmak için **contoso AD FS** ' yi seçin.

Oturum açma işlemi başarılı olursa, tarayıcınız öğesine yönlendirilir `https://jwt.ms` ve bu, Azure AD B2C tarafından döndürülen belirtecin içeriğini görüntüler.

## <a name="troubleshooting-ad-fs-service"></a>AD FS hizmeti sorunlarını giderme  

AD FS, Windows uygulama günlüğünü kullanacak şekilde yapılandırılmıştır. Azure AD B2C özel ilkeleri kullanarak SAML kimlik sağlayıcısı olarak AD FS ayarlama konusunda zorluk yaşıyorsanız, AD FS olay günlüğünü denetlemek isteyebilirsiniz:

1. Windows **arama çubuğunda** **Olay Görüntüleyicisi** yazın ve **Olay Görüntüleyicisi** masaüstü uygulamasını seçin.
1. Farklı bir bilgisayarın günlüğünü görüntülemek için **Olay Görüntüleyicisi (yerel)** öğesine sağ tıklayın. **Başka bir bilgisayara bağlan**' ı seçin ve alanları doldurarak **Bilgisayar Seç** iletişim kutusunu doldurun.
1. **Olay Görüntüleyicisi**, **uygulamalar ve hizmetler günlüklerini** açın.
1. **AD FS** seçip **yönetici**' yi seçin. 
1. Bir olayla ilgili daha fazla bilgi görüntülemek için olaya çift tıklayın.  

### <a name="saml-request-is-not-signed-with-expected-signature-algorithm-event"></a>SAML isteği beklenen imza algoritması olayı ile imzalanmadı

Bu hata, Azure AD B2C tarafından gönderilen SAML isteğinin AD FS yapılandırılan beklenen imza algoritmasıyla imzalanmadığını gösterir. Örneğin, SAML isteği imza algoritması ile imzalanır `rsa-sha256` , ancak beklenen imza algoritması olur `rsa-sha1` . Bu sorunu onarmak için hem Azure AD B2C hem de AD FS aynı imza algoritması ile yapılandırıldığından emin olun.

#### <a name="option-1-set-the-signature-algorithm-in-azure-ad-b2c"></a>Seçenek 1: Azure AD B2C imza algoritmasını ayarlama  

Azure AD B2C SAML isteğinin nasıl imzalanalınacağını yapılandırabilirsiniz. [Xmlsignaturealgorithm](identity-provider-generic-saml.md) meta VERILERI, `SigAlg` SAML isteğindeki parametrenin (sorgu dizesi veya post parametresi) değerini denetler. Aşağıdaki örnek, Azure AD B2C imza algoritmasını kullanacak şekilde yapılandırır `rsa-sha256` .

```xml
<Metadata>
  <Item Key="WantsEncryptedAssertions">false</Item>
  <Item Key="PartnerEntity">https://your-AD-FS-domain/federationmetadata/2007-06/federationmetadata.xml</Item>
  <Item Key="XmlSignatureAlgorithm">Sha256</Item>
</Metadata>
```

#### <a name="option-2-set-the-signature-algorithm-in-ad-fs"></a>Seçenek 2: AD FS imza algoritmasını ayarlama 

Alternatif olarak, AD FS ' de beklenen SAML istek imza algoritmasını yapılandırabilirsiniz.

1. Sunucu Yöneticisi **Araçlar**' ı seçin ve **AD FS Yönetim**' i seçin.
1. Daha önce oluşturduğunuz **bağlı olan taraf güvenini** seçin.
1. **Özellikler**' i seçin ve ardından **İleri** ' yi seçin
1. **Güvenli karma algoritmasını** yapılandırın ve değişiklikleri kaydetmek için **Tamam** ' ı seçin.

::: zone-end
