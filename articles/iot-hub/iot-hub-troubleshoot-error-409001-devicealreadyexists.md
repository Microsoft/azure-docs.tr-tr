---
title: Azure IoT Hub sorun giderme hatası 409001 DeviceAlreadyExists
description: 409001 DeviceAlreadyExists hatasını nasıl düzelteceğinizi öğrenin
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: b075519fff2c7c328778d770ef68e9751922807b
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061137"
---
# <a name="409001-devicealreadyexists"></a>409001 DeviceAlreadyExists

Bu makalede, **409001 DeviceAlreadyExists** hatalarına yönelik nedenler ve çözümler açıklanmaktadır.

## <a name="symptoms"></a>Belirtiler

IoT Hub bir cihazı kaydetmeye çalışırken, istek **409001 DeviceAlreadyExists** hatasıyla başarısız olur.

## <a name="cause"></a>Nedeni

IoT Hub 'ında aynı cihaz KIMLIĞINE sahip bir cihaz zaten var. 

## <a name="solution"></a>Çözüm

Farklı bir cihaz KIMLIĞI kullanıp yeniden deneyin.