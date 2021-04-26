---
title: Azure Media Services REST çağrıları için Postman ortamını içeri aktarın
description: Bu konu, Azure Media Services REST çağrıları için Postman ortamının bir tanımını sağlar.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.openlocfilehash: 5b1705fb2b3b1b69f79158747827d764f1bef4f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103007859"
---
# <a name="import-the-postman-environment"></a>Postman ortamını içeri aktarma

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)] 

Bu makale, Media Services REST API 'Leri çağıran gruplandırılmış HTTP isteklerini içeren [Postman koleksiyonunu](postman-collection.md) kullanan **Postman** ortam değişkenlerinin tanımını içerir. Ortam ve koleksiyon dosyaları, [Media Services REST API çağrıları Için Postman yapılandırma](media-rest-apis-with-postman.md) öğreticisi tarafından kullanılır.

> [!NOTE]
> Değeri `AzureADSTSEndpoint `  =  `https://login.microsoftonline.com/{{TenantId}}/oauth2/token` . Kiracı KIMLIĞINIZI almak için, farenizi portalda (sağ üst köşede) Kullanıcı adınızın üzerine getirip "Dizin: Microsoft ({{TENANTıD}}) içinde bulabilirsiniz.

```
{
  "id": "2dbce3ce-74c2-2ceb-0461-c4c2323f5b09",
  "name": "AzureMedia",
  "values": [
    {
      "enabled": true,
      "key": "TenantID",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "AzureADSTSEndpoint",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "RESTAPIEndpoint",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "ClientID",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "ClientSecret",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "AccessToken",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "LastAssetId",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "LastAccessPolicyId",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "UploadURL",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "MediaFileName",
      "value": "BigBuckBunny.mp4",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "LastChannelId",
      "value": "",
      "type": "text"
    }
  ],
  "_postman_variable_scope": "environment",
  "_postman_exported_using": "Postman/5.5.0"
}
```
