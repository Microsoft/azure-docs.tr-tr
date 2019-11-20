---
title: Azure Batch havuzu silme Tamam olayı | Microsoft Docs
description: Toplu Işlem havuzu silme Tamam olayı başvurusu.
services: batch
author: laurenhughes
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: lahugh
ms.openlocfilehash: 9bf50bd19ca3f4316c4c2ddbcdd3333745ebefd7
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70258555"
---
# <a name="pool-delete-complete-event"></a>Havuz silme tamamlama olayı

 Bu olay, bir havuz silme işlemi tamamlandığında yayınlanır.

 Aşağıdaki örnekte, bir havuz silme Tamam olayının gövdesi gösterilmektedir.

```
{
    "id": "myPool1",
    "startTime": "2016-09-09T22:13:48.579Z",
    "endTime": "2016-09-09T22:14:08.836Z"
}
```

|Öğe|Type|Notlar|
|-------------|----------|-----------|
|`id`|Dize|Havuzun KIMLIĞI.|
|`startTime`|DateTime|Havuzun silmenin başladığı zaman.|
|`endTime`|DateTime|Havuzun silineceği zaman tamamlandı.|

## <a name="remarks"></a>Açıklamalar
Havuz yeniden boyutlandırma işleminin durumları ve hata kodları hakkında daha fazla bilgi için bkz. bir [hesaptan havuz silme](https://docs.microsoft.com/rest/api/batchservice/delete-a-pool-from-an-account).
