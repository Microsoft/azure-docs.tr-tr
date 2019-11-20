---
title: Azure Application Insights .NET SDK ile özel işlemleri izleme | Microsoft Docs
description: Azure Application Insights .NET SDK ile özel işlemleri izleme
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 06/30/2017
ms.reviewer: sergkanz
ms.openlocfilehash: f05c8724fe87888c93230b4ca77a7a82fe9357c2
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72677461"
---
# <a name="track-custom-operations-with-application-insights-net-sdk"></a>.NET SDK Application Insights özel işlemleri izleme

Azure Application Insights SDK 'Ları, gelen HTTP isteklerini ve HTTP istekleri ve SQL sorguları gibi bağımlı hizmetlere yapılan çağrıları otomatik olarak izler. İsteklerin ve bağımlılıkların izlenmesi ve bağıntısı, uygulamanın bu uygulamayı birleştiren tüm mikro hizmetlerde yanıt verme ve güvenilirlik hakkında görünürlük sağlar. 

Genel olarak desteklenebilir uygulama desenlerinin bir sınıfı vardır. Bu desenleri doğru şekilde izlemek el ile kod izleme gerektirir. Bu makalede, özel kuyruk işleme ve uzun süreli arka plan görevlerini çalıştırma gibi el ile izleme gerektirebilecek birkaç desen ele alınmaktadır.

Bu belge, Application Insights SDK ile özel işlemlerin nasıl izleneceği hakkında rehberlik sağlar. Bu belge için geçerlidir:

- .NET için Application Insights (temel SDK olarak da bilinir) 2,4 + sürümü.
- Web uygulamaları için Application Insights (çalışan ASP.NET) 2.4 + sürüm.
- ASP.NET Core sürüm 2.1 + için Application Insights.

## <a name="overview"></a>Genel Bakış
İşlem, bir uygulama tarafından çalıştırılan mantıksal bir iş parçasıdır. Ad, başlangıç saati, süre, sonuç ve Kullanıcı adı, Özellikler ve sonuç gibi yürütme bağlamı vardır. İşlem A, B işlemi tarafından başlatılmışsa, B işlemi bir için üst öğe olarak ayarlanır. Bir işlemin yalnızca bir üst öğesi olabilir, ancak birçok alt işlemi olabilir. İşlemler ve telemetri bağıntısı hakkında daha fazla bilgi için bkz. [Azure Application Insights telemetri bağıntısı](correlation.md).

Application Insights .NET SDK 'sında, işlem soyut sınıf [Operationtelemetri](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Microsoft.ApplicationInsights/Extensibility/Implementation/OperationTelemetry.cs) ve bunların alt öğeleri [Requesttelemetri](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Microsoft.ApplicationInsights/DataContracts/RequestTelemetry.cs) ve [dependencytelemetri](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Microsoft.ApplicationInsights/DataContracts/DependencyTelemetry.cs)ile açıklanmıştır.

## <a name="incoming-operations-tracking"></a>Gelen işlemler izleniyor 
Application Insights Web SDK, bir IIS işlem hattında ve tüm ASP.NET Core uygulamalarda çalışan ASP.NET uygulamaları için HTTP isteklerini otomatik olarak toplar. Diğer platformlar ve çerçeveler için topluluk tarafından desteklenen çözümler vardır. Ancak, uygulama standart veya topluluk tarafından desteklenen herhangi bir çözüm tarafından desteklenmiyorsa, el ile bu çözümü kullanabilirsiniz.

Özel izleme gerektiren başka bir örnek, kuyruktan öğe alan çalışandır. Bazı sıralarda, bu kuyruğa ileti ekleme çağrısı bağımlılık olarak izlenir. Ancak, ileti işlemeyi açıklayan üst düzey işlem otomatik olarak toplanmaz.

Bu tür işlemlerin nasıl izleneceğini görelim.

Yüksek düzeyde, görev `RequestTelemetry` oluşturmak ve bilinen özellikleri ayarlamak. İşlem tamamlandıktan sonra Telemetriyi izlersiniz. Aşağıdaki örnek bu görevi gösterir.

### <a name="http-request-in-owin-self-hosted-app"></a>Owın self-hosted uygulamasındaki HTTP isteği
Bu örnekte, izleme bağlamı [bağıntı Için HTTP protokolüne](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md)göre yayılır. Burada açıklanan üst bilgileri almayı beklemeniz gerekir.

```csharp
public class ApplicationInsightsMiddleware : OwinMiddleware
{
    // you may create a new TelemetryConfiguration instance, reuse one you already have
    // or fetch the instance created by Application Insights SDK.
    private readonly TelemetryConfiguration telemetryConfiguration = TelemetryConfiguration.CreateDefault();
    private readonly TelemetryClient telemetryClient = new TelemetryClient(telemetryConfiguration);
    
    public ApplicationInsightsMiddleware(OwinMiddleware next) : base(next) {}

    public override async Task Invoke(IOwinContext context)
    {
        // Let's create and start RequestTelemetry.
        var requestTelemetry = new RequestTelemetry
        {
            Name = $"{context.Request.Method} {context.Request.Uri.GetLeftPart(UriPartial.Path)}"
        };

        // If there is a Request-Id received from the upstream service, set the telemetry context accordingly.
        if (context.Request.Headers.ContainsKey("Request-Id"))
        {
            var requestId = context.Request.Headers.Get("Request-Id");
            // Get the operation ID from the Request-Id (if you follow the HTTP Protocol for Correlation).
            requestTelemetry.Context.Operation.Id = GetOperationId(requestId);
            requestTelemetry.Context.Operation.ParentId = requestId;
        }

        // StartOperation is a helper method that allows correlation of 
        // current operations with nested operations/telemetry
        // and initializes start time and duration on telemetry items.
        var operation = telemetryClient.StartOperation(requestTelemetry);

        // Process the request.
        try
        {
            await Next.Invoke(context);
        }
        catch (Exception e)
        {
            requestTelemetry.Success = false;
            telemetryClient.TrackException(e);
            throw;
        }
        finally
        {
            // Update status code and success as appropriate.
            if (context.Response != null)
            {
                requestTelemetry.ResponseCode = context.Response.StatusCode.ToString();
                requestTelemetry.Success = context.Response.StatusCode >= 200 && context.Response.StatusCode <= 299;
            }
            else
            {
                requestTelemetry.Success = false;
            }

            // Now it's time to stop the operation (and track telemetry).
            telemetryClient.StopOperation(operation);
        }
    }
    
    public static string GetOperationId(string id)
    {
        // Returns the root ID from the '|' to the first '.' if any.
        int rootEnd = id.IndexOf('.');
        if (rootEnd < 0)
            rootEnd = id.Length;

        int rootStart = id[0] == '|' ? 1 : 0;
        return id.Substring(rootStart, rootEnd - rootStart);
    }
}
```

Bağıntı için HTTP protokolü Ayrıca `Correlation-Context` üst bilgisini bildirir. Ancak, bu, kolaylık sağlaması için burada atılır.

## <a name="queue-instrumentation"></a>Sıra izleme
Korelasyon ayrıntılarının HTTP isteğiyle geçirilmesi için [W3C Izleme bağlamı](https://www.w3.org/TR/trace-context/) ve [http Protokolü](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md) mevcut olsa da, her kuyruk protokolünün sıra iletisinde aynı ayrıntıların nasıl geçtiğini tanımlamanız gerekir. Bazı kuyruk protokolleri (AMQP gibi) ek meta verilerin geçirilmesine izin verir (örneğin, Azure depolama kuyruğu), içeriğin ileti yüküne kodlanmasını gerektirir.

> [!NOTE]
> * **Hala sıralarda çapraz bileşen izleme desteklenmez** HTTP ile, üretici ve tüketici farklı Application Insights kaynaklarına telemetri gönderiyor, Işlem tanılama deneyimi ve uygulama Haritası işlemleri gösterir ve uçtan uca eşler. Kuyruklar söz konusu olduğunda henüz desteklenmez. 

### <a name="service-bus-queue"></a>Service Bus Kuyruğu
Application Insights .NET sürüm 3.0.0 ve üzeri için yeni [Microsoft Azure ServiceBus istemcisi](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus/) Ile Service Bus mesajlaşma çağrılarını izler.
İletileri işlemek için [ileti işleyici modelini](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler) kullanırsanız, işiniz bitti: hizmetiniz tarafından gerçekleştirilen tüm Service Bus çağrıları otomatik olarak izlenir ve diğer telemetri öğeleriyle bağıntılı yapılır. İletileri el ile işlemek için [Microsoft Application Insights ile Service Bus istemci izlemeye](../../service-bus-messaging/service-bus-end-to-end-tracing.md) bakın.

[Windowsazure. ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) paketini kullanırsanız, Service Bus kuyruğu AMQP protokolünü kullandığından Application Insights otomatik olarak kuyruğu izlemediğini ve Service Bus yapılan çağrıların nasıl izleneceğini (ve ilişkilendirilacağını) gösterir. operasyonları.
Bağıntı tanımlayıcıları ileti özelliklerine geçirilir.

#### <a name="enqueue"></a>Alma

```csharp
public async Task Enqueue(string payload)
{
    // StartOperation is a helper method that initializes the telemetry item
    // and allows correlation of this operation with its parent and children.
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("enqueue " + queueName);
    
    operation.Telemetry.Type = "Azure Service Bus";
    operation.Telemetry.Data = "Enqueue " + queueName;

    var message = new BrokeredMessage(payload);
    // Service Bus queue allows the property bag to pass along with the message.
    // We will use them to pass our correlation identifiers (and other context)
    // to the consumer.
    message.Properties.Add("ParentId", operation.Telemetry.Id);
    message.Properties.Add("RootId", operation.Telemetry.Context.Operation.Id);

    try
    {
        await queue.SendAsync(message);
        
        // Set operation.Telemetry Success and ResponseCode here.
        operation.Telemetry.Success = true;
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        // Set operation.Telemetry Success and ResponseCode here.
        operation.Telemetry.Success = false;
        throw;
    }
    finally
    {
        telemetryClient.StopOperation(operation);
    }
}
```

#### <a name="process"></a>Süreç
```csharp
public async Task Process(BrokeredMessage message)
{
    // After the message is taken from the queue, create RequestTelemetry to track its processing.
    // It might also make sense to get the name from the message.
    RequestTelemetry requestTelemetry = new RequestTelemetry { Name = "process " + queueName };

    var rootId = message.Properties["RootId"].ToString();
    var parentId = message.Properties["ParentId"].ToString();
    // Get the operation ID from the Request-Id (if you follow the HTTP Protocol for Correlation).
    requestTelemetry.Context.Operation.Id = rootId;
    requestTelemetry.Context.Operation.ParentId = parentId;

    var operation = telemetryClient.StartOperation(requestTelemetry);

    try
    {
        await ProcessMessage();
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        throw;
    }
    finally
    {
        // Update status code and success as appropriate.
        telemetryClient.StopOperation(operation);
    }
}
```

### <a name="azure-storage-queue"></a>Azure depolama kuyruğu
Aşağıdaki örnek, [Azure depolama kuyruğu](../../storage/queues/storage-dotnet-how-to-use-queues.md) işlemlerinin nasıl izleneceğini ve üretici, tüketici ve Azure depolama arasındaki Telemetriyi nasıl kullanabileceğinizi gösterir. 

Depolama sırasının bir HTTP API 'SI vardır. Kuyruğa yapılan tüm çağrılar, HTTP istekleri için Application Insights bağımlılık toplayıcısı tarafından izlenir.
ASP.NET ve ASP.NET Core uygulamalarında varsayılan olarak yapılandırılır, diğer uygulama türleri ile [konsol uygulamaları belgelerine](../../azure-monitor/app/console.md) başvurabilirsiniz

Ayrıca, Application Insights işlem KIMLIĞINI depolama istek KIMLIĞIYLE ilişkilendirmek isteyebilirsiniz. Depolama isteği istemcisi ve sunucu istek KIMLIĞI ayarlama ve alma hakkında daha fazla bilgi için bkz. [Azure depolama 'Yı izleme, tanılama ve sorun giderme](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#end-to-end-tracing).

#### <a name="enqueue"></a>Alma
Depolama kuyrukları HTTP API 'sini desteklediği için, kuyruğa sahip tüm işlemler Application Insights tarafından otomatik olarak izlenir. Çoğu durumda bu izleme yeterince olmalıdır. Ancak, müşteri tarafında bulunan izlemeleri üretici izlemelerle ilişkilendirmek için, bağıntı için HTTP protokolünde bunu yaptığımız şekilde bir bağıntı bağlamı geçirmeniz gerekir. 

Bu örnek, `Enqueue` işleminin nasıl izleneceğini gösterir. Yapabilecekleriniz:

 - **Yeniden denemeler bağıntılı (varsa)** : hepsi `Enqueue` işlem olan bir ortak üst öğeye sahiptir. Aksi takdirde, bunlar gelen isteğin alt öğesi olarak izlenir. Sıraya yönelik birden çok mantıksal istek varsa, hangi çağrının yeniden denenmesine neden olduğunu bulmak zor olabilir.
 - **Depolama günlüklerini ilişkilendirme (gerekiyorsa ve gerekirse)** : Bunlar Application Insights telemetri ile bağıntılı.

@No__t_0 işlemi bir üst işlemin alt öğesidir (örneğin, gelen HTTP isteği). HTTP bağımlılık çağrısı, `Enqueue` işleminin alt öğesidir ve gelen isteğin alt öğesidir:

```csharp
public async Task Enqueue(CloudQueue queue, string message)
{
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("enqueue " + queue.Name);
    operation.Telemetry.Type = "Azure queue";
    operation.Telemetry.Data = "Enqueue " + queue.Name;

    // MessagePayload represents your custom message and also serializes correlation identifiers into payload.
    // For example, if you choose to pass payload serialized to JSON, it might look like
    // {'RootId' : 'some-id', 'ParentId' : '|some-id.1.2.3.', 'message' : 'your message to process'}
    var jsonPayload = JsonConvert.SerializeObject(new MessagePayload
    {
        RootId = operation.Telemetry.Context.Operation.Id,
        ParentId = operation.Telemetry.Id,
        Payload = message
    });
    
    CloudQueueMessage queueMessage = new CloudQueueMessage(jsonPayload);

    // Add operation.Telemetry.Id to the OperationContext to correlate Storage logs and Application Insights telemetry.
    OperationContext context = new OperationContext { ClientRequestID = operation.Telemetry.Id};

    try
    {
        await queue.AddMessageAsync(queueMessage, null, null, new QueueRequestOptions(), context);
    }
    catch (StorageException e)
    {
        operation.Telemetry.Properties.Add("AzureServiceRequestID", e.RequestInformation.ServiceRequestID);
        operation.Telemetry.Success = false;
        operation.Telemetry.ResultCode = e.RequestInformation.HttpStatusCode.ToString();
        telemetryClient.TrackException(e);
    }
    finally
    {
        // Update status code and success as appropriate.
        telemetryClient.StopOperation(operation);
    }
}  
```

Uygulama raporlarınızdaki telemetri miktarını azaltmak için veya `Enqueue` işlemini diğer nedenlerle izlemek istemiyorsanız, doğrudan `Activity` API 'sini kullanın:

- Application Insights işlemini başlatmak yerine yeni bir `Activity` oluşturun (ve başlatın). İşlem adı dışında buna herhangi bir özellik *atamanız gerekmez.*
- @No__t_0 `operation.Telemetry.Id` yerine ileti yüküne seri hale getirme. @No__t_0 de kullanabilirsiniz.


#### <a name="dequeue"></a>Sıradan çıkarma
@No__t_0 benzer şekilde, depolama kuyruğuna yapılan gerçek bir HTTP isteği Application Insights tarafından otomatik olarak izlenir. Ancak, `Enqueue` işlem üst bağlamda (gelen istek bağlamı gibi) ortaya çıkar. Application Insights SDK 'lar, bu tür bir işlem (ve HTTP bölümü) üst istek ve aynı kapsamda bildirilen diğer telemetri ile otomatik olarak ilişkilendirilecektir.

@No__t_0 işlemi karmaşık bir işlemdir. Application Insights SDK, HTTP isteklerini otomatik olarak izler. Ancak, ileti Ayrıştırılana kadar bağıntı bağlamını bilmez. Özellikle birden fazla ileti alındığında, Telemetriyi geri kalanı ile iletiyi almak için HTTP isteğini ilişkilendirmek mümkün değildir.

```csharp
public async Task<MessagePayload> Dequeue(CloudQueue queue)
{
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("dequeue " + queue.Name);
    operation.Telemetry.Type = "Azure queue";
    operation.Telemetry.Data = "Dequeue " + queue.Name;
    
    try
    {
        var message = await queue.GetMessageAsync();
    }
    catch (StorageException e)
    {
        telemetry.Properties.Add("AzureServiceRequestID", e.RequestInformation.ServiceRequestID);
        telemetry.Success = false;
        telemetry.ResultCode = e.RequestInformation.HttpStatusCode.ToString();
        telemetryClient.TrackException(e);
    }
    finally
    {
        // Update status code and success as appropriate.
        telemetryClient.StopOperation(operation);
    }

    return null;
}
```

#### <a name="process"></a>Süreç

Aşağıdaki örnekte gelen bir ileti, gelen HTTP isteğine benzer şekilde izlenir:

```csharp
public async Task Process(MessagePayload message)
{
    // After the message is dequeued from the queue, create RequestTelemetry to track its processing.
    RequestTelemetry requestTelemetry = new RequestTelemetry { Name = "process " + queueName };
    
    // It might also make sense to get the name from the message.
    requestTelemetry.Context.Operation.Id = message.RootId;
    requestTelemetry.Context.Operation.ParentId = message.ParentId;

    var operation = telemetryClient.StartOperation(requestTelemetry);

    try
    {
        await ProcessMessage();
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        throw;
    }
    finally
    {
        // Update status code and success as appropriate.
        telemetryClient.StopOperation(operation);
    }
}
```

Benzer şekilde, diğer kuyruk işlemleri de görüntülenebilir. Bir göz atma işlemi, bir sıradan çıkarma işlemi olarak benzer bir şekilde seçilmelidir. Sıra yönetimi işlemlerini işaretleme gerekli değildir. Application Insights HTTP gibi işlemleri izler ve çoğu durumda, bu yeterlidir.

İleti silme işlemini kaldırdığınızda, işlem (bağıntı) tanımlayıcılarını ayarladığınızdan emin olun. Alternatif olarak, `Activity` API 'sini de kullanabilirsiniz. Daha sonra, Application Insights SDK bunu sizin için yaptığından telemetri öğelerinde işlem tanımlayıcıları ayarlamanız gerekmez:

- Kuyruktan bir öğe aldıktan sonra yeni bir `Activity` oluşturun.
- Tüketici ve üretici günlüklerinin ilişkilendirilmesi için `Activity.SetParentId(message.ParentId)` kullanın.
- @No__t_0 başlatın.
- @No__t_0 yardımcıları kullanarak sıradan çıkarma, işleme ve silme işlemlerini izleyin. Aynı zaman uyumsuz denetim akışından (yürütme bağlamı) bunu yapın. Bu şekilde, bunlar doğru şekilde bağıntılı.
- @No__t_0 durdurun.
- @No__t_0 kullanın veya `Track` telemetrisini el ile çağırın.

### <a name="dependency-types"></a>Bağımlılık türleri

Application Insights UI deneyimlerini eklemek için bağımlılık türünü kullanır. Kuyruklar için, [işlem tanılama deneyimini](/azure/azure-monitor/app/transaction-diagnostics)geliştiren aşağıdaki `DependencyTelemetry` türlerini tanır:
- Azure depolama kuyrukları için `Azure queue`
- Azure Event Hubs `Azure Event Hubs`
- Azure Service Bus için `Azure Service Bus`

### <a name="batch-processing"></a>Toplu işlem
Bazı kuyruklarla birden çok iletiyi bir istek ile sıradan silebilirsiniz. Bu tür iletileri işlemek, büyük olasılıkla bağımsızdır ve farklı mantıksal işlemlere aittir. @No__t_0 işleminin işlenmekte olan belirli bir iletiyle ilişkilendirilmesi mümkün değildir.

Her ileti kendi zaman uyumsuz Denetim akışında işlenmelidir. Daha fazla bilgi için [giden bağımlılıklar izleme](#outgoing-dependencies-tracking) bölümüne bakın.

## <a name="long-running-background-tasks"></a>Uzun süre çalışan arka plan görevleri

Bazı uygulamalar, kullanıcı isteklerinin neden olabileceği uzun süre çalışan işlemler başlatır. İzleme/izleme perspektifinden, istek veya bağımlılık araçlarından farklı değildir: 

```csharp
async Task BackgroundTask()
{
    var operation = telemetryClient.StartOperation<DependencyTelemetry>(taskName);
    operation.Telemetry.Type = "Background";
    try
    {
        int progress = 0;
        while (progress < 100)
        {
            // Process the task.
            telemetryClient.TrackTrace($"done {progress++}%");
        }
        // Update status code and success as appropriate.
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        // Update status code and success as appropriate.
        throw;
    }
    finally
    {
        telemetryClient.StopOperation(operation);
    }
}
```

Bu örnekte, `telemetryClient.StartOperation` `DependencyTelemetry` oluşturur ve bağıntı bağlamını doldurur. İşlemi zamanladığı gelen istekler tarafından oluşturulan bir üst işlem olduğunu varsayalım. @No__t_0, gelen istek olarak aynı zaman uyumsuz Denetim akışında başladığı sürece, bu üst işlemle bağıntılı olur. `BackgroundTask` ve tüm iç içe geçmiş telemetri öğeleri, istek bittikten sonra bile, hataya neden olan istekle otomatik olarak bağıntılı olur.

Görev, kendisiyle ilişkilendirilmiş herhangi bir işlem (`Activity`) olmayan arka plan iş parçacığından başlatıldığında, hiçbir üst öğeye sahip olmaz `BackgroundTask`. Ancak, iç içe geçmiş işlemlere sahip olabilir. Görevden bildirilen tüm telemetri öğeleri, `BackgroundTask` oluşturulan `DependencyTelemetry` bağıntılı.

## <a name="outgoing-dependencies-tracking"></a>Giden bağımlılıkları izleme
Kendi bağımlılık çeşidini veya Application Insights tarafından desteklenmeyen bir işlemi izleyebilirsiniz.

Service Bus sırasındaki veya depolama kuyruğundaki `Enqueue` yöntemi bu özel izleme için örnek olarak kullanılabilir.

Özel bağımlılık izleme için genel yaklaşım şunlardır:

- Bağıntı ve diğer bazı özellikler (başlangıç zamanı damgası, süre) için gereken `DependencyTelemetry` özelliklerini dolduran `TelemetryClient.StartOperation` (uzantı) yöntemini çağırın.
- @No__t_0 adı ve ihtiyacınız olan diğer herhangi bir bağlam gibi diğer özel özellikleri ayarlayın.
- Bağımlılık çağrısı yapın ve bekleyin.
- İşlem bittiğinde `StopOperation` işlemi durdurun.
- Özel durumları işleyin.

```csharp
public async Task RunMyTaskAsync()
{
    using (var operation = telemetryClient.StartOperation<DependencyTelemetry>("task 1"))
    {
        try 
        {
            var myTask = await StartMyTaskAsync();
            // Update status code and success as appropriate.
        }
        catch(...) 
        {
            // Update status code and success as appropriate.
        }
    }
}
```

Disposing işlemi, işlemin durdurulmasına neden olur, bu nedenle `StopOperation` çağırmak yerine bunu yapabilirsiniz.

*Uyarı*: bazı durumlarda, kaldırma özel [durumu `finally`,](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/try-finally) işlemler izlenmeyebilir şekilde çağrılabilir.

### <a name="parallel-operations-processing-and-tracking"></a>Paralel işlemler işleme ve izleme

`StopOperation` yalnızca başlatılmış olan işlemi sonlandırır. Geçerli çalışan işlem durdurmak istediğiniz bir ile eşleşmiyorsa, `StopOperation` hiçbir şey yapmaz. Aynı yürütme bağlamında paralel olarak birden çok işlem başlatırsanız bu durum oluşabilir:

```csharp
var firstOperation = telemetryClient.StartOperation<DependencyTelemetry>("task 1");
var firstTask = RunMyTaskAsync();

var secondOperation = telemetryClient.StartOperation<DependencyTelemetry>("task 2");
var secondTask = RunMyTaskAsync();

await firstTask;

// FAILURE!!! This will do nothing and will not report telemetry for the first operation
// as currently secondOperation is active.
telemetryClient.StopOperation(firstOperation); 

await secondTask;
```

Paralel olarak çalışan işlemleri yalıtmak için aynı zaman **uyumsuz** yöntemde `StartOperation` ve işlem işlemini her zaman çağırdığınızdan emin olun. İşlem zaman uyumlu (veya zaman uyumsuz) ise, işlemi kaydırın ve `Task.Run` izleyin:

```csharp
public void RunMyTask(string name)
{
    using (var operation = telemetryClient.StartOperation<DependencyTelemetry>(name))
    {
        Process();
        // Update status code and success as appropriate.
    }
}

public async Task RunAllTasks()
{
    var task1 = Task.Run(() => RunMyTask("task 1"));
    var task2 = Task.Run(() => RunMyTask("task 2"));
    
    await Task.WhenAll(task1, task2);
}
```

## <a name="applicationinsights-operations-vs-systemdiagnosticsactivity"></a>ApplicationInsights işlemleri vs System. Diagnostics. Activity
`System.Diagnostics.Activity` dağıtılmış izleme bağlamını temsil eder ve çerçeve ve kitaplıklar tarafından işlemin içinde ve dışında bağlam oluşturmak ve dağıtmak ve telemetri öğelerini ilişkilendirmek için kullanılır. Etkinlik, ilginç olaylar (gelen veya giden istekler, özel durumlar, vb.) hakkında bilgilendirmek için çerçeve/kitaplık arasındaki bildirim mekanizmasıyla birlikte `System.Diagnostics.DiagnosticSource`.

Etkinlikler ilk sınıf vatandaşları Application Insights ve otomatik bağımlılık ve istek toplama işlemleri, `DiagnosticSource` olayları ile birlikte büyük ölçüde dayanır. Uygulamanızda etkinlik oluşturursanız, Application Insights telemetrinin oluşturulması neden olmaz. Application Insights, DiagnosticSource olaylarını alması ve etkinliğin telemetrisine çevrilmesi için olay adlarını ve yüklerini bilmesi gerekir.

Her bir Application Insights işlemi (istek veya bağımlılık) `Activity` içerir-`StartOperation` çağrıldığında, altında etkinlik oluşturur. `StartOperation`, istek veya bağımlılık Telemetriler el ile izlemenin ve her şeyin bağıntılı olduğundan emin olmak için önerilen yoldur.

## <a name="next-steps"></a>Sonraki adımlar

- Application Insights [telemetri bağıntısı](correlation.md) hakkında temel bilgileri öğrenin.
- Bağıntılı verilerin [Işlem tanılama deneyimini](../../azure-monitor/app/transaction-diagnostics.md) ve [uygulama haritasını](../../azure-monitor/app/app-map.md)nasıl kuvvetlerine göz atın.
- Application Insights türleri ve veri modeli için [veri modeline](../../azure-monitor/app/data-model.md) bakın.
- Application Insights için özel [olayları ve ölçümleri](../../azure-monitor/app/api-custom-events-metrics.md) bildirin.
- Bağlam özellikleri koleksiyonu için standart [yapılandırmayı](configuration-with-applicationinsights-config.md#telemetry-initializers-aspnet) inceleyin.
- Telemetriyi nasıl ilişkilendirdiğimiz hakkında bilgi için [System. Diagnostics. Activity Kullanıcı kılavuzunu](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) denetleyin.
