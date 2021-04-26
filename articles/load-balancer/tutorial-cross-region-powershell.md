---
title: 'Öğretici: Azure PowerShell kullanarak çapraz bölge yük dengeleyici oluşturma'
titleSuffix: Azure Load Balancer
description: Azure PowerShell kullanarak bir çapraz bölge Azure Load Balancer dağıtmaya yönelik Bu öğreticiye başlayın.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 02/10/2021
ms.openlocfilehash: 88e400cea764be84521c003a681aa74885dc29ce
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101721353"
---
# <a name="tutorial-create-a-cross-region-azure-load-balancer-using-azure-powershell"></a>Öğretici: Azure PowerShell kullanarak bir çapraz bölge Azure Load Balancer oluşturma

Çapraz bölge yük dengeleyici, bir hizmetin birden çok Azure bölgesinde küresel olarak kullanılabilmesini sağlar. Bir bölge başarısız olursa, trafik sonraki en iyi sağlıklı bölgesel yük dengeleyiciye yönlendirilir.  

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Bölgeler arası yük dengeleyici oluşturun.
> * Yük dengeleyici kuralı oluşturun.
> * İki bölgesel yük dengeleyicileri içeren bir arka uç havuzu oluşturun.
> * Yük dengeleyiciyi test edin.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliği.
- İki farklı Azure bölgesinde dağıtılan arka uç havuzlarıyla iki **Standart** SKU Azure Yük dengeleyicileri.
    - Bir bölgesel standart yük dengeleyici ve arka uç havuzları için sanal makineler oluşturma hakkında bilgi için bkz. [hızlı başlangıç: Azure PowerShell kullanarak VM 'lerin yükünü dengelemek için genel yük dengeleyici oluşturma](quickstart-load-balancer-standard-public-powershell.md).
        - Bir **-R1** ve **-R2** ile her bir bölgedeki yük dengeleyiciler ve sanal makinelerin adını ekleyin. 
- Azure PowerShell yerel olarak veya Azure Cloud Shell yüklendi.


PowerShell'i yerel olarak yükleyip kullanmayı tercih ederseniz bu makale, Azure PowerShell modülü 5.4.1 veya sonraki bir sürümünü gerektirir. Yüklü sürümü bulmak için `Get-Module -ListAvailable Az` komutunu çalıştırın. Yükseltmeniz gerekirse, bkz. [Azure PowerShell modülünü yükleme](/powershell/azure/install-Az-ps). PowerShell 'i yerel olarak çalıştırıyorsanız `Connect-AzAccount` Azure ile bir bağlantı oluşturmak için öğesini de çalıştırmanız gerekir.

## <a name="create-cross-region-load-balancer"></a>Çapraz bölge yük dengeleyici oluştur


### <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Azure kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır.

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)ile bir kaynak grubu oluşturun.


```azurepowershell-interactive
$rg = @{
    Name = 'MyResourceGroupLB-CR'
    Location = 'westus'
}
New-AzResourceGroup @rg

```

### <a name="create-cross-region-load-balancer-resources"></a>Bölgeler arası yük dengeleyici kaynakları oluşturma

Bu bölümde, bölgeler arası yük dengeleyici için gereken kaynakları oluşturacaksınız.

Bölgeler arası yük dengeleyicinin ön ucu için genel standart SKU genel IP 'si kullanılır.

* Genel IP adresini oluşturmak için [New-Azpublicıpaddress](/powershell/module/az.network/new-azpublicipaddress) komutunu kullanın.

* [New-Azloadbalancerfrontendıpconfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig)ile bir ön uç IP yapılandırması oluşturun.

* [New-Azloadbalancerbackendadddresspoolconfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig)ile arka uç adres havuzu oluşturun.

* [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig)ile bir yük dengeleyici kuralı oluşturun.

* [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer)ile bir çapraz bölge yük dengeleyici oluşturun.

```azurepowershell-interactive
## Create global IP address for load balancer ##
$ip = @{
    Name = 'myPublicIP-CR'
    ResourceGroupName = 'MyResourceGroupLB-CR'
    Location = 'westus'
    Sku = 'Standard'
    Tier = 'Global'
    AllocationMethod = 'Static'
}
$publicIP = New-AzPublicIpAddress @ip

## Create frontend configuration ##
$fe = @{
    Name = 'myFrontEnd-CR'
    PublicIpAddress = $publicIP
}
$feip = New-AzLoadBalancerFrontendIpConfig @fe

## Create back-end address pool ##
$be = @{
    Name = 'myBackEndPool-CR'
}
$bepool = New-AzLoadBalancerBackendAddressPoolConfig @be

## Create the load balancer rule ##
$rul = @{
    Name = 'myHTTPRule-CR'
    Protocol = 'tcp'
    FrontendPort = '80'
    BackendPort = '80'
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bepool
}
$rule = New-AzLoadBalancerRuleConfig @rul

## Create cross-region load balancer resource ##
$lbp = @{
    ResourceGroupName = 'myResourceGroupLB-CR'
    Name = 'myLoadBalancer-CR'
    Location = 'westus'
    Sku = 'Standard'
    Tier = 'Global'
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bepool
    LoadBalancingRule = $rule
}
$lb = New-AzLoadBalancer @lbp
```

## <a name="configure-backend-pool"></a>Arka uç havuzunu yapılandırma

Bu bölümde, çapraz bölge yük dengeleyicinin arka uç havuzuna iki bölgesel standart yük dengeleyici ekleyeceksiniz.

> [!IMPORTANT]
> Bu adımları tamamlayabilmeniz için, arka uç havuzlarıyla birlikte iki bölgesel yük dengeleyiciler aboneliğinizde dağıtılmış olduğundan emin olun.  Daha fazla bilgi için bkz. **[hızlı başlangıç: Azure PowerShell kullanarak VM 'lerin yükünü dengelemek için genel yük dengeleyici oluşturma](quickstart-load-balancer-standard-public-powershell.md)**.

* Bölgesel yük dengeleyici bilgilerini değişkenlerde depolamak için [Get-AzLoadBalancer](/powershell/module/az.network/get-azloadbalancer) ve [Get-Azloadbalancerfrontendıpconfig](/powershell/module/az.network/get-azloadbalancerfrontendipconfig) komutunu kullanın.

* Yük Dengeleyici için arka uç adres havuzu yapılandırmasını oluşturmak için [New-Azloadbalancerbackendadddressconfig](/powershell/module/az.network/new-azloadbalancerbackendaddressconfig) komutunu kullanın.

* Bölgesel yük dengeleyici ön ucuna bölge içi arka uç havuzuna eklemek için [set-Azloadbalancerbackendadddresspool](/powershell/module/az.network/new-azloadbalancerbackendaddresspool) komutunu kullanın.

```azurepowershell-interactive
## Place the region one load balancer configuration in a variable ##
$region1 = @{
    Name = 'myLoadBalancer-R1'
    ResourceGroupName = 'CreatePubLBQS-rg-r1'
}
$R1 = Get-AzLoadBalancer @region1

## Place the region two load balancer configuration in a variable ##
$region2 = @{
    Name = 'myLoadBalancer-R2'
    ResourceGroupName = 'CreatePubLBQS-rg-r2'
}
$R2 = Get-AzLoadBalancer @region2

## Place the region one load balancer front-end configuration in a variable ##
$region1fe = @{
    Name = 'MyFrontEnd-R1'
    LoadBalancer = $R1
}
$R1FE = Get-AzLoadBalancerFrontendIpConfig @region1fe

## Place the region two load balancer front-end configuration in a variable ##
$region2fe = @{
    Name = 'MyFrontEnd-R2'
    LoadBalancer = $R2
}
$R2FE = Get-AzLoadBalancerFrontendIpConfig @region2fe

## Create the cross-region backend address pool configuration for region 1 ##
$region1ap = @{
    Name = 'MyBackendPoolConfig-R1'
    LoadBalancerFrontendIPConfigurationId = $R1FE.Id
}
$beaddressconfigR1 = New-AzLoadBalancerBackendAddressConfig @region1ap

## Create the cross-region backend address pool configuration for region 2 ##
$region2ap = @{
    Name = 'MyBackendPoolConfig-R2'
    LoadBalancerFrontendIPConfigurationId = $R2FE.Id
}
$beaddressconfigR2 = New-AzLoadBalancerBackendAddressConfig @region2ap

## Apply the backend address pool configuration for the cross-region load balancer ##
$bepoolcr = @{
    ResourceGroupName = 'myResourceGroupLB-CR'
    LoadBalancerName = 'myLoadBalancer-CR'
    Name = 'myBackEndPool-CR'
    LoadBalancerBackendAddress = $beaddressconfigR1,$beaddressconfigR2
}
Set-AzLoadBalancerBackendAddressPool @bepoolcr

```

## <a name="test-the-load-balancer"></a>Yük dengeleyiciyi test etme

Bu bölümde, bölgeler arası yük dengeleyiciyi test edersiniz. Genel IP adresine bir Web tarayıcısında bağlanırsınız.  Bölgesel yük dengeleyici arka uç havuzlarından birindeki sanal makineleri durdurarak yük devretmeyi gözlemleyeceksiniz.

1. Yük dengeleyicinin genel IP adresini almak için [Get-Azpublicıpaddress](/powershell/module/az.network/get-azpublicipaddress) kullanın:

```azurepowershell-interactive
$ip = @{
    Name = 'myPublicIP-CR'
    ResourceGroupName = 'myResourceGroupLB-CR'
}  
Get-AzPublicIPAddress @ip | select IpAddress

```
2. Genel IP adresini kopyalayıp tarayıcınızın adres çubuğuna yapıştırın. IIS Web sunucusunun varsayılan sayfası, tarayıcıda görüntülenir.

3. Bölgesel yük dengeleyicileri birinin arka uç havuzundaki sanal makineleri durdurun.

4. Web tarayıcısını yenileyin ve diğer bölgesel yük dengeleyiciye bağlantının yük devretmesini gözlemleyin.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) komutunu kullanarak kaynak grubunu, yük dengeleyiciyi ve kalan kaynakları kaldırabilirsiniz.

```azurepowershell-interactive
Remove-AzResourceGroup -Name 'myResourceGroupLB-CR'
```

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide şunları yaptınız:

* Genel bir IP adresi oluşturuldu.
* Bir çapraz bölge yük dengeleyici oluşturuldu.
* Yük Dengeleme kuralı oluşturuldu.
* Bölgesel yük dengeleyiciler çapraz bölge yük dengeleyicinin arka uç havuzuna eklendi.
* Yük dengeleyici test edildi.


Nasıl yapılacağını öğrenmek için sonraki makaleye ilerleyin...
> [!div class="nextstepaction"]
> [Kullanılabilirlik alanları arasında yük dengeleyici VM 'Leri](tutorial-load-balancer-standard-public-zone-redundant-portal.md)