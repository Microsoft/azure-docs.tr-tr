---
title: Jeo-uzamsal işlevlere Azure Stream Analytics giriş
description: Bu makalede Azure Stream Analytics işlerinde kullanılan Jeo-uzamsal işlevler açıklanır.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.openlocfilehash: dfbe7e607395006f9bd7da0be0d5673353e2801f
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73162601"
---
# <a name="introduction-to-stream-analytics-geospatial-functions"></a>Jeo-uzamsal işlevlere Stream Analytics giriş

Azure Stream Analytics Jeo uzamsal işlevler, Jeo uzamsal verileri akışta gerçek zamanlı analizler sağlar. Yalnızca birkaç satır kodla, karmaşık senaryolar için bir üretim sınıfı çözümü geliştirebilirsiniz. 

Jeo-uzamsal işlevlerden faydalanabilir senaryolar örnekleri şunlardır:

* Ride paylaşımı
* Filo yönetimi
* Varlık izleme
* Coğrafi sınırlama
* Hücre siteleri arasında telefon izleme

Stream Analytics sorgu dili yedi yerleşik Jeo uzamsal işlevlere sahiptir: **Createlinestring**, **createpoint**, **createçokgen**, **ST_DISTANCE**, **ST_OVERLAPS**, **ST_INTERSECTS**ve **ST_WITHIN**.

## <a name="createlinestring"></a>CreateLineString

`CreateLineString` işlevi noktaları kabul eder ve bir haritada çizgi olarak çizilebilen bir GeoJSON LineString döndürür. LineString oluşturmak için en az iki noktanız olmalıdır. LineString noktaları sırayla bağlanır.

Aşağıdaki sorgu, üç noktayı kullanarak bir LineString oluşturmak için `CreateLineString` kullanır. İlk nokta akış girişi verilerinden oluşturulur, diğer ikisi el ile oluşturulur.

```SQL 
SELECT  
     CreateLineString(CreatePoint(input.latitude, input.longitude), CreatePoint(10.0, 10.0), CreatePoint(10.5, 10.5))  
FROM input  
```  

### <a name="input-example"></a>Giriş örneği  
  
|En|MIN|  
|--------------|---------------|  
|3.0|-10,2|  
|-87,33|20,2321|  
  
### <a name="output-example"></a>Çıkış örneği  

 {"tür": "LineString", "koordinatlar": [[-10,2, 3,0], [10,0, 10,0], [10,5, 10,5]]}

 {"tür": "LineString", "koordinatlar": [[20,2321,-87,33], [10,0, 10,0], [10,5, 10,5]]}

Daha fazla bilgi edinmek için [Createlinestring](https://docs.microsoft.com/stream-analytics-query/createlinestring) başvurusunu ziyaret edin.

## <a name="createpoint"></a>CreatePoint

`CreatePoint` işlevi bir enlem ve Boylam kabul eder ve bir haritada çizilebilen bir GeoJSON noktası döndürür. Latitudes ve Longitudes bir **float** veri türü olmalıdır.

Aşağıdaki örnek sorgu, akış girişi verilerinden Latitudes ve Longitudes kullanarak bir nokta oluşturmak için `CreatePoint` kullanır.

```SQL 
SELECT  
     CreatePoint(input.latitude, input.longitude)  
FROM input 
```  

### <a name="input-example"></a>Giriş örneği  
  
|En|MIN|  
|--------------|---------------|  
|3.0|-10,2|  
|-87,33|20,2321|  
  
### <a name="output-example"></a>Çıkış örneği
  
 {"Type": "Point", "koordinatlar": [-10,2, 3,0]}  
  
 {"Type": "Point", "koordinatlar": [20,2321,-87,33]}  

Daha fazla bilgi için, [Createpoint](https://docs.microsoft.com/stream-analytics-query/createpoint) başvurusunu ziyaret edin.

## <a name="createpolygon"></a>CreatePolygon

`CreatePolygon` işlevi noktaları kabul eder ve GeoJSON Çokgen kaydını döndürür. Noktaların sırası sağ halka yönüne veya saat yönünde sayaca uymalıdır. Bildirdikleri sırada bir noktadan diğerine yürüyen düşünün. Çokgenin ortası, tüm saatin solunda olacaktır.

Aşağıdaki örnek sorgu üç noktadan bir çokgen oluşturmak için `CreatePolygon` kullanır. İlk iki nokta el ile oluşturulur ve son nokta giriş verilerinden oluşturulur.

```SQL 
SELECT  
     CreatePolygon(CreatePoint(input.latitude, input.longitude), CreatePoint(10.0, 10.0), CreatePoint(10.5, 10.5), CreatePoint(input.latitude, input.longitude))  
FROM input  
```  

### <a name="input-example"></a>Giriş örneği  
  
|En|MIN|  
|--------------|---------------|  
|3.0|-10,2|  
|-87,33|20,2321|  
  
### <a name="output-example"></a>Çıkış örneği  

 {"Type": "Çokgen", "koordinatlar": [[[-10,2, 3,0], [10,0, 10,0], [10,5, 10,5], [-10,2, 3,0]]]}
 
 {"Type": "Çokgen", "koordinatlar": [[[20,2321,-87,33], [10,0, 10,0], [10,5, 10,5], [20,2321,-87,33]]]}

Daha fazla bilgi edinmek için [Createçokgen](https://docs.microsoft.com/stream-analytics-query/createpolygon) başvurusunu ziyaret edin.


## <a name="st_distance"></a>ST_DISTANCE
`ST_DISTANCE` işlevi, ölçü cinsinden iki işaret arasındaki mesafeyi döndürür. 

Aşağıdaki sorgu, bir gaz istasyonu arabadan 10 km 'tan az olduğunda bir olay oluşturmak için `ST_DISTANCE` kullanır.

```SQL
SELECT Cars.Location, Station.Location 
FROM Cars c  
JOIN Station s ON ST_DISTANCE(c.Location, s.Location) < 10 * 1000
```

Daha fazla bilgi için [ST_DISTANCE](https://docs.microsoft.com/stream-analytics-query/st-distance) başvurusunu ziyaret edin.

## <a name="st_overlaps"></a>ST_OVERLAPS
`ST_OVERLAPS` işlevi iki poligonu karşılaştırır. Çokgenler çakışırsa, işlev bir 1 döndürür. Çokgenler çakışmazsa işlev 0 döndürür. 

Aşağıdaki sorgu, bir derleme olası bir taşması bölgesi içinde olduğunda bir olay oluşturmak için `ST_OVERLAPS` kullanır.

```SQL
SELECT Building.Polygon, Building.Polygon 
FROM Building b 
JOIN Flooding f ON ST_OVERLAPS(b.Polygon, b.Polygon) 
```

Aşağıdaki örnek sorgu bir kavaya bir kaya doğru başlık olduğunda bir olay oluşturur.

```SQL
SELECT Cars.Location, Storm.Course
FROM Cars c, Storm s
JOIN Storm s ON ST_OVERLAPS(c.Location, s.Course)
```

Daha fazla bilgi için [ST_OVERLAPS](https://docs.microsoft.com/stream-analytics-query/st-overlaps) başvurusunu ziyaret edin.

## <a name="st_intersects"></a>ST_INTERSECTS
`ST_INTERSECTS` işlevi iki LineString 'i karşılaştırır. LineString kesiştiğinde, işlev 1 değerini döndürür. LineString kesişmezseniz işlev 0 döndürür.

Aşağıdaki örnek sorgu, bir pakarlı yolun bir kir yolunda kesişip kesişmediğine ilişkin `ST_INTERSECTS` kullanır.

```SQL 
SELECT  
     ST_INTERSECTS(input.pavedRoad, input.dirtRoad)  
FROM input  
```  

### <a name="input-example"></a>Giriş örneği  
  
|Datacenteralanı|stormArea|  
|--------------------|---------------|  
|{"tür": "LineString", "koordinatlar": [[-10,0, 0,0], [0,0, 0,0], [10,0, 0,0]]}|{"tür": "LineString", "koordinatlar": [[0,0, 10,0], [0,0, 0,0], [0,0,-10,0]]}|  
|{"tür": "LineString", "koordinatlar": [[-10,0, 0,0], [0,0, 0,0], [10,0, 0,0]]}|{"tür": "LineString", "koordinatlar": [[-10,0, 10,0], [0,0, 10,0], [10,0, 10,0]]}|  
  
### <a name="output-example"></a>Çıkış örneği  

 1  
  
 0  

Daha fazla bilgi için [ST_INTERSECTS](https://docs.microsoft.com/stream-analytics-query/st-intersects) başvurusunu ziyaret edin.

## <a name="st_within"></a>ST_WITHIN
`ST_WITHIN` işlevi bir noktanın veya çokgenin çokgen içinde olup olmadığını belirler. Çokgen nokta veya Çokgen içeriyorsa, işlev 1 döndürür. Nokta veya Çokgen, belirtilen çokgen içinde yer alıyorsa işlev 0 döndürür.

Aşağıdaki örnek sorgu, teslim hedef noktasının verilen ambar çokgen içinde olup olmadığını anlamak için `ST_WITHIN` kullanır.

```SQL 
SELECT  
     ST_WITHIN(input.deliveryDestination, input.warehouse)  
FROM input 
```  

### <a name="input-example"></a>Giriş örneği  
  
|deliveryDestination|ambarını|  
|-------------------------|---------------|  
|{"Type": "Point", "koordinatlar": [76,6, 10,1]}|{"Type": "Çokgen", "koordinatlar": [[0,0, 0,0], [10,0, 0,0], [10,0, 10,0], [0,0, 10,0], [0,0, 0,0]]}|  
|{"Type": "Point", "koordinatlar": [15,0, 15,0]}|{"Type": "Çokgen", "koordinatlar": [[10,0, 10,0], [20,0, 10,0], [20,0, 20,0], [10,0, 20,0], [10,0, 10,0]]}|  
  
### <a name="output-example"></a>Çıkış örneği  

 0  
  
 1  

Daha fazla bilgi için [ST_WITHIN](https://docs.microsoft.com/stream-analytics-query/st-within) başvurusunu ziyaret edin.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Stream Analytics giriş](stream-analytics-introduction.md)
* [Azure Akış Analizi'ni kullanmaya başlama](stream-analytics-real-time-fraud-detection.md)
* [Azure Akış Analizi işlerini ölçeklendirme](stream-analytics-scale-jobs.md)
* [Azure Akış Analizi Sorgu Dili Başvurusu](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Akış Analizi Yönetimi REST API'si Başvurusu](https://msdn.microsoft.com/library/azure/dn835031.aspx)