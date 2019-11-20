---
title: IoT Tak ve Kullan Preview örnek cihaz kodunu IoT Hub 'ye bağlama (Windows) | Microsoft Docs
description: IoT Hub 'ına bağlanan Windows üzerinde IoT Tak ve Kullan önizleme örnek cihaz kodu oluşturun ve çalıştırın. Cihaz tarafından hub 'a gönderilen bilgileri görüntülemek için Azure IoT gezgin aracını kullanın.
author: ChrisGMsft
ms.author: chrisgre
ms.date: 08/02/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 0d89be9da55c97a5b49157251896d3a513c2c6db
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74152077"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-running-on-windows-to-iot-hub-c-windows"></a>Hızlı başlangıç: Windows 'ta IoT Hub için çalışan bir örnek IoT Tak ve Kullan önizleme cihaz uygulaması bağlama (C Windows)

Bu hızlı başlangıçta, örnek bir IoT Tak ve Kullan cihaz uygulamasının nasıl oluşturulacağı, IoT Hub 'ınıza nasıl bağlanacağı ve hub 'a gönderdiği bilgileri görüntülemek için Azure IoT gezgin aracının nasıl kullanılacağı gösterilmektedir. Örnek uygulama C dilinde yazılır ve Azure IoT Hub Device C SDK 'sına dahildir. Bir çözüm geliştiricisi, herhangi bir cihaz kodunu görüntülemeye gerek olmadan IoT Tak ve Kullan cihazının yeteneklerini anlamak için Azure IoT gezgin aracını kullanabilir.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Önkoşullar

Bu hızlı başlangıcı tamamlayabilmeniz için yerel makinenize aşağıdaki yazılımı yüklemeniz gerekir:

* [Visual Studio (Community, Professional veya Enterprise)](https://visualstudio.microsoft.com/downloads/) -Visual Studio 'yu yüklerken **NuGet Paket Yöneticisi** bileşenini ve iş yüküyle **Masaüstü geliştirmeyi C++**  eklediğinizden emin olun.
* [Git](https://git-scm.com/download/).
* [CMake](https://cmake.org/download/).

### <a name="install-the-azure-iot-explorer"></a>Azure IoT Gezginini yüklemeyi

En son güncelleştirme için "varlıklar" altında. msi dosyasını seçerek aracın [Depo](https://github.com/Azure/azure-iot-explorer/releases) sayfasından **Azure IoT Explorer** 'ın en son sürümünü indirin ve yükleyin.

[!INCLUDE [iot-pnp-prepare-iot-hub-windows.md](../../includes/iot-pnp-prepare-iot-hub-windows.md)]

## <a name="prepare-the-development-environment"></a>Geliştirme ortamını hazırlama

Bu hızlı başlangıçta, Azure IoT Hub cihaz C SDK 'sını klonlamak ve derlemek için kullanabileceğiniz bir geliştirme ortamı hazırlarsınız.

Seçtiğiniz dizinde bir komut istemi açın. [Azure IoT C SDK 'ları ve kitaplıkları](https://github.com/Azure/azure-iot-sdk-c) GitHub deposunu bu konuma kopyalamak için aşağıdaki komutu yürütün:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
```

Bu işlemin tamamlanması için birkaç dakika beklemeniz gerekebilir.

## <a name="build-the-code"></a>Kodu oluşturma

Dahil edilen örnek kodu oluşturmak için cihaz SDK 'sını kullanın. Oluşturduğunuz uygulama, IoT Hub 'ına bağlanan bir cihaza benzetir. Uygulama telemetri ve Özellikler gönderir ve komutları alır.

1. Cihaz SDK 'Sı kök klasöründe bir `cmake` alt dizini oluşturun ve bu klasöre gidin:

    ```cmd\sh
    cd <root folder>\azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. Cihaz SDK 'sını ve üretilen kod Saplaması oluşturmak için aşağıdaki komutları çalıştırın:

    ```cmd\sh
    cmake ..
    cmake --build . -- /m /p:Configuration=Release
    ```

    > [!NOTE]
    > CMake derleyicinizi C++ bulamazsa, önceki komutu çalıştırdığınızda derleme hataları alırsınız. Bu durumda, [Visual Studio komut isteminde](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs)bu komutu çalıştırmayı deneyin.

## <a name="run-the-device-sample"></a>Cihaz örneğini çalıştırma

IoT Hub 'ınıza telemetri gönderen bir IoT Tak ve Kullan cihazının benzetimini yapmak için SDK 'da örnek bir uygulama çalıştırın. Örnek uygulamayı çalıştırmak için, bu komutları kullanın ve _Cihaz bağlantı dizesini_ bir parametre olarak geçirin.

```cmd\sh
cd digitaltwin_client\samples\digitaltwin_sample_device\Release
copy ..\EnvironmentalSensor.interface.json .
digitaltwin_sample_device.exe "<YourDeviceConnectionString>"
```

Cihaz artık komutları ve özellik güncelleştirmelerini almaya hazır ve hub 'a telemetri verileri göndermeye başladı. Sonraki adımları tamamladıktan sonra örneği çalışır durumda tutun.

## <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Kodu doğrulamak için Azure IoT gezginini kullanın

1. Azure IoT Gezginini açın. **Uygulama yapılandırması** sayfasını görürsünüz.

1. _IoT Hub bağlantı dizenizi_ girin ve **Bağlan**' ı seçin.

1. Bağlandıktan sonra **cihazlara** genel bakış sayfasını görürsünüz.

1. Aracın, cihazınızdan arabirim modeli tanımlarını okuyacağına emin olmak için **Ayarlar**' ı seçin. Ayarlar menüsünde, **bağlı cihazdaki** Tak ve kullan yapılandırmalarda zaten görünebilir. değilse, **+ modül tanımı kaynağı Ekle** ' yi seçin ve ardından **bağlı cihazda** ekleyin.

1. **Cihazların** genel bakış sayfasına geri döndüğünüzde, daha önce oluşturduğunuz cihaz kimliğini bulun. Cihaz uygulaması komut isteminde çalışmaya devam ettiğinden, cihazın Azure IoT Explorer 'daki **bağlantı durumunun** _bağlı_ olarak raporlandığından emin olun (Aksi takdirde, olana kadar **Yenile** ' ye basın). Daha fazla ayrıntı görüntülemek için cihazı seçin.

1. Arabirim ve IoT Tak ve Kullan temelleri (özellikler, komutlar ve telemetri) göstermek için ID **urn: YOUR_COMPANY_NAME_HERE: Environmentalalgılayıcı: 1** olan arabirimi genişletin.

1. **Telemetri** sayfasını seçin ve cihazın gönderdiği telemetri verilerini görüntülemek için _Başlat_ 'a basın.

1. Cihaz tarafından raporlanan yazılabilir olmayan özellikleri görüntülemek için **Özellikler (yazılabilir değil)** sayfasını seçin.

1. Güncelleştirebilir yazılabilir özellikleri görüntülemek için **Özellikler (yazılabilir)** sayfasını seçin.

1. Özellik **adı**' nı genişletin, yeni bir adla güncelleştirin ve **yazılabilir özelliği Güncelleştir**' i seçin. 

1. **Bildirilen özellik** sütununda yeni adı göster ' i görmek için sayfanın üst kısmındaki **Yenile** düğmesini seçin.

1. Cihazın desteklediği tüm komutları görüntülemek için **Komutlar** sayfasını seçin.

1. **Yanıp sönen** komutunu genişletin ve yeni bir yanıp sönen zaman aralığı ayarlayın. Cihazda komutu çağırmak için **Gönder komutunu** seçin.

1. Komutların beklenen şekilde yürütüldüğünü doğrulamak için, sanal cihaz komut istemine gidin ve yazdırılmış onay iletilerini okuyun.

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta IoT Hub 'ına IoT Tak ve Kullan cihazını bağlamayı öğrendiniz. IoT Tak ve Kullan cihazlarınızla etkileşim kuran bir çözüm oluşturma hakkında daha fazla bilgi edinmek için bkz.:

> [!div class="nextstepaction"]
> [Nasıl yapılır: IoT Tak ve Kullan önizleme cihazına bağlanma ve bunlarla etkileşim kurma](howto-develop-solution.md)
