---
title: Azure Active Directory B2C Identity Experience Framework şeması için tamsayı talep dönüştürme örnekleri | Microsoft Docs
description: Azure Active Directory B2C Identity Experience Framework şeması için tamsayı talep dönüştürme örnekleri.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: d82f7fecfb35c63d586993fed73a83209782a890
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71064209"
---
# <a name="integer-claims-transformations"></a>Tamsayı talebi dönüşümleri

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Bu makalede, Azure Active Directory B2C (Azure AD B2C) ' de kimlik deneyimi çerçevesi şemasının tamsayı talep dönüştürmelerinin kullanılmasına yönelik örnekler sağlanmaktadır. Daha fazla bilgi için bkz. [Claimstransformations](claimstransformations.md).

## <a name="convertnumbertostringclaim"></a>ConvertNumberToStringClaim

Uzun bir veri türünü dize veri türüne dönüştürür.

| Öğe | Dönüştürme Tionclaimtype | Veri Türü | Notlar |
| ---- | ----------------------- | --------- | ----- |
| Inputclaim | Inputclaim | long | Bir dizeye dönüştürülecek ClaimType. |
| outputClaim | outputClaim | dize | Bu Claimstransbir şekilde üretilen ClaimType çağırılır. |

Bu örnekte, `numericUserId` Long değer türüne sahip talep, dize değer türüne sahip bir `UserId` talebe dönüştürülür.

```XML
<ClaimsTransformation Id="CreateUserId" TransformationMethod="ConvertNumberToStringClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="numericUserId" TransformationClaimType="inputClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="UserId" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Örnek

- Giriş talepleri:
    - **ınputclaim**: 12334 (uzun)
- Çıkış talepleri:
    - **Outputclaim**: "12334" (dize)

