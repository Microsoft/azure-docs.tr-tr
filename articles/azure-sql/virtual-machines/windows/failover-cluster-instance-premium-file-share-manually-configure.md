---
title: Premium dosya paylaşımıyla bir FCı oluşturma
description: Azure sanal makinelerinde SQL Server bir yük devretme kümesi örneği (FCı) oluşturmak için Premium dosya paylaşma (PFS) kullanın.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.custom: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/18/2020
ms.author: mathoma
ms.openlocfilehash: ddd25c605ef159bddfb8a9c7cb4d02ac7094c511
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482220"
---
# <a name="create-an-fci-with-a-premium-file-share-sql-server-on-azure-vms"></a>Premium dosya paylaşımıyla bir FCı oluşturma (Azure VM 'lerinde SQL Server)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Bu makalede, [Premium dosya paylaşımının](../../../storage/files/storage-how-to-create-file-share.md)kullanıldığı Azure sanal makinelerinde (vm) SQL Server yük devretme kümesi örneği (FCI) oluşturma işlemi açıklanmaktadır.

Premium dosya paylaşımları, Windows Server 2012 veya sonraki sürümlerde SQL Server 2012 veya sonraki bir sürümü için yük devretme kümesi örnekleriyle birlikte kullanılmak üzere tam olarak desteklenen Depolama Alanları Doğrudan (SSD) destekli, sürekli düşük gecikmeli dosya paylaşımlardır. Premium dosya paylaşımları size daha fazla esneklik sağlar, böylece herhangi bir kesinti olmadan dosya paylaşımını yeniden boyutlandırabilir ve ölçeklendirebilirsiniz.

Daha fazla bilgi edinmek için bkz. Azure VM 'lerde ve [küme en iyi uygulamalarında](hadr-cluster-best-practices.md) [SQL Server ile FCI](failover-cluster-instance-overview.md) 'ye genel bakış. 

## <a name="prerequisites"></a>Önkoşullar

Bu makaledeki yönergeleri tamamlamadan önce Şu durumda olmalıdır:

- Azure aboneliği.
- Hem Azure sanal makinelerinde hem de Active Directory nesne oluşturma izinlerine sahip olan bir hesap.
- Bir [kullanılabilirlik kümesinde](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set) veya farklı [kullanılabilirlik bölgelerinde](../../../virtual-machines/windows/create-portal-availability-zone.md#confirm-zone-for-managed-disk-and-ip-address) [hazırlanmış Iki veya daha fazla Microsoft Azure sanal makinesi](failover-cluster-instance-prepare-vm.md) .
- Veri dosyalarınız için veritabanınızın depolama kotasına bağlı olarak, kümelenmiş sürücü olarak kullanılacak bir [Premium dosya paylaşımıdır](../../../storage/files/storage-how-to-create-file-share.md) .
- En son [PowerShell](/powershell/azure/install-az-ps)sürümü. 

## <a name="mount-premium-file-share"></a>Premium dosya paylaşma bağlama

1. [Azure portalında](https://portal.azure.com) oturum açın. ve depolama hesabınıza gidin.
1. **Dosya hizmeti** altında **dosya paylaşımları** ' na gidin ve ardından SQL depolaması için kullanmak istediğiniz Premium dosya paylaşımını seçin.
1. Dosya paylaşımınızın bağlantı dizesini görüntülemek için **Bağlan** ' ı seçin.
1. Açılan listede, kullanmak istediğiniz sürücü harfini seçin ve ardından her iki kod bloğunu Not defteri 'ne kopyalayın.

   :::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/premium-file-storage-commands.png" alt-text="Her iki PowerShell komutunu da dosya paylaşımının Connect portalından Kopyala":::

1. SQL Server FCı 'nizin hizmet hesabı için kullanacağı hesapla SQL Server VM bağlanmak için Uzak Masaüstü Protokolü (RDP) kullanın.
1. Bir yönetim PowerShell komut konsolunu açın.
1. Daha önce portalda çalışırken kaydettiğiniz komutları çalıştırın.
1. Dosya Gezgini 'ni veya **Çalıştır** iletişim kutusunu (Windows + R 'yi seçin) kullanarak paylaşıma gidin. Ağ yolunu kullanın `\\storageaccountname.file.core.windows.net\filesharename` . Örneğin, `\\sqlvmstorageaccount.file.core.windows.net\sqlpremiumfileshare`

1. SQL veri dosyalarınızı içine yerleştirmek için yeni bağlı dosya paylaşımında en az bir klasör oluşturun.
1. Kümeye katılacak her bir SQL Server VM bu adımları yineleyin.

  > [!IMPORTANT]
  > - Veri ve günlük dosyaları için bu paylaşımın giriş/çıkış işlemi (ıOPS) ve alan kapasitesini kaydetmek üzere yedekleme dosyaları için ayrı bir dosya paylaşma kullanmayı düşünün. Yedekleme dosyaları için Premium veya standart dosya paylaşımından birini kullanabilirsiniz.
  > - Windows 2012 R2 veya daha önceki bir sürümü kullanıyorsanız, dosya paylaşma tanığı olarak kullanacağınız dosya paylaşımının bağlanması için aynı adımları izleyin. 
  > 


## <a name="add-windows-cluster-feature"></a>Windows küme özelliği Ekle

1. Yerel yöneticilerin üyesi olan ve Active Directory nesne oluşturma izni olan bir etki alanı hesabı kullanarak RDP ile ilk sanal makineye bağlanın. Yapılandırmanın geri kalanı için bu hesabı kullanın.

1. [Yük devretme kümelemesini her bir sanal makineye ekleyin](availability-group-manually-configure-prerequisites-tutorial.md#add-failover-clustering-features-to-both-sql-server-vms).

   Yük devretme kümelemesini kullanıcı arabiriminden yüklemek için her iki sanal makinede şunları yapın:
   1. **Sunucu Yöneticisi**, **Yönet**' i seçin ve ardından **rol ve Özellik Ekle**' yi seçin.
   1. **Rol ve özellik ekleme** Sihirbazı ' nda, **özellikleri seçerken** **İleri** ' yi seçin.
   1. **Özellikleri Seç** bölümünde **Yük Devretme Kümelemesi**' ni seçin. Tüm gerekli özellikleri ve yönetim araçlarını dahil edin. 
   1. **Özellik Ekle**' yi seçin.
   1. **İleri**' yi seçin ve sonra özellikleri yüklemek için **son** ' u seçin.

   PowerShell 'i kullanarak Yük Devretme Kümelemesi 'ni yüklemek için sanal makinelerden birindeki yönetici PowerShell oturumundan aşağıdaki betiği çalıştırın:

   ```powershell
   $nodes = ("<node1>","<node2>")
   Invoke-Command  $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools}
   ```

## <a name="validate-cluster"></a>Kümeyi Doğrula

Kümeyi Kullanıcı arabiriminde veya PowerShell kullanarak doğrulayın.

Kullanıcı arabirimini kullanarak kümeyi doğrulamak için sanal makinelerden birinde şunları yapın:

1. **Sunucu Yöneticisi** altında **Araçlar**' ı seçin ve **Yük devretme kümesi Yöneticisi**' i seçin.
1. **Yük devretme kümesi Yöneticisi** altında **eylem**' i seçin ve ardından **Yapılandırmayı Doğrula**' yı seçin.
1. **İleri**’yi seçin.
1. **Sunucu veya küme Seç** altında, her iki sanal makinenin adını da girin.
1. **Test seçenekleri** altında **yalnızca Seçdiğim Testleri Çalıştır**' ı seçin. 
1. **İleri**’yi seçin.
1. **Test seçimi** altında, **depolama** ve **depolama alanları doğrudan** dışındaki tüm testleri aşağıda gösterildiği gibi seçin:

   :::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/cluster-validation.png" alt-text="Küme doğrulama testlerini seçin":::

1. **İleri**’yi seçin.
1. **Onay** altında **İleri**' yi seçin.

**Yapılandırma doğrulama** Sihirbazı doğrulama testlerini çalıştırır.

PowerShell 'i kullanarak kümeyi doğrulamak için sanal makinelerden birindeki yönetici PowerShell oturumundan aşağıdaki betiği çalıştırın:

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
   ```

Kümeyi doğruladıktan sonra, yük devretme kümesini oluşturun.


## <a name="create-failover-cluster"></a>Yük devretme kümesi oluşturma

Yük devretme kümesini oluşturmak için şunlar gerekir:

- Küme düğümleri olacak sanal makinelerin adları.
- Yük devretme kümesi için bir ad.
- Yük devretme kümesi için bir IP adresi. Küme düğümleri ile aynı Azure sanal ağı ve alt ağı üzerinde kullanılmayan bir IP adresi kullanabilirsiniz.


# <a name="windows-server-2012---2016"></a>[Windows Server 2012-2016](#tab/windows2012)

Aşağıdaki PowerShell betiği Windows Server 2016 aracılığıyla Windows Server 2012 için bir yük devretme kümesi oluşturur. Betiği, düğümlerin adlarıyla (sanal makine adları) ve kullanılabilir bir IP adresini Azure sanal ağından güncelleştirin.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

# <a name="windows-server-2019"></a>[Windows Server 2019](#tab/windows2019)

Aşağıdaki PowerShell betiği Windows Server 2019 için bir yük devretme kümesi oluşturur.  Betiği, düğümlerin adlarıyla (sanal makine adları) ve kullanılabilir bir IP adresini Azure sanal ağından güncelleştirin.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```

Daha fazla bilgi için bkz. [Yük devretme kümesi: küme ağ nesnesi](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97).

---


## <a name="configure-quorum"></a>Çekirdeği yapılandırma

İşletmenizin ihtiyaçlarına en uygun olan çekirdek çözümünü yapılandırın. Bir [disk tanığı](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum), [bulut tanığı](/windows-server/failover-clustering/deploy-cloud-witness)veya [dosya paylaşma tanığı](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)yapılandırabilirsiniz. Daha fazla bilgi için bkz. [SQL Server VM 'Lerle çekirdek](hadr-cluster-best-practices.md#quorum). 


## <a name="test-cluster-failover"></a>Test kümesi yük devretmesi

Kümenizin yük devretmesini test edin. **Yük devretme kümesi Yöneticisi**, kümenize sağ tıklayın, **diğer eylemler**' i  >  **taşıyın çekirdek küme kaynağı**  >  **Seç düğümünü** seçin ve ardından kümenin diğer düğümünü seçin. Çekirdek küme kaynağını kümenin her düğümüne taşıyın ve ardından birincil düğüme geri taşıyın. Kümeyi her düğüme başarıyla taşıyabiliyorsanız SQL Server yüklemeye hazırsınız demektir.  

:::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/test-cluster-failover.png" alt-text="Çekirdek kaynağı diğer düğümlere taşıyarak küme yük devretmesini test etme":::


## <a name="create-sql-server-fci"></a>SQL Server FCı oluştur

Yük devretme kümesini yapılandırdıktan sonra, SQL Server FCı 'yi oluşturabilirsiniz.

1. RDP kullanarak ilk sanal makineye bağlanın.

1. **Yük devretme kümesi Yöneticisi**, tüm çekirdek küme kaynaklarının ilk sanal makinede olduğundan emin olun. Gerekirse, tüm kaynakları bu sanal makineye taşıyın.

1. Yükleme medyasını bulun. Sanal makine Azure Marketi görüntülerinden birini kullanıyorsa medya konumunda bulunur `C:\SQLServer_<version number>_Full` . 

1. **Kurulum 'u** seçin.

1. **SQL Server Yükleme Merkezi**'nde **yükleme**' yi seçin.

1. **Yeni SQL Server yük devretme kümesi yükleme**' yi seçin ve ardından sihirbazdaki yönergeleri izleyerek SQL Server FCI 'yi yükleme.

   FCı veri dizinlerinin Premium dosya paylaşımında olması gerekir. Paylaşımın tam yolunu şu biçimde girin: `\\storageaccountname.file.core.windows.net\filesharename\foldername` . Veri dizini olarak bir dosya sunucusu belirtmiş olduğunu söyleyen bir uyarı görüntülenir. Bu uyarı beklenmektedir. Dosya paylaşımından kalıcı hale geldiğinde, sanal makineye RDP aracılığıyla erişmek için kullandığınız kullanıcı hesabının, SQL Server hizmetin olası hatalardan kaçınmak için kullandığı hesapla aynı olduğundan emin olun.

   :::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/use-file-share-as-data-directories.png" alt-text="Dosya paylaşımının SQL veri dizinleri olarak kullanılması":::

1. Sihirbazdaki adımları tamamladıktan sonra, kurulum ilk düğümde bir SQL Server FCı yükler.

1. Kurulum, ilk düğümde FCı 'yı yükledikten sonra, RDP kullanarak ikinci düğüme bağlanın.

1. **SQL Server yükleme merkezini** açın ve ardından **yükleme**' yi seçin.

1. **SQL Server yük devretme kümesine düğüm Ekle**' yi seçin. SQL Server yüklemek ve sunucuyu FCı 'ye eklemek için sihirbazdaki yönergeleri izleyin.

   >[!NOTE]
   >SQL Server ile bir Azure Marketi Galeri görüntüsü kullandıysanız, görüntüye SQL Server Araçlar eklenmiştir. Bu görüntülerden birini kullanmıyorsanız, SQL Server araçlarını ayrı olarak yükleyebilirsiniz. Daha fazla bilgi için bkz. [Download SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms).

1. SQL Server yük devretme kümesi örneğine eklemek istediğiniz diğer düğümlerde bu adımları yineleyin. 

## <a name="register-with-the-sql-vm-rp"></a>SQL VM RP ile kaydolun

SQL Server VM portaldan yönetmek için, SQL IaaS Aracısı uzantısı 'na (RP) [basit yönetim modunda](sql-agent-extension-manually-register-single-vm.md#lightweight-management-mode)kaydedin, şu anda yalnızca FCI ile desteklenen tek mod ve Azure vm 'lerinde SQL Server. 

PowerShell ile hafif modda bir SQL Server VM kaydetme (-LicenseType `PAYG` ya da olabilir `AHUB` ):

```powershell-interactive
# Get the existing compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
         
# Register SQL VM with 'Lightweight' SQL IaaS agent
New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
   -LicenseType ???? -SqlManagementType LightWeight  
```

## <a name="configure-connectivity"></a>Bağlantı yapılandırma 

Trafiği geçerli birincil düğüme uygun bir şekilde yönlendirmek için, ortamınız için uygun olan bağlantı seçeneğini yapılandırın. [Azure yük dengeleyici](failover-cluster-instance-vnn-azure-load-balancer-configure.md) oluşturabilir veya SQL Server 2019 CU2 uygulamazsanız (veya üzeri) ve Windows Server 2016 (veya üzeri) kullanıyorsanız, bunun yerine [dağıtılmış ağ adı](failover-cluster-instance-distributed-network-name-dnn-configure.md) özelliğini kullanabilirsiniz. 

Küme bağlantı seçenekleri hakkında daha fazla bilgi için bkz. [Azure VM 'lerinde SQL Server HADR bağlantıları yönlendirme](hadr-cluster-best-practices.md#connectivity). 

## <a name="limitations"></a>Sınırlamalar

- Microsoft Dağıtılmış İşlem Düzenleyicisi (MSDTC), Windows Server 2016 ve önceki sürümlerde desteklenmez. 
- Premium dosya paylaşımıyla yük devretme kümesi için FILESTREAM desteklenmez. FILESTREAM kullanmak için, [depolama alanları doğrudan](failover-cluster-instance-storage-spaces-direct-manually-configure.md) veya [Azure Paylaşılan disklerini](failover-cluster-instance-azure-shared-disks-manually-configure.md) kullanarak kümenizi dağıtın.
- Yalnızca [hafif yönetim modunda](sql-server-iaas-agent-extension-automate-management.md#management-modes) SQL IaaS Aracısı Uzantısı ile kaydolma desteklenir. 
- Veritabanı anlık görüntüleri, [seyrek dosya sınırlamaları nedeniyle Azure dosyaları](/rest/api/storageservices/features-not-supported-by-the-azure-file-service)ile Şu anda desteklenmiyor.  

## <a name="next-steps"></a>Sonraki adımlar

Daha önce yapmadıysanız, bir [sanal ağ adı ve bir Azure yük dengeleyici](failover-cluster-instance-vnn-azure-load-balancer-configure.md) veya [dağıtılmış ağ adı (DNN)](failover-cluster-instance-distributed-network-name-dnn-configure.md)ile FCI 'nize bağlantı yapılandırın. 


Premium dosya paylaşımları sizin için uygun FCı depolama çözümü değilse, [Azure Paylaşılan diskleri](failover-cluster-instance-azure-shared-disks-manually-configure.md) veya [depolama alanları doğrudan](failover-cluster-instance-storage-spaces-direct-manually-configure.md) kullanarak FCI 'nizi oluşturmayı düşünün. 

Daha fazla bilgi edinmek için bkz. Azure VM 'lerde SQL Server ve [küme yapılandırması en iyi yöntemleri](hadr-cluster-best-practices.md) [ile FCI](failover-cluster-instance-overview.md) 'ye genel bakış. 

Daha fazla bilgi için bkz. 
- [Windows küme teknolojileri](/windows-server/failover-clustering/failover-clustering-overview)   
- [SQL Server yük devretme kümesi örnekleri](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
