---
title: Otomatik, coğrafi olarak yedekli yedeklemeler
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Azure SQL veritabanı ve Azure SQL yönetilen örneği, birkaç dakikada bir yerel veritabanı yedeklemesi oluşturur ve coğrafi yedeklilik için Azure Okuma Erişimli Coğrafi olarak yedekli depolamayı kullanır.
services: sql-database
ms.service: sql-db-mi
ms.subservice: backup-restore
ms.custom: references_regions
ms.topic: conceptual
author: shkale-msft
ms.author: shkale
ms.reviewer: mathoma, stevestein, danil
ms.date: 03/10/2021
ms.openlocfilehash: 5879c9107a0ab5a2ef150d119e8b5ac8e16ac01d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102609932"
---
# <a name="automated-backups---azure-sql-database--sql-managed-instance"></a>Otomatik yedeklemeler-SQL yönetilen örnek & Azure SQL veritabanı

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-database-backup"></a>Veritabanı yedeklemesi nedir?

Veritabanı yedeklemeleri, iş sürekliliği ve olağanüstü durum kurtarma stratejilerinin önemli bir parçasıdır çünkü verilerinizi bozulma veya silme işleminden korur. Bu yedeklemeler, yapılandırılan saklama süresi içinde bir zaman noktasına veritabanı geri yüklemeyi etkinleştirir. Veri koruma kurallarınız, yedeklemelerinizin uzun süre (10 yıla kadar) kullanılabilmesini gerektiriyorsa, hem tek hem de havuza alınmış veritabanları için [uzun süreli saklama](long-term-retention-overview.md) yapılandırabilirsiniz.

### <a name="backup-frequency"></a>Yedekleme sıklığı

Hem SQL veritabanı hem de SQL yönetilen örneği, her hafta [tam yedeklemeler](/sql/relational-databases/backup-restore/full-database-backups-sql-server) oluşturmak için SQL Server teknolojisini kullanır, [fark yedeklemeleri](/sql/relational-databases/backup-restore/differential-backups-sql-server) her 12-24 saatte bir ve [işlem günlüğü yedeklemeleri](/sql/relational-databases/backup-restore/transaction-log-backups-sql-server) 5 ila 10 dakika sürer. İşlem günlüğü yedeklemelerinin sıklığı, işlem boyutuna ve veritabanı etkinliğinin miktarına göre belirlenir.

Bir veritabanını geri yüklediğinizde, hizmet hangi tam, fark ve işlem günlüğü yedeklemelerinin geri yüklenmesi gerektiğini belirler.

### <a name="backup-storage-redundancy"></a>Yedekleme depolama yedekliliği

Varsayılan olarak, SQL veritabanı ve SQL yönetilen örneği, [eşleştirilmiş bir bölgeye](../../best-practices-availability-paired-regions.md)çoğaltılan coğrafi olarak yedekli [depolama Bloblarında](../../storage/common/storage-redundancy.md) verileri depolar. Bu, birincil bölgedeki yedekleme depolama alanını etkileyen kesintilere karşı korunmaya yardımcı olur ve bir olağanüstü durum durumunda sunucunuzu farklı bir bölgeye geri yüklemenize olanak tanır. 

Yedekleme depolama yedekliliği yapılandırma seçeneği, bir SQL yönetilen örneği veya bir SQL veritabanı için yerel olarak yedekli, bölgesel olarak yedekli veya coğrafi olarak yedekli depolama Blobları arasında seçim yapmak için esneklik sağlar. Verilerinizin yönetilen örneğinizin veya SQL veritabanınızın dağıtıldığı bölge içinde kalmasını sağlamak için, varsayılan coğrafi olarak yedekli yedekleme depolama yedekliliği değiştirebilir ve yedeklemeler için yerel olarak yedekli ya da bölgesel olarak yedekli depolama bloblarını yapılandırabilirsiniz. Depolama artıklığı mekanizmaları, geçici donanım arızası, ağ veya güç kesintileri ya da büyük doğal felaketler dahil, planlı ve plansız olaylardan korunmak üzere verilerinizin birden çok kopyasını depolar. Yapılandırılan yedekleme depolama yedekliliği, uzun vadeli yedeklemeler (LTR) için kullanılan zaman içinde nokta geri yükleme (ıNR) ve uzun süreli bekletme yedeklemeleri için kullanılan kısa süreli yedekleme bekletme ayarlarına uygulanır. 

SQL veritabanı için yedekleme depolama yedekliliği, veritabanı oluşturma sırasında yapılandırılabilir veya var olan bir veritabanı için güncelleştirilemeyebilir; var olan bir veritabanında yapılan değişiklikler yalnızca gelecekteki yedeklemeler için geçerlidir. Mevcut bir veritabanının yedek depolama yedekliliği güncelleştirildikten sonra, değişikliklerin uygulanması 48 saate kadar sürebilir. Coğrafi geri yükleme, bir veritabanı yerel veya bölgesel olarak yedekli depolama kullanmak üzere güncelleştirildikten hemen devre dışı bırakıldığını unutmayın. 


> [!IMPORTANT]
> Yönetilen örnek oluşturma işlemi sırasında yedekleme depolama yedekliliği yapılandırma kaynak sağlandığında, artık depolama yedekliği değiştirilemez. 

> [!IMPORTANT]
> Bölgesel olarak yedekli depolama Şu anda yalnızca [belirli bölgelerde](../../storage/common/storage-redundancy.md#zone-redundant-storage)kullanılabilir. 

> [!NOTE]
> Azure SQL veritabanı için yapılandırılabilir yedekleme depolama yedekliği Şu anda Brezilya Güney ' de genel önizlemeye sunuldu ve genel olarak Güneydoğu Asya Azure bölgesinde kullanıma sunuldu. Bu özellik henüz hiper ölçek katmanı için kullanılabilir değil. 

### <a name="backup-usage"></a>Yedekleme kullanımı

Bu yedeklemeleri kullanarak şunları yapabilirsiniz:

- **Mevcut veritabanının**  -  zaman içindeki bir noktaya geri yüklemesi Mevcut bir veritabanını, Azure portal, Azure PowerShell, Azure CLı veya REST API kullanarak saklama süresi içinde [geçmiş bir noktaya geri yükleyin](recovery-using-backups.md#point-in-time-restore) . Bu işlem, SQL veritabanı için özgün veritabanıyla aynı sunucuda yeni bir veritabanı oluşturur, ancak özgün veritabanının üzerine yazılmasını önlemek için farklı bir ad kullanır. Geri yükleme tamamlandıktan sonra özgün veritabanını silebilirsiniz. Alternatif olarak, özgün veritabanını [yeniden adlandırabilir](/sql/relational-databases/databases/rename-a-database) ve sonra geri yüklenen veritabanını özgün veritabanı adıyla yeniden adlandırabilirsiniz. Benzer şekilde, SQL yönetilen örneği için bu işlem, aynı abonelikte ve aynı bölgede bulunan aynı veya farklı yönetilen örnekteki veritabanının bir kopyasını oluşturur.
- **Silinen veritabanının**  -  zaman içindeki bir noktaya geri yüklemesi [Silinen bir veritabanını silme zamanına](recovery-using-backups.md#deleted-database-restore) veya Bekletme dönemi içinde herhangi bir zaman noktasına geri yükleyin. Silinen veritabanı yalnızca özgün veritabanının oluşturulduğu sunucuya veya yönetilen örneğe geri yüklenebilir. Bir veritabanı silinirken, veri kaybını engellemek için, silme işleminden önce hizmet son işlem günlüğü yedeklemesini alır.
- **Coğrafi geri yükleme**  -  [Veritabanını başka bir coğrafi bölgeye geri yükleyin](recovery-using-backups.md#geo-restore). Coğrafi geri yükleme, birincil bölgedeki veritabanınıza veya yedeklemelerinize erişene zaman coğrafi bir olağanüstü durumdan kurtulmanızı sağlar. Herhangi bir Azure bölgesindeki var olan herhangi bir sunucuda veya yönetilen örnekte yeni bir veritabanı oluşturur.
   > [!IMPORTANT]
   > Coğrafi geri yükleme yalnızca SQL veritabanları veya coğrafi olarak yedekli yedekleme depolama ile yapılandırılmış yönetilen örnekler için kullanılabilir.
- **Uzun vadeli yedeklemeden**  -  geri yükleme Veritabanı uzun süreli bir bekletme ilkesiyle yapılandırılmışsa (LTR), tek bir veritabanı veya havuza alınmış bir veritabanının [belirli bir uzun süreli yedeklemesinden veritabanını geri yükleyin](long-term-retention-overview.md) . LTR, bir uyumluluk isteğini karşılamak veya uygulamanın eski bir sürümünü çalıştırmak için [Azure Portal](long-term-backup-retention-configure.md#using-the-azure-portal) veya [Azure PowerShell](long-term-backup-retention-configure.md#using-powershell) kullanarak veritabanının eski bir sürümünü geri yüklemenize olanak tanır. Daha fazla bilgi için bkz. [Uzun süreli saklama](long-term-retention-overview.md).

Geri yükleme gerçekleştirmek için bkz. [veritabanlarını yedeklerden geri yükleme](recovery-using-backups.md).

> [!NOTE]
> Azure depolama 'da, *çoğaltma* terimi bir konumdan diğerine blob kopyalamak anlamına gelir. *Veritabanı çoğaltması* , SQL 'de birden çok ikincil veritabanını birincil veritabanıyla eşitlenmiş halde tutmak için kullanılan çeşitli teknolojiler anlamına gelir.

Aşağıdaki örnekleri kullanarak yedekleme yapılandırma ve geri yükleme işlemlerini deneyebilirsiniz:

| İşlem | Azure portalı | Azure PowerShell |
|---|---|---|
| **Yedekleme bekletmesini değiştirme** | [SQL Veritabanı](automated-backups-overview.md?tabs=single-database#change-the-pitr-backup-retention-period-by-using-the-azure-portal) <br/> [SQL Yönetilen Örnek](automated-backups-overview.md?tabs=managed-instance#change-the-pitr-backup-retention-period-by-using-the-azure-portal) | [SQL Veritabanı](automated-backups-overview.md#change-the-pitr-backup-retention-period-by-using-powershell) <br/>[SQL Yönetilen Örnek](/powershell/module/az.sql/set-azsqlinstancedatabasebackupshorttermretentionpolicy) |
| **Uzun süreli yedekleme bekletmesini değiştirme** | [SQL Veritabanı](long-term-backup-retention-configure.md#configure-long-term-retention-policies)<br/>SQL yönetilen örneği-yok  | [SQL Veritabanı](long-term-backup-retention-configure.md)<br/>[SQL Yönetilen Örnek](../managed-instance/long-term-backup-retention-configure.md)  |
| **Bir veritabanından bir zaman noktasından geri yükleme** | [SQL Veritabanı](recovery-using-backups.md#point-in-time-restore)<br>[SQL Yönetilen Örnek](../managed-instance/point-in-time-restore.md) | [SQL Veritabanı](/powershell/module/az.sql/restore-azsqldatabase) <br/> [SQL Yönetilen Örnek](/powershell/module/az.sql/restore-azsqlinstancedatabase) |
| **Silinen veritabanını geri yükleme** | [SQL Veritabanı](recovery-using-backups.md)<br>[SQL Yönetilen Örnek](../managed-instance/point-in-time-restore.md#restore-a-deleted-database) | [SQL Veritabanı](/powershell/module/az.sql/get-azsqldeleteddatabasebackup) <br/> [SQL Yönetilen Örnek](/powershell/module/az.sql/get-azsqldeletedinstancedatabasebackup)|
| **Azure Blob depolamadan bir veritabanını geri yükleme** | SQL veritabanı-yok <br/>SQL yönetilen örneği-yok  | SQL veritabanı-yok <br/>[SQL Yönetilen Örnek](../managed-instance/restore-sample-database-quickstart.md) |

## <a name="backup-scheduling"></a>Yedekleme zamanlaması

İlk tam yedekleme yeni bir veritabanı oluşturulduktan veya geri yüklendikten hemen sonra zamanlanır. Bu yedekleme genellikle 30 dakika içinde tamamlanır, ancak veritabanı büyükse daha uzun sürebilir. Örneğin, ilk yedekleme geri yüklenen bir veritabanında veya bir veritabanı kopyasında daha uzun sürebilir, bu da genellikle yeni bir veritabanından daha büyük olur. İlk tam yedeklemeden sonra, diğer tüm yedeklemeler otomatik olarak zamanlanır ve yönetilir. Tüm veritabanı yedeklerinin tam zamanlaması, genel sistem iş yükünü dengeleyerek SQL veritabanı veya SQL yönetilen örnek hizmeti tarafından belirlenir. Yedekleme işlerinin zamanlamasını değiştiremez veya devre dışı bırakabilirsiniz.

> [!IMPORTANT]
> Yeni, geri yüklenen veya kopyalanmış bir veritabanı için, ilk tam yedeklemeyi izleyen ilk işlem günlüğü yedeklemesi oluşturulduğu zamandan itibaren bir noktadan sonraki geri yükleme özelliği kullanılabilir hale gelir.

## <a name="backup-storage-consumption"></a>Yedekleme depolama alanı tüketimi

SQL Server Yedekleme ve geri yükleme teknolojisine sahip bir veritabanını bir zaman noktasına geri yüklemek, bir tam yedeklemeden, isteğe bağlı olarak bir değişiklik yedeklemesinden ve bir veya daha fazla işlem günlüğü yedeğinden oluşan kesintisiz bir yedekleme zinciri gerektirir. SQL veritabanı ve SQL yönetilen örnek yedekleme zamanlaması, her hafta bir tam yedekleme içerir. Bu nedenle, tüm Bekletme dönemi içinde ara 'yı etkinleştirmek için sistem, yapılandırılan saklama süresinden daha uzun bir haftaya kadar ek tam, fark ve işlem günlüğü yedeklemeleri depolaması gerekir. 

Diğer bir deyişle, bekletme süresi boyunca herhangi bir zaman için, bekletme döneminin en eski zamanından daha eski olan bir tam yedekleme ve bir sonraki tam yedeklemeye kadar bu tam yedeklemeden kesintisiz bir fark ve işlem günlüğü yedeklemeleri zinciri olmalıdır.

> [!NOTE]
> Ara ' yı etkinleştirmek için, ek yedeklemeler yapılandırılmış saklama süresinden daha uzun bir haftaya kadar saklanır. Yedekleme depolaması tüm yedeklemeler için aynı hızda ücretlendirilir. 

Artık gerekli olmayan yedeklemeler otomatik olarak silinir. Fark yedeklemeleri ve günlük yedeklemeleri, daha önce bir tam yedeklemenin geri yüklenebilir olmasını gerektirdiğinden, üç yedekleme türünün tümü haftalık kümeler halinde temizlenir.

[Şifrelenen](transparent-data-encryption-tde-overview.md) veritabanları da dahil olmak üzere tüm veritabanları için yedeklemeler, yedekleme depolama sıkıştırması ve maliyetlerini azaltmak için sıkıştırılır. Ortalama yedekleme sıkıştırma oranı 3-4 zamandır, ancak verilerin doğasına ve veri sıkıştırmasının veritabanında kullanılıp kullanılmasından bağımsız olarak önemli ölçüde daha yüksek olabilir.

SQL veritabanı ve SQL yönetilen örneği toplam kullanılan yedekleme depolama alanınızı birikimli bir değer olarak hesaplama. Her saat, bu değer Azure Faturalandırma işlem hattında raporlanır ve bu saatlik kullanımı, her ayın sonunda tüketiminizi hesaplamak için sağlamaktan sorumludur. Veritabanı silindikten sonra, yedeklemeler yaşaşımına uğrar ve silindikçe, tüketim azalır. Tüm yedeklemeler silindikten sonra ve artık mümkün değilse faturalandırma duraklar.
   
> [!IMPORTANT]
> Veritabanı silinse bile, bir veritabanının yedeklemeleri, gızlı bir veritabanı sağlamak için tutulur. Bir veritabanını silmek ve yeniden oluşturmak, depolama ve işlem maliyetlerini kaydedebileceğinden, hizmet her silindiğinde silinen her veritabanı için yedekleri koruduğundan, yedekleme depolama maliyetlerini artırabilir. 

### <a name="monitor-consumption"></a>Tüketimi izleme

VCore veritabanları için her bir yedekleme türü (tam, değişiklik ve günlük) tarafından tüketilen depolama alanı, veritabanı izleme dikey penceresinde ayrı bir ölçüm olarak raporlanır. Aşağıdaki diyagramda tek bir veritabanı için yedekleme depolama tüketiminin nasıl izleneceği gösterilmektedir. Bu özellik şu anda yönetilen örnekler için kullanılamaz.

![Azure portal veritabanı yedeklemesi kullanımını izleme](./media/automated-backups-overview/backup-metrics.png)

### <a name="fine-tune-backup-storage-consumption"></a>Yedekleme depolama tüketimine ince ayar yapma

Bir veritabanı için maksimum veri boyutuna kadar yedekleme depolama tüketimi ücretlendirilmez. Fazla yedekleme depolama alanı tüketimi, bireysel veritabanlarının iş yüküne ve en büyük boyutuna bağlıdır. Yedekleme depolama tüketiminizi azaltmak için aşağıdaki ayarlama tekniklerinden bazılarını göz önünde bulundurun:

- [Yedekleme saklama süresini](#change-the-pitr-backup-retention-period-by-using-the-azure-portal) gereksinimleriniz için mümkün olan en düşük süreye düşürün.
- Dizin yeniden oluşturmanız gibi büyük yazma işlemlerini yapmaktan kaçının, ancak gerekenden daha sık.
- Büyük veri yükleme işlemleri için, [kümelenmiş columnstore dizinlerini](/sql/relational-databases/indexes/columnstore-indexes-overview) ve ilgili [en iyi uygulamaları](/sql/relational-databases/indexes/columnstore-indexes-data-loading-guidance)kullanmayı ve/veya kümelenmemiş dizinlerin sayısını azaltmayı düşünün.
- Genel Amaçlı hizmet katmanında, sağlanan veri depolama alanı, yedekleme depolama fiyatından daha ucuz. Sürekli yedekleme depolama maliyetleriniz varsa, yedekleme depolama alanı üzerinde kaydedilecek veri depolama alanını artırmayı düşünebilirsiniz.
- Geçici sonuçları ve/veya geçici verileri depolamak için uygulama mantığınızdaki kalıcı tablolar yerine TempDB kullanın.
- Mümkün olduğunda yerel olarak yedekli yedekleme depolaması kullanın (örneğin geliştirme ve test ortamları)

## <a name="backup-retention"></a>Yedekleme dosyası saklama

Tüm yeni, geri yüklenen ve kopyalanmış veritabanları için, Azure SQL veritabanı ve Azure SQL yönetilen örneği, en son 7 gün içinde varsayılan olarak veri için yeterli yedeklemeler sağlar. Hiper ölçek ve temel katman veritabanları hariç olmak üzere, 1-35 gün aralığında her etkin veritabanı için [yedekleme saklama süresini değiştirebilirsiniz](#change-the-pitr-backup-retention-period) . [Yedekleme depolama tüketimi](#backup-storage-consumption)bölümünde açıklandığı gibi, ınvr 'yi etkinleştirmek için depolanan yedeklemeler saklama süresinden daha eski olabilir. Yalnızca Azure SQL yönetilen örneği için, 0-35 gün aralığında bir veritabanı silindikten sonra, yedek saklama oranını ayarlamak mümkündür. 

Bir veritabanını silerseniz, sistem yedeklemeleri, belirli bir saklama süresi ile çevrimiçi bir veritabanı için olduğu gibi korur. Silinen bir veritabanı için yedekleme saklama süresini değiştiremezsiniz.

> [!IMPORTANT]
> Bir sunucuyu veya yönetilen örneği silerseniz, bu sunucu veya yönetilen örnekteki tüm veritabanları da silinir ve kurtarılamaz. Silinen bir sunucuyu veya yönetilen örneği geri yükleyemezsiniz. Ancak, bir veritabanı veya yönetilen örnek için uzun süreli saklama (LTR) yapılandırdıysanız, uzun süreli saklama yedeklemeleri silinmez ve aynı abonelikte bulunan farklı bir sunucudaki veya yönetilen örnekteki veritabanlarını, uzun süreli bir saklama yedeğinin alındığı zaman noktasına geri yüklemek için kullanılabilir.

Son 1-35 gün içinde yedek bekletme amaçları için bazen kısa süreli yedekleme saklama adı verilir. Yedeklemeleri 35 günlük maksimum kısa süreli saklama süresinden daha uzun tutmanız gerekiyorsa, [uzun süreli saklama](long-term-retention-overview.md)sağlayabilirsiniz.

### <a name="long-term-retention"></a>Uzun vadeli bekletme

Hem SQL veritabanı hem de SQL yönetilen örneği için, Azure Blob depolamada en fazla 10 yıla kadar tam yedekleme uzun süreli saklama (LTR) yapılandırabilirsiniz. LTR ilkesi yapılandırıldıktan sonra, tam yedeklemeler haftalık olarak farklı bir depolama kapsayıcısına kopyalanır. Çeşitli uyumluluk gereksinimlerini karşılamak için haftalık, aylık ve/veya yıllık tam yedeklemeler için farklı saklama süreleri seçebilirsiniz. Depolama alanı tüketimi, LTR yedeklemelerin seçili sıklık ve bekletme dönemlerine bağlıdır. LTR depolama maliyetini tahmin etmek için [LTR Fiyatlandırma Hesaplayıcı](https://azure.microsoft.com/pricing/calculator/?service=sql-database) ' yı kullanabilirsiniz.

> [!IMPORTANT]
> Mevcut bir Azure SQL veritabanı için yedek depolama yedekliliği güncelleştirme, yalnızca veritabanı için yapılan yedeklemeler için geçerlidir. Veritabanı için varolan tüm LTR yedeklemeleri, mevcut Depolama Blobu 'nda bulunmaya devam eder ve yeni yedeklemeler istenen Depolama Blobu türünde saklanacak. 

LTR hakkında daha fazla bilgi için bkz. [uzun süreli yedek saklama](long-term-retention-overview.md).

## <a name="backup-storage-costs"></a>Yedekleme depolama maliyetleri

Yedekleme depolaması için fiyat değişir ve satın alma modelinize (DTU veya vCore), seçili yedekleme depolama artıklığı seçeneğine ve ayrıca bölgenizde bağlıdır. Yedekleme depolaması tüketilen GB/ay başına ücretlendirilir, fiyatlandırma için bkz. [Azure SQL Veritabanı fiyatlandırma](https://azure.microsoft.com/pricing/details/sql-database/single/) sayfası ve [Azure SQL yönetilen örnek fiyatlandırma](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/) sayfası.

> [!NOTE]
> Azure faturasında, tüm yedekleme depolama tüketiminin değil, yalnızca tüketilen aşırı yedekleme depolaması gösterilir. Örneğin, bir kuramsal senaryoda 4TB veri depolama alanı sağladıysanız, 4TB boş yedekleme depolama alanı alacaksınız. Toplam 5.8 TB yedekleme depolama alanını kullandıysanız Azure faturasında yalnızca fazla yedekleme depolaması ücretlendirilildiği için, Azure faturasında yalnızca 1.8 TB görüntülenir.

### <a name="dtu-model"></a>DTU modeli

DTU modelinde, veritabanları ve elastik havuzlar için yedekleme depolaması için ek ücret alınmaz. Yedekleme depolama alanı fiyatı, veritabanının veya havuz fiyatının bir parçasıdır.

### <a name="vcore-model"></a>Sanal çekirdek modeli

SQL veritabanı 'ndaki tek veritabanları için, veritabanı için en fazla veri depolama boyutunun yüzde 100 ' una eşit bir yedekleme depolama miktarı, ek ücret ödemeden sağlanır. Esnek havuzlar ve yönetilen örnekler için, havuz için maksimum veri depolama alanının yüzde 100 ' una eşit bir yedekleme depolama miktarı veya sırasıyla en büyük örnek depolama boyutu, ek ücret ödemeden sunulmaktadır. 

Tek veritabanları için bu denklem, faturalandırılabilir toplam yedekleme depolama kullanımını hesaplamak için kullanılır:

`Total billable backup storage size = (size of full backups + size of differential backups + size of log backups) – maximum data storage`

Havuza alınmış veritabanları için, faturalandırılabilir toplam yedekleme depolama boyutu havuz düzeyinde toplanır ve şu şekilde hesaplanır:

`Total billable backup storage size = (total size of all full backups + total size of all differential backups + total size of all log backups) - maximum pool data storage`

Yönetilen örnekler için, faturalandırılabilir toplam yedekleme depolama boyutu örnek düzeyinde toplanır ve aşağıdaki şekilde hesaplanır:

`Total billable backup storage size = (total size of full backups + total size of differential backups + total size of log backups) – maximum instance data storage`

Toplam faturalanabilir yedekleme depolaması, varsa, kullanılan yedekleme depolama yedekliliğe göre GB/ay olarak ücretlendirilir. Bu yedekleme depolama alanı tüketimi, bireysel veritabanlarının, elastik havuzların ve yönetilen örneklerin iş yüküne ve boyutuna bağlı olarak değişir. Yoğun olarak değiştirilmiş veritabanlarının boyutu daha büyük farklar ve günlük yedeklemeleri olduğundan, bu yedeklemelerin boyutu veri değişikliği miktarıyla orantılıdır. Bu nedenle, bu tür veritabanları daha yüksek yedekleme ücretlerine sahip olur.

SQL veritabanı ve SQL yönetilen örneği toplam faturalandırılabilir yedekleme depolama alanınızı tüm yedekleme dosyalarında birikimli bir değer olarak hesaplar. Bu değer, her saat sonunda yedekleme depolama tüketiminizi almak için bu saatlik kullanımı toplayan Azure Faturalandırma işlem hattına bildirilir. Bir veritabanı silinirse, eski yedeklemeler yaşaşımına uğrar ve silindikçe yedekleme depolama alanı tüketimi yavaş yavaş azalır. Fark yedeklemeleri ve günlük yedeklemeleri, daha önce bir tam yedeklemenin geri yüklenebilir olmasını gerektirdiğinden, üç yedekleme türünün tümü haftalık kümeler halinde temizlenir. Tüm yedeklemeler silindikten sonra faturalandırma duraklar. 

Basitleştirilmiş bir örnek olarak, veritabanı tamamen boşta olduğu için bir veritabanının 744 GB 'lık yedekleme depolama alanı olduğunu ve bu miktarın tüm bir ay boyunca sabit kalacağını varsayalım. Bu toplu depolama tüketimini saatlik kullanıma dönüştürmek için, 744,0 (ayda 31 gün * günde 24 saat) ayırın. SQL veritabanı, veritabanının her saat 1 GB 'lık yedekleme ve sabit bir hızda tükettiği Azure Faturalandırma işlem hattına rapor eder. Azure Faturalandırma, bu tüketimi toplar ve tüm ay için 744 GB kullanımını gösterir. Maliyet, bölgenizdeki tutara/GB/ay oranına göre yapılır.

Şimdi daha karmaşık bir örnek. Aynı boştaki veritabanının bekletmenin, Ayın ortasında 7 günden 14 güne kadar arttığını varsayalım. Bu artış, toplam yedekleme depolama alanının 1.488 GB 'a katmasına neden olur. SQL veritabanı 1 ila 372 (ayın ilk yarısında) boyunca 1 GB kullanım rapor verebilir. Kullanım süresi 373 ile 744 arasında (ayın ikinci yarısında), kullanımı 2 GB olarak raporlayabilir. Bu kullanım, son 1.116 GB/ay faturalandırılmakta toplanacak.

Gerçek yedekleme faturalandırma senaryoları daha karmaşıktır. Veritabanındaki değişiklik hızı iş yüküne ve zaman içinde değişken olmasına bağlı olduğundan, her bir değişiklik ve günlük yedeklemesinin boyutu da farklılık gösterir ve saatlik yedekleme depolama tüketiminin buna uygun şekilde dalgalanmasına neden olur. Ayrıca, her fark yedeklemesi, son tam yedeklemeden bu yana veritabanında yapılan tüm değişiklikleri içerir, bu nedenle tüm fark yedeklemelerinin toplam boyutu bir hafta boyunca kademeli olarak artar ve daha eski bir tam, değişiklik ve günlük yedekleri kümesi bir kez daha belirginleşerek keskin hale getirilir. Örneğin, tam bir yedekleme tamamlandıktan sonra, dizin yeniden oluşturma gibi ağır bir yazma etkinliği çalıştırıldıysa, dizin yeniden oluşturma işlemi tarafından yapılan değişiklikler yeniden oluşturma süresince alınan işlem günlüğü yedeklemelerine, sonraki değişiklik yedeklemesine ve sonraki tam yedekleme gerçekleşene kadar her değişiklik yedeklemesine dahil edilir. Daha büyük veritabanlarındaki İkinci senaryo için, değişiklik yedeklemesi çok büyük değilse, hizmette en iyi duruma getirme değişiklik yedeklemesi yerine tam yedekleme oluşturur. Bu, aşağıdaki tam yedekleme yapılıncaya kadar tüm değişiklik yedeklemelerinin boyutunu azaltır.

Her yedekleme türü (tam, fark, işlem günlüğü) için toplam yedekleme depolama tüketimini, [tüketimi izleme](#monitor-consumption)bölümünde açıklandığı gibi zaman içinde izleyebilirsiniz.

### <a name="backup-storage-redundancy"></a>Yedekleme depolama yedekliliği

Yedekleme depolama artıklığı, yedekleme maliyetlerini aşağıdaki şekilde etkiler:
- Yerel olarak yedekli fiyat = x
- bölge-yedekli fiyat = 1,25 x
- coğrafi olarak yedekli fiyat = 2x

Yedekleme Depolama fiyatlandırması hakkında daha fazla bilgi için [Azure SQL Veritabanı fiyatlandırma sayfasını](https://azure.microsoft.com/pricing/details/sql-database/single/) ve [Azure SQL yönetilen örnek fiyatlandırma sayfasını](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/)ziyaret edin.

> [!IMPORTANT]
> SQL yönetilen örneği için yapılandırılabilir yedekleme depolama artıklığı tüm Azure bölgelerinde kullanılabilir ve şu anda Güneydoğu Asya Azure bölgesinde yalnızca SQL veritabanı için kullanılabilir. Yönetilen örnek için, yalnızca yönetilen örnek oluşturma işlemi sırasında belirtilebilir. Kaynak sağlandıktan sonra yedek depolama artıklığı seçeneğini değiştiremezsiniz.

### <a name="monitor-costs"></a>Maliyetleri izleme

Yedekleme depolama maliyetlerini anlamak için Azure portal **maliyet yönetimi + faturalandırma** ' e gidin, **maliyet yönetimi**' ni seçin ve ardından **Maliyet Analizi**' ni seçin. **Kapsam** olarak istenen aboneliği seçin ve ilgilendiğiniz zaman aralığı ve hizmet için filtre uygulayın.

**Hizmet adı** için bir filtre ekleyin ve ardından açılan listede **SQL veritabanı** ' nı seçin. Hizmetiniz için faturalandırma sayacını seçmek üzere **ölçüm alt kategori** filtresini kullanın. Tek bir veritabanı veya elastik veritabanı havuzu için, **tek/elastik havuz yedek depolama**' yı seçin. Yönetilen bir örnek için mı. **yedekleme depolama alanı**' nı seçin. **Depolama** ve **işlem** alt kategorileri sizi ilgilendirir, ancak yedekleme depolama maliyetleriyle ilişkili değildir.

![Yedekleme depolama maliyeti Analizi](./media/automated-backups-overview/check-backup-storage-cost-sql-mi.png)

  >[!NOTE]
  > Ölçümler yalnızca şu anda kullanımda olan sayaçlar için görülebilir. Bir sayaç yoksa, kategori Şu anda kullanılmıyor olabilir. Örneğin, yönetilen bir örnek dağıtılan müşteriler için yönetilen örnek sayaçları mevcut olmayacaktır. Benzer şekilde, depolama sayaçları, depolamayı tüketmeyen kaynaklar için görünür olmayacaktır. 

## <a name="encrypted-backups"></a>Şifrelenmiş yedeklemeler

Veritabanınız TDE ile şifrelenirse, yedeklemeler, LTR yedeklemeler de dahil olmak üzere Rest 'de otomatik olarak şifrelenir. Azure SQL 'deki tüm yeni veritabanları, TDE varsayılan olarak etkin ile yapılandırılır. TDE hakkında daha fazla bilgi için bkz. SQL  [veritabanı & SQL yönetilen örneği saydam veri şifrelemesi](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

## <a name="backup-integrity"></a>Yedekleme bütünlüğü

Azure SQL mühendislik ekibi, sürekli olarak otomatik veritabanı yedeklemelerinin geri yüklemesini otomatik olarak sınar. (Bu test Şu anda SQL yönetilen örneği 'nde kullanılamaz.) Bir noktadan sonra geri yükleme sonrasında veritabanları DBCC CHECKDB bütünlük denetimleri de alır.

Bütünlük denetimi sırasında bulunan tüm sorunlar, mühendislik ekibine bir uyarıya neden olur. Daha fazla bilgi için bkz. [SQL veritabanı 'Nda veri bütünlüğü](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/).

Tüm veritabanı yedeklemeleri, ek yedekleme bütünlüğü sağlamak için sağlama TOPLAMı seçeneğiyle alınır.

## <a name="compliance"></a>Uyumluluk

Veritabanınızı DTU tabanlı bir hizmet katmanından sanal çekirdek tabanlı bir hizmet katmanına geçirdiğinizde, uygulamanızın veri kurtarma ilkesinin tehlikeye atılmasını sağlamak için, sür saklama korunur. Varsayılan saklama, uyumluluk gereksinimlerinizi karşılamıyorsa, elde tutma süresini değiştirebilirsiniz. Daha fazla bilgi için bkz. [yedek saklama süresini değiştirme](#change-the-pitr-backup-retention-period).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="change-the-pitr-backup-retention-period"></a>Yedek yedekleme saklama süresini değiştirme

Varsayılan yedek saklama süresini Azure portal, PowerShell veya REST API kullanarak değiştirebilirsiniz. Aşağıdaki örneklerde, fr bekletmenin 28 güne nasıl değiştirileceği gösterilmektedir.

> [!WARNING]
> Geçerli saklama süresini azaldıysanız, yeni saklama süresinden daha eski olan noktalara geri yükleme imkanını kaybedersiniz. Yeni saklama dönemi içinde artık gerekli olmayan yedeklemeler silinir. Geçerli saklama süresini artırdıysanız, yeni saklama döneminde zaman içinde eski noktalara geri yükleme imkanını hemen elde edersiniz. Sistem yedeklemeleri daha uzun süre tutmaya başladığı için zaman içinde bu becerisine sahip olursunuz.

> [!NOTE]
> Bu API 'Ler yalnızca sür saklama süresini etkiler. Veritabanınız için LTR yapılandırdıysanız, bu etkilenmez. LTR bekletme dönemlerini değiştirme hakkında daha fazla bilgi için bkz. [uzun süreli saklama](long-term-retention-overview.md).

### <a name="change-the-pitr-backup-retention-period-by-using-the-azure-portal"></a>Azure portal kullanarak, yedek yedekleme saklama süresini değiştirin

Azure portal kullanarak etkin veritabanlarının ara yedek saklama süresini değiştirmek için, saklama süresini değiştirmek istediğiniz veritabanlarında sunucuya veya yönetilen örneğe gidin. Sol bölmedeki **yedeklemeler** ' i seçin, sonra **bekletme ilkeleri** sekmesini seçin. İçin yedek saklama için değişiklik yapmak istediğiniz veritabanlarını seçin. Ardından Eylem çubuğundan **bekletmeyi Yapılandır** ' ı seçin.



#### <a name="sql-database"></a>[SQL Veritabanı](#tab/single-database)

![GIZLI tutma bekletme, sunucu düzeyi](./media/automated-backups-overview/configure-backup-retention-sqldb.png)

#### <a name="sql-managed-instance"></a>[SQL Yönetilen Örnek](#tab/managed-instance)

![INR saklama, yönetilen örneği Değiştir](./media/automated-backups-overview/configure-backup-retention-sqlmi.png)

---

### <a name="change-the-pitr-backup-retention-period-by-using-powershell"></a>PowerShell kullanarak yedek saklama süresini değiştirme

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azurerd modülü, SQL veritabanı ve SQL yönetilen örneği tarafından hala desteklenmektedir, ancak gelecekteki tüm geliştirmeler az. SQL modülüne yöneliktir. Daha fazla bilgi için bkz. [Azurerd. SQL](/powershell/module/AzureRM.Sql/). Az Module içindeki komutlar için bağımsız değişkenler Azurerd modüllerindekilerle oldukça benzerdir.

#### <a name="sql-database"></a>[SQL Veritabanı](#tab/single-database)

Etkin Azure SQL veritabanları için yedek yedekleme bekletmesini değiştirmek için aşağıdaki PowerShell örneğini kullanın.

```powershell
# SET new PITR backup retention period on an active individual database
# Valid backup retention must be between 1 and 35 days
Set-AzSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```

#### <a name="sql-managed-instance"></a>[SQL Yönetilen Örnek](#tab/managed-instance)

**Tek bir etkin** SQL yönetilen örnek veritabanları için yedek yedekleme bekletmesini değiştirmek Için aşağıdaki PowerShell örneğini kullanın.

```powershell
# SET new PITR backup retention period on an active individual database
# Valid backup retention must be between 1 and 35 days
Set-AzSqlInstanceDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -InstanceName testserver -DatabaseName testDatabase -RetentionDays 1
```

**Tüm etkin** SQL yönetilen örnek veritabanlarının ara yedek saklama süresini değiştirmek Için aşağıdaki PowerShell örneğini kullanın.

```powershell
# SET new PITR backup retention period for ALL active databases
# Valid backup retention must be between 1 and 35 days
Get-AzSqlInstanceDatabase -ResourceGroupName resourceGroup -InstanceName testserver | Set-AzSqlInstanceDatabaseBackupShortTermRetentionPolicy -RetentionDays 1
```

**Ayrı olarak silinen** bir SQL yönetilen örnek veritabanı için, ara yedek saklama süresini değiştirmek Için aşağıdaki PowerShell örneğini kullanın.
 
```powershell
# SET new PITR backup retention on an individual deleted database
# Valid backup retention must be between 0 (no retention) and 35 days. Valid retention rate can only be lower than the period of the retention period when database was active, or remaining backup days of a deleted database.
Get-AzSqlDeletedInstanceDatabaseBackup -ResourceGroupName resourceGroup -InstanceName testserver -DatabaseName testDatabase | Set-AzSqlInstanceDatabaseBackupShortTermRetentionPolicy -RetentionDays 0
```

**Tüm silinen** SQL yönetilen örnek veritabanlarının ara yedek saklama süresini değiştirmek Için aşağıdaki PowerShell örneğini kullanın.

```powershell
# SET new PITR backup retention for ALL deleted databases
# Valid backup retention must be between 0 (no retention) and 35 days. Valid retention rate can only be lower than the period of the retention period when database was active, or remaining backup days of a deleted database
Get-AzSqlDeletedInstanceDatabaseBackup -ResourceGroupName resourceGroup -InstanceName testserver | Set-AzSqlInstanceDatabaseBackupShortTermRetentionPolicy -RetentionDays 0
```

Sıfır (0) gün bekletme, yedeklemenin hemen silindiğini ve silinen bir veritabanı için artık saklanmayacak olduğunu gösterir.
Yedek saklama, silinen bir veritabanı için azaltıldıktan sonra artık arttırılmayacaktır.

---

### <a name="change-the-pitr-backup-retention-period-by-using-the-rest-api"></a>REST API kullanarak, yedek yedekleme saklama süresini değiştirin

#### <a name="sample-request"></a>Örnek istek

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/resourceGroup/providers/Microsoft.Sql/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default?api-version=2017-10-01-preview
```

#### <a name="request-body"></a>İstek gövdesi

```json
{
  "properties":{
    "retentionDays":28
  }
}
```

#### <a name="sample-response"></a>Örnek yanıt

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

Daha fazla bilgi için bkz. [yedekleme bekletme REST API](/rest/api/sql/backupshorttermretentionpolicies).

#### <a name="sample-request"></a>Örnek istek

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/resourceGroup/providers/Microsoft.Sql/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default?api-version=2017-10-01-preview
```

#### <a name="request-body"></a>İstek gövdesi

```json
{
  "properties":{
    "retentionDays":28
  }
}
```

#### <a name="sample-response"></a>Örnek yanıt

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

Daha fazla bilgi için bkz. [yedekleme bekletme REST API](/rest/api/sql/backupshorttermretentionpolicies).

## <a name="configure-backup-storage-redundancy"></a>Yedek depolama yedekliliği yapılandırma

> [!NOTE]
> SQL yönetilen örneği için yapılandırılabilir depolama yedekliliği yalnızca yönetilen örnek oluşturma işlemi sırasında belirtilebilir. Kaynak sağlandıktan sonra yedek depolama artıklığı seçeneğini değiştiremezsiniz. SQL veritabanı için, bu özelliğin genel önizlemesi Şu anda Brezilya Güney sürümünde sunulmaktadır ve Güney Doğu Asya Azure bölgesinde genel kullanıma sunulmuştur. 

Yönetilen bir örnek için yedek depolama yedekliği yalnızca örnek oluşturma sırasında ayarlanabilir. Bir SQL veritabanı için veritabanı oluşturulurken ayarlanabilir veya var olan bir veritabanı için güncelleştirilemeyebilir. Varsayılan değer, coğrafi olarak yedekli depolama. Yerel olarak yedekli, bölgesel olarak yedekli ve coğrafi olarak yedekli yedekleme depolaması arasındaki fiyatlandırma farkları için [yönetilen örnek fiyatlandırma sayfasını](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/)ziyaret edin.

### <a name="configure-backup-storage-redundancy-by-using-the-azure-portal"></a>Azure portal kullanarak yedek depolama yedekliliği yapılandırma

#### <a name="sql-database"></a>[SQL Veritabanı](#tab/single-database)

Azure portal, **SQL veritabanı oluştur** dikey penceresinde yedek depolama yedekliği yapılandırabilirsiniz. Bu seçenek, yedekleme depolama artıklığı bölümünde bulunur. 
![SQL veritabanı oluştur dikey penceresini aç](./media/automated-backups-overview/sql-database-backup-storage-redundancy.png)

#### <a name="sql-managed-instance"></a>[SQL Yönetilen Örnek](#tab/managed-instance)

Azure portal, yedekleme depolama yedekliliği değiştirme seçeneği, SQL yönetilen örneğinizi oluştururken **temel bilgiler** sekmesinde **yönetilen örnekten yapılandırma** seçeneğinden erişilebilen **işlem + depolama** dikey penceresinde bulunur.
![Açık Işlem + depolama yapılandırması-dikey pencere](./media/automated-backups-overview/open-configuration-blade-managedinstance.png)

**İşlem + depolama** dikey penceresinde yedekleme depolama yedekliliği seçme seçeneğini bulun.
![Yedek depolama yedekliliği yapılandırma](./media/automated-backups-overview/select-backup-storage-redundancy-managedinstance.png)

---

### <a name="configure-backup-storage-redundancy-by-using-powershell"></a>PowerShell kullanarak yedek depolama yedekliliği yapılandırma

#### <a name="sql-database"></a>[SQL Veritabanı](#tab/single-database)

Yeni bir veritabanı oluştururken yedek depolama yedekliliği yapılandırmak için-BackupStoageRedundancy parametresini belirtebilirsiniz. Olası değerler coğrafi, bölge ve yerel ' dir. Varsayılan olarak, tüm SQL veritabanları yedeklemeler için coğrafi olarak yedekli depolama kullanır. Yerel veya bölge yedekli yedekleme depolama alanı ile bir veritabanı oluşturulduysa coğrafi geri yükleme devre dışı bırakılır. 

```powershell
# Create a new database with geo-redundant backup storage.  
New-AzSqlDatabase -ResourceGroupName "ResourceGroup01" -ServerName "Server01" -DatabaseName "Database03" -Edition "GeneralPurpose" -Vcore 2 -ComputeGeneration "Gen5" -BackupStorageRedundancy Geo
```

Ayrıntılar için [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)adresini ziyaret edin.

Var olan bir veritabanının yedek depolama yedekliliği güncelleştirmek için-BackupStorageRedundancy parametresini kullanabilirsiniz. Olası değerler coğrafi, bölge ve yerel ' dir.
Bu işlem, değişikliklerin veritabanına uygulanması 48 saat kadar sürebilir. Coğrafi olarak yedekli yedekleme depolama alanından yerel veya bölge yedekli depolama alanına geçiş, coğrafi geri yüklemeyi devre dışı bırakır. 

```powershell
# Change the backup storage redundancy for Database01 to zone-redundant. 
Set-AzSqlDatabase -ResourceGroupName "ResourceGroup01" -DatabaseName "Database01" -ServerName "Server01" -BackupStorageRedundancy Zone
```

Ayrıntılar için [set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) adresini ziyaret edin

> [!NOTE]
> Veritabanı geri yükleme ile-BackupStorageRedundancy parametresini kullanmak için veritabanı kopyalama veya ikincil işlemleri oluşturma, Azure PowerShell sürümünü az. SQL 2.11.0 kullanın. 


#### <a name="sql-managed-instance"></a>[SQL Yönetilen Örnek](#tab/managed-instance)

Yönetilen örnek oluşturma sırasında yedekleme depolama yedekliliği yapılandırmak için,-BackupStoageRedundancy parametresini belirtebilirsiniz. Olası değerler coğrafi, bölge ve yerel ' dir.

```powershell
New-AzSqlInstance -Name managedInstance2 -ResourceGroupName ResourceGroup01 -Location westcentralus -AdministratorCredential (Get-Credential) -SubnetId "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/resourcegroup01/providers/Microsoft.Network/virtualNetworks/vnet_name/subnets/subnet_name" -LicenseType LicenseIncluded -StorageSizeInGB 1024 -VCore 16 -Edition "GeneralPurpose" -ComputeGeneration Gen4 -BackupStorageRedundancy Geo
```

Daha fazla ayrıntı için [New-Azsqlınstance](/powershell/module/az.sql/new-azsqlinstance)adresini ziyaret edin.

---

## <a name="use-azure-policy-to-enforce-backup-storage-redundancy"></a>Yedekleme depolama yedekliliği zorlamak için Azure Ilkesini kullanma

Tüm verilerinizi tek bir Azure bölgesinde tutmanızı gerektiren veri yerleriniz varsa, Azure Ilkesi 'ni kullanarak SQL veritabanınız veya yönetilen örneğiniz için bölgesel olarak yedekli veya yerel olarak yedekli yedeklemeler zorlamak isteyebilirsiniz. Azure Ilkesi, Azure kaynaklarına kurallar uygulayan ilkeler oluşturmak, atamak ve yönetmek için kullanabileceğiniz bir hizmettir. Azure Ilkesi, bu kaynakları kurumsal standartlarınızla ve hizmet düzeyi Sözleşmelerinizle uyumlu tutmanıza yardımcı olur. Daha fazla bilgi için bkz. [Azure Ilkesine genel bakış](../../governance/policy/overview.md). 

### <a name="built-in-backup-storage-redundancy-policies"></a>Yerleşik yedekleme depolama artıklığı ilkeleri 

Yeni veritabanları veya örnek oluşturmayı engellemek için abonelik veya kaynak grubu düzeyinde atanabilir ve coğrafi olarak yedekli yedekleme depolama ile yeni veritabanı veya örnek oluşturulmasını engellemek için aşağıdaki yeni yerleşik ilkeler eklenmiştir. 

[SQL veritabanı GRS yedek yedekliliği kullanmaktan kaçınmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb219b9cf-f672-4f96-9ab0-f5a3ac5e1c13)

[SQL yönetilen örnekler, GRS yedek yedekliliği kullanmaktan kaçınmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa9934fd7-29f2-4e6d-ab3d-607ea38e9079)

SQL veritabanı ve yönetilen örnek için yerleşik ilke tanımlarının tam listesini [burada](./policy-reference.md)bulabilirsiniz.

Bir kuruluş düzeyinde veri yerleşimi gereksinimlerini zorlamak için bu ilkeler bir aboneliğe atanabilir. Bunlar bir abonelik düzeyinde atandıktan sonra, belirtilen abonelikteki kullanıcılar Azure portal veya Azure PowerShell aracılığıyla coğrafi olarak yedekli yedekleme depolama alanı ile bir veritabanı veya yönetilen örnek oluşturamaz. 

> [!IMPORTANT]
> T-SQL aracılığıyla bir veritabanı oluşturulurken Azure ilkeleri zorlanmaz. T-SQL kullanarak bir veritabanı oluştururken veri fazlalığını zorlamak için, [Create Database ifadesinde BACKUP_STORAGE_REDUNDANCY parater olarak ' Local ' veya ' Zone ' kullanın](/sql/t-sql/statements/create-database-transact-sql#create-database-using-zone-redundancy-for-backups).

[Azure Portal](../../governance/policy/assign-policy-portal.md) veya [Azure PowerShell](../../governance/policy/assign-policy-powershell.md) kullanarak ilke atamayı öğrenin


## <a name="next-steps"></a>Sonraki adımlar

- Veritabanı yedeklemeleri, verilerinizin yanlışlıkla bozulma veya silme işlemlerini korumasından dolayı, herhangi bir iş sürekliliği ve olağanüstü durum kurtarma stratejisinin önemli bir parçasıdır. Diğer SQL veritabanı iş sürekliliği çözümleri hakkında bilgi edinmek için bkz. [iş sürekliliği genel bakış](business-continuity-high-availability-disaster-recover-hadr-overview.md).
- [Azure Portal kullanarak bir veritabanını zaman noktasına geri yükleme](recovery-using-backups.md)hakkında daha fazla bilgi alın.
- [PowerShell 'i kullanarak bir veritabanını zaman içinde bir noktaya geri yükleme](scripts/restore-database-powershell.md)hakkında daha fazla bilgi alın.
- Azure Blob depolamada Azure portal kullanarak otomatik yedeklemelerin uzun süreli bekletmesini yapılandırma, yönetme ve geri yükleme hakkında daha fazla bilgi için, bkz. [Azure Portal kullanarak uzun süreli yedek saklama Işlemlerini yönetme](long-term-backup-retention-configure.md).
- PowerShell kullanarak Azure Blob depolamada otomatik yedeklemelerin uzun süreli bekletmesini yapılandırma, yönetme ve geri yükleme hakkında daha fazla bilgi için bkz. [PowerShell kullanarak uzun süreli yedek saklama 'Yı yönetme](long-term-backup-retention-configure.md).
- Azure SQL yönetilen örneği üzerinde yedekleme depolama tüketimi hakkında daha fazla bilgi edinmek için bkz. [yönetilen örnekteki yedekleme depolama tüketimi açıklanmıştı](https://aka.ms/mi-backup-explained).
- Azure SQL yönetilen örneği için yedekleme depolama tutma ve maliyetlerinin ince ayar yapma hakkında bilgi edinmek için bkz. [yönetilen örnekteki yedekleme depolama maliyetlerini ayarlama](https://aka.ms/mi-backup-tuning).
