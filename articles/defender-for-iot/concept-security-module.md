---
title: Defender-IoT-Micro-Agent ve cihaz TWINS
description: Defender-IoT-mikro-Agent TWINS kavramı ve IoT için Defender 'da nasıl kullanıldıkları hakkında bilgi edinin.
ms.topic: conceptual
ms.date: 07/24/2019
ms.openlocfilehash: 1ed6faf03d168ed7a2a2f07733cb7e238d234915
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104779180"
---
# <a name="defender-iot-micro-agent"></a>Defender-IoT-Micro-Agent

Bu makalede, IoT için Defender 'ın cihaz TWINS ve modülleri nasıl kullandığı açıklanmaktadır.

## <a name="device-twins"></a>Cihaz ikikesi

Azure 'da oluşturulan IoT çözümleri için, cihaz ikis, hem cihaz yönetimi hem de süreç otomasyonu 'nda anahtar rol oynar.

IoT için Defender, mevcut IoT cihaz yönetimi Platformunuzla tam tümleştirme sunarak cihaz güvenlik durumunuzu yönetmenize ve mevcut cihaz denetimi yeteneklerini kullanmanıza olanak sağlar. Tümleştirme, IoT Hub ikizi mekanizması kullanılarak elde edilir.

Azure IoT Hub 'de [cihaz](../iot-hub/iot-hub-devguide-device-twins.md) ikseponları kavramı hakkında daha fazla bilgi edinin.

## <a name="defender-iot-micro-agent-twins"></a>Defender-IoT-Micro-Agent TWINS

IoT için Defender, hizmette bulunan her cihaz için bir Defender-IoT-mikro-Agent ikizi tutar.
Defender-IoT-Micro-Agent ikizi, çözümünüzde bulunan her bir cihaz için cihaz güvenliğiyle ilgili tüm bilgileri tutar.
Cihaz güvenliği özellikleri, daha güvenli iletişim için ve daha az kaynak gerektiren güncelleştirmeleri ve bakımı etkinleştirmek üzere adanmış bir Defender-IoT-mikro-Agent ikizi içinde tutulur.

İkizi oluşturmayı, özelleştirmeyi ve yapılandırmayı öğrenmek için bkz. [Defender-IoT-Micro-Agent Ikizi oluşturma](quickstart-create-security-twin.md) ve [güvenlik aracılarını yapılandırma](how-to-agent-configuration.md) . IoT Hub modül TWINS kavramı hakkında daha fazla bilgi edinmek için bkz. [Modül TWINS 'ı anlama](../iot-hub/iot-hub-devguide-module-twins.md) .

## <a name="see-also"></a>Ayrıca bkz.

- [IoT için Defender genel bakış](overview.md)
- [Güvenlik aracısı dağıtma](how-to-deploy-agent.md)
- [Güvenlik Aracısı kimlik doğrulama yöntemleri](concept-security-agent-authentication-methods.md)