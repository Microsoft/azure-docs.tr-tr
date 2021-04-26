---
title: Azure Işlevleri için Azure Event Grid tetikleyicisi
description: Azure Işlevlerinde Event Grid olaylar başlatıldığında kodu çalıştırmayı öğrenin.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: devx-track-csharp, fasttrack-edit, devx-track-python
ms.openlocfilehash: 17968f2c137eef51eecdb6c7098c7056944dc970
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782198"
---
# <a name="azure-event-grid-trigger-for-azure-functions"></a>Azure Işlevleri için Azure Event Grid tetikleyicisi

Bir Event Grid konusuna gönderilen olaya yanıt vermek için işlev tetikleyicisini kullanın.

Kurulum ve yapılandırma ayrıntıları hakkında bilgi için bkz. [genel bakış](./functions-bindings-event-grid.md).

## <a name="example"></a>Örnek

# <a name="c"></a>[C#](#tab/csharp)

HTTP tetikleyicisi örneği için bkz. [http uç noktasına olay alma](../event-grid/receive-events.md).

### <a name="c-2x-and-higher"></a>C# (2. x ve üzeri)

Aşağıdaki örnek, öğesine bağlanan bir [C# işlevini](functions-dotnet-class-library.md) göstermektedir `EventGridEvent` :

```cs
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace Company.Function
{
    public static class EventGridTriggerCSharp
    {
        [FunctionName("EventGridTest")]
        public static void EventGridTest([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
        {
            log.LogInformation(eventGridEvent.Data.ToString());
        }
    }
}
```

Daha fazla bilgi için bkz. paketler, [öznitelikler](#attributes-and-annotations), [yapılandırma](#configuration)ve [kullanım](#usage).

### <a name="version-1x"></a>Sürüm 1. x

Aşağıdaki örnek, öğesine bağlanan bir Işlevler 1. x [C# işlevini](functions-dotnet-class-library.md) gösterir `JObject` :

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Azure.WebJobs.Host;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using Microsoft.Extensions.Logging;

namespace Company.Function
{
    public static class EventGridTriggerCSharp
    {
        [FunctionName("EventGridTriggerCSharp")]
        public static void Run([EventGridTrigger]JObject eventGridEvent, ILogger log)
        {
            log.LogInformation(eventGridEvent.ToString(Formatting.Indented));
        }
    }
}
```

# <a name="c-script"></a>[C# betiği](#tab/csharp-script)

Aşağıdaki örnek, bir *function.js* dosyadaki bir tetikleyiciyi ve bağlamayı kullanan bir [C# betik işlevini](functions-reference-csharp.md) gösterir.

Dosyadaki *function.js* bağlama verileri aşağıda verilmiştir:

```json
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

### <a name="version-2x-and-higher"></a>Sürüm 2. x ve üzeri

Buraya bağlanan bir örnek aşağıda verilmiştir `EventGridEvent` :

```csharp
#r "Microsoft.Azure.EventGrid"
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(EventGridEvent eventGridEvent, ILogger log)
{
    log.LogInformation(eventGridEvent.Data.ToString());
}
```

Daha fazla bilgi için bkz. paketler, [öznitelikler](#attributes-and-annotations), [yapılandırma](#configuration)ve [kullanım](#usage).

### <a name="version-1x"></a>Sürüm 1. x

Aşağıdaki IŞLEVLERE bağlanan 1. x C# betik kodu aşağıda verilmiştir `JObject` :

```cs
#r "Newtonsoft.Json"

using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

public static void Run(JObject eventGridEvent, TraceWriter log)
{
    log.Info(eventGridEvent.ToString(Formatting.Indented));
}
```

# <a name="java"></a>[Java](#tab/java)

Bu bölüm aşağıdaki örnekleri içerir:

* [Event Grid tetikleyicisi, dize parametresi](#event-grid-trigger-string-parameter)
* [Event Grid tetikleyicisi, POJO parametresi](#event-grid-trigger-pojo-parameter)

Aşağıdaki örneklerde,, ilk olarak olayı [](functions-reference-java.md) `String` bir Pojo olarak ve ikinci olarak alarak, bağlamayı kullanan ve bir olayı yazdıran Java 'daki tetikleyici bağlama gösterilmektedir.

### <a name="event-grid-trigger-string-parameter"></a>Event Grid tetikleyicisi, dize parametresi

```java
  @FunctionName("eventGridMonitorString")
  public void logEvent(
    @EventGridTrigger(
      name = "event"
    ) 
    String content, 
    final ExecutionContext context) {
      context.getLogger().info("Event content: " + content);      
  }
```

### <a name="event-grid-trigger-pojo-parameter"></a>Event Grid tetikleyicisi, POJO parametresi

Bu örnek, bir Event Grid olayının en üst düzey özelliklerini temsil eden aşağıdaki POJO 'ları kullanır:

```java
import java.util.Date;
import java.util.Map;

public class EventSchema {

  public String topic;
  public String subject;
  public String eventType;
  public Date eventTime;
  public String id;
  public String dataVersion;
  public String metadataVersion;
  public Map<String, Object> data;

}
```

Varış sonrasında, olayın JSON yükü, ```EventSchema``` işlev tarafından kullanılmak üzere Pojo 'ya de serileştirilir. Bu işlem, işlevin bir nesne yönelimli bir şekilde olay özelliklerine erişmesine olanak tanır.

```java
  @FunctionName("eventGridMonitor")
  public void logEvent(
    @EventGridTrigger(
      name = "event"
    ) 
    EventSchema event, 
    final ExecutionContext context) {
      context.getLogger().info("Event content: ");
      context.getLogger().info("Subject: " + event.subject);
      context.getLogger().info("Time: " + event.eventTime); // automatically converted to Date by the runtime
      context.getLogger().info("Id: " + event.id);
      context.getLogger().info("Data: " + event.data);
  }
```

[Java işlevleri çalışma zamanı kitaplığı](/java/api/overview/azure/functions/runtime)'nda, `EventGridTrigger` değeri eventgrid 'den gelen parametrelerde ek açıklama kullanın. Bu ek açıklamaların bulunduğu parametreler, bir olay geldiğinde işlevin çalışmasına neden olur.  Bu ek açıklama, kullanılarak yerel Java türleri, POJOs veya null atanabilir değerlerle kullanılabilir `Optional<T>` .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Aşağıdaki örnek, bir *function.js* dosyadaki bir tetikleyiciyi ve bağlamayı kullanan bir [JavaScript işlevini](functions-reference-node.md) gösterir.

Dosyadaki *function.js* bağlama verileri aşağıda verilmiştir:

```json
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

JavaScript kodu aşağıda verilmiştir:

```javascript
module.exports = function (context, eventGridEvent) {
    context.log("JavaScript Event Grid function processed a request.");
    context.log("Subject: " + eventGridEvent.subject);
    context.log("Time: " + eventGridEvent.eventTime);
    context.log("Data: " + JSON.stringify(eventGridEvent.data));
    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Aşağıdaki örnek, dosyasındaki *function.js* bir Event Grid tetikleyicisi bağlamasının nasıl yapılandırılacağını gösterir.

```powershell
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    }
  ]
}
```

Event Grid olayı, `eventGridEvent` aşağıdaki PowerShell örneğinde gösterildiği gibi, işlev için adlı bir parametre aracılığıyla kullanılabilir hale getirilir.

```powershell
param($eventGridEvent, $TriggerMetadata)

# Make sure to pass hashtables to Out-String so they're logged correctly
$eventGridEvent | Out-String | Write-Host
```

# <a name="python"></a>[Python](#tab/python)

Aşağıdaki örnek, bir *function.js* dosyadaki bir tetikleyiciyi ve bağlamayı kullanan bir [Python işlevini](functions-reference-python.md) gösterir.

Dosyadaki *function.js* bağlama verileri aşağıda verilmiştir:

```json
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "event",
      "direction": "in"
    }
  ],
  "disabled": false,
  "scriptFile": "__init__.py"
}
```

Python kodu aşağıda verilmiştir:

```python
import json
import logging

import azure.functions as func

def main(event: func.EventGridEvent):

    result = json.dumps({
        'id': event.id,
        'data': event.get_json(),
        'topic': event.topic,
        'subject': event.subject,
        'event_type': event.event_type,
    })

    logging.info('Python EventGrid trigger processed an event: %s', result)
```

---

## <a name="attributes-and-annotations"></a>Öznitelikler ve ek açıklamalar

# <a name="c"></a>[C#](#tab/csharp)

[C# sınıf kitaplıklarında](functions-dotnet-class-library.md) [eventgridtrigger](https://github.com/Azure/azure-functions-eventgrid-extension/blob/master/src/EventGridExtension/TriggerBinding/EventGridTriggerAttribute.cs) özniteliğini kullanın.

`EventGridTrigger`Bir yöntem imzasında bir özniteliği aşağıda verilmiştir:

```csharp
[FunctionName("EventGridTest")]
public static void EventGridTest([EventGridTrigger] JObject eventGridEvent, ILogger log)
{
    ...
}
```

Tüm örnek için bkz. C# örneği.

# <a name="c-script"></a>[C# betiği](#tab/csharp-script)

Öznitelikler C# betiği tarafından desteklenmez.

# <a name="java"></a>[Java](#tab/java)

[Eventgridtrigger](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/annotation/EventGridTrigger.java) ek açıklaması, yapılandırma değerlerini sağlayarak bir Event Grid bağlamasını bildirimli olarak yapılandırmanızı sağlar. Daha ayrıntılı bilgi için bkz. [örnek](#example) ve [yapılandırma](#configuration) bölümleri.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Öznitelikler JavaScript tarafından desteklenmez.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Öznitelikler PowerShell tarafından desteklenmez.

# <a name="python"></a>[Python](#tab/python)

Öznitelikler Python tarafından desteklenmez.

---

## <a name="configuration"></a>Yapılandırma

Aşağıdaki tabloda, dosyasında *function.js* ayarladığınız bağlama yapılandırma özellikleri açıklanmaktadır. Öznitelikte ayarlanacak bir oluşturucu parametresi veya özellik yok `EventGridTrigger` .

|function.jsözelliği |Description|
|---------|---------|
| **türüyle** | Gerekli-olarak ayarlanmalıdır `eventGridTrigger` . |
| **yön** | Gerekli-olarak ayarlanmalıdır `in` . |
| **ada** | Gerekli-olay verilerini alan parametre için işlev kodunda kullanılan değişken adı. |

## <a name="usage"></a>Kullanım

# <a name="c"></a>[C#](#tab/csharp)

Azure Işlevleri 1. x içinde Event Grid tetikleyicisi için aşağıdaki parametre türlerini kullanabilirsiniz:

* `JObject`
* `string`

Azure Işlevleri 2. x ve üzeri sürümlerde, Event Grid tetikleyicisi için aşağıdaki parametre türünü kullanma seçeneğiniz de vardır:

* `Microsoft.Azure.EventGrid.Models.EventGridEvent`-Tüm olay türlerinde ortak olan alanların özelliklerini tanımlar.

> [!NOTE]
> Işlev v1 ' de bağlamaya çalışırsanız `Microsoft.Azure.WebJobs.Extensions.EventGrid.EventGridEvent` , derleyici "kullanım dışı" iletisini görüntüler ve `Microsoft.Azure.EventGrid.Models.EventGridEvent` bunun yerine kullanmanız önerilir. Daha yeni türü kullanmak için [Microsoft. Azure. EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid) NuGet paketine başvurun ve `EventGridEvent` tür adını ile önek olarak niteleyin `Microsoft.Azure.EventGrid.Models` .

# <a name="c-script"></a>[C# betiği](#tab/csharp-script)

Azure Işlevleri 1. x içinde Event Grid tetikleyicisi için aşağıdaki parametre türlerini kullanabilirsiniz:

* `JObject`
* `string`

Azure Işlevleri 2. x ve üzeri sürümlerde, Event Grid tetikleyicisi için aşağıdaki parametre türünü kullanma seçeneğiniz de vardır:

* `Microsoft.Azure.EventGrid.Models.EventGridEvent`-Tüm olay türlerinde ortak olan alanların özelliklerini tanımlar.

> [!NOTE]
> Işlev v1 ' de bağlamaya çalışırsanız `Microsoft.Azure.WebJobs.Extensions.EventGrid.EventGridEvent` , derleyici "kullanım dışı" iletisini görüntüler ve `Microsoft.Azure.EventGrid.Models.EventGridEvent` bunun yerine kullanmanız önerilir. Daha yeni türü kullanmak için [Microsoft. Azure. EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid) NuGet paketine başvurun ve `EventGridEvent` tür adını ile önek olarak niteleyin `Microsoft.Azure.EventGrid.Models` . Bir C# betik işlevindeki NuGet paketlerine başvurma hakkında daha fazla bilgi için bkz. [NuGet paketlerini kullanma](functions-reference-csharp.md#using-nuget-packages)

# <a name="java"></a>[Java](#tab/java)

Event Grid olay örneği, özniteliğiyle ilişkilendirilmiş parametresi aracılığıyla kullanılabilir `EventGridTrigger` , bir `EventSchema` . Daha fazla ayrıntı için [örneğe](#example) bakın.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Event Grid örneği, dosyanın özelliğinde *function.js* yapılandırılan parametre aracılığıyla kullanılabilir `name` .

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Event Grid örneği, dosyanın özelliğinde *function.js* yapılandırılan parametre aracılığıyla kullanılabilir `name` .

# <a name="python"></a>[Python](#tab/python)

Event Grid örneği, dosyanın özelliğinde *function.js* `name` , olarak yazılmış olarak yapılandırılmış parametre aracılığıyla kullanılabilir `func.EventGridEvent` .

---

## <a name="event-schema"></a>Olay şeması

Bir Event Grid olayının verileri bir HTTP isteği gövdesinde JSON nesnesi olarak alınır. JSON aşağıdaki örneğe benzer şekilde görünür:

```json
[{
  "topic": "/subscriptions/{subscriptionid}/resourceGroups/eg0122/providers/Microsoft.Storage/storageAccounts/egblobstore",
  "subject": "/blobServices/default/containers/{containername}/blobs/blobname.jpg",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2018-01-23T17:02:19.6069787Z",
  "id": "{guid}",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "{guid}",
    "requestId": "{guid}",
    "eTag": "0x8D562831044DDD0",
    "contentType": "application/octet-stream",
    "contentLength": 2248,
    "blobType": "BlockBlob",
    "url": "https://egblobstore.blob.core.windows.net/{containername}/blobname.jpg",
    "sequencer": "000000000000272D000000000003D60F",
    "storageDiagnostics": {
      "batchId": "{guid}"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

Gösterilen örnek bir öğe dizisidir. Event Grid her zaman bir dizi gönderir ve dizide birden fazla olay gönderebilir. Çalışma zamanı, her dizi öğesi için işlevinizi bir kez çağırır.

Olay JSON verilerinde en üst düzey özellikler tüm olay türleri arasında aynıdır, ancak `data` özelliğin içeriği her bir olay türüne özeldir. Gösterilen örnek bir BLOB depolama olayı içindir.

Ortak ve olaya özgü özelliklerin açıklamaları için Event Grid belgelerindeki [olay özellikleri](../event-grid/event-schema.md#event-properties) ' ne bakın.

`EventGridEvent`Tür yalnızca en üst düzey özellikleri tanımlar; `Data` özelliği bir olur `JObject` .

## <a name="create-a-subscription"></a>Abonelik oluşturma

Event Grid HTTP isteklerini almaya başlamak için, işlevi çağıran uç nokta URL 'sini belirten bir Event Grid aboneliği oluşturun.

### <a name="azure-portal"></a>Azure portalı

Event Grid tetikleyicisiyle Azure portal geliştirdiğiniz işlevlerde **tümleştirme** ' i seçin, ardından **Event Grid tetikleyiciyi** seçin ve **Event Grid aboneliği oluştur**' u seçin.

:::image type="content" source="media/functions-bindings-event-grid/portal-sub-create.png" alt-text="Portalda tetiklemek için yeni bir olay aboneliği bağlayın.":::

Bu bağlantıyı seçtiğinizde, portal geçerli tetikleyici uç noktası zaten tanımlı olan **olay aboneliği oluştur** sayfasını açar.

:::image type="content" source="media/functions-bindings-event-grid/endpoint-url.png" alt-text="İşlev uç noktası zaten tanımlı olay aboneliği oluştur" :::

Azure portal kullanarak abonelikler oluşturma hakkında daha fazla bilgi için Event Grid belgelerinde [özel olay oluşturma-Azure Portal](../event-grid/custom-event-quickstart-portal.md) konusuna bakın.

### <a name="azure-cli"></a>Azure CLI

[Azure CLI](/cli/azure/get-started-with-azure-cli)kullanarak bir abonelik oluşturmak için [az eventgrid olay-abonelik oluştur](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_create) komutunu kullanın.

Komut, işlevi çağıran uç nokta URL 'sini gerektirir. Aşağıdaki örnekte, sürüme özgü URL kalıbı gösterilmektedir:

#### <a name="version-2x-and-higher-runtime"></a>Sürüm 2. x (ve üzeri) çalışma zamanı

```http
https://{functionappname}.azurewebsites.net/runtime/webhooks/eventgrid?functionName={functionname}&code={systemkey}
```

#### <a name="version-1x-runtime"></a>Sürüm 1. x çalışma zamanı

```http
https://{functionappname}.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName={functionname}&code={systemkey}
```

Sistem anahtarı, Event Grid tetikleyicisi için uç nokta URL 'sine dahil olması gereken bir yetkilendirme anahtarıdır. Aşağıdaki bölümde Sistem anahtarının nasıl alınacağı açıklanmaktadır.

Bir BLOB depolama hesabına abone olan bir örnek (sistem anahtarı için bir yer tutucu ile) aşağıda verilmiştir:

#### <a name="version-2x-and-higher-runtime"></a>Sürüm 2. x (ve üzeri) çalışma zamanı

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup \
    --provider-namespace Microsoft.Storage --resource-type storageAccounts \
    --resource-name myblobstorage12345 --name myFuncSub \
    --included-event-types Microsoft.Storage.BlobCreated \
    --subject-begins-with /blobServices/default/containers/images/blobs/ \
    --endpoint https://mystoragetriggeredfunction.azurewebsites.net/runtime/webhooks/eventgrid?functionName=imageresizefunc&code=<key>
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup ^
    --provider-namespace Microsoft.Storage --resource-type storageAccounts ^
    --resource-name myblobstorage12345 --name myFuncSub ^
    --included-event-types Microsoft.Storage.BlobCreated ^
    --subject-begins-with /blobServices/default/containers/images/blobs/ ^
    --endpoint https://mystoragetriggeredfunction.azurewebsites.net/runtime/webhooks/eventgrid?functionName=imageresizefunc&code=<key>
```

---

#### <a name="version-1x-runtime"></a>Sürüm 1. x çalışma zamanı

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup \
    --provider-namespace Microsoft.Storage --resource-type storageAccounts \
    --resource-name myblobstorage12345 --name myFuncSub \
    --included-event-types Microsoft.Storage.BlobCreated \
    --subject-begins-with /blobServices/default/containers/images/blobs/ \
    --endpoint https://mystoragetriggeredfunction.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName=imageresizefunc&code=<key>
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup ^
    --provider-namespace Microsoft.Storage --resource-type storageAccounts ^
    --resource-name myblobstorage12345 --name myFuncSub ^
    --included-event-types Microsoft.Storage.BlobCreated ^
    --subject-begins-with /blobServices/default/containers/images/blobs/ ^
    --endpoint https://mystoragetriggeredfunction.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName=imageresizefunc&code=<key>
```

---

Abonelik oluşturma hakkında daha fazla bilgi için bkz. [BLOB Storage hızlı](../storage/blobs/storage-blob-event-quickstart.md#subscribe-to-your-storage-account) başlangıcı veya diğer Event Grid hızlı başlangıçlar.

### <a name="get-the-system-key"></a>Sistem anahtarını al

Aşağıdaki API 'YI kullanarak sistem anahtarını alabilirsiniz (HTTP GET):

#### <a name="version-2x-and-higher-runtime"></a>Sürüm 2. x (ve üzeri) çalışma zamanı

```
http://{functionappname}.azurewebsites.net/admin/host/systemkeys/eventgrid_extension?code={masterkey}
```

#### <a name="version-1x-runtime"></a>Sürüm 1. x çalışma zamanı

```
http://{functionappname}.azurewebsites.net/admin/host/systemkeys/eventgridextensionconfig_extension?code={masterkey}
```

Bu bir yönetim API 'sidir, bu nedenle işlev uygulama [ana anahtarınızı](functions-bindings-http-webhook-trigger.md#authorization-keys)gerektirir. Ana anahtarla (bir Event Grid tetikleyici işlevi çağırmak için) sistem anahtarını karıştırmayın (işlev uygulamasında yönetim görevleri gerçekleştirmek için). Bir Event Grid konusuna abone olduğunuzda, sistem anahtarını kullandığınızdan emin olun.

Aşağıda sistem anahtarını sağlayan yanıta bir örnek verilmiştir:

```
{
  "name": "eventgridextensionconfig_extension",
  "value": "{the system key for the function}",
  "links": [
    {
      "rel": "self",
      "href": "{the URL for the function, without the system key}"
    }
  ]
}
```

İşlev uygulamanızın ana anahtarını portalda **işlev uygulaması ayarları** sekmesinden alabilirsiniz.

> [!IMPORTANT]
> Ana anahtar, işlev uygulamanıza yönetici erişimi sağlar. Bu anahtarı üçüncü taraflarla paylaşmayın veya yerel istemci uygulamalarında dağıtamazsınız.

Daha fazla bilgi için HTTP tetikleyici başvurusu makalesindeki [Yetkilendirme anahtarları](functions-bindings-http-webhook-trigger.md#authorization-keys) bölümüne bakın.

Alternatif olarak, anahtar değerini kendiniz belirtmek için bir HTTP PUT da gönderebilirsiniz.

## <a name="local-testing-with-viewer-web-app"></a>Viewer Web App ile yerel test

Event Grid bir tetikleyiciyi yerel olarak test etmek için, buluttaki kaynağından yerel makinenize teslim edilen Event Grid HTTP isteklerini almanız gerekir. Bunu yapmanın bir yolu, istekleri çevrimiçi olarak yakalayıp yerel makinenizde el ile yeniden göndermeyi kullanmaktır:

1. Olay iletilerini yakalayan [bir Görüntüleyici Web uygulaması oluşturun](#create-a-viewer-web-app) .
1. Görüntüleyici uygulamasına olayları Gönderen [Event Grid bir abonelik oluşturun](#create-an-event-grid-subscription) .
1. [Bir Istek oluşturun](#generate-a-request) ve Görüntüleyici uygulamasından istek gövdesini kopyalayın.
1. İsteği Event Grid tetikleyici işlevinizin localhost URL 'sine [el ile gönderin](#manually-post-the-request) .

Testi tamamladıktan sonra, uç noktayı güncelleştirerek üretim için aynı aboneliği kullanabilirsiniz. [Az eventgrid olay-abonelik güncelleştirme](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_update) Azure CLI komutunu kullanın.

### <a name="create-a-viewer-web-app"></a>Görüntüleyiciye Web uygulaması oluşturma

Olay iletilerini yakalamayı basitleştirmek için, olay iletilerini görüntüleyen [önceden oluşturulmuş bir Web uygulaması](https://github.com/Azure-Samples/azure-event-grid-viewer) dağıtabilirsiniz. Dağıtılan çözüm bir App Service planı, App Service web uygulaması ve GitHub'dan kaynak kod içerir.

Çözümü aboneliğinize dağıtmak için **Azure'a Dağıt**'ı seçin. Azure portalında parametre değerlerini girin.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png" alt="Button to Deploy to Azure." /></a>

Dağıtımın tamamlanması birkaç dakika sürebilir. Dağıtım başarıyla gerçekleştirildikten sonra, web uygulamanızı görüntüleyip çalıştığından emin olun. Web tarayıcısında şu adrese gidin: `https://<your-site-name>.azurewebsites.net`

Siteyi görürsünüz ancak henüz yayımlanmış olay yoktur.

![Yeni siteyi görüntüleme](media/functions-bindings-event-grid/view-site.png)

### <a name="create-an-event-grid-subscription"></a>Event Grid aboneliği oluşturma

Test etmek istediğiniz türün Event Grid aboneliğini oluşturun ve olay bildirimi için uç nokta olarak Web uygulamanızdan URL 'YI verin. Web uygulamanızın uç noktası `/api/updates/` sonekini içermelidir. Bu nedenle, tam URL `https://<your-site-name>.azurewebsites.net/api/updates`

Azure portal kullanarak abonelikler oluşturma hakkında daha fazla bilgi için, bkz. Event Grid belgelerinde [özel olay oluşturma-Azure Portal](../event-grid/custom-event-quickstart-portal.md) .

### <a name="generate-a-request"></a>İstek oluştur

Web uygulaması uç noktanıza HTTP trafiği oluşturacak bir olay tetikleyin.  Örneğin, bir BLOB depolama aboneliği oluşturduysanız, blob yükleyin veya silin. Web uygulamanızda bir istek görüntülendiğinde, istek gövdesini kopyalayın.

Abonelik doğrulama isteği ilk olarak alınır; tüm doğrulama isteklerini yoksayın ve olay isteğini kopyalayın.

![Web uygulamasından istek gövdesini Kopyala](media/functions-bindings-event-grid/view-results.png)

### <a name="manually-post-the-request"></a>İsteği el ile gönderin

Event Grid işlevinizi yerel olarak çalıştırın. `Content-Type`Ve `aeg-event-type` üst bilgilerinin el ile ayarlanması gerekir, ancak diğer tüm değerler varsayılan olarak bırakılabilir.

HTTP POST isteği oluşturmak için [Postman](https://www.getpostman.com/) veya [kıvrımlı](https://curl.haxx.se/docs/httpscripting.html) gibi bir araç kullanın:

* `Content-Type: application/json`Üst bilgi ayarlayın.
* Bir `aeg-event-type: Notification` üst bilgi ayarlayın.
* RequestBin verilerini istek gövdesine yapıştırın.
* Event Grid tetikleyici işlevinizin URL 'sine gönderin.
  * 2. x ve üzeri için aşağıdaki kalıbı kullanın:

    ```
    http://localhost:7071/runtime/webhooks/eventgrid?functionName={FUNCTION_NAME}
    ```

  * 1. x kullanımı için:

    ```
    http://localhost:7071/admin/extensions/EventGridExtensionConfig?functionName={FUNCTION_NAME}
    ```

`functionName`Parametresi, özniteliğinde belirtilen ad olmalıdır `FunctionName` .

Aşağıdaki ekran görüntülerinde, Postman 'daki üstbilgiler ve istek gövdesi gösterilmektedir:

![Postman 'daki üstbilgiler](media/functions-bindings-event-grid/postman2.png)

![Postman 'da istek gövdesi](media/functions-bindings-event-grid/postman.png)

Event Grid tetikleyici işlevi yürütülür ve aşağıdaki örneğe benzer Günlükler gösterir:

![Örnek Event Grid tetikleyici işlev günlükleri](media/functions-bindings-event-grid/eg-output.png)

## <a name="next-steps"></a>Sonraki adımlar

* [Event Grid olayı gönderme](./functions-bindings-event-grid-output.md)
