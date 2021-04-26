---
title: "Hızlı başlangıç: Azure RTOS için Defender-IoT-Micro-Agent 'ı yapılandırma ve etkinleştirme"
description: Bu hızlı başlangıçta, Azure IoT Hub Azure RTOS hizmeti için Defender-IoT-mikro-Agent ' ı eklemeyi ve etkinleştirmeyi öğrenin.
services: defender-for-iot
ms.topic: quickstart
ms.date: 01/24/2021
ms.openlocfilehash: 0575e9303068b203a5d8cd51c8a8cc1843de6f4d
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106384644"
---
# <a name="quickstart-defender-iot-micro-agent-for-azure-rtos-preview"></a>Hızlı başlangıç: Defender-IoT-mikro-aracı Azure RTOS (Önizleme)

Bu makalede, başlamadan önce önkoşulların açıklaması sağlanır ve Azure RTOS hizmeti için IoT Hub Defender-IoT-mikro aracısının nasıl etkinleştirileceği açıklanır. Şu anda bir IoT Hub yoksa, kullanmaya başlamak için [Azure Portal kullanarak IoT Hub oluşturma](../iot-hub/iot-hub-create-through-portal.md) makalesine bakın.

## <a name="prerequisites"></a>Önkoşullar 

### <a name="supported-devices"></a>Desteklenen cihazlar

- ST STM32F746G bulma seti
- NXP i.MX RT1060 EVK
- Mikro yonga SAM E54 Xplaj Pro EVK

[Azure RTOS GitHub kaynağı Için Defender-IoT-mikro-Agent](https://github.com/azure-rtos/azure-iot-preview/releases)' den seçtiğiniz belirli bir Pano ve araç (IAR, yarı IDE veya bilgisayar) için. zip dosyalarından birini indirin, derleyin ve çalıştırın.

### <a name="azure-resources"></a>Azure kaynakları

Kullanmaya başlamak için sonraki aşama, Azure kaynaklarınızı hazırlıyor. Bir IoT Hub gerekir ve bir Log Analytics çalışma alanı öneririz. IoT Hub için cihazınıza bağlanmak üzere IoT Hub bağlantı dizeniz olması gerekir. 
  
### <a name="iot-hub-connection"></a>IoT Hub bağlantısı

Başlamak için bir IoT Hub bağlantısı gerekir. 

1. **IoT Hub** Azure Portal açın.

1. **IoT cihazlarına** gidin.

1. **Oluştur**’u seçin.

1. IoT bağlantı dizesini [yapılandırma dosyasına](how-to-azure-rtos-security-module.md)kopyalayın.

Bağlantı kimlik bilgileri, Kullanıcı uygulama yapılandırmasından **host_name**, **DEVICE_ID** ve **DEVICE_SYMMETRIC_KEY** alınır.

Azure RTOS için Defender-IoT-Micro-Agent, **MQTT** protokolüne göre Azure IoT ara yazılım bağlantılarını kullanır.

## <a name="next-steps"></a>Sonraki adımlar

Çözümünüzü yapılandırmayı ve özelleştirmeyi tamamlamak için sonraki makaleye ilerleyin.

> [!div class="nextstepaction"]
> [Azure RTOS için Defender-IoT-mikro-Agent 'ı yapılandırma ve özelleştirme (Önizleme)](how-to-azure-rtos-security-module.md)
