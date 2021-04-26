---
title: Azure Işlevleri HTTP tetikleyicisi
description: HTTP aracılığıyla bir Azure Işlevi çağırmayı öğrenin.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 4550be7ae8c543eea1bdfa085db6f23fe668a121
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105025705"
---
# <a name="azure-functions-http-trigger"></a>Azure Işlevleri HTTP tetikleyicisi

HTTP tetikleyicisi, HTTP isteğiyle bir işlevi çağırmanıza olanak sağlar. HTTP tetikleyicisini kullanarak sunucusuz API 'Ler oluşturabilir ve Web kancalarına yanıt verebilirsiniz.

HTTP ile tetiklenen bir işlev için varsayılan dönüş değeri:

- `HTTP 204 No Content` 2. x ve üzeri Işlevlerde boş bir gövdele
- `HTTP 200 OK` 1. x Işlevlerinde boş bir gövdesiyle

HTTP yanıtını değiştirmek için bir [Çıkış bağlaması](./functions-bindings-http-webhook-output.md)yapılandırın.

HTTP bağlamaları hakkında daha fazla bilgi için bkz. [genel bakış](./functions-bindings-http-webhook.md) ve [Çıkış bağlama başvurusu](./functions-bindings-http-webhook-output.md).

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="example"></a>Örnek

# <a name="c"></a>[C#](#tab/csharp)

Aşağıdaki örnek, sorgu dizesinde veya HTTP isteğinin gövdesinde bir parametre gösteren bir [C# işlevini](functions-dotnet-class-library.md) gösterir `name` . Dönüş değerinin çıkış bağlaması için kullanıldığını, ancak bir dönüş değeri özniteliğinin gerekli olmadığını unutmayın.

```cs
[FunctionName("HttpTriggerCSharp")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)]
    HttpRequest req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string name = req.Query["name"];
    
    string requestBody = String.Empty;
    using (StreamReader streamReader =  new  StreamReader(req.Body))
    {
        requestBody = await streamReader.ReadToEndAsync();
    }
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;
    
    return name != null
        ? (ActionResult)new OkObjectResult($"Hello, {name}")
        : new BadRequestObjectResult("Please pass a name on the query string or in the request body");
}
```

# <a name="c-script"></a>[C# betiği](#tab/csharp-script)

Aşağıdaki örnek, bir *function.js* dosyadaki bir tetikleyiciyi ve bağlamayı kullanan bir [C# betik işlevini](functions-reference-csharp.md) gösterir. İşlevi `name` sorgu dizesinde veya http isteğinin gövdesinde bir parametre arar.

Dosyada *function.js* :

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

[Yapılandırma](#configuration) bölümünde bu özellikler açıklanmaktadır.

Buraya bağlanan C# betik kodu aşağıda verilmiştir `HttpRequest` :

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
    
    string requestBody = String.Empty;
    using (StreamReader streamReader =  new  StreamReader(req.Body))
    {
        requestBody = await streamReader.ReadToEndAsync();
    }
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;
    
    return name != null
        ? (ActionResult)new OkObjectResult($"Hello, {name}")
        : new BadRequestObjectResult("Please pass a name on the query string or in the request body");
}
```

Yerine özel bir nesneye bağlayabilirsiniz `HttpRequest` . Bu nesne, isteğin gövdesinden oluşturulur ve JSON olarak ayrıştırılır. Benzer şekilde, bir tür HTTP yanıt çıkış bağlamasına geçirilebilir ve yanıt gövdesi olarak döndürülen bir `200` durum koduyla birlikte döndürülür.

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

# <a name="java"></a>[Java](#tab/java)

* [Sorgu dizesinden parametreyi oku](#read-parameter-from-the-query-string)
* [POST isteğinden gövde oku](#read-body-from-a-post-request)
* [Bir rotadaki parametreyi oku](#read-parameter-from-a-route)
* [POST isteğinden POJO gövdesini okuyun](#read-pojo-body-from-a-post-request)

Aşağıdaki örneklerde HTTP tetikleyicisi bağlama gösterilmektedir.

#### <a name="read-parameter-from-the-query-string"></a>Sorgu dizesinden parametreyi oku

Bu örnek, sorgu dizesinden adlı bir parametresini okur ve bu parametreyi, `id` içerik türüyle istemciye döndürülen BIR JSON belgesi oluşturmak için kullanır `application/json` .

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

Bu örnek, bir POST isteğinin gövdesini bir olarak okur `String` ve içerik türü ile istemciye döndürülen BIR JSON belgesi oluşturmak için kullanır `application/json` .

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

Bu örnek, adlı zorunlu bir parametreyi `id` ve yol yolundan isteğe bağlı bir parametreyi okur `name` ve içerik türü ile istemciye döndürülen bir JSON belgesi oluşturmak için bunları kullanır `application/json` . T

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

`ToDoItem`Bu örnekte başvurulan sınıfının kodu aşağıda verilmiştir:

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

Bu örnek, POST isteğinin gövdesini okur. İstek gövdesi bir nesneye otomatik olarak seri hale `ToDoItem` getirilir ve içerik türü ile istemciye döndürülür `application/json` . `ToDoItem`Parametresi, sınıfının özelliğine atandığı Için işlevler çalışma zamanı tarafından serileştirilir `body` `HttpMessageResponse.Builder` .

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Aşağıdaki örnek, bir *function.js* dosyadaki bir tetikleyiciyi ve bağlamayı kullanan bir [JavaScript işlevini](functions-reference-node.md) gösterir. İşlevi `name` sorgu dizesinde veya http isteğinin gövdesinde bir parametre arar.

Dosyada *function.js* :

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

[Yapılandırma](#configuration) bölümünde bu özellikler açıklanmaktadır.

JavaScript kodu aşağıda verilmiştir:

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

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Aşağıdaki örnek, bir *function.js* dosyadaki ve [PowerShell işlevindeki](functions-reference-node.md)bir tetikleyiciyi bağlamayı gösterir. İşlevi `name` sorgu dizesinde veya http isteğinin gövdesinde bir parametre arar.

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "Request",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "Response"
    }
  ]
}
```

```powershell
using namespace System.Net

# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Interact with query parameters or the body of the request.
$name = $Request.Query.Name
if (-not $name) {
    $name = $Request.Body.Name
}

$body = "This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response."

if ($name) {
    $body = "Hello, $name. This HTTP triggered function executed successfully."
}

# Associate values to output bindings by calling 'Push-OutputBinding'.
Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = [HttpStatusCode]::OK
    Body       = $body
})
```


# <a name="python"></a>[Python](#tab/python)

Aşağıdaki örnek, bir *function.js* dosyadaki bir tetikleyiciyi ve bağlamayı kullanan bir [Python işlevini](functions-reference-python.md) gösterir. İşlevi `name` sorgu dizesinde veya http isteğinin gövdesinde bir parametre arar.

Dosyada *function.js* :

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
            "name": "$return"
        }
    ]
}
```

[Yapılandırma](#configuration) bölümünde bu özellikler açıklanmaktadır.

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

---

## <a name="attributes-and-annotations"></a>Öznitelikler ve ek açıklamalar

[C# sınıf kitaplıkları](functions-dotnet-class-library.md) ve Java 'da, `HttpTrigger` işlevi yapılandırmak için özniteliği kullanılabilir.

Yetkilendirme düzeyini ve izin verilen HTTP yöntemlerini öznitelik Oluşturucu parametreleri, Web kancası türü ve bir yol şablonunda ayarlayabilirsiniz. Bu ayarlar hakkında daha fazla bilgi için bkz. [yapılandırma](#configuration).

# <a name="c"></a>[C#](#tab/csharp)

Bu örnek, [Httptrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions.Http/HttpTriggerAttribute.cs) özniteliğinin nasıl kullanılacağını gösterir.

```csharp
[FunctionName("HttpTriggerCSharp")]
public static Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous)] HttpRequest req)
{
    ...
}
```

Tüm bir örnek için bkz. [tetikleyici örneği](#example).

# <a name="c-script"></a>[C# betiği](#tab/csharp-script)

Öznitelikler C# betiği tarafından desteklenmez.

# <a name="java"></a>[Java](#tab/java)

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

Tüm bir örnek için bkz. [tetikleyici örneği](#example).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Öznitelikler JavaScript tarafından desteklenmez.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Öznitelikler PowerShell tarafından desteklenmez.

# <a name="python"></a>[Python](#tab/python)

Öznitelikler Python tarafından desteklenmez.

---

## <a name="configuration"></a>Yapılandırma

Aşağıdaki tabloda, dosyasında ve özniteliğinde *function.js* ayarladığınız bağlama yapılandırma özellikleri açıklanmaktadır `HttpTrigger` .

|function.jsözelliği | Öznitelik özelliği |Description|
|---------|---------|----------------------|
| **türüyle** | yok| Gerekli-olarak ayarlanmalıdır `httpTrigger` . |
| **Görünüm** | yok| Gerekli-olarak ayarlanmalıdır `in` . |
| **ada** | yok| Required-istek veya istek gövdesi için işlev kodunda kullanılan değişken adı. |
| <a name="http-auth"></a>**authLevel** |  **AuthLevel** |, Varsa, işlevi çağırmak için istekte hangi anahtarların mevcut olması gerektiğini belirler. Yetkilendirme düzeyi aşağıdaki değerlerden biri olabilir: <ul><li><code>anonymous</code>&mdash;API anahtarı gerekli değildir.</li><li><code>function</code>&mdash;İşleve özgü bir API anahtarı gereklidir. Hiçbiri sağlanmazsa varsayılan değer budur.</li><li><code>admin</code>&mdash;Ana anahtar gereklidir.</li></ul> Daha fazla bilgi için [Yetkilendirme anahtarları](#authorization-keys)hakkında bölümüne bakın. |
| **Yöntem** |**Yöntemler** | İşlevin yanıt verdiği HTTP yöntemlerinin dizisi. Belirtilmemişse, işlev tüm HTTP yöntemlerine yanıt verir. Bkz. [http uç noktasını özelleştirme](#customize-the-http-endpoint). |
| **yol** | **Yolu** | İşlevinizin hangi istek URL 'Lerine yanıt vereceğini denetleyen yol şablonunu tanımlar. Hiçbiri sağlanmadıysa varsayılan değer `<functionname>` . Daha fazla bilgi için bkz. [http uç noktasını özelleştirme](#customize-the-http-endpoint). |
| **Web kancası türü** | **Web kancası türü** | _Yalnızca sürüm 1. x çalışma zamanı için desteklenir._<br/><br/>HTTP tetikleyicisini, belirtilen sağlayıcı için bir [Web kancası](https://en.wikipedia.org/wiki/Webhook) alıcısı olarak davranacak şekilde yapılandırır. `methods`Bu özelliği ayarlarsanız özelliği ayarlamazsanız. Web kancası türü aşağıdaki değerlerden biri olabilir:<ul><li><code>genericJson</code>&mdash;Belirli bir sağlayıcı için mantığı olmayan genel amaçlı bir Web kancası uç noktası. Bu ayar, istekleri yalnızca HTTP POST kullanan ve `application/json` içerik türüyle kısıtlar.</li><li><code>github</code>&mdash;İşlevi [GitHub Web kancalarına](https://developer.github.com/webhooks/)yanıt verir. _AUTHLEVEL_ özelliğini GitHub Web kancaları ile kullanmayın. Daha fazla bilgi için bu makalenin ilerleyen kısımlarında bulunan GitHub Web kancaları bölümüne bakın.</li><li><code>slack</code>&mdash;İşlev, [bolluk web kancalarına](https://api.slack.com/outgoing-webhooks)yanıt veriyor. _AUTHLEVEL_ özelliğini bolluk web kancaları ile kullanmayın. Daha fazla bilgi için bu makalenin ilerleyen kısımlarında yer alarak bolluk web kancaları bölümüne bakın.</li></ul>|

## <a name="payload"></a>Te

Tetikleyici giriş türü `HttpRequest` ya da özel bir tür olarak bildirilmiştir. Seçeneğini belirlerseniz `HttpRequest` , istek nesnesine tam erişim edinirsiniz. Özel bir tür için, çalışma zamanı nesne özelliklerini ayarlamak için JSON istek gövdesini ayrıştırmaya çalışır.

## <a name="customize-the-http-endpoint"></a>HTTP uç noktasını özelleştirme

Varsayılan olarak, bir HTTP tetikleyicisi için bir işlev oluşturduğunuzda, işlev, formun bir yolu ile adreslenebilir:

```http
http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>
```

Bu yolu `route` , http tetikleyicisinin giriş bağlamasındaki isteğe bağlı özelliği kullanarak özelleştirebilirsiniz. Örnek olarak, aşağıdaki *function.js* DOSYADAKI bir `route` http tetikleyicisi için bir özelliği tanımlar:

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

Bu yapılandırma, işlev kodunun adreste, _kategoride_ ve _kimliğinde_ iki parametreyi desteklemesini sağlar. Yönlendirme parametrelerinin bir URL 'de nasıl simgeleştirilmiş olduğu hakkında daha fazla bilgi için bkz. [ASP.NET Core yönlendirme](/aspnet/core/fundamentals/routing#route-constraint-reference).

# <a name="c"></a>[C#](#tab/csharp)

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

# <a name="c-script"></a>[C# betiği](#tab/csharp-script)

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

# <a name="java"></a>[Java](#tab/java)

İşlev yürütme bağlamı, özniteliğinde belirtilen özelliklerdir `HttpTrigger` . Özniteliği yol parametreleri, yetkilendirme düzeyleri, HTTP fiilleri ve gelen istek örneğini tanımlamanızı sağlar.

Rota parametreleri özniteliği aracılığıyla tanımlanır `HttpTrigger` .

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Düğümünde, Işlevleri çalışma zamanı, nesneden istek gövdesini sağlar `context` . Daha fazla bilgi için bkz. [JavaScript tetikleyici örneği](#example).

Aşağıdaki örnek, ' den rota parametrelerinin nasıl okunacağını gösterir `context.bindingData` .

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

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Dosyasında *function.js* belirtilen yol parametrelerine nesnenin bir özelliği olarak erişilebilir `$Request.Params` .

```powershell
$Category = $Request.Params.category
$Id = $Request.Params.id

$Message = "Category:" + $Category + ", ID: " + $Id

Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = [HttpStatusCode]::OK
    Body = $Message
})
```

# <a name="python"></a>[Python](#tab/python)

İşlev yürütme bağlamı olarak belirtilen bir parametre aracılığıyla sunulur `func.HttpRequest` . Bu örnek, bir işlevin veri yolu parametrelerine erişmesini, sorgu dizesi değerlerini ve HTTP yanıtlarını döndürmenize izin veren yöntemleri sağlar.

Tanımlandıktan sonra, yöntemi çağırarak yol parametreleri işlev için kullanılabilir `route_params` .

```python
import logging

import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:

    category = req.route_params.get('category')
    id = req.route_params.get('id')
    message = f"Category: {category}, ID: {id}"

    return func.HttpResponse(message)
```

---

Varsayılan olarak, tüm işlev yollarına *API* ön eki eklenir. Ayrıca, `extensions.http.routePrefix` dosyadaki [host.js](functions-host-json.md) özelliğini kullanarak ön eki özelleştirebilir veya kaldırabilirsiniz. Aşağıdaki örnek, dosyasındaki *host.js* önek için boş bir dize kullanarak *API* yol önekini kaldırır.

```json
{
    "extensions": {
        "http": {
            "routePrefix": ""
        }
    }
}
```

## <a name="using-route-parameters"></a>Rota parametrelerini kullanma

Bir işlevin stilini tanımlayan rota parametreleri `route` her bağlamada kullanılabilir. Örneğin, olarak tanımlanmış bir rota varsa, `"route": "products/{id}"` bir tablo depolama bağlaması, `{id}` bağlama yapılandırmasındaki parametresinin değerini kullanabilir.

Aşağıdaki yapılandırma, `{id}` parametresinin bağlamaya nasıl geçtiğini gösterir `rowKey` .

```json
{
    "type": "table",
    "direction": "in",
    "name": "product",
    "partitionKey": "products",
    "tableName": "products",
    "rowKey": "{id}"
}
```

Yol parametreleri kullandığınızda, `invoke_URL_template` işleviniz için bir otomatik olarak oluşturulur. İstemcileriniz, URL 'sini kullanarak işlevinizi çağırırken URL 'de geçmesi gereken parametreleri anlamak için URL şablonunu kullanabilir. [Azure Portal](https://portal.azure.com) http ile tetiklenen işlevlerinizin birine gidin ve **Işlev URL 'sini al**' ı seçin.

`invoke_URL_template` [List Işlevleri](/rest/api/appservice/webapps/listfunctions) veya [Get Işlevi](/rest/api/appservice/webapps/getfunction)için Azure Resource Manager API 'lerini kullanarak programlı olarak öğesine erişebilirsiniz.

## <a name="working-with-client-identities"></a>İstemci kimlikleriyle çalışma

İşlev uygulamanız [App Service kimlik doğrulaması/yetkilendirme](../app-service/overview-authentication-authorization.md)kullanıyorsa, koddan kimliği doğrulanmış istemcilerle ilgili bilgileri görüntüleyebilirsiniz. Bu bilgiler, [platform tarafından eklenen istek üstbilgileri](../app-service/app-service-authentication-how-to.md#access-user-claims)olarak kullanılabilir.

Ayrıca, bu bilgileri bağlama verilerinden okuyabilirsiniz. Bu özellik yalnızca 2. x ve üzeri Işlevleri çalışma zamanı için kullanılabilir. Bu, şu anda yalnızca .NET dilleri için de kullanılabilir.

# <a name="c"></a>[C#](#tab/csharp)

Kimliği doğrulanmış istemcilerle ilgili bilgiler bir [ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal)olarak sunulmaktadır. ClaimsPrincipal, aşağıdaki örnekte gösterildiği gibi istek bağlamının bir parçası olarak kullanılabilir:

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

# <a name="c-script"></a>[C# betiği](#tab/csharp-script)

Kimliği doğrulanmış istemcilerle ilgili bilgiler bir [ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal)olarak sunulmaktadır. ClaimsPrincipal, aşağıdaki örnekte gösterildiği gibi istek bağlamının bir parçası olarak kullanılabilir:

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

# <a name="java"></a>[Java](#tab/java)

Kimliği doğrulanmış kullanıcı [http üstbilgileri](../app-service/app-service-authentication-how-to.md#access-user-claims)aracılığıyla kullanılabilir.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Kimliği doğrulanmış kullanıcı [http üstbilgileri](../app-service/app-service-authentication-how-to.md#access-user-claims)aracılığıyla kullanılabilir.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Kimliği doğrulanmış kullanıcı [http üstbilgileri](../app-service/app-service-authentication-how-to.md#access-user-claims)aracılığıyla kullanılabilir.

# <a name="python"></a>[Python](#tab/python)

Kimliği doğrulanmış kullanıcı [http üstbilgileri](../app-service/app-service-authentication-how-to.md#access-user-claims)aracılığıyla kullanılabilir.


---

## <a name="function-access-keys"></a><a name="authorization-keys"></a>İşlev erişim tuşları

[!INCLUDE [functions-authorization-keys](../../includes/functions-authorization-keys.md)]

## <a name="obtaining-keys"></a>Anahtarları edinme

Anahtarlar, Azure 'daki işlev uygulamanızın bir parçası olarak depolanır ve bekleyen olarak şifrelenir. Anahtarlarınızı görüntülemek, yenilerini oluşturmak veya yeni değerlere anahtar almak için, [Azure Portal](https://portal.azure.com) http ile tetiklenen işlevlerinizin birine gidin ve **işlev anahtarları**' nı seçin.

Ayrıca, ana bilgisayar anahtarlarını da yönetebilirsiniz. [Azure Portal](https://portal.azure.com) işlev uygulamasına gidin ve **uygulama anahtarları**' nı seçin.

Azure Resource Manager API 'Lerini kullanarak işlev ve konak anahtarlarını programlama yoluyla elde edebilirsiniz. [Işlev anahtarlarını listelemek](/rest/api/appservice/webapps/listfunctionkeys) ve [konak anahtarlarını listelemek](/rest/api/appservice/webapps/listhostkeys)için API 'ler vardır ve dağıtım yuvaları kullanılırken eşdeğer API 'Ler, [Işlev anahtarları yuvası](/rest/api/appservice/webapps/listfunctionkeysslot) ve [liste ana bilgisayar anahtarları yuvası](/rest/api/appservice/webapps/listhostkeysslot)olarak listelenmektedir.

Ayrıca, programlı bir şekilde [Oluştur veya Güncelleştir Işlev gizli](/rest/api/appservice/webapps/createorupdatefunctionsecret)anahtarını kullanarak, [işlev gizli](/rest/api/appservice/webapps/createorupdatefunctionsecretslot)dizisi oluşturma veya güncelleştirme, [ana bilgisayar gizli](/rest/api/appservice/webapps/createorupdatehostsecret) dizisi oluşturma veya güncelleştirme [, ana bilgisayar gizli dizisi API 'leri](/rest/api/appservice/webapps/createorupdatehostsecretslot) oluşturma veya güncelleştirme gibi yeni işlev ve konak anahtarları da oluşturabilirsiniz.

İşlev ve ana bilgisayar anahtarları programlı olarak [silme Işlevi gizli](/rest/api/appservice/webapps/deletefunctionsecret)dizisi kullanılarak silinebilir, [işlev gizli yuvasını](/rest/api/appservice/webapps/deletefunctionsecretslot)silebilir, [konak gizliliğini](/rest/api/appservice/webapps/deletehostsecret)silebilir ve [ana bilgisayar gizli yuva](/rest/api/appservice/webapps/deletehostsecretslot) API 'lerini silebilir.

[İşlev anahtarları almak için eski anahtar yönetim API 'lerini](https://github.com/Azure/azure-functions-host/wiki/Key-management-API)de kullanabilirsiniz, ancak bunun yerine Azure Resource Manager API 'leri kullanılması önerilir.

## <a name="api-key-authorization"></a>API anahtarı yetkilendirmesi

HTTP tetikleyici şablonlarının çoğu istekte bir API anahtarı gerektirir. Bu nedenle, HTTP isteğiniz normalde aşağıdaki URL gibi görünür:

```http
https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?code=<API_KEY>
```

Anahtar, yukarıdaki gibi adlı bir sorgu dizesi değişkenine dahil edilebilir `code` . Bir HTTP başlığına da dahil edilebilir `x-functions-key` . Anahtarın değeri, işlev için tanımlanan herhangi bir işlev anahtarı veya herhangi bir konak anahtarı olabilir.

Anahtar gerektirmeyen anonim isteklere izin verebilirsiniz. Ana anahtarın kullanılmasını da gerekli kılabilirsiniz. JSON bağlama içindeki özelliğini kullanarak varsayılan yetkilendirme düzeyini değiştirirsiniz `authLevel` . Daha fazla bilgi için bkz. [tetikleyici-yapılandırma](#configuration).

> [!NOTE]
> İşlevler yerel olarak çalıştırılırken, kimlik doğrulama, belirtilen Yetkilendirme düzeyi ayarından bağımsız olarak devre dışıdır. Azure 'da yayımladıktan sonra, `authLevel` tetikleyicinizdeki ayar zorlanır. [Bir kapsayıcıda yerel olarak](functions-create-function-linux-custom-image.md#build-the-container-image-and-test-locally)çalıştırılırken Anahtarlar hala gereklidir.


## <a name="secure-an-http-endpoint-in-production"></a>Üretimde bir HTTP uç noktasının güvenliğini sağlama

İşlev uç noktalarınızı üretimde tam olarak güvenli hale getirmek için aşağıdaki işlev uygulama düzeyi güvenlik seçeneklerinden birini uygulamayı göz önünde bulundurmanız gerekir. Bu işlev uygulama düzeyi güvenlik yöntemlerinden birini kullanırken, HTTP ile tetiklenen işlev yetkilendirme düzeyini olarak ayarlamanız gerekir `anonymous` .

[!INCLUDE [functions-enable-auth](../../includes/functions-enable-auth.md)]

#### <a name="deploy-your-function-app-in-isolation"></a>İşlev uygulamanızı yalıtımına dağıtın

[!INCLUDE [functions-deploy-isolation](../../includes/functions-deploy-isolation.md)]

## <a name="webhooks"></a>Web Kancaları

> [!NOTE]
> Web kancası modu yalnızca Işlevler çalışma zamanının sürüm 1. x 'i için kullanılabilir. Bu değişiklik, sürüm 2. x ve üzeri HTTP tetikleyicilerinin performansını geliştirmek için yapılmıştır.

Sürüm 1. x içinde, Web kancası şablonları Web kancası yükleri için ek doğrulama sağlar. Sürüm 2. x ve üzeri sürümlerde, temel HTTP tetikleyicisi hala çalışıyor ve Web kancaları için önerilen yaklaşım. 

### <a name="github-webhooks"></a>GitHub Web kancaları

GitHub Web kancalarına yanıt vermek için, önce bir HTTP tetikleyicisiyle işlevinizi oluşturun ve **Web Kancatürü** özelliğini olarak ayarlayın `github` . Ardından, URL ve API anahtarını GitHub deponuzun **Web kancası Ekle** sayfasına kopyalayın. 

![İşleviniz için bir Web kancası eklemeyi gösteren ekran görüntüsü.](./media/functions-bindings-http-webhook/github-add-webhook.png)

### <a name="slack-webhooks"></a>Bolluk web kancaları

Bolluk Web kancası sizin belirtebilmenizi sağlamak yerine sizin için bir belirteç üretir. bu nedenle, bir işleve özgü anahtarı bolluk 'ten belirtece göre yapılandırmanız gerekir. Bkz. [Yetkilendirme anahtarları](#authorization-keys).

## <a name="webhooks-and-keys"></a>Web kancaları ve anahtarları

Web kancası yetkilendirmesi, HTTP tetikleyicisinin bir parçası olan Web kancası alıcısı bileşeni tarafından işlenir ve mekanizma Web kancası türüne göre farklılık gösterir. Her mekanizma bir anahtara bağlıdır. Varsayılan olarak, "varsayılan" adlı işlev anahtarı kullanılır. Farklı bir anahtar kullanmak için, Web kancası sağlayıcısını aşağıdaki yollarla anahtar adını istekle birlikte gönderecek şekilde yapılandırın:

* **Sorgu dizesi**: sağlayıcı, anahtar adını sorgu dizesi parametresinde (gibi) geçirir `clientid` `https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?clientid=<KEY_NAME>` .
* **İstek üst bilgisi**: sağlayıcı, üst bilgide anahtar adını geçirir `x-functions-clientid` .

## <a name="content-types"></a>İçerik türleri

İkili ve form verilerinin C olmayan bir işleve geçirilmesi, uygun Content-Type üst bilgisini kullanmanızı gerektirir. Desteklenen içerik türleri `octet-stream` ikili veriler ve [çok parçalı türler](https://www.iana.org/assignments/media-types/media-types.xhtml#multipart)için içerir.

### <a name="known-issues"></a>Bilinen sorunlar

C olmayan işlevlerde, içerik türü ile gönderilen istekler, `image/jpeg` `string` işleve geçirilen bir değerle sonuçlanır. Bunlar gibi durumlarda, `string` ham ikili verilere erişmek için değeri el ile bir bayt dizisine dönüştürebilirsiniz.

## <a name="limits"></a>Sınırlar

HTTP istek uzunluğu 100 MB (104.857.600 bayt) ile sınırlıdır ve URL uzunluğu 4 KB (4.096 bayt) ile sınırlıdır. Bu sınırlar, `httpRuntime` çalışma zamanının [Web.config dosyasının](https://github.com/Azure/azure-functions-host/blob/v3.x/src/WebJobs.Script.WebHost/web.config)öğesi tarafından belirtilir.

HTTP tetikleyicisini kullanan bir işlev 230 saniye içinde tamamlanmazsa, [Azure Load Balancer](../app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds) zaman aşımına uğrar ve bir HTTP 502 hatası döndürür. İşlev çalışmaya devam edecektir, ancak HTTP yanıtı dönemeyecektir. Uzun süre çalışan işlevlerde, zaman uyumsuz desenleri izlemenizi ve isteğin durumuna ping ekleyebileceğiniz bir konum döndürmenizi öneririz. Bir işlevin ne kadar süreyle çalıştırılabilmesini hakkında bilgi için bkz. [ölçek ve barındırma-tüketim planı](functions-scale.md#timeout).


## <a name="next-steps"></a>Sonraki adımlar

- [Bir işlevden HTTP yanıtı döndürme](./functions-bindings-http-webhook-output.md)