---
title: include dosyası
description: include dosyası
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 09/07/2018
ms.author: dobett
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: b1a863498603006e37ab98b838ffd426b962d788
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92755410"
---
Bu bölümde, bu makale için bir cihaz kimliği oluşturmak üzere Azure CLı 'yi kullanırsınız. Cihaz Kimlikleri büyük/küçük harfe duyarlıdır.

1. [Azure Cloud Shell](https://shell.azure.com/)’i açın.

1. Azure Cloud Shell ' de, Azure CLı için Microsoft Azure IoT uzantısını yüklemek üzere aşağıdaki komutu çalıştırın:

    ```azurecli-interactive
    az extension add --name azure-iot
    ```

2. Adlı yeni bir cihaz kimliği oluşturun `myDeviceId` ve bu komutlarla cihaz bağlantı dizesini alın:

    ```azurecli-interactive
    az iot hub device-identity create --device-id myDeviceId --hub-name {Your IoT Hub name}
    az iot hub device-identity show-connection-string --device-id myDeviceId --hub-name {Your IoT Hub name} -o table
    ```

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

Sonuçtan cihaz bağlantı dizesini bir yere göz önünde koyun. Bu cihaz bağlantı dizesi cihaz uygulaması tarafından IoT Hub bir cihaz olarak bağlanmak üzere kullanılır.

<!-- images and links -->
