---
title: Azure Windows Sanal Makinelerinde SQL Server'a Genel Bakış | Microsoft Docs
description: Şirket içi donanımları yönetmek zorunda kalmadan Bulutta Azure sanal makinelerinde SQL Server tam sürümlerinin nasıl çalıştırılacağını öğrenin.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
tags: azure-service-management
ms.assetid: c505089e-6bbf-4d14-af0e-dd39a1872767
ms.service: virtual-machines-sql
ms.topic: overview
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/27/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: b4b1234b88eafedbd03b78241baaa40231a6cbd6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97356812"
---
# <a name="what-is-sql-server-on-azure-virtual-machines-windows"></a>Azure sanal makinelerinde SQL Server nedir (Windows)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
> * [Linux](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)

[Azure sanal makinelerinde SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/) , şirket içi donanımları yönetmek zorunda kalmadan bulutta SQL Server tam sürümlerini kullanmanıza olanak sağlar. SQL Server sanal makineler (VM 'Ler), Kullandıkça Öde sırasında lisans maliyetlerini de basitleştirir.

Azure sanal makineleri dünyanın birçok farklı [coğrafi bölgesinde](https://azure.microsoft.com/regions/) çalışır. Bu makineler, ayrıca çeşitli [makine boyutları](../../../virtual-machines/sizes.md) sunar. Sanal makine resim galerisi, doğru sürüm ve işletim sistemiyle bir SQL Server VM’i oluşturmanızı sağlar. Bu, sanal makinelere birçok farklı SQL Server iş yükü için iyi bir seçenek sunar.

Azure VM 'lerinde SQL Server yeni başladıysanız, derinlemesine Azure [SQL video serimizin](https://channel9.msdn.com/Series/Azure-SQL-for-Beginners?WT.mc_id=azuresql4beg_azuresql-ch9-niner) *Azure VM 'sine genel bakış videosunu SQL Server* inceleyin:
> [!VIDEO https://channel9.msdn.com/Series/Azure-SQL-for-Beginners/SQL-Server-on-Azure-VM-Overview-4-of-61/player]

## <a name="automated-updates"></a>Otomatik güncelleştirmeler

Azure sanal makinelerinde SQL Server, önemli pencerelerin ve SQL Server güncelleştirmelerinin otomatik olarak yüklenmesine yönelik bir bakım penceresi zamanlamak için [Otomatik Düzeltme Eki uygulamayı](automated-patching.md) kullanabilir.

## <a name="automated-backups"></a>Otomatik yedeklemeler

Azure sanal makinelerinde SQL Server, düzenli olarak veritabanınızın yedeklerini blob depolamaya oluşturan [otomatik yedekleme](automated-backup.md)özelliğinden yararlanabilir. Bu tekniği el ile de kullanabilirsiniz. Daha fazla bilgi için. bkz. [SQL Server Yedekleme ve Geri Yükleme için Azure Storage’ı Kullanma](azure-storage-sql-server-backup-restore-use.md).

Azure, Azure VM 'lerinde çalışan SQL Server için kurumsal sınıf bir yedekleme çözümü de sunmaktadır. Tam olarak yönetilen bir yedekleme çözümü, her zaman açık kullanılabilirlik grupları, uzun süreli saklama, noktadan noktaya kurtarma ve merkezi yönetim ve izleme desteği destekler. Daha fazla bilgi için bkz. [Azure VM 'lerinde SQL Server Azure Backup](../../../backup/backup-azure-sql-database.md).
  

## <a name="high-availability"></a>Yüksek kullanılabilirlik

Size yüksek kullanılabilirlik gerekiyorsa, SQL Server Kullanılabilirlik gruplarını yapılandırmayı dikkate alın. Bu, bir sanal ağdaki Azure sanal makinelerinde birden çok SQL Server örneğini içerir. Yüksek kullanılabilirlik çözümünüzü el ile yapılandırabilir veya otomatik yapılandırma için Azure portal şablonları kullanabilirsiniz. Tüm yüksek kullanılabilirlik seçeneklerine genel bakış için bkz. [Azure sanal makinelerinde SQL Server Için yüksek kullanılabilirlik ve olağanüstü durum kurtarma](business-continuity-high-availability-disaster-recovery-hadr-overview.md).

## <a name="performance"></a>Performans

Azure sanal makineleri çeşitli iş yükü taleplerini karşılamak üzere farklı makine boyutları sunar. SQL Server VM 'Ler, performans gereksinimleriniz için en iyi duruma getirilmiş otomatik depolama yapılandırması da sağlar. SQL Server VM 'Ler için depolamayı yapılandırma hakkında daha fazla bilgi için bkz. [SQL Server VM 'ler Için depolama yapılandırması](storage-configuration.md). Performansı hassas bir şekilde ayarlamak için bkz. [Azure sanal makinelerinde SQL Server için En Iyi performans uygulamaları](performance-guidelines-best-practices.md).

## <a name="get-started-with-sql-server-vms"></a>SQL Server VM 'Leri kullanmaya başlama

Kullanmaya başlamak için, gerekli olan sürüm ve işletim sisteminizle birlikte bir SQL Server sanal makine resmi seçin. Aşağıdaki bölümlerde SQL Server sanal makine galeri görüntüleri için Azure portalının doğrudan bağlantıları verilmektedir.

> [!TIP]
> SQL Server görüntülerinin fiyatlandırmasının nasıl anlaşılmasıyla ilgili daha fazla bilgi için bkz. [Azure sanal makinelerinde SQL Server Için fiyatlandırma Kılavuzu](pricing-guidance.md). 

### <a name="pay-as-you-go"></a><a id="payasyougo"></a> Kullandıkça öde
Şu tabloda, kullandıkça öde SQL Server resimlerinin bir matrisi verilmektedir.

| Sürüm | İşletim sistemi | Sürüm |
| --- | --- | --- |
| **SQL Server 2019** | Windows Server 2019 | [Enterprise](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019enterprise), [Standard](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019standard), [Web](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019web), [Geliştirici](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019sqldev) | 
| **SQL Server 2017** |Windows Server 2016 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseWindowsServer2016), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonWindowsServer2016), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonWindowsServer2016), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonWindowsServer2016), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonWindowsServer2016) |
| **SQL Server 2016 SP2** |Windows Server 2016 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2016SP2EnterpriseWindowsServer2016), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2016SP2StandardWindowsServer2016), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2016SP2WebWindowsServer2016), [Express](https://portal.azure.com/#create/Microsoft.FreeLicenseSQLServer2016SP2ExpressWindowsServer2016), [Developer](https://portal.azure.com/#create/Microsoft.FreeLicenseSQLServer2016SP2DeveloperWindowsServer2016) |
| **SQL Server 2014 SP2** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2ExpressWindowsServer2012R2) |
| **SQL Server 2012 SP4** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4ExpressWindowsServer2012R2) |
| **SQL Server 2008 R2 SP3** |Windows Server 2008 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3EnterpriseWindowsServer2008R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3StandardWindowsServer2008R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3WebWindowsServer2008R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3ExpressWindowsServer2008R2) |

Kullanılabilir Linux üzerinde SQL Server sanal makine görüntülerini görmek için bkz. [Azure sanal makinelerinde SQL Server genel bakış (Linux)](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md).

> [!NOTE]
> Kullanım başına ödeme SQL Server VM lisans modelini kendi lisansınızı kullanacak şekilde değiştirmek mümkündür. Daha fazla bilgi için bkz. [SQL Server VM için lisans modelini değiştirme](licensing-model-azure-hybrid-benefit-ahb-change.md). 

### <a name="bring-your-own-license"></a><a id="BYOL"></a> Kendi lisansınızı getirin
Ayrıca kendi lisansınızı getirebilirsiniz (KLG). Bu senaryoda, SQL Server Lisans için hiçbir ek bir ücret olmadan yalnızca VM için ödeme yaparsınız.  Kendi lisansınızı getirmek, sürekli üretim iş yüklerinde zaman içinde paradan tasarruf etmenizi sağlayabilir. Bu seçeneği kullanmaya yönelik gereksinimler için bkz. [SQL Server Azure VM’leri için fiyatlandırma kılavuzu](pricing-guidance.md#byol).

Kendi lisansınızı getirmek için, mevcut bir ödeme kullanım SQL Server VM dönüştürebilir ya da önekli **{KLG}** ile bir görüntü dağıtabilirsiniz. Lisans modelinizi kullanım başına ödeme ve KLG arasında değiştirme hakkında daha fazla bilgi için bkz. [SQL Server VM için lisans modelini değiştirme](licensing-model-azure-hybrid-benefit-ahb-change.md). 

| Sürüm | İşletim sistemi | Sürüm |
| --- | --- | --- |
| **SQL Server 2019** | Windows Server 2019 | [ENTERPRISE KLG](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019-byolenterprise), [Standard KLG](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019-byolstandard)| 
| **SQL Server 2017** |Windows Server 2016 |[Enterprise KLG](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017EnterpriseWindowsServer2016), [Standard KLG](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017StandardonWindowsServer2016) |
| **SQL Server 2016 SP2** |Windows Server 2016 |[Enterprise KLG](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP2EnterpriseWindowsServer2016), [Standard KLG](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP2StandardWindowsServer2016) |
| **SQL Server 2014 SP2** |Windows Server 2012 R2 |[Enterprise KLG](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP2EnterpriseWindowsServer2012R2), [Standard KLG](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP2StandardWindowsServer2012R2) |
| **SQL Server 2012 SP4** |Windows Server 2012 R2 |[ENTERPRISE KLG](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP4EnterpriseWindowsServer2012R2), [Standard KLG](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP4StandardWindowsServer2012R2) |

PowerShell kullanılarak Azure portal kullanılamayan SQL Server eski bir görüntüsünü dağıtmak mümkündür. PowerShell kullanarak kullanılabilir tüm görüntüleri görüntülemek için aşağıdaki komutu kullanın:

  ```powershell
  Get-AzVMImageOffer -Location $Location -Publisher 'MicrosoftSQLServer'
  ```

PowerShell kullanarak SQL Server VM 'Leri dağıtma hakkında daha fazla bilgi için, [Azure PowerShell ile SQL Server sanal makinelerin nasıl](create-sql-vm-powershell.md)sağlanacağını görüntüleyin.


### <a name="connect-to-the-vm"></a>VM’ye bağlanma
SQL Server sanal makinenizi oluşturduktan sonra, SQL Server Management Studio (SSMS) gibi uygulamalar veya araçlardan bu makineye bağlanın. Yönergeler için bkz. [Azure 'da SQL Server sanal makinesine bağlanma](ways-to-connect-to-sql.md).

### <a name="migrate-your-data"></a>Verilerinizi geçirme
Var olan bir veritabanınız varsa, bunu yeni sağlanmış SQL Server VM taşımak isteyeceksiniz. Geçiş seçenekleri ve kılavuzların listesi için bkz. [Azure VM’de bir Veritabanını SQL Server’a Geçirme](migrate-to-vm-from-sql-server.md).

## <a name="create-and-manage-azure-sql-resources-with-the-azure-portal"></a>Azure portal ile Azure SQL kaynakları oluşturma ve yönetme

Azure portal, SQL sanal makineleriniz dahil olmak üzere [tüm Azure SQL kaynaklarınızı](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Sql%2Fazuresql) yönetebileceğiniz tek bir sayfa sağlar.

**Azure SQL kaynakları** sayfasına erişmek için Azure Portal menüsünde **Azure SQL** ' i seçin veya HERHANGI bir sayfadan **Azure SQL** araması yapın ve seçin.

![Azure SQL araması](./media/sql-server-on-azure-vm-iaas-what-is-overview/search-for-azure-sql.png)

> [!NOTE]
> Azure SQL, tüm Azure SQL veritabanlarına, elastik havuzlarınıza, mantıksal sunuculara, yönetilen örneklere ve sanal makinelere erişmenin hızlı ve kolay bir yolunu sunar. Azure SQL bir hizmet veya kaynak değil. 

Mevcut kaynakları yönetmek için listeden istediğiniz öğeyi seçin. Yeni Azure SQL kaynakları oluşturmak için **+ Ekle**' yi seçin. 

![Azure SQL kaynağı oluşturma](./media/sql-server-on-azure-vm-iaas-what-is-overview/create-azure-sql-resource.png)

**+ Ekle**' yi seçtikten sonra herhangi bir kutucukta **Ayrıntıları göster** ' i seçerek farklı seçeneklerle ilgili ek bilgileri görüntüleyin.

![veritabanları kutucuk ayrıntıları](./media/sql-server-on-azure-vm-iaas-what-is-overview/sql-vm-details.png)

Ayrıntılar için bkz.

- [Tek bir veritabanı oluşturma](../../database/single-database-create-quickstart.md)
- [Elastik havuz oluşturma](../../database/elastic-pool-overview.md#creating-a-new-sql-database-elastic-pool-using-the-azure-portal)
- [Yönetilen örnek oluşturma](../../managed-instance/instance-create-quickstart.md)
- [SQL Server sanal makinesi oluşturma](sql-vm-create-portal-quickstart.md)

## <a name="sql-server-vm-image-refresh-policy"></a><a id="lifecycle"></a> SQL Server VM görüntü yenileme ilkesi
Azure desteklenen her işletim sistemi, sürüm ve sürüm bileşimi için yalnızca tek bir sanal makine görüntüsü tutar. Diğer bir deyişle, zaman içinde görüntüler yenilenir ve eski görüntüler kaldırılır. Daha fazla bilgi için [SQL Server VM'leri SSS](frequently-asked-questions-faq.md#images) makalesindeki **Görüntüler** bölümüne bakın.

## <a name="customer-experience-improvement-program-ceip"></a>Müşteri deneyimini geliştirme programı (CEIP)
Müşteri Deneyimini Geliştirme Programı (CEIP) varsayılan olarak etkindir. Bu, SQL Server’ın geliştirilmesine yardımcı olmak için Microsoft’a düzenli olarak raporlar gönderir. CEIP’i hazırladıktan sonra devre dışı bırakmak istemiyorsanız CEIP için herhangi bir yönetim görevi gerekmez. VM’ye uzak masaüstüyle bağlanarak CEIP özelleştirebilir ya da devre dışı bırakabilirsiniz. Ardından **SQL Server Hata ve Kullanım Raporlama** yardımcı programını çalıştırın. Raporlamayı devre dışı bırakmak için yönergeleri izleyin. Veri toplama hakkında daha fazla bilgi için bkz. [SQL Server Gizlilik Bildirimi](/sql/sql-server/sql-server-privacy).

## <a name="related-products-and-services"></a>İlgili ürün ve hizmetler
### <a name="windows-virtual-machines"></a>Windows sanal makineleri
* [Azure sanal makinelerine genel bakış](../../../virtual-machines/windows/overview.md)

### <a name="storage"></a>Depolama
* [Microsoft Azure Depolama'ya Giriş](../../../storage/common/storage-introduction.md)

### <a name="networking"></a>Ağ
* [Sanal ağa genel bakış](../../../virtual-network/virtual-networks-overview.md)
* [Azure'da IP adresleri](../../../virtual-network/public-ip-addresses.md)
* [Azure portalda Tam Etki Alanı Adı oluşturma](../../../virtual-machines/create-fqdn.md)

### <a name="sql"></a>SQL
* [SQL Server belgeleri](/sql/index)
* [Azure SQL Veritabanı karşılaştırması](../../azure-sql-iaas-vs-paas-what-is-overview.md)

## <a name="next-steps"></a>Sonraki adımlar

Azure sanal makineler 'de SQL Server kullanmaya başlayın:

* [Azure portalında SQL Server VM’i oluşturma](sql-vm-create-portal-quickstart.md)

SQL Server VM 'Ler hakkında sık sorulan soruların yanıtlarını alın:

* [Azure Sanal Makinelerde SQL Server SSS](frequently-asked-questions-faq.md)

IaaS 'de SQL Server N katmanlı uygulamaları çalıştırmaya yönelik başvuru mimarilerini görüntüleyin

* [SQL Server ile Azure 'da Windows N katmanlı uygulama](/azure/architecture/reference-architectures/n-tier/n-tier-sql-server)
* [Yüksek kullanılabilirlik için birden fazla Azure bölgesinde N katmanlı bir uygulama çalıştırma](/azure/architecture/reference-architectures/n-tier/multi-region-sql-server)