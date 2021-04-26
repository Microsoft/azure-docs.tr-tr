---
title: IoT Tak ve Kullan Köprüsü | Microsoft Docs
description: IoT Tak ve Kullan köprüsünü ve Windows veya Linux ağ geçidine bağlı mevcut Cihazları IoT Tak ve Kullan cihazları olarak bağlamak için nasıl kullanacağınızı anlayın.
author: usivagna
ms.author: ugans
ms.date: 1/20/2021
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: e20e8ff6492db39d281fb70e35dd575ac3f8f991
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106066866"
---
# <a name="iot-plug-and-play-bridge"></a>IoT Tak Çalıştır köprüsü

IoT Tak ve Kullan Köprüsü, Windows veya Linux ağ geçidine bağlı mevcut cihazların IoT Tak ve Kullan cihazları olarak bağlanmasına yönelik açık kaynaklı bir uygulamadır. Uygulamayı Windows veya Linux makinenizde yükledikten ve yapılandırdıktan sonra, ekli cihazları bir IoT Hub 'ına bağlamak için kullanabilirsiniz. IoT Tak ve Kullan arabirimlerini eklenen cihazların gönderdiği telemetriye eşlemek, cihaz özellikleriyle çalışmak ve komutları çağırmak için köprüyü kullanabilirsiniz.

:::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-high-level.png" alt-text="Sol tarafta, IoT Tak ve Kullan Köprüsü içeren bir Windows veya Linux BILGISAYARA eklenen mevcut sensörlerden (hem kablolu hem de kablosuz) biri vardır. IoT Tak ve Kullan köprüsü daha sonra sağ taraftaki bir IoT Hub 'ına bağlanır":::

IoT Tak ve Kullan Köprüsü, Windows 10 veya Linux çalıştıran herhangi bir IoT cihazında, endüstriyel PC, sunucuda veya ağ geçidinde tek başına yürütülebilir dosya olarak dağıtılabilir. Uygulama kodunuza de derlenebilir. Basit bir Configuration JSON dosyası, IoT Tak ve Kullan köprüsüne ekli cihazların/çevre birimlerinin Azure 'a sunulacağını söyler.

## <a name="supported-protocols-and-sensors"></a>Desteklenen protokoller ve algılayıcılar

IoT Tak ve Kullan Köprüsü, bağdaştırıcı belgelerinin bağlantılarıyla birlikte, varsayılan olarak aşağıdaki çevresel çevre türlerini destekler:

|Çevre birimi|Windows|Linux|
|---------|---------|---------|
|[Bluetooth algılayıcı bağdaştırıcısı](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/bluetooth_sensor_adapter.md) , Bluetooth düşük ENERJI (BLE) etkin sensörler algıladı.       |Yes|Hayır|
|[Kamera bağdaştırıcısı](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/camera_adapter.md) , kameraları bir Windows 10 cihazına bağlar.               |Yes|Hayır|
|[Modbus bağdaştırıcısı](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/modbus_adapters.md) , bir Modbus cihazında algılayıcıların bağlanmasını sağlar.              |Yes|Yes|
|[MQTT bağdaştırıcısı](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/mqtt_adapter.md) , MQTT Aracısı kullanan cihazları bağlar.                  |Yes|Yes|
|[Serialpnp bağdaştırıcısı](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/serialpnp/Readme.md) , bir seri bağlantı üzerinden iletişim kuran cihazları bağlar.               |Yes|Yes|
|[WINDOWS USB çevre birimleri](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/coredevicehealth_adapter.md) , belirli BIR donanım kimliği olan cihazları bağlamak için bağdaştırıcı tarafından desteklenen cihaz arabirimi sınıflarının listesini kullanır.  |Yes|Geçerli değil|

IoT Tak ve Kullan köprüsünü ek cihaz protokollerini destekleyecek şekilde genişletmeyi öğrenmek için bkz. [ıot Tak ve kullan köprüsünü genişletme](howto-author-pnp-bridge-adapter.md). IoT Tak ve Kullan köprüsü oluşturma ve dağıtma hakkında bilgi edinmek için bkz. [ıot Tak ve kullan köprüsünü derleme ve dağıtma](howto-build-deploy-extend-pnp-bridge.md).

## <a name="iot-plug-and-play-bridge-architecture"></a>IoT Tak ve Kullan köprü mimarisi

:::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-components.png" alt-text="Sol tarafta IoT Tak ve Kullan Köprüsü içeren bir Windows veya Linux BILGISAYARıNA bağlı çeşitli çevresel aygıtları belirten birkaç kutu vardır. Üstten, yapılandırma noktaları etiketli bir kutu köprüye doğru. Köprü daha sonra diyagramın sağ tarafındaki bir IoT Hub 'ına bağlanır.":::

### <a name="iot-plug-and-play-bridge-adapters"></a>IoT Tak ve Kullan köprü bağdaştırıcıları

IoT Tak ve Kullan Köprüsü, çeşitli cihaz türleri için bir IoT Tak ve Kullan Köprü bağdaştırıcısı kümesini destekler. Bir *Bağdaştırıcı bildirimi* , bağdaştırıcıları bir köprüye statik olarak tanımlar.

Köprü bağdaştırıcısı Yöneticisi, bağdaştırıcı işlevlerini tanımlamak ve çağırmak için bildirimi kullanır. Bağdaştırıcı Yöneticisi, yalnızca yapılandırma dosyasında listelenen arabirim bileşenleri için gerekli olan köprü bağdaştırıcılarında Oluştur işlevini çağırır. Her IoT Tak ve Kullan bileşeni için bir bağdaştırıcı örneği oluşturulur.

Bir köprü bağdaştırıcısı, dijital bir ikizi arabirimi tanıtıcısı oluşturur ve alır. Bağdaştırıcı, bu tanıtıcıyı cihaz işlevselliğini dijital ikizi bağlamak için kullanır.

Köprü bağdaştırıcısı, yapılandırma dosyasındaki bilgileri kullanarak köprü aracılığıyla dijital ikizi iletişimine tam cihaz sağlamak için aşağıdaki teknikleri kullanır:

- Doğrudan bir iletişim kanalı oluşturur.
- Bir iletişim kanalının kullanılabilir hale gelmesini beklemek için bir cihaz İzleyicisi oluşturur.

### <a name="configuration-file"></a>Yapılandırma dosyası

IoT Tak ve Kullan Köprüsü, aşağıdakileri belirten bir JSON tabanlı yapılandırma dosyası kullanır:

- IoT Hub 'ına veya IoT Central uygulamasına bağlanma: seçenekler arasında bağlantı dizeleri, kimlik doğrulama parametreleri veya cihaz sağlama hizmeti (DPS) bulunur.
- Köprünün kullandığı IoT Tak ve Kullan yetenek modellerinin konumu. Model, IoT Tak ve Kullan cihazının yeteneklerini tanımlar ve statiktir ve sabittir.
- IoT Tak ve Kullan arabirimi bileşenlerinin listesi ve her bir bileşen için aşağıdaki bilgiler:
- Arabirim KIMLIĞI ve bileşen adı.
- Bileşenle etkileşimde bulunmak için gereken Köprü bağdaştırıcısı.
- Köprü bağdaştırıcısının cihazla iletişim kurması için ihtiyaç duyacağı cihaz bilgileri. Örneğin, donanım KIMLIĞI veya bir bağdaştırıcı, arabirim ya da protokol için özel bilgiler.
- Bağdaştırıcı, benzer cihazlara sahip birden fazla iletişim türünü destekliyorsa, isteğe bağlı bir köprü bağdaştırıcısı alt türü veya arabirim yapılandırması. Örnek, Bluetooth algılayıcı bileşeninin nasıl yapılandırılabileceğini göstermektedir:

    ```json
    {
      "_comment": "Component BLE sensor",
      "pnp_bridge_component_name": "blesensor1",
      "pnp_bridge_adapter_id": "bluetooth-sensor-pnp-adapter",
      "pnp_bridge_adapter_config": {
        "bluetooth_address": "267541100483311",
        "blesensor_identity" : "Blesensor1"
      }
    }
    ```

- Genel köprü bağdaştırıcı parametrelerinin isteğe bağlı bir listesi. Örneğin, Bluetooth algılayıcı köprü bağdaştırıcısında Desteklenen yapılandırmaların bir sözlüğü vardır. Bluetooth algılayıcı bağdaştırıcısı gerektiren bir arabirim bileşeni, bu yapılandırmalardan birini şu şekilde seçebilir `blesensor_identity` :

    ```json
    {
      "pnp_bridge_adapter_global_configs": {
        "bluetooth-sensor-pnp-adapter": {
          "Blesensor1" : {
            "company_id": "0x499",
            "endianness": "big",
            "telemetry_descriptor": [
              {
                "telemetry_name": "humidity",
                "data_parse_type": "uint8",
                "data_offset": 1,
                "conversion_bias": 0,
                "conversion_coefficient": 0.5
              },
              {
                "telemetry_name": "temperature",
                "data_parse_type": "int8",
                "data_offset": 2,
                "conversion_bias": 0,
                "conversion_coefficient": 1.0
              },
              {
                "telemetry_name": "pressure",
                "data_parse_type": "int16",
                "data_offset": 4,
                "conversion_bias": 0,
                "conversion_coefficient": 1.0
              },
              {
                "telemetry_name": "acceleration_x",
                "data_parse_type": "int16",
                "data_offset": 6,
                "conversion_bias": 0,
                "conversion_coefficient": 0.00980665
              },
              {
                "telemetry_name": "acceleration_y",
                "data_parse_type": "int16",
                "data_offset": 8,
                "conversion_bias": 0,
                "conversion_coefficient": 0.00980665
              },
              {
                "telemetry_name": "acceleration_z",
                "data_parse_type": "int16",
                "data_offset": 10,
                "conversion_bias": 0,
                "conversion_coefficient": 0.00980665
              }
            ]
          }
        }
      }
    }
    ```

## <a name="download-iot-plug-and-play-bridge"></a>IoT Tak ve Kullan köprüsü indir

Bir köprünün önceden oluşturulmuş bir sürümünü [ıot Tak ve kullan köprü sürümlerinde](https://github.com/Azure/iot-plug-and-play-bridge/releases) desteklenen bağdaştırıcılarla indirebilir ve en son sürümün varlık listesini genişletebilirsiniz. İşletim sisteminiz için uygulamanın en son sürümünü indirin.

Ayrıca, [GitHub 'Daki ıot Tak ve kullan köprüsünün](https://github.com/Azure/iot-plug-and-play-bridge)kaynak kodunu indirebilir ve görüntüleyebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

IoT Tak ve Kullan köprüsü mimarisine genel bir bakış edinolduğunuza göre, sonraki adımlar hakkında daha fazla bilgi edinebilirsiniz:

- [Linux veya Windows üzerinde çalışan bir IoT Tak ve Kullan köprüsü örneğini IoT Hub 'a bağlama](./howto-use-iot-pnp-bridge.md)
- [IoT Tak ve Kullan köprüsü oluşturma ve dağıtma](howto-build-deploy-extend-pnp-bridge.md)
- [IoT Tak ve Kullan köprü genişletme](howto-author-pnp-bridge-adapter.md)
- [GitHub 'da IoT Tak ve Kullan Köprüsü](https://github.com/Azure/iot-plug-and-play-bridge)
