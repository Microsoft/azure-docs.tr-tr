---
title: Dayanıklı İşlevler için sıfır kesinti süresi dağıtımı
description: Sıfır kesinti süresi için Dayanıklı İşlevler düzenleme özelliğini nasıl etkinleştireceğinizi öğrenin.
services: functions
author: tsushi
manager: gwallace
ms.service: azure-functions
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: azfuncdf
ms.openlocfilehash: af19f8cdcc26d1459bc024f00b963f04bd8d763b
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904053"
---
# <a name="zero-downtime-deployment-for-durable-functions"></a>Dayanıklı İşlevler için sıfır kesinti süresi dağıtımı

Dayanıklı İşlevler [güvenilir yürütme modeli](durable-functions-checkpointing-and-replay.md) , güncelleştirmeleri dağıtırken göz önünde bulundurmanız gereken ek bir sınama oluşturan, düzenleme belirleyici olmasını gerektirir. Bir dağıtım, etkinlik işlevi imzalarında veya Orchestrator mantığındaki değişiklikler içerdiğinde uçuş düzenleme örnekleri başarısız olur. Bu durum özellikle uzun süre çalışan düzenleyiciler örneklerine yönelik bir sorundur ve bu da saatleri veya iş günlerini temsil edebilir.

Bu hataların oluşmasını engellemek için iki seçeneğiniz vardır: 
- Çalışan tüm düzenleme örnekleri tamamlanana kadar dağıtımınızı geciktirebilirsiniz.
- Çalışan tüm düzenleme örneklerinin işlevlerinizin mevcut sürümlerini kullanmasını sağlayın. 

> [!NOTE]
> Bu makalede, Dayanıklı İşlevler 1. x ' i hedefleyen işlevler uygulamalarına yönelik rehberlik sunulmaktadır. Dayanıklı İşlevler 2. x içinde tanıtılan değişiklikler için hesaba güncelleştirilmedi. Uzantı sürümleri arasındaki farklar hakkında daha fazla bilgi için bkz. [dayanıklı işlevler sürümleri](durable-functions-versions.md).

Aşağıdaki grafik, Dayanıklı İşlevler için sıfır kesinti temelli bir dağıtım elde etmek üzere üç ana stratejileri karşılaştırır: 

| Strateji |  Kullanılması gereken durumlar | Ları | Simgeler |
| -------- | ------------ | ---- | ---- |
| [Sürümü](#versioning) |  Sık karşılaşılan değişiklikler hakkında daha fazla karşılaşmeyen uygulamalar [.](durable-functions-versioning.md) | Basit uygulama. |  Bellekte ve işlev sayısında daha fazla işlev uygulaması boyutu.<br/>Kod çoğaltma. |
| [Yuva ile durum denetimi](#status-check-with-slot) | 24 veya daha fazla çakışan düzenleme için uzun süre çalışan bir düzenleme gerçekleştirmeyen bir sistem. | Basit kod tabanı.<br/>Ek işlev uygulama yönetimi gerektirmez. | Ek depolama hesabı veya görev merkezi yönetimi gerektirir.<br/>Hiçbir düzenleme çalışmadığı zaman dönem gerektirir. |
| [Uygulama yönlendirme](#application-routing) | En son 24 saatten uzun veya sık sık çakışan düzenleyicilerle bu dönemler gibi, düzenleme çalışmadığı zaman süreleri olmayan bir sistem. | Sürekli değişiklikler içeren düzenlemeleri çalıştıran sistemlerin yeni sürümlerini işler. | Akıllı uygulama yönlendiricisi gerektirir.<br/>Aboneliğiniz tarafından izin verilen işlev uygulamalarının sayısı en fazla olabilir. Varsayılan değer 100 ' dir. |

## <a name="versioning"></a>Sürüm oluşturma

İşlevlerinizin yeni sürümlerini tanımlayın ve işlev uygulamanızda eski sürümleri bırakın. Diyagramda görebileceğiniz gibi, bir işlevin sürümü adının bir parçası haline gelir. İşlevlerin önceki sürümleri korunduğundan, uçuş sırasında düzenleme örnekleri bunlara başvurmasına devam edebilir. Bu sırada, Orchestration Client işlevinizin bir uygulama ayarından başvurmasına yönelik yeni düzenleme örnekleri istekleri en son sürüm için çağrı yapılır.

![Sürüm oluşturma stratejisi](media/durable-functions-zero-downtime-deployment/versioning-strategy.png)

Bu stratejide, her işlev kopyalanmalıdır ve diğer işlevlere olan başvuruların güncellenmesi gerekir. Bir komut dosyası yazarak daha kolay hale getirebilirsiniz. Geçiş betiği içeren [örnek bir proje](https://github.com/TsuyoshiUshio/DurableVersioning) aşağıda verilmiştir.

>[!NOTE]
>Bu strateji dağıtım sırasında kapalı kalma süresini önlemek için dağıtım yuvalarını kullanır. Yeni dağıtım yuvaları oluşturma ve kullanma hakkında daha ayrıntılı bilgi için bkz. [Azure işlevleri dağıtım yuvaları](../functions-deployment-slots.md).

## <a name="status-check-with-slot"></a>Yuva ile durum denetimi

İşlev uygulamanızın geçerli sürümü üretim yuvasıyla çalışırken, işlev uygulamanızın yeni sürümünü hazırlama yuvasıyla dağıtın. Üretim ve hazırlama yuvalarınızı değiştirmeden önce, çalışan herhangi bir düzenleme örneği olup olmadığını kontrol edin. Tüm düzenleme örnekleri tamamlandıktan sonra, değiştirmeyi yapabilirsiniz. Bu strateji, hiçbir düzenleme örneği kol olmadığında tahmin edilebilir Dönemleriniz olduğunda işe yarar. Bu, düzenleyiclarınız uzun süre çalışmadığı ve düzenleme yürütmeleri sıklıkla çakışmazsa en iyi yaklaşımdır.

### <a name="function-app-configuration"></a>İşlev uygulaması yapılandırması

Bu senaryoyu ayarlamak için aşağıdaki yordamı kullanın.

1. Hazırlama ve üretim için işlev uygulamanıza [dağıtım yuvaları ekleyin](../functions-deployment-slots.md#add-a-slot) .

1. Her yuva için, [AzureWebJobsStorage uygulama ayarını](../functions-app-settings.md#azurewebjobsstorage) paylaşılan bir depolama hesabının bağlantı dizesine ayarlayın. Bu depolama hesabı bağlantı dizesi, Azure Işlevleri çalışma zamanı tarafından kullanılır. Bu hesap, Azure Işlevleri çalışma zamanı tarafından kullanılır ve işlevin anahtarlarını yönetir.

1. Her yuva için yeni bir uygulama ayarı oluşturun, örneğin `DurableManagementStorage`. Değerini farklı depolama hesaplarının bağlantı dizesine ayarlayın. Bu depolama hesapları, [güvenilir yürütme](durable-functions-checkpointing-and-replay.md)için dayanıklı işlevler uzantısı tarafından kullanılır. Her yuva için ayrı bir depolama hesabı kullanın. Bu ayarı bir dağıtım yuvası ayarı olarak işaretlemeyin.

1. İşlev uygulamanızın [Host. json dosyasının durableTask bölümünde](durable-functions-bindings.md#hostjson-settings), adım 3 ' te oluşturduğunuz uygulama ayarının adı olarak `azureStorageConnectionStringName` belirtin.

Aşağıdaki diyagramda, dağıtım yuvaları ve depolama hesaplarının açıklanan yapılandırması gösterilmektedir. Bu potansiyel dağıtım senaryosunda, bir işlev uygulamasının 2. sürümü üretim yuvasında çalışmaktadır, 1. sürüm hazırlama yuvasında kalır.

![Dağıtım yuvaları ve depolama hesapları](media/durable-functions-zero-downtime-deployment/deployment-slot.png)

### <a name="hostjson-examples"></a>Host. JSON örnekleri

Aşağıdaki JSON parçaları, *Host. JSON* dosyasındaki bağlantı dizesi ayarına örnektir.

#### <a name="functions-20"></a>İşlevler 2,0

```json
{
  "version": 2.0,
  "extensions": {
    "durableTask": {
      "azureStorageConnectionStringName": "DurableManagementStorage"
    }
  }
}
```

#### <a name="functions-1x"></a>İşlevler 1.x

```json
{
  "durableTask": {
    "azureStorageConnectionStringName": "DurableManagementStorage"
  }
}
```

### <a name="cicd-pipeline-configuration"></a>CI/CD işlem hattı yapılandırması

CI/CD işlem hattınızı yalnızca işlev uygulamanızda bekleyen veya çalışan düzenleme örnekleri olmadığında dağıtılacak şekilde yapılandırın. Azure Pipelines kullanırken, aşağıdaki örnekte olduğu gibi, bu koşulları denetleyen bir işlev oluşturabilirsiniz:

```csharp
[FunctionName("StatusCheck")]
public static async Task<IActionResult> StatusCheck(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post")] HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger log)
{
    var runtimeStatus = new List<OrchestrationRuntimeStatus>();

    runtimeStatus.Add(OrchestrationRuntimeStatus.Pending);
    runtimeStatus.Add(OrchestrationRuntimeStatus.Running);

    var status = await client.GetStatusAsync(new DateTime(2015,10,10), null, runtimeStatus);
    return (ActionResult) new OkObjectResult(new Status() {HasRunning = (status.Count != 0)});
}
```

Ardından, hazırlama geçidini, hiçbir düzenleme çalışmadığı sürece bekleyecek şekilde yapılandırın. Daha fazla bilgi için bkz. [kapıları kullanarak yayın dağıtım denetimi](/azure/devops/pipelines/release/approvals/gates?view=azure-devops)

![Dağıtım kapısı](media/durable-functions-zero-downtime-deployment/deployment-gate.png)

Azure Pipelines, dağıtımınız başlamadan önce düzenleme örneklerini çalıştırmaya yönelik işlev uygulamanızı denetler.

![Dağıtım Kapısı (çalışıyor)](media/durable-functions-zero-downtime-deployment/deployment-gate-2.png)

Artık işlev uygulamanızın yeni sürümü hazırlama yuvasına dağıtılmalıdır.

![Hazırlama yuvası](media/durable-functions-zero-downtime-deployment/deployment-slot-2.png)

Son olarak, takas yuvaları. 

Dağıtım yuvası ayarları olarak işaretlenmemiş uygulama ayarları da takas edilir, bu nedenle sürüm 2 uygulaması, depolama hesabına A başvurusunu tutar. Düzenleme durumu depolama hesabında izlendiğinden, sürüm 2 uygulamasında çalışan her türlü düzenleme kesintiye uğramadan yeni yuvada çalışmaya devam eder.

![Dağıtım yuvası](media/durable-functions-zero-downtime-deployment/deployment-slot-3.png)

Her iki yuva için aynı depolama hesabını kullanmak için, görev hub 'larınızın adlarını değiştirebilirsiniz. Bu durumda, yuvalarınızın durumunu ve uygulamanızın HubName ayarlarını yönetmeniz gerekir. Daha fazla bilgi için bkz. [dayanıklı işlevler görev hub 'ları](durable-functions-task-hubs.md).

## <a name="application-routing"></a>Uygulama yönlendirme

Bu strateji en karmaşıktır. Ancak, çalışma düzenlemeleri arasında zaman olmayan işlev uygulamaları için kullanılabilir.

Bu strateji için Dayanıklı İşlevler önünde bir *uygulama yönlendirici* oluşturmanız gerekir. Bu yönlendirici Dayanıklı İşlevler ile uygulanabilir. Yönlendirici şu şekilde sorumluluğa sahiptir:

* İşlev uygulamasını dağıtın.
* Dayanıklı İşlevler sürümünü yönetin. 
* Düzenleme isteklerini işlev uygulamalarına yönlendirin.

Bir Orchestration isteği ilk kez alındığında yönlendirici aşağıdaki görevleri yapar:

1. Azure 'da yeni bir işlev uygulaması oluşturur.
2. İşlev uygulamanızın kodunu Azure 'daki yeni işlev uygulamasına dağıtır.
3. Düzenleme isteğini yeni uygulamaya iletir.

Yönlendirici, uygulama kodunuzun hangi sürümünün Azure 'daki işlev uygulamasına dağıtıldığını yönetir.

![Uygulama yönlendirme (ilk kez)](media/durable-functions-zero-downtime-deployment/application-routing.png)

Yönlendirici dağıtım ve düzenleme isteklerini istekle birlikte gönderilen sürüme bağlı olarak uygun işlev uygulamasına yönlendirir. Düzeltme Eki sürümünü yoksayar.

Uygulamanızın yeni bir sürümünü bir değişiklik yapmadan dağıttığınızda, düzeltme eki sürümünü artırabilirsiniz. Yönlendirici mevcut işlev uygulamanıza dağıtılır ve aynı işlev uygulamasına yönlendirilen kodun eski ve yeni sürümlerine yönelik istekler gönderir.

![Uygulama yönlendirme (hiçbir bölme değişikliği yok)](media/durable-functions-zero-downtime-deployment/application-routing-2.png)

Uygulamanızın yeni bir sürümünü Son değişiklik ile dağıttığınızda, büyük veya küçük sürümü artırabilirsiniz. Ardından, uygulama yönlendiricisi Azure 'da yeni bir işlev uygulaması oluşturur, buna dağıtır ve uygulamanızın yeni sürümü için istekleri yönlendirir. Aşağıdaki diyagramda uygulamanın 1.0.1 sürümünde düzenlemeler çalıştırılıyor, ancak 1.1.0 sürümü istekleri yeni işlev uygulamasına yönlendirilir.

![Uygulama yönlendirme (Son değişiklik)](media/durable-functions-zero-downtime-deployment/application-routing-3.png)

Yönlendirici 1.0.1 sürümündeki düzenlemeleri izler ve tüm düzenlemeler bittikten sonra uygulamaları kaldırır. 

### <a name="tracking-store-settings"></a>İzleme deposu ayarları

Her işlev uygulaması, muhtemelen ayrı depolama hesaplarında ayrı zamanlama kuyrukları kullanmalıdır. Uygulamanızın tüm sürümlerindeki tüm organize edilecek örnekleri sorgulamak istiyorsanız, işlev uygulamalarınız genelinde örnek ve geçmiş tabloları paylaşabilirsiniz. Her tümünün aynı değerleri kullanabilmesi için [Host. JSON ayarları](durable-functions-bindings.md#host-json) dosyasındaki `trackingStoreConnectionStringName` ve `trackingStoreNamePrefix` ayarlarını yapılandırarak tabloları paylaşabilirsiniz.

Daha fazla bilgi için bkz. [Azure 'da dayanıklı işlevler örnekleri yönetme](durable-functions-instance-management.md).

![İzleme deposu ayarları](media/durable-functions-zero-downtime-deployment/tracking-store-settings.png)

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Sürüm oluşturma Dayanıklı İşlevler](durable-functions-versioning.md)

