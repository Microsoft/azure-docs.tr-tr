---
title: Azure Kaynak günlüklerine genel bakış | Microsoft Docs
description: Azure Kaynak günlükleri için desteklenen Hizmetleri ve olay şemasını anlayın.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: reference
author: rboucher
ms.author: robb
ms.date: 10/22/2019
ms.openlocfilehash: b953f9b5e5fd8c853746caad3047986786bd1317
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/28/2019
ms.locfileid: "72989616"
---
# <a name="azure-resource-logs-overview"></a>Azure Kaynak günlüklerine genel bakış
Azure Kaynak günlükleri, iç işlemlerini tanımlayan Azure kaynakları tarafından yayılan [Platform günlüklerdir](platform-logs-overview.md) . Tüm kaynak günlükleri, ortak bir üst düzey şemayı, her hizmet için her bir hizmetin esnekliğiyle paylaşarak, kendi olayları için benzersiz özellikler sunar.

> [!NOTE]
> Kaynak günlükleri daha önce tanılama günlükleri olarak bilinirdi.

## <a name="collecting-resource-logs"></a>Kaynak günlüklerini toplama
Kaynak günlükleri desteklenen Azure kaynakları tarafından otomatik olarak oluşturulur, ancak bunları bir [Tanılama ayarı](diagnostic-settings.md)kullanarak yapılandırmadığınız müddetçe toplanmaz. Günlükleri aşağıdaki hedeflere iletmek için her bir Azure kaynağı için bir tanılama ayarı oluşturun:

| Hedef | Senaryo |
|:---|:---|:---|
| [Log Analytics çalışma alanı](resource-logs-collect-workspace.md) | Günlükleri diğer izleme verileriyle çözümleyin ve günlük sorguları ve günlük uyarıları gibi Azure Izleyici özelliklerinden yararlanın. |
| [Azure depolama alanı](resource-logs-collect-storage.md) | Denetim veya yedekleme için günlükleri arşivle. |
| [Olay Hub 'ı](resource-logs-stream-event-hubs.md) | Günlükleri üçüncü taraf günlüğe kaydetme ve telemetri sistemlerine akışa alma.  |

## <a name="compute-resources"></a>İşlem kaynakları
Kaynak günlükleri, Azure işlem kaynaklarındaki Konuk işletim sistemi düzeyi günlüklerinden farklıdır. İşlem kaynakları, olay günlükleri, syslog ve performans sayaçları gibi veriler de dahil olmak üzere Konuk IŞLETIM sisteminden günlükleri ve ölçümleri toplamak için bir aracı gerektirir. Azure sanal makineler ve [Log Analytics aracılarından](agents-overview.md#log-analytics-agent) günlük verilerinin yönlendirilmesini sağlamak Için [Tanılama uzantısını](agents-overview.md#azure-diagnostic-extension) kullanın ve Azure 'daki sanal makinelerden, diğer bulutlarda ve şirket içinde bir Log Analytics çalışma alanına Günlükler ve ölçümler toplayın. Ayrıntılar için bkz. [Azure izleyici için izleme verileri kaynakları](data-sources.md) .

## <a name="resource-logs-schema"></a>Kaynak günlükleri şeması
Kaynak günlükleri şeması ve kategorileri hakkında daha fazla bilgi için bkz. [kaynak günlüğü şeması](diagnostic-logs-schema.md). 

## <a name="next-steps"></a>Sonraki Adımlar

* Azure 'ı izlemek için kullanabileceğiniz [diğer Azure platformu günlükleri hakkında daha fazla bilgi edinin](platform-logs-overview.md) .
