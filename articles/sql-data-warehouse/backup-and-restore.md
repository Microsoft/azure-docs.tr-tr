---
title: Yedekleme ve geri yükleme-anlık görüntüler, coğrafi olarak yedekli
description: Azure SQL veri ambarı 'nda yedekleme ve geri yükleme işlemlerinin nasıl çalıştığını öğrenin. Veri Ambarınızı birincil bölgedeki bir geri yükleme noktasına geri yüklemek için veri ambarı yedeklemeleri kullanın. Coğrafi olarak yedekli yedeklemeleri, farklı bir coğrafi bölgeye geri yüklemek için kullanın.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 10/21/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019"
ms.openlocfilehash: f37ca56f7875dcb6ab254a11b859c3e85f6a1dd0
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73686157"
---
# <a name="backup-and-restore-in-azure-sql-data-warehouse"></a>Azure SQL veri ambarı 'nda yedekleme ve geri yükleme

Azure SQL veri ambarı 'nda yedekleme ve geri yüklemeyi nasıl kullanacağınızı öğrenin. Veri Ambarınızı kurtarmak veya birincil bölgedeki önceki bir duruma kopyalamak için veri ambarı geri yükleme noktalarını kullanın. Farklı bir coğrafi bölgeye geri yüklemek için veri ambarı coğrafi olarak yedekli yedeklemeleri kullanın.

## <a name="what-is-a-data-warehouse-snapshot"></a>Veri ambarı anlık görüntüsü nedir?

*Veri ambarı anlık görüntüsü* , veri Ambarınızı kurtarmak veya önceki bir duruma kopyalamak için yararlanabileceğiniz bir geri yükleme noktası oluşturur.  SQL veri ambarı dağıtılmış bir sistem olduğundan, veri ambarı anlık görüntüsü Azure Storage 'da bulunan birçok dosyadan oluşur. Anlık görüntüler, veri Ambarınızda depolanan verilerden artımlı değişiklikler yakalar.

*Veri ambarı geri yüklemesi* , var olan veya silinen bir veri ambarının geri yükleme noktasından oluşturulan yeni bir veri ambarıdır. Veri Ambarınızı geri yükleme, herhangi bir iş sürekliliği ve olağanüstü durum kurtarma stratejisinin önemli bir parçasıdır çünkü yanlışlıkla bozulma veya silme işleminden sonra verilerinizi yeniden oluşturur. Veri ambarı, test veya geliştirme amaçlarıyla veri ambarınızın kopyalarını oluşturmak için de güçlü bir mekanizmadır.  SQL veri ambarı geri yükleme ücretleri, veritabanı boyutuna ve kaynak ve hedef veri ambarına konuma göre farklılık gösterebilir. 

## <a name="automatic-restore-points"></a>Otomatik geri yükleme noktaları

Anlık görüntüler, geri yükleme noktaları oluşturan hizmetin yerleşik bir özelliğidir. Bu özelliği etkinleştirmek zorunda değilsiniz. Ancak, geri yükleme noktası oluşturmak için veri ambarının etkin durumda olması gerekir. Veri ambarı sık sık duraklatılmışsa, otomatik geri yükleme noktaları oluşturulmayabilir, bu nedenle veri ambarını duraklatmadan önce Kullanıcı tanımlı geri yükleme noktası oluşturulmasını sağlayın. Otomatik geri yükleme noktaları şu anda Kullanıcı tarafından silinemez çünkü hizmet bu geri yükleme noktalarını kurtarmak için SLA 'Ları sürdürmek üzere kullanır.

SQL veri ambarı, yedi gün için kullanılabilen geri yükleme noktaları oluşturma günü boyunca veri ambarınızın anlık görüntülerini alır. Bu saklama dönemi değiştirilemez. SQL veri ambarı, sekiz saatlik bir kurtarma noktası hedefini (RPO) destekler. Son yedi gün içinde alınan anlık görüntülerden herhangi birinden, birincil bölgedeki veri Ambarınızı geri yükleyebilirsiniz.

Son anlık görüntünün ne zaman başlatıldığını görmek için bu sorguyu çevrimiçi SQL veri Ambarınızda çalıştırın.

```sql
select   top 1 *
from     sys.pdw_loader_backup_runs
order by run_id desc
;
```

## <a name="user-defined-restore-points"></a>Kullanıcı tanımlı geri yükleme noktaları

Bu özellik, büyük değişikliklerden önce ve sonra veri ambarınızın geri yükleme noktalarını oluşturmak için anlık görüntüleri el ile tetiklemenize olanak sağlar. Bu özellik, geri yükleme noktalarının mantıksal olarak tutarlı olmasını sağlar ve bu da hızlı kurtarma süresi için herhangi bir iş yükü kesintileri veya Kullanıcı hataları durumunda ek veri koruması sağlar. Kullanıcı tanımlı geri yükleme noktaları yedi gün için kullanılabilir ve sizin adınıza otomatik olarak silinir. Kullanıcı tanımlı geri yükleme noktalarının saklama süresini değiştiremezsiniz. **42 Kullanıcı tanımlı geri yükleme noktaları** , başka bir geri yükleme noktası oluşturmadan önce [silinmesi](https://go.microsoft.com/fwlink/?linkid=875299) gereken her zaman bir noktada garanti edilir. Anlık görüntüleri, [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabaserestorepoint#examples) veya Azure Portal aracılığıyla Kullanıcı tanımlı geri yükleme noktaları oluşturacak şekilde tetikleyebilirsiniz.

> [!NOTE]
> 7 günden daha uzun geri yükleme noktalarına ihtiyacınız varsa lütfen bu özelliği [buraya](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/35114410-user-defined-retention-periods-for-restore-points)oylayın. Ayrıca, Kullanıcı tanımlı geri yükleme noktası oluşturabilir ve yeni oluşturulan geri yükleme noktasından yeni bir veri ambarına geri yükleyebilirsiniz. Geri yükledikten sonra, veri ambarını çevrimiçi hale getirebilirsiniz ve işlem maliyetlerini kaydetmek için süresiz olarak duraklatırsınız. Duraklatılmış veritabanı, Azure Premium Depolama fiyatı üzerinden depolama ücretleri doğurur. Geri yüklenen veri ambarının etkin bir kopyasına ihtiyacınız varsa, bu işlem yalnızca birkaç dakika sürer.

### <a name="restore-point-retention"></a>Geri yükleme noktası bekletme

Geri yükleme noktası bekletme dönemlerinde ayrıntıları aşağıda listelenmiştir:

1. SQL veri ambarı, 7 günlük saklama süresi geldiğinde **ve** en az 42 toplam geri yükleme noktası (Kullanıcı tanımlı ve otomatik dahil) olduğunda geri yükleme noktasını siler
2. Bir veri ambarı duraklatıldığında anlık görüntüler alınmaz
3. Geri yükleme noktasının yaşı, veri ambarının duraklatıldığı zaman dahil olmak üzere geri yükleme noktasının alındığı zamandan itibaren mutlak takvim günlerine göre ölçülür
4. Herhangi bir zamanda, bu geri yükleme noktalarında 7 günlük saklama süresine ulaşmadığında, bir veri ambarının en fazla 42 Kullanıcı tanımlı geri yükleme noktası ve 42 otomatik geri yükleme noktası depolayabilmesi garanti edilir.
5. Bir anlık görüntü alınmışsa, veri ambarı 7 günden daha uzun bir süre boyunca duraklatılır ve devam eder, toplam 42 geri yükleme noktası (Kullanıcı tanımlı ve otomatik dahil) olana kadar geri yükleme noktasının kalıcı olması mümkündür

### <a name="snapshot-retention-when-a-data-warehouse-is-dropped"></a>Veri ambarı bırakıldığında anlık görüntü bekletme

Bir veri ambarını bıraktığınızda, SQL veri ambarı son bir anlık görüntü oluşturur ve yedi gün boyunca kaydeder. Veri ambarını silme sırasında oluşturulan son geri yükleme noktasına geri yükleyebilirsiniz. Veri ambarı duraklatılmış durumda bırakılmışsa, hiçbir anlık görüntü alınmaz. Bu senaryoda, veri ambarını bırakmadan önce Kullanıcı tanımlı bir geri yükleme noktası oluşturduğunuzdan emin olun.

> [!IMPORTANT]
> Bir mantıksal SQL Server örneğini silerseniz, örneğe ait olan tüm veritabanları da silinir ve kurtarılamaz. Silinen bir sunucuyu geri yükleyemezsiniz.

## <a name="geo-backups-and-disaster-recovery"></a>Coğrafi yedeklemeler ve olağanüstü durum kurtarma

SQL veri ambarı, [eşleştirilmiş bir veri merkezine](../best-practices-availability-paired-regions.md)günde bir kez coğrafi yedekleme gerçekleştirir. Coğrafi geri yükleme için RPO 24 saattir. Coğrafi yedeklemeyi SQL veri ambarının desteklendiği herhangi bir bölgedeki bir sunucuya geri yükleyebilirsiniz. Coğrafi yedekleme, birincil bölgenizdeki geri yükleme noktalarına erişebilmek için veri ambarını geri yüklemenize da sağlar.

> [!NOTE]
> Coğrafi yedeklemeler için daha kısa bir RPO gerekliyse, bu yetenek için [burada](https://feedback.azure.com/forums/307516-sql-data-warehouse)oy verin. Ayrıca, Kullanıcı tanımlı geri yükleme noktası oluşturabilir ve yeni oluşturulan geri yükleme noktasından farklı bir bölgedeki yeni bir veri ambarına geri yükleme yapabilirsiniz. Geri yükledikten sonra, veri ambarını çevrimiçi hale getirebilirsiniz ve işlem maliyetlerini kaydetmek için süresiz olarak duraklatırsınız. Duraklatılmış veritabanı, Azure Premium Depolama fiyatı üzerinden depolama ücretleri doğurur. Veri ambarının etkin bir kopyasına ihtiyacınız olması gerekir, bu işlem yalnızca birkaç dakika sürer.

## <a name="backup-and-restore-costs"></a>Yedekleme ve geri yükleme maliyetleri

Azure faturanızda depolama için bir satır öğesi ve olağanüstü durum kurtarma depolaması için bir satır öğesi olduğunu fark edeceksiniz. Depolama ücreti, verileri birincil bölgede, anlık görüntüler tarafından yakalanan artımlı değişikliklerle birlikte depolamanın toplam maliyetidir. Anlık görüntülerin nasıl ücretlendirildiğinin daha ayrıntılı bir açıklaması için bkz. [anlık görüntülerin nasıl tahakkuk](https://docs.microsoft.com/rest/api/storageservices/Understanding-How-Snapshots-Accrue-Charges?redirectedfrom=MSDN#snapshot-billing-scenarios)ettirileceğini anlama. Coğrafi olarak yedekli ücret, coğrafi yedeklemeleri depolamanın maliyetini içerir.  

Birincil veri ambarınızın toplam maliyeti ve yedi günlük anlık görüntü değişikliği, en yakın TB 'ye yuvarlanır. Örneğin, veri ambarınız 1,5 TB ise ve anlık görüntüler 100 GB ise, Azure Premium Depolama ücretlendirirken 2 TB veri için faturalandırılırsınız.

Coğrafi olarak yedekli depolama kullanıyorsanız, ayrı bir depolama ücreti alırsınız. Coğrafi olarak yedekli depolama, standart Okuma Erişimli Coğrafi olarak yedekli depolama (RA-GRS) fiyatı üzerinden faturalandırılır.

SQL veri ambarı fiyatlandırması hakkında daha fazla bilgi için bkz. [SQL veri ambarı fiyatlandırması](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/). Bölgeler arasında geri yükleme sırasında veri çıkışı için ücretlendirilirsiniz.

## <a name="restoring-from-restore-points"></a>Geri yükleme noktalarından geri yükleme

Her anlık görüntü, anlık görüntünün başlatıldığı süreyi temsil eden bir geri yükleme noktası oluşturur. Bir veri ambarını geri yüklemek için bir geri yükleme noktası seçer ve geri yükleme komutu verirsiniz.  

Geri yüklenen veri ambarını ve geçerli olanı koruyabilir ya da bunlardan birini silebilirsiniz. Geçerli veri ambarını geri yüklenen veri ambarı ile değiştirmek istiyorsanız, [alter database (Azure SQL veri ambarı)](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse) SEÇENEĞINI kullanarak adı Değiştir seçeneğiyle yeniden adlandırabilirsiniz.

Bir veri ambarını geri yüklemek için, [Azure Portal kullanarak bir veri ambarını geri yükleme](sql-data-warehouse-restore-database-portal.md), [PowerShell kullanarak bir veri ambarını GERI yükleme](sql-data-warehouse-restore-database-powershell.md)veya [REST API 'leri kullanarak bir veri ambarını](sql-data-warehouse-restore-database-rest-api.md)geri yükleme bölümüne bakın.

Silinen veya duraklatılmış bir veri ambarını geri yüklemek için [bir destek bileti oluşturabilirsiniz](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="cross-subscription-restore"></a>Çapraz abonelik geri yükleme

Abonelik genelinde doğrudan geri yüklemeniz gerekiyorsa [Bu yetenek için](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/36256231-enable-support-for-cross-subscription-restore)bu özelliği oylayın. Farklı bir mantıksal sunucuya geri yükleme yapın ve çapraz abonelik geri yükleme işlemi gerçekleştirmek için sunucuyu abonelikler arasında [' taşıyın '](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources) . 

## <a name="geo-redundant-restore"></a>Coğrafi olarak yedekli geri yükleme

Veri Ambarınızı, seçtiğiniz performans düzeyinde SQL veri ambarı 'nı destekleyen herhangi bir bölgeye [geri yükleyebilirsiniz](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-restore-from-geo-backup#restore-from-an-azure-geographical-region-through-powershell) .

> [!NOTE]
> Coğrafi olarak yedekli geri yükleme gerçekleştirmek için bu özellikten bir daha fazla kapatma yapmanız gerekir.

## <a name="next-steps"></a>Sonraki adımlar

Olağanüstü durum planlaması hakkında daha fazla bilgi için bkz. [iş sürekliliği genel bakış](../sql-database/sql-database-business-continuity.md)
