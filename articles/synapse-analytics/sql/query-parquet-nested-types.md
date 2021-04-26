---
title: Sunucusuz SQL havuzu kullanarak iç içe geçmiş türleri sorgulama
description: Bu makalede, sunucusuz SQL havuzu kullanarak Parquet iç içe türlerini sorgulamayı öğreneceksiniz.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 2ef547bea9ba84665ec7187a5adf2f7a7d87e0a4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101666325"
---
# <a name="query-nested-types-in-parquet-and-json-files-by-using-serverless-sql-pool-in-azure-synapse-analytics"></a>Azure SYNAPSE Analytics 'te sunucusuz SQL havuzu kullanarak Parquet ve JSON dosyalarındaki iç içe türleri sorgulama

Bu makalede, Azure SYNAPSE Analytics 'te sunucusuz SQL havuzu kullanarak bir sorgu yazmayı öğreneceksiniz. Sorgu, Parquet iç içe geçmiş türlerini okur.
İç içe türler, nesneleri veya dizileri temsil eden karmaşık yapılardır. İç içe türler, içinde depolanabilir: 
- [Parquet](query-parquet-files.md), diziler ve nesneler içeren birden çok karmaşık sütunlarınız olabilir.
- Karmaşık bir JSON belgesini tek bir sütun olarak okuyabileceğiniz hiyerarşik [JSON dosyaları](query-json-files.md).
- Her belge karmaşık iç içe Özellikler içerebilen Koleksiyonlar (Şu anda geçitli genel önizleme kapsamında) Azure Cosmos DB.

Sunucusuz SQL havuzu tüm iç içe geçmiş türleri JSON nesneleri ve diziler olarak biçimlendirir. Bu nedenle, [JSON işlevleri kullanarak karmaşık nesneleri ayıklayabilir veya değiştirebilir](/sql/relational-databases/json/validate-query-and-change-json-data-with-built-in-functions-sql-server) veya [OPENJSON işlevini kullanarak JSON verilerini ayrıştırtırabilirsiniz](/sql/relational-databases/json/convert-json-data-to-rows-and-columns-with-openjson-sql-server). 

Aşağıda, iç içe geçmiş nesneler içeren [Covı-19 açık araştırma veri kümesi](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) JSON dosyasındaki skaler ve nesne değerlerini ayıklayan bir sorgu örneği verilmiştir: 

```sql
SELECT
    title = JSON_VALUE(doc, '$.metadata.title'),
    first_author = JSON_QUERY(doc, '$.metadata.authors[0]'),
    first_author_name = JSON_VALUE(doc, '$.metadata.authors[0].first'),
    complex_object = doc
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/covid19temp/comm_use_subset/pdf_json/000b7d1517ceebb34e1e3e817695b6de03e2fa78.json',
        FORMAT='CSV', FIELDTERMINATOR ='0x0b', FIELDQUOTE = '0x0b', ROWTERMINATOR = '0x0b'
    )
    WITH ( doc varchar(MAX) ) AS docs;
```

`JSON_VALUE`İşlev, belirtilen yoldaki alandan skaler bir değer döndürür. `JSON_QUERY`İşlev, belirtilen yoldaki alandan JSON olarak biçimlendirilen bir nesne döndürür.

> [!IMPORTANT]
> Bu örnek COVıD-19 açık araştırma veri kümesinden bir dosya kullanır. Bu [verilerin yapısına ve yapısına bakın](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/).

## <a name="prerequisites"></a>Önkoşullar

İlk adım, DataSource 'un oluşturulacağı bir veritabanı oluşturmaktır. Ardından, veritabanında bir [kurulum betiği](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) çalıştırarak nesneleri başlatacaksınız. Kurulum betiği, örneklerde kullanılan veri kaynaklarını, veritabanı kapsamlı kimlik bilgilerini ve harici dosya biçimlerini oluşturacaktır.

## <a name="project-nested-or-repeated-data"></a>Proje iç içe veya yinelenen veriler

Bir Parquet dosyası karmaşık türlerde birden fazla sütuna sahip olabilir. Bu sütunlardaki değerler JSON metni olarak biçimlendirilir ve VARCHAR sütunları olarak döndürülür. Aşağıdaki sorgu, Structexörnek. Parquet dosyasını okur ve iç içe geçmiş sütunların değerlerinin nasıl okunacağını gösterir: 

```sql
SELECT
    DateStruct, TimeStruct, TimestampStruct, DecimalStruct, FloatStruct
FROM
    OPENROWSET(
        BULK 'parquet/nested/structExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    )
    WITH (
        DateStruct VARCHAR(8000),
        TimeStruct VARCHAR(8000),
        TimestampStruct VARCHAR(8000),
        DecimalStruct VARCHAR(8000),
        FloatStruct VARCHAR(8000)
    ) AS [r];
```

Bu sorgu aşağıdaki sonucu döndürür. İç içe geçmiş her nesnenin içeriği JSON metni olarak döndürülür.

| DateStruct    | TimeStruct    | TimestampStruct   | DecimalStruct | FloatStruct |
| --- | --- | --- | --- | --- |
|{"Date": "2009-04-25"}| {"Time": "20:51:54.3598000"}|    {"Timestamp": "5501-04-08 12:13:57.4821000"}|    {"Decimal": 11143412.25350}| {"Float": 0,5}|
|{"Date": "1916-04-29"}| {"Time": "00:16:04.6778000"}|    {"Timestamp": "1990-06-30 20:50:52.6828000"}|    {"Decimal": 1963545.62800}|  {"Float":-2,125}|

Aşağıdaki sorgu, Adatsimplearray. Parquet dosyasını okur. İç içe geçmiş ve yinelenen veriler de dahil olmak üzere, Parquet dosyasındaki tüm sütunları.

```sql
SELECT
    SimpleArray
FROM
    OPENROWSET(
        BULK 'parquet/nested/justSimpleArray.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS [r];
```

Bu sorgu aşağıdaki sonucu döndürür:

|Simplelearray|
| --- |
|[11, 12, 13]|
|[21, 22, 23]|

## <a name="read-properties-from-nested-object-columns"></a>İç içe nesne sütunlarından özellikleri okuma

`JSON_VALUE`İşlevi, JSON metni olarak biçimlendirilen sütunlardan değer döndürmenizi sağlar:

```sql
SELECT
    title = JSON_VALUE(complex_column, '$.metadata.title'),
    first_author_name = JSON_VALUE(complex_column, '$.metadata.authors[0].first'),
    body_text = JSON_VALUE(complex_column, '$.body_text.text'),
    complex_column
FROM
    OPENROWSET( BULK 'https://azureopendatastorage.blob.core.windows.net/covid19temp/comm_use_subset/pdf_json/000b7d1517ceebb34e1e3e817695b6de03e2fa78.json',
                FORMAT='CSV', FIELDTERMINATOR ='0x0b', FIELDQUOTE = '0x0b', ROWTERMINATOR = '0x0b' ) WITH ( complex_column varchar(MAX) ) AS docs;
```

Sonuç aşağıdaki tabloda gösterilmiştir:

|başlık  | first_author_name | body_text | complex_column |
| --- | --- | --- | --- |
| Tamamlayıcı bilgiler bir ekonomik-epidemıolo... | Julien   | -Şekil S1: Phylogeny... | `{    "paper_id": "000b7d1517ceebb34e1e3e817695b6de03e2fa78",    "metadata": {        "title": "Supplementary Information An eco-epidemiological study of Morbilli-related paramyxovirus infection in Madagascar bats reveals host-switching as the dominant macro-evolutionary mechanism",        "authors": [            {                "first": "Julien"` |

Çoğu durumda karmaşık bir JSON nesnesi içeren tek bir sütun döndüren JSON dosyalarından farklı olarak, Parquet dosyalarında birden çok karmaşık sütun bulunabilir. Her sütunda işlevini kullanarak iç içe sütunların özelliklerini okuyabilirsiniz `JSON_VALUE` . `OPENROWSET` bir yan tümce içinde iç içe özelliklerin yollarını doğrudan belirtmenizi sağlar `WITH` . Yolları bir sütunun adı olarak ayarlayabilir veya sütun türünden sonra bir [JSON yolu ifadesi](/sql/relational-databases/json/json-path-expressions-sql-server) ekleyebilirsiniz.

Aşağıdaki sorgu, Structexörnek. Parquet dosyasını okur ve iç içe geçmiş bir sütunun yüzey öğelerinin nasıl gösterileceğini gösterir. İç içe bir değere başvurmak için iki yol vardır:
- Tür belirtiminden sonra iç içe değer yol ifadesini belirterek.
- Sütun adını, alanlara başvurmak için do "." kullanarak iç içe bir yol olarak biçimlendirerek.

```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK 'parquet/nested/structExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    )
    WITH (
        [DateValue] DATE '$.DateStruct.Date',
        [TimeStruct.Time] TIME,
        [TimestampStruct.Timestamp] DATETIME2,
        DecimalValue DECIMAL(18, 5) '$.DecimalStruct.Decimal',
        [FloatStruct.Float] FLOAT
    ) AS [r];
```

## <a name="access-elements-from-repeated-columns"></a>Yinelenen sütunlardan öğelerine erişin

Aşağıdaki sorgu, yatsimplearray. Parquet dosyasını okur ve bir Array veya Map gibi yinelenen bir sütunun içinden skaler bir öğe almak için [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?view=azure-sqldw-latest&preserve-view=true) kullanır:

```sql
SELECT
    *,
    JSON_VALUE(SimpleArray, '$[0]') AS FirstElement,
    JSON_VALUE(SimpleArray, '$[1]') AS SecondElement,
    JSON_VALUE(SimpleArray, '$[2]') AS ThirdElement
FROM
    OPENROWSET(
        BULK 'parquet/nested/justSimpleArray.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS [r];
```

Sonuç şu şekildedir:

|Simplelearray    | FirstElement  | SecondElement | ThirdElement |
| --- | --- | --- | --- |
| [11, 12, 13] | 11   | 12 | 13 |
| [21, 22, 23] | 21   | 22 | 23 |

## <a name="access-sub-objects-from-complex-columns"></a>Karmaşık sütunlardan alt nesnelere erişme

Aşağıdaki sorgu, mapExample. Parquet dosyasını okur ve bir dizi veya eşleme gibi yinelenen bir sütunun içinden skalar olmayan bir öğeyi almak için [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?view=azure-sqldw-latest&preserve-view=true) kullanır:

```sql
SELECT
    MapOfPersons,
    JSON_QUERY(MapOfPersons, '$."John Doe"') AS [John]
FROM
    OPENROWSET(
        BULK 'parquet/nested/mapExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS [r];
```

Ayrıca, bir yan tümce içinde döndürmek istediğiniz sütunlara açık bir şekilde başvurabilirsiniz `WITH` :

```sql
SELECT DocId,
    MapOfPersons,
    JSON_QUERY(MapOfPersons, '$."John Doe"') AS [John]
FROM
    OPENROWSET(
        BULK 'parquet/nested/mapExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) 
    WITH (DocId bigint, MapOfPersons VARCHAR(max)) AS [r];
```

Yapı, `MapOfPersons` varchar sütunu olarak döndürülür ve JSON dizesi olarak biçimlendirilir.

## <a name="project-values-from-repeated-columns"></a>Yinelenen sütunlardan proje değerleri

Bazı sütunlarda bir skaler değerler dizisine sahipseniz (örneğin `[1,2,3]` ), bu betiği kullanarak bunları kolayca genişletebilir ve ana satırla birleştirebilirsiniz:

```sql
SELECT
    SimpleArray, Element
FROM
    OPENROWSET(
        BULK 'parquet/nested/justSimpleArray.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS arrays
    CROSS APPLY OPENJSON (SimpleArray) WITH (Element int '$') as array_values
```

## <a name="next-steps"></a>Sonraki adımlar

Sonraki makalede [JSON dosyalarının nasıl sorgulanyapılacağı](query-json-files.md)gösterilir.