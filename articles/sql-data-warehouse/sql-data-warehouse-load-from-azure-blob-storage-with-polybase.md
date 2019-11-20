---
title: Contoso perakende verilerini yükleme
description: Contoso perakende verilerinden Azure SQL Analytics ' ye iki tablo yüklemek için PolyBase ve T-SQL komutlarını kullanın.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: load-data
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: d928f2392f204baae6cfdbe864938ef0dee1d6ca
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692666"
---
# <a name="load-contoso-retail-data-to-a-sql-analytics-data-warehouse"></a>Contoso perakende verilerini bir SQL Analytics veri ambarına yükleme

Bu öğreticide, contoso perakende verilerinden bir SQL Analytics veri ambarına iki tablo yüklemek için PolyBase ve T-SQL komutlarını kullanmayı öğreneceksiniz. 

Bu öğreticide şunları yapmanız gerekir:

1. PolyBase 'i Azure Blob depolamadan yüklenecek şekilde yapılandırma
2. Veritabanınıza ortak verileri yükleme
3. Yükleme tamamlandıktan sonra iyileştirmeleri gerçekleştirin.

## <a name="before-you-begin"></a>Başlamadan önce
Bu öğreticiyi çalıştırmak için, zaten bir SQ Analytics veri ambarına sahip bir Azure hesabınızın olması gerekir. Sağlanmış bir veri ambarınız yoksa, bkz. [SQL veri ambarı oluşturma ve sunucu düzeyinde güvenlik duvarı kuralı ayarlama] [SQL veri ambarı oluşturma].

## <a name="1-configure-the-data-source"></a>1. veri kaynağını yapılandırın
PolyBase, dış verilerin konumunu ve özniteliklerini tanımlamak için T-SQL dış nesnelerini kullanır. Dış nesne tanımları, SQL Analytics veri ambarında depolanır. Veriler dışarıdan depolanır.

### <a name="11-create-a-credential"></a>1,1. Kimlik bilgisi oluşturma
Contoso ortak verilerini yüklüyorsanız **Bu adımı atlayın** . Herkese zaten erişebildiğinden ortak verilere güvenli erişim gerekmez.

Bu öğreticiyi kendi verilerinizi yüklemek için bir şablon olarak kullanıyorsanız **Bu adımı atlayın** . Bir kimlik bilgisi aracılığıyla verilere erişmek için aşağıdaki betiği kullanarak veritabanı kapsamlı bir kimlik bilgisi oluşturun ve veri kaynağının konumunu tanımlarken kullanın.

```sql
-- A: Create a master key.
-- Only necessary if one does not already exist.
-- Required to encrypt the credential secret in the next step.

CREATE MASTER KEY;


-- B: Create a database scoped credential
-- IDENTITY: Provide any string, it is not used for authentication to Azure storage.
-- SECRET: Provide your Azure storage account key.


CREATE DATABASE SCOPED CREDENTIAL AzureStorageCredential
WITH
    IDENTITY = 'user',
    SECRET = '<azure_storage_account_key>'
;


-- C: Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure blob storage.
-- LOCATION: Provide Azure storage account name and blob container name.
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureStorage
WITH (
    TYPE = HADOOP,
    LOCATION = 'wasbs://<blob_container_name>@<azure_storage_account_name>.blob.core.windows.net',
    CREDENTIAL = AzureStorageCredential
);
```

### <a name="12-create-the-external-data-source"></a>1.2. Dış veri kaynağını oluşturma
Verilerin konumunu ve veri türünü depolamak için bu [dış VERI kaynağı oluştur][CREATE EXTERNAL DATA SOURCE] komutunu kullanın. 

```sql
CREATE EXTERNAL DATA SOURCE AzureStorage_west_public
WITH 
(  
    TYPE = Hadoop 
,   LOCATION = 'wasbs://contosoretaildw-tables@contosoretaildw.blob.core.windows.net/'
); 
```

> [!IMPORTANT]
> Azure Blob depolama Kapsayıcılarınızı genel hale getirmeyi seçerseniz, veriler veri merkezinden ayrıldığında veri çıkışı ücretlerine göre ücretlendirilecektir. 
> 
> 

## <a name="2-configure-data-format"></a>2. veri biçimini Yapılandır
Veriler Azure Blob depolama alanındaki metin dosyalarında depolanır ve her alan bir sınırlayıcı ile ayrılır. SSMS 'de, metin dosyalarındaki verilerin biçimini belirtmek için aşağıdaki [dış dosya biçimi oluştur][CREATE EXTERNAL FILE FORMAT] komutunu çalıştırın. Contoso verileri sıkıştırılmamış ve kanal sınırlı.

```sql
CREATE EXTERNAL FILE FORMAT TextFileFormat 
WITH 
(   FORMAT_TYPE = DELIMITEDTEXT
,    FORMAT_OPTIONS    (   FIELD_TERMINATOR = '|'
                    ,    STRING_DELIMITER = ''
                    ,    DATE_FORMAT         = 'yyyy-MM-dd HH:mm:ss.fff'
                    ,    USE_TYPE_DEFAULT = FALSE 
                    )
);
``` 

## <a name="3-create-the-external-tables"></a>3. dış tabloları oluşturma
Artık veri kaynağını ve dosya biçimini belirtmişseniz, dış tabloları oluşturmaya hazırsınız demektir. 

### <a name="31-create-a-schema-for-the-data"></a>3,1. Veriler için bir şema oluşturun.
Contoso verilerini veritabanınıza depolayabileceği bir yer oluşturmak için bir şema oluşturun.

```sql
CREATE SCHEMA [asb]
GO
```

### <a name="32-create-the-external-tables"></a>3,2. Harici tabloları oluşturun.
DimProduct ve FactOnlineSales dış tablolarını oluşturmak için aşağıdaki betiği çalıştırın. Burada yaptığınız şey sütun adlarını ve veri türlerini tanımlamakta ve bunları Azure Blob depolama dosyalarının konumuna ve biçimine bağlamaklardır. Tanım SQL Analytics veri ambarında depolanır ve veriler hala Azure Depolama Blobu.

**Konum** parametresi, Azure Depolama Blobu kök klasörün altındaki klasördür. Her tablo farklı bir klasördür.

```sql
--DimProduct
CREATE EXTERNAL TABLE [asb].DimProduct (
    [ProductKey] [int] NOT NULL,
    [ProductLabel] [nvarchar](255) NULL,
    [ProductName] [nvarchar](500) NULL,
    [ProductDescription] [nvarchar](400) NULL,
    [ProductSubcategoryKey] [int] NULL,
    [Manufacturer] [nvarchar](50) NULL,
    [BrandName] [nvarchar](50) NULL,
    [ClassID] [nvarchar](10) NULL,
    [ClassName] [nvarchar](20) NULL,
    [StyleID] [nvarchar](10) NULL,
    [StyleName] [nvarchar](20) NULL,
    [ColorID] [nvarchar](10) NULL,
    [ColorName] [nvarchar](20) NOT NULL,
    [Size] [nvarchar](50) NULL,
    [SizeRange] [nvarchar](50) NULL,
    [SizeUnitMeasureID] [nvarchar](20) NULL,
    [Weight] [float] NULL,
    [WeightUnitMeasureID] [nvarchar](20) NULL,
    [UnitOfMeasureID] [nvarchar](10) NULL,
    [UnitOfMeasureName] [nvarchar](40) NULL,
    [StockTypeID] [nvarchar](10) NULL,
    [StockTypeName] [nvarchar](40) NULL,
    [UnitCost] [money] NULL,
    [UnitPrice] [money] NULL,
    [AvailableForSaleDate] [datetime] NULL,
    [StopSaleDate] [datetime] NULL,
    [Status] [nvarchar](7) NULL,
    [ImageURL] [nvarchar](150) NULL,
    [ProductURL] [nvarchar](150) NULL,
    [ETLLoadID] [int] NULL,
    [LoadDate] [datetime] NULL,
    [UpdateDate] [datetime] NULL
)
WITH
(
    LOCATION='/DimProduct/' 
,   DATA_SOURCE = AzureStorage_west_public
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;

--FactOnlineSales
CREATE EXTERNAL TABLE [asb].FactOnlineSales 
(
    [OnlineSalesKey] [int]  NOT NULL,
    [DateKey] [datetime] NOT NULL,
    [StoreKey] [int] NOT NULL,
    [ProductKey] [int] NOT NULL,
    [PromotionKey] [int] NOT NULL,
    [CurrencyKey] [int] NOT NULL,
    [CustomerKey] [int] NOT NULL,
    [SalesOrderNumber] [nvarchar](20) NOT NULL,
    [SalesOrderLineNumber] [int] NULL,
    [SalesQuantity] [int] NOT NULL,
    [SalesAmount] [money] NOT NULL,
    [ReturnQuantity] [int] NOT NULL,
    [ReturnAmount] [money] NULL,
    [DiscountQuantity] [int] NULL,
    [DiscountAmount] [money] NULL,
    [TotalCost] [money] NOT NULL,
    [UnitCost] [money] NULL,
    [UnitPrice] [money] NULL,
    [ETLLoadID] [int] NULL,
    [LoadDate] [datetime] NULL,
    [UpdateDate] [datetime] NULL
)
WITH
(
    LOCATION='/FactOnlineSales/' 
,   DATA_SOURCE = AzureStorage_west_public
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;
```

## <a name="4-load-the-data"></a>4. verileri yükleme
Dış verilere erişmenin farklı yolları vardır.  Doğrudan dış tablolardaki verileri sorgulayabilir, verileri veri ambarındaki yeni tablolara yükleyebilir veya mevcut veri ambarı tablolarına dış veri ekleyebilirsiniz.  

### <a name="41-create-a-new-schema"></a>4,1. Yeni bir şema oluşturun
CTAS, verileri içeren yeni bir tablo oluşturur.  İlk olarak, contoso verileri için bir şema oluşturun.

```sql
CREATE SCHEMA [cso]
GO
```

### <a name="42-load-the-data-into-new-tables"></a>4,2. Verileri yeni tablolara yükleme
Verileri Azure Blob depolamadan veri ambarı tablosuna yüklemek için [Create Table as Select (Transact-SQL)][CREATE TABLE AS SELECT (Transact-SQL)] ifadesini kullanın. CTAS ile yükleme, oluşturduğunuz kesin türü belirtilmiş dış tablolardan yararlanır. Verileri yeni tablolara yüklemek için tablo başına bir [CTAS][CTAS] bildirisi kullanın. 
 
CTAS yeni bir tablo oluşturur ve bunu bir SELECT ifadesinin sonuçlarıyla doldurur. CTAS, yeni tabloyu SELECT ifadesinin sonuçlarıyla aynı sütunlara ve veri türlerine sahip olacak şekilde tanımlar. Dış tablodaki tüm sütunları seçerseniz, yeni tablo dış tablodaki sütunların ve veri türlerinin bir kopyası olur.

Bu örnekte, hem boyut hem de olgu tablosunu karma dağıtılmış tablolar olarak oluşturacağız. 

```sql
SELECT GETDATE();
GO

CREATE TABLE [cso].[DimProduct]            WITH (DISTRIBUTION = HASH([ProductKey]  ) ) AS SELECT * FROM [asb].[DimProduct]             OPTION (LABEL = 'CTAS : Load [cso].[DimProduct]             ');
CREATE TABLE [cso].[FactOnlineSales]       WITH (DISTRIBUTION = HASH([ProductKey]  ) ) AS SELECT * FROM [asb].[FactOnlineSales]        OPTION (LABEL = 'CTAS : Load [cso].[FactOnlineSales]        ');
```

### <a name="43-track-the-load-progress"></a>4,3 yükleme ilerlemesini izleme
Dinamik yönetim görünümlerini (DMVs) kullanarak yüklerinizin ilerlemesini izleyebilirsiniz. 

```sql
-- To see all requests
SELECT * FROM sys.dm_pdw_exec_requests;

-- To see a particular request identified by its label
SELECT * FROM sys.dm_pdw_exec_requests as r
WHERE r.[label] = 'CTAS : Load [cso].[DimProduct]             '
      OR r.[label] = 'CTAS : Load [cso].[FactOnlineSales]        '
;

-- To track bytes and files
SELECT
    r.command,
    s.request_id,
    r.status,
    count(distinct input_name) as nbr_files, 
    sum(s.bytes_processed)/1024/1024/1024 as gb_processed
FROM
    sys.dm_pdw_exec_requests r
    inner join sys.dm_pdw_dms_external_work s
        on r.request_id = s.request_id
WHERE 
    r.[label] = 'CTAS : Load [cso].[DimProduct]             '
    OR r.[label] = 'CTAS : Load [cso].[FactOnlineSales]        '
GROUP BY
    r.command,
    s.request_id,
    r.status
ORDER BY
    nbr_files desc,
    gb_processed desc;
```

## <a name="5-optimize-columnstore-compression"></a>5. columnstore sıkıştırmasını iyileştirme
Varsayılan olarak, SQL Analytics veri ambarı tabloyu kümelenmiş bir columnstore dizini olarak depolar. Yükleme tamamlandıktan sonra, bazı veri satırları columnstore ' de sıkıştırılmayabilir.  Bunun gerçekleşebileceği farklı nedenler vardır. Daha fazla bilgi için bkz. [columnstore dizinlerini yönetme][manage columnstore indexes].

Bir yüklemeden sonra sorgu performansını ve columnstore sıkıştırmasını iyileştirmek için, columnstore dizinini tüm satırları sıkıştırmak üzere zorlamak için tabloyu yeniden derleyin. 

```sql
SELECT GETDATE();
GO

ALTER INDEX ALL ON [cso].[DimProduct]               REBUILD;
ALTER INDEX ALL ON [cso].[FactOnlineSales]          REBUILD;
```

Columnstore dizinlerini koruma hakkında daha fazla bilgi için bkz. [columnstore dizinlerini yönetme][manage columnstore indexes] makalesi.

## <a name="6-optimize-statistics"></a>6. istatistikleri iyileştirme
Bir yükden hemen sonra tek sütunlu istatistikler oluşturmak en iyisidir. Bazı sütunların sorgu koşullarına sahip olmadığını biliyorsanız, bu sütunlarda istatistik oluşturmayı atlayabilirsiniz. Her sütunda tek sütunlu istatistikler oluşturursanız, tüm istatistiklerin yeniden oluşturulması uzun zaman alabilir. 

Her tablonun her sütununda tek sütunlu istatistikler oluşturmaya karar verirseniz, [istatistik][statistics] makalesindeki `prc_sqldw_create_stats` saklı yordam kodu örneğini kullanabilirsiniz.

Aşağıdaki örnek, istatistik oluşturmak için iyi bir başlangıç noktasıdır. Boyut tablosundaki her bir sütunda ve olgu tablolarındaki her bir birleştirme sütununda tek sütunlu istatistikler oluşturur. Daha sonra, daha sonra diğer olgu tablosu sütunlarına tek veya çok sütunlu istatistikler ekleyebilirsiniz.

```sql
CREATE STATISTICS [stat_cso_DimProduct_AvailableForSaleDate] ON [cso].[DimProduct]([AvailableForSaleDate]);
CREATE STATISTICS [stat_cso_DimProduct_BrandName] ON [cso].[DimProduct]([BrandName]);
CREATE STATISTICS [stat_cso_DimProduct_ClassID] ON [cso].[DimProduct]([ClassID]);
CREATE STATISTICS [stat_cso_DimProduct_ClassName] ON [cso].[DimProduct]([ClassName]);
CREATE STATISTICS [stat_cso_DimProduct_ColorID] ON [cso].[DimProduct]([ColorID]);
CREATE STATISTICS [stat_cso_DimProduct_ColorName] ON [cso].[DimProduct]([ColorName]);
CREATE STATISTICS [stat_cso_DimProduct_ETLLoadID] ON [cso].[DimProduct]([ETLLoadID]);
CREATE STATISTICS [stat_cso_DimProduct_ImageURL] ON [cso].[DimProduct]([ImageURL]);
CREATE STATISTICS [stat_cso_DimProduct_LoadDate] ON [cso].[DimProduct]([LoadDate]);
CREATE STATISTICS [stat_cso_DimProduct_Manufacturer] ON [cso].[DimProduct]([Manufacturer]);
CREATE STATISTICS [stat_cso_DimProduct_ProductDescription] ON [cso].[DimProduct]([ProductDescription]);
CREATE STATISTICS [stat_cso_DimProduct_ProductKey] ON [cso].[DimProduct]([ProductKey]);
CREATE STATISTICS [stat_cso_DimProduct_ProductLabel] ON [cso].[DimProduct]([ProductLabel]);
CREATE STATISTICS [stat_cso_DimProduct_ProductName] ON [cso].[DimProduct]([ProductName]);
CREATE STATISTICS [stat_cso_DimProduct_ProductSubcategoryKey] ON [cso].[DimProduct]([ProductSubcategoryKey]);
CREATE STATISTICS [stat_cso_DimProduct_ProductURL] ON [cso].[DimProduct]([ProductURL]);
CREATE STATISTICS [stat_cso_DimProduct_Size] ON [cso].[DimProduct]([Size]);
CREATE STATISTICS [stat_cso_DimProduct_SizeRange] ON [cso].[DimProduct]([SizeRange]);
CREATE STATISTICS [stat_cso_DimProduct_SizeUnitMeasureID] ON [cso].[DimProduct]([SizeUnitMeasureID]);
CREATE STATISTICS [stat_cso_DimProduct_Status] ON [cso].[DimProduct]([Status]);
CREATE STATISTICS [stat_cso_DimProduct_StockTypeID] ON [cso].[DimProduct]([StockTypeID]);
CREATE STATISTICS [stat_cso_DimProduct_StockTypeName] ON [cso].[DimProduct]([StockTypeName]);
CREATE STATISTICS [stat_cso_DimProduct_StopSaleDate] ON [cso].[DimProduct]([StopSaleDate]);
CREATE STATISTICS [stat_cso_DimProduct_StyleID] ON [cso].[DimProduct]([StyleID]);
CREATE STATISTICS [stat_cso_DimProduct_StyleName] ON [cso].[DimProduct]([StyleName]);
CREATE STATISTICS [stat_cso_DimProduct_UnitCost] ON [cso].[DimProduct]([UnitCost]);
CREATE STATISTICS [stat_cso_DimProduct_UnitOfMeasureID] ON [cso].[DimProduct]([UnitOfMeasureID]);
CREATE STATISTICS [stat_cso_DimProduct_UnitOfMeasureName] ON [cso].[DimProduct]([UnitOfMeasureName]);
CREATE STATISTICS [stat_cso_DimProduct_UnitPrice] ON [cso].[DimProduct]([UnitPrice]);
CREATE STATISTICS [stat_cso_DimProduct_UpdateDate] ON [cso].[DimProduct]([UpdateDate]);
CREATE STATISTICS [stat_cso_DimProduct_Weight] ON [cso].[DimProduct]([Weight]);
CREATE STATISTICS [stat_cso_DimProduct_WeightUnitMeasureID] ON [cso].[DimProduct]([WeightUnitMeasureID]);
CREATE STATISTICS [stat_cso_FactOnlineSales_CurrencyKey] ON [cso].[FactOnlineSales]([CurrencyKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_CustomerKey] ON [cso].[FactOnlineSales]([CustomerKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_DateKey] ON [cso].[FactOnlineSales]([DateKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_OnlineSalesKey] ON [cso].[FactOnlineSales]([OnlineSalesKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_ProductKey] ON [cso].[FactOnlineSales]([ProductKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_PromotionKey] ON [cso].[FactOnlineSales]([PromotionKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_StoreKey] ON [cso].[FactOnlineSales]([StoreKey]);
```

## <a name="achievement-unlocked"></a>Başarı kilidi açıldı!
Ortak verileri bir SQL Analytics veri ambarına başarıyla yüklesahipsiniz. Harika iş!

Artık verilerinizi araştırmak için tabloları sorgulamaya başlayabilirsiniz. Her marka için toplam satışı öğrenmek için aşağıdaki sorguyu çalıştırın:

```sql
SELECT  SUM(f.[SalesAmount]) AS [sales_by_brand_amount]
,       p.[BrandName]
FROM    [cso].[FactOnlineSales] AS f
JOIN    [cso].[DimProduct]      AS p ON f.[ProductKey] = p.[ProductKey]
GROUP BY p.[BrandName]
```

## <a name="next-steps"></a>Sonraki adımlar
Tam veri kümesini yüklemek için, örnek Microsoft SQL Server örnekleri deposundan [tam contoso perakende veri ambarını yükle](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/contoso-data-warehouse/readme.md) ' yi çalıştırın.

Daha fazla geliştirme ipucu için bkz. [SQL veri ambarı geliştirmeye genel bakış] [SQL veri ambarı geliştirmeye genel bakış].

<!--Image references-->

<!--Article references-->
[Create a SQL Analytics data warehouse]: sql-data-warehouse-get-started-provision.md
[Load data into SQL Analytics data warehouse]: sql-data-warehouse-overview-load.md
[SQL Analytics data warehouse development overview]: sql-data-warehouse-overview-develop.md
[manage columnstore indexes]: sql-data-warehouse-tables-index.md
[Statistics]: sql-data-warehouse-tables-statistics.md
[CTAS]: sql-data-warehouse-develop-ctas.md
[label]: sql-data-warehouse-develop-label.md

<!--MSDN references-->
[CREATE EXTERNAL DATA SOURCE]: https://msdn.microsoft.com/library/dn935022.aspx
[CREATE EXTERNAL FILE FORMAT]: https://msdn.microsoft.com/library/dn935026.aspx
[CREATE TABLE AS SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/mt204041.aspx
[sys.dm_pdw_exec_requests]: https://msdn.microsoft.com/library/mt203887.aspx
[REBUILD]: https://msdn.microsoft.com/library/ms188388.aspx

<!--Other Web references-->
[Microsoft Download Center]: https://www.microsoft.com/download/details.aspx?id=36433
[Load the full Contoso Retail Data Warehouse]: https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/contoso-data-warehouse/readme.md
