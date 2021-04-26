---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 10/20/2020
ms.author: trbye
ms.openlocfilehash: d2fae1c08132c855f570750a4528a9a37343434e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105104377"
---
## <a name="install-the-speech-sdk"></a>Konuşma SDK 'sını yükler

Herhangi bir şey yapabilmeniz <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">için önce JavaScript Için konuşma SDK 'sını </a>yüklemeniz gerekir. Platformunuza bağlı olarak, aşağıdaki yönergeleri kullanın:

- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=nodejs#get-the-speech-sdk" target="_blank">Node.js <span 
class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="/azure/cognitive-services/speech-service/speech-sdk?tabs=browser#get-the-speech-sdk" target="_blank">Web tarayıcısı </a>

## <a name="create-voice-signatures"></a>Ses imzaları oluşturma

İlk adım, konuşma katılımcıları için, benzersiz konuşmacı olarak belirlenebilmeleri için ses imzaları oluşturmaktır. `.wav`Ses imzaları oluşturmak için giriş ses dosyası 16 bit, 16 kHz örnek hız ve tek kanallı (mono) biçiminde olmalıdır. Her ses örneği için önerilen uzunluk otuz saniye ile iki dakika arasındadır. `.wav`Benzersiz bir ses profili oluşturulması için dosyanın bir **kişinin** sesine ait bir örnek olması gerekir.

Aşağıdaki örnek, JavaScript 'teki [REST API kullanarak](https://aka.ms/cts/signaturegenservice) bir ses imzasının nasıl oluşturulacağını gösterir. `subscriptionKey`,, Ve için gerçek bilgileri `region` bir örnek dosya için yerine getirmeniz gerektiğini unutmayın `.wav` .

```javascript
const fs = require('fs');
const axios = require('axios');
const formData = require('form-data');
 
const subscriptionKey = 'your-subscription-key';
const region = 'your-region';
 
async function createProfile() {
    let form = new formData();
    form.append('file', fs.createReadStream('path-to-voice-sample.wav'));
    let headers = form.getHeaders();
    headers['Ocp-Apim-Subscription-Key'] = subscriptionKey;
 
    let url = `https://signature.${region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromFormData`;
    let response = await axios.post(url, form, { headers: headers });
    
    // get signature from response, serialize to json string
    return JSON.stringify(response.data.Signature);
}
 
async function main() {
    // use this voiceSignature string with conversation transcription calls below
    let voiceSignatureString = await createProfile();
    console.log(voiceSignatureString);
}
main();
```

Bu betiği çalıştırmak, değişkenine bir ses imza dizesi döndürüyor `voiceSignatureString` . Değişkene giriş olarak kullanılacak iki dizeniz olması için işlevi iki kez çalıştırın `voiceSignatureStringUser1` `voiceSignatureStringUser2` .

> [!NOTE]
> Ses imzaları **yalnızca** REST API kullanılarak oluşturulabilir.

## <a name="transcribe-conversations"></a>Konuşmalar

Aşağıdaki örnek kod, iki hoparlör için konuşmaları gerçek zamanlı olarak nasıl oluşturacağınızı gösterir. Yukarıda gösterildiği gibi her konuşmacı için zaten ses imza dizeleri oluşturduğunuzu varsayar. `subscriptionKey`,, Ve için gerçek bilgileri, `region` ve almak istediğiniz sesin yolunu yerine koyun `filepath` .

Bu örnek kod şunları yapar:

* Döküm için kullanılacak bir gönderme akışı oluşturur ve örnek `.wav` dosyayı buna yazar.
* Bir `Conversation` kullanarak oluşturur `createConversationAsync()` .
* `ConversationTranscriber`Oluşturucuyu kullanarak oluşturur.
* Konuşmaya katılımcıları ekler. Dizeler `voiceSignatureStringUser1` ve `voiceSignatureStringUser2` Yukarıdaki adımlardan çıkış olarak gelmelidir.
* Olaylara kaydolduktan sonra dökümü başlatır.

```javascript
(function() {
    "use strict";
    var sdk = require("microsoft-cognitiveservices-speech-sdk");
    var fs = require("fs");
    
    var subscriptionKey = "your-subscription-key";
    var region = "your-region";
    var filepath = "audio-file-to-transcribe.wav"; // 8-channel audio
    
    // create the push stream and write file to it
    var pushStream = sdk.AudioInputStream.createPushStream();
    fs.createReadStream(filepath).on('data', function(arrayBuffer) {
        pushStream.write(arrayBuffer.slice());
    }).on('end', function() {
        pushStream.close();
    });
    
    var speechTranslationConfig = sdk.SpeechTranslationConfig.fromSubscription(subscriptionKey, region);
    var audioConfig = sdk.AudioConfig.fromStreamInput(pushStream);
    speechTranslationConfig.speechRecognitionLanguage = "en-US";
    
    // create conversation and transcriber
    var conversation = sdk.Conversation.createConversationAsync(speechTranslationConfig, "myConversation");
    var transcriber = new sdk.ConversationTranscriber(audioConfig);
    
    // attach the transcriber to the conversation
    transcriber.joinConversationAsync(conversation,
    function () {
        // add first participant using voiceSignature created in enrollment step
        var user1 = sdk.Participant.From("user1@example.com", "en-us", voiceSignatureStringUser1);
        conversation.addParticipantAsync(user1,
        function () {
            // add second participant using voiceSignature created in enrollment step
            var user2 = sdk.Participant.From("user2@example.com", "en-us", voiceSignatureStringUser2);
            conversation.addParticipantAsync(user2,
            function () {
                transcriber.sessionStarted = function(s, e) {
                console.log("(sessionStarted)");
                };
                transcriber.sessionStopped = function(s, e) {
                console.log("(sessionStopped)");
                };
                transcriber.canceled = function(s, e) {
                console.log("(canceled)");
                };
                transcriber.transcribed = function(s, e) {
                console.log("(transcribed) text: " + e.result.text);
                console.log("(transcribed) speakerId: " + e.result.speakerId);
                };
    
                // begin conversation transcription
                transcriber.startTranscribingAsync(
                function () { },
                function (err) {
                    console.trace("err - starting transcription: " + err);
                });
        },
        function (err) {
            console.trace("err - adding user1: " + err);
        });
    },
    function (err) {
        console.trace("err - adding user2: " + err);
    });
    },
    function (err) {
    console.trace("err - " + err);
    });
}()); 
```