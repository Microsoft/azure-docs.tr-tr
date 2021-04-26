---
title: Azure Işlevleri için JavaScript geliştirici başvurusu
description: JavaScript kullanarak işlevleri geliştirmeyi anlayın.
ms.assetid: 45dedd78-3ff9-411f-bb4b-16d29a11384c
ms.topic: conceptual
ms.date: 03/07/2021
ms.custom: devx-track-js
ms.openlocfilehash: 971fb2a3239614a708e14c109e567081f1ec9ff6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102614913"
---
# <a name="azure-functions-javascript-developer-guide"></a>Azure Işlevleri JavaScript Geliştirici Kılavuzu

Bu kılavuz, JavaScript kullanarak Azure Işlevleri geliştirmeye yardımcı olacak ayrıntılı bilgiler içerir.

Express.js, Node.js veya JavaScript geliştiricisi olarak Azure Işlevleri 'ne yeni bir deyişle, lütfen önce aşağıdaki makalelerden birini okuyun:

| Kullanmaya başlama | Kavramlar| Kılavuzlu öğrenme |
| -- | -- | -- | 
| <ul><li>[ Visual Studio Code kullanarakNode.js işlevi](./create-first-function-vs-code-node.md)</li><li>[ Terminal/komut istemiyleNode.js işlevi](./create-first-function-cli-node.md)</li><li>[ Azure portal kullanarakNode.js işlevi](functions-create-function-app-portal.md)</li></ul> | <ul><li>[Geliştirici kılavuzu](functions-reference.md)</li><li>[Barındırma seçenekleri](functions-scale.md)</li><li>[TypeScript işlevleri](#typescript)</li><li>[Performans &nbsp; konuları](functions-best-practices.md)</li></ul> | <ul><li>[Sunucusuz uygulamalar oluşturma](/learn/paths/create-serverless-applications/)</li><li>[Node.js ve hızlı API 'Leri sunucusuz API 'lere yeniden düzenleme](/learn/modules/shift-nodejs-express-apis-serverless/)</li></ul> |

## <a name="javascript-function-basics"></a>JavaScript işlevi temelleri

JavaScript (Node.js) işlevi, tetiklendiğinde yürütülen bir dışarıya kaydedilir `function` ([Tetikleyiciler function.jsüzerinde yapılandırılır](functions-triggers-bindings.md)). Her işleve geçirilen ilk bağımsız değişken, `context` bağlama verilerini alma ve gönderme, günlüğe kaydetme ve çalışma zamanına iletişim için kullanılan bir nesnedir.

## <a name="folder-structure"></a>Klasör yapısı

JavaScript projesi için gereken klasör yapısı aşağıdaki gibi görünür. Bu varsayılan değer değiştirilebilir. Daha fazla bilgi için aşağıdaki [scriptfile](#using-scriptfile) bölümüne bakın.

```
FunctionsProject
 | - MyFirstFunction
 | | - index.js
 | | - function.json
 | - MySecondFunction
 | | - index.js
 | | - function.json
 | - SharedCode
 | | - myFirstHelperFunction.js
 | | - mySecondHelperFunction.js
 | - node_modules
 | - host.json
 | - package.json
 | - extensions.csproj
```

Projenin kökünde, işlev uygulamasını yapılandırmak için kullanılabilecek bir paylaşılan [host.js](functions-host-json.md) dosyası vardır. Her işlevde kendi kod dosyası (. js) ve bağlama yapılandırma dosyası (function.js) içeren bir klasör vardır. `function.json`Ana dizinin adı her zaman işlevinizin adıdır.

Işlevler çalışma zamanının [2. x sürümünde](functions-versions.md) gerekli olan bağlama uzantıları, `extensions.csproj` dosyasında, klasördeki gerçek kitaplık dosyalarıyla birlikte tanımlanmıştır `bin` . Yerel olarak geliştirme yaparken, [bağlama uzantılarını kaydetmeniz](./functions-bindings-register.md#extension-bundles)gerekir. Azure portal işlevler geliştirirken, bu kayıt sizin için yapılır.

## <a name="exporting-a-function"></a>Bir işlevi dışarı aktarma

JavaScript işlevleri [`module.exports`](https://nodejs.org/api/modules.html#modules_module_exports) (veya) aracılığıyla verilmelidir [`exports`](https://nodejs.org/api/modules.html#modules_exports) . İçe aktarılmış işleviniz tetiklendiğinde yürütülen bir JavaScript işlevi olmalıdır.

Varsayılan olarak, Işlevler çalışma zamanı ' de işlevinize bakar `index.js` ve `index.js` buna karşılık olarak aynı üst dizini paylaşır `function.json` . Varsayılan durumda, dışarı aktarılmış işleviniz, dosyasından dışarı aktarma veya adlı dışarı aktarma olmalıdır `run` `index` . İşlevinizin dosya konumunu ve dışarı aktarma adını yapılandırmak için, aşağıdaki [işlevinizin giriş noktasını yapılandırma](functions-reference-node.md#configure-function-entry-point) konusunu okuyun.

Verilen işleviniz, yürütme sırasında bir dizi bağımsız değişken geçirdi. Aldığı ilk bağımsız değişken her zaman bir `context` nesnedir. İşleviniz zaman uyumludur (Promise döndürmez), `context` `context.done` doğru kullanım için çağırma gerekli olduğundan nesneyi geçirmeniz gerekir.

```javascript
// You should include context, other arguments are optional
module.exports = function(context, myTrigger, myInput, myOtherInput) {
    // function logic goes here :)
    context.done();
};
```

### <a name="exporting-an-async-function"></a>Zaman uyumsuz bir işlevi dışarı aktarma
[`async function`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function)İşlev çalışma zamanının 2. x ' de bildirimi veya düz JavaScript ['i kullanırken](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) , [`context.done`](#contextdone-method) işlevinizin tamamlandığını işaret etmek için geri çağırma işlemini açıkça çağırmanız gerekmez. Verdiğiniz zaman uyumsuz işlev/Promise tamamlandığında işleviniz tamamlanır. Sürüm 1. x çalışma zamanını hedefleyen işlevler için, kodunuzun yürütülmesi bittiğinde çağrı devam etmeniz gerekir [`context.done`](#contextdone-method) .

Aşağıdaki örnek, tetiklendiğini günlüğe kaydeden ve yürütmeyi hemen tamamlayan basit bir işlevdir.

```javascript
module.exports = async function (context) {
    context.log('JavaScript trigger function processed a request.');
};
```

Zaman uyumsuz bir işlevi dışarı aktarırken, bir çıkış bağlamayı de değeri alacak şekilde yapılandırabilirsiniz `return` . Yalnızca bir çıkış bağlaması varsa bu önerilir.

Kullanarak bir çıktı atamak için `return` `name` özelliğini içinde olarak değiştirin `$return` `function.json` .

```json
{
  "type": "http",
  "direction": "out",
  "name": "$return"
}
```

Bu durumda, işleviniz aşağıdaki örnekteki gibi görünmelidir:

```javascript
module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');
    // You can call and await an async method here
    return {
        body: "Hello, world!"
    };
}
```

## <a name="bindings"></a>Bağlamalar 
JavaScript 'te [bağlamalar](functions-triggers-bindings.md) , üzerinde bir işlevin function.jsyapılandırılır ve tanımlanır. İşlevler, bağlamalarla çeşitli yollarla etkileşime geçin.

### <a name="inputs"></a>Girişler
Giriş, Azure Işlevlerinde iki kategoriye ayrılmıştır: biri tetikleyici girişi, diğeri ise ek giriştir. Tetikleyici ve diğer giriş bağlamaları (bağlamaları `direction === "in"` ), bir işlev tarafından üç şekilde okunabilir:
 - **_[Önerilir]_ İşlevlerinizi parametre olarak geçirdi.** Bunlar, *üzerindefunction.js* tanımlandıkları sırada işleve geçirilir. `name` *function.jsüzerinde* tanımlanan özelliğin, parametresinin adı ile eşleşmesi gerekir, ancak.
 
   ```javascript
   module.exports = async function(context, myTrigger, myInput, myOtherInput) { ... };
   ```
   
 - **Nesnesinin üyeleri olarak [`context.bindings`](#contextbindings-property) .** Her üye, `name` *function.jsüzerinde* tanımlanan özellik tarafından adlandırılır.
 
   ```javascript
   module.exports = async function(context) { 
       context.log("This is myTrigger: " + context.bindings.myTrigger);
       context.log("This is myInput: " + context.bindings.myInput);
       context.log("This is myOtherInput: " + context.bindings.myOtherInput);
   };
   ```
   
 - **JavaScript nesnesini kullanan girişler [`arguments`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/arguments) .** Bu aslında girişleri parametre olarak geçirmesiyle aynıdır, ancak girişleri dinamik olarak işlemeniz sağlanır.
 
   ```javascript
   module.exports = async function(context) { 
       context.log("This is myTrigger: " + arguments[1]);
       context.log("This is myInput: " + arguments[2]);
       context.log("This is myOtherInput: " + arguments[3]);
   };
   ```

### <a name="outputs"></a>Çıkışlar
Çıktılar (bağlamalar), bir `direction === "out"` dizi şekilde bir işlev tarafından yazılabilir. Her durumda, `name` *function.jsüzerinde* tanımlanan bağlamanın özelliği, işlevinizde yazılan nesne üyesinin adına karşılık gelir. 

Çıkış bağlamalarına aşağıdaki yöntemlerden birini uygulayarak veri atayabilirsiniz (Bu yöntemleri birleştirmeyin):

- **_[Birden çok çıkış Için önerilir]_ Bir nesne döndürülüyor.** Zaman uyumsuz/Promise döndüren bir işlev kullanıyorsanız, atanmış çıkış verileri olan bir nesne döndürebilirsiniz. Aşağıdaki örnekte, çıkış bağlamaları *üzerindefunction.js*"HttpResponse" ve "queueoutput" olarak adlandırılmıştır.

  ```javascript
  module.exports = async function(context) {
      let retMsg = 'Hello, world!';
      return {
          httpResponse: {
              body: retMsg
          },
          queueOutput: retMsg
      };
  };
  ```

  Zaman uyumlu bir işlev kullanıyorsanız, bu nesneyi kullanarak döndürebilirsiniz [`context.done`](#contextdone-method) (bkz. örnek).
- **_[Tek çıkış Için önerilir]_ Doğrudan bir değer döndürme ve $return bağlama adı kullanma.** Bu yalnızca zaman uyumsuz/Promise döndüren işlevler için geçerlidir. [Zaman uyumsuz bir işlevi dışarı aktarırken](#exporting-an-async-function)örneğe bakın. 
- **Değerler `context.bindings` atama** Değerleri doğrudan Context. Bindings öğesine atayabilirsiniz.

  ```javascript
  module.exports = async function(context) {
      let retMsg = 'Hello, world!';
      context.bindings.httpResponse = {
          body: retMsg
      };
      context.bindings.queueOutput = retMsg;
      return;
  };
  ```

### <a name="bindings-data-type"></a>Bağlamalar veri türü

Bir giriş bağlamasının veri türünü tanımlamak için `dataType` bağlama tanımındaki özelliğini kullanın. Örneğin, bir HTTP isteğinin içeriğini ikili biçimde okumak için, şunu kullanın `binary` :

```json
{
    "type": "httpTrigger",
    "name": "req",
    "direction": "in",
    "dataType": "binary"
}
```

Seçenekleri `dataType` şunlardır: `binary` , `stream` , ve `string` .

## <a name="context-object"></a>bağlam nesnesi

Çalışma zamanı, `context` işlevinizden ve çalışma zamanından verileri iletmek için bir nesnesi kullanır. Bağlamaları okumak ve bağlamalardan veri yazmak ve günlüklere yazmak için kullanılır `context` . nesne her zaman bir işleve geçirilen ilk parametredir.

Zaman uyumlu kod kullanan işlevler için bağlam nesnesi, `done` işlev işlemeyi tamamladıktan sonra çağrınızı çağıran geri çağırma işlemini içerir. `done`Zaman uyumsuz kod yazılırken açıkça çağırma gereksizdir; `done` geri çağırma örtük olarak çağrılır.

```javascript
module.exports = (context) => {

    // function logic goes here

    context.log("The function has executed.");

    context.done();
};
```

İşlevinize geçirilen bağlam, `executionContext` aşağıdaki özelliklere sahip bir nesne olan bir özelliği gösterir:

| Özellik adı  | Tür  | Description |
|---------|---------|---------|
| `invocationId` | Dize | Belirli işlev çağrısı için benzersiz bir tanımlayıcı sağlar. |
| `functionName` | Dize | Çalışan işlevin adını sağlar |
| `functionDirectory` | Dize | Uygulama dizini işlevlerini sağlar. |

Aşağıdaki örnek, nasıl dönegösterdiğini gösterir `invocationId` .

```javascript
module.exports = (context, req) => {
    context.res = {
        body: context.executionContext.invocationId
    };
    context.done();
};
```

### <a name="contextbindings-property"></a>Context. Bindings özelliği

```js
context.bindings
```

Bağlama verilerini okumak veya atamak için kullanılan adlandırılmış bir nesne döndürür. Giriş ve tetikleyici bağlama verilerine, üzerindeki Özellikler okunarak erişilebilir `context.bindings` . Çıkış bağlama verileri, öğesine veri eklenerek atanabilir `context.bindings`

Örneğin, function.js' deki aşağıdaki bağlama tanımları, ' dan bir kuyruğun içeriğine erişmenize `context.bindings.myInput` ve kullanarak bir kuyruğa çıktılar atamanıza izin verir `context.bindings.myOutput` .

```json
{
    "type":"queue",
    "direction":"in",
    "name":"myInput"
    ...
},
{
    "type":"queue",
    "direction":"out",
    "name":"myOutput"
    ...
}
```

```javascript
// myInput contains the input data, which may have properties such as "name"
var author = context.bindings.myInput.name;
// Similarly, you can set your output data
context.bindings.myOutput = { 
        some_text: 'hello world', 
        a_number: 1 };
```

Nesne yerine yöntemi kullanarak çıkış bağlama verilerini tanımlamanızı seçebilirsiniz `context.done` `context.binding` (aşağıya bakın).

### <a name="contextbindingdata-property"></a>Context. bindingData özelliği

```js
context.bindingData
```

Tetikleyici meta verilerini ve işlev çağırma verilerini ( `invocationId` ,, `sys.methodName` `sys.utcNow` ,) içeren adlandırılmış bir nesne döndürür `sys.randGuid` . Tetikleyici meta verileri örneği için bkz. bu [Olay Hub 'ları örneği](functions-bindings-event-hubs-trigger.md).

### <a name="contextdone-method"></a>Context. Done yöntemi

```js
context.done([err],[propertyBag])
```

Çalışma zamanının kodunuzun tamamlandığını bilmesini sağlar. İşleviniz [`async function`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) bildirimi kullandığında, kullanmanız gerekmez `context.done()` . `context.done`Geri çağırma örtük olarak çağırılır. Zaman uyumsuz işlevler, Node 8 veya daha sonraki bir sürümde kullanılabilir ve bu Işlevler çalışma zamanının 2. x sürümünü gerektirir.

İşleviniz zaman uyumsuz bir işlev değilse,  `context.done` çalışma zamanını işlevinizin tamamlandığını bilgilendirmek için öğesini çağırmanız gerekir. Eksik ise yürütme zaman aşımına uğrar.

`context.done`Yöntemi, hem Kullanıcı tanımlı bir hatayı çalışma zamanına hem de çıkış bağlama verilerini içeren BIR JSON nesnesine geri geçirmenize olanak sağlar. `context.done`Nesne üzerinde ayarlanan her şeyi üzerine yazmak için geçirilen Özellikler `context.bindings` .

```javascript
// Even though we set myOutput to have:
//  -> text: 'hello world', number: 123
context.bindings.myOutput = { text: 'hello world', number: 123 };
// If we pass an object to the done function...
context.done(null, { myOutput: { text: 'hello there, world', noNumber: true }});
// the done method overwrites the myOutput binding to be: 
//  -> text: 'hello there, world', noNumber: true
```

### <a name="contextlog-method"></a>Context. log yöntemi  

```js
context.log(message)
```

Akış işlev günlüklerine, diğer günlük düzeyleri kullanılabilir olan varsayılan izleme düzeyinde yazmanızı sağlar. İzleme günlüğü, sonraki bölümde ayrıntılı olarak açıklanmıştır. 

## <a name="write-trace-output-to-logs"></a>İzleme çıkışını günlüklere yaz

Işlevlerde, `context.log` günlüklere ve konsola izleme çıktısı yazmak için yöntemlerini kullanırsınız. `context.log()`' İ çağırdığınızda, iletiniz, _bilgi_ izleme düzeyi olan varsayılan izleme düzeyinde günlüklere yazılır. İşlevler, işlev uygulama günlüklerinizi daha iyi yakalamak için Azure Application Insights ile tümleşir. Azure Izleyici 'nin bir parçası olan Application Insights, hem uygulama telemetrinin hem de izleme çıktılarınızın toplanması, görsel işleme ve analizine yönelik tesisler sağlar. Daha fazla bilgi için bkz. [Azure işlevlerini izleme](functions-monitoring.md).

Aşağıdaki örnek, çağrı KIMLIĞI de dahil olmak üzere bilgi izleme düzeyinde bir günlük Yazar:

```javascript
context.log("Something has happened. " + context.invocationId); 
```

Tüm `context.log` yöntemler Node.js [util. Format yöntemi](https://nodejs.org/api/util.html#util_util_format_format)tarafından desteklenen aynı parametre biçimini destekler. Varsayılan izleme düzeyini kullanarak işlev günlüklerini yazan aşağıdaki kodu göz önünde bulundurun:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=' + req.originalUrl);
context.log('Request Headers = ' + JSON.stringify(req.headers));
```

Aynı kodu aşağıdaki biçimde de yazabilirsiniz:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);
context.log('Request Headers = ', JSON.stringify(req.headers));
```

> [!NOTE]  
> `console.log`İzleme çıktıları yazmak için kullanmayın. Çıkış from `console.log` işlevi uygulama düzeyinde yakalandığından, belirli bir işlev çağrısına bağlı değildir ve belirli bir işlevin günlüklerinde gösterilmez. Ayrıca, Işlevler çalışma zamanının sürüm 1. x, `console.log` konsola yazmak için kullanmayı desteklemez.

### <a name="trace-levels"></a>İzleme düzeyleri

Varsayılan düzeyin yanı sıra, belirli izleme düzeylerinde işlev günlükleri yazmanıza olanak sağlayan aşağıdaki günlük yöntemleri kullanılabilir.

| Yöntem                 | Açıklama                                |
| ---------------------- | ------------------------------------------ |
| **hata (_ileti_)**   | Günlüklere hata düzeyi bir olay yazar.   |
| **uyar (_ileti_)**    | Günlüklere uyarı düzeyi bir olay yazar. |
| **bilgi (_ileti_)**    | Bilgi düzeyinde günlüğe kaydetme veya daha düşük bir yazma.    |
| **ayrıntılı (_ileti_)** | Ayrıntılı düzey günlüğe kaydetmeye yazar.           |

Aşağıdaki örnek, bilgi düzeyi yerine uyarı izleme düzeyinde aynı günlüğü Yazar:

```javascript
context.log.warn("Something has happened. " + context.invocationId); 
```

_Hata_ en yüksek izleme düzeyi olduğundan, bu izleme, günlük kaydı etkin olduğu sürece tüm izleme düzeylerinde çıktıya yazılır.

### <a name="configure-the-trace-level-for-logging"></a>Günlük kaydı için izleme düzeyini yapılandırma

İşlevler, günlüklere veya konsola yazmak için eşik izleme düzeyini tanımlamanızı sağlar. Belirli eşik ayarları, Işlevler çalışma zamanının sürümüne bağlıdır.

# <a name="v2x"></a>[v2. x +](#tab/v2)

Günlüklere yazılan izlemelerin eşiğini ayarlamak için `logging.logLevel` dosyadaki host.jsözelliğini kullanın. Bu JSON nesnesi, işlev uygulamanızdaki tüm işlevler için varsayılan bir eşik tanımlamanızı sağlar ve ayrıca ayrı işlevler için özel eşikler tanımlayabilirsiniz. Daha fazla bilgi için bkz. [Azure işlevleri için izlemeyi yapılandırma](configure-monitoring.md).

# <a name="v1x"></a>[v1. x](#tab/v1)

Günlüklere ve konsola yazılan tüm izlemelerin eşiğini ayarlamak için `tracing.consoleLevel` dosyadaki host.jsözelliğini kullanın. Bu ayar, işlev uygulamanızdaki tüm işlevler için geçerlidir. Aşağıdaki örnek, ayrıntılı günlük kaydını etkinleştirmek için izleme eşiğini ayarlar:

```json
{
    "tracing": {
        "consoleLevel": "verbose"
    }
}  
```

**Consolelevel** değerleri, yöntemlerin adlarına karşılık gelir `context.log` . Konsola tüm izleme günlüğünü devre dışı bırakmak için **Consolelevel** ' ı _off_ olarak ayarlayın. Daha fazla bilgi için, bkz [ . v1. x başvurusuhost.js](functions-host-json-v1.md).

---

### <a name="log-custom-telemetry"></a>Özel telemetri günlüğe kaydet

Varsayılan olarak, Işlevler çıktıyı izleme olarak Application Insights yazar. Daha fazla denetim için, Application Insights örneğinize özel telemetri verileri göndermek için [Application Insights Node.js SDK 'sını](https://github.com/microsoft/applicationinsights-node.js) kullanabilirsiniz. 

# <a name="v2x"></a>[v2. x +](#tab/v2)

```javascript
const appInsights = require("applicationinsights");
appInsights.setup();
const client = appInsights.defaultClient;

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    // Use this with 'tagOverrides' to correlate custom telemetry to the parent function invocation.
    var operationIdOverride = {"ai.operation.id":context.traceContext.traceparent};

    client.trackEvent({name: "my custom event", tagOverrides:operationIdOverride, properties: {customProperty2: "custom property value"}});
    client.trackException({exception: new Error("handled exceptions can be logged with this method"), tagOverrides:operationIdOverride});
    client.trackMetric({name: "custom metric", value: 3, tagOverrides:operationIdOverride});
    client.trackTrace({message: "trace message", tagOverrides:operationIdOverride});
    client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:231, resultCode:0, success: true, dependencyTypeName: "ZSQL", tagOverrides:operationIdOverride});
    client.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true, tagOverrides:operationIdOverride});

    context.done();
};
```

# <a name="v1x"></a>[v1. x](#tab/v1)

```javascript
const appInsights = require("applicationinsights");
appInsights.setup();
const client = appInsights.defaultClient;

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    // Use this with 'tagOverrides' to correlate custom telemetry to the parent function invocation.
    var operationIdOverride = {"ai.operation.id":context.operationId};

    client.trackEvent({name: "my custom event", tagOverrides:operationIdOverride, properties: {customProperty2: "custom property value"}});
    client.trackException({exception: new Error("handled exceptions can be logged with this method"), tagOverrides:operationIdOverride});
    client.trackMetric({name: "custom metric", value: 3, tagOverrides:operationIdOverride});
    client.trackTrace({message: "trace message", tagOverrides:operationIdOverride});
    client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:231, resultCode:0, success: true, dependencyTypeName: "ZSQL", tagOverrides:operationIdOverride});
    client.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true, tagOverrides:operationIdOverride});

    context.done();
};
```

---

`tagOverrides`Parametresi, öğesini `operation_Id` IŞLEVIN çağırma kimliğine ayarlar. Bu ayar, belirli bir işlev çağrısı için otomatik olarak oluşturulan ve özel telemetrinin tümünü ilişkilendirmenizi sağlar.

## <a name="http-triggers-and-bindings"></a>HTTP Tetikleyicileri ve bağlamaları

Http ve Web kancası Tetikleyicileri ve HTTP çıkış bağlamaları, HTTP iletilerini temsil etmek için istek ve yanıt nesnelerini kullanır.  

### <a name="request-object"></a>İstek nesnesi

`context.req`(İstek) nesnesi aşağıdaki özelliklere sahiptir:

| Özellik      | Açıklama                                                    |
| ------------- | -------------------------------------------------------------- |
| _bölümü_        | İsteğin gövdesini içeren bir nesne.               |
| _bilgisinde_     | İstek üst bilgilerini içeren bir nesne.                   |
| _yöntemidir_      | İsteğin HTTP yöntemi.                                |
| _originalUrl 'Si_ | İsteğin URL 'SI.                                        |
| _params_      | İsteğin yönlendirme parametrelerini içeren nesne. |
| _sorgulayamadı_       | Sorgu parametrelerini içeren bir nesne.                  |
| _rawBody_     | İleti gövdesi dize olarak.                           |


### <a name="response-object"></a>Yanıt nesnesi

`context.res`(Response) nesnesi aşağıdaki özelliklere sahiptir:

| Özellik  | Açıklama                                               |
| --------- | --------------------------------------------------------- |
| _bölümü_    | Yanıtın gövdesini içeren bir nesne.         |
| _bilgisinde_ | Yanıt üst bilgilerini içeren bir nesne.             |
| _isRaw_   | Yanıt için biçimlendirmenin atlandığını gösterir.    |
| _durumlarına_  | Yanıtın HTTP durum kodu.                     |
| _özgü_ | Yanıtta ayarlanan HTTP tanımlama bilgisi nesneleri dizisi. HTTP tanımlama bilgisi nesnesi,, `name` `value` ve gibi diğer tanımlama bilgisi özelliklerine sahiptir `maxAge` `sameSite` . |

### <a name="accessing-the-request-and-response"></a>İstek ve yanıta erişme 

HTTP tetikleyicilerle çalışırken, HTTP isteğine ve yanıt nesnelerine çeşitli yollarla erişebilirsiniz:

+ **`req`Nesnesinden ve `res` özellikleri `context` .** Bu şekilde, tam kalıbı kullanmak yerine bağlam nesnesinden HTTP verilerine erişmek için geleneksel bir stili kullanabilirsiniz `context.bindings.name` . Aşağıdaki örnek, `req` ve üzerindeki nesnelerine nasıl erişegösterdiğini göstermektedir `res` `context` :

    ```javascript
    // You can access your HTTP request off the context ...
    if(context.req.body.emoji === ':pizza:') context.log('Yay!');
    // and also set your HTTP response
    context.res = { status: 202, body: 'You successfully ordered more coffee!' }; 
    ```

+ **Adlandırılmış giriş ve çıkış bağlamalarından.** Bu şekilde, HTTP tetikleyicisi ve bağlamaları diğer bağlamalarla aynı şekilde çalışır. Aşağıdaki örnek, bir adlandırılmış bağlama kullanarak yanıt nesnesini ayarlar `response` : 

    ```json
    {
        "type": "http",
        "direction": "out",
        "name": "response"
    }
    ```
    ```javascript
    context.bindings.response = { status: 201, body: "Insert succeeded." };
    ```
+ **_[Yalnızca yanıt]_ Çağırarak `context.res.send(body?: any)` .** Yanıt gövdesi olarak giriş ile bir HTTP yanıtı oluşturulur `body` . `context.done()` örtük olarak çağırılır.

+ **_[Yalnızca yanıt]_ Çağırarak `context.done()` .** Özel bir HTTP bağlama türü yöntemine geçirilen yanıtı döndürür `context.done()` . Aşağıdaki HTTP çıkış bağlaması bir `$return` Çıkış parametresini tanımlar:

    ```json
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
    ``` 
    ```javascript
     // Define a valid response object.
    res = { status: 201, body: "Insert succeeded." };
    context.done(null, res);   
    ```  

## <a name="scaling-and-concurrency"></a>Ölçeklendirme ve eşzamanlılık

Varsayılan olarak, Azure Işlevleri uygulamanızdaki yükü otomatik olarak izler ve gerektiğinde Node.js için ek konak örnekleri oluşturur. İşlevler, iletilerin yaşı ve QueueTrigger için sıra boyutu gibi örneklerin ne zaman ekleneceğini belirlemek için farklı tetikleyici türleri için yerleşik (Kullanıcı tarafından yapılandırılamaz) eşikleri kullanır. Daha fazla bilgi için bkz. [Tüketim ve Premium planların nasıl çalıştığı](event-driven-scaling.md).

Bu ölçeklendirme davranışı birçok Node.js uygulama için yeterlidir. CPU 'ya yönelik uygulamalar için, birden çok dil çalışan işlemini kullanarak performansı daha da artırabilirsiniz.

Varsayılan olarak, her Işlev ana bilgisayar örneği tek bir dil çalışan işlemine sahiptir. [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count) uygulama ayarını kullanarak konak başına çalışan işlem sayısını (10 ' a kadar) artırabilirsiniz. Azure Işlevleri daha sonra bu çalışanlar genelinde aynı anda eşzamanlı işlev etkinleştirmeleri dağıtmaya çalışır. 

FUNCTIONS_WORKER_PROCESS_COUNT, uygulamanızın talebi karşılamak üzere ölçeklenmesi sırasında oluşturduğu her bir konak için geçerlidir. 

## <a name="node-version"></a>Düğüm sürümü

Aşağıdaki tabloda, işletim sistemine göre Işlevler çalışma zamanının her ana sürümü için desteklenen geçerli Node.js sürümleri gösterilmektedir:

| İşlevler sürümü | Düğüm sürümü (Windows) | Düğüm sürümü (Linux) |
|---|---| --- |
| 3. x (önerilir) | `~14` Önerilen<br/>`~12`<br/>`~10` | `node|14` Önerilen<br/>`node|12`<br/>`node|10` |
| 2.x  | `~12`<br/>`~10`<br/>`~8` | `node|10`<br/>`node|8`  |
| 'in | 6.11.2 (çalışma zamanı tarafından kilitlendi) | yok |

Çalışma zamanının herhangi bir işlevden günlüğe kaydederek kullandığı geçerli sürümü görebilirsiniz `process.version` .

### <a name="setting-the-node-version"></a>Düğüm sürümü ayarlanıyor

Windows işlev uygulamaları için `WEBSITE_NODE_DEFAULT_VERSION` [uygulama ayarını](functions-how-to-use-azure-function-app-settings.md#settings) desteklenen bir LTS sürümüne ayarlayarak Azure 'daki sürümü hedefleyin `~14` .

Linux işlev uygulamaları için, düğüm sürümünü güncelleştirmek için aşağıdaki Azure CLı komutunu çalıştırın.

```bash
az functionapp config set --linux-fx-version "node|14" --name "<MY_APP_NAME>" --resource-group "<MY_RESOURCE_GROUP_NAME>"
```

## <a name="dependency-management"></a>Bağımlılık yönetimi
Aşağıdaki örnekte gösterildiği gibi, JavaScript kodunuzda topluluk kitaplıklarını kullanmak için tüm bağımlılıkların Azure 'daki İşlev Uygulaması yüklendiğinden emin olmanız gerekir.

```javascript
// Import the underscore.js library
var _ = require('underscore');
var version = process.version; // version === 'v6.5.0'

module.exports = function(context) {
    // Using our imported underscore.js library
    var matched_names = _
        .where(context.bindings.myInput.names, {first: 'Carla'});
```

> [!NOTE]
> `package.json`İşlev uygulaması kökünde bir dosya tanımlamalısınız. Dosyayı tanımlama, uygulamadaki tüm işlevlerin aynı önbelleğe alınmış paketleri paylaşmasını sağlar ve bu da en iyi performansı verir. Bir sürüm çakışması oluşursa, belirli bir işlevin klasörüne bir dosya ekleyerek bu sorunu çözebilirsiniz `package.json` .  

Kaynak denetiminden Işlev uygulamaları dağıttığınızda, deponuzda bulunan herhangi bir `package.json` Dosya, `npm install` dağıtım sırasında kendi klasöründe tetiklenir. Ancak portal veya CLı aracılığıyla dağıtım yaparken paketleri el ile kurmanız gerekir.

İşlev Uygulaması paketleri yüklemek için iki yol vardır: 

### <a name="deploying-with-dependencies"></a>Bağımlılıklarla dağıtma
1. ' İ çalıştırarak tüm önkoşul paketlerini yerel olarak yükler `npm install` .

2. Kodunuzu dağıtın ve `node_modules` klasörün dağıtıma eklendiğinden emin olun. 


### <a name="using-kudu"></a>Kudu kullanma
1. `https://<function_app_name>.scm.azurewebsites.net` öğesine gidin.

2. **Hata ayıklama konsolu**  >  **cmd**' ye tıklayın.

3. `D:\home\site\wwwroot`' A gidin ve ardından package.jsdosyayı sayfanın üst yarısında **Wwwroot** klasörüne sürükleyin.  
    Dosyaları işlev uygulamanıza başka yollarla da yükleyebilirsiniz. Daha fazla bilgi için bkz. [işlev uygulama dosyalarını güncelleştirme](functions-reference.md#fileupdate). 

4. Dosyadaki package.jskarşıya yüklendikten sonra, `npm install` **kudu uzaktan yürütme konsolunda** komutunu çalıştırın.  
    Bu eylem, dosyadaki package.jsbelirtilen paketleri indirir ve işlev uygulamasını yeniden başlatır.

## <a name="environment-variables"></a>Ortam değişkenleri

Hem yerel hem de bulut ortamlarınızdaki işlemsel gizlilikler (bağlantı dizeleri, anahtarlar ve uç noktalar) veya ortam ayarları (örneğin profil oluşturma değişkenleri) gibi bir işlev uygulamasına kendi ortam değişkenlerinizi ekleyin. İşlev kodunuzda kullanarak bu ayarlara erişin `process.env` .

### <a name="in-local-development-environment"></a>Yerel geliştirme ortamında

Yerel olarak çalışırken, işlevleriniz, ortam değişkenlerinizi nesnesinde depoladığınız bir [ `local.settings.json` Dosya](./functions-run-local.md)içerir `Values` . 

```json
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "FUNCTIONS_WORKER_RUNTIME": "node",
    "translatorTextEndPoint": "https://api.cognitive.microsofttranslator.com/",
    "translatorTextKey": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
    "languageWorkers__node__arguments": "--prof"
  }
}
```

### <a name="in-azure-cloud-environment"></a>Azure bulut ortamında

İşlev uygulaması, Azure 'da çalışırken, hizmet bağlantı dizeleri gibi [uygulama ayarlarını](functions-app-settings.md)kullanarak ayarlamanıza ve yürütme sırasında bu ayarları ortam değişkenleri olarak kullanıma sunar. 

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

### <a name="access-environment-variables-in-code"></a>Koddaki ortam değişkenlerine erişin

Uygulama ayarlarına, burada gösterildiği gibi ortam değişkenleri olarak erişin ve bu `process.env` `context.log()` , `AzureWebJobsStorage` ve ortam değişkenlerini günlüğe verdiğimiz üçüncü çağrılarsa `WEBSITE_SITE_NAME` :

```javascript
module.exports = async function (context, myTimer) {

    context.log("AzureWebJobsStorage: " + process.env["AzureWebJobsStorage"]);
    context.log("WEBSITE_SITE_NAME: " + process.env["WEBSITE_SITE_NAME"]);
};
```

## <a name="ecmascript-modules-preview"></a><a name="ecmascript-modules"></a>ECMAScript modülleri (Önizleme)

> [!NOTE]
> ECMAScript modülleri Şu anda Node.js 14 ' te *deneysel* etiketli olduğundan, Node.js 14 Azure işlevlerinde önizleme özelliği olarak kullanılabilir. ECMAScript modülleri için Node.js 14 desteği *kararlı* hale gelene kadar, API veya davranışında olası değişiklikler beklenir.

[ECMAScript modülleri](https://nodejs.org/docs/latest-v14.x/api/esm.html#esm_modules_ecmascript_modules) (es modülleri) Node.js için yeni resmi standart modül sistemidir. Şimdiye kadar, bu makaledeki kod örnekleri CommonJS sözdizimini kullanır. Azure Işlevlerini Node.js 14 ' te çalıştırırken, işlevlerinizi ES modülleri sözdizimini kullanarak yazmayı seçebilirsiniz.

Bir işlevde ES modüllerini kullanmak için dosya adını uzantı kullanacak şekilde değiştirin `.mjs` . Aşağıdaki *index. MJS* dosyası örneği, kitaplığı içeri aktarıp bir değer döndürecek şekilde es modülleri sözdizimini kullanan bir http tetiklenen işlevdir `uuid` .

```js
import { v4 as uuidv4 } from 'uuid';

export default async function (context, req) {
    context.res.body = uuidv4();
};
```

## <a name="configure-function-entry-point"></a>İşlev giriş noktasını yapılandır

`function.json`Özellikler `scriptFile` ve, `entryPoint` verdiğiniz işlevin konumunu ve adını yapılandırmak için kullanılabilir. JavaScript transpiled olduğunda bu özellikler önemli olabilir.

### <a name="using-scriptfile"></a>`scriptFile` kullanma

Varsayılan olarak, `index.js` karşılık gelen bir üst dizini paylaşan bir dosya olan öğesinden bir JavaScript işlevi yürütülür `function.json` .

`scriptFile` Aşağıdaki örnekteki gibi görünen bir klasör yapısını almak için kullanılabilir:

```
FunctionApp
 | - host.json
 | - myNodeFunction
 | | - function.json
 | - lib
 | | - sayHello.js
 | - node_modules
 | | - ... packages ...
 | - package.json
```

`function.json`İçin `myNodeFunction` öğesine, `scriptFile` çalıştırılacak işlevi çalıştıran dosyaya işaret eden bir özelliği içermelidir.

```json
{
  "scriptFile": "../lib/sayHello.js",
  "bindings": [
    ...
  ]
}
```

### <a name="using-entrypoint"></a>`entryPoint` kullanma

`scriptFile`(Veya `index.js` ) içinde, bir işlev, bulunacak `module.exports` ve çalıştırmak için kullanılarak verilmelidir. Varsayılan olarak, tetiklendiğinde yürütülen işlev bu dosyadan tek dışarı aktarma, adlı dışarı aktarma `run` veya adlı dışarı aktarma `index` .

Bu `entryPoint` `function.json` , aşağıdaki örnekte olduğu gibi ' de kullanılarak yapılandırılabilir:

```json
{
  "entryPoint": "logFoo",
  "bindings": [
    ...
  ]
}
```

Kullanıcı işlevlerinde parametresini destekleyen v2. x Işlevleri içinde `this` , işlev kodu aşağıdaki örnekte olduğu gibi olabilir:

```javascript
class MyObj {
    constructor() {
        this.foo = 1;
    };

    logFoo(context) { 
        context.log("Foo is " + this.foo); 
        context.done(); 
    }
}

const myObj = new MyObj();
module.exports = myObj;
```

Bu örnekte, bir nesne verilse de yürütmeler arasında durumu korumak için herhangi bir garanti olmadığı unutulmamalıdır.

## <a name="local-debugging"></a>Yerel hata ayıklama

Parametresi ile birlikte başlatıldığında `--inspect` , Node.js işlemi belirtilen bağlantı noktasında hata ayıklama istemcisini dinler. Azure Işlevleri 2. x içinde, ortam değişkeni veya uygulama ayarı ekleyerek kodunuzu çalıştıran Node.js işlemine geçirilecek bağımsız değişkenleri belirtebilirsiniz `languageWorkers:node:arguments = <args>` . 

Yerel olarak hata ayıklamak için `"languageWorkers:node:arguments": "--inspect=5858"` , `Values` [local.settings.js](./functions-run-local.md#local-settings-file) dosyasına altına ekleyin ve 5858 numaralı bağlantı noktasına bir hata ayıklayıcı ekleyin.

VS Code kullanarak hata ayıklarken, `--inspect` parametresi `port` projenin launch.jsdosyadaki değeri kullanılarak otomatik olarak eklenir.

Sürüm 1. x içinde, ayar `languageWorkers:node:arguments` çalışmayacak. Hata ayıklama bağlantı noktası, [`--nodeDebugPort`](./functions-run-local.md#start) Azure Functions Core Tools parametresiyle seçilebilir.

## <a name="typescript"></a>TypeScript

Işlevler çalışma zamanının 2. x sürümünü hedeflediğinizde, hem Visual Studio Code hem de [Azure Functions Core Tools](functions-run-local.md) [için Azure işlevleri](./create-first-function-cli-typescript.md) , TypeScript işlev uygulaması projelerini destekleyen bir şablon kullanarak işlev uygulamaları oluşturmanıza olanak tanır. Şablon, `package.json` `tsconfig.json` Bu araçlarla derleyin, çalıştırmak ve TypeScript kodundan JavaScript işlevlerini yayımlamayı kolaylaştıran proje dosyaları oluşturur.

Oluşturulan bir `.funcignore` Dosya, bir proje Azure 'da yayımlandığında hangi dosyaların dışlandığını göstermek için kullanılır.  

TypeScript dosyaları (. TS), çıkış dizininde JavaScript dosyalarına (. js) transpiled `dist` . TypeScript şablonları, [ `scriptFile` ](#using-scriptfile) `function.json` klasöründe karşılık gelen. js dosyasının konumunu belirtmek için içindeki parametresini kullanır `dist` . Çıktı konumu, dosyadaki parametresi kullanılarak şablon tarafından ayarlanır `outDir` `tsconfig.json` . Bu ayarı veya klasörün adını değiştirirseniz, çalışma zamanı çalıştırılacak kodu bulamaz.

Bir TypeScript projesinden yerel olarak geliştirme ve dağıtma yöntemi, geliştirme aracınıza bağlıdır.

### <a name="visual-studio-code"></a>Visual Studio Code

[Visual Studio Code uzantısı Için Azure işlevleri](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) , Işlevlerinizi TypeScript kullanarak geliştirmenize olanak sağlar. Temel araçlar, Azure Işlevleri uzantısının bir gereksinimidir.

Visual Studio Code bir TypeScript işlev uygulaması oluşturmak için, `TypeScript` bir işlev uygulaması oluştururken diliniz olarak seçin.

Uygulamayı yerel olarak çalıştırmak için **F5** tuşuna bastığınızda, ana bilgisayar (func.exe) başlatılmadan önce transpilation yapılır. 

İşlev uygulamanızı Azure 'a dağıt **app...** düğmesini kullanarak uyguladığınızda, Azure işlevleri uzantısı Ilk olarak TypeScript kaynak dosyalarından bir JavaScript dosyaları üretimi için hazırlayın.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Bir TypeScript projesinin, temel araçları kullanırken bir JavaScript projesinden farklı olduğu çeşitli yollar vardır.

#### <a name="create-project"></a>Proje oluşturma

Çekirdek araçları kullanarak bir TypeScript işlev uygulaması projesi oluşturmak için, işlev uygulamanızı oluştururken TypeScript dil seçeneğini belirtmeniz gerekir. Bunu aşağıdaki yöntemlerle yapabilirsiniz:

- Komutunu çalıştırın `func init` , `node` dil yığınınızı olarak öğesini seçin ve ardından öğesini seçin `typescript` .

- `func init --worker-runtime typescript` komutunu çalıştırın.

#### <a name="run-local"></a>Yerel çalıştırma

İşlev uygulaması kodunuzu temel araçları kullanarak yerel olarak çalıştırmak için aşağıdaki komutları kullanın `func host start` : 

```command
npm install
npm start
```

`npm start`Komut aşağıdaki komutlarla eşdeğerdir:

- `npm run build`
- `func extensions install`
- `tsc`
- `func start`

#### <a name="publish-to-azure"></a>Azure’da Yayımlama

[`func azure functionapp publish`]Azure 'a dağıtmak için komutunu kullanmadan önce, TypeScript kaynak dosyalarından bir dizi JavaScript dosyası oluşturmaya yönelik bir yapılandırma oluşturacaksınız. 

Aşağıdaki komutlar, temel araçları kullanarak TypeScript projenizi hazırlar ve yayımlar: 

```command
npm run build:production 
func azure functionapp publish <APP_NAME>
```

Bu komutta, öğesini `<APP_NAME>` işlev uygulamanızın adıyla değiştirin.

## <a name="considerations-for-javascript-functions"></a>JavaScript işlevleriyle ilgili konular

JavaScript işlevleriyle çalışırken, aşağıdaki bölümlerde yer aldığını göz önünde bulundurun.

### <a name="choose-single-vcpu-app-service-plans"></a>Tek-vCPU App Service planlarını seçin

App Service planını kullanan bir işlev uygulaması oluşturduğunuzda, birden fazla vCPU içeren bir plan yerine tek bir vCPU planı seçmenizi öneririz. Günümüzde Işlevler, JavaScript işlevlerini tek-vCPU VM 'lerinde daha verimli bir şekilde çalıştırır ve daha büyük VM 'Lerin kullanılması beklenen performans geliştirmelerini oluşturmaz. Gerektiğinde, daha fazla çoklu-vCPU VM örneği ekleyerek ölçeği el ile ölçeklendirebilir veya otomatik ölçeklendirmeyi etkinleştirebilirsiniz. Daha fazla bilgi için bkz. [örnek sayısını el ile veya otomatik olarak ölçeklendirme](../azure-monitor/autoscale/autoscale-get-started.md?toc=/azure/app-service/toc.json).

### <a name="cold-start"></a>Soğuk başlangıç

Sunucusuz barındırma modelinde Azure Işlevleri geliştirirken soğuk başlar. *Soğuk başlatma* , işlev uygulamanız işlem yapılmayan bir süre sonra ilk kez başlatıldığında başlatılmak daha uzun sürer. Özellikle büyük bağımlılık ağaçları olan JavaScript işlevleri için soğuk başlatma önemli olabilir. Soğuk başlatma sürecini hızlandırmak için [işlevlerinizi mümkün olduğunda bir paket dosyası olarak çalıştırın](run-functions-from-deployment-package.md) . Birçok dağıtım yöntemi, varsayılan olarak paket modelinden Çalıştır ' ı kullanır, ancak büyük soğuk çalışmaya başladıysanız ve bu şekilde çalıştırılmadığında, bu değişiklik önemli bir geliştirme sağlayabilir.

### <a name="connection-limits"></a>Bağlantı sınırları

Azure Işlevleri uygulamasında hizmete özel bir istemci kullandığınızda, her işlev çağrısında yeni bir istemci oluşturmayın. Bunun yerine, genel kapsamda tek bir statik istemci oluşturun. Daha fazla bilgi için bkz. [Azure işlevlerinde bağlantıları yönetme](manage-connections.md).

### <a name="use-async-and-await"></a>`async`Ve kullanın`await`

JavaScript 'e Azure Işlevleri yazarken, `async` ve anahtar sözcüklerini kullanarak kod yazmalısınız `await` . `async` `await` Geri çağırmalar veya ve kullanarak kod yazmak `.then` , `.catch` iki yaygın sorunun önlenmesine yardımcı olur:
 - Büyük olasılıkla diğer işlevlerin yürütülmesini etkilediği [Node.js işlemini](https://nodejs.org/api/process.html#process_warning_using_uncaughtexception_correctly)engelleyen yakalanamayan özel durumlar üretiliyor.
 - Doğru şekilde beklememiş olmayan zaman uyumsuz çağrılar nedeniyle Context. log ' dan eksik Günlükler gibi beklenmeyen davranışlar.

Aşağıdaki örnekte, zaman uyumsuz yöntem `fs.readFile` ikinci parametresi olarak bir hata-geri çağırma işlevi ile çağırılır. Bu kod, yukarıda belirtilen sorunların her ikisine de neden olur. Doğru kapsamda açıkça yakalanmayan bir özel durum, işlemin tamamını (sorun #1) kilitlendi. `context.done()`Geri çağırma işlevinin kapsamının dışında çağırmak, işlev çağrısının dosya okunmadan önce bitebileceği anlamına gelir (sorun #2). Bu örnekte, `context.done()` ile başlayan eksik günlük girişlerinde çok erken sonuçlar çağırma `Data from file:` .

```javascript
// NOT RECOMMENDED PATTERN
const fs = require('fs');

module.exports = function (context) {
    fs.readFile('./hello.txt', (err, data) => {
        if (err) {
            context.log.error('ERROR', err);
            // BUG #1: This will result in an uncaught exception that crashes the entire process
            throw err;
        }
        context.log(`Data from file: ${data}`);
        // context.done() should be called here
    });
    // BUG #2: Data is not guaranteed to be read before the Azure Function's invocation ends
    context.done();
}
```

`async`Ve `await` anahtar sözcüklerinin kullanılması, bu hatalardan her ikisinin de önlenmesine yardımcı olur. [`util.promisify`](https://nodejs.org/api/util.html#util_util_promisify_original)Hata-ilk geri çağırma stili işlevlerini zaman uyumlu işlevlere dönüştürmek için Node.js yardımcı program işlevini kullanmanız gerekir.

Aşağıdaki örnekte, işlev yürütmesi sırasında oluşan işlenmemiş özel durumlar yalnızca bir özel durum oluşturan tek bir çağrıyı başarısız hale getirilir. `await`Anahtar sözcüğü, `readFileAsync` yalnızca yürütme sonrasında yürütülen adımların tamamlandığı anlamına gelir `readFile` . `async`Ve ile `await` `context.done()` geri aramayı çağırmanız gerekmez.

```javascript
// Recommended pattern
const fs = require('fs');
const util = require('util');
const readFileAsync = util.promisify(fs.readFile);

module.exports = async function (context) {
    let data;
    try {
        data = await readFileAsync('./hello.txt');
    } catch (err) {
        context.log.error('ERROR', err);
        // This rethrown exception will be handled by the Functions Runtime and will only fail the individual invocation
        throw err;
    }
    context.log(`Data from file: ${data}`);
}
```

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için aşağıdaki kaynaklara bakın:

+ [Azure İşlevleri için en iyi uygulamalar](functions-best-practices.md)
+ [Azure İşlevleri geliştirici başvurusu](functions-reference.md)
+ [Azure Işlevleri Tetikleyicileri ve bağlamaları](functions-triggers-bindings.md)

[' Func Azure functionapp Publish ']: functions-run-local.md#project-file-deployment
