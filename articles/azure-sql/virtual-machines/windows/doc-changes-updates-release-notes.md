---
title: Azure sanal makinelerinde SQL Server için belge değişiklikleri
description: Azure sanal makinelerinde SQL Server farklı sürümleri için yeni özellikler ve geliştirmeler hakkında bilgi edinin.
services: virtual-machines-windows
author: MashaMSFT
ms.author: mathoma
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.topic: reference
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/15/2020
ms.openlocfilehash: ff4e6e0451b57046fb8f07f5a1051235e1f6d0f5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96325732"
---
# <a name="documentation-changes-for-sql-server-on-azure-virtual-machines"></a>Azure sanal makinelerinde SQL Server için belge değişiklikleri
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Azure, içinde yerleşik SQL Server bir görüntü ile sanal makine (VM) dağıtmanıza olanak tanır. Bu makalede, [Azure sanal makinelerinde son SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/)sürümlerindeki yeni özellikler ve geliştirmelerle ilişkili belge değişiklikleri özetlenmektedir. 

## <a name="october-2020"></a>Ekim 2020

| Değişiklikler | Ayrıntılar |
| --- | --- |
| **AG için DNN** | Artık SQL Server 2019 CU8 ve üzeri bir [dağıtılmış ağ adı (DNN) dinleyicisi](availability-group-distributed-network-name-dnn-listener-configure.md) yapılandırarak geleneksel [vnn dinleyicisinin](availability-group-overview.md#connectivity)yerini alarak Azure Load Balancer gereksinimini ortadan kaldırabilirsiniz.   | 

## <a name="september-2020"></a>Eylül 2020

| Değişiklikler | Ayrıntılar |
| --- | --- |
| **Otomatik uzantı kaydı** | Artık [otomatik kayıt](sql-agent-extension-automatic-registration-all-vms.md) özelliğini, aboneliğinize zaten dağıtılmış olan tüm SQL Server sanal makinelerini [SQL IaaS Aracısı uzantısı](sql-server-iaas-agent-extension-automate-management.md)ile otomatik olarak kaydedecek şekilde etkinleştirebilirsiniz. Bu, tüm mevcut VM 'Ler için geçerlidir ve gelecekte eklenen tüm SQL Server VM 'Leri otomatik olarak kaydeder.   | 


## <a name="august-2020"></a>Ağustos 2020

| Değişiklikler | Ayrıntılar |
| --- | --- |
| **Portalda AG 'yi yapılandırma** | Artık [Azure Portal aracılığıyla kullanılabilirlik grubunuzu yapılandırmak](availability-group-azure-portal-configure.md)mümkündür. Bu özellik şu anda önizleme aşamasındadır ve dağıtılmakta olduğundan, istediğiniz bölge kullanılamıyorsa yakında tekrar kontrol edin. | 


## <a name="july-2020"></a>Temmuz 2020


| Değişiklikler | Ayrıntılar |
| --- | --- |
| **Günlüğü Ultra diske geçir** | Yüksek performans ve düşük gecikme süresine karşı [günlük dosyanızı bir ultra diske nasıl geçirebileceğinizi](storage-migrate-to-ultradisk.md) öğrenin. | 
| **Azure PowerShell kullanarak AG oluşturma** | Artık [Azure PowerShell](availability-group-az-commandline-configure.md) ve Azure CLI 'yi kullanarak bir kullanılabilirlik grubu oluşturulmasını basitleştirmek mümkündür. | 


## <a name="june-2020"></a>Haziran 2020

| Değişiklikler | Ayrıntılar |
| --- | --- |
| **Dağıtılmış ağ adı (DNN)** | Windows Server 2016 + üzerinde SQL Server 2019, artık Azure Load Balancer kullanmak yerine [dağıtılmış ağ adı](./failover-cluster-instance-distributed-network-name-dnn-configure.md) kullanarak yük devretme kümesi örneğinize (FCI) yönlendirme desteğini önizleyecektir. Bu destek, Azure 'da yüksek kullanılabilirlik (HA) çözümünüze bağlanmanızı basitleştirir ve kolaylaştırır. | 
| **Azure paylaşılan diskler ile FCı** | Artık [Yük devretme kümesi örneğinizi (FCı)](failover-cluster-instance-overview.md) [Azure paylaşılan diskler](failover-cluster-instance-azure-shared-disks-manually-configure.md)' i kullanarak dağıtmak mümkündür. |
| **FCı belgeleri yeniden düzenlendi** | [Azure VM 'lerinde SQL Server yük devretme kümesi örneklerinin](failover-cluster-instance-overview.md) etrafındaki belgeler yeniden yazıldı ve açıklık için yeniden düzenlendi. [Küme yapılandırması en iyi uygulamaları](hadr-cluster-best-practices.md), bir [SQL Server FCI için bir sanal makinenin](failover-cluster-instance-prepare-vm.md)hazırlanması ve [Azure Load Balancer](./availability-group-vnn-azure-load-balancer-configure.md)nasıl yapılandırılacağı gibi bazı yapılandırma içeriğini ayırdık. | 
| &nbsp; | &nbsp; |


## <a name="may-2020"></a>Mayıs 2020 

| Değişiklikler | Ayrıntılar |
| --- | --- |
| **Azure SQL ailesi** | Azure sanal makinelerinde SQL Server artık [Azure SQL ürün ailesinin](../../azure-sql-iaas-vs-paas-what-is-overview.md)bir parçasıdır. [Yeni bakımızı](../index.yml)inceleyin! Üründe hiçbir şey değişmedi, ancak Azure SQL ürün kararlarını daha kolay hale getirmeye yönelik belgeler. | 


## <a name="january-2020"></a>Ocak 2020

| Değişiklikler | Ayrıntılar |
| --- | --- |
| **Azure Kamu desteği** | Artık [Azure Kamu](https://azure.microsoft.com/global-infrastructure/government/) bulutunda barındırılan sanal MAKINELER Için SQL IaaS Aracısı uzantısına sahip SQL Server sanal makineleri kaydetmek mümkündür. | 
| &nbsp; | &nbsp; |

## <a name="2019"></a>2019

|Değişiklikler | Ayrıntılar |
 --- | --- |
| **Azure 'da ücretsiz DR çoğaltması** | [Yazılım güvencesi](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot:primaryr3)'niz varsa şirket içi SQL Server Örneğiniz için Azure 'da olağanüstü durum kurtarma için [ücretsiz bir pasif örnek](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure) barındırabilirsiniz. | 
| **Toplu SQL IaaS uzantı kaydı** | Artık [SQL IaaS Aracısı uzantısı](sql-server-iaas-agent-extension-automate-management.md)ile SQL Server sanal makinelere [toplu kayıt](sql-agent-extension-manually-register-vms-bulk.md) yapabilirsiniz. | 
|**Performans için iyileştirilmiş depolama yapılandırması** | Artık yeni bir SQL Server VM oluştururken [depolama yapılandırmanızı tamamen özelleştirebilirsiniz](storage-configuration.md#new-vms) . |
|**FCı için Premium dosya paylaşma** | Artık [depolama alanları doğrudan](failover-cluster-instance-storage-spaces-direct-manually-configure.md)orijinal yöntemi yerine [Premium dosya paylaşma](failover-cluster-instance-premium-file-share-manually-configure.md) kullanarak bir yük devretme kümesi örneği oluşturabilirsiniz. 
| **Azure adanmış ana bilgisayar** | [Azure adanmış ana bilgisayar](dedicated-host.md)üzerinde SQL Server VM çalıştırabilirsiniz. | 
| **Farklı bir bölgeye geçiş SQL Server VM** | [SQL Server VM bir bölgeden diğerine geçirmek](move-sql-vm-different-region.md)için Azure Site Recovery kullanın. |
|  **Yeni SQL IaaS yükleme modları** | SQL Server hizmetinin yeniden başlatılmasını önlemek için SQL Server IaaS uzantısını [hafif modda](sql-server-iaas-agent-extension-automate-management.md) yüklemek mümkündür.  |
| **SQL Server sürümü değişikliği** | Artık SQL Server VM için [sürüm özelliğini](change-sql-server-edition.md) değiştirebilirsiniz. |
| **SQL IaaS Aracısı uzantısında yapılan değişiklikler** | [SQL IaaS Aracısı uzantısına sahip SQL Server VM](sql-agent-extension-manually-register-single-vm.md) yeni SQL IaaS modlarını kullanarak kaydedebilirsiniz. Bu özellik [Windows Server 2008](sql-server-iaas-agent-extension-automate-management.md#management-modes) görüntülerini içerir.|
| **Azure Hibrit Avantajı kullanarak kendi lisans görüntülerini getirin** | Azure Marketi 'nden dağıtılan kendi lisans görüntülerini getir, şimdi de [Lisans türlerini Kullandıkça Öde olarak](licensing-model-azure-hybrid-benefit-ahb-change.md#remarks)değiştirebilir.| 
| **Azure portal yeni SQL Server VM yönetimi** | Artık Azure portal SQL Server VM yönetmenin bir yolu vardır. Daha fazla bilgi için bkz. [Azure portal SQL Server VM 'Leri yönetme](manage-sql-vm-portal.md).  | 
| **SQL Server 2008 ve 2008 R2 için genişletilmiş destek** | SQL Server 2008 ve SQL Server 2008 R2 desteğini bir Azure VM 'ye *olduğu gibi* geçirerek [genişletin](sql-server-2008-extend-end-of-support.md) . | 
| **Özel görüntü desteklenebilirliği** | Artık [SQL Server IaaS uzantısını](sql-server-iaas-agent-extension-automate-management.md#installation) özel işletim sistemi ve SQL Server görüntülerine yükleyebilirsiniz ve bu da [Esnek lisansın](licensing-model-azure-hybrid-benefit-ahb-change.md)sınırlı işlevlerini sunar. Özel görüntünüzü SQL IaaS Aracısı Uzantısı ile kaydederken, lisans türünü "AHUB" olarak belirtin. Aksi takdirde, kayıt başarısız olur. | 
| **Adlandırılmış örnek desteklenebilirliği** | Varsayılan örnek düzgün şekilde kaldırılmışsa, artık [SQL Server IaaS uzantısını](sql-server-iaas-agent-extension-automate-management.md#installation) adlandırılmış bir örnekle kullanabilirsiniz. | 
| **Portal geliştirmesi** | SQL Server VM dağıtmaya yönelik Azure portal deneyim, kullanılabilirliği iyileştirmeye yönelik olarak yeniden belirlenmiştir. Daha fazla bilgi için bkz. kısa [Başlangıç](sql-vm-create-portal-quickstart.md) ve daha kapsamlı [nasıl yapılır Kılavuzu](create-sql-vm-portal.md) SQL Server VM dağıtma.|
| **Portal geliştirme** | Bir SQL Server VM için lisans modelini, [Azure Portal](licensing-model-azure-hybrid-benefit-ahb-change.md#change-license-model)kullanarak kendi lisansınıza sahip olmak üzere Kullandıkça Öde için değiştirmek mümkündür.|
| **Azure CLı aracılığıyla SQL Server VM kullanılabilirlik grubu dağıtımının basitleştirmesi** | Artık bir kullanılabilirlik grubunu Azure 'da bir SQL Server VM dağıtmaya hiç zamankinden daha kolay. Windows Yük devretme kümesi, iç yük dengeleyici ve kullanılabilirlik grubu dinleyicilerini komut satırından oluşturmak için [Azure CLI](/cli/azure/sql/vm?view=azure-cli-2018-03-01-hybrid&preserve-view=true) 'yı kullanabilirsiniz. Daha fazla bilgi için bkz. Azure [CLI kullanarak bir Azure VM 'de SQL Server Için her zaman açık kullanılabilirlik grubu yapılandırma](./availability-group-az-commandline-configure.md). | 
| &nbsp; | &nbsp; |

## <a name="2018"></a>2018 

 Değişiklikler | Ayrıntılar |
| --- | --- |
|  **SQL Server kümesi için yeni kaynak sağlayıcısı** | Yeni bir kaynak sağlayıcısı (Microsoft. SqlVirtualMachine/SqlVirtualMachineGroups), Windows Yük devretme kümesinin meta verilerini tanımlar. Windows Server yük devretme kümesi (WSFC) hizmeti önyükleme ve VM 'yi kümeyle birleştiren *Sqlvirtualmachinegroups* 'a katılma SQL Server VM.  |
| **Azure hızlı başlangıç şablonları ile bir kullanılabilirlik grubu dağıtımının otomatik kurulumu** |Windows Yük devretme kümesi oluşturmak, bu kümeye SQL Server VM 'Leri birleştirmek, dinleyiciyi oluşturmak ve iki Azure hızlı başlangıç şablonunu kullanarak iç yük dengeleyiciyi yapılandırmak artık mümkündür. Daha fazla bilgi için bkz. Azure [Hızlı Başlangıç şablonlarını kullanarak bir Azure VM 'de SQL Server Için her zaman açık kullanılabilirlik grubu yapılandırma](availability-group-quickstart-template-configure.md). | 
| **SQL IaaS Aracısı uzantısına otomatik kayıt** | Bu aydan sonra dağıtılan SQL Server VM 'Ler, yeni SQL IaaS Aracısı Uzantısı ile otomatik olarak kaydedilir. Bu aya göre dağıtılan SQL Server sanal makinelerin yine de el ile kaydedilmesi gerekir. Daha fazla bilgi için bkz. [Azure 'da SQL Server sanal MAKINESINI SQL IaaS Aracısı uzantısıyla kaydetme](sql-agent-extension-manually-register-single-vm.md).|
|**Yeni SQL IaaS Aracısı uzantısı** |  Yeni bir kaynak sağlayıcısı (Microsoft. SqlVirtualMachine) SQL Server sanal makinelerinizin daha iyi yönetilmesini sağlar. VM 'lerinizi kaydetme hakkında daha fazla bilgi için bkz. [Azure 'da SQL Server sanal MAKINESINI SQL IaaS Aracısı uzantısıyla kaydetme](sql-agent-extension-manually-register-single-vm.md). |
|**Lisans modelini Değiştir** | Artık Azure CLı veya PowerShell kullanarak SQL Server VM için, kullanım başına ödeme ve kendi lisans modelleriniz arasında geçiş yapabilirsiniz. Daha fazla bilgi için bkz. [Azure 'da SQL Server sanal makinesi için lisans modelini değiştirme](licensing-model-azure-hybrid-benefit-ahb-change.md). | 
| &nbsp; | &nbsp; |

## <a name="additional-resources"></a>Ek kaynaklar

**Windows VM 'leri**:

* [Windows VM 'de SQL Server genel bakış](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Windows VM 'de SQL Server sağlama](create-sql-vm-portal.md)
* [Bir veritabanını Azure VM’deki SQL Server’a geçirme](migrate-to-vm-from-sql-server.md)
* [Azure sanal makinelerinde SQL Server için yüksek kullanılabilirlik ve olağanüstü durum kurtarma](business-continuity-high-availability-disaster-recovery-hadr-overview.md)
* [Azure sanal makinelerinde SQL Server için en iyi performans uygulamaları](performance-guidelines-best-practices.md)
* [Azure sanal makinelerinde SQL Server için uygulama desenleri ve geliştirme stratejileri](application-patterns-development-strategies.md)

**Linux VM 'leri**:

* [Linux VM 'de SQL Server genel bakış](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)
* [Linux sanal makinesinde SQL Server sağlama](../linux/sql-vm-create-portal-quickstart.md)
* [SSS (Linux)](../linux/frequently-asked-questions-faq.md)
* [Linux üzerinde SQL Server belgeleri](/sql/linux/sql-server-linux-overview)