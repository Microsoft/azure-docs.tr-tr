---
title: Raspberry Pi C kullanarak Azure IOT hub'a bağlanma | Microsoft Docs
description: Ayarlama ve Raspberry Pi Raspberry Pi, Azure bulut platformuna veri göndermek için Azure IOT hub'a bağlanma hakkında bilgi edinin
author: wesmc7777
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 02/14/2019
ms.author: wesmc
ms.openlocfilehash: 94ac75c4165b11e343ce5c31480a511ebf978a36
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67838769"
---
# <a name="connect-raspberry-pi-to-azure-iot-hub-c"></a>Raspberry Pi için Azure IoT Hub (C) bağlanma

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Bu öğreticide, Raspbian çalıştıran Raspberry Pi çalışmanın temel bilgileri öğrenerek başlayın. Daha sonra kullanarak cihazlarınızı buluta sorunsuz bir şekilde bağlanmak nasıl öğrenin [Azure IOT hub'ı](about-iot-hub.md). Windows 10 IoT Core örnekleri için Git [Windows Dev Center](https://www.windowsondevices.com/).

Bir paket henüz yok mu? Deneyin [Raspberry Pi çevrimiçi simülatör](iot-hub-raspberry-pi-web-simulator-get-started.md). Veya yeni bir paket satın [burada](https://azure.microsoft.com/develop/iot/starter-kits).

## <a name="what-you-do"></a>Neler

* IOT hub oluşturun.

* Bir cihaz IOT hub'ına PI sayısı için kaydedin.

* Raspberry Pi ayarlayın.

* Pi sensör verilerini IOT hub'ınıza gönderilecek örnek uygulamayı çalıştırın.

Raspberry Pi'yi oluşturduğunuz IOT hub'a bağlayın. Ardından BME280 algılayıcıdan sıcaklık ve nem verileri toplamak için Pi üzerinde bir örnek uygulamayı çalıştırın. Son olarak, IOT hub'ınıza sensör verilerini gönderin.

## <a name="what-you-learn"></a>Öğrenecekleriniz

* Azure IOT hub oluşturma ve yeni cihaz bağlantı dizesini almak nasıl.

* Pi BME280 algılayıcısı ile bağlanma.

* Pi üzerinde bir örnek uygulamayı çalıştırarak algılayıcı verilerini toplamak nasıl.

* IOT hub'ınıza sensör verilerini gönderme işlemini.

## <a name="what-you-need"></a>Ne gerekiyor

![Ne gerekiyor](./media/iot-hub-raspberry-pi-kit-c-get-started/0-starter-kit.png)

* Raspberry Pi 2 veya Raspberry Pi 3 Pano.

* Etkin bir Azure aboneliği. Azure hesabınız yoksa, [ücretsiz Azure deneme hesabı oluşturma](https://azure.microsoft.com/free/) yalnızca birkaç dakika içinde.

* Bir izleyici, bir USB klavye ve fare Pi bağlanan.

* Bir Mac veya Windows veya Linux çalıştıran bir bilgisayar.

* Internet bağlantısı.

* 16 GB veya üzeri microSD kartı.

* İşletim sistemi görüntüsünü microSD kartı üzerine yazmak için bir USB SD bağdaştırıcı veya microSD kartı.

* 5-volt 2 amp power 6 ft mikro USB kablosu ile sağlayın.

Aşağıdaki öğeler isteğe bağlıdır:

* Bir birleştirilmiş Adafruit BME280 sıcaklık, baskısı ve nem algılayıcı.

* Bir breadboard.

* 6 F/dk anahtar kablolarına.

* Yayılmış 10 mm LED.

> [!NOTE]
> Kod örneği sanal sensör verilerini desteklediğinden, bu öğeler isteğe bağlıdır.
>

## <a name="create-an-iot-hub"></a>IoT hub oluşturma

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Yeni bir cihaz IOT hub'ı Kaydet

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="set-up-raspberry-pi"></a>Raspberry Pi ' ayarlayın

Raspberry Pi şimdi ayarlayın.

### <a name="install-the-raspbian-operating-system-for-pi"></a>PI sayısı için Raspbian işletim sistemini yükleme

MicroSD kartı Raspbian görüntüyü yüklemesi için hazırlayın.

1. Raspbian indirin.

   1. [Masaüstü ile Raspbian Esnetme indirme](https://www.raspberrypi.org/downloads/raspbian/) (.zip dosyasında).

   2. Raspbian görüntünün bilgisayarınızdaki bir klasöre ayıklayın.

2. Raspbian microSD kartı yükleyin.

   1. [Etcher SD kart yazıcı yardımcı programını yükleyip](https://etcher.io/).

   2. Etcher çalıştırın ve 1. adımda ayıkladığınız Raspbian görüntüsünü seçin.

   3. MicroSD kartı sürücüyü seçin. Etcher zaten doğru sürücü seçmiş olabilirsiniz olduğunu unutmayın.

   4. MicroSD kartı Raspbian yüklemek için Flash'ı tıklatın.

   5. Yükleme tamamlandığında microSD kartı bilgisayarınızdan kaldırın. Etcher otomatik olarak çıkarır veya tamamlandıktan sonra microSD kartı çıkarır çünkü microSD kartı doğrudan kaldırmak güvenlidir.

   6. MicroSD kartı PI yerleştirin.

### <a name="enable-ssh-and-spi"></a>SSH ve SPI etkinleştir

1. Pi monitör, klavye ve fare bağlayın, PI başlatın ve ardından kullanarak Raspbian için oturum açın `pi` kullanıcı adı ve `raspberry` parolası.
 
2. Raspberry simgesine tıklayın > **tercihleri** > **Raspberry Pi yapılandırma**.

   ![Raspbian tercihleri menüsü](./media/iot-hub-raspberry-pi-kit-c-get-started/1-raspbian-preferences-menu.png)

3. Üzerinde **arabirimleri** sekmesinde, belirleyin **SPI** ve **SSH** için **etkinleştirme**ve ardından **Tamam**. Yoksa ve fiziksel algılayıcınız varsa sanal sensör verilerini kullanmak istiyorsanız, bu adım isteğe bağlıdır.

   ![SPI ve Raspberry Pi üzerinde SSH'ı etkinleştir](./media/iot-hub-raspberry-pi-kit-c-get-started/2-enable-spi-ssh-on-raspberry-pi.png)

> [!NOTE]
> SSH ve SPI etkinleştirmek için daha fazla başvuru belgeleri üzerinde bulabilirsiniz [raspberrypi.org](https://www.raspberrypi.org/documentation/remote-access/ssh/) ve [RASPI-CONFIG](https://www.raspberrypi.org/documentation/configuration/raspi-config.md).
>

### <a name="connect-the-sensor-to-pi"></a>Pi algılayıcı bağlanma

Bir LED'i ve bir BME280 Pi gibi bağlanmak için breadboard ve anahtar kablo kullanın. Algılayıcı yoksa [bu bölümü atlayın](#connect-pi-to-the-network).

![Raspberry Pi ve algılayıcı bağlantı](./media/iot-hub-raspberry-pi-kit-c-get-started/3-raspberry-pi-sensor-connection.png)

BME280 algılayıcı sıcaklık ve nem veri toplayabilir. Ve varsa cihaz ve bulut arasındaki iletişimi LED yanıp sönme.

Algılayıcı sabitlemek için aşağıdaki bağlantı kullanın:

| Başlangıç (sensör ve LED)     | Bitiş (Pano)            | Kablo rengi   |
| -----------------------  | ---------------------- | ------------: |
| LED VDD (PIN 5G)         | GPIO 4 (Pin 7)         | Beyaz kablosu   |
| LED GND (PIN 6G)         | GND (PIN 6)            | Siyah kablo   |
| VDD (PIN 18F)            | 3.3V PWR (Pin 17)      | Beyaz kablosu   |
| GND (PIN 20F)            | GND (PIN 20)           | Siyah kablo   |
| SCK (PIN 21F)            | SPI0 SCLK (Pin 23)     | Turuncu kablosu  |
| SDO (PIN 22F)            | SPI0 MISO (PIN 21)     | Sarı kablosu  |
| SDI (Pin 23F)            | SPI0 MOSI (Pin 19)     | Yeşil kablosu   |
| CS (PIN 24F)             | SPI0 CS (PIN 24)       | Mavi kablosu    |

Görüntülemek için tıklayın [Raspberry Pi 2 ve 3 PIN eşlemeleri](https://developer.microsoft.com/windows/iot/docs/pinmappingsrpi) referans.

Raspberry Pi'yi BME280 başarıyla bağlandıktan sonra aşağıdaki resim gibi olmalıdır.

![Bağlı Pi ve BME280](./media/iot-hub-raspberry-pi-kit-c-get-started/4-connected-pi.png)

### <a name="connect-pi-to-the-network"></a>Pi ağa bağlanma

Pi üzerinde mikro USB kablosu ve güç kaynağı kullanarak etkinleştirin. Pi, kablolu ağa bağlanacak veya takip için Ethernet kablosu kullanın [yönergeleri Raspberry Pi Foundation'dan](https://www.raspberrypi.org/learning/software-guide/wifi/) Pi, kablosuz ağa bağlanmak için. Bir not gerek Pi'yi ağa başarıyla bağlandı sonra [Pi'yi IP adresini](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-3-network-setup/finding-your-pis-ip-address).

![Kablolu ağa bağlı](./media/iot-hub-raspberry-pi-kit-c-get-started/5-power-on-pi.png)

## <a name="run-a-sample-application-on-pi"></a>Pi üzerinde bir örnek uygulamayı çalıştırma

### <a name="sign-into-your-raspberry-pi"></a>Raspberry Pi'yi'nda oturum açın

1. Raspberry Pi'yi bağlanmak için ana bilgisayarınız aşağıdaki SSH istemcilerden birini kullanın.
   
   **Windows kullanıcıları**
   1. İndirme ve yükleme [PuTTY](https://www.putty.org/) Windows için. 
   1. IP adresini, ana bilgisayar adı (veya IP adresi) PI bölümüne kopyalayın ve SSH bağlantı türü olarak seçin.
   
   ![PuTTy](./media/iot-hub-raspberry-pi-kit-node-get-started/7-putty-windows.png)

   **Mac ve Ubuntu kullanıcıları**

   Yerleşik bir SSH istemcisi, Ubuntu veya Macos'ta kullanın. Çalıştırmanız gerekebilir `ssh pi@<ip address of pi>` Pi SSH aracılığıyla bağlanmak için.
   > [!NOTE]
   > Varsayılan kullanıcı adı `pi` , ve parola `raspberry`.


### <a name="configure-the-sample-application"></a>Örnek uygulamayı yapılandırma

1. Aşağıdaki komutu çalıştırarak örnek uygulamayı kopyalayın:

   ```bash
   sudo apt-get install git-core
   git clone https://github.com/Azure-Samples/iot-hub-c-raspberrypi-client-app.git
   ```

2. Kurulum betiğini çalıştırın:

   ```bash
   cd ./iot-hub-c-raspberrypi-client-app
   sudo chmod u+x setup.sh
   sudo ./setup.sh
   ```

   > [!NOTE] 
   > Varsa, **fiziksel BME280 yoksa**, kullanabilirsiniz '--simulated-data' sıcaklık ve nem verileri benzetimini yapmak için komut satırı parametresi olarak. `sudo ./setup.sh --simulated-data`
   >

### <a name="build-and-run-the-sample-application"></a>Örnek uygulaması derleme ve çalıştırma

1. Örnek uygulama, aşağıdaki komutu çalıştırarak oluşturun:

   ```bash
   cmake . && make
   ```
   
   ![Derleme çıkışı](./media/iot-hub-raspberry-pi-kit-c-get-started/7-build-output.png)

1. Aşağıdaki komutu çalıştırarak örnek uygulamayı çalıştırın:

   ```bash
   sudo ./app '<DEVICE CONNECTION STRING>'
   ```

   > [!NOTE] 
   > Kopyalama cihaz bağlantı dizesini tek tırnak yapıştırma emin olun.
   >

Algılayıcı verilerini ve IOT hub'ınıza gönderdiği iletileri gösterir aşağıdaki çıktıyı görmeniz gerekir.

![Çıkış - IOT hub'ınıza Raspberry Pi'dan gönderilen algılayıcı verileri](./media/iot-hub-raspberry-pi-kit-c-get-started/8-run-output.png)

## <a name="read-the-messages-received-by-your-hub"></a>Hub'ınıza tarafından alınan iletileri okuma

Cihazınızdan IOT hub tarafından alınan iletileri izlemeye yönelik bir yolu, Visual Studio Code için Azure IOT araçları kullanmaktır. Daha fazla bilgi için bkz. [göndermek ve IOT Hub ve cihaz arasında iletileri almak Visual Studio Code için Azure IOT Araçları](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

Cihazınız tarafından gönderilen verileri işlemek daha fazla yolu için açın sonraki bölüme devam edin.

## <a name="next-steps"></a>Sonraki adımlar

Algılayıcı verilerini toplamak ve IOT hub'ına göndermek için örnek bir uygulama çalıştırdınız.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
