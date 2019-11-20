---
title: include dosyası
description: include dosyası
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 10/19/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 4d0a61bf7f4ee9e441a49e21ce6535dc3bff0edc
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67608405"
---
Kaç tane işlev çağrılarını belirtir ne zaman toplanan [ölçümler için Application Insights hesaplama](../articles/azure-functions/functions-monitoring.md#configure-the-aggregator). 

```json
{
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    }
}
```

|Özellik |Varsayılan  | Açıklama |
|---------|---------|---------| 
|batchSize|1000|En fazla istek sayısını toplanacak.| 
|flushTimeout|00:00:30|En uzun süreyi toplanacak dönem.| 

İşlev çağrılarını sınırlar ilk iki zaman toplanan ulaşıldı.