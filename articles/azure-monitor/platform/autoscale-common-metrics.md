---
title: Ortak ölçümleri otomatik ölçeklendirme
description: Cloud Services, sanal makinelerinizi ve Web Apps otomatik ölçeklendirme için hangi ölçümlerin yaygın olarak kullanıldığını öğrenin.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 12/6/2016
ms.author: ancav
ms.subservice: autoscale
ms.openlocfilehash: 9da8e5fb88ff34e561b579b760973ecd23c884a3
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/31/2019
ms.locfileid: "66129741"
---
# <a name="azure-monitor-autoscaling-common-metrics"></a>Azure Izleyici ortak ölçümleri otomatik ölçeklendirme

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure Izleyici otomatik ölçeklendirme, çalışan örneklerin sayısını telemetri verilerine (ölçümler) göre yukarı veya aşağı ölçeklendirmenize olanak tanır. Bu belge kullanmak isteyebileceğiniz ortak ölçümleri açıklar. Azure portal, ölçeklendirmek için kaynağın ölçüsünü seçebilirsiniz. Ancak, ölçeklendirmek üzere farklı bir kaynaktan herhangi bir ölçümü de seçebilirsiniz.

Azure Izleyici otomatik ölçeklendirme yalnızca [Sanal Makine Ölçek Kümeleri](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [Cloud Services](https://azure.microsoft.com/services/cloud-services/), [App Service-Web Apps](https://azure.microsoft.com/services/app-service/web/)ve [API Management Hizmetleri](https://docs.microsoft.com/azure/api-management/api-management-key-concepts)için geçerlidir. Diğer Azure Hizmetleri farklı ölçekleme yöntemleri kullanır.

## <a name="compute-metrics-for-resource-manager-based-vms"></a>Kaynak Yöneticisi tabanlı VM 'Ler için işlem ölçümleri
Varsayılan olarak, Kaynak Yöneticisi tabanlı sanal makineler ve sanal makine ölçek kümeleri temel (konak düzeyi) ölçümlerini yayar. Ayrıca, bir Azure VM ve VMSS için tanılama veri toplamayı yapılandırdığınızda, Azure tanılama uzantısı da konuk işletim sistemi performans sayaçlarını (genellikle "Konuk-işletim sistemi ölçümleri" olarak bilinir) yayar.  Tüm bu ölçümleri otomatik ölçeklendirme kurallarında kullanırsınız.

VMSS kaynağınız için `Get MetricDefinitions` kullanılabilen ölçümleri görüntülemek için API/Posh/CLI kullanabilirsiniz.

VM Ölçek Kümeleri kullanıyorsanız ve belirli bir ölçümü listede görmüyorsanız, bu durumda tanılama uzantıda *devre dışı bırakılır* .

Belirli bir ölçüm örneklenmiyor veya istediğiniz sıklıkta aktarılmadığından, tanılama yapılandırmasını güncelleştirebilirsiniz.

Yukarıdaki Case değeri true ise, Azure VM tanılama uzantınızı yapılandırmak ve güncelleştirmek için PowerShell ile [Windows çalıştıran bir sanal makinede Azure tanılama etkinleştirmek üzere PowerShell kullanın](../../virtual-machines/extensions/diagnostics-windows.md) ' ı gözden geçirin. Bu makalede ayrıca bir örnek tanılama yapılandırma dosyası de bulunur.

### <a name="host-metrics-for-resource-manager-based-windows-and-linux-vms"></a>Kaynak Yöneticisi tabanlı Windows ve Linux VM 'Leri için konak ölçümleri
Aşağıdaki konak düzeyi ölçümleri, hem Windows hem de Linux örneklerinde Azure VM ve VMSS için varsayılan olarak dağıtılır. Bu ölçümler, Azure VM 'nizi anlatmaktadır, ancak Konuk VM 'de yüklü aracı aracılığıyla değil, Azure VM konağından toplanır. Bu ölçümleri otomatik ölçeklendirme kurallarında kullanabilirsiniz.

- [Kaynak Yöneticisi tabanlı Windows ve Linux VM 'Leri için konak ölçümleri](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachines)
- [Kaynak Yöneticisi tabanlı Windows ve Linux VM Ölçek Kümeleri için konak ölçümleri](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachinescalesets)

### <a name="guest-os-metrics-resource-manager-based-windows-vms"></a>Konuk işletim sistemi ölçümleri Kaynak Yöneticisi tabanlı Windows VM 'Leri
Azure 'da bir VM oluşturduğunuzda, Tanılamalar tanılama uzantısı kullanılarak etkinleştirilir. Tanılama uzantısı, VM 'nin içinden alınan bir dizi ölçümü yayar. Bu, varsayılan olarak yayılmayan ölçümlerin sırasını otomatik olarak kapatabilmeniz anlamına gelir.

PowerShell 'de aşağıdaki komutu kullanarak ölçümlerin bir listesini oluşturabilirsiniz.

```
Get-AzMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Aşağıdaki ölçümler için bir uyarı oluşturabilirsiniz:

| Ölçüm Adı | Birim |
| --- | --- |
| \Processor(_Total)\% Processor Time |Percent |
| \ İşlemci (_Toplam\% ) ayrıcalıklı zaman |Percent |
| \Processor (_Total)\% Kullanıcı saati |Percent |
| \İşlemci bilgileri (_Toplam) \ işlemci sıklığı |Count |
| \System\Processes |Count |
| \Process (_Toplam) \ iş parçacığı sayısı |Count |
| \Process (_Total) \Tanıtıcı sayısı |Count |
| \ Kullanılan\% bellek kaydedilmiş bayt sayısı |Percent |
| \Memory\Available Bytes |Bayt |
| \ Bellek \ kaydedilmiş baytlar |Bayt |
| \Memory\commıt limiti |Bayt |
| \Bellek\havuz disk belleğine alınan baytlar |Bayt |
| \Bellek\havuz disk belleksiz baytlar |Bayt |
| \Fizikseldisk (\% _Total) disk saati |Percent |
| \Fiziksel disk (_Total\% ) disk okuma zamanı |Percent |
| \Fiziksel disk (_Total\% ) disk yazma zamanı |Percent |
| \Fiziksel disk (_Total) \Disk aktarımı/sn |CountPerSecond |
| \Fiziksel disk (_Total) \Disk Okuma/sn |CountPerSecond |
| \Fiziksel disk (_Total) \Disk yazma/sn |CountPerSecond |
| \Fiziksel disk (_Total) \Disk bayt/sn |BytesPerSecond |
| \Fiziksel disk (_Total) \Disk okuma bayt/sn |BytesPerSecond |
| \Fiziksel disk (_Total) \Disk yazma bayt/sn |BytesPerSecond |
| \Fiziksel disk (_Total) \Avg. Disk Kuyruğu Uzunluğu |Count |
| \Fiziksel disk (_Total) \Avg. Disk okuma sırası uzunluğu |Count |
| \Fiziksel disk (_Total) \Avg. Disk yazma sırası uzunluğu |Count |
| \MantıksalDisk (_Total\% ) boş alanı |Percent |
| \Mantıksaldisk (_Total) \ boş megabayt |Count |

### <a name="guest-os-metrics-linux-vms"></a>Konuk işletim sistemi ölçümleri Linux VM 'Leri
Azure 'da bir VM oluşturduğunuzda, Tanılamalar, tanılama uzantısı kullanılarak varsayılan olarak etkinleştirilir.

PowerShell 'de aşağıdaki komutu kullanarak ölçümlerin bir listesini oluşturabilirsiniz.

```
Get-AzMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

 Aşağıdaki ölçümler için bir uyarı oluşturabilirsiniz:

| Ölçüm Adı | Birim |
| --- | --- |
| \Bellek\kullanılabilirlik blememory |Bayt |
| \Memory\yüztavailablememory |Percent |
| \Bellek\usedmemory |Bayt |
| \Memory\yüztusedmemory |Percent |
| \Memory\yüztusedbycache |Percent |
| \Bellek\pagespersec |CountPerSecond |
| \Bellek\pagesreadpersec |CountPerSecond |
| \Memory\PagesWrittenPerSec |CountPerSecond |
| \Memory\AvailableSwap |Bayt |
| \Memory\yüztavailableswap |Percent |
| \Bellek\usedswap |Bayt |
| \Memory\yüztusedswap |Percent |
| \Processor\PercentIdleTime |Percent |
| \Processor\PercentUserTime |Percent |
| \Processor\PercentNiceTime |Percent |
| \Processor\PercentPrivilegedTime |Percent |
| \Processor\PercentInterruptTime |Percent |
| \Processor\PercentDPCTime |Percent |
| \Processor\PercentProcessorTime |Percent |
| \Processor\PercentIOWaitTime |Percent |
| \PhysicalDisk\BytesPerSecond |BytesPerSecond |
| \PhysicalDisk\ReadBytesPerSecond |BytesPerSecond |
| \PhysicalDisk\WriteBytesPerSecond |BytesPerSecond |
| \PhysicalDisk\TransfersPerSecond |CountPerSecond |
| \PhysicalDisk\ReadsPerSecond |CountPerSecond |
| \PhysicalDisk\WritesPerSecond |CountPerSecond |
| \PhysicalDisk\AverageReadTime |Saniye |
| \ Physicaldisk\averagewritetime |Saniye |
| \PhysicalDisk\AverageTransferTime |Saniye |
| \ Physicaldisk\averagediskqueuelength |Count |
| \Networkınterface\bytestransmderlenen |Bayt |
| \NetworkInterface\BytesReceived |Bayt |
| \Networkınterface\packetstransmderlenen |Count |
| \Networkınterface\packetsalındı |Count |
| \Networkınterface\bytestotal |Bayt |
| \Networkınterface\totalrxerrors |Count |
| \NetworkInterface\TotalTxErrors |Count |
| \Networkınterface\totalçarpışmalar |Count |

## <a name="commonly-used-web-server-farm-metrics"></a>Yaygın olarak kullanılan Web (sunucu grubu) ölçümleri
Ayrıca, http kuyruğu uzunluğu gibi genel Web sunucusu ölçümlerine göre otomatik ölçeklendirme yapabilirsiniz. Ölçüm adı **Httpqueuelength**olur.  Aşağıdaki bölümde, kullanılabilir sunucu grubu (Web Apps) ölçümleri listelenmektedir.

### <a name="web-apps-metrics"></a>Web Apps ölçümleri
PowerShell 'de aşağıdaki komutu kullanarak Web Apps ölçümlerinin bir listesini oluşturabilirsiniz.

```
Get-AzMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Bu ölçümler ile uyarı verebilir veya ölçeklendirebilirsiniz.

| Ölçüm Adı | Birim |
| --- | --- |
| Cpuyüzdesi |Percent |
| MemoryPercentage |Percent |
| DiskQueueLength |Count |
| HttpQueueLength |Count |
| BytesReceived |Bayt |
| BytesSent |Bayt |

## <a name="commonly-used-storage-metrics"></a>Yaygın olarak kullanılan depolama ölçümleri
Depolama kuyruğu uzunluğuna göre ölçeklendirebilirsiniz, bu da depolama sırasındaki ileti sayısıdır. Depolama kuyruğu uzunluğu özel bir ölçümdür ve eşik, örnek başına düşen ileti sayısıdır. Örneğin, iki örnek varsa ve eşik 100 olarak ayarlandıysa, sıradaki toplam ileti sayısı 200 olduğunda ölçeklendirme oluşur. Bu, örnek başına 100 ileti, 120 ve 80 veya 200 ya da daha fazlasını ekleyen herhangi bir bileşim olabilir.

**Ayarlar** dikey penceresindeki Azure Portal bu ayarı yapılandırın. VM Ölçek Kümeleri için, Kaynak Yöneticisi şablonundaki otomatik ölçeklendirme ayarını, *MetricName* değerini *yaklaşık temessagecount* olarak KULLANABILIR ve depolama kuyruğunun kimliğini *metricresourceuri*olarak geçirebilirsiniz.

Örneğin, klasik bir depolama hesabıyla, metricTrigger otomatik ölçeklendirme ayarı şunları içerir:

```
"metricName": "ApproximateMessageCount",
 "metricNamespace": "",
 "metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.ClassicStorage/storageAccounts/STORAGE_ACCOUNT_NAME/services/queue/queues/QUEUE_NAME"
 ```

(Klasik olmayan) bir depolama hesabı için, metricTrigger şunları içerir:

```
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.Storage/storageAccounts/STORAGE_ACCOUNT_NAME/services/queue/queues/QUEUE_NAME"
```

## <a name="commonly-used-service-bus-metrics"></a>Yaygın olarak kullanılan Service Bus ölçümleri
Service Bus sırasındaki ileti sayısı olan Service Bus kuyruğu uzunluğuna göre ölçeklendirebilirsiniz. Service Bus kuyruğu uzunluğu özel bir ölçümdür ve eşik, örnek başına düşen ileti sayısıdır. Örneğin, iki örnek varsa ve eşik 100 olarak ayarlandıysa, sıradaki toplam ileti sayısı 200 olduğunda ölçeklendirme oluşur. Bu, örnek başına 100 ileti, 120 ve 80 veya 200 ya da daha fazlasını ekleyen herhangi bir bileşim olabilir.

VM Ölçek Kümeleri için, Kaynak Yöneticisi şablonundaki otomatik ölçeklendirme ayarını, *MetricName* değerini *yaklaşık temessagecount* olarak KULLANABILIR ve depolama kuyruğunun kimliğini *metricresourceuri*olarak geçirebilirsiniz.

```
"metricName": "MessageCount",
 "metricNamespace": "",
"metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.ServiceBus/namespaces/SB_NAMESPACE/queues/QUEUE_NAME"
```

> [!NOTE]
> Service Bus için kaynak grubu kavramı yok, Azure Resource Manager her bölge için varsayılan bir kaynak grubu oluşturur. Kaynak grubu genellikle ' default-ServiceBus-[Region] ' biçiminde olur. Örneğin, 'Varsayılan-ServiceBus-EastUS', 'Varsayılan-ServiceBus-WestUS', 'Varsayılan-ServiceBus-AustraliaEast' vb.
>
>

