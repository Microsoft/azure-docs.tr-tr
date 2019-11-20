---
title: Azure Stream Analytics 'de yapılandırılabilir eşik tabanlı kurallar
description: Bu makalede, Azure Stream Analytics ' de yapılandırılabilir eşik tabanlı kurallara sahip bir uyarı çözümüne ulaşmak için başvuru verilerinin nasıl kullanılacağı açıklanır.
services: stream-analytics
author: zhongc
ms.author: zhongc
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/30/2018
ms.openlocfilehash: f8fd21f411093e22b2b1dc5afd6da9cb26db6ff8
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72934253"
---
# <a name="process-configurable-threshold-based-rules-in-azure-stream-analytics"></a>Azure Stream Analytics 'de yapılandırılabilir eşik tabanlı kuralları işleme
Bu makalede, Azure Stream Analytics ' de yapılandırılabilir eşik tabanlı kurallar kullanan bir uyarı çözümüne ulaşmak için başvuru verilerinin nasıl kullanılacağı açıklanır.

## <a name="scenario-alerting-based-on-adjustable-rule-thresholds"></a>Senaryo: ayarlanabilir kural eşiklerine göre uyarı
Gelen akış olayları belirli bir değere ulaştığında veya gelen akış olaylarını temel alan toplanmış bir değer belirli bir eşiği aştığında çıkış olarak bir uyarı oluşturmanız gerekebilir. Değeri, sabit ve önceden belirlenmiş bir statik eşikle karşılaştırılan Stream Analytics bir sorgu ayarlamak basittir. Sabit bir eşik, basit sayısal karşılaştırmalar (büyüktür, küçüktür ve eşitlik) kullanılarak akış sorgusu sözdizimine sabit kodlanmış olabilir.

Bazı durumlarda, eşik değerlerinin her değişiklik yaptığı her seferinde sorgu söz dizimi düzenlenmeden daha kolay yapılandırılabilir olması gerekir. Diğer durumlarda, her bir cihaz türü üzerinde farklı bir eşik değeri bulunan her biriyle aynı sorgu tarafından işlenen çok sayıda cihaza veya kullanıcıya ihtiyacınız olabilir. 

Bu model eşiklerini dinamik olarak yapılandırmak için kullanılabilir, giriş verilerini filtreleyerek eşiğin hangi türde bir cihaza uygulanacağını seçmeli olarak seçin ve çıkışa dahil edilecek alanları seçmeli olarak seçin.

## <a name="recommended-design-pattern"></a>Önerilen tasarım kalıbı
Uyarı eşiklerinin bir araması olarak bir Stream Analytics işine bir başvuru verileri girişi kullanın:
- Eşik değerlerini, anahtar başına bir değer olan başvuru verilerinde depolayın.
- Akış verileri giriş olaylarını anahtar sütunundaki başvuru verilerine katın.
- Eşik değeri olarak başvuru verilerinden anahtarlı değeri kullanın.

## <a name="example-data-and-query"></a>Örnek veri ve sorgu
Örnekte, bir dakikalık bir penceredeki cihazlardan gelen veri akışı toplamı başvuru verileri olarak sağlanan kuraldaki belirlenen değerlerle eşleştiğinde uyarılar oluşturulur.

Sorguda, her DeviceID için ve DeviceID altındaki her bir metricName için 0 ile 5 arasında bir boyut yapılandırabilirsiniz. Yalnızca karşılık gelen filtre değerlerine sahip olaylar gruplandırılır. Gruplandırıldıktan sonra, en az, en fazla, ortalama olan pencereli toplamalar 60 saniyelik bir pencere penceresinde hesaplanır. Bu durumda, uyarı çıktı olayını oluşturmak için, toplanmış değerler üzerindeki filtreler, başvurudaki yapılandırılan eşiğe göre hesaplanır.

Örnek olarak, **Rules**adlı bir başvuru veri girişi ve **ölçüm**adlı akış veri girişi olan Stream Analytics bir iş olduğunu varsayalım. 

## <a name="reference-data"></a>Başvuru verileri
Bu örnek başvuru verileri, eşik tabanlı bir kuralın nasıl temsil edileceğini gösterir. JSON dosyası başvuru verilerini barındırır ve Azure Blob depolama alanına kaydedilir ve bu blob depolama kapsayıcısı, **kurallar**adlı bir başvuru veri girişi olarak kullanılır. Bu JSON dosyasının üzerine yazabilir ve kural yapılandırmasını, akış işini durdurmadan veya başlatmadan zaman ile değiştirin.

- Örnek kural, CPU aşıldığı zaman ayarlanabilir bir uyarı temsil etmek için kullanılır (ortalama değer değerinden büyük veya buna eşitse) yüzde `90`. `value` alanı gerektiği şekilde yapılandırılabilir.
- Kuralın, daha sonra `AVGGREATEROREQUAL`sorgu sözdiziminde dinamik olarak yorumlanan bir **operatör** alanına sahip olduğuna dikkat edin. 
- Kural, belirli bir boyut anahtarındaki verileri `C1`değer ile `2` filtreler. Diğer alanlar, giriş akışının bu olay alanları tarafından filtreleneceğini belirten boş bir dizedir. Gerektiğinde diğer eşleşen alanları filtrelemek için ek CPU kuralları ayarlayabilirsiniz.
- Tüm sütunlar çıkış uyarı olayına dahil edilmez. Bu durumda `2` `includedDim` anahtar numarası, akıştaki olay verilerinin 2 numaralı alanın uygun çıkış olaylarına dahil edileceğini göstermek için `TRUE` açıktır. Diğer alanlar, uyarı çıktısına dahil değildir, ancak alan listesi ayarlanabilir.


```json
{
    "ruleId": 1234, 
    "deviceId" : "978648", 
    "metricName": "CPU", 
    "alertName": "hot node AVG CPU over 90",
    "operator" : "AVGGREATEROREQUAL",
    "value": 90, 
    "includeDim": {
        "0": "FALSE", 
        "1": "FALSE", 
        "2": "TRUE", 
        "3": "FALSE", 
        "4": "FALSE"
    },
    "filter": {
        "0": "", 
        "1": "",
        "2": "C1", 
        "3": "", 
        "4": ""
    }    
}
```

## <a name="example-streaming-query"></a>Örnek akış sorgusu
Bu örnek Stream Analytics sorgu yukarıdaki örnekteki **kuralların** başvuru verilerine, **ölçümler**adlı verilerin giriş akışına katılır.

```sql
WITH transformedInput AS
(
    SELECT
        dim0 = CASE rules.includeDim.[0] WHEN 'TRUE' THEN metrics.custom.dimensions.[0].value ELSE NULL END,
        dim1 = CASE rules.includeDim.[1] WHEN 'TRUE' THEN metrics.custom.dimensions.[1].value ELSE NULL END,
        dim2 = CASE rules.includeDim.[2] WHEN 'TRUE' THEN metrics.custom.dimensions.[2].value ELSE NULL END,
        dim3 = CASE rules.includeDim.[3] WHEN 'TRUE' THEN metrics.custom.dimensions.[3].value ELSE NULL END,
        dim4 = CASE rules.includeDim.[4] WHEN 'TRUE' THEN metrics.custom.dimensions.[4].value ELSE NULL END,
        metric = metrics.metric.value,
        metricName = metrics.metric.name,
        deviceId = rules.deviceId, 
        ruleId = rules.ruleId, 
        alertName = rules.alertName,
        ruleOperator = rules.operator, 
        ruleValue = rules.value
    FROM 
        metrics
        timestamp by eventTime
    JOIN 
        rules
        ON metrics.deviceId = rules.deviceId AND metrics.metric.name = rules.metricName
    WHERE
        (rules.filter.[0] = '' OR metrics.custom.filters.[0].value = rules.filter.[0]) AND 
        (rules.filter.[1] = '' OR metrics.custom.filters.[1].value = rules.filter.[1]) AND
        (rules.filter.[2] = '' OR metrics.custom.filters.[2].value = rules.filter.[2]) AND
        (rules.filter.[3] = '' OR metrics.custom.filters.[3].value = rules.filter.[3]) AND
        (rules.filter.[4] = '' OR metrics.custom.filters.[4].value = rules.filter.[4])
)

SELECT
    System.Timestamp as time, 
    transformedInput.deviceId as deviceId,
    transformedInput.ruleId as ruleId,
    transformedInput.metricName as metric,
    transformedInput.alertName as alert,
    AVG(metric) as avg,
    MIN(metric) as min, 
    MAX(metric) as max, 
    dim0, dim1, dim2, dim3, dim4
FROM
    transformedInput
GROUP BY
    transformedInput.deviceId,
    transformedInput.ruleId,
    transformedInput.metricName,
    transformedInput.alertName,
    dim0, dim1, dim2, dim3, dim4,
    ruleOperator, 
    ruleValue, 
    TumblingWindow(second, 60)
HAVING
    (
        (ruleOperator = 'AVGGREATEROREQUAL' AND avg(metric) >= ruleValue) OR
        (ruleOperator = 'AVGEQUALORLESS' AND avg(metric) <= ruleValue) 
    )
```

## <a name="example-streaming-input-event-data"></a>Örnek akış girişi olay verileri
Bu örnek JSON verileri, yukarıdaki akış sorgusunda kullanılan **ölçüm** giriş verilerini temsil eder. 

- Üç örnek olay 1 dakikalık TimeSpan, değer `T14:50`içinde listelenir. 
- Üçü de aynı `deviceId` değerine sahiptir `978648`.
- CPU ölçüm değerleri her olay, `98`, `95``80` sırasıyla değişir. Yalnızca ilk iki olay, kuralda belirlenen CPU uyarısı kuralını aşıyor.
- Uyarı kuralındaki ıncludedim alanı 2 numaralı önemli sayıdır. Örnek olaylardaki karşılık gelen anahtar 2 alanı `NodeName`olarak adlandırılmıştır. Üç örnek olay, sırasıyla `N024`, `N024`ve `N014` değerlere sahiptir. Çıktıda yalnızca düğüm `N024`, yalnızca yüksek CPU için uyarı ölçütleriyle eşleşen tek veri olduğunu görürsünüz. `N014` yüksek CPU eşiğini karşılamıyor.
- Uyarı kuralı, örnek olaylardaki `cluster` alanına karşılık gelen, yalnızca 2 numaralı anahtar numarasında `filter` ile yapılandırılır. Tüm üç örnek olay `C1` değerine sahiptir ve filtre ölçütlerine göre eşleşir.

```json
{
    "eventTime": "2018-04-30T14:50:23.1324132Z",
    "deviceId": "978648",
    "custom": {
        "dimensions": {
            "0": {
                "name": "NodeType",
                "value": "N1"
            },
            "1": {
                "name": "Cluster",
                "value": "C1"
            },
            "2": {
                "name": "NodeName",
                "value": "N024"
            }
        },
        "filters": {
            "0": {
                "name": "application",
                "value": "A1"
            },
            "1": {
                "name": "deviceType",
                "value": "T1"
            },
            "2": {
                "name": "cluster",
                "value": "C1"
            },
            "3": {
                "name": "nodeType",
                "value": "N1"
            }
        }
    },
    "metric": {
        "name": "CPU",
        "value": 98,
        "count": 1.0,
        "min": 98,
        "max": 98,
        "stdDev": 0.0
    }
}
{
    "eventTime": "2018-04-30T14:50:24.1324138Z",
    "deviceId": "978648",
    "custom": {
        "dimensions": {
            "0": {
                "name": "NodeType",
                "value": "N2"
            },
            "1": {
                "name": "Cluster",
                "value": "C1"
            },
            "2": {
                "name": "NodeName",
                "value": "N024"
            }
        },
        "filters": {
            "0": {
                "name": "application",
                "value": "A1"
            },
            "1": {
                "name": "deviceType",
                "value": "T1"
            },
            "2": {
                "name": "cluster",
                "value": "C1"
            },
            "3": {
                "name": "nodeType",
                "value": "N2"
            }
        }
    },
    "metric": {
        "name": "CPU",
        "value": 95,
        "count": 1,
        "min": 95,
        "max": 95,
        "stdDev": 0
    }
}
{
    "eventTime": "2018-04-30T14:50:37.1324130Z",
    "deviceId": "978648",
    "custom": {
        "dimensions": {
            "0": {
                "name": "NodeType",
                "value": "N3"
            },
            "1": {
                "name": "Cluster",
                "value": "C1 "
            },
            "2": {
                "name": "NodeName",
                "value": "N014"
            }
        },
        "filters": {
            "0": {
                "name": "application",
                "value": "A1"
            },
            "1": {
                "name": "deviceType",
                "value": "T1"
            },
            "2": {
                "name": "cluster",
                "value": "C1"
            },
            "3": {
                "name": "nodeType",
                "value": "N3"
            }
        }
    },
    "metric": {
        "name": "CPU",
        "value": 80,
        "count": 1,
        "min": 80,
        "max": 80,
        "stdDev": 0
    }
}
```

## <a name="example-output"></a>Örnek çıkış
Bu örnek çıkış JSON verileri, başvuru verilerinde tanımlanan CPU eşik kuralına göre tek bir uyarı olayının üretildiğini gösterir. Çıkış olayı, uyarının adının yanı sıra değerlendirilen alanların toplanmış (Average, min, Max) adını içerir. Çıkış olayı verileri, kural yapılandırması nedeniyle, alan anahtar numarası 2 `NodeName` değeri `N024` içerir. (JSON, okunabilirlik için satır sonlarını göstermek üzere değiştirilmiştir.)

```JSON
{"time":"2018-05-01T02:03:00.0000000Z","deviceid":"978648","ruleid":1234,"metric":"CPU",
"alert":"hot node AVG CPU over 90","avg":96.5,"min":95.0,"max":98.0,
"dim0":null,"dim1":null,"dim2":"N024","dim3":null,"dim4":null}
```