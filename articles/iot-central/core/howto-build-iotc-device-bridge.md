---
title: Azure IoT Central cihaz köprüsünü dağıtma | Microsoft Docs
description: Diğer IoT bulutlarını IoT Central uygulamanıza bağlamak için IoT Central cihaz köprüsünü dağıtın. Diğer IoT bulutları, Sigfox, parçacık cihaz bulutu ve nesnelerin ağını içerir.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 04/19/2021
ms.topic: how-to
ms.openlocfilehash: 6b535ecb80fae9f55eb6ab11751c26e0c6d0e9e5
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107713749"
---
# <a name="use-the-iot-central-device-bridge-to-connect-other-iot-clouds-to-iot-central"></a>Diğer IoT bulutlarını IoT Central için bağlamak üzere IoT Central cihaz köprüsünü kullanın

*Bu makale Yöneticiler için geçerlidir.*

## <a name="azure-iot-central-device-bridge"></a>Azure IoT Central cihaz Köprüsü

IoT Central cihaz Köprüsü, diğer IoT bulutlarını IoT Central uygulamanıza bağlayan açık kaynaklı bir çözümdür. Diğer IoT bulutlarına örnek olarak [Sigfox](https://www.sigfox.com/), [parçacık cihaz bulutu](https://www.particle.io/)ve [nesnelerin ağı](https://www.thethingsnetwork.org/)verilebilir. Cihaz Köprüsü, diğer IoT bulutlarına bağlı cihazlardan IoT Central uygulamanıza kadar veri ileterek işe yarar. Cihaz Köprüsü verileri yalnızca IoT Central iletir, IoT Central olan komutları veya özellik güncelleştirmelerini cihazlara geri göndermez.

Cihaz Köprüsü, IoT Central gücünü, sigfox 'ın düşük güç genişliğinde alan ağına bağlı varlık izleme aygıtları, parçacık cihaz bulutu üzerindeki hava kalitesinde izleme cihazları veya nesnelerin ağda SOIL nemi izleme cihazları gibi cihazlarla birleştirmenizi sağlar. Veriler üzerinde kurallar ve analiz gibi IoT Central uygulama özelliklerini kullanabilir, Power otomatikleştirin ve Azure Logic Apps 'te iş akışları oluşturabilir veya verileri dışarı aktarabilirsiniz.

Cihaz Köprüsü çözümü, Azure aboneliğinizde, cihaz iletilerini dönüştürmek ve IoT Central iletmek için birlikte çalışan çeşitli Azure kaynakları sağlar.

## <a name="prerequisites"></a>Önkoşullar

Bu nasıl yapılır kılavuzundaki adımları tamamlayabilmeniz için etkin bir Azure aboneliğine ihtiyacınız vardır.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

Özel uygulama **> özel uygulama** şablonunu kullanarak bir IoT Central uygulaması oluşturmak için [Azure IoT Central uygulaması oluşturma](./quick-deploy-iot-central.md) hızlı başlangıcı ' nı doldurun.

## <a name="overview"></a>Genel Bakış

IoT Central cihaz Köprüsü, GitHub 'daki açık kaynaklı bir çözümdür. Bir Azure işlev uygulaması da dahil olmak üzere Azure aboneliğinize birkaç kaynak dağıtan özel bir Azure Resource Manager şablonu kullanır.

İşlev uygulaması, cihaz köprüsünün temel parçasıdır. Basit bir Web Kancası aracılığıyla diğer IoT platformlarından HTTP POST istekleri alır. [Azure IoT Central cihaz Köprüsü](https://github.com/Azure/iotc-device-bridge) deposu, Sigfox, parçacık ve nesnelerin ağ bulutlarının nasıl bağlanacağını gösteren örnekler içerir. Bu çözümü, platformunuz işlev uygulamanıza HTTP POST istekleri gönderebiliyorsanız özel IoT bulutunuza bağlanmak için genişletebilirsiniz.

İşlev uygulaması, verileri IoT Central tarafından kabul edilen bir biçime dönüştürür ve cihaz sağlama hizmeti ile cihaz istemcisi API 'Lerini kullanarak iletir:

:::image type="content" source="media/howto-build-iotc-device-bridge/azure-function.png" alt-text="Azure işlevleri ekran görüntüsü.":::

IoT Central uygulamanız iletilen iletideki cihaz KIMLIĞINI tanırsa, cihazdaki telemetri IoT Central görüntülenir. Cihaz KIMLIĞI IoT Central uygulamanız tarafından tanınmazsa, işlev uygulaması cihaz KIMLIĞIYLE yeni bir cihaz kaydetmeye çalışır. Yeni cihaz, IoT Central uygulamanızdaki **cihazlar** sayfasında **ilişkilendirilmemiş bir cihaz** olarak görüntülenir. **Cihazlar** sayfasında, yeni cihazı bir cihaz şablonuyla ilişkilendirebilir ve ardından Telemetriyi görüntüleyebilirsiniz.

## <a name="deploy-the-device-bridge"></a>Cihaz köprüsünü dağıtma

Cihaz köprüsünü aboneliğinize dağıtmak için:

1. IoT Central uygulamanızda, **yönetim > cihaz bağlantısı** sayfasına gidin.

    1. **Kimlik kapsamını** bir yere getirin. Bu değeri, cihaz köprüsünü dağıtırken kullanırsınız.

    1. Aynı sayfada **SAS-IoT-Devices** kayıt grubunu açın. **SAS-IoT-Devices** grubu sayfasında, **birincil anahtarı** kopyalayın. Bu değeri, cihaz köprüsünü dağıtırken kullanırsınız.

1. Abonelik için işlev uygulamasını dağıtan özel Kaynak Yöneticisi şablonunu açmak için aşağıdaki **Azure 'A dağıt** düğmesini kullanın. Önceki adımda bulunan **kimlik kapsamını** ve **birincil anahtarı** kullanın:

    [![Azure’a dağıtın](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fiotc-device-bridge%2Fmaster%2Fazuredeploy.json)

Dağıtım tamamlandıktan sonra, işlevin gerektirdiği NPM paketlerini yüklemeniz gerekir:

1. Azure portal, aboneliğinize dağıtılan işlev uygulamasını açın. Ardından, **geliştirme araçları > konsoluna** gidin. Konsolunda, paketleri yüklemek için aşağıdaki komutları çalıştırın:

    ```bash
    cd IoTCIntegration
    npm install
    ```

    Bu komutların çalışması birkaç dakika sürebilir. Herhangi bir uyarı iletisini güvenle yoksayabilirsiniz.

1. Paket yüklemesi tamamlandıktan sonra, işlev uygulamasının **genel bakış** sayfasında **Yeniden Başlat** ' ı seçin:

    :::image type="content" source="media/howto-build-iotc-device-bridge/restart.png" alt-text="Yeniden başlatmanın ekran görüntüsü.":::

1. İşlev artık kullanıma hazırdır. Dış sistemler cihaz Köprüsü aracılığıyla IoT Central uygulamanıza cihaz verileri göndermek için HTTP POST isteği kullanabilir. İşlev URL 'sini almak için, işlev **URL 'Sini al > ıtcıntegration > Code + Test > işlevler**' e gidin:

    :::image type="content" source="media/howto-build-iotc-device-bridge/get-function-url.png" alt-text="Işlev URL 'sini al 'ın ekran görüntüsü.":::

Cihaz köprüsüne gönderilen ileti gövdeleri aşağıdaki biçimde olmalıdır:

```json
"device": {
  "deviceId": "my-cloud-device"
},
"measurements": {
  "temp": 20.31,
  "pressure": 50,
  "humidity": 8.5,
  "ledColor": "blue"
}
```

Nesnesindeki her anahtar, `measurements` IoT Central uygulamasındaki cihaz şablonundaki bir telemetri türü adıyla eşleşmelidir. Bu çözüm, ileti gövdesinde arabirim KIMLIĞINI belirtmeyi desteklemiyor. Bu nedenle, iki farklı arabirimin aynı ada sahip bir telemetri türü varsa, ölçüm IoT Central uygulamanızdaki her iki telemetri akışında da görünür.

`timestamp`ILETININ UTC Tarih ve saatini belirtmek için gövdeye bir alan ekleyebilirsiniz. Bu alan ISO 8601 biçiminde olmalıdır. Örneğin, `2020-06-08T20:16:54.602Z`. Zaman damgası eklemezseniz, geçerli tarih ve saat kullanılır.

Gövdeye bir alan ekleyebilirsiniz `modelId` . Sağlama sırasında cihazı bir cihaz şablonuyla ilişkilendirmek için bu alanı kullanın. Bu işlevsellik yalnızca [v3 uygulamaları](howto-get-app-info.md)tarafından desteklenir.

`deviceId`Alfasayısal, küçük harf olmalı ve kısa çizgi içerebilir.

`modelId`Alanı eklemezseniz veya IoT Central model kimliğini tanımlamazsanız, tanınmayan bir ileti `deviceId` IoT Central içinde yeni bir _ilişkilendirilmemiş cihaz_ oluşturur. Bir operatör, cihazı doğru cihaz şablonuna el ile geçirebilir. Daha fazla bilgi edinmek için bkz. [Azure IoT Central uygulamanızdaki cihazları yönetme > cihazları bir şablona geçirme](howto-manage-devices.md).

[V2 uygulamalarında](howto-get-app-info.md), yeni cihaz **Device Explorer > ilişkilendirilmemiş cihazlar** sayfasında görüntülenir. Cihazdan gelen telemetri almaya başlamak için **ilişkilendir** ' i ve bir cihaz şablonu seçin ' i seçin.

> [!NOTE]
> Cihaz bir şablonla ilişkilendirilene kadar, işleve yapılan tüm HTTP çağrıları 403 hata durumunu döndürür.

İşlev uygulaması için Application Insights günlüğe kaydetmeye geçmek için, Azure portal işlevindeki işlev uygulamanızda **izleme > günlüklerine** gidin. **Application Insights aç '** ı seçin.

## <a name="provisioned-resources"></a>Sağlanan kaynaklar

Kaynak Yöneticisi şablonu, Azure aboneliğinizde aşağıdaki kaynakları sağlar:

* İşlev Uygulaması
* App Service planı
* Depolama hesabı
* Key Vault

Anahtar Kasası IoT Central uygulamanız için SAS grubu anahtarını depolar.

İşlev Uygulaması, [Tüketim planı](https://azure.microsoft.com/pricing/details/functions/)üzerinde çalışır. Bu seçenek, adanmış işlem kaynakları sunmazsa, cihaz köprüsünün dakikada yüzlerce cihaz iletisi veya daha az sıklıkla ileti gönderen cihaz veya cihaz için uygun şekilde işlemesini sağlar. Uygulamanız çok sayıda cihaz iletisini akışa bağımlıysa, tüketim planını adanmış bir [App Service planıyla](https://azure.microsoft.com/pricing/details/app-service/windows/)değiştirin. Bu plan, daha hızlı sunucu yanıt süreleri veren adanmış işlem kaynakları sunar. Standart bir App Service planı kullanarak, bu depodaki Azure işlevinin gözlemlenen en yüksek performansı, dakikada 1.500 cihaz iletisi etrafında. Daha fazla bilgi için bkz. [Azure işlevi barındırma seçenekleri](../../azure-functions/functions-scale.md).

Tüketim planı yerine adanmış bir App Service planı kullanmak için, özel şablonu dağıtılmadan önce düzenleyin. **Şablonu Düzenle**' yi seçin.

:::image type="content" source="media/howto-build-iotc-device-bridge/edit-template.png" alt-text="Şablon düzenleme ekran görüntüsü.":::

Aşağıdaki segmenti değiştirin:

```json
{
  "type": "Microsoft.Web/serverfarms",
  "apiVersion": "2015-04-01",
  "name": "[variables('planName')]",
  "location": "[resourceGroup().location]",
  "properties": {
    "name": "[variables('planName')]",
    "computeMode": "Dynamic",
    "sku": "Dynamic"
  }
},
```

örneklerini şununla değiştirin:

```json
{
  "type": "Microsoft.Web/serverfarms",
  "sku": {
      "name": "S1",
      "tier": "Standard",
      "size": "S1",
      "family": "S",
      "capacity": 1
  },
  "kind": "app",
  "name": "[variables('planName')]",
  "apiVersion": "2016-09-01",
  "location": "[resourceGroup().location]",
  "tags": {
      "iotCentral": "device-bridge",
      "iotCentralDeviceBridge": "app-service-plan"
  },
  "properties": {
      "name": "[variables('planName')]"
  }
},
```

Ardından, `"alwaysOn": true` alwaysOn Yapılandırması altındaki kaynağın yapılandırmasına dahil edilecek şablonu düzenleyin, `functionapp` `"properties": {"SiteConfig": {...}}` işlev uygulamasının her [](https://github.com/Azure/Azure-Functions/wiki/Enable-Always-On-when-running-on-dedicated-App-Service-Plan) zaman çalışır durumda olmasını sağlar.

## <a name="examples"></a>Örnekler

Aşağıdaki örneklerde, çeşitli IoT bulutları için cihaz köprüsünün nasıl yapılandırılacağı ana hatlarıyla verilmiştir:

### <a name="example-1-connecting-particle-devices-through-the-device-bridge"></a>Örnek 1: parçacık cihazlarını cihaz Köprüsü aracılığıyla bağlama

IoT Central cihaz Köprüsü aracılığıyla bir parçacık cihazını bağlamak için parçacık konsoluna gidin ve yeni bir Web kancası tümleştirmesi oluşturun. **Istek biçimini** **JSON** olarak ayarlayın.  **Gelişmiş ayarlar** altında aşağıdaki özel gövde biçimini kullanın:

```json
{
  "device": {
    "deviceId": "{{{PARTICLE_DEVICE_ID}}}"
  },
  "measurements": {
    "{{{PARTICLE_EVENT_NAME}}}": {{{PARTICLE_EVENT_VALUE}}}
  }
}
```

Azure işlev uygulamanızdan **işlev URL 'sini** yapıştırın ve parçacık Cihazları IoT Central ' de ilişkilendirilmemiş cihazlar olarak göründüğünü görürsünüz. Daha fazla bilgi edinmek için bkz. [Azure IoT Central blog gönderisi Ile parçacık ile desteklenen projelerinizi tümleştirme](https://blog.particle.io/2019/09/26/integrate-particle-with-azure-iot-central/) .

### <a name="example-2-connecting-sigfox-devices-through-the-device-bridge"></a>Örnek 2: Sigfox cihazlarını cihaz Köprüsü aracılığıyla bağlama

Bazı platformlar, bir Web Kancası aracılığıyla gönderilen cihaz iletilerinin biçimini belirtmenize izin vermeyebilir. Bu tür sistemler için, cihaz Köprüsü tarafından işlem yapılmadan önce ileti yükünü beklenen gövde biçimine dönüştürmeniz gerekir. Dönüştürmeyi, cihaz Köprüsü çalıştıran aynı Azure işlevinde yapabilirsiniz.

Bu bölümde, bir Sigfox Web kancası tümleştirmesinin yükünün cihaz Köprüsü tarafından beklenen gövde biçimine nasıl dönüştürüleceği gösterilmektedir. Sigfox bulutu, cihaz verilerini onaltılık dize biçiminde iletir. Kolaylık olması için, cihaz Köprüsü Bu biçim için bir dönüştürme işlevi içerir. Bu, bir Sigfox cihaz yükünde olası alan türlerinin bir alt kümesini kabul eder: `int` ve `uint` 8, 16, 32 veya 64 bit; `float` 32 bit veya 64 bit; little-endian ve Big-endian. Sigfox Web kancası tümleştirmesinden gelen iletileri işlemek için, işlev uygulamasındaki _Iotcıntegration/index.js_ dosyasında aşağıdaki değişiklikleri yapın.

İleti yükünü dönüştürmek için, 21. satırdaki çağrısından önce aşağıdaki kodu ekleyin `handleMessage` , `payloadDefinition` sigfox yük tanımınız ile değiştirin:

```javascript
const payloadDefinition = 'gforce::uint:8 lat::uint:8 lon::uint:16'; // Replace this with your payload definition

req.body = {
    device: {
        deviceId: req.body.device
    },
    measurements: require('./converters/sigfox')(payloadDefinition, req.body.data)
};
```

Sigfox cihazları `204` yanıt kodu bekliyor. 21. satırdaki çağrısından sonra aşağıdaki kodu ekleyin `handleMessage` :

```javascript
context.res = {
    status: 204
};
```

### <a name="example-3-connecting-devices-from-the-things-network-through-the-device-bridge"></a>Örnek 3: cihazları cihaz Köprüsü aracılığıyla nesnelerin ağdan bağlama

Nesnelerin ağ cihazlarını IoT Central 'e bağlamak için:

* Uygulamanıza yeni bir HTTP tümleştirmesi ekleyin: **uygulama > tümleştirmeler > tümleştirme > http tümleştirmesi ekleyin**.
* Uygulamanızın Azure Işlevine gönderilmeden önce cihaz iletilerinizin yükünü otomatik olarak JSON 'ye dönüştüren bir kod çözücü işlevi içerdiğinden emin olun: **application > yük işlevleri > kod çözücüsü**.

Aşağıdaki örnek, ikili verilerden ortak sayısal türlerin kodunu çözmek için kullanabileceğiniz bir JavaScript kod çözücü işlevini göstermektedir:

```javascript
function Decoder(bytes, port) {
  function bytesToFloat(bytes, decimalPlaces) {
    var bits = (bytes[3] << 24) | (bytes[2] << 16) | (bytes[1] << 8) | bytes[0];
    var sign = (bits >>> 31 === 0) ? 1.0 : -1.0;
    var e = bits >>> 23 & 0xff;
    var m = (e === 0) ? (bits & 0x7fffff) << 1 : (bits & 0x7fffff) | 0x800000;
    var f = Math.round((sign * m * Math.pow(2, e - 150)) * Math.pow(10, decimalPlaces)) / Math.pow(10, decimalPlaces);
    return f;
  }

  function bytesToInt32(bytes, signed) {
    var bits = bytes[0] | (bytes[1] << 8) | (bytes[2] << 16) | (bytes[3] << 24);
    var sign = 1;

    if (signed && bits >>> 31 === 1) {
      sign = -1;
      bits = bits & 0x7FFFFFFF;
    }

    return bits * sign;
  }

  function bytesToShort(bytes, signed) {
    var bits = bytes[0] | (bytes[1] << 8);
    var sign = 1;

    if (signed && bits >>> 15 === 1) {
      sign = -1;
      bits = bits & 0x7FFF;
    }

    return bits * sign;
  }

  return {
    temperature: bytesToFloat(bytes.slice(0, 4), 2),
    presscounter: bytesToInt32(bytes.slice(4, 8), true),
    blueLux: bytesToShort(bytes.slice(8, 10), false)
  };
}
```

Tümleştirmeyi tanımladıktan sonra, `handleMessage` Azure işlev uygulamanızın *ıotcıntegration/index.js* dosyasının 21. satırında ' a çağrı yapmadan önce aşağıdaki kodu ekleyin. Bu kod, HTTP tümleştirmesinin gövdesini beklenen biçime dönüştürür.

```javascript
device: {
    deviceId: req.body.hardware_serial.toLowerCase()
},
measurements: req.body.payload_fields
};
```

## <a name="limitations"></a>Sınırlamalar

Cihaz Köprüsü iletileri yalnızca IoT Central iletir ve iletileri cihazlara geri göndermez. Bu nedenle, Özellikler ve komutlar bu cihaz Köprüsü aracılığıyla IoT Central bağlanan cihazlarda çalışmaz. Cihaz ikizi işlemleri desteklenmediği için cihaz Köprüsü aracılığıyla cihaz özelliklerini güncelleştirmek mümkün değildir. Bu özellikleri kullanmak için bir cihazın [Azure IoT cihaz SDK 'lardan](../../iot-hub/iot-hub-devguide-sdks.md)birini kullanarak IoT Central doğrudan bağlanması gerekir.

## <a name="next-steps"></a>Sonraki adımlar
Artık IoT Central cihaz köprüsünün nasıl dağıtılacağını öğrendiğinize göre, önerilen sonraki adım aşağıda verilmiştir:

> [!div class="nextstepaction"]
> [Cihazlarınızı yönetme](howto-manage-devices.md)
