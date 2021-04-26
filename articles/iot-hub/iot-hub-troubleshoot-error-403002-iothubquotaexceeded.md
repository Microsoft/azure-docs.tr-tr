---
title: Azure IoT Hub sorunlarını giderme hatası 403002 Iothubquotaaşıldı
description: 403002 Iothubquotaof hatasını nasıl düzelteceğinizi öğrenin
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 3521933baa8dc328910fbacd8b1b6768832fa5f1
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061324"
---
# <a name="403002-iothubquotaexceeded"></a>403002 IoTHubQuotaExceeded

Bu makalede, **403002 Iothubkotasına** yönelik nedenler ve çözümler açıklanmaktadır.

## <a name="symptoms"></a>Belirtiler

IoT Hub tüm istekleri  **403002 Iothubquotaerror** ile başarısız olur. Azure portal, IoT Hub cihaz listesi yüklenmez.

## <a name="cause"></a>Nedeni

IoT Hub için günlük ileti kotası aşıldı. 

## <a name="solution"></a>Çözüm

[IoT Hub 'ındaki birim sayısını yükseltin veya artırın](iot-hub-upgrade.md) veya günlük kotanın yenilenmesi için BIR sonraki UTC gününü bekleyin.

## <a name="next-steps"></a>Sonraki adımlar

* İkizi sorguları ve doğrudan yöntemleri gibi işlemlerin kotasına nasıl dahil olduğunu anlamak için bkz. [IoT Hub fiyatlandırmasını anlama](iot-hub-devguide-pricing.md#charges-per-operation)
* Günlük kota kullanımı için izlemeyi ayarlamak için, *kullanılan ölçüm toplam ileti sayısı* ile bir uyarı ayarlayın. Adım adım yönergeler için bkz. [IoT Hub ölçümleri ve uyarıları ayarlama](tutorial-use-metrics-and-diags.md#set-up-metrics)