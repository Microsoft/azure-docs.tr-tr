---
title: Azure IoT SDK 'Sı olmadan geliştirme | Microsoft Docs
description: Geliştirici Kılavuzu-Azure IoT SDK kullanmadan cihaz uygulamaları ve arka uç uygulamaları oluşturmak için kullanabileceğiniz konular hakkında bilgi ve bağlantılar.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/12/2020
ms.custom:
- mqtt
- amqp
- 'Role: IoT Device'
- 'Role: Cloud Development'
ms.openlocfilehash: ae8b01522a248b8b1dbdd255a9fcd55f16cf2369
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96461706"
---
# <a name="develop-without-using-an-azure-iot-hub-sdk"></a>Azure IoT Hub SDK kullanmadan geliştirme

Bu konu, Azure IoT SDK 'Ları kullanmadan cihaz veya arka uç uygulamaları geliştirmek isteyen geliştiriciler için yardımcı bilgiler ve bağlantılar sağlar.

Microsoft, bir Azure IoT SDK kullanma konusunda kesinlikle önerilir. Azure IoT cihaz ve hizmet SDK 'Ları birçok popüler platformda yayımlanır. SDK 'lar, cihaz bağlantısı ve yeniden bağlantı dahil olmak üzere, temel iletişim protokolünün karmaşıklığının çoğunu işleyen kolay bir katman sağlar ve ilkeyi yeniden deneyin. SDK 'lar, IoT Hub ve güvenlik güncelleştirmelerinin açığa çıkarılan en son özellikleri sağlamak için düzenli olarak güncelleştirilir. SDK 'ların kullanılması, kod bakımı için ayrılan geliştirme süresini ve süresini azaltmanıza yardımcı olabilir. Azure IoT SDK 'Ları hakkında daha fazla bilgi edinmek için bkz. [Azure IoT cihaz ve hizmet SDK 'ları](iot-hub-devguide-sdks.md). Azure IoT SDK kullanmanın avantajları hakkında daha fazla bilgi için bkz. [Azure IoT SDK 'larını kullanma avantajları ve blog gönderisi oluşturmamaya özen gösterin](https://azure.microsoft.com/en-us/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/) .

IoT Hub AMQP, AMQP Over WebSockets, HTTPS, MQTT ve WebSockets üzerinde MQTT 'yi cihazlara iletişim için desteklese de, cihazınız destekliyorsa MQTT kullanmanızı öneririz.

## <a name="development-prerequisites"></a>Geliştirme önkoşulları

Geliştirmeye başlamadan önce, IoT Hub ve cihazınızın veya arka uç uygulamanızın uygulanmasını istediğiniz özelliklerin kapsamlı bir bilgisine sahip olmanız gerekir. Tanıdık yapmanız gereken konuların çok kısaltılmış bir listesi aşağıda verilmiştir:

* IoT Hub tarafından sunulan uç noktaları ve her bir uç noktada desteklenen protokolleri anladığınızdan emin olun. Daha fazla bilgi için bkz. [IoT Hub uç noktaları](iot-hub-devguide-endpoints.md).

* Cihaz uygulamalarına bir protokol seçimi dahil olduğunda MQTT kullanmanız önemle önerilir. Bir protokol seçmeden önce, her biri tarafından uygulanan sınırlamaları anladığınızdan emin olun. Daha fazla bilgi için bkz. [iletişim protokolü seçme](iot-hub-devguide-protocols.md).

* IoT Hub ile kimlik doğrulamasını anlamak için bkz. [IoT Hub denetim erişimi](iot-hub-devguide-security.md).

[!INCLUDE [iot-hub-include-x509-ca-signed-support-note](../../includes/iot-hub-include-x509-ca-signed-support-note.md)]

## <a name="help-on-different-protocols"></a>Farklı protokollerle ilgili yardım

Azure IoT SDK olmadan aşağıdaki protokolleri kullanma hakkında yardım için:

* **AMQP** üzerinde cihaz veya arka uç uygulamaları, bkz. [AMQP desteği](iot-hub-amqp-support.md).

* **MQTT** üzerinde cihaz uygulamaları, bkz. [MQTT desteği](iot-hub-mqtt-support.md). Bu konunun çoğu, MQTT protokolünü doğrudan kullanarak ele alır. [IOT MQTT örnek deposunu](https://github.com/Azure-Samples/IoTMQTTSample)kullanma hakkındaki bilgileri de içerir. Bu depo, IoT Hub ileti göndermek için tutulma mosquıto kitaplığı kullanan C örnekleri içerir.

* **Https** üzerindeki cihaz veya arka uç uygulamaları [Azure IoT Hub REST API 'lerine](/rest/api/iothub/)bakın. [Geliştirme önkoşulları](#development-prerequisites)bölümünde belirtildiği gıbı, https ile X. 509.440 sertifika YETKILISI (CA) kimlik doğrulamasını kullanamıyoruz farkında olun.

Cihazlar için cihazınız destekliyorsa MQTT kullanmanız önemle önerilir.

## <a name="next-steps"></a>Sonraki adımlar

* [MQTT desteği](iot-hub-mqtt-support.md)