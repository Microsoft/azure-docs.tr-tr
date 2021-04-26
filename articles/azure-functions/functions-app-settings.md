---
title: Azure İşlevleri için uygulama ayarları başvurusu
description: Azure Işlevleri uygulama ayarları veya ortam değişkenleri için başvuru belgeleri.
ms.topic: conceptual
ms.date: 09/22/2018
ms.openlocfilehash: 327f120d387a3a08f0de9db2da718d530346e545
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104773088"
---
# <a name="app-settings-reference-for-azure-functions"></a>Azure İşlevleri için uygulama ayarları başvurusu

Bir işlev uygulamasındaki uygulama ayarları, bu işlev uygulaması için tüm işlevleri etkileyen genel yapılandırma seçeneklerini içerir. Yerel olarak çalıştırdığınızda, bu ayarlara yerel [ortam değişkenleri](functions-run-local.md#local-settings-file)olarak erişilir. Bu makalede, işlev uygulamalarında kullanılabilen uygulama ayarları listelenir.

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

Dosyasında ve [local.settings.json](functions-run-local.md#local-settings-file) dosyasında [host.js](functions-host-json.md) başka genel yapılandırma seçenekleri vardır.

> [!NOTE]  
> Dosya üzerinde host.jsdeğiştirmek zorunda kalmadan değerleri ayarlama host.jsgeçersiz kılmak için uygulama ayarlarını kullanabilirsiniz. Bu, belirli bir ortam için ayarlarda belirli host.jsyapılandırmanız veya değiştirmeniz gereken senaryolar için yararlıdır. Bu Ayrıca, projenizin yeniden yayımlanmasını gerektirmeden ayarları host.jsdeğiştirmenize de olanak tanır. Daha fazla bilgi edinmek için [ başvuruhost.jsmakalesine](functions-host-json.md#override-hostjson-values)bakın. İşlev uygulaması ayarlarında yapılan değişiklikler, işlev uygulamanızın yeniden başlatılmasını gerektirir.

## <a name="appinsights_instrumentationkey"></a>APPINSIGHTS_INSTRUMENTATIONKEY

Application Insights için izleme anahtarı. Ya da yalnızca birini `APPINSIGHTS_INSTRUMENTATIONKEY` kullanın `APPLICATIONINSIGHTS_CONNECTION_STRING` . Application Insights bir bağımsız bulutu 'nda çalıştığında kullanın `APPLICATIONINSIGHTS_CONNECTION_STRING` . Daha fazla bilgi için bkz. [Azure işlevleri için izlemeyi yapılandırma](configure-monitoring.md). 

|Anahtar|Örnek değer|
|---|------------|
|APPINSIGHTS_INSTRUMENTATIONKEY|55555555-af77-484b-9032-64f83bb83bb|

## <a name="applicationinsights_connection_string"></a>APPLICATIONINSIGHTS_CONNECTION_STRING

Application Insights için bağlantı dizesi. `APPLICATIONINSIGHTS_CONNECTION_STRING` `APPINSIGHTS_INSTRUMENTATIONKEY` Aşağıdaki durumlarda yerine kullanın:

+ İşlev uygulamanız, bağlantı dizesi kullanılarak desteklenen eklenmiş özelleştirmeleri gerektirdiğinde. 
+ Application Insights örneğiniz, özel bir uç nokta gerektiren bir bağımsız bulutu 'nda çalıştığında.

Daha fazla bilgi için bkz. [bağlantı dizeleri](../azure-monitor/app/sdk-connection-string.md). 

|Anahtar|Örnek değer|
|---|------------|
|APPLICATIONINSIGHTS_CONNECTION_STRING|Instrumentationkey = [anahtar]; ınestionendpoint = [URL]; LiveEndpoint = [URL]; ProfilerEndpoint = [URL]; Anlık görüntü Tendpoint = [URL];|

## <a name="azure_function_proxy_disable_local_call"></a>AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL

Varsayılan olarak, [işlev proxy](functions-proxies.md) 'LERI, API çağrılarını doğrudan aynı işlev uygulamasındaki işlevlere doğrudan göndermek için bir kısayol kullanır. Bu kısayol, yeni bir HTTP isteği oluşturmak yerine kullanılır. Bu ayar, bu kısayol davranışını devre dışı bırakmanızı sağlar.

|Anahtar|Değer|Açıklama|
|-|-|-|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|true|Yerel işlev uygulamasındaki bir işleve işaret eden arka uç URL 'SI olan çağrılar doğrudan işleve gönderilmez. Bunun yerine, istekler işlev uygulaması için HTTP ön ucunda yeniden yönlendirilir.|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|yanlış|Yerel işlev uygulamasındaki bir işleve işaret eden arka uç URL 'SI olan çağrılar doğrudan işleve iletilir. Varsayılan değer budur. |

## <a name="azure_function_proxy_backend_url_decode_slashes"></a>AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES

Bu ayar, `%2F` arka uç URL 'sine eklendiklerinde karakterlerin yol parametrelerinde eğik çizgi olarak çözülmüş olup olmadığını denetler. 

|Anahtar|Değer|Açıklama|
|-|-|-|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|true|Kodlanmış eğik çizgileri olan rota parametreleri kodu çözülür. |
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|yanlış|Tüm rota parametreleri değiştirilmeden ve varsayılan davranış olarak geçirilir. |

Örneğin, etki alanındaki bir işlev uygulaması için dosyadaki proxies.jsgöz önünde bulundurun `myfunction.com` .

```JSON
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "root": {
            "matchCondition": {
                "route": "/{*all}"
            },
            "backendUri": "example.com/{all}"
        }
    }
}
```

, `AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES` Olarak ayarlandığında `true` , URL `example.com/api%2ftest` olarak çözümlenir `example.com/api/test` . Varsayılan olarak, URL değişmeden kalır `example.com/test%2fapi` . Daha fazla bilgi için bkz. [işlev proxy 'leri](functions-proxies.md).

## <a name="azure_functions_environment"></a>AZURE_FUNCTIONS_ENVIRONMENT

2. x ve sonraki Işlevler çalışma zamanının sürümlerinde, çalışma zamanı ortamı temelinde uygulama davranışını yapılandırır. Bu değer başlatma sırasında okunurdur ve herhangi bir değere ayarlanabilir. Yalnızca, ve değerleri `Development` `Staging` `Production` çalışma zamanı tarafından kabul edilir. Azure 'da çalışırken bu uygulama ayarı mevcut olmadığında, ortamın olduğu varsayılır `Production` . `ASPNETCORE_ENVIRONMENT`Azure 'daki çalışma zamanı ortamını dışında bir şekilde değiştirmeniz gerekiyorsa, bu ayarı kullanın `Production` . Azure Functions Core Tools, `AZURE_FUNCTIONS_ENVIRONMENT` `Development` yerel bir bilgisayarda çalışırken olarak ayarlanır ve bu, dosyadaki local.settings.jsgeçersiz kılınamaz. Daha fazla bilgi için bkz. [ortam tabanlı başlangıç sınıfı ve yöntemleri](/aspnet/core/fundamentals/environments#environment-based-startup-class-and-methods).

## <a name="azurefunctionsjobhost__"></a>AzureFunctionsJobHost__\*

Işlev çalışma zamanının sürüm 2. x ve sonraki sürümlerinde, uygulama ayarları geçerli ortamdaki ayarlarda [host.js](functions-host-json.md) geçersiz kılabilir. Bu geçersiz kılmalar, adlı uygulama ayarları olarak ifade edilir `AzureFunctionsJobHost__path__to__setting` . Daha fazla bilgi için bkz. [değerler üzerinde host.jsgeçersiz kılma](functions-host-json.md#override-hostjson-values).

## <a name="azurewebjobsdashboard"></a>AzureWebJobsDashboard

Günlükleri depolamak ve portaldaki **izleyici** sekmesinde görüntülemek için isteğe bağlı depolama hesabı bağlantı dizesi. Bu ayar yalnızca Azure Işlevleri çalışma zamanının 1. x sürümünü hedefleyen uygulamalar için geçerlidir. Depolama hesabı Blobları, kuyrukları ve tabloları destekleyen bir genel amaçlı olmalıdır. Daha fazla bilgi için bkz. [depolama hesabı gereksinimleri](storage-considerations.md#storage-account-requirements).

|Anahtar|Örnek değer|
|---|------------|
|AzureWebJobsDashboard|DefaultEndpointsProtocol = https; AccountName = <name> ; AccountKey =<key>|

> [!NOTE]
> Daha iyi performans ve deneyim için, çalışma zamanı sürüm 2. x ve sonraki sürümleri, APPINSIGHTS_INSTRUMENTATIONKEY ve izleme için uygulama öngörülerini kullanır `AzureWebJobsDashboard` .

## <a name="azurewebjobsdisablehomepage"></a>AzureWebJobsDisableHomepage

`true` , bir işlev uygulamasının kök URL 'SI için gösterilen varsayılan giriş sayfasını devre dışı bırakır. `false` varsayılan değerdir.

|Anahtar|Örnek değer|
|---|------------|
|AzureWebJobsDisableHomepage|true|

Bu uygulama ayarı atlandığında veya olarak ayarlandığında `false` , URL 'ye yanıt olarak aşağıdaki örneğe benzer bir sayfa görüntülenir `<functionappname>.azurewebsites.net` .

![İşlev uygulaması giriş sayfası](media/functions-app-settings/function-app-landing-page.png)

## <a name="azurewebjobsdotnetreleasecompilation&quot;></a>AzureWebJobsDotNetReleaseCompilation

`true` , .NET kodu derlenirken yayın modunun kullanılacağı anlamına gelir; `false` hata ayıklama modunu kullanma anlamına gelir. `true` varsayılan değerdir.

|Anahtar|Örnek değer|
|---|------------|
|AzureWebJobsDotNetReleaseCompilation|true|

## <a name=&quot;azurewebjobsfeatureflags&quot;></a>AzureWebJobsFeatureFlags

Etkinleştirilecek beta özellikleri için virgülle ayrılmış bir liste. Bu bayraklar tarafından etkinleştirilen beta özellikleri üretime hazırlanmaz, ancak canlı çalışmadan önce deneysel kullanım için etkinleştirilebilir.

|Anahtar|Örnek değer|
|---|------------|
|AzureWebJobsFeatureFlags|özellik1, Özellik2|

## <a name=&quot;azurewebjobssecretstoragetype&quot;></a>AzureWebJobsSecretStorageType

Anahtar depolaması için kullanılacak depoyu veya sağlayıcıyı belirtir. Şu anda, desteklenen depolar BLOB depolama (&quot;blob") ve yerel dosya sistemi ("dosyalar") ' dir. Varsayılan, sürüm 2 ' deki blob ve sürüm 1 ' de dosya sistemidir.

|Anahtar|Örnek değer|
|---|------------|
|AzureWebJobsSecretStorageType|Dosyalar|

## <a name="azurewebjobsstorage"></a>AzureWebJobsStorage

Azure Işlevleri çalışma zamanı, normal işlem için bu depolama hesabı bağlantı dizesini kullanır. Bu depolama hesabının bazı kullanımları anahtar yönetimi, Zamanlayıcı tetikleyici yönetimi ve Event Hubs kontrol noktaları içerir. Depolama hesabı Blobları, kuyrukları ve tabloları destekleyen bir genel amaçlı olmalıdır. Bkz. [depolama hesabı](functions-infrastructure-as-code.md#storage-account) ve [depolama hesabı gereksinimleri](storage-considerations.md#storage-account-requirements).

|Anahtar|Örnek değer|
|---|------------|
|AzureWebJobsStorage|DefaultEndpointsProtocol = https; AccountName = [ad]; AccountKey = [anahtar]|

## <a name="azurewebjobs_typescriptpath"></a>AzureWebJobs_TypeScriptPath

TypeScript için kullanılan derleyicinin yolu. Gerekirse varsayılan ayarı geçersiz kılmanızı sağlar.

|Anahtar|Örnek değer|
|---|------------|
|AzureWebJobs_TypeScriptPath|%HOME%\typescript|

## <a name="function_app_edit_mode"></a>Işlev \_ uygulaması \_ düzenleme \_ modu

Azure portal düzenlemenin etkinleştirilip etkinleştirilmeyeceğini belirler. Geçerli değerler şunlardır "ReadWrite" ve "ReadOnly".

|Anahtar|Örnek değer|
|---|------------|
|Işlev \_ uygulaması \_ düzenleme \_ modu|readonly|

## <a name="functions_extension_version"></a>Işlev \_ Uzantısı \_ sürümü

İşlev uygulamanızı barındıran Işlevlerin çalışma zamanının sürümü. `~`Ana sürümü olan bir tilde (), bu ana sürümün en son sürümünü (örneğin, "~ 3") kullanır. Aynı ana sürüme yönelik yeni sürümler kullanılabilir olduğunda, bunlar işlev uygulamasına otomatik olarak yüklenir. Uygulamayı belirli bir sürüme sabitlemek için, tam sürüm numarasını (örneğin, "3.0.12345") kullanın. Varsayılan değer "~ 3". Bir değeri `~1` , uygulamanızı çalışma zamanının 1. x sürümüne sabitlemelerini sağlar. Daha fazla bilgi için bkz. [Azure işlevleri çalışma zamanı sürümlerine genel bakış](functions-versions.md).

|Anahtar|Örnek değer|
|---|------------|
|Işlev \_ Uzantısı \_ sürümü|~ 3|

## <a name="functions_v2_compatibility_mode"></a>IŞLEVLER \_ v2 \_ Uyumluluk \_ modu

Bu ayar, işlev uygulamanızın sürüm 3. x çalışma zamanı üzerinde sürüm 2. x uyumlu modda çalışmasını sağlar. Bu ayarı yalnızca [, işlev uygulamanızı çalışma zamanının 2. x sürümünden 3. x sürümüne yükseltirken](functions-versions.md#migrating-from-2x-to-3x)sorunlarla karşılaşırsanız kullanın. 

>[!IMPORTANT]
> Bu ayar yalnızca, uygulamanızı 3. x sürümünde doğru şekilde çalışacak şekilde güncelleştirdiğinizde kısa süreli geçici bir çözüm olarak tasarlanmıştır. [2. x çalışma zamanının desteklendiği](functions-versions.md)sürece bu ayar desteklenir. Uygulamanızın bu ayarı kullanmadan sürüm 3. x üzerinde çalışmasını engelleyen sorunlarla karşılaşırsanız, lütfen [sorununuzu bildirin](https://github.com/Azure/azure-functions-host/issues/new?template=Bug_report.md).

[İşlev \_ Uzantısı \_ sürümünün](functions-app-settings.md#functions_extension_version) olarak ayarlanmasını gerektirir `~3` .

|Anahtar|Örnek değer|
|---|------------|
|IŞLEVLER \_ v2 \_ Uyumluluk \_ modu|true|

## <a name="functions_worker_process_count"></a>Işlev \_ çalışan \_ işlem \_ sayısı

Varsayılan değeri olan en fazla dil çalışan işlemi sayısını belirtir `1` . İzin verilen en büyük değer `10` . İşlev etkinleştirmeleri, dil çalışan süreçler arasında eşit olarak dağıtılır. Dil çalışan işlemleri, IŞLEVLERIN \_ çalışan işlem sayısına göre ayarlanan sayıya ulaşılana kadar her 10 saniyede bir oluşturulur \_ \_ . Birden çok dil çalışan işleminin kullanılması, [ölçeklendirmeyle](functions-scale.md)aynı değildir. İş yükünüz, CPU ile bağlantılı ve g/ç bağlantılı çağırma karışımına sahip olduğunda bu ayarı kullanmayı göz önünde bulundurun. Bu ayar tüm non-.NET dilleri için geçerlidir.

|Anahtar|Örnek değer|
|---|------------|
|Işlev \_ çalışan \_ işlem \_ sayısı|2|

## <a name="functions_worker_runtime"></a>IŞLEVLER \_ Worker \_ çalışma zamanı

İşlev uygulamasında yüklenecek dil çalışanı çalışma zamanı.  Bu, uygulamanızda kullanılan dile karşılık gelir (örneğin, `dotnet` ). Azure Işlevleri çalışma zamanının 2. x sürümünden başlayarak, belirli bir işlev uygulaması yalnızca tek bir dili destekleyebilir.   

|Anahtar|Örnek değer|
|---|------------|
|IŞLEVLER \_ Worker \_ çalışma zamanı|node|

Geçerli değerler:

| Değer | Dil |
|---|---|
| `dotnet` | [C# (sınıf kitaplığı)](functions-dotnet-class-library.md)<br/>[C# (betik)](functions-reference-csharp.md) |
| `dotnet-isolated` | [C# (Yalıtılmış işlem)](dotnet-isolated-process-guide.md) |
| `java` | [Java](functions-reference-java.md) |
| `node` | [JavaScript](functions-reference-node.md)<br/>[TypeScript](functions-reference-node.md#typescript) |
| `powershell` | [PowerShell](functions-reference-powershell.md) |
| `python` | [Python](functions-reference-python.md) |

## <a name="pip_extra_index_url"></a>PıP \_ ekstra \_ Dizin \_ URL 'si

Bu ayarın değeri, Python uygulamaları için özel bir paket dizin URL 'SI gösterir. Bir ek paket dizininde bulunan özel bağımlılıklar kullanarak uzak bir derlemeyi çalıştırmanız gerektiğinde bu ayarı kullanın.   

|Anahtar|Örnek değer|
|---|------------|
|PıP \_ ekstra \_ Dizin \_ URL 'si|http://my.custom.package.repo/simple |

Daha fazla bilgi edinmek için bkz. Python geliştirici başvurusunda [özel bağımlılıklar](functions-reference-python.md#remote-build-with-extra-index-url) .

## <a name="python_threadpool_thread_count"></a>PYTHON \_ THREADPOOL \_ iş parçacığı \_ sayısı

Python dili çalışanının, Python sürümü için varsayılan bir değer ile işlev etkinleştirmeleri yürütmek için kullanacağı en fazla iş parçacığı sayısını belirtir `1` `3.8` . Python sürümü `3.9` ve üstü için değer olarak ayarlanır `None` . Bu ayarın yürütmeler sırasında ayarlanacak iş parçacığı sayısını garanti vermediğini unutmayın. Ayar, Python 'un belirtilen değere iş parçacığı sayısını genişletmesine izin verir. Bu ayar yalnızca Python işlevleri uygulamaları için geçerlidir. Ek olarak, bu ayar, eş zamanlı işlevlere yönelik değil, çağrı için de geçerlidir.

|Anahtar|Örnek değer|En yüksek değer|
|---|------------|---------|
|PYTHON \_ THREADPOOL \_ iş parçacığı \_ sayısı|2|32|

## <a name="scale_controller_logging_enabled"></a>ÖLÇEK \_ denetleyicisi \_ günlüğü \_ etkin

_Bu ayar şu an önizleme aşamasındadır._  

Bu ayar Azure Işlevleri ölçek denetleyicisindeki günlüğü denetler. Daha fazla bilgi için bkz. [Ölçek denetleyicisi günlükleri](functions-monitoring.md#scale-controller-logs).

|Anahtar|Örnek değer|
|-|-|
|SCALE_CONTROLLER_LOGGING_ENABLED|Appınsights: ayrıntılı|

Bu anahtarın değeri `<DESTINATION>:<VERBOSITY>` , aşağıdaki şekilde tanımlanan biçimde sağlanır:

[!INCLUDE [functions-scale-controller-logging](../../includes/functions-scale-controller-logging.md)]

## <a name="website_contentazurefileconnectionstring"></a>Web sItesI \_ CONTENTAZUREFILECONNECTIONSTRING

İşlev uygulaması kodu ve yapılandırmasının, Windows üzerinde çalışan olay odaklı ölçeklendirme planlarında depolandığı depolama hesabı için bağlantı dizesi. Daha fazla bilgi için bkz. [işlev uygulaması oluşturma](functions-infrastructure-as-code.md#windows).

|Anahtar|Örnek değer|
|---|------------|
|WEBSITE_CONTENTAZUREFILECONNECTIONSTRING|DefaultEndpointsProtocol = https; AccountName = [ad]; AccountKey = [anahtar]|

Yalnızca Premium planına veya Windows üzerinde çalışan bir tüketim planına dağıtım yaparken kullanılır. Linux çalıştıran tüketimlerini planları için desteklenmez. Bu ayarı değiştirmek veya kaldırmak, işlev uygulamanızın başlatılamamasına neden olabilir. Daha fazla bilgi için [Bu sorun giderme makalesine](functions-recover-storage-account.md#storage-account-application-settings-were-deleted)bakın. 

## <a name="website_contentovervnet"></a>Web sItesI \_ contenentovervnet

Yalnızca Premium planlar için. Bir değeri, `1` depolama hesabınız bir sanal ağla sınırlı olduğunda, işlev uygulamanızın ölçeğini belirlemenizi sağlar. Depolama hesabınızı bir sanal ağ ile kısıtlamadan bu ayarı etkinleştirmeniz gerekir. Daha fazla bilgi edinmek için bkz. [depolama hesabınızı bir sanal ağla kısıtlama](functions-networking-options.md#restrict-your-storage-account-to-a-virtual-network).

|Anahtar|Örnek değer|
|---|------------|
|WEBSITE_CONTENTOVERVNET|1|

## <a name="website_contentshare"></a>Web sItesI \_ contentshare

Windows üzerinde olay odaklı ölçeklendirme planında, işlev uygulaması kodu ve yapılandırmasının dosya yolu. WEBSITE_CONTENTAZUREFILECONNECTIONSTRING ile kullanılır. Varsayılan değer, işlev uygulaması adı ile başlayan benzersiz bir dizedir. Bkz. [işlev uygulaması oluşturma](functions-infrastructure-as-code.md#windows).

|Anahtar|Örnek değer|
|---|------------|
|WEBSITE_CONTENTSHARE|functionapp091999e2|

Yalnızca Premium planına veya Windows üzerinde çalışan bir tüketim planına dağıtım yaparken kullanılır. Linux çalıştıran tüketimlerini planları için desteklenmez. Bu ayarı değiştirmek veya kaldırmak, işlev uygulamanızın başlatılamamasına neden olabilir. Daha fazla bilgi için [Bu sorun giderme makalesine](functions-recover-storage-account.md#storage-account-application-settings-were-deleted)bakın.

Dağıtım sırasında bir işlev uygulaması oluşturmak için bir Azure Resource Manager kullanırken, şablonda WEBSITE_CONTENTSHARE eklemeyin. Bu uygulama ayarı dağıtım sırasında oluşturulur. Daha fazla bilgi için bkz. [işlev uygulamanız için kaynak dağıtımını otomatikleştirme](functions-infrastructure-as-code.md#windows).   

## <a name="website_dns_server"></a>Web sItesI \_ DNS \_ sunucusu

IP adreslerini çözümlerken bir uygulama tarafından kullanılan DNS sunucusunu ayarlar. Bu ayar genellikle [Azure DNS özel bölgeler](functions-networking-options.md#azure-dns-private-zones) ve [Özel uç noktalar](functions-networking-options.md#restrict-your-storage-account-to-a-virtual-network)gibi belirli ağ işlevleri kullanılırken gereklidir.   

|Anahtar|Örnek değer|
|---|------------|
|Web sItesI \_ DNS \_ sunucusu|168.63.129.16|

## <a name="website_max_dynamic_application_scale_out"></a>Web sItesI \_ en büyük \_ dinamik \_ uygulama \_ ölçeği \_ genişletme

Uygulamanın ölçeklenebilen en fazla örnek sayısı. Varsayılan sınır yoktur.

> [!IMPORTANT]
> Bu ayar önizlemededir.  [İşlev için en fazla ölçek genişletme için bir uygulama özelliği](./event-driven-scaling.md#limit-scale-out) eklenmiştir ve ölçeği ölçeğini sınırlamak için önerilen yoldur.

|Anahtar|Örnek değer|
|---|------------|
|Web sItesI \_ en büyük \_ dinamik \_ uygulama \_ ölçeği \_ genişletme|5|

## <a name="website_node_default_version"></a>Web sItesI \_ düğümü \_ DEFAULT_VERSION

_Yalnızca Windows._  
İşlev uygulamanızı Windows 'da çalıştırırken kullanılacak Node.js sürümünü ayarlar. Çalışma zamanının hedeflenen ana sürümün kullanılabilir en son sürümünü kullanmasını sağlamak için bir tilde (~) kullanmanız gerekir. Örneğin, olarak ayarlandığında `~10` , Node.js 10 ' un en son sürümü kullanılır. Ana sürüm bir tilde ile hedeflenirse, ikincil sürümü el ile güncelleştirmeniz gerekmez. 

|Anahtar|Örnek değer|
|---|------------|
|Web sItesI \_ düğümü \_ DEFAULT_VERSION|~ 10|

## <a name="website_run_from_package"></a>paketten Web sItesI \_ çalıştırma \_ \_

İşlev uygulamanızın bağlı bir paket dosyasından çalıştırılmasını sağlar.

|Anahtar|Örnek değer|
|---|------------|
|paketten Web sItesI \_ çalıştırma \_ \_|1|

Geçerli değerler, bir dağıtım paketi dosyasının konumunu çözen bir URL ya da `1` . Olarak ayarlandığında `1` , paketin klasörde olması gerekir `d:\home\data\SitePackages` . Bu ayar ile ZIP dağıtımı kullanılırken, paket otomatik olarak bu konuma yüklenir. Önizlemede, bu ayar adlandırılmıştı `WEBSITE_RUN_FROM_ZIP` . Daha fazla bilgi için bkz. [işlevlerinizi bir paket dosyasından çalıştırma](run-functions-from-deployment-package.md).

## <a name="website_time_zone"></a>Web sItesI \_ Saat \_ dilimi

İşlev uygulamanız için saat dilimini ayarlamanıza olanak sağlar. 

|Anahtar|İşletim Sistemi|Örnek değer|
|---|--|------------|
|Web sItesI \_ Saat \_ dilimi|Windows|Doğu Standart Saati|
|Web sItesI \_ Saat \_ dilimi|Linux|Amerika/New_York|

[!INCLUDE [functions-timezone](../../includes/functions-timezone.md)]

## <a name="website_vnet_route_all"></a>Web sItesI \_ VNET \_ yönlendirme \_ Tümü

Uygulamadaki tüm giden trafiğin sanal ağ üzerinden yönlendirilip yönlendirilmeyeceğini gösterir. Ayar değeri `1` , tüm trafiğin sanal ağ üzerinden yönlendirildiğini gösterir. [Bölgesel sanal ağ tümleştirmesinin](functions-networking-options.md#regional-virtual-network-integration)özelliklerini kullanarak kullanırken bu ayarı kullanmanız gerekir. [Statik bir gıden IP adresi tanımlamak için bir sanal ağ NAT ağ geçidi kullanıldığında](functions-how-to-use-nat-gateway.md)da kullanılır. 

|Anahtar|Örnek değer|
|---|------------|
|Web sItesI \_ VNET \_ yönlendirme \_ Tümü|1|

## <a name="next-steps"></a>Sonraki adımlar

[Uygulama ayarlarını güncelleştirmeyi öğrenin](functions-how-to-use-azure-function-app-settings.md#settings)

[Dosyadaki host.jsgenel ayarlar ' a bakın](functions-host-json.md)

[App Service uygulamalar için diğer uygulama ayarlarına bakın](https://github.com/projectkudu/kudu/wiki/Configurable-settings)
