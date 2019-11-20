---
title: Azure Güvenlik Duvarı günlük Analizi örnekleri
description: Azure Izleyici günlükleri, Azure Güvenlik duvarınızı analiz etmek için kullanılabilir. Örnek dosya, Azure Izleyici 'de Görünüm Tasarımcısı 'nda yerleşiktir.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: 714054b8e538b2fb1bfb3fb4f2293636802f3bc4
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74168897"
---
# <a name="azure-firewall-log-analytics-samples"></a>Azure Güvenlik Duvarı günlük Analizi örnekleri

Aşağıdaki Azure Izleyici günlükleri örnekleri, Azure Güvenlik Duvarı günlüklerinizi analiz etmek için kullanılabilir. Örnek dosya, Azure Izleyici 'de Görünüm Tasarımcısı 'nda yerleşik olarak bulunur, [Azure izleyici 'Deki Görünüm Tasarımcısı](https://docs.microsoft.com/azure/log-analytics/log-analytics-view-designer) , görünüm tasarım kavramı hakkında daha fazla bilgi içerir.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="azure-monitor-logs-view"></a>Azure Izleyici günlükleri görünümü

Örnek bir Azure Izleyici günlüğü görselleştirmesini yapılandırma hakkında daha fazla bilgiyi burada bulabilirsiniz. Örnek görselleştirmeyi [Azure-docs-JSON-Samples](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-firewall/AzureFirewall.omsview) deposundan indirebilirsiniz. En kolay yol, bu sayfadaki köprüye sağ tıklayıp *farklı kaydet* ' i seçip **AzureFirewall. omsview**gibi bir ad sağlamalıdır. 

Görünümü Log Analytics çalışma alanınıza eklemek için aşağıdaki adımları yürütün:

1. Azure portal Log Analytics çalışma alanını açın.
2. **Görünüm tasarımcısını** **genel**altında açın.
3. **İçeri Aktar**’a tıklayın.
4. Daha önce indirdiğiniz **AzureFirewall. omsview** dosyasına gözatıp seçin.
5. **Save (Kaydet)** düğmesine tıklayın.

Görünümün uygulama kuralı günlük verilerini nasıl aradığı aşağıda açıklanmıştır:

![Uygulama kuralı günlük verileri](./media/log-analytics-samples/azurefirewall-applicationrulelogstats.png)

Ağ kuralı günlük verileri için:

![Ağ kuralı günlük verileri]( ./media/log-analytics-samples/azurefirewall-networkrulelogstats.png)

Azure Güvenlik Duvarı, AzureDiagnostics ve **AzureFirewallApplicationRule** ya da **AzureFirewallNetworkRule**olarak category ile verileri günlüğe kaydeder. Ayrıntıları içeren veriler msg_s alanında depolanır. [Ayrıştırma](https://docs.microsoft.com/azure/kusto/query/parseoperator) işlecini kullanarak msg_s alanından çeşitli ilginç özellikleri ayıklayabiliriz. Aşağıdaki sorgular her iki kategorinin de bilgilerini ayıklar.

## <a name="application-rules-log-data-query"></a>Uygulama kuralları günlük verileri sorgusu

Aşağıdaki sorgu uygulama kuralı günlük verilerini ayrıştırır. Çeşitli açıklama satırlarında, sorgunun nasıl derlenildiği hakkında bazı yönergeler vardır:

```Kusto
AzureDiagnostics
| where Category == "AzureFirewallApplicationRule"
//using :int makes it easier to pars but later we'll convert to string as we're not interested to do mathematical functions on these fields
//this first parse statement is valid for all entries as they all start with this format
| parse msg_s with Protocol " request from " SourceIP ":" SourcePortInt:int " " TempDetails
//case 1: for records that end with: "was denied. Reason: SNI TLS extension was missing."
| parse TempDetails with "was " Action1 ". Reason: " Rule1
//case 2: for records that end with
//"to ocsp.digicert.com:80. Action: Allow. Rule Collection: RC1. Rule: Rule1"
//"to v10.vortex-win.data.microsoft.com:443. Action: Deny. No rule matched. Proceeding with default action"
| parse TempDetails with "to " FQDN ":" TargetPortInt:int ". Action: " Action2 "." *
//case 2a: for records that end with:
//"to ocsp.digicert.com:80. Action: Allow. Rule Collection: RC1. Rule: Rule1"
| parse TempDetails with * ". Rule Collection: " RuleCollection2a ". Rule:" Rule2a
//case 2b: for records that end with:
//for records that end with: "to v10.vortex-win.data.microsoft.com:443. Action: Deny. No rule matched. Proceeding with default action"
| parse TempDetails with * "Deny." RuleCollection2b ". Proceeding with" Rule2b
| extend 
SourcePort = tostring(SourcePortInt)
|extend
TargetPort = tostring(TargetPortInt)
| extend
//make sure we only have Allowed / Deny in the Action Field
Action1 = case(Action1 == "Deny","Deny","Unknown Action")
| extend
    Action = case(Action2 == "",Action1,Action2),
    Rule = case(Rule2a == "",case(Rule1 == "",case(Rule2b == "","N/A", Rule2b),Rule1),Rule2a), 
    RuleCollection = case(RuleCollection2b == "",case(RuleCollection2a == "","No rule matched",RuleCollection2a),RuleCollection2b),
    FQDN = case(FQDN == "", "N/A", FQDN),
    TargetPort = case(TargetPort == "", "N/A", TargetPort)
| project TimeGenerated, msg_s, Protocol, SourceIP, SourcePort, FQDN, TargetPort, Action ,RuleCollection, Rule
```

Daha daraltılmış biçimde aynı sorgu:

```Kusto
AzureDiagnostics
| where Category == "AzureFirewallApplicationRule"
| parse msg_s with Protocol " request from " SourceIP ":" SourcePortInt:int " " TempDetails
| parse TempDetails with "was " Action1 ". Reason: " Rule1
| parse TempDetails with "to " FQDN ":" TargetPortInt:int ". Action: " Action2 "." *
| parse TempDetails with * ". Rule Collection: " RuleCollection2a ". Rule:" Rule2a
| parse TempDetails with * "Deny." RuleCollection2b ". Proceeding with" Rule2b
| extend SourcePort = tostring(SourcePortInt)
| extend TargetPort = tostring(TargetPortInt)
| extend Action1 = case(Action1 == "Deny","Deny","Unknown Action")
| extend Action = case(Action2 == "",Action1,Action2),Rule = case(Rule2a == "", case(Rule1 == "",case(Rule2b == "","N/A", Rule2b),Rule1),Rule2a), 
RuleCollection = case(RuleCollection2b == "",case(RuleCollection2a == "","No rule matched",RuleCollection2a), RuleCollection2b),FQDN = case(FQDN == "", "N/A", FQDN),TargetPort = case(TargetPort == "", "N/A", TargetPort)
| project TimeGenerated, msg_s, Protocol, SourceIP, SourcePort, FQDN, TargetPort, Action ,RuleCollection, Rule
```

## <a name="network-rules-log-data-query"></a>Ağ kuralları günlük verileri sorgusu

Aşağıdaki sorgu, ağ kuralı günlük verilerini ayrıştırır. Çeşitli açıklama satırlarında, sorgunun nasıl derlenildiği hakkında bazı yönergeler vardır:

```Kusto
AzureDiagnostics
| where Category == "AzureFirewallNetworkRule"
//using :int makes it easier to pars but later we'll convert to string as we're not interested to do mathematical functions on these fields
//case 1: for records that look like this:
//TCP request from 10.0.2.4:51990 to 13.69.65.17:443. Action: Deny//Allow
//UDP request from 10.0.3.4:123 to 51.141.32.51:123. Action: Deny/Allow
//TCP request from 193.238.46.72:50522 to 40.119.154.83:3389 was DNAT'ed to 10.0.2.4:3389
| parse msg_s with Protocol " request from " SourceIP ":" SourcePortInt:int " to " TargetIP ":" TargetPortInt:int *
//case 1a: for regular network rules
//TCP request from 10.0.2.4:51990 to 13.69.65.17:443. Action: Deny//Allow
//UDP request from 10.0.3.4:123 to 51.141.32.51:123. Action: Deny/Allow
| parse msg_s with * ". Action: " Action1a
//case 1b: for NAT rules
//TCP request from 193.238.46.72:50522 to 40.119.154.83:3389 was DNAT'ed to 10.0.2.4:3389
| parse msg_s with * " was " Action1b " to " NatDestination
//case 2: for ICMP records
//ICMP request from 10.0.2.4 to 10.0.3.4. Action: Allow
| parse msg_s with Protocol2 " request from " SourceIP2 " to " TargetIP2 ". Action: " Action2
| extend
SourcePort = tostring(SourcePortInt),
TargetPort = tostring(TargetPortInt)
| extend 
    Action = case(Action1a == "", case(Action1b == "",Action2,Action1b), Action1a),
    Protocol = case(Protocol == "", Protocol2, Protocol),
    SourceIP = case(SourceIP == "", SourceIP2, SourceIP),
    TargetIP = case(TargetIP == "", TargetIP2, TargetIP),
    //ICMP records don't have port information
    SourcePort = case(SourcePort == "", "N/A", SourcePort),
    TargetPort = case(TargetPort == "", "N/A", TargetPort),
    //Regular network rules don't have a DNAT destination
    NatDestination = case(NatDestination == "", "N/A", NatDestination)
| project TimeGenerated, msg_s, Protocol, SourceIP,SourcePort,TargetIP,TargetPort,Action, NatDestination
```

Daha daraltılmış biçimde aynı sorgu:

```Kusto
AzureDiagnostics
| where Category == "AzureFirewallNetworkRule"
| parse msg_s with Protocol " request from " SourceIP ":" SourcePortInt:int " to " TargetIP ":" TargetPortInt:int *
| parse msg_s with * ". Action: " Action1a
| parse msg_s with * " was " Action1b " to " NatDestination
| parse msg_s with Protocol2 " request from " SourceIP2 " to " TargetIP2 ". Action: " Action2
| extend SourcePort = tostring(SourcePortInt),TargetPort = tostring(TargetPortInt)
| extend Action = case(Action1a == "", case(Action1b == "",Action2,Action1b), Action1a),Protocol = case(Protocol == "", Protocol2, Protocol),SourceIP = case(SourceIP == "", SourceIP2, SourceIP),TargetIP = case(TargetIP == "", TargetIP2, TargetIP),SourcePort = case(SourcePort == "", "N/A", SourcePort),TargetPort = case(TargetPort == "", "N/A", TargetPort),NatDestination = case(NatDestination == "", "N/A", NatDestination)
| project TimeGenerated, msg_s, Protocol, SourceIP,SourcePort,TargetIP,TargetPort,Action, NatDestination
```

## <a name="threat-intelligence-log-data-query"></a>Tehdit bilgileri günlük verileri sorgusu

Aşağıdaki sorgu, tehdit bilgileri kural günlüğü verilerini ayrıştırır:

```Kusto
AzureDiagnostics
| where OperationName  == "AzureFirewallThreatIntelLog"
| parse msg_s with Protocol " request from " SourceIP ":" SourcePortInt:int " to " TargetIP ":" TargetPortInt:int *
| parse msg_s with * ". Action: " Action "." Message
| parse msg_s with Protocol2 " request from " SourceIP2 " to " TargetIP2 ". Action: " Action2
| extend SourcePort = tostring(SourcePortInt),TargetPort = tostring(TargetPortInt)
| extend Protocol = case(Protocol == "", Protocol2, Protocol),SourceIP = case(SourceIP == "", SourceIP2, SourceIP),TargetIP = case(TargetIP == "", TargetIP2, TargetIP),SourcePort = case(SourcePort == "", "N/A", SourcePort),TargetPort = case(TargetPort == "", "N/A", TargetPort)
| sort by TimeGenerated desc | project TimeGenerated, msg_s, Protocol, SourceIP,SourcePort,TargetIP,TargetPort,Action,Message
```

## <a name="next-steps"></a>Sonraki adımlar

Azure Güvenlik Duvarı izleme ve tanılama hakkında bilgi edinmek için bkz. [öğretici: Azure Güvenlik Duvarı günlüklerini ve ölçümlerini izleme](tutorial-diagnostics.md).
