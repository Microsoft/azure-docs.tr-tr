---
title: Azure sanal ağları için ortak PowerShell komutları
description: VM 'Ler için bir sanal ağ ve ilişkili kaynakları oluşturmaya başlamanızı sağlamak için ortak PowerShell komutları.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 56e1a73c-8299-4996-bd03-f74585caa1dc
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/17/2017
ms.author: cynthn
ms.openlocfilehash: 3abde706ddff297094c7fbb1579b534894b349d2
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74032925"
---
# <a name="common-powershell-commands-for-azure-virtual-networks"></a>Azure sanal ağları için ortak PowerShell komutları

Bir sanal makine oluşturmak istiyorsanız [sanal bir ağ](../../virtual-network/virtual-networks-overview.md) OLUŞTURMANıZ veya VM 'nin eklenebileceği mevcut bir sanal ağ hakkında bilmeniz gerekir. Genellikle, bir VM oluşturduğunuzda, bu makalede açıklanan kaynakları da oluşturmayı göz önünde bulundurmanız gerekir.

Azure PowerShell’in en son sürümünü yükleme, aboneliğinizi seçme ve hesabınızda oturum açma hakkında bilgi almak için bkz. [Azure PowerShell’i yükleme ve yapılandırma](/powershell/azure/overview).

Bazı değişkenler, bu makaledeki komutlardan birden fazlasını çalıştırıyorsanız sizin için yararlı olabilir:

- $location-ağ kaynaklarının konumu. Sizin için uygun bir [coğrafi bölge](https://azure.microsoft.com/regions/) bulmak için [Get-azlocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation) ' i kullanabilirsiniz.
- $myResourceGroup-ağ kaynaklarının bulunduğu kaynak grubunun adı.

## <a name="create-network-resources"></a>Ağ kaynakları oluşturma

| Görev | Komut |
| ---- | ------- |
| Alt ağ yapılandırmaları oluşturma |$subnet 1 = [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig) -Name "mySubnet1"-Addresspredüzeltmesini xx. X. X. X/XX<BR>$subnet 2 = New-AzVirtualNetworkSubnetConfig-Name "mySubnet2"-Addresspredüzeltmesini XX. X. X. X/XX<BR><BR>Tipik bir ağ, [İnternet 'e yönelik yük dengeleyici](../../load-balancer/load-balancer-internet-overview.md) için bir alt ağa ve bir [iç yük dengeleyici](../../load-balancer/load-balancer-internal-overview.md)için ayrı bir alt ağa sahip olabilir. |
| Sanal ağ oluşturma |$vnet = [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork) -Name "myvnet"-resourcegroupname $MyResourceGroup-Location $Location-Addresspredüzeltmesini xx. X. X. X/XX-subnet $subnet 1, $subnet 2 |
| Benzersiz bir etki alanı adı için test |[Test-AzDnsAvailability](https://docs.microsoft.com/powershell/module/az.network/test-azdnsavailability) -domainnamelabel "mydns"-Location $Location<BR><BR>[Genel IP kaynağı](../../virtual-network/virtual-network-ip-addresses-overview-arm.md)IÇIN bir DNS etki alanı adı belirtebilirsiniz ve bu, DomainName.Location.cloudapp.Azure.com için Azure tarafından yönetilen DNS SUNUCULARıNDAKI genel IP adresine bir eşleme oluşturur. Ad yalnızca küçük harf, sayı ve kısa çizgi içerebilir. İlk ve son karakter bir harf veya sayı olmalıdır ve etki alanı adı, Azure konumu içinde benzersiz olmalıdır. **Değer** döndürülürse, önerilen adınız genel olarak benzersizdir. |
| Genel IP adresi oluşturma |$pip = [New-Azpublicıpaddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress) -Name "Mypublicıp"-resourcegroupname $MyResourceGroup-DomainNameLabel "mydns"-Location $Location-Allocationmethod Dynamic<BR><BR>Genel IP adresi, daha önce test ettiğiniz ve yük dengeleyicinin ön uç yapılandırması tarafından kullanılan etki alanı adını kullanır. |
| Ön uç IP yapılandırması oluşturma |$frontendIP = [New-Azloadbalancerfrontendıpconfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig) -Name "Myfrontendıp"-publicıpaddress $Pip<BR><BR>Ön uç yapılandırması, gelen ağ trafiği için daha önce oluşturduğunuz genel IP adresini içerir. |
| Arka uç adres havuzu oluşturma |$beAddressPool = [New-Azloadbalancerbackendadddresspoolconfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) -Name "Mybackendadddresspool"<BR><BR>Bir ağ arabiriminden erişilen yük dengeleyicinin arka ucu için iç adresler sağlar. |
| Araştırma oluşturma |$healthProbe = [New-AzLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerprobeconfig) -Name "myaraştırması"-RequestPath ' healtharaştırması. aspx '-protokol http-port 80-ıntervalınseconds 15-probecount 2<BR><BR>Arka uç adres havuzundaki sanal makine örneklerinin kullanılabilirliğini kontrol etmek için kullanılan durum araştırmalarını içerir. |
| Yük Dengeleme kuralı oluşturma |$lbRule = [New-AzLoadBalancerRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerruleconfig) -Name http-Frontendıpconfiguration $FrontendIP-BackendAddressPool $BeAddressPool-araştırma $HealthProbe-protokol TCP-frontendport 80-BackendPort 80<BR><BR>Yük dengeleyicide, arka uç adres havuzundaki bir bağlantı noktasına ortak bir bağlantı noktası atayan kuralları içerir. |
| Gelen NAT kuralı oluşturma |$inboundNATRule = [New-Azloadbalancerınboundnatrutaconfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) -Name "myInboundRule1"-Frontendıpconfiguration $FrontendIP-protokol TCP-frontendport 3441-BackendPort 3389<BR><BR>Yük dengeleyicideki genel bağlantı noktasını arka uç adres havuzundaki belirli bir sanal makineye yönelik bağlantı noktasına eşleyen kuralları içerir. |
| Yük dengeleyici oluşturma |$loadBalancer = [New-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancer) -resourcegroupname $MyResourceGroup-adı "myloadbalancer"-Location $Location-Frontendıconfiguration $FrontendIP-ınboundnatrule $InboundNATRule-LoadBalancingRule $LbRule-BackendAddressPool $BeAddressPool-araştırma $healthProbe |
| Ağ arabirimi oluşturma |$nic 1 = [New-Aznetworkınterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface) -resourcegroupname $MyResourceGroup-Name "MYNIC"-Location $Location-PRIVATEıPADDRESS xx. X. X. X-subnet $subnet 2-Loadbalancerbackendadddresspool $loadBalancer. Backennabir $loadBalancer. ınboundnatrules [0]<BR><BR>Daha önce oluşturduğunuz genel IP adresini ve sanal ağ alt ağını kullanarak bir ağ arabirimi oluşturun. |

## <a name="get-information-about-network-resources"></a>Ağ kaynakları hakkında bilgi alın

| Görev | Komut |
| ---- | ------- |
| Sanal ağları listeleme |[Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork) -resourcegroupname $myResourceGroup<BR><BR>Kaynak grubundaki tüm sanal ağları listeler. |
| Bir sanal ağ hakkında bilgi alın |Get-AzVirtualNetwork-Name "myVNet"-ResourceGroupName $myResourceGroup |
| Bir sanal ağdaki alt ağları listeleme |Get-AzVirtualNetwork-Name "myVNet"-ResourceGroupName $myResourceGroup &#124; alt ağları seçin |
| Bir alt ağ hakkında bilgi alın |[Get-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetworksubnetconfig) -adı "mySubnet1"-VirtualNetwork $VNET<BR><BR>Belirtilen sanal ağdaki alt ağ hakkında bilgi alır. $Vnet değeri, Get-AzVirtualNetwork tarafından döndürülen nesneyi temsil eder. |
| IP adreslerini Listele |[Get-Azpublicıpaddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress) -resourcegroupname $myResourceGroup<BR><BR>Kaynak grubundaki genel IP adreslerini listeler. |
| Yük dengeleyicileri listeleme |[Get-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/get-azloadbalancer) -resourcegroupname $myResourceGroup<BR><BR>Kaynak grubundaki tüm yük dengeleyicileri listeler. |
| Ağ arabirimlerini listeleme |[Get-Aznetworkınterface](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkinterface) -resourcegroupname $myResourceGroup<BR><BR>Kaynak grubundaki tüm ağ arabirimlerini listeler. |
| Ağ arabirimi hakkında bilgi alın |Get-Aznetworkınterface-adı "MYNIC"-ResourceGroupName $myResourceGroup<BR><BR>Belirli bir ağ arabirimi hakkında bilgi alır. |
| Bir ağ arabiriminin IP yapılandırmasını al |[Get-Aznetworkınterfaceipconfig](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkinterfaceipconfig) -Name "Mynicıp"-networkınterface $Nic<BR><BR>Belirtilen ağ arabiriminin IP yapılandırması hakkında bilgi alır. $Nic değeri, Get-Aznetworkınterface tarafından döndürülen nesneyi temsil eder. |

## <a name="manage-network-resources"></a>Ağ kaynaklarını yönetme

| Görev | Komut |
| ---- | ------- |
| Sanal ağa alt ağ ekleme |[Add-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/add-azvirtualnetworksubnetconfig) -addresspredüzeltmesini xx. X. X. X/XX-adı "mySubnet1"-VirtualNetwork $vnet<BR><BR>Mevcut bir sanal ağa alt ağ ekler. $Vnet değeri, Get-AzVirtualNetwork tarafından döndürülen nesneyi temsil eder. |
| Sanal ağı silme |[Remove-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/remove-azvirtualnetwork) -Name "myvnet"-resourcegroupname $myResourceGroup<BR><BR>Belirtilen sanal ağı kaynak grubundan kaldırır. |
| Ağ arabirimini silme |[Remove-Aznetworkınterface](https://docs.microsoft.com/powershell/module/az.network/remove-aznetworkinterface) -Name "MYNIC"-resourcegroupname $myResourceGroup<BR><BR>Belirtilen ağ arabirimini kaynak grubundan kaldırır. |
| Yük dengeleyici silme |[Remove-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/remove-azloadbalancer) -Name "myloadbalancer"-resourcegroupname $myResourceGroup<BR><BR>Belirtilen yük dengeleyiciyi kaynak grubundan kaldırır. |
| Genel IP adresini Sil |[Remove-Azpublicıpaddress](https://docs.microsoft.com/powershell/module/az.network/remove-azpublicipaddress)-Name "myIpAddress"-resourcegroupname $myResourceGroup<BR><BR>Belirtilen genel IP adresini kaynak grubundan kaldırır. |

## <a name="next-steps"></a>Sonraki Adımlar
* [VM oluştururken](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)yeni oluşturduğunuz ağ arabirimini kullanın.
* [Birden çok ağ arabirimi içeren BIR VM oluşturma](../../virtual-network/virtual-network-deploy-multinic-classic-ps.md)hakkında bilgi edinin.

