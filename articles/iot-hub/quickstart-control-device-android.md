---
title: Hızlı başlangıç-bir cihazı Azure IoT Hub hızlı başlangıç (Android) ile denetleme | Microsoft Docs
description: Bu hızlı başlangıçta iki örnek Java uygulaması çalıştıracaksınız. Tek bir uygulama, hub 'ınıza bağlı cihazları uzaktan denetleyebileceği bir hizmet uygulamasıdır. Diğer uygulama, uzaktan denetlediğiniz, hub 'ınıza bağlı fiziksel veya sanal bir cihazda çalışır.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom:
- mvc
- mqtt
- devx-track-java
- devx-track-azurecli
ms.date: 06/21/2019
ms.author: wesmc
ms.openlocfilehash: 5946220a688206a35d0d68cd4efd4c356d27d7ee
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107868555"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-android"></a>Hızlı başlangıç: IoT Hub 'ına bağlı bir cihazı denetleme (Android)

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

Bu hızlı başlangıçta, Azure IoT Hub bağlı sanal cihazı denetlemek için doğrudan bir yöntem kullanırsınız. IoT Hub, IoT cihazlarınızı buluttan yönetmenize ve depolama ya da işleme için yüksek hacimli cihaz telemetrisine sahip olan bir Azure hizmetidir. IoT hub’ınıza bağlı bir cihazın davranışını uzaktan değiştirmek için doğrudan yöntemler kullanabilirsiniz. Bu hızlı başlangıç iki uygulama kullanır: bir arka uç hizmet uygulamasından çağrılan doğrudan yöntemlere ve Android cihazında doğrudan yöntemi çağıran bir hizmet uygulamasına yanıt veren bir sanal cihaz uygulaması.

## <a name="prerequisites"></a>Önkoşullar

* Etkin aboneliği olan bir Azure hesabı. [Ücretsiz bir tane oluşturun](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* [Android SDK 27 ile Android Studio](https://developer.android.com/studio/). Daha fazla bilgi için bkz. [ınstall Android Studio](https://developer.android.com/studio/install).

* [Git](https://git-scm.com/download/).

* [Azure IoT örneklerine (Java)](https://github.com/Azure-Samples/azure-iot-samples-java)dahil EDILEN [cihaz SDK örneği Android uygulaması](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/device/AndroidSample).

* Azure IoT örneklerine (Java) dahil edilen [SERVICE SDK örnek Android uygulaması](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/service/AndroidSample).

* Bağlantı noktası 8883 güvenlik duvarınızda açık. Bu hızlı başlangıçta bulunan cihaz örneği, 8883 bağlantı noktası üzerinden iletişim kuran MQTT protokolünü kullanır. Bu bağlantı noktası, bazı kurumsal ve eğitim ağ ortamlarında engellenebilir. Bu sorunu geçici olarak çözmek için daha fazla bilgi ve IoT Hub bkz. [bağlanma (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>IoT hub oluşturma

Önceki hızlı başlangıcı tamamladıysanız [: bir cihazdan IoT Hub 'ına telemetri gönderin](quickstart-send-telemetry-android.md), bu adımı atlayabilir ve daha önce oluşturduğunuz IoT Hub 'ını kullanabilirsiniz.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Cihaz kaydetme

Önceki hızlı başlangıcı tamamladıysanız [: bir cihazdan IoT Hub 'ına telemetri gönderin](quickstart-send-telemetry-android.md), bu adımı atlayabilir ve önceki hızlı başlangıçta kayıtlı olan cihazı kullanabilirsiniz.

Bir cihazın bağlanabilmesi için IoT hub’ınıza kaydedilmesi gerekir. Bu hızlı başlangıçta Azure Cloud Shell kullanarak bir simülasyon cihazı kaydedeceksiniz.

1. Cihaz kimliğini oluşturmak için Azure Cloud Shell aşağıdaki komutu çalıştırın.

   **Youriothubname**: aşağıdaki yer tutucuyu IoT Hub 'ınız için seçtiğiniz adla değiştirin.

   **Myandroıddevice**: Bu, kaydetmekte olduğunuz cihazın adıdır. **Myandroıddevice** 'ın gösterildiği gibi kullanılması önerilir. Cihazınız için farklı bir ad seçerseniz, bu adı da bu makalede kullanmanız gerekir ve bunları çalıştırmadan önce örnek uygulamalarda cihaz adını güncelleştirin.

    ```azurecli-interactive
    az iot hub device-identity create \
      --hub-name {YourIoTHubName} --device-id MyAndroidDevice
    ```

2. Yeni kaydettiğiniz cihazın _cihaz bağlantı dizesini_ almak için aşağıdaki komutları Azure Cloud Shell'de çalıştırın:

   **Youriothubname**: Bu yer tutucuyu, IoT Hub 'ınız için seçtiğiniz adla değiştirin.

    ```azurecli-interactive
    az iot hub device-identity connection-string show\
      --hub-name {YourIoTHubName} \
      --device-id MyAndroidDevice \
      --output table
    ```

    Aşağıdakine benzeyen cihaz bağlantı dizenizi not alın:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}`

    Bu değeri hızlı başlangıcın ilerleyen bölümlerinde kullanacaksınız.

## <a name="retrieve-the-service-connection-string"></a>Hizmet bağlantı dizesini alma

Ayrıca, arka uç hizmet uygulamalarının, yöntemleri yürütmek ve iletileri almak üzere IoT Hub 'ınıza bağlanmasını sağlamak için bir _hizmet bağlantı dizesi_ gerekir. Aşağıdaki komut, IoT hub'ınız için hizmeti bağlantı dizesini alır:

**Youriothubname**: aşağıdaki yer tutucuyu IoT Hub 'ınız için seçtiğiniz adla değiştirin.

```azurecli-interactive
az iot hub connection-string show --policy-name service --name {YourIoTHubName} --output table
```

Şu ifadeye benzer şekilde görünen hizmet bağlantı dizesini not edin:

`HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

Bu değeri hızlı başlangıcın ilerleyen bölümlerinde kullanacaksınız. Bu hizmet bağlantı dizesi, önceki adımda not ettiğiniz cihaz bağlantı dizesinden farklıdır.

## <a name="listen-for-direct-method-calls"></a>Doğrudan yöntem çağrılarını dinleme

Bu hızlı başlangıçta örneklerin her ikisi de GitHub 'daki Azure-IoT-Samples-Java deposunun bir parçasıdır. [Azure-IoT-Samples-Java](https://github.com/Azure-Samples/azure-iot-samples-java) deposunu indirin veya kopyalayın.

Cihaz SDK örnek uygulaması, fiziksel bir Android cihazda veya bir Android öykünücüsünde çalıştırılabilir. Örnek, IoT Hub 'ınızdaki cihaza özgü bir uç noktaya bağlanır, sanal telemetri gönderir ve merkezinizden doğrudan Yöntem çağrılarını dinler. Bu hızlı başlangıçta, hub’dan gelen doğrudan yöntem çağrısı, telemetri gönderme aralığını değiştirmesini cihaza bildirir. Sanal cihaz, doğrudan yöntemini yürütmeden sonra hub 'ınıza bir bildirim gönderir.

1. Android Studio 'de GitHub örnek Android projesini açın. Proje, kopyalanmış veya indirilen [Azure-IoT-Sample-Java](https://github.com/Azure-Samples/azure-iot-samples-java) deposunun şu dizininde bulunur: *\azure-iot-Samples-java\iot-hub\Samples\device\AndroidSample*.

2. Android Studio, örnek proje için *Gradle. Properties* ' i açın ve **Device_Connection_String** yer tutucusunu, daha önce bir değişiklik yaptığınız cihaz bağlantı dizesiyle değiştirin.

    ```
    DeviceConnectionString=HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}
    ```

3. Android Studio ' de,   >  **Gradle dosyaları ile dosya eşitleme projesi**' ne tıklayın. Oluşturma işlemini doğrulayın.

   > [!NOTE]
   > Proje eşitleme başarısız olursa, aşağıdaki nedenlerden biri olabilir:
   >
   > * Android Studio sürümünüz için, projede başvurulan Android Gradle Plugin ve Gradle sürümleri güncel değil. Yüklemeniz için eklentinin ve Gradle sürümlerinin doğru sürümlerini başvurmak ve yüklemek için [Bu yönergeleri](https://developer.android.com/studio/releases/gradle-plugin) izleyin.
   > * Android SDK için lisans sözleşmesi imzalanmadı. Lisans sözleşmesini imzalamak ve SDK 'Yı indirmek için derleme çıktısındaki yönergeleri izleyin.

4. Oluşturma işlemi tamamlandıktan sonra,   >  **' uygulama '** çalıştırmasını Çalıştır ' a tıklayın. Uygulamayı fiziksel bir Android cihazda veya bir Android öykünücüsünde çalışacak şekilde yapılandırın. Bir Android uygulamasını fiziksel bir cihazda veya Öykünücüde çalıştırma hakkında daha fazla bilgi için bkz. [uygulamanızı çalıştırma](https://developer.android.com/training/basics/firstapp/running-app).

5. Uygulama yüklendikten sonra, IoT Hub telemetri göndermeye başlamak için **Başlat** düğmesine tıklayın:

    ![İstemci cihazının Android uygulamasının örnek ekran görüntüsü](media/quickstart-control-device-android/sample-screenshot.png)

Çalışma zamanı sırasında telemetri aralığını güncelleştirmek üzere Service SDK örneğini yürüttüğünüzde, bu uygulamanın bir fiziksel cihazda veya Öykünücüde çalışır olması gerekir.

## <a name="read-the-telemetry-from-your-hub"></a>Hub’ınızdan telemetri okuma

Bu bölümde, Android cihazı tarafından gönderilen iletileri izlemek için [IoT uzantısıyla](/cli/azure/iot) birlikte Azure Cloud Shell kullanacaksınız.

1. Azure Cloud Shell'i kullanarak, IoT hub’ınızdan gelen iletilere bağlanmak ve bu iletileri okumak için aşağıdaki komutu çalıştırın:

   **Youriothubname**: Bu yer tutucuyu, IoT Hub 'ınız için seçtiğiniz adla değiştirin.

    ```azurecli-interactive
    az iot hub monitor-events --hub-name {YourIoTHubName} --output table
    ```

    Aşağıdaki ekran görüntüsünde, IoT Hub 'ı Android cihaz tarafından gönderilen Telemetriyi aldığı için çıkış gösterilmektedir:

      ![Azure CLI kullanarak cihaz iletilerini okuma](media/quickstart-control-device-android/read-data.png)

Varsayılan olarak, telemetri uygulaması Android cihazdan beş saniyede bir telemetri gönderir. Bir sonraki bölümde, Android IoT cihazının telemetri aralığını güncelleştirmek için doğrudan yöntem çağrısını kullanacaksınız.

## <a name="call-the-direct-method"></a>Doğrudan yöntem çağırma

Hizmet uygulaması, IoT Hub bir hizmet tarafı uç noktasına bağlanır. Uygulama, IoT Hub 'ınız aracılığıyla bir cihaza doğrudan Yöntem çağrıları yapar ve bu bildirimleri dinler.

Bu uygulamayı ayrı bir fiziksel Android cihazında veya Android öykünücüsünde çalıştırın.

Bir IoT Hub arka uç hizmeti uygulaması genellikle bulutta çalışır, burada, bir IoT Hub tüm cihazları denetleyen hassas bağlantı dizesiyle ilişkili riskleri azaltmak daha kolay olur. Bu örnekte, bunu yalnızca tanıtım amacıyla bir Android uygulaması olarak çalıştırdık. Bu hızlı başlangıçta diğer dil sürümleri, tipik bir arka uç hizmet uygulamasıyla daha yakından hizalanacak örnekler sağlar.

1. Android Studio 'de GitHub hizmeti örnek Android projesini açın. Proje, kopyalanmış veya indirilen [Azure-IoT-Sample-Java](https://github.com/Azure-Samples/azure-iot-samples-java) deposunun şu dizininde bulunur: *\azure-iot-Samples-java\iot-hub\Samples\service\AndroidSample*.

2. Android Studio, örnek proje için *Gradle. Properties* ' i açın. **ConnectionString** ve **DeviceID** özelliklerinin değerlerini, daha önce not ettiğiniz hizmet bağlantı dizesiyle ve kaydettiğiniz Android cihaz kimliğiyle güncelleştirin.

    ```
    ConnectionString=HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}
    DeviceId=MyAndroidDevice
    ```

3. Android Studio ' de,   >  **Gradle dosyaları ile dosya eşitleme projesi**' ne tıklayın. Oluşturma işlemini doğrulayın.

   > [!NOTE]
   > Proje eşitleme başarısız olursa, aşağıdaki nedenlerden biri olabilir:
   >
   > * Android Studio sürümünüz için, projede başvurulan Android Gradle Plugin ve Gradle sürümleri güncel değil. Yüklemeniz için eklentinin ve Gradle sürümlerinin doğru sürümlerini başvurmak ve yüklemek için [Bu yönergeleri](https://developer.android.com/studio/releases/gradle-plugin) izleyin.
   > * Android SDK için lisans sözleşmesi imzalanmadı. Lisans sözleşmesini imzalamak ve SDK 'Yı indirmek için derleme çıktısındaki yönergeleri izleyin.

4. Oluşturma işlemi tamamlandıktan sonra,   >  **' uygulama '** çalıştırmasını Çalıştır ' a tıklayın. Uygulamayı ayrı bir fiziksel Android cihazında veya Android öykünücüsünde çalışacak şekilde yapılandırın. Bir Android uygulamasını fiziksel bir cihazda veya Öykünücüde çalıştırma hakkında daha fazla bilgi için bkz. [uygulamanızı çalıştırma](https://developer.android.com/training/basics/firstapp/running-app).

5. Uygulama yüklendikten sonra, **set Messaging Interval** değerini **1000** olarak güncelleştirin ve **çağır**' a tıklayın.

    Yani telemetri mesajlaşma aralığı milisaniyedir. Cihaz örneğinin varsayılan telemetri aralığı 5 saniye olarak ayarlanır. Bu değişiklik Android IoT cihazını, her saniye Telemetriyi güncelleştirecek şekilde güncelleştirecek.

    ![Telemetri aralığını girin](media/quickstart-control-device-android/enter-telemetry-interval.png)

6. Uygulama, yöntemin başarıyla yürütülüp yürütülmediğini belirten bir bildirim alır.

    ![Doğrudan yöntem bildirimi](media/quickstart-control-device-android/direct-method-ack.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, arka uç uygulamasından bir cihazda doğrudan yöntem çağırılır ve sanal bir cihaz uygulamasındaki doğrudan yöntem çağrısına yanıt vermiş olursunuz.

Cihazdan buluta iletileri, buluttaki farklı hedeflere yönlendirmeyi öğrenmek için sonraki öğreticiyle devam edin.

> [!div class="nextstepaction"]
> [Öğretici: Telemetriyi işlenmek üzere farklı uç noktalara yönlendirme](tutorial-routing.md)
