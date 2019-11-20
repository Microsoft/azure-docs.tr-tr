---
title: Raspberry PI Web simülatörünü Azure IoT Hub 'a bağlama (node. js)
description: Azure bulutuna veri göndermek için Raspberry Pi için Raspberry PI Web simülatörü Azure IoT Hub bağlayın.
author: wesmc7777
manager: philmea
keywords: Raspberry PI simülatör, Azure IoT Raspberry Pi, Raspberry PI IoT Hub, Raspberry Pi verileri buluta gönderme, Raspberry Pi-buluta
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 04/11/2018
ms.author: wesmc
ms.openlocfilehash: efbe41be6c923f3547df86fd6faeb56bff5e0802
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73954524"
---
# <a name="connect-raspberry-pi-online-simulator-to-azure-iot-hub-nodejs"></a>Raspberry PI çevrimiçi simülatörünü Azure IoT Hub 'a bağlama (node. js)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Bu öğreticide, Raspberry PI çevrimiçi simülatörü ile çalışmanın temellerini öğrenerek başlarsınız. Daha sonra [Azure IoT Hub](about-iot-hub.md)kullanarak PI benzeticisinin buluta sorunsuz bir şekilde nasıl bağlanacağını öğreneceksiniz.

Fiziksel cihazlarınız varsa, kullanmaya başlamak için [Raspberry Pi Ile Azure IoT Hub bağlama](iot-hub-raspberry-pi-kit-node-get-started.md) makalesini ziyaret edin.

<p>
<div id="diag" style="width:100%; text-align:center">
<a href="https://azure-samples.github.io/raspberry-pi-web-simulator/#getstarted" target="_blank">
<img src="media/iot-hub-raspberry-pi-web-simulator/3-banner.png" alt="Connect Raspberry Pi web simulator to Azure IoT Hub" width="400">
</div>
<p>
<div id="button" style="width:100%; text-align:center">
<a href="https://azure-samples.github.io/raspberry-pi-web-simulator/#Getstarted" target="_blank">
<img src="media/iot-hub-raspberry-pi-web-simulator/6-button-default.png" alt="Start Raspberry Pi simulator" width="400" onmouseover="this.src='media/iot-hub-raspberry-pi-web-simulator/5-button-click.png';" onmouseout="this.src='media/iot-hub-raspberry-pi-web-simulator/6-button-default.png';">
</div>

## <a name="what-you-do"></a>Yapabilecekleriniz

* Raspberry PI çevrimiçi simülatörü hakkında temel bilgileri öğrenin.

* IoT Hub 'ı oluşturun.

* IoT Hub 'ınıza PI için bir cihaz kaydedin.

* IoT Hub 'ınıza sanal algılayıcı verileri göndermek için PI üzerinde örnek bir uygulama çalıştırın.

Sanal Raspberry PI 'yi oluşturduğunuz bir IoT Hub 'ına bağlayın. Ardından, algılayıcı verileri oluşturmak için Benzetici ile örnek bir uygulama çalıştırırsınız. Son olarak, algılayıcı verilerini IoT Hub 'ınıza gönderirsiniz.

## <a name="what-you-learn"></a>Öğrenecekleriniz

* Azure IoT Hub 'ı oluşturma ve yeni cihaz Bağlantı dizenizi alma. Azure hesabınız yoksa yalnızca birkaç dakika içinde [ücretsiz bir Azure deneme hesabı oluşturun](https://azure.microsoft.com/free/) .

* Raspberry PI Çevrimiçi simülatör ile çalışma.

* IoT Hub 'ınıza algılayıcı verileri gönderme.

## <a name="overview-of-raspberry-pi-web-simulator"></a>Raspberry PI Web simülatörü 'ne genel bakış

Raspberry PI online simülatörü başlatmak için düğmeye tıklayın.

> [!div class="button"]
> <a href="https://azure-samples.github.io/raspberry-pi-web-simulator/#GetStarted" target="_blank">Start Raspberry PI simülatör</a>

Web benzeticisinde üç alan vardır.

1. Derleme alanı-varsayılan bağlantı, bir pi 'nin bir BME280 algılayıcısı ve bir LED ile bağlanacağı bir. Bu alan önizleme sürümünde kilitlidir, bu nedenle şu anda özelleştirme yapamamaktadır.

2. Kodlama alanı-Raspberry PI ile kodlabilmeniz için bir çevrimiçi kod Düzenleyicisi. Varsayılan örnek uygulama, BME280 sensörden algılayıcı verileri toplamaya yardımcı olur ve Azure IoT Hub gönderir. Uygulama, gerçek PI cihazlarıyla tamamen uyumludur. 

3. Tümleşik konsol penceresi-kodunuzun çıktısını gösterir. Bu pencerenin en üstünde üç düğme vardır.

   * **Çalıştır** -uygulamayı kodlama alanında çalıştırın.

   * **Reset** -kodlama alanını varsayılan örnek uygulamaya sıfırlayın.

   * **Katlama/genişletme** -sağ tarafta konsol penceresini katlamayı/genişletmeyi kullanabileceğiniz bir düğme vardır.

> [!NOTE]
> Raspberry PI Web simülatörü artık önizleme sürümünde kullanılabilir. [Gitter Chatroom](https://gitter.im/Microsoft/raspberry-pi-web-simulator)'de sesinizi dinlemek istiyoruz. Kaynak kodu [GitHub](https://github.com/Azure-Samples/raspberry-pi-web-simulator)üzerinde ortaktır.

![PI çevrimiçi simülatörünü genel bakış](media/iot-hub-raspberry-pi-web-simulator/0-overview.png)

## <a name="create-an-iot-hub"></a>IoT hub oluşturma

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>IoT Hub 'a yeni bir cihaz kaydetme

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="run-a-sample-application-on-pi-web-simulator"></a>PI Web simülatörü üzerinde örnek uygulama çalıştırma

1. Kodlama alanı ' nda, varsayılan örnek uygulamada çalıştığınızdan emin olun. 15. satırdaki yer tutucuyu Azure IoT Hub cihaz bağlantı dizesiyle değiştirin.
1. 
   ![Cihaz bağlantı dizesini değiştirme](media/iot-hub-raspberry-pi-web-simulator/1-connectionstring.png)

2. Uygulamayı çalıştırmak için **Çalıştır** ' ı seçin veya `npm start` yazın.

Raspberry Pi 'den IoT Hub 'ınıza gönderilen çıkış algılayıcı verileri ![, algılayıcı verilerini ve IoT Hub 'ınıza gönderilen iletileri gösteren aşağıdaki çıktıyı görmeniz gerekir](media/iot-hub-raspberry-pi-web-simulator/2-run-application.png)

## <a name="read-the-messages-received-by-your-hub"></a>Hub 'ınız tarafından alınan iletileri okuyun

Sanal cihazdan IoT Hub 'ınız tarafından alınan iletileri izlemenin bir yolu, Visual Studio Code için Azure IoT araçları 'nı kullanmaktır. Daha fazla bilgi edinmek için bkz. [Visual Studio Code Için Azure IoT araçlarını kullanarak cihazınız ve IoT Hub arasında ileti gönderme ve alma](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

Cihazınız tarafından gönderilen verileri daha fazla şekilde işlemek için bir sonraki bölüme geçin.

## <a name="next-steps"></a>Sonraki adımlar

Algılayıcı verilerini toplamak ve IoT Hub 'ınıza göndermek için örnek bir uygulama çalıştırdık.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
