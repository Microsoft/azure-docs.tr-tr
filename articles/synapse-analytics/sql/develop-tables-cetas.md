---
title: SYNAPSE SQL 'de SELECT (CETAS) olarak dış tablo oluşturma
description: SYNAPSE SQL ile CREATE EXTERNAL TABLE AS SELECT (CETAS) kullanma
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 09/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 23b74c06ebd45092b9efe36a870eeac7f6fb828a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101677571"
---
# <a name="cetas-with-synapse-sql"></a>SYNAPSE SQL ile CETAS

Aşağıdaki görevleri gerçekleştirmek için adanmış SQL havuzunda veya sunucusuz SQL havuzunda SELECT AS SELECT (CETAS) öğesini kullanabilirsiniz:  

- Dış tablo oluşturma
- Bir Transact-SQL SELECT ifadesinin sonuçlarını paralel olarak dışarı aktarın:

  - Hadoop
  - Azure Depolama Blobu
  - Azure Data Lake Storage Gen2

## <a name="cetas-in-dedicated-sql-pool"></a>Adanmış SQL havuzunda CETAS 'lar

Adanmış SQL havuzu, CETAS kullanımı ve sözdizimi için [dış tablo oluştur 'U seçin](/sql/t-sql/statements/create-external-table-as-select-transact-sql?view=azure-sqldw-latest&preserve-view=true) makalesini denetleyin. Ayrıca, Özel SQL havuzunu kullanan CTAS hakkındaki yönergeler için, [Create Table seçme](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true) makalesine bakın.

## <a name="cetas-in-serverless-sql-pool"></a>Sunucusuz SQL havuzunda CETAS

Sunucusuz SQL havuzu kullanırken CETAS, dış tablo oluşturmak ve sorgu sonuçlarını Azure Depolama Blobu veya Azure Data Lake Storage 2. dışarı aktarmak için kullanılır.

## <a name="syntax"></a>Söz dizimi

```syntaxsql
CREATE EXTERNAL TABLE [ [database_name  . [ schema_name ] . ] | schema_name . ] table_name
    WITH (
        LOCATION = 'path_to_folder',  
        DATA_SOURCE = external_data_source_name,  
        FILE_FORMAT = external_file_format_name  
)
    AS <select_statement>  
[;]

<select_statement> ::=  
    [ WITH <common_table_expression> [ ,...n ] ]  
    SELECT <select_criteria>
```

## <a name="arguments"></a>Bağımsız değişkenler

*[[ *database_name* . [ *schema_name* ]. ] | *schema_name* . ] *table_name**

Oluşturulacak tablonun üç bölümden oluşan bir adı. Bir dış tablo için sunucusuz SQL havuzu yalnızca tablo meta verilerini depolar. Sunucusuz SQL havuzunda hiçbir gerçek veri taşınmaz veya depolanmaz.

Konum = *' path_to_folder '*

Dış veri kaynağında SELECT ifadesinin sonuçlarının nereye yazılacağını belirtir. Kök klasör, dış veri kaynağında belirtilen veri konumudur. KONUM bir klasöre işaret etmelidir ve sondaki/olmalıdır. Örnek: aggregated_data/

DATA_SOURCE = *external_data_source_name*

Dış verilerin depolanacağı konumu içeren dış veri kaynağı nesnesinin adını belirtir. Dış veri kaynağı oluşturmak için [dış VERI kaynağı oluşturma (Transact-SQL)](develop-tables-external-tables.md#create-external-data-source)kullanın.

FILE_FORMAT = *external_file_format_name*

Dış veri dosyası biçimini içeren harici dosya biçim nesnesinin adını belirtir. Dış dosya biçimi oluşturmak için [dış dosya biçimi oluşturma (Transact-SQL)](develop-tables-external-tables.md#create-external-file-format)kullanın. Yalnızca FORMAT_TYPE = PARQUET ve FORMAT_TYPE = DELIMITEDTEXT içeren harici dosya biçimleri desteklenir. DELIMITEDTEXT biçimi için GZip sıkıştırması desteklenmiyor.

*<common_table_expression>*

Ortak tablo ifadesi (CTE) olarak bilinen geçici bir adlandırılmış sonuç kümesini belirtir. Daha fazla bilgi için bkz. [common_table_expression (Transact-SQL)](/sql/t-sql/queries/with-common-table-expression-transact-sql?view=azure-sqldw-latest&preserve-view=true).

<select_criteria SEÇIN>

Yeni tabloyu bir SELECT deyimindeki sonuçlarla doldurur. *select_criteria* , yeni tabloya hangi verilerin KOPYALANACAĞıNı belirleyen SELECT ifadesinin gövdesidir. SELECT deyimleri hakkında daha fazla bilgi için bkz. [Select (Transact-SQL)](/sql/t-sql/queries/select-transact-sql?view=azure-sqldw-latest&preserve-view=true).

> [!NOTE]
> SELECT içindeki ORDER BY yan tümcesi CETAS için desteklenmez.

## <a name="permissions"></a>İzinler

Klasör içeriğini listelemek ve CETAS 'ın çalışması için konum klasörüne yazmak için izinlerinizin olması gerekir.

## <a name="examples"></a>Örnekler

Bu örnekler, yıl ve eyalet tarafından toplanan toplam popülasyonu, population_ds veri kaynağında bulunan bir aggregated_data klasörüne kaydetmek için CETAS kullanır.

Bu örnek, daha önce oluşturulan kimlik bilgisini, veri kaynağını ve dış dosya biçimini kullanır. [Dış tablolar](develop-tables-external-tables.md) belgesine bakın. Sorgu sonuçlarını aynı veri kaynağındaki farklı bir klasöre kaydetmek için konum bağımsız değişkenini değiştirin. 

Sonuçları farklı bir depolama hesabına kaydetmek için DATA_SOURCE bağımsız değişkeni için farklı bir veri kaynağı oluşturun ve kullanın.

> [!NOTE]
> Aşağıdaki örnekler, genel bir Azure açık veri depolama hesabı kullanır. Salt okunurdur. Bu sorguları yürütmek için, yazma izinlerine sahip olduğunuz veri kaynağını sağlamanız gerekir.

```sql
-- use CETAS to export select statement with OPENROWSET result to  storage
CREATE EXTERNAL TABLE population_by_year_state
WITH (
    LOCATION = 'aggregated_data/',
    DATA_SOURCE = population_ds,  
    FILE_FORMAT = census_file_format
)  
AS
SELECT decennialTime, stateName, SUM(population) AS population
FROM
    OPENROWSET(BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=*/*.parquet',
    FORMAT='PARQUET') AS [r]
GROUP BY decennialTime, stateName
GO

-- you can query the newly created external table
SELECT * FROM population_by_year_state
```

Aşağıdaki örnek, bir dış tabloyu CETAS kaynağı olarak kullanır. Daha önce oluşturulan kimlik bilgisini, veri kaynağını, dış dosya biçimini ve dış tabloyu kullanır. [Dış tablolar](develop-tables-external-tables.md) belgesine bakın.

```sql
-- use CETAS with select from external table
CREATE EXTERNAL TABLE population_by_year_state
WITH (
    LOCATION = 'aggregated_data/',
    DATA_SOURCE = population_ds,  
    FILE_FORMAT = census_file_format
)  
AS
SELECT decennialTime, stateName, SUM(population) AS population
FROM census_external_table
GROUP BY decennialTime, stateName
GO

-- you can query the newly created external table
SELECT * FROM population_by_year_state
```

## <a name="supported-data-types"></a>Desteklenen veri türleri

CETAS, sonuç kümelerini aşağıdaki SQL veri türleriyle depolamak için kullanılabilir:

- ikili
- ikili
- char
- varchar
- nchar
- nvarchar
- smalldate
- date
- datetime
- datetime2
- türünde
- time
- decimal
- sayısal
- float
- real
- bigint
- tinyint
- smallint
- int
- bigint
- bit
- etmenize
- küçük para
- uniqueidentifier

> [!NOTE]
> 1 GB 'tan büyük Lobler CETAS ile kullanılamaz.

## <a name="next-steps"></a>Sonraki adımlar

[Azure SYNAPSE dış tabloları için Apache Spark](develop-storage-files-spark-tables.md)sorgulamayı deneyin.
