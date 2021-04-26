---
title: Kullanıcı öznitelikleri ekleme ve Kullanıcı girişini özelleştirme
titleSuffix: Azure AD B2C
description: Kullanıcı girişini özelleştirmeyi ve Azure Active Directory B2C kaydolma veya oturum açma yolculuğuna Kullanıcı öznitelikleri eklemeyi öğrenin.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/10/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 257ba16cf015705b8f6da264d9c25f28cef2ebb1
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106443449"
---
#  <a name="add-user-attributes-and-customize-user-input-in-azure-active-directory-b2c"></a>Azure Active Directory B2C Kullanıcı öznitelikleri ekleme ve Kullanıcı girişini özelleştirme

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

Bu makalede, kayıt seyahatinizi Azure Active Directory B2C (Azure AD B2C) sırasında yeni bir öznitelik topladığınızda. Kullanıcıların şehrini alacak, açılan olarak yapılandıracaksınız ve sağlanması gerekip gerekmediğini tanımlayacaksınız.

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

::: zone pivot="b2c-user-flow"

## <a name="add-user-attributes-your-user-flow"></a>Kullanıcı akışlarınızın Kullanıcı özniteliklerini ekleyin

1. Azure AD B2C kiracınızda **Kullanıcı akışları**' nı seçin.
1. İlkeyi (örneğin, "B2C_1_SignupSignin") seçerek açın.
1. **Kullanıcı öznitelikleri** ' ni seçin ve ardından Kullanıcı özniteliğini (örneğin, "şehir") seçin. 
1. **Kaydet**’i seçin.

## <a name="provide-optional-claims-to-your-app"></a>Uygulamanıza isteğe bağlı talepler sağlama

Uygulama talepleri, uygulamaya döndürülen değerlerdir. Kullanıcı akışınızı istenen talepleri içerecek şekilde güncelleştirin.

1. İlkeyi (örneğin, "B2C_1_SignupSignin") seçerek açın.
1. **Uygulama talepleri**’ni seçin.
1. Uygulamanıza geri göndermek istediğiniz öznitelikleri seçin (örneğin, "şehir").
1. **Kaydet**’i seçin.
 
## <a name="configure-user-attributes-input-type"></a>Kullanıcı özniteliklerinin giriş türünü yapılandırma

1. İlkeyi (örneğin, "B2C_1_SignupSignin") seçerek açın.
1. **Sayfa düzenlerini** seçin.
1. **Yerel hesap kaydolma sayfası**' nı seçin.
1. **Kullanıcı öznitelikleri** altında **şehir**' i seçin.
    1. **Kullanıcı giriş türü** açılan kutusunda **dropdownsingleselect**' i seçin. İsteğe bağlı: kaydolma sayfasındaki metin sırasını düzenlemek için "yukarı taşı/aşağı taşı" düğmelerini kullanın.
    1. **Isteğe bağlı** açılan kutuda **Hayır**' ı seçin.
1. **Kaydet**’i seçin. 

### <a name="provide-a-list-of-values-by-using-localized-collections"></a>Yerelleştirilmiş Koleksiyonlar kullanarak değerlerin bir listesini sağlama

Şehir özniteliği için bir değer listesi sağlamak için: 

1. [Kullanıcı akışında dil özelleştirmesini etkinleştir](language-customization.md#support-requested-languages-for-ui_locales)
1. İlkeyi (örneğin, "B2C_1_SignupSignin") seçerek açın.
1. Kullanıcı akışının **Diller** sayfasında, özelleştirmek istediğiniz dili seçin.
1. **Sayfa düzeyi-kaynak dosyaları** altında **yerel hesap kayıt sayfası**' nı seçin.
1. **Varsayılanları indir** ' i seçin (veya daha önce bu dili düzenlediyseniz, **geçersiz kılmaları indir** ).
1. Bir `LocalizedCollections` öznitelik oluşturun.

, `LocalizedCollections` `Name` Ve `Value` çiftleri dizisidir. Öğelerin sırası görüntülendikleri sıra olacaktır. 

* `ElementId` , bu `LocalizedCollections` özniteliğin yanıt olduğu Kullanıcı özniteliğidir.
* `Name` , kullanıcıya gösterilen değerdir.
* `Value` Bu seçenek belirlendiğinde talepte döndürülen değer.

```json
{
  "LocalizedStrings": [...],
  "LocalizedCollections": [
    {
      "ElementType": "ClaimType",
      "ElementId": "city",
      "TargetCollection": "Restriction",
      "Override": false,
      "Items": [
        {
          "Name": "Berlin",
          "Value": "Berlin"
        },
        {
          "Name": "London",
          "Value": "London"
        },
        {
          "Name": "Seattle",
          "Value": "Seattle"
        }
      ]
    }
  ]
}
```

#### <a name="upload-your-changes"></a>Değişikliklerinizi karşıya yükleyin

1. JSON dosyanızdaki değişiklikleri tamamladıktan sonra B2C kiracınıza geri dönün.
1. **Kullanıcı akışları** ' nı seçin ve ilkenizi (örneğin, "B2C_1_SignupSignin") seçerek açın.
1. **Dilleri** seçin.
1. Çevirmek istediğiniz dili seçin.
1. **Yerel hesap kayıt sayfasını** seçin.
1. Klasör simgesini seçin ve karşıya yüklenecek JSON dosyasını seçin. Değişiklikler Kullanıcı akışınıza otomatik olarak kaydedilir.

## <a name="test-your-user-flow"></a>Kullanıcı akışınızı test etme

1. İlkeyi (örneğin, "B2C_1_SignupSignin") seçerek açın.
1. İlkenizi test etmek için **Kullanıcı akışını Çalıştır**' ı seçin.
1. **Uygulama** için, daha önce kaydettiğiniz *testapp1* adlı Web uygulamasını seçin. **Yanıt URL 'si** gösterilmesi gerekir `https://jwt.ms` .
1. **Kullanıcı akışını Çalıştır** 'a tıklayın

::: zone-end

::: zone pivot="b2c-custom-policy"

> [!NOTE]
> Bu örnek, yerleşik ' City ' talebini kullanır. Bunun yerine, desteklenen [Azure AD B2C yerleşik özniteliklerinden](user-profile-attributes.md) birini veya özel bir özniteliği seçebilirsiniz. Özel bir öznitelik kullanmak için [özel öznitelikleri etkinleştirin](user-flow-custom-attributes.md). Farklı bir yerleşik veya özel öznitelik kullanmak için ' City ' değerini istediğiniz özniteliğiyle değiştirin; Örneğin, yerleşik özniteliği *JobTitle* veya *extension_loyaltyId* gibi özel bir öznitelik.  

Kayıt veya oturum açma Kullanıcı yolculuğu kullanarak kullanıcılarınızla ilk verileri toplayabilirsiniz. Ek talepler, daha sonra bir profil düzenleme Kullanıcı yolculuğu kullanılarak toplanabilir. Azure AD B2C her zaman doğrudan kullanıcıdan etkileşimli olarak bilgi toplar, kimlik deneyimi çerçevesi [kendi kendine onaylanan teknik profilini](self-asserted-technical-profile.md)kullanır. Bu örnekte şunları yapabilirsiniz:

1. "Şehir" talebi tanımlayın. 
1. Kullanıcıdan şehrini sorun.
1. Şehri Azure AD B2C dizinindeki Kullanıcı profiline kalıcı hale getirin.
1. Her oturum açma üzerinde Azure AD B2C dizininden şehir talebini okuyun.
1. Oturum açtıktan veya kaydolduktan sonra şehri bağlı olan taraf uygulamanıza döndürün.  

## <a name="define-a-claim"></a>Talep tanımlama

Bir talep, Azure AD B2C ilkesi yürütmesi sırasında verilerin geçici bir şekilde depolanmasını sağlar. [Talep şeması](claimsschema.md) , taleplerinizi bildirdiğiniz yerdir. Talebi tanımlamak için aşağıdaki öğeler kullanılır:

- **DisplayName** -kullanıcıya yönelik etiketi tanımlayan bir dize.
- [Veri türü](claimsschema.md#datatype) -talebin türü.
- **Userhelptext** -kullanıcının nelerin gerekli olduğunu anlamasına yardımcı olur.
- [Userınputtype](claimsschema.md#userinputtype) -metin kutusu, radyo seçimi, açılan liste veya birden çok seçim gibi giriş denetiminin türü.

İlkenizin uzantıları dosyasını açın. Örneğin, <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .

1. [Buildingblocks](buildingblocks.md) öğesi için arama yapın. Öğe yoksa, ekleyin.
1. [Claimsschema](claimsschema.md) öğesini bulun. Öğe yoksa, ekleyin.
1. Bir şehir talebini **Claimsschema** öğesine ekleyin.  

```xml
<!-- 
<BuildingBlocks>
  <ClaimsSchema> -->
    <ClaimType Id="city">
      <DisplayName>City where you work</DisplayName>
      <DataType>string</DataType>
      <UserInputType>DropdownSingleSelect</UserInputType>
      <Restriction>
        <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
        <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
        <Enumeration Text="Kirkland" Value="kirkland" SelectByDefault="false" />
      </Restriction>
    </ClaimType>
  <!-- 
  </ClaimsSchema>
</BuildingBlocks>-->
```

## <a name="add-a-claim-to-the-user-interface"></a>Kullanıcı arabirimine talep ekleme

Aşağıdaki teknik [profiller, bir](self-asserted-technical-profile.md)kullanıcının giriş sağlaması beklendiğinde çağrılır:

- **Localaccountsignupwithlogonemail** -yerel hesap kaydolma akışı.
- **Selfasted-sosyal** -Federasyon hesabı ilk kez Kullanıcı oturum açma.
- **Selfasserted-ProfileUpdate** -profil akışını Düzenle.

Kaydolma sırasında şehir talebini toplamak için, teknik profile bir çıkış talebi olarak eklenmelidir `LocalAccountSignUpWithLogonEmail` . Uzantı dosyasındaki bu teknik profili geçersiz kılın. Taleplerin ekranda sunulduğu sırayı denetlemek için tüm çıkış talepleri listesini belirtin. **Claimsproviders** öğesini bulun. Yeni bir ClaimsProviders şu şekilde ekleyin:

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <!--Local account sign-up page-->
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <OutputClaims>
       <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true" />
       <OutputClaim ClaimTypeReferenceId="newPassword" Required="true" />
       <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
       <OutputClaim ClaimTypeReferenceId="displayName" />
       <OutputClaim ClaimTypeReferenceId="givenName" />
       <OutputClaim ClaimTypeReferenceId="surName" />
       <OutputClaim ClaimTypeReferenceId="city"/>
     </OutputClaims>
   </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

Bir Federasyon hesabıyla ilk oturum açma işleminden sonra şehir talebini toplamak için, teknik profile bir çıkış talebi olarak eklenmesi gerekir `SelfAsserted-Social` . Yerel ve Federal hesap kullanıcılarının profil verilerini daha sonra düzenleyebilmeleri için, teknik profile giriş ve çıkış taleplerini ekleyin `SelfAsserted-ProfileUpdate` . Uzantı dosyasındaki bu teknik profilleri geçersiz kılın. Talepler ekranda sunulan sırayı denetlemek için çıkış taleplerinin tamamının listesini belirtin. **Claimsproviders** öğesini bulun. Yeni bir ClaimsProviders şu şekilde ekleyin:

```xml
<ClaimsProvider>
  <DisplayName>Self Asserted</DisplayName>
  <TechnicalProfiles>
    <!--Federated account first-time sign-in page-->
    <TechnicalProfile Id="SelfAsserted-Social">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="city" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName"/>
        <OutputClaim ClaimTypeReferenceId="givenName"/>
        <OutputClaim ClaimTypeReferenceId="surname"/>
        <OutputClaim ClaimTypeReferenceId="city"/>
      </OutputClaims>
    </TechnicalProfile>
    <!--Edit profile page-->
    <TechnicalProfile Id="SelfAsserted-ProfileUpdate">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="city" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName"/>
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="city"/>
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="read-and-write-a-claim"></a>Talep okuma ve yazma

Aşağıdaki teknik profiller, Azure Active Directory verileri okuyan ve yazan [Teknik profillerdir Active Directory](active-directory-technical-profile.md).  
`PersistedClaims`Kullanıcı profiline veri yazmak ve `OutputClaims` ilgili Active Directory teknik profillerinin içindeki kullanıcı profilinden veri okumak için kullanın.

Uzantı dosyasındaki bu teknik profilleri geçersiz kılın. **Claimsproviders** öğesini bulun.  Yeni bir ClaimsProviders şu şekilde ekleyin:

```xml
<ClaimsProvider>
  <DisplayName>Azure Active Directory</DisplayName>
  <TechnicalProfiles>
    <!-- Write data during a local account sign-up flow. -->
    <TechnicalProfile Id="AAD-UserWriteUsingLogonEmail">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="city"/>
      </PersistedClaims>
    </TechnicalProfile>
    <!-- Write data during a federated account first-time sign-in flow. -->
    <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="city"/>
      </PersistedClaims>
    </TechnicalProfile>
    <!-- Write data during edit profile flow. -->
    <TechnicalProfile Id="AAD-UserWriteProfileUsingObjectId">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="city"/>
      </PersistedClaims>
    </TechnicalProfile>
    <!-- Read data after user resets the password. -->
    <TechnicalProfile Id="AAD-UserReadUsingEmailAddress">
      <OutputClaims>  
        <OutputClaim ClaimTypeReferenceId="city" />
      </OutputClaims>
    </TechnicalProfile>
    <!-- Read data after user authenticates with a local account. -->
    <TechnicalProfile Id="AAD-UserReadUsingObjectId">
      <OutputClaims>  
        <OutputClaim ClaimTypeReferenceId="city" />
      </OutputClaims>
    </TechnicalProfile>
    <!-- Read data after user authenticates with a federated account. -->
    <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
      <OutputClaims>  
        <OutputClaim ClaimTypeReferenceId="city" />
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="include-a-claim-in-the-token"></a>Belirtece bir talep ekleyin 

Şehir talebini bağlı olan taraf uygulamasına geri döndürmek için, dosyaya bir çıkış talebi ekleyin <em>`SocialAndLocalAccounts/`**`SignUpOrSignIn.xml`**</em> . Çıkış talebi, başarılı bir Kullanıcı yolculuğuna sonra belirtece eklenir ve uygulamaya gönderilir. Şehri çıkış talebi olarak eklemek için bağlı olan taraf bölümündeki teknik profil öğesini değiştirin.
 
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
      <OutputClaim ClaimTypeReferenceId="city" DefaultValue="" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

## <a name="test-the-custom-policy"></a>Özel ilkeyi test etme

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. Üst menüdeki **Dizin + abonelik** filtresini SEÇIP Azure AD kiracınızı içeren dizini seçerek Azure AD kiracınızı içeren dizini kullandığınızdan emin olun.
3. Azure portal sol üst köşesindeki **tüm hizmetler** ' i seçin ve ardından **uygulama kayıtları**' i arayıp seçin.
4. **Kimlik deneyimi çerçevesini** seçin.
5. **Özel Ilkeyi karşıya yükle**' yi seçin ve ardından değiştirdiğiniz iki ilke dosyasını karşıya yükleyin.
2. Karşıya yüklediğiniz kaydolma veya oturum açma ilkesini seçin ve **Şimdi Çalıştır** düğmesine tıklayın.
3. Bir e-posta adresi kullanarak kaydolabilirsiniz.

::: zone-end

Kaydolma ekranı aşağıdaki ekran görüntüsüne benzer görünmelidir:

![Değiştirilen kaydolma seçeneğinin ekran görüntüsü](./media/configure-user-input/signup-with-city-claim-drop-down-example.png)

Uygulamanıza geri gönderilen belirteç `city` talebi içerir.

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1583500140,
  "nbf": 1583496540,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1583496540,
  "auth_time": 1583496540,
  "name": "Emily Smith",
  "email": "joe@outlook.com",
  "given_name": "Emily",
  "family_name": "Smith",
  "city": "Bellevue"
  ...
}
```

::: zone pivot="b2c-custom-policy"

## <a name="next-steps"></a>Sonraki adımlar

- IEF başvurusunda [Claimsschema](claimsschema.md) öğesi hakkında daha fazla bilgi edinin.
- [Azure AD B2C ' de özel öznitelikler kullanmayı](user-flow-custom-attributes.md)öğrenin.

::: zone-end
