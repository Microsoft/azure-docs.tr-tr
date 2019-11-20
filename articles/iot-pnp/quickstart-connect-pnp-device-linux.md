---
title: IoT Tak ve Kullan Preview örnek cihaz kodunu IoT Hub 'ye bağlama (Linux) | Microsoft Docs
description: IoT Hub 'ına bağlanan Linux üzerinde IoT Tak ve Kullan önizleme örnek cihaz kodu oluşturup çalıştırın. Cihaz tarafından hub 'a gönderilen bilgileri görüntülemek için Azure CLı 'yi kullanın.
author: dominicbetts
ms.author: dobett
ms.date: 09/10/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 802578c79fa086c74a56db8d47f83ae96d6b0194
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74152147"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-running-on-linux-to-iot-hub-c-linux"></a>Hızlı başlangıç: Linux üzerinde çalışan örnek IoT Tak ve Kullan önizleme cihaz uygulamasını IoT Hub (C Linux) bağlama

Bu hızlı başlangıçta Linux üzerinde örnek IoT Tak ve Kullan cihaz uygulaması oluşturma, IoT Bub 'nize bağlama ve Azure CLı kullanarak hub 'a gönderdiği bilgileri görüntüleme hakkında bilgi verilmektedir. Örnek uygulama C dilinde yazılır ve C için Azure IoT cihaz SDK 'sına dahildir. Bir çözüm geliştiricisi, herhangi bir cihaz kodunu görüntülemeye gerek olmadan bir IoT Tak ve Kullan cihazının yeteneklerini anlamak için Azure CLı 'yı kullanabilir.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Önkoşullar

Bu hızlı başlangıç, Ubuntu Linux kullandığınızı varsayar. Bu öğreticideki adımlar Ubuntu 18,04 kullanılarak test edilmiştir.

Bu hızlı başlangıcı tamamlayabilmeniz için yerel Linux makinenize aşağıdaki yazılımı yüklemeniz gerekir:

`apt-get` komutunu kullanarak **GCC**, **Git**, **CMake**ve tüm bağımlılıkları yükler:

```sh
sudo apt-get update
sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
```

`cmake` sürümünün **2.8.12** üzerinde olduğunu ve **GCC** 'nin sürümünün **4.4.7**üzerinde olduğunu doğrulayın.

```sh
cmake --version
gcc --version
```

## <a name="prepare-an-iot-hub"></a>IoT Hub 'ı hazırlama

Ayrıca, bu hızlı başlangıcı tamamlayabilmeniz için Azure aboneliğinizde bir Azure IoT Hub 'ınız olması gerekir. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun. Bir IoT Hub 'ınız yoksa, [oluşturmak için bu yönergeleri](../iot-hub/iot-hub-create-using-cli.md)izleyin.

> [!IMPORTANT]
> Genel Önizleme sırasında IoT Tak ve Kullan özellikleri yalnızca **Orta ABD**, **Kuzey Avrupa**ve **Japonya Doğu** bölgelerinde oluşturulan IoT Hub 'larında kullanılabilir.

Azure CLı 'yi yerel olarak kullanıyorsanız, `az` sürüm **2.0.73** veya üzeri olmalıdır; Azure Cloud Shell en son sürümü kullanır. Makinenizde yüklü sürümü denetlemek için `az --version` komutunu kullanın.

Azure CLı için Microsoft Azure IoT uzantısını Cloud Shell örneğinize eklemek için aşağıdaki komutu çalıştırın:
```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Bu hızlı başlangıçtaki adımlar, uzantının **0.8.5** veya üzeri bir sürümünü gerektirir. Yüklediğiniz sürümü ve gerekirse güncelleştirilecek `az extension update` komutunu denetlemek için `az extension list` komutunu kullanın.

CLı 'yi yerel olarak kullanıyorsanız, aşağıdaki komutla Azure aboneliğinizde oturum açın:

```bash
az login
```

Azure Cloud Shell kullanıyorsanız, oturumunuz zaten otomatik olarak açıldı.

IoT Hub 'ınızda cihaz kimliğini oluşturmak için aşağıdaki komutu çalıştırın. **Youriothubname** ve **yourdeviceıd** yertutucuları kendi _IoT Hub adı_ ve seçtiğiniz bir _cihaz kimliği_ ile değiştirin.

```azurecli-interactive
az iot hub device-identity create --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

Yeni kaydettiğiniz cihazın _Cihaz bağlantı dizesini_ almak için aşağıdaki komutu çalıştırın (daha sonra kullanmak üzere):

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDevice> --output table
```

## <a name="prepare-the-development-environment"></a>Geliştirme ortamını hazırlama

Bu hızlı başlangıçta, Azure IoT Hub cihaz C SDK 'sını klonlamak ve derlemek için kullanabileceğiniz bir geliştirme ortamı hazırlarsınız.

Seçtiğiniz dizinde bir komut istemi açın. [Azure IoT C SDK 'ları ve kitaplıkları](https://github.com/Azure/azure-iot-sdk-c) GitHub deposunu bu konuma kopyalamak için aşağıdaki komutu yürütün:

```bash
git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
```

Bu işlemin tamamlanması için birkaç dakika beklemeniz gerekebilir.

## <a name="build-the-code"></a>Kodu oluşturma

Dahil edilen örnek kodu oluşturmak için cihaz SDK 'sını kullanın. Oluşturduğunuz uygulama, IoT Hub 'ına bağlanan bir cihaza benzetir. Uygulama telemetri ve Özellikler gönderir ve komutları alır.

1. Cihaz SDK 'Sı kök klasöründe bir `cmake` alt dizini oluşturun ve bu klasöre gidin:

    ```bash
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. Cihaz SDK 'sını ve üretilen kod Saplaması oluşturmak için aşağıdaki komutları çalıştırın:

    ```bash
    cmake ..
    cmake --build .
    ```

## <a name="update-your-model-repository"></a>Model deponuzu güncelleştirme

Örneği çalıştırmadan önce, şirket modeli deponuza cihaz yetenek modeli ve arabirim tanımlarını ekleyin:

1. Microsoft iş veya okul hesabınızla [Azure Sertifikalı IoT Portal](https://preview.catalog.azureiotsolutions.com) portalında veya varsa Microsoft Iş ortağı Kimliğinizle oturum açın.

1. **Şirket deposu** ' nu ve ardından **yetenek modellerini**seçin.

1. **Yeni** ' yi seçip **karşıya yükleyin**.

1. Cihaz SDK kök klasöründeki `digitaltwin_client/samples` klasöründe dosya `SampleDevice.capabilitymodel.json` seçin. **Aç** ' ı seçin ve ardından **Kaydet** ' i seçerek model dosyasını deponuza yükleyin.

1. **Şirket deposu** ' nu ve ardından **arabirimler**' i seçin.

1. **Yeni** ' yi seçip **karşıya yükleyin**.

1. Cihaz SDK kök klasöründeki `digitaltwin_client/samples/digitaltwin_sample_environmental_sensor` klasöründe dosya `EnvironmentalSensor.interface.json` seçin. **Aç** ' ı seçin ve ardından **Kaydet** ' i seçerek arabirim dosyasını deponuza yükleyin.

1. **Şirket deposu** ve ardından **bağlantı dizeleri**' ni seçin. Bu hızlı başlangıçta kullanırken ilk _Şirket modeli deposu bağlantı dizesini_bir yere göz önünde bulabilirsiniz.

## <a name="run-the-device-sample"></a>Cihaz örneğini çalıştırma

IoT Hub 'ınıza telemetri gönderen bir IoT Tak ve Kullan cihazının benzetimini yapmak için SDK 'da örnek bir uygulama çalıştırın. Örnek uygulamayı çalıştırmak için:

1. `cmake` klasöründen yürütülebilir dosyayı içeren klasöre gidin:

    ```bash
    cd digitaltwin_client/samples/digitaltwin_sample_device
    ```

1. Yürütülebilir dosyayı çalıştırın:

    ```bash
    ./digitaltwin_sample_device "<YourDeviceConnectionString>"
    ```

Cihaz artık komutları ve özellik güncelleştirmelerini almaya hazır ve hub 'a telemetri verileri göndermeye başladı. Sonraki adımları tamamladıktan sonra örneği çalışır durumda tutun.

### <a name="use-the-azure-iot-cli-to-validate-the-code"></a>Kodu doğrulamak için Azure IoT CLı 'yi kullanma

Cihaz istemcisi örneği başladıktan sonra, Azure CLı ile çalıştığını doğrulayın.

Örnek cihazın gönderdiği Telemetriyi görüntülemek için aşağıdaki komutu kullanın. Çıktıda bir telemetri görmeniz için bir dakika veya iki dakika beklemeniz gerekebilir:

```azurecli-interactive
az iot dt monitor-events --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

Cihaz tarafından gönderilen özellikleri görüntülemek için aşağıdaki komutu kullanın:

```azurecli-interactive
az iot dt list-properties --hub-name <YourIoTHubName> --device-id <YourDeviceID> --interface sensor --source private --repo-login "<YourCompanyModelRepositoryConnectionString>"
```
[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta IoT Hub 'ına IoT Tak ve Kullan cihazını bağlamayı öğrendiniz. IoT Tak ve Kullan cihazlarınızla etkileşim kuran bir çözüm oluşturma hakkında daha fazla bilgi edinmek için bkz.:

> [!div class="nextstepaction"]
> [Nasıl yapılır: bir cihaza bağlanma ve cihazla etkileşim kurma](howto-develop-solution.md)
