---
title: IoT DevKit AZ3166 'i Azure IoT Hub bağlama
description: Bu öğreticide IoT DevKit AZ3166 'i ayarlamayı ve Azure bulut platformuna veri gönderebilmesi için Azure IoT Hub 'a bağlamayı öğrenin.
author: wesmc7777
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 06/25/2019
ms.author: wesmc
ms.custom:
- mqtt
- 'Role: Cloud Development'
- devx-track-azurecli
ms.openlocfilehash: 3e5ab1667ee0cda459785efa624bd7f4fc6818b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97562965"
---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub"></a>IoT DevKit AZ3166 'i Azure 'a bağlama IoT Hub

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Microsoft Azure hizmetlerinden faydalanan Nesnelerin İnterneti (IoT) çözümleri geliştirmek ve prototip oluşturmak için [Mxyonga IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) ' i kullanabilirsiniz. Zengin çevre birimleri ve algılayıcıları, açık kaynaklı bir pano paketi ve zengin bir [örnek Galerisi](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/)Içeren bir Arduino ile uyumlu bir Pano içerir.

## <a name="what-you-learn"></a>Öğrenecekleriniz

* Bir IoT Hub 'ı oluşturma ve Mxyonga IoT DevKit için bir cihaz kaydetme.
* IoT DevKit 'i Wi-Fi için bağlama ve IoT Hub bağlantı dizesini yapılandırma.
* DevKit algılayıcı telemetri verilerini IoT Hub 'ınıza gönderme.
* Geliştirme ortamını hazırlama ve IoT DevKit için uygulama geliştirme.

Henüz bir DevKit yok mu? [Devkit simülatörünü](https://azure-samples.github.io/iot-devkit-web-simulator/) deneyin veya [bir devkit satın alın](https://aka.ms/iot-devkit-purchase).

[Kod örnekleri galerisindeki](/samples/browse/?term=mxchip)tüm devkit öğreticileri için kaynak kodu bulabilirsiniz.

## <a name="what-you-need"></a>Gerekenler

- Mikro USB kablosuyla bir Mxyongaıot DevKit panosu. [Hemen alın](https://aka.ms/iot-devkit-purchase).
- Windows 10, macOS 10.10 + veya Ubuntu 18.04 + çalıştıran bir bilgisayar.
- Etkin bir Azure aboneliği. [30 günlük ücretsiz deneme Microsoft Azure hesabını etkinleştirin](https://azureinfo.microsoft.com/us-freetrial.html).
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]
  
## <a name="prepare-your-hardware"></a>Donanımınızı hazırlama

Aşağıdaki donanımı bilgisayarınıza bağlayın:

* DevKit panosu
* Mikro USB kablosu

![Gerekli donanım](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/hardware.jpg)

DevKit 'i bilgisayarınıza bağlamak için şu adımları izleyin:

1. USB ucunu bilgisayarınıza bağlayın.

2. Mikro USB ucunu DevKit’e bağlayın.

3. Güç için yeşil ışığı bağlantıyı onaylar.

   ![Donanım bağlantıları](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect.jpg)

## <a name="quickstart-send-telemetry-from-devkit-to-an-iot-hub"></a>Hızlı başlangıç: DevKit 'ten bir IoT Hub telemetri gönderin

Hızlı başlangıç, telemetrinin IoT Hub göndermek için önceden derlenmiş DevKit bellenimini kullanır. Çalıştırmadan önce bir IoT Hub oluşturun ve bir cihazı hub 'a kaydedersiniz.

### <a name="create-an-iot-hub"></a>IoT hub oluşturma

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="register-a-device"></a>Cihaz kaydetme

Bir cihazın bağlanabilmesi için IoT hub’ınıza kaydedilmesi gerekir. Bu hızlı başlangıçta Azure Cloud Shell kullanarak bir simülasyon cihazı kaydedeceksiniz.

1. Cihaz kimliğini oluşturmak için Azure Cloud Shell aşağıdaki komutu çalıştırın.

   **Youriothubname**: Bu yer tutucuyu, IoT Hub 'ınız için seçtiğiniz adla değiştirin.

   **Mynodedevice**: kaydetmekte olduğunuz cihazın adı. Gösterildiği gibi **Mynodedevice** kullanın. Cihazınız için farklı bir ad seçerseniz bu adı bu makale boyunca kullanmanız ve örnek uygulamalarda cihaz adını çalıştırmadan önce güncelleştirmeniz gerekir.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyNodeDevice
    ```

   > [!NOTE]
   > Çalışırken bir hata alırsanız `device-identity` [Azure CLI Için Azure IoT uzantısını](https://github.com/Azure/azure-iot-cli-extension/blob/dev/README.md)yükleyebilirsiniz.
   > Azure CLı için Microsoft Azure IoT uzantısını Cloud Shell örneğinize eklemek için aşağıdaki komutu çalıştırın. IoT uzantısı, Azure CLı 'ye IoT Hub, IoT Edge ve IoT cihaz sağlama hizmeti 'ne (DPS) özgü komutlar ekler.
   > 
   > ```azurecli-interactive
   > az extension add --name azure-iot
   >  ```
   >
  
1. Yeni kaydettiğiniz cihazın _cihaz bağlantı dizesini_ almak için aşağıdaki komutları Azure Cloud Shell'de çalıştırın:

   **Youriothubname**: Bu yer tutucuyu, IoT Hub 'ınız için seçtiğiniz adla değiştirin.

    ```azurecli-interactive
    az iot hub device-identity connection-string show --hub-name YourIoTHubName --device-id MyNodeDevice --output table
    ```

    Aşağıdakine benzeyen cihaz bağlantı dizenizi not alın:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Bu değeri hızlı başlangıcın ilerleyen bölümlerinde kullanacaksınız.

### <a name="send-devkit-telemetry"></a>DevKit telemetrisini gönder

DevKit, IoT Hub 'ınızdaki cihaza özgü bir uç noktaya bağlanır ve sıcaklık ve nem telemetrisini gönderir.

1. IoT DevKit için [getstarted üretici yazılımının](https://aka.ms/devkit/prod/getstarted/latest) en son sürümünü indirin.

1. IoT DevKit 'in bilgisayarınıza USB üzerinden bağlanmasını sağlayın. Dosya Gezgini 'ni açın **AZ3166** ADLı bir USB yığın depolama cihazı vardır.

    ![Windows Gezgini 'ni aç](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/az3166-usb.png)

1. Üretici yazılımını sürükleyip bırakın ve doğrudan yığın depolama cihazına indirilir. Bu işlem, otomatik olarak yanıp sönecektir.

    ![Üretici yazılımını kopyalama](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/copy-firmware.png)

1. DevKit üzerinde, düğme **b** tuşunu basılı tutarak **sıfırlama** düğmesini gönderin ve serbest bırakın ve sonra **B** düğmesini bırakın. DevKit, AP moduna girer. Doğrulamak için, ekranda DevKit ve Configuration Portal IP adresinin hizmet kümesi tanımlayıcısını (SSID) görüntülenir.

    ![Sıfırla düğmesi, düğme B ve SSID](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/wifi-ap.jpg)

    ![AP modunu ayarla](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/set-ap-mode.gif)

1. Önceki adımda gösterilen IoT DevKit SSID’ye bağlanmak için farklı bir Wi-Fi etkin cihazda (bilgisayar veya cep telefonu) bir web tarayıcısı kullanın. Parola isterse, boş bırakın.

    ![Bağlantı SSID 'SI](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/connect-ssid.png)

1. Tarayıcıda **192.168.0.1** 'yi açın. IoT DevKit 'in bağlanmasını istediğiniz Wi-Fi seçin, Wi-Fi parolasını yazın, ardından daha önce notettiğiniz cihaz bağlantı dizesini yapıştırın. Daha sonra Kaydet'e tıklayın.

    ![Yapılandırma Kullanıcı arabirimi](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/configuration-ui.png)

    > [!NOTE]
    > IoT DevKit yalnızca 2,4 GHz ağı destekler. Daha fazla ayrıntı için [SSS bölümüne](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#wi-fi-configuration) bakın.

1. Sonuç sayfasını gördüğünüzde, WiFi bilgileri ve cihaz bağlantı dizesi IoT DevKit ' de depolanır.

    ![Yapılandırma sonucu](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/configuration-ui-result.png)

    > [!NOTE]
    > Wi-Fi yapılandırıldıktan sonra, cihaz sökülse bile, kimlik bilgileriniz Bu bağlantı için cihazda kalır.

1. IoT DevKit birkaç saniye içinde yeniden başlatılır. DevKit ekranında, telekit 'in IP adresini, sıcaklık ve nem değeri de dahil olmak üzere, Azure IoT Hub ileti sayısı ile birlikte aşağıdaki şekilde görürsünüz.

    ![WiFi IP 'si](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/wifi-ip.jpg)

    ![Veriler gönderiliyor](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/sending-data.jpg)

1. Azure 'a gönderilen telemetri verilerini doğrulamak için Azure Cloud Shell ' de aşağıdaki komutu çalıştırın:

    ```azurecli
    az iot hub monitor-events --hub-name YourIoTHubName --output table
    ```

## <a name="prepare-the-development-environment"></a>Geliştirme ortamını hazırlama

DevKit için geliştirme ortamını hazırlamak üzere aşağıdaki adımları izleyin:

### <a name="install-visual-studio-code-with-azure-iot-tools-extension-package"></a>Azure IoT araçları uzantı paketiyle Visual Studio Code 'yi yükler

1. [Arduino IDE](https://www.arduino.cc/en/Main/Software)'yi yükler. Arduino kodunu derlemek ve karşıya yüklemek için gerekli araç zincirini sağlar.
    * **Windows**: Windows Installer sürümünü kullanın. Uygulama mağazasından yüklemeyin.
    * **MacOS**: ayıklanan **Arduino.app** klasörüne sürükleyip bırakın `/Applications` .
    * **Ubuntu**: gibi klasöre sıkıştırmayı açın `$HOME/Downloads/arduino-1.8.8`

2. Güçlü IntelliSense ile platformlar arası kaynak kodu Düzenleyicisi, kod tamamlama ve hata ayıklama desteği, Market 'ten de zengin uzantılar yüklenebilir [Visual Studio Code](https://code.visualstudio.com/)yükleme.

3. VS Code başlatın, uzantı marketi 'nde **Arduino** için arama yapın ve yüklemeyi yapın. Bu uzantı, Arduino platformunda geliştirme için gelişmiş deneyimler sağlar.

    ![Arduino 'yi yükler](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-arduino.png)

4. Uzantı marketi ' nde [Azure IoT araçları](https://aka.ms/azure-iot-tools) ' nı bulun ve yüklemeyi yapın.

    ![Uzantı marketi 'nde Azure IoT araçları 'nı gösteren ekran görüntüsü.](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-azure-iot-tools.png)

    Veya bu URL 'YI kopyalayıp bir tarayıcı penceresine yapıştırabilirsiniz: `vscode:extension/vsciot-vscode.azure-iot-tools`

    > [!NOTE]
    > Azure IoT araçları uzantı paketi, çeşitli IoT devkit cihazlarında geliştirme ve hata ayıklama için kullanılan [Azure IoT cihaz çalışma ekranı](https://aka.ms/iot-workbench) 'nı içerir. Azure IoT Tools uzantısıyla sunulan [Azure IoT Hub uzantısı](https://aka.ms/iot-toolkit), Azure IoT Hubs’ı yönetip bunlarla etkileşim kurmak için kullanılır.

5. Arduino ayarları ile VS Code yapılandırın.

    Visual Studio Code ' de, **dosya > tercihleri > ayarlar** ' a tıklayın (MacOS üzerinde, **kod > tercihleri > ayarlar**). Ardından *Ayarlar* sayfasının sağ üst köşesindeki **ayarları aç (JSON)** simgesine tıklayın.

    ![Azure IoT araçları 'nı yükler](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/user-settings-arduino.png)

    Platformunuza bağlı olarak Arduino’yu yapılandırmak için aşağıdaki satırları ekleyin: 

    * **Windows**:

        ```json
        "arduino.path": "C:\\Program Files (x86)\\Arduino",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

    * **macOS**:

        ```json
        "arduino.path": "/Applications",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

    * **Ubuntu**:

        Aşağıdaki **{username}** yer tutucusunu kullanıcı adınızla değiştirin.

        ```json
        "arduino.path": "/home/{username}/Downloads/arduino-1.8.8",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

6. `F1`Komut paletini açmak için tıklayın, yazın ve **Arduino: Board Manager**' ı seçin. **AZ3166** araması yapın ve en son sürümü yükler.

    ![DevKit SDK 'Yı yükler](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-az3166-sdk.png)

### <a name="install-st-link-drivers"></a>ST-Link sürücülerini yükler

[St-link/v2](https://www.st.com/en/development-tools/st-link-v2.html) , IoT devkit 'in geliştirme makineniz ile iletişim kurmak IÇIN kullandığı USB arabirimidir. Derlenen cihaz kodunu DevKit’e flaş yazma ile yüklemek için Windows’a yüklemeniz gerekir. Makinenin cihazınıza erişmesine izin vermek için işletme sistemine özgü adımları izleyin.

* **Windows**: [stmikro elektronık Web sitesinden](https://www.st.com/en/development-tools/stsw-link009.html)USB sürücü indirin ve yükleyin.
* **MacOS**: MacOS için sürücü gerekmez.
* **Ubuntu**: Komutları terminalde çalıştırın ve grup değişikliğinin etkili olması için oturumunuzu kapatıp açın:

    ```bash
    # Copy the default rules. This grants permission to the group 'plugdev'
    sudo cp ~/.arduino15/packages/AZ3166/tools/openocd/0.10.0/linux/contrib/60-openocd.rules /etc/udev/rules.d/
    sudo udevadm control --reload-rules

    # Add yourself to the group 'plugdev'
    # Logout and log back in for the group to take effect
    sudo usermod -a -G plugdev $(whoami)
    ```

Artık geliştirme ortamınızı hazırlama ve yapılandırma ile birlikte hazırsınız. Az önce çalıştırdığınız GetStarted örneğini oluşturalım.

## <a name="build-your-first-project"></a>İlk projenizi oluşturma

### <a name="open-sample-code-from-sample-gallery"></a>Örnek Galeriden örnek kodu aç

IoT DevKit, DevKit 'i çeşitli Azure hizmetlerine bağlamayı öğrenmek için kullanabileceğiniz, zengin bir örnek Galerisi içerir.

1. IoT DevKit 'in bilgisayarınıza **bağlı** olmadığından emin olun. Önce VS Code başlatın ve ardından DevKit 'i bilgisayarınıza bağlayın.

1. `F1`Komut paletini açmak için tıklayın, yazın ve **Azure IoT cihaz çalışma ekranı ' nı seçin: örnekleri aç...**. Ardından, pano olarak **IoT DevKit** ' i seçin.

1. IoT çalışma ekranı örnekleri sayfasında **Başlarken** ' i bulun ve **örneği aç**' a tıklayın. Ardından örnek kodu indirmek için varsayılan yolu seçer.

    ![Örnek Aç](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/open-sample.png)

### <a name="provision-azure-iot-hub-and-device"></a>Azure IoT Hub ve cihaz sağlama

Azure portal Azure IoT Hub ve cihaz sağlamak yerine, bunu geliştirme ortamından çıkmadan VS Code yapabilirsiniz.

1. Yeni açılan proje penceresinde, `F1` komut paletini açmak için tıklayın ve **Azure IoT cihaz çalışma ekranı: Azure hizmetleri sağla...** seçeneğini belirleyin. Azure IoT Hub sağlamayı ve IoT Hub cihazı oluşturmayı tamamlayacak adım adım kılavuzu izleyin.

    ![Komutu sağla](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision.png)

    > [!NOTE]
    > Azure 'da oturum açmadıysanız. Oturum açmak için açılır bildirimi izleyin.

1. Kullanmak istediğiniz aboneliği seçin.

    ![Alt seçme](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-subscription.png)

1. Ardından yeni bir [kaynak grubu](../azure-resource-manager/management/overview.md#terminology)seçin veya oluşturun.

    ![Kaynak grubunu seçme](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-resource-group.png)

1. Belirttiğiniz kaynak grubunda, yeni bir Azure IoT Hub seçmek veya oluşturmak için Kılavuzu izleyin.

    ![IoT Hub adımları seçin](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provision.png)

    ![IoT Hub seçin](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-hub.png)

    ![Seçili IoT Hub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-selected.png)

1. Çıkış penceresinde, sağlanan Azure IoT Hub görürsünüz.

    ![IoT Hub sağlandı](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provisioned.png)

1. Sağladığınız Azure IoT Hub yeni bir cihaz seçin veya oluşturun.

    ![IoT cihaz adımlarını seçin](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-device-provision.png)

    ![Sağlanan IoT cihazını seçin](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-device.png)

1. Artık sağlanmış bir Azure IoT Hub’ınız ve içinde oluşturulmuş bir cihazınız var. Ayrıca, IoT DevKit 'i daha sonra yapılandırmak için cihaz bağlantı dizesi VS Code kaydedilir.

    ![Sağlama tamamlandı](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision-done.png)

### <a name="configure-and-compile-device-code"></a>Cihaz kodunu yapılandırma ve derleme

1. Sağ alt durum çubuğunda, **MxyongaAZ3166** ' ın seçili pano olarak gösterildiğini ve **stmikro elektronik** ' ın kullanıldığı seri bağlantı noktasını denetleyin.

    ![Pano ve COM seçin](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-com.png)

1. `F1`Komut paletini açmak için tıklayın ve **Azure IoT cihaz çalışma ekranı: cihaz ayarlarını yapılandır...** öğesini seçin ve ardından **yapılandırma cihaz bağlantı dizesi > IoT Hub cihaz bağlantı dizesi**' ni seçin.

1. DevKit üzerinde, **düğme a**' yı basılı tutun, **Sıfırla** düğmesini gönderin ve sonra **A düğmesine** basın. DevKit yapılandırma moduna girer ve bağlantı dizesini kaydeder.

    ![Bağlantı dizesi](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connection-string.png)

1. `F1`Yeniden tıklayın, yazın ve **Azure IoT cihaz çalışma ekranı: Cihaz kodunu karşıya yükle**' yi seçin. Derlemeyi başlatır ve kodu DevKit 'e yükler.

    ![Arduino karşıya yükleme](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/arduino-upload.png)

DevKit yeniden başlatılır ve kodu çalıştırmaya başlar.

> [!NOTE]
> Herhangi bir hata veya kesintiler varsa, komutu yeniden çalıştırarak her zaman kurtarma yapabilirsiniz.

## <a name="test-the-project"></a>Projeyi test etme

### <a name="view-the-telemetry-sent-to-azure-iot-hub"></a>Azure IoT Hub gönderilen Telemetriyi görüntüleme

Durum çubuğundaki güç tak simgesine tıklayarak seri Izleyicisini açın:

![Seri izleyici](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/serial-monitor.png)

Aşağıdaki sonuçları gördüğünüzde örnek uygulama başarıyla çalışıyor:

* Seri Izleyici IoT Hub gönderilen iletiyi görüntüler.
* Mxyongaıot DevKit 'in ışığı yanıp söndüğü.

![Seri izleyici çıktısı](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/result-serial-output.png)

> [!NOTE]
> LED 'in yanıp söndüğü bir hata ile karşılaşabilirsiniz, Azure portal cihazdan gelen verileri göstermez, ancak cihaz karıştırılmış ekran çalışıyor gibi görünür **...** Sorunu çözmek için, Azure portal IoT Hub 'ındaki cihaza gidin ve cihaza bir ileti gönderin. VS Code ' de seri monitörde aşağıdaki yanıtı görürseniz, cihazdan doğrudan iletişimin, yönlendirici düzeyinde engellenmesi mümkündür. Bağlanan cihazlar için yapılandırılmış güvenlik duvarı ve yönlendirici kurallarını denetleyin. Ayrıca, 1833 giden bağlantı noktasının açık olduğundan emin olun.
> 
> Hata: mqtt_client. c (ln 454): hata: uç noktaya bağlantı açılırken hata oluştu  
> BILGI:  >>>bağlantı durumu: bağlantısı kesildi  
> Hata: tlsio_mbedtls. c (ln 604): temeldeki GÇ açma başarısız oldu  
> Hata: mqtt_client. c (ln 1042): hata: io_open başarısız oldu  
> Hata: iothubtransport_mqtt_common. c (ln 2283): Address atcsliothub.azure-devices.net 'e bağlanılırken hata oluştu.  
> BILGI:  >>>yeniden bağlanın.  
> BILGI: ıothub Version: 1.3.6  

### <a name="view-the-telemetry-received-by-azure-iot-hub"></a>Azure IoT Hub tarafından alınan telemetrileri görüntüleme

IoT Hub içindeki cihazdan buluta (D2C) iletilerini izlemek için [Azure IoT araçları](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) 'nı kullanabilirsiniz.

1. [Azure Portal](https://portal.azure.com/)oturum açın, oluşturduğunuz IoT Hub bulun.

    ![Azure portalı](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-iot-hub-portal.png)

1. **Paylaşılan erişim ilkeleri** bölmesinde, **ıothubowner Ilkesine** tıklayın ve IoT Hub 'ınızın bağlantı dizesini yazın.

    ![Azure IoT Hub bağlantı dizesi](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-portal-conn-string.png)

1. VS Code ' de `F1` , yazın ve **Azure IoT Hub: IoT Hub bağlantı dizesi ayarla**' yı seçin. Bağlantı dizesini buna kopyalayın.

    ![Azure IoT Hub bağlantı dizesi ayarla](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/set-iothub-connection-string.png)

1. Sol taraftaki **Azure ıOT hub cihazları** bölmesini genişletin, oluşturduğunuz cihaz adına sağ tıklayın ve **Izlemeyi Başlat yerleşik olay uç noktası**' nı seçin.

    ![D2C Iletisini izle](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/monitor-d2c.png)

1. **Çıkış** bölmesinde, gelen D2C iletilerini IoT Hub görebilirsiniz.

    ![IoT Hub gelen D2C iletilerini gösteren ekran görüntüsü.](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/d2c-output.png)

## <a name="review-the-code"></a>Kodu gözden geçirin

, `GetStarted.ino` Ana Arduino taslak dosyasıdır.

![D2C iletisi](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/code.png)

Cihaz telemetrinin Azure IoT Hub nasıl gönderildiğini görmek için, `utility.cpp` dosyayı aynı klasörde açın. IoT DevKit üzerinde algılayıcılar ve çevre birimleri kullanmayı öğrenmek için [API başvurusunu](https://microsoft.github.io/azure-iot-developer-kit/docs/apis/arduino-language-reference/) görüntüleyin.

`DevKitMQTTClient`Kullanılan, Azure IoT Hub etkileşimde bulunmak için [Microsoft Azure IoT SDK 'ları ve kitaplıkları](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client) **iothub_client** sarmalayıcısıdır.

## <a name="problems-and-feedback"></a>Sorunlar ve geri bildirim

Sorunlarla karşılaşırsanız [IoT DevKit SSS](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) ' de bir çözümü denetleyebilir veya [Gitter](https://gitter.im/Microsoft/azure-iot-developer-kit)adresinden bize ulaşabilirsiniz. Bu sayfada bir yorum bırakarak bize geri bildirim de verebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bir Mxyonga IoT DevKit 'i IoT Hub 'ınıza başarıyla bağladınız ve yakalanan algılayıcı verilerini IoT Hub 'ınıza gönderdiniz.

[!INCLUDE [iot-hub-get-started-az3166-next-steps](../../includes/iot-hub-get-started-az3166-next-steps.md)]
