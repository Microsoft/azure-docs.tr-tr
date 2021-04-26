---
title: Azure Batch havuzu otomatik ölçeklendirme olayı
description: Havuz otomatik ölçeklendirme yürütüldükten sonra yayılan Batch havuzu otomatik ölçeklendirme olayı için başvuru. Günlüğün içeriği, havuz için otomatik ölçeklendirme formülü ve değerlendirme sonuçları ortaya çıkarır.
ms.topic: reference
ms.date: 10/08/2020
ms.openlocfilehash: e548ed484e5f683a8f79434ce4095ac66900f01a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91852176"
---
# <a name="pool-autoscale-event"></a>Havuz otomatik ölçeklendirme olayı

 Bu olay, havuz otomatik Ölçeklendirmesi yürütüldükten sonra yayınlanır. Günlüğün içeriği, havuz için otomatik ölçeklendirme formülü ve değerlendirme sonuçları ortaya çıkarır.

 Aşağıdaki örnek, yetersiz örnek veri nedeniyle başarısız olan havuz otomatik ölçeklendirme için havuz otomatik ölçeklendirme olayının gövdesini gösterir.

```
{
    "id": "myPool1",
    "timestamp": "2020-09-21T18:59:56.204Z",
    "formula": "...",
    "results": "...",
    "error": {
        "code": "InsufficientSampleData",
        "message": "Autoscale evaluation failed due to insufficient sample data",
        "values": [{
                "name": "Message",
                "value": "Line 15, Col 44: Insufficient data from data set: $RunningTasks wanted 70%, received 50%"
            }
        ]
    }
}
```

|Öğe|Tür|Notlar|
|-------------|----------|-----------|
|`id`|Dize|Havuzun KIMLIĞI.|
|`timestamp`|DateTime|Otomatik ölçeklendirme yürütüldüğünde zaman damgası.|
|`formula`|Dize|Otomatik ölçeklendirme için tanımlanan formül.|
|`results`|Dize|Formülde kullanılan tüm değişkenlerin değerlendirme sonuçları.|
|[`error`](#error)|Karmaşık Tür|Otomatik ölçeklendirme için ayrıntılı hata.|

###  <a name="error"></a><a name="error"></a> hatayla

|Öğe adı|Tür|Notlar|
|------------------|----------|-----------|
|`code`|Dize|Otomatik ölçeklendirme hatası için bir tanımlayıcı. Kodlar değişmez ve programlı olarak tüketilmelidir.|
|`message`|Dize|Otomatik ölçeklendirme hatasını açıklayan ve Kullanıcı arabiriminde görüntülenmek üzere uygun olması amaçlanan bir ileti.|
|`values`|Dizi|Otomatik ölçeklendirme hatasının daha ayrıntılı ayrıntılarını açıklayan ad-değer çiftleri listesi.|
