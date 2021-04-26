---
title: Azure İşlevlerini test etme
description: Visual Studio 'da C# işlevi ve VS Code içindeki JavaScript Işlevinde otomatik testler oluşturma
author: craigshoemaker
ms.topic: conceptual
ms.custom: devx-track-csharp, devx-track-js
ms.date: 03/25/2019
ms.author: cshoe
ms.openlocfilehash: 27bc8171509e16f25c508dd71515d8cb25a11b0b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105639873"
---
# <a name="strategies-for-testing-your-code-in-azure-functions"></a>Azure İşlevleri'nde kodunuzu test etmeye yönelik stratejiler

Bu makalede, Azure Işlevleri için otomatikleştirilmiş testlerin nasıl oluşturulacağı gösterilmektedir.

Tüm kodun test edilmesi önerilir, ancak işlevin mantığını sarmalayarak ve Işlev dışında testler oluşturarak en iyi sonuçları elde edebilirsiniz. Soyutlama mantığı, Işlevin kod satırlarını kısıtlar ve Işlevin yalnızca diğer sınıfları veya modülleri çağırarak sorumlu olmasını sağlar. Ancak, bu makalede, HTTP ve Zamanlayıcı tarafından tetiklenen işlevlerde otomatikleştirilmiş testlerin nasıl oluşturulacağı gösterilmektedir.

Aşağıdaki içerik, farklı dilleri ve ortamları hedeflemek üzere iki farklı bölüme ayrılır. ' De test oluşturmayı öğrenebilirsiniz:

- [XUnit ile Visual Studio 'da C#](#c-in-visual-studio)
- [Jest ile VS Code JavaScript](#javascript-in-vs-code)

Örnek depo [GitHub](https://github.com/Azure-Samples/azure-functions-tests)' da kullanılabilir.

## <a name="c-in-visual-studio"></a>Visual Studio’da C#

Aşağıdaki örnek, Visual Studio 'da C# işlev uygulamasının nasıl oluşturulduğunu ve [xUnit](https://github.com/xunit/xunit)ile nasıl çalıştırılacağını ve test edileceğini açıklar.

![Visual Studio 'Da C# ile Azure Işlevlerini test etme](./media/functions-test-a-function/azure-functions-test-visual-studio-xunit.png)

### <a name="setup"></a>Kurulum

Ortamınızı ayarlamak için bir Işlev ve test uygulaması oluşturun. Aşağıdaki adımlar, testleri desteklemek için gereken uygulamaları ve işlevleri oluşturmanıza yardımcı olur:

1. [Yeni bir işlevler uygulaması oluşturun](./functions-get-started.md) ve **işlevleri** adlandırın
2. [Şablondan BIR http Işlevi oluşturun](./functions-get-started.md) ve **myhttptrigger** olarak adlandırın.
3. [Şablondan bir Zamanlayıcı Işlevi oluşturun](./functions-create-scheduled-function.md) ve **mytimertrigger** olarak adlandırın.
4. Çözümde [bir xUnit test uygulaması oluşturun](https://xunit.net/docs/getting-started/netcore/cmdline) ve bunu **Functions. Tests** olarak adlandırın.
5. Test uygulamasından [Microsoft. AspNetCore. Mvc](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc/) 'ye bir başvuru eklemek için NuGet kullanın
6. [](/visualstudio/ide/managing-references-in-a-project) Functions *. Tests* uygulamasındaki Functions uygulamasına başvurun.

### <a name="create-test-classes"></a>Test sınıfları oluşturma

Artık projeler oluşturuldığına göre otomatik testleri çalıştırmak için kullanılan sınıfları oluşturabilirsiniz.

Her işlev, ileti günlüğünü işlemek için bir [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger) örneğini alır. Bazı sınamalar iletileri günlüğe kaydetme veya günlük kaydı ile ilgili hiçbir sorun yoktur. Diğer testlerin, bir testin geçirilip geçirilmediğini belirlemede günlüğe kaydedilen iletileri değerlendirmesi gerekir.

`ListLogger`Bir test sırasında değerlendirmek için bir iç ileti listesini tutan adlı yeni bir sınıf oluşturacaksınız. Gerekli arabirimi uygulamak için `ILogger` , sınıfı bir kapsama ihtiyaç duyuyor. Aşağıdaki sınıf, sınıfa geçirilecek test çalışmalarının kapsamını gizler `ListLogger` .

Işlevlerde yeni bir sınıf oluşturun *.* **nullscope. cs** adlı test projesi ve aşağıdaki kodu girin:

```csharp
using System;

namespace Functions.Tests
{
    public class NullScope : IDisposable
    {
        public static NullScope Instance { get; } = new NullScope();

        private NullScope() { }

        public void Dispose() { }
    }
}
```

Ardından, Işlevlerde yeni bir sınıf oluşturun *.* **listgünlükçü. cs** adlı test projesi projesini sınar ve aşağıdaki kodu girin:

```csharp
using Microsoft.Extensions.Logging;
using System;
using System.Collections.Generic;
using System.Text;

namespace Functions.Tests
{
    public class ListLogger : ILogger
    {
        public IList<string> Logs;

        public IDisposable BeginScope<TState>(TState state) => NullScope.Instance;

        public bool IsEnabled(LogLevel logLevel) => false;

        public ListLogger()
        {
            this.Logs = new List<string>();
        }

        public void Log<TState>(LogLevel logLevel,
                                EventId eventId,
                                TState state,
                                Exception exception,
                                Func<TState, Exception, string> formatter)
        {
            string message = formatter(state, exception);
            this.Logs.Add(message);
        }
    }
}
```

`ListLogger`Sınıfı, arabirimi tarafından anlaşmalı olarak aşağıdaki üyeleri uygular `ILogger` :

- **BeginScope**: kapsamlar günlüğe kaydetme uygulamanıza bağlam ekler. Bu durumda, test, test işlevine izin vermek için yalnızca sınıftaki statik örneği işaret eder `NullScope` .

- **IsEnabled**: varsayılan değeri `false` sağlanır.

- **GNLK**: Bu yöntem, `formatter` iletiyi biçimlendirmek için sağlanan işlevi kullanır ve sonra elde edilen metni `Logs` koleksiyona ekler.

`Logs`Koleksiyonu bir örneğidir `List<string>` ve oluşturucuda başlatılır.

Sonra, Işlevlerde yeni bir dosya oluşturun *.* **loggertypes. cs** adlı test projesini sınar ve aşağıdaki kodu girin:

```csharp
namespace Functions.Tests
{
    public enum LoggerTypes
    {
        Null,
        List
    }
}
```

Bu numaralandırma, testler tarafından kullanılan günlükçü türünü belirtir.

Şimdi Işlevlerde yeni bir sınıf oluşturun *.* **TESTfactory. cs** adlı test projesini sınar ve aşağıdaki kodu girin:

```csharp
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Http.Internal;
using Microsoft.Extensions.Logging;
using Microsoft.Extensions.Logging.Abstractions;
using Microsoft.Extensions.Primitives;
using System.Collections.Generic;

namespace Functions.Tests
{
    public class TestFactory
    {
        public static IEnumerable<object[]> Data()
        {
            return new List<object[]>
            {
                new object[] { "name", "Bill" },
                new object[] { "name", "Paul" },
                new object[] { "name", "Steve" }

            };
        }

        private static Dictionary<string, StringValues> CreateDictionary(string key, string value)
        {
            var qs = new Dictionary<string, StringValues>
            {
                { key, value }
            };
            return qs;
        }

        public static HttpRequest CreateHttpRequest(string queryStringKey, string queryStringValue)
        {
            var context = new DefaultHttpContext();
            var request = context.Request;
            request.Query = new QueryCollection(CreateDictionary(queryStringKey, queryStringValue));
            return request;
        }

        public static ILogger CreateLogger(LoggerTypes type = LoggerTypes.Null)
        {
            ILogger logger;

            if (type == LoggerTypes.List)
            {
                logger = new ListLogger();
            }
            else
            {
                logger = NullLoggerFactory.Instance.CreateLogger("Null Logger");
            }

            return logger;
        }
    }
}
```

`TestFactory`Sınıfı aşağıdaki üyeleri uygular:

- **Veri**: Bu özellik, örnek verilerin bir [IEnumerable](/dotnet/api/system.collections.ienumerable) koleksiyonunu döndürür. Anahtar değer çiftleri bir sorgu dizesine geçirilen değerleri temsil eder.

- **CreateDictionary**: Bu yöntem, anahtar/değer çiftini bağımsız değişken olarak kabul eder ve `Dictionary` `QueryCollection` sorgu dizesi değerlerini temsil etmek için oluşturmak üzere yeni bir değer döndürür.

- **Createhttprequest**: Bu yöntem, belirtilen sorgu dizesi parametreleriyle BAŞLATıLAN bir http isteği oluşturur.

- **Creategünlükçü**: günlükçü türü temelinde, bu yöntem test için kullanılan bir günlükçü sınıfı döndürür. , `ListLogger` Testlerin değerlendirmesi için kullanılabilen günlüğe kaydedilen iletileri izler.

Son olarak, Işlevlerde yeni bir sınıf oluşturun. **Functionstests. cs** adlı *test projesini sınar* ve aşağıdaki kodu girin:

```csharp
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Logging;
using Xunit;

namespace Functions.Tests
{
    public class FunctionsTests
    {
        private readonly ILogger logger = TestFactory.CreateLogger();

        [Fact]
        public async void Http_trigger_should_return_known_string()
        {
            var request = TestFactory.CreateHttpRequest("name", "Bill");
            var response = (OkObjectResult)await MyHttpTrigger.Run(request, logger);
            Assert.Equal("Hello, Bill. This HTTP triggered function executed successfully.", response.Value);
        }

        [Theory]
        [MemberData(nameof(TestFactory.Data), MemberType = typeof(TestFactory))]
        public async void Http_trigger_should_return_known_string_from_member_data(string queryStringKey, string queryStringValue)
        {
            var request = TestFactory.CreateHttpRequest(queryStringKey, queryStringValue);
            var response = (OkObjectResult)await MyHttpTrigger.Run(request, logger);
            Assert.Equal($"Hello, {queryStringValue}. This HTTP triggered function executed successfully.", response.Value);
        }

        [Fact]
        public void Timer_should_log_message()
        {
            var logger = (ListLogger)TestFactory.CreateLogger(LoggerTypes.List);
            MyTimerTrigger.Run(null, logger);
            var msg = logger.Logs[0];
            Assert.Contains("C# Timer trigger function executed at", msg);
        }
    }
}
```

Bu sınıfta uygulanan Üyeler şunlardır:

- **Http_trigger_should_return_known_string**: Bu test bir http işlevine sorgu dizesi değerleriyle bir istek oluşturur `name=Bill` ve beklenen yanıtın döndürülüp döndürülmediğini denetler.

- **Http_trigger_should_return_string_from_member_data**: Bu test, http işlevine örnek veriler sağlamak Için xUnit özniteliklerini kullanır.

- **Timer_should_log_message**: Bu test bir örneği oluşturur `ListLogger` ve bunu bir Zamanlayıcı işlevlerine geçirir. İşlev çalıştırıldığında, beklenen iletinin mevcut olduğundan emin olmak için günlük denetlenir.

Testlerinizde uygulama ayarlarına erişmek istiyorsanız, [](./functions-dotnet-dependency-injection.md) `IConfiguration` işlevinizdeki moclenmiş ortam değişkeni değerlerini içeren bir örnek ekleyebilirsiniz.

### <a name="run-tests"></a>Testleri çalıştırma

Testleri çalıştırmak için **Test Gezgini** ' ne gidin ve **Tümünü Çalıştır**' a tıklayın.

![Visual Studio 'Da C# ile Azure Işlevlerini test etme](./media/functions-test-a-function/azure-functions-test-visual-studio-xunit.png)

### <a name="debug-tests"></a>Hata ayıklama testleri

Testlerde hata ayıklamak için bir test üzerine bir kesme noktası ayarlayın, **Test Gezgini** 'ne gidin ve **son çalıştırma > hata ayıkla Çalıştır**' a tıklayın.

## <a name="javascript-in-vs-code"></a>VS Code JavaScript

Aşağıdaki örnek, VS Code ' de bir JavaScript Işlev uygulamasının nasıl oluşturulduğunu ve [jest](https://jestjs.io)ile nasıl çalıştırılacağını ve test edileceğini açıklar. Bu yordam, Azure Işlevleri oluşturmak için [vs Code işlevleri uzantısını](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) kullanır.

![VS Code 'de JavaScript ile Azure Işlevlerini test etme](./media/functions-test-a-function/azure-functions-test-vs-code-jest.png)

### <a name="setup"></a>Kurulum

Ortamınızı ayarlamak için, çalıştırarak boş bir klasörde yeni bir Node.js uygulaması başlatın `npm init` .

```bash
npm init -y
```

Sonra, aşağıdaki komutu çalıştırarak jest 'yi çalıştırın:

```bash
npm i jest
```

Şimdi var olan test komutunu şu komutla değiştirmek için _package.jsüzerinde_ güncelleştirin:

```bash
"scripts": {
    "test": "jest"
}
```

### <a name="create-test-modules"></a>Test modülleri oluştur

Proje başlatıldıktan sonra otomatikleştirilmiş testleri çalıştırmak için kullanılan modülleri oluşturabilirsiniz. Destek modüllerini tutmak için *Test* adlı yeni bir klasör oluşturarak başlayın.

*Sınama* klasörü ' nde yeni bir dosya ekleyin, **defaultContext.js** adlandırın ve aşağıdaki kodu ekleyin:

```javascript
module.exports = {
    log: jest.fn()
};
```

Bu modül, varsayılan yürütme bağlamını göstermek için *günlük* işlevini gizler.

Sonra, yeni bir dosya ekleyin, **defaultTimer.js** adlandırın ve aşağıdaki kodu ekleyin:

```javascript
module.exports = {
    IsPastDue: false
};
```

Bu modül, `IsPastDue` özelliği tek başına sahte bir zamanlayıcı örneği olarak uygular. Test bandı yalnızca sonucu test etmek için işlevi çağırarak, NCRONTAB ifadeleri gibi Zamanlayıcı konfigürasyonları burada gerekli değildir.

Ardından, [Yeni bir JAVASCRIPT http işlevi oluşturmak](/azure/developer/javascript/tutorial-vscode-serverless-node-01) ve *httptrigger* olarak adlandırmak için vs Code işlevleri uzantısını kullanın. İşlev oluşturulduktan sonra, **index.test.js** adlı aynı klasöre yeni bir dosya ekleyin ve aşağıdaki kodu ekleyin:

```javascript
const httpFunction = require('./index');
const context = require('../testing/defaultContext')

test('Http trigger should return known text', async () => {

    const request = {
        query: { name: 'Bill' }
    };

    await httpFunction(context, request);

    expect(context.log.mock.calls.length).toBe(1);
    expect(context.res.body).toEqual('Hello Bill');
});
```

Şablondaki HTTP işlevi, sorgu dizesinde belirtilen adla birleştirilmiş bir "Hello" dizesi döndürüyor. Bu test bir isteğin sahte bir örneğini oluşturur ve bunu HTTP işlevine geçirir. Test, *günlük* yönteminin bir kez çağrıldığını ve döndürülen metnin "Hello Bill" değerine eşit olduğunu denetler.

Ardından, yeni bir JavaScript Zamanlayıcı Işlevi oluşturmak ve bunu *Timertrigger* olarak adlandırmak Için vs Code işlevleri uzantısını kullanın. İşlev oluşturulduktan sonra, **index.test.js** adlı aynı klasöre yeni bir dosya ekleyin ve aşağıdaki kodu ekleyin:

```javascript
const timerFunction = require('./index');
const context = require('../testing/defaultContext');
const timer = require('../testing/defaultTimer');

test('Timer trigger should log message', () => {
    timerFunction(context, timer);
    expect(context.log.mock.calls.length).toBe(1);
});
```

Şablondaki süreölçer işlevi, işlevin gövdesinin sonunda bir ileti kaydeder. Bu test, *günlük* işlevinin bir kez çağrıldığından emin olmanızı sağlar.

### <a name="run-tests"></a>Testleri çalıştırma

Testleri çalıştırmak için **CTRL + ~** tuşlarına basarak komut penceresini açın ve şunu çalıştırın `npm test` :

```bash
npm test
```

![VS Code 'de JavaScript ile Azure Işlevlerini test etme](./media/functions-test-a-function/azure-functions-test-vs-code-jest.png)

### <a name="debug-tests"></a>Hata ayıklama testleri

Testlerinizde hata ayıklamak için aşağıdaki yapılandırmayı dosyanıza *launch.js* ekleyin:

```json
{
  "type": "node",
  "request": "launch",
  "name": "Jest Tests",
  "disableOptimisticBPs": true,
  "program": "${workspaceRoot}/node_modules/jest/bin/jest.js",
  "args": [
      "-i"
  ],
  "internalConsoleOptions": "openOnSessionStart"
}
```

Sonra, testinizde bir kesme noktası ayarlayın ve **F5** tuşuna basın.

## <a name="next-steps"></a>Sonraki adımlar

İşlevleriniz için otomatikleştirilmiş testlerin nasıl yazılacağını öğrendiğinize göre şu kaynaklarla devam edin:

- [HTTP ile tetiklenmeyen bir işlevi el ile çalıştırma](./functions-manually-run-non-http.md)
- [Azure Işlevleri hata işleme](./functions-bindings-error-pages.md)
- [Azure Işlevi Event Grid yerel hata ayıklamayı Tetikle](./functions-debug-event-grid-trigger-local.md)