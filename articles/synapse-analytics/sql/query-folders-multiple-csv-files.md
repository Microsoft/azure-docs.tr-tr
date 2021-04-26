---
title: Sunucusuz SQL havuzunu kullanan sorgu klasörleri ve birden çok dosya
description: Sunucusuz SQL havuzu, Windows işletim sisteminde kullanılan Joker karakterlere benzer bir joker karakter kullanarak birden çok dosya/klasör okumayı destekler.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 83c4d88e1a87f6b546e26dd55da338a36f16ebe4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96462630"
---
# <a name="query-folders-and-multiple-files"></a>Klasörleri ve birden çok dosyayı sorgulama  

Bu makalede, Azure SYNAPSE Analytics 'te sunucusuz SQL havuzu kullanarak bir sorgu yazmayı öğreneceksiniz.

Sunucusuz SQL havuzu, Windows işletim sisteminde kullanılan Joker karakterlere benzer bir joker karakter kullanarak birden çok dosya/klasör okumayı destekler. Ancak, birden çok Joker karakterlere izin verildiğinden daha fazla esneklik mevcuttur.

## <a name="prerequisites"></a>Önkoşullar

İlk adımınız sorguları yürütebileceğiniz **bir veritabanı oluşturmaktır** . Sonra bu veritabanında [kurulum betiğini](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) yürüterek nesneleri başlatın. Bu kurulum betiği, veri kaynaklarını, veritabanı kapsamlı kimlik bilgilerini ve bu örneklerde kullanılan harici dosya biçimlerini oluşturacaktır.

Örnek sorguları izlemek için *CSV/Taxi* klasörünü kullanacaksınız. Bu, NYC Taxi-sarı TAXI 'yi içerir 2016 Temmuz 'dan 2018 Haziran 'a kadar verileri kaydeder. *CSV/TAXI* içindeki dosyalar şu model kullanılarak yıl ve ay sonra adlandırılır: yellow_tripdata_ <year> - <month> . csv

## <a name="read-all-files-in-folder"></a>Klasördeki tüm dosyaları oku

Aşağıdaki örnek, *CSV/TAXI* klasöründeki tüm NYC sarı TAXI veri dosyalarını okur ve yıl boyunca toplam pasger ve bayıldığı sayısını döndürür. Ayrıca toplam işlevlerin kullanımını gösterir.

```sql
SELECT 
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
        BULK 'csv/taxi/*.csv',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        pickup_datetime DATETIME2 2, 
        passenger_count INT 4
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> Tek OPENROWSET ile erişilen tüm dosyalar aynı yapıya sahip olmalıdır (yani, sütun sayısı ve veri türleri).

### <a name="read-subset-of-files-in-folder"></a>Klasördeki dosyaların alt kümesini oku

Aşağıdaki örnek, *CSV/TAXI* klasöründeki 2017 NYC sarı TAXI veri dosyalarını bir joker karakter kullanarak okur ve her ödeme türü için toplam tarifeli havayolu tutarını döndürür.

```sql
SELECT 
    payment_type,  
    SUM(fare_amount) AS fare_total
FROM OPENROWSET(
        BULK 'csv/taxi/yellow_tripdata_2017-*.csv',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        payment_type INT 10,
        fare_amount FLOAT 11
    ) AS nyc
GROUP BY payment_type
ORDER BY payment_type;
```

> [!NOTE]
> Tek OPENROWSET ile erişilen tüm dosyalar aynı yapıya sahip olmalıdır (yani, sütun sayısı ve veri türleri).

## <a name="read-folders"></a>Klasörleri oku

OPENROWSET 'e sağladığınız yol, bir klasörün yolu da olabilir. Aşağıdaki bölümler bu sorgu türlerini içerir.

### <a name="read-all-files-from-specific-folder"></a>Belirli bir klasördeki tüm dosyaları oku

Dosya düzeyi joker karakterini kullanarak bir klasördeki tüm dosyaları, [klasördeki tüm dosyaları oku](#read-all-files-in-folder)bölümünde gösterildiği gibi okuyabilirsiniz. Ancak, bir klasörü sorgulamak ve bu klasördeki tüm dosyaları kullanmak için bir yol vardır.

OPENROWSET içinde belirtilen yol bir klasöre işaret ediyorsa, bu klasördeki tüm dosyalar sorgunuz için kaynak olarak kullanılır. Aşağıdaki sorgu *CSV/TAXI* klasöründeki tüm dosyaları okuyacaktır.

> [!NOTE]
> Aşağıdaki sorgudaki yolun sonundaki/sonunda yer almadığına göz önüne alın. Bir klasörü gösterir. /Belirtilmemişse, sorgu bunun yerine *TAXI* adlı bir dosyayı hedefleyecek.

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
        BULK 'csv/taxi/',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> Tek OPENROWSET ile erişilen tüm dosyalar aynı yapıya sahip olmalıdır (yani, sütun sayısı ve veri türleri).

### <a name="read-all-files-from-multiple-folders"></a>Birden çok klasörden tüm dosyaları okuma

Joker karakter kullanarak birden çok klasörden dosya okumak mümkündür. Aşağıdaki sorgu, *t* ile başlayan ve *i* ile biten adlara sahip *CSV* klasöründe bulunan tüm klasörlerdeki tüm dosyaları okur.

> [!NOTE]
> Aşağıdaki sorgudaki yolun sonundaki/sonunda yer almadığına göz önüne alın. Bir klasörü gösterir. /Belirtilmemişse, sorgu *t &ast; ı* adlı dosyaları hedefleyecek.

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
        BULK 'csv/t*i/', 
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> Tek OPENROWSET ile erişilen tüm dosyalar aynı yapıya sahip olmalıdır (yani, sütun sayısı ve veri türleri).

Ölçütlerle eşleşen yalnızca bir klasörünüz olduğundan, sorgu sonucu [klasördeki tüm dosyaları okuma](#read-all-files-in-folder)ile aynıdır.

## <a name="traverse-folders-recursively"></a>Klasörleri yinelemeli olarak gez

Yolun sonunda/* * belirtirseniz, sunucusuz SQL havuzu klasörlere özyinelemeli olarak çapraz geçiş yapabilir. Aşağıdaki sorgu, *CSV* klasöründe yer alan tüm klasör ve alt klasörlerdeki tüm dosyaları okuyacaktır.

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
        BULK 'csv/taxi/**', 
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> Tek OPENROWSET ile erişilen tüm dosyalar aynı yapıya sahip olmalıdır (yani, sütun sayısı ve veri türleri).

## <a name="multiple-wildcards"></a>Birden çok joker karakter

Farklı yol düzeylerinde birden çok joker karakter kullanabilirsiniz. Örneğin, yalnızca 2017 veri içeren dosyaları okumak için bir önceki sorguyu zenginleştirebilirsiniz. bu adların *t* ile başlayan ve *i* ile biten tüm klasörlerden.

> [!NOTE]
> Aşağıdaki sorgudaki yolun sonundaki/sonunda yer almadığına göz önüne alın. Bir klasörü gösterir. /Belirtilmemişse, sorgu *t &ast; ı* adlı dosyaları hedefleyecek.
> Sorgu başına en fazla 10 joker sınır vardır.

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
        BULK 'csv/t*i/yellow_tripdata_2017-*.csv',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> Tek OPENROWSET ile erişilen tüm dosyalar aynı yapıya sahip olmalıdır (yani, sütun sayısı ve veri türleri).

Ölçütlere uyan yalnızca bir klasörünüz olduğundan, sorgu sonucu, [klasördeki dosyaların okuma alt kümesiyle](#read-subset-of-files-in-folder) aynıdır ve [belirli bir klasördeki tüm dosyaları okur](#read-all-files-from-specific-folder). [Sorgu Parquet dosyalarında](query-parquet-files.md)daha karmaşık joker karakter kullanım senaryoları ele alınmıştır.

## <a name="next-steps"></a>Sonraki adımlar

[Sorgu belirli dosyalar](query-specific-files.md) makalesinde daha fazla bilgi bulabilirsiniz.
