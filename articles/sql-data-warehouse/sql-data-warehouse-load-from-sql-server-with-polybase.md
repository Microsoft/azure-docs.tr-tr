---
title: "SQL Server&quot;dan Azure SQL Veri Ambarı&quot;na (PolyBase) veri yükleme | Microsoft Belgeleri"
description: "SQL Server&quot;dan düz dosyalara veri aktarmak için bcp&quot;yi, Azure blob depolama alanına veri aktarmak için AZCopy&quot;yi ve verileri Azure SQL Data Warehouse&quot;a almak için PolyBase&quot;i kullanın."
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: jhubbard
editor: 
ms.assetid: 860c86e0-90f7-492c-9a84-1bdd3d1735cd
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 10/31/2016
ms.author: cakarst;barbkess
translationtype: Human Translation
ms.sourcegitcommit: c0e2324a2b2e6294df6e502f2e7a0ae36ff94158
ms.openlocfilehash: 4f6feb844774fba00e3c46438f686e61b52d03d3
ms.lasthandoff: 01/30/2017



---
# <a name="load-data-with-polybase-in-sql-data-warehouse"></a>SQL Data Warehouse'da PolyBase ile veri yükleme
> [!div class="op_single_selector"]
> * [SSIS](sql-data-warehouse-load-from-sql-server-with-integration-services.md)
> * [PolyBase](sql-data-warehouse-load-from-sql-server-with-polybase.md)
> * [bcp](sql-data-warehouse-load-from-sql-server-with-bcp.md)
> 
> 

Bu öğreticide, AzCopy ve PolyBase kullanarak SQL Data Warehouse'a nasıl veri yükleyeceğiniz gösterilmektedir. Öğreticiyi tamamladığınızda şunları öğrenmiş olacaksınız:

* AzCopy kullanarak Azure blob depolama alanına veri kopyalama
* Verileri tanımlamak için veritabanı nesneleri oluşturma
* T-SQL sorgusu çalıştırarak veri yükleme

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Loading-data-with-PolyBase-in-Azure-SQL-Data-Warehouse/player]
> 
> 

## <a name="prerequisites"></a>Ön koşullar
Bu öğreticide ilerleyebilmeniz için, şunlar gereklidir:

* SQL Data Warehouse veritabanı.
* Standart Yerel Olarak Yedekli Depolama (Standard-LRS), Standart Coğrafi Olarak Yedekli Depolama (Standard-GRS), veya Standart Okuma Erişimli Coğrafi Olarak Yedekli Depolama (Standard-RAGRS) türünde bir Azure depolama hesabı.
* AzCopy Komut Satırı Yardımcı Programı Microsoft Azure Depolama Araçları ile birlikte yüklenen [en güncel AzCopy sürümünü][latest version of AzCopy] indirip yükleyin.
  
    ![Azure Storage Araçları](./media/sql-data-warehouse-get-started-load-with-polybase/install-azcopy.png)

## <a name="step-1-add-sample-data-to-azure-blob-storage"></a>1. Adım: Azure blob depolama alanına örnek veri ekleme
Veri yüklemek için Azure blob depolama alanına birkaç örnek veri eklememiz gerekir. Bu adımda bir Azure Storage blobunu örnek verilerle dolduracağız. Daha sonra PolyBase kullanarak bu örnek verileri SQL Data Warehouse veritabanınıza yükleyeceğiz.

### <a name="a-prepare-a-sample-text-file"></a>A. Örnek metin dosyası hazırlama
Örnek metin dosyası hazırlamak için şunları yapın:

1. Not Defteri'ni açın ve aşağıdaki veri satırlarını yeni bir dosyaya kopyalayın. Bu dosyayı yerel geçici dizininize %temp%\DimDate2.txt olarak kaydedin.

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

### <a name="b-find-your-blob-service-endpoint"></a>B. Blob hizmeti uç noktanızı bulma
Blob hizmeti uç noktanızı bulmak için şunları yapın:

1. Azure Portal'dan **Gözat** > **Storage Hesapları**'nı seçin.
2. Kullanmak istediğiniz depolama hesabına tıklayın.
3. Depolama hesabı dikey penceresinde Bloblar'a tıklayın.
   
    ![Bloblar'a tıklayın](./media/sql-data-warehouse-get-started-load-with-polybase/click-blobs.png)
4. Blob hizmeti uç nokta URL'nizi daha sonra kullanmak üzere kaydedin.
   
    ![Blob hizmeti uç noktası](./media/sql-data-warehouse-get-started-load-with-polybase/blob-service.png)

### <a name="c-find-your-azure-storage-key"></a>C. Azure depolama anahtarınızı bulma
Azure depolama anahtarınızı bulmak için şunları yapın:

1. Azure Portal'dan, **Gözat** > **Storage Hesapları**'nı seçin.
2. Kullanmak istediğiniz depolama hesabına tıklayın.
3. **Tüm ayarlar** > **Erişim anahtarları** öğesini seçin.
4. Erişim anahtarlarınızdan birini panoya kopyalamak için kopyalama kutusuna tıklayın.
   
    ![Azure depolama anahtarını kopyalama](./media/sql-data-warehouse-get-started-load-with-polybase/access-key.png)

### <a name="d-copy-the-sample-file-to-azure-blob-storage"></a>D. Örnek dosyayı Azure blob depolama alanına kopyalama
Verilerinizi Azure blob depolama alanına kopyalamak için şunları yapın:

1. Bir komut istemi açın ve dizinleri AzCopy yükleme dizini olarak değiştirin. Bu komut, 64 bit Windows istemcisinde varsayılan yükleme dizinine değiştirme işlemini gerçekleştirir.
   
    ```
    cd /d "%ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy"
    ```
2. Dosyayı karşıya yüklemek için aşağıdaki şu komutu çalıştırın: <blob service endpoint URL> için blob hizmeti uç nokta URL'nizi ve <azure_storage_account_key> için Azure depolama hesabı anahtarınızı belirtin.
   
    ```
    .\AzCopy.exe /Source:C:\Temp\ /Dest:<blob service endpoint URL> /datacontainer/datedimension/ /DestKey:<azure_storage_account_key> /Pattern:DimDate2.txt
    ```

Ayrıca bkz. [AzCopy Komut Satırı Yardımcı Programı ile Çalışmaya Başlama][latest version of AzCopy].

### <a name="e-explore-your-blob-storage-container"></a>E. Blob depolama kapsayıcınızı araştırma
Blob depolama alanına yüklediğiniz dosyayı görmek için şunları yapın:

1. Blob hizmeti dikey pencerenize geri dönün.
2. Kapsayıcılar'ın altında bulunan **datacontainer** öğesine çift tıklayın.
3. Verilerinizin yolunu keşfetmek için **datedimension** klasörüne tıkladığınızda karşıya yüklediğiniz **DimDate2.txt** dosyasıyla karşılaşırsınız.
4. Özellikleri görüntülemek için **DimDate2.txt** dosyasına tıklayın.
5. Blob özellikleri dikey penceresinde dosya indirme ve dosya silme işlemlerini gerçekleştirebileceğinizi unutmayın.
   
    ![Azure depolama blobunu görüntüleme](./media/sql-data-warehouse-get-started-load-with-polybase/view-blob.png)

## <a name="step-2-create-an-external-table-for-the-sample-data"></a>2. Adım: Örnek veriler için dış tablo oluşturma
Bu bölümde örnek verileri tanımlayan bir dış tablo oluşturuyoruz.

PolyBase, Azure blob depolama alanındaki verilere erişmek için dış tabloları kullanır. Veriler SQL Data Warehouse'da depolanmadığı için PolyBase, dış veriler için kimlik doğrulaması gerçekleştirirken veritabanı kapsamlı bir kimlik bilgisi kullanır.

Bu adımdaki örnekte, bir dış tablo oluşturmak için aşağıdaki Transact-SQL deyimleri kullanılmaktadır.

* [Create Master Key (Transact-SQL)][Create Master Key (Transact-SQL)]: Veritabanı kapsamlı kimlik bilgileri anahtarınızı şifrelemek için kullanılır.
* [Create Database Scoped Credential (Transact-SQL)][Create Database Scoped Credential (Transact-SQL)]: Azure depolama hesabınız için kimlik doğrulama bilgilerinin belirtilmesi için kullanılır.
* [Create External Data Source (Transact-SQL)][Create External Data Source (Transact-SQL)]: Azure blob depolama alanınızın konumunu belirtmek için kullanılır.
* [Create External File Format (Transact-SQL)][Create External File Format (Transact-SQL)]: Verilerinizin biçimini belirtmek için kullanılır.
* [Create External Table (Transact-SQL)][Create External Table (Transact-SQL)]: Tablo tanımını ve verilerin konumunu belirtmek için kullanılır.

Bu sorguyu SQL Data Warehouse veritabanınızda çalıştırın. Bu sorgu, Azure blob depolama alanındaki DimDate2.txt örnek verilerini gösteren dbo şemasında DimDate2External adlı bir dış tablo oluşturacak.

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


-- D: Create an external file format
-- FORMAT_TYPE: Type of file format in Azure storage (supported: DELIMITEDTEXT, RCFILE, ORC, PARQUET).
-- FORMAT_OPTIONS: Specify field terminator, string delimiter, date format etc. for delimited text files.
-- Specify DATA_COMPRESSION method if data is compressed.

CREATE EXTERNAL FILE FORMAT TextFile
WITH (
    FORMAT_TYPE = DelimitedText,
    FORMAT_OPTIONS (FIELD_TERMINATOR = ',')
);


-- E: Create the external table
-- Specify column names and data types. This needs to match the data in the sample file.
-- LOCATION: Specify path to file or directory that contains the data (relative to the blob container).
-- To point to all files under the blob container, use LOCATION='.'

CREATE EXTERNAL TABLE dbo.DimDate2External (
    DateId INT NOT NULL,
    CalendarQuarter TINYINT NOT NULL,
    FiscalQuarter TINYINT NOT NULL
)
WITH (
    LOCATION='/datedimension/',
    DATA_SOURCE=AzureStorage,
    FILE_FORMAT=TextFile
);


-- Run a query on the external table

SELECT count(*) FROM dbo.DimDate2External;

```


Visual Studio'da bulunan SQL Server Nesne Gezgini'nde dış dosya biçimini, dış veri kaynağını ve DimDate2External tablosunu görebilirsiniz.

![Dış tabloyu görüntüleme](./media/sql-data-warehouse-get-started-load-with-polybase/external-table.png)

## <a name="step-3-load-data-into-sql-data-warehouse"></a>3. Adım: SQL Data Warehouse'a veri yükleme
Dış tablo oluşturulduktan sonra verileri yeni bir tabloya yükleyebilir veya var olan bir tabloya ekleyebilirsiniz.

* Verileri yeni bir tabloya yüklemek için [CREATE TABLE AS SELECT (Transact-SQL)][CREATE TABLE AS SELECT (Transact-SQL)] deyimini çalıştırın. Yeni tablo, sorguda adlandırılan sütunları içerir. Sütunların veri türleri, dış tablo tanımındaki veri türleriyle eşleşir.
* Verileri, var olan bir tabloya yüklemek için [INSERT...SELECT (Transact-SQL)][INSERT...SELECT (Transact-SQL)] deyimini kullanın.

```sql
-- Load the data from Azure blob storage to SQL Data Warehouse

CREATE TABLE dbo.DimDate2
WITH
(   
    CLUSTERED COLUMNSTORE INDEX,
    DISTRIBUTION = ROUND_ROBIN
)
AS
SELECT * FROM [dbo].[DimDate2External];
```

## <a name="step-4-create-statistics-on-your-newly-loaded-data"></a>4. Adım: Yeni yüklenmiş verilerinize ilişkin istatistikler oluşturma
SQL Data Warehouse, istatistikleri otomatik olarak oluşturup güncelleştirmez. Bu nedenle yüksek sorgu performansı elde etmek için ilk yükleme işleminden sonra her tablonun her sütunu için istatistik oluşturulması önemlidir. Ayrıca veriler üzerinde önemli değişiklikler yapıldıktan sonra istatistiklerin güncelleştirilmesi de önemlidir.

Bu örnekte, yeni DimDate2 tablosuna ilişkin tek sütunlu istatistikler oluşturulmuştur.

```sql
CREATE STATISTICS [DateId] on [DimDate2] ([DateId]);
CREATE STATISTICS [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
CREATE STATISTICS [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
```

Daha fazla bilgi edinmek için bkz. [İstatistikler][Statistics].  

## <a name="next-steps"></a>Sonraki adımlar
PolyBase kullanan bir çözüm geliştirirken bilmeniz gereken daha fazla bilgi için bkz. [PolyBase kılavuzu][PolyBase guide].

<!--Image references-->


<!--Article references-->
[PolyBase in SQL Data Warehouse Tutorial]: ./sql-data-warehouse-get-started-load-with-polybase.md
[Load data with bcp]: ./sql-data-warehouse-load-with-bcp.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[PolyBase guide]: ./sql-data-warehouse-load-polybase-guide.md
[latest version of AzCopy]: ../storage/storage-use-azcopy.md

<!--External references-->
[supported source/sink]: https://msdn.microsoft.com/library/dn894007.aspx
[copy activity]: https://msdn.microsoft.com/library/dn835035.aspx
[SQL Server destination adapter]: https://msdn.microsoft.com/library/ms141095.aspx
[SSIS]: https://msdn.microsoft.com/library/ms141026.aspx


[CREATE EXTERNAL DATA SOURCE (Transact-SQL)]:https://msdn.microsoft.com/library/dn935022.aspx
[CREATE EXTERNAL FILE FORMAT (Transact-SQL)]:https://msdn.microsoft.com/library/dn935026.aspx
[CREATE EXTERNAL TABLE (Transact-SQL)]:https://msdn.microsoft.com/library/dn935021.aspx

[DROP EXTERNAL DATA SOURCE (Transact-SQL)]:https://msdn.microsoft.com/library/mt146367.aspx
[DROP EXTERNAL FILE FORMAT (Transact-SQL)]:https://msdn.microsoft.com/library/mt146379.aspx
[DROP EXTERNAL TABLE (Transact-SQL)]:https://msdn.microsoft.com/library/mt130698.aspx

[CREATE TABLE AS SELECT (Transact-SQL)]:https://msdn.microsoft.com/library/mt204041.aspx
[INSERT...SELECT (Transact-SQL)]:https://msdn.microsoft.com/library/ms174335.aspx
[CREATE MASTER KEY (Transact-SQL)]:https://msdn.microsoft.com/library/ms174382.aspx
[CREATE CREDENTIAL (Transact-SQL)]:https://msdn.microsoft.com/library/ms189522.aspx
[CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)]:https://msdn.microsoft.com/library/mt270260.aspx
[DROP CREDENTIAL (Transact-SQL)]:https://msdn.microsoft.com/library/ms189450.aspx

