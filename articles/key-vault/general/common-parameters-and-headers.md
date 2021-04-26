---
title: Ortak parametreler ve üst bilgiler
description: Key Vault kaynaklarıyla ilgili yapabileceğiniz tüm işlemlerde ortak olan parametreler ve üstbilgiler.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 616b6061b08258d465b09902556de6903b873199
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749879"
---
# <a name="common-parameters-and-headers"></a>Ortak parametreler ve üst bilgiler

Aşağıdaki bilgiler Key Vault kaynaklarıyla ilgili yapabileceğiniz tüm işlemler için ortaktır:

- HTTP `Host` üst bilgisi her zaman bulunmalı ve kasa ana bilgisayar adının belirtilmesi gerekir. Örnek: `Host: contoso.vault.azure.net`. İstemci teknolojilerinin `Host` üst BILGISINI URI 'den doldurtiğine unutmayın. Örneğin, `GET https://contoso.vault.azure.net/secrets/mysecret{...}` `Host` olarak ayarlanır `contoso.vault.azure.net` . Bu, gibi ham IP adresini kullanarak Key Vault eriştiğinizde `GET https://10.0.0.23/secrets/mysecret{...}` , üstbilginin otomatik değeri `Host` yanlış olur ve `Host` üst bilginin kasa ana bilgisayar adını içerdiğini el ile oluşturmanız gerekir.
- `{api-version}`URI 'deki api sürümü ile değiştirin.
- `{subscription-id}`URI 'deki abonelik tanımlayıcın ile değiştirin
- `{resource-group-name}`Kaynak grubuyla değiştirin. Daha fazla bilgi için bkz. Azure kaynaklarınızı yönetmek için kaynak gruplarını kullanma.
- `{vault-name}`URI 'deki Anahtar Kasası adınızla değiştirin.
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

|Öğe adı | Tür | Description |
|---|---|---|
| kod | string | Oluşan hata türü.|
| message | string | Hatanın neden olduğuna ilişkin bir açıklama. |



## <a name="see-also"></a>Ayrıca Bkz.
 [Azure Key Vault REST API başvurusu](/rest/api/keyvault/)
