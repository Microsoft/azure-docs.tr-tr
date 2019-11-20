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
ms.openlocfilehash: 3cdeecd5b7698274b899832d7a66a5572a114e4b
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73954510"
---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub"></a>IoT DevKit AZ3166 'i Azure 'a bağlama IoT Hub

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Microsoft Azure hizmetlerinden faydalanan Nesnelerin İnterneti (IoT) çözümleri geliştirmek ve prototip oluşturmak için [Mxyonga IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) ' i kullanabilirsiniz. Zengin çevre birimleri ve algılayıcıları, açık kaynaklı bir pano paketi ve zengin bir [örnek Galerisi](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/)Içeren bir Arduino ile uyumlu bir Pano içerir.

## <a name="what-you-learn"></a>Öğrenecekleriniz

* Bir IoT Hub 'ı oluşturma ve Mxyonga IoT DevKit için bir cihaz kaydetme.
* IoT DevKit 'i Wi-Fi ' e bağlama ve IoT Hub bağlantı dizesini yapılandırma.
* DevKit algılayıcı telemetri verilerini IoT Hub 'ınıza gönderme.
* Geliştirme ortamını hazırlama ve IoT DevKit için uygulama geliştirme.

Henüz bir DevKit yok mu? [Devkit simülatörünü](https://azure-samples.github.io/iot-devkit-web-simulator/) deneyin veya [bir devkit satın alın](https://aka.ms/iot-devkit-purchase).

[Kod örnekleri galerisindeki](https://docs.microsoft.com/samples/browse/?term=mxchip)tüm devkit öğreticileri için kaynak kodu bulabilirsiniz.

## <a name="what-you-need"></a>Ne gerekiyor

* Mikro USB kablosuyla bir Mxyongaıot DevKit panosu. [Hemen alın](https://aka.ms/iot-devkit-purchase).
* Windows 10, macOS 10.10 + veya Ubuntu 18.04 + çalıştıran bir bilgisayar.
* Etkin bir Azure aboneliği. [30 günlük ücretsiz deneme Microsoft Azure hesabını etkinleştirin](https://azureinfo.microsoft.com/us-freetrial.html).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]
  
## <a name="prepare-your-hardware"></a>Donanımınızı hazırlama

Aşağıdaki donanımı bilgisayarınıza bağlayın:

* DevKit panosu
* Mikro USB kablosu

![Gerekli donanım](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/hardware.jpg)

DevKit 'i bilgisayarınıza bağlamak için şu adımları izleyin:

1. USB sonlandırnu bilgisayarınıza bağlayın.

2. Mikro USB sonunu DevKit 'e bağlayın.

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
   > `device-identity`çalıştıran bir hata alırsanız daha fazla bilgi için [Azure CLI Için Azure IoT uzantısını](https://github.com/Azure/azure-iot-cli-extension/blob/dev/README.md) yükleyebilirsiniz.
  
1. Yeni kaydettiğiniz cihazın _cihaz bağlantı dizesini_ almak için aşağıdaki komutları Azure Cloud Shell'de çalıştırın:

   **Youriothubname**: Bu yer tutucuyu, IoT Hub 'ınız için seçtiğiniz adla değiştirin.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyNodeDevice --output table
    ```

    Şu ifadeye benzer şekilde görünen cihaz bağlantı dizesini not edin:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Bu değeri hızlı başlangıcın ilerleyen bölümlerinde kullanacaksınız.

### <a name="send-devkit-telemetry"></a>DevKit telemetrisini gönder

DevKit, IoT Hub 'ınızdaki cihaza özgü bir uç noktaya bağlanır ve sıcaklık ve nem telemetrisini gönderir.

1. IoT DevKit için [getstarted üretici yazılımının](https://aka.ms/devkit/prod/getstarted/latest) en son sürümünü indirin.

1. IoT DevKit 'in bilgisayarınıza USB üzerinden bağlanmasını sağlayın. Dosya Gezgini 'ni açın **AZ3166**ADLı bir USB yığın depolama cihazı vardır.

    ![Windows Gezgini 'ni aç](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/az3166-usb.png)

1. Üretici yazılımını sürükleyip bırakın ve doğrudan yığın depolama cihazına indirilir. Bu işlem, otomatik olarak yanıp sönecektir.

    ![Üretici yazılımını kopyalama](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/copy-firmware.png)

1. DevKit üzerinde, düğme **b**tuşunu basılı tutarak **sıfırlama** düğmesini gönderin ve serbest bırakın ve sonra **B**düğmesini bırakın. DevKit, AP moduna girer. Doğrulamak için, ekranda DevKit ve Configuration Portal IP adresinin hizmet kümesi tanımlayıcısını (SSID) görüntülenir.

    ![Sıfırla düğmesi, düğme B ve SSID](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/wifi-ap.jpg)

    ![AP modunu ayarla](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/set-ap-mode.gif)

1. Önceki adımda gösterildiği IoT DevKit SSID 'ye bağlanmak için farklı bir Wi-Fi etkin cihazda (bilgisayar veya cep telefonu) bir Web tarayıcısı kullanın. Parola isterse, boş bırakın.

    ![Bağlantı SSID 'SI](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/connect-ssid.png)

1. Tarayıcıda **192.168.0.1** 'yi açın. IoT DevKit 'in bağlanmasını istediğiniz Wi-Fi ' ı seçin, Wi-Fi parolasını yazın, ardından daha önce notettiğiniz cihaz bağlantı dizesini yapıştırın. Daha sonra Kaydet'e tıklayın.

    ![Yapılandırma Kullanıcı arabirimi](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/configuration-ui.png)

    > [!NOTE]
    > IoT DevKit yalnızca 2,4 GHz ağı destekler. Daha fazla ayrıntı için [SSS bölümüne](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#wi-fi-configuration) bakın.

1. Sonuç sayfasını gördüğünüzde, WiFi bilgileri ve cihaz bağlantı dizesi IoT DevKit ' de depolanır.

    ![Yapılandırma sonucu](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/configuration-ui-result.png)

    > [!NOTE]
    > Wi-Fi yapılandırıldıktan sonra, cihaz takılı olmasa dahi kimlik bilgileriniz Bu bağlantı için cihazda kalır.

1. IoT DevKit birkaç saniye içinde yeniden başlatılır. DevKit ekranında, telekit 'in IP adresini, sıcaklık ve nem değeri de dahil olmak üzere, Azure IoT Hub ileti sayısı ile birlikte aşağıdaki şekilde görürsünüz.

    ![WiFi IP 'si](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/wifi-ip.jpg)

    ![Veri gönderme](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/sending-data.jpg)

1. Azure 'a gönderilen telemetri verilerini doğrulamak için Azure Cloud Shell ' de aşağıdaki komutu çalıştırın:

    ```bash
    az iot hub monitor-events --hub-name YourIoTHubName --output table
    ```

## <a name="prepare-the-development-environment"></a>Geliştirme ortamını hazırlama

DevKit için geliştirme ortamını hazırlamak üzere aşağıdaki adımları izleyin:

### <a name="install-visual-studio-code-with-azure-iot-tools-extension-package"></a>Azure IoT araçları uzantı paketiyle Visual Studio Code 'yi yükler

1. [Arduino IDE](https://www.arduino.cc/en/Main/Software)'yi yükler. Arduino kodunu derlemek ve karşıya yüklemek için gerekli araç zincirini sağlar.
    * **Windows**: Windows Installer sürümünü kullanın. Uygulama mağazasından yüklemeyin.
    * **MacOS**: ayıklanan **Arduino. uygulamayı** `/Applications` klasöre sürükleyip bırakın.
    * **Ubuntu**: `$HOME/Downloads/arduino-1.8.8` gibi klasöre sıkıştırmayı açın

2. Güçlü IntelliSense ile platformlar arası kaynak kodu Düzenleyicisi, kod tamamlama ve hata ayıklama desteği, Market 'ten de zengin uzantılar yüklenebilir [Visual Studio Code](https://code.visualstudio.com/)yükleme.

3. VS Code başlatın, uzantı marketi 'nde **Arduino** için arama yapın ve yüklemeyi yapın. Bu uzantı, Arduino platformunda geliştirme için gelişmiş deneyimler sağlar.

    ![Arduino 'yi yükler](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-arduino.png)

4. Uzantı marketi ' nde [Azure IoT araçları](https://aka.ms/azure-iot-tools) ' nı bulun ve yüklemeyi yapın.

    ![Azure IoT araçları 'nı yükler](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-azure-iot-tools.png)

    Ya da bu doğrudan bağlantıyı kullanın:
    > [!div class="nextstepaction"]
    > [Azure IoT araçları uzantı paketini yükler](vscode:extension/vsciot-vscode.azure-iot-tools)

    > [!NOTE]
    > Azure IoT araçları uzantı paketi, çeşitli IoT devkit cihazlarında geliştirme ve hata ayıklama için kullanılan [Azure IoT cihaz çalışma ekranı](https://aka.ms/iot-workbench) 'nı içerir. Azure IoT araçları uzantı paketine da dahil olan [azure IoT Hub araç seti](https://aka.ms/iot-toolkit), Azure IoT Hub 'larını yönetmek ve bunlarla etkileşim kurmak için kullanılır.

5. Arduino ayarları ile VS Code yapılandırın.

    Visual Studio Code ' de, **dosya > tercihleri > ayarlar** ' a tıklayın (MacOS üzerinde, **kod > Tercihleri > Ayarlar**). Ardından *Ayarlar* sayfasının sağ üst köşesindeki **ayarları aç (JSON)** simgesine tıklayın.

    ![Azure IoT araçları 'nı yükler](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/user-settings-arduino.png)

    Platformunuza bağlı olarak Arduino 'yi yapılandırmak için aşağıdaki satırları ekleyin: 

    * **Windows**:

        ```json
        "arduino.path": "C:\\Program Files (x86)\\Arduino",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

    * **MacOS**:

        ```json
        "arduino.path": "/Applications",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

    * **Ubuntu**:

        Aşağıdaki **{UserName}** yer tutucusunu Kullanıcı adınızla değiştirin.

        ```json
        "arduino.path": "/home/{username}/Downloads/arduino-1.8.8",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

6. Komut paletini açmak için `F1` ' a tıklayın, yazın ve **Arduino: Board Manager**' ı seçin. **AZ3166** araması yapın ve en son sürümü yükler.

    ![DevKit SDK 'Yı yükler](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-az3166-sdk.png)

### <a name="install-st-link-drivers"></a>ST-Link sürücülerini yükler

[St-link/v2](https://www.st.com/en/development-tools/st-link-v2.html) , IoT devkit 'in geliştirme makineniz ile iletişim kurmak IÇIN kullandığı USB arabirimidir. Derlenen Cihaz kodunu DevKit 'e Flash için Windows 'a yüklemeniz gerekir. Cihaza makineye erişim sağlamak için işletim sistemine özgü adımları izleyin.

* **Windows**: [stmikro elektronık Web sitesinden](https://www.st.com/en/development-tools/stsw-link009.html)USB sürücü indirin ve yükleyin.
* **MacOS**: MacOS için sürücü gerekmez.
* **Ubuntu**: terminalde komutları çalıştırın ve grup değişikliğinin etkili olması için oturumu açın ve oturum açın:

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

1. Komut paletini açmak için `F1` ' a tıklayın, yazın ve **Azure IoT cihaz çalışma ekranı ' nı seçin: örnekleri aç...** . Ardından, pano olarak **IoT DevKit** ' i seçin.

1. IoT çalışma ekranı örnekleri sayfasında **Başlarken** ' i bulun ve **örneği aç**' a tıklayın. Ardından örnek kodu indirmek için varsayılan yolu seçer.

    ![Örnek Aç](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/open-sample.png)

### <a name="provision-azure-iot-hub-and-device"></a>Azure IoT Hub ve cihaz sağlama

Azure portal Azure IoT Hub ve cihaz sağlamak yerine, bunu geliştirme ortamından çıkmadan VS Code yapabilirsiniz.

1. Yeni açılan proje penceresinde `F1` ' ye tıklayarak komut paletini açın, **Azure IoT cihaz çalışma ekranı: Azure hizmetleri sağla...** seçeneğini belirleyin. Azure IoT Hub sağlamayı ve IoT Hub cihazı oluşturmayı tamamlayacak adım adım kılavuzu izleyin.

    ![Komutu sağla](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision.png)

    > [!NOTE]
    > Azure 'da oturum açmadıysanız. Oturum açmak için açılır bildirimi izleyin.

1. Kullanmak istediğiniz aboneliği seçin.

    ![Alt seçme](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-subscription.png)

1. Ardından yeni bir [kaynak grubu](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#terminology)seçin veya oluşturun.

    ![Kaynak grubu seçin](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-resource-group.png)

1. Belirttiğiniz kaynak grubunda, yeni bir Azure IoT Hub seçmek veya oluşturmak için Kılavuzu izleyin.

    ![IoT Hub adımları seçin](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provision.png)

    ![IoT Hub seçin](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-hub.png)

    ![Seçili IoT Hub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-selected.png)

1. Çıkış penceresinde, sağlanan Azure IoT Hub görürsünüz.

    ![IoT Hub sağlandı](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provisioned.png)

1. Sağladığınız Azure IoT Hub yeni bir cihaz seçin veya oluşturun.

    ![IoT cihaz adımlarını seçin](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-device-provision.png)

    ![Sağlanan IoT cihazını seçin](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-device.png)

1. Artık Azure IoT Hub sağlanmış ve cihaz bu cihazda oluşturulmuş. Ayrıca, IoT DevKit 'i daha sonra yapılandırmak için cihaz bağlantı dizesi VS Code kaydedilir.

    ![Sağlama tamamlandı](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision-done.png)

### <a name="configure-and-compile-device-code"></a>Cihaz kodunu yapılandırma ve derleme

1. Sağ alt durum çubuğunda, **MxyongaAZ3166** ' ın seçili pano olarak gösterildiğini ve **stmikro elektronik** ' ın kullanıldığı seri bağlantı noktasını denetleyin.

    ![Pano ve COM seçin](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-com.png)

1. Komut paletini açmak için `F1` ' a tıklayın ve **Azure IoT cihaz çalışma ekranı: cihaz ayarlarını yapılandır...** öğesini seçin ve ardından **yapılandırma cihaz bağlantı dizesi > IoT Hub cihaz bağlantı dizesi**' ni seçin.

1. DevKit üzerinde, **düğme a**' yı basılı tutun, **Sıfırla** düğmesini gönderin ve sonra **A düğmesine**basın. DevKit yapılandırma moduna girer ve bağlantı dizesini kaydeder.

    ![Bağlantı dizesi](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connection-string.png)

1. `F1` yeniden tıklatın, Azure IoT cihaz çalışma ekranı ' nı yazın ve seçin **: Cihaz kodunu karşıya yükleyin**. Derlemeyi başlatır ve kodu DevKit 'e yükler.

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

### <a name="view-the-telemetry-received-by-azure-iot-hub"></a>Azure IoT Hub tarafından alınan telemetrileri görüntüleme

IoT Hub içindeki cihazdan buluta (D2C) iletilerini izlemek için [Azure IoT araçları](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) 'nı kullanabilirsiniz.

1. [Azure Portal](https://portal.azure.com/)oturum açın, oluşturduğunuz IoT Hub bulun.

    ![Azure portalında](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-iot-hub-portal.png)

1. **Paylaşılan erişim ilkeleri** bölmesinde, **ıothubowner Ilkesine**tıklayın ve IoT Hub 'ınızın bağlantı dizesini yazın.

    ![Azure IoT Hub bağlantı dizesi](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-portal-conn-string.png)

1. VS Code ' de, `F1`' a tıklayın ve **Azure IoT Hub: IoT Hub bağlantı dizesi ayarla**' yı seçin. Bağlantı dizesini buna kopyalayın.

    ![Azure IoT Hub bağlantı dizesi ayarla](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/set-iothub-connection-string.png)

1. Sağdaki **Azure ıOT hub cihazları** bölmesini genişletin, oluşturduğunuz cihaz adına sağ tıklayın ve **Izlemeyi Başlat yerleşik olay uç noktası**' nı seçin.

    ![D2C Iletisini izle](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/monitor-d2c.png)

1. **Çıkış** bölmesinde, gelen D2C iletilerini IoT Hub görebilirsiniz.

    ![D2C iletisi](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/d2c-output.png)

## <a name="review-the-code"></a>Kodu gözden geçirin

`GetStarted.ino`, ana Arduino taslak dosyasıdır.

![D2C iletisi](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/code.png)

Cihaz telemetrinin Azure IoT Hub nasıl gönderildiğini görmek için, `utility.cpp` dosyasını aynı klasörde açın. IoT DevKit üzerinde algılayıcılar ve çevre birimleri kullanmayı öğrenmek için [API başvurusunu](https://microsoft.github.io/azure-iot-developer-kit/docs/apis/arduino-language-reference/) görüntüleyin.

Kullanılan `DevKitMQTTClient`, Azure IoT Hub ile etkileşime geçmek için [Microsoft Azure IoT SDK 'ları ve C kitaplıklarının](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client) **iothub_client** sarmalayıcısıdır.

## <a name="problems-and-feedback"></a>Sorunlar ve geri bildirim

Sorunlarla karşılaşırsanız [IoT DevKit SSS](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) ' de bir çözümü denetleyebilir veya [Gitter](https://gitter.im/Microsoft/azure-iot-developer-kit)adresinden bize ulaşabilirsiniz. Bu sayfada bir yorum bırakarak bize geri bildirim de verebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bir Mxyonga IoT DevKit 'i IoT Hub 'ınıza başarıyla bağladınız ve yakalanan algılayıcı verilerini IoT Hub 'ınıza gönderdiniz.

[!INCLUDE [iot-hub-get-started-az3166-next-steps](../../includes/iot-hub-get-started-az3166-next-steps.md)]
