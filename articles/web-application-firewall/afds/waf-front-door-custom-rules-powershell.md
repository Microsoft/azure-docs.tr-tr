---
title: Özel kurallarla bir Web uygulaması güvenlik duvarı (WAF) ilkesi yapılandırma ve ön kapı için varsayılan Ruse kümesi Azure PowerShell
description: Bir WAF ilkesini yapılandırma hakkında bilgi edinmek için, var olan bir ön kapı uç noktası için hem özel hem de yönetilen kurallardan oluşur.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 09/05/2019
ms.author: victorh
ms.openlocfilehash: fec1ed5d51c4afe43df513b54595b4ce1e2f4bbb
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512554"
---
# <a name="configure-a-web-application-firewall-policy-using-azure-powershell"></a>Azure PowerShell kullanarak bir Web uygulaması güvenlik duvarı ilkesi yapılandırma

Azure Web uygulaması güvenlik duvarı (WAF) ilkesi, bir istek ön kapıya ulaştığında gereken İncelemeleri tanımlar.
Bu makalede, bazı özel kurallardan oluşan ve Azure tarafından yönetilen varsayılan kural kümesi etkinleştirilmiş bir WAF ilkesinin nasıl yapılandırılacağı gösterilmektedir.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Bir hız sınırı ilkesi ayarlamaya başlamadan önce, PowerShell ortamınızı ayarlayın ve bir ön kapı profili oluşturun.

### <a name="set-up-your-powershell-environment"></a>PowerShell ortamınızı hazırlama

Azure PowerShell, Azure kaynaklarınızı yönetmek için [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) modelini kullanan bir dizi cmdlet sunar. 

[Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)'i yerel makinenize yükleyebilir ve herhangi bir PowerShell oturumunda kullanabilirsiniz. Sayfadaki yönergeleri izleyerek Azure kimlik bilgilerinizle oturum açın ve az PowerShell Module ' ü kullanın.

#### <a name="sign-in-to-azure"></a>Azure'da oturum açma

```
Connect-AzAccount

```
Front Door modülünü yüklemeden önce geçerli PowerShellGet sürümünün yüklü olduğundan emin olun. Aşağıdaki komutu çalıştırın ve PowerShell'i yeniden açın.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 

#### <a name="install-azfrontdoor-module"></a>Install az. Frontkapı Module 

```
Install-Module -Name Az.FrontDoor
```
### <a name="create-a-front-door-profile"></a>Ön kapı profili oluşturma

[Hızlı başlangıç: ön kapı profili oluşturma](../../frontdoor/quickstart-create-front-door.md) bölümünde açıklanan yönergeleri Izleyerek bir ön kapı profili oluşturun

## <a name="custom-rule-based-on-http-parameters"></a>Http parametrelerine dayalı özel kural

Aşağıdaki örnek, [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject)kullanarak iki eşleştirme koşulu ile özel bir kuralın nasıl yapılandırılacağını gösterir. İstekler, başvuran tarafından tanımlanan belirli bir siteden ve sorgu dizesinde "Password" yok. 

```powershell-interactive
$referer = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestHeader -OperatorProperty Equal -Selector "Referer" -MatchValue "www.mytrustedsites.com/referpage.html"
$password = New-AzFrontDoorWafMatchConditionObject -MatchVariable QueryString -OperatorProperty Contains -MatchValue "password"
$AllowFromTrustedSites = New-AzFrontDoorWafCustomRuleObject -Name "AllowFromTrustedSites" -RuleType MatchRule -MatchCondition $referer,$password -Action Allow -Priority 1
```

## <a name="custom-rule-based-on-http-request-method"></a>Http istek yöntemine dayalı özel kural

[New-AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject) kullanarak aşağıdaki gibi bir kural engelleme "put" yöntemi oluşturun:

```powershell-interactive
$put = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestMethod -OperatorProperty Equal -MatchValue PUT
$BlockPUT = New-AzFrontDoorWafCustomRuleObject -Name "BlockPUT" -RuleType MatchRule -MatchCondition $put -Action Block -Priority 2
```

## <a name="create-a-custom-rule-based-on-size-constraint"></a>Boyut kısıtlamasına dayalı özel bir kural oluşturun

Aşağıdaki örnek, Azure PowerShell kullanarak 100 karakterden daha uzun URL 'ye sahip bir kural engelleme istekleri oluşturur:
```powershell-interactive
$url = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestUri -OperatorProperty GreaterThanOrEqual -MatchValue 100
$URLOver100 = New-AzFrontDoorWafCustomRuleObject -Name "URLOver100" -RuleType MatchRule -MatchCondition $url -Action Block -Priority 3
```
## <a name="add-managed-default-rule-set"></a>Yönetilen varsayılan kural kümesi Ekle

Aşağıdaki örnek, Azure PowerShell kullanarak yönetilen bir varsayılan kural kümesi oluşturur:
```powershell-interactive
$managedRules =  New-AzFrontDoorWafManagedRuleObject -Type DefaultRuleSet -Version 1.0
```
## <a name="configure-a-security-policy"></a>Güvenlik ilkesi yapılandırma

`Get-AzResourceGroup`kullanarak ön kapı profilini içeren kaynak grubunun adını bulun. Ardından, ön kapı profilini içeren belirtilen kaynak grubunda [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) ' ı kullanarak önceki adımlarda oluşturulan kurallarla bir güvenlik ilkesi yapılandırın.

```powershell-interactive
$myWAFPolicy=New-AzFrontDoorWafPolicy -Name $policyName -ResourceGroupName $resourceGroupName -Customrule $AllowFromTrustedSites,$BlockPUT,$URLOver100 -ManagedRule $managedRules -EnabledState Enabled -Mode Prevention
```

## <a name="link-policy-to-a-front-door-front-end-host"></a>İlkeyi bir ön kapı ön uç konağına bağlama

Güvenlik İlkesi nesnesini var olan bir ön kapı ön uç konağına bağlayın ve ön kapı özelliklerini güncelleştirin. İlk olarak, [Get-Azfrontkapısı](/powershell/module/Az.FrontDoor/Get-AzFrontDoor)kullanarak ön kapı nesnesini alın.
Ardından, ön uç *Webapplicationfirewallpolicylink* özelliğini, [set-azfrontkapısı](/powershell/module/Az.FrontDoor/Set-AzFrontDoor)kullanarak önceki adımda oluşturulan "$MyWAFPolicy $" öğesinin *RESOURCEID* değerini olarak ayarlayın. 

Aşağıdaki örnek, [hızlı başlangıç: ön kapı oluşturma](../../frontdoor/quickstart-create-front-door.md) makalesinde sunulan yönergeleri kullanarak ön kapı profilini oluşturduğunuz varsayımıyla *myResourceGroupFD1* kaynak grubu adını kullanır. Ayrıca, aşağıdaki örnekte $frontDoorName, ön kapı profilinizin adıyla değiştirin. 

```powershell-interactive
   $FrontDoorObjectExample = Get-AzFrontDoor `
     -ResourceGroupName myResourceGroupFD1 `
     -Name $frontDoorName
   $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $myWAFPolicy.Id
   Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
 ```

> [!NOTE]
> Bir güvenlik ilkesini ön kapı ön ucuna bağlamak için yalnızca *Webapplicationfirewallpolicylink* özelliğini bir kez ayarlamanız gerekir. Sonraki ilke güncelleştirmeleri otomatik olarak ön uca uygulanır.

## <a name="next-steps"></a>Sonraki adımlar

- [Ön kapı](../../frontdoor/front-door-overview.md) hakkında daha fazla bilgi 
- [Ön kapılı WAF](afds-overview.md) hakkında daha fazla bilgi edinin
