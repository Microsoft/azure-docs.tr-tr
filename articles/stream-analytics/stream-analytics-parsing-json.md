---
title: Azure Stream Analytics 'de JSON ve AVRO ayrıştırma
description: Bu makalede, diziler, JSON, CSV biçimli veriler gibi karmaşık veri türlerinde nasıl işlem yapılacağı açıklanır.
ms.service: stream-analytics
author: sidramadoss
ms.author: sidram
ms.topic: conceptual
ms.date: 01/29/2020
ms.custom: devx-track-js
ms.openlocfilehash: f6cb131fb3ff3cab4122aac5e1c6960dee4f8421
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98012249"
---
# <a name="parse-json-and-avro-data-in-azure-stream-analytics"></a>Azure Stream Analytics JSON ve avro verilerini ayrıştırın

CSV, JSON ve avro veri biçimlerinde olayları işlemeyi destekler Azure Stream Analytics. Hem JSON hem de avro verileri yapılandırılabilir ve iç içe geçmiş nesneler (kayıtlar) ve diziler gibi bazı karmaşık türleri içerebilir. 

>[!NOTE]
>AVRO Olay Hub 'ı tarafından oluşturulan dosyalar *özel seri hale getirici* özelliğini kullanmanızı gerektiren belirli bir biçimi kullanır. Daha fazla bilgi için bkz. [.NET özel seri hale getiriciler kullanarak herhangi bir biçimde giriş okuma](./custom-deserializer-examples.md).
>
>Stream Analytics AVRO serisini kaldırma, eşleme türünü desteklemez. EventHub yakalama eşlemesi kullandığından, Stream Analytics EventHub yakalama bloblarını okuyamıyorum.


## <a name="record-data-types"></a>Veri türlerini Kaydet
Kayıt veri türleri, giriş veri akışlarında karşılık gelen biçimler kullanıldığında JSON ve avro dizilerini temsil etmek için kullanılır. Bu örneklerde, giriş olaylarını JSON biçiminde okuyan örnek bir algılayıcı gösterilmektedir. Tek bir olay örneği aşağıda verilmiştir:

```json
{
    "DeviceId" : "12345",
    "Location" :
    {
        "Lat": 47,
        "Long": 122
    },
    "SensorReadings" :
    {
        "Temperature" : 80,
        "Humidity" : 70,
        "CustomSensor01" : 5,
        "CustomSensor02" : 99,
        "SensorMetadata" : 
        {
        "Manufacturer":"ABC",
        "Version":"1.2.45"
        }
    }
}
```

### <a name="access-nested-fields-in-known-schema"></a>Bilinen şemada iç içe geçmiş alanlara erişin
Doğrudan Sorgunuzla iç içe geçmiş alanlara kolayca erişmek için nokta gösterimini (.) kullanın. Örneğin, bu sorgu önceki JSON verilerinde Location özelliği altındaki Enlem ve boylam koordinatlarını seçer. Nokta gösterimi aşağıda gösterildiği gibi birden çok düzeyde gezinmek için kullanılabilir.

```SQL
SELECT
    DeviceID,
    Location.Lat,
    Location.Long,
    SensorReadings.Temperature,
    SensorReadings.SensorMetadata.Version
FROM input
```

Sonuç:

|DeviceID|[|Kalacağını|Sıcaklık|Sürüm|
|-|-|-|-|-|
|12345|47|122|80|1.2.45|


### <a name="select-all-properties"></a>Tüm Özellikleri Seç
' * ' Joker karakterini kullanarak iç içe geçmiş bir kaydın tüm özelliklerini seçebilirsiniz. Aşağıdaki örneği inceleyin:

```SQL
SELECT
    DeviceID,
    Location.*
FROM input
```

Sonuç:

|DeviceID|[|Kalacağını|
|-|-|-|
|12345|47|122|


### <a name="access-nested-fields-when-property-name-is-a-variable"></a>Özellik adı bir değişken olduğunda iç içe alanlara erişin

Özellik adı bir değişken ise [Getrecordpropertyvalue](/stream-analytics-query/getrecordpropertyvalue-azure-stream-analytics) işlevini kullanın. Bu özellik adlarını kodlamadan dinamik sorgular oluşturulmasına olanak sağlar.

Örneğin, örnek veri akışının her bir cihaz algılayıcısı için eşikler içeren **başvuru verileriyle katılması** gerektiğini düşünün. Bu tür başvuru verilerinin bir parçacığı aşağıda gösterilmiştir.

```json
{
    "DeviceId" : "12345",
    "SensorName" : "Temperature",
    "Value" : 85
},
{
    "DeviceId" : "12345",
    "SensorName" : "Humidity",
    "Value" : 65
}
```

Buradaki amaç, makalenin en üstündeki örnek veri kümenizi, bu başvuru verilerine katmaktır ve her bir algılayıcı ölçüsü için eşiğin üstünde bir olay çıktı. Diğer bir deyişle, yukarıdaki tek olayımız, ilgili eşiklerinin üzerinde birden fazla algılayıcı varsa, bu, birleşimde teşekkürler. JOIN olmadan benzer sonuçlara ulaşmak için aşağıdaki bölüme bakın.

```SQL
SELECT
    input.DeviceID,
    thresholds.SensorName,
    "Alert : Sensor above threshold" AS AlertMessage
FROM input      -- stream input
JOIN thresholds -- reference data input
ON
    input.DeviceId = thresholds.DeviceId
WHERE
    GetRecordPropertyValue(input.SensorReadings, thresholds.SensorName) > thresholds.Value
```

**Getrecordpropertyvalue** , *Sensorreadusing* içindeki özelliği seçer. Bu ad, başvuru verilerinden gelen özellik adı ile eşleşir. Ardından *Sensorreadlılar* 'dan ilişkili değer ayıklanır.

Sonuç:

|DeviceID|SensorName|AlertMessage|
|-|-|-|
|12345|Nem oranı|Uyarı: Yukarıdaki algılayıcı eşiğin üstünde|

### <a name="convert-record-fields-into-separate-events"></a>Kayıt alanlarını ayrı olaylara Dönüştür

Kayıt alanlarını ayrı olaylara dönüştürmek için, [Getrecordproperties](/stream-analytics-query/getrecordproperties-azure-stream-analytics) Işleviyle birlikte [Apply](/stream-analytics-query/apply-azure-stream-analytics) işlecini kullanın.

Özgün örnek verilerle, aşağıdaki sorgu, özellikleri farklı olaylara ayıklamak için kullanılabilir.

```SQL
SELECT
    event.DeviceID,
    sensorReading.PropertyName,
    sensorReading.PropertyValue
FROM input as event
CROSS APPLY GetRecordProperties(event.SensorReadings) AS sensorReading
```

Sonuç:

|DeviceID|SensorName|AlertMessage|
|-|-|-|
|12345|Sıcaklık|80|
|12345|Nem oranı|70|
|12345|CustomSensor01|5|
|12345|CustomSensor02|99|
|12345|SensorMetadata|[nesne nesnesi]|

[İle](/stream-analytics-query/with-azure-stream-analytics)kullanarak, bu olayları farklı hedeflere yönlendirmek mümkün olur:

```SQL
WITH Stage0 AS
(
    SELECT
        event.DeviceID,
        sensorReading.PropertyName,
        sensorReading.PropertyValue
    FROM input as event
    CROSS APPLY GetRecordProperties(event.SensorReadings) AS sensorReading
)

SELECT DeviceID, PropertyValue AS Temperature INTO TemperatureOutput FROM Stage0 WHERE PropertyName = 'Temperature'
SELECT DeviceID, PropertyValue AS Humidity INTO HumidityOutput FROM Stage0 WHERE PropertyName = 'Humidity'
```

### <a name="parse-json-record-in-sql-reference-data"></a>SQL başvuru verilerinde JSON kaydını ayrıştırma
Azure SQL veritabanı 'nı işinizdeki başvuru verileri olarak kullanırken JSON biçiminde veri içeren bir sütun olması mümkündür. Aşağıda bir örnek gösterilmiştir.

|DeviceID|Veriler|
|-|-|
|12345|{"Key": "değer1"}|
|54321|{"Key": "değer2"}|

Basit bir JavaScript Kullanıcı tanımlı işlevi yazarak *veri* sütununda JSON kaydını ayrıştırabilirsiniz.

```javascript
function parseJson(string) {
return JSON.parse(string);
}
```

Ardından, JSON kayıtlarınızın alanlarına erişmek için aşağıda gösterildiği gibi Stream Analytics sorgunuzda bir adım oluşturabilirsiniz.

 ```SQL
 WITH parseJson as
 (
 SELECT DeviceID, udf.parseJson(sqlRefInput.Data) as metadata,
 FROM sqlRefInput
 )
 
 SELECT metadata.key
 INTO output
 FROM streamInput
 JOIN parseJson 
 ON streamInput.DeviceID = parseJson.DeviceID
```

## <a name="array-data-types"></a>Dizi veri türleri

Dizi veri türleri sıralı değerler koleksiyonudur. Dizi değerlerinde bazı tipik işlemler aşağıda ayrıntılı olarak verilmiştir. Bu örnekler, [GetArrayElement](/stream-analytics-query/getarrayelement-azure-stream-analytics), [getarrayelements](/stream-analytics-query/getarrayelements-azure-stream-analytics), [GetArrayLength](/stream-analytics-query/getarraylength-azure-stream-analytics)ve [Apply](/stream-analytics-query/apply-azure-stream-analytics) işleci işlevlerini kullanır.

Tek bir olaya bir örnek aşağıda verilmiştir. Her ikisi de `CustomSensor03` `SensorMetadata` **dizi** türündedir:

```json
{
    "DeviceId" : "12345",
    "SensorReadings" :
    {
        "Temperature" : 80,
        "Humidity" : 70,
        "CustomSensor01" : 5,
        "CustomSensor02" : 99,
        "CustomSensor03": [12,-5,0]
     },
    "SensorMetadata":[
        {          
            "smKey":"Manufacturer",
            "smValue":"ABC"                
        },
        {
            "smKey":"Version",
            "smValue":"1.2.45"
        }
    ]
}
```

### <a name="working-with-a-specific-array-element"></a>Belirli bir dizi öğesiyle çalışma

Belirtilen dizinde dizi öğesi seçin (ilk dizi öğesini seçerek):

```SQL
SELECT
    GetArrayElement(SensorReadings.CustomSensor03, 0) AS firstElement
FROM input
```

Sonuç:

|firstElement|
|-|
|12|

### <a name="select-array-length"></a>Dizi uzunluğunu seçin

```SQL
SELECT
    GetArrayLength(SensorReadings.CustomSensor03) AS arrayLength
FROM input
```

Sonuç:

|arrayLength|
|-|
|3|

### <a name="convert-array-elements-into-separate-events"></a>Dizi öğelerini ayrı olaylara Dönüştür

Tek tek olaylar olarak tüm dizi öğelerini seçin. [Getarrayelements](/stream-analytics-query/getarrayelements-azure-stream-analytics) yerleşik işlevi Ile birlikte [Apply](/stream-analytics-query/apply-azure-stream-analytics) işleci, tüm dizi öğelerini ayrı olaylar olarak ayıklar:

```SQL
SELECT
    DeviceId,
    CustomSensor03Record.ArrayIndex,
    CustomSensor03Record.ArrayValue
FROM input
CROSS APPLY GetArrayElements(SensorReadings.CustomSensor03) AS CustomSensor03Record

```

Sonuç:

|DeviceId|ArrayIndex|ArrayValue|
|-|-|-|
|12345|0|12|
|12345|1|-5|
|12345|2|0|

```SQL
SELECT   
    i.DeviceId, 
    SensorMetadataRecords.ArrayValue.smKey as smKey,
    SensorMetadataRecords.ArrayValue.smValue as smValue
FROM input i
CROSS APPLY GetArrayElements(SensorMetadata) AS SensorMetadataRecords
 ```
 
Sonuç:

|DeviceId|smKey|smValue|
|-|-|-|
|12345|Üretici|ABC|
|12345|Sürüm|1.2.45|

Ayıklanan alanların sütunlarda görünmesi gerekiyorsa, [JOIN](/stream-analytics-query/join-azure-stream-analytics) işlemine ek olarak, [WITH](/stream-analytics-query/with-azure-stream-analytics) sözdizimi kullanılarak veri kümesini Özet hale getirmeniz mümkündür. Bu birleşimin, çoğaltmayı önleyen bir [zaman sınırı](/stream-analytics-query/join-azure-stream-analytics#BKMK_DateDiff) koşulu gerekir:

```SQL
WITH DynamicCTE AS (
    SELECT   
        i.DeviceId,
        SensorMetadataRecords.ArrayValue.smKey as smKey,
        SensorMetadataRecords.ArrayValue.smValue as smValue
    FROM input i
    CROSS APPLY GetArrayElements(SensorMetadata) AS SensorMetadataRecords 
)

SELECT
    i.DeviceId,
    i.Location.*,
    V.smValue AS 'smVersion',
    M.smValue AS 'smManufacturer'
FROM input i
LEFT JOIN DynamicCTE V ON V.smKey = 'Version' and V.DeviceId = i.DeviceId AND DATEDIFF(minute,i,V) BETWEEN 0 AND 0 
LEFT JOIN DynamicCTE M ON M.smKey = 'Manufacturer' and M.DeviceId = i.DeviceId AND DATEDIFF(minute,i,M) BETWEEN 0 AND 0
```

Sonuç:

|DeviceId|[|Kalacağını|smVersion|smManufacturer|
|-|-|-|-|-|
|12345|47|122|1.2.45|ABC|

## <a name="see-also"></a>Ayrıca Bkz.
[Azure Stream Analytics veri türleri](/stream-analytics-query/data-types-azure-stream-analytics)