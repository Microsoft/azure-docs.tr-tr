---
title: Video ve ses dosyalarını çözümleme
description: Azure Media Services ' de Audioanalizzerönayar ve Videoanalizzerönayar kullanarak ses ve video içeriğini çözümlemeyi öğrenin.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.date: 03/22/2021
ms.author: inhenkel
ms.openlocfilehash: 8a2c65355f4cb423c17ade6b6f821dcb5b5bd23e
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105964369"
---
# <a name="analyze-video-and-audio-files-with-azure-media-services"></a>Video ve ses dosyalarını Azure Media Services Çözümleme

[!INCLUDE [regulation](../video-indexer/includes/regulation.md)]

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services v3, video ve ses dosyalarından Video Indexer ile öngörüleri ayıklamanıza olanak tanır. Bu makalede, bu öngörüleri ayıklamak için kullanılan Media Services v3 Analyzer önayarları açıklanmaktadır. Daha ayrıntılı içgörüler istiyorsanız doğrudan Video Indexer’ı kullanın. Video Indexer ve Media Services çözümleyici önayarlarının ne zaman kullanılacağını anlamak için [karşılaştırma belgesine](../video-indexer/compare-video-indexer-with-media-services-presets.md)göz atın.

Ses Çözümleyicisi önceden ayarlanmış, temel ve standart olmak üzere iki mod vardır. Aşağıdaki tabloda bulunan farkların açıklamasına bakın.

Media Services v3 ön ayarlarını kullanarak içeriğinizi analiz etmek için bir **dönüşüm** oluşturur ve şu önayarlardan birini kullanan bir **Iş** gönderebilirsiniz: [Videoanalizzerönayar](/rest/api/media/transforms/createorupdate#videoanalyzerpreset) veya **audioanalizzerönayar**. **Videoanalizzerönayar**'in nasıl kullanılacağını gösteren bir öğretici için bkz. [Azure Media Services videoları analiz etme](analyze-videos-tutorial.md).

## <a name="compliance-privacy-and-security"></a>Uyumluluk, Gizlilik ve Güvenlik

Önemli bir anımsatıcı olarak, Video Indexer ortamınızda geçerli olan tüm yasalara uymak zorundasınız ve Video Indexer ya da başka bir Azure hizmetini diğerlerinin haklarını ihlal eden veya başkalarına zararlı olabilecek bir biçimde kullanamazsınız. Biyometrik veriler de dahil olmak üzere herhangi bir videoyu, işleme ve depolama için Video Indexer hizmetine yüklemeden önce, videodaki her türden uygun tüm haklara sahip olmanız gerekir. Video Indexer Azure bilişsel [Hizmetler koşulları](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy/)'nda uyumluluk, gizlilik ve güvenlik hakkında bilgi edinmek için. Microsoft 'un gizlilik yükümlülüklerini ve verilerinizi işleme için lütfen Microsoft 'un [Gizlilik bildirimini](https://privacy.microsoft.com/PrivacyStatement), [çevrimiçi hizmetler koşullarını](https://www.microsoft.com/licensing/product-licensing/products) ("OST") ve [veri Işleme eki](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) 'ni ("DPA") gözden geçirin. Veri saklama, silme/yok etme dahil olmak üzere ek gizlilik bilgileri, OST ve [burada](../video-indexer/faq.md)bulunabilir. Video Indexer kullanarak bilişsel hizmetler koşulları, OST, DPA ve gizlilik bildirimiyle bağlanmayı kabul etmiş olursunuz.

## <a name="built-in-presets"></a>Yerleşik ön ayarlar

Media Services Şu anda aşağıdaki yerleşik çözümleyici önayarlarını desteklemektedir:  

|**Önceden ayarlanmış ad**|**Senaryo/mod**|**Ayrıntılar**|
|---|---|---|
|[Audioanaliz Zerönayar](/rest/api/media/transforms/createorupdate#audioanalyzerpreset)|Ses standart modunu çözümleme|Önayar, konuşma dökümü dahil olmak üzere, önceden tanımlanmış bir AI tabanlı analiz işlemleri kümesi uygular. Şu anda önayar, tek bir dilde konuşmayı içeren tek bir ses izlemeli içeriği işlemeyi destekler. Girişte ses yükünün dilini ' Language Tag-Region ' BCP-47 biçimini kullanarak belirtebilirsiniz. Desteklenen diller Ingilizce (' en-US ', ' en-GB ' ve ' en-AU '), Ispanyolca (' es-ES ' ve ' es-MX '), Fransızca (' fr-FR ' ve ' fr-CA '), Italyanca (' It-IT '), Japonca (' ja-JP '), Portekizce (' PT-BR '), Çince (' zh-CN '), Almanca (' de-DE '), Arapça ("ar-BH", ' ar-EG ', ' ar-IQ ', ' ar-JO ', ' ar-KW ', ' ar-LB ', ' ar-OM ', ' ar-QA ', ' ar-SA ' ve ' ar-SY '), Rusça (' ru-RU '), Hintçe (' Hi-ın '), Korece (' ko-KR '), Danca (' da-DK '), Norveççe (' NB-NO '), Isveççe (' ZF-ı '), Fince (' San-FI '), Tay dili (' th-TH ') ve Türkçe (' tr-TR ').<br/><br/> Dil belirtilmezse veya null olarak ayarlanırsa, otomatik dil algılama algılanan ilk dili seçer ve dosya süresince seçili dile devam eder. Otomatik dil algılama özelliği şu anda Ingilizce, Çince, Fransızca, Almanca, Italyanca, Japonca, Ispanyolca, Rusça ve Portekizce desteklemektedir. İlk dil algılandıktan sonra diller arasında dinamik geçiş yapmayı desteklemez. Otomatik dil algılama özelliği, açık bir konuşma sayesinde ses kayıtları ile en iyi şekilde işe yarar. Otomatik dil algılama dili bulamazsa, geri bulma işlemi Ingilizce 'ye döner.|
|[Audioanaliz Zerönayar](/rest/api/media/transforms/createorupdate#audioanalyzerpreset)|Ses temel modunu çözümleme|Bu önceden ayarlama modu, bir VTT alt başlık/başlık dosyasının konuşma ve oluşturma işlemlerini metne dönüştürme ve oluşturma işlemi gerçekleştirir. Bu modun çıktısı yalnızca anahtar sözcükler, döküm ve zamanlama bilgileri dahil olmak üzere bir Öngörüler JSON dosyası içerir. Bu modda otomatik dil algılama ve konuşmacı seçme dahil değildir. Desteklenen dillerin listesi yukarıdaki standart modla aynıdır.|
|[Videoanaliz Zerönayar](/rest/api/media/transforms/createorupdate#videoanalyzerpreset)|Ses ve video çözümleme|Hem ses hem de videodan Öngörüler (zengin meta veriler) ayıklar ve bir JSON biçim dosyası verir. Video dosyasını işlerken yalnızca ses öngörülerini ayıklamak isteyip istemediğinizi belirtebilirsiniz. Daha fazla bilgi için bkz. [video çözümleme](analyze-videos-tutorial.md).|
|[Facedetectorönayar](/rest/api/media/transforms/createorupdate#facedetectorpreset)|Videoda bulunan yüzler algılanıyor|Bir video çözümlenirken, mevcut tüm yüzleri tespit etmek için kullanılacak ayarları açıklar.|

### <a name="audioanalyzerpreset-standard-mode"></a>Audioanalöneri önceden ayarlanmış standart modu

Önceden ayarlanmış, bir ses veya video dosyasından birden çok ses öngörülerini ayıklamanızı sağlar.

Çıktı, ses dökümü için bir JSON dosyası (tüm Öngörüler ile) ve VTT dosyasını içerir. Bu önayar, bir [BCP47](https://tools.ietf.org/html/bcp47) dizesi biçimindeki giriş dosyasının dilini belirten bir özelliği kabul eder. Ses öngörüleri şunları içerir:

* **Ses dökümü**: zaman damgalarına sahip konuşulan sözcüklerin dökümünü alma. Birden çok dil desteklenir.
* **Konuşmacı dizin oluşturma**: konuşmacıları ve ilgili konuşulan kelimeleri eşleme.
* **Konuşma yaklaşımı Analizi**: ses dökümü üzerinde gerçekleştirilen yaklaşım analizinin çıktısı.
* **Anahtar sözcükler**: ses dökümden ayıklanan anahtar sözcükler.

### <a name="audioanalyzerpreset-basic-mode"></a>Audioanaliz Zerönayar temel modu

Önceden ayarlanmış, bir ses veya video dosyasından birden çok ses öngörülerini ayıklamanızı sağlar.

Çıktı, ses dökümü için bir JSON dosyası ve VTT dosyası içerir. Bu önayar, bir [BCP47](https://tools.ietf.org/html/bcp47) dizesi biçimindeki giriş dosyasının dilini belirten bir özelliği kabul eder. Çıktı şunları içerir:

* **Ses dökümü**: zaman damgalarına sahip konuşulan sözcüklerin dökümünü alma. Birden çok dil desteklenir, ancak otomatik dil algılama ve konuşmacı koruması dahil edilmez.
* **Anahtar sözcükler**: ses dökümden ayıklanan anahtar sözcükler.

### <a name="videoanalyzerpreset"></a>Videoanaliz Zerönayar

Önceden ayarlanmış, bir video dosyasından birden çok ses ve video öngörüleri ayıklamanızı sağlar. Çıktı, bir JSON dosyası (tüm içgörüler ile), video dökümü için bir VTT dosyası ve küçük resim koleksiyonu içerir. Bu önayar Ayrıca özellik olarak bir [BCP47](https://tools.ietf.org/html/bcp47) dizesi (videonun dilini temsil eder) kabul eder. Video öngörüleri yukarıda belirtilen tüm ses öngörülerini ve aşağıdaki ek öğeleri içerir:

* **Yüz izleme**: videoda yüzlerin bulunduğu zaman. Her yüz bir yüz KIMLIĞI ve karşılık gelen bir küçük resim koleksiyonuna sahiptir.
* **Görsel metin**: optik karakter tanıma aracılığıyla algılanan metin. Metin zaman damgalı olur ve anahtar sözcükleri ayıklamak için de kullanılır (ses dökümü dosyasına ek olarak).
* **Ana kareler**: videodan çıkarılan bir ana kare koleksiyonu.
* **Görsel içerik** denetimi: videoların, yetişkin veya Dicy olarak işaretlenen kısmı.
* **Ek açıklama**: önceden tanımlanmış bir nesne modeline göre videolara açıklama ekleme sonucu

## <a name="insightsjson-elements"></a>Öğeler üzerinde insights.js

Çıktı, videoda veya seste bulunan tüm öngörülerle bir JSON dosyası (insights.json) içerir. JSON aşağıdaki öğeleri içerebilir:

### <a name="transcript"></a>döküm

|Ad|Açıklama|
|---|---|
|kimlik|Satır KIMLIĞI.|
|metin|Dökümü.|
|language|Döküm dili. Her satırın farklı bir dile sahip olduğu yazılı betiği desteklemeye yöneliktir.|
|larında|Bu satırın göründüğü zaman aralıklarının listesi. Örnek TRANSCRIPT ise, yalnızca 1 örneğe sahip olur.|

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

|Ad|Açıklama|
|---|---|
|kimlik|OCR satır KIMLIĞI.|
|metin|OCR metni.|
|güvenilirlik|Tanıma güvenilirliği.|
|language|OCR dili.|
|larında|Bu OCR 'nin göründüğü zaman aralıklarının listesi (aynı OCR birden çok kez görünebilir).|

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

|Ad|Açıklama|
|---|---|
|kimlik|Yüz KIMLIĞI.|
|name|Yüz adı. ' Bilinmeyen #0 ', tanımlanan bir ünlüde veya müşterinin eğitilen bir kişi olabilir.|
|güvenilirlik|Yüz tanıma kimlik güveni.|
|açıklama|Ünlüğün açıklaması. |
|thumbnailId|Bu yüzün küçük resminin KIMLIĞI.|
|Knownpersonıd|İç KIMLIK (bilinen bir kişiyse).|
|Referenceıd|Bing KIMLIĞI (bir Bing ünlüğise).|
|referenceType|Şu anda yalnızca Bing.|
|başlık|Başlık (örneğin, "Microsoft 'un CEO 'SU").|
|ImageUrl|Bir ünlüğlık ise resim URL 'SI.|
|larında|Yüzün verilen zaman aralığında göründüğü örnekler. Her örneğin bir thumbnailsId de vardır. |

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

|Ad|Açıklama|
|---|---|
|kimlik|Görüntü KIMLIĞI.|
|Gezen|Görüntü içindeki anahtar çerçevelerinin listesi (her birinin bir KIMLIĞI ve bir örnek zaman aralığı listesi vardır). Anahtar çerçeve örneklerinin, ana karenin küçük resim KIMLIĞI olan bir thumbnailId alanı vardır.|
|larında|Bu görüntüsündeki zaman aralıklarının bir listesi (görüntüleri yalnızca 1 örneğe sahiptir).|

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

|Ad|Açıklama|
|---|---|
|Yazışma sayısı|Videodaki yazışma sayısı.|
|WordCount|Konuşmacı başına sözcük sayısı.|
|Hoparlörkernumberoffragments|Konuşmacının videoda bulunduğu parçaların miktarı.|
|SpeakerLongestMonolog|Hoparlörün en uzun monolog. Konuşmacı, monolog içinde susraysa dahil edilmiştir. Monolog 'in başındaki ve sonundaki sessizlik kaldırılır.|
|Hoparlörkertalktolistenratio|Hesaplama, konuşmacının monolog harcanan zamanına (arasında sessizlik olmadan), videonun toplam süresine göre bölünür. Saat, üçüncü ondalık noktaya yuvarlanır.|


### <a name="sentiments"></a>yaklaşımları

Sentiments, sentimentType alanı tarafından toplanır (pozitif/nötr/negatif). Örneğin, 0-0.1, 0,1-0.2.

|Ad|Açıklama|
|---|---|
|kimlik|Yaklaşım KIMLIĞI.|
|averageScore |Bu yaklaşım türünün tüm örneklerinin ortalaması-pozitif/nötr/negatif|
|larında|Bu yaklaşım görüntülenen zaman aralıklarının bir listesi.|
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

### <a name="labels"></a>etikete

|Ad|Açıklama|
|---|---|
|kimlik|Etiket KIMLIĞI.|
|name|Etiket adı (örneğin, ' bilgisayar ', ' TV ').|
|language|Etiket adı dili (çevrildiğinde). BCP-47|
|larında|Bu etiketin göründüğü zaman aralıklarının listesi (bir etiket birden çok kez görünebilir). Her örneğin bir güvenirlik alanı vardır. |

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

|Ad|Açıklama|
|---|---|
|kimlik|Anahtar sözcük KIMLIĞI.|
|metin|Anahtar sözcük metni.|
|güvenilirlik|Anahtar sözcüğünün tanıma güvenilirliği.|
|language|Anahtar sözcük dili (çevrildiğinde).|
|larında|Bu anahtar sözcüğünün göründüğü zaman aralıklarının listesi (bir anahtar sözcük birden çok kez görünebilir).|

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

Yetişkin veya kcy içeriği içeren videolar yalnızca özel görünüm için kullanılabilir olabilir. Kullanıcılar içeriğin insan incelemesi için bir istek gönderebilir ve bu durumda `IsAdult` öznitelik insan incelemesi sonucunu içerecektir.

|Ad|Açıklama|
|---|---|
|kimlik|Görsel içerik denetleme KIMLIĞI.|
|adultScore|Yetişkin puanı (İçerik Yöneticisi 'nden).|
|Oycyscore|Yağanın puanı (içerik denetleme).|
|larında|Bu görsel içerik denetimi 'nin göründüğü zaman aralıklarının listesi.|

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

[Öğretici: Azure Media Services ile videoları analiz etme](analyze-videos-tutorial.md)
