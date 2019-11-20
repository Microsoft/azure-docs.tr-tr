---
title: IoT Tak ve Kullan önizleme cihazı oluşturma (Windows) | Microsoft Docs
description: Cihaz kodu oluşturmak için bir cihaz yetenek modeli kullanın. Ardından, cihaz kodunu çalıştırın ve cihazın IoT Hub bağlanmasına bakın.
author: miagdp
ms.author: miag
ms.date: 08/02/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 4af238241293f32be296e7a4243b0d2a6fef15dd
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151997"
---
# <a name="quickstart-use-a-device-capability-model-to-create-an-iot-plug-and-play-preview-device-windows"></a>Hızlı başlangıç: IoT Tak ve Kullan önizleme cihazı (Windows) oluşturmak için cihaz yetenek modeli kullanma

Bir _cihaz yetenek modeli_ (DCM) IoT Tak ve kullan cihazının yeteneklerini açıklar. DCM, genellikle bir Ürün SKU 'SU ile ilişkilendirilir. DCM 'de tanımlanan yetenekler yeniden kullanılabilir arabirimler halinde düzenlenmiştir. Bir DCM 'den iskelet cihaz kodu oluşturabilirsiniz. Bu hızlı başlangıçta, DCM kullanarak IoT Tak ve Kullan cihazı oluşturmak için Windows 'ta VS Code nasıl kullanacağınız gösterilmektedir.

## <a name="prerequisites"></a>Önkoşullar

Bu hızlı başlangıcı tamamlayabilmeniz için yerel makinenize aşağıdaki yazılımı yüklemeniz gerekir:

* Visual Studio  **C++ için derleme araçları ve** **NuGet Paket Yöneticisi bileşen** iş yükleri [için derleme araçları](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=BuildTools&rel=16) . Ya da [Visual Studio (Community, Professional veya Enterprise)](https://visualstudio.microsoft.com/downloads/) 2019, 2017 veya 2015 aynı iş yükleri yüklüyken zaten yüklüyse.
* [Git](https://git-scm.com/download/).
* [CMake](https://cmake.org/download/).
* [Visual Studio Code](https://code.visualstudio.com/).

### <a name="install-azure-iot-tools"></a>Azure IoT araçları 'nı yükler

VS Code Uzantı paketi [Için Azure IoT araçları](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) 'nı yüklemek için aşağıdaki adımları kullanın:

1. VS Code, **Uzantılar** sekmesini seçin.
1. **Azure IoT araçlarını**arayın.
1. **Yükle**’yi seçin.

### <a name="install-the-azure-iot-explorer"></a>Azure IoT Gezginini yüklemeyi

En son güncelleştirme için "varlıklar" altında. msi dosyasını seçerek aracın [Depo](https://github.com/Azure/azure-iot-explorer/releases) sayfasından **Azure IoT Explorer** 'ın en son sürümünü indirin ve yükleyin.

### <a name="get-the-connection-string-for-your-company-model-repository"></a>Şirket modeli deponuzun bağlantı dizesini alın

Bir Microsoft iş veya okul hesabıyla oturum açtığınızda veya varsa Microsoft iş ortağı KIMLIĞINIZLE _Şirket modeli deposu Bağlantı dizenizi_ [Azure Sertifikalı for IoT Portal](https://preview.catalog.azureiotsolutions.com) portalında bulabilirsiniz. Oturum açtıktan sonra **Şirket deposu** ' nu ve ardından **bağlantı dizelerini**seçin.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub-windows.md](../../includes/iot-pnp-prepare-iot-hub-windows.md)]

## <a name="prepare-the-development-environment"></a>Geliştirme ortamını hazırlama

### <a name="get-azure-iot-device-sdk-for-c"></a>C için Azure IoT cihaz SDK 'sını alın

Bu hızlı başlangıçta, [Vcpkg](https://github.com/microsoft/vcpkg)aracılığıyla Azure IoT C CIHAZ SDK 'sını yükleyerek bir geliştirme ortamı hazırlarsınız.

1. Bir komut istemi açın. Vcpkg yüklemek için şu komutu yürütün:

    ```cmd/sh
    git clone https://github.com/Microsoft/vcpkg.git
    cd vcpkg

    .\bootstrap-vcpkg.bat
    ```

    Ardından, Kullanıcı genelinde [tümleştirmeyi](https://github.com/microsoft/vcpkg/blob/master/docs/users/integration.md)yedeklemek için aşağıdakileri çalıştırın (Note: ilk kullanımda yönetici gerektirir):

    ```cmd/sh
    .\vcpkg.exe integrate install
    ```

1. Azure IoT C cihaz SDK 'Sı Vcpkg 'yi yükler:

    ```cmd/sh
    .\vcpkg.exe install azure-iot-sdk-c[public-preview,use_prov_client]
    ```

## <a name="author-your-model"></a>Modelinizi yazma

Bu hızlı başlangıçta, var olan bir örnek cihaz yetenek modeli ve ilişkili arabirimleri kullanırsınız.

1. Yerel sürücünüzde bir `pnp_app` dizini oluşturun. Bu klasörü cihaz modeli dosyaları ve cihaz kodu saplaması için kullanırsınız.

1. [Cihaz yetenek modeli ve arabirim örnek dosyalarını](https://github.com/Azure/IoTPlugandPlay/blob/master/samples/SampleDevice.capabilitymodel.json) ve [arabirim örneğini](https://github.com/Azure/IoTPlugandPlay/blob/master/samples/EnvironmentalSensor.interface.json) indirin ve dosyaları `pnp_app` klasöre kaydedin.

    > [!TIP]
    > GitHub 'dan bir dosyayı indirmek için dosyasına gidin, **RAW**' a sağ tıklayın ve ardından **Bağlantıyı farklı kaydet**' i seçin.

1. VS Code `pnp_app` klasörü açın. IntelliSense ile dosyaları görüntüleyebilirsiniz:

    ![Cihaz yetenek modeli](media/quickstart-create-pnp-device/dcm.png)

1. İndirdiğiniz dosyalarda, `@id` ve `schema` alanlarındaki `<YOUR_COMPANY_NAME_HERE>`, benzersiz bir değer ile değiştirin. Yalnızca a-z, A-Z, 0-9 ve alt çizgi karakterlerini kullanın. Daha fazla bilgi için bkz. [Digital ikizi Identifier Format](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL#digital-twin-identifier-format).

## <a name="generate-the-c-code-stub"></a>C kodu saplaması oluşturma

Artık bir DCM ve ilişkili arabirimlerinizin olduğuna göre, modeli uygulayan Cihaz kodunu oluşturabilirsiniz. VS Code ' de C kodu saplaması oluşturmak için:

1. VS Code `pnp_app` klasörü açıkken, **Ctrl + Shift + P** tuşlarına basarak komut paletini açın, **IoT Tak ve kullan**girin ve **cihaz kodu saplama oluştur**' u seçin.

    > [!NOTE]
    > IoT Tak ve Kullan CodeGen CLı 'yi ilk kez kullandığınızda, otomatik olarak indirilmek ve yüklenmesi birkaç saniye sürer.

1. Cihaz kodu saplaması oluşturmak için kullanılacak **Sampledevice. capabilitymodel. JSON** dosyasını seçin.

1. **Sample_device**proje adını girin. Bu, cihaz uygulamanızın adı olacaktır.

1. Diliniz olarak **ANSI C** 'yi seçin.

1. Bağlantı yöntemi olarak **IoT Hub cihaz bağlantı dizesi aracılığıyla** seçim yapın.

1. Proje şablonunuz olarak **Windows 'Da CMake projesi '** ni seçin.

1. Cihaz SDK 'sını dahil etmek için **Vcpkg aracılığıyla** öğesini seçin.

1. **Sample_device** adlı yeni bir klasör, DCM dosyasıyla aynı konumda oluşturulur ve içinde oluşturulan cihaz kodu saplama dosyalarıdır. VS Code, bunları göstermek için yeni bir pencere açar.
    ![cihaz kodu](media/quickstart-create-pnp-device/device-code.png)

## <a name="build-and-run-the-code"></a>Kodu derleyin ve çalıştırın

Oluşturulan cihaz kodu saplaması oluşturmak için cihaz SDK kaynak kodunu kullanırsınız. Oluşturduğunuz uygulama, IoT Hub 'ına bağlanan bir cihaza benzetir. Uygulama telemetri ve Özellikler gönderir ve komutları alır.

1. `sample_device` klasöründe bir `cmake` alt dizini oluşturun ve bu klasöre gidin:

    ```cmd\sh
    mkdir cmake
    cd cmake
    ```

1. Oluşturulan kod Saplaması oluşturmak için aşağıdaki komutları çalıştırın (yer tutucuyu Vcpkg depoağınızın diziniyle değiştirin):

    ```cmd\sh
    cmake .. -G "Visual Studio 16 2019" -A Win32 -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"

    cmake --build .
    ```
    
    > [!NOTE]
    > Visual Studio 2017 veya 2015 kullanıyorsanız, kullanmakta olduğunuz yapı araçlarına göre CMake oluşturucuyu belirtmeniz gerekir:
    >```cmd\sh
    ># Either
    >cmake .. -G "Visual Studio 15 2017" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}\scripts\buildsystems\vcpkg.cmake"
    ># or
    >cmake .. -G "Visual Studio 14 2015" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}\scripts\buildsystems\vcpkg.cmake"
    >```

    > [!NOTE]
    > CMake derleyicinizi C++ bulamazsa, önceki komutu çalıştırdığınızda derleme hataları alırsınız. Bu durumda, [Visual Studio komut isteminde](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs)bu komutu çalıştırmayı deneyin.

1. Yapı başarıyla tamamlandıktan sonra, IoT Hub cihaz bağlantı dizesini parametre olarak geçirerek uygulamanızı çalıştırın.

    ```cmd\sh
    .\Debug\sample_device.exe "<device connection string>"
    ```

1. Cihaz uygulaması IoT Hub veri göndermeye başlar.

    ![Cihaz uygulaması çalışıyor](media/quickstart-create-pnp-device/device-app-running.png)

## <a name="validate-the-code"></a>Kodu doğrulama

### <a name="publish-device-model-files-to-model-repository"></a>Cihaz modeli dosyalarını model deposuna Yayımla

**Azure IoT Explorer**ile cihaz kodunu doğrulamak için dosyaları model deposuna yayımlamanız gerekir.

1. VS Code `pnp_app` klasörü açıkken, **Ctrl + Shift + P tuşlarına basarak** komut paletini açın, **IoT tak & Play: dosyaları model deposuna gönder**' i yazın ve seçin.

1. `SampleDevice.capabilitymodel.json` ve `EnvironmentalSensor.interface.json` dosyalarını seçin.

1. Şirket modeli deposu Bağlantı dizenizi girin.

    > [!NOTE]
    > Bağlantı dizesi yalnızca depoya ilk kez bağlandığınızda gereklidir.

1. VS Code çıkış penceresinde ve bildiriminde, dosyaların başarıyla yayımlandığını kontrol edebilirsiniz.

    > [!NOTE]
    > Cihaz modeli dosyalarını yayımlarken hata alırsanız, oturumu kapatmak için komut **ıot Tak ve kullan: oturumu Kapat model deposunu** kullanmayı deneyebilirsiniz ve adımları yeniden deneyin.

### <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Kodu doğrulamak için Azure IoT gezginini kullanın

1. Azure IoT Gezginini açın. **Uygulama yapılandırması** sayfasını görürsünüz.

1. _IoT Hub bağlantı dizenizi_ girin ve **Bağlan**' ı seçin.

1. Bağlandıktan sonra **cihazlara** genel bakış sayfasını görürsünüz.

1. Şirket deponuzu eklemek için **Ayarlar**' ı ve **+ modül tanımı kaynağı Ekle**' yi ve ardından **Şirket deposu**' nu seçin. Şirket modeli deposu Bağlantı dizenizi ekleyin ve **Kaydet ve Bağlan**' ı seçin.

1. **Cihazların** genel bakış sayfasına geri döndüğünüzde, daha önce oluşturduğunuz cihaz kimliğini bulun. Cihaz uygulaması komut isteminde çalışmaya devam ettiğinden, cihazın Azure IoT Explorer 'daki **bağlantı durumunun** _bağlı_ olarak raporlandığından emin olun (Aksi takdirde, olana kadar **Yenile** ' ye basın). Daha fazla ayrıntı görüntülemek için cihazı seçin.

1. IoT Tak ve Kullan temel özellikler, komutlar ve telemetri özelliklerini görmek için, KIMLIK **urn: < YOUR_INTERFACE_NAME >: Environmentalalgılayıcı: 1** olan arabirimi genişletin. Görüntülenecek arabirim adı, modelinizi yazarken yerleştirdiğiniz addır.

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

Bu hızlı başlangıçta, DCM kullanarak IoT Tak ve Kullan cihazı oluşturmayı öğrendiniz.

DCMs hakkında daha fazla bilgi edinmek ve kendi modellerinizi oluşturmak için öğreticiye geçin:

> [!div class="nextstepaction"]
> [Öğretici: Visual Studio Code kullanarak cihaz yetenek modeli oluşturma ve test etme](tutorial-pnp-visual-studio-code.md)
