---
title: include dosyası
description: include dosyası
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/28/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 24a07109fc8f4d6ebd283dee7ee00107f0eb49b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95559861"
---
Uç noktaların sistem durumunu almak için [uç nokta durumu al](/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) REST API kullanabilirsiniz. Uç nokta bu durumlardan birinde olduğunda gecikme süresi bekleniyorsa, uç nokta durumu ölü veya sağlıksız olduğunda hataları belirlemek ve hata ayıklamak için ileti gecikmesini yönlendirme ile ilgili [IoT Hub yönlendirme ölçümlerini](../articles/iot-hub/monitor-iot-hub-reference.md#routing-metrics) kullanmanızı öneririz. IoT Hub ölçümlerini kullanma hakkında daha fazla bilgi için bkz. [Monitor IoT Hub](../articles/iot-hub/monitor-iot-hub.md).

|Sistem Durumu|Description|
|---|---|
|sağlıklı|Uç nokta iletileri beklendiği gibi kabul ediyor.|
|sağlıksız|Uç nokta iletileri kabul etmiyor ve IoT Hub bu uç noktaya ileti gönderimi yeniden deniyor.|
|bilinmeyen|IoT Hub bu uç noktaya ileti teslim etmeyi denemedi.|
|düzey|Uç nokta, beklenenden yavaş iletileri kabul ediyor veya sağlıksız bir durumdan kurtarıyor.|
|teslim edilemeyen iletiler|IoT Hub artık bu uç noktaya ileti teslim ediyor. Bu uç noktaya ileti gönderme denemeleri başarısız oldu.|