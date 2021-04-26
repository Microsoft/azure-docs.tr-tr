---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 6b4913b510dabd0fc2c8456d4c199c6b11569597
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104612209"
---
Bu hızlı başlangıçta örnek bir IoT Tak ve Kullan cihaz uygulaması oluşturma, IoT Hub 'ınıza bağlama ve Azure IoT Explorer aracını kullanarak gönderdiği Telemetriyi görüntüleme gösterilmektedir. Örnek uygulama Java 'da yazılmıştır ve Java için Azure IoT cihaz SDK 'sına dahildir. Bir çözüm Oluşturucusu, herhangi bir cihaz kodunu görüntülemeye gerek olmadan IoT Tak ve Kullan cihazının yeteneklerini anlamak için Azure IoT gezgin aracını kullanabilir.

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

Bu hızlı başlangıcı Windows üzerinde gerçekleştirmek için, yerel Windows ortamınıza aşağıdaki yazılımı yüklersiniz:

* Java SE Development Kit 8. [Azure ve Azure Stack Için Java uzun süreli destek](/java/azure/jdk/), **uzun süreli destek** altında **Java 8**' i seçin.
* [Apache Maven 3](https://maven.apache.org/download.cgi).

## <a name="download-the-code"></a>Kodu indirme

Bu hızlı başlangıçta, Azure IoT Hub cihaz Java SDK 'sını klonlamak ve derlemek için kullanabileceğiniz bir geliştirme ortamı hazırlarsınız.

Seçtiğiniz dizinde bir komut istemi açın. [Azure IoT Java SDK 'ları ve kitaplıkları](https://github.com/Azure/azure-iot-sdk-java) GitHub deposunu bu konuma kopyalamak için aşağıdaki komutu yürütün:

```cmd
git clone https://github.com/Azure/azure-iot-sdk-java.git
```

## <a name="build-the-code"></a>Kodu oluşturma

Windows üzerinde kopyalanmış Java SDK deposunun kök klasörüne gidin.

Örnek uygulamayı derlemek için aşağıdaki komutu çalıştırın:

```cmd
mvn install -T 2C -DskipTests
```

## <a name="run-the-device-sample"></a>Cihaz örneğini çalıştırma

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

Örnek yapılandırma hakkında daha fazla bilgi edinmek için bkz. [örnek Readme](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/pnp-device-sample/readme.md).

*\Devıceıot-Device-samples\pnp-Device-sample\termostat-Device-Sample* klasörüne gidin.

Örnek uygulamayı çalıştırmak için aşağıdaki komutu çalıştırın:

```cmd
mvn exec:java -Dexec.mainClass="samples.com.microsoft.azure.sdk.iot.device.Thermostat"
```

Cihaz artık komutları ve özellik güncelleştirmelerini almaya hazır ve hub 'a telemetri verileri göndermeye başladı. Sonraki adımları tamamladıktan sonra örneği çalışır durumda tutun.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Kodu doğrulamak için Azure IoT Gezginini Kullanma

Cihaz istemcisi örneği başladıktan sonra, çalıştığını doğrulamak için Azure IoT gezgin aracını kullanın.

[!INCLUDE [iot-pnp-iot-explorer.md](iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>Kodu gözden geçirin

Bu örnek, basit bir IoT Tak ve Kullan termostat cihazı uygular. Bu örneğin uyguladığı model IoT Tak ve Kullan [bileşenleri](../articles/iot-pnp/concepts-modeling-guide.md)kullanmaz. [Termostat cihazının Dtdl model dosyası](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) , cihazın uyguladığı telemetri, özellik ve komutları tanımlar.

Cihaz kodu, `DeviceClient` IoT Hub 'ınıza bağlanmak için standart sınıfını kullanır. Cihaz, bağlantı isteğinde uyguladığı DTDL modelinin model KIMLIĞINI gönderir. Model KIMLIĞI gönderen bir cihaz IoT Tak ve Kullan cihazdır:

```java
private static void initializeDeviceClient() throws URISyntaxException, IOException {
    ClientOptions options = new ClientOptions();
    options.setModelId(MODEL_ID);
    deviceClient = new DeviceClient(deviceConnectionString, protocol, options);

    deviceClient.registerConnectionStatusChangeCallback((status, statusChangeReason, throwable, callbackContext) -> {
        log.debug("Connection status change registered: status={}, reason={}", status, statusChangeReason);

        if (throwable != null) {
            log.debug("The connection status change was caused by the following Throwable: {}", throwable.getMessage());
            throwable.printStackTrace();
        }
    }, deviceClient);

    deviceClient.open();
}
```

Model KIMLIĞI, aşağıdaki kod parçacığında gösterildiği gibi kodda depolanır:

```java
private static final String MODEL_ID = "dtmi:com:example:Thermostat;1";
```

Özellikleri güncelleştiren, komutları işleyen ve telemetri gönderen kod, IoT Tak ve Kullan kurallarını kullanmayan bir cihazın koduyla aynıdır.

Örnek, IoT Hub 'ınızdan gönderilen yüklerden JSON nesnelerini ayrıştırmak için bir JSON kitaplığı kullanır:

```java
import com.google.gson.Gson;

...

Date since = new Gson().fromJson(jsonRequest, Date.class);
```
