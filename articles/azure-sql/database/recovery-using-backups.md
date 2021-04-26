---
title: Bir veritabanını yedekten geri yükleme
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Azure SQL veritabanı 'nda bir veritabanını veya Azure SQL yönetilen örneğindeki bir örneği 35 güne kadar geri almanıza olanak tanıyan noktadan noktaya geri yükleme hakkında bilgi edinin.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein, danil
ms.date: 11/13/2020
ms.openlocfilehash: 670176d7478ddab3d17e15526df512dfa7e99fd4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762092"
---
# <a name="recover-using-automated-database-backups---azure-sql-database--sql-managed-instance"></a>Otomatik veritabanı yedeklemeleri kullanarak kurtarma-SQL yönetilen örnek & Azure SQL veritabanı
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

[Otomatik veritabanı yedeklemeleri](automated-backups-overview.md)kullanılarak veritabanı kurtarma için aşağıdaki seçenekler kullanılabilir. Seçenekleriniz şunlardır:

- Aynı sunucuda, bekletme döneminde belirtilen bir zaman noktasına kurtarılan yeni bir veritabanı oluşturun.
- Silinen bir veritabanı için silme zamanına kurtarıldığı aynı sunucuda bir veritabanı oluşturun.
- Aynı bölgedeki herhangi bir sunucuda, en son yedeklemelerin bulunduğu noktaya kurtarıldı yeni bir veritabanı oluşturun.
- Diğer bir bölgedeki herhangi bir sunucuda, en son çoğaltılan yedeklemelerin noktasına kurtarıldığı yeni bir veritabanı oluşturun.

[Yedekleme uzun süreli saklama](long-term-retention-overview.md)yapılandırdıysanız, herhangi bir sunucuda uzun süreli bekletme yedeklemesinden yeni bir veritabanı da oluşturabilirsiniz.

> [!IMPORTANT]
> Geri yükleme sırasında var olan bir veritabanının üzerine yazamaz.

Standart veya Premium hizmet katmanını kullanırken, veritabanı geri yüklemeniz ek bir depolama maliyeti gerektirebilir. Geri yüklenen veritabanının en büyük boyutu hedef veritabanının hizmet katmanına ve performans düzeyine dahil edilen depolama miktarından daha büyükse, ek maliyet tahakkuk edilir. Ek depolamanın fiyatlandırma ayrıntıları için bkz. [SQL Veritabanı fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/sql-database/). Kullanılan alanın gerçek miktarı dahil edilen depolama miktarından azsa, maksimum veritabanı boyutunu dahil edilen miktara ayarlayarak bu ekstra maliyetten kaçınabilirsiniz.

## <a name="recovery-time"></a>Kurtarma zamanı

Otomatik veritabanı yedeklemeleri kullanarak bir veritabanını geri yüklemeye yönelik kurtarma süresi, birkaç faktörden etkilenir:

- Veritabanının boyutu.
- Veritabanının işlem boyutu.
- Dahil edilen işlem günlüklerinin sayısı.
- Geri yükleme noktasına kurtarmak için yeniden yürütülmesi gereken etkinlik miktarı.
- Geri yükleme farklı bir bölgeye ise ağ bant genişliği.
- Hedef bölgede işlenen eşzamanlı geri yükleme isteklerinin sayısı.

Büyük veya çok etkin bir veritabanı için geri yükleme birkaç saat sürebilir. Bölgede uzun süren bir kesinti olursa, olağanüstü durum kurtarma için çok sayıda coğrafi geri yükleme isteğinin başlatılması mümkündür. Çok sayıda istek olduğunda tek tek veritabanlarının kurtarma süresi uzayabilir. Çoğu veritabanı son geri yükleme işlemi 12 saatten az sürer.

Tek bir abonelik için eşzamanlı geri yükleme isteklerinin sayısında sınırlamalar söz konusudur. Bu sınırlamalar belirli bir noktaya geri yükleme, coğrafi geri yükleme ve uzun süreli saklama yedeklemelerinden geri yükleme işlemlerinin her türlü bileşimine uygulanır.

| **Dağıtım seçeneği** | **İşlenmekte olan eşzamanlı istek sayısı üst sınırı** | **Gönderilmekte olan eşzamanlı istek sayısı üst sınırı** |
| :--- | --: | --: |
|**Tek veritabanı (abonelik başına)**|30|100|
|**Elastik havuz (havuz başına)**|4|2000|


Sunucunun tamamını geri yüklemek için yerleşik bir yöntem yoktur. Bu görevi nasıl gerçekleştireceğinizi gösteren bir örnek için bkz. [Azure SQL veritabanı: tam sunucu kurtarma](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666).

> [!IMPORTANT]
> Otomatik yedeklemeleri kullanarak kurtarmak için, abonelikte SQL Server katkıda bulunan rolün veya SQL yönetilen örnek katılımcısı rolünün (kurtarma hedefine bağlı olarak) üyesi olmanız veya abonelik sahibi olmanız gerekir. Daha fazla bilgi için bkz. [Azure RBAC: yerleşik roller](../../role-based-access-control/built-in-roles.md). Azure portal, PowerShell veya REST API kullanarak kurtarma yapabilirsiniz. Transact-SQL ' i kullanamazsınız.

## <a name="point-in-time-restore"></a>Belirli bir noktaya geri yükleme

Tek başına, havuza alınmış veya örnek veritabanını Azure portal, [PowerShell](/powershell/module/az.sql/restore-azsqldatabase)veya [REST API](/rest/api/sql/databases/createorupdate#creates-a-database-from-pointintimerestore.)kullanarak daha önceki bir zaman noktasına geri yükleyebilirsiniz. İstek, geri yüklenen veritabanı için herhangi bir hizmet katmanını veya işlem boyutunu belirtebilir. Veritabanını geri yüklediğiniz sunucuda yeterli kaynaklara sahip olduğunuzdan emin olun. 

Bu tamamlandığında, geri yükleme özgün veritabanıyla aynı sunucuda yeni bir veritabanı oluşturur. Geri yüklenen veritabanı, hizmet katmanına ve işlem boyutuna bağlı olarak normal ücretler üzerinden ücretlendirilir. Veritabanı geri yüklemesi tamamlanana kadar ücretlendirilmezsiniz.

Genellikle kurtarma amacıyla bir veritabanını önceki bir noktaya geri yüklemeniz gerekir. Geri yüklenen veritabanını özgün veritabanının yerini alacak şekilde kabul edebilir veya özgün veritabanını güncelleştirmek için bir veri kaynağı olarak kullanabilirsiniz.

- **Veritabanı değiştirme**

  Geri yüklenen veritabanını özgün veritabanının yerini alacak şekilde düşünüyorsanız, özgün veritabanının işlem boyutunu ve hizmet katmanını belirtmeniz gerekir. Ardından özgün veritabanını yeniden adlandırabilir ve T-SQL ' i [alter database](/sql/t-sql/statements/alter-database-azure-sql-database) komutunu kullanarak geri yüklenen veritabanına özgün adı verebilirsiniz.

- **Veri kurtarma**

  Bir kullanıcı veya uygulama hatasından kurtarmak için geri yüklenen veritabanından veri almayı planlıyorsanız, geri yüklenen veritabanından veri çıkaran ve özgün veritabanına uygulanan bir veri kurtarma betiği yazmanız ve yürütmeniz gerekir. Geri yükleme işleminin tamamlanması uzun zaman alabilir, ancak geri yükleme veritabanı geri yükleme işlemi boyunca veritabanı listesinde görünür. Veritabanını geri yükleme sırasında silerseniz geri yükleme işlemi iptal edilir ve geri yüklemeyi tamamlamamış olan veritabanı için ücretlendirilmeyecektir.
  
### <a name="point-in-time-restore-by-using-azure-portal"></a>Azure portal kullanarak zaman içindeki bir noktaya geri yükleme

Tek veya örnek bir veritabanını, Azure portal geri yüklemek istediğiniz veritabanının genel bakış dikey penceresinden zaman içinde bir noktaya kurtarabilirsiniz.

#### <a name="sql-database"></a>SQL Veritabanı

Azure portal kullanarak bir veritabanını zaman noktasına kurtarmak için veritabanına genel bakış sayfasını açın ve araç çubuğunda **geri yükle** ' yi seçin. Yedekleme kaynağını seçin ve yeni bir veritabanının oluşturulacağı belirli bir noktaya yedekleme noktasını seçin.

  ![SQL veritabanı için veritabanı geri yükleme seçeneklerinin ekran görüntüsü.](./media/recovery-using-backups/pitr-backup-sql-database-annotated.png)

#### <a name="sql-managed-instance"></a>SQL Yönetilen Örnek

Yönetilen bir örnek veritabanını Azure portal kullanarak bir noktaya kurtarmak için, veritabanına genel bakış sayfasını açın ve araç çubuğunda **geri yükle** ' yi seçin. Yeni bir veritabanının oluşturulacağı bir zaman noktası yedekleme noktasını seçin.

  ![SQL yönetilen örneği için veritabanı geri yükleme seçeneklerinin ekran görüntüsü.](./media/recovery-using-backups/pitr-backup-managed-instance-annotated.png)

> [!TIP]
> Bir veritabanını bir yedekten program aracılığıyla geri yüklemek için, bkz. [Otomatik yedeklemeleri kullanarak programlı kurtarma](recovery-using-backups.md).

## <a name="deleted-database-restore"></a>Veritabanı geri yükleme silindi

Silinen bir veritabanını, aynı sunucuda veya aynı yönetilen örnek üzerinde, silme zamanına veya önceki bir zaman noktasına geri yükleyebilirsiniz. Bunu Azure portal, [PowerShell](/powershell/module/az.sql/restore-azsqldatabase)veya [Rest (createmode = restore)](/rest/api/sql/databases/createorupdate)aracılığıyla gerçekleştirebilirsiniz. Yedekten yeni bir veritabanı oluşturarak silinen bir veritabanını geri yükleyin.

> [!IMPORTANT]
> Bir sunucu veya yönetilen örneği silerseniz, tüm veritabanları da silinir ve kurtarılamaz. Silinen bir sunucuyu veya yönetilen örneği geri alamazsınız.

### <a name="deleted-database-restore-by-using-the-azure-portal"></a>Azure portal kullanarak veritabanı geri yükleme silindi

Silinen veritabanlarını sunucudan veya yönetilen örnek kaynağından Azure portal geri yükleyebilirsiniz.

> [!TIP]
> Son silinen veritabanlarının Azure portal **silinen veritabanları** sayfasında görünmesi veya silinen veritabanlarının [programlı olarak](#programmatic-recovery-using-automated-backups)görüntülenmesi birkaç dakika sürebilir.

#### <a name="sql-database"></a>SQL Veritabanı

Silinen bir veritabanını Azure portal kullanarak silme zamanına kurtarmak için, sunucuya genel bakış sayfasını açın ve **silinen veritabanları**' nı seçin. Geri yüklemek istediğiniz silinen bir veritabanını seçin ve yedeklemeden geri yüklenen verilerle oluşturulacak yeni veritabanının adını yazın.

  ![Silinen veritabanını geri yükleme ekran görüntüsü](./media/recovery-using-backups/restore-deleted-sql-database-annotated.png)

#### <a name="sql-managed-instance"></a>SQL Yönetilen Örnek

Azure portal kullanarak yönetilen bir veritabanını kurtarmak için, yönetilen örneğe genel bakış sayfasını açın ve **silinen veritabanları**' nı seçin. Geri yüklemek istediğiniz silinen bir veritabanını seçin ve yedeklemeden geri yüklenen verilerle oluşturulacak yeni veritabanının adını yazın.

  ![Silinen Azure SQL yönetilen örnek veritabanını geri yükleme ekran görüntüsü](./media/recovery-using-backups/restore-deleted-sql-managed-instance-annotated.png)

### <a name="deleted-database-restore-by-using-powershell"></a>PowerShell kullanarak veritabanı geri yükleme silindi

SQL veritabanı veya SQL yönetilen örneği için silinen bir veritabanını PowerShell kullanarak geri yüklemek için aşağıdaki örnek betikleri kullanın.

#### <a name="sql-database"></a>SQL Veritabanı

Azure SQL veritabanı 'nda silinen bir veritabanının nasıl geri yükleneceğini gösteren örnek bir PowerShell betiği için bkz. [PowerShell kullanarak veritabanını geri yükleme](scripts/restore-database-powershell.md).

#### <a name="sql-managed-instance"></a>SQL Yönetilen Örnek

Silinen örnek veritabanının nasıl geri yükleneceğini gösteren örnek bir PowerShell betiği için bkz. [PowerShell kullanarak silinen örnek veritabanını geri yükleme](../managed-instance/point-in-time-restore.md#restore-a-deleted-database)

> [!TIP]
> Silinen bir veritabanını program aracılığıyla geri yüklemek için, bkz. [Otomatik yedeklemeleri kullanarak kurtarma gerçekleştirme](recovery-using-backups.md).

## <a name="geo-restore"></a>Coğrafi geri yükleme

> [!IMPORTANT]
> Coğrafi geri yükleme yalnızca SQL veritabanları veya coğrafi olarak yedekli [yedekleme depolama](automated-backups-overview.md#backup-storage-redundancy)ile yapılandırılmış yönetilen örnekler için kullanılabilir.

Herhangi bir SQL veritabanı sunucusundaki bir veritabanını veya herhangi bir Azure bölgesindeki yönetilen örnekteki bir örnek veritabanını, en son coğrafi çoğaltılan yedeklerden geri yükleyebilirsiniz. Coğrafi geri yükleme, kaynak olarak coğrafi olarak çoğaltılan bir yedeklemeyi kullanır. Veritabanı veya veri merkezi bir kesinti nedeniyle erişilemez olsa bile coğrafi geri yükleme isteğinde bulunabilir.

Coğrafi geri yükleme, veritabanınız barındırma bölgesindeki bir olay nedeniyle kullanılamadığında varsayılan kurtarma seçeneğidir. Veritabanını başka herhangi bir bölgedeki sunucuya geri yükleyebilirsiniz. Bir yedeklemenin ne zaman alındığı ve farklı bir bölgedeki Azure blobuna coğrafi olarak çoğaltılma arasında bir gecikme vardır. Sonuç olarak, geri yüklenen veritabanı özgün veritabanının arkasında bir saate kadar sürebilir. Aşağıdaki çizimde, başka bir bölgedeki son kullanılabilir yedeklemeden bir veritabanı geri yüklemesi gösterilmektedir.

![Coğrafi geri yükleme grafiği](./media/recovery-using-backups/geo-restore-2.png)

### <a name="geo-restore-by-using-the-azure-portal"></a>Azure portal kullanarak coğrafi geri yükleme

Azure portal, yeni bir tek veya yönetilen örnek veritabanı oluşturup kullanılabilir bir coğrafi geri yükleme yedeklemesi seçersiniz. Yeni oluşturulan veritabanı, coğrafi olarak geri yüklenen yedekleme verilerini içerir.

#### <a name="sql-database"></a>SQL Veritabanı

Seçtiğiniz bölgedeki ve sunucudaki Azure portal tek bir veritabanını coğrafi olarak geri yüklemek için şu adımları izleyin:

1. **Panodan**   >  **SQL veritabanı oluştur**' u seçin. **Temel bilgiler** sekmesinde, gerekli bilgileri girin.
2. **Ek ayarlar**' ı seçin.
3. **Mevcut verileri kullan** için **Yedekle**' yi seçin.
4. **Yedekleme** için, kullanılabilir coğrafi geri yükleme yedeklemeleri listesinden bir yedekleme seçin.

    ![SQL veritabanı seçeneklerini oluştur ekran görüntüsü](./media/recovery-using-backups/geo-restore-azure-sql-database-list-annotated.png)

Yedeklemeden yeni bir veritabanı oluşturma işlemini tamamlar. Azure SQL veritabanı 'nda bir veritabanı oluşturduğunuzda, geri yüklenen coğrafi geri yükleme yedeklemesini içerir.

#### <a name="sql-managed-instance"></a>SQL Yönetilen Örnek

Yönetilen bir örnek veritabanını Azure portal tercih ettiğiniz bir bölgede varolan bir yönetilen örneğe coğrafi olarak geri yüklemek için veritabanının geri yüklenmesini istediğiniz yönetilen örneği seçin. Şu adımları izleyin:

1. **Yeni veritabanı**' nı seçin.
2. İstenen bir veritabanı adı yazın.
3. **Mevcut verileri kullan** altında **Yedekle**' yi seçin.
4. Kullanılabilir coğrafi geri yükleme yedeklemeleri listesinden bir yedekleme seçin.

    ![Yeni veritabanı seçeneklerinin ekran görüntüsü](./media/recovery-using-backups/geo-restore-sql-managed-instance-list-annotated.png)

Yeni bir veritabanı oluşturma işlemini tamamlar. Örnek veritabanını oluştururken, geri yüklenen coğrafi geri yükleme yedeklemesini içerir.

### <a name="geo-restore-by-using-powershell"></a>PowerShell kullanarak coğrafi geri yükleme

#### <a name="sql-database"></a>SQL Veritabanı

Tek bir veritabanı için coğrafi geri yükleme yapmayı gösteren bir PowerShell betiği için, bkz. [tek bir veritabanını önceki bir noktaya geri yüklemek Için PowerShell 'ı kullanma](scripts/restore-database-powershell.md).

#### <a name="sql-managed-instance"></a>SQL Yönetilen Örnek

Yönetilen örnek veritabanı için coğrafi geri yükleme yapmayı gösteren bir PowerShell betiği için bkz. [yönetilen örnek veritabanını başka bir coğrafi bölgeye geri yüklemek Için PowerShell kullanma](../managed-instance/scripts/restore-geo-backup.md).

### <a name="geo-restore-considerations"></a>Coğrafi geri yükleme konuları

Coğrafi ikincil veritabanında bir zaman içinde geri yükleme gerçekleştiremezsiniz. Bunu yalnızca birincil veritabanında yapabilirsiniz. Bir kesinti durumundan kurtulmak için coğrafi geri yükleme kullanma hakkında ayrıntılı bilgi için bkz. [bir kesinti Ile kurtarma](../../key-vault/general/disaster-recovery-guidance.md).

> [!IMPORTANT]
> Coğrafi geri yükleme, SQL veritabanı ve SQL yönetilen örneğinde bulunan en temel olağanüstü durum kurtarma çözümüdür. Bir kurtarma noktası hedefi (RPO) ile en fazla 1 saat ve tahmini kurtarma süresi olan bir kurtarma noktası hedefi (RPO) ile otomatik olarak oluşturulan coğrafi olarak çoğaltılan yedeklemeleri kullanır. Bu, hedef bölgenin bir bölgesel kesintiden sonra veritabanlarınızı geri yükleme kapasitesine sahip olacağını garanti etmez, çünkü bu da keskin bir istek artışı olabilir. Uygulamanız görece küçük veritabanları kullanıyorsa ve iş için kritik öneme sahip değilse, coğrafi geri yükleme uygun bir olağanüstü durum kurtarma çözümüdür. 
>
> Büyük veritabanları gerektiren iş açısından kritik uygulamalar ve iş sürekliliği sağlamak için [otomatik yük devretme grupları](auto-failover-group-overview.md)kullanın. Daha düşük bir RPO ve kurtarma süresi hedefi sunar ve kapasite her zaman garanti edilir. 
>
> İş sürekliliği seçenekleri hakkında daha fazla bilgi için bkz. [iş sürekliliği konusuna genel bakış](business-continuity-high-availability-disaster-recover-hadr-overview.md).

## <a name="programmatic-recovery-using-automated-backups"></a>Otomatik yedeklemeleri kullanarak programlı kurtarma

Kurtarma için Azure PowerShell veya REST API de kullanabilirsiniz. Aşağıdaki tablolarda kullanılabilen komut kümesi açıklanır.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager modülü SQL veritabanı ve SQL yönetilen örneği tarafından hala desteklenmektedir, ancak gelecekteki tüm geliştirmeler az. SQL modülüne yöneliktir. Bu cmdlet 'ler için bkz. [Azurerd. SQL](/powershell/module/AzureRM.Sql/). Az Module ve Azure Resource Manager modüllerinde komutların bağımsız değişkenleri harika bir ölçüde aynıdır.

#### <a name="sql-database"></a>SQL Veritabanı

Tek başına veya havuza alınmış bir veritabanını geri yüklemek için bkz. [restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase).

  | Cmdlet | Açıklama |
  | --- | --- |
  | [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) |Bir veya daha fazla veritabanını alır. |
  | [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup) | Geri yükleyebileceğiniz, silinmiş bir veritabanını alır. |
  | [Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup) |Bir veritabanının coğrafi olarak yedekli bir yedeklemesini alır. |
  | [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase) |Bir veritabanını geri yükler. |

  > [!TIP]
  > Bir veritabanının bir noktadan noktaya geri yüklemesini nasıl gerçekleştirekullanacağınızı gösteren örnek bir PowerShell betiği için bkz. [PowerShell kullanarak veritabanını geri yükleme](scripts/restore-database-powershell.md).

#### <a name="sql-managed-instance"></a>SQL Yönetilen Örnek

Yönetilen örnek veritabanını geri yüklemek için bkz. [restore-Azsqlınstancedatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase).

  | Cmdlet | Açıklama |
  | --- | --- |
  | [Get-Azsqlınstance](/powershell/module/az.sql/get-azsqlinstance) |Bir veya daha fazla yönetilen örneği alır. |
  | [Get-Azsqlınstancedatabase](/powershell/module/az.sql/get-azsqlinstancedatabase) | Bir örnek veritabanı alır. |
  | [Restore-Azsqlınstancedatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase) |Örnek veritabanını geri yükler. |

### <a name="rest-api"></a>REST API

REST API kullanarak bir veritabanını geri yüklemek için:

| API | Açıklama |
| --- | --- |
| [REST (createMode = kurtarma)](/rest/api/sql/databases) |Bir veritabanını geri yükler. |
| [Veritabanı oluşturma veya güncelleştirme durumunu al](/rest/api/sql/operations) |Geri yükleme işlemi sırasında durumu döndürür. |

### <a name="azure-cli"></a>Azure CLI

#### <a name="sql-database"></a>SQL Veritabanı

Azure CLı kullanarak bir veritabanını geri yüklemek için, bkz. [az SQL DB restore](/cli/azure/sql/db#az_sql_db_restore).

#### <a name="sql-managed-instance"></a>SQL Yönetilen Örnek

Azure CLı kullanarak yönetilen bir örnek veritabanını geri yüklemek için, bkz. [az SQL mıdb restore](/cli/azure/sql/midb#az_sql_midb_restore).

## <a name="summary"></a>Özet

Otomatik yedeklemeler, veritabanlarınızı Kullanıcı ve uygulama hatalarından, yanlışlıkla veritabanı silmeye ve uzun kesintilere karşı korur. Bu yerleşik yetenek tüm hizmet katmanları ve işlem boyutları için kullanılabilir.

## <a name="next-steps"></a>Sonraki adımlar

- [İş sürekliliğine genel bakış](business-continuity-high-availability-disaster-recover-hadr-overview.md)
- [SQL veritabanı otomatik yedeklemeleri](automated-backups-overview.md)
- [Uzun süreli saklama](long-term-retention-overview.md)
- Daha hızlı kurtarma seçenekleri hakkında daha fazla bilgi edinmek için bkz. [etkin coğrafi çoğaltma](active-geo-replication-overview.md) veya [otomatik yük devretme grupları](auto-failover-group-overview.md).
