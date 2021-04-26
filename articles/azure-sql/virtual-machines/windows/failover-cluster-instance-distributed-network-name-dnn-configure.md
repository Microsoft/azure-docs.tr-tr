---
title: Yük devretme kümesi örneği için DNN 'yi yapılandırma
description: Azure VM yük devretme kümesi örneği (FCı) üzerinde SQL Server trafiği yönlendirmek için bir dağıtılmış ağ adı (DNN) yapılandırma hakkında bilgi edinin.
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
ms.openlocfilehash: 8549592ace00e712929ebc76045a32531b9db659
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97358325"
---
# <a name="configure-a-dnn-for-failover-cluster-instance"></a>Yük devretme kümesi örneği için DNN yapılandırma
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Azure sanal makinelerinde, dağıtılmış ağ adı (DNN) trafiği uygun kümelenmiş kaynağa yönlendirir. Bir Azure Load Balancer gerek olmadan sanal ağ adından (VNN) SQL Server yük devretme kümesi örneğine (FCı) bağlanmak için daha kolay bir yol sağlar. 

Bu makalede, yüksek kullanılabilirlik ve olağanüstü durum kurtarma (HADR) için Azure VM 'lerinde SQL Server yük devretme kümesi örneğinize trafik yönlendirmek üzere DNN kaynağı yapılandırmanız öğretilir. 

DNN özelliği şu anda yalnızca Windows Server 2016 ve sonraki sürümlerde SQL Server 2019 CU2 UYGULAMAZSANıZ ve üzeri sürümlerde kullanılabilir. 

Alternatif bir bağlantı seçeneği için, bunun yerine bir [sanal ağ adı ve Azure Load Balancer](failover-cluster-instance-vnn-azure-load-balancer-configure.md) göz önünde bulundurun. 

## <a name="overview"></a>Genel Bakış

Dağıtılmış ağ adı (DNN), [SQL Server VM 'Lerde her zaman açık yük devretme kümesi örneğiyle](failover-cluster-instance-overview.md)birlikte kullanıldığında, sanal ağ adını (vnn) bağlantı noktası olarak değiştirir. Bu, sanal nn 'e yönelik bir Azure Load Balancer yönlendirme trafiği gereksinimini geçersiz kılar, dağıtım, bakım ve yük devretmeyi geliştirir. 

FCı dağıtımıyla birlikte, VNN hala vardır ancak istemci, VNN adı yerine DNN DNS adına bağlanır. 

## <a name="prerequisites"></a>Önkoşullar 

Bu makaledeki adımları tamamlamadan önce, zaten şunları yapmalısınız:

- CU2 UYGULAMAZSANıZ veya sonraki sürümlerde 2019 SQL Server, Windows Server 2016 ve üzeri sürümlerde
- Dağıtılmış ağ adının [HADR çözümünüz için uygun bağlantı seçeneği](hadr-cluster-best-practices.md#connectivity)olduğuna karar verilir.
- [Yük devretme kümesi örneklerinizi](failover-cluster-instance-overview.md)yapılandırdınız. 
- En son [PowerShell](/powershell/azure/install-az-ps)sürümü yüklendi. 

## <a name="create-dnn-resource"></a>DNN kaynağı oluşturma 

DNN kaynağı, SQL Server FCı ile aynı küme grubunda oluşturulur. FCı küme grubu içinde DNN kaynağını oluşturmak için PowerShell 'i kullanın. 

Aşağıdaki PowerShell komutu, kaynak adı SQL Server FCı kümesi grubuna DNN kaynağı ekler `<dnnResourceName>` . Kaynak adı bir kaynağı benzersiz bir şekilde tanımlamak için kullanılır. Sizin için anlamlı olan ve küme genelinde benzersiz olan bir tane kullanın. Kaynak türü olmalıdır `Distributed Network Name` . 

`-Group`Değerin, dağıtılmış ağ adını eklemek istediğiniz SQL Server FCI 'ye karşılık gelen küme grubunun adı olması gerekir. Varsayılan örnek için tipik biçim olur `SQL Server (MSSQLSERVER)` . 


```powershell
Add-ClusterResource -Name <dnnResourceName> `
-ResourceType "Distributed Network Name" -Group "<WSFC role of SQL server instance>"
```

Örneğin, `dnn-demo` bir varsayılan SQL Server FCI için DNN kaynağınızı oluşturmak için aşağıdaki PowerShell komutunu kullanın:

```powershell
Add-ClusterResource -Name dnn-demo `
-ResourceType "Distributed Network Name" -Group "SQL Server (MSSQLSERVER)"

```

## <a name="set-cluster-dnn-dns-name"></a>Küme DNN DNS adını ayarla

Kümedeki DNN kaynağı için DNS adını ayarlayın. Daha sonra küme, trafiği Şu anda SQL Server FCı barındıran düğüme yönlendirmek için bu değeri kullanır. 

İstemciler SQL Server FCı 'ya bağlanmak için DNS adını kullanır. Benzersiz bir değer seçebilirsiniz. Ya da zaten var olan bir FCı varsa ve istemci bağlantı dizelerini güncelleştirmek istemiyorsanız DNN 'yi istemcilerin zaten kullandığı geçerli VNN 'yi kullanacak şekilde yapılandırabilirsiniz. Bunu yapmak için, DNS 'de DNN 'yi ayarlamadan önce [VNN öğesini yeniden adlandırmanız](#rename-the-vnn) gerekir.

DNN 'niz için DNS adını ayarlamak üzere bu komutu kullanın: 

```powershell
Get-ClusterResource -Name <dnnResourceName> | `
Set-ClusterParameter -Name DnsName -Value <DNSName>
```

Bu `DNSName` değer, istemcilerin SQL Server FCI 'ya bağlanmak için kullanacağı değerdir. Örneğin, istemcilerin bağlanması için `FCIDNN` aşağıdaki PowerShell komutunu kullanın:

```powershell
Get-ClusterResource -Name dnn-demo | `
Set-ClusterParameter -Name DnsName -Value FCIDNN
```

İstemciler şimdi `FCIDNN` SQL Server FCI 'ya bağlanılırken bağlantı dizesine girer. 

   > [!WARNING]
   > FCı altyapısının gerekli bir bileşeni olduğundan geçerli sanal ağ adını (VNN) silmeyin. 


### <a name="rename-the-vnn"></a>VNN 'i yeniden adlandırma 

Var olan bir sanal ağ adınız varsa ve istemcilerin SQL Server FCı 'ya bağlanmak için bu değeri kullanmaya devam etmesini istiyorsanız, geçerli VNN değerini bir yer tutucu değeri olarak yeniden adlandırmanız gerekir. Geçerli VNN yeniden adlandırıldıktan sonra DNN için DNS adı değerini VNN olarak ayarlayabilirsiniz. 

VNN 'i yeniden adlandırmak için bazı kısıtlamalar uygulanır. Daha fazla bilgi için bkz. [FCI 'Yi yeniden adlandırma](/sql/sql-server/failover-clusters/install/rename-a-sql-server-failover-cluster-instance).

Geçerli VNN kullanılması işiniz için gerekli değilse, bu bölümü atlayın. VNN 'yi yeniden adlandırdıktan sonra, [küme DNN DNS adını ayarlayın](#set-cluster-dnn-dns-name). 

   
## <a name="set-dnn-resource-online"></a>DNN kaynağını çevrimiçi ayarla

DNN kaynağınız uygun şekilde adlandırıldıktan sonra ve kümede DNS adı değerini ayarladıktan sonra, PowerShell kullanarak DNN kaynağını kümede çevrimiçi olarak ayarlayın: 

```powershell
Start-ClusterResource -Name <dnnResourceName>
```

Örneğin, DNN kaynağını başlatmak için `dnn-demo` aşağıdaki PowerShell komutunu kullanın: 

```powershell
Start-ClusterResource -Name dnn-demo
```

## <a name="configure-possible-owners"></a>Olası sahipleri yapılandırma

Varsayılan olarak, küme DNN DNS adını kümedeki tüm düğümlere bağlar. Ancak, kümedeki SQL Server FCı 'nin parçası olmayan düğümler DNN olası sahipler listesinden dışlanmalıdır. 

Olası sahipleri güncelleştirmek için şu adımları izleyin:

1. Yük Devretme Kümesi Yöneticisi DNN kaynağına gidin. 
1. DNN kaynağına sağ tıklayın ve **Özellikler**' i seçin. 

   :::image type="content" source="media/hadr-distributed-network-name-dnn-configure/fci-dnn-properties.png" alt-text="DNN kaynağı için kısayol menüsü, Özellikler komutuyla vurgulanır.":::

1. Yük devretme kümesi örneğine katılmayan düğümlerin onay kutusunu temizleyin. DNN kaynağı için olası sahipler listesi, SQL Server örneği kaynağı için olası sahipler listesiyle eşleşmelidir. Örneğin, DATA3 'in FCı 'ye katılmadığından, aşağıdaki görüntüde DNN kaynağı için olası sahipler listesinden DATA3 kaldırma örneği verilmiştir: 

   :::image type="content" source="media/hadr-distributed-network-name-dnn-configure/clear-check-for-nodes-not-in-fci.png" alt-text="DNN kaynağının olası sahipleri için FCı 'ye katılmayan düğümlerin yanındaki onay kutusunu temizleyin":::

1. Ayarlarınızı kaydetmek için **Tamam ' ı** seçin. 


## <a name="restart-sql-server-instance"></a>SQL Server örneğini yeniden Başlat 

SQL Server örneğini yeniden başlatmak için Yük Devretme Kümesi Yöneticisi kullanın. Şu adımları izleyin:

1. Yük Devretme Kümesi Yöneticisi SQL Server kaynağına gidin.
1. SQL Server kaynağına sağ tıklayın ve çevrimdışına alın. 
1. Tüm ilişkili kaynaklar çevrimdışı olduktan sonra, SQL Server kaynağına sağ tıklayıp tekrar çevrimiçi duruma getirin. 

## <a name="update-connection-string"></a>Bağlantı dizesini Güncelleştir

Yük devretme sonrasında hızlı bağlantı sağlamak için, `MultiSubnetFailover=True` SQL istemci sürümü 4.6.1 ' den daha eski olursa bağlantı dizesine ekleyin. 

Ayrıca, DNN özgün VNN kullanıyorsa, FCı SQL Server bağlanan SQL istemcilerinin bağlantı dizelerini DNN DNS adına güncelleştirmesi gerekir. Bu gereksinimden kaçınmak için DNS ad değerini VNN adı olacak şekilde güncelleştirebilirsiniz. Ancak [var olan VNN öğesini önce bir yer tutucu ile değiştirmeniz](#rename-the-vnn) gerekir. 

## <a name="test-failover"></a>Yük devretme testi


Küme işlevselliğini doğrulamak için kümelenmiş kaynağın yük devretmesini test edin. 


Yük devretmeyi sınamak için aşağıdaki adımları izleyin: 

1. RDP kullanarak SQL Server küme düğümlerinden birine bağlanın.
1. **Yük devretme kümesi Yöneticisi** açın. **Rolleri** seçin. Hangi düğümün SQL Server FCı rolüne sahip olduğuna dikkat edin.
1. SQL Server FCı rolüne sağ tıklayın. 
1. **Taşı**' yı seçin ve ardından **mümkün olan en iyi düğümü** seçin.

**Yük devretme kümesi Yöneticisi** rolü gösterir ve kaynakları çevrimdışı duruma geçer. Kaynaklar daha sonra taşınır ve diğer düğümde yeniden çevrimiçi duruma gelir.

## <a name="test-connectivity"></a>Bağlantıyı test etme

Bağlantıyı sınamak için aynı sanal ağdaki başka bir sanal makinede oturum açın. **SQL Server Management Studio** açın ve DNN DNS adını kullanarak SQL Server FCI 'ye bağlanın.

Gerekirse, [SQL Server Management Studio indirebilirsiniz](/sql/ssms/download-sql-server-management-studio-ssms).


## <a name="avoid-ip-conflict"></a>IP çakışmasını önleyin

Bu, FCı kaynağı tarafından kullanılan sanal IP (VIP) adresinin Azure 'daki başka bir kaynağa yinelenen olarak atanmasını önlemeye yönelik isteğe bağlı bir adımdır. 

Müşteriler artık SQL Server FCı 'ya bağlanmak için DNN 'yi kullansa da, sanal ağ adı (VNN) ve sanal IP, FCı altyapısının gerekli bileşenleri olduklarından silinemez. Ancak, Azure 'da sanal IP adresini artık bir yük dengeleyici ayırdığından, sanal ağdaki başka bir kaynağa, FCı tarafından kullanılan sanal IP adresiyle aynı IP adresinin atanması riski vardır. Bu muhtemelen yinelenen bir IP çakışma sorununa neden olabilir. 

IP adresini ayırmak için bir APIPA adresi veya ayrılmış bir ağ bağdaştırıcısı yapılandırın. 

### <a name="apipa-address"></a>APIPA adresi

Yinelenen IP adresleri kullanmaktan kaçınmak için, bir APIPA adresi yapılandırın (bağlantı yerel adresi olarak da bilinir). Bunun için aşağıdaki komutu çalıştırın:

```powershell
Get-ClusterResource "virtual IP address" | Set-ClusterParameter 
    –Multiple @{"Address”=”169.254.1.1”;”SubnetMask”=”255.255.0.0”;"OverrideAddressMatch"=1;”EnableDhcp”=0}
```

Bu komutta, "sanal IP adresi" kümelenmiş VIP adresi kaynağının adıdır ve "169.254.1.1", VIP adresi için seçilen APIPA adresidir. İşletmenize en uygun adresi seçin. `OverrideAddressMatch=1`APIPA adres alanı dahil olmak üzere, IP adresinin herhangi bir ağda olmasını sağlamak üzere ayarlanır. 

### <a name="dedicated-network-adapter"></a>Adanmış ağ bağdaştırıcısı

Alternatif olarak, Azure 'da sanal IP adresi kaynağı tarafından kullanılan IP adresini ayırmak için bir ağ bağdaştırıcısı yapılandırın. Ancak, bu adres, alt ağ adres alanındaki adresi kullanır ve ağ bağdaştırıcısının herhangi bir amaçla kullanılmadığından emin olmanın ek yükü vardır.

## <a name="limitations"></a>Sınırlamalar

- Şu anda, FCı ile DNN, Windows Server 2016 ve üzeri sürümlerde yalnızca SQL Server 2019 CU2 UYGULAMAZSANıZ ve üzeri sürümlerde desteklenir. 
- Diğer SQL Server özellikleriyle ve DNN içeren bir FCı ile çalışırken daha fazla dikkat edilmesi gerekebilir. Daha fazla bilgi için bkz. [DNN birlikte çalışabilirliği Ile FCI](failover-cluster-instance-dnn-interoperability.md). 

## <a name="next-steps"></a>Sonraki adımlar

Azure 'da SQL Server HADR özellikleri hakkında daha fazla bilgi için bkz. [kullanılabilirlik grupları](availability-group-overview.md) ve [Yük devretme kümesi örneği](failover-cluster-instance-overview.md). Ayrıca, yüksek kullanılabilirlik ve olağanüstü durum kurtarma için ortamınızı yapılandırmaya yönelik [en iyi yöntemleri](hadr-cluster-best-practices.md) öğrenebilirsiniz. 


