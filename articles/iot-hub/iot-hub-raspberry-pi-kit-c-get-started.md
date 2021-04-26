---
title: C kullanarak Raspberry PI 'yi Azure IoT Hub bağlama | Microsoft Docs
description: Azure bulut platformuna veri göndermek üzere Raspberry Pi için Raspberry PI 'yi ayarlama ve Azure IoT Hub bağlama hakkında bilgi edinin
author: wesmc7777
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 02/14/2019
ms.author: wesmc
ms.custom:
- 'Role: Cloud Development'
ms.openlocfilehash: 52da3c28faa375f8b308e3fe78329fec4f996af9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97804063"
---
# <a name="connect-raspberry-pi-to-azure-iot-hub-c"></a>Raspberry PI 'yi Azure 'a bağlama IoT Hub (C)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Bu öğreticide, Raspbian çalıştıran Raspberry Pi ile çalışmanın temellerini öğrenerek başlarsınız. Daha sonra [Azure IoT Hub](about-iot-hub.md)kullanarak Cihazlarınızı buluta sorunsuzca nasıl bağlayacağınızı öğreneceksiniz. Windows 10 IoT çekirdek örnekleri için [Windows Geliştirme Merkezi](https://www.windowsondevices.com/)' ne gidin.

Henüz bir paketi yok mu? [Raspberry PI çevrimiçi simülatörü](iot-hub-raspberry-pi-web-simulator-get-started.md)deneyin. Veya [buradan](https://azure.microsoft.com/develop/iot/starter-kits)yeni bir paket satın alın.

## <a name="what-you-do"></a>Yapabilecekleriniz

* IoT Hub 'ı oluşturun.

* IoT Hub 'ınıza PI için bir cihaz kaydedin.

* Setup Raspberry PI.

* IoT Hub 'ınıza algılayıcı verileri göndermek için PI üzerinde örnek bir uygulama çalıştırın.

Raspberry PI 'yi oluşturduğunuz bir IoT Hub 'ına bağlayın. Daha sonra bir BME280 sensörden sıcaklık ve nem verisi toplamak için PI üzerinde örnek bir uygulama çalıştırırsınız. Son olarak, algılayıcı verilerini IoT Hub 'ınıza gönderirsiniz.

## <a name="what-you-learn"></a>Öğrenecekleriniz

* Azure IoT Hub 'ı oluşturma ve yeni cihaz Bağlantı dizenizi alma.

* BME280 algılayıcısı ile Pi bağlama.

* PI üzerinde örnek uygulama çalıştırarak algılayıcı verilerini toplama.

* IoT Hub 'ınıza algılayıcı verileri gönderme.

## <a name="what-you-need"></a>Gerekenler

![Gerekenler](./media/iot-hub-raspberry-pi-kit-c-get-started/0-starter-kit.png)

* Raspberry PI 2 veya Raspberry PI 3 panosu.

* Etkin bir Azure aboneliği. Azure hesabınız yoksa yalnızca birkaç dakika içinde [ücretsiz bir Azure deneme hesabı oluşturun](https://azure.microsoft.com/free/) .

* PI 'ye bağlanan bir izleyici, USB klavye ve fare.

* Windows veya Linux çalıştıran bir Mac veya bılgısayar.

* Bir Internet bağlantısı.

* Bir 16 GB veya mikro SD kart.

* İşletim sistemi görüntüsünü mikro SD kartına yazmak için bir USB-SD bağdaştırıcısı veya mikro SD kart.

* 6 foot mikro USB kablosuyla 5-volt 2-amp güç kaynağı.

Aşağıdaki öğeler isteğe bağlıdır:

* Birleştirilmiş bir Adameyve BME280 sıcaklık, basınç ve nem algılayıcısı.

* Bir enine Pano.

* 6 F/p atlatıcı kabloları.

* Bir diffon 10-mm LED 'i.

> [!NOTE]
> Kod örneği sanal algılayıcı verilerini desteklediğinden bu öğeler isteğe bağlıdır.
>

## <a name="create-an-iot-hub"></a>IoT hub oluşturma

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>IoT Hub 'a yeni bir cihaz kaydetme

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="set-up-raspberry-pi"></a>Raspberry PI 'yi ayarlama

Şimdi Raspberry PI 'yi ayarlayın.

### <a name="install-the-raspbian-operating-system-for-pi"></a>PI için Raspbian işletim sistemini yükler

Raspbian görüntüsünü yüklemek için mikro SD kartını hazırlayın.

1. Raspbian indirin.

   1. Desktop (. zip dosyası) [Ile Raspbian Esnetme indirin](https://www.raspberrypi.org/software/) .

   2. Raspbian görüntüsünü bilgisayarınızdaki bir klasöre ayıklayın.

2. Raspbian 'i mikro SD kartına yükler.

   1. [Etrahi SD kart yazıcı yardımcı programını indirip yükleyin](https://etcher.io/).

   2. Oyu çalıştırın ve adım 1 ' de ayıkladığınız Raspbian görüntüsünü seçin.

   3. Mikro SD kart sürücüsünü seçin. Ethma 'nın doğru sürücüyü zaten seçmiş olabileceğini unutmayın.

   4. Raspbian 'i mikro SD kartına yüklemek için Flash 'a tıklayın.

   5. Yükleme tamamlandığında mikro SD kartını bilgisayarınızdan kaldırın. Mikro SD kartını doğrudan kaldırmak güvenlidir, çünkü etma, mikro SD kartını tamamladıktan sonra otomatik olarak çıkarır ya da çıkarır.

   6. Mikro SD kartını PI içine ekleyin.

### <a name="enable-ssh-and-spi"></a>SSH ve SPI 'yı etkinleştirme

1. PI 'yi monitöre, klavye ve fareye bağlayın, PI 'yi başlatın ve `pi` Kullanıcı adı ve parola olarak kullanarak Raspbian 'de oturum açın `raspberry` .
 
2. **Preferences**  >  **Raspberry PI Configuration**> Raspberry simgesine tıklayın.

   ![Raspbian Tercihler menüsü](./media/iot-hub-raspberry-pi-kit-c-get-started/1-raspbian-preferences-menu.png)

3. **Arabirimler** sekmesinde, **SPI** ve **SSH** 'Yi **etkinleştirmek** için ayarlayın ve ardından **Tamam**' a tıklayın. Fiziksel sensörler yoksa ve sanal algılayıcı verileri kullanmak istiyorsanız, bu adım isteğe bağlıdır.

   ![Raspberry PI üzerinde SPI ve SSH 'yi etkinleştirme](./media/iot-hub-raspberry-pi-kit-c-get-started/2-enable-spi-ssh-on-raspberry-pi.png)

> [!NOTE]
> SSH ve SPI 'yı etkinleştirmek için, [raspberrypi.org](https://www.raspberrypi.org/documentation/remote-access/ssh/) ve [PSPı-CONFIG](https://www.raspberrypi.org/documentation/configuration/raspi-config.md)üzerinde daha fazla başvuru belgesi bulabilirsiniz.
>

### <a name="connect-the-sensor-to-pi"></a>Algılayıcıyı Pi 'ye bağlama

Bir LED ve BME280 ile Pi arasında bağlantı kurmak için aşağıdaki gibi bir enine ve atlatıcı kablolarını kullanın. Sensöre sahip değilseniz, [Bu bölümü atlayın](#connect-pi-to-the-network).

![Raspberry PI ve algılayıcı bağlantısı](./media/iot-hub-raspberry-pi-kit-c-get-started/3-raspberry-pi-sensor-connection.png)

BME280 algılayıcısı, sıcaklık ve nem verileri toplayabilir. Ve cihaz ile bulut arasında bir iletişim varsa LED 'in yanıp sönmesi gerekir.

Algılayıcı PIN 'leri için aşağıdaki kabloyı kullanın:

| Başlat (algılayıcı & LED)     | Bitiş (Pano)            | Kablo rengi   |
| -----------------------  | ---------------------- | ------------: |
| LED VDD (PIN 5G)         | GPıO 4 (PIN 7)         | Beyaz kablo   |
| LED 'in (Iğne 6G)         | Iğne (pin 6)            | Siyah kablo   |
| VDD (PIN 18F)            | 3.3 v PWR (PIN 17)      | Beyaz kablo   |
| Iğne (Iğne 20F)            | Iğne (PIN 20)           | Siyah kablo   |
| SCK (pin 21F)            | SPI0 SCLK (23 pin)     | Turuncu kablo  |
| SDO (Iğne 22F)            | SPI0 MISO (PIN 21)     | Sarı kablo  |
| SDI (Iğne 23F)            | SPI0 MOSI (PIN 19)     | Yeşil kablo   |
| CS (PIN 24F)             | SPI0 CS (PIN 24)       | Mavi kablo    |

Başvurunuz için [Raspberry PI 2 & 3 PIN eşlemelerini](/windows/iot-core/learn-about-hardware/pinmappings/pinmappingsrpi) görüntülemek için tıklayın.

BME280, Raspberry Pi 'nize başarıyla bağlandıktan sonra görüntünün altında olması gerekir.

![Bağlı Pi ve BME280](./media/iot-hub-raspberry-pi-kit-c-get-started/4-connected-pi.png)

### <a name="connect-pi-to-the-network"></a>PI 'yi ağa bağlama

Mikro USB kablosunu ve güç kaynağını kullanarak PI 'yi açın. PI 'yi kablolu ağınıza bağlamak için Ethernet kablosunu kullanın veya Pi 'yi kablosuz ağınıza bağlamak için [Raspberry PI Foundation içindeki yönergeleri](https://www.raspberrypi.org/documentation/configuration/wireless/) izleyin. PI 'niz ağa başarıyla bağlandıktan sonra, [PI 'Nizin IP adresini](https://www.raspberrypi.org/documentation/remote-access/ip-address.md)bir yere getirmeniz gerekir.

![Kablolu ağa bağlanıldı](./media/iot-hub-raspberry-pi-kit-c-get-started/5-power-on-pi.png)

## <a name="run-a-sample-application-on-pi"></a>PI üzerinde örnek uygulama çalıştırma

### <a name="sign-into-your-raspberry-pi"></a>Raspberry PI 'larınızın oturumunu açın

1. Raspberry PI 'nize bağlanmak için konak bilgisayarınızdan aşağıdaki SSH istemcilerinden birini kullanın.
   
   **Windows kullanıcıları**
   1. Windows için [Putty](https://www.putty.org/) indirin ve yükleyin. 
   1. PI 'nizin IP adresini ana bilgisayar adı (veya IP adresi) bölümüne kopyalayın ve bağlantı türü olarak SSH ' yi seçin.
   
   ![PuTTy](./media/iot-hub-raspberry-pi-kit-node-get-started/7-putty-windows.png)

   **Mac ve Ubuntu kullanıcıları**

   Ubuntu veya macOS üzerinde yerleşik SSH istemcisini kullanın. `ssh pi@<ip address of pi>`PI 'YI SSH aracılığıyla bağlamak için çalıştırmanız gerekebilir.
   > [!NOTE]
   > Varsayılan Kullanıcı adı `pi` ve parola ' dır `raspberry` .


### <a name="configure-the-sample-application"></a>Örnek uygulamayı yapılandırma

1. Aşağıdaki komutu çalıştırarak örnek uygulamayı kopyalayın:

   ```bash
   sudo apt-get install git-core
   git clone https://github.com/Azure-Samples/iot-hub-c-raspberrypi-client-app.git
   ```

2. Kurulum betiğini Çalıştır:

   ```bash
   cd ./iot-hub-c-raspberrypi-client-app
   sudo chmod u+x setup.sh
   sudo ./setup.sh
   ```

   > [!NOTE] 
   > **Fiziksel BIR BME280** yoksa, sıcaklık&nem verilerinin benzetimini yapmak için komut satırı parametresi olarak '--benzetimli-Data ' kullanabilirsiniz. `sudo ./setup.sh --simulated-data`
   >

### <a name="build-and-run-the-sample-application"></a>Örnek uygulamayı derleme ve çalıştırma

1. Aşağıdaki komutu çalıştırarak örnek uygulamayı oluşturun:

   ```bash
   cmake . && make
   ```
   
   ![Derleme çıkışı](./media/iot-hub-raspberry-pi-kit-c-get-started/7-build-output.png)

1. Aşağıdaki komutu çalıştırarak örnek uygulamayı çalıştırın:

   ```bash
   sudo ./app '<DEVICE CONNECTION STRING>'
   ```

   > [!NOTE] 
   > Cihazın bağlantı dizesini tek tırnak içine yapıştırdığınızdan emin olun.
   >

Sensör verilerinin ve IoT hub’ınıza gönderilen iletilerin gösterildiği aşağıdaki çıkışı görmelisiniz.

![Çıkış - Raspberry Pi'den IoT hub'ınıza gönderilen sensör verileri](./media/iot-hub-raspberry-pi-kit-c-get-started/8-run-output.png)

## <a name="read-the-messages-received-by-your-hub"></a>Hub 'ınız tarafından alınan iletileri okuyun

Cihazınızdan IoT Hub 'ınız tarafından alınan iletileri izlemenin bir yolu, Visual Studio Code için Azure IoT araçlarını kullanmaktır. Daha fazla bilgi edinmek için bkz. [Visual Studio Code Için Azure IoT araçlarını kullanarak cihazınız ve IoT Hub arasında ileti gönderme ve alma](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

Cihazınız tarafından gönderilen verileri daha fazla şekilde işlemek için bir sonraki bölüme geçin.

## <a name="next-steps"></a>Sonraki adımlar

Algılayıcı verilerini toplamak ve IoT Hub 'ınıza göndermek için örnek bir uygulama çalıştırdık.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]