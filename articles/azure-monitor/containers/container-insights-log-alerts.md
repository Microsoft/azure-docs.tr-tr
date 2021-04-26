---
title: Kapsayıcı öngörülerinin günlük uyarıları | Microsoft Docs
description: Bu makalede, kapsayıcı öngörülerine bellek ve CPU kullanımı için nasıl özel günlük uyarıları oluşturacağınız açıklanır.
ms.topic: conceptual
ms.date: 01/05/2021
ms.openlocfilehash: 64d499d69194ac338d367ae094e42f4c8af23bef
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101711204"
---
# <a name="how-to-create-log-alerts-from-container-insights"></a>Kapsayıcı öngörülerine ait günlük uyarılarını oluşturma

Kapsayıcı öngörüleri, yönetilen veya otomatik olarak yönetilen Kubernetes kümelerine dağıtılan kapsayıcı iş yüklerinin performansını izler. Bu makalede önemli şeyler hakkında uyarı almak için, AKS kümelerinde aşağıdaki durumlar için günlük tabanlı uyarıların nasıl oluşturulacağı açıklanmaktadır:

- Küme düğümlerinde CPU veya bellek kullanımı bir eşiği aştığında
- Bir denetleyici içindeki herhangi bir kapsayıcıdaki CPU veya bellek kullanımı, karşılık gelen kaynak üzerinde ayarlanan sınıra kıyasla bir eşiği aşarsa
- *NotReady* durum düğümü sayıları
- *Başarısız*, *bekleyen*, *Bilinmeyen*, *çalışan* veya *başarılı* Pod-aşama sayıları
- Küme düğümlerindeki boş disk alanı bir eşiği aştığında

Yüksek CPU veya bellek kullanımı veya küme düğümlerinde düşük boş disk alanı için uyarı vermek üzere, bir ölçüm uyarısı veya ölçüm ölçüm uyarısı oluşturmak için belirtilen sorguları kullanın. Ölçüm uyarıları günlük uyarılarından daha düşük gecikme süresine sahip olsa da, günlük uyarıları gelişmiş sorgulama ve daha büyük gelişmiş algoritmaların mümkündür sağlar. Günlük uyarı sorguları, *Now* işlecini kullanarak ve bir saat geri dönerek bir tarih saatini mevcut ile karşılaştırır. (Kapsayıcı öngörüleri Eşgüdümlü Evrensel Saat (UTC) biçiminde tüm tarihleri depolar.)

Azure Izleyici uyarılarını bilmiyorsanız, başlamadan önce bkz. [Microsoft Azure uyarılara genel bakış](../alerts/alerts-overview.md) . Günlük sorgularını kullanan uyarılar hakkında daha fazla bilgi edinmek için bkz. [Azure izleyici 'de günlük uyarıları](../alerts/alerts-unified-log.md). Ölçüm uyarıları hakkında daha fazla bilgi için bkz. [Azure izleyici 'de ölçüm uyarıları](../alerts/alerts-metric-overview.md).

## <a name="resource-utilization-log-search-queries"></a>Kaynak kullanım günlüğü arama sorguları

Bu bölümdeki sorgular her uyarı senaryosunu destekler. Bu makalenin [uyarı oluştur](#create-an-alert-rule) bölümünde 7. adımda kullanılırlar.

Aşağıdaki sorgu ortalama CPU kullanımını her dakikada ortalama üye düğümlerin CPU kullanımı olarak hesaplar.  

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'cpuCapacityNanoCores';
let usageCounterName = 'cpuUsageNanoCores';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
// cluster filter would go here if multiple clusters are reporting to the same Log Analytics workspace
| distinct ClusterName, Computer
| join hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime
  | where TimeGenerated >= startDateTime
  | where ObjectName == 'K8SNode'
  | where CounterName == capacityCounterName
  | summarize LimitValue = max(CounterValue) by Computer, CounterName, bin(TimeGenerated, trendBinSize)
  | project Computer, CapacityStartTime = TimeGenerated, CapacityEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer
| join kind=inner hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime + trendBinSize
  | where TimeGenerated >= startDateTime - trendBinSize
  | where ObjectName == 'K8SNode'
  | where CounterName == usageCounterName
  | project Computer, UsageValue = CounterValue, TimeGenerated
) on Computer
| where TimeGenerated >= CapacityStartTime and TimeGenerated < CapacityEndTime
| project ClusterName, Computer, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize), ClusterName
```

Aşağıdaki sorgu ortalama bellek kullanımını her dakikada ortalama üye düğümlerinin bellek kullanımı olarak hesaplar.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'memoryCapacityBytes';
let usageCounterName = 'memoryRssBytes';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
// cluster filter would go here if multiple clusters are reporting to the same Log Analytics workspace
| distinct ClusterName, Computer
| join hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime
  | where TimeGenerated >= startDateTime
  | where ObjectName == 'K8SNode'
  | where CounterName == capacityCounterName
  | summarize LimitValue = max(CounterValue) by Computer, CounterName, bin(TimeGenerated, trendBinSize)
  | project Computer, CapacityStartTime = TimeGenerated, CapacityEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer
| join kind=inner hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime + trendBinSize
  | where TimeGenerated >= startDateTime - trendBinSize
  | where ObjectName == 'K8SNode'
  | where CounterName == usageCounterName
  | project Computer, UsageValue = CounterValue, TimeGenerated
) on Computer
| where TimeGenerated >= CapacityStartTime and TimeGenerated < CapacityEndTime
| project ClusterName, Computer, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize), ClusterName
```
>[!IMPORTANT]
>Aşağıdaki sorgular, yer tutucu değerlerini kullanır \<your-cluster-name> ve \<your-controller-name> kümenizi ve denetleyicinizi temsil eder. Uyarıları ayarlarken bunları ortamınıza özgü değerlerle değiştirin.

Aşağıdaki sorgu, bir denetleyicideki tüm kapsayıcıların ortalama CPU kullanımını, her dakikada bir denetleyicideki her kapsayıcı örneğinin ortalama CPU kullanımı olarak hesaplar. Ölçüm, bir kapsayıcı için ayarlanan sınırın yüzdesidir.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'cpuLimitNanoCores';
let usageCounterName = 'cpuUsageNanoCores';
let clusterName = '<your-cluster-name>';
let controllerName = '<your-controller-name>';
KubePodInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where ClusterName == clusterName
| where ControllerName == controllerName
| extend InstanceName = strcat(ClusterId, '/', ContainerName),
         ContainerName = strcat(controllerName, '/', tostring(split(ContainerName, '/')[1]))
| distinct Computer, InstanceName, ContainerName
| join hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ObjectName == 'K8SContainer'
    | where CounterName == capacityCounterName
    | summarize LimitValue = max(CounterValue) by Computer, InstanceName, bin(TimeGenerated, trendBinSize)
    | project Computer, InstanceName, LimitStartTime = TimeGenerated, LimitEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer, InstanceName
| join kind=inner hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime + trendBinSize
    | where TimeGenerated >= startDateTime - trendBinSize
    | where ObjectName == 'K8SContainer'
    | where CounterName == usageCounterName
    | project Computer, InstanceName, UsageValue = CounterValue, TimeGenerated
) on Computer, InstanceName
| where TimeGenerated >= LimitStartTime and TimeGenerated < LimitEndTime
| project Computer, ContainerName, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize) , ContainerName
```

Aşağıdaki sorgu, bir denetleyicideki tüm kapsayıcıların ortalama bellek kullanımını, her dakikada bir denetleyicideki her kapsayıcı örneğinin ortalama bellek kullanımı olarak hesaplar. Ölçüm, bir kapsayıcı için ayarlanan sınırın yüzdesidir.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'memoryLimitBytes';
let usageCounterName = 'memoryRssBytes';
let clusterName = '<your-cluster-name>';
let controllerName = '<your-controller-name>';
KubePodInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where ClusterName == clusterName
| where ControllerName == controllerName
| extend InstanceName = strcat(ClusterId, '/', ContainerName),
         ContainerName = strcat(controllerName, '/', tostring(split(ContainerName, '/')[1]))
| distinct Computer, InstanceName, ContainerName
| join hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ObjectName == 'K8SContainer'
    | where CounterName == capacityCounterName
    | summarize LimitValue = max(CounterValue) by Computer, InstanceName, bin(TimeGenerated, trendBinSize)
    | project Computer, InstanceName, LimitStartTime = TimeGenerated, LimitEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer, InstanceName
| join kind=inner hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime + trendBinSize
    | where TimeGenerated >= startDateTime - trendBinSize
    | where ObjectName == 'K8SContainer'
    | where CounterName == usageCounterName
    | project Computer, InstanceName, UsageValue = CounterValue, TimeGenerated
) on Computer, InstanceName
| where TimeGenerated >= LimitStartTime and TimeGenerated < LimitEndTime
| project Computer, ContainerName, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize) , ContainerName
```

Aşağıdaki sorgu, bir durumu *Ready* ve *NotReady* olan tüm düğümleri ve sayıları döndürür.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let clusterName = '<your-cluster-name>';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| distinct ClusterName, Computer, TimeGenerated
| summarize ClusterSnapshotCount = count() by bin(TimeGenerated, trendBinSize), ClusterName, Computer
| join hint.strategy=broadcast kind=inner (
    KubeNodeInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | summarize TotalCount = count(), ReadyCount = sumif(1, Status contains ('Ready'))
                by ClusterName, Computer,  bin(TimeGenerated, trendBinSize)
    | extend NotReadyCount = TotalCount - ReadyCount
) on ClusterName, Computer, TimeGenerated
| project   TimeGenerated,
            ClusterName,
            Computer,
            ReadyCount = todouble(ReadyCount) / ClusterSnapshotCount,
            NotReadyCount = todouble(NotReadyCount) / ClusterSnapshotCount
| order by ClusterName asc, Computer asc, TimeGenerated desc
```
Aşağıdaki sorgu tüm aşamalara göre Pod aşama sayısını döndürüyor: *başarısız*, *bekleyen*, *bilinmiyor*, *çalışıyor* veya *başarılı*.  

```kusto
let endDateTime = now(); 
let startDateTime = ago(1h);
let trendBinSize = 1m;
let clusterName = '<your-cluster-name>';
KubePodInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ClusterName == clusterName
    | distinct ClusterName, TimeGenerated
    | summarize ClusterSnapshotCount = count() by bin(TimeGenerated, trendBinSize), ClusterName
    | join hint.strategy=broadcast (
        KubePodInventory
        | where TimeGenerated < endDateTime
        | where TimeGenerated >= startDateTime
        | summarize PodStatus=any(PodStatus) by TimeGenerated, PodUid, ClusterName
        | summarize TotalCount = count(),
                    PendingCount = sumif(1, PodStatus =~ 'Pending'),
                    RunningCount = sumif(1, PodStatus =~ 'Running'),
                    SucceededCount = sumif(1, PodStatus =~ 'Succeeded'),
                    FailedCount = sumif(1, PodStatus =~ 'Failed')
                by ClusterName, bin(TimeGenerated, trendBinSize)
    ) on ClusterName, TimeGenerated
    | extend UnknownCount = TotalCount - PendingCount - RunningCount - SucceededCount - FailedCount
    | project TimeGenerated,
              TotalCount = todouble(TotalCount) / ClusterSnapshotCount,
              PendingCount = todouble(PendingCount) / ClusterSnapshotCount,
              RunningCount = todouble(RunningCount) / ClusterSnapshotCount,
              SucceededCount = todouble(SucceededCount) / ClusterSnapshotCount,
              FailedCount = todouble(FailedCount) / ClusterSnapshotCount,
              UnknownCount = todouble(UnknownCount) / ClusterSnapshotCount
| summarize AggregatedValue = avg(PendingCount) by bin(TimeGenerated, trendBinSize)
```

>[!NOTE]
>*Bekleyen*, *başarısız* veya *Bilinmeyen* gibi bazı Pod aşamalarda uyarı almak için sorgunun son satırını değiştirin. Örneğin, *FailedCount* kullanım durumunda uyarı almak için: <br/>`| summarize AggregatedValue = avg(FailedCount) by bin(TimeGenerated, trendBinSize)`

Aşağıdaki sorgu kullanılan %90 boş alanı aşan küme düğümleri disklerini döndürür. Küme KIMLIĞINI almak için, önce aşağıdaki sorguyu çalıştırın ve değeri `ClusterId` özelliğinden kopyalayın:

```kusto
InsightsMetrics
| extend Tags = todynamic(Tags)            
| project ClusterId = Tags['container.azm.ms/clusterId']   
| distinct tostring(ClusterId)   
``` 

```kusto
let clusterId = '<cluster-id>';
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
InsightsMetrics
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where Origin == 'container.azm.ms/telegraf'            
| where Namespace == 'container.azm.ms/disk'            
| extend Tags = todynamic(Tags)            
| project TimeGenerated, ClusterId = Tags['container.azm.ms/clusterId'], Computer = tostring(Tags.hostName), Device = tostring(Tags.device), Path = tostring(Tags.path), DiskMetricName = Name, DiskMetricValue = Val   
| where ClusterId =~ clusterId       
| where DiskMetricName == 'used_percent'
| summarize AggregatedValue = max(DiskMetricValue) by bin(TimeGenerated, trendBinSize)
| where AggregatedValue >= 90
```

## <a name="create-an-alert-rule"></a>Uyarı kuralı oluşturma

Bu bölümde, kapsayıcı öngörülerine ait performans verilerini kullanan ölçüm ölçümü uyarı kuralının oluşturulması gösterilmektedir. Bu temel işlemi, farklı performans sayaçlarıyla uyarı vermek için çeşitli günlük sorgularıyla kullanabilirsiniz. Kullanmaya başlamak için daha önce sağlanmış olan günlük arama sorgularından birini kullanın. ARM şablonu kullanarak oluşturmak için bkz. [Azure kaynak şablonu kullanarak günlük uyarısı oluşturma örnekleri](../alerts/alerts-log-create-templates.md).

>[!NOTE]
>Kapsayıcı kaynak kullanımı için bir uyarı kuralı oluşturmak için aşağıdaki yordam, [günlük uyarıları Için anahtar API tercihi](../alerts/alerts-log-api-switch.md)bölümünde açıklandığı gibi yeni bir günlük uyarıları API 'sine geçmeniz gerekir.
>

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. Azure portal, **Log Analytics çalışma alanlarını** arayıp seçin.
3. Log Analytics çalışma alanları listenizde, kapsayıcı öngörülerini destekleyen çalışma alanını seçin. 
4. Sol taraftaki bölmede **Günlükler** ' i seçerek Azure izleyici günlükleri sayfasını açın. Azure günlük sorgularını yazmak ve yürütmek için bu sayfayı kullanın.
5. **Günlükler** sayfasında, daha önce belirtilen [sorgulardan](#resource-utilization-log-search-queries) birini **arama sorgusu** alanına yapıştırın ve sonra sonuçları doğrulamak için **Çalıştır** ' ı seçin. Bu adımı gerçekleştirmeyin ve **+ Yeni uyarı** seçeneği seçilecek şekilde kullanılamaz.
6. Günlük uyarısı oluşturmak için **+ Yeni uyarı** ' yı seçin.
7. **Koşul** bölümünde, **özel günlük \<logic undefined> araması** önceden tanımlı özel günlük koşulu olduğunda ' ı seçin. Doğrudan Azure Izleyici günlükleri sayfasından bir uyarı kuralı oluşturduğumuz için **özel günlük araması** sinyali türü otomatik olarak seçilir.  
8. Daha önce belirtilen [sorgulardan](#resource-utilization-log-search-queries) birini **arama sorgusu** alanına yapıştırın.
9. Uyarıyı şu şekilde yapılandırın:

    1. Aşağı açılan **Tetikleyici** listesinden **Metrik ölçüm**'ü seçin. Ölçüm ölçümü, sorgudaki her bir nesne için belirtilen eşiğin üzerinde bir değer olan bir uyarı oluşturur.
    1. **Koşul** Için, **büyüktür**' i seçin ve CPU ve bellek kullanımı uyarıları için ilk temel **eşik** olarak **75** girin. Yetersiz disk alanı uyarısı için **90** girin. Ya da ölçütlerinizi karşılayan farklı bir değer girin.
    1. **Tetikleme uyarısı bölümüne göre** , **ardışık ihlal**' ı seçin. Aşağı açılan listeden **büyüktür**' i seçin ve **2** yazın.
    1. Kapsayıcı CPU veya bellek kullanımı için bir uyarı yapılandırmak için, **toplama** altında, **kapsayıcıadı**' nı seçin. Küme düğümü düşük disk uyarısını yapılandırmak için **Clusterıd**' yi seçin.
    1. **Göre değerlendirilen** bölümünde, **Dönem** değerini **60 dakika** olarak ayarlayın. Kural, her 5 dakikada bir çalışır ve geçerli zamandan son bir saat içinde oluşturulan kayıtları döndürür. Olası veri gecikmesi için zaman aralığını geniş bir pencere hesabına ayarlama. Ayrıca, uyarının hiçbir şekilde tetiklendiği yanlış negatifi önlemek için sorgunun verileri döndürdüğünden emin olmanızı sağlar.

10. Uyarı kuralını gerçekleştirmek için **bitti** ' yi seçin.
11. **Uyarı kuralı adı** alanına bir ad girin. Uyarı hakkındaki ayrıntıları sağlayan bir **Açıklama** belirtin. Ve, belirtilen seçeneklerden uygun bir önem derecesi düzeyi seçin.
12. Uyarı kuralını hemen etkinleştirmek için, **oluşturma sırasında kuralı etkinleştir** için varsayılan değeri kabul edin.
13. Mevcut bir **eylem grubu** seçin veya yeni bir grup oluşturun. Bu adım, bir uyarının tetiklendiği her seferinde aynı eylemlerin alınmasını sağlar. BT veya DevOps işlemleri takımınızın olayları yönetme şeklini temel alarak yapılandırın.
14. Uyarı kuralını gerçekleştirmek için **Uyarı kuralı oluştur** ' u seçin. Hemen çalıştırılmaya başlar.

## <a name="next-steps"></a>Sonraki adımlar

- Daha önceden tanımlanmış sorguları ve Uyarıları izlemek için değerlendirmek veya özelleştirmek üzere ön tanımlı sorguları ve örnekleri görmek için [günlük sorgusu örneklerini](container-insights-log-search.md#search-logs-to-analyze-data) görüntüleyin, kümelerinizi görselleştirmeyi veya çözümlemeyi inceleyin.

- Azure Izleyici hakkında daha fazla bilgi edinmek ve Kubernetes kümenizin diğer yönlerini izlemek için bkz. [Kubernetes küme performansını görüntüleme](container-insights-analyze.md) ve [Kubernetes küme durumunu görüntüleme](./container-insights-overview.md).