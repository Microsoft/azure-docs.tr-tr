---
title: REST API kullanarak iş ilerlemesini denetleme | Microsoft Docs
description: Bu makalede Azure Media Services V2 REST API kullanılarak yapılan işin ilerlemesini nasıl denetleyeceğinizi gösterilmektedir.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: a1a1f956-c035-448a-af9c-5ac15fcce9dd
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 8727c9e20a9a04dfc48d89b224d15f0cde184f72
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103017350"
---
# <a name="how-to-check-job-progress"></a>Nasıl yapılır: iş ilerlemesini denetleme

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Media Services v2’ye herhangi bir yeni özellik veya işlevsellik eklenmemektedir. <br/>[V3 Media Services](../latest/index.yml)en son sürüme göz atın. Ayrıca bkz. [v2 'den v3 'e geçiş kılavuzu](../latest/migrate-v-2-v-3-migration-introduction.md)

İşleri çalıştırdığınızda, genellikle işin ilerlemesini izlemek için bir yol gerekir. İşin durum özelliğini kullanarak Iş durumunu görebilirsiniz. Durum özelliği hakkında daha fazla bilgi için bkz. [Iş varlığı özellikleri](/rest/api/media/operations/job#job_entity_properties).

## <a name="connect-to-media-services"></a>Media Services’e bağlanmak

AMS API 'sine bağlanma hakkında daha fazla bilgi için bkz. [Azure AD kimlik doğrulamasıyla Azure MEDIA SERVICES API 'Sine erişme](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="check-job-progress"></a>İşin ilerleme durumunu denetleme

İstek:

```console
GET https://media.windows.net/api/Jobs()?$filter=Id%20eq%20'nb%3Ajid%3AUUID%3Af3c43f94-327f-2347-90bb-3bf79f8559f1'&$top=1 HTTP/1.1
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
Host: media.windows.net
```

Yanıt:

```output
HTTP/1.1 200 OK
Cache-Control: no-cache
Content-Length: 450
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Server: Microsoft-IIS/8.5
x-ms-client-request-id: d9b83c57-e26c-4d10-a20b-2be634c4b6a8
request-id: 91d2be35-20ed-4e1c-a147-e82cd000c193
x-ms-request-id: 91d2be35-20ed-4e1c-a147-e82cd000c193
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
Strict-Transport-Security: max-age=31536000; includeSubDomains

{"odata.metadata":"https://media.windows.net/api/$metadata#Jobs","value":[{"Id":"nb:jid:UUID:f3c43f94-327f-2347-90bb-3bf79f8559f1","Name":"Encoding BigBuckBunny into to H264 Adaptive Bitrate MP4 Set 720p","Created":"2015-02-11T01:46:08.897","LastModified":"2015-02-11T01:46:08.897","EndTime":null,"Priority":0,"RunningDuration":0.0,"StartTime":"2015-02-11T01:46:16.58","State":2,"TemplateId":null,"JobNotificationSubscriptions":[]}]} 
```


## <a name="media-services-learning-paths"></a>Media Services’i öğrenme yolları
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geribildirim gönderme
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Ayrıca bkz.

[Media Services işlemler REST API genel bakış](media-services-rest-how-to-use.md)
