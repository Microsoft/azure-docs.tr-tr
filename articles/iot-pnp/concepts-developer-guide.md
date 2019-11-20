---
title: Geliştirici Kılavuzu-IoT Tak ve Kullan önizlemesi | Microsoft Docs
description: IoT Tak ve Kullan geliştiricileri için cihaz modellemesinin açıklaması
author: dominicbetts
ms.author: dobett
ms.date: 07/05/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 6d8e0e9e675b88c69b74cdad261280f5dcaf7161
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73581617"
---
# <a name="iot-plug-and-play-preview-modeling-developer-guide"></a>IoT Tak ve Kullan Preview modelleme Geliştirici Kılavuzu

IoT Tak ve Kullan önizlemesi, yeteneklerini Azure IoT uygulamalarına duyuran cihazlar oluşturmanızı sağlar. IoT Tak ve Kullan cihazları, bir müşteri bunları IoT Tak ve Kullan özellikli uygulamalara bağladığında el ile yapılandırma gerektirmez. IoT Central, IoT Tak ve Kullan özellikli bir uygulamanın örneğidir.

IoT Tak ve Kullan cihazı oluşturmak için bir cihaz açıklaması oluşturmanız gerekir. Açıklama, dijital TWINS tanım dili (DTDL) adlı basit bir tanım diliyle yapılır.

## <a name="device-capability-model"></a>Cihaz yetenek modeli

DTDL ile cihazınızın parçalarını anlatmak için bir _cihaz yetenek modeli_ oluşturursunuz. Tipik bir IoT cihazı şu şekilde yapılır:

- Özel parçalar, cihazınızın benzersiz hale getirme işlemleri.
- Tüm cihazlarda ortak olan standart parçalar.

Bu bölümler, cihaz yetenek modelindeki _arabirimler_ olarak adlandırılır. Arabirimler, cihazınızın uyguladığı her bir bölümün ayrıntılarını tanımlar.

Aşağıdaki örnekte, bir termostat cihazının cihaz yetenek modeli gösterilmektedir:

```json
{
  "@id": "urn:example:Thermostat_T_1000:1",
  "@type": "CapabilityModel",
  "implements": [
    {
      "name": "thermostat",
      "schema": "urn:example:Thermostat:1"
    },
    {
      "name": "urn_azureiot_deviceManagement_DeviceInformation",
      "schema": "urn:azureiot:deviceManagement:DeviceInformation:1"
    }
  ],
  "@context": "http://azureiot.com/v1/contexts/CapabilityModel.json"
}
```

Yetenek modelinde bazı gerekli alanlar vardır:

- `@id`: basit bir Tekdüzen Kaynak adı biçimindeki benzersiz bir KIMLIK.
- `@type`: Bu nesnenin yetenek modeli olduğunu bildirir.
- `@context`: yetenek modeli için kullanılan DTDL sürümünü belirtir.
- `implements`: cihazınızın uyguladığı arabirimleri listeler.

Implements bölümündeki arabirimler listesindeki her giriş için şunu vardır:

- `name`: arabirimin programlama adı.
- `schema`: yetenek modelinin uyguladığı arabirim.

Görüntü adı ve açıklama gibi yetenek modeline daha fazla ayrıntı eklemek için kullanabileceğiniz ek isteğe bağlı alanlar vardır. Yetenek modeli içinde belirtilen arabirimler, cihazın bileşenleri olarak düşünülebilir. Genel önizleme için, arabirim listesinin her şema için yalnızca bir girişi olabilir.

## <a name="interface"></a>Arayüz

DTDL ile, arabirimlerini kullanarak cihazınızın yeteneklerini anlayacağız. Arabirimler, cihazınızın bir parçası olan _özellikleri_, _telemetri_ve _komutları_ anlatmaktadır:

- `Properties`. Özellikler, cihazınızın durumunu temsil eden veri alanlarıdır. Bir Coolant göndericisinin yerinde durumu gibi, cihazın dayanıklı durumunu göstermek için özellikleri kullanın. Özellikler Ayrıca cihazın bellenim sürümü gibi temel cihaz özelliklerini de temsil edebilir. Özellikleri salt okunurdur veya yazılabilir olarak bildirebilirsiniz.
- `Telemetry`. Telemetri alanları sensörlerden ölçümleri temsil eder. Cihazınız her bir algılayıcı ölçümü aldığında, algılayıcı verilerini içeren bir telemetri olayı göndermelidir.
- `Commands`. Komutlar, cihazınızın kullanıcılarının cihazda yürütebilmesi için yöntemleri temsil eder. Örneğin, bir fanı değiştirme veya kapatma için bir Reset komutu veya komutu.

Aşağıdaki örnek, bir termostat cihazının arabirimini göstermektedir:

```json
{
  "@id": "urn:example:Thermostat:1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Telemetry",
      "name": "temperature",
      "schema": "double"
    }
  ],
  "@context": "http://azureiot.com/v1/contexts/IoTModel.json"
}
```

Bir arabirimin bazı gerekli alanları vardır:

- `@id`: basit bir Tekdüzen Kaynak adı biçimindeki benzersiz bir KIMLIK.
- `@type`: Bu nesnenin bir arabirim olduğunu bildirir.
- `@context`: arabirim için kullanılan DTDL sürümünü belirtir.
- `contents`: cihazınızı oluşturan özellikleri, telemetrisi ve komutları listeler.

Bu basit örnekte yalnızca tek bir telemetri alanı vardır. En az bir alan açıklamasında şunu vardır:

- `@type`: özelliğin türünü belirtir: `Telemetry`, `Property`veya `Command`.
- `name`: telemetri değerinin adını sağlar.
- `schema`: telemetri için veri türünü belirtir. Bu değer, Double, Integer, Boolean veya String gibi bir temel tür olabilir. Karmaşık nesne türleri, diziler ve eşlemeler de desteklenir.

Görünen ad ve açıklama gibi diğer isteğe bağlı alanlar, arabirime ve yeteneklere daha fazla ayrıntı eklemenizi sağlar.

### <a name="properties"></a>Özellikler

Varsayılan olarak, özellikler salt okunurdur. Salt okuma özellikleri, cihazın IoT Hub 'ınıza Özellik değeri güncelleştirmelerini bildirdiği anlamına gelir. IoT Hub 'ınız salt okunurdur bir özelliğin değerini ayarlayamadı.

Ayrıca, bir özelliği bir arabirim üzerinde yazılabilir olarak işaretleyebilirsiniz. Bir cihaz, IoT Hub 'ınızdaki yazılabilir bir özelliğe yönelik bir güncelleştirme alabilir ve hub 'ınıza rapor özellik değeri güncelleştirmelerini alabilir.

Özellik değerlerini ayarlamak için cihazların bağlı olması gerekmez. Güncelleştirilmiş değerler cihaz daha sonra hub 'a bağlanırsa aktarılır. Bu davranış hem salt okunurdur hem de yazılabilir özellikler için geçerlidir.

Cihazınızdan telemetri göndermek için özellikleri kullanmayın. Örneğin, `temperatureSetting=80` gibi salt okunur bir özellik, cihaz sıcaklığının 80 olarak ayarlandığı ve cihazın bu sıcaklığa veya bu sıcaklığın devam etmesi için çalıştığı anlamına gelir.

Yazılabilir özellikler için, cihaz uygulaması, özellik değerini alınıp uygulamadığını göstermek için istenen bir durum durum kodu, sürüm ve açıklama döndürür.

### <a name="telemetry"></a>Telemetri

Varsayılan olarak, IoT Hub tüm telemetri iletilerini cihazlarından, [Event Hubs](https://azure.microsoft.com/documentation/services/event-hubs/)uyumlu olan [yerleşik hizmete yönelik uç noktaya (**iletiler/olaylar**)](../iot-hub/iot-hub-devguide-messages-read-builtin.md) yönlendirir.

BLOB depolama veya diğer olay hub 'ları gibi diğer hedeflere telemetri göndermek için [IoT Hub özel uç noktalarını ve yönlendirme kurallarını](../iot-hub/iot-hub-devguide-messages-d2c.md) kullanabilirsiniz. Yönlendirme kuralları iletileri seçmek için ileti özelliklerini kullanır.

### <a name="commands"></a>Komutlar

Komutlar zaman uyumlu ya da zaman uyumsuz. Zaman uyumlu bir komutun varsayılan olarak 30 saniye içinde yürütülmesi gerekir ve komut geldiğinde cihazın bağlanması gerekir. Cihaz zaman yanıt verirse veya cihaz bağlı değilse, komut başarısız olur.

Uzun süre çalışan işlemler için zaman uyumsuz komutları kullanın. Cihaz, telemetri iletilerini kullanarak ilerleme bilgileri gönderir. Bu ilerleme iletileri aşağıdaki üst bilgi özelliklerine sahiptir:

- `iothub-command-name`: komut adı, örneğin `UpdateFirmware`.
- `iothub-command-request-id`: sunucu tarafında oluşturulan ve ilk çağrıda cihaza gönderilen istek KIMLIĞI.
- `iothub-interface-id`: Bu komutun tanımlanmış olduğu arabirimin KIMLIĞI, örneğin `urn:example:AssetTracker:1`.
 `iothub-interface-name`: Bu arabirimin örnek adı, örneğin `myAssetTracker`.
- `iothub-command-statuscode`: cihazdan döndürülen durum kodu, örneğin `202`.

## <a name="register-a-device"></a>Cihaz kaydetme

IoT Tak ve Kullan, cihazınızın yeteneklerini tanıtmayı kolaylaştırır. IoT Tak ve Kullan, cihazınız IoT Hub bağlandıktan sonra cihaz yetenek modelinizi kaydetmeniz gerekir. Kayıt, müşterilerin cihazınızın IoT Tak ve Kullan yeteneklerini kullanmasına olanak sağlar.

Bu kılavuzda, C için Azure IoT cihaz SDK 'sını kullanarak bir cihazın nasıl kaydedileceği gösterilmektedir.

Cihazınızın uyguladığı her arabirim için bir arabirim oluşturmanız ve uygulamayı uygulamasına bağlamanız gerekir.

Daha önce gösterilen Ise stat arabirimi için, C SDK kullanarak, termostat arabirimini oluşturup uygulamasına bağlayın:

```c
DIGITALTWIN_INTERFACE_HANDLE thermostatInterfaceHandle;

DIGITALTWIN_CLIENT_RESULT result = DigitalTwin_InterfaceClient_Create(
    "thermostat",
    "urn:example:Thermostat:1",
    null, null,
    &thermostatInterfaceHandle);

result = DigitalTwin_Interface_SetCommandsCallbacks(
    thermostatInterfaceHandle,
    commandsCallbackTable);

result = DigitalTwin_Interface_SetPropertiesUpdatedCallbacks(
    thermostatInterfaceHandle,
    propertiesCallbackTable);

```

Cihazınızın uyguladığı her arabirim için bu kodu tekrarlayın.

Bir arabirim oluşturduktan sonra, IoT Hub 'ınız ile cihaz yetenek modelinizi ve arabirimlerini kaydedin:

```c
DIGITALTWIN_INTERFACE_CLIENT_HANDLE interfaces[2];
interfaces[0] = thermostatInterfaceHandle;
interfaces[1] = deviceInfoInterfaceHandle;

result = DigitalTwin_DeviceClient_RegisterInterfacesAsync(
    digitalTwinClientHandle, // The handle for the connection to Azure IoT
    "urn:example:Thermostat_T_1000:1",
    interfaceHandleList, 2,
    null, null);
```

## <a name="use-a-device"></a>Cihaz kullanma

IoT Tak ve Kullan, yeteneklerini IoT Hub 'ınız ile kaydetmiş olan cihazları kullanmanıza olanak sağlar. Örneğin, bir cihazın özelliklerine ve komutlarına doğrudan erişebilirsiniz.

IoT Hub 'ınıza bağlı bir IoT Tak ve Kullan cihazı kullanmak için, IoT Hub REST API veya IoT dil SDK 'Lardan birini kullanın. Aşağıdaki örneklerde IoT Hub REST API kullanılır.

Termostat 'daki `DeviceInformation` arabirimindeki bellenim sürümü (`fwVersion`) gibi bir cihaz özelliğinin değerini almak için, dijital TWINS REST API kullanırsınız.

Termostat cihazınız `t-123`çağrılırsa, cihazınız tarafından uygulanan tüm özellikleri bir REST API GET çağrısıyla alırsınız:

```REST
GET /digitalTwins/t-123/interfaces
```

Daha genel olarak, tüm özelliklere bu REST API şablonuyla erişilir; burada `{device-id}` cihaz tanımlayıcısıdır:

```REST
GET /digitalTwins/{device-id}/interfaces
```

Arabirimin adını biliyorsanız ve bu arabirimin özelliklerini almak istiyorsanız, isteği ada göre belirli bir arabirime kapsam:

```REST
GET /digitalTwins/t-123/interfaces/info
```

Daha genel olarak, belirli bir arabirimin özelliklerine, `device-id` cihaz için tanımlayıcı olduğu ve arabirimin adı `{interface-name}` olan bu REST API şablonu aracılığıyla erişilebilir:

```REST
GET /digitalTwins/{device-id}/interfaces/{interface-name}
```

IoT Tak ve Kullan cihaz komutlarını doğrudan çağırabilirsiniz. `t-123` cihazdaki `Thermostat` arabiriminin bir `restart` komutu varsa, bunu bir REST API POST çağrısıyla çağırabilirsiniz:

```REST
POST /digitalTwins/t-123/interfaces/thermostat/commands/restart
```

Genel olarak, bu REST API şablonu aracılığıyla komutlar çağrılabilir:

- `device-id`: cihazın tanımlayıcısı.
- `interface-name`: cihaz yetenek modelindeki Implements bölümünde arabirimin adı.
- `command-name`: komutun adı.

```REST
/digitalTwins/{device-id}/interfaces/{interface-name}/commands/{command-name}
```

## <a name="next-steps"></a>Sonraki adımlar

Artık cihaz modelleme hakkında bilgi edindiğinize göre, bazı ek kaynaklar aşağıda verilmiştir:

- [Digital Ikizi tanım dili (DTDL)](https://aka.ms/DTDL)
- [C cihaz API’si](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [IoT REST API](https://docs.microsoft.com/rest/api/iothub/device)
