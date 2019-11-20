---
title: IoT Tak ve Kullan önizleme aygıtları oluşturmak için Visual Studio ve Visual Studio Code kullanma | Microsoft Docs
description: IoT Tak ve Kullan cihaz modellerini yazmayı ve cihaz kodunu uygulamayı hızlandırmak için Visual Studio ve Visual Studio Code kullanın.
author: liydu
ms.author: liydu
ms.date: 10/29/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 6ce5a93cdd44af7f199d59d459daa46b4adb0719
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748042"
---
# <a name="use-visual-studio-and-visual-studio-code-to-build-iot-plug-and-play-devices"></a>IoT Tak ve Kullan cihazları oluşturmak için Visual Studio ve Visual Studio Code kullanma

Visual Studio Code için Azure IoT araçları, cihaz yeteneği modellerini (DCM) ve arabirimlerini yazmak, model depolarında yayımlamak ve cihaz uygulamasını uygulamak için iskelet C kodu oluşturmak üzere tümleşik bir ortam sağlar.

Bu makalede nasıl yapılacağı gösterilmektedir:

- Cihaz kodu ve uygulama projesi oluşturun.
- Cihaz projenizde oluşturulan kodu kullanın.
- İskelet kodunu yeniden oluşturarak yineleyin.

IoT cihazları geliştirmek için VS Code kullanma hakkında daha fazla bilgi için bkz. [https://github.com/microsoft/vscode-iot-workbench](https://github.com/microsoft/vscode-iot-workbench).

## <a name="prerequisites"></a>Ön koşullar

[Visual Studio Code](https://code.visualstudio.com/)'i yükler.

VS Code ' ye uzantı paketini yüklemek için aşağıdaki adımları kullanın.

1. VS Code, **Uzantılar** sekmesini seçin.
1. Market 'ten **Azure IoT araçları** 'nı arayın ve bunları yükler.

## <a name="generate-device-code-and-project"></a>Cihaz kodu ve proje oluştur

VS Code ' de, **Ctrl + Shift + P** tuşlarına basarak komut paletini açın, **IoT Tak ve kullan**girin ve iskelet kodunu ve proje türünü yapılandırmak Için **cihaz kodu saplama oluştur** ' u seçin. Aşağıdaki listede her bir adım ayrıntılı açıklanmaktadır:

- **Kodu oluşturmak için kullanılacak dcm dosyası**. İskelet Cihaz kodunu oluşturmak için, uyguladığı DCM ve arabirim dosyalarını içeren klasörü açın. Kod Oluşturucu bir DCM 'in gerektirdiği bir arabirim bulamazsa, gerektiğinde model deposundan indirir.

- **Cihaz kodu dili**. Şu anda kod Oluşturucu yalnızca ANSI C 'yi destekliyor.

- **Proje adı**. Proje adı, oluşturulan kod ve diğer proje dosyaları için klasör adı olarak kullanılır. Klasör, varsayılan olarak DCM dosyasının yanına yerleştirilir. DCM Dosyanızı güncelleştirdiğinizde ve cihaz kodunu yeniden oluşturduğunuzda oluşturulan kod klasörünü el ile kopyalamak zorunda kalmamak için, DCM Dosyanızı proje klasörüyle aynı klasörde saklayın.

- **Azure IoT 'ye bağlanma yöntemi**. Oluşturulan dosyalar Ayrıca, cihazı Azure IoT Hub bağlanacak şekilde yapılandırmak için kod içerir. Doğrudan [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub) bağlanmayı veya [cihaz sağlama hizmeti](https://docs.microsoft.com/azure/iot-dps)'ni kullanmayı seçebilirsiniz.

    - **IoT Hub cihaz bağlantı dizesi aracılığıyla**: cihaz uygulaması için doğrudan IoT Hub bağlanacak cihaz bağlantı dizesini belirtin.
    - **DPS simetrik anahtarı aracılığıyla**: IoT Hub veya DPS kullanarak IoT Central bağlanmak için gereken cihaz uygulaması Için **kimlik kapsamını**, **simetrik anahtarı** ve **cihaz kimliğini** belirtin.

- **Proje türü**. Kod Oluşturucu Ayrıca bir CMake veya Arduino projesi oluşturur. Şu anda desteklenen proje türleri şunlardır:

    - Windows 'da CMake **projesi**: Windows üzerinde [CMake](https://cmake.org/) olarak derleme sistemi kullanan bir cihaz projesi için. Bu seçenek, C koduyla aynı klasörde bulunan cihaz SDK yapılandırmalarının `CMakeLists.txt` oluşturur.
    - **Linux üzerinde CMake projesi**: [CMake](https://cmake.org/) 'i Linux üzerinde derleme sistemi olarak kullanan bir cihaz projesi için. Bu seçenek, C koduyla aynı klasörde bulunan cihaz SDK yapılandırmalarının `CMakeLists.txt` oluşturur.
    - **Mxyongaıot devkit projesi**: [Mxyonga IoT devkit](https://aka.ms/iot-devkit) cihazında çalışan bir cihaz projesi için. Bu seçenek, IoT DevKit cihazında derlemek ve çalıştırmak için [vs Code](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) veya ARDUINO IDE 'de kullanabileceğiniz bir Arduino projesi oluşturur.

- **CIHAZ SDK türü**. CMake 'i proje türü olarak seçerseniz, `CMakeLists.txt`oluşturulan kodun Azure IoT C cihaz SDK 'sını nasıl dahil edileceğini yapılandırmak için bu adım aşağıdaki gibidir:

    - **Kaynak kodu aracılığıyla**: oluşturulan kod, içine dahil etmek ve birlikte oluşturmak IÇIN [cihaz SDK kaynak kodunu](https://github.com/Azure/azure-iot-sdk-c) kullanır. Bu, cihaz SDK 'Sı kaynak kodunu özelleştirdiğiniz durumlarda önerilir.
    - **Vcpkg aracılığıyla**: oluşturulan kod, üzerine eklemek ve birlikte oluşturmak için [cihaz SDK 'Sı vcpkg](https://github.com/microsoft/vcpkg/tree/master/ports/azure-iot-sdk-c) 'yi kullanır. Bu, Windows, Linux veya macOS çalıştıran cihazlar için önerilen yoldur.

    > [!NOTE]
    > Azure IoT C cihaz SDK 'Sı Vcpkg için macOS desteği devam etmektedir.

Kod Oluşturucu, yerel klasörde bulunan DCM ve arabirim dosyalarını kullanmayı dener. Arabirim dosyaları yerel klasörde değilse, kod Oluşturucu bunları ortak model deposunda veya şirket modeli deposunda arar. Ortak [arabirim dosyaları](./concepts-common-interfaces.md) ortak model deposunda depolanır.

Kod oluşturma işlemi tamamlandıktan sonra uzantı, kodla yeni bir VS Code penceresi açar. **Main. c**gibi oluşturulmuş bir dosyayı açarsanız, IntelliSense 'ın c SDK kaynak dosyalarını açmadığı hakkında bilgi edinebilirsiniz. Doğru IntelliSense ve kod gezintisini etkinleştirmek için, C SDK kaynağını eklemek için aşağıdaki adımları kullanın:

1. VS Code ' de, **CTRL + SHIFT + P** tuşlarına basarak komut paletini açın, yazıp **CC++/: Düzenle yapılandırma (JSON)** öğesini seçerek **c_cpp_properties. JSON** dosyasını açın.

1. `includePath` bölümünde cihaz SDK 'sının yolunu ekleyin:

    ```json
    "includePath": [
        "${workspaceFolder}/**",
        "{path_of_device_c_sdk}/**"
    ]
    ```

1. Dosyayı kaydedin.

## <a name="use-the-generated-code"></a>Oluşturulan kodu kullan

Aşağıdaki yönergelerde, farklı geliştirme makinesi platformlarında kendi cihaz projenizde oluşturulan kodun nasıl kullanılacağı açıklanır. Yönergeler, oluşturulan kodla IoT Hub bir cihaz bağlantı dizesi kullandığınızı varsayar:

### <a name="linux"></a>Linux

Ubuntu veya de, gibi bir Linux ortamında CMake 'i kullanarak, cihaz kodunu C SDK Vcpkg ile birlikte derlemek için:

1. Bir Terminal uygulaması açın.

1. `apt-get` komutunu kullanarak **GCC**, **Git**, `cmake`ve tüm bağımlılıkları yükler:

    ```bash
    sudo apt-get update
    sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
    ```

    `cmake` sürümünün **2.8.12** üzerinde olduğunu ve **GCC** 'nin sürümünün **4.4.7**üzerinde olduğunu doğrulayın.

    ```bash
    cmake --version
    gcc --version
    ```

1. Vcpkg 'yi yükler:

    ```bash
    git clone https://github.com/Microsoft/vcpkg.git
    cd vcpkg

    ./bootstrap-vcpkg.sh
    ```

    Ardından, Kullanıcı genelinde [tümleştirmeyi](https://github.com/microsoft/vcpkg/blob/master/docs/users/integration.md)yedeklemek için şunu çalıştırın:

    ```bash
    ./vcpkg integrate install
    ```

1. Azure IoT C cihaz SDK 'Sı Vcpkg 'yi yükler:

    ```bash
    ./vcpkg install azure-iot-sdk-c[public-preview,use_prov_client]
    ```

1. Klasörde bir `cmake` alt dizini oluşturun oluşturulan kod Saplaması ' nı içerir ve bu klasöre gidin:

    ```bash
    mkdir cmake
    cd cmake
    ```

1. Aşağıdaki komutları çalıştırarak, CMake 'i kullanarak cihaz SDK 'sını ve oluşturulan kod Saplaması 'nı oluşturun:

    ```bash
    cmake .. -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}/scripts/buildsystems/vcpkg.cmake"

    cmake --build .
    ```

1. Yapı başarılı olduktan sonra, IoT Hub cihaz bağlantı dizesini parametre olarak belirterek uygulamayı çalıştırın.

    ```bash
    ./{generated_code_project_name} "[IoT Hub device connection string]"
    ```

### <a name="windows"></a>Windows

Komut satırında CMake ve Visual Studio C/C++ derleyicileri kullanarak Windows 'Daki DEVICE c SDK ile birlikte cihaz kodunu derlemek için bkz. [IoT Tak ve kullan hızlı başlangıç](./quickstart-create-pnp-device.md). Aşağıdaki adımlarda, Visual Studio 'da C SDK Vcpkg as CMake projesi olarak cihaz kodunun nasıl oluşturulacağı gösterilmektedir.

1. Vcpkg aracılığıyla C için Azure IoT cihaz SDK 'sını yüklemek için [hızlı](https://docs.microsoft.com/azure/iot-pnp/quickstart-create-pnp-device#prepare-the-development-environment) başlangıçta bulunan adımları izleyin.

1. [Visual Studio 2019 'yi (Community, Professional veya Enterprise)](https://visualstudio.microsoft.com/downloads/) yükledikten sonra, iş yüküyle **NuGet Paket Yöneticisi** bileşenini ve **Masaüstü geliştirmeyi C++**  eklediğinizden emin olun.

1. Visual Studio 'yu açın, **dosya > > CMake aç** ' ı seçin. klasörde `CMakeLists.txt` açmak için oluşturulan kodu içerir.

1. **Genel** araç çubuğunda, **Konfigürasyonlar** açılan listesini bulun. Projenizin CMake ayarını eklemek için **yapılandırmayı Yönet** ' i seçin.

    ![Yapılandırmayı Yönet](media/howto-develop-with-vs-vscode/vs-manage-config.png)

1. **CMake ayarlarında**yeni bir yapılandırma ekleyin ve hedef olarak **x86-hata ayıkla** ' yı seçin.

1. **CMake komut bağımsız değişkenlerinde**aşağıdaki satırı ekleyin:

    ```txt
    -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON
    ```

1. Dosyayı kaydedin.

1. **Konfigürasyonlar** açılan menüsünde **x86-hata ayıkla** ' ya geçin. CMake 'in önbellek oluşturması için birkaç saniye gerekir. İlerleme durumunu görmek için çıkış penceresini görüntüleyin.

    ![CMake çıkışı](media/howto-develop-with-vs-vscode/vs-cmake-output.png)

1. **Çözüm Gezgini**, kök klasördeki `CMakeLists.txt` sağ tıklayın ve oluşturulan kod Saplaması ' nı cihaz SDK 'sı ile derlemek için bağlam menüsünden **Oluştur** ' u seçin.

1. Oluşturma başarılı olduktan sonra komut isteminde, IoT Hub cihaz bağlantı dizesini parametre olarak belirten uygulamayı çalıştırın.

    ```cmd
    .\out\build\x86-Debug\{generated_code_project_name}.exe "[IoT Hub device connection string]"
    ```

> [!TIP]
> Visual Studio 'da CMake 'i kullanma hakkında daha fazla bilgi için bkz. [CMake projesi oluşturma](https://docs.microsoft.com/cpp/build/cmake-projects-in-visual-studio?view=vs-2019#building-cmake-projects) .

### <a name="macos"></a>macOS

Aşağıdaki adımlarda, CMake kullanarak macOS 'ta Device C SDK kaynak kodu ile birlikte cihaz kodunun nasıl oluşturulacağı gösterilmektedir:

1. Terminal uygulamasını açın.

1. Tüm bağımlılıkları yüklemek için [homebrew](https://homebrew.sh) kullanın:

    ```bash
    brew update
    brew install git cmake pkgconfig openssl ossp-uuid
    ```

1. [CMake](https://cmake.org/) 'in en az sürüm **2.8.12**olduğunu doğrulayın:

    ```bash
    cmake --version
    ```

1. [Düzeltme Eki](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#upgrade-curl-on-mac-os) , kullanılabilir en son sürüme kıvır.

1. Oluşturulan kodu içeren klasörde, [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) deposunu kopyalayın:

    ```bash
    git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
    ```

    Bu işlemin tamamlanması için birkaç dakika beklemeniz gerekebilir.

1. Oluşturulan kodu içeren klasör altında `cmake` adlı bir klasör oluşturun ve bu klasöre gidin.

    ```bash
    mkdir cmake
    cd cmake
    ```

1. Aşağıdaki komutları çalıştırarak, CMake 'i kullanarak cihaz SDK 'sını ve oluşturulan kod Saplaması 'nı oluşturun:

    ```bash
    cmake -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DOPENSSL_ROOT_DIR:PATH=/usr/local/opt/openssl ..
    cmake --build .
    ```

1. Yapı başarılı olduktan sonra, IoT Hub cihaz bağlantı dizesini parametre olarak belirterek uygulamayı çalıştırın.

    ```bash
    cd {generated_code_folder_name}/cmake/
    ./{generated_code_project_name} "[IoT Hub device connection string]"
    ```

## <a name="iterate-by-regenerating-the-skeleton-code"></a>İskelet kodunu yeniden oluşturarak yineleme

DCM veya arabirim dosyalarınızı güncelleştirirseniz kod Oluşturucu kodu yeniden oluşturabilir. Kodu yeniden oluşturmak için bir DCM dosyasındaki cihaz kodunuzu zaten oluşturmuş olduğunuz varsayılarak:

1. DCM dosyaları açık olan klasörde, **CTRL + SHIFT + P** tuşlarına basarak komut paletini açın, **IoT Tak ve kullan**girin ve **cihaz kodu saplama oluştur**' u seçin.

1. Güncelleştirdiğiniz DCM dosyasını seçin.

1. **{Project Name} için kodu yeniden oluştur**' u seçin.

1. Kod Oluşturucu yapılandırdığınız önceki ayarı kullanır ve kodu yeniden oluşturur. Ancak, `main.c` ve `{project_name}_impl.c`gibi Kullanıcı kodu içerebilen dosyaların üzerine yazmaz.

> [!NOTE]
> Arabirim dosyanızdaki URN kimliğini güncelleştirirseniz, yeni bir arabirim olarak kabul edilir. Kodu yeniden oluşturduğunuzda, kod Oluşturucu arabirim için kod üretir ancak `{project_name}_impl.c` dosyasında özgün olanın üzerine yazmaz.

## <a name="problems-and-feedback"></a>Sorunlar ve geri bildirim

Azure IoT araçları, GitHub 'da açık kaynaklı bir projem. Herhangi bir sorun ve özellik isteği için GitHub ile ilgili [bir sorun oluşturabilirsiniz](https://github.com/microsoft/vscode-azure-iot-tools/issues/new).

## <a name="next-steps"></a>Sonraki adımlar

Bu nasıl yapılır makalesinde, Visual Studio 'Yu kullanmayı ve Visual Studio Code cihaz uygulamasını uygulamak için iskelet C kodu oluşturmayı öğrendiniz. Önerilen bir sonraki adım, [Azure IoT gezgin aracını yüklemeyi ve kullanmayı](./howto-install-iot-explorer.md) öğrenirsiniz.
