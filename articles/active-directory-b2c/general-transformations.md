---
title: Azure Active Directory B2C Identity Experience Framework şeması için genel talep dönüştürme örnekleri
description: Azure Active Directory B2C Identity Experience Framework şeması için genel talep dönüştürme örnekleri.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 08/27/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 7cea33cb61f8f8d0fe305a757f11c80bc5da24ca
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/26/2019
ms.locfileid: "70032894"
---
# <a name="general-claims-transformations"></a>Genel talep dönüştürmeleri

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Bu makalede, Azure Active Directory B2C (Azure AD B2C) ' de kimlik deneyimi çerçevesi şemasının genel talep dönüştürmelerinin kullanılmasına yönelik örnekler sağlanmaktadır. Daha fazla bilgi için bkz. [Claimstransformations](claimstransformations.md).

## <a name="doesclaimexist"></a>Yok edilebilir

**Inputclaim** 'nin var olup olmadığını denetler ve **outputclaim** 'yi true veya false olarak ayarlayın.

| Öğe | Dönüştürme Tionclaimtype | Veri Türü | Notlar |
| ---- | ----------------------- | --------- | ----- |
| Inputclaim | ınputclaim |Any | Varlığının doğrulanması gereken giriş talebi. |
| OutputClaim | outputClaim | boolean | Bu Claimstransbir şekilde üretilen ClaimType çağırılır. |

Bir talebin mevcut olup olmadığını veya herhangi bir değer içerip içerdiğini denetlemek için bu talep dönüşümünü kullanın. Dönüş değeri, talebin mevcut olup olmadığını gösteren bir Boole değeridir. Aşağıdaki örnek, e-posta adresinin mevcut olup olmadığını denetler.

```XML
<ClaimsTransformation Id="CheckIfEmailPresent" TransformationMethod="DoesClaimExist">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="inputClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isEmailPresent" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Örnek

- Giriş talepleri:
  - **ınputclaim**:someone@contoso.com
- Çıkış talepleri:
  - **Outputclaim**: true

## <a name="hash"></a>Karma

Anahtar ve gizli anahtar kullanarak, sağlanmış düz metni karma olarak kullanın. Kullanılan karma algoritması SHA-256 ' dir.

| Öğe | Dönüştürme Tionclaimtype | Veri Türü | Notlar |
| ---- | ----------------------- | --------- | ----- |
| Inputclaim | düz metin | dize | Şifrelenecek giriş talebi |
| Inputclaim | değerinin | dize | Anahtar parametresi. Talep dönüştürmeyi kullanarak `CreateRandomString` rastgele bir değer oluşturabilirsiniz. |
| InputParameter | randomizerSecret | dize | Mevcut bir Azure AD B2C **ilkesi anahtarına**işaret eder. Yeni bir ilke anahtarı oluşturmak için: Azure AD B2C kiracınızda, **Yönet**altında **kimlik deneyimi çerçevesi**' ni seçin. Kiracınızda kullanılabilir olan anahtarları görüntülemek için **ilke anahtarlarını** seçin. **Add (Ekle)** seçeneğini belirleyin. **Seçenekler**Için **el ile**' yi seçin. Bir ad sağlayın ( *B2C_1A_* ön eki otomatik olarak eklenebilir.). **Gizli** metin kutusuna, kullanmak istediğiniz tüm gizli anahtarı (1234567890 gibi) girin. **Anahtar kullanımı**için **imza**' yı seçin. **Oluştur**’u seçin. |
| OutputClaim | yla | dize | Bu talep dönüştürmesinin ardından üretilen ClaimType çağırılır. `plaintext` Inputclaim 'de yapılandırılan talep. |

```XML
<ClaimsTransformation Id="HashPasswordWithEmail" TransformationMethod="Hash">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="password" TransformationClaimType="plaintext" />
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="salt" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="randomizerSecret" DataType="string" Value="B2C_1A_AccountTransformSecret" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="hashedPassword" TransformationClaimType="hash" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Örnek

- Giriş talepleri:
  - **düz metin**:MyPass@word1
  - **anahtar**: 487624568
  - **randomizerSecret**: B2C_1A_AccountTransformSecret
- Çıkış talepleri:
  - **Outputclaim**: CdMNb/KTEfsWzh9MR1kQGRZCKjuxGMWhA5YQNihzV6U =
