---
title: Dayanıklı İşlevler-Azure Işlevlerinde HTTP API 'Leri
description: Azure Işlevleri için Dayanıklı İşlevler uzantısında HTTP API 'Leri uygulamayı öğrenin.
author: cgillum
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: azfuncdf
ms.openlocfilehash: 0ab9f33616547c073e8e3a2128a441238bf3a17d
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106220462"
---
# <a name="http-api-reference"></a>HTTP API başvurusu

Dayanıklı İşlevler uzantısı, düzenlemeler, [varlıklar](durable-functions-types-features-overview.md#entity-functions)ve [görev hub 'ları](durable-functions-task-hubs.md) [üzerinde yönetim](durable-functions-types-features-overview.md#orchestrator-functions)GÖREVLERINI gerçekleştirmek Için kullanılabilecek bir yerleşik HTTP API 'leri kümesi sunar. Bu HTTP API 'Leri, Azure Işlevleri ana bilgisayarı tarafından yetkilendirilen ancak doğrudan Dayanıklı İşlevler uzantısı tarafından işlenen genişletilebilirlik Web kancaları.

Uzantı tarafından uygulanan tüm HTTP API 'Leri aşağıdaki parametreleri gerektirir. Tüm parametrelerin veri türü `string` .

| Parametre        | Parametre türü  | Description |
|------------------|-----------------|-------------|
| **`taskHub`**    | Sorgu dizesi    | [Görev hub 'ının](durable-functions-task-hubs.md)adı. Belirtilmemişse, geçerli işlev uygulamasının görev hub 'ı adı varsayılır. |
| **`connection`** | Sorgu dizesi    | Depolama hesabı için bağlantı dizesinin **adı** . Belirtilmemişse, işlev uygulaması için varsayılan bağlantı dizesi varsayılır. |
| **`systemKey`**  | Sorgu dizesi    | API 'YI çağırmak için gereken yetkilendirme anahtarı. |

`systemKey` , Azure Işlevleri ana bilgisayarı tarafından oluşturulan bir yetkilendirme anahtarıdır. Bu, özellikle dayanıklı görev uzantısı API 'Lerine erişim verir ve [diğer yetkilendirme anahtarlarıyla](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Key-management-API)aynı şekilde yönetilebilir. `taskHub` `connection` `systemKey` [](durable-functions-bindings.md#orchestration-client) `CreateCheckStatusResponse` `CreateHttpManagementPayload` .Net 'teki ve API 'leri ya da `createCheckStatusResponse` JavaScript 'teki API 'ler gibi Orchestration istemci bağlama API 'lerini kullanarak doğru, ve sorgu dizesi değerlerini içeren URL 'ler oluşturabilirsiniz `createHttpManagementPayload` .

Sonraki birkaç bölümde uzantı tarafından desteklenen belirli HTTP API 'Leri ele alınmaktadır ve bunların nasıl kullanılacağına ilişkin örnekler sağlanmaktadır.

## <a name="start-orchestration"></a>Düzenleme Başlat

Belirtilen Orchestrator işlevinin yeni bir örneğini yürütmeye başlar.

### <a name="request"></a>İstek

Işlevler çalışma zamanının 1. x sürümü için, istek aşağıdaki gibi biçimlendirilir (açıklık için birden fazla satır gösterilir):

```http
POST /admin/extensions/DurableTaskExtension/orchestrators/{functionName}/{instanceId?}
     ?taskHub={taskHub}
     &connection={connectionName}
     &code={systemKey}
```

Işlevler çalışma zamanının 2. x sürümünde, URL biçimi aynı parametrelere sahip ancak biraz farklı bir ön eke sahiptir:

```http
POST /runtime/webhooks/durabletask/orchestrators/{functionName}/{instanceId?}
     ?taskHub={taskHub}
     &connection={connectionName}
     &code={systemKey}
```

Bu API için istek parametreleri, daha önce belirtilen varsayılan kümeyi ve aşağıdaki benzersiz parametreleri içerir:

| Alan              | Parametre türü  | Açıklama |
|--------------------|-----------------|-------------|
| **`functionName`** | URL             | Başlatılacak Orchestrator işlevinin adı. |
| **`instanceId`**   | URL             | İsteğe bağlı parametre. Orchestration örneğinin KIMLIĞI. Belirtilmemişse, Orchestrator işlevi rastgele bir örnek KIMLIĞIYLE başlayacaktır. |
| **`{content}`**    | İstek içeriği | İsteğe bağlı. JSON biçimli Orchestrator işlev girişi. |

### <a name="response"></a>Yanıt

Olası birkaç durum kodu değeri döndürülebilir.

* **HTTP 202 (kabul edildi)**: belirtilen Orchestrator işlevi çalışmaya başlamak üzere zamanlandı. `Location`Yanıt üst bilgisi, düzenleme durumunu yoklamaya yönelik BIR URL içerir.
* **HTTP 400 (hatalı istek)**: belirtilen Orchestrator işlevi yok, BELIRTILEN örnek kimliği geçerli değil veya istek içeriği GEÇERLI bir JSON değil.

Aşağıda, `RestartVMs` Orchestrator işlevi başlatan ve JSON nesne yükünü içeren örnek bir istek verilmiştir:

```http
POST /runtime/webhooks/durabletask/orchestrators/RestartVMs?code=XXX
Content-Type: application/json
Content-Length: 83

{
    "resourceGroup": "myRG",
    "subscriptionId": "111deb5d-09df-4604-992e-a968345530a9"
}
```

**HTTP 202** örnekleri için yanıt yükü aşağıdaki alanları IÇEREN bir JSON nesnesidir:

| Alan                       | Açıklama                          |
|-----------------------------|--------------------------------------|
| **`id`**                    |Orchestration örneğinin KIMLIĞI. |
| **`statusQueryGetUri`**     |Orchestration örneğinin durum URL 'SI. |
| **`sendEventPostUri`**      |Orchestration örneğinin "olay oluştur" URL 'SI. |
| **`terminatePostUri`**      |Orchestration örneğinin "Terminate" URL 'SI. |
| **`purgeHistoryDeleteUri`** |Orchestration örneğinin "Temizleme geçmişi" URL 'SI. |
| **`rewindPostUri`**         |Önizle Orchestration örneğinin "geri sarma" URL 'SI. |

Tüm alanların veri türü `string` .

Aşağıda, `abc123` kimliği (okunabilirlik için biçimlendirilen) olan bir Orchestration örneği için örnek yanıt yükü verilmiştir:

```http
{
    "id": "abc123",
    "purgeHistoryDeleteUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "sendEventPostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/raiseEvent/{eventName}?code=XXX",
    "statusQueryGetUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "terminatePostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/terminate?reason={text}&code=XXX"
}
```

HTTP yanıtının *yoklama tüketici düzeniyle* uyumlu olması amaçlanmıştır. Ayrıca, aşağıdaki önemli yanıt üstbilgilerini de içerir:

* **Konum**: durum uç noktasının URL 'si. Bu URL, alanla aynı değeri içerir `statusQueryGetUri` .
* **Yeniden deneme-sonra**: yoklama işlemleri arasında beklenecek saniye sayısı. `10` varsayılan değerdir.

Zaman uyumsuz HTTP yoklama düzeniyle ilgili daha fazla bilgi için bkz. [http zaman uyumsuz işlem izleme](durable-functions-http-features.md#async-operation-tracking) belgeleri.

## <a name="get-instance-status"></a>Örnek durumunu al

Belirtilen bir düzenleme örneğinin durumunu alır.

### <a name="request"></a>İstek

Işlevler çalışma zamanının 1. x sürümü için, istek aşağıdaki gibi biçimlendirilir (açıklık için birden fazla satır gösterilir):

```http
GET /admin/extensions/DurableTaskExtension/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &showHistory=[true|false]
    &showHistoryOutput=[true|false]
    &showInput=[true|false]
    &returnInternalServerErrorOnFailure=[true|false]
```

Işlevler çalışma zamanının 2. x sürümünde, URL biçimi aynı parametrelere sahip ancak biraz farklı bir ön eke sahiptir:

```http
GET /runtime/webhooks/durabletask/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &showHistory=[true|false]
    &showHistoryOutput=[true|false]
    &showInput=[true|false]
    &returnInternalServerErrorOnFailure=[true|false]
```

Bu API için istek parametreleri, daha önce belirtilen varsayılan kümeyi ve aşağıdaki benzersiz parametreleri içerir:

| Alan                   | Parametre türü  | Açıklama |
|-------------------------|-----------------|-------------|
| **`instanceId`**        | URL             | Orchestration örneğinin KIMLIĞI. |
| **`showInput`**         | Sorgu dizesi    | İsteğe bağlı parametre. Olarak ayarlanırsa `false` , işlev girişi yanıt yüküne dahil edilmez.|
| **`showHistory`**       | Sorgu dizesi    | İsteğe bağlı parametre. Olarak ayarlanırsa `true` , Orchestration yürütme geçmişi yanıt yüküne dahil edilir.|
| **`showHistoryOutput`** | Sorgu dizesi    | İsteğe bağlı parametre. Olarak ayarlanırsa `true` , işlev çıkışları Orchestration yürütme geçmişine dahil edilir.|
| **`createdTimeFrom`**   | Sorgu dizesi    | İsteğe bağlı parametre. Belirtildiğinde, belirtilen ıSO8601 zaman damgasında veya bundan sonra oluşturulan döndürülen örneklerin listesini filtreler.|
| **`createdTimeTo`**     | Sorgu dizesi    | İsteğe bağlı parametre. Belirtildiğinde, belirtilen ıSO8601 zaman damgasında veya daha önce oluşturulan döndürülen örneklerin listesini filtreler.|
| **`runtimeStatus`**     | Sorgu dizesi    | İsteğe bağlı parametre. Belirtildiğinde, döndürülen örneklerin listesini çalışma zamanı durumlarına göre filtreler. Olası çalışma zamanı durum değerlerinin listesini görmek için, [örnekleri sorgulama](durable-functions-instance-management.md) makalesine bakın. |
| **`returnInternalServerErrorOnFailure`**  | Sorgu dizesi    | İsteğe bağlı parametre. , Olarak ayarlandıysa `true` , örnek bir hata durumundaysa, bu apı 200 yerine BIR HTTP 500 yanıtı döndürür. Bu parametre otomatik durum yoklama senaryolarına yöneliktir. |

### <a name="response"></a>Yanıt

Olası birkaç durum kodu değeri döndürülebilir.

* **HTTP 200 (Tamam)**: belirtilen örnek tamamlanmış veya başarısız durumda.
* **HTTP 202 (kabul edildi)**: belirtilen örnek devam ediyor.
* **HTTP 400 (hatalı istek)**: belirtilen örnek başarısız oldu veya sonlandırıldı.
* **HTTP 404 (bulunamadı)**: belirtilen örnek yok veya çalışmaya başlamadı.
* **HTTP 500 (Iç sunucu hatası)**: yalnızca `returnInternalServerErrorOnFailure` öğesine ayarlandığında `true` ve belirtilen örnek işlenmeyen bir özel durumla başarısız olduğunda döndürülür.

**Http 200** ve **http 202** örnekleri için yanıt yükü AŞAĞıDAKI alanları içeren bir JSON nesnesidir:

| Alan                 | Veri türü | Description |
|-----------------------|-----------|-------------|
| **`runtimeStatus`**   | dize    | Örneğin çalışma zamanı durumu. Değerler *çalışıyor*, *bekliyor*, *başarısız*, *iptal edildi*, *sonlandırıldı*, *tamamlandı* olarak verilebilir. |
| **`input`**           | JSON      | Örneği başlatmak için kullanılan JSON verileri. Bu alan, `null` `showInput` sorgu dizesi parametresi olarak ayarlanmışsa olur `false` .|
| **`customStatus`**    | JSON      | Özel düzenleme durumu için kullanılan JSON verileri. Bu alan `null` ayarlanmamışsa. |
| **`output`**          | JSON      | Örneğin JSON çıktısı. Bu alan, `null` Örneğin tamamlanmış durumda olmaması durumunda olur. |
| **`createdTime`**     | string    | Örneğin oluşturulduğu zaman. ISO 8601 genişletilmiş gösterimini kullanır. |
| **`lastUpdatedTime`** | string    | Örneğin son kalıcı olduğu zaman. ISO 8601 genişletilmiş gösterimini kullanır. |
| **`historyEvents`**   | JSON      | Orchestration yürütme geçmişini içeren bir JSON dizisi. Bu alan, `null` `showHistory` sorgu dizesi parametresi olarak ayarlanmadığı müddetçe olur `true` . |

Orchestration yürütme geçmişi ve etkinlik çıkışları (okunabilirlik için biçimlendirilir) dahil olmak üzere örnek bir yanıt yükü aşağıda verilmiştir:

```json
{
  "createdTime": "2018-02-28T05:18:49Z",
  "historyEvents": [
      {
          "EventType": "ExecutionStarted",
          "FunctionName": "E1_HelloSequence",
          "Timestamp": "2018-02-28T05:18:49.3452372Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello Tokyo!",
          "ScheduledTime": "2018-02-28T05:18:51.3939873Z",
          "Timestamp": "2018-02-28T05:18:52.2895622Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello Seattle!",
          "ScheduledTime": "2018-02-28T05:18:52.8755705Z",
          "Timestamp": "2018-02-28T05:18:53.1765771Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello London!",
          "ScheduledTime": "2018-02-28T05:18:53.5170791Z",
          "Timestamp": "2018-02-28T05:18:53.891081Z"
      },
      {
          "EventType": "ExecutionCompleted",
          "OrchestrationStatus": "Completed",
          "Result": [
              "Hello Tokyo!",
              "Hello Seattle!",
              "Hello London!"
          ],
          "Timestamp": "2018-02-28T05:18:54.3660895Z"
      }
  ],
  "input": null,
  "customStatus": { "nextActions": ["A", "B", "C"], "foo": 2 },
  "lastUpdatedTime": "2018-02-28T05:18:54Z",
  "output": [
      "Hello Tokyo!",
      "Hello Seattle!",
      "Hello London!"
  ],
  "runtimeStatus": "Completed"
}
```

**HTTP 202** yanıtı Ayrıca, daha önce bahsedilen alanla aynı URL 'ye başvuran bir **konum** yanıt üst bilgisi içerir `statusQueryGetUri` .

## <a name="get-all-instances-status"></a>Tüm örneklerin durumunu al

`instanceId`' Örnek durumunu Al ' isteğinden öğesini kaldırarak tüm örneklerin durumunu da sorgulayabilirsiniz. Bu durumda, temel parametreler ' Get Instance Status ' ile aynıdır. Filtreleme için sorgu dizesi parametreleri de desteklenir.

Anımsanması gereken tek şey, `connection` ve `code` isteğe bağlıdır. İşlev üzerinde anonim kimlik doğrulaması varsa, `code` gerekli değildir.
AzureWebJobsStorage uygulama ayarında tanımlı dışında farklı bir depolama bağlantı dizesi kullanmak istemiyorsanız, bağlantı sorgusu dize parametresini güvenle yoksayabilirsiniz.

### <a name="request"></a>İstek

Işlevler çalışma zamanının 1. x sürümü için, istek aşağıdaki gibi biçimlendirilir (açıklık için birden fazla satır gösterilir):

```http
GET /admin/extensions/DurableTaskExtension/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
    &showInput=[true|false]
    &top={integer}
```

Işlevler çalışma zamanının 2. x sürümünde, URL biçimi aynı parametrelere sahip ancak biraz farklı bir ön eke sahiptir:

```http
GET /runtime/webhooks/durableTask/instances?
    taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
    &showInput=[true|false]
    &top={integer}
```

Bu API için istek parametreleri, daha önce belirtilen varsayılan kümeyi ve aşağıdaki benzersiz parametreleri içerir:

| Alan                   | Parametre türü  | Açıklama |
|-------------------------|-----------------|-------------|
| **`instanceId`**        | URL             | Orchestration örneğinin KIMLIĞI. |
| **`showInput`**         | Sorgu dizesi    | İsteğe bağlı parametre. Olarak ayarlanırsa `false` , işlev girişi yanıt yüküne dahil edilmez.|
| **`showHistory`**       | Sorgu dizesi    | İsteğe bağlı parametre. Olarak ayarlanırsa `true` , Orchestration yürütme geçmişi yanıt yüküne dahil edilir.|
| **`showHistoryOutput`** | Sorgu dizesi    | İsteğe bağlı parametre. Olarak ayarlanırsa `true` , işlev çıkışları Orchestration yürütme geçmişine dahil edilir.|
| **`createdTimeFrom`**   | Sorgu dizesi    | İsteğe bağlı parametre. Belirtildiğinde, belirtilen ıSO8601 zaman damgasında veya bundan sonra oluşturulan döndürülen örneklerin listesini filtreler.|
| **`createdTimeTo`**     | Sorgu dizesi    | İsteğe bağlı parametre. Belirtildiğinde, belirtilen ıSO8601 zaman damgasında veya daha önce oluşturulan döndürülen örneklerin listesini filtreler.|
| **`runtimeStatus`**     | Sorgu dizesi    | İsteğe bağlı parametre. Belirtildiğinde, döndürülen örneklerin listesini çalışma zamanı durumlarına göre filtreler. Olası çalışma zamanı durum değerlerinin listesini görmek için, [örnekleri sorgulama](durable-functions-instance-management.md) makalesine bakın. |
| **`top`**               | Sorgu dizesi    | İsteğe bağlı parametre. Belirtildiğinde, sorgu tarafından döndürülen örneklerin sayısını kısıtlar. |

### <a name="response"></a>Yanıt

Düzenleme durumu da dahil olmak üzere yanıt yükleri örneği aşağıda verilmiştir (okunabilirlik için biçimlendirilir):

```json
[
    {
        "instanceId": "7af46ff000564c65aafbfe99d07c32a5",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2018-06-04T10:46:39Z",
        "lastUpdatedTime": "2018-06-04T10:46:47Z"
    },
    {
        "instanceId": "80eb7dd5c22f4eeba9f42b062794321e",
        "runtimeStatus": "Running",
        "input": null,
        "customStatus": null,
        "output": null,
        "createdTime": "2018-06-04T15:18:28Z",
        "lastUpdatedTime": "2018-06-04T15:18:38Z"
    },
    {
        "instanceId": "9124518926db408ab8dfe84822aba2b1",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2018-06-04T10:46:54Z",
        "lastUpdatedTime": "2018-06-04T10:47:03Z"
    },
    {
        "instanceId": "d100b90b903c4009ba1a90868331b11b",
        "runtimeStatus": "Pending",
        "input": null,
        "customStatus": null,
        "output": null,
        "createdTime": "2018-06-04T15:18:39Z",
        "lastUpdatedTime": "2018-06-04T15:18:39Z"
    }
]
```

> [!NOTE]
> Örnekler tablosunda çok sayıda satır varsa, bu işlem Azure Storage g/ç açısından çok pahalı olabilir. Örnek tablosu hakkında daha fazla ayrıntı, [dayanıklı işlevler (Azure işlevleri) belgelerinde performans ve ölçek](durable-functions-perf-and-scale.md#instances-table) bölümünde bulunabilir.
>

Daha fazla sonuç varsa, yanıt üst bilgisinde bir devamlılık belirteci döndürülür.  Üstbilginin adı `x-ms-continuation-token` .

Sonraki istek üstbilgisinde devamlılık belirteci değerini ayarlarsanız, sonraki sonuç sayfasına ulaşabilirsiniz. İstek üst bilgisinin bu adı da vardır `x-ms-continuation-token` .

## <a name="purge-single-instance-history"></a>Tek örnek geçmişini temizle

Belirtilen bir düzenleme örneği için geçmişi ve ilgili yapıtları siler.

### <a name="request"></a>İstek

Işlevler çalışma zamanının 1. x sürümü için, istek aşağıdaki gibi biçimlendirilir (açıklık için birden fazla satır gösterilir):

```http
DELETE /admin/extensions/DurableTaskExtension/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connection}
    &code={systemKey}
```

Işlevler çalışma zamanının 2. x sürümünde, URL biçimi aynı parametrelere sahip ancak biraz farklı bir ön eke sahiptir:

```http
DELETE /runtime/webhooks/durabletask/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connection}
    &code={systemKey}
```

Bu API için istek parametreleri, daha önce belirtilen varsayılan kümeyi ve aşağıdaki benzersiz parametreleri içerir:

| Alan             | Parametre türü  | Açıklama |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | Orchestration örneğinin KIMLIĞI. |

### <a name="response"></a>Yanıt

Aşağıdaki HTTP durum kodu değerleri döndürülebilir.

* **HTTP 200 (Tamam)**: örnek geçmişi başarıyla temizlendi.
* **HTTP 404 (bulunamadı)**: belirtilen örnek yok.

**HTTP 200** Case için yanıt yükü aşağıdaki alanı IÇEREN bir JSON nesnesidir:

| Alan                  | Veri türü | Açıklama |
|------------------------|-----------|-------------|
| **`instancesDeleted`** | tamsayı   | Silinen örneklerin sayısı. Tek örnekli durum için bu değer her zaman olmalıdır `1` . |

Örnek bir yanıt yükü (okunabilirlik için biçimlendirilir) aşağıda verilmiştir:

```json
{
    "instancesDeleted": 1
}
```

## <a name="purge-multiple-instance-histories"></a>Birden çok örnek geçmişlerini temizle

Ayrıca, `{instanceId}` ' tek örnekli geçmiş ' isteğinden ' ı kaldırarak bir görev hub 'ında bulunan birden çok örnek için geçmişi ve ilgili yapıları silebilirsiniz. Örnek geçmişini seçmeli olarak temizlemek için ' tüm örnekleri al ' isteğinde açıklanan filtreleri kullanın.

### <a name="request"></a>İstek

Işlevler çalışma zamanının 1. x sürümü için, istek aşağıdaki gibi biçimlendirilir (açıklık için birden fazla satır gösterilir):

```http
DELETE /admin/extensions/DurableTaskExtension/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
```

Işlevler çalışma zamanının 2. x sürümünde, URL biçimi aynı parametrelere sahip ancak biraz farklı bir ön eke sahiptir:

```http
DELETE /runtime/webhooks/durabletask/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
```

Bu API için istek parametreleri, daha önce belirtilen varsayılan kümeyi ve aşağıdaki benzersiz parametreleri içerir:

| Alan                 | Parametre türü  | Description |
|-----------------------|-----------------|-------------|
| **`createdTimeFrom`** | Sorgu dizesi    | Verilen ıSO8601 zaman damgasında veya bundan sonra oluşturulan temizlenen örneklerin listesini filtreler.|
| **`createdTimeTo`**   | Sorgu dizesi    | İsteğe bağlı parametre. Belirtildiğinde, belirtilen ıSO8601 zaman damgasında veya daha önce oluşturulan temizlenen örneklerin listesini filtreler.|
| **`runtimeStatus`**   | Sorgu dizesi    | İsteğe bağlı parametre. Belirtildiğinde, temizlenen örneklerin listesini çalışma zamanı durumlarına göre filtreler. Olası çalışma zamanı durum değerlerinin listesini görmek için, [örnekleri sorgulama](durable-functions-instance-management.md) makalesine bakın. |

> [!NOTE]
> Örneklerde ve/veya geçmiş tablolarında çok sayıda satır varsa, bu işlem Azure Storage g/ç açısından çok pahalı olabilir. Bu tablolar hakkında daha fazla ayrıntı, [dayanıklı işlevler (Azure işlevleri) belgelerinde performans ve ölçek](durable-functions-perf-and-scale.md#instances-table) bölümünde bulunabilir.

### <a name="response"></a>Yanıt

Aşağıdaki HTTP durum kodu değerleri döndürülebilir.

* **HTTP 200 (Tamam)**: örnek geçmişi başarıyla temizlendi.
* **HTTP 404 (bulunamadı)**: filtre ifadesiyle eşleşen hiçbir örnek bulunamadı.

**HTTP 200** Case için yanıt yükü aşağıdaki alanı IÇEREN bir JSON nesnesidir:

| Alan                   | Veri türü | Açıklama |
|-------------------------|-----------|-------------|
| **`instancesDeleted`**  | tamsayı   | Silinen örneklerin sayısı. |

Örnek bir yanıt yükü (okunabilirlik için biçimlendirilir) aşağıda verilmiştir:

```json
{
    "instancesDeleted": 250
}
```

## <a name="raise-event"></a>Olay oluştur

Çalışan bir düzenleme örneğine bir olay bildirim iletisi gönderir.

### <a name="request"></a>İstek

Işlevler çalışma zamanının 1. x sürümü için, istek aşağıdaki gibi biçimlendirilir (açıklık için birden fazla satır gösterilir):

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/raiseEvent/{eventName}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

Işlevler çalışma zamanının 2. x sürümünde, URL biçimi aynı parametrelere sahip ancak biraz farklı bir ön eke sahiptir:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/raiseEvent/{eventName}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

Bu API için istek parametreleri, daha önce belirtilen varsayılan kümeyi ve aşağıdaki benzersiz parametreleri içerir:

| Alan             | Parametre türü  | Açıklama |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | Orchestration örneğinin KIMLIĞI. |
| **`eventName`**   | URL             | Hedef düzenleme örneğinin beklediği olayın adı. |
| **`{content}`**   | İstek içeriği | JSON biçimli olay yükü. |

### <a name="response"></a>Yanıt

Olası birkaç durum kodu değeri döndürülebilir.

* **HTTP 202 (kabul edildi)**: oluşturulan olay işleme için kabul edildi.
* **HTTP 400 (hatalı istek)**: istek içeriği tür değildi `application/json` veya geçerli bir JSON değildi.
* **HTTP 404 (bulunamadı)**: belirtilen örnek bulunamadı.
* **HTTP 410 (kayıp)**: belirtilen örnek tamamlandı veya başarısız oldu ve herhangi bir oluşturulan olay işlenemiyor.

İşte bir `"incr"` olay adlı **işlem** için bekleyen bir örneğe JSON dizesi gönderen örnek bir istek:

```http
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/raiseEvent/operation?taskHub=DurableFunctionsHub&connection=Storage&code=XXX
Content-Type: application/json
Content-Length: 6

"incr"
```

Bu API 'nin yanıtları herhangi bir içerik içermiyor.

## <a name="terminate-instance"></a>Örneği Sonlandır

Çalışan bir düzenleme örneğini sonlandırır.

### <a name="request"></a>İstek

Işlevler çalışma zamanının 1. x sürümü için, istek aşağıdaki gibi biçimlendirilir (açıklık için birden fazla satır gösterilir):

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/terminate
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

Işlevler çalışma zamanının 2. x sürümünde, URL biçimi aynı parametrelere sahip ancak biraz farklı bir ön eke sahiptir:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/terminate
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

Bu API için istek parametreleri, daha önce belirtilen varsayılan kümeyi ve aşağıdaki benzersiz parametreyi içerir.

| Alan             | Parametre türü  | Açıklama |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | Orchestration örneğinin KIMLIĞI. |
| **`reason`**      | Sorgu dizesi    | İsteğe bağlı. Orchestration örneğini sonlandırma nedeni. |

### <a name="response"></a>Yanıt

Olası birkaç durum kodu değeri döndürülebilir.

* **HTTP 202 (kabul edildi)**: sonlandırma isteği işlenmek üzere kabul edildi.
* **HTTP 404 (bulunamadı)**: belirtilen örnek bulunamadı.
* **HTTP 410 (kayıp)**: belirtilen örnek tamamlandı veya başarısız oldu.

İşte çalışan bir örneği sonlandıran ve **önemlidir** nedenini belirten bir örnek istek aşağıdadır:

```
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/terminate?reason=buggy&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

Bu API 'nin yanıtları herhangi bir içerik içermiyor.

## <a name="rewind-instance-preview"></a>Geri sarma örneği (Önizleme)

Hatalı düzenleme örneğini, en son başarısız işlemleri yeniden gerçekleştirerek çalışır duruma geri yükler.

### <a name="request"></a>İstek

Işlevler çalışma zamanının 1. x sürümü için, istek aşağıdaki gibi biçimlendirilir (açıklık için birden fazla satır gösterilir):

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/rewind
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

Işlevler çalışma zamanının 2. x sürümünde, URL biçimi aynı parametrelere sahip ancak biraz farklı bir ön eke sahiptir:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/rewind
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

Bu API için istek parametreleri, daha önce belirtilen varsayılan kümeyi ve aşağıdaki benzersiz parametreyi içerir.

| Alan             | Parametre türü  | Açıklama |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | Orchestration örneğinin KIMLIĞI. |
| **`reason`**      | Sorgu dizesi    | İsteğe bağlı. Orchestration örneğini yeniden sargı nedeni. |

### <a name="response"></a>Yanıt

Olası birkaç durum kodu değeri döndürülebilir.

* **HTTP 202 (kabul edildi)**: işleme için geri sarma isteği kabul edildi.
* **HTTP 404 (bulunamadı)**: belirtilen örnek bulunamadı.
* **HTTP 410 (kayıp)**: belirtilen örnek tamamlandı veya sonlandırıldı.

Aşağıda, başarısız bir örneği yeniden denetleyen ve bir **sabit** neden belirten örnek bir istek verilmiştir:

```http
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/rewind?reason=fixed&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

Bu API 'nin yanıtları herhangi bir içerik içermiyor.

## <a name="signal-entity"></a>Sinyal varlığı

[Dayanıklı bir varlığa](durable-functions-types-features-overview.md#entity-functions)tek yönlü bir işlem iletisi gönderir. Varlık yoksa, otomatik olarak oluşturulur.

> [!NOTE]
> Dayanıklı varlıklar Dayanıklı İşlevler 2,0 ' den başlayarak kullanılabilir.

### <a name="request"></a>İstek

HTTP isteği aşağıdaki gibi biçimlendirilir (açıklık için birden fazla satır gösterilir):

```http
POST /runtime/webhooks/durabletask/entities/{entityName}/{entityKey}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &op={operationName}
```

Bu API için istek parametreleri, daha önce belirtilen varsayılan kümeyi ve aşağıdaki benzersiz parametreleri içerir:

| Alan             | Parametre türü  | Açıklama |
|-------------------|-----------------|-------------|
| **`entityName`**  | URL             | Varlığın adı (türü). |
| **`entityKey`**   | URL             | Varlığın anahtarı (benzersiz KIMLIĞI). |
| **`op`**          | Sorgu dizesi    | İsteğe bağlı. Çağrılacak Kullanıcı tanımlı işlemin adı. |
| **`{content}`**   | İstek içeriği | JSON biçimli olay yükü. |

Adlı bir varlığa Kullanıcı tanımlı "Ekle" iletisi gönderen örnek bir istek aşağıda verilmiştir `Counter` `steps` . İletinin içeriği değerdir `5` . Varlık zaten mevcut değilse, bu istek tarafından oluşturulacaktır:

```http
POST /runtime/webhooks/durabletask/entities/Counter/steps?op=Add
Content-Type: application/json

5
```

> [!NOTE]
> Varsayılan olarak, [.net 'teki sınıf tabanlı varlıklarla](durable-functions-dotnet-entities.md#defining-entity-classes), `op` değerini belirtmek `delete` bir varlığın durumunu siler. Varlık adlı bir işlemi tanımlıyorsa, `delete` ancak bunun yerine Kullanıcı tanımlı işlem çağrılacaktır.

### <a name="response"></a>Yanıt

Bu işlemin birkaç olası yanıtı vardır:

* **HTTP 202 (kabul edildi)**: sinyal işlemi zaman uyumsuz işleme için kabul edildi.
* **HTTP 400 (hatalı istek)**: istek içeriği türü değildi `application/json` , geçerli bir JSON değildi veya geçersiz bir `entityKey` değere sahipti.
* **HTTP 404 (bulunamadı)**: belirtilen `entityName` bulunamadı.

Başarılı bir HTTP isteği yanıtta içerik içermiyor. Başarısız bir HTTP isteği, yanıt içeriğinde JSON biçimli hata bilgileri içerebilir.

## <a name="get-entity"></a>Varlığı al

Belirtilen varlığın durumunu alır.

### <a name="request"></a>İstek

HTTP isteği aşağıdaki gibi biçimlendirilir (açıklık için birden fazla satır gösterilir):

```http
GET /runtime/webhooks/durabletask/entities/{entityName}/{entityKey}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

### <a name="response"></a>Yanıt

Bu işlemin iki olası yanıtı vardır:

* **HTTP 200 (Tamam)**: belirtilen varlık var.
* **HTTP 404 (bulunamadı)**: belirtilen varlık bulunamadı.

Başarılı bir yanıt, varlığın içeriği olarak JSON seri hale getirilmiş durumunu içerir.

### <a name="example"></a>Örnek
Aşağıdaki örnek HTTP isteği adlı var olan bir varlığın durumunu alır `Counter` `steps` :

```http
GET /runtime/webhooks/durabletask/entities/Counter/steps
```

`Counter`Varlık bir alana kaydedilmiş birkaç adım içeriyorsa `currentValue` , yanıt içeriği aşağıdaki gibi görünebilir (okunabilirlik için biçimlendirilir):

```json
{
    "currentValue": 5
}
```

## <a name="list-entities"></a>Liste varlıkları

Varlık adına veya son işlem tarihine göre birden çok varlık için sorgulama yapabilirsiniz.

### <a name="request"></a>İstek

HTTP isteği aşağıdaki gibi biçimlendirilir (açıklık için birden fazla satır gösterilir):

```http
GET /runtime/webhooks/durabletask/entities/{entityName}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &lastOperationTimeFrom={timestamp}
    &lastOperationTimeTo={timestamp}
    &fetchState=[true|false]
    &top={integer}
```

Bu API için istek parametreleri, daha önce belirtilen varsayılan kümeyi ve aşağıdaki benzersiz parametreleri içerir:

| Alan                       | Parametre türü  | Açıklama |
|-----------------------------|-----------------|-------------|
| **`entityName`**            | URL             | İsteğe bağlı. Belirtildiğinde, döndürülen varlıkların listesini varlık adlarıyla (büyük/küçük harfe duyarsız) filtreler. |
| **`fetchState`**            | Sorgu dizesi    | İsteğe bağlı parametre. Olarak ayarlanırsa `true` , varlık durumu yanıt yüküne dahil edilir. |
| **`lastOperationTimeFrom`** | Sorgu dizesi    | İsteğe bağlı parametre. Belirtildiğinde, belirtilen ıSO8601 zaman damgasından sonra işlemleri işleyen döndürülen varlıkların listesine filtre uygular. |
| **`lastOperationTimeTo`**   | Sorgu dizesi    | İsteğe bağlı parametre. Belirtildiğinde, belirtilen ıSO8601 zaman damgasından önce işlemleri işleyen döndürülen varlıkların listesine filtre uygular. |
| **`top`**                   | Sorgu dizesi    | İsteğe bağlı parametre. Belirtildiğinde, sorgu tarafından döndürülen varlıkların sayısını kısıtlar. |


### <a name="response"></a>Yanıt

Başarılı bir HTTP 200 yanıtı, JSON seri hale getirilmiş bir varlık dizisi ve isteğe bağlı olarak her bir varlığın durumunu içerir.

Varsayılan olarak işlem, sorgu ölçütleriyle eşleşen ilk 100 varlığı döndürür. Çağıran, `top` farklı sayıda sonuç döndürmek için bir sorgu dizesi parametre değeri belirtebilir. Döndürülmeden daha fazla sonuç varsa, yanıt üst bilgisinde bir devamlılık belirteci de döndürülür. Üstbilginin adı `x-ms-continuation-token` .

Sonraki istek üstbilgisinde devamlılık belirteci değerini ayarlarsanız, sonraki sonuç sayfasına ulaşabilirsiniz. İstek üst bilgisinin bu adı da vardır `x-ms-continuation-token` .

### <a name="example---list-all-entities"></a>Örnek-tüm varlıkları Listele

Aşağıdaki örnek HTTP isteği, görev hub 'ındaki tüm varlıkları listeler:

```http
GET /runtime/webhooks/durabletask/entities
```

JSON yanıtı aşağıdakine benzeyebilir (okunabilirlik için biçimlendirilir):

```json
[
    {
        "entityId": { "key": "cats", "name": "counter" },
        "lastOperationTime": "2019-12-18T21:45:44.6326361Z",
    },
    {
        "entityId": { "key": "dogs", "name": "counter" },
        "lastOperationTime": "2019-12-18T21:46:01.9477382Z"
    },
    {
        "entityId": { "key": "mice", "name": "counter" },
        "lastOperationTime": "2019-12-18T21:46:15.4626159Z"
    },
    {
        "entityId": { "key": "radio", "name": "device" },
        "lastOperationTime": "2019-12-18T21:46:18.2616154Z"
    },
]
```

### <a name="example---filtering-the-list-of-entities"></a>Örnek-varlık listesini filtreleme

Aşağıdaki örnek HTTP isteği, türündeki yalnızca ilk iki varlığı listeler `counter` ve ayrıca durumlarını getirir:

```http
GET /runtime/webhooks/durabletask/entities/counter?top=2&fetchState=true
```

JSON yanıtı aşağıdakine benzeyebilir (okunabilirlik için biçimlendirilir):

```json
[
    {
        "entityId": { "key": "cats", "name": "counter" },
        "lastOperationTime": "2019-12-18T21:45:44.6326361Z",
        "state": { "value": 9 }
    },
    {
        "entityId": { "key": "dogs", "name": "counter" },
        "lastOperationTime": "2019-12-18T21:46:01.9477382Z",
        "state": { "value": 10 }
    }
]
```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Application Insights kullanarak dayanıklı işlevlerinizi nasıl izleyeceğinizi öğrenin](durable-functions-diagnostics.md)
