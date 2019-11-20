---
title: Azure ön kapı hizmeti için bir Web uygulaması güvenlik duvarı kuralıyla bir IP kısıtlama kuralı yapılandırma
description: Mevcut bir Azure ön kapısı hizmeti uç noktası için IP adreslerini kısıtlamak üzere bir Web uygulaması güvenlik duvarı kuralı yapılandırma hakkında bilgi edinin.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 08/21/2019
ms.author: victorh
ms.reviewer: tyao
ms.openlocfilehash: d0a32c4d59d580cb82ad6174c57ebbf677ac75b3
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73517169"
---
# <a name="configure-an-ip-restriction-rule-with-a-web-application-firewall-for-azure-front-door-service"></a>Azure ön kapı hizmeti için Web uygulaması güvenlik duvarıyla bir IP kısıtlama kuralı yapılandırma
Bu makalede, Azure CLı, Azure PowerShell veya bir Azure Resource Manager şablonu kullanarak Azure ön kapısı hizmeti için bir Web uygulaması güvenlik duvarında (WAF) IP kısıtlama kurallarını yapılandırma işlemi gösterilmektedir.

IP adresi tabanlı erişim denetimi kuralı, Web uygulamalarınıza erişimi denetlemenize olanak tanıyan özel bir WAF kuralıdır. Bu, sınıfsız etki alanları arası yönlendirme (CıDR) biçimindeki IP adresleri veya IP adresi aralıklarının bir listesini belirterek yapar.

Varsayılan olarak, Web uygulamanıza internet 'ten erişilebilir. İstemcilerle erişimi bilinen IP adresleri veya IP adresi aralıkları listesinden sınırlamak isterseniz, IP adreslerinin listesini eşleşen değerler olarak içeren bir IP eşleştirme kuralı oluşturabilir ve işleç "Not" (Negate true) olarak ayarlar ve **engellenecek**eylemi. Bir IP kısıtlama kuralı uygulandıktan sonra, bu izin verilen listenin dışındaki adreslerden kaynaklanan istekler 403 yasaklanmış bir yanıt alır.  

## <a name="configure-a-waf-policy-with-the-azure-cli"></a>Azure CLı ile bir WAF ilkesi yapılandırma

### <a name="prerequisites"></a>Önkoşullar
Bir IP kısıtlama ilkesini yapılandırmaya başlamadan önce, CLı ortamınızı ayarlayın ve bir Azure ön kapı hizmeti profili oluşturun.

#### <a name="set-up-the-azure-cli-environment"></a>Azure CLı ortamını ayarlama
1. [Azure CLI](/cli/azure/install-azure-cli)'yi veya Azure Cloud Shell kullanın. Azure Cloud Shell doğrudan Azure portalının içinde çalıştırabileceğiniz ücretsiz bir Bash kabuğudur. Azure CLI, kabuğa önceden yüklenmiştir ve kabuk, hesabınızla birlikte kullanılacak şekilde yapılandırılmıştır. Aşağıdaki CLı komutlarında **deneyin** düğmesini seçin ve ardından açılan Cloud Shell oturumunda Azure hesabınızda oturum açın. Oturum başladıktan sonra Azure ön kapı hizmeti uzantısını eklemek için `az extension add --name front-door` girin.
 2. CLı 'yi Bash 'de yerel olarak kullanıyorsanız, `az login`kullanarak Azure 'da oturum açın.

#### <a name="create-an-azure-front-door-service-profile"></a>Azure ön kapı hizmeti profili oluşturma
[Hızlı başlangıç: yüksek kullanılabilirliğe sahip bir genel Web uygulaması Için ön kapı oluşturma](../../frontdoor/quickstart-create-front-door.md)bölümünde açıklanan yönergeleri Izleyerek bir Azure ön kapı hizmeti profili oluşturun.

### <a name="create-a-waf-policy"></a>WAF ilkesi oluşturma

[Az Network Front-kapısı WAF-Policy Create](/cli/azure/ext/front-door/network/front-door/waf-policy?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-create) komutunu kullanarak bir WAF ilkesi oluşturun. Aşağıdaki örnekte, *ıpallowpolicyexampleclı* ilke adı ' nı benzersiz bir ilke adıyla değiştirin.

```azurecli-interactive 
az network front-door waf-policy create \
  --resource-group <resource-group-name> \
  --subscription <subscription ID> \
  --name IPAllowPolicyExampleCLI
  ```
### <a name="add-a-custom-ip-access-control-rule"></a>Özel bir IP erişim denetimi kuralı ekleme

Az [Network ön kapı WAF-Policy Custom-Rule Create](/cli/azure/ext/front-door/network/front-door/waf-policy/rule?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-rule-create) komutunu kullanarak az önce oluşturduğunuz WAF ilkesi için özel bir IP erişim denetimi kuralı ekleyin.

Aşağıdaki örneklerde:
-  *Ipallowpolicyexampleclı* öğesini daha önce oluşturduğunuz benzersiz ilkenize değiştirin.
-  *IP adresi-aralığı-1*, *IP-adres-aralığı-2* ' yi kendi aralığınızla değiştirin.

İlk olarak, önceki adımdan oluşturulan ilke için bir IP izin verme kuralı oluşturun. Bir kuralın bir sonraki adımda eklenecek bir eşleşme koşuluna sahip olması gerektiğinden, Note **--erteleme** gereklidir.

```azurecli
az network front-door waf-policy rule create \
  --name IPAllowListRule \
  --priority 1 \
  --rule-type MatchRule \
  --action Block \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI --defer
```
Sonra, kurala eşleştirme koşulu ekleyin:

```azurecli
az network front-door waf-policy rule match-condition add\
--match-variable RemoteAddr \
--operator IPMatch
--values "ip-address-range-1" "ip-address-range-2"
--negate true\
--name IPAllowListRule\
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI 
  ```
                                                   
### <a name="find-the-id-of-a-waf-policy"></a>Bir WAF ilkesinin KIMLIĞINI bulma 
[Az Network ön kapıwaf-Policy Show](/cli/azure/ext/front-door/network/front-door/waf-policy?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-show) komutunu kullanarak bir WAF ilkesinin kimliğini bulun. Aşağıdaki örnekteki *ıpallowpolicyexampleclı* değerini, daha önce oluşturduğunuz benzersiz ilkenize göre değiştirin.

   ```azurecli
   az network front-door  waf-policy show \
     --resource-group <resource-group-name> \
     --name IPAllowPolicyExampleCLI
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-service-front-end-host"></a>Bir WAF ilkesini bir Azure ön kapısı hizmeti ön uç konağına bağlama

[Az Network ön kapı Update](/cli/azure/ext/front-door/network/front-door?view=azure-cli-latest#ext-front-door-az-network-front-door-update) komutunu kullanarak Azure ön kapı hizmeti *Webapplicationfirewallpolicylink* kimliğini ilke kimliğine ayarlayın. *Ipallowpolicyexampleclı* öğesini daha önce oluşturduğunuz benzersiz ilkenize değiştirin.

   ```azurecli
   az network front-door update \
     --set FrontendEndpoints[0].WebApplicationFirewallPolicyLink.id=/subscriptions/<subscription ID>/resourcegroups/<resource- name>/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/IPAllowPolicyExampleCLI \
     --name <frontdoor-name>
     --resource-group <resource-group-name>
   ```
Bu örnekte, WAF ilkesi **Frontendendpoints [0]** öğesine uygulanır. WAF ilkesini ön uçlarınızın herhangi birine bağlayabilirsiniz.
> [!Note]
> Bir WAF ilkesini bir Azure ön kapı hizmeti ön ucuna bağlamak için **Webapplicationfirewallpolicylink** özelliğini yalnızca bir kez ayarlamanız gerekir. Sonraki ilke güncelleştirmeleri ön uca otomatik olarak uygulanır.

## <a name="configure-a-waf-policy-with-azure-powershell"></a>Azure PowerShell bir WAF ilkesi yapılandırma

### <a name="prerequisites"></a>Önkoşullar
Bir IP kısıtlama ilkesini yapılandırmaya başlamadan önce, PowerShell ortamınızı ayarlayın ve bir Azure ön kapı hizmeti profili oluşturun.

#### <a name="set-up-your-powershell-environment"></a>PowerShell ortamınızı hazırlama
Azure PowerShell, Azure kaynaklarını yönetmek için [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) modelini kullanan bir cmdlet kümesi sağlar.

[Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)'i yerel makinenize yükleyebilir ve herhangi bir PowerShell oturumunda kullanabilirsiniz. Azure kimlik bilgilerinizi kullanarak PowerShell 'de oturum açmak için sayfasındaki yönergeleri izleyin ve sonra az Module ' ü kullanın.

1. Aşağıdaki komutu kullanarak Azure 'a bağlanın ve ardından oturum açmak için etkileşimli bir iletişim kutusu kullanın.
    ```
    Connect-AzAccount
    ```
 2. Bir Azure ön kapı hizmeti modülünü yüklemeden önce, PowerShellGet modülünün güncel sürümünün yüklü olduğundan emin olun. Aşağıdaki komutu çalıştırın ve ardından PowerShell 'i yeniden açın.

    ```
    Install-Module PowerShellGet -Force -AllowClobber
    ``` 

3. Aşağıdaki komutu kullanarak az. Frontkapısı modülünü yükler. 
    
    ```
    Install-Module -Name Az.FrontDoor
    ```
### <a name="create-an-azure-front-door-service-profile"></a>Azure ön kapı hizmeti profili oluşturma
[Hızlı başlangıç: yüksek kullanılabilirliğe sahip bir genel Web uygulaması Için ön kapı oluşturma](../../frontdoor/quickstart-create-front-door.md)bölümünde açıklanan yönergeleri Izleyerek bir Azure ön kapı hizmeti profili oluşturun.

### <a name="define-an-ip-match-condition"></a>Bir IP eşleştirme koşulu tanımlayın
Bir IP eşleştirme koşulu tanımlamak için [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) komutunu kullanın.
Aşağıdaki örnekte, *IP adresi-aralığı-1*, *IP-adres-aralığı-2* ' yi kendi aralığınızla değiştirin.    
```powershell
$IPMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable  RemoteAddr `
-OperatorProperty IPMatch `
-MatchValue "ip-address-range-1", "ip-address-range-2"
-NegateCondition 1
```
     
### <a name="create-a-custom-ip-allow-rule"></a>Özel IP izin verme kuralı oluşturma

Bir eylem tanımlamak ve öncelik ayarlamak için [New-AzFrontDoorCustomRuleObject](/powershell/module/Az.FrontDoor/New-azfrontdoorwafcustomruleobject) komutunu kullanın. Aşağıdaki örnekte, listeyle eşleşen istemci IP 'lerinden olmayan istekler engellenir.

```powershell
$IPAllowRule = New-AzFrontDoorCustomRuleObject `
-Name "IPAllowRule" `
-RuleType MatchRule `
-MatchCondition $IPMatchCondition `
-Action Block -Priority 1
```

### <a name="configure-a-waf-policy"></a>WAF ilkesini yapılandırma
`Get-AzResourceGroup`kullanarak Azure ön kapı hizmeti profilini içeren kaynak grubunun adını bulun. Ardından, [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy)kullanarak bir WAF ilkesini IP kuralıyla yapılandırın.

```powershell
  $IPAllowPolicyExamplePS = New-AzFrontDoorWafPolicy `
    -Name "IPRestrictionExamplePS" `
    -resourceGroupName <resource-group-name> `
    -Customrule $IPAllowRule`
    -Mode Prevention `
    -EnabledState Enabled
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-service-front-end-host"></a>Bir WAF ilkesini bir Azure ön kapısı hizmeti ön uç konağına bağlama

Bir WAF ilke nesnesini mevcut bir ön uç konağına bağlayın ve Azure ön kapı hizmeti özelliklerini güncelleştirin. İlk olarak [Get-Azfrontkapısı](/powershell/module/Az.FrontDoor/Get-AzFrontDoor)kullanarak Azure ön kapı hizmeti nesnesini alın. Ardından, **Webapplicationfirewallpolicylink** özelliğini, önceki adımda oluşturulan *$IPAllowPolicyExamplePS*kaynak kimliği olarak ayarlayın, [set-azfrontkapısı](/powershell/module/Az.FrontDoor/Set-AzFrontDoor) komutunu kullanın.

```powershell
  $FrontDoorObjectExample = Get-AzFrontDoor `
    -ResourceGroupName <resource-group-name> `
    -Name $frontDoorName
  $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $IPBlockPolicy.Id
  Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
```

> [!NOTE]
> Bu örnekte, WAF ilkesi **Frontendendpoints [0]** öğesine uygulanır. Bir WAF ilkesini, ön uçlarınızın herhangi birine bağlayabilirsiniz. Bir WAF ilkesini bir Azure ön kapı hizmeti ön ucuna bağlamak için **Webapplicationfirewallpolicylink** özelliğini yalnızca bir kez ayarlamanız gerekir. Sonraki ilke güncelleştirmeleri ön uca otomatik olarak uygulanır.


## <a name="configure-a-waf-policy-with-a-resource-manager-template"></a>Kaynak Yöneticisi şablonuyla bir WAF ilkesi yapılandırma
Bir Azure ön kapısı hizmet ilkesi ve özel IP kısıtlama kuralları içeren bir WAF ilkesi oluşturan şablonu görüntülemek için [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip)' a gidin.


## <a name="next-steps"></a>Sonraki adımlar

- [Azure ön kapı hizmeti profili oluşturmayı](../../frontdoor/quickstart-create-front-door.md)öğrenin.
