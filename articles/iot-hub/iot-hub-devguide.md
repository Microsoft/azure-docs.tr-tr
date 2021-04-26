---
title: Azure IoT Hub için Geliştirici Kılavuzu | Microsoft Docs
description: Azure IoT Hub Geliştirici Kılavuzu uç noktalar, güvenlik, kimlik kayıt defteri, cihaz yönetimi, doğrudan Yöntemler, cihaz ikleri, dosya karşıya yüklemeleri, işler, IoT Hub sorgu dili ve mesajlaşma için tartışmalar içerir.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.custom: mqtt
ms.openlocfilehash: 1fcf560ba1fef137a28ab1872635cc2182cd07e1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103009207"
---
# <a name="azure-iot-hub-developer-guide"></a>Azure IoT Hub Geliştirici Kılavuzu

Azure IoT Hub, milyonlarca cihaz ile bir çözüm arka ucu arasında güvenilir ve güvenli çift yönlü iletişimin sağlanmasına yardımcı olan, tam olarak yönetilen bir hizmettir.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Azure IoT Hub şunları sağlar:

* Cihaz başına güvenlik kimlik bilgilerini ve erişim denetimini kullanarak güvenli iletişim.

* Birden çok cihazdan buluta ve buluttan cihaza hiper ölçekli iletişim seçenekleri.

* Cihaz başına durum bilgilerinin ve meta verilerin sorgulanabilir depolaması.

* En popüler diller ve platformlar için cihaz kitaplıklarıyla kolay cihaz bağlantısı.

Bu IoT Hub Geliştirici Kılavuzu aşağıdaki makaleleri içerir:

* [Cihazdan buluta iletişim Kılavuzu](iot-hub-devguide-d2c-guidance.md) cihazdan buluta iletiler, Device ikizi 'ın bildirilen özellikleri ve karşıya dosya yükleme arasında seçim yapmanıza yardımcı olur.

* [Buluttan cihaza iletişim Kılavuzu](iot-hub-devguide-c2d-guidance.md) , doğrudan Yöntemler, Device ikizi 'ın istenen özellikleri ve buluttan cihaza iletiler arasında seçim yapmanıza yardımcı olur.

* [IoT Hub Ile cihazdan buluta ve buluttan cihaza mesajlaşma](iot-hub-devguide-messaging.md) , IoT Hub sunan mesajlaşma özelliklerini (cihazdan buluta ve buluttan cihaza) açıklar.

  * [IoT Hub için cihazdan buluta Iletiler gönderin](iot-hub-devguide-messages-d2c.md).

  * [Yerleşik uç noktadan cihazdan buluta Iletileri okuyun](iot-hub-devguide-messages-read-builtin.md).

  * [Cihazdan buluta iletiler için özel uç noktalar ve yönlendirme kuralları kullanın](iot-hub-devguide-messages-read-custom.md).

  * [IoT Hub 'ten cihazdan cihaza Iletiler gönderin](iot-hub-devguide-messages-c2d.md).

  * [IoT Hub Iletileri oluşturun ve okuyun](iot-hub-devguide-messages-construct.md).

* [Bir cihazdan dosya yükleme](iot-hub-devguide-file-upload.md) bir cihazdan nasıl dosya yükleyebileceğinizi açıklar. Makale ayrıca karşıya yükleme işleminin göndereme bildirimleri gibi konular hakkında bilgiler içerir.

* [' Deki cihaz kimliklerini yönetme IoT Hub](iot-hub-devguide-identity-registry.md) her bir IoT Hub 'ının kimlik kayıt defterinin depoladığı bilgileri açıklar. Makalede ayrıca nasıl erişebileceğiniz ve değiştirileceği açıklanır.

* [IoT Hub erişimi denetim](iot-hub-devguide-security.md) , hem cihazlar hem de bulut bileşenleri için IoT Hub işlevselliğine erişim vermek üzere kullanılan güvenlik modelini açıklar. Makale, belirteç ve X. 509.952 sertifikaları kullanma hakkında bilgi ve izin verdiğiniz izinlerin ayrıntılarını içerir.

* [Durum ve yapılandırmaların eşitlenmesi için cihaz](iot-hub-devguide-device-twins.md) Ikizlerini kullanın *cihaz ikizi* kavramını açıklar. Makalede ayrıca cihaz, cihaz ikizi cihazı ile eşitleme gibi cihaz oluşturma işlevleri de açıklanmaktadır. Makale, bir cihaz ikizi depolanan veriler hakkında bilgi içerir.

* [Bir cihazda doğrudan yöntem çağırma](iot-hub-devguide-direct-methods.md) bir doğrudan yöntemin yaşam döngüsünü açıklar. Makalesinde, arka uç uygulamanızdan bir cihazdaki yöntemlerin nasıl çağırılacağını ve cihazınızdan doğrudan yöntemi nasıl işleyeceğinizi açıklanmaktadır.

* [Birden çok cihazda Iş planlama](iot-hub-devguide-jobs.md) işleri, birden çok cihazda nasıl iş zamanlayabilir açıklar. Makalede, bir doğrudan yöntemi yürüten, bir cihazı cihaz ikizi kullanarak güncelleştiren işlerin nasıl yapılacağı açıklanır. Ayrıca, bir işin durumunun nasıl sorgulanılacağını açıklar.

* [Başvuru-iletişim protokolü seçin](iot-hub-devguide-protocols.md) IoT Hub cihaz iletişimi için desteklediği iletişim protokollerini açıklar ve açık olması gereken bağlantı noktalarını listeler.

* [Başvuru IoT Hub uç noktaları](iot-hub-devguide-endpoints.md) , her bir IoT Hub 'ının çalışma zamanı ve yönetim işlemleri için sunduğu çeşitli uç noktaları açıklar. Makalede ayrıca IoT Hub 'ınızda ek uç noktalar oluşturabileceğiniz ve standart olmayan senaryolarda IoT Hub uç noktalarınıza bağlantı sağlamak için bir alan ağ geçidi kullanmanın nasıl yapılacağı açıklanır.

* Cihaz ikişleriniz [, işler ve ileti yönlendirme Için başvuru IoT Hub sorgu dili,](iot-hub-devguide-query-language.md) cihazınızdan cihaz WINS ve işleriniz hakkında bilgi almanızı sağlayan IoT Hub sorgu dilini açıklar.

* [Başvuru-kotalar ve azaltma](iot-hub-devguide-quotas-throttling.md) , IoT Hub hizmetinde ayarlanan kotaları ve bir kotayı aşarsanız oluşan azaltmayı özetler.

* [Başvuru-fiyatlandırma](iot-hub-devguide-pricing.md) , farklı SKU 'lar ve fiyatlandırIoT hub mayla ilgili genel bilgileri ve çeşitli IoT Hub işlevleri IoT Hub tarafından ileti olarak ücretlendirerek ilgili ayrıntıları sağlar.

* [Başvuru-cihaz ve hizmet SDK 'ları](iot-hub-devguide-sdks.md) , IoT Hub 'ınız ile etkileşime geçen cihaz ve hizmet uygulamaları geliştirmeye yönelik Azure IoT SDK 'larını listeler. Bu makalede, çevrimiçi API belgelerinin bağlantıları bulunur.

* [Reference-IoT Hub MQTT desteği](iot-hub-mqtt-support.md) , IoT Hub MQTT protokolünü nasıl desteklediğine ilişkin ayrıntılı bilgiler sağlar. Makale, Azure IoT SDK 'lerinde yerleşik olarak bulunan MQTT Protokolü desteğini açıklar ve MQTT protokolünü doğrudan kullanma hakkında bilgi sağlar.
