---
title: Azure Active Directory B2C, uygulamalarınıza SAML IDP olarak yapılandırma
title-suffix: Azure Active Directory B2C
description: Uygulamalarınıza (hizmet sağlayıcıları) SAML protokol onayları sağlamak için Azure Active Directory B2C yapılandırma. Azure AD B2C, SAML uygulamanıza tek kimlik sağlayıcısı (IDP) olarak davranır.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/05/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 652bc9a236a4e4b9d3f99dab640919f2be985984
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107257730"
---
# <a name="register-a-saml-application-in-azure-ad-b2c"></a>Azure AD B2C bir SAML uygulaması kaydetme

Bu makalede, kimlik doğrulaması için Security Assertion Markup Language (SAML) uygulamalarınızı (hizmet sağlayıcıları) Azure Active Directory B2C (Azure AD B2C) bağlama hakkında bilgi edinin.

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="overview"></a>Genel Bakış

Müşteri kimliği ve erişim yönetimi çözümü olarak Azure AD B2C kullanan kuruluşlar, SAML protokolü kullanılarak kimlik doğrulayan uygulamalarla tümleştirme gerektirebilir. Aşağıdaki diyagramda, SAML tabanlı uygulamalarla çoklu oturum açma (SSO) sağlamak için Azure AD B2C *kimlik sağlayıcısı* (IDP) olarak nasıl hizmet verdiği gösterilmektedir.

![Sağ tarafta hizmet sağlayıcı olarak B2C ve B2C 'de kimlik sağlayıcısı olarak B2C ile diyagram.](media/saml-service-provider/saml-service-provider-integration.png)

1. Uygulama, Azure AD B2C's SAML oturum açma uç noktasına gönderilen bir SAML AuthN Isteği oluşturur.
2. Kullanıcı, kimlik doğrulaması için Azure AD B2C yerel bir hesap veya başka bir Federasyon kimlik sağlayıcısı kullanabilir (yapılandırıldıysa).
3. Kullanıcı bir Federasyon kimlik sağlayıcısı kullanarak oturum açarsa, Azure AD B2C için bir belirteç yanıtı gönderilir.
4. Azure AD B2C bir SAML onaylama işlemi oluşturur ve uygulamayı uygulamaya gönderir.

## <a name="prerequisites"></a>Önkoşullar

* [Azure AD B2C özel ilkeleri kullanmaya başlama](tutorial-create-user-flows.md?pivots=b2c-custom-policy)bölümündeki adımları uygulayın. Makalesinde açıklanan özel ilke başlangıç paketinden *SocialAndLocalAccounts* özel ilkesine ihtiyacınız vardır.
* SAML protokolünün temel olarak anlaşılmasına ve uygulamanın SAML uygulamasına aşina olma.
* SAML uygulaması olarak yapılandırılmış bir Web uygulaması. Bu öğreticide, sağladığımız bir [SAML test uygulaması][samltest] kullanabilirsiniz.

## <a name="components"></a>Bileşenler

Bu senaryo için gereken üç ana bileşen vardır:

* SAML AuthN isteklerini gönderebilme ve Azure AD B2C SAML yanıtlarını alma, kodunu çözme ve doğrulama yeteneğine sahip bir SAML **uygulaması** . SAML uygulaması, bağlı olan taraf uygulaması veya hizmet sağlayıcısı olarak da bilinir.
* SAML uygulamasının genel kullanıma açık SAML **meta veri noktası** veya XML belgesi.
* [Azure AD B2C kiracısı](tutorial-create-tenant.md)

Henüz bir SAML uygulamanız ve ilişkili meta veri uç noktası yoksa, test için kullanılabilir hale yaptığımız bu örnek SAML uygulamasını kullanabilirsiniz:

[SAML test uygulaması][samltest]

## <a name="set-up-certificates"></a>Sertifikaları ayarlama

Uygulamanız ve Azure AD B2C arasında bir güven ilişkisi oluşturmak için her iki hizmetin da bunların imzalarını oluşturup doğrulayabilmesi gerekir. Azure AD B2C ve uygulamanızda bir yapılandırma x509 sertifikası yapılandırırsınız.

**Uygulama sertifikaları**

| Kullanım | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| SAML istek imzalama  | No | Web uygulamanızda depolanan özel anahtara sahip bir sertifika, uygulamanız tarafından Azure AD B2C gönderilen SAML isteklerini imzalamak için kullanılır. Web uygulaması, ortak anahtarı SAML meta veri uç noktası aracılığıyla kullanıma sunmalıdır. Azure AD B2C, uygulama meta verilerinden ortak anahtar kullanarak SAML istek imzasını doğrular.|
| SAML onaylama şifrelemesi  | No | Web uygulamanızda depolanan özel anahtara sahip bir sertifika. Web uygulaması, ortak anahtarı SAML meta veri uç noktası aracılığıyla kullanıma sunmalıdır. Azure AD B2C, ortak anahtarı kullanarak uygulamanıza yapılan onayları şifreleyebilir. Uygulama, onay şifresini çözmek için özel anahtarı kullanır.|

**Azure AD B2C sertifikaları**

| Kullanım | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| SAML yanıt imzalama | Yes  | Azure AD B2C içinde depolanan özel anahtara sahip bir sertifika. Bu sertifika, uygulamanıza gönderilen SAML Yanıtını imzalamak için Azure AD B2C tarafından kullanılır. Uygulamanız SAML yanıtının imzasını doğrulamak için Azure AD B2C meta veri ortak anahtarını okur. |
| SAML onaylama imzalama | Yes | Azure AD B2C içinde depolanan özel anahtara sahip bir sertifika. Bu sertifika, SAML yanıtının onayını imzalamak için Azure AD B2C tarafından kullanılır. `<saml:Assertion>`SAML yanıtının parçası.  |

Bir üretim ortamında, bir ortak sertifika yetkilisi tarafından verilen sertifikaların kullanılmasını öneririz. Bununla birlikte, otomatik olarak imzalanan sertifikalarla da bu yordamı tamamlayabilirsiniz.

### <a name="create-a-policy-key"></a>İlke anahtarı oluşturma

Uygulamanız ve Azure AD B2C arasında bir güven ilişkisine sahip olmak için bir SAML yanıt imzalama sertifikası oluşturun. Azure AD B2C, uygulamanıza gönderilen SAML Yanıtını imzalamak için bu sertifikayı kullanır. Uygulamanız SAML yanıtının imzasını doğrulamak için Azure AD B2C meta veri ortak anahtarını okur. 

> [!TIP]
> Bu bölümde, [SAML onaylama](saml-service-provider-options.md#saml-assertions-signature)'da oturum açma gibi başka amaçlar için oluşturduğunuz ilke anahtarını kullanabilirsiniz. 

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
1. İlke anahtarı için bir **ad** girin. Örneğin, `SamlIdpCert`. Ön ek, `B2C_1A_` anahtarınızın adına otomatik olarak eklenir.
1. Özel anahtarla Certificate. pfx dosyanıza gidin ve bu dosyayı seçin.
1. **Oluştur**’a tıklayın.

## <a name="enable-your-policy-to-connect-with-a-saml-application"></a>İlkenizin SAML uygulamasıyla bağlantı kurmasını sağlama

SAML uygulamanıza bağlanmak için Azure AD B2C SAML yanıtları oluşturabilebilmelidir.

`SocialAndLocalAccounts\` **`TrustFrameworkExtensions.xml`** Özel ilke başlangıç paketinde açın.

`<ClaimsProviders>`SAML yanıt oluşturucuyu uygulamak için bölümünü bulun ve AŞAĞıDAKI XML kod parçacığını ekleyin.

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>

    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <Item Key="IssuerUri">https://issuerUriMyAppExpects</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
        <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
      </CryptographicKeys>
      <InputClaims/>
      <OutputClaims/>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-issuer"/>
    </TechnicalProfile>

    <!-- Session management technical profile for SAML based tokens -->
    <TechnicalProfile Id="SM-Saml-issuer">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
    </TechnicalProfile>

  </TechnicalProfiles>
</ClaimsProvider>
```

#### <a name="configure-the-issueruri-of-the-saml-response"></a>SAML yanıtının ıssuerurı 'sini yapılandırın

`IssuerUri`SAML belirteci verenin teknik profilindeki meta veri öğesinin değerini değiştirebilirsiniz. Bu değişiklik, `issuerUri` Azure AD B2C SAML yanıtında döndürülen özniteliğinde yansıtılır. Uygulamanız SAML yanıt doğrulaması sırasında aynısını kabul edecek şekilde yapılandırılmalıdır `issuerUri` .

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <Item Key="IssuerUri">https://issuerUriMyAppExpects</Item>
      </Metadata>
      ...
    </TechnicalProfile>
```

## <a name="configure-your-policy-to-issue-a-saml-response"></a>Bir SAML yanıtı vermek için ilkenizi yapılandırma

İlkenizin SAML yanıtları oluşturdığına göre, ilkeyi uygulamanıza varsayılan JWT yanıtı yerine bir SAML yanıtı verecek şekilde yapılandırmanız gerekir.

### <a name="create-a-sign-up-or-sign-in-policy-configured-for-saml"></a>SAML için yapılandırılmış bir kaydolma veya oturum açma ilkesi oluşturma

1. *SignUpOrSignin.xml* dosyanın bir kopyasını Starter Pack çalışma dizininizde oluşturun ve yeni bir adla kaydedin. Örneğin, *SignUpOrSigninSAML.xml*. Bu dosya, bağlı olan taraf ilke dosyasıdır ve varsayılan olarak bir JWT yanıtı vermek üzere yapılandırılmıştır.

1. *SignUpOrSigninSAML.xml* dosyasını tercih ettiğiniz düzenleyicide açın.

1. `PolicyId` `PublicPolicyUri` İlkenin ve _B2C_1A_signup_signin_saml_ olarak ve `http://<tenant-name>.onmicrosoft.com/B2C_1A_signup_signin_saml` aşağıda görüldüğü gibi değiştirin.

    ```xml
    <TrustFrameworkPolicy
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
    PolicySchemaVersion="0.3.0.0"
    TenantId="tenant-name.onmicrosoft.com"
    PolicyId="B2C_1A_signup_signin_saml"
    PublicPolicyUri="http://<tenant-name>.onmicrosoft.com/B2C_1A_signup_signin_saml">
    ```

1. Kullanıcı yolculuğu sonunda Azure AD B2C bir `SendClaims` adım içerir. Bu adım, belirteç verenin teknik profiline başvurur. Varsayılan JWT yanıtı yerine bir SAML yanıtı vermek için, `SendClaims` adımı yenı SAML belirteci verenin teknik profiline başvuracak şekilde değiştirin `Saml2AssertionIssuer` .

Aşağıdaki XML kod parçacığını öğesinden hemen önce ekleyin `<RelyingParty>` . Bu XML, _Signuporsignın_ Kullanıcı yolculuğunda düzenleme adımı numarası 7 ' nin üzerine yazar. Başlangıç paketindeki farklı bir klasörden başladıysanız veya düzenleme adımlarını ekleyerek veya kaldırarak Kullanıcı yolculuğu 'nı özelleştirdiyseniz, öğedeki sayının, `order` belirteç verenin adımında Kullanıcı yolculuğu 'nda belirtilen sayıya karşılık geldiğinden emin olun. Örneğin, diğer başlangıç paketi klasörlerinde, karşılık gelen adım numarası 4 `LocalAccounts` , için 6 `SocialAccounts` ve 9 için `SocialAndLocalAccountsWithMfa` ).

```xml
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
    <OrchestrationSteps>
      <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="Saml2AssertionIssuer"/>
    </OrchestrationSteps>
  </UserJourney>
</UserJourneys>
```

Bağlı olan taraf öğesi, uygulamanızın kullandığı Protokolü belirler. Varsayılan değer: `OpenId`. `Protocol`Öğesi olarak değiştirilmelidir `SAML` . Çıkış talepleri, SAML onaylaması için talep eşlemesini oluşturacaktır.

`<TechnicalProfile>`Öğesi içindeki öğesinin tamamını `<RelyingParty>` aşağıdaki teknık profil XML ile değiştirin. `tenant-name`Azure AD B2C kiracınızın adıyla güncelleştirin.

```xml
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="objectId"/>
      </OutputClaims>
      <SubjectNamingInfo ClaimType="objectId" ExcludeAsClaim="true"/>
    </TechnicalProfile>
```

Son bağlı olan taraf ilkesi dosyanız aşağıdaki XML kodu gibi görünmelidir:

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<TrustFrameworkPolicy
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="http://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="contoso.onmicrosoft.com"
  PolicyId="B2C_1A_signup_signin_saml"
  PublicPolicyUri="http://contoso.onmicrosoft.com/B2C_1A_signup_signin_saml">

  <BasePolicy>
    <TenantId>contoso.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>

  <UserJourneys>
    <UserJourney Id="SignUpOrSignIn">
      <OrchestrationSteps>
        <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="Saml2AssertionIssuer"/>
      </OrchestrationSteps>
    </UserJourney>
  </UserJourneys>

  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="objectId"/>
      </OutputClaims>
      <SubjectNamingInfo ClaimType="objectId" ExcludeAsClaim="true"/>
    </TechnicalProfile>
  </RelyingParty>
</TrustFrameworkPolicy>
```

> [!NOTE]
> Diğer Kullanıcı akışı türlerini (örneğin, oturum açma, parola sıfırlama veya profil düzenlemesi akışları) uygulamak için aynı işlemi izleyebilirsiniz.

### <a name="upload-your-policy"></a>İlkenizi karşıya yükleyin

Değişikliklerinizi kaydedin ve yeni **TrustFrameworkExtensions.xml** ve **SignUpOrSigninSAML.xml** ilke dosyalarını Azure Portal yükleyin.

### <a name="test-the-azure-ad-b2c-idp-saml-metadata"></a>IDP SAML meta verilerini Azure AD B2C test etme

İlke dosyaları karşıya yüklendikten sonra Azure AD B2C, uygulama tarafından kullanılacak kimlik sağlayıcısının SAML meta veri belgesini oluşturmak için yapılandırma bilgilerini kullanır. SAML meta veri belgesi, oturum açma ve oturum kapatma yöntemleri, sertifikalar vb. gibi hizmetlerin konumlarını içerir.

Azure AD B2C ilkesi meta verileri aşağıdaki URL 'de kullanılabilir:

`https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/samlp/metadata`

`<tenant-name>`Azure AD B2C kiracınızın adıyla ve `<policy-name>` ilkenin adı (kimliği) ile değiştirin, örneğin:

`https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1A_signup_signin_saml/samlp/metadata`

## <a name="register-your-saml-application-in-azure-ad-b2c"></a>SAML uygulamanızı Azure AD B2C kaydetme

Uygulamanıza güvenmek Azure AD B2C için, uygulamanın meta veri uç noktası gibi yapılandırma bilgilerini içeren bir Azure AD B2C uygulama kaydı oluşturursunuz.

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Üst menüden **Dizin + abonelik** filtresi ' ni seçin ve ardından Azure AD B2C kiracınızı içeren dizini seçin.
1. Sol menüden **Azure AD B2C**' yi seçin. Ya da **tüm hizmetler** ' i seçin ve **Azure AD B2C** seçin.
1. **Uygulama kayıtları** öğesini seçin ve ardından **Yeni kayıt**' ı seçin.
1. Uygulama için bir **ad** girin. Örneğin, *SAMLApp1*.
1. **Desteklenen hesap türleri** altında **yalnızca bu kuruluş dizinindeki hesaplar**' ı seçin.
1. **Yeniden yönlendirme URI 'si** altında **Web**' i seçin ve ardından girin `https://localhost` . Bu değeri daha sonra uygulama kaydının bildiriminde değiştirirsiniz.
1. **Kaydet**’i seçin.

### <a name="configure-your-application-in-azure-ad-b2c"></a>Uygulamanızı Azure AD B2C yapılandırma

SAML uygulamaları için, uygulama kaydı bildiriminde çeşitli özellikleri yapılandırmanız gerekir.

1. [Azure Portal](https://portal.azure.com), önceki bölümde oluşturduğunuz uygulama kaydına gidin.
1. **Yönet** altında, bildirim düzenleyicisini açmak için **bildirim** ' ı seçin ve ardından aşağıdaki bölümlerde açıklanan özellikleri değiştirin.

#### <a name="add-the-identifier"></a>Tanımlayıcıyı ekleyin

SAML uygulamanız Azure AD B2C bir istek yaptığında, SAML AuthN isteği, `Issuer` genellikle uygulamanın meta verileri ile aynı değer olan bir özniteliği içerir `entityID` . Azure AD B2C, dizinde uygulama kaydını aramak ve yapılandırmayı okumak için bu değeri kullanır. Bu aramanın başarılı olması için, `identifierUri` uygulama kaydında özniteliği özniteliğiyle eşleşen bir değerle doldurulmalıdır `Issuer` .

Kayıt bildiriminde, `identifierURIs` parametresini bulun ve uygun değeri ekleyin. Bu değer, uygulamadaki EntityId için SAML AuthN istekleri ve `entityID` uygulamanın meta verilerindeki değeri ile aynı olacaktır.

Aşağıdaki örnek, `entityID` SAML meta verilerinde öğesini göstermektedir:

```xml
<EntityDescriptor ID="id123456789" entityID="https://samltestapp2.azurewebsites.net" validUntil="2099-12-31T23:59:59Z" xmlns="urn:oasis:names:tc:SAML:2.0:metadata">
```

`identifierUris`Özelliği yalnızca etki alanındaki URL 'leri kabul eder `tenant-name.onmicrosoft.com` .

```json
"identifierUris":"https://samltestapp2.azurewebsites.net",
```

#### <a name="share-the-applications-metadata-with-azure-ad-b2c"></a>Uygulamanın meta verilerini Azure AD B2C ile paylaşma

Uygulama kaydı tarafından yüklendikten sonra `identifierUri` , Azure AD B2C SAML AuthN isteğini doğrulamak ve nasıl yanıt verileceğini öğrenmek için uygulamanın meta verilerini kullanır.

Uygulamanızın genel olarak erişilebilen bir meta veri uç noktası kullanıma sunabilmeniz önerilir.

SAML meta veri URL 'SI ve  uygulama kaydının bildiriminde belirtilen özellikler varsa, bunlar *birleştirilir*. Meta veri URL 'sinde belirtilen özellikler önce işlenir ve öncelik kazanır.

SAML test uygulamasını örnek olarak kullanarak, uygulama bildiriminde için aşağıdaki değeri kullanın `samlMetadataUrl` :

```json
"samlMetadataUrl":"https://samltestapp2.azurewebsites.net/Metadata",
```

#### <a name="override-or-set-the-assertion-consumer-url-optional"></a>Onaylama tüketici URL 'sini geçersiz kıl veya ayarla (isteğe bağlı)

Azure AD B2C SAML yanıtlarını gönderen yanıt URL 'sini yapılandırabilirsiniz. Yanıt URL 'Leri uygulama bildirimi içinde yapılandırılabilir. Bu yapılandırma, uygulamanız herkese açık olarak erişilebilen bir meta veri uç noktası sunmazsa faydalıdır.

Bir SAML uygulamasının yanıt URL 'SI, uygulamanın SAML yanıtlarını almasını beklediği uç noktadır. Uygulama genellikle bu URL 'YI `AssertionConsumerServiceUrl` aşağıda gösterildiği gibi özniteliğin altındaki meta veri belgesinde sağlar:

```xml
<SPSSODescriptor AuthnRequestsSigned="false" WantAssertionsSigned="false" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
    ...
    <AssertionConsumerService index="0" isDefault="true" Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST" Location="https://samltestapp2.azurewebsites.net/SP/AssertionConsumer" />        
</SPSSODescriptor>
```

Özniteliğinde belirtilen meta verileri geçersiz kılmak istiyorsanız `AssertionConsumerServiceUrl` veya meta veri BELGESINDE URL yoksa, özelliğin altındaki bildirimde URL 'yi yapılandırabilirsiniz `replyUrlsWithType` . , `BindingType` Olarak ayarlanır `HTTP POST` .

SAML test uygulamasını örnek olarak kullanarak, `url` özelliğini `replyUrlsWithType` aşağıdaki JSON kod parçacığında gösterilen değere ayarlarsınız.

```json
"replyUrlsWithType":[
  {
    "url":"https://samltestapp2.azurewebsites.net/SP/AssertionConsumer",
    "type":"Web"
  }
],
```

#### <a name="override-or-set-the-logout-url-optional"></a>Oturum kapatma URL 'sini geçersiz kıl veya ayarla (isteğe bağlı)

Oturumu kapatma isteğinden sonra Azure AD B2C kullanıcının gönderebileceği oturum kapatma URL 'sini yapılandırabilirsiniz. Yanıt URL 'Leri uygulama bildirimi içinde yapılandırılabilir.

Özniteliğinde belirtilen meta verileri geçersiz kılmak istiyorsanız `SingleLogoutService` veya meta veri BELGESINDE URL yoksa, özelliği altındaki bildirimde yapılandırabilirsiniz `Logout` . , `BindingType` Olarak ayarlanır `Http-Redirect` .

Uygulama genellikle bu URL 'YI `AssertionConsumerServiceUrl` aşağıda gösterildiği gibi özniteliğin altındaki meta veri belgesinde sağlar:

```xml
<IDPSSODescriptor WantAuthnRequestsSigned="false" WantAssertionsSigned="false" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://samltestapp2.azurewebsites.net/logout" ResponseLocation="https://samltestapp2.azurewebsites.net/logout" />

</IDPSSODescriptor>
```

SAML test uygulamasını örnek olarak kullanarak, şu `logoutUrl` şekilde ayarlı bırakın `https://samltestapp2.azurewebsites.net/logout` :

```json
"logoutUrl": "https://samltestapp2.azurewebsites.net/logout",
```

> [!NOTE]
> Uygulamanın meta veri uç noktasını özelliği aracılığıyla doldurmadan uygulama bildiriminde yanıt URL 'SI ve oturum kapatma URL 'sini yapılandırmayı seçerseniz `samlMetadataUrl` , Azure AD B2C SAML isteği imzasını doğrulamaz veya SAML yanıtını şifreleyebilir.

## <a name="configure-azure-ad-b2c-as-a-saml-idp-in-your-saml-application"></a>SAML uygulamanızda SAML IDP olarak Azure AD B2C yapılandırma

Son adım, SAML uygulamanızda SAML IDP olarak Azure AD B2C etkinleştirmektir. Her uygulama farklıdır ve adımlar farklılık gösterir. Ayrıntılar için uygulamanızın belgelerine başvurun.

Meta veriler, uygulamanızda *statik meta veriler* veya *dinamik meta veriler* olarak yapılandırılabilir. Statik modda meta verilerin tümünü veya bir kısmını Azure AD B2C ilkesi meta verilerinden kopyalayın. Dinamik modda meta verilerin URL 'sini sağlayın ve uygulamanızın meta verileri dinamik olarak okumasına izin verin.

Aşağıdakilerin bazıları veya tümü genellikle gereklidir:

* **Meta veri**: biçimi kullanın `https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/Samlp/metadata` .
* **Veren**: SAML istek `issuer` değeri, `identifierUris` uygulama kayıt bildirimi öğesinde yapılandırılan URI 'lerden biriyle eşleşmelidir. SAML istek `issuer` adı `identifierUris` öğesinde yoksa, [uygulamayı uygulama kayıt bildirimine ekleyin](#add-the-identifier). Örneğin, `https://contoso.onmicrosoft.com/app-name`. 
* **Oturum açma URL 'si/SAML uç noktası/SAML URL 'si**: XML öğesi IÇIN Azure AD B2C SAML ilkesi meta verileri dosyasındaki değeri denetleyin `<SingleSignOnService>` .
* **Sertifika**: Bu sertifika, özel anahtar olmadan *B2C_1A_SamlIdpCert*. Sertifikanın ortak anahtarını almak için:

    1. Yukarıda belirtilen meta veri URL 'sine gidin.
    1. Öğesindeki değeri kopyalayın `<X509Certificate>` .
    1. Bir metin dosyasına yapıştırın.
    1. Metin dosyasını *. cer* dosyası olarak kaydedin.

### <a name="test-with-the-saml-test-app"></a>SAML test uygulamasıyla test etme

Yapılandırmanızı test etmek için [SAML test uygulamamızı][samltest] kullanabilirsiniz:

* Kiracı adını güncelleştirin.
* İlke adını güncelleştirin, örneğin *B2C_1A_signup_signin_saml*.
* Bu veren URI 'sini belirtin. Uygulama kayıt bildirimindeki öğesinde bulunan URI 'lerden birini kullanın `identifierUris` , örneğin `https://contoso.onmicrosoft.com/app-name` .

**Oturum aç** ' ı seçin ve Kullanıcı oturum açma ekranı ile karşılaşırsınız. Oturum açma sırasında, bir SAML yanıtı örnek uygulamaya geri verilir.

## <a name="supported-and-unsupported-saml-modalities"></a>Desteklenen ve desteklenmeyen SAML modalities

Aşağıdaki SAML uygulama senaryoları kendi meta veri uç noktanız aracılığıyla desteklenir:

* Uygulama/hizmet sorumlusu nesnesinde oturum kapatma URL 'SI için çoklu oturum kapatma URL 'Leri veya GÖNDERI bağlama.
* Uygulama/hizmet sorumlusu nesnesindeki bağlı olan taraf (RP) isteklerini doğrulamak için bir imzalama anahtarı belirtin.
* Uygulama/hizmet sorumlusu nesnesinde bir belirteç şifreleme anahtarı belirtin.
* Kimlik sağlayıcısı-kimlik sağlayıcısı Azure AD B2C, oturum açma başlatıldı.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure AD B2C GitHub topluluk DEPOSUNDAN](https://github.com/azure-ad-b2c/saml-sp-tester)SAML test Web uygulamasını alın.
- [Azure AD B2C BIR SAML uygulaması kaydetme seçeneklerine](saml-service-provider-options.md) bakın

<!-- LINKS - External -->
[samltest]: https://aka.ms/samltestapp

::: zone-end
