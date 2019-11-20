---
title: SQL Server VM 'Leri için depolama yapılandırması | Microsoft Docs
description: Bu konu, Azure 'un sağlama sırasında (Kaynak Yöneticisi dağıtım modeli) SQL Server VM 'Ler için depolamayı nasıl yapılandırdığını açıklamaktadır. Ayrıca, mevcut SQL Server sanal makinelerinize yönelik depolamayı nasıl yapılandırabileceğinizi açıklar.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.assetid: 169fc765-3269-48fa-83f1-9fe3e4e40947
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/05/2017
ms.author: mathoma
ms.openlocfilehash: a91098d06f481afaae75eb497d5a076c3eb42c07
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72896946"
---
# <a name="storage-configuration-for-sql-server-vms"></a>SQL Server VM 'Ler için depolama yapılandırması

Azure 'da bir SQL Server sanal makine görüntüsü yapılandırdığınızda, Portal depolama yapılandırmanızı otomatikleştirmenize yardımcı olur. Bu, depolama alanının sanal makineye iliştirilmesi, bu depolamanın SQL Server için erişilebilir hale getirilmesi ve belirli performans gereksinimlerinizi iyileştirmek üzere yapılandırılmasını içerir.

Bu konu, Azure 'un sağlama ve mevcut VM 'Ler için SQL Server sanal makinelerinize yönelik depolamayı nasıl yapılandırdığını açıklamaktadır. Bu yapılandırma, SQL Server çalıştıran Azure sanal makineleri için [en iyi performans uygulamalarına](virtual-machines-windows-sql-performance.md) dayalıdır.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Önkoşullar

Otomatik depolama yapılandırma ayarlarını kullanmak için, sanal makineniz aşağıdaki özellikleri gerektirir:

* [SQL Server Galeri görüntüsü](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo)ile sağlandı.
* [Kaynak Yöneticisi dağıtım modelini](../../../azure-resource-manager/resource-manager-deployment-model.md)kullanır.
* [Premium SSD 'ler](../disks-types.md)kullanır.

## <a name="new-vms"></a>Yeni VM 'Ler

Aşağıdaki bölümlerde, yeni SQL Server sanal makineler için depolamanın nasıl yapılandırılacağı açıklanır.

### <a name="azure-portal"></a>Azure portalı

SQL Server Galeri görüntüsü kullanarak bir Azure VM sağlarken, **SQL Server ayarları** sekmesinde **yapılandırmayı Değiştir** ' i seçerek performans için iyileştirilmiş depolama yapılandırması sayfasını açın. Değerleri varsayılan olarak bırakabilir ya da iş yükünüze göre gereksinimlerinize en uygun disk yapılandırma türünü değiştirebilirsiniz. 

![Sağlama sırasında depolama yapılandırması SQL Server VM](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-provisioning.png)

**Depolama iyileştirmesi**altında SQL Server dağıttığınız iş yükünün türünü seçin. **Genel** iyileştirme seçeneğiyle, varsayılan olarak en fazla 5000 IOPS içeren bir veri diskine sahip olursunuz ve bu sürücüyü verileriniz, işlem günlüğü ve tempdb depolaması için kullanacaksınız. **İşlemsel işleme** (OLTP) veya **veri depolama** alanı seçildiğinde veriler için ayrı bir disk, işlem günlüğü için ayrı bir disk oluşturulur ve tempdb için yerel SSD kullanılır. **İşlemsel işleme** ve **veri depolama**arasında bir depolama farkı yoktur, ancak [Stripe yapılandırmanızı ve izleme bayraklarını](#workload-optimization-settings)değiştirir. Premium Depolama ' yı seçtiğinizde, veri sürücüsü için önbelleğe alma özelliği *ReadOnly* olarak ayarlanır ve günlük sürücü için [SQL Server VM performans En Iyi uygulamalarına](virtual-machines-windows-sql-performance.md)göre *yoktur* . 

![Sağlama sırasında depolama yapılandırması SQL Server VM](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration.png)

Disk yapılandırması tamamen özelleştirilebilir olduğundan, SQL Server VM iş yükünüz için gereken depolama topolojisini, disk türünü ve IOPS 'yi yapılandırabilirsiniz. Ayrıca, SQL Server VM desteklenen bölgelerden birinde (Doğu ABD 2, Güneydoğu Asya ve Kuzey Avrupa) ve [aboneliğiniz için Ultra diskler](/azure/virtual-machines/windows/disks-enable-ultra-ssd)etkinleştirdiyseniz, **disk türü** Için bir seçenek olarak UltraSSD (Önizleme) özelliğini kullanabilirsiniz.  

Ayrıca, diskler için önbelleğe alma özelliğini ayarlayabilirsiniz. Azure VM 'Leri, [Premium disklerle](/azure/virtual-machines/windows/disks-types#premium-ssd)kullanıldığında [blob önbelleği](/azure/virtual-machines/windows/premium-storage-performance#disk-caching) adlı çok katmanlı bir önbelleğe alma teknolojisine sahiptir. Blob önbelleği, önbelleğe alma için sanal makine RAM ve yerel SSD 'nin bir birleşimini kullanır. 

Premium SSD için disk önbelleğe alma *ReadOnly*, *ReadWrite* veya *none*olabilir. 

- *ReadOnly* önbelleğe alma, Premium depolamada depolanan SQL Server veri dosyaları için oldukça faydalıdır. *ReadOnly* önbelleğe alma işlemi, düşük okuma gecikmesi, yüksek okuma IOPS ve aktarım hızı olarak, VM belleği ve yerel SSD içinde işletim sistemi olan önbellekten, okuma işlemleri yapar. Bu okumalar, Azure Blob depolamadan olan veri diskinden okumalarından çok daha hızlıdır. Premium Depolama, önbellekten sunulan okuma sayısını disk ıOPS ve aktarım hızı ile saymaz. Bu nedenle, uygulamanız toplam ıOPS iş verimini daha yüksek olarak elde edebilir. 
- Günlük dosyası sıralı olarak yazıldığı ve *salt okunur* önbelleğe alma özelliğinden yararlanmadığı için, SQL Server günlük dosyası barındıran diskler için *hiçbiri* önbellek yapılandırması kullanılmamalıdır. 
- SQL Server, *ReadWrite* önbelleği ile veri tutarlılığını desteklemediğinden SQL Server dosyaları barındırmak için *ReadWrite* önbelleği kullanılmamalıdır. *ReadOnly* blob önbelleğinin çöp kapasitesini yazar ve yazmalar *salt okunur* blob önbelleği katmanlarına gitmez biraz artar. 


   > [!TIP]
   > Depolama yapılandırmanızın seçili VM boyutu tarafından uygulanan kısıtlamalarla eşleştiğinden emin olun. VM boyutunun performans üst sınırını aşan depolama parametrelerinin seçilmesi şu hatayla sonuçlanır: `The desired performance might not be reached due to the maximum virtual machine disk performance cap.`. Disk türünü değiştirerek IOPS 'yi azaltın veya VM boyutunu artırarak performans üst sınırı değerini artırın. 


Azure, seçimlerinize bağlı olarak VM 'yi oluşturduktan sonra aşağıdaki depolama yapılandırma görevlerini gerçekleştirir:

* Premium SSD 'leri oluşturur ve sanal makineye ekler.
* SQL Server için erişilebilir olacak veri disklerini yapılandırır.
* Veri disklerini, belirtilen boyut ve performans (ıOPS ve aktarım hızı) gereksinimlerine bağlı olarak bir depolama havuzunda yapılandırır.
* Depolama havuzunu sanal makinede yeni bir sürücüyle ilişkilendirir.
* Bu yeni sürücüyü, belirtilen iş yükü türüne (veri ambarı, Işlemsel işleme veya genel) göre iyileştirir.

Azure 'un depolama ayarlarını yapılandırma hakkında daha fazla bilgi için [depolama yapılandırması bölümüne](#storage-configuration)bakın. Azure portal SQL Server VM oluşturma hakkında tam bir anlatım için [sağlama öğreticisine](virtual-machines-windows-portal-sql-server-provision.md)bakın.

### <a name="resource-manage-templates"></a>Kaynak yönetme şablonları

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

Mevcut SQL Server VM 'Ler için Azure portal bazı depolama ayarlarını değiştirebilirsiniz. [SQL sanal makineleri kaynağını](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource)açın ve **genel bakış**' ı seçin. SQL Server genel bakış sayfası, sanal makinenizin geçerli depolama kullanımını gösterir. SANAL makinenizde bulunan tüm sürücüler bu grafikte görüntülenir. Her sürücü için, depolama alanı dört bölümde görüntülenir:

* SQL verileri
* SQL günlüğü
* Diğer (SQL depolama olmayan)
* Kullanılabilir

Depolama ayarlarını değiştirmek için **Ayarlar**altında **Yapılandır** ' ı seçin. 

![Mevcut SQL Server VM için depolamayı yapılandırma](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-existing.png)

SQL Server VM oluşturma işlemi sırasında yapılandırılmış sürücüler için disk ayarlarını değiştirebilirsiniz. **Sürücüyü Genişlet** ' i seçtiğinizde sürücü değiştirme sayfası açılır ve bu da disk türünü değiştirmenize ve ek diskler eklemenize olanak tanır. 

![Mevcut SQL Server VM için depolamayı yapılandırma](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-extend-drive.png)



## <a name="storage-configuration"></a>Depolama yapılandırması

Bu bölümde, Azure 'un Azure portal SQL VM sağlaması veya yapılandırması sırasında otomatik olarak gerçekleştirdiği depolama yapılandırması değişikliklerine yönelik bir başvuru sağlanmaktadır.

* Azure, VM 'nizden seçilen depolama alanından bir depolama havuzu yapılandırır. Bu konunun sonraki bölümünde, depolama havuzu yapılandırması hakkında ayrıntılı bilgi verilmektedir.
* Otomatik depolama yapılandırması her zaman [Premium SSDs](../disks-types.md) P30 veri disklerini kullanır. Sonuç olarak, seçtiğiniz terabaytlık ve sanal makinenize bağlı veri disklerinin sayısı arasında bir 1:1 eşlemesi vardır.

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


## <a name="workload-optimization-settings"></a>İş yükü iyileştirme ayarları

Aşağıdaki tabloda, kullanılabilir üç iş yükü türü seçeneği ve bunların karşılık gelen iyileştirmeleri açıklanmaktadır:

| İş yükü türü | Açıklama | İyileştirmeler |
| --- | --- | --- |
| **Genel** |Çoğu iş yüklerini destekleyen varsayılan ayar |Hiçbiri |
| **İşlemsel işleme** |Geleneksel veritabanı OLTP iş yükleri için depolamayı iyileştirir |İzleme bayrağı 1117<br/>İzleme bayrağı 1118 |
| **Veri depolama** |Analitik ve raporlama iş yükleri için depolamayı iyileştirir |İzleme bayrağı 610<br/>İzleme bayrağı 1117 |

> [!NOTE]
> Yalnızca depolama yapılandırması adımında bir SQL sanal makinesi sağladığınızda iş yükü türünü belirtebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Azure VM 'lerinde SQL Server çalıştırmaya ilişkin diğer konular için bkz. [Azure sanal makinelerinde SQL Server](virtual-machines-windows-sql-server-iaas-overview.md).
