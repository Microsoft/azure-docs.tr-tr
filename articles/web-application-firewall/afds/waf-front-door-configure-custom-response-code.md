---
title: Azure ön kapısına sahip Web uygulaması güvenlik duvarı (WAF) için özel yanıtları yapılandırma
description: WAF bir isteği engellediğinde özel yanıt kodunu ve iletiyi nasıl yapılandıracağınızı öğrenin.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 06/10/2020
ms.author: victorh
ms.reviewer: tyao
ms.openlocfilehash: 8fc6e71494df36cd6f823661b18e4a3d8ce2938c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94563690"
---
# <a name="configure-a-custom-response-for-azure-web-application-firewall-waf"></a>Azure Web uygulaması güvenlik duvarı (WAF) için özel bir yanıt yapılandırma

Varsayılan olarak, WAF eşleşen bir kural nedeniyle isteği engellediğinde, **istek engellendi** iletisiyle 403 durum kodu döndürür. Varsayılan ileti ayrıca, istek için [günlük girişlerine](./waf-front-door-monitor.md) bağlantı sağlamak üzere kullanılabilecek izleme başvuru dizesini de içerir.  Özel bir yanıt durum kodu ve kullanım durumunuz için başvuru dizesine sahip özel bir ileti yapılandırabilirsiniz. Bu makalede, bir istek WAF tarafından engellendiğinde özel bir yanıt sayfasının nasıl yapılandırılacağı açıklanır.

## <a name="configure-custom-response-status-code-and-message-use-portal"></a>Özel yanıt durum kodu ve ileti kullanım portalını yapılandırma

WAF portalından "Ilke ayarları" altında özel yanıt durum kodunu ve gövdesini yapılandırabilirsiniz.

:::image type="content" source="../media/waf-front-door-configure-custom-response-code/custom-response-settings.png" alt-text="WAF Ilke ayarları":::

Yukarıdaki örnekte, yanıt kodu 403 olarak tutulduk ve aşağıdaki görüntüde gösterildiği gibi kısa bir "lütfen bize başvurun" iletisi yapılandırdınız:

:::image type="content" source="../media/waf-front-door-configure-custom-response-code/custom-response.png" alt-text="Özel yanıt örneği":::

"{{Azure-ref}}", yanıt gövdesine benzersiz başvuru dizesi ekler. Değer, ve günlüklerindeki TrackingReference alanıyla eşleşir `FrontdoorAccessLog` `FrontdoorWebApplicationFirewallLog` .

## <a name="configure-custom-response-status-code-and-message-use-powershell"></a>Özel yanıt durum kodunu ve iletiyi yapılandırma PowerShell kullanma

### <a name="set-up-your-powershell-environment"></a>PowerShell ortamınızı hazırlama

Azure PowerShell, Azure kaynaklarınızı yönetmek için [Azure Resource Manager](../../azure-resource-manager/management/overview.md) modelini kullanan bir dizi cmdlet sunar. 

[Azure PowerShell](/powershell/azure/)'i yerel makinenize yükleyebilir ve herhangi bir PowerShell oturumunda kullanabilirsiniz. Azure kimlik bilgilerinizle oturum açmak ve az PowerShell modülünü yüklemek için sayfadaki yönergeleri izleyin.

### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Oturum açma için etkileşimli iletişim kutusuyla Azure 'a bağlanma

```
Connect-AzAccount
Install-Module -Name Az

```
PowerShellGet 'in güncel sürümünün yüklü olduğundan emin olun. Aşağıdaki komutu çalıştırın ve PowerShell'i yeniden açın.
```
Install-Module PowerShellGet -Force -AllowClobber
``` 
### <a name="install-azfrontdoor-module"></a>Install az. Frontkapı Module 

```
Install-Module -Name Az.FrontDoor
```

### <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Azure 'da, ilgili kaynakları bir kaynak grubuna ayırabilirsiniz. Burada, [New-AzResourceGroup](/powershell/module/Az.resources/new-Azresourcegroup)kullanarak bir kaynak grubu oluşturacağız.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupWAF
```

### <a name="create-a-new-waf-policy-with-custom-response"></a>Özel Yanıtla yeni bir WAF ilkesi oluşturma 

Aşağıda, özel yanıt durum kodu 405 olarak ayarlanan yeni bir WAF ilkesi oluşturma örneği verilmiştir ve **size ileti engellenir.** [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) kullanarak

```azurepowershell
# WAF policy setting
New-AzFrontDoorWafPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-EnabledState enabled `
-Mode Detection `
-CustomBlockResponseStatusCode 405 `
-CustomBlockResponseBody "<html><head><title>You are blocked.</title></head><body></body></html>"
```

[Update-AzFrontDoorFireWallPolicy](/powershell/module/az.frontdoor/Update-AzFrontDoorWafPolicy)kullanarak var olan bir WAF ilkesinin özel yanıt kodunu veya yanıt gövdesi ayarlarını değiştirin.

```azurepowershell
# modify WAF response code
Update-AzFrontDoorFireWallPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-EnabledState enabled `
-Mode Detection `
-CustomBlockResponseStatusCode 403
```

```azurepowershell
# modify WAF response body
Update-AzFrontDoorFireWallPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-CustomBlockResponseBody "<html><head><title>Forbidden</title></head><body>{{azure-ref}}</body></html>"
```

## <a name="next-steps"></a>Sonraki adımlar
- [Azure ön kapısına sahip Web uygulaması güvenlik duvarı](../afds/afds-overview.md) hakkında daha fazla bilgi edinin