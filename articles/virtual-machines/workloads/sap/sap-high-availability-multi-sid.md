---
title: Azure 'da SAP çoklu SID yapılandırması oluşturma | Microsoft Docs
description: Windows sanal makinelerinde yüksek kullanılabilirliğe sahip SAP NetWeaver çok SID yapılandırmasına kılavuz
services: virtual-machines-windows, virtual-network, storage
documentationcenter: saponazure
author: goraco
manager: gwallace
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 0b89b4f8-6d6c-45d7-8d20-fe93430217ca
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 75d4c4e38069cb192917f275245d87bb4c63d502
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70078162"
---
# <a name="create-an-sap-netweaver-multi-sid-configuration"></a>SAP NetWeaver çoklu SID yapılandırması oluşturma

[load-balancer-multivip-overview]:../../../load-balancer/load-balancer-multivip-overview.md
[sap-ha-guide]:sap-high-availability-guide.md 
[sap-ha-guide-figure-6001]:./media/virtual-machines-shared-sap-high-availability-guide/6001-sap-multi-sid-ascs-scs-sid1.png
[sap-ha-guide-figure-6002]:./media/virtual-machines-shared-sap-high-availability-guide/6002-sap-multi-sid-ascs-scs.png
[sap-ha-guide-figure-6003]:./media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png
[sap-ha-guide-figure-6004]:./media/virtual-machines-shared-sap-high-availability-guide/6004-sap-multi-sid-dns.png
[sap-ha-guide-figure-6005]:./media/virtual-machines-shared-sap-high-availability-guide/6005-sap-multi-sid-azure-portal.png
[sap-ha-guide-figure-6006]:./media/virtual-machines-shared-sap-high-availability-guide/6006-sap-multi-sid-sios-replication.png
[networking-limits-azure-resource-manager]:../../../azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits
[sap-ha-guide-9.1.1]:sap-high-availability-guide.md#a97ad604-9094-44fe-a364-f89cb39bf097 
[sap-ha-guide-8.8]:sap-high-availability-guide.md#f19bd997-154d-4583-a46e-7f5a69d0153c
[sap-ha-guide-8.12.3.3]:sap-high-availability-guide.md#d9c1fc8e-8710-4dff-bec2-1f535db7b006 
[sap-ha-guide-9]:sap-high-availability-guide.md#a06f0b49-8a7a-42bf-8b0d-c12026c5746b
[sap-ha-guide-9.1]:sap-high-availability-guide.md#31c6bd4f-51df-4057-9fdf-3fcbc619c170 
[sap-ha-guide-9.1.2]:sap-high-availability-guide.md#eb5af918-b42f-4803-bb50-eff41f84b0b0 
[sap-ha-guide-9.1.3]:sap-high-availability-guide.md#e4caaab2-e90f-4f2c-bc84-2cd2e12a9556 
[sap-ha-guide-9.1.4]:sap-high-availability-guide.md#10822f4f-32e7-4871-b63a-9b86c76ce761 
[sap-ha-guide-9.4]:sap-high-availability-guide.md#094bc895-31d4-4471-91cc-1513b64e406a 
[sap-ha-guide-9.5]:sap-high-availability-guide.md#2477e58f-c5a7-4a5d-9ae3-7b91022cafb5 
[sap-ha-guide-9.6]:sap-high-availability-guide.md#0ba4a6c1-cc37-4bcf-a8dc-025de4263772 
[sap-ha-guide-10]:sap-high-availability-guide.md#18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9

Microsoft, Eylül 2016 ' de bir Azure iç yük dengeleyici kullanarak birden çok sanal IP adresini yönetebileceğiniz bir özellik yayımladı. Bu işlevsellik Azure dış yük dengeleyicisinde zaten var.

SAP dağıtımınız varsa, [Windows VM 'lerinde yüksek kullanılabilirliğe sahıp SAP NetWeaver için kılavuzda][sap-ha-guide]gösterildiği gıbı, SAP Ass/SCS Için bir Windows küme yapılandırması oluşturmak üzere iç yük dengeleyici kullanabilirsiniz.

Bu makalede, var olan bir Windows Server Yük Devretme Kümelemesi (WSFC) kümesine ek SAP ASCS/SCS kümelenmiş örnekleri yükleyerek tek bir ASCS/SCS yüklemesinden SAP çoklu SID yapılandırmasına nasıl geçiş yapılacağı ele alınmaktadır. Bu işlem tamamlandığında, bir SAP çoklu SID kümesi yapılandırmış olursunuz.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Önkoşullar
Windows VM 'lerde ve bu diyagramda gösterildiği gibi [yüksek kullanılabilirliğe sahıp SAP NetWeaver için kılavuzda][sap-ha-guide] açıklandığı gibi, BIR SAP ascs/SCS örneği için kullanılan bir wsfc kümesini zaten yapılandırdınız.

![Yüksek kullanılabilirliğe sahip SAP yoks/SCS örneği][sap-ha-guide-figure-6001]

## <a name="target-architecture"></a>Hedef mimari

Amaç, burada gösterildiği gibi birden çok SAP ABAP yoks veya SAP Java SCS kümelenmiş örneğini aynı WSFC kümesine yüklemektir:

![Azure 'da birden çok SAP ASCS/SCS kümelenmiş örneği][sap-ha-guide-figure-6002]

> [!NOTE]
>Her bir Azure iç yük dengeleyici için özel ön uç IP sayısı sınırı vardır.
>
>Bir WSFC kümesindeki en fazla SAP ASCS/SCS örneği sayısı, her bir Azure iç yük dengeleyici için en fazla özel ön uç IP sayısına eşittir.
>

Yük dengeleyici sınırları hakkında daha fazla bilgi için bkz. ağ sınırları içinde ["yük dengeleyici başına özel ön uç IP". Azure Resource Manager][networking-limits-azure-resource-manager].

İki yüksek kullanılabilirliğe sahip SAP sistemiyle tam yatay şöyle görünür:

![İki SAP sistem SID ile SAP yüksek kullanılabilirliğe sahip çok düzeyli kurulum][sap-ha-guide-figure-6003]

> [!IMPORTANT]
> Kurulumun aşağıdaki koşullara uyması gerekir:
> - SAP ASCS/SCS örnekleri aynı WSFC kümesini paylaşmalıdır.
> - Her bir DBMS SID 'sinin kendi adanmış WSFC kümesi olmalıdır.
> - Bir SAP sistem SID 'sine ait SAP uygulama sunucularının kendi ayrılmış VM 'lerine sahip olması gerekir.


## <a name="prepare-the-infrastructure"></a>Altyapıyı hazırlama
Altyapınızı hazırlamak için aşağıdaki parametrelerle ek bir SAP ASCS/SCS örneği yükleyebilirsiniz:

| Parametre adı | Value |
| --- | --- |
| SAP YOKS/SCS SID 'SI |PR1-lb-yoks |
| SAP DBMS iç yük dengeleyici | PR5 |
| SAP sanal ana bilgisayar adı | PR5-SAP-CL |
| SAP ASCS/SCS sanal ana bilgisayar IP adresi (ek Azure yük dengeleyici IP adresi) | 10.0.0.50 |
| SAP ASCS/SCS örnek numarası | 50 |
| Ek SAP yoks/SCS örneği için ıLB araştırma bağlantı noktası | 62350 |

> [!NOTE]
> SAP ASCS/SCS küme örnekleri için her IP adresi için benzersiz bir yoklama bağlantı noktası gerekir. Örneğin, bir Azure iç yük dengeleyicideki bir IP adresi araştırma bağlantı noktası 62300 kullanıyorsa, bu yük dengeleyicide başka bir IP adresi araştırma bağlantı noktası 62300 ' i kullanabilir.
>
>Araştırma bağlantı noktası 62300 zaten ayrıldığından, bizim için araştırma bağlantı noktası 62350 ' i kullanıyoruz.

Mevcut WSFC kümesine iki düğüm ile ek SAP yoks/SCS örneği yükleyebilirsiniz:

| Sanal makine rolü | Sanal makine konak adı | Statik IP adresi |
| --- | --- | --- |
| ASCS/SCS örneği için 1. küme düğümü |PR1-ascs-0 |10.0.0.10 |
| ASCS/SCS örneği için 2. küme düğümü |PR1-ascs-1 |10.0.0.9 |

### <a name="create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance-on-the-dns-server"></a>DNS sunucusunda kümelenmiş SAP Ass/SCS örneği için bir sanal konak adı oluşturun

Aşağıdaki parametreleri kullanarak, ASCS/SCS örneğinin sanal ana bilgisayar adı için bir DNS girişi oluşturabilirsiniz:

| Yeni SAP yoks/SCS sanal ana bilgisayar adı | İlişkili IP adresi |
| --- | --- |
|PR5-SAP-CL |10.0.0.50 |

Yeni ana bilgisayar adı ve IP adresi, aşağıdaki ekran görüntüsünde gösterildiği gibi DNS Yöneticisi 'nde görüntülenir:

![Yeni SAP yoks/SCS kümesi sanal adı ve TCP/IP adresi için tanımlı DNS girişini vurgulayan DNS Yöneticisi listesi][sap-ha-guide-figure-6004]

DNS girişi oluşturma yordamı, [Windows VM 'lerinde yüksek kullanılabilirliğe sahıp SAP NetWeaver için ana kılavuzda][sap-ha-guide-9.1.1]ayrıntılı olarak da açıklanmıştır.

> [!NOTE]
> Ek ASCS/SCS örneğinin sanal ana bilgisayar adına atadığınız yeni IP adresi, SAP Azure yük dengeleyicisine atadığınız yeni IP adresiyle aynı olmalıdır.
>
>Senaryomızda, IP adresi 10.0.0.50 ' dir.

### <a name="add-an-ip-address-to-an-existing-azure-internal-load-balancer-by-using-powershell"></a>PowerShell kullanarak var olan bir Azure iç yük dengeleyicisine bir IP adresi ekleme

Aynı WSFC kümesinde birden fazla SAP ASCS/SCS örneği oluşturmak için, PowerShell kullanarak var olan bir Azure iç yük dengeleyicisine bir IP adresi ekleyin. Her IP adresi kendi yük dengeleme kurallarını, araştırma bağlantı noktasını, ön uç IP havuzunu ve arka uç havuzunu gerektirir.

Aşağıdaki betik var olan bir yük dengeleyiciye yeni bir IP adresi ekler. Ortamınız için PowerShell değişkenlerini güncelleştirin. Betik tüm SAP yoks/SCS bağlantı noktaları için tüm gerekli Yük Dengeleme kurallarını oluşturur.

```powershell

# Select-AzSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>
Clear-Host
$ResourceGroupName = "SAP-MULTI-SID-Landscape"      # Existing resource group name
$VNetName = "pr2-vnet"                        # Existing virtual network name
$SubnetName = "Subnet"                        # Existing subnet name
$ILBName = "pr2-lb-ascs"                      # Existing ILB name                      
$ILBIP = "10.0.0.50"                          # New IP address
$VMNames = "pr2-ascs-0","pr2-ascs-1"          # Existing cluster virtual machine names
$SAPInstanceNumber = 50                       # SAP ASCS/SCS instance number: must be a unique value for each cluster
[int]$ProbePort = "623$SAPInstanceNumber"     # Probe port: must be a unique value for each IP and load balancer

$ILB = Get-AzLoadBalancer -Name $ILBName -ResourceGroupName $ResourceGroupName

$count = $ILB.FrontendIpConfigurations.Count + 1
$FrontEndConfigurationName ="lbFrontendASCS$count"
$LBProbeName = "lbProbeASCS$count"

# Get the Azure VNet and subnet
$VNet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName
$Subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName

# Add second front-end and probe configuration
Write-Host "Adding new front end IP Pool '$FrontEndConfigurationName' ..." -ForegroundColor Green
$ILB | Add-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.Id
$ILB | Add-AzLoadBalancerProbeConfig -Name $LBProbeName  -Protocol Tcp -Port $Probeport -ProbeCount 2 -IntervalInSeconds 10  | Set-AzLoadBalancer

# Get new updated configuration
$ILB = Get-AzLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName
# Get new updated LP FrontendIP COnfig
$FEConfig = Get-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB
$HealthProbe  = Get-AzLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

# Add new back-end configuration into existing ILB
$BackEndConfigurationName  = "backendPoolASCS$count"
Write-Host "Adding new backend Pool '$BackEndConfigurationName' ..." -ForegroundColor Green
$BEConfig = Add-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB | Set-AzLoadBalancer

# Get new updated config
$ILB = Get-AzLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName

# Assign VM NICs to backend pool
$BEPool = Get-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB
foreach($VMName in $VMNames){
        $VM = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $VMName
        $NICName = ($VM.NetworkInterfaceIDs[0].Split('/') | select -last 1)        
        $NIC = Get-AzNetworkInterface -name $NICName -ResourceGroupName $ResourceGroupName                
        $NIC.IpConfigurations[0].LoadBalancerBackendAddressPools += $BEPool
        Write-Host "Assigning network card '$NICName' of the '$VMName' VM to the backend pool '$BackEndConfigurationName' ..." -ForegroundColor Green
        Set-AzNetworkInterface -NetworkInterface $NIC
        #start-AzVM -ResourceGroupName $ResourceGroupName -Name $VM.Name
}


# Create load-balancing rules
$Ports = "445","32$SAPInstanceNumber","33$SAPInstanceNumber","36$SAPInstanceNumber","39$SAPInstanceNumber","5985","81$SAPInstanceNumber","5$SAPInstanceNumber`13","5$SAPInstanceNumber`14","5$SAPInstanceNumber`16"
$ILB = Get-AzLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName
$FEConfig = get-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB
$BEConfig = Get-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB
$HealthProbe  = Get-AzLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

Write-Host "Creating load balancing rules for the ports: '$Ports' ... " -ForegroundColor Green

foreach ($Port in $Ports) {

        $LBConfigrulename = "lbrule$Port" + "_$count"
        Write-Host "Creating load balancing rule '$LBConfigrulename' for the port '$Port' ..." -ForegroundColor Green

        $ILB | Add-AzLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig  -BackendAddressPool $BEConfig -Probe $HealthProbe -Protocol tcp -FrontendPort  $Port -BackendPort $Port -IdleTimeoutInMinutes 30 -LoadDistribution Default -EnableFloatingIP   
}

$ILB | Set-AzLoadBalancer

Write-Host "Successfully added new IP '$ILBIP' to the internal load balancer '$ILBName'!" -ForegroundColor Green

```
Komut dosyası çalıştırıldıktan sonra, aşağıdaki ekran görüntüsünde gösterildiği gibi sonuçlar Azure portal görüntülenir:

![Azure portal yeni ön uç IP Havuzu][sap-ha-guide-figure-6005]

### <a name="add-disks-to-cluster-machines-and-configure-the-sios-cluster-share-disk"></a>Küme makinelerine disk ekleme ve SIOS küme paylaşma diskini yapılandırma

Her ek SAP ASCS/SCS örneği için yeni bir küme paylaşma diski eklemeniz gerekir. Windows Server 2012 R2 için şu anda kullanılmakta olan WSFC küme paylaşma diski, SIOS Verilerman yazılım çözümüdür.

Şunları yapın:
1. Küme düğümlerinin her birine aynı boyuttaki (Stripe için gereklidir) ek bir disk veya disk ekleyin ve bunları biçimlendirin.
2. SIOS Dataman ile depolama çoğaltmasını yapılandırın.

Bu yordamda, WSFC küme makinelerine zaten SIOS Dataman yüklediğinizi varsayılmaktadır. Uygulamayı yüklediyseniz, artık makineler arasında çoğaltmayı yapılandırmanız gerekir. Bu işlem, [Windows VM 'lerinde yüksek kullanılabilirliğe sahıp SAP NetWeaver için ana kılavuzda][sap-ha-guide-8.12.3.3]ayrıntılı olarak açıklanır.  

![Yeni SAP Ass/SCS paylaşma diski için Dataman zaman uyumlu yansıtma][sap-ha-guide-figure-6006]

### <a name="deploy-vms-for-sap-application-servers-and-dbms-cluster"></a>SAP uygulama sunucuları ve DBMS kümesi için VM 'Leri dağıtma

İkinci SAP sistemine yönelik altyapı hazırlığını gerçekleştirmek için aşağıdakileri yapın:

1. SAP uygulama sunucuları için adanmış VM 'Ler dağıtın ve bunları kendi adanmış kullanılabilirlik grubuna yerleştirin.
2. DBMS kümesi için adanmış VM 'Leri dağıtın ve bunları kendi adanmış kullanılabilirlik grubuna yerleştirin.


## <a name="install-the-second-sap-sid2-netweaver-system"></a>İkinci SAP SID2 NetWeaver sistemini yükler

İkinci bir SAP SID2 sisteminin yüklenmesiyle ilgili tam işlem, [Windows VM 'lerde yüksek KULLANıLABILIRLIK SAP NetWeaver için ana kılavuzda][sap-ha-guide-9]açıklanmaktadır.

Üst düzey yordam aşağıdaki gibidir:

1. [SAP ilk küme düğümünü yükler][sap-ha-guide-9.1.2].  
 Bu adımda, **mevcut wsfc küme düğümü 1**üzerinde yüksek kullanılabilirliğe sahıp bır ascs/SCS örneğiyle SAP 'yi yüklüyorsunuz.

2. [ASCS/SCS ÖRNEĞININ SAP profilini değiştirin][sap-ha-guide-9.1.3].

3. [Bir araştırma bağlantı noktası yapılandırın][sap-ha-guide-9.1.4].  
 Bu adımda, PowerShell kullanarak SAP küme kaynağı SAP-SID2-IP araştırma bağlantı noktasını yapılandırıyorsunuz. Bu yapılandırmayı SAP ASCS/SCS küme düğümlerinden birinde yürütün.

4. [Veritabanı örneğini yükler] [SAP-ha-Guide-9,2].  
 Bu adımda, özel bir WSFC kümesine DBMS yüklüyorsunuz.

5. [İkinci küme düğümünü yükler] [SAP-ha-Guide-9,3].  
 Bu adımda, mevcut WSFC küme düğümü 2 ' de yüksek kullanılabilirliğe sahip bir ASCS/SCS örneği ile SAP 'yi yüklüyorsunuz.

6. SAP ASCS/SCS örneği ve ProbePort için Windows Güvenlik Duvarı bağlantı noktalarını açın.  
 SAP ASCS/SCS örnekleri için kullanılan her iki küme düğümünde de SAP Ass/SCS tarafından kullanılan tüm Windows Güvenlik Duvarı bağlantı noktalarını açıyor olursunuz. Bu bağlantı noktaları, [Windows VM 'lerinde yüksek kullanılabilirliğe sahıp SAP NetWeaver Kılavuzu][sap-ha-guide-8.8]' nda listelenmiştir.  
 Ayrıca, Senaryomuzda 62350 olan Azure iç yük dengeleyici araştırma bağlantı noktasını açın.

7. [SAP ers Windows hizmeti örneğinin başlangıç türünü değiştirin][sap-ha-guide-9.4].

8. [SAP birincil uygulama sunucusunu][sap-ha-guide-9.5] yenı adanmış VM 'ye yükler.

9. [SAP ek uygulama sunucusunu][sap-ha-guide-9.6] yenı adanmış VM 'ye yükler.

10. [SAP ASCS/SCS örneği yük devretme ve SIOS çoğaltmasını test][sap-ha-guide-10]edin.

## <a name="next-steps"></a>Sonraki adımlar

- [Ağ sınırları: Azure Resource Manager][networking-limits-azure-resource-manager]
- [Azure Load Balancer için birden çok VIP][load-balancer-multivip-overview]
- [Windows VM 'lerinde yüksek kullanılabilirliğe sahip SAP NetWeaver Kılavuzu][sap-ha-guide]
