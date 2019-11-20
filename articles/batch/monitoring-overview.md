---
title: İzleyici Azure Batch | Microsoft Docs
description: Azure Batch için Azure izleme Hizmetleri, ölçümler, tanılama günlükleri ve diğer izleme özellikleri hakkında bilgi edinin.
services: batch
author: laurenhughes
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.workload: na
ms.date: 04/05/2018
ms.author: lahugh
ms.openlocfilehash: 3ac852b1d69c0e9fa224b449d6261eba6abaff32
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71260960"
---
# <a name="monitor-batch-solutions"></a>Batch çözümlerini izleme

Azure ve Batch hizmeti, Batch çözümlerinizi izlemek için bir dizi hizmet, araç ve API sağlar. Bu genel bakış makalesi, gereksinimlerinize uyan bir izleme yaklaşımını seçmenize yardımcı olur.

Azure kaynaklarını izlemeye yönelik Azure bileşenlerine ve hizmetlerine genel bir bakış için bkz. [Azure uygulamalarını ve kaynaklarını izleme](../monitoring-and-diagnostics/monitoring-overview.md).

## <a name="subscription-level-monitoring"></a>Abonelik düzeyinde izleme

Batch hesaplarını içeren abonelik düzeyinde, [Azure etkinlik günlüğü](../azure-monitor/platform/activity-logs-overview.md) işlemsel olay verilerini [çeşitli kategorilerde](../azure-monitor/platform/activity-logs-overview.md#categories-in-the-activity-log)toplar.

Özel olarak Batch hesapları için, etkinlik günlüğü hesap oluşturma ve silme ve anahtar yönetimiyle ilgili olayları toplar.

Etkinlik günlüğliğinizden olayları almanın bir yolu Azure portal kullanmaktır. **Tüm hizmetler** > **etkinlik günlüğü**' ne tıklayın. Veya Azure CLı, PowerShell cmdlet 'leri ya da Azure Izleyici REST API kullanarak olayları sorgulayın. Etkinlik günlüğünü de dışarı aktarabilir ya da [etkinlik günlüğü uyarılarını](../monitoring-and-diagnostics/monitoring-activity-log-alerts-new-experience.md)yapılandırabilirsiniz.

## <a name="batch-account-level-monitoring"></a>Batch hesabı düzeyinde izleme

[Azure izleyici](../azure-monitor/overview.md)özelliklerini kullanarak her Batch hesabını izleyin. Azure Izleyici, havuzlar, işler ve görevler gibi Batch hesabı düzeyinde kapsamlı kaynaklar için [ölçümleri](../azure-monitor/platform/data-platform-metrics.md) ve isteğe bağlı [tanılama günlüklerini](../azure-monitor/platform/resource-logs-overview.md) toplar. Batch hesabınızdaki etkinlikleri izlemek ve sorunları tanılamak için bu verileri el ile veya program aracılığıyla toplayıp kullanın. Ayrıntılar için bkz. [Tanılama değerlendirmesi ve izleme Için Batch ölçümleri, uyarılar ve Günlükler](batch-diagnostics.md).
 
> [!NOTE]
> Ölçümler, ek yapılandırma olmadan Batch hesabınızda varsayılan olarak kullanılabilir ve 30 günlük bir geçmiş geçmişi vardır. Bir Batch hesabı için tanılama günlüğünü etkinleştirmeniz gerekir ve tanılama günlüğü verilerini depolamak veya işlemek için ek ücret ödemeniz gerekebilir. 

## <a name="batch-resource-monitoring"></a>Batch kaynak izleme

Batch uygulamalarınızda işler, görevler, düğümler ve havuzlar dahil olmak üzere kaynaklarınızın durumunu izlemek veya sorgulamak için Batch API 'Lerini kullanın. Örneğin:

* [Görevleri ve işlem düğümlerini duruma göre say](batch-get-resource-counts.md)
* [Toplu Işlem kaynaklarını etkili bir şekilde listelemek için sorgular oluşturma](batch-efficient-list-queries.md)
* [Görev bağımlılıkları oluştur](batch-task-dependencies.md)
* Bir [İş Yöneticisi görevi](/rest/api/batchservice/job/add#jobmanagertask) kullanma
* [Görev durumunu](/rest/api/batchservice/task/list#taskstate) izleme
* [Düğüm durumunu](/rest/api/batchservice/computenode/list#computenodestate) izleme
* [Havuz durumunu](/rest/api/batchservice/pool/get#poolstate) izleme
* [Hesapta havuz kullanımını](/rest/api/batchservice/pool/listusagemetrics) izleme
* [Havuz düğümlerini duruma göre say](/rest/api/batchservice/account/listpoolnodecounts)

## <a name="vm-performance-counters-and-application-monitoring"></a>VM performans sayaçları ve uygulama izleme

* [Application Insights](../azure-monitor/app/app-insights-overview.md) , Batch işlerinizin ve görevlerinizin kullanılabilirliğini, performansını ve kullanımını programlı bir şekilde izlemek için kullanabileceğiniz bir Azure hizmetidir. İşlem düğümlerinden (VM) ve VM 'lerin görevleri için özel bilgilerden performans sayaçlarını kolayca alın. 

  Bir örnek için bkz. Application Insights ve birlikte bulunan [kod](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ApplicationInsights) [örneğiyle Batch .NET uygulamasını izleme ve hata ayıklama](monitor-application-insights.md) .

  > [!NOTE]
  > Application Insights kullanmak için ek ücret ödemeniz gerekebilir. [Fiyatlandırma seçeneklerine](https://azure.microsoft.com/pricing/details/application-insights/)bakın. 
  >

* [Batch Explorer](https://github.com/Azure/BatchExplorer) , Azure Batch uygulamaları oluşturmaya, hata ayıklamanıza ve izlemenize yardımcı olan ücretsiz, zengin özellikli, tek başına bir istemci aracıdır. Mac, Linux veya Windows için [yükleme paketi](https://azure.github.io/BatchExplorer/) indirebilirsiniz. İsteğe bağlı olarak, Batch çözümünüzü Batch Explorer VM performans sayaçları gibi [Application Insights verileri görüntüleyecek](https://github.com/Azure/batch-insights) şekilde yapılandırın.


## <a name="next-steps"></a>Sonraki adımlar

* Batch çözümleri oluşturmak için kullanılabilen [Batch API’leri ve araçları](batch-apis-tools.md) hakkında bilgi alın.
* Batch ile [tanılama günlüğü](batch-diagnostics.md) hakkında daha fazla bilgi edinin.