---
title: Azure Işlevleri için izlemeyi yapılandırma
description: İzleme için işlev uygulamanızı Application Insights bağlama ve veri toplamayı yapılandırma hakkında bilgi edinin.
ms.date: 8/31/2020
ms.topic: how-to
ms.custom: contperf-fy21q2
ms.openlocfilehash: 5007009d9aabf9a1c1c6e1d5c2f286c0ba25b340
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99493762"
---
# <a name="how-to-configure-monitoring-for-azure-functions"></a>Azure Işlevleri için izlemeyi yapılandırma

Azure Işlevleri, işlev uygulamalarınızı izlemenizi daha iyi hale getirebilmeniz için Application Insights ile tümleşir. Azure Izleyici 'nin bir özelliği olan Application Insights, uygulamanızın günlüklere yazdığı bilgiler de dahil olmak üzere, işlev uygulamanız tarafından oluşturulan verileri toplayan genişletilebilir bir uygulama performans yönetimi (APM) hizmetidir. Application Insights tümleştirme, genellikle işlev uygulamanız oluşturulduğunda etkinleştirilir. Uygulamanızda izleme anahtarı ayarlanmamışsa, öncelikle [Application Insights tümleştirmesini etkinleştirmeniz](#enable-application-insights-integration)gerekir. 

Application Insights, özel yapılandırma olmadan kullanabilirsiniz. Varsayılan yapılandırma, yüksek hacimde veri oluşmasına neden olabilir. Visual Studio Azure aboneliği kullanıyorsanız, Application Insights için veri üst sınırına ulaşırsınız. Application Insights maliyetler hakkında daha fazla bilgi edinmek için bkz. [Application Insights için kullanımı ve maliyetleri yönetme](../azure-monitor/app/pricing.md).

Bu makalenin ilerleyen kısımlarında, işlevlerinizin Application Insights gönderileceği verileri yapılandırmayı ve özelleştirmeyi öğreneceksiniz. Bir işlev uygulaması için günlüğe kaydetme, dosyadaki [host.js] yapılandırılır. 

> [!NOTE]
> Belirli bir ortam için dosyadaki bir host.jsbelirli ayarları temsil etmek üzere özel olarak yapılandırılmış uygulama ayarlarını kullanabilirsiniz. Bu, projenizdeki host.jsdosyayı yeniden yayınlamak zorunda kalmadan ayarları host.jsetkin bir şekilde değiştirmenize olanak sağlar. Daha fazla bilgi için bkz. [host.jsgeçersiz kılma değerleri](functions-host-json.md#override-hostjson-values).

## <a name="configure-categories"></a>Kategorileri yapılandırma

Azure Işlevleri günlükçüsü, her günlük için bir *Kategori* içerir. Kategori, çalışma zamanı kodunun veya işlev kodunuzun günlüğü yazanın hangi kısmının olduğunu gösterir. Kategoriler sürüm 1. x ve sonraki sürümler arasında farklılık gösterir. Aşağıdaki grafikte, çalışma zamanının oluşturduğu günlüklerin ana kategorileri açıklanmaktadır. 

# <a name="v2x"></a>[v2. x +](#tab/v2)

| Kategori | Tablo | Açıklama |
| ----- | ----- | ----- |
| **`Function.<YOUR_FUNCTION_NAME>`** | **bağlantılıdır**| Bağımlılık verileri bazı hizmetler için otomatik olarak toplanır. Başarılı çalıştırmalar için bu Günlükler `Information` düzeyindedir. Daha fazla bilgi için bkz. [Bağımlılıklar](functions-monitoring.md#dependencies). Özel durumlar, düzeyinde günlüğe kaydedilir `Error` . Çalışma zamanı ayrıca `Warning` , kuyruk iletilerinin [Zemi kuyruğuna](functions-bindings-storage-queue-trigger.md#poison-messages)gönderilmesi gibi düzey Günlükler de oluşturur. | 
| **`Function.<YOUR_FUNCTION_NAME>`** | **customMetrics**<br/>**customEvents** | C# ve JavaScript SDK 'Ları özel ölçümleri toplamanıza ve özel olayları günlüğe oluşturmanıza imkan tanır. Daha fazla bilgi için bkz. [özel telemetri verileri](functions-monitoring.md#custom-telemetry-data).|
| **`Function.<YOUR_FUNCTION_NAME>`** | **lerin**| İşlev başlatma ve belirli işlev çalıştırmaları için tamamlanan günlükleri içerir. Başarılı çalıştırmalar için bu Günlükler `Information` düzeyindedir. Özel durumlar, düzeyinde günlüğe kaydedilir `Error` . Çalışma zamanı ayrıca `Warning` , kuyruk iletilerinin [Zemi kuyruğuna](functions-bindings-storage-queue-trigger.md#poison-messages)gönderilmesi gibi düzey Günlükler de oluşturur. | 
| **`Function.<YOUR_FUNCTION_NAME>.User`** | **lerin**| Herhangi bir günlük düzeyi olabilen Kullanıcı tarafından oluşturulan Günlükler. İşlevlerinizin günlüklerine yazma hakkında daha fazla bilgi edinmek için bkz. [günlüklere yazma](functions-monitoring.md#writing-to-logs). | 
| **`Host.Aggregator`** | **customMetrics** | Çalışma zamanında oluşturulan bu Günlükler, [yapılandırılabilir](#configure-the-aggregator) bir süre boyunca işlev çağırma sayısının sayısını ve ortalamasını sağlar. Varsayılan süre 30 saniye veya 1.000 sonuçdur, hangisi önce gelir. Çalıştırma sayısı, başarı oranı ve süre örnekleri verilebilir. Tüm bu Günlükler `Information` düzeyinde yazılır. Veya üzerine filtre yaparsanız `Warning` , bu verilerden herhangi birini görmezsiniz. |
| **`Host.Results`** | **istekler** | Çalışma zamanında oluşturulan bu günlükler bir işlevin başarısını veya başarısızlığını gösterir. Tüm bu Günlükler `Information` düzeyinde yazılır. Veya üzerine filtre yaparsanız `Warning` , bu verilerden herhangi birini görmezsiniz. |
| **`Microsoft`** | **lerin** | Konak tarafından çağrılan bir .NET çalışma zamanı bileşenini yansıtan tam nitelikli günlük kategorisi.  |
| **`Worker`** | **lerin** | Non-.NET dilleri için dil çalışan işlemi tarafından oluşturulan Günlükler. Dil çalışanı günlükleri, gibi bir kategoride da kaydedilebilir `Microsoft.*` `Microsoft.Azure.WebJobs.Script.Workers.Rpc.RpcFunctionInvocationDispatcher` . Bu Günlükler `Information` düzeyinde yazılır.|

> [!NOTE]
> .NET sınıf kitaplığı işlevleri için bu kategoriler kullandığınız ve kullanmadığınız varsayılmaktadır `ILogger` `ILogger<T>` . Daha fazla bilgi edinmek için bkz. [Işlevler ILogger belgeleri](functions-dotnet-class-library.md#ilogger). 

# <a name="v1x"></a>[v1. x](#tab/v1)

| Kategori | Tablo | Açıklama |
| ----- | ----- | ----- |
| **`Function`** | **lerin**| Herhangi bir günlük düzeyi olabilen Kullanıcı tarafından oluşturulan Günlükler. İşlevlerinizin günlüklerine yazma hakkında daha fazla bilgi edinmek için bkz. [günlüklere yazma](functions-monitoring.md#writing-to-logs). | 
| **`Host.Aggregator`** | **customMetrics** | Çalışma zamanında oluşturulan bu Günlükler, [yapılandırılabilir](#configure-the-aggregator) bir süre boyunca işlev çağırma sayısının sayısını ve ortalamasını sağlar. Varsayılan süre 30 saniye veya 1.000 sonuçdur, hangisi önce gelir. Çalıştırma sayısı, başarı oranı ve süre örnekleri verilebilir. Tüm bu Günlükler `Information` düzeyinde yazılır. Veya üzerine filtre yaparsanız `Warning` , bu verilerden herhangi birini görmezsiniz. |
| **`Host.Executor`** | **lerin** | Belirli işlev çalıştırmaları için **başlatılan işlev** ve **işlev tamamlanan** günlükleri içerir. Başarılı çalıştırmalar için bu Günlükler `Information` düzeydir. Özel durumlar düzeyinde günlüğe kaydedilir `Error` . Çalışma zamanı ayrıca `Warning` , kuyruk iletilerinin [Zemi kuyruğuna](functions-bindings-storage-queue-trigger.md#poison-messages)gönderilmesi gibi düzey Günlükler de oluşturur.  |
| **`Host.Results`** | **istekler** | Çalışma zamanında oluşturulan bu günlükler bir işlevin başarısını veya başarısızlığını gösterir. Tüm bu Günlükler `Information` düzeyinde yazılır. Veya üzerine filtre yaparsanız `Warning` , bu verilerden herhangi birini görmezsiniz. |

---

**Tablo** sütunu, günlük Application Insights hangi tablonun yazıldığını gösterir. 

## <a name="configure-log-levels"></a>Günlük düzeylerini yapılandırma

[!INCLUDE [functions-log-levels](../../includes/functions-log-levels.md)]

Her kategori için, gönderileceği en düşük günlük düzeyini belirtirsiniz. Ayarlarınızdaki host.js, [işlevler çalışma zamanı sürümüne](functions-versions.md)bağlı olarak değişir. 

Aşağıdaki örnek, günlüğü aşağıdaki kurallara göre tanımlar:

+ Veya günlükleri için `Host.Results` `Function` yalnızca `Error` veya daha yüksek düzeydeki olayları günlüğe kaydedin. 
+ Günlükleri için `Host.Aggregator` oluşturulan tüm ölçümleri () günlüğe kaydedin `Trace` .
+ Kullanıcı günlükleri de dahil olmak üzere tüm diğer Günlükler için, yalnızca `Information` üst düzey ve daha yüksek olayları günlüğe kaydedin.

# <a name="v2x"></a>[v2. x +](#tab/v2)

```json
{
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "default": "Information",
      "Host.Results": "Error",
      "Function": "Error",
      "Host.Aggregator": "Trace"
    }
  }
}
```

# <a name="v1x"></a>[v1. x](#tab/v1) 

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host.Results": "Error",
        "Function": "Error",
        "Host.Aggregator": "Trace"
      }
    }
  }
}
```

---

[host.json] , aynı dizeyle başlayan birden çok günlük içeriyorsa, önce daha fazla tanımlı Günlükler eşleştirilir. Çalışma zamanında her şeyi, şu düzeyde günlüğe kaydeden aşağıdaki örneği göz önünde bulundurun `Host.Aggregator` `Error` :

# <a name="v2x"></a>[v2. x +](#tab/v2)

```json
{
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "default": "Information",
      "Host": "Error",
      "Function": "Error",
      "Host.Aggregator": "Information"
    }
  }
}
```

# <a name="v1x"></a>[v1. x](#tab/v1) 

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host": "Error",
        "Function": "Error",
        "Host.Aggregator": "Information"
      }
    }
  }
}
```

---

`None`Bir kategori için herhangi bir günlüğün yazılmasını engellemek için bir günlük düzeyi ayarı kullanabilirsiniz. 

## <a name="configure-the-aggregator"></a>Toplayıcısı yapılandırma

Önceki bölümde belirtildiği gibi, çalışma zamanı bir süre boyunca işlev yürütmeleri hakkındaki verileri toplar. Varsayılan süre 30 saniye veya 1.000 çalışma olur ve hangisi önce gelir. Bu ayarı, [host.jsdosya üzerinde] yapılandırabilirsiniz.  Aşağıda bir örnek verilmiştir:

```json
{
    "aggregator": {
      "batchSize": 1000,
      "flushTimeout": "00:00:30"
    }
}
```

## <a name="configure-sampling"></a>Örnekleme yapılandırma

Application Insights, yoğun yük saatlerinde tamamlanan yürütmeler üzerinde çok fazla telemetri verisi üretmenin bir [örnekleme](../azure-monitor/app/sampling.md) özelliğine sahiptir. Gelen yürütmeler oranı belirtilen eşiği aştığında Application Insights, gelen yürütmelerin bazılarını rastgele yok saymaya başlar. Saniyedeki en fazla yürütme sayısı için varsayılan ayar 20 ' dir (sürüm 1. x içinde beş). [Üzerindehost.js](./functions-host-json.md#applicationinsights)örnekleme yapılandırabilirsiniz.  Aşağıda bir örnek verilmiştir:

# <a name="v2x"></a>[v2. x +](#tab/v2)

```json
{
  "logging": {
    "applicationInsights": {
      "samplingSettings": {
        "isEnabled": true,
        "maxTelemetryItemsPerSecond" : 20,
        "excludedTypes": "Request;Exception"
      }
    }
  }
}
```


Belirli telemetri türlerini örnekleme dışında bırakabilirsiniz. Bu örnekte, ve türündeki veriler `Request` örnekleme dışında `Exception` tutulur. Bu, diğer telemetri türleri örneklemeye tabi kaldığı sürece *Tüm* işlev yürütmelerinin (istekler) ve özel durumların günlüğe kaydedilmesini sağlar. 

# <a name="v1x"></a>[v1. x](#tab/v1)  

```json
{
  "applicationInsights": {
    "sampling": {
      "isEnabled": true,
      "maxTelemetryItemsPerSecond" : 5
    }
  }
}
```
---

Daha fazla bilgi için bkz. [örnekleme Application Insights](../azure-monitor/app/sampling.md).

## <a name="configure-scale-controller-logs"></a>Ölçek denetleyicisi günlüklerini yapılandırma

_Bu özellik önizleme aşamasındadır._ 

Ölçek denetleyicisinin işlev uygulamanızda yapmakta olduğu kararları daha iyi anlamak için, [Azure işlevleri](./event-driven-scaling.md#runtime-scaling) 'nin, ölçek denetleyicisinin günlükleri Application Insights veya blob depolama alanına almasını sağlayabilirsiniz.

Bu özelliği etkinleştirmek için, işlev uygulaması ayarlarınıza adlı bir uygulama ayarı eklersiniz `SCALE_CONTROLLER_LOGGING_ENABLED` . Bu ayarın değeri `<DESTINATION>:<VERBOSITY>` , aşağıdakilere bağlı olarak şu biçimde olmalıdır:

[!INCLUDE [functions-scale-controller-logging](../../includes/functions-scale-controller-logging.md)]

Örneğin, aşağıdaki Azure CLı komutu, ölçek denetleyicisinden Application Insights için ayrıntılı günlük kaydını etkinleştirir:

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings SCALE_CONTROLLER_LOGGING_ENABLED=AppInsights:Verbose
```

Bu örnekte, ve ' `<FUNCTION_APP_NAME>` `<RESOURCE_GROUP_NAME>` yi sırasıyla işlev uygulamanızın adı ve kaynak grubu adı ile değiştirin. 

Aşağıdaki Azure CLı komutu, ayrıntı düzeyini şu şekilde ayarlayarak günlüğe kaydetmeyi devre dışı bırakır `None` :

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings SCALE_CONTROLLER_LOGGING_ENABLED=AppInsights:None
```

`SCALE_CONTROLLER_LOGGING_ENABLED`Aşağıdaki Azure CLI komutunu kullanarak ayarı kaldırarak günlüğü de devre dışı bırakabilirsiniz:

```azurecli-interactive
az functionapp config appsettings delete --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--setting-names SCALE_CONTROLLER_LOGGING_ENABLED
```

Ölçek denetleyicisi günlüğü etkinken, artık [Ölçek denetleyicisi günlüklerinizi sorgulayabilirsiniz](analyze-telemetry-data.md#query-scale-controller-logs). 

## <a name="enable-application-insights-integration"></a>Application Insights tümleştirmesini etkinleştirme

Bir işlev uygulamasının Application Insights verileri gönderebilmesi için, bir Application Insights kaynağının izleme anahtarını bilmeleri gerekir. Anahtar, **APPINSIGHTS_INSTRUMENTATIONKEY** adlı bir uygulama ayarında olmalıdır.

İşlev uygulamanızı [Azure Portal](./functions-get-started.md)oluşturduğunuzda, komut satırından [Azure Functions Core Tools](./create-first-function-cli-csharp.md)veya [Visual Studio Code](./create-first-function-vs-code-csharp.md)kullanarak Application Insights tümleştirme varsayılan olarak etkinleştirilir. Application Insights kaynak, işlev uygulamanızla aynı ada sahiptir ve aynı bölgede ya da en yakın bölgede oluşturulur.

### <a name="new-function-app-in-the-portal"></a>Portalda yeni işlev uygulaması

Oluşturulan Application Insights kaynağını gözden geçirmek için, **Application Insights** penceresini genişletmek üzere seçin. **Yeni kaynak adını** değiştirebilir veya verilerinizi depolamak istediğiniz [Azure Coğrafya](https://azure.microsoft.com/global-infrastructure/geographies/) 'da farklı bir **konum** seçebilirsiniz.

![İşlev uygulaması oluştururken Application Insights etkinleştirme](media/functions-monitoring/enable-ai-new-function-app.png)

**Oluştur**' u seçtiğinizde, işlev uygulamanız ile uygulama ayarlarında ayarlanmış olan bir Application Insights kaynak oluşturulur `APPINSIGHTS_INSTRUMENTATIONKEY` . Her şey başlamaya hazırlanıyor.

<a id="manually-connect-an-app-insights-resource"></a>
### <a name="add-to-an-existing-function-app"></a>Mevcut bir işlev uygulamasına ekleme 

İşlev uygulamanızla bir Application Insights kaynağı oluşturulmadıysa, kaynağı oluşturmak için aşağıdaki adımları kullanın. Daha sonra bu kaynaktaki izleme anahtarını, işlev uygulamanızda bir [uygulama ayarı](functions-how-to-use-azure-function-app-settings.md#settings) olarak ekleyebilirsiniz.

1. [Azure Portal](https://portal.azure.com), **işlev uygulamasını** arayıp seçin, sonra işlev uygulamanızı seçin. 

1. Pencerenin üst kısmında **Application Insights yapılandırılmamış** başlık ' ı seçin. Bu başlığı görmüyorsanız, uygulamanız zaten Application Insights etkinleştirilmiş olabilir.

    :::image type="content" source="media/configure-monitoring/enable-application-insights.png" alt-text="Portaldan Application Insights etkinleştirme":::

1. Aşağıdaki tabloda belirtilen ayarları kullanarak **kaynağınızın değiştirme** ve bir Application Insights kaynağı oluşturma ' yı genişletin.  

    | Ayar      | Önerilen değer  | Açıklama                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Yeni kaynak adı** | Benzersiz uygulama adı | Kendi aboneliğinizde benzersiz olması gereken işlev uygulamanız ile aynı adı kullanmak en kolay yoldur. | 
    | **Konum** | West Europe | Mümkünse, işlev uygulamanız ile aynı [bölgeyi](https://azure.microsoft.com/regions/) veya bu bölgeye yakın olanı kullanın. |

    :::image type="content" source="media/configure-monitoring/ai-general.png" alt-text="Application Insights kaynağı oluşturma":::

1. **Uygula**’yı seçin. 

   Application Insights kaynak, işlev uygulamanız ile aynı kaynak grubunda ve abonelikte oluşturulur. Kaynak oluşturulduktan sonra Application Insights penceresini kapatın.

1. İşlev uygulamanızda **Ayarlar**' ın altında **yapılandırma** ' yı seçin ve ardından **uygulama ayarları**' nı seçin. Adlı bir ayar görürseniz `APPINSIGHTS_INSTRUMENTATIONKEY` , Azure 'da çalışan işlev uygulamanız için Application Insights tümleştirme etkinleştirilir. Bu ayar bazı nedenlerle mevcut değilse, Application Insights izleme anahtarınızı kullanarak bu değeri ekleyin.

> [!NOTE]
> Işlevlerin erken sürümleri, artık önerilmeyen yerleşik izleme kullanır. Böyle bir işlev uygulaması için Application Insights tümleştirmesinin etkinleştirilmesinde, [yerleşik günlüğü de devre dışı bırakmanız](#disable-built-in-logging)gerekir.  

## <a name="disable-built-in-logging"></a>Yerleşik günlüğe kaydetmeyi devre dışı bırakma

Application Insights etkinleştirdiğinizde, Azure Storage kullanan yerleşik günlüğe kaydetmeyi devre dışı bırakın. Yerleşik günlük kaydı, hafif iş yükleri ile test etmek için kullanışlıdır, ancak yüksek yük üretim kullanımı için tasarlanmamıştır. Üretim izleme için Application Insights önerilir. Üretim ortamında yerleşik günlük kullanılıyorsa, Azure Storage üzerinde azaltma nedeniyle günlüğe kaydetme kaydı tamamlanmamış olabilir.

Yerleşik günlüğe kaydetmeyi devre dışı bırakmak için `AzureWebJobsDashboard` uygulama ayarını silin. Azure portal uygulama ayarlarının nasıl silineceği hakkında daha fazla bilgi için, [bir işlev uygulamasını yönetme](functions-how-to-use-azure-function-app-settings.md#settings)konusunun **uygulama ayarları** bölümüne bakın. Uygulama ayarını silmeden önce, aynı işlev uygulamasındaki mevcut bir işlev olmadığından emin olun, Azure depolama Tetikleyicileri veya bağlamaları için ayarı kullanın.

## <a name="next-steps"></a>Sonraki adımlar

İzleme hakkında daha fazla bilgi için bkz.

+ [Azure İşlevlerini İzleme](functions-monitoring.md)
+ [Application Insights Azure Işlevleri telemetri verilerini analiz etme](analyze-telemetry-data.md)
+ [Uygulama Bilgileri](/azure/application-insights/)


[ Üzerindehost.js]: functions-host-json.md
