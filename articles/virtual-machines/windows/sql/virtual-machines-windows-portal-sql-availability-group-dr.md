---
title: Kullanılabilirlik grubunu farklı bölgelerde yapılandırma
description: Bu makalede, Azure sanal makinelerinde farklı bir bölgedeki bir çoğaltmayla SQL Server kullanılabilirlik grubunun nasıl yapılandırılacağı açıklanmaktadır.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 388c464e-a16e-4c9d-a0d5-bb7cf5974689
ms.service: virtual-machines-sql
ms.custom: seo-lt-2019
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/02/2017
ms.author: mikeray
ms.openlocfilehash: 96b7c3cf59f947d1476ad840ae81695356d869b6
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74037547"
---
# <a name="configure-an-availability-group-on-azure-sql-server-virtual-machines-in-different-regions"></a>Farklı bölgelerde Azure SQL Server sanal makinelerde bir kullanılabilirlik grubu yapılandırma

Bu makalede, uzak bir Azure konumundaki Azure sanal makinelerinde SQL Server Always on kullanılabilirlik grubu çoğaltmasının nasıl yapılandırılacağı açıklanmaktadır. Olağanüstü durum kurtarmayı desteklemek için bu yapılandırmayı kullanın.

Bu makale Kaynak Yöneticisi modundaki Azure sanal makineleri için geçerlidir.

Aşağıdaki görüntüde, Azure sanal makinelerinde bir kullanılabilirlik grubunun ortak dağıtımı gösterilmektedir:

   ![Kullanılabilirlik Grubu](./media/virtual-machines-windows-portal-sql-availability-group-dr/00-availability-group-basic.png)

Bu dağıtımda, tüm sanal makineler tek bir Azure bölgesidir. Kullanılabilirlik grubu çoğaltmalarının, SQL-1 ve SQL-2 ' de otomatik yük devretme ile zaman uyumlu yürütmesi olabilir. Bu mimariyi derlemek için bkz. [kullanılabilirlik grubu şablonu veya öğreticisi](virtual-machines-windows-portal-sql-availability-group-overview.md).

Bu mimari, Azure bölgesinin erişilemez hale gelmesi durumunda kapalı kalma süresine karşı savunmasız kalır. Bu güvenlik açığını aşmak için, farklı bir Azure bölgesine bir çoğaltma ekleyin. Aşağıdaki diyagramda yeni mimarinin nasıl görüneceğine gösterilmektedir:

   ![Kullanılabilirlik grubu DR](./media/virtual-machines-windows-portal-sql-availability-group-dr/00-availability-group-basic-dr.png)

Yukarıdaki diyagramda, SQL-3 adlı yeni bir sanal makine gösterilmektedir. SQL-3 farklı bir Azure bölgesidir. SQL-3, Windows Server yük devretme kümesine eklenir. SQL-3, bir kullanılabilirlik grubu çoğaltmasını barındırabilir. Son olarak, SQL-3 için Azure bölgesinin yeni bir Azure yük dengeleyiciye sahip olduğuna dikkat edin.

>[!NOTE]
> Aynı bölgede birden fazla sanal makine olduğunda bir Azure kullanılabilirlik kümesi gereklidir. Bölgede yalnızca bir sanal makine varsa, kullanılabilirlik kümesi gerekli değildir. Bir sanal makineyi bir kullanılabilirlik kümesine, oluşturma zamanında yerleştirebilirsiniz. Sanal makine zaten bir kullanılabilirlik kümesinde ise, daha sonra ek bir çoğaltma için bir sanal makine ekleyebilirsiniz.

Bu mimaride, uzak bölgedeki Çoğaltma normalde zaman uyumsuz tamamlama kullanılabilirliği moduyla ve el ile yük devretme moduyla yapılandırılır.

Kullanılabilirlik grubu çoğaltmaları farklı Azure bölgelerindeki Azure sanal makinelerdeyse her bölge şunları gerektirir:

* Bir sanal ağ geçidi
* Bir sanal ağ geçidi bağlantısı

Aşağıdaki diyagramda, ağların veri merkezleri arasında nasıl iletişim kurduğu gösterilmektedir.

   ![Kullanılabilirlik Grubu](./media/virtual-machines-windows-portal-sql-availability-group-dr/01-vpngateway-example.png)

>[!IMPORTANT]
>Bu mimari, Azure bölgeleri arasında çoğaltılan veriler için giden veri ücretleri doğurur. Bkz. [bant genişliği fiyatlandırması](https://azure.microsoft.com/pricing/details/bandwidth/).  

## <a name="create-remote-replica"></a>Uzak çoğaltma oluştur

Uzak bir veri merkezinde çoğaltma oluşturmak için aşağıdaki adımları uygulayın:

1. [Yeni bölgede bir sanal ağ oluşturun](../../../virtual-network/manage-virtual-network.md#create-a-virtual-network).

1. [Azure Portal kullanarak VNET-VNet bağlantısı yapılandırın](../../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md).

   >[!NOTE]
   >Bazı durumlarda, sanal ağdan sanal ağa bağlantı oluşturmak için PowerShell kullanmanız gerekebilir. Örneğin, farklı Azure hesapları kullanıyorsanız, portalda bağlantıyı yapılandıramazsınız. Bu durumda bkz. [Azure Portal kullanarak VNET-VNet bağlantısı yapılandırma](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).

1. [Yeni bölgede bir etki alanı denetleyicisi oluşturun](../../../active-directory/active-directory-new-forest-virtual-machine.md).

   Birincil sitedeki etki alanı denetleyicisi kullanılamıyorsa, bu etki alanı denetleyicisi kimlik doğrulaması sağlar.

1. [Yeni bölgede SQL Server sanal makine oluşturun](virtual-machines-windows-portal-sql-server-provision.md).

1. [Yeni bölgedeki ağda bir Azure yük dengeleyici oluşturun](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer).

   Bu yük dengeleyici şunları içermelidir:

   - Yeni sanal makine ile aynı ağ ve alt ağ içinde olmalıdır.
   - Kullanılabilirlik grubu dinleyicisi için bir statik IP adresi vardır.
   - Yük dengeleyiciyle aynı bölgedeki yalnızca sanal makinelerden oluşan bir arka uç havuzu ekleyin.
   - IP adresine özgü bir TCP bağlantı noktası araştırması kullanın.
   - Aynı bölgedeki SQL Server özel bir yük dengeleme kuralına sahip olmalıdır.  
   - Arka uç havuzundaki sanal makineler tek bir kullanılabilirlik kümesinin ya da sanal makine ölçek kümesinin bir parçası değilse, bir Standart Load Balancer olun. Daha fazla bilgi için [Azure Load Balancer standart genel bakış ' a](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)bakın.

1. [Yeni SQL Server Yük Devretme Kümelemesi özelliği ekleyin](virtual-machines-windows-portal-sql-availability-group-prereq.md#add-failover-clustering-features-to-both-sql-server-vms).

1. [Yeni SQL Server etki alanına ekleyin](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain).

1. [Yeni SQL Server hizmet hesabını bir etki alanı hesabı kullanacak şekilde ayarlayın](virtual-machines-windows-portal-sql-availability-group-prereq.md#setServiceAccount).

1. [Yeni SQL Server Windows Server yük devretme kümesine ekleyin](virtual-machines-windows-portal-sql-availability-group-tutorial.md#addNode).

1. Kümede bir IP adresi kaynağı oluşturun.

   IP adresi kaynağını Yük Devretme Kümesi Yöneticisi ' de oluşturabilirsiniz. Kullanılabilirlik grubu rolüne sağ tıklayın, **Kaynak Ekle**, **daha fazla kaynak**ve **IP adresi**' ne tıklayın.

   ![IP adresi oluştur](./media/virtual-machines-windows-portal-sql-availability-group-dr/20-add-ip-resource.png)

   Bu IP adresini şu şekilde yapılandırın:

   - Uzak veri merkezinden ağı kullanın.
   - IP adresini yeni Azure Yük dengeleyicisinden atayın. 

1. SQL Server Yapılandırma Yöneticisi yeni SQL Server, [Always on kullanılabilirlik grupları 'nı etkinleştirin](https://msdn.microsoft.com/library/ff878259.aspx).

1. [Yeni SQL Server güvenlik duvarı bağlantı noktalarını açın](virtual-machines-windows-portal-sql-availability-group-prereq.md#endpoint-firewall).

   Açmanız gereken bağlantı noktası numaraları ortamınıza bağlıdır. Yansıtma uç noktası ve Azure Yük Dengeleyici Sistem durumu araştırması için bağlantı noktalarını açın.

1. [Yeni SQL Server kullanılabilirlik grubuna bir çoğaltma ekleyin](https://msdn.microsoft.com/library/hh213239.aspx).

   Uzak bir Azure bölgesindeki bir çoğaltma için el ile yük devretme ile zaman uyumsuz çoğaltma için ayarlayın.  

1. IP adresi kaynağını, dinleyici istemci erişim noktası (ağ adı) kümesine yönelik bir bağımlılık olarak ekleyin.

   Aşağıdaki ekran görüntüsünde düzgün yapılandırılmış bir IP adresi küme kaynağı gösterilmektedir:

   ![Kullanılabilirlik Grubu](./media/virtual-machines-windows-portal-sql-availability-group-dr/50-configure-dependency-multiple-ip.png)

   >[!IMPORTANT]
   >Küme kaynak grubu her iki IP adresini de içerir. Her iki IP adresi de dinleyici istemci erişim noktası için bağımlılıklardır. Küme bağımlılığı yapılandırmasında **or** işlecini kullanın.

1. [PowerShell 'de küme parametrelerini ayarlayın](virtual-machines-windows-portal-sql-availability-group-tutorial.md#setparam).

PowerShell betiğini, yeni bölgedeki yük dengeleyicide yapılandırdığınız küme ağ adı, IP adresi ve araştırma bağlantı noktasıyla çalıştırın.

   ```powershell
   $ClusterNetworkName = "<MyClusterNetworkName>" # The cluster name for the network in the new region (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name).
   $IPResourceName = "<IPResourceName>" # The cluster name for the new IP Address resource.
   $ILBIP = "<n.n.n.n>" # The IP Address of the Internal Load Balancer (ILB) in the new region. This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ProbePort = <nnnnn> # The probe port you set on the ILB.

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

## <a name="set-connection-for-multiple-subnets"></a>Birden çok alt ağ için bağlantı ayarlama

Uzak veri merkezindeki çoğaltma, kullanılabilirlik grubunun bir parçasıdır ancak farklı bir alt ağda yer alabilir. Bu çoğaltma birincil çoğaltma olursa, uygulama bağlantısı zaman aşımları meydana gelebilir. Bu davranış, çok alt ağ dağıtımında şirket içi kullanılabilirlik grubuyla aynıdır. İstemci uygulamalarından gelen bağlantılara izin vermek için, istemci bağlantısını güncelleştirin ya da küme ağ adı kaynağında ad çözümlemesi önbelleğe alma 'yı yapılandırın.

Tercihen, `MultiSubnetFailover=Yes`ayarlamak için istemci bağlantı dizelerini güncelleştirin. Bkz. [MultiSubnetFailover Ile bağlanma](https://msdn.microsoft.com/library/gg471494#Anchor_0).

Bağlantı dizelerini değiştiremeyeceğiniz takdirde ad çözümlemesi önbelleği yapılandırabilirsiniz. Bkz. [zaman aşımı hatası ve çok alt ağ ortamında SQL Server 2012 AlwaysOn kullanılabilirlik grubu dinleyicisine bağlanamazsınız](https://support.microsoft.com/help/2792139/time-out-error-and-you-cannot-connect-to-a-sql-server-2012-alwayson-av).

## <a name="fail-over-to-remote-region"></a>Uzak bölgeye yük devretme

Uzak bölgeye dinleyici bağlantısını test etmek için çoğaltmayı uzak bölgeye devreder. Çoğaltma zaman uyumsuz olsa da, yük devretme olası veri kaybına karşı savunmasız olur. Veri kaybı olmadan yük devretmek için kullanılabilirlik modunu eşzamanlı olarak değiştirin ve yük devretme modunu otomatik olarak ayarlayın. Aşağıdaki adımları kullanın:

1. **Nesne Gezgini**' de, birincil çoğaltmayı barındıran SQL Server örneğine bağlanın.
1. **AlwaysOn kullanılabilirlik grupları**, **kullanılabilirlik grupları**' nın altında, kullanılabilirlik grubunuza sağ tıklayın ve **Özellikler**' e tıklayın.
1. **Genel** sayfasında, **kullanılabilirlik ÇOĞALTMALARı**altında, Dr sitesindeki Ikincil çoğaltmayı, **zaman uyumlu tamamlama** kullanılabilirlik modunu ve **Otomatik** yük devretme modunu kullanacak şekilde ayarlayın.
1. Yüksek kullanılabilirlik için birincil çoğaltmayla aynı sitede ikincil bir çoğaltmeniz varsa, bu çoğaltmayı **zaman uyumsuz işlemeye** ve **el ile**ayarlayın.
1. Tamam'a tıklayın.
1. **Nesne Gezgini**, kullanılabilirlik grubuna sağ tıklayın ve **panoyu göster**' e tıklayın.
1. Panoda, DR sitesindeki çoğaltmanın eşitlendiğinden emin olun.
1. **Nesne Gezgini**, kullanılabilirlik grubuna sağ tıklayın ve **Yük devretme...** öğesine tıklayın. SQL Server Management Studios, SQL Server yük devretmek için bir sihirbaz açar.  
1. **İleri**' ye tıklayın ve DR sitesindeki SQL Server örneğini seçin. **İleri** ' ye tıklayın.
1. DR sitesindeki SQL Server örneğine bağlanın ve **İleri**' ye tıklayın.
1. **Özet** sayfasında, ayarları doğrulayın ve **son**' a tıklayın.

Bağlantıyı test ettikten sonra birincil çoğaltmayı birincil veri merkezinize geri taşıyın ve kullanılabilirlik modunu normal işletim ayarlarına geri doğru ayarlayın. Aşağıdaki tabloda, bu belgede açıklanan mimarinin normal işletimsel ayarları gösterilmektedir:

| Konum | Sunucu örneği | Rol | Kullanılabilirlik modu | Yük devretme modu
| ----- | ----- | ----- | ----- | -----
| Birincil veri merkezi | SQL-1 | Birincil | K | Otomatik
| Birincil veri merkezi | SQL-2 | İkincil | K | Otomatik
| İkincil veya uzak veri merkezi | SQL-3 | İkincil | En | El ile


### <a name="more-information-about-planned-and-forced-manual-failover"></a>Planlı ve zorlamalı el ile yük devretme hakkında daha fazla bilgi

Daha fazla bilgi edinmek için aşağıdaki kaynaklara bakın:

- [Kullanılabilirlik grubunun planlı bir el Ile yük devretmesini gerçekleştirme (SQL Server)](https://msdn.microsoft.com/library/hh231018.aspx)
- [Kullanılabilirlik grubunun zorla el Ile yük devretmesini gerçekleştirme (SQL Server)](https://msdn.microsoft.com/library/ff877957.aspx)

## <a name="additional-links"></a>Ek bağlantılar

* [Always on kullanılabilirlik grupları](https://msdn.microsoft.com/library/hh510230.aspx)
* [Azure Sanal Makineler](https://docs.microsoft.com/azure/virtual-machines/windows/)
* [Azure yük dengeleyiciler](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer)
* [Azure kullanılabilirlik kümeleri](../manage-availability.md)
