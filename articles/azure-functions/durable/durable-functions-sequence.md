---
title: Dayanıklı İşlevler-Azure 'da işlev zinciri oluşturma
description: İşlev sırasını yürüten bir Dayanıklı İşlevler örneğini çalıştırmayı öğrenin.
author: cgillum
ms.topic: conceptual
ms.date: 11/29/2019
ms.author: azfuncdf
ms.openlocfilehash: f8223b1273c2a487e15e3c10d7c6852a119e4cdc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98028259"
---
# <a name="function-chaining-in-durable-functions---hello-sequence-sample"></a>Dayanıklı İşlevler-Merhaba dizisi örneğinde işlev zinciri oluşturma

İşlev zinciri, belirli bir sırada işlev dizisini yürütme düzenine başvurur. Genellikle bir işlevin çıktısının başka bir işlevin girişine uygulanması gerekir. Bu makalede dayanıklı işlevler hızlı başlangıcı ([C#](durable-functions-create-first-csharp.md),  [JavaScript](quickstart-js-vscode.md)veya [Python](quickstart-python-vscode.md)) tamamlandığında oluşturduğunuz zincirleme sırası açıklanmaktadır. Dayanıklı İşlevler hakkında daha fazla bilgi için bkz. [dayanıklı işlevler genel bakış](durable-functions-overview.md).

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="the-functions"></a>İşlevler

Bu makalede örnek uygulamada aşağıdaki işlevler açıklanmaktadır:

* `E1_HelloSequence`: Bir dizide birden çok kez çağıran bir [Orchestrator işlevi](durable-functions-bindings.md#orchestration-trigger) `E1_SayHello` . `E1_SayHello`Çağrılardan çıkışları depolar ve sonuçları kaydeder.
* `E1_SayHello`: "Hello" ile bir dizeyi eklenen bir [etkinlik işlevi](durable-functions-bindings.md#activity-trigger) .
* `HttpStart`: Orchestrator örneğini başlatan bir HTTP tarafından tetiklenen [dayanıklı istemci](durable-functions-bindings.md#orchestration-client) işlevi.

### <a name="e1_hellosequence-orchestrator-function"></a>E1_HelloSequence Orchestrator işlevi

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=13-25)]

Tüm C# Orchestration işlevlerinin, derlemede bulunan türünde bir parametresi olmalıdır `DurableOrchestrationContext` `Microsoft.Azure.WebJobs.Extensions.DurableTask` . Bu bağlam nesnesi diğer *etkinlik* işlevlerini çağırmanıza ve metodunu kullanarak giriş parametrelerini geçirmeye olanak tanır `CallActivityAsync` .

Kod, `E1_SayHello` farklı parametre değerleriyle sırayla üç kez çağrı çağırır. Her çağrının dönüş değeri `outputs` , işlevin sonunda döndürülen listeye eklenir.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

> [!NOTE]
> JavaScript Dayanıklı İşlevler yalnızca Işlevler 3,0 çalışma zamanı için kullanılabilir.

#### <a name="functionjson"></a>function.json

Geliştirme için Visual Studio Code veya Azure portal kullanıyorsanız, Orchestrator işlevinin dosyasındaki *function.js* içeriği aşağıda verilmiştir. Dosyalardaki çoğu Orchestrator *function.js* neredeyse tam olarak bu şekilde görünür.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/function.json)]

Bağlama türü önemli bir şeydir `orchestrationTrigger` . Tüm Orchestrator işlevlerinin bu tetikleyici türünü kullanması gerekir.

> [!WARNING]
> Orchestrator işlevlerinin "g/ç" kuralına göre ABIDE 'ye, tetikleyici bağlamayı kullanırken herhangi bir giriş veya çıkış bağlaması kullanmayın `orchestrationTrigger` .  Diğer giriş veya çıkış bağlamaları gerekliyse, bunun yerine `activityTrigger` Orchestrator tarafından çağrılan işlevler bağlamında kullanılması gerekir. Daha fazla bilgi için bkz. [Orchestrator işlev kodu kısıtlamaları](durable-functions-code-constraints.md) makalesi.

#### <a name="indexjs"></a>index.js

Orchestrator işlevi şöyledir:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/index.js)]

Tüm JavaScript düzenleme işlevleri [ `durable-functions` Modül](https://www.npmjs.com/package/durable-functions)içermelidir. JavaScript içinde Dayanıklı İşlevler yazmanızı sağlayan bir kitaplıktır. Orchestrator işlevi ve diğer JavaScript işlevleri arasında üç önemli fark vardır:

1. Orchestrator işlevi bir [Oluşturucu işlevidir](/scripting/javascript/advanced/iterators-and-generators-javascript).
2. İşlev, `durable-functions` modülün `orchestrator` yöntemine (burada) yapılan bir çağrıda sarmalanır `df` .
3. İşlevin zaman uyumlu olması gerekir. ' Orchestrator ' yöntemi ' Context. Done ' çağrılmasını işletiğinden, işlev yalnızca ' Return ' olmalıdır.

`context`Nesnesi, `df` diğer *etkinlik* işlevlerini çağırmanıza ve metodunu kullanarak giriş parametrelerini geçirmeye imkan tanıyan dayanıklı bir düzenleme bağlam nesnesi içerir `callActivity` . Kod, `E1_SayHello` farklı parametre değerleriyle sırayla üç kez çağrı `yield` yaparak yürütmenin zaman uyumsuz etkinlik işlev çağrılarının döndürülecek şekilde beklemesi gerektiğini belirtmek için kullanılır. Her çağrının dönüş değeri `outputs` diziye eklenir ve işlevin sonunda döndürülür.

# <a name="python"></a>[Python](#tab/python)

> [!NOTE]
> Python Dayanıklı İşlevler yalnızca Işlevler 3,0 çalışma zamanı için kullanılabilir.


#### <a name="functionjson"></a>function.json

Geliştirme için Visual Studio Code veya Azure portal kullanıyorsanız, Orchestrator işlevinin dosyasındaki *function.js* içeriği aşağıda verilmiştir. Dosyalardaki çoğu Orchestrator *function.js* neredeyse tam olarak bu şekilde görünür.

[!code-json[Main](~/samples-durable-functions-python/samples/function_chaining/E1_HelloSequence/function.json)]

Bağlama türü önemli bir şeydir `orchestrationTrigger` . Tüm Orchestrator işlevlerinin bu tetikleyici türünü kullanması gerekir.

> [!WARNING]
> Orchestrator işlevlerinin "g/ç" kuralına göre ABIDE 'ye, tetikleyici bağlamayı kullanırken herhangi bir giriş veya çıkış bağlaması kullanmayın `orchestrationTrigger` .  Diğer giriş veya çıkış bağlamaları gerekliyse, bunun yerine `activityTrigger` Orchestrator tarafından çağrılan işlevler bağlamında kullanılması gerekir. Daha fazla bilgi için bkz. [Orchestrator işlev kodu kısıtlamaları](durable-functions-code-constraints.md) makalesi.

#### <a name="__init__py"></a>\_\_init \_ \_ . Kopyala

Orchestrator işlevi şöyledir:

[!code-python[Main](~/samples-durable-functions-python/samples/function_chaining/E1_HelloSequence/\_\_init\_\_.py)]

Tüm Python düzenleme işlevlerinin [ `durable-functions` paketi](https://pypi.org/project/azure-functions-durable)içermesi gerekir. Bu, Python 'da Dayanıklı İşlevler yazmanızı sağlayan bir kitaplıktır. Orchestrator işlevi ve diğer Python işlevleri arasında iki önemli fark vardır:

1. Orchestrator işlevi bir [Oluşturucu işlevidir](https://wiki.python.org/moin/Generators).
2. _Dosya_ , dosyanın sonuna bakarak Orchestrator işlevini bir Orchestrator olarak kaydetmelidir `main = df.Orchestrator.create(<orchestrator function name>)` . Bu, dosyanın dosyada belirtilen diğer, yardımcı ve işlevlerden ayırt edilmesini sağlar.

`context`Nesnesi diğer *etkinlik* işlevlerini çağırmanıza ve metodunu kullanarak giriş parametrelerini geçirmeye olanak tanır `call_activity` . Kod, `E1_SayHello` farklı parametre değerleriyle sırayla üç kez çağrı `yield` yaparak yürütmenin zaman uyumsuz etkinlik işlev çağrılarının döndürülecek şekilde beklemesi gerektiğini belirtmek için kullanılır. Her çağrının dönüş değeri, işlevin sonunda döndürülür.

---

### <a name="e1_sayhello-activity-function"></a>E1_SayHello Activity işlevi

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=27-32)]

Etkinlikler özniteliğini kullanır `ActivityTrigger` . `IDurableActivityContext`Kullanarak giriş değerine erişim gibi etkinlik ile ilgili eylemler gerçekleştirmek için, ' yi kullanın `GetInput<T>` .

Uygulamasının uygulanması `E1_SayHello` görece önemsiz bir dize biçimlendirme işlemidir.

Bir öğesine bağlama yerine `IDurableActivityContext` , doğrudan etkinlik işlevine geçirilen türe bağlayabilirsiniz. Örnek:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=34-38)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

#### <a name="e1_sayhellofunctionjson"></a>E1_SayHello/function.jsaçık

Etkinlik işlevi için *function.js* dosyası, `E1_SayHello` `E1_HelloSequence` `activityTrigger` bağlama türü yerine bir bağlama türü kullanması dışında öğesine benzerdir `orchestrationTrigger` .

[!code-json[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/function.json)]

> [!NOTE]
> Bir Orchestration işlevi tarafından çağrılan tüm etkinlik işlevleri `activityTrigger` bağlamayı kullanmalıdır.

Uygulamasının uygulanması `E1_SayHello` görece önemsiz bir dize biçimlendirme işlemidir.

#### <a name="e1_sayhelloindexjs"></a>E1_SayHello/index.js

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/index.js)]

Orchestration işlevinin aksine, etkinlik işlevinin özel bir kurulum yapması gerekmez. Orchestrator işlevi tarafından kendisine geçirilen giriş, `context.bindings` `activityTrigger` Bu örnekte, bağlamanın adı altındaki nesne üzerinde bulunur `context.bindings.name` . Bağlama adı, içe aktarılmış işlevin parametresi olarak ayarlanabilir ve doğrudan erişilebilen, örnek kodun yaptığı şeydir.

# <a name="python"></a>[Python](#tab/python)

#### <a name="e1_sayhellofunctionjson"></a>E1_SayHello/function.jsaçık

Etkinlik işlevi için *function.js* dosyası, `E1_SayHello` `E1_HelloSequence` `activityTrigger` bağlama türü yerine bir bağlama türü kullanması dışında öğesine benzerdir `orchestrationTrigger` .

[!code-json[Main](~/samples-durable-functions-python/samples/function_chaining/E1_SayHello/function.json)]

> [!NOTE]
> Bir Orchestration işlevi tarafından çağrılan tüm etkinlik işlevleri `activityTrigger` bağlamayı kullanmalıdır.

Uygulamasının uygulanması `E1_SayHello` görece önemsiz bir dize biçimlendirme işlemidir.

#### <a name="e1_sayhello__init__py"></a>E1_SayHello/ \_ \_ init \_ \_ . Kopyala

[!code-python[Main](~/samples-durable-functions-python/samples/function_chaining/E1_SayHello/\_\_init\_\_.py)]

Orchestrator işlevinin aksine, etkinlik işlevinin özel bir kurulum yapması gerekmez. Orchestrator işlevi tarafından kendisine geçirilen girişe, işleve parametre olarak doğrudan erişilebilir.

---

### <a name="httpstart-client-function"></a>HttpStart istemci işlevi

Bir istemci işlevi kullanarak Orchestrator işlevinin bir örneğini başlatabilirsiniz. `HttpStart`Örneklerinin başlatılması için http ile tetiklenen işlevi kullanacaksınız `E1_HelloSequence` .

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs?range=13-30)]

Düzenleyiciler ile etkileşim kurmak için işlevin bir `DurableClient` giriş bağlaması içermesi gerekir. Bir düzenleme başlatmak için istemcisini kullanırsınız. Ayrıca, yeni Orchestration 'un durumunu denetlemek için URL 'Leri içeren bir HTTP yanıtı döndürbaşlamanıza da yardımcı olabilir.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

#### <a name="httpstartfunctionjson"></a>HttpStart/function.json

[!code-json[Main](~/samples-durable-functions/samples/javascript/HttpStart/function.json?highlight=16-20)]

Düzenleyiciler ile etkileşim kurmak için işlevin bir `durableClient` giriş bağlaması içermesi gerekir.

#### <a name="httpstartindexjs"></a>HttpStart/index.js

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

`df.getClient`Bir nesne almak için kullanın `DurableOrchestrationClient` . Bir düzenleme başlatmak için istemcisini kullanırsınız. Ayrıca, yeni Orchestration 'un durumunu denetlemek için URL 'Leri içeren bir HTTP yanıtı döndürbaşlamanıza da yardımcı olabilir.

# <a name="python"></a>[Python](#tab/python)

#### <a name="httpstartfunctionjson"></a>HttpStart/function.json

[!code-json[Main](~/samples-durable-functions-python/samples/function_chaining/HttpStart/function.json)]

Düzenleyiciler ile etkileşim kurmak için işlevin bir `durableClient` giriş bağlaması içermesi gerekir.

#### <a name="httpstart__init__py"></a>HttpStart/ \_ \_ init \_ \_ . Kopyala

[!code-python[Main](~/samples-durable-functions-python/samples/function_chaining/HttpStart/\_\_init\_\_.py)]

Oluşturucuyu kullanarak `DurableOrchestrationClient` dayanıklı işlevler istemcisi elde edin. Bir düzenleme başlatmak için istemcisini kullanırsınız. Ayrıca, yeni Orchestration 'un durumunu denetlemek için URL 'Leri içeren bir HTTP yanıtı döndürbaşlamanıza da yardımcı olabilir.

---

## <a name="run-the-sample"></a>Örneği çalıştırma

`E1_HelloSequence`Orchestration işlemini yürütmek için AŞAĞıDAKI http post isteğini `HttpStart` işleve gönderin.

```
POST http://{host}/orchestrators/E1_HelloSequence
```

> [!NOTE]
> Önceki HTTP parçacığı, `host.json` dosyada, `api/` tüm http tetikleyici Işlevleri URL 'lerinden varsayılan öneki kaldıran bir giriş olduğunu varsayar. Bu yapılandırmanın işaretlemesini, `host.json` örneklerdeki dosyada bulabilirsiniz.

Örneğin, örneği "myfunctionapp" adlı bir işlev uygulamasında çalıştırıyorsanız, "{Host}" ifadesini "myfunctionapp.azurewebsites.net" ile değiştirin.

Sonuç, şöyle bir HTTP 202 yanıt olur (breçekimi için kırpılmış):

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

Bu noktada düzenleme sıraya alınır ve hemen çalışmaya başlar. Başlıktaki URL, `Location` yürütmenin durumunu denetlemek için kullanılabilir.

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

Gördüğünüz gibi, `runtimeStatus` örneği *tamamlanır* ve `output` Orchestrator işlevi yürütmenin JSON serileştirilmiş sonucunu içerir.

> [!NOTE]
> , Veya gibi diğer tetikleyici türleri için benzer başlangıç mantığı uygulayabilirsiniz `queueTrigger` `eventHubTrigger` `timerTrigger` .

İşlev yürütme günlüklerine bakın. Bu `E1_HelloSequence` işlev, [düzenleme güvenilirliği](durable-functions-orchestrations.md#reliability) konusunda açıklanan yeniden yürütme davranışı nedeniyle birden çok kez başlatıldı ve tamamlandı. Diğer taraftan, `E1_SayHello` Bu işlev yürütmelerinin yeniden çalınmadığından yalnızca üç yürütmesi vardı.

## <a name="next-steps"></a>Sonraki adımlar

Bu örnek, basit bir işlev zincirleme düzenlemesi göstermiştir. Sonraki örnek, fan/fan deseninin nasıl uygulanacağını gösterir.

> [!div class="nextstepaction"]
> [Fan/fan örneğini çalıştırın](durable-functions-cloud-backup.md)
