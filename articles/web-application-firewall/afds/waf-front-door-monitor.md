---
title: Azure Web uygulaması güvenlik duvarı izleme ve günlüğe kaydetme
description: Frontkapısı izleme ve günlüğe kaydetme ile Web uygulaması güvenlik duvarını (WAF) öğrenin
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: 0c705139c082f13f40362e598f0fda9ba0a128a5
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512489"
---
# <a name="azure-web-application-firewall-monitoring-and-logging"></a>Azure Web uygulaması güvenlik duvarı izleme ve günlüğe kaydetme 

Azure Web uygulaması güvenlik duvarı (WAF) izleme ve günlüğe kaydetme, Azure Izleyici ve Azure Izleyici günlükleri ile günlüğe kaydetme ve tümleştirme aracılığıyla sağlanır.

## <a name="azure-monitor"></a>Azure İzleyici

Frontkapısı günlüğü olan WAF, [Azure izleyici](../../azure-monitor/overview.md)ile tümleşiktir. Azure Izleyici, WAF uyarıları ve günlükleri dahil olmak üzere tanılama bilgilerini izlemenizi sağlar. WAF izlemesini, portaldaki ön kapı kaynağı içinde, **Tanılama** sekmesi altında veya doğrudan Azure İzleyici hizmeti aracılığıyla yapılandırabilirsiniz.

Azure portal, ön kapı kaynak türü ' ne gidin. Sol taraftaki **izleme**/**ölçümleri** sekmesinden, WAF kurallarıyla eşleşen Isteklerin sayısını Izlemek Için **webapplicationfirewallrequestcount** ekleyebilirsiniz. Özel filtreler, eylem türleri ve kural adlarına göre oluşturulabilir.

![Wafmetrik](../media/waf-frontdoor-monitor/waf-frontdoor-metrics.png)

## <a name="logs-and-diagnostics"></a>Günlükler ve Tanılamalar

Ön kapılı WAF algıladığı her tehdit için ayrıntılı raporlama sağlar. Günlük kaydı Azure Tanılama günlükleri ile tümleştirilir ve uyarılar json biçiminde kaydedilir. Bu Günlükler, [Azure izleyici günlükleri](../../azure-monitor/insights/azure-networking-analytics.md)ile tümleştirilebilir.

![WAFDiag](../media/waf-frontdoor-monitor/waf-frontdoor-diagnostics.png)

FrontdoorAccessLog, müşteri arka uçlarına iletilen tüm istekleri günlüğe kaydeder. FrontdoorWebApplicationFirewallLog, bir WAF kuralıyla eşleşen tüm istekleri günlüğe kaydeder.

Aşağıdaki örnek sorgu, engellenen isteklerde WAF günlüklerini edinir:

``` WAFlogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorWebApplicationFirewallLog"
| where action_s == "Block"

```

Aşağıdaki örnek sorgu AccessLogs girdilerini edinir:

``` AccessLogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorAccessLog"


```

## <a name="next-steps"></a>Sonraki adımlar

- [Ön kapı](../../frontdoor/front-door-overview.md)hakkında daha fazla bilgi edinin.

