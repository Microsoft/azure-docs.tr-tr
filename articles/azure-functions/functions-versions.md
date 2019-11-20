---
title: Azure Işlevleri çalışma zamanı sürümlerine genel bakış
description: Azure Işlevleri, çalışma zamanının birden çok sürümünü destekler. Aralarındaki farkları ve sizin için doğru olanı seçme hakkında bilgi edinin.
author: ggailey777
manager: gwallace
ms.service: azure-functions
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: glenga
ms.openlocfilehash: 9ca7006bb842cbe235d2e982e611613e1fd74ed9
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/18/2019
ms.locfileid: "72597421"
---
# <a name="azure-functions-runtime-versions-overview"></a>Azure Işlevleri çalışma zamanı sürümlerine genel bakış

Azure Işlevleri çalışma zamanının ana sürümleri, çalışma zamanının temel aldığı .NET sürümü ile ilgilidir. Aşağıdaki tabloda, çalışma zamanının geçerli sürümü, sürüm düzeyi ve ilgili .NET sürümü gösterilmektedir. 

| Çalışma zamanı sürümü | Yayın düzeyi<sup>1</sup> | .NET sürümü | 
| --------------- | ------------- | ------------ |
| 3.x  | önizleme | .NET Core 3. x | 
| 2.x | Genel Kullanım | .NET Core 2.2 |
| 'in | GA<sup>2</sup> | .NET Framework 4,6<sup>3</sup> |

<sup>1</sup> GA sürümleri, üretim senaryolarında desteklenir.   
<sup>2</sup> Sürüm 1. x bakım modunda. Geliştirmeler yalnızca sonraki sürümlerde sağlanır.   
<sup>3</sup> Yalnızca Azure portal veya Windows bilgisayarlarda yerel olarak geliştirme desteklenir.

>[!NOTE]  
> Işlevler çalışma zamanının 3. x sürümü önizlemededir ve üretim ortamlarında desteklenmez. Sürüm 3. x ' i denemek hakkında daha fazla bilgi için [bu duyuruya](https://dev.to/azure/develop-azure-functions-using-net-core-3-0-gcm)bakın.

Bu makalede çeşitli sürümler, her sürümü nasıl oluşturabileceğiniz ve sürümlerin nasıl değiştirileceği hakkında bazı farklılıklar açıklanır.

## <a name="languages"></a>Diller

Sürüm 2. x ile başlayarak, çalışma zamanı bir dil genişletilebilirlik modeli kullanır ve bir işlev uygulamasındaki tüm işlevler aynı dili paylaşmalıdır. İşlev uygulamasındaki işlevlerin dili, uygulama oluşturulurken seçilir ve [işlevler \_WORKER \_RUNTIME](functions-app-settings.md#functions_worker_runtime) ayarında saklanır. 

Azure Işlevleri 1. x deneysel dilleri yeni modeli kullanamaz, bu nedenle 2. x içinde desteklenmez. Aşağıdaki tablo, her çalışma zamanı sürümünde hangi programlama dillerinin desteklendiğini gösterir.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

Daha fazla bilgi için bkz. [Desteklenen diller](supported-languages.md).

## <a name="creating-1x-apps"></a>Belirli bir sürümde Çalıştır

Varsayılan olarak, Azure portal oluşturulan işlev uygulamaları ve Azure CLı tarafından sürüm 2. x olarak ayarlanır. Mümkün olduğunda, bu çalışma zamanı sürümünü kullanmanız gerekir. Gerekirse, sürüm 1. x çalışma zamanında bir işlev uygulamasını çalıştırmaya devam edebilirsiniz. Çalışma zamanı sürümünü yalnızca işlev uygulamanızı oluşturduktan sonra değiştirebilirsiniz, ancak herhangi bir işlev eklemeden önce. Çalışma zamanı sürümünü 1. x 'e nasıl sabitleyeceğinizi öğrenmek için bkz. [geçerli çalışma zamanı sürümünü görüntüleme ve güncelleştirme](set-runtime-version.md#view-and-update-the-current-runtime-version).

Ayrıca, önizleme aşamasında olan çalışma zamanının 3. x sürümüne de yükseltebilirsiniz. İşlevlerinizi .NET Core 3. x üzerinde çalıştırabilmeniz gerekiyorsa bunu yapın. 3\. x sürümüne yükseltmeyi öğrenmek için bkz. [geçerli çalışma zamanı sürümünü görüntüleme ve güncelleştirme](set-runtime-version.md#view-and-update-the-current-runtime-version).

## <a name="migrating-from-1x-to-later-versions"></a>1\. x 'den sonraki sürümlere geçiş

Sürüm 1. x çalışma zamanını kullanmak üzere yazılmış mevcut bir uygulamayı bunun yerine 2. x sürümünü kullanacak şekilde geçirmeyi tercih edebilirsiniz. Yapmanız gereken değişikliklerin çoğu, dil çalışma zamanındaki değişikliklerle ilgilidir, örneğin .NET Framework 4,7 ile .NET Core 2 arasındaki C# API değişiklikleri. Ayrıca, kodunuzun ve kitaplıklarınızın seçtiğiniz dil çalışma zamanıyla uyumlu olduğundan emin olmanız gerekir. Son olarak, aşağıda vurgulanan tetikleyici, bağlamalar ve özelliklerde değişiklik yaptığınızdan emin olun. En iyi geçiş sonuçları için, sürüm 2. x için yeni bir işlev uygulaması oluşturmanız ve var olan sürüm 1. x işlev kodunuzun yeni uygulamaya bağlantı noktası oluşturmanız gerekir.  

### <a name="changes-in-triggers-and-bindings"></a>Tetikleyiciler ve bağlamalardaki değişiklikler

Sürüm 2. x, uygulamanızdaki işlevler tarafından kullanılan belirli Tetikleyiciler ve bağlamalar için uzantıları yüklemenizi gerektirir. Bu HTTP ve Zamanlayıcı Tetikleyicileri için uzantı gerektirmeyen tek özel durum.  Daha fazla bilgi için bkz. [bağlama uzantılarını kaydetme ve yüklemeyi bağlama](./functions-bindings-register.md).

Ayrıca, sürümler arasında işlevin `function.json` veya özniteliklerinde birkaç değişiklik de vardır. Örneğin, Event hub `path` özelliği artık `eventHubName`. Her bağlamaya yönelik belgelerin bağlantıları için [mevcut bağlama tablosuna](#bindings) bakın.

### <a name="changes-in-features-and-functionality"></a>Özelliklerde ve işlevlerde yapılan değişiklikler

Yeni sürümde kaldırılan, güncellenen veya değiştirilmiş birkaç özellik. Bu bölümde, sürüm 1. x kullandıktan sonra 2. x sürümünde gördüğünüz değişikliklerin ayrıntıları yer aldığı bir ayrıntılardır.

2\. x sürümünde aşağıdaki değişiklikler yapılmıştır:

* HTTP uç noktalarını çağırma anahtarları, her zaman Azure Blob depolamada şifrelenir. 1\. x sürümünde anahtarlar Azure dosya depolama alanında depolanır. Bir uygulamayı 1. x sürümünden sürüm 2. x ' e yükseltirken, dosya depolamada bulunan mevcut gizlilikler sıfırlanır.

* Sürüm 2. x çalışma zamanı, Web kancası sağlayıcıları için yerleşik destek içermez. Bu değişiklik performansı artırmak için yapılmıştır. HTTP tetikleyicilerini Web kancaları için uç nokta olarak kullanmaya devam edebilirsiniz.

* Ana bilgisayar yapılandırma dosyası (Host. JSON) boş olmalıdır veya `"version": "2.0"` dize olmalıdır.

* İzlemeyi geliştirmek için, portalda [`AzureWebJobsDashboard`](functions-app-settings.md#azurewebjobsdashboard) ayarının kullanıldığı Web işleri panosu, [`APPINSIGHTS_INSTRUMENTATIONKEY`](functions-app-settings.md#appinsights_instrumentationkey) ayarını kullanan Azure Application Insights ile değiştirilmiştir. Daha fazla bilgi için bkz. [Azure Işlevlerini izleme](functions-monitoring.md).

* Bir işlev uygulamasındaki tüm işlevler aynı dili paylaşmalıdır. Bir işlev uygulaması oluşturduğunuzda, uygulama için bir çalışma zamanı yığını seçmeniz gerekir. Çalışma zamanı yığını, uygulama ayarlarındaki [`FUNCTIONS_WORKER_RUNTIME`](functions-app-settings.md#functions_worker_runtime) değeriyle belirtilir. Bu gereksinim, parmak izini ve başlangıç süresini artırmak için eklenmiştir. Yerel olarak geliştirilirken, bu ayarı [Local. Settings. JSON dosyasına](functions-run-local.md#local-settings-file)da dahil etmeniz gerekir.

* Bir App Service planındaki işlevler için varsayılan zaman aşımı 30 dakikaya dönüştürülür. Host. json ' daki [functiontimeout](functions-host-json.md#functiontimeout) ayarını kullanarak, zaman aşımını tekrar sınırsız olarak değiştirebilirsiniz.

* HTTP eşzamanlılık kısıtlılığı, örnek başına 100 eşzamanlı istek içeren tüketim planı işlevleri için varsayılan olarak uygulanır. Bunu, Host. JSON dosyasındaki [`maxConcurrentRequests`](functions-host-json.md#http) ayarında değiştirebilirsiniz.

* [.NET Core sınırlamaları](https://github.com/Azure/azure-functions-host/issues/3414)nedeniyle, komut dosyası ( F# . FSX) işlevleri için destek kaldırılmıştır. Derlenen F# işlevler (. FS) hala desteklenmektedir.

* Event Grid tetikleyici Web kancalarının URL biçimi `https://{app}/runtime/webhooks/{triggerName}` olarak değiştirildi.

### <a name="migrating-a-locally-developed-application"></a>Yerel olarak geliştirilmiş bir uygulamayı geçirme

1\. x çalışma zamanı sürümünü kullanarak yerel olarak geliştirmiş olduğunuz işlev uygulaması projelerine sahip olabilirsiniz. Sürüm 2. x ' e yükseltmek için, sürüm 2. x sürümüne ve mevcut kodunuzun bağlantı noktasına karşı yeni uygulamaya yönelik bir yerel işlev uygulama projesi oluşturmanız gerekir. Mevcut projeyi ve kodu, "yerinde" yükseltme için el ile güncelleştirebilirsiniz. Ancak, 1. x ve sürüm 2. x arasında hala yapmanız gerekebilecek bazı diğer geliştirmeler vardır. Örneğin, hata ayıklama C# nesnesi `TraceWriter`, `ILogger` olarak değiştirilmiştir. Yeni bir sürüm 2. x projesi oluşturarak, en son sürüm 2. x şablonlarına göre güncelleştirilmiş işlevlerle başlayabilirsiniz.

#### <a name="visual-studio-runtime-versions"></a>Visual Studio çalışma zamanı sürümleri

Visual Studio 'da, bir proje oluştururken çalışma zamanı sürümünü seçersiniz. Visual Studio için Azure Işlevleri araçları, hem önemli çalışma zamanı sürümlerini destekler. Hata ayıklama sırasında ve proje ayarlarına bağlı olarak yayımlandığında doğru sürüm kullanılır. Sürüm ayarları, aşağıdaki özelliklerde `.csproj` dosyasında tanımlanır:

##### <a name="version-1x"></a>Sürüm 1. x

```xml
<TargetFramework>net461</TargetFramework>
<AzureFunctionsVersion>v1</AzureFunctionsVersion>
```

##### <a name="version-2x"></a>Sürüm 2. x

```xml
<TargetFramework>netcoreapp2.2</TargetFramework>
<AzureFunctionsVersion>v2</AzureFunctionsVersion>
```

Projenizi hata ayıkladığınızda veya yayımladığınızda, çalışma zamanının doğru sürümü kullanılır.

#### <a name="vs-code-and-azure-functions-core-tools"></a>VS Code ve Azure Functions Core Tools

[Azure Functions Core Tools](functions-run-local.md) , komut satırı geliştirme ve ayrıca Visual Studio Code Için [Azure işlevleri uzantısı](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) tarafından kullanılır. Sürüm 2. x ' i geliştirmek için çekirdek araçların 2. x sürümünü yüklemelisiniz. Sürüm 1. x geliştirme, çekirdek araçların 1. x sürümünü gerektirir. Daha fazla bilgi için bkz. [Azure Functions Core Tools yüklemesi](functions-run-local.md#install-the-azure-functions-core-tools).

Visual Studio Code geliştirme için, Ayrıca, `azureFunctions.projectRuntime` için Kullanıcı ayarını, yüklü araçların sürümüyle eşleşecek şekilde güncelleştirmeniz gerekebilir.  Bu ayar, işlev uygulaması oluşturma sırasında kullanılan şablonları ve dilleri de güncelleştirir.

### <a name="changing-version-of-apps-in-azure"></a>Azure 'da uygulamaların sürümünü değiştirme

Azure 'da yayımlanan uygulamalar tarafından kullanılan Işlevlerin çalışma zamanının sürümü [`FUNCTIONS_EXTENSION_VERSION`](functions-app-settings.md#functions_extension_version) uygulama ayarı tarafından belirlenir. @No__t_0 değeri sürüm 2. x çalışma zamanına ve `~1` sürüm 1. x çalışma zamanını hedefliyor. Bu ayarı rastgele değiştirmeyin, çünkü işlevinizdeki diğer uygulama ayarı değişiklikleri ve kod değişiklikleri muhtemelen gereklidir. İşlev uygulamanızı farklı bir çalışma zamanı sürümüne geçirmek için önerilen yöntem hakkında bilgi edinmek için bkz. [Azure işlevleri çalışma zamanı sürümlerini hedefleme](set-runtime-version.md).

## <a name="bindings"></a>Bağlamalar

Çalışma zamanı, sürüm 2. x ile başlayarak bu avantajları sunan yeni bir [bağlama genişletilebilirlik modeli](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview) kullanır:

* Üçüncü taraf bağlama uzantıları için destek.

* Çalışma zamanının ve bağlamaların ayrılması. Bu değişiklik, bağlama uzantılarının bağımsız olarak yayınlanabilmesini ve serbest bırakılacağını sağlar. Örneğin, temel bir SDK 'nın daha yeni bir sürümüne bağımlı olan bir uzantının sürümüne yükseltmeyi tercih edebilirsiniz.

* Yalnızca kullanımdaki bağlamaların bilinen ve çalışma zamanı tarafından yüklendiği, daha hafif bir yürütme ortamıdır.

HTTP ve Zamanlayıcı Tetikleyicileri hariç olmak üzere tüm bağlamalar, işlev uygulaması projesine açıkça eklenmelidir veya portalda kayıtlı olmalıdır. Daha fazla bilgi için bkz. [bağlama uzantılarını kaydetme](./functions-bindings-expressions-patterns.md).

Aşağıdaki tabloda, her çalışma zamanı sürümünde hangi bağlamaların desteklendiği gösterilmektedir.

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için aşağıdaki kaynaklara bakın:

* [Azure İşlevleri’ni yerel olarak kodlama ve test etme](functions-run-local.md)
* [Azure Işlevleri çalışma zamanı sürümlerini hedefleme](set-runtime-version.md)
* [Sürüm notları](https://github.com/Azure/azure-functions-host/releases)
