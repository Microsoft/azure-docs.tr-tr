---
title: VSCode için Azure IoT araçları 'nı kullanarak BT Hub 'ını yöneticinize gönderme
description: Visual Studio Code için Azure IoT araçları 'nı kullanarak cihazı bulut iletilerine izleyip Azure IoT Hub cihaz iletilerine bulut gönderme hakkında bilgi edinin.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 01/18/2019
ms.author: junhan
ms.openlocfilehash: 785c6f2b36396558cc21ce6c025be59c456ff32d
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083264"
---
# <a name="use-azure-iot-tools-for-visual-studio-code-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>Cihazınız ile IoT Hub arasında ileti göndermek ve almak için Azure IoT araçları 'nı kullanın Visual Studio Code

![Uçtan uca diyagram](./media/iot-hub-vscode-iot-toolkit-cloud-device-messaging/e-to-e-diagram.png)

[Azure IoT araçları](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) , IoT Hub yönetimi ve IoT uygulaması geliştirmeyi kolaylaştıran yararlı bir Visual Studio Code uzantısıdır. Bu makalede, cihazınız ile IoT Hub 'ınız arasında ileti göndermek ve almak üzere Visual Studio Code için Azure IoT araçları 'nın nasıl kullanılacağı ele alınmaktadır.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="what-you-will-learn"></a>Öğrenirsiniz

Cihazdan buluta iletileri izlemek ve buluttan cihaza iletiler göndermek için Visual Studio Code için Azure IoT araçlarını kullanmayı öğreneceksiniz. Cihazdan buluta iletiler, cihazınızın topladığı ve daha sonra IoT Hub 'ınıza gönderdiği algılayıcı verileri olabilir. Buluttan cihaza iletiler, IoT Hub 'ınızın cihazınıza bağlı bir ışığı yakıp söndürmek üzere cihazınıza gönderdiği komutlar olabilir.

## <a name="what-you-will-do"></a>Yapabilecekleriniz

* Cihazdan buluta iletileri izlemek için Visual Studio Code için Azure IoT araçları 'nı kullanın.

* Buluttan cihaza iletiler göndermek için Visual Studio Code için Azure IoT araçları 'nı kullanın.

## <a name="what-you-need"></a>Ne gerekiyor

* Etkin bir Azure aboneliği.

* Aboneliğiniz kapsamındaki bir Azure IoT Hub 'ı.

* [Visual Studio Code](https://code.visualstudio.com/)

* [Vs Code Için Azure IoT araçları](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) veya [Bu bağlantıyı Visual Studio Code açın](vscode:extension/vsciot-vscode.azure-iot-tools).

## <a name="sign-in-to-access-your-iot-hub"></a>IOT hub'ınıza erişmek için oturum açın

1. VS Code **Gezgin** görünümünde, sol alt köşedeki **Azure IoT Hub cihazlar** bölümünü genişletin.

2. Bağlam menüsünde **IoT Hub Seç** ' e tıklayın.

3. Bir açılır pencere, Azure 'da ilk kez oturum açmanıza olanak sağlamak için sağ alt köşede görünür.

4. Oturum açtıktan sonra Azure abonelik listeniz gösterilir ve ardından Azure aboneliği ve IoT Hub ' ni seçin.

5. Cihaz listesi, birkaç saniye içinde **Azure IoT Hub cihazları** sekmesinde gösterilir.

   > [!Note]
   > Ayrıca, ayarlamayı tamamlamak için **IoT Hub Bağlantı Dizesini Ayarla**'yı seçebilirsiniz. IoT cihazınızın açılan pencerede bağlandığı IoT Hub 'ı için **iothubowner** ilke bağlantı dizesini girin.

## <a name="monitor-device-to-cloud-messages"></a>Cihazdan buluta iletileri izleme

Cihazınızdan IoT Hub 'ınıza gönderilen iletileri izlemek için şu adımları izleyin:

1. Cihazınıza sağ tıklayıp **Izlemeyi Başlat yerleşik olay uç noktasını**seçin.

2. İzlenen iletiler **Azure IoT Hub araç seti** görünümünde **çıktı** > gösterilir.

3. İzlemeyi durdurmak için, **Çıkış** görünümüne sağ tıklayın ve **Izlemeyi durdur yerleşik olay uç noktası**' nı seçin.

## <a name="send-cloud-to-device-messages"></a>Buluttan cihaza iletileri gönderme

IoT Hub 'ından cihazınıza bir ileti göndermek için şu adımları izleyin:

1. Cihazınıza sağ tıklayıp **CIHAZA C2D Iletisi gönder**' i seçin.

2. Giriş kutusuna iletiyi girin.

3. Sonuçlar, **Azure IoT Hub araç seti** görünümünde **çıktı** > gösterilir.

## <a name="next-steps"></a>Sonraki adımlar

IoT cihazınız ile Azure IoT Hub arasında cihazdan buluta iletileri izlemeyi ve buluttan cihaza iletiler göndermenizi öğrendiniz.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]