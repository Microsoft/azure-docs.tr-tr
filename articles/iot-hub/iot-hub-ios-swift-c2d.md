---
title: Azure IoT Hub ile buluttan cihaza iletiler (iOS) | Microsoft Docs
description: İOS için Azure IoT SDK 'larını kullanarak Azure IoT Hub 'ından bir cihaza buluttan cihaza ileti gönderme.
author: kgremban
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/19/2018
ms.author: kgremban
ms.openlocfilehash: 1875e3437c13a678532c05b4057126f785b9125c
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70147524"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-ios"></a>IoT Hub (iOS) ile buluttan cihaza iletileri gönderme

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT Hub, milyonlarca cihaz ile bir çözüm arka ucu arasında güvenilir ve güvenli çift yönlü iletişimin sağlanmasına yardımcı olan, tam olarak yönetilen bir hizmettir. [Bir cihazdan IoT Hub 'ına yönelik Telemetriyi, bir](quickstart-send-telemetry-ios.md) IoT Hub 'ı oluşturmayı, bu kodda bir cihaz kimliği sağlamayı ve cihazdan buluta iletiler gönderen bir sanal cihaz uygulamasını nasıl kodlayacağınızı gösterir.

Bu öğretici şunların nasıl yapıldığını gösterir:

* Çözüm arka ucundan, IoT Hub aracılığıyla buluttan cihaza iletileri tek bir cihaza gönderin.

* Bir cihazda buluttan cihaza iletiler alın.

* Çözüm arka uçta, IoT Hub bir cihaza gönderilen iletiler için teslim bildirimi (*geri bildirim*) isteyin.

[IoT Hub geliştirici kılavuzunun Mesajlaşma bölümünde](iot-hub-devguide-messaging.md)buluttan cihaza iletiler hakkında daha fazla bilgi edinebilirsiniz.

Bu makalenin sonunda iki Swift iOS projesi çalıştırırsınız:

* **örnek cihaz**, aynı uygulama, IoT Hub 'ınıza bağlanan ve buluttan cihaza iletiler alan [bir cihazdan IoT Hub 'ına telemetri gönderme](quickstart-send-telemetry-ios.md)sırasında oluşturulan aynı uygulamadır.

* **örnek hizmeti**, sanal cihaz uygulamasına IoT Hub aracılığıyla buluttan cihaza ileti gönderir ve ardından teslim onayını alır.

> [!NOTE]
> IoT Hub, Azure IoT cihaz SDK 'Ları aracılığıyla birçok cihaz platformu ve dili (C, Java, Python ve JavaScript dahil) için SDK desteğine sahiptir. Cihazınızı Bu öğreticinin koduna bağlama ve genellikle Azure IoT Hub 'e yönelik adım adım yönergeler için bkz. [Azure IoT Geliştirici Merkezi](https://www.azure.com/develop/iot).

## <a name="prerequisites"></a>Önkoşullar

* Etkin bir Azure hesabı. (Hesabınız yoksa yalnızca birkaç dakika içinde [ücretsiz bir hesap](https://azure.microsoft.com/pricing/free-trial/) oluşturabilirsiniz.)

* Azure 'da etkin bir IoT Hub 'ı.

* [Azure örneklerinden](https://github.com/Azure-Samples/azure-iot-samples-ios/archive/master.zip)kod örneği.

* En son iOS SDK sürümünü çalıştıran en yeni [XCode](https://developer.apple.com/xcode/) sürümü. Bu hızlı başlangıç XCode 9.3 ve iOS 11.3 ile test edilmiştir.

* [CocoaPods](https://guides.cocoapods.org/using/getting-started.html)'un en son sürümü.

## <a name="simulate-an-iot-device"></a>IoT cihazının benzetimini yapma

Bu bölümde, IoT Hub 'ından buluttan cihaza iletileri almak için bir Swift uygulaması çalıştıran bir iOS cihazının benzetimini yapın. 

Bu, [bir cihazdan IoT Hub 'ına telemetri gönderme](quickstart-send-telemetry-ios.md)makalesinde oluşturduğunuz örnek cihazdır. Çalışan zaten varsa, bu bölümü atlayabilirsiniz.

### <a name="install-cocoapods"></a>CocoaPods yükleme

CocoaPods, üçüncü taraf kitaplıklar kullanan iOS projeleri için bağımlılıkları yönetir.

Bir terminal penceresinde, önkoşulların bir parçası olarak indirdiğiniz Azure-IoT-Samples-iOS klasörüne gidin. Ardından örnek projeye gidin:

```sh
cd quickstart/sample-device
```

XCode’un kapalı olduğundan emin olun ve **podfile** dosyasında bildirilen CocoaPods’u yüklemek üzere aşağıdaki komutu çalıştırın:

```sh
pod install
```

Yükleme komutu, projeniz için gereken podları yüklemeye ek olarak bağımlılıklar için podları kullanacak şekilde önceden yapılandırılmış bir XCode çalışma alanı dosyası da oluşturmuştur.

### <a name="run-the-sample-device-application"></a>Örnek cihaz uygulamasını çalıştırma

1. Cihazınızın bağlantı dizesini alın. Bu dizeyi cihaz ayrıntıları dikey penceresindeki [Azure Portal](https://portal.azure.com) kopyalayabilir veya aşağıdaki CLI komutuyla alabilirsiniz:

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id {YourDeviceID} --output table
    ```

1. Örnek çalışma alanını XCode'da açın.

   ```sh
   open "MQTT Client Sample.xcworkspace"
   ```

2. **MQTT Istemci örnek** projesini ve ardından aynı ada sahip klasörü genişletin.  

3. XCode’da düzenlemek üzere **ViewController.swift** dosyasını açın. 

4. **ConnectionString** değişkenini arayın ve değeri ilk adımda kopyaladığınız cihaz bağlantı dizesiyle güncelleştirin.

5. Yaptığınız değişiklikleri kaydedin. 

6. **Derle ve çalıştır** düğmesi ya da **command + r** tuş birleşimi ile projeyi cihaz öykünücüsünde çalıştırın.

   ![Projeyi çalıştırma](media/iot-hub-ios-swift-c2d/run-sample.png)

## <a name="get-the-iot-hub-connection-string"></a>IoT Hub bağlantı dizesini al

Bu makalede, [bir cihazdan IoT Hub 'ına telemetri gönderme](quickstart-send-telemetry-ios.md)bölümünde oluşturduğunuz IoT Hub 'ı aracılığıyla buluttan cihaza iletileri göndermek için bir arka uç hizmeti oluşturursunuz. Buluttan cihaza iletiler göndermek için hizmetinize **hizmet bağlantısı** izni verilmesi gerekir. Varsayılan olarak, her IoT Hub, bu izni veren **hizmet** adlı paylaşılan bir erişim ilkesiyle oluşturulur.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="simulate-a-service-device"></a>Hizmet cihazının benzetimini yap

Bu bölümde, IoT Hub aracılığıyla buluttan cihaza iletiler gönderen bir Swift uygulaması ile ikinci bir iOS cihazının benzetimini yapabilirsiniz. Bu yapılandırma, bir IoT Hub 'ına bağlı diğer iOS cihazları için bir denetleyici olarak çalışan bir iPhone veya iPad 'nin bulunduğu IoT senaryolarında yararlıdır.

### <a name="install-cocoapods"></a>CocoaPods yükleme

CocoaPods, üçüncü taraf kitaplıklar kullanan iOS projeleri için bağımlılıkları yönetir.

Önkoşullardan indirdiğiniz Azure IoT iOS örnekleri klasörüne gidin. Ardından, örnek hizmet projesine gidin:

```sh
cd quickstart/sample-service
```

XCode’un kapalı olduğundan emin olun ve **podfile** dosyasında bildirilen CocoaPods’u yüklemek üzere aşağıdaki komutu çalıştırın:

```sh
pod install
```

Yükleme komutu, projeniz için gereken podları yüklemeye ek olarak bağımlılıklar için podları kullanacak şekilde önceden yapılandırılmış bir XCode çalışma alanı dosyası da oluşturmuştur.

### <a name="run-the-sample-service-application"></a>Örnek hizmet uygulamasını çalıştırma

1. Örnek çalışma alanını XCode'da açın.

   ```sh
   open AzureIoTServiceSample.xcworkspace
   ```

2. **AzureIoTServiceSample** projesini genişletin ve aynı ada sahip klasörü genişletin.  

3. XCode’da düzenlemek üzere **ViewController.swift** dosyasını açın. 

4. **ConnectionString** değişkenini arayın ve bu değeri, [IoT Hub bağlantı dizesini al](#get-the-iot-hub-connection-string)' da daha önce kopyaladığınız hizmet bağlantı dizesiyle güncelleştirin.

5. Yaptığınız değişiklikleri kaydedin.

6. Xcode 'da, öykünücü ayarlarını IoT cihazını çalıştırmak için kullanılandan farklı bir iOS cihazı ile değiştirin. XCode aynı türde birden fazla öykünücü çalıştıramıyor.

   ![Öykünücü cihazını değiştirme](media/iot-hub-ios-swift-c2d/change-device.png)

7. Projeyi cihaz öykünücüsünde **Oluştur ve Çalıştır** düğmesiyle veya anahtar açılan **komutu + r**ile çalıştırın.

   ![Projeyi çalıştırma](media/iot-hub-ios-swift-c2d/run-app.png)

## <a name="send-a-cloud-to-device-message"></a>Buluttan cihaza ileti gönderme

Artık, buluttan cihaza iletileri göndermek ve almak için iki uygulamayı kullanmaya hazırsınız.

1. Sanal IoT cihazında çalıştırılan **IOS uygulama örnek** uygulamasında **Başlat**' a tıklayın. Uygulama cihazdan buluta iletileri göndermeye başlar, ancak aynı zamanda buluttan cihaza iletileri dinlemeye başlar.

   ![Örnek IoT cihaz uygulamasını görüntüle](media/iot-hub-ios-swift-c2d/view-d2c.png)

2. Sanal hizmet cihazında çalışan **ıothub hizmeti Istemci örnek** uygulamasında, ileti göndermek istediğiniz ıOT cihazının kimliğini girin. 

3. Düz metin iletisi yazıp **Gönder**' e tıklayın.

    Gönder ' e tıkladığınızda birkaç eylem gerçekleşir. Hizmet örneği, belirttiğiniz hizmet bağlantı dizesi nedeniyle uygulamanın erişimi olan IoT Hub 'ınıza iletiyi gönderir. IoT Hub 'ınız cihaz KIMLIĞINI denetler, iletiyi hedef cihaza gönderir ve kaynak cihaza bir onay alındı bilgisi gönderir. Sanal IoT cihazınızda çalışan uygulama, IoT Hub iletileri denetler ve ekrandaki en son bir metin olan metni yazdırır.

    Çıktın aşağıdaki örnekteki gibi görünmesi gerekir:

   ![Buluttan cihaza iletileri görüntüleme](media/iot-hub-ios-swift-c2d/view-c2d.png)

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, buluttan cihaza iletileri gönderme ve alma hakkında daha fazla öğrendiniz.

IoT Hub kullanan uçtan uca çözümlerin tam örneklerini görmek için bkz. [Azure IoT Çözüm Hızlandırıcıları](https://azure.microsoft.com/documentation/suites/iot-suite/) belgeleri.

IoT Hub çözümleri geliştirme hakkında daha fazla bilgi edinmek için [IoT Hub Geliştirici Kılavuzu](iot-hub-devguide.md)' na bakın.
