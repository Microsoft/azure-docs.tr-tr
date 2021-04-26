---
title: Bir SQL Server veritabanını bir sanal makineye SQL Server geçirme | Microsoft Docs
description: Şirket içi kullanıcı veritabanını bir Azure sanal makinesine SQL Server geçirme hakkında bilgi edinin.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: 00fd08c6-98fa-4d62-a3b8-ca20aa5246b1
ms.service: virtual-machines-sql
ms.workload: iaas-sql-server
ms.tgt_pltfrm: vm-windows-sql-server
ms.subservice: migration
ms.topic: how-to
ms.date: 08/18/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: f6e9009040d2d02702f8a71c352716491d07d1f7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98704313"
---
# <a name="migrate-a-sql-server-database-to-sql-server-on-an-azure-virtual-machine"></a>SQL Server veritabanını bir Azure sanal makinesine SQL Server geçirme

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Şirket içi SQL Server kullanıcı veritabanını bir Azure sanal makinesine (VM) SQL Server geçirmek için çeşitli yollar vardır. Bu makalede çeşitli yöntemler kısaca ele alınacaktır ve çeşitli senaryolar için en iyi yöntem önerilir.


[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

  > [!NOTE]
  > SQL Server 2008 ve SQL Server 2008 R2, şirket içi örnekler için [destek yaşam döngüsünün sonuna](https://www.microsoft.com/sql-server/sql-server-2008) yaklaşıyor. Desteği genişletmek için SQL Server örneğinizi bir Azure sanal makinesine geçirebilir veya şirket içinde tutmak için genişletilmiş güvenlik güncelleştirmeleri satın alabilirsiniz. Daha fazla bilgi için bkz. [Azure ile SQL Server 2008 ve 2008 R2 desteğini genişletme](sql-server-2008-extend-end-of-support.md)

## <a name="what-are-the-primary-migration-methods"></a>Birincil geçiş yöntemleri nelerdir?

Birincil geçiş yöntemleri şunlardır:

* Sıkıştırmayı kullanarak şirket içi yedekleme gerçekleştirin ve ardından yedekleme dosyasını el ile Azure VM 'ye kopyalayın.
* URL 'ye bir yedekleme yapın ve ardından URL 'den Azure VM 'ye geri yükleyin.
* Verileri ve günlük dosyalarını ayırın, Azure Blob depolama alanına kopyalayın ve ardından bunları URL 'den Azure VM 'de SQL Server bağlayın.
* Şirket içi fiziksel makineyi bir Hyper-V VHD 'sine dönüştürün, Azure Blob depolama alanına yükleyin ve ardından karşıya yüklenen VHD 'YI kullanarak yeni VM olarak dağıtın.
* Windows Içeri/dışarı aktarma hizmetini kullanarak sabit sürücüyü sunun.
* Şirket içinde bir AlwaysOn kullanılabilirlik grubu dağıtımınız varsa, Azure 'da bir çoğaltma oluşturmak için [Azure çoğaltma ekleme Sihirbazı 'nı](/previous-versions/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-sql-onprem-availability) kullanın ve kullanıcıları Azure veritabanı örneğine işaret edin.
* Azure SQL Server örneğini bir abone olarak yapılandırmak, çoğaltmayı devre dışı bırakmak ve kullanıcıları Azure veritabanı örneğine göstermek için SQL Server [İşlemsel çoğaltma](/sql/relational-databases/replication/transactional/transactional-replication) kullanın.

> [!TIP]
> Ayrıca, veritabanlarını Azure 'daki SQL Server VM 'Ler arasında taşımak için de aynı teknikleri kullanabilirsiniz. Örneğin, bir SQL Server Gallery-Image VM 'yi bir sürümden diğerine yükseltmek için desteklenen bir yol yoktur. Bu durumda, yeni sürüm/sürüm ile yeni bir SQL Server VM oluşturmanız ve ardından veritabanlarınızı taşımak için bu makaledeki geçiş tekniklerinden birini kullanmanız gerekir. 

## <a name="choose-a-migration-method"></a>Bir geçiş yöntemi seçin

En iyi veri aktarımı performansı için, veritabanı dosyalarını sıkıştırılmış bir yedekleme dosyası kullanarak Azure VM 'ye geçirin.

Veritabanı geçiş işlemi sırasında kapalı kalma süresini en aza indirmek için, AlwaysOn seçeneğini ya da işlemsel çoğaltma seçeneğini kullanın.

Yukarıdaki yöntemlerin kullanılması mümkün değilse, veritabanınızı el ile geçirin. Genellikle, bir veritabanı yedeklemesinden başlar, veritabanını Azure 'a veritabanı yedeklemesinin bir kopyasıyla takip edin ve sonra veritabanını geri yükleyin. Ayrıca, veritabanı dosyalarını Azure 'a kopyalayabilir ve daha sonra ekleyebilirsiniz. Bu el ile bir veritabanını bir Azure VM 'ye geçirme işlemini gerçekleştirmek için kullanabileceğiniz çeşitli yöntemler vardır.

> [!NOTE]
> SQL Server 2014 ' ye veya daha eski SQL Server sürümlerinden SQL Server 2016 ' e yükselttiğinizde, değişikliklerin gerekli olup olmadığını göz önünde bulundurmanız gerekir. Geçiş projenizin bir parçası olarak SQL Server yeni sürümü tarafından desteklenmeyen özelliklerle ilgili tüm bağımlılıkları ele etmenizi öneririz. Desteklenen sürümler ve senaryolar hakkında daha fazla bilgi için bkz. [SQL Server yükseltme](/sql/database-engine/install-windows/upgrade-sql-server).

Aşağıdaki tabloda, her bir birincil geçiş yöntemi listelenmekte ve her yöntemin kullanımı en uygun olduğunda açıklanır.

| Yöntem | Kaynak veritabanı sürümü | Hedef veritabanı sürümü | Kaynak veritabanı yedekleme boyutu kısıtlaması | Notlar |
| --- | --- | --- | --- | --- |
| [Sıkıştırmayı kullanarak şirket içi yedekleme gerçekleştirin ve yedek dosyayı Azure sanal makinesine el ile kopyalayın](#back-up-and-restore) |SQL Server 2005 veya üzeri |SQL Server 2005 veya üzeri |[Azure VM depolama sınırı](../../../index.yml) | Bu teknik, veritabanlarını makineler arasında taşımak için basit ve iyi test edilmiştir. |
| [URL 'de bir yedekleme gerçekleştirin ve URL 'den Azure sanal makinesine geri yükleyin](#backup-to-url-and-restore-from-url) |SQL Server 2012 SP1 CU2 UYGULAMAZSANıZ veya üzeri | SQL Server 2012 SP1 CU2 UYGULAMAZSANıZ veya üzeri | SQL Server 2016 için < 12,8 TB, yoksa < 1 TB | Bu yöntem, Azure Storage kullanarak yedekleme dosyasını VM 'ye taşımanın başka bir yoludur. |
| [Verileri ve günlük dosyalarını Azure Blob depolamaya ayırın ve sonra URL 'den Azure sanal makinesine SQL Server ekleyin](#detach-and-attach-from-a-url) | SQL Server 2005 veya üzeri |SQL Server 2014 veya üzeri | [Azure VM depolama sınırı](../../../index.yml) | Bu [dosyaları Azure Blob depolama hizmetini kullanarak depolamayı](/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure) planlarken ve bunları BIR Azure VM 'de çalışan SQL Server, özellikle de çok büyük veritabanlarıyla kullanmak üzere planlarken bu yöntemi kullanın |
| [Şirket içi makineyi Hyper-V VHD 'lere dönüştürün, Azure Blob depolama alanına yükleyin ve ardından karşıya yüklenen VHD 'YI kullanarak yeni bir sanal makine dağıtın](#convert-to-a-vm-upload-to-a-url-and-deploy-as-a-new-vm) |SQL Server 2005 veya üzeri |SQL Server 2005 veya üzeri |[Azure VM depolama sınırı](../../../index.yml) |SQL Server eski bir sürümünde çalıştırdığınız bir veritabanını geçirirken veya diğer Kullanıcı veritabanlarına ve/veya sistem veritabanlarına bağımlı veritabanı geçişinin bir parçası olarak sistem ve Kullanıcı veritabanlarını geçirirken, [kendi SQL Server lisansınızı getirirken](../../../azure-sql/azure-sql-iaas-vs-paas-what-is-overview.md)kullanın. |
| [Windows Içeri/dışarı aktarma hizmetini kullanarak sabit sürücü gönder](#ship-a-hard-drive) |SQL Server 2005 veya üzeri |SQL Server 2005 veya üzeri |[Azure VM depolama sınırı](../../../index.yml) |El ile kopyalama yöntemi çok büyük veritabanları gibi çok yavaş olduğunda [Windows içeri/dışarı aktarma hizmetini](../../../import-export/storage-import-export-service.md) kullanın |
| [Azure çoğaltma ekleme Sihirbazı 'Nı kullanma](/previous-versions/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-sql-onprem-availability) |SQL Server 2012 veya üzeri |SQL Server 2012 veya üzeri |[Azure VM depolama sınırı](../../../index.yml) |Kapalı kalma süresini en aza indirir, her zaman açık şirket içi dağıtıma sahip olduğunuzda kullanın |
| [SQL Server işlemsel çoğaltma kullanma](/sql/relational-databases/replication/transactional/transactional-replication) |SQL Server 2005 veya üzeri |SQL Server 2005 veya üzeri |[Azure VM depolama sınırı](../../../index.yml) |Kapalı kalma süresini en aza indirmek ve her zaman açık şirket içi dağıtıma sahip değilseniz kullanın |

## <a name="back-up-and-restore"></a>Yedekleme ve geri yükleme

Veritabanınızı sıkıştırma ile yedekleyin, yedeklemeyi VM 'ye kopyalayın ve sonra veritabanını geri yükleyin. Yedekleme dosyanız 1 TB 'den büyükse, bir sanal makine diskinin en büyük boyutu 1 TB olduğundan bir şeritli küme oluşturmanız gerekir. Bu el ile yöntemi kullanarak bir kullanıcı veritabanını geçirmek için aşağıdaki genel adımları kullanın:

1. Şirket içi bir konumda tam bir veritabanı yedeklemesi gerçekleştirin.
2. SQL Server istenen sürümü olan bir sanal makine oluşturun veya karşıya yükleyin.
3. Gereksinimlerinize göre bağlantı kurun. Bkz. [Azure 'da SQL Server sanal makinesine bağlanma (Kaynak Yöneticisi)](ways-to-connect-to-sql.md).
4. Yedekleme dosyanızı, bir komut isteminden Uzak Masaüstü, Windows Gezgini veya kopyalama komutunu kullanarak sanal makinenize kopyalayın.

## <a name="backup-to-url-and-restore-from-url"></a>URL 'ye yedekleme ve URL 'den geri yükleme

Yerel bir dosyaya yedeklemek yerine, [URL 'ye yedekleme](/sql/relational-databases/backup-restore/sql-server-backup-to-url) 'yi kullanarak URL 'den VM 'ye geri yükleyebilirsiniz. SQL Server 2016, şeritli yedekleme kümelerini destekler. Bunlar, performans için önerilir ve BLOB başına düşen boyut sınırlarını aşmaları gerekir. Çok büyük veritabanları için [Windows içeri/dışarı aktarma hizmeti](../../../import-export/storage-import-export-service.md) kullanılması önerilir.

## <a name="detach-and-attach-from-a-url"></a>Bir URL 'den ayırın ve ekleyin

Veritabanınızı ve günlük dosyalarınızı ayırın ve bunları [Azure Blob depolamaya](/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure)aktarın. Ardından, Azure VM 'nizin URL 'sinden veritabanını iliştirin. Fiziksel veritabanı dosyalarının blob depolamada bulunmasını istiyorsanız, çok büyük veritabanları için faydalı olabilecek bu yöntemi kullanın. Bu el ile yöntemi kullanarak bir kullanıcı veritabanını geçirmek için aşağıdaki genel adımları kullanın:

1. Veritabanı dosyalarını şirket içi veritabanı örneğinden ayırın.
2. [AzCopy komut satırı yardımcı programını](../../../storage/common/storage-use-azcopy-v10.md)kullanarak ayrılmış veritabanı dosyalarını Azure Blob depolama alanına kopyalayın.
3. Azure URL 'sindeki veritabanı dosyalarını Azure VM 'deki SQL Server örneğine ekleyin.

## <a name="convert-to-a-vm-upload-to-a-url-and-deploy-as-a-new-vm"></a>Bir VM 'ye dönüştürme, URL 'ye yükleme ve yeni VM olarak dağıtma

Şirket içi SQL Server örneğindeki tüm sistem ve Kullanıcı veritabanlarını bir Azure sanal makinesine geçirmek için bu yöntemi kullanın. Bu el ile yöntemi kullanarak tüm SQL Server örneğini geçirmek için aşağıdaki genel adımları kullanın:

1. Fiziksel veya sanal makineleri Hyper-V VHD 'lere dönüştürün.
2. [Add-AzureVHD cmdlet 'ini](/previous-versions/azure/dn495173(v=azure.100))kullanarak VHD dosyalarını Azure depolama 'ya yükleyin.
3. Karşıya yüklenen VHD 'YI kullanarak yeni bir sanal makine dağıtın.

> [!NOTE]
> Tüm bir uygulamayı geçirmek için [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md)] kullanmayı göz önünde bulundurun.

## <a name="ship-a-hard-drive"></a>Sabit sürücü gönder

Ağ üzerinden karşıya yükleme işleminin yüksek maliyetli veya uygun olmaması durumunda büyük miktarlarda dosya verilerini Azure Blob depolama 'ya aktarmak için [Windows içeri/dışarı aktarma hizmeti yöntemini](../../../import-export/storage-import-export-service.md) kullanın. Bu hizmetle, verilerinizin depolama hesabınıza yükleneceği bir Azure veri merkezine bu verileri içeren bir veya daha fazla sabit sürücü gönderirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için bkz. [Azure sanal makinelerine genel bakış SQL Server](sql-server-on-azure-vm-iaas-what-is-overview.md).

> [!TIP]
> SQL Server sanal makineleri hakkında sorularınız olursa [Sık Sorulan Sorular](frequently-asked-questions-faq.md) bölümüne bakın.

Yakalanan görüntüden bir Azure sanal makinesinde SQL Server oluşturmayla ilgili yönergeler için, CSS SQL Server mühendislerinin blogdaki [yakalanan görüntülerden Azure SQL sanal makinelerini ' kopyalama ' hakkında ipuçları & püf noktaları](/archive/blogs/psssql/tips-tricks-on-cloning-azure-sql-virtual-machines-from-captured-images) konusuna bakın.