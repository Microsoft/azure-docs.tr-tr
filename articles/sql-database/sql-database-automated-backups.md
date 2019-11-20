---
title: Otomatik, coğrafi olarak yedekli yedeklemeler
description: SQL veritabanı, her birkaç dakikada bir yerel veritabanı yedeklemesi oluşturur ve coğrafi yedeklilik için Azure Okuma Erişimli Coğrafi olarak yedekli depolama kullanır.
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab, danil
manager: craigg
ms.date: 09/26/2019
ms.openlocfilehash: 114a5bbfd71fc0847c2b1bc65a8ba0bfa0df1add
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73821937"
---
# <a name="automated-backups"></a>Otomatik yedeklemeler

SQL veritabanı, 7 ila 35 gün arasında tutulan veritabanı yedeklerini otomatik olarak oluşturur ve veri merkezi kullanılamaz olsa bile korundıklarından emin olmak için Azure [Okuma Erişimli Coğrafi olarak yedekli depolama (RA-GRS)](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) kullanır. Bu yedeklemeler otomatik olarak oluşturulur. Veritabanı yedeklemeleri, verilerinizin yanlışlıkla bozulma veya silme işlemlerini korumasından dolayı, herhangi bir iş sürekliliği ve olağanüstü durum kurtarma stratejisinin önemli bir parçasıdır. Güvenlik kurallarınız, yedeklemelerinizin uzun bir süre (10 yıla kadar) kullanılabilmesini gerektiriyorsa, tek bir veritabanı ve elastik havuzlarda [uzun süreli bir bekletme](sql-database-long-term-retention.md) yapılandırabilirsiniz.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-sql-database-backup"></a>SQL veritabanı yedeklemesi nedir?

SQL veritabanı her hafta [tam yedeklemeler](https://docs.microsoft.com/sql/relational-databases/backup-restore/full-database-backups-sql-server) oluşturmak için SQL Server teknolojisini kullanır, her 12 saatte bir [fark yedeklemeleri](https://docs.microsoft.com/sql/relational-databases/backup-restore/differential-backups-sql-server) ve 5-10 dakikada bir [işlem günlüğü yedeklemesi](https://docs.microsoft.com/sql/relational-databases/backup-restore/transaction-log-backups-sql-server) . Yedeklemeler, veri merkezi kesintisine karşı koruma için [eşleştirilmiş bir veri merkezine](../best-practices-availability-paired-regions.md) çoğaltılan [RA-GRS depolama Blobları](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) 'nda depolanır. Bir veritabanını geri yüklerken, hizmet hangi tam, fark ve işlem günlüğü yedeklemelerinin geri yüklenmesi gerektiğini belirler.

Bu yedeklemeleri kullanarak şunları yapabilirsiniz:

- Mevcut bir veritabanını, Azure portal, Azure PowerShell, Azure CLı veya REST API kullanarak saklama süresi içinde **geçmiş bir noktaya geri yükleyin** . Tek veritabanı ve elastik havuzlarda bu işlem, özgün veritabanıyla aynı sunucuda yeni bir veritabanı oluşturur. Yönetilen örnekte bu işlem, aynı abonelik kapsamında veritabanının veya aynı ya da farklı yönetilen örneğin bir kopyasını oluşturabilir.
  - Yedekleme ilkenizi yapılandırmak için **[yedekleme saklama süresini](#how-to-change-the-pitr-backup-retention-period)** 7 ila 35 gün arasında değiştirin.
  - [Azure Portal](sql-database-long-term-backup-retention-configure.md#configure-long-term-retention-policies) veya [Azure PowerShell](sql-database-long-term-backup-retention-configure.md#use-powershell-to-manage-long-term-backups)kullanarak tek veritabanı ve elastik havuzlarda **uzun süreli saklama ilkesini 10 yıla kadar değiştirin** .
- **Silinen bir veritabanını silindiği zamana** veya saklama dönemi içinde herhangi bir zamanda geri yükleyin. Silinen veritabanı yalnızca özgün veritabanının oluşturulduğu mantıksal sunucuya veya yönetilen örneğe geri yüklenebilir.
- **Veritabanını başka bir coğrafi bölgeye geri yükleyin**. Coğrafi geri yükleme, sunucunuza ve veritabanınıza erişene zaman coğrafi bir olağanüstü durumdan kurtulmanızı sağlar. Dünyanın her yerindeki var olan herhangi bir sunucuda yeni bir veritabanı oluşturur.
- Veritabanı uzun süreli bir bekletme ilkesiyle (LTR) yapılandırılmışsa, Tek Veritabanı veya Elastik Havuz **belirli bir uzun süreli yedekten bir veritabanını geri yükleyin** . LTR, bir uyumluluk isteğini karşılamak veya uygulamanın eski bir sürümünü çalıştırmak için [Azure Portal](sql-database-long-term-backup-retention-configure.md#view-backups-and-restore-from-a-backup-using-azure-portal) veya [Azure PowerShell](sql-database-long-term-backup-retention-configure.md#use-powershell-to-manage-long-term-backups) kullanarak veritabanının eski bir sürümünü geri yüklemenize olanak tanır. Daha fazla bilgi için bkz. [Uzun süreli saklama](sql-database-long-term-retention.md).
- Geri yükleme gerçekleştirmek için bkz. [veritabanlarını yedeklerden geri yükleme](sql-database-recovery-using-backups.md).

> [!NOTE]
> Azure depolama 'da, *çoğaltma* terimi dosyaları bir konumdan diğerine kopyalamayı belirtir. SQL *veritabanının veritabanı çoğaltması* , birden çok ikincil veritabanının birincil veritabanıyla eşitlenmiş kalmasını sağlar.

Aşağıdaki örnekleri kullanarak bu işlemlerden bazılarını deneyebilirsiniz:

| | Azure portal | Azure PowerShell |
|---|---|---|
| Yedekleme bekletmesini değiştirme | [Tek Veritabanı](sql-database-automated-backups.md#change-pitr-backup-retention-period-using-azure-portal) <br/> [Yönetilen örnek](sql-database-automated-backups.md#managed-instance-database) | [Tek Veritabanı](sql-database-automated-backups.md#change-pitr-backup-retention-period-using-powershell) <br/>[Yönetilen örnek](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasebackupshorttermretentionpolicy) |
| Uzun süreli yedekleme bekletmesini değiştirme | [Tek veritabanı](sql-database-long-term-backup-retention-configure.md#configure-long-term-retention-policies)<br/>Yönetilen örnek-yok  | [Tek Veritabanı](sql-database-long-term-backup-retention-configure.md#use-powershell-to-manage-long-term-backups)<br/>Yönetilen örnek-yok  |
| Veritabanını zaman noktasından geri yükle | [Tek veritabanı](sql-database-recovery-using-backups.md#point-in-time-restore) | [Tek veritabanı](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) <br/> [Yönetilen örnek](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase) |
| Silinen veritabanını geri yükleme | [Tek veritabanı](sql-database-recovery-using-backups.md) | [Tek veritabanı](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup) <br/> [Yönetilen örnek](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeletedinstancedatabasebackup)|
| Veritabanını Azure Blob depolamadan geri yükleme | Tek veritabanı-yok <br/>Yönetilen örnek-yok  | Tek veritabanı-yok <br/>[Yönetilen örnek](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore) |

## <a name="how-long-are-backups-kept"></a>Yedeklemeler ne kadar tutuldu

Tüm Azure SQL veritabanlarının (tek, havuza alınmış ve yönetilen örnek veritabanlarının) **yedi** günlük bir varsayılan yedekleme saklama süresi vardır. [Yedekleme saklama süresini 35 güne kadar değiştirebilirsiniz](#how-to-change-the-pitr-backup-retention-period).

Bir veritabanını silerseniz, SQL veritabanı yedeklemeleri çevrimiçi bir veritabanı ile aynı şekilde tutacaktır. Örneğin, bir saklama süresi yedi güne sahip olan temel bir veritabanını silerseniz, dört günden eski bir yedekleme üç gün boyunca kaydedilir.

Yedeklemeleri maksimum saklama süresinden daha uzun süre tutmanız gerekiyorsa, veritabanınıza bir veya daha fazla uzun süreli bekletme dönemi eklemek için yedekleme özelliklerini değiştirebilirsiniz. Daha fazla bilgi için bkz. [Uzun süreli saklama](sql-database-long-term-retention.md).

> [!IMPORTANT]
> SQL veritabanlarını barındıran Azure SQL Server 'ı silerseniz, sunucuya ait olan tüm elastik havuzlar ve veritabanları da silinir ve kurtarılamaz. Silinen bir sunucuyu geri yükleyemezsiniz. Ancak uzun süreli saklama yapılandırdıysanız, LTR içeren veritabanlarının yedeklemeleri silinmez ve bu veritabanları geri yüklenebilir.

## <a name="how-often-do-backups-happen"></a>Yedeklemeler ne sıklıkla gerçekleşir?

### <a name="backups-for-point-in-time-restore"></a>Zaman içinde bir noktaya geri yükleme için yedeklemeler

SQL veritabanı, tam yedekleme, değişiklik yedeklemeleri ve işlem günlüğü yedeklemeleri otomatik olarak oluşturarak, zaman içinde nokta geri yükleme (ıNR) için self servis hizmetini destekler. Tam veritabanı yedeklemeleri haftalık olarak oluşturulur, fark veritabanı yedeklemeleri genellikle 12 saatte bir oluşturulur ve işlem günlüğü yedeklemeleri genellikle işlem boyutu ve veritabanı etkinliğinin miktarına göre sıklık ile her 5-10 dakikada bir oluşturulur. İlk tam yedekleme, bir veritabanı oluşturulduktan hemen sonra zamanlanır. Genellikle 30 dakika içinde tamamlanır, ancak veritabanı önemli boyutta olduğunda daha uzun sürebilir. Örneğin, ilk yedekleme geri yüklenen bir veritabanında veya bir veritabanı kopyasında daha uzun sürebilir. İlk tam yedeklemeden sonra, diğer tüm yedeklemeler otomatik olarak zamanlanır ve arka planda sessizce yönetilir. Tüm veritabanı yedeklerinin tam zamanlaması, genel sistem iş yükünü dengeleyerek SQL veritabanı hizmeti tarafından belirlenir. Yedekleme işlerini değiştiremez veya devre dışı bırakamezsiniz. 

INR yedeklemeleri coğrafi olarak yedekli ve [Azure Storage çapraz bölgesel çoğaltma](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) tarafından korunuyor

Daha fazla bilgi için bkz. [Noktadan noktaya geri yükleme](sql-database-recovery-using-backups.md#point-in-time-restore)

### <a name="backups-for-long-term-retention"></a>Uzun süreli saklama için yedeklemeler

Tek ve havuza alınmış veritabanları, Azure Blob depolamada en fazla 10 yıla kadar uzun süreli saklama (LTR) tam yedeklemeler yapılandırma seçeneği sunar. LTR ilkesi etkinse, haftalık tam yedeklemeler otomatik olarak farklı bir RA-GRS depolama kapsayıcısına kopyalanır. Farklı uyumluluk gereksinimini karşılamak için haftalık, aylık ve/veya yıllık yedeklemeler için farklı saklama süreleri seçebilirsiniz. Depolama alanı tüketimi, seçilen yedekleme sıklığına ve bekletme dönemine bağlıdır. LTR depolama maliyetini tahmin etmek için [LTR Fiyatlandırma Hesaplayıcı](https://azure.microsoft.com/pricing/calculator/?service=sql-database) ' yı kullanabilirsiniz.

Invr gibi, LTR yedeklemeler coğrafi olarak yedekli ve [Azure Storage çapraz bölgesel çoğaltma](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage)tarafından korunur.

Daha fazla bilgi için bkz. [uzun süreli yedek saklama](sql-database-long-term-retention.md).

## <a name="storage-costs"></a>Depolama maliyetleri
Tek veritabanları ve yönetilen örnekler için, veritabanı boyutunun %100 ' ına eşit olan en düşük yedekleme depolama miktarı ek bir ücret ödemeden sunulmaktadır. Elastik havuzlar için, havuz için ayrılan veri depolamanın %100 ' ına eşit olan en düşük yedekleme depolama miktarı ek ücret ödemeden sunulmaktadır. Ek yedekleme alanı kullanımı, GB/ay üzerinden ücretlendirilir. Bu ek tüketim, bireysel veritabanlarının iş yüküne ve boyutuna bağlı olarak değişir.

Depolama fiyatları hakkında daha fazla bilgi için bkz. [fiyatlandırma](https://azure.microsoft.com/pricing/details/sql-database/single/) sayfası. 

## <a name="are-backups-encrypted"></a>Yedeklemeler şifreli

Veritabanınız TDE ile şifrelenirse, yedeklemeler, LTR yedeklemeler de dahil olmak üzere Rest 'de otomatik olarak şifrelenir. TDE bir Azure SQL veritabanı için etkinleştirildiğinde yedeklemeler de şifrelenir. Tüm yeni Azure SQL veritabanları, varsayılan olarak TDE etkin ile yapılandırılır. TDE hakkında daha fazla bilgi için bkz. [Azure SQL veritabanı ile saydam veri şifrelemesi](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

## <a name="how-does-microsoft-ensure-backup-integrity"></a>Microsoft, yedekleme bütünlüğünü nasıl güvence altına

Azure SQL veritabanı Mühendisliği ekibi, sürekli olarak mantıksal sunuculara ve elastik havuzlara yerleştirilmiş veritabanlarının otomatik veritabanı yedeklerinin geri yüklenmesini otomatik olarak sınar (yönetilen örnekte kullanılamaz). Bir noktadan sonra geri yükleme sonrasında veritabanları DBCC CHECKDB kullanarak bütünlük denetimleri de alır.

Yönetilen örnek, geçiş tamamlandıktan sonra yerel `RESTORE` komutu veya veri geçiş hizmeti kullanılarak geri yüklenen veritabanlarının `CHECKSUM` otomatik olarak ilk yedeklemesini alır.

Bütünlük denetimi sırasında bulunan tüm sorunlar, mühendislik ekibine bir uyarıya neden olur. Azure SQL veritabanı 'nda veri bütünlüğü hakkında daha fazla bilgi için bkz. [Azure SQL veritabanı 'Nda veri bütünlüğü](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/).

## <a name="how-do-automated-backups-impact-compliance"></a>Otomatik yedeklemeler uyumluluğu nasıl etkiler

Veritabanınızı bir DTU tabanlı hizmet katmanından 35 güne ait varsayılan bir vCore tabanlı hizmet katmanına geçirdiğinizde, uygulamanızın veri kurtarma ilkesinin güvenliğinin aşılmadığından emin olmak için, sür bekletme korunur. Varsayılan saklama, uyumluluk gereksinimlerinizi karşılamıyorsa, PowerShell veya REST API kullanarak elde tutma süresini değiştirebilirsiniz. Daha fazla bilgi için bkz. [yedekleme saklama süresini değiştirme](#how-to-change-the-pitr-backup-retention-period).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="how-to-change-the-pitr-backup-retention-period"></a>Yedek yedekleme saklama süresini değiştirme

Varsayılan yedek saklama süresini Azure portal, PowerShell veya REST API kullanarak değiştirebilirsiniz. Desteklenen değerler şunlardır: 7, 14, 21, 28 veya 35 gün. Aşağıdaki örneklerde, fr bekletmenin 28 güne nasıl değiştirileceği gösterilmektedir.

> [!WARNING]
> Geçerli saklama süresini azaldıysanız, yeni saklama süresinden eski olan tüm mevcut yedeklemeler artık kullanılamaz. Geçerli saklama süresini artırırsanız, daha uzun süreli saklama süresine ulaşılana kadar SQL veritabanı mevcut yedekleri tutacaktır.

> [!NOTE]
> Bu API 'Ler yalnızca sür saklama süresini etkiler. Veritabanınız için LTR yapılandırdıysanız, bu, etkilenmeyecektir. LTR bekletme süresinin nasıl değiştirileceği hakkında daha fazla bilgi için bkz. [uzun süreli saklama](sql-database-long-term-retention.md).

### <a name="change-pitr-backup-retention-period-using-azure-portal"></a>Azure portal kullanarak yedek saklama süresini değiştirme

Azure portal kullanarak yedek saklama süresini değiştirmek için, saklama süresini portalda değiştirmek istediğiniz sunucu nesnesine gidin ve ardından değiştirmekte olduğunuz sunucu nesnesine göre uygun seçeneği belirleyin.

#### <a name="single-azure-sql-database"></a>Tek Azure SQL veritabanı

Tek Azure SQL veritabanı için yedek saklama bekletme değişikliği, sunucu düzeyinde gerçekleştirilir. Sunucu düzeyinde yapılan değişiklik, bu sunucudaki veritabanları için geçerlidir. Azure SQL veritabanı sunucusu için veri Azure portal ' dan değiştirmek için, sunucuya genel bakış dikey penceresine gidin, gezinti menüsünde Yedeklemeleri Yönet ' e tıklayın ve ardından Gezinti çubuğunda bekletme yapılandırması ' na tıklayın.

![Azure portal Değiştir](./media/sql-database-automated-backup/configure-backup-retention-sqldb.png)

#### <a name="managed-instance-database"></a>Yönetilen örnek veritabanı

SQL veritabanı yönetilen örneği için ara yedek saklama 'nın değiştirilmesi, tek bir veritabanı düzeyinde gerçekleştirilir. Azure portal bir örnek veritabanının ara veritabanı yedekleme bekletmesini değiştirmek için, tek tek veritabanına genel bakış dikey penceresine gidin ve ardından Gezinti çubuğunda yedekleme bekletmesini yapılandır seçeneğine tıklayın.

![Azure portal Değiştir](./media/sql-database-automated-backup/configure-backup-retention-sqlmi.png)

### <a name="change-pitr-backup-retention-period-using-powershell"></a>PowerShell kullanarak yedek saklama süresini değiştirme

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager modülü Azure SQL veritabanı tarafından hala desteklenmektedir, ancak gelecekteki tüm geliştirmeler az. SQL modülüne yöneliktir. Bu cmdlet 'ler için bkz. [Azurerd. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Az Module ve Azurerd modüllerinde komutların bağımsız değişkenleri önemli ölçüde aynıdır.

```powershell
Set-AzSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```

### <a name="change-pitr-retention-period-using-rest-api"></a>REST API kullanarak elde tutma süresini değiştirme

#### <a name="sample-request"></a>Örnek İsteği

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/resourceGroup/providers/Microsoft.Sql/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default?api-version=2017-10-01-preview
```

#### <a name="request-body"></a>İstek Gövdesi

```json
{
  "properties":{
    "retentionDays":28
  }
}
```

#### <a name="sample-response"></a>Örnek Yanıtı

Durum kodu: 200

```json
{
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/providers/Microsoft.Sql/resourceGroups/resourceGroup/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default",
  "name": "default",
  "type": "Microsoft.Sql/resourceGroups/servers/databases/backupShortTermRetentionPolicies",
  "properties": {
    "retentionDays": 28
  }
}
```

Daha fazla bilgi için bkz. [yedekleme bekletme REST API](https://docs.microsoft.com/rest/api/sql/backupshorttermretentionpolicies).

## <a name="next-steps"></a>Sonraki adımlar

- Veritabanı yedeklemeleri, verilerinizin yanlışlıkla bozulma veya silme işlemlerini korumasından dolayı, herhangi bir iş sürekliliği ve olağanüstü durum kurtarma stratejisinin önemli bir parçasıdır. Diğer Azure SQL veritabanı iş sürekliliği çözümleri hakkında bilgi edinmek için bkz. [iş sürekliliği genel bakış](sql-database-business-continuity.md).
- Azure portal kullanarak bir noktaya geri yüklemek için, bkz. [Azure Portal kullanarak veritabanını zaman noktasına geri yükleme](sql-database-recovery-using-backups.md).
- PowerShell 'i kullanarak zaman noktasına geri yüklemek için bkz. [PowerShell kullanarak veritabanını zaman noktasına geri yükleme](scripts/sql-database-restore-database-powershell.md).
- Azure Blob depolama alanında Azure portal kullanarak otomatik yedeklemelerin uzun süreli bekletmesini yapılandırmak, yönetmek ve geri yüklemek için, bkz. [Azure Portal kullanarak uzun süreli yedek saklama 'Yı yönetme](sql-database-long-term-backup-retention-configure.md).
- PowerShell kullanarak Azure Blob Storage 'da otomatik yedeklemelerin uzun süreli bekletmesini yapılandırmak, yönetmek ve geri yüklemek için, bkz. [PowerShell kullanarak uzun süreli yedekleme bekletmeyi yönetme](sql-database-long-term-backup-retention-configure.md).
