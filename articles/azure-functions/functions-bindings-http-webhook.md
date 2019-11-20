---
title: Azure Işlevleri HTTP Tetikleyicileri ve bağlamaları
description: Azure Işlevleri 'nde HTTP Tetikleyicileri ve bağlamalarının nasıl kullanılacağını anlayın.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: Azure işlevleri, işlevler, olay işleme, Web kancaları, dinamik işlem, sunucusuz mimari, HTTP, API, REST
ms.service: azure-functions
ms.topic: reference
ms.date: 11/21/2017
ms.author: cshoe
ms.openlocfilehash: 7cce1c9ee6ca5e01b91afd5284ca9abf84d0b56f
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158094"
---
# <a name="azure-functions-http-triggers-and-bindings"></a>Azure Işlevleri HTTP Tetikleyicileri ve bağlamaları

Bu makalede, Azure Işlevlerinde HTTP Tetikleyicileri ve çıkış bağlamalarıyla nasıl çalışılacağı açıklanmaktadır.

HTTP tetikleyicisi, [Web kancalarına](https://en.wikipedia.org/wiki/Webhook)yanıt verecek şekilde özelleştirilebilir.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

Bu makaledeki kod, .NET Core kullanan 2. x söz dizimini varsayılan olarak kullanır. 1\. x sözdizimi hakkında daha fazla bilgi için, bkz. [1. x işlevleri şablonları](https://github.com/Azure/azure-functions-templates/tree/v1.x/Functions.Templates/Templates).

## <a name="packages---functions-1x"></a>Paketler - 1.x işlevleri

HTTP bağlamaları [Microsoft. Azure. WebJobs. Extensions. http](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http) NuGet paketi, sürüm 1. x içinde sağlanır. Paketin kaynak kodu, [Azure-WebJobs-SDK-Extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/v2.x/src/WebJobs.Extensions.Http) GitHub deposunda bulunur.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="packages---functions-2x"></a>Paketler - 2.x işlevleri

HTTP bağlamaları [Microsoft. Azure. WebJobs. Extensions. http](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http) NuGet paketi, sürüm 3. x içinde verilmiştir. Paketin kaynak kodu, [Azure-WebJobs-SDK-Extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Http/) GitHub deposunda bulunur.

[!INCLUDE [functions-package](../../includes/functions-package-auto.md)]

## <a name="trigger"></a>Tetikleyici

HTTP tetikleyicisi, HTTP isteğiyle bir işlevi çağırmanıza olanak sağlar. HTTP tetikleyicisini kullanarak sunucusuz API 'Ler oluşturabilir ve Web kancalarına yanıt verebilirsiniz.

Varsayılan olarak, bir HTTP tetikleyicisi, 1. x Işlevleri içindeki boş bir gövdeden HTTP 200 Tamam 204 veya 2. x Işlevlerinde boş bir gövdeye sahip Içerik yok ' u döndürür. Yanıtı değiştirmek için bir [http çıkış bağlaması](#output)yapılandırın.

## <a name="trigger---example"></a>Tetikleyici - örnek

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Aşağıdaki örnek, sorgu dizesinde veya http isteğinin gövdesinde `name` bir parametre gibi görünen bir [ C# işlevi](functions-dotnet-class-library.md) gösterir. Dönüş değerinin çıkış bağlaması için kullanıldığını, ancak bir dönüş değeri özniteliğinin gerekli olmadığını unutmayın.

```cs
[FunctionName("HttpTriggerCSharp")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] 
    HttpRequest req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string name = req.Query["name"];

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;

    return name != null
        ? (ActionResult)new OkObjectResult($"Hello, {name}")
        : new BadRequestObjectResult("Please pass a name on the query string or in the request body");
}
```

# <a name="c-scripttabcsharp-script"></a>[C#SCRIPT](#tab/csharp-script)

Aşağıdaki örnek, bir *function. JSON* dosyasındaki bir tetikleyici bağlamayı ve bağlamayı kullanan bir [ C# betik işlevini](functions-reference-csharp.md) gösterir. İşlevi sorgu dizesinde veya HTTP isteğinin gövdesinde `name` bir parametre arar.

İşte *function.json* dosyası:

```json
{
    "disabled": false,
    "bindings": [
        {
            "authLevel": "function",
            "name": "req",
            "type": "httpTrigger",
            "direction": "in",
            "methods": [
                "get",
                "post"
            ]
        },
        {
            "name": "$return",
            "type": "http",
            "direction": "out"
        }
    ]
}
```

[Yapılandırma](#trigger---configuration) bölümde, bu özellikleri açıklanmaktadır.

`HttpRequest`bağlanan C# betik kodu aşağıda verilmiştir:

```cs
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;
using Newtonsoft.Json;

public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string name = req.Query["name"];

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;

    return name != null
        ? (ActionResult)new OkObjectResult($"Hello, {name}")
        : new BadRequestObjectResult("Please pass a name on the query string or in the request body");
}
```

`HttpRequest`yerine özel bir nesneye bağlayabilirsiniz. Bu nesne, isteğin gövdesinden oluşturulur ve JSON olarak ayrıştırılır. Benzer şekilde, bir tür HTTP yanıt çıkış bağlamasına geçirilebilir ve bir 200 durum koduyla birlikte yanıt gövdesi olarak döndürülür.

```csharp
using System.Net;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static string Run(Person person, ILogger log)
{   
    return person.Name != null
        ? (ActionResult)new OkObjectResult($"Hello, {person.Name}")
        : new BadRequestObjectResult("Please pass an instance of Person.");
}

public class Person {
     public string Name {get; set;}
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Aşağıdaki örnek, bir *function. JSON* dosyasındaki bir tetikleyici bağlamayı ve bağlamayı kullanan bir [JavaScript işlevini](functions-reference-node.md) gösterir. İşlevi sorgu dizesinde veya HTTP isteğinin gövdesinde `name` bir parametre arar.

İşte *function.json* dosyası:

```json
{
    "disabled": false,    
    "bindings": [
        {
            "authLevel": "function",
            "type": "httpTrigger",
            "direction": "in",
            "name": "req"
        },
        {
            "type": "http",
            "direction": "out",
            "name": "res"
        }
    ]
}
```

[Yapılandırma](#trigger---configuration) bölümde, bu özellikleri açıklanmaktadır.

JavaScript kod aşağıdaki gibidir:

```javascript
module.exports = function(context, req) {
    context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);

    if (req.query.name || (req.body && req.body.name)) {
        context.res = {
            // status defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Aşağıdaki örnek, bir *function. JSON* dosyasındaki bir tetikleyici bağlamayı ve bağlamayı kullanan bir [Python işlevini](functions-reference-python.md) gösterir. İşlevi sorgu dizesinde veya HTTP isteğinin gövdesinde `name` bir parametre arar.

İşte *function.json* dosyası:

```json
{
    "scriptFile": "__init__.py",
    "disabled": false,    
    "bindings": [
        {
            "authLevel": "function",
            "type": "httpTrigger",
            "direction": "in",
            "name": "req"
        },
        {
            "type": "http",
            "direction": "out",
            "name": "res"
        }
    ]
}
```

[Yapılandırma](#trigger---configuration) bölümde, bu özellikleri açıklanmaktadır.

Python kodu aşağıda verilmiştir:

```python
import logging
import azure.functions as func


def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
            "Please pass a name on the query string or in the request body",
            status_code=400
        )
```

# <a name="javatabjava"></a>[Java](#tab/java)

* [Sorgu dizesinden parametreyi oku](#read-parameter-from-the-query-string)
* [POST isteğinden gövde oku](#read-body-from-a-post-request)
* [Bir rotadaki parametreyi oku](#read-parameter-from-a-route)
* [POST isteğinden POJO gövdesini okuyun](#read-pojo-body-from-a-post-request)

Aşağıdaki örneklerde, bir *function. JSON* dosyasındaki http tetikleyicisi bağlamayı ve bağlamayı kullanan ilgili [Java işlevleri](functions-reference-java.md) gösterilmektedir. 

İşte *function.json* dosyası:

```json
{
    "disabled": false,    
    "bindings": [
        {
            "authLevel": "anonymous",
            "type": "httpTrigger",
            "direction": "in",
            "name": "req"
        },
        {
            "type": "http",
            "direction": "out",
            "name": "res"
        }
    ]
}
```

#### <a name="read-parameter-from-the-query-string"></a>Sorgu dizesinden parametreyi oku

Bu örnek, sorgu dizesinden ```id```adlı bir parametreyi okur ve içerik türü ```application/json```olan, istemciye döndürülen bir JSON belgesi oluşturmak için kullanır. 

```java
@FunctionName("TriggerStringGet")
public HttpResponseMessage run(
        @HttpTrigger(name = "req", 
            methods = {HttpMethod.GET}, 
            authLevel = AuthorizationLevel.ANONYMOUS)
        HttpRequestMessage<Optional<String>> request,
        final ExecutionContext context) {
    
    // Item list
    context.getLogger().info("GET parameters are: " + request.getQueryParameters());

    // Get named parameter
    String id = request.getQueryParameters().getOrDefault("id", "");

    // Convert and display
    if (id.isEmpty()) {
        return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                        .body("Document not found.")
                        .build();
    } 
    else {
        // return JSON from to the client
        // Generate document
        final String name = "fake_name";
        final String jsonDocument = "{\"id\":\"" + id + "\", " + 
                                        "\"description\": \"" + name + "\"}";
        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(jsonDocument)
                        .build();
    }
}
```

#### <a name="read-body-from-a-post-request"></a>POST isteğinden gövde oku

Bu örnek, bir POST isteğinin gövdesini bir ```String```olarak okur ve ```application/json```içerik türü ile istemciye döndürülen bir JSON belgesi oluşturmak için kullanır.

```java
    @FunctionName("TriggerStringPost")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", 
              methods = {HttpMethod.POST}, 
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            final ExecutionContext context) {
        
        // Item list
        context.getLogger().info("Request body is: " + request.getBody().orElse(""));

        // Check request body
        if (!request.getBody().isPresent()) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        } 
        else {
            // return JSON from to the client
            // Generate document
            final String body = request.getBody().get();
            final String jsonDocument = "{\"id\":\"123456\", " + 
                                         "\"description\": \"" + body + "\"}";
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(jsonDocument)
                          .build();
        }
    }
```

#### <a name="read-parameter-from-a-route"></a>Bir rotadaki parametreyi oku

Bu örnek, ```id```adlı zorunlu bir parametreyi ve yol yolundan ```name``` isteğe bağlı bir parametreyi okur ve ```application/json```içerik türü ile istemciye döndürülen bir JSON belgesi oluşturmak için bunları kullanır. T

```java
@FunctionName("TriggerStringRoute")
public HttpResponseMessage run(
        @HttpTrigger(name = "req", 
            methods = {HttpMethod.GET}, 
            authLevel = AuthorizationLevel.ANONYMOUS,
            route = "trigger/{id}/{name=EMPTY}") // name is optional and defaults to EMPTY
        HttpRequestMessage<Optional<String>> request,
        @BindingName("id") String id,
        @BindingName("name") String name,
        final ExecutionContext context) {
    
    // Item list
    context.getLogger().info("Route parameters are: " + id);

    // Convert and display
    if (id == null) {
        return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                        .body("Document not found.")
                        .build();
    } 
    else {
        // return JSON from to the client
        // Generate document
        final String jsonDocument = "{\"id\":\"" + id + "\", " + 
                                        "\"description\": \"" + name + "\"}";
        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(jsonDocument)
                        .build();
    }
}
```

#### <a name="read-pojo-body-from-a-post-request"></a>POST isteğinden POJO gövdesini okuyun

Bu örnekte başvurulan ```ToDoItem``` sınıfının kodu aşağıda verilmiştir:

```java

public class ToDoItem {

  private String id;
  private String description;  

  public ToDoItem(String id, String description) {
    this.id = id;
    this.description = description;
  }

  public String getId() {
    return id;
  }

  public String getDescription() {
    return description;
  }
  
  @Override
  public String toString() {
    return "ToDoItem={id=" + id + ",description=" + description + "}";
  }
}

```

Bu örnek, POST isteğinin gövdesini okur. İstek gövdesi bir ```ToDoItem``` nesnesine otomatik olarak serileştirilir ve ```application/json```içerik türü ile istemciye döndürülür. ```ToDoItem``` parametresi, ```HttpMessageResponse.Builder``` sınıfının ```body``` özelliğine atandığı için Işlevler çalışma zamanı tarafından serileştirilir.

```java
@FunctionName("TriggerPojoPost")
public HttpResponseMessage run(
        @HttpTrigger(name = "req", 
            methods = {HttpMethod.POST}, 
            authLevel = AuthorizationLevel.ANONYMOUS)
        HttpRequestMessage<Optional<ToDoItem>> request,
        final ExecutionContext context) {
    
    // Item list
    context.getLogger().info("Request body is: " + request.getBody().orElse(null));

    // Check request body
    if (!request.getBody().isPresent()) {
        return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                        .body("Document not found.")
                        .build();
    } 
    else {
        // return JSON from to the client
        // Generate document
        final ToDoItem body = request.getBody().get();
        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(body)
                        .build();
    }
}
```

---

## <a name="trigger---attributes"></a>Tetikleyici - öznitelikleri

[ C# Sınıf kitaplıkları](functions-dotnet-class-library.md) ve Java 'da, işlevi yapılandırmak için `HttpTrigger` özniteliği kullanılabilir.

Yetkilendirme düzeyini ve izin verilen HTTP yöntemlerini öznitelik Oluşturucu parametreleri, Web kancası türü ve bir yol şablonunda ayarlayabilirsiniz. Bu ayarlar hakkında daha fazla bilgi için bkz. [tetikleyici-yapılandırma](#trigger---configuration).

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Bu örnek, [Httptrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions.Http/HttpTriggerAttribute.cs) özniteliğinin nasıl kullanılacağını gösterir.

```csharp
[FunctionName("HttpTriggerCSharp")]
public static Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous)] HttpRequest req)
{
    ...
}
```

Tüm bir örnek için bkz. [tetikleyici örneği](#trigger---example).

# <a name="c-scripttabcsharp-script"></a>[C#SCRIPT](#tab/csharp-script)

Öznitelikler komut dosyası tarafından C# desteklenmiyor.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Öznitelikler JavaScript tarafından desteklenmez.

# <a name="pythontabpython"></a>[Python](#tab/python)

Öznitelikler Python tarafından desteklenmez.

# <a name="javatabjava"></a>[Java](#tab/java)

Bu örnek, [Httptrigger](https://github.com/Azure/azure-functions-java-library/blob/dev/src/main/java/com/microsoft/azure/functions/annotation/HttpTrigger.java) özniteliğinin nasıl kullanılacağını gösterir.

```java
@FunctionName("HttpTriggerJava")
public HttpResponseMessage<String> HttpTrigger(
        @HttpTrigger(name = "req",
                     methods = {"get"},
                     authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<String> request,
        final ExecutionContext context) {

    ...
}
```

Tüm bir örnek için bkz. [tetikleyici örneği](#trigger---example).

---

## <a name="trigger---configuration"></a>Tetikleyici - yapılandırma

Aşağıdaki tabloda ayarladığınız bağlama yapılandırma özelliklerini açıklayan *function.json* dosya ve `HttpTrigger` özniteliği.

|Function.JSON özelliği | Öznitelik özelliği |Açıklama|
|---------|---------|----------------------|
| **type** | yok| Gerekli-`httpTrigger`olarak ayarlanmalıdır. |
| **direction** | yok| Gerekli-`in`olarak ayarlanmalıdır. |
| **name** | yok| Required-istek veya istek gövdesi için işlev kodunda kullanılan değişken adı. |
| <a name="http-auth"></a>**authLevel** |  **AuthLevel** |, Varsa, işlevi çağırmak için istekte hangi anahtarların mevcut olması gerektiğini belirler. Yetkilendirme düzeyi aşağıdaki değerlerden biri olabilir: <ul><li><code>anonymous</code>&mdash;API anahtarı gerekli değildir.</li><li>işleve özgü bir API anahtarı &mdash;<code>function</code>gereklidir. Hiçbiri sağlanmazsa varsayılan değer budur.</li><li>Ana anahtar gerekli &mdash;<code>admin</code>.</li></ul> Daha fazla bilgi için [Yetkilendirme anahtarları](#authorization-keys)hakkında bölümüne bakın. |
| **methods** |**Methods** | İşlevin yanıt verdiği HTTP yöntemlerinin dizisi. Belirtilmemişse, işlev tüm HTTP yöntemlerine yanıt verir. Bkz. [http uç noktasını özelleştirme](#customize-the-http-endpoint). |
| **route** | **Yolu** | İşlevinizin hangi istek URL 'Lerine yanıt vereceğini denetleyen yol şablonunu tanımlar. Hiçbiri sağlanmadıysa varsayılan değer `<functionname>`. Daha fazla bilgi için bkz. [http uç noktasını özelleştirme](#customize-the-http-endpoint). |
| **webHookType** | **Web kancası türü** | _Yalnızca sürüm 1. x çalışma zamanı için desteklenir._<br/><br/>HTTP tetikleyicisini, belirtilen sağlayıcı için bir [Web kancası](https://en.wikipedia.org/wiki/Webhook) alıcısı olarak davranacak şekilde yapılandırır. Bu özelliği ayarlarsanız `methods` özelliğini ayarlama. Web kancası türü aşağıdaki değerlerden biri olabilir:<ul><li><code>genericJson</code>, belirli bir sağlayıcı için mantık olmadan genel amaçlı bir Web kancası uç noktası &mdash;. Bu ayar, istekleri yalnızca HTTP POST kullanarak ve `application/json` içerik türüyle kısıtlar.</li><li>işlevin [GitHub Web kancalarına](https://developer.github.com/webhooks/)yanıt verdiği <code>github</code>&mdash;. _AUTHLEVEL_ özelliğini GitHub Web kancaları ile kullanmayın. Daha fazla bilgi için bu makalenin ilerleyen kısımlarında bulunan GitHub Web kancaları bölümüne bakın.</li><li>işlevin [bolluk web kancalarına](https://api.slack.com/outgoing-webhooks)yanıt verdiği <code>slack</code>&mdash;. _AUTHLEVEL_ özelliğini bolluk web kancaları ile kullanmayın. Daha fazla bilgi için bu makalenin ilerleyen kısımlarında yer alarak bolluk web kancaları bölümüne bakın.</li></ul>|

## <a name="trigger---usage"></a>Tetikleyici - kullanım

Tetikleyici giriş türü `HttpRequest` veya özel bir tür olarak bildirilmiştir. `HttpRequest`' yi seçerseniz, istek nesnesine tam erişim alırsınız. Özel bir tür için, çalışma zamanı nesne özelliklerini ayarlamak için JSON istek gövdesini ayrıştırmaya çalışır.

### <a name="customize-the-http-endpoint"></a>HTTP uç noktasını özelleştirme

Varsayılan olarak, bir HTTP tetikleyicisi için bir işlev oluşturduğunuzda, işlev, formun bir yolu ile adreslenebilir:

    http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>

HTTP tetikleyicisinin giriş bağlamasında isteğe bağlı `route` özelliğini kullanarak bu yolu özelleştirebilirsiniz. Örnek olarak, aşağıdaki *function. JSON* dosyası bir http tetikleyicisi için `route` özelliğini tanımlar:

```json
{
    "bindings": [
    {
        "type": "httpTrigger",
        "name": "req",
        "direction": "in",
        "methods": [ "get" ],
        "route": "products/{category:alpha}/{id:int?}"
    },
    {
        "type": "http",
        "name": "res",
        "direction": "out"
    }
    ]
}
```

Bu yapılandırmayı kullanarak, işlev artık özgün yol yerine aşağıdaki rota ile adreslenebilir.

```
http://<APP_NAME>.azurewebsites.net/api/products/electronics/357
```

Bu, işlev kodunun adreste, _kategoride_ ve _kimliğinde_iki parametreyi desteklemesini sağlar.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Parametrelerinizi kullanarak herhangi bir [Web API yolu kısıtlaması](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints) kullanabilirsiniz. Aşağıdaki C# işlev kodu her iki parametrenin de kullanımını sağlar.

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;

public static IActionResult Run(HttpRequest req, string category, int? id, ILogger log)
{
    var message = String.Format($"Category: {category}, ID: {id}");
    return (ActionResult)new OkObjectResult(message);
}
```

# <a name="c-scripttabcsharp-script"></a>[C#SCRIPT](#tab/csharp-script)

Parametrelerinizi kullanarak herhangi bir [Web API yolu kısıtlaması](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints) kullanabilirsiniz. Aşağıdaki C# işlev kodu her iki parametrenin de kullanımını sağlar.

```csharp
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;

public static IActionResult Run(HttpRequest req, string category, int? id, ILogger log)
{
    var message = String.Format($"Category: {category}, ID: {id}");
    return (ActionResult)new OkObjectResult(message);
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Düğümünde, Işlevleri çalışma zamanı `context` nesnesinden istek gövdesini sağlar. Daha fazla bilgi için bkz. [JavaScript tetikleyici örneği](#trigger---example).

Aşağıdaki örnek, `context.bindingData`yönlendirme parametrelerinin nasıl okunacağını gösterir.

```javascript
module.exports = function (context, req) {

    var category = context.bindingData.category;
    var id = context.bindingData.id;
    var message = `Category: ${category}, ID: ${id}`;

    context.res = {
        body: message;
    }

    context.done();
}
```

# <a name="pythontabpython"></a>[Python](#tab/python)

İşlev yürütme bağlamı, `func.HttpRequest`olarak belirtilen bir parametre aracılığıyla sunulur. Bu örnek, bir işlevin veri yolu parametrelerine erişmesini, sorgu dizesi değerlerini ve HTTP yanıtlarını döndürmenize izin veren yöntemleri sağlar.

Tanımlandıktan sonra, yol parametreleri `route_params` yöntemi çağırarak işlev için kullanılabilir.

```python
import logging

import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:

    category = req.route_params.get('category')
    id = req.route_params.get('id')
    message = f"Category: {category}, ID: {id}"

    return func.HttpResponse(message)
```

# <a name="javatabjava"></a>[Java](#tab/java)

İşlev yürütme bağlamı, `HttpTrigger` özniteliğinde belirtilen özelliklerdir. Özniteliği yol parametreleri, yetkilendirme düzeyleri, HTTP fiilleri ve gelen istek örneğini tanımlamanızı sağlar.

Yol parametreleri `HttpTrigger` özniteliği aracılığıyla tanımlanır.

```java
package com.function;

import java.util.*;
import com.microsoft.azure.functions.annotation.*;
import com.microsoft.azure.functions.*;

public class HttpTriggerJava {
    public HttpResponseMessage<String> HttpTrigger(
            @HttpTrigger(name = "req",
                         methods = {"get"},
                         authLevel = AuthorizationLevel.FUNCTION,
                         route = "products/{category:alpha}/{id:int}") HttpRequestMessage<String> request,
            @BindingName("category") String category,
            @BindingName("id") int id,
            final ExecutionContext context) {

        String message = String.format("Category  %s, ID: %d", category, id);
        return request.createResponseBuilder(HttpStatus.OK).body(message).build();
    }
}
```

---

Varsayılan olarak, tüm işlev yollarına *API*ön eki eklenir. Ayrıca, [Host. JSON](functions-host-json.md) dosyanızdaki `http.routePrefix` özelliğini kullanarak ön eki özelleştirebilir veya kaldırabilirsiniz. Aşağıdaki örnek, *Host. JSON* dosyasındaki önek için boş bir dize kullanarak *API* yol önekini kaldırır.

```json
{
    "http": {
    "routePrefix": ""
    }
}
```

### <a name="working-with-client-identities"></a>İstemci kimlikleriyle çalışma

İşlev uygulamanız [App Service kimlik doğrulaması/yetkilendirme](../app-service/overview-authentication-authorization.md)kullanıyorsa, koddan kimliği doğrulanmış istemcilerle ilgili bilgileri görüntüleyebilirsiniz. Bu bilgiler, [platform tarafından eklenen istek üstbilgileri](../app-service/app-service-authentication-how-to.md#access-user-claims)olarak kullanılabilir. 

Ayrıca, bu bilgileri bağlama verilerinden okuyabilirsiniz. Bu özellik yalnızca 2. x çalışma zamanı Işlevleri için kullanılabilir. Bu, şu anda yalnızca .NET dilleri için de kullanılabilir.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Kimliği doğrulanmış istemcilerle ilgili bilgiler bir [ClaimsPrincipal](https://docs.microsoft.com/dotnet/api/system.security.claims.claimsprincipal)olarak sunulmaktadır. ClaimsPrincipal, aşağıdaki örnekte gösterildiği gibi istek bağlamının bir parçası olarak kullanılabilir:

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using System.Security.Claims;

public static IActionResult Run(HttpRequest req, ILogger log)
{
    ClaimsPrincipal identities = req.HttpContext.User;
    // ...
    return new OkObjectResult();
}
```

Alternatif olarak, ClaimsPrincipal yalnızca işlev imzasında ek bir parametre olarak eklenebilir:

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using System.Security.Claims;
using Newtonsoft.Json.Linq;

public static void Run(JObject input, ClaimsPrincipal principal, ILogger log)
{
    // ...
    return;
}
```

# <a name="c-scripttabcsharp-script"></a>[C#SCRIPT](#tab/csharp-script)

Kimliği doğrulanmış istemcilerle ilgili bilgiler bir [ClaimsPrincipal](https://docs.microsoft.com/dotnet/api/system.security.claims.claimsprincipal)olarak sunulmaktadır. ClaimsPrincipal, aşağıdaki örnekte gösterildiği gibi istek bağlamının bir parçası olarak kullanılabilir:

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using System.Security.Claims;

public static IActionResult Run(HttpRequest req, ILogger log)
{
    ClaimsPrincipal identities = req.HttpContext.User;
    // ...
    return new OkObjectResult();
}
```

Alternatif olarak, ClaimsPrincipal yalnızca işlev imzasında ek bir parametre olarak eklenebilir:

```csharp
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using System.Security.Claims;
using Newtonsoft.Json.Linq;

public static void Run(JObject input, ClaimsPrincipal principal, ILogger log)
{
    // ...
    return;
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Kimliği doğrulanmış kullanıcı [http üstbilgileri](../app-service/app-service-authentication-how-to.md#access-user-claims)aracılığıyla kullanılabilir.

# <a name="pythontabpython"></a>[Python](#tab/python)

Kimliği doğrulanmış kullanıcı [http üstbilgileri](../app-service/app-service-authentication-how-to.md#access-user-claims)aracılığıyla kullanılabilir.

# <a name="javatabjava"></a>[Java](#tab/java)

Kimliği doğrulanmış kullanıcı [http üstbilgileri](../app-service/app-service-authentication-how-to.md#access-user-claims)aracılığıyla kullanılabilir.

---

### <a name="authorization-keys"></a>Yetkilendirme anahtarları

İşlevler, geliştirme sırasında HTTP işlev uç noktalarınıza erişmeyi daha zor hale getirmek için anahtarları kullanmanıza olanak sağlar.  Standart bir HTTP tetikleyicisi istekte böyle bir API anahtarının bulunmasını gerektirebilir. 

> [!IMPORTANT]
> Anahtarlar geliştirme sırasında HTTP uç noktalarınızı gizleme konusunda yardımcı olabilir, ancak üretimde bir HTTP tetikleyicisini güvenli hale getirmenin bir yolu olarak tasarlanmamıştır. Daha fazla bilgi edinmek için bkz. [üretimde BIR HTTP uç noktası güvenli hale getirme](#secure-an-http-endpoint-in-production).

> [!NOTE]
> 1\. x çalışma zamanında, Web kancası sağlayıcıları, sağlayıcının neleri desteklediğine bağlı olarak çeşitli yollarla istekleri yetkilendirmek için anahtarlar kullanabilir. Bu, [Web kancaları ve anahtarları](#webhooks-and-keys)kapsamına alınmıştır. Sürüm 2. x çalışma zamanı, Web kancası sağlayıcıları için yerleşik destek içermez.

İki tür anahtar vardır:

* **Ana bilgisayar anahtarları**: Bu anahtarlar, işlev uygulaması içindeki tüm işlevler tarafından paylaşılır. API anahtarı olarak kullanıldığında, bu, işlev uygulaması içindeki herhangi bir işleve erişime izin verir.
* **İşlev anahtarları**: Bu anahtarlar yalnızca tanımlandıkları belirli işlevler için geçerlidir. API anahtarı olarak kullanıldığında, bunlar yalnızca bu işleve erişime izin verir.

Her anahtar başvuru için adlandırılır ve işlev ve ana bilgisayar düzeyinde bir varsayılan anahtar ("varsayılan" olarak adlandırılır) vardır. İşlev anahtarları ana bilgisayar anahtarlarına göre önceliklidir. Aynı ada sahip iki anahtar tanımlandığında, işlev anahtarı her zaman kullanılır.

Her işlev uygulamasının özel bir **ana anahtarı**da vardır. Bu anahtar, çalışma zamanı API 'Lerine yönetim erişimi sağlayan `_master`adlı bir ana bilgisayar anahtarıdır. Bu anahtar iptal edilemez. `admin`bir Yetkilendirme düzeyi ayarladığınızda, isteklerin ana anahtarı kullanması gerekir; diğer herhangi bir anahtar, yetkilendirme hatasına neden olur.

> [!CAUTION]  
> Ana anahtar tarafından verilen işlev uygulamanızda yükseltilmiş izinler nedeniyle, bu anahtarı üçüncü taraflarla paylaşmamalıdır veya yerel istemci uygulamalarında dağıtmanız gerekir. Yönetici yetkilendirme düzeyini seçerken dikkatli olun.

### <a name="obtaining-keys"></a>Anahtarları edinme

Anahtarlar, Azure 'daki işlev uygulamanızın bir parçası olarak depolanır ve bekleyen olarak şifrelenir. Anahtarlarınızı görüntülemek, yenilerini oluşturmak veya yeni değerlere anahtar almak için, [Azure Portal](https://portal.azure.com) http ile tetiklenen işlevlerinizin birine gidin ve **Yönet**' i seçin.

![Portalda işlev anahtarlarını yönetin.](./media/functions-bindings-http-webhook/manage-function-keys.png)

[Anahtar yönetimi API 'lerini](https://github.com/Azure/azure-functions-host/wiki/Key-management-API)kullanarak işlev anahtarlarını programlama yoluyla elde edebilirsiniz.

### <a name="api-key-authorization"></a>API anahtarı yetkilendirmesi

HTTP tetikleyici şablonlarının çoğu istekte bir API anahtarı gerektirir. Bu nedenle, HTTP isteğiniz normalde aşağıdaki URL gibi görünür:

    https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?code=<API_KEY>

Anahtar, yukarıdaki `code`adlı bir sorgu dizesi değişkenine dahil edilebilir. Ayrıca, bir `x-functions-key` HTTP başlığına da dahil edilebilir. Anahtarın değeri, işlev için tanımlanan herhangi bir işlev anahtarı veya herhangi bir konak anahtarı olabilir.

Anahtar gerektirmeyen anonim isteklere izin verebilirsiniz. Ana anahtarın kullanılmasını da gerekli kılabilirsiniz. JSON bağlamasındaki `authLevel` özelliğini kullanarak varsayılan yetkilendirme düzeyini değiştirirsiniz. Daha fazla bilgi için bkz. [tetikleyici-yapılandırma](#trigger---configuration).

> [!NOTE]
> İşlevler yerel olarak çalıştırılırken, yetkilendirme, belirtilen kimlik doğrulama düzeyi ayarından bağımsız olarak devre dışı bırakılır. Azure 'da yayımladıktan sonra, tetikleyicinizdeki `authLevel` ayarı zorlanır. [Bir kapsayıcıda yerel olarak](functions-create-function-linux-custom-image.md#run-the-image-locally)çalıştırılırken Anahtarlar hala gereklidir.


### <a name="secure-an-http-endpoint-in-production"></a>Üretimde bir HTTP uç noktasının güvenliğini sağlama

İşlev uç noktalarınızı üretimde tam olarak güvenli hale getirmek için aşağıdaki işlev uygulama düzeyi güvenlik seçeneklerinden birini uygulamayı göz önünde bulundurmanız gerekir:

* İşlev uygulamanız için App Service kimlik doğrulaması/yetkilendirme 'yi açın. App Service platformu, istemcilerin kimliğini doğrulamak için Azure Active Directory (AAD) ve çeşitli üçüncü taraf kimlik sağlayıcılarını kullanmanıza olanak sağlar. İşlevleriniz için özel yetkilendirme kuralları uygulamak üzere bunu kullanabilir ve işlev kodunuzda kullanıcı bilgileriyle çalışabilirsiniz. Daha fazla bilgi için bkz. [Azure App Service 'Da kimlik doğrulama ve yetkilendirme](../app-service/overview-authentication-authorization.md) ve [istemci kimlikleriyle çalışma](#working-with-client-identities).

* İsteklerin kimliğini doğrulamak için Azure API Management (APıM) kullanın. APıM, gelen istekler için çeşitli API güvenliği seçenekleri sağlar. Daha fazla bilgi için bkz. [API Management kimlik doğrulama ilkeleri](../api-management/api-management-authentication-policies.md). APıM ile, işlev uygulamanızı yalnızca APıM örneğinizin IP adresinden gelen istekleri kabul edecek şekilde yapılandırabilirsiniz. Daha fazla bilgi için bkz. [IP adresi kısıtlamaları](ip-addresses.md#ip-address-restrictions).

* İşlev uygulamanızı bir Azure App Service Ortamı dağıtın (Ao). ATıCı, işlevlerinizin çalıştırılacağı adanmış bir barındırma ortamı sağlar. Ao, tüm gelen isteklerin kimliğini doğrulamak için kullanabileceğiniz tek bir ön uç ağ geçidi yapılandırmanıza olanak tanır. Daha fazla bilgi için bkz. [App Service ortamı Için Web uygulaması güvenlik duvarı (WAF) yapılandırma](../app-service/environment/app-service-app-service-environment-web-application-firewall.md).

Bu işlev uygulama düzeyi güvenlik yöntemlerinden birini kullanırken, HTTP ile tetiklenen işlev kimlik doğrulama düzeyini `anonymous`olarak ayarlamanız gerekir.

### <a name="webhooks"></a>Web Kancaları

> [!NOTE]
> Web kancası modu yalnızca Işlevler çalışma zamanının sürüm 1. x 'i için kullanılabilir. Bu değişiklik, sürüm 2. x ' teki HTTP tetikleyicilerinin performansını geliştirmek için yapılmıştır.

Sürüm 1. x içinde, Web kancası şablonları Web kancası yükleri için ek doğrulama sağlar. 2\. x sürümünde, temel HTTP tetikleyicisi hala çalışıyor ve Web kancaları için önerilen yaklaşım. 

#### <a name="github-webhooks"></a>GitHub Web kancaları

GitHub Web kancalarına yanıt vermek için, önce bir HTTP tetikleyicisiyle işlevinizi oluşturun ve **Web Kancatürü** özelliğini `github`olarak ayarlayın. Ardından, URL ve API anahtarını GitHub deponuzun **Web kancası Ekle** sayfasına kopyalayın. 

![](./media/functions-bindings-http-webhook/github-add-webhook.png)

#### <a name="slack-webhooks"></a>Bolluk web kancaları

Bolluk Web kancası sizin belirtebilmenizi sağlamak yerine sizin için bir belirteç üretir. bu nedenle, bir işleve özgü anahtarı bolluk 'ten belirtece göre yapılandırmanız gerekir. Bkz. [Yetkilendirme anahtarları](#authorization-keys).

### <a name="webhooks-and-keys"></a>Web kancaları ve anahtarları

Web kancası yetkilendirmesi, HTTP tetikleyicisinin bir parçası olan Web kancası alıcısı bileşeni tarafından işlenir ve mekanizma Web kancası türüne göre farklılık gösterir. Her mekanizma bir anahtara bağlıdır. Varsayılan olarak, "varsayılan" adlı işlev anahtarı kullanılır. Farklı bir anahtar kullanmak için, Web kancası sağlayıcısını aşağıdaki yollarla anahtar adını istekle birlikte gönderecek şekilde yapılandırın:

* **Sorgu dizesi**: sağlayıcı, `https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?clientid=<KEY_NAME>`gibi `clientid` sorgu dizesi parametresinde anahtar adı geçirir.
* **İstek üst bilgisi**: sağlayıcı, `x-functions-clientid` üst bilgisinde anahtar adını geçirir.

## <a name="trigger---limits"></a>Tetikleme-sınırlar

HTTP istek uzunluğu 100 MB (104.857.600 bayt) ile sınırlıdır ve URL uzunluğu 4 KB (4.096 bayt) ile sınırlıdır. Bu sınırlar, çalışma zamanının [Web. config dosyasının](https://github.com/Azure/azure-webjobs-sdk-script/blob/v1.x/src/WebJobs.Script.WebHost/Web.config)`httpRuntime` öğesi tarafından belirtilir.

HTTP tetikleyicisini kullanan bir işlev yaklaşık 2,5 dakika içinde tamamlanmazsa, ağ geçidi zaman aşımına uğrar ve bir HTTP 502 hatası döndürür. İşlev çalışmaya devam edecektir, ancak HTTP yanıtı dönemeyecektir. Uzun süre çalışan işlevlerde, zaman uyumsuz desenleri izlemenizi ve isteğin durumuna ping ekleyebileceğiniz bir konum döndürmenizi öneririz. Bir işlevin ne kadar süreyle çalıştırılabilmesini hakkında bilgi için bkz. [ölçek ve barındırma-tüketim planı](functions-scale.md#timeout).

## <a name="output"></a>Çıktı

Http istek göndericisine yanıt vermek için HTTP çıkış bağlamasını kullanın. Bu bağlama bir HTTP tetikleyicisi gerektirir ve tetikleyicinin isteğiyle ilişkili yanıtı özelleştirmenize olanak sağlar. HTTP çıkış bağlaması sağlanmazsa, bir HTTP tetikleyicisi, 1. x Işlevlerinde boş bir gövdele HTTP 200 Tamam 204 veya 2. x Işlevleri 'nde boş bir gövdeye sahip Içerik yok ' u döndürür.

## <a name="output---configuration"></a>Çıkış - yapılandırma

Aşağıdaki tabloda, *function. JSON* dosyasında ayarladığınız bağlama yapılandırma özellikleri açıklanmaktadır. Sınıf C# kitaplıkları için, bu *function. JSON* özelliklerine karşılık gelen bir öznitelik özelliği yok.

|Özellik  |Açıklama  |
|---------|---------|
| **type** |Ayarlanmalıdır `http`. |
| **direction** | Ayarlanmalıdır `out`. |
| **name** | Yanıt için işlev kodunda kullanılan değişken adı veya dönüş değerini kullanmak için `$return`. |

## <a name="output---usage"></a>Çıkış - kullanım

HTTP yanıtı göndermek için, dil standardı yanıt düzenlerini kullanın. Veya C# C# betik içinde, işlev dönüş türü `IActionResult` veya `Task<IActionResult>`. ' C#De, dönüş değeri özniteliği gerekli değildir.

Örneğin, bkz. [tetikleyici örneği](#trigger---example).

## <a name="hostjson-settings"></a>Host.JSON ayarları

Bu bölümde sürümünde bu bağlama için kullanılabilen genel yapılandırma ayarları açıklanmaktadır 2.x. Aşağıdaki örnek host.json dosyasını yalnızca bu bağlama için sürüm 2.x ayarları içerir. Sürümündeki genel yapılandırma ayarları hakkında daha fazla bilgi için 2.x bkz [sürümü Azure işlevleri için host.json başvurusu 2.x](functions-host-json.md).

> [!NOTE]
> İşlevlerde host.json başvurusu için 1.x, bkz: [Azure işlevleri için host.json başvurusu 1.x](functions-host-json-v1.md#http).

```json
{
    "extensions": {
        "http": {
            "routePrefix": "api",
            "maxOutstandingRequests": 200,
            "maxConcurrentRequests": 100,
            "dynamicThrottlesEnabled": true,
            "hsts": {
                "isEnabled": true,
                "maxAge": "10"
            },
            "customHeaders": {
                "X-Content-Type-Options": "nosniff"
            }
        }
    }
}
```

|Özellik  |Varsayılan | Açıklama |
|---------|---------|---------| 
| customHeaders|yok|HTTP yanıtında özel üstbilgiler ayarlamanıza olanak sağlar. Önceki örnek, içerik türü algılaması olmaması için `X-Content-Type-Options` üst bilgisini yanıta ekler. |
|dynamicThrottlesEnabled|doğru<sup>\*</sup>|Bu ayar etkinleştirildiğinde, istek işleme işlem hattının bağlantılar/iş parçacıkları/işlemler/bellek/CPU/vb gibi sistem performans sayaçlarını düzenli olarak denetlemesini sağlar. bu sayaçlardan herhangi biri yerleşik yüksek eşikten (%80%) olursa, sayaçlar normal düzeylere dönene kadar istekler 429 "çok meşgul" yanıtıyla reddedilir.<br/><sup>\*</sup> Bir tüketim planında varsayılan değer `true`. Adanmış bir planda varsayılan değer `false`.|
|HSTS|etkin değil|`isEnabled` `true`olarak ayarlandığında, [.NET Core 'un http katı taşıma güvenliği (HSTS) davranışı](/aspnet/core/security/enforcing-ssl?view=aspnetcore-3.0&tabs=visual-studio#hsts) [`HstsOptions` sınıfında](/dotnet/api/microsoft.aspnetcore.httpspolicy.hstsoptions?view=aspnetcore-3.0)tanımlandığı gibi zorlanır. Yukarıdaki örnek ayrıca [`maxAge`](/dotnet/api/microsoft.aspnetcore.httpspolicy.hstsoptions.maxage?view=aspnetcore-3.0#Microsoft_AspNetCore_HttpsPolicy_HstsOptions_MaxAge) özelliğini 10 gün olarak ayarlar. `hsts` desteklenen özellikleri şunlardır: <table><tr><th>Özellik</th><th>Açıklama</th></tr><tr><td>Excludedkonakları</td><td>HSTS üstbilgisinin eklendiği ana bilgisayar adlarının dize dizisi.</td></tr><tr><td>includeSubDomains</td><td>Strict-Transport-Security üstbilgisinin ıncludealt etki alanı parametresinin etkinleştirilip etkinleştirilmeyeceğini gösteren Boolean değer.</td></tr><tr><td>maxAge</td><td>Strict-Transport-Security üstbilgisinin Max-Age parametresini tanımlayan dize.</td></tr><tr><td>preload</td><td>Strict-Transport-Security üstbilgisinin preload parametresinin etkin olup olmadığını gösteren Boolean.</td></tr></table>|
|maxConcurrentRequests|100<sup>\*</sup>|Paralel olarak yürütülen http işlevlerinin maksimum sayısı. Bu, kaynak kullanımının yönetilmesine yardımcı olabilecek eşzamanlılık denetlemenize olanak tanır. Örneğin, eşzamanlılık çok yüksek olduğunda sorunlara yol açacağından, çok fazla sistem kaynağı (bellek/CPU/yuva) kullanan bir http işleviniz olabilir. Ya da bir üçüncü taraf hizmetine giden istekleri yapan bir işleviniz olabilir ve bu çağrıların hız sınırlı olması gerekir. Bu durumlarda, burada bir kısıtlama uygulanması yardımcı olabilir. <br/><sup>*</sup> Tüketim planı için varsayılan değer 100 ' dir. Adanmış bir plan için varsayılan değer sınırsız (`-1`).|
|maxOutstandingRequests|200<sup>\*</sup>|Belirli bir zamanda tutulan bekleyen istek sayısı üst sınırı. Bu sınır, kuyruğa alınmış ancak yürütmeyi başlatmayan isteklerin yanı sıra devam eden yürütmeler içerir. Bu sınırın üzerindeki tüm gelen istekler, 429 "çok meşgul" yanıtıyla reddedilir. Bu, çağıranların zamana dayalı yeniden deneme stratejileri kullanmasına izin verir ve ayrıca en fazla istek gecikme sürelerini denetlemenize yardımcı olur. Bu, yalnızca betik ana bilgisayar yürütme yolu içinde oluşan kuyruğu denetler. ASP.NET istek kuyruğu gibi diğer kuyruklar da etkin olmaya devam eder ve bu ayardan etkilenmez. <br/><sup>\*</sup>, bir tüketim planı için varsayılan değer 200 ' dir. Adanmış bir plan için varsayılan değer sınırsız (`-1`).|
|routePrefix|API|Tüm yollar için geçerli olan rota öneki. Varsayılan ön eki kaldırmak için boş bir dize kullanın. |


## <a name="next-steps"></a>Sonraki adımlar

[Azure işlevleri Tetikleyicileri ve bağlamaları hakkında daha fazla bilgi edinin](functions-triggers-bindings.md)
