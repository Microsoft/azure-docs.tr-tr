---
title: Hızlı başlangıç-C kullanarak sanal bir X. 509.440 cihazını Azure IoT Hub sağlama
description: Bu hızlı başlangıçta bireysel kayıtlar kullanılmaktadır. Bu hızlı başlangıçta, Azure IoT Hub cihaz sağlama hizmeti (DPS) için C cihaz SDK 'sını kullanarak sanal bir X. 509.952 cihazı oluşturup hazırlarsınız.
author: wesmc7777
ms.author: wesmc
ms.date: 04/26/2020
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: 8f4c35a61edc970f5d4d3beb0c4255ccfe4e336a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94968154"
---
# <a name="quickstart-provision-an-x509-simulated-device-using-the-azure-iot-c-sdk"></a>Hızlı başlangıç: Azure IoT C SDK'sını kullanarak simülasyon X.509 cihazı sağlama

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

Bu hızlı başlangıçta bir Windows geliştirme makinesi üzerinde bir X.509 cihazı simülatörü oluşturmayı ve çalıştırmayı öğreneceksiniz. Cihaz Sağlama Hizmeti örneği ile bir kayıt kullanarak bir IoT hub'ına atanacak bu simülasyon cihazını yapılandıracaksınız. Cihaz için önyükleme sırası simülasyonu yapmak için [Azure IoT C SDK'sından](https://github.com/Azure/azure-iot-sdk-c) alınan örnek kod kullanılacaktır. Cihaz, sağlama hizmeti ile kayıt durumuna göre tanınacak ve IoT hub'ına atanacaktır.

Oto sağlama işlemini bilmiyorsanız, [sağlamaya](about-iot-dps.md#provisioning-process) genel bakış konusunu gözden geçirin. Ayrıca, bu hızlı başlangıçla devam etmeden önce [IoT Hub Cihazı Sağlama Hizmetini Azure portalla ayarlama](quick-setup-auto-provision.md) bölümünde bulunan adımları tamamladığınızdan emin olun. 

Azure IoT Cihaz Sağlama Hizmeti iki tür kaydı destekler:

* [Kayıt grupları](concepts-service.md#enrollment-group): Birden fazla ilgili cihazı kaydetmek için kullanılır.
* [Bireysel kayıtlar](concepts-service.md#individual-enrollment): Tek bir cihazı kaydetmek için kullanılır.

Bu makalede bireysel kayıtlar gösterilmektedir.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Önkoşullar

Aşağıdaki Önkoşullar bir Windows geliştirme ortamı içindir. Linux veya macOS için SDK belgelerinde [geliştirme ortamınızı hazırlama](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) konusunun ilgili bölümüne bakın.

* [' C++ Ile masaüstü geliştirme '](/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development) iş yükünün etkin olduğu [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019. Visual Studio 2015 ve Visual Studio 2017 de desteklenir.

* [Git](https://git-scm.com/download/)'in en son sürümünün yüklemesi.

## <a name="prepare-a-development-environment-for-the-azure-iot-c-sdk"></a>Azure IoT C SDK'sı için geliştirme ortamını hazırlama

Bu bölümde, X. 509.440 önyükleme sırasının örnek kodunu Içeren [Azure IoT C SDK 'sını](https://github.com/Azure/azure-iot-sdk-c)oluşturmak için kullanılan bir geliştirme ortamı hazırlayacaksınız.

1. [CMake derleme sistemini](https://cmake.org/download/)indirin.

    `CMake` yüklemesine başlamadan **önce** makinenizde Visual Studio önkoşullarının (Visual Studio ve "C++ ile masaüstü geliştirme" iş yükü) yüklenmiş olması önemlidir. Önkoşullar sağlandıktan ve indirme doğrulandıktan sonra, CMake derleme sistemini yükleyin.

2. SDK 'nın [en son sürümü](https://github.com/Azure/azure-iot-sdk-c/releases/latest) için etiket adını bulun.

3. Komut istemini veya Git Bash kabuğunu açın. [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub deposunun en son sürümünü kopyalamak için aşağıdaki komutları çalıştırın. Önceki adımda bulunan etiketini parametre değeri olarak kullanın `-b` :

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Bu işlemin tamamlanması için birkaç dakika beklemeniz gerekebilir.

4. Git deposunun kök dizininde bir `cmake` alt dizini oluşturun ve o klasöre gidin. Dizininden aşağıdaki komutları çalıştırın `azure-iot-sdk-c` :

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

5. Kod örneği, X.509 kimlik doğrulaması aracılığıyla kanıtlama sağlamak için bir X.509 sertifikası kullanır. Bir SDK 'nın cihaz sağlama istemcisini içeren geliştirme platformunuza özgü bir sürümünü oluşturmak için aşağıdaki komutu çalıştırın. Sanal cihaz için bir Visual Studio çözümü, `cmake` dizininde oluşturulur.

    ```cmd
    cmake -Duse_prov_client:BOOL=ON ..
    ```

    `cmake`, C++ derleyicinizi bulamazsa yukarıdaki komutu çalıştırırken derleme hatalarıyla karşılaşabilirsiniz. Bu durumda bu komutu [Visual Studio komut isteminde](/dotnet/framework/tools/developer-command-prompt-for-vs) çalıştırmayı deneyin.

    Derleme başarılı olduktan sonra, son birkaç çıktı satırı aşağıdaki çıktıya benzer şekilde görünür:

    ```cmd/sh
    $ cmake -Duse_prov_client:BOOL=ON ..
    -- Building for: Visual Studio 16 2019
    -- The C compiler identification is MSVC 19.23.28107.0
    -- The CXX compiler identification is MSVC 19.23.28107.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: C:/code/azure-iot-sdk-c/cmake
    ```

## <a name="create-a-self-signed-x509-device-certificate"></a>Otomatik olarak imzalanan X.509 sertifikası oluşturma

Bu bölümde otomatik olarak imzalanan bir X.509 sertifikası kullanacaksınız. Aşağıdaki noktaları göz önünde bulundurmak önemlidir:

* Otomatik olarak imzalanan sertifikalar yalnızca test amaçlıdır ve üretimde kullanılmamalıdır.
* Otomatik olarak imzalanan sertifikanın varsayılan sona erme tarihi bir yıldır.

Benzetim cihazının tek kayıt girdisiyle kullanılacak sertifikayı oluşturmak için Azure IoT C SDK'sından örnek kodu kullanacaksınız.

1. Visual Studio’yu başlatın ve `azure_iot_sdks.sln` adlı yeni çözüm dosyasını açın. Bu çözüm dosyası daha önce azure-iot-sdk-c git deposunun kökünde oluşturduğunuz `cmake` klasöründe yer alır.

2. Visual Studio menüsünde,   >  Çözümdeki tüm projeleri derlemek için Build **Build Solution** öğesini seçin.

3. Visual Studio'nun *Çözüm Gezgini* penceresinde **Sağlama\_Araçları** klasörüne gidin. **dice\_device\_enrollment** projesine sağ tıklayın ve **Başlangıç Projesi Olarak Ayarla**’yı seçin.

4. Çözümü çalıştırmak için Visual Studio menüsünde Hata **ayıklama**  >  **olmadan Başlat** ' ı seçin. Çıktı penceresinde, istendiğinde tek kayıt için **i** girin.

    Çıktı penceresi, sanal cihazınız için yerel olarak oluşturulmuş otomatik olarak imzalanan X.509 sertifikasını görüntüler. **-----BEGIN CERTIFICATE-----** ile başlayıp ilk **-----END PUBLIC KEY-----** ile biten çıktıyı panoya kopyalayın ve bu iki satırı da dahil ettiğinizden emin olun. Yalnızca çıktı penceresindeki ilk sertifikayı kopyalamanız gerekir.

5. Metin düzenleyicisi kullanarak sertifikayı **_X509testcert.pem_** adlı yeni bir dosyaya kaydedin.

## <a name="create-a-device-enrollment-entry-in-the-portal"></a>Portalda bir cihaz kaydı girişi oluşturma

1. Azure portal oturum açın, sol taraftaki menüden **tüm kaynaklar** düğmesini seçin ve cihaz sağlama hizmetinizi açın.

2. Kayıtları **Yönet** sekmesini seçin ve ardından üst kısımdaki **tek kayıt Ekle** düğmesini seçin.

3. **Kayıt Ekle** panelinde, aşağıdaki bilgileri girin ve **Kaydet** düğmesine basın.

    * **Mekanizma:** Kimlik onay *Mekanizması* olarak **X.509**'u seçin.
    * **Birincil sertifika. pek veya. cer dosyası:** Daha önce oluşturduğunuz X509testcert. pek adlı sertifika dosyasını seçmek için **Dosya Seç** ' i seçin.
    * **IoT Hub Cihaz kimliği:** Cihaza bir kimlik vermek için **test-docs-cert-device** girin.

      [![Portalda X. 509.440 kanıtlama için bireysel kayıt ekleme](./media/quick-create-simulated-device-x509/device-enrollment.png)](./media/quick-create-simulated-device-x509/device-enrollment.png#lightbox)

      Kayıt başarıyla tamamlandığında, X.509 cihazınız **Bireysel Kayıtlar** sekmesindeki *Kayıt Kimliği* sütununun altında *riot-device-cert* olarak gösterilir. 

## <a name="simulate-first-boot-sequence-for-the-device"></a>Cihazın ilk önyükleme sırasını benzetim olarak çalıştırma

Bu bölümde cihazın önyükleme sırasını Cihaz Sağlama Hizmeti örneğinize göndermek için örnek kodu güncelleştireceksiniz. Bu önyükleme sırası cihazın tanınmasına ve Cihaz Sağlama Hizmeti örneğine bağlı bir IoT hub'ına atanmasına neden olur.

1. Azure portal, cihaz sağlama hizmetiniz için **genel bakış** sekmesini seçin ve **_kimlik kapsamı_** değerini aklınızda yapın.

    ![Portal dikey penceresinden Cihaz Sağlama Hizmeti uç noktası bilgilerini ayıklama](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. Visual Studio'nun *Çözüm Gezgini* penceresinde **Sağlama\_Örnekleri** klasörüne gidin. **prov\_dev\_client\_sample** adlı örnek projeyi genişletin. **Kaynak Dosyalar**'ı genişletin ve **prov\_dev\_client\_sample.c** dosyasını açın.

3. `id_scope` sabitini bulun ve değeri daha önce kopyalamış olduğunuz **Kimlik Kapsamı** değerinizle değiştirin. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

4. Aynı dosyada `main()` işlevinin tanımını bulun. `hsm_type` değişkeninin aşağıda gösterildiği gibi `SECURE_DEVICE_TYPE_TPM` yerine `SECURE_DEVICE_TYPE_X509` değerine ayarlandığından emin olun.

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    hsm_type = SECURE_DEVICE_TYPE_X509;
    ```

5. **prov\_dev\_client\_sample** projesine sağ tıklayın ve **Başlangıç Projesi Olarak Ayarla**’yı seçin.

6. Çözümü çalıştırmak için Visual Studio menüsünde Hata **ayıklama**  >  **olmadan Başlat** ' ı seçin. Projeyi yeniden oluşturmak için istemde, çalıştırmadan önce projeyi yeniden derlemek için **Evet** ' i seçin.

    Aşağıdaki çıkış, başarıyla önyüklemesi yapılan cihaz istemci örneğini sağlama ve IoT hub bilgilerini almak üzere sağlama Hizmeti örneğine bağlanıp kaydolma işlemlerinin bir örneğidir:

    ```cmd
    Provisioning API Version: 1.2.7

    Registering... Press enter key to interrupt.

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service:
    test-docs-hub.azure-devices.net, deviceId: test-docs-cert-device
    ```

7. Portalda, sağlama hizmetinize bağlı olan IoT Hub 'ına gidin ve **IoT cihazları** sekmesini seçin. Benzetimli X. 509.952 cihazının hub 'a başarıyla sağlanması sırasında cihaz KIMLIĞI **IoT cihazları** dikey penceresinde *durumu* **etkinleştirilmiş** olarak görünür. Üstteki **Yenile** düğmesine basmanız gerekebilir. 

    ![Cihaz IOT hub'da kayıtlı](./media/quick-create-simulated-device-x509/hub-registration.png) 

## <a name="clean-up-resources"></a>Kaynakları temizleme

Cihaz istemci örneğini üzerinde çalışmaya ve keşfetmeye devam etmeyi planlıyorsanız, bu hızlı başlangıçta oluşturulan kaynakları temizlemeyin. Devam etmeyi planlamıyorsanız, bu hızlı başlangıç tarafından oluşturulan tüm kaynakları silmek için aşağıdaki adımları kullanın.

1. Makinenizde cihaz istemci örnek çıktı penceresini kapatın.
1. Azure portal sol taraftaki menüden **tüm kaynaklar** ' ı seçin ve ardından cihaz sağlama hizmetinizi seçin. Hizmetiniz için kayıtları **Yönet** ' i açın ve **bireysel** kayıtlar sekmesini seçin. Bu hızlı başlangıçta kaydettiğiniz cihazın *kayıt kimliği* ' nin yanındaki onay kutusunu işaretleyin ve bölmenin en üstündeki **Sil** düğmesine basın. 
1. Azure portal sol taraftaki menüden **tüm kaynaklar** ' ı seçin ve ardından IoT Hub 'ınızı seçin. , Hub 'ınız için **IoT cihazlarını** açın, bu hızlı başlangıçta kaydettiğiniz CIHAZıN *cihaz kimliği* ' nin yanındaki onay kutusunu işaretleyin ve ardından bölmenin en üstündeki **Sil** düğmesine basın.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, Windows makinenizde sanal bir X. 509.952 cihazı oluşturdunuz ve portaldaki Azure IoT Hub cihaz sağlama hizmetini kullanarak IoT Hub 'ınıza sağladınız. X. 509.952 cihazınızı programlı bir şekilde kaydetmeyi öğrenmek için, X. 509.440 cihazlarının programlı kaydı için hızlı başlangıç 'a geçin. 

> [!div class="nextstepaction"]
> [Azure hızlı başlangıç-X. 509.440 cihazlarını Azure IoT Hub cihaz sağlama hizmeti 'ne kaydetme](quick-enroll-device-x509-java.md)