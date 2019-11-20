---
title: include dosyası
description: include dosyası
services: functions
author: nzthiago
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2018
ms.author: nzthiago
ms.custom: include file
ms.openlocfilehash: 47c2429363945cf1529cee6e49947aaea95b7022
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/18/2019
ms.locfileid: "72597427"
---
## <a name="timeout"></a>İşlev uygulaması zaman aşımı süresi 

Bir işlev uygulamasının zaman aşımı süresi, [Host. JSON](../articles/azure-functions/functions-host-json.md#functiontimeout) proje dosyasındaki functiontimeout özelliği tarafından tanımlanır. Aşağıdaki tabloda, hem planlar hem de çalışma zamanı sürümlerinde, varsayılan ve en büyük değerler dakika cinsinden gösterilmektedir:

| Plan | Çalışma zamanı sürümü | Varsayılan | Maksimum |
|------|---------|---------|---------|
| Tüketim | 'in | 5 | 10 |
| Tüketim | 2.x | 5 | 10 |
| Tüketim | 3. x (Önizleme) | 5 | 10 |
| App Service | 'in | İş çalışma zamanında | İş çalışma zamanında |
| App Service | 2.x | 30 | İş çalışma zamanında |
| App Service | 3. x (Önizleme) | 30 | İş çalışma zamanında |

> [!NOTE] 
> İşlev uygulaması zaman aşımı ayarından bağımsız olarak, 230 saniye bir HTTP tetiklenen işlevin bir isteğe yanıt vermek için gidebildiği en uzun süredir. Bunun nedeni, [Azure Load Balancer varsayılan boşta kalma zaman aşımı süresi](../articles/app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds). Daha uzun işlem süreleri için [dayanıklı işlevler zaman uyumsuz model](../articles/azure-functions/durable/durable-functions-overview.md#async-http) kullanmayı veya [gerçek işi erteleyin ve anında yanıt döndürmenizi](../articles/azure-functions/functions-best-practices.md#avoid-long-running-functions)düşünün.
