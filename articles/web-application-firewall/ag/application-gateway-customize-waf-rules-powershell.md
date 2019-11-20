---
title: PowerShell kullanarak kuralları özelleştirme
titleSuffix: Azure Web Application Firewall
description: Bu makalede, PowerShell ile Application Gateway Web uygulaması güvenlik duvarı kurallarını özelleştirme hakkında bilgi sağlanır.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: victorh
ms.topic: article
ms.openlocfilehash: 55eea15da8c3a10b0421ff1576082d6b42fc7c56
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74048519"
---
# <a name="customize-web-application-firewall-rules-using-powershell"></a>PowerShell kullanarak Web uygulaması güvenlik duvarı kurallarını özelleştirme

Azure Application Gateway Web uygulaması güvenlik duvarı (WAF), Web uygulamaları için koruma sağlar. Bu korumalar, Open Web Application Security Project (OWASP) çekirdek kural kümesi (CCR) tarafından sağlanır. Bazı kurallar yanlış pozitif sonuçlar oluşmasına neden olabilir ve gerçek trafiği engelleyebilir. Bu nedenle, Application Gateway kural gruplarını ve kuralları özelleştirme yeteneği sağlar. Belirli kural grupları ve kuralları hakkında daha fazla bilgi için bkz. [Web uygulaması güvenlik duvarı liste grupları ve kuralları listesi](application-gateway-crs-rulegroups-rules.md).

## <a name="view-rule-groups-and-rules"></a>Kural gruplarını ve kuralları görüntüle

Aşağıdaki kod örnekleri, bir WAF özellikli uygulama ağ geçidinde yapılandırılabilen kuralların ve kural gruplarının nasıl görüntüleneceğini gösterir.

### <a name="view-rule-groups"></a>Kural gruplarını görüntüle

Aşağıdaki örnek, kural gruplarının nasıl görüntüleneceğini göstermektedir:

```powershell
Get-AzApplicationGatewayAvailableWafRuleSets
```

Aşağıdaki çıktı, yukarıdaki örnekten kesilen bir yanıt örneğidir:

```
OWASP (Ver. 3.0):

    General:
        Description:

        Rules:
            RuleId     Description
            ------     -----------
            200004     Possible Multipart Unmatched Boundary.

    REQUEST-911-METHOD-ENFORCEMENT:
        Description:

        Rules:
            RuleId     Description
            ------     -----------
            911011     Rule 911011
            911012     Rule 911012
            911100     Method is not allowed by policy
            911013     Rule 911013
            911014     Rule 911014
            911015     Rule 911015
            911016     Rule 911016
            911017     Rule 911017
            911018     Rule 911018

    REQUEST-913-SCANNER-DETECTION:
        Description:

        Rules:
            RuleId     Description
            ------     -----------
            913011     Rule 913011
            913012     Rule 913012
            913100     Found User-Agent associated with security scanner
            913110     Found request header associated with security scanner
            913120     Found request filename/argument associated with security scanner
            913013     Rule 913013
            913014     Rule 913014
            913101     Found User-Agent associated with scripting/generic HTTP client
            913102     Found User-Agent associated with web crawler/bot
            913015     Rule 913015
            913016     Rule 913016
            913017     Rule 913017
            913018     Rule 913018

            ...        ...
```

## <a name="disable-rules"></a>Kuralları devre dışı bırak

Aşağıdaki örnek, bir uygulama ağ geçidinde kuralları `911011` ve `911012` devre dışı bırakır:

```powershell
$disabledrules=New-AzApplicationGatewayFirewallDisabledRuleGroupConfig -RuleGroupName REQUEST-911-METHOD-ENFORCEMENT -Rules 911011,911012
Set-AzApplicationGatewayWebApplicationFirewallConfiguration -ApplicationGateway $gw -Enabled $true -FirewallMode Detection -RuleSetVersion 3.0 -RuleSetType OWASP -DisabledRuleGroups $disabledrules
Set-AzApplicationGateway -ApplicationGateway $gw
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
