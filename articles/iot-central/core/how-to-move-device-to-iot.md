---
title: Bir cihazı Azure IoT Central 'a taşıma IoT Hub
description: Cihazı Azure IoT Central IoT Hub 'tan taşıma
author: philmea
ms.author: philmea
ms.date: 02/20/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 79aead5b374714e7856897a9b85349198341cb3d
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107872740"
---
# <a name="how-to-transfer-a-device-to-azure-iot-central-from-iot-hub"></a>IoT Hub 'dan bir cihazı Azure IoT Central aktarma

*Bu makale işleçler ve cihaz geliştiricileri için geçerlidir.*  

Bu makalede, bir cihazın Azure IoT Central uygulamasına bir IoT Hub nasıl aktarılacağı açıklanır. 

Bir cihaz, uygulamanıza bağlanması gereken bilgileri almak için önce bir DPS uç noktasına bağlanır. Dahili olarak, IoT Central uygulamanız cihaz bağlantısını işlemek için bir IoT Hub 'ı kullanır.  

Bir cihaz, bir bağlantı dizesi veya DPS kullanarak doğrudan bir IoT Hub 'ına bağlanabilir. [Azure IoT Hub cihaz sağlama hizmeti (DPS)](../../iot-dps/about-iot-dps.md) IoT Central yoldur.

## <a name="to-move-the-device-to-azure-iot-central"></a>Cihazı Azure 'a taşımak için IoT Central

Bir cihazı, bir cihazın IoT Hub ile güncelleştirilmesi gereken IoT Central bağlamak için:

* IoT Central uygulamasının [kapsam kimliği](../../iot-dps/concepts-service.md) .
* [Grup SAS](concepts-get-connected.md) anahtarından veya [X. 509.440 CERT](../../iot-hub/iot-hub-x509ca-overview.md) 'dan türetilmiş bir anahtar

IoT Central etkileşimde bulunmak için cihazın uyguladığı özellikleri/telemetri/komutları modelleyen bir cihaz şablonu olmalıdır. Daha fazla bilgi edinmek için bkz. [IoT Central](concepts-get-connected.md) bağlanma ve [cihaz şablonları nelerdir?](concepts-device-templates.md)

## <a name="next-steps"></a>Sonraki adımlar

Bir cihaz geliştiricisiyseniz, önerilen bazı sonraki adımlar şunlardır:

- Eğitim bölümünde SAS belirteçlerinin nasıl kullanılacağını gösteren örnek kodları gözden geçirin [: Azure IoT Central uygulamanızda bir istemci uygulaması oluşturma ve bağlama](tutorial-connect-device.md)
- [IoT Central uygulama için Node.js cihaz SDK 'sını kullanarak X. 509.952 sertifikalarıyla cihazları bağlamayı](how-to-connect-devices-x509.md) öğrenin
- [Azure CLI kullanarak cihaz bağlantısını izlemeyi](./howto-monitor-devices-azure-cli.md) öğrenin
- [Azure IoT Central uygulamanızda yeni bir IoT cihaz türü tanımlama](./howto-set-up-template.md) hakkında bilgi edinin
- [Azure IoT Edge cihazlar ve Azure IoT Central](./concepts-iot-edge.md) hakkında bilgi edinin