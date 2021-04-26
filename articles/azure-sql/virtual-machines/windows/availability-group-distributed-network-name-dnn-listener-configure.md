---
title: Kullanılabilirlik grubu için DNN dinleyicisini yapılandırma
description: Sanal ağ adı (VNN) dinleyicinizi değiştirmek için bir dağıtılmış ağ adı (DNN) dinleyicisi yapılandırmayı ve Azure VM 'de SQL Server trafiği her zaman açık kullanılabilirlik grubunuzun yolunu öğrenin.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/07/2020
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: bf5c4c39ea8f5705cc9788fdcf2cddd01dcb4087
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105034716"
---
# <a name="configure-a-dnn-listener-for-an-availability-group"></a>Kullanılabilirlik grubu için DNN dinleyicisi yapılandırma
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Azure VM 'lerinde SQL Server, dağıtılmış ağ adı (DNN), trafiği uygun kümelenmiş kaynağa yönlendirir. Bir Azure Load Balancer gerek olmadan sanal ağ adı (VNN) dinleyicisinden her zaman açık kullanılabilirlik grubuna (AG) bağlanmak için daha kolay bir yol sağlar.

Bu makalede, yüksek kullanılabilirlik ve olağanüstü durum kurtarma (HADR) için Azure VM 'lerinde sanal nn dinleyicisini değiştirmek ve trafiği kullanılabilirlik SQL Server grubunuza yönlendirmek üzere bir DNN dinleyicisi yapılandırmanız öğretilir.

DNN dinleyicisi özelliği şu anda yalnızca Windows Server 2016 ve sonraki sürümlerde SQL Server 2019 CU8 ile başlayarak kullanılabilir.

Alternatif bir bağlantı seçeneği için, bir [vnn dinleyicisi ve Azure Load Balancer](availability-group-vnn-azure-load-balancer-configure.md) kullanın.

## <a name="overview"></a>Genel Bakış

Dağıtılmış ağ adı (DNN) dinleyicisi, [SQL Server VM 'Lerde Always on kullanılabilirlik grupları](availability-group-overview.md)ile birlikte kullanıldığında geleneksel sanal ağ adı (vnn) kullanılabilirlik grubu dinleyicisinin yerini alır. Bu, trafiği yönlendirmek, dağıtım, bakım ve yük devretmeyi iyileştirmek için bir Azure Load Balancer gereksinimini geçersiz kılar.

Mevcut bir VNN dinleyicisini değiştirmek için DNN dinleyicisini kullanın veya alternatif olarak, mevcut bir VNN dinleyicisi ile birlikte kullanın, böylece kullanılabilirlik grubunuzun iki ayrı bağlantı noktası olması gerekir. bu sayede, bir tane, VNN dinleyicisi adını (ve varsayılan olmayan bağlantı noktasını) ve bir tane DNN dinleyici adını ve bağlantı noktasını kullanarak bir tane.

> [!CAUTION]
> DNN kullandığınızda yönlendirme davranışı, VNN kullanılırken farklılık gösterir. 1433 numaralı bağlantı noktasını kullanmayın. Daha fazla bilgi edinmek için bu makalenin ilerleyen kısımlarında yer almadaki [bağlantı noktası değerlendirmesi](#port-considerations) bölümüne bakın.

## <a name="prerequisites"></a>Önkoşullar

Bu makaledeki adımları tamamlamadan önce, zaten şunları yapmalısınız:

- CU8 veya sonraki sürümlerde 2019 SQL Server, Windows Server 2016 ve üzeri sürümlerde
- Dağıtılmış ağ adının [HADR çözümünüz için uygun bağlantı seçeneği](hadr-cluster-best-practices.md#connectivity)olduğuna karar verilir.
- [Always on kullanılabilirlik grubunuz](availability-group-overview.md)yapılandırıldı. 
- En son [PowerShell](/powershell/azure/install-az-ps)sürümü yüklendi. 
- DNN dinleyicisi için kullanacağınız benzersiz bağlantı noktası tanımlandı. DNN dinleyicisi için kullanılan bağlantı noktası, kullanılabilirlik grubunun veya yük devretme kümesi örneğinin tüm çoğaltmalarının tamamında benzersiz olmalıdır.  Aynı bağlantı noktasını başka bir bağlantı paylaşamaz.



## <a name="create-script"></a>Betik oluştur

Dağıtılmış ağ adı (DNN) kaynağını oluşturmak ve kullanılabilirlik grubunuz ile ilişkilendirmek için PowerShell 'i kullanın.

Bunu yapmak için aşağıdaki adımları izleyin:

1. Not Defteri gibi bir metin düzenleyici açın.
1. Aşağıdaki betiği kopyalayıp yapıştırın:

   ```powershell
   param (
      [Parameter(Mandatory=$true)][string]$Ag,
      [Parameter(Mandatory=$true)][string]$Dns,
      [Parameter(Mandatory=$true)][string]$Port
   )
   
   Write-Host "Add a DNN listener for availability group $Ag with DNS name $Dns and port $Port"
   
   $ErrorActionPreference = "Stop"
   
   # create the DNN resource with the port as the resource name
   Add-ClusterResource -Name $Port -ResourceType "Distributed Network Name" -Group $Ag 
   
   # set the DNS name of the DNN resource
   Get-ClusterResource -Name $Port | Set-ClusterParameter -Name DnsName -Value $Dns 
   
   # start the DNN resource
   Start-ClusterResource -Name $Port
   
   
   $Dep = Get-ClusterResourceDependency -Resource $Ag
   if ( $Dep.DependencyExpression -match '\s*\((.*)\)\s*' )
   {
   $DepStr = "$($Matches.1) or [$Port]"
   }
   else
   {
   $DepStr = "[$Port]"
   }
   
   Write-Host "$DepStr"
   
   # add the Dependency from availability group resource to the DNN resource
   Set-ClusterResourceDependency -Resource $Ag -Dependency "$DepStr"
   
   
   #bounce the AG resource
   Stop-ClusterResource -Name $Ag
   Start-ClusterResource -Name $Ag
   ```

1. Betiği, gibi bir dosya olarak kaydedin `.ps1` `add_dnn_listener.ps1` .

## <a name="execute-script"></a>Betiği Yürüt

DNN dinleyicisini oluşturmak için, kullanılabilirlik grubu, dinleyici adı ve bağlantı noktası adı için parametreleri geçirme betiği yürütün.

Örneğin, bir kullanılabilirlik grubu adı `ag1` , dinleyici adı `dnnlsnr` ve dinleyici bağlantı noktası olarak kabul edildiğinde `6789` , şu adımları izleyin:

1. Komut istemi veya PowerShell gibi bir komut satırı arabirim aracı açın.
1. Betiği kaydettiğiniz yere gidin `.ps1` , örneğin c:\Documents.
1. Betiği yürütün: ```add_dnn_listener.ps1 <ag name> <listener-name> <listener port>``` . Örnek:

   ```console
   c:\Documents> add_dnn_listener.ps1 ag1 dnnlsnr 6789
   ```

## <a name="verify-listener"></a>Dinleyiciyi doğrula

DNN dinleyicinizin başarıyla oluşturulduğunu doğrulamak için SQL Server Management Studio ya da Transact-SQL kullanın.

### <a name="sql-server-management-studio"></a>SQL Server Management Studio

DNN dinleyicinizi görüntülemek için [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) Içindeki **kullanılabilirlik grubu dinleyicilerini** genişletin:

:::image type="content" source="media/availability-group-distributed-network-name-dnn-listener-configure/dnn-listener-in-ssms.png" alt-text="SQL Server Management Studio (SSMS) içindeki kullanılabilirlik grubu dinleyicileri altında DNN dinleyicisini görüntüleme":::

### <a name="transact-sql"></a>Transact-SQL

DNN dinleyicisinin durumunu görüntülemek için Transact-SQL ' i kullanın:

```sql
SELECT * FROM SYS.AVAILABILITY_GROUP_LISTENERS
```

İçin değeri, `1` `is_distributed_network_name` dinleyicinin bir dağıtılmış ağ adı (DNN) dinleyicisi olduğunu gösterir:

:::image type="content" source="media/availability-group-distributed-network-name-dnn-listener-configure/dnn-listener-tsql.png" alt-text="Değeri 1 olan DNN dinleyicilerini belirlemek için sys.availability_group_listeners kullanın is_distributed_network_name":::

## <a name="update-connection-string"></a>Bağlantı dizesini Güncelleştir

Uygulamalar için bağlantı dizelerini DNN dinleyicisine bağlanacak şekilde güncelleştirin. DNN dinleyicilerinin bağlantı dizeleri DNN bağlantı noktası numarasını sağlamalıdır. Yük devretme sonrasında hızlı bağlantı sağlamak için, `MultiSubnetFailover=True` SQL istemcisi destekliyorsa bağlantı dizesine ekleyin.

## <a name="test-failover"></a>Yük devretme testi

İşlevselliği sağlamak için kullanılabilirlik grubunun yük devretmesini test edin.

Yük devretmeyi sınamak için aşağıdaki adımları izleyin:

1. DNN dinleyicisine veya [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms)kullanarak çoğaltmalardan birine bağlanın.
1. **Nesne Gezgini**'de **Always on kullanılabilirlik grubu** ' nu genişletin.
1. Kullanılabilirlik grubuna sağ tıklayın ve yük devretme **Sihirbazı**'nı açmak Için **Yük devretme** ' yı seçin.
1. Bir yük devretme hedefi seçmek ve kullanılabilirlik grubunu ikincil bir çoğaltmaya devretmek için istemleri izleyin.
1. Veritabanının yeni birincil çoğaltmadaki eşitlenmiş durumda olduğunu onaylayın.
1. Seçim Özgün birincil veya başka bir ikincil çoğaltma için yeniden başarısız oldu.

## <a name="test-connectivity"></a>Bağlantıyı test etme

DNN dinleyicinizin bağlantısını şu adımlarla test edin:

1. [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)açın.
1. DNN dinleyiciye bağlanın.
1. Yeni bir sorgu penceresi açın ve çalıştırarak hangi çoğaltmanın bağlı olduğunu denetleyin `SELECT @@SERVERNAME` .
1. Kullanılabilirlik grubunu başka bir çoğaltmaya devreder.
1. Makul bir süre sonra, `SELECT @@SERVERNAME` kullanılabilirlik grubunuzun artık başka bir çoğaltmada barındırıldığından emin olmak için çalıştırın.

## <a name="limitations"></a>Sınırlamalar

- Şu anda, bir kullanılabilirlik grubu için DNN dinleyicisi yalnızca Windows Server 2016 ve sonrasında SQL Server 2019 CU8 ve üzeri sürümlerde desteklenir. 
- DNN dinleyicilerinin benzersiz bir bağlantı noktasıyla yapılandırılması **gerekir** .  Bağlantı noktası herhangi bir çoğaltmada diğer hiçbir bağlantıyla paylaşılamaz.
- Diğer SQL Server özellikleriyle ve DNN ile bir kullanılabilirlik grubuyla çalışırken ek hususlar olabilir. Daha fazla bilgi için bkz. [DNN birlikte çalışabilirliği Ile AG](availability-group-dnn-interoperability.md). 

## <a name="port-considerations"></a>Bağlantı noktası konuları

DNN dinleyicileri, tüm IP adreslerini dinlemek üzere tasarlanmıştır, ancak belirli, benzersiz bir bağlantı noktasıdır. Dinleyici adı için DNS girişi, kullanılabilirlik grubundaki tüm çoğaltmaların adreslerine çözümlenmelidir. Bu, [betik oluştur](#create-script) bölümünde belirtilen PowerShell betiği ile otomatik olarak yapılır. DNN dinleyicileri tüm IP adreslerinde bağlantıları kabul ettiğinde, dinleyici bağlantı noktasının benzersiz olması ve kullanılabilirlik grubundaki herhangi bir çoğaltma tarafından kullanımda olmaması kritik öneme sahiptir. SQL Server, doğrudan veya SQL Browser hizmeti aracılığıyla 1433 numaralı bağlantı noktasını her zaman dinler, hiçbir DNN dinleyicisi için bağlantı noktası 1433 kullanılamaz.

## <a name="next-steps"></a>Sonraki adımlar

Azure 'da SQL Server HADR özellikleri hakkında daha fazla bilgi için bkz. [kullanılabilirlik grupları](availability-group-overview.md) ve [Yük devretme kümesi örneği](failover-cluster-instance-overview.md). Ayrıca, yüksek kullanılabilirlik ve olağanüstü durum kurtarma için ortamınızı yapılandırmaya yönelik [en iyi yöntemleri](hadr-cluster-best-practices.md) öğrenebilirsiniz. 
