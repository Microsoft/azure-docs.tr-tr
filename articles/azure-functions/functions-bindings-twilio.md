---
title: Azure Işlevleri Twilio bağlama
description: Azure Işlevleri ile Twilio bağlamalarını nasıl kullanacağınızı anlayın.
author: craigshoemaker
ms.topic: reference
ms.date: 07/09/2018
ms.author: cshoe
ms.custom: devx-track-csharp, H1Hack27Feb2017
ms.openlocfilehash: 31d5c528112ccacab47282729b1b5f8a72f19af0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91999286"
---
# <a name="twilio-binding-for-azure-functions"></a>Azure Işlevleri için Twilio bağlama

Bu makalede, Azure Işlevlerinde [Twilio](https://www.twilio.com/) bağlamaları kullanılarak nasıl metin iletileri gönderileceği açıklanır. Azure Işlevleri, Twilio için çıkış bağlamalarını destekler.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Paketler-Işlevler 1. x

Twilio bağlamaları [Microsoft. Azure. WebJobs. Extensions. Twilio](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Twilio) NuGet paketi, sürüm 1. x içinde sağlanır. Paketin kaynak kodu, [Azure-WebJobs-SDK](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.Twilio/) GitHub deposundadır.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x-and-higher"></a>Paketler-Işlevler 2. x ve üzeri

Twilio bağlamaları [Microsoft. Azure. WebJobs. Extensions. Twilio](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Twilio) NuGet paketi, sürüm 3. x içinde sağlanır. Paketin kaynak kodu, [Azure-WebJobs-SDK](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/) GitHub deposundadır.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

<a id="example"></a>

## <a name="example---functions-2x-and-higher"></a>Örnek-Işlevler 2. x ve üzeri

# <a name="c"></a>[C#](#tab/csharp)

Aşağıdaki örnekte, bir kuyruk iletisi tarafından tetiklendiğinde kısa mesaj gönderen bir [C# işlevi](functions-dotnet-class-library.md) gösterilmektedir.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json.Linq;
using Twilio.Rest.Api.V2010.Account;
using Twilio.Types;
namespace TwilioQueueOutput
{
    public static class QueueTwilio
    {
        [FunctionName("QueueTwilio")]
        [return: TwilioSms(AccountSidSetting = "TwilioAccountSid", AuthTokenSetting = "TwilioAuthToken", From = "+1425XXXXXXX")]
        public static CreateMessageOptions Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] JObject order,
        ILogger log)
        {
            log.LogInformation($"C# Queue trigger function processed: {order}");

            var message = new CreateMessageOptions(new PhoneNumber(order["mobileNumber"].ToString()))
            {
                Body = $"Hello {order["name"]}, thanks for your order!"
            };

            return message;
        }
    }
}
```

Bu örnek, `TwilioSms` yöntemi dönüş değeri ile özniteliğini kullanır. Bir alternatif, özniteliği `out CreateMessageOptions` parametresi veya `ICollector<CreateMessageOptions>` veya `IAsyncCollector<CreateMessageOptions>` parametresiyle kullanmaktır.

# <a name="c-script"></a>[C# betiği](#tab/csharp-script)

Aşağıdaki örnek, bir *function.js* dosyadaki bir Twilio çıkış bağlamasını ve bağlamayı kullanan bir [C# betik işlevini](functions-reference-csharp.md) gösterir. İşlevi bir `out` metin mesajı göndermek için bir parametre kullanır.

İşte *function.js* dosyadaki verileri bağlama:

Örnek function.js:

```json
{
  "type": "twilioSms",
  "name": "message",
  "accountSidSetting": "TwilioAccountSid",
  "authTokenSetting": "TwilioAuthToken",
  "from": "+1425XXXXXXX",
  "direction": "out",
  "body": "Azure Functions Testing"
}
```

C# komut dosyası kodu:

```cs
#r "Newtonsoft.Json"
#r "Twilio"
#r "Microsoft.Azure.WebJobs.Extensions.Twilio"

using System;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;
using Microsoft.Azure.WebJobs.Extensions.Twilio;
using Twilio.Rest.Api.V2010.Account;
using Twilio.Types;

public static void Run(string myQueueItem, out CreateMessageOptions message,  ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a JSON string representing an order that contains the name of a
    // customer and a mobile number to send text updates to.
    dynamic order = JsonConvert.DeserializeObject(myQueueItem);
    string msg = "Hello " + order.name + ", thank you for your order.";

    // You must initialize the CreateMessageOptions variable with the "To" phone number.
    message = new CreateMessageOptions(new PhoneNumber("+1704XXXXXXX"));

    // A dynamic message can be set instead of the body in the output binding. In this example, we use
    // the order information to personalize a text message.
    message.Body = msg;
}
```

Zaman uyumsuz kodda Out parametrelerini kullanamazsınız. Zaman uyumsuz bir C# betik kodu örneği aşağıda verilmiştir:

```cs
#r "Newtonsoft.Json"
#r "Twilio"
#r "Microsoft.Azure.WebJobs.Extensions.Twilio"

using System;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;
using Microsoft.Azure.WebJobs.Extensions.Twilio;
using Twilio.Rest.Api.V2010.Account;
using Twilio.Types;

public static async Task Run(string myQueueItem, IAsyncCollector<CreateMessageOptions> message,  ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a JSON string representing an order that contains the name of a
    // customer and a mobile number to send text updates to.
    dynamic order = JsonConvert.DeserializeObject(myQueueItem);
    string msg = "Hello " + order.name + ", thank you for your order.";

    // You must initialize the CreateMessageOptions variable with the "To" phone number.
    CreateMessageOptions smsText = new CreateMessageOptions(new PhoneNumber("+1704XXXXXXX"));

    // A dynamic message can be set instead of the body in the output binding. In this example, we use
    // the order information to personalize a text message.
    smsText.Body = msg;

    await message.AddAsync(smsText);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Aşağıdaki örnek, bir *function.js* dosyadaki bir Twilio çıkış bağlamasını ve bağlamayı kullanan bir [JavaScript işlevini](functions-reference-node.md) gösterir.

İşte *function.js* dosyadaki verileri bağlama:

Örnek function.js:

```json
{
  "type": "twilioSms",
  "name": "message",
  "accountSidSetting": "TwilioAccountSid",
  "authTokenSetting": "TwilioAuthToken",
  "from": "+1425XXXXXXX",
  "direction": "out",
  "body": "Azure Functions Testing"
}
```

JavaScript kodu aşağıda verilmiştir:

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);

    // In this example the queue item is a JSON string representing an order that contains the name of a
    // customer and a mobile number to send text updates to.
    var msg = "Hello " + myQueueItem.name + ", thank you for your order.";

    // Even if you want to use a hard coded message in the binding, you must at least
    // initialize the message binding.
    context.bindings.message = {};

    // A dynamic message can be set instead of the body in the output binding. The "To" number 
    // must be specified in code. 
    context.bindings.message = {
        body : msg,
        to : myQueueItem.mobileNumber
    };

    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

Aşağıdaki örnek, *üzerinde aşağıdakifunction.js* tanımlanan şekilde çıkış BAĞLAMAYı kullanarak SMS iletisinin nasıl gönderileceğini gösterir.

```json
    {
      "type": "twilioSms",
      "name": "twilioMessage",
      "accountSidSetting": "TwilioAccountSID",
      "authTokenSetting": "TwilioAuthToken",
      "from": "+1XXXXXXXXXX",
      "direction": "out",
      "body": "Azure Functions Testing"
    }
```

SMS iletisini göndermek için, seri hale getirilmiş bir JSON nesnesini `func.Out` parametreye geçirebilirsiniz.

```python
import logging
import json
import azure.functions as func

def main(req: func.HttpRequest, twilioMessage: func.Out[str]) -> func.HttpResponse:

    message = req.params.get('message')
    to = req.params.get('to')

    value = {
      "body": message,
      "to": to
    }

    twilioMessage.set(json.dumps(value))

    return func.HttpResponse(f"Message sent")
```

# <a name="java"></a>[Java](#tab/java)

Aşağıdaki örnek, [TwilioSmsOutput](/java/api/com.microsoft.azure.functions.annotation.twiliosmsoutput) ek açıklamanın SMS iletisi göndermek için nasıl kullanılacağını gösterir. `to`, Ve değerlerini `from` `body` programlı olarak geçersiz kılsanız bile öznitelik tanımında gereklidir.

```java
package com.function;

import java.util.*;
import com.microsoft.azure.functions.annotation.*;
import com.microsoft.azure.functions.*;

public class TwilioOutput {

    @FunctionName("TwilioOutput")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", methods = { HttpMethod.GET, HttpMethod.POST },
                authLevel = AuthorizationLevel.FUNCTION) HttpRequestMessage<Optional<String>> request,
            @TwilioSmsOutput(
                name = "twilioMessage",
                accountSid = "AzureWebJobsTwilioAccountSID",
                authToken = "AzureWebJobsTwilioAuthToken",
                to = "+1XXXXXXXXXX",
                body = "From Azure Functions",
                from = "+1XXXXXXXXXX") OutputBinding<String> twilioMessage,
            final ExecutionContext context) {

        String message = request.getQueryParameters().get("message");
        String to = request.getQueryParameters().get("to");

        StringBuilder builder = new StringBuilder()
            .append("{")
            .append("\"body\": \"%s\",")
            .append("\"to\": \"%s\"")
            .append("}");

        final String body = String.format(builder.toString(), message, to);

        twilioMessage.setValue(body);

        return request.createResponseBuilder(HttpStatus.OK).body("Message sent").build();
    }
}
```

---

## <a name="attributes-and-annotations"></a>Öznitelikler ve ek açıklamalar

# <a name="c"></a>[C#](#tab/csharp)

[C# sınıf kitaplıklarında](functions-dotnet-class-library.md) [TwilioSms](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/TwilioSMSAttribute.cs) özniteliğini kullanın.

Yapılandırabileceğiniz öznitelik özellikleri hakkında daha fazla bilgi için bkz. [yapılandırma](#configuration). `TwilioSms`Bir yöntem imzasında bir öznitelik örneği aşağıda verilmiştir:

```csharp
[FunctionName("QueueTwilio")]
[return: TwilioSms(AccountSidSetting = "TwilioAccountSid", AuthTokenSetting = "TwilioAuthToken", From = "+1425XXXXXXX")]
public static CreateMessageOptions Run(
[QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] JObject order, ILogger log)
{
    ...
}
 ```

Tüm örnek için bkz. [C# örneği](#example).

# <a name="c-script"></a>[C# betiği](#tab/csharp-script)

Öznitelikler C# betiği tarafından desteklenmez.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Öznitelikler JavaScript tarafından desteklenmez.

# <a name="python"></a>[Python](#tab/python)

Öznitelikler Python tarafından desteklenmez.

# <a name="java"></a>[Java](#tab/java)

[TwilioSmsOutput](/java/api/com.microsoft.azure.functions.annotation.twiliosmsoutput) ek açıklamasını,,, [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding) `T` `int` `String` `byte[]` veya Pojo türü gibi herhangi bir yerel Java türü olabilecek bir parametreye yerleştirin.

---

## <a name="configuration"></a>Yapılandırma

Aşağıdaki tabloda, dosyasında ve özniteliğinde *function.js* ayarladığınız bağlama yapılandırma özellikleri açıklanmaktadır `TwilioSms` .

| Özellik üzerinde v1 function.js | özelliğindeki v2 function.js | Öznitelik özelliği |Description|
|---------|---------|---------|----------------------|
|**türüyle**|**türüyle**| olarak ayarlanmalıdır `twilioSms` .|
|**Görünüm**|**Görünüm**| olarak ayarlanmalıdır `out` .|
|**ada**|**ada**| Twilio SMS metin mesajı için işlev kodunda kullanılan değişken adı. |
|**accountSid**|**Accountsıdsetting**| **AccountSidSetting**| Bu değer, Twilio hesabı SID 'nizi () tutan bir uygulama ayarının adına ayarlanmalıdır `TwilioAccountSid` . Ayarlanmamışsa, varsayılan uygulama ayarı adı "AzureWebJobsTwilioAccountSid" olur. |
|**authToken**|**authTokenSetting**|**AuthTokenSetting**| Bu değer, Twilio kimlik doğrulama belirtecinizi () tutan bir uygulama ayarının adına ayarlanmalıdır `TwilioAccountAuthToken` . Ayarlanmamışsa, varsayılan uygulama ayarı adı "AzureWebJobsTwilioAuthToken" olur. |
|**kullanıcısı**| Yok-kodda belirt | **Kime**| Bu değer, SMS metninin gönderildiği telefon numarası olarak ayarlanır.|
|**Kaynak**|**Kaynak** | **Kimden**| Bu değer, SMS metninin gönderildiği telefon numarası olarak ayarlanır.|
|**bölümü**|**bölümü** | **Gövde**| Bu değer, işlevinizin kodunda dinamik olarak ayarlamanız gerekmiyorsa SMS metin iletisini sabit koda almak için kullanılabilir. |  

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure işlevleri Tetikleyicileri ve bağlamaları hakkında daha fazla bilgi edinin](functions-triggers-bindings.md)
