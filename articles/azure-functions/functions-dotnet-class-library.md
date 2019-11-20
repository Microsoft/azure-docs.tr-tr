---
title: Azure Işlevleri C# geliştirici başvurusu
description: Kullanarak C#Azure işlevleri geliştirmeyi anlayın.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: azure işlevleri, işlevler, olay işleme, web kancaları, dinamik işlem, sunucusuz mimari
ms.service: azure-functions
ms.devlang: dotnet
ms.topic: reference
ms.date: 09/12/2018
ms.author: glenga
ms.openlocfilehash: c3c13b7e28ef7c17fd45682d828f318de5326542
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/13/2019
ms.locfileid: "72293876"
---
# <a name="azure-functions-c-developer-reference"></a>Azure Işlevleri C# geliştirici başvurusu

<!-- When updating this article, make corresponding changes to any duplicate content in functions-reference-csharp.md -->

Bu makale, .NET sınıf kitaplıklarında kullanarak C# Azure işlevleri geliştirmeye giriş niteliğindedir.

Azure Işlevleri ve C# C# betik programlama dillerini destekler. [Azure Portal C# kullanma](functions-create-function-app-portal.md)hakkında rehberlik arıyorsanız, bkz [ C# . betik (. CSX) geliştirici başvurusu](functions-reference-csharp.md).

Bu makalede, aşağıdaki makaleleri zaten okuduğunuzu varsaymış olursunuz:

* [Azure Işlevleri Geliştirici Kılavuzu](functions-reference.md)
* [Azure Işlevleri Visual Studio 2019 araçları](functions-develop-vs.md)

## <a name="supported-versions"></a>Desteklenen sürümler

Azure Işlevleri 2. x çalışma zamanı .NET Core 2,2 kullanır. İşlev kodunuz, Visual Studio proje ayarlarını güncelleştirerek .NET Core 2,2 API 'Lerini kullanabilir. .NET Core 2,2 yüklü olmayan müşterilerin olumsuz şekilde etkilenmesinden kaçınmak için, Işlev şablonları .NET Core 2,2 ' a varsayılan değildir.

## <a name="functions-class-library-project"></a>İşlevler sınıf kitaplığı projesi

Visual Studio 'da **Azure işlevleri** proje şablonu, aşağıdaki dosyaları içeren C# bir sınıf kitaplığı projesi oluşturur:

* [Host. JSON](functions-host-json.md) -yerel olarak veya Azure 'da çalışırken projedeki tüm işlevleri etkileyen yapılandırma ayarlarını depolar.
* [Local. Settings. JSON](functions-run-local.md#local-settings-file) -yerel olarak çalışırken kullanılan uygulama ayarlarını ve bağlantı dizelerini depolar. Bu dosya gizli dizileri içerir ve Azure 'da işlev uygulamanıza yayımlanmaz. Bunun yerine, [uygulama ayarlarını işlev uygulamanıza ekleyin](functions-develop-vs.md#function-app-settings).

Projeyi derlediğinizde, derleme çıkış dizininde aşağıdaki örnek gibi görünen bir klasör yapısı oluşturulur:

```
<framework.version>
 | - bin
 | - MyFirstFunction
 | | - function.json
 | - MySecondFunction
 | | - function.json
 | - host.json
```

Bu dizin, Azure 'daki işlev uygulamanıza dağıtılır. Işlevler çalışma zamanının [2. x sürümünde](functions-versions.md) gerekli olan bağlama uzantıları [projeye NuGet paketleri olarak eklenir](./functions-bindings-register.md#vs).

> [!IMPORTANT]
> Yapı işlemi her işlev için bir *function. JSON* dosyası oluşturur. Bu *function. JSON* dosyası doğrudan düzenlenmeyecek. Bu dosyayı düzenleyerek bağlama yapılandırmasını değiştiremez veya işlevi devre dışı bırakabilirsiniz. Bir işlevi nasıl devre dışı bırakacağınızı öğrenmek için bkz. [işlevleri devre dışı bırakma](disable-function.md#functions-2x---c-class-libraries).


## <a name="methods-recognized-as-functions"></a>İşlev olarak tanınan Yöntemler

Bir sınıf kitaplığında, bir işlev, aşağıdaki örnekte gösterildiği gibi `FunctionName` ve bir tetikleyici özniteliği olan statik bir yöntemdir:

```csharp
public static class SimpleExample
{
    [FunctionName("QueueTrigger")]
    public static void Run(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        ILogger log)
    {
        log.LogInformation($"C# function processed: {myQueueItem}");
    }
} 
```

@No__t-0 özniteliği yöntemi bir işlev giriş noktası olarak işaretler. Ad bir proje içinde benzersiz olmalı, bir harfle başlamalı ve yalnızca harf, rakam, `_` ve `-`, en fazla 127 karakter uzunluğunda olmalıdır. Proje şablonları genellikle `Run` adlı bir yöntem oluşturur, ancak yöntem adı geçerli C# bir yöntem adı olabilir.

Tetikleyici özniteliği Tetikleyici türünü belirtir ve giriş verilerini bir yöntem parametresine bağlar. Örnek işlev bir kuyruk iletisi tarafından tetiklenir ve kuyruk iletisi `myQueueItem` parametresindeki yönteme geçirilir.

## <a name="method-signature-parameters"></a>Yöntem imza parametreleri

Yöntem imzası, tetikleyici özniteliğiyle kullanılandan farklı parametreler içeriyor olabilir. Şunları dahil edebilirsiniz:

* [Giriş ve çıkış bağlamaları](functions-triggers-bindings.md) öznitelikleri ile süsleyerek bu şekilde işaretlenir.  
* [Günlüğe kaydetme](#logging)için bir `ILogger` veya `TraceWriter` ([yalnızca sürüm 1. x](functions-versions.md#creating-1x-apps)) parametresi.
* [Düzgün kapanma](#cancellation-tokens)için `CancellationToken` parametresi.
* Tetikleyici meta verilerini almak için [ifade parametreleri bağlama](./functions-bindings-expressions-patterns.md) .

İşlev imzasında parametrelerin sırası önemi yoktur. Örneğin, tetikleme parametrelerini diğer bağlamalardan önce veya sonra koyabilirsiniz ve tetikleyici veya bağlama parametrelerinden önce veya sonra günlükçü parametresini yerleştirebilirsiniz.

### <a name="output-binding-example"></a>Çıkış bağlama örneği

Aşağıdaki örnek, bir çıkış sırası bağlaması ekleyerek önceki birini değiştirir. İşlevi, işlevi tetikleyen sıra iletisini farklı bir kuyruktaki yeni bir kuyruk iletisine yazar.

```csharp
public static class SimpleExampleWithOutput
{
    [FunctionName("CopyQueueMessage")]
    public static void Run(
        [QueueTrigger("myqueue-items-source")] string myQueueItem, 
        [Queue("myqueue-items-destination")] out string myQueueItemCopy,
        ILogger log)
    {
        log.LogInformation($"CopyQueueMessage function processed: {myQueueItem}");
        myQueueItemCopy = myQueueItem;
    }
}
```

Bağlama başvuru makaleleri (örneğin,[depolama kuyrukları](functions-bindings-storage-queue.md)) tetikleyici, giriş veya çıkış bağlama öznitelikleri ile hangi parametre türlerini kullanabileceğinizi açıklar.

### <a name="binding-expressions-example"></a>Bağlama ifadeleri örneği

Aşağıdaki kod, bir uygulama ayarından izlenecek sıranın adını alır ve `insertionTime` parametresindeki sıra iletisi oluşturma zamanını alır.

```csharp
public static class BindingExpressionsExample
{
    [FunctionName("LogQueueMessage")]
    public static void Run(
        [QueueTrigger("%queueappsetting%")] string myQueueItem,
        DateTimeOffset insertionTime,
        ILogger log)
    {
        log.LogInformation($"Message content: {myQueueItem}");
        log.LogInformation($"Created at: {insertionTime}");
    }
}
```

## <a name="autogenerated-functionjson"></a>Otomatik olarak function. JSON

Yapı işlemi yapı klasöründeki bir işlev klasöründe bir *function. JSON* dosyası oluşturur. Daha önce belirtildiği gibi, bu dosyanın doğrudan düzenlenmesi amaçlıyordu. Bu dosyayı düzenleyerek bağlama yapılandırmasını değiştiremez veya işlevi devre dışı bırakabilirsiniz. 

Bu dosyanın amacı, [Tüketim planındaki kararları ölçeklendirirken](functions-scale.md#how-the-consumption-and-premium-plans-work)kullanılacak ölçek denetleyicisine bilgi sağlamaktır. Bu nedenle, dosya yalnızca tetikleyici bilgisine sahiptir, giriş veya çıkış bağlamaları değildir.

Oluşturulan *function. JSON* dosyası, çalışma zamanının *function. JSON* yapılandırması yerine, bağlamalar için .net özniteliklerini kullanmasını söyleyen bir `configurationSource` özelliği içerir. Bir örneği aşağıda verilmiştir:

```json
{
  "generatedBy": "Microsoft.NET.Sdk.Functions-1.0.0.0",
  "configurationSource": "attributes",
  "bindings": [
    {
      "type": "queueTrigger",
      "queueName": "%input-queue-name%",
      "name": "myQueueItem"
    }
  ],
  "disabled": false,
  "scriptFile": "..\\bin\\FunctionApp1.dll",
  "entryPoint": "FunctionApp1.QueueTrigger.Run"
}
```

## <a name="microsoftnetsdkfunctions"></a>Microsoft. NET. SDK. Functions

*Function. JSON* dosya oluşturma, [Microsoft @ NO__T-2net @ No__t-3sdk @ No__t-4functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions)NuGet paketi tarafından gerçekleştirilir. 

Aynı paket, Işlevler çalışma zamanının hem sürüm 1. x hem de 2. x için kullanılır. Hedef Framework, bir 2. x projesinden 1. x projesini farklılaştırır. Farklı hedef çerçeveleri ve aynı `Sdk` paketini gösteren *. csproj* dosyalarının ilgili bölümleri aşağıda verilmiştir:

**İşlevler 1. x**

```xml
<PropertyGroup>
  <TargetFramework>net461</TargetFramework>
</PropertyGroup>
<ItemGroup>
  <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.8" />
</ItemGroup>
```

**İşlevler 2. x**

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.1</TargetFramework>
  <AzureFunctionsVersion>v2</AzureFunctionsVersion>
</PropertyGroup>
<ItemGroup>
  <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.8" />
</ItemGroup>
```

@No__t-0 paket bağımlılıkları arasında Tetikleyiciler ve bağlamalar vardır. 1\. x Projesi 1. x tetikleyicilerine ve bağlamalara başvurur çünkü bu Tetikleyiciler ve bağlamalar .NET Framework, ancak 2. x Tetikleyicileri ve bağlamaları hedef .NET Core ' u hedefler.

@No__t-0 paketi ayrıca, [Newtonsoft. JSON](https://www.nuget.org/packages/Newtonsoft.Json)öğesine ve dolaylı olarak [windowsazure. Storage](https://www.nuget.org/packages/WindowsAzure.Storage)' a bağımlıdır. Bu bağımlılıklar, projenizin, projenin hedeflediği Işlevler çalışma zamanı sürümüyle çalışan paketlerin sürümlerini kullandığından emin olun. Örneğin, `Newtonsoft.Json` .NET Framework 4.6.1 için sürüm 11 ' e sahiptir, ancak .NET Framework 4.6.1 hedefleyen Işlevler çalışma zamanı yalnızca `Newtonsoft.Json` 9.0.1 ile uyumludur. Bu nedenle, bu projedeki işlev kodunuzun de `Newtonsoft.Json` 9.0.1 kullanması gerekir.

@No__t-0 için kaynak kodu, GitHub deposu [Azure @ no__t-2 ' de @ no__t-3vs @ no__t-4build @ no__t-5sdk](https://github.com/Azure/azure-functions-vs-build-sdk)' da kullanılabilir.

## <a name="runtime-version"></a>Çalışma zamanı sürümü

Visual Studio, Işlevler projelerini çalıştırmak için [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) kullanır. Temel araçlar, Işlevler çalışma zamanı için bir komut satırı arabirimidir.

Ana araçları NPM kullanarak yüklerseniz, Visual Studio tarafından kullanılan temel araçlar sürümünü etkilemez. Işlevler çalışma zamanı sürüm 1. x, Visual Studio *%USERPROFILE%\AppData\Local\Azure.Functions.cli* ' deki temel araçlar sürümlerini depolar ve burada depolanan en son sürümü kullanır. 2\. x Işlevleri için, temel araçlar **Azure işlevleri ve Web Işleri araçları** uzantısına dahildir. Hem 1. x hem de 2. x için, bir Işlevler projesi çalıştırdığınızda konsol çıkışında hangi sürümün kullanıldığını görebilirsiniz:

```terminal
[3/1/2018 9:59:53 AM] Starting Host (HostId=contoso2-1518597420, Version=2.0.11353.0, ProcessId=22020, Debug=False, Attempt=0, FunctionsExtensionVersion=)
```

## <a name="supported-types-for-bindings"></a>Bağlamalar için desteklenen türler

Her bağlamanın kendi desteklenen türleri vardır; Örneğin, bir blob tetikleyici özniteliği bir dize parametresine, bir POCO parametresine, `CloudBlockBlob` parametresine veya desteklenen diğer birkaç türden herhangi birine uygulanabilir. [BLOB bağlamaları için bağlama başvuru makalesi](functions-bindings-storage-blob.md#trigger---usage) , desteklenen tüm parametre türlerini listeler. Daha fazla bilgi için, bkz. [Tetikleyiciler ve bağlamalar](functions-triggers-bindings.md) ve [her bağlama türü için bağlama başvurusu belgeleri](functions-triggers-bindings.md#next-steps).

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="binding-to-method-return-value"></a>Metot dönüş değerine bağlama

Özniteliği yöntem dönüş değerine uygulayarak bir çıkış bağlaması için bir yöntem dönüş değeri kullanabilirsiniz. Örnekler için bkz. [Tetikleyiciler ve bağlamalar](./functions-bindings-return-value.md). 

Dönüş değerini yalnızca başarılı bir işlev yürütmesi her zaman çıkış bağlamasına geçirilecek bir dönüş değeri ile sonuçlanırsa kullanın. Aksi takdirde, aşağıdaki bölümde gösterildiği gibi `ICollector` veya `IAsyncCollector` kullanın.

## <a name="writing-multiple-output-values"></a>Birden çok çıkış değeri yazma

Bir çıkış bağlamasına birden çok değer yazmak için veya başarılı bir işlev çağrılması çıkış bağlamasına geçirilecek herhangi bir şeyle sonuçlanmayabilir, [`ICollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) veya [`IAsyncCollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) türlerini kullanın. Bu türler, yöntem tamamlandığında çıkış bağlamaya yazılan salt yazılır koleksiyonlardır.

Bu örnek `ICollector` kullanarak birden çok kuyruk iletisini aynı kuyruğa Yazar:

```csharp
public static class ICollectorExample
{
    [FunctionName("CopyQueueMessageICollector")]
    public static void Run(
        [QueueTrigger("myqueue-items-source-3")] string myQueueItem,
        [Queue("myqueue-items-destination")] ICollector<string> myDestinationQueue,
        ILogger log)
    {
        log.LogInformation($"C# function processed: {myQueueItem}");
        myDestinationQueue.Add($"Copy 1: {myQueueItem}");
        myDestinationQueue.Add($"Copy 2: {myQueueItem}");
    }
}
```

## <a name="logging"></a>Günlüğe kaydetme

Çıktıyı içindeki C#akış günlüklerinizi günlüğe kaydetmek Için [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger)türünde bir bağımsız değişken ekleyin. @No__t-0 olarak, aşağıdaki örnekte olduğu gibi bir ad yazmanız önerilir:  

```csharp
public static class SimpleExample
{
    [FunctionName("QueueTrigger")]
    public static void Run(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        ILogger log)
    {
        log.LogInformation($"C# function processed: {myQueueItem}");
    }
} 
```

Azure Işlevlerinde `Console.Write` kullanmaktan kaçının. Daha fazla bilgi için bkz. **Azure Işlevlerini izleme** makalesindeki [ C# günlük yazma işlevleri](functions-monitoring.md#write-logs-in-c-functions) .

## <a name="async"></a>Eş

Bir işlevi [zaman uyumsuz](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/)yapmak için `async` anahtar sözcüğünü kullanın ve bir `Task` nesnesi döndürün.

```csharp
public static class AsyncExample
{
    [FunctionName("BlobCopy")]
    public static async Task RunAsync(
        [BlobTrigger("sample-images/{blobName}")] Stream blobInput,
        [Blob("sample-images-copies/{blobName}", FileAccess.Write)] Stream blobOutput,
        CancellationToken token,
        ILogger log)
    {
        log.LogInformation($"BlobCopy function processed.");
        await blobInput.CopyToAsync(blobOutput, 4096, token);
    }
}
```

Zaman uyumsuz işlevlerde `out` parametrelerini kullanamazsınız. Çıktı bağlamaları için, bunun yerine [işlev dönüş değeri](#binding-to-method-return-value) veya [toplayıcı nesnesi](#writing-multiple-output-values) kullanın.

## <a name="cancellation-tokens"></a>İptal belirteçleri

Bir işlev bir [CancellationToken](/dotnet/api/system.threading.cancellationtoken) parametresini kabul edebilir ve bu, işlev sonlandırıldıktan sonra işletim sisteminin kodunuza bildirmesini sağlar. Bu bildirimi, işlevin verileri tutarsız bir durumda bırakmak için beklenmedik bir şekilde sonlandığından emin olmak için kullanabilirsiniz.

Aşağıdaki örnek, yaklaşan işlev sonlandırmasının nasıl kontrol alınacağını gösterir.

```csharp
public static class CancellationTokenExample
{
    public static void Run(
        [QueueTrigger("inputqueue")] string inputText,
        TextWriter logger,
        CancellationToken token)
    {
        for (int i = 0; i < 100; i++)
        {
            if (token.IsCancellationRequested)
            {
                logger.WriteLine("Function was cancelled at iteration {0}", i);
                break;
            }
            Thread.Sleep(5000);
            logger.WriteLine("Normal processing for queue message={0}", inputText);
        }
    }
}
```

## <a name="environment-variables"></a>Ortam değişkenleri

Bir ortam değişkeni veya uygulama ayarı değeri almak için, aşağıdaki kod örneğinde gösterildiği gibi `System.Environment.GetEnvironmentVariable` kullanın:

```csharp
public static class EnvironmentVariablesExample
{
    [FunctionName("GetEnvironmentVariables")]
    public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, ILogger log)
    {
        log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
        log.LogInformation(GetEnvironmentVariable("AzureWebJobsStorage"));
        log.LogInformation(GetEnvironmentVariable("WEBSITE_SITE_NAME"));
    }

    public static string GetEnvironmentVariable(string name)
    {
        return name + ": " +
            System.Environment.GetEnvironmentVariable(name, EnvironmentVariableTarget.Process);
    }
}
```

Uygulama ayarları, hem yerel olarak hem de Azure 'da çalışırken ortam değişkenlerinden okunabilir. Yerel olarak geliştirilirken, uygulama ayarları *yerel. Settings. JSON* dosyasında `Values` koleksiyonundan gelir. Her iki ortamda da, yerel ve Azure `GetEnvironmentVariable("<app setting name>")`, adlandırılmış uygulama ayarının değerini alır. Örneğin, yerel olarak çalışırken, *Local. Settings. JSON* dosyanız `{ "Values": { "WEBSITE_SITE_NAME": "My Site Name" } }` Içeriyorsa "Sitem adı" döndürülür.

[System. Configuration. ConfigurationManager. AppSettings](https://docs.microsoft.com/dotnet/api/system.configuration.configurationmanager.appsettings) özelliği, uygulama ayarı değerlerini almaya yönelik ALTERNATIF bir API 'dir, ancak burada gösterildiği gibi `GetEnvironmentVariable` ' i kullanmanızı öneririz.

## <a name="binding-at-runtime"></a>Çalışma zamanında bağlama

Ve diğer .NET dillerinde, özniteliklerde [*bildirim temelli*](https://en.wikipedia.org/wiki/Declarative_programming) bağlamaların aksine, tanımlayıcı bağlama modelini kullanabilirsiniz. [](https://en.wikipedia.org/wiki/Imperative_programming) C# Bağlama parametrelerinin tasarım zamanı yerine çalışma zamanında hesaplanması gerektiğinde, kesinlik temelli bağlama kullanışlıdır. Bu düzende, işlev kodunuzda, desteklenen giriş ve çıkış bağlamalarına bağlanabilirsiniz.

Zorunlu bir bağlamayı aşağıdaki gibi tanımlayın:

- İstediğiniz kesinlik bağlamalarınız için işlev imzasına bir **öznitelik eklemeyin.**
- [@No__t-1](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) veya [`IBinder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs)giriş parametresini geçirin.
- Veri bağlamayı gerçekleştirmek C# için aşağıdaki kalıbı kullanın.

  ```cs
  using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
  {
      ...
  }
  ```

  `BindingTypeAttribute`, bağlamızı tanımlayan .NET özniteliğidir ve `T`, bu bağlama türü tarafından desteklenen bir giriş veya çıkış türüdür. `T` `out` parametre türü olamaz (`out JObject` gibi). Örneğin, Mobile Apps tablo çıkış bağlaması [altı çıkış türünü](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22)destekler, ancak yalnızca [ICollector @ No__t-2T >](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) veya [ıasynccollector @ no__t-4T >](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) seçeneğini kesinlik ile kullanabilirsiniz.

### <a name="single-attribute-example"></a>Tek öznitelik örneği

Aşağıdaki örnek kod, çalışma zamanında tanımlanmış blob yolu ile bir [Depolama Blobu çıkış bağlaması](functions-bindings-storage-blob.md#output) oluşturur ve sonra blob 'a bir dize yazar.

```cs
public static class IBinderExample
{
    [FunctionName("CreateBlobUsingBinder")]
    public static void Run(
        [QueueTrigger("myqueue-items-source-4")] string myQueueItem,
        IBinder binder,
        ILogger log)
    {
        log.LogInformation($"CreateBlobUsingBinder function processed: {myQueueItem}");
        using (var writer = binder.Bind<TextWriter>(new BlobAttribute(
                    $"samples-output/{myQueueItem}", FileAccess.Write)))
        {
            writer.Write("Hello World!");
        };
    }
}
```

[Blobattribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs) , [Depolama Blobu](functions-bindings-storage-blob.md) girişini veya çıkış bağlamasını tanımlar ve [TextWriter](/dotnet/api/system.io.textwriter) desteklenen bir çıkış bağlama türüdür.

### <a name="multiple-attribute-example"></a>Birden çok öznitelik örneği

Yukarıdaki örnek, işlev uygulamasının ana depolama hesabı bağlantı dizesi (`AzureWebJobsStorage`) için uygulama ayarını alır. [Storageaccountattribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) öğesini ekleyerek ve öznitelik dizisini `BindAsync<T>()` ' e geçirerek depolama hesabı için kullanılacak özel bir uygulama ayarı belirtebilirsiniz. @No__t-1 değil `Binder` parametresini kullanın.  Örnek:

```cs
public static class IBinderExampleMultipleAttributes
{
    [FunctionName("CreateBlobInDifferentStorageAccount")]
    public async static Task RunAsync(
            [QueueTrigger("myqueue-items-source-binder2")] string myQueueItem,
            Binder binder,
            ILogger log)
    {
        log.LogInformation($"CreateBlobInDifferentStorageAccount function processed: {myQueueItem}");
        var attributes = new Attribute[]
        {
        new BlobAttribute($"samples-output/{myQueueItem}", FileAccess.Write),
        new StorageAccountAttribute("MyStorageAccount")
        };
        using (var writer = await binder.BindAsync<TextWriter>(attributes))
        {
            await writer.WriteAsync("Hello World!!");
        }
    }
}
```

## <a name="triggers-and-bindings"></a>Tetikleyiciler ve bağlamalar 

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Tetikleyiciler ve bağlamalar hakkında daha fazla bilgi](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Azure Işlevleri için en iyi uygulamalar hakkında daha fazla bilgi edinin](functions-best-practices.md)
