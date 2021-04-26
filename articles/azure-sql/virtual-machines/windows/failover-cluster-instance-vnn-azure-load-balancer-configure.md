---
title: Yük devretme kümesi örneğini Azure Load Balancer yapılandırma VNN
description: Yüksek kullanılabilirlik ve olağanüstü durum kurtarma (HADR) için Azure VM 'lerinde SQL Server ile yük devretme kümesi örneğiniz (FCı) için sanal ağ adına (VNN) trafik yönlendirmek üzere bir Azure Load Balancer yapılandırmayı öğrenin.
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
ms.date: 06/02/2020
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 5670a29e86eb201a707e5ceef28043aafe4839d9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97357985"
---
# <a name="configure-azure-load-balancer-for-failover-cluster-instance-vnn"></a>Yük devretme kümesi örneği için Azure Load Balancer yapılandırın VNN
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Azure sanal makinelerinde kümeler, aynı anda tek bir küme düğümünde olması gereken bir IP adresini tutmak için bir yük dengeleyici kullanır. Bu çözümde, yük dengeleyici Azure 'da kümelenmiş kaynak tarafından kullanılan sanal ağ adı (VNN) için IP adresini tutar. 

Bu makale, Azure Load Balancer hizmetini kullanarak bir yük dengeleyici yapılandırmayı öğretir. Yük dengeleyici trafiği, yüksek kullanılabilirlik ve olağanüstü durum kurtarma (HADR) için Azure VM 'lerinde SQL Server [Yük devretme kümesi örneğinize (FCı)](failover-cluster-instance-overview.md) yönlendirir. 

SQL Server 2019 CU2 UYGULAMAZSANıZ ve üzeri bir alternatif bağlantı seçeneği için, Basitleştirilmiş yapılandırma ve gelişmiş yük devretme için bunun yerine bir [dağıtılmış ağ adı](failover-cluster-instance-distributed-network-name-dnn-configure.md) düşünün. 


## <a name="prerequisites"></a>Önkoşullar

Bu makaledeki adımları tamamlamadan önce, zaten şunları yapmalısınız:

- Azure Load Balancer, [HADR çözümünüz için uygun bağlantı seçeneği](hadr-cluster-best-practices.md#connectivity)olduğuna karar verdi.
- [Kullanılabilirlik grubu dinleyiciniz](availability-group-overview.md) veya [Yük devretme kümesi örneklerinizi](failover-cluster-instance-overview.md)yapılandırdınız. 
- En son [PowerShell](/powershell/azure/install-az-ps)sürümü yüklendi. 


## <a name="create-load-balancer"></a>Yük dengeleyici oluşturma

Yük dengeleyiciyi oluşturmak için [Azure Portal](https://portal.azure.com) kullanın:

1. Azure portal, sanal makineleri içeren kaynak grubuna gidin.

1. **Add (Ekle)** seçeneğini belirleyin. **Load Balancer** Için Azure Market 'te arama yapın. **Load Balancer** seçin.

1. **Oluştur**’u seçin.

1. Aşağıdaki değerleri kullanarak yük dengeleyiciyi ayarlayın:

   - **Abonelik**: Azure aboneliğiniz.
   - **Kaynak grubu**: sanal makinelerinizi içeren kaynak grubu.
   - **Ad**: yük dengeleyiciyi tanımlayan bir ad.
   - **Bölge**: sanal makinelerinizi içeren Azure konumu.
   - **Tür**: public veya Private. Sanal ağ içinden bir özel yük dengeleyiciye erişilebilir. Azure uygulamalarının çoğu özel yük dengeleyici kullanabilir. Uygulamanızın doğrudan Internet üzerinden SQL Server erişmesi gerekiyorsa, ortak yük dengeleyici kullanın.
   - **SKU**: standart.
   - **Sanal ağ**: sanal makinelerle aynı ağ.
   - **IP adresi ataması**: statik. 
   - **Özel IP adresi**: kümelenmiş ağ KAYNAĞıNA atadığınız IP adresi.

   Aşağıdaki görüntüde **yük dengeleyici oluşturma** Kullanıcı arabirimi gösterilmektedir:

   ![Yük dengeleyiciyi ayarlama](./media/failover-cluster-instance-premium-file-share-manually-configure/30-load-balancer-create.png)
   

## <a name="configure-backend-pool"></a>Arka uç havuzunu yapılandırma

1. Sanal makineleri içeren Azure Kaynak grubuna dönün ve yeni yük dengeleyiciyi bulun. Kaynak grubundaki görünümü yenilemeniz gerekebilir. Yük dengeleyiciyi seçin.

1. **Arka uç havuzları**' nı seçin ve ardından **Ekle**' yi seçin.

1. Arka uç havuzunu VM 'Leri içeren kullanılabilirlik kümesiyle ilişkilendirin.

1. **Hedef ağ IP yapılandırması** altında, **sanal makine** ' yi seçin ve küme düğümleri olarak katılacak sanal makineleri seçin. FCı veya kullanılabilirlik grubunu barındıracak tüm sanal makineleri eklediğinizden emin olun.

1. Arka uç havuzunu oluşturmak için **Tamam ' ı** seçin.

## <a name="configure-health-probe"></a>Sistem durumu araştırmasını yapılandırma

1. Yük dengeleyici bölmesinde **sistem durumu araştırmaları**' nı seçin.

1. **Add (Ekle)** seçeneğini belirleyin.

1. **Durum araştırma bölmesi Ekle** bölümünde aşağıdaki sistem <span id="probe"></span> durumu araştırma parametrelerini ayarlayın:

   - **Ad**: sistem durumu araştırması için bir ad.
   - **Protokol**: TCP.
   - **Bağlantı noktası**: [VM hazırlanırken](failover-cluster-instance-prepare-vm.md#uninstall-sql-server-1)sistem durumu araştırmasına yönelik güvenlik duvarında oluşturduğunuz bağlantı noktası. Bu makalede, örnek TCP bağlantı noktasını kullanır `59999` .
   - **Aralık**: 5 saniye.
   - **Sağlıksız eşik**: 2 ardışık başarısızlık.

1. **Tamam**’ı seçin.

## <a name="set-load-balancing-rules"></a>Yük Dengeleme kurallarını ayarlama

1. Yük dengeleyici bölmesinde, **Yük Dengeleme kuralları**' nı seçin.

1. **Add (Ekle)** seçeneğini belirleyin.

1. Yük Dengeleme kuralı parametrelerini ayarlayın:

   - **Ad**: Yük Dengeleme kuralları için bir ad.
   - **Ön uç IP adresi**: SQL Server FCI 'NıN veya AG dinleyicisinin kümelenmiş ağ kaynağının IP adresi.
   - **Bağlantı noktası**: SQL Server TCP bağlantı noktası. Varsayılan örnek bağlantı noktası 1433 ' dir.
   - **Arka uç bağlantı noktası**: **kayan IP 'yi (doğrudan sunucu dönüşü)** etkinleştirdiğinizde **bağlantı** noktası değeriyle aynı bağlantı noktasıdır.
   - **Arka uç havuzu**: daha önce yapılandırdığınız arka uç havuzu adı.
   - **Sistem durumu araştırması**: daha önce yapılandırdığınız sistem durumu araştırması.
   - **Oturum kalıcılığı**: yok.
   - **Boşta kalma zaman aşımı (dakika)**: 4.
   - **Kayan IP (doğrudan sunucu dönüşü)**: etkin.

1. **Tamam**’ı seçin.

## <a name="configure-cluster-probe"></a>Küme araştırması yapılandırma

PowerShell 'de küme araştırması bağlantı noktası parametresini ayarlayın.

Küme araştırması bağlantı noktası parametresini ayarlamak için aşağıdaki betikteki değişkenleri ortamınızdaki değerlerle güncelleştirin. Betikten açılı ayraçları ( `<` ve `>` ) kaldırın.

```powershell
$ClusterNetworkName = "<Cluster Network Name>"
$IPResourceName = "<SQL Server FCI / AG Listener IP Address Resource Name>" 
$ILBIP = "<n.n.n.n>" 
[int]$ProbePort = <nnnnn>

Import-Module FailoverClusters

Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
```

Aşağıdaki tabloda, güncelleştirmeniz gereken değerler açıklanmaktadır:


|**Değer**|**Açıklama**|
|---------|---------|
|`Cluster Network Name`| Ağ için Windows Server yük devretme kümesi adı. **Yük devretme kümesi Yöneticisi**  >  **ağlarda** ağa sağ tıklayıp **Özellikler**' i seçin. Doğru değer **Genel** sekmesindeki **Ad**’ın altında yer alır.|
|`SQL Server FCI/AG listener IP Address Resource Name`|SQL Server FCı 'nın veya AG dinleyicisinin IP adresinin kaynak adı. **Yük devretme kümesi Yöneticisi**  >  **Roller**' de, SQL Server FCI rolü altında, **sunucu adı** altında, IP adresi kaynağına sağ tıklayın ve **Özellikler**' i seçin. Doğru değer **Genel** sekmesindeki **Ad**’ın altında yer alır.|
|`ILBIP`|İç yük dengeleyicinin (ıLB) IP adresi. Bu adres, Azure portal ıLB 'nin ön uç adresi olarak yapılandırılır. Bu Ayrıca, FCı 'nın IP adresidir SQL Server. **Yük devretme kümesi Yöneticisi** içinde bulduğunuz aynı Özellikler sayfasında bulabilirsiniz `<SQL Server FCI/AG listener IP Address Resource Name>` .|
|`nnnnn`|Yük dengeleyicinin sistem durumu araştırmasına yapılandırdığınız araştırma bağlantı noktası. Kullanılmayan herhangi bir TCP bağlantı noktası geçerlidir.|
|Altağ| Küme parametresi için alt ağ maskesi. TCP IP yayını adresi olmalıdır: `255.255.255.255` .| 


Küme araştırmasını ayarladıktan sonra, PowerShell 'de tüm küme parametrelerini görebilirsiniz. Şu betiği çalıştırın:

```powershell
Get-ClusterResource $IPResourceName | Get-ClusterParameter
```


## <a name="test-failover"></a>Yük devretme testi


Küme işlevselliğini doğrulamak için kümelenmiş kaynağın yük devretmesini test edin. 

Aşağıdaki adımları izleyin:

1. RDP kullanarak SQL Server küme düğümlerinden birine bağlanın.
1. **Yük devretme kümesi Yöneticisi** açın. **Rolleri** seçin. Hangi düğümün SQL Server FCı rolüne sahip olduğuna dikkat edin.
1. SQL Server FCı rolüne sağ tıklayın. 
1. **Taşı**' yı seçin ve ardından **mümkün olan en iyi düğümü** seçin.

**Yük devretme kümesi Yöneticisi** rolü gösterir ve kaynakları çevrimdışı duruma geçer. Kaynaklar daha sonra taşınır ve diğer düğümde yeniden çevrimiçi duruma gelir.


## <a name="test-connectivity"></a>Bağlantıyı test etme

Bağlantıyı sınamak için aynı sanal ağdaki başka bir sanal makinede oturum açın. **SQL Server Management Studio** açın ve SQL Server FCI adına bağlanın. 

>[!NOTE]
>Gerekirse, [SQL Server Management Studio indirebilirsiniz](/sql/ssms/download-sql-server-management-studio-ssms).



## <a name="next-steps"></a>Sonraki adımlar

Azure 'da SQL Server HADR özellikleri hakkında daha fazla bilgi için bkz. [kullanılabilirlik grupları](availability-group-overview.md) ve [Yük devretme kümesi örneği](failover-cluster-instance-overview.md). Ayrıca, yüksek kullanılabilirlik ve olağanüstü durum kurtarma için ortamınızı yapılandırmaya yönelik [en iyi yöntemleri](hadr-cluster-best-practices.md) öğrenebilirsiniz. 



