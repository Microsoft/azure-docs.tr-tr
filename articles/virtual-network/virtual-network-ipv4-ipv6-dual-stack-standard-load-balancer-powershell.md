---
title: Azure sanal ağ 'da IPv6 ikili yığın uygulaması dağıtma-PowerShell
titlesuffix: Azure Virtual Network
description: Bu makalede, Azure PowerShell kullanarak Azure sanal ağı 'nda Standart Load Balancer IPv6 ikili yığını uygulamasının nasıl dağıtılacağı gösterilmektedir.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/08/2019
ms.author: kumud
ms.openlocfilehash: c924e59a50994827eb2e9be40caa7021c7e4ac3c
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72174478"
---
# <a name="deploy-an-ipv6-dual-stack-application-in-azure---powershell-preview"></a>Azure 'da IPv6 ikili yığın uygulaması dağıtma-PowerShell (Önizleme)

Bu makalede, bir çift yığın sanal ağı ve alt ağı içeren bir çift yığın (IPv4 + IPv6) Standart Load Balancer uygulamasının nasıl dağıtılacağı gösterilmektedir. Bu, Çift (IPv4 + IPv6) ön uç yapılandırmalarına sahip bir Standart Load Balancer, Çift IP yapılandırması, ağ güvenlik grubu ve genel IP 'Ler.

> [!Important]
> Azure sanal ağ için IPv6 desteği şu anda genel önizlemededir. Bu önizleme, bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yükleri için önerilmez. Bazı özellikler desteklenmeyebilir veya kısıtlı özelliklere sahip olabilir. Ayrıntılar için [Microsoft Azure önizlemeleri Için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) bölümüne bakın.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

PowerShell 'i yerel olarak yükleyip kullanmayı tercih ederseniz, bu makale Azure PowerShell modülü sürümü 6.9.0 veya üstünü gerektirir. Yüklü sürümü bulmak için `Get-Module -ListAvailable Az` ' i çalıştırın. Yükseltmeniz gerekiyorsa bkz. [ınstall Azure PowerShell Module](/powershell/azure/install-Az-ps). PowerShell 'i yerel olarak çalıştırıyorsanız, Azure ile bir bağlantı oluşturmak için `Connect-AzAccount` ' ı da çalıştırmanız gerekir.

## <a name="prerequisites"></a>Önkoşullar
Azure 'da bir çift yığın uygulamasını dağıtmadan önce, aşağıdaki Azure PowerShell kullanarak aboneliğinizi bu önizleme özelliği için yapılandırmanız gerekir:

Aşağıdaki gibi kaydolun:
```azurepowershell
Register-AzProviderFeature -FeatureName AllowIPv6VirtualNetwork -ProviderNamespace Microsoft.Network
Register-AzProviderFeature -FeatureName AllowIPv6CAOnStandardLB -ProviderNamespace Microsoft.Network
```
Özellik kaydının tamamlanabilmesi 30 dakika kadar sürer. Aşağıdaki Azure PowerShell komutunu çalıştırarak kayıt durumunuzu kontrol edebilirsiniz: kaydı şu şekilde denetleyin:
```azurepowershell
Get-AzProviderFeature -FeatureName AllowIPv6VirtualNetwork -ProviderNamespace Microsoft.Network
Get-AzProviderFeature -FeatureName AllowIPv6CAOnStandardLB -ProviderNamespace 
```
Kayıt tamamlandıktan sonra aşağıdaki komutu çalıştırın:

```azurepowershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Çift yığın Sanal ağınızı oluşturabilmeniz için, [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)ile bir kaynak grubu oluşturmanız gerekir. Aşağıdaki örnek, *Doğu ABD* konumunda *Myrgdualstack* adlı bir kaynak grubu oluşturur:

```azurepowershell-interactive
   $rg = New-AzResourceGroup `
  -ResourceGroupName "dsRG1"  `
  -Location "east us"
```

## <a name="create-ipv4-and-ipv6-public-ip-addresses"></a>IPv4 ve IPv6 genel IP adresleri oluşturma
Sanal makinelerinize Internet 'ten erişmek için yük dengeleyici için IPv4 ve IPv6 genel IP adreslerinin olması gerekir. [New-Azpublicıpaddress](/powershell/module/az.network/new-azpublicipaddress)Ile genel IP adresleri oluşturun. Aşağıdaki örnek *dsRG1* kaynak grubunda *dsPublicIP_v4* ve *dsPublicIP_v6* adlı IPv4 ve IPv6 genel IP adresi oluşturur:

```azurepowershell-interactive
$PublicIP_v4 = New-AzPublicIpAddress `
  -Name "dsPublicIP_v4" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Static `
  -IpAddressVersion IPv4 `
  -Sku Standard
  
$PublicIP_v6 = New-AzPublicIpAddress `
  -Name "dsPublicIP_v6" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Static `
  -IpAddressVersion IPv6 `
  -Sku Standard
```
RDP bağlantısı kullanarak sanal makinelerinize erişmek için, [New-Azpublicıpaddress](/powershell/module/az.network/new-azpublicipaddress)ile sanal makineler IÇIN bir ıPV4 genel IP adresi oluşturun.

```azurepowershell-interactive
  $RdpPublicIP_1 = New-AzPublicIpAddress `
  -Name "RdpPublicIP_1" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Static `
  -Sku Standard `
  -IpAddressVersion IPv4
  
  $RdpPublicIP_2 = New-AzPublicIpAddress `
   -Name "RdpPublicIP_2" `
   -ResourceGroupName $rg.ResourceGroupName `
   -Location $rg.Location  `
   -AllocationMethod Static `
   -Sku Standard `
   -IpAddressVersion IPv4
```

## <a name="create-standard-load-balancer"></a>Standart Load Balancer oluştur

Bu bölümde, yük dengeleyici için çift ön uç IP (IPv4 ve IPv6) ve arka uç adres havuzunu yapılandırır ve ardından bir Standart Load Balancer oluşturursunuz.

### <a name="create-front-end-ip"></a>Ön uç IP oluşturma

[New-Azloadbalancerfrontendıpconfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig)ile bir ön uç IP 'si oluşturun. Aşağıdaki örnek, *dsLbFrontEnd_v4* ve *dsLbFrontEnd_v6*adlı IPv4 ve IPv6 ön uç IP yapılandırması oluşturur:

```azurepowershell-interactive
$frontendIPv4 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v4" `
  -PublicIpAddress $PublicIP_v4

$frontendIPv6 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v6" `
  -PublicIpAddress $PublicIP_v6

```

### <a name="configure-back-end-address-pool"></a>Arka uç adres havuzunu yapılandırma

[New-Azloadbalancerbackendadddresspoolconfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig)ile arka uç adres havuzu oluşturun. VM 'Ler, kalan adımlarda bu arka uç havuzuna ekler. Aşağıdaki örnek, *dsLbBackEndPool_v4* ve *dsLbBackEndPool_v6* adlı arka uç adres havuzlarını hem ıPV4 hem de IPv6 NIC yapılandırmalarına sahip VM 'leri içerecek şekilde oluşturur:

```azurepowershell-interactive
$backendPoolv4 = New-AzLoadBalancerBackendAddressPoolConfig `
-Name "dsLbBackEndPool_v4"

$backendPoolv6 = New-AzLoadBalancerBackendAddressPoolConfig `
-Name "dsLbBackEndPool_v6"
```

### <a name="create-a-load-balancer-rule"></a>Yük dengeleyici kuralı oluşturma

Trafiğin VM 'lere nasıl dağıtıldığını tanımlamak için bir yük dengeleyici kuralı kullanılır. Gelen trafik için ön uç IP yapılandırmasını ve trafiği almak için arka uç IP havuzunu, gerekli kaynak ve hedef bağlantı noktası ile birlikte tanımlarsınız. Yalnızca sağlıklı VM 'Lerin trafik aldığından emin olmak için isteğe bağlı olarak bir sistem durumu araştırması tanımlayabilirsiniz. Temel yük dengeleyici, VM 'lerde hem IPv4 hem de IPv6 uç noktaları için sistem durumunu değerlendirmek üzere bir IPv4 araştırması kullanır. Standart yük dengeleyici, açıkça IPv6 sistem durumu araştırmaları desteği içerir.

[Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig)ile bir yük dengeleyici kuralı oluşturun. Aşağıdaki örnek, *dsLBrule_v4* ve *dsLBrule_v6* adlı yük dengeleyici kuralları oluşturur ve *TCP* bağlantı noktası *80* üzerindeki trafiği IPv4 ve IPv6 ön uç IP yapılandırmalarına dengeler:

```azurepowershell-interactive
$lbrule_v4 = New-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v4" `
  -FrontendIpConfiguration $frontendIPv4 `
  -BackendAddressPool $backendPoolv4 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80

$lbrule_v6 = New-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v6" `
  -FrontendIpConfiguration $frontendIPv6 `
  -BackendAddressPool $backendPoolv6 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80
```

### <a name="create-load-balancer"></a>Yük dengeleyici oluştur

[New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer)ile standart Load Balancer oluşturun. Aşağıdaki örnek, önceki adımlarda oluşturduğunuz IPv4 ve IPv6 ön uç IP yapılandırması, arka uç havuzları ve yük dengeleme kurallarını kullanarak *Myloadbalancer* adlı bir genel standart Load Balancer oluşturur:

```azurepowershell-interactive
$lb = New-AzLoadBalancer `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-Name "MyLoadBalancer" `
-Sku "Standard" `
-FrontendIpConfiguration $frontendIPv4,$frontendIPv6 `
-BackendAddressPool $backendPoolv4,$backendPoolv6 `
-LoadBalancingRule $lbrule_v4,$lbrule_v6

```

## <a name="create-network-resources"></a>Ağ kaynakları oluşturma
Bazı VM 'Leri dağıtmadan ve dengeleyicinizi test etmeden önce, destekleyici ağ kaynakları (kullanılabilirlik kümesi, ağ güvenlik grubu, sanal ağ ve sanal NIC 'Ler) oluşturmanız gerekir. 
### <a name="create-an-availability-set"></a>Kullanılabilirlik kümesi oluşturma
Uygulamanızın yüksek oranda kullanılabilirliğini artırmak için VM 'lerinizi bir kullanılabilirlik kümesine yerleştirin.

[New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset)ile bir kullanılabilirlik kümesi oluşturun. Aşağıdaki örnek, *myAvailabilitySet*adlı bir kullanılabilirlik kümesi oluşturur:

```azurepowershell-interactive
$avset = New-AzAvailabilitySet `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsAVset" `
  -PlatformFaultDomainCount 2 `
  -PlatformUpdateDomainCount 2 `
  -Sku aligned
```

### <a name="create-network-security-group"></a>Ağ güvenlik grubu oluştur

VNET 'iniz içindeki gelen ve giden iletişimi yönetecek kurallar için bir ağ güvenlik grubu oluşturun.

#### <a name="create-a-network-security-group-rule-for-port-3389"></a>3389 numaralı bağlantı noktası için bir ağ güvenlik grubu kuralı oluşturma

[New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig)ile 3389 bağlantı noktası üzerinden RDP bağlantılarına izin veren bir ağ güvenlik grubu kuralı oluşturun.

```azurepowershell-interactive
$rule1 = New-AzNetworkSecurityRuleConfig `
-Name 'myNetworkSecurityGroupRuleRDP' `
-Description 'Allow RDP' `
-Access Allow `
-Protocol Tcp `
-Direction Inbound `
-Priority 100 `
-SourceAddressPrefix * `
-SourcePortRange * `
-DestinationAddressPrefix * `
-DestinationPortRange 3389
```
#### <a name="create-a-network-security-group-rule-for-port-80"></a>80 numaralı bağlantı noktası için bir ağ güvenlik grubu kuralı oluşturma

[New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig)ile 80 bağlantı noktası üzerinden internet bağlantılarına izin vermek için bir ağ güvenlik grubu kuralı oluşturun.

```azurepowershell-interactive
$rule2 = New-AzNetworkSecurityRuleConfig `
  -Name 'myNetworkSecurityGroupRuleHTTP' `
  -Description 'Allow HTTP' `
  -Access Allow `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 200 `
  -SourceAddressPrefix * `
  -SourcePortRange 80 `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80
```
#### <a name="create-a-network-security-group"></a>Ağ güvenlik grubu oluşturma

[New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup)ile bir ağ güvenlik grubu oluşturun.

```azurepowershell-interactive
$nsg = New-AzNetworkSecurityGroup `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-Name "dsNSG1"  `
-SecurityRules $rule1,$rule2
```
### <a name="create-a-virtual-network"></a>Sanal ağ oluşturma

[New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)ile bir sanal ağ oluşturun. Aşağıdaki örnek, *Mysubnet*Ile *dsvnet* adlı bir sanal ağ oluşturur:

```azurepowershell-interactive
# Create dual stack subnet
$subnet = New-AzVirtualNetworkSubnetConfig `
-Name "dsSubnet" `
-AddressPrefix "10.0.0.0/24","ace:cab:deca:deed::/64"

# Create the virtual network
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsVnet" `
  -AddressPrefix "10.0.0.0/16","ace:cab:deca::/48"  `
  -Subnet $subnet
```

### <a name="create-nics"></a>NIC oluşturma

[New-Aznetworkınterface](/powershell/module/az.network/new-aznetworkinterface)Ile sanal NIC 'ler oluşturun. Aşağıdaki örnek, hem IPv4 hem de IPv6 yapılandırmalarına sahip iki sanal NIC oluşturur. (Aşağıdaki adımlarda uygulamanız için oluşturduğunuz her VM için bir sanal NIC).

```azurepowershell-interactive
  $Ip4Config=New-AzNetworkInterfaceIpConfig `
    -Name dsIp4Config `
    -Subnet $vnet.subnets[0] `
    -PrivateIpAddressVersion IPv4 `
    -LoadBalancerBackendAddressPool $backendPoolv4 `
    -PublicIpAddress  $RdpPublicIP_1
    
  $Ip6Config=New-AzNetworkInterfaceIpConfig `
    -Name dsIp6Config `
    -Subnet $vnet.subnets[0] `
    -PrivateIpAddressVersion IPv6 `
    -LoadBalancerBackendAddressPool $backendPoolv6
    
  $NIC_1 = New-AzNetworkInterface `
    -Name "dsNIC1" `
    -ResourceGroupName $rg.ResourceGroupName `
    -Location $rg.Location  `
    -NetworkSecurityGroupId $nsg.Id `
    -IpConfiguration $Ip4Config,$Ip6Config 
    
  $Ip4Config=New-AzNetworkInterfaceIpConfig `
    -Name dsIp4Config `
    -Subnet $vnet.subnets[0] `
    -PrivateIpAddressVersion IPv4 `
    -LoadBalancerBackendAddressPool $backendPoolv4 `
    -PublicIpAddress  $RdpPublicIP_2  

  $NIC_2 = New-AzNetworkInterface `
    -Name "dsNIC2" `
    -ResourceGroupName $rg.ResourceGroupName `
    -Location $rg.Location  `
    -NetworkSecurityGroupId $nsg.Id `
    -IpConfiguration $Ip4Config,$Ip6Config 

```

### <a name="create-virtual-machines"></a>Sanal makineler oluşturma

VM 'Ler için [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential)ile bir Yönetici Kullanıcı adı ve parola ayarlayın:

```azurepowershell-interactive
$cred = get-credential -Message "DUAL STACK VNET SAMPLE:  Please enter the Administrator credential to log into the VMs."
```

Artık [New-AzVM](/powershell/module/az.compute/new-azvm)Ile VM 'leri oluşturabilirsiniz. Aşağıdaki örnekte, zaten mevcut değilse iki VM ve gerekli sanal ağ bileşenleri oluşturulur. 

```azurepowershell-interactive
$vmsize = "Standard_A2"
$ImagePublisher = "MicrosoftWindowsServer"
$imageOffer = "WindowsServer"
$imageSKU = "2019-Datacenter"

$vmName= "dsVM1"
$VMconfig1 = New-AzVMConfig -VMName $vmName -VMSize $vmsize -AvailabilitySetId $avset.Id 3> $null | Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent 3> $null | Set-AzVMSourceImage -PublisherName $ImagePublisher -Offer $imageOffer -Skus $imageSKU -Version "latest" 3> $null | Set-AzVMOSDisk -Name "$vmName.vhd" -CreateOption fromImage  3> $null | Add-AzVMNetworkInterface -Id $NIC_1.Id  3> $null 
$VM1 = New-AzVM -ResourceGroupName $rg.ResourceGroupName  -Location $rg.Location  -VM $VMconfig1 

$vmName= "dsVM2"
$VMconfig2 = New-AzVMConfig -VMName $vmName -VMSize $vmsize -AvailabilitySetId $avset.Id 3> $null | Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent 3> $null | Set-AzVMSourceImage -PublisherName $ImagePublisher -Offer $imageOffer -Skus $imageSKU -Version "latest" 3> $null | Set-AzVMOSDisk -Name "$vmName.vhd" -CreateOption fromImage  3> $null | Add-AzVMNetworkInterface -Id $NIC_2.Id  3> $null 
$VM2 = New-AzVM -ResourceGroupName $rg.ResourceGroupName  -Location $rg.Location  -VM $VMconfig2
```

## <a name="determine-ip-addresses-of-the-ipv4-and-ipv6-endpoints"></a>IPv4 ve IPv6 bitiş noktalarının IP adreslerini belirleme
@No__t-0 ile bu dağıtımda kullanılan IP 'yi özetlemek için kaynak grubundaki tüm ağ arabirimi nesnelerini alın. Ayrıca, IPv4 ve IPv6 uç noktalarının Load Balancer ön uç adreslerini `get-AzpublicIpAddress` ile alın.

```azurepowershell-interactive
$rgName= "dsRG1"
$NICsInRG= get-AzNetworkInterface -resourceGroupName $rgName 
write-host `nSummary of IPs in this Deployment: 
write-host ******************************************
foreach ($NIC in $NICsInRG) {
 
    $VMid= $NIC.virtualmachine.id 
    $VMnamebits= $VMid.split("/") 
    $VMname= $VMnamebits[($VMnamebits.count-1)] 
    write-host `nPrivate IP addresses for $VMname 
    $IPconfigsInNIC= $NIC.IPconfigurations 
    foreach ($IPconfig in $IPconfigsInNIC) {
 
        $IPaddress= $IPconfig.privateipaddress 
        write-host "    "$IPaddress 
        IF ($IPconfig.PublicIpAddress.ID) {
 
            $IDbits= ($IPconfig.PublicIpAddress.ID).split("/")
            $PipName= $IDbits[($IDbits.count-1)]
            $PipObject= get-azPublicIpAddress -name $PipName -resourceGroup $rgName
            write-host "    "RDP address:  $PipObject.IpAddress
                 }
         }
 }
 
 
 
  write-host `nPublic IP addresses on Load Balancer:
 
  (get-AzpublicIpAddress -resourcegroupname $rgName | where { $_.name -notlike "RdpPublicIP*" }).IpAddress
```
Aşağıdaki şekilde, iki VM 'nin özel IPv4 ve IPv6 adreslerini ve Load Balancer ön uç IPv4 ve IPv6 IP adreslerini listeleyen bir örnek çıktı gösterilmektedir.

![Azure 'da ikili yığın (IPv4/IPv6) uygulama dağıtımının IP Özeti](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-application-summary.png)

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Azure portal 'de IPv6 çift yığın sanal ağını görüntüleme
IPv6 çift yığın sanal ağını Azure portal içinde aşağıdaki gibi görüntüleyebilirsiniz:
1. Portalın arama çubuğunda *Dsvnet*' i girin.
2. Arama sonuçlarında **Dsvnet** göründüğünde seçin. Bu, *Dsvnet*adlı çift yığın sanal ağının **genel bakış** sayfasını başlatır. Çift yığın sanal ağı, hem IPv4 hem de IPv6 yapılandırmalarına sahip ve *Dssubnet*adlı çift yığın alt ağında bulunan iki NIC 'yi gösterir.

  ![Azure 'da IPv6 çift yığın sanal ağı](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-vnet.png)

> [!NOTE]
> Azure sanal ağ için IPv6, bu önizleme sürümünde salt okunurdur Azure portal kullanılabilir.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) komutunu kullanarak kaynak grubunu, VM 'yi ve tüm ilgili kaynakları kaldırabilirsiniz.

```azurepowershell-interactive
Remove-AzResourceGroup -Name dsRG1
```

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, çift ön uç IP yapılandırması (IPv4 ve IPv6) ile bir Standart Load Balancer oluşturdunuz. Ayrıca, yük dengeleyicinin arka uç havuzuna eklenen çift IP yapılandırmalarına (ıPV4 + IPv6) sahip NIC 'Leri dahil eden iki sanal makine oluşturmuş olursunuz. Azure sanal ağlarında IPv6 desteği hakkında daha fazla bilgi edinmek için bkz. [Azure sanal ağ Için IPv6 nedir?](ipv6-overview.md)