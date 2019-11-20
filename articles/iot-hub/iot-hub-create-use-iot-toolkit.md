---
title: VS Code için Azure IoT araçlarını kullanarak Azure IoT Hub oluşturma | Microsoft Docs
description: Bir kaynak grubunda Azure IoT Hub 'ı oluşturmak için Azure IoT araçları 'nı Visual Studio Code nasıl kullanacağınızı öğrenin.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: junhan
ms.openlocfilehash: 3fe4dfae2e86d549056805e87c0fd3927697fe24
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73890560"
---
# <a name="create-an-iot-hub-using-the-azure-iot-tools-for-visual-studio-code"></a>Visual Studio Code için Azure IoT araçlarını kullanarak IoT Hub 'ı oluşturma

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Bu makalede, Azure IoT Hub 'ı oluşturmak için [Visual Studio Code Için Azure IoT araçları 'nın](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) nasıl kullanılacağı gösterilmektedir. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Bu makaleyi tamamlayabilmeniz için şunlar gerekir:

- Azure aboneliği. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

- [Visual Studio Code](https://code.visualstudio.com/)

- Visual Studio Code için [Azure IoT araçları](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) .

## <a name="create-an-iot-hub"></a>IoT hub oluşturma

1. Visual Studio Code ' de **Gezgin** görünümünü açın.

2. Gezgin 'in alt kısmındaki **Azure IoT Hub cihazlar** bölümünü genişletin. 

   ![Azure IoT Hub cihazlarını Genişlet](./media/iot-hub-create-use-iot-toolkit/azure-iot-hub-devices.png)

3. **Azure IoT Hub Devices** bölüm üstbilgisindeki **..** . öğesine tıklayın. Üç noktayı görmüyorsanız üstbilginin üzerine gelin. 

4. **IoT Hub oluştur**' a tıklayın.

5. Bir açılır pencere, Azure 'da ilk kez oturum açmanıza olanak sağlamak için sağ alt köşede görünür.

6. Azure aboneliği ' ni seçin. 

7. Kaynak grubu ' nu seçin.

8. Konum seçin.

9. Fiyatlandırma katmanını seçin.

10. IoT Hub için genel olarak benzersiz bir ad girin.

11. IoT Hub oluşturuluncaya kadar birkaç dakika bekleyin.

## <a name="next-steps"></a>Sonraki adımlar

Artık Visual Studio Code için Azure IoT araçlarını kullanarak bir IoT Hub 'ı dağıtttınız. Daha fazla bilgi edinmek için aşağıdaki makalelere göz atın:

* [Visual Studio Code Için Azure IoT araçları 'Nı kullanarak cihazınız ile bir IoT Hub arasında ileti gönderin ve alın](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

* [Azure IoT Hub cihaz yönetimi için Visual Studio Code Azure IoT araçları 'nı kullanma](iot-hub-device-management-iot-toolkit.md)

* [Bkz. Azure IoT Hub araç seti wiki sayfası](https://github.com/microsoft/vscode-azure-iot-toolkit/wiki).
