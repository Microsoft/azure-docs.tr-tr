---
title: Özel ilkeler için sosyal hesap talep dönüştürme örnekleri
titleSuffix: Azure AD B2C
description: Sosyal hesap, Azure Active Directory B2C Identity Experience Framework (ıEF) şeması için bir dönüşüm örnekleri talep ediyor.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c60cecb9d6bfeeefc0569a1a57185d13f0c6442f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94953126"
---
# <a name="social-accounts-claims-transformations"></a>Sosyal hesap talep dönüştürmeleri

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) ' de sosyal hesap kimlikleri `userIdentities` bir **Alternativesecurityıdcollection** talep türünün bir özniteliğinde depolanır. **Alternativesecurityıdcollection** içindeki her öğe veren (Facebook.com gibi kimlik sağlayıcı adı) ve `issuerUserId` veren için benzersiz bir kullanıcı tanımlayıcısı olan öğesini belirtir.

```json
"userIdentities": [{
    "issuer": "google.com",
    "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"
  },
  {
    "issuer": "facebook.com",
    "issuerUserId": "MTIzNDU="
  }]
```

Bu makalede, Azure AD B2C ' deki kimlik deneyimi çerçevesi şemasının sosyal hesap talep dönüştürmelerini kullanma örnekleri verilmektedir. Daha fazla bilgi için bkz. [Claimstransformations](claimstransformations.md).

## <a name="createalternativesecurityid"></a>Createalternativesecurityıd

Azure Active Directory çağrılarında kullanılabilecek kullanıcının Alternativesecurityıd özelliğinin JSON temsilini oluşturur. Daha fazla bilgi için bkz. [Alternativesecurityıd](/graph/api/resources/alternativesecurityid) şeması.

| Öğe | Dönüştürme Tionclaimtype | Veri Türü | Notlar |
| ---- | ----------------------- | --------- | ----- |
| Inputclaim | anahtar | string | Sosyal kimlik sağlayıcısı tarafından kullanılan benzersiz kullanıcı tanımlayıcısını belirten ClaimType. |
| Inputclaim | IdentityProvider | string | Facebook.com gibi sosyal hesap kimlik sağlayıcısı adını belirten ClaimType. |
| OutputClaim | Alternativesecurityıd | string | Claimstrans, tarafından üretilen ClaimType çağrılır. Sosyal hesap kullanıcısının kimliği hakkındaki bilgileri içerir. **Veren** , `identityProvider` talebin değeridir. **Issueruserıd** , `key` talebin Base64 biçimindeki değeridir. |

Bir ClaimType oluşturmak için bu talep dönüşümünü kullanın `alternativeSecurityId` . Bu, gibi tüm sosyal kimlik sağlayıcısı teknik profilleri tarafından kullanılır `Facebook-OAUTH` . Aşağıdaki talep dönüştürmesi, Kullanıcı sosyal hesap KIMLIĞINI ve kimlik sağlayıcısı adını alır. Bu teknik profilin çıktısı, Azure AD dizin hizmetlerinde kullanılabilen bir JSON dize biçimidir.

```xml
<ClaimsTransformation Id="CreateAlternativeSecurityId" TransformationMethod="CreateAlternativeSecurityId">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="issuerUserId" TransformationClaimType="key" />
    <InputClaim ClaimTypeReferenceId="identityProvider" TransformationClaimType="identityProvider" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="alternativeSecurityId" TransformationClaimType="alternativeSecurityId" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Örnek

- Giriş talepleri:
    - **anahtar**: 12334
    - **IdentityProvider**: Facebook.com
- Çıkış talepleri:
    - **Alternativesecurityıd**: {"Issuer": "Facebook.com", "ıssueruserıd": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"}

## <a name="additemtoalternativesecurityidcollection"></a>Additemtoalternativesecurityıdcollection

Bir `AlternativeSecurityId` talebe bir ekler `alternativeSecurityIdCollection` .

| Öğe | Dönüştürme Tionclaimtype | Veri Türü | Notlar |
| ---- | ----------------------- | --------- | ----- |
| Inputclaim | öğe | string | Çıkış talebine eklenecek ClaimType. |
| Inputclaim |  koleksiyonu | Alternativesecurityıdcollection | İlkede varsa talep dönüştürmesi tarafından kullanılan ClaimTypes. Sağlanmışsa, talep dönüştürmesi `item` koleksiyonun sonuna ekler. |
| OutputClaim |  koleksiyonu | Alternativesecurityıdcollection | Bu Claimstranssetting sonrasında üretilen ClaimTypes çağrılır. Hem girişten hem de öğeleri içeren yeni koleksiyon `collection` `item` . |

Aşağıdaki örnek, mevcut bir hesapla yeni bir sosyal kimlik bağlantısı bağlar. Yeni bir sosyal kimlik bağlantısı sağlamak için:
1. **AAD-Userreadusingalternativesecurityıd** ve **AAD-Userreadusingobjectıd** teknik profillerinde, kullanıcının **alternativesecurityıds** talebini çıkış.
1. Kullanıcıdan, bu kullanıcıyla ilişkilendirilmemiş kimlik sağlayıcılarından biriyle oturum açmasını isteyin.
1. **Createalternativesecurityıd** talep dönüşümünü kullanarak, adı ile yeni bir **alternativesecurityıd** talep türü oluşturun`AlternativeSecurityId2`
1. **AlternativeSecurityId2** talebini var olan **Alternativesecurityıds** talebine eklemek Için **Additemtoalternativesecurityıdcollection** talepler dönüşümünü çağırın.
1. **Alternativesecurityıds** talebini Kullanıcı hesabına kalıcı hale getirme

```xml
<ClaimsTransformation Id="AddAnotherAlternativeSecurityId" TransformationMethod="AddItemToAlternativeSecurityIdCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityId2" TransformationClaimType="item" />
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Örnek

- Giriş talepleri:
    - **öğe**: {"Issuer": "Facebook.com", "ıssueruserıd": "MTIzNDU ="}
    - **koleksiyon**: [{"Issuer": "Live.com", "ıssueruserıd": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"}]
- Çıkış talepleri:
    - **koleksiyon**: [{"Issuer": "Live.com", "ıssueruserıd": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"}, {"Issuer": "Facebook.com", "ıssueruserıd": "MTIzNDU ="}]

## <a name="getidentityprovidersfromalternativesecurityidcollectiontransformation"></a>Getıdentityprovidersfromalternativesecurityıdcollectiontransformation

**Alternativesecurityıdcollection** talebinin yeni bir **StringCollection** talebine olan verenler listesini döndürür.

| Öğe | Dönüştürme Tionclaimtype | Veri Türü | Notlar |
| ---- | ----------------------- | --------- | ----- |
| Inputclaim | Alternativesecurityıdcollection | Alternativesecurityıdcollection | Kimlik sağlayıcılarının (veren) listesini almak için kullanılacak ClaimType. |
| OutputClaim | identityProvidersCollection | stringCollection | Bu Claimstranssetting sonrasında üretilen ClaimTypes çağrılır. Alternativesecurityıdcollection giriş talebi ile ilişkili kimlik sağlayıcılarının listesi |

Aşağıdaki talep dönüştürmesi, Kullanıcı **değişim kimliği** talebini okur ve bu hesapla ilişkili kimlik sağlayıcısı adlarının listesini ayıklar. Kullanıcıya hesapla ilişkili kimlik sağlayıcılarının listesini göstermek için output **identityProvidersCollection** kullanın. Ya da kimlik sağlayıcı seçimi sayfasında, çıkış **identityProvidersCollection** talebine göre kimlik sağlayıcılarının listesini filtreleyin. Bu nedenle, Kullanıcı daha önce hesapla ilişkili olmayan yeni sosyal kimlik ' i bağlamayı seçebilir.

```xml
<ClaimsTransformation Id="ExtractIdentityProviders" TransformationMethod="GetIdentityProvidersFromAlternativeSecurityIdCollectionTransformation">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="alternativeSecurityIds" TransformationClaimType="alternativeSecurityIdCollection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="identityProviders" TransformationClaimType="identityProvidersCollection" />
  </OutputClaims>
</ClaimsTransformation>
```

- Giriş talepleri:
    - **Alternativesecurityıdcollection**: [{"Issuer": "Google.com", "ıssueruserıd": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"}, {"Issuer": "Facebook.com", "ıssueruserıd": "MTIzNDU ="}]
- Çıkış talepleri:
    - **identityProvidersCollection**: ["Facebook.com", "Google.com"]

## <a name="removealternativesecurityidbyidentityprovider"></a>Removealternativesecurityıdbyıdentityprovider

Bir **Alternativesecurityıd** değerini bir **Alternativesecurityıdcollection** talebi öğesinden kaldırır.

| Öğe | Dönüştürme Tionclaimtype | Veri Türü | Notlar |
| ---- | ----------------------- | --------- | ----- |
| Inputclaim | IdentityProvider | string | Koleksiyondan kaldırılacak kimlik sağlayıcısı adını içeren ClaimType. |
| Inputclaim |  koleksiyonu | Alternativesecurityıdcollection | Talep dönüştürmesi tarafından kullanılan ClaimTypes. Talep dönüştürmesi, IdentityProvider 'ı koleksiyondan kaldırır. |
| OutputClaim |  koleksiyonu | Alternativesecurityıdcollection | Bu Claimstranssetting sonrasında üretilen ClaimTypes çağrılır. Yeni koleksiyon, IdentityProvider koleksiyondan kaldırıldıktan sonra. |

Aşağıdaki örnek, sosyal kimliğin birinin mevcut bir hesapla bağlantısını kaldırır. Sosyal kimliğin bağlantısını kaldırmak için:
1. **AAD-Userreadusingalternativesecurityıd** ve **AAD-Userreadusingobjectıd** teknik profillerinde, kullanıcının **alternativesecurityıds** talebini çıkış.
2. Kullanıcıdan, bu kullanıcıyla ilişkili olan liste kimlik sağlayıcılarından hangi sosyal hesabın kaldırılacağını seçmesini isteyin.
3. Kimlik sağlayıcısı adı kullanılarak seçilen sosyal kimliği kaldıran **Removealternativesecurityıdbyıdentityprovider** talep dönüşümünü çağıran bir talep dönüştürme teknik profili çağırın.
4. **Alternativesecurityıds** talebini Kullanıcı hesabına kalıcı hale getirin.

```xml
<ClaimsTransformation Id="RemoveAlternativeSecurityIdByIdentityProvider" TransformationMethod="RemoveAlternativeSecurityIdByIdentityProvider">
    <InputClaims>
        <InputClaim ClaimTypeReferenceId="secondIdentityProvider" TransformationClaimType="identityProvider" />
        <InputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
    </InputClaims>
    <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
    </OutputClaims>
</ClaimsTransformation>
</ClaimsTransformations>
```

### <a name="example"></a>Örnek

- Giriş talepleri:
    - **IdentityProvider**: Facebook.com
    - **koleksiyon**: [{"Issuer": "Live.com", "ıssueruserıd": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"}, {"Issuer": "Facebook.com", "ıssueruserıd": "MTIzNDU ="}]
- Çıkış talepleri:
    - **koleksiyon**: [{"Issuer": "Live.com", "ıssueruserıd": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"}]