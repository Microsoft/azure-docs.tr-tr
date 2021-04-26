---
title: Cihaz Geliştirici Kılavuzu (C)-IoT Tak ve Kullan | Microsoft Docs
description: C cihaz geliştiricileri için IoT Tak ve Kullan açıklaması
author: rido-min
ms.author: rmpablos
ms.date: 11/19/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
zone_pivot_groups: programming-languages-set-twenty-six
ms.openlocfilehash: 0cca47269e632e1fcba1f8f9eb1c835f27e63059
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104582836"
---
# <a name="iot-plug-and-play-device-developer-guide"></a>IoT Tak ve Kullan cihaz Geliştirici Kılavuzu

IoT Tak ve Kullan, yeteneklerini Azure IoT uygulamalarına duyuran akıllı cihazlar oluşturmanızı sağlar. IoT Tak ve Kullan cihazları, bir müşteri bunları IoT Tak ve Kullan özellikli uygulamalara bağladığında el ile yapılandırma gerektirmez.

Akıllı bir cihaz doğrudan uygulanabilir, [modüller](../iot-hub/iot-hub-devguide-module-twins.md)kullanabilir veya [IoT Edge modüller](../iot-edge/about-iot-edge.md)kullanabilir.

Bu kılavuzda, [ıot Tak ve kullan kurallarını](../iot-pnp/concepts-convention.md)izleyen bir cihaz, modül veya IoT Edge modülü oluşturmak için gereken temel adımlar açıklanmaktadır.

IoT Tak ve Kullan cihazı, modülü veya IoT Edge modülü oluşturmak için aşağıdaki adımları izleyin:

1. Cihazınızın Azure IoT Hub bağlanmak için MQTT veya MQTT Over WebSockets protokolünü kullandığından emin olun.
1. Cihazınızı anlatmak için [dijital bir TWINS tanım dili (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl) modeli oluşturun. Daha fazla bilgi edinmek için bkz. [ıot Tak ve kullan modellerdeki bileşenleri anlama](concepts-modeling-guide.md).
1. Cihaz bağlantısının bir parçası olarak duyurmak için cihazınızı veya modülünüzü güncelleştirin `model-id` .
1. [Iot Tak ve kullan kurallarını](concepts-convention.md) kullanarak telemetri, özellik ve komutları uygulama

Cihazınız veya modül uygulamanız hazırsanız, cihazın IoT Tak ve Kullan kurallarını izlediğini doğrulamak için [Azure IoT Gezginini](howto-use-iot-explorer.md) kullanın.

:::zone pivot="programming-language-ansi-c"

[!INCLUDE [iot-pnp-device-devguide-c](../../includes/iot-pnp-device-devguide-c.md)]

:::zone-end

:::zone pivot="programming-language-csharp"

[!INCLUDE [iot-pnp-device-devguide-csharp](../../includes/iot-pnp-device-devguide-csharp.md)]

:::zone-end

:::zone pivot="programming-language-java"

[!INCLUDE [iot-pnp-device-devguide-java](../../includes/iot-pnp-device-devguide-java.md)]

:::zone-end

:::zone pivot="programming-language-javascript"

[!INCLUDE [iot-pnp-device-devguide-node](../../includes/iot-pnp-device-devguide-node.md)]

:::zone-end

:::zone pivot="programming-language-python"

[!INCLUDE [iot-pnp-device-devguide-python](../../includes/iot-pnp-device-devguide-python.md)]

:::zone-end

## <a name="next-steps"></a>Sonraki adımlar

IoT Tak ve Kullan cihaz geliştirmeyi öğrendiğinize göre artık bazı ek kaynaklar verilmiştir:

- [Dijital İkiz Tanımlama Dili (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)
- [C cihaz SDK’sı](/azure/iot-hub/iot-c-sdk-ref/)
- [IoT REST API](/rest/api/iothub/device)
- [IoT Tak ve Kullan modellerdeki bileşenleri anlama](concepts-modeling-guide.md)
- [DTDL yazma araçlarını yükle ve kullan](howto-use-dtdl-authoring-tools.md)
- [IoT Tak ve Kullan hizmeti Geliştirici Kılavuzu](concepts-developer-guide-service.md)