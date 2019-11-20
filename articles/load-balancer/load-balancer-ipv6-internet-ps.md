---
title: IPv6 ile Internet 'e yönelik yük dengeleyici oluşturma-Azure PowerShell
titleSuffix: Azure Load Balancer
description: Kaynak Yöneticisi için PowerShell kullanarak Internet 'e yönelik yük dengeleyici oluşturmayı öğrenin
services: load-balancer
documentationcenter: na
author: asudbring
keywords: IPv6, azure yük dengeleyici, ikili yığın, genel IP, yerel IPv6, mobil veya IOT
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: fb697003da8c0604b2ce1e8956fcd434014b5b82
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74077046"
---
# <a name="get-started-creating-an-internet-facing-load-balancer-with-ipv6-using-powershell-for-resource-manager"></a>Kaynak Yöneticisi için PowerShell kullanarak Internet 'e yönelik yük dengeleyici oluşturmaya başlama

> [!div class="op_single_selector"]
> * [PowerShell](load-balancer-ipv6-internet-ps.md)
> * [Azure CLI](load-balancer-ipv6-internet-cli.md)
> * [Şablon](load-balancer-ipv6-internet-template.md)

>[! NOTE: IPv6 için En Iyi uygulamada değişiklik yapın] Bu makalede temel yük dengeleyiciler hem IPv4 hem de IPv6 bağlantısı sağlamasına izin veren bir giriş IPv6 özelliği açıklanır.  Daha kapsamlı IPv6 bağlantısı artık, sanal ağlarınızla IPv6 bağlantısını tümleştiren ve IPv6 ağ güvenlik grubu kuralları, IPv6 Kullanıcı tanımlı yönlendirme, IPv6 temel ve standart yük dengeleme gibi önemli özellikleri ve daha fazlasını içeren [Azure VNET 'ler Için IPv6](../virtual-network/ipv6-overview.md) ile kullanılabilir.  Azure sanal ağları için IPv6, Azure 'daki IPv6 uygulamaları için önerilen en iyi uygulamadır. 
>Lütfen bkz. [Azure VNET PowerShell dağıtımı Için IPv6](../virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md) 

Azure Load Balancer bir Katman 4 (TCP, UDP) yük dengeleyicidir. Yük dengeleyici, gelen trafiği bulut hizmetlerindeki sağlıklı hizmet örnekleri veya bir yük dengeleyici kümesindeki sanal makineler arasında dağıtarak yüksek kullanılabilirlik sağlar. Ayrıca, Azure Load Balancer bu hizmetleri birden çok bağlantı noktasında, birden çok IP adresinde ya da her ikisinde birden sağlayabilir.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="example-deployment-scenario"></a>Örnek dağıtım senaryosu

Aşağıdaki diyagramda, bu makalede dağıtılan yük dengeleme çözümü gösterilmektedir.

![Yük dengeleyici senaryosu](./media/load-balancer-ipv6-internet-ps/lb-ipv6-scenario.png)

Bu senaryoda, aşağıdaki Azure kaynaklarını oluşturacaksınız:

* IPv4 ve IPv6 genel IP adresi ile Internet 'e yönelik Load Balancer
* Genel VIP 'leri özel uç noktalarla eşlemek için iki yük dengeleme kuralı
* İki VM 'yi içeren bir kullanılabilirlik kümesi
* iki sanal makine (VM)
* Her VM için hem IPv4 hem de IPv6 adresi atanmış bir sanal ağ arabirimi

## <a name="deploying-the-solution-using-the-azure-powershell"></a>Azure PowerShell kullanarak çözümü dağıtma

Aşağıdaki adımlarda, PowerShell ile Azure Resource Manager kullanarak Internet 'e yönelik yük dengeleyicinin nasıl oluşturulacağı gösterilmektedir. Azure Resource Manager, her kaynak ayrı ayrı oluşturulup yapılandırılır ve ardından bir kaynak oluşturmak için bir araya konur.

Yük dengeleyici dağıtmak için aşağıdaki nesneleri oluşturun ve yapılandırın:

* Ön uç IP yapılandırması: Gelen ağ trafiği için genel IP adreslerini içerir.
* Arka uç adres havuzu: Sanal makinelerin yük dengeleyiciden ağ trafiği alması için ağ arabirimlerini (NIC’ler) içerir.
* Yük dengeleme kuralları: Yük dengeleyici üzerindeki bir genel bağlantı noktasını arka uç adres havuzundaki bağlantı noktasına eşleme kurallarını içerir.
* Gelen NAT kuralları: Yük dengeleyici üzerindeki bir genel bağlantı noktasını arka uç adres havuzundaki belirli bir sanal makineye ait bağlantı noktasına eşleme kurallarını içerir.
* Araştırmalar: Arka uç adres havuzundaki sanal makine örneklerinin kullanılabilirliğini kontrol etmek için kullanılan durum araştırmalarını içerir.

Daha fazla bilgi için bkz. [Yük Dengeleyici için Azure Resource Manager desteği](load-balancer-arm.md).

## <a name="set-up-powershell-to-use-resource-manager"></a>PowerShell’i Resource Manager’ı kullanacak şekilde ayarlama

PowerShell için Azure Resource Manager modülünün en son üretim sürümüne sahip olduğunuzdan emin olun.

1. Azure 'da oturum açın

    ```azurepowershell-interactive
    Connect-AzAccount
    ```

    İstendiğinde kimlik bilgilerinizi girin.

2. Hesapla ilişkili abonelikleri kontrol etme

   ```azurepowershell-interactive
    Get-AzSubscription
    ```

3. Hangi Azure aboneliğinizin kullanılacağını seçin.

    ```azurepowershell-interactive
    Select-AzSubscription -SubscriptionId 'GUID of subscription'
    ```

4. Bir kaynak grubu oluşturun (mevcut bir kaynak grubu kullanıyorsanız bu adımı atlayın)

    ```azurepowershell-interactive
    New-AzResourceGroup -Name NRP-RG -location "West US"
    ```

## <a name="create-a-virtual-network-and-a-public-ip-address-for-the-front-end-ip-pool"></a>Ön uç IP havuzu için sanal ağ ve genel IP adresi oluşturma

1. Alt ağ içeren bir sanal ağ oluşturun.

    ```azurepowershell-interactive
    $backendSubnet = New-AzVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24
    $vnet = New-AzvirtualNetwork -Name VNet -ResourceGroupName NRP-RG -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet
    ```

2. Ön uç IP adresi havuzu için Azure genel IP adresi (PıP) kaynaklarını oluşturun. Aşağıdaki komutları çalıştırmadan önce `-DomainNameLabel` değerini değiştirdiğinizden emin olun. Değer, Azure bölgesi içinde benzersiz olmalıdır.

    ```azurepowershell-interactive
    $publicIPv4 = New-AzPublicIpAddress -Name 'pub-ipv4' -ResourceGroupName NRP-RG -Location 'West US' -AllocationMethod Static -IpAddressVersion IPv4 -DomainNameLabel lbnrpipv4
    $publicIPv6 = New-AzPublicIpAddress -Name 'pub-ipv6' -ResourceGroupName NRP-RG -Location 'West US' -AllocationMethod Dynamic -IpAddressVersion IPv6 -DomainNameLabel lbnrpipv6
    ```

    > [!IMPORTANT]
    > Yük dengeleyici, FQDN 'sinin ön eki olarak genel IP 'nin etki alanı etiketini kullanır. Bu örnekte, FQDN 'Ler *lbnrpipv4.westus.cloudapp.Azure.com* ve *lbnrpipv6.westus.cloudapp.Azure.com*' dir.

## <a name="create-a-front-end-ip-configurations-and-a-back-end-address-pool"></a>Ön uç IP yapılandırması ve arka uç adres havuzu oluşturma

1. Oluşturduğunuz genel IP adreslerini kullanan ön uç adres yapılandırması oluşturun.

    ```azurepowershell-interactive
    $FEIPConfigv4 = New-AzLoadBalancerFrontendIpConfig -Name "LB-Frontendv4" -PublicIpAddress $publicIPv4
    $FEIPConfigv6 = New-AzLoadBalancerFrontendIpConfig -Name "LB-Frontendv6" -PublicIpAddress $publicIPv6
    ```

2. Arka uç adres havuzları oluşturun.

    ```azurepowershell-interactive
    $backendpoolipv4 = New-AzLoadBalancerBackendAddressPoolConfig -Name "BackendPoolIPv4"
    $backendpoolipv6 = New-AzLoadBalancerBackendAddressPoolConfig -Name "BackendPoolIPv6"
    ```

## <a name="create-lb-rules-nat-rules-a-probe-and-a-load-balancer"></a>LB kuralları, NAT kuralları, araştırma ve yük dengeleyici oluşturma

Bu örnek aşağıdaki nesneleri oluşturur:

* 443 numaralı bağlantı noktasına gelen tüm trafiği 4443 numaralı bağlantı noktasına çevirecek bir NAT kuralı
* 80 numaralı bağlantı noktasına gelen tüm trafiği arka uç havuzundaki adreslerin 80 numaralı bağlantı noktasıyla dengeleyen yük dengeleyici kuralı.
* 3389 numaralı bağlantı noktasındaki VM 'lere RDP bağlantısı sağlayan yük dengeleyici kuralı.
* *healtharaştırma. aspx* adlı bir sayfada sistem durumunu denetlemek için araştırma kuralı veya 8080 numaralı bağlantı noktası
* Tüm bu nesneleri kullanan bir yük dengeleyici

1. NAT kurallarını oluşturun.

    ```azurepowershell-interactive
    $inboundNATRule1v4 = New-AzLoadBalancerInboundNatRuleConfig -Name "NicNatRulev4" -FrontendIpConfiguration $FEIPConfigv4 -Protocol TCP -FrontendPort 443 -BackendPort 4443
    $inboundNATRule1v6 = New-AzLoadBalancerInboundNatRuleConfig -Name "NicNatRulev6" -FrontendIpConfiguration $FEIPConfigv6 -Protocol TCP -FrontendPort 443 -BackendPort 4443
    ```

2. Durum araştırması oluşturun. Araştırmaları iki şekilde yapılandırabilirsiniz:

    HTTP araştırma

    ```azurepowershell-interactive
    $healthProbe = New-AzLoadBalancerProbeConfig -Name 'HealthProbe-v4v6' -RequestPath 'HealthProbe.aspx' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2
    ```

    veya TCP araştırması

    ```azurepowershell-interactive
    $healthProbe = New-AzLoadBalancerProbeConfig -Name 'HealthProbe-v4v6' -Protocol Tcp -Port 8080 -IntervalInSeconds 15 -ProbeCount 2
    $RDPprobe = New-AzLoadBalancerProbeConfig -Name 'RDPprobe' -Protocol Tcp -Port 3389 -IntervalInSeconds 15 -ProbeCount 2
    ```

    Bu örnekte, TCP araştırmalarını kullanacağız.

3. Yük dengeleyici kuralı oluşturun.

    ```azurepowershell-interactive
    $lbrule1v4 = New-AzLoadBalancerRuleConfig -Name "HTTPv4" -FrontendIpConfiguration $FEIPConfigv4 -BackendAddressPool $backendpoolipv4 -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 8080
    $lbrule1v6 = New-AzLoadBalancerRuleConfig -Name "HTTPv6" -FrontendIpConfiguration $FEIPConfigv6 -BackendAddressPool $backendpoolipv6 -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 8080
    $RDPrule = New-AzLoadBalancerRuleConfig -Name "RDPrule" -FrontendIpConfiguration $FEIPConfigv4 -BackendAddressPool $backendpoolipv4 -Probe $RDPprobe -Protocol Tcp -FrontendPort 3389 -BackendPort 3389
    ```

4. Daha önce oluşturulmuş nesneleri kullanarak yük dengeleyici oluşturun.

    ```azurepowershell-interactive
    $NRPLB = New-AzLoadBalancer -ResourceGroupName NRP-RG -Name 'myNrpIPv6LB' -Location 'West US' -FrontendIpConfiguration $FEIPConfigv4,$FEIPConfigv6 -InboundNatRule $inboundNATRule1v6,$inboundNATRule1v4 -BackendAddressPool $backendpoolipv4,$backendpoolipv6 -Probe $healthProbe,$RDPprobe -LoadBalancingRule $lbrule1v4,$lbrule1v6,$RDPrule
    ```

## <a name="create-nics-for-the-back-end-vms"></a>Arka uç VM 'Leri için NIC oluşturma

1. NIC 'Lerin oluşturulması gereken sanal ağ ve sanal ağ alt ağını alın.

    ```azurepowershell-interactive
    $vnet = Get-AzVirtualNetwork -Name VNet -ResourceGroupName NRP-RG
    $backendSubnet = Get-AzVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet
    ```

2. VM 'Ler için IP yapılandırması ve NIC 'Ler oluşturun.

    ```azurepowershell-interactive
    $nic1IPv4 = New-AzNetworkInterfaceIpConfig -Name "IPv4IPConfig" -PrivateIpAddressVersion "IPv4" -Subnet $backendSubnet -LoadBalancerBackendAddressPool $backendpoolipv4 -LoadBalancerInboundNatRule $inboundNATRule1v4
    $nic1IPv6 = New-AzNetworkInterfaceIpConfig -Name "IPv6IPConfig" -PrivateIpAddressVersion "IPv6" -LoadBalancerBackendAddressPool $backendpoolipv6 -LoadBalancerInboundNatRule $inboundNATRule1v6
    $nic1 = New-AzNetworkInterface -Name 'myNrpIPv6Nic0' -IpConfiguration $nic1IPv4,$nic1IPv6 -ResourceGroupName NRP-RG -Location 'West US'

    $nic2IPv4 = New-AzNetworkInterfaceIpConfig -Name "IPv4IPConfig" -PrivateIpAddressVersion "IPv4" -Subnet $backendSubnet -LoadBalancerBackendAddressPool $backendpoolipv4
    $nic2IPv6 = New-AzNetworkInterfaceIpConfig -Name "IPv6IPConfig" -PrivateIpAddressVersion "IPv6" -LoadBalancerBackendAddressPool $backendpoolipv6
    $nic2 = New-AzNetworkInterface -Name 'myNrpIPv6Nic1' -IpConfiguration $nic2IPv4,$nic2IPv6 -ResourceGroupName NRP-RG -Location 'West US'
    ```

## <a name="create-virtual-machines-and-assign-the-newly-created-nics"></a>Sanal makineler oluşturma ve yeni oluşturulan NIC 'Leri atama

VM oluşturma hakkında daha fazla bilgi için, bkz. [Kaynak Yöneticisi ve Azure PowerShell bir Windows sanal makinesi oluşturma ve önceden](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json) yapılandırma

1. Kullanılabilirlik kümesi ve depolama hesabı oluşturma

    ```azurepowershell-interactive
    New-AzAvailabilitySet -Name 'myNrpIPv6AvSet' -ResourceGroupName NRP-RG -location 'West US'
    $availabilitySet = Get-AzAvailabilitySet -Name 'myNrpIPv6AvSet' -ResourceGroupName NRP-RG
    New-AzStorageAccount -ResourceGroupName NRP-RG -Name 'mynrpipv6stacct' -Location 'West US' -SkuName "Standard_LRS"
    $CreatedStorageAccount = Get-AzStorageAccount -ResourceGroupName NRP-RG -Name 'mynrpipv6stacct'
    ```

2. Her VM oluşturma ve önceki oluşturulan NIC 'Leri atama

    ```azurepowershell-interactive
    $mySecureCredentials= Get-Credential -Message "Type the username and password of the local administrator account."

    $vm1 = New-AzVMConfig -VMName 'myNrpIPv6VM0' -VMSize 'Standard_G1' -AvailabilitySetId $availabilitySet.Id
    $vm1 = Set-AzVMOperatingSystem -VM $vm1 -Windows -ComputerName 'myNrpIPv6VM0' -Credential $mySecureCredentials -ProvisionVMAgent -EnableAutoUpdate
    $vm1 = Set-AzVMSourceImage -VM $vm1 -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
    $vm1 = Add-AzVMNetworkInterface -VM $vm1 -Id $nic1.Id -Primary
    $osDisk1Uri = $CreatedStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/myNrpIPv6VM0osdisk.vhd"
    $vm1 = Set-AzVMOSDisk -VM $vm1 -Name 'myNrpIPv6VM0osdisk' -VhdUri $osDisk1Uri -CreateOption FromImage
    New-AzVM -ResourceGroupName NRP-RG -Location 'West US' -VM $vm1

    $vm2 = New-AzVMConfig -VMName 'myNrpIPv6VM1' -VMSize 'Standard_G1' -AvailabilitySetId $availabilitySet.Id
    $vm2 = Set-AzVMOperatingSystem -VM $vm2 -Windows -ComputerName 'myNrpIPv6VM1' -Credential $mySecureCredentials -ProvisionVMAgent -EnableAutoUpdate
    $vm2 = Set-AzVMSourceImage -VM $vm2 -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
    $vm2 = Add-AzVMNetworkInterface -VM $vm2 -Id $nic2.Id -Primary
    $osDisk2Uri = $CreatedStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/myNrpIPv6VM1osdisk.vhd"
    $vm2 = Set-AzVMOSDisk -VM $vm2 -Name 'myNrpIPv6VM1osdisk' -VhdUri $osDisk2Uri -CreateOption FromImage
    New-AzVM -ResourceGroupName NRP-RG -Location 'West US' -VM $vm2
    ```


