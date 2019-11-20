---
title: Azure Işlevleri için Event Grid tetikleyicisi
description: Azure Işlevlerinde Event Grid olaylarının nasıl işleneceğini anlayın.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: ''
ms.service: azure-functions
ms.topic: reference
ms.date: 09/04/2018
ms.author: cshoe
ms.openlocfilehash: ff21a1325e341f119fc8615ca03b41b8068aca19
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/28/2019
ms.locfileid: "72991417"
---
# <a name="event-grid-trigger-for-azure-functions"></a>Azure Işlevleri için Event Grid tetikleyicisi

Bu makalede, Azure Işlevlerinde [Event Grid](../event-grid/overview.md) olaylarının nasıl işleneceği açıklanır.

Event Grid, *yayımcılardan*oluşan olayları BILDIRMEK üzere http istekleri gönderen bir Azure hizmetidir. Yayımcı, olayı oluşturan hizmet veya kaynaktır. Örneğin, bir Azure Blob depolama hesabı bir yayımcı, [BLOB karşıya yükleme veya silme bir olaydır](../storage/blobs/storage-blob-event-overview.md). Bazı [Azure hizmetlerinde Event Grid olay yayımlamaya yönelik yerleşik destek vardır](../event-grid/overview.md#event-sources).

Olay *işleyicileri* olayları alır ve işler. Azure Işlevleri, [Event Grid olaylarını işlemek için yerleşik desteğe sahip çeşitli Azure hizmetlerinden](../event-grid/overview.md#event-handlers)biridir. Bu makalede, Event Grid bir olay alındığında bir işlevi çağırmak için Event Grid tetikleyicisini nasıl kullanacağınızı öğreneceksiniz.

İsterseniz, Event Grid olaylarını işlemek için bir HTTP tetikleyicisi kullanabilirsiniz; Bu makalenin ilerleyen kısımlarında [bir Event Grid tetikleyicisi olarak http tetikleyicisi kullanma](#use-an-http-trigger-as-an-event-grid-trigger) konusuna bakın. Şu anda, olay [Cloudevents şemasında](../event-grid/cloudevents-schema.md)teslim edildiğinde Azure işlevleri uygulaması için Event Grid tetikleyicisi kullanamazsınız. Bunun yerine, bir HTTP tetikleyicisi kullanın.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x"></a>Paketler-Işlevler 2. x

Event Grid tetikleyicisi [Microsoft. Azure. WebJobs. Extensions. EventGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid) NuGet paketi, sürüm 2. x ' te sunulmaktadır. Paketin kaynak kodu, [Azure-Functions-eventgrid-Extension](https://github.com/Azure/azure-functions-eventgrid-extension/tree/v2.x) GitHub deposunda bulunur.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="packages---functions-1x"></a>Paketler-Işlevler 1. x

Event Grid tetikleyicisi [Microsoft. Azure. WebJobs. Extensions. EventGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid) NuGet paketi, sürüm 1. x ' te sunulmaktadır. Paketin kaynak kodu, [Azure-Functions-eventgrid-Extension](https://github.com/Azure/azure-functions-eventgrid-extension/tree/master) GitHub deposunda bulunur.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="example"></a>Örnek

Event Grid tetikleyicisi için dile özgü örneğe bakın:

* C#
* [C#betik (. CSX)](#c-script-example)
* [Java](#trigger---java-examples)
* [JavaScript](#javascript-example)
* [Python](#python-example)

Bir HTTP tetikleyici örneği için, bu makalede daha sonra [http tetikleyicisini kullanma](#use-an-http-trigger-as-an-event-grid-trigger) konusuna bakın.

### <a name="c-2x"></a>C#(2. x)

Aşağıdaki örnek, `EventGridEvent` ' ye bağlanan işlevler 2. x [ C# işlevini](functions-dotnet-class-library.md) gösterir:

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

Daha fazla bilgi için bkz. paketler, [öznitelikler](#attributes), [yapılandırma](#configuration)ve [kullanım](#usage).

### <a name="c-version-1x"></a>C#(Sürüm 1. x)

Aşağıdaki örnek, `JObject` ' ye bağlanan bir işlevler 1. x [ C# işlevini](functions-dotnet-class-library.md) gösterir:

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

### <a name="c-script-example"></a>C#betik örneği

Aşağıdaki örnek, bir *function. JSON* dosyasındaki bir tetikleyici bağlamayı ve bağlamayı kullanan bir [ C# betik işlevini](functions-reference-csharp.md) gösterir.

Bu, *function. JSON* dosyasındaki bağlama verileri:

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

#### <a name="c-script-version-2x"></a>C#betik (sürüm 2. x)

Aşağıda`EventGridEvent`bağlanan 2. x C# betik kodu verilmiştir:

```csharp
#r "Microsoft.Azure.EventGrid"
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(EventGridEvent eventGridEvent, ILogger log)
{
    log.LogInformation(eventGridEvent.Data.ToString());
}
```

Daha fazla bilgi için bkz. paketler, [öznitelikler](#attributes), [yapılandırma](#configuration)ve [kullanım](#usage).

#### <a name="c-script-version-1x"></a>C#betik (sürüm 1. x)

`JObject`'e bağlanan 1. x C# komut dosyası kodu aşağıda verilmiştir:

```cs
#r "Newtonsoft.Json"

using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

public static void Run(JObject eventGridEvent, TraceWriter log)
{
    log.Info(eventGridEvent.ToString(Formatting.Indented));
}
```

### <a name="javascript-example"></a>JavaScript örneği

Aşağıdaki örnek, bir *function. JSON* dosyasındaki bir tetikleyici bağlamayı ve bağlamayı kullanan bir [JavaScript işlevini](functions-reference-node.md) gösterir.

Bu, *function. JSON* dosyasındaki bağlama verileri:

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

### <a name="python-example"></a>Python örneği

Aşağıdaki örnek, bir *function. JSON* dosyasındaki bir tetikleyici bağlamayı ve bağlamayı kullanan bir [Python işlevini](functions-reference-python.md) gösterir.

Bu, *function. JSON* dosyasındaki bağlama verileri:

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
import logging
import azure.functions as func


def main(event: func.EventGridEvent):
    logging.info("Python Event Grid function processed a request.")
    logging.info("  Subject: %s", event.subject)
    logging.info("  Time: %s", event.event_time)
    logging.info("  Data: %s", event.get_json())
```

### <a name="trigger---java-examples"></a>Trigger-Java örnekleri

Bu bölüm aşağıdaki örnekleri içerir:

* [Event Grid tetikleyicisi, dize parametresi](#event-grid-trigger-string-parameter-java)
* [Event Grid tetikleyicisi, POJO parametresi](#event-grid-trigger-pojo-parameter-java)

Aşağıdaki örneklerde, önce olayı bir POJO olarak ```String``` ' ye ve ikinci kez alarak, bir olayı oluşturan bir *işlev. JSON* dosyası ve [Java işlevlerinde](functions-reference-java.md) tetikleyici bağlama gösterilmektedir.

```json
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    }
  ]
}
```

#### <a name="event-grid-trigger-string-parameter-java"></a>Event Grid tetikleyicisi, dize parametresi (Java)

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

#### <a name="event-grid-trigger-pojo-parameter-java"></a>Event Grid tetikleyicisi, POJO parametresi (Java)

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

Varış sonrasında, olayın JSON yükü, işlev tarafından kullanılmak üzere ```EventSchema``` POJO 'ya de serileştirilir. Bu, işlevin, nesne yönelimli bir şekilde olay özelliklerine erişmesini sağlar.

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

[Java işlevleri çalışma zamanı kitaplığı](/java/api/overview/azure/functions/runtime)'nda, değeri eventgrid 'den gelen parametrelerde `EventGridTrigger` ek açıklamasını kullanın. Bu ek açıklamaların bulunduğu parametreler, bir olay geldiğinde işlevin çalışmasına neden olur.  Bu ek açıklama `Optional<T>` kullanılarak yerel Java türleri, POJOs veya null atanabilir değerlerle kullanılabilir.

## <a name="attributes"></a>Öznitelikler

[ C# Sınıf kitaplıkları](functions-dotnet-class-library.md)' nda [eventgridtrigger](https://github.com/Azure/azure-functions-eventgrid-extension/blob/master/src/EventGridExtension/TriggerBinding/EventGridTriggerAttribute.cs) özniteliğini kullanın.

Yöntem imzasında bir `EventGridTrigger` özniteliği aşağıda verilmiştir:

```csharp
[FunctionName("EventGridTest")]
public static void EventGridTest([EventGridTrigger] JObject eventGridEvent, ILogger log)
{
    ...
}
```

Tüm örnek için bkz C# . örnek.

## <a name="configuration"></a>Yapılandırma

Aşağıdaki tabloda, *function. JSON* dosyasında ayarladığınız bağlama yapılandırma özellikleri açıklanmaktadır. `EventGridTrigger` özniteliğinde ayarlanacak bir oluşturucu parametresi veya özellik yok.

|function. JSON özelliği |Açıklama|
|---------|---------|
| **type** | Gerekli-`eventGridTrigger` olarak ayarlanmalıdır. |
| **direction** | Gerekli-`in` olarak ayarlanmalıdır. |
| **ada** | Gerekli-olay verilerini alan parametre için işlev kodunda kullanılan değişken adı. |

## <a name="usage"></a>Kullanım

Azure C# işlevleri F# 1. x içindeki ve işlevleri için, Event Grid tetikleyicisi için aşağıdaki parametre türlerini kullanabilirsiniz:

* `JObject`
* `string`

Azure C# işlevleri F# 2. x içindeki ve işlevleri için, Event Grid tetikleyicisi için aşağıdaki parametre türünü kullanma seçeneğiniz de vardır:

* `Microsoft.Azure.EventGrid.Models.EventGridEvent`-tüm olay türlerinde ortak olan alanların özelliklerini tanımlar.

> [!NOTE]
> Işlev v1 ' de `Microsoft.Azure.WebJobs.Extensions.EventGrid.EventGridEvent` ' a bağlamayı denerseniz, derleyici "kullanım dışı" iletisini görüntüler ve bunun yerine `Microsoft.Azure.EventGrid.Models.EventGridEvent` ' i kullanmanızı ister. Daha yeni türü kullanmak için [Microsoft. Azure. EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid) NuGet paketine başvurun ve `EventGridEvent` tür adını `Microsoft.Azure.EventGrid.Models` ' ye ekleyerek tam olarak nitelendirin. Bir C# betik işlevindeki NuGet paketlerine başvurma hakkında daha fazla bilgi için bkz. [NuGet paketlerini kullanma](functions-reference-csharp.md#using-nuget-packages)

JavaScript işlevleri için, *function. json* `name` özelliği tarafından adlandırılan parametrenin olay nesnesine bir başvurusu vardır.

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

Olay JSON verilerinde en üst düzey özellikler tüm olay türleri arasında aynıdır, ancak `data` özelliğinin içeriği her bir olay türüne özeldir. Gösterilen örnek bir BLOB depolama olayı içindir.

Ortak ve olaya özgü özelliklerin açıklamaları için Event Grid belgelerindeki [olay özellikleri](../event-grid/event-schema.md#event-properties) ' ne bakın.

`EventGridEvent` türü yalnızca en üst düzey özellikleri tanımlar; `Data` özelliği bir `JObject`.

## <a name="create-a-subscription"></a>Abonelik oluşturma

Event Grid HTTP isteklerini almaya başlamak için, işlevi çağıran uç nokta URL 'sini belirten bir Event Grid aboneliği oluşturun.

### <a name="azure-portal"></a>Azure portalı

Event Grid tetikleyicisiyle Azure portal geliştirdiğiniz işlevlerde **Event Grid aboneliği Ekle**' yi seçin.

![Portalda abonelik oluşturma](media/functions-bindings-event-grid/portal-sub-create.png)

Bu bağlantıyı seçtiğinizde, Portal Endpoint URL 'SI önceden doldurulmuş olarak **olay aboneliği oluştur** sayfasını açar.

![Endpoint URL önceden dolduruldu](media/functions-bindings-event-grid/endpoint-url.png)

Azure portal kullanarak abonelikler oluşturma hakkında daha fazla bilgi için Event Grid belgelerinde [özel olay oluşturma-Azure Portal](../event-grid/custom-event-quickstart-portal.md) konusuna bakın.

### <a name="azure-cli"></a>Azure CLI

[Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)kullanarak bir abonelik oluşturmak için [az eventgrid olay-abonelik oluştur](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-create) komutunu kullanın.

Komut, işlevi çağıran uç nokta URL 'sini gerektirir. Aşağıdaki örnekte, sürüme özgü URL kalıbı gösterilmektedir:

#### <a name="version-2x-runtime"></a>Sürüm 2. x çalışma zamanı

    https://{functionappname}.azurewebsites.net/runtime/webhooks/eventgrid?functionName={functionname}&code={systemkey}

#### <a name="version-1x-runtime"></a>Sürüm 1. x çalışma zamanı

    https://{functionappname}.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName={functionname}&code={systemkey}

Sistem anahtarı, Event Grid tetikleyicisi için uç nokta URL 'sine dahil olması gereken bir yetkilendirme anahtarıdır. Aşağıdaki bölümde Sistem anahtarının nasıl alınacağı açıklanmaktadır.

Bir BLOB depolama hesabına abone olan bir örnek (sistem anahtarı için bir yer tutucu ile) aşağıda verilmiştir:

#### <a name="version-2x-runtime"></a>Sürüm 2. x çalışma zamanı

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup \
--provider-namespace Microsoft.Storage --resource-type storageAccounts \
--resource-name myblobstorage12345 --name myFuncSub  \
--included-event-types Microsoft.Storage.BlobCreated \
--subject-begins-with /blobServices/default/containers/images/blobs/ \
--endpoint https://mystoragetriggeredfunction.azurewebsites.net/runtime/webhooks/eventgrid?functionName=imageresizefunc&code=<key>
```

#### <a name="version-1x-runtime"></a>Sürüm 1. x çalışma zamanı

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup \
--provider-namespace Microsoft.Storage --resource-type storageAccounts \
--resource-name myblobstorage12345 --name myFuncSub  \
--included-event-types Microsoft.Storage.BlobCreated \
--subject-begins-with /blobServices/default/containers/images/blobs/ \
--endpoint https://mystoragetriggeredfunction.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName=imageresizefunc&code=<key>
```

Abonelik oluşturma hakkında daha fazla bilgi için bkz. [BLOB Storage hızlı](../storage/blobs/storage-blob-event-quickstart.md#subscribe-to-your-storage-account) başlangıcı veya diğer Event Grid hızlı başlangıçlar.

### <a name="get-the-system-key"></a>Sistem anahtarını al

Aşağıdaki API 'YI kullanarak sistem anahtarını alabilirsiniz (HTTP GET):

#### <a name="version-2x-runtime"></a>Sürüm 2. x çalışma zamanı

```
http://{functionappname}.azurewebsites.net/admin/host/systemkeys/eventgrid_extension?code={masterkey}
```

#### <a name="version-1x-runtime"></a>Sürüm 1. x çalışma zamanı

```
http://{functionappname}.azurewebsites.net/admin/host/systemkeys/eventgridextensionconfig_extension?code={masterkey}
```

Bu bir yönetim API 'sidir, bu nedenle işlev uygulama [ana anahtarınızı](functions-bindings-http-webhook.md#authorization-keys)gerektirir. Ana anahtarla (bir Event Grid tetikleyici işlevi çağırmak için) sistem anahtarını karıştırmayın (işlev uygulamasında yönetim görevleri gerçekleştirmek için). Bir Event Grid konusuna abone olduğunuzda, sistem anahtarını kullandığınızdan emin olun.

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

Daha fazla bilgi için HTTP tetikleyici başvurusu makalesindeki [Yetkilendirme anahtarları](functions-bindings-http-webhook.md#authorization-keys) bölümüne bakın.

Alternatif olarak, anahtar değerini kendiniz belirtmek için bir HTTP PUT da gönderebilirsiniz.

## <a name="local-testing-with-viewer-web-app"></a>Viewer Web App ile yerel test

Event Grid bir tetikleyiciyi yerel olarak test etmek için, buluttaki kaynağından yerel makinenize teslim edilen Event Grid HTTP isteklerini almanız gerekir. Bunu yapmanın bir yolu, istekleri çevrimiçi olarak yakalayıp yerel makinenizde el ile yeniden göndermeyi kullanmaktır:

1. Olay iletilerini yakalayan [bir Görüntüleyici Web uygulaması oluşturun](#create-a-viewer-web-app) .
1. Görüntüleyici uygulamasına olayları Gönderen [Event Grid bir abonelik oluşturun](#create-an-event-grid-subscription) .
1. [Bir Istek oluşturun](#generate-a-request) ve Görüntüleyici uygulamasından istek gövdesini kopyalayın.
1. İsteği Event Grid tetikleyici işlevinizin localhost URL 'sine [el ile gönderin](#manually-post-the-request) .

Testi tamamladıktan sonra, uç noktayı güncelleştirerek üretim için aynı aboneliği kullanabilirsiniz. [Az eventgrid olay-abonelik güncelleştirme](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-update) Azure CLI komutunu kullanın.

### <a name="create-a-viewer-web-app"></a>Görüntüleyiciye Web uygulaması oluşturma

Olay iletilerini yakalamayı basitleştirmek için, olay iletilerini görüntüleyen [önceden oluşturulmuş bir Web uygulaması](https://github.com/Azure-Samples/azure-event-grid-viewer) dağıtabilirsiniz. Dağıtılan çözüm bir App Service planı, App Service web uygulaması ve GitHub'dan kaynak kod içerir.

Çözümü aboneliğinize dağıtmak için **Azure'a Dağıt**'ı seçin. Azure portalında parametre değerlerini girin.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

Dağıtımın tamamlanması birkaç dakika sürebilir. Dağıtım başarıyla gerçekleştirildikten sonra, web uygulamanızı görüntüleyip çalıştığından emin olun. Web tarayıcısında şu adrese gidin: `https://<your-site-name>.azurewebsites.net`

Siteyi görürsünüz ancak henüz yayımlanmış olay yoktur.

![Yeni siteyi görüntüleme](media/functions-bindings-event-grid/view-site.png)

### <a name="create-an-event-grid-subscription"></a>Event Grid aboneliği oluşturma

Test etmek istediğiniz türün Event Grid aboneliğini oluşturun ve olay bildirimi için uç nokta olarak Web uygulamanızdan URL 'YI verin. Web uygulamanızın uç noktası `/api/updates/` sonekini içermelidir. Bu nedenle, tam URL `https://<your-site-name>.azurewebsites.net/api/updates` ' dır

Azure portal kullanarak abonelikler oluşturma hakkında daha fazla bilgi için, bkz. Event Grid belgelerinde [özel olay oluşturma-Azure Portal](../event-grid/custom-event-quickstart-portal.md) .

### <a name="generate-a-request"></a>İstek oluştur

Web uygulaması uç noktanıza HTTP trafiği oluşturacak bir olay tetikleyin.  Örneğin, bir BLOB depolama aboneliği oluşturduysanız, blob yükleyin veya silin. Web uygulamanızda bir istek görüntülendiğinde, istek gövdesini kopyalayın.

Abonelik doğrulama isteği ilk olarak alınır; tüm doğrulama isteklerini yoksayın ve olay isteğini kopyalayın.

![Web uygulamasından istek gövdesini Kopyala](media/functions-bindings-event-grid/view-results.png)

### <a name="manually-post-the-request"></a>İsteği el ile gönderin

Event Grid işlevinizi yerel olarak çalıştırın.

HTTP POST isteği oluşturmak için [Postman](https://www.getpostman.com/) veya [kıvrımlı](https://curl.haxx.se/docs/httpscripting.html) gibi bir araç kullanın:

* `Content-Type: application/json` üst bilgisi ayarlayın.
* `aeg-event-type: Notification` üst bilgisi ayarlayın.
* RequestBin verilerini istek gövdesine yapıştırın.
* Event Grid tetikleyici işlevinizin URL 'sine gönderin.
  * 2\. x için aşağıdaki kalıbı kullanın:

    ```
    http://localhost:7071/runtime/webhooks/eventgrid?functionName={FUNCTION_NAME}
    ```

  * 1\. x kullanımı için:

    ```
    http://localhost:7071/admin/extensions/EventGridExtensionConfig?functionName={FUNCTION_NAME}
    ```

`functionName` parametresi `FunctionName` özniteliğinde belirtilen ad olmalıdır.

Aşağıdaki ekran görüntülerinde, Postman 'daki üstbilgiler ve istek gövdesi gösterilmektedir:

![Postman 'daki üstbilgiler](media/functions-bindings-event-grid/postman2.png)

![Postman 'da istek gövdesi](media/functions-bindings-event-grid/postman.png)

Event Grid tetikleyici işlevi yürütülür ve aşağıdaki örneğe benzer Günlükler gösterir:

![Örnek Event Grid tetikleyici işlev günlükleri](media/functions-bindings-event-grid/eg-output.png)

## <a name="local-testing-with-ngrok"></a>Ngrok ile yerel test

Event Grid tetikleyiciyi yerel olarak test etmenin başka bir yolu da Internet ile geliştirme bilgisayarınız arasındaki HTTP bağlantısını otomatikleştirmeye yönelik bir yoldur. Bunu [ngrok](https://ngrok.com/)gibi bir araçla yapabilirsiniz:

1. [Bir ngrok uç noktası oluşturun](#create-an-ngrok-endpoint).
1. [Event Grid tetikleyici Işlevini çalıştırın](#run-the-event-grid-trigger-function).
1. Olayları ngrok uç noktasına Gönderen [Event Grid bir abonelik oluşturun](#create-a-subscription) .
1. [Bir olayı tetikleyin](#trigger-an-event).

Testi tamamladıktan sonra, uç noktayı güncelleştirerek üretim için aynı aboneliği kullanabilirsiniz. [Az eventgrid olay-abonelik güncelleştirme](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-update) Azure CLI komutunu kullanın.

### <a name="create-an-ngrok-endpoint"></a>Ngrok uç noktası oluşturma

Ngrok *. exe* dosyasını [ngrok](https://ngrok.com/)konumundan indirin ve aşağıdaki komutla çalıştırın:

```
ngrok http -host-header=localhost 7071
```

İşlevler çalışma zamanı localhost üzerinde çalıştırıldığında localhost 'tan gelen istekleri beklediği için-Host-Header parametresi gereklidir. 7071, çalışma zamanı yerel olarak çalıştırıldığında varsayılan bağlantı noktası numarasıdır.

Komut aşağıdakine benzer bir çıktı oluşturur:

```
Session Status                online
Version                       2.2.8
Region                        United States (us)
Web Interface                 http://127.0.0.1:4040
Forwarding                    http://263db807.ngrok.io -> localhost:7071
Forwarding                    https://263db807.ngrok.io -> localhost:7071

Connections                   ttl     opn     rt1     rt5     p50     p90
                              0       0       0.00    0.00    0.00    0.00
```

Event Grid aboneliğiniz için `https://{subdomain}.ngrok.io` URL 'sini kullanacaksınız.

### <a name="run-the-event-grid-trigger-function"></a>Event Grid tetikleyici işlevini çalıştırma

Ngrok URL 'SI Event Grid tarafından özel işleme almaz, bu nedenle, abonelik oluşturulduğunda işlevinizin yerel olarak çalışıyor olması gerekir. Değilse, doğrulama yanıtı gönderilmez ve abonelik oluşturma başarısız olur.

### <a name="create-a-subscription"></a>Abonelik oluşturma

Test etmek istediğiniz türün Event Grid aboneliğini oluşturun ve bunu ngrok uç noktanıza verin.

2\. x Işlevleri için bu uç nokta modelini kullanın:

```
https://{SUBDOMAIN}.ngrok.io/runtime/webhooks/eventgrid?functionName={FUNCTION_NAME}
```

Bu uç nokta modelini 1. x Işlevleri için kullanın:

```
https://{SUBDOMAIN}.ngrok.io/admin/extensions/EventGridExtensionConfig?functionName={FUNCTION_NAME}
```

`{FUNCTION_NAME}` parametresi `FunctionName` özniteliğinde belirtilen ad olmalıdır.

Azure CLı kullanarak bir örnek aşağıda verilmiştir:

```azurecli
az eventgrid event-subscription create --resource-id /subscriptions/aeb4b7cb-b7cb-b7cb-b7cb-b7cbb6607f30/resourceGroups/eg0122/providers/Microsoft.Storage/storageAccounts/egblobstor0122 --name egblobsub0126 --endpoint https://263db807.ngrok.io/runtime/webhooks/eventgrid?functionName=EventGridTrigger
```

Abonelik oluşturma hakkında daha fazla bilgi için bu makalenin önceki kısımlarında [bir abonelik oluşturma](#create-a-subscription) bölümüne bakın.

### <a name="trigger-an-event"></a>Bir olay tetikleme

Ngrok uç noktanıza HTTP trafiği oluşturacak bir olay tetikleyin.  Örneğin, bir BLOB depolama aboneliği oluşturduysanız, blob yükleyin veya silin.

Event Grid tetikleyici işlevi yürütülür ve aşağıdaki örneğe benzer Günlükler gösterir:

![Örnek Event Grid tetikleyici işlev günlükleri](media/functions-bindings-event-grid/eg-output.png)

## <a name="use-an-http-trigger-as-an-event-grid-trigger"></a>Event Grid tetikleyicisi olarak HTTP tetikleyicisi kullanma

Event Grid olaylar HTTP istekleri olarak alınır, bu nedenle olayları, Event Grid tetikleyicisi yerine bir HTTP tetikleyicisi kullanarak işleyebilirsiniz. Bunu yapmanın olası bir nedeni, işlevi çağıran uç nokta URL 'SI üzerinde daha fazla denetim edinmektir. Diğer bir neden, [Cloudevents şemasında](../event-grid/cloudevents-schema.md)olay almanız gerektiğinde olur. Şu anda Event Grid tetikleyicisi CloudEvents şemasını desteklemez. Bu bölümdeki örneklerde, hem Event Grid şeması hem de CloudEvents şeması için çözümler gösterilmektedir.

HTTP tetikleyicisi kullanıyorsanız, Event Grid tetikleyicisinin otomatik olarak yaptığı kodu yazmanız gerekir:

* [Abonelik doğrulama isteğine](../event-grid/security-authentication.md#webhook-event-delivery)bir doğrulama yanıtı gönderir.
* İstek gövdesinde bulunan olay dizisinin öğesi başına işlevi bir kez çağırır.

İşlevi yerel olarak veya Azure 'da çalıştırıldığında kullanılacak URL hakkında daha fazla bilgi için bkz. [http tetikleyici bağlama başvurusu belgeleri](functions-bindings-http-webhook.md)

### <a name="event-grid-schema"></a>Olay Kılavuz şeması

Bir HTTP tetikleyicisi C# için aşağıdaki örnek kod, Event Grid tetikleme davranışının benzetimini yapar. Event Grid şemasında teslim edilen olaylar için bu örneği kullanın.

```csharp
[FunctionName("HttpTrigger")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]HttpRequestMessage req,
    ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    var messages = await req.Content.ReadAsAsync<JArray>();

    // If the request is for subscription validation, send back the validation code.
    if (messages.Count > 0 && string.Equals((string)messages[0]["eventType"],
        "Microsoft.EventGrid.SubscriptionValidationEvent",
        System.StringComparison.OrdinalIgnoreCase))
    {
        log.LogInformation("Validate request received");
        return req.CreateResponse<object>(new
        {
            validationResponse = messages[0]["data"]["validationCode"]
        });
    }

    // The request is not for subscription validation, so it's for one or more events.
    foreach (JObject message in messages)
    {
        // Handle one event.
        EventGridEvent eventGridEvent = message.ToObject<EventGridEvent>();
        log.LogInformation($"Subject: {eventGridEvent.Subject}");
        log.LogInformation($"Time: {eventGridEvent.EventTime}");
        log.LogInformation($"Event data: {eventGridEvent.Data.ToString()}");
    }

    return req.CreateResponse(HttpStatusCode.OK);
}
```

Bir HTTP tetikleyicisi için aşağıdaki örnek JavaScript kodu, Event Grid tetikleme davranışının benzetimini yapar. Event Grid şemasında teslim edilen olaylar için bu örneği kullanın.

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    var messages = req.body;
    // If the request is for subscription validation, send back the validation code.
    if (messages.length > 0 && messages[0].eventType == "Microsoft.EventGrid.SubscriptionValidationEvent") {
        context.log('Validate request received');
        var code = messages[0].data.validationCode;
        context.res = { status: 200, body: { "ValidationResponse": code } };
    }
    else {
        // The request is not for subscription validation, so it's for one or more events.
        // Event Grid schema delivers events in an array.
        for (var i = 0; i < messages.length; i++) {
            // Handle one event.
            var message = messages[i];
            context.log('Subject: ' + message.subject);
            context.log('Time: ' + message.eventTime);
            context.log('Data: ' + JSON.stringify(message.data));
        }
    }
    context.done();
};
```

Olay işleme kodunuz `messages` dizisi aracılığıyla döngünün içine gider.

### <a name="cloudevents-schema"></a>CloudEvents şeması

Bir HTTP tetikleyicisi C# için aşağıdaki örnek kod, Event Grid tetikleme davranışının benzetimini yapar.  CloudEvents şemasında teslim edilen olaylar için bu örneği kullanın.

```csharp
[FunctionName("HttpTrigger")]
public static async Task<HttpResponseMessage> Run([HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]HttpRequestMessage req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    var requestmessage = await req.Content.ReadAsStringAsync();
    var message = JToken.Parse(requestmessage);

    if (message.Type == JTokenType.Array)
    {
        // If the request is for subscription validation, send back the validation code.
        if (string.Equals((string)message[0]["eventType"],
        "Microsoft.EventGrid.SubscriptionValidationEvent",
        System.StringComparison.OrdinalIgnoreCase))
        {
            log.LogInformation("Validate request received");
            return req.CreateResponse<object>(new
            {
                validationResponse = message[0]["data"]["validationCode"]
            });
        }
    }
    else
    {
        // The request is not for subscription validation, so it's for an event.
        // CloudEvents schema delivers one event at a time.
        log.LogInformation($"Source: {message["source"]}");
        log.LogInformation($"Time: {message["eventTime"]}");
        log.LogInformation($"Event data: {message["data"].ToString()}");
    }

    return req.CreateResponse(HttpStatusCode.OK);
}
```

Bir HTTP tetikleyicisi için aşağıdaki örnek JavaScript kodu, Event Grid tetikleme davranışının benzetimini yapar. CloudEvents şemasında teslim edilen olaylar için bu örneği kullanın.

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    var message = req.body;
    // If the request is for subscription validation, send back the validation code.
    if (message.length > 0 && message[0].eventType == "Microsoft.EventGrid.SubscriptionValidationEvent") {
        context.log('Validate request received');
        var code = message[0].data.validationCode;
        context.res = { status: 200, body: { "ValidationResponse": code } };
    }
    else {
        // The request is not for subscription validation, so it's for an event.
        // CloudEvents schema delivers one event at a time.
        var event = JSON.parse(message);
        context.log('Source: ' + event.source);
        context.log('Time: ' + event.eventTime);
        context.log('Data: ' + JSON.stringify(event.data));
    }
    context.done();
};
```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure işlevleri Tetikleyicileri ve bağlamaları hakkında daha fazla bilgi edinin](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Event Grid hakkında daha fazla bilgi edinin](../event-grid/overview.md)
