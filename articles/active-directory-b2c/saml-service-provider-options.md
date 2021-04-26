---
title: SAML hizmeti sağlayıcısı seçeneklerini yapılandırın
title-suffix: Azure Active Directory B2C
description: Azure Active Directory B2C SAML hizmeti sağlayıcısı seçeneklerini yapılandırma
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
ms.openlocfilehash: fea39388b6b4387dfc4fe95d1cdfb3e523a8089c
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106382445"
---
# <a name="options-for-registering-a-saml-application-in-azure-ad-b2c"></a>Azure AD B2C SAML uygulaması kaydetme seçenekleri

Bu makalede, SAML uygulamanızla Azure Active Directory (Azure AD B2C) bağlanırken kullanılabilen yapılandırma seçenekleri açıklanmaktadır.

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"


## <a name="saml-response-signature"></a>SAML yanıt imzası

SAML iletilerini imzalamak için kullanılacak bir sertifika belirtebilirsiniz. İleti, bir `<samlp:Response>` SAML yanıtı İçindeki öğesidir ve uygulamaya gönderilir.

Zaten bir ilke anahtarınız yoksa [bir tane oluşturun](saml-service-provider.md#create-a-policy-key). Ardından, `SamlMessageSigning` SAML belirteci verenin teknik profilindeki meta veri öğesini yapılandırın. , `StorageReferenceId` Ilke anahtarı adına başvurmalıdır.

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
        ...
      <CryptographicKeys>
        <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SamlMessageCert"/>
        ...
      </CryptographicKeys>
    ...
    </TechnicalProfile>
```

### <a name="saml-response-signature-algorithm"></a>SAML yanıtı imza algoritması

SAML onaylama işlemi imzalamak için kullanılan imza algoritmasını yapılandırabilirsiniz. Olası değerler şunlardır,, `Sha256` `Sha384` `Sha512` veya `Sha1` . Teknik profilin ve uygulamanın aynı imza algoritmasını kullanmasından emin olun. Yalnızca sertifikanızın desteklediği algoritmayı kullanın.

`XmlSignatureAlgorithm`Bağlı olan taraf meta veri öğesi içindeki meta veri anahtarını kullanarak imza algoritmasını yapılandırın.

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="SAML2"/>
    <Metadata>
      <Item Key="XmlSignatureAlgorithm">Sha256</Item>
    </Metadata>
   ..
  </TechnicalProfile>
</RelyingParty>
```

## <a name="saml-assertions-signature"></a>SAML onaylama imzası

Uygulamanız SAML onaylama bölümünün imzalanmasını beklediğinde, SAML hizmeti sağlayıcısının ' ı ' olarak ayarladığınızdan emin olun `WantAssertionsSigned` `true` . Olarak ayarlanırsa `false` veya yoksa onaylama bölümü işaret etmez. Aşağıdaki örnek, olarak ayarlanmış bir SAML hizmet sağlayıcısı meta verilerini gösterir `WantAssertionsSigned` `true` .

```xml
<EntityDescriptor ID="id123456789" entityID="https://samltestapp2.azurewebsites.net" validUntil="2099-12-31T23:59:59Z" xmlns="urn:oasis:names:tc:SAML:2.0:metadata">
  <SPSSODescriptor  WantAssertionsSigned="true" AuthnRequestsSigned="false" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
  ...
  </SPSSODescriptor>
</EntityDescriptor>
```  

### <a name="saml-assertions-signature-certificate"></a>SAML onayları imza sertifikası

İlkenize SAML yanıtının SAML onayları bölümünü imzalamak için kullanılacak bir sertifika belirtilmelidir. Zaten bir ilke anahtarınız yoksa [bir tane oluşturun](saml-service-provider.md#create-a-policy-key). Ardından, `SamlAssertionSigning` SAML belirteci verenin teknik profilindeki meta veri öğesini yapılandırın. , `StorageReferenceId` Ilke anahtarı adına başvurmalıdır.

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
        ...
      <CryptographicKeys>
        <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_SamlMessageCert"/>
        ...
      </CryptographicKeys>
    ...
    </TechnicalProfile>
```

## <a name="saml-assertions-encryption"></a>SAML onayları şifrelemesi

Uygulamanız SAML onayları 'nin şifreli bir biçimde olmasını beklediği zaman, Azure AD B2C ilkesinde şifrelemenin etkinleştirildiğinden emin olmanız gerekir.

Azure AD B2C, SAML onaylama işlemi şifrelemek için hizmet sağlayıcısının ortak anahtar sertifikasını kullanır. Ortak anahtar, aşağıdaki örnekte gösterildiği gibi, ' Use ' anahtar tanımlayıcısı ' Encryption ' olarak ayarlanmış olan SAML uygulamasının meta veri uç noktasında bulunmalıdır:

```xml
<KeyDescriptor use="encryption">
  <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
    <X509Data>
      <X509Certificate>valid certificate</X509Certificate>
    </X509Data>
  </KeyInfo>
</KeyDescriptor>
```

Azure AD B2C şifreli onaylar göndermek üzere etkinleştirmek için, **WantsEncryptedAssertion** meta veri öğesini `true` [bağlı olan taraf teknik profilinde](relyingparty.md#technicalprofile)olarak ayarlayın. SAML onayını şifrelemek için kullanılan algoritmayı da yapılandırabilirsiniz.

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="SAML2"/>
    <Metadata>
      <Item Key="WantsEncryptedAssertions">true</Item>
    </Metadata>
   ..
  </TechnicalProfile>
</RelyingParty>
```

### <a name="encryption-method"></a>Şifreleme yöntemi

SAML onaylama verilerini şifrelemek için kullanılan şifreleme yöntemini yapılandırmak için, `DataEncryptionMethod` bağlı olan taraf içindeki meta veri anahtarını ayarlayın. Olası değerler şunlardır `Aes256` (varsayılan), `Aes192` , `Sha512` , veya `Aes128` . Meta veri, `<EncryptedData>` SAML yanıtında öğesinin değerini denetler.

SAML onaylama verilerini şifrelemek için kullanılan anahtarın kopyasını şifrelemek için kullanılan şifreleme yöntemini yapılandırmak için, `KeyEncryptionMethod` bağlı olan taraf içindeki meta veri anahtarını ayarlayın. Olası değerler `Rsa15` (varsayılan)-RSA ortak anahtar şifreleme standardı (PKCS) sürüm 1,5 algoritması ve `RsaOaep` -RSA En Iyi asimetrik şifreleme doldurma (OAEP) şifreleme algoritması.  Meta veri,  `<EncryptedKey>` SAML yanıtında öğesinin değerini denetler.

Aşağıdaki örnek, `EncryptedAssertion` BIR SAML onay bölümünün bölümünü gösterir. Şifrelenmiş veri yöntemi, `Aes128` ve şifreli anahtar yöntemidir `Rsa15` .

```xml
<saml:EncryptedAssertion>
  <xenc:EncryptedData xmlns:xenc="http://www.w3.org/2001/04/xmlenc#"
    xmlns:dsig="http://www.w3.org/2000/09/xmldsig#" Type="http://www.w3.org/2001/04/xmlenc#Element">
    <xenc:EncryptionMethod Algorithm="http://www.w3.org/2001/04/xmlenc#aes128-cbc" />
    <dsig:KeyInfo>
      <xenc:EncryptedKey>
        <xenc:EncryptionMethod Algorithm="http://www.w3.org/2001/04/xmlenc#rsa-1_5" />
        <xenc:CipherData>
          <xenc:CipherValue>...</xenc:CipherValue>
        </xenc:CipherData>
      </xenc:EncryptedKey>
    </dsig:KeyInfo>
    <xenc:CipherData>
      <xenc:CipherValue>...</xenc:CipherValue>
    </xenc:CipherData>
  </xenc:EncryptedData>
</saml:EncryptedAssertion>
```

Şifrelenmiş onayların biçimini değiştirebilirsiniz. Şifreleme biçimini yapılandırmak için, `UseDetachedKeys` bağlı olan taraf içindeki meta veri anahtarını ayarlayın. Olası değerler: `true` , veya `false` (varsayılan). Değer olarak ayarlandığında `true` , ayrılmış anahtarlar şifreli onaylama 'yı öğesinin bir alt öğesi olarak `EncrytedAssertion` öğesine ekler `EncryptedData` .

Şifreleme yöntemini ve biçimini yapılandırın, [bağlı olan taraf teknik profili](relyingparty.md#technicalprofile)içindeki meta veri anahtarlarını kullanın:

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="SAML2"/>
    <Metadata>
      <Item Key="DataEncryptionMethod">Aes128</Item>
      <Item Key="KeyEncryptionMethod">Rsa15</Item>
      <Item Key="UseDetachedKeys">false</Item>
    </Metadata>
   ..
  </TechnicalProfile>
</RelyingParty>
```

## <a name="identity-provider-initiated-flow"></a>Kimlik sağlayıcısı-başlatılan akış

Uygulamanız, kimlik sağlayıcısına bir SAML AuthN isteği göndermeden önce bir SAML onayı almayı bekliyorsa, kimlik sağlayıcısı tarafından başlatılan Flow Azure AD B2C yapılandırmanız gerekir.

Kimlik sağlayıcısı tarafından başlatılan akışta, oturum açma işlemi, hizmet sağlayıcısına (bağlı olan taraf uygulamanız) istenmeyen bir SAML yanıtı gönderen kimlik sağlayıcısı tarafından başlatılır (Azure AD B2C).

Şu anda, başlatma kimlik sağlayıcısı 'nın Azure AD B2C, örneğin [AD-FS](identity-provider-adfs.md)veya [Salesforce](identity-provider-salesforce-saml.md)gibi Federasyon federal bir dış kimlik sağlayıcısı olduğu senaryoları desteklemiyoruz. Yalnızca Azure AD B2C yerel hesap kimlik doğrulaması için desteklenir.

Kimlik sağlayıcısı tarafından başlatılan akışı etkinleştirmek için, **ıdpınitiated Profileenabled** meta veri öğesini `true` [bağlı olan taraf teknik profilinde](relyingparty.md#technicalprofile)olarak ayarlayın.

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="SAML2"/>
    <Metadata>
      <Item Key="IdpInitiatedProfileEnabled">true</Item>
    </Metadata>
   ..
  </TechnicalProfile>
</RelyingParty>
```

Kimlik sağlayıcısı tarafından başlatılan Flow aracılığıyla oturum açmak veya Kullanıcı kaydolmak için aşağıdaki URL 'YI kullanın:

```
https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/generic/login?EntityId=app-identifier-uri 
```

Aşağıdaki değerleri değiştirin:

* Kiracı adınızla kiracı **adı**
* **ilke-** SAML bağlı olan taraf ilkesi adınızla adlandırın
* **App-Identifier-** `identifierUris` meta veri dosyasında ile URI `https://contoso.onmicrosoft.com/app-name`

### <a name="sample-policy"></a>Örnek ilke

SAML test uygulaması ile test etmek için kullanabileceğiniz, örnek bir ilke sağlıyoruz.

1. [SAML-SP tarafından başlatılan oturum açma örnek ilkesini](https://github.com/azure-ad-b2c/saml-sp/tree/master/policy/SAML-SP-Initiated)indirin.
1. `TenantId`Kiracı adınızla eşleşecek şekilde güncelleştirin, örneğin *contoso.b2clogin.com*.
1. İlke adını *B2C_1A_signup_signin_saml* tutun.

## <a name="saml-response-lifetime"></a>SAML yanıtı ömrü

SAML yanıtının geçerli kaldığı sürenin uzunluğunu yapılandırabilirsiniz. `TokenLifeTimeInSeconds`SAML belirteci verenin teknik profili içindeki meta veri öğesini kullanarak yaşam süresini ayarlayın. Bu değer, `NotBefore` belirteç verme sırasında hesaplanan zaman damgasından geçebilecek saniye sayısıdır. Varsayılan yaşam süresi 300 saniyedir (5 dakika).

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <Item Key="TokenLifeTimeInSeconds">400</Item>
      </Metadata>
      ...
    </TechnicalProfile>
```

## <a name="saml-response-valid-from-skew"></a>SAML yanıtı, eğriltiden geçerli

SAML yanıt zaman damgasına uygulanan zaman çarpıklığı yapılandırabilirsiniz `NotBefore` . Bu yapılandırma, iki platformdaki süreler eşitlenmemişse, bu zaman eğriliği dahilinde SAML onaylama 'nın hala geçerli kabul edilir olmasını sağlar.

`TokenNotBeforeSkewInSeconds`SAML belirteci verenin teknik profili içindeki meta veri öğesini kullanarak zaman eğriliğini ayarlayın. Eğriliği değeri saniye cinsinden varsayılan değeri 0 ile verilir. En büyük değer 3600 ' dir (bir saat).

Örneğin, `TokenNotBeforeSkewInSeconds` saniye olarak ayarlandığında `120` :

- Belirteç 13:05:10 UTC 'de verilir
- Belirteç 13:03:10 UTC 'den geçerli

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <Item Key="TokenNotBeforeSkewInSeconds">120</Item>
      </Metadata>
      ...
    </TechnicalProfile>
```

## <a name="remove-milliseconds-from-date-and-time"></a>Tarihi ve saati geçen süreyi kaldır

Bu sürenin SAML yanıtı içindeki tarih saat değerlerinden (IssueInstant, NotBefore, NotOnOrAfter ve Authnınstant), milliseconds 'in kaldırılıp kaldırılmayacağını belirtebilirsiniz. Milisaniyeyi kaldırmak için, `RemoveMillisecondsFromDateTime
` bağlı olan taraf içindeki meta veri anahtarını ayarlayın. Olası değerler: `false` (varsayılan) veya `true` .

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <Item Key="RemoveMillisecondsFromDateTime">true</Item>
      </Metadata>
      ...
    </TechnicalProfile>
```

## <a name="azure-ad-b2c-issuer-id"></a>Azure AD B2C veren KIMLIĞI

Farklı değerlere bağlı birden çok SAML uygulamanız varsa `entityID` , `issueruri` bağlı olan taraf dosyanızdaki değeri geçersiz kılabilirsiniz. Veren URI 'yi geçersiz kılmak için, teknik profili temel dosyadaki "Saml2AssertionIssuer" KIMLIĞIYLE kopyalayın ve değeri geçersiz kılın `issueruri` .

> [!TIP]
> `<ClaimsProviders>`Temel öğeden bölümü kopyalayın ve bu öğeleri talep sağlayıcısı içinde koruyun: `<DisplayName>Token Issuer</DisplayName>` , `<TechnicalProfile Id="Saml2AssertionIssuer">` ve `<DisplayName>Token Issuer</DisplayName>` .
 
Örnek:

```xml
   <ClaimsProviders>   
    <ClaimsProvider>
      <DisplayName>Token Issuer</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Saml2AssertionIssuer">
          <DisplayName>Token Issuer</DisplayName>
          <Metadata>
            <Item Key="IssuerUri">customURI</Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
  </ClaimsProviders>
  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpInSAML" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="SAML2" />
      <Metadata>
     …
```

## <a name="session-management"></a>Oturum yönetimi

`UseTechnicalProfileForSessionManagement`Öğesini ve [Samlssosessionprovider](custom-policy-reference-sso.md#samlssosessionprovider)öğesini kullanarak Azure AD B2C ve SAML bağlı olan taraf uygulaması arasındaki oturumu yönetebilirsiniz.

## <a name="force-users-to-reauthenticate"></a>Kullanıcıları yeniden kimlik doğrulaması için zorlayın 

Kullanıcıları yeniden kimlik doğrulamaya zorlamak için, uygulama `ForceAuthn` SAML kimlik doğrulama isteğine özniteliğini içerebilir. `ForceAuthn`Özniteliği bir Boole değeridir. True olarak ayarlandığında, kullanıcıların oturumu Azure AD B2C geçersiz kılınır ve Kullanıcı yeniden kimlik doğrulaması zorlanır. Aşağıdaki SAML kimlik doğrulama isteği, `ForceAuthn` özniteliği true olarak ayarlamayı gösterir. 


```xml
<samlp:AuthnRequest 
       Destination="https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1A_SAML2_signup_signin/samlp/sso/login"
       ForceAuthn="true" ...>
    ...
</samlp:AuthnRequest>
```

## <a name="sign-the-azure-ad-b2c-idp-saml-metadata"></a>IDP SAML meta verilerini Azure AD B2C imzala

Azure AD B2C, uygulama için gerekliyse SAML IDP meta veri belgesini imzalayabilmeniz için talimat verebilir. Zaten bir ilke anahtarınız yoksa [bir tane oluşturun](saml-service-provider.md#create-a-policy-key). Ardından, `MetadataSigning` SAML belirteci verenin teknik profilindeki meta veri öğesini yapılandırın. , `StorageReferenceId` İlke anahtarı adına başvurmalıdır.

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
        ...
      <CryptographicKeys>
        <Key Id="MetadataSigning" StorageReferenceId="B2C_1A_SamlMetadataCert"/>
        ...
      </CryptographicKeys>
    ...
    </TechnicalProfile>
```

## <a name="debug-the-saml-protocol"></a>SAML protokolünde hata ayıklama

Hizmet sağlayıcınızda tümleştirmeyi yapılandırmaya ve hata ayıklamanıza yardımcı olmak için, SAML protokolü için bir tarayıcı uzantısı kullanabilirsiniz; Örneğin, Chrome için [SAML DevTools uzantısı](https://chrome.google.com/webstore/detail/saml-devtools-extension/jndllhgbinhiiddokbeoeepbppdnhhio) , Firefox için [SAML izleyici](https://addons.mozilla.org/es/firefox/addon/saml-tracer/) veya [Edge ya da IE geliştirici araçları](https://techcommunity.microsoft.com/t5/microsoft-sharepoint-blog/gathering-a-saml-token-using-edge-or-ie-developer-tools/ba-p/320957).

Bu araçları kullanarak, uygulamanız ve Azure AD B2C arasındaki tümleştirmeyi kontrol edebilirsiniz. Örnek:

* SAML isteğinin imza içerip içermediğini denetleyin ve yetkilendirme isteğinde oturum açmak için hangi algoritmanın kullanılacağını saptayın.
* Azure AD B2C bir hata iletisi döndürdüğünden emin olun.
* Onaylama bölümünün şifrelendiğini kontrol edin.

## <a name="next-steps"></a>Sonraki adımlar

- [OASSıS Web sitesinde SAML Protokolü](https://www.oasis-open.org/)hakkında daha fazla bilgi edinin.
- [Azure AD B2C GitHub topluluk DEPOSUNDAN](https://github.com/azure-ad-b2c/saml-sp-tester)SAML test Web uygulamasını alın.

<!-- LINKS - External -->
[samltest]: https://aka.ms/samltestapp

::: zone-end
