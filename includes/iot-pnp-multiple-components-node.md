---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 1d54429d901ca6caeadabfa7580e7270ceabcf45
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104612579"
---
Bu öğreticide, bileşenleriyle bir örnek IoT Tak ve Kullan cihaz uygulaması oluşturma, bunları IoT Hub 'ınıza bağlama ve Azure IoT Gezgini aracını kullanarak hub 'a gönderdiği bilgileri görüntüleme hakkında bilgi verilmektedir. Örnek uygulama Node.js için yazılmıştır ve Node.js için Azure IoT Hub cihaz SDK 'sına dahildir. Bir çözüm Oluşturucusu, herhangi bir cihaz kodunu görüntülemeye gerek olmadan IoT Tak ve Kullan cihazının yeteneklerini anlamak için Azure IoT gezgin aracını kullanabilir.

Bu öğreticide şunları yaptınız:

> [!div class="checklist"]
> * Örnek kodu indirin.
> * Örnek cihaz uygulamasını çalıştırın ve IoT Hub 'ınıza bağlandığını doğrulayın.
> * Kaynak kodunu gözden geçirin.

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

Bu öğreticiyi tamamlayabilmeniz için geliştirme makinenizde Node.js gerekir. [NodeJS.org](https://nodejs.org)adresinden birden çok platform için önerilen en son sürümü indirebilirsiniz.

Aşağıdaki komutu kullanarak geliştirme makinenizde geçerli Node.js sürümünü doğrulayabilirsiniz:

```cmd/sh
node --version
```

## <a name="download-the-code"></a>Kodu indirme

[Hızlı başlangıç: Windows üzerinde çalışan bir örnek ıot Tak ve kullan cihaz uygulamasını IoT Hub (node) olarak bağladığınızda](../articles/iot-pnp/quickstart-connect-device.md), depoyu zaten Klonladığınız.

Seçtiğiniz dizinde bir komut istemi açın. Node.jsGitHub deposunun [Microsoft Azure ıOT SDK 'sını ](https://github.com/Azure/azure-iot-sdk-node) bu konuma kopyalamak için aşağıdaki komutu yürütün:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-node
```

## <a name="install-required-libraries"></a>Gerekli kitaplıkları yükler

Dahil edilen örnek kodu oluşturmak için cihaz SDK 'sını kullanın. Oluşturduğunuz uygulama, IoT Hub 'ına bağlanan birden çok bileşeni olan bir Tak ve Kullan cihazına benzetir. Uygulama telemetri ve Özellikler gönderir ve komutları alır.

1. Yerel bir Terminal penceresinde, kopyalanmış deponuzdaki klasöre gidin ve */Azure-iot-SDK-node/Device/Samples/PNP* klasörüne gidin. Ardından gerekli kitaplıkları yüklemek için aşağıdaki komutu çalıştırın:

```cmd/sh
npm install
```

Bu işlem, örnekleri klasöründe çalıştırmak için gereken ilgili NPM dosyalarını yükler.

## <a name="review-the-code"></a>Kodu gözden geçirin

*Azure-iot-SDK-node\device\samples\pnp* klasörüne gidin.

*Azure-iot-SDK-node\device\samples\pnp* klasörü, IoT Tak ve kullan sıcaklık denetleyicisi cihazının örnek kodunu içerir.

*pnpTemperatureController.js* dosyasındaki kod, bir IoT Tak ve kullan sıcaklık denetleyicisi cihazı uygular. Bu örneğin uyguladığı model [birden çok bileşen](../articles/iot-pnp/concepts-modeling-guide.md)kullanır. [Sıcaklık cihazının dijital TWINS tanım dili (DTDL) modeli dosyası](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) , cihazın uyguladığı telemetri, Özellikler ve komutları tanımlar.

*pnpTemperatureController.js* dosyasını seçtiğiniz bir kod düzenleyicisinde açın. Örnek kod, nasıl yapılacağını göstermektedir:

- `modelId`Uygulanan cihaz IÇIN DTMI olduğunu tanımlayın. Bu DTMı Kullanıcı tanımlı ve [sıcaklık denetleyicisi DTDL modelinin](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json)dtmı ile eşleşmelidir.

- Sıcaklık denetleyicisi DTDL modelinde tanımlanan bileşenleri uygulayın. Gerçek bir sıcaklık denetleyicisindeki bileşenlerin bu iki arabirimi uygulaması gerekir. Bu iki arabirim zaten merkezi bir depoda yayımlanmış. Bu örnekte, iki arabirim şunlardır:

  - Saatiniz
  - Azure tarafından geliştirilen cihaz bilgileri

- Bileşen adlarını tanımlayın. Bu örnekte iki adet termostats ve bir cihaz bilgileri bileşeni bulunur.

- Komut adını tanımlayın. Bunlar, cihazın yanıt verdiği komutlardır.

- Sabiti tanımlayın `serialNumber` . , `serialNumber` Belirli bir cihaz sabitlenmiştir.

- Komut işleyicilerini tanımlayın.

- Komut yanıtlarının gönderileceği işlevleri tanımlayın.

- Komut isteklerini günlüğe kaydetmek için yardımcı işlevleri tanımlayın.

- Özellikleri oluşturmak için bir yardımcı işlev tanımlayın.

- Özellik güncelleştirmeleri için bir dinleyici tanımlayın.

- Bu cihazdan telemetri göndermek için bir işlev tanımlayın. Hem termostats hem de varsayılan bileşen telemetri gönderir. Bu işlev, bileşen adını parametre olarak alır.

- Şu `main` şekilde bir işlev tanımlayın:

  - Bir cihaz istemcisi oluşturmak ve IoT Hub 'ınıza bağlanmak için cihaz SDK 'sını kullanır. Cihaz, `modelId` IoT Hub cihazı ıot Tak ve Kullan cihazı olarak tanımlayabilmesi için sağlar.

  - İşlevini kullanarak komut isteklerini dinlemeye başlar `onDeviceMethod` . İşlevi, hizmetten gelen komut istekleri için bir dinleyici ayarlar:

    - DTDL cihaz, `reboot` ve komutlarını tanımlar `getMaxMinReport` .
    - `commandHandler`İşlevi, cihazın bir komuta nasıl yanıt verdiğini tanımlar.

  - Ve kullanarak telemetri göndermeye başlar `setInterval` `sendTelemetry` .

  - `helperCreateReportedPropertiesPatch`Özelliklerini oluşturmak için işlevini ve özelliklerini güncelleştirmek için öğesini kullanır `updateComponentReportedProperties` .

  - `desiredPropertyPatchListener`Özellik güncelleştirmelerini dinlemek için kullanır.

  - Tüm dinleyicileri ve görevleri devre dışı bırakır ve **q** veya **q** tuşlarına bastığınızda döngüden çıkar.

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

Örnek yapılandırma hakkında daha fazla bilgi edinmek için bkz. [örnek Readme](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/pnp/readme.md).

Artık kodu gördüğünüze göre, örneği çalıştırmak için aşağıdaki komutu kullanın:

```cmd\sh
node pnpTemperatureController.js
```

Cihazın, telemetri verilerini hub 'a göndermeyi başladığını ve artık komutları ve özellik güncelleştirmelerini almaya hazır olduğunu belirten aşağıdaki çıktıyı görürsünüz.

![Cihaz onay iletileri](media/iot-pnp-multiple-components-node/multiple-component.png)

Sonraki adımları tamamladıktan sonra örneği çalışır durumda tutun.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Kodu doğrulamak için Azure IoT Gezginini Kullanma

Cihaz istemcisi örneği başladıktan sonra, çalıştığını doğrulamak için Azure IoT gezgin aracını kullanın.

[!INCLUDE [iot-pnp-iot-explorer.md](iot-pnp-iot-explorer.md)]
