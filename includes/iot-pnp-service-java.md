---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 8d3f35a733a0f78fabc33df857d911ba3cd222f5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "102244531"
---
IoT Tak ve Kullan, arka plandaki cihaz uygulamasıyla ilgili bilgi sahibi olmadan bir cihazın özellikleri ile etkileşim kurmanızı sağlayarak IoT 'yi basitleştirir. Bu hızlı başlangıçta, çözümünüze bağlı bir IoT Tak ve Kullan cihazına bağlanmak ve bunları denetlemek için Java 'nın nasıl kullanılacağı gösterilmektedir.

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

Bu hızlı başlangıcı Windows üzerinde gerçekleştirmek için, yerel Windows ortamınıza aşağıdaki yazılımı yüklersiniz:

* Java SE Development Kit 8. [Azure ve Azure Stack Için Java uzun süreli destek](/java/azure/jdk/), **uzun süreli destek** altında **Java 8**' i seçin.
* [Apache Maven 3](https://maven.apache.org/download.cgi).

### <a name="clone-the-sdk-repository-with-the-sample-code"></a>Örnek kodla SDK deposunu kopyalayın

[Hızlı başlangıç: Windows üzerinde çalışan bir örnek ıot Tak ve kullan cihaz uygulamasını IoT Hub (Java) Ile bağlama](../articles/iot-pnp/quickstart-connect-device.md), depoyu zaten Klonladığınız.

Seçtiğiniz dizinde bir komut istemi açın. Java GitHub deposunun [Microsoft Azure IoT SDK](https://github.com/Azure/azure-iot-sdk-java) 'larını bu konuma kopyalamak için aşağıdaki komutu yürütün:

```cmd
git clone https://github.com/Azure/azure-iot-sdk-java.git
```

## <a name="build-and-run-the-sample-device"></a>Örnek cihazı derleyin ve çalıştırın

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

Örnek yapılandırma hakkında daha fazla bilgi edinmek için bkz. [örnek Readme](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/readme.md).

Bu hızlı başlangıçta, IoT Tak ve Kullan cihazı olarak Java 'da yazılmış örnek bir termostat cihazı kullanırsınız. Örnek cihazı çalıştırmak için:

1. Bir Terminal penceresi açın ve GitHub 'dan kopyaladığınız Java deposunu Microsoft Azure IoT SDK 'sını içeren yerel klasöre gidin.

1. Bu terminal penceresi, **cihaz** terminali olarak kullanılır. Kopyalanmış deponuzdaki kök klasöre gidin. Aşağıdaki komutu çalıştırarak tüm bağımlılıkları yükler:

1. Örnek cihaz uygulamasını derlemek için aşağıdaki komutu çalıştırın:

    ```cmd
    mvn install -T 2C -DskipTests
    ```

1. Örnek cihaz uygulamasını çalıştırmak için, *devıceiot-Device-samples\pnp-Device-sample\termostat-Device-Sample* klasörüne gidin ve şu komutu çalıştırın:

    ```cmd
    mvn exec:java -Dexec.mainClass="samples.com.microsoft.azure.sdk.iot.device.Thermostat"
    ```

Cihaz artık komutları ve özellik güncelleştirmelerini almaya hazır ve hub 'a telemetri verileri göndermeye başladı. Sonraki adımları tamamladıktan sonra örnek cihazı çalışır durumda tutun.

## <a name="run-the-sample-solution"></a>Örnek çözümü çalıştırma

Ortamınızı IoT Hub 'ınıza ve cihazınıza bağlanacak şekilde yapılandırmak için, [ıot Tak ve kullan hızlı başlangıçlarını ve öğreticilerini ayarlama](../articles/iot-pnp/set-up-environment.md) bölümünde iki ortam değişkeni oluşturdunuz:

* **IOTHUB_CONNECTION_STRING**: IoT Hub bağlantı dizesi daha önce bir nota yaptınız.
* **IOTHUB_DEVICE_ID**: `"my-pnp-device"` .

Bu hızlı başlangıçta, yeni ayarladığınız örnek cihazla etkileşim kurmak için Java 'da yazılmış bir örnek IoT çözümü kullanırsınız.

> [!NOTE]
> Bu örnek, **IoT Hub hizmeti istemcisinden** **com. Microsoft. Azure. SDK. IoT. Service** ad alanını kullanır. Dijital TWINS API 'SI de dahil olmak üzere API 'Ler hakkında daha fazla bilgi edinmek için bkz. [hizmet Geliştirici Kılavuzu](../articles/iot-pnp/concepts-developer-guide-service.md).

1. **Hizmet** terminali olarak kullanmak için başka bir Terminal penceresi açın.

1. Kopyalanmış Java SDK deposunda, *service\ıot-Service-samples\pnp-Service-sample\termostat-Service-Sample* klasörüne gidin.

1. Örnek hizmet uygulamasını çalıştırmak için aşağıdaki komutu çalıştırın:

    ```cmd
    mvm exec:java -Dexec.mainClass="samples.com.microsoft.azure.sdk.iot.service.Thermostat"
    ```

### <a name="get-device-twin"></a>Cihaz ikizi al

Aşağıdaki kod parçacığı, hizmetinde cihaz ikizi nasıl alınacağını gösterir:

```java
 // Get the twin and retrieve model Id set by Device client.
DeviceTwinDevice twin = new DeviceTwinDevice(deviceId);
twinClient.getTwin(twin);
System.out.println("Model Id of this Twin is: " + twin.getModelId());
```

### <a name="update-a-device-twin"></a>Cihaz ikizi güncelleştirme

Aşağıdaki kod parçacığı, ikizi cihaz aracılığıyla özellikleri güncelleştirmek için nasıl *Düzeltme Eki* kullanacağınızı gösterir:

```java
String propertyName = "targetTemperature";
double propertyValue = 60.2;
twin.setDesiredProperties(Collections.singleton(new Pair(propertyName, propertyValue)));
twinClient.updateTwin(twin);
```

Cihaz çıktısı, cihazın bu özellik güncelleştirmesine nasıl yanıt verdiğini gösterir.

### <a name="invoke-a-command"></a>Komut çağırma

Aşağıdaki kod parçacığı, cihazda bir komutu çağırılacağını gösterir:

```java
// The method to invoke for a device without components should be "methodName" as defined in the DTDL.
String methodToInvoke = "getMaxMinReport";
System.out.println("Invoking method: " + methodToInvoke);

Long responseTimeout = TimeUnit.SECONDS.toSeconds(200);
Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);

// Invoke the command.
String commandInput = ZonedDateTime.now(ZoneOffset.UTC).minusMinutes(5).format(DateTimeFormatter.ISO_DATE_TIME);
MethodResult result = methodClient.invoke(deviceId, methodToInvoke, responseTimeout, connectTimeout, commandInput);
if(result == null)
{
    throw new IOException("Method result is null");
}

System.out.println("Method result status is: " + result.getStatus());
```

Cihaz çıktısı, cihazın bu komuta nasıl yanıt verdiğini gösterir.
