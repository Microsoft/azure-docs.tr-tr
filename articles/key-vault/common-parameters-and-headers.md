---
title: Ortak parametreler ve üst bilgiler
description: Key Vault kaynaklarıyla ilgili yapabileceğiniz tüm işlemlerde ortak olan parametreler ve üstbilgiler.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 7ec8e1df71e6513b13e9c37174a3363471be01d9
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70879273"
---
# <a name="common-parameters-and-headers"></a>Ortak parametreler ve üst bilgiler

Aşağıdaki bilgiler Key Vault kaynaklarıyla ilgili yapabileceğiniz tüm işlemler için ortaktır:

- URI `{api-version}` 'deki api sürümü ile değiştirin.
- URI `{subscription-id}` 'deki abonelik tanımlayıcın ile değiştirin
- Kaynak `{resource-group-name}` grubuyla değiştirin. Daha fazla bilgi için bkz. Azure kaynaklarınızı yönetmek için kaynak gruplarını kullanma.
- URI `{vault-name}` 'deki Anahtar Kasası adınızla değiştirin.
- Content-Type üst bilgisini Application/JSON olarak ayarlayın.
- Yetkilendirme üst bilgisini Azure Active Directory (AAD) tarafından aldığınız JSON Web Token ayarlayın. Daha fazla bilgi için bkz. [kimlik doğrulama Azure Resource Manager](authentication-requests-and-responses.md) istekleri.

## <a name="common-error-response"></a>Ortak hata yanıtı
Hizmet, başarılı veya başarısız olduğunu göstermek için HTTP durum kodlarını kullanacaktır. Ayrıca, başarısızlıklar aşağıdaki biçimde bir yanıt içerir:

```
   {  
     "error": {  
     "code": "BadRequest",  
     "message": "The key vault sku is invalid."  
     }  
   }  
```

|Öğe adı | Type | Açıklama |
|---|---|---|
| code | dize | Oluşan hata türü.|
| message | dize | Hatanın neden olduğuna ilişkin bir açıklama. |



## <a name="see-also"></a>Ayrıca Bkz.
 [Azure Key Vault REST API başvurusu](/rest/api/keyvault/)
