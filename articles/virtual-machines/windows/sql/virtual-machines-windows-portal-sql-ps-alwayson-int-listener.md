---
title: Kullanılabilirlik grubu dinleyicilerini & yük dengeleyiciyi yapılandırma (PowerShell)
description: Bir veya daha fazla IP adresi olan bir iç yük dengeleyici kullanarak Azure Resource Manager modelinde kullanılabilirlik grubu dinleyicilerini yapılandırın.
services: virtual-machines
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
ms.assetid: 14b39cde-311c-4ddf-98f3-8694e01a7d3b
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/06/2019
ms.author: mikeray
ms.custom: seo-lt-2019
ms.openlocfilehash: 83910c2209b5d3d3d67578ae41afb902bc885171
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74037468"
---
# <a name="configure-one-or-more-always-on-availability-group-listeners---resource-manager"></a>Bir veya daha fazla Always on kullanılabilirlik grubu dinleyicisi yapılandırma-Kaynak Yöneticisi
Bu konuda nasıl yapılacağı gösterilmektedir:

* PowerShell cmdlet 'lerini kullanarak SQL Server kullanılabilirlik grupları için bir iç yük dengeleyici oluşturun.
* Birden fazla kullanılabilirlik grubu için yük dengeleyiciye ek IP adresleri ekleyin. 

Kullanılabilirlik grubu dinleyicisi, istemcilerin veritabanı erişimi için bağlanacağı bir sanal ağ adıdır. Azure sanal makinelerinde, yük dengeleyici dinleyicinin IP adresini tutar. Yük dengeleyici, trafiği araştırma bağlantı noktasında dinleme yapan SQL Server örneğine yönlendirir. Genellikle bir kullanılabilirlik grubu, iç yük dengeleyici kullanır. Bir Azure iç yük dengeleyici bir veya daha fazla IP adresini barındırabilirler. Her IP adresi belirli bir yoklama bağlantı noktası kullanır. Bu belge, PowerShell kullanarak yük dengeleyici oluşturma veya SQL Server kullanılabilirlik grupları için mevcut yük dengeleyiciye IP adresleri ekleme işlemlerinin nasıl yapılacağını gösterir. 

Bir iç yük dengeleyiciye birden çok IP adresi atama özelliği Azure 'da yenidir ve yalnızca Kaynak Yöneticisi modelinde kullanılabilir. Bu görevi gerçekleştirmek için Kaynak Yöneticisi modelindeki Azure sanal makinelerinde dağıtılmış bir SQL Server kullanılabilirlik grubunuz olması gerekir. SQL Server sanal makinelerin her ikisi de aynı Kullanılabilirlik kümesine ait olmalıdır. Azure Resource Manager ' de kullanılabilirlik grubunu otomatik olarak oluşturmak için [Microsoft şablonunu](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) kullanabilirsiniz. Bu şablon, sizin için iç yük dengeleyici dahil olmak üzere kullanılabilirlik grubunu otomatik olarak oluşturur. İsterseniz, [her zaman açık kullanılabilirlik grubunu el ile yapılandırabilirsiniz](virtual-machines-windows-portal-sql-availability-group-tutorial.md).

Bu konu, kullanılabilirlik gruplarınızın zaten yapılandırılmış olmasını gerektirir.  

İlgili konular şunları içerir:

* [Azure VM 'de AlwaysOn Kullanılabilirlik Grupları Yapılandırma (GUI)](virtual-machines-windows-portal-sql-availability-group-tutorial.md)   
* [Azure Resource Manager ve PowerShell kullanarak VNet-VNet bağlantısı yapılandırma](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

[!INCLUDE [Start your PowerShell session](../../../../includes/sql-vm-powershell.md)]

## <a name="verify-powershell-version"></a>PowerShell sürümünü doğrula

Bu makaledeki örneklerde Azure PowerShell Module sürümü 5.4.1 kullanılarak test edilmiştir.

PowerShell modülünüzün 5.4.1 veya sonraki bir sürümü olduğunu doğrulayın.

Bkz. [Azure PowerShell modülünü Install](https://docs.microsoft.com/powershell/azure/install-az-ps).

## <a name="configure-the-windows-firewall"></a>Windows güvenlik duvarını yapılandırma

Windows güvenlik duvarını SQL Server erişimine izin verecek şekilde yapılandırın. Güvenlik duvarı kuralları SQL Server örneği ve dinleyici araştırması tarafından kullanılan bağlantı noktalarına TCP bağlantılarına izin verir. Ayrıntılı yönergeler için bkz. [veritabanı altyapısı erişimi için bir Windows Güvenlik Duvarı yapılandırma](https://msdn.microsoft.com/library/ms175043.aspx#Anchor_1). SQL Server bağlantı noktası ve yoklama bağlantı noktası için bir gelen kuralı oluşturun.

Azure ağ güvenlik grubuyla erişimi kısıtladığınız takdirde, izin ver kurallarının arka uç SQL Server VM IP adreslerini ve ağ dinleyicisi için yük dengeleyici kayan IP adreslerini ve varsa küme çekirdek IP adresini içerdiğinden emin olun.

## <a name="determine-the-load-balancer-sku-required"></a>Gerekli yük dengeleyici SKU 'sunu belirleme

[Azure yük dengeleyici](../../../load-balancer/load-balancer-overview.md) , 2 SKU 'da kullanılabilir: temel & standart. Standart yük dengeleyici önerilir. Sanal makineler bir kullanılabilirlik kümesinde ise, temel yük dengeleyiciye izin verilir. Standart yük dengeleyici, tüm VM IP adreslerinin standart IP adreslerini kullanmasını gerektirir.

Bir kullanılabilirlik grubu için geçerli [Microsoft şablonu](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) , temel IP adresleriyle temel bir yük dengeleyici kullanır.

Bu makaledeki örneklerde standart yük dengeleyici verilmiştir. Örneklerde, komut dosyası `-sku Standard`içerir.

```powershell
$ILB= New-AzLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe -sku Standard
```

Temel bir yük dengeleyici oluşturmak için yük dengeleyiciyi oluşturan satırdan `-sku Standard` kaldırın. Örneğin:

```powershell
$ILB= New-AzLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe
```

## <a name="example-script-create-an-internal-load-balancer-with-powershell"></a>Örnek betik: PowerShell ile iç yük dengeleyici oluşturma

> [!NOTE]
> Kullanılabilirlik grubunuzu [Microsoft şablonuyla](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)oluşturduysanız, iç yük dengeleyici zaten oluşturulmuştur.

Aşağıdaki PowerShell betiği, bir iç yük dengeleyici oluşturur, Yük Dengeleme kurallarını yapılandırır ve yük dengeleyici için bir IP adresi ayarlar. Betiği çalıştırmak için Windows PowerShell ISE açın ve betiği Betik bölmesine yapıştırın. PowerShell 'de oturum açmak için `Connect-AzAccount` kullanın. Birden çok Azure aboneliğiniz varsa, aboneliği ayarlamak için `Select-AzSubscription` kullanın. 

```powershell
# Connect-AzAccount
# Select-AzSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>

$ResourceGroupName = "<Resource Group Name>" # Resource group name
$VNetName = "<Virtual Network Name>"         # Virtual network name
$SubnetName = "<Subnet Name>"                # Subnet name
$ILBName = "<Load Balancer Name>"            # ILB name
$Location = "<Azure Region>"                 # Azure location
$VMNames = "<VM1>","<VM2>"                   # Virtual machine names

$ILBIP = "<n.n.n.n>"                         # IP address
[int]$ListenerPort = "<nnnn>"                # AG listener port
[int]$ProbePort = "<nnnn>"                   # Probe port

$LBProbeName ="ILBPROBE_$ListenerPort"       # The Load balancer Probe Object Name              
$LBConfigRuleName = "ILBCR_$ListenerPort"    # The Load Balancer Rule Object Name

$FrontEndConfigurationName = "FE_SQLAGILB_1" # Object name for the front-end configuration 
$BackEndConfigurationName ="BE_SQLAGILB_1"   # Object name for the back-end configuration

$VNet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName 

$Subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName 

$FEConfig = New-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.id

$BEConfig = New-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName 

$SQLHealthProbe = New-AzLoadBalancerProbeConfig -Name $LBProbeName -Protocol tcp -Port $ProbePort -IntervalInSeconds 15 -ProbeCount 2

$ILBRule = New-AzLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -Probe $SQLHealthProbe -Protocol tcp -FrontendPort $ListenerPort -BackendPort $ListenerPort -LoadDistribution Default -EnableFloatingIP 

$ILB= New-AzLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe 

$bepool = Get-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB 

foreach($VMName in $VMNames)
    {
        $VM = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $VMName 
        $NICName = ($vm.NetworkProfile.NetworkInterfaces.Id.split('/') | select -last 1)
        $NIC = Get-AzNetworkInterface -name $NICName -ResourceGroupName $ResourceGroupName
        $NIC.IpConfigurations[0].LoadBalancerBackendAddressPools = $BEPool
        Set-AzNetworkInterface -NetworkInterface $NIC
        start-AzVM -ResourceGroupName $ResourceGroupName -Name $VM.Name 
    }
```

## <a name="Add-IP"></a>Örnek betik: PowerShell ile mevcut yük dengeleyicisine bir IP adresi ekleme
Birden fazla kullanılabilirlik grubu kullanmak için yük dengeleyicisine ek bir IP adresi ekleyin. Her IP adresi kendi yük dengeleme kuralını, araştırma bağlantı noktasını ve ön bağlantı noktasını gerektirir.

Ön uç bağlantı noktası, uygulamaların SQL Server örneğine bağlanmak için kullandığı bağlantı noktasıdır. Farklı kullanılabilirlik gruplarının IP adresleri aynı ön uç bağlantı noktasını kullanabilir.

> [!NOTE]
> SQL Server kullanılabilirlik grupları için, her IP adresi için belirli bir yoklama bağlantı noktası gerekir. Örneğin, bir yük dengeleyicideki bir IP adresi araştırma bağlantı noktası 59999 ' i kullanıyorsa, bu yük dengeleyicide başka IP adresleri araştırma bağlantı noktası 59999 ' i kullanabilir.

* Yük dengeleyici sınırları hakkında daha fazla bilgi için bkz. [ağ sınırları](../../../azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits)altında **yük dengeleyici başına özel ön uç IP 'si** Azure Resource Manager.
* Kullanılabilirlik grubu sınırları hakkında daha fazla bilgi için bkz. [kısıtlamalar (kullanılabilirlik grupları)](https://msdn.microsoft.com/library/ff878487.aspx#RestrictionsAG).

Aşağıdaki betik var olan bir yük dengeleyiciye yeni bir IP adresi ekler. ILB, Yük Dengeleme ön uç bağlantı noktası için dinleyici bağlantı noktasını kullanır. Bu bağlantı noktası SQL Server dinlediği bağlantı noktası olabilir. SQL Server varsayılan örnekleri için bağlantı noktası 1433 ' dir. Bir kullanılabilirlik grubunun Yük Dengeleme kuralı, bir kayan IP (doğrudan sunucu dönüşü) gerektirir, bu nedenle arka uç bağlantı noktası ön uç bağlantı noktasıyla aynı olur. Ortamınızın değişkenlerini güncelleştirin. 

```powershell
# Connect-AzAccount
# Select-AzSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>

$ResourceGroupName = "<ResourceGroup>"          # Resource group name
$VNetName = "<VirtualNetwork>"                  # Virtual network name
$SubnetName = "<Subnet>"                        # Subnet name
$ILBName = "<ILBName>"                          # ILB name                      

$ILBIP = "<n.n.n.n>"                            # IP address
[int]$ListenerPort = "<nnnn>"                   # AG listener port
[int]$ProbePort = "<nnnnn>"                     # Probe port 

$ILB = Get-AzLoadBalancer -Name $ILBName -ResourceGroupName $ResourceGroupName 

$count = $ILB.FrontendIpConfigurations.Count+1
$FrontEndConfigurationName ="FE_SQLAGILB_$count"  

$LBProbeName = "ILBPROBE_$count"
$LBConfigrulename = "ILBCR_$count"

$VNet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName 
$Subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName

$ILB | Add-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.Id 

$ILB | Add-AzLoadBalancerProbeConfig -Name $LBProbeName  -Protocol Tcp -Port $Probeport -ProbeCount 2 -IntervalInSeconds 15  | Set-AzLoadBalancer 

$ILB = Get-AzLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName

$FEConfig = get-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB

$SQLHealthProbe  = Get-AzLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

$BEConfig = Get-AzLoadBalancerBackendAddressPoolConfig -Name $ILB.BackendAddressPools[0].Name -LoadBalancer $ILB 

$ILB | Add-AzLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig  -BackendAddressPool $BEConfig -Probe $SQLHealthProbe -Protocol tcp -FrontendPort  $ListenerPort -BackendPort $ListenerPort -LoadDistribution Default -EnableFloatingIP | Set-AzLoadBalancer   
```

## <a name="configure-the-listener"></a>Dinleyiciyi yapılandırma

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

## <a name="set-the-listener-port-in-sql-server-management-studio"></a>SQL Server Management Studio dinleyici bağlantı noktasını ayarlama

1. SQL Server Management Studio başlatın ve birincil çoğaltmaya bağlanın.

1. **Kullanılabilirlik grubu dinleyicilerine** | **AlwaysOn yüksek kullanılabilirlik** | **kullanılabilirlik grupları** ' na gidin. 

1. Artık Yük Devretme Kümesi Yöneticisi oluşturduğunuz dinleyici adını görmeniz gerekir. Dinleyici adına sağ tıklayın ve **Özellikler**' e tıklayın.

1. **Bağlantı noktası** kutusunda, daha önce kullandığınız $EndpointPort kullanarak kullanılabilirlik grubu dinleyicisinin bağlantı noktası numarasını belirtin (1433 varsayılandır) ve ardından **Tamam**' a tıklayın.

## <a name="test-the-connection-to-the-listener"></a>Dinleyiciyle bağlantıyı test etme

Bağlantıyı test etmek için:

1. Aynı sanal ağ içinde olan, ancak çoğaltmaya sahip olmayan bir SQL Server RDP. Bu, kümedeki diğer SQL Server olabilir.

1. Bağlantıyı sınamak için **sqlcmd** yardımcı programını kullanın. Örneğin, aşağıdaki komut dosyası, Windows kimlik doğrulaması ile dinleyici aracılığıyla birincil çoğaltmaya bir **sqlcmd** bağlantısı kurar:
   
    ```
    sqlcmd -S <listenerName> -E
    ```
   
    Dinleyici varsayılan bağlantı noktası (1433) dışında bir bağlantı noktası kullanıyorsa bağlantı dizesinde bağlantı noktasını belirtin. Örneğin, aşağıdaki sqlcmd komutu 1435 numaralı bağlantı noktasında bir dinleyiciye bağlanır: 
   
    ```
    sqlcmd -S <listenerName>,1435 -E
    ```

SQLCMD bağlantısı, birincil çoğaltmayı barındıran SQL Server her örneğine otomatik olarak bağlanır. 

> [!NOTE]
> Belirttiğiniz bağlantı noktasının her iki SQL Server güvenlik duvarında açık olduğundan emin olun. Her iki sunucu da kullandığınız TCP bağlantı noktası için bir gelen kuralı gerektirir. Daha fazla bilgi için bkz. [güvenlik duvarı kuralı ekleme veya düzenleme](https://technet.microsoft.com/library/cc753558.aspx) . 
> 
> 

## <a name="guidelines-and-limitations"></a>Kılavuzlar ve sınırlamalar
İç yük dengeleyici kullanılarak Azure 'da kullanılabilirlik grubu dinleyicisi hakkında aşağıdaki yönergelere göz önünde edin:

* İç yük dengeleyici ile yalnızca aynı sanal ağ içinden gelen dinleyiciye erişirsiniz.

* Azure ağ güvenlik grubuyla erişimi kısıtladığınız takdirde, izin ver kurallarının arka uç SQL Server VM IP adreslerini ve ağ dinleyicisi için yük dengeleyici kayan IP adreslerini ve varsa küme çekirdek IP adresini içerdiğinden emin olun.

## <a name="for-more-information"></a>Daha fazla bilgi edinmek için
Daha fazla bilgi için bkz. [Azure VM 'de Always on kullanılabilirlik grubunu el Ile yapılandırma](virtual-machines-windows-portal-sql-availability-group-tutorial.md).

## <a name="powershell-cmdlets"></a>PowerShell cmdlet'leri
Azure sanal makineleri için bir iç yük dengeleyici oluşturmak için aşağıdaki PowerShell cmdlet 'lerini kullanın.

* [New-AzLoadBalancer](https://msdn.microsoft.com/library/mt619450.aspx) bir yük dengeleyici oluşturur. 
* [New-Azloadbalancerfrontendıpconfig](https://msdn.microsoft.com/library/mt603510.aspx) , yük dengeleyici için bir ön uç IP yapılandırması oluşturur. 
* [New-AzLoadBalancerRuleConfig](https://msdn.microsoft.com/library/mt619391.aspx) , bir yük dengeleyici için bir kural yapılandırması oluşturur. 
* [New-Azloadbalancerbackendadddresspoolconfig](https://msdn.microsoft.com/library/mt603791.aspx) , bir yük dengeleyici için arka uç adres havuzu yapılandırması oluşturur. 
* [New-AzLoadBalancerProbeConfig](https://msdn.microsoft.com/library/mt603847.aspx) , yük dengeleyici için bir araştırma yapılandırması oluşturur.
* [Remove-AzLoadBalancer](https://msdn.microsoft.com/library/mt603862.aspx) bir Azure Kaynak grubundaki yük dengeleyiciyi kaldırır.
