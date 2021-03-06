---
title: Azure ön kapı hizmeti için coğrafi filtreleme Web uygulaması güvenlik duvarı ilkesini yapılandırma
description: Bu öğreticide, coğrafi filtreleme ilkesi oluşturmayı ve ilkeyi mevcut ön kapılı ön uç konunuzla ilişkilendirmeyi öğreneceksiniz.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: victorh
ms.reviewer: tyao
ms.openlocfilehash: 479b1d8ed1f4238486bb78e33a6139463578dbba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94563316"
---
# <a name="set-up-a-geo-filtering-waf-policy-for-your-front-door"></a>Ön kapılarınız için bir coğrafi filtreleme WAF ilkesi ayarlama

Bu öğreticide, örnek bir coğrafi filtreleme ilkesi oluşturmak ve bu ilkeyi mevcut bir Front Door ön uç konağı ile ilişkilendirmek için Azure PowerShell kullanma gösterilmektedir. Bu örnek coğrafi filtreleme ilkesi, Birleşik Devletler dışındaki diğer tüm ülke/bölgelerden gelen istekleri engeller.

Azure aboneliğiniz yoksa şimdi [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Bir coğrafi filtre İlkesi ayarlamaya başlamadan önce, PowerShell ortamınızı ayarlayın ve bir ön kapı profili oluşturun.
### <a name="set-up-your-powershell-environment"></a>PowerShell ortamınızı hazırlama
Azure PowerShell, Azure kaynaklarınızı yönetmek için [Azure Resource Manager](../../azure-resource-manager/management/overview.md) modelini kullanan bir dizi cmdlet sunar. 

[Azure PowerShell](/powershell/azure/)'i yerel makinenize yükleyebilir ve herhangi bir PowerShell oturumunda kullanabilirsiniz. Azure kimlik bilgilerinizle oturum açmak ve az PowerShell modülünü yüklemek için sayfadaki yönergeleri izleyin.

#### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Oturum açmak için etkileşimli iletişim kutusuyla Azure 'a bağlanın

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

[Hızlı başlangıç: ön kapı profili oluşturma](../../frontdoor/quickstart-create-front-door.md)bölümünde açıklanan yönergeleri Izleyerek bir ön kapı profili oluşturun.

## <a name="define-geo-filtering-match-condition"></a>Coğrafi filtreleme eşleştirme koşulunu tanımlayın

Bir eşleşme koşulu oluştururken [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) on Parameters KULLANıLARAK "ABD" den gelenlerin kullanıldığı istekleri seçen bir örnek eşleşme koşulu oluşturun. Ülke/bölge eşlemesine yönelik iki harfli ülke/bölge kodu [, Azure ön kapısının bir etki alanında coğrafi filtreleme](waf-front-door-geo-filtering.md)olarak sunulmaktadır.

```azurepowershell-interactive
$nonUSGeoMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable RemoteAddr `
-OperatorProperty GeoMatch `
-NegateCondition $true `
-MatchValue "US"
```
 
## <a name="add-geo-filtering-match-condition-to-a-rule-with-action-and-priority"></a>Eylem ve Öncelik ile bir kurala coğrafi filtreleme eşleşme koşulu ekleme

`nonUSBlockRule` [New-AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject)kullanarak eşleşme koşuluna, eyleme ve önceliğe göre bir customrule nesnesi oluşturun.  Bir CustomRule'un birden fazla MatchCondition'ı olabilir.  Bu örnekte Eylem Engelle değerine, Öncelik ise en yüksek öncelik olan 1 değerine ayarlanmıştır.

```
$nonUSBlockRule = New-AzFrontDoorWafCustomRuleObject `
-Name "geoFilterRule" `
-RuleType MatchRule `
-MatchCondition $nonUSGeoMatchCondition `
-Action Block `
-Priority 1
```

## <a name="add-rules-to-a-policy"></a>İlkeye kural ekleme

Kullanarak ön kapı profilini içeren kaynak grubunun adını bulun `Get-AzResourceGroup` . Ardından, `geoPolicy` `nonUSBlockRule`  ön kapı profilini içeren belirtilen kaynak grubunda [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) kullanarak içeren bir ilke nesnesi oluşturun. Coğrafi ilke için benzersiz bir ad sağlamanız gerekir. 

Aşağıdaki örnek, [hızlı başlangıç: ön kapı oluşturma](../../frontdoor/quickstart-create-front-door.md) makalesinde sunulan yönergeleri kullanarak ön kapı profilini oluşturduğunuz varsayımıyla *myResourceGroupFD1* kaynak grubu adını kullanır. Aşağıdaki örnekte, *Geopolicyallowusname* Ilke adını yalnızca benzersiz bir ilke adıyla değiştirin.

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

Ardından, ön uç WebApplicationFirewallPolicyLink özelliğini, `geoPolicy` [set-Azfrontkapısının](/powershell/module/az.frontdoor/set-azfrontdoor)kullanıldığı RESOURCEID olarak ayarlayın.

```
Set-AzFrontDoor -InputObject $geoFrontDoorObjectExample[0]
```

> [!NOTE] 
> Bir WAF ilkesini bir ön kapılı ön uç konağına bağlamak için yalnızca WebApplicationFirewallPolicyLink özelliğini bir kez ayarlamanız gerekir. Sonraki ilke güncelleştirmeleri, ön uç konağına otomatik olarak uygulanır.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Web uygulaması güvenlik duvarı](../overview.md)hakkında bilgi edinin.
- [Front Door oluşturmayı](../../frontdoor/quickstart-create-front-door.md) öğrenin.