---
title: İOS üzerinde amaç-C ' d a Bing Konuşma tanıma API 'SI ile çalışmaya başlama | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Konuşulan sesi metne dönüştüren iOS uygulamaları geliştirmek için Bing Konuşma tanıma API 'sini kullanın.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: e42784e6d2751f7e76aec8caf1d6e1f9f09a9fd1
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/13/2019
ms.locfileid: "70965926"
---
# <a name="quickstart-use-the-bing-speech-recognition-api-in-objective-c-on-ios"></a>Hızlı Başlangıç: İOS üzerinde amaç-C içindeki Bing Konuşma tanıma API 'sini kullanma

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Konuşma tanıma API 'SI ile, konuşulan sesi metne dönüştürmek için bulut tabanlı konuşma hizmeti kullanan iOS uygulamaları geliştirebilirsiniz. API gerçek zamanlı akışı destekler, böylece uygulamanız aynı anda ve zaman uyumsuz olarak kısmi tanıma sonuçları alabilir ve bu da hizmete ses gönderiyor.

Bu makalede, bir iOS uygulaması geliştirmek üzere konuşma tanıma API 'sini kullanmaya nasıl başlacağınız hakkında temel bilgileri göstermek için örnek bir uygulama kullanılmaktadır. Tüm API başvurusu için bkz. [konuşma SDK 'sı istemci kitaplığı başvurusu](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-iOS/master/com.Microsoft.SpeechSDK-1_0-for-iOS.docset/Contents/Resources/Documents/index.html).

## <a name="prerequisites"></a>Önkoşullar

### <a name="platform-requirements"></a>Platform gereksinimleri

Mac XCode IDE 'nin yüklü olduğundan emin olun.

### <a name="get-the-client-library-and-examples"></a>İstemci kitaplığını ve örnekleri al

İOS için Konuşma istemci kitaplığı ve örnekleri, [iOS Için konuşma istemcisi SDK 'sında](https://github.com/microsoft/cognitive-speech-stt-ios)bulunabilir.

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>Konuşma tanıma API 'sine abone olun ve ücretsiz deneme aboneliği anahtarı alın

Konuşma API 'SI bilişsel hizmetler 'in (daha önce Project Oxford) bir parçasıdır. Bilişsel [Hizmetler aboneliği](https://azure.microsoft.com/try/cognitive-services/) sayfasından ücretsiz deneme aboneliği anahtarlarına sahip olabilirsiniz. Konuşma API 'sini seçtikten sonra anahtarı almak için **API anahtarı al** ' ı seçin. Birincil ve ikincil anahtar döndürür. Her iki anahtar de aynı kotaya bağlıdır, bu nedenle her iki anahtarı da kullanabilirsiniz.

*Tanımayı amaç ile birlikte*kullanmak istiyorsanız, [Language Understanding Intelligent Service (lusıs)](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/)için kaydolmanız gerekir.

> [!IMPORTANT]
> * Abonelik anahtarı alın. Konuşma istemci kitaplıklarını kullanabilmeniz için önce bir [abonelik anahtarınız](https://azure.microsoft.com/try/cognitive-services/)olması gerekir.
>
> * Abonelik anahtarınızı kullanın. Sağlanan iOS örnek uygulamasıyla, örnek/SpeechRecognitionServerExample/Settings. plist dosyasını abonelik anahtarınızla güncelleştirmeniz gerekir. Daha fazla bilgi için bkz. [oluşturma ve çalıştırma örnekleri](#build-and-run-samples).

## <a name="use-the-speech-client-library"></a>Konuşma istemci kitaplığı kullanın

İstemci kitaplığını bir XCode projesine eklemek için bu [yönergeleri](https://github.com/Azure-Samples/Cognitive-Speech-STT-iOS#the-client-library)izleyin.

İOS için istemci kitaplığı başvurusunu bulmak için bu [Web sayfasına](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-iOS/master/com.Microsoft.SpeechSDK-1_0-for-iOS.docset/Contents/Resources/Documents/index.html)bakın.

## <a name="build-and-run-samples"></a>Örnek oluşturma ve çalıştırma

Örnek oluşturma ve çalıştırma hakkında daha fazla bilgi için bu [Benioku sayfasına](https://github.com/Azure-Samples/Cognitive-Speech-STT-iOS#the-sample)bakın.

## <a name="samples-explained"></a>Açıklanan örnekler

### <a name="create-recognition-clients"></a>Tanıma istemcileri oluşturma

Örnekteki aşağıdaki kod, Kullanıcı senaryolarına göre tanınma istemci sınıflarının nasıl oluşturulacağını gösterir:

```
{
    NSString* language = @"en-us";

    NSString* path = [[NSBundle mainBundle] pathForResource:@"settings" ofType:@"plist"];
    NSDictionary* settings = [[NSDictionary alloc] initWithContentsOfFile:path];

    NSString* primaryOrSecondaryKey = [settings objectForKey:(@"primaryKey")];
    NSString* luisAppID = [settings objectForKey:(@"luisAppID")];
    NSString* luisSubscriptionID = [settings objectForKey:(@"luisSubscriptionID")];

    if (isMicrophoneReco) {
        if (!isIntent) {
            micClient = [SpeechRecognitionServiceFactory createMicrophoneClient:(recoMode)
                                                                   withLanguage:(language)
                                                                        withKey:(primaryOrSecondaryKey)
                                                                   withProtocol:(self)];
        }
        else {
            MicrophoneRecognitionClientWithIntent* micIntentClient;
            micIntentClient = [SpeechRecognitionServiceFactory createMicrophoneClientWithIntent:(language)
                                                                                        withKey:(primaryOrSecondaryKey)
                                                                                  withLUISAppID:(luisAppID)
                                                                                 withLUISSecret:(luisSubscriptionID)
                                                                                   withProtocol:(self)];
            micClient = micIntentClient;
        }
    }
    else {
        if (!isIntent) {
            dataClient = [SpeechRecognitionServiceFactory createDataClient:(recoMode)
                                                              withLanguage:(language)
                                                                   withKey:(primaryOrSecondaryKey)
                                                              withProtocol:(self)];
        }
        else {
            DataRecognitionClientWithIntent* dataIntentClient;
            dataIntentClient = [SpeechRecognitionServiceFactory createDataClientWithIntent:(language)
                                                                                   withKey:(primaryOrSecondaryKey)
                                                                             withLUISAppID:(luisAppID)
                                                                            withLUISSecret:(luisSubscriptionID)
                                                                              withProtocol:(self)];
            dataClient = dataIntentClient;
        }
    }
}

```

İstemci kitaplığı, konuşma tanımada tipik senaryolar için önceden uygulanmış tanıma istemci sınıfları sağlar:

* `DataRecognitionClient`: PCM verileriyle konuşma tanıma (örneğin, bir dosya veya ses kaynağından). Veriler arabelleklere ayrılır ve her bir arabellek konuşma hizmetine gönderilir. Arabelleklere herhangi bir değişiklik yapılmaz, böylece kullanıcılar isterseniz kendi sessizlik algılamayı uygulayabilir. Veriler WAV dosyalarından sağlanmışsa, dosyadaki verileri doğrudan sunucuya gönderebilirsiniz. Ham verileriniz varsa (örneğin, Bluetooth 'tan geliyorsa), önce sunucuya bir biçim üst bilgisi gönderin ve ardından verileri takip edersiniz.
* `MicrophoneRecognitionClient`: Mikrofondan gelen ses ile konuşma tanıma. Mikrofonun açık olduğundan ve mikrofondan gelen verilerin konuşma tanıma hizmetine gönderildiğinden emin olun. Mikrofon verilerine, tanıma hizmetine gönderilmeden önce yerleşik bir "sessizlik algılayıcısı" uygulanır.
* `DataRecognitionClientWithIntent`ve `MicrophoneRecognitionClientWithIntent`: Bu istemciler, tanınma metnine ek olarak, uygulamalarınızın, uygulamanızın daha fazla eylem için kullanabileceği, konuşmacı amacı hakkında yapılandırılmış bilgiler döndürür. "Amaç" kullanmak için, bir modeli [lusıs](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/)kullanarak eğitmeniz gerekir.

### <a name="recognition-language"></a>Tanıma dili

İstemcisini oluşturmak için `SpeechRecognitionServiceFactory` kullandığınızda bir dil seçmeniz gerekir. Konuşma hizmeti tarafından desteklenen dillerin tüm listesi için bkz. [desteklenen diller](../API-Reference-REST/supportedlanguages.md).

### <a name="speechrecognitionmode"></a>SpeechRecognitionMode

Ayrıca şunları ile `SpeechRecognitionServiceFactory`istemcisini oluştururken `SpeechRecognitionMode` belirtmeniz gerekir:

* `SpeechRecognitionMode_ShortPhrase`: En fazla 15 saniye uzunluğunda bir utterlik. Veriler hizmete gönderilirken, istemci birden çok kısmi sonuç ve birden çok n en iyi seçenek içeren bir nihai sonuç alır.
* `SpeechRecognitionMode_LongDictation`: En fazla iki dakikalık bir utterlik. Veriler hizmete gönderilirken, istemci birden çok kısmi sonuç ve birden çok nihai sonuç alır ve sunucunun tümce duraklamaları nerede tanımladığına bağlıdır.

### <a name="attach-event-handlers"></a>Olay işleyicilerini Ekle

Oluşturduğunuz istemciye çeşitli olay işleyicileri ekleyebilirsiniz:

* **Kısmi sonuçlar olayları**: Bu olay konuşma hizmeti 'nin söylediklerinizi tahmin etmeden önce ( `MicrophoneRecognitionClient`kullanıyorsanız) veya veri göndermeyi ( `DataRecognitionClient`kullanıyorsanız) bir kez her tahmin edildiğinde çağırılır.
* **Hata olayları**: Hizmet bir hata algıladığında çağırılır.
* **Amaç olayları**: Son tanıma sonucu yapılandırılmış bir JSON amacına ayrıştırıldıktan sonra, "Withamacını" istemcilerinde (yalnızca Shorttümcecik modunda) çağırılır.
* **Sonuç olayları**:
  * `SpeechRecognitionMode_ShortPhrase` Modda bu olay çağrılır ve konuşmayı tamamladıktan sonra n en iyi sonuçları döndürür.
  * `SpeechRecognitionMode_LongDictation` Modunda, olay işleyicisi hizmetin cümle duraklamaları tanımladığı yere bağlı olarak birden çok kez çağrılır.
  * **Her n en iyi seçenek için**, tanınan metnin bir güvenirlik değeri ve birkaç farklı biçimi döndürülür. Daha fazla bilgi için bkz. [çıkış biçimi](../Concepts.md#output-format).

## <a name="related-topics"></a>İlgili konular

* [İOS için istemci kitaplığı başvurusu](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-iOS/master/com.Microsoft.SpeechSDK-1_0-for-iOS.docset/Contents/Resources/Documents/index.html)
* [Android 'de Java 'da Microsoft konuşma tanıma ve/veya amacını kullanmaya başlama](GetStartedJavaAndroid.md)
* [JavaScript 'te Microsoft konuşma API 'SI ile çalışmaya başlama](GetStartedJSWebsockets.md)
* [REST aracılığıyla Microsoft konuşma API 'SI ile çalışmaya başlama](GetStartedREST.md)
