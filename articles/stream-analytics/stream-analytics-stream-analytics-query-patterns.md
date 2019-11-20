---
title: Azure Stream Analytics ortak sorgu desenleri
description: Bu makalede, Azure Stream Analytics işlerinde yararlı olan birçok ortak sorgu deseni ve tasarımı açıklanmaktadır.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/16/2019
ms.openlocfilehash: 729385a2ce9feb6e69f9be29c2175b403093be3f
ms.sourcegitcommit: c556477e031f8f82022a8638ca2aec32e79f6fd9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68413371"
---
# <a name="query-examples-for-common-stream-analytics-usage-patterns"></a>Ortak Stream Analytics kullanım desenlerine yönelik sorgu örnekleri

Azure Stream Analytics sorguları SQL benzeri bir sorgu dilinde ifade edilir. Dil yapıları [Stream Analytics sorgu dili başvuru](/stream-analytics-query/stream-analytics-query-language-reference) kılavuzunda belgelenmiştir. 

Sorgu tasarımı, olay verilerini bir giriş akışından çıkış veri deposuna taşımak için basit geçiş mantığını hızlı bir şekilde ifade edebilir veya çeşitli zaman pencerelerinin toplamlarını hesaplamak için zengin kalıp eşleştirme ve zamana bağlı analizler [kullanarak bir IoT çözümü oluşturma Stream Analytics Kılavuzu kullanma](stream-analytics-build-an-iot-solution-using-stream-analytics.md) . Akış olaylarını birleştirmek için birden çok girişe veri katılabilir ve olay değerlerini zenginleştirmek için statik başvuru verilerine yönelik aramalar yapabilirsiniz. Ayrıca, birden fazla çıkışına veri yazabilirsiniz.

Bu makalede, gerçek dünyada senaryolar temelinde birkaç ortak sorgu desenlerine yönelik çözümler özetlenmektedir.

## <a name="work-with-complex-data-types-in-json-and-avro"></a>JSON ve AVRO'da karmaşık Veri Türleri ile çalışma

Azure Stream Analytics CSV, JSON ve avro veri biçimlerinde olayları işlemeyi destekler.

Hem JSON hem de avro, iç içe geçmiş nesneler (kayıtlar) veya diziler gibi karmaşık türler içerebilir. Bu karmaşık veri türleriyle çalışma hakkında daha fazla bilgi için bkz. [JSON ve avro veri ayrıştırma](stream-analytics-parsing-json.md) makalesi.

## <a name="query-example-convert-data-types"></a>Sorgu örneği: Veri türlerini Dönüştür

**Açıklama**: Giriş akışındaki özellik türlerini tanımlayın. Örneğin, araba ağırlığı giriş akışında dizeler olarak geliyor ve **toplamı**gerçekleştirmek için **Int** 'e dönüştürülmesi gerekir.

**Giriş**:

| Yapın | Time | Ağırlık |
| --- | --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |"1000" |
| Honda |2015-01-01T00:00:02.0000000Z |"2000" |

**Çıkış**:

| Yapın | Ağırlık |
| --- | --- |
| Honda |3000 |

**Çözüm**:

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

**Açıklama**: Veri türünü belirtmek için **Ağırlık** alanındaki bir **cast** ifadesini kullanın. Veri türlerinde desteklenen veri türleri listesine bakın [(Azure Stream Analytics)](/stream-analytics-query/data-types-azure-stream-analytics).

## <a name="query-example-use-likenot-like-to-do-pattern-matching"></a>Sorgu örneği: LIKE ve LIKE stili eşleştirme için kullanın

**Açıklama**: Olaydaki bir alan değerinin belirli bir Düzenle eşleşip eşleşmediğini denetleyin.
Örneğin, sonucun, ile başlayan ve 9 ile biten lisans levhalarını döndürdüğünden emin olun.

**Giriş**:

| Yapın | LicensePlate | Time |
| --- | --- | --- |
| Honda |ABC-123 |2015-01-01T00:00:01.0000000Z |
| Toyota |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Nissan |ABC-369 |2015-01-01T00:00:03.0000000Z |

**Çıkış**:

| Yapın | LicensePlate | Time |
| --- | --- | --- |
| Toyota |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Nissan |ABC-369 |2015-01-01T00:00:03.0000000Z |

**Çözüm**:

```SQL
    SELECT
        *
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LicensePlate LIKE 'A%9'
```

**Açıklama**: **Licenselevha** alan değerini denetlemek için **LIKE** ifadesini kullanın. A harfi ile başlamalı, ardından sıfır veya daha fazla karakter dizesi olmalı ve ardından 9 sayısıyla bitmelidir. 

## <a name="query-example-specify-logic-for-different-casesvalues-case-statements"></a>Sorgu örneği: Farklı durumlar/değerler için mantık belirtme (CASE deyimleri)

**Açıklama**: Bir alan için belirli bir ölçüte göre farklı bir hesaplama sağlayın. Örneğin, 1 için özel bir durum ile aynı şekilde kaç otomobilin geçtiğini gösteren bir dize açıklaması sağlayın.

**Giriş**:

| Yapın | Time |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**Çıkış**:

| Carsgeçti | Time |
| --- | --- |
| 1 Honda dili |2015-01-01T00:00:10.0000000Z |
| 2 Toyotas |2015-01-01T00:00:10.0000000Z |

**Çözüm**:

```SQL
    SELECT
        CASE
            WHEN COUNT(*) = 1 THEN CONCAT('1 ', Make)
            ELSE CONCAT(CAST(COUNT(*) AS NVARCHAR(MAX)), ' ', Make, 's')
        END AS CarsPassed,
        System.TimeStamp() AS AsaTime
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)
```

**Açıklama**: **Case** ifadesi, sonucu tespit etmek için bir ifadeyi basit ifadeler kümesiyle karşılaştırır. Bu örnekte, araç 1 sayımla birlikte 1 ' den farklı bir dize açıklaması döndürdü ve 1 dışında bir sayı getirir.

## <a name="query-example-send-data-to-multiple-outputs"></a>Sorgu örneği: Verileri birden çok çıkışına gönder

**Açıklama**: Tek bir işten birden çok çıkış hedefi 'ne veri gönderme. Örneğin, eşik tabanlı bir uyarı için verileri analiz edin ve tüm olayları blob depolamaya arşivleyebilirsiniz.

**Giriş**:

| Yapın | Time |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Honda |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**Output1**:

| Yapın | Time |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Honda |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**Output2**:

| Yapın | Time | Count |
| --- | --- | --- |
| Toyota |2015-01-01T00:00:10.0000000Z |3 |

**Çözüm**:

```SQL
    SELECT
        *
    INTO
        ArchiveOutput
    FROM
        Input TIMESTAMP BY Time

    SELECT
        Make,
        System.TimeStamp() AS AsaTime,
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

**Açıklama**: **Into** yan tümcesi, bu deyimden verileri yazmak için çıkışların Stream Analytics belirtir. İlk sorgu, **ArchiveOutput**adlı bir çıktıya alınan verilerin bir geçişinden oluşur. İkinci sorgu bazı basit toplama ve filtreleme işlemlerini yapar ve sonuçları bir aşağı akış uyarı sistemine, **Alertoutput**'a gönderir.

Birden çok çıktı deyiminde ortak tablo ifadelerinin (örneğin **,** deyimler) sonuçlarını da yeniden kullanabileceğinizi unutmayın. Bu seçenekte, giriş kaynağına daha az okuyucu açma avantajı eklenmiştir.

Örneğin: 

```SQL
    WITH AllRedCars AS (
        SELECT
            *
        FROM
            Input TIMESTAMP BY Time
        WHERE
            Color = 'red'
    )
    SELECT * INTO HondaOutput FROM AllRedCars WHERE Make = 'Honda'
    SELECT * INTO ToyotaOutput FROM AllRedCars WHERE Make = 'Toyota'
```

## <a name="query-example-count-unique-values"></a>Sorgu örneği: Benzersiz değerleri say

**Açıklama**: Bir zaman penceresi içinde akışta görünen benzersiz alan değerlerinin sayısını say. Örneğin, arabaların kaç tane benzersiz olması, 2 saniyelik bir pencerede ücretli stand üzerinden mi geçer?

**Giriş**:

| Yapın | Time |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Honda |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**Çıkış:**

| CountMake | Time |
| --- | --- |
| 2 |2015-01-01T00:00:02.000Z |
| 1\. |2015-01-01T00:00:04.000Z |

**Çözümden**

```SQL
SELECT
     COUNT(DISTINCT Make) AS CountMake,
     System.TIMESTAMP() AS AsaTIME
FROM Input TIMESTAMP BY TIME
GROUP BY 
     TumblingWindow(second, 2)
```


**Açıklama:** 
**Count (DISTINCT Make)** , bir zaman penceresi içindeki **Make** sütunundaki ayrı değerlerin sayısını döndürür.

## <a name="query-example-determine-if-a-value-has-changed"></a>Sorgu örneği: Değerin değişip değişmediğini belirleme

**Açıklama**: Geçerli değerden farklı olup olmadığını öğrenmek için önceki değere bakın. Örneğin, bir önceki otomobilin, geçerli araba ile aynı anda BT yolunda mi olduğunu?

**Giriş**:

| Yapın | Time |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |

**Çıkış**:

| Yapın | Time |
| --- | --- |
| Toyota |2015-01-01T00:00:02.0000000Z |

**Çözüm**:

```SQL
    SELECT
        Make,
        Time
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LAG(Make, 1) OVER (LIMIT DURATION(minute, 1)) <> Make
```

**Açıklama**: Giriş  akışına bir olay geri göz atın ve **Make** değerini alın. Ardından, geçerli olaydaki **Make** değeri ile karşılaştırın ve farklı olmaları durumunda olayı çıkış.

## <a name="query-example-find-the-first-event-in-a-window"></a>Sorgu örneği: Penceredeki ilk olayı bulma

**Açıklama**: Her 10 dakikalık aralıkta ilk arabayı bulun.

**Giriş**:

| LicensePlate | Yapın | Time |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Ford |2015-07-27T00:02:17.0000000Z |
| RMV 8282 |Honda |2015-07-27T00:05:01.0000000Z |
| YILHN 6970 |Toyota |2015-07-27T00:06:00.0000000Z |
| VFE 1616 |Toyota |2015-07-27T00:09:31.0000000Z |
| QYIF 9358 |Honda |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000 Z |

**Çıkış**:

| LicensePlate | Yapın | Time |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| QYIF 9358 |Honda |2015-07-27T00:12:02.0000000Z |

**Çözüm**:

```SQL
    SELECT 
        LicensePlate,
        Make,
        Time
    FROM 
        Input TIMESTAMP BY Time
    WHERE 
        IsFirst(minute, 10) = 1
```

Şimdi sorunu değiştirip 10 dakikalık aralıklarla belirli bir işin ilk arabasını bulalim.

| LicensePlate | Yapın | Time |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Ford |2015-07-27T00:02:17.0000000Z |
| YILHN 6970 |Toyota |2015-07-27T00:06:00.0000000Z |
| QYIF 9358 |Honda |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000 Z |

**Çözüm**:

```SQL
    SELECT 
        LicensePlate,
        Make,
        Time
    FROM 
        Input TIMESTAMP BY Time
    WHERE 
        IsFirst(minute, 10) OVER (PARTITION BY Make) = 1
```

## <a name="query-example-find-the-last-event-in-a-window"></a>Sorgu örneği: Penceredeki son olayı bulma

**Açıklama**: Her 10 dakikalık aralıkta son arabayı bulun.

**Giriş**:

| LicensePlate | Yapın | Time |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Ford |2015-07-27T00:02:17.0000000Z |
| RMV 8282 |Honda |2015-07-27T00:05:01.0000000Z |
| YILHN 6970 |Toyota |2015-07-27T00:06:00.0000000Z |
| VFE 1616 |Toyota |2015-07-27T00:09:31.0000000Z |
| QYIF 9358 |Honda |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000 Z |

**Çıkış**:

| LicensePlate | Yapın | Time |
| --- | --- | --- |
| VFE 1616 |Toyota |2015-07-27T00:09:31.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000 Z |

**Çözüm**:

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
        Input.LicensePlate,
        Input.Make,
        Input.Time
    FROM
        Input TIMESTAMP BY Time 
        INNER JOIN LastInWindow
        ON DATEDIFF(minute, Input, LastInWindow) BETWEEN 0 AND 10
        AND Input.Time = LastInWindow.LastEventTime
```

**Açıklama**: Sorguda iki adım vardır. Birincisi, 10 dakikalık Windows 'da en son zaman damgasını bulur. İkinci adım, her penceredeki son damgalar ile eşleşen olayları bulmak için ilk sorgunun sonuçlarını orijinal akışa birleştirir. 

## <a name="query-example-locate-correlated-events-in-a-stream"></a>Sorgu örneği: Bağıntılı olayları bir akışta bulma

**Açıklama**: Bir akışta bağıntılı olayları bulun. Örneğin, en son 90 saniye içinde, aynı kaynaktan 2 ' den fazla araba mi girsin?

**Giriş**:

| Yapın | LicensePlate | Time |
| --- | --- | --- |
| Honda |ABC-123 |2015-01-01T00:00:01.0000000Z |
| Honda |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Toyota |DEF-987 |2015-01-01T00:00:03.0000000Z |
| Honda |GHI-345 |2015-01-01T00:00:04.0000000Z |

**Çıkış**:

| Yapın | Time | Currentcarlicenselevha | Firstcarlicenselevha | FirstCarTime |
| --- | --- | --- | --- | --- |
| Honda |2015-01-01T00:00:02.0000000Z |AAA-999 |ABC-123 |2015-01-01T00:00:01.0000000Z |

**Çözüm**:

```SQL
    SELECT
        Make,
        Time,
        LicensePlate AS CurrentCarLicensePlate,
        LAG(LicensePlate, 1) OVER (LIMIT DURATION(second, 90)) AS FirstCarLicensePlate,
        LAG(Time, 1) OVER (LIMIT DURATION(second, 90)) AS FirstCarTime
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LAG(Make, 1) OVER (LIMIT DURATION(second, 90)) = Make
```

**Açıklama**: Giriş  akışına bir olay geri göz atın ve **Make** değerini alın. Geçerli olaydaki **Make** değeri ile karşılaştırın ve aynı ise olayı çıkış. Ayrıca, önceki otomobil hakkındaki verileri almak için de **gecikme** kullanabilirsiniz.

## <a name="query-example-detect-the-duration-between-events"></a>Sorgu örneği: Olaylar arasındaki süreyi Algıla

**Açıklama**: Belirli bir olayın süresini bulur. Örneğin, bir web tıklama akışı verildiğinde, bir özellikte harcanan süreyi saptayın.

**Giriş**:  

| Kullanıcı | Özellik | Olay | Time |
| --- | --- | --- | --- |
| user@location.com |RightMenu |Start |2015-01-01T00:00:01.0000000Z |
| user@location.com |RightMenu |End |2015-01-01T00:00:08.0000000Z |

**Çıkış**:  

| Kullanıcı | Özellik | Duration |
| --- | --- | --- |
| user@location.com |RightMenu |7 |

**Çözüm**:

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

**Açıklama**: **Son** işlevi, olay türü başlatıldığında son **zaman** değerini almak için **kullanın.** **Son** işlev, sonucun benzersiz kullanıcı başına hesaplandığını göstermek için **[user] tarafından bölüm** kullanır. Sorgu, **Başlangıç** ve **durdurma** olayları arasındaki zaman farkı için 1 saatlik en büyük eşiğe sahiptir, ancak gerektiğinde yapılandırılabilir **(süre sınırı (saat, 1)** .

## <a name="query-example-detect-the-duration-of-a-condition"></a>Sorgu örneği: Bir koşulun süresini Algıla
**Açıklama**: Bir koşulun ne kadar süreyle oluştuğunu öğrenin.
Örneğin, bir hata, tüm otomobillerin yanlış ağırlığa (20.000 sterlini üzerinde) sahip olduğunu ve bu hatanın süresinin hesaplanması gerektiğini varsayalım.

**Giriş**:

| Yapın | Time | Ağırlık |
| --- | --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |2000 |
| Toyota |2015-01-01T00:00:02.0000000Z |25000 |
| Honda |2015-01-01T00:00:03.0000000Z |26000 |
| Toyota |2015-01-01T00:00:04.0000000Z |25000 |
| Honda |2015-01-01T00:00:05.0000000Z |26000 |
| Toyota |2015-01-01T00:00:06.0000000Z |25000 |
| Honda |2015-01-01T00:00:07.0000000Z |26000 |
| Toyota |2015-01-01T00:00:08.0000000Z |2000 |

**Çıkış**:

| StartFault | EndFault |
| --- | --- |
| 2015-01-01T00:00:02.000Z |2015-01-01T00:00:07.000Z |

**Çözüm**:

```SQL
    WITH SelectPreviousEvent AS
    (
    SELECT
    *,
        LAG([time]) OVER (LIMIT DURATION(hour, 24)) as previousTime,
        LAG([weight]) OVER (LIMIT DURATION(hour, 24)) as previousWeight
    FROM input TIMESTAMP BY [time]
    )

    SELECT 
        LAG(time) OVER (LIMIT DURATION(hour, 24) WHEN previousWeight < 20000 ) [StartFault],
        previousTime [EndFault]
    FROM SelectPreviousEvent
    WHERE
        [weight] < 20000
        AND previousWeight > 20000
```

**Açıklama**: Giriş akışını 24 saat için görüntülemek ve **startfault** ve **stopfault** 'in 20000 < ağırlığa göre kapladığı örnekleri aramak için **lag** kullanın.

## <a name="query-example-fill-missing-values"></a>Sorgu örneği: Eksik değerleri doldur

**Açıklama**: Eksik değerlere sahip olayların akışı için düzenli aralıklarla olayların akışını üretin. Örneğin, en son görülen veri noktasını raporlayan her 5 saniyede bir olay oluşturun.

**Giriş**:

| t | value |
| --- | --- |
| "2014-01-01T06:01:00" |1\. |
| "2014-01-01T06:01:05" |2 |
| "2014-01-01T06:01:10" |3 |
| "2014-01-01T06:01:15" |4 |
| "2014-01-01T06:01:30" |5 |
| "2014-01-01T06:01:35" |6 |

**Çıkış (ilk 10 satır)** :

| windowend | lastevent. t | lastevent. değer |
| --- | --- | --- |
| 2014-01-01T14:01:00.000Z |2014-01-01T14:01:00.000Z |1\. |
| 2014-01-01T14:01:05.000Z |2014-01-01T14:01:05.000Z |2 |
| 2014-01-01T14:01:10.000Z |2014-01-01T14:01:10.000Z |3 |
| 2014-01-01T14:01:15.000Z |2014-01-01T14:01:15.000Z |4 |
| 2014-01-01T14:01:20.000Z |2014-01-01T14:01:15.000Z |4 |
| 2014-01-01T14:01:25.000Z |2014-01-01T14:01:15.000Z |4 |
| 2014-01-01T14:01:30.000Z |2014-01-01T14:01:30.000Z |5 |
| 2014-01-01T14:01:35.000Z |2014-01-01T14:01:35.000Z |6 |
| 2014-01-01T14:01:40.000Z |2014-01-01T14:01:35.000Z |6 |
| 2014-01-01T14:01:45.000Z |2014-01-01T14:01:35.000Z |6 |

**Çözüm**:

```SQL
    SELECT
        System.Timestamp() AS windowEnd,
        TopOne() OVER (ORDER BY t DESC) AS lastEvent
    FROM
        input TIMESTAMP BY t
    GROUP BY HOPPINGWINDOW(second, 300, 5)
```

**Açıklama**: Bu sorgu, her 5 saniyede bir olay oluşturur ve daha önce alınan son olayı çıkarır. [Atlamalı pencere](/stream-analytics-query/hopping-window-azure-stream-analytics) süresi, sorgunun en son olayı (bu örnekte 300 saniye) bulmak için ne kadar geri göründüğünü belirler.


## <a name="query-example-correlate-two-event-types-within-the-same-stream"></a>Sorgu örneği: Aynı akış içindeki iki olay türünü ilişkilendirme

**Açıklama**: Bazen belirli bir zaman aralığında oluşan birden çok olay türüne göre uyarıların oluşturulması gerekir. Örneğin, Home ovens için bir IoT senaryosunda, fan sıcaklığı 40 ' den az olduğunda ve son 3 dakika boyunca maksimum güç 10 ' dan küçük olduğunda bir uyarı oluşturulmalıdır.

**Giriş**:

| time | deviceId | sensorName | value |
| --- | --- | --- | --- |
| "2018-01-01T16:01:00" | "Oven1" | kopyalar |120 |
| "2018-01-01T16:01:00" | "Oven1" | açılma |15 |
| "2018-01-01T16:02:00" | "Oven1" | kopyalar |100 |
| "2018-01-01T16:02:00" | "Oven1" | açılma |15 |
| "2018-01-01T16:03:00" | "Oven1" | kopyalar |70 |
| "2018-01-01T16:03:00" | "Oven1" | açılma |15 |
| "2018-01-01T16:04:00" | "Oven1" | kopyalar |50 |
| "2018-01-01T16:04:00" | "Oven1" | açılma |15 |
| "2018-01-01T16:05:00" | "Oven1" | kopyalar |30 |
| "2018-01-01T16:05:00" | "Oven1" | açılma |8 |
| "2018-01-01T16:06:00" | "Oven1" | kopyalar |20 |
| "2018-01-01T16:06:00" | "Oven1" | açılma |8 |
| "2018-01-01T16:07:00" | "Oven1" | kopyalar |20 |
| "2018-01-01T16:07:00" | "Oven1" | açılma |8 |
| "2018-01-01T16:08:00" | "Oven1" | kopyalar |20 |
| "2018-01-01T16:08:00" | "Oven1" | açılma |8 |

**Çıkış**:

| eventTime | deviceId | kopyalar | alertMessage | maxPowerDuringLast3mins |
| --- | --- | --- | --- | --- | 
| "2018-01-01T16:05:00" | "Oven1" |30 | "Kısa devre ısıtma öğeleri" |15 |
| "2018-01-01T16:06:00" | "Oven1" |20 | "Kısa devre ısıtma öğeleri" |15 |
| "2018-01-01T16:07:00" | "Oven1" |20 | "Kısa devre ısıtma öğeleri" |15 |

**Çözüm**:

```SQL
WITH max_power_during_last_3_mins AS (
    SELECT 
        System.TimeStamp() AS windowTime,
        deviceId,
        max(value) as maxPower
    FROM
        input TIMESTAMP BY t
    WHERE 
        sensorName = 'power' 
    GROUP BY 
        deviceId, 
        SlidingWindow(minute, 3) 
)

SELECT 
    t1.t AS eventTime,
    t1.deviceId, 
    t1.value AS temp,
    'Short circuit heating elements' as alertMessage,
    t2.maxPower AS maxPowerDuringLast3mins
    
INTO resultsr

FROM input t1 TIMESTAMP BY t
JOIN max_power_during_last_3_mins t2
    ON t1.deviceId = t2.deviceId 
    AND t1.t = t2.windowTime
    AND DATEDIFF(minute,t1,t2) between 0 and 3
    
WHERE
    t1.sensorName = 'temp'
    AND t1.value <= 40
    AND t2.maxPower > 10
```

**Açıklama**: İlk sorgu `max_power_during_last_3_mins`, son 3 dakika içinde her cihaz için güç algılayıcısı 'nın en büyük değerini bulmak üzere [kayan pencereyi](/stream-analytics-query/sliding-window-azure-stream-analytics) kullanır. İkinci sorgu, geçerli olayla ilgili en son pencerede bulunan güç değerini bulmak için ilk sorguya birleştirilir. Daha sonra koşullara uyulduğunda, cihaz için bir uyarı oluşturulur.

## <a name="query-example-process-events-independent-of-device-clock-skew-substreams"></a>Sorgu örneği: Olayları cihaz saati Eğilerinden (alt akışlar) bağımsız olarak işle

**Açıklama**: Olaylar, Event üreticileri, bölümler arasındaki saat eğetkinlikleri veya ağ gecikmesi arasındaki saat eğlemeleri nedeniyle geçmiş veya sıra dışı olabilir. Aşağıdaki örnekte, Tollıd 2 için cihaz saatinin Tollıd 1 ' in arkasında beş saniye olması ve Tollıd 3 ' ün cihaz saatinin Tollıd 1 ' in arkasında on saniye olması gerekir. 

**Giriş**:

| LicensePlate | Yapın | Time | TollID |
| --- | --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:01.0000000 Z | 1\. |
| YILHN 6970 |Toyota |2015-07-27T00:00:05.0000000Z | 1\. |
| QYIF 9358 |Honda |2015-07-27T00:00:01.0000000 Z | 2 |
| GXF 9462 |BMW |2015-07-27T00:00:04.0000000 Z | 2 |
| VFE 1616 |Toyota |2015-07-27T00:00:10.0000000 Z | 1\. |
| RMV 8282 |Honda |2015-07-27T00:00:03.0000000 Z | 3 |
| MDR 6128 |BMW |2015-07-27T00:00:11.0000000 Z | 2 |
| YZK 5704 |Ford |2015-07-27T00:00:07.0000000 Z | 3 |

**Çıkış**:

| TollID | Count |
| --- | --- |
| 1\. | 2 |
| 2 | 2 |
| 1\. | 1\. |
| 3 | 1\. |
| 2 | 1\. |
| 3 | 1\. |

**Çözüm**:

```SQL
SELECT
      TollId,
      COUNT(*) AS Count
FROM input
      TIMESTAMP BY Time OVER TollId
GROUP BY TUMBLINGWINDOW(second, 5), TollId
```

**Açıklama**: [Over](/stream-analytics-query/timestamp-by-azure-stream-analytics#over-clause-interacts-with-event-ordering) yan tümcesi, alt akışlar kullanılarak her cihaz zaman çizelgesine ayrı olarak bakar. Her bir Tollıd için çıkış olayları, hesaplandıkları sırada oluşturulur. Bu, olayların, tüm cihazlar aynı saat üzerinde olduğu gibi yeniden sıralamak yerine her bir Tollıd 'e göre olduğu anlamına gelir.

## <a name="query-example-remove-duplicate-events-in-a-window"></a>Sorgu örneği: Penceredeki yinelenen olayları kaldırma

**Açıklama**: Belirli bir zaman penceresinde olaylar üzerinde ortalamaları hesaplama gibi bir işlem gerçekleştirirken, yinelenen olayların filtrelenmelidir. Aşağıdaki örnekte ikinci olay, birincisinin yinelemesidir.

**Giriş**:  

| DeviceId | Time | Öznitelik | Value |
| --- | --- | --- | --- |
| 1\. |2018-07-27T00:00:01.0000000 Z |Sıcaklık |50 |
| 1\. |2018-07-27T00:00:01.0000000 Z |Sıcaklık |50 |
| 2 |2018-07-27T00:00:01.0000000 Z |Sıcaklık |40 |
| 1\. |2018-07-27T00:00:05.0000000 Z |Sıcaklık |60 |
| 2 |2018-07-27T00:00:05.0000000 Z |Sıcaklık |50 |
| 1 |2018-07-27T00:00:10.0000000 Z |Sıcaklık |100 |

**Çıkış**:  

| Averagedeğeri | DeviceId |
| --- | --- |
| 70 | 1\. |
|45 | 2 |

**Çözüm**:

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

**Açıklama**: [Count (ayrık süre)](/stream-analytics-query/count-azure-stream-analytics) bir zaman penceresi içindeki zaman sütunundaki ayrı değerlerin sayısını döndürür. Daha sonra, yinelenenleri atarak cihaz başına ortalamayı hesaplamak için bu adımın çıkışını kullanabilirsiniz.

## <a name="geofencing-and-geospatial-queries"></a>Bölge sınırlaması ve jeo-uzamsal sorgular
Azure Stream Analytics, filo yönetimi, arttırıldığında paylaşımı, bağlantılı otomobil ve varlık izleme gibi senaryoları uygulamak için kullanılabilen yerleşik Jeo-uzamsal işlevler sağlar. Jeo-uzamsal veriler, coğrafi JSON veya WKT biçimlerinde olay akışı veya başvuru verilerinin bir parçası olarak alınabilir. Daha fazla bilgi için, Azure Stream Analytics makalesinde [Bölge sınırlaması ve jeo uzamsal toplama senaryolarına](geospatial-scenarios.md) bakın.

## <a name="language-extensibility-through-javascript-and-c"></a>JavaScript ile dil genişletilebilirliği veC#
Azure Stream Ananlytiği Query langugae, JavaScript veya C# dillerde yazılmış özel işlevlerle genişletilebilir. Daha fazla bilgi için bkz. foolowing makaleleri:
* [JavaScript Kullanıcı tanımlı işlevleri Azure Stream Analytics](stream-analytics-javascript-user-defined-functions.md)
* [JavaScript Kullanıcı tanımlı toplamaları Azure Stream Analytics](stream-analytics-javascript-user-defined-aggregates.md)
* [Azure Stream Analytics Edge işleri için .NET Standard Kullanıcı tanımlı işlevler geliştirme](stream-analytics-edge-csharp-udf-methods.md)

## <a name="get-help"></a>Yardım alın

Daha fazla yardım için deneyin bizim [Azure Stream Analytics forumumuzu](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Sonraki adımlar
* [Azure Stream analytics'e giriş](stream-analytics-introduction.md)
* [Azure Akış Analizi'ni kullanmaya başlama](stream-analytics-real-time-fraud-detection.md)
* [Azure Akış Analizi işlerini ölçeklendirme](stream-analytics-scale-jobs.md)
* [Azure Akış Analizi Sorgu Dili Başvurusu](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Akış Analizi Yönetimi REST API'si Başvurusu](https://msdn.microsoft.com/library/azure/dn835031.aspx)

