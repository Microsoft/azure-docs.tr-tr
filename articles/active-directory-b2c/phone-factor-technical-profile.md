---
title: Özel ilkede telefon faktörü teknik profili tanımlama
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C bir özel ilkede telefon faktörü teknik profili tanımlayın.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/12/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 322e4b78fbfb38f1822fb7a7cdcdbfcc0738b303
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91950406"
---
# <a name="define-a-phone-factor-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Azure Active Directory B2C özel ilkesinde telefon faktörü teknik profili tanımlama

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) telefon numaralarını kaydetme ve doğrulama desteği sağlar. Bu teknik profil:

- Bir telefon numarasını doğrulamak veya kaydetmek üzere kullanıcıyla etkileşime geçmek için bir kullanıcı arabirimi sağlar.
- Telefon aramalarını ve telefon numarasını doğrulamak için SMS iletilerini destekler.
- Birden çok telefon numarasını destekler. Kullanıcı doğrulamak için telefon numaralarının birini seçebilir.  
- Kullanıcının yeni bir telefon numarası sağladığını gösteren bir talep döndürür. Telefon numarasının Azure AD B2C Kullanıcı profilinde kalıcı olup olmaması gerektiğine karar vermek için bu talebi kullanabilirsiniz.  
- Görünümü ve kullanımı denetlemek için bir [İçerik tanımı](contentdefinitions.md) kullanır.

## <a name="protocol"></a>Protokol

**Protokol** öğesinin **Name** özniteliğinin olarak ayarlanması gerekir `Proprietary` . **Handler** özniteliği, telefon faktörü için Azure AD B2C tarafından kullanılan protokol işleyici derlemesinin tam adını içermelidir:`Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

Aşağıdaki örnekte, kayıt ve doğrulama için bir telefon faktörü teknik profili gösterilmektedir:

```xml
<TechnicalProfile Id="PhoneFactor-InputOrVerify">
  <DisplayName>PhoneFactor</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## <a name="input-claims-transformations"></a>Giriş talep dönüştürmeleri

Inputclaimstransformations öğesi, giriş taleplerini değiştirmek veya yenilerini oluşturmak için kullanılan bir giriş talep dönüştürmeleri koleksiyonu içerebilir. Aşağıdaki giriş talepleri dönüştürmesi, `UserId` giriş talep koleksiyonunda daha sonra kullanılan bir talep oluşturur.

```xml
<InputClaimsTransformations>
  <InputClaimsTransformation ReferenceId="CreateUserIdForMFA" />
</InputClaimsTransformations>
```

## <a name="input-claims"></a>Giriş talepleri

Inputclaim öğesi aşağıdaki talepleri içermelidir. Ayrıca, talebin adını telefon faktörü teknik profilinde tanımlanan adla eşleyebilirsiniz. 

|  Veri türü| Gerekli | Description |
| --------- | -------- | ----------- | 
| dize| Yes | Kullanıcı için benzersiz bir tanımlayıcı. Talep adı veya PartnerClaimType olarak ayarlanmalıdır `UserId` . Bu talep, kişisel olarak tanımlanabilir bilgiler içermemelidir.|
| string| Yes | Talep türleri listesi. Her talep bir telefon numarası içerir. Giriş taleplerinden herhangi biri bir telefon numarası içermiyorsa, kullanıcıdan yeni bir telefon numarası kaydetmesi ve doğrulaması istenir. Doğrulanan telefon numarası, çıkış talebi olarak döndürülür. Giriş taleplerinden biri bir telefon numarası içeriyorsa, kullanıcıdan doğrulanması istenir. Birden çok giriş talebi bir telefon numarası içeriyorsa, kullanıcıdan telefon numaralarının birini seçmesi ve doğrulaması istenir. |

Aşağıdaki örnekte, birden çok telefon numarası kullanımı gösterilmektedir. Daha fazla bilgi için bkz. [örnek ilke](https://github.com/azure-ad-b2c/samples/tree/master/policies/mfa-add-secondarymfa).

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="userIdForMFA" PartnerClaimType="UserId" />
  <InputClaim ClaimTypeReferenceId="strongAuthenticationPhoneNumber" />
  <InputClaim ClaimTypeReferenceId="secondaryStrongAuthenticationPhoneNumber" />
</InputClaims>
```

## <a name="output-claims"></a>Çıkış talepleri

Outputclaim öğesi, telefon faktörü teknik profili tarafından döndürülen taleplerin bir listesini içerir.

|  Veri türü| Gerekli | Açıklama |
|  -------- | ----------- |----------- |
| boolean | Yes | Yeni telefon numarasının Kullanıcı tarafından girilip girilmediğini belirtir. Talep adı veya PartnerClaimType şu şekilde ayarlanmalıdır `newPhoneNumberEntered`|
| string| Yes | Doğrulanan telefon numarası. Talep adı veya PartnerClaimType olarak ayarlanmalıdır `Verified.OfficePhone` .|

OutputClaimsTransformations öğesi, çıkış taleplerini değiştirmek veya yenilerini oluşturmak için kullanılan bir Outputclaimstransreference öğeleri koleksiyonu içerebilir.

## <a name="cryptographic-keys"></a>Şifreleme anahtarları

**Cryptographickeys** öğesi kullanılmıyor.


## <a name="metadata"></a>Meta veri

| Öznitelik | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| Contentdefinitionreferenceıd | Yes | Bu teknik profille ilişkili [içerik tanımının](contentdefinitions.md) tanımlayıcısı. |
| ManualPhoneNumberEntryAllowed| No | Bir kullanıcının el ile telefon numarası girip giremeyeceğini belirtin. Olası değerler: `true` , veya `false` (varsayılan).|
| Setting. authenticationMode | No | Telefon numarasını doğrulama yöntemi. Olası değerler: `sms` , `phone` , veya `mixed` (varsayılan).|
| ayarlanıyor. otomatik çevir| No| Teknik profilin otomatik olarak bir SMS 'ye çevrilip çevrilmeyeceğini veya otomatik olarak gönderileceğini belirtin. Olası değerler: `true` , veya `false` (varsayılan). Otomatik arama `setting.authenticationMode` için meta verilerin veya olarak ayarlanması gerekir `sms` `phone` . Giriş talep koleksiyonunda tek bir telefon numarası olmalıdır. |

### <a name="ui-elements"></a>Kullanıcı arabirimi öğeleri

Telefon faktörü kimlik doğrulama sayfası kullanıcı arabirimi öğeleri [yerelleştirilebilecek](localization-string-ids.md#phone-factor-authentication-page-user-interface-elements).

## <a name="next-steps"></a>Sonraki adımlar

- MFA başlangıç paketi [ile sosyal ve yerel hesapları](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccountsWithMfa) denetleyin.
