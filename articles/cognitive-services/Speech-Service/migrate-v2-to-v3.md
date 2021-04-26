---
title: V2 'den v3 REST API-konuşma hizmetine geçiş
titleSuffix: Azure Cognitive Services
description: Bu belge, geliştiricilerin, konuşma Hizmetleri konuşmadan metne REST API, kod v2 'den v3 'e geçiş yapılmasına yardımcı olur.
services: cognitive-services
author: bexxx
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: rbeckers
ms.custom: devx-track-csharp
ms.openlocfilehash: 9c8016b566db8be1b7f5c5ddb8d92123d6673db5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "98569853"
---
# <a name="migrate-code-from-v20-to-v30-of-the-rest-api"></a>Kodu v 2.0 'dan v 3.0 'a geçirin REST API

V2 ile karşılaştırıldığında, konuşma hizmetlerinin, konuşmadan metne REST API v3 sürümü daha güvenilirdir, kullanımı daha kolaydır ve benzer hizmetler için API 'lerle daha tutarlıdır. Çoğu takım, günde bir veya iki adımda v2 'den v3 'e geçiş yapabilir.

## <a name="forward-compatibility"></a>İleriye dönük uyumluluk

V2 'nin tüm varlıkları aynı kimliğin altındaki v3 API 'sinde de bulunabilir. Bir sonucun şemasının değiştiği (örneğin, döküm), API 'nin v3 sürümündeki bir GET 'in sonucu v3 şemasını kullanır. API 'nin v2 sürümündeki bir GET 'in sonucu aynı v2 şemasını kullanır. V3 üzerinde yeni oluşturulan varlıklar ****   v2 API 'lerinden gelen yanıtlardan kullanılamaz. 

## <a name="migration-steps"></a>Geçiş adımları

Bu, geçiş için hazırlanırken bilmeniz gereken öğelerin Özet listesidir. Ayrıntılar tek tek bağlantılarda bulunur. API 'nin geçerli kullanımına bağlı olarak burada listelenen adımların hepsi geçerli olabilir. Yalnızca birkaç değişiklik, çağıran kodda önemsiz olmayan değişiklikler gerektirir. Çoğu değişiklik yalnızca öğe adlarında değişiklik yapılmasını gerektirir. 

Genel değişiklikler: 

1. [Ana bilgisayar adını değiştirme](#host-name-changes)

1. [Özellik kimliğini istemci kodunuzda kendi kendine yeniden adlandırın](#identity-of-an-entity) 

1. [Varlıkların koleksiyonları üzerinde yinelemek için kodu değiştirin](#working-with-collections-of-entities)

1. [Özellik adını istemci kodunuzda displayName olarak yeniden adlandırın](#name-of-an-entity)

1. [Başvurulan varlıkların meta verilerinin alınmasını ayarla](#accessing-referenced-entities)

1. Batch dökümünü kullanıyorsanız: 

    * [Toplu iş dökümlerini oluşturmak için kodu ayarla](#creating-transcriptions) 

    * [Kodu yeni döküm sonuçları şemasına uyarlayın](#format-of-v3-transcription-results)

    * [Sonuçların alınma biçimi için kodu ayarla](#getting-the-content-of-entities-and-the-results)

1. Özel model eğitimi ve test API 'Leri kullanıyorsanız: 

    * [Değişiklikleri özel model eğitimlerine uygulayın](#customizing-models)

    * [Taban ve özel modellerin nasıl alındığını değiştirme](#retrieving-base-and-custom-models)

    * [Accuracytests yol segmentini istemci kodunuzda değerlendirmeler olarak yeniden adlandırın](#accuracy-tests)

1. Uç nokta API 'Leri kullanıyorsanız:

    * [Uç nokta günlüklerinin alınma şeklini değiştirme](#retrieving-endpoint-logs)

1. Diğer küçük değişiklikler: 

    * [Tüm özel özellikleri POST isteklerinizin özellikleri yerine customProperties olarak geçirin](#using-custom-properties)

    * [Işlem konumu yerine yanıt üst bilgisi konumundan konumu okuyun](#response-headers)

## <a name="breaking-changes"></a>Yeni değişiklikler

### <a name="host-name-changes"></a>Ana bilgisayar adı değişiklikleri

Uç nokta ana bilgisayar adları iken `{region}.cris.ai` olarak değiştirildi `{region}.api.cognitive.microsoft.com` . Yeni uç noktalara yönelik yollar artık `api/` ana bilgisayar adının bir parçası olduğundan içermez. [Swagger belgesi](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0) geçerli bölgeleri ve yolları listeler.
>[!IMPORTANT]
>Ana bilgisayar adını, `{region}.cris.ai` konuşma aboneliğinizin bölgesi olan bölge adıyla değiştirin `{region}.api.cognitive.microsoft.com` . Ayrıca `api/` , istemci kodunuzda herhangi bir yoldan kaldırın.

### <a name="identity-of-an-entity"></a>Bir varlığın kimliği

Özelliği `id` artık `self` . V2 sürümünde bir API kullanıcısının, API 'deki yollarımızı nasıl oluşturduğumuzu öğrenmiş olması gerekiyordu. Bu, Genişletilebilir olmayan ve Kullanıcı tarafından gerekli olan gereksiz çalışmamıştı. Özelliği ( `id` UUID), `self` VARLıĞıN (URL) konumu olan (dize) ile değiştirilmiştir. Değer, tüm varlıklarınız arasında hala benzersizdir. `id`Kodunuzda bir dize olarak depolanıyorsa, yeni şemayı desteklemek için yeniden adlandırma yeterlidir. Artık `self` içeriği `GET` , `PATCH` varlığınızın,, ve REST çağrılarının URL 'si olarak kullanabilirsiniz `DELETE` .

Varlığın diğer yollarla kullanılabilir ek işlevleri varsa, bunlar altında listelenir `links` . Aşağıdaki örnek, döküm içeriğine ayrı bir yöntemi gösterir `GET` :
>[!IMPORTANT]
>Özelliği `id` istemci kodunuzda olarak yeniden adlandırın `self` . Türünü, `uuid` gerekirse olarak değiştirin `string` . 

**v2 dökümü:**

```json
{
    "id": "9891c965-bb32-4880-b14b-6d44efb158f3",
    "createdDateTime": "2019-01-07T11:34:12Z",
    "lastActionDateTime": "2019-01-07T11:36:07Z",
    "status": "Succeeded",
    "locale": "en-US",
    "name": "Transcription using locale en-US"
}
```

**v3 dökümü:**

```json
{
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3",
    "createdDateTime": "2019-01-07T11:34:12Z",
    "lastActionDateTime": "2019-01-07T11:36:07Z",
    "status": "Succeeded",
    "locale": "en-US", 
    "displayName": "Transcription using locale en-US",
    "links": {
      "files": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files"
    }
}
```

Kodunuzun uygulamasına bağlı olarak, özelliğin yeniden adlandırılması yeterli olmayabilir. Döndürülen `self` ve `links` değerlerin, istemcinizdeki yolları oluşturmak yerine Rest aramalarınızın hedef URL 'leri olarak kullanılması önerilir. Döndürülen URL 'Leri kullanarak, yollardaki gelecekteki değişikliklerin istemci kodunuzu bozmadığından emin olabilirsiniz.

### <a name="working-with-collections-of-entities"></a>Varlık koleksiyonlarıyla çalışma

Daha önce v2 API 'SI, bir sonuç içinde tüm kullanılabilir varlıkları döndürdü. V3 'de beklenen yanıt boyutu üzerinde daha ayrıntılı bir denetime izin vermek için tüm koleksiyon sonuçlarının sayfalandırılmış olması gerekir. Döndürülen varlıkların sayısı ve sayfanın başlangıç kayması üzerinde denetiminiz vardır. Bu davranış, yanıt işlemcisinin çalışma zamanının önlanmasını kolaylaştırır.

Yanıtın temel şekli tüm koleksiyonlar için aynıdır:

```json
{
    "values": [
        {     
        }
    ],
    "@nextLink": "https://{region}.api.cognitive.microsoft.com/speechtotext/v3.0/{collection}?skip=100&top=100"
}
```

`values`Özelliği, kullanılabilir koleksiyon varlıklarının bir alt kümesini içerir. Sayı ve konum, `skip` ve `top` sorgu parametreleri kullanılarak denetlenebilir. Ne zaman yoksa `@nextLink` `null` , daha fazla veri mevcuttur ve bir sonraki veri grubu bir get işlemi yaparak alınabilir `$.@nextLink` .

Bu değişiklik, `GET` tüm öğeler döndürülünceye kadar bir döngüde koleksiyon için çağrılmasını gerektirir.

>[!IMPORTANT]
>Bir alma işleminin yanıtı `speechtotext/v3.0/{collection}` ' de bir değeri içerdiğinde `$.@nextLink` , `GETs` `$.@nextLink` `$.@nextLink` Bu koleksiyonun tüm öğelerini almak için ayarlanana kadar üzerinde verme işlemine devam edin.

### <a name="creating-transcriptions"></a>Döküm oluşturma

[Toplu iş dökümlerinin nasıl yapılacağı](./batch-transcription.md)hakkında ayrıntılı bir açıklama, toplu olarak nasıl yapılır? bölümünde bulunabilir.

V3 dökümü API 'SI, belirli döküm seçeneklerini açıkça ayarlamanıza olanak sağlar. Tüm (isteğe bağlı) yapılandırma özellikleri artık `properties` özellikte ayarlanabilir.
V3 sürümü birden çok giriş dosyasını da destekler, bu nedenle v2 'nin bulunduğu tek URL yerine URL 'lerin bir listesini gerektirir. V2 Özellik adı `recordingsUrl` artık v3 'dir `contentUrls` . Analbetiklerde yaklaşım çözümleme işlevselliği v3 'de kaldırılmıştır. Yaklaşım Analizi seçenekleri için bkz. Microsoft bilişsel hizmet [metin analizi](https://azure.microsoft.com/en-us/services/cognitive-services/text-analytics/) .

Altındaki yeni özellik `timeToLive` , `properties` var olan tamamlanmış varlıkların ayıklayıp sağlanmasına yardımcı olabilir. , `timeToLive` Tamamlanan bir varlığın otomatik olarak silineceği süreyi belirtir. `PT12H`Varlıkların sürekli olarak izlendiği, tüketildiği ve silindiği ve bu nedenle genellikle 12 saat geçtikten sonra uzun süre işlendiği zaman, yüksek bir değere ayarlayın (örneğin).

**v2 istek SONRASı isteği gövdesi:**

```json
{
  "locale": "en-US",
  "name": "Transcription using locale en-US",
  "recordingsUrl": "https://contoso.com/mystoragelocation",
  "properties": {
    "AddDiarization": "False",
    "AddWordLevelTimestamps": "False",
    "PunctuationMode": "DictatedAndAutomatic",
    "ProfanityFilterMode": "Masked"
  }
}
```

**v3 döküm istek gövdesi:**

```json
{
  "locale": "en-US",
  "displayName": "Transcription using locale en-US",
  "contentUrls": [
    "https://contoso.com/mystoragelocation",
    "https://contoso.com/myotherstoragelocation"
  ],
  "properties": {
    "diarizationEnabled": false,
    "wordLevelTimestampsEnabled": false,
    "punctuationMode": "DictatedAndAutomatic",
    "profanityFilterMode": "Masked"
  }
}
```
>[!IMPORTANT]
>Özelliğini, `recordingsUrl` `contentUrls` tek bir URL yerine bir URL dizisi olarak yeniden adlandırın ve geçirin. `diarizationEnabled`Ya da yerine için ayarları geçirin `wordLevelTimestampsEnabled` `bool` `string` .

### <a name="format-of-v3-transcription-results"></a>V3 dökümü sonuçlarının biçimi

Döküm sonuçlarının şeması, gerçek zamanlı uç noktalar tarafından oluşturulan dökümlerle hizalı olarak değiştirilmiştir. [Toplu iş dökümünü nasıl yapılır](./batch-transcription.md)bölümünde yeni biçimin derinlemesine bir açıklamasını bulun. Sonucun şeması, altında [GitHub örnek depomızda](https://aka.ms/csspeech/samples) yayımlanır `samples/batch/transcriptionresult_v3.schema.json` .

Özellik adları artık Camel-cased ve için değerleri ve `channel` `speaker` artık tamsayı türlerini kullanır. Süreler için biçimlendirme artık ISO 8601 ' de açıklanan yapıyı kullanarak, diğer Azure API 'Lerinde kullanılan süre biçimlendirmesiyle eşleşir.

Bir v3 dökümü sonucunun örneği. Farklar açıklamalarda açıklanmıştır.

```json
{
  "source": "...",                      // (new in v3) was AudioFileName / AudioFileUrl
  "timestamp": "2020-06-16T09:30:21Z",  // (new in v3)
  "durationInTicks": 41200000,          // (new in v3) was AudioLengthInSeconds
  "duration": "PT4.12S",                // (new in v3)
  "combinedRecognizedPhrases": [        // (new in v3) was CombinedResults
    {
      "channel": 0,                     // (new in v3) was ChannelNumber
      "lexical": "hello world",
      "itn": "hello world",
      "maskedITN": "hello world",
      "display": "Hello world."
    }
  ],
  "recognizedPhrases": [                // (new in v3) was SegmentResults
    {
      "recognitionStatus": "Success",   // 
      "channel": 0,                     // (new in v3) was ChannelNumber
      "offset": "PT0.07S",              // (new in v3) new format, was OffsetInSeconds
      "duration": "PT1.59S",            // (new in v3) new format, was DurationInSeconds
      "offsetInTicks": 700000.0,        // (new in v3) was Offset
      "durationInTicks": 15900000.0,    // (new in v3) was Duration

      // possible transcriptions of the current phrase with confidences
      "nBest": [
        {
          "confidence": 0.898652852,phrase
          "speaker": 1,
          "lexical": "hello world",
          "itn": "hello world",
          "maskedITN": "hello world",
          "display": "Hello world.",

          "words": [
            {
              "word": "hello",
              "offset": "PT0.09S",
              "duration": "PT0.48S",
              "offsetInTicks": 900000.0,
              "durationInTicks": 4800000.0,
              "confidence": 0.987572
            },
            {
              "word": "world",
              "offset": "PT0.59S",
              "duration": "PT0.16S",
              "offsetInTicks": 5900000.0,
              "durationInTicks": 1600000.0,
              "confidence": 0.906032
            }
          ]
        }
      ]
    }
  ]
}
```
>[!IMPORTANT]
>Döküm sonucunun, yukarıda gösterildiği gibi yeni türe seri durumdan çıkarılması. Her ses kanalı başına tek bir dosya yerine, `channel` içindeki her öğe için özellik değerini denetleyerek kanalları ayırt edin `recognizedPhrases` . Her giriş dosyası için artık tek bir sonuç dosyası vardır.


### <a name="getting-the-content-of-entities-and-the-results"></a>Varlıkların içeriğini ve sonuçlarını alma

V2 'de, giriş veya sonuç dosyalarının bağlantıları, varlık meta verileri geri kalanı ile birlikte satır içine alındı. V3 'de bir geliştirme olarak, varlık meta verileri (bir GET ile döndürülen `$.self` ) ve sonuç dosyalarına erişmek için Ayrıntılar ve kimlik bilgileri arasında net bir ayrım vardır. Bu ayrım, müşteri verilerinin korunmasına yardımcı olur ve kimlik bilgilerinin geçerlilik süresi boyunca ince denetim sağlar.

V3 'de, `links` `files` varlığın verileri (veri kümeleri, döküm, uç noktaları, uç noktalar veya değerlendirmeler) açığa çıkardığı durumlarda adlı bir alt özellik ekleyin. Bir GET on, `$.links.files` her dosyanın içeriğine erişmek için bir dosya listesi ve SAS URL 'si döndürür. SAS URL 'Lerinin geçerlilik süresini denetlemek için sorgu parametresi, `sasValidityInSeconds` yaşam süresini belirtmek için kullanılabilir.

**v2 dökümü:**

```json
{
  "id": "9891c965-bb32-4880-b14b-6d44efb158f3",
  "status": "Succeeded",
  "reportFileUrl": "https://contoso.com/report.txt?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=6c044930-3926-4be4-be76-f728327c53b5",
  "resultsUrls": {
    "channel_0": "https://contoso.com/audiofile1.wav?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=6c044930-3926-4be4-be76-f72832e6600c",
    "channel_1": "https://contoso.com/audiofile2.wav?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=3e0163f1-0029-4d4a-988d-3fba7d7c53b5"
  }
}
```

**v3 dökümü:**

```json
{
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3",
    "links": {
      "files": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files"
    } 
}
```

**Bir GET `$.links.files` , ile sonuçlanır:**

```json
{
  "values": [
    {
      "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files/f23e54f5-ed74-4c31-9730-2f1a3ef83ce8",
      "name": "Name",
      "kind": "Transcription",
      "properties": {
        "size": 200
      },
      "createdDateTime": "2020-01-13T08:00:00Z",
      "links": {
        "contentUrl": "https://customspeech-usw.blob.core.windows.net/artifacts/mywavefile1.wav.json?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    },
    {
      "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files/28bc946b-c251-4a86-84f6-ea0f0a2373ef",
      "name": "Name",
      "kind": "TranscriptionReport",
      "properties": {
        "size": 200
      },
      "createdDateTime": "2020-01-13T08:00:00Z",
      "links": {
        "contentUrl": "https://customspeech-usw.blob.core.windows.net/artifacts/report.json?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    }
  ],
  "@nextLink": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files?skip=2&top=2"
}
```

`kind`Özelliği, dosyanın içerik biçimini gösterir. Döküm için, tür dosyaları `TranscriptionReport` işin özetidir ve tür dosyaları `Transcription` işin kendisinin sonucudur.

>[!IMPORTANT]
>İşlemlerin sonuçlarını almak için, bir üzerinde kullanın, veya ' de ' de `GET` `/speechtotext/v3.0/{collection}/{id}/files` yanıtlarında yer almaz `GET` `/speechtotext/v3.0/{collection}/{id}` `/speechtotext/v3.0/{collection}` .

### <a name="customizing-models"></a>Modelleri özelleştirme

V3 'den önce, bir model eğitilirken bir _akustik model_ ve _dil modeli_ arasında bir ayrım vardı. Bu ayrım, uç noktalar veya döküm oluştururken birden çok model belirtme gereksiniminden sonuçlandı. Bu işlemi bir arayan için basitleştirmek amacıyla, farkları kaldırdık ve her şeyi, model eğitimi için kullanılan veri kümelerinin içeriğine bağlı olarak yaptık. Bu değişiklik ile model oluşturma artık karışık veri kümelerini (dil verileri ve akustik veriler) desteklemektedir. Uç noktalar ve döküm artık yalnızca bir model gerektirir.

Bu değişiklik ile, işlem içindeki bir için ihtiyacı `kind` `POST` kaldırılmıştır ve `datasets[]` dizi artık aynı veya karışık türde birden çok veri kümesi içerebilir.

Eğitilen bir modelin sonuçlarını geliştirmek için, akustik veriler otomatik olarak dil eğitimi sırasında kullanılır. Genel olarak, v3 API 'SI ile oluşturulan modeller v2 API 'siyle oluşturulan modellerden daha doğru sonuçlar sunar.

>[!IMPORTANT]
>Hem akustik hem de dil modeli parçasını özelleştirmek için GÖNDERINIZIN içindeki tüm gerekli dili ve akustik veri kümelerini geçirin `datasets[]` `/speechtotext/v3.0/models` . Bu, her iki bölümden de özelleştirilmiş tek bir model oluşturur.

### <a name="retrieving-base-and-custom-models"></a>Temel ve özel modelleri alma

V3, kullanılabilir modelleri almayı basitleştirmek için "temel modeller" koleksiyonlarını müşterinin "özelleştirilmiş modeller" sahipliğinde ayırmıştır. İki yol artık `GET /speechtotext/v3.0/models/base` ve ' dir `GET /speechtotext/v3.0/models/` .

V2 'de, tüm modeller tek bir yanıtta birlikte döndürülür.

>[!IMPORTANT]
>Özelleştirme için sunulan temel modellerin bir listesini almak için `GET` üzerinde kullanın `/speechtotext/v3.0/models/base` . İle kendi özelleştirilmiş modellerinizi bulabilirsiniz `GET` `/speechtotext/v3.0/models` .

### <a name="name-of-an-entity"></a>Bir varlığın adı

`name`Özelliği artık `displayName` . Bu, kimlik özelliklerini belirtmemelidir diğer Azure API 'lerle tutarlıdır. Bu özelliğin değeri benzersiz olmamalı ve bir işlemle varlık oluşturulduktan sonra değiştirilebilir `PATCH` .

**v2 dökümü:**

```json
{
    "name": "Transcription using locale en-US"
}
```

**v3 dökümü:**

```json
{
    "displayName": "Transcription using locale en-US"
}
```

>[!IMPORTANT]
>Özelliği `name` istemci kodunuzda olarak yeniden adlandırın `displayName` .

### <a name="accessing-referenced-entities"></a>Başvurulan varlıklara erişme

V2 'de, başvurulan Varlıklar her zaman satır içine alındı, örneğin bir uç noktanın kullanılan modelleri. Varlıkların iç içe geçirilmesi büyük yanıtlar ve tüketiciler tarafından nadiren iç içe geçmiş içeriğe neden olarak tüketildi. Yanıt boyutunu küçültmek ve performansı artırmak için başvurulan varlıklar yanıtta artık satır içine alınmaz. Bunun yerine, diğer varlığa yönelik bir başvuru görüntülenir ve bağlantı ile aynı kalıbı takip eden bir sonraki `GET` (BIR URL de) için doğrudan kullanılabilir `self` .

**v2 dökümü:**

```json
{
  "id": "9891c965-bb32-4880-b14b-6d44efb158f3",
  "models": [
    {
      "id": "827712a5-f942-4997-91c3-7c6cde35600b",
      "modelKind": "Language",
      "lastActionDateTime": "2019-01-07T11:36:07Z",
      "status": "Running",
      "createdDateTime": "2019-01-07T11:34:12Z",
      "locale": "en-US",
      "name": "Acoustic model",
      "description": "Example for an acoustic model",
      "datasets": [
        {
          "id": "702d913a-8ba6-4f66-ad5c-897400b081fb",
          "dataImportKind": "Language",
          "lastActionDateTime": "2019-01-07T11:36:07Z",
          "status": "Succeeded",
          "createdDateTime": "2019-01-07T11:34:12Z",
          "locale": "en-US",
          "name": "Language dataset",
        }
      ]
    },
  ]
}
```

**v3 dökümü:**

```json
{
  "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3",
  "model": {
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/models/021a72d0-54c4-43d3-8254-27336ead9037"
  }
}
```

Yukarıdaki örnekte gösterildiği gibi başvurulan bir modelin ayrıntılarını kullanmanız gerekiyorsa, yalnızca bir GET ile sorun verirsiniz `$.model.self` .

>[!IMPORTANT]
>Başvurulan varlıkların meta verilerini almak için bir alma `$.{referencedEntity}.self` işlemi yapın, örneğin, bir döküm modelinin modelini alma `GET` `$.model.self` .


### <a name="retrieving-endpoint-logs"></a>Uç nokta günlüklerini alma

Hizmet sürüm v2 günlük bitiş noktası sonuçlarını destekler. Bir uç noktanın sonuçlarını v2 ile almak için, bir zaman aralığı tarafından tanımlanan sonuçların anlık görüntüsünü temsil eden bir "veri dışarı aktarma" oluşturursunuz. Veri toplu işleri dışarı aktarma işlemi, esnek olarak kullanılabilir. V3 API her bir dosyaya erişim sağlar ve bunlara yineleme sağlar.

**V3 uç noktası başarıyla çalıştırılıyor:**

```json
{
  "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/afa0669c-a01e-4693-ae3a-93baf40f26d6",
  "links": {
    "logs": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/afa0669c-a01e-4693-ae3a-93baf40f26d6/files/logs" 
  }
}
```

**GET yanıtı `$.links.logs` :**

```json
{
  "values": [
    {
      "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/6d72ad7e-f286-4a6f-b81b-a0532ca6bcaa/files/logs/2019-09-20_080000_3b5f4628-e225-439d-bd27-8804f9eed13f.wav",
      "name": "2019-09-20_080000_3b5f4628-e225-439d-bd27-8804f9eed13f.wav",
      "kind": "Audio",
      "properties": {
        "size": 12345
      },
      "createdDateTime": "2020-01-13T08:00:00Z",
      "links": {
        "contentUrl": "https://customspeech-usw.blob.core.windows.net/artifacts/2019-09-20_080000_3b5f4628-e225-439d-bd27-8804f9eed13f.wav?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    }    
  ],
  "@nextLink": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/afa0669c-a01e-4693-ae3a-93baf40f26d6/files/logs?top=2&SkipToken=2!188!MDAwMDk1ITZhMjhiMDllLTg0MDYtNDViMi1hMGRkLWFlNzRlOGRhZWJkNi8yMDIwLTA0LTAxLzEyNDY0M182MzI5NGRkMi1mZGYzLTRhZmEtOTA0NC1mODU5ZTcxOWJiYzYud2F2ITAwMDAyOCE5OTk5LTEyLTMxVDIzOjU5OjU5Ljk5OTk5OTlaIQ--"
}
```

Uç nokta günlükleri için sayfalandırma, hiçbir uzaklığa izin belirtilmedikçe diğer tüm koleksiyonlara benzer şekilde işler. Büyük miktarda kullanılabilir veri nedeniyle, sayfalama sunucu tarafından belirlenir.

V3 'de her bir uç nokta günlüğü, `DELETE` bir dosyanın üzerinde bir işlem göndererek `self` veya üzerinde kullanılarak tek tek silinebilir `DELETE` `$.links.logs` . Bir bitiş tarihi belirtmek için, isteğe sorgu parametresi `endDate` eklenebilir.

>[!IMPORTANT]
>Günlük `/api/speechtotext/v2.0/endpoints/{id}/data` `/v3.0/endpoints/{id}/files/logs/` dosyalarına tek tek erişmek için kullanılan günlük dışarı aktarmaları oluşturmak yerine. 

### <a name="using-custom-properties"></a>Özel özellikleri kullanma

İsteğe bağlı yapılandırma özelliklerinden özel özellikleri ayırmak için, açıkça adlandırılmış tüm özellikler artık `properties` özellikte bulunur ve çağıranlar tarafından tanımlanan tüm özellikler artık `customProperties` özellikte bulunur.

**v2 dökümü varlığı:**

```json
{
  "properties": {
    "customerDefinedKey": "value",
    "diarizationEnabled": "False",
    "wordLevelTimestampsEnabled": "False"
  }
}
```

**v3 dökümü varlığı:**

```json
{
  "properties": {
    "diarizationEnabled": false,
    "wordLevelTimestampsEnabled": false 
  },
  "customProperties": {
    "customerDefinedKey": "value"
  }
}
```

Bu değişiklik Ayrıca `properties` (örneğin, dize yerine Boolean gibi) tüm açıkça adlandırılmış özellikler üzerinde doğru türleri kullanmanıza imkan tanır.

>[!IMPORTANT]
>Tüm özel özellikleri `customProperties` istekleriniz yerine geçirin `properties` `POST` .

### <a name="response-headers"></a>Yanıt üst bilgileri

V3, `Operation-Location` isteklerle ilgili üstbilgiye ek olarak artık üstbilgiyi döndürmez `Location` `POST` . V2 içindeki her iki üst bilgilerin değeri de aynıdır. Şimdi yalnızca `Location` döndürüldü.

Yeni API sürümü artık Azure API Management (APıM) tarafından yönetildiğinden, ilgili üst bilgiler üzerinde kısıtlama, `X-RateLimit-Limit` `X-RateLimit-Remaining` ve `X-RateLimit-Reset` yanıt üst bilgilerinde yer almayan bir.

>[!IMPORTANT]
>Yerine yanıt üst bilgisinden konumu okuyun `Location` `Operation-Location` . 429 yanıt kodu söz konusu olduğunda, `Retry-After` veya yerine üst bilgi değerini okuyun `X-RateLimit-Limit` `X-RateLimit-Remaining` `X-RateLimit-Reset` .


### <a name="accuracy-tests"></a>Doğruluk testleri

Yeni ad, ne kadar iyi temsil ettiğini açıkladığı için doğruluk testleri değerlendirmelere yeniden adlandırıldı. Yeni yollar şunlardır: `https://{region}.api.cognitive.microsoft.com/speechtotext/v3.0/evaluations` .

>[!IMPORTANT]
>Yol segmentini `accuracytests` istemci kodunuzda olarak yeniden adlandırın `evaluations` .


## <a name="next-steps"></a>Sonraki adımlar

Konuşma Hizmetleri tarafından sunulan bu yaygın olarak kullanılan REST API 'lerinin tüm özelliklerini inceleyin:

* [Konuşmayı metne dönüştürme REST API'si](rest-speech-to-text.md)
* REST API v3 için [Swagger belgesi](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)
* Toplu iş dökümlerini gerçekleştirmeye yönelik örnek kod için alt dizinde [GitHub örnek deposunu](https://aka.ms/csspeech/samples) görüntüleyin `samples/batch` .
