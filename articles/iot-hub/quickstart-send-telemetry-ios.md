---
title: Azure IoT Hub hızlı başlangıç 'a telemetri gönderin | Microsoft Docs
description: Bu hızlı başlangıçta, bir IoT Hub 'ına sanal telemetri göndermek ve bulutta işlenmek üzere IoT Hub 'ından Telemetriyi okumak için örnek bir iOS uygulaması çalıştırırsınız.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: quickstart
ms.custom: mvc
ms.date: 04/03/2019
ms.openlocfilehash: a23518cd016a1711e47734df0f7179770aa92a87
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72166977"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-ios"></a>Hızlı başlangıç: bir cihazdan IoT Hub 'a telemetri gönderme (iOS)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub, IoT cihazlarınızdan depolama ya da işleme için buluta kadar yüksek miktarda telemetri almanıza olanak sağlayan bir Azure hizmetidir. Bu makalede, bir sanal cihaz uygulamasından IoT Hub telemetri gönderirsiniz. Ardından, bir arka uç uygulamasından verileri görüntüleyebilirsiniz.

Bu makalede, IoT Hub Telemetriyi okumak üzere telemetri ve bir CLı yardımcı programı göndermek için önceden yazılmış bir Swift uygulaması kullanılmaktadır.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

- [Azure örneklerinden](https://github.com/Azure-Samples/azure-iot-samples-ios/archive/master.zip) kod örneğini indirin
- İOS SDK 'sının en son sürümünü çalıştıran [Xcode](https://developer.apple.com/xcode/)'un en son sürümü. Bu hızlı başlangıç, XCode 10,2 ve iOS 12,2 ile test edilmiştir.
- En son [Cocoapods](https://guides.cocoapods.org/using/getting-started.html)sürümü.
- Azure CLı için Microsoft Azure IoT uzantısını Cloud Shell örneğinize eklemek için aşağıdaki komutu çalıştırın. IOT uzantısı, Azure CLı 'ye IoT Hub, IoT Edge ve IoT cihaz sağlama hizmeti 'ne (DPS) özel komutlar ekler.

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

## <a name="create-an-iot-hub"></a>IoT Hub 'ı oluşturma

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Bir cihazı kaydetme

Bir cihazın bağlanabilmesi için IoT Hub 'ınız ile kayıtlı olması gerekir. Bu hızlı başlangıçta, sanal cihazı kaydetmek için Azure Cloud Shell kullanırsınız.

1. Cihaz kimliğini oluşturmak için Azure Cloud Shell aşağıdaki komutu çalıştırın.

   **Youriothubname**: aşağıdaki yer tutucuyu IoT Hub 'ınız için seçtiğiniz adla değiştirin.

   **Myiosdevice**: Bu, kaydetmekte olduğunuz cihazın adıdır. Gösterildiği gibi **Myiosdevice** kullanılması önerilir. Cihazınız için farklı bir ad seçerseniz, bu adı bu makalede da kullanmanız ve bunları çalıştırmadan önce örnek uygulamalarda cihaz adını güncelleştirmeniz gerekir.

   ```azurecli-interactive
   az iot hub device-identity create --hub-name {YourIoTHubName} --device-id myiOSdevice
   ```

1. Yeni kaydettiğiniz cihazın _Cihaz bağlantı dizesini_ almak için Azure Cloud Shell ' de aşağıdaki komutu çalıştırın:

   **Youriothubname**: aşağıdaki yer tutucuyu IoT Hub 'ınız için seçtiğiniz adla değiştirin.

   ```azurecli-interactive
   az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id myiOSdevice --output table
   ```

   Cihaz bağlantı dizesini bir yere göz önünde bir şekilde görünür:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=myiOSdevice;SharedAccessKey={YourSharedAccessKey}`

    Bu değeri daha sonra hızlı başlangıçta kullanacaksınız.

## <a name="send-simulated-telemetry"></a>Sanal telemetri gönder

Örnek uygulama, IoT Hub 'ınızdaki cihaza özgü bir uç noktaya bağlanan ve sanal sıcaklık ve nem telemetrisi gönderen bir iOS cihazında çalışır. 

### <a name="install-cocoapods"></a>CocoaPods 'i yükler

CocoaPods üçüncü taraf kitaplıklarını kullanan iOS projelerine yönelik bağımlılıkları yönetin.

Yerel bir Terminal penceresinde, önkoşullara indirdiğiniz Azure-IoT-Samples-iOS klasörüne gidin. Ardından, örnek projeye gidin:

```sh
cd quickstart/sample-device
```

XCode ' un kapalı olduğundan emin olun ve ardından aşağıdaki komutu çalıştırarak **Pod dosya** dosyasında belirtilen Cocoapods 'yi çalıştırın:

```sh
pod install
```

Projeniz için gerekli olan Pod 'yi yükleme ile birlikte yükleme komutu ayrıca, bağımlılıkları için pod 'yi kullanmak üzere yapılandırılmış bir Xcode çalışma alanı dosyası da oluşturmuştur. 

### <a name="run-the-sample-application"></a>Örnek uygulamayı çalıştırma 

1. Örnek çalışma alanını XCode 'da açın.

   ```sh
   open "MQTT Client Sample.xcworkspace"
   ```

2. **MQTT Istemci örnek** projesini genişletin ve aynı ada sahip klasörü genişletin.  
3. XCode 'da düzenlenmek üzere **ViewController. Swift** dosyasını açın. 
4. **ConnectionString** değişkenini arayın ve değeri, daha önce bir değişiklik yaptığınız cihaz bağlantı dizesiyle güncelleştirin.
5. Yaptığınız değişiklikleri kaydedin. 
6. Projeyi cihaz öykünücüsünde **Oluştur ve Çalıştır** düğmesiyle veya anahtar açılan **komutu + r**ile çalıştırın. 

   ![Projeyi Çalıştır](media/quickstart-send-telemetry-ios/run-sample.png)

7. Öykünücü açıldığında, örnek uygulamada **Başlat** ' ı seçin.

Aşağıdaki ekran görüntüsünde, uygulama IoT Hub 'ınıza sanal telemetri gönderdiğinden oluşan bazı örnek çıktılar gösterilmektedir:

   ![Sanal cihazı Çalıştır](media/quickstart-send-telemetry-ios/view-d2c.png)

## <a name="read-the-telemetry-from-your-hub"></a>Hub 'ınızdaki Telemetriyi okuyun

XCode öykünücüsü üzerinde çalıştırdığınız örnek uygulama, cihazdan gönderilen iletilerle ilgili verileri gösterir. Ayrıca, alınan verileri IoT Hub 'ınız aracılığıyla görüntüleyebilirsiniz. IoT Hub CLı uzantısı, IoT Hub hizmet tarafı **olayları** uç noktasına bağlanabilir. Uzantı, sanal cihazınızdan gönderilen cihazdan buluta iletileri alır. Bir IoT Hub arka uç uygulaması, cihazdan buluta iletileri almak ve işlemek için genellikle bulutta çalışır.

Azure Cloud Shell, `YourIoTHubName` ' ı IoT Hub 'ınızın adıyla değiştirerek aşağıdaki komutları çalıştırın:

```azurecli-interactive
az iot hub monitor-events --device-id myiOSdevice --hub-name {YourIoTHubName}
```

Aşağıdaki ekran görüntüsünde, uzantı sanal cihaz tarafından hub 'a gönderilen telemetri aldığı için çıkış gösterilmektedir:

Aşağıdaki ekran görüntüsünde, yerel Terminal pencerenizde gördüğünüz telemetri türü gösterilmektedir:

![Telemetriyi görüntüle](media/quickstart-send-telemetry-ios/view-telemetry.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, bir IoT Hub 'ı ayarlarsınız, bir cihaz kaydettiniz, bir iOS cihazından hub 'a sanal telemetri gönderdiniz ve hub 'dan Telemetriyi okumalısınız. 

Bir arka uç uygulamasından sanal cihazınızı nasıl denetleyeceğinizi öğrenmek için sonraki hızlı başlangıca geçin.

> [!div class="nextstepaction"]
> [Hızlı başlangıç: IoT Hub 'ına bağlı bir cihazı denetleme](quickstart-control-device-node.md)
