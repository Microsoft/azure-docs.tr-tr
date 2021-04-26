---
title: Azure Işlevleri özel işleyiciler
description: Herhangi bir dil veya çalışma zamanı sürümü ile Azure Işlevleri 'ni kullanmayı öğrenin.
author: anthonychu
ms.author: antchu
ms.date: 12/1/2020
ms.topic: article
ms.openlocfilehash: dd112c74ea9f013a0e14bddd735060ddbf73c14e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100578534"
---
# <a name="azure-functions-custom-handlers"></a>Azure Işlevleri özel işleyiciler

Her Işlevler uygulaması dile özgü bir işleyici tarafından yürütülür. Azure Işlevleri varsayılan olarak birçok [dil işleyicisini](./supported-languages.md) desteklese de, diğer dilleri veya çalışma zamanlarını kullanmak isteyebileceğiniz durumlar vardır.

Özel işleyiciler, Işlevler konaktan olayları alan hafif Web sunucularıdır. HTTP temel öğelerini destekleyen herhangi bir dil, özel bir işleyici uygulayabilir.

Özel işleyiciler şunları yapmak istediğiniz durumlar için idealdir:

- İşlev uygulamasını, Go veya Rust gibi şu anda desteklenmeyen bir dilde uygulayın.
- Bir işlev uygulamasını Şu anda desteklenmeyen bir çalışma zamanında (deno gibi) uygulayın.

Özel işleyicilerle, [uzantı paketleri](./functions-bindings-register.md)aracılığıyla [Tetikleyicileri ve giriş ve çıkış bağlamalarını](./functions-triggers-bindings.md) kullanabilirsiniz.

[Go ve Rust 'ta hızlı](create-first-function-vs-code-other.md)başlangıçlarla Azure işlevleri özel işleyicileri ile çalışmaya başlayın.

## <a name="overview"></a>Genel Bakış

Aşağıdaki diyagramda, Işlevler ana bilgisayarı ile özel işleyici olarak uygulanan bir Web sunucusu arasındaki ilişki gösterilmektedir.

![Azure Işlevleri özel işleyicisine genel bakış](./media/functions-custom-handlers/azure-functions-custom-handlers-overview.png)

1. Her olay, Işlevler ana bilgisayarına gönderilen bir isteği tetikler. Olay, Azure Işlevleri tarafından desteklenen herhangi bir tetikleyiciden yapılır.
1. Sonra Işlevler ana bilgisayarı Web sunucusuna bir [istek yükü](#request-payload) yayınlar. Yük, tetikleyici ve giriş bağlama verilerini ve işlevin diğer meta verilerini barındırır.
1. Web sunucusu bağımsız işlevi yürütür ve Işlevler ana bilgisayarına bir [Yanıt yükü](#response-payload) döndürür.
1. Işlevler ana bilgisayarı, işleme için işlevin çıkış bağlamalarına yapılan yanıttan verileri geçirir.

Özel işleyici olarak uygulanan bir Azure Işlevleri uygulamasının, dosyalarda *host.js*, *local.settings.js* ve *function.js* bazı kurallara göre yapılandırmak gerekir.

## <a name="application-structure"></a>Uygulama yapısı

Özel bir işleyici uygulamak için, uygulamanız için aşağıdaki yönlere ihtiyacınız vardır:

- Uygulamanızın kökündeki bir *host.js* dosyası
- Uygulamanızın kökündeki bir *local.settings.js* dosyası
- Her işlev için dosyadaki bir *function.js* (işlev adıyla eşleşen bir klasör içinde)
- Bir Web sunucusu çalıştıran bir komut, betik veya yürütülebilir dosya

Aşağıdaki diyagramda, bu dosyaların "MyQueueFunction" adlı bir işlev ve *handler.exe* adlı özel bir işleyici yürütülebilir dosyası için dosya sistemine nasıl bakılacağı gösterilmektedir.

```bash
| /MyQueueFunction
|   function.json
|
| host.json
| local.settings.json
| handler.exe
```

### <a name="configuration"></a>Yapılandırma

Uygulama, dosyalar üzerinde *host.js* ve *local.settings.js* aracılığıyla yapılandırılır.

#### <a name="hostjson"></a>Üzerinde host.js

*host.json* , işlevleri http olaylarını işleyebilen bir Web sunucusuna işaret ederek istekleri nereye göndereceğini belirtir.

Özel bir işleyici, bölümünde Web sunucusunun nasıl çalıştırılacağı hakkındaki ayrıntılarla birlikte *host.js* yapılandırılarak tanımlanır `customHandler` .

```json
{
  "version": "2.0",
  "customHandler": {
    "description": {
      "defaultExecutablePath": "handler.exe"
    }
  }
}
```

`customHandler`Bölümü tarafından tanımlanan bir hedefe işaret eder `defaultExecutablePath` . Yürütme hedefi, Web sunucusunun uygulandığı bir komut, yürütülebilir dosya ya da dosya olabilir.

`arguments`Çalıştırılabilir dosyaya herhangi bir bağımsız değişken geçirmek için diziyi kullanın. Bağımsız değişkenler gösterimi kullanılarak ortam değişkenlerinin (uygulama ayarları) genişlemesine destek `%%` .

Yürütülebilir dosya tarafından kullanılan çalışma dizinini ile de değiştirebilirsiniz `workingDirectory` .

```json
{
  "version": "2.0",
  "customHandler": {
    "description": {
      "defaultExecutablePath": "app/handler.exe",
      "arguments": [
        "--database-connection-string",
        "%DATABASE_CONNECTION_STRING%"
      ],
      "workingDirectory": "app"
    }
  }
}
```

##### <a name="bindings-support"></a>Bağlama desteği

Giriş ve çıkış bağlamalarıyla birlikte standart Tetikleyiciler, *host.js* dosyadaki [uzantı](./functions-bindings-register.md) paketleriyle başvurularak kullanılabilir.

#### <a name="localsettingsjson"></a>local.settings.json

*local.settings.js* , işlev uygulamasını yerel olarak çalıştırırken kullanılan uygulama ayarlarını tanımlar. Gizli dizi içerebilen *local.settings.js* , kaynak denetiminden dışlanmalıdır. Azure 'da bunun yerine uygulama ayarlarını kullanın.

Özel işleyiciler için, `FUNCTIONS_WORKER_RUNTIME` `Custom` *üzerindelocal.settings.js* olarak ayarlayın.

```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "Custom"
  }
}
```

### <a name="function-metadata"></a>İşlev meta verileri

Özel bir işleyiciyle kullanıldığında, içerik *üzerindekifunction.js* başka bir bağlam altında bir işlevi nasıl tanımlayacağınızdan farklı değildir. Tek gereksinim, dosyalardaki *function.js* , işlev adıyla eşleşecek şekilde adlı bir klasörde olmalıdır.

Üzerinde aşağıdaki *function.js* , bir kuyruk tetikleyicisi ve bir sıra çıkış bağlaması olan bir işlevi yapılandırır. *Myqueuefunction* adlı bir klasörde olduğundan, *myqueuefunction* adlı bir işlevi tanımlar.

**Üzerinde MyQueueFunction/function.js**

```json
{
  "bindings": [
    {
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "messages-incoming",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "$return",
      "type": "queue",
      "direction": "out",
      "queueName": "messages-outgoing",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

### <a name="request-payload"></a>İstek yükü

Bir kuyruk iletisi alındığında, Işlevler ana bilgisayarı özel işleyiciye gövdedeki bir yük ile HTTP POST isteği gönderir.

Aşağıdaki kod bir örnek istek yükünü temsil eder. Yük, iki üyeye sahip bir JSON yapısı içerir: `Data` ve `Metadata` .

`Data`Üye, dosyadaki *function.js* bağlama dizisinde tanımlanan giriş ve tetikleyici adlarıyla eşleşen anahtarlar içerir.

`Metadata`Üye, [olay kaynağından oluşturulan meta verileri](./functions-bindings-expressions-patterns.md#trigger-metadata)içerir.

```json
{
  "Data": {
    "myQueueItem": "{ message: \"Message sent\" }"
  },
  "Metadata": {
    "DequeueCount": 1,
    "ExpirationTime": "2019-10-16T17:58:31+00:00",
    "Id": "800ae4b3-bdd2-4c08-badd-f08e5a34b865",
    "InsertionTime": "2019-10-09T17:58:31+00:00",
    "NextVisibleTime": "2019-10-09T18:08:32+00:00",
    "PopReceipt": "AgAAAAMAAAAAAAAAAgtnj8x+1QE=",
    "sys": {
      "MethodName": "QueueTrigger",
      "UtcNow": "2019-10-09T17:58:32.2205399Z",
      "RandGuid": "24ad4c06-24ad-4e5b-8294-3da9714877e9"
    }
  }
}
```

### <a name="response-payload"></a>Yanıt yükü

Kurala göre, işlev yanıtları anahtar/değer çiftleri olarak biçimlendirilir. Desteklenen anahtarlar şunlardır:

| <nobr>Yük anahtarı</nobr>   | Veri türü | Açıklamalar                                                      |
| ------------- | --------- | ------------------------------------------------------------ |
| `Outputs`     | object    | `bindings` *function.json* öğesinde dizi tarafından tanımlanan yanıt değerlerini barındırır.<br /><br />Örneğin, bir işlev "myQueueOutput" adlı bir sıra çıkış bağlaması ile yapılandırıldıysa `Outputs` adlı bir anahtar içerir `myQueueOutput` ve bu, özel işleyici tarafından kuyruğa gönderilen iletilere ayarlanır. |
| `Logs`        | array     | İletiler, Işlev çağırma günlüklerinde görüntülenir.<br /><br />Azure 'da çalışırken iletiler Application Insights görüntülenir. |
| `ReturnValue` | string    | function.jsdosyadaki bir çıktı yapılandırıldığında bir yanıt sağlamak için kullanılır `$return` .  |

Bu bir yanıt yükünün örneğidir.

```json
{
  "Outputs": {
    "res": {
      "body": "Message enqueued"
    },
    "myQueueOutput": [
      "queue message 1",
      "queue message 2"
    ]
  },
  "Logs": [
    "Log message 1",
    "Log message 2"
  ],
  "ReturnValue": "{\"hello\":\"world\"}"
}
```

## <a name="examples"></a>Örnekler

Özel işleyiciler, HTTP olaylarını almayı destekleyen herhangi bir dilde uygulanabilir. Aşağıdaki örneklerde, go programlama dilini kullanarak nasıl özel işleyicinin uygulanacağı gösterilmektedir.

### <a name="function-with-bindings"></a>Bağlamalarla işlev

Bu örnekte uygulanan senaryo, `order` bir `POST` ürün sırasını temsil eden bir yük ile birlikte kabul eden adlı bir işlevi sunar. İşleve bir sipariş gönderildiğinde, bir kuyruk depolama iletisi oluşturulur ve bir HTTP yanıtı döndürülür.

<a id="bindings-implementation" name="bindings-implementation"></a>

#### <a name="implementation"></a>Uygulama

*Order* adlı bir klasörde, dosyadaki *function.js* , http ile tetiklenen işlevi yapılandırır.

**sipariş/function.jsaçık**

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": ["post"]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    },
    {
      "type": "queue",
      "name": "message",
      "direction": "out",
      "queueName": "orders",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

Bu işlev, [http yanıtı](./functions-bindings-http-webhook-output.md) döndüren ve [kuyruk depolama](./functions-bindings-storage-queue-output.md) iletisini veren bir [http tetiklenen işlev](./functions-bindings-http-webhook-trigger.md) olarak tanımlanır.

Uygulamanın kökünde *host.js* dosyası, `handler.exe` ( `handler` Linux veya MacOS 'ta) adlı yürütülebilir bir dosyayı çalıştıracak şekilde yapılandırılmıştır.

```json
{
  "version": "2.0",
  "customHandler": {
    "description": {
      "defaultExecutablePath": "handler.exe"
    }
  },
  "extensionBundle": {
    "id": "Microsoft.Azure.Functions.ExtensionBundle",
    "version": "[1.*, 2.0.0)"
  }
}
```

Bu, Işlevleri çalışma zamanına gönderilen HTTP isteğidir.

```http
POST http://127.0.0.1:7071/api/order HTTP/1.1
Content-Type: application/json

{
  "id": 1005,
  "quantity": 2,
  "color": "black"
}
```

Işlevler çalışma zamanı, aşağıdaki HTTP isteğini özel işleyiciye gönderir:

```http
POST http://127.0.0.1:<FUNCTIONS_CUSTOMHANDLER_PORT>/order HTTP/1.1
Content-Type: application/json

{
  "Data": {
    "req": {
      "Url": "http://localhost:7071/api/order",
      "Method": "POST",
      "Query": "{}",
      "Headers": {
        "Content-Type": [
          "application/json"
        ]
      },
      "Params": {},
      "Body": "{\"id\":1005,\"quantity\":2,\"color\":\"black\"}"
    }
  },
  "Metadata": {
  }
}
```

> [!NOTE]
> Yükün bazı kısımları breçekimi için kaldırılmıştır.

*handler.exe* , bir Web sunucusu çalıştıran ve işlevler ana bilgisayardan işlev çağırma isteklerine yanıt veren derlenmiş go özel işleyici programıdır.

```go
package main

import (
    "encoding/json"
    "fmt"
    "log"
    "net/http"
    "os"
)

type InvokeRequest struct {
    Data     map[string]json.RawMessage
    Metadata map[string]interface{}
}

type InvokeResponse struct {
    Outputs     map[string]interface{}
    Logs        []string
    ReturnValue interface{}
}

func orderHandler(w http.ResponseWriter, r *http.Request) {
    var invokeRequest InvokeRequest

    d := json.NewDecoder(r.Body)
    d.Decode(&invokeRequest)

    var reqData map[string]interface{}
    json.Unmarshal(invokeRequest.Data["req"], &reqData)

    outputs := make(map[string]interface{})
    outputs["message"] = reqData["Body"]

    resData := make(map[string]interface{})
    resData["body"] = "Order enqueued"
    outputs["res"] = resData
    invokeResponse := InvokeResponse{outputs, nil, nil}

    responseJson, _ := json.Marshal(invokeResponse)

    w.Header().Set("Content-Type", "application/json")
    w.Write(responseJson)
}

func main() {
    customHandlerPort, exists := os.LookupEnv("FUNCTIONS_CUSTOMHANDLER_PORT")
    if !exists {
        customHandlerPort = "8080"
    }
    mux := http.NewServeMux()
    mux.HandleFunc("/order", orderHandler)
    fmt.Println("Go server Listening on: ", customHandlerPort)
    log.Fatal(http.ListenAndServe(":"+customHandlerPort, mux))
}
```

Bu örnekte, özel işleyici HTTP olaylarını işlemek için bir Web sunucusu çalıştırır ve aracılığıyla istekleri dinlemek üzere ayarlanır `FUNCTIONS_CUSTOMHANDLER_PORT` .

Işlevler ' de özgün HTTP isteği almış olsa da `/api/order` , işlev adını (klasör adı) kullanarak özel işleyiciyi çağırır. Bu örnekte, işlevi yolunda tanımlanmıştır `/order` . Ana bilgisayar özel işleyiciyi bir HTTP isteği yoluna gönderir `/order` .

`POST`Bu işleve istekler gönderildiğinde, tetikleyici veri ve işlev meta VERILERI http istek gövdesiyle kullanılabilir. Özgün HTTP istek gövdesine yük tarafından erişilebilir `Data.req.Body` .

İşlevin yanıtı, `Outputs` üyenin dosyadaki *function.js* tanımlandığı gibi çıkışlarla eşleştiği bir JSON değeri taşıdığı anahtar/değer çiftlerine biçimlendirilir.

Bu, bu işleyicinin Işlevler ana bilgisayarına döndürdüğü örnek bir yükdir.

```json
{
  "Outputs": {
    "message": "{\"id\":1005,\"quantity\":2,\"color\":\"black\"}",
    "res": {
      "body": "Order enqueued"
    }
  },
  "Logs": null,
  "ReturnValue": null
}
```

`message`Çıkışı, istekten gelen sipariş verilerine eşit olarak ayarlayarak, işlev verileri yapılandırılan sıraya göre çıkarır. Işlevler ana makinesi, ' de çağırana yapılandırılan HTTP yanıtını döndürür `res` .

### <a name="http-only-function"></a>Yalnızca HTTP işlevi

Ek bağlama veya çıkış içermeyen HTTP ile tetiklenen işlevlerde, işleyicinizin özel işleyici [isteği](#request-payload) ve [Yanıt](#response-payload) yükü yerine http isteği ve yanıtı ile doğrudan çalışmasını isteyebilirsiniz. Bu davranış ayarı kullanılarak *host.js* ' de yapılandırılabilir `enableForwardingHttpRequest` .

> [!IMPORTANT]
> Özel işleyiciler özelliğinin birincil amacı, Azure Işlevleri üzerinde şu anda birinci sınıf desteği olmayan dilleri ve çalışma zamanlarını etkinleştirmektir. Özel işleyiciler kullanarak Web uygulamalarını çalıştırmak mümkün olsa da, Azure Işlevleri standart bir ters proxy değildir. Yanıt akışı, HTTP/2 ve WebSockets gibi bazı özellikler kullanılamaz. Belirli üstbilgiler ve rotalar gibi HTTP isteğinin bazı bileşenleri kısıtlanabilir. Uygulamanız aşırı [soğuk başlatmaya](event-driven-scaling.md#cold-start)da karşılaşabilir.
>
> Bu koşullara yönelik olarak, [Azure App Service](../app-service/overview.md)Web uygulamalarınızı çalıştırmayı göz önünde bulundurun.

Aşağıdaki örnek, HTTP ile tetiklenen bir işlevin ek bağlama veya çıkış olmadan nasıl yapılandırılacağını gösterir. Bu örnekte uygulanan senaryo, veya kabul eden adlı bir işlevi sunar `hello` `GET` `POST` .

<a id="hello-implementation" name="hello-implementation"></a>

#### <a name="implementation"></a>Uygulama

*Hello* adlı bir klasörde, dosyadaki *function.js* , http ile tetiklenen işlevi yapılandırır.

**üzerinde Merhaba/function.js**

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "authLevel": "anonymous",
      "direction": "in",
      "name": "req",
      "methods": ["get", "post"]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    }
  ]
}
```

İşlevi hem hem de isteklerini kabul edecek şekilde yapılandırılmıştır `GET` `POST` ve sonuç değeri adlı bir bağımsız değişken aracılığıyla sağlanır `res` .

Uygulamanın kökünde, dosyadaki *host.js* çalıştırılmak üzere yapılandırılır `handler.exe` ve `enableForwardingHttpRequest` olarak ayarlanır `true` .

```json
{
  "version": "2.0",
  "customHandler": {
    "description": {
      "defaultExecutablePath": "handler.exe"
    },
    "enableForwardingHttpRequest": true
  }
}
```

Ne zaman olduğunda `enableForwardingHttpRequest` `true` , yalnızca http işlevlerinin davranışı varsayılan özel işleyiciler davranışından şu yollarla farklılık gösterir:

* HTTP isteği özel işleyiciler [istek](#request-payload) yükünü içermiyor. Bunun yerine, Işlevler ana bilgisayarı özgün HTTP isteğinin bir kopyasıyla işleyiciyi çağırır.
* Işlevler ana bilgisayarı, tüm sorgu dizesi parametreleri de dahil olmak üzere özgün istekle aynı yol ile işleyiciyi çağırır.
* Işlevler ana bilgisayarı, işleyicinin HTTP yanıtının bir kopyasını özgün isteğine yanıt olarak döndürür.

Aşağıda, Işlevler ana bilgisayarına bir POST isteği verilmiştir. Sonra Işlevler ana bilgisayar, isteğin bir kopyasını aynı yoldaki özel işleyiciye gönderir.

```http
POST http://127.0.0.1:7071/api/hello HTTP/1.1
Content-Type: application/json

{
  "message": "Hello World!"
}
```

Dosya *işleyicisi. go* dosyası bir Web sunucusu ve http işlevi uygular.

```go
package main

import (
    "fmt"
    "io/ioutil"
    "log"
    "net/http"
    "os"
)

func helloHandler(w http.ResponseWriter, r *http.Request) {
    w.Header().Set("Content-Type", "application/json")
    if r.Method == "GET" {
        w.Write([]byte("hello world"))
    } else {
        body, _ := ioutil.ReadAll(r.Body)
        w.Write(body)
    }
}

func main() {
    customHandlerPort, exists := os.LookupEnv("FUNCTIONS_CUSTOMHANDLER_PORT")
    if !exists {
        customHandlerPort = "8080"
    }
    mux := http.NewServeMux()
    mux.HandleFunc("/api/hello", helloHandler)
    fmt.Println("Go server Listening on: ", customHandlerPort)
    log.Fatal(http.ListenAndServe(":"+customHandlerPort, mux))
}
```

Bu örnekte, özel işleyici HTTP olaylarını işlemek için bir Web sunucusu oluşturur ve aracılığıyla istekleri dinlemek üzere ayarlanır `FUNCTIONS_CUSTOMHANDLER_PORT` .

`GET` istekler bir dize döndürerek işlenir ve `POST` isteklerin istek gövdesine erişimi vardır.

Burada Order işlevinin yolu `/api/hello` , özgün istekle aynıdır.

>[!NOTE]
>, `FUNCTIONS_CUSTOMHANDLER_PORT` İşlevi çağırmak için kullanılan genel kullanıma yönelik bağlantı noktasıdır. Bu bağlantı noktası, Işlevler ana bilgisayarı tarafından özel işleyiciyi çağırmak için kullanılır.

## <a name="deploying"></a>Dağıtılmasını

Özel bir işleyici, her Azure Işlevleri barındırma seçeneği için dağıtılabilir. İşleyiciniz işletim sistemi veya platform bağımlılıkları gerektiriyorsa (bir dil çalışma zamanı gibi), [özel bir kapsayıcı](./functions-create-function-linux-custom-image.md)kullanmanız gerekebilir.

Özel işleyiciler için Azure 'da bir işlev uygulaması oluştururken, yığın olarak .NET Core ' u seçmenizi öneririz. Özel işleyiciler için "özel" bir yığın ileride eklenecektir.

Azure Functions Core Tools kullanarak özel bir işleyici uygulamasını dağıtmak için aşağıdaki komutu çalıştırın.

```bash
func azure functionapp publish $functionAppName
```

> [!NOTE]
> Özel işleyicinizi çalıştırmak için gerekli tüm dosyaların klasörde ve dağıtıma dahil olduğundan emin olun. Özel işleyiciniz ikili bir yürütülebilirtir veya platforma özgü bağımlılıklara sahipse, bu dosyaların hedef dağıtım platformuyla eşleştiğinden emin olun.

## <a name="restrictions"></a>Kısıtlamalar

- Özel işleyici Web sunucusunun 60 saniye içinde başlaması gerekir.

## <a name="samples"></a>Örnekler

Çeşitli farklı dillerde işlevlerin nasıl uygulanacağı hakkında örnekler için [GitHub deposunun özel işleyici örneklerine](https://github.com/Azure-Samples/functions-custom-handlers) bakın.

## <a name="troubleshooting-and-support"></a>Sorun giderme ve destek

### <a name="trace-logging"></a>İzleme günlüğü

Özel işleyici işleminiz başlayamazsa veya Işlevler ana bilgisayarıyla iletişim kurma sorunları içeriyorsa, `Trace` konaktan daha fazla tanılama iletisi görmek için, işlev uygulamasının günlük düzeyini olarak artırabilirsiniz.

İşlev uygulamasının varsayılan günlük düzeyini değiştirmek için `logLevel` `logging` *host.jsüzerindeki* bölümündeki ayarı yapılandırın.

```json
{
  "version": "2.0",
  "customHandler": {
    "description": {
      "defaultExecutablePath": "handler.exe"
    }
  },
  "logging": {
    "logLevel": {
      "default": "Trace"
    }
  }
}
```

Işlevler, özel işleyici işlemiyle ilgili bilgiler dahil olmak üzere ek günlük iletileri verir. Özel işleyici işleminizi başlatma veya özel işleyicinizde işlevleri çağırma sorunlarını araştırmak için günlükleri kullanın.

Yerel olarak, Günlükler konsola yazdırılır.

Azure 'da günlük iletilerini görüntülemek için [Application Insights izlemelerini sorgulayın](analyze-telemetry-data.md#query-telemetry-data) . Uygulamanız yüksek hacimli Günlükler üretirse, Application Insights için yalnızca bir günlük iletileri alt kümesi gönderilir. Tüm iletilerin günlüğe kaydedildiğinden emin olmak için [örneklemesi devre dışı bırakın](configure-monitoring.md#configure-sampling) .

### <a name="test-custom-handler-in-isolation"></a>Yalıtım halinde test özel işleyicisi

Özel işleyici uygulamaları bir Web sunucusu işlemidir, bu nedenle, bir veya daha fazla [izleme veya Postman](https://www.postman.com/)gibi bir araç kullanarak sahte [http istekleri](#request-payload) göndererek kendi ve test işlev etkinleştirmeleri üzerinde [](https://curl.haxx.se/) başlatmak faydalı olabilir.

Bu stratejiyi, özel işleyicinizde otomatik testler çalıştırmak için CI/CD işlem hatlarınız içinde de kullanabilirsiniz.

### <a name="execution-environment"></a>Yürütme ortamı

Özel işleyiciler, tipik bir Azure Işlevleri uygulamasıyla aynı ortamda çalışır. Ortamın çalışması için gereken tüm bağımlılıkları içerdiğinden emin olmak için işleyicinizi test edin. Ek bağımlılıklar gerektiren uygulamalar için, bunları Azure Işlevleri [Premium planında](functions-premium-plan.md)barındırılan [özel bir kapsayıcı görüntüsü](functions-create-function-linux-custom-image.md) kullanarak çalıştırmanız gerekebilir.

### <a name="get-support"></a>Destek alın

Özel işleyicilerle bir işlev uygulaması için yardıma ihtiyacınız varsa, normal destek kanalları aracılığıyla bir istek gönderebilirsiniz. Ancak, özel işleyiciler uygulamaları oluşturmak için kullanılan çok çeşitli diller nedeniyle destek sınırsız değildir.

Işlevler ana bilgisayarında özel işleyici işlemiyle başlayan veya iletişim kuran sorunlar varsa destek sağlanır. Özel işleyici sürecinizin, seçilen dil veya çerçeve ile ilgili sorunlar gibi iç çalışmalarını özel sorunlar için, destek ekibimiz bu bağlamda yardım sağlayamadı.

## <a name="next-steps"></a>Sonraki adımlar

[Özel işleyiciler hızlı](create-first-function-vs-code-other.md)başlangıcı ile Go veya Rust 'Ta bir Azure işlevleri uygulaması oluşturmaya başlayın.
