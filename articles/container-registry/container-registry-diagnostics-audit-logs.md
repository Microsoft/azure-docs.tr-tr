---
title: Azure Container Registry için kaynak günlükleri | Microsoft Docs
description: Kimlik doğrulama, görüntü gönderme ve resim çekme gibi Azure Container Registry kaynak günlüğü olaylarını kaydedin ve çözümleyin.
services: container-registry
documentationcenter: ''
author: dlepow
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: container-registry
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: ''
ms.date: 10/30/2019
ms.author: danlep
ms.openlocfilehash: e419f8c5cf06efc93294f9c428e9102c1f81b36a
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73180822"
---
# <a name="azure-container-registry-logs-for-diagnostic-evaluation-and-auditing"></a>Tanılama değerlendirmesi ve denetimi için Azure Container Registry günlükleri

Bu makalede, Azure [izleyici](../azure-monitor/overview.md)'nin özellikleri kullanılarak Azure Container Registry için günlük verilerinin nasıl toplanacağı açıklanır. Azure Izleyici, kayıt defterinizde Kullanıcı odaklı olaylar için [kaynak günlüklerini](../azure-monitor/platform/resource-logs-overview.md) (eski adıyla *tanılama günlükleri*olarak adlandırılır) toplar. Bu verileri toplayın ve aşağıdaki gibi gereksinimlerinizi karşılayacak şekilde kullanın:

* Güvenlik ve uyumluluk sağlamak için kayıt defteri kimlik doğrulama olaylarını denetleme 

* Kayıt defterlerinizle ilgili işlem sorunlarını tanılamanıza olanak tanımak için çekme ve çekme olayları gibi kayıt defteri yapıtlarına yönelik kapsamlı bir etkinlik izi sağlayın 

Azure Izleyici 'yi kullanarak kaynak günlük verilerinin toplanması ek maliyetlere neden olabilir. Bkz. [Azure izleyici fiyatlandırması](https://azure.microsoft.com/pricing/details/monitor/). 


> [!IMPORTANT]
> Bu özellik şu anda önizleme aşamasındadır ve bazı [sınırlamalar](#preview-limitations) geçerlidir. Önizlemeler, [ek kullanım koşullarını][terms-of-use] kabul etmeniz şartıyla kullanımınıza sunulur. Bu özelliğin bazı yönleri genel kullanıma açılmadan önce değişebilir.

## <a name="preview-limitations"></a>Önizleme sınırlamaları

Depo düzeyindeki olayların günlüğe kaydedilmesi şu anda olayları silme veya etiketi kaldırma içermiyor. Yalnızca aşağıdaki depo olayları günlüğe kaydedilir:
* Görüntüler ve diğer yapıtlar için **anında iletme olayları**
* Görüntüler ve diğer yapıtlar için **çekme olayları**

## <a name="registry-resource-logs"></a>Kayıt defteri kaynak günlükleri

Kaynak günlükleri, iç işlemlerini tanımlayan Azure kaynakları tarafından yayılan bilgiler içerir. Azure Container Registry 'de, Günlükler aşağıdaki tablolarda depolanan kimlik doğrulama ve depo düzeyi olaylarını içerir. 

* **Kapsayıcıregistryloginevents** -kayıt defteri doğrulama olayları ve gelen KIMLIK ve IP adresi dahil olmak üzere durum
* **Containerregistryhavuzuyevents** -kayıt defteri depolarındaki görüntüler ve diğer yapılar için gönderme ve çekme gibi işlemler
* Toplu gönderim ve çekme sayıları gibi **AzureMetrics** - [kapsayıcı kayıt defteri ölçümleri](../azure-monitor/platform/metrics-supported.md#microsoftcontainerregistryregistries) .

İşlemler için günlük verileri şunları içerir:
  * Başarı veya başarısızlık durumu
  * Başlangıç ve bitiş zaman damgaları

Azure, kaynak günlüklerine ek olarak, bir kapsayıcı kayıt defterinin oluşturulması veya silinmesi gibi Azure Yönetim olaylarının tek abonelik düzeyinde bir kaydı olan bir [etkinlik günlüğü](../azure-monitor/platform/activity-logs-overview.md)sağlar.

## <a name="enable-collection-of-resource-logs"></a>Kaynak günlüklerinin toplanmasını etkinleştir

Bir kapsayıcı kayıt defteri için kaynak günlüklerinin toplanması varsayılan olarak etkin değildir. İzlemek istediğiniz her kayıt defteri için tanılama ayarlarını açık bir şekilde etkinleştirin. Tanılama ayarlarını etkinleştirme seçenekleri için bkz. [Azure 'da platform günlüklerini ve ölçümlerini toplamak için tanılama ayarı oluşturma](../azure-monitor/platform/diagnostic-settings.md).

Örneğin, Azure Izleyici 'de neredeyse gerçek zamanlı bir kapsayıcı kayıt defteri için günlükleri ve ölçümleri görüntülemek için, Log Analytics çalışma alanında kaynak günlüklerini toplayın. Azure portal kullanarak bu tanılama ayarını etkinleştirmek için:

1. Zaten bir çalışma alanınız yoksa [Azure Portal](../azure-monitor/learn/quick-create-workspace.md)kullanarak bir çalışma alanı oluşturun. Veri koleksiyonundaki gecikme süresini en aza indirmek için, çalışma alanının kapsayıcı kayıt defteriyle **aynı bölgede** olduğundan emin olun.
1. Portalda, kayıt defteri ' ni seçin ve **izleme > Tanılama ayarları > Tanılama ayarı Ekle**' yi seçin.
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

Azure portal Log Analytics kullanmaya yönelik bir öğretici için bkz. [Azure izleyici 'yi kullanmaya başlama Log Analytics](../azure-monitor/log-query/get-started-portal.md)veya Log Analytics [demo ortamını](https://portal.loganalytics.io/demo)deneme. 

Günlük sorguları hakkında daha fazla bilgi için bkz. [Azure izleyici 'de günlük sorgularına genel bakış](../azure-monitor/log-query/log-query-overview.md).

### <a name="additional-query-examples"></a>Ek sorgu örnekleri

#### <a name="100-most-recent-registry-events"></a>100 en son kayıt defteri olayları

```Kusto
ContainerRegistryRepositoryEvents
| union ContainerRegistryLoginEvents
| top 100 by TimeGenerated
| project TimeGenerated, LoginServer , OperationName , Identity , Repository , DurationMs , Region , ResultType
```

## <a name="additional-log-destinations"></a>Ek günlük hedefleri

Günlükleri Log Analytics veya alternatif olarak göndermenin yanı sıra, bir Azure Depolama hesabını günlük hedefi olarak seçmek da yaygın bir senaryodur. Azure depolama 'da günlükleri arşivlemek için Tanılama ayarları aracılığıyla arşivlemeyi etkinleştirmeden önce bir depolama hesabı oluşturun.

Ayrıca, tanılama günlüğü olaylarını bir [Azure Olay Hub 'ına](../event-hubs/event-hubs-what-is-event-hubs.md)akışla aktarabilirsiniz. Event Hubs, saniye başına milyonlarca olayı alabilir ve bu sayede herhangi bir gerçek zamanlı analiz sağlayıcısını kullanarak dönüştürebilir ve depolayabilirler. 

## <a name="next-steps"></a>Sonraki adımlar

* [Log Analytics](../azure-monitor/log-query/get-started-portal.md) kullanma ve [günlük sorguları](../azure-monitor/log-query/get-started-queries.md)oluşturma hakkında daha fazla bilgi edinin.
* Azure 'un farklı katmanlarında kullanılabilen platform günlükleri hakkında bilgi edinmek için bkz. [Azure platform günlüklerine genel bakış](../azure-monitor/platform/platform-logs-overview.md) .

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
