---
title: 'Azure Işlevleri F # geliştirici başvurusu'
description: 'F # betiği kullanarak Azure Işlevleri geliştirmeyi anlayın.'
author: sylvanc
ms.assetid: e60226e5-2630-41d7-9e5b-9f9e5acc8e50
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: syclebsc
ms.openlocfilehash: f9b7b92fd21e12f1d86c5d5878e48c6ec6b0e748
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87088028"
---
# <a name="azure-functions-f-developer-reference"></a>Azure Işlevleri F # geliştirici başvurusu

Azure Işlevleri için F #, bulutta küçük kod parçalarını veya "işlevleri" kolayca çalıştırmaya yönelik bir çözümdür. Veri, işlev bağımsız değişkenleri aracılığıyla F # işlevinizde akar. Bağımsız değişken adları içinde belirtilir `function.json` ve işlev günlükçüsü ve iptal belirteçleri gibi öğelere erişim için önceden tanımlanmış adlar vardır. 

>[!IMPORTANT]
>F # betiği (. FSX) yalnızca Azure Işlevleri çalışma zamanının [Sürüm 1. x](functions-versions.md#creating-1x-apps) 'i tarafından desteklenir. F # sürümünü sürüm 2. x ve sonraki sürümleri ile birlikte kullanmak istiyorsanız, önceden derlenmiş bir F # sınıf kitaplığı projesi (. FS) kullanmanız gerekir. [C# sınıf kitaplığı projesi](functions-dotnet-class-library.md)olarak Visual Studio 'yu kullanarak bir F # sınıf kitaplığı projesi oluşturun, yönetin ve yayımlayın. Işlev sürümleri hakkında daha fazla bilgi için bkz. [Azure işlevleri çalışma zamanı sürümlerine genel bakış](functions-versions.md).

Bu makalede, [Azure işlevleri geliştirici başvurusunu](functions-reference.md)zaten okuduğunuzu varsaymış olursunuz.

## <a name="how-fsx-works"></a>. FSX nasıl kullanılır?
Bir `.fsx` dosya F # betiğiyle belirlenir. Tek bir dosyada bulunan bir F # projesi olarak düşünülebilir. Dosya, hem programınızın kodunu (Bu durumda, Azure Işlevinizde) hem de bağımlılıkları yönetmeye yönelik yönergeleri içerir.

Bir `.fsx` Azure işlevi için kullandığınızda, genellikle gerekli derlemeler sizin için otomatik olarak eklenir ve "demirbaş" kodu yerine işleve odaklanmanızı sağlar.

## <a name="folder-structure"></a>Klasör yapısı

Bir F # betik projesinin klasör yapısı aşağıdaki gibi görünür:

```
FunctionsProject
 | - MyFirstFunction
 | | - run.fsx
 | | - function.json
 | | - function.proj
 | - MySecondFunction
 | | - run.fsx
 | | - function.json
 | | - function.proj
 | - host.json
 | - extensions.csproj
 | - bin
```

İşlev uygulamasını yapılandırmak için kullanılabilecek bir paylaşılan [host.js](functions-host-json.md) dosyası vardır. Her işlevin kendi kod dosyası (. FSX) ve bağlama yapılandırma dosyası (function.js) vardır.

Çalışma zamanının [sürüm 2. x ve sonraki sürümlerinde](functions-versions.md) gerekli olan bağlama uzantıları, `extensions.csproj` dosyasında, klasördeki gerçek kitaplık dosyalarıyla birlikte tanımlanmıştır `bin` . Yerel olarak geliştirme yaparken, [bağlama uzantılarını kaydetmeniz](./functions-bindings-register.md#extension-bundles)gerekir. Azure portal işlevler geliştirirken, bu kayıt sizin için yapılır.

## <a name="binding-to-arguments"></a>Bağımsız değişkenlere bağlama
Her bağlama, [Azure işlevleri Tetikleyicileri ve bağlamaları geliştirici başvurusu](functions-triggers-bindings.md)bölümünde açıklandığı gibi bazı bağımsız değişkenler kümesini destekler. Örneğin, bir blob tetikleyicisinin desteklediği bağımsız değişken bağlamalarından biri bir POCO, F # kaydı kullanılarak ifade edilebilir. Örnek:

```fsharp
type Item = { Id: string }

let Run(blob: string, output: byref<Item>) =
    let item = { Id = "Some ID" }
    output <- item
```

F # Azure işleviniz bir veya daha fazla bağımsız değişken alacak. Azure Işlevleri bağımsız değişkenleri hakkında konuşduğumuz zaman *giriş* bağımsız değişkenlerine ve *Çıkış* bağımsız değişkenlerine başvuracağız. Giriş bağımsız değişkeni tam olarak şu şekilde seslere sahiptir: F # Azure işlevlerinize giriş. *Çıkış* bağımsız değişkeni değişebilir veri veya `byref<>` işlevinizin geri dönmesi için bir yol görevi gören bir bağımsız değişkendir  .

Yukarıdaki örnekte, `blob` bir giriş bağımsız değişkenidir ve `output` bir çıkış bağımsız değişkenidir. İçin kullandığımızda `byref<>` `output` (ek açıklama eklemeye gerek yoktur) dikkat edin `[<Out>]` . Bir `byref<>` tür kullanılması, işlevinizin, bağımsız değişkenin başvurduğu kaydı veya nesneyi değiştirmesini sağlar.

Bir F # kaydı giriş türü olarak kullanıldığında, `[<CLIMutable>]` kaydı işlevinizin içine geçirmeden önce Azure işlevleri çerçevesinin alanları uygun şekilde ayarlayabilmesi için kayıt tanımının ile işaretlenmiş olması gerekir. Bu şekilde, `[<CLIMutable>]` kayıt özellikleri için ayarlayıcılar oluşturulur. Örnek:

```fsharp
[<CLIMutable>]
type TestObject =
    { SenderName : string
      Greeting : string }

let Run(req: TestObject, log: ILogger) =
    { req with Greeting = sprintf "Hello, %s" req.SenderName }
```

F # sınıfı, hem ın hem de out bağımsız değişkenlerinde kullanılabilir. Bir sınıf için, özelliklerin genellikle alıcıları ve ayarlayıcıları gerekir. Örnek:

```fsharp
type Item() =
    member val Id = "" with get,set
    member val Text = "" with get,set

let Run(input: string, item: byref<Item>) =
    let result = Item(Id = input, Text = "Hello from F#!")
    item <- result
```

## <a name="logging"></a>Günlüğe Kaydetme
Çıktıyı F # içindeki [akış günlüklerinizi](../app-service/troubleshoot-diagnostic-logs.md) günlüğe kaydetmek için Işleviniz [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger)türünde bir bağımsız değişken almalıdır. Tutarlılık için, bu bağımsız değişkenin adlandırılmış olmasını öneririz `log` . Örnek:

```fsharp
let Run(blob: string, output: byref<string>, log: ILogger) =
    log.LogInformation(sprintf "F# Azure Function processed a blob: %s" blob)
    output <- input
```

## <a name="async"></a>Zaman Uyumsuz
`async`İş akışı kullanılabilir, ancak sonucun bir döndürmesi gerekir `Task` . Bu, ile yapılabilir `Async.StartAsTask` , örneğin:

```fsharp
let Run(req: HttpRequestMessage) =
    async {
        return new HttpResponseMessage(HttpStatusCode.OK)
    } |> Async.StartAsTask
```

## <a name="cancellation-token"></a>İptal belirteci
İşlevinizin kapanmayı düzgün işlemesi gerekiyorsa, buna bir [`CancellationToken`](/dotnet/api/system.threading.cancellationtoken) bağımsız değişken verebilirsiniz. Bu, ile birleştirilebilir `async` , örneğin:

```fsharp
let Run(req: HttpRequestMessage, token: CancellationToken)
    let f = async {
        do! Async.Sleep(10)
        return new HttpResponseMessage(HttpStatusCode.OK)
    }
    Async.StartAsTask(f, token)
```

## <a name="importing-namespaces"></a>Ad alanlarını içeri aktarma
Ad alanları her zamanki şekilde açılabilir:

```fsharp
open System.Net
open System.Threading.Tasks
open Microsoft.Extensions.Logging

let Run(req: HttpRequestMessage, log: ILogger) =
    ...
```

Aşağıdaki ad alanları otomatik olarak açılır:

* `System`
* `System.Collections.Generic`
* `System.IO`
* `System.Linq`
* `System.Net.Http`
* `System.Threading.Tasks`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`.

## <a name="referencing-external-assemblies"></a>Dış derlemelere başvurma
Benzer şekilde, Framework derleme başvuruları `#r "AssemblyName"` yönergeyle eklenebilir.

```fsharp
#r "System.Web.Http"

open System.Net
open System.Net.Http
open System.Threading.Tasks
open Microsoft.Extensions.Logging

let Run(req: HttpRequestMessage, log: ILogger) =
    ...
```

Aşağıdaki derlemeler Azure Işlevleri barındırma ortamı tarafından otomatik olarak eklenir:

* `mscorlib`,
* `System`
* `System.Core`
* `System.Xml`
* `System.Net.Http`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`
* `Microsoft.Azure.WebJobs.Extensions`
* `System.Web.Http`
* `System.Net.Http.Formatting`.

Ayrıca, aşağıdaki derlemeler özeldir ve simpleName tarafından başvurulabilir (ör. `#r "AssemblyName"` ):

* `Newtonsoft.Json`
* `Microsoft.WindowsAzure.Storage`
* `Microsoft.ServiceBus`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNEt.WebHooks.Common`.

Özel bir derlemeye başvurmanız gerekirse, derleme dosyasını `bin` işleviniz ile ilişkili bir klasöre yükleyebilir ve dosya adını kullanarak buna başvurabilirsiniz (ör.  `#r "MyAssembly.dll"`). İşlev klasörünüze dosya yükleme hakkında daha fazla bilgi için bkz. Paket yönetimi üzerinde aşağıdaki bölüm.

## <a name="editor-prelude"></a>Düzenleyici Prelude
F # derleyici hizmetlerini destekleyen bir düzenleyici, Azure Işlevlerinin otomatik olarak içerdiği ad alanları ve derlemeler hakkında farkında olmayacaktır. Bu nedenle, düzenleyicinin kullandığınız derlemeleri bulmasını ve ad alanlarını açıkça açmasını sağlayan bir Prelude eklemek yararlı olabilir. Örnek:

```fsharp
#if !COMPILED
#I "../../bin/Binaries/WebJobs.Script.Host"
#r "Microsoft.Azure.WebJobs.Host.dll"
#endif

open System
open Microsoft.Azure.WebJobs.Host
open Microsoft.Extensions.Logging

let Run(blob: string, output: byref<string>, log: ILogger) =
    ...
```

Azure Işlevleri, kodunuzu yürüttüğünde, kaynağı tanımlanan şekilde işler, bu `COMPILED` nedenle düzenleyici Prelude yok sayılır.

<a name="package"></a>

## <a name="package-management"></a>Paket yönetimi
Bir F # işlevinde NuGet paketlerini kullanmak için işlevin `project.json` uygulamanın dosya sistemindeki klasörüne bir dosya ekleyin. `project.json`1.1.0 sürümüne bir NuGet paket başvurusu ekleyen örnek bir dosya aşağıda verilmiştir `Microsoft.ProjectOxford.Face` :

```json
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "Microsoft.ProjectOxford.Face": "1.1.0"
      }
    }
   }
}
```

Yalnızca 4,6 .NET Framework desteklenir, bu nedenle `project.json` dosyanızın `net46` burada gösterildiği gibi belirttiğinden emin olun.

Bir dosyayı karşıya yüklediğinizde `project.json` , çalışma zamanı paketleri alır ve paket derlemelerine otomatik olarak başvurular ekler. Yönergeler eklemeniz gerekmez `#r "AssemblyName"` . Dosyanıza gerekli deyimleri eklemeniz yeterlidir `open` `.fsx` .

Düzenleyicinizde F # derleme hizmetleriyle olan etkileşimi geliştirmek için otomatik olarak düzenleyicinizdeki bütünleştirilmiş kodlara otomatik olarak başvuru koymak isteyebilirsiniz.

### <a name="how-to-add-a-projectjson-file-to-your-azure-function"></a>`project.json`Azure işlevinizde dosya ekleme
1. İşlevinizi Azure portal açarak yapabileceğiniz işlev uygulamanızın çalıştığından emin olun. Bu ayrıca paket yükleme çıktısının görüntüleneceği akış günlüklerine erişim sağlar.
2. Bir dosyayı karşıya yüklemek için `project.json` [işlev uygulama dosyalarını güncelleştirme](functions-reference.md#fileupdate)bölümünde açıklanan yöntemlerden birini kullanın. [Azure işlevleri Için sürekli dağıtım](functions-continuous-deployment.md)kullanıyorsanız, `project.json` dağıtım dalınıza eklemeden önce hazırlama dalınıza bir dosya ekleyebilirsiniz.
3. `project.json`Dosya eklendikten sonra, işlevinizin akış günlüğünde aşağıdaki örneğe benzer bir çıktı görürsünüz:

```
2016-04-04T19:02:48.745 Restoring packages.
2016-04-04T19:02:48.745 Starting NuGet restore
2016-04-04T19:02:50.183 MSBuild auto-detection: using msbuild version '14.0' from 'D:\Program Files (x86)\MSBuild\14.0\bin'.
2016-04-04T19:02:50.261 Feeds used:
2016-04-04T19:02:50.261 C:\DWASFiles\Sites\facavalfunctest\LocalAppData\NuGet\Cache
2016-04-04T19:02:50.261 https://api.nuget.org/v3/index.json
2016-04-04T19:02:50.261
2016-04-04T19:02:50.511 Restoring packages for D:\home\site\wwwroot\HttpTriggerCSharp1\Project.json...
2016-04-04T19:02:52.800 Installing Newtonsoft.Json 6.0.8.
2016-04-04T19:02:52.800 Installing Microsoft.ProjectOxford.Face 1.1.0.
2016-04-04T19:02:57.095 All packages are compatible with .NETFramework,Version=v4.6.
2016-04-04T19:02:57.189
2016-04-04T19:02:57.189
2016-04-04T19:02:57.455 Packages restored.
```

## <a name="environment-variables"></a>Ortam değişkenleri
Bir ortam değişkeni veya bir uygulama ayarı değeri almak için, kullanın `System.Environment.GetEnvironmentVariable` , örneğin:

```fsharp
open System.Environment
open Microsoft.Extensions.Logging

let Run(timer: TimerInfo, log: ILogger) =
    log.LogInformation("Storage = " + GetEnvironmentVariable("AzureWebJobsStorage"))
    log.LogInformation("Site = " + GetEnvironmentVariable("WEBSITE_SITE_NAME"))
```

## <a name="reusing-fsx-code"></a>. FSX kodunu yeniden kullanma
`.fsx`Bir yönergesi kullanarak diğer dosyalardaki kodu kullanabilirsiniz `#load` . Örnek:

`run.fsx`

```fsharp
#load "logger.fsx"

let Run(timer: TimerInfo, log: ILogger) =
    mylog log (sprintf "Timer: %s" DateTime.Now.ToString())
```

`logger.fsx`

```fsharp
let mylog(log: ILogger, text: string) =
    log.LogInformation(text);
```

Yollar, `#load` dosyanın konumuyla ilişkilidir `.fsx` .

* `#load "logger.fsx"` işlev klasöründe bulunan bir dosyayı yükler.
* `#load "package\logger.fsx"` işlev klasöründeki klasöründe bulunan bir dosyayı yükler `package` .
* `#load "..\shared\mylogger.fsx"` klasörde bulunan bir dosyayı, `shared` işlev klasörüyle aynı düzeyde, diğer bir deyişle, doğrudan altında yükler `wwwroot` .

`#load`Yönerge yalnızca `.fsx` (F # betiği) dosyalarla birlikte çalışarak dosyalarla birlikte kullanılamaz `.fs` .

## <a name="next-steps"></a>Sonraki adımlar
Daha fazla bilgi için aşağıdaki kaynaklara bakın:

* [F# Kılavuzu](/dotnet/articles/fsharp/index)
* [Azure Işlevleri için en iyi uygulamalar](functions-best-practices.md)
* [Azure İşlevleri geliştirici başvurusu](functions-reference.md)
* [Azure Işlevleri Tetikleyicileri ve bağlamaları](functions-triggers-bindings.md)
* [Azure Işlevleri testi](functions-test-a-function.md)
* [Azure Işlevleri ölçeklendirme](functions-scale.md)
