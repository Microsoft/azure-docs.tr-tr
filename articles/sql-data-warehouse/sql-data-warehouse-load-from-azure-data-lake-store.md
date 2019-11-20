---
title: Eğitim Azure Data Lake Storage verileri yükleme
description: Azure Data Lake Storage verileri Azure SQL veri ambarı 'na yüklemek için PolyBase dış tablolarını kullanın.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: load-data
ms.date: 08/08/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 522cb9b75d5c0db270f8ba4a65850e35a2e8c4fd
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685684"
---
# <a name="load-data-from-azure-data-lake-storage-to-sql-data-warehouse"></a>Azure Data Lake Storage verileri SQL veri ambarı 'na yükleme
Azure Data Lake Storage verileri Azure SQL veri ambarı 'na yüklemek için PolyBase dış tablolarını kullanın. Data Lake Storage depolanan verilerde geçici sorgular çalıştırabilmenize karşın, en iyi performans için verileri SQL veri ambarı 'na aktarmayı öneririz.

> [!div class="checklist"]
> * Data Lake Storage yüklemek için gereken veritabanı nesneleri oluşturun.
> * Data Lake Storage dizinine bağlanın.
> * Azure SQL veri ambarı 'na veri yükleme.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

## <a name="before-you-begin"></a>Başlamadan önce
Bu öğreticiye başlamadan önce, [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)’nun (SSMS) en yeni sürümünü indirin ve yükleyin.

Bu öğreticiyi çalıştırmak için şunlar gerekir:

* Hizmetten hizmete kimlik doğrulaması için kullanılacak uygulamayı Azure Active Directory. Oluşturmak için [Active Directory kimlik doğrulamasını](../data-lake-store/data-lake-store-authenticate-using-active-directory.md) izleyin

* Azure SQL veri ambarı. Bkz. [oluşturma ve sorgulama ve Azure SQL veri ambarı](create-data-warehouse-portal.md).

* Data Lake Storage hesabı. Bkz. [Azure Data Lake Storage kullanmaya başlama](../data-lake-store/data-lake-store-get-started-portal.md). 

##  <a name="create-a-credential"></a>Kimlik bilgisi oluşturma
Data Lake Storage hesabınıza erişmek için, bir sonraki adımda kullanılan kimlik bilgisi gizli anahtarını şifrelemek üzere bir veritabanı ana anahtarı oluşturmanız gerekir. Daha sonra bir veritabanı kapsamlı kimlik bilgisi oluşturursunuz. Hizmet sorumlularını kullanarak kimlik doğrulaması yaparken, veritabanı kapsamlı kimlik bilgileri AAD 'de ayarlanan hizmet sorumlusu kimlik bilgilerini depolar. Depolama hesabı anahtarını, Gen2 için veritabanı kapsamlı kimlik bilgilerinde de kullanabilirsiniz. 

Hizmet sorumlularını kullanarak Data Lake Storage bağlanmak için, **önce** bir Azure Active Directory uygulaması oluşturmanız, bir erişim anahtarı oluşturmanız ve uygulamaya Data Lake Storage hesabına erişim vermeniz gerekir. Yönergeler için bkz. [Active Directory kullanarak Azure Data Lake Storage kimlik doğrulama](../data-lake-store/data-lake-store-authenticate-using-active-directory.md).

```sql
-- A: Create a Database Master Key.
-- Only necessary if one does not already exist.
-- Required to encrypt the credential secret in the next step.
-- For more information on Master Key: https://msdn.microsoft.com/library/ms174382.aspx?f=255&MSPPError=-2147217396

CREATE MASTER KEY;


-- B (for service principal authentication): Create a database scoped credential
-- IDENTITY: Pass the client id and OAuth 2.0 Token Endpoint taken from your Azure Active Directory Application
-- SECRET: Provide your AAD Application Service Principal key.
-- For more information on Create Database Scoped Credential: https://msdn.microsoft.com/library/mt270260.aspx

CREATE DATABASE SCOPED CREDENTIAL ADLSCredential
WITH
    -- Always use the OAuth 2.0 authorization endpoint (v1)
    IDENTITY = '<client_id>@<OAuth_2.0_Token_EndPoint>',
    SECRET = '<key>'
;

-- B (for Gen2 storage key authentication): Create a database scoped credential
-- IDENTITY: Provide any string, it is not used for authentication to Azure storage.
-- SECRET: Provide your Azure storage account key.

CREATE DATABASE SCOPED CREDENTIAL ADLSCredential
WITH
    IDENTITY = 'user',
    SECRET = '<azure_storage_account_key>'
;

-- It should look something like this when authenticating using service principals:
CREATE DATABASE SCOPED CREDENTIAL ADLSCredential
WITH
    IDENTITY = '536540b4-4239-45fe-b9a3-629f97591c0c@https://login.microsoftonline.com/42f988bf-85f1-41af-91ab-2d2cd011da47/oauth2/token',
    SECRET = 'BjdIlmtKp4Fpyh9hIvr8HJlUida/seM5kQ3EpLAmeDI='
;
```

## <a name="create-the-external-data-source"></a>Dış veri kaynağını oluşturma
Verilerin konumunu depolamak için bu [dış VERI kaynağı oluştur](/sql/t-sql/statements/create-external-data-source-transact-sql) komutunu kullanın. 

```sql
-- C (for Gen1): Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure Data Lake Storage.
-- LOCATION: Provide Data Lake Storage Gen1 account name and URI
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureDataLakeStorage
WITH (
    TYPE = HADOOP,
    LOCATION = 'adl://<datalakestoregen1accountname>.azuredatalakestore.net',
    CREDENTIAL = ADLSCredential
);

-- C (for Gen2): Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure Data Lake Storage.
-- LOCATION: Provide Data Lake Storage Gen2 account name and URI
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureDataLakeStorage
WITH (
    TYPE = HADOOP,
    LOCATION='abfs[s]://<container>@<AzureDataLake account_name>.dfs.core.windows.net', -- Please note the abfss endpoint for when your account has secure transfer enabled
    CREDENTIAL = ADLSCredential
);
```

## <a name="configure-data-format"></a>Veri biçimini Yapılandır
Data Lake Storage verileri içeri aktarmak için dış dosya biçimini belirtmeniz gerekir. Bu nesne, dosyaların Data Lake Storage nasıl yazıldığını tanımlar.
Tam liste için T-SQL belgelerimize [dış dosya biçimi oluşturma](/sql/t-sql/statements/create-external-file-format-transact-sql) ' ya bakın

```sql
-- D: Create an external file format
-- FIELD_TERMINATOR: Marks the end of each field (column) in a delimited text file
-- STRING_DELIMITER: Specifies the field terminator for data of type string in the text-delimited file.
-- DATE_FORMAT: Specifies a custom format for all date and time data that might appear in a delimited text file.
-- Use_Type_Default: Store missing values as default for datatype.

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

## <a name="create-the-external-tables"></a>Dış tabloları oluşturma
Artık veri kaynağını ve dosya biçimini belirtolduğunuza göre, dış tabloları oluşturmaya hazırsınız demektir. Dış tablolar, dış verilerle etkileşime girirsiniz. Konum parametresi, bir dosya veya dizin belirtebilir. Bir dizin belirtiyorsa, dizin içindeki tüm dosyalar yüklenir.

```sql
-- D: Create an External Table
-- LOCATION: Folder under the Data Lake Storage root folder.
-- DATA_SOURCE: Specifies which Data Source Object to use.
-- FILE_FORMAT: Specifies which File Format Object to use
-- REJECT_TYPE: Specifies how you want to deal with rejected rows. Either Value or percentage of the total
-- REJECT_VALUE: Sets the Reject value based on the reject type.

-- DimProduct
CREATE EXTERNAL TABLE [dbo].[DimProduct_external] (
    [ProductKey] [int] NOT NULL,
    [ProductLabel] [nvarchar](255) NULL,
    [ProductName] [nvarchar](500) NULL
)
WITH
(
    LOCATION='/DimProduct/'
,   DATA_SOURCE = AzureDataLakeStorage
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;

```

## <a name="external-table-considerations"></a>Dış tablo konuları
Dış tablo oluşturma kolaydır, ancak ele almanız gereken bazı bildirimler vardır.

Dış tablolar kesin olarak türdedir. Bu, çağrılan verilerin her satırının tablo şeması tanımını karşılaması gerektiği anlamına gelir.
Bir satır şema tanımıyla eşleşmezse, satır yük tarafından reddedilir.

REJECT_TYPE ve REJECT_VALUE seçenekleri, son tabloda kaç satır veya veri yüzdesinin mevcut olması gerektiğini tanımlamanızı sağlar. Yük sırasında reddetme değerine ulaşıldığında, yükleme başarısız olur. Reddedilen satırların en yaygın nedeni, bir şema tanımı uyuşmazlığıdır. Örneğin, bir sütun, dosyadaki verilerin bir dize olduğu zaman int şemasına yanlış verildiyse, her satır yükleme başarısız olur.

Data Lake Storage 1., verilere erişimi denetlemek için rol tabanlı Access Control (RBAC) kullanır. Bu, hizmet sorumlusunun konum parametresinde tanımlanan dizinlerde ve son dizin ve dosyaların alt öğelerinde okuma izinlerinin olması gerektiği anlamına gelir. Bu, PolyBase 'in kimlik doğrulamasını ve verileri yüklemesini sağlar. 

## <a name="load-the-data"></a>Verileri yükleme
Data Lake Storage verileri yüklemek için [Create Table Select (Transact-SQL)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) ifadesini kullanın. 

CTAS yeni bir tablo oluşturur ve bunu bir SELECT ifadesinin sonuçlarıyla doldurur. CTAS, yeni tabloyu SELECT ifadesinin sonuçlarıyla aynı sütunlara ve veri türlerine sahip olacak şekilde tanımlar. Dış tablodaki tüm sütunları seçerseniz, yeni tablo dış tablodaki sütunların ve veri türlerinin bir çoğaltmasıdır.

Bu örnekte, dış TabloDimProduct_external DimProduct adlı bir karma dağıtılmış tablo oluşturacağız.

```sql

CREATE TABLE [dbo].[DimProduct]
WITH (DISTRIBUTION = HASH([ProductKey]  ) )
AS
SELECT * FROM [dbo].[DimProduct_external]
OPTION (LABEL = 'CTAS : Load [dbo].[DimProduct]');
```


## <a name="optimize-columnstore-compression"></a>Columnstore sıkıştırmasını iyileştirme
Varsayılan olarak, SQL veri ambarı tabloyu kümelenmiş bir columnstore dizini olarak depolar. Yükleme tamamlandıktan sonra, bazı veri satırları columnstore ' de sıkıştırılmayabilir.  Bunun gerçekleşebileceği çeşitli nedenler vardır. Daha fazla bilgi için bkz. [columnstore dizinlerini yönetme](sql-data-warehouse-tables-index.md).

Bir yüklemeden sonra sorgu performansını ve columnstore sıkıştırmasını iyileştirmek için, columnstore dizinini tüm satırları sıkıştırmak üzere zorlamak için tabloyu yeniden derleyin.

```sql

ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD;

```

## <a name="optimize-statistics"></a>İstatistikleri iyileştirme
Bir yükden hemen sonra tek sütunlu istatistikler oluşturmak en iyisidir. İstatistikler için bazı seçimler vardır. Örneğin, her sütunda tek sütunlu istatistikler oluşturursanız tüm istatistiklerin yeniden oluşturulması uzun sürebilir. Bazı sütunların sorgu koşullarına sahip olmadığını biliyorsanız, bu sütunlarda istatistik oluşturmayı atlayabilirsiniz.

Her tablonun her sütununda tek sütunlu istatistikler oluşturmaya karar verirseniz, [istatistik](sql-data-warehouse-tables-statistics.md) makalesindeki `prc_sqldw_create_stats` saklı yordam kodu örneğini kullanabilirsiniz.

Aşağıdaki örnek, istatistik oluşturmak için iyi bir başlangıç noktasıdır. Boyut tablosundaki her bir sütunda ve olgu tablolarındaki her bir birleştirme sütununda tek sütunlu istatistikler oluşturur. Daha sonra, daha sonra diğer olgu tablosu sütunlarına tek veya çok sütunlu istatistikler ekleyebilirsiniz.

## <a name="achievement-unlocked"></a>Başarı kilidi açıldı!
Verileri Azure SQL veri ambarı 'na başarıyla yüklesahipsiniz. Harika iş!

## <a name="next-steps"></a>Sonraki adımlar 
Bu öğreticide, Data Lake Storage 1. depolanan verilerin yapısını tanımlamak için dış tablolar oluşturdunuz ve sonra veri ambarınıza veri yüklemek için PolyBase CREATE TABLE AS SELECT ifadesini kullandınız. 

Şu işlemleri yaptınız:
> [!div class="checklist"]
> * Data Lake Storage 1. yüklemek için gereken veritabanı nesneleri oluşturuldu.
> * Bir Data Lake Storage 1. dizinine bağlanıldı.
> * Azure SQL veri ambarı 'na veri yüklendi.
>

Veri yükleme, SQL veri ambarı kullanılarak veri ambarı çözümü geliştirmenin ilk adımıdır. Geliştirme kaynaklarımıza göz atın.

> [!div class="nextstepaction"]
> [SQL veri ambarı 'nda tablo geliştirmeyi öğrenin](sql-data-warehouse-tables-overview.md)
