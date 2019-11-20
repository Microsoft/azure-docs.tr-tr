---
title: 'Hızlı başlangıç: ses dosyasına konuşma Sentezleştir C# , (.net)-konuşma hizmeti'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/28/2019
ms.author: erhopf
ms.openlocfilehash: 24f44d02170866645711cb302453a009d2935fbf
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73503104"
---
## <a name="prerequisites"></a>Önkoşullar

Başlamadan önce şunları yaptığınızdan emin olun:

> [!div class="checklist"]
> * [Azure konuşma kaynağı oluşturma](../../../../get-started.md)
> * [Geliştirme ortamınızı kurma](../../../../quickstarts/setup-platform.md?tabs=dotnet)
> * [Boş bir örnek proje oluşturun](../../../../quickstarts/create-project.md?tabs=dotnet)

## <a name="open-your-project-in-visual-studio"></a>Projenizi Visual Studio 'da açın

İlk adım, projenizin Visual Studio 'da açık olduğundan emin olmak.

1. Visual Studio 2019 ' i başlatın.
2. Projenizi yükleyin ve `Program.cs`açın.

## <a name="start-with-some-boilerplate-code"></a>Bazı demirbaş kodla başlayın

Projemiz için bir çatı olarak çalışacak bir kod ekleyelim. `SynthesisToAudioFileAsync()`adlı zaman uyumsuz bir yöntem oluşturduğunuzdan emin olun.

````C#

using System;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;

namespace helloworld
{
    class Program
    {
        public static async Task SynthesisToAudioFileAsync()
        {
        }

        static void Main()
        {
            SynthesisToAudioFileAsync().Wait();
        }
    }
}

````

## <a name="create-a-speech-configuration"></a>Konuşma yapılandırması oluşturma

Bir `SpeechSynthesizer` nesnesi başlatabilmeniz için önce abonelik anahtarınızı ve abonelik bölgenizi kullanan bir yapılandırma oluşturmanız gerekir. `SynthesisToAudioFileAsync()` yöntemine bu kodu ekleyin.

````C#
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
````

## <a name="create-an-audio-configuration"></a>Ses yapılandırması oluşturma

Şimdi, ses dosyanıza işaret eden bir ````AudioConfig```` nesnesi oluşturmanız gerekir. Yönetilmeyen kaynakların doğru şekilde yayınlanmasıyla emin olmak için bir using ifadesinin içinde bu nesne oluşturulur. Bu kodu, konuşma yapılandırmanızın hemen altına `SynthesisToAudioFileAsync()` yöntemine ekleyin.

````C#
var fileName = "helloworld.wav";
using (var fileOutput = AudioConfig.FromWavFileOutput(fileName))
{
}
````

## <a name="initialize-a-speechsynthesizer"></a>SpeechSynthesizer başlatma

Şimdi, daha önce oluşturulan `SpeechConfig` ve `AudioConfig` nesnelerini kullanarak `SpeechSynthesizer` nesnesini oluşturalım. Bu nesne, yönetilmeyen kaynakların uygun şekilde serbest bırakılması için bir using ifadesinin içinde de oluşturulur. Bu kodu, ````AudioConfig```` nesnenizin sarmaladığı using ifadesinin içinde `SynthesisToAudioFileAsync()` yöntemine ekleyin.

````C#
using (var synthesizer = new SpeechSynthesizer(config, fileOutput))
{
}
````

## <a name="synthesize-text-using-speaktextasync"></a>Hoparlörktextasync kullanarak metin sentezleştir

`SpeechSynthesizer` nesnesinden `SpeakTextAsync()` yöntemini çağıracağız. Bu yöntem, metninizi sese dönüştüren konuşma hizmetine gönderir. `SpeechSynthesizer`, `config.VoiceName` açıkça belirtilmemişse, varsayılan sesi kullanacaktır.

Using ifadesinin içinde şu kodu ekleyin:
````C#
var text = "Hello world!";
var result = await synthesizer.SpeakTextAsync(text);
````

## <a name="check-for-errors"></a>Hataları denetle

Senşme sonucu konuşma hizmeti tarafından döndürüldüğünde, metninizin başarıyla birleştirerek emin olmak için ' i denetlemeniz gerekir.

Using ifadesinin içinde, `SpeakTextAsync()`aşağıdaki kodu ekleyin:
````C#
if (result.Reason == ResultReason.SynthesizingAudioCompleted)
{
    Console.WriteLine($"Speech synthesized to [{fileName}] for text [{text}]");
}
else if (result.Reason == ResultReason.Canceled)
{
    var cancellation = SpeechSynthesisCancellationDetails.FromResult(result);
    Console.WriteLine($"CANCELED: Reason={cancellation.Reason}");

    if (cancellation.Reason == CancellationReason.Error)
    {
        Console.WriteLine($"CANCELED: ErrorCode={cancellation.ErrorCode}");
        Console.WriteLine($"CANCELED: ErrorDetails=[{cancellation.ErrorDetails}]");
        Console.WriteLine($"CANCELED: Did you update the subscription info?");
    }
}
````

## <a name="check-your-code"></a>Kodunuzu denetleyin

Bu noktada, kodunuzun şöyle görünmesi gerekir:

````C#
//
// Copyright (c) Microsoft. All rights reserved.
// Licensed under the MIT license. See LICENSE.md file in the project root for full license information.
//

using System;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;

namespace helloworld
{
    class Program
    {
        public static async Task SynthesisToAudioFileAsync()
        {
            var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");

            var fileName = "helloworld.wav";
            using (var fileOutput = AudioConfig.FromWavFileOutput(fileName))
            {
                using (var synthesizer = new SpeechSynthesizer(config, fileOutput))
                {
                    var text = "Hello world!";
                    var result = await synthesizer.SpeakTextAsync(text);

                    if (result.Reason == ResultReason.SynthesizingAudioCompleted)
                    {
                        Console.WriteLine($"Speech synthesized to [{fileName}] for text [{text}]");
                    }
                    else if (result.Reason == ResultReason.Canceled)
                    {
                        var cancellation = SpeechSynthesisCancellationDetails.FromResult(result);
                        Console.WriteLine($"CANCELED: Reason={cancellation.Reason}");

                        if (cancellation.Reason == CancellationReason.Error)
                        {
                            Console.WriteLine($"CANCELED: ErrorCode={cancellation.ErrorCode}");
                            Console.WriteLine($"CANCELED: ErrorDetails=[{cancellation.ErrorDetails}]");
                            Console.WriteLine($"CANCELED: Did you update the subscription info?");
                        }
                    }
                }
            }
        }

        static void Main()
        {
            SynthesisToAudioFileAsync().Wait();
        }
    }
}
````

## <a name="build-and-run-your-app"></a>Uygulamanızı derleyin ve çalıştırın

Artık uygulamanızı oluşturmaya hazır olduğunuzdan konuşma senemizi konuşma hizmetini kullanarak test edebilirsiniz.

1. **Kodu derle** -Visual stuio menü çubuğundan **derleme** > **Build Solution**' ı seçin.
2. **Uygulamanızı başlatın** -menü çubuğundan **Hata Ayıkla** > hata **ayıklamayı Başlat** ' ı seçin veya **F5**tuşuna basın.
3. **Sensıs Başlat** -metniniz konuşmaya dönüştürülür ve belirtilen ses verilerinde kaydedilir.

   ```text
   Speech synthesized to [helloworld.wav] for text [Hello world!]
   ```

## <a name="next-steps"></a>Sonraki adımlar

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Ayrıca bkz.

- [Özel bir ses oluşturun](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Özel ses örneklerini Kaydet](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
