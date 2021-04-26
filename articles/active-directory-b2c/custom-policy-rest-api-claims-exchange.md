---
title: Talep değişimlerinin REST API Azure Active Directory B2C
description: Active Directory B2C içindeki özel ilkelere REST API talep alışverişleri ekleyin.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 84053df34ffda0d4686ad80a9e5f3af00ac53d72
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94949505"
---
# <a name="walkthrough-add-rest-api-claims-exchanges-to-custom-policies-in-azure-active-directory-b2c"></a>İzlenecek yol: Azure Active Directory B2C içindeki özel ilkelere REST API talep alışverişi ekleme

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C), kimlik geliştiricilerinin Kullanıcı yolculuğu içindeki bir müsteki API ile etkileşimi tümleştirmelerine olanak sağlar. Bu izlenecek yolun sonunda, [yeniden çalışan hizmetlerle](custom-policy-rest-api-intro.md)etkileşim kuran Azure AD B2C bir Kullanıcı yolculuğu oluşturabileceksiniz.

Bu senaryoda, kullanıcının belirteç verilerini kurumsal iş kolu iş akışıyla tümleştirerek zenginleştirin. Kayıt sırasında veya yerel veya Federasyon hesabıyla oturum açma sırasında, kullanıcının genişletilmiş profil verilerini uzak bir veri kaynağından almak için bir REST API çağırır Azure AD B2C. Bu örnekte, Azure AD B2C kullanıcının benzersiz tanımlayıcısını, ObjectID 'yi gönderir. REST API daha sonra kullanıcının hesap bakiyesini (rastgele bir sayı) döndürür. Bu örneği, kendi CRM sisteminizle, pazarlama veritabanınızla veya herhangi bir iş kolu iş akışıyla tümleştirilecek bir başlangıç noktası olarak kullanın.

Etkileşimi bir doğrulama teknik profili olarak da tasarlayabilirsiniz. Bu, REST API ekranda verileri doğrularken ve talepler döndüren durumlarda uygundur. Daha fazla bilgi için bkz. [Izlenecek yol: Kullanıcı girişini doğrulamak için Azure AD B2C Kullanıcı yolculuğunda REST API talep Değişimlerinizi tümleştirme](custom-policy-rest-api-claims-validation.md).

## <a name="prerequisites"></a>Önkoşullar

- [Özel ilkelerle çalışmaya başlama](custom-policy-get-started.md)bölümündeki adımları uygulayın. Kaydolma ve yerel hesaplarla oturum açma için çalışan bir özel ilkenize sahip olmanız gerekir.
- [Azure AD B2C özel ilkenizde REST API talep değişimlerinin nasıl tümleştirileceğini](custom-policy-rest-api-intro.md)öğrenin.

## <a name="prepare-a-rest-api-endpoint"></a>REST API uç noktası hazırlama

Bu izlenecek yol için, bir kullanıcının Azure AD B2C ObjectID 'nin arka uç sisteminizde kayıtlı olup olmadığını doğrulayan bir REST API olması gerekir. Kaydedilmişse, REST API Kullanıcı hesabı bakiyesini döndürür. Aksi takdirde, REST API yeni hesabı dizine kaydeder ve başlangıç bakiyesini döndürür `50.00` .

Aşağıdaki JSON kodu Azure AD B2C REST API uç noktanıza gönderilecek verileri gösterir. 

```json
{
    "objectId": "User objectId",
    "lang": "Current UI language"
}
```

REST API verileri doğruladıktan sonra, aşağıdaki JSON verileriyle birlikte bir HTTP 200 (Tamam) döndürmesi gerekir:

```json
{
    "balance": "760.50"
}
```

REST API uç noktasının kurulumu Bu makalenin kapsamı dışındadır. Bir [Azure işlevleri](../azure-functions/functions-reference.md) örneği oluşturduk. [GitHub](https://github.com/azure-ad-b2c/rest-api/tree/master/source-code/azure-function)'Daki tüm Azure işlev koduna erişebilirsiniz.

## <a name="define-claims"></a>Talepleri tanımlama

Bir talep, Azure AD B2C ilkesi yürütmesi sırasında verilerin geçici olarak depolanmasını sağlar. Talepleri [talep şeması](claimsschema.md) bölümünde bildirebilirsiniz. 

1. İlkenizin uzantıları dosyasını açın. Örneğin, <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .
1. [Buildingblocks](buildingblocks.md) öğesi için arama yapın. Öğe yoksa, ekleyin.
1. [Claimsschema](claimsschema.md) öğesini bulun. Öğe yoksa, ekleyin.
1. Aşağıdaki talepleri **Claimsschema** öğesine ekleyin.  

```xml
<ClaimType Id="balance">
  <DisplayName>Your Balance</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="userLanguage">
  <DisplayName>User UI language (used by REST API to return localized error messages)</DisplayName>
  <DataType>string</DataType>
</ClaimType>
```

## <a name="add-the-restful-api-technical-profile"></a>Yeniden takip eden API teknik profilini ekleyin 

Daha fazla [Teknik bir teknik profil](restful-technical-profile.md) , kendi restsize hizmeti ile arabirim oluşturma desteği sağlar. Azure AD B2C, verileri bir koleksiyondaki yeniden bir hizmete gönderir `InputClaims` ve verileri bir koleksiyonda geri alır `OutputClaims` . Dosyanızdaki **Claimsproviders** öğesini bulun <em>**`TrustFrameworkExtensions.xml`**</em> ve yeni bir talep sağlayıcısını aşağıdaki şekilde ekleyin:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <!-- Set the ServiceUrl with your own REST API endpoint -->
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <!-- Set AuthenticationType to Basic or ClientCertificate in production environments -->
        <Item Key="AuthenticationType">None</Item>
        <!-- REMOVE the following line in production environments -->
        <Item Key="AllowInsecureAuthInProduction">true</Item>
      </Metadata>
      <InputClaims>
        <!-- Claims sent to your REST API -->
        <InputClaim ClaimTypeReferenceId="objectId" />
        <InputClaim ClaimTypeReferenceId="userLanguage" PartnerClaimType="lang" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
      </InputClaims>
      <OutputClaims>
        <!-- Claims parsed from your REST API -->
        <OutputClaim ClaimTypeReferenceId="balance" />
      </OutputClaims>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
``` 

Bu örnekte, `userLanguage` JSON yükünün içinde olduğu gıbı Rest hizmetine gönderilir `lang` . `userLanguage`Talebin değeri geçerli kullanıcı DILI kimliğini içerir. Daha fazla bilgi için bkz. [talep çözümleyici](claim-resolver-overview.md).

### <a name="configure-the-restful-api-technical-profile"></a>Restsize API teknik profilini yapılandırma 

REST API dağıttıktan sonra, `REST-ValidateProfile` Teknik profilin meta verilerini kendi REST API yansıtacak şekilde ayarlayın, örneğin:

- **ServiceUrl**. REST API uç noktasının URL 'sini ayarlayın.
- **Sendclaimsin**. Giriş taleplerinin, Restity talep sağlayıcısına nasıl gönderileceğini belirtin.
- **AuthenticationType**. Yeniden izlenen talep sağlayıcısı tarafından gerçekleştirilen kimlik doğrulaması türünü ayarlayın. 
- **Allowınsecureauthınproduction**. Bir üretim ortamında, bu meta verileri olarak ayarladığınızdan emin olun. `true`
    
Daha fazla yapılandırma için bkz. [yeniden teknik profil meta verileri](restful-technical-profile.md#metadata) .

Yukarıdaki açıklamalar `AuthenticationType` ve `AllowInsecureAuthInProduction` bir üretim ortamına geçtiğinizde yapmanız gereken değişiklikleri belirtin. Üretim için yeniden yapılan API 'lerinizi güvenli hale getirme hakkında bilgi edinmek için bkz. [güvenli restsize API](secure-rest-api.md).

## <a name="add-an-orchestration-step"></a>Düzenleme adımı ekleme

[Kullanıcı yolculukları](userjourneys.md), bağlı olan taraf uygulamasının kullanıcı için istenen talepleri elde etmesine izin veren bir ilkenin kullandığı belirgin yolları belirtir. Kullanıcı yolculuğu, başarılı bir işlem için izlenmesi gereken bir düzenleme sırası olarak temsil edilir. Düzenleme adımları ekleyebilir veya çıkarabilirsiniz. Bu durumda, Kullanıcı kaydolduktan veya REST API çağrısıyla oturum açtıktan sonra uygulamaya girilen bilgileri artırmak için kullanılan yeni bir düzenleme adımı ekleyeceksiniz.

1. İlkenizin temel dosyasını açın. Örneğin, <em>`SocialAndLocalAccounts/`**`TrustFrameworkBase.xml`**</em> .
1. Öğesi için arama yapın `<UserJourneys>` . Tüm öğeyi kopyalayın ve silin.
1. İlkenizin uzantıları dosyasını açın. Örneğin, <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .
1. `<UserJourneys>`Öğesi kapatıldıktan sonra uzantı dosyasına yapıştırın `<ClaimsProviders>` .
1. Öğesini bulun `<UserJourney Id="SignUpOrSignIn">` ve en son düzenlemeden önce aşağıdaki düzenleme adımını ekleyin.

    ```xml
    <OrchestrationStep Order="7" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="RESTGetProfile" TechnicalProfileReferenceId="REST-GetProfile" />
      </ClaimsExchanges>
    </OrchestrationStep>
    ```

1. ' İ ' ye değiştirerek son düzenleme adımını yeniden düzenleyin `Order` `8` . Son iki düzenleme adımlarınız aşağıdaki gibi görünmelidir:

    ```xml
    <OrchestrationStep Order="7" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="RESTGetProfile" TechnicalProfileReferenceId="REST-GetProfile" />
      </ClaimsExchanges>
    </OrchestrationStep>

    <OrchestrationStep Order="8" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
    ```

1. **Profileedit** ve **passwordreset** Kullanıcı ilerilerinin son iki adımını tekrarlayın.


## <a name="include-a-claim-in-the-token"></a>Belirtece bir talep ekleyin 

`balance`Talebi bağlı olan taraf uygulamasına geri döndürmek için dosyaya bir çıkış talebi ekleyin <em>`SocialAndLocalAccounts/`**`SignUpOrSignIn.xml`**</em> . Bir çıkış talebi eklendiğinde, başarılı bir Kullanıcı yolculuğuna sonra bu isteği belirtece dönüştürür ve uygulamaya gönderilir. Bağlı olan taraf bölümündeki teknik profil öğesini, `balance` Çıkış talebi olarak eklemek üzere değiştirin.
 
```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" />
      <OutputClaim ClaimTypeReferenceId="givenName" />
      <OutputClaim ClaimTypeReferenceId="surname" />
      <OutputClaim ClaimTypeReferenceId="email" />
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="identityProvider" />
      <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
      <OutputClaim ClaimTypeReferenceId="balance" DefaultValue="" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

**ProfileEdit.xml** için bu adımı tekrarlayın ve Kullanıcı **PasswordReset.xml** .

Değiştirdiğiniz dosyaları kaydedin: *TrustFrameworkBase.xml* ve *TrustFrameworkExtensions.xml*, *SignUpOrSignin.xml*, *ProfileEdit.xml* ve *PasswordReset.xml*. 

## <a name="test-the-custom-policy"></a>Özel ilkeyi test etme

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Üst menüdeki **Dizin + abonelik** filtresini SEÇIP Azure AD kiracınızı içeren dizini seçerek Azure AD kiracınızı içeren dizini kullandığınızdan emin olun.
1. Azure portal sol üst köşesindeki **tüm hizmetler** ' i seçin ve ardından **uygulama kayıtları**' i arayıp seçin.
1. **Kimlik deneyimi çerçevesini** seçin.
1. **Özel Ilkeyi karşıya yükle**' yi seçin ve ardından değiştirdiğiniz ilke dosyalarını karşıya yükleyin: *TrustFrameworkBase.xml* ve *TrustFrameworkExtensions.xml*, *SignUpOrSignin.xml*, *ProfileEdit.xml* ve *PasswordReset.xml*. 
1. Karşıya yüklediğiniz kaydolma veya oturum açma ilkesini seçin ve **Şimdi Çalıştır** düğmesine tıklayın.
1. Bir e-posta adresi veya Facebook hesabı kullanarak kaydolabilirsiniz.
1. Uygulamanıza geri gönderilen belirteç `balance` talebi içerir.

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1584961516,
  "nbf": 1584957916,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1584957916,
  "auth_time": 1584957916,
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "given_name": "Emily",
  "family_name": "Smith",
  "balance": "202.75"
  ...
}
```

## <a name="next-steps"></a>Sonraki adımlar

API 'lerinizi güvenli hale getirme hakkında bilgi edinmek için aşağıdaki makalelere bakın:

- [İzlenecek yol: Azure AD B2C Kullanıcı yolculuğunda düzenleme adımı olarak REST API talep alışverişlerinde tümleştirin](custom-policy-rest-api-claims-exchange.md)
- [Yeniden takip eden API 'nizin güvenliğini sağlama](secure-rest-api.md)
- [Başvuru: Restuz teknik profili](restful-technical-profile.md)