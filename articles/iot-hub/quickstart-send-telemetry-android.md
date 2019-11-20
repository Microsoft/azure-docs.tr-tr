---
title: Azure IoT Hub hızlı başlangıç 'a telemetri gönderme (Android) | Microsoft Docs
description: Bu hızlı başlangıçta, bir IoT Hub 'ına sanal telemetri göndermek ve bulutta işlenmek üzere IoT Hub 'ından Telemetriyi okumak için örnek bir Android uygulaması çalıştırırsınız.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/15/2019
ms.author: wesmc
ms.openlocfilehash: e213a92397240f2646ceda30688ecef422cdf29c
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72166367"
---
# <a name="quickstart-send-iot-telemetry-from-an-android-device"></a>Hızlı başlangıç: Android cihazından IoT telemetrisi gönderme

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub, IoT cihazlarınızdan depolama ya da işleme için buluta kadar yüksek miktarda telemetri almanıza olanak sağlayan bir Azure hizmetidir. Bu hızlı başlangıçta, fiziksel veya sanal cihaz üzerinde çalışan bir Android uygulamasından bir IoT Hub telemetri gönderirsiniz.

Hızlı başlangıç, Telemetriyi göndermek için önceden yazılmış bir Android uygulaması kullanır. Telemetri, Azure Cloud Shell kullanılarak IoT Hub okunacak. Uygulamayı çalıştırmadan önce bir IoT Hub oluşturun ve bir cihazı hub 'a kaydedersiniz.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

* @No__t-0 ' dan Android Studio. Android Studio yükleme hakkında daha fazla bilgi için bkz. [Android-Installation](https://developer.android.com/studio/install).

* Android SDK 27, bu makaledeki örnek tarafından kullanılır.

* Azure CLı için Microsoft Azure IoT uzantısını Cloud Shell örneğinize eklemek için aşağıdaki komutu çalıştırın. IOT uzantısı, Azure CLı 'ye IoT Hub, IoT Edge ve IoT cihaz sağlama hizmeti 'ne (DPS) özel komutlar ekler.

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

* Bu hızlı başlangıçta çalıştırdığınız [örnek Android uygulaması](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/device/AndroidSample) , GitHub 'daki Azure-IoT-Samples-Java deposunun bir parçasıdır. [Azure-IoT-Samples-Java](https://github.com/Azure-Samples/azure-iot-samples-java) deposunu indirin veya kopyalayın.

## <a name="create-an-iot-hub"></a>IoT Hub 'ı oluşturma

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Bir cihazı kaydetme

Bir cihazın bağlanabilmesi için IoT Hub 'ınız ile kayıtlı olması gerekir. Bu hızlı başlangıçta, sanal cihazı kaydetmek için Azure Cloud Shell kullanırsınız.

1. Cihaz kimliğini oluşturmak için Azure Cloud Shell aşağıdaki komutu çalıştırın.

   **Youriothubname**: aşağıdaki yer tutucuyu IoT Hub 'ınız için seçtiğiniz adla değiştirin.

   **Myandroıddevice**: Bu, kaydetmekte olduğunuz cihazın adıdır. **Myandroıddevice** 'ın gösterildiği gibi kullanılması önerilir. Cihazınız için farklı bir ad seçerseniz, bu adı bu makalede da kullanmanız ve bunları çalıştırmadan önce örnek uygulamalarda cihaz adını güncelleştirmeniz gerekir.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyAndroidDevice
    ```

2. Yeni kaydettiğiniz cihazın _Cihaz bağlantı dizesini_ almak için Azure Cloud Shell ' de aşağıdaki komutu çalıştırın:

    **Youriothubname**: aşağıdaki yer tutucuyu IoT Hub 'ınız için seçtiğiniz adla değiştirin.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyAndroidDevice --output table
    ```

    Cihaz bağlantı dizesini bir yere göz önünde bir şekilde görünür:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}`

    Bu değeri daha sonra Telemetriyi göndermek için bu hızlı başlangıçta kullanacaksınız.

## <a name="send-simulated-telemetry"></a>Sanal telemetri gönder

1. Android Studio 'de GitHub örnek Android projesini açın. Proje, kopyalanmış veya indirilen [Azure-IoT-Sample-Java](https://github.com/Azure-Samples/azure-iot-samples-java) deposunun kopyası dizininde bulunur.

        \azure-iot-samples-java\iot-hub\Samples\device\AndroidSample

2. Android Studio ' de, örnek proje için *Gradle. Properties* ' i açın ve **Device_Connection_String** yer tutucusunu, daha önce bir örneği yaptığınız cihaz bağlantı dizesiyle değiştirin.

    ```
    DeviceConnectionString=HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}
    ```

3. Android Studio ' de, **dosya** > **Gradle dosyaları ile eşitleme projesi**' ne tıklayın. Oluşturma işlemini doğrulayın.

   > [!NOTE]
   > Proje eşitleme başarısız olursa, aşağıdaki nedenlerden biri olabilir:
   >
   > * Android Studio sürümünüz için, projede başvurulan Android Gradle Plugin ve Gradle sürümleri güncel değil. Yüklemeniz için eklentinin ve Gradle sürümlerinin doğru sürümlerini başvurmak ve yüklemek için [Bu yönergeleri](https://developer.android.com/studio/releases/gradle-plugin) izleyin.
   > * Android SDK için lisans sözleşmesi imzalanmadı. Lisans sözleşmesini imzalamak ve SDK 'Yı indirmek için derleme çıktısındaki yönergeleri izleyin.

4. Derleme tamamlandıktan sonra @no__t **Çalıştır**' a tıklayın-1 **' uygulama ' Çalıştır**' ı tıklatın. Uygulamayı fiziksel bir Android cihazda veya bir Android öykünücüsünde çalışacak şekilde yapılandırın. Bir Android uygulamasını fiziksel bir cihazda veya Öykünücüde çalıştırma hakkında daha fazla bilgi için bkz. [uygulamanızı çalıştırma](https://developer.android.com/training/basics/firstapp/running-app).

5. Uygulama yüklendikten sonra, IoT Hub telemetri göndermeye başlamak için **Başlat** düğmesine tıklayın:

    ![Uygulamanızı](media/quickstart-send-telemetry-android/sample-screenshot.png)


## <a name="read-the-telemetry-from-your-hub"></a>Hub 'ınızdaki Telemetriyi okuyun

Bu bölümde, Android cihazı tarafından gönderilen cihaz iletilerini izlemek için [IoT uzantısıyla](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) birlikte Azure Cloud Shell kullanacaksınız.

1. Azure Cloud Shell kullanarak, IoT Hub 'ınızdaki iletileri bağlamak ve okumak için aşağıdaki komutu çalıştırın:

   **Youriothubname**: aşağıdaki yer tutucuyu IoT Hub 'ınız için seçtiğiniz adla değiştirin.

    ```azurecli-interactive
    az iot hub monitor-events --hub-name {YourIoTHubName} --output table
    ```

    Aşağıdaki ekran görüntüsünde, IoT Hub 'ı Android cihaz tarafından gönderilen Telemetriyi aldığı için çıkış gösterilmektedir:

      ![Azure CLı kullanarak cihaz iletilerini okuma](media/quickstart-send-telemetry-android/read-data.png)
## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, bir IoT Hub 'ı ayarlarsınız, bir cihaz kaydettiniz, bir Android uygulaması kullanarak hub 'a sanal telemetri gönderdiniz ve Azure Cloud Shell kullanarak hub 'ın Telemetriyi okuyaöğreneceksiniz.

Bir arka uç uygulamasından sanal cihazınızı nasıl denetleyeceğinizi öğrenmek için sonraki hızlı başlangıca geçin.

> [!div class="nextstepaction"]
> [Hızlı başlangıç: IoT Hub 'ına bağlı bir cihazı denetleme](quickstart-control-device-android.md)