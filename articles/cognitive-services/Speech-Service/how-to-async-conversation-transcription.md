---
title: Zaman uyumsuz konuşma dökümü (Önizleme)-konuşma hizmeti
titleSuffix: Azure Cognitive Services
description: Konuşma hizmetini kullanarak zaman uyumsuz konuşma dökümünü nasıl kullanacağınızı öğrenin. Yalnızca Java için kullanılabilir.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: amishu
ms.openlocfilehash: 9a2a00a8c76c7b5c02cde623bf93f536b27cf074
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73621960"
---
# <a name="asynchronous-conversation-transcription-preview"></a>Zaman uyumsuz konuşma dökümü (Önizleme)

Bu makalede, **Remotekonuşmayı Tiontranscriptionclient** API 'si kullanılarak zaman uyumsuz görüşme dökümü gösterilmektedir. Zaman uyumsuz dökümü yapmak ve bir `conversationId`sağlamak için konuşma dökümünü yapılandırdıysanız, **Remotekonuşmayı Tiontranscriptionclient** API 'sini kullanarak bu `conversationId` ilişkili dökümü elde edebilirsiniz.

## <a name="asynchronous-vs-real-time--asynchronous"></a>Zaman uyumsuz ile gerçek zamanlı + zaman uyumsuz

Zaman uyumsuz döküm sayesinde, konuşma sesini akışa alırsınız ancak gerçek zamanlı olarak geri döndürülen bir döküm gerekmez. Bunun yerine, ses gönderildikten sonra, zaman uyumsuz döküm durumunu sorgulamak için `Conversation` `conversationId` kullanın. Zaman uyumsuz döküm hazırlandıktan sonra bir `RemoteConversationTranscriptionResult`alırsınız.

Gerçek zamanlı ve zaman uyumsuz olarak, dökümü gerçek zamanlı olarak alır, ancak `conversationId` (zaman uyumsuz senaryoya benzer şekilde) ile sorgulama yaparak da bir zaman alıcı elde edersiniz.

Zaman uyumsuz dökümü gerçekleştirmek için iki adım gerekir. İlk adım, yalnızca zaman uyumsuz veya gerçek zamanlı ve zaman uyumsuz olarak seçerek sesi karşıya yüklemedir. İkinci adım, döküm sonuçlarını almak için kullanılır.

## <a name="upload-the-audio"></a>Sesi karşıya yükle

Zaman uyumsuz döküm gerçekleştirilmeden önce, Microsoft bilişsel konuşma istemci SDK 'sını (sürüm 1.8.0 veya üzeri) kullanarak konuşmayı konuşmaya dönüştürme hizmetine göndermeniz gerekir.

Bu örnek kod, yalnızca zaman uyumsuz mod için görüşme dökümünü oluşturmayı gösterir. Bu şekilde ses akışını sağlamak için [konuşma SDK 'sı ile gerçek zamanlı olarak konuşmadan](how-to-use-conversation-transcription-service.md)elde edilen ses akışı kodunu eklemeniz gerekir. Desteklenen platformlar ve diller API 'Lerini görmek için ilgili konunun **sınırlamalar** bölümüne bakın.

```java
// Create the speech config object
// Substitute real information for "YourSubscriptionKey" and "Region"
SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "Region");
speechConfig.setProperty("ConversationTranscriptionInRoomAndOnline", "true");

// Set the property for asynchronous transcription
speechConfig.setServiceProperty("transcriptionMode", "Async", ServicePropertyChannel.UriQueryParameter);

// Set the property for real-time plus asynchronous transcription
//speechConfig.setServiceProperty("transcriptionMode", "RealTimeAndAsync", ServicePropertyChannel.UriQueryParameter);

// pick a conversation Id that is a GUID.
String conversationId = UUID.randomUUID().toString();

// Create a Conversation
Conversation conversation = new Conversation(speechConfig, conversationId);

// Create an audio stream from a wav file or from the default microphone if you want to stream live audio from the supported devices
// Replace with your own audio file name and Helper class which implements AudioConfig using PullAudioInputStreamCallback
PullAudioInputStreamCallback wavfilePullStreamCallback = Helper.OpenWavFile("16Khz16Bits8channelsOfRecordedPCMAudio.wav");
// Create an audio stream format assuming the file used above is 16Khz, 16 bits and 8 channel pcm wav file
AudioStreamFormat audioStreamFormat = AudioStreamFormat.getWaveFormatPCM((long)16000, (short)16,(short)8);
// Create an input stream
AudioInputStream audioStream = AudioInputStream.createPullStream(wavfilePullStreamCallback, audioStreamFormat);

// Create a conversation transcriber
ConversationTranscriber transcriber = new ConversationTranscriber(AudioConfig.fromStreamInput(audioStream));

// join a conversation
transcriber.joinConversationAsync(conversation);

// Add the event listener for the realtime events
transcriber.transcribed.addEventListener((o, e) -> {
    System.out.println("Conversation transcriber Recognized:" + e.toString());
});

transcriber.canceled.addEventListener((o, e) -> {
    System.out.println("Conversation transcriber canceled:" + e.toString());
    try {
        transcriber.stopTranscribingAsync().get();
    } catch (InterruptedException ex) {
        ex.printStackTrace();
    } catch (ExecutionException ex) {
        ex.printStackTrace();
    }
});

transcriber.sessionStopped.addEventListener((o, e) -> {
    System.out.println("Conversation transcriber stopped:" + e.toString());

    try {
        transcriber.stopTranscribingAsync().get();
    } catch (InterruptedException ex) {
        ex.printStackTrace();
    } catch (ExecutionException ex) {
        ex.printStackTrace();
    }
});

// start the transcription.
Future<?> future = transcriber.startTranscribingAsync();
...
```

Gerçek zamanlı _artı_ zaman uyumsuz isterseniz, ilgili kod satırlarının açıklamasını aşağıdaki gibi kodlayın:

```java
// Set the property for asynchronous transcription
//speechConfig.setServiceProperty("transcriptionMode", "Async", ServicePropertyChannel.UriQueryParameter);

// Set the property for real-time plus asynchronous transcription
speechConfig.setServiceProperty("transcriptionMode", "RealTimeAndAsync", ServicePropertyChannel.UriQueryParameter);
```

## <a name="get-transcription-results"></a>Dökümü alma sonuçları

Bu adım zaman uyumsuz döküm sonuçlarını alır, ancak gerekli olan gerçek zamanlı işlemleri başka bir yerde yerine getirir. Daha fazla bilgi için bkz. [KONUŞMAYı SDK ile gerçek zamanlı olarak konuşmaları dönüştürme](how-to-use-conversation-transcription-service.md).

Burada gösterilen kod için, yalnızca Windows, Linux ve Android (API düzeyi 26 veya üzeri) üzerinde Java (1.8.0 veya üzeri) için desteklenen **uzak konuşma sürümü 1.8.0**gerekir.

### <a name="obtaining-the-client-sdk"></a>İstemci SDK 'sını alma

POM. XML dosyanızı aşağıdaki gibi düzenleyerek **uzak konuşmayı** elde edebilirsiniz.

1. Dosyanın sonunda, kapatma etiketiyle `</project>`önce, konuşma SDK 'Sı için Maven deposuna başvuru içeren bir `repositories` öğesi oluşturun:

   ```xml
   <repositories>
     <repository>
       <id>maven-cognitiveservices-speech</id>
       <name>Microsoft Cognitive Services Speech Maven Repository</name>
       <url>https://csspeechstorage.blob.core.windows.net/maven/</url>
     </repository>
   </repositories>
   ```

2. Ayrıca, bir bağımlılık olarak remoteconversation-Client-SDK 1.8.0 ile bir `dependencies` öğesi ekleyin:

   ```xml
   <dependencies>
     <dependency>
       <groupId>com.microsoft.cognitiveservices.speech.remoteconversation</groupId>
       <artifactId>remote-conversation</artifactId>
       <version>1.8.0</version>
     </dependency>
   </dependencies>
   ```

3. Değişiklikleri Kaydet

### <a name="sample-transcription-code"></a>Örnek dökümü kodu

`conversationId`aldıktan sonra, zaman uyumsuz döküm durumunu sorgulamak için istemci uygulamada bir uzak konuşma döküm Client **remotetalk tiontranscriptionclient** oluşturun. Bir [Pollerflox](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/util/polling/PollerFlux.java) nesnesi almak Için **Remotetalk Tiontranscriptionclient** Içinde **gettranscriptionoperation** metodunu kullanın. Pollerflox nesnesi, uzak işlem durumu **Remotetalk Tiontranscriptionoperation** ve nihai sonuç **Remotetalk tiontranscriptionresult**hakkında bilgi sahibi olacaktır. İşlem tamamlandıktan sonra, bir [Syncpoller](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/util/polling/SyncPoller.java)üzerinde **getfinalresult** çağırarak **Remotetalk tiontranscriptionresult** alın. Bu kodda, sonuç içeriğini sistem çıktısına yazdırdık.

```java
// Create the speech config object
SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "Region");

// Create a remote Conversation Transcription client
RemoteConversationTranscriptionClient client = new RemoteConversationTranscriptionClient(speechConfig);

// Get the PollerFlux for the remote operation
PollerFlux<RemoteConversationTranscriptionOperation, RemoteConversationTranscriptionResult> remoteTranscriptionOperation = client.getTranscriptionOperation(conversationId);

// Subscribe to PollerFlux to get the remote operation status
remoteTranscriptionOperation.subscribe(
        pollResponse -> {
            System.out.println("Poll response status : " + pollResponse.getStatus());
            System.out.println("Poll response status : " + pollResponse.getValue().getServiceStatus());
        }
);

// Obtain the blocking operation using getSyncPoller
SyncPoller<RemoteConversationTranscriptionOperation, RemoteConversationTranscriptionResult> blockingOperation =  remoteTranscriptionOperation.getSyncPoller();

// Wait for the operation to finish
blockingOperation.waitForCompletion();

// Get the final result response
RemoteConversationTranscriptionResult resultResponse = blockingOperation.getFinalResult();

// Print the result
if(resultResponse != null) {
    if(resultResponse.getConversationTranscriptionResults() != null) {
        for (int i = 0; i < resultResponse.getConversationTranscriptionResults().size(); i++) {
            ConversationTranscriptionResult result = resultResponse.getConversationTranscriptionResults().get(i);
            System.out.println(result.getProperties().getProperty(PropertyId.SpeechServiceResponse_JsonResult.name()));
            System.out.println(result.getProperties().getProperty(PropertyId.SpeechServiceResponse_JsonResult));
            System.out.println(result.getOffset());
            System.out.println(result.getDuration());
            System.out.println(result.getUserId());
            System.out.println(result.getReason());
            System.out.println(result.getResultId());
            System.out.println(result.getText());
            System.out.println(result.toString());
        }
    }
}

System.out.println("Operation finished");
```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [GitHub 'da örneklerimizi keşfet](https://aka.ms/csspeech/samples)
