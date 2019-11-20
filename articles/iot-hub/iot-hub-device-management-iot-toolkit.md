---
title: VSCode için Azure IoT araçları ile Azure IoT cihaz yönetimi
description: Doğrudan Yöntemler ve Ikizi 'ın istenen özellikler yönetim seçeneklerine sahip Azure IoT Hub cihaz yönetimi için Visual Studio Code için Azure IoT araçları 'nı kullanın.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: junhan
ms.openlocfilehash: 03997bb6999af9ebaad36cc95f7834e3b9ed4569
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73953205"
---
# <a name="use-azure-iot-tools-for-visual-studio-code-for-azure-iot-hub-device-management"></a>Azure IoT Hub cihaz yönetimi için Visual Studio Code Azure IoT araçları 'nı kullanma

![Uçtan uca diyagram](media/iot-hub-get-started-e2e-diagram/2.png)

[Azure IoT araçları](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) , IoT Hub yönetimi ve IoT uygulaması geliştirmeyi kolaylaştıran yararlı bir Visual Studio Code uzantısıdır. Bu, çeşitli görevleri gerçekleştirmek için kullanabileceğiniz yönetim seçenekleriyle birlikte gelir.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| Yönetim seçeneği          | Görev                    |
|----------------------------|--------------------------------|
| Doğrudan yöntemler             | İleti göndermeyi başlatma veya durdurma ya da cihazı yeniden başlatma gibi bir cihaz oluşturun.                                        |
| Cihaz ikizi okuma           | Bir cihazın bildirilen durumunu alır. Örneğin, cihaz, LED 'in Şu anda yanıp söndüğünü bildiriyor.                                    |
| Cihaz ikizi Güncelleştir         | Bir cihazı, bir ışığı yeşil olarak ayarlama veya telemetri gönderme aralığını 30 dakikaya ayarlama gibi belirli durumlara yerleştirin.         |
| Bulut-cihaz iletilerini   | Bir cihaza bildirim gönderin. Örneğin, "Bugün sizi yağmur olasıdır. Bir şemsiye getirmeyi unutmayın. "              |

Bu seçenekleri kullanmayla ilgili farklılıklar ve yönergeler hakkında daha ayrıntılı bilgi için bkz. [cihazdan buluta iletişim Kılavuzu](iot-hub-devguide-d2c-guidance.md) ve [buluttan cihaza iletişim Kılavuzu](iot-hub-devguide-c2d-guidance.md).

Cihaz çiftleri, cihaz durumu bilgilerini (meta veriler, yapılandırmalar ve koşullar) depolayan JSON belgelerdir. IoT Hub, kendisine bağlanan her cihaz için bir cihaz ikizi devam ettirir. Cihaz TWINS hakkında daha fazla bilgi için bkz. [cihaz ikgörülerle çalışmaya başlama](iot-hub-node-node-twin-getstarted.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="what-you-learn"></a>Öğrenecekleriniz

Geliştirme makinenizde çeşitli yönetim seçenekleriyle Visual Studio Code için Azure IoT araçlarını kullanmayı öğreneceksiniz.

## <a name="what-you-do"></a>Yapabilecekleriniz

Çeşitli yönetim seçenekleriyle Visual Studio Code için Azure IoT araçları 'nı çalıştırın.

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

## <a name="direct-methods"></a>Doğrudan yöntemler

1. Cihazınıza sağ tıklayın ve **doğrudan yöntemi çağır**' ı seçin. 

2. Giriş kutusuna yöntem adını ve yükünü girin.

3. Sonuçlar, **Azure IoT Hub araç seti** görünümünde **çıktı** > gösterilir.

## <a name="read-device-twin"></a>Cihaz ikizi okuma

1. Cihazınıza sağ tıklayın ve **cihaz Ikizi Düzenle**' yi seçin. 

2. Bir **Azure-IoT-Device-ikizi. JSON** dosyası, Device ikizi içeriğiyle açılacak.

## <a name="update-device-twin"></a>Cihaz ikizi Güncelleştir

1. **Etiket** veya özelliklerde bazı düzenlemeler yapın **. istenen** alan.

2. **Azure-IoT-Device-ikizi. JSON** dosyasına sağ tıklayın.

3. Cihaz ikizi güncelleştirmek için **cihaz Ikizi Güncelleştir** ' i seçin.

## <a name="send-cloud-to-device-messages"></a>Buluttan cihaza iletileri gönderme

IoT Hub 'ından cihazınıza bir ileti göndermek için şu adımları izleyin:
 
1. Cihazınıza sağ tıklayıp **CIHAZA C2D Iletisi gönder**' i seçin. 

2. Giriş kutusuna iletiyi girin.

3. Sonuçlar, **Azure IoT Hub araç seti** görünümünde **çıktı** > gösterilir.

## <a name="next-steps"></a>Sonraki adımlar

Çeşitli yönetim seçenekleriyle Visual Studio Code için Azure IoT araçları uzantısı 'nı kullanmayı öğrendiniz.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
