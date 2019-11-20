---
title: Azure platformu günlüklerine genel bakış | Microsoft Docs
description: Azure kaynağının çalışması hakkında zengin ve sık veriler sağlayan Azure tanılama günlüklerine genel bakış.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: c1602b9beb5e667caf1688901c7ae4e67e5f5839
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262576"
---
# <a name="overview-of-azure-platform-logs"></a>Azure platformu günlüklerine genel bakış
Platform günlükleri, Azure kaynakları ve bağımlı oldukları Azure platformu için ayrıntılı tanılama ve denetim bilgileri sağlar. Bunlar otomatik olarak oluşturulur, ancak korunacak bir veya daha fazla hedefe iletilmek üzere belirli platform günlüklerinin yapılandırılması gerekir. Bu makalede, sağladığı bilgiler ve bunları toplama ve analiz için nasıl yapılandırabileceğiniz dahil olmak üzere platform günlüklerine bir genel bakış sunulmaktadır.

## <a name="types-of-platform-logs"></a>Platform günlüklerinin türleri
Aşağıdaki tabloda, Azure 'un farklı katmanlarında kullanılabilen belirli platform günlükleri listelenmiştir.

| Katman | Günlükler | Açıklama |
|:---|:---|:---|
| Azure kaynakları | [Kaynak günlükleri](resource-logs-overview.md) | Bir Azure kaynağı içinde ( *veri düzlemi*) gerçekleştirilen işlemlere Öngörüler sağlama (örneğin, bir Key Vault gizli anahtar alma veya bir veritabanına istek yapma). Kaynak günlüklerinin içeriği, Azure hizmeti ve kaynak türüne göre farklılık gösterir.<br>*Kaynak günlüklerine daha önce tanılama günlükleri adı verilir.*  |
| Azure Aboneliği | [Etkinlik Günlüğü](activity-logs-overview.md) | Hizmet durumu olaylarında güncelleştirmelere ek olarak, dışarıdaki (*Yönetim düzlemi*) abonelikteki her bir Azure kaynağında bulunan işlemlere ilişkin öngörüler sağlar. Her bir Azure aboneliği için tek bir etkinlik günlüğü vardır.   |
| Azure kiracısı | [Azure Active Directory günlükleri](../../active-directory/reports-monitoring/overview-reports.md)  | Belirli bir kiracının Azure Active Directory yapılan değişikliklerin, oturum açma etkinliğinin ve denetim izinin geçmişini içerir.   |


![Platform günlüklerine genel bakış](media/platform-logs-overview/logs-overview.png)

## <a name="viewing-platform-logs"></a>Platform günlüklerini görüntüleme
[Etkinlik günlüğünü](activity-log-view.md) ve [Azure Active Directory günlüklerini](../../active-directory/reports-monitoring/overview-reports.md) Azure Portal görüntüleyebilirsiniz. Kaynak günlüklerini görüntülemek için bir [hedefe](#destinations) göndermeniz gerekir.


## <a name="destinations"></a>Hedefler
İzleme gereksinimlerinize bağlı olarak, aşağıdaki tabloda yer alan hedeflere bir veya daha fazlasına platform günlükleri gönderebilirsiniz. 

| Destination | Senaryo | Başvurular |
|:---|:---|:---|:---|
| Log Analytics çalışma alanı | Günlükleri diğer izleme verileriyle çözümleyin ve günlük sorguları ve uyarılar gibi Azure Izleyici özelliklerinden yararlanın. | [Kaynak günlükleri](resource-logs-collect-storage.md)<br>[Etkinlik Günlüğü](activity-log-collect.md)<br>[Azure etkinlik dizini günlükleri](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) |
| Azure Storage | Denetim, statik analiz veya yedekleme için günlükleri arşivleyin. |[Kaynak günlükleri](archive-diagnostic-logs.md)<br>[Etkinlik Günlüğü](activity-log-export.md)<br>[Azure etkinlik dizini günlükleri](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) |
| Olay hub'ı | Günlükleri üçüncü taraf günlüğe kaydetme ve telemetri sistemlerine akışa alma.  |[Kaynak günlükleri](resource-logs-stream-event-hubs.md)<br>[Etkinlik Günlüğü](activity-log-export.md)<br>[Azure etkinlik dizini günlükleri](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) |


## <a name="diagnostic-settings-and-log-profiles"></a>Tanılama ayarları ve günlük profilleri
[Bir tanılama ayarı oluşturarak](diagnostic-settings.md)kaynak günlükleri ve Azure Active Directory günlükleri için hedefler yapılandırın. [Bir günlük profili oluşturarak](activity-log-export.md) veya [bir Log Analytics çalışma alanına bağlayarak](activity-log-collect.md)etkinlik günlüğü için hedefleri yapılandırın.

Tanılama ayarı ve günlük profili aşağıdakileri tanımlar:

- Seçili günlükleri ve ölçümleri göndermek için bir veya daha fazla hedef.
- Kaynaktaki hangi günlük kategorilerinin ve ölçümlerin hedefe gönderildiği.
- Bir depolama hesabı hedef olarak seçilirse, her günlük kategorisinin ne kadar süreyle korunması gerekir.



## <a name="next-steps"></a>Sonraki adımlar

* [Etkinlik günlüğü hakkında daha fazla bilgi edinin](activity-logs-overview.md)
* [Kaynak günlükleri hakkında daha fazla bilgi edinin](resource-logs-overview.md)
* [Farklı Azure hizmetleri için kaynak günlüğü şemasını görüntüleme](diagnostic-logs-schema.md)
