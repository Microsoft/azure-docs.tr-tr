---
title: Bir .NET uygulamasından günlük olayları oluşturma
description: Azure kümesinde veya tek başına kümede barındırılan .NET Service Fabric uygulamanıza günlük ekleme hakkında bilgi edinin.
ms.topic: conceptual
ms.date: 03/27/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: dd3afed85dc3eff81cbe60f252cdc16b0d4018c8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105628127"
---
# <a name="add-logging-to-your-service-fabric-application"></a>Service Fabric uygulamanıza günlük kaydı ekleme

Uygulamanız, sorun ortaya çıktığında bu sorunu gidermek için yeterli bilgi sağlamalıdır. Günlüğe kaydetme, Service Fabric uygulamanıza ekleyebileceğiniz en önemli işlemlerden biridir. Bir hata oluştuğunda, iyi günlüğe kaydetme, hataları araştırmak için bir yol sağlayabilir. Günlük düzenlerini çözümleyerek uygulamanızın performansını veya tasarımını geliştirmenin yollarını bulabilirsiniz. Bu belgede birkaç farklı günlüğe kaydetme seçeneği gösterilmektedir.

## <a name="eventflow"></a>EventFlow

[EventFlow Library](https://github.com/Azure/diagnostics-eventflow) Suite, uygulamaların hangi tanılama verilerini toplayacağınızı, hangilerinin de nereye yapılacağını tanımlamasına olanak tanır. Tanılama verileri performans sayaçlarından uygulama izlemelerinden herhangi bir şey olabilir. Uygulama ile aynı işlemde çalışır, bu nedenle iletişim yükü en aza indirilir. EventFlow ve Service Fabric hakkında daha fazla bilgi için, [EventFlow Ile Azure Service Fabric olay toplama](service-fabric-diagnostics-event-aggregation-eventflow.md)konusuna bakın.

### <a name="using-structured-eventsource-events"></a>Yapılandırılmış EventSource olaylarını kullanma

İleti olaylarının kullanım örneğine göre tanımlanması, olay ile ilgili verileri olay bağlamında paketlemenize olanak sağlar. Belirtilen olay özelliklerinin adlarına veya değerlerine göre daha kolay arama ve filtreleme yapabilirsiniz. İzleme çıkışını yapılandırmak, okumayı kolaylaştırır, ancak her kullanım örneği için bir olay tanımlamak için daha fazla düşünce ve zaman gerektirir. 

Bazı olay tanımları tüm uygulama genelinde paylaşılabilir. Örneğin, bir yöntem başlatma veya durdurma olayı, bir uygulama içindeki birçok hizmet arasında yeniden kullanılabilir. Bir sipariş sistemi gibi, etki alanına özgü bir hizmetin kendi benzersiz olayına sahip bir **CreateOrder** olayı olabilir. Bu yaklaşım çok sayıda olay oluşturabilir ve potansiyel olarak proje ekiplerinde tanımlayıcıların koordine edilmesini gerektirebilir. 

```csharp
[EventSource(Name = "MyCompany-VotingState-VotingStateService")]
internal sealed class ServiceEventSource : EventSource
{
    public static readonly ServiceEventSource Current = new ServiceEventSource();

    // The instance constructor is private to enforce singleton semantics.
    private ServiceEventSource() : base() { }

    ...

    // The ServiceTypeRegistered event contains a unique identifier, an event attribute that defined the event, and the code implementation of the event.
    private const int ServiceTypeRegisteredEventId = 3;
    [Event(ServiceTypeRegisteredEventId, Level = EventLevel.Informational, Message = "Service host process {0} registered service type {1}", Keywords = Keywords.ServiceInitialization)]
    public void ServiceTypeRegistered(int hostProcessId, string serviceType)
    {
        WriteEvent(ServiceTypeRegisteredEventId, hostProcessId, serviceType);
    }

    // The ServiceHostInitializationFailed event contains a unique identifier, an event attribute that defined the event, and the code implementation of the event.
    private const int ServiceHostInitializationFailedEventId = 4;
    [Event(ServiceHostInitializationFailedEventId, Level = EventLevel.Error, Message = "Service host initialization failed", Keywords = Keywords.ServiceInitialization)]
    public void ServiceHostInitializationFailed(string exception)
    {
        WriteEvent(ServiceHostInitializationFailedEventId, exception);
    }

    ...

```

### <a name="using-eventsource-generically"></a>EventSource kullanarak

Belirli olayların tanımlanması zor olabileceğinden, çoğu kişi genellikle bilgilerini bir dize olarak izleyen ortak bir parametre kümesiyle birkaç olay tanımlar. Yapılandırılmış en büyük değer kaybolur ve sonuçları aramak ve filtrelemek daha zordur. Bu yaklaşımda, genellikle günlük düzeylerine karşılık gelen birkaç olay tanımlanmıştır. Aşağıdaki kod parçacığı hata ayıklama ve hata iletisini tanımlar:

```csharp
[EventSource(Name = "MyCompany-VotingState-VotingStateService")]
internal sealed class ServiceEventSource : EventSource
{
    public static readonly ServiceEventSource Current = new ServiceEventSource();

    // The Instance constructor is private, to enforce singleton semantics.
    private ServiceEventSource() : base() { }

    ...

    private const int DebugEventId = 10;
    [Event(DebugEventId, Level = EventLevel.Verbose, Message = "{0}")]
    public void Debug(string msg)
    {
        WriteEvent(DebugEventId, msg);
    }

    private const int ErrorEventId = 11;
    [Event(ErrorEventId, Level = EventLevel.Error, Message = "Error: {0} - {1}")]
    public void Error(string error, string msg)
    {
        WriteEvent(ErrorEventId, error, msg);
    }

    ...

```

Yapılandırılmış ve genel bir izleme karma bir şekilde kullanılması iyi bir şekilde çalışabilir. Yapılandırılmış izleme hataları ve ölçümleri raporlamak için kullanılır. Genel olaylar, sorun giderme için mühendisler tarafından tüketilen ayrıntılı günlük kaydı için kullanılabilir.

## <a name="microsoftextensionslogging"></a>Microsoft.Extensions.Logging

ASP.NET Core günlüğü ([Microsoft. Extensions. Logging NuGet paketi](https://www.nuget.org/packages/Microsoft.Extensions.Logging)), uygulamanız için standart bir günlüğe kaydetme API 'si sağlayan bir günlük çerçevesidir. Diğer günlük arka uçları için destek, ASP.NET Core günlüğe takılabilir. Bu, çok sayıda kodu değiştirmek zorunda kalmadan, uygulamanızda günlüğe kaydetme için çok çeşitli destek sağlar.

1. İşaretlemek istediğiniz projeye **Microsoft. Extensions. Logging** NuGet paketini ekleyin. Ayrıca, tüm sağlayıcı paketlerini ekleyin. Daha fazla bilgi için bkz. [oturum açma ASP.NET Core](/aspnet/core/fundamentals/logging).
2. Hizmet dosyanıza **Microsoft. Extensions. Logging** için bir **using** yönergesi ekleyin.
3. Hizmet sınıfınız içinde özel bir değişken tanımlayın.

   ```csharp
   private ILogger _logger = null;
   ```

4. Hizmet sınıfınızın oluşturucusunda şu kodu ekleyin:

   ```csharp
   _logger = new LoggerFactory().CreateLogger<Stateless>();
   ```

5. Yöntemlerinizin içindeki kodunuzu işaretleme ile başlayın. İşte birkaç örnek:

   ```csharp
   _logger.LogDebug("Debug-level event from Microsoft.Logging");
   _logger.LogInformation("Informational-level event from Microsoft.Logging");

   // In this variant, we're adding structured properties RequestName and Duration, which have values MyRequest and the duration of the request.
   // Later in the article, we discuss why this step is useful.
   _logger.LogInformation("{RequestName} {Duration}", "MyRequest", requestDuration);
   ```

### <a name="using-other-logging-providers"></a>Diğer günlük sağlayıcılarını kullanma

Bazı üçüncü taraf sağlayıcılar, [Serilog](https://serilog.net/), [NLog](https://nlog-project.org/)ve [loggr](https://github.com/imobile3/Loggr.Extensions.Logging)gibi önceki bölümde açıklanan yaklaşımı kullanır. Bunların her birini ASP.NET Core günlüğe ekleyebilir veya bunları ayrı ayrı kullanabilirsiniz. Serilog, bir günlükçü tarafından gönderilen tüm iletileri zenginleştirir. Bu özellik, hizmet adı, tür ve bölüm bilgilerinin çıktısı için yararlı olabilir. ASP.NET Core altyapısında bu özelliği kullanmak için şu adımları uygulayın:

1. **Serilog**, **Serilog. Extensions. Logging**, **Serilog. havuzlar. Literate** ve **Serilog. havuzlar. Observable** NuGet paketlerini projeye ekleyin. 
2. `LoggerConfiguration`Ve günlükçü örneği oluşturun.

   ```csharp
   Log.Logger = new LoggerConfiguration().WriteTo.LiterateConsole().CreateLogger();
   ```

3. `Serilog.ILogger`Hizmet oluşturucusuna bir bağımsız değişken ekleyin ve yeni oluşturulan günlükçü 'yi geçirin.

   ```csharp
   ServiceRuntime.RegisterServiceAsync("StatelessType", context => new Stateless(context, Log.Logger)).GetAwaiter().GetResult();
   ```

4. Hizmet oluşturucusunda, **ServiceTypeName**, **ServiceName**, **PartitionID** ve **InstanceId** için özellik zenginler oluşturur.

   ```csharp
   public Stateless(StatelessServiceContext context, Serilog.ILogger serilog)
       : base(context)
   {
       PropertyEnricher[] properties = new PropertyEnricher[]
       {
           new PropertyEnricher("ServiceTypeName", context.ServiceTypeName),
           new PropertyEnricher("ServiceName", context.ServiceName),
           new PropertyEnricher("PartitionId", context.PartitionId),
           new PropertyEnricher("InstanceId", context.ReplicaOrInstanceId),
       };

       serilog.ForContext(properties);

       _logger = new LoggerFactory().AddSerilog(serilog.ForContext(properties)).CreateLogger<Stateless>();
   }
   ```

5. Kodu, Serilog olmadan kullanırken ASP.NET Core.

   >[!NOTE]
   >Önceki örnekle statik olarak *kullanmanız önerilmez* `Log.Logger` . Service Fabric, tek bir işlem içinde aynı hizmet türünün birden çok örneğini barındırabilir. Statik `Log.Logger` ' ı kullanırsanız, özellik sorgularının son yazarı, çalıştıran tüm örneklerin değerlerini gösterir. Bu, _logger değişkeninin hizmet sınıfının özel bir üye değişkeni olmasının bir nedenidir. Ayrıca, `_logger` Hizmetler genelinde kullanılabilecek ortak kod için kullanılabilir yapmanız gerekir.

## <a name="next-steps"></a>Sonraki adımlar

- [Service Fabric 'de uygulama izleme](service-fabric-diagnostics-event-generation-app.md)hakkında daha fazla bilgi edinin.
- [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md) ve [Windows Azure tanılama](service-fabric-diagnostics-event-aggregation-wad.md)ile günlüğe kaydetme hakkında bilgi edinin.
