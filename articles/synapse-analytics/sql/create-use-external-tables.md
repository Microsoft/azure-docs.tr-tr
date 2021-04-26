---
title: Sunucusuz SQL havuzunda dış tablolar oluşturma ve kullanma
description: Bu bölümde, sunucusuz SQL havuzunda dış tabloları oluşturmayı ve kullanmayı öğreneceksiniz.
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: 5dcd4b7b76752ca5396fc68afc8d4c8e4e1edca5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96462304"
---
# <a name="create-and-use-external-tables-using-serverless-sql-pool-in-azure-synapse-analytics"></a>Azure SYNAPSE Analytics 'te sunucusuz SQL havuzunu kullanarak dış tablolar oluşturma ve kullanma

Bu bölümde, sunucusuz SQL havuzunda [dış tabloları](develop-tables-external-tables.md) oluşturmayı ve kullanmayı öğreneceksiniz. Dış tablolar, sunucusuz SQL havuzundaki dış verilere erişimi denetlemek istediğinizde ve Power BI gibi araçları, sunucusuz SQL havuzuyla birlikte kullanmak istiyorsanız yararlıdır. Dış tablolar, iki tür depolama alanına erişebilir:
- Kullanıcıların ortak depolama dosyalarına erişebileceği ortak depolama.
- Kullanıcıların SAS kimlik bilgilerini, Azure AD kimliğini veya SYNAPSE çalışma alanının yönetilen kimliğini kullanarak depolama dosyalarına erişebileceği korumalı depolama.

## <a name="prerequisites"></a>Önkoşullar

İlk adımınız tabloların oluşturulacağı bir veritabanı oluşturmaktır. Sonra bu veritabanında [kurulum betiğini](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) yürüterek nesneleri başlatın. Bu kurulum betiği, bu örnekte kullanılan aşağıdaki nesneleri oluşturacaktır:
- `sqlondemand`SAS korumalı Azure depolama hesabına erişim sağlayan VERITABANı KAPSAMLı KIMLIK bilgileri `https://sqlondemandstorage.blob.core.windows.net` .

    ```sql
    CREATE DATABASE SCOPED CREDENTIAL [sqlondemand]
    WITH IDENTITY='SHARED ACCESS SIGNATURE',  
    SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D'
    ```

- `sqlondemanddemo`SAS anahtarıyla korunan tanıtım depolama hesabına başvuran dış VERI kaynağı ve `YellowTaxi` yerinde genel kullanıma açık Azure depolama HESABıNA başvuran dış veri kaynağı `https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/` .

    ```sql
    CREATE EXTERNAL DATA SOURCE SqlOnDemandDemo WITH (
        LOCATION = 'https://sqlondemandstorage.blob.core.windows.net',
        CREDENTIAL = sqlondemand
    );
    GO
    CREATE EXTERNAL DATA SOURCE YellowTaxi
    WITH ( LOCATION = 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/')
    ```

- `QuotedCSVWithHeaderFormat` `ParquetFormat` CSV ve Parquet dosya türlerini tanımlayan dosya biçimleri.

    ```sql
    CREATE EXTERNAL FILE FORMAT QuotedCsvWithHeaderFormat
    WITH (  
        FORMAT_TYPE = DELIMITEDTEXT,
        FORMAT_OPTIONS ( FIELD_TERMINATOR = ',', STRING_DELIMITER = '"', FIRST_ROW = 2   )
    );
    GO
    CREATE EXTERNAL FILE FORMAT ParquetFormat WITH (  FORMAT_TYPE = PARQUET );
    ```

Bu makaledeki sorgular, örnek veritabanınızda yürütülecektir ve bu nesneleri kullanacaktır. 

## <a name="create-an-external-table-on-protected-data"></a>Korumalı verilerde dış tablo oluşturma

Azure depolama hesabındaki verilere erişen, Azure AD kimlik veya SAS anahtarı olan kullanıcılara erişim sağlayan dış tablolar oluşturabilirsiniz. Dış tabloları, normal SQL Server dış tablolar oluşturduğunuz şekilde oluşturabilirsiniz. 

Aşağıdaki sorgu, veri kaynağı kullanılarak başvurulan  `sqlondemanddemo` ve çağrılan veritabanı kapsamlı kimlik bilgileriyle korunan SynapseSQL demo Azure depolama hesabındanpopulation.csvdosyayı okuyan bir dış tablo oluşturur `sqlondemand` . 

Veri kaynağı ve veritabanı kapsamlı kimlik bilgileri [Kurulum komut](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql)dosyasında oluşturulur.

> [!NOTE]
> Sorgudaki ilk satırı değiştirin, örn., [mydbname], bu nedenle Oluşturduğunuz veritabanını kullanıyorsunuz. 

```sql
USE [mydbname];
GO
CREATE EXTERNAL TABLE populationExternalTable
(
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
)
WITH (
    LOCATION = 'csv/population/population.csv',
    DATA_SOURCE = sqlondemanddemo,
    FILE_FORMAT = QuotedCSVWithHeaderFormat
);
```

## <a name="create-an-external-table-on-public-data"></a>Ortak verilerde dış tablo oluşturma

Genel kullanıma açık Azure depolama alanına yerleştirilmiş dosyalardaki verileri okuyan dış tablolar oluşturabilirsiniz. Bu [kurulum betiği](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) , aşağıdaki sorguda kullanılan ortak dış veri kaynağı ve Parquet dosya biçimi tanımı oluşturacak:

```sql
CREATE EXTERNAL TABLE Taxi (
     vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
     pickup_datetime DATETIME2, 
     dropoff_datetime DATETIME2,
     passenger_count INT,
     trip_distance FLOAT,
     fare_amount FLOAT,
     tip_amount FLOAT,
     tolls_amount FLOAT,
     total_amount FLOAT
) WITH (
         LOCATION = 'puYear=*/puMonth=*/*.parquet',
         DATA_SOURCE = YellowTaxi,
         FILE_FORMAT = ParquetFormat
);
```
## <a name="use-an-external-table"></a>Dış tablo kullan

Sorgularınızdaki [dış tabloları](develop-tables-external-tables.md) , SQL Server sorgularda kullandığınız şekilde kullanabilirsiniz.

Aşağıdaki sorgu, önceki bölümde oluşturduğumuz *popülasyon* dış tablosunu kullanarak bunu gösterir. Ülke/bölge adlarını, popülasyon 2019 olarak azalan sırada döndürür.

> [!NOTE]
> Sorgudaki ilk satırı değiştirin, örn., [mydbname], bu nedenle Oluşturduğunuz veritabanını kullanıyorsunuz.

```sql
USE [mydbname];
GO

SELECT
    country_name, population
FROM populationExternalTable
WHERE
    [year] = 2019
ORDER BY
    [population] DESC;
```

## <a name="next-steps"></a>Sonraki adımlar

Bir sorgunun sonuçlarını depolama alanına nasıl depolayabileceği hakkında bilgi edinmek için [depolama hakkında sorgu sonuçları depolama](../sql/create-external-table-as-select.md) makalesine başvurun.
