---
title: Azure sanal ağ 'da IPv4 uygulamasını IPv6 'ya yükseltme-PowerShell
titlesuffix: Azure Virtual Network
description: Bu makalede, Azure PowerShell kullanarak Azure sanal ağı 'nda var olan bir uygulamaya IPv6 adreslerinin nasıl dağıtılacağı gösterilmektedir.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/21/2019
ms.author: kumud
ms.openlocfilehash: 907a6de2ff89ddd3c2cb5bdab67e1deb984141dc
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/27/2019
ms.locfileid: "72965246"
---
# <a name="upgrade-an-ipv4-application-to-ipv6-in-azure-virtual-network---powershell-preview"></a>Azure sanal ağ 'da IPv4 uygulamasını IPv6 'ya yükseltme-PowerShell (Önizleme)

Bu makalede, bir Azure sanal ağında Standart Load Balancer ve genel IP 'ye sahip mevcut bir IPv4 uygulamasına IPv6 bağlantısı ekleme gösterilmektedir. Yerinde yükseltme şunları içerir:
- Sanal ağ ve alt ağ için IPv6 adres alanı
- hem IPv4 hem de ıPV6 ön uç yapılandırmalarına sahip bir Standart Load Balancer
- IPv4 + IPv6 yapılandırmasına sahip NIC 'leri olan VM 'Ler
- Yük dengeleyicinin Internet 'e yönelik IPv6 bağlantısı olması için IPV genel IP 'si

> [!Important]
> Azure sanal ağ için IPv6 desteği şu anda genel önizlemededir. Bu önizleme bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir. Ayrıntılar için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

PowerShell 'i yerel olarak yükleyip kullanmayı tercih ederseniz, bu makale Azure PowerShell modülü sürümü 6.9.0 veya üstünü gerektirir. Yüklü sürümü bulmak için `Get-Module -ListAvailable Az` komutunu çalıştırın. Yükseltmeniz gerekirse, bkz. [Azure PowerShell modülünü yükleme](/powershell/azure/install-Az-ps). PowerShell'i yerel olarak çalıştırıyorsanız Azure bağlantısı oluşturmak için `Connect-AzAccount` komutunu da çalıştırmanız gerekir.

## <a name="prerequisites"></a>Önkoşullar

### <a name="register-the-service"></a>Hizmeti kaydetme

Azure 'da bir çift yığın uygulamasını dağıtmadan önce, aşağıdaki Azure PowerShell kullanarak aboneliğinizi bu önizleme özelliği için yapılandırmanız gerekir:

Aşağıdaki gibi kaydolun:
```azurepowershell
Register-AzProviderFeature -FeatureName AllowIPv6VirtualNetwork -ProviderNamespace Microsoft.Network
Register-AzProviderFeature -FeatureName AllowIPv6CAOnStandardLB -ProviderNamespace Microsoft.Network
```
Özellik kaydının tamamlanabilmesi 30 dakika kadar sürer. Aşağıdaki Azure PowerShell komutunu çalıştırarak kayıt durumunuzu kontrol edebilirsiniz: kaydı şu şekilde denetleyin:
```azurepowershell
Get-AzProviderFeature -FeatureName AllowIPv6VirtualNetwork -ProviderNamespace Microsoft.Network
Get-AzProviderFeature -FeatureName AllowIPv6CAOnStandardLB -ProviderNamespace Microsoft.Network
```
Kayıt tamamlandıktan sonra aşağıdaki komutu çalıştırın:

```azurepowershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

### <a name="create-a-standard-load-balancer"></a>Standart Yük Dengeleyici oluşturma
Bu makalede [hızlı başlangıç: Standart Load Balancer Azure PowerShell oluşturma](../load-balancer/quickstart-create-standard-load-balancer-powershell.md)bölümünde açıklandığı gibi standart Load Balancer dağıttığınız varsayılmaktadır.

## <a name="retrieve-the-resource-group"></a>Kaynak grubunu alma

Çift yığın Sanal ağınızı oluşturabilmeniz için önce [Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup)ile kaynak grubunu almalısınız.

```azurepowershell
 $rg = Get-AzResourceGroup  -ResourceGroupName "myResourceGroupSLB"
```

## <a name="create-an-ipv6-ip-addresses"></a>IPv6 IP adresleri oluşturma

Standart Load Balancer için [New-Azpublicıpaddress](/powershell/module/az.network/new-azpublicipaddress) ile genel bir IPv6 adresi oluşturun. Aşağıdaki örnek, *Myresourcegroupslb* kaynak grubunda *PublicIP_v6* ADLı bir IPv6 genel IP adresi oluşturur:

```azurepowershell
  
  $PublicIP_v6 = New-AzPublicIpAddress `
  -Name "PublicIP_v6" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Sku Standard  `
  -AllocationMethod Static `
  -IpAddressVersion IPv6
```

## <a name="configure-load-balancer-frontend"></a>Yük dengeleyici ön uç 'yi yapılandırma

Mevcut yük dengeleyici yapılandırmasını alın ve ardından [Add-Azloadbalancerfrontendıpconfig](/powershell/module/az.network/Add-AzLoadBalancerFrontendIpConfig) kullanarak yenı IPv6 IP adresini aşağıdaki şekilde ekleyin:

```azurepowershell
# Retrieve the load balancer configuration
$lb = Get-AzLoadBalancer -ResourceGroupName $rg.ResourceGroupName -Name "MyLoadBalancer"
# Add IPv6 components to the local copy of the load balancer configuration
$lb | Add-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v6" `
  -PublicIpAddress $PublicIP_v6
#Update the running load balancer with the new frontend
$lb | Set-AzLoadBalancer
```

## <a name="configure-load-balancer-backend-pool"></a>Yük dengeleyici arka uç havuzunu Yapılandır

Yük dengeleyici yapılandırmasının yerel kopyasında arka uç havuzunu oluşturun ve çalışan yük dengeleyiciyi yeni arka uç havuzu yapılandırmasıyla aşağıdaki şekilde güncelleştirin:

```azurepowershell
$lb | Add-AzLoadBalancerBackendAddressPoolConfig -Name "LbBackEndPool_v6"
# Update the running load balancer with the new backend pool
$lb | Set-AzLoadBalancer
```

## <a name="configure-load-balancer-rules"></a>Yük dengeleyici kurallarını yapılandırma
Mevcut yük dengeleyici ön uç ve arka uç havuzu yapılandırmasını alın ve ardından [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/Add-AzLoadBalancerRuleConfig)kullanarak yeni yük dengeleme kuralları ekleyin.

```azurepowershell
# Retrieve the updated (live) versions of the frontend and backend pool
$frontendIPv6 = Get-AzLoadBalancerFrontendIpConfig -Name "dsLbFrontEnd_v6" -LoadBalancer $lb
$backendPoolv6 = Get-AzLoadBalancerBackendAddressPoolConfig -Name "LbBackEndPool_v6" -LoadBalancer $lb
# Create new LB rule with the frontend and backend
$lb | Add-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v6" `
  -FrontendIpConfiguration $frontendIPv6 `
  -BackendAddressPool $backendPoolv6 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80
#Finalize all the load balancer updates on the running load balancer
$lb | Set-AzLoadBalancer
```
## <a name="add-ipv6-address-ranges"></a>IPv6 adres aralıklarını Ekle

Sanal ağa ve VM 'Leri barındıran alt ağa IPv6 adres aralıklarını aşağıdaki şekilde ekleyin:

```azurepowershell
#Add IPv6 ranges to the VNET and subnet
#Retreive the VNET object
$vnet = Get-AzVirtualNetwork  -ResourceGroupName $rg.ResourceGroupName -Name "myVnet" 
#Add IPv6 prefix to the VNET
$vnet.addressspace.addressprefixes.add("ace:cab:deca::/48")
#Update the running VNET
$vnet |  Set-AzVirtualNetwork

#Retrieve the subnet object from the local copy of the VNET
$subnet= $vnet.subnets[0]
#Add IPv6 prefix to the Subnet (subnet of the VNET prefix, of course)
$subnet.addressprefix.add("ace:cab:deca::/64")
#Update the running VNET with the new subnet configuration
$vnet |  Set-AzVirtualNetwork

```
## <a name="add-ipv6-configuration-to-nic"></a>IPv6 yapılandırmasını NIC 'e Ekle

[Add-Aznetworkınterfaceipconfig](/powershell/module/az.network/Add-AzNetworkInterfaceIpConfig) komutunu kullanarak tüm VM NIC 'Leri bir IPv6 adresi ile aşağıdaki şekilde yapılandırın:

```azurepowershell

#Retrieve the NIC objects
$NIC_1 = Get-AzNetworkInterface -Name "myNic1" -ResourceGroupName $rg.ResourceGroupName
$NIC_2 = Get-AzNetworkInterface -Name "myNic2" -ResourceGroupName $rg.ResourceGroupName
$NIC_3 = Get-AzNetworkInterface -Name "myNic3" -ResourceGroupName $rg.ResourceGroupName
#Add an IPv6 IPconfig to NIC_1 and update the NIC on the running VM
$NIC_1 | Add-AzNetworkInterfaceIpConfig -Name MyIPv6Config -Subnet $vnet.Subnets[0]  -PrivateIpAddressVersion IPv6 -LoadBalancerBackendAddressPool $backendPoolv6 
$NIC_1 | Set-AzNetworkInterface
#Add an IPv6 IPconfig to NIC_2 and update the NIC on the running VM
$NIC_2 | Add-AzNetworkInterfaceIpConfig -Name MyIPv6Config -Subnet $vnet.Subnets[0]  -PrivateIpAddressVersion IPv6 -LoadBalancerBackendAddressPool $backendPoolv6 
$NIC_2 | Set-AzNetworkInterface
#Add an IPv6 IPconfig to NIC_3 and update the NIC on the running VM
$NIC_3 | Add-AzNetworkInterfaceIpConfig -Name MyIPv6Config -Subnet $vnet.Subnets[0]  -PrivateIpAddressVersion IPv6 -LoadBalancerBackendAddressPool $backendPoolv6 
$NIC_3 | Set-AzNetworkInterface

```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Azure portal 'de IPv6 çift yığın sanal ağını görüntüleme
IPv6 çift yığın sanal ağını Azure portal içinde aşağıdaki gibi görüntüleyebilirsiniz:
1. Portalın arama çubuğunda *Myvnet*' i girin.
2. Arama sonuçlarında **Myvnet** göründüğünde seçin. Bu, *Myvnet*adlı çift yığın sanal ağının **genel bakış** sayfasını başlatır. Çift yığın sanal ağı, *Mysubnet*adlı çift yığın alt ağında bulunan IPv4 ve IPv6 yapılandırmalarına sahip üç NIC 'yi gösterir.

  ![Azure 'da IPv6 çift yığın sanal ağı](./media/ipv6-add-to-existing-vnet-powershell/ipv6-dual-stack-vnet.png)

> [!NOTE]
> Azure sanal ağ için IPv6, bu önizleme sürümünde salt okunurdur Azure portal kullanılabilir.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) komutunu kullanarak kaynak grubunu, VM 'yi ve tüm ilgili kaynakları kaldırabilirsiniz.

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyAzureResourceGroupSLB
```

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, bir IPv4 ön uç IP yapılandırması olan mevcut bir Standart Load Balancer ikili yığın (IPv4 ve IPv6) yapılandırmasına güncelleştirmiş olursunuz. Ayrıca, arka uç havuzundaki VM 'lerin NIC 'lerine ve bunları barındıran sanal ağa IPv6 yapılandırması eklediniz. Azure sanal ağlarında IPv6 desteği hakkında daha fazla bilgi edinmek için bkz. [Azure sanal ağ Için IPv6 nedir?](ipv6-overview.md)
