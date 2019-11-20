---
title: Ölçümler, uyarılar ve tanılama günlükleri-Azure Batch | Microsoft Docs
description: Kayıt ve tanılama günlüğü olaylarını havuzlar ve görevler gibi Azure Batch hesabı kaynakları için analiz edin.
services: batch
documentationcenter: ''
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: big-compute
ms.date: 12/05/2018
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: aa86d6cf22562fa1fac7d45de20b28aa0eec33aa
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71261669"
---
# <a name="batch-metrics-alerts-and-logs-for-diagnostic-evaluation-and-monitoring"></a>Toplu ölçümleri, uyarılar ve değerlendirme tanılama ve izleme günlükleri

 
Bu makalede, bir Batch hesabı özelliklerini kullanarak izlemek açıklanmaktadır [Azure İzleyici](../azure-monitor/overview.md). Azure İzleyici toplar [ölçümleri](../azure-monitor/platform/data-platform-metrics.md) ve [tanılama günlükleri](../azure-monitor/platform/resource-logs-overview.md) Batch hesabınızdaki kaynaklara için. Toplama ve Batch hesabınızı izleme ve sorunlarını tanılamak için yol çeşitli bu verileri kullanır. Ayrıca [ölçüm uyarıları](../azure-monitor/platform/alerts-overview.md) bir ölçüm belirli bir değere ulaştığında bildirimleri almak için. 

## <a name="batch-metrics"></a>Toplu ölçümleri

(Performans sayaçları olarak da bilinir) Azure telemetri verilerini Azure İzleyici hizmeti tarafından kullanılan, Azure kaynaklarınızı tarafından yayılan ölçümleridir. Batch hesabındaki örnek ölçümler şunları içerir: Havuz oluşturma olayları, düşük öncelikli düğüm sayısı ve görev tamamlanma olayları. 

Bkz: [Batch desteklenen ölçümlerin listesi](../azure-monitor/platform/metrics-supported.md#microsoftbatchbatchaccounts).

Ölçümler şunlardır:

* Her Batch hesabında bir ek yapılandırma olmadan varsayılan olarak etkin
* Her 1 dakikada oluşturulan
* Otomatik olarak kalıcı değil, ancak 30 günlük çalışırken geçmişi bulunur. Tanılama günlüğü 'nün bir parçası olarak etkinlik ölçümlerini kalıcı hale getirebilirsiniz.

### <a name="view-metrics"></a>Ölçümleri görüntüle

Azure portalında Batch hesabı için ölçümleri görüntüleyin. **Genel bakış** sayfası varsayılan hesap anahtarı düğüm, Çekirdeği ve görev ölçümleri gösterir. 

Tüm Batch hesabı ölçümleri görüntülemek için: 

1. Portalında **tüm hizmetleri** > **Batch hesapları**ve ardından Batch hesabınızın adına tıklayın.
2. Altında **izleme**, tıklayın **ölçümleri**.
3. Bir veya daha fazla ölçüm seçin. İsterseniz, ek kaynak ölçümleri kullanarak seçme **abonelikleri**, **kaynak grubu**, **kaynak türü**, ve **kaynak** Açılır listeler.
    * Count tabanlı ölçümler ("adanmış çekirdek sayısı" veya "düşük öncelikli düğüm sayısı" gibi) için "Ortalama" toplamayı kullanın. Olay tabanlı ölçümler ("havuz yeniden boyutlandırma tam olayları" gibi) için "say" toplamasını kullanın.

    ![Toplu ölçümleri](media/batch-diagnostics/metrics-portal.png)

Program aracılığıyla ölçümleri almak için Azure İzleyici API'lerini kullanın. Örneğin, [almak Azure İzleyici ölçümleri .NET ile](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/).

## <a name="batch-metric-reliability"></a>Batch ölçüm güvenilirlik

Ölçümler, eğilimleri belirlemek ve veri analizi için kullanılacak yöneliktir. Ölçüm teslimi garanti edilmez ve sırası teslim, veri kaybı ve/veya çoğaltma tabidir. Uyarı veya tetikleyici işlevleri tek olayları kullanılması önerilmez. Bkz: [toplu ölçüm uyarıları](#batch-metric-alerts) eşikler için uyarı ayarlama konusunda ayrıntılı bilgi için.

Son 3 dakika içinde gösterilen ölçümleri hala toplama. Bu zaman çerçevesi sırasındaki ölçüm değerleri eksik.

## <a name="batch-metric-alerts"></a>Batch ölçüm uyarıları

İsteğe bağlı olarak, neredeyse gerçek zamanlı yapılandırma *ölçüm uyarıları* belirtilen ölçüm değerini atadığınız bir eşiği geçtiğinde tetikleyin. Bir uyarı oluşturur bir [bildirim](../monitoring-and-diagnostics/insights-alerts-portal.md) uyarı "(eşiği aşıldığında ve uyarı koşulu karşılandığında olduğunda) etkinleştirildiğinde" yanı sıra, seçtiğiniz "Çözülene" (zaman eşiği yeniden çapraz ve koşul yok artık yerine). Ölçümleri sırası teslim, veri kaybı ve/veya çoğaltma tabi olarak tek bir veri noktasının temel uyarı önerilmez. Uyarı olmalısınız eşiklerini bu tutarsızlıkları hesap için kullanın.

Örneğin, düşük öncelikli çekirdek sayısı, belirli bir düzeyde, düştüğünde havuzları, oluşumunu ayarlayabilmeniz için ölçüm uyarısı yapılandırmak isteyebilirsiniz. Burada, ortalama düşük öncelikli çekirdek sayısı tüm dönem için Eşik değerin altına düşerse uyarı tetikleyen bir süre en az 10 dakika ayarlamak için önerilir. 1-5 dakikalık bir süre üzerinde ölçümleri hala toplama gibi uyarmak için önerilmez.

Ölçüm uyarısı portalında yapılandırmak için:

1. **Tüm hizmetler** > **Batch hesapları** seçeneğine ve sonra da Batch hesabınızın adına tıklayın.
2. Altında **izleme**, tıklayın **uyarı kuralları** > **ölçüm uyarısı Ekle**.
3. Bir ölçüm, bir uyarı durumu (örneğin, bir ölçüm belirli bir değerin bir süre boyunca aştığında) ve bir veya daha fazla bildirim seçin.

Neredeyse gerçek zamanlı uyarı kullanarak bir yapılandırabilirsiniz [REST API](https://docs.microsoft.com/rest/api/monitor/). Daha fazla bilgi için [uyarılara genel bakış](../azure-monitor/platform/alerts-overview.md)

## <a name="batch-diagnostics"></a>Batch tanılama

Tanılama günlükleri, işlemi her bir kaynağın açıklayan Azure kaynakları tarafından gösterilen bilgileri içerir. Batch için aşağıdaki günlüklere toplayabilirsiniz:

* **Hizmet günlüklerini** olayları tek tek bir Batch kaynak yaşam süresi boyunca Azure Batch hizmeti tarafından yayılan bir havuzu veya göreviyle ister. 

* **Ölçümleri** hesap düzeyinde günlükleri. 

Tanılama günlüklerinin toplanmasını etkinleştirmek için ayarlar varsayılan olarak etkin değildir. Açıkça izlemek istediğiniz her bir Batch hesabı tanılama ayarlarını etkinleştirme.

### <a name="log-destinations"></a>Günlük hedefleri

Günlük hedefi bir Azure depolama hesabını seçin yaygın bir senaryodur. Azure Depolama'da günlüklerini depolamak için günlüklerin toplanmasını etkinleştirmeden önce hesabı oluşturun. Batch hesabınızla bir depolama hesabı ilişkilendirirseniz günlük hedefi bu hesabı seçebilirsiniz. 

Tanılama günlükleri için isteğe bağlı diğer hedefleri:

* Batch tanılama günlüğü olaylarını Stream bir [Azure olay hub'ı](../event-hubs/event-hubs-what-is-event-hubs.md). Event hubs'ı, sonra dönüştürebilir ve herhangi bir gerçek zamanlı analiz sağlayıcısı kullanarak depolama, saniye başına milyonlarca olayı içe alabilir. 

* Tanılama günlüklerini [Azure izleyici günlüklerine](../log-analytics/log-analytics-overview.md)göndererek bunları analiz edebilir veya Power BI veya Excel 'de Analize aktarabilirsiniz.

> [!NOTE]
> Depolama veya Azure hizmetleriyle tanılama günlük verilerini işlemek için ek ücrete neden. 
>

### <a name="enable-collection-of-batch-diagnostic-logs"></a>Batch tanılama günlükleri koleksiyonunu etkinleştir

1. Portalında **tüm hizmetleri** > **Batch hesapları**ve ardından Batch hesabınızın adına tıklayın.
2. Altında **izleme**, tıklayın **tanılama günlükleri** > **tanılamayı Aç**.
3. **Tanılama ayarları**' nda, ayar için bir ad girin ve bir günlük hedefi (mevcut depolama hesabı, Olay Hub 'ı veya Azure izleyici günlükleri) seçin. Veya ikisini birden seçin **ServiceLog** ve **AllMetrics**.

    İsteğe bağlı olarak bir depolama hesabını seçtiğinizde bir bekletme ilkesi ayarlayın. Veriler, birkaç gün bekletme için belirtmezseniz, depolama hesabının ömrü tutulur.

4. **Kaydet**’e tıklayın.

    ![Batch tanılama](media/batch-diagnostics/diagnostics-portal.png)

Günlük toplamayı etkinleştirmek için diğer seçenekler şunlardır: portalda Azure İzleyicisi'ni kullanın tanılama ayarları yapılandırmak, kullanmak için bir [Resource Manager şablonu](../azure-monitor/platform/diagnostic-settings-template.md), veya Azure PowerShell veya Azure CLI'yı kullanın. bkz: [toplamak ve Azure kaynaklarınızdan günlük verilerini kullanma](../azure-monitor/platform/resource-logs-overview.md).


### <a name="access-diagnostics-logs-in-storage"></a>Depolama erişim tanılama günlükleri

Batch tanılama günlükleri bir depolama hesabında arşivleyin, ilgili olay oluşur oluşmaz depolama hesabında bir depolama kapsayıcısı oluşturulur. Bloblar, aşağıdaki adlandırma modele göre oluşturulur:

```
insights-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/
RESOURCEGROUPS/{resource group name}/PROVIDERS/MICROSOFT.BATCH/
BATCHACCOUNTS/{Batch account name}/y={four-digit numeric year}/
m={two-digit numeric month}/d={two-digit numeric day}/
h={two-digit 24-hour clock hour}/m=00/PT1H.json
```
Örnek:

```
insights-metrics-pt1m/resourceId=/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/
RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.BATCH/
BATCHACCOUNTS/MYBATCHACCOUNT/y=2018/m=03/d=05/h=22/m=00/PT1H.json
```
Her `PT1H.json` blob dosyası, blob URL 'sinde belirtilen saat içinde gerçekleşen JSON biçimli olayları içerir (örneğin, `h=12`). Mevcut saat boyunca olaylar, oluşan sırada `PT1H.json` dosyaya eklenir. Tanılama günlüğü olayları saat`m=00`başına ayrı bloblara bölündüğü için, dakika değeri () her zaman `00`olur. (Her zaman UTC biçimindedir.)

Bir `PoolResizeCompleteEvent` `PT1H.json` günlük dosyasındaki bir girdinin örneği aşağıda verilmiştir. Bu, adanmış ve düşük öncelikli düğümlerin geçerli ve hedef sayısı ile işlemin başlangıç ve bitiş saati hakkında bilgiler içerir:

```
{ "Tenant": "65298bc2729a4c93b11c00ad7e660501", "time": "2019-08-22T20:59:13.5698778Z", "resourceId": "/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.BATCH/BATCHACCOUNTS/MYBATCHACCOUNT/", "category": "ServiceLog", "operationName": "PoolResizeCompleteEvent", "operationVersion": "2017-06-01", "properties": {"id":"MYPOOLID","nodeDeallocationOption":"Requeue","currentDedicatedNodes":10,"targetDedicatedNodes":100,"currentLowPriorityNodes":0,"targetLowPriorityNodes":0,"enableAutoScale":false,"isAutoPool":false,"startTime":"2019-08-22 20:50:59.522","endTime":"2019-08-22 20:59:12.489","resultCode":"Success","resultMessage":"The operation succeeded"}}
```

Tanılama günlükleri depolama hesabındaki şeması hakkında daha fazla bilgi için bkz. [Azure tanılama günlüklerini arşivleme](../azure-monitor/platform/resource-logs-collect-storage.md#schema-of-resource-logs-in-storage-account). Depolama hesabınızdaki günlüklerini programlı olarak erişmek için depolama API'leri kullanın. 

### <a name="service-log-events"></a>Hizmet günlüğü olayları
Azure Batch hizmeti toplanan, günlükleri ayrı bir Batch kaynak havuzu veya göreviyle gibi kullanım ömrü süresince Azure Batch hizmeti tarafından oluşturulan olayları içerir. Batch tarafından yayılan her olay, JSON biçiminde kaydedilir. Örneğin, bu bir örnek gövdesidir **havuz oluşturma olayı**:

```json
{
    "poolId": "myPool1",
    "displayName": "Production Pool",
    "vmSize": "Small",
    "cloudServiceConfiguration": {
        "osFamily": "5",
        "targetOsVersion": "*"
    },
    "networkConfiguration": {
        "subnetId": " "
    },
    "resizeTimeout": "300000",
    "targetDedicatedComputeNodes": 2,
    "maxTasksPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoscale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

Batch hizmeti, şu anda aşağıdaki hizmet günlüğü olaylarını gösterir. Bu makalenin son güncelleştirildiği olduğundan ek olaylar eklenmiş olabilecek olduğundan bu liste kapsamlı, olmayabilir.

| **Hizmet günlüğü olayları** |
| --- |
| [Havuz oluşturma](batch-pool-create-event.md) |
| [Havuz silme başlangıç](batch-pool-delete-start-event.md) |
| [Tam havuzunu Sil](batch-pool-delete-complete-event.md) |
| [Havuz yeniden boyutlandırma başlangıç](batch-pool-resize-start-event.md) |
| [Tam havuzunu boyutlandırma](batch-pool-resize-complete-event.md) |
| [Görev Başlat](batch-task-start-event.md) |
| [Görev tamamlandı](batch-task-complete-event.md) |
| [Görev başarısız](batch-task-fail-event.md) |



## <a name="next-steps"></a>Sonraki adımlar

* Batch çözümleri oluşturmak için kullanılabilen [Batch API’leri ve araçları](batch-apis-tools.md) hakkında bilgi alın.
* Daha fazla bilgi edinin [Batch çözümlerini izleme](monitoring-overview.md).
