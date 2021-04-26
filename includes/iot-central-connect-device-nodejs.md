---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 03/31/2021
ms.openlocfilehash: 6c97ee01dd1ad5b669142d74a02bada010525e81
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491130"
---
## <a name="prerequisites"></a>Önkoşullar

Bu makaledeki adımları tamamlamak için aşağıdaki kaynaklar gereklidir:

* **Özel uygulama** şablonu kullanılarak oluşturulan bir Azure IoT Central uygulaması. Daha fazla bilgi için bkz. [Uygulama oluşturma hızlı başlangıcı](../articles/iot-central/core/quick-deploy-iot-central.md). Uygulamanın 14 Temmuz 2020 tarihinde veya sonrasında oluşturulmuş olması gerekir.
* [Node.js](https://nodejs.org/) sürüm 6 veya üzeri yüklü bir geliştirme makinesi. `node --version`Sürümünüzü denetlemek için komut satırında çalıştırabilirsiniz. Bu öğreticideki yönergeler, Windows komut isteminde **node** komutunu çalıştırmakta olduğunuzu varsayar. Ancak, diğer birçok işletim sisteminde Node.js kullanabilirsiniz.
* Örnek kodu içeren Node.jsGitHub deposu [için Microsoft Azure ıOT SDK 'sının ](https://github.com/Azure/azure-iot-sdk-node) yerel bir kopyası. Deponun bir kopyasını indirmek için bu bağlantıyı kullanın: ZIP 'i [indirin](https://github.com/Azure/azure-iot-sdk-node/archive/master.zip). Sonra dosyayı yerel makinenizde uygun bir konuma ayıklayın.

## <a name="review-the-code"></a>Kodu gözden geçirin

Daha önce indirdiğiniz Node.js Microsoft Azure IoT SDK kopyasında, *Azure-IoT-SDK-node/Device/Samples/PNP/pnpTemperatureController.js* dosyasını bir metin düzenleyicisinde açın.

IoT Central bağlanmak için örneği çalıştırdığınızda, cihazı kaydetmek ve bir bağlantı dizesi oluşturmak için cihaz sağlama hizmeti 'ni (DPS) kullanır. Örnek, gereken DPS bağlantı bilgilerini komut satırı ortamından alır.

`main`Yöntemi:

* Bir `client` nesne oluşturur ve `dtmi:com:example:TemperatureController;2` bağlantıyı açmadan önce model kimliğini ayarlar. IoT Central, bu cihazın cihaz şablonunu tanımlamak veya oluşturmak için model KIMLIĞINI kullanır. Daha fazla bilgi için bkz. cihaz [şablonuyla bir cihazı ilişkilendirme](../articles/iot-central/core/concepts-get-connected.md#associate-a-device-with-a-device-template).
* Üç komut için komut işleyicileri oluşturur.
* Her bir termostat bileşeni için her 5 saniyede bir sıcaklık telemetrisi göndermek üzere bir döngü başlatır.
* Her 6 saniyede bir çalışma kümesi boyutu telemetrisi göndermek için varsayılan bileşen için bir döngü başlatır.
* Her bir `maxTempSinceLastReboot` termostat bileşeni için özelliğini gönderir.
* Cihaz bilgileri özelliklerini gönderir.
* Üç bileşen için yazılabilir Özellikler işleyicileri oluşturur.

```javascript
async function main() {
  // ...

  // fromConnectionString must specify a transport, coming from any transport package.
  const client = Client.fromConnectionString(deviceConnectionString, Protocol);
  console.log('Connecting using connection string: ' + deviceConnectionString);
  let resultTwin;

  try {
    // Add the modelId here
    await client.setOptions(modelIdObject);
    await client.open();
    console.log('Enabling the commands on the client');
    client.onDeviceMethod(commandNameGetMaxMinReport1, commandHandler);
    client.onDeviceMethod(commandNameGetMaxMinReport2, commandHandler);
    client.onDeviceMethod(commandNameReboot, commandHandler);

    // Send Telemetry after some interval
    let index1 = 0;
    let index2 = 0;
    let index3 = 0;
    intervalToken1 = setInterval(() => {
      const data = JSON.stringify(thermostat1.updateSensor().getCurrentTemperatureObject());
      sendTelemetry(client, data, index1, thermostat1ComponentName).catch((err) => console.log('error ', err.toString()));
      index1 += 1;
    }, 5000);

    intervalToken2 = setInterval(() => {
      const data = JSON.stringify(thermostat2.updateSensor().getCurrentTemperatureObject());
      sendTelemetry(client, data, index2, thermostat2ComponentName).catch((err) => console.log('error ', err.toString()));
      index2 += 1;
    }, 5500);


    intervalToken3 = setInterval(() => {
      const data = JSON.stringify({ workingset: 1 + (Math.random() * 90) });
      sendTelemetry(client, data, index3, null).catch((err) => console.log('error ', err.toString()));
      index3 += 1;
    }, 6000);

    // attach a standard input exit listener
    exitListener(client);

    try {
      resultTwin = await client.getTwin();
      // Only report readable properties
      const patchRoot = helperCreateReportedPropertiesPatch({ serialNumber: serialNumber }, null);
      const patchThermostat1Info = helperCreateReportedPropertiesPatch({
        maxTempSinceLastReboot: thermostat1.getMaxTemperatureValue(),
      }, thermostat1ComponentName);

      const patchThermostat2Info = helperCreateReportedPropertiesPatch({
        maxTempSinceLastReboot: thermostat2.getMaxTemperatureValue(),
      }, thermostat2ComponentName);

      const patchDeviceInfo = helperCreateReportedPropertiesPatch({
        manufacturer: 'Contoso Device Corporation',
        model: 'Contoso 47-turbo',
        swVersion: '10.89',
        osName: 'Contoso_OS',
        processorArchitecture: 'Contoso_x86',
        processorManufacturer: 'Contoso Industries',
        totalStorage: 65000,
        totalMemory: 640,
      }, deviceInfoComponentName);

      // the below things can only happen once the twin is there
      updateComponentReportedProperties(resultTwin, patchRoot, null);
      updateComponentReportedProperties(resultTwin, patchThermostat1Info, thermostat1ComponentName);
      updateComponentReportedProperties(resultTwin, patchThermostat2Info, thermostat2ComponentName);
      updateComponentReportedProperties(resultTwin, patchDeviceInfo, deviceInfoComponentName);
      desiredPropertyPatchListener(resultTwin, [thermostat1ComponentName, thermostat2ComponentName, deviceInfoComponentName]);
    } catch (err) {
      console.error('could not retrieve twin or report twin properties\n' + err.toString());
    }
  } catch (err) {
    console.error('could not connect Plug and Play client or could not attach interval function for telemetry\n' + err.toString());
  }
}
```

`provisionDevice`İşlevi, cihazın IoT Central kaydetmek ve bağlanmak IÇIN DPS nasıl kullandığını gösterir. Yük, [cihazı cihaz şablonuyla ilişkilendirmek](../articles/iot-central/core/concepts-get-connected.md#associate-a-device-with-a-device-template)için IoT Central tarafından kullanılan model kimliğini içerir:

```javascript
async function provisionDevice(payload) {
  var provSecurityClient = new SymmetricKeySecurityClient(registrationId, symmetricKey);
  var provisioningClient = ProvisioningDeviceClient.create(provisioningHost, idScope, new ProvProtocol(), provSecurityClient);

  if (!!(payload)) {
    provisioningClient.setProvisioningPayload(payload);
  }

  try {
    let result = await provisioningClient.register();
    deviceConnectionString = 'HostName=' + result.assignedHub + ';DeviceId=' + result.deviceId + ';SharedAccessKey=' + symmetricKey;
    console.log('registration succeeded');
    console.log('assigned hub=' + result.assignedHub);
    console.log('deviceId=' + result.deviceId);
    console.log('payload=' + JSON.stringify(result.payload));
  } catch (err) {
    console.error("error registering device: " + err.toString());
  }
}
```

`sendTelemetry`İşlevi, cihazın sıcaklık telemetrisini IoT Central nasıl göndereceğini gösterir. Bileşenlerinden telemetri için bileşen adı ile çağrılan bir özelliği ekler `$.sub` :

```javascript
async function sendTelemetry(deviceClient, data, index, componentName) {
  if (!!(componentName)) {
    console.log('Sending telemetry message %d from component: %s ', index, componentName);
  } else {
    console.log('Sending telemetry message %d from root interface', index);
  }
  const msg = new Message(data);
  if (!!(componentName)) {
    msg.properties.add(messageSubjectProperty, componentName);
  }
  msg.contentType = 'application/json';
  msg.contentEncoding = 'utf-8';
  await deviceClient.sendEvent(msg);
}
```

`main`Yöntemi, `helperCreateReportedPropertiesPatch` özellik güncelleştirme iletileri oluşturmak için çağrılan bir yardımcı yöntemi kullanır. Bu yöntem, özelliği gönderen bileşeni belirtmek için isteğe bağlı bir parametre alır.:

```javascript
const helperCreateReportedPropertiesPatch = (propertiesToReport, componentName) => {
  let patch;
  if (!!(componentName)) {
    patch = { };
    propertiesToReport.__t = 'c';
    patch[componentName] = propertiesToReport;
  } else {
    patch = { };
    patch = propertiesToReport;
  }
  if (!!(componentName)) {
    console.log('The following properties will be updated for component: ' + componentName);
  } else {
    console.log('The following properties will be updated for root interface.');
  }
  console.log(patch);
  return patch;
};
```

`main`Yöntemi, IoT Central yazılabilir özelliklerde yapılan güncelleştirmeleri işlemek için aşağıdaki yöntemi kullanır. Yönteminin sürümü ve durum kodu ile yanıtı nasıl oluşturduğuna dikkat edin:

```javascript
const desiredPropertyPatchListener = (deviceTwin, componentNames) => {
  deviceTwin.on('properties.desired', (delta) => {
    console.log('Received an update for device with value: ' + JSON.stringify(delta));
    Object.entries(delta).forEach(([key, values]) => {
      const version = delta.$version;
      if (!!(componentNames) && componentNames.includes(key)) { // then it is a component we are expecting
        const componentName = key;
        const patchForComponents = { [componentName]: {} };
        Object.entries(values).forEach(([propertyName, propertyValue]) => {
          if (propertyName !== '__t' && propertyName !== '$version') {
            console.log('Will update property: ' + propertyName + ' to value: ' + propertyValue + ' of component: ' + componentName);
            const propertyContent = { value: propertyValue };
            propertyContent.ac = 200;
            propertyContent.ad = 'Successfully executed patch';
            propertyContent.av = version;
            patchForComponents[componentName][propertyName] = propertyContent;
          }
        });
        updateComponentReportedProperties(deviceTwin, patchForComponents, componentName);
      }
      else if  (key !== '$version') { // individual property for root
        const patchForRoot = { };
        console.log('Will update property: ' + key + ' to value: ' + values + ' for root');
        const propertyContent = { value: values };
        propertyContent.ac = 200;
        propertyContent.ad = 'Successfully executed patch';
        propertyContent.av = version;
        patchForRoot[key] = propertyContent;
        updateComponentReportedProperties(deviceTwin, patchForRoot, null);
      }
    });
  });
};
```

`main`Yöntemi, IoT Central komutları işlemek için aşağıdaki yöntemleri kullanır:

```javascript
const commandHandler = async (request, response) => {
  helperLogCommandRequest(request);
  switch (request.methodName) {
  case commandNameGetMaxMinReport1: {
    await sendCommandResponse(request, response, 200, thermostat1.getMaxMinReportObject());
    break;
  }
  case commandNameGetMaxMinReport2: {
    await sendCommandResponse(request, response, 200, thermostat2.getMaxMinReportObject());
    break;
  }
  case commandNameReboot: {
    await sendCommandResponse(request, response, 200, 'reboot response');
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
    console.log('Response to method: ' + request.methodName + ' sent successfully.' );
  } catch (err) {
    console.error('An error ocurred when sending a method response:\n' + err.toString());
  }
};
```

## <a name="get-connection-information"></a>Bağlantı bilgilerini alma

[!INCLUDE [iot-central-connection-configuration](iot-central-connection-configuration.md)]

## <a name="run-the-code"></a>Kodu çalıştırma

Örnek uygulamayı çalıştırmak için bir komut satırı ortamı açın ve *pnpTemperatureController.js* örnek dosyasını içeren *Azure-IoT-SDK-node/Device/Samples/PNP* klasörüne gidin.

[!INCLUDE [iot-central-connection-environment](iot-central-connection-environment.md)]

Gerekli paketleri yükler:

```cmd/sh
npm install
```

Örneği çalıştırın:

```cmd/sh
node pnpTemperatureController.js
```

Aşağıdaki çıktı, IoT Central kaydetme ve bağlanma için cihazı gösterir. Örnek daha sonra `maxTempSinceLastReboot` özelliği Telemetriyi göndermeye başlamadan önce iki termostat bileşeninden gönderir:

```output
registration succeeded
assigned hub=iotc-....azure-devices.net
deviceId=sample-device-01
payload=undefined
Connecting using connection string: HostName=iotc-....azure-devices.net;DeviceId=sample-device-01;SharedAccessKey=qdv...IpAo=
Enabling the commands on the client
Please enter q or Q to exit sample.
The following properties will be updated for root interface.
{ serialNumber: 'alwinexlepaho8329' }
The following properties will be updated for component: thermostat1
{ thermostat1: { maxTempSinceLastReboot: 1.5902294191855972, __t: 'c' } }
The following properties will be updated for component: thermostat2
{ thermostat2: { maxTempSinceLastReboot: 16.181771928614545, __t: 'c' } }
The following properties will be updated for component: deviceInformation
{ deviceInformation:
   { manufacturer: 'Contoso Device Corporation',
     model: 'Contoso 47-turbo',
     swVersion: '10.89',
     osName: 'Contoso_OS',
     processorArchitecture: 'Contoso_x86',
     processorManufacturer: 'Contoso Industries',
     totalStorage: 65000,
     totalMemory: 640,
     __t: 'c' } }
executed sample
Received an update for device with value: {"$version":1}
Properties have been reported for component: thermostat1
Properties have been reported for component: thermostat2
Properties have been reported for component: deviceInformation
Properties have been reported for root interface.
Sending telemetry message 0 from component: thermostat1 
Sending telemetry message 0 from component: thermostat2 
Sending telemetry message 0 from root interface
```

[!INCLUDE [iot-central-monitor-thermostat](iot-central-monitor-thermostat.md)]

Aygıtın komutlara ve özellik güncelleştirmelerine nasıl yanıt verdiğini görebilirsiniz. `getMaxMinReport`Komut `thermostat2` bileşende, `reboot` komut varsayılan bileşendir. `targetTemperature`' Thermostat2 ' bileşeni için yazılabilir özellik ayarlandı:

```output
Received command request for command name: thermostat2*getMaxMinReport
The command request payload is:
2021-03-26T06:00:00.000Z
Response to method: thermostat2*getMaxMinReport sent successfully.

...

Received command request for command name: reboot
The command request payload is:
10
Response to method: reboot sent successfully.

...

Received an update for device with value: {"thermostat2":{"targetTemperature":76,"__t":"c"},"$version":2}
Will update property: targetTemperature to value: 76 of component: thermostat2
Properties have been reported for component: thermostat2
```
