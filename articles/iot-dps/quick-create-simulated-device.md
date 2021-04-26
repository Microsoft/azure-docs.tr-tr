---
title: 'Hızlı başlangıç: C kullanarak Azure IoT Hub sanal bir TPM cihazı sağlama'
description: Bu hızlı başlangıçta bireysel kayıtlar kullanılmaktadır. Bu hızlı başlangıçta, Azure IoT Hub cihaz sağlama hizmeti (DPS) için C cihaz SDK 'sını kullanarak sanal bir TPM cihazı oluşturup hazırlarsınız.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.custom:
- mvc
- amqp
- mqtt
ms.openlocfilehash: e2930a3ca2ecb9d8217fdfea1cbcb0e669f61775
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94960045"
---
# <a name="quickstart-provision-a-simulated-tpm-device-using-the-azure-iot-c-sdk"></a>Hızlı başlangıç: Azure IoT C SDK'sını kullanarak simülasyon TPM cihazı sağlama

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-tpm](../../includes/iot-dps-selector-quick-create-simulated-device-tpm.md)]

Bu hızlı başlangıçta, Windows geliştirme makinesi üzerinde Güvenilir Platform Modülü (TPM) cihazı simülatörünü oluşturmayı ve çalıştırmayı öğreneceksiniz. Cihaz Sağlama Hizmeti örneğini kullanarak bu simülasyon cihazını bir IoT hub'ına bağlayacaksınız. Cihazı Cihaz Sağlama Hizmeti örneğine kaydetmek ve cihaz için bir önyükleme sırası simülasyonu yapmak için [Azure IoT C SDK'sındaki](https://github.com/Azure/azure-iot-sdk-c) örnek kodu kullanacaksınız.

Oto sağlama işlemini bilmiyorsanız, [sağlamaya](about-iot-dps.md#provisioning-process) genel bakış konusunu gözden geçirin. Ayrıca, bu hızlı başlangıçla devam etmeden önce [IoT Hub Cihazı Sağlama Hizmetini Azure portalla ayarlama](./quick-setup-auto-provision.md) bölümünde bulunan adımları tamamladığınızdan emin olun. 

Azure IoT Cihaz Sağlama Hizmeti iki tür kaydı destekler:
- [Kayıt grupları](concepts-service.md#enrollment-group): Birden fazla ilgili cihazı kaydetmek için kullanılır.
- [Bireysel kayıtlar](concepts-service.md#individual-enrollment): Tek bir cihazı kaydetmek için kullanılır.

Bu makalede bireysel kayıtlar gösterilmektedir.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Önkoşullar

Aşağıdaki Önkoşullar bir Windows geliştirme ortamı içindir. Linux veya macOS için SDK belgelerinde [geliştirme ortamınızı hazırlama](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) konusunun ilgili bölümüne bakın.

* [' C++ Ile masaüstü geliştirme '](/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development) iş yükünün etkin olduğu [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019. Visual Studio 2015 ve Visual Studio 2017 de desteklenir.

* [Git](https://git-scm.com/download/)'in en son sürümünün yüklemesi.

<a id="setupdevbox"></a>

## <a name="prepare-a-development-environment-for-the-azure-iot-c-sdk"></a>Azure IoT C SDK'sı için geliştirme ortamını hazırlama

Bu bölümde, [Azure IoT C SDK'sı](https://github.com/Azure/azure-iot-sdk-c) ile [TPM](/windows/device-security/tpm/trusted-platform-module-overview) cihaz simülatör örneğini oluşturmak için kullanılan geliştirme ortamını hazırlayacaksınız.

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

## <a name="build-the-sdk-and-run-the-tpm-device-simulator"></a>SDK'yı derleme ve TPM cihaz simülatörünü çalıştırma

Bu bölümde, TPM cihaz simülatörü örnek kodunu içeren Azure IoT C SDK'sını oluşturacaksınız. Bu örnek, Paylaşılan Erişim İmzası (SAS) Belirteci kimlik doğrulaması yoluyla bir TPM [kanıtlama mekanizması](concepts-service.md#attestation-mechanism) sağlar.

1. azure-iot-sdk-c git deposunda oluşturduğunuz `cmake` alt dizininden aşağıdaki komutu çalıştırarak örneği derleyin. Bu derleme komutuyla simülasyon cihazı için bir de Visual Studio çözümü oluşturulur.

    ```cmd/sh
    cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
    ```

    `cmake`, C++ derleyicinizi bulamazsa yukarıdaki komutu çalıştırırken derleme hatalarıyla karşılaşabilirsiniz. Bu durumda bu komutu [Visual Studio komut isteminde](/dotnet/framework/tools/developer-command-prompt-for-vs) çalıştırmayı deneyin. 

    Derleme başarılı olduktan sonra, son birkaç çıkış satırı aşağıdaki çıkışa benzer olacaktır:

    ```cmd/sh
    $ cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```

2. Kopyaladığınız git deposunun kök klasörüne gidin ve aşağıda gösterilen yolu kullanarak [TPM](/windows/device-security/tpm/trusted-platform-module-overview) simülatörünü çalıştırın. Bu simülatör 2321 ve 2322 bağlantı noktalarında bulunan bir yuva üzerinden dinler. Bu komut penceresini kapatmayın; bu hızlı başlangıcın sonuna kadar simülatörü çalışır durumda tutmanız gerekir. 

   *cmake* klasöründeyseniz aşağıdaki komutları çalıştırın:

    ```cmd/sh
    cd ..
    .\provisioning_client\deps\utpm\tools\tpm_simulator\Simulator.exe
    ```

    Simülatörden hiçbir çıkış görmeyeceksiniz. TPM cihazının simülasyonunu yaparak çalışmaya devam etmesine izin verin.

<a id="simulatetpm"></a>

## <a name="read-cryptographic-keys-from-the-tpm-device"></a>TPM cihazından şifreleme anahtarlarını okuma

Bu bölümde, çalışır ve 2321 ile 2322 numaralı bağlantı noktaları üzerinden dinler durumda bıraktığınız TPM simülatöründen onay anahtarını ve kayıt kimliğini okuyan bir örnek derleyecek ve bu örneği yürüteceksiniz. Bu değerler, Cihaz Sağlama Hizmeti örneğinize cihaz kaydı için kullanılacak.

1. Visual Studio’yu başlatın ve `azure_iot_sdks.sln` adlı yeni çözüm dosyasını açın. Bu çözüm dosyası daha önce azure-iot-sdk-c git deposunun kökünde oluşturduğunuz `cmake` klasöründe yer alır.

2. Visual Studio menüsünde,   >  Çözümdeki tüm projeleri derlemek için Build **Build Solution** öğesini seçin.

3. Visual Studio'nun *Çözüm Gezgini* penceresinde **Sağlama\_Araçları** klasörüne gidin. **tpm_device_provision** projesine sağ tıklayın ve **Başlangıç Projesi Olarak Ayarla**’yı seçin. 

4. Çözümü çalıştırmak için Visual Studio menüsünde Hata **ayıklama**  >  **olmadan Başlat** ' ı seçin. Uygulama bir **_kayıt kimliği_** ve bir **_onay anahtarı_** okur ve görüntüler. Veya bu değerleri kopyalayın. Bunlar, bir sonraki bölümde cihaz kaydı için kullanılacaktır. 


<a id="portalenrollment"></a>

## <a name="create-a-device-enrollment-entry-in-the-portal"></a>Portalda bir cihaz kaydı girişi oluşturma

1. Azure portal oturum açın, sol taraftaki menüden **tüm kaynaklar** düğmesini seçin ve cihaz sağlama hizmetinizi açın.

1. Kayıtları **Yönet** sekmesini seçin ve ardından üst kısımdaki **tek kayıt Ekle** düğmesini seçin. 

1. **Kayıt Ekle** panelinde, aşağıdaki bilgileri girin:
   - Kimlik onay *Mekanizması* olarak **TPM** seçeneğini belirleyin.
   - Daha önce not ettiğiniz değerlerden TPM cihazınızın *kayıt kimliği* ve *onay anahtarı* ' nı girin.
   - Sağlama hizmetinizle bağlanacak IoT hub'ını seçin.
   - İsteğe bağlı olarak, aşağıdaki bilgileri sağlayabilirsiniz:
       - Benzersiz bir *CIHAZ kimliği* girin (önerilen **Test-docs-Device** ' i kullanabilir veya kendi kendinizinkini sağlayabilirsiniz). Cihazınızı adlandırırken gizli veriler kullanmaktan kaçının. Bir tane sağlamadıysanız, bunun yerine cihazı tanımlamak için kayıt KIMLIĞI kullanılır.
       - **Başlangıç cihaz ikizi durumu** alanını cihaz için istenen başlangıç yapılandırmasına göre güncelleştirin.
   - Tamamlandıktan sonra **Kaydet** düğmesine basın. 

      ![Portala cihaz kayıt bilgilerini girme](./media/quick-create-simulated-device/enter-device-enrollment.png)  

      Kayıt başarıyla tamamlanınca cihazınızın *Kayıt Kimliği* listenin altında *Tek Tek Kayıtlar* sekmesinde görünür. 


<a id="firstbootsequence"></a>

## <a name="simulate-first-boot-sequence-for-the-device"></a>Cihazın ilk önyükleme sırasını benzetim olarak çalıştırma

Bu bölümde, örnek kodu yapılandırarak cihazın önyükleme sırasını Cihaz Sağlama Hizmeti örneğinize göndermek üzere [Gelişmiş İleti Sıraya Alma Protokolü (AMQP)](https://wikipedia.org/wiki/Advanced_Message_Queuing_Protocol) kullanmasını sağlayacaksınız. Bu önyükleme sırası cihazın tanınmasına ve Cihaz Sağlama Hizmeti örneğine bağlı bir IoT hub'ına atanmasına neden olur.

1. Azure portalda, Cihaz Sağlama hizmetiniz için **Genel Bakış** sekmesini seçin ve **_Kimlik Kapsamı_** değerini kopyalayın.

    ![Portaldan Cihaz Sağlama Hizmeti uç noktası bilgilerini ayıklama](./media/quick-create-simulated-device/extract-dps-endpoints.png) 

2. Visual Studio'nun *Çözüm Gezgini* penceresinde **Sağlama\_Örnekleri** klasörüne gidin. **prov\_dev\_client\_sample** adlı örnek projeyi genişletin. **Kaynak Dosyalar**'ı genişletin ve **prov\_dev\_client\_sample.c** dosyasını açın.

3. Dosyanın üst kısmında, aşağıda gösterildiği gibi her cihaz protokolü için `#define` deyimlerini bulun. Yalnızca `SAMPLE_AMQP` öğesinin açıklama haline getirilmediğinden emin olun.

    Şu anda, [TPM Bireysel Kaydı için MQTT protokolü desteklenmemektedir](https://github.com/Azure/azure-iot-sdk-c#provisioning-client-sdk).

    ```c
    //
    // The protocol you wish to use should be uncommented
    //
    //#define SAMPLE_MQTT
    //#define SAMPLE_MQTT_OVER_WEBSOCKETS
    #define SAMPLE_AMQP
    //#define SAMPLE_AMQP_OVER_WEBSOCKETS
    //#define SAMPLE_HTTP
    ```

4. `id_scope` sabitini bulun ve değeri daha önce kopyalamış olduğunuz **Kimlik Kapsamı** değerinizle değiştirin. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

5. Aynı dosyada `main()` işlevinin tanımını bulun. `hsm_type` değişkeninin aşağıda gösterildiği gibi `SECURE_DEVICE_TYPE_X509` yerine `SECURE_DEVICE_TYPE_TPM` değerine ayarlandığından emin olun.

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    ```

6. **prov\_dev\_client\_sample** projesine sağ tıklayın ve **Başlangıç Projesi Olarak Ayarla**’yı seçin. 

7. Çözümü çalıştırmak için Visual Studio menüsünde Hata **ayıklama**  >  **olmadan Başlat** ' ı seçin. Projeyi yeniden oluşturmak için istemde, çalıştırmadan önce projeyi yeniden derlemek için **Evet**' i seçin.

    Aşağıdaki çıkış, başarıyla önyüklemesi yapılan cihaz istemci örneğini sağlama ve IoT hub bilgilerini almak üzere Cihaz Sağlama Hizmeti'ne bağlanıp kaydolma işlemlerinin bir örneğidir:

    ```cmd
    Provisioning API Version: 1.2.7
    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED

    Registering... Press enter key to interrupt.

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service:
    test-docs-hub.azure-devices.net, deviceId: test-docs-device
    ```

8. Sanal cihaz, sağlama hizmetiniz tarafından IoT Hub 'ına sağlandıktan sonra, hub 'ın **IoT cihazlarıyla** bırlıkte cihaz kimliği görüntülenir. 

    ![Cihaz IOT hub'da kayıtlı](./media/quick-create-simulated-device/hub-registration.png) 


## <a name="clean-up-resources"></a>Kaynakları temizleme

Cihaz istemci örneğini üzerinde çalışmaya ve keşfetmeye devam etmeyi planlıyorsanız, bu hızlı başlangıçta oluşturulan kaynakları temizlemeyin. Devam etmeyi planlamıyorsanız, bu hızlı başlangıç tarafından oluşturulan tüm kaynakları silmek için aşağıdaki adımları kullanın.

1. Makinenizde cihaz istemci örnek çıktı penceresini kapatın.
2. Makinenizde TPM simülatörü penceresini kapatın.
3. Azure portal sol taraftaki menüden **tüm kaynaklar** ' ı seçin ve ardından cihaz sağlama hizmetinizi seçin. Hizmetiniz için kayıtları **Yönet** ' i açın ve **bireysel** kayıtlar sekmesini seçin. Bu hızlı başlangıçta kaydettiğiniz cihazın *kayıt kimliği* ' nin yanındaki onay kutusunu işaretleyin ve bölmenin en üstündeki **Sil** düğmesine basın. 
4. Azure portal sol taraftaki menüden **tüm kaynaklar** ' ı seçin ve ardından IoT Hub 'ınızı seçin. , Hub 'ınız için **IoT cihazlarını** açın, bu hızlı başlangıçta kaydettiğiniz CIHAZıN *cihaz kimliği* ' nin yanındaki onay kutusunu işaretleyin ve ardından bölmenin en üstündeki **Sil** düğmesine basın.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, makinenizde bir TPM sanal cihazı oluşturdunuz ve IoT Hub cihaz sağlama hizmetini kullanarak IoT Hub 'ınıza sağladınız. TPM cihazınızı programlı bir şekilde kaydetmeyi öğrenmek için bir TPM cihazının programlı kaydı için hızlı başlangıç 'a geçin. 

> [!div class="nextstepaction"]
> [Azure hızlı başlangıç-TPM cihazını Azure IoT Hub cihaz sağlama hizmeti 'ne kaydetme](quick-enroll-device-tpm-java.md)