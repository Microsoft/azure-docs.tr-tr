---
title: Özel ilkede bir Azure AD teknik profili tanımlama
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C bir özel ilkede Azure Active Directory teknik profili tanımlayın.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3f92bfe98a45117264c957481a75493de652abc9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97508110"
---
# <a name="define-an-azure-active-directory-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Azure Active Directory B2C özel ilkesinde Azure Active Directory teknik profil tanımlama

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) Azure Active Directory Kullanıcı yönetimi için destek sağlar. Bu makalede, bu standartlaştırılmış protokolü destekleyen bir talep sağlayıcısıyla etkileşim kurmaya yönelik teknik bir profilin ayrıntıları açıklanmaktadır.

## <a name="protocol"></a>Protokol

**Protokol** öğesinin **Name** özniteliğinin olarak ayarlanması gerekir `Proprietary` . **Handler** özniteliği, protokol işleyici derlemesinin tam adını içermelidir `Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null` .

Aşağıdaki [özel ilke Başlatıcı paketi](custom-policy-get-started.md#custom-policy-starter-pack) Azure AD teknik profilleri **AAD ortak** teknik profilini içerir. Protokol **AAD ortak** teknik profilinde yapılandırıldığı IÇIN Azure AD teknik profilleri Protokolü belirtmez:
 
- **AAD-userreadusingalternativesecurityıd** ve **AAD-Userreadusingalternativesecurityıd-NOERROR** -dizinde sosyal hesap arama.
- **AAD-Userwriteusingalternativesecurityıd** -yeni bir sosyal hesap oluşturun.
- **AAD-Userreadusıngemapostaadresi** -dizinde yerel bir hesap arar.
- **AAD-UserWriteUsingLogonEmail** -yeni bir yerel hesap oluşturun.
- **AAD-UserWritePasswordUsingObjectId** -bir yerel hesabın parolasını güncelleştirin.
- **AAD-Userwriteprofileusingobjectıd** -yerel veya sosyal hesabın kullanıcı profilini güncelleştirin.
- **AAD-Userreadusingobjectıd** -yerel veya sosyal hesabın kullanıcı profilini okuyun.
- **AAD-Userwritephonenumberusingobjectıd** -yerel veya sosyal hesabın MFA telefon numarasını yazın

Aşağıdaki örnekte **AAD ortak** teknik profili gösterilmektedir:

```xml
<TechnicalProfile Id="AAD-Common">
  <DisplayName>Azure Active Directory</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />

  <CryptographicKeys>
    <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
  </CryptographicKeys>

  <!-- We need this here to suppress the SelfAsserted provider from invoking SSO on validation profiles. -->
  <IncludeInSso>false</IncludeInSso>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

## <a name="inputclaims"></a>Inputclaims

Inputclaim öğesi, dizindeki bir hesabı aramak veya yeni bir hesap oluşturmak için kullanılan bir talep içerir. Tüm Azure AD teknik profillerinin giriş talep koleksiyonunda tam olarak bir ınputclaim öğesi olmalıdır. İlkenizde tanımlanan talebin adını Azure Active Directory tanımlı adla eşlemeniz gerekebilir.

Mevcut bir kullanıcı hesabını okumak, güncelleştirmek veya silmek için, giriş talebi, hesabı Azure AD dizininde benzersiz bir şekilde tanımlayan bir anahtardır. Örneğin, **ObjectID**, **userPrincipalName**, **Signınnames. emapostaadı**, **Signınnames. UserName** ya da **alternativesecurityıd**. 

Yeni bir kullanıcı hesabı oluşturmak için, giriş talebi yerel veya Federasyon hesabını benzersiz bir şekilde tanımlayan bir anahtardır. Örneğin, yerel hesap: **Signınnames. Emapostaadı** veya **Signınnames. UserName**. Bir Federasyon hesabı için: **Alternativesecurityıd**.

[Inputclaimstransformations](technicalprofiles.md#input-claims-transformations) öğesi, giriş talebini değiştirmek veya yeni bir tane oluşturmak için kullanılan bir giriş talepleri dönüştürme öğeleri koleksiyonu içerebilir.

## <a name="outputclaims"></a>Outputclaim

**Outputclaim** öğesi, Azure AD teknik profili tarafından döndürülen taleplerin bir listesini içerir. İlkenizde tanımlanan talebin adını Azure Active Directory tanımlı adla eşlemeniz gerekebilir. Özniteliği ayarladığınız sürece Azure Active Directory tarafından döndürülmeyen talepleri de ekleyebilirsiniz `DefaultValue` .

[Outputclaimstransformations](technicalprofiles.md#output-claims-transformations) öğesi, çıkış taleplerini değiştirmek veya yenilerini oluşturmak için kullanılan bir **outputclaimstransreference** öğeleri koleksiyonu içerebilir.

Örneğin, **AAD-UserWriteUsingLogonEmail** teknik profili yerel bir hesap oluşturur ve aşağıdaki talepleri döndürür:

- Yeni hesabın tanımlayıcısı olan **ObjectID**
- **Newuser**, kullanıcının yeni olup olmadığını belirtir
- kimlik doğrulamasını için ayarlayan **Authenticationsource**`localAccountAuthentication`
- Yeni hesabın kullanıcı asıl adı olan **userPrincipalName**
- Hesap oturum açma adı olan ve **e-posta** girişi talebine benzeyen **signınnames. emapostaadı**

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="objectId" />
  <OutputClaim ClaimTypeReferenceId="newUser" PartnerClaimType="newClaimsPrincipalCreated" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
  <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
  <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
</OutputClaims>
```

## <a name="persistedclaims"></a>PersistedClaims

**PersistedClaims** öğesi, Ilke ve Azure AD öznitelik adındaki [Claimsschema](claimsschema.md) bölümünde zaten tanımlanmış olan bir talep türü arasındaki olası eşleme bilgileri ile Azure AD tarafından kalıcı olması gereken tüm değerleri içerir.

Yeni yerel hesap oluşturan **AAD-UserWriteUsingLogonEmail** teknik profili, sonrasında aşağıdaki talepler devam ediyor:

```xml
  <PersistedClaims>
    <!-- Required claims -->
    <PersistedClaim ClaimTypeReferenceId="email" PartnerClaimType="signInNames.emailAddress" />
    <PersistedClaim ClaimTypeReferenceId="newPassword" PartnerClaimType="password"/>
    <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />
    <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration" />

    <!-- Optional claims. -->
    <PersistedClaim ClaimTypeReferenceId="givenName" />
    <PersistedClaim ClaimTypeReferenceId="surname" />
  </PersistedClaims>
```

Azure AD öznitelik adını içeren **Partnerclaimtype** özniteliği belirtilmediği takdirde talebin adı Azure ad özniteliğinin adıdır.

## <a name="requirements-of-an-operation"></a>Bir işlemin gereksinimleri

- Tüm Azure AD teknik profillerinin talep paketinde tam olarak bir **ınputclaim** öğesi olması gerekir.
- [Kullanıcı profili öznitelikleri makalesi](user-profile-attributes.md) , giriş talepleri, çıkış talepleri ve kalıcı taleplerde kullanabileceğiniz desteklenen Azure AD B2C Kullanıcı profili özniteliklerini açıklar. 
- İşlem `Write` veya ise `DeleteClaims` , **PersistedClaims** öğesinde de görünmelidir.
- **UserPrincipalName** talebinin değeri biçiminde olmalıdır `user@tenant.onmicrosoft.com` .
- **DisplayName** talebi gereklidir ve boş bir dize olamaz.

## <a name="azure-ad-technical-provider-operations"></a>Azure AD teknik sağlayıcı işlemleri

### <a name="read"></a>Okuma

**Okuma** işlemi, tek bir kullanıcı hesabı hakkındaki verileri okur. Aşağıdaki teknik profil, kullanıcının ObjectID 'sini kullanarak bir kullanıcı hesabı hakkındaki verileri okur:

```xml
<TechnicalProfile Id="AAD-UserReadUsingObjectId">
  <Metadata>
    <Item Key="Operation">Read</Item>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
  </Metadata>
  <IncludeInSso>false</IncludeInSso>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
  </InputClaims>
  <OutputClaims>

    <!-- Required claims -->
    <OutputClaim ClaimTypeReferenceId="strongAuthenticationPhoneNumber" />

    <!-- Optional claims -->
    <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
    <OutputClaim ClaimTypeReferenceId="givenName" />
    <OutputClaim ClaimTypeReferenceId="surname" />
  </OutputClaims>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

### <a name="write"></a>Yazma

**Yazma** işlemi tek bir kullanıcı hesabı oluşturur veya güncelleştirir. Aşağıdaki teknik profil yeni sosyal hesap oluşturur:

```xml
<TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="Operation">Write</Item>
    <Item Key="RaiseErrorIfClaimsPrincipalAlreadyExists">true</Item>
    <Item Key="UserMessageIfClaimsPrincipalAlreadyExists">You are already registered, please press the back button and sign in instead.</Item>
  </Metadata>
  <IncludeInSso>false</IncludeInSso>
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="CreateOtherMailsFromEmail" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityId" PartnerClaimType="alternativeSecurityId" Required="true" />
  </InputClaims>
  <PersistedClaims>
    <!-- Required claims -->
    <PersistedClaim ClaimTypeReferenceId="alternativeSecurityId" />
    <PersistedClaim ClaimTypeReferenceId="userPrincipalName" />
    <PersistedClaim ClaimTypeReferenceId="mailNickName" DefaultValue="unknown" />
    <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />

    <!-- Optional claims -->
    <PersistedClaim ClaimTypeReferenceId="otherMails" />
    <PersistedClaim ClaimTypeReferenceId="givenName" />
    <PersistedClaim ClaimTypeReferenceId="surname" />
  </PersistedClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="newUser" PartnerClaimType="newClaimsPrincipalCreated" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
  </OutputClaims>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
</TechnicalProfile>
```

### <a name="deleteclaims"></a>Deleteclaim

**Deleteclaim** işlemi, bilgileri, belirtilen talepler listesinden temizler. Aşağıdaki teknik profilde talepler silinir:

```xml
<TechnicalProfile Id="AAD-DeleteClaimsUsingObjectId">
  <Metadata>
    <Item Key="Operation">DeleteClaims</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
  </InputClaims>
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="objectId" />
    <PersistedClaim ClaimTypeReferenceId="Verified.strongAuthenticationPhoneNumber" PartnerClaimType="strongAuthenticationPhoneNumber" />
  </PersistedClaims>
  <OutputClaims />
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

### <a name="deleteclaimsprincipal"></a>DeleteClaimsPrincipal

**DeleteClaimsPrincipal** işlemi, dizinden tek bir kullanıcı hesabını siler. Aşağıdaki teknik profil, Kullanıcı asıl adını kullanarak bir kullanıcı hesabını dizinden siler:

```xml
<TechnicalProfile Id="AAD-DeleteUserUsingObjectId">
  <Metadata>
    <Item Key="Operation">DeleteClaimsPrincipal</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
  </InputClaims>
  <OutputClaims/>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

Aşağıdaki teknik profil, bir sosyal Kullanıcı hesabını **Alternativesecurityıd** kullanarak siler:

```xml
<TechnicalProfile Id="AAD-DeleteUserUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="Operation">DeleteClaimsPrincipal</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="alternativeSecurityId" Required="true" />
  </InputClaims>
  <OutputClaims/>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```
## <a name="metadata"></a>Meta veri

| Öznitelik | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| İşlem | Yes | Gerçekleştirilecek işlem. Olası değerler: `Read` , `Write` , `DeleteClaims` veya `DeleteClaimsPrincipal` . |
| RaiseErrorIfClaimsPrincipalDoesNotExist | No | Kullanıcı nesnesi dizinde yoksa bir hata oluştur. Olası değerler: `true` veya `false` . |
| RaiseErrorIfClaimsPrincipalAlreadyExists | No | Kullanıcı nesnesi zaten mevcutsa bir hata oluştur. Olası değerler: `true` veya `false` .|
| Applicationobjectıd | No | Uzantı öznitelikleri için uygulama nesne tanımlayıcısı. Değer: bir uygulamanın ObjectID 'si. Daha fazla bilgi için bkz. [özel öznitelikler kullanma](user-flow-custom-attributes.md?pivots=b2c-custom-policy). |
| ClientId | No | Kiracıya üçüncü taraf olarak erişmek için istemci tanımlayıcısı. Daha fazla bilgi için bkz. özel [bir profil düzenleme ilkesinde özel öznitelikler kullanma](user-flow-custom-attributes.md?pivots=b2c-custom-policy) |
| IncludeClaimResolvingInClaimsHandling  | No | Giriş ve çıkış talepleri için, [talep çözümlemenin](claim-resolver-overview.md) teknik profile dahil edilip edilmeyeceğini belirtir. Olası değerler: `true` , veya `false` (varsayılan). Teknik profilde bir talep çözümleyici kullanmak istiyorsanız, bunu olarak ayarlayın `true` . |

### <a name="ui-elements"></a>Kullanıcı arabirimi öğeleri
 
Hata durumunda görüntülenecek hata iletisini yapılandırmak için aşağıdaki ayarlar kullanılabilir. Meta veriler, [kendi kendine onaylanan](self-asserted-technical-profile.md) teknik profilde yapılandırılmalıdır. Hata iletileri [yerelleştirilebilecek](localization.md).

| Öznitelik | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| Usermessageifclaimsprincıpalalreadyexists | No | Bir hata ortaya çıkarılmalıdır (bkz. RaiseErrorIfClaimsPrincipalAlreadyExists Attribute Description), Kullanıcı nesnesi zaten varsa kullanıcıya gösterilecek iletiyi belirtin. |
| UserMessageIfClaimsPrincipalDoesNotExist | No | Bir hata ortaya çıkarılmalıdır (bkz. RaiseErrorIfClaimsPrincipalDoesNotExist Attribute Description), Kullanıcı nesnesi yoksa kullanıcıya gösterilecek iletiyi belirtin. |


## <a name="next-steps"></a>Sonraki adımlar

Azure AD teknik profili kullanımı gibi aşağıdaki makaleye bakın:

- [Azure Active Directory B2C özel ilkeler kullanarak talepler ekleyin ve Kullanıcı girişini özelleştirin](configure-user-input.md)














