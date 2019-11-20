---
title: Azure Işlevleri için uygulama ayarları başvurusu
description: Azure Işlevleri uygulama ayarları veya ortam değişkenleri için başvuru belgeleri.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/22/2018
ms.author: glenga
ms.openlocfilehash: 896179a393b870390991a8e9942f6e7287ec5c90
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73063300"
---
# <a name="app-settings-reference-for-azure-functions"></a>Azure Işlevleri için uygulama ayarları başvurusu

Bir işlev uygulamasındaki uygulama ayarları, bu işlev uygulaması için tüm işlevleri etkileyen genel yapılandırma seçeneklerini içerir. Yerel olarak çalıştırdığınızda, bu ayarlara yerel [ortam değişkenleri](functions-run-local.md#local-settings-file)olarak erişilir. Bu makalede, işlev uygulamalarında kullanılabilen uygulama ayarları listelenir.

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

[Host. JSON](functions-host-json.md) dosyasında ve [Local. Settings. JSON](functions-run-local.md#local-settings-file) dosyasında başka genel yapılandırma seçenekleri vardır.

## <a name="appinsights_instrumentationkey"></a>APPINSIGHTS_INSTRUMENTATIONKEY

Application Insights kullanıyorsanız Application Insights izleme anahtarı. Bkz. [Azure Işlevlerini izleme](functions-monitoring.md).

|Anahtar|Örnek değer|
|---|------------|
|APPINSIGHTS_INSTRUMENTATIONKEY|5dbdd5e9-af77-484b-9032-64f83bb83bb|

## <a name="azure_functions_environment"></a>AZURE_FUNCTIONS_ENVIRONMENT

Işlevler çalışma zamanının 2. x sürümünde, çalışma zamanı ortamı temelinde uygulama davranışını yapılandırır. Bu değer [başlatma sırasında okundu](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/Program.cs#L43). `AZURE_FUNCTIONS_ENVIRONMENT` herhangi bir değere ayarlayabilirsiniz, ancak [üç değer](/dotnet/api/microsoft.aspnetcore.hosting.environmentname) desteklenir: [geliştirme](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.development), [hazırlık](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.staging)ve [Üretim](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.production). `AZURE_FUNCTIONS_ENVIRONMENT` ayarlanmamışsa, varsayılan olarak yerel bir ortamda `Development` ve Azure üzerinde `Production`. Çalışma zamanı ortamını ayarlamak için bu ayar `ASPNETCORE_ENVIRONMENT` yerine kullanılmalıdır. 

## <a name="azurewebjobsdashboard"></a>AzureWebJobsDashboard

Günlükleri depolamak ve portaldaki **izleyici** sekmesinde görüntülemek için isteğe bağlı depolama hesabı bağlantı dizesi. Depolama hesabı Blobları, kuyrukları ve tabloları destekleyen bir genel amaçlı olmalıdır. Bkz. [depolama hesabı](functions-infrastructure-as-code.md#storage-account) ve [depolama hesabı gereksinimleri](functions-create-function-app-portal.md#storage-account-requirements).

|Anahtar|Örnek değer|
|---|------------|
|AzureWebJobsDashboard|DefaultEndpointsProtocol = https; AccountName = [ad]; AccountKey = [anahtar]|

> [!TIP]
> Performans ve deneyim için, AzureWebJobsDashboard yerine izleme için APPINSIGHTS_INSTRUMENTATIONKEY ve Application Insights kullanılması önerilir

## <a name="azurewebjobsdisablehomepage"></a>AzureWebJobsDisableHomepage

`true`, bir işlev uygulamasının kök URL 'SI için gösterilen varsayılan giriş sayfasını devre dışı bırakma anlamına gelir. `false` varsayılan değerdir.

|Anahtar|Örnek değer|
|---|------------|
|AzureWebJobsDisableHomepage|doğru|

Bu uygulama ayarı atlandığında veya `false`olarak ayarlandığında, URL `<functionappname>.azurewebsites.net`yanıt olarak aşağıdaki örneğe benzer bir sayfa görüntülenir.

![İşlev uygulaması giriş sayfası](media/functions-app-settings/function-app-landing-page.png)

## <a name="azurewebjobsdotnetreleasecompilation"></a>AzureWebJobsDotNetReleaseCompilation

`true`, .NET kodu derlerken yayın modunu kullanmak anlamına gelir; `false`, hata ayıklama modunu kullanma anlamına gelir. `true` varsayılan değerdir.

|Anahtar|Örnek değer|
|---|------------|
|AzureWebJobsDotNetReleaseCompilation|doğru|

## <a name="azurewebjobsfeatureflags"></a>AzureWebJobsFeatureFlags

Etkinleştirilecek beta özellikleri için virgülle ayrılmış bir liste. Bu bayraklar tarafından etkinleştirilen beta özellikleri üretime hazırlanmaz, ancak canlı çalışmadan önce deneysel kullanım için etkinleştirilebilir.

|Anahtar|Örnek değer|
|---|------------|
|AzureWebJobsFeatureFlags|özellik1, Özellik2|

## <a name="azurewebjobssecretstoragetype"></a>AzureWebJobsSecretStorageType

Anahtar depolaması için kullanılacak depoyu veya sağlayıcıyı belirtir. Şu anda, desteklenen depolar BLOB depolama ("blob") ve yerel dosya sistemi ("dosyalar") ' dir. Varsayılan, sürüm 2 ' deki blob ve sürüm 1 ' de dosya sistemidir.

|Anahtar|Örnek değer|
|---|------------|
|AzureWebJobsSecretStorageType|Dosyalar|

## <a name="azurewebjobsstorage"></a>AzureWebJobsStorage

Azure Işlevleri çalışma zamanı, HTTP ile tetiklenen işlevler hariç tüm işlevler için bu depolama hesabı bağlantı dizesini kullanır. Depolama hesabı Blobları, kuyrukları ve tabloları destekleyen bir genel amaçlı olmalıdır. Bkz. [depolama hesabı](functions-infrastructure-as-code.md#storage-account) ve [depolama hesabı gereksinimleri](functions-create-function-app-portal.md#storage-account-requirements).

|Anahtar|Örnek değer|
|---|------------|
|AzureWebJobsStorage|DefaultEndpointsProtocol = https; AccountName = [ad]; AccountKey = [anahtar]|

## <a name="azurewebjobs_typescriptpath"></a>AzureWebJobs_TypeScriptPath

TypeScript için kullanılan derleyicinin yolu. Gerekirse varsayılan ayarı geçersiz kılmanızı sağlar.

|Anahtar|Örnek değer|
|---|------------|
|AzureWebJobs_TypeScriptPath|%HOME%\typescript|

## <a name="function_app_edit_mode"></a>Işlev\_APP\_düzenleme\_modu

Azure portal düzenlemenin etkinleştirilip etkinleştirilmeyeceğini belirler. Geçerli değerler şunlardır "ReadWrite" ve "ReadOnly".

|Anahtar|Örnek değer|
|---|------------|
|Işlev\_APP\_düzenleme\_modu|özelliğinin|

## <a name="functions_extension_version"></a>\_UZANTı\_sürümü IŞLEVLERI

Bu işlev uygulamasında kullanılacak Işlevler çalışma zamanının sürümü. Ana sürümü olan bir tilde, bu ana sürümün en son sürümünü (örneğin, "~ 2") kullanır. Aynı ana sürüme yönelik yeni sürümler kullanılabilir olduğunda, bunlar işlev uygulamasına otomatik olarak yüklenir. Uygulamayı belirli bir sürüme sabitlemek için, tam sürüm numarasını (örneğin, "2.0.12345") kullanın. Varsayılan değer "~ 2" dir. `~1` değeri, uygulamanızı çalışma zamanının 1. x sürümüne sabitler.

|Anahtar|Örnek değer|
|---|------------|
|\_UZANTı\_sürümü IŞLEVLERI|~ 2|

## <a name="functions_worker_process_count"></a>Işlemler\_çalışan\_Işlem\_sayısı

Varsayılan değeri `1`olan en fazla dil çalışan işlemi sayısını belirtir. İzin verilen en büyük değer `10`. İşlev etkinleştirmeleri, dil çalışan süreçler arasında eşit olarak dağıtılır. Dil çalışan işlemleri, IŞLEVLER tarafından ayarlanan sayı\_çalışan\_Işlem\_SAYıSıNA ulaşıldığında her 10 saniyede bir oluşturulur. Birden çok dil çalışan işleminin kullanılması, [ölçeklendirmeyle](functions-scale.md)aynı değildir. İş yükünüz, CPU ile bağlantılı ve g/ç bağlantılı çağırma karışımına sahip olduğunda bu ayarı kullanmayı göz önünde bulundurun. Bu ayar tüm non-.NET dilleri için geçerlidir.

|Anahtar|Örnek değer|
|---|------------|
|Işlemler\_çalışan\_Işlem\_sayısı|2|


## <a name="functions_worker_runtime"></a>ÇALıŞAN\_çalışma zamanı\_IŞLEVLERI

İşlev uygulamasında yüklenecek dil çalışanı çalışma zamanı.  Bu, uygulamanızda kullanılan dile karşılık gelir (örneğin, "DotNet"). Birden çok dildeki işlevler için, bunları her biri karşılık gelen bir çalışan çalışma zamanı değeri olan birden çok uygulamaya yayımlamanız gerekir.  Geçerli değerler şunlardır `dotnet` (C#/F#), `node` (JavaScript/TypeScript), `java` (Java), `powershell` (PowerShell) ve `python` (Python).

|Anahtar|Örnek değer|
|---|------------|
|ÇALıŞAN\_çalışma zamanı\_IŞLEVLERI|olmalı|

## <a name="website_contentazurefileconnectionstring"></a>WEBSITE_CONTENTAZUREFILECONNECTIONSTRING

Yalnızca tüketim & Premium planlar için. İşlev uygulaması kodu ve yapılandırmasının depolandığı depolama hesabı için bağlantı dizesi. Bkz. [işlev uygulaması oluşturma](functions-infrastructure-as-code.md#create-a-function-app).

|Anahtar|Örnek değer|
|---|------------|
|WEBSITE_CONTENTAZUREFILECONNECTIONSTRING|DefaultEndpointsProtocol = https; AccountName = [ad]; AccountKey = [anahtar]|

## <a name="website_contentshare"></a>Web sItesI\_CONTENTSHARE

Yalnızca tüketim & Premium planlar için. İşlev uygulaması kodu ve yapılandırmasının dosya yolu. WEBSITE_CONTENTAZUREFILECONNECTIONSTRING ile kullanılır. Varsayılan değer, işlev uygulaması adı ile başlayan benzersiz bir dizedir. Bkz. [işlev uygulaması oluşturma](functions-infrastructure-as-code.md#create-a-function-app).

|Anahtar|Örnek değer|
|---|------------|
|WEBSITE_CONTENTSHARE|functionapp091999e2|

## <a name="website_max_dynamic_application_scale_out"></a>Web sItesI\_en yüksek\_dınamık\_uygulama\_ölçek\_GENIŞLETME

İşlev uygulamasının ölçeklenebilen en fazla örnek sayısı. Varsayılan sınır yoktur.

> [!NOTE]
> Bu ayar bir önizleme özelliğidir ve yalnızca bir değere ayarlanmışsa güvenilir < = 5

|Anahtar|Örnek değer|
|---|------------|
|Web sItesI\_en yüksek\_dınamık\_uygulama\_ölçek\_GENIŞLETME|5|

## <a name="website_node_default_version"></a>Web sItesI\_NODE\_DEFAULT_VERSION

_Yalnızca Windows._  
Windows üzerinde işlev uygulamanızı çalıştırırken kullanılacak Node. js sürümünü ayarlar. Çalışma zamanının hedeflenen ana sürümün kullanılabilir en son sürümünü kullanmasını sağlamak için bir tilde (~) kullanmanız gerekir. Örneğin, `~10`olarak ayarlandığında, Node. js 10 ' un en son sürümü kullanılır. Ana sürüm bir tilde ile hedeflenirse, ikincil sürümü el ile güncelleştirmeniz gerekmez. 

|Anahtar|Örnek değer|
|---|------------|
|Web sItesI\_NODE\_DEFAULT_VERSION|~ 10|

## <a name="website_run_from_package"></a>Web sItesI\_\_PAKETINDEN\_ÇALıŞTıR

İşlev uygulamanızın bağlı bir paket dosyasından çalıştırılmasını sağlar.

|Anahtar|Örnek değer|
|---|------------|
|Web sItesI\_\_PAKETINDEN\_ÇALıŞTıR|1|

Geçerli değerler, bir dağıtım paketi dosyasının konumunu çözen bir URL veya `1`. `1`olarak ayarlandığında, paketin `d:\home\data\SitePackages` klasöründe olması gerekir. Bu ayar ile ZIP dağıtımı kullanılırken, paket otomatik olarak bu konuma yüklenir. Önizlemede, bu ayar `WEBSITE_RUN_FROM_ZIP`olarak adlandırılmıştır. Daha fazla bilgi için bkz. [işlevlerinizi bir paket dosyasından çalıştırma](run-functions-from-deployment-package.md).

## <a name="azure_function_proxy_disable_local_call"></a>AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL

Varsayılan olarak Işlev proxy 'leri, yeni bir HTTP isteği oluşturmak yerine, proxy 'lerden doğrudan aynı İşlev Uygulaması işlevlere API çağrıları göndermek için bir kısayol kullanır. Bu ayar, bu davranışı devre dışı bırakmanızı sağlar.

|Anahtar|Değer|Açıklama|
|-|-|-|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|doğru|Yerel İşlev Uygulaması bir işleve işaret eden arka uç URL 'si olan çağrılar artık doğrudan işleve gönderilmez ve bunun yerine İşlev Uygulaması için HTTP ön ucuna geri yönlendirilir|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|yanlış|Varsayılan değer budur. Yerel İşlev Uygulaması bir işleve işaret eden arka uç URL 'si olan çağrılar, doğrudan bu Işleve iletilir|


## <a name="azure_function_proxy_backend_url_decode_slashes"></a>AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES

Bu ayar,% 2F ' nin, arka uç URL 'sine eklendiklerinde rota parametrelerinde eğik çizgi olarak çözülmüş olup olmadığını denetler. 

|Anahtar|Değer|Açıklama|
|-|-|-|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|doğru|Kodlanmış eğik çizgi içeren rota parametrelerinin kodu çözülür. `example.com/api%2ftest` `example.com/api/test` olacak|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|yanlış|Bu, varsayılan davranıştır. Tüm rota parametreleri değişmeden kalacak şekilde geçirilecek|

### <a name="example"></a>Örnek

İşte myfunction.com URL 'sindeki bir işlev uygulamasında bir proxy. JSON örneği

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
|URL kod çözme|Girdi|Çıktı|
|-|-|-|
|doğru|myfunction.com/test%2fapi|example.com/test/api
|yanlış|myfunction.com/test%2fapi|example.com/test%2fapi|


## <a name="next-steps"></a>Sonraki adımlar

[Uygulama ayarlarını güncelleştirmeyi öğrenin](functions-how-to-use-azure-function-app-settings.md#settings)

[Bkz. Host. JSON dosyasındaki genel ayarlar](functions-host-json.md)

[App Service uygulamalar için diğer uygulama ayarlarına bakın](https://github.com/projectkudu/kudu/wiki/Configurable-settings)
