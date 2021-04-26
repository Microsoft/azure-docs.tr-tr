---
title: Dayanıklı İşlevler genel bakış-Azure
description: Azure Işlevleri için Dayanıklı İşlevler uzantısına giriş.
author: cgillum
ms.topic: overview
ms.date: 12/23/2020
ms.author: cgillum
ms.reviewer: azfuncdf
ms.openlocfilehash: d99f1bd97c8199de1bda12f28f3fcb31b697946f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105031503"
---
# <a name="what-are-durable-functions"></a>Dayanıklı İşlevler nedir?

*Dayanıklı işlevler* , bir sunucusuz işlem ortamında durum bilgisi olan işlevler yazmanıza olanak sağlayan bir [Azure işlevleri](../functions-overview.md) uzantısıdır. Uzantı, Azure Işlevleri programlama modelini kullanarak [*varlık işlevleri*](durable-functions-entities.md) yazarak [*Orchestrator işlevlerini*](durable-functions-orchestrations.md) ve durum bilgisi olan varlıkları yazarak durum bilgisi olan iş akışları tanımlamanızı sağlar. Arka planda, uzantı durum, kontrol noktaları ve yeniden başlatma işlemlerini yönetip iş mantığınıza odaklanmanızı sağlar.

## <a name="supported-languages"></a><a name="language-support"></a>Desteklenen diller

Dayanıklı İşlevler Şu anda aşağıdaki dilleri desteklemektedir:

* **C#**: hem [önceden derlenmiş sınıf kitaplıkları](../functions-dotnet-class-library.md) hem de [C# betiği](../functions-reference-csharp.md).
* **JavaScript**: yalnızca Azure işlevleri çalışma zamanının 2. x sürümü için desteklenir. Dayanıklı İşlevler uzantısının veya sonraki bir sürümün sürüm 1.7.0 gerektirir. 
* **Python**: dayanıklı İşlevler uzantısının veya sonraki bir sürümün sürüm 2.3.1 gerektirir.
* **F #**: önceden derlenmiş sınıf kitaplıkları ve F # betiği. F # betiği yalnızca Azure Işlevleri çalışma zamanının sürüm 1. x 'i için desteklenir.
* **PowerShell**: dayanıklı işlevler desteği şu anda genel önizlemededir. Yalnızca Azure Işlevleri çalışma zamanı ve PowerShell 7 sürümü 3. x için desteklenir. Dayanıklı İşlevler uzantısının veya sonraki bir sürümün sürüm 2.2.2 gerektirir. Şu anda yalnızca şu desenler destekleniyor: [işlev zinciri](#chaining), [fan-çıkış/fan](#fan-in-out), [zaman uyumsuz HTTP API 'leri](#async-http).

En son özelliklere ve güncelleştirmelere erişmek için, Dayanıklı İşlevler uzantısının ve dile özgü Dayanıklı İşlevler kitaplıklarının en son sürümlerini kullanmanız önerilir. [Dayanıklı işlevler sürümleri](durable-functions-versions.md)hakkında daha fazla bilgi edinin.

Dayanıklı İşlevler tüm [Azure işlevleri dillerini](../supported-languages.md)destekleme amacını içerir. Ek dilleri desteklemek için işin en son durumunun [dayanıklı işlevler sorunlar listesine](https://github.com/Azure/azure-functions-durable-extension/issues) bakın.

Azure Işlevleri gibi, [Visual Studio 2019](durable-functions-create-first-csharp.md), [Visual Studio Code](quickstart-js-vscode.md)ve [Azure Portal](durable-functions-create-portal.md)kullanarak dayanıklı işlevler geliştirmenize yardımcı olacak şablonlar vardır.

## <a name="application-patterns"></a>Uygulama desenleri

Dayanıklı İşlevler için birincil kullanım örneği sunucusuz uygulamalarda karmaşık, durum bilgisi olan düzenleme gereksinimlerini basitleştirir. Aşağıdaki bölümlerde Dayanıklı İşlevler avantajlarından faydalanabilecek tipik uygulama desenleri açıklanır:

* [İşlev zinciri oluşturma](#chaining)
* [Yelpaze dışarı/yelpaze içeri](#fan-in-out)
* [Zaman uyumsuz HTTP API’leri](#async-http)
* [İzleme](#monitoring)
* [İnsan etkileşimi](#human)
* [Toplayıcı (durum bilgisi olan varlıklar)](#aggregator)

### <a name="pattern-1-function-chaining"></a><a name="chaining"></a>Model #1: Işlev zinciri oluşturma

İşlev zincirleme modelinde, bir dizi işlev belirli bir sırada yürütülür. Bu düzende, bir işlevin çıktısı başka bir işlevin girişine uygulanır.

![İşlev zincirleme deseninin diyagramı](./media/durable-functions-concepts/function-chaining.png)

Aşağıdaki örnekte gösterildiği gibi, öz işlev zincirleme modelini uygulamak için Dayanıklı İşlevler kullanabilirsiniz.

Bu örnekte,,, `F1` ve değerleri `F2` `F3` `F4` aynı işlev uygulamasındaki diğer işlevlerin adlarıdır. Normal kesinlik temelli kodlama yapılarını kullanarak denetim akışı uygulayabilirsiniz. Kod yukarıdan aşağı doğru çalışır. Kod, koşul ve döngüler gibi var olan dil denetim akışı semantiğini içerebilir. Bloklara hata işleme mantığını dahil edebilirsiniz `try` / `catch` / `finally` .

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("Chaining")]
public static async Task<object> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    try
    {
        var x = await context.CallActivityAsync<object>("F1", null);
        var y = await context.CallActivityAsync<object>("F2", x);
        var z = await context.CallActivityAsync<object>("F3", y);
        return  await context.CallActivityAsync<object>("F4", z);
    }
    catch (Exception)
    {
        // Error handling or compensation goes here.
    }
}
```

`context`Diğer işlevleri ada göre çağırmak, parametreleri geçirmek ve işlev çıkışı döndürmek için parametresini kullanabilirsiniz. Kod her çağırdığında `await` dayanıklı işlevler Framework geçerli işlev örneğinin ilerlemesini kontrol etmektedir. İşlem veya sanal makine yürütme üzerinden geçişli olarak geri dönüştürüldüğünde, işlev örneği önceki çağrıdan devam eder `await` . Daha fazla bilgi için, bkz. bir sonraki bölüm, model #2: fan çıkış/fan

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    try {
        const x = yield context.df.callActivity("F1");
        const y = yield context.df.callActivity("F2", x);
        const z = yield context.df.callActivity("F3", y);
        return    yield context.df.callActivity("F4", z);
    } catch (error) {
        // Error handling or compensation goes here.
    }
});
```

`context.df`Diğer işlevleri ada göre çağırmak, parametreleri geçirmek ve işlev çıkışı döndürmek için nesnesini kullanabilirsiniz. Kod her çağırdığında `yield` dayanıklı işlevler Framework geçerli işlev örneğinin ilerlemesini kontrol etmektedir. İşlem veya sanal makine yürütme üzerinden geçişli olarak geri dönüştürüldüğünde, işlev örneği önceki çağrıdan devam eder `yield` . Daha fazla bilgi için, bkz. bir sonraki bölüm, model #2: fan çıkış/fan

> [!NOTE]
> `context`JavaScript içindeki nesne, tüm [işlev bağlamını](../functions-reference-node.md#context-object)temsil eder. `df`Ana bağlamdaki özelliğini kullanarak dayanıklı işlevler bağlamına erişin.

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df


def orchestrator_function(context: df.DurableOrchestrationContext):
    x = yield context.call_activity("F1", None)
    y = yield context.call_activity("F2", x)
    z = yield context.call_activity("F3", y)
    result = yield context.call_activity("F4", z)
    return result


main = df.Orchestrator.create(orchestrator_function)
```

`context`Diğer işlevleri ada göre çağırmak, parametreleri geçirmek ve işlev çıkışı döndürmek için nesnesini kullanabilirsiniz. Kod her çağırdığında `yield` dayanıklı işlevler Framework geçerli işlev örneğinin ilerlemesini kontrol etmektedir. İşlem veya sanal makine yürütme üzerinden geçişli olarak geri dönüştürüldüğünde, işlev örneği önceki çağrıdan devam eder `yield` . Daha fazla bilgi için, bkz. bir sonraki bölüm, model #2: fan çıkış/fan

> [!NOTE]
> `context`Python 'daki nesnesi düzenleme bağlamını temsil eder. Düzenleme bağlamındaki özelliğini kullanarak ana Azure Işlevleri bağlamına erişin `function_context` .

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```PowerShell
param($Context)

$X = Invoke-ActivityFunction -FunctionName 'F1'
$Y = Invoke-ActivityFunction -FunctionName 'F2' -Input $X
$Z = Invoke-ActivityFunction -FunctionName 'F3' -Input $Y
Invoke-ActivityFunction -FunctionName 'F4' -Input $Z
```

`Invoke-ActivityFunction`Diğer işlevleri ada göre çağırmak, parametreleri geçirmek ve işlev çıkışı döndürmek için komutunu kullanabilirsiniz. Anahtar olmadan kod her çağırdığında `Invoke-ActivityFunction` `NoWait` , dayanıklı işlevler Framework geçerli işlev örneğinin ilerlemesini kontrol etmektedir. İşlem veya sanal makine yürütme üzerinden geçişli olarak geri dönüştürüldüğünde, işlev örneği önceki çağrıdan devam eder `Invoke-ActivityFunction` . Daha fazla bilgi için, bkz. bir sonraki bölüm, model #2: fan çıkış/fan

---

### <a name="pattern-2-fan-outfan-in"></a><a name="fan-in-out"></a>#2 desenli desenler: fan çıkış/fan

Diğer bir deyişle, desenli çıkış/fan, birden çok işlevi paralel olarak yürütür ve sonra tüm işlevlerin bitmesini bekler. Genellikle, bazı toplama işleri işlevlerden döndürülen sonuçlar üzerinde yapılır.

![Fan çıkış/fan deseninin diyagramı](./media/durable-functions-concepts/fan-out-fan-in.png)

Normal işlevlerle, işlevi bir sıraya birden çok ileti göndermesini sağlayarak dışarı aktarabilirsiniz. Geriye doğru zor, çok daha zordur. İçinde fanı için, normal bir işlevde, kuyruk tetiklenen işlevlerin ne zaman sona erdirmek için kod yazarsınız ve sonra işlev çıkışlarını depoladığınızda.

Dayanıklı İşlevler uzantısı görece basit kodla bu düzene sahiptir:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("FanOutFanIn")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var parallelTasks = new List<Task<int>>();

    // Get a list of N work items to process in parallel.
    object[] workBatch = await context.CallActivityAsync<object[]>("F1", null);
    for (int i = 0; i < workBatch.Length; i++)
    {
        Task<int> task = context.CallActivityAsync<int>("F2", workBatch[i]);
        parallelTasks.Add(task);
    }

    await Task.WhenAll(parallelTasks);

    // Aggregate all N outputs and send the result to F3.
    int sum = parallelTasks.Sum(t => t.Result);
    await context.CallActivityAsync("F3", sum);
}
```

Fanı-Out işi işlevin birden çok örneğine dağıtılır `F2` . İş, dinamik bir görev listesi kullanılarak izlenir. `Task.WhenAll` çağrılan tüm işlevlerin bitmesini beklemek için çağırılır. Sonra, `F2` işlev çıkışları dinamik görev listesinden toplanır ve `F3` işleve geçirilir.

Çağrısında gerçekleşen otomatik onay `await` `Task.WhenAll` işareti, olası bir Midway kilitlenmesinin veya yeniden başlatmanın zaten tamamlanmış bir görevin yeniden başlatılmasını gerektirmemesini sağlar.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const parallelTasks = [];

    // Get a list of N work items to process in parallel.
    const workBatch = yield context.df.callActivity("F1");
    for (let i = 0; i < workBatch.length; i++) {
        parallelTasks.push(context.df.callActivity("F2", workBatch[i]));
    }

    yield context.df.Task.all(parallelTasks);

    // Aggregate all N outputs and send the result to F3.
    const sum = parallelTasks.reduce((prev, curr) => prev + curr, 0);
    yield context.df.callActivity("F3", sum);
});
```

Fanı-Out işi işlevin birden çok örneğine dağıtılır `F2` . İş, dinamik bir görev listesi kullanılarak izlenir. `context.df.Task.all` Çağrılan tüm işlevlerin bitmesini beklemek için API çağırılır. Sonra, `F2` işlev çıkışları dinamik görev listesinden toplanır ve `F3` işleve geçirilir.

Çağrısında gerçekleşen otomatik onay `yield` `context.df.Task.all` işareti, olası bir Midway kilitlenmesinin veya yeniden başlatmanın zaten tamamlanmış bir görevin yeniden başlatılmasını gerektirmemesini sağlar.

# <a name="python"></a>[Python](#tab/python)

```python
import azure.durable_functions as df


def orchestrator_function(context: df.DurableOrchestrationContext):
    # Get a list of N work items to process in parallel.
    work_batch = yield context.call_activity("F1", None)

    parallel_tasks = [ context.call_activity("F2", b) for b in work_batch ]
    
    outputs = yield context.task_all(parallel_tasks)

    # Aggregate all N outputs and send the result to F3.
    total = sum(outputs)
    yield context.call_activity("F3", total)


main = df.Orchestrator.create(orchestrator_function)
```

Fanı-Out işi işlevin birden çok örneğine dağıtılır `F2` . İş, dinamik bir görev listesi kullanılarak izlenir. `context.task_all` Çağrılan tüm işlevlerin bitmesini beklemek için API çağırılır. Sonra, `F2` işlev çıkışları dinamik görev listesinden toplanır ve `F3` işleve geçirilir.

Çağrısında gerçekleşen otomatik onay `yield` `context.task_all` işareti, olası bir Midway kilitlenmesinin veya yeniden başlatmanın zaten tamamlanmış bir görevin yeniden başlatılmasını gerektirmemesini sağlar.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```PowerShell
param($Context)

# Get a list of work items to process in parallel.
$WorkBatch = Invoke-ActivityFunction -FunctionName 'F1'

$ParallelTasks =
    foreach ($WorkItem in $WorkBatch) {
        Invoke-ActivityFunction -FunctionName 'F2' -Input $WorkItem -NoWait
    }

$Outputs = Wait-ActivityFunction -Task $ParallelTasks

# Aggregate all outputs and send the result to F3.
$Total = ($Outputs | Measure-Object -Sum).Sum
Invoke-ActivityFunction -FunctionName 'F3' -Input $Total
```

Fanı-Out işi işlevin birden çok örneğine dağıtılır `F2` . Lütfen `NoWait` işlev çağrısında anahtarın kullanımını unutmayın `F2` : Bu anahtar, Orchestrator 'ın etkinlik tamamlama olmadan çağırma işlemine devam etmesine olanak sağlar `F2` . İş, dinamik bir görev listesi kullanılarak izlenir. `Wait-ActivityFunction`Komutu çağrılan tüm işlevlerin bitmesini beklemek için çağırılır. Sonra, `F2` işlev çıkışları dinamik görev listesinden toplanır ve `F3` işleve geçirilir.

Çağrıda gerçekleşen otomatik onay `Wait-ActivityFunction` işareti, olası bir Midway kilitlenmesinin veya yeniden başlatmanın zaten tamamlanmış bir görevin yeniden başlatılmasını gerektirmemesini sağlar.

---

> [!NOTE]
> Nadir koşullarda, bir etkinlik işlevi tamamlandıktan sonra ancak tamamlanma alanı düzenleme geçmişine kaydedilmeden önce, bir kilitlenme olması mümkündür. Bu durumda, etkinlik işlevi işlem kurtardıktan sonra baştan sonra yeniden çalıştırılır.

### <a name="pattern-3-async-http-apis"></a><a name="async-http"></a>Model #3: zaman uyumsuz HTTP API 'Leri

Zaman uyumsuz HTTP API 'SI stili, uzun süreli işlemlerin durumunu dış istemcilerle koordine etme sorununu ele alınmaktadır. Bu düzenin uygulanması için ortak bir yol, bir HTTP uç noktasının uzun süreli eylemi tetiklemesine sahip olunmalıdır. Daha sonra, istemciyi, işlem bittiğinde öğrenmek için yokladığı bir durum uç noktasına yönlendirin.

![HTTP API deseninin diyagramı](./media/durable-functions-concepts/async-http-api.png)

Dayanıklı İşlevler, bu düzene yönelik **yerleşik destek** sağlar, bu sayede yazmanız gereken kod, uzun süre çalışan işlev yürütmeleri ile etkileşime geçmek için gerekir. Örneğin, Dayanıklı İşlevler hızlı başlangıç örnekleri ([C#](durable-functions-create-first-csharp.md) ve [JavaScript](quickstart-js-vscode.md)), yeni Orchestrator işlev örnekleri başlatmak IÇIN kullanabileceğiniz bir basit rest komutu gösterir. Bir örnek başlatıldıktan sonra uzantı, Orchestrator işlev durumunu sorgulayan Web kancası HTTP API 'Lerini kullanıma sunar. 

Aşağıdaki örnekte, bir Orchestrator başlatan ve durumunu sorgulayan REST komutları gösterilmektedir. Anlaşılırsa, bazı protokol ayrıntıları örnekteki atlanır.

```
> curl -X POST https://myfunc.azurewebsites.net/api/orchestrators/DoWork -H "Content-Length: 0" -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/instances/b79baf67f717453ca9e86c5da21e03ec

{"id":"b79baf67f717453ca9e86c5da21e03ec", ...}

> curl https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/instances/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/instances/b79baf67f717453ca9e86c5da21e03ec

{"runtimeStatus":"Running","lastUpdatedTime":"2019-03-16T21:20:47Z", ...}

> curl https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/instances/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 200 OK
Content-Length: 175
Content-Type: application/json

{"runtimeStatus":"Completed","lastUpdatedTime":"2019-03-16T21:20:57Z", ...}
```

Dayanıklı İşlevler Runtime, durumu sizin için yönettiğinden, kendi durum izleme mekanizmanızı uygulamanız gerekmez.

Dayanıklı İşlevler uzantısı, uzun süre çalışan düzenlemeleri yöneten yerleşik HTTP API 'Lerini kullanıma sunar. Bunun yerine kendi işlev tetikleyiclerinizi (HTTP, kuyruk veya Azure Event Hubs gibi) ve [Orchestration istemci bağlamasını](durable-functions-bindings.md#orchestration-client)kullanarak kendiniz uygulayabilirsiniz. Örneğin, sonlandırma tetiklenmesi için bir kuyruk iletisi kullanabilirsiniz. Ya da, kimlik doğrulaması için oluşturulmuş bir anahtar kullanan yerleşik HTTP API 'Leri yerine Azure Active Directory kimlik doğrulama ilkesi tarafından korunan bir HTTP tetikleyicisi kullanabilirsiniz.

Daha fazla bilgi için, Dayanıklı İşlevler uzantısını kullanarak HTTP üzerinden zaman uyumsuz, uzun süreli işlemleri nasıl sergilebileceğinizi anlatan [http özellikleri](durable-functions-http-features.md) makalesine bakın.

### <a name="pattern-4-monitor"></a><a name="monitoring"></a>#4 Model: Izleyici

İzleyici deseninin bir iş akışında esnek ve yinelenen bir işlem anlamına gelir. Belirli koşullar karşılanana kadar bir örnek yoklanıyor. Düzenli bir temizleme işi gibi temel bir senaryoya yönelik olarak normal bir [Zamanlayıcı tetikleyicisi](../functions-bindings-timer.md) kullanabilirsiniz, ancak aralığı statiktir ve örnek yaşam sürelerinin yönetilmesi karmaşık hale gelir. Esnek yineleme aralıkları oluşturmak, görev yaşam sürelerini yönetmek ve tek bir düzenleme işleminden birden çok izleme işlemi oluşturmak için Dayanıklı İşlevler kullanabilirsiniz.

İzleyici deseninin bir örneği, önceki zaman uyumsuz HTTP API senaryosunun tersine çevrilmeye yönelik bir örnektir. Bir dış istemcinin uzun süreli bir işlemi izlemeye yönelik bir uç nokta göstermek yerine, uzun süre çalışan izleyici bir dış uç nokta kullanır ve ardından bir durum değişikliği bekler.

![İzleyici deseninin diyagramı](./media/durable-functions-concepts/monitor.png)

Birkaç kod satırı içinde, rastgele uç noktaları gözlemleyecek birden çok izleyici oluşturmak için Dayanıklı İşlevler kullanabilirsiniz. İzleyiciler, bir koşul karşılandığında çalışmayı sonlandırabilir veya başka bir işlev, izleyicileri sonlandırmak için dayanıklı düzenleme istemcisini kullanabilir. Bir izleyicinin `wait` aralığını belirli bir koşula göre değiştirebilirsiniz (örneğin üstel geri alma) 

Aşağıdaki kod temel bir izleyiciyi uygular:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("MonitorJobStatus")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    int jobId = context.GetInput<int>();
    int pollingInterval = GetPollingInterval();
    DateTime expiryTime = GetExpiryTime();

    while (context.CurrentUtcDateTime < expiryTime)
    {
        var jobStatus = await context.CallActivityAsync<string>("GetJobStatus", jobId);
        if (jobStatus == "Completed")
        {
            // Perform an action when a condition is met.
            await context.CallActivityAsync("SendAlert", machineId);
            break;
        }

        // Orchestration sleeps until this time.
        var nextCheck = context.CurrentUtcDateTime.AddSeconds(pollingInterval);
        await context.CreateTimer(nextCheck, CancellationToken.None);
    }

    // Perform more work here, or let the orchestration end.
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const jobId = context.df.getInput();
    const pollingInterval = getPollingInterval();
    const expiryTime = getExpiryTime();

    while (moment.utc(context.df.currentUtcDateTime).isBefore(expiryTime)) {
        const jobStatus = yield context.df.callActivity("GetJobStatus", jobId);
        if (jobStatus === "Completed") {
            // Perform an action when a condition is met.
            yield context.df.callActivity("SendAlert", machineId);
            break;
        }

        // Orchestration sleeps until this time.
        const nextCheck = moment.utc(context.df.currentUtcDateTime).add(pollingInterval, 's');
        yield context.df.createTimer(nextCheck.toDate());
    }

    // Perform more work here, or let the orchestration end.
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.durable_functions as df
import json
from datetime import timedelta 


def orchestrator_function(context: df.DurableOrchestrationContext):
    job = json.loads(context.get_input())
    job_id = job["jobId"]
    polling_interval = job["pollingInterval"]
    expiry_time = job["expiryTime"]

    while context.current_utc_datetime < expiry_time:
        job_status = yield context.call_activity("GetJobStatus", job_id)
        if job_status == "Completed":
            # Perform an action when a condition is met.
            yield context.call_activity("SendAlert", job_id)
            break

        # Orchestration sleeps until this time.
        next_check = context.current_utc_datetime + timedelta(seconds=polling_interval)
        yield context.create_timer(next_check)

    # Perform more work here, or let the orchestration end.


main = df.Orchestrator.create(orchestrator_function)
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

İzleyici Şu anda PowerShell 'de desteklenmiyor.

---

Bir istek alındığında, bu iş KIMLIĞI için yeni bir düzenleme örneği oluşturulur. Örnek, bir koşul karşılanana ve döngünün çıkış yapılıncaya kadar bir durumu yoklar. Dayanıklı bir Zamanlayıcı yoklama aralığını denetler. Daha sonra, daha fazla iş gerçekleştirilebilir veya düzenleme sona erdirmek üzere. `nextCheck`Aştığında `expiryTime` , izleyici sona erer.

### <a name="pattern-5-human-interaction"></a><a name="human"></a>#5 model: Insan etkileşimi

Birçok otomatikleştirilmiş süreç, bazı insan etkileşimini içerir. İnsanlar yüksek düzeyde kullanılabilir olmadığından ve bulut hizmetleri olarak yanıt veremediği için otomatikleştirilmiş bir işlem içindeki insanların dahil olması önemlidir. Otomatik bir işlem, zaman aşımları ve maaş mantığı kullanılarak bu etkileşime izin verebilir.

Onay süreci, insan etkileşimini içeren bir iş sürecinin örneğidir. Belirli bir dolar tutarını aşan bir gider raporu için bir yöneticinin onayı gerekli olabilir. Yönetici, 72 saat içinde gider raporunu onaylamaz (örneğin, bu da yönetici tatilde durumunda olursa), bir yükseltme işlemi, başka birinden (Belki de yöneticinin Yöneticisi) onay almak için ' de açılır.

![İnsan etkileşimi deseninin diyagramı](./media/durable-functions-concepts/approval.png)

Bu örnekte, bir Orchestrator işlevi kullanarak bir stili uygulayabilirsiniz. Orchestrator onay istemek için [dayanıklı bir Zamanlayıcı](durable-functions-timers.md) kullanır. Zaman aşımı oluşursa Orchestrator ilerletir. Orchestrator, bir insan etkileşimi tarafından oluşturulan bildirim gibi bir [dış olay](durable-functions-external-events.md)bekler.

Bu örnekler insan etkileşimi modelini göstermek için bir onay işlemi oluşturur:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("ApprovalWorkflow")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    await context.CallActivityAsync("RequestApproval", null);
    using (var timeoutCts = new CancellationTokenSource())
    {
        DateTime dueTime = context.CurrentUtcDateTime.AddHours(72);
        Task durableTimeout = context.CreateTimer(dueTime, timeoutCts.Token);

        Task<bool> approvalEvent = context.WaitForExternalEvent<bool>("ApprovalEvent");
        if (approvalEvent == await Task.WhenAny(approvalEvent, durableTimeout))
        {
            timeoutCts.Cancel();
            await context.CallActivityAsync("ProcessApproval", approvalEvent.Result);
        }
        else
        {
            await context.CallActivityAsync("Escalate", null);
        }
    }
}
```

Dayanıklı Zamanlayıcı 'yı oluşturmak için çağrısı yapın `context.CreateTimer` . Bildirim tarafından alınır `context.WaitForExternalEvent` . Ardından, `Task.WhenAny` ilerletilip yükseltilmeyeceğine karar vermek için çağrılır (Öncelikle zaman aşımı olur) veya onay işlemini işleyin (onay zaman aşımından önce alınır).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");
const moment = require('moment');

module.exports = df.orchestrator(function*(context) {
    yield context.df.callActivity("RequestApproval");

    const dueTime = moment.utc(context.df.currentUtcDateTime).add(72, 'h');
    const durableTimeout = context.df.createTimer(dueTime.toDate());

    const approvalEvent = context.df.waitForExternalEvent("ApprovalEvent");
    if (approvalEvent === yield context.df.Task.any([approvalEvent, durableTimeout])) {
        durableTimeout.cancel();
        yield context.df.callActivity("ProcessApproval", approvalEvent.result);
    } else {
        yield context.df.callActivity("Escalate");
    }
});
```

Dayanıklı Zamanlayıcı 'yı oluşturmak için çağrısı yapın `context.df.createTimer` . Bildirim tarafından alınır `context.df.waitForExternalEvent` . Ardından, `context.df.Task.any` ilerletilip yükseltilmeyeceğine karar vermek için çağrılır (Öncelikle zaman aşımı olur) veya onay işlemini işleyin (onay zaman aşımından önce alınır).

# <a name="python"></a>[Python](#tab/python)

```python
import azure.durable_functions as df
import json
from datetime import timedelta 


def orchestrator_function(context: df.DurableOrchestrationContext):
    yield context.call_activity("RequestApproval", None)

    due_time = context.current_utc_datetime + timedelta(hours=72)
    durable_timeout_task = context.create_timer(due_time)
    approval_event_task = context.wait_for_external_event("ApprovalEvent")

    winning_task = yield context.task_any([approval_event_task, durable_timeout_task])

    if approval_event_task == winning_task:
        durable_timeout_task.cancel()
        yield context.call_activity("ProcessApproval", approval_event_task.result)
    else:
        yield context.call_activity("Escalate", None)


main = df.Orchestrator.create(orchestrator_function)
```

Dayanıklı Zamanlayıcı 'yı oluşturmak için çağrısı yapın `context.create_timer` . Bildirim tarafından alınır `context.wait_for_external_event` . Ardından, `context.task_any` ilerletilip yükseltilmeyeceğine karar vermek için çağrılır (Öncelikle zaman aşımı olur) veya onay işlemini işleyin (onay zaman aşımından önce alınır).

# <a name="powershell"></a>[PowerShell](#tab/powershell)

İnsan etkileşimi Şu anda PowerShell 'de desteklenmiyor.

---

Dış istemci, [YERLEŞIK HTTP API 'lerini](durable-functions-http-api.md#raise-event)kullanarak olay bildirimini bekleyen bir Orchestrator işlevine teslim edebilir:

```bash
curl -d "true" http://localhost:7071/runtime/webhooks/durabletask/instances/{instanceId}/raiseEvent/ApprovalEvent -H "Content-Type: application/json"
```

Aynı zamanda aynı işlev uygulamasındaki başka bir işlevden dayanıklı düzenleme istemcisi kullanılarak bir olay da oluşturulabilir:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("RaiseEventToOrchestration")]
public static async Task Run(
    [HttpTrigger] string instanceId,
    [DurableClient] IDurableOrchestrationClient client)
{
    bool isApproved = true;
    await client.RaiseEventAsync(instanceId, "ApprovalEvent", isApproved);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const isApproved = true;
    await client.raiseEvent(instanceId, "ApprovalEvent", isApproved);
};
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.durable_functions as df


async def main(client: str):
    durable_client = df.DurableOrchestrationClient(client)
    is_approved = True
    await durable_client.raise_event(instance_id, "ApprovalEvent", is_approved)
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

İnsan etkileşimi Şu anda PowerShell 'de desteklenmiyor.

---

### <a name="pattern-6-aggregator-stateful-entities"></a><a name="aggregator"></a>#6 Model: toplayıcı (durum bilgisi olan varlıklar)

Altıncı model, olay verilerini bir süre içinde tek bir adreslenebilir *varlığa* toplamak üzere kullanılır. Bu düzende, toplanan veriler birden çok kaynaktan gelebilir, toplu olarak teslim edilebilir veya uzun sürelerle dağılmış olabilir. Toplayıcı, ulaşan olay verileri üzerinde işlem yapması gerekebilir ve dış istemcilerin toplanan verileri sorgulaması gerekebilir.

![Toplayıcı diyagramı](./media/durable-functions-concepts/aggregator.png)

Bu düzenin normal, durum bilgisiz işlevlerle uygulamaya çalışılması, eşzamanlılık denetiminin çok büyük bir zorluk haline gelmesi. Aynı anda aynı verileri değiştiren birden çok iş parçacığı hakkında endişelenmeniz gerekmez, ayrıca toplayıcı 'nın aynı anda yalnızca tek bir VM üzerinde çalışmasını sağlamaya de endişelenmeniz gerekir.

Bu kalıbı tek bir işlev olarak kolayca uygulamak için [dayanıklı varlıkları](durable-functions-entities.md) kullanabilirsiniz.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    int currentValue = ctx.GetState<int>();
    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            int amount = ctx.GetInput<int>();
            ctx.SetState(currentValue + amount);
            break;
        case "reset":
            ctx.SetState(0);
            break;
        case "get":
            ctx.Return(currentValue);
            break;
    }
}
```

Dayanıklı varlıklar, .NET ' de sınıflar olarak modellenebilir. Bu model, işlem listesi düzeltildiğinde ve büyük olursa yararlı olabilir. Aşağıdaki örnek, `Counter` .NET sınıfları ve yöntemleri kullanılarak varlığın eşdeğer bir uygulamasıdır.

```csharp
public class Counter
{
    [JsonProperty("value")]
    public int CurrentValue { get; set; }

    public void Add(int amount) => this.CurrentValue += amount;

    public void Reset() => this.CurrentValue = 0;

    public int Get() => this.CurrentValue;

    [FunctionName(nameof(Counter))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<Counter>();
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.entity(function(context) {
    const currentValue = context.df.getState(() => 0);
    switch (context.df.operationName) {
        case "add":
            const amount = context.df.getInput();
            context.df.setState(currentValue + amount);
            break;
        case "reset":
            context.df.setState(0);
            break;
        case "get":
            context.df.return(currentValue);
            break;
    }
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import logging
import json

import azure.functions as func
import azure.durable_functions as df


def entity_function(context: df.DurableOrchestrationContext):

    current_value = context.get_state(lambda: 0)
    operation = context.operation_name
    if operation == "add":
        amount = context.get_input()
        current_value += amount
        context.set_result(current_value)
    elif operation == "reset":
        current_value = 0
    elif operation == "get":
        context.set_result(current_value)
    
    context.set_state(current_value)

main = df.Entity.create(entity_function)
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Kalıcı varlıklar Şu anda PowerShell 'de desteklenmemektedir.

---

İstemciler, [varlık istemci bağlamasını](durable-functions-bindings.md#entity-client)kullanarak bir varlık işlevi için ("sinyal" olarak da bilinir) *işlemleri* sıraya alabilir.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static async Task Run(
    [EventHubTrigger("device-sensor-events")] EventData eventData,
    [DurableClient] IDurableEntityClient entityClient)
{
    var metricType = (string)eventData.Properties["metric"];
    var delta = BitConverter.ToInt32(eventData.Body, eventData.Body.Offset);

    // The "Counter/{metricType}" entity is created on-demand.
    var entityId = new EntityId("Counter", metricType);
    await entityClient.SignalEntityAsync(entityId, "add", delta);
}
```

> [!NOTE]
> Dinamik olarak oluşturulan proxy 'ler, sinyal varlıkları için tür açısından güvenli bir şekilde .NET içinde de kullanılabilir. Ayrıca, istemciler, düzenleme istemci bağlamasında [tür kullanımı uyumlu yöntemler](durable-functions-bindings.md#entity-client-usage) kullanarak bir varlık işlevinin durumunu da sorgulayabilir.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    await context.df.signalEntity(entityId, "add", 1);
};
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df


async def main(req: func.HttpRequest, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)
    entity_id = df.EntityId("Counter", "myCounter")
    instance_id = await client.signal_entity(entity_id, "add", 1)
    return func.HttpResponse("Entity signaled")
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Kalıcı varlıklar Şu anda PowerShell 'de desteklenmemektedir.

---

Varlık işlevleri C#, JavaScript ve Python için [Dayanıklı İşlevler 2,0](durable-functions-versions.md) ve üzeri sürümlerde kullanılabilir.

## <a name="the-technology"></a>Teknoloji

Arka planda Dayanıklı İşlevler uzantısı, GitHub 'daki açık kaynaklı ve kodda iş akışları oluşturmak için kullanılan açık kaynaklı bir kitaplık olan [dayanıklı görev çerçevesinin](https://github.com/Azure/durabletask)üzerine kurulmuştur. Azure Işlevleri gibi, Azure WebJobs 'ın sunucusuz bir gelişimi olduğundan, Dayanıklı İşlevler dayanıklı görev çerçevesinin sunucusuz bir gelişmidir. Microsoft ve diğer kuruluşlar, görev açısından kritik işlemleri otomatik hale getirmek için dayanıklı görev çerçevesini yoğun bir şekilde kullanır. Sunucusuz Azure Işlevleri ortamına yönelik doğal bir uyum.

## <a name="code-constraints"></a>Kod kısıtlamaları

Güvenilir ve uzun süreli yürütme garantisi sağlamak için, Orchestrator işlevlerinin izlenmesi gereken bir dizi kodlama kuralı vardır. Daha fazla bilgi için bkz. [Orchestrator işlev kodu kısıtlamaları](durable-functions-code-constraints.md) makalesi.

## <a name="billing"></a>Faturalandırma

Dayanıklı İşlevler Azure Işlevleri ile aynı faturalandırılır. Daha fazla bilgi için bkz. [Azure işlevleri fiyatlandırması](https://azure.microsoft.com/pricing/details/functions/). Azure Işlevleri [Tüketim planında](../consumption-plan.md)Orchestrator işlevleri çalıştırıldığında, bazı fatura davranışları göz önünde bulundurulmalıdır. Bu davranışlar hakkında daha fazla bilgi için [dayanıklı işlevler faturalandırma](durable-functions-billing.md) makalesine bakın.

## <a name="jump-right-in"></a>Hemen geç

Bu dile özgü hızlı başlangıç öğreticilerden birini tamamlayarak 10 dakikadan kısa bir süre içinde Dayanıklı İşlevler kullanmaya başlamanızı sağlayabilirsiniz:

* [Visual Studio 2019 kullanarak C#](durable-functions-create-first-csharp.md)
* [Visual Studio Code kullanan JavaScript](quickstart-js-vscode.md)
* [Visual Studio Code kullanarak Python](quickstart-python-vscode.md)
* [Visual Studio Code kullanarak PowerShell](quickstart-powershell-vscode.md)

Bu hızlı başlangıçlarda, "Hello World" dayanıklı işlevini yerel olarak oluşturup test edersiniz. Ardından işlev kodunu Azure’da yayımlayacaksınız. Oluşturduğunuz işlev, diğer işlevlere yapılan çağrıları düzenler ve birbirine zincirler.

## <a name="learn-more"></a>Daha fazla bilgi edinin

Aşağıdaki videoda Dayanıklı İşlevler avantajları vurgulanmıştır:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Durable-Functions-in-Azure-Functions/player] 

Dayanıklı İşlevler ve temel alınan teknolojinin daha ayrıntılı bir tartışması için aşağıdaki videoya bakın (.NET 'e odaklanılmıştır, ancak kavramlar desteklenen diğer diller için de geçerlidir):

> [!VIDEO https://channel9.msdn.com/Events/dotnetConf/2018/S204/player]

Dayanıklı İşlevler [Azure işlevleri](../functions-overview.md)için gelişmiş bir uzantı olduğundan, tüm uygulamalar için uygun değildir. Diğer Azure düzenleme teknolojileriyle bir karşılaştırma için bkz. [Azure Işlevlerini karşılaştırma ve Azure Logic Apps](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-azure-functions-and-azure-logic-apps).

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [İşlev türlerini ve özellikleri Dayanıklı İşlevler](durable-functions-types-features-overview.md)
