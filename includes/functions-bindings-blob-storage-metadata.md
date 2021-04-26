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
ms.openlocfilehash: c6b038297945ca900508a822460e1358a2524d23
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "102455927"
---
Blob tetikleyicisi çeşitli meta veri özellikleri sağlar. Bu özellikler, diğer bağlamalardaki veya kodunuzda parametre olarak bağlama ifadelerinin bir parçası olarak kullanılabilir. Bu değerler [Cloudblob](/dotnet/api/microsoft.azure.storage.blob.cloudblob) türüyle aynı semantiğe sahiptir.

|Özellik  |Tür  |Description  |
|---------|---------|---------|
|`BlobTrigger`|`string`|Tetikleme blobunun yolu.|
|`Uri`|`System.Uri`|Birincil konumun blob URI 'SI.|
|`Properties` |[BlobProperties](/dotnet/api/microsoft.azure.storage.blob.blobproperties)|Blobun sistem özellikleri. |
|`Metadata` |`IDictionary<string,string>`|Blob için Kullanıcı tanımlı meta veriler.|

Örneğin, aşağıdaki C# betiği ve JavaScript örnekleri, kapsayıcısı da dahil olmak üzere tetikleme blobu yolunu günlüğe kaydeder:

```csharp
public static void Run(string myBlob, string blobTrigger, ILogger log)
{
    log.LogInformation($"Full blob path: {blobTrigger}");
} 
```