---
title: Azure MQTT istemci kitaplığı 'nı kullanarak bir MQTT sunucusuna ileti gönderme
description: MQTT Istemci kitaplığını kullanarak bir MQTT aracısına ileti gönderme hakkında bilgi edinin. Ayrıca, Mxyonga IoT DevKit 'i MQTT istemcisi olarak nasıl yapılandıracağınızı öğrenin.
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/02/2018
ms.author: liydu
ms.custom: mqtt
ms.openlocfilehash: fb8bf593568825793a1a205a2955599b16fa78cf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92151757"
---
# <a name="send-messages-to-an-mqtt-server"></a>Bir MQTT sunucusuna ileti gönderme

Nesnelerin İnterneti (IoT) sistemleri genellikle aralıklı, düşük kaliteli veya yavaş İnternet bağlantılarıyla ilgilenir. MQTT, göz önünde bulundurularak geliştirilen bir makineden makineye ('U M2M) bağlantı protokolüdür. 

Burada kullanılan MQTT istemci kitaplığı, birden çok taşıma yöntemi üzerinden MQTT kullanmaya yönelik API 'Ler sağlayan, [tutulma PAHO](https://www.eclipse.org/paho/) projesinin bir parçasıdır.

## <a name="what-you-learn"></a>Öğrenecekleriniz

Bu projede şunları öğrenirsiniz:
- MQTT Istemci kitaplığını kullanarak bir MQTT aracısına ileti gönderme.
- Mxyongaıot DevKit 'i MQTT istemcisi olarak yapılandırma.

## <a name="what-you-need"></a>Gerekenler

[Başlarken Kılavuzunu](./iot-hub-arduino-iot-devkit-az3166-get-started.md) şu şekilde sona erdirin:

* DevKit 'in Wi-Fi bağlı olmasını sağlama
* Geliştirme ortamını hazırlama

## <a name="open-the-project-folder"></a>Proje klasörünü açın

1. DevKit zaten bilgisayarınıza bağlanuysa bağlantıyı kesin.

2. VS Code'u başlatın.

3. DevKit 'i bilgisayarınıza bağlayın.

## <a name="open-the-mqttclient-sample"></a>MQTTClient örneğini açın

Sol taraf **Arduino örnekleri** bölümünü GENIŞLETIN, **mxyongaAZ3166 > MQTT örneklerine** gidin ve **mqttclient**' ı seçin. İçindeki bir proje klasörüyle yeni bir VS Code penceresi açılır.

> [!NOTE]
> Ayrıca, komut paletinden örnek de açabilirsiniz. `Ctrl+Shift+P` `Cmd+Shift+P` Komut paletini açmak için (MacOS:) kullanın, **Arduino** yazın ve **Arduino: örnekleri** bulun ve seçin.

## <a name="build-and-upload-the-arduino-sketch-to-the-devkit"></a>Arduino taslağı oluşturun ve DevKit 'e yükleyin

`Ctrl+P`Çalıştırılacak tür (MacOS: `Cmd+P` ) `task device-upload` . Karşıya yükleme tamamlandıktan sonra DevKit yeniden başlatılır ve taslağı çalıştırır.

![Ekran görüntüsü, Arduino taslağını yükleyen ve çalıştıran bir komut istemi penceresi gösterir.](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/device-upload.jpg)

> [!NOTE]
> "Hata: AZ3166: bilinmeyen paket" hata iletisi alabilirsiniz. Pano paket dizini doğru yenilenmediğinde bu hata oluşur. Bu hatayı çözmek için [IoT DevKit SSS konusunun geliştirme bölümüne](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development)bakın.

## <a name="test-the-project"></a>Projeyi test etme

VS Code ' de, seri Izleyiciyi açmak ve ayarlamak için şu yordamı izleyin:

1. `COM[X]`Doğru com bağlantı noktasını ile ayarlamak için durum çubuğundaki sözcüğe tıklayın `STMicroelectronics` : ![ ekran görüntüsü, COM8 S T Micro Electronics seçili Visual Studio Code gösterir.](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/set-com-port.jpg)

2. Durum çubuğundaki güç tak simgesine tıklayarak seri Izleyiciyi açın: ![ ekran görüntüsü, durum çubuğundaki sürüm özetini ve güç tak simgesini gösterir.](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/serial-monitor.jpg)
  
3. Durum çubuğunda, baud hızını temsil eden sayıya tıklayın ve bunu olarak ayarlayın `115200` : ![ ekran görüntüsü, Visual Studio Code baud hızını ayarlamayı gösterir.](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/set-baud-rate.jpg)

Seri Izleyici, örnek taslak tarafından gönderilen tüm iletileri görüntüler. Taslak, DevKit 'i Wi-Fi ' a bağlar. Wi-Fi bağlantısı başarılı olduktan sonra, taslak MQTT aracısına bir ileti gönderir. Bundan sonra örnek, sırasıyla QoS 0 ve QoS 1 kullanarak iki "iot.eclipse.org" iletisi gönderir.

![Ekran görüntüsü, taslak tarafından gönderilen iletileri gösteren seri Izleyiciyi gösterir.](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/serial-output.jpg)

## <a name="problems-and-feedback"></a>Sorunlar ve geri bildirim

Sorunlarla karşılaşırsanız [IoT DevKit SSS](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) sayfasına bakın veya aşağıdaki kanalları kullanarak bağlanın:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="see-also"></a>Ayrıca bkz.

* [IoT DevKit AZ3166 'i Bulutta Azure IoT Hub bağlama](iot-hub-arduino-iot-devkit-az3166-get-started.md)
* [Tweet için sallayın, sallayın](iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message.md)

## <a name="next-steps"></a>Sonraki adımlar

Mxyonunuzu IoT DevKit 'i MQTT istemcisi olarak yapılandırma ve MQTT Istemci kitaplığını kullanarak bir MQTT aracısına ileti gönderme hakkında bilgi edindiğinize göre, önerilen sonraki adım şu şekildedir: [Azure IoT uzaktan izleme çözüm hızlandırıcısına genel bakış](/azure/iot-suite/)