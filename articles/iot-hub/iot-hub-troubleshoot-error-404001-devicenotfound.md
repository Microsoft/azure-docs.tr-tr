---
title: Azure IoT Hub sorunlarını giderme hatası 404001 DeviceNotFound
description: 404001 DeviceNotFound hatasını nasıl düzelteceğinizi öğrenin
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 09f3c00dadc6e95aae01fb8082fb746e155d4688
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061290"
---
# <a name="404001-devicenotfound"></a>404001 DeviceNotFound

Bu makalede, **404001 DeviceNotFound** hatalarına yönelik nedenler ve çözümler açıklanmaktadır.

## <a name="symptoms"></a>Belirtiler

C2D Message, ikizi Update veya Direct yöntemi gibi buluttan cihaza (C2D) iletişim sırasında, işlem **404001 DeviceNotFound** hatasıyla başarısız olur.

## <a name="cause"></a>Nedeni

Cihaz IoT Hub tarafından bulunamadığı için işlem başarısız oldu. Cihaz kayıtlı değil veya devre dışı.

## <a name="solution"></a>Çözüm

Kullandığınız cihaz KIMLIĞINI kaydedin, sonra yeniden deneyin.