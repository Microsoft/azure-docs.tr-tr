---
title: Azure IoT SDK 'larını kullanarak Android Işleri platformu geliştirme | Microsoft Docs
description: Geliştirici Kılavuzu-Azure IoT Hub SDK 'Ları kullanarak Android şeyleri geliştirme hakkında bilgi edinin.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: robinsh
ms.openlocfilehash: 82f6da54aec7aee94c19fd75a06d2850ca0db8b6
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68883123"
---
# <a name="develop-for-android-things-platform-using-azure-iot-sdks"></a>Azure IoT SDK 'larını kullanarak Android öğeleri için geliştirme

[Azure IoT Hub SDK 'ları](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks) ; Windows, LINUX, OSX, mbed ve Android ve iOS gibi mobil platformlar gibi popüler platformlar için ilk katman desteği sağlar.  IoT dağıtımlarında daha fazla seçim ve esneklik sağlama taahhüdünün bir parçası olarak Java SDK 'Sı, [Android nesnelerin](https://developer.android.com/things/) platformunu da destekler.  Geliştiriciler cihaz tarafında Android şeyleri işletim sisteminin avantajlarından yararlanarak [Azure IoT Hub](about-iot-hub.md) 'yi aynı anda milyonlarca bağlı cihaza ölçeklendirebilen merkezi ileti hub 'ı olarak kullanabilir.

Bu öğreticide, Azure IoT Java SDK 'sını kullanarak Android öğeleri üzerinde bir cihaz tarafı uygulaması oluşturma adımları özetlenmektedir.

## <a name="prerequisites"></a>Önkoşullar

* Android nesnelerin çalıştığı bir Android tarafından desteklenen donanımlar.  Android nesnelerin işletim sistemini Flash ile nasıl flabileceğinize ilişkin [Android öğeleri belgelerini](https://developer.android.com/things/get-started/kits#flash-at) izleyebilirsiniz.  Android öğeleri cihazınızın, klavye, ekran ve fare gibi temel çevresel cihazlarda internet 'e bağlı olduğundan emin olun.  Bu öğreticide Raspberry PI 3 kullanılmaktadır.

* [Android Studio](https://developer.android.com/studio/) en son sürümü

* [Git](https://git-scm.com/) 'in en son sürümü

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-an-iot-hub"></a>IoT hub oluşturma

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Cihaz kaydetme

Bir cihazın bağlanabilmesi için IoT hub’ınıza kaydedilmesi gerekir. Bu hızlı başlangıçta Azure Cloud Shell kullanarak bir simülasyon cihazı kaydedeceksiniz.

1. Aşağıdaki komutları Azure Cloud Shell'de çalıştırarak IoT Hub CLI uzantısını ekleyin ve cihaz kimliğini oluşturun.

   **Youriothubname** : Aşağıdaki yer tutucusunu, IoT Hub 'ınız için seçtiğiniz adla değiştirin.

   **Myandroıdthingsdevice** : Bu, kayıtlı cihaz için verilen addır. Gösterildiği gibi Myandroıdthingsdevice kullanın. Cihazınız için farklı bir ad seçerseniz bu makalenin geri kalan bölümünde aynı adı kullanmanız ve örnek uygulamaları çalıştırmadan önce bunlarda da cihaz adını güncelleştirmeniz gerekir.

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyAndroidThingsDevice
    ```

2. Yeni kaydettiğiniz cihazın *Cihaz bağlantı dizesini* almak için Azure Cloud Shell ' de aşağıdaki komutları çalıştırın. Aşağıda `YourIoTHubName` , IoT Hub 'ınız için seçtiğiniz adla değiştirin.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyAndroidThingsDevice --output table
    ```

    Şu ifadeye benzer şekilde görünen cihaz bağlantı dizesini not edin:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidThingsDevice;SharedAccessKey={YourSharedAccessKey}`

    Bu değeri hızlı başlangıcın ilerleyen bölümlerinde kullanacaksınız.

## <a name="building-an-android-things-application"></a>Android öğeleri uygulaması oluşturma

1. Android nesnelerin bir uygulaması oluşturmanın ilk adımı, Android nesnelerin cihazlarına bağlanıyor. Android öğeleri cihazınızı bir görüntülemeye bağlayın ve internet 'e bağlayın. Android 'de, WiFi ile bağlantı kurmak için gereken [Belgeler](https://developer.android.com/things/get-started/kits) sağlanır. İnternet 'e bağlandıktan sonra ağlar altında listelenen IP adresini bir yere göz atın.

2. Yukarıda belirtilen IP adresiyle Android öğeleri cihazınıza bağlanmak için [ADB](https://developer.android.com/studio/command-line/adb) aracını kullanın. Terminalinizden bu komutu kullanarak bağlantıyı iki kez kontrol edin. Cihazlarınızı "bağlı" olarak listelenmiş olarak görmeniz gerekir.

   ```
   adb devices
   ```

3. Bu [depodan](https://github.com/Azure-Samples/azure-iot-samples-java) Android/Android için örneğimizi Indirin veya git kullanın.

   ```
   git clone https://github.com/Azure-Samples/azure-iot-samples-java.git
   ```

4. Android Studio ' de, "\azure-iot-samples-java\iot-hub\Samples\device\AndroidSample" içinde bulunan Android projesini açın.

5. Gradle. Properties dosyasını açın ve "Device_connection_string" değerini daha önce belirtilen cihaz bağlantı dizenizle değiştirin.
 
6. Çalıştır-Hata Ayıkla ' ya tıklayın ve bu kodu Android öğeleri cihazlarınıza dağıtmak için cihazınızı seçin.

7. Uygulama başarıyla başlatıldığında, Android öğeleri cihazınızda çalışan bir uygulamayı görebilirsiniz. Bu örnek uygulama, rastgele oluşturulmuş sıcaklık okumaları gönderir.

## <a name="read-the-telemetry-from-your-hub"></a>Hub’ınızdan telemetri okuma

Verileri IoT Hub 'ınız üzerinden alınan şekilde görüntüleyebilirsiniz. IoT Hub uzantısı IoT Hub’ınızdaki bir hizmet tarafı **Olaylar** uç noktasına bağlanabilir. Uzantı, simülasyon cihazınızdan gönderilen cihazdan buluta iletileri alır. IoT Hub arka uç uygulaması genellikle cihazdan buluta iletileri alıp işlemek için bulutta çalışır.

Aşağıdaki komutları Azure Cloud Shell'de çalıştırın, `YourIoTHubName` yerine IoT hub'ınızın adını yazın:

```azurecli-interactive
az iot hub monitor-events --device-id MyAndroidThingsDevice --hub-name YourIoTHubName
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Sonraki adımlar

* IoT Hub SDK 'Ları kullanarak [bağlantı ve güvenilir mesajlaşma yönetimi](iot-hub-reliability-features-in-sdks.md) hakkında bilgi edinin.
* İOS ve Android gibi [mobil platformlar için geliştirme](iot-hub-how-to-develop-for-mobile-devices.md) hakkında bilgi edinin.
* [Azure IoT SDK Platformu desteği](iot-hub-device-sdk-platform-support.md)
