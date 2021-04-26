---
title: Özel ilkelerde koşullu erişim teknik profilleri
titleSuffix: Azure AD B2C
description: Azure AD B2C 'de koşullu erişim teknik profillerine yönelik özel ilke başvurusu.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/19/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ebdc1c9c92f6e3debf08cb640e46424c4ad9d5ad
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107721897"
---
# <a name="define-a-conditional-access-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Azure Active Directory B2C özel ilkesinde koşullu erişim teknik profili tanımlama

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory (Azure AD) koşullu erişim, Azure AD B2C tarafından sinyalleri bir araya getirmek, kararlar almak ve kuruluş ilkelerini zorlamak için kullanılan bir araçtır. Risk değerlendirmesi ilke koşulları ile otomatikleştirilmesi, riskli oturum açma işlemlerinin bir kez tanımlanmış ve düzeltilmiş veya engellenmiş olduğu anlamına gelir.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="protocol"></a>Protokol

**Protokol** öğesinin **Name** özniteliğinin olarak ayarlanması gerekir `Proprietary` . **Handler** özniteliği, Azure AD B2C tarafından kullanılan protokol işleyici derlemesinin tam adını içermelidir:

```
Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

Aşağıdaki örnekte, bir koşullu erişim teknik profili gösterilmektedir:

```XML
<TechnicalProfile Id="ConditionalAccessEvaluation">
  <DisplayName>Conditional Access Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="OperationType">Evaluation</Item>
  </Metadata>
```

## <a name="conditional-access-evaluation"></a>Koşullu erişim değerlendirmesi

Her oturum açma için, Azure AD B2C tüm ilkeleri değerlendirir ve Kullanıcı erişimi vermeden önce tüm gereksinimlerin karşılanmasını sağlar. "Erişimi engelle" diğer tüm yapılandırma ayarlarını geçersiz kılar. Koşullu erişim teknik profilinin **değerlendirme** modu, oturum açma sırasında Azure AD B2C tarafından toplanan sinyalleri yerel bir hesapla değerlendirir. Koşullu erişim teknik profilinin sonucu, koşullu erişim değerlendirmesinden kaynaklanan bir talepler kümesidir. Azure AD B2C ilkesi, kullanıcıyı engelle veya Multi-Factor Authentication ile kullanıcıyı zorluk taşıyan bir eylem gerçekleştirmek için bu talepleri bir sonraki düzenleme adımında kullanır. Bu mod için aşağıdaki seçenekler yapılandırılabilir.

### <a name="metadata"></a>Meta veri

| Öznitelik | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| OperationType | Yes | **Değerlendirme** olmalıdır.  |

### <a name="input-claims"></a>Giriş talepleri

**Inputclaim** öğesi, koşullu erişime gönderilen taleplerin bir listesini içerir. Ayrıca, talebin adını koşullu erişim teknik profilinde tanımlanan adla eşleyebilirsiniz.

| Claimreferenceıd | Gerekli | Veri Türü | Açıklama |
| --------- | -------- | ----------- |----------- |
| UserId | Yes | string | Oturum açan kullanıcının tanımlayıcısı. |
| AuthenticationMethodsUsed | Yes |stringCollection | Kullanıcının oturum açmak için kullandığı yöntemlerin listesi. Olası değerler: `Password` , ve `OneTimePasscode` . |
| Ifederal | Yes |boolean | Bir kullanıcının bir Federasyon hesabıyla oturum açmış olup olmadığını gösterir. Değer olmalıdır `false` . |
| IsMfaRegistered | Yes |boolean | Kullanıcının Multi-Factor Authentication için telefon numarasını zaten kaydetmediğini belirtir. |


**Inputclaimstransformations** öğesi, giriş taleplerini değiştirmek veya koşullu erişim hizmetine göndermeden önce yenilerini oluşturmak Için kullanılan **inputclaimstranssize** öğelerinin bir koleksiyonunu içerebilir.

### <a name="output-claims"></a>Çıkış talepleri

**Outputclaim** öğesi ConditionalAccessProtocolProvider tarafından oluşturulan taleplerin bir listesini içerir. Ayrıca, talebin adını aşağıda tanımlanan adla eşleyebilirsiniz.

| Claimreferenceıd | Gerekli | Veri Türü | Açıklama |
| --------- | -------- | ----------- |----------- |
| Zorluklar | Yes |stringCollection | Tanımlanan tehdidi düzeltme eylemlerinin listesi. Olası değerler: `block` |
| MultiConditionalAccessStatus | Yes | stringCollection |  |

**Outputclaimstransformations** öğesi, çıkış taleplerini değiştirmek veya yenilerini oluşturmak için kullanılan bir **outputclaimstransreference** öğeleri koleksiyonu içerebilir.

### <a name="example-evaluation"></a>Örnek: değerlendirme

Aşağıdaki örnek, oturum açma tehdidi değerlendirmek için kullanılan koşullu erişim teknik profilini gösterir.

```XML
<TechnicalProfile Id="ConditionalAccessEvaluation">
  <DisplayName>Conditional Access Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="OperationType">Evaluation</Item>
  </Metadata>
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="IsMfaRegisteredCT" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="UserId" />
    <InputClaim ClaimTypeReferenceId="AuthenticationMethodsUsed" />
    <InputClaim ClaimTypeReferenceId="IsFederated" DefaultValue="false" />
    <InputClaim ClaimTypeReferenceId="IsMfaRegistered" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="conditionalAccessClaimCollection" PartnerClaimType="Challenges" />
    <OutputClaim ClaimTypeReferenceId="ConditionalAccessStatus" PartnerClaimType="MultiConditionalAccessStatus" />
  </OutputClaims>
</TechnicalProfile>
```

## <a name="remediation"></a>Düzeltme

Koşullu erişim teknik profilinin **Düzeltme** modu, oturum açma tanımlanan tehdidi düzeltilen Azure AD B2C bildirir. Düzeltme modu için aşağıdaki seçenekler yapılandırılabilir.

### <a name="metadata"></a>Meta veri

| Öznitelik | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| OperationType | Yes | **Düzeltme** olmalıdır.  |

### <a name="input-claims"></a>Giriş talepleri

**Inputclaim** öğesi, koşullu erişime gönderilen taleplerin bir listesini içerir. Ayrıca, talebin adını koşullu erişim teknik profilinde tanımlanan adla eşleyebilirsiniz.

| Claimreferenceıd | Gerekli | Veri Türü | Açıklama |
| --------- | -------- | ----------- |----------- |
| Memnun kalmayan | Yes | stringCollection| Değerlendirme modundan, zorluk talebine geri dönüş olarak, tanımlanan tehdidi düzeltmek için tatmin eden güçlüklerin listesi.|


**Inputclaimstransformations** öğesi, giriş taleplerini değiştirmek veya koşullu erişim hizmeti çağrılmadan önce yenilerini oluşturmak Için kullanılan **inputclaimstransınfo** öğelerinin bir koleksiyonunu içerebilir.

### <a name="output-claims"></a>Çıkış talepleri

Koşullu Erişim Protokolü Sağlayıcısı herhangi bir **outputclaim** döndürmez, bu nedenle çıkış taleplerini belirtmeniz gerekmez. Ancak, özniteliği ayarladığınız sürece koşullu erişim Protokolü sağlayıcısı tarafından döndürülmeyen talepleri dahil edebilirsiniz `DefaultValue` .

**Outputclaimstransformations** öğesi, çıkış taleplerini değiştirmek veya yenilerini oluşturmak için kullanılan bir **outputclaimstransreference** öğeleri koleksiyonu içerebilir.

### <a name="example-remediation"></a>Örnek: Düzeltme

Aşağıdaki örnekte, oturum açma tehdidi düzeltildiğinde kullanılan bir koşullu erişim teknik profili gösterilmektedir.

```xml
<TechnicalProfile Id="ConditionalAccessRemediation">
  <DisplayName>Conditional Access Remediation</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
  <Metadata>
    <Item Key="OperationType">Remediation</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="conditionalAccessClaimCollection" PartnerClaimType="ChallengesSatisfied" />
  </InputClaims>
</TechnicalProfile>
```

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Active Directory B2C Kullanıcı akışlarına koşullu erişim eklemeyi](conditional-access-user-flow.md)öğrenin.
