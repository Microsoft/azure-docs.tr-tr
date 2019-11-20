---
title: Azure IoT Hub hızlı başlangıç (Android) ile bir cihazı denetleme | Microsoft Docs
description: Bu hızlı başlangıçta iki örnek Java uygulaması çalıştıracaksınız. Tek bir uygulama, hub 'ınıza bağlı cihazları uzaktan denetleyebileceği bir hizmet uygulamasıdır. Diğer uygulama, uzaktan denetlediğiniz, hub 'ınıza bağlı fiziksel veya sanal bir cihazda çalışır.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom: mvc
ms.date: 06/21/2019
ms.author: wesmc
ms.openlocfilehash: d607608167e1287c7df35157ccb9870f40f22943
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72516719"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-android"></a>Hızlı başlangıç: IoT Hub 'ına bağlı bir cihazı denetleme (Android)

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

IoT Hub, IoT cihazlarınızı buluttan yönetmenizi ve depolama ya da işleme için buluta yönelik yüksek hacimli cihaz telemetrisini gerçekleştirmenizi sağlayan bir Azure hizmetidir. Bu hızlı başlangıçta, IoT hub’ınıza bağlı bir simülasyon cihazını denetlemek için *doğrudan yöntem* kullanırsınız. IoT hub’ınıza bağlı bir cihazın davranışını uzaktan değiştirmek için doğrudan yöntemler kullanabilirsiniz.

Hızlı başlangıçta, önceden yazılmış iki Java uygulaması kullanılır:

* Bir arka uç hizmet uygulamasından çağrılan doğrudan yöntemlere yanıt veren bir sanal cihaz uygulaması. Doğrudan yöntem çağrıları almak için bu uygulama, IoT hub’ınızda aygıta özgü bir uç noktaya bağlanır.

* Android cihazında doğrudan yöntemini çağıran bir hizmet uygulaması. Bir cihazda doğrudan yöntem çağırmak için bu uygulama, IoT hub’ınızda sunucu tarafı uç noktasına bağlanır.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

* Android Studio https://developer.android.com/studio/. Android Studio yükleme hakkında daha fazla bilgi için bkz. [Android-Installation](https://developer.android.com/studio/install).

* Android SDK 27, bu makaledeki örnek tarafından kullanılır.

* Azure CLı için Microsoft Azure IoT uzantısını Cloud Shell örneğinize eklemek için aşağıdaki komutu çalıştırın. IOT uzantısı, Azure CLı 'ye IoT Hub, IoT Edge ve IoT cihaz sağlama hizmeti 'ne (DPS) özel komutlar ekler.

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

* Bu hızlı başlangıç Için iki örnek uygulama gerekiyor: [CIHAZ SDK örnek Android uygulaması](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/device/AndroidSample) ve [Service SDK örnek Android uygulaması](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/service/AndroidSample). Bu örneklerin her ikisi de GitHub 'daki Azure-IoT-Samples-Java deposunun bir parçasıdır. [Azure-IoT-Samples-Java](https://github.com/Azure-Samples/azure-iot-samples-java) deposunu indirin veya kopyalayın.

## <a name="create-an-iot-hub"></a>Bir IoT Hub oluşturma

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
    az iot hub device-identity show-connection-string \
      --hub-name {YourIoTHubName} \
      --device-id MyAndroidDevice \
      --output table
    ```

    Şu ifadeye benzer şekilde görünen cihaz bağlantı dizesini not edin:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}`

    Bu değeri hızlı başlangıcın ilerleyen bölümlerinde kullanacaksınız.

## <a name="retrieve-the-service-connection-string"></a>Hizmet bağlantı dizesini alma

Ayrıca, arka uç hizmet uygulamalarının, yöntemleri yürütmek ve iletileri almak üzere IoT Hub 'ınıza bağlanmasını sağlamak için bir _hizmet bağlantı dizesi_ gerekir. Aşağıdaki komut, IoT hub'ınız için hizmeti bağlantı dizesini alır:

**Youriothubname**: aşağıdaki yer tutucuyu IoT Hub 'ınız için seçtiğiniz adla değiştirin.

```azurecli-interactive
az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
```

Şu ifadeye benzer şekilde görünen hizmet bağlantı dizesini not edin:

`HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

Bu değeri hızlı başlangıcın ilerleyen bölümlerinde kullanacaksınız. Bu hizmet bağlantı dizesi, önceki adımda not ettiğiniz cihaz bağlantı dizesinden farklıdır.

## <a name="listen-for-direct-method-calls"></a>Doğrudan yöntem çağrılarını dinleme

Cihaz SDK örnek uygulaması, fiziksel bir Android cihazda veya bir Android öykünücüsünde çalıştırılabilir. Örnek, IoT Hub 'ınızdaki cihaza özgü bir uç noktaya bağlanır, sanal telemetri gönderir ve merkezinizden doğrudan Yöntem çağrılarını dinler. Bu hızlı başlangıçta, hub’dan gelen doğrudan yöntem çağrısı, telemetri gönderme aralığını değiştirmesini cihaza bildirir. Sanal cihaz, doğrudan yöntemini yürütmeden sonra hub 'ınıza bir bildirim gönderir.

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

4. Derleme tamamlandıktan sonra **Çalıştır** '  >  **' uygulama ' Çalıştır**' a tıklayın. Uygulamayı fiziksel bir Android cihazda veya bir Android öykünücüsünde çalışacak şekilde yapılandırın. Bir Android uygulamasını fiziksel bir cihazda veya Öykünücüde çalıştırma hakkında daha fazla bilgi için bkz. [uygulamanızı çalıştırma](https://developer.android.com/training/basics/firstapp/running-app).

5. Uygulama yüklendikten sonra, IoT Hub telemetri göndermeye başlamak için **Başlat** düğmesine tıklayın:

    ![İstemci cihazının Android uygulamasının örnek ekran görüntüsü](media/quickstart-control-device-android/sample-screenshot.png)

Çalışma zamanı sırasında telemetri aralığını güncelleştirmek üzere Service SDK örneğini yürüttüğünüzde, bu uygulamanın bir fiziksel cihazda veya Öykünücüde çalışır olması gerekir.

## <a name="read-the-telemetry-from-your-hub"></a>Hub’ınızdan telemetri okuma

Bu bölümde, Android cihazı tarafından gönderilen iletileri izlemek için [IoT uzantısıyla](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) birlikte Azure Cloud Shell kullanacaksınız.

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

1. Android Studio 'de GitHub hizmeti örnek Android projesini açın. Proje, kopyalanmış veya indirilen [Azure-IoT-Sample-Java](https://github.com/Azure-Samples/azure-iot-samples-java) deposunun kopyası dizininde bulunur.

        \azure-iot-samples-java\iot-hub\Samples\service\AndroidSample

2. Android Studio, örnek proje için *Gradle. Properties* ' i açın. **ConnectionString** ve **DeviceID** özelliklerinin değerlerini, daha önce not ettiğiniz hizmet bağlantı dizesiyle ve kaydettiğiniz Android cihaz kimliğiyle güncelleştirin.

    ```
    ConnectionString=HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}
    DeviceId=MyAndroidDevice
    ```

3. Android Studio ' de, **dosya** > **Gradle dosyaları ile eşitleme projesi**' ne tıklayın. Oluşturma işlemini doğrulayın.

   > [!NOTE]
   > Proje eşitleme başarısız olursa, aşağıdaki nedenlerden biri olabilir:
   >
   > * Android Studio sürümünüz için, projede başvurulan Android Gradle Plugin ve Gradle sürümleri güncel değil. Yüklemeniz için eklentinin ve Gradle sürümlerinin doğru sürümlerini başvurmak ve yüklemek için [Bu yönergeleri](https://developer.android.com/studio/releases/gradle-plugin) izleyin.
   > * Android SDK için lisans sözleşmesi imzalanmadı. Lisans sözleşmesini imzalamak ve SDK 'Yı indirmek için derleme çıktısındaki yönergeleri izleyin.

4. Derleme tamamlandıktan sonra **Çalıştır** '  >  **' uygulama ' Çalıştır**' a tıklayın. Uygulamayı ayrı bir fiziksel Android cihazında veya Android öykünücüsünde çalışacak şekilde yapılandırın. Bir Android uygulamasını fiziksel bir cihazda veya Öykünücüde çalıştırma hakkında daha fazla bilgi için bkz. [uygulamanızı çalıştırma](https://developer.android.com/training/basics/firstapp/running-app).

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
