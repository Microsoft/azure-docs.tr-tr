---
title: Hızlı başlangıç-C kullanarak Azure IoT Hub cihazları sağlamak için simetrik anahtar kullanın
description: Bu hızlı başlangıçta, Azure IoT Hub cihaz sağlama hizmeti (DPS) ile simetrik anahtar kullanan bir cihaz sağlamak için C cihaz SDK 'sını kullanacaksınız
author: wesmc7777
ms.author: wesmc
ms.date: 01/14/2020
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 7df7c9ab6bfbc8a39050b78a76114ae2a0a9d9b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96746514"
---
# <a name="quickstart-provision-a-device-with-symmetric-keys"></a>Hızlı başlangıç: simetrik anahtarlarla cihaz sağlama

Bu hızlı başlangıçta, bir Windows geliştirme makinesinde Cihaz sağlama kodunun nasıl çalıştırılacağını IoT cihazı olarak bir IoT Hub bağlamak öğreneceksiniz. Bu cihazı bir cihaz sağlama hizmeti örneğiyle simetrik anahtar kimlik doğrulaması kullanacak ve bir IoT Hub 'ına atanacak şekilde yapılandıracaksınız. [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) 'sindeki örnek kod, cihazı sağlamak için kullanılacaktır. Cihaz, sağlama hizmeti örneği ile ayrı kayıt durumuna göre tanınacak ve bir IoT hub'ına atanacaktır.

Bu makalede tek bir kayıt ile sağlama işlemi gösterilmektedir, ancak kayıt gruplarını kullanabilirsiniz. Kayıt grupları kullanılırken bazı farklılıklar vardır. Örneğin, cihaz için benzersiz bir kayıt KIMLIĞIYLE türetilmiş bir cihaz anahtarı kullanmanız gerekir. Simetrik anahtar kayıt grupları eski cihazlarla sınırlı olmasa da, [Simetrik anahtar kanıtı kullanarak eski cihazları sağlama](how-to-legacy-device-symm-key.md) bölümünde bir kayıt grubu örneği sağlanmaktadır. Daha fazla bilgi için, bkz. [Simetrik Anahtar Kanıtlama için Grup Kayıtları](concepts-symmetric-key-attestation.md#group-enrollments).

Oto sağlama işlemini bilmiyorsanız, [sağlamaya](about-iot-dps.md#provisioning-process) genel bakış konusunu gözden geçirin. 

Ayrıca, bu hızlı başlangıçla devam etmeden önce [IoT Hub Cihazı Sağlama Hizmetini Azure portalla ayarlama](./quick-setup-auto-provision.md) bölümünde bulunan adımları tamamladığınızdan emin olun. Bu hızlı başlangıç, Cihaz Sağlama Hizmeti örneğinizi zaten oluşturmuş olmanızı gerektirir.

Bu makale Windows tabanlı bir iş istasyonuna yöneliktir. Ancak yordamları Linux üzerinde gerçekleştirebilirsiniz. Bir Linux örneği için, bkz. [Çoklu kiracı için sağlama](how-to-provision-multitenant.md).


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Önkoşullar

Aşağıdaki Önkoşullar bir Windows geliştirme ortamı içindir. Linux veya macOS için SDK belgelerinde [geliştirme ortamınızı hazırlama](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) konusunun ilgili bölümüne bakın.

* [' C++ Ile masaüstü geliştirme '](/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development) iş yükünün etkin olduğu [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019. Visual Studio 2015 ve Visual Studio 2017 de desteklenir.

* [Git](https://git-scm.com/download/)'in en son sürümünün yüklemesi.

<a id="setupdevbox"></a>

## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>Azure IoT C SDK'sı için geliştirme ortamını hazırlama

Bu bölümde, [Azure IoT C SDK'sını](https://github.com/Azure/azure-iot-sdk-c) oluşturmak için kullanılan geliştirme ortamını hazırlayacaksınız. 

SDK, cihazlar için sağlama örnek kodunu içerir. Bu kod, cihazın önyükleme sırası sırasında sağlamayı dener.

1. [CMake derleme sistemini](https://cmake.org/download/)indirin.

    `CMake` yüklemesine başlamadan **önce** makinenizde Visual Studio önkoşullarının (Visual Studio ve "C++ ile masaüstü geliştirme" iş yükü) yüklenmiş olması önemlidir. Önkoşullar sağlandıktan ve indirme doğrulandıktan sonra, CMake derleme sistemini yükleyin.

    CMake derleme sisteminin daha eski sürümleri bu makalede kullanılan çözüm dosyasını oluşturamıyor. CMake 'in daha yeni bir sürümünü kullandığınızdan emin olun.

2. **Etiketler** ' e tıklayın ve [Azure IoT C SDK 'sının yayın sayfasında](https://github.com/Azure/azure-iot-sdk-c/releases/latest)en son sürüm için etiket adını bulun.

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

5. SDK’nın geliştirme istemci platformunuza ve özgü bir sürümünü oluşturmak için aşağıdaki komutu çalıştırın. Dizinde, cihaz sağlama kodu için bir Visual Studio çözümü oluşturulacaktır `cmake` . 

    ```cmd
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```
    
    `cmake`, C++ derleyicinizi bulamazsa yukarıdaki komutu çalıştırırken derleme hatalarıyla karşılaşabilirsiniz. Bu durumda bu komutu [Visual Studio komut isteminde](/dotnet/framework/tools/developer-command-prompt-for-vs) çalıştırmayı deneyin. 

    Derleme başarılı olduktan sonra, son birkaç çıkış satırı aşağıdaki çıkışa benzer olacaktır:

    ```cmd/sh
    $ cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```

## <a name="create-a-device-enrollment-entry-in-the-portal"></a>Portalda bir cihaz kaydı girişi oluşturma

1. [Azure Portal](https://portal.azure.com)oturum açın, sol taraftaki menüden **tüm kaynaklar** düğmesini seçin ve cihaz sağlama hizmetinizi açın.

2. Kayıtları **Yönet** sekmesini seçin ve ardından üst kısımdaki **tek kayıt Ekle** düğmesini seçin. 

3. **Kayıt Ekle** panelinde, aşağıdaki bilgileri girin ve **Kaydet** düğmesine basın.

   - **Mekanizma**: Kimlik onay *Mekanizması* olarak **Simetrik Anahtar** seçeneğini belirleyin.

   - **Anahtarları otomatik oluştur**: Bu kutuyu işaretleyin.

   - **Kayıt Kimliği**: Kaydı tanımlamak için bir kayıt kimliği girin. Yalnızca küçük alfasayısal karakterler ile kısa çizgi ('-') karakterlerini kullanın. Örneğin, **SYMM-anahtar-cihaz-007**.

   - **IoT Hub Cihaz Kimliği:** Bir cihaz tanımlayıcısı girin. Örneğin, **device-007**.

     ![Portalda simetrik anahtar kanıtı için bireysel kayıt ekleme](./media/quick-create-simulated-device-symm-key/create-individual-enrollment.png)

4. Kaydınız kaydedildikten sonra, **birincil anahtar** ve **İkincil anahtar** oluşturulur ve kayıt girişine eklenir. Simetrik anahtar cihaz kaydınız *Bireysel Kayıtlar* sekmesindeki *Kayıt Kimliği* sütununun altında **symm-key-device-007** olarak gösterilir. 

    Kaydı açın ve oluşturduğunuz **Birincil Anahtar** değerini kopyalayın.



<a id="firstbootsequence"></a>

## <a name="run-the-provisioning-code-for-the-device"></a>Cihaz için sağlama kodunu çalıştırın

Bu bölümde cihazın önyükleme sırasını Cihaz Sağlama Hizmeti örneğinize göndermek için örnek kodu güncelleştireceksiniz. Bu önyükleme sırası cihazın tanınmasına ve Cihaz Sağlama Hizmeti örneğine bağlı bir IoT hub'ına atanmasına neden olur.



1. Azure portal, cihaz sağlama hizmetiniz için **genel bakış** sekmesini seçin ve **_kimlik kapsamı_** değerini aklınızda yapın.

    ![Portal dikey penceresinden Cihaz Sağlama Hizmeti uç noktası bilgilerini ayıklama](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. Visual Studio’da, CMake çalıştırılarak oluşturulan **azure_iot_sdks.sln** çözüm dosyasını açın. Çözüm dosyası şu konumda olmalıdır:

    ```
    \azure-iot-sdk-c\cmake\azure_iot_sdks.sln
    ```

    Dosya CMake dizininizde oluşturulmadığından, CMake derleme sisteminin yeni bir sürümünü kullandığınızdan emin olun.

3. Visual Studio'nun *Çözüm Gezgini* penceresinde **Sağlama\_Örnekleri** klasörüne gidin. **prov\_dev\_client\_sample** adlı örnek projeyi genişletin. **Kaynak Dosyalar**'ı genişletin ve **prov\_dev\_client\_sample.c** dosyasını açın.

4. `id_scope` sabitini bulun ve değeri daha önce kopyalamış olduğunuz **Kimlik Kapsamı** değerinizle değiştirin. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

5. Aynı dosyada `main()` işlevinin tanımını bulun. `hsm_type` değişkeninin aşağıda gösterildiği gibi `SECURE_DEVICE_TYPE_SYMMETRIC_KEY` değerine ayarlandığından emin olun:

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

6. `prov_dev_set_symmetric_key_info()`, Açıklama eklenen **prov \_ dev \_ Client \_ Sample. c** dosyasında öğesine yapılan çağrıyı bulun.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    İşlev çağrısının açıklamasını kaldırın ve yer tutucu değerlerini (açılı ayraçlar dahil) kayıt KIMLIĞINIZ ve birincil anahtar değerlerinizle değiştirin.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("symm-key-device-007", "your primary key here");
    ```
   
    Dosyayı kaydedin.

7. **prov\_dev\_client\_sample** projesine sağ tıklayın ve **Başlangıç Projesi Olarak Ayarla**’yı seçin. 

8. Çözümü çalıştırmak için Visual Studio menüsünde Hata **ayıklama**  >  **olmadan Başlat** ' ı seçin. Projeyi yeniden derle isteminde, çalıştırmadan önce projeyi yeniden derlemek için **Evet** ' i seçin.

    Aşağıdaki çıktı, bir IoT Hub 'ına atanacak sağlama hizmeti örneğine başarıyla bağlanan bir cihazın örneğidir:

    ```cmd
    Provisioning API Version: 1.2.8

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: 
    test-docs-hub.azure-devices.net, deviceId: device-007    
    Press enter key to exit:
    ```

9. Portalda, cihazınızın atandığı IoT Hub 'ına gidin ve **IoT cihazları** sekmesini seçin. Cihazın hub 'a başarıyla sağlanması sırasında cihaz KIMLIĞI **IoT cihazları** dikey penceresinde *durumu* **etkin** olarak görüntülenir. Üstteki **Yenile** düğmesine basmanız gerekebilir. 

    ![Cihaz IOT hub'da kayıtlı](./media/quick-create-simulated-device-symm-key/hub-registration.png) 


## <a name="clean-up-resources"></a>Kaynakları temizleme

Cihaz istemci örneğini üzerinde çalışmaya ve keşfetmeye devam etmeyi planlıyorsanız, bu hızlı başlangıçta oluşturulan kaynakları temizlemeyin. Devam etmeyi planlamıyorsanız, bu hızlı başlangıç tarafından oluşturulan tüm kaynakları silmek için aşağıdaki adımları kullanın.

1. Makinenizde cihaz istemci örnek çıktı penceresini kapatın.
1. Azure portal sol taraftaki menüden **tüm kaynaklar** ' ı seçin ve ardından cihaz sağlama hizmetinizi seçin. Hizmetiniz için kayıtları **Yönet** ' i açın ve **bireysel** kayıtlar sekmesini seçin. Bu hızlı başlangıçta kaydettiğiniz cihazın *kayıt kimliği* ' nin yanındaki onay kutusunu işaretleyin ve bölmenin en üstündeki **Sil** düğmesine basın. 
1. Azure portal sol taraftaki menüden **tüm kaynaklar** ' ı seçin ve ardından IoT Hub 'ınızı seçin. , Hub 'ınız için **IoT cihazlarını** açın, bu hızlı başlangıçta kaydettiğiniz CIHAZıN *cihaz kimliği* ' nin yanındaki onay kutusunu işaretleyin ve ardından bölmenin en üstündeki **Sil** düğmesine basın.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, Windows makinenizde cihaz sağlama kodu çalıştırdınız.  Cihazın kimliği doğrulandı ve simetrik bir anahtar kullanılarak IoT Hub 'ınıza sağlandı. X. 509.440 sertifika cihazının nasıl sağlanacağını öğrenmek için X. 509.440 cihazlarının hızlı başlangıç adımlarına devam edin. 

> [!div class="nextstepaction"]
> [Azure hızlı başlangıç-Azure IoT C SDK 'sını kullanarak bir X. 509.440 cihazı sağlama](quick-create-simulated-device-x509.md)