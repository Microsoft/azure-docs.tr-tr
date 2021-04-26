---
title: Adanmış SQL havuzunun en son neslini (eski adıyla SQL DW) yükseltin
description: Azure SYNAPSE Analytics adanmış SQL havuzunu (eski adıyla SQL DW) en son nesil Azure donanım ve depolama mimarisine yükseltin.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/19/2019
ms.author: martinle
ms.reviewer: jrasnick
ms.custom: seo-lt-2019
ms.openlocfilehash: b5a9d1781bd0498ac6ad74439b1572c52e3c345a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96459264"
---
# <a name="optimize-performance-by-upgrading-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Azure SYNAPSE Analytics 'te adanmış SQL havuzunu (eski adıyla SQL DW) yükselterek performansı iyileştirin

Adanmış SQL havuzunuzu (eski adıyla SQL DW) en son nesil Azure donanım ve depolama mimarisine yükseltin.

## <a name="why-upgrade"></a>Neden yükseltirsiniz?

Artık [desteklenen bölgelerde](gen2-migration-schedule.md#automated-schedule-and-region-availability-table)Azure Portal adanmış SQL Havuzu (eskı ADıYLA SQL DW) Işlem Için iyileştirilmiş Gen2 katmanına sorunsuz bir şekilde yükseltebilirsiniz. Bölgeniz kendi kendine yükseltmeyi desteklemiyorsa, desteklenen bir bölgeye yükseltebilirsiniz veya kendi bölgenizde kendi kendine yükseltmenin kullanılabilmesini bekleyebilirsiniz. Daha hızlı performans, daha yüksek ölçeklenebilirlik ve sınırsız sütunlu depolama gibi en yeni nesil Azure donanım ve gelişmiş depolama mimarisinin avantajlarından yararlanmak için hemen yükseltin.

> [!VIDEO https://www.youtube.com/embed/9B2F0gLoyss]

> [!IMPORTANT]
> Bu yükseltme, [desteklenen bölgelerde](gen2-migration-schedule.md#automated-schedule-and-region-availability-table)Işlem Için iyileştirilmiş Gen1 TIER adanmış SQL havuzları (fornmerly SQL DW) için geçerlidir.

## <a name="before-you-begin"></a>Başlamadan önce

1. GEN1 to GEN2 Migration için [bölgenizin](gen2-migration-schedule.md#automated-schedule-and-region-availability-table) desteklenip desteklenmediğini denetleyin. Otomatik geçiş tarihlerini aklınızda edin. Otomatikleştirilmiş işlemle ilgili çakışmaları önlemek için, otomatik işlem başlangıç tarihinden önce el ile geçişinizi planlayın.
2. Henüz desteklenmeyen bir bölgedeyse, desteklenen bir bölgeye [geri yükleme kullanarak](#upgrade-from-an-azure-geographical-region-using-restore-through-the-azure-portal) bölgenizin eklenmesini veya yükseltilmesi için denetlemeye devam edin.
3. Bölgeniz destekleniyorsa [Azure Portal aracılığıyla yükseltin](#upgrade-in-a-supported-region-using-the-azure-portal)
4. Aşağıdaki eşlemeyi kullanarak, Işlem için Iyileştirilmiş Gen1 katmanında geçerli performans düzeyinize bağlı olarak adanmış SQL Havuzu (eski adıyla SQL DW) için **Önerilen performans düzeyini seçin** :

   | İşlem için Iyileştirilmiş Gen1 katmanı | İşlem için Iyileştirilmiş Gen2 katmanı |
   | :-------------------------: | :-------------------------: |
   |            DW100            |           DW100c            |
   |            DW200            |           DW200c            |
   |            DW300            |           DW300c            |
   |            DW400            |           DW400c            |
   |            DW500            |           DW500c'yi seçin            |
   |            DW600            |           DW500c'yi seçin            |
   |           DW1000            |           DW1000c           |
   |           DW1200            |           DW1000c           |
   |           DW1500            |           DW1500c           |
   |           DW2000            |           DW2000c           |
   |           DW3000            |           DW3000c           |
   |           DW6000            |           DW6000c           |

> [!NOTE]
> Önerilen performans düzeyleri doğrudan dönüştürme değildir. Örneğin, DW600 ' den DW500c ' e gitmenizi öneririz.

## <a name="upgrade-in-a-supported-region-using-the-azure-portal"></a>Azure portal kullanarak desteklenen bir bölgede yükseltme

- Azure portal aracılığıyla Gen1 'den Gen2 'e geçiş kalıcıdır. Gen1 öğesine dönmek için bir işlem yoktur.
- Gen2 'e geçiş için adanmış SQL Havuzu (eski adıyla SQL DW) çalışıyor olmalıdır

### <a name="before-you-begin"></a>Başlamadan önce

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

- [Azure portalında](https://portal.azure.com/) oturum açın.
- Adanmış SQL havuzunun (eski adıyla SQL DW) çalıştığından emin olun; Gen2 'e geçirilmesi gerekir

### <a name="powershell-upgrade-commands"></a>PowerShell yükseltme komutları

1. Işlem için Iyileştirilmiş Gen1 Tier adanmış SQL Havuzu (eski adıyla SQL DW) duraklatılmışsa, [ADANMıŞ SQL havuzunu (eski ADıYLA SQL DW) sürdürür](pause-and-resume-compute-portal.md).

2. Birkaç dakika kapalı kalma süresi için hazırlıklı olun.

3. Işlem için Iyileştirilmiş Gen1 performans düzeylerine yönelik kod başvurularını belirleyip bunları eşdeğer Işlem için Iyileştirilmiş Gen2 performans düzeyiyle değiştirin. Yükseltmeden önce kod başvurularını güncelleştirmeniz gereken iki örnek aşağıda verilmiştir:

   Özgün Gen1 PowerShell komutu:

   ```powershell
   Set-AzSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300"
   ```

   Değiştirme tarihi:

   ```powershell
   Set-AzSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300c"
   ```

   > [!NOTE]
   > -RequestedServiceObjectiveName "DW300",-RequestedServiceObjectiveName "DW300 **c**" olarak değiştirildi
   >

   Özgün Gen1 T-SQL komutu:

   ```SQL
   ALTER DATABASE mySampleDataWarehouse MODIFY (SERVICE_OBJECTIVE = 'DW300') ;
   ```

   Değiştirme tarihi:

   ```sql
   ALTER DATABASE mySampleDataWarehouse MODIFY (SERVICE_OBJECTIVE = 'DW300c') ;
   ```

   > [!NOTE]
   > SERVICE_OBJECTIVE = ' DW300 ' SERVICE_OBJECTIVE = ' DW300 **c**' olarak değiştirildi

## <a name="start-the-upgrade"></a>Yükseltmeyi Başlat

1. Azure portal, Işlem için Iyileştirilmiş Gen1 adanmış SQL havuzuna (eski adıyla SQL DW) gidin. Işlem için Iyileştirilmiş Gen1 Tier adanmış SQL Havuzu (eski adıyla SQL DW) duraklatıldığında, [ADANMıŞ SQL havuzunu sürdürür](pause-and-resume-compute-portal.md).
2. Görevler sekmesinin altında **Gen2 kartına Yükselt** ' i seçin: ![ Upgrade_1](./media/upgrade-to-latest-generation/upgrade-to-gen2-1.png)

   > [!NOTE]
   > Görevler sekmesinde **Gen2 kartına yükseltme** seçeneğini görmüyorsanız, abonelik türü geçerli bölgede sınırlandırılır.
   > Aboneliğinizi onaylanmış olarak almak için [bir destek bileti gönderebilirsiniz](sql-data-warehouse-get-started-create-support-ticket.md) .

3. Yükseltmeden önce iş yükünüzün çalıştığından ve sessiz bir şekilde tamamlandığından emin olun. Adanmış SQL havuzunuz (eski adıyla SQL DW), Işlem için Iyileştirilmiş Gen2 katmanı adanmış bir SQL Havuzu (eski adıyla SQL DW) olarak yeniden çevrimiçi olacak şekilde birkaç dakika boyunca kapalı kalma süresi ile karşılaşırsınız. **Yükseltme seçin**:

   ![Upgrade_2](./media/upgrade-to-latest-generation/upgrade-to-gen2-2.png)

4. Azure portal durumunu denetleyerek **yükseltmeniz izleyin** :

   ![Upgrade3](./media/upgrade-to-latest-generation/upgrade-to-gen2-3.png)

   Yükseltme işleminin ilk adımı, tüm oturumların sonlandırdığı ölçek işlemi ("yükseltme-çevrimdışı") ile geçer ve bağlantılar bırakılır.

   Yükseltme işleminin ikinci adımı veri geçişidir ("yükseltiliyor-online"). Veri geçişi, çevrimiçi bir Trickle arka plan işlemidir. Bu işlem, yerel bir SSD Önbelleği kullanarak eski depolama mimarisinden yeni depolama mimarisine veri sütunlu verileri yavaş bir şekilde taşır. Bu süre boyunca adanmış SQL havuzunuz (eski adıyla SQL DW) sorgulama ve yükleme için çevrimiçi olacak. Verileriniz, TAŞINMADIĞINA bakılmaksızın sorgu için kullanılabilir olacaktır. Veri geçişi, veri boyutunuza, performans düzeyinize ve columnstore segmentlerinizin sayısına bağlı olarak değişen oranlarda meydana gelir.

5. **Isteğe bağlı öneri:** Ölçeklendirme işlemi tamamlandıktan sonra, veri geçişi arka plan sürecini hızlandırabilirsiniz. Daha büyük bir SLO ve kaynak sınıfında sorguladığınız tüm birincil columnstore tablolarında [alter INDEX REBUILD](sql-data-warehouse-tables-index.md) ' i çalıştırarak veri hareketini zorlayabilirsiniz. Bu işlem, tablolarınızın sayısına ve boyutlarına göre tamamlanması saat sürebilen, Trickle arka plan işlemiyle karşılaştırıldığında **çevrimdışı** . Ancak, işlem tamamlandıktan sonra, yüksek kaliteli satır grupları olan yeni Gelişmiş depolama mimarisi nedeniyle veri geçişi çok daha hızlı olur.

> [!NOTE]
> Alter INDEX REBUILD çevrimdışı bir işlemdir ve tablolar yeniden oluşturma tamamlanana kadar kullanılamaz.

Aşağıdaki sorgu, veri geçişini hızlandırmak için gereken alter INDEX yeniden oluşturma komutlarını üretir:

```sql
SELECT 'ALTER INDEX [' + idx.NAME + '] ON ['
       + Schema_name(tbl.schema_id) + '].['
       + Object_name(idx.object_id) + '] REBUILD ' + ( CASE
                                                         WHEN (
                                                     (SELECT Count(*)
                                                      FROM   sys.partitions
                                                             part2
                                                      WHERE  part2.index_id
                                                             = idx.index_id
                                                             AND
                                                     idx.object_id =
                                                     part2.object_id)
                                                     > 1 ) THEN
              ' PARTITION = '
              + Cast(part.partition_number AS NVARCHAR(256))
              ELSE ''
                                                       END ) + '; SELECT ''[' +
              idx.NAME + '] ON [' + Schema_name(tbl.schema_id) + '].[' +
              Object_name(idx.object_id) + '] ' + (
              CASE
                WHEN ( (SELECT Count(*)
                        FROM   sys.partitions
                               part2
                        WHERE
                     part2.index_id =
                     idx.index_id
                     AND idx.object_id
                         = part2.object_id) > 1 ) THEN
              ' PARTITION = '
              + Cast(part.partition_number AS NVARCHAR(256))
              + ' completed'';'
              ELSE ' completed'';'
                                                    END )
FROM   sys.indexes idx
       INNER JOIN sys.tables tbl
               ON idx.object_id = tbl.object_id
       LEFT OUTER JOIN sys.partitions part
                    ON idx.index_id = part.index_id
                       AND idx.object_id = part.object_id
WHERE  idx.type_desc = 'CLUSTERED COLUMNSTORE';
```

## <a name="upgrade-from-an-azure-geographical-region-using-restore-through-the-azure-portal"></a>Azure portal aracılığıyla geri yükleme kullanarak bir Azure coğrafi bölgesinden yükseltme

## <a name="create-a-user-defined-restore-point-using-the-azure-portal"></a>Azure portal kullanarak Kullanıcı tanımlı geri yükleme noktası oluşturma

1. [Azure portalında](https://portal.azure.com/) oturum açın.

2. İçin geri yükleme noktası oluşturmak istediğiniz adanmış SQL havuzuna (eski adıyla SQL DW) gidin.

3. Genel Bakış bölümünün en üstünde **+ Yeni geri yükleme noktası**' nı seçin.

    ![Yeni geri yükleme noktası](./media/upgrade-to-latest-generation/creating_restore_point_0.png)

4. Geri yükleme noktanız için bir ad belirtin.

    ![Geri yükleme noktasının adı](./media/upgrade-to-latest-generation/creating_restore_point_1.png)

## <a name="restore-an-active-or-paused-database-using-the-azure-portal"></a>Azure portal kullanarak etkin veya duraklatılmış bir veritabanını geri yükleme

1. [Azure portalında](https://portal.azure.com/) oturum açın.
2. Geri yüklemek istediğiniz adanmış SQL havuzuna (eski adıyla SQL DW) gidin.
3. Genel Bakış bölümünün en üstünde **geri yükle**' yi seçin.

    ![ Geri Yüklemeye Genel Bakış](./media/upgrade-to-latest-generation/restoring_0.png)

4. **Otomatik geri yükleme noktaları** veya **Kullanıcı tanımlı geri yükleme noktaları** seçeneklerinden birini belirleyin. Kullanıcı tanımlı geri yükleme noktaları için, **Kullanıcı tanımlı bir geri yükleme noktası seçin** veya **Yeni bir Kullanıcı tanımlı geri yükleme noktası oluşturun**. Sunucu için **Yeni oluştur** ' u seçin ve Gen2 tarafından desteklenen bir coğrafi bölgede sunucu seçin.

    ![Otomatik Geri Yükleme Noktaları](./media/upgrade-to-latest-generation/restoring_1.png)

## <a name="restore-from-an-azure-geographical-region-using-powershell"></a>PowerShell kullanarak bir Azure coğrafi bölgesinden geri yükleme

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Bir veritabanını kurtarmak için [restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) cmdlet 'ini kullanın.

> [!NOTE]
> Gen2 'e coğrafi geri yükleme yapabilirsiniz! Bunu yapmak için, isteğe bağlı bir parametre olarak bir Gen2 ServiceObjectiveName (ör. DW1000 **c**) belirtin.

1. Windows PowerShell'i açın.
2. Azure hesabınıza bağlanın ve hesabınızla ilişkili tüm abonelikleri listeleyin.
3. Geri yüklenecek veritabanını içeren aboneliği seçin.
4. Kurtarmak istediğiniz veritabanını alın.
5. Bir Gen2 ServiceObjectiveName belirterek veritabanı için kurtarma isteği oluşturun.
6. Coğrafi olarak geri yüklenen veritabanının durumunu doğrulayın.

```Powershell
Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName "<Subscription_name>"

# Get the database you want to recover
$GeoBackup = Get-AzSqlDatabaseGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>"

# Recover database
$GeoRestoredDatabase = Restore-AzSqlDatabase –FromGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourTargetServer>" -TargetDatabaseName "<NewDatabaseName>" –ResourceId $GeoBackup.ResourceID -ServiceObjectiveName "<YourTargetServiceLevel>" -RequestedServiceObjectiveName "DW300c"

# Verify that the geo-restored database is online
$GeoRestoredDatabase.status
```

> [!NOTE]
> Geri yükleme tamamlandıktan sonra veritabanınızı yapılandırmak için, [kurtarma sonrasında veritabanınızı yapılandırma](../../azure-sql/database/disaster-recovery-guidance.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#configure-your-database-after-recovery)konusuna bakın.

Kaynak veritabanı TDE etkinse Kurtarılan veritabanı TDE etkinleştirilir.

Adanmış SQL havuzunuz ile ilgili herhangi bir sorunla karşılaşırsanız, bir [destek isteği](sql-data-warehouse-get-started-create-support-ticket.md) oluşturun ve olası neden olarak "Gen2 Upgrade" başvurusu yapın.

## <a name="next-steps"></a>Sonraki adımlar

Yükseltilen adanmış SQL havuzunuz (eski adıyla SQL DW) çevrimiçi. Gelişmiş mimariden yararlanmak için bkz. [Iş yükü yönetimi Için kaynak sınıfları](resource-classes-for-workload-management.md).
