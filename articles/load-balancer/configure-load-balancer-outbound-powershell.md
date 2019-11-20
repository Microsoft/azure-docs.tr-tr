---
title: Azure PowerShell kullanarak yük dengelemeyi ve giden kuralları yapılandırma
titlesuffix: Azure Load Balancer
description: Bu makalede, Standart Load Balancer Azure PowerShell kullanarak yük dengelemenin ve giden kuralların nasıl yapılandırılacağı gösterilmektedir.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/24/2019
ms.author: allensu
ms.openlocfilehash: b621d56a76a51f9ce9df10f88f5c3f2f4babfc03
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71816036"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-by-using-azure-powershell"></a>Azure PowerShell kullanarak Standart Load Balancer yük dengelemeyi ve giden kuralları yapılandırma

Bu makalede Azure PowerShell kullanarak Standart Load Balancer giden kuralların nasıl yapılandırılacağı gösterilmektedir.  

Bu makalenin senaryosunu tamamladığınızda, yük dengeleyici kaynağı iki ön uç ve bunlarla ilişkili kuralları içerir. Gelen trafik için bir ön uç ve giden trafik için başka bir ön uç vardır.  

Her ön uç, bir genel IP adresine başvurur. Bu senaryoda, gelen trafik için genel IP adresi giden trafik adresinden farklıdır.   Yük Dengeleme kuralı yalnızca gelen yük dengelemeyi sağlar. Giden kuralı, VM için giden ağ adresi çevirisini (NAT) denetler.  

Senaryo iki arka uç havuzu kullanır: biri gelen trafik ve diğeri giden trafik için. Bu havuzlar, özelliği gösterir ve senaryo için esneklik sağlar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="connect-to-your-azure-account"></a>Azure hesabınıza bağlanma
[Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) komutunu kullanarak Azure aboneliğinizde oturum açın. Ardından ekrandaki yönergeleri izleyin.
    
```azurepowershell-interactive
Connect-AzAccount
```
## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

[New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0)kullanarak bir kaynak grubu oluşturun. Azure Kaynak grubu, Azure kaynaklarının dağıtıldığı mantıksal bir kapsayıcıdır. Kaynaklar bundan sonra gruptan yönetilir.

Aşağıdaki örnek *eastus2* konumunda *myresourcegroupoutbağlanmadı* adlı bir kaynak grubu oluşturur:

```azurepowershell-interactive
New-AzResourceGroup -Name myresourcegroupoutbound -Location eastus
```
## <a name="create-a-virtual-network"></a>Sanal ağ oluşturun
*Myvnetoutbağlanmadı*adlı bir sanal ağ oluşturun. Alt ağını *mysubnetoutbağlanacak*şekilde adlandırın. [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=azps-2.6.0) ve [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=azps-2.6.0)kullanarak, *myresourcegroupoutconfiguration* içine yerleştirin.

```azurepowershell-interactive
$subnet = New-AzVirtualNetworkSubnetConfig -Name mysubnetoutbound -AddressPrefix "192.168.0.0/24"

New-AzVirtualNetwork -Name myvnetoutbound -ResourceGroupName myresourcegroupoutbound -Location eastus -AddressPrefix "192.168.0.0/16" -Subnet $subnet
```

## <a name="create-an-inbound-public-ip-address"></a>Gelen genel IP adresi oluşturma 

Web uygulamanıza internet üzerinden erişmek için, yük dengeleyici için genel bir IP adresi gerekir. Standart Load Balancer yalnızca standart genel IP adreslerini destekler. 

*Myresourcegroupoutbound*içinde *mypublicipınbound* ADLı standart bir genel IP adresi oluşturmak Için [New-azpublicıpaddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=azps-2.6.0) ' i kullanın.

```azurepowershell-interactive
$pubIPin = New-AzPublicIpAddress -ResourceGroupName myresourcegroupoutbound -Name mypublicipinbound -AllocationMethod Static -Sku Standard -Location eastus
```

## <a name="create-an-outbound-public-ip-address"></a>Giden genel IP adresi oluşturma 

[New-Azpublicıpaddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=azps-2.6.0)kullanarak yük dengeleyicinin ön uç yapılandırması için standart bir IP adresi oluşturun.

```azurepowershell-interactive
$pubIPout = New-AzPublicIpAddress -ResourceGroupName myresourcegroupoutbound -Name mypublicipoutbound -AllocationMethod Static -Sku Standard -Location eastus
```

## <a name="create-an-azure-load-balancer"></a>Azure yük dengeleyici oluşturma

Bu bölümde yük dengeleyicinin aşağıdaki bileşenlerini oluşturma ve yapılandırma açıklanmaktadır:
  - Yük dengeleyicide gelen ağ trafiğini alan bir ön uç IP 'si
  - Ön uç IP 'nin yük dengeli ağ trafiğini gönderdiği bir arka uç havuzu
  - Giden bağlantı için bir arka uç havuzu
  - Arka uç sanal makine örneklerinin sistem durumunu belirleyen bir sistem durumu araştırması
  - Trafiğin VM 'lere nasıl dağıtıldığını tanımlayan bir yük dengeleyici gelen kuralı
  - Trafiğin VM 'lerden nasıl dağıtıldığını tanımlayan bir yük dengeleyici giden kuralı

### <a name="create-an-inbound-front-end-ip"></a>Gelen ön uç IP 'si oluşturma
[New-Azloadbalancerfrontendıpconfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig?view=azps-2.6.0)kullanarak yük dengeleyici için gelen ön uç IP yapılandırmasını oluşturun. Yük dengeleyici, *myfrontendinbound*adlı bir gelen ön uç IP yapılandırması içermelidir. Bu yapılandırmayı *mypublicipınpublic*IP adresiyle ilişkilendirin.

```azurepowershell-interactive
$frontendIPin = New-AzLoadBalancerFrontendIPConfig -Name "myfrontendinbound" -PublicIpAddress $pubIPin
```
### <a name="create-an-outbound-front-end-ip"></a>Giden ön uç IP 'si oluşturma
[New-Azloadbalancerfrontendıpconfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig?view=azps-2.6.0)kullanarak yük dengeleyici için giden ön uç IP yapılandırmasını oluşturun. Bu yük dengeleyici, *myfrontendoutbound*adlı bir giden ön uç IP yapılandırması içermelidir. Bu yapılandırmayı *mypublicıpoıb utbağlanmadı*genel IP adresiyle ilişkilendirin.

```azurepowershell-interactive
$frontendIPout = New-AzLoadBalancerFrontendIPConfig -Name "myfrontendoutbound" -PublicIpAddress $pubIPout
```
### <a name="create-an-inbound-back-end-pool"></a>Gelen arka uç havuzu oluşturma
[New-Azloadbalancerbackendavddresspoolconfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig?view=azps-2.6.0)kullanarak yük dengeleyici için arka uç gelen havuzunu oluşturun. Havuzu *bepoolınbound*olarak adlandırın.

```azurepowershell-interactive
$bepoolin = New-AzLoadBalancerBackendAddressPoolConfig -Name bepoolinbound
``` 

### <a name="create-an-outbound-back-end-pool"></a>Giden arka uç havuzu oluşturma
[New-Azloadbalancerbackendavddresspoolconfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig?view=azps-2.6.0)kullanarak bir VM havuzu için giden bağlantıyı tanımlamak üzere başka bir arka uç adres havuzu oluşturmak için aşağıdaki komutu kullanın. Bu havuzu *pooloutbound*olarak adlandırın. 

Ayrı bir giden havuz oluşturarak en yüksek esnekliği sağlarsınız. Ancak isterseniz bu adımı atlayabilir ve yalnızca gelen *bepoolınbound* ' i kullanabilirsiniz.  

```azurepowershell-interactive
$bepoolout = New-AzLoadBalancerBackendAddressPoolConfig -Name bepooloutbound
``` 

### <a name="create-a-health-probe"></a>Durum araştırması oluşturma

Bir sistem durumu araştırması, tüm sanal makine örneklerini denetleyerek ağ trafiği gönderediklerinden emin olmanızı sağlar. Araştırma denetimlerinde başarısız olan sanal makine örneği, yeniden çevrimiçi olana kadar yük dengeleyiciden kaldırılır ve bir araştırma denetimi sağlıklı olduğunu belirler. 

VM 'lerin sistem durumunu izlemek için, [New-AzLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerprobeconfig?view=azps-2.6.0)kullanarak bir sistem durumu araştırması oluşturun. 

```azurepowershell-interactive
$probe = New-AzLoadBalancerProbeConfig -Name http -Protocol "http" -Port 80 -IntervalInSeconds 15 -ProbeCount 2 -RequestPath /
```
### <a name="create-a-load-balancer-rule"></a>Yük dengeleyici kuralı oluşturma

Yük dengeleyici kuralı, gelen trafik için ön uç IP yapılandırmasını ve trafiği almak için arka uç havuzunu tanımlar. Ayrıca gerekli kaynak ve hedef bağlantı noktasını tanımlar. 

[New-AzLoadBalancerRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerruleconfig?view=azps-2.6.0)kullanarak *myınboundlbrule* adlı bir yük dengeleyici kuralı oluşturun. Bu kural ön *uç havuzundaki 80*numaralı bağlantı noktasını dinleyecektir. Ayrıca, yük dengeli ağ trafiğini, *bepoolının*arka uç adres havuzuna göndermek için 80 numaralı bağlantı noktasını kullanır. 

```azurepowershell-interactive
$inboundRule = New-AzLoadBalancerRuleConfig -Name inboundlbrule -FrontendIPConfiguration $frontendIPin -BackendAddressPool $bepoolin -Probe $probe -Protocol "Tcp" -FrontendPort 80 -BackendPort 80 -IdleTimeoutInMinutes 15 -EnableFloatingIP -LoadDistribution SourceIP -DisableOutboundSNAT
```

>[!NOTE]
>Bu yük dengeleme kuralı **-disableoutboundsnat** parametresi nedeniyle otomatik gıden güvenli NAT (SNAT) devre dışı bırakır. Giden NAT yalnızca giden kuralı tarafından sağlanır.

### <a name="create-an-outbound-rule"></a>Giden kuralı oluşturma

Giden bir kural, ön uç *myfrontendoutbound*tarafından temsil edilen ön uç genel IP 'sini tanımlar. Bu ön uç, tüm giden NAT trafiği ve kuralın geçerli olduğu arka uç havuzu için kullanılacaktır.  

*Bepool* arka uç havuzundaki tüm VM 'LERIN (NIC IP yapılandırmalarında) giden ağ çevirisi için *mdeni boundrule* giden kuralı oluşturmak üzere aşağıdaki komutu kullanın.  Komut, çıkış boşta kalma süresini 4 ila 15 dakika olarak değiştirir. 1\.024 yerine 10.000 SNAT bağlantı noktası ayırır. Daha fazla bilgi için bkz. [New-AzLoadBalancerOutboundRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalanceroutboundruleconfig?view=azps-2.7.0).

```azurepowershell-interactive
 $outboundRule = New-AzLoadBalancerOutBoundRuleConfig -Name outboundrule -FrontendIPConfiguration $frontendIPout -BackendAddressPool $bepoolout -Protocol All -IdleTimeoutInMinutes 15 -AllocatedOutboundPort 10000
```
Ayrı bir giden havuz kullanmak istemiyorsanız, bunun yerine *$bepoolin* belirtmek için önceki komutta adres havuzu bağımsız değişkenini değiştirebilirsiniz.  Elde edilen yapılandırmayı esnek ve okunabilir hale getirmek için ayrı havuzlar kullanmanızı öneririz.

### <a name="create-a-load-balancer"></a>Yük dengeleyici oluşturma

[New-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancer?view=azps-2.6.0)kullanarak gelen IP adresi için bir yük dengeleyici oluşturmak üzere aşağıdaki komutu kullanın. Yük dengeleyici *lb*olarak adlandırın. Bir gelen ön uç IP yapılandırması içermesi gerekir. Arka *uç havuzu,* önceki adımda oluşturduğunuz *mypublicipınbound* genel IP adresiyle ilişkilendirilmelidir.

```azurepowershell-interactive
New-AzLoadBalancer -Name lb -Sku Standard -ResourceGroupName myresourcegroupoutbound -Location eastus -FrontendIpConfiguration $frontendIPin,$frontendIPout -BackendAddressPool $bepoolin,$bepoolout -Probe $probe -LoadBalancingRule $inboundrule -OutboundRule $outboundrule 
```

Bu noktada, ilgili NIC kaynaklarının IP yapılandırmasını güncelleştirerek sanal makinelerinizi hem *bepoolınbound* hem de *bepooloutbound* arka uç havuzlarına eklemeye devam edebilirsiniz. [Add-Aznetworkınterfaceipconfig](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest)komutunu kullanarak kaynak yapılandırmasını güncelleştirin.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Kaynak grubuna, yük dengeleyicisine ve ilgili kaynaklara artık ihtiyacınız kalmadığında [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.7.0)komutunu kullanarak bunları kaldırabilirsiniz.

```azurepowershell-interactive 
  Remove-AzResourceGroup -Name myresourcegroupoutbound
```

## <a name="next-steps"></a>Sonraki adımlar
Bu makalede, bir standart yük dengeleyici oluşturdunuz, hem gelen hem de giden yük dengeleyici trafik kuralları yapılandırdınız ve arka uç havuzundaki VM 'Ler için bir sistem durumu araştırması yapılandırdınız. 

Daha fazla bilgi edinmek için [Azure Load Balancer öğreticilerine](tutorial-load-balancer-standard-public-zone-redundant-portal.md)geçin.
