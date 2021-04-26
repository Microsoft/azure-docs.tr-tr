---
title: Kaynak günlüklerini toplama & analiz etme
description: Kimlik doğrulama, görüntü gönderme ve resim çekme gibi Azure Container Registry kaynak günlüğü olaylarını kaydedin ve çözümleyin.
ms.topic: article
ms.date: 06/01/2020
ms.openlocfilehash: 952f239e94df6b3b21317985f56d3d7a999813fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100572001"
---
# <a name="azure-container-registry-logs-for-diagnostic-evaluation-and-auditing"></a>Tanılama değerlendirmesi ve denetimi için Azure Container Registry günlükleri

Bu makalede, Azure [izleyici](../azure-monitor/overview.md)'nin özellikleri kullanılarak Azure Container Registry için günlük verilerinin nasıl toplanacağı açıklanır. Azure Izleyici, kayıt defterinizde Kullanıcı odaklı olaylar için [kaynak günlüklerini](../azure-monitor/essentials/platform-logs-overview.md) (eski adıyla *tanılama günlükleri* olarak adlandırılır) toplar. Bu verileri toplayın ve aşağıdaki gibi gereksinimlerinizi karşılayacak şekilde kullanın:

* Güvenlik ve uyumluluk sağlamak için kayıt defteri kimlik doğrulama olaylarını denetleme 

* Kayıt defterlerinizle ilgili işlem sorunlarını tanılamanıza olanak tanımak için çekme ve çekme olayları gibi kayıt defteri yapıtlarına yönelik kapsamlı bir etkinlik izi sağlayın 

Azure Izleyici 'yi kullanarak kaynak günlük verilerinin toplanması ek maliyetlere neden olabilir. Bkz. [Azure izleyici fiyatlandırması](https://azure.microsoft.com/pricing/details/monitor/). 

## <a name="repository-events"></a>Depo olayları

Görüntüler ve diğer yapıtlar için aşağıdaki depo düzeyi olaylar şu anda günlüğe kaydedilir:

* **Hareketle**
* **Çek**
* **Etiketi Kaldır**
* **Sil** (depo silme olayları dahil)
* **Etiketi temizle** ve **bildirimi temizle**

> [!NOTE]
> Temizleme olayları yalnızca bir kayıt defteri [saklama ilkesi](container-registry-retention-policy.md) yapılandırılmışsa günlüğe kaydedilir.

## <a name="registry-resource-logs"></a>Kayıt defteri kaynak günlükleri

Kaynak günlükleri, iç işlemlerini tanımlayan Azure kaynakları tarafından yayılan bilgiler içerir. Azure Container Registry 'de, Günlükler aşağıdaki tablolarda depolanan kimlik doğrulama ve depo düzeyi olaylarını içerir. 

* **Kapsayıcıregistryloginevents**  -kayıt defteri doğrulama olayları ve gelen KIMLIK ve IP adresi dahil olmak üzere durum
* **Containerregistryhavuzuyevents** -kayıt defteri depolarındaki görüntüler ve diğer yapılar için gönderme ve çekme gibi işlemler
* **AzureMetrics**  -  Toplu gönderim ve çekme sayıları gibi [kapsayıcı kayıt defteri ölçümleri](../azure-monitor/essentials/metrics-supported.md#microsoftcontainerregistryregistries) .

İşlemler için günlük verileri şunları içerir:
  * Başarı veya başarısızlık durumu
  * Başlangıç ve bitiş zaman damgaları

Azure, kaynak günlüklerine ek olarak, bir kapsayıcı kayıt defterinin oluşturulması veya silinmesi gibi Azure Yönetim olaylarının tek abonelik düzeyinde bir kaydı olan bir [etkinlik günlüğü](../azure-monitor/essentials/platform-logs-overview.md)sağlar.

## <a name="enable-collection-of-resource-logs"></a>Kaynak günlüklerinin toplanmasını etkinleştir

Bir kapsayıcı kayıt defteri için kaynak günlüklerinin toplanması varsayılan olarak etkin değildir. İzlemek istediğiniz her kayıt defteri için tanılama ayarlarını açık bir şekilde etkinleştirin. Tanılama ayarlarını etkinleştirme seçenekleri için bkz. [Azure 'da platform günlüklerini ve ölçümlerini toplamak için tanılama ayarı oluşturma](../azure-monitor/essentials/diagnostic-settings.md).

Örneğin, Azure Izleyici 'de neredeyse gerçek zamanlı bir kapsayıcı kayıt defteri için günlükleri ve ölçümleri görüntülemek için, Log Analytics çalışma alanında kaynak günlüklerini toplayın. Azure portal kullanarak bu tanılama ayarını etkinleştirmek için:

1. Zaten bir çalışma alanınız yoksa [Azure Portal](../azure-monitor/logs/quick-create-workspace.md)kullanarak bir çalışma alanı oluşturun. Veri koleksiyonundaki gecikme süresini en aza indirmek için, çalışma alanının kapsayıcı kayıt defteriyle **aynı bölgede** olduğundan emin olun.
1. Portalda, kayıt defteri ' ni seçin ve **izleme > Tanılama ayarları > tanılama ayarı Ekle**' yi seçin.
1. Ayar için bir ad girin ve **Log Analytics gönder**' i seçin.
1. Kayıt defteri tanılama günlükleri için çalışma alanını seçin.
1. Toplamak istediğiniz günlük verilerini seçin ve **Kaydet**' e tıklayın.

Aşağıdaki görüntüde, portalı kullanarak bir kayıt defteri için tanılama ayarının oluşturulması gösterilmektedir.

![Tanılama ayarlarını etkinleştirme](media/container-registry-diagnostics-audit-logs/diagnostic-settings.png)

> [!TIP]
> Yalnızca ihtiyacınız olan verileri, ücret maliyetini ve izleme gereksinimlerinizi toplayın. Örneğin, yalnızca kimlik doğrulama olaylarını denetlemeniz gerekiyorsa yalnızca **Containerregistryloginevents** günlüğünü seçin. 

## <a name="view-data-in-azure-monitor"></a>Azure Izleyici 'de verileri görüntüleme

Log Analytics tanılama günlüklerini toplamayı etkinleştirdikten sonra, verilerin Azure Izleyici 'de gösterilmesi birkaç dakika sürebilir. Portalda verileri görüntülemek için kayıt defterini seçin ve **> günlüklerini izleme**' yi seçin. Kayıt defteri için veri içeren tablolardan birini seçin. 

Verileri görüntülemek için sorguları çalıştırın. Birkaç örnek sorgu sağlanır veya kendi kendinize çalıştırılır. Örneğin, aşağıdaki sorgu **Containerregistrydepotoryevents** tablosundan en son 24 saat verileri alır:

```Kusto
ContainerRegistryRepositoryEvents
| where TimeGenerated > ago(1d) 
```

Aşağıdaki görüntüde örnek çıkış gösterilmektedir:

![Günlük verilerini sorgulama](media/container-registry-diagnostics-audit-logs/azure-monitor-query.png)

Azure portal Log Analytics kullanmaya yönelik bir öğretici için bkz. [Azure izleyici 'yi kullanmaya başlama Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md)veya Log Analytics [demo ortamını](https://portal.loganalytics.io/demo)deneme. 

Günlük sorguları hakkında daha fazla bilgi için bkz. [Azure izleyici 'de günlük sorgularına genel bakış](../azure-monitor/logs/log-query-overview.md).

## <a name="query-examples"></a>Sorgu örnekleri

### <a name="error-events-from-the-last-hour"></a>Son saatin hata olayları

```Kusto
union Event, Syslog // Event table stores Windows event records, Syslog stores Linux records
| where TimeGenerated > ago(1h)
| where EventLevelName == "Error" // EventLevelName is used in the Event (Windows) records
    or SeverityLevel== "err" // SeverityLevel is used in Syslog (Linux) records
```

### <a name="100-most-recent-registry-events"></a>100 en son kayıt defteri olayları

```Kusto
ContainerRegistryRepositoryEvents
| union ContainerRegistryLoginEvents
| top 100 by TimeGenerated
| project TimeGenerated, LoginServer, OperationName, Identity, Repository, DurationMs, Region , ResultType
```

### <a name="identity-of-user-or-object-that-deleted-repository"></a>Depoyu silen kullanıcının veya nesnenin kimliği

```Kusto
ContainerRegistryRepositoryEvents
| where OperationName contains "Delete"
| project LoginServer, OperationName, Repository, Identity, CallerIpAddress
```

### <a name="identity-of-user-or-object-that-deleted-tag"></a>Etiketi silinen kullanıcının veya nesnenin kimliği

```Kusto
ContainerRegistryRepositoryEvents
| where OperationName contains "Untag"
| project LoginServer, OperationName, Repository, Tag, Identity, CallerIpAddress
```

### <a name="repository-level-operation-failures"></a>Depo düzeyi işlem sorunları

```kusto
ContainerRegistryRepositoryEvents 
| where ResultDescription contains "40"
| project TimeGenerated, OperationName, Repository, Tag, ResultDescription
```

### <a name="registry-authentication-failures"></a>Kayıt defteri kimlik doğrulama sorunları

```kusto
ContainerRegistryLoginEvents 
| where ResultDescription != "200"
| project TimeGenerated, Identity, CallerIpAddress, ResultDescription
```


## <a name="additional-log-destinations"></a>Ek günlük hedefleri

Günlükleri Log Analytics veya alternatif olarak göndermenin yanı sıra, bir Azure Depolama hesabını günlük hedefi olarak seçmek da yaygın bir senaryodur. Azure depolama 'da günlükleri arşivlemek için Tanılama ayarları aracılığıyla arşivlemeyi etkinleştirmeden önce bir depolama hesabı oluşturun.

Ayrıca, tanılama günlüğü olaylarını bir [Azure Olay Hub 'ına](../event-hubs/event-hubs-about.md)akışla aktarabilirsiniz. Event Hubs, saniye başına milyonlarca olayı alabilir ve bu sayede herhangi bir gerçek zamanlı analiz sağlayıcısını kullanarak dönüştürebilir ve depolayabilirler. 

## <a name="next-steps"></a>Sonraki adımlar

* [Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md) kullanma ve [günlük sorguları](../azure-monitor/logs/get-started-queries.md)oluşturma hakkında daha fazla bilgi edinin.
* Azure 'un farklı katmanlarında kullanılabilen platform günlükleri hakkında bilgi edinmek için bkz. [Azure platform günlüklerine genel bakış](../azure-monitor/essentials/platform-logs-overview.md) .