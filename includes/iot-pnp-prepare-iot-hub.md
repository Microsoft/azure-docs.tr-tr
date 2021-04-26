---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 03/17/2020
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: ea5d4ef26fb14e22b871bb4bfa1054cb749d38e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97673040"
---
## <a name="prepare-an-iot-hub"></a>IoT Hub 'ı hazırlama

Bu makaledeki adımları tamamlayabilmeniz için Azure aboneliğinizde bir Azure IoT Hub 'ınız olması gerekir. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

Azure CLı 'yi yerel olarak kullanıyorsanız, önce kullanarak Azure aboneliğinizde oturum açın `az login` . Bu komutları Azure Cloud Shell çalıştırıyorsanız, otomatik olarak oturum açtınız.

Azure CLı 'yi yerel olarak kullanıyorsanız, `az` Sürüm **2.8.0** veya üzeri olmalıdır; Azure Cloud Shell en son sürümü kullanır. `az --version`Makinenizde yüklü sürümü denetlemek için komutunu kullanın.

Azure CLı için Microsoft Azure IoT uzantısını örneğinize eklemek için aşağıdaki komutu çalıştırın:

```azurecli-interactive
az extension add --name azure-iot
```

Kullanabileceğiniz bir IoT Hub 'ınız yoksa, aboneliğinizdeki bir kaynak grubu ve ücretsiz bir IoT Hub 'ı oluşturmak için aşağıdaki komutları çalıştırın. `<YourIoTHubName>`İstediğiniz bir hub adıyla değiştirin:

```azurecli-interactive
az group create --name my-pnp-resourcegroup \
    --location centralus
az iot hub create --name <YourIoTHubName> \
    --resource-group my-pnp-resourcegroup --sku F1
```

IoT Hub 'ınızda cihaz kimliğini oluşturmak için aşağıdaki komutu çalıştırın. `<YourIoTHubName>`Ve `<YourDeviceID>` yer tutucuları kendi _IoT Hub adı_ ve seçtiğiniz bir _cihaz kimliği_ ile değiştirin.

```azurecli-interactive
az iot hub device-identity create --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```
