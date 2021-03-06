---
title: Jeo-uzamsal işlevlere Azure Stream Analytics giriş
description: Bu makalede Azure Stream Analytics işlerinde kullanılan Jeo-uzamsal işlevler açıklanır.
ms.service: stream-analytics
author: enkrumah
ms.author: ebnkruma
ms.topic: conceptual
ms.date: 12/06/2018
ms.openlocfilehash: a5e65ddacfd4c168d4b97816e448951bacc2b534
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102438549"
---
# <a name="introduction-to-stream-analytics-geospatial-functions"></a>Jeo-uzamsal işlevlere Stream Analytics giriş

Azure Stream Analytics Jeo uzamsal işlevler, Jeo uzamsal verileri akışta gerçek zamanlı analizler sağlar. Yalnızca birkaç satır kodla, karmaşık senaryolar için bir üretim sınıfı çözümü geliştirebilirsiniz. Bu işlevler tüm WKT türlerini ve GeoJSON noktasını, çokgeni ve LineString 'i destekler.

Jeo-uzamsal işlevlerden faydalanabilir senaryolar örnekleri şunlardır:

* Ride paylaşımı
* Fleet yönetimi
* Varlık izleme
* Coğrafi sınırlama
* Hücre siteleri arasında telefon izleme

Stream Analytics sorgu dili yedi yerleşik Jeo uzamsal işlevlere sahiptir: **Createlinestring**, **createpoint**, **createçokgen**, **ST_DISTANCE**, **ST_OVERLAPS**, **ST_INTERSECTS** ve **ST_WITHIN**.

## <a name="createlinestring"></a>CreateLineString

`CreateLineString`İşlev noktaları kabul eder ve bir haritada çizgi olarak çizilebilen bir GeoJSON LineString döndürür. LineString oluşturmak için en az iki noktanız olmalıdır. LineString noktaları sırayla bağlanır.

Aşağıdaki sorgu, `CreateLineString` üç noktayı kullanarak bir LineString oluşturmak için kullanır. İlk nokta akış girişi verilerinden oluşturulur, diğer ikisi el ile oluşturulur.

```SQL 
SELECT  
     CreateLineString(CreatePoint(input.latitude, input.longitude), CreatePoint(10.0, 10.0), CreatePoint(10.5, 10.5))  
FROM input  
```  

### <a name="input-example"></a>Giriş örneği  
  
|latitude|MIN|  
|--------------|---------------|  
|3.0|-10,2|  
|-87,33|20,2321|  
  
### <a name="output-example"></a>Çıkış örneği  

 {"tür": "LineString", "koordinatlar": [[-10,2, 3,0], [10,0, 10,0], [10,5, 10,5]]}

 {"tür": "LineString", "koordinatlar": [[20,2321,-87,33], [10,0, 10,0], [10,5, 10,5]]}

Daha fazla bilgi edinmek için [Createlinestring](/stream-analytics-query/createlinestring) başvurusunu ziyaret edin.

## <a name="createpoint"></a>CreatePoint

`CreatePoint`İşlevi bir enlem ve Boylam kabul eder ve bir haritada çizilebilen bir GeoJSON noktası döndürür. Latitudes ve Longitudes bir **float** veri türü olmalıdır.

Aşağıdaki örnek sorgu, `CreatePoint` akış girişi verilerinden Latitudes ve Longitudes kullanarak bir nokta oluşturmak için kullanır.

```SQL 
SELECT  
     CreatePoint(input.latitude, input.longitude)  
FROM input 
```  

### <a name="input-example"></a>Giriş örneği  
  
|latitude|MIN|  
|--------------|---------------|  
|3.0|-10,2|  
|-87,33|20,2321|  
  
### <a name="output-example"></a>Çıkış örneği
  
 {"Type": "Point", "koordinatlar": [-10,2, 3,0]}  
  
 {"Type": "Point", "koordinatlar": [20,2321,-87,33]}  

Daha fazla bilgi için, [Createpoint](/stream-analytics-query/createpoint) başvurusunu ziyaret edin.

## <a name="createpolygon"></a>CreatePolygon

`CreatePolygon`İşlev noktaları kabul eder ve GeoJSON Çokgen kaydını döndürür. Noktaların sırası sağ halka yönüne veya saat yönünde sayaca uymalıdır. Bildirdikleri sırada bir noktadan diğerine yürüyen düşünün. Çokgenin ortası, tüm saatin solunda olacaktır.

Aşağıdaki örnek sorgu `CreatePolygon` üç noktadan bir çokgen oluşturmak için kullanır. İlk iki nokta el ile oluşturulur ve son nokta giriş verilerinden oluşturulur.

```SQL 
SELECT  
     CreatePolygon(CreatePoint(input.latitude, input.longitude), CreatePoint(10.0, 10.0), CreatePoint(10.5, 10.5), CreatePoint(input.latitude, input.longitude))  
FROM input  
```  

### <a name="input-example"></a>Giriş örneği  
  
|latitude|MIN|  
|--------------|---------------|  
|3.0|-10,2|  
|-87,33|20,2321|  
  
### <a name="output-example"></a>Çıkış örneği  

 {"Type": "Çokgen", "koordinatlar": [[[-10,2, 3,0], [10,0, 10,0], [10,5, 10,5], [-10,2, 3,0]]]}
 
 {"Type": "Çokgen", "koordinatlar": [[[20,2321,-87,33], [10,0, 10,0], [10,5, 10,5], [20,2321,-87,33]]]}

Daha fazla bilgi edinmek için [Createçokgen](/stream-analytics-query/createpolygon) başvurusunu ziyaret edin.


## <a name="st_distance"></a>ST_DISTANCE
`ST_DISTANCE`İşlevi, ölçü cinsinden iki geometri arasındaki mesafeyi döndürür. 

Aşağıdaki sorgu, `ST_DISTANCE` bir gaz istasyonu arabadan 10 km 'tan az olduğunda bir olay oluşturmak için kullanır.

```SQL
SELECT Cars.Location, Station.Location 
FROM Cars c  
JOIN Station s ON ST_DISTANCE(c.Location, s.Location) < 10 * 1000
```

Daha fazla bilgi edinmek için [ST_DISTANCE](/stream-analytics-query/st-distance) başvurusunu ziyaret edin.

## <a name="st_overlaps"></a>ST_OVERLAPS
`ST_OVERLAPS`İşlev iki geometrileri karşılaştırır. Geometriler çakışırsa, işlev bir 1 döndürür. Geometriler örtütürse, işlev 0 döndürür. 

Aşağıdaki sorgu, `ST_OVERLAPS` bir derleme olası bir taşması bölgesi içindeyse bir olay oluşturmak için kullanır.

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

Daha fazla bilgi edinmek için [ST_OVERLAPS](/stream-analytics-query/st-overlaps) başvurusunu ziyaret edin.

## <a name="st_intersects"></a>ST_INTERSECTS
`ST_INTERSECTS`İşlev iki geometrileri karşılaştırır. Geometriler kesiştiğinde, işlev 1 değerini döndürür. Geometriler birbirleriyle kesişmezseniz işlev 0 döndürür.

Aşağıdaki örnek sorgu, `ST_INTERSECTS` bir geçen yolun bir kir yolunda kesişip kesişmediğine yönelik olarak kullanılır.

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

Daha fazla bilgi edinmek için [ST_INTERSECTS](/stream-analytics-query/st-intersects) başvurusunu ziyaret edin.

## <a name="st_within"></a>ST_WITHIN
`ST_WITHIN`İşlevi, bir geometrinin başka bir geometri dahilinde olup olmadığını belirler. Birincisi en sonda yer alıyorsa, işlev 1 döndürür. İlk geometri son bir içinde yer alıyorsa işlev 0 döndürür.

Aşağıdaki örnek sorgu, `ST_WITHIN` teslim hedef noktasının belirtilen ambar çokgen içinde olup olmadığını belirlemede kullanır.

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

Daha fazla bilgi edinmek için [ST_WITHIN](/stream-analytics-query/st-within) başvurusunu ziyaret edin.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Stream Analytics giriş](stream-analytics-introduction.md)
* [Azure Akış Analizi'ni kullanmaya başlama](stream-analytics-real-time-fraud-detection.md)
* [Azure Akış Analizi işlerini ölçeklendirme](stream-analytics-scale-jobs.md)
* [Azure Akış Analizi Sorgu Dili Başvurusu](/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Akış Analizi Yönetimi REST API'si Başvurusu](/rest/api/streamanalytics/)
