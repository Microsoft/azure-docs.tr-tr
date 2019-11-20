---
title: Öğretici-Azure ön kapı hizmeti için coğrafi filtreleme Web uygulaması güvenlik duvarı ilkesini yapılandırma
description: Bu öğreticide basit bir coğrafi filtreleme ilkesi oluşturmayı ve bu ilkeyi mevcut Front Door ön uç konağınız ile ilişkilendirmeyi öğreneceksiniz
services: frontdoor
documentationcenter: ''
author: KumudD
manager: twooley
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: kumud
ms.reviewer: tyao
ms.openlocfilehash: e40e99aa57d10bd69143efc8db38ac0071d8952f
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68827762"
---
# <a name="how-to-set-up-a-geo-filtering-waf-policy-for-your-front-door"></a>Ön kapılarınız için coğrafi filtreleme WAF ilkesini ayarlama
Bu öğreticide, örnek bir coğrafi filtreleme ilkesi oluşturmak ve bu ilkeyi mevcut bir Front Door ön uç konağı ile ilişkilendirmek için Azure PowerShell kullanma gösterilmektedir. Bu örnek coğrafi filtreleme ilkesi, Birleşik Devletler dışındaki diğer tüm ülke/bölgelerden gelen istekleri engeller.

Azure aboneliğiniz yoksa şimdi [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Önkoşullar
Bir coğrafi filtre İlkesi ayarlamaya başlamadan önce, PowerShell ortamınızı ayarlayın ve bir ön kapı profili oluşturun.
### <a name="set-up-your-powershell-environment"></a>PowerShell ortamınızı hazırlama
Azure PowerShell, Azure kaynaklarınızı yönetmek için [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) modelini kullanan bir dizi cmdlet sunar. 

[Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)'i yerel makinenize yükleyebilir ve herhangi bir PowerShell oturumunda kullanabilirsiniz. Azure kimlik bilgilerinizle oturum açmak ve az PowerShell modülünü yüklemek için sayfadaki yönergeleri izleyin.

#### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Oturum açma için etkileşimli iletişim kutusuyla Azure 'a bağlanma
```
Install-Module -Name Az
Connect-AzAccount
```
PowerShellGet 'in güncel sürümünün yüklü olduğundan emin olun. Aşağıdaki komutu çalıştırın ve PowerShell'i yeniden açın.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 
#### <a name="install-azfrontdoor-module"></a>Install az. Frontkapı Module 

```
Install-Module -Name Az.FrontDoor
```

### <a name="create-a-front-door-profile"></a>Ön kapı profili oluşturma
Hızlı Başlangıç bölümünde [açıklanan yönergeleri izleyerek bir ön kapı profili oluşturun: Ön kapı profili](quickstart-create-front-door.md)oluşturun.

## <a name="define-geo-filtering-match-condition"></a>Coğrafi filtreleme eşleştirme koşulunu tanımlayın

Bir eşleşme koşulu oluştururken [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) on Parameters KULLANıLARAK "ABD" den gelenlerin kullanıldığı istekleri seçen bir örnek eşleşme koşulu oluşturun. Ülke eşlemesine yönelik iki harfli ülke kodu [burada](front-door-geo-filtering.md)verilmiştir.

```azurepowershell-interactive
$nonUSGeoMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable RemoteAddr `
-OperatorProperty GeoMatch `
-NegateCondition $true `
-MatchValue "US"
```
 
## <a name="add-geo-filtering-match-condition-to-a-rule-with-action-and-priority"></a>Eylem ve Öncelik ile bir kurala coğrafi filtreleme eşleşme koşulu ekleme

[New-AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject)kullanarak eşleşme `nonUSBlockRule` koşuluna, eyleme ve önceliğe göre bir customrule nesnesi oluşturun.  Bir CustomRule'un birden fazla MatchCondition'ı olabilir.  Bu örnekte Eylem Engelle değerine, Öncelik ise en yüksek öncelik olan 1 değerine ayarlanmıştır.

```
$nonUSBlockRule = New-AzFrontDoorWafCustomRuleObject `
-Name "geoFilterRule" `
-RuleType MatchRule `
-MatchCondition $nonUSGeoMatchCondition `
-Action Block `
-Priority 1
```

## <a name="add-rules-to-a-policy"></a>İlkeye kural ekleme
Kullanarak `Get-AzResourceGroup`ön kapı profilini içeren kaynak grubunun adını bulun. Ardından, ön kapı `geoPolicy` profilini içeren belirtilen `nonUSBlockRule` kaynak grubunda [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) kullanarak içeren bir ilke nesnesi oluşturun. Coğrafi ilke için benzersiz bir ad sağlamanız gerekir. 

Aşağıdaki örnek, [hızlı başlangıçta belirtilen yönergeleri kullanarak ön kapı profilini oluşturduğunuz varsayımıyla *myResourceGroupFD1* kaynak grubu adını kullanır: Ön kapı](quickstart-create-front-door.md) oluşturma makalesi. Aşağıdaki örnekte, *Geopolicyallowusname* Ilke adını yalnızca benzersiz bir ilke adıyla değiştirin.

```
$geoPolicy = New-AzFrontDoorWafPolicy `
-Name "geoPolicyAllowUSOnly" `
-resourceGroupName myResourceGroupFD1 `
-Customrule $nonUSBlockRule  `
-Mode Prevention `
-EnabledState Enabled
```

## <a name="link-waf-policy-to-a-front-door-frontend-host"></a>WAF ilkesini bir ön kapılı ön uç konağına bağlama
WAF ilke nesnesini mevcut ön kapı ön uç konağına bağlayın ve ön kapı özelliklerini güncelleştirin. 

Bunu yapmak için ilk olarak [Get-Azfrontkapısı](/powershell/module/az.frontdoor/get-azfrontdoor)kullanarak ön kapı nesnenizin alınması gerekir. 

```
$geoFrontDoorObjectExample = Get-AzFrontDoor -ResourceGroupName myResourceGroupFD1
$geoFrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $geoPolicy.Id
```

Ardından, ön uç webapplicationfirewallpolicylink özelliğini, `geoPolicy` [set-azfrontkapısının](/powershell/module/az.frontdoor/set-azfrontdoor)kullanıldığı RESOURCEID olarak ayarlayın.

```
Set-AzFrontDoor -InputObject $geoFrontDoorObjectExample[0]
```

> [!NOTE] 
> Bir WAF ilkesini bir ön kapılı ön uç konağına bağlamak için yalnızca WebApplicationFirewallPolicyLink özelliğini bir kez ayarlamanız gerekir. Sonraki ilke güncelleştirmeleri, ön uç konağına otomatik olarak uygulanır.

## <a name="next-steps"></a>Sonraki adımlar
- [Azure Web uygulaması güvenlik duvarı](waf-overview.md)hakkında bilgi edinin.
- [Front Door oluşturmayı](quickstart-create-front-door.md) öğrenin.
