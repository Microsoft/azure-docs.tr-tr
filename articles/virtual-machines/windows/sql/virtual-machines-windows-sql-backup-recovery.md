---
title: Azure VM 'lerinde SQL Server için yedekleme ve geri yükleme | Microsoft Docs
description: Azure sanal makinelerinde çalışan SQL Server veritabanlarına yönelik yedekleme ve geri yükleme konularını açıklar.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-resource-management
ms.assetid: 95a89072-0edf-49b5-88ed-584891c0e066
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/04/2018
ms.author: mikeray
ms.openlocfilehash: e4c126bbac73accb984f1040a7fea1740d919233
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100542"
---
# <a name="backup-and-restore-for-sql-server-in-azure-virtual-machines"></a>Azure Sanal Makineler’de SQL Server için Yedekleme ve Geri Yükleme

Bu makalede, Azure 'da bir Windows sanal makinesinde çalışan SQL Server için kullanılabilen yedekleme ve geri yükleme seçenekleri hakkında rehberlik sağlanır. Azure depolama, veri kaybına veya fiziksel veri bozulmasına karşı koruma sağlamak için her Azure VM diskinin üç kopyasını tutar. Bu nedenle, Şirket içinden farklı olarak, donanım hatalarıyla odaklanmanız gerekmez. Ancak, yanlışlıkla veri ekleme veya silme gibi uygulama veya Kullanıcı hatalarına karşı korunmak için SQL Server veritabanlarınızı yedeklemeniz gerekir. Bu durumda, zaman içinde belirli bir noktaya geri yükleme yapmak önemlidir.

Bu makalenin ilk bölümü, kullanılabilir yedekleme ve geri yükleme seçeneklerine genel bir bakış sağlar. Bu, her strateji hakkında daha fazla bilgi sağlayan bölümler tarafından izlenir.

## <a name="backup-and-restore-options"></a>Yedekleme ve geri yükleme seçenekleri

Aşağıdaki tabloda, Azure VM 'lerinde çalışan SQL Server yönelik çeşitli yedekleme ve geri yükleme seçenekleri hakkında bilgi verilmektedir:

| Stratejisi | SQL sürümleri | Açıklama |
|---|---|---|
| [Otomatik Yedekleme](#automated) | 2014<br/> 2016<br/> 2017 | Otomatik yedekleme, bir SQL Server VM tüm veritabanları için düzenli yedeklemeler zamanlamanıza olanak sağlar. Yedeklemeler, 30 güne kadar Azure Storage 'da depolanır. SQL Server 2016 ' den başlayarak otomatik yedekleme v2, el ile zamanlamayı yapılandırma ve tam ve günlük yedeklemeleri sıklığı gibi ek seçenekler sunar. |
| [SQL VM'leri için Azure Backup](#azbackup) | 2008<br/> 2012<br/> 2014<br/> 2016<br/> 2017 | Azure Backup, Azure VM 'lerinde çalışan SQL Server için kurumsal sınıf yedekleme yeteneği sağlar. Bu hizmetle, birden çok sunucu ve binlerce veritabanı için yedeklemeleri merkezi olarak yönetebilirsiniz. Veritabanları portalda belirli bir zaman noktasına geri yüklenebilir. Yıllarca yedeklemeleri koruyasağlayan özelleştirilebilir bir bekletme ilkesi sunar. |
| [El ile yedekleme](#manual) | Tümü | SQL Server sürümünüze bağlı olarak, Azure VM 'de çalışan SQL Server el ile yedekleme ve geri yükleme için çeşitli teknikler vardır. Bu senaryoda, veritabanlarınızın yedeklenme ve bu yedeklemelerin depolama konumu ve yönetimi hakkında siz sorumlusunuz. |

Aşağıdaki bölümlerde her bir seçenek daha ayrıntılı olarak açıklanır. Bu makalenin son bölümü, bir özellik matrisi biçiminde bir Özet sağlar.

## <a id="automated"></a>Otomatik yedekleme

Otomatik yedekleme, Azure 'da Windows VM 'de çalışan SQL Server Standard ve Enterprise sürümleri için otomatik yedekleme hizmeti sağlar. Bu hizmet, Azure portal SQL Server Windows sanal makine görüntülerine otomatik olarak yüklenen [SQL Server IaaS Aracısı uzantısı](virtual-machines-windows-sql-server-agent-extension.md)tarafından sağlanır.

Tüm veritabanları, yapılandırdığınız bir Azure depolama hesabına yedeklenir. Yedeklemeler, 30 güne kadar şifrelenir ve korunabilir.

SQL Server 2016 ve üzeri VM 'Ler otomatik yedekleme v2 ile daha fazla özelleştirme seçeneği sunar. Bu geliştirmeler şunları içerir:

- Sistem veritabanı yedeklemeleri
- El ile yedekleme zamanlaması ve zaman penceresi
- Tam ve günlük dosyası yedekleme sıklığı

Bir veritabanını geri yüklemek için, depolama hesabında gerekli yedekleme dosyalarını bulmanız ve SQL Server Management Studio (SSMS) ya da Transact-SQL komutlarını kullanarak SQL VM 'niz üzerinde geri yükleme gerçekleştirmeniz gerekir.

SQL VM 'Leri için Otomatik yedeklemeyi yapılandırma hakkında daha fazla bilgi için aşağıdaki makalelerden birine bakın:

- **SQL Server 2016/2017**: [Azure sanal makineleri için otomatik yedekleme v2](virtual-machines-windows-sql-automated-backup-v2.md)
- **SQL Server 2014**: [SQL Server 2014 sanal makineleri için otomatik yedekleme](virtual-machines-windows-sql-automated-backup.md)

## <a id="azbackup"></a>SQL VM 'Leri için Azure Backup

[Azure Backup](/azure/backup/) , Azure VM 'lerinde çalışan SQL Server için kurumsal sınıf yedekleme yeteneği sağlar. Tüm yedeklemeler bir kurtarma hizmetleri kasasında saklanır ve yönetilir. Özellikle kuruluşlar için bu çözümün sağladığı birçok avantaj vardır:

- **Sıfır altyapı yedeklemesi**: Yedekleme sunucularını veya depolama konumlarını yönetmeniz gerekmez.
- **Ölçek**: Birçok SQL VM ve binlerce veritabanını koruyun.
- **Kullandıkça Öde**: Bu özellik Azure Backup tarafından sunulan ayrı bir hizmettir, ancak tüm Azure hizmetlerinde olduğu gibi, yalnızca kullandığınız kadar ödeyin.
- **Merkezi Yönetim ve izleme**: Azure Backup desteklediği diğer iş yükleri de dahil olmak üzere tüm yedeklemelerinizi Azure 'daki tek bir panodan merkezi olarak yönetin.
- **İlke temelli yedekleme ve bekletme**: Düzenli yedeklemeler için standart yedekleme ilkeleri oluşturun. Yıllarca yedeklemeleri sürdürmek için bekletme ilkeleri oluşturun.
- **SQL her zaman açık Için destek**: SQL Server her zaman yapılandırmayı algılayıp koruyun ve yedekleme kullanılabilirlik grubu yedekleme tercihini dikkate alın.
- **15 dakikalık kurtarma noktası hedefi (RPO)** : Her 15 dakikada bir SQL işlem günlüğü yedeklemelerini yapılandırın.
- **Zaman noktası geri yükleme**: Birden çok tam, değişiklik ve günlük yedeklemesini el ile geri yüklemek zorunda kalmadan veritabanlarını belirli bir zaman noktasına kurtarmak için portalını kullanın.
- **Hatalara yönelik Birleşik e-posta uyarıları**: Herhangi bir başarısızlık için birleştirilmiş e-posta bildirimlerini yapılandırın.
- **Rol tabanlı erişim denetimi**: Yedekleme ve geri yükleme işlemlerini Portal üzerinden kimin yönetebileceğini belirleme.

Tanıtım ile birlikte nasıl çalıştığı hakkında hızlı bir genel bakış için aşağıdaki videoyu izleyin:

> [!VIDEO https://www.youtube.com/embed/wmbANpHos_E]

SQL VM 'Leri için bu Azure Backup çözümü genel kullanıma sunulmuştur. Daha fazla bilgi için bkz. [SQL Server veritabanını Azure 'A yedekleme](../../../backup/backup-azure-sql-database.md).

## <a id="manual"></a>El ile yedekleme

SQL sanal makinelerinizdeki yedekleme ve geri yükleme işlemlerini el ile yönetmek isterseniz, kullanmakta olduğunuz SQL Server sürümüne bağlı olarak birkaç seçenek vardır. Yedekleme ve geri yüklemeye genel bir bakış için, SQL Server sürümünüze bağlı olarak aşağıdaki makalelerden birine bakın:

- [SQL Server 2016 ve üzeri için yedekleme ve geri yükleme](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases)
- [SQL Server 2014 için yedekleme ve geri yükleme](https://msdn.microsoft.com/library/ms187048%28v=sql.120%29.aspx)
- [SQL Server 2012 için yedekleme ve geri yükleme](https://msdn.microsoft.com/library/ms187048%28v=sql.110%29.aspx)
- [SQL Server SQL Server 2008 R2 için yedekleme ve geri yükleme](https://msdn.microsoft.com/library/ms187048%28v=sql.105%29.aspx)
- [SQL Server 2008 için yedekleme ve geri yükleme](https://msdn.microsoft.com/library/ms187048%28v=sql.100%29.aspx)

Aşağıdaki bölümlerde, birkaç el ile yedekleme ve geri yükleme seçeneği daha ayrıntılı olarak açıklanır.

### <a name="backup-to-attached-disks"></a>Ekli disklere yedekleme

Azure VM 'lerinde çalışan SQL Server için, yedekleme dosyalarının hedefi için VM 'deki bağlı diskleri kullanarak yerel yedekleme ve geri yükleme tekniklerini kullanabilirsiniz. Ancak, [sanal makinenin boyutuna](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)bağlı olarak bir Azure sanal makinesine iliştirebilmeniz için disk sayısı sınırlıdır. Ayrıca, dikkate alınması gereken disk yönetimi ek yükü de vardır.

SQL Server Management Studio (SSMS) veya Transact-SQL kullanarak tam bir veritabanı yedeklemesini el ile oluşturma hakkında bir örnek için bkz. [tam bir veritabanı yedeklemesi oluşturma](https://docs.microsoft.com/sql/relational-databases/backup-restore/create-a-full-database-backup-sql-server).

### <a name="backup-to-url"></a>URL 'ye yedekleme

SQL Server 2012 SP1 CU2 UYGULAMAZSANıZ ' den başlayarak, URL 'ye yedekleme olarak da bilinen BLOB depolama Microsoft Azure doğrudan yedekleyebilir ve geri yükleyebilirsiniz. SQL Server 2016, bu özellik için aşağıdaki geliştirmeleri de kullanıma sunmuştur:

| 2016 geliştirmesi | Ayrıntılar |
| --- | --- |
| **Erit** |Microsoft Azure Blob depolama alanına yedeklerken, SQL Server 2016, büyük veritabanlarının yedeklenmesini etkinleştirmek için en fazla 12,8 TB 'a kadar birden çok blob 'a yedeklemeyi destekler. |
| **Anlık görüntü yedeklemesi** |SQL Server dosya-anlık görüntü yedeklemesi Azure anlık görüntülerini kullanarak Azure Blob Storage hizmeti kullanılarak depolanan veritabanı dosyaları için neredeyse anlık yedeklemeler ve hızlı geri yüklemeler sağlar. Bu özellik, yedekleme ve geri yükleme ilkelerinizi basitleştirmenizi sağlar. Dosya anlık görüntüsü yedeklemesi, zaman içinde geri yükleme noktasını da destekler. Daha fazla bilgi için bkz. [Azure 'Da veritabanı dosyaları Için anlık görüntü yedeklemeleri](https://docs.microsoft.com/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure). |

Daha fazla bilgi için, SQL Server sürümünüze bağlı olarak aşağıdaki makalelerden birine bakın:

- **SQL Server 2016/2017**: [URL 'ye yedekleme SQL Server](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service)
- **SQL Server 2014**: [URL 'ye SQL Server 2014 yedeklemesi](https://msdn.microsoft.com/library/jj919148%28v=sql.120%29.aspx)
- **SQL Server 2012**: [URL 'ye SQL Server 2012 yedeklemesi](https://msdn.microsoft.com/library/jj919148%28v=sql.110%29.aspx)

### <a name="managed-backup"></a>Yönetilen yedekleme

SQL Server 2014 ' den başlayarak, yönetilen yedekleme, Azure depolama 'ya yedeklemelerin oluşturulmasını otomatikleştirir. Arka planda yönetilen yedekleme, bu makalenin önceki bölümünde açıklanan URL 'ye yedekleme özelliğini kullanır. Yönetilen yedekleme Ayrıca, SQL Server VM otomatik yedekleme hizmetini destekleyen temel bir özelliktir.

SQL Server 2016 ' den başlayarak, yönetilen yedekleme zamanlama, sistem veritabanı yedeklemesi ve tam ve günlük yedekleme sıklığı için ek seçenekler aldı.

Daha fazla bilgi için, SQL Server sürümünüze bağlı olarak aşağıdaki makalelerden birine bakın:

- [SQL Server 2016 ve üzeri için Microsoft Azure yönetilen yedekleme](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure)
- [SQL Server 2014 için Microsoft Azure yönetilen yedekleme](https://msdn.microsoft.com/library/dn449496%28v=sql.120%29.aspx)

## <a name="decision-matrix"></a>Karar matrisi

Aşağıdaki tabloda, Azure 'daki SQL Server sanal makineler için her yedekleme ve geri yükleme seçeneğinin özellikleri özetlenmektedir.

|| **Otomatik Yedekleme** | **SQL için Azure Backup** | **El ile yedekleme** |
|---|---|---|---|
| Ek Azure hizmeti gerektirir |   | ![Evet](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Azure portal 'de yedekleme ilkesini yapılandırma | ![Evet](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Evet](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Azure portal veritabanlarını geri yükleme |   | ![Evet](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Birden çok sunucuyu tek bir panoda yönetme |   | ![Evet](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Belirli bir noktaya geri yükleme | ![Evet](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Evet](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Evet](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| 15 dakikalık kurtarma noktası hedefi (RPO) | ![Evet](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Evet](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Evet](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| Kısa vadeli yedekleme bekletme ilkesi (gün) | ![Evet](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Evet](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Uzun süreli yedekleme bekletme ilkesi (aylar, yıllar) |   | ![Evet](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| SQL Server her zaman açık için yerleşik destek |   | ![Evet](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Azure depolama hesaplarına yedekleme | ![Evet](./media/virtual-machines-windows-sql-backup-recovery/yes.png)otomatik | ![Evet](./media/virtual-machines-windows-sql-backup-recovery/yes.png)otomatik | ![Evet](./media/virtual-machines-windows-sql-backup-recovery/yes.png)(müşteri tarafından yönetilen) |
| Depolama ve yedekleme dosyalarının yönetimi | | ![Evet](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |  |
| VM 'deki bağlı disklere yedekleme |   |   | ![Evet](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| Merkezi özelleştirilebilir yedekleme raporları |   | ![Evet](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Hatalara yönelik Birleşik e-posta uyarıları |   | ![Evet](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| İzlemeyi Azure Izleyici günlüklerine göre özelleştirme |   | ![Evet](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| SSMS veya Transact-SQL betikleri ile yedekleme işlerini izleme | ![Evet](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Evet](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Evet](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| SSMS veya Transact-SQL betiklerine sahip veritabanlarını geri yükleme | ![Evet](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   | ![Evet](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |

## <a name="next-steps"></a>Sonraki adımlar

SQL Server dağıtımınızı bir Azure VM 'ye planlarken, sağlama kılavuzunu aşağıdaki kılavuzda bulabilirsiniz: [Azure Portal bir Windows SQL Server sanal makinesi sağlama](virtual-machines-windows-portal-sql-server-provision.md).

Verilerinizi geçirmek için yedekleme ve geri yükleme kullanılabilse de, bir Azure VM üzerinde SQL Server daha kolay veri geçiş yolları vardır. Geçiş seçenekleri ve önerileri hakkında tam bir açıklama için bkz. bir [veritabanını Azure VM 'de SQL Server geçirme](virtual-machines-windows-migrate-sql.md).
