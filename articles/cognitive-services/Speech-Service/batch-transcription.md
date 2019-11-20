---
title: Toplu Iş dökümü kullanma-konuşma hizmeti
titleSuffix: Azure Cognitive Services
description: Batch transkripsiyonu, depolama, Azure BLOB'ları gibi ses büyük bir miktarını konuşmaların istiyorsanız idealdir. Adanmış REST API'sini kullanarak bir paylaşılan erişim imzası (SAS) URI ses dosyalarının üzerine gelin ve döküm zaman uyumsuz olarak alır.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: panosper
ms.openlocfilehash: 101cfacf071292d00556656b0df9c6bf9c15f414
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/15/2019
ms.locfileid: "69515873"
---
# <a name="why-use-batch-transcription"></a>Batch transkripsiyonu neden kullanmalısınız?

Batch transkripsiyonu, depolama, Azure BLOB'ları gibi ses büyük bir miktarını konuşmaların istiyorsanız idealdir. Adanmış REST API'sini kullanarak bir paylaşılan erişim imzası (SAS) URI ses dosyalarının üzerine gelin ve döküm zaman uyumsuz olarak alır.

## <a name="prerequisites"></a>Önkoşullar

### <a name="subscription-key"></a>Abonelik Anahtarı

Konuşma hizmeti tüm özellikleri ile bir abonelik anahtarı oluştururken [Azure portalında](https://portal.azure.com) izleyerek bizim [Başlarken Kılavuzu](get-started.md). Bizim temel modellerinden döküm almak planlıyorsanız, bir anahtar oluşturmak tek yapmanız gereken bir işlemdir.

>[!NOTE]
> Konuşma Hizmetleri standart aboneliği (S0), batch transkripsiyonu kullanmak için gereklidir. Ücretsiz Abonelik anahtarları (F0) işe yaramaz. Ek bilgi için bkz: [fiyatlandırma ve limitler](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="custom-models"></a>Özel modeller

Akustik veya dil modellerini özelleştirmeyi planlıyorsanız, [akustik modelleri özelleştirme](how-to-customize-acoustic-models.md) ve [dil modellerini özelleştirme](how-to-customize-language-model.md)bölümündeki adımları izleyin. Toplu iş dökümlerinde oluşturulan modelleri kullanmak için model kimliklerine ihtiyacınız vardır. Bu KIMLIK, uç nokta Ayrıntıları görünümünde bulduğunuz uç nokta KIMLIĞI değil, modellerin ayrıntılarını seçerken alabileceğiniz model KIMLIĞIDIR.

## <a name="the-batch-transcription-api"></a>Batch tanıma API'si

Batch tanıma API'si, ek özellikleri ile birlikte zaman uyumsuz konuşma metin tanıma sunar. Bu yöntemleri gösteren bir REST API'si değil:

1. Toplu işlem isteği oluşturma
1. Sorgu durumu
1. Döküm indiriliyor

> [!NOTE]
> Batch tanıma API'si, genellikle saatlik ses binlerce accumulate çağrı merkezleri için idealdir. Büyük hacimde ses kaydı yapmayı kolaylaştırır.

### <a name="supported-formats"></a>Desteklenen biçimler

Batch tanıma API'si, aşağıdaki biçimlerde destekler:

| Biçimlendir | Codec bileşeni | Bit hızı | Örnek hızı |
|--------|-------|---------|-------------|
| WAV | PCM | 16-bit | 8 veya 16 kHz, mono, stereo |
| MP3 | PCM | 16-bit | 8 veya 16 kHz, mono, stereo |
| OGG | GEÇERLİ | 16-bit | 8 veya 16 kHz, mono, stereo |

Stereo ses akışları için Batch transkripsiyonu API sol ve sağ kanal döküm sırasında böler. Sonuç ile iki JSON dosyaları her tek bir kanaldan oluşturulur. Zaman damgaları utterance başına bir sıralı son döküm oluşturmak Geliştirici etkinleştirin. Bu örnek istek, küfür filtrelemesinin, noktalama işaretlerinin ve sözcük düzeyi zaman damgalarının özelliklerini içerir.

### <a name="configuration"></a>Yapılandırma

Yapılandırma parametreleri JSON olarak sağlanır:

```json
{
  "recordingsUrl": "<URL to the Azure blob to transcribe>",
  "models": [{"Id":"<optional acoustic model ID>"},{"Id":"<optional language model ID>"}],
  "locale": "<locale to us, for example en-US>",
  "name": "<user defined name of the transcription batch>",
  "description": "<optional description of the transcription>",
  "properties": {
    "ProfanityFilterMode": "Masked",
    "PunctuationMode": "DictatedAndAutomatic",
    "AddWordLevelTimestamps" : "True",
    "AddSentiment" : "True"
  }
}
```

> [!NOTE]
> Döküm, durum ve ilişkili sonuçları istenirken bir REST hizmeti ve Batch tanıma API'sini kullanır. Herhangi bir dilde API'den kullanabilirsiniz. Sonraki bölümde, API'yi nasıl kullanıldığını açıklar.

### <a name="configuration-properties"></a>Yapılandırma özellikleri

Dökümü yapılandırmak için bu isteğe bağlı özellikleri kullanın:

| Parametre | Açıklama |
|-----------|-------------|
| `ProfanityFilterMode` | Tanıma sonuçları küfür nasıl ele alınacağını belirtir. Kabul edilen değerler `None` , devre dışı bırakır küfür filtresi `masked` yıldız işareti ile küfür değiştirir `removed` sonuç, tüm küfür kaldırır veya `tags` "küfür" etiketleri ekler. Varsayılan ayar `masked`. |
| `PunctuationMode` | Noktalama işaretleri tanıma sonuçları nasıl ele alınacağını belirtir. Değerler kabul `None` , devre dışı bırakır, noktalama `dictated` açık noktalama gelir `automatic` noktalama işaretleri ile uğraşmak kod çözücü olanak tanıyan veya `dictatedandautomatic` dikte noktalama işaretleri veya otomatik olduğu anlamına gelir. |
 | `AddWordLevelTimestamps` | Sözcük düzeyi tarih damgalarının çıktıya eklenip eklenmesinin gerekip gerekmediğini belirtir. Kabul edilen değerler `true` , Word düzeyi zaman damgalarını ve `false` (varsayılan değer) devre dışı bırakmak için izin veren değerlerdir. |
 | `AddSentiment` | Yaklaşım 'ın utterance 'e eklenmesi gerektiğini belirtir. Kabul edilen değerler `true` , her ay için yaklaşım ve `false` (varsayılan değer) devre dışı bırakmak için kullanılır. |
 | `AddDiarization` | İki ses içeren mono kanalı olması beklenen girişte, seçme analizinin gerçekleştirilmesi gerektiğini belirtir. Kabul edilen değerler `true` ve `false` devre dışı bırakmak için (varsayılan değer) izin verilir. Ayrıca, true `AddWordLevelTimestamps` olarak ayarlanması gerekir.|

### <a name="storage"></a>Depolama

Toplu iş dökümü, ses okumak ve depoya yazmak için [Azure Blob depolamayı](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) destekler.

## <a name="webhooks"></a>Web Kancaları

Döküm durumu için yoklama en iyi performans olmayabilir veya en iyi kullanıcı deneyimini sağlayabilir. Durumu yoklamak için geri çağırmaları kaydedebilirsiniz, bu, uzun süre çalışan döküm görevleri tamamlandığında istemciye bildirim gönderir.

Daha ayrıntılı bilgi için bkz. [Web kancaları](webhooks.md).

## <a name="speaker-separation-diarization"></a>Konuşmacı ayrımı (Diarleştirme)

Kararlama, hoparlörleri ses parçasıyla ayırma işlemidir. Batch Işlem hatmız, daha fazla seçim destekleiyor ve mono kanal kayıtlarında iki hoparlörleri tanıyor.

Ses dökümü isteğiniz için işlenmek üzere işlenmesini istemek için, yalnızca aşağıda gösterildiği gibi, HTTP isteğine ilgili parametresini eklemeniz gerekir.

 ```json
{
  "recordingsUrl": "<URL to the Azure blob to transcribe>",
  "models": [{"Id":"<optional acoustic model ID>"},{"Id":"<optional language model ID>"}],
  "locale": "<locale to us, for example en-US>",
  "name": "<user defined name of the transcription batch>",
  "description": "<optional description of the transcription>",
  "properties": {
    "AddWordLevelTimestamps" : "True",
    "AddDiarization" : "True"
  }
}
```

Yukarıdaki istekteki parametreler gösterildiği gibi, sözcük düzeyi tarih damgalarının de ' açık ' olması gerekir.

Karşılık gelen ses, bir sayıyla tanımlanan hoparlörleri içerir (Şu anda yalnızca iki ses destekliyoruz, bu nedenle konuşmacı, ' hoparlör 1 ' ve ' hoparlör 2 ' olarak tanımlanır) ve ardından döküm çıkışı gelir.

Ayrıca, stereo kayıtlarında bu özelliği kullanılamaz durumda olduğunu unutmayın. Ayrıca, tüm JSON çıktılarına konuşmacı etiketi de bulunur. Kullanım kullanılmıyorsa, ' Konuşmacı: JSON çıktısında null '.

> [!NOTE]
> Tüm bölgelerde ve tüm yerel ayarlarda kullanılabilir.

## <a name="sentiment"></a>Yaklaşım

Yaklaşım, toplu Iş dökümü API 'sindeki yeni bir özelliktir ve çağrı merkezi etki alanındaki önemli bir özelliktir. Müşteriler, `AddSentiment` parametreleri için isteklerini kullanabilir

1.  Müşteri memnuniyeti hakkında Öngörüler edinin
2.  Aracıların performansı hakkında öngörüler elde edin (çağrıları alan takım)
3.  Bir çağrının negatif yönde bir değer aldığı zaman içinde tam zaman noktası
4.  Pozitif yapılan olumsuz çağrıları etkinleştirirken, Pinpoint ne kadar iyi bir sorun oluştu
5.  Beğendikleri müşterileri ve bir ürün veya hizmet hakkında neleri beğendiklerinizi tanımlayın

Yaklaşım, bir ses kesiminin, utterlik (fark) başlangıcı ve bayt akışı için algılama sessizliği arasında geçen süre içinde tanımlandığı bir ses kesimi başına puanlanır. Bu kesimdeki tüm metin, yaklaşımı hesaplamak için kullanılır. Tüm çağrı veya her bir kanalın tüm konuşma için herhangi bir toplama yaklaşımı değeri hesaplamaz. Bu toplamalar daha sonra uygulamak için etki alanı sahibine bırakılır.

Yaklaşım, sözlü biçimde uygulanır.

Bir JSON çıkış örneği aşağıdaki gibi görünür:

```json
{
  "AudioFileResults": [
    {
      "AudioFileName": "Channel.0.wav",
      "AudioFileUrl": null,
      "SegmentResults": [
        {
          "RecognitionStatus": "Success",
          "ChannelNumber": null,
          "Offset": 400000,
          "Duration": 13300000,
          "NBest": [
            {
              "Confidence": 0.976174,
              "Lexical": "what's the weather like",
              "ITN": "what's the weather like",
              "MaskedITN": "what's the weather like",
              "Display": "What's the weather like?",
              "Words": null,
              "Sentiment": {
                "Negative": 0.206194,
                "Neutral": 0.793785,
                "Positive": 0.0
              }
            }
          ]
        }
      ]
    }
  ]
}
```
Özelliği, şu anda beta sürümünde olan bir yaklaşım modeli kullanır.

## <a name="sample-code"></a>Örnek kod

Tüm örnekler, `samples/batch` alt dizinin içindeki [GitHub örnek deposunda](https://aka.ms/csspeech/samples) bulunur.

Örnek kodu Abonelik bilgileriniz, hizmet bölgesi, ses dosyasına işaret edilecek SAS URI 'SI ve özel bir akustik veya dil modeli kullanmak istiyorsanız model kimlikleri ile özelleştirmeniz gerekir.

[!code-csharp[Configuration variables for batch transcription](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchdefinition)]

Örnek kod, istemcisini ayarlar ve döküm isteğini gönderir. Daha sonra durum bilgilerini yoklayacak ve döküm ilerlemesiyle ilgili ayrıntıları yazdıracaktır.

[!code-csharp[Code to check batch transcription status](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchstatus)]

Önceki çağrılar hakkında tam Ayrıntılar için [Swagger belgemizi](https://westus.cris.ai/swagger/ui/index)inceleyin. Burada gösterilen tam örnek için `samples/batch` alt dizinde [GitHub](https://aka.ms/csspeech/samples) ' a gidin.

Ses gönderme ve döküm durumu almak için zaman uyumsuz Kurulum not alın. Oluşturduğunuz .NET HTTP istemci istemcisidir. Var. bir `PostTranscriptions` ses dosyası ayrıntılarını göndermek için yöntem ve bir `GetTranscriptions` sonuçları almak için yöntemi. `PostTranscriptions` bir tanıtıcı döndürür ve `GetTranscriptions` transkripsiyonu durumu almak için bir tanıtıcı oluşturmak için kullanır.

Geçerli örnek kod, özel bir model belirtmez. Hizmet, dosya veya dosyalar fotoğrafını için temel modelleri kullanır. Modelleri belirtmek için model kimliklerini akustik ve dil modeli için aynı yönteme geçirebilirsiniz.

> [!NOTE]
> Ana hat düzenlemeleri için, taban çizgisi modellerinin KIMLIĞINI bildirmeniz gerekmez. Yalnızca bir dil modeli KIMLIĞI belirtirseniz (ve akustik model KIMLIĞI yoksa), eşleşen bir akustik model otomatik olarak seçilir. Yalnızca bir akustik model KIMLIĞI belirtirseniz, eşleşen bir dil modeli otomatik olarak seçilir.

## <a name="download-the-sample"></a>Örneği indirme

Örneği `samples/batch` [GitHub örnek deposundaki](https://aka.ms/csspeech/samples)dizinde bulabilirsiniz.

> [!NOTE]
> Toplu iş dökümü işleri en iyi çaba temelinde zamanlanır, bir işin çalışma durumuna ne zaman değişene olacağı için bir zaman tahmini yoktur. Çalışır duruma getirildikten sonra gerçek döküm, ses gerçek saatinden daha hızlı işlenir.

## <a name="next-steps"></a>Sonraki adımlar

* [Konuşma deneme aboneliğinizi alın](https://azure.microsoft.com/try/cognitive-services/)
