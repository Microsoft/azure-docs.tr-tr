---
title: Eğitim Azure Data Lake Storage verileri yükleme
description: Adanmış SQL havuzları için Azure Data Lake Storage verileri yüklemek üzere COPY ifadesini kullanın.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/20/2020
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 16f95a86169be04eba202b311fc4437b204ec8b3
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566537"
---
# <a name="load-data-from-azure-data-lake-storage-into-dedicated-sql-pools-in-azure-synapse-analytics"></a>Azure Data Lake Storage verileri Azure SYNAPSE Analytics 'te adanmış SQL havuzlarına yükleme

Bu kılavuzda, Azure Data Lake Storage verileri yüklemek için [Copy ifadesinin](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true) nasıl kullanılacağı özetlenmektedir. Tüm kimlik doğrulama yöntemlerinde COPY ifadesini kullanmayla ilgili hızlı örnekler için aşağıdaki belgeleri ziyaret edin: [ADANMıŞ SQL havuzlarını kullanarak verileri güvenli bir şekilde yükleyin](./quickstart-bulk-load-copy-tsql-examples.md).

> [!NOTE]  
> COPY ifadesinde geri bildirim veya rapor sorunları sağlamak için şu dağıtım listesine bir e-posta gönderin: sqldwcopypreview@service.microsoft.com .
>
> [!div class="checklist"]
>
> * Azure Data Lake Storage verileri yüklemek için hedef tablo oluşturun.
> * Veri ambarına veri yüklemek için kopyalama ifadesini oluşturun.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="before-you-begin"></a>Başlamadan önce

Bu öğreticiye başlamadan önce, [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)’nun (SSMS) en yeni sürümünü indirin ve yükleyin.

Bu öğreticiyi çalıştırmak için şunlar gerekir:

* Adanmış bir SQL Havuzu. Bkz. [adanmış BIR SQL havuzu oluşturma ve verileri sorgulama](create-data-warehouse-portal.md).
* Data Lake Storage hesabı. Bkz. [Azure Data Lake Storage kullanmaya başlama](../../data-lake-store/data-lake-store-get-started-portal.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Bu depolama hesabında, yüklemek için şu kimlik bilgilerinden birini yapılandırmanız veya belirtmeniz gerekir: depolama hesabı anahtarı, paylaşılan erişim imzası (SAS) anahtarı, bir Azure dizin uygulama kullanıcısı veya depolama hesabına uygun Azure rolüne sahip bir AAD kullanıcısı.

## <a name="create-the-target-table"></a>Hedef tabloyu oluşturma

Adanmış SQL havuzunuza bağlanın ve yüklenecek hedef tabloyu oluşturun. Bu örnekte, bir ürün boyut tablosu oluşturacağız.

```sql
-- A: Create the target table
-- DimProduct
CREATE TABLE [dbo].[DimProduct]
(
    [ProductKey] [int] NOT NULL,
    [ProductLabel] [nvarchar](255) NULL,
    [ProductName] [nvarchar](500) NULL
)
WITH
(
    DISTRIBUTION = HASH([ProductKey]),
    CLUSTERED COLUMNSTORE INDEX
    --HEAP
);
```


## <a name="create-the-copy-statement"></a>COPY ifadesini oluşturma

SQL adanmış havuzunuza bağlanın ve COPY ifadesini çalıştırın. Örneklerin tamamen listesi için aşağıdaki belgeleri ziyaret edin: [ADANMıŞ SQL havuzlarını kullanarak verileri güvenli bir şekilde yükleyin](./quickstart-bulk-load-copy-tsql-examples.md).

```sql
-- B: Create and execute the COPY statement

COPY INTO [dbo].[DimProduct] 
--The column list allows you map, omit, or reorder input file columns to target table columns. 
--You can also specify the default value when there is a NULL value in the file.
--When the column list is not specified, columns will be mapped based on source and target ordinality
(
    ProductKey default -1 1,
    ProductLabel default 'myStringDefaultWhenNull' 2,
    ProductName default 'myStringDefaultWhenNull' 3
)
--The storage account location where you data is staged
FROM 'https://storageaccount.blob.core.windows.net/container/directory/'
WITH 
(
   --CREDENTIAL: Specifies the authentication method and credential access your storage account
   CREDENTIAL = (IDENTITY = '', SECRET = ''),
   --FILE_TYPE: Specifies the file type in your storage account location
   FILE_TYPE = 'CSV',
   --FIELD_TERMINATOR: Marks the end of each field (column) in a delimited text (CSV) file
   FIELDTERMINATOR = '|',
   --ROWTERMINATOR: Marks the end of a record in the file
   ROWTERMINATOR = '0x0A',
   --FIELDQUOTE: Specifies the delimiter for data of type string in a delimited text (CSV) file
   FIELDQUOTE = '',
   ENCODING = 'UTF8',
   DATEFORMAT = 'ymd',
   --MAXERRORS: Maximum number of reject rows allowed in the load before the COPY operation is canceled
   MAXERRORS = 10,
   --ERRORFILE: Specifies the directory where the rejected rows and the corresponding error reason should be written
   ERRORFILE = '/errorsfolder',
) OPTION (LABEL = 'COPY: ADLS tutorial');
```

## <a name="optimize-columnstore-compression"></a>Columnstore sıkıştırmasını iyileştirme

Tablolar, varsayılan olarak kümelenmiş bir columnstore dizini olarak tanımlanır. Yükleme tamamlandıktan sonra, bazı veri satırları columnstore ' de sıkıştırılmayabilir.  Bunun gerçekleşebileceği çeşitli nedenler vardır. Daha fazla bilgi için bkz. [columnstore dizinlerini yönetme](sql-data-warehouse-tables-index.md).

Bir yüklemeden sonra sorgu performansını ve columnstore sıkıştırmasını iyileştirmek için, columnstore dizinini tüm satırları sıkıştırmak üzere zorlamak için tabloyu yeniden derleyin.

```sql

ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD;

```

## <a name="optimize-statistics"></a>İstatistikleri iyileştirme

Bir yükden hemen sonra tek sütunlu istatistikler oluşturmak en iyisidir. İstatistikler için bazı seçimler vardır. Örneğin, her sütunda tek sütunlu istatistikler oluşturursanız tüm istatistiklerin yeniden oluşturulması uzun sürebilir. Bazı sütunların sorgu koşullarına sahip olmadığını biliyorsanız, bu sütunlarda istatistik oluşturmayı atlayabilirsiniz.

Her tablonun her sütununda tek sütunlu istatistikler oluşturmaya karar verirseniz, istatistik makalesindeki saklı yordam kodu örneğini kullanabilirsiniz `prc_sqldw_create_stats` . [](sql-data-warehouse-tables-statistics.md)

Aşağıdaki örnek, istatistik oluşturmak için iyi bir başlangıç noktasıdır. Boyut tablosundaki her bir sütunda ve olgu tablolarındaki her bir birleştirme sütununda tek sütunlu istatistikler oluşturur. Daha sonra, daha sonra diğer olgu tablosu sütunlarına tek veya çok sütunlu istatistikler ekleyebilirsiniz.

## <a name="achievement-unlocked"></a>Başarı kilidi açıldı!

Verileri veri ambarınıza başarıyla yüklesahipsiniz. Harika iş çıkardınız!

## <a name="next-steps"></a>Sonraki adımlar
Verilerin yüklenmesi, Azure SYNAPSE Analytics kullanılarak veri ambarı çözümü geliştirmenin ilk adımıdır. Geliştirme kaynaklarımıza göz atın.

> [!div class="nextstepaction"]
> [Veri depolama için tablo geliştirmeyi öğrenin](sql-data-warehouse-tables-overview.md)

Daha fazla örnek ve başvuru yüklemek için aşağıdaki belgeleri görüntüleyin:
- [Deyimdeki başvuru belgelerini Kopyala](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true#syntax)
- [Her kimlik doğrulama yöntemi için örnekleri KOPYALAYıN](./quickstart-bulk-load-copy-tsql-examples.md)
- [Tek bir tablo için hızlı başlangıç kopyalama](./quickstart-bulk-load-copy-tsql.md)