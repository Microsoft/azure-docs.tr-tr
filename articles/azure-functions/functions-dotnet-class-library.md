---
title: Azure Işlevleri 'ni kullanarak C# sınıf kitaplığı işlevleri geliştirme
description: C# kullanarak Azure Işlevleri çalışma zamanı ile işleme içinde çalışan sınıf kitaplıkları olarak kod geliştirme ve yayımlama hakkında bilgi edinin.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 07/24/2020
ms.openlocfilehash: c7d14599ec1ebbcb94e0c0f3985a3b857f9353dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102563889"
---
# <a name="develop-c-class-library-functions-using-azure-functions"></a>Azure Işlevleri 'ni kullanarak C# sınıf kitaplığı işlevleri geliştirme

<!-- When updating this article, make corresponding changes to any duplicate content in functions-reference-csharp.md -->

Bu makale, .NET sınıf kitaplıklarında C# kullanarak Azure Işlevleri geliştirmeye giriş niteliğindedir.

>[!IMPORTANT]
>Bu makale, çalışma zamanında işlem içinde çalışan .NET sınıf kitaplığı işlevlerini destekler. İşlevler ayrıca, C# işlevlerinizi işlem dışı ve çalışma zamanından yalıtılmış olarak çalıştırarak .NET 5. x ' i destekler. Daha fazla bilgi için bkz. [.net yalıtılmış işlem işlevleri](dotnet-isolated-process-guide.md).

Bir C# geliştiricisi olarak, aşağıdaki makalelerden biriyle de ilgileniyor olabilirsiniz:

| Kullanmaya başlama | Kavramlar| Kılavuzlu öğrenme/örnekler |
|--| -- |--| 
| <ul><li>[Visual Studio’yu kullanma](functions-create-your-first-function-visual-studio.md)</li><li>[Visual Studio Code’u kullanma](create-first-function-vs-code-csharp.md)</li><li>[Komut satırı araçlarını kullanma](create-first-function-cli-csharp.md)</li></ul> | <ul><li>[Barındırma seçenekleri](functions-scale.md)</li><li>[Performans &nbsp; konuları](functions-best-practices.md)</li><li>[Visual Studio geliştirme](functions-develop-vs.md)</li><li>[Bağımlılık ekleme](functions-dotnet-dependency-injection.md)</li></ul> | <ul><li>[Sunucusuz uygulamalar oluşturma](/learn/paths/create-serverless-applications/)</li><li>[C# örnekleri](/samples/browse/?products=azure-functions&languages=csharp)</li></ul> |

Azure Işlevleri C# ve C# betiği programlama dillerini destekler. [Azure Portal c# kullanma](functions-create-function-app-portal.md)hakkında rehberlik arıyorsanız bkz. [c# betiği (. CSX) geliştirici başvurusu](functions-reference-csharp.md).

## <a name="supported-versions"></a>Desteklenen sürümler

Işlevler çalışma zamanının sürümleri .NET 'in belirli sürümleriyle çalışır. Işlev sürümleri hakkında daha fazla bilgi edinmek için bkz. [Azure işlevleri çalışma zamanı sürümlerine genel bakış](functions-versions.md)

Aşağıdaki tabloda, belirli bir Işlev sürümü ile kullanılabilecek en yüksek düzeyde .NET Core veya .NET Framework gösterilmektedir. 

| İşlevler çalışma zamanı sürümü | En yüksek .NET sürümü |
| ---- | ---- |
| İşlevler 3. x | .NET Core 3.1<br/>.NET 5,0<sup>1</sup> |
| İşlevler 2.x | .NET Core 2,2<sup>2</sup> |
| İşlevler 1.x |  .NET Framework 4.7 |

<sup>1</sup> [işlem dışı](dotnet-isolated-process-guide.md)çalışmalıdır.  
<sup>2</sup> Ayrıntılar için bkz. [işlevler v2. x konuları](#functions-v2x-considerations).   

Belirli eski küçük sürümlerin kaldırılması dahil olmak üzere Azure Işlevleri ile ilgili en son haberler için [Azure App Service bildirilerini](https://github.com/Azure/app-service-announcements/issues)izleyin.

### <a name="functions-v2x-considerations"></a>İşlevler v2. x konuları

En son 2. x sürümünü () hedefleyen işlev uygulamaları, `~2` .NET Core 3,1 ' de çalışacak şekilde otomatik olarak yükseltilir. .NET Core sürümleri arasındaki önemli değişiklikler nedeniyle, .NET Core 2,2 ile geliştirilen ve derlenen tüm uygulamalar güvenli bir şekilde .NET Core 3,1 sürümüne yükseltilebilir. İşlev uygulamanızı öğesine sabitleyerek Bu yükseltmeyi devre dışı bırakabilirsiniz `~2.0` . İşlevler ayrıca uyumsuz API 'Leri algılar ve `~2.0` .NET Core 3,1 ' de yanlış yürütmeyi engellemek için uygulamanızı ' a sabitleyebilir. 

>[!NOTE]
>İşlev uygulamanız sabitlenir `~2.0` ve bu sürüm hedefini olarak değiştirirseniz `~2` , işlev uygulamanız kesintiye uğramayabilir. ARM şablonları kullanarak dağıtıyorsanız, şablonlarınızın sürümünü kontrol edin. Bu durumda, sürümünüzü hedefle olarak değiştirin `~2.0` ve uyumluluk sorunlarını giderin. 

Hedeflenen işlev uygulamaları `~2.0` .NET Core 2,2 ' de çalıştırılmaya devam eder. .NET Core 'un bu sürümü artık güvenlik ve diğer bakım güncelleştirmelerini almaz. Daha fazla bilgi edinmek için [Bu duyuru sayfasına](https://github.com/Azure/app-service-announcements/issues/266)bakın. 

İşlevlerinizi .NET Core 3,1 ile uyumlu hale getirmek için en kısa sürede çalışmanız gerekir. Bu sorunları çözdükten sonra sürümünüzü yeniden olarak değiştirin `~2` veya sürümüne yükseltin `~3` . Işlevler çalışma zamanının hedeflenen sürümlerini hakkında daha fazla bilgi edinmek için bkz. [Azure işlevleri çalışma zamanı sürümlerini](set-runtime-version.md)hedefleme.

Bir Premium veya adanmış (App Service) planında Linux üzerinde çalışırken, nasıl ayarlanacağını öğrenmek için site yapılandırma ayarını olarak ayarlayarak belirli bir görüntüyü hedefleyerek sürümünüzü sabitleyerek `linuxFxVersion` `DOCKER|mcr.microsoft.com/azure-functions/dotnet:2.0.14786-appservice` `linuxFxVersion` , [Linux üzerinde el ile sürüm güncelleştirmeleri](set-runtime-version.md#manual-version-updates-on-linux)bölümüne bakın.

## <a name="functions-class-library-project"></a>İşlevler sınıf kitaplığı projesi

Visual Studio 'da **Azure işlevleri** proje şablonu, aşağıdaki dosyaları Içeren bir C# sınıf kitaplığı projesi oluşturur:

* [host.js](functions-host-json.md) , yerel olarak veya Azure 'da çalışırken projedeki tüm işlevleri etkileyen yapılandırma ayarlarını depolar.
* [local.settings.js](functions-run-local.md#local-settings-file) , yerel olarak çalışırken kullanılan uygulama ayarlarını ve bağlantı dizelerini depolar. Bu dosya gizli dizileri içerir ve Azure 'da işlev uygulamanıza yayımlanmaz. Bunun yerine, [uygulama ayarlarını işlev uygulamanıza ekleyin](functions-develop-vs.md#function-app-settings).

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
> Yapı işlemi her bir işlev için dosyada bir *function.js* oluşturur. Dosyadaki bu *function.js* doğrudan düzenlenmeyecek. Bu dosyayı düzenleyerek bağlama yapılandırmasını değiştiremez veya işlevi devre dışı bırakabilirsiniz. Bir işlevi nasıl devre dışı bırakacağınızı öğrenmek için bkz. [işlevleri devre dışı bırakma](disable-function.md).


## <a name="methods-recognized-as-functions"></a>İşlev olarak tanınan Yöntemler

Bir sınıf kitaplığında, bir işlev, `FunctionName` Aşağıdaki örnekte gösterildiği gibi, ve bir tetikleyici özniteliği olan statik bir yöntemdir:

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

`FunctionName`Özniteliği yöntemi bir işlev giriş noktası olarak işaretler. Ad bir proje içinde benzersiz olmalıdır, bir harfle başlamalı ve yalnızca harf, sayı, `_` ve `-` , en fazla 127 karakter içermelidir. Proje şablonları genellikle adlı bir yöntem oluşturur `Run` , ancak yöntem adı geçerli bir C# Yöntem adı olabilir.

Tetikleyici özniteliği Tetikleyici türünü belirtir ve giriş verilerini bir yöntem parametresine bağlar. Örnek işlev bir kuyruk iletisi tarafından tetiklenir ve kuyruk iletisi, parametresindeki yöntemine geçirilir `myQueueItem` .

## <a name="method-signature-parameters"></a>Yöntem imza parametreleri

Yöntem imzası, tetikleyici özniteliğiyle kullanılandan farklı parametreler içeriyor olabilir. Şunları dahil edebilirsiniz:

* [Giriş ve çıkış bağlamaları](functions-triggers-bindings.md) öznitelikleri ile süsleyerek bu şekilde işaretlenir.  
* `ILogger` `TraceWriter` [Günlüğe kaydetme](#logging)için bir veya ([Sürüm 1. x-yalnızca sürüm](functions-versions.md#creating-1x-apps)) parametresi.
* `CancellationToken` [Düzgün kapanma](#cancellation-tokens)için bir parametre.
* Tetikleyici meta verilerini almak için [ifade parametreleri bağlama](./functions-bindings-expressions-patterns.md) .

İşlev imzasında parametrelerin sırası sizin için değildir. Örneğin, tetikleme parametrelerini diğer bağlamalardan önce veya sonra koyabilirsiniz ve tetikleyici veya bağlama parametrelerinden önce veya sonra günlükçü parametresini yerleştirebilirsiniz.

### <a name="output-bindings"></a>Çıkış bağlamaları

Bir işlev, çıkış parametreleri kullanılarak tanımlanmış sıfır veya bir çıkış bağlaması olabilir. 

Aşağıdaki örnek, adlı bir çıkış sırası bağlaması ekleyerek önceki bir adı değiştirir `myQueueItemCopy` . İşlevi, işlevi tetikleyen iletinin içeriğini farklı bir kuyruktaki yeni bir iletiye yazar.

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

Çıkış bağlamalarına atanan değerler, işlev çıktığında yazılır. Yalnızca birden çok çıkış parametresine değer atayarak, bir işlevde birden fazla çıkış bağlaması kullanabilirsiniz. 

Bağlama başvuru makaleleri (örneğin,[depolama kuyrukları](functions-bindings-storage-queue.md)) tetikleyici, giriş veya çıkış bağlama öznitelikleri ile hangi parametre türlerini kullanabileceğinizi açıklar.

### <a name="binding-expressions-example"></a>Bağlama ifadeleri örneği

Aşağıdaki kod, bir uygulama ayarından izlenecek sıranın adını alır ve parametresindeki sıra iletisi oluşturma zamanını alır `insertionTime` .

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

## <a name="autogenerated-functionjson"></a>function.jsotomatik olarak oluşturulur

Yapı işlemi, derleme klasöründeki bir işlev klasöründe bir *function.js* dosya oluşturur. Daha önce belirtildiği gibi, bu dosyanın doğrudan düzenlenmesi amaçlıyordu. Bu dosyayı düzenleyerek bağlama yapılandırmasını değiştiremez veya işlevi devre dışı bırakabilirsiniz. 

Bu dosyanın amacı, [Tüketim planındaki kararları ölçeklendirirken](event-driven-scaling.md)kullanılacak ölçek denetleyicisine bilgi sağlamaktır. Bu nedenle, dosya yalnızca tetikleyici bilgisine sahiptir, giriş/çıkış bağlamaları değildir.

Dosyada oluşturulan *function.js* , `configurationSource` çalışma zamanına yapılandırma *function.js* yerine bağlama için .net öznitelikleri kullanmasını söyleyen bir özelliği içerir. Aşağıda bir örnek verilmiştir:

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

Dosya oluşturma *function.js* , NuGet paketi [Microsoft \. net \. SDK \. işlevleri](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions)tarafından gerçekleştirilir. 

Aynı paket, Işlevler çalışma zamanının hem sürüm 1. x hem de 2. x için kullanılır. Hedef Framework, bir 2. x projesinden 1. x projesini farklılaştırır. Aynı pakete sahip farklı hedef çerçeveleri gösteren *. csproj* dosyalarının ilgili bölümleri aşağıda verilmiştir `Sdk` :

# <a name="v2x"></a>[v2. x +](#tab/v2)

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.1</TargetFramework>
  <AzureFunctionsVersion>v2</AzureFunctionsVersion>
</PropertyGroup>
<ItemGroup>
  <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.8" />
</ItemGroup>
```

# <a name="v1x"></a>[v1. x](#tab/v1)

```xml
<PropertyGroup>
  <TargetFramework>net461</TargetFramework>
</PropertyGroup>
<ItemGroup>
  <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.8" />
</ItemGroup>
```
---


`Sdk`Paket bağımlılıkları arasında Tetikleyiciler ve bağlamalar vardır. 1. x Projesi 1. x tetikleyicilerine ve bağlamalara başvurur çünkü bu Tetikleyiciler ve bağlamalar .NET Framework, ancak 2. x Tetikleyicileri ve bağlamaları hedef .NET Core ' u hedefler.

`Sdk`Paket Ayrıca, [windowsazure. Storage](https://www.nuget.org/packages/WindowsAzure.Storage)üzerinde [Newtonsoft.Js](https://www.nuget.org/packages/Newtonsoft.Json)ve dolaylı olarak değişir. Bu bağımlılıklar, projenizin, projenin hedeflediği Işlevler çalışma zamanı sürümüyle çalışan paketlerin sürümlerini kullandığından emin olun. Örneğin, `Newtonsoft.Json` .NET Framework 4.6.1 için sürüm 11 ' e sahiptir, ancak .NET Framework 4.6.1 hedefleyen işlevler çalışma zamanı yalnızca 9.0.1 ile uyumludur `Newtonsoft.Json` . Bu nedenle, bu projedeki işlev kodunuzun de 9.0.1 kullanması gerekir `Newtonsoft.Json` .

İçin kaynak kodu, `Microsoft.NET.Sdk.Functions` GitHub deposu [Azure \- işlevleri \- vs \- derlemesi \- SDK 'sında](https://github.com/Azure/azure-functions-vs-build-sdk)bulunabilir.

## <a name="runtime-version"></a>Çalışma zamanı sürümü

Visual Studio, Işlevler projelerini çalıştırmak için [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) kullanır. Temel araçlar, Işlevler çalışma zamanı için bir komut satırı arabirimidir.

Ana araçları NPM kullanarak yüklerseniz, Visual Studio tarafından kullanılan temel araçlar sürümünü etkilemez. Işlevler çalışma zamanı sürüm 1. x, Visual Studio *%USERPROFILE%\AppData\Local\Azure.Functions.cli* ' deki temel araçlar sürümlerini depolar ve burada depolanan en son sürümü kullanır. 2. x Işlevleri için, temel araçlar **Azure işlevleri ve Web Işleri araçları** uzantısına dahildir. Hem 1. x hem de 2. x için, bir Işlevler projesi çalıştırdığınızda konsol çıkışında hangi sürümün kullanıldığını görebilirsiniz:

```terminal
[3/1/2018 9:59:53 AM] Starting Host (HostId=contoso2-1518597420, Version=2.0.11353.0, ProcessId=22020, Debug=False, Attempt=0, FunctionsExtensionVersion=)
```

## <a name="readytorun"></a>ReadyToRun

İşlev uygulamanızı [Readytorun ikilileri](/dotnet/core/whats-new/dotnet-core-3-0#readytorun-images)olarak derleyebilirsiniz. ReadyToRun, bir [Tüketim planında](consumption-plan.md)çalışırken [soğuk başlatmanın](event-driven-scaling.md#cold-start) etkisini azaltmaya yardımcı olmak için başlangıç performansını iyileştirebilecek bir süre öncesi derleme biçimidir.

ReadyToRun, .NET 3,0 ' de kullanılabilir ve [Azure işlevleri çalışma zamanının 3,0 sürümünü](functions-versions.md)gerektirir.

Projenizi ReadyToRun olarak derlemek için, ve öğelerini ekleyerek proje dosyanızı güncelleştirin `<PublishReadyToRun>` `<RuntimeIdentifier>` . Aşağıda Windows 32-bit işlev uygulamasına yayımlama yapılandırması verilmiştir.

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp3.1</TargetFramework>
  <AzureFunctionsVersion>v3</AzureFunctionsVersion>
  <PublishReadyToRun>true</PublishReadyToRun>
  <RuntimeIdentifier>win-x86</RuntimeIdentifier>
</PropertyGroup>
```

> [!IMPORTANT]
> ReadyToRun Şu anda çapraz derlemeyi desteklemiyor. Uygulamanızı dağıtım hedefi ile aynı platformda derlemeniz gerekir. Ayrıca, işlev uygulamanızda yapılandırılan "bit genişliği" ile ilgilenyin. Örneğin, Azure 'daki işlev uygulamanız Windows 64-bit ise, uygulamanızı `win-x64` [çalışma zamanı tanımlayıcısı](/dotnet/core/rid-catalog)olarak Windows üzerinde derlemeniz gerekir.

Uygulamanızı komut satırından ReadyToRun ile de oluşturabilirsiniz. Daha fazla bilgi için, `-p:PublishReadyToRun=true` içindeki seçeneğine bakın [`dotnet publish`](/dotnet/core/tools/dotnet-publish) .

## <a name="supported-types-for-bindings"></a>Bağlamalar için desteklenen türler

Her bağlamanın kendi desteklenen türleri vardır; Örneğin, bir blob tetikleyici özniteliği bir dize parametresine, bir POCO parametresine, `CloudBlockBlob` parametreye veya desteklenen başka birkaç türden birine uygulanabilir. [BLOB bağlamaları için bağlama başvuru makalesi](functions-bindings-storage-blob-trigger.md#usage) , desteklenen tüm parametre türlerini listeler. Daha fazla bilgi için, bkz. [Tetikleyiciler ve bağlamalar](functions-triggers-bindings.md) ve [her bağlama türü için bağlama başvurusu belgeleri](functions-triggers-bindings.md#next-steps).

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="binding-to-method-return-value"></a>Metot dönüş değerine bağlama

Özniteliği yöntem dönüş değerine uygulayarak bir çıkış bağlaması için bir yöntem dönüş değeri kullanabilirsiniz. Örnekler için bkz. [Tetikleyiciler ve bağlamalar](./functions-bindings-return-value.md). 

Dönüş değerini yalnızca başarılı bir işlev yürütmesi her zaman çıkış bağlamasına geçirilecek bir dönüş değeri ile sonuçlanırsa kullanın. Aksi takdirde, `ICollector` `IAsyncCollector` aşağıdaki bölümde gösterildiği gibi, veya kullanın.

## <a name="writing-multiple-output-values"></a>Birden çok çıkış değeri yazma

Bir çıkış bağlamasına birden çok değer yazmak için veya başarılı bir işlev çağrılması çıkış bağlamasına geçirilecek herhangi bir şeye neden olmazsa, [`ICollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) veya [`IAsyncCollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) türlerini kullanın. Bu türler, yöntem tamamlandığında çıkış bağlamaya yazılan salt yazılır koleksiyonlardır.

Bu örnek, kullanarak birden çok kuyruk iletisini aynı kuyruğa yazar `ICollector` :

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

## <a name="async"></a>Zaman Uyumsuz

Bir işlevi [zaman uyumsuz](/dotnet/csharp/programming-guide/concepts/async/)yapmak için `async` anahtar sözcüğünü kullanın ve bir `Task` nesne döndürün.

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

`out`Zaman uyumsuz işlevlerde parametreleri kullanamazsınız. Çıktı bağlamaları için, bunun yerine [işlev dönüş değeri](#binding-to-method-return-value) veya [toplayıcı nesnesi](#writing-multiple-output-values) kullanın.

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

## <a name="logging"></a>Günlüğe Kaydetme

İşlev kodunuzda, Application Insights izleme olarak görünen günlüklere çıkış yazabilirsiniz. Günlüklere yazmak için önerilen yol, genellikle adlı [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger)türünde bir parametre içerir `log` . `TraceWriter`Aynı zamanda Application Insights yazan, ancak yapısal günlüğü desteklemeyen işlevler çalışma zamanının sürüm 1. x. `Console.Write`Bu veriler Application Insights tarafından yakalandığından günlüklerinizi yazmak için kullanmayın. 

### <a name="ilogger"></a>ILogger

İşlev tanımınızda, [yapılandırılmış günlüğe kaydetmeyi](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)destekleyen bir [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger) parametresi ekleyin.

Bir `ILogger` nesnesi ile, `Log<level>` günlük oluşturmak Için [ILogger üzerinde uzantı yöntemleri](/dotnet/api/microsoft.extensions.logging.loggerextensions#methods) çağırın. Aşağıdaki kod şu `Information` kategoriye sahip günlüklere yazar `Function.<YOUR_FUNCTION_NAME>.User.` :

```cs
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger logger)
{
    logger.LogInformation("Request for item with key={itemKey}.", id);
```

Işlevlerin nasıl uyguladığı hakkında daha fazla bilgi edinmek için `ILogger` bkz. [telemetri verilerini toplama](functions-monitoring.md#collecting-telemetry-data). Ön Ekli kategoriler `Function` bir örnek kullandığınızı varsayar `ILogger` . Bunun yerine bir kullanmayı tercih ederseniz `ILogger<T>` Kategori adı temel alabilir `T` .  

### <a name="structured-logging"></a>Yapılandırılmış günlüğe kaydetme

Adları değil, yer tutucular sırası, günlük iletisinde kullanılan parametreleri belirler. Aşağıdaki koda sahip olduğunuzu varsayalım:

```csharp
string partitionKey = "partitionKey";
string rowKey = "rowKey";
logger.LogInformation("partitionKey={partitionKey}, rowKey={rowKey}", partitionKey, rowKey);
```

Aynı ileti dizesini tutar ve parametrelerin sırasını ters tutarsanız, sonuçta elde edilen ileti metninde yanlış yerlerde değerler olacaktır.

Yer tutucular bu şekilde işlenir, böylece yapılandırılmış günlüğe kaydetme yapabilirsiniz. Application Insights, ad-değer çiftlerini ve ileti dizesini depolayan parametre. Sonuç olarak, ileti bağımsız değişkenlerinin sorgulayabilmeniz için alanlar haline gelir.

Günlükçü yöntemi çağrın bir önceki örneğe benzei durumunda, alanı sorgulayabilirsiniz `customDimensions.prop__rowKey` . `prop__`Çalışma zamanı eklemeleri ve işlev kodunuzun eklediği alanlar arasında çakışma olmadığından emin olmak için ön ek eklenir.

Ayrıca, alanına başvurarak özgün ileti dizesinde sorgulama yapabilirsiniz `customDimensions.prop__{OriginalFormat}` .  

Aşağıda, verilerin JSON örnek bir gösterimi verilmiştir `customDimensions` :

```json
{
  "customDimensions": {
    "prop__{OriginalFormat}":"C# Queue trigger function processed: {message}",
    "Category":"Function",
    "LogLevel":"Information",
    "prop__message":"c9519cbf-b1e6-4b9b-bf24-cb7d10b1bb89"
  }
}
```

### <a name="log-custom-telemetry"></a><a name="log-custom-telemetry-in-c-functions"></a>Özel telemetri günlüğe kaydet

Application Insights SDK 'nın İşlevlerinizden Özel telemetri verilerini Application Insights: [Microsoft. Azure. WebJobs. Logging. ApplicationInsights](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Logging.ApplicationInsights)'a göndermek Için kullanabileceğiniz işlevlere özgü bir sürümü vardır. Bu paketi yüklemek için komut isteminde aşağıdaki komutu kullanın:

# <a name="command"></a>[Komut](#tab/cmd)

```cmd
dotnet add package Microsoft.Azure.WebJobs.Logging.ApplicationInsights --version <VERSION>
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -Version <VERSION>
```

---

Bu komutta, ' nin `<VERSION>` yüklü [Microsoft. Azure. WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs/)sürümünüzü destekleyen bir sürümü ile değiştirin. 

Aşağıdaki C# örnekleri [özel TELEMETRI API](../azure-monitor/app/api-custom-events-metrics.md)'sini kullanır. Örnek bir .NET sınıf kitaplığı içindir, ancak Application Insights kodu C# betiği için aynıdır.

# <a name="v2x"></a>[v2. x +](#tab/v2)

Çalışma zamanının sürüm 2. x ve sonraki sürümleri, geçerli işlemle Telemetriyi otomatik olarak ilişkilendirmek için Application Insights yeni özellikleri kullanır. İşlem `Id` , veya alanlarını el ile ayarlamanız gerekmez `ParentId` `Name` .

```cs
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;

using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
using System.Linq;

namespace functionapp0915
{
    public class HttpTrigger2
    {
        private readonly TelemetryClient telemetryClient;

        /// Using dependency injection will guarantee that you use the same configuration for telemetry collected automatically and manually.
        public HttpTrigger2(TelemetryConfiguration telemetryConfiguration)
        {
            this.telemetryClient = new TelemetryClient(telemetryConfiguration);
        }

        [FunctionName("HttpTrigger2")]
        public Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", Route = null)]
            HttpRequest req, ExecutionContext context, ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            DateTime start = DateTime.UtcNow;

            // Parse query parameter
            string name = req.Query
                .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
                .Value;

            // Write an event to the customEvents table.
            var evt = new EventTelemetry("Function called");
            evt.Context.User.Id = name;
            this.telemetryClient.TrackEvent(evt);

            // Generate a custom metric, in this case let's use ContentLength.
            this.telemetryClient.GetMetric("contentLength").TrackValue(req.ContentLength);

            // Log a custom dependency in the dependencies table.
            var dependency = new DependencyTelemetry
            {
                Name = "GET api/planets/1/",
                Target = "swapi.co",
                Data = "https://swapi.co/api/planets/1/",
                Timestamp = start,
                Duration = DateTime.UtcNow - start,
                Success = true
            };
            dependency.Context.User.Id = name;
            this.telemetryClient.TrackDependency(dependency);

            return Task.FromResult<IActionResult>(new OkResult());
        }
    }
}
```

Bu örnekte, özel ölçüm verileri, Customölçümler tablosuna gönderilmeden önce ana bilgisayar tarafından toplanır. Daha fazla bilgi edinmek için Application Insights içindeki [GetMetric](../azure-monitor/app/api-custom-events-metrics.md#getmetric) belgelerine bakın. 

Yerel olarak çalışırken, `APPINSIGHTS_INSTRUMENTATIONKEY` Application Insights anahtarı ile ayarını [local.settings.js](functions-run-local.md#local-settings-file) dosyasına eklemeniz gerekir.


# <a name="v1x"></a>[v1. x](#tab/v1)

```cs
using System;
using System.Net;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.Azure.WebJobs;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Extensions.Logging;
using System.Linq;

namespace functionapp0915
{
    public static class HttpTrigger2
    {
        private static string key = TelemetryConfiguration.Active.InstrumentationKey = 
            System.Environment.GetEnvironmentVariable(
                "APPINSIGHTS_INSTRUMENTATIONKEY", EnvironmentVariableTarget.Process);

        private static TelemetryClient telemetryClient = 
            new TelemetryClient() { InstrumentationKey = key };

        [FunctionName("HttpTrigger2")]
        public static async Task<HttpResponseMessage> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
            HttpRequestMessage req, ExecutionContext context, ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            DateTime start = DateTime.UtcNow;

            // Parse query parameter
            string name = req.GetQueryNameValuePairs()
                .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
                .Value;

            // Get request body
            dynamic data = await req.Content.ReadAsAsync<object>();

            // Set name to query string or body data
            name = name ?? data?.name;
         
            // Track an Event
            var evt = new EventTelemetry("Function called");
            UpdateTelemetryContext(evt.Context, context, name);
            telemetryClient.TrackEvent(evt);
            
            // Track a Metric
            var metric = new MetricTelemetry("Test Metric", DateTime.Now.Millisecond);
            UpdateTelemetryContext(metric.Context, context, name);
            telemetryClient.TrackMetric(metric);
            
            // Track a Dependency
            var dependency = new DependencyTelemetry
            {
                Name = "GET api/planets/1/",
                Target = "swapi.co",
                Data = "https://swapi.co/api/planets/1/",
                Timestamp = start,
                Duration = DateTime.UtcNow - start,
                Success = true
            };
            UpdateTelemetryContext(dependency.Context, context, name);
            telemetryClient.TrackDependency(dependency);
        }
        
        // Correlate all telemetry with the current Function invocation
        private static void UpdateTelemetryContext(TelemetryContext context, ExecutionContext functionContext, string userName)
        {
            context.Operation.Id = functionContext.InvocationId.ToString();
            context.Operation.ParentId = functionContext.InvocationId.ToString();
            context.Operation.Name = functionContext.FunctionName;
            context.User.Id = userName;
        }
    }    
}
```
---

`TrackRequest` `StartOperation<RequestTelemetry>` Bir işlev çağrısı için yinelenen istekler göreceğiniz için veya çağrısı yapmayın.  Işlevler çalışma zamanı istekleri otomatik olarak izler.

Ayarlanmadı `telemetryClient.Context.Operation.Id` . Aynı anda çok sayıda işlev çalışırken bu genel ayar yanlış bağıntı oluşmasına neden olur. Bunun yerine, yeni bir telemetri örneği ( `DependencyTelemetry` , `EventTelemetry` ) oluşturun ve `Context` özelliğini değiştirin. Ardından Telemetri örneğini `Track` `TelemetryClient` ( `TrackDependency()` , `TrackEvent()` ,) üzerinde ilgili yönteme geçirin `TrackMetric()` . Bu yöntem, telemetrinin geçerli işlev çağırma için doğru bağıntı ayrıntılarına sahip olmasını sağlar.


## <a name="environment-variables"></a>Ortam değişkenleri

Bir ortam değişkeni veya bir uygulama ayarı değeri almak için, `System.Environment.GetEnvironmentVariable` Aşağıdaki kod örneğinde gösterildiği gibi kullanın:

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

    private static string GetEnvironmentVariable(string name)
    {
        return name + ": " +
            System.Environment.GetEnvironmentVariable(name, EnvironmentVariableTarget.Process);
    }
}
```

Uygulama ayarları, hem yerel olarak hem de Azure 'da çalışırken ortam değişkenlerinden okunabilir. Yerel olarak geliştirme yaparken, uygulama ayarları `Values` dosyadaki *local.settings.js* koleksiyonundan gelir. Yerel ve Azure her iki ortamda da `GetEnvironmentVariable("<app setting name>")` adlandırılmış uygulama ayarının değerini alır. Örneğin, yerel olarak çalışırken, dosyasında *local.settings.js* içeriyorsa "Sitem adı" döndürülür `{ "Values": { "WEBSITE_SITE_NAME": "My Site Name" } }` .

[System.Configuration.ConfigurationManager. AppSettings](/dotnet/api/system.configuration.configurationmanager.appsettings) özelliği, uygulama ayarı değerlerini almaya yönelik ALTERNATIF bir API 'dir, ancak `GetEnvironmentVariable` burada gösterildiği gibi kullanmanızı öneririz.

## <a name="binding-at-runtime"></a>Çalışma zamanında bağlama

C# ve diğer .NET dillerinde, öznitelikler içindeki [*bildirime dayalı*](https://en.wikipedia.org/wiki/Declarative_programming) bağlamalardan farklı olarak, [zorunlu bir bağlama](https://en.wikipedia.org/wiki/Imperative_programming) deseninin kullanılmasını sağlayabilirsiniz. Bağlama parametrelerinin tasarım zamanı yerine çalışma zamanında hesaplanması gerektiğinde, kesinlik temelli bağlama kullanışlıdır. Bu düzende, işlev kodunuzda, desteklenen giriş ve çıkış bağlamalarına bağlanabilirsiniz.

Zorunlu bir bağlamayı aşağıdaki gibi tanımlayın:

- İstediğiniz kesinlik bağlamalarınız için işlev imzasına bir **öznitelik eklemeyin.**
- Bir giriş parametresi veya olarak [`Binder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) geçirin [`IBinder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs) .
- Veri bağlamayı gerçekleştirmek için aşağıdaki C# stilini kullanın.

  ```cs
  using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
  {
      ...
  }
  ```

  `BindingTypeAttribute` , bağlamalarınızı tanımlayan .NET özniteliğidir ve `T` Bu bağlama türü tarafından desteklenen bir giriş veya çıkış türüdür. `T` bir `out` parametre türü (gibi `out JObject` ) olamaz. Örneğin, Mobile Apps tablo çıkış bağlaması [altı çıkış türünü](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22)destekler, ancak yalnızca [ICollector \<T> ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) veya [ıasynccollector \<T> ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) öğesini kesinlik ile kullanabilirsiniz.

### <a name="single-attribute-example"></a>Tek öznitelik örneği

Aşağıdaki örnek kod, çalışma zamanında tanımlanmış blob yolu ile bir [Depolama Blobu çıkış bağlaması](functions-bindings-storage-blob-output.md) oluşturur ve sonra blob 'a bir dize yazar.

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

[Blobattribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs) , [Depolama Blobu](functions-bindings-storage-blob.md) girişini veya çıkış bağlamasını tanımlar ve [TextWriter](/dotnet/api/system.io.textwriter) desteklenen bir çıkış bağlama türüdür.

### <a name="multiple-attributes-example"></a>Birden çok öznitelik örneği

Yukarıdaki örnek, işlev uygulamasının ana depolama hesabı bağlantı dizesi (yani) için uygulama ayarını alır `AzureWebJobsStorage` . [Storageaccountattribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) ' i ekleyip öznitelik dizisini Içine geçirerek depolama hesabı için kullanılacak özel bir uygulama ayarı belirtebilirsiniz `BindAsync<T>()` . `Binder`Değil parametresini kullanın `IBinder` .  Örnek:

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
