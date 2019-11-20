---
title: Azure Active Directory B2C | kimlik deneyimi çerçevesi şemasına yönelik JSON talep dönüştürme örnekleri | Microsoft Docs
description: Azure Active Directory B2C Identity Experience Framework şeması için JSON talep dönüştürme örnekleri.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: ff70b2f54304c83f70ff578e1947d752aafb34a7
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71064157"
---
# <a name="json-claims-transformations"></a>JSON talep dönüştürmeleri

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Bu makalede, Azure Active Directory B2C (Azure AD B2C) öğesinde kimlik deneyimi çerçevesi şemasının JSON talep dönüştürmelerinin kullanılmasına yönelik örnekler sağlanmaktadır. Daha fazla bilgi için bkz. [Claimstransformations](claimstransformations.md).

## <a name="getclaimfromjson"></a>GetClaimFromJson

JSON verilerinden belirtilen bir öğeyi alır.

| Öğe | Dönüştürme Tionclaimtype | Veri Türü | Notlar |
| ---- | ----------------------- | --------- | ----- |
| Inputclaim | ınputjson | dize | Öğeyi almak için talep dönüştürmesi tarafından kullanılan ClaimTypes. |
| InputParameter | Claimtoayıkla | dize | Ayıklanacak JSON öğesinin adı. |
| outputClaim | extractedClaim | dize | Bu talep dönüşümünde oluşturulan ClaimType, _Claimtoextract_ giriş parametresinde belirtilen öğe değeri çağırılır. |

Aşağıdaki örnekte, talep dönüştürmesi JSON verilerinden `emailAddress` öğesini ayıkladı:`{"emailAddress": "someone@example.com", "displayName": "Someone"}`

```XML
<ClaimsTransformation Id="GetEmailClaimFromJson" TransformationMethod="GetClaimFromJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="customUserData" TransformationClaimType="inputJson" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="claimToExtract" DataType="string" Value="emailAddress" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="extractedEmail" TransformationClaimType="extractedClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Örnek

- Giriş talepleri:
  - **ınputjson**: {"emadresi": "someone@example.com", "DisplayName": "Birisi"}
- Giriş parametresi:
    - **Claimtoayıkla**: emaadresi
- Çıkış talepleri:
  - **Extractedclaim**:someone@example.com


## <a name="getclaimsfromjsonarray"></a>GetClaimsFromJsonArray

JSON verilerinden belirtilen öğelerin bir listesini alın.

| Öğe | Dönüştürme Tionclaimtype | Veri Türü | Notlar |
| ---- | ----------------------- | --------- | ----- |
| Inputclaim | Jsonsourceclaım | dize | Talepleri almak için talep dönüştürmesi tarafından kullanılan ClaimTypes. |
| InputParameter | Erroronmissingclaim | boolean | Taleplerden biri eksikse bir hata oluşturulup oluşturulmayacağını belirtir. |
| InputParameter | ıncludeemptyclaim | dize | Boş talepler eklenip eklenmeyeceğini belirtin. |
| InputParameter | jsonSourceKeyName | dize | Öğe anahtarı adı |
| InputParameter | jsonSourceValueName | dize | Öğe değeri adı |
| outputClaim | Collection | String, int, Boolean ve DateTime |Ayıklanacak talepler listesi. Talebin adı, _Jsonsourceclaim_ giriş talebinde belirtilen değere eşit olmalıdır. |

Aşağıdaki örnekte, talep dönüştürmesi şu talepleri ayıklar: e-posta (dize), displayName (dize), membershipNum (int), Active (Boolean) ve Doğum tarihi (DateTime) JSON verilerinden.

```JSON
[{"key":"email","value":"someone@example.com"}, {"key":"displayName","value":"Someone"}, {"key":"membershipNum","value":6353399}, {"key":"active","value":true}, {"key":"birthdate","value":"1980-09-23T00:00:00Z"}]
```

```XML
<ClaimsTransformation Id="GetClaimsFromJson" TransformationMethod="GetClaimsFromJsonArray">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="jsonSourceClaim" TransformationClaimType="jsonSource" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="errorOnMissingClaims" DataType="boolean" Value="false" />
    <InputParameter Id="includeEmptyClaims" DataType="boolean" Value="false" />
    <InputParameter Id="jsonSourceKeyName" DataType="string" Value="key" />
    <InputParameter Id="jsonSourceValueName" DataType="string" Value="value" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="membershipNum" />
    <OutputClaim ClaimTypeReferenceId="active" />
    <OutputClaim ClaimTypeReferenceId="birthdate" />
  </OutputClaims>
</ClaimsTransformation>
```

- Giriş talepleri:
  - **jsonsourceclaim**: [{"Key": "e-posta", "Value"someone@example.com: ""}, {"Key": "DisplayName", "Value": "birisi"}, {"Key": "membershipnum", "Value": 6353399}, {"Key": "etkin", "Value": true}, {"Key": "Doğum tarihi", "Value": "1980-09-23T00:00:00Z "}]
- Giriş parametreleri:
    - **Erroronmissingclaim**: false
    - **ıncludeemptyclaim**: false
    - **jsonSourceKeyName**: anahtar
    - **Jsonsourcevaluename**: değer
- Çıkış talepleri:
  - **e-posta**: "someone@example.com"
  - **DisplayName**: Başkasıyla
  - **Membershipnum**: 6353399
  - **etkin**: doğru
  - **Doğum tarihi**: 1980-09-23T00:00:00Z

## <a name="getnumericclaimfromjson"></a>GetNumericClaimFromJson

JSON verilerinden belirtilen bir sayısal (Long) öğeyi alır.

| Öğe | Dönüştürme Tionclaimtype | Veri Türü | Notlar |
| ---- | ----------------------- | --------- | ----- |
| Inputclaim | ınputjson | dize | Talebi almak için talep dönüştürmesi tarafından kullanılan ClaimTypes. |
| InputParameter | Claimtoayıkla | dize | Ayıklanacak JSON öğesinin adı. |
| outputClaim | extractedClaim | long | Bu Claimstranssetting çağrıldıktan sonra üretilen ClaimType, _Claimtoextract_ giriş parametrelerinde belirtilen öğenin değeri olarak çağırılır. |

Aşağıdaki örnekte, talep dönüştürmesi `id` öğesi JSON verilerinden ayıklar.

```JSON
{
    "emailAddress": "someone@example.com",
    "displayName": "Someone",
    "id" : 6353399
}
```

```XML
<ClaimsTransformation Id="GetIdFromResponse" TransformationMethod="GetNumericClaimFromJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="exampleInputClaim" TransformationClaimType="inputJson" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="claimToExtract" DataType="string" Value="id" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="membershipId" TransformationClaimType="extractedClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Örnek

- Giriş talepleri:
  - **ınputjson**: {"emadresi": "someone@example.com", "DisplayName": "Birisi", "kimlik": 6353399}
- Giriş parametreleri
    - **Claimtoayıkla**: kimlik
- Çıkış talepleri:
    - **Extractedclaim**: 6353399

## <a name="getsinglevaluefromjsonarray"></a>GetSingleValueFromJsonArray

JSON veri dizisindeki ilk öğeyi alır.

| Öğe | Dönüştürme Tionclaimtype | Veri Türü | Notlar |
| ---- | ----------------------- | --------- | ----- |
| Inputclaim | ınputjsonclaim | dize | JSON dizisinden öğeyi almak için talep dönüştürmesi tarafından kullanılan ClaimTypes. |
| outputClaim | extractedClaim | dize | Bu Claimstrans, tarafından üretilen ClaimType, JSON dizisindeki ilk öğe çağırılır. |

Aşağıdaki örnekte, talep dönüştürmesi JSON dizisinden `["someone@example.com", "Someone", 6353399]`ilk öğeyi (e-posta adresi) ayıklar.

```XML
<ClaimsTransformation Id="GetEmailFromJson" TransformationMethod="GetSingleValueFromJsonArray">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userData" TransformationClaimType="inputJsonClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" TransformationClaimType="extractedClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Örnek

- Giriş talepleri:
  - **ınputjsonclaim**: ["someone@example.com", "birisi", 6353399]
- Çıkış talepleri:
  - **Extractedclaim**:someone@example.com

## <a name="xmlstringtojsonstring"></a>XmlStringToJsonString

XML verilerini JSON biçimine dönüştürür.

| Öğe | Dönüştürme Tionclaimtype | Veri Türü | Notlar |
| ---- | ----------------------- | --------- | ----- |
| Inputclaim | xml | dize | Verileri XML 'den JSON biçimine dönüştürmek için talep dönüştürmesi tarafından kullanılan ClaimTypes. |
| outputClaim | json | dize | Bu Claimstransformadıktan sonra üretilen ClaimType, verileri JSON biçiminde çağırırdı. |

```XML
<ClaimsTransformation Id="ConvertXmlToJson" TransformationMethod="XmlStringToJsonString">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="intpuXML" TransformationClaimType="xml" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="outputJson" TransformationClaimType="json" />
  </OutputClaims>
</ClaimsTransformation>
```

Aşağıdaki örnekte, talep dönüştürmesi aşağıdaki XML verilerini JSON biçimine dönüştürür.

#### <a name="example"></a>Örnek
Giriş talebi:

```XML
<user>
  <name>Someone</name>
  <email>someone@example.com</email>
</user>
```

Çıkış talebi:

```JSON
{
  "user": {
    "name":"Someone",
    "email":"someone@example.com"
  }
}
```

