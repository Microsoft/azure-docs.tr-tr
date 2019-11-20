---
title: V2 API tarafından üretilen Azure Media Services Video Indexer çıktıyı inceleyin
titleSuffix: Azure Media Services
description: Bu konuda v2 API tarafından üretilen Video Indexer çıktısı incelenir.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 10/11/2019
ms.author: juliako
ms.openlocfilehash: 0a42c2dce3976a1bf83c85644f56f4c1d8abc9c8
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73839546"
---
# <a name="examine-the-video-indexer-output-produced-by-api"></a>API tarafından üretilen Video Indexer çıkışını inceleyin

**Video dizinini al** API 'sini çağırdığınızda ve yanıt durumu tamam ise yanıt içeriği olarak AYRıNTıLı bir JSON çıkışı alırsınız. JSON içeriği, belirtilen video öngörülerinin ayrıntılarını içerir. Öngörüler arasında şunlar yer alır: transcripts, OCRs, yüzler, konular, bloklar, vb. Boyutlar, her boyutun videoda görünmesinin ne zaman gösterileceğini gösteren zaman aralığı örneklerine sahiptir.  

Videonun özetlenen içgörülerini, [video Indexer](https://www.videoindexer.ai/) Web sitesindeki videonun **oynat** düğmesine basarak da görsel olarak inceleyebilirsiniz. Daha fazla bilgi için bkz. [video öngörülerini görüntüleme ve düzenleme](video-indexer-view-edit.md).

![Insights](./media/video-indexer-output-json/video-indexer-summarized-insights.png)

Bu makalede, **video dizini Al** API 'si tarafından döndürülen JSON içeriği incelenir. 

> [!NOTE]
> Video Indexer tüm erişim belirteçlerinin süre sonu bir saattir.


## <a name="root-elements"></a>Kök öğeleri

|Ad|Açıklama|
|---|---|
|Accoun|Çalma listesinin VI hesap KIMLIĞI.|
|id|Çalma listesinin KIMLIĞI.|
|ad|Çalma listesinin adı.|
|açıklama|Şarkı listesinin açıklaması.|
|Nitelen|Çalma listesini oluşturan kullanıcının adı.|
|yaratıl|Çalma listesinin oluşturulma zamanı.|
|privacyMode|Şarkı listesinin gizlilik modu (özel/genel).|
|durum|Playlist (karşıya yüklenen, işlenen, işlenen, başarısız, karantinaya alındı).|
|IKendine ait|Çalma listesinin geçerli kullanıcı tarafından oluşturulup oluşturulmayacağını gösterir.|
|IsEditable|Geçerli kullanıcının çalma listesini düzenleme yetkisine sahip olup olmadığını gösterir.|
|IsBase|Çalma listesinin bir temel çalma listesi (video) veya diğer videolardan (türetilmiş) bir çalma listesi olup olmadığını gösterir.|
|durationInSeconds|Çalma listesinin toplam süresi.|
|summarizedInsights|Bir [summarizedInsights](#summarizedinsights)içerir.
|larınız|Çalma listesini oluştururken [videoların](#videos) listesi.<br/>Diğer videoların zaman aralıklarıyla oluşturulan bu çalma listesi (türetilmiş), bu listedeki videolar yalnızca dahil edilen zaman aralıklarından verileri içerecektir.|

```json
{
  "accountId": "bca61527-1221-bca6-1527-a90000002000",
  "id": "abc3454321",
  "name": "My first video",
  "description": "I am trying VI",
  "userName": "Some name",
  "created": "2018/2/2 18:00:00.000",
  "privacyMode": "Private",
  "state": "Processed",
  "isOwned": true,
  "isEditable": false,
  "isBase": false,
  "durationInSeconds": 120, 
  "summarizedInsights" : { . . . }
  "videos": [{ . . . }]
}
```

## <a name="summarizedinsights"></a>summarizedInsights

Bu bölümde öngörülerin özeti gösterilmektedir.

|Öznitelik | Açıklama|
|---|---|
|ad|Videonun adı. Örneğin, Azure Izleyici.|
|id|Videonun KIMLIĞI. Örneğin, 63c6d532ff.|
|privacyMode|Dökümde şu modlardan biri olabilir: **Private**, **Public**. **Genel** -video, hesabınızdaki herkese ve videoya bağlantısı olan herkese görünür. **Özel** -video, hesabınızdaki herkes tarafından görülebilir.|
|sürenin|Bir öngörüden oluşan zamanı açıklayan bir süre içerir. Süre saniye cinsinden.|
|thumbnailVideoId|Küçük resmin alındığı videonun KIMLIĞI.
|thumbnailId|Videonun küçük resim KIMLIĞI. Gerçek küçük resmi almak için [Get-Thumbnail](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Thumbnail) çağırın ve ThumbnailVideoId ve thumbnailId geçirin.|
|leri|Sıfır veya daha fazla yüz içerebilir. Daha ayrıntılı bilgi için bkz. [yüzler](#faces).|
|lerimi|Sıfır veya daha fazla anahtar sözcük içerebilir. Daha ayrıntılı bilgi için bkz. [anahtar sözcükler](#keywords).|
|yaklaşımları|Sıfır veya daha fazla duygu içerebilir. Daha ayrıntılı bilgi için bkz. [yaklaşımları](#sentiments).|
|audioEffects| Sıfır veya daha fazla Audioefekt içerebilir. Daha ayrıntılı bilgi için bkz. [Audioeffects](#audioEffects).|
|etikete| Sıfır veya daha fazla etiket içerebilir. Daha ayrıntılı bilgi için bkz. [Etiketler](#labels).|
|markaları| Sıfır veya daha fazla markaya sahip olabilir. Daha ayrıntılı bilgi için bkz. [markalar](#brands).|
|girecek | Daha ayrıntılı bilgi için bkz. [İstatistikler](#statistics).|
|duyguları| Sıfır veya daha fazla durum içerebilir. Daha ayrıntılı bilgi için bkz. [duyguları](#emotions).|
|konularıyla|Sıfır veya daha fazla konu içeriyor olabilir. [Konular](#topics) boyutu.|

## <a name="videos"></a>larınız

|Ad|Açıklama|
|---|---|
|Accoun|Videonun VI hesap KIMLIĞI.|
|id|Videonun KIMLIĞI.|
|ad|Videonun adı.
|durum|Videonun durumu (karşıya yüklenen, işlenen, işlenen, başarısız, karantinaya alındı).|
|processingProgress|İşlem sırasında işleme ilerlemesi (örneğin, %20).|
|failureCode|İşlem başarısız olursa hata kodu (örneğin, ' UnsupportedFileType ').|
|failureMessage|İşlem başarısız olursa hata iletisi.|
|externalID|Videonun dış KIMLIĞI (Kullanıcı tarafından belirtilmişse).|
|externalUrl|Videonun dış URL 'si (Kullanıcı tarafından belirtilmişse).|
|meta veriler|Videonun dış meta verileri (Kullanıcı tarafından belirtilmişse).|
|ıyetişkin|Videonun el ile gözden geçirilip geçirilmediğini ve yetişkinlere yönelik bir video olarak tanımlandığını gösterir.|
|eklentisini|Öngörüler nesnesi. Daha fazla bilgi için bkz. [Öngörüler](#insights).|
|thumbnailId|Videonun küçük resim KIMLIĞI. Gerçek [küçük resim çağrısını almak](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Thumbnail) ve video kimliği ve thumbnailId iletmek için.|
|publishedUrl|Videonun akışını sağlamak için bir URL.|
|publishedUrlProxy|Videonun akışını sağlamak için bir URL (Apple cihazları için).|
|viewToken|Videoyu akışa alma için kısa süreli bir görünüm belirteci.|
|sourceLanguage|Videonun kaynak dili.|
|language|Videonun gerçek dili (çeviri).|
|indexingPreset|Videonun dizinini belirlemek için kullanılan önayar.|
|streamingPreset|Videoyu yayımlamak için kullanılan ön ayar.|
|Linguisticmodelıd|Video eklemek için CRSıS modeli kullanılır.|
|girecek | Daha fazla bilgi için bkz. [İstatistikler](#statistics).|

```json
{
    "videos": [{
        "accountId": "2cbbed36-1972-4506-9bc7-55367912df2d",
        "id": "142a356aa6",
        "state": "Processed",
        "privacyMode": "Private",
        "processingProgress": "100%",
        "failureCode": "General",
        "failureMessage": "",
        "externalId": null,
        "externalUrl": null,
        "metadata": null,
        "insights": {. . . },
        "thumbnailId": "89d7192c-1dab-4377-9872-473eac723845",
        "publishedUrl": "https://videvmediaservices.streaming.mediaservices.windows.net:443/d88a652d-334b-4a66-a294-3826402100cd/Xamarine.ism/manifest",
        "publishedProxyUrl": null,
        "viewToken": "Bearer=<token>",
        "sourceLanguage": "En-US",
        "language": "En-US",
        "indexingPreset": "Default",
        "linguisticModelId": "00000000-0000-0000-0000-000000000000"
    }],
}
```
### <a name="insights"></a>eklentisini

Öngörüler, her boyutun benzersiz öğelerin bir listesi olduğu (örneğin, face1, face2, face3) ve her bir öğenin kendi meta verilerini ve örneklerinin bir listesini (ile zaman aralıkları) içeren bir Boyutlar kümesidir (örneğin, döküm çizgileri, yüzler, markalar vb.). ek isteğe bağlı meta veriler).

Bir yüz KIMLIĞI, bir ad, küçük resim, diğer meta veriler ve bunun zamana bağlı örneklerinin bir listesi olabilir (örneğin: 00:00:05 – 00:00:10, 00:01:00-00:02:30 ve 00:41:21 – 00:41:49.) Her bir zamana bağlı örnek ek meta verilere sahip olabilir. Örneğin, yüzün dikdörtgen koordinatları (20230, 60, 60).

|Sürüm|Kod sürümü|
|---|---|
|sourceLanguage|Videonun kaynak dili (bir ana dilin olduğu varsayılarak). [Bcp-47](https://tools.ietf.org/html/bcp47) dizesi biçiminde.|
|language|Öngörüler dili (kaynak dilden çevrilmiş). [Bcp-47](https://tools.ietf.org/html/bcp47) dizesi biçiminde.|
|döküm|Döküm [boyutu.](#transcript)|
|OCR|[OCR](#ocr) boyutu.|
|lerimi|[Anahtar sözcük](#keywords) boyutu.|
|öbekleri|Bir veya daha fazla [blok](#blocks) içerebilir|
|leri|[Yüzler](#faces) boyutu.|
|etikete|[Etiketler](#labels) boyutu.|
|görüntüleri|[Anlık görüntüleri](#shots) boyutu.|
|markaları|[Markalar](#brands) boyutu.|
|audioEffects|[Audioeffects](#audioEffects) boyutu.|
|yaklaşımları|Yaklaşım [boyutu.](#sentiments)|
|Visualcontentdenetlemesi|[Visualcontentdenetlemesi](#visualcontentmoderation) boyutu.|
|Textualcontentdenetlemesi|[Textualcontentdenetlemesi](#textualcontentmoderation) boyutu.|
|duyguları| [Emomlar](#emotions) boyutu.|
|konularıyla|[Konular](#topics) boyutu.|

Örnek:

```json
{
  "version": "0.9.0.0",
  "sourceLanguage": "en-US",
  "language": "es-ES",
  "transcript": ...,
  "ocr": ...,
  "keywords": ...,
  "faces": ...,
  "labels": ...,
  "shots": ...,
  "brands": ...,
  "audioEffects": ...,
  "sentiments": ...,
  "visualContentModeration": ...,
  "textualContentModeration": ...
}
```

#### <a name="blocks"></a>öbekleri

Öznitelik | Açıklama
---|---
id|Bloğun KIMLIĞI.|
örnekler|Bu bloktaki zaman aralıklarının listesi.|

#### <a name="transcript"></a>döküm

|Ad|Açıklama|
|---|---|
|id|Satır KIMLIĞI.|
|metin|Dökümü.|
|language|Döküm dili. Her satırın farklı bir dile sahip olduğu yazılı betiği desteklemeye yöneliktir.|
|örnekler|Bu satırın göründüğü zaman aralıklarının listesi. Örnek TRANSCRIPT ise, yalnızca 1 örneğe sahip olur.|

Örnek:

```json
"transcript": [
{
    "id": 0,
    "text": "Hi I'm Doug from office.",
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:00.5100000",
        "end": "00:00:02.7200000"
    }
    ]
},
{
    "id": 1,
    "text": "I have a guest. It's Michelle.",
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:02.7200000",
        "end": "00:00:03.9600000"
    }
    ]
}
] 
```

#### <a name="ocr"></a>OCR

|Ad|Açıklama|
|---|---|
|id|OCR satır KIMLIĞI.|
|metin|OCR metni.|
|likli|Tanıma güvenilirliği.|
|language|OCR dili.|
|örnekler|Bu OCR 'nin göründüğü zaman aralıklarının listesi (aynı OCR birden çok kez görünebilir).|
|Yükseklik|OCR dikdörtgeninin yüksekliği|
|Sayfanın Üstü|Px en üstteki konum|
|tarafta| Piksel cinsinden sol konum|
|Genişlik|OCR dikdörtgeninin genişliği|

```json
"ocr": [
    {
      "id": 0,
      "text": "LIVE FROM NEW YORK",
      "confidence": 675.971,
      "height": 35,
      "language": "en-US",
      "left": 31,
      "top": 97,
      "width": 400,      
      "instances": [
        {
          "start": "00:00:26",
          "end": "00:00:52"
        }
      ]
    }
  ],
```

#### <a name="keywords"></a>lerimi

|Ad|Açıklama|
|---|---|
|id|Anahtar sözcük KIMLIĞI.|
|metin|Anahtar sözcük metni.|
|likli|Anahtar sözcüğünün tanıma güvenilirliği.|
|language|Anahtar sözcük dili (çevrildiğinde).|
|örnekler|Bu anahtar sözcüğünün göründüğü zaman aralıklarının listesi (bir anahtar sözcük birden çok kez görünebilir).|

```json
{
    id: 0,
    text: "technology",
    confidence: 1,
    language: "en-US",
    instances: [{
            adjustedStart: "0:05:15.782",
            adjustedEnd: "0:05:16.249",
            start: "0:05:15.782",
            end: "0:05:16.249"
    },
    {
            adjustedStart: "0:04:54.761",
            adjustedEnd: "0:04:55.228",
            start: "0:04:54.761",
            end: "0:04:55.228"
    }]
}
```

#### <a name="faces"></a>leri

|Ad|Açıklama|
|---|---|
|id|Yüz KIMLIĞI.|
|ad|Yüzün adı. ' Bilinmeyen #0, tanımlı bir ünlüde veya müşterinin eğitilen kişi olabilir.|
|likli|Yüz tanıma kimlik güveni.|
|açıklama|Ünlüğün açıklaması. |
|thumbnailId|Bu yüzün küçük resminin KIMLIĞI.|
|Knownpersonıd|Bilinen bir kişiyse, iç KIMLIĞI.|
|Referenceıd|Bing ünlüğlik, Bing KIMLIĞI.|
|ReferenceType|Şu anda yalnızca Bing.|
|Başlığın|Bu bir ünlüğlik ise, başlığı (örneğin, "Microsoft 'un CEO").|
|ImageUrl|Bu bir ünlüsün ise, görüntü URL 'si.|
|örnekler|Bunlar, yüzün verilen zaman aralığında göründüğü örneklerdir. Her örneğin bir thumbnailsId de vardır. |

```json
"faces": [{
    "id": 2002,
    "name": "Xam 007",
    "confidence": 0.93844,
    "description": null,
    "thumbnailId": "00000000-aee4-4be2-a4d5-d01817c07955",
    "knownPersonId": "8340004b-5cf5-4611-9cc4-3b13cca10634",
    "referenceId": null,
    "title": null,
    "imageUrl": null,
    "instances": [{
        "thumbnailsIds": ["00000000-9f68-4bb2-ab27-3b4d9f2d998e",
        "cef03f24-b0c7-4145-94d4-a84f81bb588c"],
        "adjustedStart": "00:00:07.2400000",
        "adjustedEnd": "00:00:45.6780000",
        "start": "00:00:07.2400000",
        "end": "00:00:45.6780000"
    },
    {
        "thumbnailsIds": ["00000000-51e5-4260-91a5-890fa05c68b0"],
        "adjustedStart": "00:10:23.9570000",
        "adjustedEnd": "00:10:39.2390000",
        "start": "00:10:23.9570000",
        "end": "00:10:39.2390000"
    }]
}]
```

#### <a name="labels"></a>etikete

|Ad|Açıklama|
|---|---|
|id|Etiket KIMLIĞI.|
|ad|Etiket adı (örneğin, ' bilgisayar ', ' TV ').|
|language|Etiket adı dili (çevrildiğinde). BCP-47|
|örnekler|Bu etiketin göründüğü zaman aralıklarının listesi (bir etiket birden çok kez görünebilir). Her örneğin bir güvenirlik alanı vardır. |


```json
"labels": [
    {
      "id": 0,
      "name": "person",
      "language": "en-US",
      "instances": [
        {
          "confidence": 1.0,
          "start": "00: 00: 00.0000000",
          "end": "00: 00: 25.6000000"
        },
        {
          "confidence": 1.0,
          "start": "00: 01: 33.8670000",
          "end": "00: 01: 39.2000000"
        }
      ]
    },
    {
      "name": "indoor",
      "language": "en-US",
      "id": 1,
      "instances": [
        {
          "confidence": 1.0,
          "start": "00: 00: 06.4000000",
          "end": "00: 00: 07.4670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 09.6000000",
          "end": "00: 00: 10.6670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 11.7330000",
          "end": "00: 00: 20.2670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 21.3330000",
          "end": "00: 00: 25.6000000"
        }
      ]
    }
  ] 
```

#### <a name="scenes"></a>sahne

|Ad|Açıklama|
|---|---|
|id|Sahne KIMLIĞI.|
|örnekler|Bu sahnenin zaman aralıklarının listesi (bir sahnenin yalnızca 1 örneği olabilir).|

```json
"scenes":[  
    {  
      "id":0,
      "instances":[  
          {  
            "start":"0:00:00",
            "end":"0:00:06.34",
            "duration":"0:00:06.34"
          }
      ]
    },
    {  
      "id":1,
      "instances":[  
          {  
            "start":"0:00:06.34",
            "end":"0:00:47.047",
            "duration":"0:00:40.707"
          }
      ]
    },

]
```

#### <a name="shots"></a>görüntüleri

|Ad|Açıklama|
|---|---|
|id|Görüntü KIMLIĞI.|
|Gezen|Görüntüsündeki ana karelerin bir listesi (her birinin bir KIMLIĞI ve bir örnek zaman aralığı listesi vardır). Her ana kare örneğinin, ana karenin küçük resim KIMLIĞINI tutan bir thumbnailId alanı vardır.|
|örnekler|Bu görüntüsündeki zaman aralıklarının listesi (bir görüntü yalnızca 1 örneğe sahip olabilir).|

```json
"shots":[  
    {  
      "id":0,
      "keyFrames":[  
          {  
            "id":0,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:00.209",
                  "end":"0:00:00.251",
                  "duration":"0:00:00.042"
                }
            ]
          },
          {  
            "id":1,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:04.755",
                  "end":"0:00:04.797",
                  "duration":"0:00:00.042"
                }
            ]
          }
      ],
      "instances":[  
          {  
            "start":"0:00:00",
            "end":"0:00:06.34",
            "duration":"0:00:06.34"
          }
      ]
    },

]
```

#### <a name="brands"></a>markaları

Konuşmadan metin dökümü ve/veya video OCR 'de algılanan iş ve ürün marka adları. Bu, markaların veya amblem algılamanın görsel olarak tanınmasını kapsamaz.

|Ad|Açıklama|
|---|---|
|id|Marka KIMLIĞI.|
|ad|Markalar adı.|
|Referenceıd | Vikipedi URL 'sinin son eki. Örneğin, "Target_Corporation" [https://en.wikipedia.org/wiki/Target_Corporation](https://en.wikipedia.org/wiki/Target_Corporation)sonekidir.
|referenceUrl | Varsa, markasının Vikipi URL 'si. Örneğin, [https://en.wikipedia.org/wiki/Target_Corporation](https://en.wikipedia.org/wiki/Target_Corporation).
|açıklama|Markalar açıklaması.|
|etiketler|Bu markala ilişkili önceden tanımlanmış etiketlerin listesi.|
|likli|Video Indexer marka algılayıcısının güvenirlik değeri (0-1).|
|örnekler|Bu marka için zaman aralıklarının listesi. Her örnek bir brandType içerir ve bu marka, bu markın döküm dosyasında mi yoksa OCR 'de mi görünmediğini belirtir.|

```json
"brands": [
{
    "id": 0,
    "name": "MicrosoftExcel",
    "referenceId": "Microsoft_Excel",
    "referenceUrl": "http: //en.wikipedia.org/wiki/Microsoft_Excel",
    "referenceType": "Wiki",
    "description": "Microsoft Excel is a sprea..",
    "tags": [],
    "confidence": 0.975,
    "instances": [
    {
        "brandType": "Transcript",
        "start": "00: 00: 31.3000000",
        "end": "00: 00: 39.0600000"
    }
    ]
},
{
    "id": 1,
    "name": "Microsoft",
    "referenceId": "Microsoft",
    "referenceUrl": "http: //en.wikipedia.org/wiki/Microsoft",
    "description": "Microsoft Corporation is...",
    "tags": [
    "competitors",
    "technology"
    ],
    "confidence": 1.0,
    "instances": [
    {
        "brandType": "Transcript",
        "start": "00: 01: 44",
        "end": "00: 01: 45.3670000"
    },
    {
        "brandType": "Ocr",
        "start": "00: 01: 54",
        "end": "00: 02: 45.3670000"
    }
    ]
}
]
```

#### <a name="statistics"></a>girecek

|Ad|Açıklama|
|---|---|
|Yazışma sayısı|Videodaki yazışma sayısı.|
|Hoparlörlü Kerwordsayısı|Konuşmacı başına sözcük sayısı.|
|Hoparlörkernumberoffragments|Konuşmacının videoda bulunduğu parçaların miktarı.|
|SpeakerLongestMonolog|Hoparlörün en uzun monolog. Konuşmacı, monolog içinde susraysa dahil edilmiştir. Monolog 'in başındaki ve sonundaki sessizlik kaldırılır.| 
|Hoparlörkertalktolistenratio|Hesaplama, konuşmacının monolog harcanan zamanına (arasında sessizlik olmadan), videonun toplam süresine göre bölünür. Saat, üçüncü ondalık noktaya yuvarlanır.|

#### <a name="a-idaudioeffectsaudioeffects"></a><a id="audioEffects"/>audioEffects

|Ad|Açıklama|
|---|---|
|id|Ses efekti KIMLIĞI.|
|type|Ses efekti türü (örneğin, hareketli, konuşma, sessizlik).|
|örnekler|Bu ses efektinin göründüğü zaman aralıklarının bir listesi.|

```json
"audioEffects": [
{
    "id": 0,
    "type": "Clapping",
    "instances": [
    {
        "start": "00:00:00",
        "end": "00:00:03"
    },
    {
        "start": "00:01:13",
        "end": "00:01:21"
    }
    ]
}
]
```

#### <a name="sentiments"></a>yaklaşımları

Sentiments, sentimentType alanı tarafından toplanır (pozitif/nötr/negatif). Örneğin, 0-0.1, 0,1-0.2.

|Ad|Açıklama|
|---|---|
|id|Yaklaşım KIMLIĞI.|
|averageScore |Bu yaklaşım türünün tüm örneklerinin ortalaması-pozitif/nötr/negatif|
|örnekler|Bu yaklaşım görüntülenen zaman aralıklarının bir listesi.|
|sentimentType |Tür ' pozitif ', ' Nötr ' veya ' negative ' olabilir.|

```json
"sentiments": [
{
    "id": 0,
    "averageScore": 0.87,
    "sentimentType": "Positive",
    "instances": [
    {
        "start": "00:00:23",
        "end": "00:00:41"
    }
    ]
}, {
    "id": 1,
    "averageScore": 0.11,
    "sentimentType": "Positive",
    "instances": [
    {
        "start": "00:00:13",
        "end": "00:00:21"
    }
    ]
}
]
```

#### <a name="visualcontentmoderation"></a>Visualcontentdenetlemesi

Visualcontentmoderblock blok, büyük olasılıkla yetişkinlere yönelik içeriğe sahip Video Indexer bulunan zaman aralıklarını içerir. Visualcontentdenetlemesi boşsa, tanımlı yetişkin içerik yoktur.

Yetişkin veya kcy içeriği içeren videolar yalnızca özel görünüm için kullanılabilir olabilir. Kullanıcılar, içeriğin insan incelemesi için bir istek gönderme seçeneğine sahiptir ve bu durumda Isyetişkin özniteliği insan incelemesi sonucunu içerecektir.

|Ad|Açıklama|
|---|---|
|id|Görsel içerik denetleme KIMLIĞI.|
|adultScore|Yetişkin puanı (İçerik Yöneticisi 'nden).|
|Oycyscore|Yağanın puanı (içerik denetleme).|
|örnekler|Bu görsel içerik denetimi 'nin göründüğü zaman aralıklarının listesi.|

```json
"VisualContentModeration": [
{
    "id": 0,
    "adultScore": 0.00069,
    "racyScore": 0.91129,
    "instances": [
    {
        "start": "00:00:25.4840000",
        "end": "00:00:25.5260000"
    }
    ]
},
{
    "id": 1,
    "adultScore": 0.99231,
    "racyScore": 0.99912,
    "instances": [
    {
        "start": "00:00:35.5360000",
        "end": "00:00:35.5780000"
    }
    ]
}
] 
```

#### <a name="textualcontentmoderation"></a>Textualcontentdenetlemesi 

|Ad|Açıklama|
|---|---|
|id|Metinsel içerik denetleme KIMLIĞI.|
|bannedWordsCount |Yasaklanmış sözcüklerin sayısı.|
|bannedWordsRatio |Toplam sözcük sayısından oranı.|

#### <a name="emotions"></a>duyguları

Video Indexer konuşmayı ve ses ipuçlarını temel alarak tanımlar. Tanımlanan duygu: Joy, sadyeti, Anger veya Fear olabilir.

|Ad|Açıklama|
|---|---|
|id|Duygu KIMLIĞI.|
|type|Konuşma ve ses ipuçları temelinde tanımlanan duygu. Duygu: Joy, sadyeti, Anger veya Fear olabilir.|
|örnekler|Bu duygu tanıma 'ın göründüğü zaman aralıklarının listesi.|

```json
"emotions": [{
    "id": 0,
    "type": "Fear",
    "instances": [{
      "adjustedStart": "0:00:39.47",
      "adjustedEnd": "0:00:45.56",
      "start": "0:00:39.47",
      "end": "0:00:45.56"
    },
    {
      "adjustedStart": "0:07:19.57",
      "adjustedEnd": "0:07:23.25",
      "start": "0:07:19.57",
      "end": "0:07:23.25"
    }]
  },
  {
    "id": 1,
    "type": "Anger",
    "instances": [{
      "adjustedStart": "0:03:55.99",
      "adjustedEnd": "0:04:05.06",
      "start": "0:03:55.99",
      "end": "0:04:05.06"
    },
    {
      "adjustedStart": "0:04:56.5",
      "adjustedEnd": "0:05:04.35",
      "start": "0:04:56.5",
      "end": "0:05:04.35"
    }]
  },
  {
    "id": 2,
    "type": "Joy",
    "instances": [{
      "adjustedStart": "0:12:23.68",
      "adjustedEnd": "0:12:34.76",
      "start": "0:12:23.68",
      "end": "0:12:34.76"
    },
    {
      "adjustedStart": "0:12:46.73",
      "adjustedEnd": "0:12:52.8",
      "start": "0:12:46.73",
      "end": "0:12:52.8"
    },
    {
      "adjustedStart": "0:30:11.29",
      "adjustedEnd": "0:30:16.43",
      "start": "0:30:11.29",
      "end": "0:30:16.43"
    },
    {
      "adjustedStart": "0:41:37.23",
      "adjustedEnd": "0:41:39.85",
      "start": "0:41:37.23",
      "end": "0:41:39.85"
    }]
  },
  {
    "id": 3,
    "type": "Sad",
    "instances": [{
      "adjustedStart": "0:13:38.67",
      "adjustedEnd": "0:13:41.3",
      "start": "0:13:38.67",
      "end": "0:13:41.3"
    },
    {
      "adjustedStart": "0:28:08.88",
      "adjustedEnd": "0:28:18.16",
      "start": "0:28:08.88",
      "end": "0:28:18.16"
    }]
  }
],
```

#### <a name="topics"></a>konularıyla

Video Indexer, döküm dosyalarından Ana konuların çıkarımını yapar. Mümkün olduğunda 2. düzey [IPTC](https://iptc.org/standards/media-topics/) taksonomi dahil edilir. 

|Ad|Açıklama|
|---|---|
|id|Konu KIMLIĞI.|
|ad|Konu adı, örneğin: "Ilaç".|
|Referenceıd|İçerik haritaları, konu hiyerarşisini yansıtır. Örneğin: "Sağlık ve welltısalları/Ilaç ve sağlık/Ilaç ve Alticals".|
|likli|[0, 1] aralığındaki Güvenirlik puanı. Daha yüksek olan daha emin.|
|language|Konusunda kullanılan dil.|
|iptcName|Algılanırsa, ıPTC medya kodu adı.|
|örnekler |Şu anda Video Indexer, zaman aralıklarıyla bir konunun dizinini oluşturmadığından, tüm videonun Aralık olarak kullanılması.|

```json
"topics": [{
    "id": 0,
    "name": "INTERNATIONAL RELATIONS",
    "referenceId": "POLITICS AND GOVERNMENT/FOREIGN POLICY/INTERNATIONAL RELATIONS",
    "referenceType": "VideoIndexer",
    "confidence": 1,
    "language": "en-US",
    "instances": [{
        "adjustedStart": "0:00:00",
        "adjustedEnd": "0:03:36.25",
        "start": "0:00:00",
        "end": "0:03:36.25"
    }]
}, {
    "id": 1,
    "name": "Politics and Government",
    "referenceType": "VideoIndexer",
    "iptcName": "Politics",
    "confidence": 0.9041,
    "language": "en-US",
    "instances": [{
        "adjustedStart": "0:00:00",
        "adjustedEnd": "0:03:36.25",
        "start": "0:00:00",
        "end": "0:03:36.25"
    }]
}]
. . .
```

## <a name="next-steps"></a>Sonraki adımlar

[Video Indexer geliştirici portalı](https://api-portal.videoindexer.ai)

Uygulamanıza pencere öğeleri ekleme hakkında daha fazla bilgi için bkz. [video Indexer pencere öğelerini uygulamalarınıza ekleme](video-indexer-embed-widgets.md). 

