---
title: UDF 'ler hata ayıklama-Azure dijital TWINS | Microsoft Docs
description: Azure dijital TWINS 'de Kullanıcı tanımlı işlevlerde hata ayıklama için önerilen yaklaşımlar hakkında bilgi edinin.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/01/2019
ms.custom: seodec18
ms.openlocfilehash: 130250156f0fae3e6c40742278479b5d4612657b
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74005929"
---
# <a name="how-to-debug-user-defined-functions-in-azure-digital-twins"></a>Azure dijital TWINS 'de Kullanıcı tanımlı işlevlerde hata ayıklama

Bu makalede, Azure dijital TWINS 'de Kullanıcı tanımlı işlevlerin nasıl tanılanacağı ve ayıklanacağı özetlenmektedir. Daha sonra, hata ayıklama sırasında bulunan en yaygın senaryolardan bazılarını tanımlar.

>[!TIP]
> Etkinlik günlükleri, tanılama günlükleri ve Azure Izleyici kullanarak Azure dijital TWINS 'te hata ayıklama araçları ayarlama hakkında daha fazla bilgi edinmek için [izleme ve günlüğe kaydetme 'Yi nasıl yapılandıracağınızı](./how-to-configure-monitoring.md) okuyun.

## <a name="debug-issues"></a>Hata ayıklama sorunları

Azure dijital TWINS içindeki sorunların nasıl tanınacağına bilmek, sorunları etkili bir şekilde analiz etmenizi, sorunların nedenlerini belirlemenize ve bunlara uygun çözümler sağlamanıza olanak tanır.

Bu uca çeşitli günlük kaydı, analiz ve tanılama araçları sunulmaktadır.

### <a name="enable-logging-for-your-instance"></a>Örneğiniz için günlüğe kaydetmeyi etkinleştirme

Azure dijital TWINS, sağlam günlük kaydı, izleme ve analiz desteği. Çözümler geliştiricileri, IoT uygulamasının karmaşık izleme ihtiyaçlarını desteklemek için Azure Izleyici günlüklerini, tanılama günlüklerini, etkinlik günlüklerini ve diğer hizmetleri kullanabilirler. Günlüğe kaydetme seçenekleri, çeşitli hizmetlerde kayıtları sorgulamak veya göstermek ve birçok hizmet için parçalı günlük kapsamı sağlamak üzere birleştirilebilir.

* Azure dijital TWINS 'e özgü günlüğe kaydetme yapılandırması için, [izleme ve günlüğe kaydetmeyi yapılandırma](./how-to-configure-monitoring.md)makalesini okuyun.
* Azure Izleyici ile etkin olan güçlü günlük ayarları hakkında bilgi edinmek için [Azure izleyicisine](../azure-monitor/overview.md) genel bakış bölümüne başvurun.
* Azure dijital TWINS 'de Azure portal, Azure CLı veya PowerShell aracılığıyla tanılama günlüğü ayarlarını yapılandırmak için [Azure kaynaklarınızdaki günlük verilerini toplama ve](../azure-monitor/platform/resource-logs-overview.md) kullanma makalesini inceleyin.

Yapılandırıldıktan sonra tüm günlük kategorilerini, ölçümleri seçebilir ve güçlü Azure Izleyici Log Analytics çalışma alanlarını kullanarak hata ayıklama çabalarınızı destekleyebilirsiniz.

### <a name="trace-sensor-telemetry"></a>İzleme algılayıcı telemetrisi

Algılayıcı telemetrisini izlemek için, Azure dijital TWINS örneğiniz için tanılama ayarlarının etkinleştirildiğini doğrulayın. Sonra, istenen tüm günlük kategorilerinin seçili olduğundan emin olun. Son olarak, istenen günlüklerin Azure Izleyici günlüklerine gönderildiğinden emin olun.

Bir algılayıcı telemetri iletisini ilgili günlükleriyle eşleştirmek için, gönderilmekte olan olay verileri üzerinde bir bağıntı KIMLIĞI belirtebilirsiniz. Bunu yapmak için `x-ms-client-request-id` özelliğini bir GUID olarak ayarlayın.

Telemetri gönderdikten sonra, set bağıntı KIMLIĞINI kullanarak günlükleri sorgulamak için Azure Izleyici Log Analytics 'i açın:

```Kusto
AzureDiagnostics
| where CorrelationId == 'YOUR_CORRELATION_IDENTIFIER'
```

| Sorgu değeri | Şununla değiştir |
| --- | --- |
| YOUR_CORRELATION_IDENTIFIER | Olay verilerinde belirtilen bağıntı KIMLIĞI |

Tüm son telemetri günlükleri sorgusunu görmek için:

```Kusto
AzureDiagnostics
| order by CorrelationId desc
```

Kullanıcı tanımlı işleviniz için günlük kaydını etkinleştirirseniz, bu Günlükler Log Analytics Örneğinizde kategori `UserDefinedFunction`görüntülenir. Bunları almak için Log Analytics 'te aşağıdaki sorgu koşulunu girin:

```Kusto
AzureDiagnostics
| where Category == 'UserDefinedFunction'
```

Güçlü sorgu işlemleri hakkında daha fazla bilgi için [sorguları](../azure-monitor/log-query/get-started-queries.md)kullanmaya başlama makalesini okuyun.

## <a name="identify-common-issues"></a>Yaygın sorunları tanımla

Hem tanılama hem de tanımlama yaygın sorunlar, çözümünüzde sorun giderirken önemlidir. Kullanıcı tanımlı işlevlerin geliştirilmesi sırasında yaygın olarak karşılaşılan birkaç sorun aşağıdaki alt bölümlerde özetlenir.

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

### <a name="check-if-a-role-assignment-was-created"></a>Rol atamasının oluşturulup oluşturulmadıysa denetle

Yönetim API 'SI içinde oluşturulmuş bir rol ataması olmadan, Kullanıcı tanımlı işlevin bildirimleri gönderme, meta verileri alma ve topoloji içinde hesaplanan değerleri ayarlama gibi eylemleri gerçekleştirmek için erişimi yoktur.

Yönetim API 'niz aracılığıyla Kullanıcı tanımlı işleviniz için bir rol atamasının mevcut olup olmadığını denetleyin:

```plaintext
GET YOUR_MANAGEMENT_API_URL/roleassignments?path=/&traverse=Down&objectId=YOUR_USER_DEFINED_FUNCTION_ID
```

| Parametre değeri | Şununla değiştir |
| --- | --- |
| YOUR_USER_DEFINED_FUNCTION_ID | Rol atamalarını almak için Kullanıcı tanımlı işlevin KIMLIĞI|

Rol [ataması yoksa, Kullanıcı tanımlı işleviniz için rol ataması oluşturmayı](./how-to-user-defined-functions.md)öğrenin.

### <a name="check-if-the-matcher-works-for-a-sensors-telemetry"></a>Eşleştirici 'nin bir sensör telemetri için çalışıp çalışmadığını denetle

Azure Digital TWINS örneklerinizin yönetim API 'sine karşı aşağıdaki çağrıdan, belirli bir eşleştirici belirtilen algılayıcı için geçerli olup olmadığını belirleyebilirsiniz.

```plaintext
GET YOUR_MANAGEMENT_API_URL/matchers/YOUR_MATCHER_IDENTIFIER/evaluate/YOUR_SENSOR_IDENTIFIER?enableLogging=true
```

| Parametre | Şununla değiştir |
| --- | --- |
| *YOUR_MATCHER_IDENTIFIER* | Değerlendirmek istediğiniz eşleştirici KIMLIĞI |
| *YOUR_SENSOR_IDENTIFIER* | Değerlendirmek istediğiniz sensör KIMLIĞI |

Yanıt:

```JavaScript
{
    "success": true,
    "logs": [
        "$.dataType: \"Motion\" Equals \"Motion\" => True"
    ]
}
```

### <a name="check-what-a-sensor-triggers"></a>Sensörlerin nasıl tetikleyeceğini denetleme

Azure dijital TWINS yönetim API 'Lerine yönelik aşağıdaki çağrı sayesinde, belirtilen algılayıcı gelen telemetri tarafından tetiklenen Kullanıcı tanımlı işlevlerinizin tanımlayıcılarını belirleyebileceksiniz:

```plaintext
GET YOUR_MANAGEMENT_API_URL/sensors/YOUR_SENSOR_IDENTIFIER/matchers?includes=UserDefinedFunctions
```

| Parametre | Şununla değiştir |
| --- | --- |
| *YOUR_SENSOR_IDENTIFIER* | Telemetri göndermek için sensör KIMLIĞI |

Yanıt:

```JavaScript
[
    {
        "id": "48a64768-797e-4832-86dd-de625f5f3fd9",
        "name": "MotionMatcher",
        "spaceId": "2117b3e1-b6ce-42c1-9b97-0158bef59eb7",
        "conditions": [
            {
                "id": "024a067a-414f-415b-8424-7df61392541e",
                "target": "Sensor",
                "path": "$.dataType",
                "value": "\"Motion\"",
                "comparison": "Equals"
            }
        ],
        "userDefinedFunctions": [
            {
                "id": "373d03c5-d567-4e24-a7dc-f993460120fc",
                "spaceId": "2117b3e1-b6ce-42c1-9b97-0158bef59eb7",
                "name": "Motion User-Defined Function",
                "disabled": false
            }
        ]
    }
]
```

### <a name="issue-with-receiving-notifications"></a>Bildirim alma ile ilgili sorun

Tetiklenen Kullanıcı tanımlı işlevden bildirimler almadığınızda, topoloji nesne türü parametresinin kullanılmakta olan tanımlayıcı türüyle eşleştiğini doğrulayın.

**Yanlış** Örneğinde

```JavaScript
var customNotification = {
    Message: 'Custom notification that will not work'
};

sendNotification(telemetry.SensorId, "Space", JSON.stringify(customNotification));
```

Bu senaryo, kullanılan tanımlayıcı bir sensöre başvurduğundan, belirtilen topoloji nesne türü `Space`olduğu için ortaya çıkar.

**Doğru** Örneğinde

```JavaScript
var customNotification = {
    Message: 'Custom notification that will work'
};

sendNotification(telemetry.SensorId, "Sensor", JSON.stringify(customNotification));
```

Bu sorunu çözmek için en kolay yol, meta veri nesnesi üzerinde `Notify` yöntemi kullanmaktır.

Örnek:

```JavaScript
function process(telemetry, executionContext) {
    var sensorMetadata = getSensorMetadata(telemetry.SensorId);

    var customNotification = {
        Message: 'Custom notification'
    };

    // Short-hand for above methods where object type is known from metadata.
    sensorMetadata.Notify(JSON.stringify(customNotification));
}
```

## <a name="common-diagnostic-exceptions"></a>Yaygın tanılama özel durumları

Tanılama ayarlarını etkinleştirirseniz, bu ortak özel durumlarla karşılaşabilirsiniz:

1. **Daraltma**: Kullanıcı tanımlı Işleviniz, [hizmet limitleri](./concepts-service-limits.md) makalesinde özetlenen yürütme hızı sınırlarını aşarsa, bu kısıtlanacak. Azaltma sınırları sona erene kadar başka işlem başarılı bir şekilde yürütülmedi.

1. **Veri bulunamadı**: Kullanıcı tanımlı işleviniz mevcut olmayan meta verilere erişmeye çalışırsa, işlem başarısız olur.

1. **Yetkilendirilmemiş**: Kullanıcı tanımlı işlevinizde bir rol atama kümesi yoksa veya topolojiden belirli meta verilere erişmek için yeterli izin yoksa işlem başarısız olur.

## <a name="next-steps"></a>Sonraki adımlar

- Azure dijital TWINS 'de [izleme ve günlükleri](./how-to-configure-monitoring.md) etkinleştirme hakkında bilgi edinin.

- Azure günlük seçenekleri hakkında daha fazla bilgi için [Azure etkinlik günlüğü ' ne genel bakış](../azure-monitor/platform/activity-logs-overview.md) makalesini okuyun.
