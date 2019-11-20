---
title: Dayanıklı İşlevler izleyiciler-Azure
description: Azure Işlevleri için Dayanıklı İşlevler uzantısını kullanarak bir durum İzleyicisi uygulamayı öğrenin.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 5cb4602ac0431e09208953122f13b30124ab77f5
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73614745"
---
# <a name="monitor-scenario-in-durable-functions---weather-watcher-sample"></a>Dayanıklı İşlevler-Hava durumu izleyici örneğinde izleme senaryosu

İzleyici stili, bir iş akışında esnek bir *yinelenen* işleme anlamına gelir. Örneğin, belirli koşullar karşılanana kadar yoklama yapar. Bu makalede, izlemeyi uygulamak için [dayanıklı işlevler](durable-functions-overview.md) kullanan bir örnek açıklanmaktadır.

[!INCLUDE [v1-note](../../../includes/functions-durable-v1-tutorial-note.md)]

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>Senaryoya genel bakış

Bu örnek, bir konumun geçerli hava durumu koşullarını izler ve skıes açık olduğunda bir kullanıcıyı SMS ile uyarır. Hava durumunu denetlemek ve uyarı göndermek için normal bir Zamanlayıcı tarafından tetiklenen bir işlev kullanabilirsiniz. Ancak, Bu yaklaşımdaki bir sorun **ömür yönetirüdir**. Yalnızca bir uyarı gönderilmesi gerekiyorsa, izleyicinin açık hava durumu algılandıktan sonra kendisini devre dışı bırakması gerekir. İzleme deseninin diğer avantajları arasında kendi yürütmesi sona erdirmek için:

* İzleyiciler, zamanlama değil, aralıklar üzerinde çalışır: bir Zamanlayıcı tetikleyicisi her saat *çalışır* ; izleyici, eylemler arasında bir saat *bekler* . Bir izleyicinin eylemleri belirtilmediyse, bu işlemler, uzun süre çalışan görevler için önemli olabilen, belirtilmediği sürece örtüşmeyecektir.
* İzleyicilerin dinamik aralıkları olabilir: bekleme süresi bazı koşula göre değişebilir.
* İzleyiciler, bazı koşullar karşılandığında veya başka bir işlem tarafından sonlandırılırsa sonlandırılabilir.
* İzleyiciler parametre alabilir. Örnek, aynı Hava durumu izleme işleminin istenen konuma ve telefon numarasına nasıl uygulanabileceğini gösterir.
* İzleyiciler ölçeklenebilir. Her izleyici bir düzenleme örneği olduğundan, yeni işlevler oluşturmak veya daha fazla kod tanımlamak zorunda kalmadan birden fazla izleyici oluşturulabilir.
* İzleyiciler daha büyük iş akışlarıyla kolayca tümleşir. İzleyici, daha karmaşık bir düzenleme işlevinin veya [alt Orchestration](durable-functions-sub-orchestrations.md)'un bir bölümü olabilir.

## <a name="configuring-twilio-integration"></a>Twilio tümleştirmesini yapılandırma

[!INCLUDE [functions-twilio-integration](../../../includes/functions-twilio-integration.md)]

## <a name="configuring-weather-underground-integration"></a>Hava durumu yetersiz tümleştirmesini yapılandırma

Bu örnek, bir konum için geçerli hava durumu koşullarını denetlemek için hava durumu düşük olan API 'sini kullanmayı içerir.

İhtiyaç duyduğunuz ilk şey, hava durumu düşük bir hesaptır. [https://www.wunderground.com/signup](https://www.wunderground.com/signup)adresinden ücretsiz olarak bir tane oluşturabilirsiniz. Hesabınız olduktan sonra bir API anahtarı edinmeniz gerekir. Bunu, [https://www.wunderground.com/weather/api](https://www.wunderground.com/weather/api/?MR=1)ziyaret ederek ve sonra anahtar ayarları ' nı seçerek yapabilirsiniz. Bu örneği çalıştırmak için, Stratler geliştirici planı ücretsizdir ve yeterlidir.

Bir API anahtarınız olduğunda, işlev uygulamanıza aşağıdaki **uygulama ayarını** ekleyin.

| Uygulama ayarı adı | Değer açıklaması |
| - | - |
| **Dalgalı Therundergroundadıkey**  | Hava durumu düşük API anahtarınız. |

## <a name="the-functions"></a>İşlevler

Bu makalede örnek uygulamada aşağıdaki işlevler açıklanmaktadır:

* `E3_Monitor`: düzenli aralıklarla `E3_GetIsClear` çağıran bir Orchestrator işlevi. `E3_GetIsClear` true döndürürse `E3_SendGoodWeatherAlert` çağırır.
* `E3_GetIsClear`: bir konum için geçerli hava durumu koşullarını denetleyen bir etkinlik işlevi.
* `E3_SendGoodWeatherAlert`: Twilio aracılığıyla SMS iletisi gönderen bir etkinlik işlevi.

Aşağıdaki bölümlerde, komut dosyası ve JavaScript için C# kullanılan yapılandırma ve kod açıklanmaktadır. Visual Studio geliştirme kodu makalenin sonunda gösterilmektedir.

## <a name="the-weather-monitoring-orchestration-visual-studio-code-and-azure-portal-sample-code"></a>Hava durumu izleme düzenlemesi (Visual Studio Code ve Azure portal örnek kodu)

**E3_Monitor** işlevi, Orchestrator işlevleri için standart *function. JSON* ' i kullanır.

[!code-json[Main](~/samples-durable-functions/samples/csx/E3_Monitor/function.json)]

İşlevi uygulayan kod aşağıda verilmiştir:

### <a name="c-script"></a>C#SCRIPT

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E3_Monitor/run.csx)]

### <a name="javascript-functions-20-only"></a>JavaScript (yalnızca Işlevler 2,0)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_Monitor/index.js)]

Bu Orchestrator işlevi aşağıdaki eylemleri gerçekleştirir:

1. İzlenecek *konumdan* ve SMS bildirimi gönderecek *telefon numarasına* sahip olan **monitorrequest** 'i alır.
2. İzleyicinin sona erme süresini belirler. Örnek, breçekimi için sabit kodlanmış bir değer kullanır.
3. İstenen konumda açık skiler olup olmadığını anlamak için **E3_GetIsClear** çağırır.
4. Hava durumu açık ise, istenen telefon numarasına SMS bildirimi göndermek için **E3_SendGoodWeatherAlert** çağırır.
5. Bir sonraki yoklama aralığında Orchestration işlemini sürdürecek dayanıklı bir zamanlayıcı oluşturur. Örnek, breçekimi için sabit kodlanmış bir değer kullanır.
6. `CurrentUtcDateTime` (.NET) veya `currentUtcDateTime` (JavaScript) izleyicinin sona erme süresini geçirene veya SMS uyarısı gönderildiğinde çalışmaya devam eder.

Birden çok Orchestrator **isteği**göndererek birden fazla Orchestrator örneği aynı anda çalışabilir. İzlenecek konum ve SMS uyarısı gönderilecek telefon numarası belirtilebilir.

## <a name="strongly-typed-data-transfer-net-only"></a>Kesin tür belirtilmiş veri aktarımı (yalnızca .NET)

Orchestrator birden çok veri parçası gerektirir, bu nedenle [paylaşılan POCO nesneleri](../functions-reference-csharp.md#reusing-csx-code) , ve C# C# betikteki kesin türü belirtilmiş veri aktarımı için kullanılır:  
[!code-csharp[Main](~/samples-durable-functions/samples/csx/shared/MonitorRequest.csx)]

[!code-csharp[Main](~/samples-durable-functions/samples/csx/shared/Location.csx)]

JavaScript örneği, normal JSON nesnelerini parametre olarak kullanır.

## <a name="helper-activity-functions"></a>Yardımcı etkinlik işlevleri

Diğer örneklerde olduğu gibi, yardımcı etkinlik işlevleri de `activityTrigger` tetikleyici bağlamasını kullanan normal işlevlerdir. **E3_GetIsClear** Işlevi, hava durumu düşük olan API 'yi kullanarak geçerli hava durumu koşullarını alır ve çatonun açık olup olmadığını belirler. *Function. JSON* aşağıdaki gibi tanımlanır:

[!code-json[Main](~/samples-durable-functions/samples/csx/E3_GetIsClear/function.json)]

İşte uygulama. Veri aktarımı için kullanılan POCOs gibi, API çağrısını işleme mantığı ve yanıt JSON 'u ' deki C#paylaşılan bir sınıfta soyutlanmalıdır. Bunu, [Visual Studio örnek kodunun](#run-the-sample)bir parçası olarak bulabilirsiniz.

### <a name="c-script"></a>C#SCRIPT

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E3_GetIsClear/run.csx)]

### <a name="javascript-functions-20-only"></a>JavaScript (yalnızca Işlevler 2,0)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_GetIsClear/index.js)]

**E3_SendGoodWeatherAlert** işlevi, son kullanıcıya bir ilerleme için iyi bir zaman olduğunu BILDIREN bir SMS iletisi göndermek için Twilio bağlamasını kullanır. *Function. JSON* basittir:

[!code-json[Main](~/samples-durable-functions/samples/csx/E3_SendGoodWeatherAlert/function.json)]

SMS iletisini gönderen kod aşağıda verilmiştir:

### <a name="c-script"></a>C#SCRIPT

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E3_SendGoodWeatherAlert/run.csx)]

### <a name="javascript-functions-20-only"></a>JavaScript (yalnızca Işlevler 2,0)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_SendGoodWeatherAlert/index.js)]

## <a name="run-the-sample"></a>Örneği çalıştırma

Örneğe dahil edilen HTTP ile tetiklenen işlevleri kullanarak, aşağıdaki HTTP POST isteğini göndererek düzenleme işlemini başlatabilirsiniz:

```
POST https://{host}/orchestrators/E3_Monitor
Content-Length: 77
Content-Type: application/json

{ "location": { "city": "Redmond", "state": "WA" }, "phone": "+1425XXXXXXX" }
```

```
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Location: https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635?taskHub=SampleHubVS&connection=Storage&code={SystemKey}
RetryAfter: 10

{"id": "f6893f25acf64df2ab53a35c09d52635", "statusQueryGetUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635?taskHub=SampleHubVS&connection=Storage&code={systemKey}", "sendEventPostUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/raiseEvent/{eventName}?taskHub=SampleHubVS&connection=Storage&code={systemKey}", "terminatePostUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason={text}&taskHub=SampleHubVS&connection=Storage&code={systemKey}"}
```

**E3_Monitor** örneği başlatılır ve istenen konum için geçerli hava durumu koşullarını sorgular. Hava durumu açık ise, uyarı göndermek için bir etkinlik işlevi çağırır; Aksi takdirde, bir Zamanlayıcı ayarlar. Süreölçerin süresi dolduğunda düzenleme sürdürülecek.

Azure Işlevleri portalındaki işlev günlüklerine bakarak Orchestration 'un etkinliğini görebilirsiniz.

```
2018-03-01T01:14:41.649 Function started (Id=2d5fcadf-275b-4226-a174-f9f943c90cd1)
2018-03-01T01:14:42.741 Started orchestration with ID = '1608200bb2ce4b7face5fc3b8e674f2e'.
2018-03-01T01:14:42.780 Function completed (Success, Id=2d5fcadf-275b-4226-a174-f9f943c90cd1, Duration=1111ms)
2018-03-01T01:14:52.765 Function started (Id=b1b7eb4a-96d3-4f11-a0ff-893e08dd4cfb)
2018-03-01T01:14:52.890 Received monitor request. Location: Redmond, WA. Phone: +1425XXXXXXX.
2018-03-01T01:14:52.895 Instantiating monitor for Redmond, WA. Expires: 3/1/2018 7:14:52 AM.
2018-03-01T01:14:52.909 Checking current weather conditions for Redmond, WA at 3/1/2018 1:14:52 AM.
2018-03-01T01:14:52.954 Function completed (Success, Id=b1b7eb4a-96d3-4f11-a0ff-893e08dd4cfb, Duration=189ms)
2018-03-01T01:14:53.226 Function started (Id=80a4cb26-c4be-46ba-85c8-ea0c6d07d859)
2018-03-01T01:14:53.808 Function completed (Success, Id=80a4cb26-c4be-46ba-85c8-ea0c6d07d859, Duration=582ms)
2018-03-01T01:14:53.967 Function started (Id=561d0c78-ee6e-46cb-b6db-39ef639c9a2c)
2018-03-01T01:14:53.996 Next check for Redmond, WA at 3/1/2018 1:44:53 AM.
2018-03-01T01:14:54.030 Function completed (Success, Id=561d0c78-ee6e-46cb-b6db-39ef639c9a2c, Duration=62ms)
```

Zaman aşımı süresine ulaşıldığında düzenleme [sonlandırılır](durable-functions-instance-management.md) veya temizleme işlemi algılanır. Ayrıca, başka bir işlev içinde `TerminateAsync` (.NET) veya `terminate` (JavaScript) kullanabilir veya yukarıdaki 202 yanıtında başvurulan **Terminateposturi** http post Web kancasını çağırabilirsiniz ve bu `{text}` sonlandırma nedeni ile değiştirebilirsiniz:

```
POST https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason=Because&taskHub=SampleHubVS&connection=Storage&code={systemKey}
```

## <a name="visual-studio-sample-code"></a>Visual Studio örnek kodu

Visual Studio projesindeki tek C# bir dosya olarak Orchestration aşağıda verilmiştir:

> [!NOTE]
> Aşağıdaki örnek kodu çalıştırmak için `Microsoft.Azure.WebJobs.Extensions.Twilio` NuGet paketini yüklemeniz gerekir.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/Monitor.cs)]

## <a name="next-steps"></a>Sonraki adımlar

Bu örnekte, bir dış kaynağın durumunu [dayanıklı zamanlayıcılar](durable-functions-timers.md) ve koşullu mantık kullanarak izlemek için dayanıklı işlevler nasıl kullanılacağı gösterilmiştir. Sonraki örnek, insan etkileşimini işlemek için dış olayların ve [dayanıklı zamanlayıcıları](durable-functions-timers.md) nasıl kullanacağınızı gösterir.

> [!div class="nextstepaction"]
> [İnsan etkileşimi örneğini çalıştırma](durable-functions-phone-verification.md)
