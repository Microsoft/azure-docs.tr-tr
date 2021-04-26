---
title: Azure IoT Hub hata giderme hatası 401003 Iothubyetkilendirilmemiş
description: 401003 Iothubyetkilendirilmemiş hatasını nasıl düzelteceğinizi öğrenin
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 11/06/2020
ms.author: jlian
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: c6ad0e2dd5ce5f223f4e6033ace09c31e58c85a3
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061358"
---
# <a name="401003-iothubunauthorized"></a>401003 IoTHubUnauthorized

Bu makalede, **401003 Iothubyetkilendirilmemiş** hatalara yönelik nedenler ve çözümler açıklanmaktadır.

## <a name="symptoms"></a>Belirtiler

### <a name="symptom-1"></a>Belirti 1

Günlüklerde, **401003 Iothubyetkilendirilmemiş**, sonrasında **404104 Deviceconnectioncloseduzaktan** ve sonra kısa bir süre sonra başarılı bir şekilde bağlantı kurarak cihazların bir modelini görürsünüz.

### <a name="symptom-2"></a>Belirti 2

IoT Hub istekleri aşağıdaki hata iletilerinden biriyle başarısız olur:

* Yetkilendirme üstbilgisi eksik
* Iothub ' \* ' belirtilen ' ' cihazını içermiyor \*
* ' ' Yetkilendirme kuralı \* ' ' için erişime izin vermiyor \*
* Bu cihaz için kimlik doğrulaması başarısız oldu, belirteci veya sertifikayı yenileyip yeniden bağlayın
* Parmak izi yapılandırma ile eşleşmiyor: Parmak Izi: SHA1Hash = \* , SHA2Hash = \* ; Yapılandırma: Primaryparmak Izi = \* , secondaryparmak izi =\*

## <a name="cause"></a>Nedeni

### <a name="cause-1"></a>1\. Neden

MQTT için bazı SDK 'lar, bir kilit süresinin ne zaman yenileneceğini bilmek için SAS belirtecinin süresi dolarsa bağlantıyı kesmek için IoT Hub kullanır. Dolayısıyla:

1. SAS belirtecinin süresi doluyor
1. IoT Hub sona erme süresini fark eder ve cihazın bağlantısını **401003 Iothubyetkilendirilmemiş** ile keser
1. Cihaz, **404104 Deviceconnectioncloseduzaktan** bağlantı kesmeyi tamamlar
1. IoT SDK yeni bir SAS belirteci oluşturur
1. Cihaz başarıyla IoT Hub yeniden bağlanır

### <a name="cause-2"></a>2\. Neden

IoT Hub auth üstbilgisinin, kuralın veya anahtarın kimliğini doğrulayamadı. Bu, belirtilere alıntı yapılan nedenlerden kaynaklanabilir.

## <a name="solution"></a>Çözüm

### <a name="solution-1"></a>Çözüm 1

Cihaz bağlantı dizesini kullanarak bağlantı için IoT SDK kullanıyorsanız hiçbir işlem yapmanız gerekmez. IoT SDK, SAS belirteci süre sonuna yeniden bağlanmak için yeni belirteci yeniden oluşturur.

Varsayılan belirteç kullanım ömrü, SDK 'larda 60 dakikadır; Ancak bazı SDK 'lar için, belirtecin ömrü ve belirteç yenileme eşiği yapılandırılabilir. Ayrıca, bir cihazın bağlantısı kesildiğinde ve belirteç yenilemesinde yeniden bağlandığında oluşturulan hatalar her SDK için farklılık gösterir. Daha fazla bilgi edinmek ve cihazınızın günlüklerde hangi SDK 'nın kullandığını belirleme hakkında bilgi için bkz. [Azure IoT SDK 'ları Ile MQTT cihaz bağlantısı kesme davranışı](iot-hub-troubleshoot-connectivity.md#mqtt-device-disconnect-behavior-with-azure-iot-sdks).

Cihaz geliştiricileri için, hata hacmi sorun oluşturacaksa, SAS belirtecini sona ermeden önce yenilediğinde C SDK 'sına geçin. AMQP için SAS belirteci, bağlantısı kesilmeden yenilenebilirler.

### <a name="solution-2"></a>Çözüm 2

Genel olarak, sunulan hata iletisinde hatanın nasıl düzeltileceğini açıklamalıdır. Bazı nedenlerle hata iletisi ayrıntısına erişiminiz yoksa şunları yaptığınızdan emin olun:

- Kullandığınız SAS veya diğer güvenlik belirtecinin geçerliliği dolmamıştır.
- X. 509.952 sertifikası kimlik doğrulaması için, cihaz sertifikası veya cihazla ilişkili CA sertifikası zaman aşımına uğradı. IoT Hub ile X. 509.952 CA sertifikalarını nasıl kaydedeceğinizi öğrenmek için bkz. [Azure IoT Hub 'ınızda x. 509.440 güvenliğini ayarlama](iot-hub-security-x509-get-started.md).
- X. 509.440 sertifika parmak izi kimlik doğrulaması için, cihaz sertifikasının parmak izi IoT Hub kaydedilir.
- Yetkilendirme kimlik bilgileri, kullandığınız protokol için doğru şekilde biçimlendirilir. Daha fazla bilgi için bkz. [IoT Hub erişimi denetleme](iot-hub-devguide-security.md).
- Kullanılan yetkilendirme kuralı istenen işlem için izne sahip.

## <a name="next-steps"></a>Sonraki adımlar

- IoT Hub kimlik doğrulamasını kolaylaştırmak için [Azure IoT SDK](iot-hub-devguide-sdks.md)'larını kullanmanızı öneririz.
- IoT Hub ile kimlik doğrulama hakkında ayrıntılı bilgi için bkz. [IoT Hub denetim erişimi](iot-hub-devguide-security.md).
