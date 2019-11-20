---
title: include dosyası
description: include dosyası
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 09/07/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 1e919f22d5dd7975f055f262ec9ba69230aebd17
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780844"
---
Bu bölümde, bu makale için bir cihaz kimliği oluşturmak üzere Azure CLı 'yi kullanırsınız. Cihaz Kimlikleri büyük/küçük harfe duyarlıdır.

1. [Azure Cloud Shell](https://shell.azure.com/)'i açın.

1. Azure Cloud Shell ' de, Azure CLı için Microsoft Azure IoT uzantısını yüklemek üzere aşağıdaki komutu çalıştırın:

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    ```

2. Adlı `myDeviceId` yeni bir cihaz kimliği oluşturun ve bu komutlarla cihaz bağlantı dizesini alın:

    ```azurecli-interactive
    az iot hub device-identity create --device-id myDeviceId --hub-name {Your IoT Hub name}
    az iot hub device-identity show-connection-string --device-id myDeviceId --hub-name {Your IoT Hub name} -o table
    ```

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

Sonuçtan cihaz bağlantı dizesini bir yere göz önünde koyun. Bu cihaz bağlantı dizesi cihaz uygulaması tarafından IoT Hub bir cihaz olarak bağlanmak üzere kullanılır.

<!-- images and links -->
