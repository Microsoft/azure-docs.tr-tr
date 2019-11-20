---
title: En son oluşturmaya yükselt
description: Azure SQL veri ambarı 'nı en son nesil Azure donanım ve depolama mimarisine yükseltin.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 02/19/2019
ms.author: martinle
ms.reviewer: jrasnick
ms.custom: seo-lt-2019
ms.openlocfilehash: 02c426cd921f4af19f3b8c271e4b1c08eae2c3c2
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692463"
---
# <a name="optimize-performance-by-upgrading-sql-data-warehouse"></a>SQL Veri Ambarı’nı yükselterek performansı iyileştirme

Azure SQL veri ambarı 'nı en son nesil Azure donanım ve depolama mimarisine yükseltin.

## <a name="why-upgrade"></a>Neden yükseltirsiniz?

Artık [Desteklenen bölgeler](gen2-migration-schedule.md#automated-schedule-and-region-availability-table)IÇIN Azure Portal SQL veri ambarı Işlem Için iyileştirilmiş Gen2 katmanına sorunsuz bir şekilde yükseltebilirsiniz. Bölgeniz kendi kendine yükseltmeyi desteklemiyorsa, desteklenen bir bölgeye yükseltebilirsiniz veya kendi bölgenizde kendi kendine yükseltmenin kullanılabilmesini bekleyebilirsiniz. Daha hızlı performans, daha yüksek ölçeklenebilirlik ve sınırsız sütunlu depolama gibi en yeni nesil Azure donanım ve gelişmiş depolama mimarisinin avantajlarından yararlanmak için hemen yükseltin. 

> [!VIDEO https://www.youtube.com/embed/9B2F0gLoyss]

## <a name="applies-to"></a>Uygulama hedefi

Bu yükseltme, [desteklenen bölgelerde](gen2-migration-schedule.md#automated-schedule-and-region-availability-table)Işlem Için iyileştirilmiş Gen1 katmanı veri ambarları için geçerlidir.

## <a name="before-you-begin"></a>Başlamadan önce

1. GEN1 to GEN2 Migration için [bölgenizin](gen2-migration-schedule.md#automated-schedule-and-region-availability-table) desteklenip desteklenmediğini denetleyin. Otomatik geçiş tarihlerini aklınızda edin. Otomatikleştirilmiş işlemle ilgili çakışmaları önlemek için, otomatik işlem başlangıç tarihinden önce el ile geçişinizi planlayın.
2. Henüz desteklenmeyen bir bölgedeyse, desteklenen bir bölgeye [geri yükleme kullanarak](#upgrade-from-an-azure-geographical-region-using-restore-through-the-azure-portal) bölgenizin eklenmesini veya yükseltilmesi için denetlemeye devam edin.
3. Bölgeniz destekleniyorsa [Azure Portal aracılığıyla yükseltin](#upgrade-in-a-supported-region-using-the-azure-portal)
4. Aşağıdaki eşlemeyi kullanarak, Işlem için Iyileştirilmiş Gen1 katmanında geçerli performans düzeyinizi temel alarak veri ambarı için **Önerilen performans düzeyini seçin** :

   | İşlem için Iyileştirilmiş Gen1 katmanı | İşlem için Iyileştirilmiş Gen2 katmanı |
   | :-------------------------: | :-------------------------: |
   |            DW100            |           DW100c            |
   |            DW200            |           DW200c            |
   |            DW300            |           DW300c            |
   |            DW400            |           DW400c            |
   |            DW500            |           DW500c            |
   |            DW600            |           DW500c            |
   |           DW1000            |           DW1000c           |
   |           DW1200            |           DW1000c           |
   |           DW1500            |           DW1500c           |
   |           DW2000            |           DW2000c           |
   |           DW3000            |           DW3000c           |
   |           DW6000            |           DW6000c           |

> [!Note]
> Önerilen performans düzeyleri doğrudan dönüştürme değildir. Örneğin, DW600 ' den DW500c ' e gitmenizi öneririz.

## <a name="upgrade-in-a-supported-region-using-the-azure-portal"></a>Azure portal kullanarak desteklenen bir bölgede yükseltme

## <a name="before-you-begin"></a>Başlamadan önce

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!NOTE]
> Azure portal aracılığıyla Gen1 'den Gen2 'e geçiş kalıcıdır. Gen1 öğesine dönmek için bir işlem yoktur.  

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

[Azure portalında](https://portal.azure.com/) oturum açın.

1. Yükseltilecek Işlem için Iyileştirilmiş Gen1 katmanı veri ambarı duraklatılmışsa, [veri ambarını sürdürür](pause-and-resume-compute-portal.md).

   > [!NOTE]
   > Gen2 ' ye geçirmek için Azure SQL veri ambarı 'nın çalışıyor olması gerekir.

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
   > -RequestedServiceObjectiveName "DW300",-RequestedServiceObjectiveName "DW300**c**" olarak değiştirildi
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
   > SERVICE_OBJECTIVE = ' DW300 ', SERVICE_OBJECTIVE = ' DW300**c**' olarak değiştirildi

## <a name="start-the-upgrade"></a>Yükseltmeyi Başlat

1. Azure portal, Işlem için Iyileştirilmiş Gen1 katmanı veri ambarınıza gidin. Yükseltilecek Işlem için Iyileştirilmiş Gen1 katmanı veri ambarı duraklatılmışsa, [veri ambarını sürdürür](pause-and-resume-compute-portal.md). 
2. Görevler sekmesi altında **Gen2 kartına Yükselt** ' i seçin: ![Upgrade_1](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_1.png)
    
    > [!NOTE]
    > Görevler sekmesinde **Gen2 kartına yükseltme** seçeneğini görmüyorsanız, abonelik türü geçerli bölgede sınırlandırılır.
    > Aboneliğinizi beyaz listeye almak için [bir destek bileti gönderebilirsiniz](sql-data-warehouse-get-started-create-support-ticket.md) .

3. Yükseltmeden önce iş yükünüzün çalıştığından ve sessiz bir şekilde tamamlandığından emin olun. Veri ambarınız, Işlem için Iyileştirilmiş Gen2 katmanı veri ambarı olarak yeniden çevrimiçi hale gelmeden birkaç dakika boyunca kapalı kalma süresi yaşarsınız. **Yükseltme seçin**:

   ![Upgrade_2](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_2.png)

4. Azure portal durumunu denetleyerek **yükseltmeniz izleyin** :

   ![Upgrade3](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_3.png)

   Yükseltme işleminin ilk adımı, tüm oturumların sonlandırdığı ölçek işlemi ("yükseltme-çevrimdışı") ile geçer ve bağlantılar bırakılır. 

   Yükseltme işleminin ikinci adımı veri geçişidir ("yükseltiliyor-online"). Veri geçişi, çevrimiçi bir Trickle arka plan işlemidir. Bu işlem, yerel bir SSD Önbelleği kullanarak eski depolama mimarisinden yeni depolama mimarisine veri sütunlu verileri yavaş bir şekilde taşır. Bu süre boyunca, sorgulama ve yükleme için veri ambarınızın çevrimiçi olması gerekir. Verileriniz, TAŞINMADIĞINA bakılmaksızın sorgu için kullanılabilir olacaktır. Veri geçişi, veri boyutunuza, performans düzeyinize ve columnstore segmentlerinizin sayısına bağlı olarak değişen oranlarda meydana gelir. 

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

2. İçin geri yükleme noktası oluşturmak istediğiniz SQL veri ambarına gidin.

3. Genel Bakış bölümünün en üstünde **+ Yeni geri yükleme noktası**' nı seçin.

    ![Yeni geri yükleme noktası](./media/sql-data-warehouse-restore-database-portal/creating_restore_point_0.png)

4. Geri yükleme noktanız için bir ad belirtin.

    ![Geri yükleme noktasının adı](./media/sql-data-warehouse-restore-database-portal/creating_restore_point_1.png)

## <a name="restore-an-active-or-paused-database-using-the-azure-portal"></a>Azure portal kullanarak etkin veya duraklatılmış bir veritabanını geri yükleme

1. [Azure portalında](https://portal.azure.com/) oturum açın.
2. Geri yüklemek istediğiniz SQL veri ambarı 'na gidin.
3. Genel Bakış bölümünün en üstünde **geri yükle**' yi seçin.

    ![ Geri Yüklemeye Genel Bakış](./media/sql-data-warehouse-restore-database-portal/restoring_0.png)

4. **Otomatik geri yükleme noktaları** veya **Kullanıcı tanımlı geri yükleme noktaları**seçeneklerinden birini belirleyin.

    ![Otomatik geri yükleme noktaları](./media/sql-data-warehouse-restore-database-portal/restoring_1.png)

5. Kullanıcı tanımlı geri yükleme noktaları için **bir geri yükleme noktası seçin** veya **Yeni bir Kullanıcı tanımlı geri yükleme noktası oluşturun**. Gen2 tarafından desteklenen bir coğrafi bölgede bir sunucu seçin. 

    ![Kullanıcı tanımlı geri yükleme noktaları](./media/sql-data-warehouse-restore-database-portal/restoring_2_udrp.png)

## <a name="restore-from-an-azure-geographical-region-using-powershell"></a>PowerShell kullanarak bir Azure coğrafi bölgesinden geri yükleme

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Bir veritabanını kurtarmak için [restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase) cmdlet 'ini kullanın.

> [!NOTE]
> Gen2 'e coğrafi geri yükleme yapabilirsiniz! Bunu yapmak için, isteğe bağlı bir parametre olarak bir Gen2 ServiceObjectiveName (ör. DW1000**c**) belirtin.

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
> Geri yükleme tamamlandıktan sonra veritabanınızı yapılandırmak için, [kurtarma sonrasında veritabanınızı yapılandırma](../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery)konusuna bakın.

Kaynak veritabanı TDE etkinse Kurtarılan veritabanı TDE etkinleştirilir.


Veri Ambarınızla ilgili herhangi bir sorunla karşılaşırsanız, bir [destek isteği](sql-data-warehouse-get-started-create-support-ticket.md) oluşturun ve olası neden olarak "Gen2 Upgrade" başvurusu yapın.

## <a name="next-steps"></a>Sonraki adımlar

Yükseltilen veri ambarınız çevrimiçi. Gelişmiş mimariden yararlanmak için bkz. [Iş yükü yönetimi Için kaynak sınıfları](resource-classes-for-workload-management.md).
