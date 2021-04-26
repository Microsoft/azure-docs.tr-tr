---
title: Yedekleme ve geri yükleme-anlık görüntüler, coğrafi olarak yedekli
description: Azure SYNAPSE Analytics adanmış SQL havuzunda yedekleme ve geri yükleme işlemlerinin nasıl çalıştığını öğrenin. Veri Ambarınızı birincil bölgedeki bir geri yükleme noktasına geri yüklemek için yedeklemeleri kullanın. Coğrafi olarak yedekli yedeklemeleri, farklı bir coğrafi bölgeye geri yüklemek için kullanın.
services: synapse-analytics
author: joannapea
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/13/2020
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019"
ms.openlocfilehash: 0fa6777dc5b587150f630ed8ccc110d16448cc21
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104602256"
---
# <a name="backup-and-restore-in-azure-synapse-dedicated-sql-pool"></a>Azure SYNAPSE adanmış SQL havuzunda yedekleme ve geri yükleme

Azure SYNAPSE adanmış SQL havuzunda yedekleme ve geri yüklemeyi nasıl kullanacağınızı öğrenin. Veri Ambarınızı kurtarmak veya birincil bölgedeki önceki bir duruma kopyalamak için adanmış SQL havuzu geri yükleme noktalarını kullanın. Farklı bir coğrafi bölgeye geri yüklemek için veri ambarı coğrafi olarak yedekli yedeklemeleri kullanın.

## <a name="what-is-a-data-warehouse-snapshot"></a>Veri ambarı anlık görüntüsü nedir?

*Veri ambarı anlık görüntüsü* , veri Ambarınızı kurtarmak veya önceki bir duruma kopyalamak için yararlanabileceğiniz bir geri yükleme noktası oluşturur.  Adanmış SQL havuzu dağıtılmış bir sistem olduğundan, veri ambarı anlık görüntüsü Azure Storage 'da bulunan birçok dosyadan oluşur. Anlık görüntüler, veri Ambarınızda depolanan verilerden artımlı değişiklikler yakalar.

*Veri ambarı geri yüklemesi* , var olan veya silinen bir veri ambarının geri yükleme noktasından oluşturulan yeni bir veri ambarıdır. Veri Ambarınızı geri yükleme, herhangi bir iş sürekliliği ve olağanüstü durum kurtarma stratejisinin önemli bir parçasıdır çünkü yanlışlıkla bozulma veya silme işleminden sonra verilerinizi yeniden oluşturur. Veri ambarı, test veya geliştirme amaçlarıyla veri ambarınızın kopyalarını oluşturmak için de güçlü bir mekanizmadır. Adanmış SQL havuzu geri yükleme ücretleri, veritabanı boyutuna ve kaynak ve hedef veri ambarına konuma göre farklılık gösterebilir.

## <a name="automatic-restore-points"></a>Otomatik Geri Yükleme Noktaları

Anlık görüntüler, geri yükleme noktaları oluşturan yerleşik bir özelliktir. Bu özelliği etkinleştirmek zorunda değilsiniz. Ancak, adanmış SQL havuzunun geri yükleme noktası oluşturma için etkin durumda olması gerekir. Sıklıkla duraklatılmışsa, otomatik geri yükleme noktaları oluşturulmayabilir, bu nedenle adanmış SQL havuzunu duraklatmadan önce Kullanıcı tanımlı geri yükleme noktası oluşturulmasını sağlayın. Otomatik geri yükleme noktaları şu anda Kullanıcı tarafından silinemez çünkü hizmet bu geri yükleme noktalarını kurtarmak için SLA 'Ları sürdürmek üzere kullanır.

Veri ambarınızın anlık görüntüleri, yedi gün boyunca kullanılabilen geri yükleme noktaları oluşturma gününde alınır. Bu saklama dönemi değiştirilemez. Adanmış SQL havuzu sekiz saatlik bir kurtarma noktası hedefini (RPO) destekler. Son yedi gün içinde alınan anlık görüntülerden herhangi birinden, birincil bölgedeki veri Ambarınızı geri yükleyebilirsiniz.

Son anlık görüntünün ne zaman başlatıldığını görmek için, bu sorguyu çevrimiçi adanmış SQL havuzunda çalıştırın.

```sql
select   top 1 *
from     sys.pdw_loader_backup_runs
order by run_id desc
;
```

## <a name="user-defined-restore-points"></a>Kullanıcı Tanımlı Geri Yükleme Noktaları

Bu özellik, büyük değişikliklerden önce ve sonra veri ambarınızın geri yükleme noktalarını oluşturmak için anlık görüntüleri el ile tetiklemenize olanak sağlar. Bu özellik, geri yükleme noktalarının mantıksal olarak tutarlı olmasını sağlar ve bu da hızlı kurtarma süresi için herhangi bir iş yükü kesintileri veya Kullanıcı hataları durumunda ek veri koruması sağlar. Kullanıcı tanımlı geri yükleme noktaları yedi gün için kullanılabilir ve sizin adınıza otomatik olarak silinir. Kullanıcı tanımlı geri yükleme noktalarının saklama süresini değiştiremezsiniz. **42 Kullanıcı tanımlı geri yükleme noktaları** , başka bir geri yükleme noktası oluşturmadan önce [silinmesi](/powershell/module/azurerm.sql/remove-azurermsqldatabaserestorepoint) gereken her zaman bir noktada garanti edilir. Anlık görüntüleri, [PowerShell](/powershell/module/az.sql/new-azsqldatabaserestorepoint?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.jsont#examples) veya Azure Portal aracılığıyla Kullanıcı tanımlı geri yükleme noktaları oluşturacak şekilde tetikleyebilirsiniz.

> [!NOTE]
> 7 günden daha uzun geri yükleme noktalarına ihtiyacınız varsa lütfen bu özelliği [buraya](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/35114410-user-defined-retention-periods-for-restore-points)oylayın. Ayrıca, Kullanıcı tanımlı geri yükleme noktası oluşturabilir ve yeni oluşturulan geri yükleme noktasından yeni bir veri ambarına geri yükleyebilirsiniz. ' Yi geri yükledikten sonra adanmış SQL havuzunu çevrimiçi olarak kullanabilir ve işlem maliyetlerini kaydetmek için süresiz olarak duraklatırsınız. Duraklatılmış veritabanı, Azure SYNAPSE Storage hızında depolama ücretleri doğurur. Geri yüklenen veri ambarının etkin bir kopyasına ihtiyacınız varsa, bu işlem yalnızca birkaç dakika sürer.

### <a name="restore-point-retention"></a>Geri yükleme noktası bekletme

Geri yükleme noktası bekletme dönemlerinde ayrıntıları aşağıda listelenmiştir:

1. Adanmış SQL havuzu, 7 günlük saklama süresi geldiğinde **ve** en az 42 toplam geri yükleme noktası olduğunda (hem Kullanıcı tanımlı hem de otomatik olarak) geri yükleme noktasını siler.
2. Adanmış bir SQL havuzu duraklatıldığında anlık görüntüler alınmaz.
3. Geri yükleme noktasının yaşı, SQL havuzunun duraklatıldığı zaman dahil olmak üzere geri yükleme noktasının alındığı zamandan itibaren mutlak takvim günlerine göre ölçülür.
4. Herhangi bir zamanda, bu geri yükleme noktalarında 7 günlük saklama süresine ulaşmadığı sürece, ayrılmış bir SQL havuzunun 42 ' e kadar Kullanıcı tanımlı geri yükleme noktası ve 42 otomatik geri yükleme noktası depolayabilmesi garanti edilir.
5. Bir anlık görüntü alınmışsa, adanmış SQL havuzu 7 günden daha uzun bir süre sonra duraklatılır ve devam ederse, geri yükleme noktası toplam 42 geri yükleme noktası (hem Kullanıcı tanımlı hem de otomatik dahil) olana kadar devam eder

### <a name="snapshot-retention-when-a-sql-pool-is-dropped"></a>SQL havuzu bırakıldığında anlık görüntü bekletme

Adanmış bir SQL havuzunu bıraktığınızda, son bir anlık görüntü oluşturulur ve yedi gün boyunca kaydedilir. Adanmış SQL havuzunu silme sırasında oluşturulan son geri yükleme noktasına geri yükleyebilirsiniz. Adanmış SQL havuzu duraklatılmış bir durumda bırakılmışsa, hiçbir anlık görüntü alınmaz. Bu senaryoda, adanmış SQL havuzunu bırakmadan önce Kullanıcı tanımlı bir geri yükleme noktası oluşturduğunuzdan emin olun.

> [!IMPORTANT]
> Adanmış bir SQL havuzunu barındıran sunucu/çalışma alanını silerseniz, sunucuya/çalışma alanına ait olan tüm veritabanları da silinir ve kurtarılamaz. Silinen bir sunucuyu geri yükleyemezsiniz.

## <a name="geo-backups-and-disaster-recovery"></a>Coğrafi yedeklemeler ve olağanüstü durum kurtarma

Bir coğrafi yedekleme, [eşleştirilmiş bir veri merkezine](../../best-practices-availability-paired-regions.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)günde bir kez oluşturulur. Coğrafi geri yükleme için RPO 24 saattir. Coğrafi yedeklemeyi, adanmış SQL havuzunun desteklendiği herhangi bir bölgedeki bir sunucuya geri yükleyebilirsiniz. Coğrafi yedekleme, birincil bölgenizdeki geri yükleme noktalarına erişebilmek için veri ambarını geri yüklemenize da sağlar.

Adanmış SQL havuzunuz için coğrafi yedeklemeler gerektirmiyorsa, bunları devre dışı bırakabilir ve olağanüstü durum kurtarma depolama maliyetlerine kaydedebilirsiniz. Bunu yapmak için [nasıl yapılır Kılavuzu: adanmış BIR SQL Havuzu (eski ADıYLA SQL DW) için coğrafi yedeklemeleri devre dışı bırakma](disable-geo-backup.md)konusuna bakın. Coğrafi yedeklemeleri devre dışı bıraktığınızda, birincil Azure veri merkezinizde kullanılamaz durumdaysa, ayrılmış SQL havuzunuzu eşleştirilmiş Azure bölgenize kurtaramayacağınızı unutmayın. 

> [!NOTE]
> Coğrafi yedeklemeler için daha kısa bir RPO gerekliyse, bu yetenek için [burada](https://feedback.azure.com/forums/307516-sql-data-warehouse)oy verin. Ayrıca, Kullanıcı tanımlı geri yükleme noktası oluşturabilir ve yeni oluşturulan geri yükleme noktasından farklı bir bölgedeki yeni bir veri ambarına geri yükleme yapabilirsiniz. Geri yükledikten sonra, veri ambarını çevrimiçi hale getirebilirsiniz ve işlem maliyetlerini kaydetmek için süresiz olarak duraklatırsınız. Duraklatılmış veritabanı, Azure Premium Depolama fiyatı üzerinden depolama ücretleri doğurur. Veri ambarının etkin bir kopyasına ihtiyacınız olması gerekir, bu işlem yalnızca birkaç dakika sürer.

## <a name="data-residency"></a>Veri yerleşimi 

Eşleştirilmiş veri merkezinizde Coğrafi sınırın dışında yer alıyorsa, coğrafi olarak yedekli depolamayı etkinleştirerek verilerinizin coğrafi sınırınızı içinde kalmasını sağlayabilirsiniz. Bu, adanmış bir SQL Havuzu (eski adıyla SQL DW) oluştururken veya geri yüklenirken coğrafi olarak yedekli depolama seçeneği aracılığıyla adanmış SQL havuzunuzu (eski adıyla SQL DW) sağlarken yapılabilir. 

Eşleştirilmiş veri merkezinizi farklı bir ülkede olduğunu doğrulamak için [Azure eşlenmiş bölgeler](../../best-practices-availability-paired-regions.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)' e bakın.

## <a name="backup-and-restore-costs"></a>Yedekleme ve geri yükleme maliyetleri

Azure faturanızda depolama için bir satır öğesi ve olağanüstü durum kurtarma depolaması için bir satır öğesi olduğunu fark edeceksiniz. Depolama ücreti, verileri birincil bölgede, anlık görüntüler tarafından yakalanan artımlı değişikliklerle birlikte depolamanın toplam maliyetidir. Anlık görüntülerin nasıl ücretlendirildiğinin daha ayrıntılı bir açıklaması için bkz.  [anlık görüntülerin nasıl tahakkuk](/rest/api/storageservices/Understanding-How-Snapshots-Accrue-Charges?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)ettirileceğini anlama. Coğrafi olarak yedekli ücret, coğrafi yedeklemeleri depolamanın maliyetini içerir.  

Birincil veri ambarınızın toplam maliyeti ve yedi günlük anlık görüntü değişikliği, en yakın TB 'ye yuvarlanır. Örneğin, veri ambarınız 1,5 TB ise ve anlık görüntüler 100 GB ise, Azure Premium Depolama ücretlendirirken 2 TB veri için faturalandırılırsınız.

Coğrafi olarak yedekli depolama kullanıyorsanız, ayrı bir depolama ücreti alırsınız. Coğrafi olarak yedekli depolama, standart Read-Access coğrafi olarak yedekli depolama (RA-GRS) fiyatı üzerinden faturalandırılır.

Azure SYNAPSE fiyatlandırması hakkında daha fazla bilgi için bkz. [Azure SYNAPSE fiyatlandırması](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/). Bölgeler arasında geri yükleme sırasında veri çıkışı için ücretlendirilirsiniz.

## <a name="restoring-from-restore-points"></a>Geri yükleme noktalarından geri yükleme

Her anlık görüntü, anlık görüntünün başlatıldığı süreyi temsil eden bir geri yükleme noktası oluşturur. Bir veri ambarını geri yüklemek için bir geri yükleme noktası seçer ve geri yükleme komutu verirsiniz.  

Geri yüklenen veri ambarını ve geçerli olanı koruyabilir ya da bunlardan birini silebilirsiniz. Geçerli veri ambarını geri yüklenen veri ambarı ile değiştirmek istiyorsanız, [alter database](/sql/t-sql/statements/alter-database-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) SEÇENEĞINI kullanarak adı Değiştir seçeneğiyle yeniden adlandırabilirsiniz.

Bir veri ambarını geri yüklemek için bkz. [ayrılmış BIR SQL havuzunu geri yükleme](sql-data-warehouse-restore-points.md#create-user-defined-restore-points-through-the-azure-portal).

Silinen veya duraklatılmış bir veri ambarını geri yüklemek için [bir destek bileti oluşturabilirsiniz](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="cross-subscription-restore"></a>Çapraz abonelik geri yükleme

Abonelik genelinde doğrudan geri yüklemeniz gerekiyorsa [Bu yetenek için](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/36256231-enable-support-for-cross-subscription-restore)bu özelliği oylayın. Farklı bir sunucuya geri yükleyin ve çapraz abonelik geri yüklemesi gerçekleştirmek için sunucuyu abonelikler arasında [' taşıyın '](../../azure-resource-manager/management/move-resource-group-and-subscription.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) .

## <a name="geo-redundant-restore"></a>Coğrafi olarak yedekli geri yükleme

[ADANMıŞ SQL havuzunuzu](sql-data-warehouse-restore-from-geo-backup.md#restore-from-an-azure-geographical-region-through-powershell) , seçtiğiniz performans DÜZEYINDE adanmış SQL havuzunu destekleyen herhangi bir bölgeye geri yükleyebilirsiniz.

> [!NOTE]
> Coğrafi olarak yedekli geri yükleme gerçekleştirmek için bu özellikten bir daha fazla kapatma yapmanız gerekir.

## <a name="next-steps"></a>Sonraki adımlar

Geri yükleme noktaları hakkında daha fazla bilgi için bkz. [Kullanıcı tanımlı geri yükleme noktaları](sql-data-warehouse-restore-points.md)