---
title: Bir SQL Server veritabanını bir VM 'ye SQL Server geçirme | Microsoft Docs
description: Şirket içi kullanıcı veritabanını bir Azure sanal makinesinde SQL Server geçirme hakkında bilgi edinin.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 00fd08c6-98fa-4d62-a3b8-ca20aa5246b1
ms.service: virtual-machines-sql
ms.workload: iaas-sql-server
ms.tgt_pltfrm: vm-windows-sql-server
ms.topic: article
ms.date: 08/18/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 5a8b66c181505a617b002d1a45675d4677588b1c
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102191"
---
# <a name="migrate-a-sql-server-database-to-sql-server-in-an-azure-vm"></a>Bir SQL Server veritabanını Azure VM’deki SQL Server’a geçirme

Şirket içi SQL Server kullanıcı veritabanını bir Azure sanal makinesinde SQL Server geçirmek için çeşitli yöntemler vardır. Bu makalede çeşitli yöntemler kısaca ele alınacaktır ve çeşitli senaryolar için en iyi yöntem önerilir.


[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

  > [!NOTE]
  > SQL Server 2008 ve SQL Server 2008 R2, şirket içi örnekler için [destek yaşam döngüsünün sonuna](https://www.microsoft.com/sql-server/sql-server-2008) yaklaşıyor. Desteği genişletmek için SQL Server örneğinizi bir Azure sanal makinesine geçirebilir veya şirket içinde tutmak için genişletilmiş güvenlik güncelleştirmeleri satın alabilirsiniz. Daha fazla bilgi için bkz. [Azure ile SQL Server 2008 ve 2008 R2 desteğini genişletme](virtual-machines-windows-sql-server-2008-eos-extend-support.md)

## <a name="what-are-the-primary-migration-methods"></a>Birincil geçiş yöntemleri nelerdir?
Birincil geçiş yöntemleri şunlardır:

* Sıkıştırmayı kullanarak şirket içi yedekleme gerçekleştirin ve yedek dosyayı Azure sanal makinesine el ile kopyalayın
* URL 'de bir yedekleme gerçekleştirin ve URL 'den Azure sanal makinesine geri yükleyin
* Verileri ve günlük dosyalarını Azure Blob depolamaya ayırın ve sonra URL 'den Azure VM 'ye SQL Server ekleyin
* Şirket içi fiziksel makineyi Hyper-V VHD 'sine dönüştürün, Azure Blob depolama alanına yükleyin ve ardından karşıya yüklenen VHD 'YI kullanarak yeni VM olarak dağıtın
* Windows Içeri/dışarı aktarma hizmetini kullanarak sabit sürücü gönder
* Şirket içinde bir AlwaysOn kullanılabilirlik grubu dağıtımınız varsa Azure 'da bir çoğaltma oluşturmak için [Azure çoğaltma ekleme Sihirbazı 'nı](../sqlclassic/virtual-machines-windows-classic-sql-onprem-availability.md) kullanın ve ardından yük devretme, kullanıcıları Azure veritabanı örneğine işaret eder
* Azure SQL Server örneğini bir abone olarak yapılandırmak ve ardından çoğaltmayı devre dışı bırakmak için SQL Server [İşlemsel çoğaltma](https://msdn.microsoft.com/library/ms151176.aspx) kullanın, böylece kullanıcılar Azure veritabanı örneğine işaret eder

> [!TIP]
> Ayrıca, veritabanlarını Azure 'daki SQL Server VM 'Ler arasında taşımak için de aynı teknikleri kullanabilirsiniz. Örneğin, bir SQL Server Gallery-Image VM 'yi bir sürümden diğerine yükseltmek için desteklenen bir yol yoktur. Bu durumda, yeni sürüm/sürüm ile yeni bir SQL Server VM oluşturmanız ve ardından veritabanlarınızı taşımak için bu makaledeki geçiş tekniklerinden birini kullanmanız gerekir. 

## <a name="choosing-your-migration-method"></a>Geçiş yönteminizi seçme
En iyi veri aktarımı performansı için, veritabanı dosyalarını sıkıştırılmış bir yedekleme dosyası kullanarak Azure VM 'ye geçirin.

Veritabanı geçiş işlemi sırasında kapalı kalma süresini en aza indirmek için, AlwaysOn seçeneğini ya da işlemsel çoğaltma seçeneğini kullanın.

Yukarıdaki yöntemlerin kullanılması mümkün değilse, veritabanınızı el ile geçirin. Bu yöntemi kullanarak, genellikle bir veritabanı yedeklemesinden, ardından veritabanı yedeklemesinin bir kopyasını Azure 'a ve ardından bir veritabanı geri yükleme gerçekleştirecek şekilde başlacaksınız. Ayrıca, veritabanı dosyalarını Azure 'a kopyalayabilir ve daha sonra ekleyebilirsiniz. Bu el ile bir veritabanını bir Azure VM 'ye geçirme işlemini gerçekleştirmek için kullanabileceğiniz çeşitli yöntemler vardır.

> [!NOTE]
> SQL Server 2014 ' ye veya daha eski SQL Server sürümlerinden SQL Server 2016 ' e yükselttiğinizde, değişikliklerin gerekli olup olmadığını göz önünde bulundurmanız gerekir. Geçiş projenizin bir parçası olarak SQL Server yeni sürümü tarafından desteklenmeyen özelliklerle ilgili tüm bağımlılıkları ele etmenizi öneririz. Desteklenen sürümler ve senaryolar hakkında daha fazla bilgi için bkz. [SQL Server yükseltme](https://msdn.microsoft.com/library/bb677622.aspx).

Aşağıdaki tabloda, her bir birincil geçiş yöntemi listelenmekte ve her yöntemin kullanımı en uygun olduğunda açıklanır.

| Yöntem | Kaynak veritabanı sürümü | Hedef veritabanı sürümü | Kaynak veritabanı yedekleme boyutu kısıtlaması | Notlar |
| --- | --- | --- | --- | --- |
| [Sıkıştırmayı kullanarak şirket içi yedekleme gerçekleştirin ve yedek dosyayı Azure sanal makinesine el ile kopyalayın](#backup-and-restore) |SQL Server 2005 veya üzeri |SQL Server 2005 veya üzeri |[Azure VM depolama sınırı](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) | Bu, veritabanlarını makineler arasında taşımak için çok basit ve iyi test edilmiş bir tekniktir. |
| [URL 'de bir yedekleme gerçekleştirin ve URL 'den Azure sanal makinesine geri yükleyin](#backup-to-url-and-restore) |SQL Server 2012 SP1 CU2 UYGULAMAZSANıZ veya üzeri |SQL Server 2012 SP1 CU2 UYGULAMAZSANıZ veya üzeri |SQL Server 2016 için < 12,8 TB, yoksa < 1 TB | Bu yöntem, Azure Storage kullanarak yedekleme dosyasını VM 'ye taşımanın başka bir yoludur. |
| [Verileri ve günlük dosyalarını Azure Blob depolamaya ayırın ve sonra URL 'den Azure sanal makinesine SQL Server ekleyin](#detach-and-attach-from-url) |SQL Server 2005 veya üzeri |SQL Server 2014 veya üzeri |[Azure VM depolama sınırı](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |Bu [dosyaları Azure Blob depolama hizmetini kullanarak depolamayı](https://msdn.microsoft.com/library/dn385720.aspx) planlarken ve bunları BIR Azure VM 'de çalışan SQL Server, özellikle de çok büyük veritabanlarıyla kullanmak üzere planlarken bu yöntemi kullanın |
| [Şirket içi makineyi Hyper-V VHD 'lere dönüştürün, Azure Blob depolama alanına yükleyin ve ardından karşıya yüklenen VHD 'YI kullanarak yeni bir sanal makine dağıtın](#convert-to-vm-and-upload-to-url-and-deploy-as-new-vm) |SQL Server 2005 veya üzeri |SQL Server 2005 veya üzeri |[Azure VM depolama sınırı](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |SQL Server daha eski bir sürümde çalıştıracağınız bir veritabanını geçirirken veya diğer Kullanıcı veritabanlarına bağımlı veritabanı geçişinin bir parçası olarak sistem ve Kullanıcı veritabanlarını geçirirken ve/veya [SQL Server](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md) Sistem veritabanları. |
| [Windows Içeri/dışarı aktarma hizmetini kullanarak sabit sürücü gönder](#ship-hard-drive) |SQL Server 2005 veya üzeri |SQL Server 2005 veya üzeri |[Azure VM depolama sınırı](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |El ile kopyalama yöntemi çok büyük veritabanları gibi çok yavaş olduğunda [Windows içeri/dışarı aktarma hizmetini](../../../storage/common/storage-import-export-service.md) kullanın |
| [Azure çoğaltma ekleme Sihirbazı 'Nı kullanma](../sqlclassic/virtual-machines-windows-classic-sql-onprem-availability.md) |SQL Server 2012 veya üzeri |SQL Server 2012 veya üzeri |[Azure VM depolama sınırı](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |Kapalı kalma süresini en aza indirir, her zaman açık şirket içi dağıtıma sahip olduğunuzda kullanın |
| [SQL Server işlemsel çoğaltma kullanma](https://msdn.microsoft.com/library/ms151176.aspx) |SQL Server 2005 veya üzeri |SQL Server 2005 veya üzeri |[Azure VM depolama sınırı](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |Kapalı kalma süresini en aza indirmek ve her zaman açık şirket içi dağıtıma sahip değilseniz kullanın |

## <a name="backup-and-restore"></a>Yedekleme ve geri yükleme
Veritabanınızı sıkıştırma ile yedekleyin, yedeklemeyi VM 'ye kopyalayın ve sonra veritabanını geri yükleyin. Yedekleme dosyanız 1 TB 'den büyükse, bir VM diskinin en büyük boyutu 1 TB olduğundan, onu Stripe gerekir. Bu el ile yöntemi kullanarak bir kullanıcı veritabanını geçirmek için aşağıdaki genel adımları kullanın:

1. Şirket içi bir konumda tam bir veritabanı yedeklemesi gerçekleştirin.
2. İstenen SQL Server sürümü ile bir sanal makine oluşturun veya yükleyin.
3. Gereksinimlerinize göre bağlantı kurun. Bkz. [Azure 'da SQL Server sanal makinesine bağlanma (Kaynak Yöneticisi)](virtual-machines-windows-sql-connect.md).
4. Yedekleme dosyanızı, Uzak Masaüstü, Windows Gezgini veya bir komut isteminden Kopyala komutunu kullanarak sanal makinenize kopyalayın.

## <a name="backup-to-url-and-restore"></a>URL 'ye yedekleme ve geri yükleme
Yerel bir dosyaya yedeklemek yerine, [URL 'ye yedekleme](https://msdn.microsoft.com/library/dn435916.aspx) 'yi kullanarak URL 'den VM 'ye geri yükleyebilirsiniz. SQL Server 2016 ile, şeritli yedekleme kümeleri desteklenir, performans için önerilir ve BLOB başına düşen boyut sınırlarını aşmaları gerekir. Çok büyük veritabanları için [Windows içeri/dışarı aktarma hizmeti](../../../storage/common/storage-import-export-service.md) kullanılması önerilir.

## <a name="detach-and-attach-from-url"></a>URL 'den ayır ve Ekle
Veritabanınızı ve günlük dosyalarınızı ayırın ve bunları [Azure Blob depolamaya](https://msdn.microsoft.com/library/dn385720.aspx)aktarın. Ardından, Azure VM 'nizin URL 'sinden veritabanını iliştirin. Fiziksel veritabanı dosyalarının blob depolamada bulunmasını istiyorsanız bunu kullanın. Bu çok büyük veritabanları için yararlı olabilir. Bu el ile yöntemi kullanarak bir kullanıcı veritabanını geçirmek için aşağıdaki genel adımları kullanın:

1. Veritabanı dosyalarını şirket içi veritabanı örneğinden ayırın.
2. [AzCopy komut satırı yardımcı programını](../../../storage/common/storage-use-azcopy.md)kullanarak ayrılmış veritabanı dosyalarını Azure Blob depolama alanına kopyalayın.
3. Azure URL 'sindeki veritabanı dosyalarını Azure VM 'deki SQL Server örneğine ekleyin.

## <a name="convert-to-vm-and-upload-to-url-and-deploy-as-new-vm"></a>VM'ye dönüştürüp URL'ye yükleme ve yeni VM olarak dağıtma
Şirket içi SQL Server örneğindeki tüm sistem ve Kullanıcı veritabanlarını Azure sanal makinesine geçirmek için bu yöntemi kullanın. Bu el ile yöntemi kullanarak tüm SQL Server örneğini geçirmek için aşağıdaki genel adımları kullanın:

1. Fiziksel veya sanal makineleri Hyper-V VHD 'lere dönüştürün.
2. [Add-AzureVHD cmdlet 'ini](https://msdn.microsoft.com/library/windowsazure/dn495173.aspx)kullanarak VHD dosyalarını Azure depolama 'ya yükleyin.
3. Karşıya yüklenen VHD 'YI kullanarak yeni bir sanal makine dağıtın.

> [!NOTE]
> Tüm bir uygulamayı geçirmek için [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md)] kullanmayı göz önünde bulundurun.

## <a name="ship-hard-drive"></a>Sabit sürücü gönder
Ağ üzerinden karşıya yükleme işleminin yüksek maliyetli veya uygun olmaması durumunda büyük miktarlarda dosya verilerini Azure Blob depolama 'ya aktarmak için [Windows içeri/dışarı aktarma hizmeti yöntemini](../../../storage/common/storage-import-export-service.md) kullanın. Bu hizmetle, bu verileri içeren bir veya daha fazla sabit sürücüyü, verilerinizin depolama hesabınıza yükleneceği bir Azure veri merkezine gönderirsiniz.

## <a name="next-steps"></a>Sonraki adımlar
Azure sanal makinelerinde SQL Server çalıştırma hakkında daha fazla bilgi için bkz. [Azure sanal makinelerine genel bakış SQL Server](virtual-machines-windows-sql-server-iaas-overview.md).

> [!TIP]
> SQL Server sanal makineleri hakkında sorularınız olursa [Sık Sorulan Sorular](virtual-machines-windows-sql-server-iaas-faq.md) bölümüne bakın.

Yakalanan görüntüden Azure SQL Server sanal makinesi oluşturma yönergeleri için, CSS SQL Server mühendislerinin blogdaki [yakalanan görüntülerden Azure SQL sanal makinelerini ' kopyalama ' hakkında ipuçları & püf noktaları](https://blogs.msdn.microsoft.com/psssql/2016/07/06/tips-tricks-on-cloning-azure-sql-virtual-machines-from-captured-images/) konusuna bakın.

