---
title: include dosyası
description: include dosyası
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/19/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 218e98e65c7c78272f32f75a0fdb93e4d87e6948
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92167737"
---
[Application Insights 'de örnekleme özelliğini](../articles/azure-functions/configure-monitoring.md#configure-sampling)denetler.

```json
{
    "applicationInsights": {
        "sampling": {
          "isEnabled": true,
          "maxTelemetryItemsPerSecond" : 5
        }
    }
}
```

|Özellik  |Varsayılan | Description |
|---------|---------|---------| 
|IsEnabled|true|Örneklemeyi etkinleştirilir veya devre dışı bırakır.| 
|maxTelemetryItemsPerSecond|5|Örneklemenin başladığı eşik.| 
