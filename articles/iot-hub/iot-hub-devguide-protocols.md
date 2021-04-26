---
title: Azure IoT Hub iletişim protokolleri ve bağlantı noktaları | Microsoft Docs
description: Geliştirici Kılavuzu-cihazdan buluta ve buluttan cihaza iletişimler için desteklenen iletişim protokollerini ve açık olması gereken bağlantı noktası numaralarını açıklar.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: e2578b47d27ef062d83ba8621a49e9a8f439897c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98919034"
---
# <a name="reference---choose-a-communication-protocol"></a>Başvuru-iletişim protokolü seçin

IoT Hub cihazların, cihaz tarafındaki iletişimler için şu protokolleri kullanmasına izin verir:

* [MQTT](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/mqtt-v3.1.1.pdf)
* WebSockets üzerinden MQTT
* [AMQP](https://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf)
* WebSockets üzerinden AMQP
* HTTPS

Bu protokollerin belirli IoT Hub özelliklerini nasıl desteklediği hakkında bilgi için, bkz. [cihazdan buluta iletişim Kılavuzu](iot-hub-devguide-d2c-guidance.md) ve [buluttan cihaza iletişim Kılavuzu](iot-hub-devguide-c2d-guidance.md).

Aşağıdaki tabloda, protokol seçiminiz için üst düzey öneriler verilmiştir:

| Protokol | Bu protokolü seçmeniz gerekir |
| --- | --- |
| MQTT <br> WebSocket üzerinden MQTT |Aynı TLS bağlantısı üzerinden birden çok cihaza (her biri kendi cihaz başına kimlik bilgileri ile) bağlanmasını gerektirmeyen tüm cihazlarda kullanın. |
| AMQP <br> WebSocket üzerinden AMQP |Cihazlarda bağlantı çoğullama özelliğinden yararlanmak için alan ve bulut ağ geçitlerinde kullanın. |
| HTTPS |Diğer protokolleri desteklemeyen cihazlar için kullanın. |

Cihaz tarafı iletişimleri için protokollerinizi seçerken aşağıdaki noktaları göz önünde bulundurun:

* **Buluttan cihaza yönelik model**. HTTPS, sunucu gönderimi uygulamak için etkili bir yola sahip değildir. Bu nedenle, HTTPS kullanırken, cihazlar buluttan cihaza iletiler için IoT Hub yoklar. Bu yaklaşım hem cihaz hem de IoT Hub için verimsiz bir değer. Geçerli HTTPS yönergeleri altında her bir cihaz, her 25 dakikada bir veya daha fazla ileti yoklamalıdır. Daha fazla HTTPS vermek istekleri IoT Hub azaltarak sonuçları alır. MQTT ve AMQP, buluttan cihaza iletiler alırken sunucu gönderimi destekler. IoT Hub iletileri cihaza anında göndermeyi etkinleştirir. Teslim gecikmesi bir sorun oluşturacaksa, MQTT veya AMQP kullanılacak en iyi protokollerdir. Nadiren bağlanan cihazlarda, HTTPS de kullanılabilir.

* **Alan ağ geçitleri**. MQTT ve HTTPS, TLS bağlantısı başına yalnızca tek bir cihaz kimliğini (cihaz KIMLIĞI ve kimlik bilgileri) destekler. Bu nedenle, bu protokoller IoT Hub için tek bir veya yukarı akış bağlantısı havuzu genelinde birden çok cihaz kimliği kullanarak çoğullama iletileri gerektiren [alan ağ geçidi senaryolarında](iot-hub-devguide-endpoints.md#field-gateways) desteklenmez. Bu tür ağ geçitleri, her bağlantı için, yukarı akış trafiği için AMQP gibi birden çok cihaz kimliği destekleyen bir protokol kullanabilir.

* **Düşük kaynak cihazları**. MQTT ve HTTPS kitaplıklarının AMQP kitaplıklarından daha küçük bir parmak izi vardır. Bu nedenle, cihaz sınırlı kaynaklara sahipse (örneğin, 1 MB 'den az RAM), bu protokoller kullanılabilir tek protokol uygulamasıdır.

* **Ağ geçişi**. Standart AMQP Protokolü 5671 numaralı bağlantı noktasını kullanır ve MQTT, bağlantı noktası 8883 ' de dinler. Bu bağlantı noktalarının kullanımı, HTTPS olmayan protokollere kapatılan ağlarda sorun oluşmasına neden olabilir. Bu senaryoda WebSockets üzerinden MQTT, AMQP WebSockets veya HTTPS kullanın.

* **Yük boyutu**. MQTT ve AMQP ikili protokollerdir ve bu da HTTPS 'den daha küçük bir yük yükleri oluşmasına neden olur.

> [!WARNING]
> HTTPS kullanılırken her cihaz, her 25 dakikada bir bulut-cihaz iletilerini birden çok kez yoklamalıdır. Geliştirme aşamasında, her cihaz istenirse daha sık yoklama yapabilir.

[!INCLUDE [iot-hub-include-x509-ca-signed-support-note](../../includes/iot-hub-include-x509-ca-signed-support-note.md)]

## <a name="port-numbers"></a>Bağlantı noktası numaraları

Cihazlar, çeşitli protokolleri kullanarak Azure 'da IoT Hub ile iletişim kurabilir. Genellikle, protokol seçimi çözümün belirli gereksinimlerine göre çalıştırılır. Aşağıdaki tabloda, bir cihazın belirli bir protokolü kullanabilmesi için açık olması gereken giden bağlantı noktaları listelenmektedir:

| Protokol | Bağlantı noktası |
| --- | --- |
| MQTT |8883 |
| WebSockets üzerinden MQTT |443 |
| AMQP |5671 |
| WebSockets üzerinden AMQP |443 |
| HTTPS |443 |

Bir Azure bölgesinde IoT Hub 'ı oluşturduktan sonra, IoT Hub 'ı söz konusu IoT Hub 'ının ömrü boyunca aynı IP adresini tutar. Ancak, Microsoft, hizmet kalitesi sağlamak için IoT Hub 'ını farklı bir ölçek birimine taşıdıysanız, yeni bir IP adresi atanır.

## <a name="next-steps"></a>Sonraki adımlar

IoT Hub MQTT protokolünü nasıl uygulayan hakkında daha fazla bilgi edinmek için bkz. [MQTT protokolünü kullanarak IoT Hub 'ınız Ile Iletişim kurma](iot-hub-mqtt-support.md).
