---
title: Dayanıklı düzenlemeler-Azure Işlevleri
description: Azure Dayanıklı İşlevler düzenleme özelliğine giriş.
author: cgillum
ms.topic: overview
ms.date: 09/08/2019
ms.author: azfuncdf
ms.openlocfilehash: ba314963058389e171601407ff00411049eecd45
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97845426"
---
# <a name="durable-orchestrations"></a>Dayanıklı düzenlemeler

Dayanıklı İşlevler, [Azure işlevleri](../functions-overview.md)'nin bir uzantısıdır. Bir işlev uygulaması içindeki diğer dayanıklı işlevlerin yürütülmesini düzenlemek için bir *Orchestrator işlevi* kullanabilirsiniz. Orchestrator işlevleri aşağıdaki özelliklere sahiptir:

* Orchestrator işlevleri, yordamsal kodu kullanarak işlev iş akışlarını tanımlar. Bildirime dayalı şemalar veya tasarımcı gerekmez.
* Orchestrator işlevleri, diğer dayanıklı işlevleri eşzamanlı ve zaman uyumsuz olarak çağırabilir. Çağrılan işlevlerden alınan çıkış, yerel değişkenlere güvenilir bir şekilde kaydedilebilir.
* Orchestrator işlevleri dayanıklı ve güvenilirdir. "Await" veya "The" işlevi, yürütme ilerlemesi otomatik olarak onay işareti oluşturur. İşlem geri dönüştürüldüğünde veya VM yeniden başlatıldığında yerel durum hiçbir zaman kaybedilmez.
* Orchestrator işlevleri uzun çalışıyor olabilir. Bir *Orchestration örneğinin* toplam kullanım ömrü saniye, gün, ay veya hiçbir şekilde bitmez.

Bu makale, Orchestrator işlevlerine genel bakış ve bunların çeşitli uygulama geliştirme sorunlarını çözmenize nasıl yardımcı olabilecekleri hakkında bilgiler sunar. Dayanıklı İşlevler bir uygulamada kullanılabilen işlevlerin türleri hakkında bilginiz yoksa, önce [dayanıklı işlev türleri](durable-functions-types-features-overview.md) makalesini okuyun.

## <a name="orchestration-identity"></a>Düzenleme kimliği

Bir Orchestration 'un her *örneğinin* bir örnek tanımlayıcısı vardır ( *örnek kimliği* olarak da bilinir). Varsayılan olarak, her örnek KIMLIĞI bir otomatik oluşturulan GUID 'dir. Ancak, örnek kimlikleri Kullanıcı tarafından oluşturulan herhangi bir dize değeri de olabilir. Her orchestration örneği KIMLIĞI, bir [Görev Merkezi](durable-functions-task-hubs.md)içinde benzersiz olmalıdır.

Örnek kimlikleri hakkında bazı kurallar aşağıda verilmiştir:

* Örnek kimlikleri 1 ile 256 karakter arasında olmalıdır.
* Örnek kimlikleri ile başlamamalıdır `@` .
* Örnek kimlikleri,,, `/` `\` `#` veya `?` karakterlerinden oluşmalıdır.
* Örnek kimlikleri denetim karakterleri içermemelidir.

> [!NOTE]
> Mümkün olan her durumda otomatik olarak oluşturulan örnek kimliklerini kullanmanız önerilir. Kullanıcı tarafından oluşturulan örnek kimlikleri, bir düzenleme örneği ve bir satınalma siparişi veya belge gibi bir dış uygulamaya özgü varlık arasında bire bir eşleme olduğu senaryolar için tasarlanmıştır.

Bir Orchestration örnek KIMLIĞI, çoğu [Örnek Yönetimi işlemi](durable-functions-instance-management.md)için gerekli bir parametredir. Bunlar, sorun giderme veya analiz amaçları için Application Insights [düzenleme izleme verilerini arama](durable-functions-diagnostics.md#application-insights) gibi tanılama için de önemlidir. Bu nedenle, üretilen örnek kimliklerini daha sonra kolayca başvurulabilecekleri bir dış konuma (örneğin, bir veritabanı veya uygulama günlüklerinde) kaydetmeniz önerilir.

## <a name="reliability"></a>Güvenilirlik

Orchestrator işlevleri, yürütme durumlarını [olay](/azure/architecture/patterns/event-sourcing) kaynağını belirleme tasarım düzenini kullanarak güvenilir bir şekilde korur. Bir Orchestration 'un geçerli durumunu doğrudan depolamak yerine, dayanıklı görev çerçevesi, bir yalnızca bir Append deposu kullanarak, düzenleme işlevinin aldığı tüm eylem serisini kaydeder. Yalnızca bir Append deposunda, tam çalışma zamanı durumunun "dökümünü alma" ile karşılaştırıldığında birçok avantaj bulunur. Avantajlar, performansı, ölçeklenebilirliği ve yanıt hızını daha da içerir. Ayrıca işlem verileri için nihai tutarlılık ve tam denetim izleri ve geçmişi de alırsınız. Denetim izleri güvenilir telafi eylemlerini destekler.

Dayanıklı İşlevler, saydam olarak olay kaynağını kullanır. Arka planda, `await` bir Orchestrator işlevindeki (C#) veya `yield` (JavaScript/Python) işleci, Orchestrator iş parçacığının denetimini dayanıklı görev çerçevesi dağıtıcısına verir. Dağıtıcı daha sonra Orchestrator işlevinin zamanladığı (bir veya daha fazla alt işlevi çağırma ya da dayanıklı bir Zamanlayıcı zamanlama gibi) tüm yeni eylemleri depolamaya kaydeder. Saydam işleme eylemi Orchestration örneğinin yürütme geçmişine ekler. Geçmiş bir depolama tablosunda depolanır. Sonra Kaydet eylemi, gerçek işi zamanlamak için bir kuyruğa ileti ekler. Bu noktada, Orchestrator işlevi bellekten bellekten kaldırılabilir.

Bir Orchestration işlevine daha fazla iş verildiğinde (örneğin, bir yanıt iletisi alındığında veya dayanıklı Zamanlayıcı sona erdiğinde), Orchestrator başlatılır ve yerel durumu yeniden derlemek için başlangıçtan itibaren tüm işlevi yeniden yürütür. Yeniden yürütme sırasında, kod bir işlevi çağırmaya çalışırsa (veya başka bir zaman uyumsuz çalışma yaparsanız), dayanıklı görev çerçevesi geçerli düzenleme 'nin yürütme geçmişini çağırır. [Etkinlik işlevinin](durable-functions-types-features-overview.md#activity-functions) zaten yürütüldüğünü ve bir sonuç verdiğini belirlerse, bu işlevin sonucunu yeniden yürütür ve Orchestrator kodu çalışmaya devam eder. Yeniden yürütme, işlev kodu tamamlanana kadar veya yeni zaman uyumsuz çalışmayı zamanlana kadar devam eder.

> [!NOTE]
> Yeniden yürütme deseninin doğru ve güvenilir bir şekilde çalışması için Orchestrator işlev kodu *belirleyici* olmalıdır. Orchestrator işlevlerine yönelik kod kısıtlamaları hakkında daha fazla bilgi için bkz. [Orchestrator işlev kodu kısıtlamaları](durable-functions-code-constraints.md) konusu.

> [!NOTE]
> Orchestrator işlevi günlük iletilerini yayar, yeniden yürütme davranışı yinelenen günlük iletilerinin oluşturulmasına neden olabilir. Bu davranışın neden oluştuğu ve geçici olarak nasıl çalışılacağı hakkında daha fazla bilgi edinmek için [günlüğe kaydetme](durable-functions-diagnostics.md#app-logging) konusuna bakın.

## <a name="orchestration-history"></a>Düzenleme geçmişi

Dayanıklı görev çerçevesinin olay kaynağını belirleme davranışı yazdığınız Orchestrator işlevi koduyla yakından ilişkilidir. Aşağıdaki Orchestrator işlevi gibi bir etkinlik zincirleme Orchestrator işlevinizin olduğunu varsayalım:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("E1_HelloSequence")]
public static async Task<List<string>> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var outputs = new List<string>();

    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Tokyo"));
    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Seattle"));
    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "London"));

    // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
    return outputs;
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const output = [];
    output.push(yield context.df.callActivity("E1_SayHello", "Tokyo"));
    output.push(yield context.df.callActivity("E1_SayHello", "Seattle"));
    output.push(yield context.df.callActivity("E1_SayHello", "London"));

    // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
    return output;
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    result1 = yield context.call_activity('SayHello', "Tokyo")
    result2 = yield context.call_activity('SayHello', "Seattle")
    result3 = yield context.call_activity('SayHello', "London")
    return [result1, result2, result3]

main = df.Orchestrator.create(orchestrator_function)
```
---

Her `await` (C#) veya `yield` (JavaScript/Python) bildiriminde, dayanıklı görev çerçevesi, işlevin yürütme durumunu, bazı dayanıklı depolama arka ucuna (genellikle Azure Tablo Depolaması) kontrol noktaları. Bu durum, *düzenleme geçmişi* olarak adlandırılan şeydir.

### <a name="history-table"></a>Geçmiş tablosu

Genellikle, dayanıklı görev çerçevesi her denetim noktasında aşağıdakileri yapar:

1. Yürütme geçmişini Azure depolama tablolarına kaydeder.
2. Orchestrator 'ın çağırmak istediği işlevlere yönelik iletileri sıraya alır.
3. Orchestrator için iletileri &mdash; , dayanıklı Zamanlayıcı iletileri gibi kuyruğa alır.

Kontrol noktası tamamlandıktan sonra Orchestrator işlevi, kendisi için daha fazla iş tamamlanana kadar bellekten kaldırılabilir.

> [!NOTE]
> Azure depolama, verileri tablo depolama ve kuyruklara kaydetme arasında herhangi bir işlem garantisi sağlamaz. Dayanıklı İşlevler depolama sağlayıcısı, sorunları işlemek için *nihai tutarlılık* düzenlerini kullanır. Bu desenler, bir denetim noktasının ortasında kilitlenme veya bağlantı kaybı olursa hiçbir veri kaybolmamasını önler.

Tamamlandıktan sonra, daha önce gösterilen işlevin geçmişi, Azure Tablo Depolaması 'nda aşağıdaki tabloya benzer bir şekilde görünür (çizim amaçları için kısaltılmış):

| PartitionKey (InstanceId)                     | Olay türü             | Timestamp               | Giriş | Name             | Sonuç                                                    | Durum |
|----------------------------------|-----------------------|----------|--------------------------|-------|------------------|-----------------------------------------------------------|
| eaee885b | ExecutionStarted      | 2017-05-05T18:45:28.852 Z | null  | E1_HelloSequence |                                                           |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:32.362 Z |       |                  |                                                           |                     |
| eaee885b | Taskzamanlandı         | 2017-05-05T18:45:32.670 Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:32.670 Z |       |                  |                                                           |                     |
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.201 Z |       |                  | "" "Merhaba Tokyo!" ""                                        |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:34.232 Z |       |                  |                                                           |                     |
| eaee885b | Taskzamanlandı         | 2017-05-05T18:45:34.435 Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:34.435 Z |       |                  |                                                           |                     |
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.763 Z |       |                  | "" "Merhaba Seattle!" ""                                      |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:34.857 Z |       |                  |                                                           |                     |
| eaee885b | Taskzamanlandı         | 2017-05-05T18:45:34.857 Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:34.857 Z |       |                  |                                                           |                     |
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.919 Z |       |                  | "" "Merhaba Londra!" ""                                       |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:35.032 Z |       |                  |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:35.044 Z |       |                  |                                                           |                     |
| eaee885b | ExecutionCompleted    | 2017-05-05T18:45:35.044 Z |       |                  | "[" "Merhaba Tokyo!" "," "Merhaba Seattle!" "," "Merhaba Londra!" "]" | Tamamlandı           |

Sütun değerlerinde birkaç Not:

* **Partitionkey**: Orchestration 'un örnek kimliğini içerir.
* **EventType**: olayın türünü temsil eder. Aşağıdaki türlerden biri olabilir:
  * **Orchestrationstarted**: Orchestrator işlevi bir await 'den devam ettirildi veya ilk kez çalışıyor. `Timestamp`Sütunu `CurrentUtcDateTime` (.net), `currentUtcDateTime` (JavaScript) ve `current_utc_datetime` (Python) API 'lerinin belirleyici değerini doldurmak için kullanılır.
  * **Executionstarted**: Orchestrator işlevi ilk kez yürütülmeye başladı. Bu olay sütundaki işlev girişini de içerir `Input` .
  * **Taskzamanlandı**: bir etkinlik işlevi zamanlandı. Etkinlik işlevinin adı `Name` sütununda yakalanır.
  * **TaskCompleted**: etkinlik işlevi tamamlandı. İşlevin sonucu `Result` sütununda.
  * **Timercreated**: dayanıklı bir Zamanlayıcı oluşturuldu. `FireAt`Sütun, zamanlayıcının süresinin dolacağı ZAMANLANMıŞ UTC saatini içerir.
  * **Timertetiklenme**: bir dayanıklı süreölçer tetiklendi.
  * **Eventraıda**: Orchestration örneğine bir dış olay gönderildi. `Name`Sütun, olayın adını yakalar ve `Input` sütun, olayın yükünü yakalar.
  * **Orchestratorcompleted**: Orchestrator işlevi bekletildi.
  * **Continueasnew**: Orchestrator işlevi tamamlandı ve yeni durumla birlikte yeniden başlatıldı. `Result`Sütunu, yeniden başlatılan örnekte girdi olarak kullanılan değerini içerir.
  * **Executioncompleted**: Orchestrator işlevi tamamlanana (veya başarısız) çalıştı. İşlevin çıkışları veya hata ayrıntıları `Result` sütununda depolanır.
* **Zaman damgası**: geçmış olayının UTC zaman damgası.
* **Ad**: çağrılan işlevin adı.
* **Giriş**: işlevin JSON biçimli girişi.
* **Sonuç**: işlevin çıktısı; diğer bir deyişle, dönüş değeridir.

> [!WARNING]
> Hata ayıklama aracı olarak yararlı olsa da, bu tablo üzerinde hiçbir bağımlılığı olmaz. Dayanıklı İşlevler uzantısı geliştikçe değişiklik gösterebilir.

İşlev bir `await` (C#) veya `yield` (JavaScript/Python) ' den her başlatıldığında, dayanıklı görev çerçevesi Orchestrator işlevini sıfırdan yeniden çalıştırır. Her yeniden çalıştırmada, geçerli zaman uyumsuz işlemin yapılıp yapılmayacağını belirlemede yürütme geçmişine bakar.  İşlem gerçekleştiyse, çerçeve bu işlemin çıkışını hemen yeniden yürütür ve bir sonrakine `await` (C#) veya `yield` (JavaScript/Python) gider. Bu işlem, tüm geçmiş yeniden yürütülene kadar devam eder. Geçerli geçmiş yeniden yürütüldüğünde, yerel değişkenler önceki değerlerine geri yüklenmiş olur.

## <a name="features-and-patterns"></a>Özellikler ve desenler

Sonraki bölümlerde Orchestrator işlevlerinin özellikleri ve desenleri açıklanır.

### <a name="sub-orchestrations"></a>Alt düzenlemeler

Orchestrator işlevleri, etkinlik işlevlerini, diğer Orchestrator işlevlerini de çağırabilir. Örneğin, bir Orchestrator işlevleri kitaplığından daha büyük bir düzenleme oluşturabilirsiniz. Ya da, bir Orchestrator işlevinin birden fazla örneğini paralel olarak çalıştırabilirsiniz.

Daha fazla bilgi ve örnekler için bkz. [alt](durable-functions-sub-orchestrations.md) düzenlemeler makalesi.

### <a name="durable-timers"></a>Dayanıklı zamanlayıcılar

Düzenlemeler, gecikmeler uygulamak veya zaman uyumsuz eylemlerde zaman aşımı işlemeyi ayarlamak için *sürekli zamanlayıcılar* zamanlayabilir. `Thread.Sleep`Ve `Task.Delay` (C#) veya ( `setTimeout()` `setInterval()` JavaScript) veya ( `time.sleep()` Python) yerine Orchestrator işlevlerinde dayanıklı zamanlayıcılar kullanın.

Daha fazla bilgi ve örnekler için bkz. [dayanıklı zamanlayıcılar](durable-functions-timers.md) makalesi.

### <a name="external-events"></a>Dış olaylar

Orchestrator işlevleri, dış olayların bir düzenleme örneğini güncelleştirmesini bekleyebilir. Bu Dayanıklı İşlevler özelliği genellikle insan etkileşimini veya diğer dış geri çağırmaları işlemek için yararlıdır.

Daha fazla bilgi ve örnekler için bkz. [dış olaylar](durable-functions-external-events.md) makalesi.

### <a name="error-handling"></a>Hata işleme

Orchestrator işlevleri, programlama dilinin hata işleme özelliklerini kullanabilir. Benzer desenler `try` / `catch` düzenleme kodunda desteklenir.

Orchestrator işlevleri, çağrıduydukları etkinliğe veya alt Orchestrator işlevlerine yeniden deneme ilkeleri de ekleyebilir. Bir etkinlik veya alt Orchestrator işlevi bir özel durumla başarısız olursa, belirtilen yeniden deneme ilkesi, yürütmeyi belirtilen sayıda otomatik olarak erteleyebilir ve yeniden deneyebilir.

> [!NOTE]
> Orchestrator işlevinde işlenmeyen bir özel durum varsa, düzenleme örneği bir `Failed` durumda tamamlanır. Bir Orchestration örneği başarısız olduktan sonra yeniden denenemez.

Daha fazla bilgi ve örnekler için bkz. [hata işleme](durable-functions-error-handling.md) makalesi.

### <a name="critical-sections-durable-functions-2x-currently-net-only"></a>Kritik bölümler (Dayanıklı İşlevler 2. x, şu anda yalnızca .NET)

Düzenleme örnekleri tek iş parçacıklıdır, bu nedenle bir düzenleme *içindeki* yarış koşullarına endişelenmek için gerekli değildir. Ancak, bir yandan dış sistemlerle etkileşim kurarken yarış durumları mümkündür. Dış sistemlerle etkileşim kurarken yarış koşullarını azaltmak için, Orchestrator işlevleri .NET 'teki bir yöntemi kullanarak *kritik bölümleri* tanımlayabilir `LockAsync` .

Aşağıdaki örnek kod, kritik bir bölümü tanımlayan bir Orchestrator işlevini gösterir. Yöntemini kullanarak kritik bölüme girer `LockAsync` . Bu yöntem, bir veya daha fazla başvuruyu dayanıklı bir [varlığa](durable-functions-entities.md)geçirmeyi gerektirir ve bu, kilit durumunu sürekli olarak yönetir. Bu Orchestration 'un yalnızca tek bir örneği, her seferinde kritik bölümde kodu yürütebilir.

```csharp
[FunctionName("Synchronize")]
public static async Task Synchronize(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var lockId = new EntityId("LockEntity", "MyLockIdentifier");
    using (await context.LockAsync(lockId))
    {
        // critical section - only one orchestration can enter at a time
    }
}
```

, `LockAsync` Dayanıklı kilitler elde edin ve `IDisposable` aktiften çıkarıldığı zaman kritik bölümü sonlandırır. Bu `IDisposable` sonuç `using` , kritik bölümün sözdizimsel bir gösterimini almak için bir blokla birlikte kullanılabilir. Orchestrator işlevi kritik bir bölüme girdiğinde yalnızca bir örnek bu kod bloğunu yürütebilir. Kritik bölümü girmeye çalışacak diğer tüm örnekler, önceki örnek kritik bölümden çıkana kadar engellenir.

Kritik bölüm özelliği, dayanıklı varlıklarda yapılan değişiklikleri koordine etmek için de kullanışlıdır. Kritik bölümler hakkında daha fazla bilgi için bkz. [dayanıklı varlıklar "varlık düzenlemesi"](durable-functions-entities.md#entity-coordination) konusu.

> [!NOTE]
> Kritik bölümler Dayanıklı İşlevler 2,0 ve üzeri sürümlerde kullanılabilir. Şu anda bu özelliği yalnızca .NET düzenlemeleri uygular.

### <a name="calling-http-endpoints-durable-functions-2x"></a>HTTP uç noktalarını çağırma (Dayanıklı İşlevler 2. x)

Orchestrator işlevlerinin, [Orchestrator işlev kodu kısıtlamalarında](durable-functions-code-constraints.md)açıklandığı gibi g/ç yapmasına izin verilmez. Bu sınırlamaya yönelik tipik geçici çözüm, bir etkinlik işlevinde g/ç yapması gereken her türlü kodu sarmasıdır. Dış sistemlerle etkileşime geçen düzenlemeler, HTTP çağrıları yapmak ve sonucu düzenlemeye döndürmek için sık sık etkinlik işlevlerini kullanır.

# <a name="c"></a>[C#](#tab/csharp)

Bu ortak kalıbı basitleştirmek için Orchestrator işlevleri `CallHttpAsync` doğrudan HTTP API 'leri çağırmak için yöntemini kullanabilir.

```csharp
[FunctionName("CheckSiteAvailable")]
public static async Task CheckSiteAvailable(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    Uri url = context.GetInput<Uri>();

    // Makes an HTTP GET request to the specified endpoint
    DurableHttpResponse response = 
        await context.CallHttpAsync(HttpMethod.Get, url);

    if (response.StatusCode >= 400)
    {
        // handling of error codes goes here
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const url = context.df.getInput();
    var res = yield context.df.callHttp("GET", url);
    if (res.statusCode >= 400) {
        // handling of error codes goes here
    }
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    url = context.get_input()
    res = yield context.call_http('GET', url)
    if res.status_code >= 400:
        # handing of error code goes here
```
---

Yöntemi, temel istek/yanıt düzenlerini desteklemeye ek olarak, ortak zaman uyumsuz HTTP 202 yoklama desenlerinin otomatik işlemesini destekler ve ayrıca [yönetilen kimlikleri](../../active-directory/managed-identities-azure-resources/overview.md)kullanarak dış hizmetlerle kimlik doğrulamasını destekler.

Daha fazla bilgi ve ayrıntılı örnekler için bkz. [http özellikleri](durable-functions-http-features.md) makalesi.

> [!NOTE]
> HTTP uç noktalarını doğrudan Orchestrator işlevlerinden çağırma Dayanıklı İşlevler 2,0 ve üzeri sürümlerde kullanılabilir.

### <a name="passing-multiple-parameters"></a>Birden çok parametre geçirme

Birden çok parametreyi doğrudan bir etkinlik işlevine geçirmek mümkün değildir. Öneri bir nesne veya bileşik nesne dizisinde geçmektir.

# <a name="c"></a>[C#](#tab/csharp)

.NET ' te, [Valuetuple](/dotnet/csharp/tuples) nesnelerini de kullanabilirsiniz. Aşağıdaki örnek, [C# 7](/dotnet/csharp/whats-new/csharp-7#tuples)Ile eklenen [Valuetuple](/dotnet/csharp/tuples) 'in yeni özelliklerini kullanıyor:

```csharp
[FunctionName("GetCourseRecommendations")]
public static async Task<object> RunOrchestrator(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string major = "ComputerScience";
    int universityYear = context.GetInput<int>();

    object courseRecommendations = await context.CallActivityAsync<object>(
        "CourseRecommendations",
        (major, universityYear));
    return courseRecommendations;
}

[FunctionName("CourseRecommendations")]
public static async Task<object> Mapper([ActivityTrigger] IDurableActivityContext inputs)
{
    // parse input for student's major and year in university
    (string Major, int UniversityYear) studentInfo = inputs.GetInput<(string, int)>();

    // retrieve and return course recommendations by major and university year
    return new
    {
        major = studentInfo.Major,
        universityYear = studentInfo.UniversityYear,
        recommendedCourses = new []
        {
            "Introduction to .NET Programming",
            "Introduction to Linux",
            "Becoming an Entrepreneur"
        }
    };
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

#### <a name="orchestrator"></a>Orchestrator

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const location = {
        city: "Seattle",
        state: "WA"
    };
    const weather = yield context.df.callActivity("GetWeather", location);

    // ...
};
```

#### <a name="getweather-activity"></a>`GetWeather` Etkinlik

```javascript
module.exports = async function (context, location) {
    const {city, state} = location; // destructure properties into variables

    // ...
};
```

# <a name="python"></a>[Python](#tab/python)

#### <a name="orchestrator"></a>Orchestrator

```python
from collections import namedtuple
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    Location = namedtuple('Location', ['city', 'state'])
    location = Location(city='Seattle', state= 'WA')

    weather = yield context.call_activity("GetWeather", location)

    # ...

```
#### <a name="getweather-activity"></a>`GetWeather` Etkinlik

```python
from collections import namedtuple

Location = namedtuple('Location', ['city', 'state'])

def main(location: Location) -> str:
    city, state = location
    return f"Hello {city}, {state}!"
```

---

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Düzenleyici kodu kısıtlamaları](durable-functions-code-constraints.md)
