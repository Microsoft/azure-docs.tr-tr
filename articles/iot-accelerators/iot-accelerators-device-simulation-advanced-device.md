---
title: Gelişmiş bir sanal cihaz modeli oluşturma-Azure | Microsoft Docs
description: Bu nasıl yapılır kılavuzunda, cihaz benzetimi çözüm hızlandırıcısı ile kullanmak üzere gelişmiş bir cihaz modeli oluşturmayı öğreneceksiniz.
author: troyhopwood
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: troyhop
ms.custom:
- mvc
- amqp
- mqtt
- devx-track-js
ms.openlocfilehash: dc2eac4e784d4224581078348f12b231befe1f35
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714119"
---
# <a name="create-an-advanced-device-model"></a>Gelişmiş bir cihaz modeli oluşturma

Bu nasıl yapılır kılavuzunda, özel bir cihaz modelini tanımlayan JSON ve JavaScript dosyaları açıklanmaktadır. Makale, bazı örnek cihaz modeli tanım dosyalarını içerir ve bunları cihaz benzetim örneğinize nasıl yükleyeceksiniz. Testiniz için daha gerçekçi cihaz davranışları benzetimi yapmak üzere gelişmiş cihaz modelleri oluşturabilirsiniz.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Bu nasıl yapılır kılavuzundaki adımları izlemek için, Azure aboneliğinizde bir cihaz benzetiminin dağıtılmış örneğine ihtiyacınız vardır.

Henüz cihaz benzetimi dağıtmadıysanız, GitHub 'da [cihaz benzetimi dağıtımı](https://github.com/Azure/azure-iot-pcs-device-simulation/blob/master/README.md) ' na bakın.

### <a name="open-device-simulation"></a>Cihaz Benzetimini açma

Henüz cihaz benzetimi dağıtmadıysanız, GitHub 'da [cihaz benzetimi dağıtımı](https://github.com/Azure/azure-iot-pcs-device-simulation/blob/master/README.md) ' na bakın.

## <a name="device-models"></a>Cihaz modelleri

Her sanal cihaz, benzetim davranışını tanımlayan belirli bir cihaz modeline aittir. Bu davranış ne sıklıkla telemetri gönderileceğini, ne tür iletiler gönderileceğini ve desteklenen yöntemleri içerir.

Bir JSON cihaz tanımı dosyası ve bir JavaScript dosyası kümesi kullanarak bir cihaz modeli tanımlarsınız. Bu JavaScript dosyaları, rastgele telemetri ve Yöntem mantığı gibi benzetim davranışını tanımlar.

Tipik bir cihaz modeli şunları içerir:

* Her cihaz modeli için bir JSON dosyası (örneğin, elevator.js).
* Her cihaz modeli için bir JavaScript davranış betik dosyası (örneğin, elevator-state.js)
* Her cihaz yöntemi için bir JavaScript Yöntem betik dosyası (örneğin, elevator-go-down.js)

> [!NOTE]
> Tüm cihaz modelleri Yöntemler tanımlamıyor. Bu nedenle bir cihaz modelinde Yöntem betikleri olabilir veya bulunmayabilir. Ancak, tüm cihaz modellerinin bir davranış betiği olmalıdır.

## <a name="device-definition-file"></a>Cihaz tanımı dosyası

Her Cihaz tanımı dosyası, aşağıdaki bilgiler dahil olmak üzere sanal cihaz modelinin ayrıntılarını içerir:

* Cihaz modeli adı: dize.
* Protokol: AMQP | MQTT | HTTP.
* İlk cihaz durumu.
* Cihaz durumunu yenileme sıklığı.
* Cihaz durumunu yenilemek için kullanılacak JavaScript dosyası.
* Her biri belirli bir sıklık ile gönderilen telemetri iletilerinin bir listesi.
* Arka uç uygulaması tarafından alınan telemetrinin ayrıştırılmasında kullanılan telemetri iletilerinin şeması.
* Desteklenen yöntemlerin bir listesi ve her yöntemin benzetimini yapmak için kullanılacak JavaScript dosyası.

### <a name="file-schema"></a>Dosya şeması

Şema sürümü her zaman "1.0.0" ve bu dosyanın biçimine özgüdür:

```json
"SchemaVersion": "1.0.0"
```

### <a name="device-model-description"></a>Cihaz modeli açıklaması

Aşağıdaki özellikler cihaz modelini anlatmaktadır. Her tür benzersiz bir tanımlayıcıya, anlam sürümüne, bir ada ve açıklamaya sahiptir:

```json
"Id": "chiller-01",
"Version": "0.0.1",
"Name": "Chiller",
"Description": "Chiller with external temperature and humidity sensors."
```

### <a name="iot-protocol"></a>IoT Protokolü

IoT cihazları, farklı protokoller kullanarak bağlanabilir. Benzetim, **AMQP**, **MQTT** veya **http** kullanarak şunları yapmanızı sağlar:

```json
"Protocol": "AMQP"
```

### <a name="simulated-device-state"></a>Sanal cihaz durumu

Her bir sanal cihazın, tanımlanması gereken bir iç durumu vardır. Durum ayrıca telemetride raporlanabilmesi gereken özellikleri tanımlar. Örneğin, bir Chil, şöyle bir başlangıç durumuna sahip olabilir:

```json
"InitialState": {
    "temperature": 50,
    "humidity": 40
},
```

Birkaç sensörle taşınan bir cihazın daha fazla özelliği olabilir, örneğin:

```json
"InitialState": {
    "latitude": 47.445301,
    "longitude": -122.296307,
    "speed": 30.0,
    "speed_unit": "mph",
    "cargotemperature": 38.0,
    "cargotemperature_unit": "F"
}
```

Cihaz durumu Simülasyon hizmeti tarafından bellekte tutulur ve JavaScript işlevine giriş olarak sağlanır. JavaScript işlevi şunları karar verebilir:

* Durumu yoksaymak ve bazı rastgele veriler oluşturmak için.
* Belirli bir senaryo için cihaz durumunu gerçekçi bir şekilde güncelleştirmek için.

Durumu oluşturan işlevi de giriş olarak alır:

* Cihaz KIMLIĞI.
* Cihaz modeli.
* Geçerli saat. Bu değer, cihaza ve zamana göre farklı veriler oluşturmayı mümkün kılar.

### <a name="generating-telemetry-messages"></a>Telemetri iletileri oluşturma

Simülasyon hizmeti her cihaz için birkaç telemetri türü gönderebilir. Genellikle telemetri cihaz durumundan alınan verileri içerir. Örneğin, sanal bir oda her 10 saniyede sıcaklık ve nem hakkında bilgi gönderebilir. Aşağıdaki kod parçacığındaki yer tutucuları, otomatik olarak cihaz durumundaki değerlerle değiştirilmiştir:

```json
"Telemetry": [
    {
        "Interval": "00:00:10",
        "MessageTemplate":
            "{\"temperature\":${temperature},\"temperature_unit\":\"${temperature_unit}\",\"humidity\":\"${humidity}\"}",
        "MessageSchema": {
            "Name": "RoomComfort;v1",
            "Format": "JSON",
            "Fields": {
                "temperature": "double",
                "temperature_unit": "text",
                "humidity": "integer"
            }
        }
    }
],
```

Yer tutucular, **adı** JavaScript **ana** işlevi tarafından döndürülen cihaz durumu nesnesinden bir anahtar olduğunda, **$ {Name}** özel sözdizimini kullanır. Dizeler tırnak içine alınmalıdır, sayılar memelidir.

#### <a name="message-schema"></a>İleti şeması

Her ileti türünün iyi tanımlanmış bir şeması olmalıdır. İleti şeması Ayrıca IoT Hub olarak yayımlanır, böylece arka uç uygulamaları gelen telemetrileri yorumlamak için bilgileri yeniden kullanabilir.

Şema, çeşitli sistemler ve hizmetler arasında kolayca ayrıştırma, dönüştürme ve analizler sağlayan JSON biçimini destekler.

Şemada listelenen alanlar aşağıdaki türlerde olabilir:

* Nesne-JSON kullanılarak serileştirilmiş
* Base64 kullanılarak ikili seri hale getirilmiş
* Metin
* Boole
* Tamsayı
* Çift
* DateTime

### <a name="supported-methods"></a>Desteklenen Yöntemler

Sanal cihazlar ayrıca yöntem çağrılarına yanıt verebilir, bu durumda bazı mantık yürütür ve bir yanıt sağlar. Simülasyonuna benzer şekilde, yöntem mantığı bir JavaScript dosyasında depolanır ve cihaz durumuyla etkileşime geçebilir. Örnek:

```json
"CloudToDeviceMethods": {
    "Start": {
        "Type": "JavaScript",
        "Path": "release-pressure.js"
    }
}
```

## <a name="create-a-device-definition-file"></a>Cihaz tanımı dosyası oluşturma

Bu nasıl yapılır kılavuzunda, bir drone için cihaz modeli oluşturma hakkında bilgi alabilirsiniz. Drone, konum ve yüksekliği değiştiren bir ilk koordinat kümesini rastgele bir şekilde hareket edecektir.

Aşağıdaki JSON 'ı bir metin düzenleyicisine kopyalayın ve **drone.js** olarak kaydedin.

### <a name="device-definition-json-example"></a>Cihaz tanımı JSON örneği

```json
{
  "SchemaVersion": "1.0.0",
  "Id": "drone",
  "Version": "0.0.1",
  "Name": "Drone",
  "Description": "Simple drone.",
  "Protocol": "AMQP",
  "Simulation": {
    "InitialState": {
      "velocity": 0.0,
      "velocity_unit": "mm/sec",
      "acceleration": 0.0,
      "acceleration_unit": "mm/sec^2",
      "latitude": 47.476075,
      "longitude": -122.192026,
      "altitude": 0.0
    },
    "Interval": "00:00:05",
    "Scripts": [{
      "Type": "JavaScript",
      "Path": "drone-state.js"
    }]
  },
  "Properties": {
    "Type": "Drone",
    "Firmware": "1.0",
    "Model": "P-96"
  },
  "Tags": {
    "Owner": "Contoso"
  },
  "Telemetry": [{
      "Interval": "00:00:05",
      "MessageTemplate": "{\"velocity\":\"${velocity}\",\"acceleration\":\"${acceleration}\",\"position\":\"${latitude}|${longitude}|${altitude}\"}",
      "MessageSchema": {
        "Name": "drone-event-sensor;v1",
        "Format": "JSON",
        "Fields": {
          "velocity": "double",
          "velocity_unit": "text",
          "acceleration": "double",
          "acceleration_unit": "text",
          "latitude": "double",
          "longitude": "double",
          "altitude": "double"
        }
      }
    }
  ],
    "CloudToDeviceMethods": {
        "RecallDrone": {
            "Type": "JavaScript",
            "Path": "droneRecall-method.js"
        }
    }
}
```

## <a name="behavior-script-files"></a>Davranış betiği dosyaları

Davranış betik dosyasındaki kod DRA 'yi taşıtir. Betik, cihazın bellek durumunu düzenleyerek drone 'nin yükseltmesini ve konumunu değiştirir.

JavaScript dosyalarının iki parametreyi kabul eden bir **ana** işlevi olması gerekir:

* Üç özellik içeren bir **bağlam** nesnesi:
    * **geçerli saat** , **yyyy-aa-gg't'hh: mm: sszzz** biçiminde bir dize olarak.
    * **DeviceID**. Örneğin, **benzetimli. Asansör. 123**.
    * **Devicemodel**. Örneğin, **Asansör**.
* Önceki çağrıda işlevin döndürdüğü değer olan bir **durum** nesnesi. Bu cihaz durumu Simülasyon hizmeti tarafından tutulur ve telemetri iletileri oluşturmak için kullanılır.

**Main** işlevi yeni cihaz durumunu döndürür. Örnek:

```JavaScript
function main(context, state) {

    // Use context if the simulation depends on
    // time or device details.
    // Execute some logic, updating 'state'

    return state;
}
```

## <a name="create-a-behavior-script-file"></a>Davranış betik dosyası oluşturma

Aşağıdaki JavaScript 'ı bir metin düzenleyicisine kopyalayın ve **drone-state.js** olarak kaydedin.

### <a name="device-model-javascript-simulation-example"></a>Cihaz modeli JavaScript benzetim örneği

```JavaScript
"use strict";

// Position control
const DefaultLatitude = 47.476075;
const DefaultLongitude = -122.192026;
const DistanceVariation = 10;

// Altitude control
const DefaultAltitude = 0.0;
const AverageAltitude = 499.99;
const AltitudeVariation = 5;

// Velocity control
const AverageVelocity = 60.00;
const MinVelocity = 20.00;
const MaxVelocity = 120.00;
const VelocityVariation = 5;

// Acceleration control
const AverageAcceleration = 2.50;
const MinAcceleration = 0.01;
const MaxAcceleration = 9.99;
const AccelerationVariation = 1;

// Display control for position and other attributes
const GeoSpatialPrecision = 6;
const DecimalPrecision = 2;

// Default state
var state = {
    velocity: 0.0,
    velocity_unit: "mm/sec",
    acceleration: 0.0,
    acceleration_unit: "mm/sec^2",
    latitude: DefaultLatitude,
    longitude: DefaultLongitude,
    altitude: DefaultAltitude
};

// Default device properties
var properties = {};

/**
 * Restore the global state using data from the previous iteration.
 *
 * @param previousState device state from the previous iteration
 * @param previousProperties device properties from the previous iteration
 */
function restoreSimulation(previousState, previousProperties) {
    // If the previous state is null, force a default state
    if (previousState) {
        state = previousState;
    } else {
        log("Using default state");
    }

    if (previousProperties) {
        properties = previousProperties;
    } else {
        log("Using default properties");
    }
}

/**
 * Simple formula generating a random value around the average
 * in between min and max
 */
function vary(avg, percentage, min, max) {
    var value = avg * (1 + ((percentage / 100) * (2 * Math.random() - 1)));
    value = Math.max(value, min);
    value = Math.min(value, max);
    return value;
}

/**
 * Entry point function called by the simulation engine.
 * Returns updated simulation state.
 * Device property updates must call updateProperties() to persist.
 *
 * @param context             The context contains current time, device model and id
 * @param previousState       The device state since the last iteration
 * @param previousProperties  The device properties since the last iteration
 */
/*jslint unparam: true*/
function main(context, previousState, previousProperties) {

    // Restore the global state before generating the new telemetry, so that
    // the telemetry can apply changes using the previous function state.
    restoreSimulation(previousState, previousProperties);

    state.acceleration = vary(AverageAcceleration, AccelerationVariation, MinAcceleration, MaxAcceleration).toFixed(DecimalPrecision);
    state.velocity = vary(AverageVelocity, VelocityVariation, MinVelocity, MaxVelocity).toFixed(DecimalPrecision);

    // Use the last coordinates to calculate the next set with a given variation
    var coords = varylocation(Number(state.latitude), Number(state.longitude), DistanceVariation);
    state.latitude = Number(coords.latitude).toFixed(GeoSpatialPrecision);
    state.longitude = Number(coords.longitude).toFixed(GeoSpatialPrecision);

    // Fluctuate altitude between given variation constant by more or less
    state.altitude = vary(AverageAltitude, AltitudeVariation, AverageAltitude - AltitudeVariation, AverageAltitude + AltitudeVariation).toFixed(DecimalPrecision);

    return state;
}

/**
 * Generate a random geolocation at some distance (in miles)
 * from a given location
 */
function varylocation(latitude, longitude, distance) {
    // Convert to meters, use Earth radius, convert to radians
    var radians = (distance * 1609.344 / 6378137) * (180 / Math.PI);
    return {
        latitude: latitude + radians,
        longitude: longitude + radians / Math.cos(latitude * Math.PI / 180)
    };
}
```

## <a name="create-a-method-script-file"></a>Yöntem betik dosyası oluşturma

Yöntem betikleri davranış betiklerine benzer. Belirli bir buluta cihaz yöntemi çağrıldığında cihaz davranışını tanımlar.

Drone geri çekme betiği, DRA 'nın, ana giriş döndüren kurutanın benzetimini yapmak için, kurutun koordinatlarını sabit bir noktaya ayarlar.

Aşağıdaki JavaScript 'ı bir metin düzenleyicisine kopyalayın ve **droneRecall-method.js** olarak kaydedin.

### <a name="device-model-javascript-simulation-example"></a>Cihaz modeli JavaScript benzetim örneği

```JavaScript
"use strict";

// Default state
var state = {
    velocity: 0.0,
    velocity_unit: "mm/sec",
    acceleration: 0.0,
    acceleration_unit: "mm/sec^2",
    latitude: 0.0,
    longitude: 0.0,
    altitude: 0.0
};

// Default device properties
var properties = {};

/**
 * Restore the global state using data from the previous iteration.
 *
 * @param previousState device state from the previous iteration
 * @param previousProperties device properties from the previous iteration
 */
function restoreSimulation(previousState, previousProperties) {
    // If the previous state is null, force a default state
    if (previousState) {
        state = previousState;
    } else {
        log("Using default state");
    }

    if (previousProperties) {
        properties = previousProperties;
    } else {
        log("Using default properties");
    }
}

/**
 * Entry point function called by the simulation engine.
 *
 * @param context        The context contains current time, device model and id, not used
 * @param previousState  The device state since the last iteration, not used
 * @param previousProperties  The device properties since the last iteration
 */
/*jslint unparam: true*/
function main(context, previousState, previousProperties) {

    // Restore the global device properties and the global state before
    // generating the new telemetry, so that the telemetry can apply changes
    // using the previous function state.
    restoreSimulation(previousState, previousProperties);

    //simulate the behavior of a drone when recalled
  state.latitude = 47.476075;
  state.longitude = -122.192026;
  return state;
}
```

## <a name="debugging-script-files"></a>Betik dosyalarında hata ayıklama

Çalışan bir davranış dosyasına bir hata ayıklayıcı iliştiremiyoruz, ancak **log** işlevini kullanarak hizmet günlüğüne bilgi yazmak mümkündür. Sözdizimi hataları için yorumlayıcı başarısız olur ve özel durumla ilgili bilgileri günlüğe yazar.

Günlüğe kaydetme örneği:

```JavaScript
function main(context, state) {

    log("This message will appear in the service logs.");

    log(context.deviceId);

    if (typeof(state) !== "undefined" && state !== null) {
        log("Previous value: " + state.temperature);
    }

    // ...

    return updateState;
}
```

## <a name="deploy-an-advanced-device-model"></a>Gelişmiş cihaz modeli dağıtma

Gelişmiş cihaz modelinizi dağıtmak için, cihaz benzetim örneğinizin dosyalarını karşıya yüklersiniz:

Menü çubuğunda **Cihaz modelleri**'ni seçin. Cihaz **modelleri** sayfası, bu cihaz benzetimi örneğinde bulunan cihaz modellerini listeler:

![Cihaz modelleri](media/iot-accelerators-device-simulation-advanced-device/devicemodelnav.png)

Sayfanın sağ üst köşesindeki **+ Cihaz Modeli Ekle**'ye tıklayın:

![Cihaz modeli ekleme](media/iot-accelerators-device-simulation-advanced-device/devicemodels.png)

Gelişmiş **' e** tıklayarak gelişmiş cihaz modeli sekmesini açın:

![Gelişmiş sekmesi](media/iot-accelerators-device-simulation-advanced-device/advancedtab.png)

**Araştır** ' a tıklayın ve oluşturduğunuz JSON ve JavaScript dosyalarını seçin. Üç dosyayı da seçtiğinizden emin olun. Bir dosya eksikse, doğrulama başarısız olur:

![Dosyalara gözatamıyorum](media/iot-accelerators-device-simulation-advanced-device/browse.png)

Dosyalarınız doğrulamayı geçirirseniz, **Kaydet** ' e tıklayın ve cihaz modeliniz bir simülasyonu içinde kullanılmak üzere hazırlanın. Aksi takdirde, hataları giderin ve dosyaları yeniden karşıya yükleyin:

![Kaydet](media/iot-accelerators-device-simulation-advanced-device/validated.png)

## <a name="next-steps"></a>Sonraki adımlar

Bu nasıl yapılır kılavuzunda, cihaz benzetiminde kullanılan cihaz modeli dosyaları ve gelişmiş cihaz modeli oluşturma hakkında bilgi edindiniz. Daha sonra, [cihaz benzetimi çözüm hızlandırıcısında gönderilen Telemetriyi görselleştirmek için Time Series Insights nasıl kullanacağınızı](./iot-accelerators-device-simulation-time-series-insights.md)araştırmak isteyebilirsiniz.