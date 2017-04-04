---
title: "Azure SQL Veri Ambarı - başlangıç öğreticisi | Microsoft Docs"
description: "Bu öğreticide verilerin sağlanması ve Azure SQL Veri Ambarı&quot;na yüklenmesi gösterilir. Ayrıca, ölçekleme, duraklatma ve ayarlama ile ilgili temel bilgileri öğrenirsiniz."
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: johnmac
editor: barbkess
ms.assetid: 52DFC191-E094-4B04-893F-B64D5828A900
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: quickstart
ms.date: 01/26/2017
ms.author: elbutter;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 9d3029817cae6570ff8871fbcb068250544595d7
ms.lasthandoff: 03/21/2017


---
# <a name="get-started-with-sql-data-warehouse"></a>SQL Veri Ambarı'nı kullanmaya başlayın

Bu öğreticide verilerin sağlanması ve Azure SQL Veri Ambarı'na yüklenmesi gösterilir. Ayrıca, ölçekleme, duraklatma ve ayarlama ile ilgili temel bilgileri öğrenirsiniz. Öğreticiyi tamamladığınızda, veri tabanınızı sorgulayabilir ve araştırabilirsiniz.

**Tahmini tamamlanma süresi:** Ön koşulları yerine getirmeniz durumunda yaklaşık 30 dakikada tamamlanan bir örnek kod ile birlikte uçtan uca öğreticidir. 

## <a name="prerequisites"></a>Ön koşullar

Bu öğreticide SQL Veri Ambarı ile ilgili temel kavramları bildiğiniz varsayılır. Bir tanıtım gerekirse bkz. [SQL Veri Ambarı nedir?](sql-data-warehouse-overview-what-is.md) 

### <a name="sign-up-for-microsoft-azure"></a>Microsoft Azure’a kaydolun
Bir Microsoft Azure hesabınız yoksa, bu hizmeti kullanabilmek için kaydolmanız gerekir. Zaten bir hesabınız varsa, bu adımı geçebilirsiniz. 

1. Hesap sayfalarına [https://azure.microsoft.com/account/](https://azure.microsoft.com/account/) gidin
2. Ücretsiz bir Azure hesabı oluşturun veya bir hesap satın alın.
3. Talimatları uygulayın

### <a name="install-appropriate-sql-client-drivers-and-tools"></a>Uygun SQL istemci sürücülerini ve araçlarını yükleyin

Çoğu SQL istemci aracı, JDBC, ODBC veya ADO.NET kullanarak SQL Veri Ambarı’na bağlanabilir. SQL Veri Ambarı’nın desteklediği çok sayıda T-SQL özelliği nedeniyle bazı istemci uygulamalar SQL Veri Ambarı ile tam olarak uyumlu değildir.

Bir Windows işletim sistemi çalıştırıyorsanız [Visual Studio] veya [SQL Server Management Studio] kullanmanız önerilir.

[!INCLUDE [Create a new logical server](../../includes/sql-data-warehouse-create-logical-server.md)] 

[!INCLUDE [SQL Database create server](../../includes/sql-database-create-new-server-firewall-portal.md)]

## <a name="create-a-sql-data-warehouse"></a>SQL Data Warehouse oluşturma

SQL Veri Ambarı, yüksek düzeyde paralel işleme için tasarlanmış özel bir veritabanı türüdür. Veritabanı birden fazla düğüme dağıtılmıştır ve sorguları paralel olarak işler. SQL Veri Ambarı tüm düğümlerin etkinliklerini düzenleyen bir denetim düğümüne sahiptir. Düğümler, verilerinizi yönetmek için SQL Veritabanı kullanır.  

> [!NOTE]
> SQL Veri Ambarı'nın oluşturulması ek hizmet ücretlerinin alınmasına neden olabilir.  Ayrıntılı bilgi için bkz. [SQL Veri Ambarı fiyatlandırması](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).
>

### <a name="create-a-data-warehouse"></a>Veri ambarı oluşturma

1. [Azure portal](https://portal.azure.com) oturum açın.
2. **Yeni** > **Veritabanları** > **SQL Veri Ambarı** öğelerine tıklayın.

    ![NewBlade](../../includes/media/sql-data-warehouse-create-dw/blade-click-new.png)
    ![SelectDW](../../includes/media/sql-data-warehouse-create-dw/blade-select-dw.png)

3. Dağıtım ayrıntılarını doldurun

    **Veritabanı Adı**: İstediğiniz bir adı seçin. Birden fazla veri ambarınız varsa, verdiğiniz adlarda bölge, ortam vb. ayrıntıların olması önerilir, örn. *mydw-westus-1-test*.

    **Abonelik**: Azure aboneliğiniz

    **Kaynak Grubu**: Bir kaynak grubu oluşturun veya mevcut bir kaynak grubunu kullanın.
    > [!NOTE]
    > Kaynak grupları, kapsam erişim denetimi ve şablonlu dağıtım gibi kaynak yönetimi için kullanışlıdır. Azure kaynak grupları ve en iyi uygulamalar hakkında daha fazla bilgiyi [burada](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) bulabilirsiniz

    **Kaynak**: Boş Veritabanı

    **Sunucu**: [Önkoşullar] içerisinde, oluşturduğunuz sunucu seçin.

    **Harmanlama**: Varsayılan harmanlamayı SQL_Latin1_General_CP1_CI_AS şeklinde bırakın.

    **Performans seçin**: Standart 400DWU ile başlamanız önerilir.

4. **Panoya sabitle**
    ![Panoya Sabitle](./media/sql-data-warehouse-get-started-tutorial/pin-to-dashboard.png) öğesini seçin

5. Arkanıza yaslanın ve veri ambarınızın dağıtılmasını bekleyin! Bu işlemin birkaç dakika sürmesi normal bir durumdur. Veri ambarınız kullanıma hazır olduğunda portal size bildirir. 

## <a name="connect-to-sql-data-warehouse"></a>SQL Data Warehouse'a bağlanma

Bu öğreticide veri ambarına bağlanmak için SQL Server Management Studio (SSMS) kullanılır. SQL Veri Ambarı’na desteklenen şu bağlayıcılar üzerinden bağlanabilirsiniz: ADO.NET, JDBC, ODBC ve PHP. Microsoft tarafından desteklenmeyen araçlar için işlevselliğin sınırlı olabileceğini unutmayın.


### <a name="get-connection-information"></a>Bağlantı bilgilerini alma

Veri ambarınıza bağlanmak için [Önkoşullar] içerisinde oluşturduğunuz mantıksal SQL sunucusu aracılığıyla bağlanmanız gerekir.

1. Panodan veri ambarınızı seçin veya kaynaklarınızda arayın.

    ![SQL Veri Ambarı Panosu](./media/sql-data-warehouse-get-started-tutorial/sql-dw-dashboard.png)

2. Mantıksal SQL sunucusu için tam adı bulun.

    ![Sunucu Adını seçin](./media/sql-data-warehouse-get-started-tutorial/select-server.png)

3. SSMS’yi açın ve [Önkoşullar] içerisinde oluşturduğunuz sunucu yöneticisi kimlik bilgilerini kullanarak bu sunucuya bağlanmak için nesne gezginini kullanın

    ![SSMS ile bağlanma](./media/sql-data-warehouse-get-started-tutorial/ssms-connect.png)

Her şey yolunda giderse, mantıksal SQL sunucunuza şu anda bağlı olmanız gerekir. Sunucu yöneticisi olarak oturum açtığınız için, ana veritabanı dahil sunucu tarafından barındırılan herhangi bir veritabanına bağlanabilirsiniz. 

Yalnızca bir sunucu yönetici hesabı vardır ve herhangi bir kullanıcıya göre en fazla ayrıcalığa sahiptir. Yönetici parolasını kuruluşunuzda çok sayıda kişinin bilmemesine dikkat edin. 

Bir Azure active directory yönetici hesabına da sahip olabilirsiniz. Onunla ilgili ayrıntılar burada verilmemektedir. Azure Active Directory kimlik doğrulaması hakkında daha fazla bilgi almak isterseniz bkz. [Azure AD kimlik doğrulaması](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication).

Ardından, ek oturumlar ve kullanıcılar oluşturma hakkında bilgi verilir.


## <a name="create-a-database-user"></a>Veritabanı kullanıcısı oluşturma

Bu adımda, veri ambarınıza erişmek için bir kullanıcı hesabı oluşturursunuz. Ayrıca, o kullanıcıya büyük miktarda bellek ve CPU kaynağı ile sorguları çalıştırma olanağı verme işlemi gösterilir.

### <a name="notes-about-resource-classes-for-allocating-resources-to-queries"></a>Sorgulara kaynak ayırmaya yönelik kaynak sınıfları ile ilgili notlar

- Verilerinizin güvenliğini sürdürmek için, üretim veritabanlarınızda sorgu çalıştırırken sunucu yöneticisini kullanmayın. Sunucu yöneticisi herhangi bir kullanıcıdan daha fazla ayrıcalığa sahiptir ve kullanıcı verileri üzerinde işlem yapmak için bu hesabın kullanılması, verilerinizi riske sokar. Ayrıca, sunucu yöneticisi yönetim işlemlerini yapmaya yönelik olduğundan, işlemleri az miktarda bellek ve CPU kaynağı ayırarak gerçekleştirir. 

- SQL Veri Ambarı, kullanıcılara farklı miktarda bellek, CPU kaynağı ve eşzamanlı kullanım hakkı ayırmak için kaynak sınıfı olarak adlandırılan önceden tanımlanmış veritabanı rolleri kullanır. Her kullanıcı küçük, orta, büyük veya çok büyük bir kaynak sınıfına ait olabilir. Kullanıcının kaynak sınıfı, kullanıcının sorguları ve yük işlemlerini çalıştırması için sahip olması gereken kaynakları belirler.

- En iyi veri sıkıştırma için kullanıcının büyük veya çok büyük kaynak ayırmaları ile yüklemesi gerekebilir. Kaynak sınıfları hakkında daha fazla bilgiyi [burada](./sql-data-warehouse-develop-concurrency.md#resource-classes) bulabilirsiniz:

### <a name="create-an-account-that-can-control-a-database"></a>Veritabanını denetleyebilen bir hesap oluşturma

Şu anda sunucu yöneticisi olarak oturum açtığınız için oturum ve kullanıcı oluşturma izinleriniz vardır.

1. SSMS veya başka bir sorgu istemcisini kullanarak **ana** için yeni bir sorgu açın.

    ![Asıl’da Yeni Sorgu](./media/sql-data-warehouse-get-started-tutorial/query-on-server.png)

    ![Asıl1’de Yeni Sorgu](./media/sql-data-warehouse-get-started-tutorial/query-on-master.png)

2. Sorgu penceresinde bu T-SQL komutunu çalıştırarak MedRCLogin adlı bir oturum ve LoadingUser adlı bir kullanıcı oluşturun. Bu oturum bilgileri mantıksal SQL sunucusuna bağlanabilir.

    ```sql
    CREATE LOGIN MedRCLogin WITH PASSWORD = 'a123reallySTRONGpassword!';
    CREATE USER LoadingUser FOR LOGIN MedRCLogin;
    ```

3. *SQL Veri Ambarı veritabanını* sorgularken, veritabanına erişmek ve üzerinde işlem gerçekleştirmek üzere oluşturduğunuz oturum bilgilerine göre bir veritabanı kullanıcısı oluşturun.

    ```sql
    CREATE USER LoadingUser FOR LOGIN MedRCLogin;
    ```

4. Veritabanı kullanıcısına NYT adlı veritabanı üzerinde denetim izinleri verin. 

    ```sql
    GRANT CONTROL ON DATABASE::[NYT] to LoadingUser;
    ```
    > [!NOTE]
    > Veritabanı adınızda kısa çizgi varsa, köşeli ayraç içine aldığınızdan emin olun! 
    >

### <a name="give-the-user-medium-resource-allocations"></a>Kullanıcıya orta büyüklükte kaynak ayırmalar sağlayın

1. Bu T-SQL komutunu çalıştırarak mediumrc adlı orta büyüklükte kaynak sınıfının üyesi yapın. 

    ```sql
    EXEC sp_addrolemember 'mediumrc', 'LoadingUser';
    ```
    > [!NOTE]
    > Eşzamanlılık ve kaynak sınıfları hakkında daha fazla bilgi için [buraya](sql-data-warehouse-develop-concurrency.md#resource-classes) tıklayın! 
    >

2. Yeni kimlik bilgileri ile mantıksal sunucuya bağlanma

    ![Yeni Oturum Bilgileriyle oturum açın](./media/sql-data-warehouse-get-started-tutorial/new-login.png)


## <a name="load-data-from-azure-blob-storage"></a>Azure blob depolamadan veri yükleme

Şimdi veri ambarınıza veri yüklemeye hazırsınız. Bu adımda, New York taksi verilerini genel bir Azure depolama blobundan nasıl yükleyeceğiniz gösterilmektedir. 

- SQL Veri Ambarı’na veri yüklemenin yaygın bir yolu, öncelikle verilerin Azure blob depolamaya taşınması, ardından veri ambarınıza yüklenmesidir. Yükleme işleminin anlaşılmasını kolaylaştırmak için, genel bir Azure depolama blobunda barındırılmakta olan New York taksi verilerini kullanacağız. 

- [Yüklemeye genel bakış](sql-data-warehouse-overview-load.md) bölümünde, verilerinizi Azure blob depolama alanına alma veya doğrudan kaynağınızdan SQL Veri Ambarı’na yükleme konusunda ileride işinize yarayacak bilgiler edinebilirsiniz.


### <a name="define-external-data"></a>Dış veri tanımlama

1. Bir ana anahtar oluşturun. Veritabanı başına yalnızca bir kez ana anahtar oluşturmanız gerekir. 

    ```sql
    CREATE MASTER KEY;
    ```

2. Taksi verilerini içeren Azure blobunun konumunu tanımlayın.  

    ```sql
    CREATE EXTERNAL DATA SOURCE NYTPublic
    WITH
    (
        TYPE = Hadoop,
        LOCATION = 'wasbs://2013@nytpublic.blob.core.windows.net/'
    );
    ```

3. Harici dosya biçimlerini tanımlayın

    ```CREATE EXTERNAL FILE FORMAT``` komutu, dış verileri içeren dosyaların biçimini belirtmek için kullanılır. Sınırlayıcı adlı bir veya daha fazla karakter ile ayrılmış metinler içerir. Tanıtım amacıyla, taksi verileri hem sıkıştırılmamış veri hem de gzip sıkıştırılmış verisi olarak depolanmıştır.

    Bu T-SQL komutlarını çalıştırarak iki farklı biçimi tanımlayın: sıkıştırılmamış ve sıkıştırılmış.

    ```sql
    CREATE EXTERNAL FILE FORMAT uncompressedcsv
    WITH (
        FORMAT_TYPE = DELIMITEDTEXT,
        FORMAT_OPTIONS ( 
            FIELD_TERMINATOR = ',',
            STRING_DELIMITER = '',
            DATE_FORMAT = '',
            USE_TYPE_DEFAULT = False
        )
    );

    CREATE EXTERNAL FILE FORMAT compressedcsv
    WITH ( 
        FORMAT_TYPE = DELIMITEDTEXT,
        FORMAT_OPTIONS ( FIELD_TERMINATOR = '|',
            STRING_DELIMITER = '',
        DATE_FORMAT = '',
            USE_TYPE_DEFAULT = False
        ),
        DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec'
    );
    ```

4.  Dış dosya biçiminiz için bir şema oluşturun. 

    ```sql
    CREATE SCHEMA ext;
    ```
5. Harici tabloları oluşturun. Bu tablolar Azure blob depolamada saklanan verilere başvurur. Aşağıdaki T-SQL komutlarını çalıştırarak, tamamı dış veri kaynağımızda daha önce tanımladığımız Azure blobunu işaret eden dış tablolar oluşturun.

```sql
    CREATE EXTERNAL TABLE [ext].[Date] 
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
        LOCATION = 'Date',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = uncompressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0
    );
    
    CREATE EXTERNAL TABLE [ext].[Geography]
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
        LOCATION = 'Geography',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = uncompressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0 
    );
        
    
    CREATE EXTERNAL TABLE [ext].[HackneyLicense]
    (
        [HackneyLicenseID] int NOT NULL,
        [HackneyLicenseBKey] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [HackneyLicenseCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        LOCATION = 'HackneyLicense',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = uncompressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0
    )
    ;
        
    
    CREATE EXTERNAL TABLE [ext].[Medallion]
    (
        [MedallionID] int NOT NULL,
        [MedallionBKey] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [MedallionCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        LOCATION = 'Medallion',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = uncompressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0
    )
    ;
        
    CREATE EXTERNAL TABLE [ext].[Time]
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
        LOCATION = 'Time',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = uncompressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0
    )
    ;
    
    
    CREATE EXTERNAL TABLE [ext].[Trip]
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
        LOCATION = 'Trip2013',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = compressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0
    )
    ;
    
    CREATE EXTERNAL TABLE [ext].[Weather]
    (
        [DateID] int NOT NULL,
        [GeographyID] int NOT NULL,
        [PrecipitationInches] float NOT NULL,
        [AvgTemperatureFahrenheit] float NOT NULL
    )
    WITH
    (
        LOCATION = 'Weather2013',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = uncompressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0
    )
    ;
```

### <a name="import-the-data-from-azure-blob-storage"></a>Azure blob depolamadan veri alma

SQL Veri Ambarı, CREATE TABLE AS SELECT (CTAS) adlı bir anahtar deyimini destekler. Bu deyim bir select deyiminin sonuçlarına göre yeni bir tablo oluşturur. Yeni tablo, select deyiminin sonuçları ile aynı sütunlara ve veri türlerine sahiptir.  Bu yöntem, Azure blob depolamadan SQL Veri Ambarı’na veri almanın güzel bir yoludur.

1. Verilerinizi içeri aktarmak için bu betiği çalıştırın.

    ```sql
    CREATE TABLE [dbo].[Date]
    WITH
    ( 
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS SELECT * FROM [ext].[Date]
    OPTION (LABEL = 'CTAS : Load [dbo].[Date]')
    ;
    
    CREATE TABLE [dbo].[Geography]
    WITH
    ( 
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT * FROM [ext].[Geography]
    OPTION (LABEL = 'CTAS : Load [dbo].[Geography]')
    ;
    
    CREATE TABLE [dbo].[HackneyLicense]
    WITH
    ( 
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS SELECT * FROM [ext].[HackneyLicense]
    OPTION (LABEL = 'CTAS : Load [dbo].[HackneyLicense]')
    ;
    
    CREATE TABLE [dbo].[Medallion]
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS SELECT * FROM [ext].[Medallion]
    OPTION (LABEL = 'CTAS : Load [dbo].[Medallion]')
    ;
    
    CREATE TABLE [dbo].[Time]
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS SELECT * FROM [ext].[Time]
    OPTION (LABEL = 'CTAS : Load [dbo].[Time]')
    ;
    
    CREATE TABLE [dbo].[Weather]
    WITH
    ( 
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS SELECT * FROM [ext].[Weather]
    OPTION (LABEL = 'CTAS : Load [dbo].[Weather]')
    ;
    
    CREATE TABLE [dbo].[Trip]
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS SELECT * FROM [ext].[Trip]
    OPTION (LABEL = 'CTAS : Load [dbo].[Trip]')
    ;
    ```

2. Verilerinizi yüklenirken görüntüleyin.

   Birkaç GB veri yüklüyorsunuz ve yüksek performanslı kümelenmiş columnstore dizinlerine sıkıştırıyorsunuz. Yüklemenin durumunu göstermek için, dinamik yönetim görünümleri (DMV’ler) kullanan aşağıdaki sorguyu çalıştırın. Sorguyu başlattıktan sonra, SQL Veri Ambarı ağır yükü kaldırırken siz bir kahve alıp arkanıza yaslanın.
    
    ```sql
    SELECT
        r.command,
        s.request_id,
        r.status,
        count(distinct input_name) as nbr_files,
        sum(s.bytes_processed)/1024/1024 as gb_processed
    FROM 
        sys.dm_pdw_exec_requests r
        INNER JOIN sys.dm_pdw_dms_external_work s
        ON r.request_id = s.request_id
    WHERE
        r.[label] = 'CTAS : Load [dbo].[Date]' OR
        r.[label] = 'CTAS : Load [dbo].[Geography]' OR
        r.[label] = 'CTAS : Load [dbo].[HackneyLicense]' OR
        r.[label] = 'CTAS : Load [dbo].[Medallion]' OR
        r.[label] = 'CTAS : Load [dbo].[Time]' OR
        r.[label] = 'CTAS : Load [dbo].[Weather]' OR
        r.[label] = 'CTAS : Load [dbo].[Trip]'
    GROUP BY
        r.command,
        s.request_id,
        r.status
    ORDER BY
        nbr_files desc, 
        gb_processed desc;
    ```

3. Tüm sistem sorgularını görüntüleyin.

    ```sql
    SELECT * FROM sys.dm_pdw_exec_requests;
    ```

4. Azure SQL Veri Ambarı’nıza verilerinizin sorunsuz şekilde yüklenmesinin keyfini çıkarın.

    ![Yüklenen Verilere bakın](./media/sql-data-warehouse-get-started-tutorial/see-data-loaded.png)


## <a name="improve-query-performance"></a>Sorgu performansını artırma

Sorgu performansını artırmanın ve SQL Veri Ambarı’nın tasarım amacı olan yüksek hızlı performans elde etmenin birkaç yolu vardır.  

### <a name="see-the-effect-of-scaling-on-query-performance"></a>Ölçeklendirmenin sorgu performansı üzerindeki etkisine bakın 

Sorgu performansını artırmanın bir yolu, veri ambarınız için DWU hizmet düzeyini değiştirerek kaynakları ölçeklendirmektir. Her hizmet düzeyi daha fazla maliyet getirir, ancak dilediğiniz zaman kaynakların ölçeğini azaltabilir veya kaynakları duraklatabilirsiniz. 

Bu adımda, iki farklı DWU ayarında performansı karşılaştırırsınız.

İlk olarak, bir işlem düğümünün kendi kendine nasıl çalışabileceğini anlamak için boyutu ölçeklendirerek 100 DWU’ya düşürelim.

1. Portala gidin ve SQL Veri Ambarı’nızı seçin.

2. SQL Veri Ambarı dikey penceresinde ölçeği seçin. 

    ![Portaldan Ölçek DW](./media/sql-data-warehouse-get-started-tutorial/scale-dw.png)

3. Performans çubuğunun ölçeğini düşürerek 100 DWU yapın ve kaydet öğesine tıklayın.

    ![Ölçek ve kayıt](./media/sql-data-warehouse-get-started-tutorial/scale-and-save.png)

4. Ölçek işleminizin tamamlanmasını bekleyin.

    > [!NOTE]
    > Ölçek değiştirilirken sorgular çalıştırılamaz. Ölçeklendirme, o anda çalışmakta olan sorgularınızı **sonlandırır**. İşlemi tamamladığında sorgularınızı yeniden başlatabilirsiniz.
    >
    
5. Tüm sütunlar için üst milyon girişler seçerek, giden veriler üzerinde tarama işlemi gerçekleştirin. Hızlı ilerlemek istiyorsanız, daha az satır seçmekten çekinmeyin. Bu işlemin çalıştırılması için geçen süreyi not edin.

    ```sql
    SELECT TOP(1000000) * FROM dbo.[Trip]
    ```
6. Veri ambarınızı 400 DWU’ya geri ölçeklendirin. Her 100 DWU’nun, Azure SQL Veri Ambarı’nıza başka bir işlem düğümü eklediğini unutmayın.

7. Sorguyu tekrar çalıştırın! Önemli bir fark dikkat göreceksiniz. 

> [!NOTE]
> SQL Veri Ambarı, yüksek düzeyde paralel işleme kullanır. Milyonlarca satır üzerinde tarama yapan veya analiz işlevleri gerçekleştiren sorgular, Azure SQL Veri Ambarı’nın gerçek gücünü deneyimler.
>

### <a name="see-the-effect-of-statistics-on-query-performance"></a>İstatistiklerin sorgu performansı üzerindeki etkisine bakın

1. Tarih tablosu Seyahat tablosu ile birleştiren bir sorgu çalıştırın

    ```sql
    SELECT TOP (1000000) 
        dt.[DayOfWeek],
        tr.[MedallionID],
        tr.[HackneyLicenseID],
        tr.[PickupTimeID],
        tr.[DropoffTimeID],
        tr.[PickupGeographyID],
        tr.[DropoffGeographyID],
        tr.[PickupLatitude],
        tr.[PickupLongitude],
        tr.[PickupLatLong],
        tr.[DropoffLatitude],
        tr.[DropoffLongitude],
        tr.[DropoffLatLong],
        tr.[PassengerCount],
        tr.[TripDurationSeconds],
        tr.[TripDistanceMiles],
        tr.[PaymentType],
        tr.[FareAmount],
        tr.[SurchargeAmount],
        tr.[TaxAmount],
        tr.[TipAmount],
        tr.[TollsAmount],
        tr.[TotalAmount]
    FROM [dbo].[Trip] as tr
        JOIN dbo.[Date] as dt
        ON  tr.DateID = dt.DateID
    ```

    SQL Veri Ambarı’nın birleştirme işleminden önce verileri karıştırması gerektiğinden bu sorgu biraz uzun sürer. Birleşimler, verileri dağıtıldığı şekilde birleştirmek üzere tasarlanırsa verilerin karıştırılması gerekmez. Bu daha derin bir konudur. 

2. İstatistikler fark yaratır. 
3. Birleşim sütunlarında istatistikler oluşturmak için bu deyimi çalıştırın.

    ```sql
    CREATE STATISTICS [dbo.Date DateID stats] ON dbo.Date (DateID);
    CREATE STATISTICS [dbo.Trip DateID stats] ON dbo.Trip (DateID);
    ```

    > [!NOTE]
    > SQL DW istatistikleri sizin için otomatik olarak yönetmez. İstatistikleri sorgu performansı için önemlidir ve istatistikleri oluşturmanız ve güncelleştirmeniz önemle tavsiye edilir.
    > 
    > **En çok faydayı, birleştirmelerin bulunduğu sütunlar, WHERE yan tümcesinde kullanılan sütunlar ve GROUP BY içinde bulunan sütunlar için istatistik tutarak elde edebilirsiniz.**
    >

3. Önkoşullar’dan sorguyu yeniden çalıştırın ve tüm performans farklarını inceleyin. Sorgu performansı farklılıkları ölçek büyütme kadar güçlü olmaz, ancak bir hız yükselmesi fark edersiniz. 

## <a name="next-steps"></a>Sonraki adımlar

Şimdi sorgulamaya ve incelemeye hazırsınız. En iyi yöntemlerimize veya ipuçlarımıza bakın.

Keşfetmeyi bitirdiğinizde, örneğinizi duraklatmayı unutmayın! Üretimde, iş gereksinimlerinizi karşılamak üzere, duraklatma ve ölçeklendirme yoluyla muazzam tasarruflar sağlayabilirsiniz.

![Duraklat](./media/sql-data-warehouse-get-started-tutorial/pause.png)

## <a name="useful-readings"></a>Yararlı okumalar

[Eşzamanlılık ve İş Yükü Yönetimi][]

[Azure SQL Veri Ambarı için en iyi yöntemler][]

[Sorgu İzleme][]

[Büyük Ölçekli İlişkisel Veri Ambarı Oluşturmaya Yönelik En İyi 10 Yöntem][]

[Azure SQL Veri Ambarı’na Veri Geçirme][]

[Eşzamanlılık ve İş Yükü Yönetimi]: sql-data-warehouse-develop-concurrency.md#change-a-user-resource-class-example
[Azure SQL Veri Ambarı için en iyi yöntemler]: sql-data-warehouse-best-practices.md#hash-distribute-large-tables
[Sorgu İzleme]: sql-data-warehouse-manage-monitor.md
[Büyük Ölçekli İlişkisel Veri Ambarı Oluşturmaya Yönelik En İyi 10 Yöntem]: https://blogs.msdn.microsoft.com/sqlcat/2013/09/16/top-10-best-practices-for-building-a-large-scale-relational-data-warehouse/
[Azure SQL Veri Ambarı’na Veri Geçirme]: https://blogs.msdn.microsoft.com/sqlcat/2016/08/18/migrating-data-to-azure-sql-data-warehouse-in-practice/



[!INCLUDE [Additional Resources](../../includes/sql-data-warehouse-article-footer.md)]

<!-- Internal Links -->
[Önkoşullar]: sql-data-warehouse-get-started-tutorial.md#prerequisites

<!--Other Web references-->
[Visual Studio]: https://www.visualstudio.com/
[SQL Server Management Studio]: https://msdn.microsoft.com/en-us/library/mt238290.aspx

