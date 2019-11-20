---
title: PowerShell kullanarak bir Azure iç yük dengeleyici oluşturma
titlesuffix: Azure Load Balancer
description: Azure PowerShell modülünü Azure Resource Manager ile birlikte kullanarak iç yük dengeleyici oluşturmayı öğrenin
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
ms.openlocfilehash: b53225334c6a7d61fcee70327df5979af1e424ee
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68275405"
---
# <a name="create-an-internal-load-balancer-by-using-the-azure-powershell-module"></a>Azure PowerShell modülünü kullanarak iç yük dengeleyici oluşturma

> [!div class="op_single_selector"]
> * [Azure portal](../load-balancer/load-balancer-get-started-ilb-arm-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-ilb-arm-ps.md)
> * [Azure CLI](../load-balancer/load-balancer-get-started-ilb-arm-cli.md)
> * [Şablon](../load-balancer/load-balancer-get-started-ilb-arm-template.md)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="get-started-with-the-configuration"></a>Yapılandırmaya başlama

Bu makale Azure PowerShell modülünü Azure Resource Manager ile birlikte kullanarak iç yük dengeleyici oluşturmayı açıklamaktadır. Resource Manager dağıtım modelinde iç yük dengeleyici oluşturmak için gerekli olan nesneleri ayrı ayrı yapılandırılır. Nesneler oluşturulup yapılandırıldıktan sonra birleştirilerek bir yük dengeleyici oluşturulur.

Bir yük dengeleyiciyi dağıtmak için aşağıdaki nesneleri oluşturmanız gerekir:

* Ön uç IP havuzu: Tüm gelen ağ trafiği için özel IP adresi.
* Arka uç adres havuzu: Ön uç IP adresinden yük dengeli trafiği almak için ağ arabirimleri.
* Yük Dengeleme kuralları: Yük Dengeleyici için bağlantı noktası (kaynak ve yerel) yapılandırması.
* Araştırma yapılandırması: Sanal makineler için sistem durumu araştırmaları.
* Gelen NAT kuralları: Sanal makinelere doğrudan erişim için bağlantı noktası kuralları.

Yük dengeleyici bileşenleri hakkında daha fazla bilgi için bkz. [Yük dengeleyici için Azure Resource Manager desteği](load-balancer-arm.md).

Aşağıdaki adımlarda iki sanal makine arasında yük dengeleyici yapılandırma işlemleri açıklanmaktadır.

## <a name="set-up-powershell-to-use-resource-manager"></a>PowerShell’i Resource Manager’ı kullanacak şekilde ayarlama

Azure PowerShell modülünün en güncel üretim sürümüne sahip olduğunuzdan emin olun. PowerShell'in Azure aboneliğinize erişmesi için doğru şekilde yapılandırılması gerekir.

### <a name="step-1-start-powershell"></a>1\. adım: PowerShell'i başlatma

Azure Resource Manager için PowerShell modülünü başlatın.

```azurepowershell-interactive
Connect-AzAccount
```

### <a name="step-2-view-your-subscriptions"></a>2\. adım: Aboneliklerinizi görüntüleyin

Kullanılabilir Azure aboneliklerinizi kontrol edin.

```azurepowershell-interactive
Get-AzSubscription
```

Kimlik doğrulaması isteminde kimlik bilgilerinizi girin.

### <a name="step-3-select-the-subscription-to-use"></a>3\. adım: Kullanılacak aboneliği seçin

Yük dengeleyici dağıtımı için kullanılacak Azure aboneliklerini seçin.

```azurepowershell-interactive
Select-AzSubscription -Subscriptionid "GUID of subscription"
```

### <a name="step-4-choose-the-resource-group-for-the-load-balancer"></a>4\. Adım: Yük Dengeleyici için kaynak grubunu seçin

Yük dengeleyici için yeni bir kaynak grubu oluşturun. Mevcut bir kaynak grubunu kullanıyorsanız bu adımı atlayın.

```azurepowershell-interactive
New-AzResourceGroup -Name NRP-RG -location "West US"
```

Azure Resource Manager, tüm kaynak gruplarının bir konum belirtmesini gerektirir. Konum, kaynak grubundaki tüm kaynaklar için varsayılan değer olarak kullanılır. Yük dengeleyici oluşturmayla ilgili tüm komutlar için her zaman aynı kaynak grubunu kullanın.

Örnekte Batı ABD bölgesinde **NRP-RG** adlı bir kaynak grubu oluşturduk.

## <a name="create-the-virtual-network-and-ip-address-for-the-front-end-ip-pool"></a>Ön uç IP havuzu için sanal ağ ve IP adresi oluşturma

Sanal ağ için bir alt ağ oluşturun ve **$backendSubnet** değişkenine atayın.

```azurepowershell-interactive
$backendSubnet = New-AzVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24
```

Sanal ağ oluşturun.

```azurepowershell-interactive
$vnet= New-AzVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet
```

Sanal ağ oluşturulur. **LB-Subnet-BE** alt ağı **NRPVNet** sanal ağına eklenir. Bu değerler **$vnet** değişkenine atanır.

## <a name="create-the-front-end-ip-pool-and-back-end-address-pool"></a>Ön uç IP havuzu ve arka uç adres havuzunu oluşturma

Gelen trafik için ön uç IP havuzu, yük dengeli trafiği almak için de arka uç adres havuzu oluşturun.

### <a name="step-1-create-a-front-end-ip-pool"></a>1\. adım: Ön uç IP havuzu oluşturma

10.0.2.0/24 alt ağı için 10.0.2.5 özel IP adresine sahip bir ön uç IP havuzu oluşturun. Bu adres, gelen ağ trafiği uç noktasıdır.

```azurepowershell-interactive
$frontendIP = New-AzLoadBalancerFrontendIpConfig -Name LB-Frontend -PrivateIpAddress 10.0.2.5 -SubnetId $vnet.subnets[0].Id
```

### <a name="step-2-create-a-back-end-address-pool"></a>2\. adım: Arka uç adres havuzu oluşturma

Ön uç IP havuzundan gelen trafiği almak için arka uç adres havuzunu oluşturun:

```powershell
$beaddresspool= New-AzLoadBalancerBackendAddressPoolConfig -Name "LB-backend"
```

## <a name="create-the-configuration-rules-probe-and-load-balancer"></a>Yapılandırma kurallarını, araştırmayı ve yük dengeleyiciyi oluşturma

Ön uç IP havuzu ve arka uç adres havuzu oluşturulduktan sonra yük dengeleyici kaynağı kurallarını belirtin.

### <a name="step-1-create-the-configuration-rules"></a>1\. adım: Yapılandırma kurallarını oluşturma

Örnekte aşağıdaki dört kural nesnesi oluşturulur:

* Uzak Masaüstü Protokolü (RDP) için bir gelen NAT kuralı: 3441 numaralı bağlantı noktasına gelen tüm trafiği 3389 numaralı bağlantı noktasına yönlendirir.
* RDP için ikinci bir gelen NAT kuralı: 3442 numaralı bağlantı noktasına gelen tüm trafiği 3389 numaralı bağlantı noktasına yönlendirir.
* Bir sistem durumu araştırma kuralı: Healtharaştırması. aspx yolunun sistem durumunu denetler.
* Yük dengeleyici kuralı: Yük-genel bağlantı noktası 80 ' deki tüm gelen trafiği arka uç adres havuzundaki 80 numaralı yerel bağlantı noktasına dengeler.

```azurepowershell-interactive
$inboundNATRule1= New-AzLoadBalancerInboundNatRuleConfig -Name "RDP1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389

$inboundNATRule2= New-AzLoadBalancerInboundNatRuleConfig -Name "RDP2" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389

$healthProbe = New-AzLoadBalancerProbeConfig -Name "HealthProbe" -RequestPath "HealthProbe.aspx" -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

$lbrule = New-AzLoadBalancerRuleConfig -Name "HTTP" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80
```

### <a name="step-2-create-the-load-balancer"></a>2\. adım: Yük dengeleyiciyi oluşturma

Yük dengeleyiciyi oluşturun ve kural nesnelerini birleştirin (RDP, yük dengeleyici ve sistem durumu araştırması için gelen NAT):

```azurepowershell-interactive
$NRPLB = New-AzLoadBalancer -ResourceGroupName "NRP-RG" -Name "NRP-LB" -Location "West US" -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe
```

## <a name="create-the-network-interfaces"></a>Ağ arabirimlerini oluşturma

İç yük dengeleyiciyi oluşturduktan sonra gelen yük dengeli ağ trafiğini alacak olan ağ arabirimlerini (NIC), NAT kurallarını ve araştırmayı tanımlamanız gerekir. Her ağ arabirimi bağımsız olarak yapılandırılır ve sonrasında bir sanal makineye atanır.

### <a name="step-1-create-the-first-network-interface"></a>1\. adım: İlk ağ arabirimini oluşturma

Kaynak sanal ağını ve alt ağını alın. Bu değerler ağ arabirimlerini oluşturmak için kullanılır:

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG

$backendSubnet = Get-AzVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet
```

İlk ağ arabirimini oluşturun ve **lb-nic1-be** adını verin. Arabirimi yük dengeleyici arka uç havuzuna atayın. RDP için ilk NAT kuralını bu NIC ile ilişkilendirin:

```azurepowershell-interactive
$backendnic1= New-AzNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic1-be -Location "West US" -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]
```

### <a name="step-2-create-the-second-network-interface"></a>2\. adım: İkinci ağ arabirimini oluşturma

İkinci ağ arabirimini oluşturun ve **lb-nic2-be** adını verin. İkinci arabirimini birinci arabirimle aynı yük dengeleyici arka uç havuzuna atayın. RDP için ikinci NIC birimini ikinci NAT kuralıyla ilişkilendirin:

```azurepowershell-interactive
$backendnic2= New-AzNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic2-be -Location "West US" -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]
```

Yapılandırmayı gözden geçirin:

    $backendnic1

Ayarlar aşağıdaki gibi olmalıdır:

    Name                 : lb-nic1-be
    ResourceGroupName    : NRP-RG
    Location             : westus
    Id                   : /subscriptions/[Id]/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
    Etag                 : W/"d448256a-e1df-413a-9103-a137e07276d1"
    ProvisioningState    : Succeeded
    Tags                 :
    VirtualMachine       : null
    IpConfigurations     : [
                         {
                           "PrivateIpAddress": "10.0.2.6",
                           "PrivateIpAllocationMethod": "Static",
                           "Subnet": {
                             "Id": "/subscriptions/[Id]/resourceGroups/NRP-RG/providers/Microsoft.Network/virtualNetworks/NRPVNet/subnets/LB-Subnet-BE"
                           },
                           "PublicIpAddress": {
                             "Id": null
                           },
                           "LoadBalancerBackendAddressPools": [
                             {
                               "Id": "/subscriptions/[Id]/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/backendAddressPools/LB-backend"
                             }
                           ],
                           "LoadBalancerInboundNatRules": [
                             {
                               "Id": "/subscriptions/[Id]/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/inboundNatRules/RDP1"
                             }
                           ],
                           "ProvisioningState": "Succeeded",
                           "Name": "ipconfig1",
                           "Etag": "W/\"d448256a-e1df-413a-9103-a137e07276d1\"",
                           "Id": "/subscriptions/[Id]/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be/ipConfigurations/ipconfig1"
                         }
                       ]
    DnsSettings          : {
                         "DnsServers": [],
                         "AppliedDnsServers": []
                       }
    AppliedDnsSettings   :
    NetworkSecurityGroup : null
    Primary              : False



### <a name="step-3-assign-the-nic-to-a-vm"></a>3\. adım: NIC 'yi bir VM 'ye atama

`Add-AzVMNetworkInterface` komutunu kullanarak NIC birimini bir sanal makineye atayın.

Sanal makine oluşturma ve NIC atama ile ilgili adım adım talimatlar için bkz. [PowerShell kullanarak Azure VM oluşturma](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json).

## <a name="add-the-network-interface"></a>Ağ arabirimini ekleme

Sanal makine oluşturulduktan sonra ağ arabirimini ekleyin.

### <a name="step-1-store-the-load-balancer-resource"></a>1\. adım: Yük dengeleyici kaynağını depolayın

Yük dengeleyici kaynağını bir değişkene depolayın (henüz yapmadıysanız). Değişken adı olarak **$lb** kullanıyoruz. Betikteki öznitelik değerleri için önceki adımlarda oluşturulan yük dengeleyici kaynaklarının adlarını kullanın.

```azurepowershell-interactive
$lb = Get-AzLoadBalancer –name NRP-LB -resourcegroupname NRP-RG
```

### <a name="step-2-store-the-back-end-configuration"></a>2\. adım: Arka uç yapılandırmasını depolama

Arka uç yapılandırmasını **$backend** değişkenine depolayın.

```azurepowershell-interactive
$backend = Get-AzLoadBalancerBackendAddressPoolConfig -name LB-backend -LoadBalancer $lb
```

### <a name="step-3-store-the-network-interface"></a>3\. adım: Ağ arabirimini depolayın

Ağ arabirimini başka bir değişkende depolayın. Bu arabirim "Ağ arabirimlerini oluşturma, 1. Adım" bölümünde oluşturulmuştu. Değişken adı olarak **$nic1** kullanıyoruz. Önceki örnekte kullanılan ağ arabirimi adını kullanın.

```azurepowershell-interactive
$nic = Get-AzNetworkInterface –name lb-nic1-be -resourcegroupname NRP-RG
```

### <a name="step-4-change-the-back-end-configuration"></a>4\. Adım: Arka uç yapılandırmasını değiştirme

Ağ arabiriminde arka uç yapılandırmasını değiştirin.

```azurepowershell-interactive
$nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$backend
```

### <a name="step-5-save-the-network-interface-object"></a>5\. Adım: Ağ arabirimi nesnesini kaydetme

Ağ arabirimi nesnesini kaydedin.

```azurepowershell-interactive
Set-AzNetworkInterface -NetworkInterface $nic
```

Arabirim arka uç havuzuna eklendikten sonra ağ trafiğine kurallara göre yük dengeleme uygulanır. Bu kurallar "Yapılandırma kurallarını, araştırmayı ve yük dengeleyiciyi oluşturma" adımında yapılandırılmıştır.

## <a name="update-an-existing-load-balancer"></a>Mevcut yük dengeleyiciyi güncelleştirme

### <a name="step-1-assign-the-load-balancer-object-to-a-variable"></a>1\. adım: Yük dengeleyici nesnesini bir değişkene atama

Yük dengeleyici nesnesini (önceki örnekte oluşturulan) **$slb** değişkenine atamak için `Get-AzLoadBalancer` komutunu kullanın:

```azurepowershell-interactive
$slb = Get-AzLoadBalancer -Name NRP-LB -ResourceGroupName NRP-RG
```

### <a name="step-2-add-a-nat-rule"></a>2\. adım: NAT kuralı ekleme

Var olan bir yük dengeleyiciye yeni bir gelen NAT kuralı ekleyin. Ön uç havuzu için 81, arka uç havuzu için de 8181 numaralı bağlantı noktasını kullanın:

```azurepowershell-interactive
$slb | Add-AzLoadBalancerInboundNatRuleConfig -Name NewRule -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -FrontendPort 81  -BackendPort 8181 -Protocol Tcp
```

### <a name="step-3-save-the-configuration"></a>3\. adım: Yapılandırmayı kaydetme

`Set-AzureLoadBalancer` komutunu kullanarak yeni yapılandırmayı kaydedin:

```azurepowershell-interactive
$slb | Set-AzLoadBalancer
```

## <a name="remove-an-existing-load-balancer"></a>Var olan yük dengeleyiciyi kaldırma

`Remove-AzLoadBalancer` komutunu kullanarak **NRP-RG** kaynak grubundaki **NRP-LB** yük dengeleyiciyi silin:

```azurepowershell-interactive
Remove-AzLoadBalancer -Name NRP-LB -ResourceGroupName NRP-RG
```

> [!NOTE]
> İsteğe bağlı **-Force** anahtarını kullanarak silme onayı istemini engelleyebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

* [Yük dengeleyici dağıtım modunu yapılandırma](load-balancer-distribution-mode.md)
* [Yük dengeleyiciniz için boşta TCP zaman aşımı ayarlarını yapılandırma](load-balancer-tcp-idle-timeout.md)