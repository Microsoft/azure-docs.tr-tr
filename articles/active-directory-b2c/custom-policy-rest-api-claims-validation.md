---
title: Talep değiş tokuşları doğrulama olarak REST API
titleSuffix: Azure AD B2C
description: Yeniden çalışan hizmetlerle etkileşim kuran Azure AD B2C Kullanıcı yolculuğu oluşturmaya yönelik bir yol.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 761bc4db7760ef5e84e3fc3c8a5deea5d4508f51
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94951936"
---
# <a name="walkthrough-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-to-validate-user-input"></a>İzlenecek yol: Kullanıcı girişini doğrulamak için Azure AD B2C Kullanıcı yolculuğunda REST API talep alışverişlerinde tümleştirin

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) altındaki kimlik deneyimi çerçevesi (ıEF), kimlik geliştiricilerinin Kullanıcı yolculuğu içindeki bir etkileşimi bir API ile tümleştirmesini sağlar.  Bu izlenecek yolun sonunda, Kullanıcı girişini doğrulamak üzere [yeniden çalışan hizmetlerle](custom-policy-rest-api-intro.md) etkileşim kuran Azure AD B2C bir Kullanıcı yolculuğu oluşturabileceksiniz.

Bu senaryoda, kullanıcıların Azure AD B2C kaydolma sayfasına bağlılık programı numarası girme yeteneğini ekleyeceğiz. Bu e-posta ve bağlılık programı numarası birleşiminin, bu verileri bir REST API göndererek Promosyon koduyla eşleştirildiğini doğrulayacağız. REST API bu kullanıcı için bir promosyon kodu bulursa, bu, Azure AD B2C döndürülür. Son olarak, promosyon kodu uygulamanın kullanacağı belirteç taleplerine eklenecektir.

Ayrıca, etkileşimi bir düzenleme adımı olarak tasarlayabilirler. Bu, REST API ekranda verileri doğrulamadan ve her zaman talepler döndürecek şekilde uygundur. Daha fazla bilgi için bkz. [Izlenecek yol: Azure AD B2C Kullanıcı yolculuğunda düzenleme adımı olarak REST API talep alışverişlerinde tümleştirme](custom-policy-rest-api-claims-exchange.md).

## <a name="prerequisites"></a>Önkoşullar

- [Özel ilkelerle çalışmaya başlama](custom-policy-get-started.md)bölümündeki adımları uygulayın. Kaydolma ve yerel hesaplarla oturum açma için çalışan bir özel ilkenize sahip olmanız gerekir.
- [Azure AD B2C özel ilkenizde REST API talep değişimlerinin nasıl tümleştirileceğini](custom-policy-rest-api-intro.md)öğrenin.

## <a name="prepare-a-rest-api-endpoint"></a>REST API uç noktası hazırlama

Bu kılavuzda, bir e-posta adresinin bir bağlılık programı KIMLIĞI ile arka uç sisteminizde kayıtlı olup olmadığını doğrulayan bir REST API olması gerekir. Kaydedilmişse, REST API bir kayıt promosyon kodu döndürmelidir, bu da müşterinin sizin uygulamanızdaki malları satın alabilir. Aksi takdirde REST API bir HTTP 409 hata iletisi döndürmelidir: "bağlılık programı KIMLIĞI ' {bağlılık KIMLIĞI} ', ' {email} ' e-posta adresi ile ilişkilendirilmemiş.".

Aşağıdaki JSON kodu Azure AD B2C REST API uç noktanıza gönderilecek verileri gösterir. 

```json
{
    "email": "User email address",
    "language": "Current UI language",
    "loyaltyId": "User loyalty ID"
}
```

REST API verileri doğruladıktan sonra, aşağıdaki JSON verileriyle birlikte bir HTTP 200 (Tamam) döndürmesi gerekir:

```json
{
    "promoCode": "24534"
}
```

Doğrulama başarısız olursa, REST API JSON öğesiyle bir HTTP 409 (Conflict) döndürmelidir `userMessage` . IEF, `userMessage` REST API döndürdüğü talebi bekliyor. Doğrulama başarısız olursa, bu talep kullanıcıya bir dize olarak sunulacaktır.

```json
{
    "version": "1.0.1",
    "status": 409,
    "userMessage": "LoyaltyId ID '1234' is not associated with 'david@contoso.com' email address."
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
<ClaimType Id="loyaltyId">
  <DisplayName>Your loyalty ID</DisplayName>
  <DataType>string</DataType>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
<ClaimType Id="promoCode">
  <DisplayName>Your promo code</DisplayName>
  <DataType>string</DataType>
  <UserInputType>Paragraph</UserInputType>
</ClaimType>
  <ClaimType Id="userLanguage">
  <DisplayName>User UI language (used by REST API to return localized error messages)</DisplayName>
  <DataType>string</DataType>
</ClaimType>
```

## <a name="add-the-restful-api-technical-profile"></a>Yeniden takip eden API teknik profilini ekleyin 

Daha fazla [Teknik bir teknik profil](restful-technical-profile.md) , kendi resteğiniz hizmetinize yönelik arabirim desteği sağlar. Azure AD B2C, verileri bir koleksiyondaki yeniden bir hizmete gönderir `InputClaims` ve verileri bir koleksiyonda geri alır `OutputClaims` . **Claimsproviders** öğesini bulun ve yeni bir talep sağlayıcısını aşağıdaki şekilde ekleyin:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-ValidateProfile">
      <DisplayName>Check loyaltyId Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <!-- Set the ServiceUrl with your own REST API endpoint -->
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/ValidateProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <!-- Set AuthenticationType to Basic or ClientCertificate in production environments -->
        <Item Key="AuthenticationType">None</Item>
        <!-- REMOVE the following line in production environments -->
        <Item Key="AllowInsecureAuthInProduction">true</Item>
      </Metadata>
      <InputClaims>
        <!-- Claims sent to your REST API -->
        <InputClaim ClaimTypeReferenceId="loyaltyId" />
        <InputClaim ClaimTypeReferenceId="email" />
        <InputClaim ClaimTypeReferenceId="userLanguage" PartnerClaimType="lang" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
      </InputClaims>
      <OutputClaims>
        <!-- Claims parsed from your REST API -->
        <OutputClaim ClaimTypeReferenceId="promoCode" />
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

## <a name="validate-the-user-input"></a>Kullanıcı girişini doğrulama

Kaydolma sırasında kullanıcının bağlılık programı numarasını almak için, kullanıcının bu verileri ekranda girmesine izin vermelisiniz. **Loyaltyıd** çıkış talebini, mevcut kaydolma teknik profili bölümünün öğesine ekleyerek kaydolma sayfasına ekleyin `OutputClaims` . Taleplerin ekranda sunulduğu sırayı denetlemek için tüm çıkış talepleri listesini belirtin.  

Doğrulama teknik profili başvurusunu, öğesini çağıran kaydolma teknik profiline ekleyin `REST-ValidateProfile` . Yeni doğrulama teknik profili, `<ValidationTechnicalProfiles>` temel ilkede tanımlanan koleksiyonun en üstüne eklenecektir. Bu davranış, yalnızca başarılı doğrulamadan sonra, Azure AD B2C dizinde hesap oluşturmak için ' ın üzerinde çalışacağı anlamına gelir.   

1. **Claimsproviders** öğesini bulun. Yeni bir talep sağlayıcısını aşağıdaki şekilde ekleyin:

    ```xml
    <ClaimsProvider>
      <DisplayName>Local Account</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true"/>
            <OutputClaim ClaimTypeReferenceId="newPassword" Required="true"/>
            <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true"/>
            <OutputClaim ClaimTypeReferenceId="displayName"/>
            <OutputClaim ClaimTypeReferenceId="givenName"/>
            <OutputClaim ClaimTypeReferenceId="surName"/>
            <!-- Required to present the text box to collect the data from the user -->
            <OutputClaim ClaimTypeReferenceId="loyaltyId"/>
            <!-- Required to pass the promoCode returned from "REST-ValidateProfile" 
            to subsequent orchestration steps and token issuance-->
            <OutputClaim ClaimTypeReferenceId="promoCode" />
          </OutputClaims>
          <ValidationTechnicalProfiles>
            <ValidationTechnicalProfile ReferenceId="REST-ValidateProfile" />
          </ValidationTechnicalProfiles>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    <ClaimsProvider>
      <DisplayName>Self Asserted</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="SelfAsserted-Social">
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="email" />
          </InputClaims>
            <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="email" />
            <OutputClaim ClaimTypeReferenceId="displayName"/>
            <OutputClaim ClaimTypeReferenceId="givenName"/>
            <OutputClaim ClaimTypeReferenceId="surname"/>
            <!-- Required to present the text box to collect the data from the user -->
            <OutputClaim ClaimTypeReferenceId="loyaltyId"/>
            <!-- Required to pass the promoCode returned from "REST-ValidateProfile" 
            to subsequent orchestration steps and token issuance-->
            <OutputClaim ClaimTypeReferenceId="promoCode" />
          </OutputClaims>
          <ValidationTechnicalProfiles>
            <ValidationTechnicalProfile ReferenceId="REST-ValidateProfile"/>
          </ValidationTechnicalProfiles>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

## <a name="include-a-claim-in-the-token"></a>Belirtece bir talep ekleyin 

Promosyon kodu talebini bağlı olan taraf uygulamasına geri döndürmek için, dosyaya bir çıkış talebi ekleyin <em>`SocialAndLocalAccounts/`**`SignUpOrSignIn.xml`**</em> . Çıkış talebi, başarılı bir Kullanıcı yolculuğuna sonra, talebin belirtece eklenmesine izin verir ve uygulamaya gönderilir. Bir çıkış talebi olarak eklemek için bağlı olan taraf bölümündeki teknik profil öğesini değiştirin `promoCode` .
 
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
      <OutputClaim ClaimTypeReferenceId="promoCode" DefaultValue="" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

## <a name="test-the-custom-policy"></a>Özel ilkeyi test etme

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Üst menüdeki **Dizin + abonelik** filtresini SEÇIP Azure AD kiracınızı içeren dizini seçerek Azure AD kiracınızı içeren dizini kullandığınızdan emin olun.
1. Azure portal sol üst köşesindeki **tüm hizmetler** ' i seçin ve ardından **uygulama kayıtları**' i arayıp seçin.
1. **Kimlik deneyimi çerçevesini** seçin.
1. **Özel Ilkeyi karşıya yükle**' yi seçin ve ardından değiştirdiğiniz ilke dosyalarını karşıya yükleyin: *TrustFrameworkExtensions.xml* ve *SignUpOrSignin.xml*. 
1. Karşıya yüklediğiniz kaydolma veya oturum açma ilkesini seçin ve **Şimdi Çalıştır** düğmesine tıklayın.
1. Bir e-posta adresi kullanarak kaydolabilirsiniz.
1. **Şimdi kaydolun** bağlantısına tıklayın.
1. **Bağlılık programı kimliğinizle** 1234 yazın ve **devam**' a tıklayın. Bu noktada, bir doğrulama hata iletisi almalısınız.
1. Başka bir değere değiştirip **devam**' a tıklayın.
1. Uygulamanıza geri gönderilen belirteç `promoCode` talebi içerir.

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1584295703,
  "nbf": 1584292103,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1584292103,
  "auth_time": 1584292103,
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "given_name": "Emily",
  "family_name": "Smith",
  "promoCode": "84362"
  ...
}
```

## <a name="next-steps"></a>Sonraki adımlar

API 'lerinizi güvenli hale getirme hakkında bilgi edinmek için aşağıdaki makalelere bakın:

- [İzlenecek yol: Azure AD B2C Kullanıcı yolculuğunda düzenleme adımı olarak REST API talep alışverişlerinde tümleştirin](custom-policy-rest-api-claims-exchange.md)
- [Yeniden takip eden API 'nizin güvenliğini sağlama](secure-rest-api.md)
- [Başvuru: Restuz teknik profili](restful-technical-profile.md)