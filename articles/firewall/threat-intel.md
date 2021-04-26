---
title: Azure Güvenlik Duvarı tehdit zekası tabanlı filtreleme
description: Güvenlik duvarınızda tehdit analizi tabanlı filtrelemeyi etkinleştirerek bilinen kötü amaçlı IP adreslerinden ve etki alanlarından gelen/giden trafiği reddedebilir ve uyarı oluşturulmasını sağlayabilirsiniz.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 05/12/2020
ms.author: victorh
ms.openlocfilehash: 77e36d5ab98f1177b5a0b89ee10b3e6bd22560c2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791468"
---
# <a name="azure-firewall-threat-intelligence-based-filtering"></a>Azure Güvenlik Duvarı tehdit zekası tabanlı filtreleme

Güvenlik duvarınızda tehdit analizi tabanlı filtrelemeyi etkinleştirerek bilinen kötü amaçlı IP adreslerinden ve etki alanlarından gelen/giden trafiği reddedebilir ve uyarı oluşturulmasını sağlayabilirsiniz. IP adresleri ve etki alanları, Microsoft Tehdit Analizi akışından alınır. [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence) , Microsoft Threat Intelligence 'ı güçlendirir ve Azure Güvenlik Merkezi dahil birden çok hizmet tarafından kullanılır.<br>
<br>

:::image type="content" source="media/threat-intel/firewall-threat.png" alt-text="Güvenlik Duvarı tehdit bilgileri" border="false":::

Tehdit zekası tabanlı filtrelemeyi etkinleştirdiyseniz, ilişkili kurallar NAT kurallarından, ağ kurallarından veya uygulama kurallarından önce işlenir.

Bir kural tetiklendiğinde yalnızca bir uyarıyı günlüğe kaydedebilir veya uyarı ve reddetme modunu seçebilirsiniz.

Varsayılan olarak, tehdit zekası tabanlı filtreleme, uyarı modunda etkindir. Bu özelliği kapatamaz veya Portal arabirimi bölgenizde kullanılabilir hale gelene kadar modu değiştiremezsiniz.

:::image type="content" source="media/threat-intel/threat-intel-ui.png" alt-text="Tehdit zekası tabanlı filtreleme portalı arabirimi":::

## <a name="logs"></a>Günlükler

Aşağıdaki günlük alıntısı tetiklenen bir kural gösterir:

```
{
    "category": "AzureFirewallNetworkRule",
    "time": "2018-04-16T23:45:04.8295030Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallThreatIntelLog",
    "properties": {
         "msg": "HTTP request from 10.0.0.5:54074 to somemaliciousdomain.com:80. Action: Alert. ThreatIntel: Bot Networks"
    }
}
```

## <a name="testing"></a>Test Etme

- **Giden sınama** -giden trafik uyarıları nadir bir oluşum olmalıdır, çünkü ortamınız tehlikede olduğu anlamına gelir. Giden uyarıların test sağlanmasına yardımcı olmak için bir uyarıyı tetikleyen bir test FQDN 'SI oluşturulmuştur. Giden testleriniz için **testmaliciousdomain.eastus.cloudapp.Azure.com** kullanın.

- **Gelen sınama** -güvenlik duvarında DNAT kuralları yapılandırılırsa, gelen trafikte uyarıları görmeyi bekleyebilir. Bu, DNAT kuralında yalnızca belirli kaynaklara izin verildiğinden ve trafik başka bir şekilde reddedilse bile geçerlidir. Azure Güvenlik Duvarı tüm bilinen bağlantı noktası tarayıcıları üzerinde uyarı vermez; yalnızca kötü amaçlı etkinlikler için de bilinen tarayıcılarda.

## <a name="next-steps"></a>Sonraki adımlar

- Bkz. [Azure Güvenlik duvarı Log Analytics örnekleri](./firewall-workbook.md)
- [Azure Güvenlik duvarını dağıtmayı ve yapılandırmayı](tutorial-firewall-deploy-portal.md) öğrenin
- [Microsoft Güvenlik Zekası raporunu](https://www.microsoft.com/en-us/security/operations/security-intelligence-report) gözden geçirin