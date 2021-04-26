---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 3bf5ac4e01bca3bfc3cc8720a068bc53830b4747
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104612488"
---
Bu öğretici, birden çok bileşen örnek IoT Tak ve Kullan cihaz uygulaması oluşturmayı, IoT Hub 'ınıza bağlamayı ve gönderdiği Telemetriyi görüntülemek için Azure CLı 'yi kullanmayı gösterir. Örnek uygulama Java 'da yazılmıştır ve Java için Azure IoT cihaz SDK 'sına dahildir. Bir çözüm Oluşturucusu, herhangi bir cihaz kodunu görüntülemeye gerek olmadan bir IoT Tak ve Kullan cihazının yeteneklerini anlamak için Azure CLı 'yı kullanabilir.

Bu öğreticide, bileşenleriyle bir örnek IoT Tak ve Kullan cihaz uygulaması oluşturma, bunları IoT Hub 'ınıza bağlama ve Azure IoT Gezgini aracını kullanarak hub 'a gönderdiği bilgileri görüntüleme hakkında bilgi verilmektedir. Örnek uygulama Java 'da yazılmıştır ve Java için Azure IoT cihaz SDK 'sına dahildir. Bir çözüm Oluşturucusu, herhangi bir cihaz kodunu görüntülemeye gerek olmadan IoT Tak ve Kullan cihazının yeteneklerini anlamak için Azure IoT gezgin aracını kullanabilir.

Bu öğreticide şunları yaptınız:

> [!div class="checklist"]
> * Örnek kodu indirin.
> * Örnek kodu oluşturun.
> * Örnek cihaz uygulamasını çalıştırın ve IoT Hub 'ınıza bağlandığını doğrulayın.
> * Kaynak kodunu gözden geçirin.

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

Bu öğreticiyi Windows üzerinde gerçekleştirmek için, yerel Windows ortamınıza aşağıdaki yazılımları yüklersiniz:

* Java SE Development Kit 8. [Azure ve Azure Stack Için Java uzun süreli destek](/java/azure/jdk/), **uzun süreli destek** altında **Java 8**' i seçin.
* [Apache Maven 3](https://maven.apache.org/download.cgi).

## <a name="download-the-code"></a>Kodu indirme

[Hızlı başlangıç: Windows üzerinde çalışan bir örnek ıot Tak ve kullan cihaz uygulamasını IoT Hub (Java) Ile bağlama](../articles/iot-pnp/quickstart-connect-device.md), depoyu zaten Klonladığınız.

Seçtiğiniz dizinde bir komut istemi açın. [Azure IoT Java SDK 'ları ve kitaplıkları](https://github.com/Azure/azure-iot-sdk-java) GitHub deposunu bu konuma kopyalamak için aşağıdaki komutu yürütün:

```cmd
git clone https://github.com/Azure/azure-iot-sdk-java.git
```

Bu işlemin tamamlanması birkaç dakika sürer.

## <a name="build-the-code"></a>Kodu oluşturma

Windows üzerinde kopyalanmış Java SDK deposunun kök klasörüne gidin. Bağımlılıkları derlemek için aşağıdaki komutu çalıştırın:

```cmd/sh
mvn install -T 2C -DskipTests
```

## <a name="run-the-device-sample"></a>Cihaz örneğini çalıştırma

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

Örnek uygulamayı çalıştırmak için *\device\iot-Device-samples\pnp-Device-sample\temperature-Controller-Device-Sample* klasörüne gidin ve şu komutu çalıştırın:

```cmd/sh
mvn exec:java -Dexec.mainClass="samples.com.microsoft.azure.sdk.iot.device.TemperatureController"
```

Cihaz artık komutları ve özellik güncelleştirmelerini almaya hazır ve hub 'a telemetri verileri göndermeye başladı. Sonraki adımları tamamladıktan sonra örneği çalışır durumda tutun.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Kodu doğrulamak için Azure IoT Gezginini Kullanma

Cihaz istemcisi örneği başladıktan sonra, çalıştığını doğrulamak için Azure IoT gezgin aracını kullanın.

[!INCLUDE [iot-pnp-iot-explorer.md](iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>Kodu gözden geçirin

Bu örnek, IoT Tak ve Kullan ısı denetleyicisi cihazı uygular. Bu örneğin uyguladığı model [birden çok bileşen](../articles/iot-pnp/concepts-modeling-guide.md)kullanır. [Sıcaklık cihazının dijital TWINS tanım dili (DTDL) modeli dosyası](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) , cihazın uyguladığı telemetri, Özellikler ve komutları tanımlar.

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

Cihaz IoT Hub 'ınıza bağlandıktan sonra kod, komut işleyicilerini kaydeder.

```java
deviceClient.subscribeToDeviceMethod(new MethodCallback(), null, new MethodIotHubEventCallback(), null);
```

İki termostat bileşenlerinde istenen özellik güncelleştirmelerine yönelik ayrı işleyiciler vardır:

```java
deviceClient.startDeviceTwin(new TwinIotHubEventCallback(), null, new GenericPropertyUpdateCallback(), null);
Map<Property, Pair<TwinPropertyCallBack, Object>> desiredPropertyUpdateCallback = Stream.of(
        new AbstractMap.SimpleEntry<Property, Pair<TwinPropertyCallBack, Object>>(
                new Property(THERMOSTAT_1, null),
                new Pair<>(new TargetTemperatureUpdateCallback(), THERMOSTAT_1)),
        new AbstractMap.SimpleEntry<Property, Pair<TwinPropertyCallBack, Object>>(
                new Property(THERMOSTAT_2, null),
                new Pair<>(new TargetTemperatureUpdateCallback(), THERMOSTAT_2))
).collect(Collectors.toMap(AbstractMap.SimpleEntry::getKey, AbstractMap.SimpleEntry::getValue));

deviceClient.subscribeToTwinDesiredProperties(desiredPropertyUpdateCallback);
```

Örnek kod, her bir termostat bileşeninden telemetri gönderir:

```java
sendTemperatureReading(THERMOSTAT_1);
sendTemperatureReading(THERMOSTAT_2);
```

`sendTemperatureReading`Yöntemi, `PnpHhelper` her bileşen için ileti oluşturmak üzere sınıfını kullanır:

```java
Message message = PnpHelper.createIotHubMessageUtf8(telemetryName, currentTemperature, componentName);
```

`PnpHelper`Sınıfı, birden çok bileşen modeliyle kullanabileceğiniz diğer örnek yöntemleri içerir.

İki termostat bileşeninden telemetri ve özellikleri görüntülemek için Azure IoT gezgin aracını kullanın:

:::image type="content" source="media/iot-pnp-multiple-components-java/multiple-component.png" alt-text="Azure IoT Gezgini 'nde birden çok bileşen cihazı":::

Ayrıca, iki termostat bileşeninden ya da varsayılan bileşende komutları çağırmak için Azure IoT gezgin aracını da kullanabilirsiniz.
