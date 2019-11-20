---
title: Bir veritabanını yedekten geri yükleme
description: Azure SQL veritabanı 'nı 35 güne kadar geri almanıza olanak sağlayan bir noktadan noktaya geri yükleme hakkında bilgi edinin.
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab, danil
ms.date: 09/26/2019
ms.openlocfilehash: 1c8717614ec59ef210c7340f70ddedd7f7f86f88
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74091958"
---
# <a name="recover-an-azure-sql-database-by-using-automated-database-backups"></a>Otomatik veritabanı yedeklemeleri kullanarak bir Azure SQL veritabanını kurtarma

Varsayılan olarak, Azure SQL veritabanı yedeklemeleri coğrafi olarak çoğaltılan BLOB depolama alanında (RA-GRS depolama türü) depolanır. [Otomatik veritabanı yedeklemeleri](sql-database-automated-backups.md)kullanılarak veritabanı kurtarma için aşağıdaki seçenekler kullanılabilir. Şunları yapabilirsiniz:

- Aynı SQL veritabanı sunucusunda, bekletme dönemi içinde belirli bir zaman noktasına kurtarılan yeni bir veritabanı oluşturun.
- Aynı SQL veritabanı sunucusunda bir veritabanı oluşturun ve silinen bir veritabanının silme zamanına kurtarıldı.
- Aynı bölgedeki herhangi bir SQL veritabanı sunucusunda yeni bir veritabanı oluşturun ve en son yedeklemelerin bulunduğu noktaya kurtarıldı.
- Diğer herhangi bir bölgedeki herhangi bir SQL veritabanı sunucusunda yeni bir veritabanı oluşturun ve en son çoğaltılan yedeklemelerin noktasına kurtarıldı.

[Yedekleme uzun süreli saklama](sql-database-long-term-retention.md)yapılandırdıysanız, HERHANGI bir SQL veritabanı sunucusunda uzun süreli saklama yedeklemesinden yeni bir veritabanı da oluşturabilirsiniz.

> [!IMPORTANT]
> Geri yükleme sırasında var olan bir veritabanının üzerine yazamaz.

Standart veya Premium hizmet katmanlarını kullanırken, veritabanı geri yüklemeniz ek bir depolama maliyeti gerektirebilir. Geri yüklenen veritabanının en büyük boyutu hedef veritabanının hizmet katmanına ve performans düzeyine dahil edilen depolama miktarından daha büyükse, ek maliyet tahakkuk edilir. Ek depolamanın fiyatlandırma ayrıntıları için bkz. [SQL Veritabanı fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/sql-database/). Kullanılan alanın gerçek miktarı dahil edilen depolama miktarından azsa, maksimum veritabanı boyutunu dahil edilen miktara ayarlayarak bu ekstra maliyetten kaçınabilirsiniz.

## <a name="recovery-time"></a>Kurtarma zamanı

Otomatik veritabanı yedeklemeleri kullanarak bir veritabanını geri yüklemeye yönelik kurtarma süresi, birkaç faktörden etkilenir:

- Veritabanının boyutu.
- Veritabanının işlem boyutu.
- Dahil edilen işlem günlüklerinin sayısı.
- Geri yükleme noktasına kurtarmak için yeniden yürütülmesi gereken etkinlik miktarı.
- Geri yükleme farklı bir bölgeye ise ağ bant genişliği.
- Hedef bölgede işlenen eşzamanlı geri yükleme isteklerinin sayısı.

Büyük veya çok etkin bir veritabanı için geri yükleme birkaç saat sürebilir. Bir bölgede uzun süren bir kesinti varsa, olağanüstü durum kurtarma için yüksek sayıda coğrafi geri yükleme isteği başlatılabilir. Çok sayıda istek olduğunda, bireysel veritabanlarının kurtarma süresi artabilir. Çoğu veritabanı geri yükleme işleminin 12 saatten az olması tamamlanır.

Tek bir abonelik için, eş zamanlı geri yükleme isteği sayısında sınırlamalar vardır. Bu sınırlamalar, uzun süreli saklama yedeklemesinden gelen zaman içindeki tüm geri yüklemeler, coğrafi geri yüklemeler ve geri yüklemeler için geçerlidir.

| | **İşlenmekte olan eşzamanlı istek sayısı üst sınırı** | **Gönderilen en fazla eşzamanlı istek sayısı** |
| :--- | --: | --: |
|Tek veritabanı (abonelik başına)|10|60|
|Elastik havuz (havuz başına)|4|200|
||||

Sunucunun tamamını geri yüklemek için yerleşik bir yöntem yoktur. Bu görevi nasıl gerçekleştireceğinizi gösteren bir örnek için bkz. [Azure SQL veritabanı: tam sunucu kurtarma](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666).

> [!IMPORTANT]
> Otomatik yedeklemeleri kullanarak kurtarmak için, abonelikte SQL Server katkıda bulunan rolünün bir üyesi olmanız veya abonelik sahibi olmanız gerekir. Daha fazla bilgi için bkz. [RBAC: yerleşik roller](../role-based-access-control/built-in-roles.md). Azure portal, PowerShell veya REST API kullanarak kurtarma yapabilirsiniz. Transact-SQL ' i kullanamazsınız.

## <a name="point-in-time-restore"></a>Belirli bir noktaya geri yükleme

Tek başına, havuza alınmış veya örnek veritabanını Azure portal, [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)veya [REST API](https://docs.microsoft.com/rest/api/sql/databases)kullanarak daha önceki bir zaman noktasına geri yükleyebilirsiniz. İstek, geri yüklenen veritabanı için herhangi bir hizmet katmanını veya işlem boyutunu belirtebilir. Veritabanını geri yüklediğiniz sunucuda yeterli kaynaklara sahip olduğunuzdan emin olun. Bu tamamlandığında, geri yükleme özgün veritabanıyla aynı sunucuda yeni bir veritabanı oluşturur. Geri yüklenen veritabanı, hizmet katmanına ve işlem boyutuna bağlı olarak normal ücretler üzerinden ücretlendirilir. Veritabanı geri yüklemesi tamamlanana kadar ücretlendirilmezsiniz.

Genellikle kurtarma amacıyla bir veritabanını önceki bir noktaya geri yüklemeniz gerekir. Geri yüklenen veritabanını özgün veritabanının yerini alacak şekilde kabul edebilir veya özgün veritabanını güncelleştirmek için bir veri kaynağı olarak kullanabilirsiniz.

- **Veritabanı değiştirme**

  Geri yüklenen veritabanını özgün veritabanının yerini alacak şekilde düşünüyorsanız, özgün veritabanının işlem boyutunu ve hizmet katmanını belirtmeniz gerekir. Ardından özgün veritabanını yeniden adlandırabilir ve T-SQL ' i [alter database](/sql/t-sql/statements/alter-database-azure-sql-database) komutunu kullanarak geri yüklenen veritabanına özgün adı verebilirsiniz.

- **Veri kurtarma**

  Bir kullanıcı veya uygulama hatasından kurtarmak için geri yüklenen veritabanından veri almayı planlıyorsanız, geri yüklenen veritabanından veri çıkaran ve özgün veritabanına uygulanan bir veri kurtarma betiği yazmanız ve yürütmeniz gerekir. Geri yükleme işleminin tamamlanması uzun zaman alabilir, ancak geri yükleme veritabanı geri yükleme işlemi boyunca veritabanı listesinde görünür. Veritabanını geri yükleme sırasında silerseniz geri yükleme işlemi iptal edilir ve geri yüklemeyi tamamlamamış olan veritabanı için ücretlendirilmeyecektir.
  
### <a name="point-in-time-restore-by-using-azure-portal"></a>Azure portal kullanarak zaman içindeki bir noktaya geri yükleme

Tek bir SQL veritabanını veya örnek veritabanını, Azure portal geri yüklemek istediğiniz veritabanının genel bakış dikey penceresinden zaman içindeki bir noktaya kurtarabilirsiniz.

#### <a name="single-azure-sql-database"></a>Tek Azure SQL veritabanı

Tek veya havuza alınmış bir veritabanını Azure portal kullanarak zaman içinde bir noktaya kurtarmak için, veritabanına genel bakış sayfasını açın ve araç çubuğunda **geri yükle** ' yi seçin. Yedekleme kaynağını seçin ve yeni bir veritabanının oluşturulacağı belirli bir noktaya yedekleme noktasını seçin. 

  ![Veritabanı geri yükleme seçeneklerinin ekran görüntüsü](./media/sql-database-recovery-using-backups/pitr-backup-sql-database-annotated.png)

#### <a name="managed-instance-database"></a>Yönetilen örnek veritabanı

Yönetilen bir örnek veritabanını Azure portal kullanarak bir noktaya kurtarmak için, veritabanına genel bakış sayfasını açın ve araç çubuğunda **geri yükle** ' yi seçin. Yeni bir veritabanının oluşturulacağı bir zaman noktası yedekleme noktasını seçin. 

  ![Veritabanı geri yükleme seçeneklerinin ekran görüntüsü](./media/sql-database-recovery-using-backups/pitr-backup-managed-instance-annotated.png)

> [!TIP]
> Bir veritabanını bir yedekten program aracılığıyla geri yüklemek için, bkz. [Otomatik yedeklemeleri kullanarak kurtarma gerçekleştirme](sql-database-recovery-using-backups.md).

## <a name="deleted-database-restore"></a>Veritabanı geri yükleme silindi

Silinen bir veritabanını, aynı SQL veritabanı sunucusunda veya aynı yönetilen örnek üzerinde, silme zamanına veya önceki bir zaman noktasına geri yükleyebilirsiniz. Bunu Azure portal, [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)veya [Rest (createmode = restore)](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)aracılığıyla gerçekleştirebilirsiniz. Yedekten yeni bir veritabanı oluşturarak silinen bir veritabanını geri yükleyin.

> [!IMPORTANT]
> Bir Azure SQL veritabanı sunucusunu veya yönetilen örneği silerseniz, tüm veritabanları da silinir ve kurtarılamaz. Silinen bir sunucuyu veya yönetilen örneği geri alamazsınız.

### <a name="deleted-database-restore-by-using-the-azure-portal"></a>Azure portal kullanarak veritabanı geri yükleme silindi

Silinen veritabanlarını sunucu ve örnek kaynağından Azure portal geri yükleyin.

#### <a name="single-azure-sql-database"></a>Tek Azure SQL veritabanı

Azure portal kullanarak tek veya havuza alınmış silinen bir veritabanını kurtarmak için, sunucuya genel bakış sayfasını açın ve **silinen veritabanları**' nı seçin. Geri yüklemek istediğiniz silinen bir veritabanını seçin ve yedeklemeden geri yüklenen verilerle oluşturulacak yeni veritabanının adını yazın.

  ![Silinen Azure SQL veritabanı 'nı geri yükleme ekran görüntüsü](./media/sql-database-recovery-using-backups/restore-deleted-sql-database-annotated.png)

#### <a name="managed-instance-database"></a>Yönetilen örnek veritabanı

Azure portal kullanarak yönetilen bir veritabanını kurtarmak için, yönetilen örneğe genel bakış sayfasını açın ve **silinen veritabanları**' nı seçin. Geri yüklemek istediğiniz silinen bir veritabanını seçin ve yedeklemeden geri yüklenen verilerle oluşturulacak yeni veritabanının adını yazın.

  ![Silinen Azure SQL örneği veritabanını geri yükleme ekran görüntüsü](./media/sql-database-recovery-using-backups/restore-deleted-sql-managed-instance-annotated.png)

### <a name="deleted-database-restore-by-using-powershell"></a>PowerShell kullanarak veritabanı geri yükleme silindi

Azure SQL veritabanı için silinen bir veritabanını ve PowerShell 'i kullanarak yönetilen bir örneği geri yüklemek için aşağıdaki örnek betikleri kullanın.

#### <a name="single-azure-sql-database"></a>Tek Azure SQL veritabanı

Silinen bir Azure SQL veritabanını geri yüklemeyi gösteren örnek bir PowerShell betiği için bkz. [PowerShell kullanarak BIR SQL veritabanını geri yükleme](scripts/sql-database-restore-database-powershell.md).

#### <a name="managed-instance-database"></a>Yönetilen örnek veritabanı

Silinen bir örnek veritabanının nasıl geri yükleneceğini gösteren örnek bir PowerShell betiği için bkz. [PowerShell kullanarak, yönetilen örnekte silinen veritabanını geri yükleme](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../recreate-dropped-database-on-azure-sql-managed-instance). 

> [!TIP]
> Silinen bir veritabanını program aracılığıyla geri yüklemek için, bkz. [Otomatik yedeklemeleri kullanarak kurtarma gerçekleştirme](sql-database-recovery-using-backups.md).

## <a name="geo-restore"></a>Coğrafi Geri Yükleme

Her bir Azure bölgesindeki sunucuda bir SQL veritabanını, en son coğrafi çoğaltılan yedeklerden geri yükleyebilirsiniz. Coğrafi geri yükleme, kaynak olarak coğrafi olarak çoğaltılan bir yedeklemeyi kullanır. Veritabanı veya veri merkezi bir kesinti nedeniyle erişilemez olsa bile coğrafi geri yükleme isteğinde bulunabilir.

Coğrafi geri yükleme, veritabanınız barındırma bölgesindeki bir olay nedeniyle kullanılamadığında varsayılan kurtarma seçeneğidir. Veritabanını başka bir bölgedeki sunucuya geri yükleyebilirsiniz. Bir yedeklemenin ne zaman alındığı ve farklı bir bölgedeki Azure blobuna coğrafi olarak çoğaltılma arasında bir gecikme vardır. Sonuç olarak, geri yüklenen veritabanı özgün veritabanının arkasında bir saate kadar sürebilir. Aşağıdaki çizimde, başka bir bölgedeki son kullanılabilir yedeklemeden bir veritabanı geri yüklemesi gösterilmektedir.

![Coğrafi geri yükleme grafiği](./media/sql-database-geo-restore/geo-restore-2.png)

### <a name="geo-restore-by-using-the-azure-portal"></a>Azure portal kullanarak coğrafi geri yükleme

Azure portal, yeni bir tek veya yönetilen örnek veritabanı oluşturur ve kullanılabilir bir coğrafi geri yükleme yedeklemesi seçersiniz. Yeni oluşturulan veritabanı, coğrafi olarak geri yüklenen yedekleme verilerini içerir.

#### <a name="single-azure-sql-database"></a>Tek Azure SQL veritabanı

Seçtiğiniz bölgedeki ve sunucudaki Azure portal tek bir SQL veritabanını coğrafi olarak geri yüklemek için şu adımları izleyin:

1. **Panodan**, **Ekle** > **SQL veritabanı oluştur**' u seçin. **Temel bilgiler** sekmesinde, gerekli bilgileri girin.
2. **Ek ayarlar**' ı seçin.
3. **Mevcut verileri kullan**için **Yedekle**' yi seçin.
4. **Yedekleme**için, kullanılabilir coğrafi geri yükleme yedeklemeleri listesinden bir yedekleme seçin.

    ![SQL veritabanı seçeneklerini oluştur ekran görüntüsü](./media/sql-database-recovery-using-backups/geo-restore-azure-sql-database-list-annotated.png)

Yedeklemeden yeni bir veritabanı oluşturma işlemini tamamlar. Tek bir Azure SQL veritabanı oluşturduğunuzda, geri yüklenen coğrafi geri yükleme yedeklemesini içerir.

#### <a name="managed-instance-database"></a>Yönetilen örnek veritabanı

Yönetilen bir örnek veritabanını Azure portal tercih ettiğiniz bir bölgede varolan bir yönetilen örneğe coğrafi olarak geri yüklemek için veritabanının geri yüklenmesini istediğiniz yönetilen örneği seçin. Şu adımları uygulayın:

1. **Yeni veritabanı**' nı seçin.
2. İstenen bir veritabanı adı yazın.
3. **Mevcut verileri kullan**altında **Yedekle**' yi seçin.
4. Kullanılabilir coğrafi geri yükleme yedeklemeleri listesinden bir yedekleme seçin.

    ![Yeni veritabanı seçeneklerinin ekran görüntüsü](./media/sql-database-recovery-using-backups/geo-restore-sql-managed-instance-list-annotated.png)

Yeni bir veritabanı oluşturma işlemini tamamlar. Örnek veritabanını oluştururken, geri yüklenen coğrafi geri yükleme yedeklemesini içerir.

### <a name="geo-restore-by-using-powershell"></a>PowerShell kullanarak coğrafi geri yükleme

#### <a name="single-azure-sql-database"></a>Tek Azure SQL veritabanı

Tek bir SQL veritabanı için coğrafi geri yükleme yapmayı gösteren bir PowerShell betiği için bkz. [Azure SQL tek veritabanını daha önceki bir noktaya geri yüklemek Için PowerShell 'ı kullanma](scripts/sql-database-restore-database-powershell.md).

#### <a name="managed-instance-database"></a>Yönetilen örnek veritabanı

Yönetilen örnek veritabanı için coğrafi geri yükleme yapmayı gösteren bir PowerShell betiği için bkz. [yönetilen örnek veritabanını başka bir coğrafi bölgeye geri yüklemek Için PowerShell kullanma](scripts/sql-managed-instance-restore-geo-backup.md).

### <a name="geo-restore-considerations"></a>Coğrafi geri yükleme konuları

Coğrafi ikincil veritabanında bir zaman içinde geri yükleme gerçekleştiremezsiniz. Bunu yalnızca birincil veritabanında yapabilirsiniz. Bir kesinti durumundan kurtulmak için coğrafi geri yükleme kullanma hakkında ayrıntılı bilgi için bkz. [bir kesinti Ile kurtarma](sql-database-disaster-recovery.md).

> [!IMPORTANT]
> Coğrafi geri yükleme, SQL veritabanında bulunan en temel olağanüstü durum kurtarma çözümüdür. Kurtarma noktası hedefi (RPO) ile 1 saate eşit ve yaklaşık 12 saate kadar tahmini kurtarma süresi ile otomatik olarak oluşturulan coğrafi olarak çoğaltılan yedeklemeleri kullanır. Bu, hedef bölgenin bir bölgesel kesintiden sonra veritabanlarınızı geri yükleme kapasitesine sahip olacağını garanti etmez, çünkü bu da keskin bir istek artışı olabilir. Uygulamanız görece küçük veritabanları kullanıyorsa ve iş için kritik öneme sahip değilse, coğrafi geri yükleme uygun bir olağanüstü durum kurtarma çözümüdür. Büyük veritabanları gerektiren iş açısından kritik uygulamalar ve iş sürekliliği sağlamak için [otomatik yük devretme grupları](sql-database-auto-failover-group.md)kullanın. Daha düşük bir RPO ve kurtarma süresi hedefi sunar ve kapasite her zaman garanti edilir. İş sürekliliği seçenekleri hakkında daha fazla bilgi için bkz. [iş sürekliliği konusuna genel bakış](sql-database-business-continuity.md).

## <a name="programmatically-performing-recovery-by-using-automated-backups"></a>Otomatik yedeklemeleri kullanarak program aracılığıyla kurtarma gerçekleştirme

Kurtarma için Azure PowerShell veya REST API de kullanabilirsiniz. Aşağıdaki tablolarda kullanılabilen komut kümesi açıklanır.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager modülü Azure SQL veritabanı tarafından hala desteklenmektedir, ancak gelecekteki tüm geliştirmeler az. SQL modülüne yöneliktir. Bu cmdlet 'ler için bkz. [Azurerd. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Az Module ve Azurerd modüllerinde komutların bağımsız değişkenleri harika bir ölçüde benzerdir.

#### <a name="single-azure-sql-database"></a>Tek Azure SQL veritabanı

Tek başına veya havuza alınmış bir veritabanını geri yüklemek için bkz. [restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase).

  | Cmdlet | Açıklama |
  | --- | --- |
  | [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) |Bir veya daha fazla veritabanını alır. |
  | [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup) | Geri yükleyebileceğiniz, silinmiş bir veritabanını alır. |
  | [Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup) |Bir veritabanının coğrafi olarak yedekli bir yedeklemesini alır. |
  | [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase) |SQL veritabanını geri yükler. |

  > [!TIP]
  > Bir veritabanının bir noktadan noktaya geri yüklemesini nasıl gerçekleştirekullanacağınızı gösteren örnek bir PowerShell betiği için bkz. [PowerShell kullanarak BIR SQL veritabanını geri yükleme](scripts/sql-database-restore-database-powershell.md).

#### <a name="managed-instance-database"></a>Yönetilen örnek veritabanı

Yönetilen örnek veritabanını geri yüklemek için bkz. [restore-Azsqlınstancedatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase).

  | Cmdlet | Açıklama |
  | --- | --- |
  | [Get-Azsqlınstance](/powershell/module/az.sql/get-azsqlinstance) |Bir veya daha fazla yönetilen örneği alır. |
  | [Get-Azsqlınstancedatabase](/powershell/module/az.sql/get-azsqlinstancedatabase) | Bir örnek veritabanı alır. |
  | [Restore-Azsqlınstancedatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase) |Örnek veritabanını geri yükler. |

### <a name="rest-api"></a>REST API

REST API kullanarak tek veya havuza alınmış bir veritabanını geri yüklemek için:

| API | Açıklama |
| --- | --- |
| [REST (createMode = kurtarma)](https://docs.microsoft.com/rest/api/sql/databases) |Bir veritabanını geri yükler. |
| [Veritabanı oluşturma veya güncelleştirme durumunu al](https://docs.microsoft.com/rest/api/sql/operations) |Geri yükleme işlemi sırasında durumu döndürür. |

### <a name="azure-cli"></a>Azure CLI

#### <a name="single-azure-sql-database"></a>Tek Azure SQL veritabanı

Azure CLı kullanarak tek veya havuza alınmış bir veritabanını geri yüklemek için, bkz. [az SQL DB restore](/cli/azure/sql/db#az-sql-db-restore).

#### <a name="managed-instance-database"></a>Yönetilen örnek veritabanı

Azure CLı kullanarak yönetilen bir örnek veritabanını geri yüklemek için, bkz. [az SQL mıdb restore](/cli/azure/sql/midb#az-sql-midb-restore).

## <a name="summary"></a>Özet

Otomatik yedeklemeler, veritabanlarınızı Kullanıcı ve uygulama hatalarından, yanlışlıkla veritabanı silmeye ve uzun kesintilere karşı korur. Bu yerleşik yetenek tüm hizmet katmanları ve işlem boyutları için kullanılabilir.

## <a name="next-steps"></a>Sonraki adımlar

- [İş sürekliliği genel bakış](sql-database-business-continuity.md)
- [SQL veritabanı otomatik yedeklemeleri](sql-database-automated-backups.md)
- [Uzun vadeli bekletme](sql-database-long-term-retention.md)
- Daha hızlı kurtarma seçenekleri hakkında daha fazla bilgi edinmek için bkz. [etkin coğrafi çoğaltma](sql-database-active-geo-replication.md) veya [otomatik yük devretme grupları](sql-database-auto-failover-group.md).
