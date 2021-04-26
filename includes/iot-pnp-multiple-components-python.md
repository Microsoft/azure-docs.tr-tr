---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 94451cfefefe30bbae1748844f9303b2cfdd7be1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104612627"
---
Bu öğreticide, bileşenleriyle bir örnek IoT Tak ve Kullan cihaz uygulaması oluşturma, bunları IoT Hub 'ınıza bağlama ve Azure IoT Gezgini aracını kullanarak hub 'a gönderdiği bilgileri görüntüleme hakkında bilgi verilmektedir. Örnek uygulama Python 'da yazılmıştır ve Python için Azure IoT cihaz SDK 'sına dahildir. Bir çözüm Oluşturucusu, herhangi bir cihaz kodunu görüntülemeye gerek olmadan IoT Tak ve Kullan cihazının yeteneklerini anlamak için Azure IoT gezgin aracını kullanabilir.

Bu öğreticide şunları yaptınız:

> [!div class="checklist"]
> * Örnek kodu indirin.
> * Örnek cihaz uygulamasını çalıştırın ve IoT Hub 'ınıza bağlandığını doğrulayın.
> * Kaynak kodunu gözden geçirin.

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

Bu öğreticiyi tamamlayabilmeniz için geliştirme makinenizde Python 3,7 gerekir. [Python.org](https://www.python.org/)adresinden birden çok platform için önerilen en son sürümü indirebilirsiniz. Python sürümünüzü aşağıdaki komutla kontrol edebilirsiniz:  

```cmd/sh
python --version
```

[Python.org](https://www.python.org/)adresinden birden çok platform için önerilen en son sürümü indirebilirsiniz.

## <a name="download-the-code"></a>Kodu indirme

**Azure-IoT-Device** paketi bir PIP olarak yayımlandı.

Yerel Python ortamınızda paketini aşağıdaki gibi yüklemelisiniz:

```cmd/sh
pip install azure-iot-device
```

[Hızlı başlangıç: Windows üzerinde çalışan bir örnek ıot Tak ve kullan cihaz uygulamasını IoT Hub (Python) Ile bağlama](../articles/iot-pnp/quickstart-connect-device.md), depoyu zaten Klonladığınız için.

Python SDK IoT deposunu kopyalayın:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-python
```

## <a name="review-the-code"></a>Kodu gözden geçirin

Bu örnek, IoT Tak ve Kullan ısı denetleyicisi cihazı uygular. Bu örneğin uyguladığı model [birden çok bileşen](../articles/iot-pnp/concepts-modeling-guide.md)kullanır. [Sıcaklık cihazının dijital TWINS tanım dili (DTDL) modeli dosyası](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) , cihazın uyguladığı telemetri, Özellikler ve komutları tanımlar.

*Azure-iot-SDK-python\azure-iot-device\samples\pnp* klasörü, IoT Tak ve kullan cihazının örnek kodunu içerir. Sıcaklık denetleyicisi örneği dosyaları şunlardır:

- temp_controller_with_thermostats. Kopyala
- pnp_helper. Kopyala

Sıcaklık denetleyicisi, sıcaklık denetleyicisi DTDL modeline göre birden çok bileşene ve varsayılan bileşene sahiptir.

*Temp_controller_with_thermostats. Kopyala* dosyasını istediğiniz düzenleyicide açın. Bu dosyadaki kod:

1. `pnp_helper.py`Yardımcı yöntemlere erişim sağlamak için içeri aktarır.

1. DTDL modelinde tanımlanan iki farklı arabirimi benzersiz olarak temsil eden iki dijital ikizi model tanımlayıcısını (Dtmıs) tanımlar. Gerçek bir sıcaklık denetleyicisindeki bileşenlerin bu iki arabirimi uygulaması gerekir. Bu iki arabirim zaten merkezi bir depoda yayımlanmış. Bu Dtmıs, Kullanıcı tarafından bilinmelidir ve cihaz uygulamasının senaryosuna bağlı olarak değişiklik gösterir. Geçerli örnek için, bu iki arabirim şunları temsil eder:

    - Bir termostat
    - Azure tarafından geliştirilen cihaz bilgileri.

1. `model_id`Uygulanan cihaz IÇIN DTMı 'yi tanımlar. DTMı Kullanıcı tanımlı ve DTDL model dosyasındaki DTMı ile eşleşmelidir.

1. DTDL dosyasındaki bileşenlere verilen adları tanımlar. DTDL ve bir cihaz bilgileri bileşeninde iki adet termostats vardır. Adlı bir sabit `serial_number` , varsayılan bileşende de tanımlanır. Bir `serial_number` cihaz için değişiklik yapamıyor.

1. Komut işleyici uygulamalarını tanımlar. Bu, cihazın komut istekleri aldığında ne yaptığını tanımlar.

1. Komut yanıtı oluşturmak için işlevleri tanımlar. Bu, cihazın komut istekleri ile nasıl yanıt vereceğini tanımlar. Bir komutun IoT Hub 'ına özel bir yanıt gönderebilmesi gerekiyorsa komut yanıt işlevleri oluşturursunuz. Bir komut için bir yanıt işlevi sağlanmazsa, genel bir yanıt gönderilir. Bu örnekte, yalnızca **Getmaxminreport** komutunda özel bir yanıt vardır.

1. Bu cihazdan telemetri göndermek için bir işlev tanımlar. Hem termostats hem de varsayılan bileşen telemetri gönderir. Bu işlev, bir bileşenin Telemetriyi gönderdiğini belirlemesini sağlamak için isteğe bağlı bir bileşen adı parametresi alır.

1. Komut istekleri için bir dinleyici tanımlar.

1. İstenen özellik güncelleştirmeleri için bir dinleyici tanımlar.

1. Şu `main` şekilde bir işleve sahiptir:

    - Bir cihaz istemcisi oluşturmak ve IoT Hub 'ınıza bağlanmak için cihaz SDK 'sını kullanır. Cihaz, `model_id` IoT Hub 'ın cihazı ıot Tak ve Kullan cihazı olarak tanımlayabilmesi için gönderir.

    - `create_reported_properties`Özellikleri oluşturmak için yardımcı dosyadaki işlevini kullanır. Bileşen adını ve özelliklerini bu işleve anahtar değer çiftleri olarak geçirin.

    - Çağırarak bileşenlerinin okunabilir özelliklerini güncelleştirir `patch_twin_reported_properties` .

    - İşlevini kullanarak komut isteklerini dinlemeye başlar `execute_command_listener` . İşlevi, hizmetten gelen komut istekleri için bir dinleyici ayarlar. Dinleyiciyi ayarlarken `method_name` , `user_command_handler` ve isteğe bağlı `create_user_response_handler` olarak parametre olarak belirtin.
        - `method_name`Komut isteğini tanımlar. Bu örnekte model, **yeniden başlatma** komutları ve **Getmaxminreport**' ı tanımlar.
        - `user_command_handler`İşlevi, bir komut aldığında cihazın ne yapması gerektiğini tanımlar.
        - `create_user_response_handler`İşlevi, bir komut başarıyla yürütüldüğünde IoT Hub 'ınıza gönderilecek bir yanıt oluşturur. Bu yanıtı portalda görüntüleyebilirsiniz. Bu işlev sağlanmazsa, hizmete genel bir yanıt gönderilir.

    - `execute_property_listener`Özellik güncelleştirmelerini dinlemek için kullanır.

    - Kullanarak telemetri göndermeye başlar `send_telemetry` . Örnek kod, üç telemetri gönderme işlevini çağırmak için bir döngü kullanır. Her biri sekiz saniyede bir çağrılır

    - Tüm dinleyicileri ve görevleri devre dışı bırakır ve **q** veya **q** tuşlarına bastığınızda döngüden çıkar.

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

Örnek yapılandırma hakkında daha fazla bilgi edinmek için bkz. [örnek Readme](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/pnp/README.md).

Örneği çalıştırmak için aşağıdaki komutu kullanın:

```cmd/sh
python temp_controller_with_thermostats.py
```

Örnek cihaz, IoT Hub 'ınıza her beş saniyede bir telemetri iletisi gönderir.

Aygıtın telemetri verilerini hub 'a gönderdiğini ve artık komutları ve özellik güncelleştirmelerini almaya hazır olduğunu gösteren aşağıdaki çıktıyı görürsünüz.

![Cihaz onay iletileri](media/iot-pnp-multiple-components-python/multiple-component.png)

Sonraki adımları tamamladıktan sonra örneği çalışır durumda tutun.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Kodu doğrulamak için Azure IoT Gezginini Kullanma

Cihaz istemcisi örneği başladıktan sonra, çalıştığını doğrulamak için Azure IoT gezgin aracını kullanın.

[!INCLUDE [iot-pnp-iot-explorer.md](iot-pnp-iot-explorer.md)]
