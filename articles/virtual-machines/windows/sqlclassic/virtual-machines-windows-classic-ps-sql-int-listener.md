---
title: Kullanılabilirlik grupları için bir ıLB dinleyicisi yapılandırma (klasik)
description: Bu öğretici, klasik dağıtım modeliyle oluşturulan kaynakları kullanır ve Azure 'da iç yük dengeleyici kullanan bir SQL Server VM için her zaman açık kullanılabilirlik grubu dinleyicisi oluşturur.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 291288a0-740b-4cfa-af62-053218beba77j
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/02/2017
ms.author: mikeray
ms.custom: seo-lt-2019
ms.openlocfilehash: 29aaedeafb3995cc09e221d2e049dd538808904a
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74032667"
---
# <a name="configure-an-ilb-listener-for-availability-groups-on-azure-sql-server-vms"></a>Azure SQL Server VM 'lerinde kullanılabilirlik grupları için bir ıLB dinleyicisi yapılandırma
> [!div class="op_single_selector"]
> * [İç dinleyici](../classic/ps-sql-int-listener.md)
> * [Dış dinleyici](../classic/ps-sql-ext-listener.md)
>
>

## <a name="overview"></a>Genel Bakış

> [!IMPORTANT]
> Azure 'da kaynak oluşturmak ve bunlarla çalışmak için iki farklı dağıtım modeli vardır: [Azure Resource Manager ve klasik](../../../azure-resource-manager/resource-manager-deployment-model.md). Bu makalede klasik dağıtım modelinin kullanımı ele alınmaktadır. En yeni dağıtımların Kaynak Yöneticisi modelini kullanmasını öneririz.

Kaynak Yöneticisi modelinde her zaman açık kullanılabilirlik grubu için bir dinleyici yapılandırmak üzere bkz. [Azure 'Da Always on kullanılabilirlik grubu için yük dengeleyici yapılandırma](../sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md).

Kullanılabilirlik grubunuz yalnızca şirket içinde veya yalnızca Azure 'da olan veya hem şirket içi hem de karma Yapılandırma için Azure 'a yayılan çoğaltmalar içerebilir. Azure çoğaltmaları aynı bölgede veya birden çok sanal ağ kullanan birden çok bölgede bulunabilir. Bu makaledeki yordamlarda, bir [kullanılabilirlik grubunu zaten yapılandırdığınız](../classic/portal-sql-alwayson-availability-groups.md) ancak henüz bir dinleyici yapılandırdığınızı varsayalım.

## <a name="guidelines-and-limitations-for-internal-listeners"></a>İç dinleyiciler için yönergeler ve sınırlamalar
Azure 'da bir kullanılabilirlik grubu dinleyicisi ile iç yük dengeleyici (ıLB) kullanımı aşağıdaki yönergelere tabidir:

* Kullanılabilirlik grubu dinleyicisi Windows Server 2008 R2, Windows Server 2012 ve Windows Server 2012 R2 üzerinde desteklenir.
* Her bulut hizmeti için yalnızca bir iç kullanılabilirlik grubu dinleyicisi desteklenir, çünkü dinleyici ıLB 'ye yapılandırılmıştır ve her bir bulut hizmeti için yalnızca bir ıLB vardır. Ancak, birden çok dış dinleyici oluşturmak mümkündür. Daha fazla bilgi için bkz. [Azure 'Da Always on kullanılabilirlik grupları için dış dinleyici yapılandırma](../classic/ps-sql-ext-listener.md).

## <a name="determine-the-accessibility-of-the-listener"></a>Dinleyicinin erişilebilirliğini belirleme
[!INCLUDE [ag-listener-accessibility](../../../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

Bu makale, ıLB kullanan bir dinleyici oluşturmaya odaklanır. Ortak veya dış dinleyiciye ihtiyacınız varsa, bu makalenin bir [dış dinleyiciyi](../classic/ps-sql-ext-listener.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)ayarlamayı ele alan sürümüne bakın.

## <a name="create-load-balanced-vm-endpoints-with-direct-server-return"></a>Doğrudan sunucu dönüşü ile yük dengeli VM uç noktaları oluşturma
İlk olarak bu bölümün ilerleyen kısımlarında betiği çalıştırarak bir ıLB oluşturursunuz.

Azure çoğaltması barındıran her VM için yük dengeli bir uç nokta oluşturun. Birden çok bölgede çoğaltmalar varsa, söz konusu bölgeye yönelik her çoğaltma aynı Azure sanal ağındaki aynı bulut hizmetinde olmalıdır. Birden çok Azure bölgesini kapsayan kullanılabilirlik grubu çoğaltmaları oluşturmak için birden çok sanal ağ yapılandırmanız gerekir. Çapraz sanal ağ bağlantısını yapılandırma hakkında daha fazla bilgi için bkz. [sanal ağı sanal ağ bağlantısı Ile yapılandırma](../../../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

1. Azure portal, ayrıntıları görüntülemek için bir çoğaltma barındıran her VM 'ye gidin.

2. Her VM için **uç noktalar** sekmesine tıklayın.

3. Kullanmak istediğiniz dinleyici uç noktasının **adının** ve **genel bağlantı noktasının** zaten kullanımda olmadığından emin olun. Bu bölümdeki örnekte, ad *myEndpoint*, bağlantı noktası *1433*' dir.

4. Yerel istemcinizdeki en son [PowerShell modülünü](https://azure.microsoft.com/downloads/)indirip yükleyin.

5. Azure PowerShell başlatın.  
    Yeni bir PowerShell oturumu açılarak Azure yönetim modülleri yüklendi.

6. `Get-AzurePublishSettingsFile` öğesini çalıştırın. Bu cmdlet, bir yayımlama ayarları dosyasını yerel bir dizine indirmek için sizi bir tarayıcıya yönlendirir. Azure aboneliğiniz için oturum açma kimlik bilgileriniz istenebilir.

7. Aşağıdaki `Import-AzurePublishSettingsFile` komutunu, indirdiğiniz yayımlama ayarları dosyasının yoluyla çalıştırın:

        Import-AzurePublishSettingsFile -PublishSettingsFile <PublishSettingsFilePath>

    Yayımlama ayarları dosyası alındıktan sonra, Azure aboneliğinizi PowerShell oturumunda yönetebilirsiniz.

8. *ILB*için BIR statik IP adresi atayın. Şu komutu çalıştırarak geçerli sanal ağ yapılandırmasını inceleyin:

        (Get-AzureVNetConfig).XMLConfiguration
9. Çoğaltmaları barındıran VM 'Leri içeren alt ağın *alt ağ* adını unutmayın. Bu ad, betikteki $SubnetName parametresinde kullanılır.

10. Çoğaltmaları barındıran VM 'Leri içeren alt ağ için *Virtualnetworksite* adı ve başlangıç *adresispredüzeltmesini* unutmayın. Her iki değeri `Test-AzureStaticVNetIP` komutuna geçirerek ve *Availableaddresses*inceleyerek KULLANILABILIR bir IP adresi arayın. Örneğin, sanal ağın adı *Myvnet* ise ve *172.16.0.128*adresinde başlayan bir alt ağ adres aralığı varsa, aşağıdaki komut kullanılabilir adresleri listeler:

        (Test-AzureStaticVNetIP -VNetName "MyVNet"-IPAddress 172.16.0.128).AvailableAddresses
11. Kullanılabilir adreslerden birini seçin ve sonraki adımda betiğin $ILBStaticIP parametresinde kullanın.

12. Aşağıdaki PowerShell betiğini bir metin düzenleyicisine kopyalayın ve değişken değerlerini ortamınıza uyacak şekilde ayarlayın. Bazı parametreler için varsayılanlar sağlanmış.  

    Benzeşim grupları kullanan mevcut dağıtımlar ıLB ekleyemez. ILB gereksinimleri hakkında daha fazla bilgi için bkz. [İç Yük Dengeleyiciye genel bakış](../../../load-balancer/load-balancer-internal-overview.md).

    Ayrıca, kullanılabilirlik grubunuz Azure bölgelerini yaydığı takdirde, bu veri merkezinde bulunan bulut hizmeti ve düğümleri için her bir veri merkezinde betiği bir kez çalıştırmanız gerekir.

        # Define variables
        $ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
        $AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas
        $SubnetName = "<MySubnetName>" # subnet name that the replicas use in the virtual network
        $ILBStaticIP = "<MyILBStaticIPAddress>" # static IP address for the ILB in the subnet
        $ILBName = "AGListenerLB" # customize the ILB name or use this default value

        # Create the ILB
        Add-AzureInternalLoadBalancer -InternalLoadBalancerName $ILBName -SubnetName $SubnetName -ServiceName $ServiceName -StaticVNetIPAddress $ILBStaticIP

        # Configure a load-balanced endpoint for each node in $AGNodes by using ILB
        ForEach ($node in $AGNodes)
        {
            Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -LBSetName "ListenerEndpointLB" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -InternalLoadBalancerName $ILBName -DirectServerReturn $true | Update-AzureVM
        }

13. Değişkenleri ayarladıktan sonra dosyayı çalıştırmak için metin düzenleyicisinden betiği PowerShell oturumunuza kopyalayın. İstem hala **>>** gösteriyorsa, betiğin çalışmaya başlamasını sağlamak Için yeniden ENTER tuşuna basın.

## <a name="verify-that-kb2854082-is-installed-if-necessary"></a>Gerekirse KB2854082 yüklendiğini doğrulayın
[!INCLUDE [kb2854082](../../../../includes/virtual-machines-ag-listener-kb2854082.md)]

## <a name="open-the-firewall-ports-in-availability-group-nodes"></a>Kullanılabilirlik grubu düğümlerinde güvenlik duvarı bağlantı noktalarını açma
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-open-firewall.md)]

## <a name="create-the-availability-group-listener"></a>Kullanılabilirlik grubu dinleyicisini oluşturma

Kullanılabilirlik grubu dinleyicisini iki adımda oluşturun. İlk olarak, istemci erişim noktası kümesi kaynağını oluşturun ve bağımlılıkları yapılandırın. İkincisi, PowerShell 'de küme kaynaklarını yapılandırın.

### <a name="create-the-client-access-point-and-configure-the-cluster-dependencies"></a>İstemci erişim noktasını oluşturma ve küme bağımlılıklarını yapılandırma
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-create-listener.md)]

### <a name="configure-the-cluster-resources-in-powershell"></a>PowerShell 'de küme kaynaklarını yapılandırma
1. ILB için, daha önce oluşturulmuş ıLB 'nin IP adresini kullanmanız gerekir. PowerShell 'de bu IP adresini almak için aşağıdaki betiği kullanın:

        # Define variables
        $ServiceName="<MyServiceName>" # the name of the cloud service that contains the AG nodes
        (Get-AzureInternalLoadBalancer -ServiceName $ServiceName).IPAddress

2. VM 'lerden birinde, işletim sisteminizin PowerShell betiğini bir metin düzenleyicisine kopyalayın ve ardından değişkenleri daha önce not ettiğiniz değerlere ayarlayın.

    Windows Server 2012 veya üzeri için aşağıdaki betiği kullanın:

        # Define variables
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP address resource name
        $ILBIP = "<X.X.X.X>" # the IP address of the ILB

        Import-Module FailoverClusters

        Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}

    Windows Server 2008 R2 için aşağıdaki betiği kullanın:

        # Define variables
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP address resource name
        $ILBIP = "<X.X.X.X>" # the IP address of the ILB

        Import-Module FailoverClusters

        cluster res $IPResourceName /priv enabledhcp=0 address=$ILBIP probeport=59999  subnetmask=255.255.255.255

3. Değişkenleri ayarladıktan sonra, yükseltilmiş bir Windows PowerShell penceresi açın ve betiği çalıştırmak için metin düzenleyicisinden komut dosyasını PowerShell oturumunuza yapıştırın. İstem hala **>>** gösteriyorsa, betiğin çalışmaya başlamasını sağlamak Için yeniden ENTER tuşuna basın.

4. Her VM için önceki adımları tekrarlayın.  
    Bu betik, IP adresi kaynağını bulut hizmetinin IP adresiyle yapılandırır ve araştırma bağlantı noktası gibi diğer parametreleri ayarlar. IP adresi kaynağı çevrimiçi duruma getirildiğinde, daha önce oluşturduğunuz yük dengeli uç noktadan yoklama bağlantı noktasındaki yoklamaya yanıt verebilir.

## <a name="bring-the-listener-online"></a>Dinleyiciyi çevrimiçi duruma getirin
[!INCLUDE [Bring-Listener-Online](../../../../includes/virtual-machines-ag-listener-bring-online.md)]

## <a name="follow-up-items"></a>İzleme öğeleri
[!INCLUDE [Follow-up](../../../../includes/virtual-machines-ag-listener-follow-up.md)]

## <a name="test-the-availability-group-listener-within-the-same-virtual-network"></a>Kullanılabilirlik grubu dinleyicisini test etme (aynı sanal ağ içinde)
[!INCLUDE [Test-Listener-Within-VNET](../../../../includes/virtual-machines-ag-listener-test.md)]

## <a name="next-steps"></a>Sonraki adımlar
[!INCLUDE [Listener-Next-Steps](../../../../includes/virtual-machines-ag-listener-next-steps.md)]
