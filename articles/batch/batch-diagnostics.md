---
title: Ölçümler, uyarılar ve tanılama günlükleri
description: Havuzlar ve görevler gibi Azure Batch hesabı kaynakları için tanılama günlüğü olaylarını kaydedin ve çözümleyin.
ms.topic: how-to
ms.date: 04/13/2021
ms.custom: seodec18
ms.openlocfilehash: 61aaca84b609aaf7513c6de6f0f7e73aef5a5efe
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389324"
---
# <a name="batch-metrics-alerts-and-logs-for-diagnostic-evaluation-and-monitoring"></a>Tanılama değerlendirmesi ve izleme için toplu iş ölçümleri, uyarılar ve Günlükler

Azure Izleyici, Azure Batch hesabınızdaki kaynaklar için [ölçümleri](../azure-monitor/essentials/data-platform-metrics.md) ve [tanılama günlüklerini](../azure-monitor/essentials/platform-logs-overview.md) toplar.

Batch hesabınızı izlemek ve sorunları tanılamak için çeşitli yollarla bu verileri toplayabilir ve kullanabilirsiniz. Ölçüm [uyarılarını](../azure-monitor/alerts/alerts-overview.md) Ayrıca, bir ölçüm belirtilen bir değere ulaştığında bildirimler almanızı sağlayacak şekilde de yapılandırabilirsiniz.

## <a name="batch-metrics"></a>Toplu iş ölçümleri

[Ölçümler](../azure-monitor/essentials/data-platform-metrics.md)  , Azure kaynaklarınız tarafından yayınlanan ve Azure İzleyici hizmeti tarafından tüketilen Azure telemetri verileri (performans sayaçları da denir). Batch hesabındaki ölçümlere örnek olarak havuz oluşturma olayları, Low-Priority düğüm sayısı ve görev tamamlanma olayları verilebilir. Bu ölçümler eğilimleri belirlemenize yardımcı olabilir ve veri analizi için kullanılabilir.

[Desteklenen toplu iş ölçümleri listesine](../azure-monitor/essentials/metrics-supported.md#microsoftbatchbatchaccounts)bakın.

Ölçümler şunlardır:

- Ek yapılandırma olmadan her Batch hesabında varsayılan olarak etkindir
- Her 1 dakikada bir oluşturulur
- Otomatik olarak kalıcı değildir, ancak 30 günlük bir geçmiş geçmişi vardır. Tanılama günlüğü 'nün bir parçası olarak etkinlik ölçümlerini kalıcı hale getirebilirsiniz.

## <a name="view-batch-metrics"></a>Toplu ölçümleri görüntüleme

Azure portal, Batch hesabının **genel bakış** sayfasında anahtar düğüm, çekirdek ve görev ölçümleri varsayılan olarak gösterilir.

Bir Batch hesabının ek ölçümlerini görüntülemek için:

1. Azure Portal, **tüm hizmetler**  >  **Batch hesapları**' nı seçin ve ardından Batch hesabınızın adını seçin.
1. **İzleme** seçeneğinin altından **Ölçümler**’i seçin.
1. **Ölçüm Ekle** ' yi seçin ve ardından açılır listeden bir ölçüm seçin.
1. Ölçüm için bir **toplama** seçeneği belirleyin. Count tabanlı ölçümler için ("adanmış çekirdek sayısı" veya "düşük öncelikli düğüm sayısı" gibi), **Ort** toplamayı kullanın. Olay tabanlı ölçümler ("havuz yeniden boyutlandırma tam olayları" gibi) için, "toplama" **sayısını** kullanın. **Toplam** toplamayı kullanmaktan kaçının, bu, grafiğin dönemi boyunca alınan tüm veri noktalarının değerlerini ekler.
1. Ek ölçümler eklemek için 3 ve 4 numaralı adımları tekrarlayın.

Ölçümleri Azure Izleyici API 'Leri ile programlı olarak da alabilirsiniz. Bir örnek için bkz. [.net Ile Azure izleyici ölçümlerini alma](/samples/azure-samples/monitor-dotnet-metrics-api/monitor-dotnet-metrics-api/).

> [!NOTE]
> Son 3 dakikada bulunan ölçümler yine de bir araya gelebilir, bu nedenle değerler bu zaman dilimi boyunca rapor altında olabilir. Ölçüm teslimi garanti edilmez ve sipariş dışı teslim, veri kaybı veya çoğaltmasıyla etkilenebilir.

## <a name="batch-metric-alerts"></a>Toplu ölçüm uyarıları

Belirtilen bir ölçümün değeri atadığınız bir eşiği aştığında tetiklenecek neredeyse gerçek zamanlı ölçüm uyarılarını yapılandırabilirsiniz. Uyarı, uyarı "etkinleştirildiğinde" (eşik geçildiğinde ve uyarı koşulu karşılandığında) ve "çözümlenmiş" olduğunda (eşik yeniden geçildiğinde ve koşul artık karşılanamadığında) bir bildirim oluşturur.

Ölçüm teslimi, sipariş dışı teslim, veri kaybı veya yineleme gibi tutarsızlıklara tabi olabileceğinden, tek bir veri noktasında tetiklenecek uyarıların önlemeyi yapmanızı öneririz. Bunun yerine, bir süre boyunca sıra dışı teslim, veri kaybı ve yineleme gibi herhangi bir tutarsızlıkların hesaba katması için eşikleri kullanın.

Örneğin, düşük öncelikli çekirdek sayılarınız belirli bir düzeye düştüğünde bir ölçüm uyarısı yapılandırmak isteyebilirsiniz, bu sayede havuzlarınızın birleşimini ayarlayabilirsiniz. En iyi sonuçlar için, ortalama düşük öncelikli çekirdek sayısı dönemin tamamına ait eşik değerinin altına düşerse uyarının tetiklenmesi için 10 veya daha fazla dakikalık bir süre ayarlayın. Bu, daha doğru sonuçlar elde etmenizi sağlamak için ölçümlerin toplaması için zaman sağlar.

Azure portal bir ölçüm uyarısı yapılandırmak için:

1. **Tüm hizmetler**  >  **Batch hesapları**' nı seçin ve ardından Batch hesabınızın adını seçin.
1. **İzleme** altında **Uyarılar**' ı seçin ve ardından **Yeni uyarı kuralı**' nı seçin.
1. **Koşul Ekle**' yi seçin ve bir ölçüm seçin.
1. **Grafik süresi**, **eşik**, **işleç** ve **toplama türü** için istenen değerleri seçin.
1. Bir **eşik değeri** girin ve eşiğin **birimini** seçin.  Ardından **Bitti**'yi seçin.
1. Var olan bir eylem grubunu seçerek veya yeni bir eylem grubu oluşturarak uyarıya bir [eylem grubu](../azure-monitor/alerts/action-groups.md) ekleyin.
1. **Uyarı kuralı ayrıntıları** bölümünde, bir **Uyarı kuralı adı** ve **açıklaması** girin. Uyarının hemen etkinleştirilmesini istiyorsanız, **oluşturma sonrasında uyarı kuralını etkinleştir** kutusunun işaretli olduğundan emin olun.
1. **Uyarı kuralı oluşturma**’yı seçin.

Ölçüm uyarıları oluşturma hakkında daha fazla bilgi için bkz. [ölçüm uyarılarının Azure izleyici 'de nasıl çalıştığını anlama](../azure-monitor/alerts/alerts-metric-overview.md) ve [Azure izleyici kullanarak ölçüm uyarıları oluşturma, görüntüleme ve yönetme](../azure-monitor/alerts/alerts-metric.md).

[Azure izleyici REST API](/rest/api/monitor/)kullanarak neredeyse gerçek zamanlı bir uyarı da yapılandırabilirsiniz. Daha fazla bilgi için bkz. [Microsoft Azure uyarılara genel bakış](../azure-monitor/alerts/alerts-overview.md). Uyarılarınıza işi, görevi veya havuza özel bilgileri eklemek için bkz. [Azure Izleyici uyarıları ile olaylara yanıt verme](../azure-monitor/alerts/tutorial-response.md).

## <a name="batch-diagnostics"></a>Batch tanılama

[Tanılama günlükleri](../azure-monitor/essentials/platform-logs-overview.md) , her bir kaynağın çalışmasını tanımlayan Azure kaynakları tarafından yayılan bilgileri içerir. Batch için aşağıdaki günlükleri toplayabilirsiniz:

- **ServiceLog**: bir havuz veya görev gibi tek bir kaynağın ömrü boyunca [Batch hizmeti tarafından yayılan olaylar](#service-log-events) .
- **Allölçümler**: toplu işlem hesabı düzeyindeki ölçümler.

İzlemek istediğiniz her Batch hesabı için tanılama ayarlarını açıkça etkinleştirmeniz gerekir.

### <a name="log-destination-options"></a>Günlük hedefi seçenekleri

Yaygın bir senaryo, günlük hedefi olarak bir Azure depolama hesabı seçmsudur. Günlükleri Azure depolamada depolamak için, günlüklerin toplanmasını etkinleştirmeden önce hesabı oluşturun. Batch hesabınızla bir depolama hesabı ilişkilendirdiyseniz, bu hesabı günlük hedefi olarak seçebilirsiniz.

Alternatif olarak şunları yapabilirsiniz:

- Batch tanılama günlüğü olaylarını bir [Azure Olay Hub 'ına](../event-hubs/event-hubs-about.md)akış. Event Hubs, saniye başına milyonlarca olayı alabilir ve bu sayede herhangi bir gerçek zamanlı analiz sağlayıcısını kullanarak dönüştürebilir ve depolayabilirler.
- Tanılama günlüklerini [Azure izleyici günlüklerine](../azure-monitor/logs/log-query-overview.md)göndererek bunları analiz edebilir veya Power BI veya Excel 'de Analize aktarabilirsiniz.

> [!NOTE]
> Azure hizmetleriyle tanılama günlüğü verilerini depolamak veya işlemek için ek ücret ödemeniz gerekebilir.

### <a name="enable-collection-of-batch-diagnostic-logs"></a>Batch tanılama günlüklerinin toplanmasını etkinleştir

Azure portal yeni bir tanılama ayarı oluşturmak için aşağıdaki adımları izleyin.

1. Azure Portal, **tüm hizmetler**  >  **Batch hesapları**' nı seçin ve ardından Batch hesabınızın adını seçin.
2. **İzleme** bölümünde **Tanılama ayarları**'nı seçin.
3. **Tanılama ayarları**' nda, **Tanılama ayarı Ekle**' yi seçin.
4. Ayar için bir ad girin.
5. Bir hedef seçin: **Log Analytics gönder**, **depolama hesabına Arşivle** veya **Olay Hub 'ına akış**. Bir depolama hesabı seçerseniz, isteğe bağlı olarak her günlük için verilerin saklanacağı gün sayısını seçebilirsiniz. Bekletme için gün sayısı belirtmezseniz, depolama hesabının ömrü boyunca veriler korunur.
6. **ServiceLog**, **allölçümlerini** veya her ikisini birden seçin.
7. Tanılama ayarını oluşturmak için **Kaydet** ' i seçin.

Azure portal, [Kaynak Yöneticisi şablonu](../azure-monitor/essentials/resource-manager-diagnostic-settings.md)kullanarak veya Azure PowerShell ya da Azure CLI kullanarak da [Tanılama ayarları oluşturarak](../azure-monitor/essentials/diagnostic-settings.md)günlük toplamayı etkinleştirebilirsiniz. Daha fazla bilgi için bkz. [Azure platform günlüklerine genel bakış](../azure-monitor/essentials/platform-logs-overview.md).

### <a name="access-diagnostics-logs-in-storage"></a>Depolama alanındaki tanılama günlüklerine erişin

[Batch tanılama günlüklerini bir depolama hesabında arşivlerseniz](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage), depolama hesabında ilgili bir olay meydana geldiğinde depolama kapsayıcısı oluşturulur. Blob 'lar aşağıdaki adlandırma düzenine göre oluşturulur:

```json
insights-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/
RESOURCEGROUPS/{resource group name}/PROVIDERS/MICROSOFT.BATCH/
BATCHACCOUNTS/{Batch account name}/y={four-digit numeric year}/
m={two-digit numeric month}/d={two-digit numeric day}/
h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Örnek:

```json
insights-metrics-pt1m/resourceId=/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/
RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.BATCH/
BATCHACCOUNTS/MYBATCHACCOUNT/y=2018/m=03/d=05/h=22/m=00/PT1H.json
```

Her `PT1H.json` BLOB dosyası, blob URL 'sinde belirtilen saat içinde gerçekleşen JSON biçimli olayları içerir (örneğin, `h=12` ). Mevcut saat boyunca olaylar, `PT1H.json` oluşan sırada dosyaya eklenir. `m=00` `00` Tanılama günlüğü olayları saat başına ayrı bloblara bölündüğü için, dakika değeri () her zaman olur. (Tüm zamanlar UTC 'de bulunur.)

Bir günlük dosyasındaki bir girdinin örneği aşağıda verilmiştir `PoolResizeCompleteEvent` `PT1H.json` . Bu, adanmış ve düşük öncelikli düğümlerin geçerli ve hedef sayısı ile işlemin başlangıç ve bitiş saati hakkında bilgiler içerir:

```json
{ "Tenant": "65298bc2729a4c93b11c00ad7e660501", "time": "2019-08-22T20:59:13.5698778Z", "resourceId": "/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.BATCH/BATCHACCOUNTS/MYBATCHACCOUNT/", "category": "ServiceLog", "operationName": "PoolResizeCompleteEvent", "operationVersion": "2017-06-01", "properties": {"id":"MYPOOLID","nodeDeallocationOption":"Requeue","currentDedicatedNodes":10,"targetDedicatedNodes":100,"currentLowPriorityNodes":0,"targetLowPriorityNodes":0,"enableAutoScale":false,"isAutoPool":false,"startTime":"2019-08-22 20:50:59.522","endTime":"2019-08-22 20:59:12.489","resultCode":"Success","resultMessage":"The operation succeeded"}}
```

Depolama hesabınızdaki günlüklere programlı bir şekilde erişmek için depolama API 'Lerini kullanın.

### <a name="service-log-events"></a>Hizmet günlüğü olayları

Azure Batch hizmet günlükleri, bir havuz veya görev gibi tekil bir toplu Iş kaynağının ömrü boyunca Batch hizmeti tarafından yayılan olayları içerir. Batch tarafından yayılan her olay JSON biçiminde günlüğe kaydedilir. Örneğin, bu örnek **Havuz oluşturma olayının** gövdesidir:

```json
{
    "id": "myPool1",
    "displayName": "Production Pool",
    "vmSize": "Standard_F1s",
    "imageType": "VirtualMachineConfiguration",
    "cloudServiceConfiguration": {
        "osFamily": "3",
        "targetOsVersion": "*"
    },
    "networkConfiguration": {
        "subnetId": " "
    },
    "virtualMachineConfiguration": {
          "imageReference": {
            "publisher": " ",
            "offer": " ",
            "sku": " ",
            "version": " "
          },
          "nodeAgentId": " "
        },
    "resizeTimeout": "300000",
    "targetDedicatedNodes": 2,
    "targetLowPriorityNodes": 2,
    "taskSlotsPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoScale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

Batch hizmeti tarafından yayılan hizmet günlüğü olayları şunları içerir:

- [Havuz oluşturma](batch-pool-create-event.md)
- [Havuz silme başlangıcı](batch-pool-delete-start-event.md)
- [Havuz silme Tamam](batch-pool-delete-complete-event.md)
- [Havuz yeniden boyutlandırma başlangıcı](batch-pool-resize-start-event.md)
- [Havuz yeniden boyutlandırma Tamam](batch-pool-resize-complete-event.md)
- [Havuz otomatik ölçeklendirme](batch-pool-autoscale-event.md)
- [Görev başlangıcı](batch-task-start-event.md)
- [Görev tamamlanma](batch-task-complete-event.md)
- [Görev başarısız](batch-task-fail-event.md)
- [Görev zamanlama başarısız](batch-task-schedule-fail-event.md)

## <a name="next-steps"></a>Sonraki adımlar

- Batch çözümleri oluşturmak için kullanılabilen [Batch API’leri ve araçları](batch-apis-tools.md) hakkında bilgi alın.
- [Toplu iş çözümlerini izleme](monitoring-overview.md)hakkında daha fazla bilgi edinin.
