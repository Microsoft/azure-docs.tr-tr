---
title: Dayanıklı İşlevler-Azure 'da işlev zinciri oluşturma
description: İşlev sırasını yürüten bir Dayanıklı İşlevler örneğini çalıştırmayı öğrenin.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 133169c659328fa4f713eb4b75bc460dee7a3f76
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73614682"
---
# <a name="function-chaining-in-durable-functions---hello-sequence-sample"></a>Dayanıklı İşlevler-Merhaba dizisi örneğinde işlev zinciri oluşturma

İşlev zinciri, belirli bir sırada işlev dizisini yürütme düzenine başvurur. Genellikle bir işlevin çıktısının başka bir işlevin girişine uygulanması gerekir. Bu makalede, Dayanıklı İşlevler hızlı başlangıcı 'ni ([C#](durable-functions-create-first-csharp.md) veya [JavaScript](quickstart-js-vscode.md)) tamamladığınızda oluşturduğunuz zincirleme dizisi açıklanmaktadır. Dayanıklı İşlevler hakkında daha fazla bilgi için bkz. [dayanıklı işlevler genel bakış](durable-functions-overview.md).

[!INCLUDE [v1-note](../../../includes/functions-durable-v1-tutorial-note.md)]

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="the-functions"></a>İşlevler

Bu makalede örnek uygulamada aşağıdaki işlevler açıklanmaktadır:

* `E1_HelloSequence`: bir dizide birden çok kez `E1_SayHello` çağıran bir Orchestrator işlevi. `E1_SayHello` çağrılarındaki çıktıları depolar ve sonuçları kaydeder.
* `E1_SayHello`: "Hello" ile bir dizeyi eklenen bir etkinlik işlevi.

Aşağıdaki bölümlerde, komut dosyası ve JavaScript için C# kullanılan yapılandırma ve kod açıklanmaktadır. Visual Studio geliştirme kodu makalenin sonunda gösterilmektedir.

> [!NOTE]
> JavaScript Dayanıklı İşlevler yalnızca Işlevler 2,0 çalışma zamanı için kullanılabilir.

## <a name="e1_hellosequence"></a>E1_HelloSequence

### <a name="functionjson-file"></a>function. JSON dosyası

Geliştirme için Visual Studio Code veya Azure portal kullanıyorsanız, Orchestrator işlevinin *function. JSON* dosyasının içeriği aşağıda verilmiştir. Çoğu Orchestrator *işlevi. JSON* dosyası neredeyse tam olarak bu şekilde görünür.

[!code-json[Main](~/samples-durable-functions/samples/csx/E1_HelloSequence/function.json)]

Önemli şey `orchestrationTrigger` bağlama türüdür. Tüm Orchestrator işlevlerinin bu tetikleyici türünü kullanması gerekir.

> [!WARNING]
> Orchestrator işlevlerinin "g/ç" kuralına göre ABIDE 'ye `orchestrationTrigger` tetikleyici bağlamayı kullanırken herhangi bir giriş veya çıkış bağlaması kullanmayın.  Diğer giriş veya çıkış bağlamaları gerekliyse, bunun yerine Orchestrator tarafından çağrılan `activityTrigger` işlevleri bağlamında kullanılması gerekir. Daha fazla bilgi için bkz. [Orchestrator işlev kodu kısıtlamaları](durable-functions-code-constraints.md) makalesi.

### <a name="c-script-visual-studio-code-and-azure-portal-sample-code"></a>C#betik (Visual Studio Code ve Azure portal örnek kod)

Kaynak kodu aşağıda verilmiştir:

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E1_HelloSequence/run.csx)]

Tüm C# düzenleme işlevleri, `Microsoft.Azure.WebJobs.Extensions.DurableTask` derlemesinde bulunan `DurableOrchestrationContext`türünde bir parametreye sahip olmalıdır. Betiği kullanıyorsanız C# , derlemeye `#r` gösterimi kullanılarak başvurulabilir. Bu bağlam nesnesi diğer *etkinlik* işlevlerini çağırmanıza ve `CallActivityAsync` yöntemini kullanarak giriş parametrelerini geçirmeye olanak tanır.

Kod, farklı parametre değerleri ile sırayla üç kez `E1_SayHello` çağırır. Her çağrının dönüş değeri, işlevin sonunda döndürülen `outputs` listesine eklenir.

### <a name="javascript"></a>Javascript

Kaynak kodu aşağıda verilmiştir:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/index.js)]

Tüm JavaScript düzenleme işlevleri [`durable-functions` modülünü](https://www.npmjs.com/package/durable-functions)içermelidir. JavaScript içinde Dayanıklı İşlevler yazmanızı sağlayan bir kitaplıktır. Bir Orchestration işlevi ve diğer JavaScript işlevleri arasında üç önemli fark vardır:

1. İşlev bir [Oluşturucu işlevidir.](https://docs.microsoft.com/scripting/javascript/advanced/iterators-and-generators-javascript)
2. İşlev, `durable-functions` modülünün `orchestrator` yöntemine (`df`) bir çağrıda paketlenir.
3. İşlevin zaman uyumlu olması gerekir. ' Orchestrator ' yöntemi ' Context. Done ' çağrılmasını işletiğinden, işlev yalnızca ' Return ' olmalıdır.

`context` `df` nesnesi, diğer *etkinlik* işlevlerini çağırmanıza ve `callActivity` metodunu kullanarak giriş parametrelerini geçirmeye olanak tanır. Kod, farklı parametre değerleriyle sırayla `E1_SayHello` çağırır, yürütmenin zaman uyumsuz etkinlik işlev çağrılarının döndürülecek şekilde beklemesi gerektiğini belirtmek için `yield` kullanarak. Her çağrının dönüş değeri, işlevin sonunda döndürülen `outputs` listesine eklenir.

## <a name="e1_sayhello"></a>E1_SayHello

### <a name="functionjson-file"></a>function. JSON dosyası

`E1_SayHello` Activity işlevi için *function. JSON* dosyası, `orchestrationTrigger` bağlama türü yerine `activityTrigger` bağlama türü kullanması dışında `E1_HelloSequence` benzerdir.

[!code-json[Main](~/samples-durable-functions/samples/csx/E1_SayHello/function.json)]

> [!NOTE]
> Bir Orchestration işlevi tarafından çağrılan tüm işlevleri `activityTrigger` bağlamasını kullanmalıdır.

`E1_SayHello` uygulanması görece basit bir dize biçimlendirme işlemidir.

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E1_SayHello/run.csx)]

Bu işlevin, Orchestrator işlevinin `CallActivityAsync<T>`çağrısı tarafından kendisine geçirilen girişi almak için kullandığı `DurableActivityContext`türünde bir parametresi vardır.

### <a name="javascript"></a>JavaScript

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/index.js)]

JavaScript Orchestration işlevinin aksine, etkinlik işlevinin özel bir kurulum yapması gerekmez. Orchestrator işlevi tarafından kendisine geçirilen giriş, bu örnekte `context.bindings.name``activityTrigger` bağlamanın adı altında `context.bindings` nesnesi üzerinde bulunur. Bağlama adı, içe aktarılmış işlevin parametresi olarak ayarlanabilir ve doğrudan erişilebilen, örnek kodun yaptığı şeydir.

## <a name="run-the-sample"></a>Örneği çalıştırma

`E1_HelloSequence` Orchestration 'u yürütmek için aşağıdaki HTTP POST isteğini gönderin.

```
POST http://{host}/orchestrators/E1_HelloSequence
```

> [!NOTE]
> Önceki HTTP kod parçacığında, tüm HTTP tetikleyici işlevleri URL 'Lerinden varsayılan `api/` önekini kaldıran `host.json` dosyasında bir giriş olduğunu varsaymaktadır. Bu yapılandırmanın işaretlemesini, örneklerdeki `host.json` dosyasında bulabilirsiniz.

Örneğin, örneği "myfunctionapp" adlı bir işlev uygulamasında çalıştırıyorsanız, "{Host}" ifadesini "myfunctionapp.azurewebsites.net" ile değiştirin.

Sonuç, şöyle bir HTTP 202 yanıt olur (breçekimi için kırpılmış):

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

Bu noktada düzenleme sıraya alınır ve hemen çalışmaya başlar. `Location` üstbilgisindeki URL, yürütmenin durumunu denetlemek için kullanılabilir.

```
GET http://{host}/runtime/webhooks/durabletask/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

Sonuç, düzenleme durumudur. Daha çabuk çalışır ve tamamlanır, bu sayede *tamamlandı* durumunda şuna benzer bir Yanıt ile görürsünüz (breçekimi için kırpılmış):

```
HTTP/1.1 200 OK
Content-Length: 179
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":null,"output":["Hello Tokyo!","Hello Seattle!","Hello London!"],"createdTime":"2017-06-29T05:24:57Z","lastUpdatedTime":"2017-06-29T05:24:59Z"}
```

Gördüğünüz gibi, örnek `runtimeStatus` *tamamlanır* ve `output` Orchestrator IşLEVI yürütmenin JSON tarafından serileştirilmiş sonucunu içerir.

> [!NOTE]
> Orchestrator işlevini Başlatan HTTP POST uç noktası, örnek uygulamada "HttpStart" adlı bir HTTP tetikleyici işlevi olarak uygulanır. `queueTrigger`, `eventHubTrigger`veya `timerTrigger`gibi diğer tetikleyici türleri için de benzer başlangıç mantığı uygulayabilirsiniz.

İşlev yürütme günlüklerine bakın. `E1_HelloSequence` işlevi, [düzenleme güvenilirliği](durable-functions-orchestrations.md#reliability) konusunda açıklanan yeniden yürütme davranışı nedeniyle birden çok kez başlatıldı ve tamamlandı. Diğer taraftan, bu işlev yürütmelerinin yeniden yürütülmediğinden yalnızca üç yürütme `E1_SayHello` vardı.

## <a name="visual-studio-sample-code"></a>Visual Studio örnek kodu

Visual Studio projesindeki tek C# bir dosya olarak Orchestration aşağıda verilmiştir:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

## <a name="next-steps"></a>Sonraki adımlar

Bu örnek, basit bir işlev zincirleme düzenlemesi göstermiştir. Sonraki örnek, fan/fan deseninin nasıl uygulanacağını gösterir.

> [!div class="nextstepaction"]
> [Fan/fan örneğini çalıştırın](durable-functions-cloud-backup.md)
