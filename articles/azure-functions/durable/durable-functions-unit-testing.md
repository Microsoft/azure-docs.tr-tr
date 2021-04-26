---
title: Azure Dayanıklı İşlevler birim testi
description: Birim testi Dayanıklı İşlevler nasıl yapılacağını öğrenin.
ms.topic: conceptual
ms.date: 11/03/2019
ms.openlocfilehash: fe5a25e0296eb183ef2426e12f7bdee35633ec78
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106076641"
---
# <a name="durable-functions-unit-testing"></a>Dayanıklı İşlevler birim testi

Birim testi modern yazılım geliştirme uygulamalarının önemli bir parçasıdır. Birim testleri, iş mantığı davranışını doğrular ve gelecekte önemli değişikliklerden kaçınmadan korur. Dayanıklı İşlevler, birim testlerinin önemli değişikliklerden kaçınmanıza yardımcı olacak şekilde karmaşıklığa kolayca büyüyebilir. Aşağıdaki bölümlerde,-Orchestration Client, Orchestrator ve Activity işlevlerinin üç işlev türünü test etme işlemi açıklanmaktadır.

> [!NOTE]
> Bu makalede, Dayanıklı İşlevler 2. x ' i hedefleyen Dayanıklı İşlevler uygulamalar için birim testi Kılavuzu sağlanmıştır. Sürümler arasındaki farklılıklar hakkında daha fazla bilgi için [dayanıklı işlevler sürümler](durable-functions-versions.md) makalesine bakın.

## <a name="prerequisites"></a>Önkoşullar

Bu makaledeki örneklerde aşağıdaki kavramlar ve çerçeveler hakkında bilgi sahibi olmanız gerekir:

* Birim testi

* Dayanıklı İşlevler

* [xUnit](https://github.com/xunit/xunit) -test çerçevesi

* [moq](https://github.com/moq/moq4) -Mocking çerçevesi

## <a name="base-classes-for-mocking"></a>Sahte işlem için temel sınıflar

Mocking aşağıdaki arabirim aracılığıyla desteklenir:

* [Idurableorchestrationclient](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.idurableorchestrationclient), [ıdurableentityclient](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.idurableentityclient) ve [ıdurableclient](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.idurableclient)

* [Idurableorchestrationcontext](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.idurableorchestrationcontext)

* [Idurableactivitycontext](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.idurableactivitycontext)
  
* [Idurableentitycontext](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.idurableentitycontext)

Bu arabirimler, Dayanıklı İşlevler tarafından desteklenen çeşitli tetikleyici ve bağlamalarla birlikte kullanılabilir. Azure Işlevlerinizi yürütürken, işlevler çalışma zamanı, işlev kodunuzu bu arabirimlerin somut bir uygulamasıyla çalıştırır. Birim testi için, iş mantığınızı test etmek üzere bu arabirimlerin bir moclenmiş sürümünü geçirebilirsiniz.

## <a name="unit-testing-trigger-functions"></a>Birim testi tetikleme işlevleri

Bu bölümde, birim testi yeni düzenlemeleri başlatmak için aşağıdaki HTTP tetikleme işlevinin mantığını doğrular.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

Birim testi görevi, `Retry-After` Yanıt yükünde belirtilen üstbilginin değerini doğrulamak olacaktır. Bu nedenle, birim testi `IDurableClient` tahmin edilebilir davranış sağlamak için bazı yöntemleri sahte hale getirebilir.

İlk olarak, şu şekilde bir sahte işlem çerçevesi (Bu durumda[moq](https://github.com/moq/moq4) ) kullanıyoruz `IDurableClient` :

```csharp
// Mock IDurableClient
var durableClientMock = new Mock<IDurableClient>();
```

> [!NOTE]
> Arabirimi doğrudan arabirimini bir sınıf olarak uygulayarak, sahte işlem çerçeveleri, işlemi çeşitli yollarla basitleştirir. Örneğin, alt sürümler arasında arabirime yeni bir yöntem eklenirse, moq somut uygulamalardan farklı olarak herhangi bir kod değişikliği gerektirmeyecektir.

Daha sonra `StartNewAsync` Yöntem, iyi bilinen bir örnek kimliği döndürecek şekilde yapılır.

```csharp
// Mock StartNewAsync method
durableClientMock.
    Setup(x => x.StartNewAsync(functionName, It.IsAny<object>())).
    ReturnsAsync(instanceId);
```

Sonraki adımda `CreateCheckStatusResponse` her zaman boş BIR HTTP 200 yanıtı döndürülür.

```csharp
// Mock CreateCheckStatusResponse method
durableClientMock
    // Notice that even though the HttpStart function does not call IDurableClient.CreateCheckStatusResponse() 
    // with the optional parameter returnInternalServerErrorOnFailure, moq requires the method to be set up
    // with each of the optional parameters provided. Simply use It.IsAny<> for each optional parameter
    .Setup(x => x.CreateCheckStatusResponse(It.IsAny<HttpRequestMessage>(), instanceId, returnInternalServerErrorOnFailure: It.IsAny<bool>())
    .Returns(new HttpResponseMessage
    {
        StatusCode = HttpStatusCode.OK,
        Content = new StringContent(string.Empty),
        Headers =
        {
            RetryAfter = new RetryConditionHeaderValue(TimeSpan.FromSeconds(10))
        }
    });
```

`ILogger` Ayrıca şu şekilde olur:

```csharp
// Mock ILogger
var loggerMock = new Mock<ILogger>();
```  

Artık, `Run` yöntemi birim testten çağrılır:

```csharp
// Call Orchestration trigger function
var result = await HttpStart.Run(
    new HttpRequestMessage()
    {
        Content = new StringContent("{}", Encoding.UTF8, "application/json"),
        RequestUri = new Uri("http://localhost:7071/orchestrators/E1_HelloSequence"),
    },
    durableClientMock.Object,
    functionName,
    loggerMock.Object);
 ```

 Son adım, çıktıyı beklenen değerle karşılaştırmaktır:

```csharp
// Validate that output is not null
Assert.NotNull(result.Headers.RetryAfter);

// Validate output's Retry-After header value
Assert.Equal(TimeSpan.FromSeconds(10), result.Headers.RetryAfter.Delta);
```

Tüm adımları birleştirdikten sonra birim testi aşağıdaki koda sahip olur:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HttpStartTests.cs)]

## <a name="unit-testing-orchestrator-functions"></a>Birim testi Orchestrator işlevleri

Genellikle daha fazla iş mantığı olduğundan, Orchestrator işlevleri birim testi için daha da ilginç.

Bu bölümde, birim testleri `E1_HelloSequence` Orchestrator işlevinin çıkışını doğrular:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

Birim test kodu, bir sahte oluşturmaya başlayacaktır:

```csharp
var durableOrchestrationContextMock = new Mock<IDurableOrchestrationContext>();
```

Ardından etkinlik yöntemi çağrıları yeniden yapılır:

```csharp
durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Tokyo")).ReturnsAsync("Hello Tokyo!");
durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Seattle")).ReturnsAsync("Hello Seattle!");
durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "London")).ReturnsAsync("Hello London!");
```

Birim testi daha sonra yöntemi çağıracaktır `HelloSequence.Run` :

```csharp
var result = await HelloSequence.Run(durableOrchestrationContextMock.Object);
```

Son olarak çıktının doğrulanması gerekir:

```csharp
Assert.Equal(3, result.Count);
Assert.Equal("Hello Tokyo!", result[0]);
Assert.Equal("Hello Seattle!", result[1]);
Assert.Equal("Hello London!", result[2]);
```

Tüm adımları birleştirdikten sonra birim testi aşağıdaki koda sahip olur:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceOrchestratorTests.cs)]

## <a name="unit-testing-activity-functions"></a>Birim testi etkinlik işlevleri

Etkinlik işlevleri, birim tarafından, dayanıklı olmayan işlevlerle aynı şekilde test edilebilir.

Bu bölümde, birim testi etkinlik işlevinin davranışını doğrulayacaktır `E1_SayHello` :

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

Ve birim testleri çıktının biçimini doğrular. Birim testleri doğrudan veya sahte sınıf parametre türlerini kullanabilir `IDurableActivityContext` :

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceActivityTests.cs)]

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [XUnit hakkında daha fazla bilgi](https://xunit.net/docs/getting-started/netcore/cmdline)
> 
> [Moq hakkında daha fazla bilgi](https://github.com/Moq/moq4/wiki/Quickstart)
