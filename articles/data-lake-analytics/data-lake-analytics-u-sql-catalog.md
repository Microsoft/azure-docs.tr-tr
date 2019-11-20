---
title: Azure Data Lake Analytics 'de U-SQL kataloğunu kullanma
description: U-SQL kataloğunu kullanarak kod ve veri paylaşma hakkında bilgi edinin. Tablo değerli işlevler oluşturun, görünümler oluşturun, tablo oluşturun ve bunları sorgulayın.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 57143396-ab86-47dd-b6f8-613ba28c28d2
ms.topic: conceptual
ms.date: 05/09/2017
ms.openlocfilehash: f3b9f14be4422373fb30f8c3d4909fd9c9546fdf
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/29/2019
ms.locfileid: "71672837"
---
# <a name="get-started-with-the-u-sql-catalog-in-azure-data-lake-analytics"></a>Azure Data Lake Analytics ' de U-SQL kataloğu ile çalışmaya başlama

## <a name="create-a-tvf"></a>TVF oluşturma

Önceki U-SQL komut dosyasında, aynı kaynak dosyadan okumak için ayıklama kullanımını tekrarlanmış olursunuz. U-SQL tablo değerli işlevi (TVF) sayesinde, daha sonra yeniden kullanmak üzere verileri kapsülleyebilirsiniz.  

Aşağıdaki komut, varsayılan veritabanında ve şemada çağrılan `Searchlog()` bir TVF oluşturur:

```
DROP FUNCTION IF EXISTS Searchlog;

CREATE FUNCTION Searchlog()
RETURNS @searchlog TABLE
(
            UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
)
AS BEGIN
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM "/Samples/Data/SearchLog.tsv"
USING Extractors.Tsv();
RETURN;
END;
```

Aşağıdaki betik, önceki betikte tanımlanan TVF 'yi nasıl kullanacağınızı gösterir:

```
@res =
    SELECT
        Region,
        SUM(Duration) AS TotalDuration
    FROM Searchlog() AS S
GROUP BY Region
HAVING SUM(Duration) > 200;

OUTPUT @res
    TO "/output/SearchLog-use-tvf.csv"
    ORDER BY TotalDuration DESC
    USING Outputters.Csv();
```

## <a name="create-views"></a>Görünüm oluşturma

Bir TVF yerine tek bir sorgu ifadeniz varsa, bu ifadeyi kapsüllemek için bir U-SQL görünümü kullanabilirsiniz.

Aşağıdaki komut, varsayılan veritabanında ve şemada `SearchlogView` adlı bir görünüm oluşturur:

```
DROP VIEW IF EXISTS SearchlogView;

CREATE VIEW SearchlogView AS  
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM "/Samples/Data/SearchLog.tsv"
USING Extractors.Tsv();
```

Aşağıdaki betik, tanımlanan görünümün kullanımını gösterir:

```
@res =
    SELECT
        Region,
        SUM(Duration) AS TotalDuration
    FROM SearchlogView
GROUP BY Region
HAVING SUM(Duration) > 200;

OUTPUT @res
    TO "/output/Searchlog-use-view.csv"
    ORDER BY TotalDuration DESC
    USING Outputters.Csv();
```

## <a name="create-tables"></a>Tablo oluşturma
İlişkisel veritabanı tablolarında olduğu gibi, U-SQL ile, önceden tanımlanmış bir şema içeren bir tablo oluşturabilir veya tabloyu dolduran sorgudan (SELECT veya CTAS olarak da CREATE TABLE bilinir) şemayı gösteren bir tablo oluşturabilirsiniz.

Aşağıdaki betiği kullanarak bir veritabanı ve iki tablo oluşturun:

```
DROP DATABASE IF EXISTS SearchLogDb;
CREATE DATABASE SearchLogDb;
USE DATABASE SearchLogDb;

DROP TABLE IF EXISTS SearchLog1;
DROP TABLE IF EXISTS SearchLog2;

CREATE TABLE SearchLog1 (
            UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string,

            INDEX sl_idx CLUSTERED (UserId ASC)
                DISTRIBUTED BY HASH (UserId)
);

INSERT INTO SearchLog1 SELECT * FROM master.dbo.Searchlog() AS s;

CREATE TABLE SearchLog2(
    INDEX sl_idx CLUSTERED (UserId ASC)
            DISTRIBUTED BY HASH (UserId)
) AS SELECT * FROM master.dbo.Searchlog() AS S; // You can use EXTRACT or SELECT here
```

## <a name="query-tables"></a>Sorgu tabloları
Önceki betikte oluşturulanlar gibi tabloları, veri dosyalarını Sorgulayabileceğiniz şekilde sorgulayabilirsiniz. EXTRACT kullanarak bir satır kümesi oluşturmak yerine artık tablo adına başvurabilirsiniz.

Tablolardan okumak için, daha önce kullandığınız dönüştürme betiğini değiştirin:

```
@rs1 =
    SELECT
        Region,
        SUM(Duration) AS TotalDuration
    FROM SearchLogDb.dbo.SearchLog2
GROUP BY Region;

@res =
    SELECT *
    FROM @rs1
    ORDER BY TotalDuration DESC
    FETCH 5 ROWS;

OUTPUT @res
    TO "/output/Searchlog-query-table.csv"
    ORDER BY TotalDuration DESC
    USING Outputters.Csv();
```

 >[!NOTE]
 >Şu anda, tabloyu oluşturduğunuz bir betikle aynı komut dosyasında bir SELECT SEÇIMI çalıştıramazsınız.

## <a name="next-steps"></a>Sonraki Adımlar
* [Microsoft Azure Data Lake Analytics'e genel bakış](data-lake-analytics-overview.md)
* [Visual Studio için Data Lake Araçları'nı kullanarak U-SQL betikleri geliştirme](data-lake-analytics-data-lake-tools-get-started.md)
* [Azure portalını kullanarak Azure Data Lake Analytics işlerini izleme ve sorun giderme](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
