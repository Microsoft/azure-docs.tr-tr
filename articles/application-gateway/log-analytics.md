---
title: Azure Log Analytics kullanarak WAF günlüklerini inceleyin
titleSuffix: Azure Application Gateway
description: Bu makalede, Application Gateway Web uygulaması güvenlik duvarı günlüklerini incelemek için Azure Log Analytics nasıl kullanabileceğiniz gösterilmektedir
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: 9fe4462a71852e5f66268f798f6f0418f2dd39c4
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74048118"
---
# <a name="use-log-analytics-to-examine-application-gateway-web-application-firewall-logs"></a>Log Analytics kullanarak Application Gateway Web Uygulaması Güvenlik Duvarı Günlüklerini inceleme

Application Gateway WAF çalışmaya başladıktan sonra, her istekte ne olduğunu incelemek için günlükleri etkinleştirebilirsiniz. Güvenlik Duvarı günlükleri WAF 'nin değerlendirme, eşleştirme ve engelleme hakkında fikir verir. Log Analytics sayesinde, daha da fazla öngörü sağlamak için güvenlik duvarı günlüklerinin içindeki verileri inceleyebilirsiniz. Log Analytics çalışma alanı oluşturma hakkında daha fazla bilgi için, bkz. [Azure portal Log Analytics çalışma alanı oluşturma](../azure-monitor/learn/quick-create-workspace.md). Günlük sorguları hakkında daha fazla bilgi için bkz. [Azure izleyici 'de günlük sorgularına genel bakış](../azure-monitor/log-query/log-query-overview.md).

## <a name="import-waf-logs"></a>WAF günlüklerini içeri aktar

Güvenlik Duvarı günlüklerinizi Log Analytics içine aktarmak için [arka uç sistem durumu, tanılama günlükleri ve Application Gateway ölçümler](application-gateway-diagnostics.md#diagnostic-logging)bölümüne bakın. Log Analytics çalışma alanınızda güvenlik duvarı günlüklerine sahip olduğunuzda, verileri görüntüleyebilir, sorguları yazabilir, görselleştirmeler oluşturabilir ve bunları Portal panonuza ekleyebilirsiniz.

## <a name="explore-data-with-examples"></a>Örneklerle verileri araştırma

Güvenlik Duvarı günlüğündeki ham verileri görüntülemek için aşağıdaki sorguyu çalıştırabilirsiniz:

```
AzureDiagnostics 
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
```

Bu, aşağıdaki sorguya benzer şekilde görünür:

![Log Analytics sorgu](media/log-analytics/log-query.png)

Verilerin detayına gidebilir, grafikleri çizdirebilirsiniz veya buradan görselleştirme oluşturabilirsiniz. Başlangıç noktası olarak aşağıdaki sorgulara bakın:

### <a name="matchedblocked-requests-by-ip"></a>IP ile eşleşen/Engellenen istekler

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by clientIp_s, bin(TimeGenerated, 1m)
| render timechart
```

### <a name="matchedblocked-requests-by-uri"></a>URI 'ye göre eşleşen/Engellenen istekler

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by requestUri_s, bin(TimeGenerated, 1m)
| render timechart
```

### <a name="top-matched-rules"></a>En çok eşleşen kurallar

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by ruleId_s, bin(TimeGenerated, 1m)
| where count_ > 10
| render timechart
```

### <a name="top-five-matched-rule-groups"></a>İlk beş eşleşen kural grubu

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize Count=count() by details_file_s, action_s
| top 5 by Count desc
| render piechart
```

## <a name="add-to-your-dashboard"></a>Panonuza ekleme

Bir sorgu oluşturduktan sonra panonuza ekleyebilirsiniz.  Log Analytics çalışma alanının sağ üst kısmında **panoya sabitle ' yi** seçin. Önceki dört sorgu, örnek bir panoya sabitlenmişse, bir bakışta görebileceğiniz veriler şunlardır:

![Pano](media/log-analytics/dashboard.png)

## <a name="next-steps"></a>Sonraki adımlar

[Application Gateway için arka uç sistem durumu, tanılama günlükleri ve ölçümler](application-gateway-diagnostics.md)
