---
title: Dayanıklı İşlevler-Azure Işlevlerinde HTTP özellikleri
description: Azure Işlevleri için Dayanıklı İşlevler uzantısı 'ndaki tümleşik HTTP özellikleri hakkında bilgi edinin.
author: cgillum
manager: gwallace
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: azfuncdf
ms.openlocfilehash: e9b2967905bc927432d1ca4606bc2b2ba2ac4108
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177369"
---
# <a name="http-features"></a>HTTP özellikleri

Dayanıklı İşlevler, dayanıklı düzenlemeleri ve varlıkları HTTP iş akışlarına eklemenizi kolaylaştıran çeşitli özelliklere sahiptir. Bu makale, bu özelliklerden bazıları hakkında ayrıntılara gider.

## <a name="exposing-http-apis"></a>HTTP API 'Lerini gösterme

Düzenlemeler ve varlıklar, HTTP istekleri kullanılarak çağrılabilir ve yönetilebilir. Dayanıklı İşlevler uzantısı yerleşik HTTP API 'Lerini kullanıma sunar. Ayrıca, HTTP ile tetiklenen işlevlerin içindeki düzenleyiciler ve varlıklarla etkileşim kurmak için API 'Ler sağlar.

### <a name="built-in-http-apis"></a>Yerleşik HTTP API 'Leri

Dayanıklı İşlevler uzantısı, Azure Işlevleri konağına otomatik olarak bir dizi HTTP API 'si ekler. Bu API 'lerle, herhangi bir kod yazmadan, düzenleme ve varlıklarla etkileşim kurabilir ve bunları yönetebilirsiniz.

Aşağıdaki yerleşik HTTP API 'Leri desteklenir.

* [Yeni düzenleme Başlat](durable-functions-http-api.md#start-orchestration)
* [Sorgu düzenleme örneği](durable-functions-http-api.md#get-instance-status)
* [Düzenleme örneğini Sonlandır](durable-functions-http-api.md#terminate-instance)
* [Bir Orchestration 'a dış olay gönderme](durable-functions-http-api.md#raise-event)
* [Düzenleme geçmişini temizle](durable-functions-http-api.md#purge-single-instance-history)
* [Bir varlığa işlem olayı gönderme](durable-functions-http-api.md#signal-entity)
* [Bir varlığın durumunu sorgulama](durable-functions-http-api.md#query-entity)

Dayanıklı İşlevler uzantısı tarafından kullanıma sunulan tüm yerleşik HTTP API 'Lerinin tam bir açıklaması için bkz. [http API 'leri makalesi](durable-functions-http-api.md) .

### <a name="http-api-url-discovery"></a>HTTP API URL keşfi

[Orchestration istemci bağlaması](durable-functions-bindings.md#orchestration-client) , uygun http yanıt yükleri oluşturabilen API 'leri kullanıma sunar. Örneğin, belirli bir düzenleme örneği için yönetim API 'Lerinin bağlantılarını içeren bir yanıt oluşturabilir. Aşağıdaki örneklerde, bu API 'nin yeni bir Orchestration örneği için nasıl kullanılacağını gösteren bir HTTP tetikleyici işlevi gösterilmektedir:

#### <a name="precompiled-c"></a>DerlemesiC#

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

#### <a name="c-script"></a>C# betiği

[!code-csharp[Main](~/samples-durable-functions/samples/csx/HttpStart/run.csx)]

#### <a name="javascript-with-functions-20-or-later-only"></a>Yalnızca 2,0 veya üzeri IŞLEVLERE sahip JavaScript

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

#### <a name="functionjson"></a>Function. JSON

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/function.json)]

Daha önce gösterilen HTTP-Trigger işlevlerini kullanarak bir Orchestrator işlevinin başlatılması, herhangi bir HTTP istemcisi kullanılarak yapılabilir. Aşağıdaki kıvrımlı komutu `DoWork` adlı bir Orchestrator işlevi başlatır:

```bash
curl -X POST https://localhost:7071/orchestrators/DoWork -H "Content-Length: 0" -i
```

Daha sonra, KIMLIĞI olarak `abc123` olan bir düzenleme için örnek bir yanıt örneğidir. Bazı ayrıntılar netlik açısından kaldırılmıştır.

```http
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Location: http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX
Retry-After: 10

{
    "id": "abc123",
    "purgeHistoryDeleteUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "sendEventPostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/raiseEvent/{eventName}?code=XXX",
    "statusQueryGetUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "terminatePostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/terminate?reason={text}&code=XXX"
}
```

Önceki örnekte, `Uri` ' da biten alanların her biri yerleşik bir HTTP API 'sine karşılık gelir. Hedef Orchestration örneğini yönetmek için bu API 'Leri kullanabilirsiniz.

> [!NOTE]
> Web kancası URL 'Lerinin biçimi, çalıştırdığınız Azure Işlevleri ana bilgisayarının sürümüne bağlıdır. Önceki örnek, Azure Işlevleri 2,0 konağına yöneliktir.

Tüm yerleşik HTTP API 'Lerinin açıklaması için bkz. [http API başvurusu](durable-functions-http-api.md).

### <a name="async-operation-tracking"></a>Zaman uyumsuz işlem izleme

Daha önce bahsedilen HTTP yanıtı, Dayanıklı İşlevler uzun süre çalışan HTTP zaman uyumsuz API 'Leri uygulamaya yardımcı olacak şekilde tasarlanmıştır. Bu model bazen *yoklama tüketici stili*olarak adlandırılır. İstemci/sunucu akışı aşağıdaki gibi çalışmaktadır:

1. İstemci bir Orchestrator işlevi gibi uzun süreli bir işlemi başlatmak için bir HTTP isteği yayınlar.
1. Hedef HTTP tetikleyicisi, "statusQueryGetUri" değerine sahip bir konum üst bilgisine sahip HTTP 202 yanıtı döndürüyor.
1. İstemci, konum üstbilgisindeki URL 'YI yoklar. İstemci, bir konum üstbilgisiyle HTTP 202 yanıtlarını görmeyi sürdürür.
1. Örnek tamamlandığında veya başarısız olduğunda, konum üstbilgisindeki uç nokta HTTP 200 döndürür.

Bu protokol, bir HTTP uç noktasını yoklayacak ve konum üst bilgisini izleyen dış istemcilerle veya hizmetlerle uzun süre çalışan işlemlerin koordinasyonunu sağlar. Bu düzenin istemci ve sunucu uygulamaları Dayanıklı İşlevler HTTP API 'Lerinde yerleşik olarak bulunur.

> [!NOTE]
> Varsayılan olarak, [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/) tarafından sunulan tüm HTTP tabanlı eylemler standart zaman uyumsuz işlem modelini destekler. Bu özellik, uzun süreli dayanıklı bir işlevi Logic Apps bir iş akışının parçası olarak katıştırmayı mümkün kılar. Zaman uyumsuz HTTP desenleri için Logic Apps desteği hakkında daha fazla ayrıntı için [Azure Logic Apps iş akışı eylemleri ve Tetikleyicileri belgelerine](../../logic-apps/logic-apps-workflow-actions-triggers.md)öğrenebilirsiniz.

> [!NOTE]
> Düzenleyiciyle etkileşimler yalnızca HTTP tarafından tetiklenen işlevlerden değil, herhangi bir işlev türünden yapılabilir.

İstemci API 'Leri kullanarak düzenleme ve varlıkların nasıl yönetileceği hakkında daha fazla bilgi için bkz. [örnek yönetimi makalesi](durable-functions-instance-management.md).

## <a name="consuming-http-apis"></a>HTTP API 'Lerini kullanma

Orchestrator [işlevi kod kısıtlamalarında](durable-functions-code-constraints.md)açıklandığı gibi, Orchestrator işlevleri doğrudan g/ç işlemleri yapamıyor. Bunun yerine, genellikle g/ç işlemlerini yapan [etkinlik işlevlerini](durable-functions-types-features-overview.md#activity-functions) çağırır.

Dayanıklı İşlevler 2,0 ' den başlayarak, [düzenleme, Orchestration tetikleyicisi bağlamasını](durable-functions-bindings.md#orchestration-trigger)kullanarak HTTP API 'lerini yerel olarak kullanabilir.

> [!NOTE]
> HTTP uç noktalarını doğrudan Orchestrator işlevlerinden çağırma özelliği JavaScript 'te henüz kullanılamıyor.

Aşağıdaki örnek kodda, **CallHttpAsync** .net C# API 'sini kullanarak giden http isteği oluşturan bir Orchestrator işlevi gösterilmektedir:

```csharp
[FunctionName("CheckSiteAvailable")]
public static async Task CheckSiteAvailable(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    Uri url = context.GetInput<Uri>();

    // Makes an HTTP GET request to the specified endpoint
    DurableHttpResponse response = 
        await context.CallHttpAsync(HttpMethod.Get, url);

    if (response.StatusCode >= 400)
    {
        // handling of error codes goes here
    }
}
```

"HTTP çağrısı" eylemini kullanarak Orchestrator işlevleriniz içinde aşağıdaki işlemleri yapabilirsiniz:

* Daha sonra bahsedilen bazı sınırlamalarla birlikte doğrudan düzenleme işlevlerinden HTTP API 'Lerini çağırın.
* İstemci tarafı HTTP 202 durum yoklama düzenlerini otomatik olarak destekler.
* Diğer Azure uç noktalarına yetkili HTTP çağrıları yapmak için [Azure yönetilen kimliklerini](../../active-directory/managed-identities-azure-resources/overview.md) kullanın.

HTTP API 'Lerini doğrudan Orchestrator işlevlerinden kullanma özelliği, belirli bir yaygın senaryolar kümesi için kolaylık olarak tasarlanmıştır. Bu özelliklerin tümünü etkinlik işlevlerini kullanarak kendiniz uygulayabilirsiniz. Birçok durumda, etkinlik işlevleri size daha fazla esneklik sağlayabilir.

### <a name="http-202-handling"></a>HTTP 202 işleme

"HTTP çağrısı" API 'SI, yoklama tüketici deseninin istemci tarafını otomatik olarak uygulayabilir. Çağrılan bir API, konum üst bilgisine sahip bir HTTP 202 yanıtı döndürürse, Orchestrator işlevi, 202 dışında bir yanıt alınana kadar konum kaynağını otomatik olarak yoklar. Bu yanıt Orchestrator işlev koduna döndürülen yanıt olacaktır.

> [!NOTE]
> Orchestrator işlevleri, [zaman uyumsuz işlem izleme](#async-operation-tracking)bölümünde açıklandığı gibi, sunucu tarafı yoklamayı tüketici modelini de yerel olarak destekler. Bu destek, bir işlev uygulamasındaki düzenlemeler diğer işlev uygulamalarındaki Orchestrator işlevlerini kolayca koordine edebileceğiniz anlamına gelir. Bu, [alt düzenleme](durable-functions-sub-orchestrations.md) kavramıyla benzerdir, ancak uygulamalar arası iletişime yönelik destek sağlar. Bu destek, mikro hizmet stili uygulama geliştirmesi için özellikle yararlıdır.

### <a name="managed-identities"></a>Yönetilen kimlikler

Dayanıklı İşlevler, yetkilendirme için Azure Active Directory (Azure AD) belirteçlerini kabul eden API 'Lerin çağrılarını yerel olarak destekler. Bu destek, bu belirteçleri almak için [Azure yönetilen kimliklerini](../../active-directory/managed-identities-azure-resources/overview.md) kullanır.

Aşağıdaki kod .NET Orchestrator işlevine bir örnektir. İşlevi, Azure Resource Manager [sanal makineler REST API](https://docs.microsoft.com/rest/api/compute/virtualmachines)kullanarak bir sanal makineyi yeniden başlatmak için kimliği doğrulanmış çağrılar yapar.

```csharp
[FunctionName("RestartVm")]
public static async Task RunOrchestrator(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string subscriptionId = "mySubId";
    string resourceGroup = "myRG";
    string vmName = "myVM";
    
    // Automatically fetches an Azure AD token for resource = https://management.core.windows.net
    // and attaches it to the outgoing Azure Resource Manager API call.
    var restartRequest = new DurableHttpRequest(
        HttpMethod.Post, 
        new Uri($"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Compute/virtualMachines/{vmName}/restart?api-version={apiVersion}"),
        tokenSource: new ManagedIdentityTokenSource("https://management.core.windows.net"));
    DurableHttpResponse restartResponse = await context.CallHttpAsync(restartRequest);
    if (restartResponse.StatusCode != HttpStatusCode.OK)
    {
        throw new ArgumentException($"Failed to restart VM: {restartResponse.StatusCode}: {restartResponse.Content}");
    }
}
```

Önceki örnekte `tokenSource` parametresi, [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md)IÇIN Azure AD belirteçlerini almak üzere yapılandırılmıştır. Belirteçler, kaynak URI 'SI `https://management.core.windows.net` tarafından tanımlanır. Örnek, geçerli işlev uygulamasının yerel olarak çalıştığını ya da yönetilen kimliğe sahip bir işlev uygulaması olarak dağıtıldığını varsayar. Yerel kimliğin veya yönetilen kimliğin belirtilen kaynak grubundaki VM 'Leri yönetme izni olduğu varsayılır `myRG`.

Çalışma zamanında, yapılandırılmış belirteç kaynağı otomatik olarak bir OAuth 2,0 erişim belirteci döndürür. Kaynak daha sonra, Giden isteğin yetkilendirme üstbilgisine bir taşıyıcı belirteci olarak belirteç ekler. Bu model, aşağıdaki nedenlerden dolayı HTTP isteklerine el ile Yetkilendirme üstbilgileri eklemenin bir geliştirmedir:

* Belirteç yenileme otomatik olarak işlenir. Süre dolma belirteçleri hakkında endişelenmeniz gerekmez.
* Belirteçler hiçbir şekilde dayanıklı düzenleme durumunda depolanmaz.
* Belirteç alımını yönetmek için herhangi bir kod yazmanız gerekmez.

[Önceden derlenmiş C# RestartVMs](https://github.com/Azure/azure-functions-durable-extension/blob/v2/samples/v2/precompiled/RestartVMs.cs)örneğinde daha kapsamlı bir örnek bulabilirsiniz.

Yönetilen kimlikler Azure Kaynak yönetimiyle sınırlı değildir. Microsoft ve Web Apps 'ten gelen Azure hizmetleri dahil olmak üzere Azure AD taşıyıcı belirteçlerini kabul eden herhangi bir API 'ye erişmek için Yönetilen kimlikler kullanabilirsiniz. Bir iş ortağının Web uygulaması başka bir işlev uygulaması bile olabilir. Microsoft 'un Azure AD ile kimlik doğrulamasını destekleyen Azure hizmetlerinin bir listesi için bkz. Azure [ad kimlik doğrulamasını destekleyen Azure hizmetleri](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

### <a name="limitations"></a>Sınırlamalar

HTTP API 'Lerini çağırmaya yönelik yerleşik destek, kullanışlı bir özelliktir. Tüm senaryolar için uygun değildir.

Orchestrator işlevleri tarafından gönderilen HTTP istekleri ve yanıtları kuyruk iletileri olarak serileştirilir ve kalıcıdır. Bu sıraya alma davranışı, [Orchestration Replay IÇIN http çağrılarının güvenilir ve güvenli](durable-functions-orchestrations.md#reliability)olmasını sağlar. Ancak, sıraya alma davranışının sınırlamaları da vardır:

* Her HTTP isteği, yerel bir HTTP istemcisiyle karşılaştırıldığında ek gecikme süresi içerir.
* Bir kuyruk iletisine sığamayacak olan büyük istek veya Yanıt iletileri, düzenleme performansını önemli ölçüde düşürebilir. İleti yüklerini blob depolamaya boşaltma yükü olası performans düşüşüne neden olabilir.
* Akış, öbekli ve ikili yükleri desteklenmez.
* HTTP istemcisinin davranışını özelleştirme yeteneği sınırlıdır.

Bu sınırlamaların herhangi biri kullanım durumunu etkileyebileceğinden, giden HTTP çağrıları yapmak için etkinlik işlevlerini ve dile özgü HTTP istemci kitaplıklarını kullanmayı göz önünde bulundurun.

> [!NOTE]
> .NET geliştiricisiyseniz, bu özelliğin, yerleşik .NET **HttpRequestMessage** ve **HttpResponseMessage** türleri yerine **Durablehttprequest** ve **durablehttpresponse** türlerini nasıl kullandığını merak ediyor olabilirsiniz.
>
> Bu tasarım seçeneği bilerek yapılır. Birincil neden, özel türlerin, kullanıcıların iç HTTP istemcisinin desteklenen davranışları hakkında yanlış varsayımlar olmamasını sağlamaya yardımcı olur. Dayanıklı İşlevler özgü türler Ayrıca API tasarımını basitleştirecek hale gelir. Ayrıca, [yönetilen kimlik tümleştirmesi](#managed-identities) ve [yoklama tüketicisi](#http-202-handling)gibi özel özellikleri daha kolay bir şekilde yapabilirler. 

### <a name="extensibility-net-only"></a>Genişletilebilirlik (yalnızca .NET)

Orchestration 'ın iç HTTP istemcisinin davranışını özelleştirmek, [Azure işlevleri .net bağımlılığı ekleme](https://docs.microsoft.com/azure/azure-functions/functions-dotnet-dependency-injection)kullanılarak yapılabilir. Bu özellik küçük davranışsal değişiklikler yapmak için yararlı olabilir. Ayrıca, ekleme sahte nesneler tarafından HTTP istemcisinin birim testi için de yararlı olabilir.

Aşağıdaki örnek, dış HTTP uç noktaları çağıran Orchestrator işlevleri için SSL sertifika doğrulamasını devre dışı bırakmak üzere bağımlılık ekleme 'nin kullanımını gösterir.

```csharp
public class Startup : FunctionsStartup
{
    public override void Configure(IFunctionsHostBuilder builder)
    {
        // Register own factory
        builder.Services.AddSingleton<
            IDurableHttpMessageHandlerFactory,
            MyDurableHttpMessageHandlerFactory>();
    }
}

public class MyDurableHttpMessageHandlerFactory : IDurableHttpMessageHandlerFactory
{
    public HttpMessageHandler CreateHttpMessageHandler()
    {
        // Disable SSL certificate validation (not recommended in production!)
        return new HttpClientHandler
        {
            ServerCertificateCustomValidationCallback =
                HttpClientHandler.DangerousAcceptAnyServerCertificateValidator,
        };
    }
}
```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Dayanıklı varlıklar hakkında bilgi edinin](durable-functions-entities.md)
