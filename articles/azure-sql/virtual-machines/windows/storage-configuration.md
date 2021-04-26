---
title: SQL Server VM 'Ler için depolamayı yapılandırma | Microsoft Docs
description: Bu konu, Azure 'un sağlama sırasında (Azure Resource Manager dağıtım modeli) SQL Server VM 'Ler için depolamayı nasıl yapılandırdığını açıklamaktadır. Ayrıca, mevcut SQL Server sanal makinelerinize yönelik depolamayı nasıl yapılandırabileceğinizi açıklar.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.assetid: 169fc765-3269-48fa-83f1-9fe3e4e40947
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/26/2019
ms.author: mathoma
ms.openlocfilehash: 982bd9239c5e95c9b7af09b5f54c5a09067ca7c6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105565435"
---
# <a name="configure-storage-for-sql-server-vms"></a>SQL Server VM 'Ler için depolamayı yapılandırma
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Bu makalede, Azure sanal makinelerinde (VM) SQL Server depolama alanınızı nasıl yapılandıracağınız öğretilir.

Market görüntüleri aracılığıyla dağıtılan SQL Server VM 'Ler, dağıtım sırasında değiştirilebilen varsayılan [depolama en iyi uygulamalarını](performance-guidelines-best-practices-storage.md) otomatik olarak izler. Bu yapılandırma ayarlarından bazıları dağıtımdan sonra değiştirilebilir. 


## <a name="prerequisites"></a>Önkoşullar

Otomatik depolama yapılandırma ayarlarını kullanmak için, sanal makineniz aşağıdaki özellikleri gerektirir:

* [SQL Server Gallery görüntüsüyle](sql-server-on-azure-vm-iaas-what-is-overview.md#payasyougo) sağlandı veya [SQL IaaS uzantısına]()kaydedilir.
* [Kaynak Yöneticisi dağıtım modelini](../../../azure-resource-manager/management/deployment-models.md)kullanır.
* [Premium SSD 'ler](../../../virtual-machines/disks-types.md)kullanır.

## <a name="new-vms"></a>Yeni VM 'Ler

Aşağıdaki bölümlerde, yeni SQL Server sanal makineler için depolamanın nasıl yapılandırılacağı açıklanır.

### <a name="azure-portal"></a>Azure portalı

SQL Server Galeri görüntüsü kullanarak bir Azure VM sağlarken, **SQL Server ayarları** sekmesinde **yapılandırmayı Değiştir** ' i seçerek performans için iyileştirilmiş depolama yapılandırması sayfasını açın. Değerleri varsayılan olarak bırakabilir ya da iş yükünüze göre gereksinimlerinize en uygun disk yapılandırma türünü değiştirebilirsiniz. 

![SQL Server ayarları sekmesini ve yapılandırma Değiştir seçeneğini vurgulayan ekran görüntüsü.](./media/storage-configuration/sql-vm-storage-configuration-provisioning.png)

**Depolama iyileştirmesi** altında SQL Server dağıttığınız iş yükünün türünü seçin. **Genel** iyileştirme seçeneğiyle, varsayılan olarak en fazla 5000 IOPS içeren bir veri diskine sahip olursunuz ve bu sürücüyü verileriniz, işlem günlüğü ve tempdb depolaması için kullanacaksınız. 

**İşlemsel işleme** (OLTP) veya **veri depolama** alanı seçildiğinde veriler için ayrı bir disk, işlem günlüğü için ayrı bir disk oluşturulur ve tempdb için yerel SSD kullanılır. **İşlemsel işleme** ve **veri depolama** arasında bir depolama farkı yoktur, ancak [Stripe yapılandırmanızı ve izleme bayraklarını](#workload-optimization-settings)değiştirir. Premium Depolama ' yı seçtiğinizde, veri sürücüsü için önbelleğe alma özelliği *ReadOnly* olarak ayarlanır ve günlük sürücü için [SQL Server VM performans En Iyi uygulamalarına](performance-guidelines-best-practices.md)göre *yoktur* . 

![Sağlama sırasında depolama yapılandırması SQL Server VM](./media/storage-configuration/sql-vm-storage-configuration.png)

Disk yapılandırması tamamen özelleştirilebilir olduğundan, SQL Server VM iş yükünüz için gereken depolama topolojisini, disk türünü ve IOPS 'yi yapılandırabilirsiniz. Ayrıca, SQL Server VM desteklenen bölgelerden birinde (Doğu ABD 2, Güneydoğu Asya ve Kuzey Avrupa) ve [aboneliğiniz için Ultra diskler](../../../virtual-machines/disks-enable-ultra-ssd.md)etkinleştirdiyseniz, **disk türü** Için bir seçenek olarak UltraSSD (Önizleme) özelliğini kullanabilirsiniz.  

Ayrıca, diskler için önbelleğe alma özelliğini ayarlayabilirsiniz. Azure VM 'Leri, [Premium disklerle](../../../virtual-machines/disks-types.md#premium-ssd)kullanıldığında [blob önbelleği](../../../virtual-machines/premium-storage-performance.md#disk-caching) adlı çok katmanlı bir önbelleğe alma teknolojisine sahiptir. Blob önbelleği, önbelleğe alma için sanal makine RAM ve yerel SSD 'nin bir birleşimini kullanır. 

Premium SSD için disk önbelleğe alma *ReadOnly*, *ReadWrite* veya *none* olabilir. 

- *ReadOnly* önbelleğe alma, Premium depolamada depolanan SQL Server veri dosyaları için oldukça faydalıdır. *Salt* okunur önbellek, düşük okuma gecikmesi, yüksek okuma IOPS ve aktarım hızı gibi IŞLEMLERI, VM belleği ve yerel SSD içindeki önbellekten gerçekleştirilen okuma işlemleri, önbellekten gerçekleştirilir. Bu okumalar, Azure Blob depolamadan olan veri diskinden okumalarından çok daha hızlıdır. Premium Depolama, önbellekten sunulan okuma sayısını disk ıOPS ve aktarım hızı ile saymaz. Bu nedenle, uygun olan toplam ıOPS ve aktarım hızı elde edebilirsiniz. 
- Günlük dosyası sıralı olarak yazıldığı ve *salt okunur* önbelleğe alma özelliğinden yararlanmadığı için, SQL Server günlük dosyası barındıran diskler için *hiçbiri* önbellek yapılandırması kullanılmamalıdır. 
- SQL Server, *ReadWrite* önbelleği ile veri tutarlılığını desteklemediğinden SQL Server dosyaları barındırmak için *ReadWrite* önbelleği kullanılmamalıdır. *ReadOnly* blob önbelleğinin çöp kapasitesini yazar ve yazmalar *salt okunur* blob önbelleği katmanlarına gitmez biraz artar. 


   > [!TIP]
   > Depolama yapılandırmanızın seçili VM boyutu tarafından uygulanan kısıtlamalarla eşleştiğinden emin olun. VM boyutunun performans üst sınırını aşan depolama parametrelerinin seçilmesi uyarı olarak sonuçlanır: `The desired performance might not be reached due to the maximum virtual machine disk performance cap` . Disk türünü değiştirerek IOPS 'yi azaltın veya VM boyutunu artırarak performans üst sınırı değerini artırın. Bu, sağlamayı durdurmaz. 


Azure, seçimlerinize bağlı olarak VM 'yi oluşturduktan sonra aşağıdaki depolama yapılandırma görevlerini gerçekleştirir:

* Premium SSD 'leri oluşturur ve sanal makineye ekler.
* SQL Server için erişilebilir olacak veri disklerini yapılandırır.
* Veri disklerini, belirtilen boyut ve performans (ıOPS ve aktarım hızı) gereksinimlerine bağlı olarak bir depolama havuzunda yapılandırır.
* Depolama havuzunu sanal makinede yeni bir sürücüyle ilişkilendirir.
* Bu yeni sürücüyü, belirtilen iş yükü türüne (veri ambarı, Işlemsel işleme veya genel) göre iyileştirir.

Azure portal SQL Server VM oluşturma hakkında tam bir anlatım için [sağlama öğreticisine](../../../azure-sql/virtual-machines/windows/create-sql-vm-portal.md)bakın.

### <a name="resource-manager-templates"></a>Resource Manager şablonları

Aşağıdaki Kaynak Yöneticisi şablonlarını kullanıyorsanız, varsayılan olarak, depolama havuzu yapılandırması olmadan iki Premium veri diski eklenir. Ancak, bu şablonları, sanal makineye bağlı olan Premium veri disklerinin sayısını değiştirecek şekilde özelleştirebilirsiniz.

* [Otomatik yedekleme ile VM oluşturma](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autobackup)
* [Otomatik düzeltme eki uygulama ile VM oluşturma](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autopatching)
* [AKV tümleştirmesi ile VM oluşturma](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-keyvault)

### <a name="quickstart-template"></a>Hızlı Başlangıç şablonu

Depolama iyileştirmesi kullanarak bir SQL Server VM dağıtmak için aşağıdaki hızlı başlangıç şablonunu kullanabilirsiniz. 

* [Depolama iyileştirmesi ile VM oluşturma](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-new-storage/)
* [UltraSSD kullanarak VM oluşturma](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-new-storage-ultrassd)

## <a name="existing-vms"></a>Mevcut VM 'Ler

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Mevcut SQL Server VM 'Ler için Azure portal bazı depolama ayarlarını değiştirebilirsiniz. [SQL sanal makineleri kaynağını](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource)açın ve **genel bakış**' ı seçin. SQL Server genel bakış sayfası, sanal makinenizin geçerli depolama kullanımını gösterir. SANAL makinenizde bulunan tüm sürücüler bu grafikte görüntülenir. Her sürücü için, depolama alanı dört bölümde görüntülenir:

* SQL verileri
* SQL günlüğü
* Diğer (SQL depolama olmayan)
* Kullanılabilir

Depolama ayarlarını değiştirmek için **Ayarlar** altında **Yapılandır** ' ı seçin. 

![Yapılandırma seçeneğini ve depolama kullanımı bölümünü vurgulayan ekran görüntüsü.](./media/storage-configuration/sql-vm-storage-configuration-existing.png)

SQL Server VM oluşturma işlemi sırasında yapılandırılmış sürücüler için disk ayarlarını değiştirebilirsiniz. **Sürücüyü Genişlet** ' i seçtiğinizde sürücü değiştirme sayfası açılır ve bu da disk türünü değiştirmenize ve ek diskler eklemenize olanak tanır. 

![Mevcut SQL Server VM için depolamayı yapılandırma](./media/storage-configuration/sql-vm-storage-extend-drive.png)


## <a name="automated-changes"></a>Otomatik değişiklikler

Bu bölümde, Azure 'un Azure portal SQL Server VM sağlama veya yapılandırma sırasında otomatik olarak gerçekleştirdiği depolama yapılandırması değişikliklerine yönelik bir başvuru sağlanmaktadır.

* Azure, VM 'nizden seçilen depolama alanından bir depolama havuzu yapılandırır. Bu konunun sonraki bölümünde, depolama havuzu yapılandırması hakkında ayrıntılı bilgi verilmektedir.
* Otomatik depolama yapılandırması her zaman [Premium SSDs](../../../virtual-machines/disks-types.md) P30 veri disklerini kullanır. Sonuç olarak, seçtiğiniz terabaytlık ve sanal makinenize bağlı veri disklerinin sayısı arasında bir 1:1 eşlemesi vardır.

Fiyatlandırma bilgileri için **disk depolama** sekmesindeki [Depolama fiyatlandırması](https://azure.microsoft.com/pricing/details/storage) sayfasına bakın.

### <a name="creation-of-the-storage-pool"></a>Depolama havuzu oluşturma

Azure, SQL Server VM 'lerde depolama havuzu oluşturmak için aşağıdaki ayarları kullanır.

| Ayar | Değer |
| --- | --- |
| Şerit boyutu |256 KB (veri ambarı); 64 KB (Işlem) |
| Disk boyutları |1 TB her |
| Önbellek |Okuma |
| Ayırma boyutu |64 KB NTFS ayırma birimi boyutu |
| Kurtarma | Basit kurtarma (dayanıklılık yok) |
| Sütun sayısı |8<sup>1</sup> adede kadar veri diski sayısı |


<sup>1</sup> depolama havuzu oluşturulduktan sonra, depolama havuzundaki sütun sayısını değiştiremezsiniz.


### <a name="workload-optimization-settings"></a>İş yükü iyileştirme ayarları

Aşağıdaki tabloda, kullanılabilir üç iş yükü türü seçeneği ve bunların karşılık gelen iyileştirmeleri açıklanmaktadır:

| İş yükü türü | Description | İyileştirmeler |
| --- | --- | --- |
| **Genel** |Çoğu iş yüklerini destekleyen varsayılan ayar |Yok |
| **İşlemsel işleme** |Geleneksel veritabanı OLTP iş yükleri için depolamayı iyileştirir |İzleme bayrağı 1117<br/>İzleme bayrağı 1118 |
| **Veri depolama** |Analitik ve raporlama iş yükleri için depolamayı iyileştirir |İzleme bayrağı 610<br/>İzleme bayrağı 1117 |

> [!NOTE]
> Yalnızca depolama yapılandırması adımında bir SQL Server sanal makine sağladığınızda iş yükü türünü belirtebilirsiniz.

## <a name="enable-caching"></a>Enable caching 

Önbellek ilkesini disk düzeyinde değiştirin. Azure portal, [PowerShell](/powershell/module/az.compute/set-azvmdatadisk)veya [Azure CLI](/cli/azure/vm/disk)'yı kullanarak bunu yapabilirsiniz. 

Azure portal önbelleğe alma ilkenizi değiştirmek için şu adımları izleyin:

1. SQL Server hizmetinizi durdurun. 
1. [Azure portal](https://portal.azure.com) oturum açın. 
1. Sanal makinenize gidin, **Ayarlar** altında **diskler** ' i seçin. 
   
   ![Azure portal VM disk yapılandırması dikey penceresini gösteren ekran görüntüsü.](./media/storage-configuration/disk-in-portal.png)

1. Açılır listeden diskinizin uygun önbelleğe alma ilkesini seçin. 

   ![Azure portal disk önbelleğe alma ilkesi yapılandırmasını gösteren ekran görüntüsü.](./media/storage-configuration/azure-disk-config.png)

1. Değişiklik geçerli olduktan sonra, SQL Server VM yeniden başlatın ve SQL Server hizmetini başlatın. 


## <a name="enable-write-accelerator"></a>Yazma Hızlandırıcısı etkinleştir

Yazma hızlandırma yalnızca, a serisi sanal makineler (VM) için kullanılabilen bir disk özelliğidir. Yazma hızlandırmasının amacı, yüksek hacimli görev açısından kritik OLTP iş yükleri veya veri ambarı ortamları nedeniyle tek basamaklı g/ç gecikme süresi gerektiğinde Azure Premium depolamada yapılan yazma işlemleri için g/ç gecikme süresini artırmaktır. 

Tüm SQL Server etkinliğini durdurun ve yazma hızlandırma ilkenizde değişiklik yapmadan önce SQL Server hizmetini kapatın. 

Disklerinizin şeritli olması halinde her bir disk için yazma hızlandırmasını tek tek etkinleştirin ve Azure VM 'niz herhangi bir değişiklik yapılmadan önce kapatılmalıdır. 

Azure portal kullanarak yazma hızlandırmasını etkinleştirmek için şu adımları izleyin:

1. SQL Server hizmetinizi durdurun. Disklerinizin şeritli olması durumunda sanal makineyi kapatın. 
1. [Azure portal](https://portal.azure.com) oturum açın. 
1. Sanal makinenize gidin, **Ayarlar** altında **diskler** ' i seçin. 
   
   ![Azure portal VM disk yapılandırması dikey penceresini gösteren ekran görüntüsü.](./media/storage-configuration/disk-in-portal.png)

1. Açılır listeden diskinizin **yazma Hızlandırıcısı** önbellek seçeneğini belirleyin. 

   ![Yazma Hızlandırıcısı önbellek ilkesini gösteren ekran görüntüsü.](./media/storage-configuration/write-accelerator.png)

1. Değişiklik geçerli olduktan sonra, sanal makineyi ve SQL Server hizmetini başlatın. 

## <a name="disk-striping"></a>Disk şeridi

Daha fazla verimlilik için, ek veri diskleri ekleyebilir ve disk şeritleme kullanabilirsiniz. Veri disklerinin sayısını öğrenmek için, log ve tempdb dahil olmak üzere SQL Server veri dosyalarınız için gereken aktarım hızını ve bant genişliğini çözümleyin. Aktarım hızı ve bant genişliği sınırları VM boyutuna göre farklılık gösterir. Daha fazla bilgi için bkz. [VM boyutu](../../../virtual-machines/sizes.md)


* Windows 8/Windows Server 2012 veya üzeri için, [depolama alanlarını](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831739(v=ws.11)) aşağıdaki yönergelerle kullanın:

  1. Bölüm hatalı hizalaması nedeniyle performans etkisini önlemek için Interleave (Stripe boyutu) 64 KB (65.536 bayt) olarak ayarlayın. Bunun PowerShell ile ayarlanması gerekir.

  2. Sütun sayısını ayarla = fiziksel disk sayısı. 8 ' den fazla disk yapılandırırken PowerShell kullanın (Sunucu Yöneticisi Kullanıcı arabirimi).

Örneğin, aşağıdaki PowerShell, ayırma boyutu 64 KB ile yeni bir depolama havuzu ve depolama havuzundaki fiziksel disk miktarına eşit olan sütun sayısını oluşturur:

  ```powershell
  $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}
  
  New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" `
      -PhysicalDisks $PhysicalDisks | New- VirtualDisk -FriendlyName "DataFiles" `
      -Interleave 65536 -NumberOfColumns $PhysicalDisks .Count -ResiliencySettingName simple `
      –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter `
      -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" `
      -AllocationUnitSize 65536 -Confirm:$false 
  ```

  * Windows 2008 R2 veya önceki sürümlerde, dinamik diskleri (işletim sistemi şeritli birimler) kullanabilir ve şerit boyutu her zaman 64 KB 'tır. Bu seçenek, Windows 8/Windows Server 2012 itibarıyla kullanım dışıdır. Daha fazla bilgi için bkz. [sanal disk hizmetindeki Destek bildirisi Windows Storage YÖNETIM API 'sine geçiyor](https://docs.microsoft.com/windows/win32/w8cookbook/vds-is-transitioning-to-wmiv2-based-windows-storage-management-api).
 
  * [SQL Server yük devretme kümesi örnekleriyle](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/failover-cluster-instance-storage-spaces-direct-manually-configure) [depolama alanları doğrudan (S2D)](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm) kullanıyorsanız, tek bir havuz yapılandırmanız gerekir. Bu tek havuzda farklı birimler oluşturulabilse de, hepsi aynı önbelleğe alma ilkesi gibi aynı özellikleri paylaşır.
 
  * Yük beklentilerinize göre depolama havuzunuzun ilişkili disk sayısını belirleme. Farklı VM boyutlarının farklı sayıda ekli veri diskine izin verdiğini aklınızda bulundurun. Daha fazla bilgi için bkz. [sanal makineler Için boyutlar](../../../virtual-machines/sizes.md?toc=/azure/virtual-machines/windows/toc.json).


## <a name="next-steps"></a>Sonraki adımlar

Azure VM 'lerinde SQL Server çalıştırmaya ilişkin diğer konular için bkz. [Azure sanal makinelerinde SQL Server](sql-server-on-azure-vm-iaas-what-is-overview.md).
