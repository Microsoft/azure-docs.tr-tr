---
title: Kullanıcı tanımlı işlevler oluşturma-Azure dijital TWINS 'te | Microsoft Docs
description: Azure dijital TWINS 'de Kullanıcı tanımlı işlevler, eşleştiriciler ve rol atamaları oluşturma.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/07/2019
ms.custom: seodec18
ms.openlocfilehash: 4db6f0052c92d4532917a996eda82a27d97d3063
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74009562"
---
# <a name="how-to-create-user-defined-functions-in-azure-digital-twins"></a>Azure dijital TWINS 'de Kullanıcı tanımlı işlevler oluşturma

[Kullanıcı tanımlı işlevler](./concepts-user-defined-functions.md) , kullanıcıların gelen telemetri iletilerinden ve uzamsal grafik meta verilerinden yürütülmesi için özel mantık yapılandırmasına olanak sağlar. Kullanıcılar ayrıca, önceden tanımlı [uç noktalara](./how-to-egress-endpoints.md)olay gönderebilir.

Bu kılavuzda, alınan sıcaklık olaylarından belirli bir sıcaklığın aşıldığı herhangi bir okumayı nasıl tespit ve uyaracağınızı gösteren bir örnek gösterilmektedir.

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

## <a name="client-library-reference"></a>İstemci kitaplığı başvurusu

Kullanıcı tanımlı işlevler çalışma zamanında yardımcı yöntemler olarak kullanılabilen işlevler, [istemci kitaplığı başvuru](./reference-user-defined-functions-client-library.md) belgesinde listelenir.

## <a name="create-a-matcher"></a>Bir eşleştirici oluşturma

Eşleştiriciler, belirli bir telemetri iletisi için hangi kullanıcı tanımlı işlevlerin çalıştırılacağını tespit eden grafik nesneleridir.

- Geçerli Eşleştiricisi koşul karşılaştırmaları:

  - `Equals`
  - `NotEquals`
  - `Contains`

- Geçerli Eşleştiricisi koşul hedefleri:

  - `Sensor`
  - `SensorDevice`
  - `SensorSpace`

Aşağıdaki örnek Eşleştiricisi, veri türü değeri olarak `"Temperature"` tüm algılayıcı telemetri olaylarından doğru olarak değerlendirilir. Kimliği doğrulanmış bir HTTP POST isteği yaparak, Kullanıcı tanımlı bir işlevde birden fazla eşleşme oluşturabilirsiniz:

```plaintext
YOUR_MANAGEMENT_API_URL/matchers
```

JSON gövdesi ile:

```JSON
{
  "id": "3626464-f39b-46c0-d9b0c-436aysj55",
  "name": "Temperature Matcher",
  "spaceId": "YOUR_SPACE_IDENTIFIER",
  "conditions": [
    {
      "id": "ag7gq35cfu3-e15a-4e9c-6437-sj6w68sy44s",
      "target": "Sensor",
      "path": "$.dataType",
      "value": "\"Temperature\"",
      "comparison": "Equals"
    }
  ]
}
```

| Değer | Şununla değiştir |
| --- | --- |
| YOUR_SPACE_IDENTIFIER | Örneğiniz üzerinde barındırılıyorsa hangi sunucu bölge |

## <a name="create-a-user-defined-function"></a>Kullanıcı tanımlı işlev oluşturma

Kullanıcı tanımlı bir işlev oluşturmak, Azure Digital TWINS yönetim API 'Lerine çok parçalı bir HTTP isteği yapmayı içerir.

[!INCLUDE [Digital Twins multipart requests](../../includes/digital-twins-multipart.md)]

Eşleştiriciler oluşturulduktan sonra, aşağıdaki kimliği doğrulanmış çok parçalı HTTP POST isteğiyle işlev parçacığını karşıya yükleyin:

```plaintext
YOUR_MANAGEMENT_API_URL/userdefinedfunctions
```

Aşağıdaki gövdesini kullanın:

```plaintext
--USER_DEFINED_BOUNDARY
Content-Type: application/json; charset=utf-8
Content-Disposition: form-data; name="metadata"

{
  "spaceId": "YOUR_SPACE_IDENTIFIER",
  "name": "User Defined Function",
  "description": "The contents of this udf will be executed when matched against incoming telemetry.",
  "matchers": ["YOUR_MATCHER_IDENTIFIER"]
}
--USER_DEFINED_BOUNDARY
Content-Disposition: form-data; name="contents"; filename="userDefinedFunction.js"
Content-Type: text/javascript

function process(telemetry, executionContext) {
  // Code goes here.
}

--USER_DEFINED_BOUNDARY--
```

| Değer | Şununla değiştir |
| --- | --- |
| USER_DEFINED_BOUNDARY | Çok parçalı bir içerik sınır adı |
| YOUR_SPACE_IDENTIFIER | Alan tanımlayıcısı  |
| YOUR_MATCHER_IDENTIFIER | Kullanmak istediğiniz eşleştirici KIMLIĞI |

1. Üstbilgilerin şunları içerdiğini doğrulayın: `Content-Type: multipart/form-data; boundary="USER_DEFINED_BOUNDARY"`.
1. Gövdenin çok parçalı olduğunu doğrulayın:

   - İlk bölüm, gerekli Kullanıcı tanımlı işlev meta verilerini içerir.
   - İkinci bölüm JavaScript işlem mantığını içerir.

1. **USER_DEFINED_BOUNDARY** bölümünde, **spaceıd** (`YOUR_SPACE_IDENTIFIER`) ve **Matchers** (`YOUR_MATCHER_IDENTIFIER`) değerlerini değiştirin.
1. JavaScript Kullanıcı tanımlı işlevinin `Content-Type: text/javascript`olarak verildiğini doğrulayın.

### <a name="example-functions"></a>Örnek işlevler

Algılayıcı telemetrisini, `sensor.DataType`veri türü **sıcaklık**ile doğrudan algılayıcı için okuma olarak ayarlayın:

```JavaScript
function process(telemetry, executionContext) {

  // Get sensor metadata
  var sensor = getSensorMetadata(telemetry.SensorId);

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Set the sensor reading as the current value for the sensor.
  setSensorValue(telemetry.SensorId, sensor.DataType, parseReading.SensorValue);
}
```

**Telemetri** parametresi, bir algılayıcı tarafından gönderilen iletiye karşılık gelen **sensorıd** ve **ileti** özniteliklerini gösterir. **ExecutionContext** parametresi aşağıdaki öznitelikleri kullanıma sunar:

```csharp
var executionContext = new UdfExecutionContext
{
    EnqueuedTime = request.HubEnqueuedTime,
    ProcessorReceivedTime = request.ProcessorReceivedTime,
    UserDefinedFunctionId = request.UserDefinedFunctionId,
    CorrelationId = correlationId.ToString(),
};
```

Sonraki örnekte, algılayıcı telemetri okuma işi önceden tanımlanmış bir eşiği geçerse bir ileti günlüğe kaydedilir. Azure dijital TWINS örneğinde tanılama ayarlarınız etkinleştirilmişse, Kullanıcı tanımlı işlevlerden alınan Günlükler de iletilir:

```JavaScript
function process(telemetry, executionContext) {

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Example sensor telemetry reading range is greater than 0.5
  if(parseFloat(parseReading.SensorValue) > 0.5) {
    log(`Alert: Sensor with ID: ${telemetry.SensorId} detected an anomaly!`);
  }
}
```

Aşağıdaki kod, sıcaklık düzeyi önceden tanımlanmış sabitin üzerine yükselse bir bildirimi tetikler:

```JavaScript
function process(telemetry, executionContext) {

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Define threshold
  var threshold = 70;

  // Trigger notification 
  if(parseInt(parseReading) > threshold) {
    var alert = {
      message: 'Temperature reading has surpassed threshold',
      sensorId: telemetry.SensorId,
      reading: parseReading
    };

    sendNotification(telemetry.SensorId, "Sensor", JSON.stringify(alert));
  }
}
```

Daha karmaşık bir Kullanıcı tanımlı işlev kodu örneği için bkz. [doluluk hızlı](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availability.js)başlangıcı.

## <a name="create-a-role-assignment"></a>Rol ataması oluşturma

Kullanıcı tanımlı işlevin altında çalışacağı bir rol ataması oluşturun. Kullanıcı tanımlı işlev için herhangi bir rol ataması yoksa, yönetim API 'SI ile etkileşim kurmak için gerekli izinlere sahip olmaz veya grafik nesnelerinde eylem gerçekleştirmeye yönelik erişime sahip olmaz. Kullanıcı tanımlı bir işlevin gerçekleştirebileceği eylemler, Azure Digital TWINS yönetim API 'Leri içinde rol tabanlı erişim denetimi aracılığıyla belirtilir ve tanımlanır. Örneğin, Kullanıcı tanımlı işlevler belirli roller veya belirli erişim denetimi yolları belirtilerek kapsamda sınırlandırılabilir. Daha fazla bilgi için bkz. [rol tabanlı erişim denetimi](./security-role-based-access-control.md) belgeleri.

1. Kullanıcı tanımlı işlevinizde atamak istediğiniz rol KIMLIĞINI almak için tüm roller için [SISTEM API 'Sini sorgulayın](./security-create-manage-role-assignments.md#retrieve-all-roles) . Şunları yapmak için kimliği doğrulanmış bir HTTP GET isteği yaparak:

    ```plaintext
    YOUR_MANAGEMENT_API_URL/system/roles
    ```
   İstenen rol KIMLIĞINI koruyun. Aşağıdaki JSON gövde özniteliği **rol kimliği** (`YOUR_DESIRED_ROLE_IDENTIFIER`) olarak geçirilir.

1. **ObjectID** (`YOUR_USER_DEFINED_FUNCTION_ID`), daha önce oluşturulmuş Kullanıcı TANıMLı işlev kimliği olacaktır.
1. `fullpath`ile alanları sorgulayarak **yolun** (`YOUR_ACCESS_CONTROL_PATH`) değerini bulun.
1. Döndürülen `spacePaths` değerini kopyalayın. Bunu aşağıda kullanacaksınız. Kimliği doğrulanmış bir HTTP GET isteği oluşturmak için:

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?name=YOUR_SPACE_NAME&includes=fullpath
    ```

    | Değer | Şununla değiştir |
    | --- | --- |
    | YOUR_SPACE_NAME | Kullanmak istediğiniz alanın adı |

1. Kimliği doğrulanmış bir HTTP POST isteği yaparak Kullanıcı tanımlı bir işlev rolü ataması oluşturmak için döndürülen `spacePaths` değerini **yola** yapıştırın:

    ```plaintext
    YOUR_MANAGEMENT_API_URL/roleassignments
    ```
    JSON gövdesi ile:

    ```JSON
    {
      "roleId": "YOUR_DESIRED_ROLE_IDENTIFIER",
      "objectId": "YOUR_USER_DEFINED_FUNCTION_ID",
      "objectIdType": "YOUR_USER_DEFINED_FUNCTION_TYPE_ID",
      "path": "YOUR_ACCESS_CONTROL_PATH"
    }
    ```

    | Değer | Şununla değiştir |
    | --- | --- |
    | YOUR_DESIRED_ROLE_IDENTIFIER | İstenen rolün tanımlayıcısı |
    | YOUR_USER_DEFINED_FUNCTION_ID | Kullanmak istediğiniz kullanıcı tanımlı işlevin KIMLIĞI |
    | YOUR_USER_DEFINED_FUNCTION_TYPE_ID | Kullanıcı tanımlı işlev türünü belirten KIMLIK |
    | YOUR_ACCESS_CONTROL_PATH | Erişim denetimi yolu |

>[!TIP]
> Kullanıcı tanımlı işlev yönetimi API işlemleri ve uç noktaları hakkında daha fazla bilgi için [rol atamaları oluşturma ve yönetme](./security-create-manage-role-assignments.md) makalesini okuyun.

## <a name="send-telemetry-to-be-processed"></a>İşlenmek üzere telemetri gönder

Uzamsal zeka grafiğinde tanımlanan algılayıcı telemetri gönderir. Buna karşılık telemetri, yüklenen Kullanıcı tanımlı işlevin yürütülmesini tetikler. Veri işlemcisi Telemetriyi seçer. Ardından, Kullanıcı tanımlı işlevin çağrılması için bir yürütme planı oluşturulur.

1. Okumayı oluşturan algılayıcı için eşleştiriciler alın.
1. Hangi eşleştiriciler başarıyla değerlendirildiğine bağlı olarak, ilişkili kullanıcı tanımlı işlevleri alın.
1. Kullanıcı tanımlı her işlevi yürütün.

## <a name="next-steps"></a>Sonraki adımlar

- Olayları göndermek için [Azure dijital TWINS uç noktaları oluşturmayı](./how-to-egress-endpoints.md) öğrenin.

- Azure dijital TWINS 'de yönlendirme hakkında daha fazla bilgi için [yönlendirme olayları ve iletilerini](./concepts-events-routing.md)okuyun.

- [İstemci kitaplığı başvuru belgelerini](./reference-user-defined-functions-client-library.md)gözden geçirin.