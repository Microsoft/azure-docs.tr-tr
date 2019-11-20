---
title: Azure ön kapısına sahip Web uygulaması güvenlik duvarı için özel bir yanıt yapılandırma
description: Web uygulaması güvenlik duvarı (WAF) bir isteği engellediğinde özel yanıt kodunu ve iletiyi nasıl yapılandıracağınızı öğrenin.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 08/21/2019
ms.author: victorh
ms.reviewer: tyao
ms.openlocfilehash: ae3099373bbaece57c2896eb0fb4e7d59d817f25
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73517156"
---
# <a name="configure-a-custom-response-for-azure-web-application-firewall"></a>Azure Web uygulaması güvenlik duvarı için özel bir yanıt yapılandırma

Varsayılan olarak, Azure ön kapısına sahip Azure Web uygulaması güvenlik duvarı (WAF), eşleşen bir kural nedeniyle bir isteği engellediğinde, **istek engellendi** iletisiyle 403 durum kodu döndürür. Bu makalede bir istek WAF tarafından engellendiğinde özel yanıt durum kodu ve yanıt iletisinin nasıl yapılandırılacağı açıklanır.

## <a name="set-up-your-powershell-environment"></a>PowerShell ortamınızı hazırlama
Azure PowerShell, Azure kaynaklarınızı yönetmek için [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) modelini kullanan bir dizi cmdlet sunar. 

[Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)'i yerel makinenize yükleyebilir ve herhangi bir PowerShell oturumunda kullanabilirsiniz. Azure kimlik bilgilerinizle oturum açmak ve az PowerShell modülünü yüklemek için sayfadaki yönergeleri izleyin.

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

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Azure 'da, ilgili kaynakları bir kaynak grubuna ayırabilirsiniz. Bu örnekte, [New-AzResourceGroup](/powershell/module/Az.resources/new-Azresourcegroup)kullanarak bir kaynak grubu oluşturacaksınız.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupWAF
```

## <a name="create-a-new-waf-policy-with-custom-response"></a>Özel Yanıtla yeni bir WAF ilkesi oluşturma 

Aşağıda, özel yanıt durum kodu 405 olarak ayarlanan yeni bir WAF ilkesi oluşturma örneği verilmiştir ve bu ileti **engellenir.** [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy)kullanma.

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
-CustomBlockResponseBody "<html><head><title> Forbidden</title></head><body></body></html>"
```

## <a name="next-steps"></a>Sonraki adımlar
- [Azure ön kapısına sahip Web uygulaması güvenlik duvarı](../afds/afds-overview.md) hakkında daha fazla bilgi edinin