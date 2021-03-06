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
ms.custom: devx-track-csharp
ms.openlocfilehash: 87fa97dafe9de4a23f5eaadfd4083cd1ca517cde
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "95026599"
---
# <a name="about-the-speech-sdk-audio-input-stream-api"></a>Konuşma SDK 'Sı ses girişi akış API 'SI hakkında

Konuşma SDK 'sının **ses girişi akış** API 'si, mikrofon veya giriş dosyası API 'leri kullanmak yerine tanıyıcıya ses akışı yapmanın bir yolunu sağlar.

Ses giriş akışları kullanılırken aşağıdaki adımlar gereklidir:

- Ses akışının biçimini belirler. Biçim, konuşma SDK 'Sı ve konuşma hizmeti tarafından desteklenmelidir. Şu anda yalnızca aşağıdaki yapılandırma desteklenir:

  Ses örnekleri, PCM biçiminde, bir kanal, örnek başına 16 bit, 8000 veya 16000 örnek/saniye (16000 veya 32000 bayt), iki blok hizalı (örnek için doldurma dahil olmak üzere 16 bit).

  Ses biçimini oluşturmak için SDK 'daki karşılık gelen kod şöyle görünür:

  ```csharp
  byte channels = 1;
  byte bitsPerSample = 16;
  int samplesPerSecond = 16000; // or 8000
  var audioFormat = AudioStreamFormat.GetWaveFormatPCM(samplesPerSecond, bitsPerSample, channels);
  ```

- Kodunuzun ham ses verilerini bu belirtimlere göre sağladığından emin olun. Ayrıca, 16 bit örneklerin küçük endian biçiminde gelmesini güvence altına alabilirsiniz. İmzalı örnekler de desteklenir. Ses kaynağı verileriniz desteklenen biçimleriyle eşleşmiyorsa, sesin gerekli biçime dönüştürülmesi gerekir.

- Öğesinden türetilmiş kendi ses giriş akışı sınıfınızı oluşturun `PullAudioInputStreamCallback` . `Read()`Ve üyelerini uygulayın `Close()` . Tam işlev imzası dile bağımlıdır, ancak kod şu kod örneğine benzer şekilde görünür:

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

- Ses biçiminizi ve giriş akışınızı temel alan bir ses yapılandırması oluşturun. Tanıyıcıyı oluştururken hem normal konuşma yapılandırmanızın hem de ses giriş yapılandırmasında geçiş yapın. Örnek:

  ```csharp
  var audioConfig = AudioConfig.FromStreamInput(new ContosoAudioStream(config), audioFormat);

  var speechConfig = SpeechConfig.FromSubscription(...);
  var recognizer = new SpeechRecognizer(speechConfig, audioConfig);

  // run stream through recognizer
  var result = await recognizer.RecognizeOnceAsync();

  var text = result.GetText();
  ```

## <a name="next-steps"></a>Sonraki adımlar

- [Ücretsiz bir Azure hesabı oluşturma](https://azure.microsoft.com/free/cognitive-services/)
- [Bkz. C 'de konuşmayı tanıma #](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnet)