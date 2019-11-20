---
title: Web Işleri SDK 'sını kullanma-Azure
description: Web Işleri SDK 'Sı için kod yazma hakkında daha fazla bilgi edinin. Azure hizmetleri ve üçüncü taraf hizmetlerindeki verilere erişen olay odaklı arka plan işleme işleri oluşturun.
services: app-service\web, storage
documentationcenter: .net
author: ggailey777
manager: jeconnoc
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/18/2019
ms.author: glenga
ms.openlocfilehash: 67cd7f82597d306c8bf3c463d11457199aec7277
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71815735"
---
# <a name="how-to-use-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>Olay odaklı arka plan işleme için Azure Web Işleri SDK 'sını kullanma

Bu makalede, Azure WebJobs SDK ile çalışmaya yönelik yönergeler sunulmaktadır. WebJobs 'ı hemen kullanmaya başlamak için bkz. [olay odaklı arka plan işleme Için Azure WebJobs SDK ile çalışmaya başlama](webjobs-sdk-get-started.md). 

## <a name="webjobs-sdk-versions"></a>WebJobs SDK sürümleri

Bunlar 3. sürüm arasındaki önemli farklardır. *x* ve sürüm 2. Web Işleri SDK 'sının *x* 'i:

* Sürüm 3. *x* .NET Core için destek ekler.
* Sürüm 3 ' te. *x*, Web İşleri SDK 'sı tarafından gerekli olan depolama bağlama uzantısını açıkça yüklemeniz gerekir. Sürüm 2 ' de. *x*, depolama bağlamaları SDK 'ya eklenmiştir.
* .NET Core için Visual Studio Araçları (3. *x*) projeleri, .NET Framework (2) için araç aracılığıyla farklılık gösterir. *x*) projeleri. Daha fazla bilgi için bkz. [Visual Studio kullanarak Web Işleri geliştirme ve dağıtma-Azure App Service](webjobs-dotnet-deploy-vs.md).

Mümkün olduğunda, her iki sürüm 3 için örnek sağlanır. *x* ve sürüm 2. *x*.

> [!NOTE]
> [Azure işlevleri](../azure-functions/functions-overview.md) , Web İşleri SDK 'sı üzerine kurulmuştur ve bu makalede bazı konular Için Azure işlevleri belgelerine bağlantılar sağlanmaktadır. Işlevler ve WebJobs SDK arasındaki bu farklılıkları göz önünde edin:
> * Azure Işlevleri sürüm 2. *x* , Web İşleri SDK 'sı sürüm 3 ' e karşılık gelir. *x*ve Azure işlevleri 1. *x* , Web İşleri SDK 'sı 2 ' ye karşılık gelir. *x*. Kaynak kodu depoları, WebJobs SDK numaralandırmasını kullanır.
> * Azure Işlevleri C# sınıf kitaplıkları için örnek kod, WebJobs sdk projesinde `FunctionName` özniteliğine ihtiyaç duymayın hariç Web İşleri SDK kodu gibidir.
> * Bazı bağlama türleri yalnızca HTTP (Web kancaları) ve Event Grid (HTTP tabanlı) gibi Işlevlerde desteklenir.
>
> Daha fazla bilgi için bkz. [WebJobs SDK ve Azure Işlevlerini karşılaştırın](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs).

## <a name="webjobs-host"></a>Web Işleri ana bilgisayarı

Konak, işlevleri için bir çalışma zamanı kapsayıcısıdır.  Tetikleyiciler ve çağrılar işlevlerini dinler. Sürüm 3 ' te. *x*, ana bilgisayar `IHost` ' in bir uygulamasıdır. Sürüm 2 ' de. *x*, `JobHost` nesnesini kullanırsınız. Kodunuzda bir konak örneği oluşturup davranışını özelleştirmek için kod yazabilirsiniz.

Bu, Web Işleri SDK 'sını doğrudan kullanma ve Azure Işlevleri aracılığıyla dolaylı olarak kullanma arasındaki önemli bir farktır. Azure Işlevleri 'nde hizmet ana bilgisayarı denetler ve kodu yazarak Konağı özelleştiremezsiniz. Azure Işlevleri, Host. JSON dosyasındaki ayarlar aracılığıyla ana bilgisayar davranışını özelleştirmenize olanak sağlar. Bu ayarlar, kod değil dizelerdir ve bu, yapabileceğiniz özelleştirmeler türlerini sınırlandırır.

### <a name="host-connection-strings"></a>Ana bilgisayar bağlantı dizeleri

Web Işleri SDK 'Sı yerel. Settings. json dosyasında yerel. Settings. json dosyasında ya da Azure 'da çalıştırdığınızda Web Işi ortamında Azure Storage ve Azure Service Bus bağlantı dizelerini arar. Varsayılan olarak, `AzureWebJobsStorage` adlı bir depolama bağlantı dizesi ayarı gereklidir.  

Sürüm 2. SDK 'nın *x* 'i, bu bağlantı dizeleri için kendi adlarınızı kullanmanıza veya onları başka bir yerde depolamanıza olanak sağlar. [@No__t-1]kullanarak koddaki adları burada gösterildiği gibi ayarlayabilirsiniz:

```cs
static void Main(string[] args)
{
    var _storageConn = ConfigurationManager
        .ConnectionStrings["MyStorageConnection"].ConnectionString;

    //// Dashboard logging is deprecated; use Application Insights.
    //var _dashboardConn = ConfigurationManager
    //    .ConnectionStrings["MyDashboardConnection"].ConnectionString;

    JobHostConfiguration config = new JobHostConfiguration();
    config.StorageConnectionString = _storageConn;
    //config.DashboardConnectionString = _dashboardConn;
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

3\. sürüm. *x* varsayılan .NET Core Yapılandırma API 'lerini kullanır, bağlantı dizesi adlarını değiştirmek IÇIN bir API yoktur.

### <a name="host-development-settings"></a>Konak geliştirme ayarları

Yerel geliştirmeyi daha verimli hale getirmek için Konağı geliştirme modunda çalıştırabilirsiniz. Geliştirme modunda çalıştırdığınızda değiştirilen ayarlardan bazıları şunlardır:

| Özellik | Geliştirme ayarı |
| ------------- | ------------- |
| `Tracing.ConsoleLevel` | günlük çıkışını en üst düzeye çıkarmak için 0 @no__t. |
| `Queues.MaxPollingInterval`  | Sıra yöntemlerinin hemen tetiklendiğinden emin olmak için düşük bir değer.  |
| `Singleton.ListenerLockPeriod` | hızlı yinelemeli geliştirmeye yardımcı olmak için 15 saniye. |

Geliştirme modunu etkinleştirme işlemi SDK sürümüne bağlıdır. 

#### <a name="version-3x"></a>Sürüm 3. *x*

Sürüm 3. *x* standart ASP.NET Core API 'lerini kullanır. [@No__t-3](/dotnet/api/microsoft.extensions.hosting.hostbuilder) örneğinde [`UseEnvironment`](/dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.useenvironment) metodunu çağırın. Bu örnekte olduğu gibi `development` adlı bir dize geçirin:

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.UseEnvironment("development");
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
            });
    var host = builder.Build();
    using (host)
    {
        host.Run();
    }
}
```

#### <a name="version-2x"></a>Sürüm 2. *x*

@No__t-0 sınıfında, geliştirme modunu sağlayan bir `UseDevelopmentSettings` yöntemi vardır.  Aşağıdaki örnek, geliştirme ayarlarının nasıl kullanılacağını göstermektedir. @No__t-0 ' ı yerel olarak çalıştığında `true` döndürmek için `AzureWebJobsEnv` adlı yerel bir ortam değişkenini `Development` değeriyle ayarlayın.

```cs
static void Main()
{
    config = new JobHostConfiguration();

    if (config.IsDevelopment)
    {
        config.UseDevelopmentSettings();
    }

    var host = new JobHost(config);
    host.RunAndBlock();
}
```

### <a name="jobhost-servicepointmanager-settings"></a>Eş zamanlı bağlantıları yönetme (sürüm 2. *x*)

Sürüm 3 ' te. *x*, bağlantı sınırı varsayılan olarak sonsuz bağlantılardır. Bazı nedenlerle bu sınırı değiştirmeniz gerekiyorsa, [`WinHttpHandler`](/dotnet/api/system.net.http.winhttphandler) sınıfının [`MaxConnectionsPerServer`](/dotnet/api/system.net.http.winhttphandler.maxconnectionsperserver) özelliğini kullanabilirsiniz.

Sürüm 2 ' de. *x*, [ServicePointManager. DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit#System_Net_ServicePointManager_DefaultConnectionLimit) API 'sini kullanarak bir konağa yönelik eşzamanlı bağlantı sayısını kontrol edersiniz. 2 içinde. *x*, Web işleri ana bilgisayarınızı başlatmadan önce bu değeri varsayılan değer olan 2 ' den artırmanız gerekir.

@No__t-1 aracılığıyla `HttpClient` akışı kullanarak bir işlevden yaptığınız tüm giden HTTP istekleri. @No__t-0 ' da ayarlanan değere ulaştığınızda, `ServicePointManager`, göndermeden önce sıraya alma isteklerini başlatır. @No__t-0 ' ın 2 olarak ayarlandığını ve kodunuzun 1.000 HTTP isteği oluşturduğunu varsayalım. Başlangıçta, işletim sistemine yalnızca iki istek için izin verilir. Diğer 998, bunlar için yer alana kadar sıraya alınır. Bu da `HttpClient` ' ın, isteği yaptığı gibi göründüğü, ancak istek işletim sistemi tarafından hedef sunucuya hiç gönderilmemiş olabileceği anlamına gelir. Bu nedenle, anlamlı olmayan davranışları görebilirsiniz: yerel `HttpClient` bir isteği tamamlamaya yönelik 10 saniye sürer, ancak hizmetiniz her isteği 200 MS 'de döndürüyor. 

ASP.NET uygulamaları için varsayılan değer `Int32.MaxValue` ' dır ve bu, temel veya daha yüksek bir App Service planında çalışan Web Işleri için iyi bir çalışma olabilir. Web Işleri genellikle her zaman açık ayarına gerek duyar ve yalnızca temel ve daha yüksek App Service planlarında desteklenir.

WebJob 'niz ücretsiz veya paylaşılan bir App Service planında çalışıyorsa, uygulamanız Şu anda [300 bağlantı sınırına](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#per-sandbox-per-appper-site-numerical-limits)sahip olan App Service korumalı alana göre kısıtlanır. @No__t-0 ' da ilişkisiz bağlantı sınırı ile, korumalı alan bağlantı eşiğine ulaşılmaya ve sitenin kapatılmasını daha yüksektir. Bu durumda, `DefaultConnectionLimit` ' ı daha düşük bir değere (50 veya 100 gibi) ayarlamak, bunun oluşmasını engelleyebilir ve yine de yeterli aktarım hızına izin verebilir.

Ayar, herhangi bir HTTP isteği yapılmadan önce yapılandırılmalıdır. Bu nedenle, WebJobs ana bilgisayarı ayarı otomatik olarak ayarlamamamalıdır. Ana bilgisayar başlamadan önce oluşan HTTP istekleri olabilir, bu durum beklenmeyen davranışlara neden olabilir. En iyi yaklaşım, burada gösterildiği gibi, `JobHost` ' i başlatmadan önce `Main` yönteminizin hemen ayarlanabilmesini sağlar:

```csharp
static void Main(string[] args)
{
    // Set this immediately so that it's used by all requests.
    ServicePointManager.DefaultConnectionLimit = Int32.MaxValue;

    var host = new JobHost();
    host.RunAndBlock();
}
```

## <a name="triggers"></a>Tetikleyiciler

İşlevlerin ortak Yöntemler olması ve bir tetikleyici özniteliği veya [`NoAutomaticTrigger`](#manual-triggers) özniteliği olması gerekir.

### <a name="automatic-triggers"></a>Otomatik Tetikleyiciler

Otomatik Tetikleyiciler bir olaya yanıt olarak bir işlev çağırır. Azure kuyruk depolamaya eklenen bir ileti tarafından tetiklenen bir işlev örneğini göz önünde bulundurun. Azure Blob depolamadan bir blob okuyarak yanıt verir:

```cs
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{myQueueItem}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

@No__t-0 özniteliği, `myqueue-items` kuyruğunda bir kuyruk iletisi göründüğünde çalışma zamanına işlevi çağırmasını söyler. @No__t-0 özniteliği, çalışma zamanına, *örnek-WorkItems* kapsayıcısında bir blobu okumak için kuyruk iletisini kullanmasını söyler. @No__t-0 parametresindeki işleve geçirilen kuyruk iletisinin içeriği, Blobun adıdır.

[!INCLUDE [webjobs-always-on-note](../../includes/webjobs-always-on-note.md)]

### <a name="manual-triggers"></a>El ile Tetikleyiciler

Bir işlevi el ile tetiklemek için, burada gösterildiği gibi `NoAutomaticTrigger` özniteliğini kullanın:

```cs
[NoAutomaticTrigger]
public static void CreateQueueMessage(
ILogger logger,
string value,
[Queue("outputqueue")] out string message)
{
    message = value;
    logger.LogInformation("Creating queue message: ", message);
}
```

İşlevi el ile tetikleme süreci SDK sürümüne bağlıdır.

#### <a name="version-3x"></a>Sürüm 3. *x*

```cs
static async Task Main(string[] args)
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddAzureStorage();
    });
    var host = builder.Build();
    using (host)
    {
        var jobHost = host.Services.GetService(typeof(IJobHost)) as JobHost;
        var inputs = new Dictionary<string, object>
        {
            { "value", "Hello world!" }
        };

        await host.StartAsync();
        await jobHost.CallAsync("CreateQueueMessage", inputs);
        await host.StopAsync();
    }
}
```

#### <a name="version-2x"></a>Sürüm 2. *x*

```cs
static void Main(string[] args)
{
    JobHost host = new JobHost();
    host.Call(typeof(Program).GetMethod("CreateQueueMessage"), new { value = "Hello world!" });
}
```

## <a name="input-and-output-bindings"></a>Giriş ve çıkış bağlamaları

Giriş bağlamaları, Azure veya üçüncü taraf hizmetlerden gelen verileri kodunuzun kullanımına sunmak için bildirim temelli bir yol sağlar. Çıkış bağlamaları, verileri güncelleştirmek için bir yol sağlar. [Başlarken](webjobs-sdk-get-started.md) makalesinde her birine bir örnek gösterilir.

Özniteliği yöntem dönüş değerine uygulayarak bir çıkış bağlaması için bir yöntem dönüş değeri kullanabilirsiniz. [Azure işlevi dönüş değerini kullanma](../azure-functions/functions-bindings-return-value.md)bölümündeki örneğe bakın.

## <a name="binding-types"></a>Bağlama türleri

Bağlama türlerini yükleme ve yönetme işlemi, sürüm 3 ' ü kullanıp kullanmayacağınızı bağlıdır. *x* veya sürüm 2. SDK 'nın *x* . Belirli bir bağlama türü için yüklemek üzere paketi, söz konusu bağlama türünün Azure Işlevleri [başvuru makalesinin](#binding-reference-information)"paketler" bölümünde bulabilirsiniz. Özel durum, Azure Işlevleri tarafından desteklenmeyen dosya tetikleyicisi ve bağlamadır (yerel dosya sistemi için).

#### <a name="version-3x"></a>Sürüm 3. *x*

Sürüm 3 ' te. *x*, depolama bağlamaları `Microsoft.Azure.WebJobs.Extensions.Storage` paketine dahildir. @No__t-1 yönteminde `AddAzureStorage` genişletme yöntemini burada gösterildiği gibi çağırın:

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
                b.AddAzureStorage();
            });
    var host = builder.Build();
    using (host)
    {
        host.Run();
    }
}
```

Diğer tetikleyici ve bağlama türlerini kullanmak için, bunları içeren NuGet paketini yükleyip uzantıya uygulanan `Add<binding>` genişletme yöntemini çağırın. Örneğin, bir Azure Cosmos DB bağlama kullanmak istiyorsanız, `Microsoft.Azure.WebJobs.Extensions.CosmosDB` ' ı yükleyip aşağıdaki gibi `AddCosmosDB` ' i çağırın:

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
                b.AddCosmosDB();
            });
    var host = builder.Build();
    using (host)
    {
        host.Run();
    }
}
```

Çekirdek hizmetlerin bir parçası olan Zamanlayıcı tetikleyicisini veya dosya bağlamayı kullanmak için sırasıyla `AddTimers` veya `AddFiles` uzantı yöntemlerini çağırın.

#### <a name="version-2x"></a>Sürüm 2. *x*

Bu tetikleyici ve bağlama türleri sürüm 2 ' ye dahildir. `Microsoft.Azure.WebJobs` paketinin *x* 'i:

* Blob depolaması
* Kuyruk depolama
* Tablo depolaması

Diğer tetikleyici ve bağlama türlerini kullanmak için, bunları içeren NuGet paketini yükleyip `JobHostConfiguration` nesnesinde bir `Use<binding>` yöntemi çağırın. Örneğin, bir Zamanlayıcı tetikleyicisi kullanmak istiyorsanız, `Microsoft.Azure.WebJobs.Extensions` ' ı yükleyip `UseTimers` ' i `Main` yönteminde aşağıda gösterildiği gibi çağırın:

```cs
static void Main()
{
    config = new JobHostConfiguration();
    config.UseTimers();
    var host = new JobHost(config);
    host.RunAndBlock();
}
```

Dosya bağlamayı kullanmak için `Microsoft.Azure.WebJobs.Extensions` ' ı yükleyip `UseFiles` ' i çağırın.

### <a name="executioncontext"></a>ExecutionContext

WebJobs, [`ExecutionContext`]' e bağlamanıza olanak tanır. Bu bağlama ile, [`ExecutionContext`] ' i işlev imzamanızdaki bir parametre olarak erişebilirsiniz. Örneğin, aşağıdaki kod, belirli bir işlev çağrısı tarafından üretilen tüm günlükleri ilişkilendirmek için kullanabileceğiniz, çağırma KIMLIĞINE erişmek için bağlam nesnesini kullanır.  

```cs
public class Functions
{
    public static void ProcessQueueMessage([QueueTrigger("queue")] string message,
        ExecutionContext executionContext,
        ILogger logger)
    {
        logger.LogInformation($"{message}\n{executionContext.InvocationId}");
    }
}
```

[@No__t-1] ' e bağlama işlemi SDK sürümünüze bağlıdır.

#### <a name="version-3x"></a>Sürüm 3. *x*

@No__t-1 yönteminde `AddExecutionContextBinding` genişletme yöntemini burada gösterildiği gibi çağırın:

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
                b.AddExecutionContextBinding();
            });
    var host = builder.Build();
    using (host)
    {
        host.Run();
    }
}
```

#### <a name="version-2x"></a>Sürüm 2. *x*

Daha önce bahsedilen `Microsoft.Azure.WebJobs.Extensions` paketi, `UseCore` yöntemini çağırarak kaydedebilmeniz için özel bir bağlama türü de sağlar. Bu bağlama, işlev imzasında bir [`ExecutionContext`] parametresi tanımlamanızı sağlar ve bunun gibi etkindir:

```cs
class Program
{
    static void Main()
    {
        config = new JobHostConfiguration();
        config.UseCore();
        var host = new JobHost(config);
        host.RunAndBlock();
    }
}
```

## <a name="binding-configuration"></a>Bağlama yapılandırması

Bazı Tetikleyiciler ve bağlamaların davranışını yapılandırabilirsiniz. Yapılandırma süreci SDK sürümüne göre değişir.

* **Sürüm 3. *x*:** `ConfigureWebJobs` ' te `Add<Binding>` yöntemi çağrıldığında yapılandırmayı ayarla.
* **Sürüm 2. *x*:** `JobHost` ' ye geçirdiğiniz bir yapılandırma nesnesindeki özellikleri ayarlayarak yapılandırmayı ayarlayın.

Bu bağlamaya özgü ayarlar, Azure Işlevleri 'ndeki [Host. JSON proje dosyasındaki](../azure-functions/functions-host-json.md) ayarlara eşdeğerdir.

Aşağıdaki bağlamaları yapılandırabilirsiniz:

* [Azure CosmosDB tetikleyicisi](#azure-cosmosdb-trigger-configuration-version-3x)
* [Event Hubs tetikleyicisi](#event-hubs-trigger-configuration-version-3x)
* [Kuyruk depolama tetikleyicisi](#queue-storage-trigger-configuration)
* [SendGrid bağlama](#sendgrid-binding-configuration-version-3x)
* [Service Bus tetikleyicisi](#service-bus-trigger-configuration-version-3x)

### <a name="azure-cosmosdb-trigger-configuration-version-3x"></a>Azure CosmosDB tetikleme yapılandırması (sürüm 3. *x*)

Bu örnek, Azure Cosmos DB tetikleyicisinin nasıl yapılandırılacağını göstermektedir:

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddCosmosDB(a =>
        {
            a.ConnectionMode = ConnectionMode.Gateway;
            a.Protocol = Protocol.Https;
            a.LeaseOptions.LeasePrefix = "prefix1";

        });
    });
    var host = builder.Build();
    using (host)
    {

        host.Run();
    }
}
```

Daha ayrıntılı bilgi için bkz. [Azure CosmosDB bağlama](../azure-functions/functions-bindings-cosmosdb-v2.md#hostjson-settings) makalesi.

### <a name="event-hubs-trigger-configuration-version-3x"></a>Event Hubs tetikleyicisi yapılandırması (sürüm 3. *x*)

Bu örnek, Event Hubs tetikleyicisinin nasıl yapılandırılacağını göstermektedir:

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddEventHubs(a =>
        {
            a.BatchCheckpointFrequency = 5;
            a.EventProcessorOptions.MaxBatchSize = 256;
            a.EventProcessorOptions.PrefetchCount = 512;
        });
    });
    var host = builder.Build();
    using (host)
    {

        host.Run();
    }
}
```

Daha fazla ayrıntı için [Event Hubs bağlama](../azure-functions/functions-bindings-event-hubs.md#hostjson-settings) makalesine bakın.

### <a name="queue-storage-trigger-configuration"></a>Kuyruk depolama tetikleyicisi yapılandırması

Bu örneklerde, kuyruk depolama tetikleyicisinin nasıl yapılandırılacağı gösterilmektedir:

#### <a name="version-3x"></a>Sürüm 3. *x*

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddAzureStorage(a => {
            a.BatchSize = 8;
            a.NewBatchThreshold = 4;
            a.MaxDequeueCount = 4;
            a.MaxPollingInterval = TimeSpan.FromSeconds(15);
        });
    });
    var host = builder.Build();
    using (host)
    {

        host.Run();
    }
}
```

Daha ayrıntılı bilgi için bkz. [kuyruk depolama bağlama](../azure-functions/functions-bindings-storage-queue.md#hostjson-settings) makalesi.

#### <a name="version-2x"></a>Sürüm 2. *x*

```cs
static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.Queues.BatchSize = 8;
    config.Queues.NewBatchThreshold = 4;
    config.Queues.MaxDequeueCount = 4;
    config.Queues.MaxPollingInterval = TimeSpan.FromSeconds(15);
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

Daha fazla ayrıntı için bkz. [Host. JSON v1. x başvurusu](../azure-functions/functions-host-json-v1.md#queues).

### <a name="sendgrid-binding-configuration-version-3x"></a>SendGrid bağlama yapılandırması (sürüm 3. *x*)

Bu örnek, SendGrid çıkış bağlamasının nasıl yapılandırılacağını gösterir:

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddSendGrid(a =>
        {
            a.FromAddress.Email = "samples@functions.com";
            a.FromAddress.Name = "Azure Functions";
        });
    });
    var host = builder.Build();
    using (host)
    {

        host.Run();
    }
}
```

Daha fazla ayrıntı için bkz. [SendGrid bağlama](../azure-functions/functions-bindings-sendgrid.md#hostjson-settings) makalesi.

### <a name="service-bus-trigger-configuration-version-3x"></a>Service Bus tetikleyicisi yapılandırması (sürüm 3. *x*)

Bu örnek, Service Bus tetikleyicisinin nasıl yapılandırılacağını göstermektedir:

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddServiceBus(sbOptions =>
        {
            sbOptions.MessageHandlerOptions.AutoComplete = true;
            sbOptions.MessageHandlerOptions.MaxConcurrentCalls = 16;
        });
    });
    var host = builder.Build();
    using (host)
    {

        host.Run();
    }
}
```

Daha fazla ayrıntı için [Service Bus bağlama](../azure-functions/functions-bindings-service-bus.md#hostjson-settings) makalesine bakın.

### <a name="configuration-for-other-bindings"></a>Diğer bağlamaların yapılandırması

Bazı tetikleyici ve bağlama türleri kendi özel yapılandırma türlerini tanımlar. Örneğin, dosya tetikleyicisi aşağıdaki örneklerde olduğu gibi, izlenecek kök yolunu belirtmenizi sağlar:

#### <a name="version-3x"></a>Sürüm 3. *x*

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddFiles(a => a.RootPath = @"c:\data\import");
    });
    var host = builder.Build();
    using (host)
    {

        host.Run();
    }
}
```

#### <a name="version-2x"></a>Sürüm 2. *x*

```cs
static void Main()
{
    config = new JobHostConfiguration();
    var filesConfig = new FilesConfiguration
    {
        RootPath = @"c:\data\import"
    };
    config.UseFiles(filesConfig);
    var host = new JobHost(config);
    host.RunAndBlock();
}
```

## <a name="binding-expressions"></a>Bağlama ifadeleri

Öznitelik Oluşturucu parametrelerinde, çeşitli kaynaklardaki değerlere çözüm veren ifadeleri kullanabilirsiniz. Örneğin, aşağıdaki kodda `BlobTrigger` özniteliğinin yolu `filename` adlı bir ifade oluşturur. Çıkış bağlaması için kullanıldığında, `filename` tetikleme Blobun adına çözümlenir.

```cs
public static void CreateThumbnail(
    [BlobTrigger("sample-images/{filename}")] Stream image,
    [Blob("sample-images-sm/{filename}", FileAccess.Write)] Stream imageSmall,
    string filename,
    ILogger logger)
{
    logger.Info($"Blob trigger processing: {filename}");
    // ...
}
```

Bağlama ifadeleri hakkında daha fazla bilgi için bkz. Azure Işlevleri belgelerinde [ifadeleri ve desenleri bağlama](../azure-functions/functions-bindings-expressions-patterns.md) .

### <a name="custom-binding-expressions"></a>Özel bağlama ifadeleri

Bazen bir kuyruk adı, bir blob adı veya kapsayıcısı ya da kod içinde sabit kodlamak yerine bir tablo adı belirtmek isteyebilirsiniz. Örneğin, bir yapılandırma dosyasında veya ortam değişkeninde `QueueTrigger` özniteliği için sıra adını belirtmek isteyebilirsiniz.

Bunu, `NameResolver` nesnesini `JobHostConfiguration` nesnesine geçirerek yapabilirsiniz. Tetikleyici veya bağlama öznitelik Oluşturucu parametrelerine yer tutucuları dahil edersiniz ve `NameResolver` kodunuz, bu yer tutucuların yerine kullanılacak gerçek değerleri sağlar. Yer tutucuları, yüzde (%) ile çevreleyerek belirlersiniz burada gösterildiği gibi imzalar:

```cs
public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
{
    Console.WriteLine(logMessage);
}
```

Bu kod, test ortamında `logqueuetest` adlı bir kuyruk kullanmanıza olanak sağlar ve bir diğeri de üretimde `logqueueprod` olarak adlandırılır. Sabit kodlanmış bir sıra adı yerine `appSettings` koleksiyonundaki bir girdinin adını belirtirsiniz.

Özel bir tane sağlamazsanız, geçerli bir varsayılan `NameResolver` vardır. Varsayılan değer, uygulama ayarlarından veya ortam değişkenlerinden değerleri alır.

@No__t-0 sınıfınız, burada gösterildiği gibi `appSettings` ' den sıra adını alır:

```cs
public class CustomNameResolver : INameResolver
{
    public string Resolve(string name)
    {
        return ConfigurationManager.AppSettings[name].ToString();
    }
}
```

#### <a name="version-3x"></a>Sürüm 3. *x*

Çözümleyici 'yi bağımlılık ekleme kullanarak yapılandırırsınız. Bu örnekler için aşağıdaki `using` ifadesinin olması gerekir:

```cs
using Microsoft.Extensions.DependencyInjection;
```

Bu örnekte olduğu gibi, [`ConfigureServices`] uzantı yöntemini [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder)' te çağırarak çözümleyici eklersiniz:

```cs
static async Task Main(string[] args)
{
    var builder = new HostBuilder();
    var resolver = new CustomNameResolver();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
    });
    builder.ConfigureServices(s => s.AddSingleton<INameResolver>(resolver));
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>Sürüm 2. *x*

@No__t-0 sınıfınızı, burada gösterildiği gibi `JobHost` nesnesine geçirin:

```cs
 static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.NameResolver = new CustomNameResolver();
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

Azure Işlevleri, örnekte gösterildiği gibi, uygulama ayarlarından değerleri almak için `INameResolver` ' ı uygular. WebJobs SDK 'sını doğrudan kullandığınızda, tercih ettiğiniz herhangi bir kaynaktan yer tutucu değiştirme değerlerini alan özel bir uygulama yazabilirsiniz.

## <a name="binding-at-runtime"></a>Çalışma zamanında bağlama

@No__t-0, `Blob` veya `Table` gibi bir bağlama özniteliği kullanmadan önce işlevinizde bazı çalışmalar yapmanız gerekiyorsa `IBinder` arabirimini kullanabilirsiniz.

Aşağıdaki örnek bir giriş kuyruğu iletisi alır ve bir çıkış kuyruğunda aynı içeriğe sahip yeni bir ileti oluşturur. Çıkış sırası adı, işlevin gövdesinde kodla ayarlanır.

```cs
public static void CreateQueueMessage(
    [QueueTrigger("inputqueue")] string queueMessage,
    IBinder binder)
{
    string outputQueueName = "outputqueue" + DateTime.Now.Month.ToString();
    QueueAttribute queueAttribute = new QueueAttribute(outputQueueName);
    CloudQueue outputQueue = binder.Bind<CloudQueue>(queueAttribute);
    outputQueue.AddMessageAsync(new CloudQueueMessage(queueMessage));
}
```

Daha fazla bilgi için bkz. Azure Işlevleri belgelerinde [çalışma zamanında bağlama](../azure-functions/functions-dotnet-class-library.md#binding-at-runtime) .

## <a name="binding-reference-information"></a>Başvuru bilgilerini bağlama

Azure Işlevleri belgeleri, her bağlama türü hakkında başvuru bilgileri sağlar. Her bağlama başvuru makalesinde aşağıdaki bilgileri bulabilirsiniz. (Bu örnek, depolama kuyruğuna dayalıdır.)

* [Paketler](../azure-functions/functions-bindings-storage-queue.md#packages---functions-1x). Web Işleri SDK projesinde bağlama desteğini dahil etmek için yüklemeniz gereken paket.
* [Örnekler](../azure-functions/functions-bindings-storage-queue.md#trigger---example). Kod örnekleri. C# Sınıf kitaplığı örneği, Web İşleri SDK 'sı için geçerlidir. Yalnızca `FunctionName` özniteliğini atlayın.
* [Öznitelikler](../azure-functions/functions-bindings-storage-queue.md#trigger---attributes). Bağlama türü için kullanılacak öznitelikler.
* [Yapılandırma](../azure-functions/functions-bindings-storage-queue.md#trigger---configuration). Öznitelik özellikleri ve Oluşturucu parametrelerinin açıklamaları.
* [Kullanım](../azure-functions/functions-bindings-storage-queue.md#trigger---usage). Bağlayacağınız türler ve bağlamanın nasıl çalıştığı hakkında bilgi alabilirsiniz. Örneğin: yoklama algoritması, zarar kuyruğu işleme.
  
Bağlama başvuru makalelerinin bir listesi için bkz. Azure Işlevleri için [Tetikleyiciler ve bağlamalar](../azure-functions/functions-triggers-bindings.md#supported-bindings) makalesindeki "desteklenen bağlamalar". Bu listede, HTTP, Web kancaları ve Event Grid bağlamaları yalnızca Azure Işlevleri tarafından desteklenir, Web Işleri SDK 'Sı tarafından desteklenmez.

## <a name="disable-attribute"></a>Özniteliği devre dışı bırak 

[@No__t-1](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/DisableAttribute.cs) özniteliği, bir işlevin tetiklenip tetiklenmeyeceğini denetlemenize olanak tanır. 

Aşağıdaki örnekte, `Disable_TestJob` uygulama ayarında `1` veya `True` (büyük/küçük harfe duyarsız) değeri varsa, işlev çalıştırılmaz. Bu durumda, çalışma zamanı *' Functions. TestJob '* bir günlük iletisi işlevi oluşturur.

```cs
[Disable("Disable_TestJob")]
public static void TestJob([QueueTrigger("testqueue2")] string message)
{
    Console.WriteLine("Function with Disable attribute executed!");
}
```

Azure portal uygulama ayarı değerlerini değiştirdiğinizde, WebJob yeni ayarı seçmek için yeniden başlatılır.

Özniteliği parametresi, yöntemi veya sınıf düzeyinde bildirilebilecek. Ayar adı, bağlama ifadeleri de içerebilir.

## <a name="timeout-attribute"></a>Timeout özniteliği

[@No__t-1](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TimeoutAttribute.cs) özniteliği, belirli bir süre içinde bitmemişse bir işlevin iptal edilmesine neden olur. Aşağıdaki örnekte, işlevi zaman aşımı özniteliği olmadan bir gün çalışır. Zaman aşımı işlevin 15 saniye sonra iptal edilmesine neden olur.

```cs
[Timeout("00:00:15")]
public static async Task TimeoutJob(
    [QueueTrigger("testqueue2")] string message,
    CancellationToken token,
    TextWriter log)
{
    await log.WriteLineAsync("Job starting");
    await Task.Delay(TimeSpan.FromDays(1), token);
    await log.WriteLineAsync("Job completed");
}
```

Zaman aşımı özniteliğini sınıf veya yöntem düzeyinde uygulayabilir ve `JobHostConfiguration.FunctionTimeout` ' ı kullanarak genel zaman aşımı belirleyebilirsiniz. Sınıf düzeyi veya yöntem düzeyi zaman aşımları genel zaman aşımlarını geçersiz kılar.

## <a name="singleton-attribute"></a>Singleton özniteliği

[@No__t-1](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/SingletonAttribute.cs) özniteliği, konak Web uygulamasının birden fazla örneği olsa bile, bir işlevin yalnızca bir örneğinin çalışmasını sağlar. Bunu, [Dağıtılmış kilitlemeyi](#viewing-lease-blobs)kullanarak yapar.

Bu örnekte, belirli bir zamanda `ProcessImage` işlevinin yalnızca tek bir örneği çalışır:

```cs
[Singleton]
public static async Task ProcessImage([BlobTrigger("images")] Stream image)
{
     // Process the image.
}
```

### <a name="singletonmodelistener"></a>SingletonMode. dinleyicisi

Bazı Tetikleyiciler eşzamanlılık yönetimi için yerleşik desteğe sahiptir:

* **Queuetrigger**. @No__t-0 ' yı `1` olarak ayarlayın.
* **Servicebustrigger**. @No__t-0 ' yı `1` olarak ayarlayın.
* **Dosya tetikleyicisi**. @No__t-0 ' yı `1` olarak ayarlayın.

Bu ayarları, işlevinizin tek bir örnek üzerinde Singleton olarak çalıştığından emin olmak için kullanabilirsiniz. Web uygulaması birden çok örneğe ölçeklendirirken işlevin yalnızca tek bir örneğinin çalıştığından emin olmak için, işleve bir dinleyici düzeyi tek kilit uygulayın (`[Singleton(Mode = SingletonMode.Listener)]`). JobHost başlatıldığında dinleyici kilitleri elde edilir. Üç ölçekli örnek hepsi aynı anda başladıysanız, örneklerden yalnızca biri kilidi alır ve yalnızca bir dinleyici başlatılır.

### <a name="scope-values"></a>Kapsam değerleri

Tek başına bir *kapsam ifadesi/değeri* belirtebilirsiniz. İfade/değer, belirli bir kapsamdaki işlevin tüm yürütmelerinin serileştirilmesini sağlar. Bu şekilde daha ayrıntılı kilitleme uygulamak, gereksinimlerinize göre dikte edilen diğer çağırmaları serileştirirken işleviniz için bazı paralellik düzeyine izin verebilir. Örneğin, aşağıdaki kodda, kapsam ifadesi gelen iletinin `Region` değerine bağlanır. Sıra, Doğu, Doğu ve Batı bölgelerinde üç ileti içerdiğinde, Doğu bölgesi olan iletiler, Batı bölgesindeki ileti, Doğu ile paralel olarak çalıştırılyana çalışır.

```csharp
[Singleton("{Region}")]
public static async Task ProcessWorkItem([QueueTrigger("workitems")] WorkItem workItem)
{
     // Process the work item.
}

public class WorkItem
{
     public int ID { get; set; }
     public string Region { get; set; }
     public int Category { get; set; }
     public string Description { get; set; }
}
```

### <a name="singletonscopehost"></a>SingletonScope. Host

Kilit için varsayılan kapsam `SingletonScope.Function` ' dır, yani kilit kapsamı (blob kira yolu) tam işlev adına bağlanır. İşlevler arasında kilitlemek için `SingletonScope.Host` belirtin ve aynı anda çalıştırmak istemediğiniz tüm işlevlerde aynı olan bir kapsam KIMLIĞI adı kullanın. Aşağıdaki örnekte, tek seferde yalnızca bir `AddItem` veya `RemoveItem` örneği çalışır:

```csharp
[Singleton("ItemsLock", SingletonScope.Host)]
public static void AddItem([QueueTrigger("add-item")] string message)
{
     // Perform the add operation.
}

[Singleton("ItemsLock", SingletonScope.Host)]
public static void RemoveItem([QueueTrigger("remove-item")] string message)
{
     // Perform the remove operation.
}
```

### <a name="viewing-lease-blobs"></a>Kira bloblarını görüntüleme

Web Işleri SDK 'Sı, dağıtılmış kilitlemeyi uygulamak için, kapakların altında [Azure Blob kiraları](../storage/common/storage-concurrency.md#pessimistic-concurrency-for-blobs) kullanır. Singleton tarafından kullanılan kira Blobları, "kilitler" yolu altındaki `AzureWebJobsStorage` depolama hesabındaki `azure-webjobs-host` kapsayıcısında bulunabilir. Örneğin, daha önce gösterilen ilk `ProcessImage` örnek için kira blobu yolu `locks/061851c758f04938a4426aa9ab3869c0/WebJobs.Functions.ProcessImage` olabilir. Tüm yollar, bu durumda 061851c758f04938a4426aa9ab3860 ' da JobHost KIMLIĞINI içerir.

## <a name="async-functions"></a>Zaman uyumsuz işlevler

Zaman uyumsuz işlevlerin nasıl kodgörüntüleneceği hakkında daha fazla bilgi için bkz. [Azure işlevleri belgeleri](../azure-functions/functions-dotnet-class-library.md#async).

## <a name="cancellation-tokens"></a>İptal belirteçleri

İptal belirteçlerini işleme hakkında daha fazla bilgi için bkz. [iptal belirteçleri ve düzgün kapanma](../azure-functions/functions-dotnet-class-library.md#cancellation-tokens)hakkında Azure işlevleri belgeleri.

## <a name="multiple-instances"></a>Birden çok örnek

Web uygulamanız birden çok örnek üzerinde çalışıyorsa, her örnekte sürekli bir WebJob çalışarak Tetikleyiciler ve çağırma işlevlerini dinler. Çeşitli tetikleyici bağlamaları, diğer örneklere göre ölçeklendirilmesi için daha fazla yük işleyebilmenizi sağlamak üzere iş ortak çalışan örnekleri arasında verimli bir şekilde paylaşılacak şekilde tasarlanmıştır.

Bazı Tetikleyiciler çift işleme ile sonuçlanabileceğinden, kuyruk ve BLOB depolama Tetikleyicileri, bir işlevin bir sıra iletisini veya blobu birden çok kez işlemesini otomatik olarak önler. Daha fazla bilgi için bkz. Azure Işlevleri belgelerinde [özdeş giriş Için tasarlama](../azure-functions/functions-idempotent.md) .

Zamanlayıcı tetikleyicisi, yalnızca bir Zamanlayıcı örneğinin çalışmasını sağlar, böylece belirli bir zamanlanan sürede çalışan birden fazla işlev örneği almaz.

Konak Web uygulamasının birden fazla örneği olsa bile bir işlevin yalnızca bir örneğinin çalışmasını sağlamak istiyorsanız, [`Singleton`](#singleton-attribute) özniteliğini kullanabilirsiniz.

## <a name="filters"></a>Filtreler

İşlev filtreleri (Önizleme), Web Işleri yürütme işlem hattını kendi mantığınızla özelleştirmek için bir yol sağlar. Filtreler [ASP.NET Core filtreye](https://docs.microsoft.com/aspnet/core/mvc/controllers/filters)benzerdir. Bunları, işlevleriniz veya sınıflarınıza uygulanan bildirim temelli öznitelikler olarak uygulayabilirsiniz. Daha fazla bilgi için bkz. [Işlev filtreleri](https://github.com/Azure/azure-webjobs-sdk/wiki/Function-Filters).

## <a name="logging-and-monitoring"></a>Günlüğe kaydetme ve izleme

ASP.NET için geliştirilen günlük çerçevesini öneririz. [Başlarken](webjobs-sdk-get-started.md) makalesinde nasıl kullanılacağı gösterilir. 

### <a name="log-filtering"></a>Günlük filtreleme

@No__t-0 örneğiyle oluşturulan her günlüğün ilişkili bir `Category` ve `Level` vardır. [`LogLevel`](/dotnet/api/microsoft.extensions.logging.loglevel) bir numaralandırmadır ve tamsayı kodu göreli önemi gösterir:

|LogLevel    |Kodlayın|
|------------|---|
|İzleme       | 0 |
|Hata ayıklama       | 1 |
|Bilgi | 2 |
|Uyarı     | 3 |
|Hata       | 4 |
|Kritik    | 5 |
|Hiçbiri        | 6 |

Her bir kategoriyi belirli bir [`LogLevel`](/dotnet/api/microsoft.extensions.logging.loglevel)olarak filtreleyebilirsiniz. Örneğin, blob tetikleyicisi işleme için tüm günlükleri görmek isteyebilirsiniz, ancak diğer her şey için yalnızca `Error` ve üzeri.

#### <a name="version-3x"></a>Sürüm 3. *x*

Sürüm 3. SDK 'nın *x* , .NET Core 'da yerleşik olarak bulunan filtrelemeyi temel alır. @No__t-0 sınıfı belirli işlevler, Tetikleyiciler veya kullanıcılar için kategoriler tanımlamanıza olanak sağlar. Ayrıca, `Startup` ve `Results` gibi belirli ana bilgisayar durumlarının filtrelerini de tanımlar. Bu, günlüğe kaydetme çıkışının ince ayar yapmanıza olanak sağlar. Tanımlı kategorilerde eşleşme bulunmazsa, iletiyi filtrelemeye karar verirken filtre `Default` değerine geri döner.

`LogCategories` aşağıdaki using ifadesini gerektirir:

```cs
using Microsoft.Azure.WebJobs.Logging; 
```

Aşağıdaki örnek, varsayılan olarak `Warning` düzeyinde tüm günlüklere filtre uygulayan bir filtre oluşturur. @No__t-0 ve `results` kategorileri (sürüm 2 ' deki `Host.Results` ' ye denktir. *x*) `Error` düzeyinde filtrelenmiştir. Filtre geçerli kategoriyi `LogCategories` örneğindeki tüm kayıtlı düzeylerle karşılaştırır ve en uzun eşleşmeyi seçer. Bu, `Host.Triggers` için kaydedilen `Debug` düzeyinin `Host.Triggers.Queue` veya `Host.Triggers.Blob` ' ü eşleştirtiği anlamına gelir. Bu, her birini eklemeye gerek kalmadan daha geniş kategorileri denetlemenize olanak tanır.

```cs
static async Task Main(string[] args)
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
    });
    builder.ConfigureLogging(logging =>
            {
                logging.SetMinimumLevel(LogLevel.Warning);
                logging.AddFilter("Function", LogLevel.Error);
                logging.AddFilter(LogCategories.CreateFunctionCategory("MySpecificFunctionName"),
                    LogLevel.Debug);
                logging.AddFilter(LogCategories.Results, LogLevel.Error);
                logging.AddFilter("Host.Triggers", LogLevel.Debug);
            });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>Sürüm 2. *x*

Sürüm 2 ' de. SDK 'nın *x* , `LogCategoryFilter` sınıfını kullanarak filtrelemeyi denetleyebilirsiniz. @No__t-0, ilk değeri `Information` olan `Default` özelliğine sahiptir, yani `Information`, `Warning`, `Error` veya `Critical` düzeylerindeki tüm iletiler günlüğe kaydedilir, ancak `Debug` veya `Trace` düzeylerindeki tüm iletiler dışarıda filtrelenir.

@No__t-sürüm 3 ' te 0. *x*, `CategoryLevels` özelliği belirli kategoriler için günlük düzeylerini belirtmenizi sağlar, böylece günlüğe kaydetme çıkışının ince ayarlarını yapabilirsiniz. @No__t-0 sözlüğünde eşleşme bulunmazsa, iletiyi filtreleyip filtreleyemeyeceğine karar verirken filtre `Default` değerine geri döner.

Aşağıdaki örnek, varsayılan olarak `Warning` düzeyinde tüm günlüklere filtre uygulayan bir filtre oluşturur. @No__t-0 ve `Host.Results` kategorileri `Error` düzeyinde filtrelenmiştir. @No__t-0, geçerli kategoriyi tüm kayıtlı `CategoryLevels` ile karşılaştırır ve en uzun eşleşmeyi seçer. Bu nedenle, `Host.Triggers` için kaydedilen `Debug` düzeyi `Host.Triggers.Queue` veya `Host.Triggers.Blob` ile eşleşir. Bu, her birini eklemeye gerek kalmadan daha geniş kategorileri denetlemenize olanak tanır.

```csharp
var filter = new LogCategoryFilter();
filter.DefaultLevel = LogLevel.Warning;
filter.CategoryLevels[LogCategories.Function] = LogLevel.Error;
filter.CategoryLevels[LogCategories.Results] = LogLevel.Error;
filter.CategoryLevels["Host.Triggers"] = LogLevel.Debug;

config.LoggerFactory = new LoggerFactory()
    .AddApplicationInsights(instrumentationKey, filter.Filter)
    .AddConsole(filter.Filter);
```

### <a name="custom-telemetry-for-application-insights"></a>Application Insights için özel telemetri

[Application Insights](../azure-monitor/app/app-insights-overview.md) için özel telemetri uygulama işlemi SDK sürümüne bağlıdır. Application Insights nasıl yapılandırılacağını öğrenmek için bkz. [Application Insights günlüğü ekleme](webjobs-sdk-get-started.md#add-application-insights-logging).

#### <a name="version-3x"></a>Sürüm 3. *x*

3\. sürüm. Web Işleri SDK 'sının *x* 'ı .NET Core genel ana bilgisayarına dayanır, artık özel bir telemetri fabrikası sağlanmamıştır. Ancak bağımlılık ekleme kullanarak işlem hattına özel telemetri ekleyebilirsiniz. Bu bölümdeki örneklerde aşağıdaki `using` deyimleri gerekir:

```cs
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.Channel;
```

[@No__t-1] ' in aşağıdaki özel uygulanması, kendi [@no__t 3 ' ü](/dotnet/api/microsoft.applicationinsights.channel.itelemetry) varsayılan [`TelemetryConfiguration`]' e eklemenize olanak tanır.

```cs
internal class CustomTelemetryInitializer : ITelemetryInitializer
{
    public void Initialize(ITelemetry telemetry)
    {
        // Do something with telemetry.
    }
}
```

Özel [@no__t 3 ' ü] işlem hattına eklemek için oluşturucu 'da [`ConfigureServices` ' i] çağırın.

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
    });
    builder.ConfigureLogging((context, b) =>
    {
        // Add logging providers.
        b.AddConsole();

        // If this key exists in any config, use it to enable Application Insights.
        string appInsightsKey = context.Configuration["APPINSIGHTS_INSTRUMENTATIONKEY"];
        if (!string.IsNullOrEmpty(appInsightsKey))
        {
            // This uses the options callback to explicitly set the instrumentation key.
            b.AddApplicationInsights(o => o.InstrumentationKey = appInsightsKey);
        }
    });
    builder.ConfigureServices(services =>
        {
            services.AddSingleton<ITelemetryInitializer, CustomTelemetryInitializer>();
        });
    var host = builder.Build();
    using (host)
    {

        host.Run();
    }
}
```

[@No__t-1] oluşturulduğunda, [`ITelemetryInitializer`] ' ün tüm kayıtlı türleri dahil edilir. Daha fazla bilgi için bkz. [özel olaylar ve ölçümler için Application Insights API 'si](../azure-monitor/app/api-custom-events-metrics.md).

Sürüm 3 ' te. Artık, ana bilgisayar durdurulduğunda [`TelemetryClient`] *' yi*temizleyemiyor. .NET Core bağımlılığı ekleme sistemi, kayıtlı `ApplicationInsightsLoggerProvider` ' ı otomatik olarak ortadan kaldırtır ve bu [`TelemetryClient`]' yi temizler.

#### <a name="version-2x"></a>Sürüm 2. *x*

Sürüm 2 ' de. *x*, Web İşleri SDK 'sı için Application Insights sağlayıcısı tarafından dahili olarak oluşturulan [`TelemetryClient`] [`ServerTelemetryChannel`](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/ServerTelemetryChannel/ServerTelemetryChannel.cs)kullanır. Application Insights uç noktası kullanılamaz veya gelen istekleri azaltdığı zaman, bu kanal [istekleri Web uygulamasının dosya sistemine kaydeder ve daha sonra resubmits](https://apmtips.com/blog/2015/09/03/more-telemetry-channels).

[@No__t-1] , `ITelemetryClientFactory` uygulayan bir sınıf tarafından oluşturulur. Varsayılan olarak, bu [`DefaultTelemetryClientFactory`](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/DefaultTelemetryClientFactory.cs)' dir.

Application Insights işlem hattının herhangi bir bölümünü değiştirmek istiyorsanız, kendi `ITelemetryClientFactory` sağlayabilirsiniz ve ana bilgisayar sınıfınızı [`TelemetryClient`]oluşturmak için kullanır. Örneğin, bu kod, `ServerTelemetryChannel` ' in bir özelliğini değiştirmek için `DefaultTelemetryClientFactory` ' ı geçersiz kılar:

```csharp
private class CustomTelemetryClientFactory : DefaultTelemetryClientFactory
{
    public CustomTelemetryClientFactory(string instrumentationKey, Func<string, LogLevel, bool> filter)
        : base(instrumentationKey, new SamplingPercentageEstimatorSettings(), filter)
    {
    }

    protected override ITelemetryChannel CreateTelemetryChannel()
    {
        ServerTelemetryChannel channel = new ServerTelemetryChannel();

        // Change the default from 30 seconds to 15 seconds.
        channel.MaxTelemetryBufferDelay = TimeSpan.FromSeconds(15);

        return channel;
    }
}
```

@No__t-0 nesnesi [Uyarlamalı örnekleme](https://docs.microsoft.com/azure/application-insights/app-insights-sampling)yapılandırır. Yani, bazı yüksek hacimli senaryolarda, Application Insights, Telemetri verilerinin seçili bir alt kümesini sunucusuna gönderir.

Telemetri fabrikası oluşturduktan sonra, Application Insights günlük sağlayıcısına geçitirsiniz:

```csharp
var clientFactory = new CustomTelemetryClientFactory(instrumentationKey, filter.Filter);

config.LoggerFactory = new LoggerFactory()
    .AddApplicationInsights(clientFactory);
```

## <a id="nextsteps"></a> Sonraki adımlar

Bu makale, Web Işleri SDK 'Sı ile çalışmaya yönelik yaygın senaryoları nasıl işleyeceğinizi gösteren kod parçacıkları sağlamıştır. Tam örnekler için bkz. [Azure-WebJobs-SDK-Samples](https://github.com/Azure/azure-webjobs-sdk/tree/dev/sample/SampleHost).

[ExecutionContext]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Core/ExecutionContext.cs
[TelemetryClient]: /dotnet/api/microsoft.applicationinsights.telemetryclient
[ConfigureServices]: /dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.configureservices
[' Itelemetrybaşlatıcısı ']: /dotnet/api/microsoft.applicationinsights.extensibility.itelemetryinitializer
['TelemetryConfiguration']: /dotnet/api/microsoft.applicationinsights.extensibility.telemetryconfiguration
[' JobHostConfiguration ']: https://github.com/Azure/azure-webjobs-sdk/blob/v2.x/src/Microsoft.Azure.WebJobs.Host/JobHostConfiguration.cs
