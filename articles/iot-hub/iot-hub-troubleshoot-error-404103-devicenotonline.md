---
title: Azure IoT Hub sorunlarını giderme hatası 404103 DeviceNotOnline
description: 404103 DeviceNotOnline hatasını nasıl düzelteceğinizi öğrenin
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: ed4341c1c8df588bf735bdc9c531c2165514d610
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061273"
---
# <a name="404103-devicenotonline"></a>404103 DeviceNotOnline

Bu makalede **404103 DeviceNotOnline** hatalarına yönelik nedenler ve çözümler açıklanmaktadır.

## <a name="symptoms"></a>Belirtiler

Cihaz çevrimiçi olsa bile bir cihaza doğrudan yöntem **404103 DeviceNotOnline** hatasıyla başarısız olur. 

## <a name="cause"></a>Nedeni

Cihazın çevrimiçi olduğunu ve yine de hatayı almaya başladıysanız, bunun nedeni, doğrudan yöntem geri çağrısının cihazda kayıtlı olmaması olabilir.

## <a name="solution"></a>Çözüm

Cihazınızı doğrudan yöntem geri çağırmaları için doğru şekilde yapılandırmak için, bkz. [bir cihazda doğrudan yöntem işleme](iot-hub-devguide-direct-methods.md#handle-a-direct-method-on-a-device).