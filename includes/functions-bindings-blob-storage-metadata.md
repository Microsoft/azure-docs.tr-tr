---
title: include dosyası
description: include dosyası
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: include
ms.date: 08/02/2019
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: 3788fdb954917f28f64a0dfe035bed4ded0932d5
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/20/2019
ms.locfileid: "69642142"
---
Blob tetikleyicisi çeşitli meta veri özellikleri sağlar. Bu özellikler, diğer bağlamalar bağlama ifadelerinde parçası olarak veya kodunuzu parametreler olarak kullanılabilir. Bu değerler [Cloudblob](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.cloudblob?view=azure-dotnet) türüyle aynı semantiğe sahiptir.

|Özellik  |Tür  |Açıklama  |
|---------|---------|---------|
|`BlobTrigger`|`string`|Tetikleme blobunun yolu.|
|`Uri`|`System.Uri`|Birincil konumun blob URI 'SI.|
|`Properties` |[BlobProperties](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.blobproperties)|Blobun sistem özellikleri. |
|`Metadata` |`IDictionary<string,string>`|Blob için Kullanıcı tanımlı meta veriler.|

Örneğin, aşağıdaki C# komut dosyası ve JavaScript örnekleri, kapsayıcı blob 'un yolunu, kapsayıcısı da dahil olmak üzere günlüğe kaydeder:

```csharp
public static void Run(string myBlob, string blobTrigger, ILogger log)
{
    log.LogInformation($"Full blob path: {blobTrigger}");
} 
```
