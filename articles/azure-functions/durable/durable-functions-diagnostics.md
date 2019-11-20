---
title: Dayanıklı İşlevler tanılama-Azure
description: Azure Işlevleri için Dayanıklı İşlevler uzantısıyla ilgili sorunları tanılamayı öğrenin.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 333ddbc15e3ff62b1cd46383c4e3be75fb3dbb88
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73614946"
---
# <a name="diagnostics-in-durable-functions-in-azure"></a>Azure 'da Dayanıklı İşlevler tanılama

[Dayanıklı işlevler](durable-functions-overview.md)sorunları tanılamaya yönelik çeşitli seçenekler vardır. Bu seçeneklerden bazıları normal işlevlerle aynıdır ve bazıları da Dayanıklı İşlevler için benzersizdir.

## <a name="application-insights"></a>Application Insights

[Application Insights](../../azure-monitor/app/app-insights-overview.md) , Azure işlevlerinde tanılama ve izleme yapmak için önerilen yoldur. Aynı Dayanıklı İşlevler için de geçerlidir. İşlev uygulamanızda Application Insights nasıl yararlanacağınızı gösteren bir genel bakış için bkz. [Azure Işlevlerini izleme](../functions-monitoring.md).

Azure Işlevleri dayanıklı uzantısı, bir Orchestration 'un uçtan uca yürütülmesini izlemenize imkan tanıyan *olayları izleme* özelliği de yayar. Bu izleme olayları, Azure portal [Application Insights Analytics](../../azure-monitor/app/analytics.md) Aracı kullanılarak bulunabilir ve sorgulanır.

### <a name="tracking-data"></a>İzleme verileri

Bir Orchestration örneğinin her yaşam döngüsü olayı, bir izleme olayının Application Insights **İziz** koleksiyonuna yazılmasına neden olur. Bu olay, birkaç alan içeren bir **Customdimensions** yükü içerir.  Alan adlarının tümü `prop__`ile sona erer.

* **hubname**: düzenleyiclerinizin çalıştığı görev hub 'ının adı.
* **appname**: işlev uygulamasının adı. Aynı Application Insights örneğini paylaşan birden çok işlevli uygulamanız olduğunda bu alan faydalıdır.
* **slotname**: geçerli işlev uygulamasının çalıştığı [dağıtım yuvası](../functions-deployment-slots.md) . Bu alan, ayarlarınızı yönetmek için dağıtım yuvalarından yararlandığınızda yararlıdır.
* **fonksiyonadı**: Orchestrator veya Activity işlevinin adı.
* **FunctionType**: **Orchestrator** veya **Activity**gibi işlevin türü.
* **InstanceId**: Orchestration ÖRNEĞININ benzersiz kimliği.
* **durum**: Örneğin yaşam döngüsü yürütme durumu. Geçerli değerler şunlardır:
  * **Zamanlandı**: işlev yürütme için zamanlandı ancak henüz çalışmaya başlamadı.
  * **Başlatıldı**: işlev çalışmaya başladı ancak henüz beklememiş veya tamamlanmamış.
  * **Beklenen: Orchestrator**bazı işleri zamanladı ve bunun tamamlanmasını bekliyor.
  * **Dinleme**: Orchestrator bir dış olay bildirimini dinliyor.
  * **Tamamlandı**: işlev başarıyla tamamlandı.
  * **Başarısız oldu**: işlev bir hata ile başarısız oldu.
* **neden**: izleme olayı ile ilişkili ek veriler. Örneğin, bir örnek bir dış olay bildirimi bekliyorsa Bu alan, beklediği olayın adını gösterir. Bir işlev başarısız olduysa, bu alan hata ayrıntılarını içerir.
* **ısreplay**: izleme olayının yeniden yürütülmüş yürütme için olup olmadığını gösteren Boole değeri.
* **extensionversion**: dayanıklı görev uzantısının sürümü. Sürüm bilgileri, uzantıdaki olası hatalar bildirildiğinde özellikle önemli veriler olur. Uzun süre çalışan örnekler, çalışırken bir güncelleştirme gerçekleşirse birden çok sürümü bildirebilir.
* **sequenceNumber**: bir olay için yürütme sıra numarası. Zaman damgasıyla birlikte, olayları yürütme zamanına göre sipariş etmeye yardımcı olur. *Örnek çalışırken konağın yeniden başlatılması durumunda bu sayının sıfıra sıfırlandığını unutmayın, bu nedenle her zaman zaman damgasına göre sıralamak önemlidir, sonra sequenceNumber.*

Application Insights yayılan izleme verilerinin ayrıntı düzeyi, `host.json` dosyasının `logger` (Işlevler 1. x) veya `logging` (Işlevler 2,0) bölümünde yapılandırılabilir.

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

Varsayılan olarak, tüm yeniden yürütme olmayan izleme olayları yayınlanır. `Host.Triggers.DurableTask` `"Warning"` veya `"Error"`, bu durum izleme olaylarının yalnızca olağanüstü durumlar için yayınlandığı, veri hacmi azaltılabilir.

Ayrıntılı düzenleme yeniden yürütme olaylarını yaymayı etkinleştirmek için `LogReplayEvents` gösterildiği gibi `durableTask` altındaki `host.json` dosyasında `true` olarak ayarlanabilir:

#### <a name="functions-10"></a>İşlevler 1,0

```json
{
    "durableTask": {
        "logReplayEvents": true
    }
}
```

#### <a name="functions-20"></a>İşlevler 2,0

```javascript
{
    "extensions": {
        "durableTask": {
            "logReplayEvents": true
        }
    }
}
```

> [!NOTE]
> Application Insights telemetri, verileri çok sık yaymamak için Azure Işlevleri çalışma zamanı tarafından örneklenir. Bu, kısa bir süre içinde birçok yaşam döngüsü olayı gerçekleştiğinde izleme bilgilerinin kaybolmasına neden olabilir. [Azure Işlevleri izleme makalesinde](../functions-monitoring.md#configure-sampling) , bu davranışın nasıl yapılandırılacağı açıklanmaktadır.

### <a name="single-instance-query"></a>Tek örnekli sorgu

Aşağıdaki sorgu, bir [Merhaba sıra](durable-functions-sequence.md) işlevi düzenleme işlevinin tek bir örneği için geçmiş izleme verilerini gösterir. [Application Insights sorgu dili (AIQL)](https://aka.ms/LogAnalyticsLanguageReference)kullanılarak yazılmıştır. Yalnızca *mantıksal* yürütme yolunun gösterilmesi için yeniden yürütme yürütmesini filtreler. Olaylar, aşağıdaki sorguda gösterildiği gibi `timestamp` ve `sequenceNumber` sıralaması tarafından sıralanabilir:

```AIQL
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

![Application Insights sorgu](./media/durable-functions-diagnostics/app-insights-single-instance-ordered-query.png)

### <a name="instance-summary-query"></a>Örnek Özet sorgusu

Aşağıdaki sorgu, belirli bir zaman aralığında çalıştırılan tüm düzenleme örneklerinin durumunu görüntüler.

```AIQL
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

![Application Insights sorgu](./media/durable-functions-diagnostics/app-insights-single-summary-query.png)

## <a name="logging"></a>Günlüğe kaydetme

Doğrudan bir Orchestrator işlevinden Günlükler yazarken Orchestrator yeniden yürütme davranışının aklınızda tutulması önemlidir. Örneğin, aşağıdaki Orchestrator işlevini göz önünde bulundurun:

### <a name="precompiled-c"></a>DerlemesiC#

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

### <a name="c-script"></a>C#SCRIPT

```csharp
public static async Task Run(
    IDurableOrchestrationContext context,
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

### <a name="javascript-functions-20-only"></a>JavaScript (yalnızca Işlevler 2,0)

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

Yalnızca yeniden denenmesiz yürütme oturumu açmak istiyorsanız, yalnızca `IsReplaying` `false`olduğunda günlüğe bir koşullu ifade yazabilirsiniz. Yukarıdaki örneği, ancak bu kez yeniden yürütme denetimlerini göz önünde bulundurun.

#### <a name="precompiled-c"></a>DerlemesiC#

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

#### <a name="c"></a>C#

```cs
public static async Task Run(
    IDurableOrchestrationContext context,
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

#### <a name="javascript-functions-20-only"></a>JavaScript (yalnızca Işlevler 2,0)

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

Dayanıklı İşlevler 2,0 ' den başlayarak, .NET Orchestrator işlevlerinin yeniden yürütme sırasında günlük deyimlerini otomatik olarak filtreleyen bir `ILogger` oluşturma seçeneği de vardır. Bu otomatik filtreleme `IDurableOrchestrationContext.CreateReplaySafeLogger(ILogger)` API kullanılarak yapılır.

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

Daha önce bahsedilen değişikliklerle, günlük çıktısı aşağıdaki gibidir:

```txt
Calling F1.
Calling F2.
Calling F3.
Done!
```

> [!NOTE]
> Önceki C# örnekler dayanıklı işlevler 2. x içindir. Dayanıklı İşlevler 1. x için `IDurableOrchestrationContext`yerine `DurableOrchestrationContext` kullanmanız gerekir. Sürümler arasındaki farklılıklar hakkında daha fazla bilgi için [dayanıklı işlevler sürümler](durable-functions-versions.md) makalesine bakın.

## <a name="custom-status"></a>Özel durum

Özel düzenleme durumu, Orchestrator işleviniz için özel bir durum değeri ayarlamanıza olanak sağlar. Bu durum HTTP durumu sorgu API 'SI veya `IDurableOrchestrationClient.GetStatusAsync` API 'si aracılığıyla sağlanır. Özel düzenleme durumu Orchestrator işlevleri için daha zengin izleme imkanı sunar. Örneğin, Orchestrator işlev kodu, uzun süreli bir işlemin ilerlemesini güncelleştirmek için `IDurableOrchestrationContext.SetCustomStatus` çağrılar içerebilir. Web sayfası veya diğer dış sistem gibi bir istemci, daha zengin ilerleme bilgileri için HTTP durum sorgusu API 'Lerini düzenli aralıklarla sorgulayabilir. `IDurableOrchestrationContext.SetCustomStatus` kullanan bir örnek aşağıda verilmiştir:

### <a name="precompiled-c"></a>DerlemesiC#

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
> Önceki C# örnek dayanıklı işlevler 2. x içindir. Dayanıklı İşlevler 1. x için `IDurableOrchestrationContext`yerine `DurableOrchestrationContext` kullanmanız gerekir. Sürümler arasındaki farklılıklar hakkında daha fazla bilgi için [dayanıklı işlevler sürümler](durable-functions-versions.md) makalesine bakın.

### <a name="javascript-functions-20-only"></a>JavaScript (yalnızca Işlevler 2,0)

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

Düzenleme çalışırken, dış istemciler şu özel durumu getirebilir:

```http
GET /admin/extensions/DurableTaskExtension/instances/instance123

```

İstemciler aşağıdaki yanıtı alır:

```http
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

## <a name="debugging"></a>Hata ayıklama

Azure Işlevleri, işlev kodu doğrudan hata ayıklamayı destekler ve aynı destek, Azure 'da veya yerel olarak çalıştırılmasının yanı sıra Dayanıklı İşlevler ileri taşır. Ancak, hata ayıklarken farkında olacak birkaç davranış vardır:

* Yeniden **Yürüt**: yeni girişler alındığında Orchestrator işlevleri düzenli olarak yeniden [tekrarlanır](durable-functions-orchestrations.md#reliability) . Bu davranış, bir Orchestrator işlevinin tek bir *mantıksal* yürütmesi, özellikle işlev kodunda daha önce ayarlandıysa, aynı kesme noktasına birden çok kez ulaşabilme anlamına gelir.
* **Await**: bir orchestrator işlevinde `await` her karşılaşıldığında, kalıcı görev çerçevesi dağıtıcısına denetim verir. Belirli bir `await` ilk kez karşılaşılırsa, ilişkili görev *hiçbir zaman sürdürülmez* . Görev hiçbir şekilde devam etmeyeceğinden, await (Visual Studio 'da F10) *üzerinde* Adımlama mümkün değildir. Yalnızca bir görev yeniden yürütüldüğünde, üzerinde adımlamayı yapın.
* **Mesajlaşma zaman aşımları**: dayanıklı işlevler Orchestrator, etkinlik ve varlık işlevlerinin yürütülmesini sağlamak için sıra iletilerini dahili olarak kullanır. Çoklu VM ortamında, uzun bir süre için hata ayıklamanın kesilmesi, başka bir VM 'nin iletiyi seçmesini ve yinelenen yürütmeye neden olabilir. Bu davranış düzenli sıra tetikleyicisi işlevleri için de bulunur, ancak kuyruklar bir uygulama ayrıntısı olduğundan bu bağlamda işaret etmek önemlidir.
* **Durduruluyor ve başlatılıyor**: dayanıklı işlevlerde iletiler hata ayıklama oturumları arasında kalır. Kalıcı bir işlev yürütülürken hata ayıklamayı durdurur ve yerel ana bilgisayar işlemini sonlandırabilirsiniz, bu işlev gelecekteki bir hata ayıklama oturumunda otomatik olarak yeniden çalıştırılabilir. Bu davranış, beklenmediği zaman kafa karıştırıcı olabilir. Bu davranışı önlemek için, hata ayıklama oturumları arasındaki [iç depolama sıralarındaki](durable-functions-perf-and-scale.md#internal-queue-triggers) tüm iletileri temizleme işlemi bir tekniktir.

> [!TIP]
> Orchestrator işlevlerinde kesme noktaları ayarlarken yalnızca yeniden denenmeyen yürütmeyi bölmek istiyorsanız, yalnızca `IsReplaying` `false`olan bir koşullu kesme noktası ayarlayabilirsiniz.

## <a name="storage"></a>Depolama

Varsayılan olarak, Dayanıklı İşlevler durumu Azure Storage 'da depolar. Bu davranış, [Microsoft Azure Depolama Gezgini](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)gibi araçları kullanarak, düzenleyiclarınızın durumunu inceleyebilmeniz anlamına gelir.

![Azure Depolama Gezgini ekran görüntüsü](./media/durable-functions-diagnostics/storage-explorer.png)

Bu, bir düzenleme için tam olarak hangi durum olduğunu görtiğinden hata ayıklama için yararlıdır. Kuyruklarda bulunan iletiler, hangi çalışmanın beklendiğini (veya bazı durumlarda takılı olduğunu) öğrenmek için de incelenebilir.

> [!WARNING]
> Tablo depolamada yürütme geçmişini görmek uygun olsa da, bu tablo üzerinde herhangi bir bağımlılık yapmaktan kaçının. Dayanıklı İşlevler uzantısı geliştikçe değişiklik gösterebilir.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure Işlevleri 'nde izleme hakkında daha fazla bilgi edinin](../functions-monitoring.md)
