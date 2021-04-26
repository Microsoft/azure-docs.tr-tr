---
title: 'Hızlı başlangıç: adanmış SQL havuzunda (eski adıyla SQL DW) işlem ölçeklendirme-T-SQL'
description: T-SQL ve SQL Server Management Studio (SSMS) kullanarak adanmış SQL havuzunda (eski adıyla SQL DW) işlem ölçeğini ölçeklendirin. Daha iyi performans için işlemin ölçeğini genişletin veya maliyet tasarrufu sağlamak için işlemin ölçeğini geri daraltın.
services: synapse-analytics
author: Antvgski
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: anvang
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 3c105fe217834dc9e0e652a42ebf3b526972b228
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105961379"
---
# <a name="quickstart-scale-compute-for-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics-using-t-sql"></a>Hızlı başlangıç: T-SQL kullanarak Azure SYNAPSE Analytics 'te adanmış SQL Havuzu (eski adıyla SQL DW) için işlem ölçekleme

T-SQL ve SQL Server Management Studio (SSMS) kullanarak adanmış SQL havuzunda (eski adıyla SQL DW) işlem ölçeğini ölçeklendirin. Daha iyi performans için [işlemin ölçeğini genişletin](sql-data-warehouse-manage-compute-overview.md) veya maliyet tasarrufu sağlamak için işlemin ölçeğini geri daraltın.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

## <a name="before-you-begin"></a>Başlamadan önce

[SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)’nun (SSMS) en yeni sürümünü indirin ve yükleyin.

## <a name="create-a-dedicated-sql-pool-formerly-sql-dw"></a>Adanmış bir SQL havuzu oluştur (eski adıyla SQL DW)

**Mysampledatawarehouse** adlı özel bir SQL Havuzu (eskı ADıYLA SQL DW) oluşturmak Için [hızlı başlangıç: oluşturma ve bağlanma-Portal](create-data-warehouse-portal.md) 'ı kullanın. Bir güvenlik duvarı kuralınız olduğundan ve SQL Server Management Studio içinden adanmış SQL havuzunuza (eski adıyla SQL DW) bağlanabildiğinizden emin olmak için hızlı başlangıcı doldurun.

## <a name="connect-to-the-server-as-server-admin"></a>Sunucu yöneticisi olarak sunucuya bağlanma

Bu bölümde Azure SQL sunucunuzla bağlantı kurmak için [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) (SSMS) kullanılmaktadır.

1. SQL Server Management Studio'yu açın.

2. **Sunucuya Bağlan** iletişim kutusuna şu bilgileri girin:

   | Ayar       | Önerilen değer | Açıklama |
   | ------------ | ------------------ | ------------------------------------------------- |
   | Sunucu türü | Veritabanı altyapısı | Bu değer gereklidir |
   | Sunucu adı | Tam sunucu adı | Örnek: **mySampleDataWarehouseservername.Database.Windows.net**. |
   | Kimlik doğrulaması | SQL Server Kimlik Doğrulaması | Bu öğreticide yapılandırılan tek kimlik doğrulaması türü SQL Kimlik Doğrulamasıdır. |
   | Oturum aç | Sunucu yöneticisi hesabı | Sunucuyu oluştururken belirttiğiniz hesap. |
   | Parola | Sunucu yöneticisi hesabınızın parolası | Sunucuyu oluştururken belirttiğiniz parola. |

    ![Sunucuya bağlan](./media/quickstart-scale-compute-tsql/connect-to-server.png)

3. **Bağlan**'a tıklayın. SSMS’te Nesne Gezgini penceresi açılır.

4. Nesne Gezgini’nde, **Veritabanları**’nı genişletin. Ardından, yeni veritabanınızdaki nesneleri görüntülemek için **Mysampledatawarehouse** ' i genişletin.

    ![Veritabanı nesneleri](./media/quickstart-scale-compute-tsql/connected.png)

## <a name="view-service-objective"></a>Hizmet hedefini görüntüleme

Hizmet hedefi ayarı, adanmış SQL havuzunun veri ambarı birimi sayısını (eski adıyla SQL DW) içerir.

Adanmış SQL havuzunuzun (eski adıyla SQL DW) geçerli veri ambarı birimlerini görüntülemek için:

1. **MySampleDataWarehouseservername.Database.Windows.net** bağlantısı altında **sistem veritabanları**' nı genişletin.
2. **master** seçeneğine sağ tıklayıp **Yeni Sorgu**’yu seçin. Yeni bir sorgu penceresi açılır.
3. sys.database_service_objectives dinamik yönetim görünümünden seçim yapmak için aşağıdaki sorguyu çalıştırın.

    ```sql
    SELECT
        db.name [Database]
    ,    ds.edition [Edition]
    ,    ds.service_objective [Service Objective]
    FROM
         sys.database_service_objectives ds
    JOIN
        sys.databases db ON ds.database_id = db.database_id
    WHERE
        db.name = 'mySampleDataWarehouse'
    ```

4. Aşağıdaki sonuçlar, **mySampleDataWarehouse** öğesinin DW400 hizmet hedefine sahip olduğunu gösterir.

    ![IEW-geçerli-DWU](./media/quickstart-scale-compute-tsql/view-current-dwu.png)

## <a name="scale-compute"></a>Hesaplamayı ölçeklendirme

Adanmış SQL havuzunda (eski adıyla SQL DW), veri ambarı birimlerini ayarlayarak işlem kaynaklarını artırabilir veya azaltabilirsiniz. [Oluşturma ve Bağlanma - portal](create-data-warehouse-portal.md) bölümünde **mySampleDataWarehouse** oluşturuldu ve 400 DWU ile başlatıldı. Aşağıdaki adımlar, **mySampleDataWarehouse** için DWU’ları ayarlar.

Veri ambarı birimlerini değiştirmek için:

1. **master** seçeneğine sağ tıklayıp **Yeni Sorgu**’yu seçin.
2. [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-database?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) T-SQL deyimini kullanarak hizmet hedefini değiştirin. Hizmet hedefini için DW300 olarak değiştirmek için aşağıdaki sorguyu çalıştırın.

    ```Sql
    ALTER DATABASE mySampleDataWarehouse
    MODIFY (SERVICE_OBJECTIVE = 'DW300c');
    ```

## <a name="monitor-scale-change-request"></a>Ölçek değişikliği isteğini izleme

Önceki değişiklik isteğinin ilerlemesini görmek için `WAITFORDELAY` T-SQL söz dizimini kullanarak [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database?view=azure-sqldw-latest&preserve-view=true) dinamik yönetim görünümünü (DMV) yoklayın.

Hizmet nesnesi değişiklik durumunu yoklamak için:

1. **master** seçeneğine sağ tıklayıp **Yeni Sorgu**’yu seçin.
2. [Sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database?view=azure-sqldw-latest&preserve-view=true) DMV ' i yoklamak için aşağıdaki sorguyu çalıştırın.

    ```sql
    WHILE
    (
        SELECT TOP 1 state_desc
        FROM sys.dm_operation_status
        WHERE
            1=1
            AND resource_type_desc = 'Database'
            AND major_resource_id = 'mySampleDataWarehouse'
            AND operation = 'ALTER DATABASE'
        ORDER BY
            start_time DESC
    ) = 'IN_PROGRESS'
    BEGIN
        RAISERROR('Scale operation in progress',0,0) WITH NOWAIT;
        WAITFOR DELAY '00:00:05';
    END
    PRINT 'Complete';
    ```

3. Elde edilen çıkış, durumu yoklama işleminin günlük kaydını gösterir.

    ![İşlem durumu](./media/quickstart-scale-compute-tsql/polling-output.png)

## <a name="check-dedicated-sql-pool-formerly-sql-dw-state"></a>Adanmış SQL havuzunu denetle (eski adıyla SQL DW) durumu

Adanmış bir SQL Havuzu (eski adıyla SQL DW) duraklatıldığında T-SQL ile buna bağlanamazsınız. Adanmış SQL havuzunun (eski adıyla SQL DW) geçerli durumunu görmek için PowerShell cmdlet 'ini kullanabilirsiniz. Bir örnek için bkz. [ADANMıŞ SQL havuzunu denetleme (eski ADıYLA SQL DW) durumu-PowerShell](quickstart-scale-compute-powershell.md#check-data-warehouse-state).

## <a name="check-operation-status"></a>İşlem durumunu denetleme

Adanmış SQL havuzunuzdaki (eski adıyla SQL DW) çeşitli yönetim işlemleri hakkında bilgi döndürmek için, [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) DMV ' de aşağıdaki sorguyu çalıştırın. Örneğin, işlemi ve işlemin IN_PROGRESS veya COMPLETED olan durumunu döndürür.

```sql
SELECT *
FROM
    sys.dm_operation_status
WHERE
    resource_type_desc = 'Database'
AND
    major_resource_id = 'mySampleDataWarehouse'
```

## <a name="next-steps"></a>Sonraki adımlar

Artık adanmış SQL havuzunuz için (eski adıyla SQL DW) işlem ölçeklendirmeyi öğrendiniz. Azure SYNAPSE Analytics hakkında daha fazla bilgi edinmek için veri yükleme öğreticisine geçin.

> [!div class="nextstepaction"]
>[Özel bir SQL havuzuna veri yükleme](./load-data-from-azure-blob-storage-using-copy.md)