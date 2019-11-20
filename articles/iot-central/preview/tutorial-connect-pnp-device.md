---
title: IoT Tak ve Kullan önizleme cihazını Azure IoT Central bağlayın | Microsoft Docs
description: Cihaz kodu oluşturmak için bir cihaz yetenek modeli kullanın. Ardından Cihaz kodunu çalıştırın, cihaz IoT Central uygulamasına bağlanma sayfasına bakın ve otomatik olarak oluşturulan görünümleri kullanın.
author: dominicbetts
ms.author: dobett
ms.date: 08/08/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 5d5815467444afeb5f08380eea6868336044d237
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73893266"
---
# <a name="tutorial-use-a-device-capability-model-to-create-an-iot-plug-and-play-device-and-connect-it-to-your-iot-central-application"></a>Öğretici: IoT Tak ve Kullan cihazı oluşturmak ve IoT Central uygulamanıza bağlamak için bir cihaz yetenek modeli kullanın

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Bir _cihaz yetenek modeli_ (DCM) [IoT Tak ve kullan](../../iot-pnp/overview-iot-plug-and-play.md) cihazının yeteneklerini açıklar. IoT Central, cihaz ilk kez bağlandığında cihaz için bir cihaz şablonu ve görselleştirmeler oluşturmak üzere bir DCM kullanabilir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * DCM kullanarak IoT Tak ve Kullan cihazı oluşturmak için Visual Studio Code kullanın.
> * Windows 'da Cihaz kodunu çalıştırın ve IoT Central uygulamanıza bağlanma bölümüne bakın.
> * Cihazın gönderdiği sanal Telemetriyi görüntüleyin.

## <a name="prerequisites"></a>Ön koşullar

**Özel uygulama > önizleme uygulaması** şablonunu kullanarak bir IoT Central uygulaması oluşturmak için [Azure IoT Central uygulaması oluşturma (Önizleme özellikleri)](./quick-deploy-iot-central.md) hızlı başlangıcını doldurun.

Bu öğreticiyi tamamlayabilmeniz için yerel makinenize aşağıdaki yazılımı yüklemeniz gerekir:

* [Visual Studio (Community, Professional veya Enterprise)](https://visualstudio.microsoft.com/downloads/) -Visual Studio 'yu yüklerken **NuGet Paket Yöneticisi** bileşenini ve iş yüküyle **Masaüstü geliştirmeyi C++**  eklediğinizden emin olun.
* [Git](https://git-scm.com/download/).
* [CMake](https://cmake.org/download/) - **CMake**'ı yüklediğinizde, **sistem yoluna CMake Ekle**seçeneğini belirleyin.
* [Visual Studio Code](https://code.visualstudio.com/).
* [Node.js](https://nodejs.org/)
* `dps-keygen` yardımcı programı:

    ```cmd/sh
    npm i -g dps-keygen
    ```

### <a name="install-azure-iot-tools"></a>Azure IoT araçları 'nı yükler

VS Code ' de Azure IoT araçları uzantı paketini yüklemek için aşağıdaki adımları kullanın:

1. VS Code, **Uzantılar** sekmesini seçin.
1. **Azure IoT araçlarını**arayın.
1. **Yükle**’yi seçin.

## <a name="prepare-the-development-environment"></a>Geliştirme ortamını hazırlama

### <a name="get-azure-iot-device-sdk-for-c"></a>C için Azure IoT cihaz SDK 'sını alın

Azure IoT C cihaz SDK 'sını oluşturmak için kullanabileceğiniz bir geliştirme ortamı hazırlayın.

1. Bir komut istemi açın. Aşağıdaki komutu yürüterek [Azure IoT C SDK'sı](https://github.com/Azure/azure-iot-sdk-c) GitHub deposunu kopyalayın:

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
    ```

    Bu işlemin tamamlanması için birkaç dakika beklemeniz gerekebilir.

1. Deponun yerel kopyasının kökünde bir `central_app` klasörü oluşturun. Bu klasörü cihaz modeli dosyaları ve cihaz kodu saplaması için kullanırsınız.

    ```cmd/sh
    cd azure-iot-sdk-c
    mkdir central_app
    ```

## <a name="generate-device-key"></a>Cihaz anahtarı oluştur

Bir cihazı IoT Central uygulamasına bağlamak için bir cihaz anahtarına ihtiyacınız vardır. Bir cihaz anahtarı oluşturmak için:

1. [Azure IoT Central uygulaması oluşturma (Önizleme özellikleri)](./quick-deploy-iot-central.md) hızlı başlangıcı içindeki **özel uygulama > Önizleme uygulaması** şablonunu kullanarak oluşturduğunuz IoT Central uygulamasında oturum açın.

1. **Yönetim** sayfasına gidin ve **cihaz bağlantısı**' nı seçin.

1. Anahtar **kapsamını** ve **görüntüleme anahtarlarını**seçtiğinizde gördüğünüz **birincil anahtarı** bir yere göz atın. Bu değerleri daha sonra bu öğreticide kullanacaksınız.

    ![Cihaz bağlantısı](./media/tutorial-connect-pnp-device/device-connection.png)

1. Bir komut istemi açın ve bir cihaz anahtarı oluşturmak için aşağıdaki komutu çalıştırın:

    ```cmd/sh
    dps-keygen  -di:mxchip-01 -mk:{Primary Key from previous step}
    ```

    Oluşturulan _Cihaz anahtarını_bir yere getirin, bu değeri bu öğreticideki sonraki bir adımda kullanın.

## <a name="download-your-model"></a>Modelinizi indirin

Bu öğreticide, bir Mxyonga IoT DevKit cihazı için genel DCM kullanılır. Kodu çalıştırmak için gerçek bir DevKit cihazına ihtiyacınız yoktur. Bu öğreticide, Windows 'da çalıştırmak için kodu derleyebilirsiniz.

1. VS Code `azure-iot-sdk-c\central_app` klasörü açın.

1. Komut paletini açmak için **CTRL + SHIFT + P** tuşlarını kullanın, **IoT Tak ve kullan**girin ve **model deposunu aç**' ı seçin. **Ortak depoyu**seçin. VS Code, genel model deposundaki DCMs 'lerin bir listesini gösterir.

1. KIMLIĞI `urn:mxchip:mxchip_iot_devkit:1`olan **Mxyongaıot DevKit** DCM ' i seçin. Ardından **İndir**' i seçin. Artık `central_app` klasöründe DCM 'nin bir kopyasına sahipsiniz.

![Model deposu ve DCM](./media/tutorial-connect-pnp-device/public-repository.png)

> [!NOTE]
> IoT Central çalışmak için, cihaz yetenek modelinde aynı dosyada satır içi tanımlanmış tüm arabirimlerin olması gerekir.

## <a name="generate-the-c-code-stub"></a>C kodu saplaması oluşturma

Artık **Mxyonu IoT DevKit** DCM ve ilişkili arabirimlerinizde, modeli uygulayan Cihaz kodunu oluşturabilirsiniz. VS Code 'da C kodu saplaması oluşturmak için:

1. DCM dosyaları açık olan klasörde, **CTRL + SHIFT + P** tuşlarına basarak komut paletini açın, **IoT Tak ve kullan**girin ve **cihaz kodu saplama oluştur**' u seçin.

    > [!NOTE]
    > IoT Tak ve Kullan kod Oluşturucu yardımcı programını ilk kez kullandığınızda, indirmesi birkaç saniye sürer.

1. Az önce indirdiğiniz **Mxyongaıot DevKit** DCM dosyasını seçin.

1. **Devkit_device**proje adını girin.

1. Diliniz olarak **ANSI C** 'yi seçin.

1. Proje türü olarak **CMake projesini** seçin. **Mxyongaıot devkit projesi**seçmeyin, bu seçenek, gerçek bir devkit cihazınız olduğunda yöneliktir.

1. Bağlantı yöntemi olarak, **DPS ile (cihaz sağlama hizmeti) simetrik anahtar** arasında seçim yapın.

1. VS Code, `devkit_device` klasöründe oluşturulan cihaz kodu saplama dosyaları ile yeni bir pencere açar.

![Oluşturulan cihaz kodu](./media/tutorial-connect-pnp-device/generated-code.png)

## <a name="build-the-code"></a>Kodu oluşturma

Oluşturulan cihaz kodu saplaması oluşturmak için cihaz SDK 'sını kullanın. Oluşturduğunuz uygulama bir **Mxyonga IoT DevKit** cihazına benzetir ve IoT Central uygulamanıza bağlanır. Uygulama telemetri ve Özellikler gönderir ve komutları alır.

1. VS Code, `azure-iot-sdk-c` klasöründeki `CMakeLists.txt` dosyasını açın. `CMakeLists.txt` dosyasını `devkit_device` klasöründe değil `azure-iot-sdk-c` klasöründe açtığınızdan emin olun.

1. Derleme sırasında cihaz kodu saplama klasörünü dahil etmek için, aşağıdaki satırı `CMakeLists.txt` dosyasının altına ekleyin:

    ```txt
    add_subdirectory(central_app/devkit_device)
    ```

1. `azure-iot-sdk-c` klasöründe bir `cmake` klasörü oluşturun ve komut isteminde bu klasöre gidin:

    ```cmd\sh
    mkdir cmake
    cd cmake
    ```

1. Cihaz SDK 'sını ve üretilen kod Saplaması oluşturmak için aşağıdaki komutları çalıştırın:

    ```cmd\sh
    cmake .. -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON
    cmake --build . -- /m /p:Configuration=Release
    ```

1. Derleme başarıyla tamamlandıktan sonra, aynı komut isteminde uygulamanızı çalıştırın. `scopeid`, `primarykey` daha önce not ettiğiniz değerlerle değiştirin:

    ```cmd\sh
    .\central_app\devkit_device\Release\devkit_device.exe scopeid primarykey mxchip-001
    ```

1. Cihaz uygulaması, IoT Central uygulamanıza veri göndermeye başlar.

## <a name="view-the-device"></a>Cihazı görüntüleme

Cihaz kodunuz IoT Central bağlandıktan sonra, gönderdiği özellikleri ve Telemetriyi görüntüleyebilirsiniz:

1. IoT Central uygulamanızda, **cihazlar** sayfasına gidin ve **mxyonga-01** cihazını seçin. Cihaz kodu bağlandığında bu cihaz otomatik olarak eklendi:

    ![Genel Bakış sayfası](./media/tutorial-connect-pnp-device/overview-page.png)

    Birkaç dakika sonra, bu sayfada cihazın gönderdiği telemetritin grafikleri gösterilir.

1. Aygıtın gönderildiği özellik değerlerini görmek için **hakkında** sayfasını seçin.

1. Cihazdaki komutları çağırmak için **Komutlar** sayfasını seçin. Cihaz kodunu çalıştıran komut isteminde cihazın yanıt verdiğini görebilirsiniz.

1. Ortak depodaki DCM 'ten IoT Central oluşturulan şablonu görmek için **cihaz şablonları** sayfasına gidin:

    ![Cihaz şablonları sayfası](./media/tutorial-connect-pnp-device/device-template.png)

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, genel model deposundaki bir DCM 'den oluşturulan IoT Tak ve Kullan cihazını bağlamayı öğrendiniz.

DCMs hakkında daha fazla bilgi edinmek ve kendi modellerinizi oluşturmak için nasıl yapılır kılavuzuna ilerleyin:

> [!div class="nextstepaction"]
> [Bir cihaz grubu oluşturma](./tutorial-use-device-groups.md)
