---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: ca8963ed8928745a6d5918c86021199432339c83
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104612999"
---
| Özellik | Açıklama |
|:--- |:---|
|**kimlik/tür** | İsteği yapmak için kullanılan kimlik doğrulaması türü. Örneğin: `OAuth` ,, `Kerberos` , `SAS Key` `Account Key` veya `Anonymous` |
|**Identity/tokenHash**|İstekte kullanılan kimlik doğrulama belirtecinin SHA-256 karması. <br>Kimlik doğrulama türü olduğunda `Account Key` , biçim "KEY1 \| key2 (anahtarın SHA256 karması)" olur. Örneğin: `key1(5RTE343A6FEB12342672AFD40072B70D4A91BGH5CDF797EC56BF82B2C3635CE)`. <br>Kimlik doğrulama türü olduğunda `SAS Key` , biçim "KEY1 \| key2 (anahtarın SHA 256 karması), SASSIGNATURE (SAS BELIRTECI için SHA 256 karması)" olur. Örneğin: `key1(0A0XE8AADA354H19722ED12342443F0DC8FAF3E6GF8C8AD805DE6D563E0E5F8A),SasSignature(04D64C2B3A704145C9F1664F201123467A74D72DA72751A9137DDAA732FA03CF)`. Kimlik doğrulama türü olduğunda `OAuth` , biçim "OAuth BELIRTECININ SHA 256 karması" olur. Örnek: `B3CC9D5C64B3351573D806751312317FE4E910877E7CBAFA9D95E0BE923DW25C`<br> Diğer kimlik doğrulama türleri için, tokenHash alanı yoktur. |
|**Yetkilendirme/eylem** | İsteğe atanan eylem. |
|**Authorization/Roleatamakimliği** | Rol atama KIMLIĞI. Örneğin: `4e2521b7-13be-4363-aeda-111111111111`.|
|**Yetkilendirme/Roledefinitionıd** | Rol tanımı KIMLIĞI. Örneğin: `ba92f5b4-2d11-453d-a403-111111111111"`.|
|**sorumlular/ID** | Güvenlik sorumlusunun KIMLIĞI. Örneğin: `a4711f3a-254f-4cfb-8a2d-111111111111`.|
|**sorumlular/tür** | Güvenlik sorumlusu türü. Örneğin: `ServicePrincipal`. |
|**istek sahibi/AppID** | İstek sahibi olarak kullanılan Open Authorization (OAuth) uygulama KIMLIĞI. <br> Örneğin: `d3f7d5fe-e64a-4e4e-871d-333333333333`.|
|**istek sahibi/izleyici** | İsteğin OAuth hedef kitlesi. Örneğin: `https://storage.azure.com`. |
|**istek sahibi/ObjectID** | İstek sahibinin OAuth nesne KIMLIĞI. Kerberos kimlik doğrulaması durumunda Kerberos kimliği doğrulanmış kullanıcının nesne tanımlayıcısını temsil eder. Örneğin: `0e0bf547-55e5-465c-91b7-2873712b249c`. |
|**istek sahibi/Tenantıd** | Kimliğin OAuth kiracı KIMLIĞI. Örneğin: `72f988bf-86f1-41af-91ab-222222222222`.|
|**istek sahibi/Tokenıssuer** | OAuth belirteci veren. Örneğin: `https://sts.windows.net/72f988bf-86f1-41af-91ab-222222222222/`.|
|**istek sahibi/UPN** | İstek sahibinin kullanıcı asıl adı (UPN). Örneğin: `someone@contoso.com`. |
|**istek sahibi/Kullanıcı adı** | Bu alan yalnızca iç kullanım için ayrılmıştır.|
