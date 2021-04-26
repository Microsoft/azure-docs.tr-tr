---
title: Azure IoT Hub cihaz iletilerini yönetmek için VS Cloud Explorer 'ı kullanma
description: Visual Studio için Cloud Explorer 'ı kullanarak cihazı bulut iletilerine izleme ve Azure IoT Hub cihaz iletilerine bulut gönderme hakkında bilgi edinin.
author: shizn
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: xshi
ms.openlocfilehash: 8461a77d06a63c2ac319323a91b5577ca4dce1cf
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567039"
---
# <a name="use-cloud-explorer-for-visual-studio-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>Cihazınız ve IoT Hub arasında ileti göndermek ve almak için Visual Studio için Cloud Explorer 'ı kullanın

![Uçtan uca diyagram](./media/iot-hub-visual-studio-cloud-device-messaging/e-to-e-diagram.png)

Bu makalede, cihazdan buluta iletileri izlemek ve buluttan cihaza iletiler göndermek üzere Visual Studio için Cloud Explorer 'ı nasıl kullanacağınızı öğreneceksiniz. Cihazdan buluta iletiler, cihazınızın topladığı ve sonra IoT Hub gönderdiği algılayıcı verileri olabilir. Buluttan cihaza iletiler, IoT Hub cihazınıza göndereceğini belirten komutlardır. Örneğin, cihazınıza bağlı bir LED 'in yanıp sönmesini sağlar.

[Bulut Gezgini](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) , Azure kaynaklarınızı görüntülemenize, özelliklerini Incelemenize ve Visual Studio içinden önemli geliştirici eylemleri gerçekleştirmenize olanak tanıyan yararlı bir Visual Studio uzantısıdır. Bu makalede, bulut Gezgini 'ni kullanarak cihazınız ve hub 'ınız arasında ileti gönderme ve alma hakkında odaklanılır.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="prerequisites"></a>Önkoşullar

- Etkin bir Azure aboneliği.

- Aboneliğiniz altındaki bir Azure IoT Hub.

- Microsoft Visual Studio 2017 güncelleştirme 9 veya üzeri. Bu makalede [Visual Studio 2019](https://www.visualstudio.com/vs/)kullanılmaktadır.

- Azure Iş yükü ile varsayılan olarak seçilen Visual Studio Yükleyicisi bulut Gezgini bileşeni.

## <a name="update-cloud-explorer-to-latest-version"></a>Bulut Gezginini en son sürüme Güncelleştir

Visual Studio 2017 için Visual Studio Yükleyicisi bulut Gezgini bileşeni yalnızca cihazdan buluta ve buluttan cihaza iletileri izlemeyi destekler. Visual Studio 2017 ' i kullanmak için en son [bulut Gezginini](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS)indirip yükleyin.

## <a name="sign-in-to-access-your-hub"></a>Hub 'ınıza erişmek için oturum açın

Hub 'ınıza erişmek için şu adımları izleyin:

1. Visual Studio 'da bulut Gezgini 'ni  >  açmak için **bulut Gezginini** görüntüle ' yi seçin.

1. Aboneliklerinizi göstermek için hesap yönetimi simgesini seçin.

    ![Hesap yönetimi simgesi](media/iot-hub-visual-studio-cloud-device-messaging/account-management-icon.png)

1. Azure 'da oturum açtıysanız hesaplarınız görüntülenir. Azure 'da ilk kez oturum açmak için **Hesap Ekle**' yi seçin.

1. Kullanmak istediğiniz Azure aboneliklerini seçin ve **Uygula**' yı seçin.

1. Aboneliğinizi genişlettikten sonra **IoT Hub 'ları**' nı genişletin.  Her hub 'ın altında bu hub için cihazlarınızı görebilirsiniz.

    ![Cihaz listesi](media/iot-hub-visual-studio-cloud-device-messaging/hub-device-list.png)

## <a name="monitor-device-to-cloud-messages"></a>Cihazdan buluta iletileri izleme

Cihazınızdan IoT Hub gönderilen iletileri izlemek için aşağıdaki adımları izleyin:

1. IoT Hub veya cihazınıza sağ tıklayın ve **Izlemeyi Başlat D2C iletisini** seçin.

    ![D2C Iletisini Izlemeye başla](media/iot-hub-visual-studio-cloud-device-messaging/start-monitoring-d2c-message-vs2019.png)

1. İzlenen iletiler **çıktı** altında görünür.

    ![D2C Iletisi sonucunu izleme](media/iot-hub-visual-studio-cloud-device-messaging/monitor-d2c-message-result-vs2019.png)

1. İzlemeyi durdurmak için herhangi bir IoT Hub veya cihaza sağ tıklayın ve **Izleme D2C Iletisini durdur**' u seçin.

## <a name="send-cloud-to-device-messages"></a>Buluttan cihaza iletileri gönderme

IoT Hub cihazınıza bir ileti göndermek için şu adımları izleyin:

1. Cihazınıza sağ tıklayın ve **C2D Iletisi gönder**' i seçin.

1. Giriş kutusuna iletiyi girin.

    ![C2D Iletisi gönder](media/iot-hub-visual-studio-cloud-device-messaging/send-c2d-message-test.png)

    Sonuçlar **çıktı** altında görünür.

    ![C2D Ileti sonucunu gönder](media/iot-hub-visual-studio-cloud-device-messaging/send-c2d-message-result-vs2019.png)

## <a name="next-steps"></a>Sonraki adımlar

IoT cihazınız ile Azure IoT Hub arasında cihazdan buluta iletileri izlemeyi ve buluttan cihaza iletiler göndermenizi öğrendiniz.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]