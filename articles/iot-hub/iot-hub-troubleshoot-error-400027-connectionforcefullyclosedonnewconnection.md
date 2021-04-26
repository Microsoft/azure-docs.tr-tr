---
title: Azure IoT Hub hatası 400027 ConnectionForcefullyClosedOnNewConnection sorunlarını giderme
description: 400027 ConnectionForcefullyClosedOnNewConnection hatasını nasıl düzelteceğinizi öğrenin
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.custom:
- mqtt
- fasttrack-edit
- iot
ms.openlocfilehash: cd531abe1a10abead26055c6b0d4dd328cd3e836
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061375"
---
# <a name="400027-connectionforcefullyclosedonnewconnection"></a>400027 ConnectionForcefullyClosedOnNewConnection

Bu makalede, **400027 ConnectionForcefullyClosedOnNewConnection** hatalarına yönelik nedenler ve çözümler açıklanmaktadır.

## <a name="symptoms"></a>Belirtiler

Cihazınız, .NET SDK ve MQTT aktarım türü kullanan **Connectionstatuschangereason** olarak **Communication_Error** bağlantısı kesildi.

Cihazdan buluta ikizi işlemi (okuma veya düzeltme eki bildiren özellikler gibi) veya doğrudan yöntem çağırma, **400027** hata koduyla başarısız olur.

## <a name="cause"></a>Nedeni

Başka bir istemci aynı kimlik bilgilerini kullanarak IoT Hub yeni bir bağlantı oluşturdu, bu nedenle önceki bağlantıyı kapattı IoT Hub. IoT Hub, aynı kimlik bilgileri kümesi kullanılarak birden fazla istemcinin bağlanmasına izin vermez.

## <a name="solution"></a>Çözüm

Her istemcinin kendi kimliğini kullanarak IoT Hub bağlandığından emin olun.