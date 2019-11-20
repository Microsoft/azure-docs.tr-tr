---
title: Cihaz bağlantısı ve telemetri girişi-Azure dijital TWINS | Microsoft Docs
description: Azure dijital TWINS 'te bir cihazı bağlamayı ve eklemeyi öğrenin.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/07/2019
ms.openlocfilehash: 529baf6a3eedf1d7490e8138642e90928a209876
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74010120"
---
# <a name="device-connectivity-and-telemetry-ingress"></a>Cihaz bağlantısı ve telemetri sorunları

Cihazlar ve sensörler tarafından gönderilen telemetri verileri, herhangi bir IoT çözümünün omurgasını oluşturur. Bu farklı kaynakları temsil etme ve bunları bir konum bağlamında yönetme IoT uygulama geliştirmede kaygıları beraberinde getirir. Azure dijital TWINS, uzamsal zeka Graf ile cihaz ve sensörlerden yararlanarak IoT çözümleri geliştirme sürecini basitleştirir.

Başlamak için, uzamsal grafiğin kökünde bir Azure IoT Hub kaynağı oluşturun. IoT Hub kaynağı, kök alanının altındaki tüm cihazların ileti göndermesini sağlar. IoT Hub oluşturulduktan sonra, cihazları Dijital TWINS örneği içinde sensörler ile kaydedin. Cihazlar, [Azure IoT cihaz SDK 'sı](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks)aracılığıyla dijital bir TWINS hizmetine veri gönderebilir.

Cihazları ekleme hakkında adım adım yönergeler için bkz. [dijital TWINS dağıtma ve yapılandırma öğreticisi](tutorial-facilities-setup.md). Bir bakışta, adımlar şunlardır:

- [Azure Portal](https://portal.azure.com)bir dijital TWINS örneği dağıtın.
- Grafiğinizde boşluk oluşturun.
- IoT Hub bir kaynak oluşturun ve bu kaynağı grafiğinizde bir alana atayın.
- Grafiğinizde cihazlar ve algılayıcılar oluşturun ve bunları önceki adımlarda oluşturulan alanlara atayın.
- Koşullara göre telemetri iletilerini filtrelemek için bir eşleştirici oluşturun.
- [Kullanıcı tanımlı bir işlev](concepts-user-defined-functions.md)oluşturun ve telemetri iletilerinizin özel işlenmesi için grafikteki bir alana atayın.
- Kullanıcı tanımlı işlevin grafik verilerine erişmesine izin vermek için bir rol atayın.
- Dijital TWINS yönetim API 'Lerinden IoT Hub cihaz bağlantı dizesini alın.
- Cihazda cihaz bağlantı dizesini Azure IoT cihaz SDK 'Sı ile yapılandırın.

Aşağıdaki bölümlerde, dijital TWINS yönetim API 'sinden IoT Hub cihaz bağlantı dizesinin nasıl alınacağını öğreneceksiniz. Ayrıca, algılayıcı tabanlı telemetri göndermek için IoT Hub telemetri ileti biçimini kullanmayı öğreneceksiniz. Dijital TWINS, aldığı her telemetri parçasını, uzamsal grafik içindeki bir algılayıcı ile ilişkilendirmek üzere gerektirir. Bu gereksinim, verilerin işlenmiş ve uygun uzamsal bağlam içinde yönlendirildiğinden emin olmanızı sağlar.

## <a name="get-the-iot-hub-device-connection-string-from-the-management-api"></a>Yönetim API 'sinden IoT Hub cihaz bağlantı dizesi al

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

IoT Hub cihaz bağlantı dizesini almak için `includes=ConnectionString` parametresiyle cihaz API 'SI üzerinde bir GET çağrısı yapın. Verilen cihazı bulmak için cihaz GUID 'INE veya donanım KIMLIĞINE göre filtreleyin.

```plaintext
YOUR_MANAGEMENT_API_URL/devices/YOUR_DEVICE_GUID?includes=ConnectionString
```

| Parametre | Şununla değiştir |
| --- | --- |
| *YOUR_DEVICE_GUID* | Cihaz KIMLIĞI |

```plaintext
YOUR_MANAGEMENT_API_URL/devices?HardwareIds=YOUR_DEVICE_HARDWARE_ID&includes=ConnectionString
```

| Parametre değeri | Şununla değiştir |
| --- | --- |
| *YOUR_DEVICE_HARDWARE_ID* | Cihaz donanım KIMLIĞI |

Yanıt yükünde, cihazın **ConnectionString** özelliğini kopyalayın. Verileri dijital TWINS 'e göndermek için Azure IoT cihaz SDK 'sını çağırdığınızda kullanırsınız.

## <a name="device-to-cloud-message"></a>Cihazdan buluta iletisi

Cihazınızın ileti biçimi ve yükünü, çözümünüzün ihtiyaçlarına uyacak şekilde özelleştirebilirsiniz. [Azure IoT cihaz Istemci ileti sınıfı (Byte [] ByteArray)](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.message.-ctor?view=azure-dotnet#Microsoft_Azure_Devices_Client_Message__ctor_System_Byte___)tarafından desteklenen bir bayt dizisine veya akışa seri hale getirilebilen bir veri sözleşmesi kullanın. İleti, ilgili Kullanıcı tanımlı işlevdeki veri sözleşmesinin kodunu çözmeniz koşuluyla, seçtiğiniz özel bir ikili biçimi olabilir. Cihazdan buluta ileti için yalnızca bir gereksinim vardır. İletinizin işleme motoruna uygun şekilde yönlendirildiğinden emin olmak için bir özellikler kümesi saklayın.

### <a name="telemetry-properties"></a>Telemetri özellikleri

 Bir **iletinin** yük içeriği, 256 KB 'a kadar olan rastgele veri olabilir. [`Message.Properties`](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.message.properties?view=azure-dotnet) türünün özellikleri için birkaç gereksinim bekleniyor. Tablo, sistem tarafından desteklenen gerekli ve isteğe bağlı özellikleri gösterir.

| Özellik adı | Değer | Gerekli | Açıklama |
|---|---|---|---|
| **DigitalTwins-telemetri** | 1.0 | Yes | Sisteme bir ileti tanımlayan sabit bir değer. |
| **DigitalTwins-Sensorhardwareıd** | `string(72)` | Yes | **İletiyi**gönderen sensör için benzersiz bir tanımlayıcı. Bu değer, sistemin işlemesi için bir nesnenin **HardwareID** özelliği ile aynı olmalıdır. Örneğin, `00FF0643BE88-CO2`. |
| **CreationTimeUtc** | `string` | Hayır | Yükün örnekleme süresini tanımlayan [ıso 8601](https://en.wikipedia.org/wiki/ISO_8601) biçimli bir tarih dizesi. Örneğin, `2018-09-20T07:35:00.8587882-07:00`. |
| **ID** | `string` | Hayır | Sistem genelinde olayları izlemek için kullanılan bir UUID. Örneğin, `cec16751-ab27-405d-8fe6-c68e1412ce1f`.

### <a name="send-your-message-to-digital-twins"></a>İletinizi dijital TWINS 'e gönderin

İletinizi dijital TWINS 'e göndermek için DeviceClient [SendEventAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.sendeventasync?view=azure-dotnet) veya [SendEventBatchAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.sendeventbatchasync?view=azure-dotnet) çağrısını kullanın.

## <a name="next-steps"></a>Sonraki adımlar

- Azure dijital TWINS veri işleme ve Kullanıcı tanımlı işlevler özellikleri hakkında bilgi edinmek için [Azure Digital TWINS veri işleme ve Kullanıcı tanımlı işlevleri](concepts-user-defined-functions.md)makalesini okuyun.
