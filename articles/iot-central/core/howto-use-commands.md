---
title: Azure IoT Central çözümünde cihaz komutlarını kullanma
description: Azure IoT Central çözümünde cihaz komutlarını kullanma. Bu öğretici, bir cihaz geliştiricisi olarak, istemci uygulamasındaki cihaz komutlarını Azure IoT Central uygulamanıza nasıl kullanacağınızı gösterir.
author: dominicbetts
ms.author: dobett
ms.date: 01/07/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: e53bf377a7ef8f2293debd288ba25ef8f04ff4fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98611007"
---
# <a name="how-to-use-commands-in-an-azure-iot-central-solution"></a>Azure IoT Central çözümünde komutları kullanma

Bu nasıl yapılır kılavuzunda bir cihaz geliştiricisi olarak, bir cihaz şablonunda tanımlanan komutları nasıl kullanabileceğiniz gösterilmektedir.

İşleci bir cihazdaki komutu çağırmak için IoT Central kullanıcı arabirimini kullanabilir. Komutları bir cihazın davranışını denetler. Örneğin, bir işleç bir cihazı yeniden başlatmak veya tanılama verilerini toplamak için bir komut çağırabilir.

Bir cihaz şunları yapabilir:

* Bir komuta hemen yanıt verin.
* Komutu aldığında IoT Central yanıtlayın ve sonra *uzun süre çalışan komut* tamamlandığında IoT Central bildirin.

Varsayılan olarak, komutlar bir cihazın bağlanmasını bekler ve cihaza ulaşılırsa başarısız olur. Cihaz şablonu Kullanıcı arabiriminde **çevrimdışıyken sıra** seçeneğini belirlerseniz, bir cihaz çevrimiçi olana kadar bir komut sıraya alınabilir. Bu *çevrimdışı komutlar* , bu makalenin ilerleyen bölümlerinde ayrı bir bölümde açıklanmıştır.

## <a name="define-your-commands"></a>Komutlarınızı tanımlayın

Standart komutlar cihaza bir şey yapması için bir cihaza gönderilir. Bir komut, ek bilgiler içeren parametreler içerebilir. Örneğin, bir cihazdaki vanayı açmak için bir komut, vanaın ne kadar açılacağını belirten bir parametreye sahip olabilir. Komutlar Ayrıca cihaz komutu tamamladığında bir dönüş değeri alabilir. Örneğin, bazı tanılamayı çalıştırmak için bir cihaza soran bir komut, dönüş değeri olarak bir tanılama raporu alabilir.

Komutlar bir cihaz şablonunun parçası olarak tanımlanır. Aşağıdaki ekran görüntüsünde, **termostat** cihaz şablonunda **Max-Min rapor al** komut tanımı gösterilmektedir. Bu komut hem istek hem de yanıt parametrelerine sahiptir: 

:::image type="content" source="media/howto-use-commands/command-definition.png" alt-text="Termostat cihaz şablonunda en fazla min rapor alma komutunu gösteren ekran görüntüsü":::

Aşağıdaki tabloda, bir komut özelliğine ait yapılandırma ayarları gösterilmektedir:

| Alan             |Açıklama|
|-------------------|-----------|
|Görünen Ad       |Panolar ve formlarda kullanılan komut değeri.|
| Name            | Komutun adı. IoT Central görünen adından Bu alan için bir değer oluşturur, ancak gerekirse kendi değerini seçebilirsiniz. Bu alanın alfasayısal olması gerekir. Cihaz kodu bu **ad** değerini kullanır.|
| Yetenek Türü | Komutundaki.|
| Çevrimdışıyken kuyruğa al | Bu komutun *çevrimdışı* bir komut yapıp yapmayacağı. |
| Description     | Komut yeteneğinin açıklaması.|
| Yorum     | Komut özelliğiyle ilgili herhangi bir yorum.|
| İstek     | Cihaz komutu için yük.|
| Yanıt     | Cihaz komut yanıtının yükü.|

Aşağıdaki kod parçacığında, cihaz modelindeki komutun JSON temsili gösterilmektedir. Bu örnekte, yanıt değeri birden çok alanı olan karmaşık bir **nesne** türüdür:

```json
{
  "@type": "Command",
  "name": "getMaxMinReport",
  "displayName": "Get Max-Min report.",
  "description": "This command returns the max, min and average temperature from the specified time to the current time.",
  "request": {
    "name": "since",
    "displayName": "Since",
    "description": "Period to return the max-min report.",
    "schema": "dateTime"
  },
  "response": {
    "name" : "tempReport",
    "displayName": "Temperature Report",
    "schema": {
      "@type": "Object",
      "fields": [
        {
          "name": "maxTemp",
          "displayName": "Max temperature",
          "schema": "double"
        },
        {
          "name": "minTemp",
          "displayName": "Min temperature",
          "schema": "double"
        },
        {
          "name" : "avgTemp",
          "displayName": "Average Temperature",
          "schema": "double"
        },
        {
          "name" : "startTime",
          "displayName": "Start Time",
          "schema": "dateTime"
        },
        {
          "name" : "endTime",
          "displayName": "End Time",
          "schema": "dateTime"
        }
      ]
    }
  }
}
```

> [!TIP]
> Cihaz şablonu sayfasından bir cihaz modelini dışarı aktarabilirsiniz.

Bu komut tanımını aşağıdaki alanları kullanarak Kullanıcı arabiriminin ekran görüntüsü ile ilişkilendirebilirsiniz:

* `@type` özelliğin türünü belirtmek için: `Command`
* `name` , komut değeri için.

Görünen ad ve açıklama gibi isteğe bağlı alanlar, arabirime ve yeteneklere daha fazla ayrıntı eklemenizi sağlar.

## <a name="standard-commands"></a>Standart komutlar

Bu bölümde, bir cihazın bir yanıt değerini, komutu alır almaz nasıl gönderdiği gösterilmektedir.

Aşağıdaki kod parçacığı, bir cihazın başarılı bir kodu hemen göndererek bir komuta nasıl yanıt verebilmesini göstermektedir:

> [!NOTE]
> Bu makale basitlik için Node.js kullanır. Diğer dil örnekleri için bkz. [Azure IoT Central uygulamanıza istemci uygulaması oluşturma ve bağlama](tutorial-connect-device.md) öğreticisine bakın.

```javascript
client.onDeviceMethod('getMaxMinReport', commandHandler);

// ...

const commandHandler = async (request, response) => {
  switch (request.methodName) {
  case 'getMaxMinReport': {
    console.log('MaxMinReport ' + request.payload);
    await sendCommandResponse(request, response, 200, deviceTemperatureSensor.getMaxMinReportObject());
    break;
  }
  default:
    await sendCommandResponse(request, response, 404, 'unknown method');
    break;
  }
};

const sendCommandResponse = async (request, response, status, payload) => {
  try {
    await response.send(status, payload);
    console.log('Response to method \'' + request.methodName +
              '\' sent successfully.' );
  } catch (err) {
    console.error('An error ocurred when sending a method response:\n' +
              err.toString());
  }
};
```

`onDeviceMethod`Yöntemini ayarlayan çağrı `commandHandler` . Bu komut işleyicisi:

1. Komutun adını denetler.
1. Komut için `getMaxMinReport` , `getMaxMinReportObject` dönüş nesnesine dahil edilecek değerleri almak için çağırır.
1. `sendCommandResponse`Yanıtı IoT Central geri göndermek için çağrılar. Bu yanıt, `200` başarıyı belirten yanıt kodunu içerir.

Aşağıdaki ekran görüntüsünde, başarılı komut yanıtının IoT Central Kullanıcı arabiriminde nasıl görüntüleyeceği gösterilmektedir:

:::image type="content" source="media/howto-use-commands/simple-command-ui.png" alt-text="Standart bir komut için komut yükünün nasıl görüntüleneceğini gösteren ekran görüntüsü":::

## <a name="long-running-commands"></a>Uzun süre çalışan komutlar

Bu bölümde, bir cihazın komutun kabul eden bir onaylama gönderimi nasıl geciktirilemeyeceği gösterilmektedir.

Aşağıdaki kod parçacığı, bir cihazın uzun süre çalışan bir komutu nasıl uygulayabileceğinizi göstermektedir:

> [!NOTE]
> Bu makale basitlik için Node.js kullanır. Diğer dil örnekleri için bkz. [Azure IoT Central uygulamanıza istemci uygulaması oluşturma ve bağlama](tutorial-connect-device.md) öğreticisine bakın.

```javascript
client.onDeviceMethod('rundiagnostics', commandHandler);

// ...

const commandHandler = async (request, response) => {
  switch (request.methodName) {
  case 'rundiagnostics': {
    console.log('Starting long-running diagnostics run ' + request.payload);
    await sendCommandResponse(request, response, 202, 'Diagnostics run started');

    // Long-running operation here
    // ...

    const patch = {
      rundiagnostics: {
        value: 'Diagnostics run complete at ' + new Date().toLocaleString()
      }
    };

    deviceTwin.properties.reported.update(patch, function (err) {
      if (err) throw err;
      console.log('Properties have been reported for component');
    });
    break;
  }
  default:
    await sendCommandResponse(request, response, 404, 'unknown method');
    break;
  }
};
```

`onDeviceMethod`Yöntemini ayarlayan çağrı `commandHandler` . Bu komut işleyicisi:

1. Komutun adını denetler.
1. `sendCommandResponse`Yanıtı IoT Central geri göndermek için çağrılar. Bu yanıt, `202` bekleyen sonuçları belirten yanıt kodunu içerir.
1. Uzun süre çalışan işlemi tamamlar.
1. Komutun tamamlandığını IoT Central söylemek için komutuyla aynı ada sahip bildirilen bir özelliği kullanır.

Aşağıdaki ekran görüntüsünde, 202 yanıt kodunu aldığında komut yanıtının IoT Central Kullanıcı arabiriminde nasıl görüntüleyeceği gösterilmektedir:

:::image type="content" source="media/howto-use-commands/long-running-start.png" alt-text="Cihazdan anında yanıt gösteren ekran görüntüsü":::

Aşağıdaki ekran görüntüsünde, komutun tamamlandığını belirten özellik güncelleştirmesi alındığında IoT Central kullanıcı arabirimi gösterilmektedir:

:::image type="content" source="media/howto-use-commands/long-running-finish.png" alt-text="Uzun süre çalışan komutun tamamlandığını gösteren ekran görüntüsü":::

## <a name="offline-commands"></a>Çevrimdışı komutlar

Bu bölümde, bir cihazın çevrimdışı bir komutu nasıl işlediği gösterilmektedir. Bir cihaz çevrimiçiyse, çevrimdışı komutu alındıkları anda işleyebilir. Bir cihaz çevrimdışı ise, bir sonraki IoT Central bağlandığı zaman çevrimdışı komutu işler. Cihazlar, bir çevrimdışı komuta yanıt olarak dönüş değeri gönderemez.

> [!NOTE]
> Bu makale basitlik için Node.js kullanır.

Aşağıdaki ekran görüntüsünde, **Generatediagnostics** adlı bir çevrimdışı komut gösterilmektedir. İstek parametresi, **StartTime** adlı bir DateTime özelliği ve **Bank** adlı bir tamsayı numaralandırma özelliği olan bir nesnedir:

:::image type="content" source="media/howto-use-commands/offline-command.png" alt-text="Çevrimdışı bir komutun Kullanıcı arabirimini gösteren ekran görüntüsü":::

Aşağıdaki kod parçacığı, bir istemcinin çevrimdışı komutları nasıl dinleyebildiğini ve ileti içeriğini görüntülemesini gösterir:

```javascript
client.on('message', function (msg) {
  console.log('Body: ' + msg.data);
  console.log('Properties: ' + JSON.stringify(msg.properties));
  client.complete(msg, function (err) {
    if (err) {
      console.error('complete error: ' + err.toString());
    } else {
      console.log('complete sent');
    }
  });
});
```

Önceki kod parçacığındaki çıktı, **StartTime** ve **Banka** değerleri ile yükü gösterir. Özellik listesi, **Yöntem-adı** liste öğesinde komut adını içerir:

```output
Body: {"StartTime":"2021-01-06T06:00:00.000Z","Bank":2}
Properties: {"propertyList":[{"key":"iothub-ack","value":"none"},{"key":"method-name","value":"GenerateDiagnostics"}]}
```

> [!NOTE]
> Çevrimdışı komutlar için varsayılan yaşam süresi 24 saattir ve sonrasında iletinin süresi dolar.

## <a name="next-steps"></a>Sonraki adımlar

Azure IoT Central uygulamanızda komutları nasıl kullanacağınızı öğrendiğinize göre, farklı dillerdeki kod örneklerinin tamamını görmek için komut parametreleri hakkında daha fazla bilgi edinmek ve [Azure IoT Central uygulamanıza bir istemci uygulaması oluşturmak ve bunları bağlamak](tutorial-connect-device.md) için [yükleri](concepts-telemetry-properties-commands.md) bölümüne bakın.
