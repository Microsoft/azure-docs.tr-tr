---
title: TrustFrameworkPolicy-Azure Active Directory B2C | Microsoft Docs
description: Azure Active Directory B2C özel bir ilkenin TrustFrameworkPolicy öğesini belirtin.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/15/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 9bf1cc197a7d6977ccb6ef69e157d9f8a76a58d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103470731"
---
# <a name="trustframeworkpolicy"></a>TrustFrameworkPolicy

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Özel bir ilke, bir veya daha fazla XML biçimli dosya olarak temsil edilir ve hiyerarşik bir zincirde birbirini ifade eder. XML öğeleri, ilke için talepler şeması, talep dönüştürmeleri, içerik tanımları, talep sağlayıcılar, teknik profiller, Kullanıcı yolculuğu ve düzenleme adımları gibi öğeleri tanımlar. Her ilke dosyası, bir ilke dosyasının en üst düzey **TrustFrameworkPolicy** öğesi içinde tanımlanır.

```xml
<TrustFrameworkPolicy
  xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="https://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="yourtenant.onmicrosoft.com"
  PolicyId="B2C_1A_TrustFrameworkBase"
  PublicPolicyUri="http://yourtenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
  ...
```


**TrustFrameworkPolicy** öğesi aşağıdaki öznitelikleri içerir:

| Öznitelik | Gerekli | Açıklama |
|---------- | -------- | ----------- |
| PolicySchemaVersion | Yes | İlkeyi yürütmek için kullanılacak şema sürümü. Değer şu şekilde olmalıdır `0.3.0.0` |
| Tenantobjectıd | No | Azure Active Directory B2C (Azure AD B2C) kiracının benzersiz nesne tanımlayıcısı. |
| TenantId | Yes | Bu ilkenin ait olduğu kiracının benzersiz tanımlayıcısı. |
| PolicyId | Yes | İlke için benzersiz tanımlayıcı. Bu tanımlayıcının önüne *B2C_1A_* gelmelidir |
| PublicPolicyUri | Yes | İlke için kiracı KIMLIĞI ve ilke KIMLIĞI birleşimi olan URI. |
| DeploymentMode | No | Olası değerler: `Production` , veya `Development` . `Production` varsayılan değerdir. İlkenizde hata ayıklamak için bu özelliği kullanın. Daha fazla bilgi için bkz. [günlükleri toplama](troubleshoot-with-application-insights.md). |
| Kullanıcıbağlantısı Neyırecorderendpoint | No | Günlüğe kaydetme için kullanılan uç nokta. Öznitelik varsa değeri olarak ayarlanmalıdır `urn:journeyrecorder:applicationinsights` . Daha fazla bilgi için bkz. [günlükleri toplama](troubleshoot-with-application-insights.md). |


Aşağıdaki örnek, **TrustFrameworkPolicy** öğesinin nasıl kullanılacağını gösterir:

``` XML
<TrustFrameworkPolicy
   xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
   xmlns:xsd="https://www.w3.org/2001/XMLSchema"
   xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
   PolicySchemaVersion="0.3.0.0"
   TenantId="yourtenant.onmicrosoft.com"
   PolicyId="B2C_1A_TrustFrameworkBase"
   PublicPolicyUri="http://yourtenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
```

**TrustFrameworkPolicy** öğesi aşağıdaki öğeleri içerir:

| Öğe | Öğeleri | Description |
| ------- | ----------- | ----------- |
| BasePolicy| 0:1| Temel bir ilkenin tanımlayıcısı. |
| [BuildingBlocks](buildingblocks.md) | 0:1 | İlkenizin yapı taşları. |
| [ClaimsProviders](claimsproviders.md) | 0:1 | Talep sağlayıcılar koleksiyonu. |
| [UserJourneys](userjourneys.md) | 0:1 | Kullanıcı bir koleksiyonu. |
| [RelyingParty](relyingparty.md) | 0:1 | Bağlı olan taraf ilkesinin tanımı. |

Bir ilkeyi başka bir ilkeden devralması için bir **Basepolicy** öğesi, Ilke dosyasının **TrustFrameworkPolicy** öğesi altında bildirilmelidir. **Basepolicy** öğesi, bu ilkenin türetildiği temel ilkeye bir başvurudur.

**Basepolicy** öğesi aşağıdaki öğeleri içerir:

| Öğe | Öğeleri | Description |
| ------- | ----------- | --------|
| TenantId | 1:1 | Azure AD B2C kiracınızın tanımlayıcısı. |
| PolicyId | 1:1 | Üst ilke tanımlayıcısı. |


Aşağıdaki örnek, temel bir ilkenin nasıl ekleneceğini gösterir. Bu **B2C_1A_TrustFrameworkExtensions** ilke **B2C_1A_TrustFrameworkBase** ilkesinden türetilir.

``` XML
<TrustFrameworkPolicy
   xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
   xmlns:xsd="https://www.w3.org/2001/XMLSchema"
   xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
   PolicySchemaVersion="0.3.0.0"
   TenantId="yourtenant.onmicrosoft.com"
   PolicyId="B2C_1A_TrustFrameworkExtensions"
   PublicPolicyUri="http://yourtenant.onmicrosoft.com/B2C_1A_TrustFrameworkExtensions">

  <BasePolicy>
    <TenantId>yourtenant.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkBase</PolicyId>
  </BasePolicy>
  ...
</TrustFrameworkPolicy>
```

