---
title: Azure IoT Central müşteri verileri yerleşimi | Microsoft Docs
description: Bu makalede, Azure IoT Central uygulamalarında müşteri verileri yerleşimi açıklanmaktadır.
author: dominicbetts
ms.author: dobett
ms.date: 11/02/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 9cf803ddd5a3c9329cadefffc35ae6d6999323a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93280709"
---
# <a name="azure-iot-central-customer-data-residency"></a>Azure IoT Central müşteri verileri yerleşimi

IoT Central, müşteri verilerini aşağıdaki senaryolar haricinde müşteri tarafından belirtilen Coğrafya dışında depolamaz:

- Mevcut bir IoT Central uygulamasına yeni bir kullanıcı eklendiğinde, davet edilen Kullanıcı uygulamaya ilk kez erişene kadar kullanıcının e-posta KIMLIĞI Coğrafya dışında depolanabilir.

- Pano Haritası kutucukları IoT Central [Azure Maps](../../azure-maps/about-azure-maps.md)kullanın. Mevcut bir IoT Central uygulamasına bir harita kutucuğu eklediğinizde, konum verileri Azure Maps hizmetinin coğrafi konum kurallarına göre işlenebilir veya depolanabilir.
