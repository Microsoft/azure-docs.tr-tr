---
title: include dosyası
description: include dosyası
services: iot-hub
author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 04/28/2020
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 7d6718fb25b3743a50c52f11c8e19d80839b485c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95562929"
---
<!-- operation names for the diag logs for IoT Hub -->

|İşlem adı|Level|Description|
|------------- |-----|-----------|
|UndefinedRouteEvaluation|Bilgi|İleti, bir verme koşuluyla değerlendirilemiyor. Örneğin, iletide yol sorgusu koşulundaki bir özellik yoksa. [Yönlendirme sorgusu söz dizimi](../articles/iot-hub/iot-hub-devguide-routing-query-syntax.md)hakkında daha fazla bilgi edinin.|
|RouteEvaluationError|Hata|İleti biçimindeki bir sorun nedeniyle ileti değerlendirilirken bir hata oluştu. Örneğin, içerik kodlaması belirtilmemişse veya Içerik türü iletide geçerli değilse, bu hata günlüğe kaydedilir. Bunların [Sistem özelliklerinde](../articles/iot-hub/iot-hub-devguide-routing-query-syntax.md#system-properties)ayarlanması gerekir.|
|DroppedMessage|Hata|İleti bırakıldı ve yönlendirilmedi. Bu, ileti herhangi bir yönlendirme sorgusuyla eşleşmediği veya uç nokta ölü ve birkaç yeniden denemeden sonra iletilemeyen bir nedenden kaynaklanıyor olabilir. Uç nokta [alma durumunu](/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth)REST API kullanarak uç nokta hakkında daha fazla ayrıntı almanızı öneririz.|
|EndpointUnhealthy|Hata|Uç nokta IoT Hub iletileri kabul etmiyor ve IoT Hub iletileri yeniden göndermeye çalışıyor. [Uç nokta durumu al](/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth)REST API aracılığıyla bilinen son hatayı gözlemleyerek öneririz.|
|Endpointölü|Hata|Uç nokta, bir saat boyunca IoT Hub iletileri kabul etmiyor. [Uç nokta durumu al](/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth)REST API aracılığıyla bilinen son hatayı gözlemleyerek öneririz.|
|EndpointHealthy|Bilgi|Uç nokta sağlıklı ve IoT Hub iletileri alıyor. Bu ileti sürekli olarak günlüğe kaydedilmez, ancak yalnızca uç nokta yeniden sağlıklı hale geldiğinde günlüğe kaydedilir. Bu ileti, IoT Hub uç noktaya ileti gönderemediği anlamına gelir, ancak uç nokta artık sağlıklı.|
|OrphanedMessage|Bilgi|İleti herhangi bir rota ile eşleşmiyor.|
|Invalidmessage|Hata|Uç nokta ile uyumsuzluk nedeniyle ileti geçersiz. Uç noktanın yapılandırmalarının kontrol edilmesini öneririz.|


*Undefinedrouteevaluation*, *RouteEvaluationError* ve *orphanedmessage* işlemleri, IoT Hub başına dakikada bir en çok bir kez azaltılır ve günlüğe kaydedilir.