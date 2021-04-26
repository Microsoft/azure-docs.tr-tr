---
title: Hizmet Geliştirici Kılavuzu-IoT Tak ve Kullan | Microsoft Docs
description: Hizmet geliştiricileri için IoT Tak ve Kullan açıklaması
author: dominicbetts
ms.author: dobett
ms.date: 10/01/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
zone_pivot_groups: programming-languages-set-ten
ms.openlocfilehash: a5889be88dfd0870a2eee868c97787ff354cff68
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104582740"
---
# <a name="iot-plug-and-play-service-developer-guide"></a>IoT Tak ve Kullan hizmeti Geliştirici Kılavuzu

IoT Tak ve Kullan, yeteneklerini Azure IoT uygulamalarına duyuran akıllı cihazlar oluşturmanızı sağlar. IoT Tak ve Kullan cihazları, bir müşteri bunları IoT Tak ve Kullan özellikli uygulamalara bağladığında el ile yapılandırma gerektirmez.

IoT Tak ve Kullan, IoT Hub 'ınız ile model KIMLIĞINI duyurduğu cihazları kullanmanıza olanak sağlar. Örneğin, bir cihazın özelliklerine ve komutlarına doğrudan erişebilirsiniz.

IoT Hub 'ınıza bağlı olan IoT Tak ve Kullan cihazı kullanmak için IoT hizmeti SDK 'Lardan biri:

## <a name="service-sdks"></a>Hizmet SDK'ları

Cihazlarla ve modüllerle etkileşim kurmak için çözümünüzdeki Azure IoT hizmeti SDK 'larını kullanın. Örneğin, ikizi özelliklerini okumak ve güncelleştirmek ve komutları çağırmak için hizmet SDK 'larını kullanabilirsiniz. Desteklenen diller C#, Java, Node.js ve Python içerir.

Hizmet SDK 'Ları, masaüstü veya Web uygulaması gibi bir çözümden cihaz bilgilerine erişmenizi sağlar. Hizmet SDK 'Ları, model KIMLIĞINI almak için kullanabileceğiniz iki ad alanı ve nesne modeli içerir:

- IoT Hub hizmeti istemcisi. Bu hizmet, model KIMLIĞINI bir Device ikizi özelliği olarak kullanıma sunar.

- Dijital TWINS istemcisi. Yeni dijital TWINS API 'SI, bileşenler, Özellikler ve komutlar gibi [dijital TWINS tanım dili (DTDL)](concepts-digital-twin.md) model yapıları üzerinde çalışır. Dijital Ikizi API 'Leri, çözüm oluşturucuların IoT Tak ve Kullan çözümleri oluşturmalarına daha kolay hale getirir.

:::zone pivot="programming-language-csharp"

[!INCLUDE [iot-pnp-service-devguide-csharp](../../includes/iot-pnp-service-devguide-csharp.md)]

:::zone-end

:::zone pivot="programming-language-java"

[!INCLUDE [iot-pnp-service-devguide-java](../../includes/iot-pnp-service-devguide-java.md)]

:::zone-end

:::zone pivot="programming-language-javascript"

[!INCLUDE [iot-pnp-service-devguide-node](../../includes/iot-pnp-service-devguide-node.md)]

:::zone-end

:::zone pivot="programming-language-python"

[!INCLUDE [iot-pnp-service-devguide-python](../../includes/iot-pnp-service-devguide-python.md)]

:::zone-end

## <a name="next-steps"></a>Sonraki adımlar

Artık cihaz modelleme hakkında bilgi edindiğinize göre, bazı ek kaynaklar aşağıda verilmiştir:

- [Dijital İkiz Tanımlama Dili (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)
- [C cihaz SDK’sı](/azure/iot-hub/iot-c-sdk-ref/)
- [IoT REST API](/rest/api/iothub/device)
- [IoT Tak ve Kullan modelleme Kılavuzu](concepts-modeling-guide.md)
- [DTDL yazma araçlarını yükle ve kullan](howto-use-dtdl-authoring-tools.md)