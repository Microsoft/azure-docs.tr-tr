---
title: Azure Işlevleri için PowerShell geliştirici başvurusu
description: PowerShell kullanarak işlevleri geliştirmeyi anlayın.
author: eamonoreilly
ms.topic: conceptual
ms.custom: devx-track-dotnet, devx-track-azurepowershell
ms.date: 04/22/2019
ms.openlocfilehash: a7951543d548696c8de403d7980e1a41b678c6cd
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078677"
---
# <a name="azure-functions-powershell-developer-guide"></a>Azure Işlevleri PowerShell Geliştirici Kılavuzu

Bu makalede, PowerShell kullanarak Azure Işlevleri yazma hakkında ayrıntılı bilgi sağlanır.

Bir PowerShell Azure işlevi (işlev), tetiklendiğinde yürütülen bir PowerShell betiği olarak temsil edilir. Her işlev betiği, işlevin nasıl `function.json` davranacağını, örneğin nasıl tetikleneceğini ve giriş ve çıkış parametrelerini tanımlayan ilgili bir dosya içerir. Daha fazla bilgi edinmek için [Tetikleyiciler ve bağlama makalesine](functions-triggers-bindings.md)bakın. 

Diğer işlev türleri gibi, PowerShell betik işlevleri de dosyada tanımlanan tüm giriş bağlamalarının adlarıyla eşleşen parametreleri alır `function.json` . `TriggerMetadata`İşlevi Başlatan tetikleyicide ek bilgiler içeren bir parametre de geçirilir.

Bu makalede, [Azure işlevleri geliştirici başvurusunu](functions-reference.md)zaten okuduğunuzu varsaymış olursunuz. İlk PowerShell işlevinizi oluşturmak için [PowerShell için hızlı başlangıç işlevleri](./create-first-function-vs-code-powershell.md) de tamamlanmış olmalıdır.

## <a name="folder-structure"></a>Klasör yapısı

PowerShell projesi için gereken klasör yapısı aşağıdaki gibi görünür. Bu varsayılan değer değiştirilebilir. Daha fazla bilgi için aşağıdaki [scriptfile](#configure-function-scriptfile) bölümüne bakın.

```
PSFunctionApp
 | - MyFirstFunction
 | | - run.ps1
 | | - function.json
 | - MySecondFunction
 | | - run.ps1
 | | - function.json
 | - Modules
 | | - myFirstHelperModule
 | | | - myFirstHelperModule.psd1
 | | | - myFirstHelperModule.psm1
 | | - mySecondHelperModule
 | | | - mySecondHelperModule.psd1
 | | | - mySecondHelperModule.psm1
 | - local.settings.json
 | - host.json
 | - requirements.psd1
 | - profile.ps1
 | - extensions.csproj
 | - bin
```

Projenin kökünde, [`host.json`](functions-host-json.md) işlev uygulamasını yapılandırmak için kullanılabilecek paylaşılan bir dosya vardır. Her işlevde kendi kod dosyası (. ps1) ve bağlama yapılandırma dosyası () içeren bir klasör vardır `function.json` . Dosyanın üst dizinindeki function.jsadı her zaman işlevinizin adıdır.

Belirli bağlamalar bir dosya varlığını gerektirir `extensions.csproj` . [Sürüm 2. x ve sonraki](functions-versions.md) sürümlerde işlevler çalışma zamanının gerekli olan bağlama uzantıları, `extensions.csproj` dosyasında, klasördeki gerçek kitaplık dosyaları ile tanımlanmıştır `bin` . Yerel olarak geliştirme yaparken, [bağlama uzantılarını kaydetmeniz](functions-bindings-register.md#extension-bundles)gerekir. Azure portal işlevler geliştirirken, bu kayıt sizin için yapılır.

PowerShell Işlev uygulamalarında, bir işlev uygulaması çalışmaya başladığında isteğe bağlı olarak bir `profile.ps1` çalışır (Aksi takdirde, *[soğuk bir başlangıç](#cold-start)* olarak bilinir. Daha fazla bilgi için bkz. [PowerShell profili](#powershell-profile).

## <a name="defining-a-powershell-script-as-a-function"></a>Bir PowerShell betiğini işlev olarak tanımlama

Varsayılan olarak, Işlevler çalışma zamanı ' de işlevinize bakar `run.ps1` ve `run.ps1` buna karşılık olarak aynı üst dizini paylaşır `function.json` .

Betiğinizin yürütme sırasında bir dizi bağımsız değişkeni geçti. Bu parametreleri işlemek için, `param` betiğinizin en üstüne aşağıdaki örnekte olduğu gibi bir blok ekleyin:

```powershell
# $TriggerMetadata is optional here. If you don't need it, you can safely remove it from the param block
param($MyFirstInputBinding, $MySecondInputBinding, $TriggerMetadata)
```

### <a name="triggermetadata-parameter"></a>TriggerMetadata parametresi

`TriggerMetadata`Parametresi, tetikleyici hakkında ek bilgi sağlamak için kullanılır. Ek meta veriler bağlamadan bağlamaya farklılık gösterir, ancak hepsi `sys` aşağıdaki verileri içeren bir özellik içerir:

```powershell
$TriggerMetadata.sys
```

| Özellik   | Açıklama                                     | Tür     |
|------------|-------------------------------------------------|----------|
| UtcNow     | UTC olarak işlev tetiklendiğinde        | DateTime |
| MethodName | Tetiklenen Işlevin adı     | string   |
| RandGuid   | işlevin bu yürütmeye yönelik benzersiz bir GUID | string   |

Her tetikleyici türünün farklı bir meta veri kümesi vardır. Örneğin, için, `$TriggerMetadata` `QueueTrigger` `InsertionTime` `Id` `DequeueCount` diğer şeyleri kapsayan,,,. Sıra tetikleyicisinin meta verileri hakkında daha fazla bilgi için, [sıra tetikleyicilerinin resmi belgelerine](functions-bindings-storage-queue-trigger.md#message-metadata)gidin. Tetikleyici meta verilerinin içinde neler olduğunu görmek için, üzerinde çalıştığınız [tetikleyicilerle](functions-triggers-bindings.md) ilgili belgelere bakın.

## <a name="bindings"></a>Bağlamalar

PowerShell 'de [bağlamalar](functions-triggers-bindings.md) , üzerinde bir işlevin function.jsyapılandırılır ve tanımlanır. İşlevler, bağlamalarla çeşitli yollarla etkileşime geçin.

### <a name="reading-trigger-and-input-data"></a>Tetikleyici ve giriş verilerini okuma

Tetikleyici ve giriş bağlamaları, işleviniz için parametre olarak okunabilir. Giriş bağlamalarında `direction` function.jsüzerinde bir kümesi vardır `in` . `name`İçinde tanımlanan özelliği, `function.json` bloğundaki parametrenin adıdır `param` . PowerShell, bağlama için adlandırılmış parametreler kullandığından, parametrelerin sırası bu şekilde değildir. Ancak, içinde tanımlanan bağlamaların sırasını izlemek en iyi uygulamadır `function.json` .

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)
```

### <a name="writing-output-data"></a>Çıkış verileri yazma

Işlevlerde, bir çıkış bağlamasının `direction` üzerinde function.jsiçin bir kümesi vardır `out` . `Push-OutputBinding`İşlevler çalışma zamanı için kullanılabilen cmdlet 'ini kullanarak bir çıktı bağlamaya yazabilirsiniz. Her durumda, `name` içinde tanımlandığı şekilde bağlamanın özelliği `function.json` `Name` cmdlet 'in parametresine karşılık gelir `Push-OutputBinding` .

Aşağıda, `Push-OutputBinding` işlev betiğinizde nasıl çağrılacağını gösterilmektedir:

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Push-OutputBinding -Name myQueue -Value $myValue
```

Ayrıca işlem hattı aracılığıyla belirli bir bağlama için bir değer geçirebilirsiniz.

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Produce-MyOutputValue | Push-OutputBinding -Name myQueue
```

`Push-OutputBinding` , için belirtilen değere göre farklı davranır `-Name` :

* Belirtilen ad geçerli bir çıkış bağlamasıyla çözülemezse bir hata oluşur.

* Çıkış bağlaması bir değer koleksiyonunu kabul ettiğinde, `Push-OutputBinding` birden fazla değeri göndermek için tekrar tekrar çağırabilirsiniz.

* Çıkış bağlaması yalnızca bir tek değeri kabul ettiğinde, `Push-OutputBinding` ikinci kez çağırma bir hata oluşturur.

#### <a name="push-outputbinding-syntax"></a>`Push-OutputBinding` sözdizimi

Çağırmak için geçerli parametreler aşağıda verilmiştir `Push-OutputBinding` :

| Ad | Tür | Konum | Description |
| ---- | ---- |  -------- | ----------- |
| **`-Name`** | Dize | 1 | Ayarlamak istediğiniz çıkış bağlamasının adı. |
| **`-Value`** | Nesne | 2 | Ayarlamak istediğiniz çıkış bağlamasının değeri, işlem hattı ByValue 'dan kabul edilir. |
| **`-Clobber`** | SwitchParameter | Adlandırılır | Seçim Belirtildiğinde, belirtilen bir çıkış bağlaması için değeri ayarlamaya zorlar. | 

Aşağıdaki ortak parametreler de desteklenir: 
* `Verbose`
* `Debug`
* `ErrorAction`
* `ErrorVariable`
* `WarningAction`
* `WarningVariable`
* `OutBuffer`
* `PipelineVariable`
* `OutVariable` 

Daha fazla bilgi için bkz. [CommonParameters hakkında](/powershell/module/microsoft.powershell.core/about/about_commonparameters).

#### <a name="push-outputbinding-example-http-responses"></a>Push-OutputBinding örnek: HTTP yanıtları

HTTP tetikleyicisi adlı çıkış bağlamasını kullanarak bir yanıt döndürür `response` . Aşağıdaki örnekte, çıkış bağlaması `response` "output #1" değerine sahiptir:

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #1"
})
```

Çıktı yalnızca tek bir değer kabul eden HTTP 'e ait olduğundan, `Push-OutputBinding` ikinci kez çağrıldığında bir hata oluşur.

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #2"
})
```

Yalnızca Singleton değerlerini kabul eden çıkışlar için, `-Clobber` bir koleksiyona eklemeye çalışmak yerine eski değeri geçersiz kılmak için parametresini kullanabilirsiniz. Aşağıdaki örnek, daha önce bir değer eklediğinizi varsayar. Kullanarak `-Clobber` , aşağıdaki örnekteki yanıt, "çıkış #3" değeri döndürecek varolan değeri geçersiz kılar:

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #3"
}) -Clobber
```

#### <a name="push-outputbinding-example-queue-output-binding"></a>Push-OutputBinding örnek: kuyruk çıktı bağlaması

`Push-OutputBinding` , [Azure kuyruk depolama çıkış bağlaması](functions-bindings-storage-queue-output.md)gibi çıkış bağlamalarına veri göndermek için kullanılır. Aşağıdaki örnekte, sıraya yazılan ileti "output #1" değerine sahiptir:

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #1"
```

Depolama kuyruğu için çıkış bağlaması birden çok çıkış değeri kabul eder. Bu durumda, iki öğe içeren bir listeyi kuyruğa yazmaya sonra aşağıdaki örneği çağırmak: "output #1" ve "output #2".

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #2"
```

Aşağıdaki örnek, önceki iki değerin ardından çağrıldığında, çıkış koleksiyonuna iki değer daha ekler:

```powershell
PS >Push-OutputBinding -Name outQueue -Value @("output #3", "output #4")
```

Sıraya yazıldığında ileti şu dört değeri içerir: "çıkış #1", "çıkış #2", "çıkış #3" ve "çıkış #4".

#### <a name="get-outputbinding-cmdlet"></a>`Get-OutputBinding` cmdlet

`Get-OutputBinding`Çıkış bağlamalarınız için şu anda ayarlanmış olan değerleri almak için cmdlet 'ini kullanabilirsiniz. Bu cmdlet, çıkış bağlamalarının adlarını ilgili değerleriyle içeren bir Hashtable alır. 

Aşağıda `Get-OutputBinding` geçerli bağlama değerlerini döndürmek için kullanılan bir örneği verilmiştir:

```powershell
Get-OutputBinding
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
MyOtherQueue                   myData
```

`Get-OutputBinding` Ayrıca `-Name` , aşağıdaki örnekte olduğu gibi döndürülen bağlamayı filtrelemek için kullanılabilen adlı bir parametresini de içerir:

```powershell
Get-OutputBinding -Name MyQ*
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
```

Joker karakterler (*) ' de desteklenir `Get-OutputBinding` .

## <a name="logging"></a>Günlüğe Kaydetme

PowerShell işlevlerinde günlüğe kaydetme, normal PowerShell günlüğü gibi çalışır. Günlük cmdlet 'lerini her bir çıkış akışına yazmak için kullanabilirsiniz. Her cmdlet, Işlevleri tarafından kullanılan bir günlük düzeyiyle eşlenir.

| İşlev günlüğü düzeyi | Günlük cmdlet 'i |
| ------------- | -------------- |
| Hata | **`Write-Error`** |
| Uyarı | **`Write-Warning`**  | 
| Bilgi | **`Write-Information`** <br/> **`Write-Host`** <br /> **`Write-Output`** <br/> _Bilgi_ düzeyi günlüğe yazmaya yazar. |
| Hata Ayıklama | **`Write-Debug`** |
| İzleme | **`Write-Progress`** <br /> **`Write-Verbose`** |

Bu cmdlet 'lere ek olarak, işlem hattına yazılan her şey `Information` günlük düzeyine yönlendirilir ve varsayılan PowerShell biçimlendirmesi ile görüntülenir.

> [!IMPORTANT]
> `Write-Verbose`Veya `Write-Debug` cmdlet 'lerinin kullanılması, ayrıntılı ve hata ayıklama düzeyinde günlüğe kaydetmeyi görmek için yeterli değildir. Ayrıca, gerçekten önem verdiğiniz günlükleri bildiren günlük düzeyi eşiğini da yapılandırmanız gerekir. Daha fazla bilgi için bkz. [işlev uygulaması günlük düzeyini yapılandırma](#configure-the-function-app-log-level).

### <a name="configure-the-function-app-log-level"></a>İşlev uygulaması günlük düzeyini yapılandırma

Azure Işlevleri, Işlevlerin günlüklere yazma yöntemini denetlemenizi kolaylaştırmak için eşik düzeyini tanımlamanızı sağlar. Konsola yazılan tüm izlemelerin eşiğini ayarlamak için, `logging.logLevel.default`host.js[ `host.json` dosyadaki] özelliği[ başvuru üzerinde]kullanın. Bu ayar, işlev uygulamanızdaki tüm işlevler için geçerlidir.

Aşağıdaki örnek, tüm işlevler için ayrıntılı günlük kaydını etkinleştirmek üzere eşiği ayarlar, ancak şu adlı bir işlev için hata ayıklama günlüğünü etkinleştirmek üzere eşiği ayarlar `MyFunction` :

```json
{
    "logging": {
        "logLevel": {
            "Function.MyFunction": "Debug",
            "default": "Trace"
        }
    }
}  
```

Daha fazla bilgi için bkz. [host.jsbaşvurusu].

### <a name="viewing-the-logs"></a>Günlükleri görüntüleme

İşlev Uygulaması Azure 'da çalışıyorsa, bunu izlemek için Application Insights kullanabilirsiniz. İşlev günlüklerini görüntüleme ve sorgulama hakkında daha fazla bilgi edinmek için [Azure işlevlerini izleme](functions-monitoring.md) makalesini okuyun.

Geliştirme için İşlev Uygulaması yerel olarak çalıştırıyorsanız, varsayılan olarak dosya sistemine kaydedilir. Konsolunda günlükleri görmek için, `AZURE_FUNCTIONS_ENVIRONMENT` işlev uygulaması başlatmadan önce ortam değişkenini olarak ayarlayın `Development` .

## <a name="triggers-and-bindings-types"></a>Tetikleyiciler ve bağlamalar türleri

İşlev uygulamanız ile kullanabileceğiniz birkaç tetikleyici ve bağlama vardır. Tetikleyiciler ve bağlamaların tam listesi [burada bulunabilir](functions-triggers-bindings.md#supported-bindings).

Tüm tetikleyiciler ve bağlamalar kodda birkaç gerçek veri türü olarak temsil edilir:

* Hashtable
* string
* Byte []
* int
* double
* HttpRequestContext
* HttpResponseContext

Bu listedeki ilk beş tür standart .NET türlerdir. Son ikisi yalnızca [httptrigger tetikleyicisi](#http-triggers-and-bindings)tarafından kullanılır.

İşlevinizdeki her bağlama parametresi bu türlerden biri olmalıdır.

### <a name="http-triggers-and-bindings"></a>HTTP Tetikleyicileri ve bağlamaları

Http ve Web kancası Tetikleyicileri ve HTTP çıkış bağlamaları, HTTP iletilerini temsil etmek için istek ve yanıt nesnelerini kullanır.

#### <a name="request-object"></a>İstek nesnesi

Betiğe geçirilen istek nesnesi, `HttpRequestContext` aşağıdaki özelliklere sahip olan türdür:

| Özellik  | Açıklama                                                    | Tür                      |
|-----------|----------------------------------------------------------------|---------------------------|
| **`Body`**    | İsteğin gövdesini içeren bir nesne. `Body` , verileri temel alan en iyi türe serileştirilir. Örneğin, veriler JSON ise, bir Hashtable olarak geçirilir. Veriler bir dizeyse, bir dize olarak geçirilir. | object |
| **`Headers`** | İstek üst bilgilerini içeren bir sözlük.                | Sözlük<dize, dize><sup>*</sup> |
| **`Method`** | İsteğin HTTP yöntemi.                                | string                    |
| **`Params`**  | İsteğin yönlendirme parametrelerini içeren nesne. | Sözlük<dize, dize><sup>*</sup> |
| **`Query`** | Sorgu parametrelerini içeren bir nesne.                  | Sözlük<dize, dize><sup>*</sup> |
| **`Url`** | İsteğin URL 'SI.                                        | string                    |

<sup>*</sup> Tüm `Dictionary<string,string>` anahtarlar büyük/küçük harfe duyarlıdır.

#### <a name="response-object"></a>Yanıt nesnesi

Geri göndermeniz gereken yanıt nesnesi, `HttpResponseContext` aşağıdaki özelliklere sahip olan türüdür:

| Özellik      | Açıklama                                                 | Tür                      |
|---------------|-------------------------------------------------------------|---------------------------|
| **`Body`**  | Yanıtın gövdesini içeren bir nesne.           | object                    |
| **`ContentType`** | Yanıt için içerik türünü ayarlamanın kısa bir tarafı. | string                    |
| **`Headers`** | Yanıt üst bilgilerini içeren bir nesne.               | Sözlük veya Hashtable   |
| **`StatusCode`**  | Yanıtın HTTP durum kodu.                       | dize veya tamsayı             |

#### <a name="accessing-the-request-and-response"></a>İstek ve yanıta erişme

HTTP tetikleyicilerle çalışırken, HTTP isteğine diğer giriş bağlamalarla aynı şekilde erişebilirsiniz. Bu, `param` bloğunda.

Aşağıdaki şekilde `HttpResponseContext` gösterildiği gibi bir yanıt döndürmek için bir nesnesi kullanın:

`function.json`

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "anonymous"
    },
    {
      "type": "http",
      "direction": "out"
    }
  ]
}
```

`run.ps1`

```powershell
param($req, $TriggerMetadata)

$name = $req.Query.Name

Push-OutputBinding -Name res -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "Hello $name!"
})
```

Bu işlevi çağırma sonucu şöyle olur:

```
PS > irm http://localhost:5001?Name=Functions
Hello Functions!
```

### <a name="type-casting-for-triggers-and-bindings"></a>Tetikleyiciler ve bağlamalar için tür atama

Blob bağlaması gibi bazı bağlamalar için, parametrenin türünü belirtebilirsiniz.

Örneğin, BLOB depolama alanından bir dize olarak sağlanan verilerin olmasını engellemek için aşağıdaki tür cast 'u `param` bloba ekleyin:

```powershell
param([string] $myBlob)
```

## <a name="powershell-profile"></a>PowerShell profili

PowerShell 'de bir PowerShell profili kavramı vardır. PowerShell profilleri hakkında bilgi sahibi değilseniz, bkz. [profiller hakkında](/powershell/module/microsoft.powershell.core/about/about_profiles).

PowerShell Işlevlerinde, profil betiği, ilk dağıtıldığında ve etkinleştirildikten sonra, uygulamada PowerShell çalışan örneği başına bir kez yürütülür ([soğuk başlar](#cold-start). Eşzamanlılık etkinleştirildiğinde, [PSWorkerInProcConcurrencyUpperBound](#concurrency) değeri ayarlanarak profil betiği oluşturulan her çalışma alanı için çalıştırılır.

Visual Studio Code ve Azure Functions Core Tools gibi araçları kullanarak bir işlev uygulaması oluşturduğunuzda, sizin için varsayılan bir değer `profile.ps1` oluşturulur. Varsayılan profil, [temel araçlar GitHub deposunda](https://github.com/Azure/azure-functions-core-tools/blob/dev/src/Azure.Functions.Cli/StaticResources/profile.ps1) tutulur ve şunları içerir:

* Azure 'da otomatik MSI kimlik doğrulaması.
* İsterseniz Azure PowerShell `AzureRM` PowerShell diğer adlarını açma özelliği.

## <a name="powershell-versions"></a>PowerShell sürümleri

Aşağıdaki tabloda, Işlevlerin çalışma zamanının her ana sürümü için kullanılabilen PowerShell sürümleri ve gerekli .NET sürümü gösterilmektedir:

| İşlevler sürümü | PowerShell sürümü                               | .NET sürümü  | 
|-------------------|--------------------------------------------------|---------------|
| 3. x (önerilir) | PowerShell 7 (önerilir)<br/>PowerShell Core 6 | .NET Core 3.1<br/>.NET Core 2.1 |
| 2.x               | PowerShell Core 6                                | .NET Core 2.2 |

Geçerli sürümü `$PSVersionTable` herhangi bir işlevden yazdırarak görebilirsiniz.

### <a name="running-local-on-a-specific-version"></a>Belirli bir sürümde yerel çalıştırma

Yerel olarak çalıştırılırken Azure Işlevleri çalışma zamanı varsayılan olarak PowerShell Core 6 ' yı kullanmaktır. Bunun yerine, yerel olarak çalışırken PowerShell 7 ' yi kullanmak için, `"FUNCTIONS_WORKER_RUNTIME_VERSION" : "~7"` `Values` Proje kökündeki dosyadaki local.setting.js, bu ayarı diziye eklemeniz gerekir. PowerShell 7 ' de yerel olarak çalışırken, local.settings.jsdosya aşağıdaki örnekteki gibi görünür: 

```json
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "FUNCTIONS_WORKER_RUNTIME": "powershell",
    "FUNCTIONS_WORKER_RUNTIME_VERSION" : "~7"
  }
}
```

### <a name="changing-the-powershell-version"></a>PowerShell sürümünü değiştirme

İşlev uygulamanızın PowerShell Core 6 ' dan PowerShell 7 ' ye yükseltebilmesi için sürüm 3. x üzerinde çalışıyor olması gerekir. Bunun nasıl yapılacağını öğrenmek için bkz. [geçerli çalışma zamanı sürümünü görüntüleme ve güncelleştirme](set-runtime-version.md#view-and-update-the-current-runtime-version).

İşlev uygulamanız tarafından kullanılan PowerShell sürümünü değiştirmek için aşağıdaki adımları kullanın. Bunu Azure portal ya da PowerShell kullanarak yapabilirsiniz.

# <a name="portal"></a>[Portal](#tab/portal)

1. [Azure Portal](https://portal.azure.com), işlev uygulamanıza gidin.

1. **Ayarlar** altında **yapılandırma**' yı seçin. **Genel ayarlar** sekmesinde **PowerShell sürümünü** bulun. 

    :::image type="content" source="media/functions-reference-powershell/change-powershell-version-portal.png" alt-text="İşlev uygulaması tarafından kullanılan PowerShell sürümünü seçin"::: 

1. İstediğiniz **PowerShell Çekirdek sürümünüzü** seçin ve **Kaydet**' i seçin. Bekleyen yeniden başlatma hakkında uyarı olduğunda **devam**' ı seçin. İşlev uygulaması, seçilen PowerShell sürümünde yeniden başlatılır. 

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell sürümünü değiştirmek için aşağıdaki betiği çalıştırın: 

```powershell
Set-AzResource -ResourceId "/subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.Web/sites/<FUNCTION_APP>/config/web" -Properties @{  powerShellVersion  = '<VERSION>' } -Force -UsePatchSemantics

```

, `<SUBSCRIPTION_ID>` `<RESOURCE_GROUP>` , Ve ' yi `<FUNCTION_APP>` Azure aboneliğinizin kimliğiyle, kaynak grubunuzun ve işlev uygulamanızın adıyla değiştirin.  Ayrıca, `<VERSION>` ya da ile `~6` değiştirin `~7` . `powerShellVersion`Döndürülen karma tablosunda ayarının güncelleştirilmiş değerini doğrulayabilirsiniz `Properties` . 

---

İşlev uygulaması, yapılandırma sırasında değişiklik yapıldıktan sonra yeniden başlatılır.

## <a name="dependency-management"></a>Bağımlılık yönetimi

İşlevler, bağımlılıkları yönetmek için [PowerShell galerisinden](https://www.powershellgallery.com) yararlanmanızı sağlar. Bağımlılık yönetimi etkinken, gerekli modülleri otomatik olarak indirmek için requirements.psd1 dosyası kullanılır. Bu davranışı `managedDependency` , özelliği, `true` Aşağıdaki örnekte olduğu gibi, [ dosyadakihost.js](functions-host-json.md)kökünde ayarlayarak etkinleştirin:

```json
{
  "managedDependency": {
          "enabled": true
       }
}
```

Yeni bir PowerShell işlevleri projesi oluşturduğunuzda, Azure [ `Az` modülü](/powershell/azure/new-azureps-module-az) dahil olmak üzere bağımlılık yönetimi varsayılan olarak etkindir. Şu anda desteklenen en fazla modül sayısı 10 ' dur. Desteklenen sözdizimi, _`MajorNumber`_ `.*` aşağıdaki requirements.psd1 örneğinde gösterildiği gibi, modül sürümüdür.

```powershell
@{
    Az = '1.*'
    SqlServer = '21.1.18147'
}
```

requirements.psd1 dosyasını güncelleştirdiğinizde, bir yeniden başlatmanın ardından güncelleştirilmiş modüller yüklenir.

> [!NOTE]
> Yönetilen bağımlılıklar, modülleri indirmek için www.powershellgallery.com 'e erişim gerektirir. Yerel olarak çalışırken, gerekli güvenlik duvarı kurallarını ekleyerek çalışma zamanının bu URL 'ye erişebildiğinizden emin olun.

> [!NOTE]
> Yönetilen bağımlılıklar Şu anda, lisansı etkileşimli olarak kabul ederek ya da çağrılırken anahtar sağlayarak kullanıcının bir lisansı kabul etmesini gerektiren modülleri desteklemez `-AcceptLicense` `Install-Module` .

Aşağıdaki uygulama ayarları, yönetilen bağımlılıkların nasıl indirileceğini ve yükleneceğini değiştirmek için kullanılabilir. Uygulama yükseltmeniz içinde başlar `MDMaxBackgroundUpgradePeriod` ve yükseltme işlemi yaklaşık olarak içinde tamamlanır `MDNewSnapshotCheckPeriod` .

| İşlev Uygulaması ayarı              | Varsayılan değer             | Description                                         |
|   -----------------------------   |   -------------------     |  -----------------------------------------------    |
| **`MDMaxBackgroundUpgradePeriod`**      | `7.00:00:00` (7 gün)     | Her PowerShell çalışan işlemi, işlem başlatma ve sonrasında PowerShell Galerisi modül yükseltmelerini denetlemeyi başlatır `MDMaxBackgroundUpgradePeriod` . PowerShell Galerisi yeni bir modül sürümü kullanılabilir olduğunda, dosya sistemine yüklenir ve PowerShell çalışanları için kullanılabilir hale getirilir. Bu değeri azaltmak, işlev uygulamanızın daha önce daha yeni modül sürümlerini almasını sağlar, ancak aynı zamanda uygulama kaynak kullanımını (ağ g/ç, CPU, depolama) de artırır. Bu değerin artırılması uygulamanın kaynak kullanımını düşürür, ancak uygulamanıza yeni modül sürümlerinin teslim edilmesini de erteleyebilir. | 
| **`MDNewSnapshotCheckPeriod`**         | `01:00:00` (1 saat)       | Yeni modül sürümleri dosya sistemine yüklendikten sonra, her PowerShell çalışan işleminin yeniden başlatılması gerekir. PowerShell çalışanlarını yeniden başlatmak, geçerli işlev yürütmesini kesintiye uğratmak için uygulamanızın kullanılabilirliğini etkiler. Tüm PowerShell çalışan süreçler yeniden başlatılana kadar, işlev etkinleştirmeleri eski veya yeni modül sürümlerini kullanabilir. Tüm PowerShell çalışanlarının yeniden başlatılması içinde tamamlanır `MDNewSnapshotCheckPeriod` . Bu değerin artırılması kesintiler sıklığını düşürür, ancak işlev etkinleştirmeleri eski veya yeni modül sürümlerini belirleyici olmayan şekilde kullandığınızda zaman dilimini de artırabilir. |
| **`MDMinBackgroundUpgradePeriod`**      | `1.00:00:00` (1 gün)     | Sık gerçekleştirilen çalışan yeniden başlatmalarının aşırı modül yükseltmelerini önlemek için, son olarak bu denetimi başlatmış bir çalışan varsa modül yükseltmelerini denetlemek gerçekleştirilmez `MDMinBackgroundUpgradePeriod` . |

Kendi özel modüllerinizi kullanmak, normal şekilde nasıl yapacağınıza göre biraz farklıdır.

Yerel bilgisayarınızda modül, içindeki genel kullanıma açık klasörlerden birine yüklenir `$env:PSModulePath` . Azure 'da çalışırken, makinenizde yüklü olan modüllere erişiminiz yok. Bu, `$env:PSModulePath` bir PowerShell işlev uygulamasının, `$env:PSModulePath` normal bir PowerShell betiğinden farklı olduğu anlamına gelir.

Işlevlerde `PSModulePath` iki yol içerir:

* `Modules`İşlev uygulamanızın kökünde bulunan bir klasör.
* `Modules`PowerShell dil çalışanı tarafından denetlenen bir klasörün yolu.


### <a name="function-app-level-modules-folder"></a>İşlev uygulama düzeyi `Modules` klasörü

Özel modülleri kullanmak için işlevlerinizin bir klasöre bağlı olduğu modülleri yerleştirebilirsiniz `Modules` . Bu klasörden modüller, işlevler çalışma zamanı tarafından otomatik olarak kullanılabilir. İşlev uygulamasındaki herhangi bir işlev bu modülleri kullanabilir. 

> [!NOTE]
> requirements.psd1 dosyasında belirtilen modüller otomatik olarak indirilir ve bu dosyaları modüller klasörüne dahil etmeniz gerekmez. Bunlar, `$env:LOCALAPPDATA/AzureFunctions` bulutta çalıştırıldığında klasöründe ve klasöründe yerel olarak depolanır `/data/ManagedDependencies` .

Özel modül özelliğinden yararlanmak için, `Modules` işlev uygulamanızın kökünde bir klasör oluşturun. İşlevleriniz içinde kullanmak istediğiniz modülleri bu konuma kopyalayın.

```powershell
mkdir ./Modules
Copy-Item -Path /mymodules/mycustommodule -Destination ./Modules -Recurse
```

Bir `Modules` klasör ile, işlev uygulamanız aşağıdaki klasör yapısına sahip olmalıdır:

```
PSFunctionApp
 | - MyFunction
 | | - run.ps1
 | | - function.json
 | - Modules
 | | - MyCustomModule
 | | - MyOtherCustomModule
 | | - MySpecialModule.psm1
 | - local.settings.json
 | - host.json
 | - requirements.psd1
```

İşlev uygulamanızı başlattığınızda, PowerShell dil çalışanı bu `Modules` klasörü öğesine ekler `$env:PSModulePath` . böylece, normal bir PowerShell betiğiyle yaptığınız gibi, modül oto yüklemeye güvenebilirsiniz.

### <a name="language-worker-level-modules-folder"></a>Dil çalışanı düzeyi `Modules` klasörü

Birçok modül genellikle PowerShell dil çalışanı tarafından kullanılır. Bu modüller, öğesinin son konumunda tanımlanmıştır `PSModulePath` . 

Geçerli modüller listesi aşağıdaki gibidir:

* [Microsoft. PowerShell. Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive):, ve gibi Arşivlerle çalışmak için kullanılan `.zip` modül `.nupkg` .
* **Threadjob**: PowerShell iş API 'lerinin iş parçacığı tabanlı bir uygulamasıdır.

Varsayılan olarak, Işlevler bu modüllerin en son sürümünü kullanır. Belirli bir modül sürümünü kullanmak için, söz konusu sürümü `Modules` işlev uygulamanızın klasörüne yerleştirin.

## <a name="environment-variables"></a>Ortam değişkenleri

Işlevlerde, hizmet bağlantı dizeleri gibi [uygulama ayarları](functions-app-settings.md), yürütme sırasında ortam değişkenleri olarak sunulur. `$env:NAME_OF_ENV_VAR`Aşağıdaki örnekte gösterildiği gibi, kullanarak bu ayarlara erişebilirsiniz:

```powershell
param($myTimer)

Write-Host "PowerShell timer trigger function ran! $(Get-Date)"
Write-Host $env:AzureWebJobsStorage
Write-Host $env:WEBSITE_SITE_NAME
```

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

Yerel olarak çalışırken, uygulama ayarları proje dosyasındaki [local.settings.js](functions-run-local.md#local-settings-file) okur.

## <a name="concurrency"></a>Eşzamanlılık

Varsayılan olarak, Işlevler PowerShell çalışma zamanı tek seferde yalnızca bir işlevin çağrılmasını işleyebilir. Ancak, bu eşzamanlılık düzeyi aşağıdaki durumlarda yeterli olmayabilir:

* Aynı anda çok sayıda çağırma gerçekleştirmeye çalışırken.
* Aynı işlev uygulaması içindeki diğer işlevleri çağıran işlevlere sahip olduğunuzda.

İş yükü türüne bağlı olarak keşfedebileceğiniz birkaç eşzamanlılık modeli vardır:

* Arttırın ```FUNCTIONS_WORKER_PROCESS_COUNT``` . Bu, belirli CPU ve bellek yükünü sunan aynı örnek içindeki birden çok işlemde işlev etkinleştirmeleri işlemesini sağlar. Genel olarak, g/ç ile bağlantılı işlevler bu ek yükün altına düşmeyecektir. CPU ile bağlantılı işlevler için etki önemli olabilir.

* ```PSWorkerInProcConcurrencyUpperBound```Uygulama ayarı değerini artırın. Bu, CPU ve bellek yükünü önemli ölçüde azaltan aynı işlem içinde birden çok çalışma alanı oluşturulmasına olanak sağlar.

Bu ortam değişkenlerini, işlev uygulamanızın [uygulama ayarlarında](functions-app-settings.md) ayarlarsınız.

Kullanım durumunuza bağlı olarak, Dayanıklı İşlevler ölçeklenebilirliği önemli ölçüde iyileştirebilir. Daha fazla bilgi için bkz. [dayanıklı işlevler uygulama desenleri](./durable/durable-functions-overview.md?tabs=powershell#application-patterns).

>[!NOTE]
> "İstekler kullanılabilir çalışma alanları nedeniyle sıraya alındı" uyarısı alabilir, lütfen bunun bir hata olmadığını unutmayın. İleti, isteklerin sıraya alındığını ve önceki istekler tamamlandığında işleneceğini size söylecektir.

### <a name="considerations-for-using-concurrency"></a>Eşzamanlılık kullanma konuları

PowerShell, varsayılan olarak _tek bir iş parçacıklı_ betik dilidir. Ancak, aynı işlemde birden fazla PowerShell çalışma alanı kullanılarak eşzamanlılık eklenebilir. Oluşturulan çalışma alanları miktarı ```PSWorkerInProcConcurrencyUpperBound``` uygulama ayarıyla eşleşir. Aktarım hızı, seçilen planda kullanılabilir olan CPU ve bellek miktarından etkilenecek.

Azure PowerShell, size fazla yazma işleminden tasarruf etmenize yardımcı olmak için bazı _işlem düzeyi_ bağlamlar ve durumları kullanır. Ancak, işlev uygulamanızda eşzamanlılık özelliğini açıp durumu değiştirme eylemlerini çağırdığınızda, yarış koşullarına sahip olabilirsiniz. Bir çağrı belirli bir duruma bağlı olduğundan ve diğer çağrının durumu değiştiğinden, bu yarış durumlarının hata ayıklaması zordur.

Bazı işlemler oldukça uzun sürebileceğinden, Azure PowerShell eşzamanlılık içinde de bir değer vardır. Ancak, dikkatli ilerlemeniz gerekir. Bir yarış durumu yaşadığınızı düşünüyorsanız, PSWorkerInProcConcurrencyUpperBound uygulama ayarını olarak ayarlayın `1` ve bunun yerine eşzamanlılık için [dil çalışan işlem düzeyi yalıtımı](functions-app-settings.md#functions_worker_process_count) kullanın.

## <a name="configure-function-scriptfile"></a>Configure işlevi `scriptFile`

Varsayılan olarak, `run.ps1` karşılık gelen bir üst dizini paylaşan bir dosya olan bir PowerShell işlevi yürütülür `function.json` .

`scriptFile`İçindeki özelliği, `function.json` Aşağıdaki örnekte olduğu gibi görünen bir klasör yapısını almak için kullanılabilir:

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.ps1
```

Bu durumda, `function.json` için öğesine, `myFunction` `scriptFile` çalıştırmak için aktarılmış işlevi ile başvuran bir özelliği içerir.

```json
{
  "scriptFile": "../lib/PSFunction.ps1",
  "bindings": [
    // ...
  ]
}
```

## <a name="use-powershell-modules-by-configuring-an-entrypoint"></a>Giriş noktası yapılandırarak PowerShell modüllerini kullanma

Bu makalede, `run.ps1` Şablonlar tarafından oluşturulan varsayılan betik dosyasında PowerShell işlevleri gösterilmektedir.
Ancak, işlevlerinizi PowerShell modüllerine de ekleyebilirsiniz. `scriptFile` `entryPoint` function.json ' yapılandırma dosyasındaki ve alanlarını kullanarak modüldeki özel işlev kodunuza başvurabilirsiniz.

Bu durumda, `entryPoint` içinde başvurulan PowerShell modülündeki bir işlevin veya cmdlet 'in adıdır `scriptFile` .

Aşağıdaki klasör yapısını göz önünde bulundurun:

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.psm1
```

`PSFunction.psm1`Şunları içerir:

```powershell
function Invoke-PSTestFunc {
    param($InputBinding, $TriggerMetadata)

    Push-OutputBinding -Name OutputBinding -Value "output"
}

Export-ModuleMember -Function "Invoke-PSTestFunc"
```

Bu örnekte, için yapılandırması, `myFunction` `scriptFile` başka bir klasördeki bir PowerShell modülü olan başvuran bir özelliği içerir `PSFunction.psm1` .  `entryPoint`Özelliği, `Invoke-PSTestFunc` modüldeki giriş noktası olan işlevine başvurur.

```json
{
  "scriptFile": "../lib/PSFunction.psm1",
  "entryPoint": "Invoke-PSTestFunc",
  "bindings": [
    // ...
  ]
}
```

Bu yapılandırmayla, `Invoke-PSTestFunc` tam olarak bir olarak yürütülür `run.ps1` .

## <a name="considerations-for-powershell-functions"></a>PowerShell işlevleriyle ilgili hususlar

PowerShell işlevleriyle çalışırken, aşağıdaki bölümlerde yer aldığını göz önünde bulundurun.

### <a name="cold-start"></a>Soğuk başlangıç

[Sunucusuz barındırma modelinde](consumption-plan.md)Azure işlevleri geliştirirken soğuk başlar. *Soğuk başlatma* , bir isteği işlemek için işlev uygulamanızın çalışmaya başlaması için gereken süreyi ifade eder. İşlev uygulamanız işlem yapılmayan dönemler sırasında kapandığı için, tüketim planında soğuk başlatma daha sık gerçekleşir.

### <a name="bundle-modules-instead-of-using-install-module"></a>Kullanmak yerine modülleri paketleyin `Install-Module`

Komut dosyası her çağrıdan çalıştırılır. `Install-Module`Betiğinizdeki kullanmaktan kaçının. Bunun yerine `Save-Module` yayımlamadan önce kullanın, böylece işlevinizin modül indirmede zaman harcanmasına sahip olmaması gerekir. Soğuk başlıyorsa işlevlerinizi etkilese, işlev uygulamanızı *her zaman açık* veya bir [Premium plana](functions-premium-plan.md)ayarlanmış bir [App Service plana](dedicated-plan.md) dağıtmaya göz önünde bulundurun.

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için aşağıdaki kaynaklara bakın:

* [Azure İşlevleri için en iyi uygulamalar](functions-best-practices.md)
* [Azure İşlevleri geliştirici başvurusu](functions-reference.md)
* [Azure Işlevleri Tetikleyicileri ve bağlamaları](functions-triggers-bindings.md)

[host.json başvurusu]: functions-host-json.md
