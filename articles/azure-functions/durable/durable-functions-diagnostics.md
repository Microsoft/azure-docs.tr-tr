---
title: Dayanıklı İşlevler tanılama-Azure
description: Azure Işlevleri için Dayanıklı İşlevler uzantısıyla ilgili sorunları tanılamayı öğrenin.
author: cgillum
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: azfuncdf
ms.openlocfilehash: 62cc5e1762a2a54b26cbebae5aa7cfbf64204ba5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100584618"
---
# <a name="diagnostics-in-durable-functions-in-azure"></a>Azure'da Dayanıklı İşlevler'de Tanılama

[Dayanıklı işlevler](durable-functions-overview.md)sorunları tanılamaya yönelik çeşitli seçenekler vardır. Bu seçeneklerden bazıları normal işlevlerle aynıdır ve bazıları da Dayanıklı İşlevler için benzersizdir.

## <a name="application-insights"></a>Application Insights

[Application Insights](../../azure-monitor/app/app-insights-overview.md) , Azure işlevlerinde tanılama ve izleme yapmak için önerilen yoldur. Aynı Dayanıklı İşlevler için de geçerlidir. İşlev uygulamanızda Application Insights nasıl yararlanacağınızı gösteren bir genel bakış için bkz. [Azure Işlevlerini izleme](../functions-monitoring.md).

Azure Işlevleri dayanıklı uzantısı, bir Orchestration 'un uçtan uca yürütülmesini izlemenize imkan tanıyan *olayları izleme* özelliği de yayar. Bu izleme olayları, Azure portal [Application Insights Analytics](../../azure-monitor/logs/log-query-overview.md) Aracı kullanılarak bulunabilir ve sorgulanır.

### <a name="tracking-data"></a>İzleme verileri

Bir Orchestration örneğinin her yaşam döngüsü olayı, bir izleme olayının Application Insights **İziz** koleksiyonuna yazılmasına neden olur. Bu olay, birkaç alan içeren bir **Customdimensions** yükü içerir.  Alan adlarının hepsi ile sona erer `prop__` .

* **hubname**: düzenleyiclerinizin çalıştığı görev hub 'ının adı.
* **appname**: işlev uygulamasının adı. Aynı Application Insights örneğini paylaşan birden çok işlevli uygulamanız olduğunda bu alan faydalıdır.
* **slotname**: geçerli işlev uygulamasının çalıştığı [dağıtım yuvası](../functions-deployment-slots.md) . Bu alan, ayarlarınızı yönetmek için dağıtım yuvaları kullandığınızda yararlı olur.
* **fonksiyonadı**: Orchestrator veya Activity işlevinin adı.
* **FunctionType**: **Orchestrator** veya **Activity** gibi işlevin türü.
* **InstanceId**: Orchestration ÖRNEĞININ benzersiz kimliği.
* **durum**: Örneğin yaşam döngüsü yürütme durumu. Geçerli değerler şunlardır:
  * **Zamanlandı**: işlev yürütme için zamanlandı ancak henüz çalışmaya başlamadı.
  * **Başlatıldı**: işlev çalışmaya başladı ancak henüz beklememiş veya tamamlanmamış.
  * **Beklenen: Orchestrator** bazı işleri zamanladı ve bunun tamamlanmasını bekliyor.
  * **Dinleme**: Orchestrator bir dış olay bildirimini dinliyor.
  * **Tamamlandı**: işlev başarıyla tamamlandı.
  * **Başarısız oldu**: işlev bir hata ile başarısız oldu.
* **neden**: izleme olayı ile ilişkili ek veriler. Örneğin, bir örnek bir dış olay bildirimi bekliyorsa Bu alan, beklediği olayın adını gösterir. Bir işlev başarısız olduysa, bu alan hata ayrıntılarını içerir.
* **ısreplay**: izleme olayının yeniden yürütülmüş yürütme için olup olmadığını gösteren Boole değeri.
* **extensionversion**: dayanıklı görev uzantısının sürümü. Sürüm bilgileri, uzantıdaki olası hatalar bildirildiğinde özellikle önemli veriler olur. Uzun süre çalışan örnekler, çalışırken bir güncelleştirme gerçekleşirse birden çok sürümü bildirebilir.
* **sequenceNumber**: bir olay için yürütme sıra numarası. Zaman damgasıyla birlikte, olayları yürütme zamanına göre sipariş etmeye yardımcı olur. *Örnek çalışırken konağın yeniden başlatılması durumunda bu sayının sıfıra sıfırlandığını unutmayın, bu nedenle her zaman zaman damgasına göre sıralamak önemlidir, sonra sequenceNumber.*

Application Insights yayılan izleme verilerinin ayrıntı düzeyi, `logger` dosyanın (işlevler 1. x) veya `logging` (işlevler 2,0) bölümünde yapılandırılabilir `host.json` .

#### <a name="functions-10"></a>İşlevler 1,0

```json
{
    "logger": {
        "categoryFilter": {
            "categoryLevels": {
                "Host.Triggers.DurableTask": "Information"
            }
        }
    }
}
```

#### <a name="functions-20"></a>İşlevler 2,0

```json
{
    "logging": {
        "logLevel": {
          "Host.Triggers.DurableTask": "Information",
        },
    }
}
```

Varsayılan olarak, tüm yeniden yürütme olmayan izleme olayları yayınlanır. Verilerin hacmi, `Host.Triggers.DurableTask` `"Warning"` `"Error"` Bu durum izleme olaylarının yalnızca olağanüstü durumlar için yayınlandığı, veya olarak ayarlanarak azaltılabilir.

Ayrıntılı düzenleme yeniden yürütme olaylarını yaymayı etkinleştirmek için, `LogReplayEvents` `true` `host.json` aşağıda gösterildiği gibi, dosyasında öğesine ayarlanabilir `durableTask` :

#### <a name="functions-10"></a>İşlevler 1,0

```json
{
    "durableTask": {
        "logReplayEvents": true
    }
}
```

#### <a name="functions-20"></a>İşlevler 2,0

```json
{
    "extensions": {
        "durableTask": {
            "logReplayEvents": true
        }
    }
}
```

> [!NOTE]
> Application Insights telemetri, verileri çok sık yaymamak için Azure Işlevleri çalışma zamanı tarafından örneklenir. Bu, kısa bir süre içinde birçok yaşam döngüsü olayı gerçekleştiğinde izleme bilgilerinin kaybolmasına neden olabilir. [Azure Işlevleri izleme makalesinde](../configure-monitoring.md#configure-sampling) , bu davranışın nasıl yapılandırılacağı açıklanmaktadır.

### <a name="single-instance-query"></a>Tek örnekli sorgu

Aşağıdaki sorgu, bir [Merhaba sıra](durable-functions-sequence.md) işlevi düzenleme işlevinin tek bir örneği için geçmiş izleme verilerini gösterir. [Kusto sorgu dili](/azure/data-explorer/kusto/query/)kullanılarak yazılmıştır. Yalnızca *mantıksal* yürütme yolunun gösterilmesi için yeniden yürütme yürütmesini filtreler. Olaylar, `timestamp` `sequenceNumber` aşağıdaki sorguda gösterildiği gibi ve arasında sıralama yaparak sıralanabilir:

```kusto
let targetInstanceId = "ddd1aaa685034059b545eb004b15d4eb";
let start = datetime(2018-03-25T09:20:00);
traces
| where timestamp > start and timestamp < start + 30m
| where customDimensions.Category == "Host.Triggers.DurableTask"
| extend functionName = customDimensions["prop__functionName"]
| extend instanceId = customDimensions["prop__instanceId"]
| extend state = customDimensions["prop__state"]
| extend isReplay = tobool(tolower(customDimensions["prop__isReplay"]))
| extend sequenceNumber = tolong(customDimensions["prop__sequenceNumber"])
| where isReplay != true
| where instanceId == targetInstanceId
| sort by timestamp asc, sequenceNumber asc
| project timestamp, functionName, state, instanceId, sequenceNumber, appName = cloud_RoleName
```

Sonuç, yürütme süresi tarafından artan düzende sıralanan etkinlik işlevleri dahil olmak üzere Orchestration yürütme yolunu gösteren izleme olaylarının bir listesidir.

![Tek örnekli sıralı sorgu Application Insights](./media/durable-functions-diagnostics/app-insights-single-instance-ordered-query.png)

### <a name="instance-summary-query"></a>Örnek Özet sorgusu

Aşağıdaki sorgu, belirli bir zaman aralığında çalıştırılan tüm düzenleme örneklerinin durumunu görüntüler.

```kusto
let start = datetime(2017-09-30T04:30:00);
traces
| where timestamp > start and timestamp < start + 1h
| where customDimensions.Category == "Host.Triggers.DurableTask"
| extend functionName = tostring(customDimensions["prop__functionName"])
| extend instanceId = tostring(customDimensions["prop__instanceId"])
| extend state = tostring(customDimensions["prop__state"])
| extend isReplay = tobool(tolower(customDimensions["prop__isReplay"]))
| extend output = tostring(customDimensions["prop__output"])
| where isReplay != true
| summarize arg_max(timestamp, *) by instanceId
| project timestamp, instanceId, functionName, state, output, appName = cloud_RoleName
| order by timestamp asc
```

Sonuç, örnek kimliklerinin ve bunların geçerli çalışma zamanı durumlarının bir listesidir.

![Tek örnekli sorgu Application Insights](./media/durable-functions-diagnostics/app-insights-single-summary-query.png)

## <a name="durable-task-framework-logging"></a>Dayanıklı görev çerçevesi günlüğü

Dayanıklı uzantı günlükleri, düzenleme mantığınızın davranışını anlamak için kullanışlıdır. Ancak, bu Günlükler her zaman çerçeve düzeyinde performans ve güvenilirlik sorunlarını ayıklamak için yeterli bilgi içermez. Dayanıklı uzantının **v 2.3.0** 'den başlayarak, temel dayanıklı görev çerçevesi (DTFx) tarafından oluşturulan Günlükler de koleksiyon için kullanılabilir.

DTFx tarafından oluşturulan günlüklere baktığınızda, DTFx altyapısının iki bileşenden oluştuğunu anlamak önemlidir: çekirdek dağıtım altyapısı ( `DurableTask.Core` ) ve desteklenen birçok depolama sağlayıcısından biri (dayanıklı işlevler `DurableTask.AzureStorage` Varsayılan olarak kullanır).

* **Durabletask. Core**: düzenleme yürütmesi ve düşük düzeyli zamanlama hakkındaki bilgileri içerir.
* **Durabletask. AzureStorage**: iç düzenleme durumunu depolamak ve getirmek için kullanılan iç kuyruklar, Bloblar ve depolama tabloları dahil olmak üzere Azure depolama yapıtlarıyla ilgili bilgiler içerir.

`logging/logLevel`İşlev uygulamanızın dosya **host.js** bölümünü güncelleştirerek bu günlükleri etkinleştirebilirsiniz. Aşağıdaki örnek, ve ' den her ikisinin de uyarı ve hata günlüklerinin nasıl etkinleştirileceğini gösterir `DurableTask.Core` `DurableTask.AzureStorage` :

```json
{
  "version": "2.0",
  "logging": {
    "logLevel": {
      "DurableTask.AzureStorage": "Warning",
      "DurableTask.Core": "Warning"
    }
  }
}
```

Application Insights etkinse, bu Günlükler koleksiyona otomatik olarak eklenir `trace` . Bunları, kusto sorgularını kullanarak diğer günlüklere arama yaptığınız şekilde de arayabilirsiniz `trace` .

> [!NOTE]
> Üretim uygulamaları için `DurableTask.Core` `DurableTask.AzureStorage` filtreyi kullanarak etkinleştirmeniz ve günlüklemeniz önerilir `"Warning"` . Gibi daha yüksek ayrıntı filtreleri `"Information"` , performans sorunlarını gidermek için çok yararlıdır. Ancak, bu günlük olayları yüksek hacimdir ve Application Insights veri depolama maliyetlerini önemli ölçüde artırabilir.

Aşağıdaki kusto sorgusu, DTFx günlüklerinin nasıl sorgulanacağını gösterir. Sorgunun en önemli bölümü, `where customerDimensions.Category startswith "DurableTask"` sonuçları ve kategorilerindeki günlüklere filtreleyerek bu yana olur `DurableTask.Core` `DurableTask.AzureStorage` .

```kusto
traces
| where customDimensions.Category startswith "DurableTask"
| project
    timestamp,
    severityLevel,
    Category = customDimensions.Category,
    EventId = customDimensions.EventId,
    message,
    customDimensions
| order by timestamp asc 
```
Sonuç, dayanıklı görev çerçevesi günlük sağlayıcıları tarafından yazılan bir Günlükler kümesidir.

![Application Insights DTFx sorgu sonuçları](./media/durable-functions-diagnostics/app-insights-dtfx.png)

Hangi günlük olaylarının kullanılabildiği hakkında daha fazla bilgi için bkz. [GitHub 'Da dayanıklı görev çerçevesi yapılandırılmış günlük belgeleri](https://github.com/Azure/durabletask/tree/master/src/DurableTask.Core/Logging#durabletaskcore-logging).

## <a name="app-logging"></a>Uygulama günlüğü

Doğrudan bir Orchestrator işlevinden Günlükler yazarken Orchestrator yeniden yürütme davranışının aklınızda tutulması önemlidir. Örneğin, aşağıdaki Orchestrator işlevini göz önünde bulundurun:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("FunctionChain")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context,
    ILogger log)
{
    log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    context.log("Calling F1.");
    yield context.df.callActivity("F1");
    context.log("Calling F2.");
    yield context.df.callActivity("F2");
    context.log("Calling F3.");
    yield context.df.callActivity("F3");
    context.log("Done!");
});
```

# <a name="python"></a>[Python](#tab/python)
```python
import logging
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    logging.info("Calling F1.")
    yield context.call_activity("F1")
    logging.info("Calling F2.")
    yield context.call_activity("F2")
    logging.info("Calling F3.")
    yield context.call_activity("F3")
    return None

main = df.Orchestrator.create(orchestrator_function)
```

---

Elde edilen günlük verileri aşağıdaki örnek çıkışa benzer bir şekilde görünür:

```txt
Calling F1.
Calling F1.
Calling F2.
Calling F1.
Calling F2.
Calling F3.
Calling F1.
Calling F2.
Calling F3.
Done!
```

> [!NOTE]
> Günlük, F1, F2 ve F3 çağrısı yaparken, bu *işlevlerin yalnızca ilk* kez karşılaştığı zaman adlandırıldığını unutmayın. Yeniden yürütme sırasında gerçekleşen sonraki çağrılar atlanır ve çıktılar Orchestrator mantığına yeniden yürütülür.

Yalnızca yeniden denenmeyen yürütmeler üzerinde Günlükler yazmak istiyorsanız, yalnızca "yeniden oynama" bayrağı ise günlüğe bir koşullu ifade yazabilirsiniz `false` . Yukarıdaki örneği, ancak bu kez yeniden yürütme denetimlerini göz önünde bulundurun.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("FunctionChain")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context,
    ILogger log)
{
    if (!context.IsReplaying) log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    if (!context.IsReplaying) log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    if (!context.IsReplaying) log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

Dayanıklı İşlevler 2,0 ' den başlayarak, .NET Orchestrator işlevlerinin yeniden `ILogger` yürütme sırasında günlük deyimlerini otomatik olarak filtreleyen bir oluşturma seçeneği de vardır. Bu otomatik filtreleme, [ıdurableorchestrationcontext. CreateReplaySafeLogger (ILogger)](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.durablecontextextensions.createreplaysafelogger) API 'si kullanılarak yapılır.

```csharp
[FunctionName("FunctionChain")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context,
    ILogger log)
{
    log = context.CreateReplaySafeLogger(log);
    log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

> [!NOTE]
> Önceki C# örnekleri Dayanıklı İşlevler 2. x içindir. Dayanıklı İşlevler 1. x için yerine kullanmanız gerekir `DurableOrchestrationContext` `IDurableOrchestrationContext` . Sürümler arasındaki farklılıklar hakkında daha fazla bilgi için [dayanıklı işlevler sürümler](durable-functions-versions.md) makalesine bakın.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    if (!context.df.isReplaying) context.log("Calling F1.");
    yield context.df.callActivity("F1");
    if (!context.df.isReplaying) context.log("Calling F2.");
    yield context.df.callActivity("F2");
    if (!context.df.isReplaying) context.log("Calling F3.");
    yield context.df.callActivity("F3");
    context.log("Done!");
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import logging
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    if not context.is_replaying:
        logging.info("Calling F1.")
    yield context.call_activity("F1")
    if not context.is_replaying:
        logging.info("Calling F2.")
    yield context.call_activity("F2")
    if not context.is_replaying:
        logging.info("Calling F3.")
    yield context.call_activity("F3")
    return None

main = df.Orchestrator.create(orchestrator_function)
```

---

Daha önce bahsedilen değişikliklerle, günlük çıktısı aşağıdaki gibidir:

```txt
Calling F1.
Calling F2.
Calling F3.
Done!
```

## <a name="custom-status"></a>Özel durum

Özel düzenleme durumu, Orchestrator işleviniz için özel bir durum değeri ayarlamanıza olanak sağlar. Bu özel durum daha sonra [http durum sorgu API 'si](durable-functions-http-api.md#get-instance-status) veya DILE özgü API çağrıları aracılığıyla dış istemciler tarafından görülebilir. Özel düzenleme durumu Orchestrator işlevleri için daha zengin izleme imkanı sunar. Örneğin, Orchestrator işlev kodu uzun süre çalışan bir işlemin ilerlemesini güncelleştirmek için "özel durum ayarla" API 'sini çağırabilir. Web sayfası veya diğer dış sistem gibi bir istemci, daha zengin ilerleme bilgileri için HTTP durum sorgusu API 'Lerini düzenli aralıklarla sorgulayabilir. Orchestrator işlevindeki özel durum değerini ayarlamaya yönelik örnek kod aşağıda verilmiştir:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("SetStatusTest")]
public static async Task SetStatusTest([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    // ...do work...

    // update the status of the orchestration with some arbitrary data
    var customStatus = new { completionPercentage = 90.0, status = "Updating database records" };
    context.SetCustomStatus(customStatus);

    // ...do more work...
}
```

> [!NOTE]
> Önceki C# örneği Dayanıklı İşlevler 2. x içindir. Dayanıklı İşlevler 1. x için yerine kullanmanız gerekir `DurableOrchestrationContext` `IDurableOrchestrationContext` . Sürümler arasındaki farklılıklar hakkında daha fazla bilgi için [dayanıklı işlevler sürümler](durable-functions-versions.md) makalesine bakın.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    // ...do work...

    // update the status of the orchestration with some arbitrary data
    const customStatus = { completionPercentage: 90.0, status: "Updating database records", };
    context.df.setCustomStatus(customStatus);

    // ...do more work...
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import logging
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    # ...do work...

    # update the status of the orchestration with some arbitrary data
    custom_status = {'completionPercentage': 90.0, 'status': 'Updating database records'}
    context.set_custom_status(custom_status)
    # ...do more work...

    return None

main = df.Orchestrator.create(orchestrator_function)
```

---

Düzenleme çalışırken, dış istemciler şu özel durumu getirebilir:

```http
GET /runtime/webhooks/durabletask/instances/instance123?code=XYZ

```

İstemciler aşağıdaki yanıtı alır:

```json
{
  "runtimeStatus": "Running",
  "input": null,
  "customStatus": { "completionPercentage": 90.0, "status": "Updating database records" },
  "output": null,
  "createdTime": "2017-10-06T18:30:24Z",
  "lastUpdatedTime": "2017-10-06T19:40:30Z"
}
```

> [!WARNING]
> Özel durum yükü, bir Azure Tablo depolama sütununa sığamayacak olması gerektiğinden, 16 KB 'lık UTF-16 JSON metniyle sınırlandırılmıştır. Daha büyük yük gerekiyorsa dış depolama alanını kullanabilirsiniz.

## <a name="debugging"></a>Hata Ayıklama

Azure Işlevleri, işlev kodu doğrudan hata ayıklamayı destekler ve aynı destek, Azure 'da veya yerel olarak çalıştırılmasının yanı sıra Dayanıklı İşlevler ileri taşır. Ancak, hata ayıklarken farkında olacak birkaç davranış vardır:

* Yeniden **Yürüt**: yeni girişler alındığında Orchestrator işlevleri düzenli olarak yeniden [tekrarlanır](durable-functions-orchestrations.md#reliability) . Bu davranış, bir Orchestrator işlevinin tek bir *mantıksal* yürütmesi, özellikle işlev kodunda daha önce ayarlandıysa, aynı kesme noktasına birden çok kez ulaşabilme anlamına gelir.
* **Await**: bir `await` Orchestrator işlevinde her karşılaşıldığında, dayanıklı görev çerçevesi dağıtıcısına denetim verir. İlk kez bir kez `await` karşılaşılırsa, ilişkili görev *hiçbir zaman sürdürülmez* . Görev hiçbir şekilde devam etmeyeceğinden, await (Visual Studio 'da F10) *üzerinde* Adımlama mümkün değildir. Yalnızca bir görev yeniden yürütüldüğünde, üzerinde adımlamayı yapın.
* **Mesajlaşma zaman aşımları**: dayanıklı işlevler Orchestrator, etkinlik ve varlık işlevlerinin yürütülmesini sağlamak için sıra iletilerini dahili olarak kullanır. Çoklu VM ortamında, uzun bir süre için hata ayıklamanın kesilmesi, başka bir VM 'nin iletiyi seçmesini ve yinelenen yürütmeye neden olabilir. Bu davranış düzenli sıra tetikleyicisi işlevleri için de bulunur, ancak kuyruklar bir uygulama ayrıntısı olduğundan bu bağlamda işaret etmek önemlidir.
* **Durduruluyor ve başlatılıyor**: dayanıklı işlevlerde iletiler hata ayıklama oturumları arasında kalır. Kalıcı bir işlev yürütülürken hata ayıklamayı durdurur ve yerel ana bilgisayar işlemini sonlandırabilirsiniz, bu işlev gelecekteki bir hata ayıklama oturumunda otomatik olarak yeniden çalıştırılabilir. Bu davranış, beklenmediği zaman kafa karıştırıcı olabilir. Bu davranışı önlemek için, hata ayıklama oturumları arasındaki [iç depolama sıralarındaki](durable-functions-perf-and-scale.md#internal-queue-triggers) tüm iletileri temizleme işlemi bir tekniktir.

> [!TIP]
> Orchestrator işlevlerinde kesme noktaları ayarlarken, yalnızca yeniden denenmeyen yürütmeyi bölmek istiyorsanız, yalnızca "yeniden yürütme" değeri ise kesen bir koşullu kesme noktası ayarlayabilirsiniz `false` .

## <a name="storage"></a>Depolama

Varsayılan olarak, Dayanıklı İşlevler durumu Azure Storage 'da depolar. Bu davranış, [Microsoft Azure Depolama Gezgini](../../vs-azure-tools-storage-manage-with-storage-explorer.md)gibi araçları kullanarak, düzenleyiclarınızın durumunu inceleyebilmeniz anlamına gelir.

![Azure Depolama Gezgini ekran görüntüsü](./media/durable-functions-diagnostics/storage-explorer.png)

Bu, bir düzenleme için tam olarak hangi durum olduğunu görtiğinden hata ayıklama için yararlıdır. Kuyruklarda bulunan iletiler, hangi çalışmanın beklendiğini (veya bazı durumlarda takılı olduğunu) öğrenmek için de incelenebilir.

> [!WARNING]
> Tablo depolamada yürütme geçmişini görmek uygun olsa da, bu tablo üzerinde herhangi bir bağımlılık yapmaktan kaçının. Dayanıklı İşlevler uzantısı geliştikçe değişiklik gösterebilir.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure Işlevleri 'nde izleme hakkında daha fazla bilgi edinin](../functions-monitoring.md)
