---
title: Konuşma SDK 'Sı ses girişi akışı kavramları
titleSuffix: Azure Cognitive Services
description: Konuşma SDK 'sının ses giriş akışı API 'sinin özelliklerine genel bakış.
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: fmegen
ms.openlocfilehash: 3039276a49e7bb41660d114e78ca047a3f77f279
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74109939"
---
# <a name="about-the-speech-sdk-audio-input-stream-api"></a>Konuşma SDK 'Sı ses girişi akış API 'SI hakkında

Konuşma SDK 'sının **ses girişi akış** API 'si, mikrofon veya giriş dosyası API 'leri kullanmak yerine tanıyıcıya ses akışı yapmanın bir yolunu sağlar.

Ses giriş akışları kullanılırken aşağıdaki adımlar gereklidir:

- Ses akışının biçimini belirler. Biçim, konuşma SDK 'Sı ve konuşma hizmeti tarafından desteklenmelidir. Şu anda yalnızca aşağıdaki yapılandırma desteklenir:

  PCM biçimindeki ses örnekleri, bir kanal, saniyede 16000 örnek, 32000 bayt/saniye, iki blok hizalanır (örnek için doldurma dahil olmak üzere 16 bit), örnek başına 16 bit.

  Ses biçimini oluşturmak için SDK 'daki karşılık gelen kod şöyle görünür:

  ```csharp
  byte channels = 1;
  byte bitsPerSample = 16;
  int samplesPerSecond = 16000;
  var audioFormat = AudioStreamFormat.GetWaveFormatPCM(samplesPerSecond, bitsPerSample, channels);
  ```

- Kodunuzun ham ses verilerini bu belirtimlere göre sağlaya, emin olun. Ses kaynağı verileriniz desteklenen biçimleriyle eşleşmiyorsa, sesin gerekli biçime dönüştürülmesi gerekir.

- `PullAudioInputStreamCallback`türetilmiş kendi ses giriş akışı sınıfınızı oluşturun. Uygulama `Read()` ve `Close()` üyeleri. Tam işlev imzası dile bağımlıdır, ancak kod şu kod örneğine benzer şekilde görünür:

  ```csharp
   public class ContosoAudioStream : PullAudioInputStreamCallback {
      ContosoConfig config;

      public ContosoAudioStream(const ContosoConfig& config) {
          this.config = config;
      }

      public int Read(byte[] buffer, uint size) {
          // returns audio data to the caller.
          // e.g. return read(config.YYY, buffer, size);
      }

      public void Close() {
          // close and cleanup resources.
      }
   };
  ```

- Ses biçiminizi ve giriş akışınızı temel alan bir ses yapılandırması oluşturun. Tanıyıcıyı oluştururken hem normal konuşma yapılandırmanızın hem de ses giriş yapılandırmasında geçiş yapın. Örneğin:

  ```csharp
  var audioConfig = AudioConfig.FromStreamInput(new ContosoAudioStream(config), audioFormat);

  var speechConfig = SpeechConfig.FromSubscription(...);
  var recognizer = new SpeechRecognizer(speechConfig, audioConfig);

  // run stream through recognizer
  var result = await recognizer.RecognizeOnceAsync();

  var text = result.GetText();
  ```

## <a name="next-steps"></a>Sonraki adımlar

- [Konuşma deneme aboneliğinizi alın](https://azure.microsoft.com/try/cognitive-services/)
- [C# ' de Konuşma tanıma öğrenin](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
