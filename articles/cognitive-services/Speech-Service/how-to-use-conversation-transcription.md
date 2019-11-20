---
title: Gerçek zamanlı konuşma dökümü (Önizleme)-konuşma hizmeti
titleSuffix: Azure Cognitive Services
description: Konuşma SDK 'Sı ile gerçek zamanlı konuşma dökümünü nasıl kullanacağınızı öğrenin. , C#Ve C++Java için kullanılabilir.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: weixu
ms.openlocfilehash: 4ee597f96d28b43b9c69d1515634aadde2f0ab7c
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73608057"
---
# <a name="real-time-conversation-transcription-preview"></a>Gerçek zamanlı konuşma dökümü (Önizleme)

Konuşma SDK **'sı 'nın konuşma** API 'si, `PullStream` veya `PushStream`kullanarak konuşma hizmetlerine ses akışı yaparak, birden fazla katılımcı ekleme, kaldırma ve tanımanıza olanak sağlayan toplantılar ve diğer konuşmalar sağlar. Bu konu, konuşma SDK 'Sı (sürüm 1.8.0 veya üzeri) ile konuşmayı metne nasıl kullanacağınızı bilmeniz gerekir. Daha fazla bilgi için bkz. [konuşma Hizmetleri nedir?](overview.md)

## <a name="limitations"></a>Sınırlamalar

- Windows, Linux ve Android 'de, C++ C#, ve Java için konuşma API 'si desteklenir.
- Şu anda şu bölgelerde "en-US" ve "zh-CN" dillerinde kullanılabilir: _merkezileştirme_ ve _eastaya_.
- Kayıttan yürütme başvuru akışı olan 7 MIC dairesel çok mikrofonlu bir dizi gerektirir. Microphone dizisinin [belirtimizi](https://aka.ms/sdsdk-microphone)karşılaması gerekir.
- [Konuşma cihazları SDK 'sı](speech-devices-sdk.md) , uygun cihazlar ve görüşme dökümünü gösteren örnek bir uygulama sağlar.

## <a name="optional-sample-code-resources"></a>İsteğe bağlı örnek kod kaynakları

Konuşma cihaz SDK 'Sı, 8 kanal kullanan gerçek zamanlı ses yakalama için Java 'da örnek kod sağlar.

- [ROOBO cihazı örnek kodu](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Android/Speech%20Devices%20SDK%20Starter%20App/example/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/sdsdkstarterapp/Conversation.java)
- [Azure Kinect Dev Kit örnek kodu](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Windows_Linux/SampleDemo/src/com/microsoft/cognitiveservices/speech/samples/Cts.java)

## <a name="prerequisites"></a>Ön koşullar

Bir konuşma Hizmetleri aboneliği. Bir [konuşma deneme aboneliği yoksa bir konuşma deneme aboneliği edinebilirsiniz](https://azure.microsoft.com/try/cognitive-services/) .

## <a name="create-voice-signatures"></a>Ses imzaları oluşturma

İlk adım, etkin konuşmacı tanıma için konuşma katılımcıları için ses imzaları oluşturmaktır.

### <a name="audio-input-requirements"></a>Ses girişi gereksinimleri

- Ses imzaları oluşturmak için giriş sesi dalga dosyası 16 bit örneklerde, 16 kHz örnek hızında ve tek kanal (mono) biçiminde olmalıdır.
- Her ses örneği için önerilen uzunluk otuz saniye ile iki dakika arasındadır.

### <a name="sample-code"></a>Örnek kod

Aşağıdaki örnek, içindeki C# [REST API kullanarak](https://aka.ms/cts/signaturegenservice) sesli imza oluşturmanın iki farklı yolunu göstermektedir. "YourSubscriptionKey" için gerçek bilgileri, "Hoparlörkervoice. wav" için dalga dosyası adınızı ve `{region}` ve "YourServiceRegion" (_hacmor_ _eastasıya_) bölgeniz için eklemeniz gerektiğini unutmayın.

```csharp
class Program
{
    static async Task CreateVoiceSignatureByUsingFormData()
    {
        // Replace with your own region
        var region = "YourServiceRegion";
        // Change the name of the wave file to match yours
        byte[] fileBytes = File.ReadAllBytes(@"speakerVoice.wav");
        var form = new MultipartFormDataContent();
        var content = new ByteArrayContent(fileBytes);
        form.Add(content, "file", "file");
        var client = new HttpClient();
        // Add your subscription key to the header Ocp-Apim-Subscription-Key directly
        // Replace "YourSubscriptionKey" with your own subscription key
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "YourSubscriptionKey");
        // Edit with your desired region for `{region}`
        var response = await client.PostAsync($"https://signature.{region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromFormData", form);
        // A voice signature contains Version, Tag and Data key values from the Signature json structure from the Response body.
        // Voice signature format example: { "Version": <Numeric value>, "Tag": "string", "Data": "string" }
        var jsonData = await response.Content.ReadAsStringAsync();
    }

    static async Task CreateVoiceSignatureByUsingBody()
    {
        // Replace with your own region
        var region = "YourServiceRegion";
        // Change the name of the wave file to match yours
        byte[] fileBytes = File.ReadAllBytes(@"speakerVoice.wav");
        var content = new ByteArrayContent(fileBytes);

        var client = new HttpClient();
        // Add your subscription key to the header Ocp-Apim-Subscription-Key directly
        // Replace "YourSubscriptionKey" with your own subscription key
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "YourSubscriptionKey");
        // Edit with your desired region for `{region}`
        var response = await client.PostAsync($"https://signature.{region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromByteArray", content);
        // A voice signature contains Version, Tag and Data key values from the Signature json structure from the Response body.
        // Voice signature format example: { "Version": <Numeric value>, "Tag": "string", "Data": "string" }
        var jsonData = await response.Content.ReadAsStringAsync();
    }

    static void Main(string[] args)
    {
        CreateVoiceSignatureByUsingFormData().Wait();
        CreateVoiceSignatureByUsingBody().Wait();
    }
}
```

## <a name="transcribe-conversations"></a>Konuşmalar

Aşağıdaki örnek kod, üç hoparlör için konuşmaları gerçek zamanlı olarak nasıl oluşturacağınızı gösterir. Yukarıda gösterildiği gibi, her konuşmacı için zaten ses imzaları oluşturmuş olduğunuzu varsayar. SpeechConfig nesnesini oluştururken "YourSubscriptionKey" ve "YourServiceRegion" için gerçek bilgileri değiştirin.

Örnek kod vurguları şunlardır:

- `Guid.NewGuid()` kullanılarak oluşturulan bir toplantı tanımlayıcısı kullanarak `SpeechConfig` nesnesinden `Conversation` nesnesi oluşturma
- `ConversationTranscriber` nesnesi oluşturma ve konuşmaya başlamak için `JoinConversationAsync()` ile konuşmayı birleştirin
- İlgilendiğiniz olayları kaydetme
- Konuşma nesnesini kullanarak konuşmaya katılımcı ekleme veya konuşmayı kaldırma
- Ses akışı

Döküm ve konuşmacı tanımlayıcı, kayıtlı olaylara geri dönmeyecektir.

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
using Microsoft.CognitiveServices.Speech.Transcription;

public class MyConversationTranscriber
{
    public static async Task ConversationWithPullAudioStreamAsync()
    {
        // Creates an instance of a speech config with specified subscription key and service region
        // Replace with your own subscription key and region
        var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
        config.SetProperty("ConversationTranscriptionInRoomAndOnline", "true");
        var stopTranscription = new TaskCompletionSource<int>();

        // Create an audio stream from a wav file or from the default microphone if you want to stream live audio from the supported devices
        // Replace with your own audio file name and Helper class which implements AudioConfig using PullAudioInputStreamCallback
        using (var audioInput = Helper.OpenWavFile(@"8channelsOfRecordedPCMAudio.wav"))
        {
            var meetingId = Guid.NewGuid().ToString();
            using (var conversation = new Conversation(config, meetingId))
            {
                // Create a conversation transcriber using audio stream input
                using (var conversationTranscriber = new ConversationTranscriber    (audioInput))
                {
                    await conversationTranscriber.JoinConversationAsync(conversation);

                    // Subscribe to events
                    conversationTranscriber.Transcribing += (s, e) =>
                    {
                            Console.WriteLine($"TRANSCRIBING: Text={e.Result.Text}");
                    };

                    conversationTranscriber.Transcribed += (s, e) =>
                    {
                        if (e.Result.Reason == ResultReason.RecognizedSpeech)
                        {
                            Console.WriteLine($"TRANSCRIBED: Text={e.Result.Text}, UserID={e.Result.UserId}");
                        }
                        else if (e.Result.Reason == ResultReason.NoMatch)
                        {
                            Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                        }
                    };

                    conversationTranscriber.Canceled += (s, e) =>
                    {
                        Console.WriteLine($"CANCELED: Reason={e.Reason}");

                        if (e.Reason == CancellationReason.Error)
                        {
                            Console.WriteLine($"CANCELED: ErrorCode={e.ErrorCode}");
                            Console.WriteLine($"CANCELED: ErrorDetails={e.ErrorDetails}");
                            Console.WriteLine($"CANCELED: Did you update the subscription info?");
                            stopTranscription.TrySetResult(0);
                        }
                    };

                    conversationTranscriber.SessionStarted += (s, e) =>
                    {
                        Console.WriteLine("\nSession started event.");
                    };

                    conversationTranscriber.SessionStopped += (s, e) =>
                    {
                        Console.WriteLine("\nSession stopped event.");
                        Console.WriteLine("\nStop recognition.");
                        stopTranscription.TrySetResult(0);
                    };

                    // Add participants to the conversation.
                    // Create voice signatures using REST API described in the earlier section in this document.
                    // Voice signature needs to be in the following format:
                    // { "Version": <Numeric value>, "Tag": "string", "Data": "string" }

                    var speakerA = Participant.From("Speaker_A", "en-us", signatureA);
                    var speakerB = Participant.From("Speaker_B", "en-us", signatureB);
                    var speakerC = Participant.From("SPeaker_C", "en-us", signatureC);
                    await conversation.AddParticipantAsync(speakerA);
                    await conversation.AddParticipantAsync(speakerB);
                    await conversation.AddParticipantAsync(speakerC);

                    // Starts transcribing of the conversation. Uses StopTranscribingAsync() to stop transcribing when all participants leave.
                    await conversationTranscriber.StartTranscribingAsync().ConfigureAwait(false);

                    // Waits for completion.
                    // Use Task.WaitAny to keep the task rooted.
                    Task.WaitAny(new[] { stopTranscription.Task });

                    // Stop transcribing the conversation.
                    await conversationTranscriber.StopTranscribingAsync().ConfigureAwait(false);
                 }
            }
       }
    }
}
```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Zaman uyumsuz konuşma dökümü](how-to-async-conversation-transcription.md)
