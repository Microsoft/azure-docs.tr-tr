---
title: Video ve ses dosyalarını Azure Media Services ile çözümleme | Microsoft Docs
description: Azure Media Services kullanırken, ses ve video içeriğinizi Audioanalizzerönayar ve Videoanalizzerönayar kullanarak çözümleyebilirsiniz.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 09/21/2019
ms.author: juliako
ms.openlocfilehash: bc4be8eaafe805e5d9a985b005efe80bc4af1d21
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/22/2019
ms.locfileid: "71177986"
---
# <a name="analyzing-video-and-audio-files"></a>Video ve ses dosyalarını çözümleme

Azure Media Services v3, Media Services v3 çözümleyici önayarları aracılığıyla (Bu makalede açıklanmıştır), video ve ses dosyalarından Video Indexer içeren öngörüleri ayıklamanızı sağlar. Daha ayrıntılı içgörüler istiyorsanız doğrudan Video Indexer’ı kullanın. Video Indexer’ı ve Media Services çözümleyicisinin önceden belirlenmiş ayarlarını hangi durumlarda kullanacağınızı anlamak için [karşılaştırma belgesine](../video-indexer/compare-video-indexer-with-media-services-presets.md) bakın.

Media Services v3 ön ayarlarını kullanarak içeriğinizi analiz etmek için bir **dönüşüm** oluşturur ve bu önayarlardan birini kullanan bir **iş** gönderebilirsiniz: [Videoanaliz zerönayar](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#videoanalyzerpreset) veya **audioanaldaha önayarı**. Aşağıdaki makalede, **Videoanaliz Zerönayar**nasıl kullanılır: [Öğreticide Videoları Azure Media Services](analyze-videos-tutorial-with-api.md)analiz edin.

> [!NOTE]
> Video veya Ses Çözümleyicisi önayarlarını kullanırken, hesabınızı 10 S3 Medya Ayrılmış Birimine sahip olacak şekilde ayarlamak için Azure portalı kullanın. Daha fazla bilgi için bkz. [Medya işlemeyi ölçeklendirme](media-reserved-units-cli-how-to.md).

## <a name="built-in-presets"></a>Yerleşik hazır

Media Services Şu anda aşağıdaki yerleşik çözümleyici önayarlarını desteklemektedir:  

|**Önceden tanımlı ayar adı**|**Senaryo**|**Ayrıntılar**|
|---|---|---|
|[Audioanaliz Zerönayar](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#audioanalyzerpreset)|Ses çözümleme|Önayar, konuşma dökümü dahil olmak üzere, önceden tanımlanmış bir AI tabanlı analiz işlemleri kümesi uygular. Şu anda önayar, tek bir dilde konuşmayı içeren tek bir ses izlemeli içeriği işlemeyi destekler. Girişte ses yükünün dilini ' Language Tag-Region ' BCP-47 biçimini kullanarak belirtebilirsiniz. Desteklenen diller Ingilizce (' en-US ' ve ' en-GB '), Ispanyolca (' es-ES ' ve ' es-MX '), Fransızca (' fr-FR '), Italyanca (' It-IT '), Japonca (' ja-JP '), Portekizce (' PT-BR '), Çince (' zh-CN '), Almanca (' de-DE '), Arapça (' ar-EG '), Rusça (' ru-RU '), Hintçe (' HI-IN ') ) ve Korece (' ko-KR ').<br/><br/> Dil belirtilmezse veya null olarak ayarlanırsa, otomatik dil algılama, algılanan ilk dili seçer ve dosya süresince seçili dille işlem görür. Otomatik dil algılama özelliği şu anda Ingilizce, Çince, Fransızca, Almanca, Italyanca, Japonca, Ispanyolca, Rusça ve Portekizce desteklemektedir. Bu, şu anda ilk dil algılandıktan sonra diller arasında dinamik geçiş yapmayı desteklememektedir. Otomatik dil algılama özelliği, açık bir konuşma sayesinde ses kayıtları ile en iyi şekilde işe yarar. Otomatik dil algılama dili bulamazsa, döküm, Ingilizce 'ye geri dönecektir.|
|[Videoanaliz Zerönayar](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#videoanalyzerpreset)|Ses ve video çözümleme|Hem ses hem de videodan Öngörüler (zengin meta veriler) ayıklar ve bir JSON biçim dosyası verir. Video dosyasını işlerken yalnızca ses öngörülerini ayıklamak isteyip istemediğinizi belirtebilirsiniz. Daha fazla bilgi için bkz. [video çözümleme](analyze-videos-tutorial-with-api.md).|
|[Facedetectorönayar](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#facedetectorpreset)|Videoda bulunan tüm yüzler algılanıyor|Bir video analiz edilirken kullanılacak ayarları açıklar. Bu, mevcut tüm yüzeyleri tespit etmek için kullanılır.|

### <a name="audioanalyzerpreset"></a>Audioanaliz Zerönayar

Önceden ayarlanmış, bir ses veya video dosyasından birden çok ses öngörülerini ayıklamanızı sağlar. Çıktı, ses dökümü için bir JSON dosyası (tüm Öngörüler ile) ve VTT dosyasını içerir. Bu önayar, bir [BCP47](https://tools.ietf.org/html/bcp47) dizesi biçimindeki giriş dosyasının dilini belirten bir özelliği kabul eder. Ses öngörüleri şunları içerir:

* Ses dökümü: zaman damgalarına sahip konuşulan sözcüklerin dökümünü alma. Birden çok dil destekleniyor
* Konuşmacı dizin oluşturma – hoparlörleri ve ilgili konuşulan kelimeleri eşleme
* Konuşma yaklaşımı Analizi – ses dökümü üzerinde gerçekleştirilen yaklaşım analizinin çıkışı
* Anahtar sözcükler: ses dökümden ayıklanan anahtar sözcükler.

### <a name="videoanalyzerpreset"></a>Videoanaliz Zerönayar

Önceden ayarlanmış, bir video dosyasından birden çok ses ve video öngörüleri ayıklamanızı sağlar. Çıktı, bir JSON dosyası (tüm içgörüler ile), video dökümü için bir VTT dosyası ve küçük resim koleksiyonu içerir. Bu önayar Ayrıca özellik olarak bir [BCP47](https://tools.ietf.org/html/bcp47) dizesi (videonun dilini temsil eder) kabul eder. Video öngörüleri yukarıda belirtilen tüm ses öngörülerini ve aşağıdaki ek öğeleri içerir:

* Yüz izleme: videoda yüzlerin bulunduğu zaman. Her yüz için bir yüz kimliği ve karşılık gelen küçük resim koleksiyonu bulunur
* Görsel metin: optik karakter tanıma aracılığıyla algılanan metin. Metin zaman damgalı olur ve anahtar sözcükleri ayıklamak için de kullanılır (ses dökümü dosyasına ek olarak)
* Ana kareler: videodan çıkarılan bir ana kare koleksiyonu
* Görsel içerik denetimi – videoların yetişkin veya korklık olarak işaretlenen kısmı
* Ek Açıklama: önceden tanımlanmış bir nesne modeline göre videolara açıklama ekleme sonucu

##  <a name="insightsjson-elements"></a>Insights. JSON öğeleri

Çıktı, video veya ses üzerinde bulunan tüm öngörülere sahip bir JSON dosyası (Insights. JSON) içerir. JSON aşağıdaki öğeleri içerebilir:

### <a name="transcript"></a>döküm

|Name|Açıklama|
|---|---|
|id|Satır KIMLIĞI.|
|text|Dökümü.|
|dil|Döküm dili. Her satırın farklı bir dile sahip olduğu yazılı betiği desteklemeye yöneliktir.|
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

### <a name="ocr"></a>OCR

|Name|Açıklama|
|---|---|
|id|OCR satır KIMLIĞI.|
|text|OCR metni.|
|likli|Tanıma güvenilirliği.|
|dil|OCR dili.|
|örnekler|Bu OCR 'nin göründüğü zaman aralıklarının listesi (aynı OCR birden çok kez görünebilir).|

```json
"ocr": [
    {
      "id": 0,
      "text": "LIVE FROM NEW YORK",
      "confidence": 0.91,
      "language": "en-US",
      "instances": [
        {
          "start": "00:00:26",
          "end": "00:00:52"
        }
      ]
    },
    {
      "id": 1,
      "text": "NOTICIAS EN VIVO",
      "confidence": 0.9,
      "language": "es-ES",
      "instances": [
        {
          "start": "00:00:26",
          "end": "00:00:28"
        },
        {
          "start": "00:00:32",
          "end": "00:00:38"
        }
      ]
    }
  ],
```

### <a name="faces"></a>leri

|Name|Açıklama|
|---|---|
|id|Yüz KIMLIĞI.|
|name|Yüz adı. ' Bilinmeyen #0 ', tanımlı bir ünlütlik veya müşterinin eğitilen bir kişi olabilir.|
|likli|Yüz tanıma kimlik güveni.|
|description|Ünlüğün açıklaması. |
|thumbnailId|Bu yüzün küçük resminin KIMLIĞI.|
|Knownpersonıd|Bilinen bir kişiyse, iç KIMLIĞI.|
|Referenceıd|Bing ünlüğlik, Bing KIMLIĞI.|
|referenceType|Şu anda yalnızca Bing.|
|title|Bu bir ünlüğlik ise, başlığı (örneğin, "Microsoft 'un CEO").|
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

### <a name="shots"></a>görüntüleri

|Name|Açıklama|
|---|---|
|id|Görüntü KIMLIĞI.|
|Gezen|Görüntü içindeki anahtar çerçevelerinin listesi (her birinin bir KIMLIĞI ve bir örnek zaman aralığı listesi vardır). Anahtar çerçeve örneklerinin, ana karenin küçük resim KIMLIĞI olan bir thumbnailId alanı vardır.|
|örnekler|Bu görüntüsündeki zaman aralıklarının bir listesi (görüntüleri yalnızca 1 örneğe sahiptir).|

```json
"Shots": [
    {
      "id": 0,
      "keyFrames": [
        {
          "id": 0,
          "instances": [
            {
                "thumbnailId": "00000000-0000-0000-0000-000000000000",
              "start": "00: 00: 00.1670000",
              "end": "00: 00: 00.2000000"
            }
          ]
        }
      ],
      "instances": [
        {
            "thumbnailId": "00000000-0000-0000-0000-000000000000",  
          "start": "00: 00: 00.2000000",
          "end": "00: 00: 05.0330000"
        }
      ]
    },
    {
      "id": 1,
      "keyFrames": [
        {
          "id": 1,
          "instances": [
            {
                "thumbnailId": "00000000-0000-0000-0000-000000000000",      
              "start": "00: 00: 05.2670000",
              "end": "00: 00: 05.3000000"
            }
          ]
        }
      ],
      "instances": [
        {
      "thumbnailId": "00000000-0000-0000-0000-000000000000",
          "start": "00: 00: 05.2670000",
          "end": "00: 00: 10.3000000"
        }
      ]
    }
  ]
```

### <a name="statistics"></a>girecek

|Name|Açıklama|
|---|---|
|Yazışma sayısı|Videodaki yazışma sayısı.|
|WordCount|Konuşmacı başına sözcük sayısı.|
|Hoparlörkernumberoffragments|Konuşmacının videoda bulunduğu parçaların miktarı.|
|SpeakerLongestMonolog|Hoparlörün en uzun monolog. Konuşmacı, monolog içinde susraysa dahil edilmiştir. Monolog 'in başındaki ve sonundaki sessizlik kaldırılır.| 
|Hoparlörkertalktolistenratio|Hesaplama, konuşmacının monolog harcanan zamanına (arasında sessizlik olmadan), videonun toplam süresine göre bölünür. Saat, üçüncü ondalık noktaya yuvarlanır.|


### <a name="sentiments"></a>yaklaşımlar

Sentiments, sentimentType alanı tarafından toplanır (pozitif/nötr/negatif). Örneğin, 0-0.1, 0,1-0.2.

|Name|Açıklama|
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

### <a name="labels"></a>etiketleri

|Name|Açıklama|
|---|---|
|id|Etiket KIMLIĞI.|
|name|Etiket adı (örneğin, ' bilgisayar ', ' TV ').|
|dil|Etiket adı dili (çevrildiğinde). BCP-47|
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

### <a name="keywords"></a>anahtar sözcükler

|Name|Açıklama|
|---|---|
|id|Anahtar sözcük KIMLIĞI.|
|text|Anahtar sözcük metni.|
|likli|Anahtar sözcüğünün tanıma güvenilirliği.|
|dil|Anahtar sözcük dili (çevrildiğinde).|
|örnekler|Bu anahtar sözcüğünün göründüğü zaman aralıklarının listesi (bir anahtar sözcük birden çok kez görünebilir).|

```json
"keywords": [
{
    "id": 0,
    "text": "office",
    "confidence": 1.6666666666666667,
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:00.5100000",
        "end": "00:00:02.7200000"
    },
    {
        "start": "00:00:03.9600000",
        "end": "00:00:12.2700000"
    }
    ]
},
{
    "id": 1,
    "text": "icons",
    "confidence": 1.4,
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:03.9600000",
        "end": "00:00:12.2700000"
    },
    {
        "start": "00:00:13.9900000",
        "end": "00:00:15.6100000"
    }
    ]
}
] 
```

#### <a name="visualcontentmoderation"></a>Visualcontentdenetlemesi

Visualcontentmoderblock blok, büyük olasılıkla yetişkinlere yönelik içeriğe sahip Video Indexer bulunan zaman aralıklarını içerir. Visualcontentdenetlemesi boşsa, tanımlı yetişkin içerik yoktur.

Yetişkin veya kcy içeriği içeren videolar yalnızca özel görünüm için kullanılabilir olabilir. Kullanıcılar, içeriğin insan incelemesi için bir istek gönderme seçeneğine sahiptir ve bu durumda Isyetişkin özniteliği insan incelemesi sonucunu içerecektir.

|Name|Açıklama|
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
## <a name="next-steps"></a>Sonraki adımlar

[Öğretici: Azure Media Services ile videoları analiz etme](analyze-videos-tutorial-with-api.md)
