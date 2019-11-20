---
title: Azure IoT çözümünden IoT Tak ve Kullan önizleme cihazından etkileşim kurma | Microsoft Docs
description: Çözüm geliştiricisi olarak, IoT Tak ve Kullan cihazlarıyla etkileşim kurmak için hizmet SDK 'sını kullanma hakkında bilgi edinin.
author: YasinMSFT
ms.author: yahajiza
ms.date: 07/24/2019
ms.topic: tutorial
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: 5abfe0300bd61f5ccfbfccedf16659f055eb8ad4
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69878001"
---
# <a name="connect-to-and-interact-with-an-iot-plug-and-play-preview-device"></a>IoT Tak ve Kullan önizleme cihazına bağlanma ve bunlarla etkileşim kurma

Bu nasıl yapılır Kılavuzu, IoT çözümünüzün IoT Tak ve Kullan önizleme cihazlarıyla nasıl etkileşime gireceğini gösteren düğüm hizmeti SDK 'sında örnekleri nasıl kullanacağınızı gösterir.

[Iot Tak ve kullan cihazını çözüm](quickstart-connect-pnp-device-solution.md) hızlı başlangıç için tamamlamadıysanız, bunu şimdi yapmanız gerekir. Hızlı başlangıç, SDK 'nın nasıl indirileceği ve yükleneceğine ilişkin bazı örnekleri nasıl çalıştıracağınızı gösterir.

Hizmet örneklerini çalıştırmadan önce yeni bir Terminal açın, klonlanmış deponuzdaki kök klasöre gidin, **digitaltwins/quickbaşlangıçlar/hizmet** klasörüne gidin ve ardından bağımlılıkları yüklemek için aşağıdaki komutu çalıştırın:

```cmd/sh
npm install
```

## <a name="run-the-service-samples"></a>Hizmet örneklerini çalıştırma

Node. js hizmet SDK 'sının yeteneklerini araştırmak için aşağıdaki örnekleri kullanın. `IOTHUB_CONNECTION_STRING` Ortam değişkeninin kullandığınız kabukta ayarlandığından emin olun:

### <a name="retrieve-a-digital-twin-and-list-the-interfaces"></a>Dijital ikizi alma ve arabirimleri listeleme

**get_digital_twin. js** , cihazınız ile ilişkili dijital ikizi alır ve komut satırında bileşenini yazdırır. Çalışan bir cihaz örneğinin başarılı olması gerekmez.

**get_digital_twin_interface_instance. js** , aygıtınızla ilişkili dijital ikizi 'ın tek bir arabirim örneğini alır ve komut satırına yazdırır. Cihaz örneğinin çalıştırılmasını gerektirmez.

### <a name="get-and-set-properties-using-the-node-service-sdk"></a>Düğüm hizmeti SDK 'sını kullanarak özellikleri edinme ve ayarlama

**update_digital_twin. js** , tam bir düzeltme eki kullanarak cihazınızdaki dijital ikizi yazılabilir bir özelliği güncelleştirir. İsterseniz birden fazla arabirimde birden çok özelliği güncelleştirebilirsiniz. Bu işlemin başarılı olması için, cihaz örneğinin aynı anda çalışıyor olması gerekir. Başarı, cihaz örneği, bir özelliği güncelleştirme ile ilgili bir şeyi, terminalde güncelleştirilmiş bir dijital ikizi yazdırırken hizmet örneğini güncelleştiren bir şey gibi görünüyor.

### <a name="send-a-command-and-retrieve-the-response-using-the-node-service-sdk"></a>Düğüm hizmeti SDK 'sını kullanarak bir komut gönderin ve yanıtı alın

**invoke_command. js** , cihazınızda dijital ikizi zaman uyumlu bir komutu çağırır. Bu işlemin başarılı olması için, cihaz örneğinin aynı anda çalışıyor olması gerekir. Başarı, cihaz örneği bir komutun ele aldığı bir şeyi ve hizmet istemcisini terminalde komutun sonucunu yazdırmakta olduğu gibi görünüyor.

### <a name="connect-to-the-public-repository-and-retrieve-a-model-definition-using-the-node-service-sdk"></a>Düğüm hizmeti SDK 'sını kullanarak ortak depoya bağlanma ve model tanımı alma

Hizmet ve cihaz örnekleri ile aynı yönergeleri kullanarak, aşağıdaki ortam değişkenini ayarlamanız gerekir:

* `AZURE_IOT_MODEL_REPOSITORY_CONNECTION_STRING`

Bu bağlantı dizesini, **Şirket deponuzdaki** **bağlantı dizeleri** sekmesinde [IoT için Azure sertifikası](https://preview.catalog.azureiotsolutions.com) 'nda bulabilirsiniz.

Bağlantı dizesi aşağıdaki örneğe benzer şekilde görünür:

```text
HostName={repo host name};RepositoryId={repo ID};SharedAccessKeyName={repo key ID};SharedAccessKey={repo key secret}
```

Bu dört ortam değişkenini ayarladıktan sonra, örneği diğer örnekleri çalıştırdığınız gibi çalıştırın:

```cmd/sh
node model_repo.js
```

Bu örnek, **Modeldiscovery** arabirimini indirir ve bu modeli terminalde yazdırır.

### <a name="run-queries-in-iot-hub-based-on-capability-models-and-interfaces"></a>Yetenek modellerine ve arabirimlere göre IoT Hub sorguları çalıştırın

IoT Hub sorgu dili aşağıdaki örneklerde `HAS_INTERFACE` gösterildiği `HAS_CAPABILITYMODEL` gibi ve destekler:

```sql
select * from devices where HAS_INTERFACE('id without version', version)
```

```sql
select * from devices where HAS_CAPABILITYMODEL('id without version', version)
```

### <a name="creating-digital-twin-routes"></a>Dijital ikizi rotaları oluşturma

Çözümünüz, dijital ikizi değişiklik olaylarının bildirimlerini alabilir. Bu bildirimlere abone olmak için, bildirimleri BLOB depolama, Event Hubs veya Service Bus kuyruğu gibi bir uç noktaya göndermek için [IoT Hub yönlendirme özelliğini](../iot-hub/iot-hub-devguide-endpoints.md) kullanın.

Dijital ikizi yolu oluşturmak için:

1. Azure portal IoT Hub kaynağına gidin.
1. **İleti yönlendirmeyi**seçin.
1. **Rotalar** sekmesinde **Ekle**' yi seçin.
1. **Ad** alanına bir değer girin ve bir **uç nokta**seçin. Bir uç nokta yapılandırmadıysanız, **uç nokta Ekle**' yi seçin.
1. **Veri kaynağı** açılır penceresinde **dijital ikizi değişiklik olayları**' nı seçin.
1. **Kaydet**’i seçin.

Aşağıdaki JSON bir Digital ikizi değişiklik olayına bir örnek göstermektedir:

```json
{
  "interfaces": {
    "urn_azureiot_ModelDiscovery_DigitalTwin": {
      "name": "urn_azureiot_ModelDiscovery_DigitalTwin",
      "properties": {
        "modelInformation": {
          "reported": {
            "value": {
              "modelId": "urn:domain:capabilitymodel:TestCapability:1",
              "interfaces": {
                "MyInterfaceFoo": "urn:domain:interfaces:FooInterface:1",
                "urn_azureiot_ModelDiscovery_DigitalTwin": "urn:azureiot:ModelDiscovery:DigitalTwin:1"
              }
            }
          }
        }
      }
    },
    "MyInterfaceFoo": {
      "name": "MyInterfaceFoo",
      "properties": {
        "property_1": { "desired": { "value": "value_1" } },
        "property_2": {
          "desired": { "value": 20 },
          "reported": {
            "value": 10,
            "desiredState": {
              "code": 200,
              "version": 22,
              "subCode": 400,
              "description": ""
            }
          }
        },
        "property_3": { "reported": { "value": "value_3" } }
      }
    }
  },
  "version": 4
}
```

## <a name="next-steps"></a>Sonraki adımlar

IoT Tak ve Kullan cihazlarınızla etkileşime geçen hizmet çözümlerini öğrendiğinize göre, bir sonraki adım [model bulma](concepts-model-discovery.md)hakkında bilgi edinecek.
