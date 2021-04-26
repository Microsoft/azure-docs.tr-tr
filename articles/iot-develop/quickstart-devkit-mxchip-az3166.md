---
title: Bir MXYONGA AZ3166 Azure 'a bağlama IoT Central hızlı başlangıç
description: Bir MXYONGA AZ3166 cihazını Azure IoT 'ye bağlamak ve telemetri göndermek için Azure RTOS Embedded yazılımını kullanın.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: c
ms.topic: quickstart
ms.date: 03/17/2021
ms.openlocfilehash: 160797367e2daf0cb6fe708d626cbf217c9992c8
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448618"
---
# <a name="quickstart-connect-an-mxchip-az3166-devkit-to-iot-central"></a>Hızlı başlangıç: bir MXYONGAAZ3166 devkit 'i IoT Central bağlama

**Uygulama hedefi**: [katıştırılmış cihaz geliştirme](about-iot-develop.md#embedded-device-development)<br>
**Toplam tamamlanma süresi**: 30 dakika

[![Koda göz atın](media/common/browse-code.svg)](https://github.com/azure-rtos/getting-started/tree/master/MXChip/AZ3166)

Bu öğreticide, bir MXYONGA AZ3166 IoT DevKit 'i (herbundan sonra MXYONGA DevKit) Azure IoT 'ye bağlamak için Azure RTOS 'ı kullanırsınız. Bu makale, [Azure IoT Embedded cihaz geliştirmesi ile çalışmaya başlama](quickstart-device-development.md)serisinin bir parçasıdır. Seriler cihaz geliştiricilerinin Azure RTOS 'a tanıtılmasını sağlar ve çeşitli cihaz değerlendirme setlerinin Azure IoT 'ye nasıl bağlanacağını gösterir.

Şu görevleri tamamlayacaksınız:

* C 'de MXYONGA DevKit 'i programlamak için bir yerleşik geliştirme araçları kümesi yükler
* Bir görüntü oluşturun ve MXYONGA DevKit üzerine Flash yapın
* Azure IoT Central kullanarak bulut bileşenleri oluşturun, özellikleri görüntüleyin, cihaz telemetrisini görüntüleyin ve doğrudan komutları çağırın

## <a name="prerequisites"></a>Önkoşullar

* Microsoft Windows 10 çalıştıran bir BILGISAYAR
* Depoyu kopyalamak için [Git](https://git-scm.com/downloads)
* Donanım

    > * [MXYONGAAZ3166 IoT devkit](https://aka.ms/iot-devkit) (Mxyonga devkit)
    > * Wi-Fi 2,4 GHz
    > * USB 2,0 A Erkek ve mikro USB erkek kablosu

## <a name="prepare-the-development-environment"></a>Geliştirme ortamını hazırlama

Geliştirme ortamınızı ayarlamak için öncelikle öğretici için ihtiyaç duyduğunuz tüm varlıkları içeren bir GitHub deposu klonlayın. Daha sonra bir programlama araçları kümesi yüklersiniz.

### <a name="clone-the-repo-for-the-tutorial"></a>Öğretici için depoyu kopyalayın

Tüm örnek cihaz kodunu, kurulum betiklerini ve belgelerin çevrimdışı sürümlerini indirmek için aşağıdaki depoyu kopyalayın. Bu depoyu daha önce başka bir öğreticide Klonladığınız takdirde, bunu tekrar yapmanız gerekmez.

Depoyu kopyalamak için aşağıdaki komutu çalıştırın:

```shell
git clone --recursive https://github.com/azure-rtos/getting-started.git
```

### <a name="install-the-tools"></a>Araçları yükler

Kopyalanmış depo, gerekli araçları yükleyen ve yapılandıran bir kurulum betiği içerir. Bu araçları Başlarken kılavuzunda başka bir öğreticiye yüklediyseniz, bunu tekrar yapmanız gerekmez.

> [!NOTE]
> Kurulum betiği aşağıdaki araçları kurar:
> * [CMake](https://cmake.org): derleme
> * [ARM GCC](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm): derle
> * [Termite](https://www.compuphase.com/software_termite.htm): bağlı cihazlar için seri bağlantı noktası çıkışını izleme

Araçları yüklemek için:

1. Dosya Gezgini 'nde, depoda aşağıdaki yola gidin ve *get-toolchain.bat* adlı kurulum betiğini çalıştırın:

    > *getting-started\tools\get-toolchain.bat*

1. Yükleme sonrasında, kurulum betiği tarafından yapılan yapılandırma değişikliklerini tanımak için yeni bir konsol penceresi açın. Öğreticideki kalan programlama görevlerini gerçekleştirmek için bu konsolu kullanın. Windows için Windows CMD, PowerShell veya git Bash ' i kullanabilirsiniz.
1. CMake 3,14 veya sonraki bir sürümünün yüklendiğini doğrulamak için aşağıdaki kodu çalıştırın.

    ```shell
    cmake --version
    ```

## <a name="create-the-cloud-components"></a>Bulut bileşenlerini oluşturma

### <a name="create-the-iot-central-application"></a>IoT Central uygulamasını oluşturma

Cihazları Azure IoT 'ye bağlamak için birkaç yol vardır. Bu bölümde, Azure IoT Central kullanarak bir cihazı bağlamayı öğreneceksiniz. IoT Central, IoT çözümlerini oluşturma ve yönetmenin maliyetini ve karmaşıklığını azaltan bir IoT uygulama platformudur.

Yeni bir uygulama oluşturmak için:
1. [Azure IoT Central portalından](https://apps.azureiotcentral.com/), yan gezinti menüsünde **uygulamalarım** ' ı seçin.
1. **+ Yeni uygulama**' yı seçin.
1. **Özel uygulamalar**’ı seçin.
1. Uygulama adı ve URL 'SI ekleyin.
1. 7 günlük denemeyi etkinleştirmek için **ücretsiz** fiyatlandırma planı ' nı seçin.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/iot-central-create-custom.png" alt-text="Azure IoT Central özel uygulama oluşturma":::

1. **Oluştur**’u seçin.

    Uygulamayı IoT Central ettikten sonra, yeni uygulama panosuna otomatik olarak yeniden yönlendirir.

    > [!NOTE]
    > Mevcut bir IoT Central uygulamanız varsa, yeni bir uygulama oluşturmak yerine bu makaledeki adımları tamamlayabilmeniz için kullanabilirsiniz.

### <a name="create-a-new-device"></a>Yeni cihaz oluşturma

Bu bölümde, yeni bir cihaz oluşturmak için IoT Central uygulama panosunu kullanırsınız. Fiziksel cihazınızı daha sonraki bir bölümde güvenli bir şekilde bağlamak için yeni oluşturulan cihaz için bağlantı bilgilerini kullanacaksınız.

Bir cihaz oluşturmak için:
1. Uygulama panosu ' ndan, yan gezinti menüsünde **cihazlar** ' ı seçin.
1. **Yeni bir cihaz oluştur** penceresini açmak Için **+ Yeni** ' yi seçin.
1. Cihaz şablonunu **atanmamış** olarak bırakın.
1. İstenen cihaz adı ve cihaz KIMLIĞINI girin.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/iot-central-create-device.png" alt-text="Azure IoT Central cihaz oluşturma":::

1. **Oluştur** düğmesini seçin.
1. Yeni oluşturulan cihaz **tüm cihazlar** listesinde görünür.  Ayrıntıları göstermek için cihaz adı ' nı seçin.
1. Sonraki bölümde, cihazı yapılandırmak için kullanılan bağlantı bilgilerini göstermek için sağ üst menü çubuğunda **Bağlan** ' ı seçin.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/iot-central-device-connection-info.png" alt-text="Cihaz bağlantısı ayrıntılarını görüntüle":::

1. **Bağlan** iletişim kutusunda aşağıdaki bağlantı dizesi parametrelerinin bağlantı değerlerini göz önünde edin. Sonraki adımda bu değerleri bir yapılandırma dosyasına ekleyeceksiniz:

    > * `ID scope`
    > * `Device ID`
    > * `Primary key`

## <a name="prepare-the-device"></a>Cihazı hazırlama

MXYONGADEVKIT 'i Azure 'a bağlamak için, Wi-Fi ve Azure IoT ayarları için bir yapılandırma dosyası değiştirecek, görüntüyü yeniden derlemeli ve görüntüyü cihaza yansıyacaksınız.

### <a name="add-configuration"></a>Yapılandırma ekleme

1. Aşağıdaki dosyayı bir metin düzenleyicisinde açın:

    > *getting-started\MXChip\AZ3166\app\ azure_config. h*

1. Wi-Fi sabitlerini yerel ortamınızdan aşağıdaki değerlere ayarlayın.

    |Sabit adı|Değer|
    |-------------|-----|
    |`WIFI_SSID` |{*Wi-Fi SSID*}|
    |`WIFI_PASSWORD` |{*Wi-Fi parolanız*}|
    |`WIFI_MODE` |{*Dosyadaki numaralandırılan Wi-Fi mod değerlerinden biri*}|

1. Azure IoT cihaz bilgileri sabitlerini, Azure kaynaklarını oluşturduktan sonra kaydettiğiniz değerlere ayarlayın.

    |Sabit adı|Değer|
    |-------------|-----|
    |`IOT_DPS_ID_SCOPE` |{*ID kapsam değeri*}|
    |`IOT_DPS_REGISTRATION_ID` |{*CIHAZ kimliği değeri*}|
    |`IOT_DEVICE_SAS_KEY` |{*Birincil anahtar değer*}|

1. Dosyayı kaydedin ve kapatın.

### <a name="build-the-image"></a>Görüntü oluşturma

Konsolunuzda veya dosya Gezgini 'nde, görüntüyü derlemek için aşağıdaki yolda betiği *rebuild.bat* çalıştırın:

> *getting-started\MXChip\AZ3166\tools\rebuild.bat*

Oluşturma tamamlandıktan sonra, ikili dosyanın aşağıdaki yolda oluşturulduğunu doğrulayın:

> *getting-started\MXChip\AZ3166\build\app\ mxchip_azure_iot. bin*

### <a name="flash-the-image"></a>Görüntüyü yakıp söndür

1. MXYONGA DevKit ' te, **sıfırlama** düğmesini ve mikro USB bağlantı noktasını bulun. Aşağıdaki adımlarda bu bileşenleri kullanabilirsiniz. Her ikisi de aşağıdaki resimde vurgulanır:

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/mxchip-iot-devkit.png" alt-text="Mxyonga devkit panosunda anahtar bileşenleri bulma":::

1. Mikro USB kablosunu MXYONGA DevKit üzerindeki mikro USB bağlantı noktasına bağlayın ve bilgisayarınıza bağlayın.
1. Dosya Gezgini 'nde, önceki bölümde oluşturduğunuz ikili dosyayı bulun.
1. *Mxchip_azure_iot. bin* ikili dosyasını kopyalayın.
1. Dosya Gezgini 'nde, bilgisayarınıza bağlı olan MXYONGA DevKit cihazını bulun. Cihaz, sisteminizde sürücü etiketi **AZ3166** olan bir sürücü olarak görünür.
1. İkili dosyayı MXYONGA devkit 'in kök klasörüne yapıştırın. Yanıp sönen otomatik olarak başlar ve birkaç saniye içinde tamamlanır.

    > [!NOTE]
    > Yanıp sönen işlem sırasında MXYONGA DevKit üzerinde yeşil bir LED 'e geçiş yapar.

### <a name="confirm-device-connection-details"></a>Cihaz bağlantısı ayrıntılarını Onayla

İletişimi izlemek ve cihazınızın doğru şekilde ayarlandığını onaylamak için **Termite** uygulamasını kullanabilirsiniz.

1. **Termite**'i başlatın.
    > [!TIP]
    > Termite 'yi devkit 'e bağlayadıysanız, [St-LINK sürücüsünü](https://my.st.com/content/ccc/resource/technical/software/driver/files/stsw-link009.zip) yükleyip yeniden deneyin. Ek adımlar için bkz.  [sorun giderme](https://github.com/azure-rtos/getting-started/blob/master/docs/troubleshooting.md) .
1. **Ayarlar**'ı seçin.
1. **Seri bağlantı noktası ayarları** iletişim kutusunda, aşağıdaki ayarları denetleyin ve gerekirse güncelleştirin:
    * **Baud hızı**: 115.200
    * **Bağlantı noktası**: Mxyonganın devkit 'in bağlı olduğu bağlantı noktası. Açılan listede birden çok bağlantı noktası seçeneği varsa, kullanılacak doğru bağlantı noktasını bulabilirsiniz. Windows **Device Manager** açın ve kullanılacak bağlantı noktasını belirlemek Için **bağlantı noktalarını** görüntüleyin.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/termite-settings.png" alt-text="Termite uygulamasında ayarları onaylama":::

1. Tamam'ı seçin.
1. Cihazdaki **Sıfırla** düğmesine basın. Düğme, cihazda etiketlidir ve mikro USB bağlayıcısının yakınında bulunur.
1. **Termite** uygulamasında, cihazın başlatıldığını ve Azure IoT 'ye bağlandığını onaylamak için aşağıdaki denetim noktası değerlerini denetleyin.

    ```output
    Starting Azure thread

    Initializing WiFi
        Connecting to SSID 'iot'
    SUCCESS: WiFi connected to iot

    Initializing DHCP
        IP address: 10.0.0.123
        Mask: 255.255.255.0
        Gateway: 10.0.0.1
    SUCCESS: DHCP initialized

    Initializing DNS client
        DNS address: 10.0.0.1
    SUCCESS: DNS client initialized

    Initializing SNTP client
        SNTP server 0.pool.ntp.org
        SNTP IP address: 185.242.56.3
        SNTP time update: Nov 16, 2020 23:47:35.385 UTC 
    SUCCESS: SNTP initialized

    Initializing Azure IoT DPS client
        DPS endpoint: global.azure-devices-provisioning.net
        DPS ID scope: ***
        Registration ID: ***
    SUCCESS: Azure IoT DPS client initialized

    Initializing Azure IoT Hub client
        Hub hostname: ***
        Device id: ***
        Model id: dtmi:azurertos:devkit:gsgmxchip;1
    Connected to IoTHub
    SUCCESS: Azure IoT Hub client initialized

    Starting Main loop
    ```

Aşağıdaki adımlarda cihaz çıkışını izlemek için Termite açık tutun.

## <a name="verify-the-device-status"></a>Cihaz durumunu doğrulama

IoT Central portalında cihaz durumunu görüntülemek için:
1. Uygulama panosu ' ndan, yan gezinti menüsünde **cihazlar** ' ı seçin.
1. **Cihaz durumunun** **sağlandı** olarak güncelleştirildiğinden emin olun.
1. **Cihaz şablonunun** **Başlangıç Kılavuzu ' na** güncelleştirildiğinden emin olun.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/iot-central-device-view-status.png" alt-text="IoT Central cihaz durumunu görüntüleme":::

## <a name="view-telemetry"></a>Telemetri görüntüleme

IoT Central sayesinde, cihazınızdan buluta telemetri akışını görüntüleyebilirsiniz.

IoT Central portalında Telemetriyi görüntülemek için:

1. Uygulama panosu ' ndan, yan gezinti menüsünde **cihazlar** ' ı seçin.
1. Cihaz listesinden cihazı seçin.
1. **Genel bakış** sekmesinde cihaz buluta ileti gönderdiğinden Telemetriyi görüntüleyin.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/iot-central-device-telemetry.png" alt-text="IoT Central cihaz telemetrisini görüntüleme":::

    > [!NOTE]
    > Ayrıca, Termite uygulamasını kullanarak cihazdan Telemetriyi izleyebilirsiniz.

## <a name="call-a-direct-method-on-the-device"></a>Cihazda doğrudan yöntem çağırma

Ayrıca, cihazınızda uygulamış olduğunuz bir doğrudan yöntemi çağırmak için IoT Central de kullanabilirsiniz. Doğrudan yöntemlerin bir adı vardır ve isteğe bağlı olarak bir JSON yükü, yapılandırılabilir bağlantı ve yöntem zaman aşımı olabilir. Bu bölümde, bir ışığı açık veya kapalı yapmanızı sağlayan bir yöntemi çağırın.

IoT Central portalında bir yöntemi çağırmak için:

1. Cihaz sayfasından **komut** sekmesini seçin.
1. **Durum** ' u seçin ve **Çalıştır**' ı seçin.  LED ışığı açık olmalıdır.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/iot-central-invoke-method.png" alt-text="Bir cihazda doğrudan yöntem çağırma":::

1. **Durum** seçimini kaldırın ve **Çalıştır**' ı seçin. LED ışığı devre dışı bırakılmalıdır.

## <a name="view-device-information"></a>Cihaz bilgilerini görüntüle

Cihaz bilgilerini IoT Central görüntüleyebilirsiniz.

Cihaz sayfasından **hakkında** sekmesini seçin.

:::image type="content" source="media/quickstart-devkit-mxchip-az3166/iot-central-device-about.png" alt-text="IoT Central cihaz hakkındaki bilgileri görüntüleme":::

## <a name="debugging"></a>Hata Ayıklama

Uygulamada hata ayıklamak için bkz. [Visual Studio Code Ile hata ayıklama](https://github.com/azure-rtos/getting-started/blob/master/docs/debugging.md).

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu öğreticide oluşturulan Azure kaynaklarına artık ihtiyacınız yoksa, IoT Central portalından silebilirsiniz. İsteğe bağlı olarak, bu başlangıç kılavuzunda başka bir öğreticiye devam ederseniz, önceden oluşturduğunuz kaynakları tutabilir ve yeniden kullanabilirsiniz.

Azure IoT Central örnek uygulamasının tamamını ve tüm cihazlarını ve kaynaklarını kaldırmak için:
1. Uygulamanızı **yönetme** seçeneğini belirleyin  >  .
1. **Sil**’i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Azure RTOS örnek kodunu içeren özel bir görüntü oluşturup, ardından görüntüyü MXYONGA DevKit cihazına düzflayoruz. Azure kaynakları oluşturmak, MXYONGADEVKIT 'i güvenli bir şekilde Azure 'a bağlamak, Telemetriyi görüntülemek ve ileti göndermek için IoT Central portalını da kullandınız.

* Cihaz geliştiricileri için önerilen sonraki adım, [Azure IoT Embedded cihaz geliştirmesini](quickstart-device-development.md)kullanmaya başlarken serideki diğer öğreticileri görmektedir.
* Cihazınızı bu kılavuzdaki adımları izleyerek başlatmak veya bağlanmak için alma sorunlarıyla karşılaşırsanız bkz. [sorun giderme](https://github.com/azure-rtos/getting-started/blob/master/docs/troubleshooting.md).
* Azure RTOS bileşenlerinin Bu öğretici için örnek kodda nasıl kullanıldığı hakkında daha fazla bilgi edinmek için bkz. Başlarken [kılavuzunda Azure RTOS kullanma](https://github.com/azure-rtos/getting-started/blob/master/docs/using-azure-rtos.md).

    > [!IMPORTANT]
    > Azure RTOS, OEM 'Lere iletişim sağlamak ve temel alınan MCU/MPU donanım koruma mekanizmalarını kullanarak kod ve veri yalıtımı oluşturmak için bileşenleri sağlar. Ancak, her OEM son olarak cihazın gelişen güvenlik gereksinimlerini karşıladığından emin olmanın sorumluluğundadır.

