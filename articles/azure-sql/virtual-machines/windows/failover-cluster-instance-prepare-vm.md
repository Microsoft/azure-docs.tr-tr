---
title: Bir FCı için sanal makineleri hazırlama
description: Azure sanal makinelerinizi bunları bir yük devretme kümesi örneği (FCı) ve Azure sanal makinelerinde SQL Server kullanacak şekilde hazırlayın.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/02/2020
ms.author: mathoma
ms.openlocfilehash: 10f01fd5943928eda1f1e4518f30c8e3ccf56b46
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98737804"
---
# <a name="prepare-virtual-machines-for-an-fci-sql-server-on-azure-vms"></a>Bir FCı için sanal makineleri hazırlama (Azure VM 'lerinde SQL Server)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Bu makalede, Azure sanal makinelerinin (VM 'Ler) SQL Server yük devretme kümesi örneği (FCı) ile kullanılması için nasıl hazırlanacağı açıklanmaktadır. Yapılandırma ayarları, FCı depolama çözümüne bağlı olarak farklılık gösterir, bu nedenle ortamınıza ve işletmenize uyacak doğru yapılandırmayı istediğinizi doğrulayın. 

Daha fazla bilgi edinmek için bkz. Azure VM 'lerde ve [küme en iyi uygulamalarında](hadr-cluster-best-practices.md) [SQL Server ile FCI](failover-cluster-instance-overview.md) 'ye genel bakış. 

## <a name="prerequisites"></a>Önkoşullar 

- Microsoft Azure aboneliği. [Ücretsiz](https://azure.microsoft.com/free/)olarak kullanmaya başlayın. 
- Azure sanal makinelerinde veya şirket içi bir veri merkezinde sanal ağ eşleştirmesine sahip Azure 'a genişletilmiş bir Windows etki alanı.
- Azure sanal makinelerinde ve Active Directory nesne oluşturma izinlerine sahip olan bir hesap.
- Bu bileşenler için yeterli IP adresi alanına sahip bir Azure sanal ağı ve alt ağı:
   - Her iki sanal makine
   - Windows Yük devretme kümesinin IP adresi
   - Her FCı için bir IP adresi
- Etki alanı denetleyicilerine işaret eden Azure ağı üzerinde yapılandırılmış DNS.

## <a name="choose-an-fci-storage-option"></a>FCı depolama seçeneğini belirleyin

Sanal makinenizin yapılandırma ayarları, SQL Server yük devretme kümesi örneğiniz için kullanmayı planladığınız depolama seçeneğine göre farklılık gösterir. Sanal makineyi hazırlanmadan önce, [kullanılabilir FCI depolama seçeneklerini](failover-cluster-instance-overview.md#storage) gözden geçirin ve ortamınıza ve iş gereksinimlerinize en uygun seçeneği belirleyin. Ardından, depolama seçiminize bağlı olarak bu makale boyunca uygun VM yapılandırma seçeneklerini dikkatle seçin. 

## <a name="configure-vm-availability"></a>VM kullanılabilirliğini yapılandırma 

Yük devretme kümesi özelliği, sanal makinelerin bir [kullanılabilirlik kümesine](../../../virtual-machines/linux/tutorial-availability-sets.md) veya bir [kullanılabilirlik bölgesine](../../../availability-zones/az-overview.md#availability-zones)yerleştirilmesini gerektirir. Kullanılabilirlik kümeleri ' ni seçerseniz, VM 'Leri daha yakın bulmak için [yakınlık yerleştirme gruplarını](../../../virtual-machines/co-location.md#proximity-placement-groups) kullanabilirsiniz. Aslında, yakınlık yerleştirme grupları, Azure Paylaşılan disklerini kullanmak için bir önkoşuldur. 

Amaçlanan küme yapılandırmanızla eşleşen VM kullanılabilirlik seçeneğini dikkatle seçin: 

- **Azure paylaşılan diskler**: kullanılabilirlik seçeneği, Premium ssds veya UltraDisk kullanıyorsanız değişir:
   - Premium SSD: bir [yakınlık yerleştirme grubunun](../../../virtual-machines/windows/proximity-placement-groups-portal.md)Içine yerleştirilmiş Premium SSD 'ler için farklı hata/güncelleştirme etki alanlarında [kullanılabilirlik kümesi](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set) .
   - Ultra Disk: [kullanılabilirlik alanı](../../../virtual-machines/windows/create-portal-availability-zone.md#confirm-zone-for-managed-disk-and-ip-address) ancak VM 'lerin, kümenin kullanılabilirliğini% 99,9 olarak azaltan aynı Kullanılabilirlik bölgesine yerleştirilmesi gerekir. 
- **Premium dosya paylaşımları**: [kullanılabilirlik kümesi](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set) veya [kullanılabilirlik alanı](../../../virtual-machines/windows/create-portal-availability-zone.md#confirm-zone-for-managed-disk-and-ip-address).
- **Depolama alanları doğrudan**: [kullanılabilirlik kümesi](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set).

> [!IMPORTANT]
> Bir sanal makineyi oluşturduktan sonra kullanılabilirlik kümesini ayarlayamazsınız veya değiştiremezsiniz.

## <a name="create-the-virtual-machines"></a>Sanal makineleri oluşturma

VM kullanılabilirliğini yapılandırdıktan sonra, sanal makinelerinizi oluşturmaya hazırsınız demektir. Üzerinde SQL Server zaten yüklü olmayan veya olmayan bir Azure Marketi görüntüsü kullanmayı seçebilirsiniz. Ancak, Azure VM 'lerinde SQL Server için bir görüntü seçerseniz, yük devretme kümesi örneğini yapılandırmadan önce sanal makineden SQL Server kaldırmanız gerekir. 

### <a name="considerations"></a>Dikkat edilmesi gerekenler

Bir Azure VM konuk yük devretme kümesinde, sunucu başına (küme düğümü) ve tek bir alt ağ için tek bir NIC önerilir. Azure ağ iletişimi, Azure IaaS VM Konuk kümesinde ek NIC 'Lerin ve alt ağların gereksiz olmasını sağlayan fiziksel yedekliliğe sahiptir. Küme doğrulama raporu, düğümlerin yalnızca tek bir ağ üzerinde erişilebilir durumda olduğuna dair bir uyarı gösterse de bu uyarı Azure IaaS VM konuk yük devretme kümelerinde güvenli bir şekilde yoksayılabilir.

Her iki sanal makineyi de Yerleştir:

- Kullanılabilirlik kümeleri kullanıyorsanız, kullanılabilirlik kümesiyle aynı Azure Kaynak grubunda.
- Etki alanı denetleyiciniz ile aynı sanal ağda veya etki alanı denetleyicinize uygun bağlantı sağlayan bir sanal ağ üzerinde.
- Hem sanal makineler için yeterli IP adresi alanına sahip bir alt ağda hem de tüm FC, kümede kullanmaya devam edebilirsiniz.
- Azure kullanılabilirlik kümesi veya kullanılabilirlik bölgesinde.

SQL Server önceden [yüklenmiş veya](sql-vm-create-portal-quickstart.md) [olmayan](../../../virtual-machines/windows/quick-create-portal.md) bir görüntüyü kullanarak bir Azure sanal makinesi oluşturabilirsiniz. SQL Server görüntüsünü seçerseniz, yük devretme kümesi örneğini yüklemeden önce SQL Server örneğini el ile kaldırmanız gerekir. 


## <a name="uninstall-sql-server"></a>SQL Server kaldır

FCı oluşturma sürecinin bir parçası olarak, SQL Server yük devretme kümesine bir kümelenmiş örnek olarak yüklersiniz. *SQL Server olmadan bir Azure Marketi görüntüsüne sahip bir sanal makine dağıttıysanız, bu adımı atlayabilirsiniz.* Önceden yüklenmiş SQL Server bir görüntü dağıttıysanız, SQL Server VM SQL IaaS Aracısı uzantısından silmeniz ve ardından SQL Server kaldırmanız gerekir. 

### <a name="unregister-from-the-sql-iaas-agent-extension"></a>SQL IaaS Aracısı uzantısı kaydını silme

Azure Marketi 'ndeki SQL Server VM resimler, SQL IaaS Aracısı Uzantısı ile otomatik olarak kaydedilir. Önceden yüklenmiş SQL Server örneğini kaldırmadan önce, [her bir SQL Server VM SQL IaaS Aracısı uzantısı 'ndan kaydını](sql-agent-extension-manually-register-single-vm.md#unregister-from-extension)kaldırmanız gerekir. 

### <a name="uninstall-sql-server"></a>SQL Server kaldır

Uzantıdan kaydolduktan sonra, SQL Server kaldırabilirsiniz. Her sanal makine için aşağıdaki adımları izleyin: 

1. RDP kullanarak sanal makineye bağlanın.

   RDP kullanarak bir sanal makineye ilk kez bağlandığınızda, bir istem, BILGISAYARıN ağda bulunabilir olmasını isteyip istemediğinizi sorar. **Evet**’i seçin.

1. SQL Server tabanlı sanal makine görüntülerinden birini kullanıyorsanız, SQL Server örneğini kaldırın:

   1. **Programlar ve Özellikler**' de **Microsoft SQL Server 201_ (64-bit)** öğesine sağ tıklayın ve **Kaldır/Değiştir**' i seçin.
   1. **Kaldır**' ı seçin.
   1. Varsayılan örneği seçin.
   1. **Veritabanı motoru Hizmetleri** altındaki tüm özellikleri kaldırın. **Paylaşılan Özellikler** altında herhangi bir şeyi kaldırmayın. Aşağıdaki ekran görüntüsüne benzer bir şey göreceksiniz:

      ![Özellik seçme](./media/failover-cluster-instance-prepare-vm/03-remove-features.png)

   1. **İleri**' yi ve ardından **Kaldır**' ı seçin.
   1. Örnek başarıyla kaldırıldıktan sonra, sanal makineyi yeniden başlatın. 

## <a name="open-the-firewall"></a>Güvenlik duvarını açın 

Her bir sanal makinede, SQL Server kullandığı Windows Güvenlik Duvarı TCP bağlantı noktasını açın. Varsayılan olarak, bu bağlantı noktası 1433 ' dir. Ancak, bir Azure VM dağıtımında SQL Server bağlantı noktasını değiştirebilirsiniz, bu nedenle ortamınızda kullanılan SQL Server bağlantı noktasını açın. Bu bağlantı noktası, Azure Marketi 'nden dağıtılan SQL Server görüntülerde otomatik olarak açılır. 

[Yük dengeleyici](failover-cluster-instance-vnn-azure-load-balancer-configure.md)kullanıyorsanız, sistem durumu araştırmasının kullandığı bağlantı noktasını da açmanız gerekir. Varsayılan olarak, bu bağlantı noktası 59999 ' dir. Ancak, yük dengeleyiciyi oluştururken belirttiğiniz herhangi bir TCP bağlantı noktası olabilir. 

Bu tabloda, FCı yapılandırmanıza bağlı olarak, açmanız gerekebilecek bağlantı noktaları ayrıntılı olarak yer alır: 

   | Amaç | Bağlantı noktası | Notlar
   | ------ | ------ | ------
   | SQL Server | TCP 1433 | Varsayılan SQL Server örnekleri için normal bağlantı noktası. Galeriden bir görüntü kullandıysanız, bu bağlantı noktası otomatik olarak açılır. </br> </br> **Kullanan**: tüm FCI yapılandırması. |
   | Durum yoklaması | TCP 59999 | Açık herhangi bir TCP bağlantı noktası. Yük dengeleyici [sistem durumu araştırmasını](failover-cluster-instance-vnn-azure-load-balancer-configure.md#configure-health-probe) ve kümeyi Bu bağlantı noktasını kullanacak şekilde yapılandırın. </br> </br> **Kullanan**: FCI yük dengeleyici. |
   | Dosya paylaşımı | UDP 445 | Dosya paylaşma hizmetinin kullandığı bağlantı noktası. </br> </br> **Kullanan**: FCI Premium dosya paylaşımıyla. |

## <a name="join-the-domain"></a>Etki alanına katılarak

Ayrıca sanal makinelerinizi etki alanına eklemeniz gerekir. Bunu bir [hızlı başlangıç şablonu](../../../active-directory-domain-services/join-windows-vm-template.md#join-an-existing-windows-server-vm-to-a-managed-domain)kullanarak yapabilirsiniz. 

## <a name="review-storage-configuration"></a>Depolama yapılandırmasını gözden geçir

Azure Marketi 'nden oluşturulan sanal makineler, bağlı depolama alanı ile gelir. FCı depolama alanınızı Premium dosya paylaşımları veya Azure paylaşılan diskler kullanarak yapılandırmayı planlıyorsanız, yerel depolama, yük devretme kümesi örneği için kullanılmadığından, bağlı depolamayı maliyetlerde tasarruf etmek üzere kaldırabilirsiniz. Ancak, Depolama Alanları Doğrudan FCı çözümleri için eklenen depolamayı kullanmak mümkündür. bu nedenle, bu durumda bunları kaldırmak faydalı olabilir. Bağlı depolamanın, maliyetleri kaydetmek için uygun olup olmadığını öğrenmek için FCı depolama çözümünüzü gözden geçirin. 


## <a name="next-steps"></a>Sonraki adımlar

Sanal makine ortamınızı hazırladığınıza göre, yük devretme kümesi örneğinizi yapılandırmaya hazırsınız demektir. 

İşletmeniz için uygun olan FCı ortamını yapılandırmak için aşağıdaki kılavuzlardan birini seçin: 
- [Azure Paylaşılan disklerle FCı 'yi yapılandırma](failover-cluster-instance-azure-shared-disks-manually-configure.md)
- [FCı 'yi Premium dosya paylaşımıyla yapılandırma](failover-cluster-instance-premium-file-share-manually-configure.md)
- [FCı 'yı Depolama Alanları Doğrudan ile yapılandırma](failover-cluster-instance-storage-spaces-direct-manually-configure.md)

Daha fazla bilgi edinmek için bkz. [Azure VM 'lerde SQL Server Ile FCI](failover-cluster-instance-overview.md) 'ye genel bakış ve [desteklenen HADR yapılandırması](hadr-cluster-best-practices.md). 

Daha fazla bilgi için bkz. 
- [Windows küme teknolojileri](/windows-server/failover-clustering/failover-clustering-overview)   
- [SQL Server yük devretme kümesi örnekleri](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)