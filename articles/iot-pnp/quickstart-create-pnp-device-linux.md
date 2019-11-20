---
title: Azure IoT Tak ve Kullan önizleme cihazı oluşturma (Linux) | Microsoft Docs
description: Cihaz kodu oluşturmak için bir cihaz yetenek modeli kullanın. Ardından, cihaz kodunu çalıştırın ve cihazın IoT Hub bağlanmasına bakın.
author: dominicbetts
ms.author: dobett
ms.date: 09/10/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: bc4a64985d19daf9d2f6bb86b6cfb4814f141e4b
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74152047"
---
# <a name="quickstart-use-a-device-capability-model-to-create-an-iot-plug-and-play-preview-device-linux"></a>Hızlı başlangıç: IoT Tak ve Kullan önizleme cihazı (Linux) oluşturmak için cihaz yetenek modeli kullanma

Bir _cihaz yetenek modeli_ (DCM) IoT Tak ve kullan cihazının yeteneklerini açıklar. DCM, genellikle bir Ürün SKU 'SU ile ilişkilendirilir. DCM 'de tanımlanan yetenekler yeniden kullanılabilir arabirimler halinde düzenlenmiştir. Bir DCM 'den iskelet cihaz kodu oluşturabilirsiniz. Bu hızlı başlangıçta, DCM kullanarak IoT Tak ve Kullan cihazı oluşturmak için Ubuntu Linux VS Code nasıl kullanacağınızı gösterilmektedir.

## <a name="prerequisites"></a>Önkoşullar

Bu hızlı başlangıç, masaüstü ortamıyla Ubuntu Linux kullandığınızı varsayar. Bu öğreticideki adımlar Ubuntu 18,04 kullanılarak test edilmiştir.

Bu hızlı başlangıcı tamamlayabilmeniz için yerel Linux makinenize aşağıdaki yazılımı yüklemeniz gerekir:

* `apt-get` komutunu kullanarak **GCC**, **Git**, **CMake**ve tüm bağımlılıkları yükler:

    ```sh
    sudo apt-get update
    sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
    ```

    `cmake` sürümünün **2.8.12** üzerinde olduğunu ve **GCC** 'nin sürümünün **4.4.7**üzerinde olduğunu doğrulayın.

    ```sh
    cmake --version
    gcc --version
    ```

* [Visual Studio Code](https://code.visualstudio.com/).

### <a name="install-azure-iot-tools"></a>Azure IoT araçları 'nı yükler

VS Code Uzantı paketi [Için Azure IoT araçları](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) 'nı yüklemek için aşağıdaki adımları kullanın:

1. VS Code, **Uzantılar** sekmesini seçin.
1. **Azure IoT araçlarını**arayın.
1. **Yükle**’yi seçin.

### <a name="get-the-connection-string-for-your-company-model-repository"></a>Şirket modeli deponuzun bağlantı dizesini alın

Bir Microsoft iş veya okul hesabıyla oturum açtığınızda veya varsa Microsoft iş ortağı KIMLIĞINIZLE _Şirket modeli deposu Bağlantı dizenizi_ [Azure Sertifikalı for IoT Portal](https://preview.catalog.azureiotsolutions.com) portalında bulabilirsiniz. Oturum açtıktan sonra **Şirket deposu** ' nu ve ardından **bağlantı dizelerini**seçin.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-an-iot-hub"></a>IoT Hub 'ı hazırlama

Ayrıca, bu hızlı başlangıcı tamamlayabilmeniz için Azure aboneliğinizde bir Azure IoT Hub 'ınız olması gerekir. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun. Kullanabileceğiniz bir IoT Hub 'ınız yoksa, oluşturmak için bu bölümün geri kalanını izleyin.

Azure CLı 'yi yerel olarak kullanıyorsanız, `az` sürümü **2.0.75** veya üzeri olmalıdır ve Azure Cloud Shell en son sürümü kullanır. Makinenizde yüklü sürümü denetlemek için `az --version` komutunu kullanın.

Azure CLı için Microsoft Azure IoT uzantısını Cloud Shell örneğinize eklemek için aşağıdaki komutu çalıştırın:

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Bu hızlı başlangıçtaki adımlar, uzantının **0.8.5** veya üzeri bir sürümünü gerektirir. Yüklediğiniz sürümü ve gerekirse güncelleştirilecek `az extension update` komutunu denetlemek için `az extension list` komutunu kullanın.

IoT Hub 'ınız yoksa, aşağıdaki komutları kullanarak bir tane oluşturun ve `<YourIoTHubName>` istediğiniz benzersiz bir adla değiştirin. Bu komutları yerel olarak çalıştırıyorsanız, önce `az login`kullanarak Azure aboneliğinizde oturum açın. Bu komutları Azure Cloud Shell 'de çalıştırıyorsanız, otomatik olarak oturum açtınız:

  ```azurecli-interactive
  az group create --name pnpquickstarts_rg --location centralus
  az iot hub create --name <YourIoTHubName> \
    --resource-group pnpquickstarts_rg --sku S1
  ```

Önceki komutlar, Orta ABD bölgesinde `pnpquickstarts_rg` ve IoT Hub 'ı adlı bir kaynak grubu oluşturur.

> [!IMPORTANT]
> Genel Önizleme sırasında IoT Tak ve Kullan özellikleri yalnızca **Orta ABD**, **Kuzey Avrupa**ve **Japonya Doğu** bölgelerinde oluşturulan IoT Hub 'larında kullanılabilir.

IoT Hub 'ınızda bir cihaz kimliği oluşturmak için aşağıdaki komutu çalıştırın. **Youriothubname** ve **yourdeviceıd** yertutucuları kendi _IoT Hub adı_ ve seçtiğiniz bir _cihaz kimliği_ ile değiştirin.

```azurecli-interactive
az iot hub device-identity create --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

Yeni kaydettiğiniz cihazın _Cihaz bağlantı dizesini_ almak için aşağıdaki komutları çalıştırın (daha sonra kullanmak üzere).

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDevice> --output table
```

## <a name="author-your-model"></a>Modelinizi yazma

Bu hızlı başlangıçta, var olan bir örnek cihaz yetenek modeli ve ilişkili arabirimleri kullanırsınız.

1. Yerel sürücünüzde bir `pnp_app` dizini oluşturun. Bu klasörü cihaz modeli dosyaları ve cihaz kodu saplaması için kullanırsınız.

    ```bash
    cd ~
    mkdir pnp_app
    ```

1. Cihaz yetenek modeli ve arabirim örnek dosyalarını `pnp_app` klasörüne indirin.

    ```bash
    cd pnp_app
    curl -O -L https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/SampleDevice.capabilitymodel.json
    curl -O -L https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/EnvironmentalSensor.interface.json
    ```

1. VS Code `pnp_app` klasörü açın. IntelliSense ile dosyaları görüntüleyebilirsiniz:

    ![Cihaz yetenek modeli](media/quickstart-create-pnp-device-linux/dcm.png)

1. İndirdiğiniz dosyalarda, `@id` ve `schema` alanlarındaki `<YOUR_COMPANY_NAME_HERE>`, benzersiz bir değer ile değiştirin. Yalnızca a-z, A-Z, 0-9 ve alt çizgi karakterlerini kullanın. Daha fazla bilgi için bkz. [Digital ikizi Identifier Format](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL#digital-twin-identifier-format).

## <a name="generate-the-c-code-stub"></a>C kodu saplaması oluşturma

Artık bir DCM ve ilişkili arabirimlerinizin olduğuna göre, modeli uygulayan Cihaz kodunu oluşturabilirsiniz. VS Code ' de C kodu saplaması oluşturmak için:

1. VS Code `pnp_app` klasörü açıkken, **Ctrl + Shift + P** tuşlarına basarak komut paletini açın, **IoT Tak ve kullan**girin ve **cihaz kodu saplama oluştur**' u seçin.

    > [!NOTE]
    > IoT Tak ve Kullan kod Oluşturucu yardımcı programını ilk kez kullandığınızda, otomatik olarak indirilip yüklenmesi birkaç saniye sürer.

1. Cihaz kodu saplaması oluşturmak için kullanılacak **Sampledevice. capabilitymodel. JSON** dosyasını seçin.

1. **Sample_device**proje adını girin. Bu, cihaz uygulamanızın adı olacaktır.

1. Diliniz olarak **ANSI C** 'yi seçin.

1. Bağlantı yöntemi olarak **IoT Hub cihaz bağlantı dizesi aracılığıyla** seçim yapın.

1. **Linux üzerinde CMake projesini** proje şablonunuz olarak seçin.

1. Cihaz SDK 'sını dahil etmek için **kaynak kodu aracılığıyla** seçim yapın.

1. **Sample_device** adlı yeni bir klasör, DCM dosyasıyla aynı konumda oluşturulur ve içinde oluşturulan cihaz kodu saplama dosyalarıdır. VS Code, bunları göstermek için yeni bir pencere açar.
    ![cihaz kodu](media/quickstart-create-pnp-device-linux/device-code.png)

## <a name="build-and-run-the-code"></a>Kodu derleyin ve çalıştırın

Oluşturulan cihaz kodu saplaması oluşturmak için cihaz SDK kaynak kodunu kullanırsınız. Oluşturduğunuz uygulama, IoT Hub 'ına bağlanan bir cihaza benzetir. Uygulama telemetri ve Özellikler gönderir ve komutları alır.

1. Cihaz SDK 'Sı kaynak kodunu indirmek için aşağıdaki komutları çalıştırın:

    ```bash
    cd ~/pnp_app/sample_device
    git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
    ```

1. **Sample_device** uygulaması Için **CMake** derleme klasörü oluşturun:

    ```bash
    cd ~/pnp_app/sample_device
    mkdir cmake
    cd cmake
    ```

1. SDK ile uygulamanızı derlemek için CMake 'i çalıştırın:

    ```bash
    cmake .. -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -Dskip_samples:BOOL=ON
    cmake --build .
    ```

1. Oluşturma işlemi başarıyla tamamlandıktan sonra, IoT Hub cihaz bağlantı dizesini parametre olarak geçirerek uygulamanızı çalıştırın.

    ```sh
    cd ~/pnp_app/sample_device/cmake
    ./sample_device "<YourDeviceConnectionString>"
    ```

1. Cihaz uygulaması IoT Hub veri göndermeye başlar.

    ![Cihaz uygulaması çalışıyor](media/quickstart-create-pnp-device-linux/device-app-running.png)

## <a name="validate-the-code"></a>Kodu doğrulama

### <a name="publish-device-model-files-to-model-repository"></a>Cihaz modeli dosyalarını model deposuna Yayımla

**Az** CLI ile cihaz kodunu doğrulamak için dosyaları model deposuna yayımlamanız gerekir.

1. VS Code 'da `pnp_app` klasörü açıkken, **CTRL + SHIFT + P** tuşlarına basarak komut paletini açın, **IoT tak & Play: dosyaları model deposuna gönder**' i yazın ve seçin.

1. `SampleDevice.capabilitymodel.json` ve `EnvironmentalSensor.interface.json` dosyalarını seçin.

1. Şirket modeli deposu Bağlantı dizenizi girin.

    > [!NOTE]
    > Bağlantı dizesi yalnızca depoya ilk kez bağlandığınızda gereklidir.

1. VS Code çıkış penceresinde ve bildiriminde, dosyaların başarıyla yayımlandığını kontrol edebilirsiniz.

    > [!NOTE]
    > Cihaz modeli dosyalarını yayımlarken hata alırsanız, oturumu kapatmak için komut **ıot Tak ve kullan: oturumu Kapat model deposunu** kullanmayı deneyebilirsiniz ve adımları yeniden deneyin.

### <a name="use-the-azure-iot-cli-to-validate-the-code"></a>Kodu doğrulamak için Azure IoT CLı 'yi kullanma

Cihaz istemcisi örneği başladıktan sonra, Azure CLı ile çalışıp çalışmadığını kontrol edebilirsiniz.

Örnek cihazın gönderdiği Telemetriyi görüntülemek için aşağıdaki komutu kullanın. Çıktıda bir telemetri görmeniz için bir dakika veya iki dakika beklemeniz gerekebilir:

```azurecli-interactive
az iot dt monitor-events --hub-name <YourIoTHubNme> --device-id <YourDevice>
```

Cihaz tarafından gönderilen tüm özellikleri görüntülemek için aşağıdaki komutu kullanın:

```azurecli-interactive
az iot dt list-properties --device-id <YourDevice> --hub-name <YourIoTHubNme> --source private --repo-login "<YourCompanyModelRepositoryConnectionString>"
```
[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, DCM kullanarak IoT Tak ve Kullan cihazı oluşturmayı öğrendiniz.

DCMs hakkında daha fazla bilgi edinmek ve kendi modellerinizi oluşturmak için öğreticiye geçin:

> [!div class="nextstepaction"]
> [Öğretici: Visual Studio Code kullanarak cihaz yetenek modeli oluşturma ve test etme](tutorial-pnp-visual-studio-code.md)
