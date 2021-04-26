---
title: Contoso perakende verilerini adanmış SQL havuzlarına yükleme
description: Contoso perakende verilerinden adanmış SQL havuzlarına iki tablo yüklemek için PolyBase ve T-SQL komutlarını kullanın.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/20/2020
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: b1afcdfa74245eb566663d5dec6ce2e2276fbdc8
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568242"
---
# <a name="load-contoso-retail-data-into-dedicated-sql-pools-in-azure-synapse-analytics"></a>Contoso perakende verilerini Azure SYNAPSE Analytics 'te adanmış SQL havuzlarına yükleme

Bu öğreticide, contoso perakende verilerinden adanmış SQL havuzlarına iki tablo yüklemek için PolyBase ve T-SQL komutlarını kullanmayı öğreneceksiniz.

Bu öğreticide şunları yapmanız gerekir:

1. PolyBase 'i Azure Blob depolamadan yüklenecek şekilde yapılandırma
2. Veritabanınıza ortak verileri yükleme
3. Yükleme tamamlandıktan sonra iyileştirmeleri gerçekleştirin.

## <a name="before-you-begin"></a>Başlamadan önce

Bu öğreticiyi çalıştırmak için, zaten adanmış bir SQL havuzu olan bir Azure hesabınızın olması gerekir. Sağlanmış bir veri ambarınız yoksa, bkz. [veri ambarı oluşturma ve sunucu düzeyinde güvenlik duvarı kuralı ayarlama](create-data-warehouse-portal.md).

## <a name="configure-the-data-source"></a>Veri kaynağını yapılandırma

PolyBase, dış verilerin konumunu ve özniteliklerini tanımlamak için T-SQL dış nesnelerini kullanır. Dış nesne tanımları, adanmış SQL havuzlarında depolanır. Veriler dışarıdan depolanır.

## <a name="create-a-credential"></a>Kimlik bilgisi oluşturma

Contoso ortak verilerini yüklüyorsanız **Bu adımı atlayın** . Herkese zaten erişebildiğinden ortak verilere güvenli erişim gerekmez.

Bu öğreticiyi kendi verilerinizi yüklemek için bir şablon olarak kullanıyorsanız **Bu adımı atlayın** . Bir kimlik bilgisi aracılığıyla verilere erişmek için aşağıdaki betiği kullanarak veritabanı kapsamlı bir kimlik bilgisi oluşturun. Ardından, veri kaynağının konumunu tanımlarken kullanın.

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

## <a name="create-the-external-data-source"></a>Dış veri kaynağını oluşturma

Verilerin konumunu ve veri türünü depolamak için bu [dış VERI kaynağı oluştur](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) komutunu kullanın.

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

## <a name="configure-the-data-format"></a>Veri biçimini Yapılandır

Veriler Azure Blob depolama alanındaki metin dosyalarında depolanır ve her alan bir sınırlayıcı ile ayrılır. SSMS 'de, metin dosyalarındaki verilerin biçimini belirtmek için aşağıdaki dış dosya BIÇIMI Oluştur komutunu çalıştırın. Contoso verileri sıkıştırılmamış ve kanal sınırlı.

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

## <a name="create-the-schema-for-the-external-tables"></a>Dış tablolar için şema oluşturma

Artık veri kaynağı ve dosya biçimini belirtmişseniz, dış tablolar için şema oluşturmaya hazırsınız demektir.

Contoso verilerini veritabanınıza depolayabileceği bir yer oluşturmak için bir şema oluşturun.

```sql
CREATE SCHEMA [asb]
GO
```

## <a name="create-the-external-tables"></a>Dış tabloları oluşturma

DimProduct ve FactOnlineSales dış tablolarını oluşturmak için aşağıdaki betiği çalıştırın. Burada yaptığınız şey sütun adlarını ve veri türlerini tanımlamakta ve bunları Azure Blob depolama dosyalarının konumuna ve biçimine bağlamaklardır. Tanım, veri ambarında depolanır ve veriler hala Azure Depolama Blobu.

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

## <a name="load-the-data"></a>Verileri yükleme

Dış verilere erişmenin farklı yolları vardır.  Doğrudan dış tablolardaki verileri sorgulayabilir, verileri veri ambarındaki yeni tablolara yükleyebilir veya mevcut veri ambarı tablolarına dış veri ekleyebilirsiniz.  

### <a name="create-a-new-schema"></a>Yeni bir şema oluşturun

CTAS, verileri içeren yeni bir tablo oluşturur.  İlk olarak, contoso verileri için bir şema oluşturun.

```sql
CREATE SCHEMA [cso]
GO
```

### <a name="load-the-data-into-new-tables"></a>Verileri yeni tablolara yükleme

Verileri Azure Blob depolamadan veri ambarı tablosuna yüklemek için [Create Table as Select (Transact-SQL)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) ifadesini kullanın. [CTAS](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) ile yükleme, oluşturduğunuz kesin türü belirtilmiş dış tablolardan yararlanır. Verileri yeni tablolara yüklemek için tablo başına bir CTAS bildirisi kullanın.

CTAS yeni bir tablo oluşturur ve bunu bir SELECT ifadesinin sonuçlarıyla doldurur. CTAS, yeni tabloyu SELECT ifadesinin sonuçlarıyla aynı sütunlara ve veri türlerine sahip olacak şekilde tanımlar. Dış tablodaki tüm sütunları seçerseniz, yeni tablo dış tablodaki sütunların ve veri türlerinin bir kopyası olur.

Bu örnekte, hem boyut hem de olgu tablosunu karma dağıtılmış tablolar olarak oluşturacağız.

```sql
SELECT GETDATE();
GO

CREATE TABLE [cso].[DimProduct]            WITH (DISTRIBUTION = HASH([ProductKey]  ) ) AS SELECT * FROM [asb].[DimProduct]             OPTION (LABEL = 'CTAS : Load [cso].[DimProduct]             ');
CREATE TABLE [cso].[FactOnlineSales]       WITH (DISTRIBUTION = HASH([ProductKey]  ) ) AS SELECT * FROM [asb].[FactOnlineSales]        OPTION (LABEL = 'CTAS : Load [cso].[FactOnlineSales]        ');
```

### <a name="track-the-load-progress"></a>Yükleme ilerlemesini izleme

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

## <a name="optimize-columnstore-compression"></a>Columnstore sıkıştırmasını iyileştirme

Varsayılan olarak, adanmış SQL havuzları tabloyu kümelenmiş bir columnstore dizini olarak depolar. Yükleme tamamlandıktan sonra, bazı veri satırları columnstore ' de sıkıştırılmayabilir.  Bunun gerçekleşebileceği farklı nedenler vardır. Daha fazla bilgi için bkz. [columnstore dizinlerini yönetme](sql-data-warehouse-tables-index.md).

Bir yüklemeden sonra sorgu performansını ve columnstore sıkıştırmasını iyileştirmek için, columnstore dizinini tüm satırları sıkıştırmak üzere zorlamak için tabloyu yeniden derleyin.

```sql
SELECT GETDATE();
GO

ALTER INDEX ALL ON [cso].[DimProduct]               REBUILD;
ALTER INDEX ALL ON [cso].[FactOnlineSales]          REBUILD;
```

Columnstore dizinlerini koruma hakkında daha fazla bilgi için bkz. [columnstore dizinlerini yönetme](sql-data-warehouse-tables-index.md) makalesi.

## <a name="optimize-statistics"></a>İstatistikleri iyileştirme

Bir yükden hemen sonra tek sütunlu istatistikler oluşturmak en iyisidir. Bazı sütunların sorgu koşullarına sahip olmadığını biliyorsanız, bu sütunlarda istatistik oluşturmayı atlayabilirsiniz. Her sütunda tek sütunlu istatistikler oluşturursanız, tüm istatistiklerin yeniden oluşturulması uzun zaman alabilir.

Her tablonun her sütununda tek sütunlu istatistikler oluşturmaya karar verirseniz, istatistik makalesindeki saklı yordam kodu örneğini kullanabilirsiniz `prc_sqldw_create_stats` . [](sql-data-warehouse-tables-statistics.md)

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

Genel verileri veri ambarınıza başarıyla yüklesahipsiniz. Harika iş çıkardınız!

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
Daha fazla geliştirme ipucu için bkz. [veri ambarları Için tasarım kararları ve kodlama teknikleri](sql-data-warehouse-overview-develop.md).
