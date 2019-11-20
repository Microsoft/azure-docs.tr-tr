---
title: Azure Izleyici 'de veri konumlarını izleme | Microsoft Docs
description: Azure Izleyici veri platformu dahil olmak üzere Azure 'da izlenen verilerin depolandığı farklı konumları açıklar.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/21/2019
ms.openlocfilehash: 2ec1f0161713c7014f71fb3eaee31b0abdf52902
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932552"
---
# <a name="monitoring-data-locations-in-azure-monitor"></a>Azure Izleyici 'de veri konumlarını izleme

Azure Izleyici, [Azure izleyici veri platformunda](data-platform.md)açıklandığı şekilde [günlüklerin](data-platform-logs.md) ve [ölçümlerin](data-platform-metrics.md) [veri platformunu](data-platform.md) temel alır. Azure kaynaklarından verileri izleme, Azure Izleyici 'ye kopyalanmadan veya ek senaryoları destekleyecek şekilde diğer konumlara yazılabilir. 

## <a name="monitoring-data-locations"></a>İzleme veri konumları

Aşağıdaki tabloda, Azure 'daki izleme verilerinin gönderildiği ve buna erişmek için farklı yöntemlerin bulunduğu farklı konumlar tanımlanmaktadır.

| Konum | Açıklama | Erişim yöntemleri |
|:---|:---|:---|:--|
| Azure Izleyici ölçümleri | Zaman damgalı verileri çözümlemek için en iyi duruma getirilmiş zaman serisi veritabanı. | [Ölçüm Gezgini](metrics-getting-started.md)<br>[Azure Izleyici ölçümleri API 'SI](/rest/api/monitor/metrics) |
| Azure İzleyici Günlükleri    | Güçlü bir analiz altyapısı ve zengin sorgu dili sağlayan Azure Veri Gezgini temel alan Log Analytics çalışma alanı. | [Log Analytics](../log-query/portals.md)<br>[Log Analytics API 'SI](https://dev.loganalytics.io/)<br>[Application Insights API 'SI](https://dev.applicationinsights.io/reference/get-query) |
| Etkinlik günlüğü | Etkinlik günlüğünden alınan veriler, diğer verilerle analiz etmek üzere Azure Izleyici günlüklerine gönderildiğinde en yararlı seçenektir, ancak aynı zamanda kendi Azure portal doğrudan görüntülenebilmesi için kendi üzerinde de toplanır. | [Azure portalda](activity-log-view.md#azure-portal)<br>[Azure Izleme olayları API 'SI](/rest/api/monitor/eventcategories) |
| Azure Depolama | Bazı veri kaynakları doğrudan Azure depolama 'ya yazılır ve verileri günlüklere taşımak için yapılandırma gerektirir. Ayrıca, arşivleme ve dış sistemlerle tümleştirme için Azure depolama 'ya veri gönderebilirsiniz.  | [Depolama Analizi](/rest/api/storageservices/storage-analytics)<br>[Sunucu Gezgini](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage)<br>[Depolama Gezgini](/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) |
| Event Hubs | Verileri diğer konumlara aktarmak için Azure Event Hubs 'a gönderin. | [Depolamaya yakala](../../event-hubs/event-hubs-capture-overview.md)  |
| VM'ler için Azure İzleyici | VM'ler için Azure İzleyici iş yükü sistem durumu verilerini, izleme deneyimi tarafından Azure portal tarafından kullanılan özel bir konumda depolar. | [Azure portalda](../insights/vminsights-overview.md)<br>[İş yükü İzleyicisi REST API](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components)<br>[Azure Kaynak durumu REST API](https://docs.microsoft.com/rest/api/resourcehealth/)  |
| Uyarılar | Azure Izleyici tarafından oluşturulan uyarılar. | [Azure portalda](alerts-managing-alert-instances.md)<br>[Uyarı yönetimi REST API](https://docs.microsoft.com/rest/api/monitor/alertsmanagement/alerts) |



## <a name="next-steps"></a>Sonraki adımlar

- [Azure izleyici tarafından toplanan farklı izleme verileri](data-sources.md)kaynaklarına bakın.
- [Azure izleyici tarafından toplanan izleme verilerinin türleri](data-platform.md) ve bu verilerin nasıl görüntüleneceği ve çözümleneceği hakkında bilgi edinin.
