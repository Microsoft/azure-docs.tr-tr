---
title: Uzaktan Izleme çözümünde sanal cihaz-Azure | Microsoft Docs
description: Bu makalede, uzaktan izleme çözümünde sanal bir cihazın davranışını tanımlamak için JavaScript 'in nasıl kullanılacağı açıklanır.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/29/2018
ms.topic: conceptual
ms.openlocfilehash: c39ca0a018bd22844cf7e5350e6d3586319aac16
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73890856"
---
# <a name="implement-the-device-model-behavior"></a>Cihaz modeli davranışını uygulama

Makale, sanal cihaz modelini tanımlayan şemayı açıklanan [cihaz modeli şemasını anlayın](iot-accelerators-remote-monitoring-device-schema.md) . Bu makale, sanal bir cihazın davranışını uygulayan iki tür JavaScript dosyasına başvurmaktadır:

- **Durum** Cihazın iç durumunu güncelleştirmek için sabit aralıklarda çalışan JavaScript dosyaları.
- **Yöntemi** Çözüm cihazda bir yöntemi çağırdığında çalışan JavaScript dosyaları.

> [!NOTE]
> Cihaz modeli davranışları yalnızca cihaz benzetimi hizmetinde barındırılan sanal cihazlar içindir. Gerçek bir cihaz oluşturmak istiyorsanız, bkz. [cihazınızı uzaktan izleme çözüm hızlandırıcısına bağlama](iot-accelerators-connecting-devices.md).

Bu makalede şunları öğreneceksiniz:

>[!div class="checklist"]
> * Sanal cihazın durumunu denetleme
> * Sanal cihazın uzaktan Izleme çözümünün yöntem çağrısına nasıl yanıt vereceğini tanımlayın
> * Betiklerinizde hata ayıklama

## <a name="state-behavior"></a>Durum davranışı

Cihaz modeli şemasının [Benzetim](../../articles/iot-accelerators/iot-accelerators-remote-monitoring-device-schema.md#simulation) bölümü, sanal cihazın iç durumunu tanımlar:

- `InitialState`, cihaz durumu nesnesinin tüm özellikleri için ilk değerleri tanımlar.
- `Script`, cihaz durumunu güncelleştirmek için bir zamanlamaya göre çalışan bir JavaScript dosyasını tanımlar.

Aşağıdaki örnekte, sanal bir chilcihaz için cihaz durumu nesnesinin tanımı gösterilmektedir:

```json
"Simulation": {
  "InitialState": {
    "online": true,
    "temperature": 75.0,
    "temperature_unit": "F",
    "humidity": 70.0,
    "humidity_unit": "%",
    "pressure": 150.0,
    "pressure_unit": "psig",
    "simulation_state": "normal_pressure"
  },
  "Interval": "00:00:05",
  "Scripts": {
    "Type": "javascript",
    "Path": "chiller-01-state.js"
  }
}
```

`InitialState` bölümünde tanımlandığı gibi, sanal cihazın durumu Simülasyon hizmeti tarafından bellekte tutulur. Durum bilgileri, **Chiller-01-State. js**' de tanımlanan `main` işlevine girdi olarak geçirilir. Bu örnekte, Simülasyon hizmeti her beş saniyede bir **Chiller-01-State. js** dosyasını çalıştırır. Betik, sanal cihazın durumunu değiştirebilir.

Aşağıda, tipik bir `main` işlevinin ana hattı gösterilmektedir:

```javascript
function main(context, previousState, previousProperties) {

  // Use the previous device state to
  // generate the new device state
  // returned by the function.

  return state;
}
```

`context` parametresi aşağıdaki özelliklere sahiptir:

- biçim `yyyy-MM-dd'T'HH:mm:sszzz` bir dize olarak `currentTime`
- `deviceId`, örneğin `Simulated.Chiller.123`
- `deviceModel`, örneğin `Chiller`

`state` parametresi cihaz benzetimi hizmeti tarafından sürdürülen cihaz durumunu içerir. Bu değer, önceki `main`çağrısının döndürdüğü `state` nesnesidir.

Aşağıdaki örnek, Simülasyon hizmeti tarafından tutulan cihaz durumunu işlemek için `main` yönteminin tipik bir uygulamasını gösterir:

```javascript
// Default state
var state = {
  online: true,
  temperature: 75.0,
  temperature_unit: "F",
  humidity: 70.0,
  humidity_unit: "%",
  pressure: 150.0,
  pressure_unit: "psig",
  simulation_state: "normal_pressure"
};

function restoreState(previousState) {
  // If the previous state is null, force a default state
  if (previousState !== undefined && previousState !== null) {
      state = previousState;
  } else {
      log("Using default state");
  }
}

function main(context, previousState, previousProperties) {

  restoreState(previousState);

  // Update state

  return state;
}
```

Aşağıdaki örnek, `main` yönteminin zaman içinde farklılık gösteren telemetri değerlerinin benzetimini nasıl benzebileceğini gösterir:

```javascript
/**
 * Simple formula generating a random value
 * around the average and between min and max
 */
function vary(avg, percentage, min, max) {
  var value = avg * (1 + ((percentage / 100) * (2 * Math.random() - 1)));
  value = Math.max(value, min);
  value = Math.min(value, max);
  return value;
}


function main(context, previousState, previousProperties) {

    restoreState(previousState);

    // 75F +/- 5%,  Min 25F, Max 100F
    state.temperature = vary(75, 5, 25, 100);

    // 70% +/- 5%,  Min 2%, Max 99%
    state.humidity = vary(70, 5, 2, 99);

    log("Simulation state: " + state.simulation_state);
    if (state.simulation_state === "high_pressure") {
        // 250 psig +/- 25%,  Min 50 psig, Max 300 psig
        state.pressure = vary(250, 25, 50, 300);
    } else {
        // 150 psig +/- 10%,  Min 50 psig, Max 300 psig
        state.pressure = vary(150, 10, 50, 300);
    }

    return state;
}
```

GitHub üzerinde [Chiller-01-State. js](https://github.com/Azure/device-simulation-dotnet/blob/master/Services/data/devicemodels/scripts/chiller-01-state.js) ' nin tamamını görüntüleyebilirsiniz.

## <a name="method-behavior"></a>Yöntem davranışı

Cihaz modeli şemasının [Cloudtodevicemethods](../../articles/iot-accelerators/iot-accelerators-remote-monitoring-device-schema.md#cloudtodevicemethods) bölümü, sanal bir cihazın yanıt verdiği yöntemleri tanımlar.

Aşağıdaki örnek, sanal bir chilcihaz tarafından desteklenen yöntemlerin listesini gösterir:

```json
"CloudToDeviceMethods": {
  "Reboot": {
    "Type": "javascript",
    "Path": "Reboot-method.js"
  },
  "FirmwareUpdate": {
    "Type": "javascript",
    "Path": "FirmwareUpdate-method.js"
  },
  "EmergencyValveRelease": {
    "Type": "javascript",
    "Path": "EmergencyValveRelease-method.js"
  },
  "IncreasePressure": {
    "Type": "javascript",
    "Path": "IncreasePressure-method.js"
  }
}
```

Her yöntemin, yönteminin davranışını uygulayan ilişkili bir JavaScript dosyası vardır.

Şema `InitialState` bölümünde tanımlandığı gibi, sanal cihazın durumu Simülasyon hizmeti tarafından bellekte tutulur. Durum bilgileri, yöntemi çağrıldığında JavaScript dosyasında tanımlanan `main` işlevine girdi olarak geçirilir. Betik, sanal cihazın durumunu değiştirebilir.

Aşağıda, tipik bir `main` işlevinin ana hattı gösterilmektedir:

```javascript
function main(context, previousState, previousProperties) {

}
```

`context` parametresi aşağıdaki özelliklere sahiptir:

- biçim `yyyy-MM-dd'T'HH:mm:sszzz` bir dize olarak `currentTime`
- `deviceId`, örneğin `Simulated.Chiller.123`
- `deviceModel`, örneğin `Chiller`

`state` parametresi cihaz benzetimi hizmeti tarafından sürdürülen cihaz durumunu içerir.

`properties` parametresi, IoT Hub cihaz ikizi bildirilen özellikler olarak yazılan cihazın özelliklerini içerir.

Yöntemi davranışını uygulamaya yardımcı olmak için kullanabileceğiniz üç genel işlev vardır:

- Simülasyon hizmeti tarafından tutulan durumu güncelleştirmek için `updateState`.
- tek bir cihaz özelliğini güncelleştirmek `updateProperty`.
- uzun süre çalışan bir görevin benzetimini yapmak için yürütmeyi duraklatmaya `sleep`.

Aşağıdaki örnek, **IncreasePressure-method. js** betiğinin benzetimli chilcihazlar tarafından kullanılan kısaltılmış bir sürümünü göstermektedir:

```javascript
function main(context, previousState, previousProperties) {

    log("Starting 'Increase Pressure' method simulation (5 seconds)");

    // Pause the simulation and change the simulation mode so that the
    // temperature will fluctuate at ~250 when it resumes
    var state = {
        simulation_state: "high_pressure",
        CalculateRandomizedTelemetry: false
    };
    updateState(state);

    // Increase
    state.pressure = 210;
    updateState(state);
    log("Pressure increased to " + state.pressure);
    sleep(1000);

    // Increase
    state.pressure = 250;
    updateState(state);
    log("Pressure increased to " + state.pressure);
    sleep(1000);

    // Resume the simulation
    state.CalculateRandomizedTelemetry = true;
    updateState(state);

    log("'Increase Pressure' method simulation completed");
}
```

## <a name="debugging-script-files"></a>Betik dosyalarında hata ayıklama

Durum ve Yöntem betikleri çalıştırmak için cihaz benzetimi hizmeti tarafından kullanılan JavaScript Yorumlayıcısına bir hata ayıklayıcı eklemek mümkün değildir. Ancak, bilgileri hizmet günlüğünde günlüğe kaydedebilirsiniz. Yerleşik `log()` işlevi, işlev yürütmeyi izlemek ve hata ayıklamak için bilgileri kaydetmenizi sağlar.

Bir sözdizimi hatası varsa yorumlayıcı başarısız olur ve hizmet günlüğüne bir `Jint.Runtime.JavaScriptException` girişi yazar.

GitHub 'da [hizmeti yerel olarak çalıştırma](https://github.com/Azure/device-simulation-dotnet#running-the-service-locally-eg-for-development-tasks) makalesi, cihaz benzetimi hizmetini yerel olarak nasıl çalıştıracağınızı gösterir. Hizmeti yerel olarak çalıştırmak, sanal cihazlarınızda buluta dağıtmadan önce sanal cihazlarınızın hatalarını ayıklamayı kolaylaştırır.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, kendi özel sanal cihaz modelinizin davranışının nasıl tanımlanacağı açıklanmaktadır. Bu makalede nasıl yapılacağı açıklanır:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Sanal cihazın durumunu denetleme
> * Sanal cihazın uzaktan Izleme çözümünün yöntem çağrısına nasıl yanıt vereceğini tanımlayın
> * Betiklerinizde hata ayıklama

Sanal bir cihazın davranışını nasıl belirttireceğinizi öğrendiğinize göre, önerilen sonraki adım, [sanal cihaz oluşturmayı](iot-accelerators-remote-monitoring-create-simulated-device.md)öğrenmektir.

Uzaktan Izleme çözümü hakkında daha fazla geliştirici bilgisi için bkz.:

* [Geliştirici Başvuru Kılavuzu](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Geliştirici Sorun Giderme Kılavuzu](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

<!-- Next tutorials in the sequence -->
