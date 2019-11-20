---
title: CLı kullanarak kuralları özelleştirme-Azure Web uygulaması güvenlik duvarı
description: Bu makalede, Azure CLı ile Application Gateway 'de Web uygulaması Güvenlik Duvarı kurallarının nasıl özelleştirileceği hakkında bilgi verilmektedir.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: victorh
ms.topic: article
ms.openlocfilehash: 8e8aaa9458619bc937c5bb11c450f3197b92f451
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74048524"
---
# <a name="customize-web-application-firewall-rules-using-the-azure-cli"></a>Azure CLı kullanarak Web uygulaması güvenlik duvarı kurallarını özelleştirme

Azure Application Gateway Web uygulaması güvenlik duvarı (WAF), Web uygulamaları için koruma sağlar. Bu korumalar, Open Web Application Security Project (OWASP) çekirdek kural kümesi (CCR) tarafından sağlanır. Bazı kurallar yanlış pozitif sonuçlar oluşmasına neden olabilir ve gerçek trafiği engelleyebilir. Bu nedenle, Application Gateway kural gruplarını ve kuralları özelleştirme yeteneği sağlar. Belirli kural grupları ve kuralları hakkında daha fazla bilgi için bkz. [Web uygulaması güvenlik duvarı liste grupları ve kuralları listesi](application-gateway-crs-rulegroups-rules.md).

## <a name="view-rule-groups-and-rules"></a>Kural gruplarını ve kuralları görüntüle

Aşağıdaki kod örnekleri, yapılandırılabilir kuralların ve kural gruplarının nasıl görüntüleneceğini gösterir.

### <a name="view-rule-groups"></a>Kural gruplarını görüntüle

Aşağıdaki örnek, kural gruplarının nasıl görüntüleneceğini göstermektedir:

```azurecli-interactive
az network application-gateway waf-config list-rule-sets --type OWASP
```

Aşağıdaki çıktı, yukarıdaki örnekten kesilen bir yanıt örneğidir:

```json
[
  {
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/applicationGatewayAvailableWafRuleSets/",
    "location": null,
    "name": "OWASP_3.0",
    "provisioningState": "Succeeded",
    "resourceGroup": "",
    "ruleGroups": [
      {
        "description": "",
        "ruleGroupName": "REQUEST-910-IP-REPUTATION",
        "rules": null
      },
      ...
    ],
    "ruleSetType": "OWASP",
    "ruleSetVersion": "3.0",
    "tags": null,
    "type": "Microsoft.Network/applicationGatewayAvailableWafRuleSets"
  },
  {
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/applicationGatewayAvailableWafRuleSets/",
    "location": null,
    "name": "OWASP_2.2.9",
    "provisioningState": "Succeeded",
    "resourceGroup": "",
   "ruleGroups": [
      {
        "description": "",
        "ruleGroupName": "crs_20_protocol_violations",
        "rules": null
      },
      ...
    ],
    "ruleSetType": "OWASP",
    "ruleSetVersion": "2.2.9",
    "tags": null,
    "type": "Microsoft.Network/applicationGatewayAvailableWafRuleSets"
  }
]
```

### <a name="view-rules-in-a-rule-group"></a>Kuralları bir kural grubunda görüntüleme

Aşağıdaki örnek, kuralların belirtilen bir kural grubunda nasıl görüntüleneceğini göstermektedir:

```azurecli-interactive
az network application-gateway waf-config list-rule-sets --group "REQUEST-910-IP-REPUTATION"
```

Aşağıdaki çıktı, yukarıdaki örnekten kesilen bir yanıt örneğidir:

```json
[
  {
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/applicationGatewayAvailableWafRuleSets/",
    "location": null,
    "name": "OWASP_3.0",
    "provisioningState": "Succeeded",
    "resourceGroup": "",
    "ruleGroups": [
      {
        "description": "",
        "ruleGroupName": "REQUEST-910-IP-REPUTATION",
        "rules": [
          {
            "description": "Rule 910011",
            "ruleId": 910011
          },
          ...
        ]
      }
    ],
    "ruleSetType": "OWASP",
    "ruleSetVersion": "3.0",
    "tags": null,
    "type": "Microsoft.Network/applicationGatewayAvailableWafRuleSets"
  }
]
```

## <a name="disable-rules"></a>Kuralları devre dışı bırak

Aşağıdaki örnek, bir uygulama ağ geçidinde kuralları `910018` ve `910017` devre dışı bırakır:

```azurecli-interactive
az network application-gateway waf-config set --resource-group AdatumAppGatewayRG --gateway-name AdatumAppGateway --enabled true --rule-set-version 3.0 --disabled-rules 910018 910017
```

## <a name="mandatory-rules"></a>Zorunlu kurallar

Aşağıdaki liste, WAF 'nin önleme modundayken isteği engellemesini sağlayan koşullar içerir (algılama modunda özel durumlar olarak günlüğe kaydedilir). Bunlar yapılandırılamaz veya devre dışı bırakılamaz:

* Gövde incelemesi devre dışı bırakılmadığı takdirde istek gövdesi ayrıştırılamadı, istek engellenmiyor (XML, JSON, form verileri)
* İstek gövdesi (dosya olmadan) veri uzunluğu yapılandırılan sınırdan daha büyük
* İstek gövdesi (dosyalar dahil) sınırdan daha büyük
* WAF altyapısında bir iç hata oluştu

X + x 'e özgü:

* Gelen anomali puanı eşiği aştı

## <a name="next-steps"></a>Sonraki adımlar

Devre dışı kurallarınızı yapılandırdıktan sonra, WAF günlüklerinizi görüntülemeyi öğrenebilirsiniz. Daha fazla bilgi için bkz. [tanılama Application Gateway](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging).

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
