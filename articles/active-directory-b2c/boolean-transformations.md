---
title: Azure Active Directory B2C | kimlik deneyimi çerçevesi şemasına yönelik Boole talep dönüştürme örnekleri | Microsoft Docs
description: Azure Active Directory B2C Identity Experience Framework şeması için Boole talep dönüştürme örnekleri.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: da4fc4704ee72210e180ef95fe6a821c8d116fa2
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71064569"
---
# <a name="boolean-claims-transformations"></a>Boole talep dönüştürmeleri

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Bu makalede, Azure Active Directory B2C (Azure AD B2C) ' de kimlik deneyimi çerçevesi şemasının Boolean talep dönüştürmelerinin kullanılmasına yönelik örnekler sağlanmaktadır. Daha fazla bilgi için bkz. [Claimstransformations](claimstransformations.md).

## <a name="andclaims"></a>Veilgili

İki Boolean ınputclaim için bir ve işlemi gerçekleştirir ve outputClaim 'i işlemin sonucu olarak ayarlar.

| Öğe  | Dönüştürme Tionclaimtype  | Veri Türü  | Notlar |
|-------| ------------------------ | ---------- | ----- |
| Inputclaim | inputClaim1 | boolean | Değerlendirilecek ilk ClaimType. |
| Inputclaim | inputClaim2  | boolean | Değerlendirilecek ikinci ClaimType. |
|outputClaim | outputClaim | boolean | Bu talep dönüştürmesinin ardından üretilecek olan ClaimTypes (true veya false). |

Aşağıdaki talep dönüşümünde, ve iki Boolean ClaimTypes gösterilmektedir: `isEmailNotExist`, ve. `isSocialAccount` Giriş taleplerinin `presentEmailSelfAsserted` herikisi`true` de değeri ise, çıkış talebi olarak ayarlanır. `true` Bir düzenleme adımında, yalnızca sosyal hesap e-postası boş ise, otomatik olarak onaylanan bir sayfayı önceden ayarlamak için bir ön koşul kullanabilirsiniz.

```XML
<ClaimsTransformation Id="CheckWhetherEmailBePresented" TransformationMethod="AndClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="isEmailNotExist" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="isSocialAccount" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="presentEmailSelfAsserted" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Örnek

- Giriş talepleri:
    - **inputClaim1**: true
    - **inputClaim2**: false
- Çıkış talepleri:
    - **Outputclaim**: false


## <a name="assertbooleanclaimisequaltovalue"></a>AssertBooleanClaimIsEqualToValue

İki talebin Boole değerlerinin eşit olduğunu denetler ve yoksa bir özel durum oluşturur.

| Öğe | Dönüştürme Tionclaimtype  | Veri Türü  | Notlar |
| ---- | ------------------------ | ---------- | ----- |
| Inputclaim | Inputclaim | boolean | Belirtilme ClaimType. |
| InputParameter |valueToCompareTo | boolean | Karşılaştırılacak değer (true veya false). |

**AssertBooleanClaimIsEqualToValue** talep dönüştürmesi, her zaman [otomatik olarak onaylanan bir teknik profille](self-asserted-technical-profile.md)çağrılan bir [doğrulama teknik profilinden](validation-technical-profile.md) yürütülür. **UserMessageIfClaimsTransformationBooleanValueIsNotEqual** kendiliğinden onaylanan teknik profil meta verileri, teknik profilin kullanıcıya sunduğu hata iletisini denetler.

![Assertstringclaimsareeşittir yürütme](./media/boolean-transformations/assert-execution.png)

Aşağıdaki talep dönüşümünde, bir Boolean ClaimType değerinin bir `true` değerle nasıl kontrol yapılacağı gösterilmektedir. `accountEnabled` ClaimType değeri false ise, bir hata iletisi oluşturulur.

```XML
<ClaimsTransformation Id="AssertAccountEnabledIsTrue" TransformationMethod="AssertBooleanClaimIsEqualToValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="accountEnabled" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="valueToCompareTo" DataType="boolean" Value="true" />
  </InputParameters>
</ClaimsTransformation>
```


Doğrulama teknik profili `AssertAccountEnabledIsTrue` talep dönüşümünü çağırır. `login-NonInteractive`
```XML
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertAccountEnabledIsTrue" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

Otomatik olarak onaylanan teknik profil, doğrulama **oturum açma-etkileşimsiz** teknik profilini çağırır.

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
  <Metadata>
    <Item Key="UserMessageIfClaimsTransformationBooleanValueIsNotEqual">Custom error message if account is disabled.</Item>
  </Metadata>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="login-NonInteractive" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```

### <a name="example"></a>Örnek

- Giriş talepleri:
    - **ınputclaim**: false
    - **Valuetocompareto**: true
- Kaynaklanan Hata oluştu

## <a name="notclaims"></a>Notclaim

Boolean ınputclaim ' in olmayan bir işlemini gerçekleştirir ve outputClaim 'i işlemin sonucu olarak ayarlar.

| Öğe | Dönüştürme Tionclaimtype | Veri Türü | Notlar |
| ---- | ----------------------- | --------- | ----- |
| Inputclaim | Inputclaim | boolean | İşletilen talep. |
| outputClaim | outputClaim | boolean | Bu Claimstranssetting sonrasında üretilen ClaimTypes (true veya false) çağırılır. |

Bir talep üzerinde mantıksal olumsuzlama gerçekleştirmek için bu talep dönüşümünü kullanın.

```XML
<ClaimsTransformation Id="CheckWhetherEmailBePresented" TransformationMethod="NotClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userExists" TransformationClaimType="inputClaim" />
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="userExists" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Örnek

- Giriş talepleri:
    - **ınputclaim**: false
- Çıkış talepleri:
    - **Outputclaim**: true

## <a name="orclaims"></a>Ortalepler

İki Boole ınputclaim veya birini hesaplar ve outputClaim 'yi işlem sonucuyla ayarlar.

| Öğe | Dönüştürme Tionclaimtype | Veri Türü | Notlar |
| ---- | ----------------------- | --------- | ----- |
| Inputclaim | inputClaim1 | boolean | Değerlendirilecek ilk ClaimType. |
| Inputclaim | inputClaim2 | boolean | Değerlendirilecek ikinci ClaimType. |
| outputClaim | outputClaim | boolean | Bu Claimstranssize çağrıldıktan sonra üretilecek olan ClaimTypes (true veya false). |

Aşağıdaki talep dönüşümünde `Or` iki Boolean ClaimTypes gösterilmektedir. Düzenleme adımında, taleplerden birinin değeri ise `true`, otomatik olarak onaylanan bir sayfayı önceden ayarlamak için bir ön koşul kullanabilirsiniz.

```XML
<ClaimsTransformation Id="CheckWhetherEmailBePresented" TransformationMethod="OrClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="isLastTOSAcceptedNotExists" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="isLastTOSAcceptedGreaterThanNow" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="presentTOSSelfAsserted" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
</ClaimsTransformation>
```

### <a name="example"></a>Örnek

- Giriş talepleri:
    - **inputClaim1**: true
    - **inputClaim2**: false
- Çıkış talepleri:
    - **Outputclaim**: true

