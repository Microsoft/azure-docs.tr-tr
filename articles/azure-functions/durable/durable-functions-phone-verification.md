---
title: Dayanıklı İşlevler-Azure 'da insan etkileşimi ve zaman aşımları
description: Azure Işlevleri için Dayanıklı İşlevler uzantısında insan etkileşimini ve zaman aşımlarını nasıl işleyeceğinizi öğrenin.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 0c1c92dde2d698fb2c92fb3680ab05393a25573d
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73614729"
---
# <a name="human-interaction-in-durable-functions---phone-verification-sample"></a>Dayanıklı İşlevler-telefon doğrulama örneğindeki insan etkileşimi

Bu örnek, insan etkileşimini içeren [dayanıklı işlevler](durable-functions-overview.md) bir düzenleme oluşturmayı gösterir. Her gerçek kişi otomatik bir işleme dahil olduğunda, işlem kişiye bildirim gönderebilmeli ve yanıtları zaman uyumsuz olarak alamaz. Ayrıca, kişinin kullanılamama olasılığa izin vermelidir. (Bu son bölüm, zaman aşımlarının önemli hale geldiği yerdir.)

Bu örnek SMS tabanlı bir telefon doğrulama sistemi uygular. Bu akış türleri genellikle müşterinin telefon numarası doğrulanırken veya Multi-Factor Authentication (MFA) için kullanılır. Uygulamanın tamamı birkaç küçük işlev kullanılarak yapıldığından, bu güçlü bir örnektir. Veritabanı gibi dış veri deposu gerekli değildir.

[!INCLUDE [v1-note](../../../includes/functions-durable-v1-tutorial-note.md)]

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>Senaryoya genel bakış

Telefon doğrulaması, uygulamanızın son kullanıcılarının istenmeyen posta olmaması ve bunları söyledikleri kim olduğunu doğrulamak için kullanılır. Multi-Factor Authentication, Kullanıcı hesaplarını korsanlardan korumak için kullanılan yaygın bir kullanım durumdur. Kendi telefon doğrulamanızı uygulamayla ilgili zorluk, insanla ilgili **durum bilgisi** olan bir etkileşim gerektirmesidir. Son Kullanıcı genellikle bazı kodları (örneğin, 4 basamaklı bir sayı) ve **makul bir süre içinde**yanıt vermelidir.

Sıradan Azure Işlevleri durum bilgisi yoktur (diğer platformlarda birçok diğer bulut uç noktası olduğu gibi), bu tür etkileşimler bu şekilde bir veritabanında veya diğer kalıcı depolardaki durumu açıkça yönetme ile ilgilidir. Ayrıca, etkileşim birlikte koordine edilebilir birden çok işleve bölünmelidir. Örneğin, bir kod üzerinde seçim yapmak, bir yere kalıcı hale getirme ve kullanıcının telefonuna gönderilmesi için en az bir işleve ihtiyacınız vardır. Ayrıca, kullanıcıdan yanıt almak için en az bir diğer işleve ihtiyacınız vardır ve kod doğrulamasını yapmak için onu özgün işlev çağrısına geri eşleyin. Bir zaman aşımı, güvenliğin güvence altına almak için de önemli bir yönüdür. Hızlıca karmaşık olabilir.

Dayanıklı İşlevler kullandığınızda bu senaryonun karmaşıklığı büyük ölçüde azalır. Bu örnekte göreceğiniz gibi, bir Orchestrator işlevi, herhangi bir dış veri deposu eklemeden, durum bilgisi olan etkileşimi kolayca yönetebilir. Orchestrator işlevleri *dayanıklı*olduğundan, bu etkileşimli akışlar da son derece güvenilirdir.

## <a name="configuring-twilio-integration"></a>Twilio tümleştirmesini yapılandırma

[!INCLUDE [functions-twilio-integration](../../../includes/functions-twilio-integration.md)]

## <a name="the-functions"></a>İşlevler

Bu makalede örnek uygulamada aşağıdaki işlevler gösterilmektedir:

* **E4_SmsPhoneVerification**
* **E4_SendSmsChallenge**

Aşağıdaki bölümlerde, komut dosyası ve JavaScript için C# kullanılan yapılandırma ve kod açıklanmaktadır. Visual Studio geliştirme kodu makalenin sonunda gösterilmektedir.

## <a name="the-sms-verification-orchestration-visual-studio-code-and-azure-portal-sample-code"></a>SMS doğrulama düzenlemesi (Visual Studio Code ve Azure portal örnek kodu)

**E4_SmsPhoneVerification** işlevi, Orchestrator işlevleri için standart *function. JSON* ' i kullanır.

[!code-json[Main](~/samples-durable-functions/samples/csx/E4_SmsPhoneVerification/function.json)]

İşlevi uygulayan kod aşağıda verilmiştir:

### <a name="c-script"></a>C#SCRIPT

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E4_SmsPhoneVerification/run.csx)]

### <a name="javascript-functions-20-only"></a>JavaScript (yalnızca Işlevler 2,0)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E4_SmsPhoneVerification/index.js)]

Bu Orchestrator işlevi başlatıldıktan sonra şunları yapar:

1. SMS bildirimini *gönderecek* telefon numarasını alır.
2. Kullanıcıya SMS iletisi göndermek için **E4_SendSmsChallenge** çağırır ve beklenen 4 basamaklı sınama kodunu geri döndürür.
3. Geçerli zamandan 90 saniye tetikleyen dayanıklı bir zamanlayıcı oluşturur.
4. Zamanlayıcı ile paralel olarak, kullanıcıdan **Smschallengeresbir** olay bekler.

Kullanıcı dört basamaklı kod içeren bir SMS mesajı alır. Doğrulama işlemini gerçekleştirmek için aynı 4 basamaklı kodun Orchestrator işlev örneğine geri gönderilmesi 90 saniye sürer. Yanlış kodu gönderiyorlarsa, bu iki adım daha (aynı 90-ikinci pencere içinde) daha fazla üç denemeye sahip olur.

> [!NOTE]
> İlk başta açık olmayabilir, ancak bu Orchestrator işlevi tamamen belirleyici olur. `CurrentUtcDateTime` (.NET) ve `currentUtcDateTime` (JavaScript) özelliklerinin Zamanlayıcı süre sonu süresini hesaplamak için kullanıldığı ve bu özelliklerin Orchestrator kodundaki bu noktada her yeniden yürütmeye aynı değeri döndürdüğü için belirleyici vardır. Bu davranış, yinelenen `winner` `Task.WhenAny` (.NET) veya `context.df.Task.any` (JavaScript) ' e yapılan her çağrıdan elde olduğundan emin olmak için önemlidir.

> [!WARNING]
> Sınama yanıtı kabul edildiğinde Yukarıdaki örnekte olduğu gibi, artık kullanım süreleri dolana kadar gerekmiyorsa, [zamanlayıcıları iptal etmeniz](durable-functions-timers.md) önemlidir.

## <a name="send-the-sms-message"></a>SMS iletisini gönder

**E4_SendSmsChallenge** IşLEVI, SMS iletisini son kullanıcıya 4 basamaklı kodla göndermek için Twilio bağlamasını kullanır. *Function. JSON* aşağıdaki gibi tanımlanır:

[!code-json[Main](~/samples-durable-functions/samples/csx/E4_SendSmsChallenge/function.json)]

4 basamaklı zorluk kodu üreten ve SMS iletisini gönderen kod aşağıda verilmiştir:

### <a name="c-script"></a>C#SCRIPT

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E4_SendSmsChallenge/run.csx)]

### <a name="javascript-functions-20-only"></a>JavaScript (yalnızca Işlevler 2,0)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E4_SendSmsChallenge/index.js)]

Bu **E4_SendSmsChallenge** işlevi, işlem kilitlense veya yeniden oynadığında bile yalnızca bir kez çağırılır. Son kullanıcının birden çok SMS iletisi almak istemediğinizde bu iyidir. `challengeCode` dönüş değeri otomatik olarak kalıcı hale getirilir, bu nedenle Orchestrator işlevi her zaman doğru kodun ne olduğunu bilir.

## <a name="run-the-sample"></a>Örneği çalıştırma

Örneğe dahil edilen HTTP ile tetiklenen işlevleri kullanarak, aşağıdaki HTTP POST isteğini göndererek düzenleme işlemini başlatabilirsiniz:

```
POST http://{host}/orchestrators/E4_SmsPhoneVerification
Content-Length: 14
Content-Type: application/json

"+1425XXXXXXX"
```

```
HTTP/1.1 202 Accepted
Content-Length: 695
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{"id":"741c65651d4c40cea29acdd5bb47baf1","statusQueryGetUri":"http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}","sendEventPostUri":"http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}","terminatePostUri":"http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1/terminate?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}"}
```

Orchestrator işlevi, sağlanan telefon numarasını alır ve hemen rastgele oluşturulmuş 4 basamaklı bir doğrulama kodu içeren bir SMS iletisi gönderir &mdash; Örneğin, *2168*. Bu işlev, yanıt için 90 saniye bekler.

Kodla yanıt vermek için, başka bir işlevin içinde [`RaiseEventAsync` (.net) veya `raiseEvent` (JavaScript)](durable-functions-instance-management.md) kullanabilir veya yukarıdaki 202 yanıtında başvurulan **sendEventUrl** http post Web kancasını çağırabilir, `{eventName}` olayın adıyla değiştirerek `SmsChallengeResponse`:

```
POST http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1/raiseEvent/SmsChallengeResponse?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
Content-Length: 4
Content-Type: application/json

2168
```

Bunu zamanlayıcı süresi dolmadan önce gönderirseniz, düzenleme tamamlanır ve `output` alanı, başarılı bir doğrulama olduğunu belirten `true`olarak ayarlanır.

```
GET http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

```
HTTP/1.1 200 OK
Content-Length: 144
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":"+1425XXXXXXX","output":true,"createdTime":"2017-06-29T19:10:49Z","lastUpdatedTime":"2017-06-29T19:12:23Z"}
```

Süreölçerin süresi sona ersin veya yanlış kodu dört kez girerseniz, durumu sorgulayabilir ve telefon doğrulamasının başarısız olduğunu belirten bir `false` düzenleme işlevi çıkışı görebilirsiniz.

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Content-Length: 145

{"runtimeStatus":"Completed","input":"+1425XXXXXXX","output":false,"createdTime":"2017-06-29T19:20:49Z","lastUpdatedTime":"2017-06-29T19:22:23Z"}
```

## <a name="visual-studio-sample-code"></a>Visual Studio örnek kodu

Visual Studio projesindeki tek C# bir dosya olarak Orchestration aşağıda verilmiştir:

> [!NOTE]
> Aşağıdaki örnek kodu çalıştırmak için `Microsoft.Azure.WebJobs.Extensions.Twilio` NuGet paketini yüklemeniz gerekir.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/PhoneVerification.cs)]

## <a name="next-steps"></a>Sonraki adımlar

Bu örnekte Dayanıklı İşlevler, özellikle `WaitForExternalEvent` ve `CreateTimer` API 'lerinin bazı gelişmiş özellikleri gösterilmiştir. Bunların güvenilir bir zaman aşımı sistemi uygulamak için `Task.WaitAny` ile nasıl birleştirilebileceği gördünüz. Bu, genellikle gerçek kişilerle etkileşim kurmak için kullanışlıdır. Belirli konuların ayrıntılı kapsamını sunan bir dizi makaleyi okuyarak Dayanıklı İşlevler kullanma hakkında daha fazla bilgi edinebilirsiniz.

> [!div class="nextstepaction"]
> [Serideki ilk makaleye git](durable-functions-bindings.md)
