---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/19/2020
ms.openlocfilehash: 38a29aa765bde4adb390423a9d180132b57d4fee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104582807"
---
## <a name="model-id-announcement"></a>Model KIMLIĞI duyurusu

Model KIMLIĞINI duyurmak için cihazın bağlantı bilgilerine eklemesi gerekir:

```csharp
DeviceClient.CreateFromConnectionString(
  connectionString,
  TransportType.Mqtt,
  new ClientOptions() { ModelId = modelId })
```

Yeni `ClientOptions` aşırı yükleme, `DeviceClient` bir bağlantıyı başlatmak için kullanılan tüm yöntemlerde kullanılabilir.

> [!TIP]
> Modüller ve IoT Edge için yerine kullanın `ModuleClient` `DeviceClient` .

> [!TIP]
> Bu, bir cihazın model KIMLIĞINI ayarlayabir zaman, cihaz bağlandıktan sonra güncelleştirilemeyebilir.

## <a name="dps-payload"></a>DPS yükü

[Cihaz sağlama hizmeti 'ni (DPS)](../articles/iot-dps/about-iot-dps.md) kullanan cihazlar, `modelId` aşağıdaki JSON yükü kullanılarak sağlama işlemi sırasında kullanılacak öğesini içerebilir.

```json
{
    "modelId" : "dtmi:com:example:Thermostat;1"
}
```

## <a name="implement-telemetry-properties-and-commands"></a>Telemetri, özellik ve komutları uygulama

[Iot Tak ve kullan modellerdeki bileşenleri anlama](../articles/iot-pnp/concepts-modeling-guide.md)bölümünde açıklandığı gibi, cihaz oluşturucuların cihazlarını açıklamak için bileşenleri kullanmak istemeseler de karar vermelidir. Bileşenler kullanılırken, cihazların bu bölümde açıklanan kurallara uymalıdır.

### <a name="telemetry"></a>Telemetri

Varsayılan bir bileşen herhangi bir özel özellik gerektirmez.

İç içe bileşenler kullanılırken, cihazların bileşen adı ile bir ileti özelliği ayarlaması gerekir:

```c#
public async Task SendComponentTelemetryValueAsync(string componentName, string serializedTelemetry)
{
  var message = new Message(Encoding.UTF8.GetBytes(serializedTelemetry));
  message.ComponentName = componentName;
  message.ContentType = "application/json";
  message.ContentEncoding = "utf-8";
  await client.SendEventAsync(message);
}
```

### <a name="read-only-properties"></a>Salt okunur özellikler

Varsayılan bileşenden bir özelliği raporlamak için özel bir yapı gerekmez:

```csharp
TwinCollection reportedProperties = new TwinCollection();
reportedProperties["maxTemperature"] = 38.7;
await client.UpdateReportedPropertiesAsync(reportedProperties);
```

Device ikizi, sonraki bildirilen özelliği ile güncellenir:

```json
{
  "reported": {
      "maxTemperature" : 38.7
  }
}
```

İç içe bileşenler kullanılırken, Özellikler bileşen adı içinde oluşturulmalıdır:

```csharp
TwinCollection reportedProperties = new TwinCollection();
TwinCollection component = new TwinCollection();
component["maxTemperature"] = 38.7;
component["__t"] = "c"; // marker to identify a component
reportedProperties["thermostat1"] = component;
await client.UpdateReportedPropertiesAsync(reportedProperties);
```

Device ikizi, sonraki bildirilen özelliği ile güncellenir:

```json
{
  "reported": {
    "thermostat1" : {  
      "__t" : "c",  
      "maxTemperature" : 38.7
     } 
  }
}
```

### <a name="writable-properties"></a>Yazılabilir Özellikler

Bu özellikler cihaz tarafından ayarlanabilir veya çözüm tarafından güncelleştirilir. Çözüm bir özelliği güncelleştirdi, istemci veya içinde geri çağırma olarak bir bildirim alır `DeviceClient` `ModuleClient` . IoT Tak ve Kullan kurallarını izlemek için cihazın, özelliği başarıyla alınan hizmete bildirmesi gerekir.

#### <a name="report-a-writable-property"></a>Yazılabilir bir özellik bildir

Bir cihaz yazılabilir bir özellik bildirdiğinde, `ack` kurallar içinde tanımlanan değerleri içermelidir.

Yazılabilir bir özelliği varsayılan bileşenden raporlamak için:

```csharp
TwinCollection reportedProperties = new TwinCollection();
TwinCollection ackProps = new TwinCollection();
ackProps["value"] = 23.2;
ackProps["ac"] = 200; // using HTTP status codes
ackProps["av"] = 0; // not readed from a desired property
ackProps["ad"] = "reported default value";
reportedProperties["targetTemperature"] = ackProps;
await client.UpdateReportedPropertiesAsync(reportedProperties);
```

Device ikizi, sonraki bildirilen özelliği ile güncellenir:

```json
{
  "reported": {
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
  }
}
```

İç içe geçmiş bir bileşenden yazılabilir bir özellik raporlamak için, ikizi bir işaret içermelidir:

```csharp
TwinCollection reportedProperties = new TwinCollection();
TwinCollection component = new TwinCollection();
TwinCollection ackProps = new TwinCollection();
component["__t"] = "c"; // marker to identify a component
ackProps["value"] = 23.2;
ackProps["ac"] = 200; // using HTTP status codes
ackProps["av"] = 0; // not read from a desired property
ackProps["ad"] = "reported default value";
component["targetTemperature"] = ackProps;
reportedProperties["thermostat1"] = component;
await client.UpdateReportedPropertiesAsync(reportedProperties);
```

Device ikizi, sonraki bildirilen özelliği ile güncellenir:

```json
{
  "reported": {
    "thermostat1": {
      "__t" : "c",
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
    }
  }
}
```

#### <a name="subscribe-to-desired-property-updates"></a>İstenen özellik güncelleştirmelerine abone ol

Hizmetler, bağlantılı cihazlarda bir bildirim tetikleyen istenen özellikleri güncelleştirebilir. Bu bildirim, güncelleştirmeyi tanımlayan sürüm numarası da dahil olmak üzere güncelleştirilmiş Desired özelliklerini içerir. Cihazların bildirilen özelliklerle aynı iletiyle yanıt vermesi gerekir `ack` .

Varsayılan bir bileşen, tek bir özelliği görür ve `ack` alınan sürüm ile bildirilen bir oluşturur:

```csharp
await client.SetDesiredPropertyUpdateCallbackAsync(async (desired, ctx) => 
{
  JValue targetTempJson = desired["targetTemperature"];
  double targetTemperature = targetTempJson.Value<double>();

  TwinCollection reportedProperties = new TwinCollection();
  TwinCollection ackProps = new TwinCollection();
  ackProps["value"] = targetTemperature;
  ackProps["ac"] = 200;
  ackProps["av"] = desired.Version; 
  ackProps["ad"] = "desired property received";
  reportedProperties["targetTemperature"] = ackProps;

  await client.UpdateReportedPropertiesAsync(reportedProperties);
}, null);
```

Device ikizi, özelliği istenen ve bildirilen bölümlerde gösterir:

```json
{
  "desired" : {
    "targetTemperature": 23.2,
    "$version" : 3
  },
  "reported": {
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
  }
}
```

İç içe yerleştirilmiş bir bileşen, bileşen adı ile Sarmalanan istenen özellikleri alır ve bildirilen özelliği geri bildirmelidir `ack` :

```csharp
await client.SetDesiredPropertyUpdateCallbackAsync(async (desired, ctx) =>
{
  JObject thermostatComponent = desired["thermostat1"];
  JToken targetTempProp = thermostatComponent["targetTemperature"];
  double targetTemperature = targetTempProp.Value<double>();

  TwinCollection reportedProperties = new TwinCollection();
  TwinCollection component = new TwinCollection();
  TwinCollection ackProps = new TwinCollection();
  component["__t"] = "c"; // marker to identify a component
  ackProps["value"] = targetTemperature;
  ackProps["ac"] = 200; // using HTTP status codes
  ackProps["av"] = desired.Version; // not readed from a desired property
  ackProps["ad"] = "desired property received";
  component["targetTemperature"] = ackProps;
  reportedProperties["thermostat1"] = component;

  await client.UpdateReportedPropertiesAsync(reportedProperties);
}, null);
```

Yuvalanmış bir bileşen için cihaz ikizi, istenen ve raporlanan bölümleri aşağıdaki gibi gösterir:

```json
{
  "desired" : {
    "thermostat1" : {
        "__t" : "c",
        "targetTemperature": 23.2,
    }
    "$version" : 3
  },
  "reported": {
    "thermostat1" : {
        "__t" : "c",
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
    }
  }
}
```

### <a name="commands"></a>Komutlar

Varsayılan bir bileşen, hizmet tarafından çağrıldığı şekilde komut adını alır.

İç içe yerleştirilmiş bir bileşen, bileşen adı ve ayırıcıdan önekli komut adını alır `*` .

```csharp
await client.SetMethodHandlerAsync("themostat*reboot", (MethodRequest req, object ctx) =>
{
  Console.WriteLine("REBOOT");
  return Task.FromResult(new MethodResponse(200));
},
null);
```

#### <a name="request-and-response-payloads"></a>İstek ve yanıt yükleri

Komutları, istek ve yanıt yüklerini tanımlamak için türleri kullanır. Bir cihaz gelen giriş parametresinin serisini çıkarmalıdır ve yanıtı serileştirmelidir. Aşağıdaki örnek, yüklerde tanımlanan karmaşık türlerle bir komutun nasıl uygulanacağını gösterir:

```json
{
  "@type": "Command",
  "name": "start",
  "request": {
    "name": "startRequest",
    "schema": {
      "@type": "Object",
      "fields": [
        {
          "name": "startPriority",
          "schema": "integer"
        },
        {
          "name": "startMessage",
          "schema" : "string"
        }
      ]
    }
  },
  "response": {
    "name": "startReponse",
    "schema": {
      "@type": "Object",
      "fields": [
        {
            "name": "startupTime",
            "schema": "integer" 
        },
        {
          "name": "startupMessage",
          "schema": "string"
        }
      ]
    }
  }
}
```

Aşağıdaki kod parçacıkları, bir cihazın serileştirme ve serisini kaldırma için kullanılan türler dahil olmak üzere bu komut tanımını nasıl uyguladığını göstermektedir:

```csharp
class startRequest
{
  public int startPriority { get; set; }
  public string startMessage { get; set; }
}

class startResponse
{
  public int startupTime { get; set; }
  public string startupMessage { get; set; }
}

// ... 

await client.SetMethodHandlerAsync("start", (MethodRequest req, object ctx) =>
{
  var startRequest = JsonConvert.DeserializeObject<startRequest>(req.DataAsJson);
  Console.WriteLine($"Received start command with priority ${startRequest.startPriority} and ${startRequest.startMessage}");

  var startResponse = new startResponse
  {
    startupTime = 123,
    startupMessage = "device started with message " + startRequest.startMessage
  };

  string responsePayload = JsonConvert.SerializeObject(startResponse);
  MethodResponse response = new MethodResponse(Encoding.UTF8.GetBytes(responsePayload), 200);
  return Task.FromResult(response);
},null);
```

> [!Tip]
> İstek ve yanıt adları, tel üzerinden iletilen serileştirilmiş yüklere mevcut değildir.
