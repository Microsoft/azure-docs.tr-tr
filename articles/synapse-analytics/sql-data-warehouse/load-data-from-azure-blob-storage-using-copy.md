---
title: 'Öğretici: New York Taxicab verilerini yükleme'
description: Öğretici, SYNAPSE SQL için bir Azure blobundan New York Taxicab verilerini yüklemek üzere Azure portal ve SQL Server Management Studio kullanır.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/23/2020
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 7ede40aba8e2d36e4262b4bc89a35f5d67079e0e
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567524"
---
# <a name="tutorial-load-the-new-york-taxicab-dataset"></a>Öğretici: New York Taxicab veri kümesini yükleme

Bu öğretici, New York Taxıab veri kümesini bir Azure Blob depolama hesabından yüklemek için [Copy ifadesini](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true) kullanır. Öğreticide aşağıdaki işlemler için [Azure Portal](https://portal.azure.com) ve [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) (SSMS) kullanılır:

> [!div class="checklist"]
>
> * Verileri yüklemek için belirlenen bir kullanıcı oluşturma
> * Örnek veri kümesi için tabloları oluşturma 
> * Veri ambarınıza veri yüklemek için COPY T-SQL ifadesini kullanın
> * Yüklendikleri sırada verilerin ilerleme durumunu görüntüleme

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="before-you-begin"></a>Başlamadan önce

Bu öğreticiye başlamadan önce, [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)’nun (SSMS) en yeni sürümünü indirin ve yükleyin.  

Bu öğreticide, aşağıdaki [öğreticiden](./create-data-warehouse-portal.md#connect-to-the-server-as-server-admin)zaten bir SQL adanmış havuzu oluşturmuş olduğunuz varsayılmaktadır.

## <a name="create-a-user-for-loading-data"></a>Verileri yüklemek için kullanıcı oluşturma

Sunucu yöneticisi hesabı yönetim işlemlerini gerçekleştirmeye yöneliktir ve kullanıcı verileri üzerinde sorgu çalıştırmaya uygun değildir. Verileri yükleme, yoğun bellek kullanan bir işlemdir. Bellek üst sınırları, [veri ambarı birimlerine](what-is-a-data-warehouse-unit-dwu-cdwu.md) ve yapılandırılan [kaynak sınıfına](resource-classes-for-workload-management.md) göre tanımlanır.

En iyisi verileri yüklemeye ayrılmış bir oturum açma ve kullanıcı bilgisi oluşturmaktır. Ardından yükleme kullanıcısını uygun bir bellek ayırma üst sınırına olanak tanıyan bir [kaynak sınıfına](resource-classes-for-workload-management.md) ekleyin.

Oturum açma bilgileri ve kullanıcılar oluşturabilmeniz için sunucu yöneticisi olarak bağlanın. Şu adımları kullanarak **LoaderRC20** adlı bir oturum açma bilgisi ve kullanıcı oluşturun. Sonra kullanıcıyı **staticrc20** kaynak sınıfına atayın.

1. SSMS 'de, açılan menüyü göstermek için **ana öğe** ' yi sağ seçin ve **Yeni sorgu**' yı seçin. Yeni bir sorgu penceresi açılır.

    ![Asıl veritabanında yeni sorgu](./media/load-data-from-azure-blob-storage-using-polybase/create-loader-login.png)

2. Sorgu penceresinde, şu T-SQL komutlarını girerek LoaderRC20 adlı bir oturum açma bilgisi ve kullanıcı oluşturun, 'a123STRONGpassword!' yerine kendi parolanızı girin.

    ```sql
    CREATE LOGIN LoaderRC20 WITH PASSWORD = 'a123STRONGpassword!';
    CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
    ```

3. **Yürüt**’ü seçin.

4. **mySampleDataWarehouse**’a sağ tıklayıp **Yeni Sorgu**’yu seçin. Yeni bir sorgu penceresi açılır.  

    ![Örnek veri ambarında yeni sorgu](./media/load-data-from-azure-blob-storage-using-polybase/create-loading-user.png)

5. Aşağıdaki T-SQL komutlarını girerek LoaderRC20 oturum açma bilgisi için LoaderRC20 adlı bir veritabanı kullanıcısı oluşturun. İkinci satır, yeni kullanıcıya yeni veri ambarı üzerinde DENETİM izinleri verir.  Bu izinler, kullanıcıyı veritabanı sahibi yapmaya benzer. Üçüncü satır, yeni kullanıcı staticrc20 [kaynak sınıfına](resource-classes-for-workload-management.md) üye olarak ekler.

    ```sql
    CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
    GRANT CONTROL ON DATABASE::[mySampleDataWarehouse] to LoaderRC20;
    EXEC sp_addrolemember 'staticrc20', 'LoaderRC20';
    ```

6. **Yürüt**’ü seçin.

## <a name="connect-to-the-server-as-the-loading-user"></a>Yükleme kullanıcısı olarak sunucuya bağlanma

Verileri yüklemenin ilk adımı LoaderRC20 olarak oturum açmaktır.  

1. Nesne Gezgini ' de **Bağlan** açılan menüsünü seçin ve **veritabanı altyapısı**' nı seçin. **Sunucuya Bağlan** iletişim kutusu görüntülenir.

    ![Yeni oturum açma bilgileriyle bağlanma](./media/load-data-from-azure-blob-storage-using-polybase/connect-as-loading-user.png)

2. Tam sunucu adını girin ve Oturum Açma bilgisi olarak **LoaderRC20** girin.  LoaderRC20 için parolanızı girin.

3. **Bağlan**’ı seçin.

4. Bağlantınız hazır olduğunda, Nesne Gezgini'nde iki sunucu bağlantısı görürsünüz. Bağlantılardan biri ServerAdmin ve diğeri de MedRCLogin olarak gösterilir.

    ![Bağlantı başarılı oldu](./media/load-data-from-azure-blob-storage-using-polybase/connected-as-new-login.png)

## <a name="create-tables-for-the-sample-data"></a>Örnek veriler için tablo oluşturma

Verileri yeni veri ambarınıza yükleme işlemine başlamaya hazırsınız. Öğreticinin bu bölümünde, New York City TAXI cab veri kümesini bir Azure Storage blobundan yüklemek için COPY ifadesinin nasıl kullanılacağı gösterilmektedir. Daha sonra başvurmak üzere verilerinizi Azure Blob depolama alanına alma veya doğrudan kaynağınızdan yükleme hakkında bilgi edinmek için bkz. [yüklemeye genel bakış](design-elt-data-loading.md).

Aşağıdaki SQL betiklerini çalıştırın ve yüklemek istediğiniz veriler hakkındaki bilgileri belirtin. Bu bilgiler verilerin konumu, verilerdeki içeriğin biçimi ve verilerin tablo tanımıdır.

1. Önceki bölümde veri ambarınızda LoaderRC20 olarak oturum açmıştınız. SSMS'de, LoaderRC20 bağlantınıza sağ tıklayın ve **Yeni Sorgu**'yu seçin.  Yeni bir sorgu penceresi görüntülenir.

    ![Yeni yükleme sorgusu penceresi](./media/load-data-from-azure-blob-storage-using-polybase/new-loading-query.png)

2. Sorgu pencerenizi önceki resimle karşılaştırın.  Yeni sorgu pencerenizin LoaderRC20 olarak çalıştırıldığını ve MySampleDataWarehouse veritabanınız üzerinde sorgular gerçekleştirdiğini doğrulayın. Tüm yükleme adımlarını gerçekleştirmek için bu sorgu penceresini kullanın.

7. Tabloları oluşturmak için aşağıdaki T-SQL deyimlerini çalıştırın:

    ```sql
    CREATE TABLE [dbo].[Date]
    (
        [DateID] int NOT NULL,
        [Date] datetime NULL,
        [DateBKey] char(10) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfMonth] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DaySuffix] varchar(4) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfWeek] char(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfWeekInMonth] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfWeekInYear] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfQuarter] varchar(3) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfYear] varchar(3) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [WeekOfMonth] varchar(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [WeekOfQuarter] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [WeekOfYear] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [Month] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [MonthName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [MonthOfQuarter] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [Quarter] char(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [QuarterName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [Year] char(4) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [YearName] char(7) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [MonthYear] char(10) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [MMYYYY] char(6) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [FirstDayOfMonth] date NULL,
        [LastDayOfMonth] date NULL,
        [FirstDayOfQuarter] date NULL,
        [LastDayOfQuarter] date NULL,
        [FirstDayOfYear] date NULL,
        [LastDayOfYear] date NULL,
        [IsHolidayUSA] bit NULL,
        [IsWeekday] bit NULL,
        [HolidayUSA] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[Geography]
    (
        [GeographyID] int NOT NULL,
        [ZipCodeBKey] varchar(10) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [County] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [City] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [State] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [Country] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [ZipCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[HackneyLicense]
    (
        [HackneyLicenseID] int NOT NULL,
        [HackneyLicenseBKey] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [HackneyLicenseCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[Medallion]
    (
        [MedallionID] int NOT NULL,
        [MedallionBKey] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [MedallionCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[Time]
    (
        [TimeID] int NOT NULL,
        [TimeBKey] varchar(8) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [HourNumber] tinyint NOT NULL,
        [MinuteNumber] tinyint NOT NULL,
        [SecondNumber] tinyint NOT NULL,
        [TimeInSecond] int NOT NULL,
        [HourlyBucket] varchar(15) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [DayTimeBucketGroupKey] int NOT NULL,
        [DayTimeBucket] varchar(100) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[Trip]
    (
        [DateID] int NOT NULL,
        [MedallionID] int NOT NULL,
        [HackneyLicenseID] int NOT NULL,
        [PickupTimeID] int NOT NULL,
        [DropoffTimeID] int NOT NULL,
        [PickupGeographyID] int NULL,
        [DropoffGeographyID] int NULL,
        [PickupLatitude] float NULL,
        [PickupLongitude] float NULL,
        [PickupLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DropoffLatitude] float NULL,
        [DropoffLongitude] float NULL,
        [DropoffLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [PassengerCount] int NULL,
        [TripDurationSeconds] int NULL,
        [TripDistanceMiles] float NULL,
        [PaymentType] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [FareAmount] money NULL,
        [SurchargeAmount] money NULL,
        [TaxAmount] money NULL,
        [TipAmount] money NULL,
        [TollsAmount] money NULL,
        [TotalAmount] money NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[Weather]
    (
        [DateID] int NOT NULL,
        [GeographyID] int NOT NULL,
        [PrecipitationInches] float NOT NULL,
        [AvgTemperatureFahrenheit] float NOT NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    ```
    

## <a name="load-the-data-into-your-data-warehouse"></a>Verileri veri ambarınıza yükleme

Bu bölüm, örnek verileri Azure Depolama Blobu [yüklemek Için Copy ifadesini](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true) kullanır.  

> [!NOTE]
> Bu öğretici verileri doğrudan son tabloya yükler. Genellikle üretim iş yükleriniz için bir hazırlama tablosuna yüklersiniz. Veriler hazırlama tablosundayken tüm gerekli dönüştürmeleri yapabilirsiniz. 

1. Verileri yüklemek için aşağıdaki deyimlerini çalıştırın:

    ```sql
    COPY INTO [dbo].[Date]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Date'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = ''
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Date] - Taxi dataset');
    
    
    COPY INTO [dbo].[Geography]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Geography'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = ''
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Geography] - Taxi dataset');
    
    COPY INTO [dbo].[HackneyLicense]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/HackneyLicense'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = ''
    )
    OPTION (LABEL = 'COPY : Load [dbo].[HackneyLicense] - Taxi dataset');
    
    COPY INTO [dbo].[Medallion]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Medallion'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = ''
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Medallion] - Taxi dataset');
    
    COPY INTO [dbo].[Time]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Time'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = ''
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Time] - Taxi dataset');
    
    COPY INTO [dbo].[Weather]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Weather'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = '',
        ROWTERMINATOR='0X0A'
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Weather] - Taxi dataset');
    
    COPY INTO [dbo].[Trip]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Trip2013'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = '|',
        FIELDQUOTE = '',
        ROWTERMINATOR='0X0A',
        COMPRESSION = 'GZIP'
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Trip] - Taxi dataset');
    ```

2. Verilerinizi yüklenirken görüntüleyin. Birkaç GB veri yüklüyorsunuz ve yüksek performanslı columnstore dizinlerine sıkıştırıyorsunuz. Yüklemenin durumunu göstermek için, dinamik yönetim görünümleri (DMV’ler) kullanan aşağıdaki sorguyu çalıştırın.

    ```sql
    SELECT  r.[request_id]                           
    ,       r.[status]                               
    ,       r.resource_class                         
    ,       r.command
    ,       sum(bytes_processed) AS bytes_processed
    ,       sum(rows_processed) AS rows_processed
    FROM    sys.dm_pdw_exec_requests r
                  JOIN sys.dm_pdw_dms_workers w
                         ON r.[request_id] = w.request_id
    WHERE [label] = 'COPY : Load [dbo].[Date] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[Geography] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[HackneyLicense] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[Medallion] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[Time] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[Weather] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[Trip] - Taxi dataset' 
    and session_id <> session_id() and type = 'WRITER'
    GROUP BY r.[request_id]                           
    ,       r.[status]                               
    ,       r.resource_class                         
    ,       r.command;
    ```
    
3. Tüm sistem sorgularını görüntüleyin.

    ```sql
    SELECT * FROM sys.dm_pdw_exec_requests;
    ```

4. Veri ambarınıza verilerinizin sorunsuz şekilde yüklenmesinin keyfini çıkarın.

    ![Yüklenen tabloları görüntüleme](./media/load-data-from-azure-blob-storage-using-polybase/view-loaded-tables.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

İşlem kaynakları ve veri ambarınıza yüklediğiniz veriler için ücretlendirilirsiniz. Bunlar ayrı faturalandırılır.

* Verileri depoda tutmak istiyorsanız, veri ambarını kullanmadığınız zamanlarda işlemi duraklatabilirsiniz. İşlemi duraklattığınızda yalnızca veri depolaması için ücretlendirilirsiniz ve verilerle çalışmaya hazır olduğunuzda işlemi sürdürebilirsiniz.
* Gelecekteki ücretlendirmeleri kaldırmak istiyorsanız, veri ambarını silebilirsiniz.

Kaynakları istediğiniz gibi temizlemek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com)oturum açın, veri Ambarınızı seçin.

    ![Kaynakları temizleme](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. İşlem duraklatmak için **Duraklat** düğmesini seçin. Veri ambarı duraklatıldığında, bir **Başlat** düğmesi görürsünüz.  İşlem işlemini sürdürmesini sağlamak için **Başlat**' ı seçin.

3. Veri ambarını kaldırmak için işlem veya depolama için ücretlendirilmezsiniz, **Sil**' i seçin.

4. Oluşturduğunuz sunucuyu kaldırmak için önceki görüntüde **MyNewServer-20180430.Database.Windows.net** ' ı seçin ve **Sil**' i seçin.  Sunucuyu silmek sunucuyla ilişkili tüm veritabanlarını da sileceğinden bu işlemi gerçekleştirirken dikkatli olun.

5. Kaynak grubunu kaldırmak için **Myresourcegroup**' ı seçin ve **kaynak grubunu sil**' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, veri ambarı oluşturmayı ve verileri yüklemek için kullanıcı oluşturmayı öğrendiniz. Veri ambarınıza veri yüklemek için basit [kopyalama ifadesini](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true#examples) kullandınız.

Şu işlemleri yaptınız:
> [!div class="checklist"]
>
> * Azure Portal'da veri ambarı oluşturuldu
> * Azure portalında sunucu düzeyinde güvenlik duvarı kuralı ayarlama
> * SSMS ile veri ambarına bağlandı
> * Verileri yüklemek için belirlenen bir kullanıcı oluşturuldu
> * Örnek veriler için tablolar oluşturuldu
> * Veri ambarınıza veri yüklemek için COPY T-SQL ifadesini kullandınız
> * Yüklendikleri sırada verilerin ilerleme durumu görüntülendi

Mevcut bir veritabanını Azure SYNAPSE Analytics 'e geçirmeyi öğrenmek için geliştirmeye genel bakış konusuna ilerleyin:

> [!div class="nextstepaction"]
> [Mevcut bir veritabanını Azure SYNAPSE Analytics 'e geçirmeye yönelik tasarım kararları](sql-data-warehouse-overview-develop.md)

Daha fazla örnek ve başvuru yüklemek için aşağıdaki belgeleri görüntüleyin:

- [Deyimdeki başvuru belgelerini Kopyala](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true#syntax)
- [Her kimlik doğrulama yöntemi için örnekleri KOPYALAYıN](./quickstart-bulk-load-copy-tsql-examples.md)
- [Tek bir tablo için hızlı başlangıç kopyalama](./quickstart-bulk-load-copy-tsql.md)
