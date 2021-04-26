---
title: Azure Stream Analytics ortak sorgu desenleri
description: Bu makalede, Azure Stream Analytics işlerinde yararlı olan birkaç ortak sorgu deseni ve tasarımı açıklanmaktadır.
services: stream-analytics
author: rodrigoaatmicrosoft
ms.author: rodrigoa
ms.service: stream-analytics
ms.topic: how-to
ms.date: 12/18/2019
ms.custom: devx-track-js
ms.openlocfilehash: 8fcda77858b3feb78a04971a7ad1f20c0ea51fa1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98020001"
---
# <a name="common-query-patterns-in-azure-stream-analytics"></a>Azure Stream Analytics ortak sorgu desenleri

Azure Stream Analytics sorguları SQL benzeri bir sorgu dilinde ifade edilir. Dil yapıları [Stream Analytics sorgu dili başvuru](/stream-analytics-query/stream-analytics-query-language-reference) kılavuzunda belgelenmiştir. 

Sorgu tasarımı, olay verilerini bir giriş akışından bir çıkış veri deposuna taşımak için basit geçiş mantığını ifade edebilir veya [Stream Analytics kılavuzunu kullanarak IoT çözümü oluşturma](stream-analytics-build-an-iot-solution-using-stream-analytics.md) bölümünde olduğu gibi çeşitli zaman pencerelerinin toplamlarını hesaplamak için zengin desenler eşleştirme ve zamana bağlı analiz işlemleri gerçekleştirebilir. Akış olaylarını birleştirmek için birden çok girişe veri katılabilir ve olay değerlerini zenginleştirmek için statik başvuru verilerine yönelik aramalar yapabilirsiniz. Ayrıca, birden fazla çıkışına veri yazabilirsiniz.

Bu makalede, gerçek dünyada senaryolar temelinde birkaç ortak sorgu desenlerine yönelik çözümler özetlenmektedir.

## <a name="supported-data-formats"></a>Desteklenen veri biçimleri

Azure Stream Analytics CSV, JSON ve avro veri biçimlerinde olayları işlemeyi destekler.

Hem JSON hem de avro, iç içe geçmiş nesneler (kayıtlar) veya diziler gibi karmaşık türler içerebilir. Bu karmaşık veri türleriyle çalışma hakkında daha fazla bilgi için bkz. [JSON ve avro veri ayrıştırma](stream-analytics-parsing-json.md) makalesi.

## <a name="send-data-to-multiple-outputs"></a>Verileri birden çok çıkışına gönder

Birden çok **Select** deyimi, verileri farklı çıkış havuzları için çıkarmak üzere kullanılabilir. Örneğin, bir **seçim** eşik tabanlı bir uyarının çıktısını alabilir, başka bir deyişle olaylar BLOB depolama alanına çıktı verebilir.

**Giriş**:

| Marka | Saat |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |
| Make1 |2015-01-01T00:00:02.0000000 Z |
| Make2 |2015-01-01T00:00:01.0000000 Z |
| Make2 |2015-01-01T00:00:02.0000000 Z |
| Make2 |2015-01-01T00:00:03.0000000 Z |

**Çıktı ArchiveOutput**:

| Marka | Saat |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |
| Make1 |2015-01-01T00:00:02.0000000 Z |
| Make2 |2015-01-01T00:00:01.0000000 Z |
| Make2 |2015-01-01T00:00:02.0000000 Z |
| Make2 |2015-01-01T00:00:03.0000000 Z |

**Çıkış AlertOutput**:

| Marka | Saat | Count |
| --- | --- | --- |
| Make2 |2015-01-01T00:00:10.0000000 Z |3 |

**Sorgu**:

```SQL
SELECT
    *
INTO
    ArchiveOutput
FROM
    Input TIMESTAMP BY Time

SELECT
    Make,
    System.TimeStamp() AS Time,
    COUNT(*) AS [Count]
INTO
    AlertOutput
FROM
    Input TIMESTAMP BY Time
GROUP BY
    Make,
    TumblingWindow(second, 10)
HAVING
    [Count] >= 3
```

**Into** yan tümcesi, verilerin yazılacağı çıkışların Stream Analytics belirtir. İlk **seçim** , girişten veri alan ve bunu **ArchiveOutput** adlı çıktıya gönderen bir geçiş sorgusu tanımlar. İkinci sorgu, sonuçları **Alertoutput** adlı bir aşağı akış uyarısı sistem çıktısına göndermeden önce bazı basit toplama ve filtreleme işlemi yapar.

**WITH** yan tümcesinin birden çok alt sorgu bloğu tanımlamak için kullanılabileceğini unutmayın. Bu seçenek, giriş kaynağına daha az okuyucu açma avantajına sahiptir.

**Sorgu**:

```SQL
WITH ReaderQuery AS (
    SELECT
        *
    FROM
        Input TIMESTAMP BY Time
)

SELECT * INTO ArchiveOutput FROM ReaderQuery

SELECT 
    Make,
    System.TimeStamp() AS Time,
    COUNT(*) AS [Count] 
INTO AlertOutput 
FROM ReaderQuery
GROUP BY
    Make,
    TumblingWindow(second, 10)
HAVING [Count] >= 3
```

Daha fazla bilgi için bkz. [ **WITH** yan tümcesi](/stream-analytics-query/with-azure-stream-analytics).

## <a name="simple-pass-through-query"></a>Basit geçişli sorgu

Giriş akışı verilerini çıkışa kopyalamak için basit bir geçişli sorgu kullanılabilir. Örneğin, gerçek zamanlı araç bilgilerini içeren bir veri akışının mektup analizi için bir SQL veritabanında kaydedilmesi gerekiyorsa, basit bir geçişli sorgu işi işler.

**Giriş**:

| Marka | Saat | Ağırlık |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |"1000" |
| Make1 |2015-01-01T00:00:02.0000000 Z |"2000" |

**Çıkış**:

| Marka | Saat | Ağırlık |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |"1000" |
| Make1 |2015-01-01T00:00:02.0000000 Z |"2000" |

**Sorgu**:

```SQL
SELECT
    *
INTO Output
FROM Input
```

Bir **Select** * sorgusu, gelen bir olaydaki tüm alanları ve bunları çıkışa gönderir. Aynı şekilde, **Select** , yalnızca girişte gerekli alanları proje için de kullanılabilir. Bu örnekte, araç *Oluştur* ve *zaman* , kaydedilecek tek gerekli alanlar Ise, bu alanlar **Select** ifadesinde belirlenebilir.

**Giriş**:

| Marka | Saat | Ağırlık |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |1000 |
| Make1 |2015-01-01T00:00:02.0000000 Z |2000 |
| Make2 |2015-01-01T00:00:04.0000000 Z |1500 |

**Çıkış**:

| Marka | Saat |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |
| Make1 |2015-01-01T00:00:02.0000000 Z |
| Make2 |2015-01-01T00:00:04.0000000 Z |

**Sorgu**:

```SQL
SELECT
    Make, Time
INTO Output
FROM Input
```

## <a name="string-matching-with-like-and-not-like"></a>LIKE ve LIKE ile eşleşen dize

**LIKE** ve **LIKE** , bir alanın belirli bir Düzenle eşleşip eşleşmediğini doğrulamak için kullanılabilir. Örneğin, yalnızca ' A ' harfiyle başlayan ve 9 sayısıyla biten lisans levhalarını döndürmek için bir filtre oluşturulabilir.

**Giriş**:

| Marka | License_plate | Saat |
| --- | --- | --- |
| Make1 |ABC-123 |2015-01-01T00:00:01.0000000 Z |
| Make2 |AAA-999 |2015-01-01T00:00:02.0000000 Z |
| Make3 |ABC-369 |2015-01-01T00:00:03.0000000 Z |

**Çıkış**:

| Marka | License_plate | Saat |
| --- | --- | --- |
| Make2 |AAA-999 |2015-01-01T00:00:02.0000000 Z |
| Make3 |ABC-369 |2015-01-01T00:00:03.0000000 Z |

**Sorgu**:

```SQL
SELECT
    *
FROM
    Input TIMESTAMP BY Time
WHERE
    License_plate LIKE 'A%9'
```

**License_plate** alan değerini denetlemek için **LIKE** ifadesini kullanın. ' A ' harfiyle başlamalı, ardından 9 sayısıyla biten sıfır veya daha fazla karakter dizesi içermelidir.

## <a name="calculation-over-past-events"></a>Geçmiş olaylar üzerinde hesaplama

**Lag** işlevi, bir zaman penceresi içinde geçmiş olaylara bakmak ve bunları geçerli olaya göre karşılaştırmak için kullanılabilir. Örneğin, geçerli araba, ücretli olarak geçen son arabadan farklıysa, bu marka için de çıktı alınabilir.

**Giriş**:

| Marka | Saat |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |
| Make2 |2015-01-01T00:00:02.0000000 Z |

**Çıkış**:

| Marka | Saat |
| --- | --- |
| Make2 |2015-01-01T00:00:02.0000000 Z |

**Sorgu**:

```SQL
SELECT
    Make,
    Time
FROM
    Input TIMESTAMP BY Time
WHERE
    LAG(Make, 1) OVER (LIMIT DURATION(minute, 1)) <> Make
```

Giriş akışına bir olay geri getirmek için, *Oluştur* değerini alarak ve bunu geçerli olayın *Make* değeri ile karşılaştırarak ve olayı çıkış olarak bir **olay öğesine göz** atın.

Daha fazla bilgi için, [**lag**](/stream-analytics-query/lag-azure-stream-analytics)adresine bakın.

## <a name="return-the-last-event-in-a-window"></a>Penceredeki son olayı döndürür

Olaylar, sistem tarafından gerçek zamanlı olarak tüketildiği için, bir olayın o zaman penceresinde gelmesi için en son bir olay olacağını belirleyemeyen bir işlev yoktur. Bu işlemi gerçekleştirmek için, giriş akışının bir olay zamanının, o penceredeki tüm olaylar için en uzun süredir olması gerekir.

**Giriş**:

| License_plate | Marka | Saat |
| --- | --- | --- |
| DXE 5291 |Make1 |2015-07-27T00:00:05.0000000 Z |
| YıLZK 5704 |Make3 |2015-07-27T00:02:17.0000000 Z |
| RMV 8282 |Make1 |2015-07-27T00:05:01.0000000 Z |
| YıLHN 6970 |Make2 |2015-07-27T00:06:00.0000000 Z |
| VFE 1616 |Make2 |2015-07-27T00:09:31.0000000 Z |
| QYıF 9358 |Make1 |2015-07-27T00:12:02.0000000 Z |
| MDR 6128 |Make4 |2015-07-27T00:13:45.0000000 Z |

**Çıkış**:

| License_plate | Marka | Saat |
| --- | --- | --- |
| VFE 1616 |Make2 |2015-07-27T00:09:31.0000000 Z |
| MDR 6128 |Make4 |2015-07-27T00:13:45.0000000 Z |

**Sorgu**:

```SQL
WITH LastInWindow AS
(
    SELECT 
        MAX(Time) AS LastEventTime
    FROM 
        Input TIMESTAMP BY Time
    GROUP BY 
        TumblingWindow(minute, 10)
)

SELECT 
    Input.License_plate,
    Input.Make,
    Input.Time
FROM
    Input TIMESTAMP BY Time 
    INNER JOIN LastInWindow
    ON DATEDIFF(minute, Input, LastInWindow) BETWEEN 0 AND 10
    AND Input.Time = LastInWindow.LastEventTime
```

Sorgudaki ilk adım, bu pencerenin son olayının zaman damgası olan 10 dakikalık Windows 'da en fazla zaman damgasını bulur. İkinci adım, her penceredeki son damgalar ile eşleşen olayı bulmak için ilk sorgunun sonuçlarını orijinal akışa birleştirir. 

**DATEDIFF** , Iki tarih saat alanı arasındaki zaman farkını karşılaştıran ve döndüren tarihe özgü bir işlevdir. daha fazla bilgi için [date işlevlerine](/stream-analytics-query/date-and-time-functions-azure-stream-analytics)bakın.

Akışlara katılma hakkında daha fazla bilgi için bkz. [**JOIN**](/stream-analytics-query/join-azure-stream-analytics).

## <a name="data-aggregation-over-time"></a>Zaman içinde veri toplama

Bir zaman penceresinde bilgileri hesaplamak için, veriler birlikte toplanabilir. Bu örnekte, bir sayı her bir otomobil yapması için son 10 saniyelik süre içinde hesaplanır.

**Giriş**:

| Marka | Saat | Ağırlık |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |1000 |
| Make1 |2015-01-01T00:00:02.0000000 Z |2000 |
| Make2 |2015-01-01T00:00:04.0000000 Z |1500 |

**Çıkış**:

| Marka | Sayı |
| --- | --- |
| Make1 | 2 |
| Make2 | 1 |

**Sorgu**:

```SQL
SELECT
    Make,
    COUNT(*) AS Count
FROM
    Input TIMESTAMP BY Time
GROUP BY
    Make,
    TumblingWindow(second, 10)
```

Bu toplama, otomobilleri her 10 saniyede *bir yaparak gruplandırır* ve sayar. Çıktı, ücretli olarak gelen otomobillerin *marka* ve *sayısına* sahiptir.

TumblingWindow, olayları birlikte gruplamak için kullanılan bir Pencereleme işlevidir. Bir toplama, tüm gruplanmış olaylar üzerine uygulanabilir. Daha fazla bilgi için bkz. [Pencereleme işlevleri](stream-analytics-window-functions.md).

Toplama hakkında daha fazla bilgi için bkz. [toplama işlevleri](/stream-analytics-query/aggregate-functions-azure-stream-analytics).

## <a name="periodically-output-values"></a>Düzenli aralıklarla çıkış değerleri

Düzensiz veya eksik olaylar söz konusu olduğunda, daha seyrek bir veri girişinden düzenli bir zaman aralığı çıkışı oluşturulabilir. Örneğin, en son görülen veri noktasını raporlayan her 5 saniyede bir olay oluşturun.

**Giriş**:

| Saat | Değer |
| --- | --- |
| "2014-01-01T06:01:00" |1 |
| "2014-01-01T06:01:05" |2 |
| "2014-01-01T06:01:10" |3 |
| "2014-01-01T06:01:15" |4 |
| "2014-01-01T06:01:30" |5 |
| "2014-01-01T06:01:35" |6 |

**Çıkış (ilk 10 satır)**:

| Window_end | Last_event. Işınızda | Last_event. Deeri |
| --- | --- | --- |
| 2014-01-01T14:01:00.000 Z |2014-01-01T14:01:00.000 Z |1 |
| 2014-01-01T14:01:05.000 Z |2014-01-01T14:01:05.000 Z |2 |
| 2014-01-01T14:01:10.000 Z |2014-01-01T14:01:10.000 Z |3 |
| 2014-01-01T14:01:15.000 Z |2014-01-01T14:01:15.000 Z |4 |
| 2014-01-01T14:01:20.000 Z |2014-01-01T14:01:15.000 Z |4 |
| 2014-01-01T14:01:25.000 Z |2014-01-01T14:01:15.000 Z |4 |
| 2014-01-01T14:01:30.000 Z |2014-01-01T14:01:30.000 Z |5 |
| 2014-01-01T14:01:35.000 Z |2014-01-01T14:01:35.000 Z |6 |
| 2014-01-01T14:01:40.000 Z |2014-01-01T14:01:35.000 Z |6 |
| 2014-01-01T14:01:45.000 Z |2014-01-01T14:01:35.000 Z |6 |

**Sorgu**:

```SQL
SELECT
    System.Timestamp() AS Window_end,
    TopOne() OVER (ORDER BY Time DESC) AS Last_event
FROM
    Input TIMESTAMP BY Time
GROUP BY
    HOPPINGWINDOW(second, 300, 5)
```

Bu sorgu, her 5 saniyede bir olay oluşturur ve daha önce alınan son olayı çıkarır. **Hoppingwindow** süresi, sorgunun en son olayı bulmak için ne kadar doğru göründüğünü belirler.

Daha fazla bilgi için bkz. [hopping penceresi](/stream-analytics-query/hopping-window-azure-stream-analytics).

## <a name="correlate-events-in-a-stream"></a>Bir akıştaki olayları ilişkilendirme

Aynı akıştaki olayların bağıntılandırgetirilmesi, **gecikme** işlevi kullanılarak geçmiş olaylara bakarak yapılabilir. Örneğin *, aynı kaynaktan* gelen iki araba, son 90 saniye boyunca ücretsiz olarak her seferinde bir çıktı oluşturulabilir.

**Giriş**:

| Marka | License_plate | Saat |
| --- | --- | --- |
| Make1 |ABC-123 |2015-01-01T00:00:01.0000000 Z |
| Make1 |AAA-999 |2015-01-01T00:00:02.0000000 Z |
| Make2 |DEF-987 |2015-01-01T00:00:03.0000000 Z |
| Make1 |GHI-345 |2015-01-01T00:00:04.0000000 Z |

**Çıkış**:

| Marka | Saat | Current_car_license_plate | First_car_license_plate | First_car_time |
| --- | --- | --- | --- | --- |
| Make1 |2015-01-01T00:00:02.0000000 Z |AAA-999 |ABC-123 |2015-01-01T00:00:01.0000000 Z |

**Sorgu**:

```SQL
SELECT
    Make,
    Time,
    License_plate AS Current_car_license_plate,
    LAG(License_plate, 1) OVER (LIMIT DURATION(second, 90)) AS First_car_license_plate,
    LAG(Time, 1) OVER (LIMIT DURATION(second, 90)) AS First_car_time
FROM
    Input TIMESTAMP BY Time
WHERE
    LAG(Make, 1) OVER (LIMIT DURATION(second, 90)) = Make
```

**Lag** işlevi, giriş akışına bir olay geri bakabilir ve bunu geçerli olayın *Make* değeri ile karşılaştırarak *Oluştur* değerini alabilir.  Koşul karşılandığında, önceki olayın verileri **Select** deyimindeki **lag** kullanılarak yansıtılmalıdır.

Daha fazla bilgi için, [lag](/stream-analytics-query/lag-azure-stream-analytics)adresine bakın.

## <a name="detect-the-duration-between-events"></a>Olaylar arasındaki süreyi Algıla

Bir olayın süresi, son olay alındıktan sonra son başlangıç olayına bakarak hesaplanabilir. Bu sorgu, bir kullanıcının bir sayfada veya özellikte harcadığı zamanı belirlemede yararlı olabilir.

**Giriş**:  

| Kullanıcı | Özellik | Olay | Saat |
| --- | --- | --- | --- |
| user@location.com |RightMenu |Başlangıç |2015-01-01T00:00:01.0000000 Z |
| user@location.com |RightMenu |End |2015-01-01T00:00:08.0000000 Z |

**Çıkış**:  

| Kullanıcı | Özellik | Süre |
| --- | --- | --- |
| user@location.com |RightMenu |7 |

**Sorgu**:

```SQL
SELECT
    [user],
    feature,
    DATEDIFF(
        second,
        LAST(Time) OVER (PARTITION BY [user], feature LIMIT DURATION(hour, 1) WHEN Event = 'start'),
        Time) as duration
FROM input TIMESTAMP BY Time
WHERE
    Event = 'end'
```

**Son** işlev, belirli bir koşul içindeki son olayı almak için kullanılabilir. Bu örnekte, koşul, Başlat türünde bir olaydır ve arama bölümüne Kullanıcı ve özellik **tarafından** bölümleniyor. Bu şekilde, başlangıç olayını ararken her kullanıcı ve özellik bağımsız olarak değerlendirilir. **Süre sınırı** , bitiş ve başlangıç olayları arasında geri arama süresini 1 saat olarak sınırlandırır.

## <a name="count-unique-values"></a>Benzersiz değerleri say

**Count** ve **DISTINCT** , bir zaman penceresi içinde akışta görünen benzersiz alan değerlerinin sayısını saymak için kullanılabilir. 2 saniyelik bir pencerede ücretli stand üzerinden kaç *tane benzersiz araba* olduğunu hesaplamak için bir sorgu oluşturulabilir.

**Giriş**:

| Marka | Saat |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |
| Make1 |2015-01-01T00:00:02.0000000 Z |
| Make2 |2015-01-01T00:00:01.0000000 Z |
| Make2 |2015-01-01T00:00:02.0000000 Z |
| Make2 |2015-01-01T00:00:03.0000000 Z |

**Çıktıların**

| Count_make | Saat |
| --- | --- |
| 2 |2015-01-01T00:00:02.000 Z |
| 1 |2015-01-01T00:00:04.000 Z |

**Sorgu:**

```SQL
SELECT
     COUNT(DISTINCT Make) AS Count_make,
     System.TIMESTAMP() AS Time
FROM Input TIMESTAMP BY TIME
GROUP BY 
     TumblingWindow(second, 2)
```

**Count (DISTINCT Make)** , bir zaman penceresi içindeki **Make** sütunundaki ayrı değerlerin sayısını döndürür.
Daha fazla bilgi için [ **Count** toplama işlevine](/stream-analytics-query/count-azure-stream-analytics)bakın.

## <a name="retrieve-the-first-event-in-a-window"></a>Penceredeki ilk olayı alma

**IsFirst** , zaman penceresinde ilk olayı almak için kullanılabilir. Örneğin, her 10 dakikalık aralıktaki ilk araba bilgisinin çıktısı.

**Giriş**:

| License_plate | Marka | Saat |
| --- | --- | --- |
| DXE 5291 |Make1 |2015-07-27T00:00:05.0000000 Z |
| YıLZK 5704 |Make3 |2015-07-27T00:02:17.0000000 Z |
| RMV 8282 |Make1 |2015-07-27T00:05:01.0000000 Z |
| YıLHN 6970 |Make2 |2015-07-27T00:06:00.0000000 Z |
| VFE 1616 |Make2 |2015-07-27T00:09:31.0000000 Z |
| QYıF 9358 |Make1 |2015-07-27T00:12:02.0000000 Z |
| MDR 6128 |Make4 |2015-07-27T00:13:45.0000000 Z |

**Çıkış**:

| License_plate | Marka | Saat |
| --- | --- | --- |
| DXE 5291 |Make1 |2015-07-27T00:00:05.0000000 Z |
| QYıF 9358 |Make1 |2015-07-27T00:12:02.0000000 Z |

**Sorgu**:

```SQL
SELECT 
    License_plate,
    Make,
    Time
FROM 
    Input TIMESTAMP BY Time
WHERE 
    IsFirst(minute, 10) = 1
```

**Islk** Ayrıca verileri bölümleyebilir ve her 10 dakikalık *aralıkta bulunan her* bir otomobil için ilk olayı hesaplayabilir.

**Çıkış**:

| License_plate | Marka | Saat |
| --- | --- | --- |
| DXE 5291 |Make1 |2015-07-27T00:00:05.0000000 Z |
| YıLZK 5704 |Make3 |2015-07-27T00:02:17.0000000 Z |
| YıLHN 6970 |Make2 |2015-07-27T00:06:00.0000000 Z |
| QYıF 9358 |Make1 |2015-07-27T00:12:02.0000000 Z |
| MDR 6128 |Make4 |2015-07-27T00:13:45.0000000 Z |

**Sorgu**:

```SQL
SELECT 
    License_plate,
    Make,
    Time
FROM 
    Input TIMESTAMP BY Time
WHERE 
    IsFirst(minute, 10) OVER (PARTITION BY Make) = 1
```

Daha fazla bilgi için [**IsFirst**](/stream-analytics-query/isfirst-azure-stream-analytics)öğesine bakın.

## <a name="remove-duplicate-events-in-a-window"></a>Penceredeki yinelenen olayları kaldırma

Belirli bir zaman penceresinde olaylar üzerinde ortalamaları hesaplama gibi bir işlem gerçekleştirirken, yinelenen olayların filtrelenmelidir. Aşağıdaki örnekte ikinci olay, birincisinin yinelemesidir.

**Giriş**:  

| DeviceId | Saat | Öznitelik | Değer |
| --- | --- | --- | --- |
| 1 |2018-07-27T00:00:01.0000000 Z |Sıcaklık |50 |
| 1 |2018-07-27T00:00:01.0000000 Z |Sıcaklık |50 |
| 2 |2018-07-27T00:00:01.0000000 Z |Sıcaklık |40 |
| 1 |2018-07-27T00:00:05.0000000 Z |Sıcaklık |60 |
| 2 |2018-07-27T00:00:05.0000000 Z |Sıcaklık |50 |
| 1 |2018-07-27T00:00:10.0000000 Z |Sıcaklık |100 |

**Çıkış**:  

| Averagedeğeri | DeviceId |
| --- | --- |
| 70 | 1 |
|45 | 2 |

**Sorgu**:

```SQL
With Temp AS (
SELECT
    COUNT(DISTINCT Time) AS CountTime,
    Value,
    DeviceId
FROM
    Input TIMESTAMP BY Time
GROUP BY
    Value,
    DeviceId,
    SYSTEM.TIMESTAMP()
)

SELECT
    AVG(Value) AS AverageValue, DeviceId
INTO Output
FROM Temp
GROUP BY DeviceId,TumblingWindow(minute, 5)
```

**Count (ayrık süre)** bir zaman penceresi içindeki zaman sütunundaki ayrı değerlerin sayısını döndürür. Daha sonra, ilk adımın çıktısı, yinelenenleri atarak cihaz başına ortalamayı hesaplamak için kullanılabilir.

Daha fazla bilgi için bkz. [Count (ayrık süre)](/stream-analytics-query/count-azure-stream-analytics).

## <a name="specify-logic-for-different-casesvalues-case-statements"></a>Farklı durumlar/değerler için mantık belirtme (CASE deyimleri)

**Case** deyimleri, farklı alanlar için belirli bir ölçüte göre farklı hesaplamalar sağlayabilir. Örneğin, *Make1* ve Lane ' B ' öğesinin otomobilleri Için ' A ' kulvarı diğer herhangi bir marka için atayın.

**Giriş**:

| Marka | Saat |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |
| Make2 |2015-01-01T00:00:02.0000000 Z |
| Make2 |2015-01-01T00:00:03.0000000 Z |

**Çıkış**:

| Marka |Dispatch_to_lane | Saat |
| --- | --- | --- |
| Make1 |A |2015-01-01T00:00:01.0000000 Z |
| Make2 |Kenarı |2015-01-01T00:00:02.0000000 Z |

**Çözüm**:

```SQL
SELECT
    Make
    CASE
        WHEN Make = "Make1" THEN "A"
        ELSE "B"
    END AS Dispatch_to_lane,
    System.TimeStamp() AS Time
FROM
    Input TIMESTAMP BY Time
```

**Case** ifadesi, sonucunu tespit etmek için bir ifadeyi basit ifadeler kümesiyle karşılaştırır. Bu örnekte, *Make1* için Araçlar ' A ' yoluna dağıtılır ve diğer bir deyişle, başka bir yapa ait Araçlar ' B ' yoluna atanır.

Daha fazla bilgi için bkz. [case ifadesi](/stream-analytics-query/case-azure-stream-analytics).

## <a name="data-conversion"></a>Veri dönüştürme

Veri **cast** yöntemi kullanılarak gerçek zamanlı olarak ayarlanabilir. Örneğin, araba ağırlığı, **nvarchar (max)** türünden **bigint** türüne dönüştürülebilir ve sayısal bir hesaplamada kullanılabilir.

**Giriş**:

| Marka | Saat | Ağırlık |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |"1000" |
| Make1 |2015-01-01T00:00:02.0000000 Z |"2000" |

**Çıkış**:

| Marka | Ağırlık |
| --- | --- |
| Make1 |3000 |

**Sorgu**:

```SQL
SELECT
    Make,
    SUM(CAST(Weight AS BIGINT)) AS Weight
FROM
    Input TIMESTAMP BY Time
GROUP BY
    Make,
    TumblingWindow(second, 10)
```

Veri türünü belirtmek için bir **cast** ifadesini kullanın. Veri türlerinde desteklenen veri türleri listesine bakın [(Azure Stream Analytics)](/stream-analytics-query/data-types-azure-stream-analytics).

[Veri dönüştürme işlevleri](/stream-analytics-query/conversion-functions-azure-stream-analytics)hakkında daha fazla bilgi için.

## <a name="detect-the-duration-of-a-condition"></a>Bir koşulun süresini Algıla

Birden çok olayla yayılan koşullar için, bu koşulun süresini tanımlamak üzere **lag** işlevi kullanılabilir. Örneğin, bir hata, tüm otomobillerin yanlış ağırlığa (20.000 sterlini üzerinde) sahip olduğunu ve bu hatanın süresinin hesaplanması gerektiğini varsayalım.

**Giriş**:

| Marka | Saat | Ağırlık |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |2000 |
| Make2 |2015-01-01T00:00:02.0000000 Z |25000 |
| Make1 |2015-01-01T00:00:03.0000000 Z |26000 |
| Make2 |2015-01-01T00:00:04.0000000 Z |25000 |
| Make1 |2015-01-01T00:00:05.0000000 Z |26000 |
| Make2 |2015-01-01T00:00:06.0000000 Z |25000 |
| Make1 |2015-01-01T00:00:07.0000000 Z |26000 |
| Make2 |2015-01-01T00:00:08.0000000 Z |2000 |

**Çıkış**:

| Start_fault | End_fault |
| --- | --- |
| 2015-01-01T00:00:02.000 Z |2015-01-01T00:00:07.000 Z |

**Sorgu**:

```SQL
WITH SelectPreviousEvent AS
(
SELECT
    *,
    LAG([time]) OVER (LIMIT DURATION(hour, 24)) as previous_time,
    LAG([weight]) OVER (LIMIT DURATION(hour, 24)) as previous_weight
FROM input TIMESTAMP BY [time]
)

SELECT 
    LAG(time) OVER (LIMIT DURATION(hour, 24) WHEN previous_weight < 20000 ) [Start_fault],
    previous_time [End_fault]
FROM SelectPreviousEvent
WHERE
    [weight] < 20000
    AND previous_weight > 20000
```
İlk **Select** ifadesinin geçerli ağırlık ölçüsünü önceki ölçümle karşılıklı olarak ilişkilendirir ve geçerli ölçümle birlikte yansıtıyorsunuz. İkinci **seçim** , *previous_weight* 20000 ' den küçük olan son olaya geri dönerek geçerli ağırlığın 20000 ' den küçük olduğu ve geçerli olayın *previous_weight* 20000.

End_fault, önceki olayın hatalı olduğu ve Start_fault önce son hatalı olmayan olaydır.

## <a name="process-events-with-independent-time-substreams"></a>Bağımsız bir zamana sahip olayları işle (alt akışlar)

Olaylar, Event üreticileri, bölümler arasındaki saat eğetkinlikleri veya ağ gecikmesi arasındaki saat eğlemeleri nedeniyle geçmiş veya sıra dışı olabilir.
Örneğin, *tollıd* 2 için cihaz saati, *tollıd* 1 ' in arkasında beş saniyedir ve *tollıd* 3 Için cihaz saati, *tollıd* 1 ' in arkasında on saniyedir. Hesaplama her ücretli için bağımsız olarak gerçekleşebilir ve yalnızca kendi saat verileri zaman damgası olarak ele alabilir.

**Giriş**:

| Licenselevha | Marka | Saat | Tollıd |
| --- | --- | --- | --- |
| DXE 5291 |Make1 |2015-07-27T00:00:01.0000000 Z | 1 |
| YıLHN 6970 |Make2 |2015-07-27T00:00:05.0000000 Z | 1 |
| QYıF 9358 |Make1 |2015-07-27T00:00:01.0000000 Z | 2 |
| GXF 9462 |Make3 |2015-07-27T00:00:04.0000000 Z | 2 |
| VFE 1616 |Make2 |2015-07-27T00:00:10.0000000 Z | 1 |
| RMV 8282 |Make1 |2015-07-27T00:00:03.0000000 Z | 3 |
| MDR 6128 |Make3 |2015-07-27T00:00:11.0000000 Z | 2 |
| YıLZK 5704 |Make4 |2015-07-27T00:00:07.0000000 Z | 3 |

**Çıkış**:

| Tollıd | Count |
| --- | --- |
| 1 | 2 |
| 2 | 2 |
| 1 | 1 |
| 3 | 1 |
| 2 | 1 |
| 3 | 1 |

**Sorgu**:

```SQL
SELECT
      TollId,
      COUNT(*) AS Count
FROM input
      TIMESTAMP BY Time OVER TollId
GROUP BY TUMBLINGWINDOW(second, 5), TollId
```

Yan tümce **üzerinden zaman damgası** , alt akışlar kullanılarak her bir cihaz zaman çizelgesine göre görünür. Her bir *Tollıd* için çıkış olayı, hesaplandıkları sırada oluşturulur. Bu, olayların, tüm cihazlar aynı saat üzerinde olduğu gibi yeniden sıralamak yerine her bir *tollıd* 'e göre olduğu anlamına gelir.

Daha fazla bilgi için bkz. [zaman damgasına](/stream-analytics-query/timestamp-by-azure-stream-analytics#over-clause-interacts-with-event-ordering).

## <a name="session-windows"></a>Oturum pencereleri

Oturum penceresi, belirli bir süre sonra veya pencere en uzun süreye ulaşırsa, olay gerçekleştiğinde Genişlemeden ve hesaplama için kapanmaya devam eden bir penceredir.
Bu pencere, Kullanıcı etkileşimi verileri hesaplanırken özellikle faydalıdır. Bir pencere, bir Kullanıcı sistemle etkileşime başladığında başlar ve başka hiçbir olay gözlemleniyorsa, yani Kullanıcı etkileşime göre durmuşsa kapatır.
Örneğin, bir Kullanıcı, tıklama sayısının günlüğe kaydedildiği bir Web sayfasıyla etkileşim kurmuştur, kullanıcının siteyle ne kadar süre içinde işlem açtığını öğrenmek için bir oturum penceresi kullanılabilir.

**Giriş**:

| User_id | Saat | URL |
| --- | --- | --- |
| 0 | 2017-01-26T00:00:00.0000000 Z | "www.example.com/a.html" |
| 0 | 2017-01-26T00:00:20.0000000 Z | "www.example.com/b.html" |
| 1 | 2017-01-26T00:00:55.0000000 Z | "www.example.com/c.html" |
| 0 | 2017-01-26T00:01:10.0000000 Z | "www.example.com/d.html" |
| 1 | 2017-01-26T00:01:15.0000000 Z | "www.example.com/e.html" |

**Çıkış**:

| User_id | StartTime | EndTime | Duration_in_seconds |
| --- | --- | --- | --- |
| 0 | 2017-01-26T00:00:00.0000000 Z | 2017-01-26T00:01:10.0000000 Z | 70 |
| 1 | 2017-01-26T00:00:55.0000000 Z | 2017-01-26T00:01:15.0000000 Z | 20 |

**Sorgu**:

``` SQL
SELECT
    user_id,
    MIN(time) as StartTime,
    MAX(time) as EndTime,
    DATEDIFF(second, MIN(time), MAX(time)) AS duration_in_seconds
FROM input TIMESTAMP BY time
GROUP BY
    user_id,
    SessionWindow(minute, 1, 60) OVER (PARTITION BY user_id)
```

**Seç seçeneği** , etkileşim süresiyle birlikte Kullanıcı etkileşimi ile ilgili verileri sağlar. Verileri kullanıcıya göre gruplandırma ve 1 dakika içinde hiçbir etkileşim gerçekleşmez, en fazla 60 dakikalık pencere boyutu olan bir **sessionwindow** .

**Sessionwindow** hakkında daha fazla bilgi Için [oturum penceresine](/stream-analytics-query/session-window-azure-stream-analytics) bakın.

## <a name="language-extensibility-with-user-defined-function-in-javascript-and-c"></a>JavaScript ve C 'de Kullanıcı tanımlı Işlev ile dil genişletilebilirliği #

Azure Stream Analytics sorgu dili, JavaScript veya C# dilinde yazılmış özel işlevlerle genişletilebilir. Kullanıcı tanımlı Işlevler (UDF), **SQL** dili kullanılarak kolayca belirtilemez özel/karmaşık hesaplamalardır. Bu UDF 'ler bir kez tanımlanabilir ve bir sorgu içinde birden çok kez kullanılabilir. Örneğin, bir UDF onaltılık bir *nvarchar (max)* değerini bir *bigint* değerine dönüştürmek için kullanılabilir.

**Giriş**:

| Device_id | Onaltıdeğer |
| --- | --- |
| 1 | B4 |
| 2 | "11B" |
| 3 | "121" |

**Çıkış**:

| Device_id | Ondalık |
| --- | --- |
| 1 | 180 |
| 2 | 283 |
| 3 | 289 |

```JavaScript
function hex2Int(hexValue){
    return parseInt(hexValue, 16);
}
```

```C#
public static class MyUdfClass {
    public static long Hex2Int(string hexValue){
        return int.Parse(hexValue, System.Globalization.NumberStyles.HexNumber);
    }
}
```

```SQL
SELECT
    Device_id,
    udf.Hex2Int(HexValue) AS Decimal
From
    Input
```

Kullanıcı tanımlı Işlevi, tüketilen her olayda onaltılık değerden büyük *tamsayı* değerini hesaplacaktır.

Daha fazla bilgi için [JavaScript](./stream-analytics-javascript-user-defined-functions.md) ve [C#](./stream-analytics-edge-csharp-udf.md)bölümüne bakın.

## <a name="advanced-pattern-matching-with-match_recognize"></a>MATCH_RECOGNIZE ile eşleşme gelişmiş desenler

**MATCH_RECOGNIZE** , bir olay dizisini iyi tanımlanmış bir normal ifade düzenine eşleştirmek için kullanılabilen gelişmiş bir model eşleştirme mekanizmasıdır.
Örneğin, bir ATM, hatalara karşı gerçek zamanlı olarak izlenmekte, ancak yöneticiye bildirilmesi gereken iki ardışık uyarı iletisi varsa, bu, ATM 'nin çalışması sırasında.

**Giriş**:

| ATM_id | Operation_id | Return_Code | Saat |
| --- | --- | --- | --- |
| 1 | "PIN girme" | Başarılı | 2017-01-26T00:10:00.0000000 Z |
| 2 | "Para yuvası açılıyor" | Başarılı | 2017-01-26T00:10:07.0000000 Z |
| 2 | "Para yuvası kapatılıyor" | Başarılı | 2017-01-26T00:10:11.0000000 Z |
| 1 | "Geri çekme miktarını girme" | Başarılı | 2017-01-26T00:10:08.0000000 Z |
| 1 | "Para yuvası açılıyor" | Warning | 2017-01-26T00:10:14.0000000 Z |
| 1 | "Bank bilançosu yazdırılıyor" | Warning | 2017-01-26T00:10:19.0000000 Z |

**Çıkış**:

| ATM_id | First_Warning_Operation_id | Warning_Time |
| --- | --- | --- |
| 1 | "Para yuvası açılıyor" | 2017-01-26T00:10:14.0000000 Z |

```SQL
SELECT *
FROM input TIMESTAMP BY time OVER ATM_id
MATCH_RECOGNIZE (
    LIMIT DURATION(minute, 1)
    PARTITON BY ATM_id
    MEASURES
        First(Warning.ATM_id) AS ATM_id,
        First(Warning.Operation_Id) AS First_Warning_Operation_id,
        First(Warning.Time) AS Warning_Time
    AFTER MATCH SKIP TO NEXT ROW
    PATTERN (Success* Warning{2,})
    DEFINE
        Success AS Succes.Return_Code = 'Success',
        Failure AS Warning.Return_Code <> 'Success'
) AS patternMatch
```

Bu sorgu, en az iki ardışık hata olayını eşleştirir ve koşullar karşılandığında bir alarm oluşturur.
**Model** , eşleştirme üzerinde kullanılacak normal ifadeyi tanımlar, bu durumda, her türlü başarılı işlem ve sonrasında en az iki ardışık başarısızlık gelir.
Başarı ve başarısızlık Return_Code değeri kullanılarak tanımlanır ve koşul karşılandığında, **ölçümler** *ATM_id*, ilk uyarı işlemi ve ilk uyarı süresi ile birlikte gösterilir.

Daha fazla bilgi için [MATCH_RECOGNIZE](/stream-analytics-query/match-recognize-stream-analytics)bakın.

## <a name="geofencing-and-geospatial-queries"></a>Bölge sınırlaması ve jeo-uzamsal sorgular

Azure Stream Analytics, filo yönetimi, arttırıldığında paylaşımı, bağlantılı otomobil ve varlık izleme gibi senaryoları uygulamak için kullanılabilen yerleşik Jeo-uzamsal işlevler sağlar.
Jeo-uzamsal veriler, coğrafi JSON veya WKT biçimlerinde olay akışı veya başvuru verilerinin bir parçası olarak alınabilir.
Örneğin, Passport yazdırma için üretim makinelerinde uzmanlaşmış bir şirkettir, makinelerini kamu ve sarf amaçlı olarak kiralayın. Bu makinelerin konumu yoğun olarak, Passport 'ların sahteciliği ve olası kullanımı ortadan kaldırmak için yoğun olarak denetlenir. Her makine bir GPS izleyiciye uydurulur, bu bilgiler bir Azure Stream Analytics işine geri getirilir.
Üretim, bu makinelerin konumunu izlemek ve bunlardan biri yetkili bir alandan ayrıldığında, bu şekilde uzaktan devre dışı bırakabilmeleri, uyarı yetkilileri ve ekipmanları alabilmesi için uyarı almak istiyor.

**Giriş**:

| Equipment_id | Equipment_current_location | Saat |
| --- | --- | --- |
| 1 | "NOKTA (-122.13288797982818 47.64082002051315)" | 2017-01-26T00:10:00.0000000 Z |
| 1 | "NOKTA (-122.13307252987875 47.64081350934929)" | 2017-01-26T00:11:00.0000000 Z |
| 1 | "NOKTA (-122.13308862313283 47.6406508603241)" | 2017-01-26T00:12:00.0000000 Z |
| 1 | "NOKTA (-122.13341048821462 47.64043760861279)" | 2017-01-26T00:13:00.0000000 Z |

**Başvuru veri girişi**:

| Equipment_id | Equipment_lease_location |
| --- | --- |
| 1 | "ÇOKGEN ((-122.13326028450979 47.6409833866794,-122.13261655434621 47.6409833866794,-122.13261655434621 47.64061471602751,-122.13326028450979 47.64061471602751,-122.13326028450979 47.6409833866794))" |

**Çıkış**:

| Equipment_id | Equipment_alert_location | Saat |
| --- | --- | --- |
| 1 | "NOKTA (-122.13341048821462 47.64043760861279)" | 2017-01-26T00:13:00.0000000 Z |

```SQL
SELECT
    input.Equipment_id AS Equipment_id,
    input.Equipment_current_location AS Equipment_current_location,
    input.Time AS Time
FROM input TIMESTAMP BY time
JOIN
    referenceInput 
    ON input.Equipment_id = referenceInput.Equipment_id
    WHERE 
        ST_WITHIN(input.Equipment_currenct_location, referenceInput.Equipment_lease_location) = 1
```

Sorgu, bir makine izin verilen bölge grubunu terk ettiğinde uyarı almak için üreticinin makineler konumunu otomatik olarak izlemesini sağlar. Yerleşik Jeo-uzamsal işlevi, kullanıcıların üçüncü taraf kitaplıklar olmadan sorgu içinde GPS verisi kullanmasına olanak sağlar.

Daha fazla bilgi için, Azure Stream Analytics makalesinde [Bölge sınırlaması ve jeo uzamsal toplama senaryolarına](geospatial-scenarios.md) bakın.

## <a name="get-help"></a>Yardım alın

Daha fazla yardım için, [Azure Stream Analytics Için Microsoft Q&soru sayfasını](/answers/topics/azure-stream-analytics.html)deneyin.

## <a name="next-steps"></a>Sonraki adımlar
* [Azure Stream Analytics giriş](stream-analytics-introduction.md)
* [Azure Akış Analizi'ni kullanmaya başlama](stream-analytics-real-time-fraud-detection.md)
* [Azure Akış Analizi işlerini ölçeklendirme](stream-analytics-scale-jobs.md)
* [Azure Akış Analizi Sorgu Dili Başvurusu](/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Akış Analizi Yönetimi REST API'si Başvurusu](/rest/api/streamanalytics/)