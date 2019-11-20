---
title: 'Hızlı başlangıç: ölçek işlem-T-SQL '
description: T-SQL ve SQL Server Management Studio (SSMS) kullanarak Azure SQL Veri Ambarı’nda işlemi ölçeklendirin. Daha iyi performans için işlem ölçeğini genişletin veya maliyet tasarrufu için işlem ölçeğini daraltın.
services: sql-data-warehouse
author: Antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: implement
ms.date: 04/17/2018
ms.author: anvang
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 6729552262d7bea619948ddba406418b80cf69dc
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685951"
---
# <a name="quickstart-scale-compute-in-azure-sql-data-warehouse-using-t-sql"></a>Hızlı Başlangıç: T-SQL kullanarak Azure SQL Veri Ambarı’nda işlemi ölçeklendirme

T-SQL ve SQL Server Management Studio (SSMS) kullanarak Azure SQL Veri Ambarı’nda işlemi ölçeklendirin. Daha iyi performans için [işlem ölçeğini genişletin](sql-data-warehouse-manage-compute-overview.md) veya maliyet tasarrufu için işlem ölçeğini daraltın. 

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

## <a name="before-you-begin"></a>Başlamadan önce

[SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)’nun (SSMS) en yeni sürümünü indirin ve yükleyin.
 
## <a name="create-a-data-warehouse"></a>Veri ambarı oluşturma

[Hızlı Başlangıç: Oluşturma ve bağlanma - portal](create-data-warehouse-portal.md) bölümünü kullanarak **mySampleDataWarehouse** adlı bir veri ambarı oluşturun. Bir güvenlik duvarı kuralınız olduğundan ve SQL Server Management Studio içinden veri ambarınıza bağlanabildiğinizden emin olmak için hızlı başlangıcı tamamlayın.

## <a name="connect-to-the-server-as-server-admin"></a>Sunucu yöneticisi olarak sunucuya bağlanma

Bu bölümde Azure SQL sunucunuzla bağlantı kurmak için [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) kullanılmaktadır.

1. SQL Server Management Studio’yu açın.

2. **Sunucuya Bağlan** iletişim kutusuna şu bilgileri girin:

   | Ayar       | Önerilen değer | Açıklama | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | Sunucu türü | Veritabanı altyapısı | Bu değer gereklidir |
   | Sunucu adı | Tam sunucu adı | Örnek: **mynewserver-20171113.database.windows.net**. |
   | Kimlik Doğrulaması | SQL Server Kimlik Doğrulaması | Bu öğreticide yapılandırılan tek kimlik doğrulaması türü SQL Kimlik Doğrulamasıdır. |
   | Oturum Aç | Sunucu yöneticisi hesabı | Sunucuyu oluştururken belirttiğiniz hesap. |
   | Parola | Sunucu yöneticisi hesabınızın parolası | Bu, sunucuyu oluştururken belirttiğiniz paroladır. |

    ![sunucuya bağlan](media/load-data-from-azure-blob-storage-using-polybase/connect-to-server.png)

4. **Bağlan**'a tıklayın. SSMS’te Nesne Gezgini penceresi açılır. 

5. Nesne Gezgini’nde, **Veritabanları**’nı genişletin. Daha sonra yeni veritabanınızdaki nesneleri görüntülemek için **mySampleDatabase**’i genişletin.

    ![veritabanı nesneleri](media/create-data-warehouse-portal/connected.png) 

## <a name="view-service-objective"></a>Hizmet hedefini görüntüleme
Hizmet hedefi ayarı, veri ambarı için veri ambarı birimleri sayısını içerir. 

Veri ambarınız için geçerli veri ambarı birimlerini görüntülemek için:

1. **mynewserver-20171113.database.windows.net** bağlantısının altında **Sistem Veritabanları**’nı genişletin.
2. **Ana** seçeneğine sağ tıklayıp **Yeni Sorgu**’yu seçin. Yeni bir sorgu penceresi açılır.
3. sys.database_service_objectives dinamik yönetim görünümünden seçim yapmak için aşağıdaki sorguyu çalıştırın. 

    ```sql
    SELECT
        db.name [Database]
    ,   ds.edition [Edition]
    ,   ds.service_objective [Service Objective]
    FROM
        sys.database_service_objectives ds
    JOIN
        sys.databases db ON ds.database_id = db.database_id
    WHERE 
        db.name = 'mySampleDataWarehouse'
    ```

4. Aşağıdaki sonuçlar, **mySampleDataWarehouse** öğesinin DW400 hizmet hedefine sahip olduğunu gösterir. 

    ![Geçerli DWU’ları görüntüleme](media/quickstart-scale-compute-tsql/view-current-dwu.png)


## <a name="scale-compute"></a>Hesaplamayı ölçeklendirme
SQL Veri Ambarı’nda, veri ambarı birimlerini ayarlayarak işlem kaynaklarını artırabilir veya azaltabilirsiniz. [Oluşturma ve Bağlanma - portal](create-data-warehouse-portal.md) bölümünde **mySampleDataWarehouse** oluşturuldu ve 400 DWU ile başlatıldı. Aşağıdaki adımlar, **mySampleDataWarehouse** için DWU’ları ayarlar.

Veri ambarı birimlerini değiştirmek için:

1. **Ana** seçeneğine sağ tıklayıp **Yeni Sorgu**’yu seçin.
2. [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-database) T-SQL deyimini kullanarak hizmet hedefini değiştirin. Hizmet hedefini için DW300 olarak değiştirmek için aşağıdaki sorguyu çalıştırın. 

    ```Sql
    ALTER DATABASE mySampleDataWarehouse
    MODIFY (SERVICE_OBJECTIVE = 'DW300c')
    ;
    ```

## <a name="monitor-scale-change-request"></a>Ölçek değişikliği isteğini izleme
Önceki değişiklik isteğinin ilerlemesini görmek için sys.dm_operation_status dinamik yönetim görünümünü (DMV) yoklamak üzere `WAITFORDELAY` T-SQL söz dizimini kullanabilirsiniz.

Hizmet nesnesi değişiklik durumunu yoklamak için:

1. **Ana** seçeneğine sağ tıklayıp **Yeni Sorgu**’yu seçin.
2. sys.dm_operation_status DMV’sini yoklamak için aşağıdaki sorguyu çalıştırın.

    ```sql
    WHILE 
    (
        SELECT TOP 1 state_desc
        FROM sys.dm_operation_status
        WHERE 
            1=1
            AND resource_type_desc = 'Database'
            AND major_resource_id = 'MySampleDataWarehouse'
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

    ![İşlem durumu](media/quickstart-scale-compute-tsql/polling-output.png)

## <a name="check-data-warehouse-state"></a>Veri ambarı durumunu denetleme

Bir veri ambarı duraklatıldığında için T-SQL ile buna bağlanamazsınız. Veri ambarının geçerli durumunu görmek için bir PowerShell cmdlet’ini kullanabilirsiniz. Bir örnek için bkz. [Veri ambarı durumunu denetleme - Powershell](quickstart-scale-compute-powershell.md#check-data-warehouse-state). 

## <a name="check-operation-status"></a>İşlem durumunu denetleme

Çeşitli yönetim işlemleriyle ilgili bilgileri SQL Veri Ambarı’na döndürmek için [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) DMV’de aşağıdaki sorguyu çalıştırın. Örneğin, işlemi ve işlemin IN_PROGRESS veya COMPLETED olan durumunu döndürür.

```sql
SELECT *
FROM
    sys.dm_operation_status
WHERE
    resource_type_desc = 'Database'
AND 
    major_resource_id = 'MySampleDataWarehouse'
```


## <a name="next-steps"></a>Sonraki adımlar
Artık veri ambarınız için işlemin nasıl ölçeklendirileceğini öğrendiniz. Azure SQL Veri Ambarı hakkında daha fazla bilgi edinmek için, veri yükleme öğreticisiyle devam edin.

> [!div class="nextstepaction"]
>[Verileri bir SQL veri ambarına yükleme](load-data-from-azure-blob-storage-using-polybase.md)
