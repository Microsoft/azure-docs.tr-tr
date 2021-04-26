---
title: "Öğretici: Azure SYNAPSE Studio 'da Azure açık veri kümelerini çözümlemek için sunucusuz SQL havuzu kullanma"
description: Bu öğreticide, sunucusuz SQL havuzu kullanarak farklı Azure açık veri kümelerini birleştiren keşif verileri analizini nasıl kolayca gerçekleştirebileceğiniz ve Azure SYNAPSE Studio 'daki sonuçları görselleştirebileceğiniz gösterilmektedir.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: sql
ms.date: 11/20/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: d37597f8667c461e8d61f8214483f57eb702c2a0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97007560"
---
# <a name="tutorial-explore-and-analyze-data-lakes-with-serverless-sql-pool"></a>Öğretici: sunucusuz SQL havuzuyla veri Lakes 'i araştırma ve çözümleme

Bu öğreticide, araştırmacı veri analizini nasıl gerçekleştireceğinizi öğreneceksiniz. Sunucusuz SQL havuzu kullanarak farklı Azure açık veri kümelerini birleştirebilirsiniz. Böylece sonuçları Azure SYNAPSE Analytics için SYNAPSE Studio 'da görselleştirebilirsiniz.

OPENROWSET (toplu...) işlevi, Azure Storage 'daki dosyalara erişmenize olanak tanır. [OPENROWSET](develop-openrowset.md) işlevi, uzak bir veri kaynağının içeriğini okur (örneğin, dosya) ve içeriği bir dizi satır olarak döndürür.

## <a name="automatic-schema-inference"></a>Otomatik Şema çıkarımı

Veriler Parquet dosya biçiminde depolandığından, otomatik Şema çıkarımı kullanılabilir. Dosyalardaki tüm sütunların veri türlerini listelemeden verileri kolayca sorgulayabilirsiniz. Ayrıca, belirli bir dosya alt kümesini filtrelemek için sanal sütun mekanizmasını ve FilePath işlevini de kullanabilirsiniz.

İlk olarak aşağıdaki sorguyu çalıştırarak NYC TAXI verilerini öğrenelim:

```sql
SELECT TOP 100 * FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [nyc]
```

[New York City (NYC) TAXI veri kümesi](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) şunları içerir:

- Seçme ve bırakma tarihleri ve zamanları.
- Seçme ve bırakma konumları. 
- Seyahat mesafeleri.
- Fares dökümü.
- Oran türleri.
- Ödeme türleri. 
- Sürücü tarafından bildirilen yolcular sayısı.

Benzer şekilde, aşağıdaki sorguyu kullanarak genel tatiller veri kümesini sorgulayabilirsiniz:

```sql
SELECT TOP 100 * FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/holidaydatacontainer/Processed/*.parquet',
        FORMAT='PARQUET'
    ) AS [holidays]
```

Son olarak, aşağıdaki sorguyu kullanarak hava durumu verileri veri kümesini de sorgulayabilirsiniz:

```sql
SELECT
    TOP 100 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/isdweatherdatacontainer/ISDWeather/year=*/month=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [weather]
```

Veri kümelerinin açıklamalarındaki tek tek sütunların anlamı hakkında daha fazla bilgi edinebilirsiniz: 
- [NYC TAXI](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/)
- [Ortak tatiller](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/)
- [Hava durumu verileri](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/)

## <a name="time-series-seasonality-and-outlier-analysis"></a>Zaman serisi, mevsimsellik ve aykırı değer analizi

Aşağıdaki sorguyu kullanarak yıllık sayıda TAXI bayıldığı 'i kolayca özetleyebilirsiniz:

```sql
SELECT
    YEAR(tpepPickupDateTime) AS current_year,
    COUNT(*) AS rides_per_year
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [nyc]
WHERE nyc.filepath(1) >= '2009' AND nyc.filepath(1) <= '2019'
GROUP BY YEAR(tpepPickupDateTime)
ORDER BY 1 ASC
```

Aşağıdaki kod parçacığında, yıllık sayıda TAXI rides için sonuç gösterilmektedir:

![Yıllık TAXI bayıldığı sonuç parçacığı sayısı](./media/tutorial-data-analyst/yearly-taxi-rides.png)

Veriler, **tablodan** **grafik** görünümüne geçiş yaparak SYNAPSE Studio 'da görselleştirilebilir. **Alan**, **çubuk**, **sütun**, **çizgi**, **pasta** ve **dağılım** gibi farklı grafik türleri arasından seçim yapabilirsiniz. Bu durumda, **sütun** grafiğinin **Kategori** sütununu **current_year** olarak çizimiyle:

![Yıl başına bayıldığı gösteren sütun grafiği](./media/tutorial-data-analyst/column-chart-rides-year.png)

Bu görselleştirmede, yıllar üzerinde azalan sayıların bir eğilimini görebilirsiniz. Bu azalma, en iyi şekilde, Ride paylaşım şirketlerinin son derece artmasından kaynaklanır.

> [!NOTE]
> Bu öğreticinin yazıldığı sırada, 2019 için veriler eksik. Sonuç olarak, bu yıl için bayıldığı sayısı çok büyük bir düşüş vardır.

Ardından, Analizi tek bir yılda odaklanalım, örneğin, 2016. Aşağıdaki sorgu, bu yıl boyunca günlük bayıldığı sayısını döndürür:

```sql
SELECT
    CAST([tpepPickupDateTime] AS DATE) AS [current_day],
    COUNT(*) as rides_per_day
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [nyc]
WHERE nyc.filepath(1) = '2016'
GROUP BY CAST([tpepPickupDateTime] AS DATE)
ORDER BY 1 ASC
```

Aşağıdaki kod parçacığında bu sorgunun sonucu gösterilmektedir:

![2016 sonuç parçacığı için günlük sayıda bayıldığı](./media/tutorial-data-analyst/daily-rides.png)

Daha sonra, **Kategori** sütunu **current_day** olarak ayarlanmış olan **sütun** grafiğini ve **gösterge (seri)** sütununu **rides_per_day** olarak belirterek verileri kolayca görselleştirebilirsiniz.

![2016 için günlük olarak bayıldığı gösteren sütun grafiği](./media/tutorial-data-analyst/column-chart-daily-rides.png)

Çizim grafiğinde, en yoğun gün olarak Cumartesi günleri olan haftalık bir model olduğunu görebilirsiniz. Yaz ayları sırasında tatiller nedeniyle daha az TAXI bayıldığı vardır. Ayrıca, bazı önemli düşmelere ne zaman ve neden gerçekleştiğine ilişkin açık bir model olmadan TAXI bayıldığı sayısında dikkat edin.

Daha sonra, haldeki bırakmaya genel tatiller ile ilişkili olup olmadığını görelim. NYC TAXI bayıldığı veri kümesini genel tatiller veri kümesiyle birleştirerek bir bağıntı olup olmadığını görebiliriz:

```sql
WITH taxi_rides AS
(
    SELECT
        CAST([tpepPickupDateTime] AS DATE) AS [current_day],
        COUNT(*) as rides_per_day
    FROM  
        OPENROWSET(
            BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
            FORMAT='PARQUET'
        ) AS [nyc]
    WHERE nyc.filepath(1) = '2016'
    GROUP BY CAST([tpepPickupDateTime] AS DATE)
),
public_holidays AS
(
    SELECT
        holidayname as holiday,
        date
    FROM
        OPENROWSET(
            BULK 'https://azureopendatastorage.blob.core.windows.net/holidaydatacontainer/Processed/*.parquet',
            FORMAT='PARQUET'
        ) AS [holidays]
    WHERE countryorregion = 'United States' AND YEAR(date) = 2016
)
SELECT
*
FROM taxi_rides t
LEFT OUTER JOIN public_holidays p on t.current_day = p.date
ORDER BY current_day ASC
```

![NYC TAXI bayıldığı ve genel tatiller veri kümeleri sonuç görselleştirme](./media/tutorial-data-analyst/rides-public-holidays.png)

Bu kez, genel tatiller sırasında vergileni bayıldığı sayısını vurgulamak istiyoruz. Bu amaçla **Kategori** sütunu için **hiçbiri** ' ni, **gösterge (Seriler)** sütunları olarak **rides_per_day** ve **tatil** ' i seçeceğiz.

![Genel tatiller sırasında vergileni bayıldığı sayısı çizim grafiği](./media/tutorial-data-analyst/plot-chart-public-holidays.png)

Çizim grafiğinden, genel tatiller sırasında vergilenme sayısının düşük olduğunu görebilirsiniz. 23 Ocak 'ta açıklanamayan bir büyük bırakma hala var. Hava durumu verileri veri kümesini sorgulayarak, bu gündeki NYC içindeki hava durumunu kontrol edelim:

```sql
SELECT
    AVG(windspeed) AS avg_windspeed,
    MIN(windspeed) AS min_windspeed,
    MAX(windspeed) AS max_windspeed,
    AVG(temperature) AS avg_temperature,
    MIN(temperature) AS min_temperature,
    MAX(temperature) AS max_temperature,
    AVG(sealvlpressure) AS avg_sealvlpressure,
    MIN(sealvlpressure) AS min_sealvlpressure,
    MAX(sealvlpressure) AS max_sealvlpressure,
    AVG(precipdepth) AS avg_precipdepth,
    MIN(precipdepth) AS min_precipdepth,
    MAX(precipdepth) AS max_precipdepth,
    AVG(snowdepth) AS avg_snowdepth,
    MIN(snowdepth) AS min_snowdepth,
    MAX(snowdepth) AS max_snowdepth
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/isdweatherdatacontainer/ISDWeather/year=*/month=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [weather]
WHERE countryorregion = 'US' AND CAST([datetime] AS DATE) = '2016-01-23' AND stationname = 'JOHN F KENNEDY INTERNATIONAL AIRPORT'
```

![Hava durumu veri kümesi sonuç görselleştirme](./media/tutorial-data-analyst/weather-data-set-visualization.png)

Sorgunun sonuçları, şu nedenle oluşan vergilenme sayısının meydana gelen bırakma sayısını gösterir:

- NYC 'de, ağır kar (~ 30 cm) ile bu günde bir göz at vardı.
- Bu, soğuk idi (sıcaklık 0 derecenin altında).
- Bu, WINDY (~ 10 m/s) idi.

Bu öğreticide, bir veri analistinin keşif veri analizini hızlı bir şekilde nasıl gerçekleştirebildiğini, sunucusuz SQL havuzu kullanarak farklı veri kümelerini kolayca nasıl birleştirebileceğini ve Azure SYNAPSE Studio 'Yu kullanarak sonuçları görselleştirmesini göstermiştir

## <a name="next-steps"></a>Sonraki adımlar

Sunucusuz SQL havuzunu Power BI Desktop ve rapor oluşturmaya nasıl bağlayacağınızı öğrenmek için bkz. [Power BI Desktop için sunucusuz SQL havuzunu bağlama ve rapor oluşturma](tutorial-connect-power-bi-desktop.md).

Sunucusuz SQL havuzunda dış tabloları nasıl kullanacağınızı öğrenmek için bkz. [SYNAPSE SQL ile dış tabloları kullanma](develop-tables-external-tables.md?tabs=sql-pool)
 
