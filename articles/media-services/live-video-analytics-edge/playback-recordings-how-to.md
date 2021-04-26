---
title: Kayıtları kayıttan yürütme-Azure
description: Sürekli video kaydı için IoT Edge, canlı video analizi 'ni kullanarak haftalık veya aylık bir videoyu buluta kaydedebilirsiniz. Ayrıca kaydınızı, olay tabanlı kayıt aracılığıyla ilgilendiğiniz kliplerle sınırlayabilirsiniz. Bu makalede kayıtları kayıttan yürütme hakkında bilgi gösterilmektedir.
ms.topic: how-to
ms.date: 04/27/2020
ms.openlocfilehash: 0351f10d9fac3ad7e3b4fdd5fd549eb7c0023694
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99490911"
---
# <a name="playback-of-recordings"></a>Kayıtların kayıttan yürütülmesi 

## <a name="pre-read"></a>Önceden oku  

* [Video kayıttan yürütme](video-playback-concept.md)
* [Sürekli video kaydı](continuous-video-recording-concept.md)
* [Olay tabanlı video kaydı](event-based-video-recording-concept.md)

## <a name="background"></a>Arka Plan  

[Sürekli video kaydı](continuous-video-recording-concept.md) (CVR) için IoT Edge, canlı video analizlerini kullanarak haftalık veya aylık bir videoyu buluta kaydedebilirsiniz. Ayrıca kaydınızı, [olay tabanlı video kaydı](event-based-video-recording-concept.md) (EVR) aracılığıyla ilgilendiğiniz kliplerle sınırlayabilirsiniz. 

Medya hizmeti hesabınız bir Azure depolama hesabına bağlı ve buluta video kaydettiğinizde içerik bir [medya hizmeti](terminology.md#asset)varlığına yazılır. Media Services, kayıt tamamlandıktan sonra ya da kayıt devam ederken [Bu içeriği akışa](terminology.md#streaming)almanıza olanak sağlar. Media Services, oynatma cihazları (istemciler) için HLS veya MPEG-DASH protokolleri aracılığıyla akış sunmaya yönelik gerekli özellikleri sağlar. Daha fazla bilgi için bkz. [video kayıttan yürütme](video-playback-concept.md) makalesi. Media Service API 'Lerini, istemciler tarafından video & ses çalmak için kullanılan gerekli akış URL 'Lerini oluşturmak için kullanırsınız. Bir varlık için akış URL 'SI oluşturmak için bkz. [akış bulucu ve derleme URL 'leri oluşturma](../latest/create-streaming-locator-build-url.md). Bir varlık için akış URL 'si oluşturulduktan sonra, URL 'nin ilişkilendirmesini içerik yönetim sisteminizdeki video kaynağıyla (kamera) birlikte depolamanız gerekir.

Örneğin, HLS aracılığıyla akış sırasında akış URL 'SI şöyle görünür `https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl).m3u8` . MPEG-DASH için bu şekilde görünür `https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=mpd-time-cmaf).mpd` .

Bu, diğer şeyleri kapsayan, teslim edilen akışın genel süresini ve önceden kaydedilmiş içeriği veya ' canlı ' akışını temsil edip etmediğini açıklayan bir bildirim dosyası döndürür.

### <a name="live-vs-vod"></a>Canlı ve VoD karşılaştırması  

HLS ve MPEG-DASH gibi akış protokolleri, canlı video akışı ve TV programları ve filmler gibi isteğe bağlı/önceden kaydedilmiş içerik akışı gibi senaryoları işleyecek şekilde yazıldı. Canlı videolar için, HLS ve MPEG-DASH istemcileri, ' en son ' zaman onlarından yürütmeye başlamak üzere tasarlanmıştır. Ancak filmlerle, görüntüleyiciler baştan başlayıp seçim yapacaksa bu baştan başa geçebilmelidir. HLS ve MPEG-DASH bildirimlerinin, videonun canlı bir akışı temsil ettiğini veya önceden kaydedilmiş içerik olup olmadığını belirten bayrakları vardır.
Bu kavram Ayrıca, IoT Edge üzerinde canlı video analizi kullanılarak kaydedilmiş video içeren varlıklardan HLS ve MPEG-DASH akışları için de geçerlidir.

## <a name="browsing-and-selective-playback-of-recordings"></a>Kayıtlar göz atma ve seçmeli kayıttan yürütme  

Video kaydetmek için IoT Edge üzerinde canlı video analizlerini kullandığınız senaryoyu göz önünde bulundurun. Bu, bir okulun açık olduğu günde yalnızca Pazartesi ile 10:00'DA, tüm akademik yıl boyunca videoyu kaydedebilir. Ya da belki de uluslararası tatiller için yalnızca 7-7 saat arasında video kaydediyorsanız. Bu iki senaryodan birinde, video kaydınızı gözatmaya ve görüntülemeye çalışırken şunlar gerekir:

* Bir kayıtta hangi tarih/saat videonun olduğunu belirlemek için bir yol.
* Kayıttan yürütmeye yönelik bir bölüm (örneğin, yeni yılda 00 ' a kadar saat) seçmek için bir yol.

Media Services, ilk sorunu gidermek için bir sorgu API 'SI (kullanılabilirlik kümesi) ve ikincisi ele almak için zaman aralığı filtreleri (startTime, bitişsaati) sağlar.

## <a name="query-api"></a>Sorgu API'si 

CVR kullanılırken, kayıttan yürütme aygıtları (istemciler) tüm kaydın kayıttan yürütülmesini kapsayan bir bildirim isteyememektedir.  Çok yıllık bir kayıt, kayıttan yürütme için çok büyük olan bir bildirim dosyası oluşturur ve istemci tarafında kullanılabilir bölümleri ayrıştırmaktır.  İstemcinin çok fazla tahmin olmadan geçerli istekler yapabilmesi için kayıtta hangi DateTime aralıklarının veri olduğunu bilmesi gerekir. Bu, yeni sorgu API 'sinin geldiği yerdir. istemciler artık içeriği saptamak için bu sunucu tarafı API 'sini kullanabilir.

Duyarlık değeri şunlardan biri olabilir: Year, month, Day veya Full (aşağıda gösterildiği gibi). 

|Duyarlık|yıl|ay|gün|tümünü|
|---|---|---|---|---|
|Sorgu|`/availableMedia?precision=year&startTime=2018&endTime=2019`|`/availableMedia?precision=month& startTime=2018-01& endTime=2019-02`|`/availableMedia?precision=day& startTime=2018-01-15& endTime=2019-02-02`|`/availableMedia?precision=full& startTime=2018-01-15T10:08:11.123& endTime=2019-01-015T12:00:01.123`|
|Yanıt|`{  "timeRanges":[{ "start":"2018", "end":"2019" }]}`|`{  "timeRanges":[{ "start":"2018-03", "end":"2019-01" }]}`|`{  "timeRanges":[    { "start":"2018-03-01", "end":"2018-03-07" },    { "start":"2018-03-09", "end":"2018-03-31" }  ]}`|Tam uygunluk yanıtı. Hiç boşluk yoksa, başlangıç startTime olur ve son olarak bitebileceğiniz olur.|
|Kısıtlamalar|&#x2022;startTime <= bitişsaati<br/>&#x2022;her Ikisi de YYYY biçiminde olmalıdır; Aksi takdirde hata döndürür.<br/>&#x2022;değerler herhangi bir sayıda yıl olabilir.<br/>&#x2022;değerler dahil değildir.|&#x2022;startTime <= bitişsaati<br/>&#x2022;her Ikisi de YYYY-MM biçiminde olmalı, aksi takdirde hata döndürülür.<br/>&#x2022;değerler en fazla 12 ay olabilir.<br/>&#x2022;değerler dahil değildir.|&#x2022;startTime <= bitişsaati<br/>&#x2022;her Ikisi de YYYY-AA-GG biçiminde olmalı, aksi takdirde hata döndürülür.<br/>&#x2022;değerler en fazla 31 gün olabilir.<br/>Değerler dahildir.|&#x2022;startTime < bitişsaati<br/>&#x2022;değerler en fazla 25 saat ayrı olabilir.<br/>&#x2022;değerler dahil değildir.|

#### <a name="additional-request-format-considerations"></a>Ek istek biçimi konuları

* Her zaman UTC 'de
* Duyarlık sorgu dizesi parametresi gereklidir.  
* StartTime ve bitişsaati sorgu dizesi parametreleri, month, Day ve tam duyarlık değerleri için gereklidir.  
* StartTime ve bitişsaati sorgu dizesi parametreleri, yıl duyarlık değeri için isteğe bağlıdır (hiçbiri, ya da her ikisi desteklenir).  

    * StartTime atlanırsa, kaydın ilk yılı kabul edilir.
    * Bitişsaati atlanırsa, kaydın son yılı olduğu varsayılır.
    * Örneğin, kaydınız 2011 ' de başlatılmışsa ve 2020 ' e kadar devam etseydi:

        * /Kullanılabilirliği blemeçya? duyarlık = yıl,/kullanılabilirliği blemeçya? Precision = Year&startTime = 2011&BitişZamanı = 2020
        * /Kullanılabilirliği blemeçya? Precision = Year&startTime = 2015,/kullanılabilirliği blemeçya? Precision = Year&startTime = 2015&bitişsaati = 2020
        * /Kullanılabilirliği blemeçya? Precision = Year&BitişZamanı = 2018,/kullanılabilirliği blemeçya? Precision = Year&startTime = 2011&bitişsaati = 2018

Zaman aralıkları için kullanılabilir medya verisi yoksa boş bir liste döndürülür.

Varlık bir medya grafiğinden bir kayıt içermiyorsa, bu özelliğin yalnızca bir medya grafiği aracılığıyla kaydedilen içerik için kullanılabilir olduğunu belirten bir hata iletisiyle HTTP 400 yanıtı döndürülür.

Parametreler tarafından belirtilen süre, belirli bir sorgu türü için en uzun zaman aralığı izin verilenden daha büyükse, istenen sorgu türü için izin verilen maksimum zaman aralığını açıklayan bir hata iletisiyle HTTP 400 döndürülür.

Zaman aralığının verilen parametreler için geçerli olduğu varsayıldığında, kayıt içindeki verilerin zaman penceresi dışında kalan sorgular için herhangi bir hata döndürülmez.  Kaydın 7 saat önce başlatılması, ancak müşterinin Şu anda {UtcNow – 24 saat} üzerinden kullanılabilir medya isteyip istemediği anlamına gelir. Bu durumda yalnızca {UtcNow – 7} saat veri döndürüldük.

### <a name="response-format"></a>Yanıt biçimi  

Kullanılabilirliği Blemeçya çağrısı bir zaman değeri kümesi döndürür.

Yanıt, istenen duyarlığa bağlı olarak, Timeanges değerleri yıl için ISO 8601 UTC tarihi (YYYY biçiminde), month (YYYY-MM biçiminde) veya gün (yyyy-aa-gg) dizisi olacak şekilde bir JSON gövdesi olacaktır.  Tam timeRanges, hem ISO 8601 UTC Tarih (YYYY-MM-DDThh: mm: ss. sssZ biçiminde) olarak biçimlendirilen bir başlangıç ve bitiş değeri içerir.

Kullanılabilirlik Blemeçya sorgusu için, API, video zaman çizelgesinde bir anahtara sahip olur. Zaman çizelgesindeki tüm süreksizlik, yanıtta boşluk olarak görünür.

#### <a name="response-example-for-availablemedia"></a>Kullanılabilirlik Blemeçya için yanıt örneği

##### <a name="example-1-live-recording-with-no-gaps"></a>Örnek 1: boşluk olmadan canlı kayıt

Aşağıda, kaydın %100 ' nin tamamlandığı, 21 Aralık 2019 ' ye kadar tüm kullanılabilir medyanın gösterildiği bir yanıt verilmiştir. Yanıtta yalnızca bir başlangıç/bitiş çifti vardır.

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=full&startTime=2019-12-21T00:00:00Z&endTime=2019-12-22T00:00:00Z
{
   "timeRanges":[
   {
         "start":"2019-12-21T00:00:00.000Z", 
         "end":"2019-12-22T00:00:00.000Z"
    }
   ]
}
```

##### <a name="example-2-live-recording-with-a-2-second-gap"></a>Örnek 2:2 saniyelik bir boşluklu canlı kayıt

Bir nedenden dolayı, kameranın günde 2 saniyelik bir aralığa yönelik geçerli videoyu gönderemediği hakkında daha fazla düşünün. 21 Aralık 2019 ' de kullanılabilir medyanın tümünü gösteren bir yanıt aşağıda verilmiştir:

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=full&startTime=2019-12-21T00:00:00Z&endTime=2019-12-22T00:00:00Z
{
   "timeRanges":[
    {
         "start":"2019-12-21T00:00:00Z", 
         "end":"2019-12-21T04:01:08Z"
    },
    {
         "start":"2019-12-21T04:01:10Z", 
         "end":"2019-12-22T00:00:00Z"
    }
   ]
}
```

##### <a name="example-3-live-recording-with-an-8-hour-gap"></a>Örnek 3:8 saatlik boşluklu canlı kayıt

Kamera ve/veya şirket içi tesis 'nın gün boyunca 8 saatlik bir süre boyunca güç kaybettiğini ve bir yedekleme güç kaynağı olmadığını varsayalım. Bu, bir gün boyunca kullanılabilir olan tüm medyayı gösteren bir yanıt aşağıda verilmiştir

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=full&startTime=2019-12-21T00:00:00Z&endTime=2019-12-22T00:00:00Z
{
   "timeRanges":[
    {
         "start":"2019-12-21T00:00:00Z", 
         "end":"2019-12-21T04:00:00Z"
    },
    {
         "start":"2019-12-21T12:00:00Z", 
         "end":"2019-12-22T00:00:00Z"
    }
   ]
}
```

#### <a name="example-4-live-recording-where-no-video-is-recorded-over-summer-holidays"></a>Örnek 4: yaz tatillerine video kaydedilmeyen canlı kayıt

Video ' ı yalnızca okulda oturum açtığınızda ve kayıt 17 Haziran 'dan 6 Eylül 'ye kadar durdurulduğunu varsayalım. Kullanılabilir aylar için bir sorgu şöyle görünür:

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=month&startTime=2019-01&endTime=2019-12
{
   "timeRanges":[
    {
         "start":"2019-01", 
         "end":"2019-06"
    },
    {
         "start":"2019-09", 
         "end":"2019-12"
    }
   ]
}
```

Daha sonra Haziran ayında kullanılabilir günler sorulursa şunları görürsünüz:

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=day&startTime=2019-06-01&endTime=2019-06-30
{
   "timeRanges":[
    {
         "start":"2019-06-01", 
         "end":"2019-06-17"
    }
   ]
}
```

##### <a name="example-5-live-recording-where-no-video-is-recorded-over-weekends-or-holidays"></a>Örnek 5: hafta sonları veya tatiller üzerinde hiçbir videonun kaydedilmediği canlı kayıt

Videoyu yalnızca iş saatlerinde kaydettiğiniz varsayın. Kullanılabilir günler için bir sorgu şöyle görünür

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=day&startTime=2020-02-01&endTime=2020-02-29
{
   "timeRanges":[
    {
         "start":"2020-02-03", 
         "end":"2020-02-07"
    },
    {
         "start":"2020-02-10", 
         "end":"2020-02-14"
    },
    {
         "start":"2020-02-18", // Monday Feb 17th was a holiday
         "end":"2020-02-21"
    },
    {
         "start":"2020-02-24", 
         "end":"2020-02-28"
    }
   ]
}
```

## <a name="time-range-filters"></a>Zaman aralığı filtreleri

Yukarıda bahsedildiği gibi, bu filtreler, kayıttan yürütme için kaydınızın bölümlerini seçmenize yardımcı olur (örneğin, yeni yıllarca 00 ' a kadar gün). HLS aracılığıyla akış yaparken akış URL 'SI şöyle görünür `https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl).m3u8` . Kaydınızın bir kısmını seçmek için, şöyle bir startTime ve bir bitişsaati parametresi eklersiniz: `https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2019-12-21T08:00:00Z,endTime=2019-12-21T10:00:00Z).m3u8` . Bu nedenle, zaman aralığı filtreleri, akış bildiriminde yer alan kayıt zaman çizelgesinin bölümünü anlatmak için kullanılan URL değiştiricilerdir:

* `starttime` , döndürülen bildirimde video zaman çizelgesinin istenen başlangıç saatini açıklayan bir ISO 8601 tarih saat damgası.
* `endtime` , bildirimde döndürülen video zaman çizelgesinin istenen bitiş saatini açıklayan bir ISO 8601 tarih saat damgası.

Bu bildirimin en fazla uzunluğu (Time) 24 saati aşamaz.

Filtrelerdeki kısıtlamalar aşağıda verilmiştir.

|startTime| endTime |Sonuç|
|---|---|---|
|Olmamalıdır |Olmamalıdır |En son 4 saat uzunluğuna kadar, varlık içinde videonun en son bölümünü döndürür.<br/><br/>Varlık üzerine yazılmamış (yeni video verisi olmaksızın) son zamanlarda bir isteğe bağlı (VoD) bildirimi döndürülür. Aksi takdirde, canlı bir bildirim döndürülür.|
|Görüntülemeyen|Olmamalıdır|    Böyle bir bildirimin 24 saatten kısa olması halinde, startTime değerinden daha yeni olan videonun kullanılabildiği bir bildirim döndürün.<br/>Bildirimin uzunluğu 24 saati aşarsa bir hata döndürün.<br/>Varlık üzerine yazılmamış (yeni video verisi olmaksızın) son zamanlarda bir isteğe bağlı (VoD) bildirimi döndürülür. Aksi takdirde, canlı bir bildirim döndürülür.
|Olmamalıdır|Görüntülemeyen |Hata – startTime varsa, bitişsaati zorunludur.|
|Görüntülemeyen|Görüntülemeyen|StartTime ve bitişsaati arasında videonun kullanılabildiği bir VoD bildirimi döndürün.<br/>Span (startTime, bitişsaati) 24 saati aşamaz.|

### <a name="response-examples"></a>Yanıt örnekleri  

#### <a name="example-1-live-recording-with-no-gaps"></a>Örnek 1: boşluk olmadan canlı kayıt

Aşağıda, kaydın %100 ' nin tamamlandığı, 21 Aralık 2019 ' ye kadar tüm kullanılabilir medyanın gösterildiği bir yanıt verilmiştir. Yanıtta yalnızca bir başlangıç/bitiş çifti vardır.

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=full&startTime=2019-12-21T00:00:00Z&endTime=2019-12-22T00:00:00Z
{
   "timeRanges":[
   {
         "start":"2019-12-21T00:00:00Z", 
         "end":"2019-12-22T00:00:00Z"
    }
   ]
}
```

Kayıt sürekli olduğunda, Aralık 24 saat veya daha az olduğu sürece, bu başlangıç/bitiş çiftinin içindeki zaman içindeki her bir bölüm için HLS veya DASH bildirimleri isteyebilirsiniz. Yukarıdaki 4 saatlik HLS bildirim isteğine yönelik bir örnek şöyle olacaktır:

`GET https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2019-12-21T14:00:00.000Z,endTime=2019-12-21T18:00:00.000Z).m3u8`

#### <a name="example-2-live-recording-with-a-2-second-gap"></a>Örnek 2:2 saniyelik bir boşluklu canlı kayıt

Bir nedenden dolayı, kameranın günde 2 saniyelik bir aralığa yönelik geçerli videoyu gönderemediği hakkında daha fazla düşünün. 21 Aralık 2019 ' de kullanılabilir medyayı gösteren bir yanıt aşağıda verilmiştir:

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=full&startTime=2019-12-21T00:00:00Z&endTime=2019-12-22T00:00:00Z
{
   "timeRanges":[
    {
         "start":"2019-12-21T00:00:00Z", 
         "end":"2019-12-21T04:01:08Z"
    },
    {
         "start":"2019-12-21T04:01:10Z", 
         "end":"2019-12-22T00:00:00Z"
    }
   ]
}
```

Yukarıdaki gibi bir kayıtla birlikte, yayılma süresi 24 saatin altında olduğu sürece, her bir startTime/bitişsaati çifti ile HLS ve DASH bildirimleri isteyebilirsiniz. Bu değerler 04:01:08:00:00 UTC 'de boşluğu ayırırdı, döndürülen bildirim, bu protokollerin ilgili özellikleri uyarınca medya zaman çizelgesindeki sürekliliği işaret edecektir.

#### <a name="example-3-live-recording-with-an-8-hour-gap"></a>Örnek 3:8 saatlik bir boşluklu canlı kayıt

Kamera ve/veya şirket içi tesis 'nın gün boyunca 8 saatlik bir süre boyunca güç kaybettiğini ve bir yedekleme güç kaynağı olmadığını varsayalım. Bu, bir gün boyunca tüm kullanılabilir ortamları gösteren bir yanıt aşağıda verilmiştir.

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=full&startTime=2019-12-21T00:00:00Z&endTime=2019-12-22T00:00:00Z
{
   "timeRanges":[
    {
         "start":"2019-12-21T00:00:00Z", 
         "end":"2019-12-21T04:00:00Z"
    },
    {
         "start":"2019-12-21T12:00:00Z", 
         "end":"2019-12-22T00:00:00Z"
    }
   ]
}
```

Böyle bir kayıt ile:

* Her iki startTime/bitişsaati Aralık filtresinin, zaman çizelgesinin ' kullanılabilir ' bölümlerinin içinde olduğu, yani gece yarısı veya öğleden sonra gece yarısına kadar olan bir bildirim isteğinde bulunursa, hizmet, şunun gibi startTime ile bitişten saati içeren bir bildirim döndürür:

    `GET https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2019-12-21T14:01:00.000Z,endTime=2019-12-21T03:00:00.000Z).m3u8`
* StartTime ve bitişinin ortadaki "delik" içinde olduğu bir bildirim isteğinde bulunursa ve 00 ile 10:00'DA UTC arasında bir bildirim istemeniz durumunda hizmet, bir varlık filtresinin boş bir sonuçla sonuçlandığı şekilde aynı şekilde davranır.

    [Bu, boş bir yanıt alan bir istedir] `GET https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2019-12-21T08:00:00.000Z,endTime=2019-12-21T10:00:00.000Z).m3u8`
* Başlangıç veya Bitişden yalnızca birinin ' delik ' içinde olduğu bir bildirim isteğinde bulunursa, döndürülen bildirim yalnızca bu TimeSpan 'nin bir bölümünü içerir. StartTime veya bitişsaati değerini en yakın geçerli sınıra yasırdı. Örneğin, 10:00:00-1PM arası 3 saatlik bir akış sorulursa, yanıt 12 öğleden sonra 1PM 'e kadar 1-saatlik medya içermelidir

    `GET https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2019-12-21T10:00:00.000Z,endTime=2019-12-21T13:00:00.000Z).m3u8`
    
    Döndürülen bildirim 2019-12-21T12:00:00.000 Z, istek 10 ' dan başlayıp bu noktada başlayacak. Player eklentisi ile bir video yönetim sistemi oluştururken, bunu görüntüleyiciye işaret etmesi için dikkatli olunmalıdır. Farkında olmayan bir görüntüleyici, her zaman oynatma zaman çizelgesinin öğleden itibaren sabah

## <a name="recording-and-playback-latencies"></a>Kaydetme ve kayıttan yürütme gecikmeleri

Bir varlığa kaydetmek için IoT Edge üzerinde canlı video analizi kullanırken, modüle, buluta kaydedilmeden önce en az bir video (saniye cinsinden) süresini toplamasını söyleyen bir segmentLength özelliği belirtirsiniz. Örneğin, segmentLength 300 olarak ayarlanırsa modül, 1 5 dakika "Öbek" yüklemeden önce 5 dakikalık bir video birikmesini sağlar ve ardından sonraki 5 dakika boyunca birikme moduna geçer ve yeniden karşıya yükler. SegmentLength 'in artırılması, okuma ve yazma sayısı her segmentLength saniyede bir kez daha sık olmadığı için Azure depolama işlem maliyetlerinizi azaltmaya yarar.

Sonuç olarak, Media Services videonun akışı en az bu kadar gecikecek. 

Kayıttan yürütme gecikmesini belirleyen bir başka faktör (bir olay, kameranın önünde, bir kayıttan yürütme cihazında görüntülenebilecek zamana kadar gecikme), resim grubu [GOP](https://en.wikipedia.org/wiki/Group_of_pictures) süresi olur. [3 basit tekniği kullanarak canlı akışların gecikmesini azaltmak](https://medium.com/vrt-digital-studio/reducing-the-delay-of-live-streams-by-using-3-simple-techniques-e8e028b0a641) , GOP süresini daha uzun sürede gecikme süresini azaltır. En çok 30 saniyeden daha uzun bir süre kullanmak üzere yapılandırılmış araştırma ve güvenlik senaryolarında IP kameralarının kullanılması yaygındır. Bu, genel gecikme süresi üzerinde büyük bir etkiye sahiptir.

## <a name="next-steps"></a>Sonraki adımlar

[Sürekli video kaydı öğreticisi](continuous-video-recording-tutorial.md)
