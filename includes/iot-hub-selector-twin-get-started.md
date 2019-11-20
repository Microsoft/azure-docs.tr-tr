---
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
ms.topic: include
ms.date: 08/20/2019
ms.openlocfilehash: c412f7f3e20e4d04083e457bfb245b850b65e126
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050445"
---
> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-twin-getstarted.md)
> * [.NET](../articles/iot-hub/iot-hub-csharp-csharp-twin-getstarted.md)
> * [Java](../articles/iot-hub/iot-hub-java-java-twin-getstarted.md)
> * [Python](../articles/iot-hub/iot-hub-python-twin-getstarted.md)

Cihaz TWINS, meta veriler, konfigürasyonlar ve koşullar dahil olmak üzere cihaz durum bilgilerini depolayan JSON belgelerdir. IoT Hub, kendisine bağlanan her cihaz için bir cihaz ikizi devam ettirir.

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

Cihaz ikizlerini şu şekilde kullan:

* Çözüm arka ucundan cihaz meta verilerini depolayın.

* Kullanılabilir yetenekler ve koşullar gibi geçerli durum bilgilerini (örneğin, kullanılan bağlantı yöntemi) cihaz uygulamanızdan bildirin.

* Üretici yazılımı ve yapılandırma güncelleştirmeleri gibi uzun süre çalışan iş akışlarının durumunu bir cihaz uygulaması ile arka uç uygulaması arasında eşitler.

* Cihazınızın meta verilerini, yapılandırmasını veya durumunu sorgulayın.

Cihaz TWINS, eşitleme için ve cihaz yapılandırmalarının ve koşullarının sorgulanması için tasarlanmıştır. Cihaz TWINS 'in ne zaman kullanılacağı hakkında daha fazla bilgi için bkz. cihaz ikizlerini [anlama](../articles/iot-hub/iot-hub-devguide-device-twins.md).

Cihaz ikizleri bir IoT Hub 'ında depolanır ve aşağıdaki öğeleri içerir:

* **Etiketler**. Cihaz meta verileri yalnızca çözüm arka ucu tarafından erişilebilir.

* **İstenen özellikler**. Çözüm arka ucu tarafından değiştirilebilen JSON nesneleri ve cihaz uygulaması tarafından Observable.

* **Bildirilen özellikler**. Cihaz uygulaması tarafından değiştirilebilen ve çözüm arka ucu tarafından okunabilen JSON nesneleri.

Etiketler ve Özellikler diziler içeremez, ancak nesneler iç içe olabilir.

Aşağıdaki çizimde Device ikizi organizasyonu gösterilmektedir:

![İşlevselliği gösteren cihaz ikizi resmi](./media/iot-hub-selector-twin-get-started/twin.png)

Ayrıca, çözüm arka ucu, yukarıdaki tüm verileri temel alan cihaz TWINS 'i sorgulayabilir.
Cihaz TWINS hakkında daha fazla bilgi için bkz. cihaz ikizlerini [anlama](../articles/iot-hub/iot-hub-devguide-device-twins.md). Sorgulama hakkında daha fazla bilgi için [IoT Hub sorgu dili](../articles/iot-hub/iot-hub-devguide-query-language.md)' ne bakın.


Bu öğretici şunların nasıl yapıldığını gösterir:

* Bir cihaz ikizi 'e Etiketler ekleyen bir arka uç uygulaması oluşturun ve bağlantı kanalını cihazda bildirilen bir özellik olarak raporlayan bir sanal cihaz uygulamasına ikizi.

* Daha önce oluşturulan Etiketler ve özelliklerde filtreler kullanarak arka uç uygulamanızdaki cihazları sorgulayın.
