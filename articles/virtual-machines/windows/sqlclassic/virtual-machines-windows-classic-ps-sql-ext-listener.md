---
title: Kullanılabilirlik grupları için dış dinleyici yapılandırma
description: Bu öğretici, Azure 'da, ilişkili bulut hizmetinin ortak sanal IP adresi kullanılarak dışarıdan erişilebilen her zaman açık kullanılabilirlik grubu dinleyicisi oluşturma adımlarında size yol gösterir.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: a2453032-94ab-4775-b976-c74d24716728
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/31/2017
ms.author: mikeray
ms.custom: seo-lt-2019
ms.openlocfilehash: d2dce6875ec39810a81bb5ae454d953a7b7ab0a9
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74032714"
---
# <a name="configure-an-external-listener-for-availability-groups-on-azure-sql-server-vms"></a>Azure SQL Server VM 'lerinde kullanılabilirlik grupları için dış dinleyici yapılandırma
> [!div class="op_single_selector"]
> * [İç dinleyici](../classic/ps-sql-int-listener.md)
> * [Dış dinleyici](../classic/ps-sql-ext-listener.md)
> 
> 

Bu konu başlığı altında, internet üzerinden dışarıdan erişilebilen her zaman açık kullanılabilirlik grubu için bir dinleyicinin nasıl yapılandırılacağı gösterilmektedir. Bu, bulut hizmetinin **ortak sanal IP (VIP)** adresi ile dinleyiciyle ilişkilendirerek mümkün hale getirilir.

> [!IMPORTANT] 
> Azure 'da kaynak oluşturmak ve bunlarla çalışmak için iki farklı dağıtım modeli vardır: [Kaynak Yöneticisi ve klasik](../../../azure-resource-manager/resource-manager-deployment-model.md). Bu makalede, klasik dağıtım modelinin kullanımı ele alınmaktadır. Microsoft, yeni dağıtımların çoğunun Resource Manager modelini kullanmasını önerir.

Kullanılabilirlik grubunuz yalnızca şirket içi veya Azure 'da bulunan çoğaltmalar içerebilir veya hem şirket içi hem de karma yapılandırmalara yönelik olarak Azure 'a yayılabilir. Azure çoğaltmaları, birden fazla sanal ağ (VNet) kullanılarak aynı bölgede veya birden çok bölgede bulunabilir. Aşağıdaki adımlarda, zaten bir [kullanılabilirlik grubu yapılandırdığınız](../classic/portal-sql-alwayson-availability-groups.md) ancak bir dinleyici yapılandırmış olduğunuz varsayılır.

## <a name="guidelines-and-limitations-for-external-listeners"></a>Dış dinleyiciler için yönergeler ve sınırlamalar
Bulut hizmeti genel VIP adresini kullanarak dağıtım yaparken Azure 'daki kullanılabilirlik grubu dinleyicisine ilişkin aşağıdaki yönergelere göz önünde olabilirsiniz:

* Kullanılabilirlik grubu dinleyicisi Windows Server 2008 R2, Windows Server 2012 ve Windows Server 2012 R2 üzerinde desteklenir.
* İstemci uygulaması, kullanılabilirlik grubu VM 'lerinizi içeren olandan farklı bir bulut hizmetinde bulunmalıdır. Azure, aynı bulut hizmetindeki istemci ve sunucu ile doğrudan sunucu döndürmeyi desteklemez.
* Varsayılan olarak, bu makaledeki adımlarda, bulut hizmeti sanal IP (VIP) adresini kullanmak için bir dinleyicinin nasıl yapılandırılacağı gösterilmektedir. Ancak, bulut hizmetiniz için birden çok VIP adresi ayırmak ve oluşturmak mümkündür. Bu, her biri farklı bir VIP ile ilişkili birden fazla dinleyici oluşturmak için bu makaledeki adımları kullanmanıza olanak sağlar. Birden çok VIP adresi oluşturma hakkında daha fazla bilgi için bkz. [bulut hizmeti başına birden çok VIP](../../../load-balancer/load-balancer-multivip.md).
* Karma ortam için bir dinleyici oluşturuyorsanız, şirket içi ağın, Azure sanal ağı ile siteden siteye VPN 'ye ek olarak genel Internet bağlantısı olması gerekir. Azure alt ağında, kullanılabilirlik grubu dinleyicisine yalnızca ilgili bulut hizmetinin genel IP adresi ile erişilebilir.
* Aynı bulut hizmetinde, Iç Load Balancer (ıLB) kullanan bir iç dinleyicinizin bulunduğu bir dış dinleyici oluşturmak desteklenmez.

## <a name="determine-the-accessibility-of-the-listener"></a>Dinleyicinin erişilebilirliğini belirleme
[!INCLUDE [ag-listener-accessibility](../../../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

Bu makalede, **dış yük dengelemeyi**kullanan bir dinleyici oluşturma konusu ele alınmaktadır. Sanal ağınıza özel bir dinleyici istiyorsanız, [ILB ile bir dinleyici](../classic/ps-sql-int-listener.md) ayarlamaya yönelik adımları sağlayan bu makalenin sürümüne bakın.

## <a name="create-load-balanced-vm-endpoints-with-direct-server-return"></a>Doğrudan sunucu dönüşü ile yük dengeli VM uç noktaları oluşturma
Dış Yük Dengeleme, sanal makinelerinizi barındıran bulut hizmetinin ortak sanal IP adresini kullanır. Bu nedenle, bu durumda yük dengeleyici oluşturmanız veya yapılandırmanız gerekmez.

Azure çoğaltmasını barındıran her VM için yük dengeli bir uç nokta oluşturmanız gerekir. Birden çok bölgede çoğaltmalar varsa, söz konusu bölgeye yönelik her çoğaltma aynı VNet 'te aynı bulut hizmetinde olmalıdır. Birden çok Azure bölgesini kapsayan kullanılabilirlik grubu çoğaltmaları oluşturmak için birden çok VNET yapılandırması gerekir. Çapraz VNet bağlantısını yapılandırma hakkında daha fazla bilgi için bkz. [VNET 'Ten VNET 'e bağlantı yapılandırma](../../../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

1. Azure portal, bir çoğaltma barındıran her VM 'ye gidin ve ayrıntıları görüntüleyin.
2. VM 'lerin her biri için **uç noktalar** sekmesine tıklayın.
3. Kullanmak istediğiniz dinleyici uç noktasının **adının** ve **genel bağlantı noktasının** zaten kullanımda olmadığından emin olun. Aşağıdaki örnekte, ad "MyEndpoint" ve bağlantı noktası "1433".
4. Yerel istemcinizdeki [en son PowerShell modülünü](https://azure.microsoft.com/downloads/)indirip yükleyin.
5. **Azure PowerShell**başlatın. Yüklenen Azure yönetim modülleri ile yeni bir PowerShell oturumu açıldı.
6. **Get-Azuikinci dosya SettingsFile**komutunu çalıştırın. Bu cmdlet, bir yayımlama ayarları dosyasını yerel bir dizine indirmek için sizi bir tarayıcıya yönlendirir. Azure aboneliğiniz için oturum açma kimlik bilgileriniz istenebilir.
7. İndirdiğiniz yayımlama ayarları dosyasının yoluyla **Import-Azuikinci dosya SettingsFile** komutunu çalıştırın:
   
        Import-AzurePublishSettingsFile -PublishSettingsFile <PublishSettingsFilePath>
   
    Yayımlama ayarları dosyası içeri aktarıldıktan sonra, PowerShell oturumunda Azure aboneliğinizi yönetebilirsiniz.
    
1. Aşağıdaki PowerShell betiğini bir metin düzenleyicisine kopyalayın ve değişken değerlerini ortamınıza uyacak şekilde ayarlayın (bazı parametreler için varsayılanlar verilmiştir). Kullanılabilirlik grubunuz Azure bölgelerini yaymışsa, bu veri merkezinde bulunan bulut hizmeti ve düğümlerin her bir veri merkezinde betiği bir kez çalıştırmanız gerektiğini unutmayın.
   
        # Define variables
        $ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
        $AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas
   
        # Configure a load balanced endpoint for each node in $AGNodes, with direct server return enabled
        ForEach ($node in $AGNodes)
        {
            Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -Protocol "TCP" -PublicPort 1433 -LocalPort 1433 -LBSetName "ListenerEndpointLB" -ProbePort 59999 -ProbeProtocol "TCP" -DirectServerReturn $true | Update-AzureVM
        }

2. Değişkenleri ayarladıktan sonra dosyayı çalıştırmak için metin düzenleyicisinden betiği Azure PowerShell oturumunuza kopyalayın. İstem hala > > gösteriyorsa, betiğin çalışmaya başladığı emin olmak için yeniden gırın yazın.

## <a name="verify-that-kb2854082-is-installed-if-necessary"></a>Gerekirse KB2854082 yüklendiğini doğrulayın
[!INCLUDE [kb2854082](../../../../includes/virtual-machines-ag-listener-kb2854082.md)]

## <a name="open-the-firewall-ports-in-availability-group-nodes"></a>Kullanılabilirlik grubu düğümlerinde güvenlik duvarı bağlantı noktalarını açma
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-open-firewall.md)]

## <a name="create-the-availability-group-listener"></a>Kullanılabilirlik grubu dinleyicisini oluşturma

Kullanılabilirlik grubu dinleyicisini iki adımda oluşturun. İlk olarak, istemci erişim noktası kümesi kaynağını oluşturun ve bağımlılıkları yapılandırın. İkincisi, küme kaynaklarını PowerShell ile yapılandırın.

### <a name="create-the-client-access-point-and-configure-the-cluster-dependencies"></a>İstemci erişim noktasını oluşturma ve küme bağımlılıklarını yapılandırma
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-create-listener.md)]

### <a name="configure-the-cluster-resources-in-powershell"></a>PowerShell 'de küme kaynaklarını yapılandırma
1. Dış yük dengeleme için, çoğaltmalarınızı içeren bulut hizmetinin genel sanal IP adresini edinmeniz gerekir. Azure portalında oturum açın. Kullanılabilirlik grubu VM 'nizi içeren bulut hizmetine gidin. **Pano** görünümünü açın.
2. **Genel sanal IP (VIP) adresi**altında gösterilen adresi aklınızda edin. Çözümünüz VNET 'leri yaymışsa, bir çoğaltma barındıran VM içeren her bir bulut hizmeti için bu adımı tekrarlayın.
3. VM 'lerden birinde, aşağıdaki PowerShell betiğini bir metin düzenleyicisine kopyalayın ve değişkenleri daha önce not ettiğiniz değerlere ayarlayın.
   
        # Define variables
        $ClusterNetworkName = "<ClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $CloudServiceIP = "<X.X.X.X>" # Public Virtual IP (VIP) address of your cloud service
   
        Import-Module FailoverClusters
   
        # If you are using Windows Server 2012 or higher, use the Get-Cluster Resource command. If you are using Windows Server 2008 R2, use the cluster res command. Both commands are commented out. Choose the one applicable to your environment and remove the # at the beginning of the line to convert the comment to an executable line of code.
   
        # Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$CloudServiceIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"OverrideAddressMatch"=1;"EnableDhcp"=0}
        # cluster res $IPResourceName /priv enabledhcp=0 overrideaddressmatch=1 address=$CloudServiceIP probeport=59999  subnetmask=255.255.255.255
4. Değişkenleri ayarladıktan sonra yükseltilmiş bir Windows PowerShell penceresi açın, sonra betiği metin düzenleyicisinden kopyalayın ve çalıştırmak için Azure PowerShell oturumunuza yapıştırın. İstem hala > > gösteriyorsa, betiğin çalışmaya başladığı emin olmak için yeniden gırın yazın.
5. Her VM 'de bunu tekrarlayın. Bu betik, IP adresi kaynağını bulut hizmetinin IP adresiyle yapılandırır ve araştırma bağlantı noktası gibi diğer parametreleri ayarlar. IP adresi kaynağı çevrimiçi duruma getirildiğinde, bu öğreticide daha önce oluşturulan yük dengeli uç noktadan yoklama bağlantı noktasındaki yoklamaya yanıt verebilir.

## <a name="bring-the-listener-online"></a>Dinleyiciyi çevrimiçi duruma getirin
[!INCLUDE [Bring-Listener-Online](../../../../includes/virtual-machines-ag-listener-bring-online.md)]

## <a name="follow-up-items"></a>İzleme öğeleri
[!INCLUDE [Follow-up](../../../../includes/virtual-machines-ag-listener-follow-up.md)]

## <a name="test-the-availability-group-listener-within-the-same-vnet"></a>Kullanılabilirlik grubu dinleyicisini test etme (aynı VNet içinde)
[!INCLUDE [Test-Listener-Within-VNET](../../../../includes/virtual-machines-ag-listener-test.md)]

## <a name="test-the-availability-group-listener-over-the-internet"></a>Kullanılabilirlik grubu dinleyicisini test etme (internet üzerinden)
Sanal Ağ dışından dinleyiciye erişebilmek için, yalnızca aynı VNet 'te erişilebilen ıLB yerine dış/genel yük dengelemeyi (Bu konuda açıklanan) kullanmanız gerekir. Bağlantı dizesinde, bulut hizmeti adını belirtirsiniz. Örneğin, *mycloudservice*adlı bir bulut hizmetiniz varsa, sqlcmd deyimleri şu şekilde olur:

    sqlcmd -S "mycloudservice.cloudapp.net,<EndpointPort>" -d "<DatabaseName>" -U "<LoginId>" -P "<Password>"  -Q "select @@servername, db_name()" -l 15

Önceki örneğin aksine SQL kimlik doğrulamasının kullanılması gerekir, çünkü çağıran Windows kimlik doğrulamasını Internet üzerinden kullanamaz. Daha fazla bilgi için bkz. [Azure VM 'de Always on kullanılabilirlik grubu: Istemci bağlantı senaryoları](https://blogs.msdn.com/b/sqlcat/archive/2014/02/03/alwayson-availability-group-in-windows-azure-vm-client-connectivity-scenarios.aspx). SQL kimlik doğrulaması kullanırken, her iki çoğaltmalarda de aynı oturum açmayı seçtiğinizden emin olun. Kullanılabilirlik gruplarıyla oturum açma sorunlarını giderme hakkında daha fazla bilgi için bkz. [oturum açma bilgilerini eşleme veya diğer yinelemelere bağlanmak ve kullanılabilirlik veritabanlarına eşlemek için kapsanan SQL veritabanı kullanıcısını kullanma](https://blogs.msdn.com/b/alwaysonpro/archive/2014/02/19/how-to-map-logins-or-use-contained-sql-database-user-to-connect-to-other-replicas-and-map-to-availability-databases.aspx).

Her zaman açık çoğaltmalar farklı alt ağlarda ise, istemcilerin bağlantı dizesinde **MultiSubnetFailover = true** belirtmesi gerekir. Bu durum, farklı alt ağlardaki çoğaltmalar için paralel bağlantı denemelerinde oluşur. Bu senaryonun bölgeler arası her zaman açık kullanılabilirlik grubu dağıtımı içerdiğini unutmayın.

## <a name="next-steps"></a>Sonraki adımlar
[!INCLUDE [Listener-Next-Steps](../../../../includes/virtual-machines-ag-listener-next-steps.md)]

