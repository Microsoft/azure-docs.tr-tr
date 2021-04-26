---
title: Azure ön kapısı için IP kısıtlaması WAF kuralını yapılandırma
description: Mevcut bir Azure ön kapısının IP adreslerini kısıtlamak için bir Web uygulaması güvenlik duvarı kuralının nasıl yapılandırılacağını öğrenin.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 12/22/2020
ms.author: tyao
ms.openlocfilehash: 32bf7a5ecc93fa23c8c704dc346048c26c086121
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107860860"
---
# <a name="configure-an-ip-restriction-rule-with-a-web-application-firewall-for-azure-front-door"></a>Azure ön kapısı için Web uygulaması güvenlik duvarıyla bir IP kısıtlama kuralı yapılandırma

Bu makalede, Azure ön kapısının Azure portal, Azure CLı, Azure PowerShell veya Azure Resource Manager şablonunu kullanarak bir Web uygulaması güvenlik duvarında (WAF) IP kısıtlama kurallarını yapılandırma işlemi gösterilmektedir.

IP adresi tabanlı erişim denetimi kuralı, Web uygulamalarınıza erişimi denetlemenize olanak tanıyan özel bir WAF kuralıdır. Bu, sınıfsız Inter-Domain yönlendirme (CıDR) biçimindeki IP adresleri veya IP adresi aralıklarının bir listesini belirterek yapar. IP adresi eşleşmesi, **Remoteaddr** ve **socketadresi** içinde iki tür eşleşme değişkeni vardır. RemoteAddr, genellikle X-Iletilen istek üst bilgisi aracılığıyla gönderilen özgün istemci IP 'dir. Socketadresi, WAF 'nin gördüğü kaynak IP adresidir. Kullanıcı bir proxy 'nin arkasındaysa, Socketadresi genellikle ara sunucu adresidir.

Varsayılan olarak, Web uygulamanıza Internet 'ten erişilebilir. İstemcilerle erişimi bilinen IP adresleri veya IP adresi aralıkları listesinden sınırlamak isterseniz, IP adreslerinin listesini eşleşen değerler olarak içeren bir IP eşleştirme kuralı oluşturabilir ve işleç "Not" (Negate true) olarak ayarlar ve **engellenecek** eylemi. Bir IP kısıtlama kuralı uygulandıktan sonra, bu izin verilen listenin dışındaki adreslerden kaynaklanan istekler 403 yasaklanmış bir yanıt alır.

## <a name="configure-a-waf-policy-with-the-azure-portal"></a>Azure portal bir WAF ilkesi yapılandırma

### <a name="prerequisites"></a>Önkoşullar

[Hızlı başlangıç: yüksek kullanılabilirliğe sahip bir genel Web uygulaması Için ön kapı oluşturma](../../frontdoor/quickstart-create-front-door.md)bölümünde açıklanan yönergeleri Izleyerek bir Azure ön kapısı profili oluşturun.

### <a name="create-a-waf-policy"></a>WAF ilkesi oluşturma

1. Azure portal, **kaynak oluştur**' u seçin, arama kutusuna  **Web uygulaması güvenlik duvarı** yazın ve ardından **Web uygulaması güvenlik duvarı (WAF)** seçeneğini belirleyin.
2. **Oluştur**’u seçin.
3. **BIR WAF Ilkesi oluştur** sayfasında, **temel bilgiler** sekmesini gerçekleştirmek için aşağıdaki değerleri kullanın:
   
   |Ayar  |Değer  |
   |---------|---------|
   |İlke     |Global WAF (ön kapı)|
   |Abonelik     |Aboneliğinizi seçin|
   |Kaynak grubu     |Ön kapılarınızın olduğu kaynak grubunu seçin.|
   |İlke adı     |İlkeniz için bir ad yazın|
   |İlke durumu     |Etkin|

   **İleri ' yi seçin: ilke ayarları**

1. **İlke ayarları** sekmesinde **önleme**' yı seçin. **Blok yanıt gövdesi** için, engellendiğini yazın *!* Böylece özel kuralınızın etkin olduğunu görebilirsiniz.
2. **İleri ' yi seçin: yönetilen kurallar**.
3. **İleri ' yi seçin: özel kurallar**.
4. **Özel kural ekle**' yi seçin.
5. **Özel kural ekle** sayfasında, özel bir kural oluşturmak için aşağıdaki test değerlerini kullanın:

   |Ayar  |Değer  |
   |---------|---------|
   |Özel kural adı     |FdWafCustRule|
   |Durum     |Etkin|
   |Kural türü     |Eşleştirme|
   |Öncelik    |100|
   |Eşleşme türü     |IP Adresi|
   |Match değişkeni|RemoteAddr|
   |İşlem|İçermez|
   |IP adresi veya aralığı|10.10.10.0/24|
   |Ardından|Trafiği reddetme|

   :::image type="content" source="../media/waf-front-door-configure-ip-restriction/custom-rule.png" alt-text="Özel kural":::

   **Add (Ekle)** seçeneğini belirleyin.
6. **İleri: ilişkilendirme** öğesini seçin.
7. **Ön uç Konağı Ekle**' yi seçin.
8. **Ön uç Konağı** için ön uç konağını seçin ve **Ekle**' yi seçin.
9. **Gözden geçir ve oluştur**’u seçin.
10. İlke doğrulamanız geçtikten sonra **Oluştur**' u seçin.

### <a name="test-your-waf-policy"></a>WAF ilkenizi test etme

1. WAF ilkesi dağıtımınız tamamlandıktan sonra, ön kapı ön uç ana bilgisayar adınızı inceleyin.
2. Özel blok iletinizi görmeniz gerekir.

   :::image type="content" source="../media/waf-front-door-configure-ip-restriction/waf-rule-test.png" alt-text="WAF kural testi":::

   > [!NOTE]
   > Özel bir IP adresi, kuralın tetikleneceğini güvence altına almak için özel kuralda kasıtlı olarak kullanıldı. Gerçek bir dağıtımda, özel durumunuz için IP adreslerini kullanarak *izin ver* ve *Reddet* kuralları oluşturun.

## <a name="configure-a-waf-policy-with-the-azure-cli"></a>Azure CLı ile bir WAF ilkesi yapılandırma

### <a name="prerequisites"></a>Önkoşullar
Bir IP kısıtlama ilkesini yapılandırmaya başlamadan önce, CLı ortamınızı ayarlayın ve bir Azure ön kapısı profili oluşturun.

#### <a name="set-up-the-azure-cli-environment"></a>Azure CLı ortamını ayarlama
1. [Azure CLI](/cli/azure/install-azure-cli)'yi veya Azure Cloud Shell kullanın. Azure Cloud Shell doğrudan Azure portalının içinde çalıştırabileceğiniz ücretsiz bir Bash kabuğudur. Azure CLI, kabuğa önceden yüklenmiştir ve kabuk, hesabınızla birlikte kullanılacak şekilde yapılandırılmıştır. Aşağıdaki CLı komutlarında **deneyin** düğmesini seçin ve ardından açılan Cloud Shell oturumunda Azure hesabınızda oturum açın. Oturum başladıktan sonra `az extension add --name front-door` Azure ön kapısı uzantısını eklemek için girin.
 2. CLı 'yi Bash içinde yerel olarak kullanıyorsanız, kullanarak Azure 'da oturum açın `az login` .

#### <a name="create-an-azure-front-door-profile"></a>Azure ön kapı profili oluşturma
[Hızlı başlangıç: yüksek kullanılabilirliğe sahip bir genel Web uygulaması Için ön kapı oluşturma](../../frontdoor/quickstart-create-front-door.md)bölümünde açıklanan yönergeleri Izleyerek bir Azure ön kapısı profili oluşturun.

### <a name="create-a-waf-policy"></a>WAF ilkesi oluşturma

[Az Network Front-kapısı WAF-Policy Create](/cli/azure/network/front-door/waf-policy#az_network_front_door_waf_policy_create) komutunu kullanarak bir WAF ilkesi oluşturun. Aşağıdaki örnekte, *ıpallowpolicyexampleclı* ilke adı ' nı benzersiz bir ilke adıyla değiştirin.

```azurecli-interactive 
az network front-door waf-policy create \
  --resource-group <resource-group-name> \
  --subscription <subscription ID> \
  --name IPAllowPolicyExampleCLI
  ```
### <a name="add-a-custom-ip-access-control-rule"></a>Özel bir IP erişim denetimi kuralı ekleme

Az [Network ön kapı WAF-Policy Custom-Rule Create](/cli/azure/network/front-door/waf-policy/rule#az_network_front_door_waf_policy_rule_create) komutunu kullanarak az önce oluşturduğunuz WAF ilkesi için özel bir IP erişim denetimi kuralı ekleyin.

Aşağıdaki örneklerde:
-  *Ipallowpolicyexampleclı* öğesini daha önce oluşturduğunuz benzersiz ilkenize değiştirin.
-  *IP adresi-aralığı-1*, *IP-adres-aralığı-2* ' yi kendi aralığınızla değiştirin.

İlk olarak, önceki adımdan oluşturulan ilke için bir IP izin verme kuralı oluşturun. 
> [!NOTE]
> bir kural bir sonraki adımda eklenecek bir eşleşme koşuluna sahip olması gerektiğinden, **--erteleme** gereklidir.

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
az network front-door waf-policy rule match-condition add \
--match-variable RemoteAddr \
--operator IPMatch \
--values "ip-address-range-1" "ip-address-range-2" \
--negate true \
--name IPAllowListRule \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI 
  ```
                                                   
### <a name="find-the-id-of-a-waf-policy"></a>Bir WAF ilkesinin KIMLIĞINI bulma 
[Az Network ön kapıwaf-Policy Show](/cli/azure/network/front-door/waf-policy#az_network_front_door_waf_policy_show) komutunu kullanarak bir WAF ilkesinin kimliğini bulun. Aşağıdaki örnekteki *ıpallowpolicyexampleclı* değerini, daha önce oluşturduğunuz benzersiz ilkenize göre değiştirin.

   ```azurecli
   az network front-door  waf-policy show \
     --resource-group <resource-group-name> \
     --name IPAllowPolicyExampleCLI
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-front-end-host"></a>Bir WAF ilkesini bir Azure ön kapısının ön uç konağına bağlama

[Az Network ön kapı Update](/cli/azure/network/front-door#az_network_front_door_update) komutunu kullanarak Azure ön kapısı *Webapplicationfirewallpolicylink* kimliğini ilke kimliğine ayarlayın. *Ipallowpolicyexampleclı* öğesini daha önce oluşturduğunuz benzersiz ilkenize değiştirin.

   ```azurecli
   az network front-door update \
     --set FrontendEndpoints[0].WebApplicationFirewallPolicyLink.id=/subscriptions/<subscription ID>/resourcegroups/resource-group-name/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/IPAllowPolicyExampleCLI \
     --name <frontdoor-name>
     --resource-group <resource-group-name>
   ```
Bu örnekte, WAF ilkesi **Frontendendpoints [0]** öğesine uygulanır. WAF ilkesini ön uçlarınızın herhangi birine bağlayabilirsiniz.
> [!Note]
> Bir WAF ilkesini bir Azure ön kapı ön ucuna bağlamak için **Webapplicationfirewallpolicylink** özelliğini tek bir kez ayarlamanız gerekir. Sonraki ilke güncelleştirmeleri ön uca otomatik olarak uygulanır.

## <a name="configure-a-waf-policy-with-azure-powershell"></a>Azure PowerShell bir WAF ilkesi yapılandırma

### <a name="prerequisites"></a>Önkoşullar
Bir IP kısıtlama ilkesini yapılandırmaya başlamadan önce, PowerShell ortamınızı ayarlayın ve bir Azure ön kapısı profili oluşturun.

#### <a name="set-up-your-powershell-environment"></a>PowerShell ortamınızı hazırlama
Azure PowerShell, Azure kaynaklarını yönetmek için [Azure Resource Manager](../../azure-resource-manager/management/overview.md) modelini kullanan bir cmdlet kümesi sağlar.

[Azure PowerShell](/powershell/azure/)'i yerel makinenize yükleyebilir ve herhangi bir PowerShell oturumunda kullanabilirsiniz. Azure kimlik bilgilerinizi kullanarak PowerShell 'de oturum açmak için sayfasındaki yönergeleri izleyin ve sonra az Module ' ü kullanın.

1. Aşağıdaki komutu kullanarak Azure 'a bağlanın ve ardından oturum açmak için etkileşimli bir iletişim kutusu kullanın.
    ```
    Connect-AzAccount
    ```
 2. Bir Azure ön kapısı modülünü yüklemeden önce, PowerShellGet modülünün güncel sürümünün yüklü olduğundan emin olun. Aşağıdaki komutu çalıştırın ve ardından PowerShell 'i yeniden açın.

    ```
    Install-Module PowerShellGet -Force -AllowClobber
    ``` 

3. Aşağıdaki komutu kullanarak az. Frontkapısı modülünü yükler. 
    
    ```
    Install-Module -Name Az.FrontDoor
    ```
### <a name="create-an-azure-front-door-profile"></a>Azure ön kapı profili oluşturma
[Hızlı başlangıç: yüksek kullanılabilirliğe sahip bir genel Web uygulaması Için ön kapı oluşturma](../../frontdoor/quickstart-create-front-door.md)bölümünde açıklanan yönergeleri Izleyerek bir Azure ön kapısı profili oluşturun.

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

Bir eylem tanımlamak ve öncelik ayarlamak için [New-AzFrontDoorWafCustomRuleObject](/powershell/module/Az.FrontDoor/New-azfrontdoorwafcustomruleobject) komutunu kullanın. Aşağıdaki örnekte, listeyle eşleşen istemci IP 'lerinden olmayan istekler engellenir.

```azurepowershell
$IPAllowRule = New-AzFrontDoorWafCustomRuleObject `
-Name "IPAllowRule" `
-RuleType MatchRule `
-MatchCondition $IPMatchCondition `
-Action Block -Priority 1
```

### <a name="configure-a-waf-policy"></a>WAF ilkesini yapılandırma
Kullanarak Azure ön kapısı profilini içeren kaynak grubunun adını bulun `Get-AzResourceGroup` . Ardından, [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy)kullanarak bir WAF ilkesini IP kuralıyla yapılandırın.

```azurepowershell
  $IPAllowPolicyExamplePS = New-AzFrontDoorWafPolicy `
    -Name "IPRestrictionExamplePS" `
    -resourceGroupName <resource-group-name> `
    -Customrule $IPAllowRule`
    -Mode Prevention `
    -EnabledState Enabled
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-front-end-host"></a>Bir WAF ilkesini bir Azure ön kapısının ön uç konağına bağlama

Bir WAF ilke nesnesini var olan bir ön uç konağına bağlayın ve Azure ön kapısının özelliklerini güncelleştirin. İlk olarak, [Get-Azfrontkapısı](/powershell/module/Az.FrontDoor/Get-AzFrontDoor)kullanarak Azure ön kapısı nesnesini alın. Ardından, **Webapplicationfirewallpolicylink** özelliğini, önceki adımda oluşturulan *$IPAllowPolicyExamplePS* kaynak kimliği olarak ayarlayın, [set-azfrontkapısı](/powershell/module/Az.FrontDoor/Set-AzFrontDoor) komutunu kullanın.

```azurepowershell
  $FrontDoorObjectExample = Get-AzFrontDoor `
    -ResourceGroupName <resource-group-name> `
    -Name $frontDoorName
  $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $IPBlockPolicy.Id
  Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
```

> [!NOTE]
> Bu örnekte, WAF ilkesi **Frontendendpoints [0]** öğesine uygulanır. Bir WAF ilkesini, ön uçlarınızın herhangi birine bağlayabilirsiniz. Bir WAF ilkesini bir Azure ön kapı ön ucuna bağlamak için **Webapplicationfirewallpolicylink** özelliğini tek bir kez ayarlamanız gerekir. Sonraki ilke güncelleştirmeleri ön uca otomatik olarak uygulanır.


## <a name="configure-a-waf-policy-with-a-resource-manager-template"></a>Kaynak Yöneticisi şablonuyla bir WAF ilkesi yapılandırma
Bir Azure ön kapısı ilkesi ve özel IP kısıtlama kuralları içeren bir WAF ilkesi oluşturan şablonu görüntülemek için [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip)' a gidin.


## <a name="next-steps"></a>Sonraki adımlar

- [Azure ön kapı profili oluşturmayı](../../frontdoor/quickstart-create-front-door.md)öğrenin.
