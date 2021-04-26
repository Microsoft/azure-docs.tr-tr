---
title: Azure platformu günlüklerine genel bakış | Microsoft Docs
description: Azure Izleyici 'deki günlüklere genel bakış, Azure kaynağı 'nın çalışması hakkında zengin ve sık veriler sağlar.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: bwren
ms.openlocfilehash: f2b22746b7a78349580220f4aa7579876fd21c33
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106074599"
---
# <a name="overview-of-azure-platform-logs"></a>Azure platform günlüklerine genel bakış
Platform günlükleri, Azure kaynakları ve bağımlı oldukları Azure platformu için ayrıntılı tanılama ve denetim bilgileri sağlar. Bunlar otomatik olarak oluşturulur, ancak korunacak bir veya daha fazla hedefe iletilmek üzere belirli platform günlüklerinin yapılandırılması gerekir. Bu makalede, sağladığı bilgiler ve bunları toplama ve analiz için nasıl yapılandırabileceğiniz dahil olmak üzere platform günlüklerine bir genel bakış sunulmaktadır.

## <a name="types-of-platform-logs"></a>Platform günlüklerinin türleri
Aşağıdaki tabloda, Azure 'un farklı katmanlarında kullanılabilen belirli platform günlükleri listelenmiştir.

| Günlük | Katman | Açıklama |
|:---|:---|:---|
| [Kaynak günlükleri](./resource-logs.md) | Azure Kaynakları | Bir Azure kaynağı içinde ( *veri düzlemi*) gerçekleştirilen işlemlere Öngörüler sağlama (örneğin, bir Key Vault gizli anahtar alma veya bir veritabanına istek yapma). Kaynak günlüklerinin içeriği, Azure hizmeti ve kaynak türüne göre farklılık gösterir.<br><br>*Kaynak günlüklerine daha önce tanılama günlükleri adı verilir.*  |
| [Etkinlik günlüğü](../essentials/activity-log.md) | Azure Aboneliği | Hizmet durumu olaylarında güncelleştirmelere ek olarak, dışarıdaki (*Yönetim düzlemi*) abonelikteki her bir Azure kaynağında bulunan işlemlere ilişkin öngörüler sağlar. Aboneliğinizdeki kaynaklarda _hangi_ yazma IŞLEMLERININ (put, Post _, silme_) alındığını _belirlemek için etkinlik_ günlüğünü kullanın. Her bir Azure aboneliği için tek bir etkinlik günlüğü vardır. |
| [Azure Active Directory günlükleri](../../active-directory/reports-monitoring/overview-reports.md) | Azure Kiracısı |  Belirli bir kiracının Azure Active Directory yapılan değişikliklerin, oturum açma etkinliğinin ve denetim izinin geçmişini içerir.   |

> [!NOTE]
> Azure etkinlik günlüğü öncelikle Azure Resource Manager oluşan etkinliklere yöneliktir. Klasik/RDFE modelini kullanan kaynakları izlemez. Bazı klasik kaynak türleri Azure Resource Manager bir proxy kaynak sağlayıcısına sahiptir (örneğin, Microsoft. ClassicCompute). Bu proxy kaynak sağlayıcılarını kullanarak Azure Resource Manager aracılığıyla klasik bir kaynak türüyle etkileşim ediyorsanız, işlemler etkinlik günlüğünde görüntülenir. Azure Resource Manager proxy 'leri dışında klasik bir kaynak türüyle etkileşime sahipseniz, eylemleriniz yalnızca Işlem günlüğüne kaydedilir. Işlem günlüğü, portalın ayrı bir bölümünde gözatılabilir.

![Platform günlüklerine genel bakış](media/platform-logs-overview/logs-overview.png)




## <a name="viewing-platform-logs"></a>Platform günlüklerini görüntüleme
Farklı Azure platformu günlüklerini görüntülemek ve analiz etmek için farklı seçenekler vardır.

- Azure portal etkinlik günlüğünü görüntüleyin ve PowerShell ve CLı 'deki olaylara erişin. Ayrıntılar için bkz. [etkinlik günlüğünü görüntüleme](../essentials/activity-log.md#view-the-activity-log) . 
- Azure Active Directory güvenlik ve etkinlik raporlarını Azure portal görüntüleyin. Bkz. [Azure Active Directory raporları nedir?](../../active-directory/reports-monitoring/overview-reports.md)  daha fazla bilgi için.
- Kaynak günlükleri desteklenen Azure kaynakları tarafından otomatik olarak oluşturulur, ancak bunları bir [hedefe](#destinations)göndermediğiniz takdirde görüntülenmek üzere kullanılamaz. 

## <a name="destinations"></a>Hedefler
İzleme gereksinimlerinize bağlı olarak, aşağıdaki tabloda yer alan hedeflere bir veya daha fazlasına platform günlükleri gönderebilirsiniz. [Tanılama ayarı oluşturarak](../essentials/diagnostic-settings.md)platform günlükleri için hedefleri yapılandırın.

| Hedef | Description |
|:---|:---|
| Log Analytics çalışma alanı | Tüm Azure kaynaklarınızın günlüklerini birlikte çözümleyin ve [günlük sorguları](../logs/log-query-overview.md) ve [günlük uyarıları](../alerts/alerts-log.md)da dahil olmak üzere [Azure izleyici günlüklerine](../logs/data-platform-logs.md) sunulan tüm özelliklerden yararlanın. Günlük sorgusunun sonuçlarını bir Azure panosuna sabitleyin veya etkileşimli bir raporun parçası olarak çalışma kitabına dahil edin. | 
| Olay hub'ı | Platform günlüğü verilerini Azure dışında, örneğin bir üçüncü taraf SıEM veya özel telemetri platformuna gönderin. |
| Azure depolama | Denetim veya yedekleme için günlükleri arşivle. |

- Etkinlik günlüğü veya kaynak günlükleri için bir tanılama ayarı oluşturma hakkında ayrıntılı bilgi için bkz. [Platform günlüklerini ve ölçümlerini farklı hedeflere göndermek için Tanılama ayarları oluşturma](../essentials/diagnostic-settings.md). 
- Azure Active Directory günlükleri için bir tanılama ayarı oluşturma hakkında ayrıntılı bilgi için aşağıdaki makalelere bakın.
  - [Azure AD günlüklerini Azure Izleyici günlükleriyle tümleştirme](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)
  - [Öğretici: Azure Olay Hub 'ına akış Azure Active Directory günlükleri](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)
  - [Öğretici: Azure AD günlüklerini bir Azure depolama hesabında arşivleme](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md)



## <a name="next-steps"></a>Sonraki adımlar

* [Etkinlik günlüğü hakkında daha fazla ayrıntı okuyun](../essentials/activity-log.md)
* [Kaynak günlükleri hakkında daha fazla bilgi edinin](./resource-logs.md)
