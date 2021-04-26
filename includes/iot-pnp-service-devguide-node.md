---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 10/20/2020
ms.openlocfilehash: dfeeb451912dd32770a6ae92d73de83851d9d8f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "102245094"
---
Aşağıdaki kaynaklar da kullanılabilir:

- [ SDK başvuru belgeleriniNode.js](/javascript/api/azure-iothub)
- [Hizmet istemcisi örnekleri](https://github.com/Azure/azure-iot-sdk-node/blob/master/service/samples/javascript/twin.js)
- [Dijital TWINS örnekleri](https://github.com/Azure/azure-iot-sdk-node/blob/master/service/samples/javascript/get_digital_twin.js)

## <a name="iot-hub-service-client-examples"></a>IoT Hub hizmeti istemci örnekleri

Bu bölümde, IoT Hub hizmeti istemcisi ve **kayıt defteri** ve **Istemci** sınıfları kullanılarak JavaScript örnekleri gösterilmektedir. Cihaz TWINS 'i kullanarak cihaz durumuyla etkileşim kurmak için **kayıt defteri** sınıfını kullanın. IoT Hub [cihaz kayıtlarını sorgulamak](../articles/iot-hub/iot-hub-devguide-query-language.md) Için **kayıt defteri** sınıfını da kullanabilirsiniz. Cihazdaki komutları çağırmak için **istemci** sınıfını kullanırsınız. Cihazın [Dtdl](../articles/iot-pnp/concepts-digital-twin.md) modeli, cihazın uyguladığı özellikleri ve komutları tanımlar. Kod parçacıklarında, `deviceId` değişken IoT Hub 'ınıza kayıtlı olan ıot Tak ve kullan cihazının CIHAZ kimliğini barındırır.

### <a name="get-the-device-twin-and-model-id"></a>Cihaz ikizi ve model KIMLIĞINI al

IoT Hub 'ınıza bağlanan IoT Tak ve Kullan cihazının cihaz ikizi ve model KIMLIĞINI almak için:

```javascript
var Registry = require('azure-iothub').Registry;

// ...

var registry = Registry.fromConnectionString(connectionString);
registry.getTwin(deviceId, function(err, twin) {
  if (err) {
    console.error(err.message);
  } else {
    console.log('Model Id: ' + twin.modelId);
    console.log(JSON.stringify(twin, null, 2));
  }
}
```

### <a name="update-device-twin"></a>Cihaz ikizi Güncelleştir

Aşağıdaki kod parçacığı, `targetTemperature` bir cihazdaki özelliğinin nasıl güncelleştirilmesini gösterir. Örnek, güncelleştirmeden önce ikizi nasıl almanız gerektiğini gösterir. Özelliği, cihazın varsayılan bileşeninde tanımlanmıştır:

```javascript
var Registry = require('azure-iothub').Registry;
var registry = Registry.fromConnectionString(connectionString);

registry.getTwin(deviceId, function(err, twin) {
  if (err) {
    console.error(err.message);
  } else {
    var twinPatch = {
      properties: {
        desired: {
          targetTemperature: 42
        }
      }
    };
    twin.update(twinPatch, function(err, twin) {
      if (err) {
        console.error(err.message);
      } else {
        console.log(JSON.stringify(twin, null, 2));
      }
    }
  }
}
```

Aşağıdaki kod parçacığında, `targetTemperature` bir bileşen üzerinde özelliğin nasıl güncelleştirilecek gösterilmektedir. Örnek, güncelleştirmeden önce ikizi nasıl almanız gerektiğini gösterir. Özelliği **thermostat1** bileşeninde tanımlanmıştır:

```javascript
var Registry = require('azure-iothub').Registry;
var registry = Registry.fromConnectionString(connectionString);

registry.getTwin(deviceId, function(err, twin) {
  if (err) {
    console.error(err.message);
  } else {
    var twinPatch = {
      properties: {
        desired: {
          thermostat1:
          {
            __t: "c",
            targetTemperature: 45
          }
        }
      }
    };
    twin.update(twinPatch, function(err, twin) {
      if (err) {
        console.error(err.message);
      } else {
        console.log(JSON.stringify(twin, null, 2));
      }
    }
  }
}
```

Bir bileşenin özelliği için, özellik düzeltme eki aşağıdaki örneğe benzer şekilde görünür:

```json
{
  "thermostat1":
  {
    "__t": "c",
    "targetTemperature": 20
  }
}
```

### <a name="call-command"></a>Call komutu

Aşağıdaki kod parçacığı, `getMaxMinReport` varsayılan bir bileşende tanımlanan komutun nasıl çağıralınacağını gösterir:

```javascript
var Client = require('azure-iothub').Client;

// ...

var client = Client.fromConnectionString(connectionString);

var methodParams = {
  methodName: "getMaxMinReport",
  payload: new Date().getMinutes -2,
  responseTimeoutInSeconds: 15
};

client.invokeDeviceMethod(deviceId, methodParams, function (err, result) {
  if (err) {
    console.error('Failed to invoke method \'' + methodParams.methodName + '\': ' + err.message);
  } else {
    console.log(methodParams.methodName + ' on ' + deviceId + ':');
    console.log(JSON.stringify(result, null, 2));
  }
});
```

Aşağıdaki kod parçacığında, bir bileşende komutun nasıl çağrılacağını gösterir `getMaxMinReport` . Komut **thermostat1** bileşeninde tanımlanmıştır:

```javascript
var Client = require('azure-iothub').Client;

// ...

var client = Client.fromConnectionString(connectionString);

var methodParams = {
  methodName: "thermostat1*getMaxMinReport",
  payload: new Date().getMinutes -2,
  responseTimeoutInSeconds: 15
};

client.invokeDeviceMethod(deviceId, methodParams, function (err, result) {
  if (err) {
    console.error('Failed to invoke method \'' + methodParams.methodName + '\': ' + err.message);
  } else {
    console.log(methodParams.methodName + ' on ' + deviceId + ':');
    console.log(JSON.stringify(result, null, 2));
  }
});
```

## <a name="iot-hub-digital-twin-examples"></a>IoT Hub Digital ikizi örnekleri

Dijital TWINS kullanarak cihaz durumuyla etkileşim kurmak için **Digitaltwınclient** sınıfını kullanırsınız. Cihazın [Dtdl](../articles/iot-pnp/concepts-digital-twin.md) modeli, cihazın uyguladığı özellikleri ve komutları tanımlar.

Bu bölümde, dijital TWINS API 'sini kullanan JavaScript örnekleri gösterilmektedir.

`digitalTwinId`Değişkeni, IoT Hub 'ınıza kayıtlı olan ıot Tak ve kullan cihazının CIHAZ kimliğini içerir.

### <a name="get-the-digital-twin-and-model-id"></a>Dijital ikizi ve model KIMLIĞINI alın

IoT Hub 'ınıza bağlanan IoT Tak ve Kullan cihazının dijital ikizi ve model KIMLIĞINI almak için:

```javascript
const IoTHubTokenCredentials = require('azure-iothub').IoTHubTokenCredentials;
const DigitalTwinClient = require('azure-iothub').DigitalTwinClient;
const { inspect } = require('util');

// ...

const credentials = new IoTHubTokenCredentials(connectionString);
const digitalTwinClient = new DigitalTwinClient(credentials);

const digitalTwin = await digitalTwinClient.getDigitalTwin(digitalTwinId);

console.log(inspect(digitalTwin));
console.log('Model Id: ' + inspect(digitalTwin.$metadata.$model));
```

### <a name="update-digital-twin"></a>Dijital ikizi Güncelleştir

Aşağıdaki kod parçacığı, `targetTemperature` bir cihazdaki özelliğinin nasıl güncelleştirilmesini gösterir. Özelliği, cihazın varsayılan bileşeninde tanımlanmıştır:

```javascript
const IoTHubTokenCredentials = require('azure-iothub').IoTHubTokenCredentials;
const DigitalTwinClient = require('azure-iothub').DigitalTwinClient;

// ...

const credentials = new IoTHubTokenCredentials(connString);
const digitalTwinClient = new DigitalTwinClient(credentials);

const patch = [{
  op: 'add',
  path: '/targetTemperature',
  value: 42
}];
await digitalTwinClient.updateDigitalTwin(digitalTwinId, patch);
```

Aşağıdaki kod parçacığında, `targetTemperature` bir bileşen üzerinde özelliğin nasıl güncelleştirilecek gösterilmektedir. Özelliği **thermostat1** bileşeninde tanımlanmıştır:

```javascript
const IoTHubTokenCredentials = require('azure-iothub').IoTHubTokenCredentials;
const DigitalTwinClient = require('azure-iothub').DigitalTwinClient;

// ...

const credentials = new IoTHubTokenCredentials(connString);
const digitalTwinClient = new DigitalTwinClient(credentials);

const patch = [{
  op: 'add',
  path: '/thermostat1/targetTemperature',
  value: 42
}];
await digitalTwinClient.updateDigitalTwin(digitalTwinId, patch);
```

### <a name="call-command"></a>Call komutu

Aşağıdaki kod parçacığı, `getMaxMinReport` varsayılan bir bileşende tanımlanan komutun nasıl çağıralınacağını gösterir:

```javascript
const IoTHubTokenCredentials = require('azure-iothub').IoTHubTokenCredentials;
const DigitalTwinClient = require('azure-iothub').DigitalTwinClient;
const { inspect } = require('util');

// ...

const commandPayload = new Date().getMinutes -2;

const credentials = new IoTHubTokenCredentials(connectionString);
const digitalTwinClient = new DigitalTwinClient(credentials);

const options = {
  connectTimeoutInSeconds: 30,
  responseTimeoutInSeconds: 40
};
const commandResponse = await digitalTwinClient.invokeCommand(digitalTwinId, "getMaxMinReport", commandPayload, options);

console.log(inspect(commandResponse));
```

Aşağıdaki kod parçacığında, bir bileşende komutun nasıl çağrılacağını gösterir `getMaxMinReport` . Komut **thermostat1** bileşeninde tanımlanmıştır:

```javascript
const IoTHubTokenCredentials = require('azure-iothub').IoTHubTokenCredentials;
const DigitalTwinClient = require('azure-iothub').DigitalTwinClient;
const { inspect } = require('util');

// ...

const commandPayload = new Date().getMinutes -2;

const credentials = new IoTHubTokenCredentials(connectionString);
const digitalTwinClient = new DigitalTwinClient(credentials);

const options = {
  connectTimeoutInSeconds: 30,
  responseTimeoutInSeconds: 40
};
const commandResponse = await digitalTwinClient.invokeComponentCommand(digitalTwinId, "thermostat1", "getMaxMinReport", commandPayload, options);

console.log(inspect(commandResponse));
```

## <a name="read-device-telemetry"></a>Cihaz telemetrisini okuma

IoT Tak ve Kullan cihazları DTDL modelinde tanımlanan telemetrileri IoT Hub gönderir. Varsayılan olarak, IoT Hub Telemetriyi kullanabileceğiniz bir Event Hubs uç noktasına yönlendirir. Daha fazla bilgi edinmek için bkz. [farklı uç noktalara cihazdan buluta iletiler göndermek için IoT Hub ileti yönlendirmeyi kullanma](../articles/iot-hub/iot-hub-devguide-messages-d2c.md).

Aşağıdaki kod parçacığı, varsayılan Event Hubs uç noktasından Telemetriyi okumayı gösterir. Bu kod parçacığındaki kod, IoT Hub hızlı başlangıçta [bir cihazdan IoT Hub 'ına telemetri gönderme ve arka uç uygulamasıyla okuma](../articles/iot-hub/quickstart-send-telemetry-node.md)işlemi gerçekleştirmektir:

```javascript
const { EventHubConsumerClient } = require("@azure/event-hubs");

var printError = function (err) {
  console.log(err.message);
};

var printMessages = function (messages) {
  for (const message of messages) {
    console.log("Telemetry received: ");
    console.log(JSON.stringify(message.body));
    console.log("Properties (set by device): ");
    console.log(JSON.stringify(message.properties));
    console.log("System properties (set by IoT Hub): ");
    console.log(JSON.stringify(message.systemProperties));
    console.log("");
  }
};

// ...

const clientOptions = {};

const consumerClient = new EventHubConsumerClient("$Default", connectionString, clientOptions);

consumerClient.subscribe({
  processEvents: printMessages,
  processError: printError,
});
```

Önceki koddan bulunan aşağıdaki çıktıda, çok bileşen **TemperatureController** IoT Tak ve kullan cihazının gönderdiği sıcaklık telemetrisi gösterilmektedir. `dt-subject`System özelliği, Telemetriyi gönderen bileşenin adını gösterir. Bu örnekte, iki bileşen `thermostat1` ve `thermostat2` dtdl modelinde tanımlanmıştır. `dt-dataschema`System özelliği, model kimliğini gösterir:

```cmd/sh
Telemetry received:
{"temperature":68.77370855171125}
Properties (set by device):
undefined
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-connection-auth-method":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","iothub-connection-auth-generation-id":"637388034455888246","iothub-enqueuedtime":1603206669320,"iothub-message-source":"Telemetry","dt-subject":"thermostat1","dt-dataschema":"dtmi:com:example:TemperatureController;1","contentType":"application/json","contentEncoding":"utf-8"}

Telemetry received:
{"temperature":30.833394506549226}
Properties (set by device):
undefined
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-connection-auth-method":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","iothub-connection-auth-generation-id":"637388034455888246","iothub-enqueuedtime":1603206665835,"iothub-message-source":"Telemetry","dt-subject":"thermostat2","dt-dataschema":"dtmi:com:example:TemperatureController;1","contentType":"application/json","contentEncoding":"utf-8"}
```

## <a name="read-device-twin-change-notifications"></a>Cihaz ikizi değişiklik bildirimlerini oku

Desteklenen bir uç noktaya yönlendirmek üzere cihaz ikizi değişiklik bildirimleri oluşturmak için IoT Hub yapılandırabilirsiniz. Daha fazla bilgi edinmek için bkz. [IoT Hub ileti yönlendirmeyi kullanarak, telemetri olmayan olaylar > farklı uç noktalara cihazdan buluta iletiler gönderme](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events).

Önceki JavaScript kod parçacığında gösterilen kod, bileşen olmayan bir termostat cihazı için IoT Hub cihaz ikizi değişiklik bildirimleri oluşturduğunda aşağıdaki çıktıyı üretir. Uygulama özellikleri `iothub-message-schema` ve `opType` değişiklik bildirimi türü hakkında bilgi verir:

```cmd/sh
Telemetry received:
{"version":4,"properties":{"reported":{"maxTempSinceLastReboot":42.1415152639582,"$metadata":{"$lastUpdated":"2020-10-21T10:01:40.1281138Z","maxTempSinceLastReboot":{"$lastUpdated":"2020-10-21T10:01:40.1281138Z"}},"$version":3}}}
Properties (set by device):
{"hubName":"my-pnp-hub","deviceId":"my-pnp-device","operationTimestamp":"2020-10-21T10:01:40.1281138+00:00","iothub-message-schema":"twinChangeNotification","opType":"updateTwin"}
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-enqueuedtime":1603274500282,"iothub-message-source":"twinChangeEvents","userId":{"type":"Buffer","data":[109,121,45,112,110,112,45,104,117,98]},"correlationId":"11ed82d13f50","contentType":"application/json","contentEncoding":"utf-8"}
```

Önceki JavaScript kod parçacığında gösterilen kod, bileşenler içeren bir cihaz için cihaz ikizi değişiklik bildirimleri IoT Hub oluşturduğunda aşağıdaki çıktıyı üretir. Bu örnek, bir termostat bileşeni olan bir sıcaklık algılayıcısı cihazı bildirim oluşturduğunda çıktıyı gösterir. Uygulama özellikleri `iothub-message-schema` ve `opType` değişiklik bildirimi türü hakkında bilgi verir:

```cmd/sh
Telemetry received:
{"version":4,"properties":{"reported":{"thermostat1":{"maxTempSinceLastReboot":3.5592971602417913,"__t":"c"},"$metadata":{"$lastUpdated":"2020-10-21T10:07:51.8284866Z","thermostat1":{"$lastUpdated":"2020-10-21T10:07:51.8284866Z","maxTempSinceLastReboot":{"$lastUpdated":"2020-10-21T10:07:51.8284866Z"},"__t":{"$lastUpdated":"2020-10-21T10:07:51.8284866Z"}}},"$version":3}}}
Properties (set by device):
{"hubName":"my-pnp-hub","deviceId":"my-pnp-device","operationTimestamp":"2020-10-21T10:07:51.8284866+00:00","iothub-message-schema":"twinChangeNotification","opType":"updateTwin"}
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-enqueuedtime":1603274871951,"iothub-message-source":"twinChangeEvents","userId":{"type":"Buffer","data":[109,121,45,112,110,112,45,104,117,98]},"correlationId":"11ee605b195f","contentType":"application/json","contentEncoding":"utf-8"}
```

## <a name="read-digital-twin-change-notifications"></a>Dijital ikizi değişiklik bildirimlerini oku

Desteklenen bir uç noktaya yönlendirmek için dijital ikizi değişiklik bildirimleri oluşturacak şekilde IoT Hub yapılandırabilirsiniz. Daha fazla bilgi edinmek için bkz. [IoT Hub ileti yönlendirmeyi kullanarak, telemetri olmayan olaylar > farklı uç noktalara cihazdan buluta iletiler gönderme](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events).

Önceki JavaScript kod parçacığında gösterilen kod, IoT Hub bileşen olmayan bir termostat cihazı için dijital ikizi değişiklik bildirimleri oluşturduğunda aşağıdaki çıktıyı üretir. Uygulama özellikleri `iothub-message-schema` ve `opType` değişiklik bildirimi türü hakkında bilgi verir:

```cmd/sh
Telemetry received:
[{"op":"add","path":"/$metadata/maxTempSinceLastReboot","value":{"lastUpdateTime":"2020-10-21T10:01:40.1281138Z"}},{"op":"add","path":"/maxTempSinceLastReboot","value":42.1415152639582}]
Properties (set by device):
{"hubName":"my-pnp-hub","deviceId":"my-pnp-device","operationTimestamp":"2020-10-21T10:01:40.1281138+00:00","iothub-message-schema":"digitalTwinChangeNotification","opType":"updateTwin"}
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-enqueuedtime":1603274500282,"iothub-message-source":"digitalTwinChangeEvents","userId":{"type":"Buffer","data":[109,121,45,112,110,112,45,104,117,98]},"correlationId":"11ed82d13f50","contentType":"application/json-patch+json","contentEncoding":"utf-8"}
```

Önceki JavaScript kod parçacığında gösterilen kod, IoT Hub bileşenleri olan bir cihaz için dijital ikizi değişiklik bildirimleri oluşturduğunda aşağıdaki çıktıyı üretir. Bu örnek, bir termostat bileşeni olan bir sıcaklık algılayıcısı cihazı bildirim oluşturduğunda çıktıyı gösterir. Uygulama özellikleri `iothub-message-schema` ve `opType` değişiklik bildirimi türü hakkında bilgi verir:

```cmd/sh
Telemetry received:
[{"op":"add","path":"/thermostat1","value":{"$metadata":{"maxTempSinceLastReboot":{"lastUpdateTime":"2020-10-21T10:07:51.8284866Z"}},"maxTempSinceLastReboot":3.5592971602417913}}]
Properties (set by device):
{"hubName":"my-pnp-hub","deviceId":"my-pnp-device","operationTimestamp":"2020-10-21T10:07:51.8284866+00:00","iothub-message-schema":"digitalTwinChangeNotification","opType":"updateTwin"}
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-enqueuedtime":1603274871951,"iothub-message-source":"digitalTwinChangeEvents","userId":{"type":"Buffer","data":[109,121,45,112,110,112,45,104,117,98]},"correlationId":"11ee605b195f","contentType":"application/json-patch+json","contentEncoding":"utf-8"}
```
