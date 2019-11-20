---
title: Raspberry PI 'yi Bulutta Azure IoT Hub bağlama (node. js)
description: Bu öğreticide Azure Cloud Platform 'a veri göndermek üzere Raspberry PI 'yi ayarlamayı ve Azure IoT Hub için Azure 'a bağlamayı öğrenin.
author: wesmc7777
manager: philmea
keywords: Azure IoT Raspberry PI, Raspberry PI IoT Hub, Raspberry PI buluta veri gönderme, Raspberry Pi-buluta
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: wesmc
ms.openlocfilehash: 28897ca6f80632210032b7f800404fc921143adf
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73954552"
---
# <a name="connect-raspberry-pi-to-azure-iot-hub-nodejs"></a>Raspberry PI 'yi Azure IoT Hub 'ye bağlama (node. js)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Bu öğreticide, Raspbian çalıştıran Raspberry Pi ile çalışmanın temellerini öğrenerek başlarsınız. Daha sonra [Azure IoT Hub](about-iot-hub.md)kullanarak Cihazlarınızı buluta sorunsuzca nasıl bağlayacağınızı öğreneceksiniz. Windows 10 IoT çekirdek örnekleri için [Windows Geliştirme Merkezi](https://www.windowsondevices.com/)' ne gidin.

Henüz bir paketi yok mu? [Raspberry PI çevrimiçi simülatörü](iot-hub-raspberry-pi-web-simulator-get-started.md)deneyin. Veya [buradan](https://azure.microsoft.com/develop/iot/starter-kits)yeni bir paket satın alın.

## <a name="what-you-do"></a>Yapabilecekleriniz

* IoT Hub 'ı oluşturun.

* IoT Hub 'ınıza PI için bir cihaz kaydedin.

* Raspberry PI 'yi ayarlayın.

* IoT Hub 'ınıza algılayıcı verileri göndermek için PI üzerinde örnek bir uygulama çalıştırın.

## <a name="what-you-learn"></a>Öğrenecekleriniz

* Azure IoT Hub 'ı oluşturma ve yeni cihaz Bağlantı dizenizi alma.

* BME280 algılayıcısı ile Pi bağlama.

* PI üzerinde örnek uygulama çalıştırarak algılayıcı verilerini toplama.

* IoT Hub 'ınıza algılayıcı verileri gönderme.

## <a name="what-you-need"></a>Ne gerekiyor

![Ne gerekiyor](./media/iot-hub-raspberry-pi-kit-node-get-started/0-starter-kit.png)

* Bir Raspberry PI 2 veya Raspberry PI 3 panosu.

* Azure aboneliği. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

* Pi 'ye bağlanan bir izleyici, USB klavye ve fare.

* Windows veya Linux çalıştıran bir Mac veya bılgısayar.

* Bir internet bağlantısı.

* Bir 16 GB veya mikro SD kart.

* İşletim sistemi görüntüsünü mikro SD kartına yazmak için bir USB-SD bağdaştırıcısı veya mikro SD kart.

* 6 foot mikro USB kablosuyla 5-volt 2-amp güç kaynağı.

Aşağıdaki öğeler isteğe bağlıdır:

* Birleştirilmiş bir Adameyve BME280 sıcaklık, basınç ve nem algılayıcısı.

* Bir enine Pano.

* 6 F/p atlatıcı kabloları.

* Bir diffon 10-mm LED 'i.

> [!NOTE]
> İsteğe bağlı öğeleriniz yoksa, sanal algılayıcı verilerini kullanabilirsiniz.

## <a name="create-an-iot-hub"></a>IoT hub oluşturma

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>IoT Hub 'a yeni bir cihaz kaydetme

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="set-up-raspberry-pi"></a>Raspberry PI 'yi ayarlama

### <a name="install-the-raspbian-operating-system-for-pi"></a>PI için Raspbian işletim sistemini yükler

Raspbian görüntüsünü yüklemek için mikro SD kartını hazırlayın.

1. Raspbian indirin.

   a. [Masaüstü Ile Raspbian Buster](https://www.raspberrypi.org/downloads/raspbian/) (. zip dosyası).

   b. Raspbian görüntüsünü bilgisayarınızdaki bir klasöre ayıklayın.

2. Raspbian 'i mikro SD kartına yükler.

   a. [Etrahi SD kart yazıcı yardımcı programını indirip yükleyin](https://etcher.io/).

   b. Oyu çalıştırın ve adım 1 ' de ayıkladığınız Raspbian görüntüsünü seçin.

   c. Mikro SD kart sürücüsünü seçin. Oyma, doğru sürücüyü zaten seçmiş olabilir.

   d. Raspbian 'i mikro SD kartına yüklemek için Flash 'a tıklayın.

   e. Yükleme tamamlandığında mikro SD kartını bilgisayarınızdan kaldırın. Mikro SD kartını doğrudan kaldırmak güvenlidir, çünkü etma, mikro SD kartını tamamladıktan sonra otomatik olarak çıkarır ya da çıkarır.

   f. Mikro SD kartını PI içine ekleyin.

### <a name="enable-ssh-and-i2c"></a>SSH ve ı2C 'yi etkinleştir

1. PI 'yi monitöre, klavyeye ve fareye bağlayın.

2. PI 'yi başlatın ve Kullanıcı adı olarak `pi` ve parola olarak `raspberry` kullanarak Raspbian 'de oturum açın.

3. **Tercihler** > **Raspberry PI Configuration**> Raspberry simgesine tıklayın.

   ![Raspbian Tercihler menüsü](./media/iot-hub-raspberry-pi-kit-node-get-started/1-raspbian-preferences-menu.png)

4. **Arabirimler** sekmesinde, **I2C** ve **SSH** 'Yi **etkinleştirmek**için ayarlayın ve ardından **Tamam**' a tıklayın. Fiziksel sensörler yoksa ve sanal algılayıcı verileri kullanmak istiyorsanız, bu adım isteğe bağlıdır.

   ![Raspberry PI üzerinde ı2C ve SSH 'yi etkinleştirme](./media/iot-hub-raspberry-pi-kit-node-get-started/2-enable-i2c-ssh-on-raspberry-pi.png)

> [!NOTE]
> SSH ve ı2C 'yı etkinleştirmek için, [raspberrypi.org](https://www.raspberrypi.org/documentation/remote-access/ssh/) ve [Adafruit.com](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-4-gpio-setup/configuring-i2c)üzerinde daha fazla başvuru belgesi bulabilirsiniz.

### <a name="connect-the-sensor-to-pi"></a>Algılayıcıyı Pi 'ye bağlama

Bir LED ve BME280 ile Pi arasında bağlantı kurmak için aşağıdaki gibi bir enine ve atlatıcı kablolarını kullanın. Sensöre sahip değilseniz, [Bu bölümü atlayın](#connect-pi-to-the-network).

![Raspberry PI ve algılayıcı bağlantısı](./media/iot-hub-raspberry-pi-kit-node-get-started/3-raspberry-pi-sensor-connection.png)

BME280 algılayıcısı, sıcaklık ve nem verileri toplayabilir. Cihaz buluta bir ileti gönderdiğinde bu, yanıp sönüyor.

Algılayıcı PIN 'leri için aşağıdaki kabloyı kullanın:

| Başlat (algılayıcı & LED)     | Bitiş (Pano)            | Kablo rengi   |
| -----------------------  | ---------------------- | ------------: |
| VDD (PIN 5G)             | 3.3 v PWR (pin 1)       | Beyaz kablo   |
| Iğne (PIN 7G)             | Iğne (pin 6)            | Kahverengi kablo   |
| SDI (PIN 10G)            | I2C1 SDA (PIN 3)       | Kırmızı kablo     |
| SCK (Iğne 8G)             | I2C1 SCL (PIN 5)       | Turuncu kablo  |
| LED VDD (PIN 18F)        | GPıO 24 (PIN 18)       | Beyaz kablo   |
| LED arka plan (Iğne 17F)        | Iğne (PIN 20)           | Siyah kablo   |

Başvurunuz için [Raspberry PI 2 & 3 PIN eşlemelerini](https://developer.microsoft.com/windows/iot/docs/pinmappingsrpi) görüntülemek için tıklayın.

BME280, Raspberry Pi 'nize başarıyla bağlandıktan sonra görüntünün altında olması gerekir.

![Bağlı Pi ve BME280](./media/iot-hub-raspberry-pi-kit-node-get-started/4-connected-pi.png)

### <a name="connect-pi-to-the-network"></a>PI 'yi ağa bağlama

Mikro USB kablosunu ve güç kaynağını kullanarak PI 'yi açın. PI 'yi kablolu ağınıza bağlamak için Ethernet kablosunu kullanın veya Pi 'yi kablosuz ağınıza bağlamak için [Raspberry PI Foundation içindeki yönergeleri](https://www.raspberrypi.org/learning/software-guide/wifi/) izleyin. PI 'niz ağa başarıyla bağlandıktan sonra, [PI 'Nizin IP adresini](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-3-network-setup/finding-your-pis-ip-address)bir yere getirmeniz gerekir.

![Kablolu ağa bağlanıldı](./media/iot-hub-raspberry-pi-kit-node-get-started/5-power-on-pi.png)

> [!NOTE]
> PI 'nin bilgisayarınızla aynı ağa bağlı olduğundan emin olun. Örneğin, bilgisayarınız, PI bir kablolu ağa bağlıyken kablosuz ağa bağlıysa, devdisco çıktısında IP adresini göremeyebilirsiniz.

## <a name="run-a-sample-application-on-pi"></a>PI üzerinde örnek uygulama çalıştırma

### <a name="clone-sample-application-and-install-the-prerequisite-packages"></a>Örnek uygulamayı kopyalayın ve önkoşul paketlerini yükler

1. Ana bilgisayarınızdan aşağıdaki SSH istemcilerinden biriyle Raspberry Pi 'nize bağlanın:

   **Windows kullanıcıları**

   a. Windows için [Putty](https://www.putty.org/) indirin ve yükleyin.

   b. PI 'nizin IP adresini ana bilgisayar adı (veya IP adresi) bölümüne kopyalayın ve bağlantı türü olarak SSH ' yi seçin.

   ![PuTTy](./media/iot-hub-raspberry-pi-kit-node-get-started/7-putty-windows.png)

   **Mac ve Ubuntu kullanıcıları**

   Ubuntu veya macOS üzerinde yerleşik SSH istemcisini kullanın. PI 'yi SSH aracılığıyla bağlamak için `ssh pi@<ip address of pi>` çalıştırmanız gerekebilir.

   > [!NOTE]
   > Varsayılan Kullanıcı adı `pi` ve parola `raspberry`.

2. Node. js ve NPM 'yi PI 'nize yükler.

   Önce Node. js sürümünüzü kontrol edin.

   ```bash
   node -v
   ```

   Sürüm 10. x 'den düşükse veya PI 'ağınızda Node. js yoksa, en son sürümü yükler.

   ```bash
   curl -sL https://deb.nodesource.com/setup_10.x | sudo -E bash
   sudo apt-get -y install nodejs
   ```

3. Örnek uygulamayı kopyalayın.

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-node-raspberrypi-client-app
   ```

4. Örnek için tüm paketleri yükler. Yükleme, Azure IoT cihaz SDK 'Sı, BME280 algılayıcı kitaplığı ve kablo kitaplığı 'nı içerir.

   ```bash
   cd iot-hub-node-raspberrypi-client-app
   npm install
   ```

   > [!NOTE]
   >Ağ bağlantınıza bağlı olarak, bu yükleme işleminin tamamlanması birkaç dakika sürebilir.

### <a name="configure-the-sample-application"></a>Örnek uygulamayı yapılandırma

1. Aşağıdaki komutları çalıştırarak yapılandırma dosyasını açın:

   ```bash
   nano config.json
   ```

   ![Yapılandırma dosyası](./media/iot-hub-raspberry-pi-kit-node-get-started/6-config-file.png)

   Bu dosyada yapılandırabileceğiniz iki öğe vardır. Birincisi, buluta gönderilen iletiler arasındaki zaman aralığını (milisaniye olarak) tanımlayan `interval`. İkinci tane, sanal algılayıcı verilerinin kullanılıp kullanılmayacağını belirten bir Boole değeri olan `simulatedData`.

   **Sensöre sahip**değilseniz, örnek uygulamanın benzetimli algılayıcı verilerini oluşturup kullanmasını sağlamak için `simulatedData` değerini `true` olarak ayarlayın.

   *Note: Bu öğreticide kullanılan I2C adresi varsayılan olarak 0x77 ' dir. Yapılandırmanıza bağlı olarak, 0x76 de olabilir: bir I2C hatasıyla karşılaşırsanız, değeri 118 olarak değiştirmeyi deneyin ve bunun daha iyi çalışıp çalışmadığını görüntüleyin. Sensörizin tarafından kullanılan adresi görmek için, Raspberry Pi üzerinde bir kabukta `sudo i2cdetect -y 1` çalıştırın*

2. Control-O > yazarak kaydedip çıkış yapın ve > Control-X yazın.

### <a name="run-the-sample-application"></a>Örnek uygulamayı çalıştırın

Aşağıdaki komutu çalıştırarak örnek uygulamayı çalıştırın:

   ```bash
   sudo node index.js '<YOUR AZURE IOT HUB DEVICE CONNECTION STRING>'
   ```

   > [!NOTE]
   > Cihazın bağlantı dizesini tek tırnak içine yapıştırdığınızdan emin olun.

Algılayıcı verilerini ve IoT Hub 'ınıza gönderilen iletileri gösteren aşağıdaki çıktıyı görmeniz gerekir.

![Raspberry Pi 'den IoT Hub 'ınıza gönderilen çıkış algılayıcı verileri](./media/iot-hub-raspberry-pi-kit-node-get-started/8-run-output.png)

## <a name="read-the-messages-received-by-your-hub"></a>Hub 'ınız tarafından alınan iletileri okuyun

Cihazınızdan IoT Hub 'ınız tarafından alınan iletileri izlemenin bir yolu, Visual Studio Code için Azure IoT araçlarını kullanmaktır. Daha fazla bilgi edinmek için bkz. [Visual Studio Code Için Azure IoT araçlarını kullanarak cihazınız ve IoT Hub arasında ileti gönderme ve alma](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

Cihazınız tarafından gönderilen verileri daha fazla şekilde işlemek için bir sonraki bölüme geçin.

## <a name="next-steps"></a>Sonraki adımlar

Algılayıcı verilerini toplamak ve IoT Hub 'ınıza göndermek için örnek bir uygulama çalıştırdık.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
