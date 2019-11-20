---
title: IoT Tak ve Kullan önizleme cihazı oluşturma ve test etme | Microsoft Docs
description: Bir cihaz geliştiricisi olarak, IoT Tak ve Kullan önizleme cihazı için yeni bir cihaz yetenek modeli oluşturmak ve test etmek üzere VS Code nasıl kullanacağınızı öğrenin.
author: dominicbetts
ms.author: dobett
ms.date: 07/10/2019
ms.topic: tutorial
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: b7b9cd1040accda4d39af4d0a18940b56a45f929
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73569892"
---
# <a name="tutorial-create-and-test-a-device-capability-model-using-visual-studio-code"></a>Öğretici: Visual Studio Code kullanarak cihaz yetenek modeli oluşturma ve test etme

Bu öğreticide cihaz geliştiricisi olarak, _cihaz yeteneği modeli_oluşturmak için Visual Studio Code nasıl kullanılacağı gösterilmektedir. Bulutu, buluttaki bir Azure IoT Hub örneğine bağlanan bir cihazda çalıştırmak için iskelet kodu oluşturmak üzere kullanabilirsiniz.

Bu öğreticide, oluşturulan iskelet kodunun nasıl oluşturulacağını açıklayan bölüm, Windows kullandığınızı varsayar.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Cihaz yetenek modeli oluşturma
> * Modelden iskelet cihaz kodu oluştur
> * Oluşturulan koddaki saplamaları uygulayın
> * Bir IoT Hub ile etkileşimleri test etmek için kodu çalıştırın

## <a name="prerequisites"></a>Ön koşullar

Bu öğreticide cihaz yetenek modeliyle çalışmak için şunlar gerekir:

* [Visual Studio Code](https://code.visualstudio.com/download): vs Code birden çok platformda kullanılabilir
* [Vs Code Uzantı paketi Için Azure IoT araçları](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) . VS Code ' ye uzantı paketini yüklemek için aşağıdaki adımları kullanın:

    1. VS Code, **Uzantılar** sekmesini seçin.
    1. **Azure IoT araçlarını**arayın.
    1. **Yükle**’yi seçin.

Bu öğreticide Windows üzerinde oluşturulan C kodunu derlemek için şunlar gerekir:

* Visual Studio  **C++ için derleme araçları ve** **NuGet Paket Yöneticisi bileşen** iş yükleri [için derleme araçları](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=BuildTools&rel=16) . Ya da [Visual Studio (Community, Professional veya Enterprise)](https://visualstudio.microsoft.com/downloads/) 2019, 2017 veya 2015 aynı iş yükleri yüklüyken zaten yüklüyse.
* [Git](https://git-scm.com/download)
* [CMake](https://cmake.org/download/)

Bu öğreticide cihaz kodunuzu test etmek için şunlar gerekir:

* [Azure IoT Gezgini](https://github.com/Azure/azure-iot-explorer/releases).
* Azure aboneliği. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="model-your-device"></a>Cihazınızı modelleme

Bir cihaz yetenek modeli oluşturmak için _Digital ikizi tanım dilini_ kullanırsınız. Model genellikle birden çok _arabirim_ tanım dosyasından ve tek bir model dosyasından oluşur. **Vs Code Için Azure IoT araçları** , bu JSON dosyalarını oluşturmanıza ve düzenlemenize yardımcı olacak araçlar içerir.

### <a name="create-the-interface-file"></a>Arabirim dosyası oluşturma

VS Code ' de IoT cihazınızın yeteneklerini tanımlayan bir arabirim dosyası oluşturmak için:

1. **Devicemodel**adlı bir klasör oluşturun.

1. VS Code başlatın ve komut paletini açmak için **CTRL + SHIFT + P** tuşlarını kullanın.

1. **Tak ve kullan** girin ve ardından **IoT eklentisi & Play: Create Interface** komutunu seçin.

1. ' A gidin ve oluşturduğunuz **devicemodel** klasörünü seçin.

1. Sonra arabirimin adı olarak **Environmentalalgılayıcı** girin ve **ENTER**'a basın. VS Code **Environmentalalgılayıcı. Interface. JSON**adlı örnek bir arabirim dosyası oluşturur.

1. Bu dosyanın içeriğini aşağıdaki JSON ile değiştirin ve `@id` alanındaki `{your name}` benzersiz bir değerle değiştirin. Yalnızca a-z, A-Z, 0-9 ve alt çizgi karakterlerini kullanın. Daha fazla bilgi için bkz. [Digital ikizi Identifier Format](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL#digital-twin-identifier-format). Arabirimi depoya kaydetmek için arabirim KIMLIĞI benzersiz olmalıdır:

    ```json
    {
      "@id": "urn:{your name}:EnvironmentalSensor:1",
      "@type": "Interface",
      "displayName": "Environmental Sensor",
      "description": "Provides functionality to report temperature, humidity. Provides telemetry, commands and read-write properties",
      "comment": "Requires temperature and humidity sensors.",
      "contents": [
        {
          "@type": "Property",
          "displayName": "Device State",
          "description": "The state of the device. Two states online/offline are available.",
          "name": "state",
          "schema": "boolean"
        },
        {
          "@type": "Property",
          "displayName": "Customer Name",
          "description": "The name of the customer currently operating the device.",
          "name": "name",
          "schema": "string",
          "writable": true
        },
        {
          "@type": "Property",
          "displayName": "Brightness Level",
          "description": "The brightness level for the light on the device. Can be specified as 1 (high), 2 (medium), 3 (low)",
          "name": "brightness",
          "writable": true,
          "schema": "long"
        },
        {
          "@type": [
            "Telemetry",
            "SemanticType/Temperature"
          ],
          "description": "Current temperature on the device",
          "displayName": "Temperature",
          "name": "temp",
          "schema": "double",
          "unit": "Units/Temperature/fahrenheit"
        },
        {
          "@type": [
            "Telemetry",
            "SemanticType/Humidity"
          ],
          "description": "Current humidity on the device",
          "displayName": "Humidity",
          "name": "humid",
          "schema": "double",
          "unit": "Units/Humidity/percent"
        },
        {
          "@type": "Telemetry",
          "name": "magnetometer",
          "displayName": "Magnetometer",
          "comment": "This shows a complex telemetry that contains a magnetometer reading.",
          "schema": {
            "@type": "Object",
            "fields": [
              {
                "name": "x",
                "schema": "integer"
              },
              {
                "name": "y",
                "schema": "integer"
              },
              {
                "name": "z",
                "schema": "integer"
              }
            ]
          }
        },
        {
          "@type": "Command",
          "name": "turnon",
          "response": {
            "name": "turnon",
            "schema": "string"
          },
          "comment": "This Commands will turn-on the LED light on the device.",
          "commandType": "synchronous"
        },
        {
          "@type": "Command",
          "name": "turnoff",
          "comment": "This Commands will turn-off the LED light on the device.",
          "response": {
            "name": "turnoff",
            "schema": "string"
          },
          "commandType": "synchronous"
        }
      ],
      "@context": "http://azureiot.com/v1/contexts/IoTModel.json"
    }
    ```

    Bu arabirim **müşteri adı**, **sıcaklık**gibi telemetri türleri ve **turnOn**gibi komutlar gibi cihaz özelliklerini tanımlar.

1. Bu arabirim dosyasının sonuna **yanıp sönen** adlı bir komut özelliği ekleyin. Komutu eklemeden önce bir virgül eklediğinizden emin olun. IntelliSense 'in, otomatik tamamlama ve doğrulamanın bir arabirim tanımını düzenlemenize nasıl yardımcı olduğunu görmek için tanımı yazmayı deneyin:

    ```json
    {
      "@type": "Command",
      "description": "This command will begin blinking the LED for given time interval.",
      "name": "blink",
      "request": {
        "name": "blinkRequest",
        "schema": {
          "@type": "Object",
          "fields": [
            {
              "name": "interval",
              "schema": "long"
            }
          ]
        }
      },
      "response": {
        "name": "blinkResponse",
        "schema": "string"
      },
      "commandType": "synchronous"
    }
    ```

1. Dosyayı kaydedin.

### <a name="create-the-model-file"></a>Model dosyası oluşturma

Model dosyası, IoT Tak ve Kullan cihazınızın uyguladığı arabirimleri belirtir. Genellikle, bir modelde en az iki arabirim vardır: cihazınızın belirli özelliklerini tanımlayan bir veya daha fazla ve tüm IoT Tak ve Kullan cihazların uygulaması gereken standart bir arabirim.

IoT Tak ve Kullan cihazınızın VS Code uyguladığı arabirimleri belirten bir model dosyası oluşturmak için:

1. Komut paletini açmak için **CTRL + SHIFT + P** tuşlarını kullanın.

1. **Tak ve kullan** girin ve ardından **IoT eklentisi & Play: yetenek modeli oluştur** komutunu seçin. Ardından, modelin adı olarak **Sensorboxmodel** girin. VS Code **Sensorboxmodel. capabilitymodel. JSON**adlı örnek bir arabirim dosyası oluşturur.

1. Bu dosyanın içeriğini aşağıdaki JSON ile değiştirin ve `@id` alanındaki ve `EnvironmentalSensor` arabirimindeki `{your name}`, **Environmentalalgılayıcı. Interface. JSON** dosyasında kullandığınız değerle değiştirin. Arabirimi depoya kaydetmek için arabirim KIMLIĞI benzersiz olmalıdır:

    ```json
    {
      "@id": "urn:{your name}:SensorboxModel:1",
      "@type": "CapabilityModel",
      "displayName": "Environmental Sensorbox Model",
      "implements": [
        {
          "schema": "urn:{your name}:EnvironmentalSensor:1",
          "name": "environmentalSensor"
        },
        {
          "schema": "urn:azureiot:DeviceManagement:DeviceInformation:1",
          "name": "deviceinfo"
        }
      ],
      "@context": "http://azureiot.com/v1/contexts/CapabilityModel.json"
    }
    ```

    Model, **Environmentalalgılayıcı** arabiriminizi ve standart **deviceınformation** arabirimini uygulayan bir cihaz tanımlar.

1. Dosyayı kaydedin.

### <a name="download-the-deviceinformation-interface"></a>Deviceınformation arabirimini indirin

Modelden iskelet kodu oluşturabilmeniz için önce *ortak model deposundan* **deviceınformation** yerel bir kopyasını oluşturmanız gerekir. Ortak model deposu zaten **Deviceınformation** arabirimini içeriyor.

VS Code kullanarak, ortak model deposundan **Deviceınformation** arabirimini indirmek için:

1. Komut paletini açmak için **CTRL + SHIFT + P** tuşlarını kullanın.

1. **Tak ve kullan**girin, **model depoyu aç** komutunu seçin ve ardından **ortak model deposunu aç**' ı seçin.

1. **Arabirimler**' i seçin, sonra kimlik `urn:azureiot:DeviceManagement:DeviceInformation:1`cihaz bilgileri arabirimini seçin ve ardından **İndir**' i seçin.

Artık cihaz yetenek modelinizi oluşturan üç dosya vardır:

* urn_azureiot_DeviceManagement_DeviceInformation_1. Interface. JSON
* Environmentalalgılayıcı. Interface. JSON
* SensorboxModel. capabilitymodel. JSON

## <a name="publish-the-model"></a>Modeli Yayımla

Azure IoT gezgin aracının cihaz yetenek modelinizi okuyabilmesi için, bunu şirket deponuzda yayımlamanız gerekir. VS Code yayımlamak için şirket deposu için bağlantı dizesine ihtiyacınız vardır:

1. [IoT Için Azure Sertifikalı portala](https://aka.ms/ACFI)gidin.

1. Portalda oturum açmak için Microsoft _iş hesabınızı_ kullanın.

1. **Şirket deposu** ve ardından **bağlantı dizeleri**' ni seçin.

1. Bağlantı dizesini kopyalayın.

Şirket deponuzu VS Code açmak için:

1. Komut paletini açmak için **CTRL + SHIFT + P** tuşlarını kullanın.

1. **Tak ve kullan** girin ve ardından **IoT eklentisi & Play: model depoyu aç** komutunu seçin.

1. **Kurumsal model deposunu aç** ' ı seçin ve Bağlantı dizenizi yapıştırın.

1. Şirket deponuzu açmak için **ENTER** tuşuna basın.

Cihaz yetenek modelinizi ve arabirimlerini şirket deponuza yayımlamak için:

1. Komut paletini açmak için **CTRL + SHIFT + P** tuşlarını kullanın.

1. **Tak ve kullan** girin ve ardından **IoT eklentisi & Play: dosyaları model deposuna gönder** komutunu seçin.

1. **Environmentalsensörü. Interface. JSON** ve **sensorboxmodel. capabilitymodel. JSON** dosyalarını seçin ve **Tamam**' ı seçin.

Dosyalarınız artık şirket deponuzda depolanır.

## <a name="generate-code"></a>Kod oluşturma

Modelinizden iskelet C kodu oluşturmak için **vs Code Için Azure IoT araçları** 'nı kullanabilirsiniz. VS Code ' de iskelet kodu oluşturmak için:

1. Komut paletini açmak için **CTRL + SHIFT + P** tuşlarını kullanın.

1. **Tak ve kullan** girin ve ardından **IoT eklentisi & Play: cihaz kodu saplama oluştur** komutunu seçin.

1. **Sensorboxmodel. capabilitymodel. JSON** yetenek modeli dosyanızı seçin.

1. Proje adı olarak **sensorbox_app** girin.

1. Dil olarak **ANSI C** 'yi seçin.

1. Bağlama yöntemi olarak **IoT Hub cihaz bağlantı dizesi aracılığıyla** seçim yapın.

1. Proje şablonu olarak **Windows 'Da CMake projesi** seçin.

1. Cihaz SDK 'sını dahil etmek için **Vcpkg aracılığıyla** öğesini seçin.

VS Code iskelet C kodunu oluşturur ve dosyaları **modelcode** klasöründeki **sensorbox_app** klasörüne kaydeder. VS Code oluşturulan kod dosyalarını içeren yeni bir pencere açar.

## <a name="update-the-generated-code"></a>Oluşturulan kodu Güncelleştir

Kodu derleyip çalıştırmadan önce, saplaması özelliklerini, telemetri ve komutları uygulamanız gerekir.

VS Code içindeki saplaması koduna yönelik uygulamalar sağlamak için:

1. **SensorboxModel_impl. c** dosyasını açın.

1. Saplaması işlevlerini uygulamak için kod ekleyin.

1. Yaptığınız değişiklikleri kaydedin.

## <a name="build-the-code"></a>Kodu oluşturma

IoT Tak ve Kullan cihazınızı bir Azure IoT Hub ile test etmek için kodu çalıştırmadan önce kodu derlemeniz gerekir.

Kodu Windows üzerinde derlemek ve çalıştırmak için **sensorbox_app** klasöründeki **README.MD** dosyasındaki yönergeleri izleyin. Aşağıdaki bölümde, cihaz kodunu çalıştırdığınızda kullanılacak bir cihaz bağlantı dizesi alma yönergeleri yer almaktadır.

## <a name="test-the-code"></a>Kodu test etme

Kodu çalıştırdığınızda, IoT Hub bağlanır ve örnek telemetri ve özellik değerleri göndermeye başlar. Cihaz ayrıca IoT Hub gönderilen komutlara da yanıt verir. Bu davranışı doğrulamak için:

1. IoT Hub 'ı oluşturmak için:

    ```azurecli-interactive
    az group create --name environmentalsensorresources --location centralus
    az iot hub create --name {your iot hub name} \
      --resource-group environmentalsensorresources --sku F1
    ```

1. IoT Hub 'ınıza bir cihaz ekleyin ve bağlantı dizesini alın:

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {your iot hub name} --device-id MyPnPDevice
    az iot hub device-identity show-connection-string --hub-name {your iot hub name} --device-id MyPnPDevice --output table
    ```

    Bağlantı dizesini bir yere getirin.

1. Bir komut isteminde, SDK ve örnekleri oluşturduğunuz **Azure-IoT-SDK-c** klasörüne gidin. Sonra **CMake\\sensorbox_app\\yayın** klasörüne gidin.

1. Şu komutu çalıştırın:

    ```cmd
    sensorbox_app.exe {your device connection string}
    ```

1. IoT Hub 'ınıza bağlı IoT Tak ve Kullan cihazıyla etkileşim kurmak için Azure IoT gezgin aracını kullanın. Daha fazla bilgi için bkz. [Azure IoT Explorer 'ı yükleyip kullanma](./howto-install-iot-explorer.md).

## <a name="next-steps"></a>Sonraki adımlar

Sertifika için hazır bir IoT Tak ve Kullan oluşturduğunuza göre, şu şekilde şunları öğrenin:

> [!div class="nextstepaction"]
> [Sertifika için hazırlanmaya yönelik bir cihaz oluşturun](tutorial-build-device-certification.md)
