---
title: Toplu iş dökümü kullanma-konuşma hizmeti
titleSuffix: Azure Cognitive Services
description: Toplu döküm, depolama alanında Azure Blob 'Ları gibi büyük miktarda ses eklemek istiyorsanız idealdir. Adanmış REST API kullanarak, paylaşılan erişim imzası (SAS) URI 'SI olan ses dosyalarını işaret edebilir ve zaman uyumsuz olarak alma yapabilirsiniz.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/23/2020
ms.author: wolfma
ms.custom: devx-track-csharp
ms.openlocfilehash: e48fead4d4364fd84f178388dbfb9158296e687b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "98659980"
---
# <a name="how-to-use-batch-transcription"></a>Toplu iş dökümünü kullanma

Batch dökümü, depolamada büyük miktarda ses eklemenizi sağlayan bir REST API işlemleri kümesidir. Tipik bir URI veya [paylaşılan erişim imzası (SAS)](../../storage/common/storage-sas-overview.md) URI 'si kullanarak ses dosyalarını işaret edebilir ve zaman uyumsuz olarak alma sonuçlarını alabilirsiniz. V 3.0 API 'SI ile bir veya daha fazla ses dosyası verebilir veya bir depolama kapsayıcısını tamamen işleyebilirsiniz.

Aşağıdaki yöntemleri çağırmak için Batch transcripts REST API 'Lerini kullanabilirsiniz:

|    Toplu iş dökümü Işlemi                                             |    Yöntem    |    REST API çağrısı                                   |
|------------------------------------------------------------------------------|--------------|----------------------------------------------------|
|    Yeni bir döküm oluşturur.                                              |    POST      |    speechtotext/v 3.0/döküm            |
|    Kimliği doğrulanmış aboneliğin dökümlerini listesini alır.    |    GET       |    speechtotext/v 3.0/döküm            |
|    Çevrimdışı döküm için desteklenen yerel ayarların bir listesini alır.              |    GET       |    speechtotext/v 3.0/transcripts/yerel ayarlar    |
|    KIMLIĞI tarafından tanımlanan dökümün değişebilir ayrıntılarını günceller.    |    DÜZELTMESI     |    speechtotext/v 3.0/Transcriptions/{id}       |
|    Belirtilen döküm görevini siler.                                 |    DELETE    |    speechtotext/v 3.0/Transcriptions/{id}       |
|    Verilen KIMLIK tarafından tanımlanan dökümü alır.                        |    GET       |    speechtotext/v 3.0/Transcriptions/{id}       |
|    Verilen KIMLIK tarafından tanımlanan döküm sonuç dosyalarını alır.    |    GET       |    speechtotext/v 3.0/Transcriptions/{id}/dosya |

[Swagger belgesi](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)olarak KULLANıLABILEN ayrıntılı API 'yi gözden geçirebilir ve test edebilirsiniz.

Toplu iş dökümü işleri en iyi çaba temelinde zamanlanır.
Bir işin çalışma durumuna ne zaman değişene, ancak normal sistem yükü altında dakikalar içinde gerçekleşmesi gerekir. Çalışma durumunda, döküm, ses çalışma zamanı kayıttan yürütme hızından daha hızlı gerçekleşir.

## <a name="prerequisites"></a>Önkoşullar

Konuşma hizmetinin tüm özelliklerinde olduğu gibi, [kullanmaya başlama kılavuzumuzu](overview.md#try-the-speech-service-for-free)izleyerek [Azure Portal](https://portal.azure.com) bir abonelik anahtarı oluşturursunuz.

>[!NOTE]
> Toplu iş dökümünü kullanmak için, konuşma hizmeti için standart bir abonelik (S0) gereklidir. Ücretsiz abonelik anahtarları (F0) çalışmaz. Daha fazla bilgi için bkz. [fiyatlandırma ve sınırlar](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

Modelleri özelleştirmeyi planlıyorsanız, [akustik özelleştirme](./how-to-custom-speech-train-model.md) ve [dil özelleştirme](./how-to-custom-speech-train-model.md)bölümündeki adımları izleyin. Toplu iş dökümlerinde oluşturulan modelleri kullanmak için model konumuyla ihtiyacınız vardır. Modelin ayrıntılarını inceleyebileceğiniz zaman model konumunu alabilirsiniz ( `self` özellik). Toplu iş dökümü hizmeti için dağıtılan özel bir uç nokta *gerekli değildir* .

>[!NOTE]
> REST API bir parçası olarak toplu Iş dökümü, gözden geçirmeyi teşvik ettiğimiz bir [Kotalar ve sınırlar](speech-services-quotas-and-limits.md#batch-transcription)kümesine sahiptir. Toplu geçiş yeteneğinin çok sayıda ses dosyası olmasını verimli bir şekilde sağlamak için, istek başına birden çok dosya göndermeyi veya bir BLOB depolama kapsayıcısını, bu dosyanın dökümünü alacak şekilde bir BLOB depolama kapsayıcısına işaret etmenizi öneririz. Hizmet, dosyaları aynı anda azaltma süresini azaltır. Tek bir istekte birden çok dosya kullanılması çok basittir ve kolay bir şekilde [yapılandırma](#configuration) bölümüne bakın. 

## <a name="batch-transcription-api"></a>Toplu iş dökümü API 'SI

Toplu iş dökümü API 'SI aşağıdaki biçimleri destekler:

| Biçimlendir | Bileşeni | Örnek başına bit sayısı | Örnek hız             |
|--------|-------|---------|---------------------------------|
| WAV    | PCM   | 16 bit  | 8 kHz veya 16 kHz, mono veya stereo |
| ÇA    | PCM   | 16 bit  | 8 kHz veya 16 kHz, mono veya stereo |
| OGG    | OPUS 'LAR  | 16 bit  | 8 kHz veya 16 kHz, mono veya stereo |

Stereo ses akışları için, sol ve sağ kanallar, döküm sırasında bölünür. Her kanal için bir JSON sonuç dosyası oluşturuluyor.
Sıralı bir son döküm oluşturmak için, utterance başına oluşturulan zaman damgalarını kullanın.

### <a name="configuration"></a>Yapılandırma

Yapılandırma parametreleri JSON olarak sağlanır. 

**Tek bir veya daha fazla dosyayı bir veya daha fazla dosyaya dönüştürme.** Daha fazla bilgi almak için birden fazla dosyanız varsa, tek bir istekte birden çok dosya gönderilmesini öneririz. Aşağıdaki örnekte üç dosya kullanılıyor:

```json
{
  "contentUrls": [
    "<URL to an audio file 1 to transcribe>",
    "<URL to an audio file 2 to transcribe>",
    "<URL to an audio file 3 to transcribe>"
  ],
  "properties": {
    "wordLevelTimestampsEnabled": true
  },
  "locale": "en-US",
  "displayName": "Transcription of file using default model for en-US"
}
```

**Tüm depolama kapsayıcısı işleniyor.** Kapsayıcı [SAS](../../storage/common/storage-sas-overview.md) `r` (okuma) ve `l` (liste) izinlerini içermelidir:

```json
{
  "contentContainerUrl": "<SAS URL to the Azure blob container to transcribe>",
  "properties": {
    "wordLevelTimestampsEnabled": true
  },
  "locale": "en-US",
  "displayName": "Transcription of container using default model for en-US"
}
```

**Toplu iş dökümde özel eğitilen bir model kullanın.** Örnek üç dosya kullanmaktır:

```json
{
  "contentUrls": [
    "<URL to an audio file 1 to transcribe>",
    "<URL to an audio file 2 to transcribe>",
    "<URL to an audio file 3 to transcribe>"
  ],
  "properties": {
    "wordLevelTimestampsEnabled": true
  },
  "locale": "en-US",
  "model": {
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/models/{id}"
  },
  "displayName": "Transcription of file using default model for en-US"
}
```


### <a name="configuration-properties"></a>Yapılandırma özellikleri

Dökümü yapılandırmak için bu isteğe bağlı özellikleri kullanın:

:::row:::
   :::column span="1":::
      **Parametre**
   :::column-end:::
   :::column span="2":::
      **Açıklama**
:::row-end:::
:::row:::
   :::column span="1":::
      `profanityFilterMode`
   :::column-end:::
   :::column span="2":::
      İsteğe bağlı, varsayılan olarak olur `Masked` . Tanıma sonuçlarında küfür nasıl işleneceğini belirtir. Kabul edilen değerler, küfür `None` filtrelemeyi devre dışı bırakmak, `Masked` küfür ile `Removed` tüm küfür kaldırmak veya `Tags` "küfür" etiketleri eklemek için.
:::row-end:::
:::row:::
   :::column span="1":::
      `punctuationMode`
   :::column-end:::
   :::column span="2":::
      İsteğe bağlı, varsayılan olarak olur `DictatedAndAutomatic` . Tanıma sonuçlarında noktalama işaretlerinin nasıl işleneceğini belirtir. Kabul edilen değerler `None` , `Dictated` Açık (konuşulan) noktalama işareti, `Automatic` kod çözücüsünün noktalama ile uğraşmasına veya `DictatedAndAutomatic` Dikte ve otomatik noktalama kullanımına izin vermek için noktalama işaretlerini devre dışı bırakır.
:::row-end:::
:::row:::
   :::column span="1":::
      `wordLevelTimestampsEnabled`
   :::column-end:::
   :::column span="2":::
      İsteğe bağlı, `false` Varsayılan olarak. Sözcük düzeyi tarih damgalarının çıktıya eklenip eklenmesinin gerekip gerekmediğini belirtir.
:::row-end:::
:::row:::
   :::column span="1":::
      `diarizationEnabled`
   :::column-end:::
   :::column span="2":::
      İsteğe bağlı, `false` Varsayılan olarak. Giriş analizinin, iki ses içeren mono kanalı olması beklenen girişte gerçekleştirilmesi gerektiğini belirtir. Note: `wordLevelTimestampsEnabled` olarak ayarlanması gerekir `true` .
:::row-end:::
:::row:::
   :::column span="1":::
      `channels`
   :::column-end:::
   :::column span="2":::
      İsteğe bağlı `0` ve `1` Varsayılan olarak yeniden yapılır. İşlenecek bir kanal numaraları dizisi. Burada, ses dosyasındaki kullanılabilir kanalların bir alt kümesinin işlenmek üzere belirtilenebilir (örneğin, `0` yalnızca).
:::row-end:::
:::row:::
   :::column span="1":::
      `timeToLive`
   :::column-end:::
   :::column span="2":::
      İsteğe bağlı, varsayılan olarak silme yok. Dökümü tamamladıktan sonra otomatik olarak döküm silme süresi. , `timeToLive` Sonunda silindiklerinden emin olmak için toplu işleme (örneğin, `PT12H` 12 saat) yararlı olur.
:::row-end:::
:::row:::
   :::column span="1":::
      `destinationContainerUrl`
   :::column-end:::
   :::column span="2":::
      Azure 'da yazılabilir bir kapsayıcıya, [GEÇICI SAS](../../storage/common/storage-sas-overview.md) ile Isteğe bağlı URL. Sonuç bu kapsayıcıda saklanır. Depolanan erişim ilkesiyle **SAS desteklenmez.** Belirtilmediğinde Microsoft, sonuçları Microsoft tarafından yönetilen bir depolama kapsayıcısında depolar. Döküm, [silme](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/DeleteTranscription)işlemi çağırarak silindiğinde, sonuç verileri de silinir.
:::row-end:::

### <a name="storage"></a>Depolama

Toplu iş dökümü, genel olarak görünen bir internet URI 'sinden ses okuyabilir ve [Azure Blob depolama](../../storage/blobs/storage-blobs-overview.md)Ile SAS URI 'si kullanarak ses okuyabilir veya döküm yazabilirsiniz.

## <a name="batch-transcription-result"></a>Toplu iş dökümü sonucu

Her ses girişi için, bir döküm sonuç dosyası oluşturulur.
[Döküm dosyalarını Al](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetTranscriptionFiles) işlemi, bu döküm için sonuç dosyalarının bir listesini döndürür. Belirli bir giriş dosyası için döküm dosyasını bulmak için, ve ile döndürülen tüm dosyaları filtreleyin `kind`  ==  `Transcription` `name`  ==  `{originalInputName.suffix}.json` .

Her döküm sonuç dosyası şu biçimdedir:

```json
{
  "source": "...",                      // sas url of a given contentUrl or the path relative to the root of a given container
  "timestamp": "2020-06-16T09:30:21Z",  // creation time of the transcription, ISO 8601 encoded timestamp, combined date and time
  "durationInTicks": 41200000,          // total audio duration in ticks (1 tick is 100 nanoseconds)
  "duration": "PT4.12S",                // total audio duration, ISO 8601 encoded duration
  "combinedRecognizedPhrases": [        // concatenated results for simple access in single string for each channel
    {
      "channel": 0,                     // channel number of the concatenated results
      "lexical": "hello world",
      "itn": "hello world",
      "maskedITN": "hello world",
      "display": "Hello world."
    }
  ],
  "recognizedPhrases": [                // results for each phrase and each channel individually
    {
      "recognitionStatus": "Success",   // recognition state, e.g. "Success", "Failure"          
      "speaker": 1,                     // if `diarizationEnabled` is `true`, this is the identified speaker (1 or 2), otherwise this property is not present
      "channel": 0,                     // channel number of the result
      "offset": "PT0.07S",              // offset in audio of this phrase, ISO 8601 encoded duration 
      "duration": "PT1.59S",            // audio duration of this phrase, ISO 8601 encoded duration
      "offsetInTicks": 700000.0,        // offset in audio of this phrase in ticks (1 tick is 100 nanoseconds)
      "durationInTicks": 15900000.0,    // audio duration of this phrase in ticks (1 tick is 100 nanoseconds)

      // possible transcriptions of the current phrase with confidences
      "nBest": [
        {
          "confidence": 0.898652852,    // confidence value for the recognition of the whole phrase
          "lexical": "hello world",
          "itn": "hello world",
          "maskedITN": "hello world",
          "display": "Hello world.",

          // if wordLevelTimestampsEnabled is `true`, there will be a result for each word of the phrase, otherwise this property is not present
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

Sonuç aşağıdaki alanları içerir:

:::row:::
   :::column span="1":::
      **Alan**
   :::column-end:::
   :::column span="2":::
      **İçerik**
:::row-end:::
:::row:::
   :::column span="1":::
      `lexical`
   :::column-end:::
   :::column span="2":::
      Tanınan gerçek sözcükler.
:::row-end:::
:::row:::
   :::column span="1":::
      `itn`
   :::column-end:::
   :::column span="2":::
      Tanınan metnin ters metin normalleştirilmiş biçimi. Kısaltmalar ("Doctor Smith"-"Dr Smith"), telefon numaraları ve diğer dönüşümler uygulanır.
:::row-end:::
:::row:::
   :::column span="1":::
      `maskedITN`
   :::column-end:::
   :::column span="2":::
      Küfür maskeleme uygulanmış ıTYPEFORM.
:::row-end:::
:::row:::
   :::column span="1":::
      `display`
   :::column-end:::
   :::column span="2":::
      Tanınan metnin görüntüleme formu. Eklenen noktalama işaretleri ve büyük harfler dahil edilir.
:::row-end:::

## <a name="speaker-separation-diarization"></a>Konuşmacı ayrımı (diarleştirme)

Kararlama, hoparlörleri ses parçasıyla ayırma işlemidir. Batch ardışık düzeni, erişimi destekler ve mono kanal kayıtlarında iki hoparlörleri tanıyor. Özellik, stereo kayıtlarında kullanılamaz.

Diarleştirme etkin olan döküm çıkışı, her bir çıkış `Speaker` tümceciği için bir giriş içerir. Eğer Eğer bu özellik kullanılmazsa, `Speaker` ÖZELLIĞI JSON çıktısında yok. İki sesi destekliyoruz, böylece hoparlörler veya olarak tanımlanır `1` `2` .

Daha fazla seçim yapmak için `diarizationEnabled` özelliği, `true` http isteğinin aşağıda gösterildiği gibi ayarla ' yı ekleyin.

 ```json
{
  "contentUrls": [
    "<URL to an audio file to transcribe>",
  ],
  "properties": {
    "diarizationEnabled": true,
    "wordLevelTimestampsEnabled": true,
    "punctuationMode": "DictatedAndAutomatic",
    "profanityFilterMode": "Masked"
  },
  "locale": "en-US",
  "displayName": "Transcription of file using default model for en-US"
}
```

Yukarıdaki istekteki parametreler gösterildiği için sözcük düzeyi zaman damgalarının etkinleştirilmesi gerekir.

## <a name="best-practices"></a>En iyi uygulamalar

Toplu iş dökümü hizmeti, çok sayıda gönderilen dökümü işleyebilir. Onayları [Al](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetTranscriptions)ile birlikte, döküm durumlarını sorgulayabilirsiniz.
Sonuçları aldıktan sonra hizmetten düzenli olarak [silme](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/DeleteTranscription) çağrısını çağırın. Bunun yerine `timeToLive` , sonuçların son silinmesini sağlamak için özelliği de ayarlayın.

## <a name="sample-code"></a>Örnek kod

Tüm örnekler, alt dizinin içindeki [GitHub örnek deposunda](https://aka.ms/csspeech/samples) bulunur `samples/batch` .

Örnek kodu Abonelik bilgileriniz, hizmet bölgeniz, ses dosyasına işaret eden URI ve özel bir model kullanıyorsanız model konumu ile güncelleştirin.

[!code-csharp[Configuration variables for batch transcription](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#transcriptiondefinition)]

Örnek kod, istemciyi ayarlar ve döküm isteğini gönderir. Ardından durum bilgilerini yoklar ve döküm ilerleme durumuyla ilgili ayrıntıları yazdırır.

```csharp
// get the status of our transcriptions periodically and log results
int completed = 0, running = 0, notStarted = 0;
while (completed < 1)
{
    completed = 0; running = 0; notStarted = 0;

    // get all transcriptions for the user
    paginatedTranscriptions = null;
    do
    {
        // <transcriptionstatus>
        if (paginatedTranscriptions == null)
        {
            paginatedTranscriptions = await client.GetTranscriptionsAsync().ConfigureAwait(false);
        }
        else
        {
            paginatedTranscriptions = await client.GetTranscriptionsAsync(paginatedTranscriptions.NextLink).ConfigureAwait(false);
        }

        // delete all pre-existing completed transcriptions. If transcriptions are still running or not started, they will not be deleted
        foreach (var transcription in paginatedTranscriptions.Values)
        {
            switch (transcription.Status)
            {
                case "Failed":
                case "Succeeded":
                    // we check to see if it was one of the transcriptions we created from this client.
                    if (!createdTranscriptions.Contains(transcription.Self))
                    {
                        // not created form here, continue
                        continue;
                    }

                    completed++;

                    // if the transcription was successful, check the results
                    if (transcription.Status == "Succeeded")
                    {
                        var paginatedfiles = await client.GetTranscriptionFilesAsync(transcription.Links.Files).ConfigureAwait(false);

                        var resultFile = paginatedfiles.Values.FirstOrDefault(f => f.Kind == ArtifactKind.Transcription);
                        var result = await client.GetTranscriptionResultAsync(new Uri(resultFile.Links.ContentUrl)).ConfigureAwait(false);
                        Console.WriteLine("Transcription succeeded. Results: ");
                        Console.WriteLine(JsonConvert.SerializeObject(result, SpeechJsonContractResolver.WriterSettings));
                    }
                    else
                    {
                        Console.WriteLine("Transcription failed. Status: {0}", transcription.Properties.Error.Message);
                    }

                    break;

                case "Running":
                    running++;
                    break;

                case "NotStarted":
                    notStarted++;
                    break;
            }
        }

        // for each transcription in the list we check the status
        Console.WriteLine(string.Format("Transcriptions status: {0} completed, {1} running, {2} not started yet", completed, running, notStarted));
    }
    while (paginatedTranscriptions.NextLink != null);

    // </transcriptionstatus>
    // check again after 1 minute
    await Task.Delay(TimeSpan.FromMinutes(1)).ConfigureAwait(false);
}
```

Önceki çağrılar hakkında tam Ayrıntılar için [Swagger belgemizi](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)inceleyin. Burada gösterilen tam örnek için alt dizinde [GitHub](https://aka.ms/csspeech/samples) ' a gidin `samples/batch` .

Bu örnek, ses göndermek ve döküm durumunu almak için zaman uyumsuz bir kurulum kullanır.
`PostTranscriptions`Yöntemi, ses dosyası ayrıntılarını gönderir ve `GetTranscriptions` yöntemi durumları alır.
`PostTranscriptions` bir tanıtıcı döndürür ve bunu, döküm `GetTranscriptions` durumunu almak için bir tanıtıcı oluşturmak üzere kullanır.

Bu örnek kod özel bir model belirtmez. Hizmet, dosya veya dosyaları çözümlemek için temel modeli kullanır. Modeli belirtmek için, özel model için model başvurusuyla aynı yönteme geçiş yapabilirsiniz.

> [!NOTE]
> Taban çizgisi dökümü için, taban çizgisi modelinin KIMLIĞINI bildirmeniz gerekmez.

## <a name="next-steps"></a>Sonraki adımlar

- [Konuşmaya metin v3 API başvurusu](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/CopyModelToSubscription)
