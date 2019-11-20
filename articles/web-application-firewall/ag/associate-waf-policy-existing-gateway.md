---
title: Web uygulaması güvenlik duvarı ilkesini mevcut bir Azure Application Gateway ilişkilendir
description: Bir Web uygulaması güvenlik duvarı ilkesini mevcut bir Azure Application Gateway ilişkilendirmeyi öğrenin.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 10/25/2019
ms.author: victorh
ms.openlocfilehash: 1ed2e0cf8cc8cd841d8779462d62ba4852774a3a
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083900"
---
# <a name="associate-a-waf-policy-with-an-existing-application-gateway"></a>Bir WAF ilkesini mevcut bir Application Gateway ilişkilendir

Bir [WAF ilkesi oluşturmak](create-waf-policy-ag.md)için Azure PowerShell kullanabilirsiniz, ancak zaten bir Application Gateway olabilir ve yalnızca bir WAF ilkesini bununla ilişkilendirmek isteyebilirsiniz. Bu makalede, yalnızca bu; bir WAF Ilkesi oluşturup onu zaten var olan bir Application Gateway ilişkilendirirsiniz. 

1. Application Gateway ve güvenlik duvarı Ilkenizi alın. Mevcut bir güvenlik duvarı Ilkeniz yoksa, bkz. 2. adım. 

   ```azurepowershell-interactive
      Connect-AzAccount
      Select-AzSubscription -Subscription "<sub id>"`

      #Get Application Gateway and existing policy object or create new`
      $gw = Get-AzApplicationGateway -Name <Waf v2> -ResourceGroupName <RG name>`
      $policy = Get-AzApplicationGatewayFirewallPolicy -Name <policy name> -ResourceGroupName <RG name>`
   ```

2. Seçim Bir güvenlik duvarı Ilkesi oluşturun.

   ```azurepowershell-interactive
      New-AzApplicationGatewayFirewallPolicy -Name <policy name> -ResourceGroupName <RG name>'
      $policy = Get-AzApplicationGatewayFirewallPolicy -Name <policy name> -ResourceGroupName <RG name>`
   ```
   > [!NOTE]
   > Bu WAF Ilkesini bir WAF yapılandırmadan WAF Ilkesine geçiş için oluşturuyorsanız, Ilkenin eski yapılandırmasının tam bir kopyası olması gerekir. Bu, her dışlama, özel kural, devre dışı kural grubu, vb., WAF yapılandırmasında olduğu gibi tam olarak aynı olması gerektiği anlamına gelir.
3. Seçim WAF ilkesini gereksinimlerinize uyacak şekilde yapılandırabilirsiniz. Bu özel kuralları içerir, kuralları/kural gruplarını devre dışı bırakır, dışlamaları, dosya yükleme sınırlarını ayarlar, vb. Bu adımı atlarsanız tüm varsayılanlar seçilir. 
   
4. İlkeyi kaydedin ve Application Gateway ekleyin. 
   
   ```azurepowershell-interactive
      #Save the policy itself
      Set-AzApplicationGatewayFirewallPolicy -InputObject $policy`
   
      #Attach the policy to an Application Gateway
      $gw.FirewallPolicy = $policy`
   
      #Save the Application Gateway
      Set-AzApplicationGateway -ApplicationGateway $gw`
   ```

## <a name="next-steps"></a>Sonraki adımlar
[Özel kurallar hakkında bilgi edinin.](configure-waf-custom-rules.md)
