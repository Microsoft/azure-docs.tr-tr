---
title: Birden çok IP yapılandırmasında Yük Dengeleme-Azure CLı
titleSuffix: Azure Load Balancer
description: Bu makalede, Azure CLı kullanarak birincil ve ikincil IP yapılandırmalarında yük dengeleme hakkında bilgi edinin.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: 6ac9e362314cc45e6adbdcf1390f70cbe6b05de8
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075973"
---
# <a name="load-balancing-on-multiple-ip-configurations-using-powershell"></a>PowerShell kullanarak birden çok IP yapılandırmasında Yük Dengeleme

> [!div class="op_single_selector"]
> * [Portal](load-balancer-multiple-ip.md)
> * [CLI](load-balancer-multiple-ip-cli.md)
> * [PowerShell](load-balancer-multiple-ip-powershell.md)


Bu makalede, bir ikincil ağ arabirimi (NIC) üzerinde birden çok IP adresi ile Azure Load Balancer nasıl kullanılacağı açıklanır. Bu senaryo için, her biri birincil ve ikincil NIC olan Windows çalıştıran iki sanal makine vardır. İkincil NIC 'lerin her birinin iki IP yapılandırması vardır. Her VM, hem contoso.com hem de fabrikam.com Web sitelerini barındırır. Her Web sitesi, ikincil NIC 'deki IP yapılandırmalarından birine bağlıdır. Her Web sitesi için bir tane olmak üzere, bir Web sitesi için ilgili IP yapılandırmasına trafik dağıtmak üzere iki ön uç IP adresini kullanıma sunmak için Azure Load Balancer kullanırız. Bu senaryo, hem ön uç havuzu IP adresleri hem de ön uçlarda aynı bağlantı noktası numarasını kullanır.

![LB senaryo resmi](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="steps-to-load-balance-on-multiple-ip-configurations"></a>Birden çok IP yapılandırmasında yük dengelemeye yönelik adımlar

Bu makalede özetlenen senaryoya ulaşmak için aşağıdaki adımları izleyin:

1. Azure PowerShell'i yükleyin. Azure PowerShell’in en son sürümünü yükleme, aboneliğinizi seçme ve hesabınızda oturum açma hakkında bilgi almak için bkz. [Azure PowerShell’i yükleme ve yapılandırma](/powershell/azure/overview).
2. Aşağıdaki ayarları kullanarak bir kaynak grubu oluşturun:

    ```powershell
    $location = "westcentralus".
    $myResourceGroup = "contosofabrikam"
    ```

    Daha fazla bilgi için bkz. Adım 2/ [kaynak grubu oluşturma](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json).

3. VM 'lerinizi içerecek [bir kullanılabilirlik kümesi oluşturun](../virtual-machines/windows/tutorial-availability-sets.md?toc=%2fazure%2fload-balancer%2ftoc.json) . Bu senaryo için aşağıdaki komutu kullanın:

    ```powershell
    New-AzAvailabilitySet -ResourceGroupName "contosofabrikam" -Name "myAvailset" -Location "West Central US"
    ```

4. Tek bir NIC ile VM oluşturmayı hazırlamak için [WINDOWS VM oluşturma](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json) makalesinde 3 ile 5 arasındaki yönergeleri izleyin. 6,1 adımını çalıştırın ve adım 6,2 yerine aşağıdakini kullanın:

    ```powershell
    $availset = Get-AzAvailabilitySet -ResourceGroupName "contosofabrikam" -Name "myAvailset"
    New-AzVMConfig -VMName "VM1" -VMSize "Standard_DS1_v2" -AvailabilitySetId $availset.Id
    ```

    Ardından 6,3 ile 6,8 arasında [WINDOWS VM adımları oluştur](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json) ' u doldurun.

5. VM 'lerin her birine ikinci bir IP yapılandırması ekleyin. [Sanal makinelere birden çok IP adresi atama](../virtual-network/virtual-network-multiple-ip-addresses-powershell.md#add) makalesindeki yönergeleri izleyin. Aşağıdaki yapılandırma ayarlarını kullanın:

    ```powershell
    $NicName = "VM1-NIC2"
    $RgName = "contosofabrikam"
    $NicLocation = "West Central US"
    $IPConfigName4 = "VM1-ipconfig2"
    $Subnet1 = Get-AzVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $myVnet
    ```

    Bu öğreticinin amacına uygun olarak ikincil IP yapılandırmalarının genel IP 'Ler ile ilişkilendirilmesi gerekmez. Genel IP ilişkilendirme bölümünü kaldırmak için komutu düzenleyin.

6. VM2 için bu makalenin 4 ile 6 arasındaki adımlarını tekrar doldurun. Bunu yaparken VM adını VM2 ile değiştirdiğinizden emin olun. İkinci VM için bir sanal ağ oluşturmanız gerekmediğini unutmayın. Kullanım çalışmanıza göre yeni bir alt ağ oluşturmayabilir veya oluşturamazsınız.

7. İki genel IP adresi oluşturun ve bunları gösterildiği gibi uygun değişkenlerde saklayın:

    ```powershell
    $publicIP1 = New-AzPublicIpAddress -Name PublicIp1 -ResourceGroupName contosofabrikam -Location 'West Central US' -AllocationMethod Dynamic -DomainNameLabel contoso
    $publicIP2 = New-AzPublicIpAddress -Name PublicIp2 -ResourceGroupName contosofabrikam -Location 'West Central US' -AllocationMethod Dynamic -DomainNameLabel fabrikam

    $publicIP1 = Get-AzPublicIpAddress -Name PublicIp1 -ResourceGroupName contosofabrikam
    $publicIP2 = Get-AzPublicIpAddress -Name PublicIp2 -ResourceGroupName contosofabrikam
    ```

8. İki ön uç IP yapılandırması oluşturun:

    ```powershell
    $frontendIP1 = New-AzLoadBalancerFrontendIpConfig -Name contosofe -PublicIpAddress $publicIP1
    $frontendIP2 = New-AzLoadBalancerFrontendIpConfig -Name fabrikamfe -PublicIpAddress $publicIP2
    ```

9. Arka uç adres havuzlarınızı, bir araştırmayı ve yük dengeleme kurallarınızı oluşturun:

    ```powershell
    $beaddresspool1 = New-AzLoadBalancerBackendAddressPoolConfig -Name contosopool
    $beaddresspool2 = New-AzLoadBalancerBackendAddressPoolConfig -Name fabrikampool

    $healthProbe = New-AzLoadBalancerProbeConfig -Name HTTP -RequestPath 'index.html' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

    $lbrule1 = New-AzLoadBalancerRuleConfig -Name HTTPc -FrontendIpConfiguration $frontendIP1 -BackendAddressPool $beaddresspool1 -Probe $healthprobe -Protocol Tcp -FrontendPort 80 -BackendPort 80
    $lbrule2 = New-AzLoadBalancerRuleConfig -Name HTTPf -FrontendIpConfiguration $frontendIP2 -BackendAddressPool $beaddresspool2 -Probe $healthprobe -Protocol Tcp -FrontendPort 80 -BackendPort 80
    ```

10. Bu kaynakları oluşturduktan sonra yük dengeleyiciyi oluşturun:

    ```powershell
    $mylb = New-AzLoadBalancer -ResourceGroupName contosofabrikam -Name mylb -Location 'West Central US' -FrontendIpConfiguration $frontendIP1 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe
    ```

11. İkinci arka uç adres havuzunu ve ön uç IP yapılandırmasını yeni oluşturulan yük dengeleyicisine ekleyin:

    ```powershell
    $mylb = Get-AzLoadBalancer -Name "mylb" -ResourceGroupName $myResourceGroup | Add-AzLoadBalancerBackendAddressPoolConfig -Name fabrikampool | Set-AzLoadBalancer

    $mylb | Add-AzLoadBalancerFrontendIpConfig -Name fabrikamfe -PublicIpAddress $publicIP2 | Set-AzLoadBalancer
    
    Add-AzLoadBalancerRuleConfig -Name HTTP -LoadBalancer $mylb -FrontendIpConfiguration $frontendIP2 -BackendAddressPool $beaddresspool2 -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80 | Set-AzLoadBalancer
    ```

12. Aşağıdaki komutlar NIC 'Leri alır ve her bir ikincil NIC 'in IP yapılandırmasını yük dengeleyicinin arka uç adres havuzuna ekler:

    ```powershell
    $nic1 = Get-AzNetworkInterface -Name "VM1-NIC2" -ResourceGroupName "MyResourcegroup";
    $nic2 = Get-AzNetworkInterface -Name "VM2-NIC2" -ResourceGroupName "MyResourcegroup";

    $nic1.IpConfigurations[0].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[0]);
    $nic1.IpConfigurations[1].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[1]);
    $nic2.IpConfigurations[0].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[0]);
    $nic2.IpConfigurations[1].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[1]);

    $mylb = $mylb | Set-AzLoadBalancer

    $nic1 | Set-AzNetworkInterface
    $nic2 | Set-AzNetworkInterface
    ```

13. Son olarak, DNS kaynak kayıtlarını Load Balancer ilgili ön uç IP adresini gösterecek şekilde yapılandırmanız gerekir. Etki alanlarınızı Azure DNS barındırabilirsiniz. Load Balancer ile Azure DNS kullanma hakkında daha fazla bilgi için bkz. [diğer Azure hizmetleriyle Azure DNS kullanma](../dns/dns-for-azure-services.md).

## <a name="next-steps"></a>Sonraki adımlar
- Azure 'da yük [Dengeleme hizmetlerini kullanarak](../traffic-manager/traffic-manager-load-balancing-azure.md)Yük Dengeleme hizmetlerini Azure 'da birleştirme hakkında daha fazla bilgi edinin.
- [Azure Load Balancer Için Azure izleyici günlüklerinde](../load-balancer/load-balancer-monitor-log.md)yük dengeleyiciyi yönetmek ve sorunlarını gidermek için Azure 'da farklı türlerdeki günlükleri nasıl kullanabileceğinizi öğrenin.
