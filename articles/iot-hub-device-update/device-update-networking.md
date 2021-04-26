---
title: IoT Hub ağ gereksinimleri için cihaz güncelleştirmesi | Microsoft Docs
description: IoT Hub cihaz güncelleştirmesi, farklı amaçlar için çeşitli ağ bağlantı noktalarını kullanır.
author: philmea
ms.author: philmea
ms.date: 1/11/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 0512308fbaa0a725c6ecca573c70c90d8c04e247
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105558389"
---
# <a name="ports-used-with-device-update-for-iot-hub"></a>IoT Hub için cihaz güncelleştirmesiyle kullanılan bağlantı noktaları
ADU farklı amaçlar için çeşitli ağ bağlantı noktaları kullanır.

## <a name="default-ports"></a>Varsayılan bağlantı noktaları

Amaç|Bağlantı Noktası Numarası |
---|---
Ağlardan/CDNs 'den indir  | 80 (HTTP protokolü)
MCC/CDNs 'den indir | 80 (HTTP protokolü)
Azure IoT Hub ADU Aracısı bağlantısı  | 8883 (MQTT Protokolü)

## <a name="use-azure-iot-hub-supported-protocols"></a>Desteklenen Azure IoT Hub protokollerini kullanın
ADU Aracısı desteklenen Azure IoT Hub protokollerinden birini kullanacak şekilde değiştirilebilir.

Desteklenen protokollerin geçerli listesi hakkında [daha fazla bilgi edinin](../iot-hub/iot-hub-devguide-protocols.md) .
