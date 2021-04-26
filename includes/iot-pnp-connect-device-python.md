---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 607c83bd97f8e371896a5a8ac0c9aa05ab34fa2c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95510656"
---
Bu hızlı başlangıçta örnek bir IoT Tak ve Kullan cihaz uygulaması oluşturma, IoT Hub 'ınıza bağlama ve Azure IoT Explorer aracını kullanarak gönderdiği Telemetriyi görüntüleme gösterilmektedir. Örnek uygulama Python için yazılmıştır ve Python için Azure IoT Hub cihaz SDK 'sına dahildir. Bir çözüm Oluşturucusu, herhangi bir cihaz kodunu görüntülemeye gerek olmadan IoT Tak ve Kullan cihazının yeteneklerini anlamak için Azure IoT gezgin aracını kullanabilir.

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

Bu hızlı başlangıcı tamamlayabilmeniz için geliştirme makinenizde Python 3,7 gerekir. [Python.org](https://www.python.org/)adresinden birden çok platform için önerilen en son sürümü indirebilirsiniz. Python sürümünüzü aşağıdaki komutla kontrol edebilirsiniz:  

```cmd/sh
python --version
```

Yerel Python ortamınızda paketini aşağıdaki gibi yüklemelisiniz:

```cmd/sh
pip install azure-iot-device
```

Python SDK IoT deposunu kopyalayın ve **asıl** kullanıma çekin:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-python
```

## <a name="run-the-sample-device"></a>Örnek cihazı çalıştırma

*Azure-iot-SDK-python\azure-iot-device\samples\pnp* klasörü, IoT Tak ve kullan cihazının örnek kodunu içerir. Bu hızlı başlangıçta *simple_thermostat. Kopyala* dosyası kullanılmaktadır. Bu örnek kod, IoT Tak ve Kullan uyumlu bir cihaz uygular ve Azure IoT Python cihaz Istemci kitaplığını kullanır.

**Simple_thermostat. Kopyala** dosyasını bir metin düzenleyicisinde açın. Nasıl yapıldığını fark edin:

1. [Termostat](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json)'yi benzersiz bir şekilde temsil eden tek bir cihaz ikizi model tanımlayıcısını (dtmı) tanımlar. Bir DTMı, Kullanıcı tarafından bilinmelidir ve cihaz uygulamasının senaryosuna bağlı olarak değişir. Geçerli örnek için model, telemetri, Özellikler ve izleme sıcaklığında ilişkili komutlara sahip bir termostat 'yi temsil eder.

1. Komut işleyici uygulamalarını tanımlamak için işlevlere sahiptir. Cihazın komut isteklerine nasıl yanıt verdiğini tanımlamak için bu işleyicileri yazarsınız.

1. Komut yanıtı tanımlamak için bir işleve sahiptir. IoT Hub 'ınıza yanıt göndermek için komut yanıt işlevleri oluşturursunuz.

1. Uygulamadan çıkmanızı sağlamak için bir giriş klavye dinleyicisi işlevi tanımlar.

1. **Ana** işleve sahiptir. **Main** işlevi:

    1. Bir cihaz istemcisi oluşturmak ve IoT Hub 'ınıza bağlanmak için cihaz SDK 'sını kullanır.

    1. Güncelleştirme özellikleri. Kullandığımız **model, termostat,** `targetTemperature` `maxTempSinceLastReboot` termostat için iki özelliği tanımlar, bu sayede, kullanacağız. Özellikler `patch_twin_reported_properties` , üzerinde tanımlanan yöntemi kullanılarak güncellenir `device_client` .

    1. **Execute_command_listener** işlevini kullanarak komut isteklerini dinlemeye başlar. İşlevi, hizmetten gelen komutları dinlemek için bir ' dinleyicisi ' kurar. ,, Ve sağlayan dinleyiciyi ayarlarken `method_name` `user_command_handler` `create_user_response_handler` .
        - `user_command_handler`İşlevi, bir komut aldığında cihazın ne yapması gerektiğini tanımlar. Örneğin, alarmınız kapalıysa, bu komutu almanın etkisi uyanmaya başlar. Bunu, çağrılan komutun ' etkisi ' olarak düşünün.
        - `create_user_response_handler`İşlevi, bir komut başarıyla yürütüldüğünde IoT Hub 'ınıza gönderilecek bir yanıt oluşturur. Örneğin, alarmınız devre dışı bırakılırsa, hizmete geri bildirimde bulunmak üzere bir erteleme vererek yanıt alırsınız. Bunu, hizmete verdiğiniz yanıt olarak düşünün. Bu yanıtı portalda görüntüleyebilirsiniz.

    1. Telemetri göndermeye başlar. **Pnp_send_telemetry** , pnp_methods. Kopyala dosyasında tanımlanmıştır. Örnek kod, her sekiz saniyede bir bu işlevi çağırmak için bir döngü kullanır.

    1. Tüm dinleyicileri ve görevleri devre dışı bırakır ve **soru** **-cevap '** i tıklattığınızda döngüyü yok.

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

Örnek yapılandırma hakkında daha fazla bilgi edinmek için bkz. [örnek Readme](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/pnp/README.md).

Artık kodu gördüğünüze göre, örneği çalıştırmak için aşağıdaki komutu kullanın:

```cmd/sh
python simple_thermostat.py
```

Aygıtın telemetri verilerini hub 'a gönderdiğini ve şu anda komutları ve özellik güncelleştirmelerini almaya hazır olduğunu gösteren aşağıdaki çıktıyı görürsünüz:

```cmd/sh
Listening for command requests and property updates
Press Q to quit
Sending telemetry for temperature
Sent message
```

Sonraki adımları tamamladıktan sonra örneği çalışır durumda tutun.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Kodu doğrulamak için Azure IoT Gezginini Kullanma

Cihaz istemcisi örneği başladıktan sonra, çalıştığını doğrulamak için Azure IoT gezgin aracını kullanın.

[!INCLUDE [iot-pnp-iot-explorer.md](iot-pnp-iot-explorer.md)]
