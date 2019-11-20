---
title: Azure Stream Analytics ile bölge sınırlama ve jeo uzamsal toplama
description: Bu makalede, bölge sınırlaması ve jeo-uzamsal toplama için Azure Stream Analytics nasıl kullanılacağı açıklanır.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: d44b2fae677554594f0cc280c1129bbd6effddf2
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72935067"
---
# <a name="geofencing-and-geospatial-aggregation-scenarios-with-azure-stream-analytics"></a>Azure Stream Analytics ile bölge sınırlama ve jeo uzamsal toplama senaryoları

Yerleşik Jeo-uzamsal işlevlerle, filo yönetimi, arttırıldığında paylaşımı, bağlantılı otomobiller ve varlık izleme gibi senaryolar için uygulama oluşturmak üzere Azure Stream Analytics kullanabilirsiniz.

## <a name="geofencing"></a>Coğrafi Sınırlama

Azure Stream Analytics, bulutta ve IoT Edge çalışma zamanında düşük Gecikmeli gerçek zamanlı bölge sınırlaması destekler.

### <a name="geofencing-scenario"></a>Bölge sınırlaması senaryosu

Bir üretim şirketinin, binalarında varlıkları izlemesi gerekir. Her cihazı GPS ile donatılmıştır ve bir cihaz belirli bir alandan ayrıldığında bildirim almak istiyor.

Bu örnekte kullanılan başvuru verileri, binalara ve binalarda izin verilen cihazlara ilişkin bölge bilgilerine sahiptir. Başvuru verilerinin statik veya yavaş değişiklik olduğunu unutmayın. Bu senaryo için statik başvuru verileri kullanılır. Bir veri akışı, cihaz KIMLIĞINI ve geçerli konumunu sürekli olarak yayar.

### <a name="define-geofences-in-reference-data"></a>Başvuru verilerinde bölge tanımlar

Bir bölge bölge, GeoJSON nesnesi kullanılarak tanımlanabilir. Uyumluluk sürümü 1,2 ve üzeri olan işler için, bölge 'lar `NVARCHAR(MAX)`olarak Iyi bilinen metin (WKT) kullanılarak da tanımlanabilir. WKT, uzamsal verileri metinsel biçimde temsil etmek için kullanılan bir Open Geospatial Consortium (OGC) standardıdır.

Yerleşik Jeo-uzamsal işlevler, bir öğenin belirli bir bölge poligonun içinde mi yoksa dışında mı olduğunu bulmak için tanımlı bölge sınırlarını kullanabilir.

Aşağıdaki tabloda, Azure Blob depolama veya bir Azure SQL tablosu 'nda depolanabilecek bölge başvurusu verilerinin bir örneği verilmiştir. Her site bir jeo-uzamsal çokgen ile temsil edilir ve her cihaz izin verilen bir site KIMLIĞIYLE ilişkilendirilir.

|SiteId|SiteName|Bölge sınırı|Alloweddeviceıd|
|------|--------|--------|---------------|
|1|"Redmond derleniyor 41"|"ÇOKGEN ((-122.1337357922017 47.63782998329432,-122.13373042778369 47.637634793257305,-122.13346757130023 47.637642022530954,-122.13348902897235 47.637508280806806,-122.13361777500506 47.637508280806806,-122.13361241058703 47.63732393354484,-122.13265754417773 47.63730947490855,-122.13266290859576 47.637519124743164,-122.13302232460376 47.637515510097955,-122.13301696018573 47.63764925180358,-122.13272728161212 47.63764925180358,-122.13274873928424 47.63784082716388,-122.13373579220172 47.63782998329432))"|Kenarı|
|2|"Redmond derleniyor 40"|"ÇOKGEN ((-122.1336154507967 47.6366745947009,-122.13361008637867 47.636483015064535,-122.13349206918201 47.636479400347675,-122.13349743360004 47.63636372927573,-122.13372810357532 47.63636372927573,-122.13373346799335 47.63617576323771,-122.13263912671528 47.63616491902258,-122.13264985555134 47.63635649982525,-122.13304682248554 47.636367344000604,-122.13305218690357 47.63650831807564,-122.13276250832996 47.636497473929516,-122.13277323716602 47.63668543881025,-122.1336154507967 47.6366745947009))"|A|
|3|"Redmond derleme 22"|"ÇOKGEN ((-122.13611660248233 47.63758544698554,-122.13635263687564 47.6374083293018,-122.13622389084293 47.63733603619712,-122.13622389084293 47.63717699101473,-122.13581619507266 47.63692757827657,-122.13559625393344 47.637046862778135,-122.13569281345798 47.637144458985965,-122.13570890671207 47.637314348246214,-122.13611660248233 47.63758544698554))"|,|

### <a name="generate-alerts-with-geofence"></a>Bölge sınırı ile uyarı oluşturma

Cihazlar, `DeviceStreamInput`adlı bir akış aracılığıyla her dakikada kendi KIMLIKLERINI ve konumlarını yayabilir. Aşağıdaki tablo bir giriş akışıdır.

|DeviceID|Coğrafi konum|
|--------|-----------|
|A|"NOKTA (-122.13292341559497 47.636318374032726)"|
|Kenarı|"NOKTA (-122.13338475554553 47.63743531308874)"|
|,|"NOKTA (-122.13354001095752 47.63627622505007)"|

Cihaz akışını bölge başvurusu verileriyle birleştiren bir sorgu yazabilir ve bir cihaz izin verilen bir bina dışında olduğunda bir uyarı oluşturur.

```SQL
SELECT DeviceStreamInput.DeviceID, SiteReferenceInput.SiteID, SiteReferenceInput.SiteName 
INTO Output
FROM DeviceStreamInput 
JOIN SiteReferenceInput
ON st_within(DeviceStreamInput.GeoPosition, SiteReferenceInput.Geofence) = 0
WHERE DeviceStreamInput.DeviceID = SiteReferenceInput.AllowedDeviceID
```

Aşağıdaki resim, bölge sınırlarını temsil eder. Cihazların akış veri girişine göre nerede olduğunu görebilirsiniz.

![Bölge oluşturma](./media/geospatial-scenarios/building-geofences.png)

"C" aygıtı, başvuru verilerine göre izin verilmeyen derleme KIMLIĞI 2 içinde bulunuyor. Bu cihaz, derleme KIMLIĞI 3 içinde bulunmalıdır. Bu işi çalıştırmak, bu belirli ihlalin için bir uyarı oluşturur.

### <a name="site-with-multiple-allowed-devices"></a>Birden çok izin verilen cihaz içeren site

Bir site birden çok cihaza izin veriyorsa, bir cihaz kimlikleri dizisi `AllowedDeviceID` tanımlanabilir ve `WHERE` yan tümcesinde Kullanıcı tanımlı bir Işlev, Stream cihaz KIMLIĞININ bu listedeki herhangi bir cihaz KIMLIĞIYLE eşleşip eşleşmediğini doğrulamak için kullanılabilir. Daha fazla bilgi için, bulut işleri için [JavaScript UDF](stream-analytics-javascript-user-defined-functions.md) öğreticisini ve Edge işleri için [ C# UDF](stream-analytics-edge-csharp-udf.md) öğreticisini görüntüleyin.

## <a name="geospatial-aggregation"></a>Jeo-uzamsal toplama

Azure Stream Analytics, bulutta ve IoT Edge çalışma zamanında düşük Gecikmeli gerçek zamanlı Jeo uzamsal toplamayı destekler.

### <a name="geospatial-aggregation-scenario"></a>Jeo-uzamsal toplama senaryosu

Bir cab şirketi, büyük bir zamanda daha önce daha fazla talep yaşayan şehirlerin alanlarında ne gibi bir grup için bir gerçek zamanlı uygulama oluşturmak istiyor.

Şirket, şehrin mantıksal bölgelerini başvuru verileri olarak depolar. Her bölge bir RegionID, RegionName ve geofence tarafından tanımlanır.

### <a name="define-the-geofences"></a>Bölge sınırlarını tanımlama

Aşağıdaki tabloda, Azure Blob depolama veya bir Azure SQL tablosu 'nda depolanabilecek bölge başvurusu verilerinin bir örneği verilmiştir. Her bölge, akış verilerinden gelen isteklerle bağıntı kurmak için kullanılan bir jeo-uzamsal Çokgen tarafından temsil edilir.

Bu çokgenler yalnızca başvuru amaçlıdır ve gerçek şehir mantıksal veya fiziksel ayrımlarını temsil etmez.

|RegionID|RegionName|Bölge sınırı|
|--------|----------|--------|
|1|Soho|"ÇOKGEN ((-74.00279525078275 40.72833625216264,-74.00547745979765 40.721929158663244,-74.00125029839018 40.71893680218994,-73.9957785919998 40.72521409075776,-73.9972377137039 40.72557184584898,-74.00279525078275 40.72833625216264) )"|
|2|"CHINATOWN"|"ÇOKGEN ((-73.99712367114876 40.71281582267133,-73.9901070123658 40.71336881907936,-73.99023575839851 40.71452359088633,-73.98976368961189 40.71554823078944,-73.99551434573982 40.717337246783735,-73.99480624255989 40.718491949759304,-73.99652285632942 40.719109951574,-73.99776740131233 40.7168005470334,-73.99903340396736 40.71727219249899,-74.00193018970344 40.71938642421256,-74.00409741458748 40.71688186545551,-74.00051398334358 40.71517415773184,-74.0004281526551 40.714377212470005,-73.99849696216438 40.713450141693166,-73.99748845157478 40.71405192594819,-73.99712367114876 40.71281582267133))"|
|3|"Tribeca"|"ÇOKGEN ((-74.01091641815208 40.72583120006787,-74.01338405044578 40.71436586362705,-74.01370591552757 40.713617702123415,-74.00862044723533 40.711308107057235,-74.00194711120628 40.7194238654018,-74.01091641815208 40.72583120006787))"|

### <a name="aggregate-data-over-a-window-of-time"></a>Bir zaman penceresi üzerinde verileri toplama

Aşağıdaki tabloda, "rides" akış verileri yer almaktadır.

|UserID|FromLocation|ToLocation|Üçlü Requestedtime|
|------|------------|----------|-----------------|
|A|"NOKTA (-74.00726861389182 40.71610611981975)"|"NOKTA (-73.98615095917779 40.703107386025835)"|"2019-03-12T07:00:00Z"|
|Kenarı|"NOKTA (-74.00249841021645 40.723827238895666)"|"NOKTA (-74.01160699942085 40.71378884930115)"|"2019-03-12T07:01:00Z"|
|,|"NOKTA (-73.99680120565864 40.716439898624024)"|"NOKTA (-73.98289663412544 40.72582343969828)"|"2019-03-12T07:02:00Z"|
|TID|"NOKTA (-74.00741090068288 40.71615626755086)"|"NOKTA (-73.97999843120539 40.73477895807408)"|"2019-03-12T07:03:00Z"|

Aşağıdaki sorgu, cihaz akışını bölge başına başvuru verileriyle birleştirir ve her dakika 15 dakikalık bir zaman penceresinde her bölge için istek sayısını hesaplar.

```SQL
SELECT count(*) as NumberOfRequests, RegionsRefDataInput.RegionName 
FROM UserRequestStreamDataInput
JOIN RegionsRefDataInput 
ON st_within(UserRequestStreamDataInput.FromLocation, RegionsRefDataInput.Geofence) = 1
GROUP BY RegionsRefDataInput.RegionName, hoppingwindow(minute, 1, 15)
```

Bu sorgu, şehir içindeki her bölgeye göre son 15 dakika boyunca her dakikada istek sayısını verir. Bu bilgiler Power BI Pano tarafından kolayca görüntülenebilir veya Azure işlevleri gibi hizmetlerle tümleştirmeyle SMS mesajı olarak tüm sürücülere bağlanabilir.

Aşağıdaki görüntüde, Power BI panoya olan sorgunun çıktısı gösterilmektedir. 

![Power BI panosundaki sonuç çıktısı](./media/geospatial-scenarios/power-bi-output.png)


## <a name="next-steps"></a>Sonraki adımlar

* [Jeo-uzamsal işlevlere Stream Analytics giriş](stream-analytics-geospatial-functions.md)
* [Jeo-uzamsal Işlevler (Azure Stream Analytics)](https://docs.microsoft.com/stream-analytics-query/geospatial-functions)
