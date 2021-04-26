---
title: SAML kimlik sağlayıcısı seçenekleriyle oturum açma ayarla
titleSuffix: Azure Active Directory B2C
description: Azure Active Directory B2C 'de oturum açma SAML kimlik sağlayıcısı (IDP) seçeneklerini yapılandırın.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/22/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 32f9df410dabf1902e9a7d9aadbf47288bfa90f5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104798247"
---
# <a name="configure-saml-identity-provider-options-with-azure-active-directory-b2c"></a>SAML kimlik sağlayıcısı seçeneklerini Azure Active Directory B2C ile yapılandırma

Azure Active Directory B2C (Azure AD B2C) SAML 2,0 kimlik sağlayıcılarıyla Federasyonu destekler. Bu makalede, bir SAML kimlik sağlayıcısı ile oturum açma etkinleştirilirken kullanılabilen yapılandırma seçenekleri açıklanmaktadır.

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="claims-mapping"></a>Talep eşleme

**Outputclaim** Öğesı, SAML kimlik sağlayıcısı tarafından döndürülen taleplerin bir listesini içerir. İlkenizde tanımlanan talebin adını kimlik sağlayıcısında tanımlanan adla eşlemeniz gerekir. Talepler listesi (onaylama) için kimlik sağlayıcınızı denetleyin. Kimlik sağlayıcınızın döndürdüğü SAML yanıtının içeriğini de denetleyebilirsiniz. Daha fazla bilgi için bkz. [SAML Iletilerinde hata ayıklama](#debug-saml-protocol). Talep eklemek için, önce [bir talep tanımlayın](claimsschema.md), sonra talebi çıkış talep koleksiyonuna ekleyin.

Özniteliği ayarladığınız sürece, kimlik sağlayıcısı tarafından döndürülmeyen talepleri de ekleyebilirsiniz `DefaultValue` . Varsayılan değer, [bağlam taleplerini](#enable-use-of-context-claims)kullanarak statik veya dinamik olabilir.

Çıkış talebi öğesi aşağıdaki öznitelikleri içerir:

- **ClaimTypeReferenceId** , bir talep türüne başvurudur. 
- **Partnerclaimtype** , SAML onaylama görünen özelliğin adıdır. 
- **DefaultValue** önceden tanımlanmış bir varsayılan değerdir. Talep boşsa, varsayılan değer kullanılacaktır. İlişki KIMLIĞI veya Kullanıcı IP adresi gibi bağlamsal bir değere sahip bir [talep çözümleyicilerine](claim-resolver-overview.md) de kullanabilirsiniz.

### <a name="subject-name"></a>Konu adı

**KONUDAKI** SAML onaylama **NameID** 'sini normalleştirilmiş bir talep olarak okumak Için, talep **ortağı ClaimType** değerini özniteliğin değerine ayarlayın `SPNameQualifier` . `SPNameQualifier`Öznitelik sunulmadığı takdirde, Claim **partnerclaimtype** değerini özniteliğin değerine ayarlayın `NameQualifier` .

SAML onaylama:

```xml
<saml:Subject>
  <saml:NameID SPNameQualifier="http://your-idp.com/unique-identifier" Format="urn:oasis:names:tc:SAML:2.0:nameid-format:transient">david@contoso.com</saml:NameID>
  <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
    <SubjectConfirmationData InResponseTo="_cd37c3f2-6875-4308-a9db-ce2cf187f4d1" NotOnOrAfter="2020-02-15T16:23:23.137Z" Recipient="https://<your-tenant>.b2clogin.com/<your-tenant>.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer" />
    </SubjectConfirmation>
  </saml:SubjectConfirmation>
</saml:Subject>
```

Çıkış talebi:

```xml
<OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="http://your-idp.com/unique-identifier" />
```

`SPNameQualifier` `NameQualifier` SAML onaylama işlemi içinde her iki veya öznitelik sunulmadığı takdirde, talep **partnerclaimtype** 'ı olarak ayarlayın `assertionSubjectName` . **NameID** 'nın assertion XML içindeki ilk değer olduğundan emin olun. Birden fazla onay tanımladığınızda, Azure AD B2C son onaylamanın konu değerini seçer.


## <a name="configure-saml-protocol-bindings"></a>SAML protokol bağlamalarını yapılandırma

SAML istekleri, kimlik sağlayıcısının meta veri öğesinde belirtilen kimlik sağlayıcısına gönderilir `SingleSignOnService` . Kimlik sağlayıcılarının yetkilendirme isteklerinin çoğu, bir HTTP GET isteğinin URL sorgu dizesinde doğrudan taşınır (iletiler görece kısaysa). Her iki SAML isteği için bağlamaları yapılandırmak üzere kimlik sağlayıcı belgelerinize bakın.

Aşağıda, iki bağlama sahip Azure AD meta verileri çoklu oturum açma hizmetine bir örnek verilmiştir. , `HTTP-Redirect` `HTTP-POST` SAML kimlik sağlayıcısı meta verilerinde önce göründüğünden, öğesinden önceliklidir.

```xml
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
  ...
  <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000/saml2"/>
  <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST" Location="https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000/saml2"/>
</IDPSSODescriptor>
```

### <a name="assertion-consumer-service"></a>Onaylama tüketici hizmeti

Onaylama tüketici hizmeti (veya ACS), kimlik sağlayıcısı SAML yanıtlarının Azure AD B2C tarafından gönderilebildiği ve alınabileceği yerdir. SAML yanıtları HTTP POST bağlaması aracılığıyla Azure AD B2C iletilir. ACS konumu, bağlı olan tarafın temel ilkesini işaret eder. Örneğin, bağlı ilke *B2C_1A_signup_signin*, ACS, *B2C_1A_signup_signin* *B2C_1A_TrustFrameworkBase* gibi temel ilkedir.

Aşağıda, Azure AD B2C ilkesi meta verileri onaylama tüketici hizmeti öğesine bir örnek verilmiştir. 

```xml
<SPSSODescriptor AuthnRequestsSigned="true" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
  ...
  <AssertionConsumerService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST" Location="https://your-tenant.b2clogin.com/your-tenant/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer" index="0" isDefault="true"/>
</SPSSODescriptor>
```

## <a name="configure-the-saml-request-signature"></a>SAML istek imzasını yapılandırma

Azure AD B2C, **Samlmessagesigning** şifreleme anahtarını kullanarak giden tüm kimlik doğrulama isteklerini imzalar. SAML istek imzasını devre dışı bırakmak için **Wantssignedrequests** ' i olarak ayarlayın `false` . Meta veriler olarak ayarlanırsa `false` , istek Içinden **Sigalg** ve **imza** parametreleri (sorgu dizesi veya post parametresi) atlanır.

Bu meta veriler ayrıca kimlik sağlayıcısıyla paylaşılan Azure AD B2C teknik profilin meta verilerinde yer alan **AuthnRequestsSigned** özniteliğini de denetler. Teknik profil meta verilerindeki **Wantssignedrequests** değeri olarak ayarlandıysa `false` ve **Wantauthnrequestssigned** kimlik sağlayıcısı meta verileri olarak ayarlandıysa veya belirtilmemişse isteği imzalayamıyor Azure AD B2C `false` .

Aşağıdaki örnek, SAML isteğinden imza kaldırır.

```xml
<Metadata>
  ...
  <Item Key="WantsSignedRequests">false</Item>
</Metadata>
```

### <a name="signature-algorithm"></a>İmza algoritması

Azure AD B2C `Sha1` SAML isteğini imzalamak için kullanılır. Kullanılacak algoritmayı yapılandırmak için **Xmlsignaturealgorithm** meta verilerini kullanın. Olası değerler şunlardır,, `Sha256` `Sha384` `Sha512` veya `Sha1` (varsayılan). Bu meta veriler, SAML isteğindeki  **Sigalg** parametresinin (sorgu dizesi veya post parametresi) değerini denetler. Aynı değere sahip her iki tarafta de imza algoritmasını yapılandırdığınızdan emin olun. Yalnızca sertifikanızın desteklediği algoritmayı kullanın.

### <a name="include-key-info"></a>Anahtar bilgisini dahil et

Kimlik sağlayıcısı Azure AD B2C bağlamanın olarak ayarlandığını gösteriyorsa `HTTP-POST` , Azure AD B2C, SAML isteği gövdesinde imzayı ve algoritmayı içerir. Ayrıca, Azure AD 'yi, bağlama ayarlandığında sertifikanın ortak anahtarını içerecek şekilde de yapılandırabilirsiniz `HTTP-POST` . Veya için **ıncludekeyınfo** meta verilerini kullanın `true` `false` . Aşağıdaki örnekte, Azure AD sertifikanın ortak anahtarını içermez.

```xml
<Metadata>
  ...
  <Item Key="IncludeKeyInfo">false</Item>
</Metadata>
```

## <a name="configure-saml-request-name-id"></a>SAML istek adı KIMLIĞINI yapılandırma

SAML yetkilendirme isteği `<NameID>` Öğesı SAML adı tanımlayıcı biçimini gösterir. Bu bölüm, varsayılan yapılandırmayı ve ad KIMLIĞI öğesinin nasıl özelleştirileceğini açıklar.

## <a name="preferred-username"></a>Tercih edilen Kullanıcı adı

Bir oturum açma Kullanıcı yolculuğu sırasında, bağlı olan taraf uygulaması belirli bir kullanıcıyı hedefleyebilir. Azure AD B2C, SAML Identity sağlayıcısına tercih edilen bir Kullanıcı adı göndermenizi sağlar. **Inputclaim** Öğesı, SAML yetkilendirme isteğinin **konusu** içinde bir **NameID** göndermek için kullanılır.

Kimlik doğrulama isteğine konu adı KIMLIĞINI eklemek için, `<InputClaims>` öğesinden hemen sonra aşağıdaki öğeyi ekleyin `<CryptographicKeys>` . **Partnerclaimtype** , olarak ayarlanmalıdır `subject` .

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="signInName" PartnerClaimType="subject" />
</InputClaims>
```

Bu örnekte, Azure AD B2C SAML yetkilendirme isteğinin **konusu** Içinde **NameID** ile **signınname** talebinin değerini gönderir.

```xml
<samlp:AuthnRequest ... >
  ...
  <saml:Subject>
    <saml:NameID>sam@contoso.com</saml:NameID>
  </saml:Subject>
</samlp:AuthnRequest>
```

Talep değerini doldurmak için gibi [bağlam taleplerini](claim-resolver-overview.md)kullanabilirsiniz `{OIDC:LoginHint}` .

```xml
<Metadata>
  ...
  <Item Key="IncludeClaimResolvingInClaimsHandling">true</Item>
</Metadata>
  ...
<InputClaims>
  <InputClaim ClaimTypeReferenceId="signInName" PartnerClaimType="subject" DefaultValue="{OIDC:LoginHint}" AlwaysUseDefaultValue="true" />
</InputClaims>
```

### <a name="name-id-policy-format"></a>Ad KIMLIĞI ilke biçimi

Varsayılan olarak, SAML yetkilendirme isteği `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` ilkeyi belirtir. Bu, istenen konu için kimlik sağlayıcısı tarafından desteklenen herhangi bir tür tanımlayıcının kullanılabileceğini gösterir.

Bu davranışı değiştirmek için, hangi ad KIMLIK ilkelerinin desteklendiği hakkında rehberlik için kimlik sağlayıcınızın belgelerine bakın. Ardından, `NameIdPolicyFormat` ilgili ilke biçiminde meta verileri ekleyin. Örnek:

```xml
<Metadata>
  ...
  <Item Key="NameIdPolicyFormat">urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress</Item>
</Metadata>
```

Aşağıdaki SAML yetkilendirme isteği, ad KIMLIĞI ilkesini içerir.

```xml
<samlp:AuthnRequest ... >
  ...
  <samlp:NameIDPolicy Format="urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress" />
</samlp:AuthnRequest>
```

### <a name="allow-creating-new-accounts"></a>Yeni hesap oluşturulmasına izin ver

[Ad kimlik ilkesi biçimini](#name-id-policy-format)belirtirseniz, `AllowCreate` kimlik sağlayıcısının oturum açma akışı sırasında yeni bir hesap oluşturmalarına izin verilip verilmeyeceğini belirtmek Için **nameıdpolicy** özelliğini de belirtebilirsiniz. Rehberlik için kimlik sağlayıcınızın belgelerine bakın.

Azure AD B2C `AllowCreate` Varsayılan olarak özelliği atlar. Meta verileri kullanarak bu davranışı değiştirebilirsiniz `NameIdPolicyAllowCreate` . Bu meta verilerin değeri `true` veya `false` .

Aşağıdaki örnek, öğesinin özelliğinin nasıl ayarlanacağını göstermektedir `AllowCreate` `NameIDPolicy` `true` .

```xml
<Metadata>
  ...
  <Item Key="NameIdPolicyFormat">urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress</Item>
  <Item Key="NameIdPolicyAllowCreate">true</Item>
</Metadata>
```


Aşağıdaki örnek, yetkilendirme isteğindeki **Nameıdpolicy** öğesinin **AllowCreate** ile bir yetkilendirme isteğini gösterir.


```xml
<samlp:AuthnRequest ... >
  ...
  <samlp:NameIDPolicy 
      Format="urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress" 
      AllowCreate="true" />
</samlp:AuthnRequest>
```

### <a name="include-authentication-context-class-references"></a>Kimlik doğrulama bağlamı sınıf başvurularını dahil et

SAML yetkilendirme isteği bir yetkilendirme isteğinin bağlamını belirten bir **AuthnContext** öğesi içerebilir. Öğesi, SAML kimlik sağlayıcıya kullanıcıya hangi kimlik doğrulama mekanizmasını sunulacağını söyleyen bir kimlik doğrulama bağlam sınıfı başvurusu içerebilir.

Kimlik doğrulama bağlamı sınıfı başvurularını yapılandırmak için **ıncludeauthncontextclassreferences** meta verilerini ekleyin. Değerde, kimlik doğrulama bağlamı sınıflarını tanımlayan bir veya daha fazla URI başvurusu belirtin. Birden çok URI 'yi virgülle ayrılmış bir liste olarak belirtin. Desteklenen **Authncontextclassref** URI 'leri hakkında rehberlik için kimlik sağlayıcınızın belgelerine bakın.

Aşağıdaki örnek, kullanıcıların Kullanıcı adı ve parolayla oturum açmasını ve korumalı bir oturum (SSL/TLS) üzerinde Kullanıcı adı ve parolayla oturum açmasını sağlar.

```xml
<Metadata>
  ...
  <Item Key="IncludeAuthnContextClassReferences">urn:oasis:names:tc:SAML:2.0:ac:classes:Password,urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport</Item>
</Metadata>
```

Aşağıdaki SAML yetkilendirme isteği, kimlik doğrulama bağlamı sınıfı başvurularını içerir.

```xml
<samlp:AuthnRequest ... >
  ...
  <samlp:RequestedAuthnContext>
    <saml:AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:Password</saml:AuthnContextClassRef>
    <saml:AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport</saml:AuthnContextClassRef>
  </samlp:RequestedAuthnContext>
</samlp:AuthnRequest>
```

## <a name="include-custom-data-in-the-authorization-request"></a>Yetkilendirme isteğine özel verileri dahil et

İsteğe bağlı olarak hem Azure AD BC hem de kimlik sağlayıcınız tarafından kabul edilen protokol iletisi uzantısı öğelerini ekleyebilirsiniz. Uzantı XML biçiminde sunulmuştur. XML verilerini CDATA öğesi içine ekleyerek uzantı öğeleri dahil edersiniz `<![CDATA[Your IDP metadata]]>` . Uzantı öğesinin desteklenip desteklenmediğini görmek için kimlik sağlayıcınızın belgelerini denetleyin.

Aşağıdaki örnek, uzantı verilerinin kullanımını göstermektedir:

```xml
<Metadata>
  ...
  <Item Key="AuthenticationRequestExtensions"><![CDATA[
            <ext:MyCustom xmlns:ext="urn:ext:custom">
              <ext:AssuranceLevel>1</ext:AssuranceLevel>
              <ext:AssuranceDescription>Identity verified to level 1.</ext:AssuranceDescription>
            </ext:MyCustom>]]></Item>
</Metadata>
```

Saml protokol iletisi uzantısı kullanılırken, SAML yanıtı aşağıdaki örneğe benzer şekilde görünür:

```xml
<samlp:AuthnRequest ... >
  ...
  <samlp:Extensions>
    <ext:MyCustom xmlns:ext="urn:ext:custom">
      <ext:AssuranceLevel>1</ext:AssuranceLevel>
      <ext:AssuranceDescription>Identity verified to level 1.</ext:AssuranceDescription>
    </ext:MyCustom>
  </samlp:Extensions>
</samlp:AuthnRequest>
```

## <a name="require-signed-saml-responses"></a>İmzalı SAML yanıtları gerektir

Azure AD B2C tüm gelen onayların imzalanmasını gerektirir. **Wantssignedasnotions** ' i ayarlayarak bu gereksinimi kaldırabilirsiniz `false` . Kimlik sağlayıcısı bu durumda onaylamaları imzalamamamalıdır, ancak bile Azure AD B2C imzayı doğrulamaz.

**Wantssignedassertions** meta verileri, kimlik sağlayıcısıyla paylaşılan Azure AD B2C teknik profilin meta verilerinde bulunan **WantAssertionsSigned** SAML meta verileri bayrağını denetler.

```xml
<SPSSODescriptor AuthnRequestsSigned="true" WantAssertionsSigned="true" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
```

Onaylama doğrulamasını devre dışı bırakırsanız, yanıt iletisi imza doğrulamasını devre dışı bırakmak isteyebilirsiniz. **ResponsesSigned** meta verilerini olarak ayarlayın `false` . Kimlik sağlayıcısı bu durumda SAML yanıt iletisini imzalamaz, ancak bile Azure AD B2C imzayı doğrulamaz.

Aşağıdaki örnek hem iletiyi hem de onaylama imzasını kaldırır:

```xml
<Metadata>
  ...
  <Item Key="WantsSignedAssertions">false</Item>
  <Item Key="ResponsesSigned">false</Item>
</Metadata>
```

## <a name="require-encrypted-saml-responses"></a>Şifrelenmiş SAML yanıtları gerektir

Tüm gelen onayların şifrelenmesini gerektirmek için, **WantsEncryptedAssertions** meta verilerini ayarlayın. Şifreleme gerektiğinde, kimlik sağlayıcısı bir Azure AD B2C teknik profilinde bir şifreleme sertifikasının ortak anahtarını kullanır. Azure AD B2C, şifreleme sertifikasının özel bölümünü kullanarak yanıt onaylamasının şifresini çözer.

Onaylama şifrelemesini etkinleştirirseniz, yanıt imzası doğrulamasını devre dışı bırakmanız da gerekebilir (daha fazla bilgi için bkz. [IMZALı SAML yanıtları gerektirme](#require-signed-saml-responses).

**WantsEncryptedAssertions** meta verileri olarak ayarlandığında `true` , Azure AD B2C teknik profilinin meta verileri **şifreleme** bölümünü içerir. Kimlik sağlayıcısı meta verileri okur ve Azure AD B2C teknik profilinin meta verilerinde belirtilen ortak anahtarla SAML yanıtı onayını şifreler.

Aşağıdaki örnek, şifreleme için kullanılan SAML meta verilerinin anahtar tanımlayıcı bölümünü göstermektedir:

```xml
<SPSSODescriptor AuthnRequestsSigned="true"  protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
  <KeyDescriptor use="encryption">
    <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
      <X509Data>
        <X509Certificate>valid certificate</X509Certificate>
      </X509Data>
    </KeyInfo>
  </KeyDescriptor>
  ...
</SPSSODescriptor>
```

SAML yanıtı onayını şifrelemek için:

1. Benzersiz bir tanımlayıcıya sahip [bir ilke anahtarı oluşturun](identity-provider-generic-saml.md#create-a-policy-key) . Örneğin, `B2C_1A_SAMLEncryptionCert`.
2. SAML Technical profile **Cryptographickeys** koleksiyonunuzda. **Storagereferenceıd** öğesini ilk adımda oluşturduğunuz ilke anahtarının adı olarak ayarlayın. `SamlAssertionDecryption`Kimlik, SAML yanıtının onayını şifrelemek ve şifresini çözmek için şifreleme anahtarının kullanımını gösterir.

    ```xml
    <CryptographicKeys>
            ...
      <Key Id="SamlAssertionDecryption" StorageReferenceId="B2C_1A_SAMLEncryptionCert"/>
    </CryptographicKeys>
    ```

3. Teknik profil meta verilerini **WantsEncryptedAssertions** olarak ayarlayın `true` .
    
    ```xml
    <Metadata>
      ...
      <Item Key="WantsEncryptedAssertions">true</Item>
    </Metadata>
    ```

4. Kimlik sağlayıcınızı yeni Azure AD B2C teknik profil meta verileri ile güncelleştirin. **Anahtar tanımlayıcısını** ,  `encryption` sertifikanızın ortak anahtarını içerecek şekilde Use özelliği ayarlanmış olarak görmeniz gerekir.

## <a name="enable-use-of-context-claims"></a>Bağlam taleplerinin kullanımını etkinleştir

Giriş ve çıkış talepleri koleksiyonunda, özniteliği ayarladığınız sürece kimlik sağlayıcısı tarafından döndürülmeyen talepleri dahil edebilirsiniz `DefaultValue` . Teknik profilde yer almak için [bağlam taleplerini](claim-resolver-overview.md) da kullanabilirsiniz. Bağlam talebi kullanmak için:

1. [Buildingblocks](buildingblocks.md)Içindeki [Claimsschema](claimsschema.md) öğesine bir talep türü ekleyin.
2. Giriş veya çıkış koleksiyonuna bir çıkış talebi ekleyin. Aşağıdaki örnekte, ilk talep kimlik sağlayıcısının değerini ayarlar. İkinci talep, Kullanıcı IP adresi [bağlam taleplerini](claim-resolver-overview.md)kullanır.
    
    ```xml
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" AlwaysUseDefaultValue="true" />
      <OutputClaim ClaimTypeReferenceId="IpAddress" DefaultValue="{Context:IPAddress}" AlwaysUseDefaultValue="true" />
    </OutputClaims>
    ```

## <a name="disable-single-logout"></a>Çoklu oturum kapatma devre dışı bırak

Uygulama oturumu kapatma isteği sonrasında, Azure AD B2C SAML kimlik sağlayıcınızdan oturumu açmaya çalışır. Daha fazla bilgi için bkz. [oturum oturumunu Azure AD B2C](session-behavior.md#sign-out).  Çoklu oturum kapatma davranışını devre dışı bırakmak için, **Singlelogoutenabled** meta verilerini olarak ayarlayın `false` .

```xml
<Metadata>
  ...
  <Item Key="SingleLogoutEnabled">false</Item>
</Metadata>
```

## <a name="debug-saml-protocol"></a>SAML protokolünde hata ayıkla

SAML kimlik sağlayıcısı ile Federasyonu yapılandırmaya ve hata ayıklamanıza yardımcı olmak için, SAML protokolü için, Chrome için [SAML DevTools uzantısı](https://chrome.google.com/webstore/detail/saml-devtools-extension/jndllhgbinhiiddokbeoeepbppdnhhio) , Firefox için [SAML izleyici](https://addons.mozilla.org/es/firefox/addon/saml-tracer/) veya [Edge ya da IE geliştirici araçları](https://techcommunity.microsoft.com/t5/microsoft-sharepoint-blog/gathering-a-saml-token-using-edge-or-ie-developer-tools/ba-p/320957)gibi bir tarayıcı uzantısı kullanabilirsiniz.

Bu araçları kullanarak, Azure AD B2C ile SAML kimlik sağlayıcınız arasındaki tümleştirmeyi kontrol edebilirsiniz. Örnek:

* SAML isteğinin bir imza içerip içermediğini ve yetkilendirme isteğinde oturum açmak için hangi algoritmanın kullanılacağını denetleyin.
* Bölüm altındaki talepleri (Onaylamalar) alın `AttributeStatement` .
* Kimlik sağlayıcısının bir hata iletisi döndürdüğünden emin olun.
* Onaylama bölümünün şifrelenip şifrelenmediğini denetleyin.

## <a name="next-steps"></a>Sonraki adımlar

- [Application Insights](troubleshoot-with-application-insights.md)kullanarak özel ilkelerinizle ilgili sorunları tanılamayı öğrenin. 

::: zone-end
